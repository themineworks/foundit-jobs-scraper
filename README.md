# Foundit Jobs Scraper: Salary, Skills & City Search

Scrape Foundit.in (formerly Monster India) job search: title, company, location, experience range in years, salary band in lakhs, employment type, industries, functions, and a skills array. Plain HTTP, no proxy required. Monitor mode bills only for new jobs.

**Run it on Apify:** [apify.com/themineworks/foundit-jobs-scraper](https://apify.com/themineworks/foundit-jobs-scraper)
**Docs, FAQ and pricing:** [themineworks.com/actors/foundit-jobs-scraper](https://themineworks.com/actors/foundit-jobs-scraper/)

**Price:** From $1.50 per 1,000 jobs on Apify's higher plans ($2.50 on the free plan). Failed and empty results are never charged.

## What it returns

* Structured experience (years) and salary (lakhs) as numbers
* Skills returned as an array for demand analysis
* Filter by keyword, city, experience, and salary band
* Monitor mode bills only for new postings since last run
* No login or proxy required

## Quick start

You need a free [Apify account](https://console.apify.com/sign-up) and its API token (Settings, API & Integrations).

### Python

```bash
pip install apify-client
```

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")
run = client.actor("themineworks/foundit-jobs-scraper").call(run_input={
    "searchKeywords": [
        "python developer"
    ],
    "location": "Bangalore",
    "employmentType": "Full Time",
    "postedWithinDays": "7"
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

### Node.js

```bash
npm install apify-client
```

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_TOKEN' });
const run = await client.actor('themineworks/foundit-jobs-scraper').call({
    "searchKeywords": [
        "python developer"
    ],
    "location": "Bangalore",
    "employmentType": "Full Time",
    "postedWithinDays": "7"
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

### cURL

One request that runs the actor and returns the results in the response (for runs under 5 minutes):

```bash
curl -X POST "https://api.apify.com/v2/acts/themineworks~foundit-jobs-scraper/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"searchKeywords": ["python developer"], "location": "Bangalore", "employmentType": "Full Time", "postedWithinDays": "7"}'
```

### Command line

This repo includes ready-made clients that save results to JSON and CSV:

```bash
python3 foundit_jobs_scraper.py --token YOUR_APIFY_TOKEN --search-keywords "python developer" --location "Bangalore" --employment-type "Full Time" --posted-within-days "7"
node foundit_jobs_scraper.mjs --token YOUR_APIFY_TOKEN --search-keywords "python developer" --location "Bangalore" --employment-type "Full Time" --posted-within-days "7"
```

## Input

| Field | Type | Default | Description |
|---|---|---|---|
| `searchKeywords` (required) | array |  | One or more job-search keywords, for example "python developer" or "data analyst" |
| `location` | string |  | City or region, for example 'Bangalore', 'Mumbai', 'Delhi NCR' |
| `employmentType` | string |  | Full-time or part-time roles only |
| `postedWithinDays` | string |  | Only show jobs posted within the last N days |
| `maxJobs` | integer | `5` | Maximum number of jobs to scrape across all keywords |
| `includeJobDescription` | boolean | `false` | If true, fetch each job's detail page (one extra HTTP request per job) for the full description… |
| `monitorMode` | boolean | `false` | Run on a schedule and deliver ONLY results not seen in a previous run |
| `experienceMinYears` | integer |  | Minimum years of experience |
| `experienceMaxYears` | integer |  | Maximum years of experience required for a role to match |
| `salaryMinLakhs` | integer |  | Minimum annual CTC in INR Lakhs |
| `salaryMaxLakhs` | integer |  | Maximum annual CTC in INR Lakhs |

## Output

One row per result, as JSON, CSV, Excel or through the API.

| Field | Type | Description |
|---|---|---|
| `job_id` | string | Foundit internal job ID |
| `title` | string | Job title |
| `company` | string | Company / recruiter-listed name |
| `company_id` | string | Foundit internal company ID |
| `location` | string | Job location(s) as listed on Foundit |
| `experience_min_years` | number | Minimum years of experience required |
| `experience_max_years` | number | Maximum years of experience required |
| `salary_confidential` | boolean | True if the employer marked salary as confidential (Foundit hides salary on most listings) |
| `salary_min_lakhs` | number | Minimum disclosed salary in INR lakhs per annum (null if confidential or not disclosed) |
| `salary_max_lakhs` | number | Maximum disclosed salary in INR lakhs per annum (null if confidential or not disclosed) |
| `employment_type` | string | Full time or Part time |
| `industries` | array | Industry tags |
| `functions` | array | Functional area tags (for example Software Engineering, Sales) |
| `skills` | array | Required / tagged skills |
| `posted_date_text` | string | Raw relative posted-date text from Foundit (for example '3 days ago') |
| `posted_days_ago` | number | Days since the job was posted, computed from the listing's creation timestamp |
| `total_applicants` | number | Number of applicants Foundit reports for this listing |
| `is_urgently_hiring` | boolean | True if Foundit flags this as an urgent hire |
| `description` | string | Full job description HTML/text (only populated when includeJobDescription=true) |
| `responsibilities` | string | Responsibilities text from the job detail page (only when includeJobDescription=true) |
| `qualifications` | string | Qualifications text from the job detail page (only when includeJobDescription=true) |
| `date_posted` | string | Structured posting date from the job detail page's JSON-LD (only when includeJobDescription=true) |
| `valid_through` | string | Structured listing expiry date from the job detail page's JSON-LD (only when includeJobDescription=true) |
| `apply_url` | string | URL to the Foundit job detail / apply page |
| `scraped_at` | string | ISO timestamp when this record was scraped |

## Use it from an AI agent

The actor works as a tool in Claude, Cursor or any MCP client through Apify's MCP server:

```
https://mcp.apify.com/?tools=themineworks/foundit-jobs-scraper
```

## FAQ

### Is this Monster India?

Yes. Monster India rebranded to Foundit; this actor targets foundit.in.

### Do I need a login or a proxy?

Neither. The default proxy setting is off and there is no account requirement.

### What does monitor mode do?

Keeps a record of job IDs already delivered and skips them on later runs, so a scheduled task returns and bills only genuinely new postings.

### Why is salary missing on some jobs?

Indian job boards let employers hide it. Those rows come back with salary_confidential set rather than a fabricated range.

### What does it cost?

Pay per job delivered. See the Pricing tab for the current rate.

### Can I export the results to CSV or Excel?

Yes. Every run saves to an Apify dataset you can download as JSON, CSV, Excel or XML, or read through the API. The Python and Node clients in this repo also write the results to local files.

### Can I run it on a schedule?

Yes. Save your input as a task on Apify and attach a schedule, or call the API from your own cron job. Scheduled runs are billed the same way as manual ones.

## Related scrapers

* [Hirist Jobs Scraper](https://themineworks.com/actors/hirist-jobs-scraper/): India IT jobs across 147 locations, 19 fields
* [Naukri Jobs Scraper](https://themineworks.com/actors/naukri-jobs/): India's largest job board structured as clean JSON
* [Shine.com Jobs Scraper](https://themineworks.com/actors/shine-jobs-scraper/): Times Group India job board, 22 fields, monitor mode

Part of [The Mine Works](https://themineworks.com/): 151 pay-per-result scrapers with no login and no browser setup on your side.

## License

MIT © The Mine Works
