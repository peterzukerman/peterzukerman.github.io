---
layout: post
title: Generating Artificial Audio Modifications with PyDub
---

PyDub is a simple but powerful [python library](https://github.com/jiaaro/pydub) that lets you quickly and easily manipulate audio files.

### Increasing Prediction Accuracy

For speech processing, we need a robust dataset in order to properly predict phonemes from utterances. Certain problems might arise when we work with languages with smaller datasets, or with particular data where the sample size is small. For example, a low resource language might have few samples, as might a corpus of Turkish speakers saying Spanish words. Thus, artificially generated audio files that slightly differ from the original can dramatically increase the size of the dataset. Such modifications include:

* Changing the volume of the entire sample
* Changing the volume of different parts of the sample
* Altering the pitch [without changing playback speed](https://en.wikipedia.org/wiki/Audio_time_stretching_and_pitch_scaling)
* Modifying the playback speed [without changing the pitch](https://en.wikipedia.org/wiki/Phase_vocoder)
* Overlaying noise

### Hyde features

In addition to the features of Poole, Hyde adds the following:

* Sidebar includes support for textual modules and a dynamically generated navigation with active link support
* Two orientations for content and sidebar, default (left sidebar) and [reverse](https://github.com/poole/lanyon#reverse-layout) (right sidebar), available via `<body>` classes
* [Eight optional color schemes](https://github.com/poole/hyde#themes), available via `<body>` classes

[Head to the readme](https://github.com/poole/hyde#readme) to learn more.

### Browser support

Hyde is by preference a forward-thinking project. In addition to the latest versions of Chrome, Safari (mobile and desktop), and Firefox, it is only compatible with Internet Explorer 9 and above.

### Download

Hyde is developed on and hosted with GitHub. Head to the <a href="https://github.com/poole/hyde">GitHub repository</a> for downloads, bug reports, and features requests.

Thanks!
