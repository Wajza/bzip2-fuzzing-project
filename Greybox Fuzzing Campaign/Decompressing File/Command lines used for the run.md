## AFL++ Fuzzing Configuration

### Environment Variables
```
AFL_CUSTOM_INFO_PROGRAM=./bzip2_fuzzer
AFL_CUSTOM_INFO_PROGRAM_ARGV=-cfk @@
AFL_CUSTOM_INFO_OUT=out_compress_small/default
AFL_I_DONT_CARE_ABOUT_MISSING_CRASHES=1
```
| Variable | Value | Description |
|----------|-------|-------------|
| `AFL_CUSTOM_INFO_PROGRAM` | `./bzip2_fuzzer` | Specifies the target program for fuzzing (custom fuzzing harness for bzip2) |
| `AFL_CUSTOM_INFO_PROGRAM_ARGV` | `-d -c @@` | Arguments passed to the target: `-d` (decompress), `-c` (write to stdout), `@@` (placeholder for fuzzed input file path) |
| `AFL_CUSTOM_INFO_OUT` | `outdec/default` | Directory for custom results/logs (separate from AFL's main output directory) |

----------------------------------------------------------------------------------------

### Execution Flow
```
'afl-fuzz' '-i' 'in_dec' '-o' 'outdec' '--' './bzip2_fuzzer' '-d' '-c' '@@'
```
| Component | Value | Description |
|-----------|-------|-------------|
| Fuzzer | `afl-fuzz` | American Fuzzy Lop fuzzer executable |
| Input Directory | `-i in_dec` | Directory containing seed inputs for fuzzing (decompression seeds) |
| Output Directory | `-o outdec` | Directory for results (queue, crashes, hangs, etc.) |
| Separator | `--` | Marks the end of AFL options and start of target program + arguments |
| Target Program | `./bzip2_fuzzer` | The compiled fuzzing harness for bzip2 |
| Target Arguments | `-d -c @@` | Decompress (`-d`), write to stdout (`-c`), `@@` replaced with fuzzed input file path |

### Target Program Arguments
```
./bzip2_fuzzer
-d
-c
@@
```
| Argument | Purpose |
|----------|---------|
| `./bzip2_fuzzer` | The compiled fuzzing harness for bzip2 |
| `-d` | Decompress mode - tests bzip2's decompression logic |
| `-c` | Write output to stdout (don't create output files, prevents disk clutter) |
| `@@` | Placeholder replaced by AFL++ with the actual fuzzed input file path |

----------------------------------------------------------------------------------------

## AFL++ Decompressing Fuzzing — All Commands

### Phase 1 — Compile with AFL++ instrumentation

```bash
export CC=afl-clang-fast
export CXX=afl-clang-fast++
```

```bash
./configure --disable-shared && make -j$(nproc)
```

```bash
cp bzip2 ./bzip2_fuzzer
```
| Step | Command | Purpose |
|------|---------|---------|
| Set compiler | `export CC=afl-clang-fast` | Use AFL++ instrumented Clang compiler |
| Set C++ compiler | `export CXX=afl-clang-fast++` | Use AFL++ instrumented Clang++ compiler |
| Configure & build | `./configure --disable-shared && make -j$(nproc)` | Build static binary with instrumentation |
| Copy binary | `cp bzip2 ./bzip2_fuzzer` | Rename for fuzzing harness |

---

### Phase 2 — Build seed corpus

```bash
mkdir in_dec
cp a.txt b.txt s3.txt s4.txt s5.txt s6.bin s7.bin in_dec/
```

```bash
bzip2 -k in_dec/*.txt in_dec/*.bin
```
| Step | Command | Purpose |
|------|---------|---------|
| Create directory | `mkdir in_dec` | Directory for seed inputs |
| Copy seeds | `cp a.txt b.txt s3.txt s4.txt s5.txt s6.bin s7.bin in_dec/` | Add initial seed files |
| Compress seeds | `bzip2 -k in_dec/*.txt in_dec/*.bin` | Convert seeds to bzip2 format (preserve originals with `-k`) |

---

### Phase 3 — OS tuning

```bash
echo core | sudo tee /proc/sys/kernel/core_pattern
```

```bash
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```
| Step | Command | Purpose |
|------|---------|---------|
| Core pattern | `echo core \| sudo tee /proc/sys/kernel/core_pattern` | Prevent crash dumps from being piped to system handlers |
| CPU governor | `echo performance \| sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor` | Maximize CPU performance for fuzzing |

---

### Phase 4 — Run the fuzzer (main command)

```bash
afl-fuzz -i in_dec -o outdec -- ./bzip2_fuzzer -d -c @@
```
| Component | Value | Description |
|-----------|-------|-------------|
| Fuzzer | `afl-fuzz` | AFL++ fuzzing engine |
| Input dir | `-i in_dec` | Seed corpus directory (bzip2-compressed files) |
| Output dir | `-o outdec` | Results directory |
| Separator | `--` | End of AFL options |
| Target | `./bzip2_fuzzer -d -c @@` | Target with decompression flags |

---

### Phase 5 — Monitor progress

```bash
afl-whatsup outdec
```

```bash
afl-plot outdec/default plot_output/
```

```bash
cat outdec/default/fuzzer_stats
```
| Command | Purpose |
|---------|---------|
| `afl-whatsup outdec` | Display overall fuzzing status and statistics for all fuzzer instances |
| `afl-plot outdec/default plot_output/` | Generate visual graphs of fuzzing progress over time |
| `cat outdec/default/fuzzer_stats` | Show detailed real-time statistics for the main fuzzer instance |

---

### Phase 6 — Minimize crashes and corpus

```bash
afl-tmin -i outdec/default/crashes/id:000000,sig:06,src:000263,time:169795,execs:213496,op:havoc,rep:1 \
         -o crash_min.bz2 -- ./bzip2_fuzzer -d -c @@
```

```bash
afl-cmin -i outdec/default/queue -o corpus_min -- ./bzip2_fuzzer -d -c @@
```
| Command | Purpose |
|---------|---------|
| `afl-tmin -i outdec/default/crashes/id:000000,sig:06,src:000263,time:169795,execs:213496,op:havoc,rep:1 -o crash_min.bz2 -- ./bzip2_fuzzer -d -c @@` | Minimize a crashing test case to its smallest possible size that still reproduces the crash |
| `afl-cmin -i outdec/default/queue -o corpus_min -- ./bzip2_fuzzer -d -c @@` | Minimize the corpus to the smallest set of inputs that still covers all execution paths |

**afl-tmin arguments breakdown:**

| Argument | Meaning |
|----------|---------|
| `-i` | Input crash file to minimize |
| `-o` | Output file for minimized crash |
| `sig:06` | Signal 06 (SIGABRT - assertion failure) |
| `op:havoc` | Operation that found the crash (havoc mutation) |
| `rep:1` | Mutation repetition count |

**afl-cmin arguments breakdown:**

| Argument | Meaning |
|----------|---------|
| `-i` | Input directory (contains all interesting test cases) |
| `-o` | Output directory for minimized corpus |

---

### Phase 7 — Reproduce and analyse crashes

```bash
./bzip2_fuzzer -d -c outdec/default/crashes/id:000001,sig:11,src:000225,time:718432,execs:953516,op:havoc,rep:2
```

```bash
AFL_DEBUG=1 afl-fuzz -i in_dec -o outdec -- ./bzip2_fuzzer -d -c @@
```

```bash
gdb --args ./bzip2_fuzzer -d -c outdec/default/crashes/id:000002,sig:11,src:000444,time:11876893,execs:13608266,op:havoc,rep:3
```

```bash
ls outdec/default/hangs/ | wc -l
```

```bash
AFL_NO_UI=1 afl-fuzz -i in_dec -o outdec -S secondary1 -- ./bzip2_fuzzer -d -c @@
```
| Command | Purpose |
|---------|---------|
| `./bzip2_fuzzer -d -c outdec/default/crashes/id:000001,sig:11,src:000225,time:718432,execs:953516,op:havoc,rep:2` | Reproduce a crash to verify it triggers the fault |
| `AFL_DEBUG=1 afl-fuzz -i in_dec -o outdec -- ./bzip2_fuzzer -d -c @@` | Run fuzzer with detailed debug output to troubleshoot issues |
| `gdb --args ./bzip2_fuzzer -d -c outdec/default/crashes/id:000002,sig:11,src:000444,time:11876893,execs:13608266,op:havoc,rep:3` | Debug a crash using GDB (find root cause, stack trace, memory corruption) |
| `ls outdec/default/hangs/ \| wc -l` | Count the number of hang (timeout) test cases |
| `AFL_NO_UI=1 afl-fuzz -i in_dec -o outdec -S secondary1 -- ./bzip2_fuzzer -d -c @@` | Start a secondary fuzzer instance for parallel fuzzing (silent mode, no UI) |

**Crash file naming convention:**

| Field | Example | Meaning |
|-------|---------|---------|
| `id` | `000001` | Unique crash ID number |
| `sig` | `11` | Signal number (11 = SIGSEGV, 06 = SIGABRT) |
| `src` | `000225` | Source location in the fuzzer |
| `time` | `718432` | Unix timestamp when crash occurred |
| `execs` | `953516` | Total executions performed when crash found |
| `op` | `havoc` | Mutation operation used (`havoc`, `splice`, etc.) |
| `rep` | `2` | Repetition count for the mutation |

**Signal meanings:**

| Signal | Number | Meaning |
|--------|--------|---------|
| `SIGABRT` | 06 | Abort - assertion failure or `abort()` called |
| `SIGSEGV` | 11 | Segmentation fault - invalid memory access |

**Parallel fuzzing options:**

| Variable/Option | Purpose |
|-----------------|---------|
| `AFL_NO_UI=1` | Suppress fuzzer interface (reduces screen output) |
| `-S secondary1` | Specify secondary fuzzer instance name (unique) |
| Main instance | Should be started with `-M main` (not shown) |
