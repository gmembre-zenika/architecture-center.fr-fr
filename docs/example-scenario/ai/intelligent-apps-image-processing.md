---
title: Classification d’images pour les déclarations de sinistre sur Azure
description: Scénario éprouvé pour générer le traitement d’images dans vos applications Azure.
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 0ca0b46e83219afc5e22c2ac6467bf4be945c97a
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389160"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a>Classification d’images pour les déclarations de sinistre sur Azure

Cet exemple de scénario s’applique aux entreprises qui doivent traiter des images.

Applications potentielles : classification d’images pour un site web de mode, analyse de texte et d’images pour les déclarations de sinistre ou compréhension des données de télémétrie issues des captures d’écran de jeux. Traditionnellement, les entreprises devaient développer une expertise en matière de modèles Machine Learning, effectuer l’apprentissage des modèles et enfin exécuter les images via leur processus personnalisé pour obtenir les données des images.

En utilisant des services Azure tels que l’API Vision par ordinateur et Azure Functions, les sociétés peuvent éliminer la nécessité de gérer des serveurs individuels, tout en réduisant les coûts et en tirant parti de l’expertise que Microsoft a déjà développée en matière de traitement d’images avec Cognitives Services. Cet exemple de scénario concerne plus particulièrement un cas d’usage de traitement d’images. Si vos besoins en termes d’intelligence artificielle sont variés, pensez à la suite complète de [Cognitive Services][cognitive-docs].

## <a name="related-use-cases"></a>Cas d’usage connexes

Pensez à ce scénario pour les cas d’usage suivants :

* Classer des images sur un site web de mode
* Classer des données de télémétrie provenant de captures d’écran de jeux

## <a name="architecture"></a>Architecture

![Architecture des applications intelligentes : Vision par ordinateur][architecture-computer-vision]

Ce scénario couvre les composants principaux d’une application web ou mobile. Les données circulent dans le scénario comme suit :

1. Azure Functions fait office de couche API. Ces API permettent à l’application de charger des images et de récupérer des données à partir de Cosmos DB.

2. Quand une image est chargée via un appel d’API, elle est stockée dans le stockage Blob.

3. L’ajout de nouveaux fichiers dans le stockage Blob déclenche l’envoi d’une notification EventGrid à une instance Azure Functions.

4. Azure Functions envoie un lien vers le fichier qui vient d’être chargé à l’API Vision par ordinateur à des fins d’analyse.

5. Une fois que les données ont été retournées à partir de l’API Vision par ordinateur, Azure Functions crée une entrée dans Cosmos DB pour conserver les résultats de l’analyse en même temps que les métadonnées d’images.

### <a name="components"></a>Composants

* [L’API Vision par ordinateur][computer-vision-docs] fait partie de la suite Cognitive Services et est utilisée pour récupérer des informations sur chaque image.

* [Azure Functions][functions-docs] fournit l’API principale pour l’application web et assure le traitement des événements correspondant aux images chargées.

* [Event Grid][eventgrid-docs] déclenche un événement quand une nouvelle image est chargée dans le stockage Blob. L’image est ensuite traitée avec Azure Functions.

* [Stockage Blob][storage-docs] stocke tous les fichiers image qui sont chargés dans l’application web, ainsi que les fichiers statiques qui sont consommés par l’application web.

* [Cosmos DB][cosmos-docs] stocke des métadonnées sur chaque image chargée, notamment les résultats du traitement de l’API Vision par ordinateur.

## <a name="alternatives"></a>Autres solutions

* [Service Vision personnalisée][custom-vision-docs]. L’API Vision par ordinateur retourne un ensemble de [catégories basées sur la taxonomie][cv-categories]. Si vous devez traiter des informations qui ne sont pas retournées par l’API Vision par ordinateur, pensez au service Vision personnalisée, qui vous permet de créer des classifieurs d’images personnalisés.

* [Recherche Azure][azure-search-docs]. Si votre cas d’usage implique l’interrogation de métadonnées pour rechercher des images qui répondent à des critères spécifiques, pensez à utiliser Recherche Azure. Actuellement en préversion, la [recherche cognitive][cognitive-search] intègre parfaitement ce flux de travail.

## <a name="considerations"></a>Considérations

### <a name="scalability"></a>Extensibilité

La plupart des composants utilisés dans ce scénario sont des services gérés avec mise à l’échelle automatique. Deux exceptions notables : la solution Azure Functions est limitée à un maximum de 200 instances. Si vous avez besoin de plus d’instances, vous pouvez utiliser plusieurs régions ou plans d’application.

Cosmos DB n’effectue pas de mise à l’échelle automatique en termes d’unités de requête approvisionnées (RU).  Pour obtenir des conseils sur l’estimation de vos besoins, consultez la section relative aux [unités de requête][request-units] dans notre documentation. Pour tirer pleinement parti de la mise à l’échelle dans Cosmos DB, explorez les [clés de partition][partition-key].

Les bases de données NoSQL sacrifient souvent la cohérence (au sens du théorème CAP) au profit de la disponibilité, de l’extensibilité et du partitionnement.  Cet exemple de scénario utilisant un modèle de données clé-valeur, la cohérence des transactions est rarement nécessaire, car la plupart des opérations sont par définition atomiques. Vous trouverez de l’aide pour [Choisir un magasin de données adapté](../../guide/technology-choices/data-store-overview.md) dans le Centre des architectures Azure.

Pour obtenir des conseils d’ordre général sur la conception de solutions évolutives, consultez la [liste de contrôle de l’extensibilité][scalability] dans le Centre des architectures Azure.

### <a name="security"></a>Sécurité

Des instances [Managed Service Identity][msi] (MSI) permettent à d’autres ressources internes d’accéder à votre compte et de les affecter à vos instances Azure Functions. Autorisez uniquement l’accès aux ressources requises dans ces identités pour vous assurer qu’aucun élément supplémentaire n’est exposé à vos fonctions (et potentiellement à vos clients).  

Pour obtenir des conseils d’ordre général sur la conception de solutions sécurisées, consultez la [documentation sur la sécurité Azure][security].

### <a name="resiliency"></a>Résilience

Comme tous les composants de ce scénario sont gérés, à un niveau régional, ils sont résilients automatiquement.

Pour obtenir des conseils d’ordre général sur la conception de solutions résilientes, consultez l’article [Conception d’applications résilientes pour Azure][resiliency].

## <a name="pricing"></a>Tarifs

Pour explorer le coût d’exécution de ce scénario, tous les services sont préconfigurés dans le calculateur de coûts. Pour pouvoir observer l’évolution de la tarification pour votre cas d’usage particulier, modifiez les variables appropriées en fonction du trafic que vous escomptez.

Nous proposons trois exemples de profils de coût selon la quantité de trafic (nous supposons que toutes les images font 100 Ko) :

* [Petit][pricing] : &lt; 5 000 images traitées par mois.
* [Moyen][medium-pricing] : 500 000 images traitées par mois.
* [Grand][large-pricing] : 50 000 000 images traitées par mois.

## <a name="related-resources"></a>Ressources associées

Pour un parcours d’apprentissage interactif de ce scénario, consultez l’article [Build a serverless web app in Azure][serverless] (Créer une application web serverless dans Azure).  

Avant de déployer cet exemple de scénario dans un environnement de production, consultez les [meilleures pratiques][functions-best-practices] Azure Functions.

<!-- links -->
[pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[functions-docs]: /azure/azure-functions/
[computer-vision-docs]: /azure/cognitive-services/computer-vision/home
[storage-docs]: /azure/storage/
[azure-search-docs]: /azure/search/
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[architecture-computer-vision]: ./media/architecture-computer-vision.png
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cosmos-docs]: /azure/cosmos-db/
[eventgrid-docs]: /azure/event-grid/
[cognitive-docs]: /azure/#pivot=products&panel=ai
[custom-vision-docs]: /azure/cognitive-services/Custom-Vision-Service/home
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity
[request-units]: /azure/cosmos-db/request-units
[partition-key]: /azure/cosmos-db/partition-data