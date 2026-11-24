# Siliconics: Product Categories and UI Features

| | |
|---|---|
| **Version** | 1.0 (Draft), 24 Nov 2026 |
| **Related** | BRD v1.2, TRS v1.3 (FR IDs), LLD v1.2 (routes in 14.1, API in 7), [UI architecture diagrams](#9-ui-architecture-diagrams) |
| **Readers** | Product owner, designers, front-end and back-end developers, QA |
| **Scope** | The free prototype build. Every feature here works with free services and simulators; nothing needs a paid provider. |

> IDs: `UI-<AREA>-NN` for UI features, traced to TRS functional requirements (`FR-…`). Categories are **seed data**: admins can add, rename or retire categories and their spec fields later without code changes (TRS FR-CAT-03).

---

## 1. How the catalogue is organised

- **Two levels:** Category → Sub-category. Products always belong to one sub-category.
- **Spec fields per sub-category:** each sub-category defines its structured specification fields (stored in `categories.spec_schema`). These drive the product form for sellers, the "main specifications" on the product page, and the filters on listing pages.
- **Variants:** a product can have variants (for example colour, storage, size). Each variant has its own SKU, price, MRP and stock.
- **Commission and GST rate** are set per category by admins (`commission_bps`, `gst_rate_bps`, HSN code). The values are still to be agreed (TRS Q-01) and confirmed with a chartered accountant.
- **Collections** (section 3) cut across categories: Made in India, launch exclusives, member deals, products of the week and so on. They are flags or computed lists, not categories.

---

## 2. Category tree (seed data)

### 2.1 Mobiles & Tablets
| Sub-category | Example products | Key spec fields (also filters) |
|---|---|---|
| Smartphones | 5G phones, foldables | Brand, OS, RAM, storage, display size, refresh rate, main camera MP, battery mAh, charging W, 5G, SIM type |
| Feature phones | Keypad phones | Brand, dual SIM, battery mAh, 4G/VoLTE |
| Tablets | Android tablets, iPads | Brand, OS, display size, RAM, storage, cellular/Wi-Fi, stylus support |
| Mobile accessories | Chargers, cables, cases, screen guards, phone holders | Type, compatible devices, connector (USB-C, Lightning), wattage |
| Power banks | 10,000–30,000 mAh banks | Capacity mAh, output W, ports, fast-charge standard, weight |

### 2.2 Computers & Laptops
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Laptops | Ultrabooks, business, gaming laptops | Brand, processor, RAM, storage type/size, GPU, display size/resolution, weight, OS, battery hours |
| Desktops & mini PCs | All-in-ones, mini PCs | Processor, RAM, storage, GPU, OS, form factor |
| Monitors | Office, gaming, 4K monitors | Size, resolution, refresh rate, panel type, ports, HDR |
| Keyboards & mice | Mechanical keyboards, wireless mice | Type, connectivity, switch type, layout, DPI |
| Storage | SSDs, HDDs, pen drives, memory cards | Type, capacity, interface, read/write speed |
| Networking | Routers, mesh systems, switches, Wi-Fi adapters | Wi-Fi standard, speed class, bands, ports, coverage |
| Printers & scanners | Ink tank, laser, scanners | Type, functions, connectivity, colour/mono, ppm |
| PC components | CPUs, GPUs, motherboards, RAM, PSUs, cabinets, coolers | Component type, socket/chipset, capacity, wattage, form factor |

### 2.3 Audio
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Headphones | Over-ear, on-ear, neckbands | Type, wired/wireless, ANC, battery hours, driver size, codec |
| True wireless earbuds | TWS earbuds | ANC, battery (buds + case), water resistance (IP), codec, latency mode |
| Bluetooth speakers | Portable, party speakers | Output W, battery hours, IP rating, Bluetooth version |
| Soundbars & home theatre | Soundbars, 5.1 systems | Channels, output W, subwoofer, HDMI eARC, Dolby Atmos |
| Microphones | USB, lavalier, podcast mics | Type, connector, polar pattern |

### 2.4 Wearables
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Smartwatches | AMOLED watches, calling watches | Display size/type, battery days, GPS, Bluetooth calling, sensors, compatibility |
| Fitness bands | Activity trackers | Battery days, sensors, water resistance |
| Smart rings | Health rings | Sizes, sensors, battery days |
| VR / AR headsets | VR headsets, smart glasses | Type, resolution, standalone/tethered, field of view |

### 2.5 TV & Home Entertainment
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Smart TVs | LED, QLED, OLED TVs | Size, resolution, panel, smart OS, refresh rate, HDR, ports |
| Projectors | Home, portable projectors | Brightness lumens, resolution, throw ratio, smart OS |
| Streaming devices | Streaming sticks, set-top boxes | Resolution, OS, remote type |

### 2.6 Cameras & Drones
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Cameras | Mirrorless, DSLR, compact | Sensor size, MP, video resolution, mount, stabilisation |
| Action cameras | Helmet and sports cameras | Video resolution/fps, waterproof depth, stabilisation |
| Security cameras | Wi-Fi, outdoor, dash cams (home) | Resolution, night vision, storage (cloud/SD), two-way audio |
| Drones | Camera drones, mini drones | Camera resolution, flight time, range, weight class (DGCA) |
| Camera accessories | Lenses, tripods, gimbals, lights | Type, mount/compatibility |

### 2.7 Gaming
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Consoles | Home, handheld consoles | Platform, storage, edition |
| Controllers & accessories | Controllers, headsets, chairs, mousepads | Platform compatibility, connectivity |

### 2.8 Smart Home
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Smart plugs, bulbs & switches | Wi-Fi plugs, RGB bulbs | Protocol (Wi-Fi, Zigbee, Matter), voice assistant support, wattage |
| Smart speakers & displays | Voice assistants | Assistant, display size, speaker output |
| Door & security | Video doorbells, smart locks, sensors | Type, power source, connectivity, unlock methods |
| Robot cleaners | Robot vacuums, mops | Suction Pa, battery minutes, mapping, mop function |

### 2.9 Personal & Home Gadgets
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Personal care electronics | Trimmers, hair dryers, electric toothbrushes | Type, battery/corded, run time |
| Air & water | Air purifiers, humidifiers, smart water purifiers | Coverage sq ft, filter type, CADR |
| Kitchen electronics | Smart kettles, induction cooktops, air fryers | Capacity, wattage, smart features |

### 2.10 Tools & DIY Electronics
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Power tools | Drills, drivers, grinders | Type, cordless/corded, voltage, battery Ah, torque |
| Soldering & repair | Soldering stations, heat guns, repair kits | Wattage, temperature range |
| Test & measurement | Multimeters, oscilloscopes, clamp meters | Type, ranges, accuracy, channels |
| Development boards | Microcontroller and single-board computers | Chip, clock, RAM, GPIO count, wireless |
| Electronic components & modules | Sensors, relays, ICs, resistor kits | Component type, package, voltage |
| 3D printers & accessories | FDM/resin printers, filament | Build volume, technology, materials |

### 2.11 Power & Energy
| Sub-category | Example products | Key spec fields |
|---|---|---|
| UPS & inverters | Home inverters, PC UPS | VA rating, battery type, backup time |
| Solar | Panels, kits, charge controllers | Wattage, panel type, efficiency |
| Batteries & chargers | Rechargeable cells, chargers | Chemistry, capacity, size |
| EV charging | Home EV chargers | kW, connector type |

### 2.12 Car & Bike Electronics
| Sub-category | Example products | Key spec fields |
|---|---|---|
| Dash cams | Front, dual dash cams | Resolution, night vision, parking mode, GPS |
| Car audio & infotainment | Stereos, Android Auto/CarPlay units | Screen size, CarPlay/Android Auto, output W |
| Trackers & helmets tech | GPS trackers, helmet intercoms | Connectivity, battery, range |
| Car accessories | Car chargers, tyre inflators, vacuum cleaners | Type, power source, output |

---

## 3. Collections and badges (across categories)

| Collection / badge | How it is decided | Where it shows | Trace |
|---|---|---|---|
| **Made in India (Local)** | Seller sets the flag; admin can verify | Badge on cards and product page; filter; home "Local picks" | FR-CAT-02 |
| **Launch exclusive** | Admin creates a launch for a new local product | Launch page, home hero, badge | FR-LAU-01 |
| **Member deal: 80% off** | Deal units calculated per launch (TRS 7.1) | "Buy with 80% discount" button for Electra Members | FR-LAU-03 |
| **Products of the week** | Weekly score from sales and ratings | Home section, badge | FR-DIS-02 |
| **Verified company** | Seller's delivered sales over ₹4,00,000 in the last 12 months | Tick next to company name | FR-SEL-06 |
| **Promoted by** | Verified promoters who pressed Promote | Product page section; card hint | FR-PRO-03 |
| **Creator reviewed** | At least one approved creator review | Badge; filter | FR-CRE-04 |
| **Recommended for you / Trending** | Rule-based recommendations | Home, product page | FR-DIS-03 |
| **New arrivals** | Listed in the last 30 days | Home, sort option | — |
| **Back in stock / price drop** | Wishlist alerts | Notification and wishlist | FR-CHK-10 |

---

## 4. Product data shown in the UI

| Field | Product card | Product page | Seller form |
|---|---|---|---|
| Name, brand, model | Yes | Yes | Required |
| Main image | Yes | Gallery (3–10 images) | 3–10 images required |
| Price and MRP (discount %) | Yes | Yes | Required (price ≤ MRP) |
| Rating and count | Yes | Yes | — |
| Badges (section 3) | Yes | Yes | Local flag |
| Main specifications | Up to 3 | Full table from spec fields | Per sub-category form |
| Variants | Colour dots | Selector | Variant editor |
| Stock left | "Only N left" under 10 | Exact count | Inventory field |
| Electrons you earn | — | Yes | — |
| Warranty and replacement | — | Yes | Required |
| Country of origin | — | Yes | Required |
| About the company | — | Section with links | Company profile |

---

## 5. Search, filters and sorting

- **Search box** (every page): suggestions for products, brands and categories as you type; recent searches (stored on the device).
- **Global filters:** category, price range, brand, rating (4★ and up…), Made in India, in stock, launch deals, creator reviewed, verified company.
- **Category filters:** the spec fields marked as filters in that sub-category (for example RAM and storage for smartphones).
- **Sort:** relevance, price low→high, price high→low, newest, top rated, most popular.
- **Results:** grid on desktop, two-column list on phones; 24 per page with "Load more"; filter chips show active filters with one-tap remove.

---

## 6. UI features

### 6.1 Global (every page)
| ID | Feature | Who | Trace |
|---|---|---|---|
| UI-GLB-01 | Header: logo, search, category menu, cart with item count, wishlist, notification bell, account menu | All | BR-ACC-01 |
| UI-GLB-02 | Category mega-menu (desktop) and drawer (phone) with the 12 categories | All | FR-CAT-04 |
| UI-GLB-03 | Notification bell with unread count, live updates and a list panel | Signed in | FR-NOT-01 |
| UI-GLB-04 | Account menu shows the account type (Buyer, Seller, Creator, Promoter, Admin) and its portal link | Signed in | FR-ROLE-01 |
| UI-GLB-05 | **TEST MODE banner** with links to the test inbox and simulator help (prototype only) | All | FR-NOT-05 |
| UI-GLB-06 | **Acting-as banner** for admins who switched role, with "Return to admin" | Admin | FR-ROLE-07 |
| UI-GLB-07 | Footer: For Content Creators, For Promoters, help, legal pages, grievance officer contact | All | FR-ADM-08 |
| UI-GLB-08 | Toasts for success and errors; friendly messages for rate limits (429) | All | NFR-RATE-02 |
| UI-GLB-09 | Skeleton loading states; no layout jumps | All | NFR-ACC-05 |
| UI-GLB-10 | Accessibility: skip link, full keyboard use, screen-reader labels, colour-blind safe status chips (icon + text) | All | NFR-ACC-01–03 |
| UI-GLB-11 | Responsive layout from 360 px phones to large desktops; bottom navigation on phones (Home, Categories, Cart, Account) | All | NFR-ACC-04 |
| UI-GLB-12 | Legal re-acceptance prompt when terms change | Signed in | FR-ADM-08 |

### 6.2 Public pages
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-PUB-01 | Home hero with the live or upcoming launch deal and countdown | Home | FR-DIS-01 |
| UI-PUB-02 | Home sections: Products of the week, Local picks, Popular, Recommended / Trending, categories, creator and promoter panels | Home | FR-DIS-01 |
| UI-PUB-03 | Category and search listing with filters, sort and chips | Category, Search | FR-CAT-04 |
| UI-PUB-04 | Product gallery, variant selector, price/MRP, stock, Electrons preview | Product | FR-PDP-02 |
| UI-PUB-05 | Role-based action area: Buy / Add to cart / Wishlist (buyer), "Buy with 80% discount" (member), Promote (promoter), Request demo / Add review (creator) | Product | FR-PDP-01, 03, 07 |
| UI-PUB-06 | Review tabs: Promoted by · Creator reviews (thumbs up/down) · Buyer reviews (photos filter), 10 per page | Product | FR-PDP-04 |
| UI-PUB-07 | About the company with verified tick and links | Product | FR-PDP-06 |
| UI-PUB-08 | Similar and related products carousel | Product | FR-PDP-08 |
| UI-PUB-09 | Private feedback to manufacturer form | Product | FR-REV-05 |
| UI-PUB-10 | Launch page: countdown, live units left, member limit, waiting-room position, 10-minute hold timer | Launch | FR-LAU-04–11 |
| UI-PUB-11 | Sold-out state: "All items on discount were purchased", buttons disabled; "reserved, may free up" state | Launch, Product | FR-LAU-08 |
| UI-PUB-12 | For Content Creators / For Promoters pages: how it works, criteria, rewards, apply button | Info pages | FR-CRE-07, BR-ACC-02 |
| UI-PUB-13 | Legal pages (terms per role, privacy policy, returns policy) | Legal | FR-ADM-08 |

### 6.3 Sign-in and accounts
| ID | Feature | Trace |
|---|---|---|
| UI-AUT-01 | Sign-in page: **mobile number + OTP highlighted**, "Continue with Google" second; account type chooser (Buyer, Seller, Creator, Promoter) | FR-AUTH-01 |
| UI-AUT-02 | OTP entry: 6 boxes, 30-second resend timer, attempts left; link to the test inbox in the prototype | FR-AUTH-02, FR-NOT-05 |
| UI-AUT-03 | "Choose account" when one phone or Google identity has several role accounts | FR-ROLE-03 |
| UI-AUT-04 | Verify mobile step for Google accounts before checkout or applications | FR-AUTH-04 |
| UI-AUT-05 | Admin second step: authenticator (TOTP) code | FR-AUTH-06 |
| UI-AUT-06 | Guest cart merged after sign-in, with a notice if quantities changed | FR-AUTH-09 |

### 6.4 Buyer
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-BUY-01 | Cart: grouped by seller, quantity steppers, price-change and stock warnings, Electrons earn preview | Cart | FR-CHK-01 |
| UI-BUY-02 | One-page checkout: address → Electrons slider → payment method (COD shown only when eligible, with the reason when not) → place order | Checkout | FR-CHK-04–07 |
| UI-BUY-03 | Hand-off to the sim-pay test checkout and the result page (waits for confirmation) | Checkout result | FR-PAY-03 |
| UI-BUY-04 | Orders list with status chips; order detail timeline per shipment with tracking and invoice download | Orders | FR-ORD-01, 05 |
| UI-BUY-05 | Cancel before shipping | Order detail | FR-ORD-02 |
| UI-BUY-06 | Return or replace within 7 days: reason, photos, pickup status | Returns | FR-ISS-01, 02 |
| UI-BUY-07 | Raise an issue in 3 steps; issue chat and status | Issues | FR-ISS-03, 04 |
| UI-BUY-08 | Write a review (stars, text, up to 5 photos) for delivered items; mark reviews helpful | Order detail, Product | FR-REV-01, 03 |
| UI-BUY-09 | Electrons page: available, pending, next expiry, history with order links | Electrons | FR-REW-06 |
| UI-BUY-10 | Wishlist with back-in-stock and price-drop alerts | Wishlist | FR-CHK-10 |
| UI-BUY-11 | Addresses, profile, notification preferences, active sessions, account deletion | Account | FR-AUTH-07, 08 |

### 6.5 Electra Member
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-MEM-01 | Membership page: benefits, ₹650/year, pay with money and/or Electrons | Membership | FR-MEM-01 |
| UI-MEM-02 | Auto-renew toggle (on by default), next charge date, pre-debit notice history | Membership | FR-MEM-04, 05 |
| UI-MEM-03 | Member badge in the header; member price and COD hints across the site | Global | FR-MEM-03 |
| UI-MEM-04 | Launch alerts opt-in | Launch | FR-LAU-04 |

### 6.6 Seller portal
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-SEL-01 | Onboarding wizard: business type, legal details, PAN, GSTIN, bank (sim penny-drop), documents, terms; approval status tracker | Onboarding | FR-SEL-01 |
| UI-SEL-02 | Dashboard: sales, orders to fulfil, returns, rating, feedback, next payout | Dashboard | FR-SEL-08 |
| UI-SEL-03 | Product list and editor: category picker, spec form from the sub-category, variants, images, warranty, Local flag; pause/publish | Products | FR-SEL-02 |
| UI-SEL-04 | Inventory editing per variant | Products | FR-SEL-02 |
| UI-SEL-05 | Orders: accept, pack, ship (sim-ship label and tracking), invoice | Orders | FR-ORD-04 |
| UI-SEL-06 | Returns: approve or reject with reason | Returns | FR-ISS-02 |
| UI-SEL-07 | Creator demo requests: approve or decline, ship demo | Demo requests | FR-SEL-07 |
| UI-SEL-08 | Promoters: confirm investors; set promoter reward per product | Promoters | FR-PRO-02, 04 |
| UI-SEL-09 | Feedback inbox with one reply | Feedback | FR-REV-05 |
| UI-SEL-10 | Payouts: weekly statements, commission and deductions, download | Payouts | FR-SEL-04 |
| UI-SEL-11 | Company profile: description, links, verified status | Company | FR-SEL-03, 06 |

### 6.7 Creator portal
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-CRE-01 | Application: social profiles, follower counts and evidence, niche, sample links, terms; criteria check and status | Onboarding | FR-CRE-01, 02 |
| UI-CRE-02 | Dashboard: reviews, agree/disagree counts, earnings, balance | Dashboard | FR-CRE-05 |
| UI-CRE-03 | Demo requests and shipment tracking; review due date | Demos | FR-CRE-03 |
| UI-CRE-04 | Write a creator review: rating, text, video/post link preview | Reviews | FR-CRE-04 |
| UI-CRE-05 | Earnings by product and month (reward pool) | Earnings | FR-CRE-06 |
| UI-CRE-06 | Payout KYC: PAN and bank (sim-kyc) | KYC | FR-CO-02 |
| UI-CRE-07 | Cash-out: amount (min 250 Electrons), weekly payout status, statements | Cash-out | FR-CO-01–05 |

### 6.8 Promoter portal
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-PRO-01 | Application: individual or company details, investments, proof upload, terms; status | Onboarding | FR-PRO-01, 02 |
| UI-PRO-02 | Promotable products (verified startups only) with Promote / Stop promoting | Products | FR-PRO-03 |
| UI-PRO-03 | Dashboard: promoted products, sales, rewards | Rewards | FR-PRO-05 |

### 6.9 Admin console
| ID | Feature | Page | Trace |
|---|---|---|---|
| UI-ADM-01 | Overview: health status, today's orders, approvals waiting, issues overdue | Admin home | FR-HLT-03 |
| UI-ADM-02 | Approval queues for sellers, creators, promoters with documents and decision reasons | Approvals | FR-ADM-01 |
| UI-ADM-03 | Launch creator with the deal-unit calculator (k, revenue, profit %) and scheduling | Launches | FR-LAU-01, 02 |
| UI-ADM-04 | Live launch monitor: held, sold, remaining, queue length | Launch detail | FR-ADM-02 |
| UI-ADM-05 | Users: search, view, suspend, reactivate; create admins (max 3) | Users | FR-ADM-04, FR-ROLE-06 |
| UI-ADM-06 | Role switch into any role's view | Global | FR-ROLE-07 |
| UI-ADM-07 | Orders, issues and returns handling with assignment and notes | Orders, Issues, Returns | FR-ADM-05 |
| UI-ADM-08 | Payout batches: review, approve, release | Payouts | FR-ADM-06 |
| UI-ADM-09 | Review moderation | Reviews | FR-REV-02 |
| UI-ADM-10 | Categories and spec fields, commission and GST per category | Categories | FR-CAT-03 |
| UI-ADM-11 | Configuration (Electrons, membership, COD, pools, rate limits) with history | Config | FR-ADM-03 |
| UI-ADM-12 | Legal documents with versions | Legal | FR-ADM-08 |
| UI-ADM-13 | Reports with CSV export | Reports | FR-ADM-07 |
| UI-ADM-14 | Health monitor: checks, uptime, latency, errors, queues | Health | FR-HLT-03 |
| UI-ADM-15 | Audit log viewer | Audit | FR-ADM-09 |
| UI-ADM-16 | Ledger adjustment with second-admin approval | Users | FR-REW-01 |

### 6.10 Test tools (prototype only, free)
| ID | Feature | Trace |
|---|---|---|
| UI-TST-01 | Test inbox (`/test-inbox`): search messages and OTPs by phone number, tester password | FR-NOT-05 |
| UI-TST-02 | sim-pay checkout page with test UPI IDs and cards listed on the page | D-05 |
| UI-TST-03 | Simulator help page listing all test inputs (free-services.md section 2) | D-20 |

---

## 7. Page inventory

| Route | Page | Who | Rendering | Main API calls |
|---|---|---|---|---|
| `/` | Home | All | ISR 120 s + live block | `GET /home`, `/launches?status=live` |
| `/c/[slug]` | Category listing | All | ISR + client filters | `GET /products?category=` |
| `/search` | Search results | All | Client | `GET /products?q=` |
| `/p/[slug]` | Product page | All | ISR 60 s + live block | `GET /products/:slug`, `/live`, `/reviews`, `/similar` |
| `/launches/[id]` | Launch page | All (buy: members) | ISR + SSE | `GET /launches/:id`, `/stream`, `POST /reserve` |
| `/for-creators`, `/for-promoters` | Programme info | All | Static | — |
| `/legal/[doc]` | Legal | All | Static | — |
| `/auth/sign-in`, `/auth/verify`, `/auth/choose-account`, `/auth/mfa` | Sign-in steps | Guests | Client | `/auth/*` |
| `/cart` | Cart | Guest, buyer | Client | `/cart` |
| `/checkout`, `/checkout/result/[orderId]` | Checkout | Buyer | SSR + client | `/checkout/quote`, `POST /orders`, `GET /orders/:id` |
| `/account/*` | Orders, returns, issues, Electrons, membership, wishlist, addresses, notifications, sessions | Buyer | SSR | `/orders`, `/returns`, `/issues`, `/electrons`, `/membership`, `/wishlist`, `/me/*` |
| `/seller/*` | Seller portal (11 pages) | Seller | SSR + tables | `/seller/*` |
| `/creator/*` | Creator portal (6 pages) | Creator | SSR | `/creator/*` |
| `/promoter/*` | Promoter portal (3 pages) | Promoter | SSR | `/promoter/*` |
| `/admin/*` | Admin console (16 pages) | Admin | SSR, TOTP required | `/admin/*` |
| `/test-inbox` | Test inbox | Testers | Served by sim-notify | — |

---

## 8. Key UI states

| Component | States |
|---|---|
| Product action button | Buy now · Add to cart · Out of stock (disabled, "Notify me") · Members: Buy with 80% discount · Deal reserved by others ("may free up") · All items on discount were purchased (disabled) · Sign in to buy |
| Launch page | Upcoming (countdown, alert opt-in) · Live · In queue (position) · Held (10-minute timer) · Deal sold out · Normal-price sale · Completed |
| Order status chip | Pending payment · Confirmed · Packed · Shipped · Out for delivery · Delivered · Cancelled · Expired · Returned to origin (each with icon and text) |
| Application status (seller, creator, promoter) | Draft · Submitted · Under review · Approved · Rejected (reason, re-apply) · Suspended |
| Electrons | Pending (unlocks on date) · Available · Expiring soon · Expired · Reversed |
| Lists and tables | Loading skeleton · Empty (with a next step) · Error (retry) · Paged |

---

## 9. UI architecture diagrams

Interactive versions are in `.archify/ui-siliconics-pages-20261124-105850/` (start with `frontend-architecture.html`; each route group opens its page structure and page flow). PNG exports:

| Diagram | Shows |
|---|---|
| [Front-end architecture](design/ui/frontend-architecture.png) | How the Next.js app is organised: middleware, app shell, route groups, design system, data layer, API and free test pages |
| [Page structure: public and sign-in](design/ui/pages-public.png) | Every public and sign-in page and its main sections, plus the global shell |
| [Page structure: buyer and member](design/ui/pages-buyer.png) | Every buyer and member page and its main sections |
| [Page structure: seller portal](design/ui/pages-seller.png) | Every seller page and its main sections |
| [Page structure: creator and promoter portals](design/ui/pages-creator-promoter.png) | Every creator and promoter page and its main sections |
| [Page structure: admin console](design/ui/pages-admin.png) | Every admin page and its main sections |
| [Page flow: shopping and checkout](design/ui/flow-shopping.png) | Guest to buyer: browse, cart, sign-in, checkout, test payment, orders, returns, reviews, issues |
| [Page flow: member launch deal](design/ui/flow-launch.png) | Membership, launch page, waiting room, hold, checkout, sold-out and expired paths |
| [Page flow: seller](design/ui/flow-seller.png) | Application, admin review, products, orders, shipping, returns, payouts |
| [Page flow: creator and promoter](design/ui/flow-creator-promoter.png) | Applications, checks, demos, reviews, buyer votes, earnings, cash-out; promoting products |
| [Page flow: admin](design/ui/flow-admin.png) | TOTP sign-in, approvals, operations, payouts, launches with the calculator, health, config, role switch |
