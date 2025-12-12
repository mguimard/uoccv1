# TP5.2 Installation de Helm - déploiement nextcloud

Helm est un utilitaire en ligne de commande pour faciliter le déploiement d'applications.

## Récupération de l'archive et installation

Vérification de la version installée : 

```bash
helm version
```

## Installation de nextcloud

```bash
helm repo add nextcloud https://nextcloud.github.io/helm/
helm repo update
helm install my-nextcloud nextcloud/nextcloud
```

Proxy et test :

```bash
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=nextcloud" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward --namespace default $POD_NAME 8080:80 --address 0.0.0.0
```

Accéder à http://192.168.56.10:8080