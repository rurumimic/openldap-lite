# OpenLDAP ldap

## Links

- [How to OpenLDAP](https://github.com/rurumimic/how-to-openldap)

---

## Refresh Certs

you will see this error:

```bash
TASK [Add new entries] *********************************************************
fatal: [default]: FAILED! => {"changed": true, "cmd": "ldapadd -x -w password -D \"cn=manager,ou=admins,dc=example,dc=com\" -f directories.ldif -Z", "delta": "0:00:00.031612", "end": "2021-02-25 07:28:21.822569", "msg": "non-zero return code", "rc": 254, "start": "2021-02-25 07:28:21.790957", "stderr": "ldap_start_tls: Connect error (-11)\n\tadditional info: error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify failed (certificate has expired)\nldap_result: Can't contact LDAP server (-1)", "stderr_lines": ["ldap_start_tls: Connect error (-11)", "\tadditional info: error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify failed (certificate has expired)", "ldap_result: Can't contact LDAP server (-1)"], "stdout": "", "stdout_lines": []}
```

refresh certs:

```bash
cd ldap/certs
rm rootca* example*
./gen.certs.sh
```

---

## Start

```bash
vagrant up
```

### Client

```bash
vagrant ssh
```


#### LDAP User

```bash
ldapwhoami -x -w password -H ldap://ldap.example.com -D "cn=manager,ou=admins,dc=example,dc=com" -Z

dn:cn=manager,ou=admins,dc=example,dc=com
```

#### LDAP Search

1. Search all as `cn=manager`
1. Add 'keanu': [Add data to LDAP](#add-data-to-ldap)
1. Search 'keanu' as `cn=manager`
1. Search 'keanu' as `cn=Keanu Reeves`

```bash
ldapsearch -x -w password -H ldap://ldap.example.com -D "cn=manager,ou=admins,dc=example,dc=com" objectClass=* -b dc=example,dc=com -Z
```

#### Add data to LDAP

```bash
ldapadd -x -w password -D "cn=manager,ou=admins,dc=example,dc=com" -Z -f /share/keanu.ldif
```

```bash
adding new entry "cn=Keanu Reeves,ou=people,dc=example,dc=com"
```

#### Search 'keanu' as `cn=manager`

```bash
ldapsearch -x -w password -D "cn=manager,ou=admins,dc=example,dc=com" -b "dc=example,dc=com" "uid=keanu" -Z
```

#### Search 'keanu' as `cn=Keanu Reeves`

```bash
ldapsearch -x -w password -D "cn=Keanu Reeves,ou=people,dc=example,dc=com" -b "dc=example,dc=com" "uid=keanu" -Z
ldapsearch -x -w password -D "cn=Keanu Reeves,ou=people,dc=example,dc=com" -b dc=example,dc=com objectClass=* -Z
```
#### Who am i

```bash
ldapwhoami -x -w password -H ldap://ldap.example.com -D "cn=Keanu Reeves,ou=people,dc=example,dc=com" -Z

dn:cn=Keanu Reeves,ou=people,dc=example,dc=com
```

#### Delete data

```bash
ldapdelete -x -w password -D "cn=manager,ou=admins,dc=example,dc=com" "cn=Keanu Reeves,ou=people,dc=example,dc=com" -Z
```

---

## Destroy VM

```bash
vagrant destroy -f
```
