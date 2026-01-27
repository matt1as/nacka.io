# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a minimal single-page static site (`index.html` only) that serves as a personal digital hub - pulling together Mastodon posts and SAP Community blog posts into one unified presence at nacka.io.

**Core Philosophy**: Signal over noise. Minimal, clean, zero dependencies.

## Architecture

### Single-File Structure
All code lives in `index.html`:
- **HTML**: Semantic structure with three main cards (landing, Mastodon feed, SAP feed)
- **CSS**: Embedded `<style>` block with dark theme, glassmorphism effects, and responsive design
- **JavaScript**: Three IIFE modules at the bottom for feeds and Easter eggs

### Design System
CSS custom properties in `:root`:
- `--bg`: Dark background (#0b0f14)
- `--fg`: Foreground text color
- `--muted`: Subdued text (65% opacity)
- `--hair`: Subtle borders (12% opacity)
- `--glow`: Accent color (cyan: rgba(120, 215, 255, .18))
- `--max`: Max content width (980px)

Visual style: Dark minimalist with glassmorphism cards, radial gradients, and cyan/purple accent colors.

### Feed Integrations

**Mastodon Feed** (`#mastodon-feed`):
- Fetches from `https://social.nacka.io/api/v1/timelines/public`
- Shows 7 recent local posts (no replies)
- Content sanitization: removes dangerous elements, ensures safe links
- Media rendering disabled (returns empty string) for minimal aesthetic
- Clickable post cards via stretched link pattern (`:before` pseudo-element)

**SAP Community Feed** (`#sap-feed`):
- Fetches via CORS proxy: `https://corsproxy.io/?` + encoded Khoros API URL
- LiQL query for user "Mattias", blog posts only, 5 most recent
- Strips HTML from teaser text, truncates to 150 chars
- No avatars, emphasis on blog titles and content

### Easter Eggs

**90s Retro Mode**:
- Trigger: Click copyright year 5 times (within 2 seconds)
- Toggles `retro-mode` class on body
- Persists in localStorage
- Effects: Comic Sans, rainbow backgrounds, neon colors, scrolling marquee, visitor counter

## Development Workflow

### Testing
Open `index.html` directly in browser:
```bash
open index.html
```

No build step, no dependencies, no server required.

### Deployment
This site is deployed to Cloudflare Pages. Commits to `main` trigger automatic deployments.

### Making Changes

**Styling**:
- All CSS is in the `<style>` block
- Use existing CSS custom properties for consistency
- Maintain glassmorphism aesthetic (backdrop-filter, subtle borders, glow effects)
- Ensure hover states have smooth transitions (0.2-0.3s)

**Feed Updates**:
- Mastodon API: Direct access, no auth required (public timeline)
- SAP API: Requires CORS proxy due to browser restrictions
- Both feeds follow same pattern: loading → fetch → render/error
- Always sanitize/escape user-generated content

**Text Content**:
- Keep copy minimal and evocative (not technical jargon)
- Current theme: "A digital home away from home"
- Footer shows dynamic year and "status: live"

### Git Workflow

**Branching**:
- `main` is production (auto-deploys)
- Feature branches: `feature/description`
- Fix branches: `fix/description`
- Update branches: `update/description`

**Committing**:
- Use descriptive commit messages
- Include `Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>` in footer
- Prefer atomic commits (one logical change per commit)

**Pull Requests**:
- Create PRs using `gh pr create` with descriptive body
- Include summary, features, and rationale
- Test by opening index.html in browser before pushing

## Common Patterns

### Adding New Features
1. Read existing code structure first
2. Match existing patterns (IIFEs for JS, BEM-like CSS naming)
3. Keep minimal aesthetic (no unnecessary elements)
4. Add hover states and transitions for interactivity
5. Test in browser with `open index.html`
6. Ensure no layout shifts on interactions

### Hover Effects
Always include smooth transitions:
```css
.element {
  transition: all 0.2s ease;
}
.element:hover {
  color: rgba(120, 215, 255, .85);
}
```

### Feed Card Pattern
Each feed follows this structure:
- `.card.feed-card` wrapper
- `.topline` with mark and metadata
- Container div with loading/error/content states
- `.feed-footer` with external link

### Clickable Cards
Use stretched link pattern (not pointer-events on parent):
```css
.post {
  position: relative;
}
.post-link::after {
  content: '';
  position: absolute;
  inset: 0;
  z-index: 1;
}
/* Ensure content links are above */
.post-content a {
  position: relative;
  z-index: 2;
}
```

## Key Constraints

- **No external dependencies**: Vanilla JS, no libraries
- **No media in feeds**: Images disabled for minimal aesthetic
- **Single HTML file**: All code must stay in index.html
- **Static deployment**: No server-side logic
- **CORS awareness**: SAP API requires proxy, Mastodon doesn't
- **Security**: Sanitize all user-generated content from feeds

## APIs Used

### Mastodon API
- Endpoint: `https://social.nacka.io/api/v1/timelines/public`
- Parameters: `?limit=7&local=true`
- No authentication required
- CORS-enabled

### SAP Community (Khoros) API
- Endpoint: `https://community.sap.com/api/2.0/search`
- Query language: LiQL (SQL-like)
- No CORS support → requires proxy
- Proxy: `https://corsproxy.io/?` + encoded URL

## Responsive Design

Mobile breakpoint at 640px:
- Smaller avatars (38px vs 42px)
- Reduced font sizes for post content
- Same layout, adjusted proportions
