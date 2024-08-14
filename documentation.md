# server
## cgi.rs
 Explication des différentes fonctions et structures dans le fichier ``cgi.rs``:
1. **Imports**

    use crate::log; : Importe le module log de votre crate (projet).
    use crate::log::*; : Importe tous les éléments publics du module log.
    use crate::server::path::add_root_to_path; : Importe la fonction add_root_to_path du module path situé dans le module server.
    use crate::server::{get_route, Bytes, ServerConfig, StatusCode}; : Importe les fonctions et structures get_route, Bytes, ServerConfig, et StatusCode du module server.
    use crate::type_aliases::FileExtension; : Importe l'alias de type FileExtension du module type_aliases.
    use http::header::*; : Importe tous les en-têtes HTTP définis dans le module http::header.
    use http::{HeaderMap, HeaderName, HeaderValue, Request, Response}; : Importe les structures HeaderMap, HeaderName, HeaderValue, Request, et Response du module http.
    use std::env; : Importe le module standard env de Rust, qui permet de travailler avec les variables d'environnement.
    use std::process::Command; : Importe la structure Command de la bibliothèque standard, utilisée pour exécuter des processus externes.

2. **enum Cgi**

L'énumération Cgi définit les différents types de scripts CGI (Common Gateway Interface) que le serveur peut exécuter, tels que JavaScript, PHP, Python, et Ruby. Chaque variante représente un langage de programmation.
3. **fn is_cgi_request**

Cette fonction vérifie si un chemin de requête contient "/cgi/", indiquant qu'il s'agit d'une requête CGI.
4. **fn execute_cgi_script**

Cette fonction exécute un script CGI en fonction de la requête et de la configuration du serveur. Voici un résumé de son fonctionnement :

    Récupère la route de la requête.
    Vérifie si les paramètres CGI sont définis.
    Ajoute le chemin racine au chemin de la requête.
    Identifie l'extension du fichier pour déterminer le type de CGI.
    Définit les variables d'environnement nécessaires pour exécuter le script CGI.
    Exécute le script CGI à l'aide de Command::new et capture la sortie.
    Construit et retourne la réponse HTTP en incluant la sortie du script CGI.

5. **fn add_env_variables**

Cette fonction ajoute les variables d'environnement nécessaires pour le script CGI en fonction de la requête et de la configuration du serveur. Elle configure des variables comme QUERY_STRING, REQUEST_METHOD, SERVER_NAME, SERVER_PORT, etc.
6. **fn add_http_variables**

Cette fonction parcourt les en-têtes HTTP de la requête et les ajoute en tant que variables d'environnement (par exemple, HTTP_ACCEPT, CONTENT_LENGTH, CONTENT_TYPE, etc.) si leur valeur n'est pas vide.
7. **fn contains_path_info**

Cette fonction vérifie si le chemin contient des informations supplémentaires après l'extension du fichier. Si le chemin est divisé en deux parties, cela indique qu'il y a des informations sur le chemin.
# handle.rs
Le code que vous avez fourni est une implémentation en Rust pour gérer les connexions HTTP dans un serveur. Le code est assez complet, couvrant divers aspects du traitement des requêtes et des réponses HTTP, de la gestion des routes, de l'exécution des scripts CGI, et du service de contenus de répertoires. Voici une analyse des principales parties du code :
Composants Clés :

1. **Gestion des Requêtes (handle_connection)** :
    - Cette fonction gère les connexions TCP entrantes et traite les requêtes HTTP.
    - Elle commence par analyser la requête HTTP en utilisant la fonction ``parse_http_request``.
    - La route est déterminée, et si la route nécessite une redirection ou rencontre une erreur, elle répond en conséquence.
    - Si la route a un gestionnaire associé, celui-ci est appelé.
    - La fonction gère les demandes de répertoires, l'exécution de scripts CGI, et la diffusion de fichiers statiques.

    2. **Analyse des Requêtes ``(parse_http_request)``** :
    - Cette fonction lit les données entrantes à partir du flux TCP, en séparant l'en-tête de la requête HTTP du corps.
    - Elle lit la requête par morceaux jusqu'à ce qu'elle trouve la fin de la section d'en-tête (indiquée par ``\r\n\r\n``).

    3. **Remplacement de Chemin (replace_path_in_request)**:
    - Cette fonction utilitaire remplace un chemin spécifié dans la requête HTTP par un nouveau chemin, principalement utilisé pour servir des fichiers par défaut lorsque le chemin demandé est un répertoire.

    4. **Service des Réponses (``serve_response``)** :
    - Cette fonction écrit une réponse HTTP dans le flux TCP. Elle gère les écritures partielles et s'assure que toute la réponse est envoyée au client.

    5. **Service des Contenus de Répertoires (``serve_directory_contents``)** :
    - Si le chemin demandé est un répertoire et que la liste des répertoires est activée, cette fonction génère une page HTML listant toutes les entrées du répertoire.
    - Elle crée des liens hypertextes pour chaque fichier et sous-répertoire dans le répertoire.

    6. **Tests** :
        Les tests fournis vérifient la validité de la fonction replace_path_in_request, en s'assurant qu'elle se comporte comme prévu lorsqu'elle remplace des chemins dans les requêtes HTTP.

**Considérations** :

- Gestion des Erreurs :
    - Le code tente de gérer les erreurs de manière élégante en retournant des codes d'état HTTP appropriés et en enregistrant les erreurs.

- Exécution de Scripts CGI :
    - Les scripts CGI sont gérés en vérifiant si la requête est destinée à une ressource CGI, puis en exécutant le script. Le serveur doit être configuré correctement pour supporter l'exécution des CGI de manière sécurisée.

- Liste de Répertoires :
    - La fonctionnalité de liste de répertoires peut représenter un risque de sécurité si elle n'est pas correctement contrôlée, en particulier si des fichiers sensibles sont exposés. Assurez-vous que la liste de répertoires n'est activée que pour des chemins non sensibles.

- Améliorations :

- Concurrence :
    - Envisagez d'ajouter la prise en charge de la gestion de plusieurs connexions simultanément en utilisant des IO asynchrones ou des pools de threads.

- Sécurité :
    - Mettez en œuvre des mesures de sécurité supplémentaires, telles que la validation des entrées, pour prévenir les attaques par traversée de répertoires ou d'autres vulnérabilités.

- Journalisation :
    - Améliorez la journalisation pour inclure des informations plus détaillées sur les requêtes et les erreurs, ce qui peut être utile pour le débogage et la surveillance.

Ce code offre une base solide pour un serveur HTTP de base en Rust, avec des possibilités d'amélioration et d'optimisation supplémentaires.
# methods.rs
Le code fourni est une implémentation en Rust pour gérer différentes méthodes HTTP (GET, POST, PUT, PATCH, DELETE, etc.) dans un serveur HTTP. Voici une explication détaillée de chaque section du code :
1. **Importations**

Le code commence par importer plusieurs modules et bibliothèques nécessaires pour la gestion des requêtes HTTP, des réponses, des méthodes, des chemins de fichiers, et de la journalisation.
2. **Fonctions Générales**

- ``get_method(req: &str) -> Result<Method, StatusCode> ``:
        Cette fonction extrait la méthode HTTP (comme GET, POST, etc.) d'une requête HTTP. Si la méthode n'est pas valide, elle retourne un code de statut HTTP BAD_REQUEST.

- `` method_is_allowed(method: &Method, route: &Route) -> bool`` :
        Vérifie si la méthode HTTP spécifiée est autorisée pour la route donnée.

- ``handle_method(route: &Route, req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode> ``:
        Cette fonction gère l'appel de la méthode appropriée en fonction de la méthode HTTP reçue dans la requête. Les méthodes "sûres" (comme GET, OPTIONS) et "non sûres" (comme POST, DELETE) sont traitées séparément.

3. **Module safe**

Ce module gère les méthodes HTTP dites "sûres", c'est-à-dire celles qui ne modifient pas l'état du serveur.

- ``get(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode>`` :
        Gère les requêtes GET en lisant le fichier correspondant au chemin demandé et en retournant son contenu avec un code de statut OK. Si le fichier n'est pas trouvé, un code de statut NOT_FOUND est retourné.

- ``head(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode>`` :
        Gère les requêtes HEAD en retournant les mêmes en-têtes qu'une requête GET, mais sans le corps du message.

- ``trace(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode>`` :
        Gère les requêtes TRACE en renvoyant la requête reçue comme corps de réponse, après avoir mis à jour l'en-tête Via et vérifié l'en-tête Max-Forwards.

- ``options(route: &Route, req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode>`` :
        Gère les requêtes OPTIONS en retournant les méthodes HTTP autorisées pour la route demandée.

4. **Module not_safe**

Ce module gère les méthodes HTTP dites "non sûres", c'est-à-dire celles qui peuvent modifier l'état du serveur.

- ``post(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode>`` :
        Gère les requêtes POST en écrivant le corps de la requête dans un nouveau fichier sur le serveur. Si le fichier existe déjà, il est renommé pour éviter les conflits.

- ``put(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode>`` :
        Gère les requêtes PUT en écrivant le contenu de la requête dans le fichier spécifié, remplaçant tout contenu existant.

- ``patch(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode> ``:
        Gère les requêtes PATCH en modifiant un fichier existant avec le contenu de la requête.

    delete(req: &Request<Bytes>, config: &ServerConfig) -> Result<Response<Bytes>, StatusCode> :
        Gère les requêtes DELETE en supprimant le fichier ou répertoire spécifié sur le serveur.

**Conclusion**

Ce code est bien structuré pour gérer les différentes méthodes HTTP dans un serveur Rust, avec une séparation claire entre les méthodes sûres et non sûres. Chaque méthode HTTP est gérée de manière appropriée, avec des vérifications d'erreurs et des réponses HTTP conformes. Cela constitue une bonne base pour un serveur HTTP en Rust.
# requests.rs
Votre code Rust implémente diverses fonctions utilitaires pour l'analyse des requêtes HTTP, notamment l'extraction de la méthode HTTP, de la version, du chemin, des en-têtes et du corps. Voici un aperçu des composants clés :
1. **Gestion des Requêtes (get_request)**

- Extraction de la Version : Extrait la version HTTP de l'en-tête de la requête en utilisant la fonction ``version::get_version``.
- Extraction du Chemin : Récupère le chemin de la requête à partir de l'en-tête en utilisant ``path::get_path``.
- Extraction de la Méthode : Extrait la méthode HTTP avec ``super::get_method``.
- Traitement des En-têtes : Parcourt les en-têtes, les formate et les ajoute au constructeur de la requête.
- Gestion du Corps : En fonction de l'encodage de transfert (chunked ou non), le corps est traité avec ``body::get_chunked_body`` ou ``body::get_body``.

2. **Gestion des Chemins (path module)**

- Extraction du Chemin : get_path extrait le chemin demandé de la requête.
- Correspondance des Chemins : path_exists vérifie si le chemin demandé correspond à l'un des itinéraires du serveur. Il prend en charge les correspondances exactes et les correspondances de chemin racine.
- Ajout du Chemin Racine : ``add_root_to_path`` ajoute un répertoire racine au chemin demandé si cela est spécifié dans les paramètres de l'itinéraire.

3. **Gestion des Versions (version module)**

- Analyse de la Version : get_version associe la chaîne de version de la requête aux versions HTTP prises en charge, et journalise les erreurs pour les versions non prises en charge.

4. **Gestion des En-têtes (headers module)**

- Extraction des En-têtes : ``get_headers`` extrait tous les en-têtes de la requête, à l'exception de la ligne de requête HTTP.
- Vérification de l'Encodage de Transfert : ``is_chunked`` vérifie si l'en-tête Transfer-Encoding est défini sur "chunked".
- Formatage des En-têtes : ``format_header`` analyse et formate les en-têtes individuels.

5. **Gestion du Corps (body module)**

    Corps Régulier : get_body récupère le corps, en s'assurant qu'il ne dépasse pas une limite de taille spécifiée.
    Corps Fragmenté : get_chunked_body traite l'encodage de transfert par fragments (chunked), en analysant et en reconstruisant le corps tout en respectant la limite de taille.

6. Fonctions Utilitaires (utils module)

- Index de Séparation : ``get_split_index`` récupère un index spécifique d'une chaîne séparée par des espaces.
- Récupération de Ligne : ``get_line`` récupère une ligne spécifique d'une chaîne séparée par CRLF.

**Tests**

    Vous avez des tests pour chacun des modules, assurant le bon fonctionnement de fonctionnalités telles que l'analyse, le formatage des en-têtes, la gestion du corps, et l'extraction du chemin.

Le code est modulaire et bien structuré pour une implémentation de serveur HTTP personnalisé, gérant efficacement divers aspects du traitement des requêtes. Les tests offrent également un bon niveau de validation pour les fonctionnalités principales, facilitant ainsi l'identification et la correction des problèmes.
# responses.rs
Votre code Rust implémente plusieurs fonctionnalités essentielles pour un serveur HTTP personnalisé, telles que la gestion des réponses HTTP, la définition du type de contenu, et la gestion des redirections et des erreurs. Voici un résumé des principales fonctions et modules :
1. **format_response**

    Cette fonction formate une réponse HTTP en convertissant ses parties en une séquence d'octets (Bytes). Elle gère les en-têtes et le corps de la réponse. Si le corps est codé en morceaux ("chunked"), il sera formaté en conséquence, sinon le corps entier est simplement ajouté à la réponse.

2. **is_chunked**

    Cette fonction vérifie si une réponse HTTP utilise le codage par morceaux en regardant l'en-tête Transfer-Encoding.

3. **content_type**

    Cette fonction détermine le type MIME d'un fichier basé sur son extension. Elle retourne une chaîne représentant le type de contenu (content-type) qui sera utilisée dans les en-têtes HTTP.

4. **Module informational**

    Ce module contient une fonction informational qui génère une réponse HTTP d'information (1xx). Elle inclut des en-têtes tels que Host et la version HTTP.

5. Module redirections

- Le module redirections gère les redirections HTTP. La fonction redirect crée une réponse de redirection (3xx) avec les en-têtes Location et Host.
- La fonction is_redirect vérifie si un chemin donné doit être redirigé en fonction des redirections définies dans la configuration du serveur.

6. Module errors

- Le module errors gère la génération de réponses d'erreur HTTP. La fonction error crée une réponse HTTP avec un code d'état d'erreur (4xx ou 5xx) et, si disponible, un corps d'erreur personnalisé ou un corps généré par défaut.
   `` generate_error_html`` crée une page HTML simple pour afficher les messages d'erreur.
    check_errors vérifie si un fichier HTML personnalisé pour l'erreur existe dans le chemin spécifié par la configuration du serveur, sinon il génère une réponse d'erreur par défaut.

**Fonctionnalités supplémentaires** :

    Traitement des en-têtes et du corps : Le code gère les en-têtes HTTP de manière efficace, y compris la conversion en chaîne et la vérification des encodages.
    Gestion des types de contenu : Le type de contenu est automatiquement déterminé par rapport aux extensions de fichiers, ce qui permet de servir correctement une grande variété de fichiers.

Ce code est bien organisé et suit une approche modulaire, ce qui facilite la maintenance et l'évolution du serveur HTTP.
# routes.rs
Cette fonction get_route en Rust est utilisée pour récupérer la route associée à une requête HTTP donnée, tout en effectuant plusieurs vérifications importantes pour s'assurer que la requête peut être correctement traitée. Voici une explication détaillée :
**Fonction get_route**
- **Entrées**:

    - ``req: &Request<Bytes> ``: La requête HTTP que le serveur a reçue, encapsulée dans un type Request avec un corps de type Bytes.
    - ``config: &ServerConfig`` : La configuration du serveur, qui inclut les routes disponibles et d'autres paramètres.

- **Sorties** :

    - Retourne un Result contenant soit :
        - ``Ok(Route<'a>)`` : La route associée à l'URI demandée si tout est valide.
        - ``Err((StatusCode, String))`` : Une erreur avec un code d'état HTTP et un message, si la route n'est pas valide ou si la requête ne peut pas être traitée.

- Étapes principales de la fonction :

    - Récupération du chemin de l'URI :
        La fonction extrait le chemin de l'URI de la requête en utilisant req.uri().path().

    - Vérification de l'existence du chemin :
        - La fonction vérifie si le chemin demandé existe parmi les routes configurées dans le serveur via la fonction path_exists.
        - Si le chemin n'existe pas, une erreur 404 (Not Found) est retournée, et un message d'erreur est enregistré dans les logs du serveur.

    - Vérification des redirections :
        - Si la route a des paramètres définis (route.settings), la fonction vérifie si le chemin demandé doit être redirigé en utilisant is_redirect.
        - Si une redirection est nécessaire, la fonction retourne une erreur avec le code de redirection approprié (par défaut 307 Temporary Redirect).

    - Vérification de la méthode HTTP :
        - La fonction vérifie si la méthode HTTP utilisée dans la requête (GET, POST, etc.) est autorisée pour la route demandée via method_is_allowed.
        - Si la méthode n'est pas autorisée, une erreur 405 (Method Not Allowed) est retournée, avec un message d'erreur enregistré dans les logs du serveur.

    **Retour de la route** :
        Si toutes les vérifications sont réussies, la fonction retourne la route associée à l'URI demandée.

- Utilité :

   - **Routage dynamique** : Cette fonction permet de gérer dynamiquement les requêtes HTTP en les associant aux routes configurées sur le serveur. Elle vérifie également les redirections et les méthodes autorisées, ce qui contribue à la robustesse du serveur.
  - Gestion des erreurs : Les vérifications effectuées permettent de capturer et de gérer différentes erreurs courantes, comme les chemins non trouvés ou les méthodes non autorisées, avant qu'elles n'affectent le traitement de la requête.

En résumé, cette fonction joue un rôle crucial dans le fonctionnement du serveur HTTP en s'assurant que chaque requête est correctement routée ou rejetée avec le code d'état approprié.
# sessions.rs
Ce code Rust gère la manipulation des cookies dans un serveur HTTP. Voici une explication détaillée de chaque fonction et de leur rôle dans la gestion des cookies.
**Fonction update_cookie**

- Entrées :
  - ``req : &Request<Bytes> ``: Requête HTTP contenant potentiellement des cookies.
  - ``conf : &ServerConfig`` : Configuration du serveur, notamment l'hôte (host).
  - Sortie : Retourne un ``Result<Response<Bytes>, StatusCode>``, représentant soit une réponse HTTP avec une mise à jour de cookie, soit un code d'état HTTP en cas d'erreur.
- Description :
   - Cette fonction vérifie si la requête contient un cookie spécifique (grit:lab=cookie).
   - Si le cookie est trouvé, il est supprimé en appelant la fonction remove_cookie.
   - Sinon, un nouveau cookie est ajouté à la réponse via la fonction set_cookie.
        - Dans les deux cas, la fonction retourne une réponse HTTP avec le cookie ajouté ou supprimé.

**Fonction validate_cookie**

- Entrées :
       - ``req : &Request<Bytes> ``: Requête HTTP contenant potentiellement des cookies.
       - ``conf : &ServerConfig ``: Configuration du serveur.
- Sortie : Retourne un Result<Response<Bytes>, StatusCode>, représentant soit une réponse HTTP validée, soit un code d'état HTTP (401 Unauthorized) si le cookie n'est pas trouvé ou non valide.
    - Description :
        - Cette fonction vérifie si un cookie spécifique (grit:lab=cookie) est présent dans la requête.
        - Si le cookie est trouvé et valide, la fonction retourne une réponse HTTP confirmant la validité du cookie.
        - Si le cookie n'est pas trouvé, une réponse 401 Unauthorized est renvoyée.

**Fonction cookie_demo**

 - Entrées :
    - req : &Request<Bytes> : Requête HTTP.
    - config : &ServerConfig : Configuration du serveur.
- Sortie : Retourne un Result<Response<Bytes>, StatusCode>, représentant soit une réponse HTTP avec une page HTML, soit un code d'état HTTP (404 Not Found) si le fichier HTML n'est pas trouvé.
    - Description :
        - Cette fonction lit un fichier HTML (cookie-demo.html) depuis le système de fichiers et le retourne comme réponse HTTP.
        - La réponse inclut des en-têtes standard et potentiellement les en-têtes trouvés dans la requête, pour les en-têtes définis comme sûrs (STANDARD_HEADERS).

**Fonction set_cookie**

 - Entrées :
    - resp : Builder : Constructeur de réponse HTTP.
    - value : &Cookie : Valeur du cookie à définir.
- Sortie : Retourne un Builder modifié avec l'en-tête SET_COOKIE ajoutant un cookie.
    - Description :
        - Cette fonction ajoute un cookie à la réponse avec une durée de vie définie (Max-Age de 3600 secondes).

**Fonction remove_cookie**

- Entrées :
  - resp : Builder : Constructeur de réponse HTTP.
  - value : &Cookie : Valeur du cookie à supprimer.
- Sortie : Retourne un Builder modifié avec l'en-tête SET_COOKIE pour supprimer le cookie.
    - Description :
        Cette fonction ajoute un en-tête SET_COOKIE à la réponse pour supprimer le cookie en définissant sa date d'expiration à une date passée.

**Fonction cookie**

- Entrées :
  - resp : Builder : Constructeur de réponse HTTP.
  - value : &Cookie : Valeur du cookie à ajouter.
- Sortie : Retourne un Builder modifié avec l'en-tête COOKIE.
    - Description :
        Cette fonction ajoute un cookie aux en-têtes de réponse.

**Fonction get_cookie**

- Entrées :
   - req : &Request<Bytes> : Requête HTTP.
   - value : &Cookie : Valeur du cookie à rechercher.
- Sortie : Retourne une Option<&HeaderValue>, qui est soit le cookie trouvé, soit None si le cookie n'est pas trouvé.
    - Description :
        Cette fonction recherche un cookie spécifique dans les en-têtes de la requête HTTP et retourne ce cookie s'il est trouvé.

**Conclusion**

Ces fonctions permettent de manipuler les cookies dans un serveur HTTP en Rust. Elles fournissent des mécanismes pour ajouter, supprimer et valider des cookies dans le cadre des requêtes et réponses HTTP, tout en gérant les erreurs potentielles associées.

# start.rs
Ce code Rust gère le démarrage d'un serveur HTTP en utilisant des configurations spécifiées. Voici un aperçu détaillé des différentes fonctions et modules, ainsi qu'une explication des tests unitaires associés :
**Fonction start**

- Entrées :
  - ``configs: Vec<ServerConfig<'static>> ``: Liste des configurations du serveur à démarrer.

    - Sortie : Aucun retour, la fonction démarre le serveur et entre dans une boucle de gestion des événements.

    - Description :
        - Cette fonction reçoit une liste de configurations de serveur et utilise get_servers pour créer des instances de serveurs basés sur ces configurations.
        - Si aucun serveur n'est créé, le programme affiche un message d'erreur et se termine.
        - Sinon, elle initialise l'état du serveur avec ServerState::init et entre dans une boucle infinie pour poller et gérer les événements des serveurs.

**Fonction bind_port**

 - Entrées :
     - host: &str : Nom d'hôte ou adresse IP du serveur.
        - port: &Port : Port sur lequel le serveur doit écouter.

- Sortie : Retourne une Option<TcpListener>, représentant le listener TCP si le port a été correctement lié, ou None en cas d'erreur.

    - Description :
        - La fonction tente de résoudre l'adresse et le port fournis en utilisant ToSocketAddrs.
        - Si l'adresse est résolue correctement, elle essaie de créer un TcpListener pour cette adresse.
        - En cas de succès, elle retourne le listener. En cas d'échec, elle affiche un message d'erreur.

**Fonction get_servers**

- Entrées :
       - ``configs: Vec<ServerConfig<'static>> ``: Liste des configurations du serveur.

    - Sortie : Retourne un ``Vec<Server<'static>>``, représentant les serveurs créés à partir des configurations fournies.

    - Description :
        Cette fonction parcourt les configurations de serveur, vérifie si des ports sont spécifiés et tente de lier chaque port en utilisant bind_port.
        Si des ports sont liés avec succès, elle crée une instance de Server et l'ajoute à la liste des serveurs.
        Elle retourne la liste des serveurs créés.

**Module de tests**

 - Test test_bind_port :
      - Vérifie que bind_port retourne None pour une adresse invalide et un port invalide.
        - Utilise init_logs() pour initialiser les journaux avant les tests (probablement pour configurer les messages d'erreur et les informations de débogage).

- Test test_get_servers :
        Vérifie que get_servers retourne une liste vide lorsqu'aucun port n'est spécifié dans la configuration du serveur.

**Conclusion**

Le code est bien structuré pour gérer le démarrage et la configuration des serveurs HTTP. Les tests permettent de vérifier le comportement de liaison des ports et de création des serveurs avec des configurations valides et invalides.

Les tests sont simples mais efficaces pour garantir que les fonctions bind_port et get_servers se comportent comme prévu dans des situations typiques et atypiques.
# stats.rs
Ce code Rust définit et gère l'état du serveur HTTP, y compris la gestion des connexions et la surveillance des événements. Voici une vue d'ensemble des différentes parties du code et une explication des fonctions :
**Structures et Fonctions Principales**
- Structure Connection

    - Champs :
        - stream: TcpStream : La connexion TCP avec le client.
        - ``config: Arc<ServerConfig<'a>>`` : La configuration du serveur associée à cette connexion.
        - ``last_activity``: Instant : La dernière fois que l'activité a été détectée sur cette connexion.

    - Fonction new :
        Crée une nouvelle instance de Connection avec un TcpStream et une configuration de serveur.

- Structure ServerState

   - Champs :
        - ``poll: Poll ``: Instance de Poll pour surveiller les événements I/O.
        - ``events: Events`` : Structure pour stocker les événements I/O.
        - ``token_id: usize`` : Identifiant de token pour les connexions.
        - ``listeners: Vec<Listener<'a>>`` : Liste des écouteurs pour les serveurs.
        - ``connections: HashMap<Token, Connection<'a>> ``: Carte des connexions actives, associée à des tokens.

    - Fonction init :
        - Initialise l'état du serveur avec les serveurs spécifiés.
        - Enregistre tous les écouteurs avec le Poll et les ajoute à la liste des écouteurs.

    - Fonction poll :
        - Effectue un poll pour surveiller les événements I/O.
        - Appelle handle_timeout pour gérer les connexions expirées.

    - Fonction handle_events :
        Gère les événements I/O en acceptant de nouvelles connexions et en traitant les connexions existantes.

    - Fonction handle_timeout :
        Supprime les connexions qui ont expiré du HashMap.

**Fonction accept_connection**

 - Entrées :
     - poll: &Poll : Instance de Poll pour enregistrer la nouvelle connexion.
     - token_id: &mut usize : Identifiant de token à incrémenter pour chaque nouvelle connexion.
        - listener: &Listener<'a> : Écouteur à partir duquel la connexion est acceptée.
        - connections: &mut HashMap<Token, Connection<'a>> : Carte des connexions actives.

- Sortie : Retourne true si une connexion a été acceptée avec succès, sinon false.

    - Description :
        - Accepte une nouvelle connexion et l'enregistre dans Poll.
        - Définit les options de linger pour la connexion.
        - Configure le TTL pour le stream.

**Fonction handle_existing_connection**

- Entrées :
    - poll: &Poll : Instance de Poll pour gérer la connexion.
    - token: Token : Token de la connexion à traiter.
    - connections: &mut HashMap<Token, Connection> : Carte des connexions actives.

    - Description :
        - Traite les données de la connexion existante.
        - Enregistre ou désinscrit la connexion en fonction des erreurs rencontrées.

**Fonction set_linger_option**

- Entrées :
    - stream: &TcpStream : Stream TCP à configurer.
        - linger_duration: Option<Duration> : Durée de linger à définir.

- Sortie : Retourne un std::io::Result<()>.

    - Description :
       -  Définit l'option de linger sur le stream TCP.
       -  Utilise des traits spécifiques au système d'exploitation pour accéder au descripteur de socket et définir les options.

**Module de Tests**

Le code n'inclut pas de tests explicites dans ce fichier, mais les tests unitaires pourraient inclure :

 - Vérification de la gestion des connexions et de l'état des sockets.
 - Validation des options de linger sur différents systèmes d'exploitation.

**Remarques**

    La gestion des erreurs est en place pour les opérations importantes, comme l'enregistrement des écouteurs et des connexions.
    Les connexions expirées sont nettoyées pour éviter les fuites de ressources.
    Les options spécifiques au système d'exploitation, comme set_linger_option, montrent une attention à la portabilité et à la configuration fine des connexions réseau.

Ce code est bien structuré pour gérer les connexions serveur de manière efficace et robuste, en prenant en compte des aspects spécifiques des systèmes d'exploitation pour la gestion des sockets.