# DISABLE SELINUX AND FIREWALL

>> FOR SELINUX 
setenforce 0
getenforce

>> FOR FIREWALL
systemctl status firewalld
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld

# CHECK HOSTNAME AND SET IT AS PER YOUR WISH

>> hostname
>> hostnamectl set-hostname ldap1.cdac.in


#INSTALL OPENLDAP SERVICES

>> yum install -y openldap-servers openldap-clients
>> cp /usr/share/openldap-servers/DB_CONFIG.example  /var/lib/ldap/DB_CONFIG

#CHANGE OWNERSHIP
>> chown ldap. /var/lib/ldap/DB_CONFIG 

#START SLAPD

>> systemctl start slapd
>> systemctl enable slapd
>> systemctl status slapd


#TO SET OpenLDAP ADMIN Password

>> slappasswd
New password: 
Re-enter new password: 

SUCH KEY WILL GENERATE COPY FOR NEXT USE HERE I'VE USED PASSWORD AS "ROOT"
{SSHA}Xw4kWMRG1Su3XeqSF/bMah5RCgDH54o+


#SET NEW PASSWORD TO OLCROOTPW FILE

>> vi chrootpw.ldif

AND ADD THIS IN THE FILE 

                    dn: olcDatabase={0}config,cn=config
                    changetype: modify
                    add: olcRootPW
                    olcRootPW: {SSHA}Xw4kWMRG1Su3XeqSF/bMah5RCgDH54o+



#WE ARE ADDING CHROOT FILE TO LDAP DATABASE

>> ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif

                    SASL/EXTERNAL authentication started
                    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
                    SASL SSF: 0
                    modifying entry "olcDatabase={0}config,cn=config"



#IMPORT BASIC SCHEMA which are already present and we are importing in our ldap

>> ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif 

                    SASL/EXTERNAL authentication started
                    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
                    SASL SSF: 0
                    adding new entry "cn=cosine,cn=schema,cn=config"


>> ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 

                    SASL/EXTERNAL authentication started
                    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
                    SASL SSF: 0
                    adding new entry "cn=nis,cn=schema,cn=config"


>> ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif 

                    SASL/EXTERNAL authentication started
                    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
                    SASL SSF: 0
                    adding new entry "cn=inetorgperson,cn=schema,cn=config"


#SET DOMAIN NAME ON LDAP DB.

>> vi chdomain.ldif

# replace to your own domain name for "dc=***,dc=***" section
# specify the password generated above for "olcRootPW" section

                    dn: olcDatabase={1}monitor,cn=config
                    changetype: modify
                    replace: olcAccess
                    olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
                      read by dn.base="cn=Manager,dc=cdac,dc=in" read by * none

                    dn: olcDatabase={2}hdb,cn=config
                    changetype: modify
                    replace: olcSuffix
                    olcSuffix: dc=cdac,dc=in

                    dn: olcDatabase={2}hdb,cn=config
                    changetype: modify
                    replace: olcRootDN
                    olcRootDN: cn=Manager,dc=cdac,dc=in

                    dn: olcDatabase={2}hdb,cn=config
                    changetype: modify
                    add: olcRootPW
                    olcRootPW: {SSHA}Xw4kWMRG1Su3XeqSF/bMah5RCgDH54o+

                    dn: olcDatabase={2}hdb,cn=config
                    changetype: modify
                    add: olcAccess
                    olcAccess: {0}to attrs=userPassword,shadowLastChange by
                      dn="cn=Manager,dc=cdac,dc=in" write by anonymous auth by self write by * none
                    olcAccess: {1}to dn.base="" by * read
                    olcAccess: {2}to * by dn="cn=Manager,dc=cdac,dc=in" write by * read


>> ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif

                    SASL/EXTERNAL authentication started
                    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
                    SASL SSF: 0
                    modifying entry "olcDatabase={1}monitor,cn=config"

                    modifying entry "olcDatabase={2}hdb,cn=config"

                    modifying entry "olcDatabase={2}hdb,cn=config"

                    modifying entry "olcDatabase={2}hdb,cn=config"

                    modifying entry "olcDatabase={2}hdb,cn=config"


>> vi basedomain.ldif

                    dn: dc=cdac,dc=in
                    objectClass: top
                    objectClass: dcObject
                    objectclass: organization
                    o: cdac in
                    dc: cdac

                    dn: cn=Manager,dc=cdac,dc=in
                    objectClass: organizationalRole
                    cn: Manager
                    description: Directory Manager

                    dn: ou=People,dc=cdac,dc=in
                    objectClass: organizationalUnit
                    ou: People

                    dn: ou=Group,dc=cdac,dc=in
                    objectClass: organizationalUnit
                    ou: Group


>> ldapadd -x -D cn=Manager,dc=cdac,dc=in -W -f basedomain.ldif 

                    Enter LDAP Password: 
                    adding new entry "dc=cdac,dc=in"

                    adding new entry "cn=Manager,dc=cdac,dc=in"

                    adding new entry "ou=People,dc=cdac,dc=in"

                    adding new entry "ou=Group,dc=cdac,dc=in"


# ADDING USERS 
>> vim ldapuser.ldif

                    dn: uid=user1,ou=People,dc=cdac,dc=in
                    objectClass: inetOrgPerson
                    objectClass: posixAccount
                    objectClass: shadowAccount
                    cn: user1
                    sn: test
                    userPassword: {SSHA}Xw4kWMRG1Su3XeqSF/bMah5RCgDH54o+
                    loginShell: /bin/bash
                    uidNumber: 1001
                    gidNumber: 1001
                    homeDirectory: /home/user1

                    dn: cn=user1,ou=Group,dc=cdac,dc=in
                    objectClass: posixGroup
                    cn: user1
                    gidNumber: 1001
                    
                    
>> ldapadd -x -D cn=Manager,dc=cdac,dc=in -W -f ldapuser.ldif

                    Enter LDAP Password: 
                    adding new entry "uid=user1,ou=People,dc=cdac,dc=in"

                    adding new entry "cn=user1,ou=Group,dc=cdac,dc=in"




##################################################################################################################################
ON CLIENT MACHINE

#INSTALL OPENLDAP SERVICES ON CLIENT

>> yum -y install openldap-clients nss-pam-ldapd

>> authconfig --enableldap --enableldapauth --ldapserver=192.168.100.134 --ldapbasedn="dc=cdac,dc=in" --enablemkhomedir --update

      authconfig --enableldap --enableldapauth --ldapserver=*YOUR IP ADDRESS* --ldapbasedn="dc=*YOUR DC*,dc=*YOUR* " --enablemkhomedir --update
      
      
>> systemctl restart nslcd
>>ldapsearch -x


>>vim /etc/nsswitch.conf
    insert ldap infront 
    (order-->files--> sss-->ldap)

>> systemctl restart nslcd

>> getent passwd


##################################################################################################################################
IF YOU SEE PASSWORD IS DENIED COMPARE WITH THE FILE BELOW AND CHANGE PARAMETERS

>> vim /etc/pam.d/password-auth-ac

                #%PAM-1.0
                # This file is auto-generated.
                # User changes will be destroyed the next time authconfig is run.
                auth        required      pam_env.so
                auth        required      pam_faildelay.so delay=2000000
                auth        [default=1 ignore=ignore success=ok] pam_succeed_if.so uid >= 1000 quiet
                auth        [default=1 ignore=ignore success=ok] pam_localuser.so
                auth        sufficient    pam_unix.so nullok try_first_pass
                auth        requisite     pam_succeed_if.so uid >= 1000 quiet_success
                auth        sufficient    pam_sss.so forward_pass
                auth        required      pam_deny.so

                account     required      pam_unix.so broken_shadow
                account     sufficient    pam_localuser.so
                account     sufficient    pam_succeed_if.so uid < 1000 quiet
                account     [default=bad success=ok user_unknown=ignore] pam_sss.so
                account     required      pam_permit.so

                password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
                password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
                password    sufficient    pam_sss.so use_authtok


                password    required      pam_deny.so

                session     optional      pam_keyinit.so revoke
                session     required      pam_limits.so
                -session     optional      pam_systemd.so
                session     optional      pam_oddjob_mkhomedir.so umask=0077
                session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
                session     required      pam_unix.so
                session     optional      pam_sss.so
##################################################################################################################################

*******NOW STEPS TO INSTALL ""PHPLDAPADMIN""********

# INSTALL PHP LDAP ADMIN

>> yum install epel-release
>> yum --enablerepo=epel -y install phpldapadmin

# EDIT IN Config.php

>> vi /etc/phpldapadmin/config.php

              # line 397: uncomment, line 398: comment out
              $servers->setValue('login','attr','dn');
              // $servers->setValue('login','attr','uid');

>> vi /etc/httpd/conf.d/phpldapadmin.conf

COMPARE AND MAKE NECESSARY CHANGES
   
   #
#  Web-based tool for managing LDAP servers
#

Alias /phpldapadmin /usr/share/phpldapadmin/htdocs
Alias /ldapadmin /usr/share/phpldapadmin/htdocs

<Directory /usr/share/phpldapadmin/htdocs>
  <IfModule mod_authz_core.c>
    # Apache 2.4
    Require local
    Require ip 10.0.0.0/24
</IfModule>
  <IfModule !mod_authz_core.c>
    # Apache 2.2
    Order Deny,Allow
    Deny from all
    Allow from 127.0.0.1
    Allow from ::1
  </IfModule>
</Directory>
   
>> systemctl restart httpd

##################################################################################################################################

Access to the "http://(server's hostname or IP address)/ldapadmin/" from a client which is in the network allowed by http server and then Click "login".

![LDAP - VMware Workstation 14-06-2023 12_29_03](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/91cec342-0daf-49a6-bc90-c8cd3c94fb4d)

Just logined. It's possible to manage LDAP server on here.

![LDAP - VMware Workstation 14-06-2023 12_40_10](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/48a6602a-e76a-4ae6-8db2-8e0dab9a88de)

This is what homepage looks like.

![LDAP - VMware Workstation 14-06-2023 12_41_09](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/0b7bd1a5-3d60-47a7-acd1-4622d3169bb3)

You can see on left hand side the OU,GROUPS AND USERS which youv've created

![LDAP - VMware Workstation 14-06-2023 12_44_22](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/ffa1588a-a84b-4e71-898c-12ba433b46ed)

You can add yourself too like done here
1. lets add in "dc" look for highlighted section

![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/03a091bd-d016-4053-80ce-fed3ae488641)

   then click on create child entry

![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/e142955c-0bc8-478c-b96d-5f579d374f74)

   see for highlighted field

![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/96b2c47f-5c4b-4cb2-9da0-8da8f0836939)
![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/34f7e39f-e473-4362-8bf5-259c75842e67)

  add the required fields as per your needs and click on create object.

![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/3971c002-3b13-480f-8716-0b20f180a8b3)

check for entries and commit.

![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/84ba35ca-42cf-43c8-bb2c-322e6bbfa1fd)
NOW you can see in left panel Systemadmin 

2. To add user
 perform adding like before and you'll see panel like this and you can add.
 
![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/784fc33b-0877-4a8b-b948-7c084b42e148)

![image](https://github.com/shubnimkar/Automation-Scripts/assets/46809421/ab89cffe-d249-4ce0-859e-00d49e583399)

and Done!!!

Hence you can manage as per your needs.

