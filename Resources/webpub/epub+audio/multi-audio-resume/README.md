# Multi-audio narration resume regression fixture

A synthetic, copyright-clear WebPub for restoring narrated EPUB bookmarks when
several audio files share one XHTML resource. Text and audio were authored for
these tests. Three quiet generated tones provide real 12-second MP3 timelines.

| Audio file | Time (seconds) | Text target |
| --- | --- | --- |
| first.mp3 | 0-10 | chapter.xhtml#first |
| first.mp3 | 10-12 | chapter.xhtml#page |
| second.mp3 | 0-12 | chapter.xhtml#second |
| third.mp3 | 0-12 | chapter.xhtml#third |

The track clocks overlap, while every text anchor identifies one audio file.
A bookmark four seconds into `second` or `third` must restore that track and
its time, even though the first track contains a cue at the same time and a later
page-break cue in the same XHTML file. The XHTML duration is 36 seconds, so using
it to scale an audio track's progression also gives an incorrect restore time.

The integration regression saves the emitted locator, closes the publication,
then reopens and restores it repeatedly. Both the paragraph and playback offset
must survive each cycle; checking only the XHTML href would miss a wrong track.

Build through `bin/build_publications.sh`; do not hand-maintain packaged outputs.
To reproduce the audio:

```sh
for track in first:440 second:550 third:660; do
  ffmpeg -f lavfi -i "sine=frequency=${track#*:}:sample_rate=22050:duration=12" \
    -af volume=0.01 -codec:a libmp3lame -b:a 32k "${track%:*}.mp3"
done
```
