# Ferry Process Synchronization

> VUT FIT IOS Project 2 - process synchronization simulation of a ferry transporting vehicles between two ports, implemented in C using semaphores and shared memory. **Scored 15/15.**

---

##  Overview

A multi-process C program simulating a ferry (`přívoz`) shuttling trucks and cars between two ports. Synchronization is achieved exclusively via **POSIX semaphores** and **shared memory** — no active waiting, no threads, just `fork()`.

The ferry alternates between ports, unloads all vehicles, then loads waiting vehicles up to capacity (trucks count as 3 car slots). It keeps running until all vehicles have been transported.

---

##  Usage

```bash
make
./proj2 N O K TA TP
```

| Argument | Description                                              |
|----------|----------------------------------------------------------|
| `N`      | Number of trucks (`N < 10000`)                           |
| `O`      | Number of cars (`O < 10000`)                             |
| `K`      | Ferry capacity in car slots (`3 <= K <= 100`)            |
| `TA`     | Max travel time for vehicles to port in µs (`0–10000`)  |
| `TP`     | Max travel time for ferry between ports in µs (`0–1000`)|

### Example

```bash
./proj2 4 4 10 10 10
```

---

##  Output

All process actions are logged in order to `proj2.out`:

```
1: P: started
2: O 1: started
3: N 1: started
4: P: arrived to 0
5: O 1: arrived to 0
6: O 1: boarding
7: P: leaving 0
...
44: P: finish
```

Each line is prefixed with a global action counter `A`, ensuring a consistent ordering across all concurrent processes.

---

##  Architecture

**Processes spawned:** `2 + N + O` (main, ferry, N trucks, O cars)

**Ferry process (`P`)**
- Travels between ports 0 and 1
- Unloads all vehicles, then loads waiting ones up to capacity
- Alternates truck/car loading; fills remaining slots with cars if no truck fits
- Exits to dock once all vehicles are transported

**Truck process (`N idN`)**
- Travels to assigned port, waits for ferry, boards, crosses, deboards

**Car process (`O idO`)**
- Same behavior as truck, but occupies 1 slot instead of 3

---

##  Implementation Details

- **Shared memory** — global action counter + synchronization variables
- **POSIX semaphores** — all process coordination (no active waiting)
- **`fork()`** — process creation only; no threads
- **`usleep()`** — simulates travel time with random delays
- Proper cleanup of all semaphores and shared memory on exit or error

---

##  Build

```bash
make        # builds proj2
make clean  # removes binaries
```

Compiled with: `-std=gnu99 -Wall -Wextra -Werror -pedantic`

---

##  Testing

```bash
# Run with Python test script
python3 tests/test.py
```

Tests verify output format correctness, action ordering, and that all processes terminate cleanly.

---

##  Grade

**15.0 / 15.0** 

---

##  License

MIT License. See [LICENSE](LICENSE) for details.
