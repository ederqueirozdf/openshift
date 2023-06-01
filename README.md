# openshift
openshift utils

### Note
*You can use a JSONPath filter to retrieve the results. The FILTER variable is written on a single line.*

    [user@host ~]$ FILTER='{range .items[?(@.status.removedInRelease!="")]}{.status.removedInRelease}{"\t"}{.status.requestCount}{"\t"}{.metadata.name}{"\n"}{end}'
    [user@host ~]$ oc get apirequestcounts -o jsonpath="${FILTER}" | \
      column -t -N "RemovedInRelease,RequestCount,Name"
    RemovedInRelease  RequestCount  Name
    1.25              44            cronjobs.v1beta1.batch
    ...output omitted...
