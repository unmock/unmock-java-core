# Unmock

[npmjs]: https://www.npmjs.com/package/unmock
[build]: https://circleci.com/gh/unmock/unmock-js
[coverage]: https://coveralls.io/github/unmock/unmock-js

Public API mocking for Java, Scala, Kotlin, Clojure, Groovy and friends.

Unmock can be used to test modules that perform requests to third-party APIs like Hubspot, SendGrid, Behance, and hundreds of other public APIs.

`unmock-java-core` is designed to be a base package for implementations of unmock in a variety of JVM frameworks and languages, ie `okhttp`, `spring` and `volley`.

The ultimate goal of unmock is to provide a semantically and functionally adequate mock of the internet.

## How does it work?

The `unmock-java-core` library works by providing utility functions that, when used with a networking, inject JIT or persisted mocks of hundreds of APIs.  Developers can use these utility functions directly, or they can use one of several framework implementations including:

- `unmock-okhttp`
- `unmock-spring`
- `unmock-volley`

## Install

```gradle
dependencies {
  compile group: 'io.unmock', name: 'core', version: '0.0.0';
}
```

### Java version support

Unmock is written in Java 1.8.  It is currently untested for older versions of Java but may work for them as well.

## unmock.io

The URLs printed to the command line are hosted by [unmock.io](https://www.unmock.io).  You can consult the documentation about that service [here](https://www.unmock.io/docs).

## Options

### Saving mocks

All mocks can be saved to a folder called `.unmock` in your project's root directory by providing a `Save` instance to the `UnmockOptions` object like so:

```java
UnmockOptions unmockOptions = new UnmockOptions.Builder()
                .save(Save.ofBoolean(true))
                .build();
```

The `Save` object can be set in one of two ways:

- `Save.ofBoolean` : set to `true` to save all mocks, `false` to save none.
- `Save.ofCollection` : provide a collection of mock hashes to save.  Each mock generated by unmock has a unique hash, accessible in the logs of your build or through the unmock dashboard.

### Ignoring aspects of a mock

Sometimes, you would like for two mocks of slightly API calls to be treated as equivalent by unmock.  For example, you may want all `GET` calls to the same path with different headers to be served the same mock.  To do this, set the `ignore` field of the `UnmockOptions` object.  Note that the `ignore` field **must** be valid JSON.  To make sure this is the case, we recommend using a JSON generator like [GSON](https://github.com/google/gson).

```java
UnmockOptions unmockOptions = new UnmockOptions.Builder()
                .ignore(new Gson().toJson("path"))
                .build();
```

The following fields may be ignored:

* `headers`: the headers of the request
* `hostname`: the hostname of the request
* `method`: the method of the request (ie GET, POST, PUT, DELETE). Note that this is *case insensitive*!
* `path`: the path of the request
* `story`: the story of the request, meaning its order in a series of requests

Ignore evaluates regular expressions, so you can also pass
`"headers|path"` instead of `["headers", "path"]`.  Furthermore,
to ignore nested headers, pass an object such as
`{headers: "Authorization" }`, or to match against the value of
a header, `{headers: { Authorization: "Bearer *" }}`.

The default ignore is `{headers: "\w*User-Agent\w*"}` and will be incorporated into the ignore pattern unless ignore is explicitly set to an empty object, ie `unmock({ ignore: {} })`.

### Adding a signature

Sometimes, it is useful to sign a mock with a unique signature.  This is useful, for example, when AB testing code that should serve two different mocks for the same endpoint in otherwise similar conditions.  Do this, use the `signature` field of the `UnmockOptions` object:

```java
UnmockOptions unmockOptions = new UnmockOptions.Builder()
                .signature("my-secret-signature")
                .build();
```

### Whitelisting API

If you do not want a particular API to be mocked, whitelist it.

```java
UnmockOptions unmockOptions = new UnmockOptions.Builder()
                .whitelist(Arrays.toList("api.typeform.com"))
                .build();
```

### unmock.io tokens

If you are subscribed to the [unmock.io](https://www.unmock.io) service, you can pass your unmock token directly to the `UnmockOptions` instance.

```java
UnmockOptions unmockOptions = new UnmockOptions.Builder()
                .token("my-secret-token")
                .build();
```

At a certain point this becomes a bit tedious, at which point you will want to create a credentials file.  See [unmock.io/docs](https://www.unmock.io/docs) for more information on credential files.

## Contributing

Thanks for wanting to contribute! Take a look at our [Contributing Guide](CONTRIBUTING.md) for notes on our commit message conventions and how to run tests.

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md).
By participating in this project you agree to abide by its terms.

## License

[MIT](LICENSE)

Copyright (c) 2018‚ 2019 [Meeshkan](http://meeshkan.com) and other [contributors](https://github.com/unmock/unmock-js/graphs/contributors).
