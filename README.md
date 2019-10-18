# Helix Log Framework

Logging framework used within the Helix Project.

## Status

[![codecov](https://img.shields.io/codecov/c/github/adobe/helix-log.svg)](https://codecov.io/gh/adobe/helix-log)
[![CircleCI](https://img.shields.io/circleci/project/github/adobe/helix-log.svg)](https://circleci.com/gh/adobe/helix-log)
[![GitHub license](https://img.shields.io/github/license/adobe/helix-log.svg)](https://github.com/adobe/helix-log/blob/master/LICENSE.txt)
[![GitHub issues](https://img.shields.io/github/issues/adobe/helix-log.svg)](https://github.com/adobe/helix-log/issues)
[![LGTM Code Quality Grade: JavaScript](https://img.shields.io/lgtm/grade/javascript/g/adobe/helix-log.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/adobe/helix-log)

## Development


### Build

```bash
npm install
```

### Test

```bash
npm test
```

### Lint

```bash
npm run lint
```

# Usage

```
const { info, error } = require('@adobe/helix-log');

info('This is an info level message ', { foo: 42 }, '; dumping any javascript variable like console.log is supported');
error('You can log exceptions like this', new Error('This is a problem'));
```


# API Reference
## Classes

<dl>
<dt><a href="#BunyanStreamInterface">BunyanStreamInterface</a></dt>
<dd><p>Bunyan stream that can be used to forward any Bunyan messages
to Helix log.</p>
</dd>
<dt><a href="#CoralogixLogger">CoralogixLogger</a></dt>
<dd><p>Sends log messages to the Coralogix logging service.</p>
</dd>
<dt><a href="#LoggerBase">LoggerBase</a></dt>
<dd><p>Can be used as a base class/helper for implementing loggers.</p>
<p>This will first apply the filter, drop the message if the level is
too low or if the filter returned undefined and will then call the
subclass provided this._logImpl function for final processing.</p>
<p>This also wraps the entire logging logic into a promise enabled/async
error handler that will log any errors using the rootLogger.</p>
</dd>
<dt><a href="#FormattedLoggerBase">FormattedLoggerBase</a></dt>
<dd><p>Can be used as a base class/helper for implementing loggers that
require a formatter.</p>
<p>Will do the same processing as <code>LoggerBase</code> and in addition call
the formatter before invoking _logImpl.</p>
</dd>
<dt><a href="#ConsoleLogger">ConsoleLogger</a></dt>
<dd><p>Logger that is especially designed to be used in Node.js
Print&#39;s to stderr; Marks errors, warns &amp; debug messages
with a colored <code>[ERROR]</code>/... prefix.</p>
<p>Formatter MUST produce strings. Default formatter is messageFormatConsole.</p>
</dd>
<dt><a href="#MultiLogger">MultiLogger</a></dt>
<dd><p>Simple logger that forwards all messages to the underlying loggers.</p>
<p>This maintains an ES6 map called loggers. Consumers of this API are
explicitly permitted to mutate this map or replace it all together in
order to add, remove or alter logger.</p>
</dd>
<dt><a href="#FileLogger">FileLogger</a></dt>
<dd><p>Logger specifically designed for logging to unix file descriptors.</p>
<p>This logger is synchronous: It uses blocking syscalls and thus guarantees
that all data is written even if process.exit() is called immediately after
logging.
For normal files this is not a problem as Linux will never block when writing
to files, however for sockets, pipes and ttys this might block the process for a considerable
time.</p>
<p>Formatter MUST produce strings. Default formatter is messageFormatTechnical.</p>
</dd>
<dt><a href="#MemLogger">MemLogger</a></dt>
<dd><p>Logs messages to an in-memory buffer.</p>
<p>Formatter can be anything; default just logs the messages as-is.</p>
</dd>
<dt><a href="#InterfaceBase">InterfaceBase</a></dt>
<dd><p>Can be used as a base class/helper for implementing logging interfaces.</p>
<p>This will make sure that all the required fields are set to their
default value, apply the filter, drop the message if the level is
too low or if the filter returned undefined and will then forward
the message to the logger configured.</p>
<p>This also wraps the entire logging logic into a promise enabled/async
error handler that will log any errors using the rootLogger.</p>
</dd>
<dt><a href="#SimpleInterface">SimpleInterface</a></dt>
<dd><p>The fields interface provides the ability to conveniently
specify both a message and custom fields to the underlying logger.</p>
</dd>
<dt><a href="#LogdnaLogger">LogdnaLogger</a></dt>
<dd><p>Sends log messages to the LogDNA logging service.</p>
<p>Log messages are sent immediately.</p>
</dd>
<dt><a href="#Secret">Secret</a></dt>
<dd><p>Special wrapper that should be used to protect secret values.</p>
<p>Effectively this tries to hide the actual payload so that the
secret has to be explicitly extracted before using it.</p>
<pre><code>const mySecret = new Secret(42); // Create a secret
mySecret.value; // =&gt; 42; extract the value
mySecret.value = 64; // assign a new value</code></pre><p>The secret can only be accessed through the .secret property
in order to make sure that the access is not accidental;
the secret property cannot be iterated over and the secret will
not be leaked when converted to JSON or when printed.</p>
</dd>
</dl>

## Constants

<dl>
<dt><a href="#rootLogger">rootLogger</a></dt>
<dd><p>The logger all other loggers attach to.</p>
<p>Must always contain a logger named &#39;default&#39;; it is very much recommended
that the default logger always be a console logger, since this can serve as a good
fallback in case other loggers fail.</p>
</dd>
<dt><a href="#JsonifyForLog">JsonifyForLog</a></dt>
<dd><p>Trait used to serialize json objects to json. See jsonifyForLog.</p>
</dd>
</dl>

## Functions

<dl>
<dt><a href="#eraseBunyanDefaultFields">eraseBunyanDefaultFields(fields)</a> ⇒ <code>Object</code></dt>
<dd><p>Remove the default fields emitted by buyan.</p>
<p>These fields are added by Bunyan by default but are often not of
much interest (like name, bunyanLevel, or <code>v</code>) or can be easily added
again later for data based loggers where this information might be
valuable (e.g. hostname, pid).</p>
<p>Use like this:</p>
</dd>
<dt><a href="#numericLogLevel">numericLogLevel(name)</a> ⇒ <code>number</code></dt>
<dd><p>This can be used to convert a string log level into it&#39;s
numeric equivalent. More pressing log levels have lower numbers.</p>
</dd>
<dt><a href="#makeLogMessage">makeLogMessage([fields])</a> ⇒ <code><a href="#Message">Message</a></code></dt>
<dd><p>Supplies default values for all the required fields in log messages.</p>
<p>You may pass this a message that is just a string (as opposed to the
usually required array of message components). The message will then automatically
be wrapped in an array.</p>
</dd>
<dt><a href="#tryInspect">tryInspect(what, opts)</a></dt>
<dd><p>Wrapper around inspect that is extremely robust against errors
during inspection.</p>
<p>Specifically designed to handle errors in toString() functions
and custom inspect functions.</p>
<p>If any error is encountered a less informative string than a full
inspect is returned and the error is logged using <code>err()</code>.</p>
</dd>
<dt><a href="#serializeMessage">serializeMessage(msg, opts)</a> ⇒ <code>string</code></dt>
<dd><p>Turns the message field into a string.</p>
</dd>
<dt><a href="#messageFormatSimple">messageFormatSimple(fields)</a> ⇒ <code><a href="#MessageFormatter">MessageFormatter</a></code> | <code>string</code></dt>
<dd><p>Simple message format: Serializes the message and prefixes it with
the log level.</p>
<p>This is used by the MemLogger by default for instance, because it is relatively
easy to test with and contains no extra info.</p>
</dd>
<dt><a href="#messageFormatTechnical">messageFormatTechnical(fields)</a> ⇒ <code><a href="#MessageFormatter">MessageFormatter</a></code> | <code>string</code></dt>
<dd><p>Message format that includes extra information; prefixes each messagej</p>
<p>This is used by FileLogger by default for instance because if you
work with many log files you need that sort of info.</p>
</dd>
<dt><a href="#messageFormatConsole">messageFormatConsole(fields)</a> ⇒ <code><a href="#MessageFormatter">MessageFormatter</a></code> | <code>string</code></dt>
<dd><p>Message format with coloring/formatting escape codes</p>
<p>Designed for use in terminals.</p>
</dd>
<dt><a href="#messageFormatJson">messageFormatJson(fields)</a> ⇒ <code><a href="#MessageFormatter">MessageFormatter</a></code> | <code>Object</code></dt>
<dd><p>Use jsonifyForLog to turn the fields into something that
can be converted to JSON.</p>
</dd>
<dt><a href="#messageFormatJsonString">messageFormatJsonString(fields)</a> ⇒ <code><a href="#MessageFormatter">MessageFormatter</a></code> | <code>Object</code></dt>
<dd><p>Message format that produces &amp; serialize json.</p>
<p>Really just an alias for <code>JSON.stringify(messageFormatJson(fields))</code>.</p>
</dd>
<dt><a href="#__handleLoggingExceptions">__handleLoggingExceptions(fields, logger, code)</a> ⇒ <code><a href="#Message">Message</a></code></dt>
<dd><p>Helper to wrap any block of code and handle it&#39;s async &amp; sync exceptions.</p>
<p>This will catch any exceptions/promise rejections and log them using the rootLogger.</p>
</dd>
<dt><a href="#fatal">fatal(...msg)</a></dt>
<dd><p>Log just a message to the rootLogger using the SimpleInterface.</p>
<p>Alias for <code>new SimpleInterface().log(...msg)</code>.</p>
<p>This is not a drop in replacement for console.log, since this
does not support string interpolation using <code>%O/%f/...</code>, but should
cover most use cases.</p>
</dd>
<dt><a href="#messageFormatJsonStatic">messageFormatJsonStatic(fields)</a> ⇒ <code>Object</code></dt>
<dd><p>Message format used for comparing logs in tests.</p>
<p>Pretty much just messageFormatJson, but removes the time stamp
because that is hard to compare since it is different each time...</p>
<p>If other highly variable fields are added in the future (e.g. id = uuidgen())
these shall be removed too.</p>
</dd>
<dt><a href="#recordLogs">recordLogs(opts, fn)</a> ⇒ <code>string</code></dt>
<dd><p>Record the log files with debug granularity while the given function is running.</p>
<p>While the logger is recording, all other loggers are disabled.
If this is not your desired behaviour, you can use the MemLogger
manually.</p>
</dd>
<dt><a href="#assertLogs">assertLogs(opts, fn, logs)</a></dt>
<dd><p>Assert that a piece of code produces a specific set of log messages.</p>
</dd>
<dt><a href="#recordAsyncLogs">recordAsyncLogs(opts, fn)</a> ⇒ <code>string</code></dt>
<dd><p>Async variant of recordLogs.</p>
<p>Note that using this is a bit dangerous as other async procedures
may also emit log messages while this procedure is running</p>
</dd>
<dt><a href="#assertAsyncLogs">assertAsyncLogs(opts, fn, logs)</a></dt>
<dd><p>Async variant of assertLogs</p>
<p>Note that using this is a bit dangerous as other async procedures
may also emit logs while this async procedure is running.</p>
</dd>
<dt><a href="#jsonifyForLog">jsonifyForLog(what)</a> ⇒ <code>*</code></dt>
<dd><p>jsonify the given data using the JsonifyForLog trait.</p>
<p>Takes any Javascript object and produces an object tree
that only contains JSON compatible objects (objects, arrays,
numbers, bools, strings and such).</p>
<p>This is a no-op if the input is already json compatible.</p>
<p>Note that this is specifically designed to serialize data for structured
logging. This is NOT suitable for proper serialization of JSON; specifically
this may loose information in cases where that makes sense.</p>
<p>Features a default converter for any exception/subclass of Error.</p>
</dd>
</dl>

## Typedefs

<dl>
<dt><a href="#MessageFormatter">MessageFormatter</a> ⇒ <code>*</code></dt>
<dd><p>Most loggers take a message with <code>log()</code>, encode it and write it to some
external resource.</p>
<p>For example, most Loggers (like ConsoleLogger, FileLogger, ...) write to a text-oriented resource, so the message needs to be converted to text. This is what the formatter is for.</p>
<p>Not all Loggers require text; some are field oriented (working with json
compatible data), others like the MemLogger can handle arbitrary javascript
objects, but still provide an optional formatter (in this case defaulted to
the identity function – doing nothing) in case the user explicitly wishes
to perform formatting.</p>
</dd>
</dl>

## Interfaces

<dl>
<dt><a href="#Message">Message</a></dt>
<dd><p>Internally helix log passes these messages around.</p>
<p>Messages are just plain objects with some conventions
regarding their fields:</p>
</dd>
<dt><a href="#Logger">Logger</a></dt>
<dd><p>Loggers are used to write a log message.</p>
<p>These receive a message via their log() method and forward
the message to some external resource or other loggers in
the case of MultiLogger.</p>
<p>Loggers MUST provide a <code>log(message)</code> method accepting log messages.</p>
<p>Loggers SHOULD provide a constructor that can accept options as
the last argument <code>new MyLogger(..args, { ...options })</code>;</p>
<p>Loggers MAY support any arguments or options in addition to the ones
described here.</p>
<p>Loggers SHOULD NOT throw exceptions; instead they should log an error.</p>
<p>Loggers MUST use the optional fields &amp; named options described in this
interface either as specified here, or not at all.</p>
<p>Loggers SHOULD provide a named constructor option &#39;level&#39; and associated field
that can be used to limit logging to messages to those with a sufficiently
high log level.</p>
<p>Loggers SHOULD provide a named constructor option &#39;filter&#39; and the associated field
that can be used to transform messages arbitrarily. This option should default to
the <code>identity()</code> function from ferrum. If the filter returns <code>undefined</code>
the message MUST be discarded.</p>
<p>If loggers send messages to some external resource not supporting the Message
format, they SHOULD also provide an option &#39;formatter&#39; and associated field
that is used to produce the external format. This formatter SHOULD be set
to a sane default.</p>
<p>Helix-log provides some built-in formatters e.g. for plain text, JSON and
for consoles supporting ANSI escape sequences.</p>
</dd>
<dt><a href="#LoggingInterface">LoggingInterface</a></dt>
<dd><p>Helix-Log LoggingInterfaces take messages as defined by some external interface,
convert them to the internal Message format and forward them to a logger.</p>
<p>Some use cases include:</p>
<ul>
<li>Providing a Console.log/warn/error compatible interface</li>
<li>Providing winston or bunyan compatible logging API</li>
<li>Providing a backend for forwarding bunyan or winston logs to helix log</li>
<li>Receiving log messages over HTTP</li>
<li>SimpleInterface and SimpleInterface are used to provide the
<code>info(&quot;My&quot;, &quot;Message&quot;)</code> and <code>info.fields(&quot;My&quot;, &quot;Message&quot;, { cutsom: &quot;fields&quot; })</code>
interfaces.</li>
</ul>
<p>LoggingInterfaces SHOULD provide a constructor that can accept options as
the last argument <code>new MyInterface(..args, { ...options })</code>;</p>
<p>LoggingInterfaces MAY support any arguments or options in addition to the ones
described here.a</p>
<p>LoggingInterfaces MUST use the optional fields &amp; named options described in this
LoggingInterface either as specified here, or not at all.</p>
<p>LoggingInterfaces SHOULD NOT throw exceptions; instead they should log errors using
the global logger.</p>
<p>LoggingInterfaces SHOULD provide a named constructor option/field &#39;logger&#39; that indicates
which destination logs are sent to. This option SHOULD default to the rootLogger.</p>
<p>LoggingInterfaces SHOULD provide a named constructor option &#39;level&#39; and associated field
that can be used to limit logging to messages to those with a sufficiently
high log level.</p>
<p>LoggingInterfaces SHOULD provide a named constructor option &#39;filter&#39; and associated field
that can be used to transform messages arbitrarily. This option should default to
the <code>identity()</code> function from ferrum. If the filter returns <code>undefined</code>
the message MUST be discarded.</p>
</dd>
</dl>

<a name="Message"></a>

## Message
Internally helix log passes these messages around.

Messages are just plain objects with some conventions
regarding their fields:

**Kind**: global interface  
**Example**  
```
const myMessage = {
  // REQUIRED

  level: 'info',
  timestamp: new Date(),

  // OPTIONAL

  // This is what constitutes the actual log message an array
  // of any js objects which are usually later converted to text
  // using `tryInspect()`; we defer this text conversion step so
  // formatters can do more fancy operations (like colorizing certain
  // types; or we could)
  message: ['Print ', 42, ' deep thoughts!']
  exception: {

    // REQUIRED
    $type: 'MyCustomException',
    name; 'MyCustomException',
    message: 'Some custom exception ocurred',
    stack: '...',

    // OPTIONAL
    code: 42,
    causedBy: <nested exception>

    // EXCEPTION MAY CONTAIN ARBITRARY OTHER FIELDS
    ...fields,
  }

  // MESSAGE MAY CONTAIN ARBITRARY OTHER FIELDS
  ...fields,
}
```
<a name="Logger"></a>

## Logger
Loggers are used to write log messages.

These receive a message via their log() method and forward
the message to some external resource or other loggers in
the case of MultiLogger.

Loggers MUST provide a `log(message)` method accepting log messages.

Loggers SHOULD provide a constructor that can accept options as
the last argument `new MyLogger(..args, { ...options })`;

Loggers MAY support any arguments or options in addition to the ones
described here.

Loggers SHOULD NOT throw exceptions; instead they should log an error.

Loggers MUST use the optional fields & named options described in this
interface either as specified here, or not at all.

Loggers SHOULD provide a named constructor option 'level' and associated field
that can be used to limit logging to messages to those with a sufficiently
high log level.

Loggers SHOULD provide a named constructor option 'filter' and associated field
that can be used to transform messages arbitrarily. This option should default to
the `identity()` function from ferrum. If the filter returns `undefined`
the message MUST be discarded.

If loggers send messages to some external resource not supporting the Message
format, they SHOULD also provide an option 'formatter' and associated field
that is used to produce the external format. This formatter SHOULD be set
to a sane default.

Helix-log provides some built-in formatters e.g. for plain text, json and
for consoles supporting ANSI escape sequences.

**Kind**: global interface  
<a name="Logger+log"></a>

### logger.log(fields)
Actually print a log message

Implementations of this MUST NOT throw exceptions. Instead implementors
ARE ADVISED to attempt to log the error using err() while employing some
means to avoid recursively triggering the error. Loggers SHOULD fall back
to logging with console.error.

Even though loggers MUST NOT throw exceptions; users of this method SHOULD
still catch any errors and handle them appropriately.

**Kind**: instance method of [<code>Logger</code>](#Logger)  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

<a name="LoggingInterface"></a>

## LoggingInterface
Helix-Log LoggingInterfaces take messages as defined by some external interface,
convert them to the internal Message format and forward them to a logger.

Some use cases include:

- Providing a Console.log/warn/error compatible interface
- Providing winston or bunyan compatible logging API
- Providing a backend for forwarding bunyan or winston logs to helix log
- Receiving log messages over HTTP
- SimpleInterface and SimpleInterface are used to provide the
  `info("My", "Message")` and `info.fields("My", "Message", { cutsom: "fields" })`
  interfaces.

LoggingInterfaces SHOULD provide a constructor that can accept options as
the last argument `new MyInterface(..args, { ...options })`;

LoggingInterfaces MAY support any arguments or options in addition to the ones
described here.a

LoggingInterfaces MUST use the optional fields & named options described in this
LoggingInterface either as specified here, or not at all.

LoggingInterfaces SHOULD NOT throw exceptions; instead they should log errors using
the global logger.

LoggingInterfaces SHOULD provide a named constructor option/field 'logger' that indicates
which destination logs are sent to. This option SHOULD default to the rootLogger.

LoggingInterfaces SHOULD provide a named constructor option 'level' and associated field
that can be used to limit logging to messages to those with a sufficiently
high log level.

LoggingInterfaces SHOULD provide a named constructor option 'filter' and associated field
that can be used to transform messages arbitrarily. This option should default to
the `identity()` function from ferrum. If the filter returns `undefined`
the message MUST be discarded.

**Kind**: global interface  
<a name="BunyanStreamInterface"></a>

## BunyanStreamInterface
Bunyan stream that can be used to forward any bunyan messages
to helix log.

**Kind**: global class  
**Implements**: [<code>LoggingInterface</code>](#LoggingInterface)  
<a name="CoralogixLogger"></a>

## CoralogixLogger
Sends log messages to the coralogix logging service.

**Kind**: global class  
**Implements**: [<code>Logger</code>](#Logger)  

* [CoralogixLogger](#CoralogixLogger)
    * [new CoralogixLogger(apikey, app, subsystem)](#new_CoralogixLogger_new)
    * [.apikey](#CoralogixLogger+apikey) : [<code>Secret</code>](#Secret)
    * [.app](#CoralogixLogger+app) : <code>string</code>
    * [.subsystem](#CoralogixLogger+subsystem) : <code>string</code>
    * [.host](#CoralogixLogger+host) : <code>string</code>

<a name="new_CoralogixLogger_new"></a>

### new CoralogixLogger(apikey, app, subsystem)

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| apikey | <code>string</code> \| [<code>Secret</code>](#Secret) |  | – Your coralogix api key |
| app | <code>string</code> |  | – Name of the app under which the log messages should be categorized |
| subsystem | <code>string</code> |  | – Name of the subsystem under which |
| [opts.host] | <code>string</code> | <code>&quot;os.hostname()&quot;</code> | The hostname under which to categorize the messages |
| [opts.apiurl] | <code>string</code> | <code>&quot;&#x27;https://api.coralogix.com/api/v1/&#x27;&quot;</code> | where the coralogix api can be found; for testing; where the coralogix api can be found; for testing |

<a name="CoralogixLogger+apikey"></a>

### coralogixLogger.apikey : [<code>Secret</code>](#Secret)
Name of the app under which the log messages should be categorized

**Kind**: instance property of [<code>CoralogixLogger</code>](#CoralogixLogger)  
<a name="CoralogixLogger+app"></a>

### coralogixLogger.app : <code>string</code>
Name of the app under which the log messages should be categorized

**Kind**: instance property of [<code>CoralogixLogger</code>](#CoralogixLogger)  
<a name="CoralogixLogger+subsystem"></a>

### coralogixLogger.subsystem : <code>string</code>
Name of the subsystem under which the log messages should be categorized

**Kind**: instance property of [<code>CoralogixLogger</code>](#CoralogixLogger)  
<a name="CoralogixLogger+host"></a>

### coralogixLogger.host : <code>string</code>
The hostname under which to categorize the messages

**Kind**: instance property of [<code>CoralogixLogger</code>](#CoralogixLogger)  
<a name="LoggerBase"></a>

## LoggerBase
Can be used as a base class/helper for implementing loggers.

This will first apply the filter, drop the message if the level is
too low or if the filter returned undefined and will then call the
subclass provided this._logImpl function for final processing.

This also wraps the entire logging logic into a promise enabled/async
error handler that will log any errors using the rootLogger.

**Kind**: global class  

* [LoggerBase](#LoggerBase)
    * [new LoggerBase(opts)](#new_LoggerBase_new)
    * [.level](#LoggerBase+level) : <code>string</code>
    * [.filter](#LoggerBase+filter) : <code>function</code>

<a name="new_LoggerBase_new"></a>

### new LoggerBase(opts)

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| opts | <code>Object</code> |  | – Optional, named parameters |
| [opts.level] | <code>string</code> | <code>&quot;&#x27;silly&#x27;&quot;</code> | The minimum log level to sent to loggly |
| [opts.filter] | <code>function</code> | <code>identity</code> | Will be given every log message to perform   arbitrary transformations; must return either another valid message object or undefined   (in which case the message will be dropped). |

**Example**  
```
class MyConsoleLogger extends LoggerBase {
  _logImpl(fields) {
    console.log(fields);
  }
}
```
<a name="LoggerBase+level"></a>

### loggerBase.level : <code>string</code>
The minimum log level for messages to be printed.
Feel free to change to one of the available levels.

**Kind**: instance property of [<code>LoggerBase</code>](#LoggerBase)  
<a name="LoggerBase+filter"></a>

### loggerBase.filter : <code>function</code>
Used to optionally transform all messages.
Takes a message and returns a transformed message
or undefined (in which case the message will be dropped).

**Kind**: instance property of [<code>LoggerBase</code>](#LoggerBase)  
<a name="FormattedLoggerBase"></a>

## FormattedLoggerBase
Can be used as a base class/helper for implementing loggers that
require a formatter.

Will do the same processing as `LoggerBase` and in addition call
the formatter before invoking _logImpl.

**Kind**: global class  

* [FormattedLoggerBase](#FormattedLoggerBase)
    * [new FormattedLoggerBase()](#new_FormattedLoggerBase_new)
    * [.formatter](#FormattedLoggerBase+formatter) : [<code>MessageFormatter</code>](#MessageFormatter)

<a name="new_FormattedLoggerBase_new"></a>

### new FormattedLoggerBase()

| Param | Type | Description |
| --- | --- | --- |
| [opts.formatter] | <code>function</code> | In addition to the filter, the formatter   will be used to convert the message into a format compatible with   the external resource. |

**Example**  
```
class MyConsoleLogger extends FormattedLoggerBase {
  _logImpl(payload, fields) {
    console.log(`[${fields.level}]`, payload);
  }
}
```
<a name="FormattedLoggerBase+formatter"></a>

### formattedLoggerBase.formatter : [<code>MessageFormatter</code>](#MessageFormatter)
Formatter used to format all the messages.
Must yield an object suitable for the external resource
this logger writes to.

**Kind**: instance property of [<code>FormattedLoggerBase</code>](#FormattedLoggerBase)  
<a name="ConsoleLogger"></a>

## ConsoleLogger
Logger that is especially designed to be used in node.js
Print's to stderr; Marks errors, warns & debug messages
with a colored `[ERROR]`/... prefix.

Formatter MUST produce strings. Default formatter is messageFormatConsole.

**Kind**: global class  
**Implements**: [<code>Logger</code>](#Logger)  

* [ConsoleLogger](#ConsoleLogger)
    * [new ConsoleLogger()](#new_ConsoleLogger_new)
    * [.stream](#ConsoleLogger+stream) : <code>Writable</code>

<a name="new_ConsoleLogger_new"></a>

### new ConsoleLogger()

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| [opts.stream] | <code>Writable</code> | <code>console._stderr</code> | A writable stream to log to. |

<a name="ConsoleLogger+stream"></a>

### consoleLogger.stream : <code>Writable</code>
Writable stream to write log messages to. Usually console._stderr.

**Kind**: instance property of [<code>ConsoleLogger</code>](#ConsoleLogger)  
<a name="MultiLogger"></a>

## MultiLogger
Simple logger that forwards all messages to the underlying loggers.

This maintains an es6 map called loggers. Consumers of this API are
explicitly permitted to mutate this map or replace it all together in
order to add, remove or alter logger.

**Kind**: global class  
**Implements**: [<code>Logger</code>](#Logger)  
**Parameter**: <code>Sequence&lt;Loggers&gt;</code> loggers – The loggers to forward to.  
<a name="FileLogger"></a>

## FileLogger
Logger specifically designed for logging to unix file descriptors.

This logger is synchronous: It uses blocking syscalls and thus guarantees
that all data is written even if process.exit() is called immediately after
logging.
For normal files this is not a problem as linux will never block when writing
to files, for sockets, pipes and ttys this might block the process for a considerable
time.

Formatter MUST produce strings. Default formatter is messageFormatTechnical.

**Kind**: global class  
**Implements**: [<code>Logger</code>](#Logger)  

* [FileLogger](#FileLogger)
    * [new FileLogger(name)](#new_FileLogger_new)
    * [.fd](#FileLogger+fd) : <code>Integer</code>

<a name="new_FileLogger_new"></a>

### new FileLogger(name)

| Param | Type | Description |
| --- | --- | --- |
| name | <code>string</code> \| <code>Integer</code> | The path of the file to log to   OR the unix file descriptor to log to. |

<a name="FileLogger+fd"></a>

### fileLogger.fd : <code>Integer</code>
The underlying operating system file descriptor.

**Kind**: instance property of [<code>FileLogger</code>](#FileLogger)  
<a name="MemLogger"></a>

## MemLogger
Logs messages to an in-memory buffer.

Formatter can be anything; default just logs the messages as-is.

**Kind**: global class  
**Implements**: [<code>Logger</code>](#Logger)  
<a name="MemLogger+buf"></a>

### memLogger.buf : <code>Array</code>
An array that holds all the messages logged thus far.
May be modified An array that holds all the messages logged thus far.
May be modified.

**Kind**: instance property of [<code>MemLogger</code>](#MemLogger)  
<a name="InterfaceBase"></a>

## InterfaceBase
Can be used as a base class/helper for implementing logging interfaces.

This will make sure that all the required fields are set to their
default value, apply the filter, drop the message if the level is
too low or if the filter returned undefined and will then forward
the message to the logger configured.

This also wraps the entire logging logic into a promise enabled/async
error handler that will log any errors using the rootLogger.

**Kind**: global class  
<a name="new_InterfaceBase_new"></a>

### new InterfaceBase(opts)

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| opts | <code>Object</code> |  | – Optional, named parameters |
| [opts.level] | <code>string</code> | <code>&quot;&#x27;silly&#x27;&quot;</code> | The minimum log level to sent to loggly |
| [opts.filter] | <code>function</code> | <code>identity</code> | Will be given every log message to perform   arbitrary transformations; must return either another valid message object or undefined   (in which case the message will be dropped). |

**Example**  
```
class MyTextInterface extends InterfaceBase {
  logText(str) {
    this._logImpl({ message: [str] });
  }
};

const txt = new MyTextInterface({ logger: rootLogger });
txt.logText("Hello World");
```
<a name="SimpleInterface"></a>

## SimpleInterface
The fields interface provides the ability to conveniently
specify both a message and custom fields to the underlying logger.

**Kind**: global class  
**Implements**: [<code>LoggingInterface</code>](#LoggingInterface)  

* [SimpleInterface](#SimpleInterface)
    * [.fatalFields(...msg)](#SimpleInterface+fatalFields)
    * [.fatal(...msg)](#SimpleInterface+fatal)

<a name="SimpleInterface+fatalFields"></a>

### simpleInterface.fatalFields(...msg)
The *Fields methods are used to log both a message
custom fields to the underlying logger.

The fields object must be present (and error will be thrown
if the last element is not an object) and any fields specified
take precedence over default values.

**Kind**: instance method of [<code>SimpleInterface</code>](#SimpleInterface)  

| Param | Type | Description |
| --- | --- | --- |
| ...msg | <code>\*</code> | The message to write |

**Example**  
```
const { SimpleInterface } = require('helix-log');

const logger = new SimpleInterface();

// Will throw an error because the fields object is missing.
logger.verboseFields("Hello");

// Will log a message 'Fooled!' with level error and the time
// stamp set to the y2k (although it would be more customary
// use logFields to indicate that the log level will be set through
// the fields).
sillyFields("Hello World", {
  level: 'error',
  timestamp: new Date("2000-01-01"),
  message: ["Fooled!"]
});
```
<a name="SimpleInterface+fatal"></a>

### simpleInterface.fatal(...msg)
These methods are used to log just a message with no custom
fields to the underlying logger; similar to console.log.

This is not a drop in replacement for console.log, since this
does not support string interpolation using `%O/%f/...`, but should
cover most use cases.

**Kind**: instance method of [<code>SimpleInterface</code>](#SimpleInterface)  

| Param | Type | Description |
| --- | --- | --- |
| ...msg | <code>\*</code> | The message to write |

<a name="LogdnaLogger"></a>

## LogdnaLogger
Sends log messages to the logdna logging service.

Log messages are sent immediately.

**Kind**: global class  
**Implements**: [<code>Logger</code>](#Logger)  

* [LogdnaLogger](#LogdnaLogger)
    * [new LogdnaLogger(apikey, app, file, opts)](#new_LogdnaLogger_new)
    * [.apikey](#LogdnaLogger+apikey) : [<code>Secret</code>](#Secret)
    * [.app](#LogdnaLogger+app) : <code>string</code>
    * [.host](#LogdnaLogger+host) : <code>string</code>
    * [.apiurl](#LogdnaLogger+apiurl) : <code>string</code>

<a name="new_LogdnaLogger_new"></a>

### new LogdnaLogger(apikey, app, file, opts)

| Param | Type | Description |
| --- | --- | --- |
| apikey | <code>string</code> \| [<code>Secret</code>](#Secret) | – Your logdna api key |
| app | <code>string</code> | – Name of the app under which the log messages should be categorized |
| file | <code>string</code> | – Name of the file(/subsystem) under which the    log messages should be categorized |
| opts | <code>Object</code> | - `host`: Default is the system hostname; The hostname under which to categorize the messages   - `apiurl`: Default `https://logs.logdna.com`; The url under which the logdna api is hosted. |

<a name="LogdnaLogger+apikey"></a>

### logdnaLogger.apikey : [<code>Secret</code>](#Secret)
Name of the app under which the log messages should be categorized

**Kind**: instance property of [<code>LogdnaLogger</code>](#LogdnaLogger)  
<a name="LogdnaLogger+app"></a>

### logdnaLogger.app : <code>string</code>
Name of the app under which the log messages should be categorized

**Kind**: instance property of [<code>LogdnaLogger</code>](#LogdnaLogger)  
<a name="LogdnaLogger+host"></a>

### logdnaLogger.host : <code>string</code>
The hostname under which to categorize the messages

**Kind**: instance property of [<code>LogdnaLogger</code>](#LogdnaLogger)  
<a name="LogdnaLogger+apiurl"></a>

### logdnaLogger.apiurl : <code>string</code>
The url under which the logdna api is hosted.

**Kind**: instance property of [<code>LogdnaLogger</code>](#LogdnaLogger)  
<a name="Secret"></a>

## Secret
Special wrapper that should be used to protect secret values.

Effectively this tries to hide the actual payload so that the
secret has to be explicitly extracted before using it.

```
const mySecret = new Secret(42); // Create a secret
mySecret.value; // => 42; extract the value
mySecret.value = 64; // assign a new value
```

The secret can only be accessed through the .secret property
in order to make sure that the access is not accidental;
the secret property cannot be iterated over and the secret will
not be leaked when converted to json or when printed.

**Kind**: global class  
<a name="rootLogger"></a>

## rootLogger
The logger all other loggers attach to.

Must always contain a logger named 'default'; it is very much recommended
that the default logger always be a console logger; this can serve as a good
fallback in case other loggers fail.

**Kind**: global constant  
**Example**  
```js
// Change the default logger
rootLogger.loggers.set('default', new ConsoleLogger({level: 'debug'}));
```

You should not log to the root logger directly; instead use one of the
wrapper functions `log, fatal, err, warn, info, verbose, debug`; they
perform some additional
<a name="JsonifyForLog"></a>

## JsonifyForLog
Trait used to serialize json objects to json. See jsonifyForLog.

**Kind**: global constant  
<a name="eraseBunyanDefaultFields"></a>

## eraseBunyanDefaultFields(fields) ⇒ <code>Object</code>
Remove the default fields emitted by buyan.

These fields are added by bunyan by default but are often not of
much interest (like name, bunyanLevel, or `v`) or can be easily added
again later for data based loggers where this information might be
valuable (e.g. hostname, pid).

Use like this:

**Kind**: global function  

| Param | Type |
| --- | --- |
| fields | <code>Object</code> | 

**Example**  
```
const bunyan = require('bunyan');
const { BunyanStreamInterface, eraseBunyanDefaultFields } = require('helix-log');

const logger = bunyan.createLogger({name: 'helixLogger'});
logger.addStream(BunyanStreamInterface.createStream({
  filter: eraseBunyanDefaultFields
}));
```
<a name="numericLogLevel"></a>

## numericLogLevel(name) ⇒ <code>number</code>
This can be used to convert a string log level into it's
numeric equivalent. More pressing log levels have lower numbers.

**Kind**: global function  
**Returns**: <code>number</code> - The numeric log level  
**Throws**:

- <code>Error</code> If the given log level name is invalid.


| Param | Type | Description |
| --- | --- | --- |
| name | <code>string</code> | Name of the log level |

<a name="makeLogMessage"></a>

## makeLogMessage([fields]) ⇒ [<code>Message</code>](#Message)
Supplies default values for all the required fields in log messages.

You may pass this a message that is just a string (as opposed to the
usually required array of message components). The message will then automatically
be wrapped in an array.

**Kind**: global function  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| [fields] | <code>Object</code> | <code>{}</code> | User supplied field values; can overwrite   any default values |

<a name="tryInspect"></a>

## tryInspect(what, opts)
Wrapper around inspect that is extremely robust against errors
during inspection.

Specifically designed to handle errors in toString() functions
and custom inspect functions.

If any error is encountered a less informative string than a full
inspect is returned and the error is logged using `err()`.

**Kind**: global function  

| Param | Type | Description |
| --- | --- | --- |
| what | <code>\*</code> | The object to inspect |
| opts | <code>Object</code> | Options will be passed through to inspect.   Note that these may be ignored if there is an error during inspect(). |

<a name="serializeMessage"></a>

## serializeMessage(msg, opts) ⇒ <code>string</code>
Turns the message field into a string.

**Kind**: global function  

| Param | Type | Description |
| --- | --- | --- |
| msg | <code>Array.&lt;\*&gt;</code> \| <code>undefined</code> | – Message components to serialize |
| opts | <code>Object</code> | – Parameters are forwarded to tryInspect() |

<a name="messageFormatSimple"></a>

## messageFormatSimple(fields) ⇒ [<code>MessageFormatter</code>](#MessageFormatter) \| <code>string</code>
Simple message format: Serializes the message and prefixes it with
the log level.

This is used by the MemLogger by default for instance, because it is relatively
easy to test with and contains no extra info.

**Kind**: global function  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

<a name="messageFormatTechnical"></a>

## messageFormatTechnical(fields) ⇒ [<code>MessageFormatter</code>](#MessageFormatter) \| <code>string</code>
Message format that includes extra information; prefixes each messagej

This is used by FileLogger by default for instance because if you
work with many log files you need that sort of info.

**Kind**: global function  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

<a name="messageFormatConsole"></a>

## messageFormatConsole(fields) ⇒ [<code>MessageFormatter</code>](#MessageFormatter) \| <code>string</code>
Message format with coloring/formatting escape codes

Designed for use in terminals.

**Kind**: global function  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

<a name="messageFormatJson"></a>

## messageFormatJson(fields) ⇒ [<code>MessageFormatter</code>](#MessageFormatter) \| <code>Object</code>
Use jsonifyForLog to turn the fields into something that
can be converted to json.

**Kind**: global function  
**Oaram**: [<code>Message</code>](#Message) message the log message  

| Param | Type | Description |
| --- | --- | --- |
| fields | <code>\*</code> | additional log fields |

<a name="messageFormatJsonString"></a>

## messageFormatJsonString(fields) ⇒ [<code>MessageFormatter</code>](#MessageFormatter) \| <code>Object</code>
Message format that produces & serialize json.

Really just an alias for `JSON.stringify(messageFormatJson(fields))`.

**Kind**: global function  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

<a name="__handleLoggingExceptions"></a>

## \_\_handleLoggingExceptions(fields, logger, code) ⇒ [<code>Message</code>](#Message)
Helper to wrap any block of code and handle it's async & sync exceptions.

This will catch any exceptions/promise rejections and log them using the rootLogger.

**Kind**: global function  
**Access**: package  

| Param | Type | Description |
| --- | --- | --- |
| fields | <code>Object</code> | Fields to set in any error message (usually indicates which logger   was used) |
| logger | [<code>Logger</code>](#Logger) | the logger to wrap |
| code | <code>function</code> | The code to wrap |

<a name="fatal"></a>

## fatal(...msg)
Log just a message to the rootLogger using the SimpleInterface.

Alias for `new SimpleInterface().log(...msg)`.

This is not a drop in replacement for console.log, since this
does not support string interpolation using `%O/%f/...`, but should
cover most use cases.

**Kind**: global function  

| Param | Type | Description |
| --- | --- | --- |
| ...msg | <code>\*</code> | The message to write |

<a name="messageFormatJsonStatic"></a>

## messageFormatJsonStatic(fields) ⇒ <code>Object</code>
Message format used for comparing logs in tests.

Pretty much just messageFormatJson, but removes the time stamp
because that is hard to compare since it is different each time...

If other highly variable fields are added in the future (e.g. id = uuidgen())
these shall be removed too.

**Kind**: global function  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

<a name="recordLogs"></a>

## recordLogs(opts, fn) ⇒ <code>string</code>
Record the log files with debug granularity while the given function is running.

While the logger is recording, all other loggers are disabled.
If this is not your desired behaviour, you can use the MemLogger
manually.

**Kind**: global function  
**Returns**: <code>string</code> - The logs that where produced by the codee  

| Param | Type | Description |
| --- | --- | --- |
| opts | <code>Object</code> | – optional first parameter; options passed to MemLogger |
| fn | <code>function</code> | The logs that this code emits will be recorded. |

**Example**  
```
recordLogs(() => {
  info('Hello World');
  err('Nooo');
});
```

will return something like this:

```
[
  { level: 'info', message: 'Hello World', timestamp: '...' },
  { level: 'error', message: 'Noo', timestamp: '...' }
]
```
<a name="assertLogs"></a>

## assertLogs(opts, fn, logs)
Assert that a piece of code produces a specific set of log messages.

**Kind**: global function  

| Param | Type | Description |
| --- | --- | --- |
| opts | <code>Object</code> | – optional first parameter; options passed to MemLogger |
| fn | <code>function</code> | The logs that this code emits will be recorded. |
| logs | <code>string</code> |  |

**Example**  
```
const { assertLogs, info, err } = require('@adobe/helix-shared').log;

assertLogs(() => {
  info('Hello World');
  err('Nooo');
}, [
  { level: 'info', message: 'Hello World' },
  { level: 'error', message: 'Noo' }
]);
```
<a name="recordAsyncLogs"></a>

## recordAsyncLogs(opts, fn) ⇒ <code>string</code>
Async variant of recordLogs.

Note that using this is a bit dangerous as other async procedures
may also emit log messages while this procedure is running

**Kind**: global function  
**Returns**: <code>string</code> - The logs that where produced by the code  

| Param | Type | Description |
| --- | --- | --- |
| opts | <code>Object</code> | – optional first parameter; options passed to MemLogger |
| fn | <code>function</code> | The logs that this code emits will be recorded. |

<a name="assertAsyncLogs"></a>

## assertAsyncLogs(opts, fn, logs)
Async variant of assertLogs

Note that using this is a bit dangerous as other async procedures
may also emit logs while this async procedure is running.

**Kind**: global function  

| Param | Type | Description |
| --- | --- | --- |
| opts | <code>Object</code> | – optional first parameter; options passed to MemLogger |
| fn | <code>function</code> | The logs that this code emits will be recorded. |
| logs | <code>string</code> |  |

<a name="jsonifyForLog"></a>

## jsonifyForLog(what) ⇒ <code>\*</code>
jsonify the given data using the JsonifyForLog trait.

Takes any javascript object and produces an object tree
that only contains json compatible objects (objects, arrays,
numbers, bools, strings and such).

This is a no-op if the input is already json compatible.

Note that this is specifically designed to serialize data for structured
logging. This is NOT suitable for proper serialization of json; specifically
this may loose information in cases where that makes sense.

Features a default converter for any exception/subclass of Error.

**Kind**: global function  
**Returns**: <code>\*</code> - Json compatible object  
**Throws**:

- TraitNotImplemented If any object in the given object tree
  can not be converted to json-compatible


| Param | Type | Description |
| --- | --- | --- |
| what | <code>\*</code> | The object to convert |

<a name="MessageFormatter"></a>

## MessageFormatter ⇒ <code>\*</code>
Most loggers take a message with `log()`, encode it and write it to some
external resource.

E.g. most Loggers (like ConsoleLogger, FileLogger, ...) write to a text
oriented resource, so the message needs to be converted to text. This is
what the formatter is for.

Not all Loggers require text; some are field oriented (working with json
compatible data), others like the MemLogger can handle arbitrary javascript
objects, but still provide an optional formatter (in this case defaulted to
the identity function – doing nothing) in case the user explicitly wishes
to perform formatting.

**Kind**: global typedef  
**Returns**: <code>\*</code> - Whatever kind of type the Logger needs. Usually a string.  

| Param | Type |
| --- | --- |
| fields | [<code>Message</code>](#Message) | 

