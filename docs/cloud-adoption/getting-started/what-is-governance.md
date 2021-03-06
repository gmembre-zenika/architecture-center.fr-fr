---
title: 'Adoption du cloud d’entreprise : Qu’est-ce que la gouvernance de ressources cloud ?'
description: Explication du concept de gouvernance des accès aux ressources sur Azure
author: petertaylor9999
ms.date: 09/10/2018
ms.openlocfilehash: 14c26cbccdbea524a7c7220b3bb98ed2a118c30d
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389211"
---
# <a name="enterprise-cloud-adoption-what-is-cloud-resource-governance"></a>Adoption du cloud d’entreprise : Qu’est-ce que la gouvernance de ressources cloud ?

Dans l’article [Comment fonctionne Azure ?](what-is-azure.md), vous avez appris qu’Azure est une collection de serveurs et d’équipements de mise en réseau sur lesquels fonctionnent du matériel virtualisé et des logiciels pour le compte des utilisateurs. Azure garantit l’agilité des développeurs et des services informatiques de votre organisation en les aidant à créer, lire, mettre à jour et supprimer des ressources en fonction de leurs besoins.

Mais si le fait d’accorder aux développeurs un accès illimité aux ressources les fait considérablement gagner en agilité, une telle approche peut également entraîner des coûts inattendus. Par exemple, une équipe de développement peut être autorisée à déployer un ensemble de ressources à des fins de test mais oublier de les supprimer une fois les tests terminés. Ces ressources continueront d’engendrer des coûts, même si leur utilisation n’est plus nécessaire ou autorisée. 

La **gouvernance** de l’accès aux ressources offre un moyen de résoudre ce problème. La gouvernance consiste à gérer, surveiller et contrôler en continu les ressources Azure afin d’atteindre les objectifs et de satisfaire aux exigences de votre organisation. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94] 

Ces objectifs et exigences étant propres à chaque organisation, il est impossible de définir une approche unique de la gouvernance. C’est pourquoi Azure implémente deux principaux outils de gouvernance : le **contrôle d’accès basée sur les ressources (RBAC)** et la **stratégie de ressources**. Il appartient à chaque organisation d’utiliser ces outils pour concevoir son propre modèle de gouvernance.

Le RBAC définit des rôles, qui eux-mêmes définissent les fonctionnalités dont dispose un utilisateur à qui est attribué le rôle. Par exemple, le rôle de **propriétaire** active toutes les fonctionnalités (création, lecture, mise à jour et suppression) pour une ressource, tandis que les rôles de **lecteur** activent uniquement la fonction de lecture. Les rôles peuvent couvrir une vaste étendue qui s’applique à de nombreux types de ressources. Ils peuvent aussi avoir une portée étroite qui s’applique à seulement quelques ressources. 

Les stratégies de ressources définissent des règles pour la création de ressources. Par exemple, une stratégie de ressources peut limiter la référence SKU d’une machine virtuelle à une taille donnée pré-approuvée. Une stratégie de ressources peut également appliquer l’ajout d’une étiquette auprès d’un centre de coûts au moment de la demande de création de la ressource. 

Lors de la configuration de ces outils, il est important de veiller à garantir le meilleur compromis entre gouvernance et agilité organisationnelle. Autrement dit, plus votre stratégie de gouvernance sera restrictive, moins vos développeurs et vos ingénieurs informatiques seront agiles. Une stratégie de gouvernance restrictive peut en effet impliquer davantage d’étapes manuelles, par exemple exiger d’un développeur de remplir un formulaire ou d’envoyer un e-mail à une personne de l’équipe de gouvernance pour créer manuellement une ressource. L’équipe de gouvernance a des capacités limitées et peut être retardée. Résultat : en attendant que leurs ressources soient créées, les équipes de développement ne sont plus productives et les ressources inutiles entraînent une augmentation des coûts tant qu’elles n’ont pas été supprimées.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez le concept de gouvernance des ressources cloud, poursuivez pour en apprendre davantage sur [la gestion de l’accès aux ressources](azure-resource-access.md) dans Azure en vue de découvrir la conception d’un modèle de gouvernance pour [une seule](../governance/governance-single-team.md) ou [plusieurs équipes](../governance/governance-multiple-teams.md).

> [!div class="nextstepaction"]
> [En savoir plus sur l’accès aux ressources dans Azure](azure-resource-access.md)