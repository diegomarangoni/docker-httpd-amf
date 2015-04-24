# What is Apache Mobile Filter (AMF)?

Apache Mobile Filter (created by Idel Fuschini) is the easiest and fastest way to detect mobile devices.
AMF is a suite of tools that allow access to a Device Repository (such as WURFL, DetectRight, 51Degrees or others) directly from Apache: now you can detect devices no matter what language your website uses.

> [http://www.apachemobilefilter.org](http://www.apachemobilefilter.org)

# How to use this image.

## Configure

- Create a configuration file `amf.conf` within the content (adjust to fit your needs):

    PerlSetEnv AMFMobileHome /data/amf
    PerlSetEnv CapabilityList brand_name,model_name,max_image_width
    PerlSetEnv AMFProductionMode true
    PerlSetEnv CacheDirectoryStore /data/cache
    PerlSetEnv ResizeImageDirectory /data/cache
    PerlSetEnv ServerMemCached memcached:11211

- Mount this file as a volume into `/etc/httpd/conf.d/amf.conf`
- Create a vhost for your application, use this as example:

    <VirtualHost *:80>
        DocumentRoot /data/app
        Alias /images /data/images

        PerlTransHandler +Apache2::AMFWURFLFilterMemcached

        <Location /images/*>
            Require all granted

            SetHandler modperl
            PerlOutputFilterHandler Apache2::AMFImageRendering
        </Location>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>

- Mount this vhost as a volume into `/etc/httpd/conf.d/revive.conf`

## Run

docker run -it \
    -v $PWD/amf.conf:/etc/httpd/conf.d/amf.conf \
    -v $PWD/my-app_vhost.conf:/etc/httpd/conf.d/my-app.conf \
    diegomarangoni/apache-amf
