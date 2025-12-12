## 🧠 Quiz : Bases de Kubernetes & `kubectl`

### Question 1

**Qu’est-ce qu’un Pod dans Kubernetes ?**

* [ ] Une machine virtuelle
* [ ] L’unité de déploiement la plus petite dans Kubernetes
* [ ] Un cluster de conteneurs
* [ ] Un ou plusieurs conteneurs partageant le même réseau et stockage

---

### Question 2

**Quelle commande permet de lister tous les pods dans un namespace spécifique ?**

* [ ] `kubectl get pods -n <namespace>`
* [ ] `kubectl list pods`
* [ ] `kubectl pods get --namespace <namespace>`
* [ ] `kubectl get all -n pods`

---

### Question 3

**Quels objets Kubernetes sont utilisés pour exposer un Pod en externe ?**

* [ ] Service
* [ ] Volume
* [ ] Pod
* [ ] ConfigMap

---

### Question 4

**Que fait la commande suivante ?**

```bash
kubectl apply -f deployment.yaml
```

* [ ] Applique une configuration définie dans un fichier YAML
* [ ] Supprime les ressources définies dans le fichier
* [ ] Crée ou met à jour les ressources Kubernetes
* [ ] Exécute un Pod temporaire

---

### Question 5

**Quelle commande permet d'obtenir des informations détaillées sur un Pod ?**

* [ ] `kubectl logs pod-name`
* [ ] `kubectl describe node pod-name`
* [ ] `kubectl describe pod pod-name`
* [ ] `kubectl show pod pod-name`

---

### Question 6

**Quels types de Services Kubernetes existent ?**

* [ ] ClusterIP
* [ ] NodePort
* [ ] LoadBalancer
* [ ] ExternalNamePort

---

### Question 7

**Quel fichier contient généralement la définition d’un déploiement dans Kubernetes ?**

* [ ] deployment.yaml
* [ ] config.txt
* [ ] service.json
* [ ] deploy.yml

---

### Question 8

**Quelles sont les responsabilités du `kubelet` ?**

* [ ] Superviser les Pods sur un nœud
* [ ] Gérer le réseau du cluster
* [ ] Compiler les fichiers YAML
* [ ] Communiquer avec l’API Server

---

### Question 9

**Que retourne la commande `kubectl get nodes` ?**

* [ ] La liste des nœuds du cluster
* [ ] La liste des Pods sur chaque nœud
* [ ] L’état des conteneurs
* [ ] Les métriques de CPU et mémoire

---

### Question 10

**Comment supprimer un Pod nommé `test-pod` ?**

* [ ] `kubectl delete pod test-pod`
* [ ] `kubectl remove pod test-pod`
* [ ] `kubectl destroy pod test-pod`
* [ ] `kubectl pod delete test-pod`
