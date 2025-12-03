# Project Journal – AWS-Smart-Task-Automator

## Day 1 – Project Setup & Planning

**Date:** `23-11-2025`  
**Focus:** Project idea, repo setup, initial documentation.

### ✅ What I did
- Finalized the project idea: a serverless daily task automation system on AWS.
- Created a public GitHub repository for the project.
- Wrote the initial README with project goals and high-level architecture.
- Set the primary AWS region to `ap-south-1` (Mumbai).

### 🤔 What I learned / reinforced
- The importance of **clear goals** before writing any code.
- How documenting from Day 1 makes the project more attractive for recruiters.
- Why serverless + event-driven design fits well for automation workflows.

### 🧩 Next planned step
- Set up the basic AWS environment (IAM roles, region, and initial Lambda skeleton).

---

_More days will be added as I progress._

## Day 2 – Securing My AWS Environment

**Date:** `23-11-2025`  
**Focus:** IAM setup, avoiding root usage, securing the account.

### ✅ What I did
- Created an `AdminGroup` IAM user group with `AdministratorAccess`.
- Created an IAM user for daily use instead of logging in as the root account.
- Enabled MFA (Multi-Factor Authentication) on the root account.
- Switched to using the IAM user in the `ap-south-1` (Mumbai) region for all future work.

### 🤔 What I learned / reinforced
- Root accounts should only be used for billing and critical account-level operations.
- IAM groups make it easier to manage permissions instead of attaching policies to each user.
- Enabling MFA is a simple but powerful security improvement and is considered a must-have by companies.

### 🧩 Next planned step
- Create the first Lambda function and set up the basic project skeleton on AWS.

## Day 3 – Created My First Lambda Function

**Date:** 23-11-2025  
**Focus:** Creating the initial Lambda function for the project.

### ✅ What I did
- Created my first AWS Lambda function named `taskAutomationHandler`.
- Chose Python 3.12 as the runtime.
- Deployed a simple function that returns a JSON response.
- Successfully tested the function in the AWS console.

### 🤔 What I learned / reinforced
- How Lambda initializes permissions and execution roles.
- The basics of deploying and testing serverless functions.
- Importance of starting small before integrating services like EventBridge or DynamoDB.

### 🧩 Next planned step
- Create the DynamoDB table for storing tasks.
- Connect Lambda with DynamoDB in the next step.

---

# **Day 4 – Setting Up DynamoDB TaskTable**

Today I created my DynamoDB table, which will serve as the primary data source for the automation system.

### 🔹 **Table Name**

```
TaskTable
```

### 🔹 **Primary Keys**

* **Partition key:** `userId` (String)
* **Sort key:** `taskId` (String)

### 🔹 **Why this design?**

* It allows multiple tasks per user
* Querying becomes simple and fast using `KeyConditionExpression`
* Perfect for running scheduled reminders based on each user

### 🔹 **Test Items Added**

I manually inserted two sample items:

```json
{
  "userId": "pingjahangir",
  "taskId": "task1",
  "title": "Complete project overview",
  "status": "pending"
}
```

```json
{
  "userId": "pingjahangir",
  "taskId": "task2",
  "title": "Prepare AWS documentation",
  "status": "pending"
}
```

This confirmed table creation + partition/sort key configuration.

---

# **Day 5 – Creating IAM Role & Attaching Proper Permissions**

To allow Lambda to access DynamoDB and SNS, I created a dedicated execution role:

### 🔹 **Role Name**

```
taskAutomationHandler-role-7vqg6b4y
```

### 🔹 **Attached Policies**

1. **AWSLambdaBasicExecutionRole**
   Grants CloudWatch Logs permissions.

2. **TaskTableReadAccess** (Custom Inline Policy)

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:Query",
           "dynamodb:GetItem"
         ],
         "Resource": "arn:aws:dynamodb:ap-south-1:xxxxxxxxxxxx:table/TaskTable"
       }
     ]
   }
   ```

3. **TaskNotificationsPublishAccess** (Custom Inline Policy)
   Added later when SNS was introduced:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "sns:Publish",
         "Resource": "arn:aws:sns:ap-south-1:662792375823:TaskNotificationsTopic"
       }
     ]
   }
   ```

This role now allows:
✔ DynamoDB reads
✔ SNS publishes
✔ Logging to CloudWatch

---

# **Day 6 – Improving Lambda Function (DynamoDB Query)**

I built a Python Lambda function that retrieves tasks from DynamoDB for a specific user.

### 🔹 **Core Logic**

```python
response = table.query(
    KeyConditionExpression=Key("userId").eq("pingjahangir")
)
tasks = response.get("Items", [])
```

### 🔹 **Success**

* Lambda successfully connected to DynamoDB
* Execution logs confirmed data retrieval
* CloudWatch Logs displayed correct JSON responses

This validated the Lambda + DynamoDB pipeline.

---

# **Day 7 – Creating EventBridge Scheduler**

To automate task fetches, I created a scheduled rule in EventBridge.

### 🔹 **Schedule Pattern**

```
rate(12 hours)
```

Later used shorter intervals for debugging.

### 🔹 **Target**

* **Lambda Function:** `taskAutomationHandler`

### 🔹 **Outcome**

EventBridge successfully invoked the Lambda at scheduled intervals — visible in CloudWatch Invocation metrics.

This completed the **automation trigger**.

---

# **Day 8 – Setting Up SNS Notifications**

To send reminders via email:

### 🔹 **SNS Topic Created**

```
TaskNotificationsTopic
arn:aws:sns:ap-south-1:662792375823:TaskNotificationsTopic
```

### 🔹 **Subscription**

* Protocol: **Email**
* Subscriber: **my email**
* Status: **Confirmed** (after checking spam folder)

### 🔹 **IAM Update**

Attached a new inline policy to Lambda’s execution role to allow:

```json
{
  "Effect": "Allow",
  "Action": "sns:Publish",
  "Resource": "arn:aws:sns:ap-south-1:662792375823:TaskNotificationsTopic"
}
```

---

# **Day 9 – Integrating Lambda With SNS (Final End-to-End Automation)**

Today I combined everything:

### ✔ Fetch tasks from DynamoDB

### ✔ Check if tasks need notification

### ✔ Publish reminder email using SNS

### ✔ Log every action in CloudWatch

### 🔹 **Final Lambda Code Trigger**

Printed detailed debug logs:

```python
print("SNS publish response:", response)
```

### 🔹 **CloudWatch Confirmation**

I used:

```
SNS publish response
```

filter to find publish logs.

Every run showed:

```
SNS publish response: {'MessageId': 'xxxx-xxxx-xxxx'}
```

### 🔹 **Final Step: Received Email Notification**

✔ The email arrived in mailbox
✔ Notification contained task details
✔ End-to-end workflow confirmed working

This marked the **completion** of the core AWS automation system.

---

# **Project Status: Core System Completed**

I now have a fully working automated task reminder system using:

### ✅ DynamoDB

### ✅ Lambda

### ✅ IAM

### ✅ EventBridge

### ✅ SNS

### ✅ CloudWatch

This is a complete cloud automation workflow built entirely using serverless services.

---

# 📘 journal.md (Day 4 – DynamoDB + Lambda + SNS Integration & Debugging)

## **Day 4 – Advanced Automation Logic + IAM Debugging + SNS Notifications**

Today was the biggest progress day of the project so far.
I built and debugged a **complete cloud automation pipeline** involving:

* **AWS Lambda**
* **Amazon DynamoDB**
* **Amazon SNS**
* **IAM Roles & Policies**
* **CloudWatch Logs**
* **Timezone handling (IST)**
* **Priority-based sorting**
* **Due-date filtering**

This took time, multiple errors, troubleshooting, and real-world debugging — but by the end, the entire workflow was working end-to-end.

---

# ✅ **1. Added New Task (task-003) in DynamoDB**

I inserted a new item into DynamoDB:

| Attribute  | Value                       |
| ---------- | --------------------------- |
| `userId`   | `pingjahangir`              |
| `taskId`   | `task-003`                  |
| `Title`    | *Finish the project*        |
| `Status`   | `PENDING`                   |
| `Priority` | `1`                         |
| `DueDate`  | *today's date (YYYY-MM-DD)* |

All tasks now follow a uniform structure with:

* **Title**
* **Status**
* **Priority**
* **DueDate**

---

# ✅ **2. Implemented New Lambda Logic (Due Dates + Priority)**

I wrote a full Lambda function that:

* Retrieves tasks for the user
* Converts current time to **Indian Standard Time (IST)**
* Filters only tasks that are **PENDING**
* Filters tasks that are **due today**
* Sorts tasks by **priority**
* Sends a formatted notification email via SNS
* Logs detailed debug information into CloudWatch

### **📌 Final Lambda Code (Sensitive Values Hidden)**

```python
import json
import os
import boto3
from boto3.dynamodb.conditions import Key
from datetime import datetime, timedelta, timezone

# DynamoDB + SNS Clients
dynamodb = boto3.resource("dynamodb")
table = dynamodb.Table("TaskTable")
sns = boto3.client("sns")

# Environment variable fallback (masked)
SNS_TOPIC_ARN = os.environ.get(
    "SNS_TOPIC_ARN",
    "arn:aws:sns:ap-south-1:****:TaskNotificationsTopic"
)

def get_today_ist_iso() -> str:
    """Return today's date in IST as YYYY-MM-DD."""
    ist_offset = timedelta(hours=5, minutes=30)
    ist_tz = timezone(ist_offset)
    return datetime.now(ist_tz).date().isoformat()

def lambda_handler(event, context):
    user_id = "pingjahangir"  # Hardcoded for prototype

    try:
        # 1) Fetch tasks
        resp = table.query(
            KeyConditionExpression=Key("userId").eq(user_id)
        )
        tasks = resp.get("Items", [])
        print(f"Found {len(tasks)} total task(s) for user {user_id}")

        # 2) Filter due-today pending tasks
        today_ist = get_today_ist_iso()
        print(f"Today in IST: {today_ist}")

        pending_due_today = []
        for t in tasks:
            status = (t.get("status") or t.get("Status") or "").upper()
            due = t.get("dueDate") or t.get("DueDate") or ""
            if status == "PENDING" and due == today_ist:
                pending_due_today.append(t)

        print(f"Tasks pending & due today (IST): {len(pending_due_today)}")

        # 3) If tasks exist → sort by priority and notify
        if pending_due_today and SNS_TOPIC_ARN:

            def pri_val(x):
                try:
                    return int(x.get("priority") or x.get("Priority") or 999)
                except:
                    return 999

            pending_due_today.sort(key=pri_val)

            # Email formatting
            lines = []
            for t in pending_due_today:
                title = t.get("title") or t.get("Title") or "(no title)"
                due = t.get("dueDate") or t.get("DueDate") or "N/A"
                status = (t.get("status") or t.get("Status") or "UNKNOWN")
                task_id = t.get("taskId", "N/A")
                priority = t.get("priority") or t.get("Priority") or "N/A"

                lines.append(
                    f"- [{priority}] {title} (ID: {task_id}) | due: {due} | status: {status}"
                )

            message = (
                f"Hello {user_id},\n\n"
                f"You have {len(pending_due_today)} pending task(s) due today:\n\n"
                + "\n".join(lines) +
                "\n\n— AWS Smart Task Automator"
            )

            print("Final notification message:", message)

            publish_resp = sns.publish(
                TopicArn=SNS_TOPIC_ARN,
                Subject="Tasks due today — AWS Smart Task Automator",
                Message=message
            )
            print("SNS publish response:", publish_resp)

        else:
            print("No pending tasks due today OR SNS Topic missing.")

        return {
            "statusCode": 200,
            "body": json.dumps({
                "message": "Task check completed.",
                "totalTasks": len(tasks),
                "pendingToday": len(pending_due_today)
            })
        }

    except Exception as ex:
        print("Error in lambda:", str(ex))
        return {"statusCode": 500, "body": json.dumps({"error": str(ex)})}
```

---

# ❗ 3. Encountered Major Error: AccessDeniedException

When testing the Lambda, I saw this in CloudWatch:

```
An error occurred (AccessDeniedException) when calling the Query operation
```

This meant the Lambda **did not have permission** to query DynamoDB.

This is a **real-world production error** that cloud engineers face often.

---

# 🔧 **4. Debugging the IAM Issue (Professional-Level Fix)**

### Problems Identified:

* Lambda was using a role I initially **did not update**
* Inline policy was created but **not attached**
* DynamoDB read access was missing

### Solutions:

✔ Verified the **exact Execution Role** used by Lambda
✔ Added **AmazonDynamoDBReadOnlyAccess**
✔ Recreated the **TaskTableReadAccess** inline policy
✔ Ensured SNS publish permissions
✔ Attached everything to the correct role

### Inline policy used (masked):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:Query",
        "dynamodb:GetItem",
        "dynamodb:Scan"
      ],
      "Resource": "arn:aws:dynamodb:ap-south-1:****:table/TaskTable"
    }
  ]
}
```

---

# 📊 5. Validation via CloudWatch Logs

After updating IAM:

* No more `AccessDenied` errors
* Logs showed correct task counts
* Logs printed the formatted email body
* SNS responded with:

```
SNS publish response: { 'MessageId': 'xxxx', ... }
```

---

# 📬 6. Final Result — Email Received Successfully

I successfully received the **task notification email** through SNS including:

* Task titles
* Priorities
* Due dates
* Status
* Sorted order

This confirms the *entire serverless system* is functioning.

---

# 🎉 **Summary of What I Achieved Today**

* Added third task in DynamoDB
* Built advanced Lambda logic
* Added due-date + priority filtering
* Implemented IST timezone
* Added SNS email notifications
* Performed real AWS debugging
* Fixed IAM role mismatches
* Fixed DynamoDB access errors
* Verified with CloudWatch log streams
* Successfully received email notifications

This is now a **production-grade serverless automation pipeline**.

---

# 🚀 Next Steps (Upcoming)

* REST API using API Gateway
* Frontend UI in S3
* Overdue task detection
* Multi-user support
* CloudWatch Alarms
* Rich HTML email formatting

---
