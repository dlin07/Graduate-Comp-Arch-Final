# SimpleScalar Next-Line Prefetcher (NLP)

**Group Members:** Damon Lin, Sree Harshitha Jonnalagadda

## Project Overview
This project implements a hardware-based **Next-Line Prefetching (NLP)** mechanism within the SimpleScalar `sim-outorder` simulator. 

The goal of this implementation is to mitigate the "memory wall" by exploiting spatial locality. When a demand miss occurs in the cache, the prefetcher aggressively fetches the subsequent memory block (Line X+1) before the processor explicitly requests it. This helps hide memory access latency and improve Instructions Per Cycle (IPC).

### Key Modifications
The following files in the SimpleScalar source code were modified:
*   **`cache.h`**: Added global visibility for the prefetch enable flag.
*   **`sim-outorder.c`**: Registered the new command-line argument `-cache:nlp`.
*   **`cache.c`**: Implemented the core logic to calculate next-line addresses, probe the cache, and issue recursive prefetch requests on demand misses.

## Prerequisites
*   GCC Compiler
*   SimpleScalar Tool Set (v3.0)
*   Alpha or PISA benchmark binaries (e.g., `compress95`, `go`)

## Compilation
To build the modified simulator, run the following commands from the root of the SimpleScalar directory:

```bash
# Clean previous builds
make clean

# Configure for Alpha ISA (or PISA if applicable)
make config-alpha

# Build the out-of-order simulator
make sim-outorder
```

## Usage
We added a new configuration flag -cache:nlp to toggle the prefetcher.
1. Running the Baseline (No Prefetching)
Run the simulator without the flag (or set it to false).
```bash
./sim-outorder -config project.cfg <benchmark_binary> <benchmark_args>
```
2. Running with Next-Line Prefetching
Enable the prefetcher by setting the flag to true.

```bash
./sim-outorder -config project.cfg -cache:nlp <benchmark_binary> <benchmark_args>
```
## Running the Benchmarks
We evaluated the prefetcher using the Compress95 and Go benchmarks. Below are the specific commands used for evaluation. Please refer to the `README` file located in the `benchmarks` directory to see how to run the benchmarks in more detail.

### Compress95 (Integer/Linear)

Baseline:

```bash
./sim-outorder -config final_project/project.cfg -redir:sim baseline_compress.out benchmarks/compress95.alpha < benchmarks/compress95.in > OUT
```
With NLP:

```bash
./sim-outorder -config final_project/project.cfg -cache:nlp -redir:sim nlp_compress.out benchmarks/compress95.alpha < benchmarks/compress95.in > OUT
```
### Go (AI/Random Access)

Baseline:

```bash
./sim-outorder -config final_project/project.cfg -redir:sim baseline_go.out benchmarks/go.alpha 50 9 benchmarks/2stone9.in > OUT
```
With NLP:

```bash
./sim-outorder -config final_projectproject.cfg -cache:nlp -redir:sim nlp_go.out benchmarks/go/alpha 50 9 benchmarks/2stone9.in > OUT
```

## Interpreting Results
After running the simulations, look for the following metrics in the output files to determine performance impact:
sim_IPC: Instructions Per Cycle (Higher is better).
dl1.miss_rate: L1 Data Cache Miss Rate (May increase due to prefetch activity).
sim_cycle: Total simulation cycles (Lower is better).

## Credits
The entire project is built upon Todd Austin's SimpleScalar simulator. This project would not be possible without his work.
