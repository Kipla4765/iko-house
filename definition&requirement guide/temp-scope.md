# Iko House — MVP Scope

## What It Is
A curated rental listing platform for properties near a university. The Iko House team visits properties, takes photos and video, and creates listings. Renters browse and contact via WhatsApp.

## How It Works
1. Team visits a property, gets landlord/caretaker consent
2. Takes photos and optionally a short video walkthrough
3. Creates the listing in the admin panel
4. Listing goes live immediately
5. Renter finds listing, taps WhatsApp button, contacts the caretaker/landlord directly

## Roles
| Role | What They Do |
|---|---|
| **Renter** | Browses listings, filters, views detail, taps WhatsApp to contact. No account needed. |
| **Admin** | Creates and manages all listings via a protected admin panel. |

## What Gets Built
- Admin login (single admin account, seeded)
- Admin panel — create, edit, pause, archive listings
- Public listing feed with filters and keyword search
- Listing detail page — photos, video, pricing tiers, amenities, walk distance, WhatsApp CTA
- Web inquiry form on listing detail (stored in DB)
- Mobile-first responsive UI

## What Is Not Built
- Landlord or tenant accounts
- Payments or booking of any kind
- WhatsApp bot
- Map view
- Reviews or ratings
- Multi-location / multi-university support
