```bash
service network-manager stop
rm /var/lib/NetworkManager/NetworkManager.state
service network-manager start

nano /etc/NetworkManager/NetworkManager.conf
```
把false改成true
