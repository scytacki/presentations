# Per-paper deep-dives

Detailed write-ups of the papers closest to our work, pulled out of the
[literature review](../grounding-literature-review.md) so it stays readable. Each is a faithful,
caveated summary of one paper (or, for sycophancy, a closely-related pair), in a common structure:
the problem, what it actually does, what it ran on, how it was evaluated, the key caveat, why it
matters for us, limitations, and a verification note.

These cover the **key papers only** — the ones most decision-relevant to grounding an LLM helper in
a simulation. The remaining papers stay as citations in the lit review's reference list.

| Paper | Grounding theme | One-line takeaway |
|---|---|---|
| [SimPal](simpal.md) | Sim-*adjacent* (content/curriculum anchors) | Teacher-facing front-end that extracts a sim's variables from its *description* (not the sim) to prompt a *hypothetical* downstream agent, on 63 PhET/Golabz sims — no existence-check, no student-facing tutor, no end-to-end test. |
| [World Simulator (Wang et al.)](world-simulator-wang.md) | Sim model/state (content anchor) | GPT-4 gets ~40% of state transitions wrong even with rules in hand — don't let the LLM *be* the simulator. (Text games, by analogy.) |
| [Adapting While Learning (Li et al.)](adapting-while-learning-li.md) | Tool use (content anchor) | Grounding an 8B model in external tools (a physics engine, numerical solvers, neural-surrogate emulators) raised its accuracy over its *own base model* and cut hallucination — and it must learn *when* to call the tool, not just how. (The "beats GPT-4o" result holds only on the authors' own custom datasets.) |
| [MCP-SIM](mcp-sim.md) | Execute-the-model (content anchor) | The most aggressive "run real solver code and self-correct against it" grounding — but engineering FEM, a tiny *author-built* benchmark, and "12/12 solved" means ran-and-converged (not verified-correct), with one ablation rung human-assisted. |
| [NewtBot](newtbot.md) | Prompt steering (pedagogy anchor) | Prompt-only GPT-3.5 tutor; a randomized between-subjects UX study (n=50) found students prefer the "tutor" prompt — but no grounding and no independent correctness audit (perceived accuracy only). |
| [Sycophancy (Anthropic; Kasneci & Kasneci)](sycophancy.md) | Pedagogy (pedagogy anchor) | Sycophancy is baked in by alignment, so a perfectly grounded tutor will still cave to a confident, wrong student — grounding alone isn't enough. |

See the [four-anchors framing](../response-types-and-failures.md) for what the four anchors mean.
