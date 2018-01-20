---
title: "Style d’architecture basée sur les événements"
description: "Décrit les avantages, les inconvénients et les bonnes pratiques pour les architectures basées sur les événements et les architectures IoT sur Azure"
author: MikeWasson
ms.openlocfilehash: ff7f936ceabefe7079a1ebbfa717ff4095bf133b
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="event-driven-architecture-style"></a><span data-ttu-id="5fc46-103">Style d’architecture basée sur les événements</span><span class="sxs-lookup"><span data-stu-id="5fc46-103">Event-driven architecture style</span></span>

<span data-ttu-id="5fc46-104">Une architecture basée sur les événements est constituée de **producteurs d’événements** qui génèrent un flux d’événements et de **consommateurs d’événements** qui écoutent les événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-104">An event-driven architecture consists of **event producers** that generate a stream of events, and **event consumers** that listen for the events.</span></span> 

![](./images/event-driven.svg)

<span data-ttu-id="5fc46-105">Les événements étant remis en quasi-temps réel, les consommateurs peuvent y répondre immédiatement à mesure qu’ils se produisent.</span><span class="sxs-lookup"><span data-stu-id="5fc46-105">Events are delivered in near real time, so consumers can respond immediately to events as they occur.</span></span> <span data-ttu-id="5fc46-106">Les producteurs sont dissociés des consommateurs (un producteur ne peut pas identifier les consommateurs qui sont à l’écoute).</span><span class="sxs-lookup"><span data-stu-id="5fc46-106">Producers are decoupled from consumers &mdash; a producer doesn't know which consumers are listening.</span></span> <span data-ttu-id="5fc46-107">Les consommateurs sont aussi dissociés les uns des autres, et chaque consommateur voit tous les événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-107">Consumers are also decoupled from each other, and every consumer sees all of the events.</span></span> <span data-ttu-id="5fc46-108">Il s’agit d’une différence notable par rapport au modèle des [consommateurs concurrents][competing-consumers], où les consommateurs extraient les messages d’une file d’attente et où un message est traité une seule fois (à condition qu’il n’y ait pas d’erreurs).</span><span class="sxs-lookup"><span data-stu-id="5fc46-108">This differs from a [Competing Consumers][competing-consumers] pattern, where consumers pull messages from a queue and a message is processed just once (assuming no errors).</span></span> <span data-ttu-id="5fc46-109">Dans certains systèmes, comme IoT, les événements doivent être ingérés dans de très gros volumes.</span><span class="sxs-lookup"><span data-stu-id="5fc46-109">In some systems, such as IoT, events must be ingested at very high volumes.</span></span>

<span data-ttu-id="5fc46-110">Une architecture basée sur les événements peut utiliser un modèle de publication/abonnement ou un modèle de flux d’événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-110">An event driven architecture can use a pub/sub model or an event stream model.</span></span> 

- <span data-ttu-id="5fc46-111">**Publication/abonnement** : l’infrastructure de messagerie assure le suivi des abonnements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-111">**Pub/sub**: The messaging infrastructure keeps track of subscriptions.</span></span> <span data-ttu-id="5fc46-112">Quand un événement est publié, elle communique l’événement à chaque abonné.</span><span class="sxs-lookup"><span data-stu-id="5fc46-112">When an event is published, it sends the event to each subscriber.</span></span> <span data-ttu-id="5fc46-113">Une fois l’événement reçu, il ne peut pas être relu et les nouveaux abonnés ne le voient pas.</span><span class="sxs-lookup"><span data-stu-id="5fc46-113">After an event is received, it cannot be replayed, and new subscribers do not see the event.</span></span> 

- <span data-ttu-id="5fc46-114">**Diffusion d’événements** : les événements sont écrits dans un journal.</span><span class="sxs-lookup"><span data-stu-id="5fc46-114">**Event streaming**: Events are written to a log.</span></span> <span data-ttu-id="5fc46-115">Les événements sont classés dans un ordre strict (au sein d’une partition) et sont durables.</span><span class="sxs-lookup"><span data-stu-id="5fc46-115">Events are strictly ordered (within a partition) and durable.</span></span> <span data-ttu-id="5fc46-116">Les clients ne s’abonnent pas au flux, mais ils peuvent lire n’importe quelle partie du flux.</span><span class="sxs-lookup"><span data-stu-id="5fc46-116">Clients don't subscribe to the stream, instead a client can read from any part of the stream.</span></span> <span data-ttu-id="5fc46-117">Il revient au client d’avancer sa position dans le flux.</span><span class="sxs-lookup"><span data-stu-id="5fc46-117">The client is responsible for advancing its position in the stream.</span></span> <span data-ttu-id="5fc46-118">Cela signifie qu’un client peut se joindre à tout moment et relire les événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-118">That means a client can join at any time, and can replay events.</span></span>

<span data-ttu-id="5fc46-119">Du côté du consommateur, il existe quelques variantes courantes :</span><span class="sxs-lookup"><span data-stu-id="5fc46-119">On the consumer side, there are some common variations:</span></span>

- <span data-ttu-id="5fc46-120">**Traitement des événements simples** :</span><span class="sxs-lookup"><span data-stu-id="5fc46-120">**Simple event processing**.</span></span> <span data-ttu-id="5fc46-121">un événement déclenche immédiatement une action dans le consommateur.</span><span class="sxs-lookup"><span data-stu-id="5fc46-121">An event immediately triggers an action in the consumer.</span></span> <span data-ttu-id="5fc46-122">Par exemple, vous pouvez utiliser Azure Functions avec un déclencheur Service Bus, de telle sorte qu’une fonction s’exécute chaque fois qu’un message est publié dans une rubrique Service Bus.</span><span class="sxs-lookup"><span data-stu-id="5fc46-122">For example, you could use Azure Functions with a Service Bus trigger, so that a function executes whenever a message is published to a Service Bus topic.</span></span>

- <span data-ttu-id="5fc46-123">**Traitement des événements complexes** :</span><span class="sxs-lookup"><span data-stu-id="5fc46-123">**Complex event processing**.</span></span> <span data-ttu-id="5fc46-124">un consommateur traite une série d’événements, à la recherche de modèles dans les données d’événement, en s’appuyant sur une technologie telle qu’Azure Stream Analytics ou Apache Storm.</span><span class="sxs-lookup"><span data-stu-id="5fc46-124">A consumer processes a series of events, looking for patterns in the event data, using a technology such as Azure Stream Analytics or Apache Storm.</span></span> <span data-ttu-id="5fc46-125">Par exemple, vous pouvez agréger les relevés d’un appareil intégré dans une fenêtre de temps et générer une notification si la moyenne mobile dépasse un certain seuil.</span><span class="sxs-lookup"><span data-stu-id="5fc46-125">For example, you could aggregate readings from an embedded device over a time window, and generate a notification if the moving average crosses a certain threshold.</span></span> 

- <span data-ttu-id="5fc46-126">**Traitement des flux d’événements** :</span><span class="sxs-lookup"><span data-stu-id="5fc46-126">**Event stream processing**.</span></span> <span data-ttu-id="5fc46-127">utilisez une plateforme de diffusion de données, telle qu’Azure IoT Hub ou Apache Kafka, comme pipeline pour ingérer les événements et les transmettre aux processeurs de flux.</span><span class="sxs-lookup"><span data-stu-id="5fc46-127">Use a data streaming platform, such as Azure IoT Hub or Apache Kafka, as a pipeline to ingest events and feed them to stream processors.</span></span> <span data-ttu-id="5fc46-128">Les processeurs de flux agissent de façon à traiter ou transformer le flux.</span><span class="sxs-lookup"><span data-stu-id="5fc46-128">The stream processors act to process or transform the stream.</span></span> <span data-ttu-id="5fc46-129">Il peut exister plusieurs processeurs de flux pour différents sous-systèmes de l’application.</span><span class="sxs-lookup"><span data-stu-id="5fc46-129">There may be multiple stream processors for different subsystems of the application.</span></span> <span data-ttu-id="5fc46-130">Cette approche est parfaitement adaptée aux charges de travail IoT.</span><span class="sxs-lookup"><span data-stu-id="5fc46-130">This approach is a good fit for IoT workloads.</span></span>

<span data-ttu-id="5fc46-131">La source des événements peut être extérieure au système. Il peut s’agir par exemple des appareils physiques d’une solution IoT.</span><span class="sxs-lookup"><span data-stu-id="5fc46-131">The source of the events may be external to the system, such as physical devices in an IoT solution.</span></span> <span data-ttu-id="5fc46-132">Dans ce cas, le système doit pouvoir ingérer les données selon le volume et le débit imposés par la source de données.</span><span class="sxs-lookup"><span data-stu-id="5fc46-132">In that case, the system must be able to ingest the data at the volume and throughput that is required by the data source.</span></span>

<span data-ttu-id="5fc46-133">Dans le diagramme logique ci-dessus, chaque type de consommateur est représenté par un cadre unique.</span><span class="sxs-lookup"><span data-stu-id="5fc46-133">In the logical diagram above, each type of consumer is shown as a single box.</span></span> <span data-ttu-id="5fc46-134">Dans la pratique, il est courant d’avoir plusieurs instances d’un même consommateur pour éviter que celui-ci devienne un point de défaillance unique dans le système.</span><span class="sxs-lookup"><span data-stu-id="5fc46-134">In practice, it's common to have multiple instances of a consumer, to avoid having the consumer become a single point of failure in system.</span></span> <span data-ttu-id="5fc46-135">Plusieurs instances peuvent aussi s’avérer nécessaires pour gérer le volume et la fréquence des événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-135">Multiple instances might also be necessary to handle the volume and frequency of events.</span></span> <span data-ttu-id="5fc46-136">De même, un même consommateur peut traiter les événements de plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="5fc46-136">Also, a single consumer might process events on multiple threads.</span></span> <span data-ttu-id="5fc46-137">Cela peut être une source de problèmes si les événements doivent être traités dans l’ordre ou s’ils nécessitent une sémantique « exactly-once » (exactement une fois).</span><span class="sxs-lookup"><span data-stu-id="5fc46-137">This can create challenges if events must be processed in order, or require exactly-once semantics.</span></span> <span data-ttu-id="5fc46-138">Consultez [Minimiser la coordination][minimize-coordination].</span><span class="sxs-lookup"><span data-stu-id="5fc46-138">See [Minimize Coordination][minimize-coordination].</span></span> 

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="5fc46-139">Quand utiliser cette architecture</span><span class="sxs-lookup"><span data-stu-id="5fc46-139">When to use this architecture</span></span>

- <span data-ttu-id="5fc46-140">Plusieurs sous-systèmes doivent traiter les mêmes événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-140">Multiple subsystems must process the same events.</span></span> 
- <span data-ttu-id="5fc46-141">Traitement en temps réel avec un décalage dans le temps minimal.</span><span class="sxs-lookup"><span data-stu-id="5fc46-141">Real-time processing with minimum time lag.</span></span>
- <span data-ttu-id="5fc46-142">Traitement des événements complexes, tel que les critères spéciaux ou l’agrégation dans des fenêtres de temps.</span><span class="sxs-lookup"><span data-stu-id="5fc46-142">Complex event processing, such as pattern matching or aggregation over time windows.</span></span>
- <span data-ttu-id="5fc46-143">Volume et vélocité élevés des données, par exemple IoT.</span><span class="sxs-lookup"><span data-stu-id="5fc46-143">High volume and high velocity of data, such as IoT.</span></span>

## <a name="benefits"></a><span data-ttu-id="5fc46-144">Avantages</span><span class="sxs-lookup"><span data-stu-id="5fc46-144">Benefits</span></span>

- <span data-ttu-id="5fc46-145">Les producteurs et les consommateurs sont dissociés.</span><span class="sxs-lookup"><span data-stu-id="5fc46-145">Producers and consumers are decoupled.</span></span>
- <span data-ttu-id="5fc46-146">Absence d’intégrations point à point.</span><span class="sxs-lookup"><span data-stu-id="5fc46-146">No point-to point-integrations.</span></span> <span data-ttu-id="5fc46-147">Simplicité d’ajout de nouveaux consommateurs au système.</span><span class="sxs-lookup"><span data-stu-id="5fc46-147">It's easy to add new consumers to the system.</span></span>
- <span data-ttu-id="5fc46-148">Les consommateurs peuvent répondre immédiatement aux événements, dès leur arrivée.</span><span class="sxs-lookup"><span data-stu-id="5fc46-148">Consumers can respond to events immediately as they arrive.</span></span> 
- <span data-ttu-id="5fc46-149">Architecture hautement scalable et distribuée.</span><span class="sxs-lookup"><span data-stu-id="5fc46-149">Highly scalable and distributed.</span></span> 
- <span data-ttu-id="5fc46-150">Les sous-systèmes ont des vues indépendantes du flux d’événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-150">Subsystems have independent views of the event stream.</span></span>

## <a name="challenges"></a><span data-ttu-id="5fc46-151">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="5fc46-151">Challenges</span></span>

- <span data-ttu-id="5fc46-152">Transmission non garantie.</span><span class="sxs-lookup"><span data-stu-id="5fc46-152">Guaranteed delivery.</span></span> <span data-ttu-id="5fc46-153">Dans certains systèmes, surtout dans les scénarios IoT, il est essentiel de garantir la transmission des événements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-153">In some systems, especially in IoT scenarios, it's crucial to guarantee that events are delivered.</span></span>
- <span data-ttu-id="5fc46-154">Traitement des événements dans l’ordre ou une seule fois.</span><span class="sxs-lookup"><span data-stu-id="5fc46-154">Processing events in order or exactly once.</span></span> <span data-ttu-id="5fc46-155">Chaque type de consommateur s’exécute généralement dans plusieurs instances à des fins de résilience et de scalabilité.</span><span class="sxs-lookup"><span data-stu-id="5fc46-155">Each consumer type typically runs in multiple instances, for resiliency and scalability.</span></span> <span data-ttu-id="5fc46-156">Cela peut être une source de problèmes si les événements doivent être traités dans l’ordre (dans un type de consommateur) ou si la logique de traitement n’est pas idempotent.</span><span class="sxs-lookup"><span data-stu-id="5fc46-156">This can create a challenge if the events must be processed in order (within a consumer type), or if the processing logic is not idempotent.</span></span>

## <a name="iot-architecture"></a><span data-ttu-id="5fc46-157">Architecture IoT</span><span class="sxs-lookup"><span data-stu-id="5fc46-157">IoT architecture</span></span>

<span data-ttu-id="5fc46-158">Les architectures basées sur les événements sont des éléments essentiels dans les solutions IoT.</span><span class="sxs-lookup"><span data-stu-id="5fc46-158">Event-driven architectures are central to IoT solutions.</span></span> <span data-ttu-id="5fc46-159">Le diagramme suivant présente une architecture logique possible pour IoT.</span><span class="sxs-lookup"><span data-stu-id="5fc46-159">The following diagram shows a possible logical architecture for IoT.</span></span> <span data-ttu-id="5fc46-160">Le diagramme met en avant les composants de diffusion d’événements de l’architecture.</span><span class="sxs-lookup"><span data-stu-id="5fc46-160">The diagram emphasizes the event-streaming components of the architecture.</span></span>

![](./images/iot.png)

<span data-ttu-id="5fc46-161">La **passerelle cloud** ingère les événements d’appareils à la limite du cloud en utilisant un système de messagerie fiable et à faible latence.</span><span class="sxs-lookup"><span data-stu-id="5fc46-161">The **cloud gateway** ingests device events at the cloud boundary, using a reliable, low latency messaging system.</span></span>

<span data-ttu-id="5fc46-162">Les appareils peuvent envoyer les événements directement à la passerelle cloud ou via une **passerelle de champ**.</span><span class="sxs-lookup"><span data-stu-id="5fc46-162">Devices might send events directly to the cloud gateway, or through a **field gateway**.</span></span> <span data-ttu-id="5fc46-163">Une passerelle de champ est un appareil ou un logiciel spécialisé, généralement colocalisée avec les appareils, qui reçoit les événements et les transfère à la passerelle cloud.</span><span class="sxs-lookup"><span data-stu-id="5fc46-163">A field gateway is a specialized device or software, usually colocated with the devices, that receives events and forwards them to the cloud gateway.</span></span> <span data-ttu-id="5fc46-164">La passerelle de champ peut aussi prétraiter les événements d’appareils bruts, remplissant des fonctions de filtrage, d’agrégation ou de transformation de protocole.</span><span class="sxs-lookup"><span data-stu-id="5fc46-164">The field gateway might also preprocess the raw device events, performing functions such as filtering, aggregation, or protocol transformation.</span></span>

<span data-ttu-id="5fc46-165">Après ingestion, les événements transitent par un ou plusieurs **processeurs de flux** qui peuvent acheminer les données (par exemple, vers le stockage) ou procéder à l’analytique et autres traitements.</span><span class="sxs-lookup"><span data-stu-id="5fc46-165">After ingestion, events go through one or more **stream processors** that can route the data (for example, to storage) or perform analytics and other processing.</span></span>

<span data-ttu-id="5fc46-166">Voici quelques types de traitement courants.</span><span class="sxs-lookup"><span data-stu-id="5fc46-166">The following are some common types of processing.</span></span> <span data-ttu-id="5fc46-167">(Cette liste n’est certainement pas exhaustive.)</span><span class="sxs-lookup"><span data-stu-id="5fc46-167">(This list is certainly not exhaustive.)</span></span>

- <span data-ttu-id="5fc46-168">Écriture de données d’événement dans un stockage froid pour archivage ou traitement analytique par lots.</span><span class="sxs-lookup"><span data-stu-id="5fc46-168">Writing event data to cold storage, for archiving or batch analytics.</span></span>

- <span data-ttu-id="5fc46-169">Analytique de séquence à chaud (« hot path analytics »), avec une analyse du flux d’événements en (quasi) temps réel, pour détecter les anomalies, reconnaître les modèles dans des fenêtres de temps glissantes ou déclencher des alertes quand une condition spécifique est rencontrée dans le flux.</span><span class="sxs-lookup"><span data-stu-id="5fc46-169">Hot path analytics, analyzing the event stream in (near) real time, to detect anomalies, recognize patterns over rolling time windows, or trigger alerts when a specific condition occurs in the stream.</span></span> 

- <span data-ttu-id="5fc46-170">Gestion de types de messages d’appareils non liés à la télémétrie, tels que les notifications et les alarmes.</span><span class="sxs-lookup"><span data-stu-id="5fc46-170">Handling special types of non-telemetry messages from devices, such as notifications and alarms.</span></span> 

- <span data-ttu-id="5fc46-171">Apprentissage automatique.</span><span class="sxs-lookup"><span data-stu-id="5fc46-171">Machine learning.</span></span>

<span data-ttu-id="5fc46-172">Les cadres gris représentent les composants d’un système IoT qui ne sont pas directement liés à la diffusion d’événements, mais qui sont inclus ici par souci d’exhaustivité.</span><span class="sxs-lookup"><span data-stu-id="5fc46-172">The boxes that are shaded gray show components of an IoT system that are not directly related to event streaming, but are included here for completeness.</span></span>

- <span data-ttu-id="5fc46-173">Le **registre d’appareils** est une base de données qui recense les appareils provisionnés, avec notamment leur ID et les métadonnées associées usuelles, telles que l’emplacement.</span><span class="sxs-lookup"><span data-stu-id="5fc46-173">The **device registry** is a database of the provisioned devices, including the device IDs and usually device metadata, such as location.</span></span>

- <span data-ttu-id="5fc46-174">L’**API de provisionnement** est une interface externe commune pour provisionner et inscrire de nouveaux appareils.</span><span class="sxs-lookup"><span data-stu-id="5fc46-174">The **provisioning API** is a common external interface for provisioning and registering new devices.</span></span>

- <span data-ttu-id="5fc46-175">Certaines solutions IoT autorisent l’envoi de **messages de commande et de contrôle** aux appareils.</span><span class="sxs-lookup"><span data-stu-id="5fc46-175">Some IoT solutions allow **command and control messages** to be sent to devices.</span></span>

> <span data-ttu-id="5fc46-176">Cette section a fourni une vue d’ensemble globale d’IoT et bon nombre de subtilités et d’écueils sont à prendre en considération.</span><span class="sxs-lookup"><span data-stu-id="5fc46-176">This section has presented a very high-level view of IoT, and there are many subtleties and challenges to consider.</span></span> <span data-ttu-id="5fc46-177">Pour obtenir une description et une présentation plus détaillées d’une architecture de référence, consultez [Microsoft Azure IoT Reference Architecture][iot-ref-arch] (PDF téléchargeable).</span><span class="sxs-lookup"><span data-stu-id="5fc46-177">For a more detailed reference architecture and discussion, see the [Microsoft Azure IoT Reference Architecture][iot-ref-arch] (PDF download).</span></span>

 <!-- links -->

[competing-consumers]: ../../patterns/competing-consumers.md
[iot-ref-arch]: https://azure.microsoft.com/en-us/updates/microsoft-azure-iot-reference-architecture-available/
[minimize-coordination]: ../design-principles/minimize-coordination.md

