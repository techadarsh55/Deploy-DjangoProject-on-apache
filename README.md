# Deploying a django project on apache
deploying the django project on linux apache server using following steps 

step 1 : Copy the project & add into var/www/html/yourprojectfolder

step 2 : after that you need to create on virtual enviroment in /opt folder

step 3 : active the virtual env and goto you project folder & run the project check the all functionality working or not. if it is working fine then you go to next step

step 4 : you need to create global static folder in my case i'm working on django therefore, i run this command on terminal python manage.py collectstatic and type yes or they don't ask directly create the static folder

step 5 : Now your project ready to publish on apache, check the httpd status

# systemctl status httpd 
if httpd status enable, once restart
# systemctl restart httpd

step 6 : now goto the /etc/httpd/conf.d. In conf.d folder you saw the django.conf file open this write this lines 

#-------------------------------------------------------

      <VirtualHost *:8001>
        
        ServerName 192.168.0.168
        DocumentRoot /var/www/

        Alias /static /var/www/html/GoAirFirst/static 
        <Directory /var/www/html/GoAirFirst/static>  
           Options FollowSymLinks
                Order allow,deny
                Allow from all
                Require all granted
        </Directory> 
        <Directory /var/www/html/GoAirFirst/GoAirFirst>    
          <Files wsgi.py>        
            Require all granted    
          </Files> 
        </Directory> 
        
        WSGIDaemonProcess GoAirFirst python-home=/opt/vivr python-path=/var/www/html/GoAirFirst:/opt/vivr/lib/python3.10/site-packages
        WSGIProcessGroup GoAirFirst
        WSGIScriptAlias / /var/www/html/GoAirFirst/GoAirFirst/wsgi.py
	
        <Directory var/www/html/GoAirFirst/GoAirFirst>
                <Files wsgi.py>
                        Require all granted
                </Files>
        </Directory>
        </virtualHost>

#--------------------------------------------------------
# Note : Don't forget the replace the place with your project

step 7 : now open httpd.conf active the port which you want listen globally in my case i used the listen 8001

step 8 : now restart the httpd and check on localhost:8001 & you can see your project is now live.
  
    systemctl restart httpd

step 9 : if you facing the database problem then you can used pymysql probably mysqlclient is not working on linux enviroment and don't forgot to add this line in init.py file folder main project

    import pymysql
	  pymysql.install_as_MySQLdb()

step 10 : now your database problem moreover solved you can connect to database.


# Important Note *
if your create the folder in your project using code then you need to give the permission on project folder for apache folder
    
    chcon -R -t httpd_sys_rw_content_t /var/www/html/projectfolder/projectfolder/media
    
    chown apache:apache /var/www/html/projectfolder/projectfolder/media
    
    chmod 755 /var/www/html/projectfolder/projectfolder/media
    
    chown -R apache:apache /var/www/html/projectfolder/projectfolder/media
