# Configure a Secure Deployment with Wallets

As noted in the readme document, labs 1 - 5 in the [Oracle_GoldenGate_12c_HOL_Current.pdf](https://dgcameron.github.io/veridata/Oracle_GoldenGate_12c_HOL_Current.pdf) from the base image have already been done for you in the .  You may wish to walk through the creation of deployments in that document.  Note that all the GG product path references (eg: /opt/app/oracle/product/12.3.0.1...) will be /opt/app/oracle/product/18.1... to reflect an upgrade to 18.1.

## **Configuration Steps:**

- Start the database.  Open a terminal window and enter:
    - `/home/oracle/Desktop/Scripts/startup.sh`

- Follow the steps documentated in the file `/home/oracle/Desktop/misc/wallet_setup` on the image desktop.  The steps are also copied below.

- Start services: `/opt/app/oracle/gg_deployments/ServiceManagerSecure/bin/startSM.sh` (disregard the message saying the service could not be started)

- Start Firefox (top manu) and log in using the shortcut.

![](images/Secure/001.png)

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

- Run through the following labs in the HOL document noted above.
    - Lab 6 - Configure Swingbench Schemas
    - 7a - Configure Uni-Directional Replication Integrated Extract)
    - 7b - Configure Uni-Directional Replication Distribution Server)
    - 7c - Configure Uni-Directional Replication Receiver Server)
    - 7d - Configure Uni-Directional Replication Integrated Replicat)

## **Wallet Setup**

### **Setup the DB and environment variables**

- run rman and clear logs
```
rman
connect target /
delete noprompt archivelog all;
exit;
```

- add the following to ~.bashrc (this has already been done for you).
```
export OGG_HOME=/opt/app/oracle/product/12.3.0.1/oggcore_1
export OGG_BASE_DIR=/opt/app/oracle/product/12.3.0.1
export OGG_WALLET=/opt/app/oracle/product/wallet
export OGG_DEPLOYMENT=/opt/app/oracle/gg_deployments
```

### **Create the Wallet Directory**

- Create the wallet directory
    - `mkdir /opt/app/oracle/product/wallet`

- Invoke orapki to create an automatic login wallet:
-    `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet create -wallet $OGG_WALLET/Root_CA -auto_login -pwd welcome1`

- Create the root certificate:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/Root_CA -dn 'CN=dcameron,OU=NAS,O=Oracle America,L=Redwood Shores,ST=CA,C=US' -keysize 2048 -self_signed -validity 15000 -pwd welcome1`

- Use the orapki wallet display command to display the certificate requests, user certificates, and trusted certificates contained in the wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet display -wallet $OGG_WALLET/Root_CA -pwd welcome1`

- Export the root certificate to a pem file. This file will be used to sign the Server and Distribution Server certificates used in our secure OGG-MA replication environment:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet export -wallet $OGG_WALLET/Root_CA -dn 'CN=dcameron,OU=NAS,O=Oracle America,L=Redwood Shores,ST=CA,C=US' -cert $OGG_WALLET/root_ca.pem -pwd welcome1`

### **Create server certificates**

- Invoke orapki to create the server wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet create -wallet $OGG_WALLET/ogg123rs -auto_login -pwd welcome1`

- Add a certificate request to the newly created wallet that will identify the server:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/ogg123rs -dn 'CN=ogg123rs,L=Redwood Shores,ST=CA,C=US' -keysize 2048 -pwd welcome1`

- Export the request:
- `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet export -wallet $OGG_WALLET/ogg123rs -dn 'CN=ogg123rs,L=Redwood Shores,ST=CA,C=US' -request $OGG_WALLET/ogg123rs_req.pem`

- Create a signed certificate from the request. Be sure to assign an unique serial number to each certificate:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki cert create -wallet $OGG_WALLET/Root_CA -request $OGG_WALLET/ogg123rs_req.pem -cert $OGG_WALLET/ogg123rs_cert.pem -serial_num 20 -validity 15000`

- View the certificate:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki cert display -cert $OGG_WALLET/ogg123rs_cert.pem -complete`

```
Oracle PKI Tool : Version 12.2.0.1.0
Copyright (c) 2004, 2016, Oracle and/or its affiliates. All rights reserved.

{ fingerprint = c76cc3e83a9e00a28338e4d606e3dd27, notBefore = Thu Nov 29 19:55:08 EST 2018, notAfter = Wed Dec 24 19:55:08 EST 2059, holder = CN=ogg123rs,L=Redwood Shores,ST=CA,C=US, issuer = CN=dcameron,OU=NAS,O=Oracle America,L=Redwood Shores,ST=CA,C=US, serialNo = 20, sigAlgOID = 1.2.840.113549.1.1.11, key = { modulus = 16696955521607816437535518720499191712708462336097144752551501657668133653470955100930417509848549918970283621441859388643075101511413386114229297349730161609745995815642227267179713843349453930568769480088139234722084997834891743471868500869211816197001048603917901747559837185186304674678247397909367332426687074197409097688927329920649698211138357190899052484453388243451425042268897160926386538470609397472471123490079888239211044137793156730046549626318728175575750938365375968895593569247969026724048114173800673440541775275366405930929715671666131560018522994559652382797073954143913734315708851962885149240689, exponent = 65537 } }
```

- Add the trusted certificate to the server wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/ogg123rs -trusted_cert -cert $OGG_WALLET/root_ca.pem -pwd welcome1`

- Add the server certificate to the wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/ogg123rs -user_cert -cert $OGG_WALLET/ogg123rs_cert.pem -pwd welcome1`

- To view the user and trusted certificates in the wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet display -wallet $OGG_WALLET/ogg123rs`

```
Oracle PKI Tool : Version 12.2.0.1.0
Copyright (c) 2004, 2016, Oracle and/or its affiliates. All rights reserved.

Requested Certificates: 
User Certificates:
Subject:        CN=ogg123rs,L=Redwood Shores,ST=CA,C=US
Trusted Certificates: 
Subject:        CN=dcameron,OU=NAS,O=Oracle America,L=Redwood Shores,ST=CA,C=US
```

- After creating wallets and certificates for each server in my trusted OGG-MA environment, the wallet directory contained the following files and directories:
    - `ls -l`

```
total 12
drwx------. 2 oracle oracle   86 Nov 29 19:50 ogg123rs
-rw-------. 1 oracle oracle 1154 Nov 29 19:55 ogg123rs_cert.pem
-rw-------. 1 oracle oracle  967 Nov 29 19:54 ogg123rs_req.pem
drwx------. 2 oracle oracle   86 Nov 29 19:01 Root_CA
-rw-------. 1 oracle oracle 1232 Nov 29 19:10 root_ca.pem
```

- We can clean things up by removing the server certificate request and certificate files. Be sure to preserve the root certificate (root_ca.pem) so additional server certificates may be created:
```
rm /opt/app/oracle/product/wallet/ogg123rs_req.pem 
rm /opt/app/oracle/product/wallet/ogg123rs_cert.pem
```

### **Create distribution serve certificats**

- Create the Distribution Server wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet create -wallet $OGG_WALLET/oggmadistsrvr -auto_login -pwd welcome1`

- Add a certificate request to the newly created wallet that will identify the Distribution Server:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/oggmadistsrvr -dn 'CN=distclient,L=Redwood Shores,ST=CA,C=US' -keysize 2048 -pwd welcome1`

- Export the request:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet export -wallet $OGG_WALLET/oggmadistsrvr -dn 'CN=distclient,L=Redwood Shores,ST=CA,C=US' -request $OGG_WALLET/distclient_req.pem`

- Create a signed certificate from the request. Be sure to assign an unique serial number to each certificate:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki cert create -wallet $OGG_WALLET/Root_CA -request $OGG_WALLET/distclient_req.pem -cert $OGG_WALLET/distclient_cert.pem -serial_num 20 -validity 15000`

- Add the trusted certificate to the wallet:
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/oggmadistsrvr -trusted_cert -cert $OGG_WALLET/root_ca.pem -pwd welcome1`
    - `/opt/app/oracle/product/18.1/oggcore_1/bin/orapki wallet add -wallet $OGG_WALLET/oggmadistsrvr -user_cert -cert $OGG_WALLET/distclient_cert.pem -pwd welcome1`

