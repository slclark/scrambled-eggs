This boilerplate is for converting websites into a repository that keeps WP files out of version control. Download this repo and move original `wp-content` into `html`, and follow the instructions below.

# Instructions for converting old sites to the new boilerplate:

1.) copy over the original `wp-content` into the `/html` directory

2.) copy over the original files listed below into `/html`

`.htaccess`

`wp-config.php`

### Changes to wp-config.php

3.) Add to the `wp-config.php` file the below code snippet:

```
// define where wp-content is since wordpress is in its own directory not committed through version control
define('WP_CONTENT_DIR', __DIR__ . '/wp-content');

define('WP_DEFAULT_THEME', 'taco-theme');
```

(be sure to replace the WP_DEFAULT_THEME with matching theme named directory)

4.) Make sure to also add to the `wp-config.php` the below code snippet, to enable minor updates on production:

```
define('WP_AUTO_UPDATE_CORE', 'minor');
```


5.) Don't let warnings show up on production server (this should already be enabled but services like MT don't suppress errors on their servers). All our sites should have an ENVIRONMENT variable so just make sure of this.

```
/** no errors on production **/
if(ENVIRONMENT === ENVIRONMENT_PROD) {
  error_reporting(0);
  @ini_set('display_errors', 0);
}
```

### Changes to functions.php

1.) To enable WP auto-updates locally, you need to add a filter to the project to ensure the .git files don't keep WP from updating.

```
function allow_local_autoupdates( $checkout, $context) {
  return false;
}

add_filter( 'automatic_updates_is_vcs_checkout', 'allow_local_autoupdates', 10, 2);
```


### Changes to `.htaccess`

5.) Update the `.htaccess` inside `/html` to include the below WP stable code snippet to include the `/wordpress/` path as seen below:

```
# BEGIN WordPress
<IfModule mod_rewrite.c>
  # Rewrite all files that aren't already in the wordpress directory to that subdirectory
  # Serve up a file directly if it actually does exist
  RewriteEngine on
  RewriteCond %{REQUEST_URI} !^/wordpress/
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule ^(.*)$ /wordpress/$1

  RewriteRule ^(/)?$ wordpress/index.php [L]
</IfModule>
# END WordPress
```

5.) cd into `/html` and run `composer install`. When finished running, delete `wp-content` in the `wordpress` directory.

> OPTIONAL - Instead of running `composer install` in the `/html` directory, copy `composer.json` up one directory and update any references to `vendor` to point to this directory instead.  The main one to update is the autoload script in `wp-config.php`.  This will only work on sites that we are deploying the entire directory structure and if there's not any crazy relative directory linking in the project, but it is a little cleaner to not expose the `vendor` directory publicly.

6.) Deal with Taco and installing it so it works for the website (this is a case-by-case scenario depending on how the original boilerplate was setup. Lookout for symlinks in the plugins if it's an original copeland boilerplate.)

7.) For the staging environment, add the below code snippet to the .htaccess file that is created one level above html, which includes the .htpasswd stuff:

```
# Require password for staging
AuthName "Restricted Area"
AuthType Basic
AuthUserFile /var/www/vhosts/boulder-shelter/.htpasswd
AuthGroupFile /
Require valid-user

Satisfy Any
Order deny,allow
Deny from all
Allow from env=!PROTECTED_ENV

<FilesMatch "^wp-cron\.php$">
  Allow from all
</FilesMatch>

```

8.) Don't forget to test the robots.txt file as some sites use a redirect to point to a template in the theme, so check that the template file paths are working correctly. Another thing to do is to remove `Disallow: /wp-includes/` because it's no longer best practices and certain .js files get loaded onto all pages that are located in this directory. Based on this yoast article from Feb. 2017, https://yoast.com/wordpress-robots-txt-example/

This is to enable auto-updates on staging by allowing the wp-cron.php to run even when the site is password protected.






