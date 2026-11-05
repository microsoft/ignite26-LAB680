## Lab 680: AI Agents Are Sending Signals. Make Access Respond



**Session code:** LAB680

**Total session:** 75 minutes (15 min intro, 55 min hands-on, 5 min wrap-up)

**Lab profile:** [LabOnDemand Profile 224816](https://labondemand.com/LabProfile/224816)

**Portal:** [Microsoft Entra admin center](https://entra.microsoft.com)

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
| 4 | Understand sign-in logs | 5 min |

===

Select **Sign-in**

In **entra.admin.com**, enter the following credentials:

Username: ++@lab.CloudCredential(LAB680-Ignite26wTAP).UserPrincipalName++

TAP: ++@lab.CloudCredential(LAB680-Ignite26wTAP).TemporaryAccessPass++


===

## Section 1: Manage Conditional Access policies at scale

Before configuring agent protections, Maya reviews Contoso's existing Conditional Access policies. Several
policies repeat the same user assignments and exclusions, making them harder to maintain consistently. 

In this section, create a Policy Plan for HQ users and migrate existing branch-office policies into a plan.
Policy Plans let you define shared conditions once and organize distinct controls as separate rules.

### Create a Policy Plan for HQ users

1. In the Microsoft Entra admin center (entra.microsoft.com), navigate to > **Conditional Access** ++https://aka.ms/CAAssistiveAgentsWithRisk++ (Copy + Right-click Paste and Go in the toolbar) > **Policy Plans** 
3. Select the option to create a new Policy Plan.
4. Configure the plan:
   - **Name:** Enter the name: +++HQ Policy Plan+++.
   - **Description:** Enter: +++Compliance for HQ users+++.
   - **Tags:** Add the tag +++HQ+++.
   - Verify that your account is listed as the owner.
5. Under the plan's shared assignments, select **Users and groups**.
6. For **Include**, search or filter for +++HQ+++, then select the **HQ all users** group.
7. For **Exclude**, search or filter for +++HQ+++, then select **Breakglass HQ**.
8. Continue to create the first rule. The users and groups you selected are shared by the rules in this plan.

Now that we have configured a policy plan for the HQ users and excluded an emergency-access group, let's define separate rules for phishing resistant and compliant devices.

### Add a rule to require phishing-resistant authentication

1. Create the first rule in the Policy Plan.
2. Configure the rule:
   - **Name:** `Require Phishing-Resistant Authentication`
   - **Description:** Describe the purpose of the rule.
   - **State:** **Report-only**
   - Under **Accessing these resources**, select **All resources**.
   - Under **Conditions**, leave **Client apps** set to **All client apps**.
3. Under **Grant**, configure the access control:
   - Select **Grant access**.
   - Select **Require authentication strength**.
   - Select **Phishing-resistant MFA**, then confirm your selection.
4. Review and create the Policy Plan and its initial rule.

### Add a rule to require compliant devices

1. From the HQ Policy Plan, add a new rule.
2. Configure the rule:
   - **Name:** `Require Compliant Device`
   - **Description:** Explain the purpose of the rule.
   - Under **Accessing these resources**, select **All resources**.
   - Under **Conditions**, leave **Client apps** set to **All client apps**.
3. Under **Grant**, select **Grant access**, then **Require device to be marked as compliant**.
4. Save the rule. Keep new rules in **Report-only** while validating their effect.

### Migrate existing Conditional Access policies into a Policy Plan

1. Navigate to your **Conditional Access policies** at ++https://aka.ms/CAAssistiveAgentsWithRisk++ (Copy + Right-click Paste and Go in the toolbar).
2. Filter the policy list for `Branch` to display the branch-office policies.
3. Select **Migrate to Conditional Access Policy Plans**, then select **Get started**.
4. From the filtered list, select the policies to migrate. Use **Select all** to select all five branch-office policies.
5. Select **Merge 5 policies into a plan**.
6. Review the preview of the new Policy Plan.
7. Enter `Branch Office Access` as the Policy Plan name.
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

Next, Maya opens Risky agents in Microsoft Entra ID Protection and discovers an agent flagged for
investigation. 

Rather than treating the risk label alone as proof of compromise, she examines the available runtime,
exposure, and blast-radius information. What evidence supports the assessment? What resources could be
affected? Is any supporting information unavailable? 

### Review agent risk in Microsoft Entra

1. In the Microsoft Entra admin center, go to Microsoft Entra ID Protection > **Risky Agents** [Risky Agents](https://entra.microsoft.com/?Microsoft_AAD_RegisteredApps=demo&exp.riskyAgentDetailsPaneV2Enabled=true#view/Microsoft_AAD_IAM/IdentityProtectionMenuBlade/~/RiskyAgents/menuId/RiskyAgents/fromNav) (Copy + Right-click Paste and Go in the toolbar)
2. Open **Risky agents** section and select an agent. 
3. Review and record the agent's:
   - Overall risk
   - Risk state
   - Available runtime risk information
   - Available exposure information
   - Available blast-radius information
4. Open the available evidence or investigation links. Verify that the evidence is associated with the agent you selected.

Maya follows the investigation into Microsoft Defender AI inventory, carefully verifying that she is reviewing the same agent or instance. She compares the findings across both experiences and checks the identity, source, and time associated with the available evidence. 


### Review agent risk in Microsoft Defender

1. Go to ++security.microsoft.com++ (Copy + Right-click Paste and Go in the toolbar). Under ***Assets**, open **Defender AI inventory**.
2. Locate the same agent or agent instance reviewed in Microsoft Entra. Verify that it maps to the agent identified in the previous exercise.
3. Review the agent's **Dynamic risk assessment**, including available:
   - Exposure indicators
   - Blast-radius indicators
   - Affected resources
   - Recommendations
4. Open available supporting evidence, including runtime alerts. Review the source, time, and identity associated with the evidence.
5. Compare the Microsoft Defender findings with the risk information reviewed in Microsoft Entra.

**Expected outcome:** Use the supporting evidence to explain the agent's Dynamic risk and potential impact, and confirm that you investigated the same agent in both experiences.

===

## Section 3: Protect agent access and adapt access to risk and action

Maya prepares two layers of protection. 
  
For high-risk agents, she configures a Policy Plan with separate rules covering the lab's selected agent
identity and supported assistive-agent interactions for the test user. Her intended outcome is to block
high-risk agent access while retaining existing user-access protections.

### Create a Policy Plan to block high-risk agents

The plan organizes the rules needed to cover different agent types and establishes shared conditions.

1. Navigate to **Conditional Access** > ++https://aka.ms/CAAssistiveAgentsWithRisk++ (Copy + Right-click Paste and Go in the toolbar) > **Policy Plans** and create a new Policy Plan.
2. Enter a name. Suggested format: `[your name or initials] [Test] Block High-Risk Agents`.
3. Under **For these identities**, leave the selection unconfigured at the plan level.
4. Under **Accessing these resources** > **Resources**, include **All resources**.
5. Under **Conditions** > **Agent risk**, select **High**.
6. Create the Policy Plan.

### Create a rule to block high-risk agent identities

**Policy intent:** For an agent identity, when agent risk is High, block access to all Entra resources. For this lab, create the rule in **Report-only** mode.

1. From the Policy Plan, add a rule. Suggested name: `[your name or initials] [Test] Block High-Risk Agent Identities`.
2. Set the rule **Status** to **Report-only**.
3. Under **For these identities**, select **Agents**. Under **Included users**, select **Select agent identities**, search for the test agent provided in the lab, and select it.  Agent Risk is inherited from the plan; do not configure it again in the rule.
4. Under **Grant and Session Controls** > **Grant**, select **Block**.
5. Save the rule.

### Create a rule to block high-risk assistive agents

**Policy intent:** Block access to all Entra resources when a high-risk assistive agent acts on behalf of
the selected test user. This adds agent-aware protection to existing user-scoped Conditional Access
policies. It applies to supported flows where agent context is available and might not identify every agent
interaction. Keep existing policies that govern users' access to protected resources in place.

For this lab, target only the specified test user and keep the rule in **Report-only** mode.

1. From the Policy Plan, add a rule. Suggested name: `[your name or initials] [Test] Block High-Risk Assistive Agents`.
2. Set the rule **Status** to **Report-only**.
3. Under **Conditions** > **Assistive Agents**, select **All Assistive Agents**.
4. Under **For these identities**, select **Users & Groups**. Under **Included Users**, select **Users and Groups**, search for your test user, and select that user. Agent Risk is inherited from the plan; do not configure it again in the rule.
5. Under **Grant and Session Controls** > **Grant**, select **Block**.
6. Save the rule.

For medium-risk assistive agent actions, Maya takes a more targeted approach. She configures a separate plan that applies Restrict Actions to Microsoft-recommended protected actions. The intent is to constrain sensitive actions rather than apply a blanket access block. 

### Create a Policy Plan to restrict medium-risk agent actions

1. Navigate to **Conditional Access** > **Policy Plans** and create a new Policy Plan.
2. Enter a name. Suggested format: `[your name or initials] [Test] Restrict Agent Actions`.
3. Under **For these identities** > **Users and Groups**, select **All Users**.
4. Under **Accessing these resources**, leave the plan-level selection unconfigured.
5. Under **Conditions** > **Agent Risk**, select **Medium**.
6. Create the Policy Plan.

### Create a rule to restrict medium-risk assistive-agent actions

1. From the Policy Plan, add a rule. Suggested name: `[your name or initials] [Test] Restrict Agent Actions for Medium-Risk Agents`.
2. Under **For these identities**, use the inherited **All Users** assignment from the plan; do not add another selection.
3. Under **Accessing these resources** > **Agent Actions**, select **Microsoft recommended protected actions**.
4. Under **Conditions** > **Assistive Agents**, select **All Assistive Agents**. Agent Risk is inherited from the plan; do not configure it again in the rule.
5. Under **Grant and session controls** > **Session**, select **Restrict Actions**.
6. Review and save the rule in **Report-only** mode so you can validate its effect before enforcement.

===

Finally, Maya opens the sign-in logs, filters to the lab account and time window, and reviews an available
event and its Conditional Access details. We can understand from these sign-in logs whether policies applied to specific identities.

Her investigation has connected three things: the agent's risk signals, the intended access response, and
the evidence available to validate that response. 

 
## Section 4: Understand sign-in logs

Review sign-in activity related to your lab work.

1. Open the **Microsoft Entra admin center.**
2. Navigate to Entra ID > Monitoring & health > Sign-in logs. 
3. Use the available filters to narrow the sign-in activity to your user account and the date/time range of the lab.
4. Select one of your sign-in events to review its details.
5. Open the Conditional Access tab and review the policies evaluated for the sign-in. Check whether any Report-only policies appear and review their results.

===

# Wrap-up

You used Conditional Access Policy Plans to organize shared conditions and separate rules, reviewed Dynamic Agent Risk in Microsoft Entra and Microsoft Defender, created report-only protections for high-risk agent identities and assistive agents, and explored how to restrict protected agent actions.
