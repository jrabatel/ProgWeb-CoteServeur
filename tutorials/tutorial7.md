---
title: TD4 &ndash; Cookies & sessions
subtitle: Panier et préférences
layout: tutorial
---

<!--
Explication au tableau :
schéma avec ce que fait le PHP avant d'exécuter le fichier

techniques : 2 actions lire et écrire/màj/supprimer (sans garantie)

scénarios:
- 1ère écriture cookie, pas dans $_COOKIE
- màj cookie, pas màj dans $_COOKIE

Rq: 
- décalage dans le temps
- peut stocker plusieurs cookies
- écrire sur $_COOKIE n'a aucun effet
- stocker tableau
- Si je ne refait pas de setcookie, est-ce que le cookie reste chez l'utilisateur ?
  Oui, car pas de setcookie = pas d'action sur le cookie (sauf si expiration)
-->

<!--
On peut se faire passer pour quelqu'un si on connait son PHPSESSID
=> HTTPS et paramétrisation des cookies par nom de domaine et chemin
=> Fixation de session si session_id par query string ou faille XSS

-->

HTTP est un protocole de communication avec lequel chaque requête-réponse est
indépendante l'une de l'autre. Du coup, le serveur n'a pas de moyen de
reconnaître un client particulier, et donc n'a pas de moyen d'enregistrer
d'informations liées à un client spécifique. Par exemple, avec le HTTP de base,
si vous allez plusieurs fois sur Facebook, et bien le réseau social ne sait pas
reconnaître quelles requêtes viennent de vous parmi toutes les requêtes qu'il
reçoit, et donc il ne peut pas vous connecter, vous afficher votre page
personnelle...

Pour remédier à cela, HTTP prévoit le mécanisme des cookies qui permet
d'enregistrer des informations sur l'ordinateur du client. De plus, en utilisant
des cookies pour identifier ses clients, les serveurs PHP peuvent stocker côté
serveur des informations spécifiques à un client : c'est le mécanisme des
sessions.

## Les cookies

Un cookie est utilisé pour stocker quelques informations spécifiques à un
utilisateur comme :

* ses préférences sur un site (personnalisation de l'interface, ...),
* le contenu de son panier d'achat électronique,
* son identifiant de session (voir prochain TD sur les sessions).

Les informations sont envoyées par le site (serveur HTTP) en même temps que la page
Web. Le client stocke ces informations sur sa machine dans un fichier appelé
*cookie* : il s'agit d'une table d'associations
nom/valeur.  
**Attention** : il ne faut pas stocker de données critiques dans les cookies car
elles sont stockées à la vue de tous chez le client !

 
### Déposer un cookie

Les cookies sont des informations stockées sur l'ordinateur du client (par le navigateur) à
l'initiative du serveur.

#### Comment déposer un cookie en PHP ?

D'un point de vue pratique en PHP, on dépose un cookie à l'aide de la fonction
[`setcookie`](http://php.net/manual/fr/function.setcookie.php). Par exemple, la
ligne ci-dessous crée un cookie nommé `TestCookie` contenant la valeur `"OK"`
et qui expire dans 1h.

```php?start_inline=1
setcookie("TestCookie", "OK", time()+3600);  /* expire dans 1 heure = 3600 secondes */
```


#### Que fait `setcookie` ? Comment fait le serveur pour demander au client d'enregistrer un cookie ?

D'un point de vue technique, voici ce qui se passe au niveau du protocole HTTP
(dont nous avons notamment parlé lors 
[du cours 1]({{site.baseurl}}/classes/class1.html#protocole-de-communication--http)).
Pour stocker des informations dans un cookie chez le client, le serveur écrit
des lignes `Set-Cookie` dans l'en-tête de sa réponse HTTP

```http
HTTP/1.1 200 OK
Date:Thu, 22 Oct 2015 15:43:27 GMT
Server: Apache/2.2.14 (Ubuntu)
Accept-Ranges: bytes
Content-Length: 5781
Content-Type: text/html
Set-Cookie: TestCookie1=valeur1; expires=Thu, 22-Oct-2015 16:43:27 GMT; Max-Age=3600
Set-Cookie: TestCookie2=valeur2; expires=Thu, 22-Oct-2015 16:43:27 GMT; Max-Age=3600

<html><head>...
```

Remarquons que le serveur écrit une ligne `Set-Cookie` par paire nom/valeur. Ici
nous avons un cookie `"TestCookie1"` de valeur `"valeur1"` et un cookie
`"TestCookie2"` de valeur `"valeur2"`.


**Attention :** la fonction `setcookie()` doit être appelée avant tout écriture
de la page HTML. Le protocole HTTP impose cette restriction.  
**Pourquoi ?** *(Optionnel)* Le Set-Cookie est une information envoyé dans
l'en-tête de la réponse. Le corps de la réponse HTTP, c'est-à-dire la page
HTML, doit être envoyée après son en-tête. Or PHP écrit et envoie la page HTML
dans le corps de la réponse HTTP au fur et à mesure.

C'est le navigateur qui stocke (ou pas) le cookie sur l'ordinateur du client. De
manière générale, le serveur n'a aucune garantie sur le comportement du client :
le cookie pourrait ne pas être enregistré (cookie désactivé chez l'utilisateur),
ou être altéré (valeur modifiée, date d'expiration changée ...).

<!-- The Max-Age attribute defines the lifetime of the  cookie, in seconds. -->
<!-- The Expires attribute indicates the maximum lifetime of the cookie, -->
<!-- represented as the date and time at which the cookie expires. -->
**Référence :** [La RFC des cookies](https://tools.ietf.org/html/rfc6265)

### Récupérer un cookie

Après qu'un cookie a été déposé chez un client par le serveur par l'opération
précédente, le navigateur du client envoie les informations du cookie à chaque
requête HTTP.

#### Comment le client transmet-il les informations de ses cookies ?

D'un point de vue technique, voici ce qui se passe au niveau du protocole HTTP.
Le client envoie les informations de ses cookies dans l'en-tête de ses
requêtes.


```http
GET /~rletud/index.html HTTP/1.1
Host: webinfo.iutmontp.univ-montp2.fr
Cookie: TestCookie1=valeur1; TestCookie2=valeur2
```

#### Comment le serveur peut-il lire en PHP les cookies envoyés par le client ?

Le PHP traite juste l'information reçue pour la rendre
disponible dans la variable
[`$_COOKIE`](http://php.net/manual/fr/reserved.variables.cookies.php) de la même
manière de `$_GET` récupère l'information dans l'URL et que `$_POST` récupère
l'information dans le corps de la requête (*cf.* [le cours
1]({{site.baseurl}}/classes/class1.html#protocole-de-communication--http)).

Par exemple,

```php?start_inline=1
echo $_COOKIE["TestCookie1"];
```

devrait afficher `valeur1`.

#### Les cookies sont associés à des noms de domaines

Nous avons précédemment dit que le client envoie ses cookies à chaque requête
HTTP. Mais heureusement le navigateur n'envoie pas tous ses cookies à tous les
sites. Déjà, le nom de domaine du site est enregistré en même temps que les
cookies pour se souvenir de leur provenance. Le comportement
normal d'un navigateur est d'envoyer tous les cookies provenant des
sous-domaines du domaine de la page Web qu'il demande.

Par exemple, un cookie enregistré à l'initiative d'un site hébergé sur
`webinfo.iutmontp.univ-montp2.fr` (nom de domaine `univ-montp2.fr`) sera
disponible à tous les sites ayant ce nom de domaine, donc en particulier aux
pages de `webinfo.iutmontp.univ-montp2.fr`, `iutmontp.univ-montp2.fr` ou
`univ-montp2.fr`, mais pas aux autres domaines tels que `google.fr`.

Il est possible de préciser ce comportement en donnant plus de paramètres (nom
de domaine, chemin) à la fonction
[`setcookie`](http://php.net/manual/fr/function.setcookie.php).


### Effacer un cookie

Enfin pour effacer un cookie, il suffit de le faire expirer (en lui mettant une
date d'expiration inférieure à la date courante).

```php?start_inline=1
setcookie ("TestCookie", "", time() - 1);
```

C'est alors le navigateur du client qui se charge (normalement) de supprimer les
cookies périmés chez le client. Encore une fois, le serveur n’a aucune garantie
sur le comportement du client.

### Notes techniques 

1. La taille d'un cookie est limité à 4KB (car les en-têtes HTTP doivent être <4KB).

1. **Important** : Les cookies ne peuvent contenir que du texte, donc *a priori*
   pas des objets PHP. **Cependant** la fonction
   [`serialize`](http://php.net/manual/fr/function.serialize.php) de PHP permet
   de transformer n'importe quelle valeur PHP en texte que l'on pourra donc
   stocker dans le cookie. Il faudra donc appliquer l'opération inverse
   `unserialize` lorsque l'on récupère le cookie.

   <!-- https://stackoverflow.com/questions/1969232/allowed-characters-in-cookies -->
   <!-- Quels caractères peuvent contenir les cookies ? alphanumérique ? ASCII ? -->

1. Pour voir les cookies déposés sur votre navigateur, voici comment faire :

   * sous Firefox, allez dans les outils développeurs (avec `F12`) &#8594; Onglet
     Stockage (ou Application) &#8594; Cookies.
   * sous Chrome, allez dans les outils développeurs (avec `F12`) &#8594; Onglet
     Ressources (ou Application) &#8594; puis Storage et Cookies.

1. Si vous ne spécifiez pas le temps d'expiration d'un cookie (3ème paramètre de
   `setcookie`) ou que vous le mettez à `0` alors le cookie sera supprimé à la
   fermeture du navigateur.

### Exercice sur l'utilisation des cookies


<div class="exercise">

Créez un nouveau fichier PHP et vérifiez votre compréhension en implémentant les
fonctionnalités suivantes :

1. Écrivez un cookie,
1. Vérifiez que le cookie a bien été écrit à l'aide des outils de développement
   du navigateur,
1. Lisez le cookie en PHP,
1. Supprimez le cookie en PHP.
1. Stockez un tableau dans un cookie et récupérez-le.

**Aide :** Si vous ne savez pas stocker un tableau dans un cookie, c'est que
  vous avez lu trop rapidement la partie "Notes techniques".

</div>

## Les sessions 

Les sessions sont un mécanisme basé sur les cookies qui permet de stocker des
informations non plus du côté du client mais sur le serveur.  Le principe des
sessions est d'identifier les clients pour que le serveur puisse stocker des
informations liées à chacun d'entre eux.

Pour faire ceci, la seule information stockée chez le client dans les cookies
est un identifiant unique (par défaut dans la variable nommée `PHPSESSID`).
Lorsqu'il demande une page, le client envoie son cookie contenant son
identifiant (dans sa requête HTTP).

Le serveur stocke de son côté des informations liées à chaque client. En
utilisant le cookie contenant l'identifiant, le serveur peut reconnaître quel
client est en train de demander une page Web et ainsi récupérer les informations
propre à ce client.

<div class="centered">
<img alt="Schéma des sessions" src="{{site.baseurl}}/assets/session.png" width="60%">
</div>

### Opérations sur les sessions

On peut stocker presque tout dans une variable de session : un chiffre, un
texte, voir un tableau ou un objet. Contrairement aux cookies, il n'est même pas
nécessaire d'utiliser `serialize()` pour convertir tous les types en texte.

Présentons maintenant les opérations fondamentales sur les sessions :

*  **Initialiser les sessions**

   ```php?start_inline=1
   session_start();
   ```

   <!-- session_name("chaineUniqueInventeParMoi");  // Optionnel : voir section 3.2 -->

   [`session_start()`](http://php.net/manual/fr/function.session-start.php)
   démarre une nouvelle session ou reprend une session existante. Cette fonction
   est donc indispensable pour se servir des sessions (et donc pouvoir utiliser
   `$_SESSION`).

   **Attention :** Il faut mettre <!-- `session_name()` avant `session_start()`
     et --> `session_start()` avant toute écriture de code HTML dans la page
     pour la même raison qu'il faut mettre `setcookie()` avant les mêmes
     écritures (on doit écrire l'en-tête HTTP avant d'envoyer le corps de la
     réponse HTTP).

*  **Lire une variable de session**

   ```php?start_inline=1
   echo $_SESSION['login'];
   ```

   À la manière de `$_GET`, `$_POST` et `$_COOKIE`, la variable `$_SESSION`
   permet de lire les informations de sessions (stockées sur le disque dur du
   serveur et liées à l'identifiant de session).

*  **Mettre une variable en session**

   ```php?start_inline=1
   $_SESSION['login'] = 'remi';
   ```

   **Nouveauté :** Contrairement à `$_GET`, `$_POST` et `$_COOKIE`, la variable
   `$_SESSION` est aussi une variable d'écriture : elle permet d'écrire des
   informations de sessions.

*  **Vérifier qu'une variable existe en session**

   ```php?start_inline=1
   if (isset($_SESSION['login'])) { /*do something*/ }
   ```
   
*  **Supprimer une variable de session**

   ```php?start_inline=1
   unset($_SESSION['login']);
   ```

   Comme la variable `$_SESSION` est aussi en écriture, alors le fait de vider
   l'un de ses champs fera que ce champ sera aussi effacé des données de session
   sur le disque dur.

*  **Suppression complète d'une session**

   ```php?start_inline=1
   session_unset();     // unset $_SESSION variable for the run-time 
   session_destroy();   // destroy session data in storage
   // Il faut réappeler session_start() pour accéder de nouveau aux variables de session
   setcookie(session_name(),'',time()-1); // deletes the session cookie containing the session ID
   ```

   Pour le dire autrement :

   * `session_unset()` vide le tableau `$_SESSION` en faisant
   `unset($_SESSION['name_var'])` pour tous les champs `name_var` de
   `$_SESSION`,
   * `session_destroy()` supprime le fichier de données associées à la session
   courante qui étaient enregistrées sur le disque dur du serveur,
   * `setcookie` demande au client de supprimer son cookie de session (sans garantie).

### Notes techniques

#### Avantages des sessions

Par rapport aux cookies, les sessions offrent plusieurs avantages. Il n'y a plus
de limite de taille sur les données stockées côté client. 

Mais surtout, l'utilisateur ne peut plus tricher en éditant lui même le contenu
du cookie. Imaginons par exemple que l'on note si l'utilisateur est
administrateur dans la variable `isAdmin` d'un cookie. Alors rien n'empêche
l'utilisateur de modifier son cookie en passant le champ `isAdmin` à la valeur
`true`. Cependant, avec le mécanisme de sessions, l'utilisateur n'a pas accès
aux données qui lui sont associées.


#### Expiration des sessions

1. **Durée de vie d'une session :**

   Par défaut, une session est supprimée à la fermeture du navigateur. Ceci est dû
   au délai d'expiration du cookie de l'identifiant unique `PHPSESSID` qui est par
   défaut `0`. Or nous avons vu dans la section sur les cookies que cela entraîne
   l'expiration du cookie à la fermeture du navigateur.

	Vous pouvez changer cela en modifiant la variable de configuration
	`session.cookie_lifetime` qui gère le délai d'expiration du cookie. La
	fonction
	[`session_set_cookie_params()`](http://php.net/manual/en/function.session-set-cookie-params.php)
	permet de régler facilement cette variable.

	Ceci peut être utile si vous souhaitez que votre panier stocké avec des
	sessions soit conservé disons 30 minutes, même en cas de fermeture du
	navigateur.

<!--
Note technique :

Si les cookies ne sont pas utilisés, le PHPSESSID peut être passé en GET (et en
POST ?)
Alors il y a un risque plus important de "session fixation"
cf http://defeo.lu/aws/lessons/session-fixation
-->

#### Où sont stockées les sessions ?

Les sessions sont stockées sur le disque dur du serveur web, par exemple avec
LAMP dans le dossier `/var/lib/php/sessions` (il faut être `root` pour y
accéder). Reportez-vous à la partie `session` de la fonction `phpinfo()` pour
connaître ce chemin.

**Exemple :** Si mon identifiant de session est `PHPSESSID=aapot` et si mon code
PHP est le suivant

```php?start_inline=1
session_start();
$_SESSION['login'] = "rlebreton";
$_SESSION['isAdmin'] = "1";
```

alors le fichier `/var/lib/php/sessions/sess_aapot` contient

```
login|s:9:"rlebreton";isAdmin|s:1:"1";
```


### Exercice sur les sessions

<div class="exercise">

Créez un nouveau fichier PHP et vérifiez votre compréhension en implémentant les
fonctionnalités suivantes :

1. Démarrer une session,
1. Écrire des variables de session de différents types (chaînes de caractères,
   tableaux, objets, ...),
1. Lire une variable de session,
1. Supprimer une variable de session,
1. Supprimer complètement une session

</div>

Pour approfondir votre compréhension des cookies et des sessions, vous avez
aussi accès aux [notes complémentaires à ce
sujet]({{site.baseurl}}/assets/tut7-complement).
