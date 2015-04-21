# Neubot scheduler

- Author: Simone Basso and Federico Morando and Davide Allavena
- Version: 1.0
- Date: 2015-04-21
- Documents: Neubot scheduler

This document specifies the neubot-scheduler, that is the component
of Neubot that implements Neubot API and runs periodic tests.

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
