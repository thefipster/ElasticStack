# Logging with the Elastic Stack

## Kibana Access

Kibana is protected with a nginx reverse proxy. The credentials currently default to:
> hcvision : awesomelogging

If you want to change the credentials use the following tool to create a `htpasswd` file.
> http://www.htaccesstools.com/htpasswd-generator/

Then update the `htpasswd.users` file inside the `nginx` folder with the generated content.
