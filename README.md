# Beam Pharmacy v3 — Two-branch team tasks

Includes a responsive task and medicine-order dashboard with **two separate branches**, role-based Admin/Staff access, staff-submitted tasks assigned to Admin, shared medicine requests across branches, daily/weekly/monthly recurrence, Thai speech recognition (browser dependent), snooze 5/10/30 minutes, and **red in-app alerts when opening the app**. **NO Push Notifications.**

## Quick preview (no server)
Upload `index.html`, `app.js`, `config.js`, `logo.png`, `manifest.webmanifest`, `sw.js` to existing `beamharit/Beam-Pharmacy` GitHub repository root and commit. If `config.js` is left empty, site starts in **demo mode**, which saves work only in this browser. There is **no actual login, staff accounts, cross-device sync or shared multi-user data in demo mode**. Do not use demo mode for live shared operation.

## Enable real login and cross-device synchronization (Supabase)

1. Create a Supabase project (free tier availability and limits can change). Keep the project's database password and service-role key **private**.
2. In Supabase SQL Editor run `supabase/schema.sql` on a **new project**. This creates the two branches (rename in Admin settings), secure tables and Row Level Security (RLS) policies.
3. In Authentication > Users, **manually create your Admin user** with email and password (mark email confirmed). Copy that user's UUID. Then run the following SQL with the exact UUID and email. **Replace the placeholders before running**:

```sql
insert into public.profiles(id,name,email,role,branch_id,active)
values ('ADMIN_USER_UUID','พี่บีม','ADMIN_EMAIL','admin',null,true);
```

4. In Supabase Dashboard, under Project Settings > API / Data API, find **Project URL** and **anon / publishable public key**. Set values in `config.js` and upload it. Do **not** use the `service_role` or any secret key here. Public anon key can be shipped in the browser because RLS is enforced in the database.
5. Deploy the Edge Function `supabase/functions/create-staff/index.ts` with name `create-staff` using Supabase CLI, e.g. `supabase functions deploy create-staff`. Leave JWT verification **enabled**. The function reads `SUPABASE_URL`, `SUPABASE_ANON_KEY`, and `SUPABASE_SERVICE_ROLE_KEY` from the function environment. Supabase-managed environment typically supplies these values; verify in your project. Never put the service-role key in GitHub Pages files.
6. In Supabase Authentication settings, disable public email signup if you want **Admin-only account creation**, and configure appropriate password policies and account recovery procedures.
7. Log into the live GitHub Pages site as Admin, visit the **พนักงาน** page to create staff accounts and assign a branch. Staff can log in from any device and see task/order records across two branches; they cannot mark medicine orders “สั่งแล้ว”.
8. To receive live updates without re-opening the page, ensure **Realtime** is enabled for the tables. If not, records still reload when reopening/refocusing the app. Refresh the GitHub Pages page after deployment and clear old cached site data if needed.

## Permissions / behavior

- Admin sees all tasks, two-branch med lists and staff; can mark an order `ordered`, close/deactivate staff profiles, rename branches, and delete historical records.
- Staff sees the shared order list from **both** branches, can create med requests and append/edit quantities/notes. Staff can add tasks for Admin or other staff. Database policies restrict unauthorized status updates.
- Red in-app badge is **not a mobile push notification**. It counts pending medicine orders for Admin plus overdue tasks. For Staff it counts their overdue tasks. The count is refreshed on launch, focus and via online Realtime. Snooze changes when the task becomes overdue again; it does not schedule background reminders.
- Repeating tasks advance to their next due date **when marked completed**. Previous completions of recurring tasks are not retained as separate history rows in this version. Monthly recurrence at month-end is approximate when a short month intervenes.
- Thai speech command parsing is rule based, not a full language model; inspect due/repeat options before saving. Microphone access requires browser support and HTTPS.
- Duplicate medicine requests are warned within the same branch if the same name is pending; this is a client-side check, not a fully atomic duplicate lock under concurrent submission.
- **Do not store patient names, personal health records or other sensitive personal information.** This tool is for store operations only.

## Important

GitHub Pages hosts only static site files. Without configured Supabase and deployed Edge Function, shared accounts and real-time team usage do not exist. The database service may have quota/billing considerations; review vendor policies before operational use. Test access with separate Admin and Staff accounts before relying on this at the pharmacy.
