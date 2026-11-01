<p align="center">
  <img src="docs/img/hero.svg" width="100%" alt="ClaimTrail: finds every claim in an ad, checks it, cites the proof, and routes it to a named person for sign-off">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-building%20in%20public-c2410c" alt="Status: building in public">
  <img src="https://img.shields.io/badge/python-3.12-3776AB?logo=python&logoColor=white" alt="Python 3.12">
  <img src="https://img.shields.io/badge/Microsoft%20Foundry-Agent%20Service-0078D4" alt="Microsoft Foundry Agent Service">
  <img src="https://img.shields.io/badge/auth-keyless%20(Entra%20ID)-555" alt="Keyless auth with Entra ID">
</p>

> [!NOTE]
> **Built in public, one step at a time.** The design, rule library and test plan are done. Each step lands here once it has been built and run. Nothing is deployed yet: the roadmap below is the source of truth.

## Why

- **The rules put the burden on brands.** India's ASCI code, consumer-protection law and new AI-label guidance all apply.
- **Nobody checks the regional versions.** One campaign carries hundreds of variants, and the Hindi, Tamil and Telugu ones are the ones that get skipped.
- **A chatbot can only guess.** It can't time a disclaimer to the frame, cite the lab-report page, or prove that a human approved.

## How it works

<img src="docs/img/architecture.svg" width="100%" alt="Pipeline: campaign pack, read every format, claim finder, five rule-checker agents, suggested fixes, named sign-off, audit record">

## What a finding looks like

<img src="docs/img/reel-timeline.svg" width="100%" alt="Timeline of a 15-second reel: a Hindi voiceover claim at 3 s, small print on screen for 1.8 s against a 4 s rule, and an on-screen claim the lab report doesn't support">

| Catches | How |
|---|---|
| A claim spoken in Hindi, Tamil or Telugu | Speech-to-text with language ID, then claim patterns and timestamps |
| Small print shown too briefly | A frame-accurate (10 fps) re-measure against the rule's minimum |
| A claim stronger than the evidence | Search over the brand's lab reports, with the quote checked word for word |
| An unlabelled AI-generated image | Content Credentials (C2PA), a vision check and the rule text |
| An ad that hides orders for the AI | Prompt Shields: an attack never comes out "clear" |

## The human gate is enforced by the platform, not the prompt

```mermaid
sequenceDiagram
    autonumber
    participant W as Workflow
    participant A as Checker agents
    participant T as Sign-off tool (MCP)
    actor L as Named reviewer
    participant R as Audit record
    W->>A: check every creative
    A-->>W: findings + cited rule + evidence page
    Note over A,W: agents can only return fail, needs a person, or awaiting sign-off
    W->>T: mark_cleared(creative)
    Note over T: require_approval = always: the call pauses
    T->>L: approve this exact call?
    L-->>T: approve, signed in as themselves
    T->>R: who · when · on what proof · trace id
```

## Design principles

| | Principle | In practice |
|---|---|---|
| 🧭 | A pre-screen, never legal advice | Says "likely problem" or "needs a person", never "compliant" |
| 🔒 | Fail closed | Anything uncertain, unreadable or attacked goes to a person |
| 📎 | Evidence or it didn't happen | Every flag cites the rule clause and the evidence page |
| ✋ | The platform holds the gate | Clearing needs a named approval of the exact call; who can bypass it is written down and detected |
| 📏 | Measured, not claimed | Frozen held-out set, code-based evaluators, red teaming, and a CI gate that fails a change when recall drops |
| ₹ | Cost per ad is a feature | Overnight batch, one retry layer, prompt caching, and ₹ per creative on a dashboard |

## Who else does this

<img src="docs/img/competitors.svg" width="100%" alt="Capability matrix: ClaimTrail by roadmap step against Tap-a-Gain AdCheck, AdCheck India, ChatGPT or Gemini with a prompt, Puntt, Haast, Luthor and Content Credentials Verify">

- **Checking one ad is already free.** Tap-a-Gain's AdCheck is a custom GPT built by a former ASCI Secretary-General.
- **The gap ClaimTrail targets:** reels in Hindi, Tamil and Telugu, timed to the frame, checked against the brand's own evidence, with a named sign-off and a measured accuracy.
- Details and what changed in the plan: [COMPETITORS.md](COMPETITORS.md).

## Roadmap

<img src="docs/img/roadmap.svg" width="100%" alt="Roadmap: 0 of 11 steps done; step 0, accounts and guard rails, is next">

<details>
<summary>What each step proves</summary>

| # | Step | Proves |
|---|---|---|
| 0 | Accounts, budget alarms, block expensive deployments | The cloud bill can't surprise anyone |
| 1 | Script checker | Strict structured output and tool calls |
| 2 | Cited rules and evidence | Hybrid search with a per-brand filter; rules include MeitY's synthetic-content amendment and ASCI's AI-labelling guidelines |
| 3 | Injection shield | Ads and PDFs can't instruct the model |
| 4 | Reels and lab reports become timelines | Video and documents read to the frame and the page |
| 5 | Every language, every image | Hindi, Tamil, Telugu and English; claim types |
| 6 | Rule-checker agents | Agents with only the tools they need, traced end to end |
| 7 | Named human sign-off | An approval the platform enforces |
| 8 | One "check campaign" workflow | AI-label decision and suggested fixes in one command |
| 9 | Campaign scale | 200 ads overnight and ₹ per creative |
| 10 | Evaluated, red-teamed, CI-gated, hosted | The numbers in this README come from files, not claims, including a ChatGPT or Gemini with-a-prompt baseline on the same held-out set |

</details>

## Stack

Python 3.12 · [uv](https://docs.astral.sh/uv/) · Microsoft Foundry (Agent Service, Content Understanding, AI Search, Speech, Translator, Content Safety) · Agent Framework · OpenTelemetry · keyless (Entra ID) everywhere

## Run

```bash
uv sync
```

Each step adds its own commands here when it lands.

---

<sub>ClaimTrail screens ads for likely rule problems and records who signed them off. It isn't legal advice, and it doesn't certify that an ad complies with any law or code. All brands, people and ads in the examples are invented.</sub>
