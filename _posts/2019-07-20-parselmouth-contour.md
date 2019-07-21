---
layout: post
title: "Adjusting Pitch Contour with Parselmouth-Praat"
excerpt: Praat is a powerful tool for phonetics and speech processing. However, since it's GUI based, it's often very slow to use for multiple files. By using Parselmouth, we can wrap Praat functions and access them in Python. One such function is pitch contouring.
tags:       [programming, python, signal processing, praat, english]
header-img: "https://zuker.io/output_8_0fb.png"
---

[Praat](http://www.fon.hum.uva.nl/praat/) is an application developed for phonetic analysis and speech processing tasks. It's a very powerful tool that remains a state-of-the-art solution for many phonetic needs. However, I want to use it in Python so that I can write general algorithms and store variables, passing them to Praat functions. A library called [Parselmouth](https://github.com/YannickJadoul/Parselmouth) wraps the GUI and gives us access to Praat functions in Python. Let's take a look at how we can use these tools to create custom speech contours.

### Pitch Contour

Words in all languages have varying pitch. In Chinese and Thai, we have tones, that tell us when to rise and fall in pitch. Languages like English, Hebrew, or Russian don't have tones. Let's look at some sample pitch contours. First let's import the samples into Python. The sentence I'll use is "We must go to school" in Chinese, Hebrew, and English.


```python
import parselmouth
from IPython.display import Audio
```


```python
#我们必须去学校了。zh-CN
soundZh = parselmouth.Sound("chineseSample.mp3")
Audio(data=soundZh.values, rate=soundZh.sampling_frequency) #Mandarin
```

```python
#We must go to school. en-US
soundEn = parselmouth.Sound("englishSample.mp3")
Audio(data=soundEn.values, rate=soundEn.sampling_frequency) #English
```

```python
#.אנחנו צריכים ללכת לבית הספר he-IL
soundHe = parselmouth.Sound("hebrewSample.mp3")
Audio(data=soundHe.values, rate=soundHe.sampling_frequency) #Hebrew
```

Now we need libraries to display the pitch. Let's import and configure those.


```python
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```


```python
def plotOnGraph(pitch, color):
    pitch_values = pitch.selected_array['frequency']
    pitch_values[pitch_values==0] = np.nan
    plt.plot(pitch.xs(), pitch_values, 'o', markersize=2.5, color=color)
    
def setupGraph(ymin, ymax):
    sns.set() # Use seaborn's default style to make attractive graphs
    plt.rcParams['figure.dpi'] = 150 # Show nicely large images in this notebook
    plt.figure()
    plt.ylim(ymin, ymax)
    plt.ylabel("frequency [Hz]")
    plt.xlabel("seconds")
    plt.grid(True)
```


```python
pitchZh = soundZh.to_pitch()
pitchEn = soundEn.to_pitch()
pitchHe = soundHe.to_pitch()

setupGraph(50, 375)

plotOnGraph(pitchZh, 'r')
plotOnGraph(pitchEn, 'g')
plotOnGraph(pitchHe, 'b')

plt.gca().legend(('zh','en','he'))

plt.show()
```


![](../../output_8_0.png)


As we can see, there is high variance in frequency in the Mandarin sample, and less so in the English and Hebrew samples. In Hebrew, we can see a rise between seconds 1-1.5, where the speaker is saying צריכים (need). The frequency in English varies only slightly. This was expected, as Mandarin is a tonal language, whereas English and Hebrew are not. 

### Mandarin Tones
Adjusting pitch contours in tonal languages like Mandarin is simple algorithmically, but keep in mind that the meaning of the word changes. Let's look at the 4 words 妈, 麻, 马, and 骂. They are pronounced mā, má, mǎ, and mà respectively. Each change in tone changes the meaning of the word. Let's take a look at the pitches of these four words.


```python
ma1 = parselmouth.Sound("ma1.mp3")
Audio(data=ma1.values, rate=ma1.sampling_frequency) #妈, first tone
```

```python
ma2 = parselmouth.Sound("ma2.mp3")
Audio(data=ma2.values, rate=ma2.sampling_frequency) #麻, second tone
```

```python
ma3 = parselmouth.Sound("ma3.mp3")
Audio(data=ma3.values, rate=ma3.sampling_frequency) #马, third tone
```

```python
ma4 = parselmouth.Sound("ma4.mp3")
Audio(data=ma4.values, rate=ma4.sampling_frequency) #骂, fourth tone
```

```python
pitch1 = ma1.to_pitch()
pitch2 = ma2.to_pitch()
pitch3 = ma3.to_pitch()
pitch4 = ma4.to_pitch()

setupGraph(50, 250)

plotOnGraph(pitch1, 'r')
plotOnGraph(pitch2, 'b')
plotOnGraph(pitch3, 'g')
plotOnGraph(pitch4, 'k')

plt.gca().legend(('1','2','3','4'))

plt.show()
```


![](../../output_15_0.png)


This is in line with Chinese tonal pitch patters, as expected. Let's try to change one of these tones to match a different one.

### Parseltongue and Changing Contours
Let's try to change tone 1 to tone 2.


```python
from parselmouth.praat import call

manipulation = call(ma1, "To Manipulation", 0.01, 20, 600) #look for values between 20Hz and 250Hz
pitch_tier = call("Create PitchTier", "name", 0, 0.8) #create a pitchTier between 0 and 0.8 seconds.
```

We need to add 2 pitchPoint objects to the pitchTier, as it changes between two different pitches.


```python
call(pitch_tier, "Add point", 0.01, 100) #create pitchPoint at 0s at 100Hz
call(pitch_tier, "Add point", 0.59, 100) #create pitchPoint at 0.59s at 100Hz
call(pitch_tier, "Add point", 0.6, 150) #create pitchPoint at 0.6s at 150Hz

call([pitch_tier, manipulation], "Replace pitch tier")
tone1Mod = call(manipulation, "Get resynthesis (overlap-add)")

#sound.pre_emphasize()
Audio(data=tone1Mod.values, rate=tone1Mod.sampling_frequency)
```

```python
setupGraph(0, 250)

plotOnGraph(tone1Mod.to_pitch(), 'r')
plotOnGraph(pitch2, 'b')
```


![](../../output_21_0.png)


We're close. The pitch jumps up artificially. Let's smooth it out.


```python
manipulation_improved = call(ma1, "To Manipulation", 0.01, 20, 600)
pitch_tier_improved = call("Create PitchTier", "name", 0, 0.8) #create a pitchTier between 0 and 0.8 seconds.

call(pitch_tier_improved, "Add point", 0.01, 100) #create pitchPoint at 0s at 100Hz

#at 0.4s, the pitch starts increasing
call(pitch_tier_improved, "Add point", 0.4, 100)
call(pitch_tier_improved, "Add point", 0.5, 120)
call(pitch_tier_improved, "Add point", 0.6, 150)

call([pitch_tier_improved, manipulation_improved], "Replace pitch tier")
tone1Improved = call(manipulation_improved, "Get resynthesis (overlap-add)")

#sound.pre_emphasize()
Audio(data=tone1Improved.values, rate=tone1Improved.sampling_frequency)
```

```python
setupGraph(0, 250)

plotOnGraph(tone1Improved.to_pitch(), 'r')
plotOnGraph(pitch2, 'b')
```


![](../../output_24_0.png)


Much better. Finally, let's try it with an english sentence.


```python
manipulation = call(soundEn, "To Manipulation", 0.01, 20, 600)
pitch_tier = call("Create PitchTier", "name", 0, 1.7) 

call(pitch_tier, "Add point", 0.01, 100) #create pitchPoint at 0s at 100Hz
call(pitch_tier, "Add point", 0.5, 150) #create pitchPoint at 0s at 100Hz
call(pitch_tier, "Add point", 1, 100) #create pitchPoint at 0s at 100Hz
call(pitch_tier, "Add point", 1.7, 175) #create pitchPoint at 0.59s at 100Hz


call([pitch_tier, manipulation], "Replace pitch tier")
engMod = call(manipulation, "Get resynthesis (overlap-add)")

#sound.pre_emphasize()
Audio(data=engMod.values, rate=engMod.sampling_frequency)
```

```python
setupGraph(0, 250)

plotOnGraph(engMod.to_pitch(), 'r')
plotOnGraph(soundEn.to_pitch(), 'b')
```


![](../../output_27_0.png)


It's a little artificial, but it works.

### Conclusion
Parselmouth can effectively wrap praat functions and lets us modify various aspects of audio files using python.
