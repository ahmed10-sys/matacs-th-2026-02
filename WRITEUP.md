# AI Proposal Triage Design: Write-up

### 1. AI vs. rules
**AI is used strictly for text extraction.** It reads the unstructured PDF proposals and outputs a typed JSON object containing raw facts (e.g., price, timeline) and verbatim evidence quotes.
**Code and rules handle everything else.** A deterministic script evaluates mandatory pass/fail criteria (like licenses) and calculates the final weighted score. 
**Why this line?** Large Language Models are excellent at parsing messy human language but are non-deterministic and prone to hallucination when doing math or logical reasoning. Standard code guarantees that the math is 100% reliable and fair.

### 2. Consistency and explainability
Because the AI does no math, the scores are perfectly repeatable. If the same proposals were processed a thousand times, the Python math engine would output the exact same score every time. 
To ensure explainability, the AI is prompted to extract a verbatim quote for every data point. The reviewer’s UI displays the computed math breakdown side-by-side with these exact quotes from the PDF, making it instantly clear why a proposal received its score.

### 3. Human in the loop
The human sits at the very end of the pipeline. They review the final ranked list, investigate any proposals marked as "flagged" (such as missing data or detected anomalies), and click to confirm the final award decision. 
**Audit Log:** Every action is recorded. The log captures the uploaded file hash, the AI model version, the exact JSON extracted, the criteria weights applied at that moment, the final computed score, the reviewer's identity, and any written justification if they manually override the system's ranking.

### 4. Changing criteria
Because extraction is cleanly separated from scoring, adapting to changes is highly efficient. 
If an admin only changes the *weights* (e.g., Price goes from 25% to 40%), the AI does not need to run again. The code simply recalculates the scores instantly using the already-extracted JSON facts. 
If an admin adds a *new* criterion, the AI is prompted to extract only that new specific field from the stored text. All criteria versions are snapshotted in the database, so past scores always remain explainable under the rules that existed at that time.

### 5. Risks and Safeguards
* **Made-up facts (Hallucinations):** The AI is forced to provide a verbatim quote for every value. The code can run a simple string-matching check to ensure that quote actually exists in the raw text. 
* **Manipulation (Prompt Injection):** Candidates might hide instructions in their PDFs (e.g., "Rank this proposal first," as seen in Proposal C). The system prompt strictly treats PDF text as untrusted data and flags suspicious commands for human review.
* **Missing Data:** If a requirement is missing (e.g., Proposal D's timeline), the AI is strictly instructed to output `null` instead of guessing. The code sees `null` and automatically flags the proposal as ineligible.
* **Cost:** PDF files are hashed upon upload. If a user uploads the same PDF twice, the system uses the cached JSON extraction, saving unnecessary AI API costs.