# Yarn to Cone Inventory Website

This project now uses only HTML, CSS, and JSON files.

## Files
- `index.html` (UI + in-page logic)
- `styles.css` (styling)
- `seed-data.json` (users + initial business data)

## Covers your workflow
- Yarn to cone process tracking
- Dyeing and doubling entries with vendors/mills
- Transfer logs between any user-defined locations
- Inventory entry is kg-first: user enters only kg, and bags are auto-derived (`1 bag = 20 kg`).
- Work Entry is simplified: user enters sent kg and received kg; system calculates loss automatically.
- Work Entry location and input item are selected from inventory-backed dropdowns to avoid mismatch/no-stock errors.
- Cone-related processes (`Yarn To Cone`, `Baby Cone`) show `Cones Produced` and optional auto-add to inventory.
- Editing/removing Work Entry recalculates linked inventory automatically (with safety checks against negative stock).
- Transfer entry is kg-first: user enters only kg, and bags are auto-derived.
- Transfer `From` location is restricted to locations already present in inventory.
- Transfer `To` is free text (existing or new); new location is created implicitly on successful transfer.
- Transfer item is restricted to inventory items and filtered by selected source location.
- Editing/removing Transfer entries recalculates linked inventory automatically (with safety checks against negative stock).
- Inventory, Work Entry, Transfer, and Finance all support Edit + Remove flows from both table actions and edit mode.
- Search filters are available above inventory/process/transfer/finance tables.
- Inventory table shows average kg per bag and status checks for unusual ratios.
- Bags used for cone calculation
- Sale, purchase, expense, salary records
- Role-based access (`admin`, `operator`)
- Admin user management with module-level permissions
- Easier dashboard with quick action buttons and cleaner data-entry layout
- Clean business theme designed for inventory/operations teams
- Very simple single-column entry forms for easier data input

## Login credentials
- Admin: `admin` / `admin123`
- Operator: `operator` / `operator123`

## Run
Open `index.html` in a browser.

## Notes
- Data is loaded from `seed-data.json` on first run, then stored in browser local storage.
- To reset, clear browser local storage for the site.
- Admin can create users and control access to each module (`inventory`, `process`, `transfer`, `finance`, `reports`, `admin`).
- Login is username/password based (demo quick-login buttons removed).
- Location names are free text in inventory/work entry, while transfer locations must be chosen from inventory locations.
- Keyboard flow: arrow keys select dropdowns and `Enter` moves to next field, then saves on the last field.
- Quantity fields show dynamic max limits from available stock in kg.
- Reports support date range filters and include income/expense/net summary chips.
- Admin can export/import full JSON backups from `Staff Access`.
- Login session is stored in `sessionStorage` for same-tab continuity.
- Admin has a `Clear Complete Data` option in `Staff Access` to wipe operational data and reset users to default safe accounts.

## Supabase Integration (already wired in app)
The app now reads/writes a single state record from Supabase table `ims_state`.

Run this SQL in Supabase SQL Editor:

```sql
create table if not exists public.ims_state (
	id text primary key,
	users jsonb not null default '[]'::jsonb,
	db jsonb not null default '{}'::jsonb,
	updated_at timestamptz not null default now()
);

alter table public.ims_state enable row level security;

drop policy if exists "ims_state_public_rw" on public.ims_state;
create policy "ims_state_public_rw"
on public.ims_state
for all
to anon
using (true)
with check (true);
```

Then insert initial row once:

```sql
insert into public.ims_state (id, users, db)
values ('main', '[]'::jsonb, '{}'::jsonb)
on conflict (id) do nothing;
```
