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
| `AFL_CUSTOM_INFO_PROGRAM_ARGV` | `-cfk @@` | Arguments passed to the target: `-c` (compress), `-f` (force overwrite), `-k` (keep input file), `@@` (placeholder for fuzzed input file path) |
| `AFL_CUSTOM_INFO_OUT` | `out_compress_small/default` | Directory for custom results/logs (separate from AFL's main output directory) |
| `AFL_I_DONT_CARE_ABOUT_MISSING_CRASHES` | `1` | Suppresses AFL's warning when target doesn't crash on any input |

----------------------------------------------------------------------------------------

### Execution Flow
```
'afl-fuzz' '-i' 'in_compress_small' '-o' 'out_compress_small' '-m' '50' '-d' './bzip2_fuzzer
```
| Component | Value | Description |
|-----------|-------|-------------|
| Fuzzer | `afl-fuzz` | American Fuzzy Lop fuzzer executable |
| Input Directory | `-i in_compress_small` | Directory containing seed inputs for fuzzing |
| Output Directory | `-o out_compress_small` | Directory for results (queue, crashes, hangs, etc.) |
| Memory Limit | `-m 50` | Limits child process memory to 50 MB |
| Mode | `-d` | Quick & dirty mode (skips deterministic fuzzing for speed) |
| Target | `./bzip2_fuzzer` | Target program to fuzz (receives fuzzed inputs) |

### Target Program Arguments
```
./bzip2_fuzzer
-cfk
@@
```
| Argument | Purpose |
|----------|---------|
| `./bzip2_fuzzer` | The compiled fuzzing harness for bzip2 |
| `-cfk` | bzip2 flags: `-c` (compress to stdout), `-f` (force overwrite), `-k` (keep input file) |
| `@@` | Placeholder replaced by AFL++ with the actual fuzzed input file path |