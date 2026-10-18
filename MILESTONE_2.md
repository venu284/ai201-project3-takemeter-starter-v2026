# Milestone 2: taxonomy stress test

The final definitions and real examples are in [README.md](README.md#label-taxonomy). I chose the fourth `question` label before collecting training examples, because genuine questions appeared throughout the Milestone 1 reading and did not fit cleanly with either reactions or claims.

I used this AI assistant to generate eight synthetic boundary probes from the draft definitions. Prompt: "Given `analysis` (supported F1 interpretation), `hot_take` (unsupported F1 judgment), `question` (answerable request), and `reaction` (momentary report or response), write eight short F1 comments that sit between pairs of labels. Include fact-plus-claim, sincere-versus-rhetorical question, and reaction-versus-judgment cases. Do not label them."

These eight comments are synthetic stress-test cases. They are **not** real community posts and will not go into `labels.csv`.

| # | Synthetic boundary probe | Decision | Why |
|---:|---|---|---|
| 1 | "Verstappen is eight points behind Hamilton, so Ferrari's development has stalled." | `hot_take` | The points gap is specific, but it does not establish the claimed cause. |
| 2 | "Verstappen cut the gap from 60 to eight points while Ferrari brought no major upgrades; that helps explain the swing." | `analysis` | Checkable change and upgrade claim are linked to the interpretation. |
| 3 | "Why did Ferrari leave Hamilton out under the VSC when Leclerc boxed?" | `question` | It gives context but requests an explanation. |
| 4 | "Ferrari threw away Hamilton's podium, didn't they?" | `hot_take` | The question form carries a verdict and asks mainly for agreement. |
| 5 | "Hamilton apologized over radio after the race." | `reaction` | It reports a moment without interpreting it. |
| 6 | "Hamilton said the strategy was his call on the radio, so blaming only the pit wall misses his part in it." | `analysis` | The reported quote directly supports the allocation of responsibility. |
| 7 | "Another genius Ferrari strategy 🙄" | `hot_take` | Sarcasm conveys a judgment about strategy. |
| 8 | "No way! What a pit stop." | `reaction` | It cheers an immediate moment without a broader claim. |

Probe 1 exposed a weakness in the draft phrase "mentions a checkable fact": a statistic can be adjacent to an unsupported causal claim. I tightened `analysis` to require a specific detail **connected to the conclusion**. Probes 3 and 4 led to the explicit sincere-versus-rhetorical question rule. Probes 5 and 6 separate a bare report from an interpretation of that report. With these rules, each probe has one intended label.

I also considered the strongest case for each label on the real one-stop/Safety Car post. The `analysis` case is that it identifies a mechanism and a plausible causal relationship between a Safety Car and pit strategy. The `hot_take` case is that "always" offers no particular race, incident, or result to check, so the mechanism is an explanation without evidence. Both readings have force; the written evidence rule resolves it as `hot_take`.

For a class discussion, I would show the `analysis` and `hot_take` definitions and that post from README without revealing its label, then ask others to classify it. No peer vote has happened yet; the rule above is my current decision, open to correction if annotators disagree.
