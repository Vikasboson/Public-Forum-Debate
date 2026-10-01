# Public Forum Debate: UI Guide for New Users

This guide walks you through the web app (`app.py`): what you see on screen, what
you type, and what happens at each step. No coding knowledge is needed.

## 1. What the app does

You debate the **tool** in a Public Forum (PF) round on a fixed resolution.

- You pick a side (**PRO** or **CON**). The tool argues the opposite side.
- You write your own speeches. The tool answers from a knowledge base (KB) of
  scraped, citation-checked sources, so what it says is backed by real quotes.
  When it has no evidence, it says so rather than inventing a source.
- A debate has **7 rounds** (speeches and crossfires), played in a fixed order.
- When it ends, the whole debate is archived and you can download it.

## 2. Starting the app

From the `Scripts/` folder:

```
source venv/bin/activate
streamlit run app.py
```

Open the URL Streamlit prints (usually http://localhost:8501).

> After anyone edits a file in `pf/`, restart the app. It does not reload those
> files automatically.

## 3. Logging in

The first screen is a login form (**Username** and **Password**). It is a single
shared demo login, not per-user accounts. The credentials are set near the top of
`app.py` (`APP_USERNAME` / `APP_PASSWORD`); ask whoever set up the app, or
look there. Wrong values show "Username is incorrect" or "Password is incorrect".

## 4. Choosing your side

After login, a **Choose your side** popup appears. It can't be dismissed; you have
to pick something.

| Control | What it does |
|---|---|
| **Which side will you argue?** | Choose PRO or CON. The label shows which side the tool takes. |
| **Start debate** | Begins a new debate. If the tool has no prepared case yet, the app builds one from the KB first (a spinner shows this). |
| **Resume that debate** | Only appears if an unfinished debate is saved. Continues exactly where it stopped. |

Starting a new debate while one is saved **discards the unfinished one**.

## 5. Screen layout

```
┌──────────────────────────────────────────────────────────────┐
│ Public Forum Debate                    You: PRO · Tool: CON  │  ← sticky header
│ Resolution: ...                        [progress bar]        │
├───────────────┬──────────────────────────────────────────────┤
│ Sidebar       │ Main page: the current round's transcript    │
│  Mock mode    │   speeches / crossfire as chat bubbles       │
│  Rounds list  │                                              │
│  Contentions  │ ───────────────────────────────────────────  │
│  Tokens       │ Your turn: text box + submit button          │
│  Restart /    │   (or a spinner while the tool is working)   │
│  New debate   │                                              │
└───────────────┴──────────────────────────────────────────────┘
```

**Header (always visible at the top).** The resolution, which side you and the
tool argue, and a progress bar reading "Round X of 7: <stage>".

**Main page.** One round per page. Your speeches appear in one chat style and the
tool's in another, each tagged with the side (PRO in blue, CON in orange), who
spoke ("You" or "Tool"), and a word count against the limit.

**Sidebar.**

- **Mock mode (no model calls)**: a checkbox for testing. It uses fake output and
  makes no AI calls. Leave it off for a real debate.
- **Rounds**: the 7 rounds with status markers. `✓` is done, `▶` is in progress
  (highlighted), and grey means not reached yet. Click a finished or current
  round to jump to it. Speech rounds expand to show the PRO and CON speeches.
- **Contentions**: each side's contentions with a status tag (see section 8).
- **Tokens**: how much model usage the debate has consumed.
- **Restart** and **New debate** buttons (see section 9).

## 6. The seven rounds

| Round | Stage | Who acts |
|---|---|---|
| 1 | Constructive | You and the tool each give one |
| 2 | Crossfire | Questions and answers between you and the tool |
| 3 | Rebuttal | Each side attacks the other's contentions |
| 4 | Second Crossfire | Focuses on the rebuttal |
| 5 | Summary | Each side summarizes |
| 6 | Grand Crossfire | Focuses on the summaries |
| 7 | Final Focus | Each side's closing speech |

When a round finishes, a green banner says "Round N of 7 complete" and a
**Next: Round N+1 →** button appears. The tool never starts the next round on its
own; click it when you're ready. After round 7 the button reads **Finish →**.

## 7. What you do in each round

### Constructive (round 1)

Paste your **entire constructive speech** into the box and click **Submit
constructive**.

- You write it yourself; the tool does not offer or mix in KB arguments for you.
- The app splits your speech into contentions by line range, so **your wording is
  kept exactly**. Any quote you cite is checked against what you pasted.
- It needs at least one contention. If none is found, you'll be asked again.
- Limit: **600 words**.

### Crossfire (rounds 2, 4, 6)

A back-and-forth of questions and answers.

- When it's your turn to **ask**, type a question and click **Ask** (the box can't be empty).
  A hint tells you which contention to press on.
- When it's your turn to **answer**, type your reply and click **Answer**. An empty answer
  counts as no answer.
- After each exchange you choose **Next exchange** or **End crossfire**.
- The tool's answers are labelled either *evidence-backed* or *no evidence --
  reasoning only*. That label matters for scoring (section 8).
- No word limit applies.

### Rebuttal, Summary, Final Focus (rounds 3, 5, 7)

Type your speech and click **Give speech**. Limits:

| Speech | Word limit |
|---|---|
| Constructive | 600 |
| Rebuttal | 500 |
| Summary | 400 |
| Final Focus | 400 |

A live count shows under the box after you submit. If you go over, an **Over the
word limit** popup appears. Click **OK, I'll shorten it**; your text stays in the
box so you can trim it and submit again. The debate won't advance until it fits.
The tool's own speeches are trimmed to fit the same limits.

### While the tool is working

A spinner shows what the tool is doing. Just
wait. The debate is saved after every turn.

## 8. Contention status tags

The sidebar tags each contention based on how the other side treated it:

| Tag | Meaning |
|---|---|
| `HELD` | It was attacked, and at least one attack was answered. A tool answer counts only if backed by evidence. A human answer counts if it isn't empty. |
| `CONTESTED` | It was attacked, but no attack has been answered yet. |
| `CONCEDED` | Never attacked, so the other side let it stand. |

Tip: **answer every crossfire question, even briefly.** An unanswered attack leaves
your contention `CONTESTED`.

## 9. Restart, New debate, and resuming

- **Restart** (sidebar): throws away this debate and starts again from round 1 on
  the same side. A confirmation popup (**Yes, restart** / **Cancel**) protects
  against misclicks. This can't be undone.
- **New debate**: goes back to the side-choice popup so you can pick a different
  side.
- **Closing the tab or a crash is safe.** Progress is saved after every turn. Reopen
  the app and choose **Resume that debate** to continue at the exact turn.
- There is **one debate per corpus**, so two browser tabs on the same app show the
  same debate. A debate started in the terminal (`python -m pf.round debate-run`)
  can also be continued in the UI, and the reverse.

## 10. Finishing a debate

After round 7 and **Finish →**, the **Debate finished** page appears with:

- **Show debate record** / **Hide debate record**: view the full archived debate
  as JSON in the page.
- **Download debate JSON**: saves the archive to your computer.

The UI does **not** judge the debate. To get a score, run from `Scripts/`:

```
python -m pf.judge score --debate corpus/debates/debate_<timestamp>.json
python -m pf.judge latest --corpus ./corpus
```

## 11. Warnings and errors you may see

| Message | What to do |
|---|---|
| "Type something first." | The box is required and empty. Enter text. |
| Yellow notices ([warning], [empty...], [over word limit], [tool speech trimmed]) | Informational. The engine adjusted something, such as trimming a tool speech. |
| Red error with a **Retry** button | A model call failed (throttling, credentials, etc.). Click **Retry**. Nothing is lost. |
| "No knowledge base at ...kb.sqlite" | The KB hasn't been built. See the pipeline steps in `CLAUDE.md`. |
| "No resolution config at ..." | `config/resolution.json` is missing. |
| A Bedrock/credentials error at start | Check `.env` for AWS credentials and `BEDROCK_MODEL_ID`. |

## 12. Quick tips

1. Answer every crossfire question.
2. Keep within the word limits; check the count before submitting.
3. Cite specific evidence in your own speeches. The tool will challenge unsupported claims.
4. Use **Mock mode** to practise the flow without spending model tokens.
5. Use the sidebar **Rounds** list to re-read earlier speeches while writing your next one.
