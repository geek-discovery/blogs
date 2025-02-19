---
title: Comment s'y prendre avec Azure AD Conditional Access ?
layout: post
post-image: /assets/images/CloudSecurity.jpg
description: Comment s'y prendre avec Azure AD Conditional Access ?
tags:
- Azure Active Directory
- Conditional Access
- AAD
- Zero Trust
- Zero-Trust
- Microsoft
---

Dans les programmes de bascule des entreprises vers les services Cloud Collaboratifs de Microsoft (le bien nommé Office 365) la question de la sécurisation des accès aux services est bien souvent non adressée au bon niveau. En effet, la question de quelles politiques d’accès doivent être mises en place doit se poser en amont. Bien souvent la réponse est de vouloir bloquer les accès selon les cas d’usages hérités des politiques de sécurité appliquées sur les environnements *OnPremise*. Toutefois est-ce la bonne approche ?  
L’objectif de cet article est de donner les clés (sans pour autant entrer dans l'ensemble des subtilités) pour comprendre les enjeux autour de la politique d’accès aux services Office 365, aux applicatifs utilisant le SSO Azure AD et d’expliciter certaines bonnes pratiques.

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
- Le sujet va delà d'Office 365 et requiert une vision transverse
- La constitution d’un écosystème intégré de sécurité et de gestion des identités et terminaux est un accélérateur

## La philosophie c’est bien, mais de manière pragmatique comment je m’y prends ?
Tout d’abord sur la base de mes retours d’expériences, l’approche progressive donne des succès probants plus facilement et rapidement qu’une approche exhaustive. Concrètement les premières politiques doivent être simple en se basant sur un volume faible de conditions, puis après les avoir éprouvées dans le contexte, l’ajout de conditions complémentaires peut être envisagé. Ainsi de manière itérative une maturité est acquise et cela permet aussi de mieux appréhender les nouvelles conditions qui pourraient être mise à disposition par Microsoft.
Ensuite, il est important de construire des règles qui s’inscrivent dans la philosophie du *Zero Trust* (cf.la règle 1) qui porte le principe de la confiance explicite synthétisée par les règles suivantes (contextualisées à l'*Azure AD Conditional Access*) :
* L’ensemble des éléments de la chaine de traitement participent à la sécurisation des accès
* Adaptabilité des décisions selon les conditions requises 
* Définit une matrice globale permettant d’assembler l’ensemble des conditions attendues pour autoriser les accès ou les bloquer (ci-dessous une exemple)

![Azure AD Conditional Access Template](/assets/images/AADCA_Template.png)

## La démonstration par l’exemple
Les cas types (non exhaustif) ci-dessous sont à titre descriptif. Dans la réalité il s’agira de cumuler l’ensemble de ces cas d’usage pour définir la politique d’accès conditionnelle globale.

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
        - La notion de durée de validité repose sur le contrôle *Session > Sign-in frequency*
        - La notion de ciblage de l'application est possible suite à l'enregistrement de l'application concernée dans le *SSO Azure AD*. Elle devra être sélectionnée dans *Cloud apps or actions*
    - Matrice
        
        ![Azure AD Conditional Access Cas4](/assets/images/AADCA_CAS4.png)

- **Cas 5 : Je souhaite que les utilisateurs s’authentifient de manière forte et quotidiennement lorsque l’utilisateur accède aux applications et services depuis l’étranger car je n’accorde pas confiance aux opérateurs étrangers.**

    - <div style="text-align: left">Explications</div>
        - il s'agit d'un des mécanismes pour palier la faiblesse de la protection du transport via opérateurs mobiles ou internet dans les pays étrangers
        - La notion de localisation repose sur la condition *Locations > Selected locations*
    - <div style="text-align: left">Matrice</div>
        
        ![Azure AD Conditional Access Cas5](/assets/images/AADCA_CAS5.png)

- **Cas 6 : Je veux autoriser uniquement les machines *Hybrid Azure AD Join* à se connecter aux services O365.**

    - <div style="text-align: left">Ce cas là n'est pas conforme aux concepts du <em>Zero Trust</em>, en effet</div>
        - Cela ne couvre pas le cas des machines en *Azure AD Join* et *AD Join*. Et par conséquent génère des effets de bords pour les accès depuis les serveurs (eg. les fermes RDS)
        - Cela ne permet pas de garantir un niveau de sécurité. En effet un poste *Hybrid Azure AD Join* non conforme (eg. non patché depuis 6 mois) peut présenter un exposition sécurité supérieure à un poste non connu de l’entreprise mais conforme en terme de sécurité (eg. il dispose de la totalité des patches)
        - Cela permet de s’assurer partiellement qu’uniquement les machines fournis par l’entreprise sont enrôlés et laisse de côté une partie du périmètre
    - <div style="text-align: left">La recommandation est de contraindre l’enrôlement des terminaux Windows principalement afin de définir une politique perenne (comme les futurs postes de travail enrôlés en Azure <em>AD Join</em> et/ou via autopilot)</div>
    - <div style="text-align: left">Pour interdire l’enrôlement des postes de travail Windows personnel, deux méthodes complémentaires sont possibles</div>
        - *AD Join* via la gestion des privilèges sur l'*AD*
        - *Azure AD Join* via une politique d’*Enrollement device platform restrictions*  
<br/>
- **Cas 7 : Je veux bloquer l’accès aux terminaux BYOD car je ne maitrise pas leur sécurité.**

    - <div style="text-align: left">Ce cas là n'est pas conforme aux concepts du <em>Zero Trust</em>, en effet</div>
        - Il n’y a pas de lien direct entre terminal BYOD et le niveau de management et conformité
    - <div style="text-align: left">La recommandation est de définir les critères pour les deux cas suivants</div>
        - Terminal BYOD managé
        - Terminal BYOD non managé
        - La notion de management pouvant reposer soit sur son enrôlement dans Intune, soit sur sa conformité.
        - L'usage de politiques MAM appliquées (de manière forcée ou non) sur des applications compatibles peut aussi être une solution possible avec l'*Azure AD Conditional Access*  
<br/>
- **Cas 8 : Je ne veux pas que mes utilisateurs aient une authentification multifacteur lorsqu'ils sont sur le réseau d'entreprise.**

    - <div style="text-align: left">Ce cas là n'est pas conforme aux concepts du <em>Zero Trust</em>, en effet</div>
        - Dans le cadre de l'usage d'une solution SASE (*Proxy Cloud*), le réseau de l'entreprise est masqué par la solution SASE
        - Lors de la bascule des politiques de sécurité selon les concepts du *Zero Trust* (à contrario du modèle de forteresse), la notion de réseau d'entreprise et réseau public n'a plus lieu d'être
        - Le *Offloading* de certains trafic (comme les flux audio/video temps réel de Teams) ne sera pas vu comme arrivant depuis le *Proxy* d'entreprise
    - <div style="text-align: left">La recommandation est de revenir sur des conditions standards pour définir l'activation ou non d'une authentification multifacteur</div>

## Quelques cas avancés
Certaines fonctionnalités peu utilisées de l’Azure AD Conditional Access peuvent toutefois apportées des bénéfices supplémentaires. Toutefois, leur usage requiert un bon niveau de maitrise (et potentiellement des licenses avancées) :
* Les **contextes d'authentification** : ils permettent d'instancier des critères d'accès spécifiques selon l'étiquette de sensibilité d'un *Group O365* ou d'un site *SharePoint*. Par exemple lors de l'accès à un site *SharePoint* classifié restreint, une authentification forte toutes les 8 heures pourra être requise, tandis qu'une authentification simple toutes les 36 heures pourra être requise pour les autres sites *SharePoint*
* Demande automatisé de **changement de mot de passe** : afin de réagir automatiquement en cas de suspicion de compromission de comptes utilisateurs ou d’activités suspectes, il est possible de forcer la demande de modification du mot de passe du compte de l’utilisateur. Ce fonctionnement repose sur l’usage des conditions *Sign-In risk level* et *User risk level*. Ces deux signaux sont classifiés de manière automatique par le moteur de sécurité de l’*Azure AD*. De ce fait en utilisant des niveaux élevés de risques dans les conditions, il est possible de demander automatiquement le changement de mot de passe. Les niveaux de risques seront alors ensuite diminués.
* Usage de **Microsoft Defender for Cloud Apps** : ce service permet d’appliquer des contrôles plus poussés comme les actions effectuées sur les données, la vérification de la présence d’un certificat émis par l’entreprise, etc … toutefois le niveau de possibilité permis implique une maitrise de la solution.
* Validation de la **Charte Utilisateur** : afin de s’assurer que les utilisateurs aient pris connaissance de la Charte d’accès aux systèmes d’informations de l’entreprise il est possible de mettre en place une règle sur l’*Azure AD Conditional Access* obligeant l’utilisateur à la valider. Tant que cette validation n’est pas réalisée, les accès seront bloqués (en particulier pour les populations prestataires, ce cas d’usage prend tout son sens). A noter que la validité de l’approbation de la Charte peut être conditionnée à une durée.

# Bénéfices et limites du Conditional Access
Bien qu’un effort intellectuel soit nécessaire pour conceptualiser l’ensemble des règles à mettre en place, l’intérêt majeur réside dans la consolidation en un endroit unique des politiques d’accès. Cela permet en autres de s’affranchir mesures de protection périmtétriques (comme des règles de filtrage sur les *firewalls* et autres *VPN*) et de proposer aux utilisateurs quasi systématiquement un environnement similaire en situation de travail à distance et au sein du réseau d’entreprise ainsi que d’adapter les règles d’accès selon le niveau d’atteinte des conditions (au lieu du blocage systématique)

Néanmoins certaines limites existent
* Afin de s’affranchir de la protection traditionnelle du réseau de transport (via *VPN* principalement ou mécanismes propres au flux applicatif) et se concentrer uniquement sur les conditions d’accès, il est nécessaire que ces flux réposent sur le protocole standard (*http*), avec une sécurisation basée sur les standards actuels (*TLS1.2+*) et des algorithmes de chiffrement réputés fiable (tel que *AES*) et déprécier les algorithmes de chiffrement réputés peu fiables (tel que *3DES*) 
* L’usage de l’*Azure AD Conditional Access* requiert L’intégration des apps dans le *SSO Azure AD* ou éligibles avec *Azure AD Proxy*. Pour ce dernier les critères de compatibilité sont décrit dans cet article [Remote access to on-premises apps - Azure AD Application Proxy](https://docs.microsoft.com/en-us/azure/active-directory/app-proxy/application-proxy#what-is-application-proxy)

# Quelques *tips* pour concevoir les règles de l’*Azure AD Conditional Access*
- <div style="text-align: left">Les <strong>règles</strong> sont <strong>cumulatives</strong> : de ce fait, il est possible de concevoir les règles selon deux grands principes</div>
    - L’**inclusivité** par recouvrement des règles. Dans cette approche, unitairement chaque règle est simple (par exemple une règle pour définir la durée de vie de jeton, une règle pour définir le comportement pour chaque système d’exploitation, une règle pour définir le comportement selon le niveau de conformité …), et le comportement final sera obtenu par le cumul des règles. Le bénéfice de cette approche est qu’il est plus aisé de traiter les angles morts mais les recouvrements peuvent générer des effets de bords inattendus.
    - L’**exclusivité des règles**. Dans cette approche, unitairement chaque règle est complexe et des exclusions sont appliquées afin de ne pas obtenir de recouvrement vis-à-vis des autres règles. Chaque accès sera autorisé par une seule règle. Le bénéfice de cette approche est qu’elle génère une facilité dans le diagnostic d’erreurs, toutefois leurs conceptions peut être couteuse.

- Effectuer des **tests comportementaux** à l’aide de la fonction *What If*. Elle permet d’identifier les règles de l’*Azure AD Conditional Access* qui s’activeront lors de différentes conditions. Je recommande d’abuser de cette fonction afin de s’assurer que le comportement obtenu est aligné avec le comportement souhaité. Elle apportera toute sa valeur en particulier dans l’approche de conception inclusive.

- La mise en place de l’authentification forte (*MFA*) peut nécessiter des **campagnes d’enregistrement en masse du second facteur** (Microsoft Authenticator). Selon la durée de vie des jetons, il peut être difficile de gérer la temporalité de cette action utilisateur (et d’aligner les ressources nécessaires au sein du Support Utilisateur) lorsqu’elle est uniquement conditionnée par une règle de l’*Azure AD Conditional Access*. L’usage de la fonctionnalité *Registration Campaign* permet de mieux gérer la temporalité des enrôlements en masse.

- **Adhérences** entre l’*Azure AD MFA* et *Office 365 MFA* : bien qu’en termes d’expérience utilisateur les deux solutions sont similaires, dans les faits le comportement lors de l’authentification des utilisateurs est différent. En effet, uniquement l’*Azure AD MFA* est instancié par une règle de l’*Azure AD Conditional Acces* lorsque l’authentification multi-facteur est requise. Par conséquent, je recommande, pour éviter tout effet de bords, de laisser à la configuration d’origine l’*Office 365 MFA* lorsque des règles de l’*Azure AD Conditional Access* requièrent l’authentification multi-facteur.

- <div style="text-align: left">Les critères de <strong>localisation</strong> peuvent désormais reposer sur trois types. Chacun peut apporter des bénéfices et des limites</div>
    - via l’usage des **coordonnées GPS** : le gain principal est la précision de la localisation de l’utilisateur obtenu par des données objectives. Néanmoins l’usage est uniquement possible si le terminal dispose d’une capacité de localisation via GPS (principalement les *Smartphones* et rarement les postes de travail).
    - via l’usage de **segments réseaux IP de types Pays** : le gain principal est qu’il reste facile de choisir les pays. Toutefois il s’agit de données disposant d’une fiabilité relativement moyenne car elles soumises à la qualité des données déclarées par les opérateurs.
    - via la définition de **segments IP Publics spécifiques** : le gain principal est qu’une entreprise dispose de la possibilité de construire une table en liaison avec l’adressage IP (NAT) de leurs accès Internet.  Néanmoins la limite principale de ce type est complètement conditionnée par l’architecture des accès Internet (*Offloading du traffic* en local sur les sites, solutions *SASE* qui ne dédient pas des adresses IP Publiques à leur clients …)

- Pour des cas spécifiques nécessitant une **gestion fine** (en particulier pour cibler des applications natives Microsoft bien spécifiques) des règles de l’*Azure AD Conditional Access*, il est possible de les configurer au travers de Graph API – [ConditionalAccessPolicy resource type - Microsoft Graph v1.0](https://docs.microsoft.com/en-us/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- Pour des **besoins spécifiques** (en particulier certains comptes de services), il peut être nécessaire de les **exclure des règles** de l’*Azure AD Conditional Access*. Je recommande d’ajouter l’ensemble de ces comptes dans un groupe d’exclusion, d’appliquer l’exclusion sur la base de ce groupe et de manière régulière d’effectuer une revue de ce groupe

- <div style="text-align: left">Selon les conditions requises par les règles de l’<em>Azure AD Conditional Access</em> des <strong>limites</strong> existent qu’il faut prendre en compte pour les accès depuis les <strong>navigateurs</strong></div>
    - L’usage du mode *InPrivate* ou *Incoginito* ne permet pas de remonter toutes les informations pour évaluer les conditions
    - Pour qu’*Edge Chromium* remonte l’exhaustivité des informations pour évaluer les conditions, il est nécessaire que le profil au niveau du navigateur de l’utilisateur soit authentifié
    - Pour que *Google Chrome* remonte l’exhaustivité des informations pour évaluer les conditions, il est nécessaire que le module *Windows Account* fournis par Microsoft soit installé

# Conclusion
* Embrasser l’*Azure AD Conditional Access* requiert un certain effort. J’ai pu constater au travers de mes précédentes missions clients, que la maitrise de cette solution est relativement **faible**, trop souvent réduite à l’activation de l’*Azure AD MFA* pour l’accès à *Office 365* et bien loin des concepts du *Zéro Trust*.
* Toutefois une réelle **appropriation** des concepts permet de définir une réelle **trajectoire de simplification et d’amélioration de l’expérience utilisateur** tout en gagnant en **maîtrise** et en **réduction de la surface d’exposition**. L’*Azure AD Conditionnal Access* permet d’aller au-delà du contrôle subjectif (les règles de filtrage de partout) .

