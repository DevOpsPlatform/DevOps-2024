
**win_ping issues and solutions**:

#### **Error-1**:

```
"msg": "Failed to connect to the host via ssh: OpenSSH_8.7p1, OpenSSL 3.0.7 1 Nov 2022\r\ndebug1: Reading configuration data /etc/ssh/ssh_config\r\ndebug3: /etc/ssh/ssh_config line 55:
Including file /etc/ssh/ssh_config.d/50-redhat.conf depth 0\r\ndebug1: Reading configuration data /etc/ssh/ssh_config.d/50-redhat.conf\r\ndebug2:
checking match for 'final all' host 172.190.97.225 originally 172.190.97.225\r\ndebug3: /etc/ssh/ssh_config.d/50-redhat.conf line 3:
not matched 'final'\r\ndebug2: match not found\r\ndebug3: /etc/ssh/ssh_config.d/50-redhat.conf line 5:
Including file /etc/crypto-policies/back-ends/openssh.config depth 1 (parse only)\r\ndebug1: Reading configuration data /etc/crypto-policies/back-ends/openssh.config\r\ndebug3:
 gss kex names ok: [gss-curve25519-sha256-,gss-nistp256-sha256-,gss-group14-sha256-,gss-group16-sha512-]\r\ndebug3:
kex names ok: [curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha256,
diffie-hellman-group16-sha512,diffie-hellman-group18-sha512]\r\ndebug1: configuration requests final Match pass\r\ndebug2: resolve_canonicalize:
hostname 172.190.97.225 is address\r\ndebug1: re-parsing configuration\r\ndebug1: Reading configuration data /etc/ssh/ssh_config\r\ndebug3:
/etc/ssh/ssh_config line 55: Including file /etc/ssh/ssh_config.d/50-redhat.conf depth 0\r\ndebug1: Reading configuration data /etc/ssh/ssh_config.d/50-redhat.conf\r\ndebug2:
checking match for 'final all' host 172.190.97.225 originally 172.190.97.225\r\ndebug3: /etc/ssh/ssh_config.d/50-redhat.conf line 3: matched 'final'\r\ndebug2: match found\r\ndebug3:
/etc/ssh/ssh_config.d/50-redhat.conf line 5: Including file /etc/crypto-policies/back-ends/openssh.config depth 1\r\ndebug1: Reading configuration
data /etc/crypto-policies/back-ends/openssh.config\r\ndebug3: gss kex names ok: [gss-curve25519-sha256-,gss-nistp256-sha256-,gss-group14-sha256-,gss-group16-sha512-]\r\ndebug3:
 kex names ok: [curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,
diffie-hellman-group14-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512]\r\ndebug3: expanded UserKnownHostsFile '~/.ssh/known_hosts' ->
'/runner/.ssh/known_hosts'\r\ndebug3: expanded UserKnownHostsFile '~/.ssh/known_hosts2' -> '/runner/.ssh/known_hosts2'\r\ndebug1: auto-mux:
Trying existing master\r\ndebug1: Control socket \"/runner/.ansible/cp/b207c448fd\" does not exist\r\ndebug3: ssh_connect_direct: entering\r\ndebug1:
Connecting to 172.190.97.225 [172.190.97.225] port 22.\r\ndebug3: set_sock_tos: set socket 3 IP_TOS 0x48\r\ndebug2: fd 3 setting O_NONBLOCK\r\ndebug1:
connect to address 172.190.97.225 port 22: Connection timed out\r\nssh: connect to host 172.190.97.225 port 22: Connection timed out",
```

**Solution**:

add these vars to inventory: https://github.com/DevOpsPlatform/DevOps-2024/blob/awx/winrm/vars-for-win.yml

```
---
ansible_port: 5986
ansible_connection: winrm
ansible_winrm_transport: credssp
ansible_winrm_server_cert_validation: ignore
```

#### **Error-2**:
```
<172.###.###.###> ESTABLISH WINRM CONNECTION FOR USER: practicewi on PORT 5986 TO 172.###.###.###
172.###.###.### | UNREACHABLE! => {
    "changed": false,
    "msg": "credssp: requests auth method is credssp, but requests-credssp is not installed",
    "unreachable": true
}
```

**Solution**: Build custom image and push to repo - https://github.com/DevOpsPlatform/DevOps-2024/blob/awx/custome-ee/Dockerfile

```
docker build -t customee:1.0 -f Dockerfile .
docker tag customee:1.0 venkatasykam/awx-customee:1.0
docker push venkatasykam/awx-customee:1.0
```

configure this image for `AWX EE (latest)` Execution Environment

![image](https://github.com/DevOpsPlatform/DevOps-2024/assets/24622526/f8a46107-216d-4663-8ec8-83497fc73494)


![image](https://github.com/DevOpsPlatform/DevOps-2024/assets/24622526/b349ca7c-75d5-4be9-9c35-3262a4fcbe21)


#### **Error-3**:

```
<172.###.###.###> ESTABLISH WINRM CONNECTION FOR USER: practicewi on PORT 5986 TO 172.###.###.###
172.###.###.### | UNREACHABLE! => {
    "changed": false,
    "msg": "credssp: HTTPSConnectionPool(host='172.###.###.###', port=5986): Max retries exceeded with url: /wsman (Caused by ConnectTimeoutError(<urllib3.connection.HTTPSConnection object at 0x7fe7903d8a60>, 'Connection to 172.###.###.### timed out. (connect timeout=30)'))",
    "unreachable": true
}
```

**Solutions**:

**Step-1**: Open 5986 port at server side at inbound rules.
and 
**Step-2**: Connect to the windows server and run [ConfigureRemotingForAnsible.ps1](https://github.com/DevOpsPlatform/DevOps-2024/blob/awx/winrm/ConfigureRemotingForAnsible.ps1) this ps1 file in admin mode in powershell commond line.




