# Brag Coach Widget: Behavioral Spec

**Version:** v8
**Last updated:** April 15, 2026
**Prototype:** [brag-coach-v8.html](https://type37.github.io/brag-coach-prototype/brag-coach-v8.html)
**Repo:** [github.com/Type37/brag-coach-prototype](https://github.com/Type37/brag-coach-prototype)

This document describes every behavior in the prototype and what Satisfi Labs needs to replicate in production.

---

## What the Brag Coach is

A content generation tool for Cleveland advocacy. Users ask it to write Instagram captions, comebacks for haters, DMs to friends, TikTok ideas, and other shareable content celebrating Cleveland. It is not a support chatbot and not a search engine. Every design decision should be evaluated against one question: **does this help content leave the chat window?**

The widget lives inside Satisfi's standard bottom-bar embed on ThisIsCleveland.com pages. It coexists with DC's existing travel agent (the visitor experience chatbot Satisfi already operates). Users can switch between the two.

---

## Widget Structure

Top to bottom, the widget is five layers:

1. **Header** (60px, black, Cleveland Script vector logo centered, minimize button right-aligned)
2. **Mode Bar** (58px, contains a single full-width button)
3. **Panels Area** (fills remaining space; two panels overlap, one visible at a time)
4. **Input Bar** (text field + send button)
5. **Powered By Satisfi Labs** (small footer text)

The header and mode bar never move, never scroll, never change height. They are structural. The panels scroll independently below them.

---

## The Mode Bar

This is one button, not two. It sits between the header and the panels area. It is not inside either panel. It never shifts position because it has an explicit height (58px) and the button inside it has an explicit height (38px).

When the widget is in **travel mode**, the button reads "Chat with Brag Coach" in golden brown (#c4792b) on a white background. When the widget is in **brag mode**, the button reads "← Back to Travel Agent" as outlined text (#ddd) on the dark background (#161616).

Clicking the button triggers the mode switch animation and swaps the visible panel.

---

## Travel Mode (the DC Travel Agent)

This is Satisfi's existing visitor experience agent. We are not redesigning it. The prototype mimics its appearance using the actual CSS variables extracted from Satisfi's production build (pageID=10647).

**Visual:**
- Background: #F5F5F5
- Bot bubble: #dcdcdc, 12px border-radius
- User bubble: #fff
- Button pills: #2d6bf1 (DC Cuyahoga Skies blue), 50px border-radius
- Avatar: 80px blue circle with "DC" in IBM Plex Mono Bold
- Text label: "Travel Agent" in IBM Plex Mono 9px uppercase
- All body text: Noto Sans, 14px

**Welcome state shows:** avatar circle, intro paragraph (Satisfi's existing copy), then prompt buttons (Things to Do, Events, 2026 Brewery Passport, Trip Planner, Download Our App, Chat With Live CLE Concierge).

**Messages:** Standard chat layout. Bot messages have a blue DC avatar on the left. User messages are right-aligned white bubbles. Satisfi handles all response logic for this mode.

---

## Brag Mode

**Visual:**
- Background: #161616 (DC Classic Charcoal)
- Bot bubble: #222, 2px border-radius (sharp, not rounded)
- User bubble: #333
- Prompt pills: 1.5px solid #666 border, #ddd text, 50px radius
- Avatar: 80px circle with dashed #f56115 (DC Electric Orange) border, "?" in orange, "Coach" label
- All text: IBM Plex Mono (bot messages at 13px, prompts at 12px, display elements in Bold)
- The monospace treatment is a deliberate contrast with travel mode's Noto Sans

**Welcome state shows:** the coach avatar circle centered. Below the circle: "READY. SET." in small caps, "BRAG" in large IBM Plex Mono Bold, then the brand voice words as a single centered line: "Passionate. Bold. Clever. Unapologetic. Proud." in IBM Plex Mono 12px #aaa. Below that: "Your Cleveland trash talk machine." in green (#5dd57a), and "Your Brag Coach." in #888. The words form a vertical stack that reads as a statement of identity, not scattered decoration.

Below that, 4 prompt buttons whose text changes depending on which DC page the widget appears on.

---

## Page-Context Prompts

The brag mode prompts change based on the page URL. This is the only form of contextual awareness available without backend changes. Satisfi can detect which page the widget is embedded on and serve different prompt sets.

**Homepage:**
- Hype me up about Cleveland
- Give me a comeback for haters
- Write me an Instagram caption
- Craft a DM inviting my friend to visit Cleveland

**Events page:**
- Write a caption for tonight's event
- Roast someone who says CLE has nothing going on
- Hype up the Guardians home opener
- Tell me something fun this weekend

**Downtown page:**
- Caption for my E. 4th Street pic
- Comeback for "downtown is dead"
- Best brag about Playhouse Square
- Where should I eat downtown?

**Mabel's BBQ page:**
- Instagram caption for this brisket
- Why is Cleveland BBQ better than Texas?
- DM to send my friend about this place
- What else should I try downtown?

**Music/Rock Hall page:**
- Caption for my Rock Hall visit
- Roast someone who says CLE has no music scene
- Brag about the Cleveland Orchestra
- TikTok idea for a concert tonight

---

## Response Formatting

Responses from the brag coach are not generic chat bubbles. The formatting changes based on what the user asked for.

### Captions
Left border: 3px solid #f56115 (orange). Body text is the caption. Hashtags appear below in lighter blue (#5da8f5) at 12px. A "Copy" button sits at the bottom.

### Comebacks
Body text opens with an intro line. Individual comebacks appear in a stacked list, each in its own card (#2a2a2a background, #444 border). Each card has the comeback text on the left and an individual "Copy" button on the right. Users can copy any single comeback without grabbing the whole batch.

### DMs
A small "✉ Ready to paste" label in IBM Plex Mono uppercase sits above the DM text. A "Copy" button at the bottom. The label signals that this response is designed to be pasted directly into a messaging app.

### Default
Plain text response in the standard bot bubble styling. "Copy" button at the bottom.

### Deflection
When the user asks about something outside the coach's lane (weather, crime, LeBron, politics, directions, population), the response has a green left border (#5dd57a) and includes a "Try Travel Agent" link that switches modes.

Deflection text: "That's outside my lane. I'm your Cleveland brag coach: trash talk, captions, and comebacks."

### Error
Red left border (#eb3b26). Text in #ff6b5a for contrast. Message: "Hold on, even hype men need a second sometimes. Try again?"

### Loading
Orange italic text with a pulse animation. Randomly selected from:
- "Cooking up something good..."
- "Sharpening the trash talk..."
- "Consulting the Guardians..."
- "Checking with the bridge..."

---

## Follow-Up Prompts

After every bot response, two prompt buttons appear below the message. These are drawn from the current page's prompt pool, minus the one the user just tapped. Tapping a follow-up removes the previous set and starts a new exchange.

This is the single most important conversational design pattern in the widget. Without follow-ups, users hit a dead end after every response. The coach should always offer something to do next.

---

## Mode Switch Animations

Two options exist in the prototype for client review:

### Slide
The travel panel slides left out of frame while the brag panel slides in from the right (or vice versa). 350ms, cubic-bezier(.4,0,.2,1). The mode bar button updates its text and style at the start of the animation.

### Wipe
An orange bar (#f56115) sweeps across the widget from left to right. At the 180ms mark, the content behind it swaps. The bar exits right, revealing the new mode. 400ms total. The coach avatar does a squash-and-stretch pop animation after the bar clears.

The avatar pop animation on mode switch to brag: scales from 80% to 112% height, then oscillates to 92%, 103%, and settles at 100%. Cubic-bezier(.34,1.56,.64,1). Takes 500ms. Fires after the slide completes or after the wipe bar clears.

---

## Textures

### Paper (Travel Mode)
A tiled crumpled paper photograph at 500px repeat width over the #F5F5F5 background. Adds warmth. Applied via `background-image` (not `background` shorthand) to preserve the base color if the image fails to load.

### Dark Mode Textures (Brag Mode)
Three SVG tile patterns inspired by Guardian statuary motifs (Art Deco crosses, starbursts, lozenges, stars):

- **Whisper:** White elements at 4% opacity on #111114. Almost invisible.
- **Texture:** White elements at 7% opacity on #111114. Noticeable on close inspection.
- **Warm:** DC orange (#ea611f) elements at 5% opacity on #12100e. Adds a faint warm tint.

All three tile at 160px width. Applied via `background-image` to preserve the base #161616 color as a fallback.

---

## Minimize

The − button in the header collapses the entire widget to just the header bar (the Cleveland logo and the minimize button). Everything else disappears: mode bar, panels, input, footer. Clicking the button again expands the widget. Standard chat widget behavior.

---

## Auto-Scroll

Every time a message is appended to the messages area, the panel scrolls to the bottom. This includes: user messages, loading indicators, bot responses, and follow-up prompts.

---

## Typography

**Noto Sans** (loaded from Google Fonts): Travel mode body text, intro paragraphs, mode button text. Weights: 400, 500, 600, 700.

**IBM Plex Mono** (loaded from Google Fonts): The text entry field in brag mode, prompt buttons, scattered brand voice words, "READY. SET. BRAG.", "Your Cleveland trash talk machine," coach and agent labels, loading micro-copy, copy buttons, the "Ready to paste" DM label. Weights: 400, 500, 700.

Bot responses in both modes use Noto Sans. Readability wins over branding when someone is actually reading a paragraph of generated content. The IBM Plex personality lives in the input field (where the user types), the prompt buttons (where the user chooses), and the display elements (where the brand speaks). The responses themselves need to be as easy to read as possible because they're going to be copied and pasted somewhere else.

---

## Brand Colors (from DC Brand Bible, July 2022)

| Name | Hex | Usage |
|------|-----|-------|
| Classic Charcoal | #161616 | Brag mode background |
| Stone White | #F7F7F4 | (not used directly; Satisfi's #F5F5F5 is close) |
| Electric Orange | #F56115 | Coach avatar border, caption accent, wipe bar |
| Forest Hill Green | #5DD57A | Subtitle text, deflection accent, send button (brag) |
| Cuyahoga Skies | #2D6CF0 | Travel mode buttons, travel avatar |
| Rally Red | #EB3B26 | Error accent, scattered word color |

The golden brown (#c4792b) on the "Chat with Brag Coach" button is not a DC brand color. It was chosen to feel warm and inviting without competing with Electric Orange.

---

## Implementation Notes for Satisfi

This prototype serves as a visual and behavioral reference. The hardcoded responses stand in for what Satisfi's AI backend will generate in production. The following notes describe what the production build would need:

1. Replicate the dual-panel layout within their widget framework
2. Use the mode bar pattern (single button outside both panels) for switching
3. Serve different prompt sets based on the host page URL
4. Apply the response formatting rules (caption/comeback/DM/deflection/error) based on the response type their backend returns
5. Show follow-up prompts after every response
6. Implement the avatar pop animation on mode switch
7. Match the Satisfi CSS variables for travel mode (they already have these)
8. Apply the brag mode color scheme and typography

Areas where Satisfi's platform expertise will determine the best approach:
- Knowledge base content (determines response quality)
- Response classification (caption vs comeback vs DM vs deflection)
- Prompt rotation logic and timing
- Session persistence across page navigation
- Analytics and tracking

---

## Open Questions

- [ ] Widget width in production: 607px or Satisfi's standard 425px?
- [ ] Can Satisfi detect the host page URL and change prompts per page?
- [ ] What response metadata does Satisfi return? Can we use it for format detection?
- [ ] Copy/share buttons: does Satisfi's framework support clipboard API access?
- [ ] Dark mode textures: which one does DC prefer? Or none?
- [ ] Paper texture: include in production or prototype-only?
- [ ] Loading micro-copy: can Satisfi inject custom loading messages?
