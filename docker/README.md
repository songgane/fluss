- Deploying with Helm Charts
  - https://fluss.apache.org/docs/install-deploy/deploying-with-helm/
  - helm repo
    https://downloads.apache.org/incubator/fluss/helm-chart/0.8.0-incubating/

```
root@tablet-server-0:/opt/fluss/plugins# ls hdfs/
fluss-fs-hdfs-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls jmx/
fluss-metrics-jmx-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls lance/
fluss-lake-lance-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls s3/
fluss-fs-s3-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls prometheus/
fluss-metrics-prometheus-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls iceberg/
fluss-lake-iceberg-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls paimon/
flink-shaded-hadoop-2-uber-2.8.3-10.0.jar  fluss-lake-paimon-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls
hdfs  iceberg  jmx  lance  oss  paimon  prometheus  s3
```

"Fluss uses file systems as remote storage to store snapshots for Primary-Key Table and store tiered log segments for Log Table."
local file systems 에 log와 primary key table을 저장하고
tiered 구조로 remote storage를 구성해 snapshot등을 저장한다.
hot -> cold 이동은 어떻게 할까?

- custom image build
```
docker build . -t registry.tde.sktelecom.com/emergingdp/tlake/fluss:0.8.0-incubating-hive-iceberg-s3 --plat
form linux/amd64 -f docker/Dockerfile.iceberg-s3
docker push registry.tde.sktelecom.com/emergingdp/tlake/fluss:0.8.0-incubating-hive-iceberg-s3
```