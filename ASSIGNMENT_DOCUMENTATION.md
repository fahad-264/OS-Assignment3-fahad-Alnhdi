# Assignment 3 - Complete Documentation

**Student Name**: fahad alnahdi  
**Student ID**: [445050264]  
**Date Submitted**: [2 May]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `445050264_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [April 30, 8:00 PM]
**What I implemented**: 
I created the basic scheduler simulation with processes and threads
**Challenges encountered**: 
I had difficulty understanding how threads run in parallel.
**How I solved it**: 
I tested simple thread examples and read about Runnable.
**Testing approach**: 
Ran the program and checked if processes execute
**Time spent**: 
2 hours
---

### Entry 2 - [April 30, 10:00 PM]
**What I implemented**: 
I added Round Robin scheduling with time quantum.
**Challenges encountered**: 
Processes were not switching correctly
**How I solved it**: 
I fixed the queue logic and re-added processes
**Testing approach**: 
Checked ready queue order after each execution
**Time spent**: 
2.5 hours
---

### Entry 3 - [May 1, 7:00 PM]
**What I implemented**: 
I added shared counters (waiting time, context switch)
**Challenges encountered**: 
Values were sometimes incorrect
**How I solved it**: 
I realized it is race condition problem
**Testing approach**: 
Printed values multiple times to compare
**Time spent**: 
3 hours

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

One race condition occurs in contextSwitchCount because multiple threads increment it at the same time. This can cause incorrect values because updates may overwrite each other. Another race condition occurs in totalWaitingTime where threads add values concurrently, which can lead to wrong total. Without synchronization, threads may read and write at the same time causing inconsistency. For example, two threads may read the same value and both update it incorrectly. This leads to incorrect statistics in the program.

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

ReentrantLock is used to protect shared variables like counters so only one thread can modify them at a time. Semaphore is used to control access to a resource like CPU. In my code, I used ReentrantLock for contextSwitchCount, waitingTime, and log. I used Semaphore with 1 permit to simulate one CPU. Locks protect data, while Semaphore controls access to resources. That is why I used both.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock happens when threads wait for each other forever. One prevention technique is using try-finally to always release locks. Another technique is avoiding nested locks or keeping lock order fixed. In my code, I used try-finally to ensure locks are always released. I also kept locks simple and independent. This prevents threads from blocking each other.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used separate locks for each counter which is fine-grained locking. I did this because each counter is independent and does not need to block others. This improves concurrency because multiple threads can update different counters at the same time. If I used one lock for all counters, it would reduce performance because threads will wait unnecessarily. The trade-off is that fine-grained locking is more complex, while coarse-grained is simpler but slower. Since counters are independent, fine-grained locking is better for concurrency.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, totalWaitingTime, completedProcessCount
**Why they need protection**: 
hey are shared between threads and updated concurrently
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
contextSwitchLock.lock();
try {
    contextSwitchCount++;
} finally {
    contextSwitchLock.unlock();
}
```

**Justification**:
nsures only one thread updates the variable at a time
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (shared list)
**Why it needs protection**: 
Multiple threads add messages at same time
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}
```

**Justification**: 
Prevents ConcurrentModificationException
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
Control access to CPU
**Number of permits and why**: 
1 permit → simulate single CPU
**Where implemented**: 
Inside run() method
**Code snippet**:
```java
cpuSemaphore.acquire();
try {
    // process execution
} finally {
    cpuSemaphore.release();
}
```

**Effect on program behavior**: 
Only one process runs at a time
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
Results are consistent in all runs
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Without it, shared variables like counters may have incorrect values
**Conclusion**: 
Synchronization ensures correct results
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
Run program multiple times
**Results**: 
No ConcurrentModificationException occurred
**What this proves**: 
Log is safely synchronized
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Correct total waiting time and completed processes
**Actual values**: 
Values matched expectations
**Analysis**: 
Synchronization fixed incorrect values
---

### Test 4: Different Scenarios
Scenario tested:
Different time quantum values

Purpose:
Check scheduling behavior

Results:
Program worked correctly

What I learned:
Time quantum affects performance
## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that synchronization is important when multiple threads access shared data. Without it, race conditions happen and results become incorrect. I understood how locks ensure only one thread accesses a resource. I also learned how semaphores control access to resources like CPU. Using try-finally is important to avoid errors. Synchronization improves correctness but may reduce performance. Designing locks carefully is important.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Bank system transactions
**Example 2**: 
Operating system process scheduling
---

### How I would explain synchronization to others:

Synchronization is like a key for a room. Only one person can enter at a time. If many enter together, things get messy. So we use locks to control access.

---

## Part 6: GitHub Repository Information

**Repository URL**: 
(put your link)
**Number of commits**: 
4
**Commit messages**: 
1. Initial scheduler implementation
2. Added Round Robin logic
3. Added synchronization
4. Final fixes

---

## Summary

**Total time spent on assignment**: 
11.5 hours
**Key takeaways**: 
1. Importance of synchronization
2. Understanding threads
3. Preventing race conditions

**Most challenging aspect**: 
Understanding race condition
**What I'm most proud of**: 
Making the program thread-safe
---

**End of Documentation**
