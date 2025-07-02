# LLM-Assisted Project Development Framework

A comprehensive approach to building software projects that combines structured planning with AI-powered development practices and practical idea validation.

> "Solve a painful problem for a niche audience. Everything else is noise." – JKI Playbook

## Phase 1: Foundation & Planning

### Step 1. Start from your goal (with LLM collaboration and validation)

Get crystal-clear on:
- Why am I making this project?
- Who is this project for?
- What is going to make it valuable?

**Work with the LLM to refine your vision using Opportunity Discovery Methods:**
1. **Scratch Your Own Itch**: Find inefficiencies, annoyances, or repetitive tasks in your daily life or workflow
2. **Market Gap Analysis**: Look for underserved segments, poor reviews, or gaps in tool ecosystems
3. **Niche Community Monitoring**: Track communities with passionate users (Reddit, Discord, GitHub) for unaddressed needs
4. **Emerging Tech Leverage**: Build lightweight tools using new APIs, platforms, or under-supported technologies
5. **No-Code/Low-Code Gaps**: Turn complex workflows into simple UIs for non-developers
6. **B2B Friction Finder**: Automate repetitive tasks within verticals: invoices, analytics, internal tooling
7. **Redundancy → SaaS**: Anything handled by spreadsheets, email, or copy/pasting can likely be automated

**Apply the Practicality Filter before proceeding:**

| Question | Yes/No |
|----------|--------|
| Does this solve a painful or frequent problem? |   |
| Would we pay for this if we had this problem? |   |
| Can we describe the target user clearly? |   |
| Can we build a simple version in 1–2 weeks? |   |
| Is someone *already* searching for this solution? |   |
| Can we test demand without writing code? |   |

**Only proceed with 5+ "Yes" answers.**

This avoids your project from becoming directionless.

**Mission: Build small, useful tools** that solve real problems and can generate income or traction with minimal maintenance.
> No passion projects without validation. Practicality comes first.

**Build something digestible**
**Emphasize clean understandable code**

### Step 2. Collaborative User Story Development with Idea Validation

Work with the LLM to write comprehensive user stories:

Examples:
- The user should be able to upload a file
- The user should be able to create an account
- The user should be able to view a dashboard

**Apply the Idea Scorecard** to rate each core feature:
- **Pain Level (1–5)**: Hair-on-fire or minor annoyance?
- **Reach (1–5)**: How many people are affected?
- **Speed to MVP (1–5)**: How quickly can we validate it?
- **Revenue Potential (1–5)**: Is there a real monetization path?

**Ideas below 14/20 go to the icebox.**

**LLM Enhancement Process:**
- Have the LLM generate initial user stories based on your validated goal
- Review and refine with the LLM, marking some as "won't do" or "too complicated"
- Tag ideas appropriately: `painkiller`, `vitamin`, `automation`, `B2B`, `cool`
- Keep a section of ideas for later features (out of scope for now)

**The Cool-Down Rule**: Welcome "cool" ideas—but only after launching or validating a practical one.
> Sandbox projects are fun. Real-world solutions come first.

Note: These user stories should remain non-technical

You usually want to have 10-20 user stories before getting started.

### Step 3. LLM-Assisted Data Model Design

Sketch out each model and the properties with LLM help:
- Use the LLM to think through all data relationships
- Ask for potential edge cases in your data structure
- Get suggestions for scalability considerations

For example:
#### User's Model
- users may have many posts

#### Post's Model
- posts may have many comments

#### Comment's Model
- comments belong to users & different posts

### Step 4. Create a Comprehensive Plan Document with Validation Structure

**Put your plan in a markdown document inside your repo** and keep referring back to it.

**Use the structured idea template:**

```markdown
### Idea: [Concise title]
**Problem:** What problem are we solving?  
**Target Audience:** Who specifically needs this?  
**Current Workaround:** How is it handled today?  
**Why Now:** Why is this moment ripe for the idea?  
**Potential Moat:** What defensibility or edge exists?  
**1st MVP:** What is the fastest way to test it?  
**Monetization:** SaaS, license, affiliate, etc.?  
**Risks:** Key unknowns or dependencies?  
**Status:** [idea | in-validation | building | launched | shelved]  
```

Work with the LLM to:
1. Nail a Minimum Viable Product (MVP) that validates core assumptions
2. Remove anything that isn't required for validation
3. Focus on what actually matters and gets you to a testable prototype fast
4. Ensure all sections of the validation template are complete

After creating the first draft:
- Go through it and delete or remove things you don't like
- Mark certain features as "won't do" or "too complicated"
- Keep ideas for later features clearly separated in an "Icebox" section
- Update status as you progress through development phases

### Step 5. Design & Architecture

#### Draw a simple wireframe or prototype
- Can be done on paper, iPad, or whiteboard
- Use screenshots with LLM tools for feedback
- Catches user experience issues early

#### Understand project future
Consider how today's decisions impact you in 6 months:
- Is it a hobby project?
- Will it need to scale to thousands of users?

#### Choose the correct stack
- Make the stack fit the project
- Use LLM to evaluate different technology options
- Consider both current needs and future scalability

## Phase 2: Development Setup & Process

### Step 6. Project Skeleton & Environment

Create foundational structure:
- Basic folder structure
- Development environments
- **Version control setup** (essential!)
- Choose appropriate tools:
  - New coders: Replit, Lovable
  - Experienced engineers: Windsurf, Cursor, Claude Code

### Step 7. LLM-Assisted Implementation Strategy

#### Section-by-Section Development with Status Tracking
Once you have your validated plan:
1. Work with the LLM to implement it section by section
2. Explicitly say "let's just do section 2 for now"
3. Check that it works, run your tests
4. **Commit your changes**
5. Update your idea status: `idea` → `in-validation` → `building` → `launched` → `shelved`
6. Have the LLM mark that section as complete in your plan
7. **Prioritize MVPs, not perfection** - aim to validate fast

**Launch > Perfect**: An imperfect MVP that collects feedback is worth more than a "perfect" idea still on the whiteboard.

#### Database and Data Models
- Setting up the database
- Creating data models with LLM assistance
- Use small files and modularity

#### Backend Development
- Build API endpoints systematically
- Use LLM to write comprehensive tests (focus on end-to-end, not just unit)
- If there's another implementation available, point the LLM at it for reference

#### Frontend Interface
- Connect to the backend
- Use screenshots for LLM feedback on UI/UX

## Phase 3: Development Best Practices

### Version Control Mastery
- **Don't be afraid to `git reset --hard HEAD`**
- For bug fixes: reset changes before trying new approaches
- Commit frequently after each working section
- Clean codebase prevents LLM from adding cruft

### Testing Strategy
- **Use the LLM to write end-to-end tests** (they default to unit level)
- Run tests before committing each section
- Test-driven development with LLM assistance

### Bug Fixing Process
1. **Copy paste the error message** back into the LLM
2. For complex bugs: ask LLM to think through 3-4 possible causes
3. **Don't make multiple attempts** without git reset
4. **If in doubt, switch models** (Claude Sonnet 3.7, OpenAI models, Gemini)
5. When you find the source: reset all changes and give LLM specific instructions for a clean fix

### Documentation & Learning
- **Download API documentation** to local folders for better LLM interpretation
- **Use the LLM as a teacher**: get line-by-line explanations of implementations
- Write clear instructions for the LLM in your codebase

### Advanced Techniques
- **Use voice interaction** with tools (look up Aqua Voice)
- **Use screenshots** - copy/paste into most LLM tools
- **Model specialization**:
  - Gemini: better for whole codebase indexing and implementation plans
  - Sonnet 3.7: better for implementing code changes
  - Experiment to find what works best for your workflow

## Phase 4: Iteration & Maintenance

### Project Iteration Strategy with Validation Workflow
- Gradually add features section by section
- **Deploy early and often** - validate assumptions quickly
- Reference your plan document constantly
- Update plan as you learn and priorities shift
- **Review ideas weekly** using the practicality filter
- Tag and categorize features as they evolve
- Maintain the idea lifecycle: Add ideas → Filter → Validate → Build → Launch
- Keep validated, practical projects as priority over experimental ones

### Refactoring & Code Quality
- **Ask the LLM to identify repetitive code** or refactoring candidates
- Refactor frequently to maintain clean, understandable code
- Small, modular files are your friend

### Automated Systems
- Set up automated deployments
- Implement automated testing
- Use LLM to help configure CI/CD pipelines

### Continuous Experimentation
- **Keep experimenting** with different LLM models and approaches
- Try new tools as they become available
- Share learnings and update this framework based on experience

## Key Principles

1. **Validate first, plan second, code third** - ensure real problems before building
2. **Practicality over novelty** - solve painful problems for specific audiences
3. **Commit frequently** - after each working section
4. **Clean codebase always** - reset when things get messy
5. **Test everything** - especially end-to-end functionality and market assumptions
6. **Document as you go** - in your plan and in code
7. **MVP over perfection** - launch fast to collect real feedback
8. **Use LLM as teacher and collaborator** - not just a code generator
9. **Choose tools that fit your experience level**
10. **Version control is your safety net** - use it liberally
11. **Keep experimenting** - but only after shipping something practical
12. **Structured idea evaluation** - use scorecards and filters consistently
13. **Status tracking** - maintain clear progression through development phases

---

## 🔧 Contributing to Your Own Ideas Repository

Consider creating your own ideas repository with:
- GitHub Issues for new idea proposals
- Automated practicality checks using GitHub Actions
- Regular review processes for idea validation
- Clear tagging and categorization systems
- Status tracking from conception to launch

---

*This framework combines structured project planning with modern LLM-assisted development practices and practical idea validation. The best ideas are discovered, not invented. Let the market pull you.*

*Update and adapt based on your experience and emerging tools.*
