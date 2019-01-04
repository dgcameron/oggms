# OGG123RS_PDBS_HOLBASE+

This image is a copy of the [OGG123RS_PDBS_HOLBASE](http://retriever.us.oracle.com/apex/f?p=121:22:7096965570613831::NO:RP:P22_CONTAINER_ID,P22_PREV_PAGE:81018,5425609) base image (focused on OGG 12.1.3 with Microservices) with the following added.  Note this can be used as both a demo image and a how to guide for installation and configuration.  It is not a polished HOL.  It also has SQL Developer installed and has many additional REST API examples. 
- Base Image:
    - OGG123RS_PDBS_HOLBASE+ (14.4G, must be on VPN): `ftp://soleng/soleng@ff-ftp.us.oracle.com/SOA/OPC/SALESKITS/Veridata`
    - Base image HOL:  [Oracle_GoldenGate_12c_HOL_Current.pdf](https://dgcameron.github.io/veridata/Oracle_GoldenGate_12c_HOL_Current.pdf).  Note that the software has been removed from the image to reduce the size.  If you wish to do lab 1 you will need to first copy the software to the image, and install into a different location from the existing install.  **Labs 1 - 5 have already been done for you.**
- SQLDeveloper installed with connections configured.
- Veridata installed and Configured
- Separate secure deployment with wallet configuration.
- Shell scripts:
    - Unprompted configuration and replication of existing Atlanta and SanFran deployments.
    - Drop Configuration
    - Script with prompts to clone a pdb and start replication.
- PL/SQL packages:
    - Create configuration and start replication using pl/sql (same as above that use curl statements, but with pl/sql).
    - Drop configuration with sql/plsql.

## **Getting Started**

- VM Login:  `oracle/welcome1`
- Start the database:  `/home/oracle/Desktop/Scripts/startup.sh`
- Access SQLDeveloper - Applications top left menu drop down - Other - SQLDeveloper
- GoldenGate Deployments:
    - Non-secure
        - Start services: `/opt/app/oracle/gg_deployments/ServiceManager/bin/startSM.sh` (disregard the message saying the service could not be started)
        - `/opt/app/oracle/gg_deployments/ServiceManager` (userid/pw oggadmin / welcome1)
        - `/opt/app/oracle/gg_deployments/Atlanta_1` (source)
            - Admin Server:         16001
            - Distribution Server:  16002
            - Receiver Server:      16003
            - Metrics Server:       16004
        - `/opt/app/oracle/gg_deployments/Sanfran_1` (target)
            - Admin Server:         17001
            - Distribution Server:  17002
            - Receiver Server:      17003
            - Metrics Server:       17004
    - Secure
        - Start services:  `/opt/app/oracle/gg_deployments/ServiceManagerSecure/bin/startSM.sh` (disregard the message saying the service could not be started)
        - `/opt/app/oracle/gg_deployments/ServiceManagerSecure` (userid/pw oggadmin / welcome1)
        - `/opt/app/oracle/gg_deployments/Seattle_1` (source)
            - Admin Server:         18001
            - Distribution Server:  18002
            - Receiver Server:      18003
            - Metrics Server:       18004
        - `/opt/app/oracle/gg_deployments/Vancouver_1` (target)
            - Admin Server:         18011
            - Distribution Server:  18012
            - Receiver Server:      18013
            - Metrics Server:       18014
- Stop all services: `sudo pkill -f Service` (note the stop script does not seem to work).
- Scripts:
    - Database `/home/oracle/Desktop/Scripts`
    - Swingbench: `/opt/app/oracle/product/swingbench/bin/oewizard`
    - Docker/Elk Monitoring: `/home/oracle/Desktop/ogg_elk_monitoring`
    - bash and plsql orchestration: `/home/oracle/Desktop/api`
    - GG Client: `/opt/app/oracle/product/18.1/oggcore_1/bin/adminclient`
    - Other: `/home/oracle/Desktop/misc1` (wallet configuration, gg client examples)

## **Resetting the state of the image and data**

Most of the labs use rman to clear the logs before starting, but it is sometimes useful to re-set the data.  Ideally you would restore to an image snapshot to ensure a complete image re-set.  However you can also use the following for data and/or config:

- SOE Schema.  Be sure to run this twice - once for the source pdb and again for the target pdb:
```
sqlplus soe/welcome1@pdb2
alter table orders disable constraint ORDERS_CUSTOMER_ID_FK;
alter table order_items disable constraint order_items_product_id_fk;
alter table inventories disable constraint inventories_warehouses_fk;
alter table inventories disable constraint inventories_product_id_fk;
alter table order_items disable constraint order_items_order_id_fk;
alter table order_items disable constraint order_items_product_id_fk;
alter table addresses disable constraint add_cust_fk;
truncate table order_items;
truncate table inventories;
truncate table orderentry_metadata;
truncate table card_details;
truncate table addresses;
truncate table logon;
truncate table product_descriptions;
truncate table orders;
truncate table warehouses;
truncate table product_information;
truncate table customers;
```

- Remove default gg configuration.
    - `/home/oracle/Desktop/api/drop_bash_config.sh`