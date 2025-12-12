# TP6: Mise en place d'écran de monitoring, simulation de panne

Le but de ce TP est de déployer Kubernetes Dashboard pour déployer des conteneurs avec réplicas, et de monitorer une panne d'un noeud.

## Déploiement du dashboard

L'interface utilisateur du tableau de bord n'est pas déployée par défaut. Le dépot du projet se trouve ici : https://github.com/kubernetes/dashboard

Pour le déployer, exécutez les commandes suivantes :


Old fashion

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

Recommended :

https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#deploying-the-dashboard-ui

https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md#creating-a-service-account


Vérification des pods :

```bash
kubectl get pods -n kubernetes-dashboard
```

Vérfication des services : 

```bash
kubectl get svc -n kubernetes-dashboard
```

## Création du user admin

Créer un fichier nommé `admin-user.yaml`

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

Puis lancer la commande suivante:

```bash
kubectl apply -f admin-user.yaml
```

Créer un fichier nommé `cluster-admin.yaml`

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Puis lancer la commande suivante:

```bash
kubectl apply -f cluster-admin.yaml
```

Récupérer un token pour la connection.

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

Exemple de token:

```bash
eyJhbGciOiJSUzI1NiIsImtpZCI6ImZMclktVHkwenZ6QXJEMG83SEh2SDVjRGxEWnpmcEhBZU9TTTl1aGZ1V3cifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWc1eDlxIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJlMjliMjkxNy01OGU3LTQ5ODQtOTc2Zi1hNTk0ZGE2ZjQzODIiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.bLzbqWYyPB79RhPNgGJpKCAqCQMofWsC4Q3g3VhsBtAp3PTVWM7W7xW9l38nug2TLUPIc0W_MWpITB3lVqvBI-etcRg1TdJTVbJCb3GksPE7qUyGbW7qklpS2I7UMt9htVH3hNifsggDN3e2sA1JeKUKHhkudFPNlYXJJeilAxQmHC0cHs1VY1MdNv4dzp5Zx9-x_JUeI_7WeR8EfeAV2uC_AGtadW5mOn0_Qqs5fgKWE3M1fqQxSNcGpLMjIWZWKRTDaJ4sucRNs4-AISAg9yjYcVL_CR4RpMx_69Kk2_DN8iaXZh_3AhvMN2LW2MMZVkm6P3a69HJ_9JVczNN-lw
```

## Lancement d'un proxy et connection à l'interface

Lancer un proxy pour un accès extérieur à notre VM master:

```bash
kubectl proxy
```

Dans un nouveau terminal, dossier lab, ouvrir un tunnel SSH sur notre machine master (la connexion par token ne peut se faire qu'avec localhost ou https)

Old fashion

```bash
ssh -L 8001:localhost:8001 vagrant@192.168.56.10 -i .vagrant/machines/master/virtualbox/private_key 
```

HTTPS 8443

```bash
ssh -L 8443:localhost:8443 vagrant@192.168.56.10 -i .vagrant/machines/master/virtualbox/private_key 
```

Soit proxy le port avec kubectl

```bash
kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy --address 0.0.0.0 8443:443
```


Accéder à l'interface:

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

Se connecter avec le token obtenu précédement

## Déploiement de conteneurs depuis l'interface

Le tableau de bord permet de créer et de déployer une application conteneurisée en tant que Deployment et optionnellement un Service

Cliquer sur le bouton "Create new resource" (+) dans le coin supérieur droit de n’importe quelle page pour commencer.

* Ouvrir l'onglet "Create from form" (créer depuis un formulaire)
* App name: Nom de votre application. Un label avec le nom sera ajouté au Deployment et Service.
* Container image: L'URL d'une image de conteneur sur n'importe quel registre.
* Number of pods: Nombre cible de pods dans lesquels vous souhaitez déployer votre application
* Service (optionnel): exposer un Service sur une adresse IP externe, peut-être publique.
* Namespace: my-apps (créer ce namespace)

Vérifier que le déploiement est OK. Accéder au logs du pods depuis l'interface graphique

## Déploiement de quelques conteneurs et simulation de panne

Depuis les écrans de gestion des conteneurs, lancer quelques conteneurs avec réplication.

Analyser la répartition de la charge, puis couper le worker2 (vagrant halt worker2).

Vérifier que les réplicas ont bien été attribués au worker1.

Redémarrer le worker2, vérifier qu'il reçoit des réplicas.

## Bonus : prometheus/grafana

Installation :

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create ns monitoring
helm -n monitoring install kube-prometheus-stack prometheus-community/kube-prometheus-stack
```

Accès à grafana 

```bash
kubectl -n monitoring port-forward deployment/kube-prometheus-stack-grafana 3000:3000 --address 0.0.0.0      
```

Accéder à 192.168.56.10:3000 depuis votre navigateur.

login = admin, password = prom-operator

Cliquer sur le bouton "+" en haut a droite, puis importer un dashboard. Id du dashboard : 15661

https://grafana.com/grafana/dashboards/15661-k8s-dashboard-en-20250125/

Sélectioner la source Prometheus. Visualiser le dashboard et les différentes métriques.