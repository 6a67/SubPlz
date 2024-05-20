# SubPlz🫴: Get Incredibly Accurate Subs for Anything


https://user-images.githubusercontent.com/32607317/219973521-5a5c2bf2-4df1-422b-874c-5731b4261736.mp4


Generate accurate subtitles from audio, align existing subs to videos, generate your own Kindle's Immersion Reading like audiobook subs.

This tool allows you to use AI models to generate subtitles from only audio, then match the subtitles to an accurate text, like a book. You can also just generate subtitles for videos with it, without needing any existing subtitles. Soon, it will support syncronizing existing subs as well. Currently I am only developing this tool for Japanese use.

It requires a modern GPU with decent VRAM, CPU, and RAM. There's also a communty built Google Colab notebook available on discord.

Current State: The transcript will be extremely accurate. The timings will be mostly accurate, but may come late or leave early. Accuracy has improved tremendously with the latest updates to the AI tooling used.

Support for this tool can be found [on KanjiEater's thread](https://discord.com/channels/617136488840429598/1076966013268148314)  [on The Moe Way Discord](https://learnjapanese.moe/join/)

Support for any tool by KanjiEater can be found [on KanjiEater's Discord](https://discord.com/invite/agbwB4p)


# Install

Currently supports Docker (preferred), Windows, and unix based OS's like Ubuntu 22.04 on WSL2. Primarily supports Japanese, but other languages may work as well with limited dev support.

## Running from Docker

## Setup from source

1. Install `ffmpeg` and make it available on the path

1. `git clone https://github.com/kanjieater/SubPlz.git`

2. Use python >= `3.11.2` (latest working version is always specified in `pyproject.toml`)

3. `pip install .`

4. You can get a full list of cli params from `subplz sync -h `
5. If you're using a single file for the entire audiobook with chapters you are good to go. If an file with audio is too long it may use up all of your RAM. You can use the docker image  [`m4b-tool`](https://github.com/sandreas/m4b-tool#installation) to make a chaptered audio file. Trust me, you want the improved codec's that are included in the docker image. I tested both and noticed a huge drop in sound quality without them. When lossy formats like mp3 are transcoded they lose quality so it's important to use the docker image to retain the best quality if you plan to listen to the audio file.



# How to use

## Quick Guide

1. Put an `m4b` (or any other audio/video file) and a `txt` or `epub` file in a folder. The `-d` parameter can multiple audiobooks to process like: `subplz sync -d "/mnt/d/sync/Harry Potter 1/" "/mnt/d/sync/Harry Potter 2 The Spooky Sequel/"`
```bash
/sync/
└── /Harry Potter 1/
   ├── Im an audio file.m4b
   └── Harry Potter.epub
└── /Harry Potter 2 The Spooky Sequel/
   ├── Harry Potter 2 The Spooky Sequel.mp3
   └── script.txt
```
2. Run `subplz sync -d "<full folder path>"` like `/mnt/d/sync/Harry Potter 1"`
2. From there, use a [texthooker](https://github.com/Renji-XD/texthooker-ui) with something like [mpv_websocket](https://github.com/kuroahna/mpv_websocket) and enjoy Immersion Reading.

## Note
- This can be GPU intense, RAM intense, and CPU intense script part. `subplz sync -d "<full folder path>"` eg `subplz sync -d "/mnt/d/Editing/Audiobooks/かがみの孤城/"`. This runs each file to get a character level transcript. It then creates a sub format that can be matched to the `script.txt`. Each character level subtitle is merged into a phrase level, and your result should be a `<name>.srt` file. The video or audio file then can be watched with `MPV`, playing audio in time with the subtitle.
- Users with a modern CPU with lots of threads won't notice much of a difference between using CUDA/GPU & CPU

# Overwrite
By default the tool will overwrite any existing srt named after the audio file's name. If you don't want it to do this you must explicitly tell it not to.

`subplz sync -d "/mnt/v/somefolder" --no-overwrite`

# Only Running for the Files It Needs
SubPlz writes a file in the same folder to the audio with the `<audiofile>.subplz` extension. It starts with a `.` so you may need to enable hidden files if you want to see it. This ensures that subplz runs once and only once per directory for your content. If you want to rerun the SubPlz syncing, delete the file.


# Split m4b by chapter
`./split.sh "/mnt/d/Editing/Audiobooks/かがみの孤城/"`

# Get a subtitle with synced transcript from split files
`subplz sync -d "/mnt/d/Editing/Audiobooks/かがみの孤城/"`

`subplz sync -d "<full folder path>"` eg `subplz sync -d "$(wslpath -a "D:\Editing\Audiobooks\かがみの孤城\\")"` or `subplz sync -d "/mnt/d/sync/Harry Potter 1/" "/mnt/d/sync/Harry Potter The Sequel/"`

# Generate subs for a folder of video or audio file
`python gen.py -d "/mnt/u/Videos/J-Shows/MAG Net/"`

# Merge split files into a single m4b
`./merge.sh "/mnt/d/Editing/Audiobooks/ｍｅｄｉｕｍ霊媒探偵城塚翡翠"`

# Merge split files into a single m4b for a library

This assumes you just have mp4's in a folder like `/mnt/d/Editing/Audiobooks/ｍｅｄｉｕｍ霊媒探偵城塚翡翠`. It will run all of the folder's with mp4's and do a check on them after to make sure the chapters line up. Requires `docker` command to be available.

`python ./helpers/merge.py "/mnt/d/Editing/Audiobooks/"`


# What does "bad" look like using the stable-ts library?

At this point I would recommend reading from the texthooker instead of a sub. (CTRL+SHIFT+RIGHT in mpv to set offset as the next sub). Then you can see the next line coming in the texthooker, and not be distracted by subtitle jumps.

Update: The timing is much more accurate, but it still makes sense to show what going wrong could look like

https://user-images.githubusercontent.com/32607317/219973663-7fcac162-b162-4a02-839c-0be2385f6166.mp4





# Anki Support

- Generates subs2srs style deck
- Imports the deck into Anki automatically

The Anki support currently takes your m4b file in `<full_folder_path>` named `<name>.m4b`, where `<name>` is the name of the media, and it outputs srs audio and a TSV file that can is sent via AnkiConnect to Anki. This is useful for searching across [GoldenDict](https://www.youtube.com/playlist?list=PLV9y64Yrq5i-1ztReLQQ2oyg43uoeyri-) to find sentences that use a word, or to merge automatically with custom scripts (more releases to support this coming hopefully).


1. Install ankiconnect add-on to Anki.
2. I recommend using `ANKICONNECT` as an environment variable. Set `export ANKICONNECT=localhost:8755` or `export ANKICONNECT="$(hostname).local:8765"` in your `~/.zshrc` or bashrc & activate it.
3. Make sure you are in the project directory `cd ./AudiobookTextSync`
4. Install `pip install ./requirements.txt` (only needs to be done once)
5. Set `ANKI_MEDIA_DIR` to your anki profile's media path: `/mnt/f/Anki2/KanjiEater/collection.media/`
6. Run the command below



Command:
`./anki.sh "<full_folder_path>"`

Example:
`./anki.sh "/mnt/d/sync/kokoro/"`




# WSL2

If you're using WSL2 there a few networking quirks.

1. Enable WSL2 to talk to your Windows machine. https://github.com/microsoft/WSL/issues/4585#issuecomment-610061194
2. Set your `$ANKICONNECT` url to your windows machine url, `export ANKICONNECT="http://$(hostname).local:8765"`. https://github.com/microsoft/WSL/issues/5211
3. Make sure inside of Anki's addon config `"webBindAddress": "0.0.0.0", "webBindPort": "8765"`. `0.0.0.0` binds to all network interfaces, so WSL2 can connect.

# Testing connection to Anki from WSL2

```
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{ "action": "guiBrowse", "version": 6, "params": { "query": "flag:3 is:new -is:suspended -tag:重複 tag:重複3" } }' \
  http://172.18.224.1:8765
```
# Troubleshooting
You might see various issues while trying this out in the early state. Here are some of the pieces at work in sequence:
## Stages
1. Filter down audio to improve future results - slow & probably not heavy cpu or gpu usage. Heavier on cpu
2. split_run & stable-ts: Starts off heavy on CPU & RAM to identify the audio spectrum
3. stable-ts: GPU heavy & requires lots of vRAM depending on the model. This is the part with the long taskbar, where it tries to transcribe a text from the audio. Currently the default is [tiny](https://github.com/openai/whisper#available-models-and-languages). Ironically tiny, does a better job of keeping the phrases short, at the cost of accuracy of transcription, which since we are matching a script, doesn't matter. Also it runs 32x faster than large.
4. Merge vtt's for split subs
5. Split the script
6. match the script to the generated transcription to get good timestamps

# Getting Book Scripts

Books now have furigana automatically escaped in txt and epub. You can use calibre though to export them in appropriate formats.

This program supports `txt` files. You may need to use an external program like Calibre to convert your kindle formats like `azw3` to a `txt` of `epub` file.

To convert in Calibre:
1. Right click on the book and convert the individual book (or use the batch option beneath it)
![image](https://user-images.githubusercontent.com/32607317/226463043-f2f89382-a75f-48ea-bb91-00efe0f05893.png)
2. At the top right for output format, select `txt`
![image](https://user-images.githubusercontent.com/32607317/226463797-1c19385d-c6e7-4564-a795-926e04716562.png)
3. Click Find & Replace. If your book has 《》for furigana as some aozora books do (戦場《せんじょう》), then add a regex. If they have rt for furigana use the rt one: `《(.+?)》` or `<rt>(.*?)<\/rt>`. When you copy the regex into the regex box, don't forget to click the Add button
![image](https://user-images.githubusercontent.com/32607317/226463912-48bcfd57-4935-48fb-af7e-13d2a024cdee.png)
4. You can add multiple regexes to strip any extra content or furigana as need be.
![image](https://user-images.githubusercontent.com/32607317/226464346-a752970e-0f1c-42db-b64d-a3bc6df6ebdd.png)
5. Click ok and convert it & you should now be able to find the file wherever Calibre is saving your books

# Thanks

Besides the other ones already mentioned & installed this project uses other open source projects subs2cia, & anki-csv-importer

https://github.com/gsingh93/anki-csv-importer

https://github.com/kanjieater/subs2cia

https://github.com/ym1234/audiobooktextsync
