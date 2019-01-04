# Veridata Demo and Install and Configuration

This install was done as a build add-on to the OGG123RS_PDBS_HOLBASE VM image that was built to demo GoldenGate Microservices 12.1.3.  Note this is a rough guide - not all steps were captured in detail.

## **Demo Veridata**

- VM Login:  `oracle/welcome1`

- Start the database:  `/home/oracle/Desktop/Scripts/startup.sh`

- Start the Weblogic Admin Server: `/opt/app/oracle/product/fmw/user_projects/domains/base_domain/startWebLogic.sh (weblogic/admin when prompted)`  **DO NOT CLOSE THE WINDOW**

- Start the Weblogic Managed/Veridata Server: `/opt/app/oracle/product/fmw/user_projects/domains/base_domain/bin/startManagedWebLogic.sh VERIDATA_server1 http://127.0.0.1:7001 (weblogic/admin when prompted)` **DO NOT CLOSE THE WINDOW**

- Start the source pdb1 Veridata agent: `/home/oracle/agent/agent.sh start /home/oracle/agent/agent.properties`

- Start the target pdb2 Veridata agent: `/home/oracle/agent2/agent.sh start /home/oracle/agent2/agent.properties`

- Log into Weblogic Console:  `http://127.0.0.1:7001/console (weblogic/welcome1)`

![](images/veridata/008.png)

- Log into Veridata:  `http://127.0.0.1:8830/veridata (weblogic/welcome1)`.  See Step 10 below in the Install Steps for schema compare examples.

![](images/veridata/009.png)

## **Install and Configure Veridata**

### **Step 1 - Download HOLBASE VM**

Location of OGG123RS_PDBS_HOLBASE VM (in Retriever/ff-ftp.us.oracle.com, must be on VPN to download)

[OGG123RS_PDBS_HOLBASE](http://retriever.us.oracle.com/apex/f?p=121:22:7096965570613831::NO:RP:P22_CONTAINER_ID,P22_PREV_PAGE:81018,5425609)

### **Step 2 - Download Software**

- Here are the software versions.

![](images/veridata/001.png)

- log into [Edelivery](https://edelivery.oracle.com/osdc/faces/Home.jspx_) and download version 12.2.1.2.0 

![](images/veridata/002.png)

![](images/veridata/003.png)

![](images/veridata/004.png)

- Download [JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

![](images/veridata/005.png)

- Download [SQL Developer](https://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html) (not necessary, but convenient)

![](images/veridata/006.png)

### **Step 3 - Install JDK and SQL Developer**

- SCP the files to the running image and install JDK (`sudo rpm -ivh jdk-8u191-linux-x64.rpm`)

- Unzip SQL Developer

- Optional - add SQL Developer to the desktop menu.

- Start SQL Developer (`/home/oracle/sqldeveloper/sqldeveloper.sh`).  Enter the jdk path `/usr/java/jdk1.8.0_191-amd64`.

### **Step 4 - [Install Fusion Middleware](https://docs.oracle.com/middleware/12212/lcm/INFIN/GUID-943F11B4-DD9E-4631-8F5F-80B3ADC06F26.htm#INFIN125)**

- I installed into `/opt/app/oracle/product/fmw`, since the database and OGG was already install in /opt.

### **Step 5 - [Install Veridata](https://docs.oracle.com/goldengate/v12212/gg-veridata/GVDIS/title.htm)**

- Select oracle home `/opt/app/oracle/product/fmw`.

### **Step 6 - Create Repository with RCU**

- run `/opt/app/oracle/product/fmw/oracle_common/bin/rcu`

![](images/veridata/019.png)

![](images/veridata/020.png)

![](images/veridata/021.png)

![](images/veridata/022.png)

![](images/veridata/023.png)

![](images/veridata/024.png)

### **Step 6 - Create New Veridata Domain**

- run `/opt/app/oracle/product/fmw/oracle_common/common/bin/config.sh`

![](images/veridata/025.png)

![](images/veridata/026.png)

![](images/veridata/027.png)

![](images/veridata/028.png)

![](images/veridata/029.png)

![](images/veridata/030.png)

![](images/veridata/031.png)

![](images/veridata/032.png)

![](images/veridata/033.png)

![](images/veridata/034.png)

![](images/veridata/035.png)

![](images/veridata/036.png)

![](images/veridata/037.png)

![](images/veridata/038.png)

![](images/veridata/039.png)

![](images/veridata/040.png)

![](images/veridata/041.png)

![](images/veridata/042.png)

![](images/veridata/043.png)

![](images/veridata/044.png)

![](images/veridata/045.png)

### **Step 7 - Start WLS Admin and Veridata Servers and Login to the Console**

![](images/veridata/046.png)

![](images/veridata/047.png)

![](images/veridata/048.png)

![](images/veridata/049.png)

![](images/veridata/050.png)

### **Step 8 - Configure the Agent**

- First lets set the java home so we don't have to keep setting it in new terminal windows.

![](images/veridata/052.png)

- Now configure two agents - one for the source pdb1 database and the other for the target pdb2 database (replicating soe schema).

![](images/veridata/053.png)

![](images/veridata/055.png)

![](images/veridata/056.png)

![](images/veridata/057.1.png)

![](images/veridata/057.2.png)

![](images/veridata/057.3.png)

![](images/veridata/057.4.png)

### **Step 9 - Add Veridata priviledges to Weblogic user.**

![](images/veridata/058.png)

![](images/veridata/059.png)

![](images/veridata/060.png)

![](images/veridata/061.png)

![](images/veridata/062.png)

### **Step 10 - Log into Veridata and configure compare jobs.**

- [User Guide](https://docs.oracle.com/goldengate/v1221/gg-veridata/GVDUG/title.htm)

![](images/veridata/063.png)

![](images/veridata/064.png)

![](images/veridata/065.png)

![](images/veridata/066.png)

![](images/veridata/067.png)

![](images/veridata/068.png)

![](images/veridata/069.png)

![](images/veridata/070.png)

![](images/veridata/071.png)

![](images/veridata/072.png)

![](images/veridata/073.png)

![](images/veridata/074.png)

![](images/veridata/075.png)

![](images/veridata/076.png)

![](images/veridata/077.png)

![](images/veridata/078.png)

![](images/veridata/079.png)

![](images/veridata/080.png)

![](images/veridata/081.png)

![](images/veridata/082.png)

![](images/veridata/083.png)

![](images/veridata/083.png)

![](images/veridata/084.png)

![](images/veridata/085.png)

![](images/veridata/086.png)

![](images/veridata/087.png)

![](images/veridata/088.png)

![](images/veridata/089.png)

![](images/veridata/090.png)

![](images/veridata/091.png)

![](images/veridata/092.png)

![](images/veridata/093.png)

![](images/veridata/094.png)

![](images/veridata/095.png)

![](images/veridata/096.png)

![](images/veridata/097.png)

![](images/veridata/098.png)

![](images/veridata/099.png)

![](images/veridata/100.png)

![](images/veridata/101.png)

![](images/veridata/102.png)

![](images/veridata/103.png)

![](images/veridata/104.png)

![](images/veridata/105.png)

![](images/veridata/106.png)

![](images/veridata/107.png)

![](images/veridata/108.png)

![](images/veridata/109.png)

![](images/veridata/110.png)

