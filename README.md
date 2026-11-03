# ETL AI Automation (n8n)

An n8n workflow that cleans student enrollment data with an LLM.

## How it works

1. **On form submission**: a form where you upload a spreadsheet or CSV.
2. **Switch**: continues only if a file was uploaded.
3. **Extract from File**: turns the file into one item per row.
4. **Basic LLM Chain + OpenAI Chat Model** (`gpt-4.1-mini`): cleans each row and returns JSON:
   - Name and City in Title Case (empty City becomes `UNKNOWN`)
   - Invalid email becomes `INVALID_EMAIL`
   - Phone with fewer than 10 digits becomes `MISSING`
   - Course limited to Python, Machine Learning or Data Science
   - Fee_Paid as true/false
   - Enrolled_Date as `YYYY-MM-DD`
5. **Code in JavaScript**: parses the model's output and handles parse errors.
6. **If**: keeps rows where `Fee_Paid` is true.
7. **Convert to File**: writes the result to an `.xlsx` file.

## Import

In n8n: **Workflows → Import from File**, then pick `etl-ai-automation.json`.
Attach your own OpenAI credential to the **OpenAI Chat Model** node.

## Sample run

| File | Rows | What it is |
|------|------|------------|
| `student_enrollment_raw.csv` | 50 | Input: the original messy file uploaded through the form |
| `students_clean.csv` | 33 | Output: cleaned rows where the fee is paid (17 unpaid rows filtered out) |

Invalid emails are left empty in the clean file, and short phone numbers show `MISSING`.
