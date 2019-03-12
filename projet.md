---
title:  Démarrage du projet Web
subtitle: Planning et cahier des charges 
layout: tutorial
---

## Sujet

Vous devrez terminer et affiner le site de covoiturage que vous avez construit 
tout au long des TD.

## Modalités

Le projet est dimensionné pour des groupes de 2 étudiants.

Le 27/03, date de présentation de votre projet, **le site devra être déployé sur `webinfo`** sous l'un
de vos comptes. Les sources du site devront être accessible dans un fichier
`sources.zip` à la racine de votre site.

### Ce que ce projet n'est pas :

* Un site vitrine joli avec beaucoup d'images par exemple. Vous n'aurez pas de
  points supplémentaires sur cet aspect.
* Un site réaliste qui implémente `X` fois la même fonctionnalité. Le but est
  de réaliser correctement un maximum de fonctionnalités différentes.

### Où héberger ce site? Comment partager un répertoire ?

Le site à rendre sera à héberger dans le répertoire de l'un des membres de votre groupe. 
Par exemple [http://webinfo.iutmontp.univ-montp2.fr/~mon_login/eCommerce/](http://webinfo.iutmontp.univ-montp2.fr/~mon_login/covoiturage/)

L'étudiant 1 doit donc créer le répertoire `covoiturage` dans son dossier `public_html`
puis donner les droits aux autres étudiants de son groupe sur ce répertoire:

* `setfacl -m u:loginetudiant2:x /home/ann2/loginetudiant1` (droit de
     lister le repertoire personnel)
* `setfacl -m u:loginetudiant2:x /home/ann2/loginetudiant1/public_html` (droit de
     lister le repertoire public_html)
* `setfacl -R -m u:loginetudiant2:rwx covoiturage` (donne récursivement les droits
à tout les fichiers inclus dans eCommerce)
* `setfacl -R -m d:u:loginetudiant2:rwx covoiturage` (défini des droits par
défaut : les nouveaux fichiers créés prendront ces droits)

**Référence :**
  [La page Côté Technique > Site Web > Partager public_html de intradepinfo](https://iutdepinfo.iutmontp.univ-montp2.fr/index.php/cote-technique/site-web/partager-publichtml)

## Critères de notation

Le but pédagogique de ce projet est de mettre en application toutes les
techniques que vous avez apprises lors des TDs. Voici donc [les critères sur
lesquels vous serez notés](https://docs.google.com/spreadsheets/d/1oUd7fe0K8WZhI2TPRRvgZ2xPZf5H22CUvlpcXEMD3Ao/edit#gid=0).

* Utilision d'un cadre MVC pour l'ensemble des pages

Il n'y a **pas** de rapport à écrire, ni de présentation à préparer. Vous devrez
juste répondre à une série de questions sur votre code qui nous permettra
d'évaluer ce qui a été implémenté.

<!-- [Planning des soutenances]() -->

<!--
### Front-Office 

**Rappel:** le projet portant sur la programmation côté serveur, la partie de la note correspondante au design HTML/CSS est faible. 

1. HTML / CSS valides et séparés
3. Problème d’encodage: problème avec les accents dans les textes fixes ou issues de la BD.
4. W3C (plus de 10 erreurs  / 1-2 erreurs / aucune erreur)
5. Factorisation code (aucune / include header+footer / include content)

Pourquoi les items suivants ?
3. utilisation de `<div>` pour la mise en page
2. CSS responsive


### Gestion des formulaires Formulaire (de contacts ou autre)

2. Vérification des données en HTML5 ou Javascript
3. Vérification des données en PHP
4. Re-Remplissage du formulaire en cas d'erreur de saisie.


### Gestion des  utilisateurs

1. mail confirmation pour l'inscription

2. différents niveaux: admin/users

### Gestion du panier 
1. Cookie 

### Back-office

1. Utilisation des sessions: 
2. Message bienvenue
3. Sécurisation de quelques pages (manuellement)
4. Sécurisation de toutes les pages (automatisé via le controleur)

### CRUD

Produits:
Ajout / Suppression  / Modification

Relations annexes nécesitant une jointure (genre accesoires):
Ajout / Suppression  / Modification

### MVC 

1. Vues liste / liste paginée / détail 

2. Critères visant à évaluer la qualité de votre MVC: (to be completed)

Aucun code HTML hors des vues

Aucun SQL hors du modèle 

### Qualité de la démonstration 

-->
