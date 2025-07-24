# INVEST Criteria for User Stories

The **INVEST** acronym provides a widely accepted set of guidelines for writing effective User Stories in Agile and Scrum. Each letter outlines a characteristic that a good story should possess.

## 📋 INVEST Breakdown

|Letter|Term|Description|
|---|---|---|
|**I**|**Independent**|The story should not rely on other stories. It can be developed, tested, and delivered on its own.|
|**N**|**Negotiable**|The story is not a rigid contract; it should allow for discussion, refinement, and changes.|
|**V**|**Valuable**|It delivers clear, measurable value to the customer, user, or business.|
|**E**|**Estimable**|The team can estimate the effort or complexity of the story based on its clarity and scope.|
|**S**|**Small**|The story is small enough to be completed within a single Sprint (typically in a few days).|
|**T**|**Testable**|The story has clear acceptance criteria or test cases that determine when it is complete.|

---

# 🧠 LLM Prompt: Evaluate Stories Against INVEST Criteria

The following prompt can be used to guide a Large Language Model (LLM) to assess whether a single story—or a batch of user stories—adhere to the INVEST principles.

## 🗞️ LLM Prompt Template

```
You are an expert Agile coach and Scrum Master. Analyze the following user story (or stories) and determine whether it adheres to the INVEST criteria: Independent, Negotiable, Valuable, Estimable, Small, and Testable.

For each story, provide:
1. A binary INVEST scorecard (✅ or ❌ for each criterion)
2. A brief explanation for each score
3. Suggestions for improvement if any criteria are not met

User Story (or Stories):

[Insert user stories here]

Use the following format in your response:

## Story #[Number or Title]
**Independent**: ✅/❌ — [explanation]  
**Negotiable**: ✅/❌ — [explanation]  
**Valuable**: ✅/❌ — [explanation]  
**Estimable**: ✅/❌ — [explanation]  
**Small**: ✅/❌ — [explanation]  
**Testable**: ✅/❌ — [explanation]  

### Suggested Improvements:
[List suggestions if applicable]
```

## 🔁 Example Use

```
[Insert Story Here]
As a user, I want to reset my password so that I can regain access to my account if I forget it.
```

Expected output:

```
## Story #1
**Independent**: ✅ — This story does not depend on others.  
**Negotiable**: ✅ — Scope can be discussed (e.g., password strength, recovery flow).  
**Valuable**: ✅ — Provides clear user value.  
**Estimable**: ✅ — Complexity can be estimated based on known requirements.  
**Small**: ✅ — Easily implementable within a sprint.  
**Testable**: ✅ — Acceptance criteria could include success and failure cases.

### Suggested Improvements:
None needed. The story fits INVEST well.
```

---

# 📦 Use Cases

- Sprint Planning Quality Gate
    
- Automated LLM Review via API
    
- Agile Training Material
    
- Definition of Ready Enforcement
    

---

# 📄 Overview: "As a / I want to / So that" Format

## Purpose

This structured format helps ensure clarity and context when capturing user stories in Agile software development. It emphasizes **who** the user is, **what** they want, and **why** they want it.

## Format Explanation

```
As a [type of user], I want to [perform an action], so that [I can achieve a goal].
```

### Components:

- **As a**: Identifies the user role or persona (e.g., admin, customer, editor).
    
- **I want to**: Specifies the intent or task the user wants to perform.
    
- **So that**: Describes the value or reason behind the action.
    

## Benefits

- Encourages user-centric thinking
    
- Ensures traceability to business value
    
- Simplifies test case generation (linked to "so that")
    
- Enhances team communication and shared understanding
    

---

# 🧵 LLM Prompt: Convert Story Description to "As a / I want to / So that"

Use the following prompt to instruct an LLM to rewrite story descriptions into the standard user story format.

## 🗞️ Prompt Template

```
You are a skilled Agile Product Owner. Your task is to rewrite the following user story description into the standard "As a / I want to / So that" format.

Also identify:
- The user persona (or role)
- The goal or functionality requested
- The business or user value being delivered

Input:
[Insert raw story description here]

Format the output like this:

## Reformatted User Story
As a [persona], I want to [action], so that [value].

## Breakdown
- Persona: [Extracted persona]
- Action: [What they want to do]
- Value: [Why they want it]

If the original description lacks sufficient clarity to generate a valid user story, note what additional information is needed.
```

---

**Author:** Omar Crosby  
**Last Updated:** {{Replace with date}}