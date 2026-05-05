# environment variables:
AFL_CUSTOM_INFO_PROGRAM=./bzip2_fuzzer
AFL_CUSTOM_INFO_PROGRAM_ARGV=-dfk @@
AFL_CUSTOM_INFO_OUT=out_new/default
# command line:
'afl-fuzz' '-m' '50' '-i' 'in' '-o' 'out_new' '-d' './bzip2_fuzzer' '-dfk' '@@'

./bzip2_fuzzer
-dfk
@@

Command line used to find this crash:

afl-fuzz -m 50 -i in -o out_new -d ./bzip2_fuzzer -dfk @@

If you can't reproduce a bug outside of afl-fuzz, be sure to set the same
memory limit. The limit used for this fuzzing session was 50.0 MB.

Need a tool to minimize test cases before investigating the crashes or sending
them to a vendor? Check out the afl-tmin that comes with the fuzzer!

Found any cool bugs in open-source tools using afl-fuzz? If yes, please post
to AFLplusplus/AFLplusplus#286 once the issues
 are fixed :)

 # environment variables:
AFL_CUSTOM_INFO_PROGRAM=./bzip2_afl
AFL_CUSTOM_INFO_PROGRAM_ARGV=-z @@
AFL_CUSTOM_INFO_OUT=out/default
# command line:
'afl-fuzz' '-i' '-' '-o' 'out' '-m' 'none' '--' './bzip2_afl' '-z' '@@'

./bzip2_afl
-z
@@

Command line used to find this crash:

afl-fuzz -i - -o out -m none -- ./bzip2_afl -z @@

If you can't reproduce a bug outside of afl-fuzz, be sure to set the same
memory limit. The limit used for this fuzzing session was 0 B.

Need a tool to minimize test cases before investigating the crashes or sending
them to a vendor? Check out the afl-tmin that comes with the fuzzer!

Found any cool bugs in open-source tools using afl-fuzz? If yes, please post
to AFLplusplus/AFLplusplus#286 once the issues
 are fixed :)

Command line used to find this crash:

afl-fuzz -i in -o out -m none -- ./bzip2_afl -dfk @@

If you can't reproduce a bug outside of afl-fuzz, be sure to set the same
memory limit. The limit used for this fuzzing session was 0 B.

Need a tool to minimize test cases before investigating the crashes or sending
them to a vendor? Check out the afl-tmin that comes with the fuzzer!

Found any cool bugs in open-source tools using afl-fuzz? If yes, please post
to AFLplusplus/AFLplusplus#286 once the issues
 are fixed :)

 # environment variables:
AFL_CUSTOM_INFO_PROGRAM=./bzip2_fuzzer
AFL_CUSTOM_INFO_PROGRAM_ARGV=-cfk @@
AFL_CUSTOM_INFO_OUT=out_compress_small/default
AFL_I_DONT_CARE_ABOUT_MISSING_CRASHES=1
# command line:
'afl-fuzz' '-i' 'in_compress_small' '-o' 'out_compress_small' '-m' '50' '-d' './bzip2_fuzzer' '-cfk' '@@'

./bzip2_fuzzer
-cfk
@@

# environment variables:
AFL_CUSTOM_INFO_PROGRAM=./bzip2_fuzzer
AFL_CUSTOM_INFO_PROGRAM_ARGV=-cfk @@
AFL_CUSTOM_INFO_OUT=out_compress/default
AFL_I_DONT_CARE_ABOUT_MISSING_CRASHES=1
# command line:
'afl-fuzz' '-i' 'in_compress' '-o' 'out_compress' '-m' '50' '-d' './bzip2_fuzzer' '-cfk' '@@'

./bzip2_fuzzer
-cfk
@@

# environment variables:
AFL_CUSTOM_INFO_PROGRAM=./bzip2_afl
AFL_CUSTOM_INFO_PROGRAM_ARGV=-z @@
AFL_CUSTOM_INFO_OUT=out/default
# command line:
'afl-fuzz' '-i' 'in' '-o' 'out' '-m' 'none' '--' './bzip2_afl' '-z' '@@'

./bzip2_afl
-z
@@

genhtml coverage.info --output-directory cov_report





wget https://sourceware.org/pub/bzip2/bzip2-1.0.8.tar.gz
tar -xf bzip2-1.0.8.tar.gz && cd bzip2-1.0.8
CC=afl-clang-fast make
cp bzip2 ../bzip2_fuzzer
cd ..


cd bzip2-1.0.8
CC=gcc CFLAGS="-fprofile-arcs -ftest-coverage" make clean all
cp bzip2 ../bzip2_cov
cd ..


mkdir in_compress_small
echo "test1" > in_compress_small/s1.txt
echo "hello" > in_compress_small/s2.txt
echo "12345" > in_compress_small/s3.txt
echo "abcde" > in_compress_small/s4.txt
echo "fuzz!" > in_compress_small/s5.txt
echo "kfupm" > in_compress_small/s6.txt


export AFL_I_DONT_CARE_ABOUT_MISSING_CRASHES=1

afl-fuzz \
  -i in_compress_small \
  -o out_compress_small \
  -m 50 \
  -d \
  ./bzip2_fuzzer -cfk @@


  afl-cov \
  -d out_compress_small/default \
  --coverage-cmd "./bzip2_cov -d AFL_FILE" \
  --code-dir . \
  --afl-file AFL_FILE \
  --overwrite \
  --ignore-core-pattern


  lcov \
  --capture \
  --directory . \
  --output-file coverage.info

  genhtml coverage.info --output-directory cov_report
