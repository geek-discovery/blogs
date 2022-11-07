---
title: The Identity syndrome during a merger or divestiture program
layout: post
post-image: /assets/images/MA_Identity.png
description: Why identity is always a main painpoint during merger or divestiture program and how to properly address it ?
tags:
- M&A
- Merge and Acquisition
- Divestiture
- Identity
- HRIS
- AD
- AAD
- Active Directory
- Azure Active Directory
- IAM
- Identity and Access Management
---
Anytime I've performed a M&A or Divestiture program the 



La part prépondérante de l'Active Directory, l'arrivée d'Office 365, l'avènement des services Cloud, ouvre la voie de la transformation du service d'annuaire de Microsoft. J'ai souvent eu la question suivante "Est ce que je peux migrer mon Active Directory sur Azure Active Directory ?". La réponse n'est pas triviale du fait de la multiplicité des fonctions portées par l'Active Directory, de la philosophie d'Azure Active Directory qui ne reprend pas l'ensemble des fonctionalités de Active Directory tout en proposant des nouvelles et de la gouvernance de l'entreprise.

# Préambule

La nomenclature des annuaires pouvant prêter à confusion, une précision sur le vocabulaire est nécessaire pour clarifier la portée des transformations possibles.

* **Active Directory** : annuaire historique de Microsoft disponible depuis le debut des années 2000. Traditionnellement il est hébergé de manière OnPremise sous la forme de machines physiques ou virtuelles.
* **Active Directory Cloud** ou **Active Directory Azure** : terme utilisé pour modéliser l'hébergement de l'Active Directory au sein de fournisseurs de Cloud Public (tels que Azure ou AWS). Ce terme ne fait pas référence à une technologie mais à une situation d'hébergement.
* **Azure Active Directory** : annuaire Microsoft apparu avec les services Office 365 et les services *IaaS*, *PaaS*, *SaaS* fournis par Microsoft Azure. Cet annuaire est souvent assimilé à tord comme la transformation sous la forme d'un service *PaaS* de l'annuaire Active Directory.
* **Azure Active Directory Domain Services** : annuaire Active Directory fourni sous la forme d'un service *PaaS* par Microsoft. Les comptes utilisateurs sont provisionnés depuis Azure Active Directory.

# Le poids de l'histoire

A l'échelle des systèmes d'information, l'Active Directory (apparu en 2000) est un dinosaure. Au fil des années, il a pris un part prépondérante au sein des entreprises. Et de fait porte le poids de l'histoire avec des ajustements plus ou moins heureux en termes d'architecture, une gouvernance devenue au fil du temps inadaptée, des couts d'exploitation importants, et devient un frein aux transformations avec le paradigme *On ne change pas ce qui marche* (c) très souvent entendu chez mes clients.

**A titre d'exemple:**
* l'affaiblissement des règles de protection de l'Active Directory pour supporter des applications historiques est couramment rencontré et va à l'encontre des bonnes pratiques de sécurité
* la difficulté de réduire les systèmes d'authentification et le nombre de login / mot de passe utilisés quotidiennement
* gouvernance verticale des services ITs au sein des entreprises induisant une autonomie de chaque département les uns par rapport aux autres avec en transverse l'annuaire d'authentification. Le cas typique couramment rencontré est la gestion dissociée de l'Active Directory par la celulle identités et de l'Azure Active Directory par la cellule Office 365 rendant complexe la limite des roles & responsabilités : qui doit gérer l'Azure AD Connect, l'ADFS, l'usage de l'Azure AD comme IDP ... 

Au final, la volonté de basculer vers des services Cloud pour répondre aux enjeux de réductions de coûts, de simplification, d'amélioration de l'expérience utilisateur doit passer par une analyse fine de fonctionnalités portées par les fondations techniques dont l'Active Directory fait parti intégrante.

# Comment répondre à cette problématique ?

Le point de départ est de disposer d'une cartographie exhaustive des adhérences avec l'Active Directory afin de pouvoir traiter l'ensemble des sujets, qu'ils soient techniques, sécuritaires ou applicatifs. On constate ainsi l'intérêt de traiter cette question de manière globale. Un traitement partiel pouvant aboutir à un échec (le besoin de conservation d'un Active Directory pour traiter des adhérences est un échec en soit).

A titre d'exemple, ci-dessous une modélisation d'un système d'information. 

![Spaghetti Pasta](/assets/images/2021-06-01-MapEcosystème.png)

La complexité du sujet fait qu'il n'existe pas de chemin de transition unique (ce serait trop facile et cet article perdrait son intérêt). Ce chemin doit être adapté au contexte de chaque entreprise en particulier du fait qu'il n'est pas possible de réaliser une transition 1 pour 1 des différentes fonctionnalités.

Dans le but de mieux appréhender les sujets, les chapitres suivants ont pour objectif de proposer des recommandations et de mettre en évidence les écueils à éviter au travers des questions principales à se poser.


## **Question 1** : Quels organisation mettre en place pour apporter une vision globale et transverse ?

L'organisation à mettre en place pour accompagner de telles transformations doit avoir pour objectifs principaux de garantir la cohérence de l'architecture et le traitement des adhérences entre les différentes fondations. Un écueil couramment vécu dans les entreprises et que les propositions de design de chaque chantier sont validés au sein de chaque chantier sans tenir compte d'une vision transverse et allant au-delà du périmètre du chantier. La résultante de cette approche est qu'une complexité inutile apparait aux niveaux des interfaces voire même des incompatibilités.

Concrètement, le découpage des chantiers dans le cadre du transformation Active Directory vers Azure Active Directory doit se découper par besoins transverses et non plus par ligne managériale/produits. Le tout régie par une celulle de type **Design Authority** pour garantir une cohérence de l'architecture permettant de répondre à l'ensemble des besoins. Bien que bénéfique pour *le time to market*, la simplicité, la normalisation des interfaces, ce type d'approche nécessite un fort sponsorship managérial du fait de la remise en cause plus ou moins pprofonde de la hiérarchie établie dans l'entreprise ainsi que des processus d'approbations des évolutions.

Quelques exemples de découpage transverse des sujets permettant d'être en phase avec la transition vers Azure AD :
* **Stream Identité, Authentification & Habilitation :** afin de couvrir l'ensemble des objets au sein de l'Active Directory et/ou l'Azure AD, des traitements appliqués sur ces objets, des protocoles d'authentification, des méthodes d'habilitations et des cycles de vie de bout en bout.
* **Stream Collaboratif :** afin de couvrir l'ensemble des services collaboratifs ayant une adhérence avec l'Active Directory et/ou l'Azure AD.
* **Stream Gestion des terminaux :** afin de couvrir les solutions de gestion des devices et leur cycle de vie. Sur ce chantier un des enjeux importants dans le cadre de ce Stream est d'uniformiser l'outil de gestion de l'ensemble des terminaux (PC, Smartphones, tablettes ...), la bascule vers Azure AD apportant de nouvelles fonctionnalités (Gestion Intune, Conditional Access ...)

Outre le découpage selon différents Streams, il est nécessaire que ce découpage soit correctement mis en place lors des projets avec une comitologie globale au niveau du Stream et éviter les projets dans les projets du fait du cloisonnement des équipes entre elles ou la présence de plusieurs solutions/technologies dans l'écosystème. Ce découpage devrait être régie par une approche agile (avec un découpage par sprint) sans cloisonnement entre les équipes qui doivent fournir les ressources pour contribuer sur les sprints et devrait perdurer au delà des programmes de transformation afin de proposer les réponses pertinentes aux métiers et un *time to market* réduit.
La cellule **Design Authority** quand à elle sera garante que les choix d'architecture fait au sein de chaque Stream soient cohérents entre eux et conforme au schéma directeur IT.

**Indicateur d'effort de transformation**
* **Difficulté  :** importante
* **Charges     :** moyenne


## **Question 2** : Quel circuit de provisioning des objets ?

Dans un environnement de plus en complexe du fait de l'agrégation de nombreux services avec l'identité comme pierre angulaire, l'automatisation du (de)provisioning doit être la norme afin de réduire les délais et les coûts de support. Il est donc nécessaire de définir les cycles de vie suivant pour ensuite pouvoir automatiser la gestion des objets concernés :
* Quel est le cycle de vie des **utilisateurs internes** de l'entreprise ?
* Quel est le cycle de vie de **utilisateurs sous contrats** (les prestataires, les freelances, ...) ?
* Quel est le cycle de vie des **utilisateurs *Guests*** sur le tenant Office 365 ?
* Quel est le cycle de vie des objets **non-utilisateurs** hébergés sur Active Directory (Pur Active Directory, Synchronisés sur Azure AD) et/ou sur Azure AD (Objets uniquement cloud) ? 
* Quel est le cycle de vie des **administrateurs** ?
