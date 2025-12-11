# 🧪 Exercice : Déployer et diagnostiquer des Pods

 L'objectif est d'apprendre à :

* Déployer des Pods à partir d'un fichier YAML
* Lire leurs logs
* Vérifier leur état
* Déboguer un Pod planté

## 🧾 Objectif

* Créer trois Pods : deux fonctionnels et un délibérément cassé.
* Utiliser `kubectl` pour lire les logs, vérifier l'état des Pods, et identifier les erreurs.

---

## 📁 Fichier YAML (`pods-exercice.yaml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-ok-1
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-ok-2
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["sh", "-c", "echo Hello from busybox && sleep 3600"]
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-ko
spec:
  containers:
    - name: fail-container
      image: busybox
      command: ["sh", "-c", "exit 1"]
```

---

## 📌 Étapes à suivre avec `kubectl`

1. **Appliquer le fichier YAML**

```bash
kubectl apply -f pods-exercice.yaml
```

2. **Lister les Pods et vérifier leur état**

```bash
kubectl get pods
```

3. **Vérifier les détails des Pods**

```bash
kubectl describe pod pod-ok-1
kubectl describe pod pod-ko
```

4. **Lire les logs des Pods**

```bash
kubectl logs pod-ok-2
kubectl logs pod-ko
```

5. **Diagnostiquer le Pod en échec**

Questions à se poser :

* Le conteneur du Pod `pod-ko` s'est-il lancé ?
* Y a-t-il un code de sortie anormal ?
* Quelle est la raison du crash indiquée dans `kubectl describe` ?

6. **Corriger le Pod KO (bonus)**
   Modifiez le fichier pour que `pod-ko` exécute `sleep 3600` au lieu de `exit 1`, puis relancez :

```bash
kubectl delete pod pod-ko
# Modifier le fichier YAML ou appliquer dynamiquement avec --force
kubectl apply -f pods-exercice.yaml
```

---

## ✅ Résultats attendus

* `pod-ok-1` et `pod-ok-2` doivent être dans l’état `Running`
* `pod-ko` doit être dans l’état `Error` ou `CrashLoopBackOff`
* Les logs de `pod-ok-2` doivent afficher :

  ```
  Hello from busybox
  ```
* Les logs de `pod-ko` doivent être vides ou indiquer une erreur
