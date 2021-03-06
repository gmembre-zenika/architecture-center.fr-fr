---
title: Modèles de messagerie
description: La nature distribuée des applications cloud nécessite une infrastructure de messagerie qui connecte les composants et les services, dans l’idéal, d’une manière faiblement couplée afin d’optimiser l’extensibilité. La messagerie asynchrone est largement utilisée et offre de nombreux avantages, mais elle apporte également des défis, tels que le classement des messages, la gestion des messages incohérents, l’idempotence et bien plus encore.
keywords: modèle de conception
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 8bf37903df3a6eb23f1581e0405358a7aee61f79
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30848578"
---
# <a name="messaging-patterns"></a>Modèles de messagerie

[!INCLUDE [header](../../_includes/header.md)]

La nature distribuée des applications cloud nécessite une infrastructure de messagerie qui connecte les composants et les services, dans l’idéal, d’une manière faiblement couplée afin d’optimiser l’extensibilité. La messagerie asynchrone est largement utilisée et offre de nombreux avantages, mais elle apporte également des défis, tels que le classement des messages, la gestion des messages incohérents, l’idempotence et bien plus encore.


|                            Modèle                             |                                                                        Résumé                                                                         |
|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
|        [Consommateurs concurrents](../competing-consumers.md)        |                            Activez plusieurs consommateurs simultanés pour traiter les messages reçus sur le même canal de messagerie.                            |
|          [Canaux et filtres](../pipes-and-filters.md)          |                       Divisez une tâche qui exécute un traitement complexe en une série d’éléments séparés qui peuvent être réutilisés.                        |
|             [File d’attente de priorité](../priority-queue.md)             | Classez par ordre de priorité les requêtes envoyées aux services, de telle sorte que les demandes ayant une priorité plus élevée soient reçues et traitées plus rapidement que celles de moindre priorité. |
|  [Nivellement de la charge basé sur une file d’attente](../queue-based-load-leveling.md)  |              Utilisez une file d’attente qui agit comme mémoire tampon entre une tâche et un service qu’elle appelle, afin d’atténuer les surcharges intermittentes.               |
| [Superviseur de l’agent du planificateur](../scheduler-agent-supervisor.md) |                              Coordonnez un ensemble d’actions sur un ensemble distribué de services et d’autres ressources à distance.                              |

