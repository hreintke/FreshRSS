# Les pré-requis sur le serveur

FreshRSS est un logiciel développé en PHP reposant sur le modèle client - serveur. C'est-à-dire qu'il vous faudra un serveur web pour en profiter. Ensuite, FreshRSS ne demande pas une configuration très fournie et peut donc, en théorie, tourner sur la plupart des serveurs mutualisés.

Il est toutefois de votre responsabilité de vérifier que votre hébergement permettra de faire tourner FreshRSS avant de nous taper dessus. Dans le cas où les informations listées ci-dessous ne seraient pas à jour, vous pourrez.

 | Logiciel         | Recommandé                                                                                                     | Fonctionne aussi avec          |
 | --------         | -----------                                                                                                    | ---------------------          |
 | Serveur web      | **Apache 2**                                                                                                   | Nginx                          |
 | PHP              | **PHP 5.3.7+**                                                                                                 | PHP 5.2+                       |
 | Modules PHP      | Requis : libxml, cURL, PDO_MySQL, PCRE et ctype \\ Requis (32 bits seulement) : GMP \\ Recommandé : JSON, Zlib, mbstring et iconv, ZipArchive |                                |
 | Base de données  | **MySQL 5.0.3+**                                                                                               | SQLite 3.7.4+                  |
 | Navigateur       | **Firefox**                                                                                                    | Chrome, Opera, Safari or IE 9+ |

## Note importante

FreshRSS **PEUT** fonctionner sur la version de PHP 5.3.3. En effet, nous utilisons des fonctions spécifiques pour la connexion par formulaire et notamment la bibliothèque ''password_compat''. Celle-ci est compatible avec PHP >= 5.3.7 ou certaines versions plus anciennes incluant un patch spécifique. Cela dépend de la distribution :

*  CentOS et la Red Hat Enterprise Linux 6.5 sont supportés.
*  En revanche, **Debian avec PHP 5.3.3 n'est pas supporté !** ([Plus d'informations](https://github.com/ircmaxell/password_compat#requirements))

# Choisir la bonne version de FreshRSS

FreshRSS possède trois versions différentes (nous parlons de branches) qui sortent à des fréquences plus ou moins rapides. Aussi prenez le temps de comprendre à quoi correspond chacune de ces versions.

## La version stable

[Téléchargement](https://github.com/FreshRSS/FreshRSS/archive/master.zip)

Cette version sort lorsqu'on considère qu'on a répondu à nos objectifs en terme de nouvelles fonctionnalités. Deux versions peuvent ainsi sortir de façon très rapprochée si les développeurs travaillent bien. En pratique, comme nous nous fixons de nombreux objectifs et que nous travaillons sur notre temps libre, les versions sont souvent assez espacées (plusieurs mois). Son avantage est que le code est particulièrement stable et vous ne devriez pas faire face à de méchants bugs.

## La version de développement

[Téléchargement](https://github.com/FreshRSS/FreshRSS/archive/dev.zip)

Comme son nom l'indique, il s'agit de la version sur laquelle les développeurs travaillent. **Elle est donc totalement instable !** Si vous souhaitez recevoir les améliorations au jour le jour, vous pouvez l'utiliser, mais attention à bien suivre les évolutions sur Github (via [le flux RSS de la branche](https://github.com/FreshRSS/FreshRSS/commits/dev.atom) par exemple). On raconte que les développeurs principaux l'utilisent quotidiennement sans avoir de soucis. Sans doute savent-ils ce qu'ils font…

# Installation sur Apache

**TODO**

Cette partie n'a pas encore été écrite. Néanmoins, comme il s'agit d'une bête application PHP, cela ne pose généralement pas de soucis à installer :)

# Installation sur Nginx

Voici un fichier de configuration pour nginx. Il couvre la configuration pour http, https et php.

_Vous pourrez trouver d'autres fichiers de configuration plus simples mais ces derniers ne seront peut-être pas compatibles avec l'API FreshRSS._

```
server {
  listen 80; # http sur le port 80
  listen 443 ssl; # https sur le port 443

  # configuration https 
  ssl on;
  ssl_certificate      /etc/nginx/server.crt;
  ssl_certificate_key  /etc/nginx/server.key;

  # l'url ou les urls de votre serveur
  server_name example.com rss.example.com;

  # le répertoire où se trouve le dossier p de FreshRSS
  root /srv/FreshRSS/p/;

  index index.php index.html index.htm;

  # les fichiers de log nginx
  access_log /var/log/nginx/rss.access.log;
  error_log /var/log/nginx/rss.error.log;

  # gestion des fichiers php
  # il est nécessaire d'utiliser cette expression régulière pour le bon fonctionnement de l'API 
  location ~ ^.+?\.php(/.*)?$ {
    fastcgi_pass unix:/var/run/php5-fpm.sock;
    fastcgi_split_path_info ^(.+\.php)(/.*)$;
    # Par défaut la variable PATH_INFO n'est pas définie sous PHP-FPM
    # or l'API FreshRSS greader.php en a besoin. Si vous avez un "Bad Request", vérifiez bien cette dernière !
    fastcgi_param PATH_INFO $fastcgi_path_info;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
  }
  
  location / {
    try_files $uri $uri/ index.php;
  }
}
```

Pour un tutoriel pas à pas, vous pouvez suivre [cet article dédié](http://www.pihomeserver.fr/2013/05/08/raspberry-pi-home-server-installer-un-agregateur-de-flux-rss-pour-remplacer-google-reader/).

# Conseils de sécurité

**TODO**
