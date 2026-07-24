# DCM Automation Skill

This repository contains an automated workflow skill designed for **VS Code Copilot** to streamline the process of updating DCM files in the `dcm/` directory and opening a Pull Request.

## How the Skill Works

The assistant uses the configuration stored in `.github/update_dcm_files.md` and `.github/copilot-instructions.md` to guide you through the process. It eliminates the need for manual git operations or external tools like the GitHub CLI (`gh`) and `curl`.

Instead, it prepares a clean environment, extracts your new files, pushes them to a dedicated branch, and provides a **direct, clickable hyperlink** to open the Pull Request interface in your browser.

---

## Prerequisites

Before using the skill, ensure that:
1. You have **VS Code** with the **GitHub Copilot** extension installed.
2. You have a local clone of this repository open in VS Code.
3. You have the **ZIP package** containing the new DCM files saved on your computer (local path) or accessible via a URL.

---

## Step-by-Step Guide: How to Update DCM Files

Follow these steps to execute the DCM update workflow using VS Code Copilot:

### Step 1: Open Copilot Chat
Open the Copilot Chat panel in VS Code by pressing:
* **Windows/Linux**: `Ctrl` + `Alt` + `I`
* **Mac**: `Cmd` + `Ctrl` + `I`

### Step 2: Prompt the Copilot Agent
Type a request into the chat telling Copilot to run the skill. You must provide the **source path** to your ZIP file. 

*Example Prompt:*
> Run the skill from `@update_dcm_files.md`. The new data is located at `C:/Users/Name/Downloads/new_dcm_package.zip`.

### Step 3: Specify the Target Branch
If you did not include the target branch in your initial prompt, Copilot will explicitly ask you:
> *"In which target branch (e.g., main, develop, release-v2) should the Pull Request be merged into?"*

Reply to Copilot with your desired base branch (for example: `develop` or `main`).

### Step 4: Run the Commands
Copilot will automatically read the skill file and generate a sequence of safe terminal commands divided into three parts:
1. **Workspace Sync**: It switches to your chosen target branch, resets any local changes (`git reset --hard`), and wipes untracked files (`git clean -fd`) to ensure a 100% clean baseline.
2. **File Replacement**: It deletes the old files inside the `dcm/` directory and unzips your new package into it.
3. **Git Push**: It creates the local branch `bedatung/neue_dacms`, commits the changes, and pushes it to the remote repository.

Click the **"Run in Terminal"** button next to the generated code blocks in the chat window to execute them sequentially.

### Step 5: Create the Pull Request
At the very end of its response, Copilot will output a direct Markdown link styled like this:

**[👉 Click here to create the Pull Request into the branch 'your-target-branch'](https://github.com...)**

1. **Click the link** provided by Copilot.
2. Your web browser will open GitHub directly to a pre-filled, expanded comparison page.
3. Review your changes and click the green **"Create pull request"** button to finish the process.

---

## Troubleshooting
* **Missing ZIP Tool**: If your local terminal environment does not have the `unzip` utility installed, ask Copilot in the chat: *"I don't have unzip installed, please provide a Python script snippet to extract the ZIP archive instead."*
* **Authentication Issues**: Ensure your local Git configuration has the correct write permissions to push branches to `://github.com`.
