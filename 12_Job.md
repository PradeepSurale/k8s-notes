## What is JOB?

A **Job** is a Kubernetes workload object used to run tasks that **must complete successfully.**
Unlike Deployments or StatefulSets (which run continuously), a Job runs **once**  , or **a fixed number of times**, until the task is completed.

Examples:
- Data processing task
- Backup operation
- Database migration
- File conversion
- Sending batch emails

A Job ensures that the task **runs to completion** even if Pod(s) fail.

## Key Characteristics

**1. Ensures task completion**

A Job keeps creating Pods until the task succeeds:

  - If a Pod fails → Job retries
  - If a Pod completes → Job marks it done

It guarantees “at least once” execution.

**2. Creates one or multiple Pods**

Depending on configuration, a Job can run:

- 1 Pod once (default)
- Multiple pods in parallel (parallel processing)
- Pods sequentially (completions setting)

**3. Retries on failure**

If a Pod crashes:
- Job creates another Pod
- Job respects the backoffLimit (max retries)

**4. Supports parallelism**

Jobs allow parallel execution using:
  - parallelism (how many Pods run at the same time)
  - completions (how many total successful Pods needed)
```
parallelism: 5
completions: 20
```
  - Run 5 Pods at a time until 20 finish.

**5. Can run to completion and exit**
  Once complete:
  - The Job is marked Completed
  - Pods remain for logs (unless TTL is set)

## Use cases 
  - Batch processing
  - ETL jobs
