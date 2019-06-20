### routing all the 80 requests to https*

## Create custom file called *-http.conf under /etc/nginx/conf.d
server {
	listen 80 default_server;
	listen [::]:80 default_server;
	server_name hackmd.cisco.com;
	return 301 https://$server_name$request_uri;

}

## Create custom file called *-https.conf under /etc/nginx/conf.d for https 

## upstream codesnippet is for load balancer
upstream hackmd {
  ip_hash;
  server private_ip:3000;
  server private_ip:3000;
}

server {
  listen 443 ssl;

  ssl_certificate_key /etc/nginx/ssl/codehacks/codehacks.key;
  ssl_certificate /etc/nginx/ssl/codehacks/codehacks_StackedChain.pem;

  server_name hackmd.cisco.com;

  proxy_set_header HOST $host;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_http_version 1.1;
  proxy_request_buffering off;
  proxy_ssl_server_name on;

  location = /home {
    return 301 https://$server_name/Jcw04-U6SLSyEdTSG37x6A;
  }

  location / {
    proxy_pass http://hackmd;
  }

}

###Create custom file called *-https.conf under /etc/nginx/conf.d for  minio http configuration

server {
	listen 80;
	listen [::]:80;
	server_name minio.cisco.com minio-beta.cisco.com;

	# To allow special characters in headers
	ignore_invalid_headers off;

	# Allow any size file to be uploaded.
	# Set to a value such as 1000m; to restrict file size to a specific value
	client_max_body_size 0;

	# To disable buffering
	proxy_buffering off;

	location / {
		proxy_set_header Host $http_host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_pass http://10.0.5.12:9001;
	}
}
