# Intro to Using Javascript Proxy: Learn to Optimize a Logger

- [Intro to Using Javascript Proxy: Learn to Optimize a Logger](#intro-to-using-javascript-proxy-learn-to-optimize-a-logger)
  - [Create a Simple Logger](#create-a-simple-logger)
  - [Expose the Logger’s Weakness](#expose-the-loggers-weakness)
  - [Create The Proxy Wrapper](#create-the-proxy-wrapper)
  - [Use the FastLogger](#use-the-fastlogger)
  - [Conclusion](#conclusion)

## Create a Simple Logger

Create a logger resembling version 2 of the npm logging library, winston. This logger has a performance flaw that we can resolve with a Proxy.

```tsx
// Logger.ts
export const LogLevel = {
  INFO: 30,
  DEBUG: 20,
};

export class Logger {
 // default log level
  level = LogLevel.INFO;

  info(data: object, msg: string) {
    const logLine = this.serialize(data, msg);
    this.log(LogLevel.INFO, logLine);
  }

  debug(data: object, msg: string) {
    const logLine = this.serialize(data, msg);
    this.log(LogLevel.DEBUG, logLine);
  }

  serialize(data: object, msg: string): string {
    const logObject = { msg, meta: data };
    const serializedLog = JSON.stringify(logObject);
    return serializedLog;
  }

 // log the line to stdout
  log(level: number, json: string): void {
    if (level >= this.level) {
      console.log(json.slice(0, 100)); // don't log huge output
    }
  }
}
```

The key thing to note here is that the data being logged in the `info` and `debug` methods is **first** serialized then logged. This means that if we pepper our code base with thousands of debug statements logging our requests, we're expose ourselves to a serious performance impact.

## Expose the Logger’s Weakness

Let’s create `index.ts` to use the logger at level `INFO` and we’ll see that it still takes a really long time even though level `DEBUG` is not active.

```tsx
// index.ts
import { LogLevel, Logger } from "./Logger";
const logger = new Logger();

// Create an object that's expensive to serialize.
const largeObject: Record<string, any> = {};
new Array(10000 * 1000).fill("").forEach((el: string, index) => {
  largeObject[`${index}`] = el;
});

logger.level = LogLevel.INFO;
logger.info({}, "staring up!");
logger.debug({ largeObject }, "thinking about being an astronaut");
```

## Create The Proxy Wrapper

We can create a Proxy that will wrap the Logger and control how it logs.

```tsx
// FastLogger.ts
import { Logger, LogLevel as _LogLevel } from "./Logger";

export const LogLevel = _LogLevel;

type LogLevelType = "info" | "debug";

const _logger = new Logger();
const proxyHandler: ProxyHandler<Logger> = {
  get(target, propName: keyof Logger) {
    if (isLogMethod(propName)) {
      if (getLogLevelOfMethod(propName as LogLevelType) < target.level) {
        return () => {};
      }
    }
    return target[propName];
  },
};

function isLogMethod(propName: string) {
  return ["info", "debug"].includes(propName);
}
function getLogLevelOfMethod(propName: LogLevelType): number {
  if (propName === "info") return LogLevel.INFO;
  if (propName === "debug") return LogLevel.DEBUG;
  throw new Error("error");
}

export const logger = new Proxy<Logger>(_logger, proxyHandler);
```

The ProxyHandler is an object with a `get` method that will be called any time a property is accessed on the `target` object.

In the `get` method, we first check if this is a call to a log method. Then we check if the `getLogLevelOfMethod()` is less than our currently configured `target.level`. If so, instead of calling the logger’s log method, for example the `debug` method, we simply return a no-op method.

This ensures that we skip the `Logger` function to serialize the crazy large object.

## Use the FastLogger

Now update `index.ts` to use the `FastLogger`.

```tsx
// index.ts
import { LogLevel, logger } from "./FastLogger";

// Compare with...
// import { LogLevel, Logger } from "./Logger";
// const logger = new Logger();

// Create an object that's expensive to serialize.
const largeObject: Record<string, any> = {};
new Array(10000 * 1000).fill("").forEach((el: string, index) => {
  largeObject[+index] = el;
});

logger.level = LogLevel.INFO;
logger.info({}, "staring up!");
logger.debug({ largeObject }, "thinking about being an astronaut");
```

Witness the SPEED. Switch the `level` of the logger to see that debug still works and is still slow. At least now, it's only in debug.

## Conclusion

What other use cases can you think of? Perhaps our next example will be using the Proxy class to switch between two completely different instances of an object in order to manage context. It's quite the advanced use case, but turned out to be a useful one for us.

At [Jemini.io](https://jemini.io) we love finding hacks like this that can quickly and easily unlock gains in our apps. Reach out to find out what we can do for your projects!
