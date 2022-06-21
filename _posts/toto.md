---
title: CA ?
layout: post
post-image: /assets/images/ActiveDirectory.jpg
description: Mes convictions sur les pistes de transformation pour s'affranchir de l'Active Directory.
tags:
- Active Directory
- Azure Active Directory
- AD
- AAD
- Microsoft
---


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
