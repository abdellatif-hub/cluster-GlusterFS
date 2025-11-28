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
# Ubuntu
<img width="1289" height="534" alt="image" src="https://github.com/user-attachments/assets/3d5d23a0-8d23-409b-ac98-18b023c287bc" />

---
# Server1
<img width="1912" height="238" alt="image" src="https://github.com/user-attachments/assets/d12a0661-1c1f-4780-a9bf-68e717a581d5" />
...
# Server2
<img width="800" height="486" alt="image" src="https://github.com/user-attachments/assets/53330e5e-a40d-4267-b4a0-8e7aecc8bc7b" />




---

## 📌 2. Configuration du fichier `/etc/hosts`

À ajouter sur **server1**, **server2**, et **ubuntu** :


### 📸 Screenshot : /etc/hosts  
`![hosts_file](screenshots/hosts_file.png)`

---

## ⚙️ 3. Installation de GlusterFS sur server1 & server2

```bash
sudo apt update -y
sudo apt install glusterfs-server -y
sudo systemctl enable --now glusterd
systemctl status glusterd
📸 Screenshot : installation GlusterFS

![install_glusterfs](screenshots/install_glusterfs.png)
````
## 🔗 4. Peering : connexion entre server1 et server2

Sur server1 uniquement :

sudo gluster peer probe server2
sudo gluster peer status

## 📸 Screenshot : peer status

![peer_status](screenshots/peer_status.png)




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
