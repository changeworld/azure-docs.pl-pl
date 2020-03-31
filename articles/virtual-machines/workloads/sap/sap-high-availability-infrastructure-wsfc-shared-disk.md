---
title: Infrastruktura platformy Azure dla SAP ASCS/SCS z dyskiem współdzielonym WSFC&| Dokumenty firmy Microsoft
description: Dowiedz się, jak przygotować infrastrukturę platformy Azure dla środowiska SAP HA przy użyciu klastra trybu failover systemu Windows i udostępnionego dysku dla wystąpienia SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a49bc979923bf52d099e30615910c5bdb0601b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279860"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Przygotowanie infrastruktury platformy Azure dla usługi SAP HA przy użyciu klastra trybu failover systemu Windows i udostępnionego dysku dla SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności sap multi-SID)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

W tym artykule opisano kroki, które należy wykonać w celu przygotowania infrastruktury platformy Azure do instalowania i konfigurowania systemu SAP o wysokiej dostępności w klastrze trybu failover systemu Windows przy użyciu *udostępnionego dysku klastra* jako opcji klastrowania wystąpienia SAP ASCS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji zapoznaj się z tym artykułem:

* [Przewodnik po architekturze: klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowanie infrastruktury do szablonu architektonicznego 1
Szablony usługi Azure Resource Manager dla systemu SAP ułatwiają wdrażanie wymaganych zasobów.

Trójwarstwowe szablony w usłudze Azure Resource Manager obsługują również scenariusze wysokiej dostępności. Na przykład szablon architektury 1 ma dwa klastry. Każdy klaster jest pojedynczym punktem awarii SAP dla SAP ASCS/SCS i DBMS.

Oto, gdzie można uzyskać szablony usługi Azure Resource Manager dla przykładowego scenariusza, który opisujemy w tym artykule:

* [Obraz portalu Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obraz portalu Azure Marketplace przy użyciu dysków zarządzanych platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Obraz niestandardowy przy użyciu dysków zarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Aby przygotować infrastrukturę dla szablonu architektonicznego 1:

- W witrynie Azure portal w okienku **Parametry** w polu **DOSTĘPNOŚĆ SYSTEMU** wybierz pozycję **HA**.

  ![Rysunek 1: Ustawianie parametrów usługi Azure Resource Manager o wysokiej dostępności SAP][sap-ha-guide-figure-3000]

_**Rysunek 1:** Ustawianie parametrów usługi Azure Resource Manager o wysokiej dostępności sap_


  Szablony tworzą:

  * **Maszyny wirtualne:**
    * Maszyny wirtualne serwera \<aplikacji SAP: SAPSystemSID\>-di-\<Number\>
    * Maszyny wirtualne klastra ASCS/SCS: \<SAPSystemSID\>-ascs-\<Liczba\>
    * Klaster DBMS: \<SAPSystemSID\>\<-db- Numer\>

  * **Karty sieciowe dla wszystkich maszyn wirtualnych ze skojarzonymi adresami IP:**
    * \<SAPSystemSID\>-nic-di-\<Liczba\>
    * \<SAPSystemSID\>-nic-ascs-\<Liczba\>
    * \<SAPSystemSID\>-nic-db-\<Liczba\>

  * **Konta magazynu platformy Azure (tylko dyski niezarządzane):**

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera \<aplikacji SAP: SAPSystemSID\>-avset-di
    * Maszyny wirtualne klastra SAP \<ASCS/SCS: SAPSystemSID\>-avset-ascs
    * Maszyny wirtualne klastra \<DBMS: SAPSystemSID\>-avset-db

  * **Wewnętrzny moduł równoważenia obciążenia platformy Azure:**
    * Ze wszystkimi portami wystąpienia ASCS/SCS i \<\>adresu IP SAPSystemSID -lb-ascs
    * Ze wszystkimi portami dla programu SQL \<Server DBMS i adres IP SAPSystemSID\>-lb-db

  * **Grupa zabezpieczeń** \<sieci :\>SAPSystemSID -nsg-ascs-0  
    * Z otwartym zewnętrznym portem protokołu RDP \<(Remote Desktop\>Protocol) na maszynie wirtualnej SAPSystemSID -ascs-0

> [!NOTE]
> Wszystkie adresy IP kart sieciowych i wewnętrznych modułów równoważenia obciążenia platformy Azure są domyślnie dynamiczne. Zmień je na statyczne adresy IP. Opisujemy, jak to zrobić w dalszej części artykułu.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Wdrażanie maszyn wirtualnych z łącznością sieciową firmy (między lokalizacjami) do użycia w środowisku produkcyjnym
W przypadku systemów SAP produkcyjnych wdrażaj maszyny wirtualne platformy Azure z łącznością sieciową firmy przy użyciu bramy sieci VPN platformy Azure lub usługi Azure ExpressRoute.

> [!NOTE]
> Można użyć wystąpienia sieci wirtualnej platformy Azure. Sieć wirtualna i podsieć zostały już utworzone i przygotowane.
>
>

1. W witrynie Azure portal w okienku **Parametry** w polu **NEWOREXISTINGSUBNET** wybierz **istniejący**.
2. W polu **PODSIECI** dodaj pełny ciąg identyfikatora przygotowanej podsieci sieci platformy Azure, w którym planujesz wdrożyć maszyny wirtualne platformy Azure.
3. Aby uzyskać listę wszystkich podsieci sieci platformy Azure, uruchom to polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Pole **Identyfikator** zawiera wartość identyfikatora podsieci.
4. Aby uzyskać listę wszystkich wartości identyfikatora podsieci, uruchom to polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Identyfikator podsieci wygląda następująco:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Wdrażanie wystąpień SAP tylko w chmurze do testowania i demonstracji
System SAP o wysokiej dostępności można wdrożyć w modelu wdrażania tylko w chmurze. Ten rodzaj wdrożenia jest przede wszystkim przydatne w przypadku użycia demo i test. Nie nadaje się do zastosowań produkcyjnych.

- W witrynie Azure portal w okienku **Parametry** w polu **NEWOREXISTINGSUBNET** wybierz **nowy**. Pozostaw pole **PODSIEĆ** puste.

  Szablon usługi SAP Azure Resource Manager automatycznie tworzy sieć wirtualną platformy Azure i podsieć.

> [!NOTE]
> Należy również wdrożyć co najmniej jedną dedykowaną maszynę wirtualną dla usługi Active Directory i usługi DNS w tym samym wystąpieniu sieci wirtualnej platformy Azure. Szablon nie tworzy tych maszyn wirtualnych.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowanie infrastruktury do szablonu architektonicznego 2

Można użyć tego szablonu usługi Azure Resource Manager dla sap, aby uprościć wdrażanie wymaganych zasobów infrastruktury dla szablonu architektury SAP 2.

Oto, gdzie można uzyskać szablony usługi Azure Resource Manager dla tego scenariusza wdrażania:

* [Obraz portalu Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obraz portalu Azure Marketplace przy użyciu dysków zarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Obraz niestandardowy przy użyciu dysków zarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowanie infrastruktury do szablonu architektonicznego 3

Można przygotować infrastrukturę i skonfigurować sap dla wielu identyfikatorów SID. Na przykład można dodać dodatkowe wystąpienie SAP ASCS/SCS do *istniejącej* konfiguracji klastra. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dodatkowego wystąpienia SAP ASCS/SCS dla istniejącej konfiguracji klastra w celu utworzenia konfiguracji sap wieloustawowych w usłudze Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster wieloustawowy, możesz użyć szablonów szybkiego startu z wieloma identyfikatorami SID [w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates).

Aby utworzyć nowy klaster wieloustawowy, należy wdrożyć następujące trzy szablony:

* [Szablon ASCS/SCS](#ASCS-SCS-template)
* [Szablon bazy danych](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje zawierają więcej szczegółów na temat szablonów i parametrów, które należy podać w szablonach.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Szablon ASCS/SCS

Szablon ASCS/SCS wdraża dwie maszyny wirtualne, których można użyć do utworzenia klastra trybu failover systemu Windows Server, który obsługuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon wielosuwowy ASCS/SCS, w [szablonie wielouchantowym ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] lub [szablonie wielosuwowym ASCS/SCS przy użyciu dysków zarządzanych][sap-templates-3-tier-multisid-xscs-marketplace-image-md]wprowadź wartości następujących parametrów:

- **Prefiks zasobu:** Ustaw prefiks zasobu, który jest używany do prefiksu wszystkich zasobów, które są tworzone podczas wdrażania. Ponieważ zasoby nie należą tylko do jednego systemu SAP, prefiks zasobu nie jest identyfikatorem SID jednego systemu SAP.  Prefiks musi zawierać od trzech do sześciu znaków.
- **Typ stosu:** Wybierz typ stosu systemu SAP. W zależności od typu stosu moduł równoważenia obciążenia platformy Azure ma jeden (tylko ABAP lub Java) lub dwa (ABAP + Java) prywatne adresy IP na system SAP.
- **Typ systemu operacyjnego:** Wybierz system operacyjny maszyn wirtualnych.
- **Liczba systemów SAP:** Wybierz liczbę systemów SAP, które chcesz zainstalować w tym klastrze.
- **Dostępność systemu**: Wybierz **HA**.
- **Nazwa użytkownika administratora i hasło administratora:** Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
- **Nowa lub istniejąca podsieć**: Ustaw, czy utworzyć nową sieć wirtualną i podsieć, czy użyć istniejącej podsieci. Jeśli masz już sieć wirtualną połączoną z siecią lokalną, wybierz **istniejący**program .
- **Identyfikator**podsieci: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, do której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda następująco:

  /subscriptions/\<subscription\>id\</resourceGroups/\>resource group name /providers/Microsoft.Network/virtualNetworks/virtual\<network name\>/subnets/ subnets/\<nazwa podsieci\>

Szablon wdraża jedno wystąpienie modułu równoważenia obciążenia platformy Azure, które obsługuje wiele systemów SAP:

- Wystąpienia ASCS są skonfigurowane dla numer 00, 10, 20...
- Wystąpienia SCS są skonfigurowane dla numer 01, 11, 21...
- Wystąpienia serwera replikacji formatu ENCS Enqueue (ERS) (tylko w systemie Linux) są skonfigurowane na przykład numer 02, 12, 22...
- Wystąpienia SCS ERS (tylko w systemie Linux) są skonfigurowane na przykład numer 03, 13, 23...

Moduł równoważenia obciążenia zawiera 1 adres VIP (2 dla linuksa), 1x VIP dla ASCS/SCS i 1x VIP dla ERS (tylko linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Porty SAP ASCS/SCS
Poniższa lista zawiera wszystkie reguły równoważenia obciążenia (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Porty specyficzne dla systemu Windows dla każdego systemu SAP: 445, 5985
- Porty ASCS (numer instancji x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (numer instancji x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porty ASCS ERS w systemie Linux (numer instancji x2): 33x2, 5x213, 5x214, 5x216
- Porty SCS ERS w systemie Linux (numer instancji x3): 33x3, 5x313, 5x314, 5x316

Moduł równoważenia obciążenia jest skonfigurowany do używania następujących portów sondy (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Wewnętrzny port sondy modułu równoważenia obciążenia ASCS/SCS: 620x0
- Wewnętrzny port sondy modułu równoważenia obciążenia ERS (tylko Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Szablon bazy danych

Szablon bazy danych wdraża jedną lub dwie maszyny wirtualne, których można użyć do zainstalowania relacyjnego systemu zarządzania bazami danych (RDBMS) dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon multi-SID bazy danych, w [szablonie wielu identyfikatorów SID bazy danych][sap-templates-3-tier-multisid-db-marketplace-image] lub [szablonie multi-SID bazy danych przy użyciu dysków zarządzanych][sap-templates-3-tier-multisid-db-marketplace-image-md]wprowadź wartości dla następujących parametrów:

- **Sap System Id**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
- **Typ systemu operacyjnego:** Wybierz system operacyjny maszyn wirtualnych.
- **Typ bazy danych:** Wybierz typ bazy danych, którą chcesz zainstalować w klastrze. Wybierz **sql,** jeśli chcesz zainstalować program Microsoft SQL Server. Wybierz **HANA,** jeśli planujesz zainstalować SAP HANA na maszynach wirtualnych. Upewnij się, że wybrano poprawny typ systemu operacyjnego. Wybierz **pozycję Windows** dla języka SQL i wybierz dystrybucję systemu Linux dla hana. Moduł równoważenia obciążenia platformy Azure połączony z maszynami wirtualnymi jest skonfigurowany do obsługi wybranego typu bazy danych:
  * **SQL:** Port równoważenia obciążenia 1433 modułu równoważenia obciążenia. Upewnij się, że ten port jest używany do konfiguracji programu SQL Server AlwaysOn.
  * **HANA:** Porty równoważenia obciążenia równoważenia obciążenia 35015 i 35017. Upewnij się, że zainstalowano SAP HANA o numerze wystąpienia **50**.
  Moduł równoważenia obciążenia wykorzystuje port sondy 62550.
- **Sap Rozmiar systemu:** Ustaw liczbę SAPS nowy system zapewnia. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
- **Dostępność systemu**: Wybierz **HA**.
- **Nazwa użytkownika administratora i hasło administratora:** Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
- **Identyfikator podsieci:** Wprowadź identyfikator podsieci użytej podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony w ramach wdrożenia szablonu ASCS/SCS.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Szablon serwerów aplikacji

Szablon serwerów aplikacji wdraża co najmniej dwie maszyny wirtualne, które mogą być używane jako wystąpienia serwera aplikacji SAP dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon wielu identyfikatorów SID serwerów aplikacji, w [szablonie wielu identyfikatorów SID serwerów aplikacji][sap-templates-3-tier-multisid-apps-marketplace-image] lub [serwerach aplikacji przy użyciu dysków zarządzanych][sap-templates-3-tier-multisid-apps-marketplace-image-md]wprowadź wartości następujących parametrów:

  -  **Sap System Id**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
  -  **Typ systemu operacyjnego:** Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu SAP:** Liczba SAPS, które zapewnia nowy system. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
  -  **Dostępność systemu**: Wybierz **HA**.
  -  **Nazwa użytkownika administratora i hasło administratora:** Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
  -  **Identyfikator podsieci:** Wprowadź identyfikator podsieci użytej podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony w ramach wdrożenia szablonu ASCS/SCS.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Wirtualna sieć platformy Azure
W naszym przykładzie przestrzeń adresowa wystąpienia sieci wirtualnej platformy Azure jest 10.0.0.0/16. Istnieje jedna podsieć o nazwie Podsieć, z zakresem adresów 10.0.0.0/24. Wszystkie maszyny wirtualne i wewnętrzne moduły równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzać żadnych zmian w ustawieniach sieciowych wewnątrz systemu operacyjnego gościa. Obejmuje to adresy IP, serwery DNS i podsieć. Skonfiguruj wszystkie ustawienia sieciowe na platformie Azure. Usługa DHCP (Dynamic Host Configuration Protocol) propaguje ustawienia.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Adresy IP DNS

Aby ustawić wymagane adresy IP DNS, wykonaj następujące czynności:

1. W witrynie Azure portal w okienku **Serwery DNS** upewnij się, że opcja **serwerów DNS** w sieci wirtualnej jest ustawiona na **Niestandardowy system DNS**.
2. Wybierz ustawienia na podstawie typu posiadanej sieci. Więcej informacji zawierają następujące zasoby:
   * Dodaj adresy IP lokalnych serwerów DNS.  
   Lokalne serwery DNS można rozszerzyć na maszyny wirtualne uruchomione na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których uruchomisz usługę DNS.
   * W przypadku wdrożeń maszyn wirtualnych, które są izolowane na platformie Azure: Wdrażanie dodatkowej maszyny wirtualnej w tym samym wystąpieniu sieci wirtualnej, które służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure skonfigurowanych do uruchamiania usługi DNS.

   ![Rysunek 2: Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure][sap-ha-guide-figure-3001]

   _**Rysunek 2:** Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure_

   > [!NOTE]
   > Jeśli zmienisz adresy IP serwerów DNS, musisz ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować zmianę i propagować nowe serwery DNS.
   >
   >

W naszym przykładzie usługa DNS jest instalowana i konfigurowana na tych maszynach wirtualnych systemu Windows:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nazwy hostów i statyczne adresy IP dla wystąpienia klastrowanego SAP ASCS/SCS i wystąpienia klastrowanego DBMS

W przypadku wdrażania lokalnego potrzebne są te zastrzeżone nazwy hostów i adresy IP:

| Rola nazwy hosta wirtualnego | Nazwa hosta wirtualnego | Wirtualny statyczny adres IP |
| --- | --- | --- |
| Sap ASCS/SCS pierwsza nazwa hosta wirtualnego klastra (do zarządzania klastrem) |pr1-ascs-vir |10.0.0.42 |
| Nazwa wirtualnego hosta wystąpienia SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nazwa hosta wirtualnego drugiego klastra SAP DBMS (zarządzanie klastrem) |pr1-dbms-vir |10.0.0.32 |

Podczas tworzenia klastra należy utworzyć nazwy hostów wirtualnych pr1-ascs-vir i pr1-dbms-vir oraz skojarzone adresy IP, które zarządzają samym klastrem. Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [Zbieranie węzłów klastra w konfiguracji klastra][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Pozostałe dwie nazwy hostów wirtualnych, pr1-ascs-sap i pr1-dbms-sap oraz skojarzone adresy IP, można utworzyć na serwerze DNS. Klastrowane wystąpienie SAP ASCS/SCS i klastrowane wystąpienie DBMS używają tych zasobów. Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ustawianie statycznych adresów IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyn wirtualnych do użycia w klastrze należy ustawić statyczne adresy IP dla wszystkich maszyn wirtualnych. Zrób to w konfiguracji sieci wirtualnej platformy Azure, a nie w systemie operacyjnym gościa.

1. W witrynie Azure portal wybierz pozycję Adres**IP****Ustawienia** > **karty sieciowej** >  **grupy** > zasobów .
2. W okienku **Adresy IP** w obszarze **Przydział**wybierz pozycję **Statyczne**. W polu **Adres IP** wprowadź adres IP, którego chcesz użyć.

   > [!NOTE]
   > Jeśli zmienisz adres IP karty sieciowej, musisz ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować zmianę.  
   >
   >

   ![Rysunek 3: Ustawianie statycznych adresów IP dla karty sieciowej każdej maszyny wirtualnej][sap-ha-guide-figure-3002]

   _**Rysunek 3:** Ustawianie statycznych adresów IP dla karty sieciowej każdej maszyny wirtualnej_

   Powtórz ten krok dla wszystkich interfejsów sieciowych, czyli dla wszystkich maszyn wirtualnych, w tym maszyn wirtualnych, których chcesz użyć w usłudze Active Directory lub usłudze DNS.

W naszym przykładzie mamy te maszyny wirtualne i statyczne adresy IP:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwsze wystąpienie serwera aplikacji SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Drugie wystąpienie serwera aplikacji SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Ostatnie wystąpienie serwera aplikacji SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Pierwszy węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Drugi węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Pierwszy węzeł klastra dla wystąpienia usługi DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Drugi węzeł klastra dla wystąpienia usługi DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ustawianie statycznego adresu IP wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon usługi SAP Azure Resource Manager tworzy wewnętrzny moduł równoważenia obciążenia platformy Azure, który jest używany dla klastra wystąpień SAP ASCS/SCS i klastra DBMS.

> [!IMPORTANT]
> Adres IP nazwy hosta wirtualnego SAP ASCS/SCS jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia SAP ASCS/SCS: pr1-lb-ascs.
> Adres IP wirtualnej nazwy systemu dbms jest taki sam jak adres IP modułu równoważenia obciążenia wewnętrznego systemu DBMS: pr1-lb-dbms.
>
>

Aby ustawić statyczny adres IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure:

1. Początkowe wdrożenie ustawia adres IP wewnętrznego modułu równoważenia obciążenia na **dynamiczny**. W witrynie Azure portal w okienku **Adresy IP** w obszarze **Przydział**wybierz pozycję **Statyczne**.
2. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-ascs** na adres IP nazwy hosta wirtualnego wystąpienia SAP ASCS/SCS.
3. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-dbms** na adres IP nazwy hosta wirtualnego wystąpienia DBMS.

   ![Rysunek 4: Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Rysunek 4:** Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS_

W naszym przykładzie mamy dwa wewnętrzne moduły równoważenia obciążenia platformy Azure, które mają te statyczne adresy IP:

| Rola wewnętrznego modułu równoważenia obciążenia platformy Azure | Nazwa wewnętrznego modułu równoważenia obciążenia platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| Wewnętrzny moduł równoważenia obciążenia wystąpienia SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon sap Azure Resource Manager tworzy potrzebne porty:
* Wystąpienie ASCS aap z domyślnym numerem wystąpienia 00
* Wystąpienie Java SCS z domyślnym numerem wystąpienia 01

Podczas instalowania wystąpienia SAP ASCS/SCS należy użyć domyślnego numeru wystąpienia 00 dla wystąpienia ASCS ABAP i domyślnego numeru wystąpienia 01 dla wystąpienia java SCS.

Następnie należy utworzyć wymagane wewnętrzne punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver.

Aby utworzyć wymagane wewnętrzne punkty końcowe równoważenia obciążenia, najpierw należy utworzyć te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver ABAP ASCS:

| Nazwa reguły równoważenia usługi/obciążenia | Domyślne numery portów | Betonowe porty dla (wystąpienie ASCS z numerem wystąpienia 00) (ERS z 10) |
| --- | --- | --- |
| Serwer w kolejce / *lbrule3200* |32\<Numer instancji\> |3200 |
| Serwer wiadomości ABAP / *lbrule3600* |36\<Numer instancji\> |3600 |
| Wewnętrzny komunikat ABAP / *lbrule3900* |39\<Numer instancji\> |3900 |
| Serwer wiadomości HTTP / *Lbrule8100* |81\<Numer instancji\> |8100 |
| Sap uruchomić usługę ASCS HTTP / *Lbrule50013* |5\<Numer instancji\>13 |50013 |
| Sap uruchomić usługę ASCS HTTPS / *Lbrule50014* |5\<Numer instancji\>14 |50014 |
| Replikacja w kolejce / *Lbrule50016* |5\<Numer instancji\>16 |50016 |
| Sap uruchomić usługę ERS HTTP *Lbrule51013* |5\<Numer instancji\>13 |51013 |
| Sap uruchomić usługę ERS HTTP *Lbrule51014* |5\<Numer instancji\>14 |51014 |
| Zdalne zarządzanie systemem Windows (WinRM) *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

**Tabela 1:** Numery portów wystąpień SAP NetWeaver ABAP ASCS

Następnie należy utworzyć te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver Java SCS:

| Nazwa reguły równoważenia usługi/obciążenia | Domyślne numery portów | Betonowe porty dla (wystąpienie SCS o numerze wystąpienia 01) (ERS z 11) |
| --- | --- | --- |
| Serwer enqueue / *lbrule3201* |32\<Numer instancji\> |3201 |
| Serwer bramy / *lbrule3301* |33\<Numer instancji\> |3301 |
| Serwer komunikatów Java / *lbrule3900* |39\<Numer instancji\> |3901 |
| Serwer wiadomości HTTP / *Lbrule8101* |81\<Numer instancji\> |8101 |
| Usługa uruchamiania SAP SCS HTTP / *Lbrule50113* |5\<Numer instancji\>13 |50113 |
| Usługa uruchamiania SAP SCS HTTPS / *Lbrule50114* |5\<Numer instancji\>14 |50114 |
| Replikacja w kolejce / *Lbrule50116* |5\<Numer instancji\>16 |50116 |
| Sap uruchomić usługę ERS HTTP *Lbrule51113* |5\<Numer instancji\>13 |51113 |
| Sap uruchomić usługę ERS HTTP *Lbrule51114* |5\<Numer instancji\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

**Tabela 2:** Numery portów wystąpień SAP NetWeaver Java SCS

![Rysunek 5: Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3004]

_**Rysunek 5:** Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

Ustaw adres IP modułu równoważenia obciążenia pr1-lb-dbms na adres IP nazwy hosta wirtualnego wystąpienia dbms.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Jeśli chcesz użyć różnych numerów dla wystąpień SAP ASCS lub SCS, musisz zmienić nazwy i wartości ich portów z wartości domyślnych.

1. W witrynie Azure portal wybierz pozycję > **Load Balancing Rules** ** \<Reguły równoważenia obciążenia modułu SID\>-lb-ascs**.
2. W przypadku wszystkich reguł równoważenia obciążenia należących do wystąpienia SAP ASCS lub SCS zmień następujące wartości:

   * Nazwa
   * Port
   * Port zaplecza

   Na przykład, jeśli chcesz zmienić domyślny numer wystąpienia ASCS z 00 na 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

   Oto przykład aktualizacji dla portu *lbrule3200*.

   ![Rysunek 6: Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3005]

   _**Rysunek 6:** Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Dodawanie maszyn wirtualnych systemu Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych dodaj maszyny wirtualne do domeny.

![Rysunek 7: Dodawanie maszyny wirtualnej do domeny][sap-ha-guide-figure-3006]

_**Rysunek 7:** Dodawanie maszyny wirtualnej do domeny_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Dodawanie wpisów rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS

Moduł równoważenia obciążenia platformy Azure ma wewnętrzny moduł równoważenia obciążenia, który zamyka połączenia, gdy połączenia są bezczynne przez określony czas (limit czasu bezczynności). Procesy robocze SAP w wystąpieniach okien dialogowych otwierają połączenia z procesem enqueue SAP, gdy tylko pierwsze żądanie enqueue/dequeue musi zostać wysłane. Połączenia te zwykle pozostają nawiązane do czasu ponownego uruchomienia procesu pracy lub procesu w kolejce. Jednak jeśli połączenie jest bezczynne przez określony czas, wewnętrzny moduł równoważenia obciążenia platformy Azure zamyka połączenia. Nie stanowi to problemu, ponieważ proces pracy SAP przywraca połączenie z procesem enqueue, jeśli już nie istnieje. Te działania są udokumentowane w ślady dewelopera procesów SAP, ale tworzą dużą ilość dodatkowej zawartości w tych śladów. Warto zmienić protokół TCP/IP `KeepAliveTime` i `KeepAliveInterval` oba węzły klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP, opisanymi w dalszej części artykułu.

Aby dodać wpisy rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS, najpierw dodaj te wpisy rejestru systemu Windows w obu węzłach klastra systemu Windows dla SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (dziesiętne) |
| Wartość |120000 |
| Łącze do dokumentacji |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** Zmienianie pierwszego parametru TCP/IP

Następnie dodaj ten wpis rejestru systemu Windows w obu węzłach klastra systemu Windows dla SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (dziesiętne) |
| Wartość |120000 |
| Łącze do dokumentacji |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabela 4:** Zmienianie drugiego parametru TCP/IP

Aby zastosować zmiany, uruchom ponownie oba węzły klastra.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS

Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS obejmuje następujące zadania:

- Zbieranie węzłów klastra w konfiguracji klastra.
- Konfigurowanie monitora udziału plików klastra.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Zbieranie węzłów klastra w konfiguracji klastra

1. W Kreatorze dodawania roli i funkcji dodaj klastrowanie trybu failover do obu węzłów klastra.
2. Konfigurowanie klastra trybu failover przy użyciu Menedżera klastrów trybu failover. W Menedżerze klastrów trybu failover wybierz pozycję **Utwórz klaster**, a następnie dodaj tylko nazwę pierwszego klastra (węzeł A). Nie należy jeszcze dodawać drugiego węzła; dodać drugi węzeł w późniejszym kroku.

   ![Rysunek 8: Dodawanie nazwy serwera lub maszyny wirtualnej pierwszego węzła klastra][sap-ha-guide-figure-3007]

   _**Rysunek 8:** Dodawanie nazwy serwera lub maszyny wirtualnej pierwszego węzła klastra_

3. Wprowadź nazwę sieci (nazwę hosta wirtualnego) klastra.

   ![Rysunek 9: Wprowadź nazwę klastra][sap-ha-guide-figure-3008]

   _**Rysunek 9:** Wprowadź nazwę klastra_

4. Po utworzeniu klastra uruchom test sprawdzania poprawności klastra.

   ![Rysunek 10: Uruchom sprawdzanie poprawności klastra][sap-ha-guide-figure-3009]

   _**Rysunek 10:** Uruchamianie sprawdzania poprawności klastra_

   W tym momencie można zignorować wszelkie ostrzeżenia dotyczące dysków. Później dodasz monitor udziału plików i dyski udostępnione SIOS. Na tym etapie nie musisz się martwić o kworum.

   ![Rysunek 11: Nie znaleziono dysku kworum][sap-ha-guide-figure-3010]

   _**Wykres 11.** Nie znaleziono dysku kworum_

   ![Rysunek 12: Podstawowy zasób klastra potrzebuje nowego adresu IP][sap-ha-guide-figure-3011]

   _**Wykres 12.** Podstawowy zasób klastra potrzebuje nowego adresu IP_

5. Zmień adres IP podstawowej usługi klastrowania. Klaster nie może się uruchomić, dopóki nie zmienisz adresu IP podstawowej usługi klastrowania, ponieważ adres IP serwera wskazuje jeden z węzłów maszyny wirtualnej. W tym celu na stronie **Właściwości** zasobu IP podstawowej usługi klastrowania.

   Na przykład musimy przypisać adres IP (w naszym przykładzie 10.0.0.42) dla nazwy hosta wirtualnego klastra pr1-ascs-vir.

   ![Rysunek 13: W oknie dialogowym Właściwości zmień adres IP][sap-ha-guide-figure-3012]

   _**Wykres 13.** W oknie dialogowym **Właściwości** zmień adres IP_

   ![Rysunek 14: Przypisywanie adresu IP zarezerwowanego dla klastra][sap-ha-guide-figure-3013]

   _**Wykres 14.** Przypisywanie adresu IP zarezerwowanego dla klastra_

6. Przewiń nazwę hosta wirtualnego klastra do trybu online.

   ![Rysunek 15: Podstawowa usługa klastra jest uruchomiona, z poprawnym adresem IP][sap-ha-guide-figure-3014]

   _**Wykres 15:** Podstawowa usługa klastra jest uruchomiona z poprawnym adresem IP_

7. Dodaj drugi węzeł klastra.

   Teraz, gdy podstawowa usługa klastrowania jest uruchomiona, można dodać drugi węzeł klastra.

   ![Rysunek 16 Dodawanie drugiego węzła klastra][sap-ha-guide-figure-3015]

   _**Wykres 16.** Dodawanie drugiego węzła klastra_

8. Wprowadź nazwę drugiego hosta węzła klastra.

   ![Rysunek 17: Wprowadź nazwę hosta drugiego węzła klastra][sap-ha-guide-figure-3016]

   _**Wykres 17:** Wprowadź nazwę hosta drugiego węzła klastra_

   > [!IMPORTANT]
   > Upewnij się, że pole wyboru **Dodaj wszystkie kwalifikujące się miejsca do magazynu do klastra** *nie* jest zaznaczone.  
   >
   >

   ![Rysunek 18: Nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

   _**Wykres 18:** *Nie* zaznaczaj pola wyboru_

   Można zignorować ostrzeżenia dotyczące kworum i dysków. Możesz ustawić kworum i udostępnić dysk później, zgodnie z opisem w [Zainstaluj SIOS DataKeeper Cluster Edition dla dysku współużytkowania klastra SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Rysunek 19: Ignorowanie ostrzeżeń dotyczących kworum dysku][sap-ha-guide-figure-3018]

   _**Wykres 19.** Ignoruj ostrzeżenia dotyczące kworum dysku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurowanie monitora udziału plików klastra

Konfigurowanie monitora udziału plików klastra obejmuje następujące zadania:

- Tworzenie udziału plików.
- Ustawianie kworum świadka udziału plików w Menedżerze klastrów trybu failover.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Tworzenie udziału plików

1. Wybierz monitor udziału plików zamiast dysku kworum. SIOS DataKeeper obsługuje tę opcję.

   W przykładach w tym artykule monitor udziału plików znajduje się na serwerze usługi Active Directory lub DNS, który jest uruchomiony na platformie Azure. Monitor udziału plików jest nazywany domcontr-0. Ponieważ skonfigurowano połączenie sieci VPN z platformą Azure (za pośrednictwem bramy sieci VPN lub usługi Azure ExpressRoute), usługa Active Directory lub usługa DNS jest lokalna i nie jest odpowiednia do uruchamiania monitora udziału plików.

   > [!NOTE]
   > Jeśli usługa Active Directory lub USŁUGA DNS działa tylko lokalnie, nie konfiguruj monitora udziału plików w lokalnym systemie operacyjnym Active Directory lub DNS Windows. Opóźnienie sieci między węzłami klastra działającymi na platformie Azure i w usłudze Active Directory lub u lokalnego systemu DNS może być zbyt duże i powodować problemy z łącznością. Należy skonfigurować monitora udziału plików na maszynie wirtualnej platformy Azure, która działa w pobliżu węzła klastra.  
   >
   >

   Dysk kworum potrzebuje co najmniej 1024 MB wolnego miejsca. Zalecamy 2048 MB wolnego miejsca na dysk kworum.

2. Dodaj obiekt nazwy klastra.

   ![Rysunek 20: Przypisywanie uprawnień do udziału dla obiektu nazwy klastra][sap-ha-guide-figure-3019]

   _**Wykres 20.** Przypisywanie uprawnień do udziału dla obiektu nazwy klastra_

   Upewnij się, że uprawnienia obejmują uprawnienia do zmiany danych w udziale obiektu nazwy klastra (w naszym przykładzie pr1-ascs-vir$).

3. Aby dodać obiekt nazwy klastra do listy, wybierz pozycję **Dodaj**. Zmień filtr, aby sprawdzić, czy obiekty komputera, oprócz tych pokazanych na rysunku 22.

   ![Rysunek 21: Zmienianie typów obiektów w celu uwzględnienia komputerów][sap-ha-guide-figure-3020]

   _**Wykres 21.** Zmienianie **typów obiektów** w celu uwzględnienia komputerów_

   ![Rysunek 22: Zaznacz pole wyboru Komputery][sap-ha-guide-figure-3021]

   _**Wykres 22.** Zaznacz pole wyboru **Komputery**_

4. Wprowadź obiekt nazwy klastra, jak pokazano na rysunku 21. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 20.

5. Wybierz kartę **Zabezpieczenia** udziału, a następnie ustaw bardziej szczegółowe uprawnienia dla obiektu nazwy klastra.

   ![Rysunek 23: Ustawianie atrybutów zabezpieczeń obiektu nazwy klastra w kworum udziału plików][sap-ha-guide-figure-3022]

   _**Wykres 23.** Ustawianie atrybutów zabezpieczeń obiektu nazwy klastra w kworum udziału plików_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ustawianie kworum monitora udziału plików w Menedżerze klastrów trybu failover

1. Otwórz Kreatora konfigurowania ustawienia kworum.

   ![Rysunek 24: Uruchamianie Kreatora ustawiania konfigurowania kworum klastra][sap-ha-guide-figure-3023]

   _**Wykres 24.** Uruchamianie Kreatora konfigurowania ustawienia kworum klastra_

2. Na stronie **Wybierz opcję konfiguracji kworum** wybierz pozycję **Wybierz monitor kworum**.

   ![Rysunek 25: Konfiguracje kworum, które można wybrać][sap-ha-guide-figure-3024]

   _**Wykres 25.** Konfiguracje kworum, które można wybrać_

3. Na stronie **Wybierz monitor kworum** wybierz pozycję **Konfigurowanie monitora udziału plików**.

   ![Rysunek 26: Wybierz monitor udziału plików][sap-ha-guide-figure-3025]

   _**Wykres 26.** Wybieranie monitora udziału plików_

4. Wprowadź ścieżkę UNC do udziału plików \\(w naszym przykładzie domcontr-0\FSW). Aby wyświetlić listę zmian, które można wprowadzić, wybierz przycisk **Dalej**.

   ![Rysunek 27: Definiowanie lokalizacji udziału plików dla udziału świadka][sap-ha-guide-figure-3026]

   _**Wykres 27.** Definiowanie lokalizacji udziału plików dla udziału świadka_

5. Zaznacz żądane zmiany, a następnie wybierz pozycję **Dalej**. Należy pomyślnie skonfigurować konfigurację klastra zgodnie z rysunkiem 28:  

   ![Rysunek 28: Potwierdzenie ponownej konfiguracji klastra][sap-ha-guide-figure-3027]

   _**Wykres 28:** Potwierdzenie ponownej konfiguracji klastra_

Po pomyślnym zainstalowaniu klastra trybu failover systemu Windows należy zmienić niektóre progi, aby dostosować wykrywanie pracy awaryjnej do warunków na platformie Azure. Parametry, które mają zostać zmienione, są dokumentowane w [dostrajaniu progów sieci klastra trybu failover][tuning-failover-cluster-network-thresholds]. Zakładając, że dwie maszyny wirtualne, które tworzą konfigurację klastra systemu Windows dla ASCS/SCS znajdują się w tej samej podsieci, zmień następujące parametry na następujące wartości:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Te ustawienia zostały przetestowane z klientami i oferują dobry kompromis. Są wystarczająco odporne, ale zapewniają również przejście w błąd, które jest wystarczająco szybkie w rzeczywistych warunkach błędu w oprogramowaniu SAP lub w awarii węzła lub maszyny Wirtualnej.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalowanie programu SIOS DataKeeper Cluster Edition dla dysku współużytkowego klastra SAP ASCS/SCS

Masz teraz działającą konfigurację klastrowania trybu failover systemu Windows Server na platformie Azure. Aby zainstalować wystąpienie SAP ASCS/SCS, potrzebny jest zasób dysku udostępnionego. Nie można utworzyć zasobów dysku udostępnionego, których potrzebujesz na platformie Azure. SIOS DataKeeper Cluster Edition to rozwiązanie innej firmy, którego można używać do tworzenia zasobów dysku współdzielonego.

Instalowanie programu SIOS DataKeeper Cluster Edition dla dysku współużytkowego klastra SAP ASCS/SCS obejmuje następujące zadania:

- Dodaj program Microsoft .NET Framework 3.5.
- Zainstaluj SIOS DataKeeper.
- Skonfiguruj SIOS DataKeeper.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Dodaj platformę .NET Framework 3.5
Program .NET Framework 3.5 nie jest automatycznie aktywowany ani instalowany w systemie Windows Server 2012 R2. Ponieważ program SIOS DataKeeper wymaga, aby program .NET był znajdować się we wszystkich węzłach, w których instalujesz program DataKeeper, należy zainstalować program .NET Framework 3.5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania programu .NET Framework 3.5:

- Użyj Kreatora dodawania ról i funkcji w systemie Windows, jak pokazano na rysunku 29:

  ![Rysunek 29: Instalowanie programu .NET Framework 3.5 za pomocą Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3028]

  _**Wykres 29.** Instalowanie programu .NET Framework 3.5 za pomocą Kreatora dodawania ról i funkcji_

  ![Rysunek 30: Pasek postępu instalacji podczas instalowania programu .NET Framework 3.5 przy użyciu Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3029]

  _**Rysunek 30:** Pasek postępu instalacji podczas instalowania programu .NET Framework 3.5 przy użyciu Kreatora dodawania ról i funkcji_

- Użyj narzędzia wiersza polecenia dism.exe. W przypadku instalacji tego typu należy uzyskać dostęp do katalogu SxS na nośniku instalacyjnym systemu Windows. W wierszu polecenia z podwyższonym poziomem uprawnień wprowadź to polecenie:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalowanie programu SIOS DataKeeper

Zainstaluj SIOS DataKeeper Cluster Edition w każdym węźle w klastrze. Aby utworzyć wirtualny magazyn udostępniony za pomocą programu SIOS DataKeeper, utwórz zsynchronizowany plik dublowany, a następnie symuluj współdzielony magazyn klastra.

Przed zainstalowaniem oprogramowania SIOS utwórz użytkownika domeny DataKeeperSvc.

> [!NOTE]
> Dodaj użytkownika domeny DataKeeperSvc do grupy Administrator lokalny w obu węzłach klastra.
>
>

Aby zainstalować SIOS DataKeeper:

1. Zainstaluj oprogramowanie SIOS w obu węzłach klastra.

   ![Instalator SIOS][sap-ha-guide-figure-3030]

   ![Rysunek 31: Pierwsza strona instalacji SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Wykres 31:** Pierwsza strona instalacji SIOS DataKeeper_

2. W oknie dialogowym wybierz pozycję **Tak**.

   ![Rysunek 32: DataKeeper informuje, że usługa zostanie wyłączona][sap-ha-guide-figure-3032]

   _**Wykres 32.** DataKeeper informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym zaleca się wybranie **konta domeny lub serwera**.

   ![Rysunek 33: Wybór użytkownika dla SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Wykres 33.** Wybór użytkownika dla SIOS DataKeeper_

4. Wprowadź nazwę użytkownika konta domeny i hasło utworzone dla identyfikatora SIOS DataKeeper.

   ![Rysunek 34: Wprowadź nazwę użytkownika domeny i hasło do instalacji SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Wykres 34:** Wprowadź nazwę użytkownika domeny i hasło do instalacji SIOS DataKeeper_

5. Zainstaluj klucz licencyjny dla wystąpienia SIOS DataKeeper, jak pokazano na rysunku 35.

   ![Rysunek 35: Wprowadź klucz licencyjny SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Wykres 35:** Wprowadź klucz licencyjny SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Konfigurowanie sios datakeeper

Po zainstalowaniu SIOS DataKeeper w obu węzłach uruchom konfigurację. Celem konfiguracji jest synchroniczne replikacji danych między dodatkowych dysków, które są dołączone do każdej z maszyn wirtualnych.

1. Uruchom narzędzie Zarządzanie i konfiguracja menedżera danych, a następnie wybierz pozycję **Połącz serwer**.

   ![Rysunek 36: Narzędzie do zarządzania i konfiguracji danych SIOS][sap-ha-guide-figure-3036]

   _**Wykres 36.** Narzędzie SIOS DataKeeper Zarządzanie i konfiguracja_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, z który powinno się połączyć narzędzie Zarządzanie i konfiguracja, a w drugim kroku z drugim węzłem.

   ![Rysunek 37: Wstaw nazwę lub adres TCP/IP pierwszego węzła, z który powinno się połączyć narzędzie Zarządzanie i konfiguracja, a w drugim kroku drugi węzeł][sap-ha-guide-figure-3037]

   _**Wykres 37:** Wstaw nazwę lub adres TCP/IP pierwszego węzła, z który powinno się połączyć narzędzie Zarządzanie i konfiguracja, a w drugim kroku drugi węzeł_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Rysunek 38: Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _**Wykres 38:** Tworzenie zadania replikacji_

   Kreator prowadzi użytkownika przez proces tworzenia zadania replikacji.

4. Zdefiniuj nazwę zadania replikacji.

   ![Rysunek 39: Zdefiniuj nazwę zadania replikacji][sap-ha-guide-figure-3039]

   _**Wykres 39:** Definiowanie nazwy zadania replikacji_

   ![Rysunek 40: Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem źródłowym][sap-ha-guide-figure-3040]

   _**Rysunek 40:** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem źródłowym_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Rysunek 41: Definiowanie nazwy, adresu TCP/IP i woluminu dysku bieżącego węzła docelowego][sap-ha-guide-figure-3041]

   _**Wykres 41:** Definiowanie nazwy, adresu TCP/IP i woluminu dysku bieżącego węzła docelowego_

6. Zdefiniuj algorytmy kompresji. W naszym przykładzie zaleca się skompresowanie strumienia replikacji. Szczególnie w sytuacjach ponownej synchronizacji kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Kompresja używa zasobów procesora i pamięci RAM maszyny wirtualnej. Wraz ze wzrostem szybkości kompresji zwiększa się również ilość zasobów procesora CPU, które są używane. To ustawienie można dostosować później.

7. Innym ustawieniem, które należy sprawdzić, jest to, czy replikacja odbywa się asynchronicznie lub synchronicznie. Podczas ochrony konfiguracji SAP ASCS/SCS należy użyć replikacji synchroniczowej.  

   ![Rysunek 42: Definiowanie szczegółów replikacji][sap-ha-guide-figure-3042]

   _**Wykres 42.** Definiowanie szczegółów replikacji_

8. Zdefiniuj, czy wolumin replikowany przez zadanie replikacji powinien być reprezentowany w konfiguracji klastra trybu failover systemu Windows Server jako dysk udostępniony. W przypadku konfiguracji SAP ASCS/SCS wybierz opcję **Tak,** aby klaster systemu Windows postrzegał wolumin replikowany jako dysk udostępniony, którego może używać jako wolumin klastra.

   ![Rysunek 43: Wybierz tak, aby ustawić wolumin replikowany jako wolumin klastra][sap-ha-guide-figure-3043]

   _**Wykres 43.** Wybierz **tak,** aby ustawić wolumin replikowany jako wolumin klastra_

   Po utworzeniu woluminu narzędzie Zarządzanie i konfiguracja datakeeper pokazuje, że zadanie replikacji jest aktywne.

   ![Rysunek 44: Synchronizaczne dublowanie DataKeeper dla dysku współużytkującego SAP ASCS/SCS jest aktywne][sap-ha-guide-figure-3044]

   _**Wykres 44:** Aktywne jest synchroniczne dublowanie datakeeper dla dysku współużytkującego SAP ASCS/SCS_

   Menedżer klastrów trybu failover pokazuje teraz dysk jako dysk DataKeeper, jak pokazano na rysunku 45:

   ![Rysunek 45: Menedżer klastra trybu failover pokazuje dysk, który replikował program DataKeeper][sap-ha-guide-figure-3045]

   _**Wykres 45.** Menedżer klastrów trybu failover pokazuje dysk, który program DataKeeper został zreplikowany_

## <a name="next-steps"></a>Następne kroki

* [Instalowanie usługi SAP NetWeaver HA przy użyciu klastra trybu failover systemu Windows i udostępnionego dysku dla wystąpienia SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
