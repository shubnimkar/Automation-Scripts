# INSTALLATION OF GANGLIA ON MASTER

#INSTALL HTTPD IF NOT INSTALLED ALREADY

   >> yum install httpd

#THIS RPM ALLOWS USER TO INSTALL ANY OPENHPC COMPONENT SO IT'S GOOD TO HAVE IT INSTALLED

   >> yum install http://build.openhpc.community/OpenHPC:/1.3/CentOS_7/x86_64/ohpc-release-1.3-1.el7.x86_64.rpm

#INSTALL GANGLIA

   >> yum install ohpc-ganglia

#CONFIGURE gmetad.conf

   >> vim /etc/ganglia/gmetad.conf
   >> in line 44 "INSERT YOUR CLUSTER NAME" localhost

#START AND ENABLE gmetad,gmond,httpd

   >> systemctl start gmetad.service gmond.service httpd.service

   >> systemctl status gmetad.service gmond.service httpd.service

   >> systemctl enable gmetad.service gmond.service httpd.service

#CONFIGURE gmond.conf

   >> vi /etc/ganglia/gmond.conf 

   >> In line 30
   >> "name = "Cluster name"

   >> In line 50 
   >> mcast_join = "YOUR MASTER IP ADDRESS"

   >> Comment line 57 and line 59

#RESTART gmond.service

   >> systemctl restart gmond
   
   >> systemctl status gmond

#YOU CAN ALSO DO THIS TO COPY gmond.conf to client machines except for making changes again in gmond.config files

   >> scp -r /etc/ganglia/gmond.conf root@ip of client:/etc/ganglia/gmond.conf


# INSTALLATION OF GANGLIA ON CLIENTS

#THIS RPM ALLOWS USER TO INSTALL ANY OPENHPC COMPONENT SO IT'S GOOD TO HAVE IT INSTALLED

   >> yum install http://build.openhpc.community/OpenHPC:/1.3/CentOS_7/x86_64/ohpc-release-1.3-1.el7.x86_64.rpm

#INSTALL ganglia-gmond-ohpc

   >> yum install ganglia-gmond-ohpc

#CONFIGURE gmond.conf

   >> vi /etc/ganglia/gmond.conf 

   >> In line 30
   >> "name = "Cluster name"

   >> In line 50 
   >> mcast_join = "YOUR MASTER IP ADDRESS"

   >> Comment line 57 and line 59

#RESTART gmond.service
   >> systemctl restart gmond
    
   >> systemctl status gmond
