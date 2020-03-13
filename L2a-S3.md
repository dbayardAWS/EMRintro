# Working with Hive and Pig

This lab demonstrates the steps involved in submitting Hive and Pig jobs on EMR.  Hive and Pig jobs can be submitted interactively via the Hive and Pig shells on the master node or jobs can be submitted directly to the cluster using the console, CLI, or EMR API.  Jobs can be submitted at cluster creation and the cluster can be auto-terminated afterwards (ephemeral clusters) or multiple jobs can be submitted to long living clusters.  In this lab we’ll go through submitting jobs interactively via shells and EMR steps.

## Create a S3 bucket and populate it with data
