# SuperCollider 3 Basics

## Download
Go [here](https://supercollider.github.io/download.html) to download the `Current Version` of SuperCollider 3 (3.9.3 as of this writing). Once your download is complete install SC.

## About
Supercollider is made up of two applications: a language interpreter and one or more synthesis servers. All communication between the `lang` (short for language) and the `server` is done via [OpenSoundControl](http://opensoundcontrol.org/).

## Starting the server
The Server must be running in order to make sound (more on this shortly). Type the following into a new window (COMMAND+N makes a new window) and then hit SHIFT+ENTER on the same line to execute the code:

```python3
s.boot;
```

Here `s` refers to the `localhost` server on your CPU.

`s.boot;` generally results in something like this in the post window:

```python3
-> localhost
booting server 'localhost' on address: 127.0.0.1:57110
Found 0 LADSPA plugins
Number of Devices: 2
   0 : "Built-in Microph"
   1 : "Built-in Output"

"Built-in Microph" Input Device
   Streams: 1
      0  channels 2

"Built-in Output" Output Device
   Streams: 1
      0  channels 2

SC_AudioDriver: sample rate = 44100.000000, driver's block size = 512
SuperCollider 3 server ready.
Requested notification messages from server 'localhost'
localhost: server process's maxLogins (1) matches with my options.
localhost: keeping clientID (0) as confirmed by server process.
Shared memory server interface initialized
```

One can quit the server by executing the following code:

```python3
s.quit;
```

## Functions
Similar to most other programming language functions in SC are denoted by the use of curly brackets. Anything between `{ }` is a function.

*For Example*

```python3
f = { "hello world!".postln; };
f.value;
```

The first line of code stores the function at `f`. The second line returns the `value` (in this case, prints the message "hello world!" to the post window) associated with the function. Here `value` is short for `evaluate`. If one needs to use a function simply `.value` it. Note that running `f.value` will result in "hellow world!" twice in the post window. This is normal in SC: SuperCollider returns the value of the last line of code by default.

## Arguments and Variables within Functions
Arguments allow one to pass values to a function when the function is called.

*For Example*

```python3
(
f = { arg a, b;
    a - b;
};
f.value(5, 3);
)
```

In the above code, evaluating the `f.value(5, 3)` results in the number `2` at the post. Try running `f.value` with two different numbers.

One can also use variables in functions.

*For Example*

```python3
(
f = { arg a, b;
    var firstResult, finalResult;
    firstResult = a + b;
    finalResult = firstResult * 2;
    finalResult;
};
f.value(2, 3);    // this will return (2 + 3) * 2 = 10
)
```
