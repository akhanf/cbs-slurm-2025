# CBS SLURM 2025 Presentation

[![Build and Deploy Marp Presentation](https://github.com/akhanf/cbs-slurm-2025/actions/workflows/marp-to-pages.yml/badge.svg)](https://github.com/akhanf/cbs-slurm-2025/actions/workflows/marp-to-pages.yml)

A comprehensive introduction to the CBS SLURM cluster built with [Marp](https://marp.app/).

## 📊 View the Presentation

- **[Live Presentation](https://akhanf.github.io/cbs-slurm-2025/)** - View in your browser
- **[PDF Version](https://akhanf.github.io/cbs-slurm-2025/presentation.pdf)** - Download PDF
- **[PowerPoint Version](https://akhanf.github.io/cbs-slurm-2025/presentation.pptx)** - Download PPTX
- **[Source Markdown](https://akhanf.github.io/cbs-slurm-2025/presentation.md)** - View source

## 🛠️ Local Development

### Prerequisites

- [Node.js](https://nodejs.org/) (v20 or later)
- npm (comes with Node.js)

### Setup

```bash
# Install dependencies
npm install

# Build HTML presentation
npm run build

# Build all formats (HTML, PDF, PPTX)
npm run build:all

# Watch mode for development
npm run watch
```

### View Locally

After building, open `index.html` in your web browser.

## 📝 Editing the Presentation

Edit `presentation.md` to modify the content. The presentation uses Marp's extended Markdown syntax.

Key features used:
- Slide separators: `---`
- Front matter for configuration
- Code syntax highlighting
- Emoji support
- Custom themes

## 🚀 Deployment

The presentation is automatically built and deployed to GitHub Pages on every push to the `main` branch using GitHub Actions.

### Setup GitHub Pages (one-time)

1. Go to repository Settings
2. Navigate to Pages section
3. Under "Build and deployment", set:
   - Source: **GitHub Actions**

## 📚 Resources

- [Marp Official Site](https://marp.app/)
- [Marp CLI Documentation](https://github.com/marp-team/marp-cli)
- [Markdown Guide](https://www.markdownguide.org/)

## 🎨 Customization

To customize the presentation theme:

1. Edit the front matter in `presentation.md`
2. Create a custom theme CSS file
3. Use Marp's built-in themes: `default`, `gaia`, `uncover`

## 📄 License

MIT
