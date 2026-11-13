## Lab 680: AI Agents Are Sending Signals. Make Access Respond


**Session code:** LAB680

**Total session:** 75 minutes (15 min intro, 55 min hands-on, 5 min wrap-up)

**Lab profile:** [LabOnDemand Profile 224816](https://labondemand.com/LabProfile/224816)

**Portal:** [Microsoft Entra admin center](https://entra.microsoft.com)

##Access the lab
Select **Sign-in**

In the browser, go to +++entra.microsoft.com+++ and enter the following credentials:

Username: +++@lab.CloudCredential(LAB680-Ignite26wTAP).UserPrincipalName+++

TAP: +++@lab.CloudCredential(LAB680-Ignite26wTAP).TemporaryAccessPass+++

===

## Scenario

Maya is a security administrator at Contoso, where employees increasingly rely on AI agents to research
information, summarize documents, and complete tasks. Some agents use their own identities; others act on
behalf of employees. 

Her challenge is to protect Contoso's resources without unnecessarily interrupting useful work. She needs
to understand which agents present risk, investigate the evidence, and configure access controls that
respond appropriately.  In this lab, you will use Policy Plans to organize shared conditions and rules,
review Dynamic Agent Risk across Microsoft Entra and Defender, and create agent-aware access controls.

The lab uses a prepared tenant. Use the accounts, agent, user, links, and existing policies supplied in
your lab environment. Do not substitute production identities or enable policies unless a task explicitly
directs you to do so. The required policies are already configured in the lab tenant, so you do not need to
save new policies. If you choose to save a policy, keep it in Report-only mode. Do not enable enforcement.

## Lab sections

| Section | Activity | Estimated time |
|---|---|---:|
| 1 | Manage Conditional Access policies at scale | 15 min |
| 2 | Gain visibility into Dynamic Agent Risk | 10 min |
| 3 | Protect agent access and adapt access to risk and action | 25 min |

===

## Section 1: Manage Conditional Access policies at scale

Before configuring agent protections, Maya reviews Contoso's existing Conditional Access policies. Several
policies repeat the same user assignments and exclusions, making them harder to maintain consistently. 

In this section, create a Policy Plan for HQ users and migrate existing branch-office policies into a plan.
Policy Plans let you define shared conditions once and organize distinct controls as separate rules.

### 1.1 Create a Policy Plan for HQ users

1. In the Microsoft Entra admin center (entra.microsoft.com), navigate to > **Conditional Access** ++https://aka.ms/continuous-ca++ (Copy + Right-click Paste and Go in the toolbar) > **Policy Plans** 
3. Select the option to create a new Policy Plan.
4. Configure the plan:
   - **Name:** Enter the name: +++HQ Policy Plan+++.
   - **Description:** Enter: +++Compliance for HQ users+++.
   - **Tags:** Add the tag +++HQ+++.
   - Verify that your account is listed as the owner.
5. Under Shared identities, select **+ Add users** > **Users and groups**.
6. For **Include**, search or filter for +++HQ+++, then select **Users and groups** > Search for **+++HQ all users+++** group.
7. For **Excluded users**, select **Users and groups** > search or filter for +++Breakglass HQ+++, then select **Breakglass HQ**. Select **Add**
8. Select **Next: Rule Information** to create the first rule. The users and groups you selected are shared by the rules in this plan.

Now that we have configured a policy plan for the HQ users and excluded an emergency-access group, let's define separate rules for phishing resistant and compliant devices.

### 1.2 Add a rule to require phishing-resistant authentication

1. Create the first rule in the Policy Plan.
2. Configure the rule:
   - **Name:** +++Require Phishing-Resistant Authentication+++
   - **Description:** Leave blank.
   - **State:** Leave as **Report-only**
   - Under **Accessing these resources**, **+Add resource** > **Resources** > select **All resources** > **Add**.
   - Under **Conditions**, leave **Client apps** set to **All client apps**.
3. Under **Grant and session controls**, configure the access control:
   - Select **+ Add control**.
   - Select **Grant**.
   - Select **Require authentication strength**.
   - Select **Phishing-resistant MFA**, then **Add** to confirm your selection.
4. Select **Next: Review + create** and **Create** the Policy Plan and its initial rule.

### 1.3 Add a rule to require compliant devices

1. From the HQ Policy Plan, add a new rule. (Go to **Entra ID** > **Conditional Access** > **Policy Plans** > +++HQ Policy Plan+++
2. Configure the rule:
   - **Name:** +++Require Compliant Device+++
   - **Description:** Leave blank.
   - Unfer **For these identities**, leave the selections as-is from the policy plan.
   - Under **Accessing these resources**, select **+Add resources** > **Resources** > **All resources** > **Add**.
   - Under **Under the conditions**, leave **Client apps** set to **All client apps**.
3. Under **Grant**, select **Add control** > **Grant**, then **Require device to be marked as compliant** > **Add**.
4. Save the rule. Keep new rules in **Report-only** while validating their effect.

### 1.4 Migrate existing Conditional Access policies into a Policy Plan

1. Navigate to your **Conditional Access policies** at ++https://aka.ms/CAAssistiveAgentsWithRisk++ (Copy + Right-click Paste and Go in the toolbar) > **Policies**.
2. Select **Migrate to Conditional Access Policy Plans**, then select **Get started**.
3. Filter the policy list for +++Branch+++ to display the branch-office policies.
4. From the filtered list, select the policies to migrate. Use **Select all** to select all five branch-office policies.
5. Select **Merge 5 policies into a plan**.
6. Review the preview of the new Policy Plan.
7. Enter +++Branch Office Access+++ as the Policy Plan name.
8. Review the **Shared Conditions** identified from configuration common to the selected policies.
9. Review the individual rules created from the existing policies. Examine the identities, resources, locations, grant controls, and session configurations associated with each rule.
10. Select **Create policy plan**.
11. Verify that the five existing policies have been migrated into the **Branch Office Access** plan with shared conditions and individual rules.

**What you accomplished:** You helped Maya simplify Contoso's Conditional Access management by consolidating
five existing branch-office policies into a single Branch Office Access plan. By organizing common
configurations as shared conditions while preserving each policy's distinct controls, you made the policies
easier to manage and maintain consistently.

===

## Section 2: Gain visibility into Dynamic Agent Risk

With Conditional Access policies organized, Maya turns to a new challenge: understanding the risks posed by AI agents accessing her organization's resources.

She opens Risky agents in Microsoft Entra ID Protection and discovers an agent flagged for investigation. Rather than treating the risk label alone as proof of compromise, she examines the available runtime, exposure, and blast-radius information. What evidence supports the assessment? What resources could be affected? Is any supporting information unavailable?

### 2.1 Review agent risk in Microsoft Entra

1. In the Microsoft Entra admin center, go to Microsoft Entra ID Protection > **Risky Agents** ++https://entra.microsoft.com/?Microsoft_AAD_RegisteredApps=demo&exp.riskyAgentDetailsPaneV2Enabled=true#view/Microsoft_AAD_IAM/IdentityProtectionMenuBlade/~/RiskyAgents/menuId/RiskyAgents/fromNav++ (Copy + Right-click Paste and Go in the toolbar)
2. Open **Risky agents** section and select the **IT Management** agent. 
3. Review the agent's:
   - Overall risk
   - Risk state
   - Available runtime risk information
   - Available exposure information
   - Available blast-radius information

Note that the list of agents contains both cloud agents built in Copilot Studio, and local agents such as Github Copilot CLI.
 
4. Scroll through the list of detections and recommendations. Open the available evidence or investigation links. Verify that the evidence is associated with the agent you selected. 

The new risk assessment model includes three factors: indications of malicious activity (**runtime**), how easy it is to attack (**exposure**), what the potential impact (**blast radius**). These signals are now unified across Entra, Defender, and Purview.

Maya follows the investigation into Microsoft Defender AI inventory, carefully verifying that she is reviewing the same agent or instance. She compares the findings across both experiences and checks the identity, source, and time associated with the available evidence. 


### 2.2 Review agent risk in Microsoft Defender

1. Go to ++security.microsoft.com++ (Copy + Right-click Paste and Go in the toolbar). Under **Show navigation** > **Assets**, open **AI Agents**.
2. Locate the same agent or agent instance reviewed in Microsoft Entra: the +++IT Management+++ agent. Verify that the signals match what was shown for the agent identified in the previous exercise.
3. Click into the agent and scroll to review the agent's **Dynamic risk assessment**, including available:
   - Exposure level and indicators
   - Blast-radius indicators
   - Tools used by the agent: Does the agent have permissions to merge pull requests or peform other privileged actions?
   - Connected MCP servers
   - Capabilities
   - Recommendations
4. Open available supporting evidence, including runtime alerts. Review the source, time, and identity associated with the evidence.
5. Compare the Microsoft Defender findings with the risk information reviewed in Microsoft Entra.

**Expected outcome:** Use the supporting evidence to explain the agent's Dynamic risk and potential impact, and confirm that you investigated the same agent in both experiences. In a single blade, we have visibility into risk factors such as weak instructions, privileged access, tool connections, and agent capabilities.

===

## Section 3: Protect agent access and adapt access to risk and action

To enable secure agent access without disrupting productivity, Maya configures a Policy Plan with separate rules covering supported assistive-agent interactions for the test user. Her intended outcome is to constrain users' risky assisitive agent access (e.g. local agents such as Github Copilot CLI and Claude Code) without affecting the users' direct access.

This plan adds an additional layer of protection on top of existing user-scoped Conditional Access policies. It applies to supported flows where agent context is available and might not identify every agent interaction. Keep existing policies that govern users' access to protected resources in place.

### 3.1 Create a Policy Plan to apply progressive controls based on agent risk

The plan organizes the rules needed to cover different agent types and establishes shared conditions.

1. Navigate to **Conditional Access** > ++https://aka.ms/continuous-ca++ (Copy + Right-click Paste and Go in the toolbar) > **Policy Plans** and create a new Policy Plan.
2. Enter a name. Suggested format: +++Block High-Risk Agents+++.
3. Under **Shared identities**, leave the selection unconfigured at the plan level.
4. Under **Shared resources** leave the selection unconfigured at the plan level
5. Under **Shared Conditions** > **Add condition** > **Assistive Agents**, select **All Assistive Agents**
6. Select **Next: Rule information**.

### 3.2 Create a rule to require human approval for sensitive actions when agent risk is low.

**Policy intent:** For low-risk assistive agents, Maya does not want to block the agent entirely. However, she does not want the risky agent to have no policy guardrails either. She configures a rule that applies the **Require human approval** control to Microsoft-classified actions on MS Graph and WorkIQ. The intent is to introduce an admin-designated human reviewer whose approval is required for the agent to be able to proceed with the action.

1. From the Policy Plan, add a rule. Suggested name: +++Require manager approval for Low-Risk Agents+++.
2. Under **For the identities**, **+ Add users** > **Users and groups** > **Included users** > **Users and groups** > search for **+++Test User+++**, and select that user. Select **Add**.
3. Under **Accessing these resources** > **Actions and destination**, select **Supported Microsoft destinations** > **Protect sensitive actions** > Choose categories such as **Sensitive data access** > **Add**.
4. Under **Under these conditions** > **+Add control**> select **Agent Risk**, select **Low** > **Apply**.
5. Under **Grant and session controls** > **Add control** > **Session**, select **Require human approval** > select **Use an existing access package** > select **Access package for human approval** > **Add**.
6. Review and save the rule in **Report-only** mode so you can validate its effect before enforcement.

### 3.3 Create a rule to block sensitive actions when agent risk is medium.

**Policy intent:** For medium-risk assistive agents, Maya introduces additional policy restrctions that governs what an assisitve agent can do. She configures a separate rule that applies the **Restrict Actions** control to Microsoft-classified actions on MS Graph and WorkIQ and categories of 3P SaaS destinations. The intent is to constrain sensitive actions rather than apply a blanket access block that would disrupt routine work. 

1. From the Policy Plan, add a rule. Suggested name: +++Restrict Agent Actions for Medium-Risk Agents+++.
2. Under **For these identities**, **+ Add users** > **Users and groups** > select **users and groups**> search for **++Test User++**, and select that user.
3. Under **Accessing these resources** > **Add Resources** > **Actions and destination**, select **Microsoft destinations** > **Protect sensitive actions** > Choose categories such as **Sensitive data access** > **Add**.
4. Under **Conditions** > **Agent Risk**, select **Medium**.
5. Under **Grant and session controls** > **Add control** > **Session**, select **Restrict Actions** > **Add**.
6. Review and save the rule in **Report-only** mode so you can validate its effect before enforcement.

### 3.4 Create a rule to block all actions when agent risk is high.

**Policy intent:** For high-risk assisitve agents, Maya wants to block the agent's access to all Entra and non-Entra resources. This ensures that a high-risk assistive agent acting on behalf of the selected test user is not allowed to perform any action regardless of how sensitive the action is.

For this lab, target only the specified test user and keep the rule in **Report-only** mode.

1. From the Policy Plan, add a rule. Suggested name: +++Block High-Risk Assistive Agents+++.
2. Leave the **Status** as **Report-only**.
3. Under **For the identities identities**, select **Add users, agents, or workload identities**. Select **Users & Groups**. Under **Included Users**, select **Users and Groups**, search for **++Test User++**, and select that user. Select **Add**. 
4. Under **Accessing these resources** > **Resources** > select **All resources** > **Add**.
5. Under **Under these conditions** > **+ Add condition** > **Agent risk**, select **High**.
6. Under **Grant and Session Controls** > **+ Add control** > **Grant**, select **Block**.
7. Save the rule.

===

# Wrap-up

You used Conditional Access Policy Plans to organize shared conditions and separate rules, reviewed Dynamic Agent Risk in Microsoft Entra and Microsoft Defender, created report-only protections for high-risk agent identities and assistive agents, and explored how to restrict protected agent actions.
