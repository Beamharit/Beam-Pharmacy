# BEAM PHARMACY v3.1 — voice, registration approval, two branches

## What changed
- Home Screen icon is designed as a square **green cross + navy BEAM**, without PHARMACY; includes 180/192/512 px PNG icons. Full original logo stays in the page header.
- Global 🎙️ button uses browser speech recognition and rule-based Thai parsing to distinguish tasks from medicine-low-stock reports. Always review and correct information before saving. It is not an AI language model and may not recognize every phrase/browser.
- Medicine `quantity` field now means **remaining stock reported by staff**, NOT quantity to order. Admin enters **ordered_quantity** separately before marking ordered. Existing v3 free-text quantities remain unchanged; review older data manually as their original meaning may differ.
- Open email signup, but each new user is **pending and inactive** until Admin approves. Pending accounts cannot access store data under database RLS. Admin sees approval requests (in-app red badge), approves/rejects. Staff can work across both branches without fixed assignment.
- No Push Notifications. Red in-app alerts only when app is open, launched or refocused.

## IMPORTANT: Update your existing Supabase project BEFORE uploading the new website
1. Supabase Dashboard → SQL Editor → New Query. Open `supabase/migration_v3_1.sql`, paste ALL content, then Run. **Do not run `schema.sql` again** and do not delete existing tables/data.
2. Supabase → Authentication → Providers → Email. **Enable email signup**. Configure email verification policy and email templates/redirect URLs for production. Public signup is allowed but store access is forbidden until approval. Supabase email delivery has rate limits; configure SMTP for real usage.
3. Open `config.js`. It already includes the public project URL and publishable key entered during setup. If your project has changed, update with the new public values. Never include `service_role`/secret key in public files.
4. Back up GitHub `main` branch and upload ALL ROOT files to `beamharit/Beam-Pharmacy`. Keep `supabase` folder as documentation/migration. GitHub Pages is static; no server needed for the new approval workflow since it uses a database trigger and existing admin RLS policies.
5. Log in as Admin; test **new signup** with a separate email account. Check Staff → Pending requests → Approve. Then sign in as staff from a second browser/device, select branch 1/2 and submit medicine with remaining count. Confirm Admin sees in-app alerts and chooses order quantity.
6. On iPhone, remove old Home Screen shortcut then add the updated site again if the old icon sticks. Safari's icon can remain cached after web updates.

## Verification / constraints
- Existing v3 accounts remain intact; new account creation via email requires Supabase Auth signup and (depending on settings) email confirmation.
- Approved staff accounts have access to both branches. A new signup is always Staff, inactive and pending; never Admin. Admin can suspend an approved account.
- UI parsing for e.g. `เตือนพี่บีม พรุ่งนี้ 9 โมง โทรหาบริษัทยา` or `แจ้งยาขาด สาขา 2 พาราเซตามอล 500 มิลลิกรัม เหลือ 10 กล่อง` is heuristic. The confirmation form is essential.
- Previously deployed `create-staff` Edge Function is no longer required for new employee signups.
- Keep all patient-identifiable information OUT of this app.
- Static syntax/asset checks are not a substitute for real Supabase end-to-end testing. Test on two separate devices before relying on the app operationally.
