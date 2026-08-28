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

# WF2 — Content Production Engine

The content factory. It receives a calendar/opportunity/audience/pillar and:

Loads the brand profile and the selected opportunity's research and angle.
Uses Gemini to write the actual LinkedIn post draft (hook, body, styled to Ntein's tone and words-to-avoid).
Generates a detailed image prompt as text (saved to visual_prompts with status prompt_ready) so images can be created manually — no image-generation API is used.
Saves the draft to content_drafts, updates the calendar to ready_for_review, and logs the run.
Output: a ready-to-review post draft + an image prompt in Supabase for the Lovable dashboard to display.

# WF3 — Content Management & Learning Engine

The control surface between the Lovable frontend and the system. It receives POST requests with an action and routes to the matching handler:

approve_draft / reject_draft — update draft + calendar status
regenerate_draft — re-trigger WF2 to remake content
reschedule_content — change a post's scheduled date
mark_as_published — record post analytics, then use Gemini to analyze historical performance (top topics, audiences, hooks, formats, weak patterns) and save learning insights back to Supabase
request_visual / retry_visual — queue a visual for (re)generation
save_feedback — store your feedback on a draft
Unknown actions return a clean 400 error.
Every action responds to the frontend and logs to automation_runs.

Output: applied content decisions + a growing set of performance insights that feed back into future content quality.

The loop: WF1 researches → WF2 produces → your frontend reviews/publishes via WF3 → WF3 learns from performance → insights improve the next cycle.
