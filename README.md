Prometheus WebHook to SNMP-trap forwarder
=========================================

This is a quick (and dirty) way to get Prometheus to send SNMP traps, by mapping AlertManager "Annotations" and "Labels" to generic SNMP OIDs.

Integration with Prometheus
---------------------------
1. Prometheus gathers metricscd 
2. Prometheus appraises metrics against rules
3. If rules are triggered then alerts are raised through the AlertManager
4. The AlertManager triggers notifications to the webhook_snmptrapper
5. The webhook_snmptrapper forwards alerts as SNMP traps to the configured trap-address

SNMP integration
----------------
The traps are being send with the enterprise ID of .1.3.6.1.4.1.56.12.1.7 and the following specific IDs that designate alerts' severity levels:

### Specific IDs:
- ***0***: normal/resolved
- ***1***: undetermined
- ***2***: warning
- ***3***: minor
- ***4***: major
- ***5***: critical

### SNMP variables (as of version 0.0.9):
- ***Component***: An instance or a hostname
- ***SubComponent***: -- not populated yet --
- ***Severity***: Alert's severity as set in prometheus rules configuration
- ***Message***: Alert's text
- ***Summary***: Alert's short description
- ***Namespace***: Kuberneters namespace of the alerting component
- ***Application***: Alert's prom_application as set in prometheus rules configuration
- ***Object***: Alert's prom_object as set in prometheus rules configuration
- ***Datacenter***: Alerting component's location by external_label

Command-line flags
------------------
- **-snmpcommunity**: The SNMP community string (_default_ = `public`)
- **-snmpretries**: The number of times to retry sending traps (_default_ = `1`)
- **-snmptrapaddress**: The address to send traps to (_default_ = `127.0.0.1:162`)
- **-webhookaddress**: The address to listen for incoming webhooks on (_default_ = `0.0.0.0:9099`)



THE FOLLOWING IS NOT IN USE (legacy from chrusty/prometheus_webhook_snmptrapper):
------------------
    The provided MIB (`PROMETHEUS-TRAPPER-MIB.txt`) defines two notifications:
    - ***prometheusTrapperFiringNotification***: Notification for an alert that has occured
    - ***prometheusTrapperRecoveryNotification***: Notification for an alert that has recovered

    The MIB can be loaded into whatever SNMP Trap-server you're using. See [Dockerfile](trapdebug/net-snmp/Dockerfile) for a working demo using net-snmp on Alpine Linux.

    ### MIB's SNMP variables
    Both of these traps contain the following variables:
    - ***prometheusTrapperNotificationInstance***: The instance or hostname
    - ***prometheusTrapperNotificationService***: A name for the service affected
    - ***prometheusTrapperNotificationLocation***: The physical location where the alert was generated
    - ***prometheusTrapperNotificationSeverity***: The severity of the alert
    - ***prometheusTrapperNotificationDescription***: Text description of the alert
    - ***prometheusTrapperNotificationTimestamp***: When the alert was first generated
------------------
