<img align=center height=50 src="https://seeklogo.com/images/W/wordpress-logo-9F351E1870-seeklogo.com.png"></img>


 ## In order to execute this project, you must install Docker, and generate a SSL certificate (following the readme at cert folder);

 ## After you got the certificate files at the cert folder, you can run the https-deploy.sh to assign a self-signed certificate to the nginx host. Maybe some password are required for the SSL certificate. Follow the instructions provided when running https-deploy.sh. Letsencrypt is not available when running with self-signed certificates. But to run with external certificates, you might use letsencrypt rather than the https-deploy.sh. If you have a CA signed certificate, you can skip the https-deploy.sh execution. And place the CA certificate at the cert folder. 

 #### After installing Docker and placing the cert at the right folder, now you can use the following commands to run your docker container:

    cd "/path/to/this root directory"

    docker-compose up -d

#### Wait for the docker container to be created, and click ["here"]('https://localhost/wp-admin') to access your local Wordpress panel.

#### If you have problems while installing any WordPress plugin, please go to /wordpress/wp-config.php and add the following:

    define( 'FS_METHOD', 'direct' );

#### Place this line of code right after the comment:

    /* Add any custom values between this line and the "stop editing" line. */