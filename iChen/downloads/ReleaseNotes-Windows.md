CHANGES LOG
===========

4.1.0.0 build 6845.39 (Release)
-------------------------------

This is the initial release of version 4.1 of the iChen Server,
which has been converted to .NET Standard 2.0 and runs under both
Windows (with .NET Framework) and Linux (with .NET Core).

This new version includes countless enhancements to version 4.0 
with more hardware support (e.g. Linux tty-style serial ports, 
CDC2000WIN and MPC-7.0 controllers), advanced features (e.g. RT_TEMP
messages, broadcasting and relaying mode, Azure IOT Hub support),
significant stability and performance improvements, as well as
numerous bug fixes.




4.0.4.0 build 6425.20461 (Release)
----------------------------------

New Feature:

- Supports direct connections via serial (COM) ports to Ai/CPC/MPC controllers.

Bug Fix:

- Fixes a bug related to HTTP port reservation that causes setup to fail.

Enhancement:

- The Console is now multi-colored to better highlight errors and warnings.



4.0.3.0
-------

New Features:

- Supports TLS/SSL3 encryption for OpenProtocol WebSocket connections.

- Supports HTTPS protocol for web interfaces.

- Supports HTTPS protocol and WS-Security (HTTP) for OPC UA access.



4.0.2.0
-------

New Feature:

- Allows "locking" of a machine serial number to a particular IP address pattern (regex).
  When a machine JOIN's from a matched IP, the serial number is overwritten.
  This is to handle certain controllers on the field that tend to revert back to the
  default serial number for unknown reasons.



4.0.1.2 build 6362.24844 (Release)
----------------------------------

Release date: 2017-06-02

Hot Fix:

- Fixes a bug which allows multiple controllers to connect simultaneously with the same
  machine serial number.  Now only one machine can be connected with each unique serial
  number. The JOIN messages of any duplicating controller will be rejected.

Enhancements:

- Support more firmware versions of the Ai-11 controller.



4.0.1.1 build 6351.32843 (Release)
----------------------------------

Release date: 2017-05-22

Hot Fix:

- Fixes a bug which prevents changing the password of the admin user.



4.0.1.0 build 6288.22134 (Release)
----------------------------------

Release date: 2017-03-20

Hot Fix:

- Fixes a bug which blocks storage of data to external database when there is a mold data set
  pending.

New Feature:

- New setup option to automatically login any connecting controller to authorization level 5
  (i.e. line supervisor).  This is particularly useful for Ai-series controllers.



4.0.0.0 build 6250.41030 (Official Release)
-------------------------------------------

Release date: 2017-02-10

Description:

This is the official iChen System 4.0 release.

New Feature:

- One-click MSI setup package with uninstaller

Enhancements:

- The Terminal is now multi-lingual.



4.0.0.0 build 6215.32800 (Release RC6.1)
----------------------------------------

Release date: 2017-01-06

Description:

This is a maintenance release that fixes certain bugs on RC6.

Breaking Change:

- Machine mold setting parameters returned with the MoldData message have been fine-tuned
  to include more descriptive variable names. Consequently the names of some variables are
  different from previous versions.



4.0.0.0 build 6213.28860 (RC6)
------------------------------

Release date: 2017-01-04

New Features:

- Supports off-line data archiving to a SQL Server database

- Supports Analytics backed by data archived on a SQL Server database



4.0.0.0 build 6206.505 (RC5)
----------------------------

Release date: 2016-12-28

New Features:

- All web user interfaces (including server configuration) are now multi-lingual
  (currently supporting English, Traditional and Simplified Chinese)

- Analytics module for running analysis and downloading reports on historical data
  archived in the cloud



4.0.0.0 build 6185.23504 (RC4.1)
--------------------------------

Release date: 2016-12-10

Description:

This is a maintenance release that fixes a bug on editing user permissions
via the web interface.

Enhancements:

- iChen Server now runs under LocalService account and there is no need to provide a
  password during installation

Bug Fixes:

- Fixed a bug preventing changing/modifying user permissions on the web configuration



4.0.0.0 build 6173.23190 (RC4)
------------------------------

Release date: 2016-11-25

Breaking Changes:

- MoldData message of Open Protocol now includes a dictionary of all current mold settings
  (notice that, in order to reduce payload size, variables/fields with zero values are not included)

- Audit trail now gives the name of the corresponding variable/field

New Features:

- iChen Server 4.0 now acts as an OPC UA Server

- New ReadMoldData command added to Open Protocol to read the current value of a variable/field,
  and a new MoldDataValue response message containing the current value

- All web-based examples are enhanced with the ReadMoldData command



4.0.0.0 build 6157.37906 (RC3.1)
--------------------------------

Release date: 2016-11-09

Description:

This is a maintenance release that fixes a RequestMoldData
bug on Ai controllers.



4.0.0.0 build 6150.29856 (RC3)
------------------------------

Release date: 2016-11-02

Upgrades:

- Web interface is now based on Bootstrap 4

Bug Fixes:

- Fixed bug on cycle data variable names for Ai controllers

- Fixed bug on cycle data on CDC2000WIN controllers

- Fixed error on invalid password login for Terminal

- Fixed issues on Safari



4.0.0.0 build 6109.30398 (RC2)
------------------------------

Release date: 2016-09-22

Enhancements:

- Added support for CDC2000WIN controllers

- All-rounded performance improvements

Bug Fixes:

- Fixed login issues on Chrome

- Fixed missing Intl on Safari

- Fixed Terminal background

- Numerous small bug fixes

Known Issues:

- Some layout formatting on Safari is still screwed up
