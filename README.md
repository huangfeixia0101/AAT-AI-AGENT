

https://github.com/user-attachments/assets/132672ee-49d1-4a27-ae6f-09d26f51ce2e

# Putting an AI Agent into a 40-Person Test Organization: Hard-Won Lessons and Real Numbers

Start with three numbers:

- **200** — internal registered users of this system today, all front-line test and development engineers;
- **2–3 days → 10 minutes** — the time it used to take an engineer to hand-write an executable test case from a packet capture, versus what the Agent takes now with a human confirming;
- **98%** — the correctness rate of generated test cases. And "correct" doesn't mean "looks right": every case is wired into CI/CD and actually runs, then verified by humans.

I'm not here to share an AI vision. Of the "AI transformation" talks out there, 90% stop at slides and demos; most of the rest are chatbots. This article is about what happens when AI gets wired into a software delivery pipeline that actually runs — with numbers, and with costs.

First, my position: I'm not a machine learning engineer. I run a 40-person test organization at a multinational telecom company — delivery across time zones — and my daily tools are process, CI/CD, and the 3GPP protocol stack. Everything in this article happened on that production line.

## 1. Our reality: when a feature lands

Telecom software delivery has a defining trait: the protocol is fixed, the change is not.

Every new feature triggers the same four-item checklist for the team: **analyze the protocol and requirements, design and run tests, write documentation, learn the new material.** For an individual, every one of those is pure time burn. Testing one feature used to cost the team about **10 person-days**.

Do the arithmetic on 40 people across multiple countries and time zones: how much of a year goes to "repeated labor" instead of "judgment"?

## 2. The starting point: one server and two people

At the end of 2025, open-source agent tools exploded. I started using one at home immediately and spent the entire Spring Festival holiday talking to an agent on my phone. Somewhere in those two weeks, two things became clear to me: this is real; and in work like ours — process-heavy, protocol-deep, documentation-mountainous — it would be worth more than almost anywhere else.

After the holiday, I decided to **build one myself** and bring it into our delivery process.

The kickoff was embarrassingly simple for a big company: I drew a flowchart, walked the managers through it together with our existing CI/CD system, got **one server** approved, and started.

Some background matters here: our CI/CD system — Vue frontend, Flask backend, PostgreSQL, Jenkins pipelines, managing test cases, environment resource pools, packages and results — was something I proposed and led years earlier. Nobody thought it was a big deal at the time; it was just an internal tool. Later, it became the foundation of this entire story.

The core team was two people: **me** — architecture, process design, and the Agent itself, which I wrote alone (Svelte + FastAPI + SQLite); and **one colleague**, the long-time CI/CD maintainer, who handled the backend integration. No budget committee, no cross-department task force, no "AI Transformation Office."

## 3. The mindset shift: don't issue AI an "assistant" badge

The easiest move would have been giving everyone on the team a chat assistant. We didn't take that road.

Because what I wanted wasn't "someone writes code a bit faster" — it was **automating the entire delivery chain**: requirement comes in → generate the test documentation → generate test cases → generate test code → run them for real in CI/CD → analyze results automatically → humans decide.

I came to call this an **AI Harness** rather than an AI Agent: the Agent is just the conversational brain; the Harness is what wires that brain into the whole pipeline and the full CI/CD machinery. **People move from executors to decision-makers** — the system runs, humans review, humans call it. That's where human-in-the-loop actually belongs: not as a safety net, but as a design principle.

The Harness has a second user group: customers of our products. They used to dig through product documentation to get anything done; now they ask the Agent in natural language, or just toss it a packet capture file.

## 4. Architecture, and the question you can't dodge: does the data leave the intranet?

The architecture in one sentence: **a self-built Agent in the middle, one hand holding knowledge and users, the other holding CI/CD.**

- **Inputs**: natural language, packet captures, Jira requirements, code repositories;
- **Inside the Agent**: RAG over our product knowledge and predefined reference test case library; Skills for generating test docs / cases / test code / reports; Tools for running tests, ticketing, configuration;
- **Execution**: generated cases go straight into the CI/CD environment and actually run; results flow back;
- **Models**: a self-built GPU cluster running open-source models (GLM, DeepSeek), behind an OpenAI-compatible API.

That last line is the point. Every team doing AI hits the same wall: customer packet captures, product data — do you dare feed that to a large model? Our answer: **we host the models ourselves; the data never leaves the intranet.** The company bought GPUs and deployed open models as the tide turned; but "deciding where the model lives" is homework every team has to do before anything else.



https://github.com/user-attachments/assets/6a81ea07-78b8-4443-85dd-c068a60b5737



*Figure 1: The AI Harness end-to-end — a self-built Agent wired into CI/CD, data stays on-prem, human-in-the-loop throughout.* 

## 5. Three hard battles

### Battle one: natural language → test cases. What 85% actually means

A user describes a test scenario in plain language; the Agent generates an executable test case.

Our bar for "usable" is a triple gate: **generated without edits, passes review, actually runs.** The 85% first-shot rate is the share that clears all three gates. The other 15%? Human in the loop — the user tells the Agent what's wrong, it fixes only that part and keeps the rest. That's why the "edit" button exists in the Harness.

### Battle two: packet capture → test case. From 2–3 days to 10 minutes

This is the chain that says the most. In telecom, packet captures (PCAP) are the customer's native language: when something breaks, what arrives is a bag of data.

An engineer used to manually analyze the protocol interactions in one PCAP, design a case and write it into executable form: **2–3 days**. Now the PCAP goes to the Agent, and **10 minutes** later there's a case — correct **98%** of the time, by the same standard: really run in CI/CD, then human-verified.

From first line of code to production use, this feature took **6 weeks**. No single week was dramatic — our method was the industry's most boring rule: **ship a crude version first, then iterate without mercy.** Version one was ugly but stable. And once version one went live, feedback and numbers started compounding.

### Battle three: the efficiency ledger, counted in person-days

The metric is real team person-days: **testing one feature dropped from roughly 10 person-days to 4 — minus 60%.**

There's no magic. Once case generation, documentation, and result analysis are eaten by the Harness, people move from "writing" to "judging." The efficiency gain is the outcome; the role shift is the cause.

## 6. The organization is harder than the tech: the best marketing is users who grow a mouth

The technical battles won, the real test began: why should 40 people believe you?

My method was dumb but it worked: **find one volunteer per team**, let them use it internally, and improve it week by week on their feedback. I never held an all-hands pitch and never issued a single mandate.

The turning point came from them: the ones who used it and actually saved time started pulling colleagues in. Then a user **self-organized an internal session, presented the system to a 700-person group, and invited me to give the technical talk.** From there, adoption stopped being my problem — **the best marketing is the first users growing a missionary's mouth.**

Of course there was anxiety. Test engineers aren't blind: if cases are generated, what am I for? My answer has stayed the same: **nobody can stop technical progress; rather than panic, be among the first to master it — competition rewards the early hands, not the irreplaceable.** Those 200 registered users are 200 people who went first.

## 7. The costs and the luck deserve their say

A few things that don't fit a success story:

- **Efficiency went up; the organization got smaller — and I've decided to say this straight.** Six test teams became three. When case generation, documentation, and result analysis are absorbed by the Harness, the demand for "executing hands" does go down. That's not science fiction; it's happening now. The other half is just as true: the people who remain are no longer executors but decision-makers — what gets filtered out is never "people replaced by AI," it's "people who refuse to go first." Anyone pushing AI adoption owes you this page, not just the efficiency slide.
- **The crude first version took every insult.** Six weeks to production only works if you accept that version one is ugly.
- **Luck exists, but it favors the ones who stayed in the game.** The company buying GPUs and deploying open models was the tide; but I could build this system because I had run CI/CD for years, accumulated eighteen years of protocol knowledge, and spent every role thinking about how to make the pipeline faster. Opportunity is what happens when a new tool shows up and you happen to be standing on the pipeline it's about to reshape — and you actually build.

## 8. A checklist for anyone walking this road

1. **Don't build an assistant; build a Harness.** The value isn't the conversation, it's the pipeline connection.
2. **Fix your CI/CD first.** Without a programmable delivery pipeline, generated artifacts have nowhere to land.
3. **Keep the models on-prem.** If the data-security question fails, everything else is talk.
4. **Let version one be ugly.** Ship, iterate; feedback beats perfect design.
5. **Find one seed user per team.** Don't push; let word of mouth grow.
6. **Human-in-the-loop is designed, not an apology.** Give users the power to fix only what's wrong.
7. **Every number needs a real acceptance standard.** "Actually ran + human-verified" beats "feels accurate" a hundred to one.
8. **Person-days are the only metric nobody argues with.** Do the ledger before telling the story.
9. **Don't sugarcoat the anxiety.** "Be among the first to master it" is more honest — and more useful — than "AI won't replace you."
10. **Stay in the game.** You can't predict which tool explodes next, but you can be the one person qualified to use it when it does.

---

*The author put an AI Agent into a 40-person software delivery organization. Eighteen years in telecom software delivery; his self-built AI Harness serves 200 engineers in production.
GitHub: github.com/huangfeixia0101 | Email: huangfeixia0101@163.com*
