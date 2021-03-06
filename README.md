# Galaxy Admin Utility [![Build Status](https://travis-ci.org/usegalaxy-eu/gxadmin.svg?branch=master)](https://travis-ci.org/usegalaxy-eu/gxadmin)

A command line tool for [Galaxy](https://github.com/galaxyproject/galaxy)
administrators to run common queries against our Postgres databases. It additionally
includes some code for managing zerglings under systemd, and other utilities.

Mostly gxadmin acts as a repository for the common queries we all run regularly
but fail to share with each other. We even include some [unlisted
queries](./parts/27-unlisted.sh) which may be useful as examples, but are not generically useful.

It comes with around 40 commonly useful queries included, but you can easily
add more to your installation with local functions. gxadmin attempts to be a
very readable bash script and avoids using fancy new bash features.

This script strictly expects a postgres database and has no plans to support
mysql or sqlite3.

## Installation

```
curl https://raw.githubusercontent.com/usegalaxy-eu/gxadmin/master/gxadmin > /usr/bin/gxadmin
chmod +x /usr/bin/gxadmin
```

## Changelog

[Changelog](CHANGELOG.md)

## Contributors

- Helena Rasche ([@erasche](https://github.com/erasche))
- Nate Coraor ([@natefoo](https://github.com/natefoo))
- Simon Gladman ([@slugger70](https://github.com/slugger70))
- Anthony Bretaudeau ([@abretaud](https://github.com/abretaud))
- Manuel Messner (mm@skellet.io)

## License

GPLv3

## Configuration

`gxadmin` does not have much configuration, mostly env vars and functions will complain if you don't have them set properly.

### Postgres

Queries support being run in normal postgres table, csv, or tsv output as you
need. Just use `gxadmin query`, `gxadmin tsvquery`, or `gxadmin csvquery` as
appropriate.

You should have a `~/.pgpass` with the database connection information, and set
`PGDATABASE`, `PGHOST`, and `PGUSER` in your environment.

Example .pgpass:

```
<pg_host>:5432:*:<pg_user>:<pg_password>
```

### GDPR

You may want to set `GDPR_MODE=1`. Please determine your own legal responsibilities, the authors take no responsibility for anything you may have done wrong.

### Local Functions

If you want to add some site-specific functions, you can do this in `~/.config/gxadmin-local.sh` (location can be overridden by setting `$GXADMIN_SITE_SPECIFIC`)

You should write a bash script which looks like. **ALL functions must be prefixed with `local_`**

```bash
local_cats() { ## cats: Makes cat noises
	handle_help "$@" <<-EOF
		Here is some documentation on this function
	EOF

	echo "Meow"
}
```

This can then be called with `gxadmin` like:

```console
$ gxadmin local cats --help
gxadmin local functions usage:

    cats   Cute kitties

help / -h / --help : this message. Invoke '--help' on any subcommand for help specific to that subcommand
$ gxadmin local cats
Meow
$
```

meta_cmdlist
## Commands

### config

Command | Description
------- | -----------
[`config dump`](docs/README.config.md#config-dump) | Dump Galaxy configuration as JSON
[`config validate`](docs/README.config.md#config-validate) | validate config files

### filter

Command | Description
------- | -----------
[`filter digest-color`](docs/README.filter.md#filter-digest-color) | Color an input stream based on the contents (e.g. hostname)
[`filter hexdecode`](docs/README.filter.md#filter-hexdecode) | Decodes any hex blobs from postgres outputs
[`filter identicon`](docs/README.filter.md#filter-identicon) | Convert an input data stream into an identicon (e.g. with hostname)
[`filter pg2md`](docs/README.filter.md#filter-pg2md) | Convert postgres table format outputs to something that can be pasted as markdown

### galaxy

Command | Description
------- | -----------
[`galaxy cleanup`](docs/README.galaxy.md#galaxy-cleanup) | Cleanup histories/hdas/etc for past N days (default=30)
[`galaxy migrate-tool-install-to-sqlite`](docs/README.galaxy.md#galaxy-migrate-tool-install-to-sqlite) | Converts normal potsgres toolshed repository tables into the SQLite version

### meta

Command | Description
------- | -----------
[`meta slurp-current`](docs/README.meta.md#meta-slurp-current) | Executes what used to be "Galaxy Slurp"
[`meta slurp-upto`](docs/README.meta.md#meta-slurp-upto) | Slurps data "up to" a specific date.
[`meta update`](docs/README.meta.md#meta-update) | Update the script

### mutate

Command | Description
------- | -----------
[`mutate fail-terminal-datasets`](docs/README.mutate.md#mutate-fail-terminal-datasets) | Causes the output datasets of jobs which were manually failed, to be marked as failed

### query

Command | Description
------- | -----------
`query active-users` | Deprecated, use monthly-users-active
[`query collection-usage`](docs/README.query.md#query-collection-usage) | Information about how many collections of various types are used
[`query datasets-created-daily`](docs/README.query.md#query-datasets-created-daily) | The min/max/average/p95/p99 of total size of datasets created in a single day.
[`query disk-usage`](docs/README.query.md#query-disk-usage) | Disk usage per object store.
[`query errored-jobs`](docs/README.query.md#query-errored-jobs) | Lists jobs that errored in the last N hours.
[`query groups-list`](docs/README.query.md#query-groups-list) | List all groups known to Galaxy
[`query job-history`](docs/README.query.md#query-job-history) | Job state history for a specific job
[`query job-inputs`](docs/README.query.md#query-job-inputs) | Input datasets to a specific job
[`query job-outputs`](docs/README.query.md#query-job-outputs) | Output datasets from a specific job
[`query jobs-max-by-cpu-hours`](docs/README.query.md#query-jobs-max-by-cpu-hours) | Top 10 jobs by CPU hours consumed (requires CGroups metrics)
[`query jobs-nonterminal`](docs/README.query.md#query-jobs-nonterminal) | Job info of nonterminal jobs separated by user
[`query jobs-per-user`](docs/README.query.md#query-jobs-per-user) | Number of jobs run by a specific user
[`query largest-collection`](docs/README.query.md#query-largest-collection) | Returns the size of the single largest collection
[`query largest-histories`](docs/README.query.md#query-largest-histories) | Largest histories in Galaxy
[`query latest-users`](docs/README.query.md#query-latest-users) | 40 recently registered users
[`query monthly-cpu-years`](docs/README.query.md#query-monthly-cpu-years) | CPU years allocated to tools by month
[`query monthly-data`](docs/README.query.md#query-monthly-data) | Number of active users per month, running jobs
[`query monthly-jobs`](docs/README.query.md#query-monthly-jobs) | Number of jobs run each month
[`query monthly-users-active`](docs/README.query.md#query-monthly-users-active) | Number of active users per month, running jobs
`query monthly-users` | Deprecated, use monthly-users-active
[`query monthly-users-registered`](docs/README.query.md#query-monthly-users-registered) | Number of users registered each month
[`query old-histories`](docs/README.query.md#query-old-histories) | Lists histories that haven't been updated (used) for <weeks>
[`query queue`](docs/README.query.md#query-queue) | Brief overview of currently running jobs
[`query queue-detail`](docs/README.query.md#query-queue-detail) | Detailed overview of running and queued jobs
[`query queue-overview`](docs/README.query.md#query-queue-overview) | View used mostly for monitoring
[`query queue-time`](docs/README.query.md#query-queue-time) | The average/95%/99% a specific tool spends in queue state.
[`query recent-jobs`](docs/README.query.md#query-recent-jobs) | Jobs run in the past <hours> (in any state)
[`query runtime-per-user`](docs/README.query.md#query-runtime-per-user) | computation time of user (by email)
[`query tool-available-metrics`](docs/README.query.md#query-tool-available-metrics) | list all available metrics for a given tool
[`query tool-last-used-date`](docs/README.query.md#query-tool-last-used-date) | When was the most recent invocation of every tool
[`query tool-metrics`](docs/README.query.md#query-tool-metrics) | See values of a specific metric
[`query tool-popularity`](docs/README.query.md#query-tool-popularity) | Most run tools by month
[`query tool-usage`](docs/README.query.md#query-tool-usage) | Counts of tool runs in the past weeks (default = all)
[`query training-list`](docs/README.query.md#query-training-list) | List known trainings
[`query training-members-remove`](docs/README.query.md#query-training-members-remove) | Remove a user from a training
[`query training-members`](docs/README.query.md#query-training-members) | List users in a specific training
[`query training-queue`](docs/README.query.md#query-training-queue) | Jobs currently being run by people in a given training
[`query ts-repos`](docs/README.query.md#query-ts-repos) | Counts of toolshed repositories by toolshed and owner.
[`query users-count`](docs/README.query.md#query-users-count) | Shows sums of active/external/deleted/purged accounts
[`query users-total`](docs/README.query.md#query-users-total) | Total number of Galaxy users (incl deleted, purged, inactive)
[`query workflow-connections`](docs/README.query.md#query-workflow-connections) | The connections of tools, from output to input, in the latest (or all) versions of user workflows
[`query workflow-invocation-status`](docs/README.query.md#query-workflow-invocation-status) | Report on how many workflows are in new state by handler

### report

Command | Description
------- | -----------
[`report assigned-to-handler`](docs/README.report.md#report-assigned-to-handler) | Report what items are assigned to a handler currently.
[`report job-info`](docs/README.report.md#report-job-info) | Information about a specific job
[`report user-info`](docs/README.report.md#report-user-info) | Quick overview of a Galaxy user in your system

### uwsgi

Command | Description
------- | -----------
[`uwsgi handler-restart`](docs/README.uwsgi.md#uwsgi-handler-restart) | Restart all handlers
[`uwsgi handler-strace`](docs/README.uwsgi.md#uwsgi-handler-strace) | Strace a handler
[`uwsgi lastlog`](docs/README.uwsgi.md#uwsgi-lastlog) | Fetch the number of seconds since the last log message was written
[`uwsgi memory`](docs/README.uwsgi.md#uwsgi-memory) | Current system memory usage
[`uwsgi pids`](docs/README.uwsgi.md#uwsgi-pids) | Galaxy process PIDs
[`uwsgi stats-influx`](docs/README.uwsgi.md#uwsgi-stats-influx) | InfluxDB formatted output for the current stats
`uwsgi stats_influx` | Deprecated, use uwsgi stats-influx
[`uwsgi stats`](docs/README.uwsgi.md#uwsgi-stats) | uwsgi stats
[`uwsgi status`](docs/README.uwsgi.md#uwsgi-status) | Current system status
[`uwsgi zerg-scale-down`](docs/README.uwsgi.md#uwsgi-zerg-scale-down) | Remove an extraneous zergling
[`uwsgi zerg-scale-up`](docs/README.uwsgi.md#uwsgi-zerg-scale-up) | Add another zergling to deal with high load
[`uwsgi zerg-strace`](docs/README.uwsgi.md#uwsgi-zerg-strace) | Strace a zergling
[`uwsgi zerg-swap`](docs/README.uwsgi.md#uwsgi-zerg-swap) | Swap zerglings in order (unintelligent version)

