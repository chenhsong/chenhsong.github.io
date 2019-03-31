iChen® Server 4 -- Store Data to External Archival Database
=========================================================

Copyright © Chen Hsong Holdings Ltd.  All rights reserved.  
Document Version: 4.1  
Last Edited: 2018-02-09


Introduction
------------

iChen® System 4 comes standard with **Chen Hsong Cloud** storage capabilities,
allowing all data to be seamlessly uploaded to the cloud with no additional
requirement other than a connection to the Internet itself (and an storage
account ID from Chen Hsong, of course).
A full *Analytics* suite of reports and historical data
retrieval download options stands ready to work with data stored in the
**Chen Hsong Cloud**.

Out-of-the-box iChen® Server 4 does not provide any data storage facilities
other than **Chen Hsong Cloud**. This design offers tremendous benefits.
Not only does it obey the principle of
[*Separation of Concerns (SoC)*](https://en.wikipedia.org/wiki/Separation_of_concerns),
it helps keep the CPU, memory and hard-disk footprints of the server minimal,
allowing it to run on a diverse range of hardware, such as head-less, fan-less
mini-PC's with tiny form factor, small solid-state flash storage, weak CPU's and
as low as 1GB of RAM. Such a device can practically be deployed *anywhere* with ease.

Typically, users  employ their own storage mechanism for data archival purposes.
Data is obtained via an Open Protocol™ or OPC UA connection to the iChen® Server 4
instance.

Still, there are times that enterprise-local data storage is desired, especially
where the enterprise already operates in-house database servers or private cloud
storage facilities.  The iChen® Server 4 installation program automatically
handles the configuration, asking only a few information pieces such as the
server name, user name and password etc.

If the database system is very non-standard, so much so that cannot be handled by the
installation program, it can still be done manually. This requires minor changes to
several configuration files in order to redirect data storage to that external database.

> **Note:** Mold data is *always* stored with the server for integrity purposes,
> although the user may use the [REST API](https://github.com/chenhsong/iChen.Web/blob/master/REST%20API%20Reference.md) to manage
> stored mold data sets.


Supported Databases
-------------------

* Any database system with an ODBC driver is supported.

* A *database creation script* is provided for Microsoft SQL Server®
  (including variants such as MSDE, SQL Server® Express etc.).

* For other database systems, it is necessary to convert this script to the
  appropriate format.

* Consult Chen Hsong for help in database script conversion.

### Database Creation Scripts

|  Database System          |    Database Creation Script    |
|---------------------------|:------------------------------:|
|Microsoft SQL Server®      |[Download Script for SQL Server](../scripts/create_archive_database_sqlserver.sql)|
|Microsoft SQL Server® Express|[Download Script for SQL Server](../scripts/create_archive_database_sqlserver.sql)|
|MSDE                       |[Download Script for SQL Server](../scripts/create_archive_database_sqlserver.sql)|
|MySQL                      |[Download Script for MySQL](../scripts/create_archive_database_mysql.sql)|
|PostgreSQL                 |*call for support*              |
|Oracle                     |*call for support*              |
|DB2                        |*call for support*              |


Step 1 - Create the Archive Database
------------------------------------

### Important

This database must *always* be created manually, as the installation program does not
create any database by itself. It expects the database to already exist.

### Create the Database

In the database server designated for data storage, create a new database
(or use an existing one) of any name.

### Database Creation Script

Download the appropriate [database creation script](#supported-databases) and run it
to create the necessary tables listed below.
Make sure the tables are created inside the appropriate database.

| Table Name        | Data Stored                     |
|-------------------|---------------------------------|
| `Events`          | All status change data          |
| `Alarms`          | All alarms data                 |
| `AuditTrail`      | All parameter change records    |
| `CycleData`       | All cycle data                  |
| `CycleDataValues` | Cycle data variables and values |

### SQL Server® Specific

In Microsoft SQL Server®, make sure that the script is run when the database
is the active one. Use the following T-SQL command to change the active database
(assuming the database's name is `HistoricalDataStorageDB`):

~~~~~~~~sql
USE HistoricalDataStorageDB;
GO
~~~~~~~~

#### Security Considerations for SQL Server®

The iChen® Server 4 instance must be able to connect to the external archival
database via a user account that has permissions to read from and write to those
tables. In general, this is usually accomplished by creating a special login
exclusively for use by iChen® System 4, granting it access to the database
as well as the `db_datareader` and `db_datawriter` roles.

In some installations, Windows®-based or domain-based authorization may be used by
the iChen® Server 4 instance to connect to the database. In that case, the user
account used must still be granted access to the database as well as the `db_datareader`
and `db_datawriter` roles.

#### Option: Create Tables under Custom Schema in SQL Server®

When using an existing database for such storage (perhaps to include
iChen® System 4 data together with data from other equipment),
it is possible to create the tables under their own *schema* for better management
and security control.  For example, running the following *before* creating the tables
will change the *default schema* of the specified user (in this example, `iChenServer`)
to `iChen`:

~~~~~~~~sql
ALTER USER iChenServer WITH DEFAULT_SCHEMA = iChen;
GO
~~~~~~~~

Needless to say, the schema in question (`iChen` in the above example) must already
exist -- it can be created with any database management tool, or the
`CREATE SCHEMA` T-SQL statement.

The user (`iChenServer` above) must be the user account that is used by the
iChen® Server 4 instance to connect to the SQL Server® instance.

After changing the default schema of the user, login with the user before running
the database creation script.  For this to succeed, the user must have `CREATE TABLE`
and associated permissions (such as `CREATE INDEX`), usually by granting it the
`db_ddladmin` database role.

When the database creation script is run under a user login, and the user has its
default schema properly set as well as the necessary permissions to create tables,
the tables will then be created under the `iChen` schema.

After creation of the necessary tables, the role `db_ddladmin` may be dropped from the
user to prevent unauthorized modifications of the table structures.


Step 1b -- Use the Installation Program
--------------------------------------------

There are no more steps to go. The installation program does all the work automatically.
If the iChen® System 4 has already been installed, it is still possible to
add an external data storage database by re-running the installation program and choosing
the "Change" option.


Step 2 -- Edit the Database Connection String Manually
-----------------------------------------------------------

This step, and the steps following, are only for manually configuring an external data
storage database.

Open the file `iChenServerService.exe.config` (within the main iChen® System 4 folder)
and find the following section:

~~~~~~~~xml
<configuration>
    <connectionStrings>
        <add name="ConfigDB" connectionString="Data Source=|DataDirectory|\Database\iChenServerDB.sdf" providerName="System.Data.SqlServerCe.4" />
        <add name="DataArchiveDB" connectionString="..." providerName="System.Data.Odbc" />
    </connectionStrings>

        :
        :
</configuration>
~~~~~~~~

Find the connection string:

~~~~~~~~xml
<add name="DataArchiveDB" connectionString="..." providerName="System.Data.Odbc" />
~~~~~~~~

For the `connectionString` attribute, change it to the *ODBC connection string* to connect
to the  database.  For example, a typical *connection string* for Microsoft SQL Server®
should look like (in one single line):

~~~~~~~~txt
Driver={SQL Server};
    Server=ServerName\SQLSERVER;
    Database=HistoricalDataStorageDB;
    Uid=iChenServer;
    Pwd=ThePassword
~~~~~~~~

Different database systems have different connection string formats. Consult on-line
documentation (such as [www.connectionstrings.com](http://www.connectionstrings.com/))
to find out the appropriate connection string for the actual database
system used.

The final `<connectionStrings>` section should now look like this (if using SQL Server®):

~~~~~~~~xml
<configuration>
    <connectionStrings>
        <add name="ConfigDB" connectionString="Data Source=|DataDirectory|\Database\iChenServerDB.sdf" providerName="System.Data.SqlServerCe.4" />
        <add name="DataArchiveDB" connectionString="Driver={SQL Server};Server=ServerName\SQLSERVER;Database=HistoricalDataStorageDB;Uid=iChenServer;Pwd=ThePassword" providerName="System.Data.Odbc" />
    </connectionStrings>

        :
        :
</configuration>
~~~~~~~~

This allows the iChen® Server 4 instance to connect to the database at the
server `ServerName`, as the user `iChenServer` with password `ThePassword`.

> **Note:** Do the same for the file `iChenConsole.exe.config` if `iChenConsole.exe` will be used.


Step 3 -- Specify Database Connection in Configuration File Manually
-------------------------------------------------------------------------

Open the file `iChenServer.config` (within the main iChen® System 4 folder)
and find the following section:

~~~~~~~~xml
<configuration>
    <appSettings>
            :
            :
        <add key="DataStore" />
        <add key="CloudStore_Account" value="ichen" />
        <add key="CloudStore_Signature" value=" ... " />
            :
            :
    </appSettings>
</configuration>
~~~~~~~~

The `CloudStore` keys provision the **Chen Hsong Cloud** storage account `ichen` for
data storage purposes while the `DataStore` key provisions an external storage database.
Change the `DataStore` key entry to `DataArchiveDB`:

~~~~~~~~xml
<configuration>
    <appSettings>
            :
            :
        <!-- External archive database added -->
        <add key="DataStore" value="DataArchiveDB" />

        <!-- Chen Hsong Cloud can be commented out if no longer needed -->
        <!-- or just leave it be; data can be stored in both places simultaneously -->
        <!--add key="CloudStore_Account" value="ichen" /-->
        <!--add key="CloudStore_Signature" value=" ... " /-->
            :
            :
    </appSettings>
</configuration>
~~~~~~~~

This provisions the database connection as specified under the name `DataArchiveDB`
(set up in Step 2 above) to be used for storing historical data.

Leaving the **Chen Hsong Cloud** settings intact will upload data for storage on
*both* the cloud as well as the external database:

~~~~~~~~xml
<configuration>
    <appSettings>
            :
            :
        <!-- External archive database added -->
        <add key="DataStore" value="DataArchiveDB" />

        <!-- Chen Hsong Cloud still used -->
        <add key="CloudStore_Account" value="ichen" />
        <add key="CloudStore_Signature" value=" ... " />
            :
            :
    </appSettings>
</configuration>
~~~~~~~~


Step 4 -- Restart
----------------------

Restart the iChen® Server 4 instance. Data should now be stored in the
provisioned database server.

The *Analytics* module, by default, automatically runs off stored data
from the external archival database if one is provisioned.  If not,
it runs off stored data inside the **Chen Hsong Cloud**.
