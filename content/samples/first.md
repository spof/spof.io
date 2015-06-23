+++
date = "2015-04-29T18:37:17-07:00"
draft = true
title = "first"

+++
```
#!/bin/bash
cd /tmp
curl -O http://jenkins.local.com/job/build-groot_build-vertx-rpm/ws/vertx-2.1.5-1.x86_64.rpm
curl -O http://jenkins.local.com/job/build-groot_build-groot-rpm/ws/groot-0.0.19-1.x86_64.rpm
rpm -ivh /tmp/vertx-2.1.5-1.x86_64.rpm /tmp/groot-0.0.19-1.x86_64.rpm
yum install -y java
/opt/vertx/vert.x-2.1.5/bin/vertx runzip /opt/groot/target/groot-0.0.19-mod.zip  -instances 4
echo "Hi"
```

{{< highlight bash >}}
#!/bin/bash
cd /tmp
curl -O http://jenkins.local.com/job/build-groot_build-vertx-rpm/ws/vertx-2.1.5-1.x86_64.rpm
curl -O http://jenkins.local.com/job/build-groot_build-groot-rpm/ws/groot-0.0.19-1.x86_64.rpm
rpm -ivh /tmp/vertx-2.1.5-1.x86_64.rpm /tmp/groot-0.0.19-1.x86_64.rpm
yum install -y java
/opt/vertx/vert.x-2.1.5/bin/vertx runzip /opt/groot/target/groot-0.0.19-mod.zip  -instances 4
echo "Hi"
{{< /highlight >}}
