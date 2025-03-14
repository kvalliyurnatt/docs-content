# Monitoring and security [secure-monitoring]

The {{stack}} {{monitor-features}} consist of two components: an agent that you install on each {{es}} and Logstash node, and a Monitoring UI in {{kib}}. The monitoring agent collects and indexes metrics from the nodes and you visualize the data through the Monitoring dashboards in {{kib}}. The agent can index data on the same {{es}} cluster, or send it to an external monitoring cluster.

To use the {{monitor-features}} with the {{security-features}} enabled, you need to [set up {{kib}} to work with the {{security-features}}](../../../deploy-manage/security.md) and create at least one user for the Monitoring UI. If you are using an external monitoring cluster, you also need to configure a user for the monitoring agent and configure the agent to use the appropriate credentials when communicating with the monitoring cluster.

For more information, see:

* [*Monitoring in a production environment*](../../../deploy-manage/monitor/stack-monitoring/elasticsearch-monitoring-self-managed.md)
* [Configuring monitoring in {{kib}}](/deploy-manage/monitor/stack-monitoring/kibana-monitoring-legacy.md)
* [Configuring monitoring for Logstash nodes](logstash://reference/monitoring-logstash-legacy.md)

