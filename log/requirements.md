# Log requirements

## Application Level

Note: Need to collectively create a logging format that is easy to parse as well as provide sufficient logging information (e.g Time, Action taken)

- [ ] Actions carried out by users
    - [ ] Authentication, TOTP success/failure
    - [ ] Access of specific user information
    - [ ] Upload of user information
- [ ] etc...

## Service Level

- [ ] NGINX Reverse Proxy
    - [ ] Access Logs (Shows what client IP has requested what URL and the HTTP status code)
    - [ ] Error logs (Useless)

- [ ] Gunicorn
    - [ ] Access logs (Pretty much the same as NGINX Access logs)
    - [ ] Error logs (Useless)

- [ ] PostgreSQL (See [here](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT))
    - [ ] `all` or `ddl` or `mod`

- [ ] Vault
    - [ ] Operational logs