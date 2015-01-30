consul-boshworkspace
================

## Deploy Consul

__Create deployment file__
Just as with the microbosh deployment file we need to fill in some information in our Consul deployment file.

```
export CONSUL_SG=<your consul security group>
export IPMASK=<the first two octets of your AWS VPC, i.e., 10.10 or 10.0>
export CONSUL_SUBNET=<the subnet to put the consul servers on>
export CONSUL_ZONE=<What availability zone to use>
```

Now lets replace the placeholders in `consul-aws.yml`

```bash
for VAR in CONSUL_SG IPMASK CONSUL_SUBNET CONSUL_ZONE
do
  eval REP=\$$VAR
  perl -pi -e "s/$VAR/$REP/g" deployments/consul-aws.yml
done
```

__Upload dependencies__
Our Consul deployment depends on the `consol-release` and on the `bosh-stemcell`, before we can deploy we will need to make sure those dependencies have been resolved. Luckily the bosh-workspace has build in support for resolving those depedencies. 
```
bosh deployment consul-aws
bosh prepare deployment
```
> Alternatively when not using an inception server, you can use a remote release: 
`bosh upload release https://community-shared-boshreleases.s3.amazonaws.com/boshrelease-consul-10.tgz`

__Deploy__
With the dependencies resolved it's time to deploy Consul version 10. This will create a cluster of 3 machines.

```
bosh deploy
```
