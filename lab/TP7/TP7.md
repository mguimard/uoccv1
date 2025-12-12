# TP7 : Contrôler le trafic réseau avec les règles Ingress et Egress dans Kubernetes

## Objectif

Comprendre et appliquer des **NetworkPolicies** dans Kubernetes pour contrôler les flux **ingress** (entrant) et **egress** (sortant) entre les pods.

---

## Contexte

Votre cluster Kubernetes est déjà prêt. Vous allez :

- Déployer deux namespaces : `frontend` et `backend`.
- Déployer une application simple dans chaque namespace (serveur + client).
- Appliquer progressivement des règles réseau pour contrôler les communications.

> **Important :** Assurez-vous que votre cluster a un CNI compatible avec les NetworkPolicies (comme Calico, Cilium, etc). Sinon, les règles ne seront pas appliquées.

---

## Préparation de l’environnement

### Créez deux namespaces

```bash
kubectl create namespace frontend
kubectl create namespace backend
````

### Déployez une application serveur dans `backend`

```yaml
# backend-deploy.yaml
apiVersion: v1
kind: Pod
metadata:
  name: backend
  namespace: backend
  labels:
    app: backend
spec:
  containers:
  - name: web
    image: nginx
    ports:
    - containerPort: 80
```

```bash
kubectl apply -f backend-deploy.yaml
```

### Déployez un client dans `frontend`

```yaml
# frontend-client.yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
  namespace: frontend
  labels:
    app: frontend
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]
```

```bash
kubectl apply -f frontend-client.yaml
```

Exposer frontend et backend avec un service

```bash
kubectl expose pod backend --port 80 --namespace backend
```

---

## Tester la communication entre pods

Depuis le pod `frontend`, tentez de contacter le serveur `backend` :

```bash
kubectl exec -n frontend frontend -- wget -qO- backend.backend.svc.cluster.local
```

Cela devrait fonctionner au début.

---

## Créer une règle Ingress restrictive

Créez une **NetworkPolicy** qui bloque tout trafic entrant vers le pod `backend`.

```yaml
# deny-all-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
  namespace: backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
```

Appliquez-la :

```bash
kubectl apply -f deny-all-ingress.yaml
```

### Question :

* Que se passe-t-il si vous relancez la commande `wget` depuis le pod `frontend` ?

---

## Créer une règle Ingress autorisant uniquement `frontend`

Ajoutez maintenant une règle plus fine qui **autorise uniquement** le pod `frontend` à communiquer avec le pod `backend`.

```yaml
# allow-frontend.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
  namespace: backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
```

> Pour que cette règle fonctionne, ajoutez un label au namespace `frontend` :

```bash
kubectl label namespace frontend name=frontend
```

---

## Créer une règle Egress

Ajoutez une règle Egress dans le namespace `frontend` qui **bloque toute sortie**, sauf vers le namespace `backend`.

```yaml
# egress-policy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-egress-backend
  namespace: frontend
spec:
  podSelector:
    matchLabels:
      app: frontend
  policyTypes:
  - Egress
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: backend
```

N'oubliez pas de labeler le namespace `backend` :

```bash
kubectl label namespace backend name=backend
```

La commande wget ne devrait plus fonctionner avec le nom de domaine `backend.backend.svc.cluster.local`. C'est tout à fait logique, car le traffic sortant vers le serveur DNS est bloqué. Vérifier que notre pod frontend peut communiquer verts le backend avec son ip.

Modifier la règle egress pour autoriser le traffic vers le DNS.

```yaml
# egress-policy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-egress-backend
  namespace: frontend
spec:
  podSelector:
    matchLabels:
      app: frontend
  policyTypes:
  - Egress
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: backend
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: kube-system
      podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
```

Appliquer à nouveau la règle et vérifier que la commande wget fonctionne.

---

## Questions de révision

1. Quelle est la différence entre `Ingress` et `Egress` ?
2. Une `NetworkPolicy` agit-elle comme un pare-feu ou comme une ACL ?
3. Que se passe-t-il si **aucune** `NetworkPolicy` n’existe dans un namespace ?
4. Une `NetworkPolicy` sans règle `egress` bloque-t-elle tout le trafic sortant ?
5. Peut-on filtrer par labels de **pods** et de **namespaces** ? Pourquoi est-ce utile ?
