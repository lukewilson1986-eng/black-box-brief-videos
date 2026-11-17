# The Black Box Brief – production guide

Read this in full before making an episode. Follow it exactly. Luke does not review scripts or videos – make the episode in full and schedule it.

## Channel
- YouTube: The Black Box Brief, @BlackBoxBriefAviation (channel ID UCqwitBryWAxjUP3yqqazDXA), owned by Luke.
- Niche: real air accidents and serious incidents, explained from the official investigation reports (AAIB, NTSB, BEA, TSB, ATSB etc.). Calm, factual, respectful. No sensationalism, no speculation beyond the report, no gore.
- YouTube's 2026 rules demonetise generic/template "AI slop". Every episode must be properly researched from the official report, with its own script and its own explanatory visuals (maps, timelines, diagrams). No stock-footage montages.
- Parked ideas for later (not now): forgotten British history; how everyday things work.

## Schedule
- One full episode every TWO weeks, live **Monday 19:00 Europe/London**.
- Four Shorts cut from each episode, all public at **18:00**: Shorts 1–3 on the **Tuesday, Wednesday and Thursday** straight after the episode (to push it while it's fresh), Short 4 on the **Tuesday of the following week** (the quiet week, so the channel never goes more than about 6 days without a post).
- Cycle: Sunday – make episode + 4 Shorts and schedule them; Monday 19:00 – episode live; Tue/Wed/Thu 18:00 – Shorts 1–3; next Tue 18:00 – Short 4; following Sunday – make the next episode.
- Episode log: `episodes.md` (newest at bottom). Never repeat an incident already in the log.

## Episode spec
- Length: **8–10 minutes** (must be over 8:00 for mid-roll ads). Episode 1 was 8:55.
- 1920x1080, 30 fps, H.264 MP4 (`-movflags +faststart`), AAC audio. Keep the file under 95 MB (GitHub limit is 100 MB per file); re-encode at a higher CRF if needed.
- Structure: cold open (the most dramatic moment, ~20 s) → title card → the flight → what went wrong → the critical minutes → the investigation and findings → what changed afterwards → short outro asking viewers to subscribe.
- Topic choice: pick a well-documented incident with a full, freely available official report. Prefer a mix of eras and countries, and survivable incidents as well as fatal ones. Check `episodes.md` first.

## Brand / visual style
- Background near-black `#0D1016` / `#161B24` with a faint grid `#1E2530`.
- Accent orange `#FF6A13` (dark `#D65006`), text off-white `#F2EFE8`, secondary grey `#78828F`, red only for failures/warnings.
- Fonts: Inter / Inter Display (bold for headlines), DejaVu Sans Mono for data labels (times, altitudes, speeds, callsigns).
- Visual language: flight-data look – animated route maps with the aircraft path, altitude/time readouts, step-by-step timelines, simple flat diagrams of the aircraft/part involved, quote cards for key cockpit/ATC lines from the report (attributed, short).
- Logo: orange flight recorder with reflective stripes and a trace line with one spike.
- Reusable rendering code lives in `engine/` in this repo. If it doesn't exist yet, build it (Python + Pillow + ffmpeg, scenes rendered as frames → MP4 clips, then concatenated) and **commit it to `engine/`** so later runs reuse and improve it rather than starting over.
- Soundtrack: quiet ambient score generated in code, plus simple effects (wind, bang, alarm) where the story needs them. Narration must sit clearly above music (music around −24 dB under voice). No copyrighted music.

## Voiceover (ElevenLabs)
- Voice ID `VvVVKaa2nHZpbZdtSRSX` (the channel voice used for Episode 1). Model `eleven_v4`.
- Split the script into ~5 parts. **Generate ONE take per part** (`generations_count: 1`) – never the default 2. Never re-run a generation to retry; if a part fails, report it.
- Budget: ElevenLabs Starter plan, ~35k credits/month, resets around the 13th. An episode costs ~8,000 credits. Check `account_get_usage` first; if fewer than 9,000 credits remain, do not generate – notify Luke instead.
- Getting the audio into the workspace: get each take's `full_res_url` via `creative_get_generations` and `curl` it. If the workspace can't reach elevenlabsusercontent.com (network blocked), and Luke's computer is reachable via the `mcp__remote-devices__device_bash` tool, download it there into `~/Downloads/bbb_work/` and stage it in with `device_stage_files`. If neither works, stop and notify Luke that network access needs "All domains" in Claude settings.

## Shorts
- Four vertical 1080x1920 Shorts per episode, each 35–55 s, cut from the finished episode's strongest moments (cold open, the critical moment, the cause, plus one more – e.g. what changed afterwards or a striking detail from the investigation). Each Short must use different footage from the others. They reuse the narration, so they cost no credits.
- Layout (as Episode 1): dark gridded background, "THE BLACK BOX BRIEF" in orange mono at the top, a 2–3 line bold headline (last line orange), the 16:9 clip in the middle framed by thin orange bars, and a "Full investigation on the channel" banner with the logo below.
- Title: short punchy hook + ` #shorts`. Description: one or two sentences + `Watch the full investigation: https://youtu.be/<episode id>` + hashtags.

## Hosting and scheduling
1. Commit files to this repo under `epNN/` (e.g. `ep02/BBB_Ep02_<Slug>.mp4`, `ep02/BBB_Ep02_Short1.mp4`, `ep02/BBB_Ep02_thumbnail.jpg`). Push to `main`.
2. Public file link: `https://raw.githubusercontent.com/lukewilson1986-eng/black-box-brief-videos/main/<path>`.
3. Schedule with Metricool (`createScheduledPost`), brand/blogId `8132607`, timezone Europe/London, network `youtube`:
   - Episode: `type: "video"`, `privacy: "public"`, title, full description (summary, chapters with timestamps, sources section naming the official report), tags, `category: "EDUCATION"`, `madeForKids: false`, `videoThumbnailUrl` = the thumbnail's raw link (1280x720 JPG).
   - Shorts: `type: "short"`, same settings, no thumbnail.
4. The Shorts must link to the episode, but its YouTube ID only exists once Metricool uploads it (at 19:00 Monday). So: schedule the episode first; then schedule the four Shorts with `https://www.youtube.com/@BlackBoxBriefAviation` as a placeholder link, and mark the row in `episodes.md` with `SHORTS LINK PENDING`. A separate Monday-evening scheduled task finds the new video's ID (WebFetch `https://www.youtube.com/@BlackBoxBriefAviation/videos`, or the built-in browser on YouTube Studio if Luke's computer is reachable), replaces the placeholder in each of the four pending Shorts with `https://youtu.be/<id>` using `updateScheduledPost`, records the ID in `episodes.md`, and removes the PENDING mark.
5. Confirm with `getScheduledPosts` that all five posts (episode + 4 Shorts) are queued.
6. YouTube's API can't set a Short's "Related video" link (the tappable link to the full episode), so neither Metricool nor these runs can. Links in Shorts descriptions aren't clickable. Until there's an automated route, Luke sets Related video in YouTube Studio after each Short publishes – a daily 18:17 scheduled task ("Related video reminder") checks Metricool and pushes him a reminder on any day a Short goes live.

## Thumbnail
- 1280x720 JPG under 2 MB. Dark brand background, a bold 2–4 word hook in white/orange, a clear graphic of the aircraft or the key part, small logo. Readable at phone size.

## Quality checks before scheduling
- Facts: every date, time, altitude, name and finding checked against the official report.
- Watch it: contact sheet of 8+ frames across the video, check no text overlaps or cut-off labels, no missing glyphs.
- Audio: decode the whole file with ffmpeg without errors; voice clearly audible; no long silences.
- Duration over 8:00.

## After scheduling
- Append the episode to `episodes.md` (number, incident, report, live date, file paths, Metricool post IDs) and commit.
- Send Luke a short summary (push notification): episode title, air date, the four Shorts with dates, credits left.
