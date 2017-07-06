+++
date = "2017-07-06T16:25:31-04:00"
highlight = true
math = false
tags = []
title = "Cutting Videos with FFmpeg"

[header]
  caption = ""
  image = ""

+++

While building the [downloading and decoding
scripts](https://github.com/mbuckler/youtube-bb) for the [Youtube BoundingBoxes
dataset](https://research.google.com/youtube-bb/index.html) I needed to
accurately cut videos into smaller clips. Some of the annotated videos were
quite long and the annotations rarely covered the full video, so to save space
my scripts cut out and save only the annotated sections. If done incorrectly
this video cutting can cause subtle frame timing issues which I didn't fully
understand when I started writing these scripts. If you want to avoid these
problems read below for the proper way to accurately cut videos with
[FFmpeg](http://ffmpeg.org/).

First, a very quick review of video compression. Most modern video compression
algorithms use a technique called [motion
compensation](https://en.wikipedia.org/wiki/Motion_compensation) to reduce the
size of stored video files. All frames in MPEG are stored as either Key frames
(I frames) or Predicted frames (P or B frames). Each Key frame is compressed
with some variation on the [JPEG compression
codec](https://en.wikipedia.org/wiki/JPEG), but Predicted frames are represented
as motion compensated versions of earlier frames (as in P frames) or both
earlier and later frames (as in B frames). Because these motion compensation
references take up much less space than a compressed JPEG, overall the
compressed video takes up less space. For (much) more detail you can see [Dave
Marshall's excellent
explanation](https://users.cs.cf.ac.uk/Dave.Marshall/Multimedia/node255.html).

Now that we've reviewed video compression and the different kinds of encoded
frames, we can talk about seeking. Seeking is the process used to find certain
sections of the video to either extract a frame or cut out a portion. FFmpeg has
implemented a few options for our use, but their differences in behavior and/or
usage can be easy to overlook. Here we will discuss the three methods of seeking
(and by extension cutting) video with FFmpeg, with each corresponding to a point
on the speed/accuracy tradeoff.

* Key Frame Seeking

    The fastest way to extract a portion of video from a larger video (with a 60
    second clip starting 30 seconds in) would be the following:

        ffmpeg -ss 30 -i input_vid.mp4 -t 60 -c copy output_clip.mp4

    This method is so fast because it uses Key frames when performing seek, and
    there are far fewer Key frames than Predicted frames. It also is a [stream
    copy](https://ffmpeg.org/ffmpeg.html#Stream-copy) meaning that the encoded data
    extracted from the original video is taken directly without decoding and then
    re-encoding. While this feature may be useful for some users who don't care
    about frame accuracy, it doesn't work for our purposes. This is because even
    though FFmpeg 2.1 enabled frame accurate Key frame seeking for frame extraction
    ([more detail here](https://trac.ffmpeg.org/wiki/Seeking)) this behavior is not
    supported in FFmpeg 2.6 when cutting out clips of video. This means that the
    start of your video will be aligned with the nearest Key frame to 30 seconds,
    not the nearest frame independent of encoding format. 

* All-Frame Seeking

    A slightly slower and more accurate method of video cutting would be the
    following:

        ffmpeg -i input_vid.mp4 -ss 30 -t 60 -c copy output_clip.mp4

    Can you tell the difference? It is frustratingly similar to the previous
    command... It turns out that FFmpeg arguments are order sensitive, so choosing
    the clip's start time after the input video causes a change in behavior! This
    method uses all-frame seeking, also known as ["output
    seeking"](https://trac.ffmpeg.org/wiki/Seeking#Outputseeking) by FFmpeg users.
    It is also a stream copy, but considers all frames (including Predicted frames)
    when performing a seek. This means that your output clip will start with the nearest frame
    to 30 seconds even if it is a predicted frame.

    If you use this command and try to play your output clip, you may notice that
    the clip starts frozen, or with black frames in the beginning. Did FFmpeg do
    something wrong? No, that behavior is simply a result of the functionality that
    we requested when we performed an all-frame seek on a stream copy. If your 30
    second start happens to align with a Predicted frame, that Predicted frame will
    be copied into the output clip, along with the rest of the frames in the 60
    second portion. But as we already established, Predicted frames are simply
    motion compensated references to previous or following Key frames. By stream
    copying with all-frame seeking we have removed the reference Key frames which
    came before our first frame. This is why your video player displays frozen or
    black frames: the necessary data to represent the first portion of your
    video isn't included in the file.

* Full Re-Encoding

    So Key frame seeking causes missalignment and All-frame seeking causes broken
    frames, is there another option? Yes, but some folks don't like it. Instead of
    performing a stream copy we can decode the source video, and then re-encode the
    output clip:

        ffmpeg -i input_vid.mp4 -ss 30 -strict -2 -t 60 output_clip.mp4

    This isn't perfect for two reasons: 1) Re-encoding can cause
    quality loss if done incorrectly, and 2) Re-encoding is far slower than stream
    copying. While both of these two points should be considered, if you want to
    copy a frame-exact section of video you have to re-encode. At least now you
    know!
