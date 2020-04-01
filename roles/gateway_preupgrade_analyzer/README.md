Pre-Upgrade-Upgrader
======================

This role will inform which customizations on the Gateway will be migrated automatically, and which ones customer will need to migrate themself.

Requirements
------------
Target Gateway is started.
Provide Gateway Database admin user and password, gateway user and password.
Have full access both Gateway (ssg) database. 
MySql Shell Checker is installed on the control node

Role Variables
--------------
access both Gateway (ssg) and OTK database using mysql user gateway. 

Dependencies
------------
There are no dependencies upon other roles.


Author Information
------------------
