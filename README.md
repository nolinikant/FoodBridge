# FoodBridge: Good Food. Shared Hope.

A front-end demo of a surplus food recovery network. Donors (banquet halls, hotels, messes, cafeterias) list leftover food, verified NGOs request it, volunteers collect and deliver it, and every step is recorded until the meals are confirmed distributed.

Built as a college project demo for **CSE326**. It is not a real service: all data is sample data stored in your own browser.

**Live demo:** _add your published link here_

---

## Features

- **Food listings** with search, category / location / status filters and sorting by pickup deadline or quantity
- **Donation form** with full client-side validation and a live preview card
- **Eligibility rules** that decide whether a listing can be requested (verified, not expired, still available)
- **NGO request flow**: request a donation, donor approves or rejects, and a dispatch is created automatically
- **Volunteer workspace** that moves each dispatch through pickup, delivery and distribution
- **Dashboard** with totals, a dispatch status chart and recent activity
- **Dispatch reports** with filters, **CSV export** and a print-friendly layout
- **Impact page** with live stats from the demo records
- **Role-based demo login** with five sample accounts
- Responsive layout (mobile menu, card-style tables on small screens), keyboard-visible focus styles, and reduced-motion support

## Demo accounts

All accounts use the password **`Demo@1234`**. On the login page, click **Use** next to a role to fill the form.

| Role | Email | Lands on |
|---|---|---|
| Admin | `admin@foodbridge.demo` | Dashboard |
| Donor | `donor@foodbridge.demo` | Donate Food |
| NGO | `ngo@foodbridge.demo` | NGO & Volunteer |
| Volunteer | `volunteer@foodbridge.demo` | NGO & Volunteer |
| Recipient | `recipient@foodbridge.demo` | Food Listings |

> Roles currently only set the landing page and the badge in the header. Pages are not permission-gated.

## Getting started

There is no build step and nothing to install. The whole app is one file: `index.html`.

1. Download or clone the repository.
2. Open `index.html` in a modern browser, or serve the folder locally:

   ```bash
   python -m http.server 8000
   # then visit http://localhost:8000
   ```

3. An internet connection is needed for the Inter font, the Lucide icons (cdnjs) and the Unsplash photos. If a photo fails to load, the card shows a green placeholder instead.

### Two-minute walkthrough

1. Open **Food Listings** and click **Request Food** on an eligible donation.
2. Go to **NGO & Volunteer** and **Approve** the request. A dispatch (`FB-1006`, and so on) is created.
3. Under **Volunteer tasks**, advance it: *Accept pickup* → *Confirm pickup* → *Confirm delivery* → *Confirm distribution*.
4. Open **Reports** to see the full trail, then **Export CSV** or **Print**.
5. Use **Dashboard → Reset** to restore the sample data.

## Pages

The app is a hash-routed single page app.

| Route | What it shows |
|---|---|
| `#/home` | Hero, demo stats, how it works, featured donations, photo gallery |
| `#/how` | Jumps to the "How it works" section on the home page |
| `#/listings` | Search, filter and sort donations; view details; request food |
| `#/donate` | Donation form with preview |
| `#/impact` | Live totals, illustrative monthly chart, gallery |
| `#/about` | Problem, solution, objectives, future development, limitations |
| `#/dash` | Totals, dispatch status chart, recent activity, reset button |
| `#/network` | NGO profile, incoming requests, volunteer tasks |
| `#/reports` | Dispatch table with filters, CSV export and print |
| `#/login` | Login form and demo accounts |

## How a donation moves through the system

**1. Listing eligibility.** A listing can only be requested when it is `AVAILABLE`, its pickup deadline has not passed, and its safety status is `VERIFIED`. Otherwise it shows as *Pending verification*, *Expired* or *Unavailable* and the Request button is disabled.

**2. Dispatch lifecycle.** Approving a request creates a dispatch that follows this flow:

```
APPROVED → PICKUP_ASSIGNED → COLLECTED → DELIVERED → DISTRIBUTED
    └──────────┴─→ CANCELLED
```

| Transition | Button label | What it records |
|---|---|---|
| `APPROVED` → `PICKUP_ASSIGNED` | Accept pickup / assign | Assigns a demo volunteer |
| `PICKUP_ASSIGNED` → `COLLECTED` | Confirm pickup | Pickup time and collected quantity |
| `COLLECTED` → `DELIVERED` | Confirm delivery | Delivery time and delivered quantity |
| `DELIVERED` → `DISTRIBUTED` | Confirm distribution | Confirmed servings (simulated as 90% of the estimate) |

`CANCELLED` is available before collection and asks for confirmation. `EXPIRED` and `REJECTED` are terminal states. Invalid transitions are blocked in code.

## Form validation

The donation form is validated in the browser before submission:

| Field | Rule |
|---|---|
| Organization, food title | Required |
| Contact | Valid email, or phone number with 8 to 15 digits |
| Quantity | Greater than 0 and at most 5000 kg |
| Estimated servings | Greater than 0 and at most 20000 |
| Pickup address | At least 8 characters |
| Prepared at | Required, cannot be in the future |
| Pickup deadline | Required, must be in the future and after preparation time |
| Photo (optional) | Image file under 2 MB; preview only, not saved |

User-supplied text is HTML-escaped before rendering, and CSV cells that start with `=`, `+`, `-` or `@` are neutralised on export to prevent spreadsheet formula injection.

## Tech stack

- Plain **HTML, CSS and vanilla JavaScript** in a single file, with no framework or build tooling
- **Inter** font via Google Fonts
- **Lucide** icons 0.263.1 via cdnjs
- **Unsplash** stock photos (illustrative only)

## Data and storage

| Key | Storage | Purpose |
|---|---|---|
| `fb_front_v1` | `localStorage` | Donations, dispatches and requests |
| `fb_session_v1` | `sessionStorage` | Logged-in demo user (cleared when the tab closes) |

Sample data is generated on first load, with times relative to the current moment so deadlines always look realistic. To reset, use **Dashboard → Reset**, or clear the `fb_front_v1` key in your browser's dev tools.

## Limitations

This is a demo, and these are known and intentional:

- **No backend or database.** Data lives only in your browser and is never shared between users or devices.
- **Not real authentication.** The accounts and password are in the source and checked client-side. A production system needs a server that hashes passwords and issues tokens.
- **No server-side validation or permission checks.** Client-side validation is not a security boundary.
- **No verification step in the UI.** Newly submitted donations are saved as *pending verification* and there is no admin screen to verify them, so only the seeded verified listings can be requested.
- **Requests use a fixed NGO.** *Request Food* always creates the request as "Hope Community Kitchen (demo)".
- **Contact details are validated but not stored.**
- **No maps or notifications.**
- Monthly bars on the Impact page are illustrative, and estimated servings are estimates, not confirmed meals.
- Listings are not a food-safety certificate. Real handling must be confirmed before distribution.

## Roadmap

- Real backend and database (for example Node.js, Express and PostgreSQL) with proper authentication and role-based access control
- Admin verification workflow for donations and NGOs
- Maps and distance-based matching between donors, NGOs and volunteers
- Email / SMS notifications
- Food-safety audits and an NGO verification process

## Customising

All of these live near the top of the `<script>` block in `index.html`:

- **Images:** edit the `IMG` object to point at local files instead of Unsplash URLs.
- **Sample data:** edit `seed()`. If you change the data shape, bump `KEY` (for example to `fb_front_v2`) so old browser data is ignored.
- **Demo accounts and password:** edit `ACCTS` and `DEMO_PW`.
- **Workflow:** `FLOW`, `TR` and `LBL` define the statuses, allowed transitions and button labels.
- **Colours:** change the CSS variables in `:root` (`--g` green, `--d` deep green, `--c` cream, `--o` orange).

## Credits

- Photos from [Unsplash](https://unsplash.com), used as illustrative stock images (not real FoodBridge beneficiaries)
- Icons by [Lucide](https://lucide.dev)
- Typeface: [Inter](https://rsms.me/inter/)
