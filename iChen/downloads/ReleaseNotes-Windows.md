CHANGES LOG
===========

4.2 build 7093.35619 (Releaes)
-----------------------------

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
  to set default time-zone for all connected controllers.

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

- A new command flag `-x` is added to the Server Console.  When
  specified, the console program will loop forever (requiring `Ctrl-C`
  to terminate the program).  Without this flag, the console program
  will exit when `Enter` is pressed.

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


4.1 build 6845.39 (Release)
--------------------------

This is the initial release of version 4.1 of the iChen® Server,
which has been converted to .NET Standard 2.0 and runs under both
Windows (with .NET Framework) and Linux (with .NET Core).

This new version includes countless enhancements to version 4.0 
with more hardware support (e.g. Linux `tty`-style serial ports, 
CDC2000WIN and MPC-7.0 controllers), advanced features (e.g. `RT_TEMP`
messages, broadcasting and relaying mode, Azure IOT Hub support),
significant stability and performance improvements, as well as
numerous bug fixes.




4.0.4 build 6425.20461 (Release)
-------------------------------

### New Feature

- Supports direct connections via serial (COM) ports to Ai/CPC/MPC controllers.

### Bug Fix

- Fixes a bug related to HTTP port reservation that causes setup to fail.

### Enhancement

- The Console is now multi-colored to better highlight errors and warnings.



4.0.3
-----

### New Features

- Supports TLS/SSL3 encryption for Open Protocol™ WebSocket connections.

- Supports HTTPS protocol for web interfaces.

- Supports HTTPS protocol and WS-Security (HTTP) for OPC UA access.



4.0.2
-----

### New Feature

- Allows "locking" of a machine serial number to a particular IP address pattern (regex).
  When a machine `JOIN`'s from a matched IP, the serial number is overwritten.
  This is to handle certain controllers on the field that tend to revert back to the
  default serial number for unknown reasons.



4.0.1.2 build 6362.24844 (Release)
----------------------------------

Release date: 2017-06-02

### Hot Fix

- Fixes a bug which allows multiple controllers to connect simultaneously with the same
  machine serial number.  Now only one machine can be connected with each unique serial
  number. The `JOIN` messages of any duplicating controller will be rejected.

### Enhancements

- Support more firmware versions of the Ai-11 controller.



4.0.1.1 build 6351.32843 (Release)
----------------------------------

Release date: 2017-05-22

### Hot Fix

- Fixes a bug which prevents changing the password of admin users.



4.0.1.0 build 6288.22134 (Release)
----------------------------------

Release date: 2017-03-20

### Hot Fix

- Fixes a bug which blocks storage of data to external database when there is a mold data set
  pending.

### New Feature

- New setup option to automatically login any connecting controller to authorization level 5
  (i.e. line supervisor).  This is particularly useful for Ai-series controllers.



4.0 build 6250.41030 (Official Release)
--------------------------------------

Release date: 2017-02-10

### Description

This is the official iChen System 4.0 release.

### New Feature

- One-click MSI setup package with uninstaller

### Enhancements

- The Terminal is now multi-lingual.



4.0 build 6215.32800 (Release RC6.1)
-----------------------------------

Release date: 2017-01-06

### Description

This is a maintenance release that fixes certain bugs on RC6.

### Breaking Change

- Machine mold setting parameters returned with the `MoldData` message have been fine-tuned
  to include more descriptive variable names. Consequently the names of some variables are
  different from previous versions.



4.0 build 6213.28860 (RC6)
-------------------------

Release date: 2017-01-04

### New Features

- Supports off-line data archiving to a SQL Server database

- Analytics backed by data archived on a SQL Server database



4.0 build 6206.505 (RC5)
-----------------------

Release date: 2016-12-28

### New Features

- All web user interfaces (including server configuration) are now multi-lingual
  (currently supporting English, Traditional and Simplified Chinese)

- Analytics module for running analysis and downloading reports on historical data
  archived in the cloud



4.0 build 6185.23504 (RC4.1)
---------------------------

Release date: 2016-12-10

### Description

This is a maintenance release that fixes a bug on editing user permissions
via the web interface.

### Enhancements

- iChen Server now runs under `LocalService` account and there is no need to provide a
  password during installation

### Bug Fixes

- Fixed a bug preventing changing/modifying user permissions on the web configuration



4.0 build 6173.23190 (RC4)
-------------------------

Release date: 2016-11-25

### Breaking Changes

- `MoldData` message of Open Protocol™ now includes a dictionary of all current mold settings
  (notice that, in order to reduce payload size, variables/fields with zero values are not included)

- Audit trail now gives the name of the corresponding variable/field

### New Features

- iChen Server 4.0 now acts as an OPC UA Server

- New `ReadMoldData` command added to Open Protocol to read the current value of a variable/field,
  and a new `MoldDataValue` response message containing the current value

- All web-based examples are enhanced with the `ReadMoldData` command



4.0 build 6157.37906 (RC3.1)
---------------------------

Release date: 2016-11-09

### Description

This is a maintenance release that fixes a `RequestMoldData`
bug on Ai controllers.



4.0 build 6150.29856 (RC3)
-------------------------

Release date: 2016-11-02

### Upgrades

- Web interface is now based on Bootstrap 4

### Bug Fixes

- Fixed bug on cycle data variable names for Ai controllers

- Fixed bug on cycle data on CDC2000WIN controllers

- Fixed error on invalid password login for Terminal

- Fixed issues on Safari



4.0 build 6109.30398 (RC2)
-------------------------

Release date: 2016-09-22

### Enhancements

- Added support for CDC2000WIN controllers

- All-rounded performance improvements

### Bug Fixes

- Fixed login issues on Chrome

- Fixed missing Intl on Safari

- Fixed Terminal background

- Numerous small bug fixes

### Known Issues

- Some layout formatting on Safari is still screwed up
