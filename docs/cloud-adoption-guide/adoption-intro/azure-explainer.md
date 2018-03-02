---
title: "Explicatif : comment Azure fonctionne-t-il ?"
description: "Explique le fonctionnement interne d’Azure"
author: petertay
ms.openlocfilehash: 847d24b7057d80f3d34aac7900cfb64fec60a640
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="explainer-how-does-azure-work"></a><span data-ttu-id="5ae84-103">Explicatif : comment Azure fonctionne-t-il ?</span><span class="sxs-lookup"><span data-stu-id="5ae84-103">Explainer: How does Azure work?</span></span>

<span data-ttu-id="5ae84-104">Azure est la plateforme de cloud public de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5ae84-104">Azure is Microsoft's public cloud platform.</span></span> <span data-ttu-id="5ae84-105">Azure offre une grande collection de services, y compris la plateforme en tant que service (PaaS), l’infrastructure en tant que service (IaaS), la base de données en tant que service (DBaaS) et bien d’autres.</span><span class="sxs-lookup"><span data-stu-id="5ae84-105">Azure offers a large collection of services including platform as a service (PaaS), infrastructure as a service (IaaS), database as a service (DBaaS), and many others.</span></span> <span data-ttu-id="5ae84-106">Mais ce qu’est -ce qu’Azure exactement et comment fonctionne-t-il ?</span><span class="sxs-lookup"><span data-stu-id="5ae84-106">But what exactly is Azure, and how does it work?</span></span>

<span data-ttu-id="5ae84-107">Azure, comme les autres plateformes de cloud, s’appuie sur une technologie appelée **virtualisation**.</span><span class="sxs-lookup"><span data-stu-id="5ae84-107">Azure, like other cloud platforms, relies on a technology known as **virtualization**.</span></span> <span data-ttu-id="5ae84-108">Le matériel informatique peut être, en général, émulé dans le logiciel, car il s’agit simplement d’un ensemble d’instructions encodées de façon permanente ou semi-permanente en silicium.</span><span class="sxs-lookup"><span data-stu-id="5ae84-108">Most computer hardware can be emulated in software, because most computer hardware is simply a set of instructions permanently or semi-permanently encoded in silicon.</span></span> <span data-ttu-id="5ae84-109">À l’aide d’une couche d’émulation qui mappe les instructions du logiciel pour obtenir des instructions de matériel, le matériel virtualisé peut s’exécuter dans le logiciel comme s’il s’agissait du matériel réel lui-même.</span><span class="sxs-lookup"><span data-stu-id="5ae84-109">Using an emulation layer that maps software instructions to hardware instructions, virtualized hardware can execute in software as if it were the actual hardware itself.</span></span>

<span data-ttu-id="5ae84-110">Fondamentalement, le cloud est un ensemble de serveurs physiques dans un ou plusieurs centres de données qui exécutent un matériel virtualisé pour le compte de clients.</span><span class="sxs-lookup"><span data-stu-id="5ae84-110">Essentially, the cloud is a set of physical servers in one or more datacenters that execute virtualized hardware on behalf of customers.</span></span> <span data-ttu-id="5ae84-111">Ainsi, comment le cloud crée, démarre, arrête et supprime-t-il simultanément des millions d’instances d’un matériel virtualisé pour des millions de clients ?</span><span class="sxs-lookup"><span data-stu-id="5ae84-111">So how does the cloud create, start, stop, and delete millions of instances of virtualized hardware for millions of customers simultaneously?</span></span>

<span data-ttu-id="5ae84-112">Pour le comprendre, examinons l’architecture du matériel dans le centre de données.</span><span class="sxs-lookup"><span data-stu-id="5ae84-112">To understand this, let's look at the architecture of the hardware in the datacenter.</span></span>  <span data-ttu-id="5ae84-113">Dans chaque centre de données se trouve une collection de serveurs dans les racks de serveurs.</span><span class="sxs-lookup"><span data-stu-id="5ae84-113">Within each datacenter is a collection of servers sitting in server racks.</span></span> <span data-ttu-id="5ae84-114">Chaque rack de serveurs contient de nombreuses **lames** de serveurs ainsi qu’un commutateur de réseau fournissant une connectivité réseau et une unité de distribution de l’alimentation (PDU) fournissant l’électricité.</span><span class="sxs-lookup"><span data-stu-id="5ae84-114">Each server rack contains many server **blades** as well as a network switch providing network connectivity and a power distribution unit (PDU) providing power.</span></span> <span data-ttu-id="5ae84-115">Les racks sont parfois regroupés en unités plus grandes appelées **clusters**.</span><span class="sxs-lookup"><span data-stu-id="5ae84-115">Racks are sometimes grouped together in larger units known as **clusters**.</span></span> 

<span data-ttu-id="5ae84-116">Dans chaque rack ou cluster, la plupart des serveurs sont désignés pour exécuter ces instances du matériel virtualisé au nom de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5ae84-116">Within each rack or cluster, most of the servers are designated to run these virtualized hardware instances on behalf of the user.</span></span> <span data-ttu-id="5ae84-117">Toutefois, plusieurs serveurs exécutent le logiciel de gestion du cloud connu sous le nom de contrôleur de structure.</span><span class="sxs-lookup"><span data-stu-id="5ae84-117">However, a number of the servers run cloud management software known as a fabric controller.</span></span> <span data-ttu-id="5ae84-118">Le **contrôleur de structure** est une application distribuée avec beaucoup de responsabilités.</span><span class="sxs-lookup"><span data-stu-id="5ae84-118">The **fabric controller** is a distributed application with many responsibilities.</span></span> <span data-ttu-id="5ae84-119">Il alloue des services, analyse l’intégrité du serveur et les services qui y sont exécutés et répare des serveurs en cas d’échec.</span><span class="sxs-lookup"><span data-stu-id="5ae84-119">It allocates services, monitors the health of the server and the services running on it, and heals servers when they fail.</span></span>

<span data-ttu-id="5ae84-120">Chaque instance du contrôleur de structure est connectée à un autre ensemble de serveurs exécutant le logiciel d’orchestration du cloud, généralement appelé **frontal**.</span><span class="sxs-lookup"><span data-stu-id="5ae84-120">Each instance of the fabric controller is connected to another set of servers running cloud orchestration software, typically known as a **front end**.</span></span> <span data-ttu-id="5ae84-121">Le serveur frontal héberge les services web, les API RESTful et les bases de données Azure internes utilisés pour toutes les fonctions exécutées par le cloud.</span><span class="sxs-lookup"><span data-stu-id="5ae84-121">The front end hosts the web services, RESTful APIs, and internal Azure databases used for all functions the cloud performs.</span></span> 

<span data-ttu-id="5ae84-122">Par exemple, le serveur frontal héberge les services qui gèrent les demandes clients pour allouer des ressources Azure telles que [réseaux virtuels][vnet], [machines virtuelles] [ vms]et des services tels que [CosmosDB].</span><span class="sxs-lookup"><span data-stu-id="5ae84-122">For example, the front end hosts the services that handle customer requests to allocate Azure resources such as [virtual networks][vnet], [virtual machines][vms], and services like [CosmosDB].</span></span> <span data-ttu-id="5ae84-123">Tout d’abord, le serveur frontal valide l’utilisateur et vérifie que l’utilisateur est autorisé à allouer les ressources demandées.</span><span class="sxs-lookup"><span data-stu-id="5ae84-123">First, the front end validates the user and verifies the user is authorized to allocate the requested resources.</span></span> <span data-ttu-id="5ae84-124">Dans ce cas, le serveur frontal consulte une base de données pour localiser un rack de serveurs avec une capacité suffisante, puis fait en sorte que le contrôleur de structure sur le rack alloue la ressource.</span><span class="sxs-lookup"><span data-stu-id="5ae84-124">If so, the front end consults a database to locate a server rack with sufficient capacity, and then instructs the fabric controller on the rack to allocate the resource.</span></span>

<span data-ttu-id="5ae84-125">Par conséquent, tout simplement, Azure est une vaste collection de serveurs et de matériel réseau, ainsi qu’un ensemble complexe d’applications distribuées qui orchestrent la configuration et l’opération du matériel et des logiciels virtualisés sur ces serveurs.</span><span class="sxs-lookup"><span data-stu-id="5ae84-125">So, very simply, Azure is a huge collection of servers and networking hardware, along with a complex set of distributed applications that orchestrate the configuration and operation of the virtualized hardware and software on those servers.</span></span> <span data-ttu-id="5ae84-126">Cette orchestration rend alors Azure tellement puissant : les utilisateurs ne sont plus responsables du suivi ni de la mise à niveau du matériel, Azure effectue tout cela en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="5ae84-126">And it is this orchestration that makes Azure so powerful - users are no longer responsible for maintaining and upgrading hardware, Azure does all this behind the scenes.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="5ae84-127">étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="5ae84-127">Next steps</span></span>

* <span data-ttu-id="5ae84-128">Maintenant que vous comprenez le fonctionnement interne d’Azure, la première étape pour l’adoption d’Azure consiste à [comprendre l’identité numérique dans Azure](tenant-explainer.md).</span><span class="sxs-lookup"><span data-stu-id="5ae84-128">Now that you understand the internal functioning of Azure, the first step to adopting Azure is to [understand digital identity in Azure](tenant-explainer.md).</span></span> <span data-ttu-id="5ae84-129">Vous pouvez alors enfin [créer votre premier utilisateur dans Azure AD][docs-add-users-to-aad].</span><span class="sxs-lookup"><span data-stu-id="5ae84-129">You are then ready to [create your first user in Azure AD][docs-add-users-to-aad].</span></span>

<!-- Links -->

[cosmosdb]: /azure/cosmos-db/introduction
[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[vms]: /azure/virtual-machines/
[vnet]: /azure/virtual-network/virtual-networks-overview