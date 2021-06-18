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
* la difficulté de réduire les systèmes d'authentification et le nombre de login / mot de passe

Au final, la volonté de basculer vers des services Cloud pour répondre aux enjeux de réducations de coûts, de simplification, d'amélioration de l'expérience utilisateur doit passer par une analyse fine de fonctionnalités portées par les fondations techniques dont l'Active Directory fait parti intégrante.

# Comment répondre à cette problématique ?

Les interdépendances entre les différentes strates & services des systèmes d'informations font qu'il est voué 

A titre d'exemple, ci dessous une modélisation d'un système d'information. Les constats sont qu'il y a des interdépendances sur l'ensemble des foundations. Quelles soient techniques, sécurité et applicatives.
![Spaghetti Pasta](/assets/images/2021-06-01-MapEcosystème.png)



## **Question 1** : Quels organisation mettre en place pour apporter une vision globale et transverse ?




La 
Modèle de déléguation
Décloisonnement




## **Question 3** : Quel circuit de provisionning des comptes utilisateurs ?



## **Question 1** : Comment mes utilisateurs peuvent ils s'authentifier sur le 

## **Question X** : Comment protéger mon système d'informations ?

L'évolution des menaces Zero Trust EDR ...

# Conclusion

Par conséquent, Son abandon par les entreprises ne peut être réalisable sans la définition d'un plan stratégique et l'ajustement de la gouvernance du système d'informations et la revue 
Les moyens techniques sont disponibles et permettent d'atteindre cet objectif.
Neanmoins selon les transformations à apporter les durées nécessaires peuvent être plus ou moins longues
La fin de l'Active Directory n'est pas pour tout de suite néanmoins il est nécessaire de définir une sortie sur plusieurs années
Souveraineté / indépendance -> difficulté de se passer des Services Microsoft sans recréer en cloud une nouvelle complexité
