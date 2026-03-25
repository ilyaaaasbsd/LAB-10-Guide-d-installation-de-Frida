# LAB-10-Guide-d-installation-de-Frida
#  Rapport d’audit – Installation & Validation de Frida

##  Informations générales

* **Lab** : Frida Setup & Instrumentation
* **Date** : 2026-03-25
* **Auditeur** : ilyas
* **OS hôte** : Windows 11 / Linux / macOS
* **Cible** : Appareil Android (API 29)
* **Outils utilisés** : Frida, ADB, Python

---

##  Objectifs

Ce laboratoire avait pour objectifs :

* Installer et configurer Frida (CLI + Python)
* Déployer `frida-server` sur Android
* Tester la communication entre PC et appareil
* Injecter un script Frida minimal
* Diagnostiquer les erreurs courantes

---

##  Méthodologie

1. Installation de Python et pip
2. Installation de Frida (client)
3. Configuration ADB et connexion appareil
4. Déploiement de `frida-server`
5. Vérification de la communication
6. Injection d’un script test
7. Dépannage et validation

---

##  Installation et vérification

###  Vérification Python

```bash
python --version
pip --version
```

###  Installation Frida

```bash
pip install --upgrade frida frida-tools
```

###  Vérification

```bash
frida --version
frida-ps --help
python -c "import frida; print(frida.__version__)"
```

 **Résultat** : Installation réussie (version 16.x.x)

---

##  Configuration Android (ADB)

```bash
adb version
adb devices
```

### Résultat attendu :

* Appareil détecté avec statut `device`

---

##  Déploiement de frida-server

###  Identification de l’architecture

```bash
adb shell getprop ro.product.cpu.abi
```

 Résultat : `arm64-v8a`

---

###  Upload du serveur

```bash
adb push frida-server /data/local/tmp/
adb shell chmod 755 /data/local/tmp/frida-server
```

---

###  Lancement

```bash
adb shell /data/local/tmp/frida-server -l 0.0.0.0
```

 **Résultat** : serveur lancé avec succès

---

##  Test de connexion

```bash
frida-ps -U
```

### Résultat

* Liste des processus affichée
* Connexion USB fonctionnelle

---

### Liste des applications

```bash
frida-ps -Uai
```

 Exemple :

| Package              | Nom       |
| -------------------- | --------- |
| com.android.settings | Settings  |
| com.android.systemui | System UI |
| com.example.app      | Test App  |

---

##  Injection Frida (Test)

###  Script hello.js

```javascript
Java.perform(function () {
  console.log("[+] Frida Java.perform OK");
});
```

---

###  Exécution

```bash
frida -U -f com.example.app -l hello.js --no-pause
```

---

###  Résultat

```
[+] Frida Java.perform OK
```

✔ Injection réussie
✔ Environnement fonctionnel

---

##  Test avancé (Native Hook)

```javascript
console.log("[+] Script chargé");

Interceptor.attach(Module.getExportByName(null, "recv"), {
  onEnter(args) {
    console.log("recv appelée");
  }
});
```

---

##  Dépannage (Troubleshooting)

###  Problème simulé

```
unable to connect to remote frida-server
```

---

###  Diagnostic

```bash
adb devices
adb shell ps | grep frida
```

 Résultat : frida-server arrêté

---

###  Solution

```bash
adb shell /data/local/tmp/frida-server -l 0.0.0.0
adb forward tcp:27042 tcp:27042
```

 Connexion restaurée

---

##  Analyse des risques

###  Risques potentiels liés à Frida

* Instrumentation dynamique → accès mémoire runtime
* Bypass de sécurité applicative
* Interception de données sensibles

---

##  Bonnes pratiques

* Utiliser uniquement sur environnement autorisé
* Aligner versions client/serveur
* Ne jamais exposer frida-server en production
* Restreindre accès USB/debug

---

##  Résultats globaux

| Étape              | Statut |
| ------------------ | ------ |
| Installation Frida | ✅ OK   |
| ADB configuration  | ✅ OK   |
| frida-server       | ✅ OK   |
| Connexion          | ✅ OK   |
| Injection          | ✅ OK   |
| Debugging          | ✅ OK   |

---

##  Nettoyage

```bash
adb shell pkill -f frida-server
adb shell rm /data/local/tmp/frida-server
pip uninstall frida frida-tools
```

---

##  Annexes

### Commandes principales

```bash
frida --version
frida-ps -U
adb devices
adb push frida-server
```

---

##  Conclusion

L’environnement Frida a été correctement installé et validé.

✔ Communication établie entre PC et Android
✔ Injection de scripts réussie
✔ Capacité d’analyse dynamique confirmée

 Ce lab démontre que Frida est un outil puissant pour :

* Reverse engineering
* Analyse runtime
* Audit de sécurité mobile

---

##  Déclaration

Je certifie que ce laboratoire a été réalisé :

* Dans un cadre légal et pédagogique
* Sans atteinte à des systèmes réels
* Selon les bonnes pratiques de sécurité

**Nom** : [Ton Nom]
**Date** : 2026-03-25

---
