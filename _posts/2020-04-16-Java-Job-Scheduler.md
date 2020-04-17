---
layout: post
title:  "Learning Through Problem Solving: Java Task Scheduler"
description: understand Java Job Scheduler through solving an coding problem.
---

# Intro

In the actual project development, task scheduling is indispensable. Task scheduling can be applied in many scenarios. For example, some apps or game want to give members a growth daily at specific time. Some online shopping apps may want to send members some coupons weekly or monthly. Moreover, task scheduling becomes necessary in regular comparison or cleaning work. For instance, generating reports or emails regularly, or clean up data periodically.



# Problem

- Implement a job scheduler which takes in a function `f` and an integer `n`, and calls `f` after `n` milliseconds.



# [Timer](https://docs.oracle.com/javase/7/docs/api/java/util/Timer.html) (not recommend)

- A facility for threads to schedule tasks for future execution in a background thread. Tasks may be scheduled for one-time execution, or for repeated execution at regular intervals.

- Tasks that are automatically executed based on a given time point, a given time interval, and a given number of executions.

- 4 ways of using `schedule` method:

```java
// execution after the specified delay
public void schedule(TimerTask task, long delay)
// execute at the specified time
// If the time is in the past, the task is scheduled for immediate execution
public void schedule(TimerTask task, Date time)
// repeated fixed-delay execution, beginning at the specified time.
public void schedule(TimerTask task, Date firstTime, long period)
// repeated fixed-delay execution, beginning after the specified delay.
public void schedule(TimerTask task, long delay, long period)
```

- 2 ways of using `scheduleAtFixedRate` method

```java
public void scheduleAtFixedRate(TimerTask task, long delay, long period)
public void scheduleAtFixedRate(TimerTask task, Date firstTime, long period)
```

### Difference between `schedule` and `scheduleAtFixedRate`
For `schedule`, each execution time is one period interval from the beginning of the last task, while for `scheduleAtFixedRate`, is one period interval from the end of the last task.

- If `TimeTask` throws `RuntimeException`, then `Timer` will stop all tasks!

### Problem of Timer
Corresponding to each Timer object is a single background thread that is used to execute all of the timer's tasks, sequentially. If a timer task takes excessive time to complete, it "hogs" the timer's task execution thread. This can, in turn, delay the execution of subsequent tasks.



# [ScheduledExecutorService](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ScheduledExecutorService.html) (recommend)

- `ScheduledExecutorService` implements [`ScheduledThreadPoolExecutor`](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ScheduledThreadPoolExecutor.html), so the problem in using `Timer` will not happen. When we have strict requirements for the delay and circulation, we consider using `ScheduledExecutorService` instead of `Timer`.

- Each scheduled task will be executed by a thread in the thread pool, so the tasks can be executed concurrently and are not affected by each other.

## Usage Example (from Java SE 7 Official Docs):
Here is a class with a method that sets up a `ScheduledExecutorService` to beep every 10s for 1h:

```java
 class BeeperControl {
   private final ScheduledExecutorService scheduler =
     Executors.newScheduledThreadPool(1);

   public void beepForAnHour() {
     final Runnable beeper = new Runnable() {
       public void run() { System.out.println("beep"); }
     };
     final ScheduledFuture<?> beeperHandle =
       scheduler.scheduleAtFixedRate(beeper, 10, 10, SECONDS);
     scheduler.schedule(new Runnable() {
       public void run() { beeperHandle.cancel(true); }
     }, 60 * 60, SECONDS);
   }
 }
```
As seen in the example, we first create a scheduled thread pool that can schedule commands to run after a given delay, or to execute periodically. The `Executors.newScheduledThreadPool(1)` returns a `static ScheduledExecutorService`. Before scheduling tasks, we need to create an instance implementing `Runnable` interface, which is intended to be executed by thread. Implementing `Runnable`, we must define(Override) a method of no arguments called `run`. Finally, we call `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)` , to create and execute a periodic beep action that will beep after the initial delay time, and repeatedly beep with the period time.  

- There are other scheduling methods in `ScheduledExecutorService`:

```java
// one-shot action, start after delay time
schedule(Runnable command, long delay, TimeUnit unit)
// periodic action, the delay will be the time from terminating of one and starting of the next
scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)
```
Obviously, in the original problem, we should have a one-shot action starting after a specific delay time. Therefore, we pick the `schedule(Runnable command, long delay, TimeUnit unit)` method. Now the answer should be very clear.


# Code For Original Problem

```java
public class JobScheduler {
	
	private final ScheduledExecutorService scheduler;
	
	public JobScheduler() {
		this.scheduler = Executors.newSingleThreadScheduledExecutor();
	}
	
	// Job scheduling method, which takes function `f` and delay time `n`
	public void solution(Runnable command, int delay_ms) {
		// Creates and executes a one-shot action that becomes enabled after the given delay.
	     ScheduledFuture<?> jobHandle =
	       scheduler.schedule(command, delay_ms, TimeUnit.SECONDS);
	    // Initiates an orderly shutdown in which previously submitted tasks are executed, but no new tasks will be accepted.
	    scheduler.shutdown();
	}

	public static void main(String[] args) {
		// print current time
		System.out.println(new Date());
		// instantiate a Runnable and define the `run()` method
		// You can define your own `run()` method
		Runnable command = () -> System.out.println("executed at " + new Date());
		JobScheduler jobScheduler = new JobScheduler();
		jobScheduler.solution(command, 3);
	}

}
```


### Hope this article helps you learning new things. Happy coding today!  

Github: [AbbyXing/DailyCodingProblem](https://github.com/AbbyXing/DailyCodingProblem).  
Welcome watching and let's tackle one coding problem everyday!
