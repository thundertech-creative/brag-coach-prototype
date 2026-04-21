# Brag Coach Widget: Design Notes & Open Questions
## For internal reference; not a client deliverable

---

## Changes in v4/v5 (post-client feedback)

### What got killed
- Omnibar/search pill integration. Client said they can't integrate into the site search bar.
- Color-coded per-button borders. Satisfi's platform can't style individual buttons differently.
- Character peek/eyecatch animation. Cut for scope.
- Fake DC page backgrounds. Looked bad; replaced with plain backgrounds.
- Dark theme as the default. Brag mode is dark; travel mode stays white to match Satisfi's existing build.

### What's new
- Bottom-bar chat widget format (standard Satisfi embed).
- Dual mode: Travel Assistant (white, blue, Noto Sans) and Brag Coach (dark, orange/green accents).
- Mode switch via full-width button in consistent position in both modes.
- Category dividers (colored top-border lines with labels) replace per-button color coding.
- Crossfade background transition + squash-stretch avatar pop on mode switch.
- Widget width: 506px (15% wider than standard 440px Satisfi widget).

---

## Open Questions

### "READY. SET. BRAG." tagline
STATUS: UNCONFIRMED. This appears on the physical board DC photographed, but the Satisfi build uses "Destination Cleveland Brag Coach" as the header. We used "READY. SET. BRAG." because it has more energy and matches the board aesthetic. Needs confirmation from DC whether this is approved copy for the chatbot or just event collateral.

### Cleveland Script logo
STATUS: PLACEHOLDER. The prototype uses italic serif text "Cleveland" as a stand-in. The actual Cleveland Script logo (trademarked, from the grainville font family) needs to be provided as a transparent SVG or PNG by DC's brand team. The safari-pinned-tab.svg from the DC site is a filled square, not usable for overlaying on the black header.

ACTION: Ask DC or Satisfi for the actual logo asset file used in the travel mode header (visible in the Satisfi build screenshot).

### Widget width
Current: 506px. Standard Satisfi widget appears to be ~400-440px. We went 15% wider per Jet's request. This may need negotiation with Satisfi if their embed framework constrains width. Mobile will be 100% viewport width regardless.

### "Brag Coach" naming visibility
FEEDBACK: "I'm not sure users will have context for that switch, nobody will know what a brag coach is."

RECOMMENDATION: Don't lead with the name. On standard pages, the travel assistant is default. The brag mode appears as a secondary option with action-oriented copy like "Talk trash about Cleveland" rather than "Chat with Brag Coach." The name "Brag Coach" can exist internally and in the brag mode header, but the entry point button should describe what it does, not what it's called.

On brag-oriented landing pages (social campaign links, QR codes, annual meeting), the widget starts in brag mode. No naming problem there because the user arrived with intent.

### Entry point by URL
CONFIRMED: Widget can customize based on entry point URL. This means:
- thisiscleveland.com/things-to-do/music → could start in brag mode or show brag-heavy prompts
- thisiscleveland.com/events/event-calendar → standard travel mode with event prompts
- campaign landing page → starts directly in brag mode

This aligns with our prompt system architecture spec (Layer 2: page-context prompts).

### Category dividers vs per-button colors
Since Satisfi can't do per-button colors, we use colored top-border lines as section dividers between prompt groups. The divider line uses the category color (orange for Things To Do, green for Events, blue for Eat & Drink, etc.) and has a small label. The buttons themselves are uniform in style.

Alternatives considered:
- Emoji prefixes on button text (functional but not brand-aligned)
- Grouped sections with no visual indicator (loses the category information entirely)

### Font decisions
- Noto Sans: All readable text (bot messages, user-facing copy, buttons, intro text). Matches the Satisfi travel mode.
- IBM Plex Mono: User input field text, small labels, category divider labels, technical/meta elements. Matches DC's body copy font from the brand bible but isn't used for extended reading.

Rationale: IBM Plex Mono at chat-widget sizes is hard to read for paragraphs of text. Noto Sans is what Satisfi already uses. Using it for body copy in both modes creates visual continuity across the mode switch while still allowing IBM Plex Mono to appear in accent positions.

### The mode switch animation
Sequence: 
1. Crossfade backgrounds (500ms ease)
2. After 300ms delay, avatar does squash-stretch pop (scales 80% → 112% → 92% → 103% → 100%, 500ms)
3. Input bar transitions colors (300ms)

This can be simplified or removed if Satisfi's platform can't support CSS animations. The crossfade alone (just swapping background colors) is the minimum viable version.

---

## Brand Bible Confirmed Values

### Colors (from July 2022 Brand Bible, page 38-39)
Primary:
- Classic Charcoal: #161616
- Stone White: #F7F7F4

Secondary:
- Electric Orange: #F56115 (site uses #f66116; negligible difference)
- Forest Hill Green: #5DD57A (site uses #5ed47b)
- Cuyahoga Skies: #2D6CF0 (site uses #2d6bf1)
- Rally Red: #EB3B26 (matches site exactly)

### Typography (from Brand Bible, page 30-31)
Primary typefaces:
- Spirits Sharp Regular (headlines, subhead, display)
- Landry Gothic Regular (headlines, subhead, display)
- IBM Plex Mono (primary body copy)

Display only:
- Amador Regular (blackletter)
- BN Grainville Script (the Cleveland script logo font)

### Brand Voice (page 14-15)
- Unapologetic
- Proud
- Bold
- Clever
- Passionate

### Brand Values (page 8-9)
- Determined
- Fun
- Connecting
- Creative
- Unpretentious

### Brand Promise (page 12-13)
"Cleveland inspires the underdog in all of us; by showing what you can do when you don't give up or take yourself too seriously."

---

## What still needs to happen

- [ ] Get real Cleveland Script logo asset (transparent SVG/PNG)
- [ ] Confirm "READY. SET. BRAG." as chatbot tagline
- [ ] Confirm widget width with Satisfi (can their embed support 506px?)
- [ ] Write final loading state micro-copy (4-6 rotating lines)
- [ ] Write final error state copy (2-3 variants)
- [ ] Finalize deflection copy and behavior
- [ ] Review prompt text with DC's content team
- [ ] Get hero image asset for travel mode welcome
- [ ] Written spec doc for Satisfi implementation
- [ ] QA testing plan for annual meeting
- [ ] Confirm which landing pages start in brag mode vs travel mode
