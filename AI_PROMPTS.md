# Generative AI Disclosure

Using AI tools for this challenge is allowed. Be honest here — an accurate
"I used AI heavily" is worth more to us than an inaccurate "I did not".

**Did you use AI tools?** (yes / no): `yes`

**Which ones?** (e.g. ChatGPT, Claude, Copilot, Cursor): `Gemini, ChatGPT`

> If you answered **no**, write "N/A" in sections 1 and 2 and skip to
> section 3. That is a perfectly good answer.

---

## 1. Key prompts

List the main prompts you used. Paraphrasing is fine.

1. `Explain the dpkg failure in the proxy build logs. Also, from a security standpoint, why is using a full Ubuntu base image with tools like vim, git, and htop problematic for a small proxy container, and how does combining apt-get update and install in a single RUN layer affect SHA256 caching?`

2. `After adding a healthcheck to handle the backend's 8-second config loading delay, isn't a 20-second maximum retry window too long for a server to wait in a real environment? How does Docker Compose behave once the /health endpoint responds?`

3. `Since the test script already passes on my local Windows machine, how can I verify that the permission change was applied successfully, how would we have noticed this CI issue by ourselves in the real world if it wasn't mentioned in the instructions?`

---

## 2. Where the AI got it wrong

During Task 4, my Git Bash terminal had a glitch and didn't display the "git ls-files -s" output right away. The AI incorrectly assumed I was on the wrong branch, and even after verifying I was on the correct one, it just told me to move on and blindly "trust" that the permissions were applied. I knew verifying is critical, so instead of trusting it blindly, I ran the command multiple times and resized my screen until the output finally appeared so I could confirm the fix myself.

> ...

---

## 3. Anything in your submission you could not fully explain?

I took the time to research and learn the concepts behind every problem I solved. I believe I could comfortably discuss most of the topics related to this submission.

> ...
