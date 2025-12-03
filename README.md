# Cursus Public Knowledge Base

This repository contains the Quartz v4 static site generator that publishes the Cursus knowledge base to https://danielmcgiffin.github.io/cursus-pub-kb/

## What is This?

This is a [Quartz v4](https://quartz.jzhao.xyz/) site that automatically converts markdown files from the [cursus-kb](https://github.com/danielmcgiffin/cursus-kb) vault into a beautiful, searchable website.

## How It Works

```
cursus-kb/public/  →  [symlink]  →  cursus-quartz/content/  →  [GitHub Actions]  →  Website
```

1. You write notes in the `cursus-kb/public/` folder
2. The `content/` folder in this repo is a symlink to `../cursus-kb/public/`
3. When you push to the `v4` branch, GitHub Actions automatically builds and deploys the site

## Repository Structure

```
cursus-pub-kb/
├── content/              # Symlink to ../cursus-kb/public/
├── quartz/              # Quartz framework code
├── quartz.config.ts     # Site configuration
├── quartz.layout.ts     # Layout configuration
├── .github/workflows/   # GitHub Actions for auto-deploy
└── public/              # Built site (generated, not committed)
```

## Setup Instructions

### Prerequisites

- Node.js v20 or higher
- Git with SSH configured
- The [cursus-kb](https://github.com/danielmcgiffin/cursus-kb) repository cloned

### Initial Setup

1. Clone both repositories side by side:
   ```bash
   cd /srv/notes  # or your preferred location
   git clone git@github.com:danielmcgiffin/cursus-kb.git
   git clone git@github.com:danielmcgiffin/cursus-pub-kb.git cursus-quartz
   ```

2. Install dependencies:
   ```bash
   cd cursus-quartz
   npm install
   ```

3. Verify the symlink:
   ```bash
   ls -la content
   # Should show: content -> ../cursus/public
   ```

   If the symlink is broken, recreate it:
   ```bash
   rm content
   ln -s ../cursus/public content
   ```

### Building Locally

To preview the site locally:

```bash
npx quartz build --serve
```

Then open http://localhost:8080 in your browser.

### Publishing to the Web

The site auto-deploys via GitHub Actions when you push to the `v4` branch. You have two options:

#### Option 1: Use the Push Script (Recommended)

From the parent directory:
```bash
./push-cursus.sh "Your commit message"
```

This handles both repositories automatically.

#### Option 2: Manual Sync

Use Quartz's built-in sync command:
```bash
cd cursus-quartz
npx quartz sync --no-pull
```

This will:
1. Commit changes to the content (via symlink)
2. Push to the `v4` branch
3. Trigger GitHub Actions to build and deploy

## Configuration

### Site Configuration (`quartz.config.ts`)

Key settings:

```typescript
{
  pageTitle: "Quartz 4",           // Site title
  baseUrl: "danielmcgiffin.github.io/cursus-pub-kb",  // IMPORTANT: Must match GitHub Pages URL
  enableSPA: true,                 // Single-page app routing
  enablePopovers: true,            // Hover previews
  // ... theme, colors, typography
}
```

**Important:** If you ever change the repository name or GitHub Pages URL, update `baseUrl` in `quartz.config.ts`.

### Layout Configuration (`quartz.layout.ts`)

Controls what components appear on each page (sidebar, table of contents, etc.).

## GitHub Actions Deployment

The `.github/workflows/deploy.yml` file handles automatic deployment:

1. **Trigger:** Pushes to the `v4` branch
2. **Build:** Runs `npx quartz build` on GitHub's servers
3. **Deploy:** Publishes to GitHub Pages

### Checking Deployment Status

Visit: https://github.com/danielmcgiffin/cursus-pub-kb/actions

Builds typically take 1-2 minutes.

## Customization

### Changing the Theme

Edit colors in `quartz.config.ts`:

```typescript
theme: {
  colors: {
    lightMode: {
      light: "#faf8f8",
      // ... other colors
    },
    darkMode: {
      // ... dark mode colors
    }
  }
}
```

### Adding Plugins

Quartz has many plugins. See the [plugins documentation](https://quartz.jzhao.xyz/plugins).

Edit the `plugins` section in `quartz.config.ts`.

### Changing Typography

Edit fonts in `quartz.config.ts`:

```typescript
theme: {
  typography: {
    header: "Schibsted Grotesk",
    body: "Source Sans Pro",
    code: "IBM Plex Mono",
  }
}
```

## Troubleshooting

### Site shows XML/RSS feed instead of homepage

**Problem:** Wrong `baseUrl` in config or missing `index.md`

**Solution:**
1. Verify `baseUrl` in `quartz.config.ts` matches your GitHub Pages URL
2. Ensure `cursus-kb/public/index.md` exists
3. Rebuild and deploy:
   ```bash
   npx quartz build
   npx quartz sync --no-pull
   ```

### Changes not appearing on the site

1. Check that files are in `cursus-kb/public/` (not `internal/`)
2. Verify the symlink is working: `ls -la content`
3. Check GitHub Actions: https://github.com/danielmcgiffin/cursus-pub-kb/actions
4. Wait 1-2 minutes for the build to complete
5. Hard refresh your browser (Ctrl+Shift+R or Cmd+Shift+R)

### Symlink broken

If you move the repositories or the symlink breaks:

```bash
cd cursus-quartz
rm content
ln -s ../cursus/public content
```

### Build fails on GitHub Actions

1. Check the Actions tab for error logs
2. Try building locally: `npx quartz build`
3. Common issues:
   - Invalid markdown syntax
   - Missing dependencies (run `npm install`)
   - Broken links or image references

### "Not found" errors for pages

Quartz uses the shortest unique path for links. If you have:
- `folder/note.md` → URL is `/note`
- `folder/subfolder/note.md` → May conflict!

Solution: Use unique filenames or check link resolution in `quartz.config.ts`.

## Updating Quartz

To update to the latest Quartz version:

```bash
# Add Quartz upstream if not already added
git remote add upstream https://github.com/jackyzha0/quartz.git

# Fetch and merge updates
git fetch upstream
git merge upstream/v4

# Resolve any conflicts
npm install
```

## Development Tips

### Testing Changes Locally

Always test locally before pushing:

```bash
npx quartz build --serve
```

### Watching for Changes

Quartz will auto-rebuild when files change if you use `--serve`.

### Clearing the Cache

If you see stale content:

```bash
rm -rf .quartz-cache
npx quartz build
```

## Resources

- [Quartz Documentation](https://quartz.jzhao.xyz/)
- [Quartz GitHub](https://github.com/jackyzha0/quartz)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)

## Related Repositories

- [cursus-kb](https://github.com/danielmcgiffin/cursus-kb) - The Obsidian vault containing the source notes
- Live site: https://danielmcgiffin.github.io/cursus-pub-kb/

## Questions?

Contact the repository maintainers or check the Quartz documentation.
