---
title:  Démarrage du projet Web
subtitle: Planning et cahier des charges 
layout: tutorial
---

## Sujet

Vous devrez compléter et affiner le site de covoiturage que vous avez construit 
tout au long des séances de TD.

## Modalités

Le projet est dimensionné pour des groupes de 2 étudiants.

Le 27/03, date de présentation de votre projet, **le site devra être déployé sur `webinfo`** sous l'un
de vos comptes. Les sources du site devront être accessibles dans un fichier
`sources.zip` à la racine de votre site.

### Ce que ce projet n'est pas :

* Un site vitrine joli avec beaucoup d'images par exemple. Vous n'aurez pas de
  points supplémentaires sur cet aspect.
* Un site réaliste qui implémente `X` fois la même fonctionnalité. Le but est
  de réaliser correctement un maximum de fonctionnalités différentes.

### Où héberger ce site? Comment partager un répertoire ?

Le site à rendre sera à héberger dans le répertoire de l'un des membres de votre groupe. 
Par exemple [http://webinfo.iutmontp.univ-montp2.fr/~mon_login/covoiturage/](http://webinfo.iutmontp.univ-montp2.fr/~mon_login/covoiturage/)

L'étudiant 1 doit donc créer le répertoire `covoiturage` dans son dossier `public_html`
puis donner les droits aux autres étudiants de son groupe sur ce répertoire:

* `setfacl -m u:loginetudiant2:x /home/ann2/loginetudiant1` (droit de
     lister le repertoire personnel)
* `setfacl -m u:loginetudiant2:x /home/ann2/loginetudiant1/public_html` (droit de
     lister le repertoire public_html)
* `setfacl -R -m u:loginetudiant2:rwx covoiturage` (donne récursivement les droits
à tout les fichiers inclus dans covoiturage)
* `setfacl -R -m d:u:loginetudiant2:rwx covoiturage` (défini des droits par
défaut : les nouveaux fichiers créés prendront ces droits)

**Référence :**
  [La page Côté Technique > Site Web > Partager public_html de intradepinfo](https://iutdepinfo.iutmontp.univ-montp2.fr/index.php/cote-technique/site-web/partager-publichtml)

## Critères de notation

Le but pédagogique de ce projet est de mettre en application toutes les
techniques que vous avez apprises lors des TDs.

Voici donc les critères les plus importants :

### Fonctionnalités prioritaires
* Utilisation de l'architecture MVC pour l'ensemble des pages du site (voir TD5),
* Chaque page doit contenir une barre de navigation, que vous remplirez avec des liens vers les pages principales du site (voir TD5),
* Toutes les requêtes SQL contenant au moins un paramètre doivent être préparées (voir TD3),
* La page à la racine du site (index.php), doit rediriger vers la **liste des trajets** (voir TD5).

* Pages requises du site :

    * **Voitures**
        * Liste des voitures (chaque voiture a un lien vers sa page de détail),
        * Détail d'une voiture,
        * Page de création d'une voiture.


    * **Trajets**
        * Liste des trajets (chaque trajet a un lien vers sa page de détail),
        * Détail d'une trajet : inclut notamment la liste des utilisateur du trajet (TD3).

* *Remarque* :  Chacune des pages du site doit s'intégrer dans l'architecture MVC, c'est-à-dire passant par un routeur qui redirige l'utilisateur vers l'action d'un contrôleur. Il vous faudra un contrôleur par type d'objet (par exemple, `ControllerVoiture`, `ControllerTrajet`).
Le routeur prendra, en plus du paramètre GET `action`, un paramètre GET `controller`. Le routeur pourra diriger l'utilisateur sur la bonne page avec le code `$controller::$action();`. *Attention* : ce code n'est pas sécurisé !

### Fonctionnalités secondaires
Les fonctionnalités ci-dessous sont considérées comme secondaires, finissez en priorité tous les points précédents !

* Pages supplémentaires :
    * *Utilisateurs*
        * Liste des utilisateurs (chaque utilisateur a un lien vers sa page de détail),
        * Détail d'un utilisateur (inclut notamment la liste des trajets d'un utilisateur, et la liste des trajets dont il est conducteur).
f
* Les exercices complémentaires des TDs en rapport avec le covoiturage (les exercices du type *Si le temps vous le permet...*) sont considérés comme des exercices secondaires. Par exemple, désinscrire un utilisateur d'un trajet (TD3) rentre dans cette catégorie.

### Bonus
Les TDs supplémentaires facultattifs (architecture MVC avancée, Authentification et validation par email) ne sont pas attendus pour ce projet et seraient donc considérés comme du bonus.

### Modalités
Il n'y a **pas** de rapport à écrire, ni de présentation à préparer. Vous devrez
juste répondre à une série de questions sur votre code qui permettra
d'évaluer ce qui a été implémenté.

L'ordre de passage des groupes sera tiré aléatoirement.

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
