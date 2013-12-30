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

### Requirements

Currently targeting: 

1. [Virtual Box 4.3.6](https://www.virtualbox.org/wiki/Downloads "Virtual Box 4.3.6")
2. [Vagrant 1.4.1](http://www.vagrantup.com/downloads.html "Vagrant 1.4.1")

Install those pieces of software. Make sure they exist. VirtualBox will be
available on the system with a GUI. You can make sure that Vagrant exists using 
the command line.

    waffles:workbench pstein$ vagrant --version
    Vagrant 1.4.1

### Usage

This is a `Vagrantfile`. It can be used like any `Vagrantfile`. You can read 
more about Vagrant [here](http://docs.vagrantup.com/v2/ "Vagrant Documentation").
Move your terminal into the directory that contains the `Vagrantfile`. The basic
commands are:

- `vagrant up`: Spins up a new virtual machine.
- `vagrant destroy`: Destroys the virtual machine
- `vagrant ssh`: SSHs into the virtual machine.
- `vagrant provision`: Reprovisions the virtual machine. 

### The Basics of Provisioning (An Essay for University)

*Merriam-Webster's Collegiate Dictionary (Laminated Cover)* by Merriam-Webster
defines "provision" as "the act or process of supplying or providing something".
Indeed, when discussing computers made in the tradition of the Von Neumann 
architecture, one means process in more ways that one! 
(*ed.- The author wore quite the shit-eating grin when he penned that line*).

In the context which has been established by the virtualization paradigm by 
computer scientists -- or "hackers" as they are ofttimes called -- provisioning
means supplying a virtual machine with its configuration and, in some ways, it's
soul. The act of provisioning a virtual machine  can install and update software
on the machine, change the configuration of applications and services on the 
machine, start and stop services on the machine, and perform a whole host of 
other useful functions!

The agent used for provisioning "virtual" machines in this piece is Chef!
(*ed.- Why is "virtual" now in quotes...*). Chef is "a configuration management 
tool written in Ruby and Erlang. It uses a pure-Ruby, domain-specific language 
(DSL) for writing system configuration 'recipes'. Chef is used to streamline 
the task of configuring & maintaining a company's servers, and can integrate 
with cloud-based platforms such as Rackspace and Amazon EC2 to automatically 
provision and configure new machines" (Wikipedia, [http://en.wikipedia.org/wiki/Chef_(software)](http://en.wikipedia.org/wiki/Chef_(software))).

One could think of "provisioning" as a creative-restorative act; that is, to 
provision is to give life, a soul, to the machine, *deus ad machina*. 
(*ed.- That Latin is definitely wrong. This guy is insufferable*). Vagrant is 
used to interface with Virtualbox and create a new blank virtual machine, a 
*tabula rasa*. Chef then takes over and executes commands on the system to put
it into the state ordained by the creator defined in the recipes. 

The project appears on the computer like this:

    [Ex. 1]

    my-great-project
        |- Vagrantfile
        |- cookbooks
        |--- a-cookbook
        |----- recipes
        |------- default.rb
        |--- another-cookbook
        |------- default.rb

Where more dashes -- or '-''s -- reflect the level of nesting in the directory 
structure of the computer's file system.

In this piece by default, the Vagrant software application will create a list of
the directories in the cookbook directory and run all of the default recipes
(which are encoded in the `default.rb` files). These recipes can perform a variety of 
useful, effective and efficient actions such as installing `ruby` or `node.js`
as well are configuring services such as `MySQL` or `HTTP`. Readers interested 
in writing recipes are encouraged to peruse this piece 
([Link](http://reiddraper.com/first-chef-recipe/ "First Chef Recipe")) at their
leisure. (*ed.- thx, dood*).

The directory on the user's file system in which the `Vagrantfile` resides is mounted
on the virtual machine. Changes that occur to files in that directory on the user's 
computer are immediately reflected in the mounted directory on the virtual machine
and *vice versa*. In the situation set up in Ex. 1, the directory on the user's 
filesystem would be mounted as `/opt/my-great-project` in the virtual machine.
This process is demonstrated in the example below.

    [Ex. 2]

    waffles:workbench pstein$ ls
    README.md Vagrantfile cookbooks
    waffles:workbench pstein$ touch hello-world
    waffles:workbench pstein$ ls
    README.md Vagrantfile cookbooks hello-world
    waffles:workbench pstein$ vagrant ssh
    Welcome to Ubuntu 12.04.1 LTS (GNU/Linux 3.2.0-32-generic x86_64)
    
     * Documentation:  https://help.ubuntu.com/
    
      System information as of Mon Dec 30 03:32:23 BRST 2013
    
      System load:  0.04              Processes:           81
      Usage of /:   12.8% of 7.87GB   Users logged in:     0
      Memory usage: 11%               IP address for eth0: 10.0.2.15
      Swap usage:   0%                IP address for eth1: 33.33.33.23
    
      Graph this data and manage this system at https://landscape.canonical.com/
    
    Last login: Mon Dec 30 03:31:38 2013 from 10.0.2.2
    vagrant@workbench:~$ cd /opt/workbench/
    vagrant@workbench:/opt/workbench$ ls
    cookbooks  hello-world  README.md  Vagrantfile
    vagrant@workbench:/opt/workbench$ sudo rm hello-world
    vagrant@workbench:/opt/workbench$ ls
    cookbooks  README.md  Vagrantfile
    vagrant@workbench:/opt/workbench$ exit
    logout
    Connection to 127.0.0.1 closed.
    waffles:workbench pstein$ ls
    README.md Vagrantfile cookbooks

In conclusion, it is this writer's opinion that provisioning is a powerful tool
which should be used by developers, engineers and normal folks alike! 

### Notes

1. Make sure that [the box](http://chickenandwaffl.es/boxes/ubuntu-12.04-x86_64.box)
stays online. If I move it, remember to update the reference in the `Vagrantfile`.
2. The IP address is set in the `Vagrantfile` (it's the `IP_ADDRESS` thing). If
you're going to spin up more than on `workbench` box, then you'll need to change
it based on the project. Maybe I can automate that by hashing the project
name? I'm not super sure that's necessary, but worth keeping in mind.

-PJ$, 12.29.2013
