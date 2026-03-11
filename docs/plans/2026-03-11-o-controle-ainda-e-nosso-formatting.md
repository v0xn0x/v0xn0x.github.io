# O Controle Ainda e Nosso Formatting Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Apply the repository's standard Jekyll post structure to the 2026 article while preserving all current prose exactly.

**Architecture:** This is a single-file Markdown change. The implementation adds front matter and normalizes section spacing to mirror the editorial layout already used in the 2024 reference post.

**Tech Stack:** Jekyll, Markdown, YAML front matter

---

### Task 1: Prepare post metadata and structure

**Files:**
- Modify: `_posts/2026-03-11-o-controle-ainda-e-nosso.md`

**Step 1: Define front matter**

Add title, author, date, categories, and tags in YAML front matter at the top of the file.

**Step 2: Normalize Markdown spacing**

Insert blank lines between headings and paragraph blocks without changing the prose.

**Step 3: Preserve body content**

Keep all existing section headings and all sentences exactly as they are.

**Step 4: Verify the final structure**

Run: `sed -n '1,220p' _posts/2026-03-11-o-controle-ainda-e-nosso.md`

Expected: valid front matter followed by the unchanged article text with cleaner section spacing.
