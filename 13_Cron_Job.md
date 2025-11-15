## What is CronJob?
A CronJob is a Kubernetes object used to run Jobs on a schedule, similar to Linux cron.

It is used for recurring or time-based tasks.

Examples:

- Run a backup every night
- Clean temp files every hour
- Send daily email reports
- Rotate logs weekly
- Trigger batch jobs periodically

CronJob = scheduled Job


## Key Characteristics

**1. Scheduled execution (cron format)**

CronJob uses standard cron syntax:
```
* * * * *
min hour day month weekday
```
Example
```
0 2 * * *   → Run every day at 2 AM
```

**2. Creates a Job at schedule time**
A CronJob does not run tasks directly.
Instead, it creates a Job object at each scheduled time, and the Job runs the Pod(s).

**3. Handles missed schedules**
If the cluster was down or CronJob controller was not working, you can configure:
- Run immediately after recovery
- Skip missed executions
- Using
```
startingDeadlineSeconds
```

**4. Controls concurrency (important)**

The concurrencyPolicy controls how CronJob handles overlapping executions:

Options:
1. Allow – Run new Job even if previous one still running (default)
2. Forbid – Do NOT start new Job if previous hasn’t finished
3. Replace – Cancel running Job and start a new one

**5. Runs in UTC by default**

By default, schedules are interpreted as UTC.
Kubernetes does not support time zones directly in CronJob specs.

**6. Backoff & retry policies**

Jobs created by CronJob inherit retry logic:
- backoffLimit
- parallelism
- completions


## Use Cases

- Database backups
- Cleanup old logs / temporary files
- Sending periodic emails or alerts
- Running analytics / ETL batch workflows
- Refreshing caches regularly
- Nightly integration tests
- Rotating tokens/secrets

## YAML Definition
```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nightly-backup
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: busybox
            command: ["sh", "-c", "echo Running backup..."]
          restartPolicy: Never
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1
  concurrencyPolicy: Forbid
```
