---
title: Wprowadzenie do rozwiązania SAP na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rozwiązaniach SAP uruchamianych na maszynach wirtualnych (VM) w systemie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7653541a6a66ce5e94119dbb720213a4b40adc81
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303440"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Korzystanie z platformy Azure do hostowania i uruchamiania scenariusze obciążeń SAP
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8
[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b
[deploy-template-cli]:../../../resource-group-template-deploy.md
[deploy-template-portal]:../../../resource-group-template-deploy.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c


[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056
[ha-guide]:sap-high-availability-guide.md
[ha-guide-get-started]:sap-high-availability-guide-start.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktów SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

Dzięki systemowi Azure firmy Microsoft jako partnerem chmurze SAP, jesteś w stanie uruchamiać scenariuszy i obciążeń o znaczeniu krytycznym SAP na platformie skalowalne, zgodne i sprawdzonym działaniu w przedsiębiorstwach.  Korzystaj ze skalowalności, elastyczności i oszczędności platformy Azure. Dzięki rozszerzonemu partnerstwu między firmami Microsoft i SAP umożliwia uruchamianie aplikacji SAP w środowiskach deweloperskich lub testowych oraz produkcyjnych na platformie Azure — i być w pełni obsługiwane. Od oprogramowania SAP NetWeaver SAP S4/HANA, SAP BI, systemu Linux Windows, SQL oprogramowania SAP HANA mamy potrzebne.

Oprócz hostowania oprogramowania SAP NetWeaver scenariuszy obejmujących różne systemy DBMS na platformie Azure, możesz hostować różne inne scenariusze obciążeń SAP, takich jak SAP BI na platformie Azure. Dokumentacja dotyczące wdrożeń oprogramowania SAP NetWeaver na maszynach wirtualnych Azure natywnych znajduje się w sekcji "Oprogramowanie SAP NetWeaver na maszynach wirtualnych platformy Azure".

Platforma Azure oferuje natywne oferty maszyny wirtualnej platformy Azure, które nigdy nie rośnie w ilości zasobów Procesora i pamięci, aby pokrywał korzysta z oprogramowania SAP HANA obciążenia SAP. Aby uzyskać więcej informacji na temat tego obszaru Wyszukaj dokumenty w sekcji SAP HANA na maszynach wirtualnych platformy Azure."

Unikatowość platformy Azure dla oprogramowania SAP HANA jest unikatowy oferta, która ustawia Azure oprócz konkurencji. Aby włączyć hostowanie więcej pamięci i zasobów procesora CPU w wymagających scenariuszach SAP obejmujących platformy SAP HANA, platforma Azure oferuje użycie klienta dedykowanego sprzętu bez systemu operacyjnego na potrzeby uruchamiania wdrożenia oprogramowania SAP HANA, które wymagają do 20 TB (60 TB skalowalnego w poziomie) pamięci dla S/4HANA lub innych obciążeń SAP HANA. To unikatowe rozwiązanie platformy Azure środowiska SAP Hana na platformie Azure (duże wystąpienia) umożliwia uruchamianie oprogramowania SAP HANA na dedykowanym sprzęcie bez systemu operacyjnego w warstwie aplikacji SAP lub warstwy pośredniczącym obciążenia hostowane natywnych maszynach wirtualnych platformy Azure. To rozwiązanie jest udokumentowany w różnych dokumentach w sekcji "Platformy SAP HANA na platformie Azure (duże wystąpienia)."   

Wymagania dotyczące integracji tożsamości i logowanie jednokrotne przy użyciu usługi Azure Active Directory do różnych składników SAP i SAP SaaS również hostingu scenariusze obciążeń SAP na platformie Azure można utworzyć lub PaaS oferuje. Listę takich integracji i scenariusze logowanie jednokrotne z jednostkami usługi Azure Active Directory (AAD) i SAP jest opisane i opisano w sekcji "Integracja tożsamości usługi AAD SAP oraz logowanie jednokrotne."

## <a name="latest-changes"></a>Najnowsze zmiany

Dokumentacja wokół SAP HANA dynamiczna obsługa warstw na maszynach wirtualnych platformy Azure

- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#sap-hana-dynamic-tiering-20-for-azure-virtual-machines)

Dokumentacja wokół oprogramowania SAP HANA skalowalnego w poziomie na M128s maszyny Wirtualnej platformy Azure masz dodane do:

- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)
- [SAP HANA dostępności w obrębie jednego regionu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>Oprogramowanie SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia)

Seria dokumentacji prowadzi za pośrednictwem platformy SAP HANA na platformie Azure (duże wystąpienia) lub w krótkich dużych wystąpień HANA. Dokumenty przekazywać uwzględnione na liście obszarów dużych wystąpień HANA:

- [Omówienie środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura i łączność do platformy SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalowanie platformy SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Wysoka dostępność i odzyskiwanie po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Rozwiązywanie problemów i monitorowania środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Następne kroki:

- Odczyt [omówienie i architektura oprogramowania SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Oprogramowanie SAP HANA na maszynach wirtualnych platformy Azure
Ten rozdział dokumentacji opisano różne aspekty platformy SAP HANA. Jako warunek wstępny należy zapoznać się z zasadą usługi Azure świadczące podstawowe usługi w modelu IaaS platformy Azure. więc przede wszystkim wiedzę na temat usługi Azure compute, magazynu i sieci. Wiele z tych tematów są obsługiwane w oprogramowanie SAP NetWeaver powiązane [Azure Planning Guide](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/planning-guide). 

Spoza tej listy, artykuły i artykułów podrzędnych spadkobiercy składa się z dokumentacją specyficzną dla platformy HANA na platformie Azure:

- [Szybki Start: Instalacja ręczna jednego wystąpienia środowiska SAP Hana na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-get-started)
- [Wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA dostępności w obrębie jednego regionu platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Dostępność platformy SAP HANA w regionach platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Wysoka dostępność środowiska SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA usługi Azure Backup na poziomie plików](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Oprogramowanie SAP NetWeaver na maszynach wirtualnych Azure wdrożone

### <a name="deploy-sap-ides-system-on-windows-and-sql-server-through-sap-cal-on-azure"></a>Wdrożenia systemu SAP IDES na Windows i program SQL Server za pośrednictwem SAP CAL na platformie Azure
Tytuł: Testowanie środowiska SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure

Podsumowanie: Ten dokument zawiera opis wdrażania systemu SAP IDES na podstawie Windows i program SQL Server na platformie Azure przy użyciu biblioteki SAP Cloud Appliance Library. SAP Cloud appliance Library jest usług SAP, który umożliwia wdrożenie produktów SAP na platformie Azure. W tym dokumencie omówiono krok po kroku wdrażania systemu SAP IDES. System środowisk IDE jest tylko przykładem kilka innych aplikacji kilkudziesięciu, które można wdrożyć za pomocą urządzenia w chmurze SAP na Microsoft Azure.


[Ten przewodnik można znaleźć tutaj](cal-ides-erp6-erp7-sp3-sql.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Przewodnik Szybki Start do środowiska NetWeaver w systemie SUSE Linux na platformie Azure
Tytuł: Testowanie środowiska SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure

Podsumowanie: W tym artykule opisano różne kwestii, które należy wziąć pod uwagę podczas pracy w oprogramowanie SAP NetWeaver na maszynach wirtualnych Microsoft Azure w systemie SUSE Linux (VM). Oprogramowanie SAP NetWeaver jest oficjalnie obsługiwana na maszynach wirtualnych systemu SUSE Linux na platformie Azure. Wszystkie szczegółowe informacje dotyczące wersji systemu Linux, wersje jądra SAP i inne szczegóły można znaleźć w 1928533 Uwaga SAP "aplikacji SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure".


[Ten przewodnik można znaleźć tutaj](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>Planowanie i wdrażanie
Nazwa: Maszyny wirtualne platformy Azure planowania i implementacji środowiska SAP NetWeaver

Podsumowanie: Ten dokument jest przewodnik zaczynać Jeśli myślisz o uruchamianiu oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure. Ten podręcznik planowania i implementacji pomoże Ci ocenić, czy istniejący lub planowany oparte na oprogramowanie SAP NetWeaver system może zostać wdrożony w środowisku usługi Azure Virtual Machines. Obejmuje on wiele scenariuszy wdrażania oprogramowania SAP NetWeaver i zawiera konfiguracje rozwiązań SAP specyficzne dla platformy Azure. W dokumencie wymieniono i opisano wszystkie niezbędne informacje o konfiguracji potrzebne do uruchomienia hybrydowego środowiska SAP na stronie SAP/Azure. Omówiono również działania, które można podjąć w celu zapewnienia wysokiej dostępności systemów opartych na oprogramowaniu SAP NetWeaver działających na platformie IaaS.



[Ten przewodnik można znaleźć tutaj][planning-guide]

### <a name="high-availability-configurations-of-sap-netweaver-in-azure-vms"></a>Konfiguracje wysokiej dostępności oprogramowanie SAP NetWeaver na maszynach wirtualnych Azure
Tytuł: Maszyn wirtualnych wysokiej dostępności dla oprogramowania SAP NetWeaver

Podsumowanie: W tym dokumencie omówione czynności, które można wykonać w celu wdrażania systemów SAP o wysokiej dostępności na platformie Azure za pomocą modelu wdrażania usługi Azure Resource Manager. W jaki sposób można za pomocą tych głównych zadań. W dokumencie opisano jak pojedynczego punktu z awarii składników takich jak Advanced Business Application programowania (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) i systemy zarządzania bazami danych (DBMS) oraz nadmiarowych elementów, takich jak SAP Serwer aplikacji mają być chronione, podczas uruchamiania na maszynach wirtualnych Azure. Przykład krok po kroku instalacji i konfiguracji systemu SAP o wysokiej dostępności w klastrze systemu Windows Server Failover Clustering i SUSE Linux Enterprise Server klaster Framework na platformie Azure jest przedstawiona i opisane w tym dokumencie.



[Ten przewodnik można znaleźć tutaj][ha-guide-get-started]

### <a name="realizing-multi-sid-deployments-of-sap-netweaver-in-azure-vms"></a>Wdrażanie — wiele identyfikatorów SID wdrożenia oprogramowania SAP NetWeaver na maszynach wirtualnych Azure
Tytuł: Tworzenie konfiguracji z wieloma identyfikatorami SID oprogramowanie SAP NetWeaver

Podsumowanie: Ten dokument jest dodatkiem do dokumentu, wysoką dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure. Ze względu na nową funkcję na platformie Azure, który został wprowadzony we wrześniu 2016 to można wdrożyć wiele wystąpień SAP NetWeaver ASCS/SCS para maszyn wirtualnych platformy Azure. Dzięki takiej konfiguracji można ograniczyć liczbę niezbędnych do wdrożenia maszyn wirtualnych do osiągnięcia wysokiej dostępności konfiguracje SAP NetWeaver. W tym przewodniku opisano ustawienia takie konfiguracje — wiele identyfikatorów SID.



[Ten przewodnik można znaleźć tutaj](high-availability-multi-sid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>Wdrożenie oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure
Tytuł: Wdrażanie maszyn wirtualnych platformy Azure oprogramowanie SAP NetWeaver

Podsumowanie: Ten dokument zawiera wskazówki proceduralne dotyczące wdrażania oprogramowania SAP NetWeaver na maszynach wirtualnych na platformie Azure. Dokument koncentruje się na trzech konkretnych scenariuszach wdrażania, z naciskiem na włączanie rozszerzeń monitorowania platformy Azure dla oprogramowania SAP, w tym zalecenia dotyczące rozwiązywania problemów dotyczących rozszerzeń monitorowania platformy Azure dla oprogramowania SAP. W tym dokumencie przyjęto założenie, że użytkownik przeczytał Podręcznik planowania i implementacji.



[Ten przewodnik można znaleźć tutaj][deployment-guide]

### <a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>Przewodnik wdrażania systemu DBMS
Tytuł: Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver

Podsumowanie: W tym dokumencie opisano zagadnienia dotyczące planowania i wdrażania systemów DBMS, które powinny być uruchamiane w połączeniu z oprogramowaniem SAP. W pierwszej części wymieniono i przedstawiono zagadnienia ogólne. Kolejne części dokumentu dotyczą wdrożeń różnych systemów DBMS na platformie Azure obsługiwanych przez oprogramowanie SAP. Różne przedstawione systemy DBMS obejmują program SQL Server, SAP ASE i Oracle. W tych konkretnych częściach zostały omówione zagadnienia, które należy uwzględnić dla przypadku uruchamiania systemów SAP na platformie Azure w połączeniu z tymi systemami DBMS. Tematy takie jak metody tworzenia kopii zapasowej i zapewniania wysokiej dostępności obsługiwane przez różne systemy DBMS na platformie Azure są przedstawione do użycia z aplikacjami SAP.



[Ten przewodnik można znaleźć tutaj][dbms-guide]

### <a name="using-azure-site-recovery-for-sap-workload"></a>Za pomocą usługi Azure Site Recovery w przypadku obciążeń SAP
Tytuł: Oprogramowanie SAP NetWeaver: tworzenie rozwiązania odzyskiwania po awarii przy użyciu usługi Azure Site Recovery

Podsumowanie: W tym dokumencie opisano sposób, jak używać usługi Azure Site Recovery na potrzeby obsługi scenariuszy odzyskiwania po awarii. Przypadki, w którym Azure jest używana jako lokalizacji odzyskiwania danych po awarii dla środowisko SAP w środowisku lokalnym za pomocą usług Azure Site Recovery. Inny scenariusz opisany w dokumencie jest w przypadku odzyskiwania po awarii Azure – Azure (A2A) i jak jest zarządzana przy użyciu usługi Azure Site Recovery.  



[Ten przewodnik można znaleźć tutaj](http://aka.ms/asr-sap)
