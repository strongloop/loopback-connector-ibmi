# loopback-connector-ibmi

[IBM® Db2® for i](http://www-03.ibm.com/systems/power/software/i/db2/) is the database of choice for robust, enterprise-wide solutions handling high-volume workloads. It is optimized to deliver industry-leading performance while lowering costs and is fully integrated into the (IBM i operating system)[https://www.ibm.com/it-infrastructure/power/os/ibm-i].  The `loopback-connector-ibmi`
module is the LoopBack connector for Db2 for i.

The LoopBack Db2 for i connector supports:

- All [create, retrieve, update, and delete operations](http://loopback.io/doc/en/lb2/Creating-updating-and-deleting-data.html).
- [Queries](http://loopback.io/doc/en/lb2/Querying-data.html) with fields, limit, order, skip and where filters.

## Major differences from version 0.x of loopback-connector-ibmi
This version of the module is significantly different from version 0.x and constitutes a complete rewrite. This project is a derivative of [loopback-connector-db2iseries](https://github.com/strongloop/loopback-connector-db2iseries). 
The original 0.x codebase can be found [here](https://github.com/andrescolodrero/loopback-connector-ibmi).

The main difference between this and the other packages for IBM i (including v0.x of this package) is that it uses ODBC to communicate to the database. Version 0.x of this package was built using the Db2 for i CLI API set, hence the need for important prerequisites (below). 

## Prerequisites
Before installing this package, you will need an ODBC driver and a driver manager (with development libraries). 
This package is primarily developed and tested with the IBM i Access ODBC driver, which is supported as part of IBM i software maintenance agreements (SWMA) and comes with no additional licensing fees. 

### On IBM i
- Install the `unixODBC-devel` package. See [the RPM and yum documentation for IBM i](http://ibm.biz/ibmi-rpms) for more detailed steps.
- TBD
### On Linux
- Install the `unixODBC-devel` package with your operating system's package manager (apt-get, zypper, yum, etc).
- Install the "Linux Application Package" of IBM i Access Client Solutions. Consult [this document](http://www-01.ibm.com/support/docview.wss?uid=nas8N1010355) for assistance.
### On Windows
- Install the "Windows Application Package" of IBM i Access Client Solutions. Consult [this document](http://www-01.ibm.com/support/docview.wss?uid=nas8N1010355) for assistance.


## Installation
Once the prerequisites are satisfied, enter the following in the top-level directory of your LoopBack application:

```
$ npm install loopback-connector-ibmi --save
```

The `--save` option adds the dependency to the application's `package.json` file.

## Configuration

Use the [data source generator](http://loopback.io/doc/en/lb2/Data-source-generator.html) to add the Db2 for i data source to your application.
The entry in the application's `server/datasources.json` will look something like this:

```js
"mydb": {
  "name": "mydb",
  "connector": "ibmi"
}
```

Edit `server/datasources.json` to add other supported properties as required:

```js
"mydb": {
  "name": "mydb",
  "connector": "ibmi",
  "username": <username>,
  "password": <password>,
  "database": <database name>,
  "hostname": <db2 server hostname>,
  "port":     <port number>
}
```

The following table describes the connector properties.

Property&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Type&nbsp;&nbsp;    | Description
---------------| --------| --------
database       | String  | Database name
schema         | String  | Specifies the default schema name that is used to qualify unqualified database objects in dynamically prepared SQL statements. The value of this property sets the value in the CURRENT SCHEMA special register on the database server. The schema name is case-sensitive, and must be specified in uppercase characters
username       | String  | DB2 Username
password       | String  | DB2 password associated with the username above
hostname       | String  | DB2 server hostname or IP address
port           | String  | DB2 server TCP port number
useLimitOffset | Boolean | LIMIT and OFFSET must be configured on the DB2 server before use (compatibility mode)
supportDashDB  | Boolean | Create ROW ORGANIZED tables to support dashDB.


Alternatively, you can create and configure the data source in JavaScript code.
For example:

```js
var DataSource = require('loopback-datasource-juggler').DataSource;
var DB2 = require('loopback-connector-ibmi');

var config = {
  username: process.env.DB2_USERNAME,
  password: process.env.DB2_PASSWORD,
  hostname: process.env.DB2_HOSTNAME,
  port: 50000,
  database: 'SQLDB',
};

var db = new DataSource(DB2, config);

var User = db.define('User', {
  name: { type: String },
  email: { type: String },
});

db.autoupdate('User', function(err) {
  if (err) {
    console.log(err);
    return;
  }

  User.create({
    name: 'Tony',
    email: 'tony@t.com',
  }, function(err, user) {
    console.log(err, user);
  });

  User.find({ where: { name: 'Tony' }}, function(err, users) {
    console.log(err, users);
  });

  User.destroyAll(function() {
    console.log('example complete');
  });
});
```
