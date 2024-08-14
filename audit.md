# Audit Localhost
Localhost, c'est créer votre propre serveur HTTP et le tester avec un navigateur réel.
Prenez le temps nécessaire pour comprendre le projet et le tester, l'examen du code source aidera beaucoup.
**Fonctionnel**

L'étudiant est-il en mesure de justifier ses choix et d'expliquer ce qui suit: Remarque: Demandez à l'étudiant de vous montrer l'implémentation dans le code source si nécessaire.
Comment fonctionne un serveur HTTP?
Quelle fonction a été utilisée pour le multiplexage des E/S et comment fonctionne-t-elle?
Le serveur n'utilise-t-il qu'une seule sélection (ou équivalent) pour lire les demandes du client et les réponses en écriture?
Pourquoi est-il important de n'utiliser qu'un seul choix et comment a-t-il été réalisé?
Lire le code qui va de la sélection (ou équivalent) à la lecture et à l'écriture d'un client, y a-t-il seulement une lecture ou une écriture par client par sélection (ou équivalent)?
Les valeurs de retour pour les fonctions d'E/S sont-elles correctement vérifiées?
Si une erreur est renvoyée par les fonctions précédentes sur un socket, le client est-il supprimé?
Écrire et lire TOUJOURS-IL-TOUJOURS-IL-S-IL-S-IL-T-ÉCAL--
**Fichier de configuration**

Vérifiez le fichier de configuration et modifiez-le si nécessaire. Les configurations suivantes fonctionnent-elles correctement:
Configuration d'un serveur unique avec un seul port.
Configuration de plusieurs serveurs avec un port différent.
Configuration de plusieurs serveurs avec différents noms d'hôte (par exemple: curl --resolve test.com:80:127.0.0.1 http://test.com/). Cela vise à confirmer si votre serveur distingue correctement les demandes pour différents noms d'hôtes, même s'ils se résolvent au même IP et au même port.
Configuration de pages d'erreur personnalisées.
Limiter l'organisme client (par exemple: curl -X POST -H "Contenu-Type: plain/texte" --données "BODI avec quelque chose de plus court ou plus long que la limite corporelle").
Placer les itinéraires et veiller à ce qu'ils soient pris en compte.
Configuration d'un fichier par défaut dans le cas où le chemin est un répertoire.
Configurer une liste de méthodes acceptées pour une route (par exemple: essayez de faire dÉLETER quelque chose avec et sans autorisation).
**Méthodes et cookies**

Pour chaque méthode, assurez-vous de vérifier le code d'état (200, 404, etc):
Les demandes d'EEG fonctionnent-elles correctement?
Les demandes POST fonctionnent-elles correctement?
Les demandes DELETE fonctionnent-elles correctement?
Tester une demande vrée, le serveur fonctionne-t-il toujours correctement ?
Téléchargez certains fichiers sur le serveur et les répétant pour tester qu'ils n'étaient pas corrompus.
Une session de travail et un système de cookies sont-ils présents sur le serveur?
**Interaction avec le navigateur**

Ouvrez le navigateur utilisé par l'équipe pendant les tests et son panneau d'outils de développement pour vous aider à tester.
Le navigateur se connecte-t-il au serveur sans problème ?
La demande et les en-têtes de réponse sont-ils corrects? (Il devrait servir un site Web entièrement statique sans aucun problème).
Essayez une mauvaise URL sur le serveur, est-ce correctement géré ?
Essayez d'inscrire un annuaire, est-il géré correctement ?
Essayez une URL redirigée, est-elle gérée correctement ?
Vérifiez le CGI mis en œuvre, fonctionne-t-il correctement avec des données découpées et déchonnées?
**Questions relatives au port**
Configurer plusieurs ports et sites Web et veiller à ce qu'il fonctionne comme prévu.
Configurer le même port plusieurs fois. Le serveur doit trouver l'erreur.

Ce que nous voulons dire, c'est comment votre serveur gère les erreurs de configuration ou les conflits. Si vous configurez le même port plusieurs fois dans le fichier de configuration de votre serveur pour le même serveur ou des serveurs différents, l'on s'attend à ce que votre serveur attrape cette erreur et la gère de manière appropriée. Il s'agit de s'assurer que votre serveur peut identifier les erreurs de configuration.
Configurer plusieurs serveurs en même temps avec des configurations différentes mais avec des ports communs. Demandez pourquoi le serveur devrait fonctionner si l'une des configurations ne fonctionne pas.

Cela vise à valider le traitement de la configuration de votre serveur. Vous configurerez plusieurs serveurs simultanément, chacun avec des configurations différentes mais avec des ports partagés. Si l'une de ces configurations n'est pas valide ou rencontre un problème, votre serveur doit continuer à fonctionner pour les autres configurations sans être entièrement perturbé. Le but est de s'assurer qu'une erreur dans la configuration d'un serveur ne fait baisser pas l'ensemble du serveur si d'autres configurations sont correctement configurées.
**Assiége et test de résistance**
Utiliser le siège avec une méthode GET sur une page vide, la disponibilité doit être d'au moins 99,5% avec la commande ``siege -b [IP]:[PORT]``.
Vérifiez s'il n'y a pas de fuite de mémoire (vous pourriez utiliser certains outils comme le haut).
Vérifiez s'il n'y a pas de connexion suspendue.
Généralités
«Il y a plus d'un système CGI tel que [Python,C,Perl.
«Il s'agit d'une deuxième implémentation du serveur dans un langage différent (répétvoir des tests pratiques avant pour valider).
