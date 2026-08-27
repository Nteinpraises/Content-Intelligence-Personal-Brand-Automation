# Content-Intelligence-Personal-Brand-Automation
A daily automation pipeline that scans trending news and topics, extracts and stores key insights in a database, then feeds a website that automatically generates ready-to-publish posts turning raw daily news into personal brand content, hands-free.


# workflow 1 does the daily research brain of the system. Every morning it:

  1. Loads the active brand profile from Supabase and picks the day's audience + content pillar using a rotation that avoids repeating recent combinations (5      audiences × 4 pillars).
  2. Creates (or reuses) a content calendar plan row for the day.
  3. Uses Gemini to generate 5–10 focused research queries tailored to that audience/pillar and Ntein's expertise.
  4. Runs those queries through Tavily web search to pull fresh, real web results with sources.
  5. Extracts sources, dedupes against existing research (by hash), and saves new items to research items.
  6. Loops the top items one at a time (paced 30s apart to respect Gemini's free-tier limits), where Gemini analyzes each and scores it as a content opportunity.
  7. Selects the best-scoring opportunity, marks it on the calendar, and hands off to WF2 to produce the actual content. Logs the run to automation_runs.
  
  
  Output: scored content opportunities in Supabase + an automatic trigger of content production.
