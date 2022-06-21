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

Dans les programmes de bascule des entreprises vers les services Cloud Collaboratifs de Microsoft (le bien nommé Office 365) la question de la sécurisation des accès aux services est bien souvent non adressée au bon niveau. En effet, la question de quelles politiques d’accès doivent être mises en place doit se poser en amont. Bien souvent la réponse est de vouloir bloquer les accès selon les cas d’usages hérités des politiques de sécurité appliquées sur les environnements OnPremise. Toutefois est-ce la bonne approche ?
L’objectif de cet article est de donner les clés pour comprendre les enjeux autour de la politique d’accès aux services Office 365 et d’expliciter certaines bonnes pratiques.

# Préambule

Au-delà des concepts généraux du modèle Zero Trust, Microsoft décline au travers de l’Azure AD Conditional Access, ce concept pour les accès aux services reposent sur l’Azure AD comme fournisseur d’identité (Identity Provider - IDP). Bien que relativement simple à son lancement (peu de critères), au fil du temps le Conditional Access a fortement évolué pour traiter un nombre important de cas d’usages. L’image ci-dessous est la situation à février 2022.

Outre son fonctionnement et ses bénéfices dont je parle ultérieurement, le mode de fonctionnement du Conditional Access lève des questions chez quasiment tous mes clients qui restent sur une approche défensive, c’est-à-dire sur le modèle historique du château fort.

L’objectif de cet article est d’apporter des explications sur le fonctionnement du Conditional Acces (qui devient incontournable dans tous programmes de Modern Management basés sur Intune, tous programmes de bascule vers Office 365 ou sur tous programmes de massification SSO Azure AD), les impacts sur le modèle de sécurité et de gouvernance de l’entreprise, et les bonnes pratiques pour en tirer le plus de bénéfices.

De manière globale, l’Azure AD Conditional Access se base sur le triptyque Signal, Décision et Contrôle représenté dans la cartographie suivante :

![Zero Trust Access by Microsoft Azure AD Conditional Access](/assets/images/MicrosoftZeroTrustAccess.png)

Source Microsoft : [Conditional Access design principles and dependencies - Azure Architecture Center](https://docs.microsoft.com/en-us/azure/architecture/guide/security/conditional-access-design)


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
