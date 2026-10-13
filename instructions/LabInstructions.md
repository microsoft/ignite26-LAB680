# Lab Instructions: Securing AI Agents with Microsoft Entra Conditional Access

**Session Code:** LAB680  
**Duration:** 75 minutes total (15 min Intro, 45–55 min Hands-on, 5 min Wrap-up)  
**Lab Profile:** [LabOnDemand Profile 224816](https://labondemand.com/LabProfile/224816)  
**Primary Portal:** +++https://entra.microsoft.com+++  

---

## Lab Architecture & Scenario

You are the Lead Identity & AI Security Architect for **Contoso Enterprises**. 

Over the past six months, development and operations teams across Contoso have deployed multiple types of AI agents:
1. **Autonomous Cloud Agents:** Deployed via Microsoft Foundry and Copilot Studio that run unattended in the cloud to process data and call internal APIs.
2. **Digital Agent Workers (Agent Users):** Agents that operate with dedicated user identities to triage helpdesk mailboxes, participate in Teams channels, and execute automated desktop tasks on Windows 365 Cloud PCs.
3. **Third-Party & Local Agents:** External tools and custom Python/Node.js agents accessing corporate resources and MCP (Model Context Protocol) servers.

To establish Zero Trust guardrails without impeding innovation, your leadership has tasked you with deploying a comprehensive Conditional Access architecture for AI agents.

One security plan must express security intent across users and agents. Shared targeting and exclusions must apply to every rule inside it, so admins stop rebuilding the same policy repeatedly. New rules must start in report-only mode, so intent can be validated before it is enforced.

---

## Lab Sections & Time Allocation

| Section | Title | Delivery Mode | Estimated Time |
|:---|:---|:---|:---|
| **Section 1** | **Manage Conditional Access Policies at Scale** | Hands-on Interactive | 20 minutes |
| **Section 2** | **Protect Agent Access to Resources** | Hands-on Interactive | 25 minutes |
| **Section 3** | **Gain Visibility into Unified Agent Risk** | Guided Walkthrough & Click-Through Video | 5 minutes |
| **Section 4** | **Adapt Access Continuously to Risk and Action** | Guided Walkthrough & Click-Through Video | 5 minutes |

---

## Section 1: Manage Conditional Access Policies at Scale

> **Objective:** Author a scalable Conditional Access baseline that avoids brittle, per-agent maintenance by leveraging reusable exclusion frameworks, custom security attributes, and safe rollout via report-only mode.

### Background
Managing individual policies for dozens or hundreds of AI agents quickly becomes unmanageable. If every new agent requires an administrator to edit five different policies, human error is inevitable. A scalable security plan:
1. Applies consistent targeting and emergency exclusions across human and agent workloads.
2. Uses metadata (Custom Security Attributes) so agents are governed automatically based on their lifecycle status.
3. Always deploys new rules in **Report-only** mode first so administrators can validate intent without breaking mission-critical autonomous jobs.

---

### Task 1.1: Standardize Shared Targeting and Break-Glass Exclusions

In this task, you will create a foundation policy that enforces global baseline hygiene while ensuring emergency break-glass accounts and core orchestration workloads are excluded.

1. Open **Microsoft Edge** inside the lab VM and browse to the **Microsoft Entra admin center** at:
   +++https://entra.microsoft.com+++
2. Sign in using the lab administrator credentials:
   - **Username:** +++admin@contoso.com+++
   - **Password:** +++Pass@word123!+++ *(or use the VM auto-type macro)*
3. In the left navigation menu, navigate to **Protection** > **Conditional Access** > **Policies**.
4. At the top of the page, select **+ New policy**.
5. Set the policy name to:
   +++[Baseline-Scale] Agent Identity Security Standard - Global Exclusions+++
6. Under **Assignments**, select **Users, agents or workload identities**.
7. In the **What does this policy apply to?** dropdown, select **Agents**.
8. Under **Include**, ensure **All agent identities** is selected.
9. Under **Exclude**:
   1. Select **Users and groups** tab (or **Workload identities / Agent Blueprints**).
   2. Check **Select excluded users / service principals**.
   3. Search for and select the pre-provisioned emergency administrative group:
      +++Emergency-BreakGlass-Agents+++
   4. Select **Select**.
10. Under **Target resources**:
    1. In the **Include** tab, select **All resources (formerly 'All cloud apps')**.
11. Keep this tab open as we configure attribute-based filtering in Task 1.2.

---

### Task 1.2: Implement Attribute-Driven Agent Governance with Custom Security Attributes

Rather than hard-coding specific agent IDs into policies, we will configure attribute-driven Conditional Access. Agents tagged with the attribute `AgentApprovalStatus = Approved` will be permitted, while unauthorized agents will be blocked.

1. Continuing in the policy authoring screen from Task 1.1 (or open a new browser tab to check the attribute configuration under **Protection** > **Custom security attributes**):
2. In the policy builder under **Assignments** > **Agents** > **Exclude**:
   1. Select **Select agent identities based on attributes**.
   2. Set **Configure** to **Yes**.
   3. Configure the rule with the following parameters:
      - **Attribute set:** +++AgentGovernance+++
      - **Attribute name:** +++AgentApprovalStatus+++
      - **Operator:** +++Equals+++
      - **Value:** +++Approved+++
   4. Select **Done**.

> **Pro Tip:** Custom Security Attributes support role delegation. A compliance team or AI Governance Board can update an agent's attribute to `Approved` via Microsoft Entra Governance or API without needing full `Conditional Access Administrator` directory permissions.

3. Under **Access controls** > **Grant**:
   1. Select **Block access**.
   2. Select **Select**.
4. Under **Enable policy**, set the toggle to **Report-only**.
5. Select **Create** to save the policy.

---

### Task 1.3: Deploy Policy in Report-Only Mode and Verify Evaluation Intent

Before turning any agent policy to **On**, you must evaluate its real-world impact. Autonomous agents run batch jobs 24/7; a misconfigured block policy could take down customer-facing workflows without generating human MFA prompts.

1. In the Conditional Access policies list, select **Insights and reporting** in the left navigation under **Monitoring**.
2. Set the **Time range** to **Last 24 hours**.
3. In the **Conditional Access policy** dropdown, select:
   +++[Baseline-Scale] Agent Identity Security Standard - Global Exclusions+++
4. Review the dashboard:
   - **Report-only summary:** View the count of agent sign-ins evaluated as **Report-only: Success**, **Report-only: Failure (Blocked)**, or **Report-only: Not Applied**.
   - Review the **Sign-in list** below. Note that unapproved test agents reflect a `Result: Report-only Failure`, verifying that the policy would properly stop unapproved agents while approved agents are excluded from the block rule.
5. Once intent is confirmed, return to **Policies**, click on your policy, change **Enable policy** to **On**, and select **Save**.

---

## Section 2: Protect Agent Access to Resources

> **Objective:** Build granular, identity-centric protection policies that differentiate between autonomous backend agents and computer-using agent workers running on managed endpoints.

### Background
Agents interact with corporate data through distinct authentication flows:
- **Autonomous Agents (App-Only):** The agent identity or blueprint is the token subject.
- **Agent Users (Interactive/Desktop Workers):** A user object tied to an agent operates in a desktop environment to execute tasks.

In this section, you will configure policies that target both patterns: restricting autonomous agents to designated MCP servers, enforcing endpoint compliance for agent users, and blocking compromised agents with agent risk.

---

### Task 2.1: Scope Autonomous Agents using the Enhanced Object Picker & Blueprints

An **Agent Identity Blueprint** defines the configuration and governance model for child agent identities generated from it. Targeting the blueprint ensures all child agents—current and future—are consistently governed.

1. In the **Conditional Access** blade, select **+ New policy**.
2. Name the policy:
   +++[Agent-Protect] Blueprint-Scoped Autonomous Access to Financial Core+++
3. Under **Assignments**, select **Users, agents or workload identities**.
4. In the **What does this policy apply to?** dropdown, select **Agents**.
5. Under **Include**, choose **Select agent identities**.
6. The **Enhanced Object Picker** opens. Notice the three tabs:
   - **All**
   - **Agent blueprint principals**
   - **Agent identities**
7. Click the **Agent blueprint principals** tab.
8. Search for and select:
   +++Finance-Operations-Blueprint+++
9. Notice that all current child agents derived from this blueprint are automatically covered. Select **Select**.
10. Under **Target resources**:
    1. Select **Select resources**.
    2. Click **None** under Select to launch the resource picker.
    3. Search for the sensitive internal financial system:
       +++Contoso Financial Data API+++
    4. Select **Select**.
11. Under **Access controls** > **Grant**:
    1. Ensure **Grant access** is selected.
12. Set **Enable policy** to **Report-only** and click **Create**.

---

### Task 2.2: Enforce Device Compliance & Execution Environment Boundaries on Agent Users

Some agents are "computer-using agents" that control a browser and desktop operating system (e.g., executing UI automation inside a Windows 365 Cloud PC for Agents). Because they run on endpoints, they must satisfy device compliance and network requirements. However, enforcing device compliance globally would break cloud-native SaaS agents that have no device.

Here, you will use the **Agent execution environments (Preview)** condition to selectively enforce endpoint controls.

1. In the **Conditional Access** blade, select **+ New policy**.
2. Name the policy:
   +++[Agent-User] Require Intune Compliance for Endpoint Agent Workers+++
3. Under **Assignments**, select **Users, agents or workload identities**.
4. In **What does this policy apply to?**, select **Agents**.
5. Under **Include**, choose:
   +++All agent users (Preview)+++
6. Under **Target resources** > **Include**, select **All resources (formerly 'All cloud apps')**.
7. Under **Conditions**:
   1. Select **Agent execution environments (Preview)**.
   2. Set **Configure** to **Yes**.
   3. Select:
      +++Agent user sessions initiated from endpoints+++
   4. Select **Done**.
8. Under **Conditions** > **Network**:
   1. Set **Configure** to **Yes**.
   2. Under **Include**, select **All Compliant Network locations** (powered by Global Secure Access).
   3. Select **Done**.
9. Under **Access controls** > **Grant**:
   1. Select **Grant access**.
   2. Check:
      +++Require device to be marked as compliant+++
   3. Select **Select**.
10. Set **Enable policy** to **Report-only** and select **Create**.

> **Key Learning:** The **Agent execution environments** condition ensures that cloud-hosted agents (such as standard Copilot Studio chatbots) are bypassed, while computer-using agent workers running on VMs or Cloud PCs must verify Intune compliance and compliant network routing.

---

### Task 2.3: Restrict Autonomous Agents to Approved Enterprise APIs and MCP Servers

Agents must not have carte-blanche access to all enterprise APIs. Model Context Protocol (MCP) servers and Open API tools expose high-privilege operations. In this task, you create an explicit boundary policy that blocks autonomous agents from accessing uncategorized enterprise resources.

1. In the **Conditional Access** blade, select **+ New policy**.
2. Name the policy:
   +++[Agent-Boundary] Block Autonomous Agents from Unapproved MCP Endpoints+++
3. Under **Assignments**, select **Users, agents or workload identities**.
4. In **What does this policy apply to?**, select **Agents**.
5. Under **Include**, select **All agent identities**.
6. Under **Target resources**:
   1. Select **Select resources**.
   2. Under **Include**, select:
      +++Core-Customer-Records-MCP-Server+++
   3. Under **Exclude**, select approved internal resources:
      +++Contoso-Approved-Internal-MCP+++
7. Under **Access controls** > **Grant**:
   1. Select **Block access**.
   2. Select **Select**.
8. Set **Enable policy** to **Report-only** and select **Create**.

---

### Task 2.4: Build a Unified Agent Risk-Based Quarantine Policy

Just like human user accounts are evaluated for sign-in and user risk, AI agent identities possess continuous risk scores generated by Microsoft Entra ID Protection. When an agent shows signs of token compromise, unusual API calling frequency, or malicious behavior, it must be quarantined immediately.

1. In the **Conditional Access** blade, select **+ New policy**.
2. Name the policy:
   +++[Agent-Risk] Quarantine High-Risk Agent Identities+++
3. Under **Assignments**, select **Users, agents or workload identities**.
4. In **What does this policy apply to?**, select **Agents**.
5. Under **Include**, select **All agent identities**.
6. Under **Target resources** > **Include**, select **All resources (formerly 'All cloud apps')**.
7. Under **Conditions**:
   1. Select **Agent risk (Preview)**.
   2. Set **Configure** to **Yes**.
   3. Under **Configure agent risk levels needed for policy to be enforced**, check:
      - +++High+++
      - +++Medium+++
   4. Select **Done**.
8. Under **Access controls** > **Grant**:
   1. Select **Block access**.
   2. Select **Select**.
9. Set **Enable policy** to **On**.
10. Select **Create**.

---

## Section 3: Gain Visibility into Unified Agent Risk

> **Mode:** Guided Walkthrough & Analysis *(Click-through Video Component)*  
> **Estimated Time:** ~5 minutes  

### Conceptual Overview
Securing autonomous agents requires an end-to-end view of risk. Rather than forcing security operations teams to monitor siloed alerts across Defender, Purview, Entra, and network gateways, Microsoft Security synthesizes signals into a **single, unified Agent Risk level**:

```
┌────────────────────────────────────────────────────────┐
│             UNIFIED AGENT RISK SIGNALS                 │
├──────────────────────────┬─────────────────────────────┤
│ Microsoft Entra IDP      │ Anomalous token replay,     │
│                          │ atypical execution pattern  │
├──────────────────────────┼─────────────────────────────┤
│ Microsoft Defender XDR   │ Runtime tampering, malware, │
│                          │ unauthorized code execution │
├──────────────────────────┼─────────────────────────────┤
│ Microsoft Purview        │ Data leakage, sensitive doc │
│                          │ exfiltration, mass export   │
├──────────────────────────┼─────────────────────────────┤
│ Global Secure Access     │ Malicious MCP call, shadow  │
│                          │ AI destination, prompt inj  │
└──────────────────────────┴─────────────────────────────┘
                           │
                           ▼
             ┌───────────────────────────┐
             │  UNIFIED AGENT RISK LEVEL │
             │   (Low / Medium / High)   │
             └───────────────────────────┘
```

Each agent carries a single risk level, drawn from how compromised it is, how exposed it is, and how much damage it could do. Signals from Defender, Purview, GSA, and Entra ID Protection roll up into that one level. The same level drives triage, access policy, and network controls.

---

### Guided Walkthrough Steps

#### Step 1: Navigating to Risky Agents in Microsoft Entra ID Protection
1. In the left navigation, browse to **Protection** > **Identity Protection** > **Risky agents (Preview)**.
2. Review the aggregated queue. Each row represents an agent identity or blueprint with its computed risk state:
   - **Risk state:** `At risk`, `Confirmed compromised`, `Dismissed`, `Remediated`.
   - **Risk level:** `High`, `Medium`, `Low`.
   - **Risk detail:** Summarizes the driving factor (e.g., `Suspicious tool call volume`, `Leaked credentials`, `Prompt injection detected`).

#### Step 2: Correlating Cross-Workstream Telemetry
1. Click on the flagged agent: **Contoso-CustomerService-Agent-04**.
2. In the investigation pane:
   - **Risk detections tab:** Shows an alert originating from **Global Secure Access** indicating an attempted connection to an untrusted MCP server (`api.untrusted-tools-mcp.net`).
   - **Defender correlation:** Correlates a runtime alert for unusual process injection within the agent's container environment.
   - **Purview correlation:** Highlights an attempt to summarize files labeled `Confidential \ M&A Strategy`.
3. Because all four engines feed the same unified agent risk model, the agent risk was elevated to **High**, triggering the Conditional Access quarantine policy built in Task 2.4.

---

## Section 4: Adapt Access Continuously to Risk and Action

> **Mode:** Guided Walkthrough & Analysis *(Click-through Video Component)*  
> **Estimated Time:** ~5 minutes  

### Conceptual Overview
Traditional identity verification occurs only at sign-in. For long-running autonomous agents, a static sign-in check is insufficient. If an agent begins a session in a clean state but is hijacked via a prompt injection 20 minutes later, waiting for the token to expire leaves a dangerous window of vulnerability.

Access is evaluated on every request, not just at sign-in, so it changes mid-session as risk changes. Risky agents are down-scoped to read-only, with high-impact actions blocked or sent for human approval. Global Secure Access extends the same enforcement to 3P resources while the user's own sessions continue.

---

### Guided Walkthrough Steps

#### Step 1: Real-Time Token Revocation via Continuous Access Evaluation (CAE)
1. In the lab demo simulation, observe an autonomous agent interacting with the **Contoso Document Store API**.
2. At `T+00:00`, the agent executes legitimate read queries using a valid token issued with 1-hour lifetime.
3. At `T+00:14`, the agent ingests an untrusted email containing an adversarial prompt injection payload.
4. Defender and Purview detect the anomalous payload and publish an agent risk event to Entra ID Protection.
5. Within seconds, Microsoft Entra emits a **CAE revocation event**.
6. The Document Store API rejects the agent's next call with `401 Unauthorized; error="insufficient_claims"`, forcing immediate re-evaluation against Conditional Access. The agent is blocked by the **[Agent-Risk] Quarantine High-Risk Agent Identities** policy.

#### Step 2: Mid-Session Down-Scoping to Read-Only
1. In the advanced policy walkthrough, examine how Conditional Access session controls enforce **limited access** / down-scoping.
2. The agent is permitted to read public support docs, but any POST/DELETE call to internal customer records triggers an automated step-up requirement or approval task routed to the agent's human sponsor.

#### Step 3: Global Secure Access 3P Gateway Enforcement
1. Observe the network traffic logs under **Global Secure Access** > **Traffic logs**.
2. Notice the agent attempting an outbound connection to an unauthorized external AI analytics destination.
3. The Global Secure Access client intercepts the connection inline, evaluates the agent's identity context, blocks the connection, and logs the blocked MCP connection event without severing the host computer's general internet connectivity.

---

## Summary and Wrap-Up (5 Minutes)

Congratulations! You have completed **LAB680**.

### What You Built Today:
1. **Scalable Management:** A unified policy framework utilizing Custom Security Attributes and Report-Only evaluation to govern hundreds of agents without manual policy sprawl.
2. **Resource Boundaries:** Targeted controls protecting APIs and MCP servers using Agent Identity Blueprints.
3. **Execution Environment Hygiene:** Differentiated endpoint protection ensuring computer-using agent workers comply with Intune and network posture without breaking cloud-native agents.
4. **Unified Risk & Continuous Zero Trust:** Complete integration between Entra ID Protection, Defender, Purview, and Global Secure Access providing continuous mid-session protection.
