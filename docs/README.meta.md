# meta

Command | Description
------- | -----------
[`meta slurp-current`](#galaxy-migrate-tool-install-to-sqlite) | Executes what used to be "Galaxy Slurp"
[`meta slurp-upto`](#galaxy-migrate-tool-install-to-sqlite) | Slurps data "up to" a specific date.
[`meta update`](#galaxy-migrate-tool-install-to-sqlite) | Update the script

### meta slurp-current

**NAME**

meta slurp-current -  Executes what used to be "Galaxy Slurp"

**SYNOPSIS**

gxadmin meta slurp-current

**NOTES**

Obtain influx compatible metrics regarding the current state of the
server. UseGalaxy.EU uses this to display things like "Current user
count" and "Current dataset size/distribution/etc."

It is expected that you are passing this straight to telegraf, there is
no non-influx output supported currently.

You can add your own functions which are included in this output, using
the $GXADMIN_SITE_SPECIFIC file. They must start with the prefix
"query_server-", e.g. "query_server-mymetric".

    $ gxadmin meta slurp-current
    server-allocated-cpu,job_runner_name=condor cpu_years=102.00
    server-allocated-cpu,job_runner_name=local cpu_years=1346.00
    server-datasets,deleted=f,object_store_id=,state=error,purged=f count=37,size=29895528
    server-datasets,deleted=f,object_store_id=,state=ok,purged=f count=72,size=76739510
    server-datasets,deleted=f,object_store_id=,state=discarded,purged=f count=2,size=0
    server-hda,deleted=f,extension=gff3 max=2682565,sum=2682565,avg=2682565,min=2682565
    server-hda,deleted=t,extension=tabular max=468549,sum=597843,avg=22142,min=2
    server-hda,deleted=f,extension=fastqsanger max=3,sum=3,avg=3,min=3
    server-hda,deleted=f,extension=tabular max=2819293,sum=3270268,avg=155727,min=3
    server-hda,deleted=f,extension=png max=800459,sum=7047695,avg=503407,min=142863
    server-hda,deleted=t,extension=auto max=9571,sum=9571,avg=9571,min=9571
    server-hda,deleted=t,extension=data max=2,sum=2,avg=2,min=2
    server-hda,deleted=t,extension=txt max=5023,sum=5039,avg=2520,min=16
    server-hda,deleted=t,extension=bigwig max=2972569,sum=5857063,avg=1464266,min=0
    server-hda,deleted=t,extension=tar.gz max=209034,sum=1318690,avg=188384,min=91580
    server-hda,deleted=f,extension=vcf_bgzip max=3965889,sum=3965889,avg=3965889,min=3965889
    server-hda,deleted=t,extension=png max=2969711,sum=6584812,avg=1097469,min=183
    server-hda,deleted=f,extension=txt max=9584828,sum=132124407,avg=4556014,min=0
    server-hda,deleted=f,extension=bigwig max=14722,sum=17407,avg=8704,min=2685
    server-hda,deleted=f,extension=tar.gz max=209025,sum=390421,avg=195211,min=181396
    server-histories,importable=f,deleted=f,genome_build=?,published=f,importing=f,purged=f count=14
    server-jobs,state=ok,destination_id=slurm_singularity,job_runner_name=local count=26
    server-jobs,state=error,destination_id=condor_resub,job_runner_name=condor count=1
    server-jobs,state=deleted,destination_id=local,job_runner_name=local count=1
    server-jobs,state=error,destination_id=condor,job_runner_name=condor count=8
    server-jobs,state=ok,destination_id=local,job_runner_name=local count=13
    server-jobs,state=ok,destination_id=condor,job_runner_name=condor count=2
    server-jobs,state=error,destination_id=local,job_runner_name=local count=3
    server-users,active=t,deleted=f,external=f,purged=f count=3
    server-workflows,deleted=f,importable=f,published=f count=3


### meta slurp-upto

**NAME**

meta slurp-upto -  Slurps data "up to" a specific date.

**SYNOPSIS**

gxadmin meta slurp-upto [yyyy-mm-dd]

**NOTES**

Obtain influx compatible metrics regarding the summed state of the
server up to a specific date. UseGalaxy.EU uses this to display things
like charts of "how many users were registered as of date X". You can
backfill data from your server by running a for loop like:

    #!/bin/bash
    for i in range {1..365}; do
        gxadmin meta slurp-upto $(date -d " days ago" "+%Y-%m-%d") | <get into influx somehow>
    done

It is expected that you are passing this straight to telegraf, there is
no non-influx output supported currently.

This calls all of the same functions as 'gxadmin meta slurp-current',
but with date filters for the entries' creation times.

You can add your own functions which are included in this output, using
the $GXADMIN_SITE_SPECIFIC file. They must start with the prefix
"query_server-", e.g. "query_server-mymetric". They should include a
date filter as well, or the metrics reported here will be less useful.

    $ gxadmin meta slurp-upto 2019-01-01
    server-allocated-cpu.daily,job_runner_name=condor cpu_years=102.00
    server-datasets.daily,deleted=f,object_store_id=,state=error,purged=f count=37,size=29895528
    server-datasets.daily,deleted=f,object_store_id=,state=ok,purged=f count=72,size=76739510
    server-datasets.daily,deleted=f,object_store_id=,state=discarded,purged=f count=2,size=0
    server-hda.daily,deleted=t,extension=data max=2,sum=2,avg=2,min=2
    server-hda.daily,deleted=t,extension=txt max=5023,sum=5039,avg=2520,min=16
    server-hda.daily,deleted=f,extension=fastqsanger max=3,sum=3,avg=3,min=3
    server-hda.daily,deleted=f,extension=tabular max=3,sum=51,avg=3,min=3
    server-hda.daily,deleted=t,extension=tabular max=468549,sum=552788,avg=21261,min=2
    server-histories.daily,importable=f,deleted=f,genome_build=?,published=f,importing=f,purged=f count=5
    server-jobs.daily,state=error,destination_id=condor,job_runner_name=condor count=8
    server-jobs.daily,state=error,destination_id=condor_resub,job_runner_name=condor count=1
    server-jobs.daily,state=error,destination_id=condor_a,job_runner_name=condor count=11
    server-jobs.daily,state=ok,destination_id=condor,job_runner_name=condor count=2
    server-jobs.daily,state=ok,destination_id=condor_a,job_runner_name=condor count=23
    server-users.daily,active=t,deleted=f,external=f,purged=f count=1
    server-workflows.daily,deleted=f,importable=f,published=f count=1


### meta update

**NAME**

meta update -  Update the script

**SYNOPSIS**

gxadmin meta update

