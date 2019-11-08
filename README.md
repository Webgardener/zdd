# Déploiement Blue-Green avec HAProxy

Le code présent dans ce repo permet le déploiement d'une application Tomcat avec une gestion intelligente des versions afin d’éviter les déploiements inutiles.

Il permet également d'effectuer un déploiement sans interruption du service.

Afin de s'assurer que l'application fonctionne avant d'être exposée, la socket d’administration Haproxy est utilisée via un module Ansible prévu à cet effet.


## Installation

Nous allons d'abord installer tomcat et HAProxy :

```shell
ansible-playbook install.yml
```

Ensuite, nous pouvons déployer l'application dans sa version 1.1.2 :

```shell
ansible-playbook deploy.yml -e tomcat_app_version=1.1.2
```


## Gestion active du pool de serveurs
Dans les premières secondes avant qu’il ne le marque down HAProxy pourrait laisser revenir des requêtes en erreur. Heureusement c’est un logiciel bien conçu qui en cas d’absence de réponse d’un backend tente d’en joindre un autre.

Mais comment réagirait HAProxy si un backend non fonctionnel était démarré suite à une mise à jour ?

À partir du moment où le Tomcat est de nouveau démarré et répond bien sur l’URL que vérifie HAProxy il est remis dans le pool actif. HAProxy est capable de gérer un backend qui ne répond pas, mais ne gère pas ceux qui répondent mal.

C’est à nous de nous assurer que nous ne remettons pas un Tomcat non fonctionnel dans le pool actif.

Il nous faut tester les réponses de ce Tomcat après son démarrage mais avant de le laisser rejoindre le pool actif.

Nous allons donc utiliser `delegate_to` et le module `haproxy` d’Ansible pour manipuler les backends actifs dans HAProxy.
