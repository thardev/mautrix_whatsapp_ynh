# Matrix WhatsApp bridge pour YunoHost

[![Niveau d'intégration](https://dash.yunohost.org/integration/mautrix_whatsapp.svg)](https://dash.yunohost.org/appci/app/mautrix_whatsapp) ![](https://ci-apps.yunohost.org/ci/badges/mautrix_whatsapp.status.svg) ![](https://ci-apps.yunohost.org/ci/badges/mautrix_whatsapp.maintain.svg)  
[![Installer Matrix WhatsApp bridge avec YunoHost](https://install-app.yunohost.org/install-with-yunohost.svg)](https://install-app.yunohost.org/?app=mautrix_whatsapp)

*[Read this readme in english.](./README.md)*
*[Lire ce readme en français.](./README_fr.md)*

> *Ce package vous permet d'installer Matrix WhatsApp bridge rapidement et simplement sur un serveur YunoHost.
Si vous n'avez pas YunoHost, regardez [ici](https://yunohost.org/#/install) pour savoir comment l'installer et en profiter.*

## Vue d'ensemble

Une passerelle entre Matrix et WhatsApp empaquetée comme un service YunoHost.
Les messages, médias et notifications sont relayées entre un compte WhatsApp et un compte Matrix.
Avec l'option de connexion Robot-Relai, 1 compte Matrix peut inviter d'autres comptes Matrix dans un salon Matrix relayé avec un groupe WhatsApp, ainsi même des personnes sans compte WhatsApp peuvent communiquer avec des utilisateur.ice.s de WhatsApp.
La passerelle ["Mautrix-WhatsApp"](https://docs.mau.fi/bridges/go/whatsapp/index.html) consiste en un Service d'Application Matrix-Synapse et repose sur une base-de-données postgresql (mysql également possible).
C'est pourquoi [Synapse for YunoHost](https://github.com/YunoHost-Apps/synapse_ynh) doit être préalablemnet installé.

** Attention : sauvegardez et restaurez toujours les deux applications Yunohost matrix_synapse et mautrix_whatsapp en même temps!**


**Version incluse :** 0.2.4~ynh1



## Avertissements / informations importantes

### Liste de passerelles publiques

* Demandez sur un des salons suivants: #mautrix_yunohost:matrix.fdn.fr or #whatsapp:maunium.net

### Usages de la passerelle
** Notez que plusieurs comptes WhatsApp et Matrix peuvent être relayés, chaque compte WhatsApp connecté a son propre Salon d'Administration. Si plusieurs utilisateur.ice.s du Robot sont dans un même groupe WhatsApp, seul un Salon Matrix sera créé par la passerelle. **

#### Relayer TOUTES les conversations entre UN compte WhatsApp et UN compte Matrix
* Prérequis : votre compte Matrix ou le serveur sur lequel il est hébergé doit être autorisé dans la configuration de la passerelle (voir ci-dessous)
* Invitez le Robot (par défaut @whatsappbot:synapse.votredomaine) à une nouvelle conversation.
* Ce nouveau salon d'administration du Robot Mautrix-WhatsApp est appelé "Administration Room".
* Tapez ``login``
* Scannez le QR-code avec la caméra Whatsapp de votre Machine Virtuelle ou ordiphone (option WhatsApp Web dans l'appli)
* Envoyez ``help`` au Robot dans le "Administration Room" pour une liste des commandes d'administration de la passerelle.
Voir aussi [upstream wiki Authentication page](https://docs.mau.fi/bridges/go/whatsapp/authentication.html)

#### Robot-Relai "Relaybot": Relayer les conversations de TOUS les comptes Matrix et TOUS les comptes WhatsApp présents dans UN groupe/salon

* Prérequis : Créer un nouveau salon vide "Relaybot Administration Room" et rechercher son identifiant.
* Activer l'option Robot-Relai "Relaybot" dans la configuration de la passerelle (default=disabled, voir ci-dessous) et ajouter l'identifiant du salon d'administration du Relaybot "Relaybot Administration Room".
* Redémarrer le service mautrix_whatsapp
* Vous pouvez maintenant relayer un groupe WhatsApp en y invitant le numéro de téléphone du compte WhatsApp connecté au RobotRelai. Côté WhatsApp, tous les messages venant de Matrix seront vus comme envoyés depuis le compte WhatsApp connecté, avec un préfix indiquant le compte Matrix correspondant. Côté Matrix, la passerelle va créer des comptes Matrix pour chaque compte WhatsApp présent dans le groupe relayé. Les messages sont indiqués comme provenant soit du numéro de téléphone, soit du pseudo WhatsApp.
Voir aussi [la page wiki principale sur le Robot-Relai](https://docs.mau.fi/bridges/go/whatsapp/relaybot.html)

### Configuration de la passerelle

La passerelle est [configurée avec les paramètres standards adaptés pour votre YunoHost et l'instance Matrix-Synapse sélectionnée](https://github.com/YunoHost-Apps/mautrix_whatsapp_ynh/blob/master/conf/config.yaml). Vous pouvez par exemple ajouter des administrateur.ice.s et utilisateur.ice.s du Robot autorisés en modifiant le fichier de configuration par liaison SSH: 
``` sudo nano /opt/yunohost/mautrix_whatsapp/config.yaml```
puis en redémarrant le service: 
``` sudo yunohost service restart mautrix_whatsapp```

### Support multi-comptes

* Les utilisateur.ice.s du Robot ne sont pas liés aux comptes Yunohost. N'importe quel compte Matrix ou serveur Synapse autorisés dans la configuration de la passerelle peut inviter/utiliser le Robot. 
* Le robot WhatsApp est un utilisateur Matrix-Synapse local, mais accessible via la fédération (Synapse public ou privé).
* Plusieurs comptes WhatsApp et Matrix peuvent être liés avec une seule passerelle, chaque compte a son propre salon d'administration. 
* Si plusieurs utilisateur.ice.s du Robot sont dans un même groupe WhatsApp, seul un Salon Matrix sera créé par la passerelle. Autrement dit, la passerelle construit un seul miroir du réseau de discussion existant sur WhatsApp (utilisateurs et salons).
* Voir https://github.com/YunoHost-Apps/synapse_ynh#multi-users-support

### Support multi-instance

* L'installation multi-instance devrait fonctionner. Plusieurs instances de passerelles pourraient être installées pour une instance de Matrix-Synapse. Cela permet à un compte matrix de se relier à plusieurs comptes WhatsApp. 
* Plusieurs instances de passerelles pourraient être installées pour que chaque instance de Matrix-Synapse puisse en bénéficier. Mais une passerelle peut être utilisée par les comptes de plusieurs instances Matrix-Synapse.

### Architectures supportées

ARM, ARM64, AMD64

### Limitations

* Les appels Audio/Video ne sont pas relayés. Seule une notification apparait. 
* Si WhatsApp perd la connexion, par ex. l'ordiphone est mis en mode avion ou les notifications poussées sont désactivées, le robot doit parfois être redémarré à la main en envoyant un message ``wa !reconnnect`` dans le salon d'administration.

### Informations additionnelles

* Il est recommandé d'installer WhatsApp sur une machine Android virtuelle  tournant sur un serveur, cf. le [page Android-VM-Setup du wiki](https://docs.mau.fi/bridges/go/whatsapp/android-vm-setup.html)

Pour tester la communication entre le Service d'Application et Matrix-Synapse sur une Machine Virtuelle (ex. avec un nom de domaine: synapse.vm), vous devez installer un certificat:
```
echo | openssl s_client -showcerts -servername synapse.vm -connect synapse.vm:443 2>/dev/null | awk '/-----BEGIN CERTIFICATE-----/, /-----END CERTIFICATE-----/' >> /usr/local/share/ca-certificates/synapse.vm.crt
update-ca-certificates
```

### Informations diverses

* Salon Matrix sur les Passerelles dans Yunohost): #mautrix_yunohost:matrix.fdn.fr
* Salon Matrix (application principale): #whatsapp:maunium.net
Si vous devez téléverser vos fichiers log quelque-part, soyez avertis qu'ils contiennent des informations sur vos contacts et vos numéros de téléphone. Effacez-les avec 
``| sed -r 's/[0-9]{10,}/📞/g' ``
* La passerelle "Mautrix-WhatsApp" repose sur l'implémentation [Rhymen/go-whatsapp](https://github.com/Rhymen/go-whatsapp) du projet [sigalor/whatsapp-web-reveng](https://github.com/sigalor/whatsapp-web-reveng).

## Documentations et ressources

* Documentation officielle de l'admin : https://docs.mau.fi/bridges/go/whatsapp/index.html
* Dépôt de code officiel de l'app : https://github.com/mautrix/whatsapp
* Documentation YunoHost pour cette app : https://yunohost.org/app_mautrix_whatsapp
* Signaler un bug : https://github.com/YunoHost-Apps/mautrix_whatsapp_ynh/issues

## Informations pour les développeurs

Merci de faire vos pull request sur la [branche testing](https://github.com/YunoHost-Apps/mautrix_whatsapp_ynh/tree/testing).

Pour essayer la branche testing, procédez comme suit.
```
sudo yunohost app install https://github.com/YunoHost-Apps/mautrix_whatsapp_ynh/tree/testing --debug
ou
sudo yunohost app upgrade mautrix_whatsapp -u https://github.com/YunoHost-Apps/mautrix_whatsapp_ynh/tree/testing --debug
```

**Plus d'infos sur le packaging d'applications :** https://yunohost.org/packaging_apps