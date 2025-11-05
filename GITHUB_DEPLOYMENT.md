# GitHub Deployment Instructions

**Created:** November 4, 2025
**GitHub Username:** Ingramml

---

## Quick Deployment Steps

### 1. Create the Repository on GitHub

Go to: **https://github.com/new**

**Repository Settings:**
- **Name:** `racial-terror-map`
- **Description:** Interactive historical map documenting racial terrorism and violence against Black people in America
- **Visibility:** ✅ **Public** (required for free GitHub Pages)
- **DO NOT** check:
  - ❌ Add a README file
  - ❌ Add .gitignore
  - ❌ Choose a license

Click **"Create repository"**

---

### 2. Push Your Code (Run in Terminal)

Copy and paste these commands in your terminal:

```bash
# Navigate to project directory (if not already there)
cd "/Users/michaelingram/Documents/GitHub/Website projects/Racial Terror Maps/qgis2web_2025_11_03-13_40_37_708678"

# Add GitHub as remote
git remote add origin https://github.com/Ingramml/racial-terror-map.git

# Push your code
git push -u origin master
```

**Note:** You may be prompted to authenticate with GitHub. Use your GitHub password or a Personal Access Token.

---

### 3. Enable GitHub Pages

After pushing:

1. Go to: **https://github.com/Ingramml/racial-terror-map**
2. Click the **Settings** tab
3. In the left sidebar, click **Pages**
4. Under **Source**, select:
   - **Branch:** `master`
   - **Folder:** `/ (root)`
5. Click **Save**

---

### 4. Access Your Live Map

After 2-3 minutes, your map will be live at:

**https://ingramml.github.io/racial-terror-map/**

---

## If You Need to Change the Repository Name

If you want a different name (e.g., "racial-violence-archive"), just:

1. Use that name when creating the repository on GitHub
2. Update the remote URL:

```bash
git remote add origin https://github.com/Ingramml/YOUR-REPO-NAME.git
git push -u origin master
```

Your map URL will be: `https://ingramml.github.io/YOUR-REPO-NAME/`

---

## Troubleshooting

### "Remote already exists" error

If you get this error, remove the old remote first:

```bash
git remote remove origin
git remote add origin https://github.com/Ingramml/racial-terror-map.git
git push -u origin master
```

### Authentication Required

GitHub may require:
- Your GitHub password, OR
- A Personal Access Token (PAT)

**To create a PAT:**
1. Go to: https://github.com/settings/tokens
2. Click "Generate new token (classic)"
3. Select scope: `repo`
4. Copy the token and use it as your password

### 404 Error on GitHub Pages

- Wait 2-3 minutes after enabling Pages
- Check that you selected `master` branch and `/ (root)` folder
- Verify the repository is **Public**

---

## What's Being Deployed

Your repository will include:

- ✅ Interactive map (`index.html`)
- ✅ Data files (church bombings, racial violence incidents)
- ✅ All JavaScript libraries (Leaflet, marker clustering)
- ✅ CSS stylesheets
- ✅ Documentation (popup mockups, clustering implementation)
- ✅ Claude Code skills (racial-terror-researcher, mobile-responsiveness-audit)
- ✅ All markers and icons

**Total size:** ~2.5 MB (well within GitHub's limits)

---

## Future Updates

To update the live map after making changes:

```bash
# Make your changes to files
# Stage and commit
git add -A
git commit -m "Description of changes"

# Push to GitHub
git push origin master
```

GitHub Pages will automatically update within 1-2 minutes.

---

## Share Your Map

Once live, share this URL:

**https://ingramml.github.io/racial-terror-map/**

---

*This map documents historical racial terrorism in America to preserve the memory of victims and educate future generations.*
