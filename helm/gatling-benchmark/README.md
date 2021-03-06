# Helm Chart for Gatling benchmark

This helm chart have two purposes. First one is to run actual tests, second one
to expose results that can be viewed/downloaded

## Setup

### Prerequisites
This helm chart expects product deployment to be running and configured.

### Gatling benchmark image
You can either get gatling benchmark image from bintray forgerock repository
or build it by yourself. Docker image dockerfile can be found in `forgeops/docker/gatling`.
To add custom benchmarks, you need to add them to `forgeops/docker/gatling/simulations` folder.

If you are building docker image by yourself, don't forget to change docker image
name in values.yaml to match your image.

### Benchmark configuration
Benchmark configuration can be found in values.yaml file.
You can configure all necessary test related variables there.

## Run benchmark
To run the benchmark, run following

`helm install --name benchmark gatling-benchmark`

Now you can list pods in your namespace. Output should be as this:

```
kubectl get pods
NAME                                     READY     STATUS     RESTARTS   AGE
amster-7f58b78755-5bxn4                  2/2       Running    0          7m
configstore-0                            1/1       Running    0          7m
ctsstore-0                               1/1       Running    0          7m
forgeops-benchmark-8566b4cf98-4j78b      0/1       Init:0/1   0          3m
openam-pyforge-openam-6c7575b4f5-5cxxh   1/1       Running    0          7m
userstore-0                              1/1       Running    0          7m
```

Benchmark pod will be in Init:0/1 state until tests are finished.

To see gatling progress output, run `./logs.sh`.

## Access benchmark results

Once tests are finished, you need to make sure you have ingress address with
fqdn in your /etc/hosts file.


```
kubectl get ingress
NAME      HOSTS                          ADDRESS         PORTS     AGE
gatling   gatling.pyforge.forgeops.com   35.227.42.137   80        3m
openam    openam.pyforge.forgeops.com    35.227.42.137   80        7m
```
Then you can simply access results by going to :
`http://gatling.[NAMESPACE].[DOMAIN]/` e.g. `http://gatling.pyforge.forgeops.com`

Accessing this URL will show following:

```
Index of /
../
restlogin-1523282212519/                           09-Apr-2018 14:59                   -
restlogin-1523282212519.tar.gz                     09-Apr-2018 15:00            33988974
```

Folder contains HTML report which can be directly opened and inspected.
Archive .tar.gz can be downloaded and kept for future usage. Once helm chart with
benchmark is deleted, you can no longer access these files, so download it if you need to archive results.
