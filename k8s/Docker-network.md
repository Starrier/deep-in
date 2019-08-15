docker run -d --restart=unless-stopped \
-p 80:80 -p 443:443 \
-v <主机路径>:/var/lib/rancher/ \
-v /root/var/log/auditlog:/var/log/auditlog \
-e AUDIT_LEVEL=3 \
rancher/rancher:stable (或者rancher/rancher:latest)