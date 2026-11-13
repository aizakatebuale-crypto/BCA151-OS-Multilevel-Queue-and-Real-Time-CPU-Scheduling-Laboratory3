# OS Laboratory Activity 3
## Multilevel Queue and Real-Time CPU Scheduling

### Student Information
- **Name:** Aiza Kate D. Buale
- **Course and Section:** BSCA B186
- **Instructor:** Maria Fe Bahinting
- **Date Submitted:** November 14, 2026

---

### I. Introduction
This laboratory activity explores the implementation and simulation of Multilevel Queue (MLQ) and real-time CPU scheduling algorithms—specifically Rate Monotonic Scheduling (RMS) and Earliest Deadline First (EDF)—using Python and GitHub. Through 1 ms time-step simulations, this activity demonstrates how different scheduling policies manage interactive processes, batch processes, and periodic real-time tasks based on queue priorities, task periods, and absolute deadlines.

---

### II. Objectives
- Explain the operation of Multilevel Queue, RMS, and EDF scheduling algorithms.
- Implement MLQ scheduling using Round Robin and FCFS.
- Implement preemptive RMS and EDF scheduling simulations using Python.
- Generate and interpret Gantt charts showing process and task execution.
- Calculate CPU utilization, waiting time, turnaround time, and deadline compliance.
- Create and manage a GitHub repository containing Python source code and laboratory documentation.

---

### III. Scheduling Algorithms
1. **Multilevel Queue (MLQ) Scheduling:** Partitions the ready queue into separate queues based on process type. Queue 1 (interactive processes) uses Round Robin (Quantum = 2 ms) and holds strict priority over Queue 2 (batch processes), which uses First Come, First Served (FCFS).
2. **Rate Monotonic Scheduling (RMS):** A static/fixed-priority preemptive scheduling algorithm for periodic real-time tasks where priority is assigned inversely to the task's period (shorter period = higher priority).
3. **Earliest Deadline First (EDF):** A dynamic-priority preemptive scheduling algorithm that prioritizes ready tasks based on which task has the earliest absolute deadline (`Release Time + Relative Deadline`).

---

### IV. Program Implementation
- **`multilevel_queue/mlq.py`:** Simulates MLQ in 1 ms increments using Python `deque` queues (`q1` and `q2`). Newly arriving Queue 1 processes immediately preempt any running Queue 2 process, while Queue 1 processes execute in 2 ms time slices.
- **`real_time_scheduling/rms.py`:** Generates periodic job instances up to `SIMULATION_TIME = 20` ms, sorts tasks by shortest period (`T1 > T2 > T3`), calculates CPU utilization against the RMS sufficient bound, and tracks deadline compliance.
- **`real_time_scheduling/edf.py`:** Generates periodic job instances over 20 ms and dynamically selects the ready job with the minimum absolute deadline at every 1 ms step, breaking ties by release time and task ID.

---

### V. Test Data

#### 1. Multilevel Queue (MLQ) Dataset (Quantum = 2 ms for Queue 1)
| Process | Arrival Time (ms) | Burst Time (ms) | Queue |
| :--- | :---: | :---: | :---: |
| P1 | 0 | 5 | 1 |
| P2 | 1 | 4 | 1 |
| P3 | 0 | 6 | 2 |
| P4 | 2 | 3 | 2 |
| P5 | 3 | 2 | 1 |

#### 2. RMS & EDF Periodic Task Dataset (Simulation Duration = 20 ms)
| Task | Execution Time (C) | Period (T) | Relative Deadline (D) |
| :--- | :---: | :---: | :---: |
| T1 | 1 ms | 4 ms | 4 ms |
| T2 | 1 ms | 5 ms | 5 ms |
| T3 | 2 ms | 10 ms | 10 ms |

---

### VI. Results and Discussion

#### 1. Multilevel Queue (MLQ) Results
- **Gantt Chart:**
  `| P1 [0-2] | P2 [2-4] | P1 [4-6] | P5 [6-8] | P2 [8-10] | P1 [10-11] | P3 [11-17] | P4 [17-20] |`
- **Performance Metrics:**
  | PID | AT | BT | CT | TAT | WT |
  | :--- | :---: | :---: | :---: | :---: | :---: |
  | P1 | 0 | 5 | 11 | 11 | 6 |
  | P2 | 1 | 4 | 10 | 9 | 5 |
  | P3 | 0 | 6 | 17 | 17 | 11 |
  | P4 | 2 | 3 | 20 | 18 | 15 |
  | P5 | 3 | 2 | 8 | 5 | 3 |
- **Average Turnaround Time:** 12.0 ms
- **Average Waiting Time:** 8.0 ms
- **Discussion:** Queue 1 processes (`P1`, `P2`, `P5`) completely monopolized the CPU from `0 ms` to `11 ms` due to strict queue priority. Batch processes (`P3` and `P4`) in Queue 2 only started executing at `11 ms` once Queue 1 became empty.

#### 2. Rate Monotonic Scheduling (RMS) Results
- **Priority Order:** `T1 > T2 > T3`
- **CPU Utilization:** 65.0%
- **RMS Sufficient Utilization Bound:** 77.98% (`PASS`)
- **Gantt Chart:**
  `| T1 [0-1] | T2 [1-2] | T3 [2-4] | T1 [4-5] | T2 [5-6] | IDLE [6-8] | T1 [8-9] | IDLE [9-10] | T2 [10-11] | T3 [11-12] | T1 [12-13] | T3 [13-14] | IDLE [14-15] | T2 [15-16] | T1 [16-17] | IDLE [17-20] |`
- **Deadline Compliance:** All 11 task instances met their deadlines (**Deadline Misses: 0**). Notice at `t = 12 ms`, `T1` preempted `T3` because `T1` has a shorter period (4 ms vs. 10 ms).

#### 3. Earliest Deadline First (EDF) Results
- **CPU Utilization:** 65.0%
- **Classical EDF Utilization Test:** `PASS` (≤ 100%)
- **Gantt Chart:**
  `| T1 [0-1] | T2 [1-2] | T3 [2-4] | T1 [4-5] | T2 [5-6] | IDLE [6-8] | T1 [8-9] | IDLE [9-10] | T2 [10-11] | T3 [11-12] | T1 [12-13] | T3 [13-14] | IDLE [14-15] | T2 [15-16] | T1 [16-17] | IDLE [17-20] |`
- **Deadline Compliance:** All 11 task instances met their deadlines (**Deadline Misses: 0**). For this specific task set, EDF produced the same execution schedule as RMS because at every point of contention (such as `t = 12 ms` where `T1`'s deadline is `16 ms` vs. `T3`'s deadline of `20 ms`), the shorter-period task also held the earlier absolute deadline.

---

### VII. Algorithm Comparison

| Criteria | MLQ | RMS | EDF |
| :--- | :--- | :--- | :--- |
| **Scheduling basis** | Queue priority | Task period | Absolute deadline |
| **Priority assignment** | Fixed queue priority | Fixed task priority | Dynamic task priority |
| **Scheduling type** | Depends on queue policy | Preemptive | Preemptive |
| **Main application** | Process classification | Periodic real-time tasks | Deadline-based real-time tasks |
| **Deadline monitoring** | Not inherent | Yes | Yes |

---

### VIII. Guide Questions & Answers

1. **What is the main difference between Multilevel Queue and Multilevel Feedback Queue scheduling?**
   In Multilevel Queue (MLQ), processes are permanently assigned to a specific queue based on their type and cannot move between queues. In Multilevel Feedback Queue (MLFQ), processes can dynamically move between queues based on their CPU burst behavior and aging, preventing starvation.

2. **Why does MLQ use different scheduling algorithms for different process queues?**
   Different process categories have different performance needs. Interactive/foreground processes require quick response times (best served by Round Robin), whereas background/batch processes prioritize throughput with minimal context-switching overhead (best served by FCFS).

3. **How does RMS determine which task receives the CPU first?**
   RMS assigns priority inversely proportional to a task's period ($T$). The ready task with the shortest period is given the highest priority and receives the CPU first.

4. **Why is RMS considered a fixed-priority scheduling algorithm?**
   A task's priority is determined statically before execution based on its constant period and never changes across different job releases during runtime.

5. **How does EDF determine which task should execute next?**
   EDF evaluates the absolute deadline (`Release Time + Relative Deadline`) of all currently ready tasks and dispatches the task with the earliest (nearest) absolute deadline.

6. **Why is EDF considered a dynamic-priority scheduling algorithm?**
   Priorities are recalculated at runtime whenever new task instances are released, meaning one task type can have higher priority than another at one moment and lower priority in a later cycle depending on their current deadlines.

7. **What happens when a newly released task has an earlier deadline than the currently running task under EDF?**
   The currently running task is immediately preempted (interrupted and placed back into the ready queue), and the CPU is allocated to the newly released task with the earlier deadline.

8. **Why is CPU utilization important in real-time scheduling?**
   CPU utilization ($U = \sum C_i / T_i$) measures the fraction of processor capacity required by a task set, allowing engineers to perform mathematical schedulability tests to guarantee whether tasks can meet their deadlines before deployment.

9. **Can a task set with CPU utilization below 100% still miss a deadline? Explain the conditions under which this may happen.**
   Yes. Under fixed-priority algorithms like RMS, a task set with utilization between the Liu & Layland bound (~77.98% for 3 tasks) and 100% can miss deadlines due to priority inversion/blocking or unfavorable task phasing. Even under EDF, deadlines can be missed below 100% utilization if relative deadlines are shorter than periods ($D_i < T_i$), tasks are non-preemptible, or context-switching overhead is significant.

10. **Which algorithm would you recommend for periodic real-time tasks, RMS or EDF? Justify your answer.**
    **RMS** is recommended for predictable, safety-critical embedded systems with low overhead because its static priorities are simple to implement and exhibit predictable behavior under overload. However, **EDF** is recommended when maximizing CPU utilization (up to 100%) is required on a single processor, as it dynamically adapts to deadlines and avoids idle capacity wasted by fixed-priority bounds.

---

### IX. Conclusion
In this laboratory activity, I successfully implemented and simulated Multilevel Queue, Rate Monotonic Scheduling, and Earliest Deadline First algorithms in Python. Analyzing the generated Gantt charts reinforced how strict queue priority can cause starvation for batch processes in MLQ, and how fixed-priority (period-based) versus dynamic-priority (deadline-based) preemption governs real-time task schedulability and deadline compliance.
