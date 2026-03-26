# Exercice 9 — Déploiement PostgreSQL avec persistance

## Création du cluster

```bash
kind create cluster --config=kind-config.yaml
kubectl config use-context kind-exo-09
```

Création d’un cluster Kind nommé `exo-09`  
Connexion au cluster Kubernetes  

---

## Création du namespace

```bash
kubectl apply -f namespace.yaml
```

Création du namespace `postgres-ns`  
Pour isoler les ressources PostgreSQL  

---

## Mise en place du stockage

```bash
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
```

Résultat :

```
persistentvolume/postgres-pv created
persistentvolumeclaim/postgres-pvc created
```

**PV (PersistentVolume)** : stockage physique (1Gi)  
**PVC (PersistentVolumeClaim)** : demande de stockage (512Mi)  

---

## Création du secret

```bash
kubectl apply -f secret.yaml
```

Résultat :

```
secret/postgres-secret created
```

Stocke :
- utilisateur
- mot de passe
- nom de la base  

Injecté dans le container PostgreSQL  

---

## Déploiement de PostgreSQL

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Résultat :

```
deployment.apps/postgres-deployment created
service/postgres-service created
```

Déploiement du conteneur PostgreSQL  
Utilisation du PVC pour stocker les données  
Service exposé via NodePort  

---

## Vérification du pod

```bash
kubectl get pods -n postgres-ns
```

Résultat :

```
postgres-deployment-6c69b9cc8c-g782f   1/1   Running
```
---

## Vérification du PVC

```bash
kubectl get pvc -n postgres-ns
```

Résultat :

```
postgres-pvc   Bound   ...
```

Le PVC est **Bound** → lié à un volume  
Le stockage est bien attribué  

---

## Vérification du PV

```bash
kubectl get pv
```

Résultat :

```
postgres-pv   Available
pvc-xxxx      Bound
```

Le PV initial est disponible  
Kubernetes a créé un volume lié au PVC  

---

## Vérification du service

```bash
kubectl get services -n postgres-ns
```

Résultat :

```
postgres-service   NodePort   5432:30007/TCP
```

Service exposé sur le port :
- interne : 5432
- externe : 30007  

---

#  Récap 

##  Résumé du tp

### 1. Cluster
Création d’un cluster Kubernetes local avec Kind.

### 2. Namespace
Isolation des ressources PostgreSQL dans `postgres-ns`.

### 3. Stockage
- PV → stockage physique
- PVC → demande de stockage
- Liaison automatique PVC ↔ PV

### 4. Secret
Stockage sécurisé des variables PostgreSQL :
- utilisateur
- mot de passe
- base

### 5. Deployment
- Lance PostgreSQL
- Monte le volume persistant
- Utilise les secrets

### 6. Service
- Permet d’accéder à PostgreSQL
- Exposé via NodePort

---

# Objectifs visés:

PostgreSQL fonctionne  
Les données sont stockées dans un volume persistant  
Les données survivent à la suppression du pod  

---
