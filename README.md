# bzip2-fuzzing-project
---
## Call & Control Flow Graphs
### Call Graph
[Call Graph and Control Flow Graph Generation](/Call%20Graph%20and%20Control%20Flow%20Graph%20Generation/Call%20Graph/bzip2_callgraph.png)

### Control Flow Graph (CFG)
#### BZ2_bzWrite
[Call Graph and Control Flow Graph Generation](/Call%20Graph%20and%20Control%20Flow%20Graph%20Generation/Control%20Flow%20Graph/BZ2_bzWrite/BZ2_bzWrite_cfg.png)

#### uncompressStream
[Call Graph and Control Flow Graph Generation](/Call%20Graph%20and%20Control%20Flow%20Graph%20Generation/Control%20Flow%20Graph/uncompressStream/uncompressStream_cfg.png)

---
## Fuzzing Campaigns
### Compressing File
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Compressing%20File)

#### Initial seed corpus 
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Compressing%20File/Initial%20Seed%20Corpus)

#### Fuzzer_stats and progress files.
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Compressing%20File/fuzzer_stats%20and%20progress%20files)

#### All directories (queue, crashes, hangs)
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Compressing%20File/All%20directories%20(queue,%20crashes,%20hangs))

#### Code
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Compressing%20File/Command%20lines%20used%20for%20the%20run.md)

### Decompressing File
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Decompressing%20File)

#### Initial seed corpus 
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Decompressing%20File/Initial%20seed%20corpus)

#### Fuzzer_stats and progress files.
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Decompressing%20File/fuzzer_stats%20and%20progress%20files)

#### All directories (queue, crashes, hangs)
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Decompressing%20File/All%20directories%20(queue,%20crashes,%20hangs))

#### Code
[Greybox Fuzzing Campaign](/Greybox%20Fuzzing%20Campaign/Decompressing%20File/Command%20lines%20used%20for%20the%20run.md)

---
## Source-Based Coverage
### Compressing File
[Source-Based Coverage]()
### Decompressing File
[Source-Based Coverage]()

---
## Coverage Mapping & Analysis
### Compressing File
#### Call Graph
[Coverage Mapping and Analysis](/Coverage%20Mapping%20and%20Analysis/Compressing%20File/Call%20Graph/bzip2_callgraph_annotated.png)

#### Control Flow Graph
[Coverage Mapping and Analysis](/Coverage%20Mapping%20and%20Analysis/Compressing%20File/Control%20Flow%20Graph/BZ2_bzWrite_cfg_annotated.png)

### Decompressing File
#### Call Graph
[Coverage Mapping and Analysis](/Coverage%20Mapping%20and%20Analysis/Decompressing%20File/Call%20Graph/bzip2_bc_callgraph_annotated.png)

#### Control Flow Graph
[Coverage Mapping and Analysis](/Coverage%20Mapping%20and%20Analysis/Decompressing%20File/Control%20Flow%20Graph/uncompressStream_cfg_annotated.png)