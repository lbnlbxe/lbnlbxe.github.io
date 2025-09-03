# BXE Project Website

A Jekyll-based website for the BXE project, built with Bootstrap and following LBNL's visual identity guidelines.

## Color Scheme

This site uses the official LBNL color palette with **Yellow** as the accent color:
- **Dark Blue (#00313C)** - Primary brand color
- **Yellow** - Selected accent color
- **Teal (#007681)** - Links and interactions
- **Light Gray (#B1B3B3)** and **Dark Gray (#63666A)** - Supporting colors

## Setup

1. Install dependencies:
   ```bash
   bundle install
   ```

2. Run locally:
   ```bash
   bundle exec jekyll serve
   ```

3. Visit `http://localhost:4000` to view the site.

## Adding Pages

To add new pages to the navigation:

1. Create a new Markdown file in the `pages/` directory
2. Add the following frontmatter:
   ```yaml
   ---
   layout: default
   title: Page Title
   navbar: true
   order: 3
   ---
   ```
3. The page will automatically appear in the navigation bar

## Customizing Colors

The accent color is defined as `--lbnl-accent` in the CSS variables. To change it:

1. Edit `assets/css/main.scss`
2. Update the `--lbnl-accent` variable to point to your desired color
3. Available LBNL secondary colors: orange, green, yellow, red, dusty-rose, olive-green, blue, purple, burgundy

## Deployment

This site is configured for GitHub Pages deployment from the `gh-pages` branch.

## LBNL Brand Compliance

This site follows the [LBNL Visual Identity Guidelines](https://creative.lbl.gov/visual-identity/).
