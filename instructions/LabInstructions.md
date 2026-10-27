# Lab instructions: Securing AI agents with Microsoft Entra Conditional Access

**Session code:** LAB680
**Total session:** 75 minutes (15 min intro, 55 min hands-on, 5 min wrap-up)
**Lab profile:** [LabOnDemand Profile 224816](https://labondemand.com/LabProfile/224816)
**Portal:** [Microsoft Entra admin center](https://entra.microsoft.com)

## Scenario

You are helping Contoso strengthen Conditional Access for users and AI agents. In this lab, you will use Policy Plans to organize shared conditions and rules, review Dynamic Agent Risk across Microsoft Entra and Defender, and create agent-aware access controls.

The lab uses a prepared tenant. Use the accounts, agent, user, links, and existing policies supplied in your lab environment. Do not substitute production identities or enable policies unless a task explicitly directs you to do so. You do not need to save the policies as we already have the same policies configured in the tenant. If you choose to enable the policies, please only do so in Report-Only Mode. 

## Lab sections

| Section | Activity | Estimated time |
|---|---|---:|
| 1 | Manage Conditional Access policies at scale | 15 min |
| 2 | Gain visibility into Dynamic Agent Risk | 10 min |
| 3 | Protect agent access and adapt access to risk and action | 25 min |
| 4 | Understand sign-in logs | 5 min |

## Section 1: Manage Conditional Access policies at scale

In this section, create a Policy Plan for HQ users, explore a plan for agent protection, and migrate existing branch-office policies into a plan. Policy Plans let you define shared conditions once and organize distinct controls as separate rules.

### Create a Policy Plan for HQ users

1. In the Microsoft Entra admin center, navigate to [**Conditional Access**](https://entra.microsoft.com/?Microsoft_AAD_RegisteredApps=demo&exp.riskyAgentDetailsPaneV2Enabled=true#view/Microsoft_AAD_ConditionalAccess/ConditionalAccessBlade/~/Overview) > [**Policy Plans**](https://entra.microsoft.com/?Microsoft_AAD_RegisteredApps=demo&exp.riskyAgentDetailsPaneV2Enabled=true#view/Microsoft_AAD_ConditionalAccess/ConditionalAccessBlade/~/PolicyPlans).
2. Select the option to create a new Policy Plan.
3. Configure the plan:
   - **Name:** Enter a name for the HQ Policy Plan.
   - **Description:** Explain the purpose of the plan.
   - **Tags:** Add the tag `HQ`.
   - Verify that your account is listed as the owner.
4. Under the plan's shared assignments, select **Users and groups**.
5. For **Include**, search or filter for `HQ`, then select the **HQ all users** group.
6. For **Exclude**, search or filter for `HQ`, then select **Breakglass HQ**.
7. Continue to create the first rule. The users and groups you selected are shared by the rules in this plan.

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

**What you accomplished:** The HQ user scope and exclusions are defined once and shared across the plan's rules. The phishing-resistant authentication and compliant-device requirements are separate controls.

### Migrate existing Conditional Access policies into a Policy Plan

1. Navigate to your [Conditional Access policies](https://entra.microsoft.com/?Microsoft_AAD_RegisteredApps=demo&exp.riskyAgentDetailsPaneV2Enabled=true#view/Microsoft_AAD_ConditionalAccess/ConditionalAccessBlade/~/Policies).
2. Filter the policy list for `Branch` to display the branch-office policies.
3. Select **Migrate to Conditional Access Policy Plans**, then select **Get started**.
4. From the filtered list, select the policies to migrate. Use **Select all** to select all five branch-office policies.
5. Select **Merge 2 policies into a plan**.
6. Review the preview of the new Policy Plan.
7. Enter `Branch Office Access` as the Policy Plan name.
8. Review the **Shared Conditions** identified from configuration common to the selected policies.
9. Review the individual rules created from the existing policies. Examine the identities, resources, locations, grant controls, and session configurations associated with each rule.
10. Select **Create policy plan**.
11. Verify that the five existing policies have been migrated into the **Branch Office Access** plan with shared conditions and individual rules.

**What you accomplished:** You consolidated existing Conditional Access configuration into shared conditions and individual rules in a Policy Plan.

## Section 2: Gain visibility into Dynamic Agent Risk

### Review agent risk in Microsoft Entra

1. In the Microsoft Entra admin center, go to Microsoft Entra ID Protection > [Risky agents](https://entra.microsoft.com/?Microsoft_AAD_RegisteredApps=demo&exp.riskyAgentDetailsPaneV2Enabled=true#view/Microsoft_AAD_IAM/IdentityProtectionMenuBlade/~/RiskyAgents/menuId/RiskyAgents/fromNav/).
2. Open **Risky agents** section and select an agent. 
3. Review and record the agent's:
   - Overall risk
   - Risk state
   - Available runtime risk information
   - Available exposure information
   - Available blast-radius information
4. Open the available evidence or investigation links. Verify that the evidence is associated with the agent you selected.

**Expected outcome:** Use the risk assessment and its supporting evidence to explain the agent's risk and identify a next investigation step.

### Review agent risk in Microsoft Defender

1. Open **Defender AI inventory**.
2. Locate the same agent or agent instance reviewed in Microsoft Entra. Verify that it maps to the agent identified in the previous exercise.
3. Review the agent's **Dynamic risk assessment**, including available:
   - Exposure indicators
   - Blast-radius indicators
   - Affected resources
   - Recommendations
4. Open available supporting evidence, including runtime alerts. Review the source, time, and identity associated with the evidence.
5. Compare the Microsoft Defender findings with the risk information reviewed in Microsoft Entra.

**Expected outcome:** Use the supporting evidence to explain the agent's Dynamic risk and potential impact, and confirm that you investigated the same agent in both experiences.

## Section 3: Protect agent access and adapt access to risk and action

**Goal:** Prevent agents identified as high risk from obtaining access to protected resources, and let agents continue non-risky work while preventing sensitive actions or requiring a person to approve them.

### Create a Policy Plan to block risky agents

The plan organizes the rules needed to cover different agent types and establishes shared conditions.

1. Navigate to **Conditional Access** > **Policy Plans** and create a new Policy Plan.
2. Enter a name. Suggested format: `[your name or initials] [Test] Block High-Risk Agents`.
3. Under **For these identities**, leave the selection unconfigured at the plan level.
4. Under **Accessing these resources** > **Resources**, include **All resources**.
5. Create the Policy Plan.

### Create a rule to block high-risk agent identities

**Policy intent:** For every agent identity, when agent risk is High, block access to all Entra resources. For this lab, create the rule in **Report-only** mode.

1. From the Policy Plan, add a rule. Suggested name: `[your name or initials] [Test] Block High-Risk Agent Identities`.
2. Set the rule **Status** to **Report-only**.
3. Under **For these identities**, select **Agents**. Under **Included users**, select **Select agent identities**, search for the test agent provided in the lab, and select it.
4. Under **Conditions** > **Agent risk**, select **High**.
5. Under **Grant and Session Controls** > **Grant**, select **Block**.
6. Save the rule.

### Create a rule to block high-risk assistive agents

**Policy intent:** Block access when Microsoft identifies a high-risk assistive agent acting on behalf of a user. This adds agent-aware protection to existing user-scoped Conditional Access policies. It applies to supported flows where agent context is available and might not identify every agent interaction. Keep existing policies that govern users' access to protected resources in place.

For this lab, target only the specified test user and keep the rule in **Report-only** mode.

1. From the Policy Plan, add a rule. Suggested name: `[your name or initials] [Test] Block High-Risk Assistive Agents`.
2. Set the rule **Status** to **Report-only**.
3. Under **Conditions** > **Assistive Agents**, select **All Assistive Agents**.
4. Under **For these identities**, select **Users & Groups**. Under **Included Users**, select **Users and Groups**, search for your test user, and select that user.
5. Under **Grant and Session Controls** > **Grant**, select **Block**.
6. Save the rule.

### Create the Policy Plan

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

## Section 4: Understand sign-in logs

Review sign-in activity related to your lab work.

1. Open the Microsoft Entra admin center.
2. Navigate to **Entra ID** > **Monitoring & health** > **Sign-in logs**.
3. Use the available filters to narrow sign-in activity to your user account and the date/time range of the lab.
4. Select one of your sign-in events and review its details.

## Wrap-up

You used Conditional Access Policy Plans to organize shared conditions and separate rules, reviewed Dynamic Agent Risk in Microsoft Entra and Microsoft Defender, created report-only protections for high-risk agent identities and assistive agents, and explored how to restrict protected agent actions.
