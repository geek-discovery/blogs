---
title: Comment s'y prendre avec Azure AD Conditional Access.
layout: post
post-image: /assets/images/CloudSecurity.jpg
description: Comment s'y prendre avec Azure AD Conditional Access.
tags:
- Azure Active Directory
- Conditional Access
- AAD
- Zero Trust
- Zero-Trust
- Microsoft
---

Dans les programmes de bascule des entreprises vers les services Cloud Collaboratifs de Microsoft (le bien nommé Office 365) la question de la sécurisation des accès aux services est bien souvent non adressée au bon niveau. En effet, la question de quelles politiques d’accès doivent être mises en place doit se poser en amont. Bien souvent la réponse est de vouloir bloquer les accès selon les cas d’usages hérités des politiques de sécurité appliquées sur les environnements *OnPremise*. Toutefois est-ce la bonne approche ?
L’objectif de cet article est de donner les clés (sans pour autant entrer dans l'ensemble des subtilités) pour comprendre les enjeux autour de la politique d’accès aux services Office 365 et d’expliciter certaines bonnes pratiques.

# Préambule

Au-delà des concepts généraux du modèle *Zero Trust*, Microsoft décline au travers de l’*Azure AD Conditional Access*, ce concept pour les accès aux services reposent sur l’*Azure AD* comme fournisseur d’identité (*Identity Provider - IDP*). Bien que relativement simple à son lancement (peu de critères), au fil du temps l'*Azure AD Conditional Access* a fortement évolué pour traiter un nombre important de cas d’usages.

Outre son fonctionnement et ses bénéfices dont je parle ultérieurement, le mode de fonctionnement de l'*Azure AD Conditional Access* lève des questions chez quasiment tous mes clients qui restent sur une approche défensive, c’est-à-dire sur le modèle historique du château fort.

L’objectif de cet article est d’apporter des explications sur le fonctionnement de l'*Azure AD Conditional Access* (qui devient incontournable dans tous programmes de *Modern Management* basés sur Intune, tous programmes de bascule vers Office 365 ou sur tous programmes de massification *SSO Azure AD*), les impacts sur le modèle de sécurité et de gouvernance de l’entreprise, et les bonnes pratiques pour en tirer le plus de bénéfices.

De manière globale, l’*Azure AD Conditional Access* se base sur le triptyque Signal, Décision et Contrôle représenté dans la cartographie suivante (Février 2022):

![Zero Trust Access by Microsoft Azure AD Conditional Access](/assets/images/MicrosoftZeroTrustAccess.png)

Source Microsoft : [Conditional Access design principles and dependencies - Azure Architecture Center](https://docs.microsoft.com/en-us/azure/architecture/guide/security/conditional-access-design)

# Fonctionnement de l'*Azure AD Conditional Access*
Avant de poursuivre sur le rôle et l’usage de l'*Azure AD Conditional Access*, il est nécessaire de correctement appréhender le principe d’applications des règles de l'*Azure AD Conditional Access* :
* Il ne s’agit pas d’un fonctionnement de type **Firewall** avec une règle de **Deny All** si aucune règle ne s’applique
* Si une règle s’applique selon les conditions définies, alors des contraintes de connexions sont appliquées (par exemple requérir une authentification Multi-facteur)
* Si l’ensemble des règles ne s’applique pas (aucune des conditions d’accès définies ne permettent une corrélation avec le contexte d’authentification), alors l’accès est possible aux services avec une authentification simple de type login / mot de passe
Ensuite pour correctement appréhender sa mise en place, il est nécessaire de correctement définir les cas d’usage d’accès aux services et les exigences d’accès associées de bout en bout.

# Quelle philosophie adopter pour tirer bénéfices de l'*Azure AD Conditional Access* ?

Lors d’échanges sur le sujet de la protection des accès à Azure AD et Office 365, je constate très (trop) souvent que les politiques de sécurité historiques (le SI d’entreprise est un sanctuaire, tout le reste doit être bloqué sans discrimination) sont ancrés profondément dans les esprits. L’avènement du cloud & la massification du télétravail font qu’une refonte de l’approche de la sécurité est nécessaire pour proposer une expérience utilisateur moderne.

**Mes règles** :
- Ouvrez vos chakras car la sécurité ne se résume plus au sanctuaire du SI de l’entreprise et aux terminaux managés
- Définissez le niveau de sécurité minimal qui doit être appliqué dans telle ou telle conditions
- Contrôler afin de détecter des nouveaux usages et statuer sur la légitimité / non-légitimité
- Effectuer un suivi mensuel de l'évolution des conditions et des critères possibles sur l'*Azure AD Conditional Access*
- La constitution d’un écosystème intégré de sécurité et de gestion des identités et terminaux est un accélérateur

## La philosophie c’est bien, mais de manière pragmatique comment je m’y prends ?
Tout d’abord sur la base de mes retours d’expériences, l’approche progressive donne des succès probants plus facilement et rapidement qu’une approche exhaustive. Concrètement les premières politiques doivent être simple en se basant sur un volume faible de conditions, puis après les avoir éprouvées dans le contexte, l’ajout de conditions complémentaires peut être envisagé. Ainsi de manière itérative une maturité est acquise et cela permet aussi de mieux appréhender les nouvelles conditions qui pourraient être mise à disposition par Microsoft.
Ensuite, il est important de construire des règles qui s’inscrivent dans la philosophie du *Zero Trust* (cf.la règle 1) qui porte le principe de la confiance explicite synthétisée par les règles suivantes (contextualisées à l'*Azure AD Conditional Access*) :
* L’ensemble des éléments de la chaine de traitement participent à la sécurisation des accès
* Adaptabilité des décisions selon les conditions requises 
* Définit une matrice globale permettant d’assembler l’ensemble des conditions attendues pour autoriser les accès ou les bloquer (ci-dessous une exemple)

![Azure AD Conditional Access Template](/assets/images/AADCA_Template.png)

## La démonstration par l’exemple
Les cas décrit ci-dessous sont à titre descriptif. Dans la réalité il s’agira de cumuler l’ensemble de ces cas d’usage pour définir la politique d’accès conditionnelle globale.

- **Cas 1 : Je souhaite que les utilisateurs de l’entreprise puissent accéder aux services O365 avec une authentification forte et des clients lourds et web uniquement si je maitrise les terminaux.**

    - Explications
        - Les terminaux étant maitrisés (eg. politiques de configuration et protection déployées via Intune ou via GPO), les accès aux données reposant sur une authentification multificateur alors les données peuvent être librement consommées depuis les terminaux
        - La notion de maitrise repose sur le type de jointure effectuée (*Azure AD Join* et/ou *Hybrid Azure AD Join*) via la condition *Filter for Devices > TrustType*
    - Matrice
        
        ![Azure AD Conditional Access Cas1](/assets/images/AADCA_CAS1.png)
- **Cas 2 : Je souhaite que les utilisateurs de l’entreprise puissent accéder aux services O365 avec une authentification forte et des clients légers si je ne maitrise pas la sécurité des terminaux Windows.**
    - Explications
        - Cela permet de couvrir certains cas de compromissions comme un malware sur le poste qui via Outlook (client lourd) exfiltre l’annuaire d’entreprise ou effectue du phishing en mon nom
        - Cela permet de couvrir certains cas d’exfiltration de données via la récupération du cache Outlook
        - La notion de maitrise repose sur le type de jointure effectuée (Différent de *Azure AD Join* et *Hybrid Azure AD Join*) via la condition *Filter for Devices > TrustType*
    - Matrice
        ![Azure AD Conditional Access Cas2](/assets/images/AADCA_CAS2.png)
- **Cas 3 : Je souhaite que sur des postes de travail pour lesquels les critères de conformité sont validés, mes utilisateurs puissent utiliser les clients lourds et web sans authentification multifacteur, dans le cas contraire uniquement les client web seront autorisés avec une authentification multifacteur.**
    - Explications
        - Cela permet de garantir que les données sont consommés depuis un terminal présentant une exposition sécurité minime
        - Outre les politiques de conformité standards de Intune, il est possible d'ajouter des critères custom (eg. présence du client SASE, présence d'un certificat émis par l'autorité de certification de l'entreprise ...)
        - Cela permet de couvrir certains cas d’exfiltration de données via l'utilisation de failles de sécurité connues non corrigées
        - La notion de conformité repose sur la condition *Filter for Devices > IsCompliant*
    - Matrice
        ![Azure AD Conditional Access Cas3](/assets/images/AADCA_CAS3.png)
- **Cas 4 : Je souhaite que les accès à cette application requièrent une authentification forte quotidienne car il s’agit d’une application sensible.**
    - Explications
        - il s'agit d'un des mécanismes pour palier le vol de crédential en invalidant le jeton au bout de 12h et en reforcant l'authentification des utilisateurs
        - La notion de du durée de validité repose sur le contrôle *Session > Sign-in frequency*
        - La notion de ciblage de l'application est possible suite à l'enregistrement de l'application concernée dans le *SSO Azure AD*. Elle devra être sélectionnée dans *Cloud apps or actions*
    - Matrice
        ![Azure AD Conditional Access Cas4](/assets/images/AADCA_CAS4.png)
- **Cas 5 : Je veux autoriser uniquement les machines *Hybrid Azure AD Join* à se connecter aux services O365.**
    - Ce cas là n'est pas conforme aux concepts du *Zero Trust*, en effet
    	- Cela ne couvre pas le cas des machines en *Azure AD Join* et *AD Join*. Et par conséquent génère des effets de bords pour les accès depuis les serveurs (eg. les fermes RDS)
    	- Cela ne permet pas de garantir un niveau de sécurité. En effet un poste *Hybrid Azure AD Join* non conforme (eg. non patché depuis 6 mois) peut présenter un exposition sécurité supérieure à un poste non connu de l’entreprise mais conforme en terme de sécurité (eg. il dispose de la totalité des patches)
    	- Cela permet de s’assurer partiellement qu’uniquement les machines fournis par l’entreprise sont enrôlés et laisse de côté une partie du périmètre
    - La recommandation est de contraindre l’enrôlement des terminaux Windows principalement afin de définir une politique perenne (comme les futurs postes de travail enrôlés en Azure *AD Join* – autopilot)
    - Pour interdire l’enrôlement des postes de travail Windows personnel, deux méthodes complémentaires sont possibles
        - *AD Join* via la gestion des privilèges sur l'*AD*
        - *Azure AD Join* via une politique d’*Enrollement device platform restrictions*
- **Cas 6 : Je veux bloquer l’accès aux terminaux BYOD car je ne maitrise pas leur sécurité.**
    - Ce cas là n'est pas conforme aux concepts du *Zero Trust*, en effet
        - Il n’y a pas de lien direct entre terminal BYOD et le niveau de management et conformité
    - La recommandation est de définir les critères pour les deux cas suivants
        - Terminal BYOD managé
        - Terminal BYOD non managé
        - La notion de management pouvant reposer soit sur son enrôlement dans Intune, soit sur sa conformité
- **Cas 7 : Je ne veux pas que mes utilisateurs aient une authentification multifacteur uniquement en defors de mon réseau d'entreprise.**
    - Ce cas là n'est pas conforme aux concepts du *Zero Trust*, en effet
        - Dans le cadre de l'usage d'une solution SASE (*Proxy Cloud*), le réseau de l'entreprise est masqué par la solution SASE
        - Le Offloading de certains trafic (comme les flux audio/video temps réel de Teams) ne sera pas vu comme arrivant depuis le *Proxy* d'entreprise
    - La recommandation est de revenir sur des conditions standards pour définir l'activation ou non d'une authentification multifacteur


# TEST

Quelques idées de critères ultra avancés
- Inscriptions Zscaler
- Vérification de la conformité en intégrant le fait que Zscaler soit up -> Attention la conformité n'est pas live

Authentication context

 eeee
    - ffff

# 1
* toto 1
* toto 2
    * toto 2.1
    * toto 2.2
## 2
### 3
#### 4
##### 5 
