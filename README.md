This boilerplate is for converting websites into a repository that keeps WP files out of version control.

# Instructions for converting old sites to the new boilerplate:

1. copy/paste the old `wp-content` into /html directory
2. copy over the below files into /html

`.htaccess`
`wp-config.php`

3. Add to the wp-config.php file the below code snippet:

`// wp core files specifications to keep wp core files out of the repo
define('WP_CONTENT_DIR', __DIR__ . '/wp-content');
define('WP_CONTENT_URL', 'http://' . $_SERVER['SERVER_NAME'] . '/wp-content');
define('WP_SITEURL', 'http://' . $_SERVER['SERVER_NAME'] . '/wp');
define('WP_HOME', 'http://' . $_SERVER['SERVER_NAME']);
define('WP_DEFAULT_THEME', 'taco-theme');`

(be sure to replace the WP_DEFAULT_THEME with matching theme named directory)

4. Make sure to also add to the `wp-config.php` the below code snippet:

`// Enable auto security updates
define('WP_AUTO_UPDATE_CORE', 'minor');`

5. cd into `/html` and run composer install. When finished running, delete `wp-content` in the `wp` directory.

6. Deal with Taco and installing it so it works for the website (this is a case-by-case scenario depending on how the original boilerplate was setup)

7. For the staging environment, add the below code snippet to the .htaccess file that is created one level above html, which includes the .httpasswd stuff:

`<FilesMatch "wp-cron\.php$">
    Satisfy Any
    Order allow,deny
    Allow from all
</FilesMatch>`

This is to enable auto-updates on staging by allowing the wp-cron.php to run even when the site is password protected.

8. For production environement, it's recommended to setup a subdomain and add the new repo/boilerplate there first, not deploying new repo to existing production directory. After testing, switch the name of the directory to production for a smooth transition and deployment.




