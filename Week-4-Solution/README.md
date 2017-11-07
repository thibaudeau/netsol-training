# Week 4 Deploy Network Services From a Data Model

I have setup the lab with 5 routers, 2 hubs and 3 spokes. Each spoke is a branch, and each hub is a small Datacenter.

## Create Initial Device Configurations
### Base and Function Configurations

Playbook "create-base-config.yml" uses the "base" role and the function-specific roles (hub and branches) to build the configurations and deploy them. Output of the playbook is in the "compiled" folder

Base role:

        base
        ├── tasks
        │   └── main.yml
        └── templates
            └── ios
                └── base.j2
Data model uses the common group_vars  and business logic from previous weeks exercise to build the config.

Branch and Hub roles follow the same pattern:

        branch
        ├── tasks
        │   └── main.yml
        └── templates
            └── ios
                └── branch.j2

Most of the configuration here is derived by business logic.

### Fabric Configurations

Fabric configurations is done by the "create-fabric-config.yml". I use the fabric role to depoy the fabric. Output is the fabric.conf configuration file in the "compiled" folder.

Fabric role looks like this:

        fabric
        ├── tasks
        │   └── main.yml
        ├── templates
        │   └── ios
        │       └── fabric.j2
        └── vars
            └── main.yml

Data model is in the "vars" subfolder.

## Validate Infrastructure Configurations

Validation is done via the validate-fabric.yml playbook. Which also import the includes/validate.yml tasks.

I do a data model transformation from the roles/fabric/vars/main.yml data model in order to have something a little more useful during the validation. The Jinja2 template for the transformation is in the templates folder. I output the new data model to the validations folder for each of the nodes in the network.

        .
        ├── includes
        │   └── validate.yml
        ├── roles
        │   ├── fabric
        │   │   └── vars
        │   │       └── main.yml
        ├── templates
        │   └── fabric-to-nodes.j2
        ├── validate-fabric.yml
        └── validations
            ├── branch-11_node.yml
            ├── branch-12_node.yml
            ├── branch-13_node.yml
            ├── hub-1_node.yml
            └── hub-2_node.yml


I check for BGP neighbors, that the bgp neighbor is up, and that the ASN of the peer matches the model.

## Deploy VoIP Services

In this part I deploy a VOIP "VLAN" on each of the branch routers as well as three server VLANs on the Hubs, with HSRP and routing.

I have voip role much like the other parts of the assignment. Vars for the role are stored in the role/voip/vars folder and are limited. I derived much of the IP configurations from node IDs once again.

      roles
      ├── remove_voip
      │   ├── tasks
      │   │   └── main.yml
      │   ├── templates
      │   │   └── ios
      │   │       └── voip.j2
      │   └── vars
      │       └── main.yml
      └── voip
          ├── tasks
          │   └── main.yml
          ├── templates
          │   └── ios
          │       └── voip.j2
          └── vars
              └── main.yml

I started only with the voip role, and subsequently added the remove_voip role to enable removal of the service. Passing the variable state=disabled at run time removes the service from all or a limited amount of the nodes depending on the scope of the playbook. The playbook to deploy this service is _deploy-voip.yml_. Config snippets generated are in the _compiled_ folder both for the removal and deployment

### Validation of the VoIP service

As with the validation of the infrastructure, I do a model transformation to generate the validation. I have template called _voip-to-validation.j2_ that generates a yaml file in the format required by the napalm_validate ansible module. The folder validations has the generated yaml files. I check that interfaces deployed are as per model and ping hubs and branches accordingly.

      templates
      └── voip-to-validation.j2
      validations
      ├── branch-11_voip.yml
      ├── branch-12_voip.yml
      ├── branch-13_voip.yml
      ├── hub-1_voip.yml
      └── hub-2_voip.yml
      validate-voip.yml

The playbook to run the validations is _validate-voip.yml_


## Remainder of the Assignment

I am still working on the remainder of the assignment. Namely:

3. Check device state before configuring the services and reject the change if it would overwrite an existing service. For example, reject adding a device into a VRF if it already belongs to another VRF (another customer).

4. Report extraneous services – list all target services (example: all VRFs) configured on a device, compare them to the expected list of configured services, and report any discrepancies.

5. Automatic cleanup – automatically remove all unexpected services found on the network devices.
