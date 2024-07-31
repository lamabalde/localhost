Objectifs du Projet

    Implémentation en Rust :
        Utilisez Rust pour coder le serveur HTTP.
        Vous pouvez utiliser des fonctions de la caisse libc pour des appels système comme epoll, mais évitez les caisses qui implémentent déjà des fonctionnalités de serveur comme tokio ou nix.
        Vous devrez peut-être utiliser le mot-clé unsafe pour certaines opérations, mais avec prudence.

    Fonctionnalités du Serveur :
        Stabilité : Le serveur ne doit jamais planter.
        Gestion des délais d'attente : Les demandes doivent expirer si elles prennent trop de temps.
        Multithreading : Le serveur doit pouvoir écouter sur plusieurs ports et gérer plusieurs instances simultanément avec un seul processus et un seul thread.
        Conformité HTTP/1.1 : Le serveur doit respecter le protocole HTTP/1.1.
        Méthodes HTTP : Le serveur doit supporter au moins les méthodes GET, POST, et DELETE.
        Téléchargements de fichiers : Il doit pouvoir recevoir des fichiers envoyés par le client.
        Gestion des cookies et des sessions : Implémentation de la gestion des cookies et des sessions.
        Pages d'erreur : Créez des pages d'erreur personnalisées pour les codes d'erreur 400, 403, 404, 405, 413, et 500.
        Epoll : Utilisez epoll ou une API équivalente pour chaque communication client/serveur, avec des opérations d'E/S non bloquantes.
        Requêtes fragmentées et non fragmentées : Gérez les deux types de requêtes.
        Statut des réponses : Définissez le bon statut pour chaque réponse.

    CGI (Common Gateway Interface) :
        Implémentez un CGI pour exécuter des fichiers selon leur extension (par exemple, .php ou .py).
        Vous pouvez créer un nouveau processus pour exécuter le CGI.
        Le CGI doit être appelé avec le fichier comme premier argument et EOF comme fin du corps.
        Assurez-vous de gérer correctement les chemins relatifs.

    Fichier de Configuration :
        Serveurs et Ports : Spécifiez l'hôte, les ports et les serveurs dans le fichier de configuration.
        Pages d'erreur : Définissez les chemins pour les pages d’erreur personnalisées.
        Taille des téléchargements : Limitez la taille des corps de client pour les téléchargements.
        Routes : Configurez les routes avec les méthodes HTTP acceptées, redirections, répertoires/fichiers à rechercher, fichiers par défaut, CGI, et liste des répertoires.
        Pas de commentaires : Les commentaires dans le fichier de configuration ne sont pas nécessaires.

    Tests :
        Tests de stress : Utilisez siege pour tester la disponibilité du serveur. Assurez-vous qu'il reste disponible avec un taux de disponibilité d'au moins 99,5%.
        Tests exhaustifs : Créez des tests pour divers scénarios (redirections, erreurs de configuration, pages statiques et dynamiques, etc.).
        Fuites de mémoire : Vérifiez les fuites de mémoire avant de soumettre le projet.
        Préparation des tests : Vous pouvez utiliser n'importe quel langage pour écrire les tests, tant qu'ils sont exhaustifs et vérifiables.

Points Clés pour la Mise en Œuvre

    HTTP/1.1 Conformité : Assurez-vous que le serveur est totalement conforme aux spécifications du protocole HTTP/1.1.
    Epoll et E/S Non Bloquantes : Utilisez epoll pour gérer les E/S de manière non bloquante afin de maintenir les performances du serveur.
    Configuration Flexible : Le fichier de configuration doit permettre une gestion souple des routes, des méthodes HTTP, et des fichiers CGI.