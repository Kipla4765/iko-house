# Iko House — Database Definition

## Engine
PostgreSQL — pairs with Prisma or Drizzle ORM in a Next.js stack.

---

## Entity Overview

```
admins
areas
amenities
listings
  └── listing_photos
  └── listing_pricing_tiers
  └── listing_amenities  →  amenities
  └── inquiries
settings
```

---

## Tables

---

### `admins`

| Column | Type | Constraints |
|---|---|---|
| `id` | `uuid` | PK, default `gen_random_uuid()` |
| `email` | `varchar(255)` | UNIQUE, NOT NULL |
| `password_hash` | `text` | NOT NULL |
| `full_name` | `varchar(255)` | NOT NULL |
| `created_at` | `timestamptz` | NOT NULL, default `now()` |
| `updated_at` | `timestamptz` | NOT NULL, default `now()` |

Seeded manually. No registration endpoint.

---

### `areas`
Admin-managed list of neighborhoods.

| Column | Type | Constraints |
|---|---|---|
| `id` | `uuid` | PK |
| `name` | `varchar(100)` | NOT NULL, UNIQUE |
| `slug` | `varchar(100)` | NOT NULL, UNIQUE |
| `is_active` | `boolean` | NOT NULL, default `true` |
| `created_at` | `timestamptz` | NOT NULL, default `now()` |

---

### `amenities`
Admin-managed lookup list.

| Column | Type | Constraints |
|---|---|---|
| `id` | `uuid` | PK |
| `name` | `varchar(100)` | NOT NULL, UNIQUE |
| `slug` | `varchar(100)` | NOT NULL, UNIQUE |
| `icon` | `varchar(50)` | NULLABLE |
| `sort_order` | `int` | NOT NULL, default `0` |

---

### `listings`

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PK | |
| `area_id` | `uuid` | FK → `areas.id`, NOT NULL | |
| `title` | `varchar(255)` | NOT NULL | |
| `description` | `text` | NOT NULL | Rich text |
| `property_type` | `varchar(50)` | NOT NULL | See values below |
| `furnishing_status` | `varchar(30)` | NOT NULL | `furnished`, `semi_furnished`, `unfurnished` |
| `walk_distance` | `varchar(100)` | NULLABLE | e.g. "3 mins walk" |
| `specific_directions` | `text` | NULLABLE | Landmark / directions |
| `video_url` | `text` | NULLABLE | YouTube or Drive link |
| `contact_name` | `varchar(255)` | NOT NULL | |
| `contact_whatsapp` | `varchar(30)` | NOT NULL | |
| `contact_phone` | `varchar(30)` | NULLABLE | |
| `availability_status` | `varchar(30)` | NOT NULL, default `'available'` | `available`, `coming_soon`, `unavailable` |
| `status` | `varchar(20)` | NOT NULL, default `'active'` | `active`, `paused`, `archived` |
| `cover_photo_id` | `uuid` | NULLABLE | FK → `listing_photos.id` |
| `slug` | `varchar(300)` | NOT NULL, UNIQUE | e.g. `bedsitter-ngara-ab3f` |
| `view_count` | `int` | NOT NULL, default `0` | |
| `created_at` | `timestamptz` | NOT NULL, default `now()` | |
| `updated_at` | `timestamptz` | NOT NULL, default `now()` | |

**`property_type` values:**
`bedsitter`, `single_room`, `studio`, `1_bedroom`, `2_bedroom`, `3_bedroom`, `shared_room`

---

### `listing_pricing_tiers`
One listing can have multiple tiers (e.g. single occupancy and shared per person).

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PK | |
| `listing_id` | `uuid` | FK → `listings.id` ON DELETE CASCADE | |
| `label` | `varchar(100)` | NOT NULL | e.g. "Self-contained", "Shared per person" |
| `occupancy_type` | `varchar(20)` | NOT NULL | `single` or `shared` |
| `price_per_month` | `numeric(10,2)` | NOT NULL | Price per person in KES |
| `currency` | `varchar(5)` | NOT NULL, default `'KES'` | |
| `is_available` | `boolean` | NOT NULL, default `true` | |
| `sort_order` | `smallint` | NOT NULL, default `0` | |

Example:
```
label="Single occupancy", occupancy_type="single", price=7000, is_available=true
label="Shared per person", occupancy_type="shared", price=3500, is_available=true
```

---

### `listing_photos`

| Column | Type | Constraints |
|---|---|---|
| `id` | `uuid` | PK |
| `listing_id` | `uuid` | FK → `listings.id` ON DELETE CASCADE |
| `url` | `text` | NOT NULL |
| `storage_key` | `text` | NOT NULL |
| `sort_order` | `smallint` | NOT NULL, default `0` |
| `created_at` | `timestamptz` | NOT NULL, default `now()` |

---

### `listing_amenities`
Junction table.

| Column | Type | Constraints |
|---|---|---|
| `listing_id` | `uuid` | FK → `listings.id` ON DELETE CASCADE |
| `amenity_id` | `uuid` | FK → `amenities.id` ON DELETE CASCADE |

PK: `(listing_id, amenity_id)`

---

### `inquiries`

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PK | |
| `listing_id` | `uuid` | FK → `listings.id` ON DELETE SET NULL | |
| `renter_id` | `uuid` | NULLABLE | FK stub → future `renters` table; null for anonymous submissions |
| `renter_name` | `varchar(255)` | NOT NULL | |
| `renter_phone` | `varchar(30)` | NULLABLE | At least one of phone/email required at app layer |
| `renter_email` | `varchar(255)` | NULLABLE | |
| `message` | `text` | NULLABLE | |
| `status` | `varchar(20)` | NOT NULL, default `'new'` | `new`, `read`, `responded`, `closed` |
| `created_at` | `timestamptz` | NOT NULL, default `now()` | |

---

### `settings`
Key-value store for platform config.

| Column | Type | Constraints |
|---|---|---|
| `key` | `varchar(100)` | PK |
| `value` | `text` | NOT NULL |
| `description` | `text` | NULLABLE |
| `updated_at` | `timestamptz` | NOT NULL, default `now()` |

Seed values:
```
key="university_name",   value="[University Name]"
key="university_label",  value="[University Main Gate]"
key="platform_name",     value="Iko House"
key="platform_whatsapp", value="+254..."
```

---

## Indexes

```sql
CREATE INDEX idx_listings_status ON listings(status);
CREATE INDEX idx_listings_area ON listings(area_id, status);
CREATE INDEX idx_listings_created ON listings(created_at DESC);
CREATE INDEX idx_pricing_listing ON listing_pricing_tiers(listing_id);
CREATE INDEX idx_listings_fts ON listings
  USING gin(to_tsvector('english', title || ' ' || coalesce(description, '')));
CREATE INDEX idx_inquiries_listing ON inquiries(listing_id);
CREATE INDEX idx_inquiries_created ON inquiries(created_at DESC);
CREATE INDEX idx_photos_listing_order ON listing_photos(listing_id, sort_order);
```

---

## Design Decisions

| Decision | Reason |
|---|---|
| No landlord/tenant tables | Admin manages all listings directly |
| `listing_pricing_tiers` as a child table | A single unit can have both single and shared pricing |
| Contact fields on `listings` | Contact is per-property, not tied to a user account |
| `areas` and `amenities` as tables | Admin can add new entries without a code change |
| `walk_distance` as free text | Simple and clear for renters — no map/GPS logic needed |
| `video_url` as a link | No self-hosted video; YouTube/Drive link is free and reliable |
| `settings` table | University name and platform config are rows, not hardcoded strings |
| `view_count` on listings | Lets admin see which listings get traction |
