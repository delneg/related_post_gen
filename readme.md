### Problem:

Given a list of posts, compute the top 5 related posts for each post based on the number of shared tags.

<details>
<summary> Steps </summary>

-   Read the posts JSON file.
-   Iterate over the posts and populate a map containing: `tag -> List<int>`, with the int representing the post index of each post with that tag.
-   Iterate over the posts and for each post:
    -   Create a map: `PostIndex -> int` to track the number of shared tags
    -   For each tag, Iterate over the posts that have that tag
    -   For each post, increment the shared tag count in the map.
-   Sort the related posts by the number of shared tags.
-   Write the top 5 related posts for each post to a new JSON file.
</details>

### Run Benchmark

```
./run.sh go | rust | python | all

# windows (powershell)
./run.ps1 go | rust | python | all

or
pwsh ./run.ps1 go | rust | python | all

# Docker (check the dockerfile for available variables)
docker build -t databench .
docker run -e TEST_NAME=all -it --rm databench
```

<details>
<summary> Rules </summary>

<h3>No:</h3>

-   FFI (including assembly inlining)
-   Unsafe code blocks
-   Custom benchmarking
-   Disabling runtime checks (bounds etc)
-   Specific hardware targeting
-   SIMD for single threaded solutions
-   Hardcoding number of posts
-   Lazy evaluation (Unless results are computed at runtime and timed)
-   Computation Caching

<h3>Must:</h3>

-   Support up to 100,000 posts
-   Support UTF8 strings
-   Parse json at runtime
-   Support up to 100 tags
-   Use a stable release of the compiler/runtime
-   Represent tags as strings
-   Be production ready
-   Use less than 8GB of memory
</details>

### Updated Results from github workflow ([raw data](https://github.com/jinyus/related_post_gen/blob/main/raw_results.md))

##### VM Specs ( Azure F4s v2 - 4vCPU-8GB-Ubuntu 22.04 )

| Language       | Time (5k posts)                       | 20k posts                              | 60k posts                           | Total     |
| -------------- | ------------------------------------- | -------------------------------------- | ----------------------------------- | --------- |
| _Julia HO_[^1] | 8.15 ms | 56.33 ms | 147.00 ms | 211.49 ms |
| D | 24.87 ms | $\textsf{\color{lightgreen}317.38 ms}$ | $\textsf{\color{lightgreen}2.71 s}$ | 3.05 s |
| Rust | $\textsf{\color{lightgreen}23.66 ms}$ | 340.98 ms | 3.04 s | 3.41 s |
| C# (AOT) | 26.29 ms | 370.74 ms | 3.25 s | 3.65 s |
| C# (JIT) | 27.18 ms | 375.36 ms | 3.26 s | 3.66 s |
| Go | 24.55 ms | 371.06 ms | 3.29 s | 3.68 s |
| Zig | 29.00 ms | 429.33 ms | 3.79 s | 4.25 s |
| Nim | 30.02 ms | 433.71 ms | 3.83 s | 4.29 s |
| Java (GraalVM) | 43.69 ms | 462.33 ms | 4.12 s | 4.63 s |
| Julia | 30.23 ms | 481.00 ms | 4.25 s | 4.76 s |
| C++ | 36.00 ms | 519.67 ms | 4.58 s | 5.13 s |
| F# (AOT) | 35.85 ms | 530.00 ms | 4.68 s | 5.25 s |
| F# (JIT) | 37.23 ms | 536.67 ms | 4.71 s | 5.28 s |
| Odin | 48.23 ms | 687.08 ms | 6.08 s | 6.81 s |
| Swift | 51.10 ms | 713.57 ms | 6.20 s | 6.97 s |
| Vlang | 51.53 ms | 756.98 ms | 6.67 s | 7.48 s |
| Crystal | 60.57 ms | 906.97 ms | 8.02 s | 8.98 s |
| JS (Bun) | 126.38 ms | 1.46 s | 12.13 s | 13.72 s |
| LuaJIT | 103.84 ms | 1.47 s | 12.59 s | 14.16 s |
| JS (Deno) | 178.69 ms | 1.81 s | 17.22 s | 19.20 s |
| JS (Node) | 134.62 ms | 2.29 s | 20.87 s | 23.29 s |
| Dart VM | 132.38 ms | 2.34 s | 21.07 s | 23.54 s |
| Dart AOT | 186.38 ms | 2.88 s | 25.79 s | 28.86 s |
| ocaml | 145.00 ms | 2.51 s | 33.50 s | 36.16 s |
| Lua | 1.10 s | 16.72 s | 159.68 s | 177.49 s |
| Python | 1.47 s | 23.47 s | 214.79 s | 239.72 s |
| Erlang | 1.52 s | 24.55 s | 232.82 s | 258.89 s |
| Numpy | 441.44 ms | 7.23 s | OOM | N/A |
| Java (JIT) | 55.38 ms | OOM | OOM | N/A |

### Multicore Results

| Language       | Time (5k posts) | 20k posts        | 60k posts        | Total     |
| -------------- | --------------- | ---------------- | ---------------- | --------- |
| D Concurrent | 17.01 ms | 172.39 ms | $\textsf{\color{lightgreen}1.40 s}$ | 1.59 s |
| Go Concurrent | 14.56 ms | $\textsf{\color{lightgreen}171.56 ms}$ | 1.42 s | 1.61 s |
| Rust Concurrent | $\textsf{\color{lightgreen}13.59 ms}$ | 179.47 ms | 1.58 s | 1.78 s |
| C++ Concurrent | 17.23 ms | 207.00 ms | 1.75 s | 1.97 s |
| Julia Concurrent | 16.92 ms | 222.33 ms | 1.94 s | 2.18 s |
| F# Concurrent (AOT) | 15.62 ms | 230.67 ms | 2.03 s | 2.28 s |
| F# Concurrent (JIT) | 20.08 ms | 276.33 ms | 2.49 s | 2.78 s |
| Swift Concurrent | 31.91 ms | 393.57 ms | 3.49 s | 3.92 s |
| Java (GraalVM) Concurrent | 39.08 ms | 464.67 ms | 4.24 s | 4.75 s |

<details>
<summary> Old Results with details (on my machine) </summary>

| Language   | Processing Time | Total (+ I/O) | Details                                                                                                                                                                                                                                                                                         |
| ---------- | --------------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Rust       | -               | 4.5s          | Initial                                                                                                                                                                                                                                                                                         |
| Rust v2    | -               | 2.60s         | Replace std HashMap with fxHashMap by [phazer99](https://www.reddit.com/r/rust/comments/16plgok/comment/k1rtr4x/?utm_source=share&utm_medium=web2x&context=3)                                                                                                                                   |
| Rust v3    | -               | 1.28s         | Preallocate and reuse map and unstable sort by [vdrmn](https://www.reddit.com/r/rust/comments/16plgok/comment/k1rzo7g/?utm_source=share&utm_medium=web2x&context=3) and [Darksonn](https://www.reddit.com/r/rust/comments/16plgok/comment/k1rzwdx/?utm_source=share&utm_medium=web2x&context=3) |
| Rust v4    | -               | 0.13s         | Use Post index as key instead of Pointer and Binary Heap by [RB5009](https://www.reddit.com/r/rust/comments/16plgok/comment/k1s5ea0/?utm_source=share&utm_medium=web2x&context=3)                                                                                                               |
| Rust v5    | 38ms            | 52ms          | Rm hashing from loop and use vec[count] instead of map[index]count by RB5009                                                                                                                                                                                                                    |
| Rust v6    | 23ms            | 36ms          | Optimized Binary Heap Ops by [scottlamb](https://github.com/jinyus/related_post_gen/pull/12)                                                                                                                                                                                                    |
| Rust Rayon | 9ms             | 22ms          | Parallelize by [masmullin2000](https://github.com/jinyus/related_post_gen/pull/4)                                                                                                                                                                                                               |
| Rust Rayon | 8ms             | 22ms          | Remove comparison out of hot loop                                                                                                                                                                                                                                                               |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Go         | -               | 1.5s          | Initial                                                                                                                                                                                                                                                                                         |
| Go v2      | -               | 80ms          | Add rust optimizations                                                                                                                                                                                                                                                                          |
| Go v3      | 56ms            | 70ms          | Use goccy/go-json                                                                                                                                                                                                                                                                               |
| Go v3      | 34ms            | 55ms          | Use generic binaryheap by [DrBlury](https://github.com/jinyus/related_post_gen/pull/7)                                                                                                                                                                                                          |
| Go v4      | 26ms            | 50ms          | Replace binary heap with custom priority queue                                                                                                                                                                                                                                                  |
| Go v5      | 20ms            | 43ms          | Remove comparison out of hot loop                                                                                                                                                                                                                                                               |
| Go Con     | 10ms            | 33ms          | Go concurrency by [tirprox](https://github.com/jinyus/related_post_gen/pull/17) and [DrBlury](https://github.com/jinyus/related_post_gen/pull/8)                                                                                                                                                |
| Go Con v2  | 5ms             | 29ms          | Use arena, use waitgroup, rm binheap by [DrBlury](https://github.com/jinyus/related_post_gen/pull/20)                                                                                                                                                                                           |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Python     | -               | 7.81s         | Initial                                                                                                                                                                                                                                                                                         |
| Python v2  | 1.35s           | 1.53s         | Add rust optimizations by [dave-andersen](https://github.com/jinyus/related_post_gen/pull/10)                                                                                                                                                                                                   |
| Numpy      | 0.57s           | 0.85s         | Numpy implementation by [Copper280z](https://github.com/jinyus/related_post_gen/pull/11)                                                                                                                                                                                                        |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Crystal    | 50ms            | 96ms          | Inital w/ previous optimizations                                                                                                                                                                                                                                                                |
| Crystal v2 | 33ms            | 72ms          | Replace binary heap with custom priority queue                                                                                                                                                                                                                                                  |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Odin       | 110ms           | 397ms         | Ported from golang code                                                                                                                                                                                                                                                                         |
| Odin v2    | 104ms           | 404ms         | Remove comparison out of hot loop                                                                                                                                                                                                                                                               |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Dart VM    | 125ms           | 530ms         | Ported from golang code                                                                                                                                                                                                                                                                         |
| Dart bin   | 274ms           | 360ms         | Compiled executable                                                                                                                                                                                                                                                                             |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Vlang      | 339ms           | 560ms         | Ported from golang code                                                                                                                                                                                                                                                                         |
| ⠀          | ⠀               | ⠀             | ⠀                                                                                                                                                                                                                                                                                               |
| Zig        | 80ms            | 110ms         | Provided by [akhildevelops](https://github.com/jinyus/related_post_gen/pull/30)                                                                                                                                                                                                                 |

</details>

[^1]: Uses specialized datastructures meant for demonstation purposes: [more](https://github.com/LilithHafner/Jokes/tree/main/SuperDataStructures.jl)
