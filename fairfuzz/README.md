## fairfuzz

https://hub.docker.com/r/zjuchenyuan/fairfuzz

Source: https://github.com/carolemieux/afl-rb

```
Current Version: 2.52b
More Versions: Ubuntu 16.04, gcc 5.4, clang 3.8
Last Update: 2017/11
Language: C
Type: Mutation Fuzzer, Compile-time Instrumentation, AFL-based
Tag: targeting rare branches, ASE 2018
```

## Guidance

### Step1: System configuration & Step2: Compile target programs

Welcome to the world of fuzzing! 
In this tutorial, we will experience a simple realistic fuzzing towards [MP3Gain](http://mp3gain.sourceforge.net/) 1.6.2.

### Step1: System configuration & Step2: Compile target programs

Since AFLFast is based on AFL, these steps are equal to [AFL Guidance](https://hub.docker.com/r/zjuchenyuan/afl) Step 1 and 2. 

```
echo "" | sudo tee /proc/sys/kernel/core_pattern
echo 0 | sudo tee /proc/sys/kernel/core_uses_pid
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
echo 1 | sudo tee /proc/sys/kernel/sched_child_runs_first
echo 0 | sudo tee /proc/sys/kernel/randomize_va_space

wget https://sourceforge.net/projects/mp3gain/files/mp3gain/1.6.2/mp3gain-1_6_2-src.zip/download -O mp3gain-1_6_2-src.zip
mkdir -p mp3gain1.6.2 && cd mp3gain1.6.2
unzip ../mp3gain-1_6_2-src.zip
# build using afl-gcc
docker run --rm -w /work -it -v `pwd`:/work --privileged zjuchenyuan/afl \
    sh -c "make clean; make"

svn export https://github.com/UNIFUZZ/dockerized_fuzzing_examples/trunk/seed/mp3 seed_mp3
```

### Step3: Start Fuzzing

Here we assume you have built mp3gain in current folder and downloaded mp3 seed files.

```
mkdir -p output/fairfuzz
docker run -it --rm -w /work -v `pwd`:/work --privileged  zjuchenyuan/fairfuzz \
    afl-fuzz -i seed_mp3 -o output/fairfuzz -- ./mp3gain @@
```


### More Usage

See https://github.com/carolemieux/afl-rb

## Example Output

See https://github.com/UNIFUZZ/dockerized_fuzzing_examples/tree/master/output/fairfuzz