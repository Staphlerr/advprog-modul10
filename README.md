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
(2s delay between howdy dan done)

**Explanation:**

Calling spawn merely schedules the async task, it isn’t driven to run until we call executor.run(). Therefore our extra println! in the main thread always executes before the executor starts polling the spawned future.

</details>