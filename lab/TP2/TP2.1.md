# Découverte des commandes de base kubeadm et kubectl

Le but de ce TP est d'étudier les options des commandes kubeadm et kubectl pour être à l'aise avec la suite des TPs.

Nous allons aussi lancer quelques conteneurs pour que les commandes de base renvoient de la donnée à exploiter.

## kubectl cheatsheet

Cette resource nous aidera tout au long des TP pour se rappeler des commandes :

https://kubernetes.io/fr/docs/reference/kubectl/cheatsheet/

## kubeadm help

Sur la machine master, lancer la commande `kubeadm help` et lire la sortie.

Pour obtenir l'aide d'une sous commande, taper `kubeadm help <command>`. Demander l'aide de la commande `config` et  lire la sortie.

`kubeadm help config`

## kubectl help

Répéter les mêmes opérations avec la commande `kubectl`

* `kubectl help`
* `kubectl help run`

## Lancement de quelques conteneurs avec kubectl et utilisation de commandes de base

Lancer un conteneur nginx :

`kubectl run my-nginx --image=nginx`

Monitorer sa création avec :

`watch kubectl get pods`

Cela peut prendre quelques secondes/minutes.

Une fois le conteneur déployé, demander ses informations techniques 

`kubectl describe pods`

Exemples:

```bash
$ kubectl get pods
NAME         READY   STATUS    RESTARTS   AGE
my-nginx     1/1     Running   0          78s
```

```bash
$ kubectl describe pods
Name:         my-nginx
Namespace:    default
Priority:     0
Node:         worker2/10.0.2.15
Start Time:   Mon, 24 Jan 2022 11:51:53 +0000
Labels:       run=my-nginx
Annotations:  <none>
Status:       Running
IP:           10.244.0.3
IPs:
  IP:  10.244.0.3
Containers:
  my-nginx:
    Container ID:   docker://6dcfe09e29a0b7ddf4a5b073290ba3bdf07ef2aa4bf078589cd469e9d2cd3ad2
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 24 Jan 2022 11:52:09 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-sw4zx (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-sw4zx:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  108s  default-scheduler  Successfully assigned default/my-nginx to worker2
  Normal  Pulling    107s  kubelet            Pulling image "nginx"
  Normal  Pulled     93s   kubelet            Successfully pulled image "nginx" in 14.686468098s
  Normal  Created    93s   kubelet            Created container my-nginx
  Normal  Started    92s   kubelet            Started container my-nginx

```

Nous pouvons voir que l'image a été téléchargée et que le conteneur a été créé sur worker2 dans cet exemple.

## Accès au serveur nginx déployé

La commande port-forware permet de donner un accès depuis l'extérieur de la VM master :

```bash
kubectl port-forward pod/my-nginx 9999:80 --address 192.168.56.10
```

Avec votre navigateur, accéder à http://192.168.56.10:9999, vérifier que nginx est bien déployé.


## Suppression du conteneur

Utiliser la fonction help ou l'autocomplétion pour supprimer le conteneur "my-nginx"

Vérifier avec `kubectl get pods` que l'opération s'est bien déroulée.

