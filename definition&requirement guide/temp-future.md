# Iko House — Future Roadmap

Parked here so it's not lost. None of this is in scope for MVP.

---

## P1 — WhatsApp Bot
- Automated inquiry handling via a platform WhatsApp number
- Bot receives a message, identifies the listing, notifies the caretaker/landlord, logs the inquiry
- DB is already ready for this — `inquiries` table has `channel`, `renter_whatsapp`, and `bot_session_id` fields from day one

## P2 — Landlord / Agent Self-Service
- Landlords or agents create accounts and post their own listings
- Listings go into a review queue before going live
- Verified agent badge (manual, admin-granted)
- Listing agreement / consent flow

## P3 — Expand Beyond One University
- Multi-area, multi-city support
- Areas table is already data-driven so no schema change needed
- University / reference point is a configurable setting

## P4 — Tenant Features
- Tenant accounts
- Saved/favourited listings
- Listing enquiry history

## P5 — Reviews & Trust
- Tenant reviews on listings
- Landlord response to reviews
- Verified listing badge (team-visited, photos authentic)

## P6 — Property Management Services
- Maintenance request tracking for listed properties
- Rent payment reminders (not processing)
- Occupancy tracking for landlords
- Dashboard for landlords managing multiple units

## P7 — Booking Layer (Optional, Evaluate Later)
- Reserve a viewing slot
- Hold a room with a deposit
- Requires payment processing — evaluate if this fits the platform identity
