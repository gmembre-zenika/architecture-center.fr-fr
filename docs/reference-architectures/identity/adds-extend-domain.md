---
title: Étendre Active Directory Domain Services (AD DS) à Azure
description: Étendre votre domaine local Active Directory à Azure
author: telmosampaio
ms.date: 05/02/2018
pnp.series.title: Identity management
pnp.series.prev: azure-ad
pnp.series.next: adds-forest
ms.openlocfilehash: 1e19d03998a18d997c2840f573e7bc79b24efbbc
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47427970"
---
# <a name="extend-active-directory-domain-services-ad-ds-to-azure"></a>Étendre Active Directory Domain Services (AD DS) à Azure

Cette architecture de référence indique comment étendre votre environnement Active Directory à Azure pour fournir des services d’authentification distribuée à l’aide d’Active Directory Domain Services (AD DS). [**Déployez cette solution**.](#deploy-the-solution)

[![0]][0] 

*Téléchargez un [fichier Visio][visio-download] de cette architecture.*

AD DS est utilisé pour authentifier un utilisateur, un ordinateur, une application ou autres identités qui sont incluses dans un domaine de sécurité. Il peut être hébergé localement, mais si votre application est hébergée en partie localement et en partie dans Azure, il peut être plus efficace de répliquer cette fonctionnalité dans Azure. Cela peut réduire la latence provoquée par l’envoi des demandes d’authentification et d’autorisation locale depuis le cloud vers les services AD DS exécutés localement. 

Cette architecture est couramment utilisée quand le réseau local et le réseau virtuel Azure sont connectés par une connexion VPN ou ExpressRoute. De plus, cette architecture prend en charge la réplication bidirectionnelle : si des modifications sont effectuées localement ou dans le cloud, les deux sources restent cohérentes. Parmi les utilisations courantes de cette architecture citons les applications hybrides dans lesquelles la fonctionnalité est répartie entre l’environnement local et Azure, et les applications et services qui effectuent l’authentification à l’aide d’Active Directory.

Pour plus d’informations, consultez [Choisir une solution pour intégrer l’environnement Active Directory local à Azure][considerations]. 

## <a name="architecture"></a>Architecture 

Cette architecture étend l’architecture illustrée dans [Zone DMZ entre Azure et Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Ses composants sont les suivants :

* **Réseau local**. Le réseau local comprend des serveurs Active Directory locaux qui peuvent effectuer l’authentification et l’autorisation pour les composants situés dans l’environnement local.
* **Serveurs Active Directory**. Il s’agit de contrôleurs de domaine qui implémentent des services d’annuaire (AD DS) s’exécutant en tant que machines virtuelles dans le cloud. Ces serveurs peuvent assurer l’authentification des composants s’exécutant dans votre réseau virtuel Azure.
* **Sous-réseau Active Directory**. Les serveurs AD DS sont hébergés dans un sous-réseau distinct. Des règles de groupe de sécurité réseau (NSG) protègent les serveurs AD DS et fournissent un pare-feu contre le trafic en provenance de sources inconnues.
* **Synchronisation Active Directory et passerelle Azure**. La passerelle Azure fournit une connexion entre le réseau local et le réseau virtuel Azure. Il peut s’agir d’une [connexion VPN][azure-vpn-gateway] ou [d’Azure ExpressRoute][azure-expressroute]. Toutes les demandes de synchronisation entre les serveurs Active Directory dans le cloud et dans l’environnement local passent par la passerelle. Les itinéraires définis par l’utilisateur gèrent le routage du trafic local vers Azure. Le trafic vers et depuis les serveurs Active Directory ne passe pas par les appliances virtuelles réseau dans ce scénario.

Pour plus d’informations sur les itinéraires définis par l’utilisateur et les appliances virtuelles réseau, consultez [Implémentation d’une architecture réseau hybride sécurisée dans Azure][implementing-a-secure-hybrid-network-architecture]. 

## <a name="recommendations"></a>Recommandations

Les recommandations suivantes s’appliquent à la plupart des scénarios. Suivez ces recommandations, sauf si vous avez un besoin spécifique qui vous oblige à les ignorer. 

### <a name="vm-recommendations"></a>Recommandations pour les machines virtuelles

Déterminez la [taille de machine virtuelle][vm-windows-sizes] requise en fonction du volume attendu de demandes d’authentification. Utilisez les spécifications des machines hébergeant AD DS localement comme point de départ et mettez-les en correspondance avec les tailles des machines virtuelles Azure. Une fois le déploiement effectué, surveillez l’utilisation et procédez à un ajustement d’échelle en fonction de la charge réelle qui pèse sur les machines virtuelles. Pour plus d’informations sur le dimensionnement des contrôleurs de domaine AD DS, consultez [Capacity Planning for Active Directory Domain Services][capacity-planning-for-adds] (Planification de la capacité pour Active Directory Domain Services).

Créez un disque de données virtuel distinct pour le stockage de la base de données, des journaux et de SYSVOL pour Active Directory. Ne stockez pas ces éléments sur le même disque que le système d’exploitation. Par défaut, les disques de données qui sont attachés à une machine virtuelle utilisent le cache à double écriture. Toutefois, cette forme de mise en cache peut entrer en conflit avec les exigences d’AD DS. Vous devez donc, sur le disque de données, définir le paramètre *Préférences de cache d’hôte* sur *Aucun*. Pour plus d’informations, consultez [Emplacement de la base de données Windows Server AD DS et de SYSVOL][adds-data-disks].

Déployez au moins deux machines virtuelles exécutant AD DS en tant que contrôleurs de domaine et ajoutez-les à un [groupe à haute disponibilité][availability-set].

### <a name="networking-recommendations"></a>Recommandations pour la mise en réseau

Configurez l’interface réseau de machine virtuelle pour chaque serveur AD DS avec une adresse IP privée statique pour la prise en charge complète du service de noms de domaine (DNS). Pour plus d’informations, consultez [Définition d’une adresse IP privée statique dans le portail Azure][set-a-static-ip-address].

> [!NOTE]
> Ne configurez pas l’interface réseau de machine virtuelle pour un serveur AD DS avec une adresse IP publique. Pour plus d’informations, consultez [Sécurité - Éléments à prendre en compte][security-considerations].
> 
> 

Le groupe de sécurité réseau du sous-réseau Active Directory requiert des règles pour autoriser le trafic entrant à partir de l’environnement local. Pour plus d’informations sur les ports utilisés par AD DS, consultez [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports] (Exigences de port pour Active Directory et Active Directory Domain Services). De plus, vérifiez que les tables d’itinéraires définis par l’utilisateur n’acheminent pas le trafic AD DS via les appliances virtuelles réseau utilisées dans cette architecture. 

### <a name="active-directory-site"></a>Site Active Directory

Dans AD DS, un site représente un emplacement physique, un réseau ou une collection d’appareils. Les sites AD DS permettent de gérer la réplication de la base de données AD DS en regroupant les objets AD DS qui sont situés à proximité les uns des autres et qui sont connectés par un réseau haut débit. AD DS inclut la logique qui assure la sélection de la meilleure stratégie de réplication de la base de données AD DS entre les sites.

Nous vous recommandons de créer un site AD DS incluant les sous-réseaux définis pour votre application dans Azure. Ensuite, configurez un lien de site entre vos sites AD DS locaux, afin qu’AD DS effectue automatiquement la réplication de base de données la plus efficace possible. Notez que cette réplication de base de données nécessite peu de tâches de configuration en plus de la configuration initiale.

### <a name="active-directory-operations-masters"></a>Maîtres d’opérations Active Directory

Vous pouvez affecter le rôle de maître d’opérations aux contrôleurs de domaine AD DS afin qu’ils prennent en charge le contrôle de la cohérence entre les instances des bases de données AD DS répliquées. Il existe cinq rôles de maître d’opérations : contrôleur de schéma, maître d’opérations des noms de domaine, maître d’identificateur relatif, émulateur maître de contrôleur de domaine principal et maître d’infrastructure. Pour plus d’informations sur ces rôles, consultez [What are Operations Masters?][ad-ds-operations-masters] (Présentation des maîtres d’opérations).

Nous vous déconseillons d’affecter des rôles de maître d’opérations aux contrôleurs de domaine déployés dans Azure.

### <a name="monitoring"></a>Surveillance

Surveillez les ressources des machines virtuelles de contrôleur de domaine ainsi que les services AD DS et créez un plan pour corriger les problèmes rapidement. Pour plus d’informations, consultez [Surveillance d’Active Directory][monitoring_ad]. Vous pouvez également installer des outils tels que [Microsoft Systems Center][microsoft_systems_center] sur le serveur de surveillance (voir le diagramme de l’architecture) pour effectuer ces tâches.  

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

AD DS est conçu dans un souci de scalabilité. Vous n’avez pas besoin de configurer un équilibreur de charge ou un contrôleur de trafic pour diriger les demandes vers les contrôleurs de domaine AD DS. Vous devez simplement configurer les machines virtuelles exécutant AD DS avec la taille appropriée suivant les exigences de charge de votre réseau, surveiller la charge sur les machines virtuelles et faire évoluer la configuration en fonction des besoins.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Déployez les machines virtuelles exécutant AD DS sur un [groupe à haute disponibilité][availability-set]. De plus, envisagez d’attribuer le rôle de [maître d’opérations en attente][standby-operations-masters] à un serveur, voire plus, selon vos besoins. Un maître d’opérations en attente est une copie active du maître d’opérations qui peut être utilisée à la place du serveur maître d’opérations principal au cours du basculement.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Effectuez des sauvegardes régulières d’AD DS. Ne vous contentez pas de copier les fichiers VHD des contrôleurs de domaine au lieu d’effectuer des sauvegardes régulières ; en effet, si le fichier de la base de données AD DS sur le disque dur virtuel est dans un état incohérent quand il est copié, il est impossible de redémarrer la base de données.

N’arrêtez pas une machine virtuelle du contrôleur de domaine à l’aide du portail Azure. Au lieu de cela, arrêtez et redémarrez à partir du système d’exploitation invité. Effectuer un arrêt par le biais du portail provoque la libération de la machine virtuelle et, dans la foulée, la réinitialisation de l’identifiant `VM-GenerationID` et de l’identifiant `invocationID` du référentiel Active Directory. Cette opération supprime le pool d’identificateurs relatifs (RID) AD DS et marque SYSVOL comme ne faisant pas autorité, et peut nécessiter la reconfiguration du contrôleur de domaine.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Les serveurs AD DS fournissent des services d’authentification, ce qui en fait une cible de choix pour les attaques. Pour les sécuriser, empêchez la connectivité Internet directe en les plaçant dans un sous-réseau distinct doté d’un groupe de sécurité réseau en guise de pare-feu. Fermez tous les ports sur les serveurs AD DS, à l’exception de ceux nécessaires à l’authentification, à l’autorisation et à la synchronisation des serveurs. Pour plus d’informations, consultez [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports] (Exigences de port pour Active Directory et Active Directory Domain Services).

Envisagez d’implémenter un périmètre de sécurité supplémentaire autour des serveurs avec une paire de sous-réseaux et d’appliances virtuelles réseau, comme décrit dans [Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access] (Mise en œuvre d’une architecture réseau hybride sécurisée avec un accès à Internet dans Azure).

Utilisez BitLocker ou le chiffrement de disque Azure pour chiffrer le disque qui héberge la base de données AD DS.

## <a name="deploy-the-solution"></a>Déployer la solution

Un déploiement pour cette architecture est disponible sur [GitHub][github]. Remarque : le déploiement entier peut prendre jusqu’à deux heures, en incluant la création de la passerelle VPN et l’exécution des scripts qui configurent AD DS.

### <a name="prerequisites"></a>Prérequis

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter"></a>Déployer le centre de données local simulé

1. Accédez au dossier `identity/adds-extend-domain` du dépôt GitHub.

2. Ouvrez le fichier `onprem.json` . Cherchez les instances de `adminPassword` et `Password` et ajoutez les valeurs pour les mots de passe.

3. Exécutez la commande suivante et attendez que le déploiement se termine :

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onprem.json --deploy
    ```

### <a name="deploy-the-azure-vnet"></a>Déployer le réseau virtuel Azure

1. Ouvrez le fichier `azure.json` .  Cherchez les instances de `adminPassword` et `Password` et ajoutez les valeurs pour les mots de passe. 

2. Dans le même fichier, recherchez les instances de `sharedKey` et entrez les clés partagées pour la connexion VPN. 

    ```bash
    "sharedKey": "",
    ```

3. Exécutez la commande suivante et attendez que le déploiement se termine.

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onoprem.json --deploy
    ```

   Déployez dans le même groupe de ressource que le réseau virtuel local.

### <a name="test-connectivity-with-the-azure-vnet"></a>Tester la connectivité avec le réseau virtuel Azure

Une fois le déploiement terminé, vous pouvez tester la connectivité entre l’environnement local simulé et le réseau virtuel Azure.

1. Utilisez le portail Azure, accédez au groupe de ressources que vous avez créé.

2. Trouvez la machine virtuelle nommée `ra-onpremise-mgmt-vm1`.

3. Cliquez sur `Connect` pour ouvrir une session Bureau à distance vers la machine virtuelle. Le nom d’utilisateur est `contoso\testuser`, et le mot de passe est celui que vous avez spécifié dans le fichier de paramètre `onprem.json`.

4. Une fois dans votre session Bureau à distance, ouvrez une autre session Bureau à distance vers 10.0.4.4, qui correspond à l’adresse IP de la machine virtuelle appelée `adds-vm1`. Le nom d’utilisateur est `contoso\testuser`, et le mot de passe est celui que vous avez spécifié dans le fichier de paramètre `azure.json`.

5. Une fois dans la session Bureau à distance pour `adds-vm1`, allez dans **Gestionnaire de serveur** et cliquez sur **Ajouter d’autres serveurs à gérer**. 

6. Dans l’onglet **Active Directory**, cliquez sur **Rechercher maintenant**. Vous devriez voir une liste des machines virtuelles AD, AD DS et web.

   ![](./images/add-servers-dialog.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les bonnes pratiques pour [créer une forêt de ressources AD DS][adds-resource-forest] dans Azure.
* Découvrez les bonnes pratiques pour [créer une infrastructure de services de fédération Active Directory (AD FS)][adfs] dans Azure.

<!-- links -->

[adds-resource-forest]: adds-forest.md
[adfs]: adfs.md
[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[ad-ds-operations-masters]: https://technet.microsoft.com/library/cc779716(v=ws.10).aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azure-expressroute]: /azure/expressroute/expressroute-introduction
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[capacity-planning-for-adds]: https://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[considerations]: ./considerations.md
[GitHub]: https://github.com/mspnp/reference-architectures/tree/master/identity/adds-extend-domain
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[security-considerations]: #security-considerations
[set-a-static-ip-address]: /azure/virtual-network/virtual-networks-static-private-ip-arm-pportal
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
[vm-windows-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes

[0]: ./images/adds-extend-domain.png "Architecture réseau hybride sécurisée avec Active Directory"
