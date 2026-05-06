# Assignment 3 - Complete Documentation

**Student Name**: [Your Full Name]  
**Student ID**: [Your ID]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [2026/5/5, 11:12 PM]
**What I implemented**: Forked the repo, changed student ID to 445052075. 

**Challenges encountered**: No challenges, pretty straightforward.

**How I solved it**: Followed the VS Code cloning steps.

**Testing approach**: Ran the original code to see the race conditions – numbers were different every time.

**Time spent**: 20 min.

---

### Entry 2 - [2026/5/5, 11:34 PM]
**What I implemented**: Added ReentrantLock to protect shared variables (contextSwitchCount, completedProcessCount, totalWaitingTime) and wrapped them using lock/unlock with try-finally.

**Challenges encountered**: I was a bit confused where exactly to place the locks and how many locks I should use (one lock or multiple locks).

**How I solved it**: I followed the hint in the code and used separate locks for each variable to allow better concurrency. Also made sure to use try-finally to avoid deadlock.

**Testing approach**: Ran the program multiple times and checked if the counters are now consistent.

**Time spent**: 45 min.

---

### Entry 3 - [2026/5/6, 12:25 AM]
**What I implemented**: Added ReentrantLock for executionLog (ArrayList) to prevent concurrent modification issues.

**Challenges encountered**: I wasn’t sure if ArrayList really needs synchronization since it worked before sometimes.

**How I solved it**: After reading the assignment, I understood that multiple threads can access it at the same time, so I added a lock to protect add operations.

**Testing approach**: Ran the program several times and ensured no errors or inconsistent log sizes appear.

**Time spent**: 30 min.

---

### Entry 4 - [2026/5/6, 12:55 AM]
**What I implemented**: Added a binary Semaphore for CPU access control by defining cpuSemaphore = new Semaphore(1) in the shared resources.

**Challenges encountered**: I was initially unsure what value to use for the semaphore and how it controls process execution.

**How I solved it**: I used a binary semaphore (1 permit) as required in the assignment to ensure only one process can access the CPU at a time.

**Testing approach**: Ran the program multiple times and confirmed that only one process executes at a time and results are consistent.

**Time spent**: 45 min.

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[First race condition – contextSwitchCount++ (and other counters)
The shared resources here are the integer counters.
The issue is that the ++ operation is not atomic, meaning multiple threads can read the same value at the same time, increment it, and then write it back. This leads to a lost update problem.
As a result, the final value of the counter may be lower than the actual number of increments that occurred.

Second race condition – executionLog.add(message)
The shared resource is the ArrayList<String>.
The problem is that ArrayList is not thread-safe, so if multiple threads call add() simultaneously, it can cause data corruption, missing log entries, or even ConcurrentModificationException.
This means some log messages might not be recorded correctly or the program may behave unpredictably.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[A ReentrantLock is a mutual exclusion lock that allows only one thread to access a critical section at a time. I used it to protect the counters and the execution log because these shared resources require exclusive access to avoid inconsistency.

A Semaphore, on the other hand, controls access using permits. It can allow multiple threads depending on the number of permits available. In my case, I used a Semaphore(1) to restrict execution so that only one process runs at a time, similar to how a single-core CPU works.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[A deadlock happens when threads are stuck waiting for each other’s locked resources indefinitely, causing the system to freeze.
To prevent this, I applied the following techniques:

1- Lock ordering: I avoided acquiring multiple locks at the same time, which prevents circular waiting between threads.

2-try-finally blocks: Every lock() or acquire() is always followed by a finally block to ensure the lock is released even if an exception occurs.

In addition, the semaphore is acquired at the start of the critical section and released immediately after, ensuring there is no nested locking or risk of deadlock.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I chose a fine-grained locking approach by using three separate ReentrantLocks, each one protecting a different counter (contextSwitchCount, completedProcessCount, and waitingTimeCount).

Reason: These counters are completely independent, so updating one does not interfere with the others. If a single coarse-grained lock was used, all threads would be forced to wait even when accessing different counters, which would reduce performance unnecessarily.
With fine-grained locking, different threads can update different counters at the same time, improving parallelism and overall efficiency.

Trade-off: It adds a bit more complexity in implementation and requires careful handling, but since the resources are independent, the performance benefit is more important.

Overall, fine-grained locking provides better concurrency because it avoids unnecessary blocking between unrelated operations.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: 
These variables are shared between multiple threads, and operations like incrementing or adding are not atomic. Without synchronization, concurrent updates can overwrite each other and lead to incorrect final values.

**Synchronization mechanism used**: Fine-grained locking using separate ReentrantLock objects for each counter.

**Code snippet**:
```java
public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}
```

**Justification**: Each counter is independent, so using separate locks allows multiple threads to update different counters at the same time, improving concurrency while still ensuring correctness.

---

### Critical Section #2: Execution Log

**What resource**: executionLog (ArrayList of strings)

**Why it needs protection**: ArrayList is not thread-safe, so concurrent add() operations may lead to data corruption, lost entries, or runtime exceptions.

**Synchronization mechanism used**: ReentrantLock (logLock)

**Code snippet**:
```java
public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}
```

**Justification**: Using a lock ensures only one thread can modify the log at a time, preserving data consistency and preventing race conditions.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: To simulate a single-core CPU environment where only one process can execute at a time.

**Number of permits and why**: 1 permit (binary semaphore), because only one process should access the CPU at any given moment.

**Where implemented**: Inside Process.run() and Process.runToCompletion() methods.

**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try {
    // execution section
} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 
It ensures mutual exclusion at the CPU level, meaning even though multiple threads exist, only one process executes at a time, accurately simulating a uniprocessor scheduling system.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
java SchedulerSimulationSync
# repeated 5 times
```

**Results**: 
(All runs produced identical results:

Same number of context switches each time
Same completed process count (matching number of processes created)
Same total and average waiting time
No variation in final statistics)

**Why synchronization is necessary**: 
(Without synchronization, race conditions could occur in shared resources such as counters and logs. This would lead to inconsistent or incorrect final values depending on thread timing. Locks and semaphores ensure deterministic behavior by preventing simultaneous unsafe updates.)

**Conclusion**: The program produces stable and repeatable results, which confirms that shared resources are properly synchronized.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: Ran the program multiple times and observed execution under multithreading conditions.

**Results**: No exceptions occurred during execution.

**What this proves**: The logLock successfully protects the ArrayList, ensuring that only one thread modifies it at a time, which prevents concurrent modification issues.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Completed processes = total number of created processes
Context switches = number of CPU allocations
Waiting time = sum of all process waiting times

**Actual values**: All displayed values matched the expected results shown in the final output.

**Analysis**: Synchronization does not affect the logic of the program; it only ensures that shared data is updated correctly without interference from concurrent threads.

---

### Test 4: Different Scenarios
**Scenario tested**: [ I modified the CPU scheduling conditions by changing the time quantum value (e.g., testing with a smaller quantum and then a larger one) to observe how the system behaves under different scheduling intensities.]

**Purpose**: To analyze how the scheduler reacts when the CPU time slice changes, and to check whether synchronization still guarantees correct execution regardless of scheduling frequency and context switching rate.

**Results**: 
With a smaller time quantum, context switches increased significantly
With a larger time quantum, processes executed in longer continuous segments with fewer switches
In both cases, final statistics (counters, logs, and waiting time) remained correct and consistent
No race conditions or exceptions were observed

**What I learned**: Changing the time quantum affects performance and scheduling behavior but does not affect correctness due to proper synchronization. This proves that the system is stable under different scheduling configurations.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[Through this assignment, I learned that synchronization is essential in multi-threaded systems to prevent inconsistent and unpredictable behavior. Even simple operations like incrementing a variable can produce incorrect results when multiple threads execute at the same time. I also realized that race conditions are not always obvious because the program may work correctly most of the time before failing under specific timing conditions. Using locks ensures that only one thread can access critical sections at a time, which guarantees correctness. I understood that fine-grained locking improves performance by allowing independent resources to be accessed concurrently. However, it also requires careful design to avoid complexity and mistakes. Semaphores provide another level of control by limiting how many threads can access a resource instead of blocking all of them completely.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Airline booking systems – When multiple users try to book the same seat at the same time, synchronization ensures that only one booking is confirmed, preventing double booking.

**Example 2**: Online multiplayer games – Player actions such as updating scores, positions, or shared game state must be synchronized to avoid inconsistencies between different players’ views of the game.

---

### How I would explain synchronization to others:

[I would explain synchronization using an ATM example. If two people try to use the same bank account at the same time, both might read the same balance before it updates and withdraw money, which can cause incorrect results. Synchronization makes sure only one person can access and update the account at a time, so the balance stays correct.

In programming, it works the same way by allowing only one thread to use shared data at a time to avoid mistakes.]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
