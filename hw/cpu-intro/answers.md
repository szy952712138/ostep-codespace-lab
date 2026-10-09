# OSTEP Chapter 4 Homework - Predictions

## Q1
- Prediction:
```
Time  PID 0    PID 1    CPU  IOs
1-5   RUN:cpu  READY    1
6-10  DONE     RUN:cpu  1
```
Total time: 10 ticks. CPU utilization: 100%. I/O utilization: 0%.
- Reasoning: Both processes contain only five CPU instructions. PID 0 runs first and finishes, then PID 1 runs; the CPU is never idle.

## Q2
- Prediction:
```
Time   PID 0    PID 1       CPU  IOs
1-4    RUN:cpu  READY       1
5      DONE     RUN:io      1
6-10   DONE     BLOCKED          1
11*    DONE     RUN:io_done 1
```
Total time: 11 ticks. CPU utilization: 6/11 = 54.55%. I/O utilization: 5/11 = 45.45%.
- Reasoning: PID 0 uses four CPU ticks before PID 1 begins I/O. With no other ready process, the CPU is idle during the five I/O wait ticks.

## Q3
- Prediction:
```
Time   PID 0       PID 1    CPU  IOs
1      RUN:io      READY    1
2-5    BLOCKED     RUN:cpu  1    1
6      BLOCKED     DONE          1
7*     RUN:io_done DONE     1
```
Total time: 7 ticks. CPU utilization: 6/7 = 85.71%. I/O utilization: 5/7 = 71.43%.
- Reasoning: SWITCH_ON_IO lets PID 1 complete all four CPU instructions while PID 0 is blocked, leaving only one idle tick.

## Q4
- Prediction:
```
Time   PID 0       PID 1    CPU  IOs
1      RUN:io      READY    1
2-6    BLOCKED     READY         1
7*     RUN:io_done READY    1
8-11   DONE        RUN:cpu  1
```
Total time: 11 ticks. CPU utilization: 6/11 = 54.55%. I/O utilization: 5/11 = 45.45%.
- Reasoning: SWITCH_ON_END does not let PID 1 run while PID 0 is blocked, so the CPU is idle for the entire I/O wait.

## Q5
- Prediction:
```
Time   PID 0       PID 1    CPU  IOs
1      RUN:io      READY    1
2-5    BLOCKED     RUN:cpu  1    1
6      BLOCKED     DONE          1
7*     RUN:io_done DONE     1
```
Total time: 7 ticks. CPU utilization: 6/7 = 85.71%. I/O utilization: 5/7 = 71.43%.
- Reasoning: SWITCH_ON_IO overlaps PID 1's CPU work with PID 0's I/O wait, reducing total time from 11 ticks in Q4 to 7 ticks.

## Q6
- Prediction:
```
Time    PID 0        CPU work
1       RUN:io       PID 0 starts I/O
2-6     BLOCKED      PID 1 runs five CPU instructions
7-11    READY        PID 2 runs five CPU instructions
12-16   READY        PID 3 runs five CPU instructions
17      RUN:io       PID 0 starts its second I/O
18-22   BLOCKED      CPU idle
23*     RUN:io_done  I/O completion handled
24      RUN:io       PID 0 starts its third I/O
25-29   BLOCKED      CPU idle
30*     RUN:io_done  I/O completion handled
31      RUN:io       PID 0 starts the last I/O
32-36   BLOCKED      CPU idle
37*     RUN:io_done  I/O completion handled
```
Total time: 37 ticks. CPU utilization: 21/37 = 56.76%. I/O utilization: 15/37 = 40.54%.
- Reasoning: With IO_RUN_LATER, PID 0 waits behind all three CPU-bound processes after its first I/O. The I/O device is idle while PID 0 is ready but not scheduled.

## Q7
- Prediction:
```
Time    PID 0        CPU work
1       RUN:io       PID 0 starts I/O
2-6     BLOCKED      PID 1 runs five CPU instructions
7*      RUN:io_done  I/O completion handled immediately
8       RUN:io       PID 0 starts the next I/O
9-13    BLOCKED      PID 2 runs five CPU instructions
14*     RUN:io_done  I/O completion handled immediately
15      RUN:io       PID 0 starts the last I/O
16-20   BLOCKED      PID 3 runs five CPU instructions
21*     RUN:io_done  I/O completion handled immediately
```
Total time: 21 ticks. CPU utilization: 21/21 = 100%. I/O utilization: 15/21 = 71.43%.
- Reasoning: IO_RUN_IMMEDIATE lets PID 0 handle each completion and issue its next I/O before it waits behind the CPU-bound processes. This avoids the long device-idle gap in Q6.

## Q8
- Prediction:
  - Seed 1: PID 0 = cpu, io, io; PID 1 = io, cpu, cpu.
  - Seed 2: PID 0 = io, io, cpu; PID 1 = cpu, io, io.
  - Seed 3: PID 0 = cpu, io, cpu; PID 1 = io, io, cpu.
  - The default policy should overlap ready CPU work with I/O waits. IO_RUN_IMMEDIATE should help most when a process has consecutive I/O instructions (seed 2 PID 0 and seed 3 PID 1). SWITCH_ON_END should produce more CPU-idle time because the other process cannot run during an I/O wait.
- Reasoning: I predicted from the instruction lists before requesting the simulator's full trace. The scheduling policy changes when a ready process is selected, not the instruction list itself.
