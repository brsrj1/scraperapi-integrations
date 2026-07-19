# ScraperAPI Integration Complete Guide: How to Connect ScraperAPI with Python, Scrapy, LangChain, n8n, and Zapier — Which Plan Is Right for You, Is It Worth the Cost, and How to Get Started Without Getting Burned (Full Plan Breakdown + Credit Multiplier Explained)

Web scraping sounds simple until it isn't. You write a quick `requests.get()`, feel pretty good about yourself, and then three hours later you're staring at a wall of CAPTCHAs, 403 errors, and IP bans. That's usually the moment people start googling "scraperapi integration."

And look — that's exactly what ScraperAPI was built for. You hand it a URL, it hands you back the HTML. No proxy rotation, no CAPTCHA solving, no headless browser drama. Just data.

But before you commit to a plan, there are a few things you really need to understand about how ScraperAPI actually works, how its credit system charges you, and which integration method fits your specific tech stack. That's what this guide covers.

---

**What Is ScraperAPI and Why Do Developers Use It?**

ScraperAPI is a web scraping API that sits between your code and whatever website you're trying to scrape. Instead of calling `example.com` directly, you call ScraperAPI's endpoint with your target URL as a parameter — and ScraperAPI handles everything behind the scenes: rotating proxies across 40 million+ IPs in 50+ countries, solving CAPTCHAs, rendering JavaScript when necessary, and automatically retrying failed requests.

The company has been around since 2018, bootstrapped its way to $3M in revenue and 10,000+ customers, and was later acquired by SaaS.group. They now process over 36 billion API requests per month and count brands like Deloitte, Sony, and Alibaba among their users.

The pitch is simple: **one API call replaces an entire proxy + browser infrastructure.** For teams that don't want to maintain that infrastructure themselves, it's a pretty compelling value proposition.

👉 [Try ScraperAPI free — 1,000 credits/month, no credit card required](https://www.scraperapi.com/?fp_ref=coupons)

---

**The Credit System: The Most Important Thing Most Guides Skip**

Before you touch a single line of code, you need to understand ScraperAPI's credit multiplier system — because ignoring it is how people end up burning through a $299/month plan in three days.

Here's the base idea: each API request costs credits, and every plan comes with a monthly credit pool. The Hobby plan ($49/month) includes 100,000 credits. Sounds like a lot. But the actual number of requests you get depends entirely on what parameters you use and which sites you're targeting.

**Credit costs by request type:**

| Request Type | Credits Per Request |
|---|---|
| Standard HTML page | 1 |
| JavaScript rendering (`render=true`) | 10 |
| Premium proxy (`premium=true`) | 10 |
| Screenshot (`screenshot=true`) | 10 |
| Premium + render combined | 25 |
| Ultra-premium proxy (`ultra_premium=true`) | 30 |
| Ultra-premium + render combined | 75 |
| Anti-bot bypass (Cloudflare, DataDome, etc.) | 10 (auto-applied) |

**Domain-specific credit costs (applied automatically):**

| Target Domain | Credits Per Request |
|---|---|
| Standard website | 1 |
| Amazon | 5 |
| Google / Bing SERP | 25 |
| LinkedIn | 30 |

That last column is key. You don't choose whether to pay the Amazon multiplier — it's applied automatically the moment ScraperAPI detects the domain. Same goes for anti-bot bypass credits: if the target site uses Cloudflare or DataDome, those +10 credits get charged without you explicitly requesting it.

The practical math: on a Hobby plan with 100,000 credits, you get 100,000 standard requests, 10,000 JS-rendered requests, or 1,333 ultra-premium + JS-rendered requests. That's a 75x swing in actual capacity from the same plan.

> "You sign up, see '100,000 credits,' and start scraping. Three days later your dashboard shows 80% is gone and you've scraped maybe 6,000 pages. What happened is the credit multiplier system."

One other thing worth noting: **credits do not roll over.** Unused credits expire at the end of each billing cycle. And **Pay-As-You-Go overage is only available on the Scaling plan ($475/month) and above.** On Hobby, Startup, or Business, if you run out of credits mid-cycle, you're simply cut off until the next billing date.

Annual billing takes 10% off every plan, which is worth factoring in if you're planning to use ScraperAPI long-term.

---

**Three Ways to Integrate ScraperAPI (And Which One to Use)**

ScraperAPI supports three integration methods. Each has its use case, and knowing which to pick upfront saves you a lot of backtracking.

**Method 1: API Endpoint (The Most Common Approach)**

This is the default integration. You replace your target URL with ScraperAPI's endpoint, pass the target as a parameter, and add your API key. That's it.

python
import requests

api_key = "YOUR_API_KEY"
target_url = "https://example.com"

params = {
    "api_key": api_key,
    "url": target_url,
    "render": "true",           # Enable JS rendering
    "country_code": "us",       # Geo-target to US IPs
    "premium": "true",          # Use premium residential proxies
}

response = requests.get("http://api.scraperapi.com", params=params)
print(response.text)


This approach works with any HTTP library in any language. It's the most flexible, and it's what most tutorials show. Use this when you want full control over every parameter per-request.

**Method 2: Proxy Port (Drop-In Replacement)**

If you already have a scraper set up with a traditional proxy service, the proxy port method lets you swap ScraperAPI in with minimal code changes:

python
import requests

api_key = "YOUR_API_KEY"

proxies = {
    "http": f"http://scraperapi:{api_key}@proxy-server.scraperapi.com:8001",
    "https": f"http://scraperapi:{api_key}@proxy-server.scraperapi.com:8001",
}

response = requests.get("https://example.com", proxies=proxies)
print(response.text)


This is especially useful for headless browser setups (Puppeteer, Selenium, Playwright) that accept a proxy configuration. You point the browser at ScraperAPI's proxy server and everything flows through automatically — no request-level code changes required.

**Method 3: Python SDK (Cleanest for Python Projects)**

ScraperAPI publishes official SDKs for Python, Node.js, PHP, and Ruby. The Python SDK is the most polished:

python
from scraperapi_sdk import ScraperAPIClient

client = ScraperAPIClient("YOUR_API_KEY")

result = client.get(
    url="https://example.com",
    render=True,
    country_code="us"
)

print(result.text)


The SDK abstracts the URL construction, handles retry logic, and gives you a cleaner interface if you're building a Python-native data pipeline. For quick scripts or prototypes, the API endpoint approach is fine. For production Python pipelines, the SDK is worth using.

👉 [Get your free API key and start integrating ScraperAPI today](https://www.scraperapi.com/?fp_ref=coupons)

---

**ScraperAPI Integration with Python Scrapy**

Scrapy is the go-to framework for large-scale crawling in Python, and integrating ScraperAPI with Scrapy is one of the most popular use cases. There are two ways to do it: the middleware approach (recommended) and the manual URL-rewriting approach.

The middleware approach rewrites every outgoing request URL to pass through ScraperAPI automatically, so you don't have to change individual spider code:

python
# In settings.py
SCRAPERAPI_KEY = "YOUR_API_KEY"

DOWNLOADER_MIDDLEWARES = {
    "scraperapi_sdk.scrapy.middleware.ScraperAPIMiddleware": 100,
}


After this, your spiders run as normal, but every request automatically goes through ScraperAPI's proxy and anti-bot infrastructure. Concurrent requests are governed by ScraperAPI's thread limits on your plan, not Scrapy's own concurrent settings.

For a simple spider:

python
import scrapy

class ProductSpider(scrapy.Spider):
    name = "products"
    start_urls = ["https://example-shop.com/products"]

    def parse(self, response):
        for product in response.css("div.product"):
            yield {
                "name": product.css("h2::text").get(),
                "price": product.css(".price::text").get(),
            }


With the ScraperAPI middleware active, every request in this spider automatically gets proxy rotation, CAPTCHA handling, and optional JS rendering — without changing a single line of spider code.

---

**ScraperAPI Integration with Async Requests**

For high-volume scraping where you need to fire off thousands of requests without blocking, ScraperAPI's asynchronous approach is the right tool. Using Python's `asyncio` and `aiohttp`:

python
import aiohttp
import asyncio

async def fetch(session, url, api_key):
    api_url = f"http://api.scraperapi.com?api_key={api_key}&url={url}&render=true"
    async with session.get(api_url) as response:
        return await response.text()

async def main():
    api_key = "YOUR_API_KEY"
    urls = [
        "https://example.com/page1",
        "https://example.com/page2",
        "https://example.com/page3",
    ]
    async with aiohttp.ClientSession() as session:
        tasks = [fetch(session, url, api_key) for url in urls]
        results = await asyncio.gather(*tasks)
        for result in results:
            print(result[:200])  # Preview first 200 chars

asyncio.run(main())


Just remember: your concurrent thread limit is set by your ScraperAPI plan (20 on Hobby, up to 500 on Advanced/Enterprise). Firing more concurrent requests than your plan allows will result in queued or failed requests, not faster scraping.

---

**ScraperAPI Integration with AI Tools: LangChain, LlamaIndex, and MCP**

This is where things get interesting in 2026. ScraperAPI has been actively building out integrations for AI workflows — not just for traditional data pipelines, but for AI agents that need live web access.

**LangChain Integration**

LangChain is the dominant framework for building LLM-powered applications, and ScraperAPI integrates directly as a tool that gives your agents real-time web browsing capabilities. The combination is powerful: LangChain handles the agent orchestration and reasoning, while ScraperAPI handles proxy rotation, CAPTCHA bypass, and JS rendering on the scraping side.

A LangChain agent with ScraperAPI access can browse the web, extract structured data, and feed it directly into prompts — all while bypassing bot detection that would block a simple `requests.get()` call.

**MCP Server (Model Context Protocol)**

ScraperAPI now offers a hosted MCP (Model Context Protocol) server at `https://mcp.scraperapi.com/mcp`. This is the "write a prompt, get scraped data" integration — you connect your LLM to the MCP server using Bearer authentication with your API key, and the model can trigger scraping operations using natural language descriptions.

Configuration is straightforward: endpoint `https://mcp.scraperapi.com/mcp`, transport `HTTP Streamable`, authentication via your ScraperAPI key as a Bearer token. From that point, the AI agent can use ScraperAPI's full parameter set (geotargeting, JS rendering, premium proxies) just by describing what it needs.

ScraperAPI also offers a self-hosted MCP server option via their open-source `scraperapi-mcp` repository if you need to keep data on-premise.

**LlamaIndex Integration**

For teams using LlamaIndex to build retrieval-augmented generation (RAG) pipelines, ScraperAPI integrates as a data loader that fetches and formats live web content. The integration supports markdown output format, which is particularly useful for feeding scraped content directly into LLM contexts.

---

**ScraperAPI Integration with n8n and Zapier (No-Code Workflows)**

Not everyone building with ScraperAPI is writing Python. A significant portion of users integrate it into no-code automation workflows — and ScraperAPI now has official nodes for both n8n and Zapier.

**n8n Integration**

ScraperAPI has an official n8n node available directly from the n8n node library. To add it: open a workflow, click the + button to add a node, search "ScraperAPI," and select it. The node supports all of ScraperAPI's core parameters: JS rendering, geo-targeting, device type (desktop/mobile), premium and ultra-premium proxies, and output format (markdown, text, JSON for structured data endpoints).

Where it gets compelling: you can connect ScraperAPI as a tool node to an AI Agent in n8n. The setup looks like this:

1. Add a **Chat Message Received** trigger
2. Add an **AI Agent** node (connected to an OpenAI or Anthropic model)
3. Connect **Simple Memory** to the agent
4. Connect the **ScraperAPI** node as a tool
5. Write a system prompt describing the agent's behavior

The result is a conversational AI agent that can browse any website on request, with ScraperAPI handling all the anti-bot infrastructure in the background. It's a pretty powerful combination for building internal research tools or automated competitive intelligence pipelines.

ScraperAPI also integrates with n8n's MCP Client Tool node — configure the endpoint as `https://mcp.scraperapi.com/mcp` with Bearer auth, and you can use ScraperAPI as an MCP server within any n8n workflow.

**Zapier Integration**

ScraperAPI's Zapier integration (launched June 2026) brings scraping capabilities directly into Zapier's workflow ecosystem. You can trigger a scrape as part of any Zap, pass the target URL from any previous step, and pipe the scraped content into downstream actions — Google Sheets, Slack, Airtable, email, whatever your workflow needs.

This is particularly useful for non-developers who need to pull live web data into existing business workflows without writing any code.

👉 [Start your ScraperAPI free trial and explore all integrations](https://www.scraperapi.com/?fp_ref=coupons)

---

**Site-Specific Performance: Where ScraperAPI Works Well and Where It Doesn't**

Integration is only half the story. The other half is whether ScraperAPI actually succeeds at scraping your target site. Independent benchmarks from Scrapeway (April 2026) show a clear pattern:

| Target Site | Success Rate | Avg Speed | Notes |
|---|---|---|---|
| Zillow | 100% | 10.5s | Excellent |
| Etsy | 99% | 4.8s | Excellent |
| Amazon | 98% | 6.5s | Strong (use SDE) |
| LinkedIn | 95% | 17.8s | Expensive (30 credits) |
| Walmart | 93% | 11.4s | Good |
| Indeed | 90% | 15.8s | Acceptable |
| StockX | 84% | 3.9s | Moderate |
| Realtor.com | 12% | 11.8s | Poor |
| Instagram | 0% | — | Fails completely |
| Booking.com | 0% | — | Fails completely |
| Twitter/X | 0% | — | Fails completely |

The pattern: **e-commerce and real estate are ScraperAPI's wheelhouse.** Social media is a dead zone. If your primary targets are Amazon, Google, Zillow, or Walmart, ScraperAPI is a genuinely solid choice. If you need Instagram, Booking.com, or Twitter data, it simply won't work.

One other gotcha: ScraperAPI applies a **10-minute forced result cache on difficult targets**, meaning if you're scraping time-sensitive pricing or inventory data, you may receive results that are up to 10 minutes stale.

---

**Structured Data Endpoints: Skip the Parser, Get JSON Directly**

For the most popular scraping targets, ScraperAPI offers Structured Data Endpoints (SDEs) — pre-built parsers that return clean JSON instead of raw HTML. Current SDE coverage includes:

- **Amazon**: Product details (by ASIN), search results, competitor offers — 18+ fields including price, ratings, BSR, images, seller info, supporting 21 regional marketplaces
- **Google**: SERP (organic results, knowledge graph, ads, People Also Ask), Shopping, Maps, News, Jobs
- **Walmart**: Product, search, category, reviews
- **eBay**: Product, search
- **Redfin**: Property search, agent details, rentals, for-sale listings

SDEs are available on all plans including Free. The trade-off: Amazon requests cost 5 credits each via SDE (vs. 1 credit for a standard request you'd then need to parse yourself). For small teams without dedicated parser maintenance, the credit premium is often worth it for the saved development time. For high-volume teams with engineering capacity, building your own parser on standard requests saves significant credits at scale.

---

**Full Plan Comparison: All ScraperAPI Plans and Pricing**

Here's every currently available ScraperAPI plan, with pricing and key specs. Annual billing saves 10% across all paid plans.

| Plan | Monthly Price | Annual (per mo) | API Credits | Concurrent Threads | Geotargeting | Pay-As-You-Go | |
|---|---|---|---|---|---|---|---|
| **Free** | $0 | $0 | 1,000 | 5 | None | No | [ Get Started Free](https://www.scraperapi.com/?fp_ref=coupons) |
| **Hobby** | $49 | ~$44 | 100,000 | 20 | US & EU | No | [ Get Hobby Plan](https://www.scraperapi.com/?fp_ref=coupons) |
| **Startup** | $149 | ~$134 | 1,000,000 | 50 | US & EU | No | [ Get Startup Plan](https://www.scraperapi.com/?fp_ref=coupons) |
| **Business** | $299 | ~$269 | 3,000,000 | 100 | All countries | No | [ Get Business Plan](https://www.scraperapi.com/?fp_ref=coupons) |
| **Scaling** | $475 | ~$427 | 5,000,000 | 200 | All countries | Yes | [ Get Scaling Plan](https://www.scraperapi.com/?fp_ref=coupons) |
| **Professional** | $975 | ~$877 | 10,500,000 | 300 | All countries | Yes | [ Get Professional Plan](https://www.scraperapi.com/?fp_ref=coupons) |
| **Advanced** | $1,975 | ~$1,777 | 21,500,000 | 500 | All countries | Yes | [ Get Advanced Plan](https://www.scraperapi.com/?fp_ref=coupons) |
| **Enterprise** | Custom | Custom | 22M+ | 500+ | All countries | Yes | [ Contact Sales](https://www.scraperapi.com/?fp_ref=coupons) |

A few important distinctions between tiers:

- **Hobby and Startup**: Geotargeting limited to US and EU only. No Pay-As-You-Go — when credits run out, you're cut off until the next billing date.
- **Business**: First tier with global (country-level) geotargeting and unlimited analytics history.
- **Scaling**: Marked as "Most popular" — first tier with Pay-As-You-Go overage, so you can continue scraping when credits run out at a predictable per-credit rate.
- **Professional and Advanced**: Include priority support and Pay-As-You-Go.
- **Enterprise**: Includes a dedicated support team, a private Slack channel, and custom pricing based on volume.

For annual billing, multiply the monthly price by 0.9 to get the effective per-month rate. The 7-day trial gives new signups 5,000 free credits on top of the regular 1,000 free monthly credits to test at a larger scale before committing.

---

**Practical Tips for Getting the Most Out of ScraperAPI Integration**

Once you've got your integration set up, here are the things that actually matter for keeping costs under control and maintaining reliability:

**Check your credit math before you launch a large job.** Use ScraperAPI's `urlcost` API endpoint (`https://api.scraperapi.com/account/urlcost?api_key=YOUR_KEY&url=TARGET_URL&render=true`) to programmatically check the credit cost of any specific request before you run a batch. Every API response also includes a `sa-credit-cost` header showing the exact cost of that request — monitor this during your first few runs.

**Don't enable `render=true` by default.** JavaScript rendering costs 10 credits per request. Only enable it for pages that actually need it. Run a test request without rendering first; if you get the data you need, skip rendering entirely.

**Use structured data endpoints for supported sites.** If you're scraping Amazon or Google, the SDEs save you significant parser development and maintenance time. The credit premium is usually worth it unless you're running at very high volume.

**Set a spending cap if you're on Scaling or above.** Pay-As-You-Go is convenient, but ScraperAPI lets you set a monthly spending cap to prevent unexpected bills. Set it before you forget.

**Monitor the dashboard manually for the first month.** ScraperAPI doesn't send proactive usage alerts — no email when you hit 80% of credits. You need to check the dashboard manually. Analytics history is limited to 2 weeks on Hobby/Startup plans and extends to 6+ months on Business and above.

**Test your specific targets on the free tier before committing to a paid plan.** The 1,000 free credits plus 5,000-credit 7-day trial is enough to validate success rates on your target sites and understand your actual credit consumption with realistic parameters before you hand over a credit card.

---

**What Real Users Are Saying**

ScraperAPI has a solid reputation across review platforms, with ratings that reflect both genuine strengths and some real frustrations:

- **Capterra**: 4.6/5 (62 reviews) — Ease of Use scores 4.9/5, which reflects how genuinely frictionless the initial integration is
- **Trustpilot**: 4.5/5 (43 reviews)
- **G2**: 4.4/5 (16 reviews)

The consistent positives: fast to set up, solid documentation, reliable on Amazon/Google/Zillow, responsive support team.

The consistent complaints: credit multipliers create pricing surprises, credits don't roll over, and performance is highly variable depending on the target site. One Reddit user reported being quoted a price for 60 million Amazon credits, then discovering after payment that the 5-credit multiplier applied — effectively reducing their capacity from 60M to 12M requests. Not ideal.

> "ScraperAPI was extremely easy to use out of the box. We are able to get around website blocks easily." — Trustpilot

> "Breakdown of credit costs can be confusing." — John S., Founder, Capterra

The takeaway: ScraperAPI is genuinely good for what it's designed for. The frustrations almost always come from misunderstanding the credit system before signing up.

---

**Is ScraperAPI Worth It? Honest Assessment**

For developer teams building data pipelines against well-supported targets (Amazon, Google, Zillow, Walmart), ScraperAPI is a legitimate time-saver. Maintaining your own proxy fleet, CAPTCHA-solving infrastructure, and headless browser farm costs significantly more — in both time and money — at any serious scale.

The credit multiplier system is the main thing to get right before committing. Run the math for your specific use case: target sites × parameters × monthly volume = credit cost. If the effective cost per request fits your budget, ScraperAPI is a reliable choice with minimal infrastructure overhead.

If you're a solo developer scraping a handful of sites for a side project, the $49 Hobby plan covers a lot of ground for simple HTML scraping. If you need JS rendering on Amazon at volume, the math pushes you toward Business or Scaling quickly.

The 7-day free trial with 5,000 credits gives you enough room to validate both the integration and the performance on your specific targets before you commit to anything.

👉 [Start your free ScraperAPI trial — no credit card required](https://www.scraperapi.com/?fp_ref=coupons)

---

**Frequently Asked Questions**

**Can I use ScraperAPI for free?**
Yes. ScraperAPI offers a permanent free tier with 1,000 API credits per month and up to 5 concurrent connections. New signups also get a 7-day trial with an additional 5,000 credits. The free tier is functional for small projects and testing — just keep the credit multipliers in mind when estimating real capacity.

**Which ScraperAPI integration method should I use?**
For Python projects: use the SDK or direct API endpoint approach. For Scrapy: use the official middleware. For no-code workflows: use the n8n node or Zapier integration. For AI agents and LLMs: use the MCP server or LangChain integration. For existing proxy-based setups: use the proxy port method.

**Does ScraperAPI work with Scrapy?**
Yes, ScraperAPI has an official Scrapy middleware that routes all spider requests through its proxy and anti-bot infrastructure automatically. Install the SDK, add the middleware to `settings.py`, and your existing spiders work with ScraperAPI without modification.

**What happens when I run out of credits?**
On Hobby, Startup, and Business plans, your service is paused until the next billing cycle — there's no overage option. On Scaling ($475/month) and above, Pay-As-You-Go kicks in at a fixed per-credit rate, so you can continue scraping past your monthly limit.

**Does ScraperAPI support JavaScript rendering?**
Yes. Add `render=true` to any request to enable headless browser rendering. This costs 10 credits per request instead of 1, so only enable it when the target site requires JavaScript to load its content.

**Can ScraperAPI scrape sites that require login?**
No. ScraperAPI explicitly forbids scraping behind authentication walls. It supports session persistence via `session_number` for maintaining the same IP across requests, but it cannot handle login forms, 2FA, or cookie-based authentication.

**Is there a discount for annual billing?**
Yes — annual billing saves 10% on every paid plan. The Hobby plan drops from $49/month to approximately $44/month (billed annually), for example.

---

*The ScraperAPI free tier is available with no credit card required. Paid plans include a 7-day no-questions-asked refund policy.*
