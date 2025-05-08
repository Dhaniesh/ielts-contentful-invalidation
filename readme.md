# Cache Invalidation Workflow (AWS Step Functions)

## Overview 🧭

This document provides a high-level understanding of the **Cache Invalidation Workflow** orchestrated using **AWS Step Functions**. It outlines the purpose, flow, and implementation details suitable for software engineers integrating or maintaining this workflow.


![State Machine Workflow](https://github.com/Dhaniesh/ielts-contentful-invalidation/blob/main/cdn-architecture.jpg)

---

## Purpose 🎯

The goal of this workflow is to **invalidate cached content (e.g., in a CDN like CloudFront)** for specific resources after they are updated, ensuring end-users always receive the most recent data.

---

## Flow Summary 🔁

1. A **child component** is published in **Contentful**.
2. A **webhook** is triggered, sending the **child ID** to an API endpoint or directly to the **Step Function**.
3. The **Step Function execution** starts.
4. The execution lambda fetches **parent component IDs** related to the child.
5. A **CloudFront invalidation request** is created for the affected paths.
6. The state machine continues with its polling-based workflow until the invalidation completes.

---

## Workflow Summary 📈

This is a polling-based Step Function that:

1. Triggers a cache invalidation for linked resources.
2. Waits for a fixed interval.
3. Checks the status of the invalidation.
4. Repeats until all invalidations are complete.
5. Exits.

---

## State Machine Steps ⚙️

![State Machine Workflow](https://github.com/Dhaniesh/ielts-contentful-invalidation/blob/main/step-function.jpg)

### 1. **Linked-Resource-Cache-Invalidation**

* Initiates the cache invalidation request for one or more resources.
* Invokes a Lambda function.

### 2. **Wait-For-1-Min**

* Waits for 60 seconds.
* Provides time for the invalidation process to proceed.

### 3. **Check-Invalidation-Status**

* Queries the current status of the invalidation request.
* Returns a status string like `InProgress` or `Completed`.

### 4. **Check-Status-Choice**

* Conditional logic:

  * If `InProgress`: Go back to wait.
  * If `Completed`: Proceed.

### 5. **Check-Invalidation-Completion**

* Final confirmation logic.

### 6. **EndInvalidation**

* Marks the successful end of the state machine execution.

---

## Benefits ✅

* Ensures cache consistency across CDN layers.
* Prevents unnecessary invalidation of resources.

---

## Technologies Used 🧰

* **AWS Step Functions** – Workflow orchestration
* **AWS Lambda** – (Optional) For status checks or triggering invalidation
* **CDN Provider (e.g., CloudFront)** – Manages the cache
* **Contentful** – Integration as CMS/source of cache update triggers

---
