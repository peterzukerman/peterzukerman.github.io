---
layout: post
title: "Generating Artificial Audio Modifications with PyDub"
author:     "Peter Zukerman"
header-img: "audioVec.png"
tags:       [machine learning, programming, python, signal processing]
---

PyDub is a simple but powerful [python library](https://github.com/jiaaro/pydub) that lets you quickly and easily manipulate audio files.

### Increasing Prediction Accuracy

For speech processing, we need a robust dataset in order to properly predict phonemes from utterances. Certain problems might arise when we work with languages with smaller datasets, or with particular data where the sample size is small. For example, a low resource language might have few samples, as might a corpus of Turkish speakers saying Spanish words. Thus, artificially generated audio files that slightly differ from the original can dramatically increase the size of the dataset. Such modifications include:

* Changing the volume of the entire sample
* Changing the volume of different parts of the sample
* Altering the pitch [without changing playback speed](https://en.wikipedia.org/wiki/Audio_time_stretching_and_pitch_scaling)
* Modifying the playback speed [without changing the pitch](https://en.wikipedia.org/wiki/Phase_vocoder)
* Overlaying noise

### PyDub

Thankfully, PyDub has several functions that make it much easier to work with audio files of various formats. Let's start by loading in a WAV file.

{% highlight python %}
# Load in library
from pydub import AudioSegment

# Load in WAV file
sound = AudioSegment.from_file("sample.wav", format="wav")

{% endhighlight %}

Changing the volume of the whole sample is easy.

{% highlight python %}

louder = sound + 5 #5 dB louder
quieter = sound - 8 #8 dB quieter

{% endhighlight %}

Or, we can split it up into segments and adjust each segment individually. We can later use this to generate natural sounding inflections.

{% highlight python %}
length = sound.duration_seconds

first = sound[:length/2 * 1000] #pydub works in milliseconds
second = sound[length/2 * 1000:]

first += 2
second -= 4

sound = first + second #concatenate

{% endhighlight %}

We can shift the pitch as well, but this isn't the result we actually want. When you increase the speed of the clip, the pitch would also go up.

{% highlight python %}

# shift the pitch down by half an octave (speed will decrease proportionally)
octaves = -0.5

newRate = int(sound.frame_rate * (2.0 ** octaves))

lowpitch = sound._spawn(sound.raw_data, overrides={'frame_rate': new_sample_rate})

{% endhighlight %}

But how can we preserve the speed while changing the pitch? Or preserve the pitch while changing the speed? Unfortunately, PyDub doesn't have a solid implementation for this, so I won't touch on it here. If you want to see a working solution in python, check out [this article.](http://andrewslotnick.com/posts/speeding-up-a-speech.html)

### Conclusion

By making slight alterations in given voice samples, we can exponentially increase the size of a dataset. Even small changes like this increase prediction algorithms' success rates. An extension of making life-like intonations might even be used to generate voices.

