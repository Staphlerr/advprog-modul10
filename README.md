# Tutorial 10 Asynchronous Programming

Name: Belva Ghani Abhinaya

Class: Advance Programming B

Student Number: 2306203526

<details open>
<summary><b>Reflection on Module 9</b></summary>
<br>

### Reflection on Module 10

#### 1. Experiment 1.2: Understanding how it works
**Modification:**  

```rust
println!("Belva's Komputer: hey hey");
```
added immediately after spawner.spawn(…) in main.rs.

**Result:**
![image](https://github.com/user-attachments/assets/72e57884-d96d-460b-9315-6e021cf7b854)
(2s delay between howdy and done)

**Explanation:**

Calling spawn merely schedules the async task, it isn’t driven to run until we call executor.run(). Therefore our extra println! in the main thread always executes before the executor starts polling the spawned future.

#### 2. Experiment 1.3: Multiple Spawn and removing `drop(spawner)`

**Modification:**  
— Spawned three async tasks in a row (printing “howdy!”, waiting 2 s, then “done!” in each)  
— Commented out the `drop(spawner);` line before calling `executor.run()`.

**Results:**
- **Without** `drop(spawner)`:
  ![image](https://github.com/user-attachments/assets/adc54b89-b4df-4242-a4f1-40017c65bf94)
  The program then hangs indefinitely—no prompt returns.
- **With** `drop(spawner)` re-enabled:
  ![image](https://github.com/user-attachments/assets/0de409cb-82a3-4107-948b-b67c7aeb38d3)
  Same prints, but once all three tasks complete the executor exits and the program terminates normally.

**Explanation:**  
The executor’s run loop blocks on receiving new tasks until the sending side (the `Spawner`) is closed. If we never drop the spawner, the channel stays open, so after our three tasks finish there’s no “end-of-stream” signal and `executor.run()` blocks forever waiting for more work. Dropping the spawner closes the channel, lets the executor detect “no more tasks,” and allows it to return, ending the program. 

On `executor.run()`, the executor pulls each spawned task from its queue in **FIFO** order. It polls each future up to the first `await` (where we call `println!("howdy")`), so “howdy”, “howdy2”, “howdy3” always print in the same order the tasks were spawned. 

Each `TimerFuture` then spawns a background thread that sleeps for ~2 seconds before calling the waker and re-queuing its task. Even though all three timers request the same delay, OS scheduling and timer-wake jitter make them fire at slightly different times. The executor then polls those woken tasks in the order it receives them, so the “done” messages interleave unpredictably.

</details>