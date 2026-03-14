# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?

The game appeared to run well and seemed stable the first time I tried it, but when I began testing edge cases and replaying the game, bugs started to become very apparent.

- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").

Two concrete bugs I noticed at the start was that the attempts left weren't tracked correctly and the bounds of the available guesses had no failsafe. When I got to one attempt left, the website claimed I was out of attempts, contradicting the statement. In addition, the website says I can only guess between numbers 1 to 100, but allows me to input numbers outside of those bounds such as negative numbers or numbers in the hundreds. Another weird bug is that the hint saying "Go lower" or "Go higher" was inconsistent if the number was out of bounds, especially for out of bound numbers (Go higher for 100, Go lower for 101). The last major concrete bug I initially spotted was that the "New game" button did not work if you ran out of attempts. 

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?

I used VSCode's Claude AI chat extension for this project.

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).

One example of an AI suggestion that was correct was how it fixed the issue where the game would always say "Guess a number between 1 and 100" despite the fact that the range of numbers varies on difficulty (easy difficulty is 1 to 20). The AI identified the line of code where this mistake occurred and offered a fix to it by adding the high and low variables in the string. I verified the results by checking each difficulty and seeing if the string changed based on that.

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

The most frustrating bug that the AI suggestions weren't completely helpful with was fixing the bug where it would claim I have one attempt left but also says I'm out of attempts and lost the game. It kept suggesting changes that didn't work, until I realized there was another bug that didn't decrement the "attempts left" on your first attempt. After fixing that bug, both bugs disappeared. I verified the results by running out of attempts for each difficulty and seeing if when I had 0 attempts left, the game would end.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?

I decided a bug was really fixed by thinking through the specific scenario that triggered it and confirming the correct behavior appeared. For the hint bug, I checked that guessing too high showed "Go LOWER" and guessing too low showed "Go HIGHER." For the rerun bug, I verified the hint message stayed on screen after a wrong guess instead of disappearing immediately. I also ran pytest to confirm the logic held up in code, not just in the browser.

- Describe at least one test you ran (manual or using pytest)
  and what it showed you about your code.

I used pytest to run "test_too_high_message_says_go_lower", which calls "check_guess(60, 50)" and asserts that the outcome is "Too High" and the message contains "LOWER". This test showed me that the original hint messages were backwards, as the code was telling players to go higher when they were already too high. Running pytest also revealed that the existing starter tests were broken because they compared the full return value (a tuple) to a plain string, so I fixed those too.

- Did AI help you design or understand any tests? How?

Claude Code helped me design the new pytest case by identifying the exact bug to target which was the backwards hint messages in "check_guess" and suggesting a simple test: one guess, one secret, assert both the outcome and the direction of the hint. This helped me understand that a good unit test should pin down one specific behavior rather than testing everything at once.

---

## 4. What did you learn about Streamlit and state?

- In your own words, explain why the secret number kept changing in the original app.

The secret number appeared to keep changing because Streamlit reruns the entire script from top to bottom on every interaction such as clicking a button or typing in a box. In the original app, "random.randint" was called at the top level without being guarded by a session state check, so a fresh random number was generated on every rerun. The fix was wrapping the secret generation in "if 'secret' not in st.session_state", so it only runs once per game session.

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

If I were to explain Streamlit "reruns" and session state to a friend, I would tell them to imagine every time they click a button on a webpage, the entire page reloads from scratch, with all their variables reset to zero. I would say that is how Streamlit normally works. I would explain how session state isn't affected by the reloads, as you write a value to it once it remains the next time the page reruns. Without session state, the game forgets the secret number, the attempt count, and the score every single time you interact with it.

- What change did you make that finally gave the game a stable secret number?

The key change was guarding the secret with "if 'secret' not in st.session_state" so "random.randint" only runs when the game first loads or after a New Game reset. Additionally, the New Game button was fixed to explicitly write a new value into st.session_state.secret, ensuring the secret only changes when the player intentionally starts a new game.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?

Writing a focused pytest case immediately after identifying a bug is a habit I want to keep. It forces you to be specific about what "fixed" actually means, and it gives a safety net so the same bug can't quietly come back later. In this project, writing `test_too_high_message_says_go_lower` made me describe the expected behavior exactly before I even confirmed the fix worked.

- What is one thing you would do differently next time you work with AI on a coding task?

I would verify AI suggestions against the actual running code sooner rather than accepting them without review. In this project, Claude correctly identified several bugs, but I learned that AI can also confidently describe a bug that doesn't match the real cause. Running the code and reading the error output myself would have saved time on a few of the trickier fixes.

- In one or two sentences, describe how this project changed the way you think about AI generated code.

This project showed me that AI generated code can look clean and reasonable on the surface while hiding subtle bugs that only appear once I run the code. I now treat AI generated code the same way I'd treat code from any other source: read it critically, test it, and don't assume it's correct just because it compiles and runs.
