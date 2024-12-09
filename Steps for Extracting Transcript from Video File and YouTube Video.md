# Video File

1. Download video to windowwsPS
2. Trasnfer to Linux
3. Run command below

```
whisper "<audio_filename>" --model base
```

# Youtube Video

1. Install yt-dlp

```
pip intstall yt-dlp
```

2. Create audio file from youtube video link

```
yt-dlp -f bestaudio --extract-audio --audio-format mp3 "<youtube_video_url"
```

3. Use the whisper command above

```
whisper "<audio_filename>" --model base
```

# Script to Process All videos

```
#!/bin/bash

# Folder to save audio and transcriptions
OUTPUT_DIR="./output"
mkdir -p "$OUTPUT_DIR"

# YouTube playlist or video links file
VIDEO_LIST="videos.txt"

# Loop through each video link in the file
while IFS= read -r VIDEO_URL; do
  echo "Processing $VIDEO_URL..."
  
  # Extract the video title (used for filenames)
  VIDEO_TITLE=$(yt-dlp --get-title "$VIDEO_URL" | tr -d '/?<>\\:*|\"') # Sanitize title
  
  # Define filenames for MP3 and transcription
  MP3_FILE="$OUTPUT_DIR/${VIDEO_TITLE}.mp3"
  TXT_FILE="$OUTPUT_DIR/${VIDEO_TITLE}.txt"
  
  # Check if the MP3 file already exists
  if [ -f "$MP3_FILE" ]; then
    echo "MP3 file already exists: $MP3_FILE. Skipping download."
  else
    echo "Downloading and extracting audio..."
    yt-dlp -f bestaudio --extract-audio --audio-format mp3 -o "$MP3_FILE" "$VIDEO_URL"
  fi

  # Check if the transcription already exists
  if [ -f "$TXT_FILE" ]; then
    echo "Transcript already exists: $TXT_FILE. Skipping transcription."
  else
    if [ -f "$MP3_FILE" ]; then
      echo "Transcribing $MP3_FILE with Whisper..."
      whisper "$MP3_FILE" --model base --output_dir "$OUTPUT_DIR"
    else
      echo "Error: MP3 file $MP3_FILE does not exist. Skipping transcription."
    fi
  fi
done < "$VIDEO_LIST"

echo "Processing completed. Check $OUTPUT_DIR for results."
```

## create videos.txt file

create a file called `videos.txt` that contains all youtube video links
