---
title: Peut on remplacer Active Directory par Azure Active Directory ?
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
L'avènement des services Cloud, en particulier avec le positionnement central d'Office 365 au sein des entreprises, pose la question de l'usage et du positionnement de l'Active Directory au sein des entereprises.
J'ai souvent eu la question suivante "Est ce que je peux migrer mon Active Directory sur Azure Active Directory ?". La réponse n'est pas triviale du fait de la multiplicité des fonctions portées par l'Active Directory, de la philosophie d'Azure Active Directory qui ne reprend pas l'ensemble des fonctionalités de Active Directory tout en proposant des nouvelles et de la gouvernance de l'entreprise.

# Préambule

La nomenclature des annuaires pouvant pouvant préter à confusion, une précision sur le vocabulaire est nécessaire pour clarifier la portée des transformations possibles.

* **Active Directory** : annuaire historique de Microsoft disponible depuis le debut des années 2000. Traditionnellement il est hébergé de manière OnPremise sous la forme de machines physiques ou virtualles.
* **Active Directory Cloud** ou **Acive Directory Azure** : terme utilisé pour modéliser l'hébergement de l'Active Directory au sein de fournisseurs de Cloud Public (tels que Azure ou AWS). Ce terme ne fait pas référence à une technologie mais à une situation d'hébergement.
* **Azure Active Directory** : annuaire Microsoft apparu avec les services Office 365 et les services *IaaS*, *PaaS*, *SaaS* fourni par Microsoft Azure. Cet annuaire est souvent assimilé à tord comme la transformation sous la forme d'un service *PaaS* de l'annuaire Active Directory.
* **Azure Active Directory Domain Services** : annuaire Active Directory fourni sous la forme d'un service *PaaS* par Microsoft. Les comptes utilisateurs sont provisionnés depuis Azure Active Directory.

# Pourquoi cette question devrait se poser ?

Historiquement la gouvernance des services ITs au sein des entreprise se décline de manière verticale induisant une autonomie de chaque services les uns par rapport aux autres avec en transverse l'annuaire d'authentification. Au fil des années cette approche cloisonnée a pu générer une complexité (et par conséquents des coûts) non négiligeable en particulier pour les interfaces entre les silos et/ou garantir la rétrocompatibilité. 

**A titre d'exemple:**
* l'affaiblissement des regles de protection de l'Active Directory pour supporter des applications historiques est couramment rencontré et va à l'encontre des bonnes pratiques de sécurité
* la gestion dissociée de l'Active Directory par la celulle identités et l'Azure Active Directory par la cellule Office 365 rendant complexe la limite des roles & responsabilités : qui doit gérer l'Azure AD Connect, l'ADFS, l'usage de l'Azure AD comme IDP ... 
* la difficulté de réduire les systèmes d'authentification et le nombre de login / mot de passe utilisés quotidiennement

Au final, la volonté de basculer vers des services Cloud pour répondre aux enjeux de réducations de coûts, de simplification, d'amélioration de l'expérience utilisateur doit passer par une analyse fine de fonctionnalités portées par les fondations techniques dont l'Active Directory fait parti intégrante.

# Comment répondre à cette problématique ?

A titre d'exemple, ci dessous une modélisation d'un système d'information. Les constats sont qu'il y a des interdépendances sur l'ensemble des foundations. Quelles soient techniques, sécurité et applicatives.

Les chapitres suivant ont pour objectif de proposer des recommandations sur les solutions de transformation envisageables et les eceuils à éviter.

![Spaghetti Pasta](/assets/images/2021-06-01-MapEcosystème.png)


## **Question 1** : Quels organisation mettre en place pour apporter une vision globale et transverse ?

L'organisation à mettre en place pour accompagner de telles transformations doit avoir pour objectifs principaux de garantir la cohérence de l'architecture et le traitement des adhérences entre les différentes foundations. Un ecueil courrament vécu dans les entreprises et que les propositions de design de chaque chantier sont validés au sein de chaque chantier sans tenir compte d'une vision transverse et allant au delà du périmètre du chantier. La résultante de cette approche est qu'une complexité inutile apparait aux niveaux des interfaces voire même des incompatibilités.

Concrètement, le découpage des chantiers dans le cadre du transformation Active Directory vers Azure Active Directory doit se découper par besoins transverses et non plus par ligne managériale/produits. Le tout régie par une celulle de type **Design Authority** pour garantir une cohérence de l'architecture permettant de répondre à l'ensemble des besoins. Bien que bénéfique pour *le time to market*, la simplicité, la normalisation des interfaces, ce type d'approche nécessite un fort sponsorship managérial du fait de la remise en cause plus ou moins profondre de la hierarchie établie dans l'entreprise ainsi que des processus d'approbations des évolutions.

Quelques examples de découpage transverse des sujets permettant d'être en phase avec la transition vers Azure AD :
* **Stream Identité, Authentification & Habilitation :** afin de couvrir l'ensemble des objets au sein de l'Active Directory et/ou l'Azure AD, des traitements appliqués sur ces objets, des protocoles d'authentification, des méthodes d'habilitations et des cycles de vie de bout en bout.
* **Stream Collaboratif :** afin de couvrir l'ensemble des services collaboratifs ayant une adhérence avec l'Active Directory et/ou l'Azure AD.
* **Stream Gestion des terminaux :** afin de couvrir les solutions de gestion des devices et leur cycle de vie. Sur ce chantier un des enjeux important dans le cadre de ce stream est d'uniformiser l'outil de gestion de l'ensemble des terminaux (PC, Smartphones, tablettes ...), la bascule vers Azure AD apportant de nouvelles fonctionnalités (Gestion Intune, Conditional Access ...)

Outre le découpage selon différents Streams, il est nécessaire que ce découpage soit correctement mis en place lors des projets avec une comitologie globale au niveau du Stream et éviter les projets dans les projets du fait du cloisonnement des équipes entre elles ou la présence de plusieurs solutions/technologies dans l'écosystème. Ce découpage devrait être régie par une approche agile (avec un découpage par sprint) sans cloisonnement entre les équipes qui doivent fournir les ressources pour contribuer sur les sprints et devrait perdurer au delà des programmes de transformation afin de proposer les réponses pertinentes aux métiers et un *time to market* réduit.
La celulle **Design Authority** quand à elle sera garante que les choix d'architecture fait au sein de chaque Stream soient cohérents entre eux et conforme au schéma directeur IT.

**Indicateur d'effort de transformation**
* **Difficulté  :** importante
* **Charges     :** moyenne


## **Question 2** : Quel circuit de provisionning des objets ?

Dans un environnement de plus en complexe du fait de l'aggrégation de nombreux services avec l'identité comme pierre angulaire, l'automatisation du (de)provisionning doit être la norme afin de réduire les délais et les coûts de support. Il est donc nécessaire de définir les cycles de vie suivant pour ensuite pouvoir automatiser la gestion des objets concernés :
* Quel est le cycle de vie des **utilisateurs internes** de l'entreprise ?
* Quel est le cycle de vie de **utilisateurs sous contrats** (les prestataires, les freelances, ...) ?
* Quel est le cycle de vie des **utilisateurs *Guests*** sur le tenant Office 365 ?
* Quel est le cycle de vie des objets **non utilisateurs** hébergés sur Active Directory (Pur Active Directory, Synchronisés sur Azure AD) et/ou sur Azure AD (Objets uniquement cloud) ? 

De manière usuelle, chacune des populations d'utilisateurs disposent de son propre cycle de vie, induisant une complexité au niveau des outils (1 outil par population) voire même une gestion manuelle. Le cas le plus représentatif étant la gestion des utilisateurs *Guests* sur le tenant Office 365.

![Q2a](/assets/images/AD_Q1a.png)

Les objets non utilisateurs (tel que les groupes de sécurité, les listes de distribution ...) sont quand à eux historiquement provisionnés dans l'Active Directory puis synchronisés dans l'Azure AD. L'arrivé d'Office 365 soulève plusieurs problématiques structurantes pour apporter une gestion efficace de ces objets. En effet :
* Certains objets comme les **comptes de ressources** ou les **boites aux lettres partagées** doivent subir une adaptation post-provisionning depuis l'Active Directory
* Certains objets comme les **listes de distributions**, **les boites aux lettres partagées**, les **comptes de ressources** ne doivent pas être provisionnés depuis l'Active Directory afin de proposer des capacités de gestion aux titulaires de ces objets (eg. en tant que propriétaires d'une liste de distribution, je veux pouvoir modifier les membres depuis mon client Outlook)
* Certains objets comme les comptes ***Guests*** ou les ***Groups O365*** n'ont pas d'existances sur Active Directory

Outre l'aspect annuaire Active Directory & Azure AD, le traitement de cette question implique de concevoir le cycle de vie de bout en bout en prennant en compte les outils amonts aux annuaires d'identité (eg. référentiel RH), les outils de gestion des demandes et d'automatisation des modifications (ITSM), la structure de gouvernance de l'entreprise qui conditionne le modèle de déléguation (eg. combien d'équipes doivent manager les objets, combien de périmètres de responsabilité ...). Dans le cas d'une transition complète vers Azure AD, la transformation de cet écosystème est à prendre en compte.

### Plusieurs scénarios permettent toutefois de traiter cette question

#### **Usage d'un outil inhouse dédié**

Cette solution permet de proposer via un moteur de traitement dédié, mis à disposition par les équipes en charge de la gestion des identités, de connecteurs Powershell et/ou Graph vers Active Directory et l'Azure AD afin de soumettre des traitement via des fichiers standardisés. Bien que cet outil puisse être percu comme un nouvel outil dans l'entreprise (et de faits des côuts complémentaires de dévellopement, évolution, maintien en condition opérationnelle) il apporte les bénéfices suivants :
* facilité d'évolution pour la prise en charge de nouvelles fonctionnalités du fait de la maitrise de l'ensemble des briques par les équipes identités
* possibilité de disposer d'un modèle de déléguation granulaire adapté au modèle de gouvernance des entreprises via la déclinaison du modèle de déléguation au niveau de l'outil
* réduit les besoins d'accès aux consoles de management Azure AD / Office 365
* gestion exhaustive de tous types de d'objets
* permet de provisionner les objets au sein de l'annuaire le plus pertinent
* possibilité de traitement en masse
* possibilité d'interfacage via fichier à plat avec un ITSM

**Indicateur d'effort de transformation**
* **Difficulté  :** moyenne
* **Charges     :** moyenne

#### **Usage d'un référentiel technique (type IAM)**

Dans le cas où l'entreprise dispose d'une outil de gestion des identités et d'habilitations, l'intéret de le faire évoluer afin d'atteindre les objectifs en terme de gestion exhaustive des objets permet de s'affranchir de la mise en place d'un outil dédié. Neanmoins l'effort d'évolutions n'est généralement pas neutre, du fait qu'il faut mettre en place un connecteur PowerShell / GraphAPI enntre cet outil et l'Azure AD, ce type de connecteur reposant sur des protocoles de communications différents de ceux utilisés avec Active Directory.

En termes de bénéfices, la plupart de ceux apportés par la mise en place d'un outil dédié sont repris et des nouveaux peuvent aussi être apportés comme :
* la capacité de mise en place de workflows
* la mise à disposition d'interfaces accessibles et compréhensibles pour toute la population (IT et non IT)

Neanmoins, du fait de la position transverse d'un tel outil au sein de l'entreprise, la prise en compte de nouveaux besoins est moins évidente du fait des instances de validation généralement plus lourde que pour un outil autonome.

**Indicateur d'effort de transformation**
* **Difficulté  :** importante
* **Charges     :** moyenne

#### **Fusion du référentiel RH et du référentiel technique**

Ce scénario repose sur la fusion de l'outil en charge de la gestion du référentiel RH et du référentiel technique. Bien qu'il permette la rationalisation des outils, il requiert une maturité forte sur la gestion du cycle de vie des employées et des objets ainqi que le décloisonement des équipes en charge des outils. Les coûts engendrés ainsi que les impacts organisationels étant non négligeables ce scénario est rarement rencontré.

**Indicateur d'effort de transformation**
* **Difficulté  :** importante
* **Charges     :** importante

**A noter** que le cas spécifique des comptes utilisateurs *Guests*, la possibilité d'appliquer une date d'expiration sur ce type de comptes est apparue début 2021.


## **Question 3** : Comment gérer les habilitations des utilisateurs ?

La gestion uniformisée et automatisée des habilitations est un corollaire à la question précédente.
Bien que les mécanimes de fourniture de permissions sur l'Active Directory & l'Azure AD soit similaires - ajout d'un utilisateur dans un groupe - le passage à l'Azure AD est l'occasion de réaliser une mise à niveau des processus d'habilitations afin d'être aligné avec les standards - comme le respect des différentes reglèmentations requierant de la tracabilité.

### Quels sont les enjeux à traiter ?

La maitrise des habilitations et de l'accès aux données sous-jacentes requiert le traitement des enjeux suivants:
* Comment provisionner et déprovisionner les habilitations selon les profils et/ou pour des utilisateurs *Guests* ?
* Comment connaitre les données accédées par les utilisateurs et/ou les données dont sont propriétaires les utilisateurs ?
* Comment m'assurer que des données ne sont pas perdues lors du départ d'un collaborateur ?

### Comment décliner ces besoins sur Azure AD ?

Le point de départ est de profiler les différents utilisateurs selon des critères objectifs. Ce profil permettra ensuite de déclencher les automatismes de fourniture des habilitations selon les profils:
* Identification des différents profils métiers (IT, RH, Finance, Magasins ...) et sous profils métiers (IT Workplace, IT Networks, Magasins Vendeurs, Magasins Directeurs ...)
* Référencement dans l'outil RH de ces différents profils sous la forme de valeurs standardisées avec une table de correspondance. Par exemple 01-001 pour IT (01) Workplace (001)
* Provisionning de ces valeurs dans les attributs *custom* dans lesquels les valeurs sont à la discrétion de l'entreprise (par exemple extensionAttribute01 ou via des attributs customs spécifiquements créés)
* Identification des ressources éligible à chaque profil et sous profil métiers (applications, liste de distribution, ....)
* Création d'un groupe dynamique avec une règle de remplissage automatique basée sur les profils pour l'habilitation aux ressources concernées
* Déploiement des habilitations au travers groupes dynamiques sur les applications concernées. Ces dernières peuvent être de tout types (services O365, applications intégrées à Azure AD, fourniture des licenses ...)

La mise en place de ces groupes dynamiques est à revoir lorsque des évolutions de profils métiers sont constatés.
Les bénéfices de cette approche sont:
* que lorsqu'une personne quitte l'entreprise ou lorsqu'une personne fait une mobilité interne les habilitations liées aux profils sont automatiquement rectifiées
* que les fonctions natives d'Azure AD sont utilisés
Neanmoins les permissions spécifiques doivent quand à elle être traitées via le cycle des différents objets (et par conséquent au travers de la solution définie) afin de minimiser les actions directement sur les objets via IHM / PowersShell et par conséquent la réduction de la charge des équipes support ainsi que les erreurs humaines.

Outre les fonctions natives présentées préalablement, d'autres mécanismes natifs à Azure AD, en particulier pour les comptes *Guests* qui reposent sur un circuit de gestion spécifique, permettent d'automatiser le déprovisionning automatique des habilitations :
* durée de vie des comptes *Guests* au bout duquel le compte (et les habilitations associées) est automatiquement supprimée
* *Access Package* pour limiter dans le temps l'accès aux ressources lorsque les habilitations sont fournies au travers de groupes de sécurité

Le dernier enjeux est de garantir la cohérence entre les habilitations fournies et leur validité. En particulier lors des mobilités internes ou de départ de l'entreprise.
* Azure AD propose la fonctionnalité d'*Access Review* qui permet de simplifier (sans passer par l'analyse fastidieuse des logs) la revue des utilisateurs ayant accédés à des données. Bien qu'actuellement limitée à des Teams, des groups, ou des applications intégrées à Azure AD (Azure AD se positionnant comme le référentiel d'authentification), cette fonctionnalitée permet de limiter la durée nécessaire des phases d'audits.   
* En complément de cette précédente fonction, l'usage d'un script pour auditer les services non couverts (tel que les habilitations sur les boites aux lettres partagées) permet d'obtenir une vue 360 degrée. Dans une optique d'automatisation, ce script peut être intégré sous la forme d'une *Azure Function*.

**Indicateur d'effort de transformation**
* **Difficulté  :** faible
* **Charges     :** faible

## **Question 4** : Quel impact pour la gestion des terminaux ?

La transition vers l'Azure AD pose un challenge important pour la gestion des terminaux du fait que les outils traditionnels (type SCCM, Ivanti ...) ne peuvent pas s'interfacer avec l'Azure AD mais ouvre la porte à des simplifications et des nouveaux modèles de delivery des terminaux concrétisés par le Modern Management. Concrètement il s'agit d'adresser des chantiers pour l'ensemble des terminaux (PCs, MacOS, iOS, Android) afin d'uniformiser la gestion de leur cycle de vie.

### Chantier à adresser pour les PCs :

* **Transition de l'usine de préparation des PCs vers Windows Autopilot :** permettant de préparer les PCs 
* **Gestion de la configuration des 
* **Intégration des applications dans le centre logiciel de la  

* Transition de la configuration des terminaux Windows via GPO au travers de profils de configuration MDM

### Chantier à adresser pour les MacOS, iOS, Android :




## **Question 5** : Quel impact sur les services techniques ?

VPN

## **Question 6** : Quel impact sur les applicatifs ?




## **Question 5** : Est ce une opportunité pour simplifier la vie des utilisateurs ?




## **Question X** : Comment protéger mon système d'informations ?

> L'évolution des menaces Zero Trust EDR ..
> Le Byod

# Conclusion

Par conséquent, Son abandon par les entreprises ne peut être réalisable sans la définition d'un plan stratégique et l'ajustement de la gouvernance du système d'informations et la revue 
La traitement de toutes les questions précédentes permet d'envisager le passage vers Azure AD et de s'affranchir de l'AD. Neanmoins le non traitement d'une des questions impliquent la conservation de l'AD
Les moyens techniques sont disponibles et permettent d'atteindre cet objectif.
Neanmoins selon les transformations à apporter les durées nécessaires peuvent être plus ou moins longues
La fin de l'Active Directory n'est pas pour tout de suite néanmoins il est nécessaire de définir une sortie sur plusieurs années
Souveraineté / indépendance -> difficulté de se passer des Services Microsoft sans recréer en cloud une nouvelle complexité
