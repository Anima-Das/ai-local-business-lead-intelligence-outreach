<div align="center">

# AI Local Business Lead Intelligence & Outreach Pipeline

**AI-assisted local-business discovery, enrichment, validation, qualification, and personalized outreach drafting, orchestrated in n8n.**

Search queries go in. Structured, scored, outreach-ready lead rows come out in Google Sheets.

<br/>

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-EA4B71?style=flat-square&logo=n8n&logoColor=white)
![Google Maps](https://img.shields.io/badge/Google%20Maps-via%20Serper-4285F4?style=flat-square&logo=googlemaps&logoColor=white)
![Serper](https://img.shields.io/badge/Serper-Maps%20%2B%20Search%20API-0F766E?style=flat-square)
![Ollama](https://img.shields.io/badge/Ollama-llama3-334155?style=flat-square)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Lead%20Output-34A853?style=flat-square&logo=googlesheets&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-Code%20Nodes-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
![Export](https://img.shields.io/badge/Workflow%20Export-Inactive-6B7280?style=flat-square)

<br/>

`Local Business Lead Intelligence` · `AI Enrichment` · `Social Discovery` · `Email Validation` · `Outreach Automation`

<sub>Google Maps Discovery · Website Intelligence · Social Discovery · Email Validation · AI Enrichment · Lead Scoring · Outreach Drafting</sub>

</div>

---

## 🧩 Project Snapshot

| 🧩 Area | ⚙️ Implementation | 🎯 Purpose |
|:--|:--|:--|
| **Orchestration** | n8n workflow `05 AI Local Business Lead Intelligence & Outreach Pipeline`, started by a manual trigger. 35 working nodes plus 8 sticky notes. | Run the whole pipeline on demand from a list of search queries |
| **Business Discovery** | Serper Maps endpoint: page 1 by GET, pages 2 to 12 by one batched POST | Collect local business records: name, phone, website, rating, category |
| **Website Research** | One HTTP request per business website, parsed by JavaScript | Find an email address and social links on the business site |
| **Social Intelligence** | Website links combined with a separate Serper search, compared by handle | Resolve six social platforms with HIGH / MEDIUM / LOW confidence |
| **Email Validation** | HTTP call to `rapid-email-verifier.fly.dev/api/validate` | Label each found email as `VALID`, `RISKY`, or `NOT_FOUND_OR_INVALID` |
| **AI Enrichment** | Ollama-style `/api/generate` request using `llama3` | Generate a services list and a one-sentence summary |
| **Outreach** | Second Ollama-style request plus a cleanup step | Draft a short outreach message per lead (drafted only, not sent) |
| **Lead Scoring** | JavaScript rule set | Produce a workflow-defined `N/10` score |
| **Persistence** | Google Sheets, append-or-update matched on `Name` | Final structured lead output |
| **Rate Limiting** | Five Wait nodes and four batch loops | Pace requests to external services |



## 🖼️ Workflow Overview

<img width="1271" height="508" alt="05 AI Local Business Lead Intelligence   Outreach Pipeline" src="https://github.com/user-attachments/assets/7046e26d-daaa-45eb-846c-6cc398b9919e" />


The canvas is annotated with sticky notes for the introduction and for the Maps, Scraping, Social, Email validation, AI, and Export sections, plus an Ollama setup note. Business discovery runs first, website and social intelligence follow, then email validation, cleaning, scoring, deduplication, AI enrichment, outreach drafting, and the Google Sheets write.

---

## 🎯 What This System Does

The workflow turns a list of local-business search queries into structured lead records. Everything below is taken from node configuration and Code node logic in the exported JSON.

1. Receives a `queries` array and splits it into individual `query` items.
2. Fetches page 1 of Google Maps results for each query through Serper.
3. Fetches pages 2 through 12 using the `q` and `ll` values returned with page 1.
4. Extracts business name, phone, website, rating, and category, keeping places that include a phone number.
5. Processes businesses in batches of 5 and requests each business website.
6. Extracts the first email and the first social link per platform found in the response.
7. Runs a separate Serper search on the business name to find social profiles.
8. Combines both sources, resolves each platform, and assigns HIGH, MEDIUM, or LOW confidence.
9. Validates each discovered email through an external validation endpoint.
10. Routes leads without an email down a separate preparation path with status `NOT_FOUND`.
11. Merges both paths, cleans fields and phone formatting, and calculates a rule-based lead score.
12. Removes duplicates by `website` and `name`.
13. Asks an LLM for services and a summary, then for a short outreach message.
14. Appends or updates the finished record in Google Sheets.

> [!NOTE]
> The workflow drafts outreach text only. No node sends an email or a message.

---

## ✨ Core Capabilities

| Capability | What the workflow does |
|:--|:--|
| 🔎 **Local Business Discovery** | Serper Maps retrieval across page 1 and pages 2 to 12 per query |
| 🌐 **Website Intelligence** | Requests each business website and scans the response for emails and social links |
| 📱 **Social Profile Discovery** | Instagram, Facebook, LinkedIn, Twitter/X, YouTube, and TikTok from the website and from Serper search |
| 🎯 **Social Confidence** | HIGH, MEDIUM, or LOW per platform based on source availability and agreement |
| ✉️ **Email Extraction & Validation** | Regex extraction with obfuscation handling, then an external validation call |
| 🧠 **AI Business Enrichment** | LLM-generated services list and one-sentence summary |
| ✍️ **Outreach Drafting** | LLM-generated short message, cleaned by a Code node |
| 📊 **Lead Scoring** | Rule-based score from 0 to 10, output as `N/10` |
| 🧹 **Deduplication** | Duplicate leads removed on `website` and `name` before AI enrichment |
| 📋 **Google Sheets Output** | 23 mapped columns, appended or updated on `Name` |
| ⏱️ **Rate-Limit Protection** | Wait nodes and batch loops around Maps, AI, and outreach calls |

---

## 🔄 Workflow Stages

| # | Stage | What happens |
|:--:|:--|:--|
| 01 | Query Intake | Manual trigger starts the run with a `queries` array |
| 02 | Query Split | `queries` is split into one `query` item each and paced through a loop |
| 03 | Maps Discovery | Page 1, then pages 2 to 12, retrieved from Serper Maps |
| 04 | Business Extraction | Name, phone, website, rating, and type are extracted into lead items |
| 05 | Website Intelligence | Each website is requested in batches of 5; email and social links are extracted |
| 06 | Social Search | Serper search supplies social profiles to compare with website links |
| 07 | Social Resolution | Sources are merged per platform and given a confidence level |
| 08 | Email Validation | Leads with an email are validated; leads without one take a separate path |
| 09 | Cleaning | Defaults, phone formatting, and standard fields are applied |
| 10 | Lead Scoring | Rule-based `N/10` score is calculated |
| 11 | Deduplication | Duplicates are removed on `website` and `name` |
| 12 | AI Enrichment | Services and summary are generated and parsed, with fallback logic |
| 13 | Outreach and Export | A message is drafted and cleaned, then the record is saved to Google Sheets |

---

## 🏗️ Architecture Overview

| Layer | Responsibility | Main components |
|:--|:--|:--|
| **Input** | Accept search queries | `Start Lead Generation`, `Split Search Queries` |
| **Pacing** | Process queries and requests at a controlled rate | `Process Each Query`, `Rate Limit Protection`, `Delay Between Requests` |
| **Discovery** | Find local businesses | `Fetch Maps Results (Page 1)`, `Fetch Maps Results (Pages 2–12)` |
| **Extraction** | Build lead items from Maps data | `Extract Businesses From Maps` |
| **Website Intelligence** | Extract email and social links | `Scrape Business Website`, `Extract Socials & Email` |
| **Social Intelligence** | Find and resolve social profiles | `Build Social Search Query`, `Search Social Profiles (Serper)`, `Parse Social Results`, `Resolve & Score Social Profiles` |
| **Validation** | Evaluate discovered emails | `Check Email Exists`, `Validate Email Address` |
| **Cleaning** | Normalize, score, and deduplicate | `Clean Lead Data`, `Calculate Lead Score`, `Remove Duplicate Leads` |
| **AI Enrichment** | Services, summary, outreach text | `Analyze Business (AI)`, `Extract AI Insights`, `Create Outreach Message`, `Clean Outreach Message` |
| **Persistence** | Store structured leads | `Save Leads to Google Sheets` |

<details>
<summary><b>🧱 Key Workflow Components (all 35 working nodes)</b></summary>

<br/>

| Node | Type | Responsibility |
|:--|:--|:--|
| `Start Lead Generation` | Manual Trigger | Starts a run |
| `Split Search Queries` | Split Out | Splits the `queries` field into `query` items |
| `Process Each Query` | Split In Batches | Loops queries through a wait; its completion output feeds Maps page 1 |
| `Rate Limit Protection` | Wait | Pacing inside the query loop (amount `3`) |
| `Fetch Maps Results (Page 1)` | HTTP Request | GET `https://google.serper.dev/maps?q=` with the query |
| `Delay Between Requests` | Wait | Pause between page 1 and later pages (node defaults) |
| `Fetch Maps Results (Pages 2–12)` | HTTP Request | POST to Serper Maps with a JSON array of requests for pages 2 to 12 |
| `Extract Businesses From Maps` | Code | Combines page 1 and later results; keeps places with a phone number |
| `Process Businesses in Batches` | Split In Batches | Batch size 5; loop output feeds website scraping, completion feeds email routing |
| `Scrape Business Website` | HTTP Request | Requests the business website; continues on error |
| `Extract Socials & Email` | Code | Regex extraction of email and six social links |
| `Build Social Search Query` | Code | Cleans the business name and builds a site-restricted `smartQuery` string |
| `Search Social Profiles (Serper)` | HTTP Request | POST to Serper search for social profiles |
| `Parse Social Results` | Code | Pulls the first matching profile URL per platform from the search response |
| `Combine Website + Serper Results` | Merge | Combines website and search results by position |
| `Resolve & Score Social Profiles` | Code | Filters non-profile links, merges sources, assigns confidence |
| `Normalize Social Data` | Code | Applies `-` defaults and outputs the standard lead shape |
| `Check Email Exists` | IF | Routes on whether an email is present |
| `Validate Email Address` | HTTP Request | GET to the email validation endpoint |
| `Prepare Valid Leads` | Code | Maps validator result to `VALID`, `RISKY`, or `NOT_FOUND_OR_INVALID` |
| `Prepare Leads Without Email` | Code | Builds leads with empty email and status `NOT_FOUND` |
| `Merge Lead Results` | Merge | Joins the with-email and without-email paths |
| `Clean Lead Data` | Code | Safe defaults and phone formatting |
| `Calculate Lead Score` | Code | Rule-based score from 0 to 10 |
| `Remove Duplicate Leads` | Remove Duplicates | Compares `website, name` |
| `Process Leads for AI Enrichment` | Split In Batches | Loops leads through the AI request; completion feeds insight extraction |
| `AI Rate Limit Buffer` | Wait | Pacing before each analysis request (amount `2`) |
| `Analyze Business (AI)` | HTTP Request | POST to `YOUR_OLLAMA_URL/api/generate` with `llama3` |
| `Extract AI Insights` | Code | Parses JSON from the model response; applies deterministic fallback |
| `AI Processing Buffer` | Wait | Pause between enrichment and outreach (amount `2`) |
| `Process Outreach Message` | Split In Batches | Loops leads through message creation; completion feeds cleanup |
| `Message Rate Limit Buffer` | Wait | Pacing before each outreach request (amount `2`) |
| `Create Outreach Message` | HTTP Request | POST to `YOUR_OLLAMA_URL/api/generate` with `llama3` |
| `Clean Outreach Message` | Code | Strips intro text, edge quotes, and newlines |
| `Save Leads to Google Sheets` | Google Sheets | Append or update on `Name` |

</details>

<details>
<summary><b>🔁 Loop wiring</b></summary>

<br/>

| Loop node | Loop output | Completion output |
|:--|:--|:--|
| `Process Each Query` | `Rate Limit Protection`, then back into the loop | `Fetch Maps Results (Page 1)` |
| `Process Businesses in Batches` (size 5) | `Scrape Business Website`, social resolution, `Normalize Social Data`, then back into the loop | `Check Email Exists` |
| `Process Leads for AI Enrichment` | `AI Rate Limit Buffer`, `Analyze Business (AI)`, then back into the loop | `Extract AI Insights` |
| `Process Outreach Message` | `Message Rate Limit Buffer`, `Create Outreach Message`, then back into the loop | `Clean Outreach Message` |

</details>

---

## 🗺️ Local Business Discovery

| Step | Node | Behavior |
|:--:|:--|:--|
| 1 | `Fetch Maps Results (Page 1)` | GET `https://google.serper.dev/maps?q={{ query }}` using generic HTTP Header Auth |
| 2 | `Delay Between Requests` | Wait node before the next request |
| 3 | `Fetch Maps Results (Pages 2–12)` | POST `https://google.serper.dev/maps` with a JSON array of 11 request objects, one each for pages 2 to 12, reusing `searchParameters.q` and `ll` from page 1 |
| 4 | `Extract Businesses From Maps` | Merges page 1 with the later pages and extracts business fields |

Extracted fields: `name` (from `title`), `phone` (from `phoneNumber`), `website`, `rating`, and `type`. Only places that include a phone number are kept.

Page retrieval is fixed by the current node configuration. Result volume depends entirely on what Serper returns for each query, so coverage is not guaranteed.

---

## 🔎 Search Query Processing

- A **manual trigger** (`Start Lead Generation`) starts the run.
- The input item carries a `queries` array; `Split Search Queries` emits one item per entry in a `query` field.
- `Process Each Query` is a batch loop that passes each query through `Rate Limit Protection` (Wait, amount `3`). When the loop completes, the query items continue to Maps retrieval.
- Each query is retrieved independently through the Maps nodes.

The export contains pinned sample data with a single query, `bakery littlevenice`. That is sample input stored with the workflow, not production configuration.

---

## 🌐 Website Intelligence

`Scrape Business Website` requests each business website with browser-style headers (`User-Agent: Mozilla/5.0`, `Accept: text/html`, `Accept-Language: en-US,en;q=0.9`) and is set to continue on error. `Extract Socials & Email` then serializes the response to lowercase text and scans it with regular expressions.

| Target | Matching behavior |
|:--|:--|
| **Email** | Regex for plain addresses and `mailto:` links, with obfuscation handling |
| **Instagram** | `instagram.com` URLs, `instagr.am` links, `instagram:` handles |
| **Facebook** | `facebook.com` and `fb.com` links |
| **LinkedIn** | `linkedin.com` links |
| **Twitter / X** | `twitter.com` and `x.com` links |
| **YouTube** | `youtube.com` links |
| **TikTok** | `tiktok.com` links |

For each platform, the first match is used, query strings are removed, and `-` is returned when nothing is found. Website access varies by target site, so extraction depends on what each site returns and is not guaranteed to succeed.

---

## 📱 Social Profile Discovery

Profiles can come from two sources, which are then combined per platform.

| Source | How it works |
|:--|:--|
| **1. Business website** | Links extracted from the website response by `Extract Socials & Email` |
| **2. Serper social search** | `Build Social Search Query` prepares the name, `Search Social Profiles (Serper)` calls Serper search, and `Parse Social Results` extracts the first profile URL per platform |

**Platforms:** Instagram, Facebook, LinkedIn, Twitter/X, YouTube, TikTok.

`Parse Social Results` applies platform-specific patterns: Instagram excludes post and reel paths, Facebook excludes share and video paths, LinkedIn accepts `/company/` pages, YouTube accepts `@`, `channel`, and `c/` paths, and TikTok accepts `@` handles.

`Combine Website + Serper Results` merges both streams by position. `Resolve & Score Social Profiles` then discards links that look like posts, videos, or share links, compares the remaining website and search results, and assigns a confidence level. The workflow does not verify account ownership.

### 🎯 Social Profile Confidence

| Situation per platform | Resolved value | Confidence |
|:--|:--|:--:|
| Website link only | Website link | `HIGH` |
| Both sources, same final path segment (handle) | Website link | `HIGH` |
| Search result only | Search result | `MEDIUM` |
| Both sources, different handles | Both URLs joined with ` \| ` | `MEDIUM` |
| Nothing usable found | `-` | `LOW` |

Confidence reflects source availability and agreement. It is a heuristic label, not identity verification.

---

## ✉️ Email Extraction

Implemented inside `Extract Socials & Email`:

- Replaces `[at]`, `(at)`, and whitespace-delimited `at` with `@`, and `[dot]`, `(dot)`, and whitespace-delimited `dot` with `.`.
- Extracts addresses with a regex that also handles `mailto:` links.
- Lowercases, trims, and removes duplicates.
- Filters addresses containing basic placeholder patterns: `example.com`, `domain.com`, `email.com`, `test.com`, `user@`, `admin@domain`, `yourname@`, `noreply@`, `donotreply@`.
- Selects the first remaining address, or an empty string when none remains.

An extracted address is a candidate only. It is not guaranteed to be deliverable.

---

## ✅ Email Validation

`Check Email Exists` is an IF node that tests whether the email from `Normalize Social Data` is non-empty. When it is, `Validate Email Address` sends a GET request to:

`https://rapid-email-verifier.fly.dev/api/validate`

with the address in the `email` query parameter. `Prepare Valid Leads` reads the `result` field (or `status` if `result` is absent) and maps it:

| Validator result | Stored `Email` | Stored `Email Status` |
|:--|:--|:--|
| `invalid`, `undeliverable` | Cleared to empty | `NOT_FOUND_OR_INVALID` |
| `risky`, `catch-all`, `unknown` | Kept | `RISKY` |
| Any other value | Kept | `VALID` |

Statuses reflect what the external validator reported. They do not guarantee that a mailbox exists or that a message will be delivered.

### Leads Without an Email

When no email was found, the lead is not discarded. `Prepare Leads Without Email` builds the same lead shape with business information, social profiles, and confidence fields, an empty `email`, and `emailStatus` set to `NOT_FOUND`. `Merge Lead Results` then joins both paths.

---

## 🧹 Data Cleaning & Normalization

`Normalize Social Data` and `Clean Lead Data` apply the same safe-value rule: `undefined`, `null`, and blank strings become `-`.

`Clean Lead Data` also formats phone numbers:

- Removes everything except digits and `+`, and drops a leading `+`.
- Returns very short numbers (under 7 digits) as plain digits.
- Treats digits beyond the last 10 as a country code.
- Formats 10-digit numbers without a country code as `(XXX) XXX-XXXX`.
- Formats other numbers as `(+CC)` followed by grouped digits.
- Prefixes an apostrophe so Google Sheets keeps the value as text.

This is formatting only. The workflow does not validate phone numbers.

---

## ♻️ Duplicate Lead Protection

`Remove Duplicate Leads` is a Remove Duplicates node comparing the selected fields `website, name`. It runs after cleaning and scoring and before AI enrichment, so duplicates do not consume model requests. The comparison covers items within the current execution only; it is not a global deduplication against previously saved sheet data.

---

## 🧠 AI Business Enrichment

| Item | Detail |
|:--|:--|
| **Node** | `Analyze Business (AI)` |
| **Request** | POST `YOUR_OLLAMA_URL/api/generate` with `Content-Type: application/json` |
| **Model** | `llama3`, with `stream` set to `false` |
| **Timeout** | 60000 ms |
| **Inputs in the prompt** | Business name, business type, website, rating |
| **Expected output** | JSON with `services` (array) and `summary` (one sentence) |
| **Parsing** | `Extract AI Insights` |

The prompt tells the model to return only JSON, keep the summary to one natural sentence, avoid repeated phrasing, and avoid empty fields. It also tells the model to infer from the business name and type when the website or details are missing. The model does not browse the web in this workflow, and its output is not independently fact-checked. Services and summaries should be read as model-generated text.

`Extract AI Insights` extracts the first JSON object from the `response` text, parses it, and joins the services array into a comma-separated string.

### 🧯 AI Output Fallback

If the response has no parseable JSON, or the fields are empty, deterministic logic fills the gap. This is template logic, not AI generation:

- **Services:** the business type, or `Local business services` when no type is available.
- **Summary:** a fixed template sentence built from the business name and type.

---

## ✍️ Personalized Outreach

`Create Outreach Message` sends a second request to `YOUR_OLLAMA_URL/api/generate` with `llama3`, using the lead's name, type, and website from `Clean Lead Data`. The prompt frames the writer as an AI automation specialist and instructs the model to:

- write 2 to 3 sentences that sound natural and human
- avoid sounding salesy or spammy
- suggest improving bookings, customer engagement, or reducing manual work
- avoid pretending to be the business
- avoid mentioning AI unless natural
- return only the message text

`Clean Outreach Message` then removes an introductory `Here is ...:` line, strips edge quotes, and collapses newlines into spaces. The result is stored as `message` and written to the `Draft message (AI)` column. Message quality depends on the model, and the workflow makes no claims about response or conversion.

---

## 📊 Lead Scoring

`Calculate Lead Score` assigns a **rule-based, workflow-defined lead score**. It is not a machine-learning prediction and not a validated conversion probability.

| Factor | Condition | Points |
|:--|:--|:--:|
| Website | Present / absent | +2 / +1 |
| Instagram | Instagram field is populated | +2 |
| Social breadth | 3 or more active profiles / 1 or 2 | +3 / +2 |
| Rating | 4.6 or higher / 4.2 or higher | +2 / +1 |
| Email | Status is `VALID` | +1 |
| No social presence | Zero active profiles | +1 |
| Instagram and no website | Instagram field set and website is `-` | +2 |

The total is rounded, capped at 10, and written as a string such as `7/10`. Scoring runs after cleaning and before deduplication and AI enrichment.

---

## 📋 Google Sheets Output

`Save Leads to Google Sheets` uses the `appendOrUpdate` operation with `Name` as the matching column and an explicit column mapping. Google Sheets is the final structured storage and output layer; the workflow does not use it as, or connect it to, a CRM.

## 🧾 Lead Output Structure

| Field | Meaning |
|:--|:--|
| `Name` | Business name (also the match column) |
| `Type` | Business category from Maps |
| `Website` | Business website |
| `Email` | Extracted email (empty if none or rejected) |
| `Email Status` | `VALID`, `RISKY`, `NOT_FOUND_OR_INVALID`, or `NOT_FOUND` |
| `Phone` | Formatted phone number |
| `Rating` | Maps rating |
| `Instagram` and `Instagram Confidence` | Resolved profile and HIGH / MEDIUM / LOW |
| `Services (AI)` | Comma-separated services |
| `Summary (AI)` | One-sentence business summary |
| `LeadScore` | Rule-based score such as `7/10` |
| `Draft message (AI)` | Cleaned outreach draft |

<details>
<summary><b>📑 All 23 mapped columns and sheet header notes</b></summary>

<br/>

| Group | Columns |
|:--|:--|
| **Business** | `Name`, `Type`, `Website`, `Phone`, `Rating` |
| **Email** | `Email`, `Email Status` |
| **Social profiles** | `Instagram`, `Facebook`, `Linkedin`, `Twitter`, `Tiktok`, `Youtube` |
| **Social confidence** | `Instagram Confidence`, `Facebook Confidence`, `Linkedin Confidence`, `Twitter Confidence`, `Tiktok Confidence`, `Youtube Confidence` |
| **AI and scoring** | `Services (AI)`, `Summary (AI)`, `LeadScore`, `Draft message (AI)` |

The column identifiers in the export contain a line break before the second word (for example `Email` + newline + ` Status`) and the LinkedIn confidence column is spelled `Linkedln Confidence`. The sheet header row has to match these identifiers exactly, or the column mapping in the Google Sheets node has to be re-pointed to your own headers.

</details>

<details>
<summary><b>🧪 Illustrative record shape (sanitized, not real output)</b></summary>

<br/>

| Field | Example value |
|:--|:--|
| `Name` | Example Business |
| `Type` | Example category |
| `Website` | `https://www.business-domain.com` |
| `Email` | `example@business-domain.com` |
| `Email Status` | `VALID` |
| `Instagram` | `https://www.instagram.com/example-handle` |
| `Instagram Confidence` | `HIGH` |
| `Facebook` | `-` |
| `Facebook Confidence` | `LOW` |
| `LeadScore` | `7/10` |

</details>

---

## ⏱️ Rate-Limit Protection

| Wait node | Position in the flow | Configured amount |
|:--|:--|:--:|
| `Rate Limit Protection` | Inside the query loop | `3` |
| `Delay Between Requests` | Between Maps page 1 and pages 2 to 12 | node defaults |
| `AI Rate Limit Buffer` | Before each business analysis request | `2` |
| `AI Processing Buffer` | Between AI enrichment and outreach | `2` |
| `Message Rate Limit Buffer` | Before each outreach request | `2` |

Together with the batch loops (businesses in batches of 5), these nodes pace requests. They do not guarantee compliance with any provider's usage limits. The export does not store a time unit for these Wait nodes, so confirm the effective unit in the n8n editor before running.

---

## 🧰 Technology Stack

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=flat-square&logo=n8n&logoColor=white)
![Serper](https://img.shields.io/badge/Serper-0F766E?style=flat-square)
![Google Maps](https://img.shields.io/badge/Google%20Maps-4285F4?style=flat-square&logo=googlemaps&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-334155?style=flat-square)
![Llama 3](https://img.shields.io/badge/Model-llama3-475569?style=flat-square)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=flat-square&logo=googlesheets&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)

| Technology | Role in this workflow |
|:--|:--|
| **n8n** | Workflow orchestration, loops, routing, merging, scheduling of waits |
| **Serper API** | Google Maps results and social-profile search over authenticated HTTP |
| **Google Maps data (via Serper)** | Business name, phone, website, rating, category |
| **Email validation API** | `rapid-email-verifier.fly.dev/api/validate` for email status |
| **Ollama-style endpoint, `llama3`** | Business analysis and outreach drafting |
| **Google Sheets** | Lead output with append-or-update |
| **JavaScript (Code nodes)** | Extraction, parsing, confidence logic, cleaning, scoring |
| **HTTP Request** | Website fetches and all provider calls |
| **Social platforms** | Instagram, Facebook, LinkedIn, Twitter/X, YouTube, TikTok are detected and searched as link targets only |

---

## ⚙️ AI Configuration

| Setting | Value |
|:--|:--|
| Endpoint (both AI nodes) | `YOUR_OLLAMA_URL/api/generate` |
| Method | `POST` |
| Model | `llama3` |
| Streaming | `false` |
| Request format | JSON body with `model`, `prompt`, and `stream` |
| Analysis output | JSON with `services` and `summary`, parsed from the `response` text |
| Outreach output | Plain message text from `response`, cleaned by a Code node |
| Failure handling | Deterministic fallback for services and summary |

The endpoint value is a placeholder. Replace it with your own Ollama URL and make sure the `llama3` model is available there.

<details>
<summary><b>🔀 Optional alternative endpoints mentioned in the workflow note</b></summary>

<br/>

> [!NOTE]
> This list comes from the `Ollama Setup` sticky note. Only the Ollama-style `/api/generate` configuration above is set in the nodes. These alternatives are optional possibilities and are not integrated or enabled.

**Ollama endpoint variants named in the note**

| Environment | Endpoint |
|:--|:--|
| Mac (Docker) | `http://docker.for.mac.host.internal:11434/api/generate` |
| Windows / Linux (Docker) | `http://host.docker.internal:11434/api/generate` |
| Local (no Docker) | `http://localhost:11434/api/generate` |
| Remote server | `http://YOUR_SERVER_IP:11434/api/generate` |
| n8n Cloud | Ollama must be reachable through a public URL or tunnel |

**Other endpoints the note names for OpenAI-compatible use**

`https://api.openai.com/v1/chat/completions` · `https://api.groq.com/openai/v1/chat/completions` · `https://api.together.xyz/v1/chat/completions` · `https://api.mistral.ai/v1/chat/completions` · `https://api.anthropic.com/v1/messages`

The note also says to update the Authorization header with an API key. The current request bodies use the Ollama `prompt` format and the parsing nodes read the `response` field, so switching to a chat-completions style API would also require changes to the request body and to the parsing code.

</details>

## 🔑 Serper Configuration

Three nodes call Serper with generic HTTP Header Auth: `Fetch Maps Results (Page 1)`, `Fetch Maps Results (Pages 2–12)`, and `Search Social Profiles (Serper)`. Create an n8n Header Auth credential that carries your Serper API key and attach it to those nodes. The exported JSON contains no credential bindings and no key values.

## 📋 Google Sheets Configuration

- Google Sheets OAuth must be configured in n8n and attached to `Save Leads to Google Sheets`.
- The document identifier and the sheet selection are both placeholders in the export: `YOUR_GOOGLE_SHEET_ID`. Replace them with your own spreadsheet and tab.
- The node writes structured lead records with `appendOrUpdate`, matched on `Name`.

---

## 🚀 Setup & Import

> [!IMPORTANT]
> The workflow needs external credentials and provider configuration before it can run. It does not work out of the box.

1. Import `AI Local Business Lead Intelligence & Outreach Pipeline.json` into n8n.
2. Create a Header Auth credential for Serper and attach it to the three Serper nodes.
3. Configure Google Sheets OAuth in n8n.
4. Replace the `YOUR_GOOGLE_SHEET_ID` placeholders in the Google Sheets node (spreadsheet and sheet).
5. Make sure your sheet header row matches the mapped column names listed above.
6. Review the email validation endpoint in `Validate Email Address` and change it if you use a different service.
7. Replace `YOUR_OLLAMA_URL` in `Analyze Business (AI)` and `Create Outreach Message`.
8. Confirm the `llama3` model is available on that endpoint.
9. Confirm the unit of each Wait node in the n8n editor.
10. Review the search queries you plan to use.
11. Test the discovery path with one query and a small result set.
12. Test the validation and AI paths, then confirm the Google Sheets output.
13. Activate only after controlled testing.

### 📥 Input Format

The manual trigger starts the run with an item containing a `queries` array. The queries below are an example only.

```json
{
  "queries": [
    "dentists in Pune",
    "gyms in Delhi"
  ]
}
```

---

## 🧪 Validation Scenarios

<details>
<summary><b>Show scenarios (these are test scenarios; none are claimed as executed)</b></summary>

<br/>

| Scenario | Expected behavior |
|:--|:--|
| One valid search query | Businesses are returned from Maps |
| Multiple search queries | Each query is processed through the loop |
| Missing website | Website-dependent extraction degrades to empty or `-` values |
| No email found | Lead takes the without-email path with status `NOT_FOUND` |
| Invalid email | Email is cleared and status is `NOT_FOUND_OR_INVALID` |
| Risky email | Email is kept with status `RISKY` |
| Website unavailable | Request continues on error and available fields are preserved |
| Social profile not on site | Serper search result is used with `MEDIUM` confidence |
| Duplicate business | Duplicate on `website` and `name` is filtered |
| AI JSON valid | Services and summary are parsed from the response |
| AI JSON malformed | Deterministic fallback services and summary are used |
| Outreach generated | Message is cleaned and stored as `Draft message (AI)` |
| Lead score calculated | An `N/10` rule-based score is produced |
| Google Sheets write | Lead row is appended or updated by `Name` |

</details>

---

## 🧯 Failure & Edge Cases

| Situation | Handling in the workflow | Caveat |
|:--|:--|:--|
| No email found | Separate path, status `NOT_FOUND` | Lead is kept |
| Invalid email | Email cleared, status `NOT_FOUND_OR_INVALID` | Depends on validator output |
| Risky email | Status `RISKY`, email kept | Includes `catch-all` and `unknown` |
| Website request failure | `Scrape Business Website` continues on error | Extracted fields may be empty or `-` |
| Unavailable social profiles | Stored as `-` with `LOW` confidence | No further retry |
| Malformed AI response | JSON parse in try/catch, then fallback | Fallback text is generic |
| Empty AI response | No JSON match, fallback is used | Outreach path has no separate fallback |
| Duplicate data | Removed on `website` and `name` | Same-execution items only |
| Missing optional fields | `-` defaults applied | Missing website is `-` in scoring |
| Provider errors | No dedicated error branch is configured | Only the website request sets continue-on-error |
| API delays | Wait nodes and a 60000 ms timeout on the analysis request | Outreach request has no timeout set |

Not every failure is recovered. Errors from Serper, the email validator, the AI endpoint, or Google Sheets are not given custom handling in this export.

---

## 🎯 Data Quality Controls

| Control | Mechanism |
|:--|:--|
| **Normalization** | `-` defaults, phone formatting, standard lead shape |
| **Deduplication** | `website` and `name` comparison before AI enrichment |
| **Email status** | Four explicit states from validator results or absence |
| **Social confidence** | HIGH, MEDIUM, or LOW from source availability and agreement |
| **Link filtering** | Post, reel, video, share, and intent links are discarded during social resolution |
| **AI output parsing** | JSON extraction with try/catch and deterministic fallback |
| **Safe defaulting** | Missing values become `-` or empty strings instead of failing the item |
| **Missing-field preservation** | Leads without email or social profiles remain in the output |

These are controls, not guarantees of data accuracy.

---

## ⚠️ Limitations

- Discovery depends on Serper and on what it returns for each query. Only places with a phone number are carried forward.
- Website extraction depends on site availability and returns the first match per field.
- Email status depends on an external validation service. Any validator response outside the listed values, including a missing `result` field, is treated as `VALID`.
- Social confidence is heuristic and source-based. Name-based search results can differ from the actual business account, and ownership is not verified.
- Services, summaries, and outreach drafts are model-generated. The analysis prompt allows the model to infer from name and type when details are missing, so the output is not verified fact.
- The lead score is a rule-based heuristic. The Instagram-and-no-website rule tests the Instagram field for any non-empty value, and unresolved profiles are stored as `-`, so review that rule before relying on scores.
- The Google Sheets upsert matches on `Name`, so different businesses with the same name would target the same row.
- The workflow drafts messages only and sends nothing.
- The social search request body sends the business name as `q`; the `smartQuery` string built by `Build Social Search Query` is not referenced by that request.
- The website request has `allowUnauthorizedCerts` enabled, which skips TLS certificate checks for that call.
- Wait node time units are not stored in the export.
- No runtime performance metrics are established by the JSON, and no production deployment should be inferred from an inactive workflow export.
- Google Sheets is an output store, not a CRM.

---

## 🔐 Security & Secret Handling

> [!WARNING]
> Do not commit real API keys, OAuth credentials, private contact data, or production spreadsheet identifiers.

- Keep the Serper key and any other API keys in n8n credentials, not in node parameters.
- Do not commit Google OAuth credentials.
- Do not commit AI endpoint URLs or tokens that expose a private server.
- Review spreadsheet identifiers before publishing.
- Check workflow exports for secrets and private identifiers before pushing them to a public repository.

## 🛡️ Privacy & Data Handling

The workflow processes business lead data, including emails, phone numbers, and social profile URLs. Handle that data responsibly. Do not commit real lead datasets or exported sheets to a public repository, and sanitize any screenshot used for the portfolio. This section is a technical handling note and makes no legal compliance claims.

---

## 📁 Repository Structure

```text
.
  README.md
  AI Local Business Lead Intelligence & Outreach Pipeline.json
  screenshots/
    workflow-overview.png
```

---

## 💡 Engineering Highlights

| Highlight | Where it appears |
|:--|:--|
| Multi-page local business discovery | Page 1 by GET, pages 2 to 12 by one batched POST |
| Website-based contact extraction | Regex email and social extraction with obfuscation handling |
| Cross-source social resolution | Website links compared with Serper search by handle |
| Social confidence levels | HIGH, MEDIUM, LOW per platform |
| Structured email states | `VALID`, `RISKY`, `NOT_FOUND_OR_INVALID`, `NOT_FOUND` |
| Duplicate removal | `website` and `name` before AI calls |
| AI-generated summaries and outreach | Two `llama3` requests per lead |
| Deterministic fallbacks | Template services and summary when parsing fails |
| Rule-based lead scoring | Transparent `N/10` factors |
| Google Sheets persistence | 23-column append-or-update mapping |
| Request pacing | Five Wait nodes and four batch loops |

AI handles services, summaries, and outreach drafts. Discovery, extraction, validation routing, cleaning, scoring, deduplication, and persistence are deterministic JavaScript, HTTP, branching, and Google Sheets logic.

---

## 🏁 Final Takeaway

This project combines local-business discovery, website and social contact intelligence, email validation, AI enrichment, outreach drafting, rule-based lead qualification, and structured Google Sheets persistence in a single n8n workflow. Its value is in how the stages are wired together: deterministic extraction and routing around two focused LLM calls, explicit confidence and status labels, and fallbacks where model output cannot be parsed. It is documented as an inactive workflow export that requires your own credentials, endpoints, and testing before use.
