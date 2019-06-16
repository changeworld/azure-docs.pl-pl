---
title: Architektura wysokiej dostępności na platformie Azure maszyn wirtualnych i scenariusze SAP NetWeaver | Dokumentacja firmy Microsoft
description: Architektura wysokiej dostępności i scenariusze SAP NetWeaver na maszynach wirtualnych platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37f5040585681a53743fb3426b7f7ffac36de51c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60936275"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektura wysokiej dostępności i scenariusze SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności — wiele identyfikatorów SID SAP)


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


## <a name="terminology-definitions"></a>Definicji

**Wysoka dostępność**: Dotyczy to zestaw technologii, które zminimalizować zakłócenia IT, zapewniając ciągłość usług IT za pomocą składników nadmiarowe, odpornej na uszkodzenia lub pracy awaryjnej, chronione wewnątrz *tego samego* centrum danych. W naszym przypadku centrum danych znajduje się w obrębie jednego regionu platformy Azure.

**Odzyskiwanie po awarii**: Odnosi się także do zminimalizowanie przerw w działaniu usług IT oraz ich odzyskiwanie, ale na *różnych* centrów danych, które mogą być setek mil od siebie nawzajem. W naszym przypadku centrów danych mogą znajdować się w różnych regionach platformy Azure, w tym samym regionie geograficznymi, lub w lokalizacjach, jak ustalono przez Ciebie jako klient.


## <a name="overview-of-high-availability"></a>Omówienie wysokiej dostępności
SAP wysokiej dostępności na platformie Azure można podzielić na trzy typy:

* **Wysoka dostępność infrastruktury platformy Azure**: 

    Na przykład wysokiej dostępności może zawierać obliczeniowych (maszyn wirtualnych), sieci lub magazynu oraz korzyści dla zwiększenia dostępności aplikacji SAP.

* **Przy użyciu infrastruktury platformy Azure maszyna wirtualna ponownie uruchomiona, aby osiągnąć *wyższą dostępność* aplikacje SAP**: 

    Jeśli zdecydujesz się nie należy używać funkcji, takich jak Windows Server Failover Clustering (WSFC) lub program Pacemaker w systemie Linux jest wykorzystywany ponownego uruchomienia maszyny Wirtualnej platformy Azure. Chroni systemów SAP przed planowanych lub nieplanowanych przestojów infrastruktury platformy Azure serwer fizyczny i ogólną podstawowej platformy Azure.

* **Wysoka dostępność aplikacji SAP**: 

    Aby osiągnąć pełną SAP system wysokiej dostępności, należy włączyć ochronę wszystkich krytycznych składników systemu SAP. Na przykład:
    * Nadmiarowe serwery aplikacji SAP.
    * Unikatowe składniki. Przykładem może być pojedynczym punktem awarii (SPOF) składnika, takiego jak wystąpienie SAP ASCS/SCS lub system zarządzania bazy danych (DBMS).

SAP wysokiej dostępności na platformie Azure różni się od SAP wysokiej dostępności w lokalnym środowisku fizycznym lub wirtualnym. Poniższy dokument [oprogramowanie SAP NetWeaver wysoką dostępność i ciągłość działania w środowiskach wirtualnych programu VMware i funkcji Hyper-V w systemie Microsoft Windows] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] w tym artykule opisano standardowy SAP o wysokiej dostępności konfiguracje w środowiskach zwirtualizowanych na Windows.

Brak konfiguracji o wysokiej dostępności SAP zintegrowane sapinst dla systemu Linux, ponieważ ma dla Windows. Aby uzyskać informacji na temat SAP o wysokiej dostępności w środowisku lokalnym dla systemu Linux, zobacz [informacji o partnerze wysokiej dostępności][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure

### <a name="sla-for-single-instance-virtual-machines"></a>Umowa SLA dla maszyn wirtualnych z jednego wystąpienia

Obecnie jest maszyn wirtualnych na jednym umów SLA na poziomie 99,9% dzięki usłudze premium storage. Aby uzyskać pojęcie o tym, co może być dostępności jednej maszyny Wirtualnej, możesz tworzyć produktu dostępna różnych [umowy dotyczące poziomu usług platformy Azure][azure-sla].

Podstawą obliczeń jest 30 dni, każdego miesiąca lub — 43 200 minut. Na przykład przestój 0,05% odnosi się do 21,6 minut. Dostępność poszczególnych usług oblicza się w zwykły sposób, w następujący sposób:

(Usługa dostępności #1/100) * (Usługa dostępności #2/100) * (Usługa dostępności #3/100) \*...

Na przykład:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 lub ogólnej dostępności 99.75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Wiele wystąpień maszyn wirtualnych w tym samym zestawie dostępności
Dla wszystkich maszyn wirtualnych, co najmniej dwóch wystąpieniach wdrożonych w tym samym *zestaw dostępności*, firma Microsoft gwarantuje, że będą mieć łączność maszyny wirtualnej z co najmniej jedno wystąpienie co najmniej 99,95% czasu.

Gdy co najmniej dwie maszyny wirtualne są częścią tego samego zestawu dostępności, każda maszyna wirtualna w zestawie dostępności jest przypisany *domena aktualizacji* i *domena błędów* odpowiedniej platformy Azure.

* **Domeny aktualizacji** gwarantuje, że wiele maszyn wirtualnych nie są ponownie uruchamiane w tym samym czasie podczas planowanej konserwacji infrastruktury platformy Azure. Tylko jedna maszyna wirtualna jest uruchomiona ponownie w danym momencie.

* **Domeny błędów** gwarantuje, że maszyny wirtualne są wdrażane na składniki sprzętowe, które nie mają wspólnego źródła zasilania i sieci przełącznika. Jeśli serwery, przełącznik sieciowy lub źródła zasilania poddaje nieplanowane przestoje, tylko jedna maszyna wirtualna ma wpływ.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure][azure-virtual-machines-manage-availability].

Zestaw dostępności jest używany dla osiągnięcia wysokiej dostępności:

* Nadmiarowe serwery aplikacji SAP.  
* Klastry z co najmniej dwa węzły (maszyny wirtualne, na przykład), które chronią SPOFs, takiego jak wystąpienie SAP ASCS/SCS lub DBMS.


### <a name="azure-availability-zones"></a>Strefy dostępności platformy Azure
Platforma Azure jest zadaniem wprowadza pojęcia związane z [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) w całym różnych [regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). W regionach platformy Azure, w których są oferowane strefy dostępności regiony platformy Azure mają wiele centrów danych, które są niezależne w dostarczaniu źródła zasilania, chłodzenie i usługi sieci. Przyczyna oferty różnych stref w jednym regionie platformy Azure jest umożliwia wdrażanie aplikacji w dwóch lub trzech strefach dostępności oferowanych. Przy założeniu, że jedna infrastruktura strefy dostępności będzie wpływają problemy z źródła zasilania i/lub sieci, wdrażania aplikacji w regionie platformy Azure nadal jest w pełni funkcjonalny. Po pewnym czasie za pomocą niektórych pojemności mniejsze od niektórych maszyn wirtualnych w jednej strefie mogą zostać utracone. Ale maszyny wirtualne w innych strefach są wciąż uruchomione i działają prawidłowo. Regiony platformy Azure, które oferują strefy są wymienione w [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Przy użyciu stref dostępności, istnieje kilka kwestii do rozważenia. Lista zagadnienia, takie jak:

- Nie można wdrożyć zestawy dostępności platformy Azure w strefie dostępności. Musisz wybrać strefy dostępności lub zestawie dostępności jako ramkę wdrażania dla maszyny Wirtualnej.
- Nie można użyć [podstawowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) tworzenia rozwiązań klastra na podstawie usługi klastra pracy awaryjnej Windows lub program Pacemaker w systemie Linux, trybu failover. Zamiast tego należy użyć [standardowy SKU modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- Strefy dostępności platformy Azure nie daje żadnej gwarancji niektórych odległość między różnymi strefami w ramach jednego regionu
- Opóźnienie sieci między różnymi strefami dostępności platformy Azure w różnych regionach platformy Azure może się różnić od regionu platformy Azure. Będzie przypadki, gdzie można zgodnie z klientem rozsądnie uruchomić warstwy aplikacji SAP, które są wdrożone w różnych strefach od opóźnienia sieci z jedną strefę do aktywnej maszyny Wirtualnej systemu DBMS jest nadal dopuszczalne z procesu wpływ na działalność. Konieczne będzie scenariuszy, w którym opóźnienie między aktywnej maszyny Wirtualnej systemu DBMS, w jednej strefie i wystąpienie aplikacji SAP na maszynie wirtualnej w innej strefie może być zbyt inwazyjne i nie do przyjęcia dla procesów biznesowych SAP. Co w efekcie architektur wdrożeń muszą być inne w przypadku architektury aktywny/aktywny dla aplikacji lub architektury aktywny/pasywny, jeśli czas oczekiwania jest zbyt duża.
- Za pomocą [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/) jest obowiązkowa w przypadku wdrażania w strefy dostępności platformy Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Planowanych i nieplanowanych konserwacji maszyn wirtualnych

Dwa typy zdarzeń platformy Azure może mieć wpływ na dostępność maszyn wirtualnych:

* **Planowana konserwacja** zdarzenia to okresowe aktualizacje przeprowadzane przez firmę Microsoft dla podstawowej platformy Azure. Aktualizacje zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy, które są uruchamiane maszyny wirtualne.

* **Nieplanowana konserwacja** zdarzenia wystąpić, gdy sprzętu lub infrastruktury fizycznej maszyny wirtualnej nie powiodło się w jakiś sposób. Może on zawierać awarii sieci lokalnej, awariami dysków lokalnych lub inne błędy poziomu stojaka. Po wykryciu awarii tego typu platforma Azure automatycznie migruje maszynę wirtualną z złej kondycji serwera fizycznego, który jest hostem maszyny wirtualnej do fizycznego serwera dobrej kondycji. Takie zdarzenia występują rzadko, ale również spowodować ponowne uruchomienie Twojej maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Nadmiarowość magazynu platformy Azure
Dane na koncie usługi storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności, spełnia wymagania umowy SLA magazynu platformy Azure nawet w wypadku przejściowych awarii sprzętu.

Ponieważ usługi Azure Storage przechowuje trzy obrazy danych domyślnie nie jest konieczne użycie RAID 5 lub RAID 1 na wielu dyskach platformy Azure.

Aby uzyskać więcej informacji, zobacz [replikacja usługi Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Dyski zarządzane platformy Azure
Managed Disks to typ zasobu w usłudze Azure Resource Manager zalecaną ma być używana zamiast funkcji wirtualnych dysków twardych (VHD), które są przechowywane na kontach usługi Azure storage. Dyski zarządzane automatycznie dostosowanie zestawu dostępności platformy Azure, maszyny wirtualnej, które są dołączone. Zwiększają dostępność maszyny wirtualnej i usługi, które w nim działają.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Managed Disks][azure-storage-managed-disks-overview].

Firma Microsoft zaleca, korzystają z dysków zarządzanych, ponieważ mogą uprościć wdrażanie i zarządzanie maszynami wirtualnymi.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Korzystanie z wysokiej dostępności infrastruktury platformy Azure, aby osiągnąć *wyższą dostępność* aplikacje SAP

Jeśli zdecydujesz się nie należy używać funkcji, takich jak usługi WSFC lub program Pacemaker w systemie Linux (obecnie obsługiwane tylko w przypadku systemu SUSE Linux Enterprise Server [SLES] 12 i nowsze), ponowne uruchomienie maszyny Wirtualnej platformy Azure jest używana. Chroni systemów SAP przed planowanych lub nieplanowanych przestojów infrastruktury platformy Azure serwer fizyczny i ogólną podstawowej platformy Azure.

Aby uzyskać więcej informacji na temat tego podejścia, zobacz [korzystanie z infrastruktury platformy Azure maszyna wirtualna uruchomiony ponownie w celu osiągnięcia wyższej dostępności systemu SAP][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Wysoka dostępność aplikacji SAP na platformie IaaS Azure

Aby osiągnąć pełną SAP system wysokiej dostępności, należy włączyć ochronę wszystkich krytycznych składników systemu SAP. Na przykład:
  * Nadmiarowe serwery aplikacji SAP.
  * Unikatowe składniki. Przykładem może być pojedynczym punktem awarii (SPOF) składnika, takiego jak wystąpienie SAP ASCS/SCS lub system zarządzania bazy danych (DBMS).

W kolejnych sekcjach omówiono sposób zapewniania wysokiej dostępności dla wszystkich składników systemu SAP krytyczne trzy.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architektura wysokiej dostępności dla serwerów aplikacji SAP

> W tej sekcji mają zastosowanie do:
>
> ![Windows][Logo_Windows] Windows i ![Linux][Logo_Linux] Linux
>

Zazwyczaj nie trzeba konkretnego rozwiązania o wysokiej dostępności dla wystąpienia okno dialogowe i serwera aplikacji SAP. Uzyskiwanie wysokiej dostępności dzięki nadmiarowości i skonfiguruj wiele wystąpień okna dialogowego w różnych wystąpieniach maszyn wirtualnych platformy Azure. Należy mieć co najmniej dwa wystąpienia aplikacji SAP, zainstalowane w dwa wystąpienia maszyn wirtualnych platformy Azure.

![Rysunek 1: Serwer aplikacji SAP o wysokiej dostępności][sap-ha-guide-figure-2000]

_**Rysunek 1:** Serwer aplikacji SAP o wysokiej dostępności_

Należy umieścić wszystkie maszyny wirtualne, które ustawione wystąpień serwera aplikacji SAP hosta w tym samym dostępności platformy Azure. Zestawu dostępności platformy Azure zapewnia, że:

* Wszystkie maszyny wirtualne są częścią tej samej domenie aktualizacji.  
    Domena aktualizacji gwarantuje, że maszyny wirtualne nie zostaną zaktualizowane w tym samym czasie podczas planowanej konserwacji przestojów.

    Podstawowe funkcje, która opiera się na różnych aktualizacji i domen błędów w jednostce skalowania na platformie Azure, już została wprowadzona w systemie [domeny aktualizacji] [ planning-guide-3.2.2] sekcji.

* Wszystkie maszyny wirtualne są częścią tej samej domenie błędów.  
    Domena błędów gwarantuje, że maszyny wirtualne są wdrażane tak, aby żadnego pojedynczego punktu awarii wpływa na dostępność maszyn wirtualnych.

Liczba domen aktualizacji i błędów, które mogą być używane przez zestaw w jednostce skalowania na platformie Azure dostępności platformy Azure jest jednak ograniczona. Zachowaj dodawania maszyn wirtualnych na pojedynczym zestawie dostępności, co najmniej dwie maszyny wirtualne będą ostatecznie znajdą się w tej samej domenie błędów lub aktualizacji.

Jeśli wdrożono kilka wystąpień serwera aplikacji SAP w ich dedykowanych maszyn wirtualnych, przy założeniu, że mamy pięcioma domenami aktualizacji, wynika poniższej ilustracji. Rzeczywiste maksymalną liczbę domen aktualizacji i błędów w zestawie dostępności mogą ulec zmianie w przyszłości:

![Rysunek 2: Wysoką dostępność serwerów aplikacji SAP w zestawie dostępności platformy Azure][planning-guide-figure-3000]
 _**rysunek 2:** Ustaw wysoką dostępność serwerów aplikacji SAP w dostępności platformy Azure_

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure][azure-virtual-machines-manage-availability].

Aby uzyskać więcej informacji, zobacz [zestawami dostępności platformy Azure] [ planning-guide-3.2.3] części maszyn wirtualnych platformy Azure planowania i implementacji dla oprogramowania SAP NetWeaver dokumentu.

**Tylko dyski niezarządzane:** Ponieważ konto usługi Azure storage jest potencjalnym pojedynczym punktem awarii, należy mieć co najmniej dwa konta magazynu platformy Azure, w których co najmniej dwie maszyny wirtualne są rozproszone. W idealnym konfiguracji dysków w każdej maszyny wirtualnej, na którym jest uruchomione wystąpienie okna dialogowego SAP będzie można wdrożyć w innego konta magazynu.

> [!IMPORTANT]
> Zdecydowanie zalecamy skorzystanie z usługi Azure managed disks dla instalacji o wysokiej dostępności SAP w. Ponieważ dyski zarządzane automatycznie wyrównane z zestawem dostępności tej maszyny wirtualnej, które są dołączone, zwiększają dostępność maszyny wirtualnej i usługi, które w nim działają.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architektura wysokiej dostępności dla wystąpienia SAP ASCS/SCS na Windows

> ![Windows][Logo_Windows] Windows
>

Aby chronić wystąpienie SAP ASCS/SCS, można użyć rozwiązania usługi WSFC. Rozwiązanie ma dwa warianty:

* **Klaster wystąpienia SAP ASCS/SCS przy użyciu klastrowanego udostępnione dyski**: Aby uzyskać więcej informacji na temat tej architektury, zobacz [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk].   

* **Klaster wystąpienia SAP ASCS/SCS przy użyciu udziału plików**: Aby uzyskać więcej informacji na temat tej architektury, zobacz [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udziału plików][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architektura wysokiej dostępności dla wystąpienia SAP ASCS/SCS w systemie Linux

> ![Linux][Logo_Linux] Linux
> 
> Aby uzyskać więcej informacji na temat klastrów wystąpienia SAP ASCS/SCS przy użyciu struktury klastra z systemem SLES zobacz [wysoką dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP] [ sap-suse-ascs-ha]. Alternatywne architektury wysokiej dostępności w systemie SLES, który nie wymaga o wysokiej dostępności systemu plików NFS, zobacz [przewodnika o wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp dla aplikacji SAP] [ sap-suse-ascs-ha-anf].

Aby uzyskać więcej informacji o klastrach wystąpienia SAP ASCS/SCS przy użyciu struktury klastra firmy Red Hat, zobacz [wysoką dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Oprogramowanie SAP NetWeaver — wiele identyfikatorów SID konfigurację dla klastrowanego wystąpienia SAP ASCS/SCS

> ![Windows][Logo_Windows] Windows
> 
> Obecnie — wiele identyfikatorów SID jest obsługiwana tylko w przypadku usługi WSFC. — Wiele identyfikatorów SID jest obsługiwana przy użyciu udziału plików i udostępnionego dysku.
> 
> Aby uzyskać więcej informacji na temat architektury wysokiej dostępności — wiele identyfikatorów SID Zobacz:

* [SAP ASCS/SCS wystąpienia o wysokiej dostępności — wiele identyfikatorów SID dla systemu Windows Server Failover Clustering i udziału plików][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS wystąpienia o wysokiej dostępności — wiele identyfikatorów SID dla systemu Windows Server Failover Clustering i udostępnionego dysku][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Wystąpienie DBMS wysokiej dostępności

Systemu DBMS również jest pojedynczym punktem kontaktu w systemie SAP. Należy chronić go przy użyciu rozwiązania wysokiej dostępności. Na poniższej ilustracji przedstawiono rozwiązanie o wysokiej dostępności AlwaysOn programu SQL Server na platformie Azure, za pomocą systemu Windows Server Failover Clustering i Azure wewnętrznego modułu równoważenia obciążenia. AlwaysOn programu SQL Server są replikowane plików danych i dziennika systemu DBMS przy użyciu funkcji replikacji swój własny system DBMS. W takim przypadku nie ma potrzeby udostępniony dysk klastra, który upraszcza całą konfigurację.

![Rysunek 3: Przykład wysokiej dostępności SAP DBMS przy użyciu programu SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Rysunek 3:** Przykład wysokiej dostępności SAP DBMS przy użyciu programu SQL Server AlwaysOn_

Aby uzyskać więcej informacji na temat klastrów programu SQL Server DBMS na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager zobacz następujące artykuły:

* [Konfigurowanie zawsze włączonej grupy dostępności na maszynach wirtualnych platformy Azure ręcznie przy użyciu usługi Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurowanie usługi Azure wewnętrznego modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Aby uzyskać więcej informacji dotyczących klastrowania systemu SAP HANA DBMS na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, zobacz [wysoką dostępność środowiska SAP HANA na maszynach wirtualnych Azure (maszyny wirtualne)][sap-hana-ha].
