# Wingtips Changelog / Release Notes

All notable changes to `Wingtips` will be documented in this file. `Wingtips` adheres to [Semantic Versioning](http://semver.org/).

## Why pre-1.0 releases?

Wingtips is used heavily and is stable internally at Nike, however the wider community may have needs or use cases that we haven't considered. Therefore Wingtips will live at a sub-1.0 version for a short time after its initial open source release to give it time to respond quickly to the open source community without ballooning the version numbers. Once its public APIs have stabilized again as an open source project it will be switched to the normal post-1.0 semantic versioning system.

#### 0.x Releases

- `0.12.x` Releases - [0.12.1](#0121), [0.12.0](#0120)
- `0.11.x` Releases - [0.11.2](#0112), [0.11.1](#0111), [0.11.0](#0110)
- `0.10.x` Releases - [0.10.0](#0100)
- `0.9.x` Releases - [0.9.0.1](#0901), [0.9.0](#090)

## [0.12.1](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.12.1)

Released on 2017-09-27.

### Fixed

- Fixed `RequestTracingFilter` to work properly with async servlet requests. Previously the overall request span was
completing instantly when the endpoint method returned rather than waiting for the async request to finish. Tests have
been added that execute against a real running Jetty server equipped with `RequestTracingFilter` and several different
types of servlet endpoints - these tests prevent regression and verify proper Wingtips behavior for the following use 
cases: synchronous/blocking servlet, async servlet, blocking-forwarded servlet (using the request dispatcher to forward 
the request to a different blocking servlet), async-forwarded servlet (using the `AsyncContext.dispatch(...)` method to 
forward the request to a different async servlet), and an async servlet that errors-out due to hitting its request 
timeout.   
    - Fixed by [Nic Munroe][contrib_nicmunroe] in pull request [#42](https://github.com/Nike-Inc/wingtips/pull/42). 
    
### Deprecated

- With the fixes to `RequestTracingFilter`, several methods and classes are no longer needed and have been marked 
`@Deprecated`. They will be removed in a future update. Full deprecation/migration instructions are in the `@deprecated` 
javadocs, but here is a short list:
    - `RequestTracingFilterNoAsync` class - this class is no longer needed since `RequestTracingFilter` is no longer
    abstract. Move to using `RequestTracingFilter` directly.
    - `RequestTracingFilter.isAsyncDispatch(HttpServletRequest)` method - this method is no longer needed or used by 
    `RequestTracingFilter`. It remains to prevent breaking subclasses that overrode it, but it will not be used. 
    - `RequestTracingFilter.ERROR_REQUEST_URI_ATTRIBUTE` field - this field is no longer used. If you still need it for
    some reason you can refer to `javax.servlet.RequestDispatcher.ERROR_REQUEST_URI` instead.   
    - Deprecated by [Nic Munroe][contrib_nicmunroe] in pull request [#42](https://github.com/Nike-Inc/wingtips/pull/42).

### Added

- [Added a warning to the readme](https://github.com/Nike-Inc/wingtips#try_with_resources_warning) about error handling 
and potentially confusing/non-obvious behavior when autoclosing `Span`s with `try-with-resources` statements.
    - Added by [Nic Munroe][contrib_nicmunroe] in pull request [#43](https://github.com/Nike-Inc/wingtips/pull/43).
- Added several sample applications that show how Wingtips works in various frameworks and use cases. See their 
respective readmes for more information:
    - [samples/sample-jersey1](samples/sample-jersey1/)
    - [samples/sample-jersey2](samples/sample-jersey2/)
    - [samples/sample-spring-web-mvc](samples/sample-spring-web-mvc/)
    - Added by [Nic Munroe][contrib_nicmunroe] in pull request [#44](https://github.com/Nike-Inc/wingtips/pull/44). 

## [0.12.0](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.12.0)

Released on 2017-09-18.

### Added

- Added support for reporting 128-bit trace IDs to Zipkin.
    - Done by [Adrian Cole][contrib_adriancole] in pull request [#34](https://github.com/Nike-Inc/wingtips/pull/34).
- Added `Tracer.getCurrentSpanStackSize()`method.
    - Added by [Nic Munroe][contrib_nicmunroe] in pull request [#38](https://github.com/Nike-Inc/wingtips/pull/38).
- Added Java 7 and Java 8 async usage helpers. Please see the 
[async section of the main readme](https://github.com/Nike-Inc/wingtips#async_usage) and the 
[readme for the new Java 8 module](https://github.com/Nike-Inc/wingtips/tree/master/wingtips-java8) for details. 
    - Added by [Nic Munroe][contrib_nicmunroe] in pull request [#39](https://github.com/Nike-Inc/wingtips/pull/39).
- Added `AutoCloseable` implementation to `Span` to support usage in Java `try-with-resources` statements. See the
[try-with-resources section of the readme](https://github.com/Nike-Inc/wingtips#try_with_resources_info) for details.
    - Added by [Nic Munroe][contrib_nicmunroe] in pull request [#40](https://github.com/Nike-Inc/wingtips/pull/40). 

### Updated

- Zipkin modules updated to use Zipkin version `1.16.2`.
    - Updated by [Adrian Cole][contrib_adriancole] in pull request [#34](https://github.com/Nike-Inc/wingtips/pull/34).
- Updated SLF4J API dependency to version `1.7.25`.
    - Updated by [Nic Munroe][contrib_nicmunroe] in pull request [#38](https://github.com/Nike-Inc/wingtips/pull/38). 

### Project Build

- Upgraded to Gradle `4.1`.
    - Done by [Nic Munroe][contrib_nicmunroe] in pull request [#38](https://github.com/Nike-Inc/wingtips/pull/38).
- Updated Logback dependency to `1.2.3` (only affects tests).
    - Updated by [Nic Munroe][contrib_nicmunroe] in pull request [#38](https://github.com/Nike-Inc/wingtips/pull/38).
- Changed Travis CI to use oraclejdk8 when building Wingtips.
    - Done by [Nic Munroe][contrib_nicmunroe] in pull request [#38](https://github.com/Nike-Inc/wingtips/pull/38).    
    

## [0.11.2](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.11.2)

Released on 2016-09-17.

### Updated

- 32 character (128 bit) trace IDs are now gracefully handled by throwing away the high bits (any characters left of 16 characters). This allows the tracing system to more flexibly introduce 128bit trace ID support in the future.
    - Updated by [Adrian Cole][contrib_adriancole] in pull request [#28](https://github.com/Nike-Inc/wingtips/pull/28).
- Zipkin modules updated to use Zipkin version 1.11.1
    - Updated by [Adrian Cole][contrib_adriancole] in pull request [#28](https://github.com/Nike-Inc/wingtips/pull/28).

## [0.11.1](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.11.1)

Released on 2016-09-05.

### Updated

- Zipkin updated to version 1.8.4.
	- Updated by [Adrian Cole][contrib_adriancole] in pull request [#24](https://github.com/Nike-Inc/wingtips/pull/24).

## [0.11.0](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.11.0)

Released on 2016-08-20.

### Fixed

- Trace and span ID generation changed to conform to Zipkin/B3 standards by encoding IDs in lowercase hexadecimal format.
	- Fixed by [Nic Munroe][contrib_nicmunroe] in pull request [#16](https://github.com/Nike-Inc/wingtips/pull/16). For issues [#14](https://github.com/Nike-Inc/wingtips/issues/14) and [#15](https://github.com/Nike-Inc/wingtips/issues/15).

## [0.10.0](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.10.0)

Released on 2016-08-11.

### Added

- Zipkin integration.
	- Added by [Nic Munroe][contrib_nicmunroe] in pull requests [#7](https://github.com/Nike-Inc/wingtips/pull/7), [#8](https://github.com/Nike-Inc/wingtips/pull/8), and [#10](https://github.com/Nike-Inc/wingtips/pull/10). For issue [#9](https://github.com/Nike-Inc/wingtips/issues/9).

## [0.9.0.1](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.9.0.1)

Released on 2016-07-19.

### Added

- Javadoc jar, CONTRIBUTING.md doc, Travis CI badge in readme, Download badge in readme, artifact publishing support.
	- Added by [Nic Munroe][contrib_nicmunroe].

### Fixed

- Broken Zipkin link.
	- Fixed by [Adrian Cole][contrib_adriancole].

## [0.9.0](https://github.com/Nike-Inc/wingtips/releases/tag/wingtips-v0.9.0)

Released on 2016-06-07.

### Added

- Initial open source code drop for Wingtips.
	- Added by [Nic Munroe][contrib_nicmunroe].
	

[contrib_nicmunroe]: https://github.com/nicmunroe
[contrib_adriancole]: https://github.com/adriancole