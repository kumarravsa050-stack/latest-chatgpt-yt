# YouTube Telegram Downloader — HD quality fix

## What this package changes
- Removes the forced YouTube `player_client` list, which can hide available formats for some videos.
- Uses a clearer yt-dlp format selector for 360p/480p/720p/1080p and Best Available.
- Enables verbose yt-dlp logs and prints requested quality, extracted format details, and the final output file dimensions.
- Prints task errors to Render logs.
- Reads `OWNER_ID` from the Render environment instead of hardcoding an owner ID.
- Keeps FFmpeg installed in the Docker image so separate video/audio streams can be merged.

## Important limits
- A requested resolution is a maximum, not a promise that every video has that resolution. The source video must actually offer that resolution and yt-dlp must be able to access it.
- Telegram cannot increase a 360p source into real 720p/1080p. This code does not upscale video.
- Some YouTube videos may fail due to age/region restrictions, cookies, or YouTube extractor changes. Check Render logs for `[DOWNLOAD]`, `[EXTRACTOR]`, `[OUTPUT FILE]`, and `[TASK ERROR]`.
- Render free services can sleep or have resource/time limits; large HD files and playlists may fail or take a long time.
- Use this only for content you have permission to download.

## Deploy from Android browser
1. Extract this ZIP.
2. Replace the files in your GitHub repository with the files from this folder. Keep the filenames exactly as provided.
3. Commit/push the changes.
4. In Render, open the service and confirm the environment variables exist:
   - `API_ID`
   - `API_HASH`
   - `BOT_TOKEN`
   - `OWNER_ID` (your Telegram numeric user ID)
5. Trigger **Manual Deploy → Deploy latest commit**.
6. Open **Logs → Application logs**.
7. Send a new YouTube URL to the bot and select a quality. Look for `[DOWNLOAD]` and `[OUTPUT FILE]`.
8. `[OUTPUT FILE] ... 1280x720` means the local downloaded file is 720p; `1920x1080` means 1080p. If the file is still 360p, check the `[EXTRACTOR]` / yt-dlp verbose output to see what formats were available.

## Notes
- Do not paste your bot token, API hash, or cookies into public GitHub issues or screenshots.
- The existing bot's access-control behavior is retained. `OWNER_ID` must be set correctly in Render; if left as `0`, only the owner ID is not configured and access behavior may be broader than intended.


## Added commands

- `/live <YouTube_URL>`: downloads a live stream from the beginning when YouTube/yt-dlp exposes the stream; archived completed livestreams can also be downloaded if the replay remains available.
- `/less stream <YouTube_URL> <start> <end>`: downloads only the selected clip. Time values accept seconds, `MM:SS`, or `HH:MM:SS`.
  - Example: `/less stream https://youtu.be/VIDEO_ID 02:13 03:15`
  - Uses the user's default resolution from `/setquality` (720p by default).

Only download videos or clips you own or are authorized to download. Live streams that are private, deleted, region-restricted, or not made available by YouTube cannot be retrieved by the bot.
