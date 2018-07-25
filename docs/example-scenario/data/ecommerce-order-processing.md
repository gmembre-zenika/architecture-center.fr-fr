---
title: Traitement évolutif des commandes sur Azure
description: Exemple de scénario pour la création d’un pipeline de traitement de commande hautement évolutif à l’aide d’Azure Cosmos DB.
author: alexbuckgit
ms.date: 07/10/2018
ms.openlocfilehash: 541b5e9f523c64bc55526e4e2dffc57a5212e67f
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060980"
---
# <a name="scalable-order-processing-on-azure"></a><span data-ttu-id="efd01-103">Traitement évolutif des commandes sur Azure</span><span class="sxs-lookup"><span data-stu-id="efd01-103">Scalable order processing on Azure</span></span>

<span data-ttu-id="efd01-104">Cet exemple de scénario s’applique aux entreprises ayant besoin d’une architecture hautement évolutive et résiliente pour le traitement de commandes en ligne.</span><span class="sxs-lookup"><span data-stu-id="efd01-104">This example scenario is relevant to organizations that need a highly scalable and resilient architecture for online order processing.</span></span> <span data-ttu-id="efd01-105">Les applications potentielles incluent le e-commerce et les points de vente au détail, le traitement des commandes ainsi que la réservation et le suivi d’un stock.</span><span class="sxs-lookup"><span data-stu-id="efd01-105">Potential applications include e-commerce and retail point-of-sale, order fulfillment, and inventory reservation and tracking.</span></span> 

<span data-ttu-id="efd01-106">Ce scénario prend une approche d’approvisionnement en événements, à l’aide d’un modèle de programmation fonctionnel implémenté par le biais de microservices.</span><span class="sxs-lookup"><span data-stu-id="efd01-106">This scenario takes an event sourcing approach, using a functional programming model implemented via microservices.</span></span> <span data-ttu-id="efd01-107">Chaque microservice est traité comme un processeur de flux de données, et toute logique métier est implémentée par le biais de microservices.</span><span class="sxs-lookup"><span data-stu-id="efd01-107">Each microservice is treated as an stream processor, and all business logic is implemented via microservices.</span></span> <span data-ttu-id="efd01-108">Cette approche permet une disponibilité et une résilience élevées, une géo-réplication et des performances rapides.</span><span class="sxs-lookup"><span data-stu-id="efd01-108">This approach enables high availability and resiliency, geo-replication, and fast performance.</span></span>

<span data-ttu-id="efd01-109">L’utilisation de services gérés par Azure tels que Cosmos DB et HDInsight peut aider à réduire les coûts en tirant parti de l’expertise de Microsoft dans le stockage et la récupération de données mondialement distribuées à l’échelle du cloud.</span><span class="sxs-lookup"><span data-stu-id="efd01-109">Using managed Azure services such as Cosmos DB and HDInsight can help reduce costs by leveraging Microsoft's expertise in globally distributed cloud-scale data storage and retrieval.</span></span> <span data-ttu-id="efd01-110">Ce scénario concerne plus spécialement un scénario de e-commerce ou de vente au détail. Si vous avez d’autres besoins en matière de services de données, vous devez examiner la liste des [services de base de données intelligente entièrement gérée dans Azure][product-category] qui sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="efd01-110">This scenario specifically addresses an e-commerce or retail scenario; if you have other needs for data services, you should review the list of available [fully managed intelligent database services in Azure][product-category].</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="efd01-111">Cas d’usage connexes</span><span class="sxs-lookup"><span data-stu-id="efd01-111">Related use cases</span></span>

<span data-ttu-id="efd01-112">Pensez à ce scénario pour les cas d’usage suivants :</span><span class="sxs-lookup"><span data-stu-id="efd01-112">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="efd01-113">Systèmes principaux de e-commerce ou de point de vente au détail.</span><span class="sxs-lookup"><span data-stu-id="efd01-113">E-commerce or retail point-of-sale back-end systems.</span></span>
* <span data-ttu-id="efd01-114">Systèmes de gestion des stocks.</span><span class="sxs-lookup"><span data-stu-id="efd01-114">Inventory management systems.</span></span>
* <span data-ttu-id="efd01-115">Systèmes de traitement de commande.</span><span class="sxs-lookup"><span data-stu-id="efd01-115">Order fulfillment systems.</span></span>
* <span data-ttu-id="efd01-116">Autres scénarios d’intégration concernant un pipeline de traitement de commande.</span><span class="sxs-lookup"><span data-stu-id="efd01-116">Other integration scenarios relevant to an order processing pipeline.</span></span>

## <a name="architecture"></a><span data-ttu-id="efd01-117">Architecture</span><span class="sxs-lookup"><span data-stu-id="efd01-117">Architecture</span></span>

![Exemple d’architecture pour un pipeline de traitement évolutif des commandes][architecture-diagram]

<span data-ttu-id="efd01-119">Cette architecture décrit en détail les composants clés d’un pipeline de traitement des commandes.</span><span class="sxs-lookup"><span data-stu-id="efd01-119">This architecture details key components of an order processing pipeline.</span></span> <span data-ttu-id="efd01-120">Les données circulent dans le scénario comme suit :</span><span class="sxs-lookup"><span data-stu-id="efd01-120">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="efd01-121">Les messages d’événements entrent dans le système via des applications orientées client (de façon synchrone sur HTTP) et divers systèmes back-end (de façon asynchrone via Apache Kafka).</span><span class="sxs-lookup"><span data-stu-id="efd01-121">Event messages enter the system via customer-facing applications (synchronously over HTTP) and various back-end systems (asynchronously via Apache Kafka).</span></span> <span data-ttu-id="efd01-122">Ces messages sont passés dans un pipeline de traitement des commandes.</span><span class="sxs-lookup"><span data-stu-id="efd01-122">These messages are passed into a command processing pipeline.</span></span>
2. <span data-ttu-id="efd01-123">Chaque message d’événement est ingéré et mappé à un ensemble défini de commandes par un microservice de traitement des commandes.</span><span class="sxs-lookup"><span data-stu-id="efd01-123">Each event message is ingested and mapped to one of a defined set of commands by a command processor microservice.</span></span> <span data-ttu-id="efd01-124">Le traitement des commandes récupère n’importe quel état actuel pertinent pour l’exécution de la commande à partir d’une base de données de capture instantanée de flux d’événements.</span><span class="sxs-lookup"><span data-stu-id="efd01-124">The command processor retrieves any current state relevant to executing the command from an event stream snapshot database.</span></span> <span data-ttu-id="efd01-125">La commande est ensuite exécutée, et la sortie de la commande est émise comme un nouvel événement.</span><span class="sxs-lookup"><span data-stu-id="efd01-125">The command is then executed, and the output of the command is emitted as a new event.</span></span>
3. <span data-ttu-id="efd01-126">Chaque événement émis en tant que sortie d’une commande est affecté à une base de données des flux d’événement à l’aide de Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="efd01-126">Each event emitted as the output of a command is committed to an event stream database using Cosmos DB.</span></span>
4. <span data-ttu-id="efd01-127">Pour chaque insertion de base de données ou mise à jour affectée dans la base de données de flux d’événements, un événement est déclenché par le flux de modification de Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="efd01-127">For each database insert or update committed to the event stream database, an event is raised by the Cosmos DB Change Feed.</span></span> <span data-ttu-id="efd01-128">Les systèmes en aval peuvent s’abonner aux rubriques d’événement qui s’appliquent à ce système.</span><span class="sxs-lookup"><span data-stu-id="efd01-128">Downstream systems can subscribe to any event topics that are relevant to that system.</span></span>
5. <span data-ttu-id="efd01-129">Tous les événements à partir du flux de modification de Cosmos DB sont également envoyés à un microservice de flux d’événements de capture instantanée, qui calcule les modifications d’état provoquées par des événements qui se sont produits.</span><span class="sxs-lookup"><span data-stu-id="efd01-129">All events from the Cosmos DB Change Feed are also sent to a snapshot event stream microservice, which calculates any state changes caused by events that have occurred.</span></span> <span data-ttu-id="efd01-130">Le nouvel état est ensuite validé dans la base de données de capture instantanée de flux événement stockée dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="efd01-130">The new state is then committed to the event stream snapshot database stored in Cosmos DB.</span></span>  <span data-ttu-id="efd01-131">La base de données de capture instantanée fournit une source de données mondialement distribuée et à faible latence pour l’état actuel de tous les éléments de données.</span><span class="sxs-lookup"><span data-stu-id="efd01-131">The snapshot database provides a globally distributed, low latency data source for the current state of all data elements.</span></span> <span data-ttu-id="efd01-132">La base de données de flux d’événements fournit un enregistrement complet de tous les messages d’événement passés à travers l’architecture, ce qui permet des scénarios robustes de récupération d’urgence, de dépannage et de test.</span><span class="sxs-lookup"><span data-stu-id="efd01-132">The event stream database provides a complete record of all event messages that have passed through the architecture, which enables robust testing, troubleshooting, and disaster recovery scenarios.</span></span>  

### <a name="components"></a><span data-ttu-id="efd01-133">Composants</span><span class="sxs-lookup"><span data-stu-id="efd01-133">Components</span></span>

* <span data-ttu-id="efd01-134">[Cosmos DB][docs-cosmos-db] est une base de données multi-modèles et distribuée mondialement de Microsoft qui permet de faire évoluer à votre guise le débit et le stockage de vos solutions sur n’importe quel nombre de régions géographiques.</span><span class="sxs-lookup"><span data-stu-id="efd01-134">[Cosmos DB][docs-cosmos-db] is Microsoft's globally distributed, multi-model database that enables your solutions to elastically and independently scale throughput and storage across any number of geographic regions.</span></span> <span data-ttu-id="efd01-135">Il offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des contrats SLA complets.</span><span class="sxs-lookup"><span data-stu-id="efd01-135">It offers throughput, latency, availability, and consistency guarantees with comprehensive service level agreements (SLAs).</span></span> <span data-ttu-id="efd01-136">Ce scénario utilise Cosmos DB pour le stockage de flux d’événements et le stockage de capture instantanée et tire parti des fonctionnalités du flux de modification de Cosmos DB pour fournir une cohérence des données et une récupération après incident.</span><span class="sxs-lookup"><span data-stu-id="efd01-136">This scenario uses Cosmos DB for event stream storage and snapshot storage, and leverages Cosmos DB's Change Feed features to provide data consistency and fault recovery.</span></span> 
* <span data-ttu-id="efd01-137">[Apache Kafka sur HDInsight][docs-kafka] est une implémentation de service géré d’Apache Kafka, une plateforme de diffusion en continu distribuée en open source pour la création d’applications et de pipelines de diffusion de données en continu et en temps réel.</span><span class="sxs-lookup"><span data-stu-id="efd01-137">[Apache Kafka on HDInsight][docs-kafka] is a managed service implementation of Apache Kafka, an open-source distributed streaming platform for building real-time streaming data pipelines and applications.</span></span> <span data-ttu-id="efd01-138">Kafka fournit également des fonctionnalités de courtier de messages semblables à une file d’attente, pour la publication et l’abonnement aux flux de données nommés.</span><span class="sxs-lookup"><span data-stu-id="efd01-138">Kafka also provides message broker functionality similar to a message queue, for publishing and subscribing to named data streams.</span></span> <span data-ttu-id="efd01-139">Ce scénario utilise Kafka pour traiter les événements entrants et en aval dans le pipeline de traitement des commandes.</span><span class="sxs-lookup"><span data-stu-id="efd01-139">This scenario uses Kafka to process incoming as well as downstream events in the order processing pipeline.</span></span> 

## <a name="considerations"></a><span data-ttu-id="efd01-140">Considérations</span><span class="sxs-lookup"><span data-stu-id="efd01-140">Considerations</span></span>

<span data-ttu-id="efd01-141">De nombreuses options technologiques sont disponibles pour l’ingestion des messages en temps réel, le stockage de données, le traitement de flux, le stockage des données d’analyse et l’analyse et la création de rapports.</span><span class="sxs-lookup"><span data-stu-id="efd01-141">Many technology options are available for real-time message ingestion, data storage, stream processing, storage of analytical data, and analytics and reporting.</span></span> <span data-ttu-id="efd01-142">Pour une vue d’ensemble de ces options, de leurs fonctionnalités et des principaux critères de sélection, consultez l’article [Architectures de Big Data : Traitement en temps réel](/azure/architecture/data-guide/technology-choices/real-time-ingestion) dans le [Guide d’architecture des données Azure](/azure/architecture/data-guide/).</span><span class="sxs-lookup"><span data-stu-id="efd01-142">For an overview of these options, their capabilities, and key selection criteria, see [Big data architectures: Real-time processing](/azure/architecture/data-guide/technology-choices/real-time-ingestion) in the [Azure Data Architecture Guide](/azure/architecture/data-guide/).</span></span>

<span data-ttu-id="efd01-143">Les microservices sont devenus un style architectural populaire pour générer des applications cloud résilientes, hautement évolutives, capables d’évoluer rapidement et pouvant être déployées indépendamment.</span><span class="sxs-lookup"><span data-stu-id="efd01-143">Microservices have become a popular architectural style for building cloud applications that are resilient, highly scalable, independently deployable, and able to evolve quickly.</span></span> <span data-ttu-id="efd01-144">Les microservices requièrent une approche différente pour concevoir et générer des applications.</span><span class="sxs-lookup"><span data-stu-id="efd01-144">Microservices require a different approach to designing and building applications.</span></span> <span data-ttu-id="efd01-145">Des outils tels que Docker, Kubernetes, Azure Service Fabric et Nomad facilitent le développement d’architectures basées sur des microservices.</span><span class="sxs-lookup"><span data-stu-id="efd01-145">Tools such as Docker, Kubernetes, Azure Service Fabric, and Nomad enable the development of microservices-based architectures.</span></span> <span data-ttu-id="efd01-146">Pour obtenir des conseils sur la création et l’exécution d’une architecture basée sur des microservices, consultez [Conception des microservices sur Azure](/azure/architecture/microservices/) dans le Centre des architectures Azure.</span><span class="sxs-lookup"><span data-stu-id="efd01-146">For guidance on building and running a microservices-based architecture, see [Designing microservices on Azure](/azure/architecture/microservices/) in the Azure Architecture Center.</span></span>

### <a name="availability"></a><span data-ttu-id="efd01-147">Disponibilité</span><span class="sxs-lookup"><span data-stu-id="efd01-147">Availability</span></span>

<span data-ttu-id="efd01-148">L’approche d’approvisionnement en événements de ce scénario permet aux composants de système faiblement couplés et déployés indépendamment les uns des autres.</span><span class="sxs-lookup"><span data-stu-id="efd01-148">This scenario's event sourcing approach allows system components to be loosely coupled and deployed independently of one another.</span></span> <span data-ttu-id="efd01-149">Cosmos DB offre une [haute disponibilité][docs-cosmos-db-regional-failover] et permet à l’organisation de gérer les compromis associés à la cohérence, la disponibilité et les performances, avec [les garanties correspondantes][docs-cosmos-db-guarantees].</span><span class="sxs-lookup"><span data-stu-id="efd01-149">Cosmos DB offers [high availability][docs-cosmos-db-regional-failover] and helps organization manage the tradeoffs associated with consistency, availability, and performance, all with [corresponding guarantees][docs-cosmos-db-guarantees].</span></span> <span data-ttu-id="efd01-150">Apache Kafka sur HDInsight est également conçu pour une [haute disponibilité][docs-kafka-high-availability].</span><span class="sxs-lookup"><span data-stu-id="efd01-150">Apache Kafka on HDInsight is also designed for [high availability][docs-kafka-high-availability].</span></span>

<span data-ttu-id="efd01-151">Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre divers services Azure.</span><span class="sxs-lookup"><span data-stu-id="efd01-151">Azure Monitor provides unified user interfaces for monitoring across various Azure services.</span></span> <span data-ttu-id="efd01-152">Pour plus d’informations, consultez l’article [Monitoring in Microsoft Azure](/azure/monitoring-and-diagnostics/monitoring-overview) (Surveillance dans Microsoft Azure).</span><span class="sxs-lookup"><span data-stu-id="efd01-152">For more information, see [Monitoring in Microsoft Azure](/azure/monitoring-and-diagnostics/monitoring-overview).</span></span> <span data-ttu-id="efd01-153">Event Hubs et Stream Analytics sont intégrés à Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="efd01-153">Event Hubs and Stream Analytics are both integrated with Azure Monitor.</span></span> 

<span data-ttu-id="efd01-154">Pour d’autres considérations relatives à la disponibilité, consultez la [check-list de disponibilité][availability].</span><span class="sxs-lookup"><span data-stu-id="efd01-154">For other availability considerations, see the [availability checklist][availability].</span></span>

### <a name="scalability"></a><span data-ttu-id="efd01-155">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="efd01-155">Scalability</span></span>

<span data-ttu-id="efd01-156">Kafka sur HDInsight permet la [configuration du stockage et de l’extensibilité](/azure/hdinsight/kafka/apache-kafka-scalability) pour des clusters Kafka.</span><span class="sxs-lookup"><span data-stu-id="efd01-156">Kafka on HDInsight allows [configuration of storage and scalability](/azure/hdinsight/kafka/apache-kafka-scalability) for Kafka clusters.</span></span> <span data-ttu-id="efd01-157">Cosmos DB offre des performances rapides et prévisibles et [se met à l’échelle de façon transparente](/azure/cosmos-db/partition-data) à mesure que votre application se développe.</span><span class="sxs-lookup"><span data-stu-id="efd01-157">Cosmos DB provides fast, predictable performance and [scales seamlessly](/azure/cosmos-db/partition-data) as your application grows.</span></span>
<span data-ttu-id="efd01-158">L’architecture basée sur les microservices d’approvisionnement en événements de ce scénario simplifie la mise à l'échelle de votre système et l’extension de ses fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="efd01-158">The event sourcing microservices-based architecture of this scenario also makes it easier to scale your system and expand its functionality.</span></span>

<span data-ttu-id="efd01-159">Pour voir d’autres considérations relatives à l’extensibilité, consultez la [liste de contrôle de l’extensibilité][scalability] disponible dans le Centre des architectures Azure.</span><span class="sxs-lookup"><span data-stu-id="efd01-159">For other scalability considerations, see the [scalability checklist][scalability] available in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="efd01-160">Sécurité</span><span class="sxs-lookup"><span data-stu-id="efd01-160">Security</span></span>

<span data-ttu-id="efd01-161">Le [modèle de sécurité de Cosmos DB](/azure/cosmos-db/secure-access-to-data) authentifie les utilisateurs et fournit l’accès à ses données et les ressources.</span><span class="sxs-lookup"><span data-stu-id="efd01-161">The [Cosmos DB security model](/azure/cosmos-db/secure-access-to-data) authenticates users and provides access to its data and resources.</span></span> <span data-ttu-id="efd01-162">Pour plus d’informations, consultez la [sécurité de base de données Cosmos DB](/en-us/azure/cosmos-db/database-security).</span><span class="sxs-lookup"><span data-stu-id="efd01-162">For more information, see [Cosmos DB database security](/en-us/azure/cosmos-db/database-security).</span></span>

<span data-ttu-id="efd01-163">Pour obtenir des conseils d’ordre général sur la conception de solutions sécurisées, consultez la [documentation sur la sécurité Azure][security].</span><span class="sxs-lookup"><span data-stu-id="efd01-163">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="efd01-164">Résilience</span><span class="sxs-lookup"><span data-stu-id="efd01-164">Resiliency</span></span>

<span data-ttu-id="efd01-165">L’architecture d’approvisionnement en événements et les technologies associées dans cet exemple de scénario rendent ce scénario hautement résilient lorsque des échecs se produisent.</span><span class="sxs-lookup"><span data-stu-id="efd01-165">The event sourcing architecture and associated technologies in this example scenario make this scenario highly resilient when failures occur.</span></span> <span data-ttu-id="efd01-166">Pour obtenir des conseils d’ordre général sur la conception de solutions résilientes, consultez l’article [Conception d’applications résilientes pour Azure][resiliency].</span><span class="sxs-lookup"><span data-stu-id="efd01-166">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="efd01-167">Tarifs</span><span class="sxs-lookup"><span data-stu-id="efd01-167">Pricing</span></span>

<span data-ttu-id="efd01-168">Pour examiner le coût d’exécution de ce scénario, tous les services sont préconfigurés dans le calculateur de coûts.</span><span class="sxs-lookup"><span data-stu-id="efd01-168">To examine the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span>  <span data-ttu-id="efd01-169">Pour pouvoir observer l’évolution de la tarification pour votre scénario particulier, modifiez les variables appropriées en fonction du volume de données que vous escomptez.</span><span class="sxs-lookup"><span data-stu-id="efd01-169">To see how pricing would change for your particular scenario, change the appropriate variables to match your expected data volume.</span></span> <span data-ttu-id="efd01-170">Pour ce scénario, la tarification de l’exemple inclut uniquement Cosmos DB et un cluster Kafka pour le traitement des événements déclenchés à partir du flux de modification Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="efd01-170">For this scenario, the example pricing includes only Cosmos DB and a Kafka cluster for processing events raised from the Cosmos DB Change Feed.</span></span> <span data-ttu-id="efd01-171">Les processeurs et les microservices d’événement pour des systèmes origineant et d’autres systèmes en aval ne sont pas inclus, et leur coût dépend beaucoup de la quantité et de la mise à l’échelle de ces services, de même que les technologies choisies pour les mettre en œuvre.</span><span class="sxs-lookup"><span data-stu-id="efd01-171">Event processors and microservices for originating systems and other downstream systems are not included, and their cost is highly dependent on the quantity and scale of these services as well as the technologies chosen for implementing them.</span></span>

<span data-ttu-id="efd01-172">La devise d’Azure Cosmos DB est l’unité de requête (RU).</span><span class="sxs-lookup"><span data-stu-id="efd01-172">The currency of Azure Cosmos DB is the request unit (RU).</span></span> <span data-ttu-id="efd01-173">Avec les unités de requête, vous n’avez pas besoin de réserver de capacités en lecture et en écriture, ni de configurer les ressources de processeur, de mémoire et d’E/S par seconde.</span><span class="sxs-lookup"><span data-stu-id="efd01-173">With request units, you don't need to reserve read/write capacities or provision CPU, memory, and IOPS.</span></span> <span data-ttu-id="efd01-174">Azure Cosmos DB prend en charge des API variées avec différentes opérations allant des lectures et des écritures simples aux requêtes de graphe complexes.</span><span class="sxs-lookup"><span data-stu-id="efd01-174">Azure Cosmos DB supports various APIs that have different operations, ranging from simple reads and writes to complex graph queries.</span></span> <span data-ttu-id="efd01-175">Toutes les requêtes n’étant pas égales, la quantité normalisée d’unités de requête qui leur est affectée est fonction de la quantité de calcul requise pour traiter chaque requête.</span><span class="sxs-lookup"><span data-stu-id="efd01-175">Because not all requests are equal, requests are assigned a normalized quantity of request units based on the amount of computation required to serve the request.</span></span> <span data-ttu-id="efd01-176">Le nombre d’unités de requête requis par votre solution dépend de la taille d’élément de données et du nombre d’opérations de lecture et d’écriture de la base de données, par seconde.</span><span class="sxs-lookup"><span data-stu-id="efd01-176">The number of request units required by your solution is dependent on data element size and the number of database read and write operations per second.</span></span> <span data-ttu-id="efd01-177">Pour en savoir plus, voir [Unités de requête dans Azure Cosmos DB](/azure/cosmos-db/request-units).</span><span class="sxs-lookup"><span data-stu-id="efd01-177">For more information, see [Request units in Azure Cosmos DB](/azure/cosmos-db/request-units).</span></span> <span data-ttu-id="efd01-178">Ces prix estimés sont basés sur Cosmos DB en cours d’exécution dans deux régions Azure.</span><span class="sxs-lookup"><span data-stu-id="efd01-178">These estimated prices are based on Cosmos DB running in two Azure regions.</span></span>

<span data-ttu-id="efd01-179">Nous proposons trois exemples de profils de coût basés sur la quantité d’activité que vous escomptez :</span><span class="sxs-lookup"><span data-stu-id="efd01-179">We have provided three sample cost profiles based on amount of activity you expect:</span></span>

* <span data-ttu-id="efd01-180">[Petite][small-pricing] : cela correspond à 5 unités de requête réservées avec un magasin de données de 1 To dans Cosmos DB et un petit cluster Kafka (D3 v2).</span><span class="sxs-lookup"><span data-stu-id="efd01-180">[Small][small-pricing]: this correlates to 5 RUs reserved with a 1TB data store in Cosmos DB and a small (D3 v2) Kafka cluster.</span></span>
* <span data-ttu-id="efd01-181">[Moyenne][medium-pricing] : cela correspond à 50 unités de requête réservées avec un magasin de données de 10 To dans Cosmos DB et un cluster Kafka (D4 v2) moyen.</span><span class="sxs-lookup"><span data-stu-id="efd01-181">[Medium][medium-pricing]: this correlates to 50 RUs reserved with a 10TB data store in Cosmos DB and a midsized (D4 v2) Kafka cluster.</span></span>
* <span data-ttu-id="efd01-182">[Grande][large-pricing] : cela correspond à 500 unités de requête réservées avec un magasin de données de 30 To dans Cosmos DB et un grand cluster Kafka (D5 v2).</span><span class="sxs-lookup"><span data-stu-id="efd01-182">[Large][large-pricing]: this correlates to 500 RUs reserved with a 30TB data store in Cosmos DB and a large (D5 v2) Kafka cluster.</span></span>

## <a name="related-resources"></a><span data-ttu-id="efd01-183">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="efd01-183">Related Resources</span></span>

<span data-ttu-id="efd01-184">Cet exemple de scénario est basé sur une version plus étendue de cette architecture générée par [Jet.com](https://jet.com) pour son pipeline de traitement de commandes de bout en bout.</span><span class="sxs-lookup"><span data-stu-id="efd01-184">This example scenario is based on a more extensive version of this architecture built by [Jet.com](https://jet.com) for its end-to-end order processing pipeline.</span></span> <span data-ttu-id="efd01-185">Pour plus d’informations, consultez le [profil de client technique jet.com][source-document] et la [présentation de jet.com au Build 2018][source-presentation].</span><span class="sxs-lookup"><span data-stu-id="efd01-185">For more information, see the [jet.com technical customer profile][source-document] and [jet.com's presentation at Build 2018][source-presentation].</span></span> 

<span data-ttu-id="efd01-186">Les autres ressources liées incluent :</span><span class="sxs-lookup"><span data-stu-id="efd01-186">Other related resources include:</span></span>
* <span data-ttu-id="efd01-187">_[Designing Data-Intensive Applications](https://dataintensive.net/)_ par Martin Kleppmann (O'Reilly Media, 2017).</span><span class="sxs-lookup"><span data-stu-id="efd01-187">_[Designing Data-Intensive Applications](https://dataintensive.net/)_ by Martin Kleppmann (O'Reilly Media, 2017).</span></span>
* <span data-ttu-id="efd01-188">_[Domain Modeling Made Functional: Tackle Software Complexity with Domain-Driven Design and F#](https://pragprog.com/book/swdddf/domain-modeling-made-functional)_ par Scott Wlaschin (Pragmatic Programmers LLC, 2018).</span><span class="sxs-lookup"><span data-stu-id="efd01-188">_[Domain Modeling Made Functional: Tackle Software Complexity with Domain-Driven Design and F#](https://pragprog.com/book/swdddf/domain-modeling-made-functional)_ by Scott Wlaschin (Pragmatic Programmers LLC, 2018).</span></span>
* <span data-ttu-id="efd01-189">Autres [cas d’utilisation de Cosmos DB][docs-cosmos-db-use-cases]</span><span class="sxs-lookup"><span data-stu-id="efd01-189">Other [Cosmos DB use cases][docs-cosmos-db-use-cases]</span></span>
* <span data-ttu-id="efd01-190">[Architecture de traitement en temps réel](/azure/architecture/data-guide/big-data/real-time-processing) dans le [Guide d’architecture de données Azure](/azure/architecture/data-guide/)</span><span class="sxs-lookup"><span data-stu-id="efd01-190">[Real time processing architecture](/azure/architecture/data-guide/big-data/real-time-processing) in the [Azure Data Architecture Guide](/azure/architecture/data-guide/)</span></span>

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/databases/
[source-document]: https://customers.microsoft.com/en-us/story/jet-com-powers-innovative-e-commerce-engine-on-azure-in-less-than-12-months
[source-presentation]: https://channel9.msdn.com/events/Build/2018/BRK3602
[small-pricing]: https://azure.com/e/3d43949ffbb945a88cc0a126dc3a0e6e
[medium-pricing]: https://azure.com/e/1f1e7bf2a6ad4f7799581211f4369b9b
[large-pricing]: https://azure.com/e/75207172ece94cf6b5fb354a2252b333
[architecture-diagram]: ./images/architecture-diagram-cosmos-db.png
[docs-cosmos-db]: /azure/cosmos-db
[docs-cosmos-db-change-feed]: /azure/cosmos-db/change-feed
[docs-cosmos-db-regional-failover]: /azure/cosmos-db/regional-failover
[docs-cosmos-db-guarantees]: /azure/cosmos-db/distribute-data-globally#AvailabilityGuarantees
[docs-cosmos-db-use-cases]: /azure/cosmos-db/use-cases
[docs-kafka]: /azure/hdinsight/kafka/apache-kafka-introduction
[docs-kafka-high-availability]: /azure/hdinsight/kafka/apache-kafka-high-availability
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[docs-blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: /azure/architecture/patterns/category/resiliency/
[security]: /azure/security/