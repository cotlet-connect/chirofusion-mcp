# ChiroFusion API integration and MCP server: connect ChiroFusion to ChatGPT, Claude and any AI agent

**ChiroFusion API integration and MCP server by Cotlet.dev: yes, Claude, ChatGPT and other AI agents can work in your ChiroFusion account.** ChiroFusion has no official MCP server and no ChatGPT or Claude app, and it has no public API (checked 26 Nov 2026). Cotlet.dev connects your own signed-in ChiroFusion account to any AI agent, so you ask in plain words and the AI helps you post EOBs, chase claims, send statements and rebook no-shows.

## What you can ask

> Match this EOB to the open claims and list what to post on each. Post after my OK.

> List outstanding claims over 30 days by payer with the last status, oldest first.

> List patients with a balance older than 60 days and draft a statement message for each. Send nothing yet.

> Who no-showed this week and has no next visit? Draft a rebooking text for each.

> Put this month's visits and collections by provider in a sheet for my accountant.

## What chiropractic clinic owners and billers ask their AI to do in ChiroFusion

From chiropractic clinic owners and billers' posts on Reddit, forums, review sites and the ChiroFusion help center (checked 26 Nov 2026).

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Post EOBs and insurance checks | "Match this EOB to the open claims and list what to post on each. Post after my OK." | When you ask |
| Outstanding claims | "List outstanding claims over 30 days by payer with the last status, oldest first." | Weekly |
| Patient statements and balances | "List patients with a balance older than 60 days and draft a statement message for each. Send nothing yet." | Monthly |
| No-shows to rebook | "Who no-showed this week and has no next visit? Draft a rebooking text for each." | Weekly |
| Visits and collections in a sheet | "Put this month's visits and collections by provider in a sheet for my accountant." | Your own endpoint, called from a sheet or n8n |
| Bookings from website or phone AI | "Book this caller into the first free slot on Tuesday with Dr Smith and confirm the time." | Your own endpoint, called from a sheet or n8n |
| Intake forms not done | "Which patients booked for tomorrow have not finished the online intake? Draft a reminder for each." | Daily |

Your AI asks you before it sends, books, replies or changes anything. Payments stay in your hands.

## ChiroFusion API

ChiroFusion has no public API or developer docs; its integrations are built by ChiroFusion with partners (checked 26 Nov 2026). With Cotlet.dev you build your own ChiroFusion API: show your AI agent a job once in your signed-in ChiroFusion account, save it as an endpoint and call it from n8n, Make, Zapier or your own code. How it works: https://cotlet.dev/integrations/api/chirofusion

Endpoints you can build in a few minutes (none of them exists until you build it):

- `book-appointment`: Book a patient into a free slot and return the time
- `outstanding-claims`: Claims outstanding over N days by payer
- `visits-and-collections`: Visits and collections by provider for a month
- `patient-balances`: Patients with a balance older than N days
- `no-shows-this-week`: Patients who no-showed and have no next visit
- `intake-pending`: Patients booked tomorrow who have not finished intake

Call one with your Cotlet API key: `POST https://cotlet.dev/api/v1/custom/book-appointment`. One call runs up to 90 seconds, writes wait for your OK, payments are blocked.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its ChiroFusion tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"chirofusion":{"command":"uvx","args":["chirofusion-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t chirofusion-mcp .
docker run -i --rm -e COTLET_API_KEY chirofusion-mcp
```

Tools: `chirofusion_guide`, `chirofusion_status`, `chirofusion_open`, `chirofusion_read`, `chirofusion_click`, `chirofusion_type`, `chirofusion_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install chirofusion-mcp        # or run it once: uvx chirofusion-mcp connect
chirofusion-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 chirofusion_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 chirofusion_agent.py status    # is ChiroFusion ready in your Cotlet.dev account
python3 chirofusion_agent.py guide     # ChiroFusion screens and sign-in link
python3 chirofusion_agent.py read      # open ChiroFusion, read the page, close the browser
python3 chirofusion_agent.py tools     # list Cotlet.dev tools
python3 chirofusion_agent.py serve     # start the stdio MCP server (same as no argument)
```

## What you can ask your AI to do in ChiroFusion

These are real jobs ChiroFusion users asked about. Cotlet lets ChatGPT, Claude or any MCP client do them in your own signed-in ChiroFusion account.

### Insurance and claims

#### Can ChatGPT or Claude match an EOB to my open claims and post the insurance checks in ChiroFusion?

Yes. ChiroFusion has EOB Posting & ERAs under Billing, which you use by hand. With Cotlet, ChatGPT or Claude reads the EOB, matches it to your open claims in your signed-in account and prepares the posting on each one. Nothing is posted until you say yes.

Ask your agent: "Match this EOB to the open claims in ChiroFusion and list what to post on each. Post after my OK."

#### Can ChatGPT or Claude list my outstanding claims over 30 days in ChiroFusion by payer?

Yes. With Cotlet, your agent reads Billing & Claims Management in your signed-in ChiroFusion account and lists claims outstanding over the days you choose, by payer, with the last status and the oldest first.

Ask your agent: "List outstanding claims over 30 days in ChiroFusion by payer with the last status, oldest first."

As an API endpoint: Yes. Save the claims check as an endpoint in Cotlet and call it every Monday from n8n, Make or Zapier to get the list by email.

### Patients and rebooking

#### Can ChatGPT or Claude find patients with an old balance in ChiroFusion and draft a statement message for each?

Yes. ChiroFusion works with MailMyStatements for statements, which you run by hand. With Cotlet, your agent lists patients with a balance older than the days you choose and drafts a statement message for each. Nothing is sent until you say yes.

Ask your agent: "List ChiroFusion patients with a balance older than 60 days and draft a statement message for each. Send nothing yet."

As an API endpoint: Yes. Save the balance list as an endpoint in Cotlet and call it monthly from n8n, Make or Zapier.

#### Can ChatGPT or Claude find this week's no-shows in ChiroFusion who have no next visit and draft a rebooking text?

Yes. ChiroFusion sends text reminders on its own. With Cotlet, your agent reads the scheduler in your signed-in account, finds patients who no-showed this week with no next visit and drafts a rebooking text for each. Cotlet does not send texts itself, and nothing goes out until you say yes.

Ask your agent: "Who no-showed this week in ChiroFusion and has no next visit? Draft a rebooking text for each."

As an API endpoint: Yes. Save the no-show list as an endpoint in Cotlet and call it every Friday from n8n, Make or Zapier.

#### Can ChatGPT or Claude tell me which patients booked for tomorrow have not finished their ChiroFusion online intake?

Yes. With Cotlet, your agent reads tomorrow's scheduler and the online intake forms in your signed-in ChiroFusion account, lists patients who have not finished and drafts a reminder for each. Nothing is sent until you say yes.

Ask your agent: "Which patients booked for tomorrow in ChiroFusion have not finished the online intake? Draft a reminder for each."

As an API endpoint: Yes. Save the unfinished-intake list as an endpoint in Cotlet and call it every afternoon from n8n, Make or Zapier.

#### Can ChatGPT or Claude book patients into ChiroFusion from my website form or a phone AI?

Yes. ChiroFusion has no public API. With Cotlet, a form or phone assistant calls your own endpoint and your agent books the visit on the scheduler in your signed-in ChiroFusion account and returns the time. Nothing is booked in chat until you say yes.

Ask your agent: "Book this caller into the first free slot on Tuesday with Dr Smith in ChiroFusion and confirm the time."

As an API endpoint: Yes. Save the booking step as an endpoint in Cotlet and call it from your website form, a phone AI, n8n, Make or Zapier whenever a request arrives.

### Reports for your accountant

#### Can ChatGPT or Claude put this month's ChiroFusion visits and collections by provider in a sheet for my accountant?

Yes. ChiroFusion reports export to Excel or PDF, which you run by hand. With Cotlet, your agent runs the reports in your signed-in account and puts visits and collections by provider in a Google Sheet. Nothing is added until you say yes.

Ask your agent: "Put this month's ChiroFusion visits and collections by provider in a sheet for my accountant."

As an API endpoint: Yes. Save the monthly report as an endpoint in Cotlet and call it on the first of each month from n8n, Make or Zapier to fill the sheet.

## Questions from ChiroFusion users

**Does ChiroFusion have an API?**

ChiroFusion has no public API or developer docs; its integrations are built by ChiroFusion with partners (checked 26 Nov 2026). With Cotlet.dev you build your own ChiroFusion API on your signed-in account and call it from n8n, Make or your own code: https://cotlet.dev/integrations/api/chirofusion

**How do I connect ChiroFusion to ChatGPT?**

Add Cotlet.dev as an app in ChatGPT (Settings → Apps, connector URL `https://cotlet.dev/mcp/v1`), sign in to ChiroFusion once in your Cotlet.dev Cloud Browser, and ask ChatGPT in plain words. No code and no ChiroFusion API key.

**Is there a way to use Claude with ChiroFusion?**

Yes. In Claude Code run the one-line `claude mcp add` command above; in the Claude app add `https://cotlet.dev/mcp/v1` as a custom connector. Claude then works in your own ChiroFusion account.

**Is there a ChiroFusion MCP server?**

ChiroFusion has no official MCP server for your account. Cotlet.dev is a hosted MCP server that works in your own ChiroFusion account for you, with Claude, ChatGPT, Cursor, Codex, n8n, Make and Zapier.

**Can AI help with EOB posting in ChiroFusion?**

Yes. It reads the EOB, matches it to your open claims and prepares the posting on each one. It posts only after your OK.

**Does ChiroFusion have an API I can use for my clinic?**

ChiroFusion has no public API (checked 26 Nov 2026). With Cotlet.dev you build your own endpoint, and your AI does the step in your ChiroFusion account in the browser.

## Find it on

- [ChiroFusion on Cotlet.dev](https://cotlet.dev/integrations/mcp/chirofusion?utm_source=github&utm_medium=readme&utm_campaign=chirofusion&utm_content=find)
- [PyPI package chirofusion-mcp](https://pypi.org/project/chirofusion-mcp/)
- [Odel](https://odel.app/modules/cotlet/chirofusion)
- [mcpbeat](https://mcpbeat.com/mcp-servers/cotlet/chirofusion/)

## Also works with

- [Mangomint MCP](https://github.com/cotlet-connect/mangomint-mcp)
- [Wanderlog MCP](https://github.com/cotlet-connect/wanderlog-mcp)
- [SmugMug MCP](https://github.com/cotlet-connect/smugmug-mcp)
- [Garmin Connect MCP](https://github.com/cotlet-connect/garmin-mcp)
- [Housecall Pro MCP](https://github.com/cotlet-connect/housecall-pro-mcp)
- [Medium MCP](https://github.com/cotlet-connect/medium-mcp)
- [Crexi MCP](https://github.com/cotlet-connect/crexi-mcp)
- [DistroKid MCP](https://github.com/cotlet-connect/distrokid-mcp)
- [LinkedIn MCP](https://github.com/cotlet-connect/linkedin-mcp)
- [EstimateOne MCP](https://github.com/cotlet-connect/estimateone-mcp)
- [Google Search Console MCP](https://github.com/cotlet-connect/google-search-console-mcp)
- [Lichess MCP](https://github.com/cotlet-connect/lichess-mcp)
- [Strava MCP](https://github.com/cotlet-connect/strava-mcp)
- [StreamElements MCP](https://github.com/cotlet-connect/streamelements-mcp)
- [Meetup MCP](https://github.com/cotlet-connect/meetup-mcp)
- [AccuRanker MCP](https://github.com/cotlet-connect/accuranker-mcp)
- [GetYourGuide MCP](https://github.com/cotlet-connect/getyourguide-mcp)
- [Idealista MCP](https://github.com/cotlet-connect/idealista-mcp)
- [Riverside MCP](https://github.com/cotlet-connect/riverside-mcp)
- [Udemy MCP](https://github.com/cotlet-connect/udemy-mcp)
- [Faire MCP](https://github.com/cotlet-connect/faire-mcp)
- [Teachable MCP](https://github.com/cotlet-connect/teachable-mcp)
- [Vagaro MCP](https://github.com/cotlet-connect/vagaro-mcp)
- [Buildertrend MCP](https://github.com/cotlet-connect/buildertrend-mcp)
- [Zoopla MCP](https://github.com/cotlet-connect/zoopla-mcp)
- [Dribbble MCP](https://github.com/cotlet-connect/dribbble-mcp)
- [Hostelworld MCP](https://github.com/cotlet-connect/hostelworld-mcp)
- [Mashvisor MCP](https://github.com/cotlet-connect/mashvisor-mcp)
- [Rent Manager MCP](https://github.com/cotlet-connect/rent-manager-mcp)
- [ThriveCart MCP](https://github.com/cotlet-connect/thrivecart-mcp)
- [Fresha MCP](https://github.com/cotlet-connect/fresha-mcp)
- [Booksy MCP](https://github.com/cotlet-connect/booksy-mcp)
- [BookingKoala MCP](https://github.com/cotlet-connect/bookingkoala-mcp)
- [Milanote MCP](https://github.com/cotlet-connect/milanote-mcp)
- [Partiful MCP](https://github.com/cotlet-connect/partiful-mcp)
- [Contractor Foreman MCP](https://github.com/cotlet-connect/contractor-foreman-mcp)
- [Pixieset MCP](https://github.com/cotlet-connect/pixieset-mcp)
- [GlossGenius MCP](https://github.com/cotlet-connect/glossgenius-mcp)
- [ZenMaid MCP](https://github.com/cotlet-connect/zenmaid-mcp)
- [AroFlo MCP](https://github.com/cotlet-connect/aroflo-mcp)
- [Simpro MCP](https://github.com/cotlet-connect/simpro-mcp)
- [Workiz MCP](https://github.com/cotlet-connect/workiz-mcp)
- [Kickserv MCP](https://github.com/cotlet-connect/kickserv-mcp)
- [Service Autopilot MCP](https://github.com/cotlet-connect/service-autopilot-mcp)
- [Mindbody MCP](https://github.com/cotlet-connect/mindbody-mcp)
- [DaySmart MCP](https://github.com/cotlet-connect/daysmart-mcp)
- [Acuity Scheduling MCP](https://github.com/cotlet-connect/acuity-scheduling-mcp)
- [Jane App MCP](https://github.com/cotlet-connect/jane-app-mcp)
- [FieldEdge MCP](https://github.com/cotlet-connect/fieldedge-mcp)
- [Phorest MCP](https://github.com/cotlet-connect/phorest-mcp)
- [DINGG MCP](https://github.com/cotlet-connect/dingg-mcp)
- [Invoice Simple MCP](https://github.com/cotlet-connect/invoice-simple-mcp)
- [PhotoShelter MCP](https://github.com/cotlet-connect/photoshelter-mcp)
- [RazorSync MCP](https://github.com/cotlet-connect/razorsync-mcp)
- [ShootProof MCP](https://github.com/cotlet-connect/shootproof-mcp)
- [Invoice2go MCP](https://github.com/cotlet-connect/invoice2go-mcp)
- [Appointy MCP](https://github.com/cotlet-connect/appointy-mcp)
- [Contractor+ MCP](https://github.com/cotlet-connect/contractor-plus-mcp)
- [Petpooja MCP](https://github.com/cotlet-connect/petpooja-mcp)
- [Teachers Pay Teachers MCP](https://github.com/cotlet-connect/teachers-pay-teachers-mcp)
- [Bloom MCP](https://github.com/cotlet-connect/bloom-mcp)
- [hipages MCP](https://github.com/cotlet-connect/hipages-mcp)
- [ServiceSeeking MCP](https://github.com/cotlet-connect/serviceseeking-mcp)
- [MyJobQuote MCP](https://github.com/cotlet-connect/myjobquote-mcp)
- [MyBuilder MCP](https://github.com/cotlet-connect/mybuilder-mcp)
- [Yelp for Business MCP](https://github.com/cotlet-connect/yelp-for-business-mcp)
- [Rosie MCP](https://github.com/cotlet-connect/rosie-mcp)
- [Roofr MCP](https://github.com/cotlet-connect/roofr-mcp)
- [FieldPulse MCP](https://github.com/cotlet-connect/fieldpulse-mcp)
- [YourTradebase MCP](https://github.com/cotlet-connect/yourtradebase-mcp)
- [JobNimbus MCP](https://github.com/cotlet-connect/jobnimbus-mcp)
- [Ascora MCP](https://github.com/cotlet-connect/ascora-mcp)
- [eLocal MCP](https://github.com/cotlet-connect/elocal-mcp)
- [Alto MCP](https://github.com/cotlet-connect/alto-mcp)
- [Rightmove Plus MCP](https://github.com/cotlet-connect/rightmove-plus-mcp)
- [TurboTenant MCP](https://github.com/cotlet-connect/turbotenant-mcp)
- [Innago MCP](https://github.com/cotlet-connect/innago-mcp)
- [Property Finder MCP](https://github.com/cotlet-connect/property-finder-mcp)
- [LoopNet MCP](https://github.com/cotlet-connect/loopnet-mcp)
- [Dotloop MCP](https://github.com/cotlet-connect/dotloop-mcp)
- [SkySlope MCP](https://github.com/cotlet-connect/skyslope-mcp)
- [ShowingTime MCP](https://github.com/cotlet-connect/showingtime-mcp)
- [connectMLS MCP](https://github.com/cotlet-connect/connectmls-mcp)
- [WebPT MCP](https://github.com/cotlet-connect/webpt-mcp)
- [Tebra MCP](https://github.com/cotlet-connect/tebra-mcp)
- [ChiroTouch MCP](https://github.com/cotlet-connect/chirotouch-mcp)
- [Semble MCP](https://github.com/cotlet-connect/semble-mcp)
- [Nookal MCP](https://github.com/cotlet-connect/nookal-mcp)
- [Cliniko MCP](https://github.com/cotlet-connect/cliniko-mcp)
- [Gentu MCP](https://github.com/cotlet-connect/gentu-mcp)
- [splose MCP](https://github.com/cotlet-connect/splose-mcp)
- [Physitrack MCP](https://github.com/cotlet-connect/physitrack-mcp)
- [All apps connected through Cotlet.dev](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/mcp/chirofusion?utm_source=github&utm_medium=readme&utm_campaign=chirofusion

Start free: https://cotlet.dev/signup?app=chirofusion&utm_source=github&utm_medium=readme&utm_campaign=chirofusion
