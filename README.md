# Internal Traffic Exclusion Setup (GA4 + Microsoft Clarity)

How internal users are excluded from analytics using a query parameter, cookie, Google Tag Manager, Google Analytics 4, and Microsoft Clarity.


## Triggering Internal Mode

Visit:

`https://yourwebsite.com?internal_user=true`

This creates a first-party cookie:

`internal_user=1`

The cookie persists for 400 days. Why 400 days? Most Chromium based browsers set `max validity` to 400 days. I think only Brave has the shortest time period (180 days).
Mind you, Safari's Intelligent Tracking Prevention will delete the cookie after 7 days of no interaction. I have not found a way around it yet.


## Google Tag Manager (GTM)

### 1) Variable — `cookie_internal_user`
Purpose: read the `internal_user` cookie.

Steps:
1. GTM → **Variables** → **New**
2. Variable type: **1st Party Cookie**
3. Cookie name: `internal_user`
4. Name the variable: `cookie_internal_user`
5. Save

### 2) Trigger — `internal user trigger`
Purpose: fire tags only when the cookie exists.

Steps:
1. GTM → **Triggers** → **New**
2. Trigger type: **Page View**
3. This trigger fires on: **Some Page Views**
4. Condition:

   `{{cookie_internal_user}}` **equals** `1`

5. Name the trigger: `internal user trigger`
6. Save

### 3) Tag 1 — Cookie + Clarity Tag
Type: **Custom HTML**  This is where the custom script is pasted
Trigger: **All Pages**

Purpose:
- If URL contains `internal_user=true` → set cookie `internal_user=1`
- If cookie exists → set Clarity tag `internal_user=true`

### 4) Tag 2 — GA4 Internal Tag
Type: **Google Tag**  
Tag ID: `G-XXX`  Insert your Google Property's ID here
Trigger: **internal user trigger**

Configuration settings:
- Parameter: `traffic_type`
- Value: `internal`


## Google Analytics 4 (GA4)

### Activate the internal traffic filter
Path:

Admin → Data settings → Data filters → **Internal Traffic**

Condition:

traffic_type equals internal

Set state to:

Active. It will likely be in testing

Result: future internal traffic is excluded from GA4 reports.


## Microsoft Clarity

In dashboards: Filters → Customised tags

Set:
- Tag: `internal_user`
- Value: `true`
- Enable: **Exclude selection**

Result: future internal sessions excluded from recordings/heatmaps.


## Verification

### GA4
1. Visit the URL you choose
2. Open DevTools → Network → open a `collect` request after navigating to a second page.

Confirm:

`tt`=`internal`

### Clarity
Open a recording → **More details** → Customised tags.

Confirm:

`internal_user` = `true`


