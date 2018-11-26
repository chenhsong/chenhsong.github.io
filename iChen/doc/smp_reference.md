Simple Message Protocol (SMP) for iChen® System 4.0
===================================================

Protocol Version: 1.0.0  
Revision Date: 2018-10-01


Preamble
--------

The Simple Message Protocol (SMP) is a text-based, single-direction protocol
designed for third-party systems to make a simple data connection to an
iChen® 4.0 Server.

Connection Types
----------------

Any transport mechanism that supports sending a simple `LF`-delimited ASCII
text stream can be used for SMP.  Currently, the iChen® 4.0 Server supports
the following connection methods:

* TCP/IP
* Serial port
* WebSocket (IETF RFC 6455)
* MQTT
  

Message Syntax
--------------

All messages are sent in ASCII characters, with the vertical _pipe_ character `|` delimiting fields and a _line-feed_ `LF` character signaling the end of a message.

_MESSAGE_ `|` _SERIAL_ `|` _SESSION_ `|` _COUNTER_ `|` _SEND TIME_ `|` _EVENT TIME_ `|` _Data_ `LF`

Message Fields
--------------

|Field|Description|Type|Format|Comments|
|-----|-----------|----|------|--------|
|MESSAGE|Message type|Text|[`HEARTBEAT`](#heartbeat), [`CONNECT`](#connect), [`DISCONNECT`](#disconnect), [`CYCLEDATA`](#cycledata), [`ALARMON`](#alarmon), [`ALARMOFF`](#alarmoff), [`OPERATOR`](#operator), [`OPMODE`](#opmode), [`JOBMODE`](#jobmode), [`VAR`](#var), [`AUDIT`](#audit), [`MOLDNAME`](#moldname), [`ALARMS`](#alarms), [`MOLDDATA`](#molddata), [`VARIABLES`](#variables)||
|SERIAL|Serial number of the controller|Unsigned integer|`\d+`||
|SESSION|Unique session ID|Text|`[A-Za-z0-9_-]+`|Unique per connection session|
|COUNTER|Sequence number|Unsigned integer|`\d+`|Increment per message, starting from 1|
|SEND TIME|Date/time the this message is sent from the controller|Text|ISO 8601 `yyyy-MM-ddTHH:mm:ss.fff+zz:zz` or Unix time `\d+`|Optional|
|EVENT TIME|Date/time the event actually occurred|Text|ISO 8601 `yyyy-MM-ddTHH:mm:ss.fff+zz:zz` or Unix time `\d+`||


HEARTBEAT
---------

**Description:** I'm still alive!  
**Periodic:**    Once every _n_ seconds (_n_ should be configurable)  
**Data:**        None

CONNECT
-------

**Description:** Connect to an iChen® 4.0 Server  
**Send:**        First message when connection is made  
**Data:**
  
_MODEL_ `|` _TYPE_ `|` _PROTOCOL VERSION_ `|` _FIRMWARE VERSION_ `|` _PROGRAM VERSION_ `|` _NAME_

|Field|Description|Type|Format|
|-----|-----------|----|------|
|MODEL|Model of the machine|Text|UTF-8, avoid `\|`|
|PROTOCOL VERSION|Version of the SMP protocol|Text|`\d+\.\d+\.\d+`|
|FIRMWARE VERSION|Version of the controller's firmware|Text|`\d+\.\d+\.\d+`|
|PROGRAM VERSION|Version of the controller's PLC program|Text|`\d+\.\d+\.\d+`|
|NAME|Name of the machine|Text|UTF-8, avoid `\|`|

Follow-on: The [`OPMODE`](#opmode), [`JOBMODE`](#jobmode), [`OPERATOR`](#operator), [`ALARMS`](#alarms), [`VARIABLES`](#variables) and [`MOLDDATA`](#molddata) messages should be sent immediately after this message to prime the initial states of the controller.

DISCONNECT
----------

**Description:** Disconnect from the iChen® 4.0 Server  
**Send:**        Last message before connection is terminated  
**Data:**        None
  
CYCLEDATA
---------

**Description:** Cycle data values  
**Send:**        After each cycle  
**Data:**
  
_VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ ...
	
|Field|Description|Type|Format|
|-----|-----------|----|------|
|VARIABLE|Name of cycle variable|Text|`[A-Za-z0-9_-]+`|
|VALUE|Value of cycle variable|Floating-point|`\d+(\.\d+)?`|

ALARMON
-------

**Description:** Alarm on  
**Send:**        When an alarm activates  
**Data:**
  
_ALARM_ `|` _ALARM_ ...
	
|Field|Description|Type|Format|
|-----|-----------|----|------|
|ALARM|Name of alarm|Text|`[A-Za-z0-9_-]+`|

ALARMOFF
--------

**Description:** Alarm off  
**Send:**        When an alarm deactivates  
**Data:**
  
_ALARM_ `|` _ALARM_ ...
	
|Field|Description|Type|Format|
|-----|-----------|----|------|
|ALARM|Name of alarm|Text|`[A-Za-z0-9_-]+`|

OPERATOR
--------

**Description:** Operator of the machine  
**Send:**        Whenever the logged-in operator changes, or immediately after [`CONNECT`](#connect)  
**Periodic:**    Every _n_ minutes (_n_ should be configurable)  
**Data:**
  
_LEVEL_ `|` _OPERATOR_ `|` _NAME_

|Field|Description|Type|Format|
|-----|-----------|----|------|
|LEVEL|Authorization level of the operator|Unsigned integer|`\d+`|
|OPERATOR|Unique ID of the operator|Unsigned integer|`\d+`|
|NAME|Name of the operator|Text|UTF-8, avoid `\|`|

OPMODE
------

**Description:** Operating mode of the machine  
**Send:**        Whenever the operating mode changes, or immediately after CONNECT  
**Periodic:**    Every _n_ minutes (_n_ should be configurable)  
**Data:**
  
_OPMODE_

|Field|Description|Type|Format|
|-----|-----------|----|------|
|OPMODE|Operating mode of the machine|Text|`MANUAL` or `SEMIAUTO` or `AUTO`|

JOBMODE
-------

**Description:** Job mode of the machine  
**Send:**        Whenever the job mode changes, or immediately after CONNECT  
**Periodic:**    Every _n_ minutes (_n_ should be configurable)  
**Data:**
  
_JOBMODE_

|Field|Description|Type|Format|
|-----|-----------|----|------|
|JOBMODE|Job mode value|Unsigned integer|`0`-`15`|

VAR
---

**Description:** Variable change  
**Send:**        Whenever a machine variable changes value  
**Data:**
  
_VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ ...

|Field|Description|Type|Format|
|-----|-----------|----|------|
|VARIABLE|Name of variable|Text|`[A-Za-z0-9_-]+`|
|VALUE|Value of variable|Floating-point|`\d+(\.\d+)?`|

AUDIT
-----

**Description:** Setting value change  
**Send:**        Whenever a setting variable changes value  
**Data:**
  
_VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ ...

|Field|Description|Type|Format|
|-----|-----------|----|------|
|VARIABLE|Name of setting|Text|`[A-Za-z0-9_-]+`|
|VALUE|Value of setting|Floating-point|`\d+(\.\d+)?`|

MOLDNAME
--------

**Description:** Mold name changes  
**Send:**        Whenever a machine's loaded mold set changes  
**Data:**
  
_NAME_

|Field|Description|Type|Format|
|-----|-----------|----|------|
|NAME|Name of mold|Text|UTF-8, avoid `\|`|

ALARMS
------

**Description:** Complete set of outstanding alarms  
**Send:**        Immediately after [`CONNECT`](#connect)
**Periodic:**    Every _n_ minutes (_n_ should be configurable)  
**Data:**
  
_ALARM_ `|` _ALARM_ `|` _ALARM_ ...

|Field|Description|Type|Format|
|-----|-----------|----|------|
|ALARM|Name of alarm|Text|`[A-Za-z0-9_-]+`|

MOLDDATA
--------

**Description:** Complete set of mold data  
**Send:**        Immediately after [`CONNECT`](#connect)
**Periodic:**    Every _n_ minutes (_n_ should be configurable)  
**Data:**
  
_VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ ...

|Field|Description|Type|Format|
|-----|-----------|----|------|
|VARIABLE|Name of mold data variable|Text|`[A-Za-z0-9_-]+`|
|VALUE|Value of mold data variable|Floating-point|`\d+(\.\d+)?`|

VARIABLES
---------

**Description:** Complete set of variables on the machine  
**Send:**        Immediately after [`CONNECT`](#connect)
**Periodic:**    Every _n_ minutes (_n_ should be configurable)  
**Data:**
  
_VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ `|` _VARIABLE_ `=` _VALUE_ ...

|Field|Description|Type|Format|
|-----|-----------|----|------|
|VARIABLE|Name of variable|Text|`[A-Za-z0-9_-]+`|
|VALUE|Value of variable|Floating-point|`\d+(\.\d+)?`|
