# Logging with the Elastic Stack

## Kibana Access

Kibana is protected with a nginx reverse proxy. The credentials can be configured in the htpasswd file which is injected into the container via a docker swarm secret.

### Update credentials

If you want to change the credentials use the following tool to create a `htpasswd` file.
> http://www.htaccesstools.com/htpasswd-generator/

Then update the `htpasswd.users` file inside the `nginx` folder with the generated content.

### Deploy credentials

Update the secret inside of the docker swarm and restart or redeploy the nginx proxy container.
