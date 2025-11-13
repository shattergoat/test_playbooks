# Déploiement OpenNebula via AWX + OneDeploy

Ce projet contient la configuration Ansible pour déployer un OpenNebula
**tout-en-un** (frontend + nœud KVM) sur le serveur `192.168.1.105`
à partir d'AWX, en s'appuyant sur la collection **opennebula.deploy** (OneDeploy).

## Prérequis

- AWX / Ansible Controller accessible
- Serveur OpenNebula cible : Debian 12, IP `192.168.1.105`
  - Accès SSH depuis AWX (clé ou mot de passe)
  - Utilisateur avec sudo (ou root)
- Ports sortants ouverts vers les dépôts OpenNebula / OS

## Structure

- `ansible.cfg` : configuration Ansible pour ce dépôt
- `requirements.yml` : installation de la collection `opennebula.deploy`
- `inventories/opennebula_aio.yml` : inventaire YAML (frontend + node = même host)
- `playbooks/deploy_opennebula_aio.yml` : playbook principal importé par AWX

## Utilisation avec AWX

1. Créer un **Project** AWX pointant sur ce dépôt Git.
   - Cocher l'option d'installation des **collections** à partir de `requirements.yml`
     (Project → SCM → "Ansible Galaxy/Collections" selon la version AWX).

2. Créer un **Inventory** AWX :
   - Type : "Source = SCM" ou "File-based" (suivant version)
   - Chemin vers le fichier : `inventories/opennebula_aio.yml`

3. Créer un **Credential** pour le serveur `192.168.1.105` :
   - Type Machine
   - User = `root` (ou user sudoer)
   - Clé privée ou mot de passe

4. Créer un **Job Template** :
   - Inventory : celui créé au point 2
   - Project : celui de ce dépôt
   - Playbook : `playbooks/deploy_opennebula_aio.yml`
   - Credentials : le Machine credential vers `192.168.1.105`

5. Lancer le job.  
   Si tout est OK, à la fin tu dois avoir :
   - Services OpenNebula actifs sur `192.168.1.105`
   - Sunstone accessible sur `http://192.168.1.105:9869` (par défaut)
