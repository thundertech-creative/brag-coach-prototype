# Brag Coach: Prompt System Architecture Spec
## For Satisfi Labs Implementation

---

## What this document covers

How the suggested prompt buttons work, where the prompt data lives, who updates it, and what the technical architecture needs to support. This is a UX architecture question, not just a content question, because it determines whether the prompt system is hardcoded, config-driven, or needs a CMS backend.

---

## The four layers of prompts

The brag coach surfaces prompts in two places: the cycling placeholder text in the search/omnibar, and the clickable prompt buttons inside the chat. Both draw from the same underlying pool, but the pool has four distinct layers with different update frequencies and different ownership.


### Layer 1: Evergreen defaults

Prompts that work on any page, any time of year, for any user. These are the foundation.

Examples:
- "Give me a comeback for haters"
- "Write me an Instagram caption"
- "Hype me up about Cleveland"
- "Help me convince a friend to visit"

Update frequency: Rarely. Maybe once or twice a year to freshen up the wording.

Who owns it: Thunder Tech / Satisfi during initial build. DC reviews annually.

Technical requirement: Static. Can be hardcoded JSON in the front-end config or stored in Satisfi's platform as a permanent prompt set.


### Layer 2: Page-context prompts

Different prompts depending on what section of the site the user is browsing. These map to DC's existing content categories.

Things To Do pages (orange):
- "What should I brag about at [attraction name]?"
- "Make this look amazing for Instagram"

Events pages (green):
- "Write a caption for tonight's event"
- "Hype up this weekend to my group chat"

Eat & Drink pages (blue):
- "Instagram caption for this meal"
- "Why is Cleveland food better than [rival city]?"
- "DM to send my friend about this place"

Where To Stay pages (red):
- "Make my hotel look bougie"
- "Convince someone this neighborhood is worth staying in"

Neighborhoods pages:
- "Give me a comeback for someone who says [neighborhood] is boring"
- "Best brag about [neighborhood name]"

Update frequency: Infrequent. These are tied to page categories, not specific content. Adding a new restaurant to the site doesn't require a new prompt because "caption for this meal" works everywhere. Update when new categories are added or the tone needs refreshing.

Who owns it: Thunder Tech builds the initial set. DC reviews and approves.

Technical requirement: Static JSON keyed to page category. When the chat opens, it reads the current page's category (from URL path, meta tag, or data attribute) and loads the matching prompt set. No CMS needed. A config file or Satisfi platform setting works.

Key question for Satisfi: Can your platform read page-level metadata (URL, category, data attributes) at chat initialization and select a prompt set based on it? This is the context-awareness question from Jeremy's email to Scott Dressler.


### Layer 3: Timely / event-driven prompts

Prompts tied to real events, seasons, or cultural moments. These have expiration dates.

Examples:
- "Hype up the Guardians home opener" (valid April through October)
- "Write a post about the Cleveland International Film Festival" (valid during CIFF dates)
- "Brag about Cleveland in the snow" (valid November through March)
- "What should I say about the NFL Draft?" (valid only during draft week)

Update frequency: Ongoing. New events get added as they're announced. Old ones expire when the event passes.

Who owns it: DC's content or marketing team. They already maintain an event calendar on the site. The prompt pool should ideally draw from the same source.

Technical requirement: This is where static JSON breaks down. Two options:

Option A (preferred): Satisfi ingests DC's event calendar feed (likely available as RSS, JSON API, or iCal from Xperience by Kentico). Prompts are auto-generated or pre-mapped to event categories. When an event is live, its prompts enter the rotation. When the event passes, they drop out. No manual work after initial setup.

Option B (fallback): Satisfi provides an admin interface where DC's team can add/remove/schedule prompts manually. Each prompt gets a start date, end date, and page-category tag. A content calendar cadence (monthly or biweekly) ensures the pool stays fresh.

Option C (minimum viable): A Google Sheet or simple JSON file hosted on DC's server. Updated manually by DC's team. The chat loads this file on page load and merges timely prompts into the rotation. Low-tech but functional.

Key question for Satisfi: Does your platform support scheduled prompt sets with start/end dates? Can it ingest an external calendar or event feed?


### Layer 4: Cycling search bar text

The placeholder text that rotates inside the omnibar when the user hasn't clicked it yet. This serves as ambient advertising for the brag coach.

Examples by page:
- Homepage: "SEARCH OR CHAT WITH OUR ASSISTANT" / "WHAT'S HAPPENING THIS WEEKEND"
- Downtown: "EXPLORE DOWNTOWN CLEVELAND" / "BRAG ABOUT HOW GREAT THIS LOOKS"
- Music: "FROM ROCK TO BACH: CLEVELAND'S MUSIC SCENE" / "SAY HOW MUCH COOLER THIS IS THAN DETROIT"

Update frequency: Quarterly at most. These are flavor text, not functional prompts. Freshening them up seasonally is nice but not critical.

Who owns it: Thunder Tech writes, DC approves.

Technical requirement: Static JSON in the front-end config. Each page category gets an array of strings. The UI cycles through them on a timer (currently 3.5 seconds with a fade transition). No backend needed.

---

## Prompt selection logic

When the chat opens, the system selects prompts using this priority:

1. Check for timely/event prompts matching the current date and page category. If any exist, include 1 or 2 in the visible set.
2. Fill remaining slots with page-context prompts matching the current page category.
3. If fewer than 4 prompts are available from steps 1 and 2, fill with evergreen defaults.
4. Display 4 prompts total. (This number is a UX decision; 4 fits the layout without scrolling. Could go to 5 or 6 if the layout supports it.)

After the user sends their first message, follow-up prompt buttons use the same pool but exclude the prompt they just tapped. Show 2 follow-ups.


## Prompt pool size recommendations

Per category, maintain:
- 8 to 12 evergreen defaults
- 6 to 10 page-context prompts per category
- As many timely prompts as there are active events/seasons
- 4 to 6 cycling search bar strings per page category

The system randomly selects from the pool each time the chat opens, so even with a small pool, users see variation between sessions.


## Color coding

Prompts are color-coded by the DC content category they relate to:
- Orange outline (#f66116): Things To Do
- Green outline (#5ed47b): Events
- Blue outline (#2d6bf1): Eat & Drink
- Red outline (#eb3b26): Where To Stay
- Gray outline: General / no category (comebacks, generic captions)

This mapping is fixed and should be stored alongside each prompt in the data.


## Data format

Each prompt in the pool should have:

```
{
  "text": "Hype up the Guardians home opener",
  "category": "events",
  "color": "green",
  "layer": "timely",
  "start_date": "2026-03-15",
  "end_date": "2026-10-01",
  "page_types": ["events", "homepage"],
  "search_bar_variant": "HYPE UP THE GUARDIANS HOME OPENER"
}
```

Fields:
- text: The prompt button label
- category: DC content category (things-to-do, events, eat-and-drink, where-to-stay, general)
- color: Outline color mapping (orange, green, blue, red, gray)
- layer: Which layer this belongs to (evergreen, page-context, timely, search-bar)
- start_date / end_date: Only for timely prompts. Null for evergreen/page-context.
- page_types: Which page categories this prompt should appear on. Empty array means all pages.
- search_bar_variant: Optional uppercase version for the cycling omnibar text. Null if this prompt shouldn't appear in the search bar.


## Questions for Satisfi

1. Can the chat widget read page-level metadata (URL path, meta tags, data attributes) at initialization?
2. Does your platform support multiple prompt sets that can be activated/deactivated by date range?
3. Can your platform ingest an external event feed (RSS, JSON, iCal) to auto-populate timely prompts?
4. Is there an admin interface where DC's team can add/edit/schedule prompts without developer involvement?
5. What is the maximum number of prompt buttons the chat UI can display?
6. Can prompt buttons be styled with different border colors per-button, or only one color for all buttons?


## Content operations cadence (recommendation)

- Launch: Thunder Tech delivers the full evergreen and page-context prompt pool. Satisfi implements.
- Monthly: DC's content team reviews the timely prompt pool and adds/removes based on upcoming events. 30 minutes of work.
- Quarterly: Thunder Tech and DC review the evergreen and page-context pools. Refresh stale wording. 1 hour of work.
- Annually: Full audit of all prompt layers. Retire underperforming prompts (if analytics are available on prompt tap rates). Half a day.

---

## Summary

Layers 1, 2, and 4 are static config. Build once, update infrequently. No CMS needed.

Layer 3 (timely/event prompts) is the one that needs either an admin interface, an event feed integration, or a manual update process. This is the only part that requires ongoing content operations.

The worst-case minimum viable version: everything is static JSON, DC sends Thunder Tech a spreadsheet of timely prompts once a month, someone updates the config file. That works for launch. The ideal version: Satisfi's platform manages all four layers with an admin UI and date scheduling, and Layer 3 pulls from DC's event calendar automatically. Ask Scott's team which of these their platform can support.
