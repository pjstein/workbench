# Workbench
## Automation 4 Me

Workbench is intended to provide some boilerplate automation for personal
projects. Damn near every `Vagrantfile` ends up looking basically the same so 
I've written this one, and I'll never need to write another.

This is setup to use [chef](http://docs.opscode.com/) for provisioning. It will
perform provisioning if the project housing this `Vagrantfile` includes a 
`cookbooks` directory. If that's the case, the it'll find all the cookbooks with
a `default` recipe and run those, passing nothing into `chef.json`.

You can, however, put two files in your `cookbooks` directory to override that.
The names of these files are defined in the `Vagrantfile`. Currently, you can
specify an ordered runlist using the `.workbench.runlist` file, and you can 
specify `chef.json` using the `.workbench.config.json` file.

### Notes

1. Make sure that [the box](http://chickenandwaffl.es/boxes/ubuntu-12.04-x86_64.box)
stays online. If I move it, remember to update the reference in the `Vagrantfile`.
2. The IP address is set in the `Vagrantfile` (it's the `IP_ADDRESS` thing). If
you're going to spin up more than on `workbench` box, then you'll need to change
it based on the project. Maybe I can automate that by hashing the project
name? I'm not super sure that's necessary, but worth keeping in mind.

-PJ$, 12.29.2013
