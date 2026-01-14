# Keeping a Single Slack Thread Across Travis CI → ArgoCD → Jenkins

This document describes a **practical, production-ready approach** to keeping all deployment-related Slack notifications (build, deploy, sync, and end-to-end tests) together in **one Slack thread**, even though they originate from **multiple systems**:

* Travis CI (build + push image, update infra repo)
* ArgoCD (autosync deployment to Kubernetes)
* Jenkins (end-to-end tests)

The core idea is to treat the Slack thread identifier as a **correlation ID** that is created once and then **explicitly passed through the pipeline**.

---

## Key Insight

Slack threading does **not** happen automatically across systems.

To keep messages grouped, you must:

1. **Create the Slack parent message once** (early in the pipeline)
2. Capture its **thread identifier** (`thread_ts` / `threadId`)
3. **Persist that value** somewhere ArgoCD can see
4. **Pass it into Jenkins as a build parameter**
5. Have Jenkins post replies using that thread ID

---

## Recommended Pattern (End‑to‑End Thread Propagation)

### High‑Level Flow

```text
Travis CI
  └─ Creates Slack message → captures thread ID
      └─ Commits infra change (image tag + thread ID)
          └─ ArgoCD autosyncs
              └─ ArgoCD triggers Jenkins with thread ID
                  └─ Jenkins posts test results into same Slack thread
```

---

## Step 1: Create the Slack Thread in Travis CI

At the start of the deployment pipeline:

* Travis sends a **Slack parent message** (e.g., "Deploying API v1.2.3")
* Capture the **thread identifier** returned by Slack

  * Slack API: `thread_ts`
  * Jenkins Slack plugin terminology: `threadId`

This parent message becomes the **anchor** for all subsequent updates.

> Important: This must be done *once*. All other systems only reply.

---

## Step 2: Persist the Thread ID in Git (Infra Repo)

Because **ArgoCD autosync cannot accept runtime parameters**, the thread ID must be stored in something Argo reconciles from Git.

### Recommended Storage Options

Choose **one** of the following (in order of preference):

#### Option A: ArgoCD Application Annotation (Best Practice)

```yaml
metadata:
  annotations:
    slackThreadId: "1705078123.456789"
    slackChannelId: "C0123456789"
```

**Why this works well:**

* Declarative
* Versioned
* Easily accessible in Argo templates

#### Option B: Helm Values

```yaml
deployment:
  slack:
    threadId: "1705078123.456789"
    channelId: "C0123456789"
```

#### Option C: Dedicated Metadata File

```yaml
slack:
  threadId: "1705078123.456789"
  channelId: "C0123456789"
```

> Travis already commits an infra change (image tag bump), so adding one more value is low overhead.

---

## Step 3: Use ArgoCD Notifications to Trigger Jenkins

ArgoCD **should not** invoke Jenkins directly via hooks or ad‑hoc Jobs.

Instead, use **ArgoCD Notifications** with a **webhook service**.

### Why Notifications?

* Designed for external integrations
* Templated access to application metadata
* Works cleanly with autosync

### Jenkins Job Requirements

Your Jenkins job must define **string parameters**:

* `SLACK_THREAD_ID`
* `SLACK_CHANNEL_ID`

### Example Conceptual Webhook Call

ArgoCD sends:

```text
POST /job/e2e-tests/buildWithParameters
  SLACK_THREAD_ID={{ .app.metadata.annotations.slackThreadId }}
  SLACK_CHANNEL_ID={{ .app.metadata.annotations.slackChannelId }}
```

ArgoCD automatically fills these from the Application object at runtime.

---

## Step 4: Post Replies from Jenkins Into the Thread

Inside the Jenkinsfile, **never create a new Slack message**.

Always reply using the provided thread ID.

### Jenkins Slack Plugin Behavior

* The plugin allows posting into a thread by using the **thread ID as the channel**
* This keeps all messages grouped under the original parent message

### Conceptual Jenkins Behavior

* "E2E tests started"
* "Running smoke tests"
* "All tests passed ✅"

All appear under the same Slack thread created in Travis.

---

## Optional Enhancements

### 1. Update the Original Message

Instead of (or in addition to) replies, Jenkins can:

* Update the original Slack message
* Append final status (✅ / ❌)

This uses Slack’s `timestamp + channelId` update semantics.

---

### 2. Use Grouping Keys for Argo‑Only Messages

If ArgoCD sends multiple Slack notifications on its own (sync started, sync completed, health degraded), you can:

* Use a **grouping key** (e.g., commit SHA or image tag)
* Have Argo auto‑thread its own messages

> Note: This does **not** automatically include Jenkins — Jenkins still needs the explicit thread ID.

---

## Summary

**What makes this work:**

* Slack thread ID treated as a **correlation ID**
* Persisted declaratively in Git
* Passed explicitly to Jenkins via ArgoCD Notifications

**What to avoid:**

* Trying to pass runtime parameters through Argo autosync
* Creating new Slack messages in Jenkins
* Relying on implicit Slack grouping

---

## Final Recommendation

If you want **clean, traceable, single‑thread deployment visibility**:

* Create the Slack thread **once** in Travis
* Store the thread ID in the infra repo
* Use ArgoCD Notifications to trigger Jenkins with parameters
* Have Jenkins only post **replies**

This pattern scales cleanly across environments, services, and teams.

