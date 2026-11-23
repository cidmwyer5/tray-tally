# Tray Tally site + menu server

This folder becomes a public GitHub repo named `tray-tally`. GitHub Pages serves:

| Path | What |
| --- | --- |
| `privacy-policy.html` | Privacy policy (App Store Connect → App Privacy → Privacy Policy URL) |
| `support.html` | Support page (App Store Connect → Support URL) |
| `terms.html` | Terms and community rules people agree to before turning on friends |
| `menus/YYYY-MM-DD.json` | Daily VT menus in the app's standard `[HallSpec]` format |

## Setup

1. Fill in every `[BRACKETED]` placeholder in the HTML files.
2. Create a public repo named `tray-tally` on GitHub and push this folder to it.
3. Repo **Settings → Pages**: Source "Deploy from a branch", branch `main`, folder `/ (root)`.
4. **Only after VT Dining says yes:** go to **Actions → Update menus → Run workflow** to check that it works.
   It then runs on its own three times a day.
5. In the app, set the URLs:
   - `ProfileView.swift` → `AppInfo.privacyPolicyURL`, `AppInfo.supportURL` and `AppInfo.termsURL`
   - `MenuData.swift` → the VT `School(...)`: add
     `feedURL: "https://YOUR-GITHUB-USERNAME.github.io/tray-tally/menus/{date}.json"`

   When `feedURL` is set, the app reads menus from your server and stops calling FoodPro directly.
   If VT's site changes, fix `scripts/fetch-menus.mjs` and push. No app update is needed.

## Notes

- **Before switching the app to this server:** `scripts/fetch-menus.mjs` only covers D2 and the older parsing
  rules. Port the current app logic from `FoodPro.swift` first: all ten VT locations and their station rules,
  combined meal names ("Lunch/Dinner", "Daily Items"), allergens, and splitting build-your-own sections
  into their own stations.

- FoodPro publishes one nutrition label per menu item, so a day at D2 takes 200+ requests. Labels are cached
  for 14 days in `data/labels-cache.json`, so after the first run only new recipes are fetched. That's one
  small job a few times a day instead of every student's phone hitting VT.
- If FoodPro is down, the previous menu file is kept and the run is marked failed in the Actions tab.
- To add a hall, add its FoodPro location number to `LOCATIONS` in the script, using the same hall name
  as the app's bundled menu so hours and icons match.
