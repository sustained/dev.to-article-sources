# Sforzando

![A picture of the musical symbol known as sforzando which translates roughly to "sudden force", from Italian subito forzando and is a direction to heavily accent a note or chord, playing it much more strongly/loudly than the surrounding ones.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/sforzando.jpg)

# Table of Contents

- [Introduction](#introduction)
  - [What libraries are you using?](#what-libraries-are-you-using)
  - [Why the name "Sforzando"?](#why-the-name)
- [My ideas and experimentation thus far](#my-ideas-and-experimentation-thus-far)
  - [The Piano component](#the-piano-component)
  - [Playing some music with Tone.js](#playing-some-music-with-tone.js)
    - [A brief distraction...](#a-brief-distraction)
    - [To sample or to synthesize...](#to-sample-or-to-synthesize)
  - [Displaying the music on the piano](#displaying-the-music-on-the-piano)
    - [A first attempt](#a-first-attempt)
      - [What's with Tone.Draw?](#what's-with-tone.draw)
    - [A second attempt](#a-second-attempt)
  - [Bach to getting distracted - reading midi files](#bach-to-getting-distracted---reading-midi-files)
  - [The colour of music](#the-colour-of-music)
    - [Clavier à lumieères](#clavier-à-lumieères)
    - [Mapping sound waves to light waves](#mapping-sound-waves-to-light-waves)
  - [Animated sheet music with SVG](#animated-sheet-music-with-svg)
- [What's next for Sforzando?](#what's-next-for-sforzando)
  - [Any actual ideas, then?](#any-actual-ideas-then)
    - [Every truly cultured music student knows...](#every-truly-cultured-music-student-knows)
    - [The circle of li--err, fifths](#The-circle-of-li--err-fifths)
    - [To send light into the darkness of men's hearts](#to-send-light-into-the-darkness-of-men's-hearts)
- [Fin](#fin)
- [Ideas, suggestions, feedback?](#ideas-suggestions-feedback)

[Back to top](#sforzando)

# All I see is blonde, brunette, redhead...

This article is fairly code-heavy but if you're not a programmer there are **definitely** at least [a few](#a-brief-distraction) sections which [you will](#to-sample-or-to-synthesize) still be [able to](#the-colour-of-music) appreciate, especially if [you're a](any-actual-ideas-then) musician, or are just interested in music.

I'd even argue that the above linked sections are more interesting than the code-related ones. :)

[Back to top](#sforzando)

---

![A picture of the famous scrolling code scene from the film The Matrix - many overlapping columns, neverending green streams, of alien-looking characters.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/matrix.jpeg)

*All I see is 90s-era special effects.*

---

# Introduction

> **Yawn!** The introduction is probably the most boring section - just a heads up.

This article is an introduction to one of my (many) pet projects - Sforzando.

I'm not 100% sure what it will become but basically I imagine some kind of app that allows you to **experiment with music theory, harmony and composition in an interactive manner**. It's still very much in the **early** prototype phase.

Like most of my projects, my reasons for creating it are:

- simply because I'm interested in music theory, harmony and composition
- to experiment with new ideas, techniques, technologies etc.
- because I'll surely learn _something_ from it and I like to learn

Unlike most of my projects, I decided to open-source it. For many years I was afraid of sharing my code but if I want to get hired then I need to start writing about and sharing my projects (as well as contributing to other people's projects), so:

## What libraries are you using?

The most noteworthy are as follows:

- [TonalJS](https://github.com/tonaljs/tonal), a music theory.
- [ToneJS](https://github.com/Tonejs/Tone.js), a web audio framework.
- [VuejS](https://github.com/vuejs/vue), a UI library.

## Why the name?

[Sforzando is a musical term](<https://en.wikipedia.org/wiki/Sforzando_(musical_direction)>) (so, Italian) which as far as I know means something like "sudden force". It accentuates a note - basically instructing the player to play it louder/harder than the other surrounding notes, so that it **stands out**.

My hope is that the app, too, can stand out from other similar apps, at least eventually! For that reason it felt like a good name.

[Back to top](#sforzando)

---

![An image of an upright acoustic piano, taken in Kontich, Belgium, taken from the right/high side of the piano. The notes blur more and more as the octaves go down.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/piano.jpeg)

*I've got 88 keys and somehow I still struggle to open my door.*

---

# My ideas and experimentation thus far

## The Piano component

![A screenshot of a browser window with the Vue devtools opened and the Sforzando Piano Vue component highlighted. The piano stretches from A1 to C#5. Various data and computed properties can be seen in the dev tools, such as octave-start, octave-end, numOctaves, pianoState and more.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/piano-component.png)

To start with, I set out to create a UI component which generates a piano.

I looked around on CodePen, JSFiddle and CodeSandbox for ideas and inspiration for a while and then off I went, with the task of creating my own.

It's entirely dynamic, so you can tell it at which octave it should start and end at and such things as these.

It uses (mostly) [CSS grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout) and a bit of [Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox).

**Example Usage:**

```html
<piano
  octave-start="3"
  octave-end="6"
  note-start="A"
  note-end="C" />
```

*[Back to top](#sforzando)*

## Playing some music with Tone.js

Now that I had a dynamic piano component, my next goal was to get Tone.js to play some music (and then to display said music on the piano).

### A brief distraction...

So, like any other non-distractible person, I went straight to the piano and wrote a chord progression:

1. Cm (`i`)
2. G (`V`)
3. Bb (`VII`)
4. F (`V / VII`)
5. Ab (`VI`)
6. Cm (`i`)
7. F#dim7 (`vii° / V`)
8. Gsus4 (`Vsus4`), G (`V`)

...it's nothing special but the goal here isn't to write good music.

> **Note:** It's very possible that my harmonic analysis _of my own chord progression_ is incorrect...

I then broke those chords up into some basic arpeggios, here they are represented as code:

```js
[
  ["C2", "D#2", "G2", "C3", "G2", "D#2"],
  ["B1", "D2", "G2", "B2", "G2", "D2"],
  ["A#1", "D2", "F2", "A#2", "F2", "D2"],
  ["A1", "C2", "F2", "A2", "F2", "C2"],
  ["G#1", "C2", "D#2", "G#2", "D#2", "C2"],
  ["G1", "C2", "D#2", "G2", "D#2", "C2"],
  ["F#1", "C2", "D#2", "F#2", "D#2", "C2"],
  ["G1", "C2", "D2", "G2", "D2", "B1"]
];
```

I chose the key of C minor, of course, because [it's the only actual good key](https://en.wikipedia.org/wiki/Beethoven_and_C_minor).

*[Back to top](#sforzando)*

---

![A large metallic statue of Ludwig van Beethoven with several smaller golden statues scattered around it. The large status is covered in a patina - a layer of green-ish oxidation that often forms on metals like copper and brass. The smaller statues are golden and out-of-focus.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/beethoven-approves.jpeg)

*Beethoven's reaction to my choice of key.*

---

### To sample or to synthesize...

Synthesizers are cool and all (oh who am I kidding - they're freaking awesome) and my chord progression sounds perfectly swell when pumped into a synthesizer:

{% codesandbox swen0 %}

But I quickly decided I wanted to use real piano samples (mostly because I am a pianist myself - the sound is just comfortable to my ear), so I [found some free samples](http://vis.versilstudios.net/vsco-community.html) and hooked them up:

{% codesandbox see7r %}

Excellent, with little effort our little chord progression sounds even nicer.

> **What's a synthesizer?** If you'd like to learn about synthesizers then check out Ableton's new [Learning Music](https://www.ableton.com/en/blog/learn-music-in-your-browser/) website...
> 
> ...after reading this article, of course.

*[Back to top](#sforzando)*

---

![The string section of an orchestra as seen from above, in the middle of playing some music.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/orchestra.jpeg)

*Q: What's the definition of a semitone? A: Two violinists playing in unison.*

---

## Displaying the music on the piano

Now I needed a way to highlight the active note on my piano.

### A first attempt

My initial implementation of this was _heresy_ - DOM manipulation... in Vue.js! Shudder. But I wasn't really sure how else I could make it work.

Anyway, it ended up looking a little something like this:

```js
Transport.scheduleRepeat(time => {
  sampler.triggerAttackRelease(this.activeNote, "8n");

  Draw.schedule(() => {
    const notes = document.querySelectorAll("li.note");

    if (notes) {
      for (let i = 0; i < notes.length; i++) {
        notes[i].classList.remove("active");
      }
    }

    document
      .querySelector(`li.${this.activeNote}`)
      .classList.add("active");
  }, time);

  this.step();
});
```

I know, it's really bad isn't it? But it worked for an initial proof-of-concept implementation. Well, mostly...

It was one note out-of-sync and so I had to add a hack - a computed property that returned the *previous note* and then I added the class to _that_ instead.

Yep, who imagined that it could get any worse?

Fear not dear reader, we can do much better than this... and we will, in a moment.

#### What's with Tone.Draw?


Just in case you were wondering - the callbacks passed to `Transport.schedule` are ran in a [Web Worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) and the entire library (really any music app or library, in fact) is _really time-sensitive/performance-critical_.

If we were to do anything heavy in the callback it could (and probably would) destroy performance. Additionally, the events can be scheduled far in advance of you actually _hearing them_, or can be ran in a background tab (when there's not even anything to see).

`Tone.Draw` addresses this problem by making use of [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame). It will trigger our drawing code _as close to the Tone event as possible_. Maybe slightly before or after. But always very, very close.

*[Back to top](#sforzando)*

---

![A picture of badly formatted/indented computer code. It is enough to make your eye's bleed. Luckily for you, this text won't do that.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/bad-code.jpeg)

*Even the above code is better than DOM manipulation in Vue.*

---

### A second attempt

I reached out for advice and someone suggested to me the idea of using [Vue.observable](https://vuejs.org/v2/api/#Vue-observable) to store the active key state.

For those not in the know, `Vue.observable` is what is used to make your `data` reactive internally - tl;dr: it's magic.

Here's what I ended up with:

```js
import Vue from "vue";
import { createRange } from "./music";

const notes = createRange("A0", "C8");

const noteMap = notes.reduce((map, note) => {
  map[note.name] = false;
  return map;
}, {});

const pianoState = new Vue.observable(noteMap);

export default pianoState;

export function reset() {
  for (const note of notes) {
    pianoState[note.name] = false;
  }
}
```

It constructs an object that looks something like this:

```js
{
  "A0": false,
  "A#0": false,
  "B0": false,
  "C1": false
  // etc.
}
```

**That's one key-value pair for every note on a grand piano (A0-C8, 88 keys)**.

If a key is `true` then that note is being "held" (and therefore it should be highlighted) and if it's `false` then the opposite is true.

Because it's a Vue-wrapped reactive object we can use this in computed properties and such and it will trigger a re-render whenever it changes - perfect!

*[Back to top](#sforzando)*

---

![A picture of a beaker submerged in some kind of glass container which contains a liquid. It looks like science is afoot. We should probably take a step back.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/reaction.jpeg)

*According to preliminary analysis, Vue reactivity is 172,643% more interesting than the above reaction.*

---

## Bach to getting distracted - reading midi files

I decided that in order to put this system to the test I'd need to throw some _real_ music at it and so [I choose a Bach prelude - the most famous one, in fact](https://en.wikipedia.org/wiki/Prelude_and_Fugue_in_C_major,_BWV_846). You've probably heard it before.

Hooking up midi to my sampler was rather simple and ended up looking something like this:

```js
midi.tracks.forEach(track => {
  track.notes.forEach(note => {
    Transport.schedule(() => {
      piano.triggerAttackRelease(
        note.name,
        note.duration,
        Tone.now(),
        note.velocity
      );
    }, note.time + Tone.now() + 0.5);
  });
});
```

Of course, we'd also need to hook this up to our new `Vue.observable`-powered reactive piano state.

After a few attempts I settled on this method with 3x separate `Transport.schedule` calls. Somehow it seems to work better than the other methods I tried and honestly I don't understand why:

```js
midi.tracks.forEach(track => {
  track.notes.forEach(note => {
    Transport.schedule(() => {
      piano.triggerAttackRelease(
        note.name,
        note.duration,
        Tone.now(),
        note.velocity
      );
    }, note.time + Tone.now() + 0.5);

    Transport.schedule(time => {
      Draw.schedule(() => {
        pianoState[note.name] = true;
      }, time);
    }, note.time + Tone.now() + 0.5);

    Transport.schedule(time => {
      Draw.schedule(() => {
        pianoState[note.name] = false;
      }, time);
    }, note.time + note.duration + Tone.now() + 0.5);
  });
});
```

*[Back to top](#sforzando)*

---

![A picture of a midi keyboard as seen from the side, sitting by a window, on a table. An empty street can be seen in the background.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/synth.jpeg)

*I want one. I want one. I want one.*

---

## The colour of music

So, I had it sort of working on a basic level but all the notes were highlighted red and it was awful.

Off to Google I went to see if there were any existing techniques for mapping frequencies to colours.

### Clavier à lumieères

![A picture of a midi keyboard as seen from the side, sitting by a window, on a table. An empty street can be seen in the background.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/scriabin_circle.jpg)

A Russian composer called [Alexandar Scriabin](https://en.wikipedia.org/wiki/Alexander_Scriabin) is purported to have suffered from the condition [Synesthesia](https://en.wikipedia.org/wiki/Synesthesia). If you haven't heard of it, it can essentially be summed up as when the "wires" relating to two senses get crossed.

For Scriabin, it was his sense of hearing and sight that were affected and so to him - **musical notes had colour.**

Based on this, he developed a system - **Clavier à lumieères** (Keyboard with lights).

> In his autobiographical Recollections, [Sergei Rachmaninoff](https://en.wikipedia.org/wiki/Sergei_Rachmaninoff) recorded a conversation he had had with Scriabin and [Nikolai Rimsky-Korsakov](https://en.wikipedia.org/wiki/Nikolai_Rimsky-Korsakov) **about Scriabin's association of colour and music.**
> 
> Rachmaninoff was surprised to find that Rimsky-Korsakov agreed with Scriabin on associations of musical keys with colors; himself skeptical, Rachmaninoff made the obvious objection that the two composers did not always agree on the colours involved.
> 
> Both maintained that **the key of D major was golden-brown; but Scriabin linked E-flat major with red-purple, while Rimsky-Korsakov favored blue.** However, Rimsky-Korsakov protested that a passage in Rachmaninoff's opera The Miserly Knight accorded with their claim: the scene in which the Old Baron opens treasure chests to reveal gold and jewels glittering in torchlight is written in D major.
> 
> Scriabin told Rachmaninoff that "your intuition has unconsciously followed the laws whose very existence you have tried to deny.
> 
> **Source:** Wikipedia

It's a really cool system and I'd like to find some way to use it in my app, however I settled on another technique...

*[Back to top](#sforzando)*

---

![One octave of a piano keyboard with the keys coloured according to Alexander Scriabin's perception of the notes due to his condition - Synesthesia.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/scriabin_keyboard.png)

*How to get your young child interested in learning piano 101.*

---

### Mapping sound waves to light waves

As you may or may not know, sound is basically vibrations and we measure vibrations by using [Hertz](https://en.wikipedia.org/wiki/Hertz) (cycles per second).

For example:

- the note A4 (A above middle C) is 440 Hz
- the note A5 (the next A up) is 880 Hz

Light is made of waves and waves have a length, which we measure using [nanometres](https://en.wikipedia.org/wiki/Nanometre) (at least for the visible spectrum, which lies between 400-700nm).

If we convert that to hertz then we get 430-750THz (1Hz = 10<sup>12</sup>Hz).

> **Ouch!** That's a lot of Hertz.

Sorry about that. Where was I? Ah, yes.

So, basically we can **directly map sound frequency to light.**

I tried to implement the algorithm myself but ran into some problems, so I just copy-pasted some colour values.

Unfortunately for you, that means that the sandbox I was going to put here does not exist. :(

---

![A screenshot of the CSS that maps the notes to colours, in the same order as the visible light spectrum, starting at F sharp (a deep red) and going to F (a deep purple).](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/css-light-sound-colours.png)

*Behold the power of copy-paste.*

---

## Animated sheet music with SVG

{% codesandbox go4pm %}

Another thing I've (just barely) started experimenting with is generating [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) with [MuseScore](https://musescore.org/en) and then animating it. 

Additionally I played around with generating my own SVG-based sheet music from scratch.

This experimentation was actually for [another very early musical project of mine](https://github.com/sustained/die-sonaten) but that doesn't matter as said experimentation will undoubtedly make its way into this project, too, in some way, shape or form.

I have to say though, I've actually never worked with SVG before, so it's all very new to me but based on my very limited exposure - it's very cool.

Also, you should definitely check out [SVG.js](https://svgjs.com/) and [Anime.js](https://animejs.com/)!

*[Back to top](#sforzando)*

---

![A picture of a rainbow, taken in Maui, Hawaii. The sky is quite cloudy. There are lush green rolling plains along with a small amount of hilly or even somewhat mountaineous terrain.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/rainbow.jpeg)

*There's a land that I heard of once in a lullaby...*

---

# What's next for Sforzando?

Unfortunately, many of my projects end up dead and buried; often hidden away in private repositories.

This generally happens because of one or more of the following reasons:

- I'm being too grandiose with my vision
  - and/or scope creep - trying to add too much, too quickly
- Rewriting the entire project too early on
  - and/or changing technology choices part way through
- Struggling with how to architect the API, or the schema etc.

But I really would like to keep working on this one.Out of all of my projects, old and new, it's probably my second favourite idea.

And I'm deeply passionate about music.

**So here's what I intend to do...**

Yes, I'll keep experimenting with random ideas like animating SVGs and parsing MIDI files and whatever else comes to mind but simultaneously I am going to devote some time and effort to actually **planning** and **designing** the app this time around, instead of just blindly coding away for several months until I have some undocumented and unmaintainable organicly-grown beast that even I don't fully understand.

*[Back to top](#sforzando)*

## Any actual ideas, then?

### Every truly cultured music student knows...

{% youtube khvaIwonxUk %}

I'd like to add a bunch of stuff relating to chords, scales, arpeggios etc. as soon as possible (the initial prototype had it).

In terms of the actual musical (i.e. sound-making) part of the app:

- an arpeggiator that can generate and play arpeggios
- a way to have the app play scales for you
- a system to play common harmonic sequences/progressions
- etc.

As well as learning/visual aids:

- something to help with learning chord inversions
- learn key signatures using the circle of fifths as a guide
- fingering charts for scales
- etc.

Not to mention compositional aids:

- highlight instrument ranges on the piano component
- etc.

*[Back to top](#sforzando)*

### The circle of li--err, fifths

I've been working on a circle of fifths component (also SVG) and I have various ideas relating to the visualisation of harmonic progressions, of diatonic harmony, of key signatures and so much more using it.

Honestly the circle of fifths is the most fascinating thing ever and I highly recommend learning about it. 

And don't stop until you **understand** it.

*[Back to top](#sforzando)*

---

![A circle of fifths - a visual aid for understanding the relationship between keys. At 12 o'clock we have C Major and a minor, both with zero accidentals. Going clockwise by intervals of perfect fifths adds a sharp to the key signature - G (1 sharp), D (2 sharps) whereas going anti-clockwise by intervals of perfect fourths adds a flat - F (1 flat), Bb (2 flats) etc.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/circle-of-fifths.png)

*The Theory of Everything............ of Music™.*

---

### To send light into the darkness of men's hearts

I'd definitely like for this to be a useful for **musical composition** if at all possible but I'm not really sure how that is going to work.

Traditionally and personally I always favoured apps like [Sibelius](http://www.sibelius.com/products/avid_scorch/index.html) and [Finale](https://www.finalemusic.com/) but lately I've been turning to paper more and more, mostly because those apps aren't available for my Operating System and I find it hard to work with [MuseScore](https://musescore.org/).

The other day though I started playing with Sequencers and Digital Audio Workstations and I have to say I really like some of their ideas and wouldn't mind stealing a couple.

In any case, music composition is something I'm trying to get back into and so I'll absolutely be [dogfooding](https://en.wikipedia.org/wiki/Eating_your_own_dog_food) this app/tool when it gets to a more usable state.

*[Back to top](#sforzando)*

---

![An aging hand reaching out to touch a manuscript.](https://raw.githubusercontent.com/sustained/dev.to-article-sources/master/media/sforzando-an-interactive-music-theory-harmony-learning-experimentation-app/composer.jpeg)

*I don't have anything witty left to say.*

---

# Fin

If you enjoyed this article and would like me to write more then **please** show some love because it took several **hours** to put this together and I could have spent that time working on my projects instead, or writing music, or whatever else.

*[Back to top](#sforzando)*

---

# Ideas, suggestions, feedback?

Feel free to message me on here or reply to the article but otherwise I'm always reachable:

- on Discord (sustained#2329)
- on Github (sustained)

**I'd especially be interested to hear from you if:**

- you're a music teacher/student and have ideas
- you have feedback on how I can improve my writing

Otherwise, [follow me](https://dev.to/sustained) for more posts (probably) about:

- This app
- My other projects
- Vue.js, Laravel and other neat technologies
- Music composition and production
- Language learning

*[Back to top](#sforzando)*

# Links

- [Source code](https://github.com/sustained/sforzando)
- [Live demo](https://sforzando.herokuapp.com/)
  
    _Mobile is 100% untested (at this early stage)._
  
- [Project page](https://www.sustained.name/projects/sforzando/)

    _Information on my website is out of date._

*[Back to top](#sforzando)*

---

> Something missing or incorrect? This [article's source is on Github](https://github.com/sustained/dev.to-article-sources/).
> 
> Please feel free to [open an issue](https://github.com/sustained/dev.to-article-sources/issues/new) or [send a PR](https://github.com/sustained/dev.to-article-sources/compare).
