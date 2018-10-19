# SynthDefs & Envelopes

SC has an optimized way of taking in information about `UGens` and their interconnections: `SynthDef`s. A `SynthDef` is the blueprint that defines a particular instance of a playing `Synth`. Or, if you prefer, a `SynthDef` is a cake recipe and a `Synth` is the cake you end up with by following the recipe.

What follows are two versions of the same instrument: one that sustains until receiving an off message and another which has a specific duration.

### sustaining synth

```python3
SynthDef( \sin,	{ | amp = 0.0, freq = 440, out = 0, trig = 0 |
	var env, sig, finalSig;
	env = EnvGen.kr( Env.asr( 0.001, 0.9, 0.001 ), trig, doneAction: 0 );
	sig = SinOsc.ar( freq, 0.0, amp );
	finalSig = sig * env * 0.6;
	Out.ar( out, Pan2.ar(finalSig) );
}).add;

x = Synth( \sin, [ \freq, 400, \amp, 0.5]);

x.set(\trig, 1);
x.set(\trig, 0 );
s.scope;
```

Here we define a `SynthDef` named `\sin` with a `sustaining` envelope, or an envelope that plays continuously until receiving an off message. Variables are used to organize the code into chunks: `env`, short for envelope, is the portion of our code that allows us to toggle the sound on and off; `sig`, short for signal, is the portion of our code that defines what the sound is.  Multiplying the `env` by the `sig` is what results in the "on/off" functionality above and occurs in the `finalSig` line.

The resulting signal is written to a `Bus` (in this case, our speakers) in the `Out.ar` line, which also converts our `Mono` signal to `Stereo`. We will revisit this line in the future so for now don't spend too much time paying attention to it. Note: I reuse the above general structure for all sustaining sounds unless something special/unusual is needed.

To hear the `\sin` we need to create and play an instance of it, which we accomplish using `Synth`. In order to turn this instance of `\sin` off in the future we store it to the variable `x`. After running the `Synth` line, we can turn the envelope on by setting the `\trig` argument to `1`. Later we can turn our synth off by setting `\trig` to `0`. Think of these two lines as a metaphorical toggle button for `\sin`.


### deterministic synth

```python3
SynthDef( \sin,	{ | amp = 0.0, freq = 440, out = 0, sus = 1, trig = 0 |
	var env, sig, finalSig;
	env = EnvGen.kr( Env.linen( 0.001, sus, 0.001 ), trig, doneAction: 2 );
	sig = SinOsc.ar( freq, 0.0, amp );
	finalSig = sig * env * 0.6;
	Out.ar( out, Pan2.ar(finalSig) );
}).add;


Synth( \sin, [ \amp, 0.5, \freq, 400, \trig, 1]);

```

The primary difference between the `sustaining` and `deterministic` synth can be seen in their respective `env` lines. Here we use a different `Env`: `.linen`. Take a moment to compare the two `Env` (`.asr` and `.linen`) by highlighting `Env` and looking it up in the the Help COMMAND+D.

`.linen` creates Envelopes in a trapezoidal shape. In order to calculate this shape, `.linen` needs information regarding the duration, in seconds, of each segment of its shape. One commonly refers to each segment of the shape as `attack time` (segment 1), `sustain time` (segment 2), and `release time` (segment 3). In other words, if we want our `Synth` to play for 10 seconds we would want to set each of our segments such that the total time adds up to `10`. In the above Synth we accomplish this by using super tiny numbers for `attack` and `release` and setting `sus` to 10.

The benefit here is that the envelope (and associated `Synth`) will clean itself up after it is `done` (this is set by `doneAction: 2` in the `SynthDef`), so we could run the `Synth` line repeatedly to create, for example, 5 instances of `\sin` each with a total duration of (approximately) 10 seconds. Note: I reuse the above general structure for all deterministic sounds unless something special/unusual is needed.
