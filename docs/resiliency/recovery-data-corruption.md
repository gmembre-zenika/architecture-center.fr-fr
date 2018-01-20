---
title: "Récupérer suite à une altération de données ou à une suppression accidentelle"
description: "Article dédié à la description de la récupération suite à une corruption de données ou à une suppression accidentelle de données et à la conception d’applications résilientes, hautement disponibles et tolérantes aux pannes, ainsi qu’à la planification de la récupération d’urgence"
author: MikeWasson
ms.date: 01/10/2018
ms.openlocfilehash: 76d2f996750d5a67b67bd5dc4977580f3b8abbc3
ms.sourcegitcommit: 3d6dba524cc7661740bdbaf43870de7728d60a01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="recover-from-data-corruption-or-accidental-deletion"></a><span data-ttu-id="fac87-103">Récupérer suite à une altération de données ou à une suppression accidentelle</span><span class="sxs-lookup"><span data-stu-id="fac87-103">Recover from data corruption or accidental deletion</span></span> 

<span data-ttu-id="fac87-104">Un plan solide de continuité d’activité englobe une portion dédiée à la récupération suite aux corruptions ou aux suppressions de données.</span><span class="sxs-lookup"><span data-stu-id="fac87-104">Part of a robust business continuity plan is having a plan if your data gets corrupted or accidentally deleted.</span></span> <span data-ttu-id="fac87-105">Vous trouverez ci-après des informations sur la récupération suite à une corruption ou à une suppression accidentelle de vos données en raison d’erreurs applicatives ou d’erreurs de l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="fac87-105">The following is information about recovery after data has been corrupted or accidentally deleted, due to application errors or operator error.</span></span>

## <a name="virtual-machines"></a><span data-ttu-id="fac87-106">Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="fac87-106">Virtual Machines</span></span>

<span data-ttu-id="fac87-107">Pour protéger les machines virtuelles Azure contre les erreurs applicatives ou les suppressions accidentelles, utilisez [Sauvegarde Azure](/azure/backup/).</span><span class="sxs-lookup"><span data-stu-id="fac87-107">To protect Azure Virtual Machines (VMs) from application errors or accidental deletion, use [Azure Backup](/azure/backup/).</span></span> <span data-ttu-id="fac87-108">Azure Backup prend en charge la création de sauvegardes cohérentes sur plusieurs disques de machine virtuelle.</span><span class="sxs-lookup"><span data-stu-id="fac87-108">Azure Backup enables the creation of backups that are consistent across multiple VM disks.</span></span> <span data-ttu-id="fac87-109">En outre, l’archivage de sauvegarde peut être répliqué entre les régions, afin d’assurer la prise en charge de la récupération suite à une perte de région.</span><span class="sxs-lookup"><span data-stu-id="fac87-109">In addition, the Backup Vault can be replicated across regions to provide recovery from region loss.</span></span>

## <a name="storage"></a><span data-ttu-id="fac87-110">Stockage</span><span class="sxs-lookup"><span data-stu-id="fac87-110">Storage</span></span>

<span data-ttu-id="fac87-111">Le stockage Azure fournit la résilience des données via des réplicas automatisés.</span><span class="sxs-lookup"><span data-stu-id="fac87-111">Azure Storage provides data resiliency through automated replicas.</span></span> <span data-ttu-id="fac87-112">Toutefois, cela n’empêche pas le code d’application ou les utilisateurs d’altérer des données de façon volontaire ou accidentelle.</span><span class="sxs-lookup"><span data-stu-id="fac87-112">However, this does not prevent application code or users from corrupting data, whether accidentally or maliciously.</span></span> <span data-ttu-id="fac87-113">La conservation de l’intégrité des données sous la menace d’erreurs des utilisateurs et des applications nécessite des techniques plus avancées, comme la copie des données vers un emplacement de stockage secondaire avec un journal d’audit.</span><span class="sxs-lookup"><span data-stu-id="fac87-113">Maintaining data fidelity in the face of application or user error requires more advanced techniques, such as copying the data to a secondary storage location with an audit log.</span></span> 

- <span data-ttu-id="fac87-114">**Objets blob de blocs**.</span><span class="sxs-lookup"><span data-stu-id="fac87-114">**Block blobs**.</span></span> <span data-ttu-id="fac87-115">Créez un instantané à un point dans le temps de chaque objet blob de blocs.</span><span class="sxs-lookup"><span data-stu-id="fac87-115">Create a point-in-time snapshot of each block blob.</span></span> <span data-ttu-id="fac87-116">Pour plus d’informations, consultez [Création d’un instantané d’objet blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob).</span><span class="sxs-lookup"><span data-stu-id="fac87-116">For more information, see [Creating a Snapshot of a Blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob).</span></span> <span data-ttu-id="fac87-117">Pour chaque instantané, vous êtes facturé pour le stockage requis pour stocker les différences identifiées sur l’objet blob depuis le dernier état d’instantané.</span><span class="sxs-lookup"><span data-stu-id="fac87-117">For each snapshot, you are only charged for the storage required to store the differences within the blob since the last snapshot state.</span></span> <span data-ttu-id="fac87-118">Les instantanés dépendant de l’existence de l’objet blob d’origine sur lequel ils sont basés, nous vous recommandons de copier les données sur un autre objet blob voire sur un autre compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="fac87-118">The snapshots are dependent on the existence of the original blob they are based on, so a copy operation to another blob or even another storage account is advisable.</span></span> <span data-ttu-id="fac87-119">Cela garantit la protection des données de sauvegarde contre tout risque de suppression accidentelle.</span><span class="sxs-lookup"><span data-stu-id="fac87-119">This ensures that backup data is properly protected against accidental deletion.</span></span> <span data-ttu-id="fac87-120">Vous pouvez utiliser [AzCopy](/azure/storage/common/storage-use-azcopy) ou [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) pour copier les objets blob vers un autre compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="fac87-120">You can use [AzCopy](/azure/storage/common/storage-use-azcopy) or [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) to copy the blobs to another storage account.</span></span>

- <span data-ttu-id="fac87-121">**Fichiers**.</span><span class="sxs-lookup"><span data-stu-id="fac87-121">**Files**.</span></span> <span data-ttu-id="fac87-122">Utilisez les [instantanés de partage (préversion)](/azure/storage/files/storage-how-to-use-files-snapshots), AzCopy ou PowerShell pour copier vos fichiers vers un autre compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="fac87-122">Use [share snapshots (preview)](/azure/storage/files/storage-how-to-use-files-snapshots), or use AzCopy or PowerShell to copy your files to another storage account.</span></span>

- <span data-ttu-id="fac87-123">**Tables**.</span><span class="sxs-lookup"><span data-stu-id="fac87-123">**Tables**.</span></span> <span data-ttu-id="fac87-124">Utilisez AzCopy pour exporter les données de table vers un autre compte de stockage dans une autre région.</span><span class="sxs-lookup"><span data-stu-id="fac87-124">Use AzCopy to export the table data into another storage account in another region.</span></span>

## <a name="database"></a><span data-ttu-id="fac87-125">Base de données</span><span class="sxs-lookup"><span data-stu-id="fac87-125">Database</span></span>

### <a name="azure-sql-database"></a><span data-ttu-id="fac87-126">Base de données SQL Azure</span><span class="sxs-lookup"><span data-stu-id="fac87-126">Azure SQL Database</span></span> 

<span data-ttu-id="fac87-127">SQL Database effectue automatiquement une combinaison de sauvegardes de bases de données complètes (toutes les semaines), de sauvegardes de bases de données différentielles (toutes les heures), et de sauvegardes de journaux de transactions (toutes les cinq à dix minutes) pour protéger votre entreprise contre la perte de données.</span><span class="sxs-lookup"><span data-stu-id="fac87-127">SQL Database automatically performs a combination of full database backups weekly, differential database backups hourly, and transaction log backups every five - ten minutes to protect your business from data loss.</span></span> <span data-ttu-id="fac87-128">Utilisez la restauration dans le temps pour restaurer une base de données à une heure antérieure.</span><span class="sxs-lookup"><span data-stu-id="fac87-128">Use point-in-time restore to restore a database to an earlier time.</span></span> <span data-ttu-id="fac87-129">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fac87-129">For more information, see:</span></span>

- [<span data-ttu-id="fac87-130">Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données</span><span class="sxs-lookup"><span data-stu-id="fac87-130">Recover an Azure SQL database using automated database backups</span></span>](/azure/sql-database/sql-database-recovery-using-backups)

- [<span data-ttu-id="fac87-131">Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL</span><span class="sxs-lookup"><span data-stu-id="fac87-131">Overview of business continuity with Azure SQL Database</span></span>](/azure/sql-database/sql-database-business-continuity)

### <a name="sql-server-on-vms"></a><span data-ttu-id="fac87-132">SQL Server sur une machine virtuelle</span><span class="sxs-lookup"><span data-stu-id="fac87-132">SQL Server on VMs</span></span>

<span data-ttu-id="fac87-133">Pour SQL Server exécuté sur une machine virtuelle, il existe deux options : les sauvegardes traditionnelles et la copie des journaux de transaction.</span><span class="sxs-lookup"><span data-stu-id="fac87-133">For SQL Server running on VMs, there are two options: traditional backups and log shipping.</span></span> <span data-ttu-id="fac87-134">Les sauvegardes traditionnelles vous permettent de restaurer les données à un point spécifique dans le temps, mais le processus de récupération est lent.</span><span class="sxs-lookup"><span data-stu-id="fac87-134">Traditional backups enables you to restore to a specific point in time, but the recovery process is slow.</span></span> <span data-ttu-id="fac87-135">Avec la restauration de sauvegardes traditionnelles, vous devez démarrer par une sauvegarde initiale complète, puis appliquer toutes les sauvegardes effectuées ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="fac87-135">Restoring traditional backups requires starting with an initial full backup, and then applying any backups taken after that.</span></span> <span data-ttu-id="fac87-136">La seconde option consiste en la configuration d’une session de copie des journaux de transaction pour reporter la restauration des sauvegardes des journaux (par exemple, de deux heures).</span><span class="sxs-lookup"><span data-stu-id="fac87-136">The second option is to configure a log shipping session to delay the restore of log backups (for example, by two hours).</span></span> <span data-ttu-id="fac87-137">Vous disposez ainsi d’une fenêtre permettant de récupérer des erreurs identifiées sur la sauvegarde primaire.</span><span class="sxs-lookup"><span data-stu-id="fac87-137">This provides a window to recover from errors made on the primary.</span></span>

### <a name="azure-cosmos-db"></a><span data-ttu-id="fac87-138">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="fac87-138">Azure Cosmos DB</span></span>

<span data-ttu-id="fac87-139">Azure Cosmos DB effectue des sauvegardes automatiques à des intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="fac87-139">Azure Cosmos DB automatically takes backups at regular intervals.</span></span> <span data-ttu-id="fac87-140">Les sauvegardes sont stockées séparément dans un autre service de stockage, et ces sauvegardes sont répliquées globalement pour garantir la résilience contre les sinistres régionaux.</span><span class="sxs-lookup"><span data-stu-id="fac87-140">Backups are stored separately in another storage service, and those backups are globally replicated for resiliency against regional disasters.</span></span> <span data-ttu-id="fac87-141">En cas de suppression accidentelle de votre base de données ou collection, vous pouvez émettre un ticket de support ou appeler le support technique Azure pour restaurer les données à partir de la dernière sauvegarde automatique.</span><span class="sxs-lookup"><span data-stu-id="fac87-141">If you accidentally delete your database or collection, you can file a support ticket or call Azure support to restore the data from the last automatic backup.</span></span> <span data-ttu-id="fac87-142">Pour plus d’informations, consultez [Sauvegarde et restauration en ligne automatiques avec Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).</span><span class="sxs-lookup"><span data-stu-id="fac87-142">For more information, see [Automatic online backup and restore with Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).</span></span>

### <a name="azure-database-for-mysql-azure-database-for-postresql"></a><span data-ttu-id="fac87-143">Azure Database pour MySQL, Azure Database pour PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="fac87-143">Azure Database for MySQL, Azure Database for PostreSQL</span></span>

<span data-ttu-id="fac87-144">Lorsque vous utilisez Azure Database pour MySQL ou Azure Database pour PostgreSQL, le service de base de données crée automatiquement une sauvegarde du service toutes les cinq minutes.</span><span class="sxs-lookup"><span data-stu-id="fac87-144">When using Azure Database for MySQL or Azure Database for PostreSQL, the database service automatically makes a backup of the service every five minutes.</span></span> <span data-ttu-id="fac87-145">À l’aide de cette fonctionnalité de sauvegarde automatique, vous pouvez restaurer le serveur et toutes ses bases de données dans un nouveau serveur à un moment antérieur.</span><span class="sxs-lookup"><span data-stu-id="fac87-145">Using this automatic backup feature you may restore the server and all its databases into a new server to an earlier point-in-time.</span></span> <span data-ttu-id="fac87-146">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fac87-146">For more information, see:</span></span>

- [<span data-ttu-id="fac87-147">Sauvegarde et restauration d’un serveur Azure Database pour MySQL à l’aide du portail Azure</span><span class="sxs-lookup"><span data-stu-id="fac87-147">How to back up and restore a server in Azure Database for MySQL by using the Azure portal</span></span>](/azure/mysql/howto-restore-server-portal)

- [<span data-ttu-id="fac87-148">Comment sauvegarder et restaurer un serveur Azure Database pour PostgreSQL à l’aide du portail Azure</span><span class="sxs-lookup"><span data-stu-id="fac87-148">How to backup and restore a server in Azure Database for PostgreSQL using the Azure portal</span></span>](/azure/postgresql/howto-restore-server-portal)
