# Skill: Sync DCM Data

## Description
Extracts new DCM files from a ZIP package, replaces old files in the `dcm/` directory, and generates a direct web link to create a Pull Request into a dynamically requested base branch.

## System Prompt & Rules
You are an expert automation agent. You must execute the following workflow steps deterministically. If any required information is missing, halt execution and ask the user immediately. Do not assume default branches like `main` or `master` for the merge target unless explicitly confirmed by the user.

## Input Parameters
- **source_path** (Required): The local path or URL to the source ZIP package containing the new DCM files.
  - *If missing, ask*: "Bitte gib den Quellpfad (source_path) zur ZIP-Datei mit den neuen DCM-Dateien an."
- **target_branch** (Required): The specific base branch where the pull request should be merged into.
  - *If missing, ask*: "In welchen Ziel-Branch (z.B. `main`, `develop`, `release-v2`) soll der Pull Request gemerged werden?"

## Workflow Steps

### Step 1: Validate Inputs
Verify that both `source_path` and `target_branch` are provided by the user. If `target_branch` is missing, prompt the user for the target branch name and wait for their input before proceeding to Step 2.

### Step 2: Prepare Workspace (Clean and Update Target Branch)
Checkout the chosen target branch, pull the latest changes from remote, and ensure the working directory is absolutely clean by discarding all local modifications and untracked files:
```bash
# Fetch latest state from remote
git fetch origin

# Switch to the requested target branch
git checkout <target_branch>

# Reset local branch to match remote exactly
git reset --hard origin/<target_branch>

# Pull latest changes securely
git pull origin <target_branch>

# Force clean the workspace (remove untracked files/directories)
git clean -fd
```

### Step 3: Clean and Extract
Completely remove existing DCM files to avoid old artifacts, then extract the new ones:
```bash
# Delete old files if present
rm -rf dcm/*

# Extract new ZIP content into the dcm folder
unzip -o <source_path> -d dcm/
```

### Step 4: Git Operations
Create the specific feature branch, stage the newly extracted files, and push them to the remote repository using the upstream flag:
```bash
# Create and switch to the required branch
git checkout -b bedatung/neue_dacms

# Stage and commit the changes
git add dcm/*
git commit -m "Update DCM files from zip package"

# Push the branch to the remote repository
git push -u origin bedatung/neue_dacms
```

### Step 5: Generate Direct Web Link
**CRITICAL**: Do NOT use the `gh` command or any API calls. Instead, construct the direct compare URL using the user-defined `<target_branch>` parameter, and output a clickable Markdown hyperlink at the very end of your response so the user can open the Pull Request in their browser with one click.

The agent must output the link in this exact format (replacing `<target_branch>` with the user's choice):
**[👉 Klicke hier, um den Pull Request in den Branch '<target_branch>' zu erstellen](https://github.com<target_branch>...bedatung/neue_dacms?expand=1)**
