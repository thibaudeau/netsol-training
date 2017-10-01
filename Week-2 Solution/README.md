# Reporting on my Environment

What I have done is get device info with the Inventory-Report.yml playbook via NAPALM-ansible.

The playbook outputs the info I collected in the folder __"reports"__ as bot JSON and YAML.

I also created a quick jinja2 template (templates/arp-report.j2) to output the ARP table as a CSV file sorted by MAC addresses in the same folder.

I really wanted to take this up a level of complexity but I had an extremely busy week... You know, life happens.

Thanks,

Simon
