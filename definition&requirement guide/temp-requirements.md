# Iko House — Requirements Specification

## 1. Admin Authentication

**FR-01** — Admin logs in with email and password. Single admin account, seeded directly in the database. No registration page.

**FR-02** — Admin can log out and reset their password via a reset link sent to their email.

**FR-03** — All `/admin/*` routes are protected. Unauthenticated requests redirect to the login page.

---

## 2. Listings

**FR-04** — Admin creates a listing with these fields:

| Field | Type | Required |
|---|---|---|
| Title | Short text | Yes |
| Description | Rich text | Yes |
| Property type | Select: `bedsitter`, `single_room`, `studio`, `1_bedroom`, `2_bedroom`, `3_bedroom`, `shared_room` | Yes |
| Furnishing status | Select: `furnished`, `semi_furnished`, `unfurnished` | Yes |
| Area | Select from areas list | Yes |
| Walk distance | Free text (e.g. "3 mins walk") | Yes |
| Specific directions | Free text, landmark or how to find it | No |
| Photos | Upload, min 1 max 12, cover photo selectable | Yes |
| Video tour URL | YouTube or Google Drive link | No |
| Amenities | Multi-select from amenities list | No |
| Pricing tiers | One or more (see FR-05) | Yes |
| Contact name | Caretaker / landlord name | Yes |
| Contact WhatsApp | Phone number for WA button | Yes |
| Contact phone | If different from WhatsApp | No |
| Availability status | Select: `available`, `coming_soon`, `unavailable` | Yes |

**FR-05** — A listing has one or more pricing tiers. Each tier has:
- Label — free text (e.g. "Self-contained", "Shared per person")
- Occupancy type — `single` or `shared`
- Price per person per month (KES)
- Available — yes/no toggle per tier

**FR-06** — Admin can edit any listing field at any time. Changes are live immediately.

**FR-07** — Admin can set listing status to `active`, `paused`, or `archived`. Only `active` listings appear publicly.

**FR-08** — Photos are uploaded to object storage. Admin can reorder photos and set a cover photo.

**FR-09** — Admin can manage the areas list: add, rename, show/hide an area.

**FR-10** — Admin can manage the amenities list: add, rename, reorder.

---

## 3. Public Listing Feed

**FR-11** — Public feed shows all `active` listings, newest first.

**FR-12** — Renter can filter by:
- Property type
- Max price (matched against lowest tier price on a listing)
- Furnishing status
- One or more amenities
- Availability — "Available now" toggle filters to listings with at least one available tier

**FR-13** — Renter can search by keyword. Matches against title, description, and area name.

**FR-14** — Each listing card shows: cover photo, title, area, walk distance, property type, lowest price with occupancy label.

---

## 4. Listing Detail Page

**FR-15** — Listing detail page shows:
- Photo gallery (swipeable on mobile)
- Video tour — embedded or linked if URL provided
- Title, area, walk distance, specific directions
- Property type, furnishing status
- All pricing tiers in a clear list
- All amenities as an icon/label list
- Contact name
- WhatsApp CTA button (see FR-16)
- Web inquiry form (see FR-17)

**FR-16** — WhatsApp CTA button opens:
`https://wa.me/<number>?text=Hi%2C+I%27m+interested+in+<listing-title>+listed+on+Iko+House`

**FR-17** — Renter can submit an inquiry form: name, phone or email (at least one required), message (optional). No account required. Inquiry is saved to the database and visible in the admin panel.

---

## 5. Admin Panel Pages

**FR-18** — Admin dashboard shows: count of active listings, count of paused listings, count of new inquiries (last 7 days).

**FR-19** — Listings page: table of all listings with status, title, area, date created. Admin can filter by status. Click through to edit.

**FR-20** — Listing create/edit form covering all fields in FR-04 and FR-05.

**FR-21** — Inquiries page: list of all submitted inquiries showing renter name, contact, message, linked listing, date. Admin can mark as read or closed.

**FR-22** — Areas management page (FR-09).

**FR-23** — Amenities management page (FR-10).

---

## Non-Functional Requirements

**NFR-01 — Mobile-first**: UI designed for mobile screens first. Renters are primarily on phones.

**NFR-02 — Image delivery**: Photos stored in object storage (Cloudflare R2, Supabase Storage, or equivalent). Served via CDN. Max 5MB per image. Accepted formats: JPEG, PNG, WebP.

**NFR-03 — Video**: Stored as a URL only. No video uploads.

**NFR-04 — Security**:
- Admin password hashed with bcrypt or argon2
- Rich text input sanitized before storage and render (XSS prevention)
- File uploads validated on type and size server-side
- Rate limiting on the inquiry submission endpoint
- All admin routes require an authenticated session

**NFR-05 — Data-driven config**: Areas and amenities are database rows, not hardcoded values. New entries added via admin UI without a code change.
