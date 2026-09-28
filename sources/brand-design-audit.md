# Brand Design Audit Guide

*Process for senior developers/architects to go from generic "AI slop" to brand-specific, high-end web apps by cloning validated architectures and customizing them via AI-assisted workflows.*

- Goal: use validated architectures as baseline → drastically shorter time-to-market without sacrificing technical integrity

## 1. Strategic Foundation: No More "Blank Canvas"

- Starting from an empty wireframe (in 2024) = budget-burning ego trip, not creativity
- Platforms like CoinMarketCap (CMC) already invested millions in UX validation → clone an architecture that already converts millions of users
- **Baseline Validation:** start directly from a functional structure; focus on high-end customization

| Aspect | Traditional Design | AI-Assisted Architecture Cloning |
|---|---|---|
| Starting point | Blank page (high error risk) | Validated high-performance UI (e.g. CMC) |
| UX confidence | Expensive A/B tests required | Based on proven conversion patterns |
| Time-to-market | Weeks (wireframing/prototyping) | Hours (extraction of production-ready assets) |
| Development focus | Searching for basic structure | Strategic contextualization & polish |

- **Takeaway:** turn mere inspiration into deep contextualization by using proven architectures as technical anchor

## 2. Phase I: Deep Context – The "Relentless Interview" Workflow

- Deep project context = only way to avoid "AI slop" (soulless, generic code)
- **"Grill Me" skill:** puts AI into "relentless interviewing" mode
  - AI must not guess; keeps questioning the architect until the vision is fully captured

### Core Components of Spec-Driven Development

- **`spec.md` (source of truth):** aggregated summary of all features and tech stack
- **`decisions.md` (context logbook):** logs every design and logic decision
  - Prevents "context drift" across hundreds of prompts
  - Serves as long-term project memory
- **Iterative sharpening:** AI validates assumptions via targeted follow-up questions instead of guessing

## 3. Phase II: Architecture Extraction and Deep Research

- **Deep Research skill:** understand underlying infrastructure, not just copy surfaces
- Identify open-source libraries for charts, tables, navigation already running stably on high-traffic sites like CMC

### Cloning Checklist

1. **URL deep scan:** analyze target architecture (e.g. dashboard and detail pages)
2. **Library audit:** identify open-source components used (charts, UI kits)
3. **Repository structure:** initialize a clean `web/` folder
4. **Baseline build:** create 1:1 functional copy as technical foundation

- **Takeaway:** identifying existing libraries → inherit robust codebase and working data pipelines instead of debugging custom components

## 4. Phase III: Systematic Restyling with "Impeccable" & Worktrees

- **Impeccable framework** (Start, Iterate, Polish): transforms clone into unique product
- **Git worktrees:** view parallel design variations simultaneously on different ports (e.g. 5173, 5175)
  - True side-by-side comparison without branch hopping

### Four Levels of Visual Variation

- **Small (Retint):** minimal token changes (colors, radii)
- **Medium (Skin Change):** swap typography and UI elements (buttons, cards)
- **Large (New Visual World):** complete visual redefinition (e.g. "newspaper style"); UX skeleton stays stable
- **Surprise:** AI-driven wildcard proposals based on `spec.md`

- **Takeaway:** worktrees reduce friction in stakeholder reviews; directions compared in real time → risk of wrong decisions near zero

## 5. Phase IV: Technical Audit & Accessibility Polish

- Eliminate hardcoded styles (e.g. CMC-specific purple tones) → move consistently into design tokens

### Audit Checklist for Senior Architects

- [ ] **Tokenization:** all hardcoded hex codes replaced by CSS variables/tokens?
- [ ] **Dark mode contrast:** buttons and pill elements clearly distinguishable on dark background?
- [ ] **Chart visibility:** chart lines switched from "original purple" to brand colors (e.g. yellow)?
- [ ] **Hover & active states:** all interactive elements have consistent feedback states?
- [ ] **Social asset audit:** all social icons (X, Telegram) high-contrast visible in dark/light theme?

## 6. Phase V: High-End Motion Design with Higgsfield.ai (Credit-Optimized)

- Scroll animations for Apple-like premium feel
- Video generation is expensive (up to 270 credits per 30s) → credit-optimization workflow

### Animated Website Skill Workflow

1. **Storyboard sketching:** generate 2x3 character sheets/sketches (~4–14 credits) to fix keyframes visually before expensive rendering
2. **Video analysis:** frame-by-frame analysis of a reference video for motion dynamics
3. **Prompting (Seedance 2.5):** create final sequence based on validated storyboard
4. **MP4-to-scroll conversion:** convert MP4 into scroll-based animation for landing pages or about sections

- **Takeaway:** dynamic backgrounds strongly increase time-on-site and perceived brand value – difference between "web app" and "high-end product"

## 7. Phase VI: Deployment via Cloudflare

- Cloudflare preferred over Vercel for AI-native workflows:
  - Smooth integration of private repositories
  - Faster build cycles

### Deployment Sequence

1. **CLI authorization:** `wrangler login` (authorizes the agent)
2. **Production build:** `npm run build` (generates optimized assets)
3. **Go live:** direct upload via Cloudflare CLI → generates stakeholder URL

## Conclusion

- Architecture cloning + spec-driven customization = most efficient way to build high-end software
- Leverage stability of proven platforms; refine via deep context, Git-based variations, and motion design into a unique brand
- Result: not a clone but a technically superior evolution
