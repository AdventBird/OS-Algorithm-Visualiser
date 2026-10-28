# OS Algorithm Visualizer

A browser-based mini-project that demonstrates operating-system concepts through an online order-processing scenario. It is built with **HTML, CSS, and vanilla JavaScript** in one file, requires no backend, and works offline.

## 1. How to run the project

1. Download `OS Algorithm Visualizer – Order Processing.html` and `README.md` into the same folder.
2. Open the HTML file by double-clicking it, or right-click it and choose a modern browser such as Firefox or Chrome.
3. No installation, server, package manager, or internet connection is required.
4. Use the tabs to switch between the five simulations.

> **Note:** Changes made with **Edit orders** are kept in the current page session. Reloading the page restores the original default orders; this version does not save edits to a file or browser storage.

## 2. Shared Orders table

The table at the top is the common data source for CPU scheduling, Banker's Algorithm, memory management, and the integrated pipeline.

| Field | Meaning |
|---|---|
| Order | Process/order ID (`P0`–`P4`) |
| Arrival | Time at which the order becomes available to the CPU scheduler |
| Burst | CPU processing time required by the order |
| Memory (MB) | Memory requested by the order |
| Allocation (A B C) | Currently allocated units of each shared resource |
| Max (A B C) | Maximum units of each resource the order may require |

The shared resources are **A = Payment Gateway (10 units)**, **B = Inventory Service (5 units)**, and **C = DB Connections (7 units)**. The initial allocations result in an Available vector of `(3, 3, 2)`. The memory blocks are `100, 500, 200, 300, 600 MB`.

### Editing the orders

1. Click **Edit orders** beside the Shared orders heading.
2. Edit the numeric fields in the table. Allocation and Max each have three separate inputs, corresponding to A, B, and C.
3. Click **Save changes** to validate and apply the values. The app refreshes the module displays and simulations using the updated order data.
4. Click **Cancel** to discard unsaved changes, or **Restore defaults** to return to the original five orders.

The form checks for whole-number values, positive burst and memory sizes, non-negative arrival/allocation/maximum values, allocations that do not exceed their maximums, maximum values within the system resource totals, and total allocated resources within the available system totals. If validation fails, correct the values shown in the message before saving.

## 3. Tab-by-tab guide

### Tab 1 — Producer–Consumer (bounded buffer)

**Purpose:** Demonstrates a shared bounded queue between a producer (which inserts orders) and a consumer (which removes them). The buffer must not overflow or underflow.

**How to use it**

1. Set **Buffer size** to the number of slots you want (minimum 1, maximum 10).
2. Click **Produce order** to place a new item in the next available slot.
3. Click **Consume order** to remove the oldest item in the buffer.
4. Click **Auto-run** to alternate producer and consumer actions automatically. Click **Pause** (the same button while running) to stop the automatic actions.
5. Click **Reset** to clear the buffer, counters, and event log.

**What to observe**

- **mutex** represents exclusive access to the shared buffer; it is displayed as available (`1`) between actions.
- **empty** is the number of vacant slots; **full** is the number of occupied slots.
- **produced**, **consumed**, and **blocked events** count successful insertions, successful removals, and attempts blocked by a full or empty buffer.
- The event log records the wait/signal sequence and explains why an action was blocked.

Try filling the buffer completely and producing one more item to see a full-buffer block. Then empty the buffer and try consuming again to see an empty-buffer block. This tab uses generated demonstration items (`O1`, `O2`, …); it illustrates the synchronization mechanism rather than processing the shared `P0`–`P4` workload.

### Tab 2 — CPU Scheduling

**Purpose:** Compares scheduling policies and shows how each order gets CPU time.

**How to use it**

1. Select an algorithm from the dropdown:
   - **FCFS (First-Come, First-Served):** processes orders in arrival order.
   - **SJF (non-preemptive):** chooses the shortest available burst and runs it to completion.
   - **Round Robin:** gives each ready process a time slice, then returns unfinished processes to the ready queue.
2. For Round Robin, enter a **Quantum** of 1 or more. The default is `2`.
3. Click **Run simulation** to animate the Gantt chart. Each segment shows which process uses the CPU and the time boundaries.
4. Read the results table after the animation completes. The algorithm comparison table below lists average turnaround and waiting times for FCFS, SJF, and Round Robin with quantum 2.

**Metric definitions**

- **Completion Time (CT):** time at which the process finishes.
- **Turnaround Time (TAT):** `CT − Arrival Time`.
- **Waiting Time (WT):** `TAT − Burst Time`.
- **Average TAT / Average WT:** arithmetic averages across the orders.

For the default five orders, the expected comparison is:

| Algorithm | Average TAT | Average WT |
|---|---:|---:|
| FCFS | 12.2 | 7.4 |
| SJF (non-preemptive) | 10.4 | 5.6 |
| Round Robin (quantum 2) | 15.0 | 10.2 |

These figures apply to the default input values. Editing arrival or burst times changes the results. The implemented animation is started by **Run simulation**; this tab does not have a separate single-step control.

### Tab 3 — Banker's Algorithm

**Purpose:** Demonstrates deadlock avoidance. Before granting a resource request, the algorithm checks whether the system can still finish all processes in some order.

**How to use it**

1. Review the **Allocation**, **Max**, and calculated **Need** columns. `Need = Max − Allocation`.
2. Click **Check safety**. The event log shows each process that can finish, the current Work vector, and the Work vector after releasing that process's allocation. A safe sequence means the state is safe; otherwise, the result is marked **UNSAFE**.
3. To simulate a request, choose a process from the **Request** dropdown and enter the requested units for A, B, and C in the three numeric fields.
4. Click **Send request**. The request must be no greater than the process's remaining Need and no greater than the current Available vector. If both checks pass, the app temporarily allocates the request and runs the safety check. A safe result is **GRANTED**; an unsafe result is **DENIED** and not committed.
5. Use the example buttons to try the preset requests:
   - **P1 asks (1, 0, 2):** expected to be granted in the default state.
   - **P4 asks (3, 3, 0):** expected to be denied because the tentative allocation is unsafe.
6. Click **Reset state** to rebuild the allocation/maximum matrices from the current Shared orders table and recalculate Available resources.

A granted request changes the displayed Banker state until you reset it or reload the page. A denied request leaves the original state unchanged.

### Tab 4 — Memory Management

**Purpose:** Compares placement strategies for assigning the orders to the available memory blocks.

**How to use it**

1. Select **First Fit**, **Best Fit**, or **Worst Fit**.
2. Click **Allocate**.
3. Review the memory bars, allocation table, summary, and algorithm comparison table.

**How the algorithms work**

- **First Fit:** selects the first remaining hole large enough for the order.
- **Best Fit:** selects the smallest remaining hole that can fit the order.
- **Worst Fit:** selects the largest remaining hole that can fit the order.

The colored areas identify allocated orders. The hatched/grey area represents free memory remaining in each block. The summary shows total free memory, the largest remaining hole, and whether any order could not be allocated. An order may fail to fit even when the total free memory is large enough, because free memory is split across holes; this illustrates external fragmentation.

With the default data, First Fit leaves `P3` unallocated and has `869 MB` free. Best Fit allocates all five orders and has `443 MB` free. The displayed comparison lets you compare the unallocated orders, total free memory, and largest hole for all three policies.

### Tab 5 — Integrated Pipeline

**Purpose:** Connects the main concepts in a single simulation of the five shared orders.

The pipeline combines arrivals and bounded-buffer behavior, Round Robin CPU slices, a Banker safety check, Best Fit memory allocation, order processing, and resource/memory release when a process completes.

**How to use it**

1. Set the **Buffer** size and **Quantum**. Default values are `5` and `2`.
2. Click **Step** to advance one simulation action.
3. Click **Run all** to run automatically. Click **Pause** while it is running to stop it.
4. Click **Reset** to restart the pipeline from time zero using the current Shared orders values.
5. Watch the dashboard for simulation time, available resources, completed order count, and buffer occupancy. The memory bars show current allocations and free space; the results table records completion, turnaround, and waiting time for completed orders.
6. Read the **Event log** to see order arrivals, buffer blocking, Banker waiting or denial, memory allocation, CPU execution, and release when an order completes.

If the pipeline stalls, inspect the event log for a resource-safety or memory-fit condition that prevents the waiting order from being admitted. You can reset and try a different buffer size, quantum, or set of order inputs.

## 4. Suggested demonstration sequence

For a quick classroom demonstration:

1. **Producer–Consumer:** fill the buffer, trigger a blocked producer, consume items, then trigger a blocked consumer.
2. **CPU Scheduling:** run FCFS, SJF, and Round Robin and compare the Gantt chart and averages.
3. **Banker's Algorithm:** check the default safe sequence, then run both preset resource requests.
4. **Memory Management:** allocate using First Fit and Best Fit; compare the unallocated orders and free holes.
5. **Integrated Pipeline:** use Step to explain each event, then Reset and use Run all to show the full processing flow.

## 5. Current implementation notes

- The project is a visual teaching aid, not a production order-management or operating-system simulator.
- The Producer–Consumer tab uses generated queue items (`O1`, `O2`, …), while the other tabs use the shared `P0`–`P4` order data.
- In this version, CPU Scheduling supports FCFS, non-preemptive SJF, and Round Robin. Priority scheduling is not included.
- In this version, Memory Management shows allocation and comparison, but does not provide a separate manual deallocation control. The Integrated Pipeline releases memory and resources automatically when a process completes.
- The Integrated Pipeline runs the five current shared orders. A separate Peak Load control for injecting extra random orders is not included in this version.
- Edits are not persisted after reloading the HTML file.

## 6. Test cases

| Test | Action | Expected observation |
|---|---|---|
| Normal CPU load | Run each algorithm with default orders | FCFS: average TAT 12.2, WT 7.4; SJF: TAT 10.4, WT 5.6; RR q=2: TAT 15.0, WT 10.2 |
| Producer buffer full | Set buffer size to 2 and produce 3 items without consuming | Third produce action is blocked; blocked-event count increases |
| Producer buffer empty | Reset and click Consume order | Consumer is blocked; blocked-event count increases |
| Banker safety | Click Check safety with default orders | Safe sequence: P1 → P3 → P4 → P0 → P2 |
| Safe request | Reset Banker state and use P1 (1, 0, 2) | Request is granted and the new state remains safe |
| Unsafe request | Reset Banker state and use P4 (3, 3, 0) | Request is denied and state is rolled back |
| Memory First Fit | Select First Fit and click Allocate | P3 is not allocated; total free memory is 869 MB |
| Memory Best Fit | Select Best Fit and click Allocate | All five orders are allocated; total free memory is 443 MB |
| Edited order values | Edit one or more numeric fields and save | Shared table and module calculations refresh using the updated values |
| Integrated flow | Reset, then click Step or Run all | Event log shows arrivals, resource/memory decisions, CPU execution, completion, and release |

---

**Files:** `OS Algorithm Visualizer – Order Processing.html` — application; `README.md` — this guide.
