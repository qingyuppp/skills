# Bootstrap Skills Repository Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a public, portable source repository for two selected productivity skills.

**Architecture:** Store each skill under `skills/productivity/<skill-name>/` and retain upstream source files unchanged. Repository-level documentation establishes this project as the editable source and records upstream attribution and synchronization rules.

**Tech Stack:** Git, Markdown, GitHub.

---

### Task 1: Create the repository structure

**Files:**
- Create: `skills/productivity/handoff/SKILL.md`
- Create: `skills/productivity/writing-great-skills/SKILL.md`
- Create: `skills/productivity/writing-great-skills/GLOSSARY.md`

- [x] **Step 1: Create the selected skill directories.**
- [x] **Step 2: Import the upstream source files without local changes.**
- [x] **Step 3: Verify that each skill contains `SKILL.md`, and that the writing skill retains its glossary reference.**

### Task 2: Document provenance and maintenance

**Files:**
- Create: `README.md`
- Create: `LICENSE`

- [x] **Step 1: Add a README identifying the purpose, included skills, structure, and synchronization policy.**
- [x] **Step 2: Add the upstream MIT license text and attribution.**
- [x] **Step 3: Verify that the README links to the upstream repository and describes the local source-of-truth rule.**

### Task 3: Publish the initial public repository

**Files:**
- Modify: Git index and GitHub repository metadata.

- [x] **Step 1: Inspect the staged file list for only the intended skills and documentation.**
- [x] **Step 2: Commit the initial snapshot.**
- [x] **Step 3: Create a public GitHub repository named `skills`, add it as `origin`, and push the initial branch.**
- [x] **Step 4: Verify the remote URL, public visibility, and clean local status.**
