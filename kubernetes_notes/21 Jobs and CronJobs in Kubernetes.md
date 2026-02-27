# ✅ Jobs and CronJobs in Kubernetes

When creating Jobs and CronJobs in Kubernetes, the container images you
use determine what tasks can be performed.

------------------------------------------------------------------------

## 1️⃣ Jobs: One-Time Task Execution

A **Job** creates one or more Pods to perform a specific task and runs
Pods until successful completion.\
Once the task is done, the Pod terminates.

**📌 Use Case:** Running a one-time task like a database migration or
backup.

``` yaml
apiVersion: batch/v1  # API version for batch jobs
kind: Job             # Declares a Job resource
metadata:
  name: hello-job     # Name of the Job
spec:
  template:           # Pod template that will be created by the Job
    spec:
      containers:
      - name: hello   # Name of the container
        image: busybox
        command: ["echo", "Hello from Job"]
      restartPolicy: Never
  backoffLimit: 4
```

### 📝 Explanation

-   Runs a container that echoes a message.
-   `restartPolicy: Never` means the pod won't restart on its own.
-   If a Job pod fails, Kubernetes creates a new Pod to retry until
    success or the backoff limit is reached.

### Important Job Fields

-   **backoffLimit:** Number of retries before marking failed.
-   **activeDeadlineSeconds:** Time limit (seconds) before the job
    fails.
-   **completions:** Total successful Pods needed.
-   **parallelism:** Number of Pods running at the same time.
-   **restartPolicy:** Must be `OnFailure` or `Never`.

### 🔧 Useful Job Commands

``` bash
kubectl apply -f job.yaml
kubectl get jobs
kubectl describe job <name>
kubectl delete job <name>
kubectl logs <pod-name>
```

------------------------------------------------------------------------

## 2️⃣ CronJobs: Scheduled Jobs

A **CronJob** creates Jobs on a time-based schedule (like Linux cron).\
Each scheduled run creates a new Job object.

**🔧 Use Case:**\
- Night backups\
- Hourly log cleanup\
- Weekly reports

``` yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cron
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ["echo", "Hello from CronJob"]
          restartPolicy: OnFailure
```

### 📝 Explanation

-   Runs every 1 minute and echoes a message.
-   `restartPolicy: OnFailure` retries only if the job fails.

------------------------------------------------------------------------

## ⏰ Cron Syntax Format

    * * * * *
    | | | | |
    | | | | └── Day of Week (0-6 or Sun-Sat)
    | | | └──── Month (1-12)
    | | └────── Day of Month (1-31)
    | └──────── Hour (0-23)
    └────────── Minute (0-59)

**Example:**\
`0 2 * * *` → Run at 2:00 AM every day

------------------------------------------------------------------------

## 🌍 CronJob with Timezone (Kubernetes 1.27+)

``` yaml
annotations:
  k8s.io/cronjob-timezone: "America/New_York"
```

------------------------------------------------------------------------

## 🔧 Useful CronJob Commands

``` bash
kubectl apply -f cronjob.yaml
kubectl get cronjobs
kubectl get jobs
kubectl delete cronjob <name>
```

------------------------------------------------------------------------

© Kubernetes Jobs & CronJobs Guide
