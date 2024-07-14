# Hotspot Tracker

## Overview

The Hotspot Tracker efficiently tracks the top N most frequently requested keys across a distributed system. It uses sharding and min-heaps to maintain performance and scalability.

## Design Choices

### Min-Heap
Min-heap is used within each shard as basic data structure to efficiently track the top N keys by frequency. The min-heap ensures that operations for maintaining the top N keys are logarithmic in complexity, providing an efficient way to manage frequent updates.

##### Trade-offs:
Memory Usage vs. Performance: Using a heap data structure ensures that the tracker operates efficiently even with frequent updates. While this incurs some memory overhead for maintaining the heap, the performance gains from logarithmic operations make it a suitable choice.

### Sharding
Sharding distributes the load across multiple sub-trackers, reducing contention and improving concurrency. By breaking the data into smaller, manageable pieces, each shard can operate independently, which enhances parallel processing and reduces bottlenecks.

##### Trade-offs:
Memory Usage vs. Performance: While sharding improves concurrency and reduces contention, it increases memory usage because each shard maintains its own data structures. However, the performance benefits from reduced contention outweigh the increased memory overhead. While latency increased for individual operations but concurrent operations improved.


### FNV Hash
The FNV hash function is chosen for key partitioning because it provides a good distribution of hash values, reducing the likelihood of hash collisions. This helps in evenly distributing keys across shards.

##### Trade-offs:
Complexity vs. Distribution Quality: While FNV is relatively simple and fast, it provides a good balance between complexity and the quality of distribution. This ensures that keys are evenly spread across shards, minimizing contention and maximizing concurrency.


## Usage

### Import

import github.com/aayush993/hotspot-tracker

### Initialization

```go
ht := htracker.NewHotspotTracker(10, 4) // Track top 10 keys across 4 shards
ht.RecordRequest("key1")
ht.RecordRequest("key2")

hotspots := ht.GetHotspots()

isHotspot := ht.IsHotspot("key1")

```

```bash
go test -v

```

```bash
go test -bench=.

```


## Improvements

### Read Cache
A cache mechanism is implemented to periodically update the list of hotspots. This caching strategy balances the need for performance with the requirement for accuracy, ensuring that the system does not become a bottleneck while still providing up-to-date hotspot information.

##### Trade-offs:
Staleness vs. Performance: The cache may introduce slight staleness in hotspot data, but it is expected to reduce the performance overhead of frequently updating the hotspot list.