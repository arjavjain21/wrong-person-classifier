# Wrong-Person Classifier (Python)

This repository contains a standalone Python port of the original Google Apps Script classifier in
`classifier_v1.gs`. The script inspects email reply bodies, classifies "wrong person" responses,
extracts alternate email addresses, and writes structured output columns to a CSV file.

## What it does

* Detects delivery status notifications (DSN) vs. human replies.
* Tags replies as `wrong_person` or `not_wrong_person`.
* Assigns a subtype (left company, not monitored, email changed, etc.).
* Extracts alternate email addresses and tries to choose the best candidate.
* Captures the regex patterns that matched for transparency.

## Quick start

1. Ensure you have Python 3.9+ available.
2. Prepare a CSV with headers that include `email_body_text` and/or `email_body_html`.
3. Run:

```bash
python wrong_person_classifier.py data/sample_input.csv
```

This will create `data/sample_input_classified.csv` with the extra output columns.

## CLI usage

```bash
python wrong_person_classifier.py INPUT_CSV [--output OUTPUT_CSV]
  --body-text-col TEXT_COLUMN_NAME
  --body-html-col HTML_COLUMN_NAME
  --body-text-index 0_BASED_INDEX
  --body-html-index 0_BASED_INDEX
  --original-email-col ORIGINAL_EMAIL_COLUMN
  --original-email-index 0_BASED_INDEX
```

### Examples

Use custom column names:

```bash
python wrong_person_classifier.py data/sample_input.csv \
  --body-text-col body_text \
  --body-html-col body_html \
  --original-email-col recipient_email
```

Use column indices instead of names:

```bash
python wrong_person_classifier.py data/sample_input.csv \
  --body-text-index 14 \
  --body-html-index 15
```

## Output columns

The script appends the following columns:

| Column | Description |
| --- | --- |
| reply_type | `dsn_bounce`, `auto_reply`, or `human_reply` |
| tag | `wrong_person`, `not_wrong_person`, or `dsn_bounce` |
| subtype | Category such as `WP1_LEFT_COMPANY` |
| total_score | Total rule score across matched buckets |
| best_alternate_email | Best alternate email candidate (if any) |
| best_alternate_name | Best matching name near the email (if any) |
| alternate_emails | All alternate emails found (comma-separated) |
| matched_patterns | JSON of pattern IDs that matched |

## Input format

The input CSV must include a header row. At least one body column (`email_body_text` or
`email_body_html`) is required. You can optionally include a column with the original recipient email
address to avoid returning it as an alternate.

## Notes

* The `classifier_v1.gs` file is kept for historical reference.
* All logic is rules-based and resides in `wrong_person_classifier.py` for easy customization.

## License

MIT
