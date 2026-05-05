we missed photos for AFL++

Command line used to find this crash:

afl-fuzz -i in_dec -o outdec -- ./bzip2_fuzzer -d -c @@

If you can't reproduce a bug outside of afl-fuzz, be sure to set the same
memory limit. The limit used for this fuzzing session was 0 B.

Need a tool to minimize test cases before investigating the crashes or sending
them to a vendor? Check out the afl-tmin that comes with the fuzzer!

Found any cool bugs in open-source tools using afl-fuzz? If yes, please post
to https://github.com/AFLplusplus/AFLplusplus/issues/286 once the issues
 are fixed :)

 ./bzip2_fuzzer
-d
-c
@@

# environment variables:
AFL_CUSTOM_INFO_PROGRAM=./bzip2_fuzzer
AFL_CUSTOM_INFO_PROGRAM_ARGV=-d -c @@
AFL_CUSTOM_INFO_OUT=outdec/default
# command line:
'afl-fuzz' '-i' 'in_dec' '-o' 'outdec' '--' './bzip2_fuzzer' '-d' '-c' '@@'


# AFL++ Decompressing Fuzzing — All Commands

## Phase 1 — Compile with AFL++ instrumentation

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

---

## Phase 2 — Build seed corpus

```bash
mkdir in_dec
cp a.txt b.txt s3.txt s4.txt s5.txt s6.bin s7.bin in_dec/
```

```bash
bzip2 -k in_dec/*.txt in_dec/*.bin
```

---

## Phase 3 — OS tuning

```bash
echo core | sudo tee /proc/sys/kernel/core_pattern
```

```bash
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

---

## Phase 4 — Run the fuzzer (main command)

```bash
afl-fuzz -i in_dec -o outdec -- ./bzip2_fuzzer -d -c @@
```

---

## Phase 5 — Monitor progress

```bash
afl-whatsup outdec
```

```bash
afl-plot outdec/default plot_output/
```

```bash
cat outdec/default/fuzzer_stats
```

---

## Phase 6 — Minimize crashes and corpus

```bash
afl-tmin -i outdec/default/crashes/id:000000,sig:06,src:000263,time:169795,execs:213496,op:havoc,rep:1 \
         -o crash_min.bz2 -- ./bzip2_fuzzer -d -c @@
```

```bash
afl-cmin -i outdec/default/queue -o corpus_min -- ./bzip2_fuzzer -d -c @@
```

---

## Phase 7 — Reproduce and analyse crashes

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

