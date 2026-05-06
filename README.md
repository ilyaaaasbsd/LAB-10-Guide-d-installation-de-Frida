LAB 10 : Guide d'installation de Frida
 Introduction
Ce laboratoire a pour objectif de comprendre et maîtriser l’outil Frida, utilisé pour l’analyse dynamique des applications Android. Contrairement à l’analyse statique, Frida permet d’observer et de modifier le comportement d’une application en temps réel pendant son exécution.
Frida repose sur une architecture composée de deux éléments principaux :
•	Le client : installé sur le PC (interface de commande) 
•	Le serveur (frida-server) : exécuté sur le téléphone Android 
Ces deux composants communiquent via ADB (Android Debug Bridge). 

 Étape 1 — Installation du client Frida
Avant toute chose, il est nécessaire de préparer l’environnement Python, car Frida dépend de cet outil.
 Procédure :
1.	Vérifier l’installation de Python : 
python --version
2.	Installer Frida : 
pip install frida-tools
3.	Vérifier l’installation : 
frida --version
Si une version s’affiche, le client Frida est correctement installé.
 

 Étape 2 — Configuration ADB
ADB permet d’établir une communication entre le PC et le téléphone ou l’émulateur.
 Vérification :
adb devices
 Résultat attendu :
emulator-5554    device
 Cela confirme que l’appareil est bien connecté.
 


 Étape 3 — Installation de frida-server
Cette étape consiste à déployer la partie serveur directement sur l’appareil Android.
 Procédure :
1.	Identifier l’architecture : 
adb shell getprop ro.product.cpu.abi
2.	Télécharger la version correspondante de frida-server (GitHub) 
 Important :
•	La version du serveur doit être identique à celle du client 
3.	Envoyer le fichier vers l’appareil : 
adb push frida-server /data/local/tmp/
4.	Donner les permissions : 
adb shell chmod 755 /data/local/tmp/frida-server
5.	Lancer le serveur : 
adb shell
su
/data/local/tmp/frida-server &
 À ce stade, le serveur tourne en arrière-plan et attend les commandes du PC.
 

 Étape 4 — Test de connexion
Pour vérifier la communication entre le PC et l’appareil :
frida-ps -U
 Résultat :
•	Liste des applications en cours d’exécution 
•	PID + noms des processus 
 Cela confirme que Frida est opérationnel. 
 

 Étape 5 — Injection simple
Cette étape permet de tester le fonctionnement de Frida.
 Objectif :
•	Injecter un script JavaScript minimal dans une application 
 Le script affiche simplement un message pour valider :
•	communication OK 
•	injection OK 
 
 Étape 6 — Console interactive Frida
Frida propose une console interactive permettant d’analyser une application en direct.
 Fonctionnalités :
•	Exploration des classes Java 
•	Liste des méthodes disponibles 
•	Analyse du comportement interne 
 Cela permet de comprendre l’architecture de l’application en temps réel.

 Analyse approfondie
  
1. Analyse des classes Java
Permet de :
•	identifier les classes principales 
•	repérer les modules critiques (authentification, réseau, stockage) 

 2. Détection des bibliothèques sensibles
Objectif :
•	vérifier l’utilisation de cryptographie (AES, TLS) 
•	identifier des bibliothèques vulnérables 
 

 3. Analyse du système de fichiers
Observation des accès aux fichiers :
•	SharedPreferences 
•	SQLite 
•	fichiers de configuration 
 Permet de détecter :
•	stockage de données sensibles 
•	mauvaises pratiques de sécurité 

 4. Informations sur le processus
•	PID 
•	état de l’application 
•	interactions système 

 Étape 7 — Surveillance avancée
Frida permet d’intercepter plusieurs éléments critiques :
 Chiffrement
•	interception des fonctions AES/RSA 
•	récupération des données avant chiffrement 

 Stockage local
•	surveillance de SharedPreferences 
•	détection de mots de passe ou tokens stockés en clair 

 Réseau
•	interception des requêtes HTTP/HTTPS 
•	analyse des headers et données envoyées 
 

 Étape 8 — Hooking des méthodes Java
Le hooking est la fonctionnalité principale de Frida.
 Cas d’usage :
 SharedPreferences
•	interception de getString()
 Permet de voir les données lues en temps réel 

SQLite
•	interception de execSQL() et rawQuery()
Permet de voir les requêtes SQL exécutées 

 Sécurité (Root / SSL)
•	modification du comportement des fonctions 
•	exemple : 
isRooted() → false
 Cela permet de contourner certaines protections

 Conclusion
Ce laboratoire montre que Frida est un outil extrêmement puissant pour l’analyse dynamique des applications Android.
Contrairement à l’analyse statique, Frida permet :
•	d’observer le comportement réel de l’application 
•	d’intercepter les données sensibles 
•	de tester la robustesse des mécanismes de sécurité 
 Le hooking constitue l’élément central de cette analyse, permettant de manipuler l’application en temps réel.

