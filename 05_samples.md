# working with samples



```python3
SynthDef(\play, { | amp = 0.0, buf, len, start = 0, trig = 1 |
  var env, sig;

  env = EnvGen.kr( Env.linen( 0.0, len, 0.01), trig,  doneAction: 2 );
  sig = PlayBuf.ar(1, buf, BufRateScale.kr( buf ), startPos: (start * BufFrames.kr(buf)), loop: 1) * env;
  Out.ar(0, sig);
}).add;
```
