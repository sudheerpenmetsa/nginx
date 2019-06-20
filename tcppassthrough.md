## ## tcp LB and SSL passthrough for backend ##
stream {

    log_format combined '$remote_addr - - [$time_local] $protocol $status $bytes_sent $bytes_received $session_time "$upstream_addr"';

    access_log /var/log/nginx/stream-access.log combined;


#    upstream minio1-rcdn-9001 {
#        server private_ip:9001 max_fails=3 fail_timeout=10s;
#    }

#    server {
#        listen 9001;
#        proxy_pass minio1-rcdn-9001;
#        proxy_next_upstream on;
#    }

    upstream psql-5431 {
        server private_ip:5431 max_fails=3 fail_timeout=10s;
    }

    server {
        listen 5431;
        proxy_pass psql-5431;
        proxy_next_upstream on;
    }

}
