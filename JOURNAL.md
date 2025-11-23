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

**Date:** `<fill-today>`  
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

