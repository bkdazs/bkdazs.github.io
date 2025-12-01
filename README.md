# Olox — The Developer's Guide to Wealth

A blog exploring the intersection of Technology and Finance, built with [Hugo](https://gohugo.io/) and the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme.

**Live Site:** [https://olox.in](https://olox.in)

## 🚀 Topics Covered

- 💹 **Finance**: Markets, investing, personal finance, fintech
- 🤖 **AI & ML**: Deep learning, LLMs, computer vision, NLP
- 💻 **Programming**: Python, algorithms, system design, interviews
- 🔗 **Blockchain**: Crypto, DeFi, Web3 technologies
- 📊 **Data Science**: Analytics, visualization, big data

## 🛠️ Local Development

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) (v0.128.0 or later)
- Git

### Setup

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/techfinance-blog.git
cd techfinance-blog

# Initialize submodules (for theme)
git submodule update --init --recursive

# Start the development server
hugo server -D
```

Visit `http://localhost:1313` to see your site.

### Creating New Posts

```bash
# Create a new post
hugo new posts/category/my-new-post.md

# Examples:
hugo new posts/finance/stock-analysis.md
hugo new posts/ai-ml/neural-networks.md
hugo new posts/programming/python-tips.md
```

### Post Front Matter Template

```yaml
---
title: "Your Post Title"
date: 2025-11-30
draft: false
description: "A brief description of your post"
categories:
  - Technology  # or Finance, AI-ML, Programming, etc.
tags:
  - tag1
  - tag2
series:
  - "Series Name"  # Optional
cover:
  image: "images/cover.jpg"  # Optional
  alt: "Cover image description"
ShowToc: true
TocOpen: true
---
```

## 📁 Directory Structure

```
techfinance-blog/
├── archetypes/          # Post templates
├── content/
│   ├── posts/
│   │   ├── finance/     # Finance articles
│   │   ├── technology/  # Tech articles
│   │   ├── ai-ml/       # AI/ML articles
│   │   ├── programming/ # Coding articles
│   │   ├── blockchain/  # Blockchain articles
│   │   └── data-science/# Data science articles
│   ├── archives.md      # Archive page
│   └── search.md        # Search page
├── static/              # Static assets (images, etc.)
├── themes/PaperMod/     # Hugo theme (submodule)
├── hugo.yaml            # Site configuration
└── .github/workflows/   # GitHub Actions for deployment
```

## 🚀 Deployment to GitHub Pages

### One-Time Setup

1. **Create a GitHub repository** named `techfinance-blog`

2. **Push your code**:
   ```bash
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/YOUR_USERNAME/techfinance-blog.git
   git push -u origin main
   ```

3. **Enable GitHub Pages**:
   - Go to repository **Settings** → **Pages**
   - Under "Build and deployment", select **GitHub Actions**

4. **Update `hugo.yaml`**:
   - Replace `YOUR_GITHUB_USERNAME` with your actual GitHub username
   - Replace `YOUR_USERNAME` in social links

5. **Push changes** - GitHub Actions will automatically build and deploy!

### Automatic Deployment

Every push to `main` triggers automatic deployment via GitHub Actions.

## ✏️ Customization

### Site Configuration (`hugo.yaml`)

- **baseURL**: Your GitHub Pages URL
- **title**: Site title
- **params.author**: Your name
- **params.description**: Site description
- **socialIcons**: Your social media links
- **menu.main**: Navigation menu items

### Adding Images

Place images in `static/images/` and reference them:
```markdown
![Alt text](/images/my-image.png)
```

### Custom CSS

Create `assets/css/extended/custom.css` for custom styles.

## 📝 License

Content is © Your Name. Code is MIT licensed.

## 🔗 Links

- **Live Site**: https://YOUR_USERNAME.github.io/techfinance-blog/
- **Hugo Documentation**: https://gohugo.io/documentation/
- **PaperMod Theme**: https://github.com/adityatelange/hugo-PaperMod
