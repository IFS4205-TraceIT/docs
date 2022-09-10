# PKI requirements
This document will details the certificates required to secure communications within the infrastructure and the purpose and usage method of each certicate.

## Root CA

`vault` will be used as the root CA via its `pki` secrets engine.

## Intermediate CA

Since we only have one instance of `vault`, that instance will act as both the root CA and the intermediate CA. However, anything with issuing certicates (for HTTPs or database TLS communication) will be signed and issued by the intermediate CA.

Technically, we can use the root CA to sign and issue certificates, but it is not a recommended practice.

## Certficate Checklist

* [ ] TLS Between `vault` clients (i.e web applications) and `vault` server
    * Ensure encrypted delivery of secrets
    * TLS is configured via the `vault` server's `tcp` Listener configuration: 
        * `tls_cert_file`: HTTPS certificate
        * `tls_key_file`: Private key to HTTPS certificate
        * Read more [here](https://developer.hashicorp.com/vault/docs/configuration/listener/tcp#tcp-listener-parameters)
    * `hvac.Client` takes in 2 arguments:
        * `cert`: ??
        * `verify`: CA Certificate Bundle
        * Read more [here](https://hvac.readthedocs.io/en/stable/source/hvac_v1.html#hvac.v1.Client.__init__)

* [ ] TLS between `nginx` internal reverse proxy and `Django` web applications, via the `uWSGI` interface
    * Ensure encrypted delivery of web traffic
    * Read more [here](https://docs.nginx.com/nginx/admin-guide/web-server/app-gateway-uwsgi-django/)

* [ ] TLS between `Django` web applications, anonymization process and `PostgreSQL`
    * Ensure encrypted delivery of database query results
    * Sample database configuration:
        ```python
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.postgresql_psycopg2',
                'HOST': 'server-ip',
                'PORT': '5432',
                'NAME': 'database-name',
                'USER': 'username',
                'PASSWORD': 'password',
                'OPTIONS': {
                    'sslmode': 'require',
                    'sslcert': '/path/to/file',     # SSL Certficate
                    'sslkey': '/path/to/file',      # Private key to SSL Certficate
                    'sslrootcert': '/path/to/file', # CA Certficates bundle
                },
            },
        }
        ```

* [ ]  TLS between Fluentbit on each individual VMs and Grafana Loki 
    * Ensure encrypted delivery of logs and metrics

* [ ] **(Optional)** TLS between Grafana and Grafana Loki