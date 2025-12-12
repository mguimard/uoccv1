                                                                                                                                                                                                           ─╯
# TP5.1 : Déploiement d'une application

Le but de ce TP est de déployer une application de type wordpress.

Objectifs:

* Créer des volumes persistents
* Créer un fichier kustomization.yaml avec
        - générateur de mot de passe
        - configuration MySQL
        - configuration WordPress
* Appliquer le répertoire kustomization avec "kubectl apply -k ./"
* Nettoyage

## Etude d'un exemple de stockage

Mettre les 4 fichiers .yaml sur votre noeud master et les inspecter.

Appliquer les commandes listées dans le fichier run-example.md.

## Téléchargement des configurations

Depuis le noeud master, taper ces commandes:

```bash
mkdir wp
cd wp
wget https://kubernetes.io/examples/application/wordpress/mysql-deployment.yaml
wget https://kubernetes.io/examples/application/wordpress/wordpress-deployment.yaml
```

## Création des dossiers

MySQL et Wordpres ont tout les 2 besoin d'un volume pour stocker les données.

Inspecter les fichiers mysql-deployment.yaml et wordpress-deployment.yaml, ils contiennent la configuration des volumes

Sur le worker 1, créer les dossiers suivants

```bash
mkdir /mnt/data/mysql
mkdir /mnt/data/wordpress
```

## Adaptation des sources

Les fichiers de déploiements pour wordpress et mysql doivent être modifiés pour correspondre à notre nommage de storage et de volumes.

Modifier ces fichiers en conséquence.

## Création de kustomization.yaml

Créer un fichier yaml avec la commande suivante:

```bash
cat <<EOF >./kustomization.yaml
secretGenerator:
- name: mysql-pass
  literals:
  - password=changeit
resources:
  - mysql-deployment.yaml
  - wordpress-deployment.yaml
EOF
```

Puis, lancer la création de la stack.

```bash
kubectl apply -k ./
```

Vérifier les pods, au moindre problème, recommencer :

```bash
kubectl delete -k ./
# modif des sources... puis...
kubectl apply -k ./
```

Vérifier la création du secret

```bash
kubectl get secrets
```

Vérifier la création des pods

```bash
kubectl get pods
```

Vérifier la création du Service

```bash
kubectl get services wordpress
```

## Accès à wordpress

Sur le worker1: 

http://192.168.56.11:31238

Lancer l'installation, vérifier qu'elle se déroule correctement (entrer un email jetable type yopmail.com si souhaité) et ajouter un peu de données dans le système (ajout entrée wordpress, page, etc...)

Redémarrer les machines virtuelles. Vérifier que les données sont toujours présentes.

NFS Server

```bash
sudo apt install nfs-kernel-server
sudo mkdir /var/nfs/kube-storage -p
sudo chown nobody:nogroup /var/nfs/kube-storage
sudo nano /etc/exports
/var/nfs/kube-storage    192.168.0.0/16(rw,sync,no_subtree_check)
```

NFS Client

```bash
sudo apt install -y nfs-common
```