# PotatoHead Media Works - website (static HTML/CSS/JS, no build)
Open index.html (keep the folder together). Look: clean, minimal (nevermind.ae-style). White + orange, black as the third colour. One typeface: Studio (from the original page).

## Section order
Header (white) -> 1 Hero (mountain) -> 2 We make people unforgettable (copy from pmw-self.vercel.app)
-> 3 Work reels (auto-scrolling, click = info + video) -> 4 Client logos (auto-scrolling) -> 5 Services (click a row = work)
-> 6 Your brand. Our obsession. + process (black) -> 7 Carousels (auto-scrolling wall) -> 8 Long-form (YouTube)
-> 9 Real brands. Real growth. -> 10 Let's build your brand together -> 11 Founders -> Footer (black).
Floating: chat widget, sound opt-in (synthesised effects). NOT included: iPod/music player, potato claw game, particle services (later).

## Automatic motion
- PAGE AUTO-SCROLL: starts ~3s after load (~80 px/s). Any wheel / touch / key / click pauses it; it resumes after 8s idle. The "auto-scroll" button (bottom right) turns it off for good (until clicked again). Speed: auto.speed in js/main.js. Off with prefers-reduced-motion.
- Hero images drift up (CSS), reels + logos marquee, carousel wall columns scroll up and down. All pause on hover.

## PLACEHOLDERS TO REPLACE BEFORE GOING LIVE
- Hero art line (index.html h1.statement.left) - draft wording
- Client logos: assets/placeholder/logo-01..10.svg
- Carousel covers: assets/placeholder/carousel-01..06.jpg (the wall + links go to Instagram)
- Long-form thumbs: assets/placeholder/longform-01..02.jpg; put the YouTube video ID in data-yt="" (index.html #longform) to play inline; also change href
- Founders: names, roles, bios, photos (mascot poses are stand-ins)
- REELS: the 13 Instagram reels are in js/main.js (REEL_IDS). Thumbnails are placeholders (assets/placeholder/reel-01..13.jpg, same order) - replace with real cover images (Instagram would not let me fetch them). Clicking a card opens the reel's Instagram embed. Titles / blurbs / tags per reel: REELS array in js/main.js. If an embed does not show, that reel has embedding disabled - use the 'open on Instagram' link.
- Services thumbnails: stock images (assets/stock/*) + reels
- Hero/stock pictures are the Pinterest images from the original page; use licensed images before launch.
- Copy comes from https://pmw-self.vercel.app/ (footer line there reads (c) 2025; here the year is automatic).
- Font licence: "Studio" came embedded in the original page; confirm you hold a licence for it before launch.

## Motion rules (latest)
- All animation runs regardless of the OS "reduce motion" setting; no scrollbars on the reel strip, logo rows or carousel wall.
- Reels: right → left. Brands: 3 rows (rows 1 & 3 right → left, row 2 left → right).
- Carousel wall: columns 1 & 3 move top → bottom, column 2 bottom → top while the page scrolls down; all reverse while scrolling up (smooth flip, small speed boost while scrolling). Hover pauses.
