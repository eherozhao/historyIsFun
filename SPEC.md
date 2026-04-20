# Project Spec: Chronicles of China (华夏春秋)

## Vision
A bilingual (Chinese/English) interactive website presenting Chinese history chronologically — from ancient legends through the founding of the PRC. Content is sourced from authentic historical texts with citations. The site features a **modern Chinese-aesthetic UX** that feels contemporary, not antiquated.

## Core Principles
1. **Content-first**: History content drives the experience; UI serves readability
2. **Bilingual**: Full Chinese and English support, togglable per-page
3. **Source-cited**: All historical content references primary sources (《史记》《资治通鉴》《二十四史》etc.)
4. **Modern Chinese aesthetic**: Minimalist design with subtle cultural cues — NOT heavy traditional ornamentation
5. **Iterative**: Ship early, improve continuously

---

## Tech Stack

| Layer | Choice | Rationale |
|-------|--------|-----------|
| Framework | **Astro 4.16+** | Content-first, SSG, file-based routing, Content Collections |
| Styling | **Tailwind CSS 3.4** | Utility-first, custom design tokens |
| Content | **Markdown** | `src/content/dynasty/zh/` and `src/content/dynasty/en/` |
| i18n | Route-based | `/zh/dynasty/:slug` and `/en/dynasty/:slug` |
| Deployment | **GitHub Pages** via GitHub Actions | Auto-deploy on push to `main` |
| Interactive | Vanilla JS in `<script>` blocks | No framework components — zero runtime dependencies |

---

## Design System

### Modern Chinese Aesthetic (v2)

**Philosophy**: "Less ink, more meaning" (惜墨如金). Think modern museum exhibition design — generous whitespace, restrained palette, typography-driven hierarchy, with cultural identity expressed through subtle details rather than heavy ornamentation.

#### Color Palette
| Token | Hex | Usage |
|-------|-----|-------|
| `ink` | `#1a1a2e` | Primary text, deep navy-black |
| `ink-light` | `#4a4a5a` | Secondary text |
| `silk` | `#faf9f6` | Background, warm white |
| `silk-dark` | `#f0ede6` | Card backgrounds, subtle contrast |
| `vermillion` | `#c0392b` | Primary accent (links, highlights, active states) |
| `gold` | `#b8860b` | Secondary accent (subtle dividers, metadata) |
| `jade` | `#2d6a4f` | Tertiary accent (tags, success states) |
| `cloud` | `#e8e4dd` | Borders, dividers |

#### Typography
- **Headings**: System serif stack — `Georgia, "Noto Serif SC", "Source Han Serif SC", "Songti SC", serif`
- **Body**: System sans — `"Inter", system-ui, -apple-system, "Noto Sans SC", sans-serif`
- **Scale**: Modular scale (1.25 ratio), generous line-height (1.7+ for body)
- **No custom web fonts in v1** (performance priority; revisit later)

#### Design Elements — DO vs DON'T
| DO | DON'T |
|----|-------|
| Generous whitespace | Heavy ornamental borders |
| Thin gold accent lines as dividers | Corner bracket decorations |
| Subtle ink-wash gradient (barely visible) | Seal stamp logos |
| Large, confident typography | Cloud pattern backgrounds |
| Smooth scroll-triggered animations | Static, dense layouts |
| Clean card shadows | Faux-paper textures |
| Color as accent, not decoration | Red-and-gold everywhere |
| Horizontal rules with cultural meaning | Decorative SVG patterns |

#### Core UX Concept: The Digital Scroll (数字卷轴)

The homepage is a **horizontal scroll experience** — the user moves from right to left (following traditional Chinese reading direction), and the page feels like unrolling an actual scroll painting.

**Key characteristics:**
- **Horizontal navigation**: User scrolls/drags left to traverse history from ancient to modern
- **Scroll unfurling animation**: As the user moves, the scroll visually "unrolls" — content appears with a flowing, ink-on-silk feeling
- **Dynasty content embedded in scroll**: Dynasties are NOT separate cards floating above a timeline; they are **part of the scroll surface itself**, like text and illustrations painted onto silk
- **Flowing transitions between eras**: Ink-wash dividers or subtle gradient transitions between dynasties, creating a sense of continuous historical flow
- **Scroll rollers**: Decorative scroll ends (轴杆) visible at the left/right edges as visual anchors
- **Dark backdrop outside scroll**: The scroll "paper" is warm silk/parchment color, floating on a dark background to create depth and focus
- **Parallax depth**: Subtle parallax layers (background texture moves slower than content) for depth

**Inspiration**: Imagine browsing the 《清明上河图》(Along the River During the Qingming Festival) digitally — that sense of discovery as you scroll through a continuous, richly detailed horizontal panorama.

#### Interaction Patterns
- **Horizontal scroll/drag**: Primary navigation on homepage (mouse wheel maps to horizontal, drag supported)
- **Scroll-triggered reveal**: Content fades/flows in as the scroll "reaches" that section
- **Smooth page transitions** between dynasty pages
- **Hover**: Subtle lift + vermillion accent shift on interactive elements
- **Language toggle**: Clean pill switch, not a flag dropdown

#### Layout Principles
- Max content width: 768px (for readability)
- Max layout width: 1200px
- Mobile-first responsive
- Generous padding: 2rem+ on sections
- Asymmetric grids for visual interest on detail pages

---

## Content Structure

### Directory Layout
```
prototype-astro/src/content/dynasty/
├── zh/          # Chinese Markdown files
│   ├── beisong.md
│   ├── tang.md
│   ├── qin.md
│   ├── wudai.md
│   └── {slug}.md ...
└── en/          # English Markdown files (same slugs)
    ├── beisong.md
    ├── tang.md
    ├── qin.md
    ├── wudai.md
    └── {slug}.md ...
```

### Markdown Frontmatter Schema

The implemented schema (see `src/content/config.ts`):

```yaml
---
dynastySlug: wudai           # matches slug in dynasties.ts
lang: zh                     # zh or en
title: 五代十国
englishName: Five Dynasties and Ten Kingdoms
period: "907–979"            # en-dash, not hyphen; always quote if starts with digit
overview: "..."              # one-line tagline
color: "#696969"             # optional, match dynasties.ts

figures:
  - name: 柴荣
    englishName: Chai Rong (Emperor Shizong of Later Zhou)
    role: 后周世宗
    years: "921–959"
    bio: "..."
    image: images/figures/chai-rong.jpg   # optional

events:
  - year: "960"              # always quoted string; can use 约/c. prefix
    name: 陈桥兵变
    englishName: Coup at Chenqiao
    description: "..."

achievements:
  economy:
    - title: "..."
      description: "..."
      artifact: "..."        # optional — triggers artifact gallery card
      museum: "..."          # optional
  politics:
    - title: "..."
      description: "..."
  culture:
    - title: "..."
      description: "..."
      artifact: "..."
      museum: "..."

references:
  primary:
    - "脱脱等《宋史》，元至正三年至五年（1343–1345年）成书"
  secondary:
    - "Twitchett, Denis. *The Cambridge History of China*, Vol. 5. CUP, 2009."
---

Prose overview body (2–3 paragraphs, rendered as 简介 / Overview section).
```

### Dynasty Detail Page — Section Structure

Each dynasty detail page follows this fixed structure (in order):

---

#### 1. Hero (dark background)
- **Large calligraphic Chinese name** — `font-heading`, `text-7xl sm:text-9xl`
- **English name** — `text-xl`, subdued opacity (`text-scroll/50`)
- **Period dates** — `text-3xl`, gold tint (`text-gold-light/70`)
- **Language toggle** — `text-lg`, inline: `中文 | English` (active shown muted, other is a gold link)
- **Hero artwork** — optional `<img>` background (opacity 0.38, `filter: saturate(0.7)`) for dynasties with `artwork` set in `dynasties.ts`

---

#### 2. 简介 / Overview
A 2–4 paragraph narrative overview of the dynasty: founding context, defining character, and historical significance. Written in flowing prose, not bullet points.

---

#### 3. 重要人物 / Key Figures
- Displayed as a **grid of figure cards** (avatar circle with first character + name + role)
- Each card is **clickable**:
  - **Desktop (≥1280px)**: detail sidebar appears to the right of the figures section, `position: absolute` anchored within the section (scrolls with page). Escape or ✕ to close.
  - **Mobile**: bottom sheet popup with backdrop overlay
- Only one figure panel open at a time
- Avatar circle uses the dynasty's accent `color` as background
- Portrait images not yet implemented — tracked in M5

---

#### 4. 重大事件 / Key Events
- Displayed as a **vertical timeline** with date markers
- Each event is **clickable**:
  - **Desktop**: description expands inline below the event item (one at a time, click again to collapse)
  - **Mobile**: bottom sheet popup
- Events sorted chronologically (from `events[]` in the content file)

---

#### 5. 文化成就 / Cultural Achievements
Divided into **three sub-sections**, each collapsible (`<details open>`):

- **经济 Economy** — trade, currency, agriculture, commercial innovation
- **政治 Politics** — governance reforms, institutions, administrative innovations
- **文化 Culture** — literature, art, philosophy, religion, science & technology

Items with `artifact` field in the content file automatically appear in the **Featured Artifacts gallery** below the three sub-sections:
- Card with placeholder (artifact name rendered in calligraphic style)
- Click opens a **lightbox** with artifact name, achievement title, and museum attribution
- Artifact images not yet sourced — tracked in M5

---

#### 6. 参考资料 / References
Split into two tiers:
- **Primary sources** (原始文献): original Chinese historical texts with author, title, and date of compilation
- **Secondary scholarship** (现代学术著作): peer-reviewed monographs and Cambridge History of China volumes

References render in a smaller, muted style to distinguish from main narrative.

---

---

## Dynasties (Full List, Chronological)

| Order | Chinese | English | Period |
|-------|---------|---------|--------|
| 1 | 上古传说 | Ancient Legends | ~2852 - 2070 BCE |
| 2 | 夏 | Xia Dynasty | ~2070 - 1600 BCE |
| 3 | 商 | Shang Dynasty | ~1600 - 1046 BCE |
| 4 | 西周 | Western Zhou | ~1046 - 771 BCE |
| 5 | 东周 (春秋/战国) | Eastern Zhou | 770 - 221 BCE |
| 6 | 秦 | Qin Dynasty | 221 - 206 BCE |
| 7 | 西汉 | Western Han | 202 BCE - 9 CE |
| 8 | 东汉 | Eastern Han | 25 - 220 CE |
| 9 | 三国 | Three Kingdoms | 220 - 280 CE |
| 10 | 西晋 | Western Jin | 266 - 316 CE |
| 11 | 东晋 | Eastern Jin | 317 - 420 CE |
| 12 | 南北朝 | Northern & Southern | 420 - 589 CE |
| 13 | 隋 | Sui Dynasty | 581 - 618 CE |
| 14 | 唐 | Tang Dynasty | 618 - 907 CE |
| 15 | 五代十国 | Five Dynasties and Ten Kingdoms | 907 - 979 CE |
| 16 | 北宋 | Northern Song | 960 - 1127 CE |
| 17 | 南宋 | Southern Song | 1127 - 1279 CE |
| 18 | 元 | Yuan Dynasty | 1271 - 1368 CE |
| 19 | 明 | Ming Dynasty | 1368 - 1644 CE |
| 20 | 清 | Qing Dynasty | 1644 - 1912 CE |
| 21 | 中华民国 | Republic of China | 1912 - 1949 CE |
| 22 | 中华人民共和国 | People's Republic | 1949 - present |

---

## Milestones

### M1: Website Foundation ✅
- [x] Initialize Astro project with Tailwind
- [x] Basic layout (Header, Footer, Language toggle)
- [x] Homepage with interactive horizontal scroll timeline
- [x] Dynasty detail page template
- [x] GitHub Pages deployment pipeline
- [x] Contemporary Chinese aesthetic UX (inked paper palette, typography-driven)
- [x] Scroll-triggered reveal animations (Intersection Observer)
- [x] Responsive mobile design

### M2: Content Pipeline ✅
- [x] Set up Astro Content Collections for Markdown (`src/content/config.ts`)
- [x] Create Markdown frontmatter schema (`dynastySlug`, `lang`, `title`, `englishName`, `period`, `overview`, `color`, `figures`, `events`, `achievements`, `references`)
- [x] Directory structure: `src/content/dynasty/zh/` and `src/content/dynasty/en/`
- [x] Markdown rendering inside structured dynasty detail page layout
- [x] Bilingual route switching (`/zh/dynasty/:slug` ↔ `/en/dynasty/:slug`)
- [x] Old `/dynasty/:slug` URLs 301-redirect to `/zh/dynasty/:slug`
- [x] Language toggle in dynasty hero section

### M3: First Content ✅
- [x] Qin Dynasty (秦) — zh + en with primary source citations
- [x] Tang Dynasty (唐) — zh + en with primary source citations
- [x] Northern Song (北宋) — zh + en with citations, hero artwork (千里江山图)
- [x] Five Dynasties and Ten Kingdoms (五代十国) — zh + en, key figures (朱温, 李存勖, 柴荣, 李煜, 冯道), events, achievements, citations, hero artwork (韩熙载夜宴图)

### M4: Rich Detail Pages & Interactivity ✅
- [x] **重要人物**: figure grid with detail sidebar (desktop, anchored absolute within figures section, Escape to close) and bottom sheet (mobile)
- [x] **重大事件**: vertical timeline with inline expand (desktop) and bottom sheet (mobile)
- [x] **文化成就**: three collapsible sub-sections (经济 / 政治 / 文化) with artifact cards
- [x] Artifact gallery: auto-generated from `achievements.*.artifact` fields
- [x] Artifact lightbox: click artifact card for full-screen detail view
- [x] Dynasty hero artwork backgrounds (public domain paintings)
- [x] Homepage dropdown dynasty navigator

### M5: Content Expansion (Next)
- [ ] Content for remaining 18 dynasties (priority order: 南宋, 明, 清, 汉, 唐五代衔接期)
- [ ] Portrait images for key figures (Wikimedia Commons / National Palace Museum Open Data, CC0/public domain only)
- [ ] Dynasty territory maps (open-source SVG)

### M6: Discovery & Scale
- [ ] Search functionality across dynasty content
- [ ] SEO optimization (meta tags, structured data, sitemap)
- [ ] Performance audit (Core Web Vitals)
- [ ] Accessibility audit (WCAG 2.1 AA)
- [ ] Social sharing (Open Graph images per dynasty)

### M7: Custom Domain
- [ ] Register domain (e.g. `chroniclesofchina.com` or `huaxiachunqiu.com`)
- [ ] Add `prototype-astro/public/CNAME` containing just the domain name
- [ ] Update `astro.config.mjs`: `site` → custom domain, `base` → `'/'`
- [ ] Configure DNS: four A records (`185.199.108–111.153`) + CNAME `www` → `eherozhao.github.io`
- [ ] GitHub repo Settings → Pages: verify domain, enable Enforce HTTPS

---

## Hero Artwork — All 22 Dynasties

Each dynasty page can have a hero background image (`artwork` + `artworkPosition` in `dynasties.ts`, file in `public/images/`). All images must be public domain. Target file size ≤200 KB; resize to ~1280px wide before saving.

**How to add an image:**
1. Download from the Wikimedia URL below
2. Resize/compress to ≤200 KB (e.g. `sips -Z 1280 file.jpg` on Mac, or squoosh.app)
3. Save to `prototype-astro/public/images/{filename}`
4. The `artwork` entry in `dynasties.ts` is already set — no code change needed

| # | Dynasty | Status | Artwork | Artist / Date | Save as | Wikimedia URL |
|---|---------|--------|---------|--------------|---------|---------------|
| 1 | 上古传说 | ⬜ | 伏羲女娲图 (Fuxi and Nü Wa) | Tang dynasty silk painting, c. 7–8th c. | `shanggu-fuxi-nuwa.jpg` | `https://upload.wikimedia.org/wikipedia/commons/2/2c/Fuxi_and_N%C3%BCwa2.jpg` |
| 2 | 夏 | ⬜ | 二里头铜爵 (Erlitou bronze jue) | c. 1700 BCE, archaeological | `xia-erlitou-jue.jpg` | `https://upload.wikimedia.org/wikipedia/commons/7/7f/Erlitou-bronzeJue.jpg` |
| 3 | 商 | ⬜ | 后母戊鼎 (Houmuwu Ding) | c. 1250 BCE, National Museum | `shang-houmuwu-ding.jpg` | `https://upload.wikimedia.org/wikipedia/commons/f/f7/Simuwu_ding.jpg` |
| 4 | 西周 | ⬜ | 何尊 (He Zun bronze) | c. 1038 BCE, Baoji Bronze Museum | `xizhou-he-zun.jpg` | `https://upload.wikimedia.org/wikipedia/commons/2/2e/He_Zun_inscription.jpg` |
| 5 | 东周 | ⬜ | 曾侯乙编钟 (Marquis Yi bells) | 433 BCE, Hubei Provincial Museum | `dongzhou-bianzhong.jpg` | `https://upload.wikimedia.org/wikipedia/commons/4/4f/Bianzhong_of_Marquis_Yi_of_Zeng.jpg` |
| 6 | 秦 | 🟡 placeholder | 铜车马 (Bronze Chariot No. 2) | c. 210 BCE, Xi'an | `qin-bronze-chariot.jpg` | `https://upload.wikimedia.org/wikipedia/commons/a/a0/Qin_bronze_chariot_two.jpg` |
| 7 | 西汉 | ⬜ | 马王堆T形帛画 (Mawangdui banner) | c. 168 BCE, Hunan Museum | `xihan-mawangdui.jpg` | `https://upload.wikimedia.org/wikipedia/commons/9/9e/MawangduiBanner.jpg` |
| 8 | 东汉 | ⬜ | 铜奔马 (Flying Horse of Gansu) | c. 2nd c. CE, Gansu Museum | `donghan-flying-horse.jpg` | `https://upload.wikimedia.org/wikipedia/commons/e/e3/Flying_Horse_of_Gansu.jpg` |
| 9 | 三国 | ⬜ | 曹操高陵出土石牌 (Cao Cao tomb stone tablet) | c. 220 CE | `sanguo-caocao-tomb.jpg` | Search Wikimedia: `Cao Cao tomb` |
| 10 | 西晋 | ⬜ | 青瓷羊形烛台 (Western Jin celadon) | 3rd–4th c., various museums | `xijin-celadon.jpg` | Search Wikimedia: `Western Jin celadon` |
| 11 | 东晋 | ⬜ | 洛神赋图 (Nymph of the Luo River) | Attr. Gu Kaizhi, 4th c. (Song copy) | `dongjin-luoshen.jpg` | `https://upload.wikimedia.org/wikipedia/commons/a/a1/Gu_Kaizhi_-_Nymph_of_the_Luo_River_%28detail%29.jpg` |
| 12 | 南北朝 | ⬜ | 敦煌莫高窟第285窟壁画 (Mogao Cave 285) | Western Wei, c. 538–539 CE | `nanbeichao-mogao285.jpg` | `https://upload.wikimedia.org/wikipedia/commons/5/56/Dunhuang_Mogao_Cave_285.jpg` |
| 13 | 隋 | ⬜ | 游春图 (Spring Excursion) | Attr. Zhan Ziqian, c. 600 CE | `sui-youchu-tu.jpg` | `https://upload.wikimedia.org/wikipedia/commons/6/6b/Zhan_Ziqian_-_Excursion_in_Spring.jpg` |
| 14 | 唐 | 🟡 placeholder | 步辇图 (Emperor Taizong Receives Tibetan Envoy) | Yan Liben, c. 641 CE | `tang-bunian-tu.jpg` | `https://upload.wikimedia.org/wikipedia/commons/1/1b/Emperor_Taizong_Receiving_the_Tibetan_Envoy%28Bunian_Tu%29.jpg` |
| 15 | 五代十国 | ✅ | 韩熙载夜宴图 (Night Revels of Han Xizai) | Attr. Gu Hongzhong, c. 970 CE | `night-revels.jpg` | already in repo |
| 16 | 北宋 | ✅ | 千里江山图 (A Thousand Li of Rivers and Mountains) | Wang Ximeng, 1113 CE | `qianli-jiangshan.jpg` | already in repo |
| 17 | 南宋 | ⬜ | 溪山清远图 (Clear Distant View) | Xia Gui, c. 1200 CE | `nansong-xishan-qingyuan.jpg` | `https://upload.wikimedia.org/wikipedia/commons/7/7e/Xia_Gui_-_Twelve_Views_from_a_Thatched_Hut_%28detail%29.jpg` |
| 18 | 元 | ⬜ | 富春山居图 (Dwelling in the Fuchun Mountains) | Huang Gongwang, 1350 CE | `yuan-fuchun-shan.jpg` | `https://upload.wikimedia.org/wikipedia/commons/3/37/Huang_Gongwang_Fuchun.jpg` |
| 19 | 明 | ⬜ | 汉宫春晓图 (Spring Morning in the Han Palace) | Qiu Ying, c. 1530 CE | `ming-hangong-chunxiao.jpg` | `https://upload.wikimedia.org/wikipedia/commons/5/54/Qiu_Ying_-_Spring_Morning_in_the_Han_Palace_%28detail%29.jpg` |
| 20 | 清 | ⬜ | 百骏图 (One Hundred Horses) | Giuseppe Castiglione, 1728 CE | `qing-baijun-tu.jpg` | `https://upload.wikimedia.org/wikipedia/commons/e/e2/Giuseppe_Castiglione_-_One_Hundred_Horses.jpg` |
| 21 | 中华民国 | ⬜ | 奔马图 (Galloping Horse) | Xu Beihong, c. 1940 CE | `minguo-xu-beihong-horse.jpg` | `https://upload.wikimedia.org/wikipedia/commons/0/0b/Xu_Beihong_horse.jpg` |
| 22 | 中华人民共和国 | ⬜ | 江山如此多娇 (The Land Is So Beautiful) | Fu Baoshi & Guan Shanyue, 1959 CE | `gongheguo-jiangshan.jpg` | Search: `Fu Baoshi Guan Shanyue landscape 1959` |

**Legend**: ✅ image in repo and wired | 🟡 wired in `dynasties.ts` but placeholder only | ⬜ not yet added

**artworkPosition suggestions** (add to `dynasties.ts` alongside `artwork`):

| Slug | artworkPosition |
|------|----------------|
| shanggu | `center 60%` |
| xia | `center 50%` |
| shang | `center 40%` |
| xizhou | `center 50%` |
| dongzhou | `center 30%` |
| xihan | `center 55%` |
| donghan | `center 45%` |
| dongjin | `center 40%` |
| nanbeichao | `center 50%` |
| sui | `center 60%` |
| nansong | `center 50%` |
| yuan | `center 45%` |
| ming | `center 40%` |
| qing | `center 50%` |
| minguo | `center 45%` |

---

## Image & Copyright Policy
- Prefer public domain sources: Wikimedia Commons, National Palace Museum Open Data, Library of Congress
- All images must be CC0, CC-BY, or public domain
- Maintain an `ATTRIBUTIONS.md` for all media credits
- For maps: use open-source historical map datasets or create original SVG maps

---

## References & Inspiration
- [LingDong-/grand-timeline](https://github.com/LingDong-/grand-timeline) — interactive timeline of 30,800 Chinese historical figures
- [quzhi1/ChineseHistoricalSource](https://github.com/quzhi1/ChineseHistoricalSource) — searchable Twenty-Four Histories
- [cadicex/history_map](https://github.com/cadicex/history_map) — Chinese historical atlas
- Modern design references: Palace Museum digital exhibitions, Apple China, contemporary Chinese tea brand websites
