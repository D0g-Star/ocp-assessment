# Install the Openshift Logging Operator
# Create Cluster Logging Instance
# Create Cluster Logging Forwarder
## Tested Log Forwarders:
    * Splunk Server
    * Syslog
## Notes:
* Fluentd does not support Elasticsearch 8 in the logging version 5.6.2.
* Vector supports Syslog in the logging version 5.7 and higher
* https://docs.openshift.com/container-platform/4.13/observability/logging/log_collection_forwarding/logging-output-types.html