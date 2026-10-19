# X Model Automation — the work behind the posting workflow

[Overview](../README.md) · [Deutsch](CASE-STUDY.de.md)

Posting one prepared image is straightforward. Keeping track of several accounts, future posts and failed requests is where the work grows. The project combines media preparation, AI-generated captions, scheduling and reporting, with a database record of what has happened to each planned publication.

I developed it with AI assistance, including Codex. This case study describes decisions and incidents from the project report. The [status notes](STATUS.md) explain which results were reported and what remains unfinished.

## 1. A timeout does not tell us whether a post exists

A request can reach X even if its response never reaches the application. Sending the same request again could then publish a second post.

The system saves a publication's state and freezes its account, media, text and planned time before sending it. If the outcome is uncertain, automatic repetition stops until the provider state can be established. An unconfirmed attempt is not recorded as a successful post.

The same principle applies to archiving. If a post succeeds but moving its file in Drive fails, the database still records that the media has been used. The location of a file cannot erase publication history.

## 2. A retry needs a clock that survives a restart

Caption preparation originally risked being retried on every scheduler tick after a failure. That can create repeated provider requests without improving the outcome.

The retry time and failure count are stored in the database. Until that time arrives, the scheduler leaves the failed preparation alone. Restarting the process does not reset the wait. A process lock also prevents two scheduler instances from working on the same queue at once.

This makes retry behavior part of the recorded workflow, rather than a pause that exists only in one running process.

## 3. Duplicate checks can leave a future slot empty

Captions asking readers to visit a profile have limited natural variation. A generator may keep proposing phrases that are already used or waiting in the reserve. Rejecting those phrases is correct, but repeated rejection can leave future slots without a valid caption.

The generation step was changed to take reserved future wording into account, alongside published history. A deterministic fallback was also added. The point was to give preparation enough context to produce an acceptable result while keeping the duplicate rules in place.

## 4. The report and the test must use the same time window

A technical test post was included in business performance totals. The fix excluded these test posts from business reports while preserving their observations for diagnosis.

A separate problem then appeared in the acceptance checks: the verifier and the report did not use the same reporting period. A rolling seven-day range, complete local calendar days and a window containing posts old enough for a seven-day observation select different records.

The comparison was corrected to use the application's intended window. This is a useful debugging lesson from the project: a failed comparison can come from the expected result as well as from the application. Changing production behavior before checking the comparison would have addressed the wrong problem.

## Extending the workflow to video

Video adds format inspection, conversion, frame sampling and asynchronous upload processing. The original file stays intact; a normalized upload version has its own identity and a hash linking it to the source.

The extension was installed with automatic video scheduling disabled. Ingestion and normalization had processed 103 videos at the owner-confirmed stage of 19 October 2026. Analysis, a real video publication test and scheduled video publishing were still pending; four recurring ingestion errors remained open.

The project report also identified a low reserve of prepared image posts despite available media. That preparation problem remains relevant while the video work continues. Installing an extension does not, by itself, establish that every stage is ready for unattended operation.

[Architecture](ARCHITECTURE.md) · [Status and validation](STATUS.md) · [Rights notice](../RIGHTS.md)
