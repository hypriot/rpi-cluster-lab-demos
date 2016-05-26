Webserver with distributed Crate.io database
============================================

Overview
---------
This use case is about to use a distributed database over several nodes.  We will start a small webserver that communicates with crate.io databases that are fully synchronized over several nodes in the network.

**Note:** For now, all containers are started only on a single node! We have not yet found an easy way to bring crate.io and Docker networking together. You are very welcome to join us extending this!

Let's do it step by step
-----------------------------

- Login via SSH to an arbitrary node of your cluster.
- Checkout this repository:

  `git clone git@github.com:hypriot/rpi-cluster-demo.git`

- Change to proper folder:

  `cd voting_app_with_crate.io`

- Start a webserver with a voting app and crate.io as database:

  `docker-compose -f voting_app_with_crate.yml up -d`

- Point your browser to the node you executed the previous commands. To do so, execute `hostname` on the node, and put a `http://` in front. Thus, e.g. the URL is

  `http://hcl-master/`

You should see a simple website showing a welcome message, the hostname of the container and some buttons.

- Now, connect the webinterface of crate. To so, add `:4200/admin` in the URL of your browser. The URl should result in something like

  `http://hcl-master:4200/admin`

In the browser you should see a webinterface showing new number of nodes in the top menu bar. After some seconds, "2" nodes should be shown there.

- Spawn up several more crate databases with


`docker-compose -f voting_app_with_crate.yml scale crate-slave=X`

where `X` is the number of crate database you would like to start.

- If you then open reload your browser, the number of nodes should have increased by `X`!

- To test, if all crate nodes receive data, on the website (http://hcl-master/) press the button **create table**. This initializes an empty SQL table in the crate database. Then, press the buttons **dogs** and **cats** a few times.
If you then point your browser to the crate webinterface again, go to **console** in the menu on the left side, and exectute this query:

`select * from voting;`

Then you should see a list of how often you pressed the **dogs** or **cats** button.

- Now list all containers running on the node with

  `docker ps`

and see the **PORTS** column of your crate containers. Choose a port from one of them starting with `32`. Then replace this port in the URL pointing to the webinterface of crate. This is the webinterface of another  container.
Using the query statement above you should see the same results as on all other webinterfaces of crate. Thus, all crate nodes are fully synchronized.


Reset your environment
----------------------

Execute the following command in the folder in which the *.yml* files reside:
```
docker-compose -f loadbalancing-applications.yml kill && \
docker-compose -f loadbalancing-applications.yml rm -f
```
