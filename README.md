# Expiring F5 Certificates Audit

- [Expiring F5 Certificates Audit](#expiring-f5-certificates-audit)
  - [Prerequisites](#prerequisites)
    - [Adapters](#adapters)
    - [Ansible Playbooks](#ansible-playbooks)
    - [Additional Workflows](#additional-workflows)
  - [Installation](#installation)
    - [Workflows](#workflows)
      - [F5Certs: MAIN Process F5 Details](#f5certs-main-process-f5-details)
        - [Process CMDB Raw Output](#process-cmdb-raw-output)
        - [Post Process F5s](#post-process-f5s)
      - [F5Certs: CHILD Get F5 Details](#f5certs-child-get-f5-details)
        - [Transform bigip_cert_info to CertificateObjectList.jst](#transform-bigip_cert_info-to-certificateobjectlistjst)
    - [Automation Catalog](#automation-catalog)

## Prerequisites

The Ansible playbook is based on the [F5 Modules for Ansible](https://galaxy.ansible.com/f5networks/f5_modules) and supports BIG-IP 12.x onwards.
Please see [F5 Modules for Ansible -> Releases and Versioning](https://clouddocs.f5.com/products/orchestration/ansible/devel/usage/supported-versions.html) for exact details.

### Adapters

- [EmailV2 Adapter](https://github.com/CenturyLink/ias-adapter-emailV2): To send emails
- [app-template_engine](https://github.com/CenturyLink/ias-app-template-engine): Used to create the email body

### Ansible Playbooks

- bigip_cert_info: Ansible playbook to run against F5 Load Balancers

### Additional Workflows

- [IAS Common Workflows](https://github.com/CenturyLink/ias-workflows)

## Installation

Please ensure that all prerequisites mentioned above are available.

### Workflows

1. Log into the IAP console and choose the 'Automation Studio' from the menu.
2. Select the six squares image icon from the 'Automations' section in left pane.

   ![Six Squares](docs/common/studioSix.png)

3. Choose the import icon. Select all workflows in the 'workflows' directory. Each should be imported.

   ![Import Workflows](docs/common/importWorkflows.png)

4. Open each of the workflows detailed bellow to configure transforms. N.B. if the workflow is not detailed bellow, it is assumed that there are no transforms.
   1. Double click on the JST to open it's specific dialog.
   2. Next to the 'Transformation Name' click the 'X' button.
   3. Click the Upload button next to the 'X' button.
   4. In the workflows\\_workflow name_ directory, pick the transform.
   5. Set the values of each incoming schema. N.B. Pay attention to both the Location and Value properties to ensure they match exactly.
   6. Choose save.
5. Save the work flow by choosing save.

#### F5Certs: MAIN Process F5 Details

![MAIN Process F5 Details](docs/f5CertsMAINProcessF5Devices.png) \* each JST is shown in order and is configured as above using the following steps:

##### Process CMDB Raw Output

This processes the output from the CMDB, filtering the results to just those that can be processed by this workflow.

| Name      | Location          | Value  |
| --------- | ----------------- | ------ |
| **f5Raw** | getNetworkDevices | result |

##### Post Process F5s

This post-processes the results from all of the F5s, sorting by certificate and separating into expiring and expired certificates.

| Name         | Location | Value    |
| ------------ | -------- | -------- |
| **f5Result** | job      | f5Result |

#### F5Certs: CHILD Get F5 Details

![CHILD Get F5 Details](docs/F5CertsCHILDGetF5Details.png) \* each JST is shown in order and is configured as above using the following steps:

##### Transform bigip_cert_info to CertificateObjectList.jst

This processes the output from each F5, filtering for certificates that match the criteria specified in the main workflow.

| Name                | Location                     | Value         |
| ------------------- | ---------------------------- | ------------- |
| **bigip_cert_info** | Extract response from stdout | return_data   |
| **expiryObject**    | job                          | expirtyObject |

### Automation Catalog

Please ensure that all of the [workflows](#workflows) have been added before proceeding with Automation Catalog entries.

1. Log into the IAP console and choose the 'Automation Catalog' from the menu.
2. Select the six squares image icon from the 'Automations' section in left pane.

   ![Six Squares](docs/common/catalogSix.png)

3. Choose the import icon. Select all automations in the 'catalog' directory. Each should be imported.

   ![Import Automations](docs/common/importAutomations.png)

4. Open one of the Automations and press the 'New Form' button.

   ![New Form](docs/common/automationNewForm.png)

5. Select the siz squares image icon from the 'Forms' section in the left pane.
6. From the three dots, choose Import form(s).  Select all forms in the 'catalog/forms' directory.
7.
   ![Import Forms](docs/common/importForms.png)

8. Return to the main menu by pressing the home icon in the top bar then choose the 'Automation Catalog' from the menu.
9. Assign the specific Form to each Automation as detailed in the table below.  N.B. if the automation is not detailed bellow, it is assumed that there are no transforms.
   1. Select the Automation from the 'Automations' pane on the left hand side.
   2. Choose the form from the Form drop down menu in the centre pane.
   3. Set a schedule if specified below.
   4. Click 'Save'

      | Automation Name | Form | Schedule |
      | --------------- | ---- | -------- |
      | **F5 Expiring Certificates** | F5Certs: Catalog Form | **Run At:** 06:00:00 AM<br>**Repeat:** Monthly<br>---<br>**Days:** 90 <br> **TO:** email address <br> **CC:** email address|
