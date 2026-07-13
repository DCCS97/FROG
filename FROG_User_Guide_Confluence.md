# FROG — Field Redaction & Obfuscation Gadget

**Team User Guide**  
**Applies to:** FROG v1.1.x  
**Classification:** Internal use

> **Purpose:** Use FROG to replace likely personal data in support logs before pasting the reviewed scrubbed result into an approved LLM chat tool.
>
> **Important:** FROG is a risk-reduction aid, not a certified DLP control. Always review the output. Never paste or share the local mapping because it contains the original values.

## Quick answer: using a ServiceNow incident number as the seed

Yes. In **Synthetic** mode, entering `INC123456789` as the seed means the same real value will receive the same synthetic replacement when you scrub it again with that same seed, provided you use the same FROG version/generation logic.

```text
Seed: INC123456789
Marcus Vance-Delgado   -> the same synthetic name each time
GB29BARC20451288102938 -> the same synthetic IBAN each time
```

**Important exception:** In **Redact** mode, placeholders such as `[NAME_1]` are numbered by first appearance in the current log. The seed does not guarantee the same placeholder number across separate logs.

## 1. What FROG does

FROG scans pasted or loaded log text and replaces likely PII in one of two modes:

- **Synthetic:** realistic, format-preserving pseudonyms. Recommended for technical diagnosis.
- **Redact:** numbered placeholders such as `[EMAIL_1]`.

Within a scrub, repeated real values always receive the same replacement.

### Detected by default

- Emails
- IBANs
- UK National Insurance numbers
- UK postcodes
- UK sort codes
- UK phone numbers, including `+44`
- Labelled account numbers
- Luhn-valid card candidates
- Labelled dates of birth
- Labelled/titled names and optional known names

> Pattern matching can miss unfamiliar or organisation-specific formats. Review the output. IP addresses, street addresses, BIC/SWIFT codes and internal IDs may require custom rules.

## 2. Quick start

1. Launch `FROG.exe`.
2. Leave **Mode** set to **Synthetic** unless placeholders are required.
3. Enter the incident reference in **Seed**, e.g. `INC123456789`, when repeatable values may be needed.
4. Leave **Include LLM preamble** enabled.
5. Paste the log, or select **Load .log / .txt…**.
6. Select **Scrub**, or use **Auto-scrub after paste/edit**.
7. Review the entire output.
8. Select **Copy Output** and paste only that reviewed output into the approved LLM.
9. Open **View Mapping (Local Only)** only when translation is required. Never paste or share it.

## 3. Recommended incident workflow

| Stage | Action |
|---|---|
| Start | Use the ServiceNow incident reference as the Synthetic-mode seed. |
| First log | Scrub, review and copy only the output. |
| Additional logs | Reuse the exact same seed. The same original value should receive the same synthetic replacement. |
| Mapping | Each mapping contains only values found in that scrub. Save each required mapping, or scrub related logs together when appropriate. |
| Closure | Dispose of mappings according to incident and data-retention policy. |

## 4. Interface controls

| Control | Purpose |
|---|---|
| Synthetic | Creates realistic, format-preserving pseudonyms. |
| Redact | Creates placeholders such as `[NAME_1]`. |
| Seed | Makes Synthetic-mode output deterministic for the same original value/type. |
| Include LLM preamble | Explains that identifiers are synthetic. Normally leave enabled. |
| Interface / Appearance | Retro or Modern; Light or Dark. Visual only. |
| Auto-scrub | Scrubs after input settles for roughly half a second. |
| Known names file | One real name per line, for names generic rules may miss. |
| Copy Output | Copies scrubbed output only—not the mapping. |
| View Mapping | Shows fake-to-real translations. Local only and highly sensitive. |

## 5. Seed rules

For repeatability in Synthetic mode, all of these must remain the same:

- Exact seed text
- Original value
- Detected PII type
- FROG version/generation logic

Recommended seed: the approved internal incident reference, e.g. `INC123456789`.

- `INC123456789` and `inc123456789` are different seeds.
- Do not use customer PII as a seed.
- The seed is not encryption and cannot reverse values.
- A blank seed creates a fresh random mapping.
- Keep the same FROG executable for a case where exact repeatability matters.
- The mapping for a scrub contains only values found in that input.

## 6. Synthetic vs Redact

| Feature | Synthetic | Redact |
|---|---|---|
| Example | `John Smith -> Daniel Fletcher` | `John Smith -> [NAME_1]` |
| Format preservation | Yes, where supported | No |
| Same value within one log | Consistent | Consistent |
| Same seed across logs | Deterministic | Placeholder number not guaranteed |
| Mapping | Yes | Yes |

## 7. Mandatory output review

Before pasting the result:

- Check for names, emails, addresses, IPs, IDs and other organisation-specific values.
- Confirm timestamps, errors, stack traces and event sequence remain readable.
- Confirm repeated synthetic values are consistent.
- Paste only the scrubbed output.
- Never paste the mapping.

## 8. Local-only mapping

The mapping contains the original values and must be treated as sensitive source data.

- Save it only to an approved restricted location, if required.
- Do not attach it to general tickets, email or chat.
- `Copy Output` does not copy the mapping.
- Dispose of it according to retention policy.

## 9. Known names file

Create an approved plain-text file with one complete name per line:

```text
Alex Morgan
Marcus Vance-Delgado
Jane Doe
```

Select it using **Browse…** beside **Known names file**. The file itself contains PII and must not be uploaded with FROG or committed to source control.

## 10. Limitations

FROG does not guarantee that all PII is removed. Particular care is required for:

- Street addresses
- IP addresses
- Internal customer/user IDs
- BIC/SWIFT codes
- Unlabelled account numbers and dates
- Names appearing only in free text

## 11. Troubleshooting

| Issue | Check |
|---|---|
| Output did not update | Press **Scrub**, or check Auto-scrub and wait briefly. |
| Value not replaced | Confirm it is supported/labelled; use a known-name file or approved custom rule. |
| Same value changed | Confirm Synthetic mode, exact same seed and same FROG executable/version. |
| Redact placeholders differ | Expected across logs; use Synthetic mode for deterministic identities. |
| Mapping button disabled | Run a successful scrub first. |
| Executable blocked | Do not bypass controls; request signing or allow-listing. |

## 12. FAQ

**Does FROG use AI?**  
No. It uses local Python pattern matching, validation and deterministic data generation.

**Does it send logs anywhere?**  
No network functionality is implemented. Scrubbing runs locally.

**Where do fake names come from?**  
The locally bundled British-English Faker dataset. Synthetic emails use `example.com`.

**Is the seed encryption?**  
No. It controls deterministic pseudonym generation.

**Can one incident reference be used for all related logs?**  
Yes, in Synthetic mode. Reuse the exact seed.

**Does a successful scrub certify that no PII remains?**  
No. Manual review remains mandatory.

## 13. Team details to complete before publishing

- Application owner: `[Insert team / owner]`
- Support route: `[Insert channel or queue]`
- Approved download location: `[Insert location]`
- Approved mapping storage: `[Insert location/policy]`
- Custom-rule approval route: `[Insert process]`
- Current approved version/hash: `[Insert version/hash]`
