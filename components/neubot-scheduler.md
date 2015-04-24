# Neubot scheduler

- Author: Simone Basso and Federico Morando and Davide Allavena and Alessio Melandri
- Version: 1.0
- Date: 2015-04-21
- Documents: Neubot scheduler

This document specifies the neubot-scheduler, that is the component
of Neubot that implements Neubot API and runs periodic tests.

## Assumptions

We assume that the neubot-runner runs in the `/` folder and that it
does not run with root privileges. The latter is checked when the
process is started on a Unix system.

We assume that, when the neubot-runner is run at startup it is run
by another process which drops the privileges.

## API

Here we describe the APIs implemented by the neubot-scheduler and
we describe the high-level algorithm implementing them.

### /

Originally Neubot included the sources of a modern AJAX based web interface
but this is not the case anymore.

Now the full fledged web interface is accessible online and when one
requests for the / URL Neubot shall generate a brief report of its
state and point the user to the online web interface for more info.

Example:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Neubot 0.5.0.0 status</title>
  </head>
  <body>
    <h1>Neubot 0.5.0.0 status</h1>
    <p>Neubot is running with pid 501 since 2015-03-18 @ 13:00 CET.</p>
    <p>The last tests run are:</p>
    <pre>
+-----------+----------------+--------------+---------+
| Test name | Download speed | Upload speed | Latency |
+-----------+----------------+--------------+---------+
| Speedtest |    11.3 Mbit/s |  0.95 Mbit/s |   66 ms |
+-----------+----------------+--------------+---------+
    </pre>
    <p>See the <a href="https://ui.neubot.org/">online interface</a> for more details.</p>
  </body>
</html>
```

## Test descriptors

Test descriptors are JSON files describing how a test should be run. They
are not packaged with the neubot-scheduler, however the neubot-scheduler
needs to read them when new tests are started. For this reason we describe
here the minimum subset needed by the neubot-scheduler.

The directory in which these descriptors are is `/etc/neubot/` under Unix
and a to be specified directory under other systems.

When the neubot-scheduler is requested to present the list of available
tests using the `/api/descriptors` API (or when an automatic test is
to be run), the neubot-scheduler reads all the files available in the
above mentioned directory so to build the most up-to-date list of
available tests.

The following information is needed by the neubot-scheduler to
run a test through the neubot-runner process:

* the test name (deduced from the file name)
* the test default parameters as a dictionary

For example:

```json
{
    "parameters": {
      ...
    },
    ...
}
```

## Databases

The scheduler process is very web like, therefore we use sqlite3
databases to store variable data to avoid surprises.

To keep databases size under control, we use separate databases for
logs, configuration, and test results. Each database cannot grow
larger than 10 MiB. When this happens the scheduler removes the 50%
oldest rows in the database tables.

The user can configure the maxium size that a database can take
using the `max_database_size` setting.

The databases live in `/var/lib/neubot/` under Linux, in
`/var/neubot/` under BSD, and in a yet to be specified place
under Windows.

### Databases

The following databases are used:

#### config.sqlite3

This database contains a single table with the following schema:

```text
+-------------------------+--------------+
| name PRIMARY KEY STRING | value STRING |
+-------------------------+--------------+
```

We save integer variables as strings and we cast them back
as integer when they are read.

#### data.sqlite3

This database contains a table having the following schema:

```text
+------------------------------------+------------------+-------------+
| timestamp_msec INTEGER PRIMARY KEY | test_name STRING | json STRING |
+------------------------------------+------------------+-------------+
```

where timestamp is the timestamp when the test started with millisecond
precision, and json is the json-serialized test result.

#### log.sqlite3

This database contains a table having the following schema:

```text
+ ----------------------------- + --------------- + -------------- +
| TIMESTAMP INTEGER PRIMARY KEY | SEVERITY STRING | MESSAGE STRING |
+ ----------------------------- + --------------- + -------------- +
```

where timestamp is the timestamp when the test started with millisecond
precision, and severity is one of DEBUG, INFO, WARNING, ERR.
