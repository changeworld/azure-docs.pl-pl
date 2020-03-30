---
title: Architektura wysokiej lokalizacji maszyn wirtualnych platformy Azure i scenariusze dla sap NetWeaver | Dokumenty firmy Microsoft
description: Architektura o wysokiej dostępności i scenariusze dla sap NetWeaver na maszynach wirtualnych platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08f770ced6cb1ec1102159788e1583d481436b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279912"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektura i scenariusze wysokiej dostępności dla SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności sap multi-SID)


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


## <a name="terminology-definitions"></a>Definicje terminologii

**Wysoka dostępność:** Odnosi się do zestawu technologii, które minimalizują zakłócenia w technologii IT, zapewniając ciągłość działania usług IT za pośrednictwem nadmiarowych, odpornych na uszkodzenia lub składników chronionych przez tryb failover w *tym samym* centrum danych. W naszym przypadku centrum danych znajduje się w jednym regionie platformy Azure.

**Odzyskiwanie po awarii:** odnosi się również do minimalizacji zakłóceń w usługach IT i ich odzyskiwania, ale w *różnych* centrach danych, które mogą znajdować się setki kilometrów od siebie. W naszym przypadku centra danych mogą znajdować się w różnych regionach platformy Azure w tym samym regionie geopolitycznym lub w lokalizacjach ustalonych przez Ciebie jako klienta.


## <a name="overview-of-high-availability"></a>Przegląd wysokiej dostępności
Wysoka dostępność sap na platformie Azure można podzielić na trzy typy:

* **Wysoka dostępność infrastruktury platformy Azure:** 

    Na przykład wysoka dostępność może obejmować obliczeń (maszyny wirtualne), sieci lub magazynu i jego korzyści dla zwiększenia dostępności aplikacji SAP.

* **Korzystanie z ponownego uruchamiania maszyny wirtualnej infrastruktury platformy Azure w celu uzyskania *większej dostępności* aplikacji SAP:** 

    Jeśli zdecydujesz się nie używać funkcji, takich jak klastrowanie trybu failover systemu Windows Server (WSFC) lub rozrusznik serca w systemie Linux, zostanie użyte ponowne uruchomienie maszyny Wirtualnej platformy Azure. Chroni systemy SAP przed planowanymi i nieplanowanymi przestojami infrastruktury serwera fizycznego platformy Azure i ogólnej podstawowej platformy Azure.

* **Wysoka dostępność aplikacji SAP:** 

    Aby uzyskać pełną wysoką dostępność systemu SAP, należy chronić wszystkie krytyczne składniki systemu SAP. Przykład:
    * Nadmiarowe serwery aplikacji SAP.
    * Unikalne komponenty. Przykładem może być pojedynczy składnik punktu awarii (SPOF), taki jak wystąpienie SAP ASCS/SCS lub system zarządzania bazami danych (DBMS).

Wysoka dostępność sap na platformie Azure różni się od wysokiej dostępności SAP w lokalnym środowisku fizycznym lub wirtualnym. W poniższym dokumencie [SAP NetWeaver wysoka dostępność i ciągłość działania w środowiskach wirtualnych z VMware i Hyper-V w systemie Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] opisuje standardowe konfiguracje SAP o wysokiej dostępności w środowiskach zwirtualizowanych w systemie Windows.

Nie ma sapinst zintegrowane SAP wysokiej dostępności konfiguracji dla Systemu Linux, jak istnieje dla systemu Windows. Aby uzyskać informacje o wysokiej dostępności sap w środowisku lokalnym dla systemu Linux, zobacz [Informacje o partnerach o wysokiej dostępności][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure

### <a name="sla-for-single-instance-virtual-machines"></a>La dla maszyn wirtualnych z jednym wystąpieniem

Obecnie istnieje pojedyncza maszyna wirtualna SLA 99,9% z pamięcią masową w jakości Premium. Aby zorientować się, jaka może być dostępność pojedynczej maszyny Wirtualnej, można utworzyć produkt różnych dostępnych [umów dotyczących poziomu usług platformy Azure.][azure-sla]

Podstawą obliczeń jest 30 dni w miesiącu lub 43 200 minut. Na przykład przestój 0,05% odpowiada 21,6 minut. Jak zwykle dostępność różnych usług jest obliczana w następujący sposób:

(Usługa dostępności #1/100) * (usługa dostępności #2/100) * (usługa dostępności #3/100) \*...

Przykład:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 lub ogólna dostępność 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Wiele wystąpień maszyn wirtualnych w tym samym zestawie dostępności
Dla wszystkich maszyn wirtualnych, które mają dwa lub więcej wystąpień wdrożonych w tym samym *zestawie dostępności,* gwarantujemy, że będziesz miał łączność z maszyną wirtualną z co najmniej jednym wystąpieniem co najmniej 99,95% czasu.

Gdy co najmniej dwie maszyny wirtualne są częścią tego samego zestawu dostępności, każdej maszynie wirtualnej w zestawie dostępności jest przypisywana *domena aktualizacji* i *domena błędów* przez podstawową platformę Azure.

* **Domeny aktualizacji** gwarantują, że wiele maszyn wirtualnych nie są ponownie uruchamiane w tym samym czasie podczas planowanej konserwacji infrastruktury platformy Azure. Tylko jedna maszyna wirtualna jest ponownie uruchamiana w czasie.

* **Domeny błędów** gwarantują, że maszyny wirtualne są wdrażane na składnikach sprzętowych, które nie mają wspólnego źródła zasilania i przełącznika sieciowego. Gdy serwery, przełącznik sieciowy lub źródło zasilania przechodzą nieplanowany czas przestoju, dotyczy to tylko jednej maszyny Wirtualnej.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure][azure-virtual-machines-manage-availability].

Zestaw dostępności służy do osiągnięcia wysokiej dostępności:

* Nadmiarowe serwery aplikacji SAP.  
* Klastry z co najmniej dwoma węzłami (na przykład maszynami wirtualnymi), które chronią pliki SPOF, takie jak wystąpienie SAP ASCS/SCS lub system dbms.


### <a name="azure-availability-zones"></a>Strefy dostępności platformy Azure
Platforma Azure jest w trakcie rozwijania koncepcji [stref dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) w różnych [regionach platformy Azure.](https://azure.microsoft.com/global-infrastructure/regions/) W regionach platformy Azure, w których oferowane są strefy dostępności, regiony platformy Azure mają wiele centrów danych, które są niezależne pod względem źródła zasilania, chłodzenia i sieci. Powodem oferowania różnych stref w jednym regionie platformy Azure jest umożliwienie wdrażania aplikacji w dwóch lub trzech strefach dostępności oferowanych. Zakładając, że problemy ze źródłami zasilania i/lub siecią będą miały wpływ tylko na jedną infrastrukturę strefy dostępności, wdrożenie aplikacji w regionie platformy Azure jest nadal w pełni funkcjonalne. Ostatecznie z pewną zmniejszoną pojemnością, ponieważ niektóre maszyny wirtualne w jednej strefie mogą zostać utracone. Ale maszyny wirtualne w pozostałych dwóch strefach są nadal uruchomione. Regiony platformy Azure, które oferują strefy są wymienione w [strefach dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Korzystając ze stref dostępności, należy wziąć pod uwagę kilka czynników. Lista zagadnień, takich jak:

- Nie można wdrożyć zestawów dostępności platformy Azure w strefie dostępności. Należy wybrać strefę dostępności lub zestaw dostępności jako ramkę wdrażania maszyny Wirtualnej.
- Nie można użyć [podstawowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) do tworzenia rozwiązań klastra trybu failover opartych na usługach klastra trybu failover systemu Windows lub rozruszniku systemu Linux. Zamiast tego należy użyć jednostki [SKU równoważenia obciążenia standardowego platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- Strefy dostępności platformy Azure nie dają żadnych gwarancji pewnej odległości między różnymi strefami w jednym regionie
- Opóźnienie sieci między różnymi strefami dostępności platformy Azure w różnych regionach platformy Azure może się różnić w zależności od regionu platformy Azure. Będą przypadki, w których jako klient można rozsądnie uruchomić warstwę aplikacji SAP wdrożone w różnych strefach, ponieważ opóźnienie sieci z jednej strefy do aktywnej maszyny Wirtualnej DBMS jest nadal dopuszczalne z wpływu procesu biznesowego. Scenariusze klienta będą, w których opóźnienie między aktywną maszyną wirtualną DBMS w jednej strefie a wystąpieniem aplikacji SAP na maszynie Wirtualnej w innej strefie może być zbyt inwazyjne i nie do przyjęcia dla procesów biznesowych SAP. W rezultacie architektury wdrażania muszą być różne z aktywnej/aktywnej architektury dla aplikacji lub architektury aktywnej/pasywnej, jeśli opóźnienie jest zbyt wysokie.
- Korzystanie z [dysków zarządzanych platformy Azure](https://azure.microsoft.com/services/managed-disks/) jest obowiązkowe do wdrażania w strefach dostępności platformy Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Planowana i nieplanowana konserwacja maszyn wirtualnych

Dwa typy zdarzeń platformy Azure platformy może mieć wpływ na dostępność maszyn wirtualnych:

* **Planowane** zdarzenia konserwacji są okresowymi aktualizacjami wprowadzonymi przez firmę Microsoft na podstawowej platformie Azure. Aktualizacje zwiększają ogólną niezawodność, wydajność i bezpieczeństwo infrastruktury platformy, na których działają maszyny wirtualne.

* **Nieplanowane** zdarzenia konserwacji występują, gdy sprzęt lub infrastruktura fizyczna leżąca u podstaw maszyny wirtualnej w jakiś sposób uległa awarii. Może to obejmować awarie sieci lokalnej, awarie dysków lokalnych lub inne awarie na poziomie stelaża. Po wykryciu takiego błędu platforma Azure automatycznie migruje maszynę wirtualną ze złej kondycji serwera fizycznego, na którym znajduje się maszyna wirtualna, na zdrowy serwer fizyczny. Takie zdarzenia są rzadkie, ale mogą również powodować ponowne uruchomienie maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage
Dane na koncie magazynu są zawsze replikowane, aby zapewnić trwałość i wysoką dostępność, spełniając umowy SLA usługi Azure Storage nawet w przypadku przejściowych awarii sprzętu.

Ponieważ usługa Azure Storage domyślnie przechowuje trzy obrazy danych, użycie raidu 5 lub RAID 1 na wielu dyskach platformy Azure jest niepotrzebne.

Aby uzyskać więcej informacji, zobacz [Replikacja usługi Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Dyski zarządzane platformy Azure
Dyski zarządzane to typ zasobu w usłudze Azure Resource Manager, który jest zalecany do użycia zamiast wirtualnych dysków twardych (VHD), które są przechowywane na kontach magazynu platformy Azure. Dyski zarządzane automatycznie wyrównują się z zestawem dostępności platformy Azure maszyny wirtualnej, do których są dołączone. Zwiększają one dostępność maszyny wirtualnej i usług, które są na niej uruchomione.

Aby uzyskać więcej informacji, zobacz [omówienie omówienie dysków zarządzanych platformy Azure][azure-storage-managed-disks-overview].

Zaleca się używanie dysków zarządzanych, ponieważ upraszczają one wdrażanie maszyn wirtualnych i zarządzanie nimi.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Wykorzystanie wysokiej dostępności infrastruktury platformy Azure w celu uzyskania *większej dostępności* aplikacji SAP

Jeśli zdecydujesz się nie używać funkcji, takich jak WSFC lub Pacemaker w systemie Linux (obecnie obsługiwane tylko dla SUSE Linux Enterprise Server [SLES] 12 i nowsze), ponowne uruchomienie maszyny Wirtualnej platformy Azure jest wykorzystywane. Chroni systemy SAP przed planowanymi i nieplanowanymi przestojami infrastruktury serwera fizycznego platformy Azure i ogólnej podstawowej platformy Azure.

Aby uzyskać więcej informacji na temat tego podejścia, zobacz [Korzystanie z ponownego uruchamiania maszyny wirtualnej infrastruktury platformy Azure w celu uzyskania większej dostępności systemu SAP.][sap-higher-availability]

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Wysoka dostępność aplikacji SAP na platformie Azure IaaS

Aby uzyskać pełną wysoką dostępność systemu SAP, należy chronić wszystkie krytyczne składniki systemu SAP. Przykład:
  * Nadmiarowe serwery aplikacji SAP.
  * Unikalne komponenty. Przykładem może być pojedynczy składnik punktu awarii (SPOF), taki jak wystąpienie SAP ASCS/SCS lub system zarządzania bazami danych (DBMS).

W następnych sekcjach omówiono sposób osiągnięcia wysokiej dostępności dla wszystkich trzech krytycznych składników systemu SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architektura wysokiej dostępności dla serwerów aplikacji SAP

> Niniejsza sekcja dotyczy:
>
> ![Windows][Logo_Windows] Okna i ![Linux][Logo_Linux] Linux
>

Zazwyczaj nie potrzebujesz określonego rozwiązania o wysokiej dostępności dla serwera aplikacji SAP i wystąpień okien dialogowych. Osiągasz wysoką dostępność przez nadmiarowość i konfigurujesz wiele wystąpień okien dialogowych w różnych wystąpieniach maszyn wirtualnych platformy Azure. Powinien mieć co najmniej dwa wystąpienia aplikacji SAP zainstalowane w dwóch wystąpieniach maszyn wirtualnych platformy Azure.

![Rysunek 1: Serwer aplikacji SAP o wysokiej dostępności][sap-ha-guide-figure-2000]

_**Rysunek 1:** Serwer aplikacji SAP o wysokiej dostępności_

Należy umieścić wszystkie maszyny wirtualne, które obsługują wystąpienia serwera aplikacji SAP w tym samym zestawie dostępności platformy Azure. Zestaw dostępności platformy Azure zapewnia, że:

* Wszystkie maszyny wirtualne są częścią tej samej domeny aktualizacji.  
    Domena aktualizacji zapewnia, że maszyny wirtualne nie są aktualizowane w tym samym czasie podczas planowanych przestojów konserwacji.

    Podstawowe funkcje, które opiera się na różnych domen aktualizacji i błędów w ramach jednostki skalowania platformy Azure, został już wprowadzony w sekcji [domen aktualizacji.][planning-guide-3.2.2]

* Wszystkie maszyny wirtualne są częścią tej samej domeny błędów.  
    Domena błędów zapewnia, że maszyny wirtualne są wdrażane, dzięki czemu żaden pojedynczy punkt awarii nie wpływa na dostępność wszystkich maszyn wirtualnych.

Liczba domen aktualizacji i błędów, które mogą być używane przez zestaw dostępności platformy Azure w jednostce skalowania platformy Azure jest skończona. Jeśli nadal dodajesz maszyny wirtualne do jednego zestawu dostępności, co najmniej dwie maszyny wirtualne ostatecznie znajdą się w tej samej domenie błędów lub aktualizacji.

Jeśli wdrożysz kilka wystąpień serwera aplikacji SAP w dedykowanych maszynach wirtualnych, przy założeniu, że mamy pięć domen aktualizacji, pojawi się następujący obraz. Rzeczywista maksymalna liczba domen aktualizacji i błędów w ramach zestawu dostępności może ulec zmianie w przyszłości:

![Rysunek 2: Wysoka dostępność serwerów aplikacji SAP][planning-guide-figure-3000]
w zestawie dostępności platformy Azure_**Rysunek 2:** Wysoka dostępność serwerów aplikacji SAP w zestawie dostępności platformy Azure_

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure][azure-virtual-machines-manage-availability].

Aby uzyskać więcej informacji, zobacz sekcję [Zestawy dostępności platformy Azure][planning-guide-3.2.3] w dokumencie i implementacji maszyn wirtualnych platformy Azure dla dokumentu SAP NetWeaver.

**Tylko dyski niezarządzane:** Ponieważ konto magazynu platformy Azure jest potencjalnym pojedynczym punktem awarii, ważne jest, aby mieć co najmniej dwa konta magazynu platformy Azure, na których są dystrybuowane co najmniej dwie maszyny wirtualne. W idealnej konfiguracji dyski każdej maszyny wirtualnej z uruchomionym wystąpieniem okna dialogowego SAP zostaną wdrożone na innym koncie magazynu.

> [!IMPORTANT]
> Zdecydowanie zaleca się używanie dysków zarządzanych platformy Azure do instalacji o wysokiej dostępności sap. Ponieważ dyski zarządzane automatycznie wyrównać z zestawem dostępności maszyny wirtualnej, które są dołączone do, zwiększają dostępność maszyny wirtualnej i usług, które są na niej uruchomione.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architektura wysokiej dostępności dla wystąpienia SAP ASCS/SCS w systemie Windows

> ![Windows][Logo_Windows] Windows
>

Można użyć rozwiązania WSFC do ochrony wystąpienia SAP ASCS/SCS. Rozwiązanie ma dwa warianty:

* **Grupowanie wystąpienia SAP ASCS/SCS przy użyciu klastrowanych dysków udostępnionych:** Aby uzyskać więcej informacji na temat tej architektury, zobacz [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk].   

* **Grupowanie wystąpienia SAP ASCS/SCS przy użyciu udziału plików:** Aby uzyskać więcej informacji na temat tej architektury, zobacz [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików][sap-high-availability-guide-wsfc-file-share].

* **Grupowanie wystąpienia SAP ASCS/SCS przy użyciu udziału SMB ANF:** Aby uzyskać więcej informacji na temat tej architektury, zobacz [Klaster klastra wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików SMB ANF](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb).

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architektura o wysokiej dostępności dla wystąpienia SAP ASCS/SCS w systemie Linux

> ![Linux][Logo_Linux] Linux
> 
> Aby uzyskać więcej informacji na temat klastrowania wystąpienia SAP ASCS/SCS przy użyciu struktury klastra SLES, zobacz [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server dla aplikacji SAP][sap-suse-ascs-ha]. Alternatywna architektura wysokiej dostępności w systemie SLES, która nie wymaga wysoce dostępnego systemu plików NFS, zobacz [Przewodnik o wysokiej dostępności dla SAP NetWeaver na SUSE Linux Enterprise Server z usługą Azure NetApp Files for SAP applications][sap-suse-ascs-ha-anf].

Aby uzyskać więcej informacji na temat klastrowania wystąpienia SAP ASCS/SCS przy użyciu struktury klastra Red Hat, zobacz [Wysokiej dostępności maszyn wirtualnych platformy Azure dla SAP NetWeaver w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Konfiguracja wielu identyfikatorów SID SAP NetWeaver dla klastrowanego wystąpienia SAP ASCS/SCS

> ![Windows][Logo_Windows] Windows
> 
> Multi-SID jest obsługiwany przez WSFC, przy użyciu udziału plików i udostępnionego dysku.
> 
> Aby uzyskać więcej informacji na temat architektury o wysokiej dostępności wielu identyfikatorów SID w systemie Windows, zobacz:

* [Sap ASCS/SCS wystąpienie wielu identyfikatorów SID wysoka dostępność dla klastrów trybu failover systemu Windows Server i udziału plików][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Sap ASCS/SCS wystąpienie wielu identyfikatorów SID wysoka dostępność dla klastrów trybu failover systemu Windows Server i udostępnionego dysku][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux][Logo_Linux] Linux
> 
> Klastrowanie wielu identyfikatorów SID jest obsługiwane w klastrach rozruszników systemu Linux dla SAP ASCS/ERS, ograniczonych do **pięciu** identyfikatorów SAP W TYM SAMYM KLASTRZE.
> Aby uzyskać więcej informacji na temat architektury o wysokiej dostępności wielu identyfikatorów SID w systemie Linux, zobacz:

* [Ha dla SAP NW na maszynach wirtualnych platformy Azure na SLES dla aplikacji SAP — przewodnik po wielu identyfikatorach SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Ha dla SAP NW na maszynach wirtualnych platformy Azure na RHEL dla aplikacji SAP — przewodnik po wielu identyfikatorach SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="high-availability-dbms-instance"></a>Wystąpienie usługi DBMS o wysokiej dostępności

DbMS jest również pojedynczym punktem kontaktowym w systemie SAP. Należy go chronić za pomocą rozwiązania o wysokiej dostępności. Na poniższej ilustracji przedstawiono rozwiązanie SQL Server AlwaysOn o wysokiej dostępności na platformie Azure z klastrem trybu failover systemu Windows Server i wewnętrznym modułem równoważenia obciążenia platformy Azure. SQL Server AlwaysOn replikuje pliki danych i dzienników DBMS przy użyciu własnej replikacji dbms. W takim przypadku nie potrzebujesz udostępnionego dysku klastra, co upraszcza całą konfigurację.

![Rysunek 3: Przykład wysokiej dostępności SAP DBMS z programem SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Rysunek 3:** Przykład wysokiej dostępności sap DBMS z SQL Server AlwaysOn_

Aby uzyskać więcej informacji na temat klastrowania usługi DBMS programu SQL Server na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, zobacz następujące artykuły:

* [Ręczne konfigurowanie grupy dostępności AlwaysOn na maszynach wirtualnych platformy Azure przy użyciu Menedżera zasobów][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurowanie wewnętrznego modułu równoważenia obciążenia platformy Azure dla grupy dostępności AlwaysOn na platformie Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Aby uzyskać więcej informacji na temat klastrowania systemu SAP HANA DBMS na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, zobacz [Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure (VMs).][sap-hana-ha]
