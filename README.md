# AWS-Smart-Task-Automator

This project is a **cloud-native, serverless task automation system** built on AWS.  
It sends daily reminders to users, lets them mark tasks as completed, and automatically moves incomplete tasks to the next day.

---

## 🎯 Project Goal

Build a production-style **serverless workflow** using:

- ✅ AWS Lambda  
- ✅ Amazon EventBridge (scheduler)  
- ✅ Amazon DynamoDB  
- ✅ Amazon SNS (notifications)  
- ✅ Amazon API Gateway  

This project is part of my learning journey in **Cloud Computing, DevOps, and Backend Automation**.

---

## 🧱 High-Level Architecture (v0.1)

- A **scheduler** (EventBridge) triggers a **Lambda** function every day.
- The Lambda:
  - Reads tasks from **DynamoDB**
  - Sends notifications via **SNS**
- Users can mark tasks as **completed** through an **API (API Gateway + Lambda)**.
- Another scheduled Lambda:
  - Moves any **incomplete tasks** to the next day.

Architecture will be refined as the project evolves.

---

## 📚 Learning Objectives

By doing this project I aim to learn:

- Designing **serverless architectures**
- Using **DynamoDB** for task data
- Building **event-driven workflows** with EventBridge
- Handling **notifications** with SNS
- Exposing APIs with **API Gateway**
- Following good **GitHub documentation practices**

---

## 📝 Project Log

I am documenting my journey step by step (design, mistakes, fixes, improvements)  
so recruiters and other learners can see **how I think**, not just the final code.

Stay tuned for more updates!
