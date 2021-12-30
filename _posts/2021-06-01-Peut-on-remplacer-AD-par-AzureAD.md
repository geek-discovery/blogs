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

Dans un environnement de plus en complexe du fait de l'aggrégation de nombreux services avec l'identité comme pierre angulaire, l'automatisation du (de)provisionning doit être la norme afin de réduire les délais et les coûts de support. Il est donc nécessaire de définir les cycles de vie suivant pour ensuite pouvoir automatiser la gestion des objets concernés :
* Quel est le cycle de vie des **utilisateurs internes** de l'entreprise ?
* Quel est le cycle de vie de **utilisateurs sous contrats** (les prestataires, les freelances, ...) ?
* Quel est le cycle de vie des **utilisateurs *Guests*** sur le tenant Office 365 ?
* Quel est le cycle de vie des objets **non utilisateurs** hébergés sur Active Directory (Pur Active Directory, Synchronisés sur Azure AD) et/ou sur Azure AD (Objets uniquement cloud) ? 
* Quel est le cycle de vie des **administrateurs** ?

De manière usuelle, chacune des populations d'utilisateurs disposent de son propre cycle de vie, induisant une complexité au niveau des outils (1 outil par population) voire même une gestion manuelle. Le cas le plus représentatif étant la gestion des utilisateurs *Guests* sur le tenant Office 365.

![Q2a](/assets/images/AD_Q1a.png)

Les objets non utilisateurs (tel que les groupes de sécurité, les listes de distribution ...) sont quand à eux historiquement provisionnés dans l'Active Directory puis synchronisés dans l'Azure AD. L'arrivé d'Office 365 soulève plusieurs problématiques structurantes pour apporter une gestion efficace de ces objets. En effet :
* Certains objets comme les **comptes de ressources** ou les **boites aux lettres partagées** peuvent nécessiter une adaptation post-provisionning depuis l'Active Directory
* Certains objets comme les **listes de distributions**, **les boites aux lettres partagées**, les **comptes de ressources** ne devraient pas être provisionnés depuis l'Active Directory afin de proposer des capacités de gestion aux titulaires de ces objets (eg. en tant que propriétaires d'une liste de distribution, je veux pouvoir modifier les membres depuis mon client Outlook)
* Certains objets comme les comptes ***Guests*** ou les ***Groups O365*** n'ont pas d'existances sur Active Directory

Outre l'aspect annuaires Active Directory & Azure AD, le traitement de cette question implique de concevoir le cycle de vie de bout en bout en prennant en compte les outils amonts aux annuaires d'identité (eg. référentiel RH), les outils de gestion des demandes et d'automatisation des modifications (ITSM), la structure de gouvernance de l'entreprise qui conditionne le modèle de déléguation (eg. combien d'équipes doivent manager les objets, combien de périmètres de responsabilité ...). Dans le cas d'une transition complète vers Azure AD, la transformation de cet écosystème est à prendre en compte.

### Plusieurs scénarios permettent toutefois de traiter cette question

#### **Usage d'un outil inhouse dédié**

Cette solution permet de proposer via un **moteur de traitement** dédié, mis à disposition par les équipes en charge de la gestion des identités, de connecteurs Powershell et/ou Graph vers Active Directory et l'Azure AD afin de soumettre des traitement via des fichiers standardisés. Bien que cet outil puisse être percu comme un nouvel outil dans l'entreprise (et de faits des côuts complémentaires de dévellopement, évolution, maintien en condition opérationnelle) il apporte les bénéfices suivants :
* facilité d'évolution pour la prise en charge de nouvelles fonctionnalités du fait de la maitrise de l'ensemble des briques par les équipes identités
* possibilité de disposer d'un modèle de déléguation granulaire adapté au modèle de gouvernance des entreprises via la déclinaison du modèle de déléguation au niveau de l'outil
* réduit les besoins d'accès aux consoles de management Azure AD / Office 365
* gestion exhaustive de tous types de d'objets
* permet de provisionner les objets au sein de l'annuaire le plus pertinent
* possibilité de traitement en masse
* possibilité d'interfacage via fichier à plat avec un ITSM

![Q2c](/assets/images/AD_Q1c.png)

**Indicateur d'effort de transformation**
* **Difficulté  :** moyenne
* **Charges     :** moyenne

#### **Usage d'un référentiel technique (type IAM)**

Dans le cas où l'entreprise dispose d'une outil de gestion des identités et d'habilitations, l'intéret de le faire évoluer afin d'atteindre les objectifs en terme de gestion exhaustive des objets permet de s'affranchir de la mise en place d'un outil dédié. Neanmoins l'effort d'évolutions n'est généralement pas neutre, du fait qu'il faut mettre en place un connecteur PowerShell / GraphAPI enntre cet outil et l'Azure AD, ce type de connecteur reposant sur des protocoles de communications différents de ceux utilisés avec Active Directory.

En termes de bénéfices, la plupart de ceux apportés par la mise en place d'un outil dédié sont repris et des nouveaux peuvent aussi être apportés comme :
* la capacité de mise en place de workflows
* la mise à disposition d'interfaces accessibles et compréhensibles pour toute la population (IT et non IT)

Neanmoins, du fait de la position transverse d'un tel outil au sein de l'entreprise, la prise en compte de nouveaux besoins est moins évidente du fait des instances de validation généralement plus lourde que pour un outil autonome.

![Q2d](/assets/images/AD_Q1d.png)

**Indicateur d'effort de transformation**
* **Difficulté  :** importante
* **Charges     :** moyenne

#### **Fusion du référentiel RH et du référentiel technique**

Ce scénario repose sur la fusion de l'outil en charge de la gestion du référentiel RH et du référentiel technique. Bien qu'il permette la rationalisation des outils, il requiert une maturité forte sur la gestion du cycle de vie des employées et des objets ainqi que le décloisonement des équipes en charge des outils. Les coûts engendrés ainsi que les impacts organisationels étant non négligeables ce scénario est rarement rencontré.

![Q2e](/assets/images/AD_Q1e.png)

### Et les admins quel doit être leur cycle de provisioning / déprovisioning ?

#### Les clés du royaume

La pierre angulaire de la sécurité du système d'information (et des données hébergées) reposent sur les comptes administrateurs. Bien que ce risque puisse être assimilé à une banalité, il mérite toutefois une attention particulière du fait que la volonté des cyber-attaquants est d'obtenir les permissions fournies à ces comptes par propagation latérale.

Ma conviction est qu'il est nécessaire de s'astreindre à respecter les règles suivantes :
* **Dissociation du circuit de création d'un compte administrateur de la fourniture des habilitations**. Le bénéfice est qu'en cas de compromission de l'outil d'alimentation des annuaires, le modèle d'habilitations ne sera pas corrompu. 
* **Découpage des comptes administrateurs par périmètre**. Ainsi en cas de compromission des comptes administrateurs sur un périmètre (eg. un administrateur identité possède un compte sur Active Directory et 1 compte sur l'Azure AD), la propagation latérale sur un autre périmètre ne pourra s'effectuer. Pour le cas spécifique de l'Azure AD, il n'y a pas de contre-indications à provisionner le compte depuis l'Active Directory à partir du moment où l'ensemble des regles sont respectées.
* **Mise en place d'alertes spécifiques pour la réinitialisation des mots de passe**. Ce type d'alerte permet de détecter les tentatives d'usurpation d'identité. Les outils de création de comptes ayant des capacités de réinitialisation de passe pour couvrir les cas de pertes / oublis de mot de passe, cette fonctionnalité peut être détournée par des personnes malveillantes pour prendre la possession de comptes administrateurs et de-facto des permissions attribuées. La mise en place d'une alerte automatique de reinitialisation de mot de passe (le changement de mot de passe initié par le titulaire du compte administrateur ne doit toutefois pas faire l'objet d'une alerte) est alors nécessaire. A noter que des détections supplémentaires de comportements suspicieux peuvent être faites au travers d'un SIEM.
* **Analyse des habilitations fournies au préalabale de la suppression des comptes**. L'objectif de cette analyse est de mitiger le risque de pertes de privilèges (les privilèges dont dispose le compte à supprimer doivent être fournis, à minima, à un autre administrateur) au niveau global en particulier pour les entreprises ayant un fort *turn-over* sur les équipes admins.

#### Concrètement deux cas sont possibles selon le niveau d'avancement du plan de transformation vers le cloud de l'entreprise :

##### 1) Identités hébergées sur Active Directory et Azure AD :
* Les comptes administrateurs sur le périmètre Active Directory sont provisionnés dans cet annuaire par l'outil de gestion des comptes utilisateurs standards
* Les comptes administrateurs sur le périmètre Azure AD sont provisionnés depuis Active Directory vers Azure AD au travers de Azure AD Connect de manière similaire aux comptes utilisateurs standards
* Habilitations fournies aux comptes au travers d'un mécanisme adhoc
* Mise en place d'un processus de détection de réinitialisation de mots de passe via une tâche planifiée (*Azure Function* sur la périmètre Azure AD) qui détecte des evenements spécifiques sur Active Directory et l'Azure AD
* Mise en place d'un processus d'audit standardisé permettant l'extraction de l'ensemble des permissions fournies à un administrateur que cela soit sur l'Active Directory et l'Azure AD

##### 2) Identités hébergées sur Azure AD uniquement :
* Les comptes administrateurs sur le périmètre Azure AD sont provisionnés sur Azure AD de manière similaire aux comptes utilisateurs standards
* Habilitations fournies aux comptes au travers d'un mécanisme adhoc et à la demande (*Azure Privileged Identity Management*)
* Mise en place d'un processus de détection de réinitialisation de mots de passe via une tâche planifiée (*Azure Function*) qui détecte des evenements spécifiques sur l'Azure AD
* Mise en place d'un processus d'audit standardisé permettant l'extraction de l'ensemble des permissions fournies à un administrateur sur l'Azure AD

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

Le dernier enjeu est de garantir la cohérence entre les habilitations fournies et leur validité. En particulier lors des mobilités internes ou de départ de l'entreprise.
* Azure AD propose la fonctionnalité d'*Access Review* qui permet de simplifier (sans passer par l'analyse fastidieuse des logs) la revue des utilisateurs ayant accédés à des données. Bien qu'actuellement limitée à des Teams, des groups, ou des applications intégrées à Azure AD (Azure AD se positionnant comme le référentiel d'authentification), cette fonctionnalitée permet de limiter la durée nécessaire des phases d'audits.   
* En complément de cette précédente fonction, l'usage d'un script pour auditer les services non couverts (tel que les habilitations sur les boites aux lettres partagées) permet d'obtenir une vue 360 degrée. Dans une optique d'automatisation, ce script peut être intégré sous la forme d'une *Azure Function*.

### Et les habililitations des admins dans tout ca ?

La gestion des habilitations (à fournir et/ou obtenues) est souvent un sujet épineux qui nécessite de correctement apréhender les enjeux en particulier pour les entreprises ayant un fort *turnover* sur les populations et/ou faisant appel à un volume important de prestataires. Pour rappel il y a plusieurs familles de comptes administrateurs :

* **Les comptes en lecture seule** qui permettent d'avoir une vue sur une ou l'ensemble des services. Bien qu'ils ne permettent pas 
* **Les comptes disposant de privilèges sur un périmètre restreint** (d'attributs, d'objets, de services ...)
* **Les comptes à privilèges globaux** (tel que *Global Adminstrators*)

Tout d'abord la déléguation de privilèges doit faire l'objet de la définition d'une matrice RACI afin de definir de manière objective la relation entre l'équipe qui disposent de privilèges, les privilèges fournis et les périmtères à déléguer au sein de l'annuaire permettant ainsi de s'inscrire dans un modèle de Tiering. Le traitement du sujet nécessite par conséquent de faire convgerger le modèle opérationnel de l'entreprise avec les exigences sécurité. Sur cette base, il est ensuite possible de mettre en place le modèle RBAC (déclinaison technique de la matrice RACI sur l'annuaire).

Bien que le sujet soit complexe, au travers de mon expérience, j'ai pu identifier quelques accélarateurs à cette réflexion :
* Une bonne connaissance des roles et responsabilités des equipes **ET** des inforgérants est nécessaire
* Automatiser autant que possible les workflows d'habilitations pour les besoins de tracabilité et de circuits d'approbation stricts
* L'équipe Tiers 0 doit être globale et être assujettie à une chaine de commandement unique. Néanmoins elle peut être consitutée sous la forme de virtual team
* Les ressources Tiers 0 ne doivent pas se limiter aux annuaires d'identité (tel que Active Directory et l'Azure AD) mais à l'ensemble des ressources interagissant avec eux (eg: les fermes de virtualisation, les solutions de patches management ...)
* Respecter les politiques de moindre privilèges

![Q3a](/assets/images/AD_Q3a.png)

A noter qu'un des problèmes couramment rencontrés est la transformation de la matrice RACI définie pour l'Active Directory sur l'Azure AD (cf. l'exemple de matrice RACI au dessus). En effet sur l'Active Directory une extrème granularité est possible permettant typiquement d'autonomiser des filialles. L'Azure AD est quand à lui *by design* concu pour une administration centralisé, et par conséquent peut induire des limites assez structurantes vis à vis du modèle organisationel des entreprises.  Des évolutions vont toutefois dans le sens de la granularisation des privilèges (eg: les *Administrative Units*) mais un écart persiste. Dans ces travaux de transposition, il est donc nécessaire de tenir compte des possibilités concrètes de déléguation proposées par l'Azure AD, une reflexion purement théorique basée sur l'organisation actuelle de l'entreprise (surtout si les responsabilités sont fortement diluées) va au mieux créer des frustrations au sein des équipes opérationnelles ou au pire dégrader fortement le niveau de protection de l'Azure AD.

Un dernier enjeu est de garantir la pérénité du modèle dans la durée. Les principales pistes de reflexion sont :
* Effectuer de manière régulière une revue des permissions accordées au travers d'un processus de recertification pour répondre à la question : Est ce que telle ou telle administrateurs doit toujours disposer de privilèges sur ce périmètre ?
* Effectuer de manière régulière une revue de la matrice RACI afin de s'assurer de son alignement avec le modèle organisation de l'entreprise et de la non porosité entre les périmètres gérés
* Tracer l'ensemble des activités effectuées pour détecter les non conformités
* User & abuser de *Azure Privileges Identity Management* pour les administrateurs ayant des privilèges sur Azure AD et les ressources associées.

**Indicateur d'effort de transformation**
* **Difficulté  :** moyenne
* **Charges     :** moyenne


## **Question 4** : Quel impact pour la gestion des terminaux ?

La transition vers l'Azure AD pose un challenge important pour la gestion des terminaux du fait que les outils traditionnels (type SCCM, Ivanti ...) ne peuvent pas s'interfacer avec l'Azure AD de manière similaire à l'Active Directory mais ouvre la porte à des simplifications et des nouveaux modèles de delivery des terminaux concrétisés par le **Modern Management**. De manière pragmatique, il s'agit d'adresser des chantiers pour l'ensemble des terminaux (PCs, MacOS, iOS, Android) afin d'uniformiser la gestion de leur cycle de vie et de basculer vers une solution de gestion Cloud interopérable avec Azure AD. Bien que cette cible soit atteignable, la séparation historique de la gestion des PCs et des autres terminaux peut soulèver des questions organisationnelles et méthodologique (eg: processus d'évolution continue)

### Chantiers à adresser au niveau de l'organisation :

La transition de le gestion des terminaux vers une solution Cloud, présente des challenges techniques mais aussi organisationel. La partie la plus visible de cette transformation est qu'il est nécessaire de décloisonner les équipes et d'horizontaliser l'organisation. Les deux exemples les plus concrets que j'ai pu recontrer en terme d'organisation inadaptée sont :
* La séparation de la fillière postes de travail avec la filière terminaux mobiles
* La séparation de la gestion des annuaires d'identité entre les équipes *OnPremise* (Scope Active Directory) et *Cloud* (Scope Active Directory)

Au fil de mes missions, je suis arrivé à la conclusion qu'une organisation type pourrait être :
* **Equipe Identité & sécurité de l'identité** : En charge de la gestion de l'Active Directory, de l'Azure Active Directory, des services intermédiaires (tels que Azure AD Connect, solutions d'Identity Providers, GraphAPI).
* **Equipe Digital Workplace** : En charge de l'ensemble des services collaboratifs et de gestion des terminaux (Cycle de vie, configurations, préparation ...) vie l'UEM (tel que Microsoft Endpoint Management ou tout autre UEM).
* **Equipe Applicative** : En charge du portfolio applicatif de l'entreprise (tout type de terminaux) et de sa pérénité dans le temps. Ce dernier point est particulièrement important du fait que les cycles de mises à jour entre les terminaux mobiles et les PCs se sont rapprochés.

Ensuite lors de l'abandon ultérieur de l'Active Directory, le modèle organisationel décrit préalabalement mis en place durant la phase de transition restera pérenne.

### Chantiers à adresser pour l'ensemble des terminaux :

* **Intégration des applications dans le centre logiciel de la solution UEM (Unified Endpoint Management)** afin de disposer d'un catalogue exhaustif d'applications accessible par les utilisateurs et d'atteindre les prérequis du *Zero Master* pour les PCs (capacité de fabriquer de manière automatisée des machines à partir d'une image brute de Windows). Ces applications peuvent être installées de manière obligatoire ou être laissées à la discrétion des utilisateurs, groupées par profil métiers ...
* **Gestion des mises à jour (security updates, features updates, quality updates)**
* **Gestion de la conformité** pour garantir que le PCs est conforme aux exigences de l'entreprise tel que le respect des mesures de sécurité, le niveau de version du système d'exploitation ... A noter qu'un indicateur de conformité est ainsi calculé pour chaque terminal et peut être ensuite consommé par le *Azure AD Conditional Access* (colonne vertébrale du modèle Zero Trust proposé par Microsoft) pour autoriser/bloquer les accès aux services

### Chantiers complémentaires à adresser pour les PCs :

* **Transition de l'usine de préparation des PCs vers Windows Autopilot** permettant ainsi de préparer les PCs (inscription sécurisée dans l'Azure Active Directory, enrollement dans l'outil de gestion, descente des profils de configuration, descente des applications obligatoires) directement depuis des services cloud.   
* **Gestion de la configuration des PCs** au travers de profils de configurations (en lieu et place des GPOs) poussés par la solution UEM : Microsoft Endpoint Management (ex-Intune) ou toute autre solution UEM supportée.

Le couplage de l'Azure AD avec une solution de MDM permet ainsi de couper les adhérences entre les terminaux et l'Active Directory, du fait que la présence de leur identité n'est plus nécessaire.
A noter que la gestion Cloud des terminaux permet de s'affranchir des adhérences avec le réseau d'Entreprise. Les terminaux sont ainsi manageables où qu'ils se trouvent et sans avoir à utiliser de VPN.

**Indicateur d'effort de transformation**
* **Difficulté  :** moyenne
* **Charges     :** moyenne


## **Question 5** : Quel impact sur les services techniques ?

La bascule vers l'Azure AD implique de définir des stratégies de transformation pour certains services techniques bien souvent adhérents à l'Active Directory. La liste ci-dessous (non exhaustive) reprend les principaux :

### Le DNS : Service de résolution des noms

Dans le SI d'une entreprise, le DNS a deux principales vocations : localiser les ressources internes à l'entreprise et permettre de router les requêtes DNS pour la localisation de ressources sur Internet. L'active Directory reposant sur le service DNS pour localiser dynamiquement ses propres ressources (*Design for failure*), la topologie DNS repose de manière plus au moins profonde sur le service DNS hébergés par l'Active Directory. Il est donc nécessaire de définir une stratégie pour répondre aux deux besoins précédements listés.

L'approche la plus réaliste que je recommande couramment est de mettre en place (ou de capitaliser) sur une infrastructure autonome DDI (DNS DHCP IPAM) permettant de réaliser une transition douce. Lorsque l'entreprise ne dispose plus de ressources localisables via des zones DNS privées (eg. les ressources sont alors accédées uniquement via des zones DNS publiques) il est alors possible de décomissioner cette infrastructure. L'usage d'une machine Windows autonome pour rendre le service DNS peut être envisagée, toutefois elle doit être envisagée en tant que solution alternative, le DDI (type Infoblox ou Efficient IP) restant préférable.

### Le DHCP : Service d'allocation dynamique des adresses IPs

Il s'agit du premier service utilisé principalement par l'ensemble des postes de travail. Les adhérences avec l'Active Directory sont relativement faibles et peuvent être principalement liés à la fourniture du service depuis une machine membre du domaine de l'Active Directory et approuvé par l'Active Directory (pour éviter les *Rogues DHCP*). A noter que cette implémentation du service DHCP n'est pas la norme que je rencontre au sein de mes clients.

Toutefois, lorsque le cas existe, la stratégie que je recommande est de basculer sur une infrastructure autonome DDI (DNS DHCP IPAM). L'usage d'une machine Windows autonome pour rendre le service DHCP peut être envisagée, toutefois elle doit être envisagée en tant que solution alternative, le DDI (type Infoblox ou Efficient IP) restant préférable.

### Le VPN : Accès sécurisé au SI

Le mode de fonctionnement du VPN historiquement mis en place fonctionne dans un contexte utilisateur (c'est à dire qu'il est démarré à posteriori de l'authentification de l'utilisateur sur le poste de travail) et en *forced tunneling* (c'est à dire qu'au préalable du démarrage du VPN, tous les flux sont bloqués) doit être ajusté afin d'être en confirmatité avec les paradigmes du **Modern Management**. En effet l'accès à Internet pour accéder aux ressources cloud (en particulier de gestion des postes de travail) devient le vecteur de communication principal à contrario du réseau d'entreprise qui devient nécessaire uniquement pour accéder aux ressources et applications conservées en Interne.

A noter que la crise du COVID19 avec la généralisation du télétravail a démontré les limites de l'approche traditionnelle du VPN.

Bien que ce sujet soit assez éloigné de l'Active Directory, les transformations structurantes induites par le passage vers l'Azure AD et les services cloud (tel que la gestion MDM des postes de travail) implique de revoir la stratégie d'usage du VPN pour le limiter au strict nécessaire. En effet, au travers de solutions de gestion cloud (MDM) et de protections cloud (EDR) des postes, l'accès au réseau d'entrerise n'est plus nécessaire pour garantir sa sécurité.

### Les GPOs : Normalisation des configurations

Je constate couramment que le poids des GPOs peut constituer un énorme frein vis à vis de la transition vers l'Azure AD. En effet, il n'y a pas d'équivalent direct sur l'Azure AD. Il est nécessaire de reposer sur des politiques de configurations déployées par la solution MDM, de manière similaire à ce qui peut être réalisé sur des smartphones.
Dans la stratégie de traitement des GPOs, deux périmètres sont à évaluer :

* **Les postes de travail** : bien souvent un volume conséquent de GPOs existent sur ce périmètre. L'approche analytique est généralement préférée car considéré comme étant sure. La complexité de l'historique fait qu'une refonte de zéro est plus efficace (et permet d'éliminer des configurations obsolètes). De même que la standardisation et l'allègement des configurations devennant la norme (au grand dam de certaines équipes qui souhaitent pousser le cursus du configuration à son paroxysme), cette approche analytique deviendrait trop couteuse en charges de travail. Je recommande généralement une approche itérative lissée dans le temps pour transformer ces besoins de configurations vers les politiques MDM.

* **Les serveurs** : l'usage des GPOs sur ce périmètre (en particulier pour la standardisation des politiques de sécurité) est intimement lié à la transformation des services et applications d'enterprise vers des services Cloud (PaaS / SaaS). Elles ne pourront être décommissionnées qu'à posteriori de l'arrêt des serveurs.

**Indicateur d'effort de transformation**
* **Difficulté  :** faible (sauf si l'approche analytique pour les GPOs est retenue)
* **Charges     :** faible (sauf si l'approche analytique pour les GPOs est retenue)


## **Question 6** : Quel impact sur les applicatifs  ?

Il s'agit du sujet le plus structurant - et souvent minimisé - pour le désengeagement de l'Active Directory. La règle que j'explique généralement est que **tant qu'une application reste intégrée à l'Active Directory ou hébergée sur une machine membre du domaine, l'Active Directory doit perdurer**. Bien que cette règle puisse créer une certaine appréhension, des scénarios plus ou moins complexes sont envisgeables selon les méthodes d'authentification :

### Pour les applications utilisant le protocole Kerberos, NTLM, LDAP(s)

Ce type de protocole n'étant pas supporté nativement par Azure AD, une transformation des applications est nécessaire. Plusieurs chemins sont enviseagbles :
* Soit via un lift & shift de l'application vers du IaaS Azure et une intégration sur Azure Active Directory Domain services
* Soit via une transformation de la méthode d'authentication vers des protocoles supportés nativement sur l'Azure AD
* Soit via une migration de l'applicatif vers une solution SaaS utilisant un protocole supporté nativement sur l'Azure AD
* Soit via une migration de l'applicatif vers un nouvel applicatif utilisant un protocole supporté nativement sur l'Azure AD
* Soit via un redevellopement de l'applicatif répondant aux standards actuels et en privilégiant les services PaaS
* Soit via une déconnexion de la source d'authentification (l'Active Directory) au profil d'une authentification locale

Je recommande d'envisager la conservation de l'Active Directory pour couvrir les authentifications applicatives qu'en ultime recours.

### Pour les applications utilisant les protocoles SAML, OAuth ...

Il s'agit du périmètre le plus *simple* a traiter du fait que ces protocoles sont supportées nativement sur l'Azure AD. De ce fait le chemin de transformation peut être relativement court. 
A noter qu'une identification de la supportatibilité de ces protocoles pour l'ensemble du portofolio applicatif peut être un accélarateur. Il est alors enviseagble de basculer une application reposant sur du LDAP(s), Kerberos ... sur une méthode d'authentification supportée par Azure AD.

Une question que me pose souvent mes clients est l'intéret de la solution **Azure AD application Proxy**. Souvent une incompréhension existe vis à vis de cette solution. Cette solution permet de faciliter le SSO depuis un poste pure cloud (ie. Azure AD Join Only), mais ne permet pas pour une application supportée par Azure AD Application Proxy de s'affranchir de l'Active Directory.
Pour plus de détail : [Référence Azure AD Application Proxy](https://docs.microsoft.com/en-us/azure/active-directory/app-proxy/application-proxy)

**Indicateur d'effort de transformation**
* **Difficulté  :** moyenne à forte selon le portfoilio applicatif
* **Charges     :** moyenne à forte selon le portfoilio applicatif


## **Question 7** : Est ce une opportunité pour simplifier la vie des utilisateurs ?

Du point de vue des utilisateurs, les gains peuvent être de plusieurs niveaux :

* **Authentification** : L'Azure AD devenant la source d'authentification globale, l'unicité du Royaume SSO peut être atteinte, l'authentification entre les applications et services est alors réellement transparente.
* **Accès aux services et applications** : L'Azure AD étant un fort drivers pour massifier l'usage des services Cloud, la sanctuarisation du **réseau d'entreprise** se réduit très fortment (et de fait l'usage du VPN). Les utilisateurs peuvent ainsi, où qu'ils soient, simplement accéder aux services et applications.
* **Autonomie** : Le couplage des fonctionnalités d'enrollement automatique des postes de travail (autopilot) avec la solution de MDM, permet aux utilisateurs de (re)préparer leur postes de travail où qu'ils se trouvent sans nécessiter le passage dans les locaux de l'entreprise
* **Autonomie** : Les fonctions nativement de reinitialisation de mot de passe (Self Service Reset Password) permet aux utilisateurs de régénérer ce mot de passe de manière sécurisée en autonomie sans avoir à recourir au helpdesk


## **Question X** : Et au final, si l'entreprise bascule sur l'Azure AD, est ce que je peux garantir la sécurité de mon SI ?

Outre la gestion des identités et authentifications proposées par l'Azure AD, l'annuaire s'inscrit dans une démarche de Zero Trust permettant de garantir un haut niveau de sécurité de manière nominale sans avoir à recourir à des solutions complémentaires comme sur l'Active Directory. L'Azure AD Conditional Access permet d'ajuster les règles selon des conditions qui ne cessent d'augmenter. Neanmoins l'appropriation de l'Azure AD Conditional Access requiert une remise en cause des dogmes de sécurité (le bien nommé **Chateau Fort**). En effet, il ne faut plus définir des politiques d'accès sur une approche blocage, mais plutôt sur une approche de situation : quelles situation je veux / doit traiter.


# Conclusion

Par conséquent, Son abandon par les entreprises ne peut être réalisable sans la définition d'un plan stratégique global, l'ajustement de la gouvernance du système d'informations et la revue du modèle opérationnel. La traitement de toutes les questions précédentes permet d'envisager le passage vers Azure AD et de s'affranchir de l'AD. Neanmoins le non traitement d'une des questions impliquent la conservation de l'AD

Bien que les moyens techniques sont disponibles et permettent d'atteindre cet objectif, les transformations à apporter ainsi que les durées nécessaires peuvent être plus ou moins longues. Il s'agit de fait d'un plan sur plusieurs années (en particulier pour le traitement des applications).
