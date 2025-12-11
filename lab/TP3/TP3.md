# TP3 : Déploiement, publication et analyse d'un déploiement

Le but de ce TP est de comprendre les techniques et mécanismes de déploiement d'une solution applicative.

## 1ère partie : déploiement

Ouvir le fichier deployment.yaml et inspecter son contenu.

Copier ce fichier sur la machine master et démarrer cette solution.

```bash
kubectl apply -f deployment.yaml
```

Monitorer l'avancement du déploiement, afficher la liste des déploiements et la liste des réplicas:

```bash
kubectl get deployments
kubectl get rs
```

En cas d'erreur ou de besoin d'annuler le déploiement:

```bash
kubectl delete -f deployment.yaml
```

## 2ème partie : Création d'un service

Ouvir le fichier service.yaml et inspecter son contenu.

Copier ce fichier sur la machine master et créer le service.

```bash
kubectl create -f service.yaml
```

Vérifier que le service est bien créé

```bash
kubectl get svc
```

Réponse:

```
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE

ngnix-service   NodePort    10.102.124.180   <none>        80:31367/TCP   5m57s
```

Repérer le noeud sur lequel est déployé le pod

```bash
kubectl describe pods nginx-74d589986c-dg26b |grep Node
```

Avec votre navigateur, ouvrir la page http://192.168.56.11:31367 (penser à changer l'IP avec celle d'un des noeuds worker1 ou worker2)


