# LLM-Assisted Project Development Framework

A comprehensive approach to building software projects that combines structured planning with AI-powered development practices.

## Phase 1: Foundation & Planning

### Step 1. Start from your goal (with LLM collaboration)

Get crystal-clear on:
- Why am I making this project?
- Who is this project for?
- What is going to make it valuable?

**Work with the LLM to refine your vision:**
- Use the LLM as a teacher to explore different approaches
- Ask for 3-4 possible architectural directions
- Get the LLM to challenge your assumptions about user needs

This avoids your project from becoming directionless.

**Build something digestible**
**Emphasize clean understandable code**

### Step 2. Collaborative User Story Development

Work with the LLM to write comprehensive user stories:

Examples:
- The user should be able to upload a file
- The user should be able to create an account
- The user should be able to view a dashboard

**LLM Enhancement Process:**
- Have the LLM generate initial user stories based on your goal
- Review and refine with the LLM, marking some as "won't do" or "too complicated"
- Keep a section of ideas for later features (out of scope for now)

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

### Step 4. Create a Comprehensive Plan Document

**Put your plan in a markdown document inside your repo** and keep referring back to it.

Work with the LLM to:
1. Nail a Minimum Viable Product (MVP)
2. Remove anything that isn't required for the app to function
3. Focus on what actually matters and gets you to a working prototype fast

After creating the first draft:
- Go through it and delete or remove things you don't like
- Mark certain features as "won't do" or "too complicated"
- Keep ideas for later features clearly separated

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

#### Section-by-Section Development
Once you have your plan:
1. Work with the LLM to implement it section by section
2. Explicitly say "let's just do section 2 for now"
3. Check that it works, run your tests
4. **Commit your changes**
5. Have the LLM mark that section as complete in your plan

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

### Project Iteration Strategy
- Gradually add features section by section
- **Deploy early and often**
- Reference your plan document constantly
- Update plan as you learn and priorities shift

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

1. **Plan first, code second** - but use LLM to enhance both phases
2. **Commit frequently** - after each working section
3. **Clean codebase always** - reset when things get messy
4. **Test everything** - especially end-to-end functionality  
5. **Document as you go** - in your plan and in code
6. **Iterate quickly** - MVP first, features later
7. **Use LLM as teacher and collaborator** - not just a code generator
8. **Choose tools that fit your experience level**
9. **Version control is your safety net** - use it liberally
10. **Keep experimenting** - the field is rapidly evolving

---

*This framework combines structured project planning with modern LLM-assisted development practices. Update and adapt based on your experience and emerging tools.*
