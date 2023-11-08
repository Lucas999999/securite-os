# Examen-Securite-OS-Windows
Nom: Fallous Lucas

## Introduction
Nous avons un poste de travail sous windows 10 pro, nous devons repérer des vulnérabilités sur ce poste liées à des problèmes de configuration ou des suivis de mauvaises pratiques.

## Fichier "secret" dans C:\\secrets\
###  description
Il y a un fichier "secret.txt" dans C:\\Secrets\\ Ce fichier contient les identifiants de l'administrateurs, c'est une très mauvaise pratique de laisser des fichiers contenant les identifiants d'un compte en clair, cependant seul 'Administrateur' a les droits d'accès à ce fichier.
### observation
![]("./images/Pasted image 20231103113816.png")
### remédiation
Sensibiliser les utilisateurs aux bonne pratiques de sécurité.
## LM Hash utilisé
###  description
Les mots de passe sont stockés sur la machine en hash LM ce qui est une mauvaise pratique car ce chiffrement n'est pas du tout robuste.
### observation
![]("./images/Pasted image 20231103115422.png")
### remédiation
Il faut cocher la case "Activé" pour arrêter le stockage des mots de passes en hash LM.

## Pas de seuil de verrouillage
###  description
Le seuil de verrouillage n'est pas défini ce qui fait qu'un acteur malveillant peut tenter de bruteforce l'accès aux comptes sans avoir de restriction avec des mauvais mots de passe.
### observation
![]("./images/Pasted image 20231103115941.png")
### remédiation
Activer le seuil de verrouillage.
## Utilisation de NTLMv1
###  description
NTLMv1 est utilisé à la place de NTLMv2, NTLMv2 utilise des hachages plus robustes et évite les vulnérabilités présentes dans sa version précédente.
### observation
![]("./images/Pasted image 20231103134636.png")
### remédiation
Activer NTLMv2 à la place de NTLMv1.

## Service contoso vulnérable
###  description
le service contoso exécuté au chemin suivant:  C:\\Program Files (x86)\\Contoso Services\\Contoso Hello\\service.exe est vulnérable, l'utilisateur 'User' possède le droit d'écriture sur le fichier service.exe et peut ainsi le réécrire. 
### observation
![]("./images/Pasted image 20231103152436.png")
![]("./images/Pasted image 20231103154823.png")
### remédiation
changer les droits du fichier service.exe pour qu'on ne puisse pas le réécrire.
### Misconfiguration
Ce même service est également victime d'une misconfiguration, son chemin contient des espaces ce qui peut mener à execution de fichiers non maîtrisés: C:\\Program Files (x86)\\Contoso.exe, C:\\Program Files (x86)\\Contoso Services\\Contoso.exe, etc... Cependant aucune de ces possibilités n'est actuellement exploitable car l'utilisateur 'User' n'a pas les droits d'écriture dans ces dossiers. Il faudrait tout de même modifier ces chemins d'accès pour y supprimer les espaces.
## Droits trop élevés de l'utilisateur "User"
###  description
L'utilisateur "User" possède tous les droits sur le dossier C:\\Users\\Administrateur, le dossier home de l'administrateur, bien qu'il n'ait pas de droits dans ses sous dossiers il peut quand même créer de nouveaux fichiers dans son dossier home ce qui reste une très mauvaise pratique.
### observation
![]("./images/Pasted image 20231103163045.png")
### remédiation
retirer ces droits 
## Droits du dossier C:\\tools
### description
L'utilisateur "User" a trop de droits sur le dossier "C:\\tools", ce dossier est de toute évidence utilisé pour faire tourner un programme, on y retrouve hello.exe, hello.dll et hello.ps1, l'utilisateur "User" possède le droit de créer des dossiers dans ce dossier et est ainsi en mesure de créer de nouveaux fichiers étant donné qu'il est propriétaire des dossiers qu'il créé
### observation
![]("./images/Pasted image 20231106230409.png")
### remédiation
Retirer les droits de création de dossier (AD) à l'utilisateur User
## Execution-policy non restreinte
###  description
La politique d'éxecution de scripts powershell est définie comme étant "non-restreinte", des scripts potentiellement malveillants peuvent donc être lancés sur le poste, ce n'est pas un gros problème en soi car il peut y avoir des raisons légitimes de désactiver cette politique mais dans notre cas il est important de relever cette potentielle misconfiguration
### observation
![]("./images/Pasted image 20231108002815.png")
### remédiation
définir une politique d'éxecution des scripts avec la commande: `set-executionpolicy`

## Conclusion
Nous avons pu remarque que le poste de travail embarque plusieurs vulnérabilités en grande partie liées à des misconfigurations. Elles peuvent pour la plupart être résolues très simplement et le suivi des bonnes pratiques de sécurité empêcherait une grande partie de ces erreurs de se produire notamment la gestion des droits qui revient comme problème récurent et qui peut être réglé par de la sensibilisation.
