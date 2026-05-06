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

**Which variables**: 

**Why they need protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

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
