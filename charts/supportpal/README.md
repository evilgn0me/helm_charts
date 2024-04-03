# SupportPal helm chart 

This is unofficial helm chart for SupportPal. It runs monolithic SupportPal installation.
You need to use ingress-nginx with this, since you need configuration-snippet. Without configuration-snippet annotation it won't work.

```
ingress:
  enabled: true
  className: "nginx"
  annotations: 
    acme.cert-manager.io/http01-edit-in-place: "true"
    cert-manager.io/cluster-issuer: letsencrypt
    cert-manager.io/issue-temporary-certificate: "true"
    kubernetes.io/ingress.class: nginx
    kubernetes.io/tls-acme: "true"
    nginx.ingress.kubernetes.io/from-to-www-redirect: "true"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "3600"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "3600"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/configuration-snippet: |
          add_header X-Frame-Options SAMEORIGIN always;
          add_header X-Content-Type-Options nosniff always;
          add_header X-XSS-Protection "1; mode=block" always;
          add_header Referrer-Policy strict-origin-when-cross-origin always;
          set $user_request 0;
          if ($request_method = GET) {
              set $user_request "GET";
          }
          if ($request_uri ~* "^(.*/)index\.php/?(.*)$") {
              set $user_request "${user_request}-index.php";
          }
          if ($user_request = "GET-index.php") {
              return 301 $1$2;
          }
  hosts:
    - host: supportpal.example.com
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: 
    - secretName: helpdesk-tls
      hosts:
        - supportpal.example.com
```

