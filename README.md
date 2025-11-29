# 🚀 Déploiement d’un Mini-Cluster GlusterFS (Replica 2)

Ce projet montre comment déployer un cluster **GlusterFS** composé de deux serveurs
(*server1* et *server2*) ainsi qu’un client Linux (*ubuntu*).  
L’objectif est d’installer GlusterFS, créer un volume répliqué, le monter sur le client, et vérifier la réplication et la tolérance aux pannes.

---

## 🧩 1. Architecture du Cluster
```
| Machine  | Rôle               | Adresse IP      |
|----------|--------------------|-----------------|
| server1  | Serveur GlusterFS  | 192.168.56.103  |
| server2  | Serveur GlusterFS  | 192.168.56.104  |
| ubuntu   | Client Linux       | 192.168.56.102  |
```
---
## Ubuntu
<img width="1289" height="534" alt="image" src="https://github.com/user-attachments/assets/3d5d23a0-8d23-409b-ac98-18b023c287bc" />


## Server1
<img width="1937" height="404" alt="image" src="https://github.com/user-attachments/assets/23f28c33-a7ed-4d93-b08f-2b28cb522430" />


## Server2
<img width="804" height="438" alt="image" src="https://github.com/user-attachments/assets/fe67e904-192f-4993-9926-bfc4f87ef431" />





---

## 📌 2. Configuration du fichier `/etc/hosts`

À ajouter sur **server1**, **server2**, et **ubuntu** :


<img width="1539" height="1074" alt="image" src="https://github.com/user-attachments/assets/bc8cdffa-6dca-418f-9b37-c3415cc4c5a0" />


---

## ⚙️ 3. Installation de GlusterFS sur server1 & server2


````sudo apt update -y
sudo apt install glusterfs-server -y
sudo systemctl enable --now glusterd
systemctl status glusterd
````
<img width="1926" height="872" alt="image" src="https://github.com/user-attachments/assets/171a1479-21d7-4975-adce-5820751284c5" />


## 🔗 4. Peering : connexion entre server1 et server2
````
Sur server1 uniquement :

sudo gluster peer probe server2
sudo gluster peer status
````

<img width="1944" height="239" alt="image" src="https://github.com/user-attachments/assets/379ac140-8bb2-434f-af4d-080cdc79b882" />





## 🧱 5. Création du volume répliqué (Replica 2)
✔️ Créer un brick sur server1 et server2
sudo mkdir -p /gluster/brick1

✔️ Créer le volume répliqué sur server1
sudo gluster volume create voldata \
replica 2 \
server1:/gluster/brick1 \
server2:/gluster/brick1 \
force

✔️ Démarrer le volume
sudo gluster volume start voldata
gluster volume info

## 📸 Screenshot : volume info

![volume_info](screenshots/volume_info.png)

## 📂 6. Montage du volume sur le client ubuntu

Sur ubuntu :

sudo apt update -y
sudo apt install glusterfs-client -y
sudo mkdir /mnt/voldata
sudo mount -t glusterfs server1:/voldata /mnt/voldata

✔️ Vérification :
df -h | grep voldata

## 📸 Screenshot : volume monté

![mount_volume](screenshots/mount_volume.png)

## 🔁 7. Test de réplication
✔️ Sur le client (ubuntu)
echo "test replication glusterfs" | sudo tee /mnt/voldata/test.txt

✔️ Vérifier sur server1
cat /gluster/brick1/test.txt

✔️ Vérifier sur server2
cat /gluster/brick1/test.txt

## 📸 Screenshots : test de réplication

![client_test](screenshots/client_test.png)

![server1_test](screenshots/server1_test.png)

![server2_test](screenshots/server2_test.png)

## 🛡️ 8. Test de tolérance aux pannes
✔️ Arrêter GlusterFS sur server2
sudo systemctl stop glusterd

✔️ Lire le fichier depuis ubuntu
cat /mnt/voldata/test.txt


➡️ Le fichier doit rester accessible.

✔️ Redémarrer server2
sudo systemctl start glusterd

## 📸 Screenshot : test de panne

![failover_test](screenshots/failover_test.png)
