---
title: "Sélection d’une technologie d’orchestration de pipeline de données"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 17aeb871bc815793295ed610795e5e83de72c637
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-data-pipeline-orchestration-technology-in-azure"></a><span data-ttu-id="4154c-102">Sélection d’une technologie d’orchestration de pipeline de données dans Azure</span><span class="sxs-lookup"><span data-stu-id="4154c-102">Choosing a data pipeline orchestration technology in Azure</span></span>

<span data-ttu-id="4154c-103">La plupart des solutions de Big Data se composent d’opérations de traitement des données répétées, encapsulées dans des workflows.</span><span class="sxs-lookup"><span data-stu-id="4154c-103">Most big data solutions consist of repeated data processing operations, encapsulated in workflows.</span></span> <span data-ttu-id="4154c-104">Un orchestrateur de pipeline est un outil qui permet d’automatiser ces workflows.</span><span class="sxs-lookup"><span data-stu-id="4154c-104">A pipeline orchestrator is a tool that helps to automate these workflows.</span></span> <span data-ttu-id="4154c-105">Un orchestrateur peut planifier des travaux, exécuter des workflows et coordonner les dépendances entre des tâches.</span><span class="sxs-lookup"><span data-stu-id="4154c-105">An orchestrator can schedule jobs, execute workflows, and coordinate dependencies among tasks.</span></span>

## <a name="what-are-your-options-for-data-pipeline-orchestration"></a><span data-ttu-id="4154c-106">Quelles sont vos options d’orchestration de pipeline de données ?</span><span class="sxs-lookup"><span data-stu-id="4154c-106">What are your options for data pipeline orchestration?</span></span>

<span data-ttu-id="4154c-107">Dans Azure, les outils et services suivants répondent aux exigences principales d’orchestration de pipeline, de flux de contrôle et de déplacement des données :</span><span class="sxs-lookup"><span data-stu-id="4154c-107">In Azure, the following services and tools will meet the core requirements for pipeline orchestration, control flow, and data movement:</span></span>

- [<span data-ttu-id="4154c-108">Azure Data Factory</span><span class="sxs-lookup"><span data-stu-id="4154c-108">Azure Data Factory</span></span>](/azure/data-factory/)
- [<span data-ttu-id="4154c-109">Oozie sur HDInsight</span><span class="sxs-lookup"><span data-stu-id="4154c-109">Oozie on HDInsight</span></span>](/azure/hdinsight/hdinsight-use-oozie-linux-mac)
- [<span data-ttu-id="4154c-110">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="4154c-110">SQL Server Integration Services (SSIS)</span></span>](/sql/integration-services/sql-server-integration-services)

<span data-ttu-id="4154c-111">Ces services et outils peuvent être utilisés indépendamment l’un de l’autre ou conjointement pour créer une solution hybride.</span><span class="sxs-lookup"><span data-stu-id="4154c-111">These services and tools can be used independently from one another, or used together to create a hybrid solution.</span></span> <span data-ttu-id="4154c-112">Par exemple, Integration Runtime (IR) dans Azure Data Factory V2 peut exécuter en mode natif des packages SSIS dans un environnement de calcul Azure géré.</span><span class="sxs-lookup"><span data-stu-id="4154c-112">For example, the Integration Runtime (IR) in Azure Data Factory V2 can natively execute SSIS packages in a managed Azure compute environment.</span></span> <span data-ttu-id="4154c-113">S’il existe certains recoupements des fonctionnalités entre ces services, il existe aussi quelques différences importantes.</span><span class="sxs-lookup"><span data-stu-id="4154c-113">While there is some overlap in functionality between these services, there are a few key differences.</span></span>

## <a name="key-selection-criteria"></a><span data-ttu-id="4154c-114">Critères de sélection principaux</span><span class="sxs-lookup"><span data-stu-id="4154c-114">Key Selection Criteria</span></span>

<span data-ttu-id="4154c-115">Pour restreindre les choix, commencez par répondre aux questions suivantes :</span><span class="sxs-lookup"><span data-stu-id="4154c-115">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="4154c-116">Avez-vous besoin des fonctionnalités de Big Data pour déplacer et transformer vos données ?</span><span class="sxs-lookup"><span data-stu-id="4154c-116">Do you need big data capabilities for moving and transforming your data?</span></span> <span data-ttu-id="4154c-117">Généralement, cela signifie des gigaoctets à des téraoctets de données.</span><span class="sxs-lookup"><span data-stu-id="4154c-117">Usually this means multi-gigabytes to terabytes of data.</span></span> <span data-ttu-id="4154c-118">Dans ce cas, limitez vos options à celles qui sont le mieux adaptées au Big Data.</span><span class="sxs-lookup"><span data-stu-id="4154c-118">If yes, then narrow your options to those that best suited for big data.</span></span>

- <span data-ttu-id="4154c-119">Avez-vous besoin d’un service géré qui puisse fonctionner à l’échelle ?</span><span class="sxs-lookup"><span data-stu-id="4154c-119">Do you require a managed service that can operate at scale?</span></span> <span data-ttu-id="4154c-120">Dans ce cas, sélectionnez un des services cloud non limité par votre puissance de traitement local.</span><span class="sxs-lookup"><span data-stu-id="4154c-120">If yes, select one of the cloud-based services that aren't limited by your local processing power.</span></span>

- <span data-ttu-id="4154c-121">Certaines de vos données sources sont-elles locales ?</span><span class="sxs-lookup"><span data-stu-id="4154c-121">Are some of your data sources located on-premises?</span></span> <span data-ttu-id="4154c-122">Dans l’affirmative, recherchez les options qui peuvent fonctionner avec les sources de données ou les destinations locales et sur cloud.</span><span class="sxs-lookup"><span data-stu-id="4154c-122">If yes, look for options that can work with both cloud and on-premises data sources or destinations.</span></span>

- <span data-ttu-id="4154c-123">Vos données sources sont-elles stockées dans le stockage Blob sur un système de fichiers HDFS ?</span><span class="sxs-lookup"><span data-stu-id="4154c-123">Is your source data stored in Blob storage on an HDFS filesystem?</span></span> <span data-ttu-id="4154c-124">Dans ce cas, choisissez une option qui prend en charge les requêtes Hive.</span><span class="sxs-lookup"><span data-stu-id="4154c-124">If so, choose an option that supports Hive queries.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="4154c-125">Matrice des fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="4154c-125">Capability matrix</span></span>

<span data-ttu-id="4154c-126">Les tableaux suivants résument les principales différences entre les fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="4154c-126">The following tables summarize the key differences in capabilities.</span></span>

### <a name="general-capabilities"></a><span data-ttu-id="4154c-127">Fonctionnalités générales</span><span class="sxs-lookup"><span data-stu-id="4154c-127">General capabilities</span></span>

| | <span data-ttu-id="4154c-128">Azure Data Factory</span><span class="sxs-lookup"><span data-stu-id="4154c-128">Azure Data Factory</span></span> | <span data-ttu-id="4154c-129">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="4154c-129">SQL Server Integration Services (SSIS)</span></span> | <span data-ttu-id="4154c-130">Oozie sur HDInsight</span><span class="sxs-lookup"><span data-stu-id="4154c-130">Oozie on HDInsight</span></span>
| --- | --- | --- | --- |
| <span data-ttu-id="4154c-131">Adresses IP gérées</span><span class="sxs-lookup"><span data-stu-id="4154c-131">Managed</span></span> | <span data-ttu-id="4154c-132">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-132">Yes</span></span> | <span data-ttu-id="4154c-133">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-133">No</span></span> | <span data-ttu-id="4154c-134">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-134">Yes</span></span> |
| <span data-ttu-id="4154c-135">Sur le cloud</span><span class="sxs-lookup"><span data-stu-id="4154c-135">Cloud-based</span></span> | <span data-ttu-id="4154c-136">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-136">Yes</span></span> | <span data-ttu-id="4154c-137">Non (Local)</span><span class="sxs-lookup"><span data-stu-id="4154c-137">No (local)</span></span> | <span data-ttu-id="4154c-138">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-138">Yes</span></span> |
| <span data-ttu-id="4154c-139">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="4154c-139">Prerequisite</span></span> | <span data-ttu-id="4154c-140">Abonnement Azure</span><span class="sxs-lookup"><span data-stu-id="4154c-140">Azure Subscription</span></span> | <span data-ttu-id="4154c-141">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4154c-141">SQL Server</span></span>  | <span data-ttu-id="4154c-142">Abonnement Azure, cluster HDInsight</span><span class="sxs-lookup"><span data-stu-id="4154c-142">Azure Subscription, HDInsight cluster</span></span> |
| <span data-ttu-id="4154c-143">Outils de gestion</span><span class="sxs-lookup"><span data-stu-id="4154c-143">Management tools</span></span> | <span data-ttu-id="4154c-144">Portail Azure, PowerShell, CLI, .NET SDK</span><span class="sxs-lookup"><span data-stu-id="4154c-144">Azure Portal, PowerShell, CLI, .NET SDK</span></span> | <span data-ttu-id="4154c-145">SSMS, PowerShell</span><span class="sxs-lookup"><span data-stu-id="4154c-145">SSMS, PowerShell</span></span> | <span data-ttu-id="4154c-146">Interpréteur de commandes Bash, API REST Oozie, IU Web Oozie</span><span class="sxs-lookup"><span data-stu-id="4154c-146">Bash shell, Oozie REST API, Oozie web UI</span></span> |
| <span data-ttu-id="4154c-147">Tarifs</span><span class="sxs-lookup"><span data-stu-id="4154c-147">Pricing</span></span> | <span data-ttu-id="4154c-148">Paiement à l’utilisation</span><span class="sxs-lookup"><span data-stu-id="4154c-148">Pay per usage</span></span> | <span data-ttu-id="4154c-149">Licences / paiement des fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="4154c-149">Licensing / pay for features</span></span> | <span data-ttu-id="4154c-150">Aucun frais supplémentaire sur l’exécution du cluster HDInsight</span><span class="sxs-lookup"><span data-stu-id="4154c-150">No additional charge on top of running the HDInsight cluster</span></span> |

### <a name="pipeline-capabilities"></a><span data-ttu-id="4154c-151">Fonctionnalités du pipeline</span><span class="sxs-lookup"><span data-stu-id="4154c-151">Pipeline capabilities</span></span>

| | <span data-ttu-id="4154c-152">Azure Data Factory</span><span class="sxs-lookup"><span data-stu-id="4154c-152">Azure Data Factory</span></span> | <span data-ttu-id="4154c-153">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="4154c-153">SQL Server Integration Services (SSIS)</span></span> | <span data-ttu-id="4154c-154">Oozie sur HDInsight</span><span class="sxs-lookup"><span data-stu-id="4154c-154">Oozie on HDInsight</span></span>
| --- | --- | --- | --- |
| <span data-ttu-id="4154c-155">Copier des données</span><span class="sxs-lookup"><span data-stu-id="4154c-155">Copy data</span></span> | <span data-ttu-id="4154c-156">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-156">Yes</span></span> | <span data-ttu-id="4154c-157">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-157">Yes</span></span> | <span data-ttu-id="4154c-158">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-158">Yes</span></span> |
| <span data-ttu-id="4154c-159">Transformations personnalisées</span><span class="sxs-lookup"><span data-stu-id="4154c-159">Custom transformations</span></span> | <span data-ttu-id="4154c-160">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-160">Yes</span></span> | <span data-ttu-id="4154c-161">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-161">Yes</span></span> | <span data-ttu-id="4154c-162">Oui (travaux MapReduce, Pig et Hive)</span><span class="sxs-lookup"><span data-stu-id="4154c-162">Yes (MapReduce, Pig, and Hive jobs)</span></span> |
| <span data-ttu-id="4154c-163">Notation d’Azure Machine Learning</span><span class="sxs-lookup"><span data-stu-id="4154c-163">Azure Machine Learning scoring</span></span> | <span data-ttu-id="4154c-164">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-164">Yes</span></span> | <span data-ttu-id="4154c-165">Oui (avec des scripts)</span><span class="sxs-lookup"><span data-stu-id="4154c-165">Yes (with scripting)</span></span> | <span data-ttu-id="4154c-166">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-166">No</span></span> |
| <span data-ttu-id="4154c-167">HDInsight à la demande</span><span class="sxs-lookup"><span data-stu-id="4154c-167">HDInsight On-Demand</span></span> | <span data-ttu-id="4154c-168">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-168">Yes</span></span> | <span data-ttu-id="4154c-169">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-169">No</span></span> | <span data-ttu-id="4154c-170">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-170">No</span></span> |
| <span data-ttu-id="4154c-171">Azure Batch</span><span class="sxs-lookup"><span data-stu-id="4154c-171">Azure Batch</span></span> | <span data-ttu-id="4154c-172">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-172">Yes</span></span> | <span data-ttu-id="4154c-173">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-173">No</span></span> | <span data-ttu-id="4154c-174">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-174">No</span></span> |
| <span data-ttu-id="4154c-175">Pig, Hive, MapReduce</span><span class="sxs-lookup"><span data-stu-id="4154c-175">Pig, Hive, MapReduce</span></span> | <span data-ttu-id="4154c-176">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-176">Yes</span></span> | <span data-ttu-id="4154c-177">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-177">No</span></span> | <span data-ttu-id="4154c-178">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-178">Yes</span></span> |
| <span data-ttu-id="4154c-179">Spark</span><span class="sxs-lookup"><span data-stu-id="4154c-179">Spark</span></span> | <span data-ttu-id="4154c-180">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-180">Yes</span></span> | <span data-ttu-id="4154c-181">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-181">No</span></span> | <span data-ttu-id="4154c-182">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-182">No</span></span> |
| <span data-ttu-id="4154c-183">Exécuter le Package SSIS</span><span class="sxs-lookup"><span data-stu-id="4154c-183">Execute SSIS Package</span></span> | <span data-ttu-id="4154c-184">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-184">Yes</span></span> | <span data-ttu-id="4154c-185">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-185">Yes</span></span> | <span data-ttu-id="4154c-186">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-186">No</span></span> |
| <span data-ttu-id="4154c-187">Flux de contrôle</span><span class="sxs-lookup"><span data-stu-id="4154c-187">Control flow</span></span> | <span data-ttu-id="4154c-188">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-188">Yes</span></span> | <span data-ttu-id="4154c-189">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-189">Yes</span></span> | <span data-ttu-id="4154c-190">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-190">Yes</span></span> |
| <span data-ttu-id="4154c-191">Accès aux données locales</span><span class="sxs-lookup"><span data-stu-id="4154c-191">Access on-premises data</span></span> | <span data-ttu-id="4154c-192">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-192">Yes</span></span> | <span data-ttu-id="4154c-193">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-193">Yes</span></span> | <span data-ttu-id="4154c-194">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-194">No</span></span> |

### <a name="scalability-capabilities"></a><span data-ttu-id="4154c-195">Fonctionnalités d’extensibilité</span><span class="sxs-lookup"><span data-stu-id="4154c-195">Scalability capabilities</span></span>

| | <span data-ttu-id="4154c-196">Azure Data Factory</span><span class="sxs-lookup"><span data-stu-id="4154c-196">Azure Data Factory</span></span> | <span data-ttu-id="4154c-197">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="4154c-197">SQL Server Integration Services (SSIS)</span></span> | <span data-ttu-id="4154c-198">Oozie sur HDInsight</span><span class="sxs-lookup"><span data-stu-id="4154c-198">Oozie on HDInsight</span></span>
| --- | --- | --- | --- |
| <span data-ttu-id="4154c-199">Monter en puissance</span><span class="sxs-lookup"><span data-stu-id="4154c-199">Scale up</span></span> | <span data-ttu-id="4154c-200">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-200">Yes</span></span> | <span data-ttu-id="4154c-201">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-201">No</span></span> | <span data-ttu-id="4154c-202">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-202">No</span></span> |
| <span data-ttu-id="4154c-203">Montée en charge</span><span class="sxs-lookup"><span data-stu-id="4154c-203">Scale out</span></span> | <span data-ttu-id="4154c-204">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-204">Yes</span></span> | <span data-ttu-id="4154c-205">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-205">No</span></span> | <span data-ttu-id="4154c-206">Oui (via l’ajout de nœuds de travail en cluster)</span><span class="sxs-lookup"><span data-stu-id="4154c-206">Yes (by adding worker nodes to cluster)</span></span> |
| <span data-ttu-id="4154c-207">Optimisé pour le Big Data</span><span class="sxs-lookup"><span data-stu-id="4154c-207">Optimized for big data</span></span> | <span data-ttu-id="4154c-208">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-208">Yes</span></span> | <span data-ttu-id="4154c-209">Non </span><span class="sxs-lookup"><span data-stu-id="4154c-209">No</span></span> | <span data-ttu-id="4154c-210">OUI</span><span class="sxs-lookup"><span data-stu-id="4154c-210">Yes</span></span> |
