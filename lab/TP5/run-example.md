# Exemple de création d'un pod (nginx) avec dossier monté en volume.

## Préparation data

Sur le worker 1 et 2

```bash
sudo mkdir /mnt/data
sudo sh -c "echo 'Salut depuis /mnt/data :)' > /mnt/data/index.html"
cat /mnt/data/index.html
```

## Création volumes, claims, pod

Sur le master 

```bash
kubectl apply -f storage-class.yaml
kubectl apply -f pv-volume.yaml
kubectl apply -f pv-claim.yaml
kubectl apply -f pv-pod.yaml
```

## Récupération IP et tests

Sur le master

```bash
kubectl get pods -o wide
curl <ip>
```




