---
name: share-card-generator
description: |
  Generate beautiful high-resolution share cards and posters for WeChat/社交媒体 sharing.
  Use this skill whenever the user wants to: create a share image/poster/card for a web link,
  generate a promotional card with QR code, make a WeChat group share image, design a social
  media card, or produce a downloadable PNG card for any URL. Also triggers on requests like
  "生成分享图", "做一张分享卡片", "制作海报", "generate a share card", "create an OG image".
---

# Share Card Generator

Generate a high-resolution (1200×1600) share card PNG with:

- Japanese清新 (fresh/minimalist) aesthetic — soft greens, warm yellows, clean white space
- Emoji decorative elements and circles
- Feature list with emoji bullets
- QR code linking to the target URL
- Downloadable as PNG

## Workflow

### Step 1: Gather information

Ask the user (or extract from context) these inputs:

| Field | Example | Required |
|-------|---------|----------|
| `url` | `https://lunch-1t7.pages.dev/` | Yes |
| `title` | `中午吃什么` | Yes |
| `subtitle` | `随机决定午餐的治愈小工具` | Yes |
| `features` | `["🎲  一键随机", "📝  自由编辑", "🎰  老虎机动画"]` | No (default: empty) |
| `primaryColor` | `#81C784` | No (default: `#81C784`) |

If the user doesn't provide some fields, infer reasonable defaults from context.

### Step 2: Generate the card HTML

Read `references/card-template.html` and replace the placeholder values:

- `__URL__` → user's URL
- `__TITLE__` → user's title
- `__SUBTITLE__` → user's subtitle
- `__FEATURES__` → JSON array of feature strings, e.g. `["🎲  feature one", "📝  feature two"]`

Update `primaryColor` and `primaryLight` in CONFIG if the user wants a different color scheme.

### Step 3: Deliver to user

Write the filled template to the project directory as `share-card.html`. Tell the user:

1. Open `share-card.html` in a browser
2. Wait for the QR code to load
3. Click "下载 PNG" to save the image

The resulting PNG can be:
- Set as `og:image` for link previews
- Shared directly in WeChat groups (long-press → send)
- Used as a poster/banner

## Color Themes

| Theme | primaryColor | primaryLight | Vibe |
|-------|-------------|--------------|------|
| 日系清新 (default) | `#81C784` | `#A5D6A7` | Green, fresh |
| 暖橙 | `#FF8A65` | `#FFAB91` | Warm, foodie |
| 樱花粉 | `#F48FB1` | `#F8BBD0` | Soft, cute |
| 天空蓝 | `#64B5F6` | `#90CAF9` | Clean, tech |

Ask the user if they want a specific theme, otherwise default to 日系清新.

## Important Notes

- The QR code is generated via `api.qrserver.com` — requires internet connection
- If the QR API fails, the card is still usable (just without QR)
- The output PNG is ~150KB at 1200×1600, crisp on Retina displays
- The card HTML is self-contained — no dependencies, single file
