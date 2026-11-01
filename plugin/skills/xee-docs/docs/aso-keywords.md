Xee's agent suggests a new App Store keywords field for each language of your listing, with the reason for each keyword. You approve it and paste it into your next App Store version. Xee never changes App Store Connect itself.

After the version goes on sale, the agent checks back, says whether the keywords helped, and remembers what it learned for the next suggestion.

## What the agent reads

| Source | What it gives | How to set it up |
| --- | --- | --- |
| Your App Store listing | The name, subtitle and keywords field for each language, from the version on sale | An App Store Connect key, picked in **App settings › iOS** under **Apple keys** |
| Xee's keyword data | Every term your app ranks for in each storefront, with popularity, difficulty and your app's rank, updated daily | Built in. Set the app's **Apple app ID** in **App settings › iOS** |
| Apple Ads search terms | What people typed before tapping your ads, with installs and spend | Optional. See [Apple Ads](/docs/apple-ads) |
| Your own ASO provider | Popularity and difficulty for any term, including ones your app doesn't rank for yet | Optional. Add an AppTweak API key in **App settings › ASO**. Each keyword the agent looks up uses your AppTweak credits |

Xee's keyword data only knows terms your app already ranks for. A new term with no numbers is unknown, not unpopular; add your own provider if you want numbers for those too.

## See your keywords

The **ASO** page in the sidebar shows what the agent reads:

- **Keywords**: every term your app ranks for in a storefront, most popular first, with popularity, difficulty and rank. Terms already in your keywords field are marked **In field**. Filter by popularity or search for a word.
- **Listing**: the name, subtitle and keywords field per language with Apple's character limits, as last read from App Store Connect. **Read again** reads it now.

## Ask for keywords

1. Open the **Agent** page and the **Keywords** tab. The agent must be on (**App settings › General**).
2. Pick a **Language**, or all of them, and press **Ask for keywords**.
3. A proposal appears in a few minutes. It shows the keywords field **Now** and **Proposed**, each with its character count, and every new, kept and removed keyword with the agent's reason. A reason names its data, such as "popularity 61, difficulty 38, rank 34" or "140 installs from ads".

Every proposed field follows Apple's rules and Xee's: at most 100 characters, separated by commas with no spaces, no keyword twice, and no word that is already in the app's name or subtitle (Apple already indexes those). The agent doesn't propose competitors' brand names.

## Approve and paste

1. Press **Approve**, then **Copy keywords**.
2. In App Store Connect, paste the field into **Keywords** for that language in your next app version, and submit it.
3. The proposal shows **Waiting for a live version**. Xee checks App Store Connect daily and marks it **Live** once a version with those keywords is on sale. If you already shipped it, press **Mark as applied**.

A proposal you don't want: write why (optional) and press **Skip**. An approved proposal that never goes live within 60 days is skipped with the reason "never applied".

## The check-in

The agent checks back after the number of days in the proposal, 14 by default. It compares the same number of days before and after the version went live: the ranks and popularity of the new keywords, and the installs from Apple Ads search terms. Then it recommends one of:

- **Keep**: the keywords helped or did no harm.
- **Roll back**: installs from the new keywords fell. It proposes the field to go back to.
- **Go further**: they helped and more terms are worth adding. It proposes the next field.

What it learned is saved under **App settings › Memory**, and the agent reads it before its next proposal.

## Use it from Claude

The same tools are available through the Xee MCP: `get_listing`, `get_ranked_keywords`, `get_keyword_metrics`, `get_search_terms` and `propose_keywords`. See [Use Xee in Claude](/docs/use-xee-in-claude). Claude can propose keywords; only you approve them, in the dashboard.
