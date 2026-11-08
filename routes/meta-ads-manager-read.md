# Route: Meta Ads Manager, read only (November 2026)

## URLs (date is in the URL, no clicking needed)
- Campaigns, yesterday:
  `https://adsmanager.facebook.com/adsmanager/manage/campaigns?act=<AD_ACCOUNT_ID>&business_id=<BUSINESS_ID>&date=YYYY-MM-DD_YYYY-MM-DD+1%2Cyesterday`
- Today: `...&date=YYYY-MM-DD_YYYY-MM-DD+1%2Ctoday`
- Ad level: `/adsmanager/manage/ads?...` (same parameters); ad set level: `/adsmanager/manage/adsets?...`
- Billing: `https://business.facebook.com/billing_hub/accounts/details?asset_id=<AD_ACCOUNT_ID>&business_id=<BUSINESS_ID>`
  -> `find "See all transactions"` for the transaction list.

## Reading
- navigate -> wait 10 + 6 -> `get_page_text`. Table rows come back as text: name, status, results, budget, spent.
- Reading `[role=row]` with JS returned nothing; use `get_page_text`.
- The ad-level list is virtualized: `get_page_text` only returns the first ~5 ads. Narrow it with a campaign/ad set filter if needed.

## Traps
- Without `business_id` the first load hung on "Loading your ad account" for over a minute; with `business_id` it loaded.
- The top bar has "Discard drafts" and "Review and publish (N)": click neither while only reading.
- After saving a dialog, `navigate` may show "Leave site?"; opening a new tab is easier.
