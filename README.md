# CFA Enhanced Monitoring

## Purpose

CFA Enhanced Monitoring dashboard is a project to provide customized insight into availability and performance of CFA network equipments. The dashboard (code maintained elsewhere), invokes Itential workflows to reach and communicate to Network equipments, monitoring tools (SevOne), etc., This repository is to manage the workflows and its dependent assets like JST, Handlebar template, etc.,

### Adapters

* SevOne
* MSSQL DB
* HPNA DB
* IAG

### Ansible Playbooks

* CFA_Stability_Check_IOS_6880
* CFA_Stability_Check_IOS_3850_9300,


### Applications

* CFALib
* TemplateEngine
* WorkflowUtils

### Workflows
* CFAOA_CHK_dev_hpna_bkup_db
* CFAOA_CHK_dev_hpna_db
* CFAOA_CHK_dev_sev1_db
* CFAOA_CHK_device_details_db
* CFAOA_CHK_device_details_mock
* CFAOA_CHK_device_login_db
* CFAOA_CHK_telemetry_sev1_db
* CFAOA_DB_CreateCheckJobRecord
* CFAOA_DB_CreateJobRecord
* CFAOA_DB_UpdateCheckJobRecord
* CFAOA_DB_UpdateJobRecord
* CFAOA_DB_getCheck
* CFA_EM_CheckExecutor
* CFA_LookupPlaybookForDeviceModel
* CFA_sev1_getDeviceObjects
* CFA_sev1_getIndicatorValues

## Installation

Please ensure that all prerequisites mentioned above are available.

### Workflows

TODO

### Design

Refer this confluence space for [CFA Ops Acceptance WorkFlows Automation Design](https://confluence.savvis.net/display/manserv/Automation+Design)

#### CFA Workflows arrangement details

As of today *12/30/2021*
CFA Dashboard needs to invoke Itential Workflows for two requirements.

1. Collecting SevOne Telemetry data to display RAG status against Network devices.
2. To obtain output of certain CLI commands against given Network device in real time.

##### SevOne Telemetry data
The following flows are used for collecting SevOne Telemetry data.

* CFA_sev1_getDeviceObjects
* CFA_sev1_getIndicatorValues

This is not using the SevOne Adapter directly but via a custom built wrapper around SevOne Adapter to filter the relevant Objects and Indicators. Refer Adapter CFALib for more details.

##### To Obtain output for commands

For this purpose, the workflow **CFA_EM_CheckExecutor** is the entry point. Rest of the flows are subflows underneath. This workflow takes the following input

device name - Name of the device to run the commands against
device model string - To issue relevant commands for the appropriate device type/model.
check_id - executing commands against device is one of the several functions. This check_id distinguish the actual function to perform against the device. check_id 2001 is for commands execution.

#### Workflow Runner

All the above workflows are invoked via a mini app called WorkflowRunner sitting in between the Itential IAP system and the actual caller. This provides a controlled access of the workflows without dealing with complex multi-factor/ Azure Authentication layer. This is a common utility used for many projects including this one.