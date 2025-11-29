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
<img width="1573" height="544" alt="image" src="https://github.com/user-attachments/assets/76c03912-d871-4749-acf0-c08275fd1f33" />



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
## sur server1
<img width="1944" height="239" alt="image" src="https://github.com/user-attachments/assets/379ac140-8bb2-434f-af4d-080cdc79b882" />

## sur server2
<img width="811" height="251" alt="image" src="https://github.com/user-attachments/assets/5dadc624-7d25-4388-bd0e-88def439775d" />





## 🧱 5. Création du volume répliqué (Replica 2)
````
✔️ Créer un brick sur server1 et server2
sudo mkdir -p /gluster/brick1
````

<img width="1931" height="83" alt="image" src="https://github.com/user-attachments/assets/34fc6fae-ac43-4345-8d17-379da03a9ca3" />


````
✔️ Créer le volume répliqué sur server1
sudo gluster volume create voldata replica 2 server1:/gluster/brick1 server2:/gluster/brick1 force
✔️ Démarrer le volume
sudo gluster volume start voldata
gluster volume info
````

<img width="1948" height="382" alt="image" src="https://github.com/user-attachments/assets/548cc694-ffc2-434e-b7d9-04641e8c1191" />


## 📂 6. Montage du volume sur le client ubuntu

Sur ubuntu :

sudo apt update -y
sudo apt install glusterfs-client -y
sudo mkdir /mnt/voldata

<img width="1465" height="158" alt="image" src="https://github.com/user-attachments/assets/f1f89d58-a0bd-4de6-8096-a3cf1304584b" />

sudo mount -t glusterfs server1:/voldata /mnt/voldata

✔️ Vérification :
df -h | grep voldata

<img width="1570" height="201" alt="image" src="https://github.com/user-attachments/assets/46578ead-b25e-4056-b60e-af83407fb597" />


## 🔁 7. Test de réplication

✔️ Sur le client (ubuntu)
echo "test replication glusterfs" | sudo tee /mnt/voldata/test.txt

<img width="1548" height="431" alt="image" src="https://github.com/user-attachments/assets/e878ff62-48ba-48f5-ab61-41b5aa72196d" />



✔️ Vérifier sur server1
cat /gluster/brick1/test.txt

<img width="1956" height="70" alt="image" src="https://github.com/user-attachments/assets/d8320cdd-58d7-468b-af08-d8eaf3e40324" />




✔️ Vérifier sur server2
cat /gluster/brick1/test.txt

<img width="850" height="78" alt="image" src="https://github.com/user-attachments/assets/b0c10d83-6558-4b6d-abe2-023ca6913771" />


## ✅ Conclusion 

Ce TP nous a permis de configurer un cluster GlusterFS composé de deux serveurs afin de créer un volume répliqué et hautement disponible. Après la configuration réseau, l’ajout des pairs et la création du volume voldata, le montage sur la machine cliente a confirmé le bon fonctionnement du système. Ce travail a montré l’intérêt de GlusterFS pour assurer une réplication automatique des données et une meilleure tolérance aux pannes.


