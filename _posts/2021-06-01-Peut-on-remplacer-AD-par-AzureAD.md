---
title: Peut on remplacer Active Directory par Azure Active Directory ?
layout: post
post-image: "https://www.geek-discovery.com/assets/images/ActiveDirectory.jpg"
description: Mes convictions sur les pistes de transformation pour s'affranchir de l'Active Directory.
tags:
- Active Directory
- Azure Active Directory
- AD
- AAD
- Microsoft
---
L'avènement des services Cloud, en particulier avec le positionnement central d'Office 365 au sein des entreprises, pose la question de l'usage et du positionnement de l'Active Directory au sein des entereprises. Du fait de la multiplicité des fonctions portées par l'Active Directory, une approche globale et stratégie est nécessaire. Une approche technique se confrontera aux limitations d'Azure Active Direcotry qui ne reprend pas l'ensemble des fonctionalités de Active Directory

# Préambule

La nomenclature des annuaires pouvant pouvant préter à confusion, une précision sur le vocabulaire est nécessaire pour clarifier la portée des transformations possibles.

* **Active Directory** : annuaire historique de Microsoft disponible depuis le debut des années 2000. Traditionnellement il est hébergé de manière OnPremise sous la forme de machines physiques ou virtualles.
* **Active Directory Cloud** ou **Acive Directory Azure** : terme utilisé pour modéliser l'hébergement de l'Active Directory au sein de fournisseurs de Cloud Public (tels que Azure ou AWS). Ce terme ne fait pas référence à une technologie mais à une situation d'hébergement.
* **Azure Active Directory** : annuaire Microsoft apparu avec les services Office 365 et les services *IaaS*, *PaaS*, *SaaS* fourni par Microsoft Azure. Cet annuaire est souvent assimilé à tord comme la transformation sous la forme d'un service *PaaS* de l'annuaire Active Directory.
* **Azure Active Directory Domain Services** : annuaire Active Directory fourni sous la forme d'un service *PaaS* par Microsoft. Les comptes utilisateurs sont provisionnés depuis Azure Active Directory.

# Pourquoi cette question devrait se poser ?

Historiquement la gouvernance des services ITs au sein des entreprise se décline de manière verticale induisant une autonomie de chaque services les uns par rapport aux autres avec en transverse l'annuaire d'authentification. 

A

# Comment répondre à cette problématique ?







## **Question 1** : Comment mes utilisateurs peuvent ils s'authentifier sur le 


# Conclusion
