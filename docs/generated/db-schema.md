# Database Schema (Auto-Generated)

> This file is auto-generated from the Supabase schema. Do not edit manually.
> Regenerate with: `supabase gen types typescript --linked > docs/generated/db-schema.md`

---

## Tables

### profiles
| Column | Type | Nullable | Default |
|---|---|---|---|
| id | uuid | NO | — |
| email | text | NO | — |
| full_name | text | YES | — |
| avatar_url | text | YES | — |
| role | text | NO | 'user' |
| company_name | text | YES | — |
| phone | text | YES | — |
| subscription_tier | text | YES | 'free' |
| created_at | timestamptz | YES | now() |
| updated_at | timestamptz | YES | now() |

### properties
| Column | Type | Nullable | Default |
|---|---|---|---|
| id | uuid | NO | gen_random_uuid() |
| owner_id | uuid | NO | — |
| address | text | NO | — |
| area | text | NO | — |
| property_type | text | NO | — |
| bedrooms | integer | NO | 0 |
| bathrooms | integer | YES | 0 |
| sqft | numeric | NO | — |
| price_aed | numeric | NO | — |
| annual_rent_aed | numeric | YES | — |
| service_charge_aed | numeric | YES | — |
| latitude | numeric | YES | — |
| longitude | numeric | YES | — |
| description | text | YES | — |
| images | text[] | YES | '{}' |
| has_3d_tour | boolean | YES | false |
| created_at | timestamptz | YES | now() |
| updated_at | timestamptz | YES | now() |

### tours
| Column | Type | Nullable | Default |
|---|---|---|---|
| id | uuid | NO | gen_random_uuid() |
| property_id | uuid | NO | — |
| owner_id | uuid | NO | — |
| scene_url | text | YES | — |
| thumbnail_url | text | YES | — |
| status | text | YES | 'queued' |
| world_api_job_id | text | YES | — |
| quality | text | YES | 'standard' |
| photo_count | integer | YES | 0 |
| processing_time_ms | integer | YES | — |
| is_public | boolean | YES | false |
| share_password | text | YES | — |
| created_at | timestamptz | YES | now() |
| updated_at | timestamptz | YES | now() |

### annotations
| Column | Type | Nullable | Default |
|---|---|---|---|
| id | uuid | NO | gen_random_uuid() |
| tour_id | uuid | NO | — |
| type | text | NO | — |
| title | text | NO | — |
| description | text | YES | — |
| x | numeric | NO | — |
| y | numeric | NO | — |
| z | numeric | NO | — |
| icon | text | YES | 'info' |
| created_at | timestamptz | YES | now() |

### roi_calculations
| Column | Type | Nullable | Default |
|---|---|---|---|
| id | uuid | NO | gen_random_uuid() |
| user_id | uuid | NO | — |
| property_id | uuid | YES | — |
| purchase_price | numeric | NO | — |
| annual_rent | numeric | NO | — |
| service_charge | numeric | YES | 0 |
| has_mortgage | boolean | YES | false |
| down_payment_pct | numeric | YES | — |
| mortgage_rate_pct | numeric | YES | — |
| mortgage_term_years | integer | YES | — |
| holding_period_years | integer | YES | 5 |
| appreciation_rate_pct | numeric | YES | 5 |
| gross_yield | numeric | YES | — |
| net_yield | numeric | YES | — |
| monthly_cashflow | numeric | YES | — |
| irr_5yr | numeric | YES | — |
| irr_10yr | numeric | YES | — |
| created_at | timestamptz | YES | now() |

