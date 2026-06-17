# Project Specification: Deterministic AdSense + SEO + Codebase Intelligence Platform

## 1. Project name

Working name: **PageOps Intelligence**

Alternative names:

* **AdSense SEO Agent**
* **Revenue SEO Copilot**
* **Page Doctor**
* **SiteOps Mother**
* **Content Revenue Intelligence**

The internal orchestrator can be called **Mother**.

---

## 2. Core idea

Build a system that collects monetization, SEO, traffic, technical, and codebase data for a website, joins everything by normalized page URL, analyzes page performance deterministically in Python, and produces prioritized improvement reports.

LLMs are not used for raw analytics. They are used only when helpful:

* explaining deterministic findings;
* generating SEO/title/meta suggestions;
* proposing content improvements;
* creating implementation prompts for stronger models;
* optionally generating code patches;
* summarizing reports for humans.

The platform must keep a clean split between:

* backend data and analysis system;
* model execution layer;
* UI/dashboard;
* codebase patching layer.

---

## 3. Product goals

### 3.1 Main goal

Help the website owner understand:

* which pages earn well;
* which pages have traffic but low revenue;
* which pages rank but underperform in CTR;
* which pages have SEO growth potential;
* which pages are decaying;
* which pages have technical or codebase issues;
* what exact improvements should be made;
* where in the codebase those improvements belong.

### 3.2 Secondary goals

The system should support:

* scheduled daily/weekly reports;
* manual analysis of one URL;
* batch analysis of all important URLs;
* deterministic scoring;
* exportable reports;
* GitHub issue creation;
* optional code patch generation;
* prompts for external stronger models;
* local model execution for privacy-sensitive tasks;
* external model execution for long or complex reasoning.

### 3.3 Non-goals

The system should not:

* auto-click ads;
* simulate ad behavior;
* scrape private Google UI pages when APIs exist;
* blindly change ad placement without review;
* automatically deploy AI-generated changes;
* claim guaranteed SEO or revenue outcomes;
* use the Google Indexing API for normal blog/content pages unless the page type is actually supported;
* use LLM output as financial truth.

---

## 4. High-level architecture

```text
Scheduler / Manual Trigger
        |
        v
Backend API
        |
        +--> Data Collection Layer
        |       - AdSense collector
        |       - Search Console collector
        |       - GA4 collector
        |       - PageSpeed collector
        |       - Sitemap collector
        |       - Crawler
        |       - Codebase scanner
        |
        +--> Normalization Layer
        |       - URL normalization
        |       - canonical URL mapping
        |       - page-to-source-file mapping
        |
        +--> Data Store
        |       - raw API snapshots
        |       - normalized page facts
        |       - daily aggregates
        |       - recommendations
        |       - reports
        |
        +--> Deterministic Analysis Engine
        |       - page scoring
        |       - anomaly detection
        |       - decay detection
        |       - monetization classification
        |       - SEO opportunity detection
        |       - technical SEO checks
        |
        +--> Model Gateway
        |       - local model provider
        |       - external model provider
        |       - model router
        |       - structured JSON validation
        |
        +--> Code Intelligence Layer
        |       - source file mapping
        |       - metadata detection
        |       - schema detection
        |       - internal link graph
        |       - ad placement detection
        |       - patch generation
        |
        +--> Reporting Layer
                - dashboard
                - Markdown report
                - HTML report
                - CSV/XLSX export
                - GitHub issue
                - email/Slack/Telegram
```

---

## 5. Clean backend/UI split

### 5.1 Backend responsibilities

The backend owns:

* authentication to Google APIs;
* scheduled jobs;
* data collection;
* storage;
* URL normalization;
* deterministic analytics;
* report generation;
* model calls;
* codebase scanning;
* patch generation;
* audit logs;
* user/project configuration.

The backend must expose stable APIs to the UI.

### 5.2 UI responsibilities

The UI owns:

* dashboards;
* filters;
* tables;
* charts;
* report reading;
* manual analysis triggers;
* reviewing AI suggestions;
* approving or rejecting actions;
* viewing code patches;
* copying prompts for stronger models;
* configuring schedules and projects.

The UI must not perform analytics directly. It should request computed results from the backend.

### 5.3 Backend stack

Recommended backend stack:

```text
Python 3.12+
FastAPI
Pydantic
SQLAlchemy or SQLModel
DuckDB for MVP analytics
Postgres for production
Redis for queues/cache
Celery, Dramatiq, RQ, or Prefect for jobs
APScheduler for simple scheduling
Polars or Pandas for dataframes
Playwright for rendering/crawling when needed
BeautifulSoup / lxml for static HTML parsing
GitPython or shell git for repository operations
```

### 5.4 UI stack

Recommended UI stack:

```text
Next.js / React
TypeScript
TanStack Query
TanStack Table
ECharts / Recharts / Tremor
Tailwind CSS
shadcn/ui
Monaco editor for prompts and patches
```

The UI should treat backend responses as typed API contracts.

---

## 6. Deterministic-first principle

Everything that can be computed exactly should be computed exactly.

### 6.1 Deterministic tasks

These must be pure Python/business logic, not LLM work:

* API fetching;
* date range handling;
* currency handling;
* URL normalization;
* joining datasets;
* calculating RPM, CTR, CPC, deltas, moving averages;
* classifying numeric thresholds;
* detecting traffic/revenue drops;
* detecting missing title/meta/canonical tags;
* detecting broken links;
* detecting sitemap/index mismatches;
* detecting slow PageSpeed pages;
* detecting missing structured data;
* detecting source files for routes;
* generating CSV/HTML/Markdown tables;
* validating model JSON;
* creating git branches;
* applying simple patches after approval;
* running tests/build/lint.

### 6.2 Model-assisted tasks

Models may be used for:

* explaining why a deterministic pattern matters;
* summarizing page performance;
* rewriting titles;
* rewriting meta descriptions;
* proposing section outlines;
* generating content-refresh briefs;
* suggesting internal anchor text;
* classifying intent when deterministic rules are insufficient;
* generating implementation prompts;
* proposing complex code changes;
* reviewing generated patches.

### 6.3 Model-forbidden tasks

Models must not be trusted for:

* final revenue calculations;
* API result interpretation when raw data is available;
* deciding policy compliance alone;
* deciding whether to deploy;
* silently modifying production code;
* inventing facts about Google policies;
* inventing traffic or revenue numbers;
* making hidden changes without audit logs.

---

## 7. Data sources

## 7.1 AdSense

Purpose:

* revenue by page;
* RPM;
* clicks;
* impressions;
* CTR;
* monetization quality.

Suggested dimensions:

* date;
* page URL;
* country;
* platform/device if available;
* ad unit if useful.

Suggested metrics:

* estimated earnings;
* page views;
* impressions;
* clicks;
* page RPM;
* impression RPM;
* CPC;
* CTR;
* viewability metrics if available.

Store raw API responses before transformation.

Important issue: AdSense page URLs may not perfectly match website URLs. URL normalization and canonical mapping are mandatory.

---

## 7.2 Google Search Console

Purpose:

* search demand;
* SEO visibility;
* CTR opportunities;
* ranking opportunities;
* query/page mapping.

Suggested dimensions:

* page;
* query;
* date;
* country;
* device;
* search appearance, if useful.

Suggested metrics:

* clicks;
* impressions;
* CTR;
* average position.

Use Search Console for:

* pages with high impressions but low CTR;
* pages ranking positions 4–15;
* queries where the page is under-optimized;
* device/country-specific opportunities;
* decay in clicks or impressions.

---

## 7.3 GA4

Purpose:

* user behavior;
* landing page performance;
* engagement;
* conversions;
* traffic source.

Suggested dimensions:

* landingPagePlusQueryString;
* pagePathPlusQueryString;
* sessionSourceMedium;
* country;
* deviceCategory;
* date.

Suggested metrics:

* sessions;
* activeUsers;
* screenPageViews;
* averageSessionDuration;
* engagementRate;
* keyEvents;
* conversions;
* bounce rate if available/configured.

GA4 should not replace AdSense revenue reporting, but it helps explain behavior.

---

## 7.4 PageSpeed Insights / Lighthouse

Purpose:

* technical performance;
* page quality;
* improvement tasks.

Collect both mobile and desktop where practical.

Store:

* performance score;
* accessibility score;
* best practices score;
* SEO score;
* LCP;
* CLS;
* INP/TBT where available;
* opportunities;
* diagnostics.

Use this for top pages only at first, because running every URL daily is unnecessary.

Recommended schedule:

* daily for top 20 revenue pages;
* weekly for top 100 traffic pages;
* manual for selected URLs.

---

## 7.5 Sitemap and crawler

Purpose:

* discover pages;
* detect metadata;
* detect canonical tags;
* detect structured data;
* detect internal links;
* detect broken links;
* detect orphan pages.

Crawler should collect:

* status code;
* canonical URL;
* title;
* meta description;
* H1;
* headings;
* word count;
* internal links;
* outgoing links;
* image alt coverage;
* robots meta;
* structured data JSON-LD;
* Open Graph tags;
* Twitter card tags;
* ad slot markers;
* layout markers;
* last modified from sitemap if available.

Use polite crawling:

* obey rate limits;
* configurable concurrency;
* user-agent setting;
* no aggressive crawling.

---

## 7.6 Codebase scanner

Purpose:

* connect page-level findings to actual files;
* detect reusable components;
* allow patch generation.

Supported project types for MVP:

* Next.js App Router;
* Next.js Pages Router;
* Astro;
* static Markdown/MDX content;
* Hugo/Jekyll-like content directories.

Scanner should detect:

* route files;
* content files;
* metadata exports;
* JSON-LD components;
* ad components;
* layout files;
* sitemap generator;
* robots configuration;
* internal link components;
* image components;
* build/test commands.

---

## 8. URL normalization

This is critical.

### 8.1 Normalization function

Input:

```text
https://www.example.com/article/?utm_source=x&fbclid=y
```

Output:

```text
https://example.com/article/
```

Configurable rules:

```yaml
site:
  canonical_host: "example.com"
  force_https: true
  trailing_slash: true
  remove_query_params:
    - utm_source
    - utm_medium
    - utm_campaign
    - utm_term
    - utm_content
    - fbclid
    - gclid
    - msclkid
  keep_query_params:
    - page
    - q
  lowercase_path: false
  strip_index_html: true
```

### 8.2 URL mapping table

Create a `url_aliases` table:

| source_url           | normalized_url | canonical_url | reason                  |
| -------------------- | -------------- | ------------- | ----------------------- |
| `/post?utm_source=x` | `/post/`       | `/post/`      | tracking params removed |
| `/post/amp/`         | `/post/`       | `/post/`      | AMP canonical           |
| `/index.html`        | `/`            | `/`           | index normalized        |

### 8.3 Join key

All analytics datasets must join on:

```text
project_id + canonical_url + date
```

Never join raw URLs directly.

---

## 9. Database schema

For MVP, use DuckDB or SQLite for local-first analytics. For production, use Postgres.

### 9.1 Core tables

```sql
projects (
  id,
  name,
  base_url,
  canonical_host,
  timezone,
  currency,
  repo_path,
  created_at,
  updated_at
)
```

```sql
data_sources (
  id,
  project_id,
  type,
  name,
  config_json,
  enabled,
  created_at,
  updated_at
)
```

```sql
raw_api_snapshots (
  id,
  project_id,
  source,
  endpoint,
  date_start,
  date_end,
  request_hash,
  response_json,
  fetched_at
)
```

```sql
pages (
  id,
  project_id,
  canonical_url,
  path,
  title,
  meta_description,
  h1,
  canonical_tag,
  status_code,
  indexable,
  word_count,
  source_file,
  content_type,
  first_seen_at,
  last_seen_at,
  updated_at
)
```

```sql
url_aliases (
  id,
  project_id,
  raw_url,
  normalized_url,
  canonical_url,
  source,
  reason,
  created_at
)
```

---

### 9.2 Analytics tables

```sql
adsense_daily (
  project_id,
  date,
  canonical_url,
  country,
  device,
  estimated_earnings,
  page_views,
  impressions,
  clicks,
  page_rpm,
  impression_rpm,
  ctr,
  cpc,
  raw_url,
  created_at
)
```

```sql
gsc_daily (
  project_id,
  date,
  canonical_url,
  query,
  country,
  device,
  clicks,
  impressions,
  ctr,
  average_position,
  raw_url,
  created_at
)
```

```sql
ga4_daily (
  project_id,
  date,
  canonical_url,
  sessions,
  active_users,
  page_views,
  engagement_rate,
  avg_session_duration,
  conversions,
  source_medium,
  country,
  device,
  raw_path,
  created_at
)
```

```sql
pagespeed_runs (
  id,
  project_id,
  canonical_url,
  strategy,
  performance_score,
  accessibility_score,
  best_practices_score,
  seo_score,
  lcp_ms,
  cls,
  inp_ms,
  tbt_ms,
  opportunities_json,
  diagnostics_json,
  fetched_at
)
```

---

### 9.3 Analysis tables

```sql
page_scores (
  project_id,
  canonical_url,
  period_start,
  period_end,
  revenue_score,
  seo_score,
  technical_score,
  opportunity_score,
  risk_score,
  total_score,
  category,
  confidence,
  evidence_json,
  created_at
)
```

```sql
recommendations (
  id,
  project_id,
  canonical_url,
  type,
  priority,
  status,
  title,
  description,
  evidence_json,
  deterministic_reason,
  model_reason,
  proposed_changes_json,
  created_by,
  created_at,
  updated_at
)
```

```sql
reports (
  id,
  project_id,
  report_type,
  period_start,
  period_end,
  title,
  markdown,
  html,
  summary_json,
  created_at
)
```

```sql
code_patches (
  id,
  project_id,
  recommendation_id,
  branch_name,
  files_changed_json,
  diff_text,
  status,
  created_by,
  created_at,
  applied_at
)
```

---

## 10. Analysis categories

Each page gets one primary category and optional tags.

### 10.1 Primary categories

```text
winner
seo_opportunity
under_monetized
traffic_decay
revenue_decay
technical_issue
thin_content
orphan_page
high_value_risk
new_page_watch
low_priority
unknown
```

### 10.2 Tags

```text
high_impressions_low_ctr
position_4_to_15
high_traffic_low_rpm
high_rpm_low_traffic
slow_mobile
missing_meta_description
weak_title
missing_h1
duplicate_title
missing_schema
poor_internal_links
not_in_sitemap
non_200_status
canonical_mismatch
adsense_url_mismatch
content_old
needs_manual_review
```

---

## 11. Deterministic scoring

### 11.1 Base metrics

For each URL and period:

```python
earnings_30d
earnings_prev_30d
earnings_delta_pct
page_views_30d
rpm_30d
site_median_rpm
gsc_clicks_30d
gsc_impressions_30d
gsc_ctr_30d
average_position_30d
ga4_sessions_30d
engagement_rate_30d
pagespeed_mobile_score_latest
word_count
internal_inlinks
```

### 11.2 Revenue score

Example:

```text
revenue_score =
  percentile_rank(earnings_30d) * 0.50
+ percentile_rank(rpm_30d) * 0.30
+ percentile_rank(page_views_30d) * 0.20
```

### 11.3 SEO opportunity score

Example:

```text
seo_opportunity_score =
  impressions_percentile * 0.35
+ position_opportunity * 0.30
+ low_ctr_opportunity * 0.25
+ query_diversity_score * 0.10
```

Where:

```text
position_opportunity:
  position 4-10  => 1.00
  position 11-20 => 0.70
  position 21-50 => 0.30
  otherwise      => 0.00
```

### 11.4 Under-monetized score

Example:

```text
under_monetized_score =
  traffic_percentile * 0.45
+ max(0, site_median_rpm - page_rpm) / site_median_rpm * 0.45
+ engagement_score * 0.10
```

### 11.5 Decay score

Example:

```text
traffic_decay_score =
  drop_pct_30d_vs_prev * volume_weight
```

Only flag decay when:

```text
previous_period_clicks >= minimum_threshold
AND drop_pct <= -20%
```

This avoids noisy alerts on tiny pages.

### 11.6 Technical score

Example:

```text
technical_issue_score =
  slow_mobile_weight
+ missing_metadata_weight
+ broken_status_weight
+ canonical_problem_weight
+ schema_problem_weight
+ internal_link_problem_weight
```

### 11.7 Priority

```text
priority = high if:
  revenue_score high AND issue_score high

priority = medium if:
  seo_opportunity_score high OR under_monetized_score high

priority = low if:
  small page AND weak evidence
```

Priority must be deterministic. The model may explain it, but not assign it freely.

---

## 12. Report types

## 12.1 Daily report

Purpose:

* quick overview;
* new anomalies;
* yesterday vs recent baseline;
* revenue and traffic changes.

Sections:

1. Executive summary.
2. Top revenue pages.
3. Biggest revenue drops.
4. Biggest traffic drops.
5. High-traffic low-RPM pages.
6. High-impression low-CTR pages.
7. Pages needing manual review.
8. Recommended actions.

Schedule:

```text
daily at 06:00 site timezone
```

---

## 12.2 Weekly strategy report

Purpose:

* deeper prioritization;
* SEO/content/code recommendations.

Sections:

1. Weekly summary.
2. Winners to protect.
3. Pages to refresh.
4. Pages to monetize better.
5. Technical SEO issues.
6. Internal link opportunities.
7. Structured data opportunities.
8. Codebase improvement proposals.
9. Suggested backlog.

Schedule:

```text
weekly Monday 06:30
```

---

## 12.3 Per-page report

Purpose:

* deep analysis of one URL.

Sections:

1. Page identity.
2. Revenue trend.
3. Search trend.
4. Traffic behavior.
5. Query opportunities.
6. Metadata audit.
7. Content audit.
8. Technical audit.
9. Codebase mapping.
10. Recommended changes.
11. Optional patch/prompt.

---

## 13. Backend API design

Base path:

```text
/api/v1
```

### 13.1 Projects

```http
GET /projects
POST /projects
GET /projects/{project_id}
PATCH /projects/{project_id}
DELETE /projects/{project_id}
```

### 13.2 Data source configuration

```http
GET /projects/{project_id}/data-sources
POST /projects/{project_id}/data-sources
PATCH /projects/{project_id}/data-sources/{source_id}
POST /projects/{project_id}/data-sources/{source_id}/test
```

### 13.3 Jobs

```http
GET /projects/{project_id}/jobs
POST /projects/{project_id}/jobs/run
GET /projects/{project_id}/jobs/{job_id}
POST /projects/{project_id}/jobs/{job_id}/cancel
```

Example run request:

```json
{
  "job_type": "daily_report",
  "date_start": "2026-06-01",
  "date_end": "2026-06-16",
  "options": {
    "refresh_adsense": true,
    "refresh_gsc": true,
    "refresh_ga4": true,
    "run_pagespeed": false,
    "use_model": false
  }
}
```

### 13.4 Pages

```http
GET /projects/{project_id}/pages
GET /projects/{project_id}/pages/{page_id}
GET /projects/{project_id}/pages/by-url?url=/example/
```

### 13.5 Analysis

```http
POST /projects/{project_id}/analysis/run
GET /projects/{project_id}/analysis/pages
GET /projects/{project_id}/analysis/pages/{page_id}
```

### 13.6 Recommendations

```http
GET /projects/{project_id}/recommendations
GET /projects/{project_id}/recommendations/{recommendation_id}
PATCH /projects/{project_id}/recommendations/{recommendation_id}
POST /projects/{project_id}/recommendations/{recommendation_id}/explain
POST /projects/{project_id}/recommendations/{recommendation_id}/generate-prompt
POST /projects/{project_id}/recommendations/{recommendation_id}/generate-patch
```

### 13.7 Reports

```http
GET /projects/{project_id}/reports
POST /projects/{project_id}/reports/generate
GET /projects/{project_id}/reports/{report_id}
GET /projects/{project_id}/reports/{report_id}/download.md
GET /projects/{project_id}/reports/{report_id}/download.html
GET /projects/{project_id}/reports/{report_id}/download.csv
```

### 13.8 Model gateway

```http
GET /models
POST /models/test
POST /models/complete
POST /models/structured
```

This is mostly internal. UI should usually call higher-level endpoints, not raw model endpoints.

### 13.9 Codebase

```http
POST /projects/{project_id}/codebase/scan
GET /projects/{project_id}/codebase/files
GET /projects/{project_id}/codebase/map-url?url=/example/
POST /projects/{project_id}/codebase/generate-patch
POST /projects/{project_id}/codebase/apply-patch
POST /projects/{project_id}/codebase/create-branch
POST /projects/{project_id}/codebase/run-checks
```

---

## 14. Model gateway

### 14.1 Provider abstraction

Every model provider should implement:

```python
class ModelProvider:
    def complete(self, request: ModelRequest) -> ModelResponse:
        ...

    def structured(self, request: StructuredModelRequest, schema: dict) -> dict:
        ...

    def healthcheck(self) -> ProviderHealth:
        ...
```

### 14.2 Supported providers

MVP providers:

```text
local_ollama
local_vllm
deepseek
openai_compatible_custom
```

Configuration example:

```yaml
model_gateway:
  default_provider: local_ollama

  providers:
    local_ollama:
      type: openai_compatible
      base_url: "http://localhost:11434/v1"
      api_key_env: "OLLAMA_API_KEY"
      default_model: "gemma-4-e4b"

    local_vllm:
      type: openai_compatible
      base_url: "http://localhost:8000/v1"
      api_key_env: "VLLM_API_KEY"
      default_model: "gemma-4-12b"

    deepseek:
      type: openai_compatible
      base_url: "https://api.deepseek.com"
      api_key_env: "DEEPSEEK_API_KEY"
      default_model: "deepseek-v4-flash"
```

### 14.3 Model router

The router decides which model to use.

Example policy:

```yaml
routing:
  default: local_ollama

  rules:
    - name: private_codebase_analysis
      when:
        task_type: code_review
        privacy: high
      provider: local_vllm
      model: gemma-4-12b

    - name: cheap_batch_page_summaries
      when:
        task_type: page_summary
        max_tokens_lt: 4000
      provider: local_ollama
      model: gemma-4-e4b

    - name: long_context_report
      when:
        task_type: weekly_report
        context_tokens_gt: 50000
      provider: deepseek
      model: deepseek-v4-flash

    - name: complex_patch_prompt
      when:
        task_type: code_patch
        difficulty: high
      provider: deepseek
      model: deepseek-v4-pro
```

### 14.4 Structured output only

Every model call should return validated JSON.

Bad:

```text
The page is doing badly and maybe you should improve it.
```

Good:

```json
{
  "diagnosis": "High impressions but low CTR.",
  "confidence": 0.78,
  "actions": [
    {
      "type": "title_rewrite",
      "priority": "high",
      "proposal": "Example title",
      "evidence_keys": ["gsc_impressions_30d", "gsc_ctr_30d"]
    }
  ]
}
```

### 14.5 Validation

Every model response goes through:

```text
JSON parse
    ↓
Pydantic schema validation
    ↓
evidence key validation
    ↓
policy validation
    ↓
save or reject
```

If validation fails:

* retry once with validation error;
* then mark model output as failed;
* never silently accept malformed output.

---

## 15. Mother orchestrator

Mother is the top-level planner/executor.

Mother does not replace deterministic jobs. It coordinates them.

### 15.1 Responsibilities

Mother can:

* run analysis jobs;
* select relevant deterministic findings;
* decide whether model assistance is useful;
* call model gateway;
* generate human-readable reports;
* generate code patch plans;
* generate prompts for stronger models;
* open GitHub issues;
* create branches;
* create patches;
* run checks;
* prepare PR descriptions.

Mother cannot:

* deploy automatically;
* edit production directly;
* bypass tests;
* bypass policy checks;
* override deterministic metrics;
* invent data.

### 15.2 Modes

```text
report_only
prompt_only
patch_draft
patch_apply_local
github_issue
pull_request
```

### 15.3 Mode behavior

#### report_only

Mother creates a report only.

No model required.

#### prompt_only

Mother creates a strong prompt package for another model.

Package contains:

* page data;
* relevant code files;
* deterministic findings;
* constraints;
* expected output format;
* safety rules;
* test commands.

#### patch_draft

Mother generates a patch but does not apply it.

Output:

* diff;
* explanation;
* files touched;
* risk level;
* test instructions.

#### patch_apply_local

Mother applies patch to a new branch only after approval.

Steps:

```text
create branch
apply patch
run formatter
run lint
run tests/build
save diff
mark patch ready for review
```

#### github_issue

Mother creates a GitHub issue with deterministic evidence and recommendations.

#### pull_request

Mother opens a PR only after explicit approval.

---

## 16. Code change strategy

### 16.1 Safe deterministic code changes

These can often be automated with templates:

* update markdown frontmatter title;
* update markdown frontmatter description;
* add canonical field if framework supports it;
* add missing sitemap entry;
* fix obvious internal broken link if exact replacement exists;
* add internal link to known related page in markdown;
* add generated report file;
* update JSON config.

### 16.2 Model-assisted code changes

These should be reviewed:

* changing React/Next.js layouts;
* changing ad placement;
* adding structured data components;
* changing content significantly;
* rewriting article sections;
* modifying route logic;
* modifying sitemap generation logic;
* adding tests.

### 16.3 Never auto-apply

Do not auto-apply:

* ad density increases;
* anything that may encourage accidental clicks;
* hidden text/content;
* fake schema;
* misleading title/meta;
* cloaking-like behavior;
* mass noindex changes;
* canonical changes across many URLs;
* redirects;
* robots.txt changes;
* production deployment.

---

## 17. Prompt package for stronger model

When Mother creates a prompt for a stronger model, it should include:

```text
1. Objective
2. Website/framework context
3. Page URL
4. Deterministic findings
5. Relevant metrics
6. Relevant code files
7. Allowed changes
8. Forbidden changes
9. Output format
10. Tests to preserve
11. Risk notes
```

Example prompt package structure:

```json
{
  "task": "Improve metadata and structured data for one page.",
  "url": "/best-tools/",
  "framework": "Next.js App Router",
  "source_files": [
    {
      "path": "app/best-tools/page.tsx",
      "content": "..."
    },
    {
      "path": "components/SeoJsonLd.tsx",
      "content": "..."
    }
  ],
  "deterministic_findings": {
    "gsc_impressions_30d": 42000,
    "gsc_ctr_30d": 0.011,
    "average_position_30d": 8.4,
    "adsense_rpm_30d": 1.2,
    "site_median_rpm_30d": 5.8,
    "title_length": 82,
    "meta_description_missing": true
  },
  "allowed_changes": [
    "Rewrite title",
    "Add meta description",
    "Add Article JSON-LD if content supports it",
    "Add 2 internal links to known related pages"
  ],
  "forbidden_changes": [
    "Do not increase ad density",
    "Do not add fake FAQ schema",
    "Do not change route path",
    "Do not remove existing analytics"
  ],
  "required_output": {
    "format": "unified_diff",
    "include_explanation": true,
    "include_risk_level": true
  }
}
```

---

## 18. UI specification

## 18.1 Main pages

### Dashboard

Shows:

* total revenue;
* revenue delta;
* search clicks;
* impressions;
* average CTR;
* top winners;
* top problems;
* open recommendations;
* latest reports.

### Pages table

A powerful sortable/filterable table.

Columns:

```text
URL
Category
Priority
Revenue 30d
Revenue delta
RPM
Page views
GSC clicks
GSC impressions
CTR
Avg position
GA sessions
PageSpeed mobile
Source file
Open recommendations
```

Filters:

```text
category
priority
revenue range
traffic range
position range
CTR range
has source file
has recommendations
technical issue
date range
```

### Page detail

Tabs:

```text
Overview
Revenue
Search
Traffic
Technical SEO
Content
Codebase
Recommendations
History
```

### Recommendations

Shows all recommendations with:

* priority;
* type;
* evidence;
* status;
* owner;
* generated action;
* patch/prompt buttons.

Statuses:

```text
new
reviewing
accepted
rejected
in_progress
patched
merged
done
ignored
```

### Reports

Shows:

* daily reports;
* weekly reports;
* per-page reports;
* exports.

### Codebase

Shows:

* route mapping;
* detected framework;
* components;
* ad components;
* metadata files;
* schema files;
* recent generated patches.

### Model settings

Shows:

* providers;
* model names;
* API base URLs;
* local health status;
* routing rules;
* cost controls;
* privacy controls.

### Schedules

Shows:

* daily report schedule;
* weekly report schedule;
* PageSpeed scan schedule;
* codebase scan schedule;
* manual run button.

---

## 19. API response examples

### 19.1 Page analysis response

```json
{
  "canonical_url": "https://example.com/best-tools/",
  "category": "seo_opportunity",
  "priority": "high",
  "scores": {
    "revenue_score": 0.72,
    "seo_opportunity_score": 0.91,
    "technical_score": 0.67,
    "risk_score": 0.18,
    "total_score": 0.83
  },
  "metrics": {
    "adsense": {
      "earnings_30d": 42.1,
      "rpm_30d": 18.4,
      "page_views_30d": 2290
    },
    "gsc": {
      "clicks_30d": 1220,
      "impressions_30d": 34000,
      "ctr_30d": 0.036,
      "average_position_30d": 7.8
    },
    "ga4": {
      "sessions_30d": 2900,
      "engagement_rate_30d": 0.61
    },
    "technical": {
      "pagespeed_mobile": 71,
      "title_length": 64,
      "meta_description_missing": false
    }
  },
  "recommendations": [
    {
      "type": "title_meta",
      "priority": "high",
      "deterministic_reason": "High impressions and average position 7.8 with CTR below expected range.",
      "model_available": true
    }
  ]
}
```

### 19.2 Recommendation response

```json
{
  "id": "rec_123",
  "canonical_url": "https://example.com/best-tools/",
  "type": "title_meta",
  "priority": "high",
  "status": "new",
  "title": "Improve title and meta description",
  "evidence": {
    "gsc_impressions_30d": 34000,
    "gsc_ctr_30d": 0.036,
    "average_position_30d": 7.8
  },
  "deterministic_reason": "The page is already ranking on page one for many queries but receives weaker CTR than expected.",
  "proposed_changes": {
    "title_candidates": [],
    "meta_description_candidates": []
  },
  "actions": [
    "explain_with_model",
    "generate_prompt",
    "generate_patch"
  ]
}
```

---

## 20. Jobs

## 20.1 Daily job

```text
daily_report_job(project_id, date)
```

Steps:

```text
1. Fetch AdSense for yesterday and rolling 30 days.
2. Fetch Search Console for last 30/90 days.
3. Fetch GA4 for last 30/90 days.
4. Normalize URLs.
5. Update page aggregates.
6. Run deterministic scoring.
7. Generate recommendations.
8. Generate Markdown/HTML report.
9. Send notification.
```

Default model usage:

```text
off
```

Optional model usage:

```text
only summarize top 10 findings
```

---

## 20.2 Weekly job

```text
weekly_strategy_job(project_id, week_start, week_end)
```

Steps:

```text
1. Refresh AdSense/GSC/GA4.
2. Crawl sitemap.
3. Crawl top pages.
4. Run PageSpeed on selected pages.
5. Scan codebase.
6. Map URLs to files.
7. Run deterministic scoring.
8. Generate recommendations.
9. Use model for strategic summary if enabled.
10. Create weekly report.
```

---

## 20.3 Manual URL analysis

```text
analyze_url(project_id, url)
```

Steps:

```text
1. Normalize URL.
2. Fetch latest stored metrics.
3. Optionally refresh live data.
4. Crawl page.
5. Map to source file.
6. Run deterministic checks.
7. Generate per-page report.
8. Optionally call model for recommendations.
9. Optionally generate prompt or patch.
```

---

## 21. Repository structure

Recommended monorepo:

```text
pageops/
  backend/
    app/
      main.py
      api/
        routes_projects.py
        routes_jobs.py
        routes_pages.py
        routes_reports.py
        routes_recommendations.py
        routes_codebase.py
        routes_models.py
      core/
        config.py
        logging.py
        security.py
        errors.py
      db/
        models.py
        session.py
        migrations/
      collectors/
        adsense.py
        search_console.py
        ga4.py
        pagespeed.py
        sitemap.py
      crawler/
        fetcher.py
        parser.py
        robots.py
      normalization/
        urls.py
        canonical.py
      analysis/
        scoring.py
        categories.py
        anomalies.py
        opportunities.py
        technical_seo.py
      codebase/
        scanner.py
        framework_detect.py
        route_mapper.py
        patcher.py
        prompts.py
      models/
        gateway.py
        providers/
          ollama.py
          vllm.py
          deepseek.py
          openai_compatible.py
        schemas.py
        router.py
      reports/
        markdown.py
        html.py
        csv.py
      jobs/
        scheduler.py
        daily.py
        weekly.py
        manual.py
      tests/
    pyproject.toml

  ui/
    app/
      dashboard/
      pages/
      recommendations/
      reports/
      codebase/
      settings/
    components/
    lib/
      api.ts
      types.ts
    package.json

  packages/
    shared-schemas/
      openapi.json

  docker/
    docker-compose.yml

  docs/
    architecture.md
    api.md
    model-routing.md
    codebase-scanning.md
```

---

## 22. Configuration

Example project config:

```yaml
project:
  name: "Example Site"
  base_url: "https://example.com"
  canonical_host: "example.com"
  timezone: "Europe/Berlin"
  currency: "EUR"
  repo_path: "/repos/example-site"

url_normalization:
  force_https: true
  trailing_slash: true
  strip_www: true
  lowercase_host: true
  lowercase_path: false
  remove_query_params:
    - utm_source
    - utm_medium
    - utm_campaign
    - utm_term
    - utm_content
    - fbclid
    - gclid
    - msclkid

analysis:
  min_clicks_for_decay: 50
  min_earnings_for_revenue_alert: 5.0
  high_impressions_threshold: 1000
  low_ctr_multiplier: 0.65
  position_opportunity_min: 4
  position_opportunity_max: 20

pagespeed:
  enabled: true
  daily_top_revenue_pages: 20
  weekly_top_traffic_pages: 100

models:
  enabled_by_default: false
  require_approval_for_external: true
  privacy_default: "local_first"

codebase:
  allow_patch_generation: true
  allow_patch_apply: false
  require_git_clean: true
  default_branch_prefix: "pageops/"
  test_commands:
    - "npm run lint"
    - "npm run build"
```

---

## 23. Security and privacy

### 23.1 Secrets

Store secrets in:

* `.env` for local dev;
* Docker secrets;
* Vault/1Password/Bitwarden/Cloud secret manager for production.

Never store API keys in reports.

### 23.2 Data privacy

Default policy:

```text
metrics can go to external models only if enabled;
source code goes to external models only if explicitly approved;
raw API responses are not sent to models unless needed;
private config/secrets are never sent to models.
```

### 23.3 Model redaction

Before model calls:

* strip secrets;
* strip `.env`;
* strip tokens;
* strip private keys;
* limit source files to relevant snippets;
* include only necessary analytics;
* log what was sent.

### 23.4 Audit logs

Log:

* job runs;
* API fetches;
* model calls;
* reports generated;
* recommendations created;
* prompts generated;
* patches generated;
* patches applied;
* approvals;
* errors.

---

## 24. Policy guardrails

### 24.1 AdSense guardrails

The system should flag but not automatically apply:

* ad layout changes;
* ad density increases;
* ads near deceptive elements;
* ads that could cause accidental clicks;
* misleading headings around ads;
* content that might violate publisher policies.

### 24.2 SEO guardrails

The system should block recommendations involving:

* hidden text;
* keyword stuffing;
* fake schema;
* irrelevant structured data;
* misleading titles;
* doorway pages;
* cloaking;
* mass AI content without review;
* fake reviews;
* false freshness updates.

### 24.3 Code guardrails

The system should require review for:

* redirects;
* canonical changes;
* robots.txt changes;
* noindex changes;
* sitemap generator changes;
* ad component changes;
* layout-wide changes.

---

## 25. Testing strategy

### 25.1 Unit tests

Test:

* URL normalization;
* API response parsing;
* metric calculations;
* scoring;
* category assignment;
* JSON schema validation;
* prompt generation;
* patch application.

### 25.2 Integration tests

Test:

* collector to database;
* database to analysis;
* analysis to report;
* codebase scan to URL mapping;
* model response validation.

### 25.3 Golden tests

Use fixed input datasets and expected outputs.

Examples:

```text
sample_adsense.csv
sample_gsc.csv
sample_ga4.csv
sample_pagespeed.json
sample_crawl.html
expected_page_scores.json
expected_recommendations.json
```

### 25.4 Patch tests

For code changes:

```text
1. Create temporary git repo.
2. Apply patch.
3. Run formatter.
4. Run lint/build/test.
5. Verify expected file changes.
6. Verify no forbidden files changed.
```

---

## 26. MVP roadmap

### Phase 1: Deterministic analytics MVP

Build:

* project config;
* AdSense collector;
* Search Console collector;
* URL normalization;
* DuckDB storage;
* page-level joined table;
* deterministic scoring;
* Markdown daily report;
* CLI.

No UI required yet.

Output:

```bash
pageops run daily --project example
pageops report latest --format markdown
```

---

### Phase 2: UI MVP

Build:

* FastAPI backend;
* Next.js dashboard;
* pages table;
* page detail view;
* reports view;
* manual run button.

No patch generation yet.

---

### Phase 3: SEO/crawler layer

Build:

* sitemap fetcher;
* crawler;
* metadata audit;
* internal link graph;
* PageSpeed integration;
* technical recommendations.

---

### Phase 4: Model gateway

Build:

* local Ollama provider;
* local vLLM provider;
* DeepSeek provider;
* structured JSON validation;
* model router;
* per-page explanation;
* title/meta suggestions;
* report summaries.

Default: model disabled unless requested.

---

### Phase 5: Codebase intelligence

Build:

* framework detector;
* URL-to-source mapper;
* metadata scanner;
* schema scanner;
* ad component detector;
* prompt package generator.

---

### Phase 6: Patch generation

Build:

* prompt-only mode;
* patch draft mode;
* local patch apply mode;
* git branch creation;
* test command runner;
* PR/issue integration.

---

## 27. First implementation milestone

The very first milestone should be boring and reliable:

```text
Input:
  AdSense data
  Search Console data
  sitemap URLs

Output:
  One joined page-performance table
  One daily Markdown report
  One CSV export
```

Do not start with agents.

Start with:

```text
collect -> normalize -> join -> score -> report
```

Then add models.

---

## 28. Example CLI

```bash
pageops init
pageops sources test
pageops collect adsense --days 30
pageops collect gsc --days 90
pageops collect ga4 --days 30
pageops crawl sitemap
pageops analyze --period 30d
pageops report daily
pageops ui
```

Manual URL:

```bash
pageops analyze-url https://example.com/best-tools/
pageops explain-url https://example.com/best-tools/ --model local
pageops prompt-url https://example.com/best-tools/ --target deepseek-v4-pro
pageops patch-url https://example.com/best-tools/ --mode draft
```

---

## 29. Success criteria

The project is successful when it can answer:

1. Which pages made the most money in the last 30 days?
2. Which pages lost the most money compared with the previous 30 days?
3. Which high-traffic pages earn below the site median RPM?
4. Which pages have high impressions but weak CTR?
5. Which pages rank between positions 4 and 15 and deserve SEO work?
6. Which pages are slow or technically weak?
7. Which pages have missing/weak metadata?
8. Which pages are not mapped to source files?
9. Which recommendations are worth doing first?
10. Which code files need to change?
11. Can a patch or stronger-model prompt be produced safely?

---

## 30. Final principle

The platform should behave like this:

```text
Python computes the truth.
Rules decide priority.
Models explain and propose.
Humans approve.
Mother coordinates.
Code changes are reviewed.
Nothing risky ships automatically.
```
