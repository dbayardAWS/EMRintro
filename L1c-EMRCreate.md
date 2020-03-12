# Launch an EMR Cluster

## Create a Cloud9 Development Environment

## Setup network access between Cloud9 and your Redshift cluster

NOTE: If you are following the Redshift Immersion Day lab and you left the InboundTraffic parameter at its default of 0.0.0.0/0, then you do not need to do this section and jump to the [next section](#Gather-the-endpoint-hostname-of-your-Redshift-cluster).

* While leaving your Cloud9 environment open, navigate to the EC2 Console in a different browser tab.

* Click on Instances on the left hand column.  Select the ec2 instance that corresponds to your Cloud9 environment.  Make a note of the Public IP address.  For instance, copy the Public IP address to your clipboard as shown:

![screen](images/net1.png)

* Click on "Security Groups" under "NETWORK & SECURITY" on the left-hand column.  Then select the Security Group that corresponds to your Redshift Cluster.  If following the Redshift Immersion Day labs, its name will be prefixed with the name you gave your CloudFormation stack.

* Be sure the "Inbound" tab is selected on the bottom half of the page and then click Edit.

![screen](images/net2.png)

* In the pop-up, click "Add Rule".  

* In the new line, use the Type drop-down to choose Redshift

* In the source field, enter/paste the Public IP address of your Cloud9 ec2 instance AND append "/32" to it.

![screen](images/net3.png)

* Click the Save button


## Gather the endpoint hostname of your Redshift cluster

If following the Immersion Day labs, this information can be found in the Outputs section of your CloudFormation stack.  Or it can be found via the Redshift console.  We will use the Redshift console to look this up.

* Navigate to the Redshift console

* Click on Clusters on the left hand column.  Then select your redshift cluster.

* In the bottom half of the screen, select the endpoint and copy it.

![screen](images/rs1.png)


## Connect from Cloud9 to Redshift

* Switch back to your open Cloud9 browser tab

* Open up a New File by clicking the + sign to the right of the Terminal tab

![screen](images/rs2.png)

* Paste the endpoint in the new file (so you have it handy for later)

![screen](images/rs3.png)

* Go back to your bash terminal and paste BUT DONT RUN YET the following code

```
psql -p 5439 -U awsuser -d dev -h ENDPOINT-WITHOUT-:5439
```

You terminal will look like this:

![screen](images/rs4.png)

* Delete the part of the command that says "ENDPOINT-WITHOUT-:5439"

* Go to your new file and copy just the hostname part of the endpoint (that is don't copy the :5439 part).

![screen](images/rs4a.png)

* Go back to your bash terminal and paste the hostname and run the command

![screen](images/rs5.png)

Note: If you are not able to connect (the command hangs then eventually times out), you may need to use the Private IP address of the Cloud9 EC2 instance in your Redshift Cluster Security Group inbound rule (as compared to using the Public IP address).  This can happen if your VPC is such that the Cloud9 EC2 subnet is setup to route locally to the Redshift cluster's subnet, rather than via an Internet Gateway.

* Enter the password and have fun.

Note: if you followed the Immersion Day labs and didn't change the default password in the CloudFormation template, then the awsuser password will default to Awsuser123

![screen](images/rs6.png)

You can find tips for psql usage [here](http://postgresguide.com/utilities/psql.html)

Or, look for the "Introduction to psql command-line" entry under the "Using SQL" topic [here](http://pg-au.com/training_decks.html).

Here are some example commands you can run inside psql:

```
CREATE TABLE hello_world   (id int, fname varchar(30));
\timing
\l
\dn
\d
\dt hello_world
\cd /home/ec2-user/environment/amazon-redshift-utils/src/AdminScripts/
\i top_queries.sql
\q

```


Note: If your Cloud9 environment goes to sleep (by default after 30 minutes of inactivity), then when you re-launch the Cloud9 environment, it will have a new Public IP address.  As such you may need to repeat the steps in the [Setup network access between Cloud9 and your Redshift cluster](#Setup-network-access-between-Cloud9-and-your-Redshift-cluster) section.

## Congratulations - you have created a Cloud9 Development Environment and an EC2 Key Pair
Please continue to the [next section](L1c-EMRCreate.md).

