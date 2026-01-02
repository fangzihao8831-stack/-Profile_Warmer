# Profile Warmer System - Complete Blueprint

## Project Purpose

Autonomous browser profile warming system that:
- Runs **as long as user commits** browsing like a real human
- Builds **cookies and device reputation** over multiple days
- Warms up a **target site** for AVS (Address Verification System) bypass
- Is **completely undetectable** (no automation signatures)
- Works on **any website** using local vision LLM

---

## Core Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         PROFILE WARMER SYSTEM                           │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌──────────────┐    ┌──────────────┐    ┌───────────────────────────┐ │
│  │   PERSONA    │───▶│   SESSION    │───▶│    DECISION ENGINE        │ │
│  │   + TARGET   │    │   MANAGER    │    │    (Local Qwen2-VL)       │ │
│  └──────────────┘    └──────────────┘    └───────────────────────────┘ │
│         │                   │                        │                  │
│         ▼                   ▼                        ▼                  │
│  ┌──────────────┐    ┌──────────────┐    ┌───────────────────────────┐ │
│  │   INTEREST   │    │    STATE     │    │     VISION ENGINE         │ │
│  │   SYSTEM     │    │   TRACKER    │    │   (Screenshot Analysis)   │ │
│  └──────────────┘    └──────────────┘    └───────────────────────────┘ │
│                                                      │                  │
│                                                      ▼                  │
│                              ┌───────────────────────────────────────┐ │
│                              │         EXECUTION LAYER               │ │
│                              │  ┌─────────┐ ┌─────────┐ ┌──────────┐ │ │
│                              │  │ Mouse   │ │Keyboard │ │  Cookie  │ │ │
│                              │  │ Control │ │ Control │ │  Handle  │ │ │
│                              │  └─────────┘ └─────────┘ └──────────┘ │ │
│                              └───────────────────────────────────────┘ │
│                                                      │                  │
│                                                      ▼                  │
│                              ┌───────────────────────────────────────┐ │
│                              │           ADSPOWER BROWSER            │ │
│                              │  (Fingerprint, Timezone, Language,    │ │
│                              │   Proxy, WebGL, Canvas - ALL handled) │ │
│                              └───────────────────────────────────────┘ │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| Anti-detection Browser | **AdsPower** | ALL fingerprinting (timezone, language, fonts, WebGL, canvas, proxy, etc.) |
| Vision AI | **Qwen2-VL 7B** | Understand any webpage, find elements |
| DOM Reading | **Selenium** (read-only) | Get element positions, page state |
| Real Input | **PyAutoGUI** | Undetectable OS-level mouse/keyboard |
| Language | **Python 3.13** | Main runtime |
| GPU | **RTX 5080** (16GB VRAM) | Local LLM inference |

### Install Vision LLM

```bash
# Install Ollama from https://ollama.ai
# Then run:
ollama pull qwen2-vl:7b
```

This model:
- Fits in 16GB VRAM easily
- Fast inference (~1-2 seconds)
- Excellent at UI understanding
- Handles multiple languages (Spanish, English, etc.)

---

## Key Principle: Separation of Concerns

```
AdsPower   → Handles EVERYTHING about browser identity
             (fingerprint, timezone, language, proxy, cookies storage)

Selenium   → ONLY reads positions (passive, safe)
             Never clicks, never types

Local LLM  → ONLY makes decisions (what to do next)
             Analyzes screenshots, chooses actions

PyAutoGUI  → ONLY performs actions (real OS input)
             Mouse movement, clicks, typing - undetectable
```

**NEVER use Selenium's click() or send_keys() - they are detectable.**

---

## Project Structure

```
profile_warmer/
│
├── main.py                      # Entry point + CLI
├── config.yaml                  # Global configuration
├── requirements.txt             # Dependencies
│
├── core/
│   ├── __init__.py
│   ├── browser_controller.py    # AdsPower integration
│   ├── real_input.py            # PyAutoGUI mouse/keyboard
│   ├── element_finder.py        # Cascade element detection
│   ├── coordinate_translator.py # Selenium → Screen with DPI handling
│   ├── diagnostics.py           # Auto-diagnosis system (--diagnose)
│   └── debugger.py              # Structured logging
│
├── learning/
│   ├── __init__.py
│   └── interactive_learner.py   # --learn-interactive mode
│
├── persona/
│   ├── __init__.py
│   ├── persona.py               # Persona class
│   └── templates/
│       └── spanish_fashion.json # Prototype persona
│
├── session/
│   ├── __init__.py
│   ├── manager.py               # Session orchestrator (configurable duration)
│   ├── rhythm.py                # Natural browsing patterns
│   └── state.py                 # Session state tracking
│
├── target/
│   ├── __init__.py
│   ├── strategy.py              # Target visit planning
│   ├── tracker.py               # Progress tracking
│   └── readiness.py             # Purchase readiness check
│
├── brain/
│   ├── __init__.py
│   ├── decision_engine.py       # LLM decision making
│   ├── context.py               # Browsing context
│   └── prompts/
│       ├── next_action.txt
│       ├── page_analysis.txt
│       └── find_element.txt
│
├── vision/
│   ├── __init__.py
│   ├── analyzer.py              # Page analysis
│   ├── element_finder.py        # Find elements by description (VLM fallback)
│   └── ollama_client.py         # Qwen2-VL interface
│
├── execution/
│   ├── __init__.py
│   ├── mouse.py                 # Human-like mouse (Bezier curves)
│   ├── keyboard.py              # Human-like typing
│   ├── scroll.py                # Natural scrolling
│   └── coordinates.py           # Coordinate utilities
│
├── browser/
│   ├── __init__.py
│   ├── adspower.py              # AdsPower API integration
│   └── screenshot.py            # Screenshot capture
│
├── debug/
│   ├── __init__.py
│   ├── logger.py                # Structured action logging
│   ├── screenshots.py           # Screenshot capture + annotation
│   ├── dashboard.py             # Live terminal dashboard
│   └── replay.py                # Session replay + HTML reports
│
├── data/
│   ├── sites/
│   │   ├── ES.json              # Spanish sites by interest
│   │   └── general.json         # Major platforms
│   ├── search_terms/
│   │   └── ES_fashion.txt       # Spanish fashion searches
│   ├── selectors.json           # Learned selectors per domain
│   ├── profiles/
│   │   └── active_profiles.json
│   └── checkpoints/             # Crash recovery state
│
└── logs/
    ├── sessions/                # JSONL action logs
    ├── screenshots/             # Per-session screenshots
    ├── learning_sessions/       # Raw learning mode logs
    └── reports/                 # Generated HTML reports
```

---

## Persona Structure

**Note:** AdsPower handles timezone, language, fingerprint, proxy. Persona only defines behavior and interests.

```json
{
  "identity": {
    "name": "Carlos",
    "adspower_profile_id": "xxxxxx"
  },

  "interests": {
    "primary": ["fashion", "streetwear"],
    "secondary": ["music", "travel", "food"],
    "brands": ["Zara", "Mango", "Massimo Dutti", "Pull&Bear"],
    "avoid": ["sports", "gaming", "crypto"]
  },

  "browsing_behavior": {
    "style": "casual",
    "attention_span": "medium",
    "video_watcher": true,
    "shops_online": true
  },

  "target": {
    "site": "ralphlauren.es",
    "category": "fashion",

    "warming_config": {
      "min_visits_before_purchase": 4,
      "min_days_warming": 5,
      "min_category_sites_visited": 8,
      "min_total_sessions": 6
    },

    "related_sites": [
      "zara.com/es",
      "massimodutti.com",
      "mango.com",
      "pullandbear.com",
      "elcorteingles.es"
    ],

    "search_terms": [
      "ralph lauren espana",
      "polo ralph lauren hombre",
      "ralph lauren camisetas",
      "ralph lauren rebajas"
    ]
  },

  "warmup_status": {
    "current_phase": 1,
    "sessions_completed": 0,
    "target_visits": 0,
    "category_sites_visited": [],
    "total_browsing_hours": 0,
    "ready_for_purchase": false
  }
}
```

---

## Warming Strategy (Improved)

### The 4 Phases

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      AVS BYPASS WARMING STRATEGY                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  PHASE 1: Foundation (Sessions 1-2)                                     │
│  ├── Build cookies on major platforms                                   │
│  │   • Google.es (searches in Spanish)                                  │
│  │   • YouTube (watch Spanish content)                                  │
│  │   • Amazon.es (browse, don't buy)                                    │
│  ├── Accept ALL cookie banners (critical for tracking cookies)          │
│  ├── Natural scrolling and reading behavior                             │
│  └── NO target site visits yet                                          │
│                                                                         │
│  PHASE 2: Interest Building (Sessions 2-4)                              │
│  ├── Heavy focus on target CATEGORY sites                               │
│  │   • Zara.com/es, Mango.com, Massimo Dutti                           │
│  │   • Browse products, check sizes, add to wishlists                   │
│  ├── Build "fashion shopper" profile                                    │
│  │   • Visit fashion blogs, magazines                                   │
│  │   • Watch fashion YouTube videos                                     │
│  ├── Create shopping intent signals                                     │
│  │   • Add items to carts (abandon)                                     │
│  │   • Compare prices across sites                                      │
│  └── Still NO target site visits                                        │
│                                                                         │
│  PHASE 3: Target Introduction (Sessions 4-6)                            │
│  ├── First target visit via GOOGLE SEARCH (never direct)                │
│  │   • Search: "ralph lauren espana"                                    │
│  │   • Click organic result                                             │
│  │   • Browse 5-10 min, leave                                           │
│  ├── Second visit via RELATED SITE or Google Shopping                   │
│  │   • Longer session (10-15 min)                                       │
│  │   • View products, check sizes                                       │
│  ├── Third visit - show purchase intent                                 │
│  │   • Add to wishlist                                                  │
│  │   • Add to cart, start checkout, ABANDON                             │
│  ├── Continue visiting category sites between target visits             │
│  └── Mix target visits with normal browsing                             │
│                                                                         │
│  PHASE 4: Purchase Ready (Session 6+)                                   │
│  ├── Final target visit                                                 │
│  │   • Can be DIRECT (returning customer behavior)                      │
│  │   • Check cart/wishlist                                              │
│  ├── Profile now shows:                                                 │
│  │   • Multiple target visits over several days                         │
│  │   • Consistent fashion interest                                      │
│  │   • Shopping behavior across similar sites                           │
│  │   • Cart/wishlist activity on target                                 │
│  └── READY for real purchase with minimal AVS risk                      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Readiness Requirements

```python
def is_ready_for_purchase(status, config):
    """Check ALL conditions before purchase"""
    return (
        status.sessions_completed >= config.min_total_sessions and
        status.target_visits >= config.min_visits_before_purchase and
        status.days_since_first_session >= config.min_days_warming and
        len(status.category_sites_visited) >= config.min_category_sites_visited and
        status.has_cart_activity and
        status.has_wishlist_activity
    )

# Example check:
# sessions_completed: 6 >= 6 ✓
# target_visits: 4 >= 4 ✓
# days_warming: 5 >= 5 ✓
# category_sites: 10 >= 8 ✓
# cart_activity: True ✓
# wishlist_activity: True ✓
# RESULT: READY
```

---

## Session Flow (Configurable Duration)

Session duration is fully configurable per run. Below is an example rhythm for a 2-hour session, which scales proportionally for longer/shorter sessions.

### Phase Distribution in Single Session

```python
session_rhythm = {
    "warmup": {
        "duration_min": (10, 15),
        "activities": ["news", "social_check", "light_search"],
        "description": "Just sat down, starting slow"
    },

    "active_browsing": {
        "duration_min": (25, 40),
        "activities": ["interest_sites", "product_browsing", "video_watching"],
        "description": "Engaged with content"
    },

    "micro_break": {
        "duration_min": (1, 3),
        "activities": ["idle", "minimal_movement"],
        "description": "Quick mental break"
    },

    "deep_interest": {
        "duration_min": (20, 35),
        "activities": ["category_shopping", "comparison", "wishlist"],
        "description": "Focused on primary interest"
    },

    "distraction": {
        "duration_min": (5, 12),
        "activities": ["youtube_shorts", "random_browsing", "social"],
        "description": "Got distracted"
    },

    "target_visit": {
        "duration_min": (8, 20),
        "activities": ["browse_target", "view_products", "check_sizes"],
        "description": "Visit target site (if scheduled)",
        "condition": "only_in_phase_3_or_4"
    },

    "wind_down": {
        "duration_min": (8, 15),
        "activities": ["light_browsing", "final_scrolls"],
        "description": "Session ending naturally"
    }
}
```

### Example 2-Hour Session Timeline

```
00:00 - 00:12  │ WARMUP         │ Open Google.es, check news
00:12 - 00:45  │ ACTIVE         │ Search fashion trends, browse Zara
00:45 - 00:48  │ BREAK          │ Idle (simulated phone check)
00:48 - 01:15  │ DEEP INTEREST  │ Compare jackets on Mango, Massimo Dutti
01:15 - 01:25  │ DISTRACTION    │ YouTube shorts rabbit hole
01:25 - 01:28  │ BREAK          │ Idle
01:28 - 01:45  │ TARGET VISIT   │ Search "ralph lauren espana", browse
01:45 - 01:52  │ DISTRACTION    │ Back to YouTube
01:52 - 02:00  │ WIND DOWN      │ Light browsing, session ends
```

---

## LLM Decision Engine

### Decision Weight Distribution

The system uses a hybrid approach - pre-configured data provides constraints, LLM decides within them.

```
┌─────────────────────────────────────────────────────────┐
│                 BROWSING DECISIONS                       │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   PRE-CONFIGURED DATA          LLM DECIDES               │
│   (constraints)                (within constraints)      │
│                                                          │
│   • Site lists by category     • Which site to visit now │
│   • Search term lists          • What to search          │
│   • Persona interests          • When to leave a page    │
│   • Target warming rules       • What to click           │
│   • Session phase rules        • How long to stay        │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

**Note:** The exact weight distribution (LLM-heavy vs data-heavy) will be determined through testing. The system should be configurable to adjust this balance based on real-world performance and detection rates.

### How Qwen2-VL Decides Actions

```python
async def decide_next_action(self, screenshot, context):
    prompt = f"""
You are simulating a real person browsing the internet naturally.

CURRENT STATE:
- Browsing for: {context.session_duration} minutes
- Current site: {context.current_url}
- Session phase: {context.phase}
- Interests: {context.interests}

LOOKING AT THIS WEBPAGE:
[Screenshot attached]

What would a real person naturally do now?

OPTIONS:
1. SCROLL - Read more of this page (specify: small/medium/large)
2. CLICK - Click on something (describe what and why)
3. SEARCH - Search for something (provide query)
4. NAVIGATE - Go to a different site (provide url)
5. BACK - Go back to previous page
6. IDLE - Brief pause (person is thinking)

RULES:
- Be unpredictable like a real human
- Don't always pick the obvious choice
- Sometimes get distracted
- Consider if getting bored of current page
- Match the interests of the persona

Respond ONLY with JSON:
{{"action": "...", "target": "...", "reasoning": "brief explanation"}}
"""
    return await self.llm.query(screenshot, prompt)
```

### Finding Elements

```python
async def find_element(self, screenshot, description):
    prompt = f"""
Find this element in the screenshot: "{description}"

Return the coordinates where I should click.
Respond ONLY with JSON:
{{"found": true, "x": pixels_from_left, "y": pixels_from_top}}

If element not found:
{{"found": false, "reason": "why not found"}}
"""
    return await self.llm.query(screenshot, prompt)
```

---

## Human-Like Execution

### Mouse Movement (Bezier Curves)

```python
class HumanMouse:
    def move_to(self, x, y):
        """Move with natural curve, not straight line"""
        start = pyautogui.position()
        distance = self._calculate_distance(start, (x, y))

        # Natural duration based on distance
        duration = 0.2 + (distance / 1000) * 0.5
        duration += random.uniform(-0.1, 0.2)

        # Generate curved path
        control_points = self._random_control_points(start, (x, y))
        path = self._bezier_curve(start, control_points, (x, y))

        # Add micro-jitter
        path = [(p[0] + random.randint(-2, 2),
                 p[1] + random.randint(-2, 2)) for p in path]

        # Execute movement
        for point in path:
            pyautogui.moveTo(point[0], point[1], duration=0.01)

    def click(self, x=None, y=None):
        """Click with natural hesitation"""
        if x and y:
            self.move_to(x, y)

        # Pre-click pause (human hesitation)
        time.sleep(random.uniform(0.05, 0.2))

        pyautogui.click()

        # Post-click pause
        time.sleep(random.uniform(0.1, 0.3))
```

### Natural Typing

```python
class HumanKeyboard:
    def type_text(self, text):
        """Type with variable speed and occasional mistakes"""
        for i, char in enumerate(text):
            # Base delay
            delay = random.uniform(0.04, 0.12)

            # Slower after punctuation
            if i > 0 and text[i-1] in '.!?,;:':
                delay += random.uniform(0.15, 0.4)

            # Slight pause after space
            if i > 0 and text[i-1] == ' ':
                delay += random.uniform(0.02, 0.08)

            # 2% typo chance
            if random.random() < 0.02 and char.isalpha():
                nearby = self._nearby_key(char)
                pyautogui.write(nearby, interval=0.05)
                time.sleep(random.uniform(0.1, 0.25))
                pyautogui.press('backspace')
                time.sleep(random.uniform(0.05, 0.15))

            pyautogui.write(char, interval=delay)
```

### Natural Scrolling

```python
class HumanScroll:
    def scroll_read(self):
        """Scroll like reading content"""
        # Variable scroll amount
        scroll = random.randint(150, 400)
        pyautogui.scroll(-scroll)

        # Reading pause
        time.sleep(random.uniform(2, 6))

        # 12% chance to scroll back (re-reading)
        if random.random() < 0.12:
            pyautogui.scroll(random.randint(50, 150))
            time.sleep(random.uniform(1, 3))
```

---

## AdsPower Integration

```python
class AdsPowerBrowser:
    def __init__(self, api_url="http://localhost:50325"):
        self.api_url = api_url

    async def open_profile(self, profile_id):
        """Open AdsPower profile and get connection"""
        response = requests.get(
            f"{self.api_url}/api/v1/browser/start",
            params={"user_id": profile_id}
        )
        data = response.json()

        if data["code"] == 0:
            selenium_url = data['data']['ws']['selenium']
            return selenium_url
        else:
            raise Exception(f"Failed: {data['msg']}")

    async def connect_selenium(self, selenium_url):
        """Connect Selenium for READ-ONLY operations"""
        options = webdriver.ChromeOptions()
        options.add_experimental_option("debuggerAddress", selenium_url)
        driver = webdriver.Chrome(options=options)
        return driver

    async def close_profile(self, profile_id):
        """Close browser profile"""
        requests.get(
            f"{self.api_url}/api/v1/browser/stop",
            params={"user_id": profile_id}
        )
```

---

## Debugging System

### Action Logging

```python
# Every action creates log entry
{
    "timestamp": "2025-12-03T14:32:15",
    "session_id": "sess_abc123",
    "action_num": 47,
    "action": {
        "type": "click",
        "target": "Add to cart button",
        "coordinates": [845, 432]
    },
    "context": {
        "url": "https://zara.com/es/producto/12345",
        "phase": "deep_interest",
        "session_minute": 48
    },
    "llm": {
        "decision": "Click add to cart to show purchase intent",
        "confidence": "high"
    },
    "result": {
        "success": true,
        "page_changed": true
    },
    "screenshot": "logs/screenshots/sess_abc123/047_click.png"
}
```

### Live Dashboard

```
╔═══════════════════════════════════════════════════════════════════╗
║                   PROFILE WARMER - LIVE DEBUG                     ║
╠═══════════════════════════════════════════════════════════════════╣
║  Session: sess_abc123            Duration: 48 min                 ║
║  Phase: deep_interest            Actions: 47                      ║
╠═══════════════════════════════════════════════════════════════════╣
║  URL: https://zara.com/es/chaquetas                               ║
║  Last: CLICK → "View jacket details"                              ║
║  LLM: "This jacket matches the style I'm looking for"             ║
╠═══════════════════════════════════════════════════════════════════╣
║  WARMING PROGRESS                                                 ║
║  ├─ Sessions: 3/6                                                 ║
║  ├─ Target visits: 1/4                                            ║
║  ├─ Category sites: 5/8                                           ║
║  └─ Status: PHASE 2 - Building Interest                           ║
╠═══════════════════════════════════════════════════════════════════╣
║  [P] Pause  [R] Resume  [Q] Quit  [S] Screenshot  [L] View Log    ║
╚═══════════════════════════════════════════════════════════════════╝
```

### Debug Commands

```bash
# Run with full debugging
python main.py run --profile carlos_es --duration 2 --debug verbose

# Run auto-diagnosis (DPI, coordinates, browser connection, etc.)
python main.py --diagnose

# Interactive learning mode - learn selectors by watching you browse
python main.py --learn-interactive

# Test coordinate accuracy (calibration)
python main.py debug-coords

# Replay session with screenshots
python main.py replay sess_abc123

# Generate HTML report
python main.py report sess_abc123

# Check purchase readiness
python main.py status carlos_es
```

---

## Dependencies

```txt
# requirements.txt

# Browser
selenium>=4.15.0

# Input automation
pyautogui>=0.9.54
pillow>=10.0.0

# Local LLM
ollama>=0.1.0

# HTTP
requests>=2.31.0
aiohttp>=3.9.0

# Config & Logging
pyyaml>=6.0
loguru>=0.7.0

# CLI
click>=8.0.0
rich>=13.0.0

# Async
asyncio
```

---

## Setup Steps

### 1. Install Ollama + Qwen2-VL

```bash
# Download Ollama from https://ollama.ai
# Install it, then run:
ollama pull qwen2-vl:7b

# Verify it works:
ollama run qwen2-vl:7b "describe this image" --image test.png
```

### 2. AdsPower Setup

- Download AdsPower from adspower.com
- Create profile with:
  - Spanish proxy (your native IP is fine for prototype)
  - Spanish language
  - Spain timezone
  - Realistic fingerprint
- Note the **profile ID**

### 3. Project Setup

```bash
# Create project directory
mkdir C:\Users\fangz\Projects\profile_warmer
cd C:\Users\fangz\Projects\profile_warmer

# Create virtual environment
python -m venv venv
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 4. Create Persona

```bash
python main.py create-persona carlos_es --target ralphlauren.es
```

### 5. Run First Session

```bash
python main.py run --profile carlos_es --duration 2 --debug verbose
```

### 6. Check Progress

```bash
python main.py status carlos_es

# Output:
# ══════════════════════════════════════════
#  PROFILE: carlos_es
#  TARGET: ralphlauren.es
# ══════════════════════════════════════════
#  Sessions completed: 3/6
#  Target visits: 1/4
#  Days warming: 2/5
#  Category sites: 5/8
#  Cart activity: No
#  Wishlist activity: Yes
# ──────────────────────────────────────────
#  STATUS: NOT READY
#  Next: Complete 3 more sessions, visit
#        target 3 more times, add to cart
# ══════════════════════════════════════════
```

---

## Implementation Priority

### Phase 1: Foundation
1. [ ] DPI scaling detection (Windows)
2. [ ] Coordinate translation system (Selenium → Screen)
3. [ ] AdsPower connection via API
4. [ ] Selenium read-only connection
5. [ ] PyAutoGUI mouse with Bezier curves
6. [ ] PyAutoGUI keyboard with human patterns
7. [ ] Auto-diagnosis system (--diagnose)
8. [ ] Structured logging

### Phase 2: Learning System
9. [ ] Interactive learning mode (--learn-interactive)
10. [ ] Real-time domain tracking
11. [ ] Selector extraction and caching
12. [ ] Purpose inference (search_input, add_to_cart, etc.)
13. [ ] selectors.json management

### Phase 3: Element Finding (Cascade)
14. [ ] Selector cache lookup (instant)
15. [ ] Common pattern matching (fast)
16. [ ] Heuristic detection (medium)
17. [ ] Vision LLM fallback - Qwen2-VL (slow)
18. [ ] Auto-cache selectors from VLM results
19. [ ] Cookie banner detector + handler

### Phase 4: Brain
20. [ ] Decision engine (choose next action)
21. [ ] Context tracker (remember what we did)
22. [ ] Interest-based site selection
23. [ ] Search query generation

### Phase 5: Reliability
24. [ ] Crash recovery / checkpointing
25. [ ] Calibration routine
26. [ ] Error recovery
27. [ ] Element readiness delays (0.2-0.6s)

### Phase 6: Session
28. [ ] Session manager (configurable duration)
29. [ ] Phase/rhythm system
30. [ ] Natural timing and breaks
31. [ ] Search-first navigation (never direct URL)
32. [ ] Persona loader

### Phase 7: Target
33. [ ] Target visit scheduler
34. [ ] Visit progression (search → direct)
35. [ ] Tracker (visits, actions, dates)
36. [ ] Readiness checker

### Phase 8: Polish
37. [ ] Debug dashboard
38. [ ] Session replay
39. [ ] HTML reports

---

## Critical Rules

### NEVER
- Never use `driver.click()` or `driver.send_keys()`
- Never move mouse in straight lines
- Never visit target directly before Phase 3
- Never buy on target until READY status
- Never skip cookie banners (we WANT those cookies)
- Never navigate directly to URLs (bot-like) - always search first

### ALWAYS
- Always use PyAutoGUI for all interactions
- Always move mouse in curves
- Always add random delays (0.2-0.6s before clicks)
- Always arrive at sites via Google search first (natural referrer)
- Always log every action with screenshot
- Always check readiness before real purchase
- Always retrieve DPI scale at startup

---

## Success Metrics

| Metric | Requirement |
|--------|-------------|
| Sessions before purchase | >= 6 |
| Target visits before purchase | >= 4 |
| Days of warming | >= 5 |
| Category sites visited | >= 8 |
| Detection rate | 0% (no captchas) |
| Session completion rate | > 95% |

---

*Build trust over days, not hours. The goal is making the profile look like a genuine interested customer who naturally discovered the target site.*

---

# EXTENDED TECHNICAL SPECIFICATIONS

The following sections contain detailed implementation specifications.

---

## DPI SCALING HANDLING (WINDOWS)

### The Problem
- Windows DPI set to 125%
- Selenium reports element at (100, 200) in logical pixels
- PyAutoGUI operates in physical pixels
- Clicking at (100, 200) misses by 25%

### Solution: Retrieve DPI at Startup

```python
import ctypes

def get_windows_dpi_scale():
    """Get Windows DPI scaling factor - call once at startup"""
    try:
        # Make process DPI aware first
        ctypes.windll.shcore.SetProcessDpiAwareness(2)
    except:
        pass

    # Get scale factor
    scale = ctypes.windll.shcore.GetScaleFactorForDevice(0) / 100
    return scale  # Returns 1.25 for 125%, 1.5 for 150%, etc.

# Initialize at program start
DPI_SCALE = get_windows_dpi_scale()

def logical_to_physical(x, y):
    """Convert logical coords to physical screen coords"""
    return int(x * DPI_SCALE), int(y * DPI_SCALE)
```

---

## COORDINATE TRANSLATION (Selenium → PyAutoGUI)

### Full Translation Chain

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COORDINATE TRANSLATION                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  STEP 1: Selenium gives element position (relative to page)         │
│          element.location = {'x': 450, 'y': 180}                    │
│          element.size = {'width': 200, 'height': 40}                │
│                                                                      │
│  STEP 2: Get browser window position on screen                      │
│          window_x = driver.execute_script("return window.screenX")  │
│          window_y = driver.execute_script("return window.screenY")  │
│                                                                      │
│  STEP 3: Get browser chrome height (toolbar/address bar)            │
│          outer_h = driver.execute_script("return window.outerHeight")│
│          inner_h = driver.execute_script("return window.innerHeight")│
│          chrome_height = outer_h - inner_h  (~80-120px)             │
│                                                                      │
│  STEP 4: Account for scroll position                                │
│          scroll_y = driver.execute_script("return window.scrollY")  │
│          scroll_x = driver.execute_script("return window.scrollX")  │
│                                                                      │
│  STEP 5: Calculate center of element on screen                      │
│          screen_x = window_x + elem_x - scroll_x + (elem_w / 2)     │
│          screen_y = window_y + chrome_height + elem_y - scroll_y    │
│                     + (elem_h / 2)                                  │
│                                                                      │
│  STEP 6: Apply DPI scaling                                          │
│          final_x = int(screen_x * DPI_SCALE)                        │
│          final_y = int(screen_y * DPI_SCALE)                        │
│                                                                      │
│  STEP 7: PyAutoGUI clicks at (final_x, final_y)                     │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Reference Implementation

```python
def get_element_screen_coords(driver, element):
    """Convert Selenium element to PyAutoGUI screen coordinates"""

    # Element position relative to page
    elem_x = element.location['x']
    elem_y = element.location['y']
    elem_w = element.size['width']
    elem_h = element.size['height']

    # Browser window position on screen
    window_x = driver.execute_script("return window.screenX")
    window_y = driver.execute_script("return window.screenY")

    # Browser chrome height (toolbars, address bar)
    outer_h = driver.execute_script("return window.outerHeight")
    inner_h = driver.execute_script("return window.innerHeight")
    chrome_height = outer_h - inner_h

    # Current scroll position
    scroll_y = driver.execute_script("return window.scrollY")
    scroll_x = driver.execute_script("return window.scrollX")

    # Calculate center of element on screen
    screen_x = window_x + elem_x - scroll_x + (elem_w / 2)
    screen_y = window_y + chrome_height + elem_y - scroll_y + (elem_h / 2)

    # Apply DPI scaling
    screen_x = int(screen_x * DPI_SCALE)
    screen_y = int(screen_y * DPI_SCALE)

    return screen_x, screen_y
```

---

## ELEMENT FINDING CASCADE

Vision LLM is slow (~1 second per inference). Use faster methods first, Vision LLM as fallback only.

### Strategy

```
┌─────────────────────────────────────────────────────────┐
│                    ELEMENT FINDING                       │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  1. Check selector cache (instant, <10ms)               │
│     └─ selectors.json: {"amazon.com": {"search": "..."}}│
│                                                          │
│  2. Try common patterns (fast, <50ms)                   │
│     └─ Search boxes: input[type=search], #search, etc.  │
│     └─ Buttons: button:contains("Add"), [data-action]   │
│                                                          │
│  3. Heuristic detection (medium, <200ms)                │
│     └─ Find by text content                             │
│     └─ Find by position (top-right = cart, etc.)        │
│     └─ Find by visual hierarchy                         │
│                                                          │
│  4. Vision LLM fallback (slow, ~1000ms)                 │
│     └─ Screenshot → "Find the search box" → coords      │
│     └─ IMMEDIATELY cache the working selector           │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Vision LLM Model Options (RTX 5080 / 16GB VRAM)

| Model | VRAM | UI Accuracy | Speed |
|-------|------|-------------|-------|
| **Qwen2-VL-7B** | ~14GB | Very good | Fast |
| LLaVA-1.6-13B (4-bit) | ~10GB | Good | Medium |
| CogVLM2 (4-bit) | ~12GB | Good | Medium |
| Florence-2-large | ~4GB | Basic | Very fast |
| Moondream2 | ~4GB | Basic | Very fast |

**Recommendation**: Qwen2-VL-7B - best balance for UI element detection.

**Inference time**: 500ms-2000ms per screenshot. This is the time for the LLM to analyze the screen and return element coordinates. Use sparingly.

---

## ELEMENT READINESS DELAY

### The Problem
Element visible in DOM but JavaScript handlers not attached yet. Clicking too fast = no response.

### Solution
Wait 0.2-0.6 seconds after finding element, before clicking.

```python
def safe_click_delay():
    """Human-like pause before clicking - also ensures element ready"""
    time.sleep(random.uniform(0.2, 0.6))
```

This serves dual purpose:
1. Ensures JS handlers are attached
2. Mimics human reaction time (natural behavior)

---

## CRASH RECOVERY / CHECKPOINTING

Sessions can run for hours building cookies. Crashes shouldn't lose progress.

### What Gets Saved

```json
{
  "session_id": "abc123",
  "started_at": "2024-01-15T10:00:00",
  "profile_id": "adspower_xyz",
  "current_site_index": 26,
  "total_sites": 50,
  "completed_actions": [
    {"site": "amazon.com", "action": "searched", "completed": true},
    {"site": "amazon.com", "action": "added_to_cart", "completed": true}
  ],
  "cookies_snapshot": {...}
}
```

### Checkpoint Location

```
data/checkpoints/session_{id}.json
```

### Recovery Flow

```
Script crashes at hour 2
  ↓
Restart script
  ↓
"Found checkpoint: session_abc123"
"Resuming from site 26 of 50..."
  ↓
Continue from where it left off
```

---

## AUTO-DIAGNOSIS SYSTEM

Built-in diagnostics to identify issues automatically.

### Command

```bash
python main.py --diagnose
```

### Checks Performed

```
🔍 Running diagnostics...

  ✓ DPI Detection: DPI scale: 1.25
  ✓ Browser Connection: AdsPower connected
  ✓ Coordinate Accuracy: Clicks landing correctly
  ✓ Element Visibility: Elements detected properly
  ✓ PyAutoGUI Permissions: Input working
  ✓ Window Focus: Browser in foreground
  ✓ Selector Cache: 15 sites cached
```

### Coordinate Accuracy Test
Creates a test button at known position, clicks it, verifies click registered correctly. Identifies DPI/chrome height calculation issues.

### Error Logging with Context
When errors occur, captures without screenshots:
- Current URL and page title
- Scroll position
- Window size and position
- Summary of visible clickable elements
- Failing selector details

---

## INTERACTIVE LEARNING MODE

Learn selectors by watching real user behavior. The most versatile approach.

### Command

```bash
python main.py --learn-interactive
```

### Key Features

- **Real-time domain tracking**: Monitors `location.hostname` every 300ms
- **Auto re-inject on navigation**: Re-injects recorder script when page changes
- **Per-domain organization**: Actions tagged and grouped by domain automatically
- **Multiple selector fallbacks**: Generates ID, name, class, aria, text selectors
- **Purpose inference**: Guesses "search_input", "add_to_cart" from context

### User Flow

1. User logs into target sites before starting (important - logged-in UI differs from guest)
2. Start `--learn-interactive`
3. Browse freely across any sites
4. System tracks domain and records every action in real-time
5. Press Ctrl+Q when done
6. System saves selectors organized by domain

### Console Output

```
╔══════════════════════════════════════════════════════════╗
║         PROFILE WARMER - INTERACTIVE LEARNING            ║
╠══════════════════════════════════════════════════════════╣
║  Browse normally. I'm watching and learning.             ║
║                                                          ║
║  • Actions are recorded per domain automatically         ║
║  • Switch between sites freely - I'll track it           ║
║  • Press Ctrl+Q when completely done                     ║
╚══════════════════════════════════════════════════════════╝

📍 Now on: www.google.com
  ⚡ [www.google.com] Click: Search
  ⌨️  [www.google.com] Type in: text field
  ⚡ [www.google.com] Click: Google Search

📍 Now on: www.amazon.com
  📜 [www.amazon.com] Scroll: 15%
  ⚡ [www.amazon.com] Click: twotabsearchtextbox
  ⌨️  [www.amazon.com] Type in: text field
  ⚡ [www.amazon.com] Click: nav-search-submit-button
  📜 [www.amazon.com] Scroll: 45%
  ⚡ [www.amazon.com] Click: iPhone 15 Pro Max...
  ⚡ [www.amazon.com] Click: Add to Cart

📍 Now on: www.ebay.com
  ⚡ [www.ebay.com] Click: gh-ac
  ⌨️  [www.ebay.com] Type in: text field
  ...

[Ctrl+Q pressed]

==================================================
Recording complete!
==================================================

📁 www.google.com: 3 actions recorded
   • search_input: textarea[name="q"] (+1 fallbacks)
   • search_submit: button.gNO89b (+0 fallbacks)

📁 www.amazon.com: 8 actions recorded
   • search_input: #twotabsearchtextbox (+2 fallbacks)
   • search_submit: #nav-search-submit-button (+1 fallbacks)
   • add_to_cart: #add-to-cart-button (+1 fallbacks)
   • link_iphone_15_pro: [data-asin="B0CMZ4S8MN"] (+2 fallbacks)

📁 www.ebay.com: 5 actions recorded
   • search_input: #gh-ac (+1 fallbacks)
   • search_submit: #gh-btn (+0 fallbacks)

✅ Saved to data/selectors.json
📝 Raw log: logs/learning_sessions/session_20240115_143022.json
```

### Important Notes

- **Login first**: Always log into sites before learning - logged-in UI has different elements than guest view
- **Search-first navigation**: Don't type URLs directly. Search domain on Google, click result.
- **Automatic domain tracking**: No need to specify sites upfront - system detects where you are in real-time

---

## NAVIGATION STRATEGY

### Never Direct URL

Going directly to `https://amazon.com` is bot-like behavior.

### Always Search First

```
1. Go to Google
2. Type "amazon.com" in search
3. Click first organic result
4. Now on Amazon with natural referrer
```

This creates realistic:
- Referrer headers
- Navigation patterns
- Cookie acquisition flow

---

## LEARNED SELECTOR STORAGE FORMAT

### selectors.json (Enhanced)

```json
{
  "amazon.com": {
    "learned_at": "2024-01-15T14:30:22",
    "action_count": 15,
    "selectors": {
      "search_input": [
        {"type": "id", "value": "#twotabsearchtextbox", "priority": 1},
        {"type": "name", "value": "[name='field-keywords']", "priority": 2},
        {"type": "aria", "value": "[aria-label='Search']", "priority": 3}
      ],
      "add_to_cart": [
        {"type": "id", "value": "#add-to-cart-button", "priority": 1},
        {"type": "name", "value": "[name='submit.add-to-cart']", "priority": 2}
      ],
      "search_submit": [
        {"type": "id", "value": "#nav-search-submit-button", "priority": 1}
      ]
    }
  },
  "ebay.com": {
    "learned_at": "2024-01-15T14:45:10",
    "action_count": 8,
    "selectors": {
      "search_input": [
        {"type": "id", "value": "#gh-ac", "priority": 1}
      ],
      "search_submit": [
        {"type": "id", "value": "#gh-btn", "priority": 1}
      ]
    }
  }
}
```

---

## KNOWN CONSIDERATIONS

### DPI Scaling
- Retrieve at startup, apply to all coordinates
- Test with `--diagnose` if clicks miss

### Browser Chrome Height
- Varies by theme, extensions, bookmarks bar
- Calculated as: `outer_height - inner_height`
- May need calibration per AdsPower profile

### Element Readiness
- Elements may be visible but JS not attached
- Always wait 0.2-0.6s after finding element
- Check for element position stability if issues persist

### Vision LLM Latency
- 500ms-2000ms per inference
- Only use as last resort after selector methods fail
- Always cache working selectors immediately after VLM finds them
