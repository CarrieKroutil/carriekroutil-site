---
title: "Video Localization: Captions & Spoken-Word"
weight: 80
description: "Captions, subtitles, and dubbing/voiceover — how to make video reach more people across languages and abilities, and the formats engineers actually have to wire up."
lastUpdated: 2026-06-25
stub: true
goDeeper:
  - group: Tools
    title: "MDN — WebVTT API"
    url: "https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API"
    why: "The format you'll most likely implement for web captions and subtitles, explained with real cue syntax and examples."
  - group: Tools
    title: "W3C Internationalization (i18n) Activity"
    url: "https://www.w3.org/International/"
    why: "Where the standards bodies work through the cross-language and text-direction questions that subtitle work runs straight into."
  - group: Tools
    title: "MDN — Adding captions and subtitles to HTML video"
    url: "https://developer.mozilla.org/en-US/docs/Web/Media/Audio_and_video_delivery/Adding_captions_and_subtitles_to_HTML5_video"
    why: "A concrete, copy-along guide to attaching caption tracks to a video element."
---

Video localization is how you make video content reach people who don't share your language or can't hear the audio. It splits into two broad families: **text on screen** (captions, which include sound cues like *[door slams]* for deaf and hard-of-hearing viewers, and subtitles, which assume you can hear but translate the words) and **replacing the spoken audio** (dubbing or voiceover in another language). It matters for the same two reasons as [accessibility]({{< relref "/handbook/engineering-foundations/accessibility-a11y.md" >}}) and [i18n]({{< relref "/handbook/engineering-foundations/internationalization-i18n.md" >}}) combined — reach and inclusion — and it's worth knowing because a huge share of people watch video with the sound off regardless of ability.

When this page is filled in, it'll cover the caption-versus-subtitle distinction, the WebVTT format you'll most often implement on the web, the trade-offs of dubbing versus subtitling (cost, speed, authenticity), open versus closed captions, and the workflow questions — do you translate from a transcript, who reviews quality, how do you sync timing. Until then, the Go Deeper links carry it.

{{< protip >}}
Captions and subtitles are not the same thing, and treating them as one is the classic mistake. Captions assume the viewer can't hear, so they include speaker labels and non-speech sounds; subtitles assume you can hear but don't understand the language. If you're building for accessibility, you need captions — translated subtitles alone leave deaf viewers without the doorbell, the laughter, or the ominous music cue.
{{< /protip >}}
