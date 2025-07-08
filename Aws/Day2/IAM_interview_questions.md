# Interview Questions

Q: What is AWS IAM, and why is it important?

A: AWS IAM (Identity and Access Management) is a service provided by Amazon Web Services that helps you control access to your AWS resources. It allows you to manage user identities, permissions, and policies. IAM is important because it enhances security by ensuring that only authorized individuals or entities have access to your AWS resources, helping you enforce the principle of least privilege and maintain a secure environment.

Q: What is the difference between IAM users and IAM roles?

A: IAM users represent individual people or entities that need access to your AWS resources. They have their own credentials and are typically associated with long-term access. On the other hand, IAM roles are used to grant temporary access to AWS resources, usually for applications or services. Roles have associated policies and can be assumed by trusted entities to access resources securely.

Q: What are IAM policies, and how do they work?

A: IAM policies are JSON documents that define permissions. They specify what actions are allowed or denied on AWS resources and can be attached to IAM users, groups, or roles. Policies control access by matching the actions requested by a user or entity with the actions allowed or denied in the policy. If a requested action matches an allowed action in the policy, access is granted; otherwise, it is denied.

Q: What is the principle of least privilege, and why is it important in IAM?

A: The principle of least privilege states that users should be granted only the permissions necessary to perform their tasks and nothing more. It is important in IAM because it minimizes the risk of unauthorized access and limits the potential damage that could be caused by a compromised account. Following the principle of least privilege helps maintain a secure environment by ensuring that users have only the permissions they need to perform their job responsibilities.

Q: What is an AWS managed policy?

A: An AWS managed policy is a predefined policy created and managed by AWS. These policies cover common use cases and provide predefined permissions for specific AWS services or actions. AWS managed policies are maintained and updated by AWS, ensuring they stay up to date with new AWS services and features. They can be attached to IAM users, groups, or roles in your AWS account.

Q: IAM **roles** and **policies** difference ?

### 🔐 **IAM Role** – *“Who you are and what you're allowed to do temporarily”*

* **A role is an identity** with **temporary permissions**.
* Roles are meant to be **assumed by users, services, or other AWS accounts**.
* Example: An EC2 instance assumes a role to access S3.

#### 📌 Think of a role as:

> "A jacket you wear that comes with permission tags."

---

### 🧾 **IAM Policy** – *“What you’re allowed to do”*

* A policy is a **document (in JSON)** that defines **permissions**.
* It controls **what actions are allowed/denied**, on which **resources**.
* Policies can be:

  * Attached to **users**, **groups**, or **roles**
  * Managed by AWS or custom-created

#### 📌 Think of a policy as:

> "The rulebook that says what you can and can't do."

---

### ✅ Example in Action:

Let’s say you want a Lambda function to access DynamoDB.

1. You create an **IAM Role** for Lambda.
2. You attach a **policy** to that role with `dynamodb:*` permissions.
3. The Lambda function **assumes the role** and gets the permissions from the **policy**.

---

### 🔄 Summary:

| Feature     | **IAM Role**                        | **IAM Policy**                             |
| ----------- | ----------------------------------- | ------------------------------------------ |
| What it is  | Identity with temporary permissions | Permission rules (JSON documents)          |
| Used for    | Delegation / temporary access       | Defining allowed actions and resources     |
| Attached to | AWS services, users, other roles    | Roles, users, groups                       |
| Contains    | No permissions by itself            | Actual permissions (Allow/Deny statements) |



