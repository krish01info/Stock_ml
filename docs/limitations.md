# Limitations and Threats to Validity

- **Judgement is simulated.** Most experiments use news-based mood as a stand-in for a person's view. The biased simulated trader ([experiments §2.2](experiments.md#22-two-kinds-of-trader)) covers some known human biases, but real traders may differ in ways neither simulation captures.
- **The upper limit is loose.** Future mood is partly a reaction to future prices. Mitigations reduce this but cannot remove it, so row 4 overstates what perfect judgement is worth.
- **Small edges, wide intervals.** Price direction is hard to predict, and ten years gives only a few hundred independent k-day periods. Judgement may add little or nothing, or the difference may be too small to detect. Either is a valid result.
- **Few rare regimes.** Ten years contain only a handful of crises, so the rarest regime has few examples.
- **Change over time.** Relationships learned from the past may not hold in future markets.
- **Uneven news coverage.** Some days and companies have many more headlines than others, and providers add sources over time.
- **Domain gap in the sentiment classifier.** Financial PhraseBank sentences are not news headlines; classifier accuracy on our own headlines may be lower than on PhraseBank.
- **Real-human sample is tiny.** 40–60 calls per person is a case study, not evidence.
- **Not financial advice.** This is a learning project.
