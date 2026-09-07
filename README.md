# elasticsearch-loader

> Learning project, 2017. Not maintained — archived for reference.

A concurrent bulk loader that reads news/company records and indexes them into
Elasticsearch, built to practise backpressure and thread-pool control.

## What's interesting in it

- **`ShadowThreadPoolExecutor`** — extends `ThreadPoolExecutor` and adds `pause()` /
  `resume()`. `beforeExecute` blocks workers on a `ReentrantLock` `Condition` while paused,
  so the loader can throttle indexing when Elasticsearch falls behind
  (`WAITING_THRESHOLD_PAUSE` / `_RESUME`).
- **`ShadowQueue`** — batches items into fixed-size lists, tracks `requested` / `completed`
  / `loading` with `AtomicInteger`, backed by a `ConcurrentLinkedDeque`.
- **`Loader`** — submit work as `Future<Index>`, drain the futures, batch through the queue,
  hand batches to a separate fixed indexing pool, pause/resume around each cycle.

## Layout

`news-loader-domain` (executor, queue, DDD value objects) · `news-loader-application`
(ES `TransportClient` config, loaders, entry point). Gradle multi-module.

Java 8 · Elasticsearch `TransportClient` (pre-REST era) · Gradle · Lombok

Part of a since-retired news + company aggregation system, so it won't build/run
standalone.
