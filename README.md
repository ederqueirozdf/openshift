# openshift
openshift utils

### Identifying Deprecated APIs

#####  Note
˜You can use a JSONPath filter to retrieve the results. The FILTER variable is written on a single line.˜

        [user@host ~]$ FILTER='{range .items[?(@.status.removedInRelease!="")]}{.status.removedInRelease}{"\t"}{.status.requestCount}{"\t"}{.metadata.name}{"\n"}{end}'

        [user@host ~]$ oc get apirequestcounts -o jsonpath="${FILTER}" | \
          column -t -N "RemovedInRelease,RequestCount,Name"
        RemovedInRelease  RequestCount  Name
        1.25              44            cronjobs.v1beta1.batch
        ...output omitted...

You can extract the alerts in JSON format from the Prometheus stateful set, and then filter the result to retrieve the deprecated API alerts.

        [user@host ~]$ oc exec -it statefulset/prometheus-k8s -c prometheus \
          -n openshift-monitoring -- \
            curl -fsSL 'http://localhost:9090/api/v1/alerts' | jq . > alerts.json

        [user@host ~]$ jq '[.data.alerts[] |
          select(.labels.alertname=="APIRemovedInNextReleaseInUse" or
                 .labels.alertname=="APIRemovedInNextEUSReleaseInUse")]' < alerts.json
        [
          {
            "labels": {
              "alertname": "APIRemovedInNextReleaseInUse",
        ...output omitted...
            },
            "state": "firing",
        ...output omitted...
          },
          {
            "labels": {
              "alertname": "APIRemovedInNextEUSReleaseInUse",
        ...output omitted...
            },
            "state": "firing",
        ...output omitted...
          }
