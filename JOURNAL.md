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






