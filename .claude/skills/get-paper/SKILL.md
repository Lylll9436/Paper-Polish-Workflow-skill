---
name: get-paper
description: >-
  Search Google Scholar via Chrome DevTools MCP, let the user select a paper,
  and retrieve its BibTeX entry. 通过 Chrome DevTools MCP 在 Google Scholar 搜索论文并获取 BibTeX 引用。
triggers:
  primary_intent: search Google Scholar and retrieve BibTeX entries
  examples:
    - "/get-paper urban perception street view"
    - "get paper about deep learning for land use"
    - "从Google Scholar找论文"
    - "帮我在谷歌学术搜索论文"
tools:
  - Chrome DevTools MCP
references:
  required: []
  leaf_hints: []
input_modes:
  - pasted_text
output_contract:
  - bibtex_entry
---

## Purpose

This Skill searches Google Scholar using Chrome DevTools MCP tools, presents search results to the user for selection, and retrieves BibTeX entries for selected papers. It automates the manual process of searching Google Scholar, clicking "Cite", and copying BibTeX. This Skill does NOT use Semantic Scholar MCP — it operates entirely through browser automation.

## Trigger

**Activates when the user asks to:**
- Search Google Scholar for a paper
- Get BibTeX from Google Scholar
- `/get-paper <search query>`

## Workflow

### Step 1: Navigate to Google Scholar

- Call `mcp__chrome-devtools__list_pages` to check browser state.
- If no page is open on `scholar.google.com`, call `mcp__chrome-devtools__navigate_page` to `https://scholar.google.com`.
- If already on Google Scholar, reuse the current page.

### Step 2: Search

- Take a snapshot to find the search box element.
- Fill the search box with the user's query using `mcp__chrome-devtools__fill`.
- Click the Search button using `mcp__chrome-devtools__click`.
- Wait for results with `mcp__chrome-devtools__wait_for` (text: `["Cited by"]`).

### Step 3: Present Results

- Take a snapshot of search results.
- Extract the top 5-8 results: title, authors, venue, year, citation count.
- Present them as a numbered list to the user.
- Ask the user which paper(s) to retrieve BibTeX for (support multiple selections).
- If the user's query is an exact title and the first result is a clear match, skip asking and auto-select it.

### Step 4: Get BibTeX (for each selected paper)

For each selected paper:

1. **Click Cite**: Click the `Cite` button (haspopup="menu") for the selected result.
2. **Wait for dialog**: Wait for `["BibTeX"]` text to appear.
3. **Click BibTeX link**: Click the `BibTeX` link inside the Cite dialog.
4. **Read BibTeX**: Wait for `["@"]` to appear on the new page. Take a snapshot and extract the full BibTeX entry text.
5. **Navigate back**: Go back to the search results page (`mcp__chrome-devtools__navigate_page` type=back).
6. **Close dialog if open**: If the Cite dialog is still showing, click Cancel.

### Step 5: Output

- Display all collected BibTeX entries to the user.
- Ask the user if they want to append these entries to a specific `.bib` file.
  - If yes, read the target bib file, check for duplicate keys, and append new entries.
  - Default target: `manuscript/references/references.bib` (if it exists in the current project).

## Output Contract

| Output | Format | Condition |
|--------|--------|-----------|
| `bibtex_entry` | BibTeX text | Always produced |
| Paper metadata | Numbered list with title, authors, venue, year, citations | Always presented before selection |

## Edge Cases

| Situation | Handling |
|-----------|----------|
| Google Scholar CAPTCHA or rate-limit | Inform the user and stop |
| No results found | Inform the user, suggest alternative keywords |
| BibTeX page fails to load | Retry once, then inform the user |
| Multiple papers selected | Loop through Step 4 for each selection |
| Cite dialog not closing | Navigate back to force close |

## Notes

- BibTeX keys from Google Scholar are auto-generated (e.g., `wang2024assessing`). Do not modify them unless the user asks.
- This skill uses Chrome DevTools MCP tools only. It does NOT use Semantic Scholar MCP.
- Multiple papers can be fetched in one session by repeating Step 4.

## Prerequisites

This Skill requires Chrome DevTools MCP server to be configured in Claude Code. To enable it:

1. Open Claude Code settings.
2. Navigate to **MCP Servers**.
3. Add the Chrome DevTools MCP server.
4. Ensure a Chrome browser is running with remote debugging enabled.

---

*Skill: get-paper*
