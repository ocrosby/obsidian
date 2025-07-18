The first thing that you need to know about vim is: Vim is a modal editor. This means that vim has different modes that can be used to interact with it.  If you are asking why, remember that when the original vi editor was created in 1976, there were no graphical interfaces (GUI) or mouse, and even displays were not as powerful as todays. Now, in order to edit text or navigate around, you need to switch between the different modes. Lets understand the different modes available in vim.

---

- [[Normal Mode]]: (a.k.a Command Mode) here is where you do things like copy, paste, find, or replace, and execute commands like (:w to save or :q to quit).
- `Visual Mode`: here is where you can select text.
- [[Insert Mode]]: here is where you can edit your text.

An important tip: look at the bottom of vim's screen and you will see which mode is currently in use. You can see `INSERT` or `VISUAL` for the respective modes, or when you don't see anything it means you are in `NORMAL/COMMAND` mode.

