# Timeout for Deno

Create (abortable) promises that resolve/reject when the delay is exceeded.

Very useful for racing strategies using Promise.race or Abort.race.

## Install

    $ deno cache https://deno.land/x/timeout/mod.ts

## Usage with await

```typescript
import { Timeout, TimeoutError } from "https://deno.land/x/timeout/mod.ts"

const timeout = Timeout.wait(1000)

try {
    timeout.abort() // Comment me
    await timeout
} catch(e){
    if(e instanceof AbortSignal)
        // Aborted
}
```

## Usage with callbacks

```typescript
import { Timeout, TimeoutError } from "https://deno.land/x/timeout/mod.ts"

// Will resolve when timed out
Timeout.wait(1000)
    .then(() => console.log("That was long!"))
    .catch(() => console.error("Aborted"))

// Will reject with TimeoutError when timed out
Timeout.error(2000)
    .catch((e) => console.error("Timed out or aborted"))
    .abort() // Comment me
```

## Usage with racing Promises

You can use Timeout.race to race some Promises with a timeout.

Promises are ignored if the delay is exceeded.

```typescript
import { Timeout, TimeoutError } from "https://deno.land/x/timeout/mod.ts"

try{
    const req1 = fetch("...")
    const req2 = fetch("...")

    // Requests are ignored if the delay is exceeded
    const res = await Timeout.race([req1, req2], 1000)
    const json = await res.json()
} catch(e){
    if(e instanceof TimeoutError)
        // Timed out
}
```

## Usage with racing Abortables

You can use Timeout.race to race some Abortables with a timeout.

Abortables are aborted if the delay is exceeded.

```typescript
import { Abort, Abortable } from "https://deno.land/x/abortable/mod.ts"
import { Timeout, TimeoutError } from "https://deno.land/x/timeout/mod.ts"

try{
    const req = Abort.fetch("...")
    const req2 = Abort.fetch("...")

    // Both requests are aborted if the delay is exceeded
    const res = await Timeout.race([req1, req2], 1000)
    const json = await res.json()
} catch(e){
    if(e instanceof TimeoutError)
        // Timed out
}
```

## Test

Run test.ts with the given timeout delay (in milliseconds)

    $ deno run test.ts 1000
    OK

    $ deno run test.ts 100
    Timeout