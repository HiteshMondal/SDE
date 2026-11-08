# Operating System (OS)

A complete guide covering core operating system concepts with detailed explanations, worked numerical examples, and scenario-based questions and answers.

---

# Introduction to Operating Systems

## What is an Operating System?

An Operating System (OS) is system software that manages the computer's hardware and provides a convenient, safe and efficient environment in which application programs can run. It acts as an **intermediary** between the user/applications and the hardware, and as a **resource manager** that allocates the CPU, memory, storage and I/O devices among competing programs.

## Goals and Functions

| Function | What the OS does |
|---|---|
| Process management | Creates, schedules, synchronizes and terminates processes and threads |
| Memory management | Allocates and protects memory, implements virtual memory |
| File system management | Organizes data into files and directories on storage devices |
| I/O and device management | Controls devices through drivers; buffers, caches and schedules I/O |
| Protection and security | Controls who may access which resource |
| User interface | Command-line shell or graphical interface |
| Networking | Sockets, protocol stacks, remote file access |
| Accounting and error handling | Tracks usage, detects and reports errors |

## Computer System Organization

```text
   +---------+    +----------------+    +-------------------+
   |   CPU   |<-->|  Memory (RAM)  |<-->|  Device controllers|--> disk, keyboard, NIC, GPU ...
   +---------+    +----------------+    +-------------------+
        ^                 ^                       |
        |_________________|__ system bus _________|
```

- **Interrupts** let devices signal the CPU asynchronously. The CPU stops, saves its state, runs an **interrupt service routine (ISR)** found through the **interrupt vector table**, and then resumes.
- **DMA (Direct Memory Access)**: a controller moves blocks of data between a device and memory without the CPU copying each byte; it interrupts the CPU once when the transfer finishes.

### Memory Hierarchy

| Level | Typical size | Typical access time |
|---|---|---|
| Registers | Bytes | < 1 ns |
| L1/L2/L3 cache | KB – MB | 1 – 30 ns |
| Main memory (RAM) | GB | 50 – 100 ns |
| SSD | 100s of GB – TB | 50 – 150 µs |
| Hard disk | TB | 5 – 10 ms |

**Locality of reference** makes the hierarchy work: *temporal* locality (recently used data is used again) and *spatial* locality (data near recently used data is used soon).

```text
Average memory access time (AMAT) = hit time + miss rate × miss penalty
```

## Dual-Mode Operation and Protection

The hardware provides at least two modes, indicated by a **mode bit**:

| Mode | Used by | Privileges |
|---|---|---|
| **Kernel (supervisor) mode** | The OS | Can execute **privileged instructions** (I/O, changing page tables, disabling interrupts, halting the CPU) |
| **User mode** | Applications | Limited instruction set; privileged instructions cause a trap |

A program switches from user to kernel mode only through a **system call**, an **interrupt** or an **exception**, and the OS returns to user mode before resuming the program.

**Other protection mechanisms:** a **timer** (guarantees the OS regains control, so no program can hold the CPU forever), **memory protection** (base/limit registers or page tables), and **I/O protection** (all I/O instructions are privileged).

## Interrupts, Traps and Exceptions

| Kind | Source | Synchronous? | Examples |
|---|---|---|---|
| Interrupt (hardware) | External device | No (asynchronous) | Keyboard press, disk completion, timer tick |
| Exception (fault/trap) | The running instruction | Yes | Divide by zero, invalid memory access, **page fault** |
| Trap / software interrupt | Deliberate instruction | Yes | `syscall`, `int 0x80` (system call) |

**Interrupt handling steps:** (1) hardware saves the program counter and flags, (2) switches to kernel mode, (3) looks up the ISR in the vector table, (4) the ISR saves other registers and services the device, (5) restores state and returns to the interrupted program.

Interrupts can be **masked** (disabled) briefly while critical kernel data is updated. Modern kernels split interrupt work into a short **top half** (acknowledge the device) and a deferred **bottom half** (softirq/tasklet/thread) for the remaining work.

## Operating System Structures

| Structure | Idea | Examples | Pros / Cons |
|---|---|---|---|
| Monolithic | The whole OS runs in one kernel address space | Traditional UNIX, Linux (with loadable modules) | Fast (direct calls) / a bug in any part can crash the system |
| Layered | OS built as layers; each uses only the layer below | THE system | Easy to verify / overhead, hard to order layers |
| Microkernel | Only the minimum (scheduling, IPC, basic memory) in the kernel; file systems, drivers, networking run as user-level servers | Mach, QNX, MINIX | Reliable, extensible / message-passing overhead |
| Modular | Core kernel plus dynamically loadable modules | Linux, Solaris | Flexible and efficient |
| Hybrid | Mix of monolithic and microkernel ideas | Windows NT family, macOS (XNU) | Compromise |
| Exokernel / Unikernel | Minimal OS exposing hardware or linking the OS with one application | Research and cloud systems | Very light |

## Types of Operating Systems

| Type | Description |
|---|---|
| Batch | Jobs with similar needs are grouped and run without user interaction |
| Multiprogramming | Several programs kept in memory; the CPU switches when one waits for I/O → better CPU utilization |
| Time-sharing (multitasking) | CPU switches rapidly among programs so that users get interactive response |
| Multiprocessor / multicore | Several CPUs share memory and the OS (symmetric multiprocessing, SMP) |
| Real-time | Must meet deadlines. **Hard** real-time: missing a deadline is a failure (flight control). **Soft** real-time: occasional misses are tolerable (video playback) |
| Distributed | Multiple machines appear as one system |
| Embedded / mobile | Small, dedicated, resource-constrained (Android, FreeRTOS) |

| Term | Meaning |
|---|---|
| Multiprogramming | Many programs **in memory** at once; goal is CPU utilization |
| Multitasking | Multiprogramming plus time slicing; goal is responsiveness |
| Multiprocessing | More than one **CPU/core** executing simultaneously |
| Multithreading | More than one **thread** within a process |

## Operating System Services Seen by Users and Programs

Program execution, I/O operations, file-system manipulation, inter-process communication, error detection, resource allocation, accounting, and protection and security.

## Booting an Operating System

```text
Power on → firmware (BIOS/UEFI) runs POST (hardware self-test)
        → loads the bootloader (GRUB, Windows Boot Manager) from the boot device
        → bootloader loads the kernel image into memory and passes control to it
        → kernel initializes memory management, scheduler, drivers, mounts the root file system
        → starts the first user process (init / systemd, PID 1)
        → init starts services and finally the login prompt or desktop
```

## Virtualization and Containers

| Technology | How it isolates | Weight |
|---|---|---|
| **Virtual machine** | A **hypervisor** emulates hardware; each VM runs its own full OS kernel. *Type 1* runs directly on hardware (Xen, KVM, ESXi); *Type 2* runs as an application on a host OS (VirtualBox) | Heavy (GBs, seconds to boot) |
| **Container** | Processes share the **host kernel** but are isolated by **namespaces** (own view of process IDs, network, mounts, users) and limited by **cgroups** (CPU, memory, I/O quotas) | Light (MBs, milliseconds to start) |

---

# System Calls

## What is a System Call?

A **system call** is the controlled entry point through which a user program requests a service from the kernel (create a process, read a file, allocate memory, send data). Because user programs cannot execute privileged instructions, they must ask the kernel to do so on their behalf.

## System Call vs API vs Library Function

```text
Application  →  library/API function (printf, fopen)  →  system call (write, open)  →  kernel
```

| Aspect | Library function | System call |
|---|---|---|
| Runs in | User mode (may call a system call inside) | Kernel mode |
| Cost | Cheap | Costly (mode switch, argument checks) |
| Example | `printf()`, `malloc()`, `fopen()` | `write()`, `brk()`/`mmap()`, `open()` |
| Portability | Standardized (C library) | OS-specific (POSIX defines the common ones) |

Libraries reduce the number of expensive system calls, for example by **buffering**: `printf` collects text in a user-space buffer and calls `write` once.

## What Happens During a System Call

```text
1. The program calls a wrapper (e.g., read) in the C library.
2. The wrapper puts the system call number in a register (e.g., rax on x86-64) and the arguments in other registers.
3. It executes a trap instruction (syscall / int 0x80 / svc).
4. The CPU switches to kernel mode and jumps to the kernel's system-call handler.
5. The handler uses the number to index the system call table and calls the right kernel routine.
6. The kernel validates the arguments (pointers, permissions), performs the work (may block the process).
7. The result (or an error code) is stored and the CPU returns to user mode.
8. The wrapper returns to the caller (on error, returns -1 and sets errno).
```

### Passing Parameters

| Method | Description |
|---|---|
| Registers | Fastest; limited number of arguments (most common) |
| Memory block (table) | Parameters stored in memory; its address is passed in a register (used by Linux for many arguments) |
| Stack | Parameters pushed by the program and popped by the kernel |

## Categories of System Calls

| Category | Linux/POSIX examples | Windows examples |
|---|---|---|
| Process control | `fork`, `execve`, `exit`, `wait`, `waitpid`, `kill`, `getpid` | `CreateProcess`, `ExitProcess`, `WaitForSingleObject` |
| File management | `open`, `read`, `write`, `lseek`, `close`, `unlink`, `stat`, `chmod` | `CreateFile`, `ReadFile`, `WriteFile`, `CloseHandle` |
| Device management | `ioctl`, `read`, `write` on device files | `DeviceIoControl` |
| Information maintenance | `getpid`, `time`, `uname`, `sysinfo` | `GetCurrentProcessId`, `GetSystemTime` |
| Communication | `pipe`, `socket`, `send`, `recv`, `shmget`, `mmap`, `msgget` | `CreatePipe`, `CreateFileMapping` |
| Memory management | `brk`, `mmap`, `munmap`, `mprotect` | `VirtualAlloc`, `VirtualFree` |
| Protection | `chmod`, `chown`, `umask`, `setuid` | `SetFileSecurity` |

## Example: A Tiny Program and Its System Calls

```c
#include <fcntl.h>
#include <unistd.h>

int main(void) {
    int fd = open("data.txt", O_RDONLY);          // system call: open
    char buf[100];
    ssize_t n = read(fd, buf, sizeof buf);        // system call: read
    write(STDOUT_FILENO, buf, n);                 // system call: write to the screen
    close(fd);                                    // system call: close
    return 0;                                     // leads to exit()
}
```

`strace ./a.out` on Linux prints every system call a program makes, which is the standard tool for debugging "why is my program stuck or failing?".

## Cost of a System Call

A system call costs far more than an ordinary function call (hundreds of nanoseconds to microseconds) because of the mode switch, saving registers, argument validation and cache effects. Frequently used cheap calls (such as reading the time) may be served from user space by the **vDSO** mechanism on Linux, without entering the kernel.

## Standard File Descriptors

Every UNIX process starts with three open descriptors: **0 = stdin**, **1 = stdout**, **2 = stderr**. A file descriptor is a small integer index into the process's file-descriptor table. `open()` returns the **lowest unused** descriptor.

---

# Processes

## Program vs Process

A **program** is a passive file of instructions on disk. A **process** is a program **in execution**: code plus current activity (program counter, registers, stack, heap, open files).

### Memory Layout of a Process

```text
High addresses  +------------------+
                |      Stack       |  function calls, local variables (grows down)
                |        ↓         |
                |   (free space)   |
                |        ↑         |
                |       Heap       |  dynamic memory: malloc/new (grows up)
                +------------------+
                |  Data (.bss)     |  uninitialized globals (zeroed)
                |  Data (.data)    |  initialized globals and statics
                +------------------+
Low addresses   |  Text (code)     |  read-only machine instructions
                +------------------+
```

## Process Control Block (PCB)

The OS keeps a **PCB** (also called task control block, `task_struct` in Linux) for every process:

| Field | Meaning |
|---|---|
| Process ID (PID), parent PID | Identity |
| Process state | New, ready, running, waiting, terminated |
| Program counter | Address of the next instruction |
| CPU registers | Saved when the process is not running |
| Scheduling information | Priority, queue pointers, time used |
| Memory-management information | Page table pointer, base/limit registers |
| Accounting information | CPU time used, time limits |
| I/O status information | Open files, allocated devices |

## Process States

```text
            admitted              scheduler dispatch
   New ───────────────► Ready ─────────────────────► Running ──────► Terminated
                          ▲  ◄─────────────────────     │    exit
                          │      interrupt (time slice  │
                          │      expired / preempted)   │ I/O or event wait
                          │                             ▼
                          └───────────────────────── Waiting (Blocked)
                              I/O or event completion
```

| State | Meaning |
|---|---|
| New | Being created |
| Ready | In memory, waiting for the CPU |
| Running | Instructions being executed (at most one per CPU core) |
| Waiting / Blocked | Waiting for an event (I/O completion, a signal, a lock) |
| Terminated | Finished; PCB waits to be cleaned up |
| Suspended ready / suspended blocked | Swapped out to disk by the medium-term scheduler (extra states in some models) |

Valid transitions: New→Ready, Ready→Running, Running→Ready (preemption), Running→Waiting, Waiting→Ready, Running→Terminated. **Waiting→Running is not allowed** (the process must go through Ready) and **Ready→Waiting is not allowed** (only a running process can issue a blocking request).

## Scheduling Queues and Schedulers

| Queue | Holds |
|---|---|
| Job queue | All processes in the system |
| Ready queue | Processes in memory ready to run |
| Device (I/O) queues | Processes waiting for a particular device |

| Scheduler | Also called | Decides | Frequency |
|---|---|---|---|
| Long-term | Job scheduler | Which jobs are admitted into memory (controls the **degree of multiprogramming**) | Rare (seconds to minutes) |
| Short-term | CPU scheduler | Which ready process runs next | Very frequent (milliseconds) |
| Medium-term | Swapper | Which processes to swap out and back in | Occasional |

A good job mix includes both **CPU-bound** processes (long computations) and **I/O-bound** processes (many short CPU bursts), so that both the CPU and the devices stay busy.

## Context Switch

A **context switch** saves the state of the running process in its PCB and loads the saved state of the next process.

```text
Process P0 running → interrupt or system call → save P0 state into PCB0
                  → scheduler picks P1 → load P1 state from PCB1 → P1 running
```

- It is **pure overhead**: the system does no useful work during the switch (typically a few microseconds, plus indirect cost: cache and TLB misses).
- Threads of the same process switch faster than processes (no address-space change).
- Hardware support such as multiple register sets can reduce the cost.

## Process Creation and Termination (UNIX)

Processes form a **tree**: each process has a parent. In Linux, PID 1 (`init`/`systemd`) is the root.

### fork(), exec(), wait(), exit()

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>

int main(void) {
    pid_t pid = fork();                 // creates a nearly identical child process
    if (pid < 0) {
        perror("fork failed");          // no child was created
        exit(1);
    } else if (pid == 0) {              // fork returns 0 in the CHILD
        execlp("ls", "ls", "-l", NULL); // replace the child's program with /bin/ls
        perror("exec failed");          // reached only if exec fails
        exit(1);
    } else {                            // fork returns the child's PID in the PARENT
        int status;
        waitpid(pid, &status, 0);       // parent waits for the child to finish
        printf("Child %d finished\n", pid);
    }
    return 0;
}
```

| Call | Behaviour |
|---|---|
| `fork()` | Duplicates the calling process. Returns **0 to the child**, the **child's PID to the parent**, and **-1 on failure**. Child gets a copy of the parent's memory (implemented lazily with **copy-on-write**), open files, and a new PID. |
| `exec*()` | **Replaces** the current program image with a new program. PID stays the same; it does not return on success. |
| `wait()/waitpid()` | Parent blocks until a child terminates and collects its exit status. |
| `exit()` | Terminates the process, closes files, releases memory; the exit status goes to the parent. |

> The shell runs a command by `fork()` then `exec()` in the child, and `wait()` in the parent (unless the command is followed by `&`).

### Counting Processes Created by fork()

If a program executes *n* `fork()` calls in sequence (no conditions), every existing process splits at each call:

```text
Total processes = 2ⁿ        Child processes created = 2ⁿ − 1
```

```c
fork();
fork();
printf("hello\n");   // 2 forks → 4 processes → "hello" printed 4 times
```

For conditional forks use the return value: in `if (fork() && fork())`, the first `fork()` returns non-zero only in the parent, so only the parent evaluates the second `fork()`; the first child skips it (short-circuit evaluation). Total processes = **3**.

### Zombie and Orphan Processes

| Kind | What it is | Cause | Cleanup |
|---|---|---|---|
| **Zombie** | Finished process whose PCB entry remains | Parent has not yet called `wait()` | Parent calls `wait()`; if the parent never does, zombies pile up and waste PID slots |
| **Orphan** | Running process whose parent has terminated | Parent exited first | **Adopted by `init`** (PID 1), which waits for it |
| **Daemon** | Background service process with no controlling terminal | Created deliberately (`sshd`, `cron`) | Managed by init system |

### Process Termination Reasons

Normal exit, error exit, fatal error (illegal instruction, invalid memory access), killed by another process (`kill`), or killed by the kernel (for example the out-of-memory killer). When a parent terminates, the OS may either terminate its children (cascading termination, as in some systems) or reparent them (UNIX).

## Signals

A **signal** is a software notification delivered to a process.

| Signal | Number | Default action | Notes |
|---|---|---|---|
| `SIGINT` | 2 | Terminate | Ctrl+C |
| `SIGKILL` | 9 | Terminate | **Cannot be caught or ignored** |
| `SIGTERM` | 15 | Terminate | Polite request; can be caught for clean shutdown |
| `SIGSEGV` | 11 | Terminate + core dump | Invalid memory access |
| `SIGCHLD` | 17 (Linux) | Ignore | Sent to the parent when a child terminates |
| `SIGSTOP` / `SIGCONT` | 19 / 18 | Stop / continue | `SIGSTOP` cannot be caught |

---

# Threads

## What is a Thread?

A **thread** is the basic unit of CPU execution inside a process. Threads of one process **share** the code, data, heap, open files and signal handlers, but each thread has its **own** program counter, registers, **stack** and thread ID.

```text
          Single-threaded process              Multithreaded process
   +----------------------------+        +----------------------------------+
   | code | data | files        |        | code | data | files  (shared)     |
   | registers | stack          |        | reg1 | reg2 | reg3                |
   |   one thread               |        | stk1 | stk2 | stk3  (per thread)  |
   +----------------------------+        +----------------------------------+
```

## Benefits of Multithreading

| Benefit | Explanation |
|---|---|
| Responsiveness | A UI thread stays responsive while another thread does a long task |
| Resource sharing | Threads share memory by default; no IPC needed |
| Economy | Creating and switching threads is cheaper than for processes |
| Scalability | Threads run in parallel on different cores |

## Process vs Thread

| Aspect | Process | Thread |
|---|---|---|
| Address space | Own (isolated) | **Shared** with other threads of the process |
| Creation / context switch | Slow | Fast |
| Communication | Needs IPC (pipes, messages, shared memory) | Direct, through shared variables (needs synchronization) |
| Failure impact | Crash is contained | A crash (or bad pointer) can kill the **whole process** |
| Own resources | Memory, files, PCB | Stack, registers, program counter, thread-local storage |
| What `fork()` copies | Whole process | Only the calling thread is copied into the child |

## User-Level vs Kernel-Level Threads

| Aspect | User-level threads | Kernel-level threads |
|---|---|---|
| Managed by | A thread library in user space | The OS kernel |
| Kernel aware of them? | No (sees one process) | Yes |
| Creation / switch cost | Very low (no system call) | Higher (system call, kernel scheduling) |
| Blocking system call | **Blocks the whole process** (all threads) | Blocks only that thread |
| Multicore parallelism | **No** (kernel schedules one entity) | **Yes** |
| Examples | Early green threads | Linux (NPTL), Windows threads |

## Multithreading Models

| Model | Mapping | Result |
|---|---|---|
| Many-to-One | Many user threads → one kernel thread | No parallelism; one blocking call blocks all |
| One-to-One | Each user thread → one kernel thread | Full concurrency; limit on number of threads (Linux, Windows) |
| Many-to-Many | Many user threads → a smaller or equal number of kernel threads | Flexible but complex |

## POSIX Threads (pthreads) Example

```c
#include <pthread.h>
#include <stdio.h>

void *worker(void *arg) {
    int id = *(int *)arg;
    printf("Thread %d running\n", id);
    return NULL;
}

int main(void) {
    pthread_t t[3];
    int ids[3] = {0, 1, 2};
    for (int i = 0; i < 3; i++) pthread_create(&t[i], NULL, worker, &ids[i]);
    for (int i = 0; i < 3; i++) pthread_join(t[i], NULL);     // wait for all threads
    return 0;
}
```

Compile with `gcc prog.c -pthread`.

## Thread Pools

Instead of creating a thread for every request (costly, and unbounded), a server creates a fixed number of worker threads in advance and hands them tasks from a queue. This bounds resource use and removes thread-creation delay.

## Issues with Threads

| Issue | Discussion |
|---|---|
| `fork()` and `exec()` | `fork()` may copy only the calling thread (POSIX) or all threads; `exec()` replaces the whole process |
| Signal handling | A signal can be delivered to one thread, specific threads or all threads |
| Thread cancellation | *Asynchronous* (immediate, risky) vs *deferred* (thread checks at safe points) |
| Thread-local storage (TLS) | Data private to each thread (such as `errno`) |
| Shared data | Needs synchronization, otherwise race conditions occur |

## Amdahl's Law

If a fraction *S* of a program is **serial** (cannot be parallelized) and the rest is run on *N* cores:

```text
Speedup  ≤  1 / ( S + (1 − S) / N )          Maximum speedup as N → ∞ is 1 / S
```

**Example:** 25% serial (S = 0.25) on 4 cores: speedup = 1 / (0.25 + 0.75/4) = 1 / 0.4375 ≈ **2.29**. Even with unlimited cores the speedup can never exceed 1/0.25 = **4**.

---

# CPU Scheduling

## Why Scheduling?

With many ready processes and few CPUs, the **short-term (CPU) scheduler** selects which process runs next. Processes alternate between **CPU bursts** and **I/O bursts**; the scheduler tries to keep the CPU busy while giving every process fair and timely service.

## Preemptive vs Non-Preemptive

| Type | Meaning | When scheduling happens |
|---|---|---|
| **Non-preemptive** | Once a process gets the CPU it keeps it until it finishes or blocks | Running → Waiting, or Running → Terminated |
| **Preemptive** | The OS can take the CPU away (time slice ends, higher-priority process arrives) | Also on Running → Ready and Waiting → Ready |

The **dispatcher** is the module that gives control of the CPU to the chosen process (context switch, switch to user mode, jump to the right instruction). **Dispatch latency** is the time it takes.

## Terminology and Formulas

| Term | Meaning |
|---|---|
| Arrival Time (AT) | When the process enters the ready queue |
| Burst Time (BT) | CPU time needed |
| Completion Time (CT) | When the process finishes |
| **Turnaround Time (TAT)** | `CT − AT` |
| **Waiting Time (WT)** | `TAT − BT` (time spent in the ready queue) |
| **Response Time (RT)** | `(time of first CPU allocation) − AT` |
| Throughput | Number of processes completed per unit time |
| CPU utilization | Percentage of time the CPU is busy |

```text
TAT = CT − AT          WT = TAT − BT          RT = first_run − AT
```

**Goal:** maximize CPU utilization and throughput; minimize turnaround, waiting and response time.

## Scheduling Algorithms

### First-Come First-Served (FCFS)

Processes run in the order of arrival. **Non-preemptive**, simple, uses a FIFO queue.

- **Convoy effect:** many short processes wait behind one long CPU-bound process, which lowers utilization.
- Average waiting time can be very high and depends heavily on arrival order.

### Shortest Job First (SJF)

Select the process with the **smallest next CPU burst**. Non-preemptive.

- **Provably optimal** for minimizing the **average waiting time** (among non-preemptive algorithms) when all jobs are available.
- Problem: the next burst length is **unknown**; it is predicted using exponential averaging: `τ(n+1) = α·t(n) + (1 − α)·τ(n)` (t = actual last burst, τ = last prediction, 0 ≤ α ≤ 1).
- Can cause **starvation** of long jobs.

### Shortest Remaining Time First (SRTF)

The **preemptive** version of SJF: when a new process arrives with a burst shorter than the remaining time of the running process, it preempts it. Gives the minimum average waiting time overall, but has higher overhead and also starves long jobs.

### Priority Scheduling

The CPU goes to the process with the highest priority (smaller number usually means higher priority). Can be preemptive or not.

- **Starvation** (indefinite blocking) of low-priority processes.
- **Aging** fixes it: gradually raise the priority of waiting processes.
- SJF is priority scheduling where priority = 1 / (predicted burst).

### Round Robin (RR)

Each process gets a fixed **time quantum (q)**; if not finished, it is preempted and put at the **end** of the ready queue. Designed for time-sharing. **Preemptive.**

| Quantum | Effect |
|---|---|
| Very large | Behaves like FCFS |
| Very small | Many context switches → high overhead |
| Rule of thumb | About 80% of CPU bursts should be shorter than q |

```text
With n processes and quantum q:   no process waits more than (n − 1) × q for its next turn.
CPU efficiency = q / (q + s)       where s = context-switch time
```

### Highest Response Ratio Next (HRRN)

Non-preemptive. Pick the process with the highest `Response Ratio = (Waiting time + Burst time) / Burst time`. Short jobs get a good ratio, but a long job's ratio grows as it waits, so there is **no starvation**.

### Multilevel Queue Scheduling

The ready queue is split into separate queues by process type (system, interactive, batch), each with its own algorithm (for example RR for interactive, FCFS for batch). Queues are scheduled by **fixed priority** (starvation possible) or by **time slices** among queues. A process stays in its queue permanently.

### Multilevel Feedback Queue (MLFQ)

Like multilevel queue, but processes **move between queues** based on behaviour: a process that uses its whole quantum is demoted to a lower-priority queue with a longer quantum; a process that waits too long is promoted (aging). It favours short and I/O-bound jobs without knowing burst lengths in advance. It is defined by the number of queues, each queue's algorithm, and the rules for promotion, demotion and initial placement.

## Worked Example

| Process | Arrival Time | Burst Time |
|---|---|---|
| P1 | 0 | 8 |
| P2 | 1 | 4 |
| P3 | 2 | 9 |
| P4 | 3 | 5 |

**FCFS**

```text
Gantt:  | P1 (0–8) | P2 (8–12) | P3 (12–21) | P4 (21–26) |
CT:  P1=8, P2=12, P3=21, P4=26
TAT: 8, 11, 19, 23   → average 15.25
WT:  0, 7, 10, 18    → average 8.75
```

**SJF (non-preemptive)** — at t = 0 only P1 is present, so it runs to completion; at t = 8 the shortest of P2 (4), P3 (9), P4 (5) is chosen.

```text
Gantt:  | P1 (0–8) | P2 (8–12) | P4 (12–17) | P3 (17–26) |
TAT: P1=8, P2=11, P3=24, P4=14  → average 14.25
WT:  0, 7, 15, 9                → average 7.75
```

**SRTF (preemptive SJF)**

```text
t=1: P2 (4) arrives, P1 has 7 left → preempt P1.   t=3: P4 (5) arrives, P2 has 2 left → P2 continues.
Gantt:  | P1 (0–1) | P2 (1–5) | P4 (5–10) | P1 (10–17) | P3 (17–26) |
CT:  P1=17, P2=5, P3=26, P4=10
TAT: 17, 4, 24, 7   → average 13
WT:  9, 0, 15, 2    → average 6.5
```

**Round Robin, q = 4** (new arrivals join the queue **before** a preempted process)

```text
Queue evolution:  P1 | P2,P3,P4,P1 | ...
Gantt:  | P1 (0–4) | P2 (4–8) | P3 (8–12) | P4 (12–16) | P1 (16–20) | P3 (20–24) | P4 (24–25) | P3 (25–26) |
CT:  P1=20, P2=8, P3=26, P4=25
TAT: 20, 7, 24, 22   → average 18.25
WT:  12, 3, 15, 17   → average 11.75
Response times: P1=0, P2=3, P3=6, P4=9 → average 4.5  (best of all four algorithms)
```

| Algorithm | Average WT | Average TAT |
|---|---|---|
| FCFS | 8.75 | 15.25 |
| SJF (non-preemptive) | 7.75 | 14.25 |
| SRTF | **6.5** | **13** |
| RR (q = 4) | 11.75 | 18.25 |

**Pattern to solve scheduling questions:** (1) draw the Gantt chart carefully, advancing the clock and adding arrivals at each step; (2) compute CT from the chart; (3) get TAT = CT − AT and WT = TAT − BT; (4) remember idle CPU time if no process is ready; (5) for RR, state the tie rule (arrival vs preempted process first).

## Comparison of Algorithms

| Algorithm | Preemptive? | Starvation? | Strength | Weakness |
|---|---|---|---|---|
| FCFS | No | No | Simple | Convoy effect, poor average waiting time |
| SJF | No | Yes | Minimum average waiting time | Needs burst prediction |
| SRTF | Yes | Yes | Minimum average waiting time overall | Overhead, prediction |
| Priority | Either | Yes (fix: aging) | Supports importance | Starvation |
| Round Robin | Yes | No | Fair, good response time | Throughput drops if q is poorly chosen |
| HRRN | No | No | Balances short and long jobs | Needs burst estimate |
| MLFQ | Yes | Possible (fix: aging) | Adapts without burst knowledge | Complex to tune |

## Real-Time Scheduling

| Algorithm | Rule | Schedulability test (n periodic tasks, CPU burst Cᵢ, period Tᵢ) |
|---|---|---|
| **Rate Monotonic (RMS)** | **Static priority**: shorter period = higher priority | Utilization `U = Σ Cᵢ/Tᵢ ≤ n(2^(1/n) − 1)` (sufficient test; bound ≈ 0.693 as n → ∞) |
| **Earliest Deadline First (EDF)** | **Dynamic priority**: nearest deadline runs first | `U ≤ 1` (necessary and sufficient) |

**Example:** tasks (C, T) = (1, 4), (2, 6), (1, 8) give U = 0.25 + 0.333 + 0.125 = 0.708. The RMS bound for n = 3 is 3(2^(1/3) − 1) ≈ 0.78, and 0.708 ≤ 0.78, so the set is schedulable under RMS (and obviously under EDF, since U ≤ 1).

## Multiprocessor Scheduling

| Concept | Meaning |
|---|---|
| Asymmetric vs symmetric (SMP) | One master processor does scheduling vs every processor schedules itself |
| Processor affinity | Keep a process on the same core to reuse its cache (soft = preference, hard = guarantee) |
| Load balancing | Push migration or pull migration moves work from busy to idle cores |
| NUMA awareness | Place processes near the memory they use |

## Scheduling in Real Systems

- **Linux CFS (Completely Fair Scheduler):** each task has a **virtual runtime (`vruntime`)**; the task with the smallest `vruntime` runs next, found in O(log n) from a red-black tree. `nice` values (−20 to +19) change how fast `vruntime` grows (lower nice = more CPU share). Newer kernels use EEVDF with the same goal.
- **Windows:** priority-based preemptive scheduling with 32 priority levels and dynamic boosts for interactive and I/O-completing threads.

---

# Inter-Process Communication (IPC)

## Why IPC?

Processes are isolated, so cooperating processes (information sharing, speed-up, modularity, convenience) need mechanisms to exchange data and synchronize.

## Two Fundamental Models

| Model | How it works | Pros | Cons |
|---|---|---|---|
| **Shared memory** | A region of memory is mapped into the address spaces of several processes; they read and write it directly | Fast (system calls only for setup) | Processes must **synchronize** their access |
| **Message passing** | Processes exchange messages through the kernel with `send(dest, msg)` and `receive(src, msg)` | Easy to use, works across machines, no shared-data conflicts | Slower (system call and copying per message) |

## Message Passing Design Choices

| Issue | Options |
|---|---|
| Naming | **Direct** (name the other process) or **indirect** (via a mailbox/port) |
| Synchronization | **Blocking (synchronous)**: sender waits until received; receiver waits until a message arrives. **Non-blocking (asynchronous)**: calls return immediately |
| Buffering | **Zero capacity** (rendezvous), **bounded capacity** (sender blocks when full), **unbounded capacity** |

## IPC Mechanisms in UNIX/Linux

| Mechanism | Description | Direction / Scope |
|---|---|---|
| **Anonymous pipe** (`pipe()`, `\|` in the shell) | Byte stream in a kernel buffer; read end and write end | Unidirectional, related processes (parent and child) |
| **Named pipe (FIFO)** (`mkfifo`) | Pipe with a name in the file system | Unidirectional, unrelated processes on the same machine |
| **Message queue** (`msgget`, `mq_open`) | Kernel-held queue of discrete, typed messages | Same machine |
| **Shared memory** (`shmget`/`shmat`, `mmap`, `shm_open`) | Shared region; fastest IPC | Same machine; needs synchronization |
| **Semaphore** | Synchronization primitive (not data transfer) | Same machine |
| **Signal** | Small asynchronous notification | Same machine |
| **Socket** | Bidirectional communication endpoint (TCP/UDP, UNIX domain) | Same machine or across a network |
| **Memory-mapped file** (`mmap`) | File contents mapped into memory | Same machine |
| **RPC / gRPC** | Call a procedure that runs in another process or machine | Across a network |

### Pipe Example (parent writes, child reads)

```c
#include <stdio.h>
#include <string.h>
#include <sys/wait.h>
#include <unistd.h>

int main(void) {
    int fd[2];                       // fd[0] = read end, fd[1] = write end
    pipe(fd);
    if (fork() == 0) {               // child: reader
        close(fd[1]);                // close unused write end
        char buf[64];
        ssize_t n = read(fd[0], buf, sizeof buf - 1);
        buf[n] = '\0';
        printf("Child got: %s\n", buf);
        close(fd[0]);
    } else {                         // parent: writer
        close(fd[0]);                // close unused read end
        const char *msg = "hello";
        write(fd[1], msg, strlen(msg));
        close(fd[1]);
        wait(NULL);
    }
    return 0;
}
```

> **Always close the unused ends of a pipe.** A reader only sees end-of-file when *every* write end is closed; a forgotten write end makes the reader hang forever. Writing to a pipe whose read ends are all closed raises `SIGPIPE`.

## Producer–Consumer Problem

The classic cooperation example: a **producer** puts items into a shared **bounded buffer** and a **consumer** takes them out. The producer must wait when the buffer is full and the consumer must wait when it is empty, so synchronization is required (solved with semaphores below).

## Pipes vs Shared Memory vs Sockets

| Criterion | Pipe | Shared memory | Socket |
|---|---|---|---|
| Speed | Medium (kernel copy) | Fastest | Slower (protocol overhead) |
| Synchronization needed from the programmer | No (built in) | **Yes** | No (built in) |
| Across machines | No | No | **Yes** |
| Data model | Byte stream | Raw memory | Stream or datagrams |

---

# Concurrency and Synchronization

## Race Condition

A **race condition** occurs when the result depends on the **order or timing** of concurrent accesses to shared data.

```c
// Shared: int counter = 0;   Two threads each run counter++ 1,000,000 times
// counter++ is NOT atomic. It compiles to three steps:
//     LOAD  R, counter
//     ADD   R, 1
//     STORE counter, R
//
// Thread A: LOAD (0)                      → R_A = 0
// Thread B:       LOAD (0), ADD, STORE    → counter = 1
// Thread A:                  ADD, STORE   → counter = 1   (one increment lost)
```

The final value can be anywhere between 1,000,000 and 2,000,000.

## The Critical Section Problem

A **critical section** is a code segment that accesses shared data. The general structure of a process is:

```text
do {
    entry section        // request permission
        critical section
    exit section         // release
        remainder section
} while (true);
```

A correct solution must satisfy **all three** requirements:

| Requirement | Meaning |
|---|---|
| **Mutual exclusion** | At most one process is in its critical section at a time |
| **Progress** | If no process is in its critical section, the choice of who enters next must be made only by processes wanting to enter, and cannot be postponed indefinitely |
| **Bounded waiting** | After a process requests entry, there is a limit on how many times others may enter before it does (no starvation) |

(No assumptions may be made about relative speeds or number of CPUs.)

## Software Solutions

### Strict Alternation (fails)

```c
// Shared: int turn = 0;
// Process i:
while (turn != i) ;           // busy wait
/* critical section */
turn = 1 - i;
```

Guarantees mutual exclusion, but **violates progress**: if process 0 is slow in its remainder section, process 1 cannot enter even though the critical section is free.

### Peterson's Solution (two processes, correct)

```c
// Shared: bool flag[2] = {false, false};  int turn;
// Process i (j = 1 - i):
flag[i] = true;               // I want to enter
turn = j;                     // but let the other go first if it also wants to
while (flag[j] && turn == j) ; // wait while the other wants in and it is its turn
/* critical section */
flag[i] = false;              // exit section
```

It satisfies **mutual exclusion, progress and bounded waiting**. It uses **busy waiting**, works for exactly two processes, and on modern CPUs needs **memory barriers** because the hardware may reorder memory operations.

## Hardware Support

| Mechanism | Idea |
|---|---|
| Disable interrupts | Works on a single CPU only (no preemption inside the critical section); unsafe for user programs and not usable on multiprocessors |
| **Test-and-Set (TAS)** | Atomically returns the old value of a lock variable and sets it to true |
| **Compare-and-Swap (CAS)** | Atomically sets a variable to a new value only if it currently equals an expected value |
| Atomic instructions / fetch-and-add | Atomic read-modify-write operations; basis of locks and lock-free structures |

```c
// Spinlock with Test-and-Set (atomic hardware instruction)
bool lock = false;                     // shared

void acquire(void) { while (test_and_set(&lock)) ; }   // spin until it returns false
void release(void) { lock = false; }
```

A **spinlock** busy waits and wastes CPU time, so it is appropriate only for **very short** critical sections on multiprocessors (no context switch cost). Basic TAS locks give mutual exclusion and progress, but **not bounded waiting**.

## Semaphores

A **semaphore** is an integer variable accessed only through two **atomic** operations:

```text
wait(S)   (P, down):    S = S − 1;  if S < 0, block the caller in S's queue
signal(S) (V, up):      S = S + 1;  if S ≤ 0, wake one blocked process
```

A semaphore with a waiting queue (no busy waiting) is implemented like this:

```text
wait(S):    S.value--;  if (S.value < 0) { add process to S.list; block(); }
signal(S):  S.value++;  if (S.value <= 0) { remove a process P from S.list; wakeup(P); }
```

| Type | Initial value | Use |
|---|---|---|
| **Binary semaphore** (mutex-like) | 0 or 1 | Mutual exclusion |
| **Counting semaphore** | n ≥ 0 | Control access to *n* identical resources; signalling between processes |

**Interpreting the value:** if S ≥ 0, it is the number of *additional* processes that can pass `wait()` without blocking; if S < 0, |S| is the number of **blocked** processes.

```text
Counting semaphore starts at n.   After k wait() calls and v signal() calls (no earlier waiters):
    value = n − k + v
    blocked processes = max(0, k − v − n)
Example: S = 3, then 5 waits and 1 signal  → value = 3 − 5 + 1 = −1 → 1 process is blocked.
```

### Mutual Exclusion and Ordering with Semaphores

```c
semaphore mutex = 1;
wait(mutex);   /* critical section */   signal(mutex);

// Force "A runs before B":   semaphore s = 0;
// Process 1:  A();  signal(s);
// Process 2:  wait(s);  B();
```

**Common mistakes:** swapping `wait` and `signal` (mutual exclusion lost); omitting a `signal` (deadlock); doing `wait(mutex)` twice (self-deadlock).

> A **mutex** has an *owner* (only the locker may unlock it) and is for mutual exclusion; a **semaphore** has no owner and can be used for signalling between different processes.

## Classic Synchronization Problems

### 1. Bounded Buffer (Producer–Consumer)

```c
semaphore mutex = 1;      // protects the buffer
semaphore empty = N;      // number of empty slots
semaphore full  = 0;      // number of filled slots

// Producer                       // Consumer
do {                              do {
    item = produce();                 wait(full);       // wait for an item
    wait(empty);   // free slot       wait(mutex);
    wait(mutex);                      item = remove();
    insert(item);                     signal(mutex);
    signal(mutex);                    signal(empty);    // one more free slot
    signal(full);  // one more item    consume(item);
} while (1);                      } while (1);
```

> Reversing the order `wait(mutex)` before `wait(empty)` in the producer can **deadlock**: a producer holding the mutex on a full buffer waits forever for a slot that only a consumer (blocked on the mutex) can free.

### 2. Readers–Writers (readers' preference)

```c
semaphore rw_mutex = 1;    // exclusive access for writers (and the first/last reader)
semaphore mutex = 1;       // protects read_count
int read_count = 0;

// Writer                         // Reader
wait(rw_mutex);                   wait(mutex);
   write();                       read_count++;
signal(rw_mutex);                 if (read_count == 1) wait(rw_mutex);   // first reader locks out writers
                                  signal(mutex);
                                     read();
                                  wait(mutex);
                                  read_count--;
                                  if (read_count == 0) signal(rw_mutex); // last reader lets writers in
                                  signal(mutex);
```

Many readers may read together; a writer needs exclusive access. This version can **starve writers**; a writers'-preference version can starve readers.

### 3. Dining Philosophers

Five philosophers sit around a table with five chopsticks (one between each pair of neighbours). To eat, a philosopher needs both adjacent chopsticks.

```c
semaphore chopstick[5] = {1,1,1,1,1};
// Philosopher i:   wait(chopstick[i]);  wait(chopstick[(i+1)%5]);  eat();  signal(...);  signal(...);
// If all five pick up their left chopstick at once → DEADLOCK.
```

| Fix | Idea (breaks which deadlock condition) |
|---|---|
| Allow at most **4** philosophers to sit | Circular wait cannot form |
| **Asymmetric**: odd philosophers pick left first, even pick right first | Circular wait cannot form |
| **Resource ordering**: always pick the lower-numbered chopstick first | Circular wait cannot form |
| Pick both chopsticks **only if both are free** (inside a critical section) | Hold-and-wait removed |
| Monitor with a `HUNGRY/EATING/THINKING` state per philosopher | Safe and deadlock-free (starvation still possible without extra care) |

### 4. Sleeping Barber, Cigarette Smokers

Other well-known problems: a barber with limited waiting-room chairs (semaphores for customers, barber and chairs), and smokers needing three ingredients supplied by an agent.

## Monitors and Condition Variables

A **monitor** is a high-level construct: a module whose **shared data is accessible only through its procedures**, and **only one process can be active inside the monitor at a time** (mutual exclusion is automatic). **Condition variables** (`wait`, `signal`/`notify`) let a process wait inside the monitor until a condition holds.

```c
// POSIX mutex + condition variable: wait until "ready" becomes true
pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t  c = PTHREAD_COND_INITIALIZER;
int ready = 0;

// Waiting thread
pthread_mutex_lock(&m);
while (!ready)                       // ALWAYS a while loop (spurious wakeups, other threads may change the state)
    pthread_cond_wait(&c, &m);       // atomically releases m and sleeps; re-locks m on wakeup
pthread_mutex_unlock(&m);

// Signalling thread
pthread_mutex_lock(&m);  ready = 1;  pthread_cond_signal(&c);  pthread_mutex_unlock(&m);
```

| Semaphore `signal` | Condition variable `signal` |
|---|---|
| Value remembered (increments the count) | **Lost** if nobody is waiting |

## Priority Inversion

A **high-priority** task H waits for a lock held by a **low-priority** task L; meanwhile a **medium-priority** task M preempts L, so H is effectively blocked by M.

```text
L holds lock → H needs lock, blocks → M becomes ready and preempts L → L cannot release → H starves
```

**Solutions:** **priority inheritance** (L temporarily runs at H's priority while holding the lock), or the **priority ceiling protocol**. This bug famously affected the Mars Pathfinder lander in 1997.

## Other Synchronization Concepts

| Concept | Description |
|---|---|
| **Barrier** | All threads wait at a point until every thread has arrived |
| **Read–write lock** | Many readers or one writer |
| **Lock-free / wait-free structures** | Built on CAS; no thread holds a lock, so a stalled thread cannot block others |
| **Memory model / barriers** | Compilers and CPUs reorder instructions; atomics with proper memory ordering or locks are required for correctness |
| **Livelock** | Processes keep changing state in response to each other but make no progress (like two people repeatedly stepping aside in a corridor) |
| **Starvation** | A process waits indefinitely even though the system as a whole makes progress |
| **False sharing** | Different variables on the same cache line, written by different cores, cause the line to bounce between caches and slow the program |

## Spinlock vs Mutex vs Semaphore

| Aspect | Spinlock | Mutex | Semaphore |
|---|---|---|---|
| Waiting | Busy waits | Sleeps (blocks) | Sleeps (blocks) |
| Ownership | Yes | **Yes** | No |
| Best for | Very short critical sections, multiprocessors | General mutual exclusion | Counting resources, signalling |
| Overhead | No context switch, burns CPU | Context switch if contended | Context switch if contended |

---

# Deadlock

## What is a Deadlock?

A set of processes is **deadlocked** when every process in the set is waiting for a resource held by another process in the same set, so none can ever proceed.

```text
P1 holds R1, requests R2          P1 ──waits for──► R2 (held by P2)
P2 holds R2, requests R1          P2 ──waits for──► R1 (held by P1)
```

A process uses a resource in three steps: **request → use → release**. Resources may be physical (printers, disks) or logical (locks, semaphores, database rows), and may have a single instance or several identical instances.

## Four Necessary Conditions (Coffman Conditions)

Deadlock can occur **only if all four hold simultaneously**:

| Condition | Meaning |
|---|---|
| **Mutual exclusion** | At least one resource is non-sharable (held by one process at a time) |
| **Hold and wait** | A process holds at least one resource while waiting for others |
| **No preemption** | A resource can only be released voluntarily by its holder |
| **Circular wait** | A cycle P0 → P1 → ... → Pn → P0 exists, each waiting for a resource held by the next |

## Resource Allocation Graph (RAG)

- Vertices: processes (circles) and resource types (rectangles with a dot per instance).
- **Request edge** P → R: P is waiting for R. **Assignment edge** R → P: an instance of R is allocated to P.

| Graph situation | Conclusion |
|---|---|
| No cycle | **No deadlock** |
| Cycle, every resource has a **single instance** | **Deadlock** |
| Cycle, resources have **multiple instances** | **Possible** deadlock (the cycle is necessary but not sufficient) |

## Methods for Handling Deadlocks

| Approach | Idea |
|---|---|
| **Prevention** | Design the system so at least one Coffman condition can never hold |
| **Avoidance** | Use advance information; grant a request only if the system stays in a **safe state** |
| **Detection and recovery** | Allow deadlock, detect it, then recover |
| **Ignore (ostrich algorithm)** | Pretend it never happens; used by most general-purpose OSs (Linux, Windows) because deadlocks are rare and prevention is costly |

## Deadlock Prevention

| Condition to break | Technique | Drawback |
|---|---|---|
| Mutual exclusion | Make resources sharable (read-only files), spool printers | Not possible for many resources |
| Hold and wait | Request **all** resources at the start, or release everything before requesting more | Low utilization, starvation |
| No preemption | If a request cannot be granted, the process **releases what it holds** (or the OS preempts) | Works only for resources whose state can be saved (CPU, memory) |
| **Circular wait** | Number all resources and require requests in **increasing order** | Practical and widely used (e.g., lock ordering in code) |

## Deadlock Avoidance

### Safe and Unsafe States

A state is **safe** if there exists a **safe sequence** ⟨P₁, P₂, ..., Pₙ⟩ in which each process can obtain its remaining maximum need from the currently free resources plus the resources released by earlier processes in the sequence. An **unsafe** state *may* lead to deadlock (not necessarily). Deadlock states are a subset of unsafe states.

```text
Safe state  →  no deadlock.      Unsafe state  →  deadlock possible.      Avoidance keeps the system in safe states.
```

### Banker's Algorithm (multiple instances)

Data structures for *n* processes and *m* resource types:

| Structure | Meaning |
|---|---|
| `Available[m]` | Free instances of each resource |
| `Max[n][m]` | Maximum demand of each process |
| `Allocation[n][m]` | Currently allocated |
| `Need[n][m]` | `Max − Allocation` |

**Safety algorithm:**

```text
1. Work = Available;  Finish[i] = false for all i.
2. Find i with Finish[i] == false and Need[i] ≤ Work.   If none, go to 4.
3. Work = Work + Allocation[i];  Finish[i] = true;  go to 2.
4. If Finish[i] is true for all i, the state is SAFE; otherwise UNSAFE.
```

**Resource-request algorithm** (process Pᵢ requests `Request`): (1) if `Request > Need[i]`, error; (2) if `Request > Available`, Pᵢ must wait; (3) **pretend** to allocate (`Available −= Request`, `Allocation[i] += Request`, `Need[i] −= Request`) and run the safety algorithm; if safe, grant it, otherwise roll back and make Pᵢ wait. Complexity: O(m × n²).

### Worked Example

Total resources: A = 10, B = 5, C = 7.

| Process | Allocation (A B C) | Max (A B C) | Need = Max − Allocation |
|---|---|---|---|
| P0 | 0 1 0 | 7 5 3 | 7 4 3 |
| P1 | 2 0 0 | 3 2 2 | 1 2 2 |
| P2 | 3 0 2 | 9 0 2 | 6 0 0 |
| P3 | 2 1 1 | 2 2 2 | 0 1 1 |
| P4 | 0 0 2 | 4 3 3 | 4 3 1 |

Available = Total − ΣAllocation = (10,5,7) − (7,2,5) = **(3, 3, 2)**.

```text
Work = (3,3,2)
P1: Need (1,2,2) ≤ Work → run, Work = (3,3,2) + (2,0,0) = (5,3,2)
P3: Need (0,1,1) ≤ Work → run, Work = (5,3,2) + (2,1,1) = (7,4,3)
P4: Need (4,3,1) ≤ Work → run, Work = (7,4,3) + (0,0,2) = (7,4,5)
P0: Need (7,4,3) ≤ Work → run, Work = (7,4,5) + (0,1,0) = (7,5,5)
P2: Need (6,0,0) ≤ Work → run, Work = (7,5,5) + (3,0,2) = (10,5,7)
Safe sequence: ⟨P1, P3, P4, P0, P2⟩  → the state is SAFE.
```

**Request:** P1 asks for (1, 0, 2). It is ≤ Need (1,2,2) and ≤ Available (3,3,2). Pretend: Available = (2,3,0), P1 Allocation = (3,0,2), P1 Need = (0,2,0). The safety algorithm again finds a safe sequence (for example ⟨P1, P3, P4, P0, P2⟩), so the request is **granted**.

## Deadlock Detection

**Single instance per resource:** maintain a **wait-for graph** (remove the resource nodes from the RAG; edge Pᵢ → Pⱼ if Pᵢ waits for a resource held by Pⱼ). **A cycle means deadlock.** Searching for a cycle costs O(n²).

**Multiple instances:** use an algorithm like the Banker's safety algorithm, with the current *Request* matrix instead of *Need*. Processes whose `Finish` stays false at the end are deadlocked. Run the detector periodically or when CPU utilization drops.

## Recovery from Deadlock

| Method | Details |
|---|---|
| **Process termination** | Abort **all** deadlocked processes (certain but expensive), or abort **one at a time** until the cycle disappears. Choose by priority, work done, resources held, resources still needed. |
| **Resource preemption** | Take a resource from a process and give it to another: choose a **victim**, **roll back** the victim to a safe state, and avoid starving the same victim repeatedly (include the rollback count in the cost). |

## Useful Formulas

If *n* processes each need a **maximum of k** instances of the **same** resource type, and the system has **R** instances:

```text
Deadlock is impossible if     R ≥ n × (k − 1) + 1
Maximum n that is safe:       n ≤ (R − 1) / (k − 1)
```

**Reasoning:** the worst case is every process holding k − 1 instances and waiting for one more; if at least one extra instance exists, some process can finish and release its resources.

**Example:** 3 processes, each needing at most 2 instances → minimum R = 3 × (2 − 1) + 1 = **4** instances prevent deadlock. With R = 3 deadlock is possible (each holds 1 and waits for 1).

For different maximum needs mᵢ: deadlock-free if `R ≥ Σ(mᵢ − 1) + 1`.

## Deadlock in Everyday Code

```c
// Thread 1: lock(A); lock(B); ...        Thread 2: lock(B); lock(A); ...    → possible deadlock
// Fix: every thread acquires locks in the same global order (A then B).
```

Other fixes: `trylock` with back-off and release, timeouts, and holding as few locks as possible for as short a time as possible.

## Deadlock, Livelock and Starvation

| Problem | Processes... | Cause |
|---|---|---|
| Deadlock | Blocked forever, waiting for each other | Circular wait |
| Livelock | Running but making no progress | Both keep retrying/yielding in sync |
| Starvation | One process is repeatedly passed over | Unfair scheduling or lock policy |

---

# Memory Management

## Basics

Programs must be in main memory to execute. The OS must give each process its own **protected** address space, allow memory to be shared when needed, and use RAM efficiently.

### Logical vs Physical Addresses

| Address | Generated by | Seen by |
|---|---|---|
| **Logical (virtual) address** | The CPU while running a program | The program |
| **Physical address** | After translation by the **Memory Management Unit (MMU)** | The memory hardware |

```text
CPU ──logical address──► MMU (relocation/page table) ──physical address──► Memory
```

**Address binding** (when logical addresses become physical): at **compile time** (absolute code), at **load time** (relocatable code), or at **execution time** (needs an MMU; required for swapping and virtual memory).

| Technique | Idea |
|---|---|
| Dynamic loading | A routine is loaded only when first called |
| Static vs dynamic linking | Libraries combined at build time vs linked at run time (shared libraries, `.so`/`.dll`) |
| **Swapping** | A process can be temporarily moved from RAM to a backing store (swap space) and back |

## Contiguous Memory Allocation

Each process occupies one contiguous block. A **base (relocation) register** and a **limit register** protect it: a logical address `a` is valid if `a < limit`, and the physical address is `base + a`.

### Partitioning

| Scheme | Description | Fragmentation |
|---|---|---|
| **Fixed (static) partitions** | Memory pre-divided into fixed-size partitions | **Internal** (unused space inside a partition) |
| **Variable (dynamic) partitions** | A partition of exactly the needed size is created for each process | **External** (free space split into many small holes) |

| Fragmentation | Meaning | Remedy |
|---|---|---|
| Internal | Allocated block larger than needed; unused space is inside the block | Smaller allocation units |
| External | Enough total free memory but not contiguous | **Compaction** (move processes together; needs execution-time binding) or **paging/segmentation** (non-contiguous allocation) |

### Placement Strategies

| Strategy | Choice | Notes |
|---|---|---|
| **First fit** | First hole big enough | Fast, good in practice |
| **Next fit** | First fit starting from the last allocation | Similar to first fit |
| **Best fit** | **Smallest** hole big enough | Slow (search all), leaves tiny useless holes |
| **Worst fit** | **Largest** hole | Leaves big remainders, usually performs worst |

**Example:** free holes in order: 100K, 500K, 200K, 300K, 600K. Processes arrive: 212K, 417K, 112K, 426K.

```text
First fit: 212→500 (288 left), 417→600 (183 left), 112→288 (176 left), 426 → no hole large enough (waits)
Best fit : 212→300 (88 left), 417→500 (83 left), 112→200 (88 left), 426→600 (174 left)  → all placed
Worst fit: 212→600 (388 left), 417→500 (83 left), 112→388 (276 left), 426 → no hole large enough (waits)
```

### Buddy System

Memory is managed in blocks whose sizes are **powers of two**. A request is rounded up to the next power of two; larger blocks are split in halves ("buddies") until the size fits. When a block is freed and its buddy is also free, they **merge**. Fast and easy coalescing, but causes **internal fragmentation** (for instance, a 100 KB request gets a 128 KB block, wasting 28 KB).

## Paging

Paging divides the logical address space into fixed-size **pages** and physical memory into **frames** of the same size. Pages can be placed in any free frames, so there is **no external fragmentation** (only a little internal fragmentation in the last page).

```text
Logical address  = | page number (p) | page offset (d) |
Physical address = | frame number (f) | page offset (d) |      (the offset is unchanged)
```

```text
CPU: (p, d) → page table[p] gives frame f → physical address = f × page_size + d
```

### Address Calculations

| Given | Formula |
|---|---|
| Logical address space = 2ᵐ bytes, page size = 2ⁿ bytes | Page number has **m − n** bits; offset has **n** bits |
| Number of pages | 2^(m − n) |
| Physical memory = 2ᵖ bytes | Frame number has **p − n** bits; number of frames = 2^(p − n) |
| Page-table entries per process | Number of pages |
| Page table size | Entries × entry size |

**Example:** 32-bit logical addresses, 4 KB pages (n = 12), 4-byte page-table entries.

```text
Page number = 32 − 12 = 20 bits → 2²⁰ = 1,048,576 pages → page table = 2²⁰ × 4 B = 4 MB per process
```

**Optimal page size** (minimizing page-table overhead plus internal fragmentation): for average process size *s* and page-table entry size *e*, `page size ≈ √(2·s·e)`. For s = 1 MB and e = 8 bytes: √(2 × 2²⁰ × 8) = √(2²⁴) = **4 KB**.

### Page Table Entry (PTE) Contents

| Field | Purpose |
|---|---|
| Frame number | Where the page is in RAM |
| **Valid/present bit** | Page is in memory (0 → page fault or invalid) |
| **Protection bits** | Read / write / execute permissions |
| **Dirty (modified) bit** | Page was written; must be saved before eviction |
| **Reference (accessed) bit** | Page was used recently (used by replacement) |
| Caching/user-supervisor bits | Cache policy; kernel-only pages |

### Translation Lookaside Buffer (TLB)

Without help, every memory reference needs **two** memory accesses (page table, then data). The **TLB** is a small, fast, associative cache of recent page-table entries.

```text
Effective Access Time (EAT), single-level page table:
    EAT = α × (t + m)  +  (1 − α) × (t + 2m)
    α = TLB hit ratio, t = TLB lookup time, m = memory access time

For an L-level page table, a TLB miss costs t + (L + 1) × m.
```

**Example:** α = 0.90, t = 20 ns, m = 100 ns → EAT = 0.9 × 120 + 0.1 × 220 = 108 + 22 = **130 ns**.

- On a **context switch** the TLB must be flushed, unless entries carry an **address-space identifier (ASID)**.
- **TLB reach** = number of entries × page size.

### Multilevel (Hierarchical) Page Tables

A single 4 MB page table per process is wasteful, so the page table itself is paged. The **outer page table** points to inner page-table pages, and unused regions need no inner tables.

```text
32-bit address, 4 KB pages, 4-byte entries:  1 page holds 4096 / 4 = 1024 entries (10 bits)
Address split:  | 10 bits: outer index | 10 bits: inner index | 12 bits: offset |
64-bit addresses need 3–5 levels (x86-64 uses 4 or 5 levels with 48-bit/57-bit virtual addresses).
```

**Design rule:** each page-table level should fit exactly into one page. Number of levels = ⌈(page-number bits) / (bits indexable in one page-table page)⌉.

### Other Page-Table Structures

| Structure | Idea | Use |
|---|---|---|
| **Hashed page table** | Hash of the virtual page number; chain of entries | Large sparse 64-bit spaces |
| **Inverted page table** | **One entry per physical frame** (holds process id + page number); search by hash | Size depends on RAM, not on virtual space; slower lookup, harder sharing |

**Example (inverted):** 1 GB RAM with 4 KB frames → 2³⁰ / 2¹² = 2¹⁸ = 262,144 entries, regardless of the number of processes.

### Shared Pages and Copy-on-Write

Read-only code (and shared libraries) can be mapped by many processes to the **same frames**. **Copy-on-write:** after `fork()`, parent and child share pages marked read-only; the first write triggers a fault and the kernel copies just that page. This makes `fork()` fast.

## Segmentation

Segmentation divides a program into **logical units** of varying size (main code, functions, stack, heap, arrays). A logical address is a pair **(segment number s, offset d)**.

```text
Segment table entry:  base, limit
Translation:  if d ≥ limit[s] → trap (addressing error)    else  physical = base[s] + d
```

| Aspect | Paging | Segmentation |
|---|---|---|
| Unit | Fixed-size page | Variable-size logical segment |
| Visible to programmer? | No | **Yes** |
| Fragmentation | Internal | **External** |
| Sharing and protection | Per page | Natural per segment (code, data) |
| Address | (page, offset) computed by hardware from a flat address | (segment, offset) |

**Segmentation with paging** (Intel x86 in 32-bit mode, MULTICS) pages each segment, combining logical protection with no external fragmentation.

## Comparison of Memory Allocation Schemes

| Scheme | External fragmentation | Internal fragmentation | Notes |
|---|---|---|---|
| Fixed partitions | No | **Yes** | Limits degree of multiprogramming |
| Variable partitions | **Yes** | No | Needs compaction |
| Paging | No | Yes (last page only) | Page tables, TLB |
| Segmentation | **Yes** | No | Matches program structure |

---

# Virtual Memory

## Concept

**Virtual memory** lets a process run even if only **part** of it is in physical memory, so the logical address space can be much larger than RAM. It relies on **locality of reference**: programs use a small part of their address space at any time. Benefits: programs larger than RAM, more programs in memory (higher multiprogramming), less I/O to load programs, easy sharing and protection.

## Demand Paging

Pages are brought into memory **only when they are referenced** (lazy loading). The **valid bit** in the page table tells whether the page is in memory.

### Page Fault Handling

```text
1. The CPU references a page whose valid bit is 0 → trap to the OS (page fault).
2. The OS checks (using the PCB) whether the reference is legal; if illegal, terminate the process.
3. The OS finds a free frame (or runs page replacement to free one).
4. It schedules a disk read of the page into that frame (the process blocks; the CPU runs other work).
5. When the I/O completes, the OS updates the page table (frame number, valid = 1).
6. The instruction that faulted is restarted.
```

| Fault type | Meaning | Cost |
|---|---|---|
| **Minor (soft)** | Page already in memory (e.g., shared or in the page cache) but not mapped, or a zero page needs creating | Microseconds |
| **Major (hard)** | Page must be read from disk | Milliseconds |

### Effective Access Time with Page Faults

```text
EAT = (1 − p) × m  +  p × (page fault service time)           p = page fault rate
```

**Example:** m = 200 ns, page fault service time = 8 ms = 8,000,000 ns.
EAT = (1 − p) × 200 + p × 8,000,000 ≈ 200 + 7,999,800 × p. To limit the slowdown to **10%** (EAT ≤ 220 ns): p ≤ 20 / 7,999,800 ≈ **2.5 × 10⁻⁶**, about one fault in 400,000 accesses. Page faults must be extremely rare.

If a page being evicted is **dirty**, it must be written back first, which can double the service time.

## Page Replacement Algorithms

When no frame is free, the OS selects a **victim page**. The goal is the **lowest page-fault rate**. Evaluated on a **reference string** (sequence of page numbers).

| Algorithm | Victim | Notes |
|---|---|---|
| **FIFO** | The **oldest** page in memory | Simple; **Belady's anomaly** possible |
| **Optimal (OPT / MIN)** | The page that will **not be used for the longest time** in the future | Lowest possible faults; **not implementable** (needs the future); used as a benchmark |
| **LRU (Least Recently Used)** | The page **not used for the longest time in the past** | Good approximation of OPT; needs hardware support (counters or stack) |
| **LFU / MFU** | Least / most frequently used | Counting-based; rarely used |
| **Second chance (clock)** | FIFO, but a page with reference bit 1 gets its bit cleared and is skipped once | Cheap LRU approximation |
| **Enhanced second chance** | Uses (reference, dirty) bits; prefers pages that are unused **and** clean | Used in some UNIX systems |

### Worked Example (3 frames)

Reference string: `7 0 1 2 0 3 0 4 2 3 0 3 2 1 2 0 1 7 0 1` (20 references)

```text
FIFO trace (frames after each fault):
7:[7]  0:[7,0]  1:[7,0,1]  2:[2,0,1]  0:hit  3:[2,3,1]  0:[2,3,0]  4:[4,3,0]  2:[4,2,0]  3:[4,2,3]
0:[0,2,3]  3:hit  2:hit  1:[0,1,3]  2:[0,1,2]  0:hit  1:hit  7:[7,1,2]  0:[7,0,2]  1:[7,0,1]
FIFO: 15 page faults
```

| Algorithm | Page faults | Page fault rate |
|---|---|---|
| FIFO | 15 | 75% |
| LRU | 12 | 60% |
| **OPT** | **9** | 45% |

Always: OPT ≤ LRU, and typically LRU ≤ FIFO (but FIFO is not guaranteed to be worse).

### Belady's Anomaly

For FIFO, **adding more frames can increase the number of page faults**. Reference string `1 2 3 4 1 2 5 1 2 3 4 5`: FIFO has **9 faults with 3 frames** but **10 faults with 4 frames**. **Stack algorithms** such as **LRU and OPT** never suffer from it (the set of pages in memory with *n* frames is always a subset of the set with *n + 1* frames).

### LRU Implementations

| Method | Idea |
|---|---|
| Counter | Each page-table entry stores the time of last use; evict the smallest |
| Stack | Doubly-linked stack of page numbers; the referenced page moves to the top; evict from the bottom |
| Approximations | Reference bit, additional-reference-bits (shift register), second chance/clock |

**Pattern to solve page-replacement questions:** write the frames after every reference, mark hits and faults, and count faults. For OPT look **forward** in the string; for LRU look **backward**; for FIFO use arrival order only (a hit does **not** change the order).

## Allocation of Frames

| Scheme | Rule |
|---|---|
| Equal allocation | m frames / n processes each |
| **Proportional allocation** | `aᵢ = (sᵢ / S) × m` (sᵢ = size of process i, S = total size) |
| Priority allocation | More frames for high-priority processes |

| Replacement scope | Description |
|---|---|
| **Global** | A process may evict a page of **any** process (better throughput; a process's fault rate depends on others) |
| **Local** | A process evicts only from its own frames (predictable; may underuse memory) |

Every process needs a **minimum** number of frames, set by the instruction set (the most pages one instruction can reference).

## Thrashing

A process is **thrashing** when it spends more time **paging than executing**. The cycle: too many processes → each gets too few frames → high page-fault rate → the CPU is idle waiting for the disk → the OS sees low CPU utilization and **admits more processes** → even worse.

```text
CPU utilization rises with the degree of multiprogramming, then COLLAPSES once thrashing begins.
```

**Remedies:**

| Technique | Idea |
|---|---|
| **Working-set model** | Working set WS(t, Δ) = pages referenced in the last Δ references. If Σ|WSᵢ| > total frames, suspend a process; if there is spare memory, admit one |
| **Page-fault frequency (PFF)** | Keep each process's fault rate between an upper and a lower bound by giving or taking frames |
| Local replacement | Thrashing of one process does not steal frames from others |
| Reduce multiprogramming | Swap out processes |
| Add RAM | Fix the root cause |

## Other Virtual Memory Topics

| Topic | Description |
|---|---|
| **Memory-mapped files** | A file is mapped into the address space (`mmap`); page faults load it, and writes go back to the file |
| **Prepaging** | Load pages the process will probably need, to avoid initial faults |
| **Page size trade-off** | Small pages: less internal fragmentation, larger page tables, more faults and I/O operations. Large pages: the reverse; better TLB reach |
| **Huge pages** | 2 MB or 1 GB pages to extend the TLB reach |
| **Kernel memory allocation** | Slab allocator: caches of pre-built objects of common kernel types; avoids fragmentation and allocation cost |
| **Swap space** | Disk area (partition or file) for evicted anonymous pages |
| **OOM killer (Linux)** | When memory is exhausted and nothing can be reclaimed, the kernel kills a process with a high "badness" score |
| **Demand-zero pages** | Pages of the heap and stack start as zero-filled frames allocated on first touch |
| **ASLR** | Randomizes the layout of the address space to make exploits harder |

---

# File Systems

## The File Concept

A **file** is a named collection of related information stored on secondary storage; the OS presents it as a logical storage unit independent of the physical device.

| Attribute | Meaning |
|---|---|
| Name | Human-readable identifier |
| Identifier | Unique number inside the file system (inode number) |
| Type | Regular file, directory, symbolic link, device file, socket, pipe |
| Location, size | Where it is on the device and how big it is |
| Protection | Who may read, write, execute |
| Timestamps | Creation, last modification, last access |

**Basic operations:** create, write, read, reposition (`seek`), delete, truncate, open, close. `open()` loads the file's metadata into memory once so later reads and writes do not have to search the directory.

### Open-File Tables (UNIX)

```text
Per-process file descriptor table ──► System-wide open-file table (offset, mode, reference count) ──► In-memory inode table
```

- After `fork()`, parent and child **share the same open-file table entry**, hence the same file offset.
- Two independent `open()` calls of the same file create **two entries** with independent offsets.
- `dup2(old, new)` makes two descriptors point to the **same** entry (this is how shell redirection `> file` works).

## File Access Methods

| Method | Description | Example |
|---|---|---|
| **Sequential** | Records are read in order; a pointer advances automatically | Tape, log files |
| **Direct (random)** | Jump to any block number directly | Databases |
| **Indexed** | An index maps keys to block positions | Database files |

## Directory Structure

| Structure | Description | Problem |
|---|---|---|
| Single-level | All files in one directory | Name clashes, no grouping |
| Two-level | One directory per user | No sharing between users |
| **Tree-structured** | Hierarchy; each file has a unique path | Most common |
| **Acyclic-graph** | Tree plus shared subdirectories/files via **links** | Deletion needs reference counts; avoid cycles |
| General graph | Cycles allowed | Needs garbage collection or cycle checks |

### Hard Links vs Soft (Symbolic) Links

| Aspect | Hard link | Soft (symbolic) link |
|---|---|---|
| What it is | Another **directory entry** pointing to the **same inode** | A special file containing a **path** |
| Across file systems | **No** | Yes |
| Directories | Not allowed (normally) | Allowed |
| If the original name is deleted | Data survives until the **link count** reaches 0 | Link **dangles** (broken) |
| Inode | Same inode number | Own inode |

### Path Names and Mounting

An **absolute path** starts at the root (`/home/user/a.txt`); a **relative path** starts at the current directory. **Mounting** attaches a file system to a directory (the mount point) of the existing tree (`mount /dev/sdb1 /mnt/data`).

## File System Structure

```text
Application programs
   ↓
Logical file system        (directories, file control blocks/inodes, protection)
   ↓
File-organization module   (file blocks → logical blocks; free-space management)
   ↓
Basic file system          (issues commands to the device driver; buffer/cache)
   ↓
I/O control (device drivers, interrupt handlers)
   ↓
Devices
```

A **Virtual File System (VFS)** layer gives a uniform interface (open, read, write) over different file systems (ext4, NTFS, FAT32, NFS) at the same time.

### On-Disk Layout (typical UNIX-style file system)

| Region | Contents |
|---|---|
| Boot block | Code to boot an OS |
| **Superblock** | Size of the file system, block size, number of free blocks and inodes, pointers to free lists |
| Inode table / bitmaps | One inode per file; bitmaps showing free inodes and blocks |
| Data blocks | File contents and directory contents |

An **inode** stores the file's metadata (owner, permissions, size, timestamps, link count) and the block pointers. **It does not store the file name**; names live in directory entries that map name → inode number.

## Allocation Methods

How disk blocks are assigned to files.

### 1. Contiguous Allocation

Each file occupies consecutive blocks. The directory stores the **start block and length**.

| Pros | Cons |
|---|---|
| Fast sequential and **direct** access (`block = start + i`), few seeks | **External fragmentation**; file size must be known or hard to grow; compaction needed |

### 2. Linked Allocation

Each block contains a **pointer to the next block**; the directory stores the first (and last) block.

| Pros | Cons |
|---|---|
| No external fragmentation; files can grow | **No efficient direct access** (reading block *i* needs *i* disk reads); pointer space wasted in each block; one damaged pointer loses the rest |

**File Allocation Table (FAT):** the pointers are collected in one table at the start of the volume (entry *i* holds the next block after block *i*). The table can be cached in memory, so direct access improves. FAT size = number of blocks × entry size. **Example:** 1 GB disk, 4 KB blocks → 2¹⁸ blocks × 4 B = **1 MB** table.

### 3. Indexed Allocation

Each file has an **index block** containing the addresses of all its data blocks.

| Pros | Cons |
|---|---|
| Direct access, no external fragmentation | Index block overhead (wasteful for tiny files); a large file may need more than one index block |

Schemes for large files: **linked** index blocks, **multilevel** index, or the **combined scheme** used by UNIX inodes.

### Combined Scheme: the UNIX inode

```text
inode:  12 direct pointers  ──► 12 data blocks
        1 single indirect   ──► block of pointers ──► data blocks
        1 double indirect   ──► block of pointers ──► blocks of pointers ──► data blocks
        1 triple indirect   ──► three levels of pointer blocks ──► data blocks
```

**Maximum file size** with block size B and pointer size P (pointers per block `n = B / P`):

```text
Max size = (12 + n + n² + n³) × B
```

**Example:** B = 4 KB, P = 4 bytes → n = 1024.

| Part | Size |
|---|---|
| 12 direct | 12 × 4 KB = 48 KB |
| Single indirect | 1024 × 4 KB = 4 MB |
| Double indirect | 1024² × 4 KB = 4 GB |
| Triple indirect | 1024³ × 4 KB = 4 TB |
| **Total** | ≈ 4 TB + 4 GB + 4 MB + 48 KB |

Small files need **no extra disk reads for pointers**; the number of disk accesses to reach a byte grows with the file's offset (direct: 1 data block; single indirect: 2; double: 3; triple: 4, ignoring caching).

### Comparison of Allocation Methods

| Aspect | Contiguous | Linked | FAT | Indexed (inode) |
|---|---|---|---|---|
| Sequential access | Excellent | Good | Good | Good |
| Direct access | **Excellent** | Poor | Good (table cached) | Good |
| External fragmentation | **Yes** | No | No | No |
| File growth | Hard | Easy | Easy | Easy (to a limit) |
| Reliability | Good | Poor (one bad pointer) | Table is critical | Index block is critical |

## Free-Space Management

| Method | Description | Notes |
|---|---|---|
| **Bit vector (bitmap)** | One bit per block (a set bit marks a free block in many systems; the convention varies) | Easy to find contiguous free blocks; the bitmap for a big disk is large (1 TB with 4 KB blocks: 2²⁸ bits = 32 MB) |
| Linked list | Free blocks chained together | No waste, but slow to traverse |
| Grouping | The first free block holds addresses of *n* other free blocks | Many free blocks found quickly |
| Counting | Store (first free block, number of consecutive free blocks) | Good when free space is contiguous |
| Space maps | Log-structured updates to bitmaps (ZFS) | Scales to large volumes |

## Directory Implementation

| Method | Pros | Cons |
|---|---|---|
| Linear list | Simple | Slow search (O(n)) |
| Hash table | Fast lookup | Collisions, fixed size issues |
| B-tree / B+ tree (ext4 HTree, NTFS) | Fast search and ordered listing | More complex |

## Disk Space, Caching and Performance

- **Block size trade-off:** large blocks give higher transfer rates and smaller tables but more internal fragmentation (average waste = half a block per file); small blocks do the opposite.
- **Buffer cache / page cache:** disk blocks cached in RAM to avoid repeated I/O. **Read-ahead** prefetches sequential data; **write-back** delays writes (faster, but risky on a crash); **write-through** writes at once.
- **Fragmentation of files:** defragmentation helps hard disks, not SSDs.

## Consistency and Journaling

A single logical operation (e.g., creating a file) updates several structures (inode, directory, bitmap). A crash in the middle leaves the file system **inconsistent**.

| Technique | Idea |
|---|---|
| `fsck` / `chkdsk` | Scan the entire file system after a crash and repair it (slow) |
| **Journaling (write-ahead logging)** | First write the intended changes to a **journal (log)**, then apply them; after a crash, replay or discard the journal (fast recovery). Modes: *metadata-only* (default in ext4) or *full data* journaling |
| **Copy-on-write file systems** (ZFS, Btrfs, APFS) | Never overwrite in place; write new blocks and atomically switch the root pointer; enables snapshots |
| Log-structured file systems | The entire disk is a log written sequentially (good for SSDs and flash) |

## Common File Systems

| File system | Used by | Notes |
|---|---|---|
| **FAT32 / exFAT** | USB sticks, cameras | Simple; FAT32 has a 4 GB maximum file size and no permissions |
| **NTFS** | Windows | Master File Table (MFT), journaling, ACLs, compression, encryption |
| **ext4** | Linux | Extents, journaling, large files and volumes |
| **XFS / Btrfs / ZFS** | Linux servers, storage systems | Scalability, snapshots, checksums |
| **APFS** | macOS, iOS | Copy-on-write, snapshots |
| **NFS / SMB** | Network file sharing | Remote access through the VFS layer |

## File Protection and Permissions

UNIX permissions are three sets of **r**ead (4), **w**rite (2), e**x**ecute (1) bits for **owner**, **group** and **others**.

```text
-rwxr-xr--   =  owner: rwx (7)   group: r-x (5)   others: r-- (4)   →  chmod 754 file
```

| Command | Effect |
|---|---|
| `chmod 644 f` | Owner read/write; others read only |
| `chmod u+x f` | Add execute for the owner |
| `chown user:group f` | Change owner and group |
| `umask 022` | New files get 666 − 022 = 644; new directories 777 − 022 = 755 |

For a **directory**, `r` means list the names, `w` means create or delete entries, and `x` means enter it and access entries by name.

| Special bit | Effect |
|---|---|
| **setuid / setgid** | The program runs with the file owner's / group's privileges |
| **sticky bit** (on directories such as `/tmp`) | Only the file's owner may delete or rename it |

**Access Control Lists (ACLs)** grant permissions per user or group beyond owner/group/other. An **access matrix** (rows = domains, columns = objects) is the general model; ACLs are its **columns**, capability lists are its **rows**.

---

# I/O Systems and Disk Scheduling

## I/O Hardware

| Concept | Description |
|---|---|
| **Device controller** | Hardware that operates a device and has registers (status, command, data) |
| **Port-mapped I/O** | Special I/O instructions (`in`, `out`) and a separate address space |
| **Memory-mapped I/O** | Device registers appear as ordinary memory addresses |
| Bus | Path connecting CPU, memory and controllers |

### Three Ways to Do I/O

| Method | How it works | CPU involvement |
|---|---|---|
| **Programmed I/O (polling)** | The CPU repeatedly checks the device's status register (busy waiting) | Constant; wasteful for slow devices, fine for very fast ones |
| **Interrupt-driven I/O** | The CPU starts the operation and does other work; the device interrupts when ready | Per byte or per word |
| **DMA** | The CPU programs a DMA controller (address, count, direction); data moves between device and memory directly; one interrupt at the end | Minimal; best for bulk transfers |

## I/O Software Layers

```text
User-level I/O software     (libraries, spooling)
Device-independent OS layer (naming, buffering, caching, protection, error reporting)
Device drivers              (device-specific code)
Interrupt handlers          (hardware-triggered)
Hardware
```

| Concept | Meaning |
|---|---|
| **Buffering** | Temporary storage that smooths speed mismatches between producer and consumer, adapts transfer sizes, and supports copy semantics (single, double or circular buffers) |
| **Caching** | A fast copy of data that is also stored elsewhere |
| **Spooling** | A buffer for devices that cannot interleave data (a printer queue on disk) |
| Blocking I/O | The calling process sleeps until the operation completes |
| Non-blocking I/O | Returns immediately with whatever is available |
| **Asynchronous I/O** | The call returns at once, and the completion is signalled later (`io_uring`, overlapped I/O) |
| Device types | **Block devices** (disks: fixed-size blocks, random access) and **character devices** (keyboards: byte streams) |

## Disk Structure and Access Time

A hard disk has **platters** divided into **tracks**, tracks into **sectors**; the same track on all platters forms a **cylinder**.

```text
Disk access time = seek time + rotational latency + transfer time
Average rotational latency = ½ × (60 / RPM) seconds
Transfer time = data size / transfer rate
```

**Example:** 7200 RPM, average seek 8 ms, transfer rate 100 MB/s, reading 4 KB:
rotational latency = ½ × 8.33 ms = 4.17 ms; transfer = 4 KB / 100 MB/s = 0.04 ms; total ≈ 8 + 4.17 + 0.04 ≈ **12.2 ms**. Seek and rotation dominate, so the OS tries to **reduce head movement**.

## Disk Scheduling Algorithms

The OS orders pending requests to minimize total seek distance (**head movement**) and improve fairness.

| Algorithm | Rule | Properties |
|---|---|---|
| **FCFS** | Serve in arrival order | Fair, but long head movements |
| **SSTF** (Shortest Seek Time First) | Serve the request closest to the current head position | Lower movement, but **starvation** of distant requests |
| **SCAN (elevator)** | Head moves in one direction serving requests to the end of the disk, then reverses | No starvation; favours the middle of the disk |
| **C-SCAN** | Moves in one direction only; at the end, jumps back to the start without serving on the return | More uniform wait times |
| **LOOK** | Like SCAN, but reverses at the **last request** instead of the disk end | Less wasted movement |
| **C-LOOK** | Like C-SCAN, but jumps from the last request to the first request | Common practical variant |

### Worked Example

Cylinders 0–199, request queue (in arrival order): **98, 183, 37, 122, 14, 124, 65, 67**; head starts at **53**.

```text
FCFS : 53→98→183→37→122→14→124→65→67
       45 + 85 + 146 + 85 + 108 + 110 + 59 + 2                               = 640 cylinders

SSTF : 53→65→67→37→14→98→122→124→183
       12 + 2 + 30 + 23 + 84 + 24 + 2 + 59                                    = 236 cylinders

SCAN (moving toward 0 first): 53→37→14→0→65→67→98→122→124→183
       16 + 23 + 14 + 65 + 2 + 31 + 24 + 2 + 59                               = 236 cylinders

LOOK (toward 0 first): 53→37→14→65→67→98→122→124→183
       16 + 23 + 51 + 2 + 31 + 24 + 2 + 59                                    = 208 cylinders

C-SCAN (moving up): 53→65→67→98→122→124→183→199 | jump to 0 | →14→37
       146 up to 199, then 199 (return jump), then 37                          = 382 with the return jump (183 without it)

C-LOOK (moving up): 53→65→67→98→122→124→183 | jump to 14 | →37
       130 + 169 (jump) + 23                                                   = 322 with the jump (153 without it)
```

| Algorithm | Total head movement |
|---|---|
| FCFS | 640 |
| SSTF | 236 |
| SCAN (down first) | 236 |
| LOOK (down first) | 208 |
| C-SCAN (up) | 382 (183 without the return jump) |
| C-LOOK (up) | 322 (153 without the return jump) |

> State whether the **return seek** is counted, and the initial direction, as it changes the answer. SCAN/C-SCAN go to the **physical end** of the disk (0 or 199); LOOK/C-LOOK stop at the last request.

**Choosing:** SSTF or LOOK for light loads; SCAN/C-SCAN/LOOK for heavy loads (no starvation). On **SSDs** there is no seek, so simple schedulers (`none`, `mq-deadline`) are preferred. Linux uses **mq-deadline**, **BFQ**, **Kyber** or **none**.

## Disk Management

| Task | Description |
|---|---|
| Low-level formatting | Divides the disk into sectors with headers, data area and error-correcting codes |
| Partitioning | Splits the disk into logical volumes (MBR or GPT partition table) |
| Logical formatting | Creates the file system structures |
| Boot block / bootloader | Starts the OS |
| Bad-block handling | Spare sectors replace bad ones (sector sparing / slipping) |
| Swap space | Raw partition or file for virtual memory |

## SSDs vs Hard Disks

| Aspect | HDD | SSD |
|---|---|---|
| Medium | Spinning magnetic platters | Flash memory chips |
| Random access | Slow (seek and rotation) | Fast, no moving parts |
| Writing | In place | Must erase a block before rewriting; **wear leveling**, **TRIM**, garbage collection |
| Defragmentation | Helpful | Pointless, causes wear |
| Cost per GB | Lower | Higher |

## RAID (Redundant Array of Independent Disks)

| Level | Technique | Min. disks | Usable capacity (n disks of size S) | Fault tolerance |
|---|---|---|---|---|
| **RAID 0** | Striping (no redundancy) | 2 | n × S | None (one failure loses all) |
| **RAID 1** | Mirroring | 2 | S (for 2 disks) | One disk (per mirror pair) |
| **RAID 5** | Striping + **distributed single parity** | 3 | (n − 1) × S | **One** disk |
| **RAID 6** | Striping + **double distributed parity** | 4 | (n − 2) × S | **Two** disks |
| **RAID 10** | Mirrored pairs, then striped | 4 | n/2 × S | One per mirror pair (up to n/2 total) |

Parity = XOR of the data blocks; a lost block is rebuilt by XORing the survivors. RAID 5 has a **small-write penalty** (a write needs 2 reads + 2 writes to update data and parity). RAID improves performance and availability but is **not a backup** (it does not protect against deletion, corruption or fire).

---

# Protection and Security Fundamentals

## Protection vs Security

**Protection** controls access to resources *inside* the system (which process or user may use which file, device or memory region). **Security** defends the system against *external and internal* threats (attackers, malware, misuse).

## Principles

| Principle | Meaning |
|---|---|
| **Least privilege** | Give each user, process and program only the access it needs |
| **Separation of privilege / defence in depth** | Several independent layers of protection |
| Fail-safe defaults | Deny unless explicitly allowed |
| Complete mediation | Check every access, every time |
| Open design | Security must not depend on secrecy of the design |

## Authentication and Authorization

| Concept | Description |
|---|---|
| Authentication | Proving identity: password (store only **salted hashes**), biometrics, tokens, **multi-factor authentication** |
| Authorization | Deciding what an authenticated identity may do (permissions, ACLs, roles, capabilities) |
| Auditing | Logging who did what |

## Protection Rings and Privilege Levels

CPUs provide privilege levels (x86 rings 0–3; most OSs use ring 0 for the kernel and ring 3 for applications). Memory protection (page tables with user/supervisor and read/write/execute bits) stops a user process from touching kernel memory or another process's memory.

## Common Threats and Defences

| Threat | Description | Defence |
|---|---|---|
| **Buffer overflow** | Writing past the end of a buffer overwrites the return address or other data | Bounds checking, safe languages, **stack canaries**, **DEP/NX** (non-executable stack), **ASLR** |
| Malware (virus, worm, trojan, ransomware) | Malicious code that spreads, hides or encrypts data | Updates, least privilege, antivirus, backups |
| Privilege escalation | Gaining kernel/admin rights through a bug | Patching, sandboxing, minimal setuid programs |
| Denial of service | Exhausting CPU, memory or network | Resource limits (cgroups, quotas), rate limiting |
| Race conditions (TOCTOU) | State changes between a check and its use | Atomic operations, file descriptors instead of paths |
| Injection | Untrusted input interpreted as code or commands | Validate and sanitize input; parameterized APIs |
| Side channels (e.g., Spectre/Meltdown) | Leaking data through timing or speculation | Microcode and kernel mitigations |

## Cryptography Basics Used by the OS

| Tool | Purpose |
|---|---|
| Hash (SHA-256) | Integrity checks, password storage (with salt) |
| Symmetric encryption (AES) | Disk and file encryption (LUKS, BitLocker, FileVault) |
| Public-key cryptography (RSA, ECC) | Key exchange, digital signatures (signed updates, SSH keys, TLS) |
| Secure boot / TPM | Verify that the bootloader and kernel are trusted |

---

# Linux/UNIX Essentials Every Engineer Should Know

## Shell Basics

| Feature | Example | Meaning |
|---|---|---|
| Redirect output | `cmd > out.txt`, `cmd >> out.txt` | Overwrite / append stdout |
| Redirect input | `cmd < in.txt` | Read stdin from a file |
| Redirect errors | `cmd 2> err.txt`, `cmd > all.txt 2>&1` | stderr to a file / both to one file |
| Pipe | `ps aux \| grep nginx \| wc -l` | stdout of one command becomes stdin of the next |
| Background job | `cmd &`, `jobs`, `fg`, `bg`, Ctrl+Z | Job control |
| Exit status | `echo $?` | 0 = success; non-zero = failure |
| Command chaining | `a && b`, `a \|\| b`, `a ; b` | Run b only if a succeeds / fails / always |
| Environment | `export PATH=$PATH:/opt/bin`, `env` | Variables inherited by child processes |

## Essential Commands

| Task | Commands |
|---|---|
| Processes | `ps aux`, `top`/`htop`, `kill -15 PID`, `kill -9 PID`, `pkill name`, `nice`, `renice` |
| Memory and CPU | `free -h`, `vmstat 1`, `uptime` (load average), `lscpu` |
| Disk and file systems | `df -h`, `du -sh dir`, `lsblk`, `mount`, `fsck`, `iostat` |
| Files | `ls -l`, `cp`, `mv`, `rm`, `ln` (`ln -s` = soft link), `find`, `stat`, `cat`, `less`, `head`, `tail -f` |
| Text processing | `grep`, `sed`, `awk`, `sort`, `uniq`, `cut`, `wc` |
| Permissions | `chmod`, `chown`, `umask`, `sudo` |
| Open files and ports | `lsof`, `ss -tulpn`, `netstat` |
| Debugging | `strace` (system calls), `ltrace` (library calls), `gdb`, `perf`, `dmesg` |
| Scheduling jobs | `cron`, `crontab -e`, `systemd` timers, `at` |
| Services | `systemctl status|start|stop|enable service`, `journalctl -u service` |

## Useful Filesystem Locations

| Path | Contents |
|---|---|
| `/proc` | Virtual files exposing process and kernel information (`/proc/PID/status`, `/proc/meminfo`, `/proc/cpuinfo`) |
| `/dev` | Device files (`/dev/sda`, `/dev/null`, `/dev/random`) |
| `/etc` | System configuration |
| `/var/log` | Log files |
| `/tmp` | Temporary files |
| `/home`, `/root` | User home directories |
| `/bin`, `/usr/bin`, `/sbin` | Executables |

## Load Average

`uptime` shows three numbers: the average number of processes that are **running or waiting to run (or in uninterruptible I/O wait)** over 1, 5 and 15 minutes. A load around the number of CPU cores means the CPU is fully used; much higher means processes are queueing.

---

# Multicore, Cache Coherence and NUMA

| Concept | Description |
|---|---|
| **Cache coherence** | When several cores cache the same memory line, the hardware protocol (e.g., MESI: Modified, Exclusive, Shared, Invalid) keeps copies consistent |
| **False sharing** | Unrelated variables in one cache line, written by different cores, cause constant invalidations (fix by padding or aligning data to cache lines) |
| **NUMA** (Non-Uniform Memory Access) | Memory attached to each CPU socket; accessing remote memory is slower, so the OS tries to allocate and schedule locally |
| **Memory consistency** | Hardware and compilers may reorder reads/writes; use locks, atomics or fences for correct cross-thread visibility |
| **Hyper-threading (SMT)** | One physical core presents two logical CPUs to share execution units |

---

# Diagnosing Common Operating System Problems

| Symptom | Likely causes | What to check |
|---|---|---|
| System is slow, high load average | CPU saturation, I/O wait, memory pressure | `top`, `vmstat 1`, `iostat -x 1` |
| High CPU in `%wa` (I/O wait) | Slow disk or too many I/O requests | `iostat`, `iotop` |
| Out of memory, processes killed | Memory leak, no swap, undersized RAM | `free -h`, `dmesg \| grep -i oom`, `ps aux --sort=-%mem` |
| Heavy swapping (high `si`/`so` in `vmstat`) | Thrashing, working sets exceed RAM | `vmstat`, reduce memory use or add RAM |
| Many zombie processes | Parent never calls `wait()` | `ps aux \| grep Z`; fix or restart the parent |
| "Too many open files" | File-descriptor leak or low `ulimit -n` | `lsof -p PID`, raise the limit, close descriptors |
| "No space left on device" with free space shown | Out of **inodes** | `df -i` |
| Process hangs | Deadlock, waiting on I/O or a lock | `strace -p PID`, `gdb` thread dump |
| Port already in use | Another process is bound to it | `ss -tulpn \| grep PORT` |

---

# Comparison: Windows, Linux and macOS

| Aspect | Linux | Windows | macOS |
|---|---|---|---|
| Kernel | Monolithic (modular) | Hybrid (NT) | Hybrid (XNU: Mach + BSD) |
| Source model | Open source | Proprietary | Proprietary (Darwin partly open) |
| Default file system | ext4 / XFS / Btrfs | NTFS | APFS |
| Process creation | `fork()` + `exec()` | `CreateProcess()` | `fork()` + `exec()` (and `posix_spawn`) |
| Package/service management | apt, dnf, systemd | MSI/winget, Services | Homebrew, launchd |
| Typical use | Servers, embedded, cloud, Android base | Desktops, enterprise | Developer and creative desktops |

---

# Scenario-Based Questions and Answers

## Question 1: Counting Processes

**Scenario:** A program executes `fork(); fork(); fork();` and then prints a line. How many lines are printed and how many child processes are created?

**Answer:** Each `fork()` doubles the number of processes, so three forks give 2³ = **8 processes** and the line is printed **8 times**. The number of children created is 2³ − 1 = **7**.

---

## Question 2: Zombie Processes Piling Up

**Scenario:** A server forks a child for each request. After a few days `ps` shows thousands of `<defunct>` entries and new forks start failing.

**Answer:** These are **zombie processes**: the children finished, but the parent never called `wait()`/`waitpid()`, so their PCB entries remain and consume PID slots. Fix the parent by calling `waitpid()` (for example in a `SIGCHLD` handler), or by ignoring `SIGCHLD` with `SA_NOCLDWAIT` so the kernel reaps them automatically. Killing a zombie is not possible (it is already dead); killing or restarting the parent makes `init` adopt and reap them.

---

## Question 3: Lost Updates in a Multithreaded Counter

**Scenario:** Two threads each increment a global counter one million times, but the final value is only about 1.4 million.

**Answer:** `counter++` is a read-modify-write of three machine steps, so the threads interleave and overwrite each other's updates (a **race condition**). Protect the increment with a **mutex** (or use an **atomic increment** such as `atomic_fetch_add`) so that the three steps execute as one indivisible unit. The critical section should be as short as possible to keep contention low.

---

## Question 4: Choosing a Scheduling Algorithm

**Scenario:** A system runs interactive editors and a few long batch jobs. Users complain that typing lags whenever a batch job runs.

**Answer:** FCFS (or a non-preemptive policy) lets a long job hold the CPU, causing the **convoy effect**. Use a **preemptive, time-sliced** policy such as **Round Robin** or, better, a **Multilevel Feedback Queue**: interactive processes use short CPU bursts and stay in high-priority queues with short quanta, while CPU-bound batch jobs sink to lower queues with longer quanta. Aging prevents the batch jobs from starving.

---

## Question 5: Deadlock Between Two Locks

**Scenario:** Thread 1 locks `accounts` then `audit_log`; Thread 2 locks `audit_log` then `accounts`. Under load the service freezes.

**Answer:** This is a classic **circular wait**: each thread holds one lock and waits for the other, and all four Coffman conditions hold. Break circular wait by imposing a **global lock ordering** (always `accounts` before `audit_log`). Alternatives: acquire both locks together with `trylock` and back off on failure, use timeouts, or restructure the code so that only one lock is needed.

---

## Question 6: Is the System in a Safe State?

**Scenario:** A system has 12 tape drives. P1 holds 5 and may need 10 in total, P2 holds 2 and may need 4, P3 holds 2 and may need 9. Is the state safe?

**Answer:** Available = 12 − (5 + 2 + 2) = 3. Remaining needs: P1 needs 10 − 5 = 5, P2 needs 4 − 2 = 2, P3 needs 9 − 2 = 7. Apply the safety algorithm with Work = 3:

```text
P2: need 2 ≤ 3 → can finish, Work = 3 + 2 (its allocation) = 5
P1: need 5 ≤ 5 → can finish, Work = 5 + 5 = 10
P3: need 7 ≤ 10 → can finish
Safe sequence: ⟨P2, P1, P3⟩  → the state is SAFE
```

Had one more drive been granted to P3 (so P3 holds 3 and only 2 remain), P2 could still finish (Work becomes 4), but then neither P1 (needs 5) nor P3 (needs 6) could proceed, so that state would be **unsafe**. This is exactly the request the Banker's algorithm refuses.

---

## Question 7: Priority Inversion

**Scenario:** A low-priority logging task holds a mutex needed by a high-priority control task. A medium-priority computation task keeps the CPU busy, and the control task misses its deadlines.

**Answer:** This is **priority inversion**: the medium task indirectly blocks the high-priority task because it keeps preempting the low-priority lock holder. Enable **priority inheritance** on the mutex so the low-priority task temporarily runs at the high priority until it releases the lock (or use the priority ceiling protocol).

---

## Question 8: Page Table Size and Levels

**Scenario:** A system has 48-bit virtual addresses, 4 KB pages and 8-byte page-table entries. How many page-table levels are needed if each table must fit in a single page?

**Answer:** The offset takes 12 bits, leaving 36 bits for the page number. One page holds 4096 / 8 = 512 entries, which can be indexed with 9 bits. The number of levels is 36 / 9 = **4** levels. A flat table would need 2³⁶ entries × 8 B = 512 GB per process, which is why multilevel tables (or inverted tables) are used.

---

## Question 9: Effective Memory Access Time with a TLB

**Scenario:** The TLB access takes 10 ns, memory access takes 100 ns, and the hit ratio is 95%. The system uses a two-level page table. What is the effective access time?

**Answer:** On a TLB hit: 10 + 100 = 110 ns. On a miss: 10 ns (TLB) + 2 × 100 ns (two page-table levels) + 100 ns (data) = 310 ns.
EAT = 0.95 × 110 + 0.05 × 310 = 104.5 + 15.5 = **120 ns**.

---

## Question 10: Why Does Adding Memory Make Things Worse?

**Scenario:** A system using FIFO page replacement runs a program with the reference string `1 2 3 4 1 2 5 1 2 3 4 5`. Going from 3 to 4 frames increases the page faults.

**Answer:** This is **Belady's anomaly**: FIFO has 9 faults with 3 frames and 10 faults with 4 frames, because FIFO is not a stack algorithm (pages resident with *n* frames need not be a subset of those resident with *n + 1* frames). **LRU** and **OPT** never show this behaviour, so switching to LRU or a clock approximation avoids it.

---

## Question 11: System Becomes Extremely Slow When More Jobs Are Started

**Scenario:** CPU utilization is 5%, the disk is constantly busy, and the administrator starts more jobs to "use the idle CPU". The system gets even slower.

**Answer:** This is **thrashing**: processes lack enough frames for their working sets and spend nearly all their time swapping pages. Low CPU utilization here is a symptom of paging, not spare capacity. Reduce the degree of multiprogramming (suspend or kill processes), use the **working-set** or **page-fault-frequency** policy, or add RAM. Starting more jobs makes it worse.

---

## Question 12: Maximum File Size

**Scenario:** An ext2-style inode has 10 direct pointers, 1 single, 1 double and 1 triple indirect pointer. The block size is 1 KB and a pointer is 4 bytes. What is the maximum file size?

**Answer:** Pointers per block = 1024 / 4 = 256. Data blocks addressable = 10 + 256 + 256² + 256³ = 10 + 256 + 65,536 + 16,777,216 = 16,843,018 blocks. Maximum size = 16,843,018 × 1 KB ≈ **16.06 GB** (about 16 GB, dominated by the triple-indirect part).

---

## Question 13: Hard Link or Symbolic Link?

**Scenario:** You want another name for a file on the same partition that keeps working even if the original name is deleted. Another case: you need a shortcut to a directory on a different partition.

**Answer:** For the first case use a **hard link** (`ln file alias`): both names point to the same inode and the data lives until the link count drops to zero. For the second, use a **symbolic link** (`ln -s /other/dir shortcut`): hard links cannot cross file systems and normally cannot point to directories. The cost is that a symbolic link breaks if its target is moved or removed.

---

## Question 14: Disk Scheduling Choice

**Scenario:** A busy file server has a hard disk with hundreds of pending requests, and some requests at the far edge of the disk are waiting for a very long time under SSTF.

**Answer:** SSTF favours requests near the head and can **starve** distant ones. Switch to **SCAN, C-SCAN, LOOK or C-LOOK**, which sweep the head across the disk and bound every request's waiting time. C-LOOK is a common choice because it gives uniform waiting times without wasting movement to the disk edges. On an SSD, use a simple scheduler such as `none` or `mq-deadline`.

---

## Question 15: Surviving a Crash in the Middle of Writing

**Scenario:** The power fails while a file is being created. After reboot, a directory entry points to an inode that was never initialized.

**Answer:** The create operation updates multiple structures (inode, directory entry, bitmap) and was interrupted, leaving the file system **inconsistent**. A **journaling file system** (ext4, NTFS, XFS) writes the intended changes to a log first, so on reboot it replays committed transactions and discards incomplete ones, restoring consistency in seconds. Without a journal, `fsck` must scan the entire volume. Applications needing durability for their own data should also call `fsync()`.

---

## Question 16: Choosing an IPC Mechanism

**Scenario:** (a) A parent process pipes a log stream to a child that compresses it. (b) Two unrelated processes on one machine must exchange large video frames at high speed. (c) A client on another machine must talk to a server.

**Answer:** (a) An anonymous **pipe**: simple, built-in flow control and synchronization. (b) **Shared memory** (with a semaphore or mutex for synchronization): no per-message copying through the kernel. (c) A **socket** (TCP for reliable streams): the only one of the three that works across machines.

---

## Question 17: Semaphore Value Puzzle

**Scenario:** A counting semaphore is initialized to 2. Five processes each execute `wait(S)` once, and then two of them execute `signal(S)`. What is the value of S and how many processes are blocked at the end?

**Answer:** After 5 waits: S = 2 − 5 = −3, so 3 processes are blocked (the first two passed). After 2 signals: S = −3 + 2 = **−1**, and two blocked processes were woken, leaving **1 process still blocked**.

---

## Question 18: Why Is My Multithreaded Program Not Faster?

**Scenario:** A program is 30% serial. It is moved from 1 to 8 cores but the speed-up is much lower than 8×.

**Answer:** By **Amdahl's law**, speed-up = 1 / (0.30 + 0.70/8) = 1 / 0.3875 ≈ **2.58×**, and the limit even with infinite cores is 1/0.30 ≈ 3.33×. The serial fraction dominates. Improve the speed-up by shrinking the serial part (less locking, parallelize initialization and I/O), and check for lock contention and false sharing, which add hidden serial work.

---

## Question 19: Container or Virtual Machine?

**Scenario:** A company wants to run many isolated instances of the same Linux web service on one server, starting in milliseconds. A second team needs to run a Windows application on the same Linux host.

**Answer:** For many Linux instances of one service, use **containers**: they share the host kernel, use **namespaces** for isolation and **cgroups** for resource limits, and start in milliseconds with little overhead. The Windows application needs a different kernel, so it requires a **virtual machine** under a hypervisor (for example KVM). Containers give lighter isolation (shared kernel); VMs give stronger isolation at higher cost.

---

## Question 20: Memory Leak on a Long-Running Server

**Scenario:** A server's memory usage grows steadily until the OOM killer terminates it.

**Answer:** The likely cause is a **memory leak**: allocated heap memory is never freed (or objects are retained in growing caches). Confirm with `ps`/`top` (resident set size rising over time) and `/proc/PID/status`, then locate it using tools such as Valgrind, AddressSanitizer, or a heap profiler. Mitigations: fix the leak, bound caches, set memory limits with cgroups so one process cannot take down the host, and monitor memory so the problem is caught before the OOM killer acts.

---

# Quick Formula Cheat Sheet

| Topic | Formula |
|---|---|
| Turnaround / waiting / response time | TAT = CT − AT; WT = TAT − BT; RT = first run − AT |
| Fork count | n sequential forks → 2ⁿ processes, 2ⁿ − 1 children |
| Amdahl's law | Speed-up = 1 / (S + (1 − S)/N) |
| Round Robin efficiency | q / (q + s) |
| RMS bound / EDF bound | U ≤ n(2^(1/n) − 1) / U ≤ 1 |
| Deadlock-free resources (n processes, each needs ≤ k) | R ≥ n(k − 1) + 1 |
| Semaphore with initial value n | value = n − (waits) + (signals) |
| Page number bits | log₂(logical space) − log₂(page size) |
| Page table size | Number of pages × entry size |
| Optimal page size | √(2 × process size × entry size) |
| EAT with TLB (1-level) | α(t + m) + (1 − α)(t + 2m) |
| EAT with page faults | (1 − p) × m + p × fault service time |
| Disk access time | seek + ½ × (60/RPM) + transfer |
| UNIX inode max file size | (direct + n + n² + n³) × block size, n = block size / pointer size |
| FAT size | Number of blocks × entry size |
| Average rotational latency | ½ × 60 / RPM seconds |
| RAID capacity | RAID 0: n·S, RAID 1: n·S/2, RAID 5: (n − 1)·S, RAID 6: (n − 2)·S, RAID 10: n·S/2 |

---

# Common Operating System Mistakes

```text
1. Forgetting that fork() returns twice (0 in the child, the PID in the parent) and not checking for -1.
2. Never calling wait(), leaving zombie processes.
3. Leaving the unused ends of a pipe open, so the reader never sees end-of-file.
4. Using a plain variable shared by threads without a lock or atomic (race condition).
5. Acquiring several locks in different orders in different threads (deadlock).
6. Using "if" instead of "while" around a condition-variable wait.
7. Holding a lock while doing slow I/O or calling unknown code.
8. Assuming a larger quantum or more RAM always helps (Round Robin degeneration, Belady's anomaly with FIFO).
9. Counting the cost of disk scheduling without stating the starting direction and whether return jumps are included.
10. Confusing internal fragmentation (inside an allocated block) with external fragmentation (between blocks).
11. Believing a cycle in a resource allocation graph always means deadlock (true only for single-instance resources).
12. Treating RAID as a backup.
13. Running everything as root instead of using least privilege.
14. Using kill -9 as the first choice instead of letting a process shut down cleanly with SIGTERM.
```

**Remember:** an operating system is a **resource manager** and a **protection layer**. Almost every concept in this guide (scheduling, synchronization, memory management, file systems) is a different answer to the same question: *how do many programs share limited hardware safely, fairly and efficiently?*