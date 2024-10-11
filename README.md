# TP_2_UNIX

## Secure Shell: SSH
### Connection SSH root

Ici on se relance un serveur ssh sur la VM avec `systemctl restart ssh`et on vérifie `systemctl status ssh`. 
Ensuite on veut changer la configuration du serveur pour permettre les connections à distance de root (avec mot de passe évidemment). 

Pour se faire on va juste modifier dans le fichier /etc/ssh/sshd_config la ligne `PermitRootLogin` et y mettre tout de suite après `yes`. 

Il existe 4 options de configuration différentes de `PermitRootLogin`:
  -prohibited-password: désactive la connexion par mot de passe de root au serveur ssh
  -yes: autorise la connexion avec mot de passe
  -no: n'autorise pas la connexion 
  -forced-commands-only: les connexions de root avec une clé public sont autorisés (utiles si on veut récuperer des sauvegardes à distance si root n'est pas autorisé à la base). Toutes autres méthodes est désactivé.

### Authentification par clef / Génération de clefs

Pour générer une paire de clé public privé, il faut taper dans le terminal de la machine hôte la commande ` ssh-keygen -t rsa `, elles sont générées et on nous demande un passphrase, bien entendu dans un cas réel il serait plus judicieux d'en mettre une, parce que si quelqu'un réussit à avoir accès à la clé privée d'un serveur il peut avoir accès à absolument tout les machines qui sont liés. 

Ensuite on crée le fichier "authorized_keys" qui n'existe pas, le dossier ssh existe déjà mais on aurait utilisé `mkdir` pour le créer sinon, ensuite on va ajouter la clé dans le fichier grâce à la commande `cat>authorized_keys`.
Puis on donne uniquement les droits avec la commande `chmod 700 authorized_keys`. 

### Authentification depuis la machine hôte

On se connecte avec la commande `ssh -i id_rsa.pub root@10.20.0.137`spécifique à la machine. 

### Sécurisation 

On crée une copie de la clé avec `ssh-copy-id root@10.20.0.137`: ![image](https://github.com/user-attachments/assets/ecc5dfef-4b47-454a-98d8-6eb4e6d270ad) voici une explication en anglais de comment fonctionne la clé. 


ensuite on reconfigure le fichier sshd_config et on remet `PermitRootLogin no` au lieu de yes. 

## Processus

### Etude des processus UNIX 

1. Voici ce que donne la commande `ps`:

![image](https://github.com/user-attachments/assets/cca8f707-321a-4770-b45c-af3f80881cd5)

La commande ps affiche les processus en cours avec des informations supplémentaires dont TIME qui indique le temps cumulé du processeur. 
Le processus lancé après le démarrage est systemmd, parce que c'est le premier qu'on retrouve en tapant la commande `ps -e`: 

![image](https://github.com/user-attachments/assets/c9008e24-85b9-4715-ac8d-1afcabe17e1d)

Voici la commande permettant de savoir depuis quand mon serveur tourne 


Le processus qui est le plus utilisé par le processeur est la commande en elle-même parce qu'on quand on regarde dans la colonne %CPU soit le pourcentage que chaque processus utilise dans le CPU, tout les processus sont à 0% sauf la commande elle-même. 

Voici le nombre de processus: 

![image](https://github.com/user-attachments/assets/b92d0829-d46b-4a42-baf6-402f5f73ba2e)

Bon finalement je viens d'apprendre que ps, c'est les processus en cours, donc pas tout ceux depuis le démarrage, finalement lorsqu'on fait ps, c'est bien le dernier PID affiché qui compte, ainsi il y a 1188 processus depuis le début.

![image](https://github.com/user-attachments/assets/5dc00d61-5da6-453b-bd7d-ab053dab21f8)


2. grâce à ce lien :
    [Lien](https://www.funix.org/fr/unix/process.htm)
On trouve la commande `ps -ef` qui affiche le ppid de chaque pid. 

4. On essaie d'installer le pstree avec `apt search pstree`, il me répond que c'est pas le bon dossier : 

![image](https://github.com/user-attachments/assets/52fc173f-e477-4d08-a224-25100d15178d)

Donc, il faut faire `apt search psmisc` puis `apt install psmisc`

Ensuite pour trier le top en fonction du pourcentage d'utilisation de la mémoire c'est `top -o %MEM` [Lien](https://www.malekal.com/top-lister-processus-linux/)

Le processus le plus gourmand est systemmd aussi bien en terme de cpu que mémoire.  

![image](https://github.com/user-attachments/assets/f4697f6f-130c-4981-8c74-e147227df788)

Pour changer la couleur dans top il faut aller dans top et taper la touche "z" : 
[Lien](https://www.malekal.com/top-lister-processus-linux/#Comment_changer_les_couleurs)

Htop c'est plus beau déjà, ensuite il y a moins de lignes, ensuite il y a un menu qui peut aider en bas :

![image](https://github.com/user-attachments/assets/64234467-8a2a-49db-b3f9-789d7af98ed4)







