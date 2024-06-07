# OSPF and FRR

**2.	Настройте внутреннюю динамическую маршрутизацию по средствам FRR. Выберите и обоснуйте выбор протокола динамической маршрутизации из расчёта, что в дальнейшем сеть будет масштабироваться.**  
**Настройка FRR**  
Для настройки потребуется включённый forwarding, настройка выполнялась ранее.  

Предварительно настроем интерфейс туннеля:
## **HQ-R**
```
nmtui
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/62a54525-cff5-46fb-9c46-1d4f0f9a1499)  
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/4df2a006-fa62-4cf3-aa34-2c7685c470a5)  

## **BR-R**
```
nmtui
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/62a54525-cff5-46fb-9c46-1d4f0f9a1499)  
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/914a562f-36cd-4bab-989e-97f2db457044)  

**Обоснование**: Настройку динамическое маршрутизации производим с помощью протокола **OSPF** – Данный протокол динамической сети позволяет разделять сеть на логические области, что делает его масштабируемым для больших сетей.  
Каждая область может иметь свою таблицу маршрутизации, что уменьшает нагрузку на маршрутизаторы и улучшает производительность сети.  
## **HQ-R**

```
nano /etc/frr/daemons
меняем строчку
ospfd=no на строчку
ospfd=yes
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/3e2c4d58-ea53-44a9-b222-7bf85951b9b4)  

```
ctrl-x
y
systemctl enable --now frr
vtysh
conf t
router ospf
passive-interface default
network 10.0.0.0/26 area 0
network 172.16.0.0/24 area 0
exit
interface tun1
no ip ospf network broadcast
no ip ospf passive
exit
do write memory
exit
```
Зададим TTL для OSPF  
```
nmcli connection edit tun1
set ip-tunnel.ttl 64
save
quit
```

Временно выключаем сервис службы **firewalld**  
```
systemctl stop firewalld.service
systemctl disable --now firewalld.service
```
```
systemctl restart frr
```

## **BR-R**

```
nano /etc/frr/daemons
меняем строчку
ospfd=no на строчку
ospfd=yes
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/3e2c4d58-ea53-44a9-b222-7bf85951b9b4)  

```
ctrl-x
y
systemctl enable --now frr
vtysh
conf t
router ospf
passive-interface default
network 10.0.2.0/28 area 0
network 172.16.0.0/24 area 0
exit
interface tun1
no ip ospf network broadcast
no ip ospf passive
exit
do write memory
exit
```
Зададим TTL для OSPF  
```
nmcli connection edit tun1
set ip-tunnel.ttl 64
save
quit
```
Временно выключаем сервис службы **firewalld**  
```
systemctl stop firewalld.service
systemctl disable --now firewalld.service
```
```
systemctl restart frr
```
Проверим работу OSPF:  
```
show ip ospf neighbor
exit
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/c9b9072d-3d7d-4949-8541-cb45601ccb61)
P.S. в случае, если OSPF не заработал, можно перезапустить машины **ISP** **BR-R** **HQ-R**  

**a.	Составьте топологию сети L3.**  

**Схема топологии L3**  
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/9ad4ac5a-68a1-4fcd-ad5d-697776518faa)  
 
**P.S.** спасибо sysahelper за рисунок!  
