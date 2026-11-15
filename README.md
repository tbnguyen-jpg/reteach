# ReTeach: one assignment, personal paths

**Built at the n8n × AIC Club Santa Clara Buildathon (Nov 15, 2026).**

> Teachers assign one worksheet to 30 students who are confused for different reasons. ReTeach groups students by *why* they got it wrong, fixes the shared confusion together, and gives smaller groups their own path, all in one assignment the teacher approves in one click.

## The problem
Every student gets the same assignment, but a class average hides the minority. Two students with the same 67% can need completely different help. Gradebooks show *what* students got wrong, not *why*.

## How it works (n8n workflow)
1. **Teacher Upload Form**: course topic, email, quiz questions, results CSV (student IDs only).
2. **Anonymize & Group by Misconception** (Code): groups students by which wrong answer they picked, not by score.
   - **Class-wide** (30%+ of the class made the same mistake) → **Core Mission** everyone does
   - **Small group** (2+ students) → targeted **Side Quests**
   - **One-off errors** → **Personal Review**
   - **No mistakes** → **Challenge Extension**
3. **Diagnose Misconceptions** (AI Agent): explains the wrong mental model behind each group, with evidence ("50% picked B").
4. **Write Core Mission + Side Quests** (AI Agent): story-based steps. Every wrong choice reflects a real misconception and gets its own feedback.
5. **Self-Check Accuracy** (AI Agent): a second AI reviews answer keys, checks for questions with two right answers and for wording that repeats the misconception, and fixes problems before the teacher sees them.
6. **Ask Teacher to Approve** (Gmail send-and-wait): the teacher approves or requests changes. Changes go back to the writer.
7. **Delivery**: emails a Google Classroom-ready student version (no answer keys) plus a teacher-only "who gets what" list. An optional **Post to Google Classroom** node (Classroom API) is included but disabled.

## Privacy
- Student data comes in as **IDs only**, so no names enter the system.
- **The AI never sees individual students.** It only receives aggregated patterns (e.g. "14 students picked B on Q1"). Matching each student to a Side Quest happens in plain code inside n8n.
- Note: the aggregated patterns and quiz questions are sent to the AI provider (OpenAI).

## Try it
1. In n8n: **Workflows → Import from File** → `workflow/reteach.json`.
2. Add credentials: **OpenAI** (Diagnosis/Writer/Checker models) and **Gmail** (both Gmail nodes).
3. Open the form's Test URL, upload `sample-data/calculus_quiz_results_ids.csv`, and paste `sample-data/calculus_quiz_questions.txt` into the questions box.
4. Approve from your email, then paste the student version into Google Classroom.

Optional: enable **Post to Google Classroom**, add a Google OAuth2 credential with scope `https://www.googleapis.com/auth/classroom.coursework.students`, and replace `YOUR_COURSE_ID`.

## CSV format
```
student,Q1,Q2,...,Qn,score
S10401,A,C,...,B,75
answer_key,A,C,...,B,
```

## Sample data
- `calculus_quiz_*`: 8 derivative questions aligned to the topics of [OpenStax Calculus Volume 1](https://openstax.org/details/books/calculus-volume-1) (§3.3, 3.5, 3.6, 3.9). The questions were written for this demo, not copied from the book. About 40% of the simulated students consistently forget the inner derivative in the chain rule, so the same mistake shows up across Q1, Q3 and Q4.
- `physics_quiz_*`: 6 Newton's-laws questions with planted misconceptions (mass vs. weight; net force at the top of a throw).
- **All student responses are simulated.** No real student data is included.

## Stack
n8n (Form Trigger, Extract from File, Code, AI Agent + structured output parsers, Gmail send-and-wait, IF, HTTP Request) · OpenAI gpt-4o / gpt-4o-mini
