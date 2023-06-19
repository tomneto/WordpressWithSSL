<img align=center height=50 src="https://seeklogo.com/images/W/wordpress-logo-9F351E1870-seeklogo.com.png"></img>


 ## In order to execute this project, you must install Docker, and generate a SSL certificate (following the readme at cert folder);

 #### After installing Docker, you can use the following commands to run your docker container:

    cd "/path/to/this root directory"

    docker-compose up -d

#### Wait for the docker container to be created, and click [here]('https://localhost/wp-admin') to your local Wordpress panel.

#### If you have problems while installing any WordPress plugin, please go to /wordpress/wp-config.php and add the following:

    define( 'FS_METHOD', 'direct' );

#### Place this line of code right after:

    define( 'WP_DEBUG', !!getenv_docker('WORDPRESS_DEBUG', '') );
