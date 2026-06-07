# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?

The game had a title, a difficulty selector, and a text input for guessing. It looked mostly complete at first glance, but as soon as I started playing, things felt wrong. The hints were sending me in the wrong direction, and even when I guessed the correct number I couldn't win consistently.

- List at least two concrete bugs you noticed at the start
  (for example: "the hints were backwards").

  - The hints were backwards, when my guess was too high, the game told me to go even higher.
  - The Hard difficulty was actually easier than Normal because it used a smaller number range (1–50 vs 1–100).
  - The debug expander always showed the secret number, making it impossible to play fairly.
  - On even-numbered attempts the secret got cast to a string, so correct guesses didn't register.

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Guess 80 when secret is 40 | Hint says "Go LOWER" | Hint says "📈 Go HIGHER!" sending me the wrong way | No error; wrong message returned by `check_guess` |
| Select "Hard" difficulty | Range wider than Normal (1–100) | Range is only 1–50, easier than Normal | No error; `get_range` returns `(1, 50)` |
| Guess the correct number on an even attempt | Game says "🎉 Correct!" and I win | Game says too high/low and I can't win | No error; `"40" == 40` is `False` (string vs int comparison) |
| Open "Developer Debug Info" expander | Should be hidden or gated during normal play | Secret number is always shown in plain view | No error; `st.write("Secret:", ...)` always runs |
| Click "New Game" on Hard | New secret picked from 1–50 | Secret always picked from 1–100, ignoring difficulty | No error; `random.randint(1, 100)` hardcoded |

**Hint for the next student:** Don't try to fix everything by reading the code top to bottom. Instead, play the game first and write down exactly what you did, what you expected, and what actually happened (like the table above). 

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?

I used Claude Code throughout this project to help identify bugs, explain confusing code, and suggest fixes.

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).

Claude correctly identified that `logic_utils.py` was full of `NotImplementedError` stubs and that the tests would fail because of it. I verified this by running `pytest` and seeing the import error before moving the functions over.

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

Claude initially suggested naming the virtual environment `venv` with the command `python3 -m venv venv`, which is a generic name that doesn't work well when you have many projects. I pointed out that a more descriptive name would be easier to manage, so I named it `codepath` instead using `python3 -m venv codepath`. It was a small thing, but it showed me that AI gives generic advice by default and doesn't always account for your personal setup.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?

I tested each fix either by running the app and playing through it manually, or by running `pytest` for the logic functions. A bug was only considered fixed when the behavior matched what I expected, for example, guessing too high now correctly says "Go LOWER."

- Describe at least one test you ran (manual or using pytest)
  and what it showed you about your code.

I ran `pytest tests/test_game_logic.py` after moving the functions into `logic_utils.py`. At first it failed with a `ModuleNotFoundError` because pytest couldn't find the module. Adding a `conftest.py` to the project root fixed the path issue, and all three tests passed after that.

- Did AI help you design or understand any tests? How?

Yes, Claude explained that the tests were failing because `check_guess` returns a tuple `("Win", "🎉 Correct!")` but the tests were comparing against just the string `"Win"`. The fix was to use `[0]` to extract only the outcome from the tuple.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

Think of Streamlit like a whiteboard that gets erased and redrawn every time you interact with it. Session state is like a sticky note on the side that survives each erase — whatever you write there stays around between reruns. Without it, everything resets on every click.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?

I want to keep separating logic from UI, putting game rules in their own file (like `logic_utils.py`) made the code much easier to test and understand. Running `pytest` to catch logic bugs before even opening the app is a habit I'll carry forward.

- What is one thing you would do differently next time you work with AI on a coding task?

I would ask the AI to explain *why* a bug exists, not just what to change. Understanding the root cause helped me recognize similar patterns in other parts of the code, rather than just applying a fix blindly.

- In one or two sentences, describe how this project changed the way you think about AI generated code.

This project showed me that AI-generated code can look correct on the surface but have subtle, compounding bugs underneath. I now treat AI output as a starting point that needs to be read carefully and tested, not something that can be trusted without review.
