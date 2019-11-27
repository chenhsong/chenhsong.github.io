CHANGES LOG
===========

4.3.1 build 7265.38116 (Hot Fix)
--------------------------------

### Bug Fixes

- Fixes a bug that causes batch message sends to Azure IOT Hub to fail
  by reverting the library to a previous working version.


4.3 build 7211.23391 (Bug Fix Release)
-------------------------------------

### System

#### Bug Fixes

- Certain crashes due to undisposed messages are avoided.

### `Open Protocol`

#### Bug Fixes
  
- `TypeName` field of `Message` is now thread-safe.

- `OperatorInfo` now takes zero in the `OperatorId` field indicating
  that the operator is not found.


4.2 build 7093.35619 (Release)
------------------------------

### System

#### New Features

- The database and web server can be disabled (via new configuration
  settings `DatabaseEnable` and `iChenWeb_Enable`). Compact builds
  without these components can be made.

- Cycle data records stored to the cloud or an external archive
  database can optionally be tagged with a unique ID for tracking
  purposes.

- A new, text-based, Simple Message Protocol (SMP) is added for
  simple integration with third-party controllers.

- A new configuration setting `DefaultControllerTimeZone` is added
  to set default time-zone for all connected controllers.  This is
  primarily to handle certain embedded O/S environments (especially
  embedded Linux) where there are no system built-in time-zone feature.

- A new configuration setting `StandAlone` is added (default `true`)
  to specify that this server is a stand-alone instance and thus
  no cache sharing, broadcasting, and synchronization etc. are
  necessary. This greatly simplifies the operation of the server
  when running in a single-instance deployment.

- The configuration file now allows a controller serial number to
  map directly to another without going through address mapping.

- Enabling HTTPS (via the `Https_Certificate_File` and
  `Https_Certificate_Hash` configuration settings) now exposes an
  HTTPS end-point (i.e. `https://`) for the web interface instead
  of HTTP (`http://`).

- A new configuration setting `iChenWeb_Http_Redirection_Port` is
  added to expose an optional HTTP end-point when HTTPS is enabled.
  Any request made to this port will be redirected to the main
  web interface port which serves HTTPS.

- Enabling HTTPS turns on support for HTTP/2 for clients that
  support it.

- Brotli compression is added for clients that support it.
  
#### Enhancements

- Memory usage is reduced and execution speed increased via `System.Span`
  and `System.Memory` and reduced allocations of arrays.

- When a distributed shared cache is used (instead of the default
  in-memory shared cache), data for connected controllers will
  be cached locally to speed up read access.

- When a distributed shared cache is used (instead of the default
  in-memory shared cache), controllers will sync to it
  periodically, just in case a prior cache write fails.

- Using Azure IOT Hub as a distributed shared cache is refined to
  avoid hitting on the IOT Hub's throttle limits.

- Error messages for the Azure IOT Hub (when used as a distributed
  shared cache) are enhanced.

- New configuration settings `CloudStore_BatchSize` and
  `CloudStore_CycleData_BatchSize` to enable fine-tuning the
  upload batch sizes for Azure Table storage.

- Warnings will be displayed when certain configuration options are
  set wrong (e.g. enabling Web API when a compact build is used).

- Use ASP.NET Core cookies authentication middleware.

- The configuration setting `DataStore` for specifying an external
  archive database can now hold a raw ODBC connection string itself
  instead of only a connection name. This simplifies provisioning
  of the archive database.

- A new `Variable` field is added to `MoldSetting` which contains
  the CRC32 hash for the mold setting variable's name, if any.
  This allows directly mapping a mold setting offset to a
  particular variable.

- Controller variable mappings now operate off external text-based
  mapping tables, enabling easy updates without touchinng system
  programs.
  
- Cycle data variable mapping table for the `CBmold` series of
  controllers is added.

#### Bug Fixes

- Typo's preventing proper installation of the executable config
  files are fixed.

- On certain controllers using Unicode (e.g. CDC2000WIN), bugs
  that cause the reading of text names (e.g. Mold ID) to stop at
  a zero byte are now fixed.

### Breaking Changes

- C# 7.3 or higher is now required.

### Log4Net

#### Enhancements

- `log4net.config` is now watched for changes and the logging level
  will adjust dynamically.

- Customized logger names can be used, in the format `#serial`, for
  example `#123456`, and these will automatically be used for any
  controller with that serial number.

#### Breaking Changes

- A new logging level, `TRACE`, is added. Some detailed log messages
  that used to show up for the `DEBUG` logging level now show up
  for `TRACE` instead. This is to reduce log cluttering.
  
- A new logging level, `VERBOSE`, is added for fine-tuned logging.
  It will display, among other detailed information, specific data/bytes
  sent/received over the wire.
  
- The configuration setting `DebugMessages` is removed.  Use the
  `VERBOSE` logging level instead.

- Most `ALIVE` and `ACTION_CHANGED` messages now show up only for
  the `TRACE` logging level to reduce log cluttering.
  
- Most `Alive` and `ControllerAction` `Open Protocol` messages
  now show up only when for the `TRACE` logging level to reduce log
  cluttering.

### `Open Protocol`

#### Bug Fixes

- A WebSocket connection is now properly terminated when an error
  is encountered.  Previously, some errors might cause the connection
  to be kept alive forever on the server.
  
- Previously, when a connected client dropped off suddenly without
  going through the disconnect handshake, the WebSocket connection
  would be kept alive on the server forever.  Now, the `RecvAliveCounter`
  server configuration setting is strictly observed; when the server
  does not receive an `Alive` message from the client (which will be
  the case when the client's connection fails) within the `RecvAliveCounter`
  window, the server will assume that the connection is dead and
  will terminate it.

#### Enhancements

- `RequestControllersListMessage` can now take an optional
  `ControllerId` parameter which, when set, limits the returned list
  to only the controller with the specified ID.

- Setting `Field` to null or an empty/white-space string in
  `ReadMoldDataMessage` now returns a `MoldDataMessage` with the
  full set of buffered mold setting values instead of causing an
  exception.  Under this scenario, a `MoldDataValueMessage` will
  not be returned.

- A `State` field is added to `ControllerStateMessage` which may
  hold a `StateValues` object containing the state values
  (e.g. op mode, job mode etc.) of the controller at the time
  of the event.  This is commonly used for analytic purposes.

- Enabling HTTPS (via the `Https_Certificate_File` and
  `Https_Certificate_Hash` configuration settings) now also secures
  the Open Protocol WebSocket end-point (i.e. `wss://` instead of
  `ws://`).
  
- Much more complete logging is provided for messages.

#### Breaking Changes

- `ControllerType` field in `Controller` is changed to `String` in
  order to accommodate future controller types. 
  
- The `ControllerTypes` `enum` is removed.

- `JSON` representation of `ControllerStateMessage` is refined.

- The `RecvAliveCounter` server configuration setting must now be
  strictly observed.  An `Alive` message must be received by the
  server within the `RecvAliveCounter` window, or the WebSocket
  connection will be assumed dead and the server may unilaterally
  terminate the connection.  Previously a server did not time-out
  a connection even when no `Alive` message is received.


4.1 build 6760.32845 (Initial Release)
--------------------------------------

This is the initial release of version 4.1 of the iChen® Server,
which has been converted to .NET Standard 2.0 and runs under both
Windows and Linux (with .NET Core).

There are also countless enhancements to version 4.0 with more
hardware support (e.g. Windows-style COM-ports, Linux `tty`-style
serial ports, CDC2000WIN), advanced features (e.g. broadcasting
and relaying mode, Azure IOT Hub support), and significant
performance improvements.
