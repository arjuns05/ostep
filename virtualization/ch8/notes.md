The multi-level feedback queue
    - goal is to optimize turnaround time
    - also to make OS responsive to new processes
The solution is essentially learning more about processes as they run 
MLFQ will learn from the past to predict the future

To implement this, we have a number of distinct queues, each given a priority level 
At any given time, a job that is ready to run is on a single queue, MLFQ prioritizes which job should run at a given time

More than one job may be on a queue, having the same priority, in this case you just round-robin on those elements

At a point:
- if priority (A) > priotity(B), processes A will run, B does not
- if priority(A) = priority(B), process A and B run in round robin 

MLFQ will vary priority based on behavior
If a job constantly leaves CPU (to do I/O), it will be high priority, as this would be interactive (likely)
If it uses CPU intensively for a long time, the priority is reduced, so this is how it learns about processes

Changing priorities:
- Attempt 1:
    - our workload is going to be short-running CPU jobs with I/O or long-running intensive jobs
    - Allotment: amount of atime a job can spend at a given priority level before the scheduler reduces priority
    - When a job enters, it is at highest priotity, then if it uses allotment, it is reduced in priority, if a job gives up CPU before allotment it stays at the same priority level (reset allotment)
    - Let's say A is running (CPU intensive, low priority eventually), and then B comes in at t = 100ms, 
        - Then B is at highest priority, so it runs and then B is done before reaching bottom of the queue
        - Then A will run after at lowest priority
        - This approach will assume that a new job is very short and should go first and then correct that assumption as we continue
    - Problems:
        - too many interactive jobs, they will combine to consume all CPU time, (starvation)
        - A smart user could game the schduler, so we need to make sure the scheduler is not attackable
            - you can game it by doing this: before allotment over, do an I/O operation, and give up CPU so you stay in same queue
        - Also program can change behavior, after running a lot of CPU intensive work and beocming low priority it can have a lot of interactive work which means it would be very slowly done as it is low priority
    Approach 2: 
        - Priority Boost:
            - Periodically boost priority of all the jobs in the system, after some time move all the jobs to the topmost queue
            - processes are now guaranteed to not starve, as they will eventually do round robin, also if a CPU-bound job becomes interactive it will be treated properly with the boost

        - What should the boost time be, 
            - If too high, long-running jobs could tarve, 
            - too low, then interactive jobs won't get CPU time
    Attempt 3:
        - How to prevent gaming of scheduler, as currently allotment resetting can be gamed 
        - perform better accounting of CPU time at each level of MFLQ, so instead of forgetting allotment of CPU time when given I/O, scheduler keeps track and process is demoted to the next level when allotment is over
        - New rule: Once a job uses its allotment at a given level (regardless of when it gives up CPU), its priority is reduced
Tuning the Multi-Level Feedback Queue:
    - How to parameterize the scheduler: 
        - how many queues should there be 
        - how big is the time slice per queue
        - how big is the allotment 
        - how often priority can be boosted
    - Generally, there is varying time-slice length across different queues, high-priority queues are given short time slices (interactive jobs, so need quick alternation between processes in the round robin fashion), lower level jobs tend to be long-running so more time to run in between round robin switches

    - Solaris:
        - 60 queues, increasing time slice lengths from 20ms to a few hundred miliseconds, priorities boosted every 1 second or so
    - FreeBSD scheduler:
        - uses a formula to calculate priority level, basing it on CPU used, usage decayed over time, roviding the priority boost in a different manner
    Some schedulers vary, can reserve highest priority for OS level work, so user jobs never have highest levels of priority
    - Systems allow user advice to set priorities, using CLU, (nice utility lets you increase or decrease priority of a job)
        - Generally OS can get some hints (can choose to use) from the user, examples are helping scheduler with nice, memory manager (madvise), file ystem (informed prefetching and caching)

    Summarized rules:
        1) If priority(A) > priority(B), run A, don't run B
        2) If priority(A) = priority(B), round robin between A,B
        3) when a job enters system it is at top of the queue
        4) Once a job uses up its time allotment at a given level (we don't forget about it when we relinquish for I/O), its priority is redyced
        5)After some time period S, move all the jobs to the topmost of the queue