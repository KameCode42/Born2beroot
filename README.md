<img width="720" height="720" alt="Image" src="https://github.com/user-attachments/assets/2d551d67-9319-4f4d-8f6c-f27c3b4acbe3" />

# Sujet :
Le projet Born2beroot a pour objectif de configurer une machine virtuelle Linux sécurisée (souvent basée sur Debian ou Rocky Linux) en respectant les bonnes pratiques d'administration système.

# Objectifs :
- Installer et configurer une VM Linux (avec VirtualBox ou UTM)
- Mettre en place un compte utilisateur non-root avec des droits sudo
- Configurer un pare-feu (ufw) et des services comme ssh
- Appliquer des règles de sécurité système via sudo, password policies, etc.
- Configurer LVM (Logical Volume Management) à l’installation

# Mise en place et explications :
Debian :
- C'est un systeme d'exploitation libre et open source base sur le noyau linux
- Stable et robuste
- Utilise apt qui permet d’installer, de mettre à jour et de supprimer des logiciels facilement
- Systeme de qualite sans se soucier des dernieres fonctionnalite

Rocky :
- Acces entreprise
- Systeme pour le long terme
- Plus complexe a installer

Difference apt et aptitude :
Les deux commandes sont pratiquement similaire sauf que : <br>
- apt : ne propose pas de solution alternative en cas d'erreur d'installation des paquets
- aptitude : offre une interface semi-graphique dans le terminal et offre des solutions en cas de conflit

APPAarmor :
- Restreint les acces aux chemins dans le systeme de fichier, 
- Il contrôle précisément quelles applications peuvent accéder à quels fichiers ou répertoires

------------------------------------------------------------------------------------------------------------------

UFW : 
- Outil de configuration de pare-feu, permet de creer un pare-feu sur l'hote
- UFW permet de simplifier la gestion des règles du pare-feu

Installation et configuration de l'UFT :
- Iptables permet a un admin systeme de configurer les regles du pare-feu
- sudo apt install ufw pour installer le pare-feu
- sudo ufw enable nous montre que le pare-feu est actif
- sudo ufw allow 4242 permet de laisser notre pare-feu accepter les connexions dans le port4242
- sudo ufw status permet de verifier l etat du pare-feu
- sudo ufw delete allow 4242 permet de supprimer une regle

------------------------------------------------------------------------------------------------------------------

Sudo : <br>

Changer nom host :
- sudo hostnamectl set-hostname <new_hostname>
- hostnamectl status

Afficher partition :
- lsblk
- LVM (Logical Volume Manager) permet de gérer les partitions de disque, permettant aux administrateurs de modifier, redimensionner et gérer les systèmes de fichiers
- swap : sert de memoire virtuelle en cas de memoire saturer
- home : repertoir ou se trouve les fichiers utlisateurs
- boot : repetoire ou se trouve les fichiers necessaire au demarrage du systeme d'exploitation

Ajouter au groupe un utilisateur :
- sudo adduser <login> permet de creer un utilisateur additionel
- sudo addgroup user42 permet de creer un groupe appeler user42
- sudo usermod -aG <sudo> <login> permet d'ajouter le nouvel utilisateur au groupe sudo
- sudo usermod -aG <group> <login> permet d ajouter le nouvel utilisateur au goupe
- sudo adduser dlefur user42 inclut un utilisateur dans les groupes sudo et user42

- getent group sudo et getent group user42 permet de verifier si les groupes sudo et user42 sont present avec notre utilisateur

- sudo reboot permet de redemarrer la machine
- sudo -V permet de montrer les infos sudo
- su permet de passer en root dlefur42
- GID est l'identifiant du groupe

------------------------------------------------------------------------------------------------------------------

Installation et configuration de SSH :
-  L'acronyme SSH signifie Secure Shell permet de communiquer via un canal securiser entre deux ordinateurs, 
   entre un client  et un serveur, les donnees ne peuvent pas etre lues par des tiers
- sudo apt update permet de mettre a jour le systeme
- sudo apt install openssh-server est l'outil principal pour l'acces a distance avec le protocole SSH
- sudo service ssh status permet si l'etat est actif

Connexion par SSH :
- ssh <user>@localhost -p 4242 permet de se connecter via SSH a notre port 4242
- En root ne dois pas se connecter mais avec le login oui

------------------------------------------------------------------------------------------------------------------

Mise en place politique sudo :
- Creer le fichier pour stocker la politique sudo
- Creer un dossier pour que chaque commande soit enregistre

- Defaults  passwd_tries=3
- Defaults  badpass_message="Mensaje de error personalizado"
- Defaults  logfile="/var/log/sudo/sudo_config"
- Defaults  log_input, log_output
- Defaults  iolog_dir="/var/log/sudo"
- Defaults  requiretty
- Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

- ligne 1 : nombre d'essaie pour entrer le mdp sudo
- ligne 2 : message d erreur en cas de mdp faux
- ligne 3 : le chemin ou les logs sudo seront stocker
- ligne 4 : ce qui sera enregistrer comme entree et sortie
- ligne 5 : repertoire ou sont stocker entre et sortie (log input et output sont enregister dans /var/log/sudo)
- ligne 6 : exige que sudo soit executer depuis un terminal TTY (force l'utilisation d un terminal pour lancer des commandes)
- ligne 7 : definie le chemin securise pour les executable que sudo est autoriser a lancer

------------------------------------------------------------------------------------------------------------------

Editer un mdp fort :
- sudo vim /etc/pam.d/common-password
- entrer dans l editeur avec vim
- pass_max_day est le jour max jusqu'a l'expiration du mot de passe
- pass_min_day nombre minimum de jours avant de pouvoir changer le mot de passe
- pass_warn_age nombre de jours d'avertissement avant l'expiration du mot de passe
- sudo apt install libpam-pwquality permet d utiliser des paquets pour definir des regles stricte dans le mdp, 
  par exemple longueur min mdp, chiffre et majuscule, mdp trop simple, mdp similaire

- minlen=10
- ucredit=-1
- dcredit=-1
- lcredit=-1
- maxrepeat=3
- reject_username
- difok=7
- enforce_for_root

- ligne 1 : le nbr de caractere mini que le mdp doit contenir
- ligne 2 : le mot de passe doit au moins contenir une lettre majuscule
- ligne 3 : le monde de passe doit au moins contenir un chiffre.
- ligne 4 : le mot de passe doit au moins contenir une lettre minuscule.
- ligne 5 : le mdp ne doit pas contenir 3 fois le meme caractere 
- ligne 6 : le mdp ne doit pas contenir le nom de l'utilisateur
- ligne 7 : le mot de passe qu'il doit contenir au moins sept caractères diférent du dernier mot de passe usé.
- ligne 8 : l’option enforce_for_root permet d'appliquer strictement les règles de sécurité des mots de passe configurées 
          pour tous les utilisateurs, y compris root

- Modifier les caracteristique du dessus pour l'utilisateur root
- sudo chage -l <username>pour vérifier le mot de passe de l'utilisateur de root et de login
- sudo chage -m <time> <username> et sudo chage -M <time> <username> permet de modifier les caracteristique du mdp

------------------------------------------------------------------------------------------------------------------

Script :
grep :
- permet de rechercher des informations precise dans des fichiers
awk :
-  langage de programmation principalement utilisé pour traiter 
   et manipuler du texte et des fichiers structurés en lignes et colonnes
TCP :
- garantit que les données sont transmises dans le bon ordre et sans pertes
df : 
- signifie systeme de fichiers de disque
grep -v 
- pour exclure les ligne contenant /boot

- uname -a cette commande permet d'imprimer toutes les informations, sauf si le CPU n'est pas connu

- grep "physical id" /proc/cpinfo, cette partie recherche toutes les lignes contenant la chaîne "physical id" dans le fichier /proc/cpuinfo
  chaque ligne "physical id" représente un processeur physique unique.

- grep "processor" /proc/cpuinfo | wc -l pour le processeur

- free permet de montrer la memoire ram

- free --mega | awk '$1 == "Mem:" {print $3}' permet d'afficher la memoire utiliser

- free --mega | awk '$1 == "Mem:" {print $2} permet d'obtenir la memoire totale

- free --mega | awk '$1 == "Mem:" {printf("(%.2f%%)\n", $3/$2*100)}' permet d'imprimer le pourcentage de ram utiliser

- df -m | grep "/dev/" | grep -v "/boot" | awk '{memory_use += $3} END {print memory_use}' permet de visualiser la memoire occuper et dispo du disque

- df -m | grep "/dev/" | grep -v "/boot" | awk '{use += $3} {total += $2} END {printf("(%d%%)\n"), use/total*100}' combiner deux commande,
  un pour la memoire utiliser et un pour la memoire totale pour obtenir le pourcentage

- vmstat 1 4 | tail -1 | awk '{print $15}' permet de voir le pourcentage d'utilisation

- who -b | awk '$1 == "system" {print $3 " " $4}' pour la voir la date et heure du dernier redemarrage

- if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi, pour vérifier si LVM est actif ou non, nous utiliserons le lsblk, 
  une commande qui nous montre des informations sur tous les dispositifs de bloc (disques durs, SSD, mémoires, etc.) parmi toutes les informations qu'il fournit, 
  nous pouvons voir lvm dans le type de gestionnaire. Pour cette commande, nous ferons un if parce que nous imprimerons Oui ou Non. 
  La condition que nous recherchons sera de compter le nombre de lignes dans lesquelles "lvm" apparaît et
  s'il y a plus de 0 nous imprimerons Oui, s'il y a 0 nous imprimerons Non

- ss -ta | grep ESTAB | wc -l permet de voir le nbr de connexion TCP etablie et utiliser grep pour voir ceux qui sont etablie

- users | wc -w permet de nous montrer les nombre d'utilisateurs

- ip link | grep "link/ether" | awk '{print $2}' permet d'obtenir l'adresse ip mac

- journalctl _COMM=sudo | grep COMMAND | wc -l permet d'obtenir le nbr de commande executer avec sudo

------------------------------------------------------------------------------------------------------------------

Crontab :
- permet d'exécuter automatiquement des commandes ou des scripts à des moments spécifiés

- sudo crontab -u root -e modifie le crontab de l'utilisateur root -u, -e par defaut utilisateur courant, -e ouvrir editeur de texte
- */10 * * * * sh /ruta del script
*/10 signifie que la tache s execute toute les 10 minutes
* tache execute toutes les heures
* tache execute tous les jours du mois
* tache execute tous les mois
* tache execute tous les jours de la semaine

Le script sera exectuer toute les 10 min , chaque jour, chaque mois quelque soit le jour de la semaine
