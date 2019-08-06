# Mosaico PHP Backend

This is a PHP backend for Mosaico

Mosaico can be found at https://github.com/voidlabs/mosaico

First, install and set up Mosaico.  Then install these files on top of the Mosaico installation.

## Dependencies

It is expected that you are running Apache with mod_rewrite support enabled.

You also do need to have Imagemagick support enabled in your PHP configuration.

This project also requires Premailer (http://premailer.dialect.ca/).  Premailer is used to inline the CSS styles.  If that service is ever taken down, we will have to find an alternate solution.  Or, if you have an alternate solution that does not require dependencies on a web service, feel free to contribute!

## New folders and files
```
backend-php/config.php
```
In this file are a few variables that you can adjust if necessary.  Please check this file and make sure all the paths are correct for your Mosaico installation, and that PHP can write files to those paths.  If they are wrong or PHP cannot write files to those paths, your image uploads *will not* work.

```
/backend-php/index.php
```
This is the PHP backend engine that handles the required functions:
* image uploads
* retrieving of a list of uploaded images
* downloading of the HTML email
* sending of the test email
* generating the placeholder images
* the resizing of images

The PHP backend also generates static resized images when downloading the HTML email or sending the test email.

```
/backend-php/premailer.php
```
This premailer.php file came from here: https://gist.github.com/barock19/1591053

## Modified files

```
editor.html
```
This example file has been slightly modified to work with the php backend. You may possibly need to configure this file as well.

## Install Instructions for CENTOS
```
# Get Root
sudo su -
# Install dev tools 
yum groupinstall -y 'Development Tools'
# Install NodeJS + ImageMagick + php5.4 + httpd
curl --silent --location https://rpm.nodesource.com/setup_7.x | bash -
yum install -y nodejs ImageMagick ImageMagick-devel php-devel php-pear httpd php
# php5.4 imagemagick integration
echo | pecl install imagick
echo "extension=imagick.so" > /etc/php.d/imagick.ini

# Install mosaico NodeJS Part
mkdir -p /srv
cd /srv
git clone https://github.com/direktspeed/mosaico
cd mosaico
npm install grunt@^1.0.0
npm install -g grunt-cli
npm install
rm -rf /var/www/html
grunt build && ln -s $PWD /var/www/html
# cd /var/www/html
curl -L https://raw.githubusercontent.com/direktspeed/mosaico-php-backend/master/backend-php/config.php > config.php
curl -L https://raw.githubusercontent.com/direktspeed/mosaico-php-backend/master/backend-php/index.php > index.php
curl -L https://raw.githubusercontent.com/direktspeed/mosaico-php-backend/master/backend-php/premailer.php > premailer.php
curl -L https://raw.githubusercontent.com/direktspeed/mosaico-php-backend/master/backend-php/.htaccess > .htaccess
mkdir -p uploads/thumbnails
chown apache:apache /srv/mosaico
chown -R apache:apache /srv/mosaico
chown apache:apache /var/www
#allow overide check if enabled should be     AllowOverride All
sed -i 's/AllowOverride None/AllowOverride All/g' /etc/httpd/conf/httpd.conf
sed -i 's/AllowOverride none/AllowOverride All/g' /etc/httpd/conf/httpd.conf
sed -i 's/enforcing/Permissive/g' /etc/sysconfig/selinux
setenforce Permissive
firewall-cmd --permanent --zone=public --add-port=80/tcp 
firewall-cmd --permanent --zone=public --add-port=443/tcp
firewall-cmd --reload
systemctl enable httpd && systemctl start httpd
echo "ALL DONE"
```
