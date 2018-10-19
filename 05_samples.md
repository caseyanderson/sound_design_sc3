# working with samples

## Pre-flight

Acquire or create a **.wav** / **.aiff** file (note: **SC** does not support **MP3** because **MP3** is not [free software](https://en.wikipedia.org/wiki/Free_software))

## playing samples

Once one acquires a recording it must be loaded into a `Buffer` object. Pull up the reference for `Buffer` and scroll down to `Buffer.read` to follow along.

In order to load a file into a Buffer one needs to tell `scserver` where the file is located on the computer (i.e. the `filepath`). In the example below we load a sound file into a `Buffer` object, stored in the global variable `~x`:

`~x = Buffer.read( s, '/Users/cta/Desktop/haiti_baptism.wav' );`

BTW, variable names in SC that begin with a `~` are global variables.

The simplest UGen available to play samples is `PlayBuf`. For example, here is a `SynthDef` that uses a sustaining envelope to loop a sample until receiving an off message:

```python3
SynthDef(\play, { | amp = 0.0, buf, len, start = 0, trig = 1 |
  var env, sig;

	env = EnvGen.kr( Env.asr( 0.001, 0.9, 0.001 ), trig, doneAction: 0 );
  sig = PlayBuf.ar(2, buf, BufRateScale.kr( buf ), loop: 1) * env;
  Out.ar(0, sig);
}).add;
```

Looping this sample would simply be a matter of running the following code:

```python3
~x = Buffer.read( s, '/Users/cta/Desktop/haiti_baptism.wav' ); // load the sample into a Buffer

// below is the SynthDef

SynthDef(\play, { | amp = 0.0, buf, len, start = 0, trig = 1 |
  var env, sig;

	env = EnvGen.kr( Env.asr( 0.001, 0.9, 0.001 ), trig, doneAction: 0 );
  sig = PlayBuf.ar(2, buf, BufRateScale.kr( buf ), loop: 1) * env;
  Out.ar(0, sig);
}).add;

x = Synth( \play, [ \trig, 1, \amp, 0.5, \buf, ~x ] ); // create an instance of the synth and play it

x.set(\trig, 0); // stop the synth
```

Perhaps more interesting, though, would be to loop a sample and then filter it. Below we run our sample through a Band Pass Filter (or BPF in SC):

```python3
~x = Buffer.read( s, '/Users/cta/Desktop/haiti_baptism.wav' ); // load the sample into a Buffer


SynthDef(\play, { | amp = 0.0, buf, len, start = 0, trig = 1 |
	var env, sig;

	env = EnvGen.kr( Env.asr( 0.001, 0.9, 0.001 ), trig, doneAction: 0 );
	sig = BPF.ar(PlayBuf.ar(2, buf, BufRateScale.kr( buf ), loop: 1), MouseX.kr( 50, 2000), MouseY.kr( 0.1, 0.5));
	Out.ar(0, sig * env);
}).add;


x = Synth( \play, [ \trig, 1, \amp, 0.5, \buf, ~x ] ); // create an instance of the synth and play it

x.set(\trig, 0); // stop the synth
```

## deterministic synth + random start time

An alternate approach to playing samples would be to use a deterministic envelope to play a short fragment of a recording with a random start time. For example:

```python3
~x = Buffer.read( s, '/Users/cta/Desktop/haiti_baptism.wav' ); // load the sample into a Buffer

SynthDef(\play, { | amp = 0.0, buf, len, start = 0, trig = 1 |
	var env, sig;

	env = EnvGen.kr( Env.linen( 0.0, len, 0.1), trig,  doneAction: 2 );
	sig = PlayBuf.ar(2, buf, BufRateScale.kr( buf ), startPos: (start * BufFrames.kr(buf)), loop: 1) * env;
	Out.ar(0, sig);
}).add;

Synth( \play, [ \trig, 1, \amp, 0.5, \buf, ~x, \len, 2, \start, rrand(0.01, 1.0) ] ); // create an instance of the synth and play it
```
