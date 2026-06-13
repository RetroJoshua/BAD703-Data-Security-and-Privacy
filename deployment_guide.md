# Course Site Deployment Guide

A step-by-step guide to deploy your course materials using GitHub Pages.

---

## Overview

This guide walks you through hosting your course materials (README, schedule, modules, assignments, and resources) on a public GitHub Pages site. Your 75 students will have instant access to all materials via a simple URL.

**Estimated time:** 15–20 minutes

---

## Prerequisites

- A GitHub account (free tier is sufficient)
- Git installed on your local machine (optional but recommended)
- Access to the course materials files (provided in the chat)

---

## Step 1: Download Course Materials

### 1.1 Locate Files in the Chat Interface

All course materials are available in the chat UI. You will find:
- `README.md`
- `schedule.md`
- `module1.md`
- `module2.md`
- `module3.md`
- `module4.md`
- `module5.md`
- `assignments.md`
- `resources.md`

### 1.2 Download from Files Button

1. Click the **Files** button (typically located at the top-right of the chat interface)
2. Locate the course materials in the file list
3. Download all files to a local folder on your computer (e.g., `~/my_course_materials/`)

### 1.3 Verify Downloads

Confirm that all 9 files are present in your local folder before proceeding.

---

## Step 2: Create a New GitHub Repository

### 2.1 Sign In to GitHub

1. Go to [github.com](https://github.com)
2. Sign in with your GitHub account (or create one if needed)

### 2.2 Create a New Repository

1. In the top-right corner, click the **+** icon and select **New repository**
2. **Repository name:** Use a descriptive name, e.g., `course-materials` or `[coursename]-site`
3. **Description (optional):** Enter a brief description, e.g., "Course materials for [Course Name]"
4. **Public:** Select **Public** (required for GitHub Pages)
5. **Initialize with a README:** Leave unchecked (you already have a README.md)
6. Click **Create repository**

### 2.3 Note Your Repository URL

Copy the HTTPS URL of your new repository (e.g., `https://github.com/your-username/course-materials.git`). You'll need this in the next step.

---

## Step 3: Upload Files to GitHub

### Option A: Using Git (Recommended)

**If you have Git installed on your machine:**

1. **Open terminal/command prompt** and navigate to your course materials folder:
   ```bash
   cd ~/my_course_materials/
   ```

2. **Initialize Git repository:**
   ```bash
   git init
   ```

3. **Add your GitHub remote:**
   ```bash
   git remote add origin https://github.com/your-username/course-materials.git
   ```

4. **Stage all files:**
   ```bash
   git add .
   ```

5. **Commit the files:**
   ```bash
   git commit -m "Initial commit: Add course materials"
   ```

6. **Push to GitHub (main branch):**
   ```bash
   git branch -M main
   git push -u origin main
   ```

7. **Verify:** Go to your GitHub repository page and confirm all files appear under the "main" branch.

### Option B: Using GitHub Web Interface (No Git Required)

**If you prefer not to use Git:**

1. Go to your repository on GitHub
2. Click **Add file** → **Upload files**
3. Drag and drop all 9 course material files into the upload area
4. Add a commit message: "Initial commit: Add course materials"
5. Click **Commit changes**

---

## Step 4: Enable GitHub Pages

### 4.1 Access Repository Settings

1. Navigate to your repository on GitHub
2. Click the **Settings** tab (near the top of the repository page)

### 4.2 Locate GitHub Pages Settings

1. In the left sidebar, scroll down and click **Pages**
2. Under **Source**, select:
   - **Branch:** `main`
   - **Folder:** `/ (root)`
3. Click **Save**

### 4.3 Wait for Deployment

GitHub will automatically build your site. This typically takes **1–2 minutes**. You will see a message like:

> "Your site is live at `https://your-username.github.io/course-materials`"

---

## Step 5: Share the URL with Your Class

### 5.1 Locate Your Live URL

After GitHub Pages finishes deploying (you'll see a green checkmark):
1. Return to the **Pages** settings (Settings → Pages)
2. Copy the URL shown under **Your site is live at**

### 5.2 Share with Students

Use one of these methods to share the URL with your 75 students:

- **Email:** Send the course site URL in a class email or learning management system (Canvas, Blackboard, etc.)
- **Syllabus:** Add the URL to your course syllabus and materials page
- **Learning Management System:** Post it in your course's main announcement or resources section
- **QR Code:** Use a QR code generator (e.g., [qr-server.com](https://qr-server.com)) to create a scannable code from your URL

### 5.3 Example Message for Students

> Welcome! All course materials are now available at: **[Your GitHub Pages URL]**
>
> You can access:
> - Course schedule and syllabus
> - All module materials (Modules 1–5)
> - Assignments and submission guidelines
> - Additional resources
>
> Bookmark this page for easy reference throughout the semester.

---

## Maintenance & Updates

### Adding or Updating Course Materials

1. **Using Git:**
   ```bash
   # Make changes to your local files
   git add .
   git commit -m "Update: [Description of changes]"
   git push origin main
   ```

2. **Using GitHub Web Interface:**
   - Navigate to the file you want to edit
   - Click the pencil icon to edit in-browser
   - Make your changes and click **Commit changes**

Changes typically appear on your live site within **1–2 minutes**.

### Updating Navigation

Consider adding a navigation table to your `README.md` for easier student access:

```markdown
## Course Navigation

| Section | Link |
|---------|------|
| Schedule | [View Schedule](schedule.md) |
| Module 1 | [View Module 1](module1.md) |
| Module 2 | [View Module 2](module2.md) |
| ... | ... |
| Assignments | [View Assignments](assignments.md) |
| Resources | [View Resources](resources.md) |
```

---

## Troubleshooting

### Site Not Appearing

- **Check GitHub Pages is enabled:** Settings → Pages, verify "Branch: main" is selected
- **Check the wait time:** Deployment can take 1–2 minutes; refresh the page
- **Verify file names:** Ensure files are named correctly (case-sensitive on GitHub)

### File Names or Formatting Issues

- GitHub renders Markdown files automatically; no special formatting is needed
- Ensure all file names use lowercase with hyphens (e.g., `module1.md`, not `Module 1.md`)

### Students Cannot Access the Site

- Verify the repository is set to **Public** (not Private)
- Double-check the URL is correct and share the exact link from GitHub Pages settings
- Test the URL in an incognito/private browser window to ensure it's publicly accessible

---

## Support & Resources

- **GitHub Pages Documentation:** [pages.github.com](https://pages.github.com)
- **Markdown Guide:** [commonmark.org](https://commonmark.org)
- **GitHub Help:** [docs.github.com](https://docs.github.com)

---

## Checklist

Use this checklist to ensure all steps are completed:

- [ ] Downloaded all 9 course material files
- [ ] Created a new GitHub repository (set to Public)
- [ ] Uploaded all files to the main branch
- [ ] Enabled GitHub Pages in repository settings
- [ ] Verified the site is live (checked the GitHub Pages URL)
- [ ] Shared the URL with your class of 75 students
- [ ] Tested the URL in a browser to confirm all materials are accessible

---

**Your course is now live!** Students can access all materials 24/7 via the GitHub Pages URL.
