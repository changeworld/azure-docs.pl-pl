---
title: SAP ASCS/SCS multi-SID HA z WSFC&udostępnionego dysku na platformie Azure | Dokumenty firmy Microsoft
description: Wysoka dostępność wielu identyfikatorów SID dla wystąpienia SAP ASCS/SCS z klastrem trybu failover systemu Windows Server i dyskiem udostępnionym na platformie Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 446091263596a1fd5503f38c6a60316f9b0b6843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245176"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0f3ee255-b31e-4b8a-a95a-d9ed6200468b

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png



[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>Wysoka dostępność wystąpienia SAP ASCS/SCS z klastrem trybu failover systemu Windows Server i dyskiem współdzielonym na platformie Azure

> ![Windows][Logo_Windows] Windows
>

We wrześniu 2016 r. firma Microsoft wydała funkcję, w której można zarządzać wieloma wirtualnymi adresami IP przy użyciu [wewnętrznego modułu równoważenia obciążenia platformy Azure.][load-balancer-multivip-overview] Ta funkcja już istnieje w zewnętrznym modułem równoważenia obciążenia platformy Azure. 

Jeśli masz wdrożenie SAP, musisz użyć wewnętrznego modułu równoważenia obciążenia, aby utworzyć konfigurację klastra systemu Windows dla wystąpień SAP Central Services (ASCS/SCS).

W tym artykule skupiono się na tym, jak przenieść się z pojedynczej instalacji ASCS/SCS do konfiguracji wielu identyfikatorów SAP, instalując dodatkowe wystąpienia klastrowane SAP ASCS/SCS w istniejącym klastrze windows server failover clustering (WSFC) z dyskiem udostępnionym. Po zakończeniu tego procesu skonfigurowano klaster sap z wieloma identyfikatorami SID.

> [!NOTE]
> Ta funkcja jest dostępna tylko w modelu wdrażania usługi Azure Resource Manager.
>
>Istnieje limit liczby prywatnych adresów IP front-end dla każdego modułu równoważenia obciążenia wewnętrznego platformy Azure.
>
>Maksymalna liczba wystąpień SAP ASCS/SCS w jednym klastrze WSFC jest równa maksymalnej liczbie prywatnych adresów IP front-end dla każdego modułu równoważenia obciążenia wewnętrznego platformy Azure.
>

Aby uzyskać więcej informacji na temat limitów równoważenia obciążenia, zobacz sekcję "Prywatny adres IP na moduł równoważenia obciążenia" w [limitach sieci: Usługa Azure Resource Manager][networking-limits-azure-resource-manager].

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Klaster WSFC został już skonfigurowany do używania dla jednego wystąpienia SAP ASCS/SCS przy użyciu **udziału plików,** jak pokazano na tym diagramie.

![Wystąpienie SAP ASCS/SCS o wysokiej dostępności][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> Konfiguracja musi spełniać następujące warunki:
> * Wystąpienia SAP ASCS/SCS muszą współużytkować ten sam klaster WSFC.
> * Każdy identyfikator SID systemu zarządzania bazami danych (DBMS) musi mieć własny dedykowany klaster WSFC.
> * Serwery aplikacji SAP, które należą do jednego identyfikatora SID systemu SAP, muszą mieć własne dedykowane maszyny wirtualne.

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>Architektura sap ASCS/SCS z dyskiem współdzielonym

Celem jest zainstalowanie wielu wystąpień klastrowanych SAP ABAP ASCS lub SAP Java SCS w tym samym klastrze WSFC, jak pokazano tutaj:

![Wiele wystąpień klastrowanych SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-6002]

Aby uzyskać więcej informacji na temat limitów równoważenia obciążenia, zobacz sekcję "Prywatny adres IP na moduł równoważenia obciążenia" w [limitach sieci: Usługa Azure Resource Manager][networking-limits-azure-resource-manager].

Pełny krajobraz z dwoma systemami SAP o wysokiej dostępności wyglądałby następująco:

![Konfiguracja sap o wysokiej dostępności wielu identyfikatorów SID z dwoma identyfikatorami SID systemu SAP][sap-ha-guide-figure-6003]

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a><a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a>Przygotowanie infrastruktury do scenariusza sap multi-SID

Aby przygotować infrastrukturę, można zainstalować dodatkowe wystąpienie SAP ASCS/SCS z następującymi parametrami:

| Nazwa parametru | Wartość |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS | PR5 |
| Nazwa hosta wirtualnego SAP | pr5-sap-cl |
| Adres IP hosta wirtualnego SAP ASCS/SCS (dodatkowy adres IP modułu równoważenia obciążenia platformy Azure) | 10.0.0.50 |
| Numer wystąpienia SAP ASCS/SCS | 50 |
| Port sondy równoważenia obciążenia sieciowego dla dodatkowego wystąpienia SAP ASCS/SCS | 62350 |

> [!NOTE]
> W przypadku wystąpień klastra SAP ASCS/SCS każdy adres IP wymaga unikatowego portu sondy. Na przykład jeśli jeden adres IP na wewnętrznym modułem równoważenia obciążenia platformy Azure używa portu sondy 62300, żaden inny adres IP na tym modułze równoważenia obciążenia nie może używać portu sondy 62300.
>
>Do naszych celów, ponieważ port sondy 62300 jest już zarezerwowany, używamy portu sondy 62350.

W istniejącym klastrze WSFC można zainstalować dodatkowe wystąpienia SAP ASCS/SCS za pomocą dwóch węzłów:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Statyczny adres IP |
| --- | --- | --- |
| Pierwszy węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| Drugi węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS na serwerze DNS

Wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS można utworzyć przy użyciu następujących parametrów:

| Nowa nazwa wirtualnego hosta SAP ASCS/SCS | Skojarzony adres IP |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Nowa nazwa hosta i adres IP są wyświetlane w Menedżerze DNS, jak pokazano na poniższym zrzucie ekranu:

![Lista Menedżera DNS z wyróżnioniem zdefiniowanego wpisu DNS dla nowej nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-6004]

> [!NOTE]
> Nowy adres IP przypisany do nazwy hosta wirtualnego dodatkowego wystąpienia ASCS/SCS musi być taki sam jak nowy adres IP przypisany do modułu równoważenia obciążenia platformy SAP Azure.
>
>W naszym scenariuszu adres IP to 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Dodawanie adresu IP do istniejącego wewnętrznego modułu równoważenia obciążenia platformy Azure przy użyciu programu PowerShell

Aby utworzyć więcej niż jedno wystąpienie SAP ASCS/SCS w tym samym klastrze WSFC, użyj programu PowerShell, aby dodać adres IP do istniejącego wewnętrznego modułu równoważenia obciążenia platformy Azure. Każdy adres IP wymaga własnych reguł równoważenia obciążenia, portu sondy, puli adresów IP frontowego i puli zaplecza.

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. Zaktualizuj zmienne programu PowerShell dla swojego środowiska. Skrypt tworzy wszystkie wymagane reguły równoważenia obciążenia dla wszystkich portów SAP ASCS/SCS.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure virtual network and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get a new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get an updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po uruchomieniu skryptu wyniki są wyświetlane w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Nowa pula adresów IP front-end w witrynie Azure portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Dodawanie dysków do komputerów klastrowych i konfigurowanie dysku klastra SIOS

Należy dodać nowy dysk udziału klastra dla każdego dodatkowego wystąpienia SAP ASCS/SCS. W systemie Windows Server 2012 R2 dysk udziałów klastrowych WSFC jest obecnie używany jako rozwiązanie programowe SIOS DataKeeper.

Wykonaj następujące czynności:
1. Dodaj dodatkowy dysk lub dyski o tym samym rozmiarze (które należy rozebrać) do każdego z węzłów klastra i sformatować je.
2. Skonfiguruj replikację magazynu za pomocą programu SIOS DataKeeper.

W tej procedurze przyjęto założenie, że program SIOS DataKeeper został już zainstalowany na komputerach klastrowych WSFC. Jeśli został zainstalowany, należy teraz skonfigurować replikację między komputerami. Proces ten jest szczegółowo opisany w pliku [Install SIOS DataKeeper Cluster Edition dla dysku współużytkowania klastra SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].  

![Synchronizowanie datakeeper dla nowego dysku współużytkującego SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>Wdrażanie maszyn wirtualnych dla serwerów aplikacji SAP i klastra DBMS

Aby zakończyć przygotowanie infrastruktury dla drugiego systemu SAP, wykonaj następujące czynności:

1. Wdrażanie dedykowanych maszyn wirtualnych dla serwerów aplikacji SAP i umieszczanie ich we własnej dedykowanej grupie dostępności.
2. Wdrażaj dedykowane maszyny wirtualne dla klastra DBMS i umieść każdy z nich we własnej dedykowanej grupie dostępności.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>Instalowanie systemu SAP NetWeaver z wieloma identyfikatorami SID

Aby uzyskać opis pełnego procesu instalowania drugiego systemu SAP SID2, zobacz [Instalacja SAP NetWeaver HA w klastrze trybu failover systemu Windows i dysku współdzielonym dla wystąpienia SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk].

Procedura wysokiego poziomu jest następująca:

1. [Zainstaluj sap z wysoką dostępnością ascs/scs wystąpienia][sap-high-availability-installation-wsfc-shared-disk-install-ascs].  
 W tym kroku instalujesz sap z wysokiej dostępności ASCS/SCS wystąpienie w istniejącym węźle klastra WSFC 1.

2. [Zmodyfikuj profil SAP wystąpienia ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile].

3. [Konfigurowanie portu sondy][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].  
 W tym kroku konfigurujesz port sondy SAP za pomocą zasobu klastra SAP SAP SAP-SID2-IP przy użyciu programu PowerShell. Wykonaj tę konfigurację w jednym z węzłów klastra SAP ASCS/SCS.

4. Zainstaluj wystąpienie bazy danych.  
 Aby zainstalować drugi klaster, wykonaj kroki opisane w podręczniku instalacji SAP.

5. Zainstaluj drugi węzeł klastra.  
 W tym kroku instalujesz sap z wysokiej dostępności ASCS/SCS wystąpienie w istniejącym węźle klastra WSFC 2. Aby zainstalować drugi klaster, wykonaj kroki opisane w podręczniku instalacji SAP.

6. Otwórz porty Zapory systemu Windows dla wystąpienia SAP ASCS/SCS i portu sondowania.  
    W obu węzłach klastra, które są używane dla wystąpień SAP ASCS/SCS, są otwierane wszystkie porty Zapory systemu Windows, które są używane przez SAP ASCS/SCS. Te porty wystąpienia SAP ASCS/SCS są wymienione w rozdziale [SAP ASCS / SCS Ports][sap-net-weaver-ports-ascs-scs-ports].

    Aby uzyskać listę wszystkich innych portów SAP, zobacz [Porty TCP/IP wszystkich produktów SAP][sap-net-weaver-ports].  

    Otwórz również port sondy modułu równoważenia obciążenia platformy Azure, który jest 62350 w naszym scenariuszu. Jest to opisane [w tym artykule][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port].

7. [Zmień typ początkowy wystąpienia usługi rozliczenia paragonu (ERS) sap oceniane (ERS).][sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]

8. Zainstaluj podstawowy serwer aplikacji SAP na nowej dedykowanej maszynie wirtualnej, zgodnie z opisem w przewodniku instalacji SAP.  

9. Zainstaluj serwer aplikacji SAP na nowej dedykowanej maszynie wirtualnej, zgodnie z opisem w przewodniku instalacji SAP.

10. [Przetestuj proces pracy awaryjnej wystąpienia SAP ASCS/SCS i replikację SIOS][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl].

## <a name="next-steps"></a>Następne kroki

- [Limity sieci: Usługa Azure Resource Manager][networking-limits-azure-resource-manager]
- [Wiele adresów VIP dla modułu równoważenia obciążenia platformy Azure][load-balancer-multivip-overview]
