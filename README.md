# pump1090

### Overview

  `pump1090` is a simple Rust program that provides one service: it reads and parses dumpfile JSON files generated by `dump1090` and pipes them to a WebSocket endpoint. That's it.

  Theoretically, `pump1090` could work with any server with a WebSocket endpoint that doesn't need authentication, but was designed to work with [serve1090](https://github.com/robertsteilberg/serve1090).

  `piaware` is a better-known and feature complete example of software that does the exact same thing, but `piaware` runs on Debian-like Linux only and is specifically made to send dumpfile data to FlightAware. `piaware` and `pump1090` will run just fine in concert.

  This program would probably have been much easier to write in Python or some other scripting language, but it was a nice introduction to learning Rust.

### Arguments

  `pump1090` needs three inputs to operate properly. It will first check for a `.env` file containing the necessary arguments, will next check for command line arguments, and will finally use the default values specified in the source code.

| Name | Description | .env var name | CLI arg name | Default value |
|--------------------|---------------------------------------------------------------|--------------------|--------------------|----------------------------|
| Dumpfile path | Path to the JSON file generated by dump1090 | `DUMPFILE_PATH` | `-d`, `--dumpfile` | `data/aircraft.sjon` |
| WebSocket endpoint | URL of the WebSocket endpoint to send dump1090 data | `WS_ENDPOINT` | `-e, --endpoint` | `ws://localhost:3000/pump` |
| Serve1090 secret | Secret for the serve1090 instance accepting the dumpfile data | `SERVE1090_SECRET` | `-s, --secret` | `undefined` |

### Implementation notes

  `pump1090` is relatively resilient against failure. It will automatically attempt to establish a connection with the WebSocket endpoint every 5 seconds. If connection with the endpoint is lost during the pumping process, `pump1090` will re-enter the "establish" mode, retrying the endpoint until a successful connection is made.

  There is no file watching structure in place--the dumpfile is automatically parsed and piped to the endpoint every half second. File watching proved to be very complicated to implement, especially across the Linux/Darwin platforms, with marginal performance gain.
