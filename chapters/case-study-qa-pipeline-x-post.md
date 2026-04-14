I had a working QA pipeline — 27,273 financial records, 16 validation checks, ran in about 12 seconds. Good enough. Then I wanted to make it faster.

What happened next is the best argument I've ever seen for writing pure functions.

---

THE STARTING POINT

One big function called `scan_records()` that did everything: validated each record, built lookup indexes, tracked field population stats, accumulated issues. It worked fine. But it was doing two fundamentally different jobs in one loop:

1. Validate each record independently (no shared state needed)
2. Build indexes across all records (accumulates into shared dicts)

Job 1 is embarrassingly parallel. Job 2 is inherently sequential. But they were tangled together in the same function, so BOTH were stuck running sequentially.

---

THE REFACTOR

We split one function into two:

```
validate_all(records)   → runs each check independently
build_indexes(records)  → accumulates shared lookup tables
```

That's it. No rewrite. No new abstractions. We literally separated the two jobs that were already happening inside the same loop into two loops.

This was trivial BECAUSE every check function was already pure. `check_score_range()` takes a dict and returns a list. It doesn't read files. It doesn't increment a counter on `self`. It doesn't append to a shared report. It has no opinion about what else is happening.

If the checks had been methods on a Validator class — mutating `self.issue_count`, writing to `self.report`, referencing `self.schema` — you couldn't split them out without untangling every shared reference first. That's a rewrite, not a refactor.

---

THE PARALLELIZATION

With the split done, parallelizing was three lines:

```
# Before
results = [validate_record(raw, fname) for fname, raw in records]

# After
with ProcessPoolExecutor() as pool:
    results = list(pool.map(validate_chunk, chunks))
```

validate_record() didn't change. Not one line. The pure functions don't know they're running in separate processes. They don't care. Data in, issues out.

---

THE BENCHMARKS

We actually tried threads first (ThreadPoolExecutor). Seemed like the obvious choice.

Sequential:     3.49s
ThreadPool:     4.66s ← SLOWER (GIL contention)
ProcessPool:    1.35s ← 2.6x faster

Threads made it worse because Python's GIL means CPU-bound threads fight over one lock. But we could switch to ProcessPoolExecutor without touching the validation logic because pure functions have nothing to share between threads OR processes. No locks needed. No shared state to protect. No race conditions possible.

Total pipeline time: 11.7s → 9.3s

---

THE POINT

The parallelization was easy because we'd already done the hard part — months ago, when we wrote the checks as pure functions instead of methods on a class.

We didn't plan for parallelism. We didn't design for it. We just followed one principle: data in, data out, no side effects.

Then when we needed speed, the architecture was already parallel-ready. The refactor was splitting one function into two. The parallelization was three lines. The pure functions never changed.

This is what people mean when they say good architecture "pays for itself." It doesn't mean you save time on day one. It means on the day you need to change something — add parallelism, add a check, swap the output format — the change is proportional to what you're actually changing, not to the complexity of everything else in the system.

If your functions are pure, your options are open.
If your methods have state, your options are whatever the state allows.

---

honestcode.software
