# 05 — Network Automation

---

## لماذا أتمتة الشبكات؟

```
بدون أتمتة: تكوين 100 راوتر = 100 ساعة عمل يدوي + أخطاء بشرية
مع أتمتة:   نفس المهمة = دقائق + لا أخطاء
```

---

## Python للشبكات

```python
# Netmiko — SSH للأجهزة الشبكية
from netmiko import ConnectHandler

cisco = {
    'device_type': 'cisco_ios',
    'host': '192.168.1.1',
    'username': 'admin',
    'password': 'secret',
}

with ConnectHandler(**cisco) as conn:
    output = conn.send_command('show ip interface brief')
    print(output)

    # تكوين
    commands = [
        'interface loopback 0',
        'ip address 1.1.1.1 255.255.255.255',
    ]
    conn.send_config_set(commands)
```

```python
# Nornir — أسرع (Parallel)
from nornir import InitNornir
from nornir_netmiko.tasks import netmiko_send_command
from nornir_utils.plugins.functions import print_result

nr = InitNornir(config_file="config.yml")
result = nr.run(task=netmiko_send_command, command_string="show version")
print_result(result)
```

---

## Ansible للشبكات

```yaml
# playbook الشبكات
- hosts: routers
  gather_facts: no
  tasks:
    - name: Configure OSPF
      cisco.ios.ios_ospf_interfaces:
        config:
          - name: GigabitEthernet0/0
            address_family:
              - afi: ipv4
                process:
                  id: 1
                  area_id: "0"

    - name: Backup Config
      cisco.ios.ios_config:
        backup: yes
```

---

## RESTCONF / NETCONF

```python
# NETCONF — XML-based
import ncclient

with ncclient.manager.connect(
    host='192.168.1.1',
    username='admin',
    password='secret',
    hostkey_verify=False
) as m:
    # Get running config
    config = m.get_config(source='running')
    print(config.xml)

# RESTCONF — REST API للأجهزة الحديثة
import requests

url = "https://192.168.1.1/restconf/data/ietf-interfaces:interfaces"
headers = {'Accept': 'application/yang-data+json'}
r = requests.get(url, auth=('admin', 'secret'), headers=headers, verify=False)
print(r.json())
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Network Programmability and Automation](https://www.oreilly.com/library/view/network-programmability-and/9781491931240/) - كتاب
- [Cisco DevNet](https://developer.cisco.com/learning/) ← مجاني
