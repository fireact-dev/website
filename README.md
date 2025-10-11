# Fireact.dev Marketing Website

This repository hosts the official marketing website for Fireact.dev, built with [Hugo](https://gohugo.io/) and the Universal theme.

## Overview

The marketing website showcases Fireact.dev features, provides information for potential users, and serves as the main entry point for the project.

## Live Site

Visit the live website at: [fireact.dev](https://fireact.dev)

## Tech Stack

- **Hugo**: Static site generator
- **Hugo Universal Theme**: Responsive business theme
- **Markdown**: Content format
- **Bootstrap**: CSS framework (via theme)
- **JavaScript**: Interactive features

## Prerequisites

- **Hugo** (v0.110.0 or higher) - [Installation Guide](https://gohugo.io/getting-started/installing/)
- **Git**: For version control

### Installing Hugo

**macOS:**
```bash
brew install hugo
```

**Windows:**
```powershell
choco install hugo
# or
scoop install hugo
```

**Linux:**
```bash
snap install hugo
```

Verify installation:
```bash
hugo version
```

## Getting Started

### 1. Clone and Setup

```bash
# Navigate to website directory
cd website

# Initialize theme submodule
git submodule update --init --recursive
```

### 2. Run Local Server

```bash
# Start Hugo development server
hugo server

# With drafts
hugo server -D
```

The site will be available at: http://localhost:1313

### 3. Build for Production

```bash
# Build static site
hugo

# Output will be in public/ directory
```

## Project Structure

```
website/
├── content/               # Website content (Markdown)
│   ├── _index.md         # Homepage
│   ├── about.md          # About page
│   ├── features.md       # Features page
│   ├── pricing.md        # Pricing page
│   └── blog/             # Blog posts
├── themes/                # Hugo themes
│   └── hugo-universal-theme/  # Universal theme (submodule)
├── static/                # Static assets
│   ├── css/              # Custom styles
│   ├── js/               # Custom scripts
│   └── img/              # Images
├── layouts/               # Custom layout overrides
├── config.toml            # Hugo configuration
└── README.md              # This file
```

## Content Management

### Editing Pages

1. **Homepage**: Edit `content/_index.md`
2. **About**: Edit `content/about.md`
3. **Features**: Edit `content/features.md`
4. **Pricing**: Edit `content/pricing.md`

### Adding Blog Posts

```bash
# Create new blog post
hugo new blog/my-post.md
```

Front matter template:
```markdown
---
title: "Post Title"
date: 2024-01-01
author: "Author Name"
description: "Brief description"
featured_image: "/img/post-image.jpg"
tags: ["firebase", "react", "saas"]
---

Post content here...
```

### Images

Place images in `static/img/` and reference:
```markdown
![Alt text](/img/image.png)
```

## Configuration

Main configuration in `config.toml`:

```toml
baseURL = "https://fireact.dev/"
languageCode = "en-us"
title = "Fireact.dev"
theme = "hugo-universal-theme"

[params]
  description = "Open-source SaaS framework"
  author = "Fireact.dev Team"

  # Contact info
  email = "contact@fireact.dev"

  # Social media
  github = "https://github.com/fireact-dev"

  # Features
  [params.features]
    enable = true

  # Testimonials
  [params.testimonials]
    enable = true
```

## Customization

### Styling

Add custom CSS in `static/css/custom.css`:

```css
/* Custom styles */
.custom-section {
  /* Your styles */
}
```

Link in config.toml:
```toml
[params]
  custom_css = ["css/custom.css"]
```

### Layouts

Override theme layouts by creating files in `layouts/` matching the theme structure:

```
layouts/
├── index.html           # Homepage override
├── _default/
│   └── baseof.html     # Base template override
└── partials/
    └── nav.html        # Navigation override
```

### Theme Colors

Modify theme colors in `static/css/custom.css`:

```css
:root {
  --primary-color: #your-color;
  --secondary-color: #your-color;
}
```

## Features

### Homepage Sections

Configure sections in `config.toml`:

```toml
[[params.features.items]]
  title = "Feature Title"
  description = "Feature description"
  icon = "fa fa-icon"

[[params.testimonials.items]]
  text = "Testimonial text"
  name = "Customer Name"
  position = "Title"
  company = "Company"
```

### Navigation Menu

Configure menu in `config.toml`:

```toml
[[menu.main]]
  name = "Home"
  url = "/"
  weight = 1

[[menu.main]]
  name = "Features"
  url = "/features/"
  weight = 2

[[menu.main]]
  name = "Docs"
  url = "https://docs.fireact.dev"
  weight = 3
```

### Footer

Configure footer in `config.toml`:

```toml
[params.footer]
  enable = true
  copyright = "© 2024 Fireact.dev"

  [[params.footer.links]]
    title = "Resources"
    [[params.footer.links.items]]
      name = "Documentation"
      url = "https://docs.fireact.dev"
```

## Deployment

### Firebase Hosting

```bash
# Build
hugo

# Deploy
firebase deploy --only hosting
```

### Netlify

1. Connect repository to Netlify
2. Build command: `hugo`
3. Publish directory: `public`
4. Deploy

### GitHub Pages

```bash
# Build
hugo

# Push to gh-pages
git subtree push --prefix public origin gh-pages
```

### Vercel

1. Import repository to Vercel
2. Framework: Hugo
3. Build command: `hugo`
4. Output directory: `public`

## Development

### Live Reload

Hugo automatically reloads when files change:

```bash
hugo server
# Make changes and see them instantly
```

### Draft Content

Preview draft content:

```bash
hugo server -D
```

Mark content as draft in front matter:
```markdown
---
draft: true
---
```

### Future Content

Schedule future posts:
```markdown
---
date: 2024-12-31
---
```

Build with future content:
```bash
hugo --buildFuture
```

## Maintenance

### Updating Theme

```bash
cd themes/hugo-universal-theme
git checkout main
git pull origin main
cd ../..
git add themes/hugo-universal-theme
git commit -m "website: update theme"
```

### Optimizing Images

Before adding images, optimize them:

```bash
# Install imagemin-cli
npm install -g imagemin-cli

# Optimize
imagemin static/img/*.{jpg,png} --out-dir=static/img/
```

### Checking Links

```bash
# Check for broken links
hugo server &
wget --spider -r -nd -nv -o wget.log http://localhost:1313
grep -B 2 '404' wget.log
```

## Troubleshooting

### Theme Not Found

Initialize submodules:
```bash
git submodule update --init --recursive
```

### Build Errors

Clean and rebuild:
```bash
rm -rf public/
hugo --cleanDestinationDir
```

### Port Already in Use

Use different port:
```bash
hugo server -p 1314
```

### Changes Not Reflecting

Clear browser cache or use incognito mode.

## Content Guidelines

### Writing Style

- Clear and concise
- Professional tone
- Action-oriented
- Benefits-focused

### SEO Best Practices

- Use descriptive titles
- Add meta descriptions
- Include alt text for images
- Use proper heading hierarchy
- Internal and external links

### Image Guidelines

- Use high-quality images
- Optimize for web (< 200KB)
- Use WebP format when possible
- Include descriptive filenames
- Add alt text

## Resources

- **Hugo Documentation**: https://gohugo.io/documentation/
- **Universal Theme**: https://github.com/devcows/hugo-universal-theme
- **Hugo Forum**: https://discourse.gohugo.io/
- **Main Project**: https://github.com/fireact-dev/fireact.dev

## Contributing

To contribute to the website:

1. Fork the repository
2. Create a feature branch
3. Make changes
4. Test locally with `hugo server`
5. Submit pull request

See [CONTRIBUTING.md](../CONTRIBUTING.md) for detailed guidelines.

## License

This website is open source and available under the [MIT License](../LICENSE).
