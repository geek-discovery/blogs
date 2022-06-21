---
title: CA ?
layout: post
post-image: /assets/images/ActiveDirectory.jpg
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
L’objectif de cet article est de donner les clés pour comprendre les enjeux autour de la politique d’accès aux services Office 365 et d’expliciter certaines bonnes pratiques.

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


# TEST


## La philosophie c’est bien, mais de manière pragmatique comment je m’y prends ?
Tout d’abord sur la base de mes retours d’expériences, l’approche progressive donne des succès probants plus facilement et rapidement qu’une approche exhaustive. Concrètement les premières politiques doivent être simple en se basant sur un volume faible de conditions, puis après les avoir éprouvées dans le contexte, l’ajout de conditions complémentaires peut être envisagé. Ainsi de manière itérative une maturité est acquise et cela permet aussi de mieux appréhender les nouvelles conditions qui pourraient être mise à disposition par Microsoft.
Ensuite, il est important de construire des règles qui s’inscrivent dans la philosophie du Zero Trust (cf.la règle 1) qui porte le principe de la confiance explicite synthétisée par les règles suivantes (contextualisées au Conditional Access) :
* L’ensemble des éléments de la chaine de traitement participent à la sécurisation des accès
* Adaptabilité des décisions selon les conditions requises

Et pour finir réfléchir sur une matrice permettant d’assembler l’ensemble des conditions attendues pour autoriser les accès ou les bloquer (ci-dessous une exemple)


- eeee
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
