# wireguard_playbook

## Шаги для настройки vpn
1. Покупаем vps
2. Отключаем вход по паролю ssh
3. делаем все из под рута, если имеется
4. `apt update && apt upgrade -y && apt autoremove -y`
5. `apt install wireguard`
6. `cd /etc/wireguard`
7. `wg genkey | tee /etc/wireguard/privatekey | wg pubkey | tee /etc/wireguard/publickey`
8. `chmod 600 privatekey`
9. `cat privatekey`
10. `vim wg0.conf`
11. Вставляем в wg0.conf
```conf
[Interface]
PrivateKey = <privatekey>
Address = 10.0.0.1/24
ListenPort = 51830
PostUp = /etc/wireguard/helper/add-nat-routing.sh
PostDown = /etc/wireguard/helper/remove-nat-routing.sh
```
12. `mkdir helper`
13. `cd helper`
14. Копируем из репозиторя `add/remove-nat-routing.sh`
15. Редактируем интерфейс в файлах `add/remove-nat-routing.sh`
16. `chmod 700 add-nat-routing.sh`
17. `chmod 700 remove-nat-routing.sh`
18. `echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf`
19. `sysctl -p`
20. `systemctl enable wg-quick@wg0.service`
21. `systemctl start wg-quick@wg0.service`
22. `wg genkey | tee /etc/wireguard/gromov_privatekey | wg pubkey | tee /etc/wireguard/gromov_publickey`
23. Добавляем это в wg0.conf
```conf
[Peer]
PublicKey = <gromov_publickey>
AllowedIPs = 10.0.0.2/32
```
24. `systemctl restart wg-quick@wg0.service`
