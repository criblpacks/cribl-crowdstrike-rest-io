# Crowdstrike REST Collector IO
----
## About this Pack

This pack is built as a complete SOURCE + DESTINATION solution (identified by the IO suffix). Data collection and delivery happen entirely within the pack's context, eliminating the need to connect it to globally defined Sources and Destinations. 

This Pack is designed to collect, process, and output Crowdstrike data via the Crowdstrike REST API. It currently supports the following endpoints:
* Host/Device Details ([falconpy link](https://www.falconpy.io/Service-Collections/Hosts.html#getdevicedetailsv2))
* Vulnerabilities ([falconpy link](https://www.falconpy.io/Service-Collections/Spotlight-Vulnerabilities.html#combinedqueryvulnerabilities))
* Endpoint Alerts V2([falconpy link](https://www.falconpy.io/Service-Collections/Alerts.html#postentitiesalertsv2))

The Pack includes OCSF and Splunk output processing. OCSF data is mapped to the following Classes:
* Host/Device Details [Device Inventory Info [5001] Class](https://schema.ocsf.io/1.4.0/classes/inventory_info)
* Vulnerabilities - [Vulnerability Finding [2002] Class](https://schema.ocsf.io/1.4.0/classes/vulnerability_finding)
* Endpoint Alerts - [Detection Finding [2004] Class](https://schema.ocsf.io/1.4.0/classes/detection_finding)

Splunk data is mapped to the following sourcetypes - these are the sourcetypes used by the Crowdstrike-supported TA's:
* Host/Device Details:`sourcetype=crowdstrike:device:json`
* Vulnerabilities: `sourcetype=crowdstrike:spotlight:vulnerability:json`
* Endpoint Alerts (V2): `sourcetype=crowdstrike:unified:alert_v2:json`

Note: The official Crowdstrike API documentation access requires a support contract.

## Deployment

* This pack is configured by default to use the Worker Group's *Default Destination*.
* To use the *Default Destination*: No changes are required. The pack will route the data to the destination currently set as the Default on the Worker Group.
* To use a different Destination: You must update the pack's routes to specify your desired Destination.
* For immediate functionality without requiring Pack route filter expression modifications, every bundled Source within this pack adds a hidden field: `__packsource`. This field allows for seamless routing based on the Pack source.

### Configure the Collectors
* Obtain a `Base URL`, `Client ID` and `Client Secret` from your Crowdstrike Administrator and Update the Pack variables with the information (see below for details). The credentials must have read access to the following API endpoints:
  * `/alerts/entities/alerts/v2`
  * `/spotlight/combined/vulnerabilities/v1`
  * `/devices/entities/devices/v2` 
* Perform a **Run > Preview** of each Collector to verify that they work correctly.
* Schedule the Collectors and adjust the schedule as needed. Collectors requiring State Tracking should already have it enabled.

### Configure Output Format

Each data type can be configured to output data in either normalized JSON, OCSF, or Splunk (`_raw` + Splunk fields) format. Enable *only one* format for each of the following pipelines:
* `cribl_crowdstrike_devices`
* `cribl_crowdstrike_vulnerabilities`
* `cribl_crowdstrike_alerts_v2`

### Configure your Destination/Update Pack Routes
To ensure proper data routing, you must make a choice: retain the current setting to use the Default Destination defined by your Worker Group, or define a new Destination directly inside this pack and adjust the pack's routes accordingly.

### Commit and Deploy
Once everything is configured, perform a Commit & Deploy to enable data collection.

#### Lookups
The Pack includes a lookup called `crowdstrike_device_type_mapping.csv` that is used to generate the [OSCF Device](https://schema.ocsf.io/1.4.0/objects/device) `type` and `type_id` fields:
* `product_type_description`: A user-defined value within Crowdstrike. Add/update entries for your environment.
* `chassis_type_desc`: A standard Crowdstrike field. 

#### Variables

The Pack has the following variables:
* `crowdstrike_base_url`: Your Crowdstrike Base URL (NO backslash at the end!)
* `crowdstrike_client_id`: Your Crowdstrike API Client ID
* `crowdstrike_client_secret`: Your Crowdstrike API Client Secret
* `crowdstrike_alerts_filter_low_severity`: Set to true (the default) to filter out low severity alerts.
* `crowdstrike_default_splunk_index`: Default index for the Splunk output - defaults to `crowdstrike`.

## Upgrades

Upgrading certain Cribl Packs using the same Pack ID can have unintended consequences. See [Upgrading an Existing Pack](https://docs.cribl.io/stream/packs#upgrading) for details.

## Release Notes

### Version 1.1.1
* Event Breaker timestamp fix for Alerts
* Updated AlertsV2 Discovery Filter to eliminate duplicate ingestion
* Alerts pipeline - don't remove additional timestamps

### Version 1.1.0
* REST Collectors now rely on variables for all configuration
* Collector State Tracking and pagination fixes for Alerts and Vulnerabilities

### Version 1.0.1
* Collector parameter bug fixes

### Version 1.0.0
Initial release

## Contributing to the Pack

To contribute to the Pack, please connect with us on [Cribl Community Slack](https://cribl-community.slack.com/). You can suggest new features or offer to collaborate.

## License
This Pack uses the following license: [Apache 2.0](https://github.com/criblio/appscope/blob/master/LICENSE).
