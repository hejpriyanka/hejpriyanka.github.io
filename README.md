Here's a comprehensive README.md template for your GitHub Pages portfolio setup with Hugo Ananke theme:

```markdown
# Priyanka Yelluru - Portfolio & Blog Setup Guide

This document contains step-by-step instructions for setting up and maintaining a Hugo-based portfolio website with GitHub Pages.

## Prerequisites
- GitHub account
- Git installed locally
- Hugo extended version (v0.115.4+)

## 1. Initial Setup

### Install Hugo (macOS/Linux)
```bash
brew install hugo
```

### Create New Site
```bash
hugo new site portfolio
cd portfolio
git init
```

## 2. Add Ananke Theme
```bash
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> config/_default/config.toml
```

## 3. Configuration
Create `config/_default/config.toml` with:

```toml
baseURL = "https://yourusername.github.io/"
languageCode = "en-us"
title = "Priyanka Yelluru | Backend Developer"
theme = "ananke"

[params]
  author = "Priyanka Yelluru"
  description = "Python Backend Developer | Cloud Technologies | Test Automation"
  featured_image = "/images/header-bg.jpg"
  github = "yourgithubusername"
  linkedin = "priyanka-yelluru"

[[menu.main]]
  name = "About"
  url = "/about/"
  weight = 1

[[menu.main]]
  name = "Now"
  url = "/now/"
  weight = 2

[[menu.main]]
  name = "Blog"
  url = "/posts/"
  weight = 3

[[menu.main]]
  name = "Contact"
  url = "/contact/"
  weight = 4
```

## 4. Content Creation

### About Page
```bash
hugo new about.md
```
Edit `content/about.md`:
```markdown
---
title: "About Priyanka"
date: 2024-02-15
draft: false
---

### Professional Summary
Results-driven Software Engineer with extensive experience in Python development, cloud technologies, and test automation...

### Technical Skills
- **Cloud Technologies**: AWS (Lambda, S3, EC2), Docker
- **Backend Development**: Microservices, REST APIs, FastAPI
- **Data Analysis**: Pandas, NumPy, Data Visualization
```

### Now Page
```bash
hugo new now.md
```
Edit `content/now.md` with current focus areas and updates.

### Contact Page
```bash
hugo new contact.md
```
Edit `content/contact.md`:
```markdown
---
title: "Contact"
date: 2024-02-15
draft: false
---

📧 hejpriyanka@gmail.com  
📍 Kista, Stockholm  
🔗 [LinkedIn](https://linkedin.com/in/priyanka-yelluru)

<!-- Add contact form integration here -->
```

## 5. Blog Setup
Create first post:
```bash
hugo new posts/first-post.md
```

## 6. GitHub Pages Deployment

### Create gh-pages Branch
```bash
git checkout --orphan gh-pages
git rm -rf .
git commit --allow-empty -m "Initial gh-pages commit"
git push origin gh-pages
git checkout main
```

### Setup GitHub Actions
Create `.github/workflows/gh-pages.yml`:
```yaml
name: GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
          
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.115.4'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## 7. Final Steps

1. Create GitHub repository named `yourusername.github.io`
2. Push local repository:
```bash
git remote add origin https://github.com/yourusername/yourusername.github.io.git
git branch -M main
git push -u origin main
```

3. Enable GitHub Pages:
   - Go to Repository Settings > Pages
   - Source: Deploy from branch
   - Branch: gh-pages (root)

## Maintenance

- Add new blog posts: `hugo new posts/post-title.md`
- Local preview: `hugo server -D`
- Push changes to main branch to trigger auto-deploy

```

This README provides:
1. Clear setup instructions
2. Configuration details matching your CV
3. Automated deployment workflow
4. Content structure for portfolio sections
5. Maintenance guidelines

The Ananke theme provides built-in features for:
- Responsive design
- SEO optimization
- Social media integration
- Blog post taxonomy
- Contact form integration (you can add Formspree or similar service)

Remember to:
1. Replace placeholder values (yourusername, etc.)
2. Add actual images to `/static/images/`
3. Configure contact form service
4. Customize theme colors in `config.toml`
5. Add Google Analytics if needed