---
mapped_urls:
  - https://www.elastic.co/guide/en/security/current/detection-engine-overview.html
  - https://www.elastic.co/guide/en/serverless/current/security-detection-engine-overview.html
---

# Detections and alerts

% What needs to be done: Align serverless/stateful

% Use migrated content from existing pages that map to this page:

% - [x] ./raw-migrated-files/security-docs/security/detection-engine-overview.md
% - [ ] ./raw-migrated-files/docs-content/serverless/security-detection-engine-overview.md

% Internal links rely on the following IDs being on this page (e.g. as a heading ID, paragraph ID, etc):

$$$support-indicator-rules$$$

$$$detections-permissions$$$

$$$machine-learning-model$$$

Use the detection engine to create and manage rules and view the alerts these rules create. Rules periodically search indices (such as `logs-*` and `filebeat-*`) for suspicious source events and create alerts when a rule’s conditions are met. When an alert is created, its status is `Open`. To help track investigations, an alert’s [status](/solutions/security/detect-and-alert/manage-detection-alerts.md#detection-alert-status) can be set as `Open`, `Acknowledged`, or `Closed`.

:::{image} ../../images/security-alert-page.png
:alt: Alerts page
:screenshot:
:::

In addition to creating [your own rules](/solutions/security/detect-and-alert/create-detection-rule.md), enable [Elastic prebuilt rules](/solutions/security/detect-and-alert/install-manage-elastic-prebuilt-rules.md#load-prebuilt-rules) to immediately start detecting suspicious activity. For detailed information on all the prebuilt rules, see the [*Prebuilt rule reference*](security-docs://reference/prebuilt-rules/index.md) section. Once the prebuilt rules are loaded and running, [*Tune detection rules*](/solutions/security/detect-and-alert/tune-detection-rules.md) and [Add and manage exceptions](/solutions/security/detect-and-alert/add-manage-exceptions.md) explain how to modify the rules to reduce false positives and get a better set of actionable alerts. You can also use exceptions and value lists when creating or modifying your own rules.

There are several special prebuilt rules you need to know about:

* [**Endpoint protection rules**](/solutions/security/manage-elastic-defend/endpoint-protection-rules.md): Automatically create alerts based on {{elastic-defend}}'s threat monitoring and prevention.
* [**External Alerts**](https://www.elastic.co/guide/en/security/current/external-alerts.html): Automatically creates an alert for all incoming third-party system alerts (for example, Suricata alerts).

If you want to receive notifications via external systems, such as Slack or email, when alerts are created, use the {{kib}} [Alerting and Actions](/explore-analyze/alerts-cases.md) framework.

::::{note}
To use {{kib}} Alerting for detection alert notifications, you need the [appropriate license](https://www.elastic.co/subscriptions).
::::


After rules have started running, you can monitor their executions to verify they are functioning correctly, as well as view, manage, and troubleshoot alerts (see [*Manage detection alerts*](/solutions/security/detect-and-alert/manage-detection-alerts.md) and [*Monitor and troubleshoot rule executions*](/troubleshoot/security/detection-rules.md)).

You can create and manage rules and alerts via the UI or the [Detections API](https://www.elastic.co/docs/api/doc/kibana/group/endpoint-security-detections-api).

::::{important}
To make sure you can access Detections and manage rules, see [*Detections requirements*](/solutions/security/detect-and-alert/detections-requirements.md).

::::



## Compatibility with cold and frozen tier nodes [cold-tier-detections]

Cold and frozen [data tiers](/manage-data/lifecycle/data-tiers.md) hold time series data that is only accessed occasionally. In {{stack}} version >=7.11.0, {{elastic-sec}} supports cold but not frozen tier data for the following {{es}} indices:

* Index patterns specified in `securitySolution:defaultIndex`
* Index patterns specified in the definitions of detection rules, except for indicator match rules
* Index patterns specified in the data sources selector on various {{security-app}} pages

{{elastic-sec}} does **NOT** support either cold or frozen tier data for the following {{es}} indices:

* Index patterns controlled by {{elastic-sec}}, including alerts and list indices
* Index patterns specified in the definition of indicator match rules

Using either cold or frozen tier data for unsupported indices may result in detection rule timeouts and overall performance degradation.


## Limited support for indicator match rules [support-indicator-rules]

Indicator match rules provide a powerful capability to search your security data; however, their queries can consume significant deployment resources. When creating an [indicator match rule](/solutions/security/detect-and-alert/create-detection-rule.md#create-indicator-rule), we recommend limiting the time range of the indicator index query to the minimum period necessary for the desired rule coverage. For example, the default indicator index query `@timestamp > "now-30d/d"` searches specified indicator indices for indicators ingested during the past 30 days and rounds the query start time down to the nearest day (resolves to UTC `00:00:00`). Without this limitation, the rule will include all of the indicators in your indicator indices, which may extend the time it takes for the indicator index query to complete.

In addition, the following support restrictions are in place:

* Indicator match rules don’t support cold or frozen data. Cold or frozen data in indices queried by indicator match rules must be older than the time range queried by the rule. If your data’s timestamps are unreliable, you can exclude cold and frozen tier data using a [Query DSL filter](/solutions/security/detect-and-alert/exclude-cold-frozen-data-from-individual-rules.md).
* Indicator match rules with an additional look-back time value greater than 24 hours are not supported.


## Detections configuration and index privilege prerequisites [detections-permissions]

[*Detections requirements*](/solutions/security/detect-and-alert/detections-requirements.md) provides detailed information on all the permissions required to initiate and use the Detections feature.


## Malware prevention [malware-prevention]

Malware, short for malicious software, is any software program designed to damage or execute unauthorized actions on a computer system. Examples of malware include viruses, worms, Trojan horses, adware, scareware, and spyware. Some malware, such as viruses, can severely damage a computer’s hard drive by deleting files or directory information. Other malware, such as spyware, can obtain user data without their knowledge.

Malware may be stealthy and appear as legitimate executable code, scripts, active content, and other software. It is also often embedded in non-malicious files, non-suspicious websites, and standard programs — sometimes making the root source difficult to identify. If infected and not resolved promptly, malware can cause irreparable damage to a computer network.

For information on how to enable malware protection on your host, see [Malware Protection](/solutions/security/configure-elastic-defend/configure-an-integration-policy-for-elastic-defend.md#malware-protection).


### Machine learning model [machine-learning-model]

To determine if a file is malicious or benign, a machine learning model looks for static attributes of files (without executing the file) that include file structure, layout, and content. This includes information such as file header data, imports, exports, section names, and file size. These attributes are extracted from millions of benign and malicious file samples, which then are passed to a machine-learning algorithm that distinguishes a benign file from a malicious one. The machine learning model is updated as new data is procured and analyzed.


### Threshold [_threshold]

A malware threshold determines the action the agent should take if malware is detected. The Elastic Agent uses a recommended threshold level that generates a balanced number of alerts with a low probability of undetected malware. This threshold also minimizes the number of false positive alerts.


## Ransomware prevention [ransomware-prevention]

Ransomware is computer malware that installs discreetly on a user’s computer and encrypts data until a specified amount of money (ransom) is paid. Ransomware is usually similar to other malware in its delivery and execution, infecting systems through spear-phishing or drive-by downloads. If not resolved immediately, ransomware can cause irreparable damage to an entire computer network.

Behavioral ransomware prevention on the Elastic Endpoint detects and stops ransomware attacks on Windows systems by analyzing data from low-level system processes, and is effective across an array of widespread ransomware families — including those targeting the system’s master boot record.

For information on how to enable ransomware protection on your host, see [Ransomware protection](/solutions/security/configure-elastic-defend/configure-an-integration-policy-for-elastic-defend.md#ransomware-protection).

::::{note}
Ransomware prevention is a paid feature and is enabled by default if you have a [Platinum or Enterprise license](https://www.elastic.co/pricing).
::::



### Resolve UI error messages [_resolve_ui_error_messages]

Depending on your privileges and whether detection system indices have already been created for the {{kib}} space, you might get one of these error messages when you open the **Alerts** or **Rules** page:

* **`Let’s set up your detection engine`**

    If you get this message, a user with specific privileges must visit the **Alerts** or **Rules** page before you can view detection alerts and rules. Refer to [Enable and access detections](/solutions/security/detect-and-alert/detections-requirements.md#enable-detections-ui) for a list of all the requirements.

    ::::{note}
    For **self-managed** {{stack}} deployments only, this message may be displayed when the [`xpack.encryptedSavedObjects.encryptionKey`](/solutions/security/detect-and-alert.md#detections-permissions) setting has not been added to the `kibana.yml` file. For more information, refer to [Configure self-managed {{stack}} deployments](/solutions/security/detect-and-alert/detections-requirements.md#detections-on-prem-requirements).
    ::::

* **`Detection engine permissions required`**

    If you get this message, you do not have the [required privileges](/solutions/security/detect-and-alert.md#detections-permissions) to view the **Detections** feature, and you should contact your {{kib}} administrator.

    ::::{note}
    For **self-managed** {{stack}} deployments only, this message may be displayed when the [`xpack.security.enabled`](/solutions/security/detect-and-alert.md#detections-permissions) setting is not enabled in the `elasticsearch.yml` file. For more information, refer to [Configure self-managed {{stack}} deployments](/solutions/security/detect-and-alert/detections-requirements.md#detections-on-prem-requirements).
    ::::



## Using logsdb index mode [detections-logsdb-index-mode]

To learn how your rules and alerts are affected by using the [logsdb index mode](/manage-data/data-store/data-streams/logs-data-stream.md), refer to [*Using logsdb index mode with {{elastic-sec}}*](/solutions/security/detect-and-alert/using-logsdb-index-mode-with-elastic-security.md).

