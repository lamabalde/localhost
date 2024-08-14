# Correction Audits
1. **Comment fonctionne un serveur HTTP ?**

Un serveur HTTP est responsable de la gestion des requêtes des clients (navigateurs web, par exemple) et de la fourniture de réponses appropriées. Voici une vue d'ensemble de son fonctionnement :

- ``Écoute des connexions entrantes`` : Le serveur écoute les connexions entrantes sur des ports spécifiés en utilisant des sockets.
- ``Acceptation des connexions`` : Lorsqu'une connexion est acceptée, le serveur crée un- TcpStream pour communiquer avec le client.
- ``Traitement des requêtes`` : Le serveur lit la requête HTTP du client, la traite (par exemple, en accédant à des fichiers ou en exécutant des scripts), puis prépare une réponse HTTP.
- ``Envoi des réponses`` : Le serveur envoie la réponse HTTP au client et ferme la connexion si nécessaire.

2. **Quelle fonction a été utilisée pour le multiplexage des E/S et comment fonctionne-t-elle ?**

Le code utilise la fonction poll de la bibliothèque mio pour le multiplexage des E/S. Cette fonction est essentielle pour gérer les connexions simultanées de manière efficace.

- ``Poll::poll`` : Cette fonction bloque jusqu'à ce qu'un ou plusieurs événements se produisent sur les descripteurs de fichiers surveillés. Elle permet au serveur de surveiller plusieurs connexions en même temps et de traiter les événements associés, tels que les données disponibles en lecture ou les connexions acceptées.

3. **Le serveur n'utilise-t-il qu'une seule sélection (ou équivalent) pour lire les demandes du client et les réponses en écriture ?**

Oui, le serveur utilise une seule instance de Poll pour surveiller toutes les connexions simultanées. Cette instance de Poll permet au serveur de surveiller les événements de lecture et d'écriture pour plusieurs descripteurs de fichiers (sockets) en même temps.
4. **Pourquoi est-il important de n'utiliser qu'un seul choix et comment a-t-il été réalisé ?**

L'utilisation d'une seule instance de Poll est importante pour plusieurs raisons :

- ``Efficacité`` : Elle permet de gérer de nombreuses connexions de manière efficace en évitant la surcharge liée à la gestion individuelle des descripteurs de fichiers.
- ``Scalabilité`` : Elle aide à supporter un grand nombre de connexions simultanées sans créer de threads ou de processus supplémentaires pour chaque connexion.

Cela a été réalisé en créant une instance de Poll dans la fonction ``ServerState::init`` et en utilisant cette instance pour surveiller tous les écouteurs et les connexions actives.
5. **Lire le code qui va de la sélection (ou équivalent) à la lecture et à l'écriture d'un client, y a-t-il seulement une lecture ou une écriture par client par sélection (ou équivalent) ?**

Le code montre que chaque itération du loop de poll traite à la fois les connexions entrantes et les connexions existantes. Voici comment cela fonctionne :

- Lecture et écriture : Pour chaque événement, le code appelle accept_connection pour accepter les nouvelles connexions et ``handle_existing_connection`` pour gérer les connexions déjà établies. ``handle_existing_connection`` gère à la fois la lecture et l'écriture des données en fonction des événements disponibles.

6. **Les valeurs de retour pour les fonctions d'E/S sont-elles correctement vérifiées ?**

Oui, les valeurs de retour des fonctions d'E/S sont vérifiées :

- Les erreurs lors de l'appel à accept_connection et handle_existing_connection sont correctement journalisées en utilisant log!.
- Les erreurs lors de l'enregistrement ou de la désinscription des sockets sont également gérées et enregistrées.

7. **Si une erreur est renvoyée par les fonctions précédentes sur un socket, le client est-il supprimé ?**

Oui, si une erreur est rencontrée lors du traitement d'une connexion, comme une erreur de lecture ou d'écriture, la connexion est supprimée :

- Dans ``handle_existing_connection``, si une erreur est rencontrée et qu'elle n'est pas ``ErrorKind::WouldBlock``, la connexion est désinscrite du Poll et supprimée du HashMap des connexions.

8. **Écrire et lire TOUJOURS-IL-TOUJOURS-IL-S-IL-S-IL-T-ÉCAL--**

Le code doit assurer que les opérations d'écriture et de lecture sont traitées correctement :

- Lecture : La fonction handle_existing_connection gère la lecture des données du client.
- Écriture : Les réponses aux clients sont envoyées en écrivant sur le ``TcpStream``.

En résumé, le code utilise un modèle de multiplexage efficace avec Poll, gère les erreurs et assure que les connexions sont correctement traitées. La gestion unique des descripteurs de fichiers permet une gestion efficace et scalable des connexions réseau.