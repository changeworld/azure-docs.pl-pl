---
title: Przygotowywanie infrastruktury platformy Azure do SAP wysokiej dostępności przy użyciu klastra pracy awaryjnej Windows i udostępnionego dysku dla SAP ASCS/SCS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować infrastrukturę platformy Azure do SAP wysokiej dostępności przy użyciu klastra pracy awaryjnej Windows i udostępnionego dysku dla wystąpienia usługi SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae3d1b36b89bb1bce1ff384bfa12a1bf643614fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408776"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Przygotowanie infrastruktury platformy Azure do SAP wysokiej dostępności przy użyciu klastra pracy awaryjnej Windows i udostępnionego dysku dla SAP ASCS/SCS.

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności — wiele identyfikatorów SID SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

W tym artykule opisano kroki należy wykonać w celu przygotowania infrastruktury platformy Azure do instalowania i konfigurowania systemu SAP o wysokiej dostępności na klastrze pracy awaryjnej Windows za pomocą *udostępniony dysk klastra* jako opcja do klastrowania Wystąpienie SAP ASCS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji należy przejrzeć ten artykuł:

* [Przewodnik dotyczący architektury: Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowywanie infrastruktury dla architektury 1 szablonu
Szablony usługi Azure Resource Manager dla programu SAP ułatwiają wdrażanie wymaganych zasobów.

Trójwarstwowa szablonów usługi Azure Resource Manager obsługują także scenariuszy wysokiej dostępności. Na przykład architektury 1 szablon ma dwa klastry. Każdy klaster jest SAP pojedynczego punktu awarii SAP ASCS/SCS i DBMS.

Poniżej przedstawiono, gdzie można uzyskać szablony usługi Azure Resource Manager przykładowy scenariusz, który opisano w tym artykule:

* [Azure Marketplace image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obraz z witryny Marketplace platformy Azure przy użyciu usługi Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Niestandardowy obraz przy użyciu dysków Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Aby przygotować infrastrukturę do architektury 1 szablonu:

- W witrynie Azure portal w **parametry** okienko w **SYSTEMAVAILABILITY** wybierz opcję **HA**.

  ![Rysunek 1: Ustaw parametry programu SAP o wysokiej dostępności usługi Azure Resource Manager][sap-ha-guide-figure-3000]

_**Rysunek 1:** Ustaw parametry programu SAP o wysokiej dostępności usługi Azure Resource Manager_


  Utwórz szablony:

  * **Maszyny wirtualne**:
    * Maszyny wirtualne serwera aplikacji SAP: \<SAPSystemSID\>- di -\<numer\>
    * ASCS/SCS klastra maszyn wirtualnych: \<SAPSystemSID\>-ascs-\<Number\>
    * Klaster systemu DBMS: \<SAPSystemSID\>- db-\<numer\>

  * **Karty dla wszystkich maszyn wirtualnych za pomocą adresów IP skojarzone sieciowe**:
    * \<SAPSystemSID\>-nic-di-\<Number\>
    * \<SAPSystemSID\>-nic-ascs-\<Number\>
    * \<SAPSystemSID\>-nic-db-\<Number\>

  * **Konta usługi Azure storage (tylko w przypadku dysków niezarządzanych)** :

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera aplikacji SAP: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS klastra maszyn wirtualnych: \<SAPSystemSID\>-avset-ascs
    * System DBMS klastra maszyn wirtualnych: \<SAPSystemSID\>-avset-db

  * **Usługa Azure wewnętrznego modułu równoważenia obciążenia**:
    * Z wszystkimi portami dla wystąpienia ASCS/SCS i adresu IP \<SAPSystemSID\>ascs - lb
    * Z wszystkimi portami dla adresu IP i bazami danych programu SQL Server \<SAPSystemSID\>- lb-db

  * **Sieciowa grupa zabezpieczeń**: \<SAPSystemSID\>-nsg-ascs-0  
    * Za pomocą zewnętrznego otwartego portu protokołu RDP (Remote Desktop) do \<SAPSystemSID\>0 - ascs maszyny wirtualnej

> [!NOTE]
> Wszystkie adresy IP kart sieciowych i Azure wewnętrznych modułów równoważenia obciążenia są dynamiczne domyślnie. Zmień je na statyczne adresy IP. Opisano, jak to zrobić w dalszej części tego artykułu.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Wdrażanie maszyn wirtualnych z połączeniem sieci firmowej (cross-premises) do użycia w środowisku produkcyjnym
Dla systemów SAP w środowisku produkcyjnym, wdrażanie maszyn wirtualnych platformy Azure za pomocą [łączności sieci firmowej (cross-premises)] [ planning-guide-2.2] przy użyciu usługi Azure VPN Gateway lub ExpressRoute systemu Azure.

> [!NOTE]
> Możesz użyć swojego wystąpienia usługi Azure Virtual Network. Sieć wirtualną i podsieć już zostały utworzone i przygotowane.
>
>

1. W witrynie Azure portal w **parametry** okienko w **NEWOREXISTINGSUBNET** wybierz opcję **istniejących**.
2. W **SUBNETID** Dodaj pełny ciąg Identyfikatora podsieci przygotować sieć platformy Azure, gdzie planujesz wdrożyć usługi Azure virtual machines.
3. Aby uzyskać listę wszystkich podsieci w sieci platformy Azure, uruchom następujące polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Identyfikator** pole zawiera wartość dla identyfikatora podsieci.
4. Aby uzyskać listę wszystkich wartości Identyfikatora podsieci, uruchom następujące polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Identyfikator podsieci wygląda następująco:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Wdrożenie oparte tylko na chmurze wystąpień SAP na potrzeby pokazu i testowania
Możesz wdrożyć systemu SAP o wysokiej dostępności w modelu wdrażania tylko w chmurze. Tego rodzaju wdrożenia przede wszystkim jest przydatne w przypadku pokaz i testowania przypadków użycia. Nie jest odpowiedni dla produkcyjne przypadki użycia.

- W witrynie Azure portal w **parametry** okienko w **NEWOREXISTINGSUBNET** wybierz opcję **nowe**. Pozostaw **SUBNETID** pole puste.

  Szablon SAP usługi Azure Resource Manager automatycznie tworzy sieć wirtualną platformy Azure i podsieć.

> [!NOTE]
> Należy również wdrożyć co najmniej jedną maszynę wirtualną dedykowanego dla usługi Active Directory i DNS w tym samym wystąpieniu usługi Azure Virtual Network. Szablon nie tworzy tych maszyn wirtualnych.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowywanie infrastruktury dla architektury 2 szablonu

Za pomocą tego szablonu usługi Azure Resource Manager dla programu SAP na celu uproszczenie wdrażania zasobów wymaganej infrastruktury SAP architektury szablon 2.

Poniżej przedstawiono, gdzie można uzyskać szablony usługi Azure Resource Manager, w tym scenariuszu wdrażania:

* [Azure Marketplace image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obraz z witryny Marketplace platformy Azure przy użyciu dysków Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Niestandardowy obraz przy użyciu dysków Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowywanie infrastruktury dla architektury 3 szablonu

Można przygotować infrastrukturę i skonfigurować SAP — wiele identyfikatorów SID. Na przykład można dodać dodatkowego wystąpienia SAP ASCS/SCS do *istniejących* konfiguracji klastra. Aby uzyskać więcej informacji, zobacz [skonfigurować dodatkowe wystąpienia SAP ASCS/SCS dla istniejącej konfiguracji klastra do utworzenia konfiguracji usługi SAP — wiele identyfikatorów SID w usłudze Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster — wiele identyfikatorów SID, możesz użyć wieloma identyfikatorami SID [szablony szybkiego startu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates).

Aby utworzyć nowy klaster — wiele identyfikatorów SID, należy wdrożyć trzy następujące szablony:

* [Szablon ASCS/SCS](#ASCS-SCS-template)
* [Baza danych szablonów](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje mają więcej szczegółów na temat szablonów i parametry, które należy podać w szablonach.

### <a name="ASCS-SCS-template"></a> Szablon ASCS/SCS

Szablon ASCS/SCS umożliwia wdrożenie dwóch maszyn wirtualnych, które służą do tworzenia klastra trybu failover systemu Windows Server, który obsługuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon — wiele identyfikatorów SID ASCS/SCS, w [szablonu — wiele identyfikatorów SID ASCS/SCS] [ sap-templates-3-tier-multisid-xscs-marketplace-image] lub [ASCS/SCS — wiele identyfikatorów SID szablonu przy użyciu dysków Managed Disks] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], wprowadź wartości dla następujących parametrów:

- **Prefiks zasobów**:  Ustaw prefiks zasobu, który służy jako prefiks wszystkie zasoby, które są tworzone podczas wdrażania. Ponieważ zasoby, które nie należą do tylko jednego systemu SAP, prefiks zasobu nie jest identyfikator SID jednego systemu SAP.  Prefiks musi mieć od trzech do sześciu znaków.
- **Stos typu**: Wybierz typ stosu systemu SAP. W zależności od typu stosu usługi Azure Load Balancer ma jedną (ABAP lub tylko dla środowiska Java) lub dwóch (ABAP + Java) prywatnych adresów IP na systemie SAP.
- **Typ systemu operacyjnego**: Wybierz system operacyjny maszyn wirtualnych.
- **Liczba systemów SAP**: Wybierz liczbę systemów SAP, które chcesz zainstalować w tym klastrze.
- **Dostępność systemu**: Wybierz **HA**.
- **Nazwa użytkownika administratora i hasło administratora**: Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
- **Nowej lub istniejącej podsieci**: Określić, czy można utworzyć nową sieć wirtualną i podsieć, lub użyj istniejącej podsieci. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz opcję **istniejących**.
- **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda następująco:

  /Subscriptions/\<identyfikator subskrypcji\>/resourceGroups/\<nazwy grupy zasobów\>/providers/Microsoft.Network/virtualNetworks/\<nazwa sieci wirtualnej\>/subnets/ \<Nazwa podsieci\>

Szablon umożliwia wdrożenie jedno wystąpienie usługi Azure Load Balancer, która obsługuje wiele systemów SAP:

- Wystąpienia ASCS powinny być skonfigurowane dla liczby wystąpień 00, 10, 20...
- Wystąpienia usług SCS powinny być skonfigurowane dla liczby wystąpień 01, 11, 21...
- Wystąpienia ASCS umieścić w kolejce replikacji serwera (Wywołujących) (tylko system Linux) powinny być skonfigurowane dla liczby wystąpień 02, 12, 22...
- Wystąpienia usług SCS Wywołujących (tylko system Linux) powinny być skonfigurowane dla liczby wystąpień 03, 13, 23...

Moduł równoważenia obciążenia zawiera 1 VIP(s) (2 dla systemu Linux), 1 x dla ASCS/SCS i 1 x adresów VIP dla Wywołujących (tylko system Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> Porty SAP ASCS/SCS
Poniższa lista zawiera wszystkie reguły, o których (gdzie x jest numer systemu SAP, na przykład 1, 2, 3...) równoważenia obciążenia:
- Porty Windows specyficznych dla wszystkich systemów SAP: 445, 5985
- Porty ASCS (liczba wystąpień x0): 32 x 0, 36 x 0, 39 x 0, 81 x 0, 5 x 013, 5 x 014, 5 x 016
- Porty SCS (liczba wystąpień x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- Porty ASCS Wywołujących w systemie Linux (liczba wystąpień x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Porty SCS Wywołujących w systemie Linux (liczba wystąpień x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Moduł równoważenia obciążenia jest skonfigurowany do używania następujące porty sondy (gdzie x jest numer systemu SAP, na przykład 1, 2, 3...):
- Port sondy modułu równoważenia obciążenia wewnętrznego ASCS/SCS: 620x0
- Wewnętrzny Wywołujących obciążenia równoważenia porcie sondowania (tylko system Linux): 621x2

### <a name="database-template"></a> Baza danych szablonów

Szablon bazy danych umożliwia wdrożenie co najmniej dwóch maszyn wirtualnych, które można użyć, aby zainstalować system zarządzania relacyjnymi bazami (danych RDBMS) dla jednego systemu SAP. Na przykład jeśli wdrożono szablon pięć systemów SAP ASCS/SCS, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon — wiele identyfikatorów SID bazy danych, w [bazy danych — wiele identyfikatorów SID szablonu] [ sap-templates-3-tier-multisid-db-marketplace-image] lub [szablonu — wiele identyfikatorów SID bazy danych przy użyciu dysków Managed Disks] [ sap-templates-3-tier-multisid-db-marketplace-image-md], wprowadź wartości dla następujących parametrów:

- **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Ten identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
- **Typ systemu operacyjnego**: Wybierz system operacyjny maszyn wirtualnych.
- **Atrybut DbType**: Wybierz typ bazy danych, który chcesz zainstalować w klastrze. Wybierz **SQL** Jeśli chcesz zainstalować program Microsoft SQL Server. Wybierz **HANA** Jeśli planowane jest instalowanie platformy SAP HANA na maszynach wirtualnych. Upewnij się, że wybrano prawidłowy typ systemu operacyjnego. Wybierz **Windows** dotyczącymi usług SQL i wybierz opcję dystrybucji systemu Linux dla platformy HANA. Usługa Azure Load Balancer, która jest połączona z maszynami wirtualnymi jest skonfigurowany do obsługi typu wybranej bazy danych:
  * **SQL**: Obciążenia równoważenia Równoważenie obciążenia port 1433. Upewnij się, że ten port jest używany do konfiguracji programu SQL Server AlwaysOn.
  * **HANA**: Obciążenia równoważenia porty Równoważenie obciążenia 35015 i 35017. Upewnij się, że instalowanie platformy SAP HANA z liczbą wystąpień **50**.
  Port sondy 62550 korzysta z modułu równoważenia obciążenia.
- **Rozmiar systemu SAP**: Ustaw liczbę protokoły SAP udostępnia nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
- **Dostępność systemu**: Wybierz **HA**.
- **Nazwa użytkownika administratora i hasło administratora**: Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
- **Identyfikator podsieci**: Wprowadź identyfikator podsieci, które były używane podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony jako część wdrożenia szablonu ASCS/SCS.

### <a name="application-servers-template"></a> Szablon serwerów aplikacji

Szablon serwerów aplikacji umożliwia wdrożenie co najmniej dwóch maszyn wirtualnych, które może służyć jako wystąpień serwera aplikacji SAP dla jednego systemu SAP. Na przykład jeśli wdrożono szablon pięć systemów SAP ASCS/SCS, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon — wiele identyfikatorów SID serwerów aplikacji, w [szablon — wiele identyfikatorów SID serwerów aplikacji] [ sap-templates-3-tier-multisid-apps-marketplace-image] lub [szablon — wiele identyfikatorów SID serwerów aplikacji za pomocą Managed Disks] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], wprowadź wartości dla następujących parametrów:

  -  **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Ten identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
  -  **Typ systemu operacyjnego**: Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu SAP**: Liczba protokoły SAP udostępnia nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
  -  **Dostępność systemu**: Wybierz **HA**.
  -  **Nazwa użytkownika administratora i hasło administratora**: Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
  -  **Identyfikator podsieci**: Wprowadź identyfikator podsieci, które były używane podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony jako część wdrożenia szablonu ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Usługa Azure Virtual Network
W naszym przykładzie przestrzeni adresowej wystąpienia usługi Azure Virtual Network to 10.0.0.0/16. Brak jednej podsieci o nazwie podsieci, z zakresu adresów 10.0.0.0/24. Wszystkich maszyn wirtualnych i wewnętrznych modułów równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzaj żadnych zmian, ustawienia sieciowe w systemie operacyjnym gościa. W tym adresy IP, serwery DNS i podsieci. Konfigurowanie ustawień sieciowych na platformie Azure. Usługa dynamicznej konfiguracji hosta (DHCP, Dynamic Host Configuration Protocol) propaguje ustawienia.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Adresy IP serwera DNS

Aby ustawić wymagany adres IP DNS adresy, wykonaj następujące czynności:

1. W witrynie Azure portal w **serwerów DNS** okienka, upewnij się, że Twoja sieć wirtualna **serwerów DNS** opcja jest ustawiona na **niestandardowych serwerów DNS**.
2. Wybierz ustawienia, na podstawie typu sieci, do których masz. Więcej informacji zawierają następujące zasoby:
   * [Łączność sieci firmowej (cross-premises)][planning-guide-2.2]: Dodaj adresy IP serwerów DNS w środowisku lokalnym.  
   Możesz rozszerzyć lokalne serwery DNS do maszyn wirtualnych, które działają na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których uruchomiono usługę DNS.
   * W przypadku wdrożeń maszyn wirtualnych odizolowane na platformie Azure: Należy wdrożyć dodatkowe maszyny wirtualnej, w tym samym wystąpieniu sieci wirtualnej, która służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure, które zostały ustawione do uruchamiania usługi DNS.

   ![Rysunek 2: Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure][sap-ha-guide-figure-3001]

   _**Rysunek 2:** Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure_

   > [!NOTE]
   > W przypadku zmiany adresów IP serwerów DNS, konieczne jest ponowne uruchomienie maszyn wirtualnych platformy Azure, aby zastosować zmiany i propagowanie nowych serwerów DNS.
   >
   >

W naszym przykładzie usługa DNS jest zainstalowany i skonfigurowany na tych maszynach wirtualnych Windows:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nazwy hostów i statyczne adresy IP dla klastrowanego wystąpienia SAP ASCS/SCS i klastrowane wystąpienie systemu DBMS

Do wdrożenia w środowisku lokalnym należy te hosta zastrzeżonych nazw i adresów IP:

| Rola nazwy hostów wirtualnych | Nazwa hosta wirtualnego | Wirtualne statyczny adres IP |
| --- | --- | --- |
| SAP ASCS/SCS klaster wirtualny host na imię (w przypadku klastra zarządzania) |pr1-ascs-vir |10.0.0.42 |
| Nazwa hosta wirtualnego wystąpienia SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nazwa systemu SAP DBMS do hostów wirtualnych drugiego klastra (Zarządzanie klastrem) |pr1-dbms-vir |10.0.0.32 |

Podczas tworzenia klastra, należy utworzyć wirtualny host na nazwy pr1-ascs-vir i pr1-dbms-vir i skojarzony adresy IP, które zarządzania klastrem. Aby dowiedzieć się, jak to zrobić, zobacz [zbieranie węzłów klastra w konfiguracji klastra][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Można ręcznie utworzyć inne dwie nazwy hostów wirtualnych, pr1 ascs sap i pr1-dbms-sap i skojarzony adresy IP na serwerze DNS. Dzięki tym zasobom, wystąpienie SAP ASCS/SCS klastra i klastrowanego wystąpienia systemu DBMS. Aby dowiedzieć się, jak to zrobić, zobacz [Utwórz nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ustawianie statycznych adresów IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyn wirtualnych do użycia w klastrze, należy ustawić statycznych adresów IP dla wszystkich maszyn wirtualnych. W tym w konfiguracji usługi Azure Virtual Network, a nie w systemie operacyjnym gościa.

1. W witrynie Azure portal wybierz **grupy zasobów** > **karty sieciowej** > **ustawienia** > **adresu IP**.
2. W **adresów IP** okienku w obszarze **przypisania**, wybierz opcję **statyczne**. W **adresu IP** wprowadź adres IP, który chcesz użyć.

   > [!NOTE]
   > Jeśli zmienisz adres IP karty sieciowej, musisz ponownie uruchomić maszyn wirtualnych platformy Azure, aby zastosować zmiany.  
   >
   >

   ![Rysunek 3: Ustawianie statycznych adresów IP dla każdej maszyny wirtualnej karty sieciowej][sap-ha-guide-figure-3002]

   _**Rysunek 3:** Ustawianie statycznych adresów IP dla każdej maszyny wirtualnej karty sieciowej_

   Powtórz ten krok dla wszystkich interfejsów sieciowych, dla wszystkich maszyn wirtualnych, włącznie ze maszyn wirtualnych, które chcesz użyć dla usługi Active Directory ani systemu DNS.

W tym przykładzie mamy tych maszyn wirtualnych i statyczne adresy IP:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwsze wystąpienie serwera aplikacji SAP |PR1-di-0 |PR1-karta sieciowa di-0 |10.0.0.50 |
| Drugie wystąpienie serwera aplikacji SAP |pr1-di-1 |PR1-karta sieciowa di-1 |10.0.0.51 |
| Przyciski ... |Przyciski ... |Przyciski ... |Przyciski ... |
| Ostatnie wystąpienie serwera aplikacji SAP |PR1-di-5 |PR1-karta sieciowa di-5 |10.0.0.55 |
| Pierwszym węźle klastra na potrzeby wystąpienia ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Drugim węźle klastra na potrzeby wystąpienia ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Pierwszym węźle klastra dla wystąpienia systemu DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Drugim węźle klastra dla wystąpienia systemu DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ustawianie statycznego adresu IP dla modułu równoważenia obciążenia wewnętrznego platformy Azure

Szablon SAP usługi Azure Resource Manager umożliwia utworzenie Azure wewnętrznego modułu równoważenia obciążenia używanego dla wystąpienia SAP ASCS/SCS i klastrze DBMS.

> [!IMPORTANT]
> Adres IP, nazwy hostów wirtualnych SAP ASCS/SCS jest taki sam jak adres IP modułu równoważenia obciążenia wewnętrznego SAP ASCS/SCS: pr1-lb-ascs.
> Adres IP, nazwy wirtualnego systemu DBMS jest taki sam jak adres IP modułu równoważenia obciążenia wewnętrznego systemu DBMS: pr1-lb-dbms.
>
>

Aby ustawić statyczny adres IP dla modułu równoważenia obciążenia wewnętrznego platformy Azure:

1. Początkowe wdrożenie ustawia adres IP modułu równoważenia obciążenia wewnętrznego **dynamiczne**. W witrynie Azure portal na **adresów IP** okienku w obszarze **przypisania**, wybierz opcję **statyczne**.
2. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-ascs** adres IP, nazwy hostów wirtualnych wystąpienia SAP ASCS/SCS.
3. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-dbms** adres IP, nazwy hostów wirtualnych wystąpienia systemu DBMS.

   ![Rysunek 4: Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia na potrzeby wystąpienia SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Rysunek 4:** Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia na potrzeby wystąpienia SAP ASCS/SCS_

W tym przykładzie mamy dwóch Azure wewnętrznych modułów równoważenia obciążenia, które mają te statyczne adresy IP:

| Rola modułu równoważenia obciążenia wewnętrznego platformy Azure | Nazwa modułu równoważenia obciążenia wewnętrznego platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| SAP ASCS/SCS wystąpienia wewnętrznego modułu równoważenia obciążenia |pr1-lb-ascs |10.0.0.43 |
| Systemu SAP DBMS wewnętrznego modułu równoważenia obciążenia |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Domyślne ASCS/SCS reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia

Szablon SAP usługi Azure Resource Manager umożliwia utworzenie porty, które są potrzebne:
* Wystąpienie ABAP ASCS przy użyciu domyślnej liczby wystąpień 00
* Wystąpienie usług SCS Java, przy użyciu domyślnej liczby wystąpień 01

Podczas instalacji wystąpienia usługi SAP ASCS/SCS należy użyć domyślnej liczby wystąpień 00 wystąpienia ABAP ASCS i numer wystąpienia domyślnego 01 wystąpienia SCS języka Java.

Następnie należy utworzyć wymagane wewnętrznego równoważenia obciążenia, punkty końcowe dla portów oprogramowanie SAP NetWeaver.

Aby utworzyć wymagane wewnętrznego równoważenia obciążenia, punktów końcowych, najpierw utwórz tych końcowych dla portów SAP NetWeaver ABAP ASCS równoważenia obciążenia:

| Nazwa reguły z równoważenia obciążenia/usługi | Domyślne numery portów | Konkretnych portów dla (ASCS wystąpienia o numerze wystąpienia 00) (Wywołujących z 10) |
| --- | --- | --- |
| Enqueue server / *lbrule3200* |32\<InstanceNumber\> |3200 |
| Serwer komunikatów ABAP / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Komunikat wewnętrzny ABAP / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Serwer komunikatów HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP uruchamiania usługi ASCS HTTP / *Lbrule50013* |5\<Numerwystąpienia\>13 |50013 |
| SAP Uruchom usługę ASCS HTTPS / *Lbrule50014* |5\<Numerwystąpienia\>14 |50014 |
| Umieścić w kolejce replikacji / *Lbrule50016* |5\<Numerwystąpienia\>16 |50016 |
| SAP uruchamiania usługi HTTP Wywołujących *Lbrule51013* |5\<Numerwystąpienia\>13 |51013 |
| SAP uruchamiania usługi HTTP Wywołujących *Lbrule51014* |5\<Numerwystąpienia\>14 |51014 |
| Usługa Windows Remote Management (WinRM) *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

**Tabela 1:** Numery portów wystąpień oprogramowania SAP NetWeaver ABAP ASCS

Następnie utwórz tych końcowych dla portów SAP NetWeaver Java SCS równoważenia obciążenia:

| Nazwa reguły z równoważenia obciążenia/usługi | Domyślne numery portów | Konkretnych portów dla (SCS wystąpienia o numerze wystąpienia 01) (Wywołujących z 11) |
| --- | --- | --- |
| Enqueue server / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Serwer bramy / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Serwer komunikatów Java / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Serwer komunikatów HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP uruchamiania usługi SCS HTTP / *Lbrule50113* |5\<Numerwystąpienia\>13 |50113 |
| SAP Uruchom usługę SCS HTTPS / *Lbrule50114* |5\<Numerwystąpienia\>14 |50114 |
| Umieścić w kolejce replikacji / *Lbrule50116* |5\<Numerwystąpienia\>16 |50116 |
| SAP uruchamiania usługi HTTP Wywołujących *Lbrule51113* |5\<Numerwystąpienia\>13 |51113 |
| SAP uruchamiania usługi HTTP Wywołujących *Lbrule51114* |5\<Numerwystąpienia\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

**Tabela 2:** Numery portów wystąpień SAP NetWeaver Java SCS

![Rysunek 5: Domyślne ASCS/SCS reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia][sap-ha-guide-figure-3004]

_**Rysunek 5:** Domyślne ASCS/SCS reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia_

Ustaw adres IP obciążenia równoważenia pr1-lb-dbms na adres IP, nazwy hostów wirtualnych wystąpienia systemu DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Zmień ASCS/SCS domyślne reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia

Jeśli chcesz użyć innej liczby wystąpień SAP ASCS lub SCS, możesz zmienić nazwy i wartości ich portów z wartościami domyślnymi.

1. W witrynie Azure portal wybierz  **\<SID\>ascs - lb moduł równoważenia obciążenia** > **załadować reguł równoważenia**.
2. W przypadku reguł, które należą do wystąpienia SAP ASCS lub SCS równoważenia obciążenia wszystkie zmieniać tych wartości:

   * Name (Nazwa)
   * Port
   * Port zaplecza

   Na przykład jeśli chcesz zmienić domyślny numer wystąpienia ASCS z zakresu od 00 do 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

   Oto przykład aktualizacji dla portu *lbrule3200*.

   ![Rysunek 6: Zmień ASCS/SCS domyślne reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia][sap-ha-guide-figure-3005]

   _**Rysunek 6:** Zmień ASCS/SCS domyślne reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Dodawanie maszyn wirtualnych Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych, należy dodać maszyn wirtualnych do domeny.

![Rysunek 7: Dodaj maszynę wirtualną do domeny][sap-ha-guide-figure-3006]

_**Rysunek 7:** Dodaj maszynę wirtualną do domeny_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Dodawanie wpisów rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS

Usługa Azure Load Balancer ma wewnętrznego modułu równoważenia obciążenia, zamyka połączeń w przypadku połączeń są w stanie bezczynności pewien okres czasu (limit czasu bezczynności). Procesy robocze SAP w oknie dialogowym wystąpień otwartych połączeń można umieścić w kolejce SAP przetwarzać tak szybko, jak pierwszy dodawania/usuwania z kolejki żądania musi być wysyłane. Te połączenia zazwyczaj pozostaje ustanowionych do procesu roboczego lub ponowne uruchomienie procesu umieścić w kolejce. Jednakże jeśli połączenie jest bezczynne na pewien okres czasu, Azure wewnętrznego modułu równoważenia obciążenia zamyka połączenia. Nie stanowi problemu, ponieważ proces roboczy SAP przywraca połączenie do procesu umieścić w kolejce, jeśli już nie istnieje. Te działania są udokumentowane w śladach projektanta procesów SAP, ale mogą tworzyć dużą ilością dodatkowa zawartość w tych śladów. To dobry pomysł, aby zmienić TCP/IP `KeepAliveTime` i `KeepAliveInterval` na obu węzłach klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP, opisane w dalszej części tego artykułu.

Aby dodać wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS, najpierw dodaj te wpisy rejestru Windows na obu węzłach klastra Windows SAP ASCS/SCS programu:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (Decimal) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** Zmień pierwszy parametr TCP/IP

Następnie dodaj ten wpis rejestru Windows na obu węzłach klastra Windows SAP ASCS/SCS programu:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (Decimal) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabela 4:** Zmień drugi parametr TCP/IP

Aby zastosować zmiany, należy ponownie uruchomić obu węzłów klastra.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS

Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS obejmuje następujące zadania:

- Zbieraj węzłów klastra w konfiguracji klastra.
- Skonfiguruj Monitor udziału plików klastra.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Zbieraj węzłów klastra w konfiguracji klastra

1. Dodawanie roli i funkcje kreatora Dodaj klaster do obu węzłów klastra trybu failover.
2. Konfigurowanie klastra trybu failover przy użyciu Menedżera klastra trybu Failover. W Menedżerze klastra trybu Failover wybierz **tworzenia klastrów**, a następnie dodaj tylko nazwę pierwszego klastra (węzeł A). Nie należy dodawać drugiego węzła jeszcze; dodaniu drugiego węzła w późniejszym kroku.

   ![Rysunek 8: Dodaj nazwę serwera lub maszyny wirtualnej w pierwszym węźle klastra][sap-ha-guide-figure-3007]

   _**Rysunek 8:** Dodaj nazwę serwera lub maszyny wirtualnej w pierwszym węźle klastra_

3. Wprowadź nazwę sieci (nazwa hosta wirtualnego) klastra.

   ![Rysunek 9: Wprowadź nazwę klastra][sap-ha-guide-figure-3008]

   _**Rysunek 9:** Wprowadź nazwę klastra_

4. Po utworzeniu klastra, uruchom test sprawdzania poprawności klastra.

   ![Rysunek 10. Uruchom sprawdzenie poprawności klastra][sap-ha-guide-figure-3009]

   _**Rysunek 10.** Uruchom sprawdzenie poprawności klastra_

   Możesz zignorować ostrzeżenia dotyczące dysków na tym etapie w procesie. Należy dodać monitora udostępniania plików i oprogramowanie SIOS udostępnionych dysków później. Na tym etapie nie trzeba martwić o kworum.

   ![Rysunek 11: Nie dysku kworum zostanie znaleziony.][sap-ha-guide-figure-3010]

   _**Rysunek 11:** Nie dysku kworum zostanie znaleziony._

   ![Ilustracja 12 Zasób klastra core wymaga nowego adresu IP][sap-ha-guide-figure-3011]

   _**Ilustracja 12** Zasób klastra core wymaga nowego adresu IP_

5. Zmień adres IP podstawowej usługi klastra. Klaster nie można uruchomić, dopóki nie zmieni adres IP podstawowej usługi klastra, ponieważ adres IP serwera wskazuje na jednym z węzłów maszyny wirtualnej. To zrobić na **właściwości** strony podstawowej usługi klastrowania zasób adresu IP.

   Na przykład należy przypisać adres IP (w tym przykładzie 10.0.0.42) dla klastra hostów wirtualnych nazwa pr1-ascs-vir.

   ![Ilustracja 13 W oknie dialogowym właściwości zmiana adresu IP][sap-ha-guide-figure-3012]

   _**Ilustracja 13** W **właściwości** okna dialogowego pola, zmiana adresu IP_

   ![Ilustracja 14 Przypisanie adresu IP, która jest zarezerwowana dla klastra][sap-ha-guide-figure-3013]

   _**Ilustracja 14** Przypisanie adresu IP, która jest zarezerwowana dla klastra_

6. Przenieś nazwę hosta wirtualnego klastra online.

   ![Ilustracja 15 Podstawowa usługa klastrowania jest uruchomiona, prawidłowy adres IP][sap-ha-guide-figure-3014]

   _**Ilustracja 15** Podstawowa usługa klastrowania jest uruchomiona, prawidłowy adres IP_

7. Dodaj drugi węzeł klastra.

   Teraz, że podstawowa usługa klastrowania jest uruchomiona, można dodać drugi węzeł klastra.

   ![Rysunek 16 Dodawanie drugiego węzła klastra][sap-ha-guide-figure-3015]

   _**Ilustracja 16** Dodaj drugi węzeł klastra_

8. Wprowadź nazwę dla drugiego hosta węzła klastra.

   ![Ilustracja 17 Wprowadź nazwę hosta drugiego węzła klastra][sap-ha-guide-figure-3016]

   _**Ilustracja 17** Wprowadź nazwę hosta drugiego węzła klastra_

   > [!IMPORTANT]
   > Upewnij się, że **Dodaj wszystkie odpowiednie magazyny do klastra** pole wyboru jest *nie* wybrane.  
   >
   >

   ![Ilustracja 18 Nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

   _**Ilustracja 18** Czy *nie* zaznacz pole wyboru_

   Możesz zignorować ostrzeżenia dotyczące kworum i dysków. Można ustawić kworum i później współużytkować dysku, zgodnie z opisem w [zainstalować oprogramowanie SIOS DataKeeper Cluster Edition dla dysku udział klastra SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Ilustracja 19 Ignoruj ostrzeżenia dotyczące dysku kworum][sap-ha-guide-figure-3018]

   _**Ilustracja 19** Ignoruj ostrzeżenia dotyczące dysku kworum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurowanie monitora udziału plików klastra

Konfigurowanie monitora udostępniania plików klastra obejmuje następujące zadania:

- Utwórz udział plików.
- Ustaw kworum monitora udziału plików w Menedżerze klastra trybu Failover.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Utwórz udział plików

1. Wybierz monitor udziału plików, zamiast dysku kworum. Oprogramowanie SIOS DataKeeper obsługuje tę opcję.

   W przykładach w niniejszym artykule monitora udziału plików jest na serwerze DNS lub usługi Active Directory, który działa w systemie Azure. Monitor udziału plików jest nazywany domcontr 0. Czy skonfigurować połączenia sieci VPN na platformie Azure (za pośrednictwem bramy sieci VPN lub usługi Azure ExpressRoute), usługi DNS lub usługi Active Directory działa lokalnie i nie jest odpowiednia do uruchamiania Monitora udziału plików.

   > [!NOTE]
   > Jeśli usługa DNS lub usługi Active Directory działa tylko w środowisku lokalnym, nie należy konfigurować usługi monitora udziału plików w systemie operacyjnym Windows DNS lub usługi Active Directory, która jest uruchomiona w środowisku lokalnym. Opóźnienie sieci między węzłami klastra uruchomione na platformie Azure i usługi Active Directory lub DNS w środowisku lokalnym może być za duży i spowodować problemy z połączeniem. Pamiętaj skonfigurować monitor udostępniania plików na maszynie wirtualnej platformy Azure, które jest uruchamiane w pobliżu węzła klastra.  
   >
   >

   Dysku kworum wymaga przynajmniej 1024 MB wolnego miejsca na dysku. Zaleca się 2048 MB wolnego miejsca na dysku kworum.

2. Dodaj obiekt nazwy klastra.

   ![Ilustracja 20 Przypisz uprawnienia w udziale obiektu nazwy klastra][sap-ha-guide-figure-3019]

   _**Ilustracja 20** Przypisz uprawnienia w udziale obiektu nazwy klastra_

   Pamiętaj, że uprawnienia obejmują uprawnień do zmiany danych w udziale dla obiektu nazwy klastra (w tym przykładzie pr1-ascs-vir$).

3. Aby dodać obiekt nazwy klastra, do listy, wybierz **Dodaj**. Zmień filtr, aby wyszukać obiekty komputerów, oprócz tych pokazano na rysunku 22.

   ![Ilustracja 21 Zmień typy obiektów, aby uwzględnić komputery][sap-ha-guide-figure-3020]

   _**Ilustracja 21** Zmiana **wybierane** włączania komputerów_

   ![Ilustracja 22 Zaznacz pole wyboru komputerów][sap-ha-guide-figure-3021]

   _**Ilustracja 22** Wybierz **komputerów** pola wyboru_

4. Wprowadź obiekt nazwy klastra, jak pokazano na rysunku 21. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 20.

5. Wybierz **zabezpieczeń** na karcie udziału, a następnie ustaw bardziej szczegółowe uprawnienia dla obiektu nazwy klastra.

   ![Ilustracja 23: Ustaw atrybuty zabezpieczeń dla obiektu nazwy klastra w kworum udziału plików][sap-ha-guide-figure-3022]

   _**Ilustracja 23:** Ustaw atrybuty zabezpieczeń dla obiektu nazwy klastra w kworum udziału plików_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ustaw kworum monitora udziału plików w Menedżerze klastra trybu Failover

1. Otwórz kworum ustawienia kreatora konfiguracji.

   ![Rysunek 24: Uruchamianie Kreatora ustawienie Konfigurowanie kworum klastra][sap-ha-guide-figure-3023]

   _**Rysunek 24:** Uruchamianie Kreatora ustawienie Konfigurowanie kworum klastra_

2. Na **Wybieranie opcji konfiguracji kworum** wybierz **Wybieranie monitora kworum**.

   ![Rysunek 25: Konfiguracji kworum, w których mogą wybierać][sap-ha-guide-figure-3024]

   _**Rysunek 25:** Konfiguracji kworum, w których mogą wybierać_

3. Na **Wybieranie monitora kworum** wybierz **Konfigurowanie monitora udziału plików**.

   ![Ilustracja 26 Wybierz monitor udziału plików][sap-ha-guide-figure-3025]

   _**Ilustracja 26** Wybierz monitor udziału plików_

4. Wprowadź ścieżkę UNC do udziału plików (w naszym przykładzie \\domcontr 0\FSW). Aby wyświetlić listę wprowadzania zmian, wybierz **dalej**.

   ![Ilustracja 27 Zdefiniuj lokalizację udziału plików monitora udziału][sap-ha-guide-figure-3026]

   _**Ilustracja 27** Zdefiniuj lokalizację udziału plików monitora udziału_

5. Wybierz zmiany, a następnie wybierz **dalej**. Należy pomyślnie ponownie skonfigurować konfigurację klastra, jak pokazano na rysunku 28:  

   ![Ilustracja 28 Potwierdzenie, że został ponownie skonfigurowany klaster][sap-ha-guide-figure-3027]

   _**Ilustracja 28** Potwierdzenie, że został ponownie skonfigurowany klaster_

Po zainstalowaniu klastra pracy awaryjnej Windows, należy zmienić niektóre wartości progowe, więc ich dostosowania trybu failover wykrywania warunków na platformie Azure. Parametry, które mają być zmienione są udokumentowane w artykule [dostrajania progów sieci klastra trybu failover][tuning-failover-cluster-network-thresholds]. Przy założeniu, że dwie maszyny wirtualne, które tworzą Windows konfigurację klastra ASCS/SCS znajdują się w tej samej podsieci, Zmień te wartości następujących parametrów:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Te ustawienia zostały przetestowane z klientami i oferuje dobry kompromis. Są odporne na błędy wystarczająco, ale również zapewniają trybu failover, który jest wystarczająco szybko, w warunkach rzeczywistych błędu, od oprogramowania SAP lub w węźle lub awarii maszyny Wirtualnej.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Zainstaluj oprogramowanie SIOS DataKeeper Cluster Edition dla udziału dysk klastrowy SAP ASCS/SCS

Masz teraz działającą konfiguracji klastra trybu failover systemu Windows Server na platformie Azure. Aby zainstalować wystąpienie SAP ASCS/SCS, konieczne jest zasób udostępniony dysk. Nie można utworzyć współużytkowane zasoby dyskowe potrzebne na platformie Azure. Oprogramowanie SIOS DataKeeper Cluster Edition to rozwiązanie innej firmy, które umożliwia tworzenie współużytkowane zasoby dyskowe.

Instalowanie oprogramowanie SIOS DataKeeper Cluster Edition dla udziału dysk klastrowy SAP ASCS/SCS obejmuje następujące zadania:

- Dodaj program Microsoft .NET Framework 3.5.
- Zainstaluj oprogramowanie SIOS DataKeeper.
- Skonfiguruj oprogramowanie SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Dodaj .NET Framework 3.5
.NET framework 3.5 nie jest automatycznie aktywowane lub zainstalowane w systemie Windows Server 2012 R2. Ponieważ oprogramowanie SIOS DataKeeper wymaga się we wszystkich węzłach, na którym jest instalowany DataKeeper języka .NET, należy zainstalować program .NET Framework 3.5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania .NET Framework 3.5:

- Użyj funkcji Kreatora dodawania ról i w Windows, jak pokazano na rysunku 29:

  ![Ilustracja 29 Zainstaluj program .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora][sap-ha-guide-figure-3028]

  _**Ilustracja 29** Zainstaluj program .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora_

  ![Ilustracja 30 Postęp instalacji pasek po zainstalowaniu programu .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora][sap-ha-guide-figure-3029]

  _**Ilustracja 30** Postęp instalacji pasek po zainstalowaniu programu .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora_

- Użyj narzędzia wiersza polecenia dism.exe. Ten typ instalacji musisz uzyskać dostęp do katalogu SxS, na nośniku instalacyjnym programu Windows. Wprowadź następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Zainstaluj oprogramowanie SIOS DataKeeper

Zainstaluj oprogramowanie SIOS DataKeeper Cluster Edition w każdym węźle w klastrze. Aby utworzyć wirtualny magazynu udostępnionego z oprogramowanie SIOS DataKeeper, utworzyć duplikat zsynchronizowane, a następnie symulować magazyn udostępniony klastra.

Przed zainstalowaniem oprogramowania oprogramowanie SIOS, należy utworzyć DataKeeperSvc użytkownika domeny.

> [!NOTE]
> Dodaj DataKeeperSvc użytkownika domeny do grupy administratorów lokalnych na obu węzłach klastra.
>
>

Aby zainstalować oprogramowanie SIOS DataKeeper:

1. Zainstaluj oprogramowanie oprogramowanie SIOS na obu węzłach klastra.

   ![Oprogramowanie SIOS Instalatora][sap-ha-guide-figure-3030]

   ![Ilustracja 31 Pierwsza strona instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Ilustracja 31** Pierwsza strona instalacji oprogramowanie SIOS DataKeeper_

2. W oknie dialogowym wybierz **tak**.

   ![Ilustracja 32 DataKeeper informuje, że usługa zostanie wyłączona][sap-ha-guide-figure-3032]

   _**Ilustracja 32** DataKeeper informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym zaleca się, że wybrano **konta domeny lub serwer**.

   ![Ilustracja 33 Oprogramowanie SIOS DataKeeper wybór użytkownika][sap-ha-guide-figure-3033]

   _**Ilustracja 33** Oprogramowanie SIOS DataKeeper wybór użytkownika_

4. Wprowadź nazwę użytkownika konta domeny i hasła, który został utworzony dla oprogramowanie SIOS DataKeeper.

   ![Ilustracja 34 Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Ilustracja 34** Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper_

5. Zainstaluj klucz licencji dla swojego wystąpienia oprogramowanie SIOS DataKeeper, jak pokazano na rysunku 35.

   ![Ilustracja 35 Podaj klucz licencji na oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Ilustracja 35** Podaj klucz licencji na oprogramowanie SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Skonfiguruj oprogramowanie SIOS DataKeeper

Po zainstalowaniu oprogramowanie SIOS DataKeeper na obu węzłach uruchomić konfigurację. Celem konfiguracji jest synchronicznej replikacji danych między dodatkowych dysków, które są dołączone do wszystkich maszyn wirtualnych.

1. Uruchom narzędzie DataKeeper zarządzania i konfiguracji, a następnie wybierz **Połącz serwerem**.

   ![Ilustracja 36 Oprogramowanie SIOS DataKeeper zarządzania i konfiguracji narzędzia][sap-ha-guide-figure-3036]

   _**Ilustracja 36** Oprogramowanie SIOS DataKeeper zarządzania i konfiguracji narzędzia_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, w których narzędzie zarządzania i konfiguracji należy połączenia oraz, w drugim kroku drugiego węzła.

   ![Ilustracja 37 Wstaw nazwę lub adres TCP/IP pierwszego węzła zarządzania i narzędzia do konfiguracji należy łączyć, a w drugim kroku drugiego węzła][sap-ha-guide-figure-3037]

   _**Ilustracja 37** Wstaw nazwę lub adres TCP/IP pierwszego węzła zarządzania i narzędzia do konfiguracji należy łączyć, a w drugim kroku drugiego węzła_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Ilustracja 38 Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _**Ilustracja 38** Tworzenie zadania replikacji_

   Kreator prowadzi przez proces tworzenia zadania replikacji.

4. Zdefiniuj nazwę zadania replikacji.

   ![Ilustracja 39 Zdefiniuj nazwę zadania replikacji][sap-ha-guide-figure-3039]

   _**Ilustracja 39** Zdefiniuj nazwę zadania replikacji_

   ![Ilustracja 40 Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącego węzła źródłowego][sap-ha-guide-figure-3040]

   _**Ilustracja 40** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącego węzła źródłowego_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Ilustracja 41 Zdefiniuj nazwę, adres TCP/IP i wolumin dysku bieżącego węzła docelowego][sap-ha-guide-figure-3041]

   _**Ilustracja 41** Zdefiniuj nazwę, adres TCP/IP i wolumin dysku bieżącego węzła docelowego_

6. Zdefiniuj algorytmy kompresji. W tym przykładzie firma Microsoft zaleca kompresowania strumienia replikacji. Szczególnie w sytuacjach, podczas ponownej synchronizacji Kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Kompresja używa zasobów procesora CPU i pamięci RAM maszyny wirtualnej. W miarę zwiększania szybkości kompresji, co powoduje ilości zasobów procesora CPU, które są używane. Można dostosować to ustawienie, później.

7. Inne ustawienie, które należy sprawdzić to, czy replikacja jest wykonywana asynchronicznie lub synchronicznie. W przypadku ochrony konfiguracji SAP ASCS/SCS, należy użyć replikacji synchronicznej.  

   ![Ilustracja 42 Zdefiniuj szczegóły replikacji][sap-ha-guide-figure-3042]

   _**Ilustracja 42** Zdefiniuj szczegóły replikacji_

8. Zdefiniuj, czy wolumin, który jest replikowany za zadanie replikacji powinny być przedstawione do konfiguracji klastra trybu failover w systemie Windows Server jako udostępniony dysk. W przypadku konfiguracji SAP ASCS/SCS wybierz **tak** tak, aby klastra Windows uznaje replikowanego woluminu dysku udostępnionego, który można użyć jako woluminu klastra.

   ![Ilustracja 43 Wybierz pozycję Tak, aby ustawić replikowanego woluminu jako woluminu klastra][sap-ha-guide-figure-3043]

   _**Ilustracja 43** Wybierz **tak** można ustawić replikowanego woluminu jako woluminu klastra_

   Po jego utworzeniu, narzędzie DataKeeper zarządzania i konfiguracji pokazuje, że zadanie replikacji jest aktywne.

   ![Ilustracja 44 DataKeeper synchronicznego duplikowania dysku udziału SAP ASCS/SCS jest aktywny][sap-ha-guide-figure-3044]

   _**Ilustracja 44** DataKeeper synchronicznego duplikowania dysku udziału SAP ASCS/SCS jest aktywny_

   Menedżer klastra trybu failover zawiera obecnie dysku jako dysku DataKeeper, jak pokazano na rysunku 45:

   ![Ilustracja 45 Menedżer klastra trybu failover zawiera dysk czy DataKeeper zreplikowane][sap-ha-guide-figure-3045]

   _**Ilustracja 45** Menedżer klastra trybu failover zawiera dysk czy DataKeeper zreplikowane_

## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj oprogramowanie SAP NetWeaver wysokiej dostępności przy użyciu klastra pracy awaryjnej Windows i udostępnionego dysku dla wystąpienia usługi SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
