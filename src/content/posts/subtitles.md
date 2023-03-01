---
layout: blog-post.njk
bodyClass: "blog-post"

seo:
  title: How to generate subtitles for your PeerTube video
  description: "How to generate subtitles for your PeerTube video"
  canonicalOverwrite: ""

blogTitle: "How to generate subtitles for your PeerTube video"
date: "2023-02-28T12:00:00Z"
author: ""
image: "/assets/images/blog-images/subtitle.png"
featuredBlogpost: true
featuredBlogpostOrder: 1
excerpt: |-
  Adding subtitles to videos is important for accessibility, but also broader engagement with users that might not have sound turned on.
  Luckily, a free, open source tool called Vosk can transcribe subtitles for you!
---

Adding subtitles to videos is important for accessibility, but also broader engagement with users that might not have sound turned on.

Unfortunately, many tools available require paid, expensive, proprietary subscriptions. Luckily, [Vosk](https://alphacephei.com/vosk/), a "speech recognition toolkit," can automatically generate subtitles for you in [`SRT` format](https://en.wikipedia.org/wiki/SubRip), to be added to your PeerTube videos.

> **Note:** The below procedure is technical and requires command line knowledge. If you are not comfortable working in the command line and your video is on urbanists.video, DM [@video@urbanists.social](https://urbanists.social/@video) for help to get your subtitles generated. 🙂

## Download your video

If you have already uploaded your video to PeerTube, download it. That can be done on the video page. Download the lowest quality version since Vosk just analyzes audio.

![Download video dropdown](/assets/images/blog-images/download-video-dropdown.png)

![Download video modal](/assets/images/blog-images/download-video-modal.png)

If the video opens in browser instead of downloading, simply right click the video and press "Save video as..."

![Save video as...](/assets/images/blog-images/save-video-as.png)

## Install dependencies

To install Vosk, you will need pip3. pip3 is bundled with Python 3.

### Install pip3

If you don't have pip3:

#### For macOS

[Install Homebrew](https://brew.sh/), then run:

```bash
brew install python3
```

#### For Windows

You can install python 3 via the Microsoft Store. Alternatively, download an install [from python.org](https://www.python.org/downloads/windows/).

### Install Vosk

Install Vosk with pip3:

```bash
pip3 install vosk
```

### Optional: Download Vosk model

Download the high quality language model for better transcription quality. For English, `vosk-model-en-us-0.42-gigaspeech`.

That can be found here: https://alphacephei.com/vosk/models

Extract it into a directory alongside your video file.

![Vosk model alongside video file in the same folder](/assets/images/blog-images/vosk.png)

## Run it

Run `vosk-transcriber` from the directory with your extracted Vosk model folder and video file:

```bash
vosk-transcriber -i MY_VIDEO.mp4 -t srt -o sub_en.srt -m vosk-model-en-us-0.42-gigaspeech
```

- Replace `MY_VIDEO.mp4` with your video filename.
- You can also use a different language by downloading a different model and setting `-l`, for example, `-l fr` for French.
- You can omit `-m vosk-model-en-us-0.42-gigaspeech` if you didn't download the higher quality model

After a short while you will get the subtitles in an SRT file format. 🎉

## Upload and cleanup

You can upload this `.srt` file via your PeerTube video settings. Once uploaded you can edit the raw file to fix any transcription errors. Alternatively, you can use subtitle editing software such as [subtitleedit](https://www.nikse.dk/subtitleedit/online) or edit with your text editor of choice.

## All done!

That's it! Thanks for taking the time to add subtitles to your video, it really helps. 🥰 Finally, please reach out via Mastodon if you know of a GUI for Vosk that makes this process easier and more accessible!
