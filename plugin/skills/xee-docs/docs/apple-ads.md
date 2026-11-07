Connect Apple Ads and Xee's agent reads your campaigns, judges them on what your installs earn in the app, and proposes changes. You decide: nothing changes in Apple Ads until you tick changes on To decide and apply them.

Apple reports installs and spend. Xee also sees the subscriptions those installs turn into, so it can tell a keyword that brings trials from one that only brings installs.

## Connect Apple Ads

You invite Xee's API user into your Apple Ads account, and Xee finishes the connection. You don't make keys or copy IDs. You make a connection once in Settings, then pick it for each app in App settings, the way you pick an Apple key.

1. In Apple Ads, an account administrator opens **Account Settings › User Management** and invites a user with these details:

   | Field | Value |
   | --- | --- |
   | First Name | Xee |
   | Last Name | API |
   | Email Address | ads@xee.ai |
   | Role | **API Account Manager**, so the agent's changes can be applied. **API Account Read Only** if Xee should only read. |

   Pick one of the two API roles. Account Admin, Account Read Only and Limited Access don't work for Xee.
2. In Xee, open **Settings › Apple Ads** and press **Add connection**. Enter your **Apple Ads account ID** and press **Add connection**. To find the ID, sign in at ads.apple.com, open the account you invited Xee to and click your name at the top right: the menu shows each account's ID under its name. It's only numbers. The connection shows **Pending verification**.
3. Xee accepts the invite and sets up the API key in your account, usually within a working day. The connection then shows **Verified**.
4. In **App settings › Apple Ads**, pick the connection for the app and press **Check connection**. It shows the ad account, what the agent may do there (read and write, or read only) and when it was last checked.
5. Make sure the app's Apple app ID is set in App settings, so Xee can find the campaigns that promote it.

A connection reaches only the account ID you entered. Xee is invited into other accounts too, but your apps never see them, and other customers never see yours.

| Status | What it means |
| --- | --- |
| Pending verification | Your connection is added. Xee hasn't finished it yet. |
| Verified | Xee finished it. Pick it for an app in App settings. |
| Not working | Apple refused it. The reason is shown next to the status. Check that ads@xee.ai is still invited with an API role. |

To disconnect, delete the connection in Xee and remove ads@xee.ai from **User Management** in Apple Ads.

### Targets and campaign roles

In **App settings › Apple Ads**, set the two numbers the agent judges by:

- **Target CPA (USD)**: the most you want to pay for one paying subscriber.
- **Target ROAS D7 (%)**: the revenue from a subscriber's first 7 days, as a share of what their install cost.

Without targets the agent changes only clear outliers and says that targets would help.

On the **Agent** page, the **Apple Ads** tab lists the campaigns that promote the app under **Campaign roles**. Each has a role: Discovery, Exact, Brand or Competitor. The agent moves search terms that earn from a Discovery campaign into an Exact one, and judges Brand and Competitor campaigns apart. **Automatic** reads the role from the campaign's name, so a campaign called "US - Exact" counts as Exact. Pick a role only where the name doesn't say.

## How installs are matched

Your app's Xee SDK sends Apple's attribution token once per user. Xee asks Apple's AdServices API which campaign, ad group and keyword the install came from, within about half an hour, and keeps the answer on the profile. It shows on the profile page as Attribution. An install with no ad behind it is **Organic**.

Apple's spend and installs arrive once a day, a day behind. The first sync reads the last 90 days. Revenue from your own purchases is joined when you look, so a refund or a renewal shows up without waiting.

## Read the numbers

The **Apple Ads** page in the sidebar has three tabs. **Campaigns** shows each campaign live from Apple, with its daily budget, role, ad groups and keyword bids. **Changes** lists every change the agent applied and how it went. **Performance** shows each campaign, ad group, keyword or search term with its spend, installs, trials, paid subscribers, CPA, ROAS D7 and revenue. A few things to know:

- **CPA** is spend divided by paid subscribers. **ROAS D7** is the revenue in each install's first 7 days divided by spend.
- Installs younger than 7 days have no ROAS D7 yet. The row says **too recent**.
- Apple doesn't say which search term an install came from, so at the search-term level trials, subscribers and revenue are blank.
- Spend in a currency Xee has no rate for counts as $0.

## What the agent proposes

The agent looks once a week, and you can press **Ask for changes** on the Apple Ads page to get a proposal now. It acts only on a keyword, search term or campaign with at least $20 spend or 3 subscribers in the period. It can propose to:

- raise or lower a keyword's bid, or pause a keyword that spent $20 with no subscribers;
- add a search term that earns in a Discovery campaign as an exact keyword in the Exact campaign, and as an exact negative in the Discovery ad group;
- add a search term that spent $20 with no subscribers as a negative keyword;
- raise the budget of a campaign that spends all of it at a CPA under target;
- pause or resume a keyword, ad group or campaign.

Xee never creates or deletes campaigns or ad groups. Do that in Apple Ads.

A proposal has up to 20 changes. Each shows the agent's reason with the numbers behind it.

## Apply a proposal

On the **Agent** page, open the **Apple Ads** tab. Every change is ticked. Untick any you don't want, then press **Apply selected**.

Xee checks each change before it writes, one by one:

- **Applied**: it matched Apple's current value and was within the limits.
- **Skipped**: the bid, budget or keyword changed in Apple Ads since the agent looked, so Xee left it alone.
- **Refused**: it was outside the limits below, or Apple said no. The reason is shown.
- **Failed**: Apple couldn't be reached.

The limits hold whatever the agent proposed:

| Change | Limit |
| --- | --- |
| Bid | Moves at most 30% per change and stays between $0.10 and $50.00 |
| Daily budget | Rises at most 50% per change and stays at $1.00 or more |
| Per proposal | At most 50 changes |
| Per target | No new change on a keyword, ad group or campaign that has one waiting or applied in the last 3 days |

A connection that can only read shows "This connection can only read. Reconnect with an API Account Manager user." when you press **Apply selected**.

After you apply, the agent checks back after the number of days in the proposal (7 by default). It compares the days before and after for each change and keeps it, rolls it back or goes further with a new proposal. What it learns is saved under **Settings › Memory**.

## Use it from Claude

The same tools are available through the Xee MCP: `get_ads_overview`, `get_ads_performance`, `get_ads_targets`, `get_ads_proposal` and `propose_ads_changes`. See [Use Xee in Claude](/docs/use-xee-in-claude). Claude can propose changes; only you apply them, in the dashboard.
