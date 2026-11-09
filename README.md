# Terminatus

A [Gatus](https://github.com/TwiN/gatus) status page in the terminal: every endpoint's state,
response time, uptime, and recent checks on one screen, refreshed in place.

![terminatus screenshot](screenshot.png)

## Features

- A summary of how many endpoints are up, their average 24-hour uptime, and the average response
  time, with a graph of its recent history (scaled to its peak, colored by how slow it was)
- One panel per endpoint group, with a row per endpoint: up or down and for how long, the latest
  response time and its trend, 24-hour uptime, a strip of the most recent checks, and the error
  when the endpoint is down
- Endpoints that are down come first in their group
- `--watch` redraws in place: `r` refreshes now, `q` quits, and a resized terminal is redrawn at once
- `--concise` prints a single line, for a status bar or a script
- Basic authentication, with the credentials in the URL
- Works with time stamps in any time zone, keeps going when the uptime or event data is unavailable
  (the 24h column then shows —), and fits narrow terminals by leaving out the smaller graphs
- A single file using only the Python standard library

## Requirements

- Python 3.6 or newer (tested with 3.13)
- Linux or macOS
- A Gatus instance whose API you can reach

## Install

```sh
curl -fsSLo terminatus https://raw.githubusercontent.com/the-farshad/Terminatus/main/terminatus
chmod +x terminatus
./terminatus https://status.example.com
```

Or clone the repo and place the script on your `PATH`:

```sh
git clone https://github.com/the-farshad/Terminatus.git
cp Terminatus/terminatus ~/.local/bin/
```

## Usage

```sh
terminatus https://status.example.com              # print one snapshot and exit
terminatus https://status.example.com --watch      # refresh every 10 seconds
terminatus https://status.example.com --watch 30   # refresh every 30 seconds
terminatus https://status.example.com --concise    # one line
terminatus -h                                      # show help
```

The concise line looks like this:

```
Acme Status 14:32:07  9/10 up  181ms avg  down: payments
```

### Authentication

For a status page behind basic authentication, put the credentials in the URL:

```sh
terminatus https://user:password@status.example.com
```

A password in a command line stays in your shell history and is visible to other users of the
machine through the process list. Setting `GATUS_URL` in a file that only you can read avoids both.

## Configuration

| Variable    | Default | Description                                                          |
|-------------|---------|----------------------------------------------------------------------|
| `GATUS_URL` | none    | Address of the Gatus instance, used when no URL is given             |
| `SLOW_MS`   | `1000`  | Response time, in milliseconds, that fills a meter or a small graph  |

## How it works

Terminatus reads the Gatus API: `/api/v1/endpoints/statuses` for every endpoint's recent results
on each refresh, and, once a minute per endpoint, `/api/v1/endpoints/{key}/statuses` for its
events (to tell how long it has been up or down) and `/api/v1/endpoints/{key}/uptimes/24h` for
its 24-hour uptime.

## License

GNU General Public License v3.0 — see [LICENSE](LICENSE).
