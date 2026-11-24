# Media jobs queue (for Claude)

This folder is the job queue. Muse (the fulfiller) checks it every 2 minutes and makes the media. No MCP server or tunnel is needed for this flow.

## To send a job

Create ONE file per job: `jobs/<job_id>.json`

- `job_id`: `video-` + 12 random lowercase hex characters for a video, `image-` + 12 random lowercase hex characters for an image. Example: `video-81ab4f562b5f`. Never reuse a job_id.
- `type`: `"video"` or `"image"`.
- `prompt`: the exact prompt, as written. Do not change it after creating the file.
- `aspect_ratio`: `"9:16"` (always vertical).
- `duration`: `10` for video (seconds). Omit or `null` for image.
- `status`: `"queued"`.
- `created_at` / `updated_at`: current UTC time in ISO format, e.g. `2026-11-25T01:30:00+00:00`.
- `error`: `null`.
- `output_file`: `null`.
- `github_url`: `null`.

Example (video):

```json
{
  "job_id": "video-81ab4f562b5f",
  "type": "video",
  "prompt": "Lord Hanuman as a cute, expressive 3D-animated family-film character with soft stylized lighting, big warm eyes, golden-orange fur, small crown and gada, leaping joyfully through a glowing sunrise sky toward the sun, flowing clouds, vibrant colors, cinematic camera follow, heartwarming and respectful tone",
  "aspect_ratio": "9:16",
  "duration": 10,
  "status": "queued",
  "created_at": "2026-11-25T01:30:00+00:00",
  "updated_at": "2026-11-25T01:30:00+00:00",
  "error": null,
  "output_file": null,
  "github_url": null
}
```

Rules:

- Videos are 10 seconds, vertical 9:16, muted natural colors.
- Send at most 50 jobs in one batch (one file per job).
- Never edit a job file after creating it. Never delete a job file.
- Never write to `outputs/` — the fulfiller owns that folder.

## To check a job

Read `jobs/<job_id>.json` again.

- `status: "queued"` — still waiting, check again in 2 minutes.
- `status: "done"` — finished. `github_url` is the direct download link (fast CDN). `output_file` is the filename in `outputs/`.
- `status: "failed"` — could not be made. Read `error` for the reason. Do not resend the same job; send a new one with a new job_id if needed.

Finished files are in `outputs/<job_id>.<ext>`, downloadable at `https://raw.githubusercontent.com/Chirag862/muse-media-outputs/main/outputs/<job_id>.<ext>`.
