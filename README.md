Detect if your application needs multithreading. Simply by checking event-loop delay.

This module tries to calculate event-loop delay with the help of an interval. Since an interval is based on time and process ticks, it's easy to detect a thread block (actually event-loop thread).

Usage
```js
// Common
const { ThreadBlockDetect } = require("@semizero/thread-block-detect");
// or Module
import { ThreadBlockDetect } from "@semizero/thread-block-detect";


// parameterless
new ThreadBlockDetect();

// pass max valid amount of delay in milliseconds (default: 20)
new ThreadBlockDetect(100);

// pass callback; notice about thread blocks (delay is in milliseconds)
new ThreadBlockDetect(delay => { /* do something */ });

// pass logger; e.g. console, winston & ... (default is console)
new ThreadBlockDetect(console);

// you can also clear interval by calling `dispose`
// it's not necessary to dispose since `.unref()` will be called on interval creation if available
const instance = new ThreadBlockDetect();
instance.dispose();
```
This will detect thread blocks in the thread <ins>where it's constructed and running</ins>.


If there are thread blocks <ins>regularly</ins> or it's <ins>rare but massive</ins> (e.g. >5000ms), these are the solutions to offload tasks:
- Web workers (browsers)
- Worker threads or child process (nodejs)  
 [Piscina](https://www.npmjs.com/package/piscina) is recommended for node 16 & newer

How does it work?  
1. Get and save current time (using performance.now)
2. Initialize an interval which runs the detector every 1000ms

Detector:  
1. Get current time
2. Calculate delay (`current - previous`)
3. Check if delay is smaller or bigger than a valid delay (e.g. 20ms) because delay cannot be 0 (single-threaded runtime architecture)
4. Save current time
