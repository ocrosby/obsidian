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

**Author:** Omar Crosby  
**Last Updated:** {{Replace with date}}