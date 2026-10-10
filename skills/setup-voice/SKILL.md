---
name: setup-voice
description: >
  Set up brand voice profile for content repurposing. Use when starting the
  content repurposer for the first time, updating tone, vocabulary, or writing
  style preferences. Creates a brand-voice.md file in the project folder.
---

# Brand Voice Setup

Capture a creator's brand voice through a structured interview and writing sample analysis, then save it as a reusable `brand-voice.md` profile. Other content repurposer skills reference this file to write in the creator's voice. Run this once when starting out, or revisit it whenever tone, vocabulary, or style preferences shift.

## Capabilities

- Check for an existing `brand-voice.md` and offer to update specific sections or start fresh
- Conduct a focused 5-question voice interview, one question at a time
- Analyze writing samples to extract tone, sentence patterns, vocabulary habits, and structural tendencies
- Combine interview answers with sample analysis into a complete voice profile
- Save the profile to `brand-voice.md` in the project folder
- Confirm the output with the user and adjust based on feedback

## How to Use

Invoke this skill at any point to create or update a brand voice profile:

- `Set up my brand voice`
- `Update my brand voice`
- `I want to change my tone`
- `My writing style has changed, let's redo the voice profile`
- `/setup-voice`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Project folder path | No | Where to save `brand-voice.md` (defaults to current working directory) |
| Existing samples | No | Writing samples to analyze (provided during the interview) |
| Sections to update | No | Specific sections to revise when updating an existing profile |

## Output

A `brand-voice.md` file saved to the project folder, containing:
- Tone description
- Person and perspective (pronouns, point of view)
- Preferred vocabulary and phrases to avoid
- Style patterns extracted from writing samples
- Representative sample excerpts

---

## Phase 1: Check for Existing Voice Profile

Before starting the interview, look for an existing `brand-voice.md` in the project folder.

### If no profile exists:

Greet the user and explain what's about to happen:

> "I don't see a brand voice profile yet. I'll ask you 5 quick questions and have you paste a few writing samples, then I'll put together a `brand-voice.md` you can use across all your content. Let's get started."

Proceed to Phase 2.

### If a profile already exists:

Read the existing `brand-voice.md` and summarize what it currently contains. Then offer two options:

> "I found your existing brand voice profile. Here's what it covers: [brief summary of current tone, person, key vocabulary]. Would you like to:
>
> 1. **Update specific sections** — tell me what's changed and I'll revise just those parts
> 2. **Start fresh** — go through the full interview again from scratch"

Wait for the user's choice before proceeding.

- If updating specific sections: ask targeted questions only about those sections, then skip to Phase 3 with the existing profile as the base.
- If starting fresh: proceed to Phase 2 with the full interview.

---

## Phase 2: Voice Interview

Ask these 5 questions one at a time. Wait for a response before asking the next question. Do not present all questions at once.

### Question 1: Tone

> "How would you describe the overall tone of your writing? Choose the option that fits best, or describe your own:
>
> - **Casual** — conversational, relaxed, like texting a friend
> - **Professional** — polished, credible, business-appropriate
> - **Authoritative** — expert, confident, definitive
> - **Playful** — witty, light, fun
> - **Something else** — describe it in your own words"

Record the response. If the user picks a label, ask: "Anything you'd add to that? Any nuances?"

### Question 2: Person and Perspective

> "Do you write as 'I' (first person singular) or 'we' / your company name (first person plural or third person)?
>
> For example: 'I've found that...' vs. 'We've found that...' vs. '[Company] believes...'"

Record the response and any clarifying details.

### Question 3: Words and Phrases You Love

> "Are there any words, phrases, or expressions you use often and want to keep using? These could be industry terms, signature phrases, or just words you gravitate toward."

If the user isn't sure, offer a prompt: "Think about words that feel very 'you' when you read them back."

### Question 4: Words and Phrases to Avoid

> "Now the flip side — are there any words, phrases, or expressions you actively dislike or want to avoid? These might be corporate jargon, overused buzzwords, or anything that feels off-brand."

Common examples to prompt if needed: "Things like 'synergy,' 'leverage,' 'game-changer,' 'utilize,' em dashes, etc."

### Question 5: Writing Samples

> "Finally, paste 2-3 examples of your writing that you feel represent your voice well. These could be emails, blog posts, social captions, newsletter sections — anything you've written that sounds like you at your best."

Wait for the user to paste the samples. If they provide fewer than 2, gently ask for at least one more: "One more example would help me spot patterns more reliably — do you have anything else?"

---

## Phase 3: Analyze and Generate Profile

Once all 5 questions are answered and samples are collected, analyze the writing samples to extract patterns. Combine the analysis with the interview answers to build the full profile.

### Sample Analysis

For each writing sample, look for:

- **Sentence length and rhythm** — short and punchy, long and flowing, or mixed?
- **Paragraph length** — dense or airy? Single-sentence paragraphs or multi-sentence?
- **Use of questions** — does the writer address the reader directly?
- **Use of data and specificity** — do they cite numbers, examples, or concrete details?
- **Storytelling tendencies** — do they open with anecdotes, scenarios, or straight information?
- **Vocabulary register** — simple everyday words vs. technical or elevated language?
- **Structural patterns** — do they use lists, headers, bold text? Or flowing prose?
- **Transitions** — how do they move between ideas?
- **Recurring phrases or tics** — any expressions that show up more than once?

After analyzing individual samples, look for patterns across all of them and note what's consistent.

### Profile Synthesis

Combine the interview answers with the sample analysis:

- Use the tone label and nuances from Question 1 as the foundation, then layer in what the samples reveal
- Verify the person and perspective from Question 2 against actual usage in the samples (note any inconsistencies)
- Add loved words/phrases from Question 3 to the Vocabulary section
- Add words to avoid from Question 4 to the Avoid list, supplemented by any patterns spotted in samples
- Distill the most representative style observations into the Style Patterns section
- Select 2-3 short excerpts (2-4 sentences each) from the samples that best exemplify the voice

---

## Phase 4: Save brand-voice.md

Save the completed profile as `brand-voice.md` in the project folder using the following exact template:

```markdown
---
name: Brand Voice Profile
created: [YYYY-MM-DD]
updated: [YYYY-MM-DD]
---

## Tone
[Description based on interview + sample analysis]

## Person & Perspective
[First/third person, pronouns to use]

## Vocabulary
### Preferred Words & Phrases
- [word/phrase]

### Avoid
- [word/phrase]

## Style Patterns
[Extracted from writing samples: sentence length, paragraph structure,
use of questions, use of data, storytelling tendencies, etc.]

## Sample Excerpts
[2-3 short excerpts from their samples that exemplify their voice]
```

Use today's date for both `created` and `updated` on first creation. On subsequent updates, preserve the original `created` date and update only the `updated` date.

---

## Phase 5: Confirm with User

After saving the file, show the user a summary of the completed profile and ask for feedback.

> "Here's what I captured for your brand voice profile:
>
> **Tone:** [one-sentence summary]
> **Person:** [first/third person]
> **Preferred phrases:** [short list]
> **Avoid:** [short list]
> **Key style patterns:** [2-3 bullet points]
>
> I've saved this to `brand-voice.md`. Does this feel accurate? Anything you'd like to adjust?"

If the user requests changes:
- For small adjustments (a word here or there), make the edits directly and confirm the updated line.
- For larger changes (tone feels wrong, needs new examples), ask targeted follow-up questions rather than restarting the full interview.

Once the user confirms the profile is accurate, let them know it's ready to use:

> "Your brand voice profile is set. Other content skills will use it automatically when generating content for you."

---

## Re-run Behavior

If `brand-voice.md` already exists (covered in Phase 1), always offer to update specific sections rather than forcing a full restart. Preserve existing content in sections that aren't being updated. Never overwrite the full file without explicit user confirmation.

---

## Best Practices

1. **One question at a time** — never present all 5 interview questions at once; the conversation feels more natural and yields better answers
2. **Read the samples carefully** — the most valuable voice data comes from the samples, not the interview; let the analysis reinforce or complicate what the user said about themselves
3. **Flag contradictions** — if the user says "professional" but the samples are clearly casual, surface that: "Your samples read pretty conversationally — is professional your aspiration, or should I match what I see in your writing?"
4. **Don't over-engineer the profile** — a clear, concise profile is more useful than an exhaustive one; aim for precision, not comprehensiveness
5. **Excerpts should be verbatim** — copy the sample excerpts exactly as written; these serve as anchors for future content generation

## Limitations

- Voice profiles are only as accurate as the samples provided; 2-3 samples is a minimum, and they should represent the creator's best and most typical work
- Tone is subjective; the profile captures a snapshot and may need updating as the creator's style evolves
- Style patterns extracted from samples reflect what the creator has written, not necessarily what they aspire to write; ask if there's a gap
