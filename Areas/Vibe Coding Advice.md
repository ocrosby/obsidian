## Where to start

- Pick Up The Tools
- Version Control
- Write Tests
- Bug Fixes
- Documentation
- Functionality
- Choose Correct Stack
- Refactor Frequently
- Keep Experimenting

Tools for new coders

- Replit
- Lovable

These tools don't really work well for experienced software engineers

Tools for experienced software engineers

- windsurf
- cursor
- claude code

## Work with the LLM to write a comprehensive plan

Put your plan in a markdown document inside your repo and keep referring back to it.

After you've created a first draft of the plan go through it and delete or remove things you don't like.

Mark certain features as "won't do" or "too complicated"

Keep a section of ideas for later to tell the LLM that we've considered this but it's out of scope for now. 

Once you have this plan work with the LLM to implement it section by section. Explicitly say "let's just do section 2 for now".  Check that it works, run your tests then commit your changes.
Then have the LLM go back to your plan and mark "section 2" as complete.

## Use Version Control

Don't be afraid to `git reset head hard`

## Write Tests

Use the LLM to write end to end tests. They tend to focus on the unit level by default.

## Bug Fixes

Copy paste the error message back into the LLM.

For more complicated bugs, ask the LLM to think through 3/4 possible causes.

Don't make multiple attempts without doing a git reset as the LLM's tend to add cruft.

If in doubt, switch models. (claud sonnet 3.7, one of the open AI models, gemini)

If you do find the source of a bad bug reset all of the changes and give the LLM specific instructions on how to fix that precise bug on a clean codebase.

W



