
# TP4 : Tolérance à la panne dans un cluster Kubernetes

## Objectif

Comprendre et tester le comportement de Kubernetes en cas de panne d’un nœud (`worker2`) en utilisant les objets **ReplicaSet** et **DaemonSet**.

## Contexte

Vous disposez d’un cluster Kubernetes composé de 3 nœuds :

- `master`
- `worker1`
- `worker2`

Vous allez :

1. Déployer un ReplicaSet avec plusieurs pods.
2. Déployer un DaemonSet avec un pod par nœud.
3. Simuler une panne de `worker2`.
4. Observer le comportement du cluster et la tolérance à la panne.

---

## Étapes de l'exercice

### Créer un ReplicaSet

Créez un fichier `deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deploment
metadata:
  name: nginx-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
````

Appliquez le manifeste :

```bash
kubectl apply -f deployment.yaml
```

### Créer un DaemonSet

Créez un fichier `daemonset.yaml` :

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemon-nginx
spec:
  selector:
    matchLabels:
      app: daemon-nginx
  template:
    metadata:
      labels:
        app: daemon-nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

Appliquez le manifeste :

```bash
kubectl apply -f daemonset.yaml
```

Vérifiez la distribution des pods du DaemonSet :

```bash
kubectl get pods -o wide -l app=daemon-nginx
```

Vous devriez avoir un pod sur chaque nœud (`master`, `worker1`, `worker2`).

---

## Simuler une panne d'un pod

Lister les pods et récupérer le nom d'un des pods nginx

```bash
kubectl get pods -o wide
```

Tuer un pod

```bash
kubectl delete pods <podname>
```

Vérifier que les 4 réplicas sont toujours disponibles.

---

## Simuler une panne du nœud `worker2`

Simulez une panne en arrêtant le service kubelet ou éteignez le nœud dans votre environnement :

**Option A - Stopper kubelet sur le worker2:**

```bash
sudo systemctl stop kubelet
```

**Option B - Utiliser vagrant pour arreter la VM:**

```bash
# Sur la machine hôte 
vagrant halt worker2
# Pour le redémarrer plus tard
vagrant up worker2
```

Attendez quelques minutes (le délai de détection dépend du paramétrage de l’API server, généralement ~5 min).

---

##  Observer le comportement du cluster

### Questions :

1. Que devient le pod du DaemonSet prévu sur `worker2` ?
2. Que deviennent les pods du ReplicaSet initialement programmés sur `worker2` ?
3. Les pods sont-ils redéployés ailleurs ?
4. Combien de pods du ReplicaSet sont en état `Running` après la panne ?
5. Que se passe-t-il si vous relancez `worker2` ?


Si les pods ne sont que sur le worker1, il est possible de rebalancer la charge

```bash
kubectl rollout restart deployment nginx-deployment 
```

Vérifier que le worker2 a bien récupérer des pods.

### Commandes utiles :

```bash
kubectl get nodes
kubectl describe nodes worker2
kubectl get pods -o wide
kubectl get rs
```
