---
name: transcript
description: Save a pasted meeting transcript to a local text file without changing the current model/profile. Preserves the transcript verbatim, adds simple metadata, and returns the saved path.
triggers:
- /transcript
- save transcript
- granola transcript
- zoom transcript
- transcript file
---

# Transcript Saver

Save pasted meeting transcripts as UTF-8 `.txt` files with minimal metadata.

## Defaults

- Folder: the user's `Documents/Transcripts` directory when it exists.
  - Examples: `~/Documents/Transcripts` on macOS/Linux, `%USERPROFILE%\\Documents\\Transcripts` on Windows.
- Filename: `YYYY-MM-DD HHmm.txt`
- If a clear title is available, filename: `YYYY-MM-DD HHmm - Title.txt`
- Preserve transcript text verbatim.
- Keep using the current chat model/profile. Do not switch LLMs.

## Workflow

1. If the transcript body is missing, ask the user to paste it.
2. Infer source, title, date/time, and participants only when obvious.
3. Use the current local date/time when no meeting date/time is obvious.
4. Ensure the destination folder exists before writing.
   - If the default `Documents/Transcripts` location is not available, ask the user where to save the file instead of guessing.
5. Save a UTF-8 `.txt` file with this structure:

```txt
Title: <meeting title or blank>
Source: <Granola | Zoom | Manual>
Meeting Date: <best available date/time>
Saved At: <current local date/time>
Participants: <comma-separated list or Unknown>

--- Transcript ---

<verbatim transcript body>
```

6. Avoid overwriting by appending ` (2)`, ` (3)`, and so on.
7. Reply with the absolute saved path.

## Notes

- Do not summarize or clean the transcript unless the user explicitly asks.
- Do not ask for a folder path unless the default location is unavailable or the user wants a different destination.
- Keep metadata extraction simple and deterministic.
