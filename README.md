# BitcoinCities.org
Bitcoin Cities is a community-driven platform that tracks every Bitcoin-accepting business across the United States. Cities compete on leaderboards based on the number of Bitcoin-friendly businesses, and users earn badges by checking in at those businesses in person.

## Bitcoin Cities v1.7.5

A gamified platform that turns local Bitcoin adoption into a competitive, community-driven movement. Cities compete on leaderboards based on the number of Bitcoin-accepting businesses, sourced from OpenStreetMap. Users earn badges by checking in at businesses in person, and everyone can track adoption progress through interactive maps, leaderboards, and challenges.

Built with PHP, MySQL/PostgreSQL, and vanilla HTML/CSS/JS. No frameworks, no build tools, no npm. Just upload the files and run.

---

## What Is Bitcoin Cities?

[Bitcoin Cities](https://bitcoincities.org/) makes Bitcoin adoption visible and measurable at the city level. Instead of abstract statistics, you see exactly which cities have the most Bitcoin-accepting businesses, who the most active community members are, and how adoption is growing over time.

Users contribute by checking in at businesses that actually accept Bitcoin. Every check-in unlocks badges and helps their city climb the leaderboard. Businesses are imported directly from OpenStreetMap, so the data starts real and stays community-maintained.

---

## Features

### Map-First Discovery (Explore Page)
- Full-screen interactive Leaflet.js map as the primary entry point
- Browse all Bitcoin-accepting businesses across the country
- Category filter pills: Food, Cafe, Bar/Brewery, Retail, Services, Other
- Lightning Network and Verified business toggle filters
- "Near Me" geolocation button to find nearby businesses
- Marker clustering for dense areas with custom cluster icons
- Search by business name across the entire map
- Business popups showing name, category, payment methods, open/closed status
- Mobile: collapsible bottom drawer with business listings
- Desktop: sidebar list synced with map view

### Business Check-in System
- Five vote types: Lightning OK, On-chain OK, Issues, Refused, Closed
- Location-verified: users must be within 50 miles of a business to submit a vote
- Optional note (up to 280 characters) for positive votes; required (min 5 chars) for negative votes (issues, refused, closed)
- 24-hour cooldown per business per user to prevent spam
- Time-weighted verification score with 6-month half-life decay
- Check-in summary on every business card: positive/negative counts, last checked date
- Anonymous check-ins supported with IP-based rate limiting (30-min global cooldown, 24-hr per-business cooldown); anonymous check-ins update business scores but do not count toward badges or ambassador progression

### Badge System (Check-in Based)
Badges are earned purely through check-in counts -- no points system:

| Badge | Check-ins Required |
|-------|-------------------|
| Normie | 0 |
| Pleb | 3 |
| Explorer | 5 |
| Contributor | 10 |
| City Ambassador | 20 |
| State Ambassador | 50 |
| Sovereign | 100 |

Badges display with tier-specific Unicode icons for Contributor and above, with CSS glow/border effects for higher tiers. Ambassadors and Sovereigns lose editing powers after 60 days without a check-in; reactivation requires 10 check-ins within 60 days.

### Ambassador System
Activity-based auto-promotion tied to check-in counts:
- **Contributor** (10+ check-ins): Recognized on city pages
- **City Ambassador** (20+ check-ins): Auto-assigned to your top city; can edit city info and pin local tips within 50 miles
- **State Ambassador** (50+ check-ins): Edit powers across states where you hold ambassador records
- **Sovereign** (100+ check-ins): Nationwide editing powers

Ambassador assignments are re-evaluated on each check-in. City Ambassadors are limited to one city (the one with the most check-ins). Inactivity decay demotes City Ambassadors to Contributor after 60 days without a check-in; reactivation requires 10 check-ins within 60 days.

### Leaderboard and Rankings
- Five tabs: Top Cities, Top Members, Ambassadors, Rising Stars, Monthly Leaders
- Filter all rankings by state
- **Top Cities**: Ranked by number of Bitcoin-accepting businesses
- **Top Members**: All-time user rankings by total check-ins
- **Ambassadors**: Sovereigns, State Ambassadors, and City Ambassadors with assigned cities/states and stats
- **Rising Stars**: Cities with the biggest rank improvements
- **Monthly Leaders**: Most active members this month

### Dashboard and Progression
- Global badge roadmap showing the full tier path: Normie through Sovereign
- Visual progress indicators with highlighted current position
- Per-city ambassador progress with motivational hint text describing what each next tier unlocks
- Active cities, recent check-ins, and top cities at a glance

### City Pages
- City detail pages with business maps, check-in history, comments, and tips
- Prestigious ambassador section with gold-accent styling for City Ambassadors
- Featured ambassador rows showing editing powers and check-in stats
- Separator between City Ambassadors and Contributors
- Motivational CTA when no ambassadors exist yet
- City certification levels based on verified business count

### Certification Levels
Cities earn certification badges based on their verified business count:

| Level | Businesses Required |
|-------|-------------------|
| Bronze | 5+ |
| Silver | 20+ |
| Gold | 50+ |
| Platinum | 100+ |

Progress bars show how close a city is to the next certification level.

### Challenges and Achievements
Eight all-time challenges spanning three difficulty levels:

**Easy:**
- First Steps: Complete 3 business check-ins
- Neighborhood Scout: Check in at businesses in 2 different cities

**Medium:**
- Check-in Sprint: Complete 25 check-ins
- Explorer: Check in at businesses in 5 different cities
- Trailblazer: Be the first person to check in at 10 different businesses
- Dedicated Checker: Check in at businesses on 15 different days

**Hard:**
- Centurion: Complete 100 check-ins
- Sovereign: Reach 100 total check-ins

Completed challenges are displayed as pill badges on user public profiles.

### Business Pages
- Businesses sourced exclusively from OpenStreetMap via the Overpass API
- Categories: Restaurant, Cafe, Bar/Brewery, Retail, Services, Hotel/Lodging, Entertainment, Health/Wellness, Education, Automotive, Technology, Other
- Tracks Lightning Network and on-chain Bitcoin acceptance separately
- Extended metadata from OSM: opening hours, cuisine, operator, social contacts, wheelchair access, outdoor seating, takeaway, payment methods, brand, images, descriptions (stored in `osm_tags` JSONB column)
- Open/Closed status calculated from OSM opening hours in real time (see Future Optimizations)
- "Edit on OSM" link on each business detail page, directing users to suggest corrections on OpenStreetMap
- SEO-friendly URLs: `/state/city/business-name` format with auto-generated slugs
- **Auto-generated descriptions**: Every business page has a unique, factual description built from OSM data (name, category, city, state, payment methods, address, hours) via `generateBusinessDescription()` with 80+ category display name mappings for grammatical correctness
- **AI-enhanced descriptions** (optional): Richer 150-250 word descriptions generated via OpenAI in bulk from the admin panel, stored in `ai_description` column. AI descriptions display on the page when available; auto-generated template used as fallback. Ambassadors cannot edit descriptions — AI generation is admin-only
- **Schema.org types**: Business pages use specific schema types (CafeOrCoffeeShop, Restaurant, BarOrPub, Store, etc.) via `getBusinessSchemaType()` mapping 70+ categories instead of generic LocalBusiness
- **Disclaimer**: Bottom of each business page shows OSM data source notice, accuracy warning, and link to edit on OSM or check in

### OpenStreetMap Import (Overpass API)
- Import Bitcoin-accepting businesses directly from OpenStreetMap
- Multi-mirror failover (kumi.systems, mail.ru, overpass-api.de) with exponential backoff
- Queries all nodes/ways tagged with `payment:bitcoin=yes`, excluding ATMs and vending machines
- Automatic city creation and US state normalization during import
- Reverse geocoding via Nominatim for businesses missing address data
- Safe to re-run: duplicates are skipped by OSM ID

### AI-Powered Content (Optional)
Three-provider system for business descriptions, plus city auto-fill:

- **OpenAI (GPT-4o-mini)**: Fast bulk generation of 150-250 word descriptions. Best for quickly filling in content across all businesses. "Missing only" or "Regenerate all" modes with auto-continue
- **Perplexity Sonar**: Live-web research phase. Searches the web, reviews, Reddit, news, and more to surface real, current data with inline citations. Research is stored in `perplexity_research` column for the writing phase to consume
- **Claude (Anthropic)**: Writing and synthesis phase. Produces polished, publication-ready descriptions that synthesize Perplexity research with structured data. Consistent voice, less revision needed. Three modes: "Has research, missing description" (intended workflow), "Missing description (any)", or "Regenerate all"
- **City auto-fill**: Uses OpenAI to auto-fill city details (population, main industries, description)

All prompts are editable in the admin panel under **AI Settings**. API keys for all three providers are stored securely in the database via the same settings panel. The platform works fully without AI — auto-generated templates fill in for all content.

### User Profiles
- Badge tier with tier-specific icon prominently displayed
- Badge progression ladder showing path to next tier
- Active cities based on check-in history
- Check-in history with business links and vote details
- Vote type breakdown (Lightning/On-chain/Issues/Refused/Closed)
- Community trust system: vouch (public trust signal) and flag (private, sent to mod queue)
- Completed challenges shown as pill badges
- Tip section with QR codes for Lightning/BTC addresses, copy-to-clipboard buttons, deep-linkable via `#tip` anchor
- Tip pills displayed next to usernames across the platform

### Interactive Maps
- Leaflet.js maps with OpenStreetMap tiles
- City detail pages: businesses plotted with custom Bitcoin markers
- Lightning (yellow) vs. on-chain (orange) marker differentiation
- Verified businesses highlighted with green borders
- Marker clustering on both explore and city maps

### Support Ticket System
- Users can submit support tickets with subject and message
- Threaded conversation between user and moderators
- Ticket status tracking (open, in progress, resolved, closed)
- Accessible from user navigation when logged in

### Moderator and Admin Panel
Three-tier role system:

| Role | Capabilities |
|------|-------------|
| User | Check in at businesses, comment, submit support tickets |
| Moderator | All user actions + manage users (ban/unblock), delete reviews/comments, view/reply support tickets, post mod notes |
| Admin | All moderator actions + assign roles, trigger OSM imports, manage system settings, delete accounts |

### Tiered Editing Powers
- Contributors (10+ check-ins) do NOT have editing powers
- City Ambassador (20+ check-ins): Edit city info and pin tips for their assigned city (within 50 miles)
- State Ambassador (50+ check-ins): Edit across their ambassador states
- Sovereign (100+ check-ins): Nationwide editing powers
- All editing requires active status (no 60-day inactivity gap)
- Edits logged in the `edit_log` table

### Password Reset
- Forgot password flow with token-based reset via email
- Rate-limited (5 requests per 60 minutes) to prevent abuse
- Tokens expire after 1 hour
- Constant-time response prevents email enumeration
- "Forgot your password?" link on login page

### Email System
- Lightweight PHP `mail()` wrapper for notifications, email verification, and password reset
- Broadcast capabilities with admin controls
- Support ticket notification emails for moderators/admins

### SEO and GEO Optimization
- Comprehensive Schema.org JSON-LD structured data on every page type:
  - Landing: `WebSite` + `Organization` + `FAQPage` with `about`, `knowsAbout`, `speakable`
  - City: `Place` + `BreadcrumbList` + `FAQPage` + `Article` with `containsPlace` (top 10 businesses as `LocalBusiness`), `containedInPlace`, `speakable`
  - Business: `LocalBusiness` + `BreadcrumbList` + `AggregateRating` (from check-in vote data), `about`, `speakable`
  - State: `AdministrativeArea` + `BreadcrumbList` with `containedInPlace`, `about`, `mentions`, `speakable`
  - About: `AboutPage` + `HowTo` (4 steps) + `Organization` with `about`, `mentions`
  - Explore: `WebPage` with `about`, `mentions`, `speakable`
  - Profile: `ProfilePage` + `Person` with `interactionStatistic`, `knowsAbout`, conditional `jobTitle`
- Entity-based E-E-A-T: `about`/`mentions` properties link to Wikipedia entities for Bitcoin and Lightning Network via `sameAs`
- `SpeakableSpecification` on key content selectors for voice assistant citation
- Unique title and meta description per page
- Open Graph and Twitter Card meta tags for social sharing
- Canonical URLs on all pages
- XML sitemap at `/sitemap.xml`
- `robots.txt` with proper crawl directives
- `geo.region` meta tag for geographic relevance

### Landing Page FAQ
- Six dropdown FAQ items covering platform overview, check-ins, badges, ambassadors, data sources, and account requirements
- FAQPage schema.org markup for rich search results
- Link to About page for detailed information

### Design
- Bitcoin orange (#F7931A) color scheme with supporting gradients
- Dark and light mode toggle with `localStorage` persistence
- Mobile-first responsive design
- Inter (body) and Space Grotesk (headings) fonts via Google Fonts
- No build step required

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Backend | PHP 8.0+ with PDO |
| Database | MySQL 5.7+ / MariaDB 10.3+ / PostgreSQL 14+ |
| Frontend | Server-side rendered HTML, CSS, vanilla JavaScript |
| Maps | Leaflet.js + Leaflet.markercluster + OpenStreetMap tiles |
| AI (optional) | OpenAI API, Perplexity Sonar API, Anthropic Claude API |
| Auth | Email/password with bcrypt + PHP sessions |
| QR Codes | qrcode-generator@1.4.4 (CDN) |

No frameworks. No build tools. No npm. Just PHP files you upload and run.

---

## Project Structure

```
bitcoin-cities/
├── index.php                    # Main router (API + page routing)
├── .htaccess                    # Apache rewrite rules + security headers
├── .env.example                 # Environment configuration template
├── robots.txt                   # Search engine crawl directives
├── config/
│   ├── database.php             # PDO connection + .env file loader
│   └── app.php                  # App constants (site name, cert levels, categories, states,
│                                #   AI prompt templates, GA tracking ID)
├── includes/
│   ├── header.php               # HTML head, navbar, dark/light toggle, SEO meta
│   ├── footer.php               # Footer with nav links, scripts
│   ├── auth.php                 # Register, login, logout functions (bcrypt)
│   └── functions.php            # Helpers: CSRF, UUID, badges, check-in scoring,
│                                #   certification levels, opening hours parser, haversine distance,
│                                #   ambassador promotion, tip pills, badge icons,
│                                #   business description generator, schema type mapper
├── api/
│   ├── auth.php                 # Login, register, logout, get current user
│   ├── cities.php               # City CRUD, search, top cities, state list
│   ├── businesses.php           # Business CRUD, explore endpoint
│   ├── verifications.php        # Submit votes, get check-in summary
│   ├── challenges.php           # Challenges & achievements, progress tracking
│   ├── stats.php                # Platform-wide statistics
│   ├── comments.php             # City comment CRUD
│   ├── tips.php                 # Tip system
│   ├── ambassadors.php          # Ambassador auto-promotion
│   ├── btcmap_sync.php          # OSM Overpass API import with multi-mirror failover
│   ├── geocode.php              # Reverse geocoding helper
│   ├── support.php              # Support ticket system
│   ├── mod.php                  # Moderator actions (ban, delete, notes)
│   ├── business_edits.php       # Ambassador business editing (hours, phone, website, cuisine)
│   ├── city_edits.php           # Ambassador city editing (description, FAQ, content fields)
│   └── admin.php                # Admin actions (roles, imports, AI generation, system management)
├── pages/
│   ├── landing.php              # Public landing page (hero, stats, how-it-works, FAQ)
│   ├── home.php                 # Logged-in dashboard (active cities, top cities)
│   ├── explore.php              # Full-screen interactive map with filters
│   ├── cities.php               # City directory with search, filter, pagination
│   ├── city_detail.php          # City profile: map, businesses, ambassadors, check-ins, stats
│   ├── state_detail.php         # State profile: cities, stats
│   ├── business_detail.php      # Individual business page with check-in UI
│   ├── leaderboard.php          # Rankings: cities, people, ambassadors, rising stars
│   ├── challenges.php           # Challenges & achievements with progress bars
│   ├── dashboard.php            # User dashboard with badge roadmap and city progress
│   ├── profile.php              # Edit own profile
│   ├── user_profile.php         # Public user profile (badges, activity, challenges, tips)
│   ├── support.php              # Support ticket list
│   ├── support_ticket.php       # Individual ticket conversation
│   ├── mod_panel.php            # Moderator/admin control panel
│   ├── login.php                # Login form
│   ├── forgot_password.php      # Forgot password form
│   ├── reset_password.php       # Reset password form (token-based)
│   ├── register.php             # Registration form
│   ├── about.php                # About page (how it works, ambassador system, data sources)
│   ├── terms.php                # Terms of service
│   ├── privacy.php              # Privacy policy
│   ├── sitemap.php              # XML sitemap generator
│   └── 404.php                  # Not found page
├── assets/
│   ├── css/style.css            # Full responsive stylesheet (dark/light mode, badge tiers,
│   │                            #   ambassador sections, badge roadmap)
│   └── js/
│       ├── app.js               # Core JS: fetch wrapper, toast, modal, theme toggle, CSRF
│       ├── explore.js           # Explore map: markers, clustering, filters, geolocation, drawer
│       └── map.js               # City detail map initialization
├── cron/
│   └── sync_osm.php             # Cron-compatible OSM sync script (weekly recommended)
└── sql/
    ├── schema_postgres.sql      # PostgreSQL database schema
    ├── schema_mysql.sql         # MySQL database schema
    ├── migration_v6_business_ai_description.sql  # Adds ai_description column to businesses
    ├── migration_v7_perplexity_research.sql     # Adds perplexity_research column to businesses
    ├── migration_v8_security_hardening.sql      # Slug indexes + unique constraints
    └── migration_v9_password_reset.sql          # Password reset columns on users table
```

---

## Installation

### Requirements

- PHP 8.0 or higher
- MySQL 5.7+ / MariaDB 10.3+ or PostgreSQL 14+
- Apache with `mod_rewrite` enabled (standard on most shared hosting)
- PHP extensions: `pdo`, `pdo_mysql` or `pdo_pgsql`, `curl`, `mbstring`, `json` (all typically included by default)

### Step 1: Download and Upload

1. Download or clone this repository
2. Upload all files to your web root (usually `public_html` or `htdocs`)
3. Make sure `index.php` and `.htaccess` are in the root of your web directory

### Step 2: Create the Database

**Using a hosting control panel (cPanel, SiteGround, Plesk, etc.):**
1. Create a new MySQL database (e.g., `btccities`)
2. Create a database user with a strong password
3. Grant the user **All Privileges** on the database

**Using command line (PostgreSQL):**
```bash
createdb bitcoin_cities
```

### Step 3: Import the Schema

**MySQL via phpMyAdmin:**
1. Open phpMyAdmin from your hosting panel
2. Select your database
3. Click the **Import** tab
4. Choose `sql/schema_mysql.sql`
5. Click **Go**

**MySQL via command line:**
```bash
mysql -u your_user -p your_database < sql/schema_mysql.sql
```

**PostgreSQL via command line:**
```bash
psql -U your_user -d bitcoin_cities -f sql/schema_postgres.sql
```

### Step 4: Configure Environment

1. Copy `.env.example` to `.env`
2. Edit `.env` with your database credentials:

**MySQL (most common for shared hosting):**
```env
DATABASE_URL=mysql://your_db_user:your_db_password@localhost/your_db_name
SESSION_SECRET=replace_with_a_long_random_string_at_least_32_characters
```

**PostgreSQL:**
```env
DATABASE_URL=postgresql://your_db_user:your_db_password@localhost:5432/your_db_name
SESSION_SECRET=replace_with_a_long_random_string_at_least_32_characters
```

You can generate a session secret with:
```bash
openssl rand -hex 32
```

The `.env` file is protected by `.htaccess` and cannot be accessed from the web.

### Step 5: Set Up Your Admin Account

1. Visit your site and click **Sign Up** to create your account
2. Open phpMyAdmin (or your database tool) and find the `users` table
3. Edit your user row and change the `role` column from `user` to `admin`
4. Save the change

You now have full admin and moderator access. The **Mod Panel** link will appear in your navigation.

### Step 6 (Optional): Import Businesses from OpenStreetMap

Once logged in as admin:

1. Go to the **Mod Panel** and click the **Admin Tools** tab
2. Click **Import US Bitcoin Businesses** to pull all businesses tagged with `payment:bitcoin=yes` from OpenStreetMap
3. Cities are created automatically during import -- no need to add them manually
4. The import takes a few minutes depending on data volume
5. Re-running is safe -- businesses already imported (by OSM ID) are skipped
6. ATMs and vending machines are excluded automatically

### Step 7 (Optional): Enable AI Content Generation

AI powers city auto-fill and a three-provider business description pipeline. API keys can be entered in the **Mod Panel > Settings > AI Settings** panel (stored securely in the database), or via `.env` for OpenAI.

**OpenAI** (fast bulk descriptions + city auto-fill):
1. Get an API key from [OpenAI](https://platform.openai.com/api-keys)
2. Enter it in **AI Settings** or add to `.env`: `OPENAI_API_KEY=sk-your-key`

**Perplexity Sonar** (live-web research):
1. Get an API key from [Perplexity](https://docs.perplexity.ai/)
2. Enter it in **AI Settings**

**Claude / Anthropic** (polished writing):
1. Get an API key from [Anthropic](https://console.anthropic.com/)
2. Enter it in **AI Settings**

**Recommended workflow:**
1. Run **Perplexity Research** to gather real, cited web data for each business
2. Run **Claude Descriptions** (mode: "Has research, missing description") to synthesize research into polished descriptions
3. Or use **OpenAI Business Descriptions** for faster, simpler generation without the research step

All prompts are editable in **AI Settings**. The platform works fully without AI — auto-generated templates provide unique content for every page.

### Step 8 (Optional): Set Up Cron for OSM Sync

To keep business data fresh automatically:

1. In your hosting panel (e.g. SiteGround cPanel > Devs > Cron Jobs), add:
```
php ~/www/bitcoincities.org/public_html/cron/sync_osm.php
```
2. Set to run weekly (recommended)

---

## How It Works

### For Users
1. **Explore** the interactive map to find Bitcoin-accepting businesses near you
2. **Check in** at businesses you visit by submitting a vote (Lightning OK, On-chain OK, Issues, Refused, or Closed)
3. **Earn badges** and climb the tiers from Normie to Sovereign
4. **Become an Ambassador** at 20+ check-ins to earn editing powers for your city
5. **Complete challenges** to earn achievement badges on your profile
6. **Watch your city** climb the rankings as more businesses are checked in

### For Moderators
1. **Manage users**: ban/unblock accounts, delete inappropriate reviews or comments
2. **Handle support tickets**: respond to user questions and issues
3. **Post mod notes** for internal team communication

### For Admins
1. Everything moderators can do, plus:
2. **Assign roles**: promote users to moderator or admin
3. **Import data**: trigger OpenStreetMap business imports
4. **AI content generation**: bulk-generate city and business descriptions via OpenAI
5. **Generate SEO slugs**: backfill URL slugs for businesses, cities, and users
6. **Fix city slugs**: rename duplicate-suffixed slugs (e.g., `houston-2` to `houston`) when safe
7. **Normalize states**: fix inconsistent state names across the database
8. **Merge duplicate cities**: consolidate cities with matching names in the same state
9. **Remove non-US data**: clean up accidentally imported non-US cities and businesses
10. **Scan for removed businesses**: compare database against OSM to find businesses no longer tagged as accepting Bitcoin
11. **System management**: delete accounts, manage system-wide settings, send broadcasts, configure AI prompts

---

## Configuration

### `config/app.php`

| Setting | Default | Description |
|---------|---------|-------------|
| `SITE_NAME` | Bitcoin Cities | Shown in navigation, titles, and footer |
| `SITE_DESCRIPTION` | (see file) | Used in meta tags and structured data |
| `GA_MEASUREMENT_ID` | G-ZR851PJ8TH | Google Analytics 4 measurement ID |
| `BTCMAP_CACHE_HOURS` | 72 | Hours between OSM re-syncs per city |
| `CITY_SEARCH_RADIUS_MILES` | 30 | Radius for matching businesses to cities |
| `CERT_LEVELS` | 5/20/50/100 | Business counts for Bronze/Silver/Gold/Platinum |
| `BUSINESS_CATEGORIES` | 12 categories | Dropdown options for business submissions |
| `DEFAULT_AI_BIZ_SYSTEM_PROMPT` | (see file) | System prompt for AI business description generation |
| `DEFAULT_AI_BIZ_PROMPT` | (see file) | User prompt template for AI business descriptions |

### Custom Domain

Point your domain's DNS to your hosting provider. The app auto-detects its URL from the `HTTP_HOST` header -- no hardcoded URLs to change.

### Nginx Configuration

If using Nginx instead of Apache, add this to your server block (the `.htaccess` file is Apache-only):

```nginx
location / {
    try_files $uri $uri/ /index.php?$query_string;
}

location ~ /\.(env|htaccess) {
    deny all;
}

location ~ ^/(config|includes|sql)/ {
    deny all;
}

location ~ \.php$ {
    fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}
```

---

## Running on Replit

This project runs on Replit out of the box with PostgreSQL:

1. Fork or import the repository
2. Replit automatically provisions a PostgreSQL database
3. Set a `SESSION_SECRET` in the Secrets tab
4. Run the PostgreSQL schema: connect to the database and execute `sql/schema_postgres.sql`
5. Start the application -- it runs on PHP's built-in development server

The `DATABASE_URL`, `PGHOST`, `PGDATABASE`, `PGUSER`, `PGPASSWORD`, and `PGPORT` environment variables are set automatically by Replit.

---

## Security

- All database queries use PDO prepared statements (SQL injection protection)
- All HTML output is escaped with `htmlspecialchars()` (XSS protection)
- CSRF tokens required on all POST/PUT/PATCH/DELETE API requests
- Passwords hashed with `bcrypt`
- `.env` file blocked from web access via `.htaccess`
- `config/`, `includes/`, and `sql/` directories blocked from direct web access
- Session-based authentication with secure cookie handling
- Business check-ins require 50-mile proximity (location spoofing deterrent)
- 24-hour cooldown on repeat check-ins per business per user
- Role-based access control for all administrative actions
- Tiered editing powers with ambassador table verification and edit logging

---

## API Endpoints

All API endpoints are under `/api/` and return JSON.

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/auth/register` | No | Create account |
| POST | `/api/auth/login` | No | Log in |
| POST | `/api/auth/logout` | Yes | Log out |
| POST | `/api/auth/forgot-password` | No | Request password reset email |
| POST | `/api/auth/reset-password` | No | Reset password with token |
| GET | `/api/auth/me` | Yes | Get current user |
| GET | `/api/cities` | No | List cities (paginated, filterable) |
| GET | `/api/cities/top` | No | Top cities by business count |
| GET | `/api/cities/states` | No | List of states with cities |
| GET | `/api/cities/{id}` | No | Single city details |
| PATCH | `/api/cities/{id}` | Amb | Update city description (ambassador+) |
| GET | `/api/businesses/explore` | No | Businesses by map bounds with filters |
| GET | `/api/businesses/{id}` | No | Single business details |
| GET | `/api/verifications/{bizId}` | No | Check-in summary for a business |
| POST | `/api/verifications` | Yes | Submit a check-in vote |
| GET | `/api/challenges` | No | List challenges and progress |
| GET | `/api/stats` | No | Platform statistics |
| POST | `/api/support` | Yes | Create support ticket |
| GET | `/api/support` | Yes | List user's tickets |
| PATCH | `/api/business-edits?business_id=X` | Amb | Edit business details (hours, phone, website, cuisine) |
| POST | `/api/admin/generate-slugs` | Admin | Backfill SEO slugs for all records |
| POST | `/api/admin/generate-city-descriptions` | Admin | AI-generate city descriptions in bulk |
| POST | `/api/admin/generate-business-descriptions` | Admin | AI-generate business descriptions via OpenAI |
| POST | `/api/admin/generate-perplexity-research` | Admin | Research businesses via Perplexity Sonar |
| POST | `/api/admin/generate-claude-descriptions` | Admin | Generate descriptions via Claude/Anthropic |
| GET | `/api/admin/businesses-without-descriptions` | Admin | Count businesses missing AI descriptions |
| POST | `/api/admin/normalize-states` | Admin | Fix inconsistent state names |
| POST | `/api/admin/merge-duplicate-cities` | Admin | Merge cities with duplicate names |
| POST | `/api/admin/cleanup-non-us` | Admin | Remove non-US cities and businesses |

**Auth levels:** No = public, Yes = logged in, Amb = ambassador or higher, Mod = moderator or admin, Admin = admin only.

---

## Changelog

### v1.7.5
- **Password reset flow:** Full forgot/reset password system with token-based email links, 1-hour expiry, rate limiting (5/60min), and constant-time responses to prevent email enumeration. New pages: `forgot_password.php`, `reset_password.php`. New API endpoints: `POST /api/auth/forgot-password`, `POST /api/auth/reset-password`. "Forgot your password?" link added to login page
- **User deletion cascade:** Admin user deletion now properly cleans up all referencing tables: `ambassadors`, `user_reputation`, `challenge_progress`, `user_achievements`, `support_messages`, `support_tickets`, `edit_log`, `mod_notes` (in addition to existing `business_verifications`, `comments`, `user_activity`, `tips`)
- **Rising Stars state filter:** Leaderboard Rising Stars tab now respects the state dropdown filter (was previously showing all states regardless of selection)
- Migration: `sql/migration_v9_password_reset.sql` — adds `password_reset_token` and `password_reset_expires` columns to `users` table

### v1.7.4
- **Security hardening for production launch:**
  - Session cookies now set with `Secure`, `HttpOnly`, and `SameSite=Lax` flags
  - XSS fix: `addslashes()` replaced with `json_encode()` in all JS contexts (support_ticket.php, login.php)
  - XSS fix: dashboard `$hintText` now escaped with `e()`
  - CSRF fix: reputation voting now uses `apiFetch()` instead of raw `fetch()` (votes were silently failing)
  - SQL injection prevention: `$scopeCol` in `ensureUniqueSlug()` now allow-listed
  - Host header injection: `SITE_URL` fallback sanitizes `HTTP_HOST` to alphanumeric characters
  - Rate limiter now fails closed (blocks requests) on database errors instead of allowing them
  - Blocked users are now immediately logged out on next request (session destroyed in `currentUser()`)
  - API route matching tightened to exact segment boundaries (prevents prefix collision)
  - Cron API files (`cron_merge_cities.php`, `cron_fix_city_slugs.php`) reject direct access outside front controller
  - `apiFetch()` now handles non-JSON responses gracefully (502s, HTML error pages)
  - IP spoofing hardened: `getClientIP()` walks X-Forwarded-For right-to-left, skipping all private/reserved IPs
- **Merge script improvements:**
  - MySQL-only `REGEXP` replaced with portable `LIKE` clauses
  - No-op `business_verifications` query removed
  - All merge operations wrapped in database transactions with rollback on failure
  - `user_activity` records now moved during merges (previously orphaned)
- **Performance & reliability:**
  - Added indexes on `cities.slug`, `businesses.slug`, `users.slug` for faster URL routing
  - Challenges page: N+1 queries eliminated — progress records batch-loaded, same-type calculations cached, unchanged progress skipped
  - OSM cron sync: file lock (`flock`) prevents overlapping runs
- **Bug fixes:**
  - `daily_streak` now counts actual consecutive days (was counting total distinct days)
  - Open Now filter: US timezone estimation replaced with proper zone boundaries (Eastern/Central/Mountain/Pacific/Alaska/Hawaii) with DST support
  - `isMobile` now updates on window resize (was set once on page load)
  - `.env` parser now strips surrounding quotes from values
  - Navbar z-index raised above map controls to prevent overlap
- Migration: `sql/migration_v8_security_hardening.sql` — slug indexes + unique constraints

### v1.7.3
- Three-provider AI business description system: OpenAI (fast bulk), Perplexity Sonar (live-web research with citations), Claude (polished writing that synthesizes research)
- Perplexity research stored in new `perplexity_research` column; Claude and OpenAI write to `ai_description`
- All AI prompts (OpenAI, Perplexity, Claude) editable in admin AI Settings panel with "Reset to Defaults" button
- API keys for Perplexity and Anthropic stored in `site_settings` via admin panel (no `.env` needed)
- OpenAI business descriptions now have "Missing only" / "Regenerate all" mode dropdown (matching city descriptions)
- Claude has three modes: "Has research, missing description" (intended Perplexity→Claude workflow), "Missing description (any)", "Regenerate all"
- Migration: `sql/migration_v7_perplexity_research.sql` adds `perplexity_research TEXT` column to businesses

### v1.7.2
- Landing page FAQ section with six dropdown questions and FAQPage schema.org markup
- About page: new Ambassador System section explaining City/State/Sovereign tiers and editing powers
- About page: updated leaderboard section to document all five tabs
- Leaderboard "Top Contributors" tab renamed to "Top Members"
- Leaderboard empty state text updated to use "check-in" terminology
- Map popup "# Check-ins" button replaced with "View Details" (both explore and city detail maps)
- Map popup meta line separator changed from middot to comma
- Ambassador hours editing: hours input only appears when OSM has no hours; API enforces the same restriction
- Anonymous check-ins: non-logged-in users can submit check-ins with IP-based rate limiting (30-min global, 24-hr per-business)
- FAQ text corrections: inactivity applies to Ambassadors/Sovereigns editing powers (not Contributors), anonymous check-ins acknowledged

### v1.7.1
- AI-powered business descriptions: bulk-generate 150-250 word unique descriptions via OpenAI from the admin panel, stored in `ai_description` column
- Auto-generated business descriptions for all pages via `generateBusinessDescription()` with 80+ category display name mappings for grammatical correctness (e.g. "other" → "business", "coffee_shop" → "coffee shop", "automotive" → "automotive shop")
- Specific Schema.org types per business category via `getBusinessSchemaType()` (CafeOrCoffeeShop, Restaurant, BarOrPub, Store, etc. instead of generic LocalBusiness)
- Business page disclaimer with OSM data source notice, accuracy warning, and edit-on-OSM link
- Configurable AI prompts for business descriptions (`DEFAULT_AI_BIZ_SYSTEM_PROMPT`, `DEFAULT_AI_BIZ_PROMPT`) in `config/app.php`
- Google Analytics 4 integration via `GA_MEASUREMENT_ID` constant
- City slug scoping by state to prevent cross-state slug collisions; admin tool to fix existing `-2`/`-3` suffixed slugs
- Ambassador business editing limited to hours, phone, website, and cuisine (descriptions are admin-only)
- Redundant cuisine suppression in auto-generated descriptions (e.g. "coffee shop serving coffee shop" no longer occurs)

### v1.7.0
- Comprehensive Schema.org JSON-LD overhaul across all page types with entity-based `about`/`mentions` properties linking to Wikipedia entities (Bitcoin, Lightning Network)
- Added `SpeakableSpecification` to key content selectors on every page type for voice assistant citation
- City pages embed top 10 businesses as `LocalBusiness` via `containsPlace`, with `containedInPlace` geographic hierarchy
- Business pages include `AggregateRating` computed from check-in vote data for rich search results
- State pages upgraded from `Place` to `AdministrativeArea` schema type
- Profile pages include `interactionStatistic` (CheckInAction count) and conditional `jobTitle` for badge tier
- About page includes `HowTo` schema with 4 detailed steps for AI model consumption
- Organization schema includes `about` and `knowsAbout` for E-E-A-T topical authority
- Merchant category chips converted to clickable filter buttons on city detail pages
- Merchant mix summary wrapped in collapsible element
- Comments capped at 1,500 characters (API + frontend enforcement)
- FAQ editor rebuilt as structured Q&A interface with XSS-safe DOM construction
- Nearby cities section on city detail pages (up to 8 cities within 100 miles)
- Rich city content fields (bitcoin_angle, economy_summary, merchant_mix_summary, faq_json) with ambassador editing
- Nostr share buttons on business and city detail pages
- "I was just here" CTA text in map popups
- Pin color by verification freshness (green/yellow/gray) on map markers

### v1.6.0
- Added Ambassadors tab to leaderboard with Sovereigns, State Ambassadors, and City Ambassadors sections
- Enhanced dashboard with global badge roadmap (Normie through Sovereign) and per-city ambassador progress with motivational hints
- Redesigned city page ambassador section with gold-accent styling, featured ambassador rows, powers descriptions, and separator between ambassadors and contributors
- Added tier-specific Unicode icons to badges for Contributor+ tiers
- Added CSS glow/border effects for higher-tier badges (City Ambassador gold accent, Sovereign purple glow)
- Motivational CTA on city pages when no ambassadors exist
- Replaced reputation system with trust network: vouch (public trust signal) and flag (private, routed to mod queue as mod notes)

### v1.5.1
- Names link to user profiles across all pages (dashboard, mod panel, leaderboard, city detail)
- Emails no longer exposed on any public-facing pages
- Removed last_name from registration flow and all display contexts
- Tip pill system for Lightning/BTC tipping across the platform

---

## Future Optimizations

### Opening Hours Pre-Parsing
The `isOpenNow()` function in `includes/functions.php` currently parses OSM opening_hours strings via regex in PHP on every explore API request when the "Open Now" filter is active. At current scale (~2,300 businesses) this takes single-digit milliseconds and is not a bottleneck. However, at 10,000+ businesses this will become a measurable cost.

**Recommended approach when scaling:** Add a cached `next_close_at` / `next_open_at` timestamp pair to the `businesses` table, recalculated by a cron job every 15 minutes. The explore API filter then becomes a simple SQL `WHERE next_close_at > NOW()` instead of fetching all businesses and filtering in PHP. This avoids the complexity of structured day/time columns (OSM hours include seasonal rules, holidays, `24/7`, etc.) while moving the computation off the request path entirely.

---

## Attribution

- Map data and business import data: [OpenStreetMap contributors](https://www.openstreetmap.org/copyright), licensed under the [Open Database License (ODbL)](https://opendatacommons.org/licenses/odbl/)
- Maps rendered with [Leaflet.js](https://leafletjs.com/) and [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster)
- OpenStreetMap tiles served by the [OpenStreetMap Foundation](https://wiki.osmfoundation.org/wiki/Main_Page)

---

## License

Copyright (c) 2026 [Bitcoin Cities](https://bitcoincities.org/). All rights reserved.

This software is proprietary. No part of this codebase may be reproduced, distributed, or transmitted in any form without the prior written permission of the copyright holder. For licensing inquiries, contact the project owner.
