# Neubot runner

- Author: Simone Basso and Federico Morando
- Version: 1.0
- Date: 2015-04-17
- Documents: Neubot runner

This document specifies Neubot's runner, that is the software component
that takes care of running Neubot tests.

## The high-level algorithm

The runner is invoked by the local web server.

- input:
    - path of the test descriptor
    - input file path
        - this contains input parameters as JSON
    - output file path
    - error file path
    - file containing list of available servers
- output:
    - exit 0 on success, nonzero on failure
- error:
    - log messages written on error file

The algorithm proper:

- 1. validate input parameters or exit 1
- 2. if AP address auto-discover:
    - 2.1. query mlabns
    - 2.2. otherwise select test at random from static list
    - 2.3. otherwise exit 2
- 3. test negotiation (to be expanded)
- 4. execute real test redirecting stdin, stdout, stderr
- 5. if runtime > max_runtime, kill test, exit 3
- 6. test collect (to be expanded)
- 7. exit 0

## Unclear / XXX / TODO things

- log streaming / web UX interactivity
- server side results in a separate file?
- local web server must check privacy
- local web server must evict processes running for too long
- local web server must write database
- local web server manages static list of mlab servers
