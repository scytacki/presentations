# Presentation Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Write the presentation "AI is changing how we work together" as a Marp slide deck with speaker notes.

**Architecture:** Single markdown file in Marp format. Each slide is separated by `---`. Speaker notes go in `<!-- -->` comments below each slide. The content follows the 4-section structure from the design doc.

**Tech Stack:** Marp (markdown slide deck), no build step needed — can be previewed with Marp CLI or VS Code extension.

**Time constraint:** ~20 minutes total. Keep slides minimal — short phrases, not paragraphs. Talking points go in speaker notes.

---

### Task 1: Gather concrete details from ocean simulation project

We need real specifics to make the presentation concrete, not generic.

**Step 1: Ask the user for key details**

We need to know:
- What is the ocean simulation? (one sentence for the audience)
- What planning documents were created? (science doc, roadmap, phase plans — any specifics about what was in them?)
- How many intermediate builds did PIs/PMs see?
- Any specific moment where the documents helped — or where the speed was challenging?
- Anything the audience has already seen of this simulation?

**Step 2: Note any details we can pull from conversation data**

Check `/Users/scytacki/Development/presentations/building-simulations-with-ai-partner/claude-conversation-data.md` for any relevant stats (already read — has conversation data reference but no simulation-specific content).

---

### Task 2: Write the Marp slide deck

**File:** Modify `building-simulations-with-ai-partner/presentation.md`

Replace the existing brainstorming notes with the full Marp presentation.

**Step 1: Write frontmatter and title slide**

Marp frontmatter + title slide with presentation name and author.

**Step 2: Write Section 1 slides — Opening**

1-2 slides. Callback to retreats, "a lot has changed", set up the story.

**Step 3: Write Section 2 slides — The work**

3-5 slides. Walk through the ocean simulation process: planning docs, dual purpose, sharing with PIs/PMs, intermediate builds.

**Step 4: Write Section 3 slides — What this opens up**

2-3 slides. Broaden to other roles. Note that the pattern adapts to different kinds of work. Tie into second presentation.

**Step 5: Write Section 4 slides — Open questions**

1 slide. Discussion questions.

**Step 6: Commit**

```bash
git add building-simulations-with-ai-partner/presentation.md
git commit -m "feat: write presentation slides and speaker notes"
```

---

### Task 3: Review and refine

**Step 1: Read through the full deck**

Check flow, pacing, and that speaker notes have enough detail to present from.

**Step 2: Adjust based on user feedback**

User reviews and we iterate.
