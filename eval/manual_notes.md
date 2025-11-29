For evaluation, a small set of test scenarios was used, for example:

New student onboarding

Input: New user_id, exam = TCS NQT, 45 days, 2 hours/day.

Expectation:

Study plan produced.

Plan covers multiple days and multiple sections (Quant, Reasoning, Verbal).

Quiz generation quality

Input: Request a quiz for “Percentages – easy”.

Expectation:

5 MCQs generated.

Clear A/B/C/D options.

Correct answer and explanation present for each.

Explanation clarity

Input: Selected question + wrong answer from student.

Expectation:

Explanation is step-by-step.

Clearly states correct vs wrong answer.

Provides at least one tip.

Progress analysis

Input: After a few quiz rounds for different topics.

Expectation:

Identifies which topics were answered correctly more often.

Labels strong vs weak topics.

Suggests concrete next steps.

Each scenario is tested manually through the ADK web UI, and responses are reviewed for:

Correctness

Clarity

Use of tools (profile, quiz history)