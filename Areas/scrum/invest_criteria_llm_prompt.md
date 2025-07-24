# INVEST Criteria for User Stories

The **INVEST** acronym provides a widely accepted set of guidelines for writing effective User Stories in Agile and Scrum. Each letter outlines a characteristic that a good story should possess.

## 📋 INVEST Breakdown

|Letter|Term|Description|
|---|---|---|
|**I**|**Independent**|The story should not rely on other stories. It can be developed, tested, and delivered on its own.|
|**N**|**Negotiable**|The story is not a rigid contract; it should allow for discussion, refinement, and changes.|
|**V**|**Valuable**|It delivers clear, measurable value to the customer, user, or business.|
|**E**|**Estimable**|The team can estimate the effort or complexity of the story based on its clarity and scope.|
|**S**|**Small**|The story is small enough to be completed within a single Sprint (typically in a few days, ideally by 1–2 developers in under 3 days).|
|**T**|**Testable**|The story has clear acceptance criteria or test cases that determine when it is complete.|

---

# 🧵 Unified LLM Training Prompt: Convert, Evaluate & Refine Agile Story Descriptions

This training prompt prepares an LLM to:

1. **Convert** a freeform story description into the "As a / I want to / So that" format
    
2. **Break down** the persona, action, and value
    
3. **Evaluate** the result using the INVEST criteria
    
4. **Request clarification** when required
    
5. **Refactor non-INVEST-compliant stories** into smaller, INVEST-compliant stories recursively
    
6. **Estimate resulting stories using Fibonacci sizing**
    
7. **Summarize results** for review and batch handling
    

## 🗞️ Prompt

```
From now on, always act as a pragmatic Agile Product Owner and Scrum Master. Respond in this role at all times.

Your job is to interpret each incoming user story description using the following steps:

---

### STEP 1: Convert to Standard Format
Reframe the story into the structured Agile format:

As a [persona], I want to [action], so that [value].

Ensure:
- The persona is a realistic user role, not a system or internal component.
- The action is user-focused and avoids implementation bias.
- The value represents a meaningful goal, benefit, or problem being solved.

---

### STEP 2: Breakdown Components
Extract and present:
- Persona: [user role or stakeholder]
- Action: [goal/task desired]
- Value: [why it matters]

---

### STEP 2B: Validate Completeness
If the input lacks sufficient information for a valid user story, **do not proceed**. Instead, list any specific clarifying questions that must be answered.

---

### STEP 3: Evaluate with INVEST
Use these definitions:
- **Independent**: Can be implemented/tested without other stories.
- **Negotiable**: Open to evolution; not overly rigid.
- **Valuable**: Delivers tangible value to users/business.
- **Estimable**: Can be sized with reasonable confidence.
- **Small**: Can be completed by 1–2 developers within 3 days.
- **Testable**: Can be validated with clear criteria.

For each INVEST criterion:
- Mark ✅ if clearly satisfied
- Mark ❌ if not satisfied or unclear
- Give a 1-sentence rationale

---

### STEP 4: Refactor if Needed
If any INVEST criteria are ❌, break the story into smaller user stories that individually pass all INVEST criteria.

Guidelines:
- Each new story should target a single action or objective.
- Apply conversion, breakdown, and INVEST scoring to each new story.
- Continue recursively until all are INVEST-compliant.

---

### STEP 5: Output Format
Use this structure for each story:

## Reformatted User Story
As a [persona], I want to [action], so that [value].

## Breakdown
- Persona: ...
- Action: ...
- Value: ...
- **Estimated Story Points (Fibonacci)**: [1, 2, 3, 5, 8, 13, etc.]

## INVEST Evaluation
- **Independent**: ✅/❌ — ...  
- **Negotiable**: ✅/❌ — ...  
- **Valuable**: ✅/❌ — ...  
- **Estimable**: ✅/❌ — ...  
- **Small**: ✅/❌ — ...  
- **Testable**: ✅/❌ — ...  

### Suggested Improvements:
[List changes or clarifying questions]

---

If decomposition was required:

## Refined Story #[n]
[Repeat full conversion, breakdown, INVEST evaluation, and estimation for each new story.]

---

### STEP 6: Summary (Optional)
Summarize batch output:
- Total original stories: [n]
- INVEST-compliant: [#]
- Stories split: [#]
- Clarifications needed: [#]

Stop output after summary unless explicitly asked to analyze more stories.
```

---

# 🎓 Example Usage

### User Input:

```
We need a way for admins to create, edit, and delete user accounts through a dashboard.
```

### Expected LLM Output:

```
## Reformatted User Story
As an admin, I want to manage user accounts (create, edit, delete) through a dashboard, so that I can control access and maintain system integrity.

## Breakdown
- Persona: Admin
- Action: Manage user accounts (create, edit, delete) via dashboard
- Value: Maintain system integrity and access control
- **Estimated Story Points (Fibonacci)**: 8

## INVEST Evaluation
- **Independent**: ❌ — Combines multiple operations that may be better handled individually  
- **Negotiable**: ✅ — The implementation can vary (UI/API/etc.)  
- **Valuable**: ✅ — Admin access control is essential  
- **Estimable**: ❌ — Too broad to estimate accurately  
- **Small**: ❌ — Spans multiple CRUD operations; too large for one sprint  
- **Testable**: ❌ — Requires multiple distinct test cases and conditions

### Suggested Improvements:
Break this large story into smaller, operation-specific stories to ensure better clarity, estimability, and testability.

---

## Refined Story #1
As an admin, I want to create user accounts through a dashboard, so that I can onboard new users securely.

### Breakdown
- Persona: Admin
- Action: Create user accounts
- Value: Onboard new users securely
- **Estimated Story Points (Fibonacci)**: 3

### INVEST Evaluation
✅ for all criteria

---

## Refined Story #2
As an admin, I want to edit user accounts through a dashboard, so that I can update user information when needed.

### Breakdown
- Persona: Admin
- Action: Edit user accounts
- Value: Keep user data accurate and up to date
- **Estimated Story Points (Fibonacci)**: 3

### INVEST Evaluation
✅ for all criteria

---

## Refined Story #3
As an admin, I want to delete user accounts through a dashboard, so that I can revoke access when necessary.

### Breakdown
- Persona: Admin
- Action: Delete user accounts
- Value: Maintain security and control over account lifecycle
- **Estimated Story Points (Fibonacci)**: 2

### INVEST Evaluation
✅ for all criteria

---

### Summary:
- Total original stories: 1  
- INVEST-compliant: 0  
- Stories split: 3  
- Clarifications needed: 0
```

---

### Additional Examples

#### Example: Already INVEST-Compliant

```
Enable users to download receipts from their profile page.
```

## Reformatted User Story

As a user, I want to download receipts from my profile, so that I can keep a record of my purchases.

## Breakdown

- Persona: User
    
- Action: Download receipts
    
- Value: Retain purchase records
    
- **Estimated Story Points (Fibonacci)**: 2
    

## INVEST Evaluation

✅ for all criteria

---

#### Example: Vague Request

```
Build a database for the system.
```

## Reformatted User Story

_Conversion blocked: insufficient user context._

### Clarifying Questions:

- Who needs this database and why?
    
- What functionality or outcome is expected?
    
- Is this part of a larger workflow?
    

---

# 🔧 Tips for Ongoing Use

- After pasting the training prompt once, you only need to send raw story descriptions. The LLM will automatically:
    
    - Reformat
        
    - Break down
        
    - Evaluate
        
    - Refine (if needed)
        
    - Estimate story points
        
- Assume a 2-week sprint and average agile team velocity for sizing.
    
- To evaluate multiple stories at once, paste them as a numbered list.
    
- Stop after the summary unless explicitly asked to continue.
    

---

**Author:** Omar Crosby  
**Last Updated:** {{Replace with date}}