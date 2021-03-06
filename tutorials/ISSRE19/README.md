# ISSRE 2019 Tutorial Activity - Instructions

## Setup
We provide a VM image with LLFI and all dependencies pre-installed, with the tutorial files also included. To use the VM image please install [VirtualBox](https://www.virtualbox.org/wiki/Downloads).
You are welcome to use your own installation of LLFI, but these instructions assume you are using the VM image with specific experiment folder paths.

You can download the VirtualBox image [here](https://drive.google.com/file/d/15KK7-zy7ba-I9tkGloX_XOqipkeQtnWL/view?usp=sharing).

username: `llfi`

password: `root`

## Benchmarks
We provide two benchmarks to run fault injections for this tutorial. They are simple deterministic programs with predefined inputs.

### sqrt
This program implements a square root operation using Taylor series. The predefined input to the sqrt program is the floating point number `123.123`.

### matmult
This program multiplies two 20 by 20 matrices (predefined). The program outputs all the values of the result matrix.

## Part 1: Fault Injection
1. Open a terminal and navigate to the ISSRE19 folder on the desktop: `cd ~/Desktop/ISSRE19/`

2. Navigate to the first benchmark folder: `cd 1-sqrt`

3. The compiled LLVM IR file (.ll) is already provided. To re-compile from C to IR use the command:

`clang -S -emit-llvm sqrt.c -o sqrt.ll`

4. Instrumentation phase:

`$LLFI/instrument --readable sqrt.ll`

5. Profiling phase:

`$LLFI/profile llfi/sqrt-profiling.exe`

6. Fault injection phase:

`$LLFI/injectfault llfi/sqrt-faultinjection.exe`

7. Execute the `measure.py` script in each benchmark folder to measure the SDC and crash rates.

`python3 ./measure.py`

8. (Optional) Navigate to the second benchmark folder `cd ../2-matmult` and repeat Steps 4-7 (make sure to change `sqrt` to `matmult` in the commands). Observe the differences in SDC and crash rates.


## Part 2: Specify injection targets

1. Open the input.yaml file and change the target instruction type from `all` to `add` and `sub` instructions only.

```
compileOption:
    instSelMethod:
      - insttype:
          include: 
            - add
            - sub
          exclude:
            - ret

    regSelMethod: regloc
    regloc: dstreg

runOption:
    - run:
        numOfRuns: 2000
        fi_type: bitflip
        
defaultTimeout: 30

```

2. Repeat the fault injection experiments and observe how the resulting SDC and crash rates are affected. (Please note: to re-run FI experiments in the same folder you must delete or re-name the existing `llfi` folder, e.g., `rm -rf ./llfi/ ./llfi.*`)


## Part 3: Trace analysis
1. Navigate to the last benchmark folder: `cd ../3-matmult_trace`

2. Instrumentation phase:

`$LLFI/instrument --readable matmult.ll`

5. Profiling phase:

`$LLFI/profile llfi/matmult-profiling.exe`

6. Fault injection phase:

`$LLFI/injectfault llfi/matmult-faultinjection.exe`

7. Analyze trace propagation:

`$LLFItools/tracediff llfi/baseline/llfi.stat.trace.prof.txt llfi/llfi_stat_output/llfi.stat.trace.0-5.txt > diffReport.txt`(Modify the 5 to the desired trace number.)

`$LLFItools/traceontograph diffReport.txt llfi.stat.graph.dot > tracedGraph.dot`

`$LLFItools/zgrviewer/run.sh tracedGraph.dot`


