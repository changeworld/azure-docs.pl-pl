---
title: 'SAP na platformie Azure: Przewodnik planowania i wdrażania'
description: Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ddcc5165f5588ff9015d7fafbc2b822268ffea7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337163"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver

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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
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
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

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

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Platforma Microsoft Azure umożliwia firmom nabywanie zasobów obliczeniowych i magazynowych w minimalnym czasie bez długich cykli zaopatrzenia. Usługa Azure Virtual Machine umożliwia firmom wdrażanie klasycznych aplikacji, takich jak aplikacje oparte na SAP NetWeaver na platformie Azure, oraz rozszerzanie ich niezawodności i dostępności bez konieczności posiadania dodatkowych zasobów dostępnych lokalnie. Usługi maszyny wirtualnej platformy Azure obsługują również łączność między środowiskami lokalnymi, co umożliwia firmom aktywną integrację maszyn wirtualnych platformy Azure z ich domenami lokalnymi, chmurami prywatnymi i krajobrazem systemu SAP.
W tym opracowaniu w tym opracowaniu tym artykule opisano podstawy maszyny wirtualnej platformy Microsoft Azure i przedstawiono zagadnienia dotyczące planowania i wdrażania instalacji SAP NetWeaver na platformie Azure i jako takie powinny być dokumentem do przeczytania przed rozpoczęciem rzeczywistego wdrożenia sap NetWeaver na platformie Azure.
Dokument uzupełnia dokumentację instalacyjną SAP i notatki SAP, które reprezentują podstawowe zasoby dotyczące instalacji i wdrożeń oprogramowania SAP na danych platformach.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Podsumowanie
Cloud Computing to powszechnie używany termin, który zyskuje coraz większe znaczenie w branży IT, od małych firm po duże i międzynarodowe korporacje.

Microsoft Azure to platforma usług w chmurze firmy Microsoft, która oferuje szerokie spektrum nowych możliwości. Teraz klienci są w stanie szybko aprowizować i wyrównywać aplikacje jako usługę w chmurze, więc nie są one ograniczone do ograniczeń technicznych lub budżetowania. Zamiast inwestować czas i budżet w infrastrukturę sprzętową, firmy mogą skupić się na aplikacji, procesach biznesowych i jej korzyściach dla klientów i użytkowników.

Wraz z usługami Microsoft Azure Virtual Machines firma Microsoft oferuje kompleksową platformę typu infrastruktura jako usługa (IaaS). Aplikacje oparte na oprogramowaniu SAP NetWeaver są obsługiwane w usłudze Azure Virtual Machines (IaaS). W tym opracowaniu zawierającym oficjalny opis sposobu planowania i implementowania aplikacji opartych na systemie SAP NetWeaver na platformie Microsoft Azure jako wybranej platformy.

Sam dokument koncentruje się na dwóch głównych aspektach:

* Pierwsza część opisuje dwa obsługiwane wzorce wdrażania aplikacji opartych na SAP NetWeaver na platformie Azure. Opisano w nim również ogólną obsługę platformy Azure z myślą o wdrożeniach SAP.
* Druga część zawiera szczegółowe informacje na temat wdrażania różnych scenariuszy opisanych w pierwszej części.

Aby uzyskać dodatkowe zasoby, zobacz rozdział [Zasoby][planning-guide-1.2] w tym dokumencie.

### <a name="definitions-upfront"></a>Definicje z góry
W całym dokumencie używamy następujących terminów:

* IaaS: Infrastruktura jako usługa
* PaaS: Platforma jako usługa
* SaaS: Oprogramowanie jako usługa
* Komponent SAP: indywidualna aplikacja SAP, taka jak ECC, BW, Solution Manager lub S/4HANA.  Składniki SAP mogą być oparte na tradycyjnych technologiach ABAP lub Java lub aplikacji nienapartej na NetWeaver, takiej jak Obiekty biznesowe.
* Środowisko SAP: jeden lub więcej składników SAP logicznie pogrupowane w celu wykonania funkcji biznesowej, takiej jak programowanie, qas, szkolenie, dr lub produkcja.
* Sap Landscape: Termin ten odnosi się do całych zasobów SAP w krajobrazie IT klienta. Krajobraz SAP obejmuje wszystkie środowiska produkcyjne i nieprodukcyjne.
* Sap System: Połączenie warstwy DBMS i warstwy aplikacji, na przykład, systemu rozwoju SAP ERP, systemu testowego SAP BW, systemu produkcyjnego SAP CRM itp. W wdrożeniach platformy Azure nie jest obsługiwane dzielenie tych dwóch warstw między lokalnie i platformy Azure. Oznacza, że system SAP jest wdrażany lokalnie lub jest wdrażany na platformie Azure. Można jednak wdrożyć różne systemy środowiska SAP na platformie Azure lub lokalnie. Na przykład można wdrożyć sap CRM rozwoju i testowania systemów na platformie Azure, ale system produkcyjny SAP CRM lokalnie.
* Międzylokacyjny lub hybrydowy: Opisuje scenariusz, w którym maszyny wirtualne są wdrażane w ramach subskrypcji platformy Azure, która ma łączność lokacja-lokacja, wiele lokacji lub usługi ExpressRoute między lokalnymi centrami danych a platformą Azure. We wspólnej dokumentacji platformy Azure tego rodzaju wdrożenia są również opisane jako scenariusze międzylokacyjnymi lub hybrydowymi. Powodem połączenia jest rozszerzenie domen lokalnych, lokalnej usługi Active Directory/OpenLDAP i lokalnego systemu DNS na platformę Azure. Lokalny krajobraz jest rozszerzany na zasoby platformy Azure subskrypcji. Po tym rozszerzeniu maszyny wirtualne mogą być częścią domeny lokalnej. Użytkownicy domeny lokalnej mogą uzyskiwać dostęp do serwerów i uruchamiać usługi na tych maszynach wirtualnych (takich jak usługi DBMS). Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożonymi lokalnie i wdrożonymi maszynami wirtualnymi platformy Azure jest możliwe. Jest to najbardziej typowy i prawie ekskluzywny przypadek wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [ten][vpn-gateway-cross-premises-options] artykuł i [ten][vpn-gateway-site-to-site-create].
* Rozszerzenie monitorowania platformy Azure, rozszerzone monitorowanie i rozszerzenie platformy Azure dla sap: opisz jeden i ten sam element. Opisano w nim rozszerzenie maszyny Wirtualnej, które musi zostać wdrożone przez Ciebie, aby dostarczyć niektóre podstawowe dane dotyczące infrastruktury platformy Azure agentowi hosta SAP. SAP w uwagach SAP może odnosić się do niego jako rozszerzenie monitorowania lub rozszerzone monitorowanie. Na platformie Azure odnosimy się do niego jako **rozszerzenia platformy Azure dla SAP.**

> [!NOTE]
> Wdrożenia międzylokacyjne lub hybrydowe systemów SAP, w których maszyny wirtualne platformy Azure z systemami SAP są członkami domeny lokalnej, są obsługiwane w systemach SAP w produkcji. Konfiguracje międzylokacyjne lub hybrydowe są obsługiwane w celu wdrażania części lub kompletnych środowisk SAP na platformie Azure. Nawet uruchomienie pełnego środowiska SAP na platformie Azure wymaga, aby te maszyny wirtualne były częścią domeny lokalnej i ADS/OpenLDAP. 
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Resources
Punkt wejścia dla obciążenia SAP w dokumentacji platformy Azure znajduje się [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Począwszy od tego punktu wejścia można znaleźć wiele artykułów, które obejmują tematy:

- SAP NetWeaver i Business One na platformie Azure
- Przewodniki SAP DBMS dla różnych systemów DBMS na platformie Azure
- Wysoka dostępność i odzyskiwanie po awarii dla obciążenia SAP na platformie Azure
- Wskazówki dotyczące uruchamiania usługi SAP HANA na platformie Azure
- Wskazówki dotyczące dużych wystąpień platformy Azure HANA dla systemu SAP HANA DBMS 


> [!IMPORTANT]
> W miarę możliwości stosuje się link do przewodników instalacji SAP lub innej dokumentacji SAP <http://service.sap.com/instguides>(Reference InstGuide-01, patrz ). Jeśli chodzi o wymagania wstępne, proces instalacji lub szczegóły dotyczące określonych funkcji SAP, dokumentacja i przewodniki SAP powinny być zawsze uważnie odczytywane, ponieważ dokumenty firmy Microsoft obejmują tylko określone zadania dla oprogramowania SAP zainstalowanego i obsługiwanego w Maszyna wirtualna platformy Microsoft Azure.
>
>

Następujące uwagi SAP są związane z tematem SAP na platformie Azure:

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i rozmiary |
| [2015553] |SAP na platformie Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z rozszerzonym monitorowaniem platformy Azure dla systemu SAP |
| [2178632] |Metryki monitorowania kluczy dla sap na platformie Microsoft Azure |
| [1409604] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2191498] |SAP na Linuksie z platformą Azure: Ulepszone monitorowanie |
| [2243692] |Maszyna wirtualna systemu Linux na platformie Microsoft Azure (IaaS): problemy z licencją SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalacja i aktualizacja |
| [2069760] |Instalacja i uaktualnienie systemu Oracle Linux 7.x SAP |
| [1597355] |Zalecenie dotyczące przestrzeni wymiany dla systemu Linux |

Przeczytaj także [SCN Wiki,](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) która zawiera wszystkie notatki SAP dla Linuksa.

Ogólne ograniczenia domyślne i maksymalne ograniczenia subskrypcji platformy Azure można znaleźć w [tym artykule][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Możliwe scenariusze
Sap jest często postrzegany jako jedna z najbardziej krytycznych aplikacji w przedsiębiorstwach. Architektura i operacje tych aplikacji jest w większości skomplikowane i zapewnienie, że spełniają wymagania dotyczące dostępności i wydajności jest ważne.

W związku z tym przedsiębiorstwa muszą dokładnie przemyśleć, który dostawca chmury wybrać do prowadzenia takich procesów biznesowych o krytycznym znaczeniu dla firmy. Platforma Azure to idealna platforma chmury publicznej dla krytycznych dla biznesu aplikacji SAP i procesów biznesowych. Biorąc pod uwagę szeroką gamę infrastruktury platformy Azure, prawie wszystkie istniejące systemy SAP NetWeaver i S/4HANA mogą być obecnie hostowane na platformie Azure. Platforma Azure udostępnia maszynom wirtualnym wiele terabajtów pamięci i ponad 200 procesorów. Poza tym platforma Azure oferuje [duże wystąpienia HANA,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)które umożliwiają skalowanie wdrożeń HANA o rozmiarze do 24 TB i skalowaniu SAP HANA w poziomie do 120 TB. Można dziś stwierdzić, że prawie wszystkie scenariusze lokalnego SAP można uruchomić również na platformie Azure. 

Aby uzyskać przybliżony opis scenariuszy i niektórych scenariuszy nieobjętych obsługą, zobacz [dokument obciążenia SAP na komputerze wirtualnym platformy Azure obsługiwane scenariusze](./sap-planning-supported-configurations.md).

Sprawdź te scenariusze i niektóre warunki, które zostały nazwane jako nie są obsługiwane w dokumentacji, do której istnieje odwołanie, podczas planowania i rozwoju architektury, którą chcesz wdrożyć na platformie Azure.

Ogólnie rzecz biorąc, najczęstszym wzorcem wdrażania jest scenariusz między lokalami, taki jak

![SIEĆ VPN z łącznością lokacja-lokacja (między lokalizacjami)][planning-guide-figure-300]

Powodem dla wielu klientów do zastosowania wzorca wdrażania między środowiskami lokalnych jest fakt, że jest najbardziej przejrzysty dla wszystkich aplikacji, aby rozszerzyć lokalnie na platformę Azure przy użyciu usługi Azure ExpressRoute i traktować platformę Azure jako wirtualne centrum danych. W miarę jak coraz więcej zasobów jest przenoszonych na platformę Azure, infrastruktura i infrastruktura sieciowa wdrożona na platformie Azure będą się rozwijać, a zasoby lokalne będą odpowiednio zmniejszać. Wszystko przejrzyste dla użytkowników i aplikacji.

Aby pomyślnie wdrożyć systemy SAP w usłudze Azure IaaS lub IaaS w ogóle, ważne jest, aby zrozumieć znaczne różnice między ofertami tradycyjnych zleceniodawców lub hostów i ofert IaaS. Podczas gdy tradycyjny hoster lub zleceniodawca dostosowuje infrastrukturę (sieć, pamięć masową i typ serwera) do obciążenia, które klient chce hostować, zamiast tego klient lub partner jest odpowiedzialny za scharakteryzowanie obciążenia i wybranie odpowiedniej platformy Azure składniki maszyn wirtualnych, magazynu i sieci dla wdrożeń IaaS.

Aby zebrać dane do planowania wdrożenia na platformie Azure, ważne jest, aby:

- Oceń, jakie produkty SAP są obsługiwane na maszynach wirtualnych platformy Azure
- Oceń, jakie wersje określonych systemów operacyjnych są obsługiwane przez określone maszyny wirtualne platformy Azure dla tych produktów SAP
- Oceń, jakie wersje DBMS są obsługiwane dla twoich produktów SAP za pomocą określonych maszyn wirtualnych platformy Azure
- Oceń, czy niektóre z wymaganych wersji systemu operacyjnego/dbms wymagają wykonania wersji SAP, uaktualnienia pakietu pomocy technicznej i jądra, aby przejść do obsługiwanej konfiguracji
- Oceń, czy musisz przejść do różnych systemów operacyjnych, aby wdrożyć je na platformie Azure.

Szczegółowe informacje na temat obsługiwanych składników SAP na platformie Azure, obsługiwanych jednostek infrastruktury platformy Azure i powiązanych wersji systemów operacyjnych oraz wersji DBMS zostały wyjaśnione w artykule [Jakie oprogramowanie SAP jest obsługiwane dla wdrożeń platformy Azure.](./sap-supported-product-on-azure.md) Wyniki uzyskane z oceny prawidłowych wersji SAP, systemu operacyjnego i wersji DBMS mają duży wpływ na wysiłki związane z przenoszeniem systemów SAP na platformę Azure. Wyniki tej oceny będą określać, czy mogą być znaczące wysiłki na rzecz przygotowania w przypadkach, gdy potrzebne są uaktualnienia lub zmiany systemów operacyjnych firmy SAP.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiony platformy Azure
Usługi platformy Azure firmy Microsoft są zbierane w regionach platformy Azure. Region platformy Azure to jeden lub zbiór z centrów danych, które zawierają sprzęt i infrastrukturę, która uruchamia i obsługuje różne usługi platformy Azure. Ta infrastruktura obejmuje dużą liczbę węzłów, które działają jako węzły obliczeniowe lub węzły magazynu lub uruchamiają funkcje sieciowe. 

Aby uzyskać listę różnych regionów platformy Azure, zapoznaj się z artykułem [Geografie platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Nie wszystkie regiony platformy Azure oferują te same usługi. W zależności od produktu SAP, który chcesz uruchomić, a system operacyjny i system dbms z nim związane, może skończyć się w sytuacji, że określony region nie oferuje typy maszyn wirtualnych, które są wymagane. Jest to szczególnie ważne w przypadku uruchamiania sap HANA, gdzie zwykle potrzebujesz maszyn wirtualnych z serii M/Mv2 VM. Te rodziny maszyn wirtualnych są wdrażane tylko w podzbiorze regionów. Możesz dowiedzieć się, jaka dokładna maszyna wirtualna, typy, typy magazynu platformy Azure lub inne usługi platformy Azure są dostępne w których regionach za pomocą witryny [Produkty dostępne według regionu.](https://azure.microsoft.com/global-infrastructure/services/) Po rozpoczęciu planowania i mieć pewne regiony na uwadze jako regionu podstawowego i ostatecznie regionu pomocniczego, należy najpierw zbadać, czy niezbędne usługi są dostępne w tych regionach. 

### <a name="availability-zones"></a>Strefy dostępności
Kilka regionów platformy Azure zaimplementowano koncepcję o nazwie Strefy dostępności. Strefy dostępności są fizycznie oddzielne lokalizacje w regionie platformy Azure. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Na przykład wdrażanie dwóch maszyn wirtualnych w dwóch strefach dostępności platformy Azure i implementowanie struktury wysokiej dostępności dla systemu SAP DBMS lub usług SAP Central zapewnia najlepszą usługę SLA na platformie Azure. W przypadku tej konkretnej umowy SLA maszyny wirtualnej na platformie Azure sprawdź najnowszą wersję [umowy SLA maszyny wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Ponieważ regiony platformy Azure opracowane i rozszerzone szybko w ciągu ostatnich lat, topologia regionów platformy Azure, liczba fizycznych centrów danych, odległość między tymi centrami danych i odległość między strefami dostępności platformy Azure mogą być różne. A wraz z tym opóźnienie sieci.

Zasada stref dostępności nie ma zastosowania do usługi szczegółowej HANA [hana dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Umowy dotyczące poziomu usług dla dużych wystąpień HANA można znaleźć w artykule [Umowa SLA dla SAP HANA na dużych wystąpieniach platformy Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domeny błędów
Domeny błędów reprezentują fizyczną jednostkę awarii, ściśle związaną z infrastrukturą fizyczną zawartą w centrach danych i chociaż fizyczny blok lub stojak można uznać za domenę błędów, nie ma bezpośredniego mapowania jeden do jednego między nimi.

Podczas wdrażania wielu maszyn wirtualnych w ramach jednego systemu SAP w usługach maszyn wirtualnych platformy Microsoft Azure można mieć wpływ na kontroler sieci szkieletowej platformy Azure w celu wdrożenia aplikacji w różnych domenach błędów, spełniając tym samym wyższe wymagania dostępności SLA. Jednak dystrybucja domen błędów za pośrednictwem jednostki skalowania platformy Azure (kolekcja setek węzłów obliczeniowych lub węzłów magazynu i sieci) lub przypisanie maszyn wirtualnych do określonej domeny błędów jest czymś, nad czym nie masz bezpośredniej kontroli. Aby skierować kontroler sieci szkieletowej platformy Azure do wdrożenia zestawu maszyn wirtualnych w różnych domenach błędów, należy przypisać dostępność platformy Azure ustawioną na maszyny wirtualne w czasie wdrażania. Aby uzyskać więcej informacji na temat zestawów dostępności platformy Azure, zobacz rozdział [zestawy dostępności platformy Azure][planning-guide-3.2.3] w tym dokumencie.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Uaktualnij domeny
Domeny uaktualnienia reprezentują jednostkę logiczną, która pomaga określić sposób aktualizowanie maszyny wirtualnej w systemie SAP, który składa się z wystąpień SAP uruchomionych na wielu maszynach wirtualnych. Po wystąpieniu uaktualnienia platforma Microsoft Azure przechodzi przez proces aktualizowania tych domen uaktualnienia jeden po drugim. Rozprzestrzeniając maszyny wirtualne w czasie wdrażania w różnych domenach uaktualnienia, można chronić system SAP częściowo przed potencjalnymi przestojami. Aby wymusić na platformie Azure wdrożenie maszyn wirtualnych systemu SAP rozłożonych na różne domeny uaktualnień, należy ustawić określony atrybut w czasie wdrażania każdej maszyny Wirtualnej. Podobnie jak w przypadku domen błędów, jednostka skalowania platformy Azure jest podzielona na wiele domen uaktualnienia. Aby skierować kontroler sieci szkieletowej platformy Azure do wdrożenia zestawu maszyn wirtualnych w różnych domenach uaktualnień, należy przypisać zestaw dostępności platformy Azure do maszyn wirtualnych w czasie wdrażania. Aby uzyskać więcej informacji na temat zestawów dostępności platformy Azure, zobacz rozdział [zestawy dostępności platformy Azure][planning-guide-3.2.3] poniżej.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Zestawy dostępności platformy Azure
Maszyny wirtualne platformy Azure w ramach jednego zestawu dostępności platformy Azure są dystrybuowane przez kontroler sieci szkieletowej platformy Azure w różnych domenach błędów i uaktualnień. Celem dystrybucji w różnych domenach błędów i uaktualnień jest zapobieganie zamykaniu wszystkich maszyn wirtualnych systemu SAP w przypadku konserwacji infrastruktury lub awarii w jednej domenie błędów. Domyślnie maszyny wirtualne nie są częścią zestawu dostępności. Udział maszyny Wirtualnej w zestawie dostępności jest definiowany w czasie wdrażania lub później przez ponowną konfigurację i ponowne wdrożenie maszyny Wirtualnej.

Aby zrozumieć pojęcie zestawów dostępności platformy Azure i sposób, w jaki zestawy dostępności odnoszą się do domen błędów i uaktualnień, przeczytaj [ten artykuł][virtual-machines-manage-availability]. 

Podczas definiowania zestawów dostępności i próby mieszania różnych maszyn wirtualnych z różnych rodzin maszyn wirtualnych w ramach jednego zestawu dostępności, mogą wystąpić problemy, które uniemożliwiają włączenie określonego typu maszyny Wirtualnej do takiego zestawu dostępności. Powodem jest to, że zestaw dostępności jest powiązany ze skalowaniem jednostki zawierającej określony typ hostów obliczeniowych. A określony typ hosta obliczeniowego może uruchamiać tylko niektóre typy rodzin maszyn wirtualnych. Na przykład jeśli utworzysz zestaw dostępności i wdrożysz pierwszą maszynę wirtualną w zestawie dostępności i wybierzesz typ maszyny Wirtualnej z rodziny Esv3, a następnie spróbujesz wdrożyć jako drugą maszynę wirtualną z rodziny M, zostaniesz odrzucony w drugiej alokacji. Powodem jest to, że maszyny wirtualne z rodziny Esv3 nie są uruchomione na tym samym sprzęcie hosta, co maszyny wirtualne z rodziny M. Ten sam problem może wystąpić, podczas próby zmiany rozmiaru maszyn wirtualnych i spróbuj przenieść maszynę wirtualną z rodziny Esv3 do typu maszyny Wirtualnej z rodziny M. W przypadku zmiany rozmiaru do rodziny maszyn wirtualnych, które nie mogą być hostowane na tym samym sprzęcie hosta, należy zamknąć wszystkie maszyny wirtualne w zestawie dostępności i zmienić ich rozmiar, aby móc działać na innym typie komputera hosta. W przypadku zestawów SLA maszyn wirtualnych wdrożonych w ramach zestawu dostępności zapoznaj się z artykułem [Ujeżdona ujeżdne instaly sla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

Zasada zestawu dostępności i powiązanej aktualizacji i domeny błędów nie ma zastosowania do określonej usługi [HANA dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Umowy dotyczące poziomu usług dla dużych wystąpień HANA można znaleźć w artykule [Umowa SLA dla SAP HANA na platformie Azure Dużych Wystąpień.](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 

> [!IMPORTANT]
> Pojęcia strefy dostępności platformy Azure i zestawy dostępności platformy Azure wzajemnie się wykluczają. Oznacza to, że można wdrożyć parę lub wiele maszyn wirtualnych w określonej strefie dostępności lub zestaw dostępności platformy Azure. Ale nie jedno i drugie.


## <a name="azure-virtual-machine-services"></a>Usługi maszyn wirtualnych platformy Azure
Platforma Azure oferuje szeroką gamę maszyn wirtualnych, które można wybrać do wdrożenia. Nie ma potrzeby zakupu technologii z góry i infrastruktury. Usługa azure VM upraszcza obsługę i obsługę aplikacji, zapewniając zasoby obliczeniowe i magazynowe na żądanie do hostowania, skalowania i zarządzania aplikacjami sieci Web i połączonymi aplikacjami. Zarządzanie infrastrukturą jest zautomatyzowane za pomocą platformy, która jest przeznaczona do wysokiej dostępności i dynamicznego skalowania, aby dopasować potrzeby użycia z opcją kilku różnych modeli cenowych.

![Pozycjonowanie usług maszyn wirtualnych platformy Microsoft Azure][planning-guide-figure-400]

Dzięki maszynom wirtualnym platformy Azure firma Microsoft umożliwia wdrażanie niestandardowych obrazów serwerów na platformie Azure jako wystąpienia usługi IaaS. Możesz też wybrać z galerii obrazów obrazów systemu operacyjnego platformy Azure bogaty wybór obrazów z materiałów eksploatacyjnych.

Z operacyjnego punktu widzenia usługa maszyny wirtualnej platformy Azure oferuje podobne środowisko jak maszyny wirtualne wdrożone lokalnie. Użytkownik jest odpowiedzialny za administrowanie, operacje, a także poprawki określonego systemu operacyjnego, uruchomione na maszynie Wirtualnej platformy Azure i jej aplikacji w tej maszynie wirtualnej. Firma Microsoft nie świadczy więcej usług poza hosting tej maszyny Wirtualnej na swojej infrastrukturze platformy Azure (Infrastruktura jako usługa — IaaS). W przypadku obciążenia SAP, które można wdrożyć jako klient, firma Microsoft nie ma ofert poza ofertą IaaS. 

Platforma Microsoft Azure jest platformą wielodostępną. W rezultacie zasoby magazynu, sieci i obliczeń, które hostują maszyny wirtualne platformy Azure są, z kilkoma wyjątkami, współużytkowane przez dzierżawców. Inteligentna logika ograniczania przepustowości i przydziału jest używana, aby zapobiec wpływowi jednego dzierżawcy na wydajność innego dzierżawcy (hałaśliwego sąsiada) w drastyczny sposób. Specjalnie w przypadku certyfikacji platformy Azure dla sap HANA firma Microsoft musi udowodnić izolację zasobów w przypadkach, gdy wiele maszyn wirtualnych można regularnie uruchamiać na tym samym hoście do sap. Chociaż logika na platformie Azure stara się zachować różnice w przepustowości doświadczonych małych, wysoce współużytkowane platformy mają tendencję do wprowadzania większych odchyleń w dostępności zasobów/przepustowości niż klienci mogą wystąpić w ich wdrożeniach lokalnych. Należy wziąć pod uwagę prawdopodobieństwo, że system SAP na platformie Azure może wystąpić większe odchylenia niż w systemie lokalnym.

### <a name="azure-virtual-machines-for-sap-workload"></a>Maszyny wirtualne platformy Azure dla obciążenia SAP

W przypadku obciążenia SAP zawęziliśmy wybór do różnych rodzin maszyn wirtualnych, które są odpowiednie dla obciążenia SAP i obciążenia SAP HANA w szczególności. Sposób, w jaki można znaleźć odpowiedni typ maszyny Wirtualnej i jego zdolność do pracy za pośrednictwem obciążenia SAP jest opisany w dokumencie [Jakie oprogramowanie SAP jest obsługiwane dla wdrożeń platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 

> [!NOTE]
> Typy maszyn wirtualnych, które są certyfikowane dla obciążenia SAP, nie ma nadmiernej obsługi administracyjnej zasobów procesora CPU i pamięci.

Oprócz wyboru wyłącznie obsługiwanych maszyn wirtualnych, należy również sprawdzić, czy są dostępne w określonym regionie na podstawie [witryny Produkty dostępne dla regionu.](https://azure.microsoft.com/global-infrastructure/services/) Ale co ważniejsze, musisz ocenić, czy:

- Zasoby procesora i pamięci różnych typów maszyn wirtualnych 
- Przepustowość we/wy na różnych typach maszyn wirtualnych
- Możliwości sieciowe różnych typów maszyn wirtualnych
- Liczba dysków, które można podłączyć
- Możliwość wykorzystania niektórych typów magazynu platformy Azure

do twoich potrzeb. Większość tych danych można znaleźć [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows] dla określonego typu maszyny Wirtualnej.

Jako model cenowy masz kilka różnych opcji cen, które wymieniają takie opcje:

- Płatność zgodnie z rzeczywistym użyciem
- Jeden rok zarezerwowany
- Trzy lata zarezerwowane
- Ceny kasowe

Ceny każdej z różnych ofert z różnych ofert usług wokół systemów operacyjnych i różnych regionów jest dostępny na stronie [Linux Virtual Machines Cennik](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [Windows Virtual Machines Ceny](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Aby uzyskać szczegółowe informacje i elastyczność wystąpienia zarezerwowanego na rok i trzy lata, zapoznaj się z tymi artykułami:

- [Co to są rezerwacje platformy Azure?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
- [Elastyczność rozmiaru maszyny wirtualnej w usłudze Reserved VM Instances](https://docs.microsoft.com/azure/virtual-machines/windows/reserved-vm-instance-size-flexibility)
- [Jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-vm-reservation-charges) 

Aby uzyskać więcej informacji na temat cen spot, przeczytaj artykuł [Azure Spot Virtual Machines](https://azure.microsoft.com/pricing/spot/). Ceny tego samego typu maszyny Wirtualnej mogą również różnić się między różnymi regionami platformy Azure. W przypadku niektórych klientów warto było wdrożyć w tańszym regionie platformy Azure.

Ponadto platforma Azure oferuje koncepcje dedykowanego hosta. Koncepcja dedykowanego hosta zapewnia większą kontrolę nad cyklami poprawek wykonywanych przez platformę Azure. Możesz czas poprawki zgodnie z własnymi harmonogramami. Ta oferta jest specjalnie kierowane do klientów z obciążeniem, które mogą nie po normalnym cyklu obciążenia. Aby zapoznać się z pojęciami ofert dedykowanych hostów platformy Azure, przeczytaj artykuł [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts). Korzystanie z tej oferty jest obsługiwane dla obciążenia SAP i jest używane przez kilku klientów SAP, którzy chcą mieć większą kontrolę nad łataniem infrastruktury i ewentualnymi planami konserwacji firmy Microsoft. Aby uzyskać więcej informacji na temat sposobu, w jaki firma Microsoft przechowuje i łata infrastrukturę platformy Azure, która obsługuje maszyny wirtualne, przeczytaj artykuł [Konserwacja maszyn wirtualnych na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/maintenance-and-updates)

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Maszyny wirtualne generacji 1 i 2 generacji
Hipernadzorcy firmy Microsoft jest w stanie obsłużyć dwie różne generacje maszyn wirtualnych. Formaty te nazywane są **generacją 1** i **generacją 2**. **Generacja 2** została wprowadzona w roku 2012 z hypervisorem systemu Windows Server 2012. Platforma Azure rozpoczęła się przy użyciu maszyn wirtualnych generacji 1. Podczas wdrażania maszyn wirtualnych platformy Azure domyślnie jest nadal używać formatu generacji 1. W międzyczasie można również wdrożyć formaty maszyn wirtualnych generacji 2. Artykuł [Obsługa maszyn wirtualnych generacji 2 na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) zawiera listę rodzin maszyn wirtualnych platformy Azure, które można wdrożyć jako maszynę wirtualną generacji 2. W tym artykule wymieniono również bardzo ważne różnice funkcjonalne maszyn wirtualnych generacji 2, które można uruchomić w chmurze prywatnej funkcji Hyper-V i platformie Azure. Ważniejsze w tym artykule wymieniono również różnice funkcjonalne między maszynami wirtualnymi generacji 1 i maszynami wirtualnymi generacji 2, jak te uruchamiane na platformie Azure. 

> [!NOTE]
> Istnieją różnice funkcjonalne generacji 1 i generacji 2 maszyn wirtualnych uruchomionych na platformie Azure. Przeczytaj artykuł [Obsługa maszyn wirtualnych generacji 2 na platformie Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) aby wyświetlić listę tych różnic.  
 
Przeniesienie istniejącej maszyny Wirtualnej z jednej generacji na drugą generację nie jest możliwe. Aby zmienić generowanie maszyny wirtualnej, należy wdrożyć nową maszynę wirtualną generacji, której potrzebujesz, i ponownie zainstalować oprogramowanie uruchomione na maszynie wirtualnej generacji. Dotyczy to tylko podstawowego obrazu VHD maszyny wirtualnej i nie ma wpływu na dyski danych lub dołączone udziały NFS lub SMB. Dyski danych, nfs lub udziały SMB, które pierwotnie zostały przypisane do, na przykład, na maszynie wirtualnej generacji 1

W tej chwili wystąpi ten problem, szczególnie między maszynami wirtualnymi z serii M platformy Azure i maszynami wirtualnymi z serii Mv2. Ze względu na ograniczenia w formacie maszyny Wirtualnej generacji 1 duże maszyny wirtualne z rodziny Mv2 nie mogły być oferowane w formacie generacji 1, ale muszą być oferowane wyłącznie w generacji 2. Z drugiej strony rodzina maszyn wirtualnych serii M nie jest jeszcze włączona do wdrażania w generacji 2. W rezultacie ponowne zmiany rozmiaru między maszynami wirtualnymi serii M i Mv2 serii wymagają ponownej instalacji oprogramowania na maszynie wirtualnej, która jest kierowana na inną rodzinę maszyn wirtualnych. Firma Microsoft pracuje nad wdrożeniem maszyn wirtualnych z serii M dla wdrożeń generacji 2. Wdrażanie maszyn wirtualnych z serii M jako maszyn wirtualnych generacji 2 w przyszłości umożliwi pozornie mniej zmiany rozmiaru między maszynami wirtualnymi serii M i Mv2. W obu kierunkach rozmiary z serii M do większych maszyn wirtualnych serii Mv2 lub rozmiary w dół z większych maszyn wirtualnych z serii Mv2 do mniejszych maszyn wirtualnych z serii M. Dokumentacja zostanie zaktualizowana, gdy tylko maszyny wirtualne z serii M będą mogły zostać wdrożone jako maszyny wirtualne generacji 2.    

 

### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Magazyn: Magazyn microsoft azure i dyski danych
Maszyny wirtualne platformy Microsoft Azure korzystają z różnych typów magazynu. Podczas implementowania sap na usługi maszyny wirtualnej platformy Azure, ważne jest, aby zrozumieć różnice między tymi dwoma głównymi typami magazynu:

* Nietrwałe, nietrwałe przechowywanie.
* Magazyn trwały.

Maszyny wirtualne platformy Azure oferują dyski nietrwałe po wdrożeniu maszyny wirtualnej. W przypadku ponownego uruchomienia maszyny Wirtualnej cała zawartość na tych dyskach zostanie wymazana. W związku z tym jest to, że pliki danych i log/redo plików baz danych nie powinny w żadnym wypadku znajdować się na tych dyskach nieutrwalonych. Mogą istnieć wyjątki dla niektórych baz danych, gdzie te nieutrwalone dyski mogą być odpowiednie dla tempdb i temp tablespaces. Należy jednak unikać używania tych dysków dla maszyn wirtualnych serii A, ponieważ te dyski nieutrwalone są ograniczone w przepływności z tej rodziny maszyn wirtualnych. Aby uzyskać więcej informacji, przeczytaj artykuł [Opis tymczasowego dysku na maszynach wirtualnych z systemem Windows na platformie Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> Dysk D:\ w maszynie Wirtualnej platformy Azure jest dyskiem nieutrwalone, który jest wspierany przez niektóre dyski lokalne w węźle obliczeniowym platformy Azure. Ponieważ nie jest utrwalony, oznacza to, że wszelkie zmiany wprowadzone do zawartości na D:\ dysk zostanie utracony po ponownym uruchomieniu maszyny Wirtualnej. Przez "wszelkie zmiany", takie jak przechowywane pliki, utworzone katalogi, zainstalowane aplikacje itp.
> 
> ![Linux][Logo_Linux] Linux
> 
> Maszyny wirtualne platformy Azure z systemem Linux automatycznie montują dysk w /mnt/resource, który jest dyskiem nieutrwalonym wspieranym przez dyski lokalne w węźle obliczeniowym platformy Azure. Ponieważ nie jest utrwalony, oznacza to, że wszelkie zmiany wprowadzone do zawartości w /mnt/resource zostaną utracone po ponownym uruchomieniu maszyny Wirtualnej. Przez wszelkie zmiany, takie jak przechowywane pliki, katalogi utworzone, zainstalowane aplikacje itp.
> 
> 

---

Usługa Microsoft Azure Storage zapewnia utrwalone miejsce do magazynowania oraz typowe poziomy ochrony i nadmiarowości widoczne w magazynie san. Dyski oparte na usłudze Azure Storage to wirtualne dyski twarde (VHD) znajdujące się w usługach usługi Azure Storage. Lokalny dysk systemu operacyjnego (Windows C:\, Linux /dev/sda1) jest przechowywany w usłudze Azure Storage, a dodatkowe woluminy/dyski zainstalowane na maszynie wirtualnej są tam przechowywane.

Istnieje możliwość przekazania istniejącego dysku wirtualnego z lokalnego lub utworzyć puste z poziomu platformy Azure i dołączyć te dyski WIRTUALNE do wdrożonych maszyn wirtualnych.

Po utworzeniu lub przekazaniu dysku VHD do usługi Azure Storage można zainstalować i dołączyć je do istniejącej maszyny wirtualnej i skopiować istniejący (odinstalowany) dysk VHD.

Ponieważ te dyski VHD są utrwalone, dane i zmiany w tych dyskach VHD są bezpieczne podczas ponownego uruchamiania i ponownego tworzenia wystąpienia maszyny wirtualnej. Nawet jeśli wystąpienie zostanie usunięte, te dyski VHD pozostają bezpieczne i mogą zostać ponownie rozmieszczone lub w przypadku dysków innych niż OS mogą być instalowane na innych maszynach wirtualnych.

Więcej informacji na temat usługi Azure Storage można znaleźć tutaj:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Usługa Azure Standard storage była typem magazynu dostępnego po wydaniu usługi Azure IaaS. Na jednym dysku wymuszano przydziały we/wyz. Wystąpiło opóźnienie nie było w tej samej klasie co urządzenia SAN/NAS zwykle wdrażane dla wysokiej klasy systemów SAP hostowanych lokalnie. Niemniej jednak usługa Azure Standard Storage okazała się wystarczająca dla wielu setek systemów SAP wdrożonych na platformie Azure.

Dyski przechowywane na kontach usługi Azure Standard Storage są naliczane na podstawie rzeczywistych danych, które są przechowywane, ilość transakcji magazynu, wychodzących transferów danych i wybranej opcji nadmiarowości. Wiele dysków można utworzyć z maksymalnym rozmiarem 1 TB, ale tak długo, jak te pozostają puste, nie ma żadnych opłat. Jeśli następnie wypełnisz jeden VHD po 100 GB, zostaniesz obciążony opłatą za przechowywanie 100 GB, a nie za nominalny rozmiar, z którego został utworzony dysk VHD.

#### <a name="azure-premium-storage"></a><a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Usługa Azure w magazynie w wersji premium
Usługa Azure Premium Storage została wprowadzona w celu zapewnienia:

* Lepsze opóźnienie we/wy.
* Lepsza przepustowość.
* Mniejsza zmienność opóźnienia we/wy.

W tym celu wprowadzono wiele zmian, z których dwa najważniejsze to:

* Użycie dysków SSD w węzłach usługi Azure Storage
* Nowa pamięć podręczna odczytu, która jest wspierana przez lokalny ssd węzła obliczeniowego platformy Azure

W przeciwieństwie do magazynu standardowego, gdzie możliwości nie zmieniły się w zależności od rozmiaru dysku (lub VHD), magazyn w wersji Premium ma obecnie trzy różne kategorie dysków, które są pokazane w tym artykule:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Widać, że przepływność/dysk we/wydziałów/dysku zależy od kategorii rozmiaru dysków

Podstawa kosztowa w przypadku magazynu w wersji Premium nie jest rzeczywistym woluminem danych przechowywanym na takich dyskach, ale kategorią rozmiaru takiego dysku, niezależnie od ilości danych przechowywanych w dysku.

Można również utworzyć dyski w magazynie w wersji Premium, które nie są bezpośrednio mapowanie do kategorii rozmiaru pokazano. Może tak być w przypadku, zwłaszcza podczas kopiowania dysków z magazynu standardowego do magazynu w wersji Premium. W takich przypadkach wykonywane jest mapowanie do następnej największej opcji dysku magazynu w wersji Premium.

Większość rodzin maszyn wirtualnych platformy Azure certyfikowanych za pomocą usługi SAP może pracować z magazynem w wersji Premium lub z mieszaniną między standardem Azure a magazynem w wersji Premium.

Jeśli wyewidencjonowywania części maszyn wirtualnych z serii DS w [tym artykule (Linux)][virtual-machines-sizes-linux] i [w tym artykule (Windows)][virtual-machines-sizes-windows]zdajesz sobie sprawę, że istnieją ograniczenia woluminu danych dysków magazynu w wersji Premium na poziomie szczegółowości maszyny Wirtualnej. Różne maszyny wirtualne serii DS lub GS mają również różne ograniczenia w liczbie dysków z danymi, które można zamontować. Limity te są również udokumentowane w artykule wymienionym powyżej. Ale w istocie oznacza to, że jeśli, na przykład, zamontować 32 x P30 dysków do jednego DS14 VM nie można uzyskać 32 x maksymalną przepustowość dysku P30. Zamiast tego maksymalna przepływność na poziomie maszyny Wirtualnej, zgodnie z dokumentem w artykule ogranicza przepływność danych.

Więcej informacji na temat magazynu w wersji Premium można znaleźć tutaj:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

Podczas wdrażania usług lub maszyn wirtualnych na platformie Azure wdrażanie dysków VHD i obrazów maszyn wirtualnych można zorganizować w jednostkach o nazwie Konta usługi Azure Storage. Planując wdrożenie platformy Azure, należy dokładnie rozważyć ograniczenia platformy Azure. Z jednej strony istnieje ograniczona liczba kont magazynu na subskrypcję platformy Azure. Mimo że każde konto usługi Azure Storage może pomieścić dużą liczbę plików VHD, istnieje stały limit całkowitego konta We/Wy na konto magazynu. Podczas wdrażania setek maszyn wirtualnych SAP z systemami DBMS tworzących znaczące wywołania we/wy zaleca się dystrybucję maszyn wirtualnych we/wy na wiele maszyn wirtualnych usług We/Wy na wiele różnych kont usługi Azure Storage. Należy zadbać o to, aby nie przekroczyć bieżącego limitu kont usługi Azure Storage na subskrypcję. Ponieważ magazyn pamięci masowej jest istotną częścią wdrażania bazy danych dla systemu SAP, ta koncepcja została omówiona bardziej szczegółowo w [przewodniku wdrażania dbms,][dbms-guide]do którego istnieje już przywoływalona .

Więcej informacji na temat kont usługi Azure Storage można znaleźć w [obiektach docelowych skalowalności dla standardowych kont magazynu](../../../storage/common/scalability-targets-standard-account.md) i celów [skalowalności dla kont magazynu obiektów blob strony premium.](../../../storage/blobs/scalability-targets-premium-page-blobs.md) Czytając te artykuły, zdajesz sobie sprawę, że istnieją różnice w ograniczeniach między kontami usługi Azure Standard Storage i kontami magazynu w jakości. Główne różnice to ilość danych, które mogą być przechowywane na takim koncie magazynu. W magazynie standardowym wolumin ma rozmiar większy niż w przypadku magazynu w wersji Premium. Z drugiej strony standardowe konto magazynu jest poważnie ograniczone w usługach We/Wy (zobacz kolumna **Całkowita szybkość żądania),** podczas gdy konto usługi Azure Premium Storage nie ma takiego ograniczenia. Omówimy szczegóły i wyniki tych różnic podczas omawiania wdrożeń systemów SAP, zwłaszcza serwerów DBMS.

W ramach konta magazynu masz możliwość tworzenia różnych kontenerów w celu organizowania i kategoryzacji różnych dysków VHD. Kontenery te są używane na przykład do oddzielnych wirtualnych maszyn wirtualnych różnych maszyn wirtualnych. Nie ma żadnych skutków dla wydajności przy użyciu tylko jednego kontenera lub wielu kontenerów pod jednym kontem usługi Azure Storage.

W ramach platformy Azure nazwa dysku VHD następuje następujące połączenie nazewnictwa, które musi zapewnić unikatową nazwę dysku wirtualnego na platformie Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Powyższy ciąg musi jednoznacznie zidentyfikować dysk VHD, który jest przechowywany w usłudze Azure Storage.

#### <a name="managed-disks"></a><a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Dyski zarządzane

Dyski zarządzane to nowy typ zasobu w usłudze Azure Resource Manager, który może być używany zamiast dysków wirtualnych przechowywanych na kontach usługi Azure Storage. Dyski zarządzane automatycznie wyrównać z zestawem dostępności maszyny wirtualnej, które są dołączone do i w związku z tym zwiększyć dostępność maszyny wirtualnej i usług, które są uruchomione na maszynie wirtualnej. Aby uzyskać więcej informacji, przeczytaj [artykuł .](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)

Zaleca się używanie dysku zarządzanego, ponieważ upraszczają one wdrażanie maszyn wirtualnych i zarządzanie nimi.
Sap obecnie obsługuje tylko dyski zarządzane w wersji Premium. Aby uzyskać więcej informacji, przeczytaj notatkę SAP [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Odporność usługi Microsoft Azure Storage

Usługa Microsoft Azure Storage przechowuje podstawowy dysk VHD (z systemem operacyjnym) i dołączone dyski lub obiekty BLOB w co najmniej trzech oddzielnych węzłach magazynu. Fakt ten jest nazywany local redundant storage (LRS). LRS jest domyślny dla wszystkich typów magazynu na platformie Azure. 

Istnieje kilka innych metod nadmiarowości, które są opisane w artykule [Replikacja usługi Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

> [!NOTE]
>W przypadku usługi Azure Premium Storage, która jest zalecanym typem magazynu dla maszyn wirtualnych i dysków DBMS, które przechowują pliki bazy danych i log/redo, jedyną dostępną metodą jest LRS. W rezultacie należy skonfigurować metody bazy danych, takie jak SQL Server Always On, Oracle Data Guard lub HANA System Replication, aby włączyć replikację danych bazy danych do innego regionu platformy Azure lub innej strefy dostępności platformy Azure.


> [!NOTE]
> W przypadku wdrożeń systemu dbms użycie magazynu geograficznie nadmiarowego dostępnego w usłudze Azure Standard Storage nie jest zalecane, ponieważ ma on duży wpływ na wydajność i nie jest honorowane w kolejności zapisu w różnych dyskach wirtualnych, które są dołączone do maszyny Wirtualnej. Fakt nie honorowania kolejności zapisu w różnych vhds nosi wysoki potencjał, aby skończyć w niespójnych baz danych po stronie docelowej replikacji, jeśli bazy danych i log /redo pliki są rozłożone na wiele VHDs (jak głównie w przypadku) po stronie źródłowej maszyny Wirtualnej.


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Sieć Platformy Microsoft Azure

Platforma Microsoft Azure udostępnia infrastrukturę sieciową, która umożliwia mapowanie wszystkich scenariuszy, które chcemy zrealizować za pomocą oprogramowania SAP. Możliwości są następujące:

* Dostęp z zewnątrz, bezpośrednio do maszyn wirtualnych za pośrednictwem usług terminalowych systemu Windows lub ssh/VNC
* Dostęp do usług i określonych portów używanych przez aplikacje na maszynach wirtualnych
* Wewnętrzna komunikacja i rozpoznawanie nazw między grupą maszyn wirtualnych wdrożonych jako maszyny wirtualne platformy Azure
* Łączność między lokalizacjami między siecią lokalną klienta a siecią platformy Azure
* Łączność między regionem platformy Azure lub centrum danych między lokacjami platformy Azure

Więcej informacji można znaleźć tutaj: <https://azure.microsoft.com/documentation/services/virtual-network/>

Istnieje wiele różnych możliwości konfigurowania nazwy i rozpoznawania adresów IP na platformie Azure. Istnieje również usługa DNS platformy Azure, która może być używana zamiast konfigurowania własnego serwera DNS. Więcej informacji można znaleźć w [tym artykule][virtual-networks-manage-dns-in-vnet] i na [tej stronie](https://azure.microsoft.com/services/dns/).

W przypadku scenariuszy międzylokacyjnych lub hybrydowych polegamy na tym, że lokalna usługa AD/OpenLDAP/DNS została rozszerzona za pośrednictwem sieci VPN lub połączenia prywatnego z platformą Azure. W przypadku niektórych scenariuszy, zgodnie z tym opisem, może być konieczne zainstalowanie repliki usługi AD/OpenLDAP na platformie Azure.

Ponieważ rozpoznawanie sieci i nazw jest istotną częścią wdrażania bazy danych dla systemu SAP, ta koncepcja została omówiona bardziej szczegółowo w [Przewodniku wdrażania systemu DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

Tworząc sieć wirtualną platformy Azure, można zdefiniować zakres adresów prywatnych adresów IP przydzielonych przez funkcję usługi Azure DHCP. W scenariuszach międzylokacjowych zdefiniowany zakres adresów IP jest nadal przydzielany przy użyciu usługi DHCP przez platformę Azure. Jednak rozpoznawanie nazwy domeny odbywa się lokalnie (przy założeniu, że maszyny wirtualne są częścią domeny lokalnej) i w związku z tym można rozwiązać adresy poza różnymi usługami w chmurze azure.

Każda maszyna wirtualna na platformie Azure musi być połączona z siecią wirtualną.

Więcej szczegółów można znaleźć w [tym artykule][resource-groups-networking] i na [tej stronie](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Domyślnie po wdrożeniu maszyny Wirtualnej nie można zmienić konfiguracji sieci wirtualnej. Ustawienia protokołu TCP/IP należy pozostawić serwerowi DHCP platformy Azure. Domyślnym zachowaniem jest dynamiczne przypisywanie adresów IP.
>
>

Adres MAC wirtualnej karty sieciowej może ulec zmianie, na przykład po zmianie rozmiaru, a system operacyjny windows lub linux gościa odbiera nową kartę sieciową i automatycznie używa dhcp do przypisywania adresów IP i DNS w tym przypadku.

##### <a name="static-ip-assignment"></a>Statyczne przypisanie adresu IP
Istnieje możliwość przypisania stałych lub zastrzeżonych adresów IP do maszyn wirtualnych w ramach sieci wirtualnej platformy Azure. Uruchamianie maszyn wirtualnych w sieci wirtualnej platformy Azure otwiera wielką możliwość wykorzystania tej funkcji, jeśli jest to konieczne lub wymagane w niektórych scenariuszach. Przypisanie IP pozostaje ważne przez cały okres istnienia maszyny Wirtualnej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona, czy zamykana. W związku z tym należy wziąć pod uwagę ogólną liczbę maszyn wirtualnych (uruchomionych i zatrzymanych maszyn wirtualnych) podczas definiowania zakresu adresów IP dla sieci wirtualnej. Adres IP pozostaje przypisany do momentu usunięcia maszyny Wirtualnej i jej interfejsu sieciowego lub do momentu ponownego przypisania adresu IP. Aby uzyskać więcej informacji, przeczytaj [ten artykuł][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Należy przypisać statyczne adresy IP za pośrednictwem platformy Azure oznacza do poszczególnych vNICs. Nie należy przypisywać statycznych adresów IP w ramach systemu operacyjnego gościa do vNIC. Niektóre usługi platformy Azure, takie jak usługa Azure Backup Service, opierają się na tym, że co najmniej podstawowa usługa vNIC jest ustawiona na usługę DHCP, a nie na statyczne adresy IP. Zobacz też dokument [Rozwiązywanie problemów z tworzeniem kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Wiele kart sieciowych na maszynę wirtualną

Można zdefiniować wiele kart interfejsu sieci wirtualnej (vNIC) dla maszyny wirtualnej platformy Azure. Dzięki możliwości wielu vNCS można rozpocząć konfigurowanie separacji ruchu sieciowego, gdzie, na przykład, ruch klienta jest kierowany przez jeden vNIC i backend ruchu jest kierowane przez drugi vNIC. W zależności od typu maszyny Wirtualnej istnieją różne ograniczenia dotyczące liczby maszyn wirtualnych, które maszyna wirtualna może być przypisana. Dokładne szczegóły, funkcjonalność i ograniczenia można znaleźć w następujących artykułach:

* [Tworzenie maszyny Wirtualnej systemu Windows z wieloma kartami sieciowymi][virtual-networks-multiple-nics-windows]
* [Tworzenie maszyny wirtualnej z systemem Linux z wieloma kartami sieciowym][virtual-networks-multiple-nics-linux]
* [Wdrażanie maszyn wirtualnych z wieloma kartami sieciowymi przy użyciu szablonu][virtual-network-deploy-multinic-arm-template]
* [Wdrażanie wielu maszyn wirtualnych kart sieciowych przy użyciu programu PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Wdrażanie wielu maszyn wirtualnych kart sieciowych przy użyciu interfejsu wiersza polecenia platformy Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Łączność między lokacjami

Między środowiskiem lokalnym są maszyny wirtualne platformy Azure i lokalne połączenia połączone z przezroczystym i stałym połączeniem sieci VPN. Oczekuje się, że stanie się najbardziej typowym wzorcem wdrażania SAP na platformie Azure. Założenie jest takie, że procedury operacyjne i procesy z wystąpieniami SAP na platformie Azure powinny działać w sposób przejrzysty. Oznacza to, że powinieneś być w stanie wydrukować z tych systemów, a także użyć SAP Transport Management System (TMS) do transportu zmian z systemu deweloperskiego na platformie Azure do systemu testowego, który jest wdrażany lokalnie. Więcej dokumentacji dotyczącej witryny na miejscu można znaleźć w [tym artykule][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Urządzenie tunelu VPN

Aby utworzyć połączenie lokacja-lokacja (lokalne centrum danych z centrum danych platformy Azure), należy uzyskać i skonfigurować urządzenie sieci VPN lub użyć usługi Routing i dostęp zdalny (RRAS), która została wprowadzona jako składnik oprogramowania z systemem Windows Server 2012.

* [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu programu PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informacje o urządzeniach sieci VPN używanych na potrzeby połączeń bramy VPN typu lokacja-lokacja][vpn-gateway-about-vpn-devices]
* [Brama sieci VPN — często zadawane pytania][vpn-gateway-vpn-faq]

![Połączenie lokacja-lokacja między środowisku lokalnym a platformą Azure][planning-guide-figure-600]

Rysunek powyżej przedstawia dwie subskrypcje platformy Azure mają podrangi adresów IP zarezerwowane do użycia w sieciach wirtualnych na platformie Azure. Łączność z sieci lokalnej z platformą Azure jest ustanawiana za pośrednictwem sieci VPN.

#### <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Sieć VPN typu punkt-lokacja wymaga, aby każdy komputer kliencki łączył się z własną siecią VPN na platformie Azure. W przypadku scenariuszy SAP analizujemy, łączność z punktami lokacji nie jest praktyczna. W związku z tym nie dalsze odwołania są podane do łączności sieci VPN typu punkt-lokacja.

Więcej informacji można znaleźć tutaj
* [Konfigurowanie połączenia typu "wskaż lokacja" z siecią wirtualną przy użyciu portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Sieć VPN dla wielu lokacji

Platforma Azure oferuje również obecnie możliwość tworzenia łączności sieci VPN z wieloma lokacjami dla jednej subskrypcji platformy Azure. Wcześniej pojedyncza subskrypcja była ograniczona do jednego połączenia sieci VPN lokacja-lokacja. To ograniczenie znikło z wieloumiejecowymi połączeniami VPN dla jednej subskrypcji. Dzięki temu można wykorzystać więcej niż jeden region platformy Azure dla określonej subskrypcji za pośrednictwem konfiguracji między lokalniem.

Aby uzyskać więcej dokumentacji, zobacz [ten artykuł][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Połączenie sieci wirtualnej z siecią wirtualną

Korzystając z sieci VPN z wieloma lokacjami, należy skonfigurować oddzielną sieć wirtualną platformy Azure w każdym z regionów. Jednak często istnieje wymóg, że składniki oprogramowania w różnych regionach powinny komunikować się ze sobą. W idealnym przypadku ta komunikacja nie powinna być kierowana z jednego regionu platformy Azure do lokalnego i stamtąd do innego regionu platformy Azure. Aby przejść na skróty, platforma Azure oferuje możliwość skonfigurowania połączenia z jednej sieci wirtualnej platformy Azure w jednym regionie do innej sieci wirtualnej platformy Azure hostowanej w innym regionie. Ta funkcja jest nazywana połączeniem sieci wirtualnej z siecią wirtualną. Więcej informacji na temat tej funkcji <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>można znaleźć tutaj: .

#### <a name="private-connection-to-azure-expressroute"></a>Prywatne połączenie z usługą Azure ExpressRoute

Usługa Microsoft Azure ExpressRoute umożliwia tworzenie połączeń prywatnych między centrami danych platformy Azure a infrastrukturą lokalną klienta lub w środowisku kolokacji. Usługa ExpressRoute jest oferowana przez różnych dostawców sieci VPN (mpls (przełączane pakietami) lub innych dostawców usług sieciowych. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Połączenia usługi ExpressRoute zapewniają większe bezpieczeństwo, większą niezawodność dzięki wielu obwodom równoległym, szybsze prędkości i mniejsze opóźnienia niż typowe połączenia przez Internet.

Więcej informacji na temat usługi Azure ExpressRoute i ofert można znaleźć tutaj:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Usługa Express Route umożliwia wiele subskrypcji platformy Azure za pośrednictwem jednego obwodu usługi ExpressRoute, zgodnie z dokumentem w tym miejscu

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Wymuszone tunelowanie w przypadku pomieszczeń poprzecznym
W przypadku maszyn wirtualnych dołączających do domen lokalnych za pośrednictwem lokacji do lokacji, punktu do lokacji lub usługi ExpressRoute należy się upewnić, że ustawienia internetowego serwera proxy są wdrażane również dla wszystkich użytkowników tych maszyn wirtualnych. Domyślnie oprogramowanie uruchomione na tych maszynach wirtualnych lub użytkowników korzystających z przeglądarki w celu uzyskania dostępu do Internetu nie przechodziłoby przez firmowy serwer proxy, ale łączyłoby się bezpośrednio za pośrednictwem platformy Azure z Internetem. Ale nawet ustawienie proxy nie jest 100% rozwiązaniem do kierowania ruchem przez pełnomocnika firmy, ponieważ jest odpowiedzialny za oprogramowanie i usługi, aby sprawdzić, czy serwer proxy. Jeśli oprogramowanie uruchomione na maszynie wirtualnej nie robi tego lub administrator manipuluje ustawieniami, ruch do Internetu może być ponownie objazdem bezpośrednio za pośrednictwem platformy Azure do Internetu.

Aby uniknąć takiej bezpośredniej łączności z Internetem, można skonfigurować tunelowanie wymuszone za pomocą połączenia lokacja lokacja lokacja między środowiskiem lokalnym a platformą Azure. Szczegółowy opis funkcji tunelowania wymuszonego został opublikowany tutaj<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Wymuszone tunelowanie za pomocą usługi ExpressRoute jest włączane przez klientów reklamujących trasę domyślną za pośrednictwem sesji komunikacji równorzędnej Usługi ExpressRoute BGP.

#### <a name="summary-of-azure-networking"></a>Podsumowanie sieci platformy Azure

Ten rozdział zawierał wiele ważnych kwestii dotyczących usługi Azure Networking. Oto podsumowanie głównych punktów:

* Usługi Azure Virtual Networks umożliwiają umieszczenie struktury sieciowej w usłudze Azure. Sieci wirtualne mogą być izolowane względem siebie lub za pomocą sieciowych grup zabezpieczeń ruch między sieciami wirtualnymi może być kontrolowany.
* Sieci wirtualne platformy Azure mogą być wykorzystywane do przypisywania zakresów adresów IP do maszyn wirtualnych lub przypisywania stałych adresów IP do maszyn wirtualnych
* Aby skonfigurować połączenie lokacja-lokacja lub punkt-lokacja, musisz najpierw utworzyć sieć wirtualną platformy Azure
* Po wdrożeniu maszyny wirtualnej nie można już zmienić sieci wirtualnej przypisanej do maszyny wirtualnej

### <a name="quotas-in-azure-virtual-machine-services"></a>Przydziały w usługach maszyny wirtualnej platformy Azure
Musimy być jasne o tym, że infrastruktura magazynu i sieci jest współużytkowana przez maszyny wirtualne z różnymi usługami w infrastrukturze platformy Azure. I podobnie jak w centrach danych klienta, nadmierne aprowizacji niektórych zasobów infrastruktury odbywa się do pewnego stopnia. Platforma Microsoft Azure używa przydziałów dysków, procesorów, sieci i innych w celu ograniczenia zużycia zasobów oraz zachowania spójnej i deterministycznej wydajności.  Różne typy maszyn wirtualnych (A5, A6 itp.) mają różne przydziały dla liczby dysków, procesora, pamięci RAM i sieci.

> [!NOTE]
> Zasoby procesora CPU i pamięci typów maszyn wirtualnych obsługiwanych przez sap są wstępnie przydzielane w węzłach hosta. Oznacza to, że po wdrożeniu maszyny Wirtualnej zasoby na hoście są dostępne zgodnie z definicją typu maszyny Wirtualnej.
>
>

Podczas planowania i dokonywać rozmiaru sap na rozwiązania platformy Azure, przydziały dla każdego rozmiaru maszyny wirtualnej muszą być brane pod uwagę. Przydziały maszyn wirtualnych są opisane [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Opisane przydziały reprezentują teoretyczne wartości maksymalne.  Limit we/wy na dysk można osiągnąć za pomocą małych ios (8kb), ale prawdopodobnie nie można osiągnąć z dużych IOs (1Mb).  Limit we/wyciągnięty jest wymuszany na ziarnistość pojedynczego dysku.

Jako drzewo decyzji szorstka, aby zdecydować, czy system SAP pasuje do usług maszyny wirtualnej platformy Azure i jego możliwości lub czy istniejący system musi być skonfigurowany inaczej w celu wdrożenia systemu na platformie Azure, poniższego drzewa decyzyjnego można użyć:

![Drzewo decyzji, aby zdecydować o możliwości wdrożenia sap na platformie Azure][planning-guide-figure-700]

**Krok 1:** Najważniejszą informacją na początek jest wymóg SAPS dla danego systemu SAP. Wymagania SAPS muszą być oddzielone na część DBMS i część aplikacji SAP, nawet jeśli system SAP jest już wdrożony lokalnie w konfiguracji 2-warstwowej. W przypadku istniejących systemów SAPS związany z używanym sprzętem często można określić lub oszacować na podstawie istniejących testów porównawczych SAP. Wyniki można znaleźć tutaj: <https://sap.com/about/benchmark.html>.
W przypadku nowo wdrożonych systemów SAP należy przejść przez ćwiczenie zmiany rozmiaru, które powinno określać wymagania SAPS systemu.
Zobacz też ten blog i załączony dokument dotyczący rozmiaru sap na platformie Azure:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Krok 2:** W przypadku istniejących systemów należy zmierzyć wolumin we/wy i operacje we/wy na sekundę na serwerze DBMS. W przypadku nowo planowanych systemów, zmiany rozmiaru dla nowego systemu powinny również dać przybliżone pomysły wymagań we/wy po stronie dbms. Jeśli nie masz pewności, w końcu musisz przeprowadzić dowód koncepcji.

**Krok 3:** Porównaj wymagania SAPS dla serwera DBMS z SAPS różnych typów maszyn wirtualnych platformy Azure może zapewnić. Informacje o SAPS różnych typów maszyn wirtualnych platformy Azure są udokumentowane w SAP Note [1928533]. Należy skupić się na maszynie wirtualnej DBMS pierwszy, ponieważ warstwa bazy danych jest warstwą w systemie SAP NetWeaver, który nie jest skalowany w poziomie w większości wdrożeń. Z drugiej strony warstwę aplikacji SAP można skalować w poziomie. Jeśli żaden z typów maszyn wirtualnych platformy Azure obsługiwanych przez usługę SAP nie może dostarczyć wymaganego systemu SAPS, nie można uruchomić obciążenia planowanego systemu SAP na platformie Azure. Należy wdrożyć system lokalnie lub trzeba zmienić wolumin obciążenia dla systemu.

**Krok 4:** Zgodnie z opisanymi [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows]Platforma Azure wymusza przydział we/wydziału We/Wy na dysk niezależnie od tego, czy używasz magazynu standardowego, czy magazynu w wersji Premium. W zależności od typu maszyny Wirtualnej liczba dysków z danymi, które mogą być montowane, jest różna. W rezultacie można obliczyć maksymalną liczbę we/wy, którą można osiągnąć z każdym z różnych typów maszyn wirtualnych. W zależności od układu pliku bazy danych można rozbierać dyski, aby stały się jednym woluminem w systemie operacyjnym gościa. Jeśli jednak bieżący wolumin we/wy wdrożonego systemu SAP przekracza obliczone limity największego typu maszyny Wirtualnej platformy Azure i jeśli nie ma szans na zrekompensowanie większej ilości pamięci, obciążenie systemu SAP może mieć poważny wpływ. W takich przypadkach można trafić punkt, w którym nie należy wdrażać systemu na platformie Azure.

**Krok 5:** Szczególnie w systemach SAP, które są wdrażane lokalnie w konfiguracjach 2-warstwowych, istnieje prawdopodobieństwo, że system może wymagać skonfigurowania na platformie Azure w konfiguracji 3-warstwowej. W tym kroku należy sprawdzić, czy istnieje składnik w warstwie aplikacji SAP, który nie może być skalowany w poziomie i który nie pasuje do zasobów procesora CPU i pamięci różnych typów maszyn wirtualnych platformy Azure. Jeśli rzeczywiście istnieje taki składnik, systemu SAP i jego obciążenia nie można wdrożyć na platformie Azure. Ale jeśli można skalować w poziomie składników aplikacji SAP do wielu maszyn wirtualnych platformy Azure, system można wdrożyć na platformie Azure.

**Krok 6:** Jeśli składniki warstwy aplikacji DBMS i SAP można uruchomić na maszynach wirtualnych platformy Azure, konfiguracja musi zostać zdefiniowana w odniesieniu do:

* Liczba maszyn wirtualnych platformy Azure
* Typy maszyn wirtualnych dla poszczególnych komponentów
* Liczba vhdów w maszynie wirtualnej DBMS w celu zapewnienia wystarczającej liczby IOPS

## <a name="managing-azure-assets"></a>Zarządzanie zasobami platformy Azure

### <a name="azure-portal"></a>Portal Azure

Portal Azure jest jednym z trzech interfejsów do zarządzania wdrożeniami maszyn wirtualnych platformy Azure. Podstawowe zadania zarządzania, takie jak wdrażanie maszyn wirtualnych z obrazów, można wykonać za pośrednictwem witryny Azure portal. Ponadto tworzenie kont magazynu, sieci wirtualnych i innych składników platformy Azure są również zadaniami, które witryna Azure portal może dobrze obsługiwać. Jednak funkcje, takie jak przekazywanie dysków VHD z lokalnego środowiska na platformę Azure lub kopiowanie dysku VHD na platformie Azure to zadania, które wymagają narzędzi innych firm lub administrowania za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia.

![Witryna portalu Microsoft Azure — omówienie maszyny wirtualnej][planning-guide-figure-800]


Zadania administracyjne i konfiguracyjne dla wystąpienia maszyny wirtualnej są możliwe z poziomu witryny Azure portal.

Oprócz ponownego uruchamiania i zamykania maszyny wirtualnej można również dołączyć, odłączyć i utworzyć dyski danych dla wystąpienia maszyny wirtualnej, aby przechwycić wystąpienie przygotowania obrazu i skonfigurować rozmiar wystąpienia maszyny wirtualnej.

Portal Azure zawiera podstawowe funkcje wdrażania i konfigurowania maszyn wirtualnych i wielu innych usług platformy Azure. Jednak nie wszystkie dostępne funkcje są objęte witryny Azure portal. W witrynie Azure portal nie można wykonywać takich zadań, jak:

* Przekazywanie identyfikatorów VHD na platformę Azure
* Kopiowanie maszyn wirtualnych


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Zarządzanie za pomocą poleceń cmdlet programu Microsoft Azure PowerShell

Program Windows PowerShell to zaawansowana i rozszerzalna struktura, która została szeroko przyjęta przez klientów wdrażających większą liczbę systemów na platformie Azure. Po zainstalowaniu poleceń cmdlet programu PowerShell na komputerze stacjonarnym, laptopie lub dedykowanej stacji zarządzania polecenia cmdlet programu PowerShell można uruchamiać zdalnie.

Proces włączania lokalnego komputera stacjonarnego/przenośnego do korzystania z poleceń cmdlet programu Azure PowerShell i sposobu konfigurowania tych dla użycia z subskrypcjami platformy Azure opisano w [tym artykule][powershell-install-configure].

Bardziej szczegółowe kroki dotyczące instalowania, aktualizowania i konfigurowania poleceń cmdlet programu Azure PowerShell można również znaleźć w [tym rozdziale Przewodnika po wdrażaniu][deployment-guide-4.1].

Doświadczenie klienta do tej pory było to, że program PowerShell (PS) jest z pewnością bardziej zaawansowane narzędzie do wdrażania maszyn wirtualnych i tworzenia niestandardowych kroków we wdrażaniu maszyn wirtualnych. Wszyscy klienci korzystający z wystąpień SAP na platformie Azure używają poleceń cmdlet PS w celu uzupełnienia zadań zarządzania wykonywanych w witrynie Azure portal lub nawet używają poleceń cmdlet PS wyłącznie do zarządzania wdrożeniami na platformie Azure. Ponieważ polecenia cmdlet specyficzne dla platformy Azure współużytkują tę samą konwencję nazewnictwa co ponad 2000 poleceń cmdlet związanych z systemem Windows, administratorzy systemu Windows mogą łatwo wykorzystać te polecenia cmdlet.

Zobacz przykład tutaj:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Wdrożenie rozszerzenia platformy Azure dla systemu SAP (zobacz rozdział [Rozszerzenie platformy Azure dla sap][planning-guide-9.1] w tym dokumencie) jest możliwe tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia. W związku z tym jest obowiązkowe, aby skonfigurować i skonfigurować program PowerShell lub interfejsu wiersza polecenia podczas wdrażania lub administrowania systemu SAP NetWeaver na platformie Azure.  

Ponieważ platforma Azure zapewnia więcej funkcji, zostaną dodane nowe polecenia cmdlet PS, które wymagają aktualizacji poleceń cmdlet. W związku z tym warto sprawdzić witryny pobierania <https://azure.microsoft.com/downloads/> platformy Azure co najmniej raz w miesiącu dla nowej wersji poleceń cmdlet. Nowa wersja jest zainstalowana na starszej wersji.

Aby uzyskać ogólną listę poleceń programu PowerShell związanych z platformą Azure, sprawdź tutaj: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Zarządzanie za pomocą poleceń interfejsu wiersza polecenia platformy Microsoft Azure

Dla klientów, którzy korzystają z systemu Linux i chcą zarządzać zasobami platformy Azure Program PowerShell może nie być opcją. Firma Microsoft oferuje platformę Azure CLI jako alternatywę.
Narzędzie interfejsu wiersza polecenia platformy Azure udostępnia zestaw poleceń typu open source, międzyplatformowych do pracy z platformą Azure Platform. Narzędzie wiersza polecenia platformy Azure zapewnia wiele z tych samych funkcji, które można znaleźć w witrynie Azure portal.

Aby uzyskać informacje na temat instalacji, konfiguracji i używania poleceń interfejsu wiersza polecenia do wykonywania zadań platformy Azure, zobacz

* [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure][xplat-cli]
* [Wdrażanie maszyn wirtualnych i zarządzanie nimi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows w usłudze Azure Resource Manager][xplat-cli-azure-resource-manager]

Przeczytaj także rozdział [Platformy Azure CLI dla maszyn wirtualnych z systemem Linux][deployment-guide-4.5.2] w [Przewodniku wdrażania,][planning-guide] jak używać interfejsu wiersza polecenia platformy Azure do wdrażania rozszerzenia azure dla sap.


## <a name="first-steps-planning-a-deployment"></a>Pierwsze kroki planowania wdrożenia
Pierwszym krokiem w planowaniu wdrażania nie jest sprawdzenie, czy maszyny wirtualne są dostępne do uruchomienia sap. Pierwszym krokiem może być taki, który jest czasochłonne, ale najważniejsze, jest do pracy z zespołami zgodności i zabezpieczeń w firmie, na jakie warunki graniczne są do wdrażania, który typ obciążenia SAP lub proces biznesowy w chmurze publicznej. Jeśli twoja firma wdrożyła inne oprogramowanie przed platformą Azure, proces może być łatwe. Jeśli twoja firma jest bardziej na początku podróży, mogą być większe dyskusje niezbędne w celu ustalenia warunków brzegowych, warunków zabezpieczeń, które umożliwiają hostowanie niektórych danych SAP i procesów biznesowych SAP w chmurze publicznej.

Jako przydatną pomoc można wskazać [oferty firmy Microsoft dotyczące zgodności,](https://docs.microsoft.com/microsoft-365/compliance/offering-home) aby wyświetlić listę ofert zgodności, które firma Microsoft może dostarczyć. 

Inne obszary problemów, takie jak szyfrowanie danych w spoczynku lub inne szyfrowanie w usłudze platformy Azure, są udokumentowane w [omówienie szyfrowania platformy Azure.](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview)

Nie lekceważ tej fazy projektu w planowaniu. Tylko wtedy, gdy masz umowy i reguły wokół tego tematu, należy przejść do następnego kroku, który jest planowanie architektury sieci, które można wdrożyć na platformie Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Różne sposoby wdrażania maszyn wirtualnych dla sap na platformie Azure

W tym rozdziale dowiesz się, jak wdrożyć maszynę wirtualną na platformie Azure. Dodatkowe procedury przygotowania, a także obsługa maszyn wirtualnych i maszyn wirtualnych na platformie Azure są omówione w tym rozdziale.

### <a name="deployment-of-vms-for-sap"></a>Wdrażanie maszyn wirtualnych dla sap

Platforma Microsoft Azure oferuje wiele sposobów wdrażania maszyn wirtualnych i skojarzonych dysków. Dlatego ważne jest, aby zrozumieć różnice, ponieważ przygotowania maszyn wirtualnych mogą się różnić w zależności od metody wdrażania. Ogólnie rzecz biorąc, przyjrzymy się następującym scenariuszom:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Przenoszenie maszyny Wirtualnej z lokalnego środowiska na platformę Azure za pomocą dysku nieuogólnionego

Planujesz przenieść określony system SAP z lokalnego na platformę Azure. Można to zrobić, przekazując dysk wirtualny, który zawiera system operacyjny, pliki binarne SAP i pliki binarne DBMS oraz vhds z plikami danych i dzienników systemu dbms na platformę Azure. W przeciwieństwie do [scenariusza #2 poniżej,][planning-guide-5.1.2]zachować hostname, SAP SID i SAP kont użytkowników w maszynie Wirtualnej platformy Azure, ponieważ zostały one skonfigurowane w środowisku lokalnym. W związku z tym uogólnienie obrazu nie jest konieczne. Zobacz rozdziały [Przygotowanie do przenoszenia maszyny Wirtualnej z lokalnego na platformę Azure z nieogólnionym dyskiem][planning-guide-5.2.1] tego dokumentu do lokalnych kroków przygotowania i przekazywania nieogólnionych maszyn wirtualnych lub wirtualnych na platformę Azure. Przeczytaj rozdział [Scenariusz 3: Przenoszenie maszyny Wirtualnej z lokalnego przy użyciu nieogólnionego dysku wirtualnego platformy Azure z sap][deployment-guide-3.4] w [Przewodniku wdrażania][deployment-guide] szczegółowych kroków wdrażania takiego obrazu na platformie Azure.

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Wdrażanie maszyny Wirtualnej z obrazem specyficznym dla klienta

Ze względu na określone wymagania dotyczące poprawek w wersji systemu operacyjnego lub dbms dostarczone obrazy w portalu Azure Marketplace mogą nie odpowiadać Twoim potrzebom. W związku z tym może być konieczne utworzenie maszyny Wirtualnej przy użyciu własnego prywatnego obrazu maszyny Wirtualnej systemu operacyjnego/DBMS, który można wdrożyć kilka razy później. Aby przygotować taki prywatny obraz do powielania, należy wziąć pod uwagę następujące elementy:

---
> ![Windows][Logo_Windows] Windows
>
> Zobacz więcej szczegółów <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> tutaj: Ustawienia systemu Windows (takie jak identyfikator SID systemu Windows i nazwa hosta) muszą być abstrakcyjne/uogólnione na lokalnej maszynie wirtualnej za pomocą polecenia sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Wykonaj kroki opisane w tych artykułach dla [SUSE,][virtual-machines-linux-create-upload-vhd-suse] [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]lub [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], aby przygotować dysk VHD do przekazania na platformę Azure.
>
>

---
Jeśli zawartość SAP została już zainstalowana w lokalnej maszynie wirtualnej (szczególnie w przypadku systemów 2-warstwowych), można dostosować ustawienia systemu SAP po wdrożeniu maszyny Wirtualnej platformy Azure za pomocą procedury zmiany nazwy wystąpienia obsługiwanej przez Menedżera aprowizacji oprogramowania SAP (UWAGA SAP [1619720]). Zobacz rozdziały [Przygotowanie do wdrażania maszyny Wirtualnej z obrazem specyficznym dla klienta dla sap][planning-guide-5.2.2] i [przekazywanie dysku wirtualnego z lokalnego do platformy Azure][planning-guide-5.3.2] tego dokumentu dla lokalnych kroków przygotowania i przekazywania uogólnionej maszyny Wirtualnej na platformę Azure. Przeczytaj rozdział [Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazem niestandardowym dla systemu SAP][deployment-guide-3.3] w [Przewodniku wdrażania, aby][deployment-guide] uzyskać szczegółowe kroki wdrażania takiego obrazu na platformie Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Wdrażanie maszyny wirtualnej z portalu Azure Marketplace

Chcesz użyć obrazu maszyny Wirtualnej dostarczonej przez firmę Microsoft lub innej firmy z portalu Azure Marketplace w celu wdrożenia maszyny Wirtualnej. Po wdrożeniu maszyny Wirtualnej na platformie Azure należy postępować zgodnie z tymi samymi wytycznymi i narzędziami do instalowania oprogramowania SAP i/lub systemu dbms wewnątrz maszyny Wirtualnej, tak jak w środowisku lokalnym. Aby uzyskać bardziej szczegółowy opis wdrożenia, zobacz rozdział [1: Wdrażanie maszyny wirtualnej z portalu Azure Marketplace dla sap][deployment-guide-3.2] w [Przewodniku wdrażania][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Przygotowywanie maszyn wirtualnych za pomocą usługi SAP dla platformy Azure

Przed przekazaniem maszyn wirtualnych na platformę Azure należy upewnić się, że maszyny wirtualne i wirtualne maszyny wirtualne spełniają określone wymagania. Istnieją niewielkie różnice w zależności od metody wdrażania, która jest używana.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Przygotowanie do przenoszenia maszyny Wirtualnej z lokalnego środowiska na platformę Azure za pomocą dysku nieogólnionego

Typową metodą wdrażania jest przeniesienie istniejącej maszyny Wirtualnej, która uruchamia system SAP z lokalnego na platformę Azure. Ta maszyna wirtualna i system SAP na maszynie wirtualnej po prostu należy uruchomić na platformie Azure przy użyciu tej samej nazwy hosta i prawdopodobnie ten sam identyfikator SID SAP. W takim przypadku system operacyjny gościa maszyny Wirtualnej nie powinny być uogólnione dla wielu wdrożeń. Jeśli sieć lokalna została rozszerzona na platformę Azure, nawet te same konta domeny mogą być używane w ramach maszyny Wirtualnej, ponieważ były używane przed lokalnymi.

Wymagania dotyczące przygotowywania własnego dysku maszyn wirtualnych platformy Azure to:

* Pierwotnie VHD zawierający system operacyjny może mieć maksymalny rozmiar tylko 127GB. Ograniczenie to zostało wyeliminowane pod koniec marca 2015 r. Teraz dysk VHD zawierający system operacyjny może mieć rozmiar do 1 TB, jak każdy inny hostowany dysk VHD usługi Azure Storage.
* Musi być w stałym formacie VHD. Dynamiczne dyski VHD lub VHD w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamiczne dyski VHD zostaną przekonwertowane na statyczne dyski VHD podczas przesyłania vhd z poleceniami programu PowerShell lub cli
* VHDs, które są zainstalowane na maszynie Wirtualnej i powinny być ponownie zamontowane na platformie Azure do maszyny Wirtualnej muszą być w stałym formacie VHD, jak również. Przeczytaj [ten artykuł (Linux)](../../linux/managed-disks-overview.md) i [ten artykuł (Windows)](../../windows/managed-disks-overview.md)dla limitów rozmiaru dysków z danymi. Dynamiczne dyski VHD zostaną przekonwertowane na statyczne dyski VHD podczas przesyłania vhd z poleceniami programu PowerShell lub cli
* Dodaj inne konto lokalne z uprawnieniami administratora, które może być używane przez pomoc techniczną firmy Microsoft lub które można przypisać jako kontekst dla usług i aplikacji do uruchomienia, dopóki maszyna wirtualna nie zostanie wdrożona i będzie można używać bardziej odpowiednich użytkowników.
* Dodaj inne konta lokalne, ponieważ mogą być potrzebne dla określonego scenariusza wdrażania.

---
> ![Windows][Logo_Windows] Windows
>
> W tym scenariuszu nie uogólniania (sysprep) maszyny Wirtualnej jest wymagane do przekazywania i wdrażania maszyny Wirtualnej na platformie Azure.
> Upewnij się, że dysk D:\ nie jest używany.
> Ustaw automatyczne montowanie dysku dla dołączonych dysków zgodnie z opisem w rozdziale [Ustawianie autoinstalowania dla dołączonych dysków][planning-guide-5.5.3] w tym dokumencie.
>
> ![Linux][Logo_Linux] Linux
>
> W tym scenariuszu nie uogólniania (waagent -deprovision) maszyny Wirtualnej jest wymagane do przekazywania i wdrażania maszyny Wirtualnej na platformie Azure.
> Upewnij się, że /mnt/resource nie jest używany i że wszystkie dyski są zainstalowane za pośrednictwem uuid. W przypadku dysku systemu operacyjnego upewnij się, że wpis bootloadera odzwierciedla również instalację opartą na uuid.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Przygotowanie do wdrażania maszyny Wirtualnej z obrazem specyficznym dla klienta dla systemu SAP

Pliki VHD, które zawierają uogólniony system operacyjny są przechowywane w kontenerach na kontach usługi Azure Storage lub jako obrazy dysku zarządzanego. Nową maszynę wirtualną można wdrożyć z takiego obrazu, odwołując się do obrazu VHD lub Dysku zarządzanego jako źródła w plikach szablonów wdrożenia, jak opisano w rozdziale [Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazem niestandardowym dla sap][deployment-guide-3.3] [przewodnika wdrażania][deployment-guide].

Wymagania dotyczące przygotowywania własnego obrazu maszyny Wirtualnej platformy Azure to:

* Pierwotnie VHD zawierający system operacyjny może mieć maksymalny rozmiar tylko 127GB. Ograniczenie to zostało wyeliminowane pod koniec marca 2015 r. Teraz dysk VHD zawierający system operacyjny może mieć rozmiar do 1 TB, jak każdy inny hostowany dysk VHD usługi Azure Storage.
* Musi być w stałym formacie VHD. Dynamiczne dyski VHD lub VHD w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamiczne dyski VHD zostaną przekonwertowane na statyczne dyski VHD podczas przesyłania vhd z poleceniami programu PowerShell lub cli
* VHDs, które są zainstalowane na maszynie Wirtualnej i powinny być ponownie zamontowane na platformie Azure do maszyny Wirtualnej muszą być w stałym formacie VHD, jak również. Przeczytaj [ten artykuł (Linux)](../../windows/managed-disks-overview.md) i [ten artykuł (Windows)](../../linux/managed-disks-overview.md) dla limitów rozmiaru dysków z danymi. Dynamiczne dyski VHD zostaną przekonwertowane na statyczne dyski VHD podczas przesyłania vhd z poleceniami programu PowerShell lub cli
* Dodaj inne konta lokalne, ponieważ mogą być potrzebne dla określonego scenariusza wdrażania.
* Jeśli obraz zawiera instalację SAP NetWeaver i zmiana nazwy hosta z oryginalnej nazwy w punkcie wdrożenia platformy Azure jest prawdopodobne, zaleca się skopiować najnowsze wersje dysku DVD programu SAP Software Provisioning Manager do szablonu. Umożliwi to łatwe korzystanie z funkcji zmiany nazwy sap pod warunkiem, że zmiana nazwy hosta i/lub zmienić identyfikator SID systemu SAP w obrazie wdrożonej maszyny Wirtualnej zaraz po uruchomieniu nowej kopii.

---
> ![Windows][Logo_Windows] Windows
>
> Upewnij się, że dysk D:\ nie jest używany Ustaw automatyczne dozowanie dysku dla dołączonych dysków, zgodnie z opisem w rozdziale [Ustawianie automount dla dołączonych dysków][planning-guide-5.5.3] w tym dokumencie.
>
> ![Linux][Logo_Linux] Linux
>
> Upewnij się, że /mnt/resource nie jest używany i że wszystkie dyski są zainstalowane za pośrednictwem uuid. W przypadku dysku systemu operacyjnego upewnij się, że wpis bootloadera odzwierciedla również mocowanie oparte na uuid.
>
>

---
* Interfejs GUI SAP (do celów administracyjnych i konfiguracyjnych) może być wstępnie zainstalowany w takim szablonie.
* Inne oprogramowanie niezbędne do pomyślnego uruchomienia maszyn wirtualnych w scenariuszach międzylokatywnych można zainstalować tak długo, jak to oprogramowanie może pracować ze zmianą nazwy maszyny Wirtualnej.

Jeśli maszyna wirtualna jest przygotowana na tyle, aby być ogólny i ostatecznie niezależne od kont/użytkowników nie są dostępne w scenariuszu wdrożenia platformy Azure docelowe, ostatni krok przygotowania uogólniania takiego obrazu jest przeprowadzany.

##### <a name="generalizing-a-vm"></a>Uogólnianie maszyny Wirtualnej
---
> ![Windows][Logo_Windows] Windows
>
> Ostatnim krokiem jest zalogowanie się do maszyny Wirtualnej przy za pomocą konta administratora. Otwórz okno polecenia systemu Windows jako *administrator*. Przejdź do %windir%\windows\system32\sysprep i wykonaj polecenie sysprep.exe.
> Pojawi się małe okno. Ważne jest, aby sprawdzić opcję **Generalize** (domyślnie nie jest zaznaczone) i zmienić opcję zamykania z domyślnej "Reboot" na "shutdown". W tej procedurze przyjęto założenie, że proces sysprep jest wykonywany lokalnie w obiekcie operacyjnym gościa maszyny Wirtualnej.
> Jeśli chcesz wykonać procedurę z maszyną wirtualną już uruchomię na platformie Azure, wykonaj kroki opisane w [tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Jak przechwycić maszynę wirtualną z systemem Linux do użycia jako szablon usługi Resource Manager?][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Przenoszenie maszyn wirtualnych i wirtualnych maszyn wirtualnych między lokalnymi na platformę Azure
Ponieważ przekazywanie obrazów i dysków maszyn wirtualnych na platformę Azure nie jest możliwe za pośrednictwem witryny Azure portal, należy użyć poleceń cmdlet programu Azure PowerShell lub interfejsu wiersza polecenia. Inną możliwością jest użycie narzędzia "AzCopy". Narzędzie można kopiować veds między lokalnymi i azure (w obu kierunkach). Można również kopiować wirtualne identyfikatory między regionami platformy Azure. Zapoznaj się z [niniejszą dokumentacją,][storage-use-azcopy] aby pobrać i korzystać z AzCopy.

Trzecią alternatywą byłoby użycie różnych narzędzi zorientowanych na gui innych firm. Upewnij się jednak, że te narzędzia obsługują obiekty blob strony platformy Azure. Dla naszych celów musimy korzystać ze sklepu Azure Page Blob (różnice są opisane tutaj: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Również narzędzia dostarczane przez platformę Azure są wydajne w kompresji maszyn wirtualnych i wirtualnych, które muszą zostać przekazane. Jest to ważne, ponieważ ta wydajność kompresji skraca czas przekazywania (który i tak zmienia się w zależności od łącza przekazywania do Internetu z obiektu lokalnego i regionu wdrażania platformy Azure. Jest słusznym założeniem, że przekazywanie maszyny Wirtualnej lub wirtualnej z lokalizacji europejskiej do centrów danych platformy Azure z siedzibą w USA zajmie więcej czasu niż przekazywanie tych samych maszyn wirtualnych/wirtualnych dysków do europejskich centrów danych platformy Azure.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Przekazywanie dysku twardego z lokalnego środowiska na platformę Azure
Aby przekazać istniejącą maszynę wirtualną lub wirtualną z sieci lokalnej, taka maszyna wirtualna lub dysk wirtualny musi spełniać wymagania wymienione w rozdziale [Przygotowanie do przenoszenia maszyny Wirtualnej z lokalnego na platformę Azure za pomocą nieogólnionego dysku][planning-guide-5.2.1] tego dokumentu.

Taka maszyna wirtualna NIE musi być uogólniona i może być przekazana w stanie i kształcie, który ma po zamknięciu po stronie lokalnej. To samo dotyczy dodatkowych vhdów, które nie zawierają żadnego systemu operacyjnego.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Przekazywanie dysku VHD i uczynienie go dyskiem platformy Azure
W takim przypadku chcemy przekazać wirtualny dysk wirtualny z lub bez systemu operacyjnego w nim i zainstalować go na maszynie wirtualnej jako dysk danych lub użyć go jako dysku systemu operacyjnego. Jest to wieloetapowy proces

**Powershell**

* Zaloguj się do subskrypcji za pomocą *Connect-AzAccount*
* Ustawianie subskrypcji kontekstu za pomocą *set-azcontext* i parametr SubscriptionId lub SubscriptionName - zobacz<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Przekazywanie dysku VHD z *dodatkiem AzVhd* na konto usługi Azure Storage — zobacz<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcjonalnie) Tworzenie dysku zarządzanego z dysku VHD za pomocą *nowego dysku AzDisk* — zobacz<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Ustawianie dysku systemu operacyjnego nowej konfiguracji maszyny Wirtualnej na dysk VHD lub dysk zarządzany *za* pomocą<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Tworzenie nowej maszyny wirtualnej z konfiguracji maszyny Wirtualnej z *new-AzVM* - zobacz<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Dodawanie dysku danych do nowej maszyny Wirtualnej za pomocą *dodatku AzVMDataDisk* — zobacz<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Interfejs wiersza polecenia platformy Azure**

* Zaloguj się do subskrypcji za pomocą *logowania az*
* Wybierz subskrypcję z *zestawem kont `<subscription name or id` AZ --subscription*
* Przekazywanie dysku VHD z *obiektem blob magazynu az* — zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure z usługą Azure Storage][storage-azure-cli]
* (Opcjonalnie) Tworzenie dysku zarządzanego z dysku VHD za pomocą *tworzenia dysku az* — zobaczhttps://docs.microsoft.com/cli/azure/disk
* Tworzenie nowej maszyny Wirtualnej określającej przekazany dysk VHD lub Dysk zarządzany jako dysk systemu operacyjnego z *tworzeniem az vm* i *parametrem --attach-os-disk*
* Dodawanie dysku danych do nowej maszyny Wirtualnej z *dołączoną dyskiem maszyny wirtualnej az* i *parametrem --new*

**Szablonu**

* Przekazywanie dysku VHD za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure
* (Opcjonalnie) Tworzenie dysku zarządzanego z dysku VHD za pomocą programu PowerShell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal
* Wdrażanie maszyny Wirtualnej z szablonem JSON odwołującego się do dysku VHD, jak pokazano w [tym przykładzie szablonu JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) lub przy użyciu dysków zarządzanych, jak pokazano w [tym przykładzie szablonu JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Wdrażanie obrazu maszyny Wirtualnej
Aby przekazać istniejącą maszynę wirtualną lub wirtualną z sieci lokalnej, aby użyć go jako obrazu maszyny Wirtualnej platformy Azure, takiego jak maszyna wirtualna lub dysk wirtualny, muszą spełniać wymagania wymienione w rozdziale Przygotowanie do [wdrażania maszyny Wirtualnej z obrazem specyficznym dla klienta dla sap][planning-guide-5.2.2] tego dokumentu.

* Użyj *sysprep* w systemie Windows lub *waagent -deprovision* na Linuksie, aby uogólnić swoją maszynę wirtualną — zobacz [Dokumentacja techniczna Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla systemu Windows lub [Jak przechwycić maszynę wirtualną Systemu Linux do użycia jako szablon Menedżera zasobów][capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Zaloguj się do subskrypcji za pomocą *Connect-AzAccount*
* Ustawianie subskrypcji kontekstu za pomocą *set-azcontext* i parametr SubscriptionId lub SubscriptionName - zobacz<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Przekazywanie dysku VHD z *dodatkiem AzVhd* na konto usługi Azure Storage — zobacz<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego z dysku VHD za pomocą *new-AzImage* — zobacz<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Ustaw dysk systemu operacyjnego nowej konfiguracji maszyny Wirtualnej na
  * VHD z *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* - zobacz<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Zestaw obrazów dysku *zarządzanego-AzVMSourceImage* — zobacz<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Tworzenie nowej maszyny wirtualnej z konfiguracji maszyny Wirtualnej z *new-AzVM* - zobacz<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Interfejs wiersza polecenia platformy Azure**

* Użyj *sysprep* w systemie Windows lub *waagent -deprovision* na Linuksie, aby uogólnić swoją maszynę wirtualną — zobacz [Dokumentacja techniczna Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla systemu Windows lub [Jak przechwycić maszynę wirtualną Systemu Linux do użycia jako szablon Menedżera zasobów][capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Zaloguj się do subskrypcji za pomocą *logowania az*
* Wybierz subskrypcję z *zestawem kont `<subscription name or id` AZ --subscription*
* Przekazywanie dysku VHD z *obiektem blob magazynu az* — zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure z usługą Azure Storage][storage-azure-cli]
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego z dysku VHD za pomocą *obrazu az —* zobaczhttps://docs.microsoft.com/cli/azure/image
* Tworzenie nowej maszyny Wirtualnej określającej przekazany obraz dysku VHD lub dysku zarządzanego jako dysk systemu operacyjnego z tworzeniem i *parametrem az vm* *--image*

**Szablonu**

* Użyj *sysprep* w systemie Windows lub *waagent -deprovision* na Linuksie, aby uogólnić swoją maszynę wirtualną — zobacz [Dokumentacja techniczna Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla systemu Windows lub [Jak przechwycić maszynę wirtualną Systemu Linux do użycia jako szablon Menedżera zasobów][capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Przekazywanie dysku VHD za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego z dysku wirtualnego z programem PowerShell, platformą Azure CLI lub witryną Azure portal
* Wdrażanie maszyny Wirtualnej z szablonem JSON odwołującego się do obrazu VHD, jak pokazano w [tym przykładzie szablonu JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) lub przy użyciu obrazu dysku zarządzanego, jak pokazano w [tym przykładzie szablonu JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Pobieranie dysków VHD lub dysków zarządzanych do środowiska lokalnego
Usługa Azure Infrastructure as a Service nie jest jednokierunkową ulicą, która może przesyłać tylko systemy VHD i SAP. Można przenieść systemy SAP z platformy Azure z powrotem do świata lokalnego, jak również.

W czasie pobierania dysków VHD lub dysków zarządzanych nie można aktywna. Nawet podczas pobierania dysków, które są zainstalowane na maszynach wirtualnych, maszyna wirtualna musi zostać zamknięta i cofnięta alokacja. Jeśli chcesz tylko pobrać zawartość bazy danych, która powinna być używana do skonfigurowania nowego systemu lokalnie i jeśli jest dopuszczalne, że w czasie pobierania i konfiguracji nowego systemu, że system na platformie Azure może nadal działać można uniknąć długiego przestoju, wykonując skompresowaną kopię zapasową bazy danych na dysku i po prostu pobrać ten dysk, zamiast pobierać podstawową maszynę wirtualną systemu operacyjnego.

#### <a name="powershell"></a>PowerShell

* Pobieranie dysku zarządzanego  
  Najpierw musisz uzyskać dostęp do bazowego obiektu blob dysku zarządzanego. Następnie można skopiować podstawowy obiekt blob do nowego konta magazynu i pobrać obiekt blob z tego konta magazynu.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Pobieranie dysku twardego  
  Po zatrzymaniu systemu SAP i zamknięciu maszyny Wirtualnej można użyć polecenia cmdlet Save-AzVhd programu PowerShell w lokalnym miejscu docelowym, aby pobrać dyski VHD z powrotem do świata lokalnego. Aby to zrobić, potrzebujesz adresu URL dysku VHD, który można znaleźć w sekcji "magazyn" w witrynie Azure portal (należy przejść do konta magazynu i kontenera magazynu, w którym utworzono dysk wirtualny) i musisz wiedzieć, gdzie dysk VHD powinien zostać skopiowany do.

  Następnie można wykorzystać polecenie, definiując parametr SourceUri jako adres URL VHD do pobrania i LocalFilePath jako fizycznej lokalizacji VHD (w tym jego nazwę). Polecenie może wyglądać następująco:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Aby uzyskać więcej informacji na temat polecenia cmdlet <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>Save-AzVhd, sprawdź tutaj .

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
* Pobieranie dysku zarządzanego  
  Najpierw musisz uzyskać dostęp do bazowego obiektu blob dysku zarządzanego. Następnie można skopiować podstawowy obiekt blob do nowego konta magazynu i pobrać obiekt blob z tego konta magazynu.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Pobieranie dysku twardego   
  Po zatrzymaniu systemu SAP i zamknięciu maszyny Wirtualnej można użyć polecenia Azure CLI _do pobrania obiektu blob magazynu_ na lokalnym miejscu docelowym, aby pobrać dyski VHD z powrotem do świata lokalnego. Aby to zrobić, potrzebujesz nazwy i kontenera VHD, które można znaleźć w sekcji "Magazyn" w witrynie Azure portal (trzeba przejść do konta magazynu i kontenera magazynu, w którym utworzono dysk VHD) i musisz wiedzieć, gdzie vHD powinien zostać skopiowany do.

  Następnie można wykorzystać polecenie, definiując parametry obiektu blob i kontenera VHD do pobrania i miejsce docelowe jako fizycznej lokalizacji docelowej VHD (w tym jego nazwę). Polecenie może wyglądać następująco:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Przenoszenie maszyn wirtualnych i dysków na platformie Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopiowanie systemów SAP na platformie Azure

System SAP lub nawet dedykowany serwer DBMS obsługujący warstwę aplikacji SAP prawdopodobnie będzie składał się z kilku dysków, które zawierają system operacyjny z plikami binarnymi lub pliki danych i plików dziennika bazy danych SAP. Ani funkcja platformy Azure kopiowania dysków, ani funkcja platformy Azure zapisywania dysków na dysku lokalnym nie ma mechanizmu synchronizacji, który migaje wiele dysków w spójny sposób. W związku z tym stan skopiowanych lub zapisanych dysków, nawet jeśli są zainstalowane na tej samej maszynie wirtualnej będzie inna. Oznacza to, że w konkretnym przypadku posiadania różnych danych i plików dziennika zawartych w różnych dyskach baza danych w końcu byłaby niespójna.

**Wniosek: Aby skopiować lub zapisać dyski, które są częścią konfiguracji systemu SAP, należy zatrzymać system SAP, a także zamknąć wdrożoną maszynę wirtualną. Tylko wtedy można skopiować lub pobrać zestaw dysków, aby utworzyć kopię systemu SAP na platformie Azure lub lokalnie.**

Dyski danych mogą być przechowywane jako pliki VHD na koncie usługi Azure Storage i mogą być bezpośrednio dołączone do maszyny wirtualnej lub używane jako obraz. W takim przypadku dysk VHD jest kopiowany do innej lokalizacji przed dołączeniem do maszyny wirtualnej. Pełna nazwa pliku VHD na platformie Azure musi być unikatowa na platformie Azure. Jak wspomniano już wcześniej, nazwa jest rodzajem trzyczęściowej nazwy, która wygląda następująco:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Dyski danych mogą być również dyskami zarządzanymi. W takim przypadku dysk zarządzany jest używany do tworzenia nowego dysku zarządzanego przed dołączeniem do maszyny wirtualnej. Nazwa dysku zarządzanego musi być unikatowa w grupie zasobów.

##### <a name="powershell"></a>PowerShell

Polecenia cmdlet programu Azure PowerShell można użyć do skopiowania dysku twardego wirtualnego, jak pokazano w [tym artykule][storage-powershell-guide-full-copy-vhd]. Aby utworzyć nowy dysk zarządzany, należy użyć new-AzDiskConfig i New-AzDisk, jak pokazano w poniższym przykładzie.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można skopiować dysk VHD. Aby utworzyć nowy dysk zarządzany, użyj *tworzenia dysku az,* jak pokazano w poniższym przykładzie.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Narzędzia usługi Azure Storage

* <https://storageexplorer.com/>

Profesjonalne wersje eksploratorów usługi Azure Storage można znaleźć tutaj:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Kopia vhd się w ramach konta magazynu jest procesem, który trwa tylko kilka sekund (podobnie jak sprzęt SAN tworzenia migawek z opóźnieniem kopiowania i kopiowania w zapisie). Po dołączeniu kopii pliku VHD można dołączyć go do maszyny wirtualnej lub użyć go jako obrazu do dołączania kopii dysku VHD do maszyn wirtualnych.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopiowanie dysków między kontami usługi Azure Storage
Tego zadania nie można wykonać w witrynie Azure portal. Można użyć poleceń cmdlet azure powershell, interfejsu wiersza polecenia platformy Azure lub przeglądarki magazynu innej firmy. Polecenia cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia można tworzyć i zarządzać obiektami blob, które obejmują możliwość asynchronicznie kopiowania obiektów blob na kontach magazynu i w różnych regionach w ramach subskrypcji platformy Azure.

##### <a name="powershell"></a>PowerShell
Można również kopiować identyfikatory VHD między subskrypcjami. Aby uzyskać więcej informacji, przeczytaj [ten artykuł][storage-powershell-guide-full-copy-vhd].

Podstawowy przepływ logiki poleceń cmdlet PS wygląda następująco:

* Tworzenie kontekstu konta magazynu dla konta magazynu **źródłowego** za pomocą *New-AzStorageContext* — zobacz<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Tworzenie kontekstu konta magazynu dla **docelowego** konta magazynu za pomocą *New-AzStorageContext* — zobacz<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Rozpocznij kopię od

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Sprawdź stan kopii w pętli za pomocą

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Dołącz nowy dysk VHD do maszyny wirtualnej, jak opisano powyżej.

Przykłady można znaleźć w [tym artykule][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
* Rozpocznij kopię od

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Sprawdź stan, jeśli kopia jest nadal w pętli z

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Dołącz nowy dysk VHD do maszyny wirtualnej, jak opisano powyżej.

### <a name="disk-handling"></a>Obsługa dysków

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Struktura maszyn wirtualnych/dysków dla wdrożeń SAP

Najlepiej obsługi struktury maszyny Wirtualnej i skojarzonych dysków powinny być proste. W instalacjach lokalnych klienci opracowali wiele sposobów strukturyzowania instalacji serwera.

* Jeden dysk podstawowy, który zawiera system operacyjny i wszystkie pliki binarne systemu DBMS i/lub SAP. Od marca 2015 r. ten dysk może mieć rozmiar do 1 TB zamiast wcześniejszych ograniczeń, które ograniczyły go do 127 GB.
* Jeden lub wiele dysków, które zawierają plik dziennika DBMS bazy danych SAP i plik dziennika obszaru magazynu tymczasowego DBMS (jeśli dbms obsługuje to). Jeśli wymagania we/wy dziennika bazy danych są wysokie, należy rozbierać wiele dysków, aby osiągnąć wymagany wolumin We/Wy.
* Liczba dysków zawierających jeden lub dwa pliki bazy danych z bazy danych SAP i pliki danych tymczasowych DBMS, jak również (jeśli dbms obsługuje to).

![Konfiguracja referencyjna maszyny Wirtualnej usługi Azure IaaS dla systemu SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> U wielu klientów widzieliśmy konfiguracje, w których na przykład pliki binarne SAP i DBMS nie były zainstalowane na c:\ dysku, w którym zainstalowano system operacyjny. Było wiele powodów, ale kiedy wróciliśmy do korzenia, zwykle było to, że dyski były małe, a aktualizacje systemu operacyjnego potrzebowały dodatkowej przestrzeni 10-15 lat temu. Oba warunki nie mają już zastosowania w tych dniach zbyt często. Dziś c:\ dysk może być mapowany na dyskach o dużym woluminie lub maszynach wirtualnych. Aby w łączyć wdrożenia w swojej strukturze, zaleca się stosowanie następującego wzorca wdrażania systemów SAP NetWeaver na platformie Azure
>
> Plik stronicowy systemu operacyjnego Windows powinien znajdować się na dysku D: (dysk nietrwały)
>
> ![Linux][Logo_Linux] Linux
>
> Umieść plik wymiany Linuksa pod /mnt /mnt/resource na Linuksie, jak opisano w [tym artykule][virtual-machines-linux-agent-user-guide]. Plik wymiany można skonfigurować w pliku konfiguracyjnym agenta Linux /etc/waagent.conf. Dodaj lub zmień następujące ustawienia:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Aby aktywować zmiany, należy ponownie uruchomić agenta systemu Linux

```console
sudo service waagent restart
```

Przeczytaj sap uwaga [1597355] aby uzyskać więcej informacji na temat zalecanego rozmiaru pliku wymiany

---
Liczba dysków używanych dla plików danych DBMS i typ usługi Azure Storage, na których są hostowane te dyski, powinna być określona przez wymagania we/wy i wymagane opóźnienie. Dokładne przydziały są opisane w [tym artykule (Linux)][virtual-machines-sizes-linux] i [w tym artykule (Windows)][virtual-machines-sizes-windows].

Doświadczenie wdrożeń SAP w ciągu ostatnich dwóch lat nauczyło nas kilku lekcji, które można podsumować jako:

* Ruch We/Wy do różnych plików danych nie zawsze jest taki sam, ponieważ istniejące systemy klientów mogą mieć pliki danych o różnej wielkości reprezentujące ich bazy danych SAP. W rezultacie okazało się, że lepiej jest używać konfiguracji RAID na wielu dyskach, aby umieścić pliki danych LUN wyrzeźbione z tych. Zdarzały się sytuacje, szczególnie w przypadku usługi Azure Standard Storage, w których wskaźnik We/Wy osiągnął przydział pojedynczego dysku względem dziennika transakcji usługi DBMS. W takich scenariuszach zaleca się użycie magazynu w wersji Premium lub alternatywnie agregowanie wielu dysków magazynu standardowego z paskiem oprogramowania.

---
> ![Windows][Logo_Windows] Windows
>
> * [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurowanie macierzy RAID oprogramowania w systemie Linux][virtual-machines-linux-configure-raid]
> * [Konfigurowanie usługi LVM na maszynie Wirtualnej z systemem Linux na platformie Azure][virtual-machines-linux-configure-lvm]
> * [Wpisy tajne usługi Azure Storage i optymalizacje we/wy systemu Linux](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Magazyn w jakości Premium wykazuje znacznie lepszą wydajność, szczególnie w przypadku zapisów dziennika transakcji krytycznych. W przypadku scenariuszy SAP, które mają zapewnić wydajność podobną do produkcji, zdecydowanie zaleca się użycie serii maszyn wirtualnych, które mogą korzystać z usługi Azure Premium Storage.

Należy pamiętać, że dysk, który zawiera system operacyjny i jak zaleca się, pliki binarne SAP i bazy danych (podstawowa maszyna wirtualna), jak również, nie jest już ograniczona do 127GB. Teraz może mieć do 1 TB rozmiaru. Powinno to być wystarczająco dużo miejsca, aby zachować wszystkie niezbędne pliki, w tym, na przykład, dzienniki zadań wsadowych SAP.

Aby uzyskać więcej sugestii i więcej szczegółów, w szczególności dla maszyn wirtualnych systemu dbms, zapoznaj się z [Przewodnikiem wdrażania systemu dbms][dbms-guide]

#### <a name="disk-handling"></a>Obsługa dysków

W większości scenariuszy należy utworzyć dodatkowe dyski w celu wdrożenia bazy danych SAP na maszynie Wirtualnej. Rozmawialiśmy o zagadnieniach dotyczących liczby dysków w rozdziale [Struktura maszyn wirtualnych/dysków dla wdrożeń SAP][planning-guide-5.5.1] tego dokumentu. Witryna Azure portal umożliwia dołączanie i odłączanie dysków po wdrożeniu podstawowej maszyny Wirtualnej. Dyski mogą być podłączone/odłączone, gdy maszyna wirtualna jest uruchomiona i działa, a także po jej zatrzymaniu. Podczas dołączania dysku portal Azure portal oferuje dołączyć pusty dysk lub istniejący dysk, który w tym momencie nie jest dołączony do innej maszyny Wirtualnej.

**Uwaga:** Dyski można podłączyć tylko do jednej maszyny Wirtualnej w danym momencie.

![Dołączanie / odłączanie dysków za pomocą usługi Azure Standard Storage][planning-guide-figure-1400]

Podczas wdrażania nowej maszyny wirtualnej można zdecydować, czy chcesz używać dysków zarządzanych, czy umieszczać dyski na kontach usługi Azure Storage. Jeśli chcesz używać magazynu w wersji Premium, zalecamy użycie dysków zarządzanych.

Następnie należy zdecydować, czy chcesz utworzyć nowy i pusty dysk, czy chcesz wybrać istniejący dysk, który został przekazany wcześniej i powinien być teraz dołączony do maszyny Wirtualnej.

**WAŻNE:** **NIE** chcesz używać buforowania hostów w usłudze Azure Standard Storage. Należy pozostawić preferencję Pamięci podręcznej hosta domyślnie NONE. Za pomocą usługi Azure Premium Storage należy włączyć buforowanie odczytu, jeśli charakterystyka we/wy jest w większości odczytywana jako typowy ruch we/wy względem plików danych bazy danych. W przypadku pliku dziennika transakcji bazy danych nie zaleca się buforowania.

---
> ![Windows][Logo_Windows] Windows
>
> [Jak dołączyć dysk danych w witrynie Azure portal][virtual-machines-linux-attach-disk-portal]
>
> Jeśli dyski są podłączone, musisz zalogować się do maszyny Wirtualnej, aby otworzyć Menedżera dysków systemu Windows. Jeśli autodostawienie nie jest włączone zgodnie z zaleceniami w rozdziale [Ustawianie autodostawienia dla podłączonych dysków,][planning-guide-5.5.3]nowo podłączony wolumin musi zostać wykonany w trybie online i zainicjowany.
>
> ![Linux][Logo_Linux] Linux
>
> Jeśli dyski są podłączone, musisz zalogować się do maszyny Wirtualnej i zainicjować dyski zgodnie z opisem w [tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Jeśli nowy dysk jest pustym dyskiem, należy również sformatować dysk. Do formatowania, szczególnie w przypadku plików danych i dzienników DBMS, obowiązują takie same zalecenia, jak w przypadku wdrożeń systemu dbms.

Konto usługi Azure Storage nie zapewnia nieskończonych zasobów pod względem woluminu We/Wy, usługi We/Wy i woluminu danych. Zazwyczaj maszyny wirtualne DBMS są najbardziej dotknięte tym. Może być najlepiej użyć osobnego konta magazynu dla każdej maszyny Wirtualnej, jeśli masz kilka maszyn wirtualnych woluminu we/wy do wdrożenia, aby pozostać w granicach woluminu konta usługi Azure Storage. W przeciwnym razie musisz zobaczyć, jak można zrównoważyć te maszyny wirtualne między różnymi kontami magazynu bez osiągnięcia limitu każdego pojedynczego konta magazynu. Więcej szczegółów można znaleźć w [Przewodniku wdrażania systemu DBMS][dbms-guide]. Należy również pamiętać o tych ograniczeniach dla maszyn wirtualnych serwera aplikacji SAP lub innych maszyn wirtualnych, które ostatecznie mogą wymagać dodatkowych identyfikatorów VHD. Te ograniczenia nie mają zastosowania w przypadku korzystania z dysku zarządzanego. Jeśli planujesz używać magazynu w wersji Premium, zalecamy użycie dysku zarządzanego.

Innym tematem, który jest istotny dla kont magazynu jest to, czy veds na koncie magazynu są coraz replikowane geograficznie. Replikacja geograficzna jest włączona lub wyłączona na poziomie konta magazynu, a nie na poziomie maszyny Wirtualnej. Jeśli replikacja geograficzna jest włączona, dyski VHD w ramach konta magazynu będą replikowane do innego centrum danych platformy Azure w tym samym regionie. Przed podjęciem decyzji w tej sprawie należy pomyśleć o następujących ograniczeniach:

Replikacja geograficzna platformy Azure działa lokalnie na każdej dysku VHD na maszynie Wirtualnej i nie replikuje obiektów we/w porządku chronologicznym w wielu dyskach wirtualnych na maszynie wirtualnej. W związku z tym VHD, który reprezentuje podstawowej maszyny Wirtualnej, jak również wszelkie dodatkowe dyski VHD dołączone do maszyny Wirtualnej są replikowane niezależnie od siebie. Oznacza to, że nie ma synchronizacji między zmianami w różnych VHDs. Fakt, że operacje we/wy są replikowane niezależnie od kolejności, w jakiej są napisane, oznacza, że replikacja geograficzna nie ma wartości dla serwerów baz danych, które mają swoje bazy danych rozproszone za pośrednictwem wielu vhdów. Oprócz dbms, mogą istnieć również inne aplikacje, w których procesy zapisu lub manipulowania danymi w różnych VHDs i gdzie jest ważne, aby zachować kolejność zmian. Jeśli jest to wymagane, replikacja geograficzna na platformie Azure nie powinna być włączona. W zależności od tego, czy potrzebujesz replikacji geograficznej dla zestawu maszyn wirtualnych, ale nie dla innego zestawu, można już kategoryzować maszyny wirtualne i powiązane z nimi dyski wirtualne na różne konta magazynu, które mają włączoną lub wyłączoną replikację geograficzną.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Automatyczne ustawianie dla podłączonych dysków
---
> ![Windows][Logo_Windows] Windows
>
> Dla maszyn wirtualnych, które są tworzone z własnych obrazów lub dysków, konieczne jest sprawdzenie i ewentualnie ustawić parametr automount. Ustawienie tego parametru pozwoli maszynie Wirtualnej po ponownym uruchomieniu lub ponownym rozmieszczeniu na platformie Azure, aby zainstalować podłączone/zainstalowane dyski ponownie automatycznie.
> Parametr jest ustawiony dla obrazów dostarczonych przez firmę Microsoft w portalu Azure Marketplace.
>
> Aby ustawić automontowanie, sprawdź dokumentację pliku wykonywalnego diskpart.exe wiersza polecenia tutaj:
>
> * [Opcje wiersza polecenia programu DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatycznej instalacji](https://technet.microsoft.com/library/cc753703.aspx)
>
> Okno wiersza polecenia systemu Windows powinno zostać otwarte jako administrator.
>
> Jeśli dyski są podłączone, musisz zalogować się do maszyny Wirtualnej, aby otworzyć Menedżera dysków systemu Windows. Jeśli autodostawienie nie jest włączone zgodnie z zaleceniami w rozdziale [Ustawianie autodostawienia dla podłączonych dysków,][planning-guide-5.5.3]nowo dołączony wolumin >musi zostać wykonany w trybie online i zainicjowany.
>
> ![Linux][Logo_Linux] Linux
>
> Należy zainicjować nowo dołączony pusty dysk zgodnie z opisem w [tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Należy również dodać nowe dyski do /etc/fstab.
>
>

---
### <a name="final-deployment"></a>Ostateczne wdrożenie

Aby uzyskać ostateczne wdrożenie i dokładne kroki, szczególnie w odniesieniu do wdrażania rozszerzenia platformy Azure dla sap, zapoznaj się z [Przewodnikiem wdrażania][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Uzyskiwanie dostępu do systemów SAP działających w ramach maszyn wirtualnych platformy Azure

W przypadku scenariuszy, w których chcesz połączyć się z tymi systemami SAP w publicznym Internecie przy użyciu interfejsu GUI SAP, należy zastosować następujące procedury.

W dalszej części dokumentu omówimy inny główny scenariusz, łącząc się z systemami SAP we wdrożeniach między lokalnymi, które mają połączenie lokacja-lokacja (tunel SIECI VPN) lub połączenie Azure ExpressRoute między systemami lokalnymi a systemami platformy Azure.

### <a name="remote-access-to-sap-systems"></a>Zdalny dostęp do systemów SAP

W usłudze Azure Resource Manager nie ma już żadnych domyślnych punktów końcowych, takich jak w poprzednim modelu klasycznym. Wszystkie porty maszyny Wirtualnej usługi Azure Resource Manager są otwarte tak długo, jak:

1. Dla podsieci ani interfejsu sieciowego nie zdefiniowano żadnej sieciowej grupy zabezpieczeń. Ruch sieciowy do maszyn wirtualnych platformy Azure można zabezpieczyć za pośrednictwem tak zwanych "sieciowych grup zabezpieczeń". Aby uzyskać więcej informacji, zobacz [What is a Network Security Group (NSG)?][virtual-networks-nsg] (Co to jest sieciowa grupa zabezpieczeń?).
2. Dla interfejsu sieciowego nie zdefiniowano modułu równoważenia obciążenia platformy Azure   

Zobacz różnicę architektury między modelem klasycznym a ARM, jak opisano w [tym artykule][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfiguracja systemu SAP i łączności GUI SAP przez Internet

Zobacz ten artykuł, który opisuje szczegóły tego tematu:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Zmienianie ustawień zapory w maszynie wirtualnej

Może być konieczne skonfigurowanie zapory na maszynach wirtualnych, aby zezwolić na ruch przychodzący do systemu SAP.

---
> ![Windows][Logo_Windows] Windows
>
> Domyślnie Zapora systemu Windows w ramach platformy Azure wdrożonej maszyny Wirtualnej jest włączona. Teraz musisz zezwolić na otwarcie portu SAP, w przeciwnym razie interfejs GUI SAP nie będzie mógł się połączyć.
> W tym celu:
>
> * Otwórz Panel sterowania\System i Zabezpieczenia\Zapora systemu Windows w **ustawieniach zaawansowanych**.
> * Teraz kliknij prawym przyciskiem myszy na Reguły przychodzące i wybierz **Nową regułę**.
> * W poniższym Kreatorze wybierz opcję utworzenia nowej **reguły portu.**
> * W następnym kroku kreatora pozostaw ustawienie w TCP i wpisz numer portu, który chcesz otworzyć. Ponieważ nasz identyfikator wystąpienia SAP wynosi 00, wzięliśmy 3200. Jeśli wystąpienie ma inny numer wystąpienia, port zdefiniowany wcześniej na podstawie numeru wystąpienia powinien zostać otwarty.
> * W następnej części kreatora należy pozostawić zaznaczony element **Zezwalaj na połączenie.**
> * W następnym kroku kreatora należy określić, czy reguła ma zastosowanie do sieci domeny, sieci prywatnej i publicznej. W razie potrzeby dostosuj go do swoich potrzeb. Jednak połączenie z GUI SAP z zewnątrz za pośrednictwem sieci publicznej, należy zastosować regułę do sieci publicznej.
> * W ostatnim kroku kreatora nazwij regułę i zapisz, naciskając **przycisk Zakończ**.
>
> Reguła wchodzi w życie natychmiast.
>
> ![Definicja reguły portu][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Obrazy systemu Linux w portalu Azure Marketplace domyślnie nie włączają zapory iptables, a połączenie z systemem SAP powinno działać. Jeśli włączono iptables lub inną zaporę, zapoznaj się z dokumentacją iptables lub używanej zapory, aby zezwolić na przychodzący ruch tcp do portu 32xx (gdzie xx jest numerem systemowym systemu SAP).
>
>

---
#### <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Interfejs GUI SAP nie łączy się natychmiast z żadnymi wystąpieniami SAP (port 32xx), które są uruchomione, ale najpierw łączy się za pośrednictwem portu otwartego do procesu serwera komunikatów SAP (port 36xx). W przeszłości ten sam port był używany przez serwer wiadomości do komunikacji wewnętrznej z wystąpieniami aplikacji. Aby zapobiec przypadkowej komunikacji serwerów aplikacji lokalnych z serwerem wiadomości na platformie Azure, można zmienić wewnętrzne porty komunikacyjne. Zdecydowanie zaleca się zmianę komunikacji wewnętrznej między serwerem komunikatów SAP i jego wystąpieniami aplikacji na inny numer portu w systemach sklonowanych z systemów lokalnych, takich jak klon rozwoju do testowania projektu itp. Można to zrobić za pomocą domyślnego parametru profilu:

> rdisp/msserv_internal
>
>

zgodnie z dokumentami w [ustawieniach zabezpieczeń serwera komunikatów SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Pojedyncza maszyna wirtualna z scenariuszem demonstracyjnym/szkoleniowym SAP NetWeaver

![Uruchamianie pojedynczych systemów demonstracyjnych SAP maszyn wirtualnych o tych samych nazwach maszyn wirtualnych, izolowanych w usługach w chmurze Azure][planning-guide-figure-1700]

W tym scenariuszu implementujemy typowy scenariusz systemu szkolenia/demo, w którym pełny scenariusz szkolenia/demo znajduje się w jednej maszynie wirtualnej. Zakładamy, że wdrożenie odbywa się za pośrednictwem szablonów obrazów maszyn wirtualnych. Zakładamy również, że wiele z tych wersji demonstracyjnych/szkoleń maszyn wirtualnych muszą być wdrożone z maszyn wirtualnych o tej samej nazwie. Całe systemy szkoleniowe nie mają łączności z zasobami lokalnymi i są przeciwieństwem wdrożenia hybrydowego.

Założenie jest, że utworzono obraz maszyny Wirtualnej, jak opisano w niektórych sekcjach rozdziału [Przygotowywanie maszyn wirtualnych z SAP dla platformy Azure][planning-guide-5.2] w tym dokumencie.

Sekwencja zdarzeń do zaimplementowania scenariusza wygląda następująco:

##### <a name="powershell"></a>PowerShell

* Tworzenie nowej grupy zasobów dla każdego szkolenia/krajobrazu demonstracyjnego

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Utwórz nowe konto magazynu, jeśli nie chcesz używać dysków zarządzanych

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Utwórz nową sieć wirtualną dla każdego szkoleniowego/demonstracyjnyego krajobrazu, aby umożliwić użycie tej samej nazwy hosta i adresów IP. Sieć wirtualna jest chroniona przez sieciową grupę zabezpieczeń, która zezwala tylko na ruch do portu 3389, aby włączyć dostęp do pulpitu zdalnego i port 22 dla SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Tworzenie nowego publicznego adresu IP, który może służyć do uzyskiwania dostępu do maszyny wirtualnej z Internetu

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Tworzenie nowego interfejsu sieciowego dla maszyny wirtualnej

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Tworzy maszynę wirtualną. W tym scenariuszu każda maszyna wirtualna będzie miała taką samą nazwę. Identyfikator SID SAP wystąpień SAP NetWeaver w tych maszynach wirtualnych będzie taka sama. W ramach grupy zasobów platformy Azure nazwa maszyny wirtualnej musi być unikatowa, ale w różnych grupach zasobów platformy Azure można uruchamiać maszyny wirtualne o tej samej nazwie. Domyślne konto "Administrator" systemu Windows lub "root" dla linuksa jest nieprawidłowe. W związku z tym nowa nazwa użytkownika administratora musi być zdefiniowana razem z hasłem. Należy również zdefiniować rozmiar maszyny Wirtualnej.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcjonalnie dodaj dodatkowe dyski i przywróć niezbędną zawartość. Wszystkie nazwy obiektów blob (adresy URL do obiektów blob) muszą być unikatowe na platformie Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>Interfejs wiersza polecenia

Poniższy przykładowy kod może być używany w systemie Linux. W systemie Windows użyj programu PowerShell w sposób opisany powyżej lub dostosuj przykład do używania %rgName% zamiast $rgName i ustaw zmienną środowiskową za pomocą *zestawu*poleceń systemu Windows .

* Tworzenie nowej grupy zasobów dla każdego szkolenia/krajobrazu demonstracyjnego

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Tworzenie nowego konta magazynu

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Utwórz nową sieć wirtualną dla każdego szkoleniowego/demonstracyjnyego krajobrazu, aby umożliwić użycie tej samej nazwy hosta i adresów IP. Sieć wirtualna jest chroniona przez sieciową grupę zabezpieczeń, która zezwala tylko na ruch do portu 3389, aby włączyć dostęp do pulpitu zdalnego i port 22 dla SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Tworzenie nowego publicznego adresu IP, który może służyć do uzyskiwania dostępu do maszyny wirtualnej z Internetu

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Tworzenie nowego interfejsu sieciowego dla maszyny wirtualnej

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Tworzy maszynę wirtualną. W tym scenariuszu każda maszyna wirtualna będzie miała taką samą nazwę. Identyfikator SID SAP wystąpień SAP NetWeaver w tych maszynach wirtualnych będzie taka sama. W ramach grupy zasobów platformy Azure nazwa maszyny wirtualnej musi być unikatowa, ale w różnych grupach zasobów platformy Azure można uruchamiać maszyny wirtualne o tej samej nazwie. Domyślne konto "Administrator" systemu Windows lub "root" dla linuksa jest nieprawidłowe. W związku z tym nowa nazwa użytkownika administratora musi być zdefiniowana razem z hasłem. Należy również zdefiniować rozmiar maszyny Wirtualnej.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Opcjonalnie dodaj dodatkowe dyski i przywróć niezbędną zawartość. Wszystkie nazwy obiektów blob (adresy URL do obiektów blob) muszą być unikatowe na platformie Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Szablon

Przykładowych szablonów można użyć w repozytorium szablonów szybkiej sieci azure w usłudze GitHub.

* [Prosta maszyna wirtualna z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Prosta maszyna wirtualna systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Maszyna wirtualna z obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementowanie zestawu maszyn wirtualnych komunikujych na platformie Azure

Ten scenariusz niebrydyjny jest typowym scenariuszem do celów szkoleniowych i demonstracyjnych, w którym oprogramowanie reprezentujące scenariusz demo/szkolenia jest rozłożone na wiele maszyn wirtualnych. Różne składniki zainstalowane w różnych maszynach wirtualnych muszą komunikować się ze sobą. Ponownie w tym scenariuszu nie jest wymagana komunikacja sieci lokalnej ani scenariusz międzylokacyjnie.

Ten scenariusz jest rozszerzenie instalacji opisane w rozdziale [Pojedyncza maszyna wirtualna z SAP NetWeaver scenariusz demo/szkolenia][planning-guide-7.1] tego dokumentu. W takim przypadku więcej maszyn wirtualnych zostanie dodanych do istniejącej grupy zasobów. W poniższym przykładzie poziom szkolenia składa się z maszyny wirtualnej SAP ASCS/SCS, maszyny Wirtualnej z uruchomionym systemem dbms i wystąpienia serwera aplikacji SAP.

Przed utworzeniem tego scenariusza należy pomyśleć o podstawowych ustawień, jak już wykonywane w scenariuszu przed.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nazewnictwo grupy zasobów i maszyny wirtualnej

Wszystkie nazwy grup zasobów muszą być unikatowe. Opracowanie własnego schematu nazewnictwa zasobów, takich jak `<rg-name`>-sufiks.

Nazwa maszyny wirtualnej musi być unikatowa w grupie zasobów.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurowanie sieci do komunikacji między różnymi maszynami wirtualnymi

![Zestaw maszyn wirtualnych w sieci wirtualnej platformy Azure][planning-guide-figure-1900]

Aby zapobiec nazywaniu kolizji z klonami tych samych krajobrazów szkoleniowych/demonstracyjnych, należy utworzyć sieć wirtualną platformy Azure dla każdego środowiska. Rozpoznawanie nazw DNS zostanie dostarczone przez platformę Azure lub można skonfigurować własny serwer DNS poza platformą Azure (nie do dalszego omówienia tutaj). W tym scenariuszu nie konfigurujemy własnego systemu DNS. Dla wszystkich maszyn wirtualnych wewnątrz jednej sieci wirtualnej platformy Azure komunikacja za pośrednictwem nazwy hostów zostanie włączona.

Powody, dla których warto oddzielić środowisko szkoleniowe lub demonstratyczne przez sieci wirtualne, a nie tylko grupy zasobów, mogą być następujące:

* Krajobraz SAP w miarę konfiguracji wymaga własnego ad/OpenLDAP, a serwer domeny musi być częścią każdego krajobrazu.  
* Krajobraz SAP w konfiguracji ma składniki, które muszą pracować ze stałymi adresami IP.

Więcej szczegółów na temat sieci wirtualnych platformy Azure i sposobu ich definiowania można znaleźć w [tym artykule][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Wdrażanie maszyn wirtualnych SAP z łącznością sieciową firmy (między lokalizacjami)

Uruchom środowisko SAP i chcesz podzielić wdrożenie między gołym poziomem dla zaawansowanych serwerów DBMS, lokalnie zwirtualizowanymi środowiskami dla warstw aplikacji i mniejszymi 2-warstwowymi skonfigurowanymi systemami SAP i usługą Azure IaaS. Podstawowym założeniem jest to, że systemy SAP w ramach jednego środowiska SAP muszą komunikować się ze sobą i z wieloma innymi składnikami oprogramowania wdrożonymi w firmie, niezależnie od ich formularza wdrażania. Nie powinno być również żadnych różnic wprowadzonych przez formularz wdrożenia dla użytkownika końcowego łączącego się z interfejsem UŻYTKOWNIKA SAP lub innymi interfejsami. Te warunki mogą być spełnione tylko wtedy, gdy mamy lokalne usługi Active Directory/OpenLDAP i USŁUGI DNS rozszerzone na systemy platformy Azure za pośrednictwem łączności lokacja-lokacja/wiele lokacji lub połączeń prywatnych, takich jak Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenariusz krajobrazu SAP

Scenariusz międzypomieszczeń lub hybrydowych może być z grubsza opisane w grafice poniżej:

![Łączność między lokacjami a zasobami platformy Azure][planning-guide-figure-2100]

Minimalnym wymaganiem jest użycie bezpiecznych protokołów komunikacyjnych, takich jak SSL/TLS dla dostępu do przeglądarki lub połączeń opartych na sieci VPN w celu uzyskania dostępu systemowego do usług platformy Azure. Założenie jest takie, że firmy obsługują połączenie sieci VPN między siecią firmową a platformą Azure w inny sposób. Niektóre firmy mogą otwierać wszystkie porty. Niektóre inne firmy mogą chcieć dokładnie otworzyć porty itp.

W poniższej tabeli znajdują się typowe porty komunikacyjne SAP. Zasadniczo wystarczy otworzyć port bramy SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Usługa | Nazwa portu | Przykład `<nn`> = 01 | Domyślny zakres (min-max) | Komentarz |
| --- | --- | --- | --- | --- |
| Dyspozytora |sapdp`<nn>` zobaczyć * |3201 |3200 - 3299 |Sap Dyspozytor, używany przez SAP GUI dla Windows i Java |
| Serwer wiadomości |sapms`<sid`> zobaczyć ** |3600 |darmowe sapms`<anySID`> |sid = SAP-System-ID |
| Brama |sapgw`<nn`> zobaczyć * |3301 |free |Brama SAP, używana do komunikacji CPIC i RFC |
| Router SAP |sapdp99 |3299 |free |Tylko CI (wystąpienie centralne) Nazwy usług można ponownie przypisać w /etc/services do dowolnej wartości po instalacji. |

*) nn = numer wystąpienia SAP

**) sid = SAP-System-ID

Bardziej szczegółowe informacje na temat portów wymaganych dla różnych <https://scn.sap.com/docs/DOC-17124>produktów SAP lub usług przez produkty SAP można znaleźć tutaj .
Za pomocą tego dokumentu powinno być możliwe otwieranie dedykowanych portów w urządzeniu sieci VPN niezbędnych dla określonych produktów i scenariuszy SAP.

Inne środki zabezpieczeń podczas wdrażania maszyn wirtualnych w takim scenariuszu może być utworzenie [sieciowej grupy zabezpieczeń][virtual-networks-nsg] do definiowania reguł dostępu.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Drukowanie na lokalnej drukarce sieciowej z wystąpienia SAP na platformie Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Drukowanie za 1/4 TCP w scenariuszu międzylokacyjnie

Konfigurowanie lokalnych drukarek sieciowych opartych na protoko>>> TCP/IP na maszynie Wirtualnej platformy Azure jest ogólnie takie samo jak w sieci firmowej, przy założeniu, że masz tunel lokacji do lokalizacji sieci VPN lub nawiązane połączenie Usługi ExpressRoute.

---
> ![Windows][Logo_Windows] Windows
>
> W tym celu:
>
> * Niektóre drukarki sieciowe są wyposażone w kreatora konfiguracji, który ułatwia konfigurowanie drukarki na maszynie Wirtualnej platformy Azure. Jeśli z drukarką nie zostało rozesłane żadne oprogramowanie kreatora, ręcznym sposobem skonfigurowania drukarki jest utworzenie nowego portu drukarki TCP/IP.
> * Otwórz Panel sterowania - > urządzenia i drukarki - > Dodaj drukarkę
> * Wybieranie opcji Dodaj drukarkę przy użyciu adresu TCP/IP lub nazwy hosta
> * Wpisz adres IP drukarki
> * Standard portu drukarki 9100
> * W razie potrzeby zainstaluj odpowiedni sterownik drukarki ręcznie.
>
> ![Linux][Logo_Linux] Linux
>
> * jak w przypadku systemu Windows, postępuj zgodnie ze standardową procedurą instalowania drukarki sieciowej
> * wystarczy postępować zgodnie z publicznymi przewodnikami linuksowymi dla [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) lub [Red Hat i Oracle Linux,](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) aby dodawać drukarkę.
>
>

---
![Drukowanie sieciowe][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Drukarka oparta na hoście za oknem SMB (drukarka udostępniona) w scenariuszu międzylokacjowym

Drukarki hosta nie są zgodne z siecią zgodnie z projektem. Ale drukarka oparta na hoście może być współużytkowana między komputerami w sieci, o ile jest podłączona do komputera podłączonego. Połącz sieć firmową od strony lokacji lub usługi ExpressRoute i udostępnij drukarkę lokalną. Protokół SMB używa systemu NetBIOS zamiast usługi DNS jako usługi nazw. Nazwa hosta NetBIOS może się różnić od nazwy hosta DNS. Standardowa obudowa jest taka, że nazwa hosta NetBIOS i nazwa hosta DNS są identyczne. Domena DNS nie ma sensu w obszarze nazw NetBIOS. W związku z tym w obszarze nazw NetBIOS nie można używać w pełni kwalifikowanej nazwy hosta DNS składającej się z nazwy hosta DNS i domeny DNS.

Udział drukarki jest identyfikowany przez unikatową nazwę w sieci:

* Nazwa hosta hosta SMB (zawsze potrzebne).
* Nazwa udziału (zawsze potrzebne).
* Nazwa domeny, jeśli udział drukarki nie znajduje się w tej samej domenie co system SAP.
* Ponadto do uzyskania dostępu do udziału drukarki może być wymagana nazwa użytkownika i hasło.

Instrukcje:

---
> ![Windows][Logo_Windows] Windows
>
> Udostępnij lokalną drukarkę.
> Na maszynie Wirtualnej platformy Azure otwórz Eksploratora Windows i wpisz nazwę udziału drukarki.
> Kreator instalacji drukarki przeprowadzi Cię przez proces instalacji.
>
> ![Linux][Logo_Linux] Linux
>
> Oto kilka przykładów dokumentacji dotyczącej konfigurowania drukarek sieciowych w systemie Linux lub w tym rozdział dotyczący drukowania w systemie Linux. Będzie działać w ten sam sposób na maszynie Wirtualnej systemu Azure z systemem Linux, o ile maszyna wirtualna jest częścią sieci VPN:
>
> * Sles<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL lub Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Drukarka USB (przekazywanie drukarek)

Na platformie Azure możliwość usług pulpitu zdalnego, aby zapewnić użytkownikom dostęp do swoich urządzeń drukarki lokalnej w sesji zdalnej nie jest dostępna.

---
> ![Windows][Logo_Windows] Windows
>
> Więcej informacji na temat drukowania <https://technet.microsoft.com/library/jj590748.aspx>za pomocą systemu Windows można znaleźć tutaj: .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integracja systemów SAP Azure z systemem korekcji i transportu (TMS) w różnych pomieszczeniach

Sap Change and Transport System (TMS) musi być skonfigurowany do eksportowania i importowania żądań transportu między systemami w krajobrazie. Zakładamy, że wystąpienia programistyczne systemu SAP (DEV) znajdują się na platformie Azure, podczas gdy zapewnienie jakości (QA) i systemy produkcyjne (PRD) są lokalne. Ponadto zakładamy, że istnieje centralny katalog transportowy.

##### <a name="configuring-the-transport-domain"></a>Konfigurowanie domeny transportu

Skonfiguruj domenę transportu w systemie wyznaczonym jako kontroler domeny transportu, zgodnie z [opisem w temacie Konfigurowanie kontrolera domeny transportu](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Zostanie utworzony użytkownik systemu TMSADM i zostanie wygenerowany wymagany cel docelowy RFC. Można sprawdzić te połączenia RFC przy użyciu transakcji SM59. Rozdzielczość nazwy hosta musi być włączona w całej domenie transportu.

Instrukcje:

* W naszym scenariuszu zdecydowaliśmy, że lokalnym systemem QAS będzie kontroler domeny CTS. Wywołanie transakcji STMS. Zostanie wyświetlone okno dialogowe TMS. Zostanie wyświetlone okno dialogowe Konfigurowanie domeny transportu. (To okno dialogowe jest wyświetlane tylko wtedy, gdy domena transportu nie została jeszcze skonfigurowana).
* Upewnij się, że automatycznie utworzony użytkownik TMSADM jest autoryzowany (SM59 -> ABAP Connection -> TMSADM@E61.DOMAIN_E61 -> Details -> Utilities(M) -> Authorization Test). Początkowy ekran transakcji STMS powinien pokazać, że ten system SAP działa teraz jako kontroler domeny transportu, jak pokazano poniżej:

![Początkowy ekran transakcji STMS na kontrolerze domeny][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Uwzględnienie systemów SAP w domenie transportu

Sekwencja dołączania systemu SAP do domeny transportu wygląda następująco:

* W systemie DEV na platformie Azure przejdź do systemu transportu (klient 000) i wywołaj usługę STMS. Z okna dialogowego wybierz pozycję Inna konfiguracja i kontynuuj dołączanie systemu do domeny. Określ kontroler domeny jako hosta docelowego[(w tym systemy SAP w domenie transportu).](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm) System oczekuje teraz na uwzględnienie w domenie transportu.
* Ze względów bezpieczeństwa należy wrócić do kontrolera domeny, aby potwierdzić żądanie. Wybierz pozycję Omówienie systemu i Zatwierdź system oczekujący. Następnie potwierdź monit, a konfiguracja zostanie rozesłana.

Ten system SAP zawiera teraz niezbędne informacje o wszystkich innych systemach SAP w domenie transportu. W tym samym czasie dane adresowe nowego systemu SAP są wysyłane do wszystkich innych systemów SAP, a system SAP jest wprowadzany do profilu transportu programu kontroli transportu. Sprawdź, czy kontrolery RFC i dostęp do katalogu transportu domeny działają.

Kontynuuj konfigurację systemu transportowego w zwykły sposób, jak opisano w dokumentacji [Change and Transport System](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Instrukcje:

* Upewnij się, że lokalny system STMS jest poprawnie skonfigurowany.
* Upewnij się, że nazwa hosta kontrolera domeny transportu może być rozpoznawana przez maszynę wirtualną na platformie Azure i wizie vice.
* Wywołanie transakcji STMS -> Inne konfiguracje -> uwzględnić system w domenie.
* Potwierdź połączenie w lokalnym systemie TMS.
* Skonfiguruj trasy transportu, grupy i warstwy w zwykły sposób.

W scenariuszach połączonych między lokacjami między lokalami opóźnienie między środowiskiem lokalnym a platformą Azure nadal może być znaczne. Jeśli będziemy śledzić sekwencję transportu obiektów poprzez rozwój i testy systemów do produkcji lub myśleć o zastosowaniu transportów lub pakietów wsparcia do różnych systemów, zdajesz sobie sprawę, że w zależności od lokalizacji centralnego katalogu transportu, niektóre systemy napotykają duże opóźnienia odczytu lub zapisu danych w centralnym katalogu transportu. Sytuacja jest podobna do konfiguracji krajobrazowych SAP, w których różne systemy są rozłożone w różnych centrach danych ze znaczną odległością między centrami danych.

Aby obejść takie opóźnienie i mieć systemy działają szybko w odczytie lub zapisie do lub z katalogu transportu, można skonfigurować dwie domeny transportu STMS (jeden dla lokalnych i jeden z systemami na platformie Azure i połączyć domen transportu. Proszę sprawdzić tę dokumentację, która wyjaśnia zasady leżące u <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>podstaw tej koncepcji w SAP TMS: .

Instrukcje:

* Konfigurowanie domeny transportu w każdej lokalizacji (lokalnej i azure) przy użyciu usługi STMS transakcji<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Połącz domeny z łączem domeny i potwierdź łącze między dwiema domenami.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Rozłóż konfigurację do połączonego systemu.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Ruch RFC między wystąpieniami SAP znajdującymi się na platformie Azure i lokalnie (między lokalizacjami)

Ruch RFC między systemami, które są lokalne i na platformie Azure musi działać. Aby skonfigurować transakcję wywołania połączenia SM59 w systemie źródłowym, w którym należy zdefiniować połączenie RFC do systemu docelowego. Konfiguracja jest podobna do standardowej konfiguracji połączenia RFC.

Zakładamy, że w scenariuszu między środowiskami, maszyny wirtualne, które uruchamiają systemy SAP, które muszą komunikować się ze sobą, znajdują się w tej samej domenie. W związku z tym konfiguracja połączenia RFC między systemami SAP nie różni się od kroków instalacji i danych wejściowych w scenariuszach lokalnych.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Uzyskiwanie dostępu do lokalnych udziałów plików z wystąpień SAP znajdujących się na platformie Azure lub odwrotnie

Wystąpienia SAP znajdujące się na platformie Azure muszą uzyskiwać dostęp do udziałów plików, które znajdują się w pomieszczeniach firmowych. Ponadto lokalne wystąpienia SAP muszą uzyskać dostęp do udziałów plików, które znajdują się na platformie Azure. Aby włączyć udziały plików, należy skonfigurować uprawnienia i opcje udostępniania w systemie lokalnym. Upewnij się, że otworzysz porty w sieci VPN lub połączeniu Usługi ExpressRoute między platformą Azure a centrum danych.

## <a name="supportability"></a>Możliwości obsługi

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Rozszerzenie platformy Azure dla systemu SAP

Aby przekazać część informacji o infrastrukturze platformy Azure o krytycznych systemach SAP do wystąpień agenta hosta SAP, zainstalowanych na maszynach wirtualnych, rozszerzenie platformy Azure (VM) dla sap musi zostać zainstalowane dla wdrożonych maszyn wirtualnych. Ponieważ wymagania sap były specyficzne dla aplikacji SAP, firma Microsoft postanowiła nie implementować ogólnie wymaganych funkcji na platformie Azure, ale pozostawić klientom wdrożenie niezbędnego rozszerzenia maszyny wirtualnej i konfiguracji na uruchomionych maszynach wirtualnych na platformie Azure. Jednak wdrażanie i zarządzanie cyklem życia rozszerzenia maszyny wirtualnej platformy Azure dla sap będzie w większości zautomatyzowane przez platformę Azure.

#### <a name="solution-design"></a>Projekt rozwiązania

Rozwiązanie opracowane w celu umożliwienia agentowi hosta SAP uzyskiwania wymaganych informacji jest oparte na architekturze platformy Azure VM Agent i rozszerzenie. Ideą platformy Agent maszyny Wirtualnej platformy Azure i rozszerzenia jest umożliwienie instalacji aplikacji dostępnych w galerii rozszerzenia maszyny Wirtualnej platformy Azure w ramach maszyny Wirtualnej. Ideą tej koncepcji jest umożliwienie (w przypadkach takich jak rozszerzenie platformy Azure dla SAP), wdrożenie specjalnych funkcji na maszynie wirtualnej i konfiguracja takiego oprogramowania w czasie wdrażania.

"Agent maszyny wirtualnej platformy Azure", który umożliwia obsługę określonych rozszerzeń maszyn wirtualnych platformy Azure w ramach maszyny wirtualnej jest wstrzykiwany do maszyn wirtualnych systemu Windows domyślnie podczas tworzenia maszyn wirtualnych w witrynie Azure portal. W przypadku SUSE, Red Hat lub Oracle Linux agent maszyny Wirtualnej jest już częścią obrazu portalu Azure Marketplace. W przypadku, gdy można przekazać maszynę wirtualną z systemem Linux z lokalnego do platformy Azure agent maszyny Wirtualnej musi być zainstalowany ręcznie.

Podstawowe bloki konstrukcyjne rozwiązania zapewniającego agentowi sap hosta platformy SAP informacje o infrastrukturze platformy Azure wygląda następująco:

![Składniki rozszerzenia platformy Microsoft Azure][planning-guide-figure-2400]

Jak pokazano na powyższym diagramie blokowym, jedna część rozwiązania jest hostowana w obrazie maszyny wirtualnej platformy Azure i galerii rozszerzeń platformy Azure, które jest globalnie replikowanym repozytorium zarządzanym przez usługę Azure Operations. Jest odpowiedzialny za wspólny zespół SAP/MS pracujący nad implementacją platformy Azure sap do pracy z usługą Azure Operations, aby opublikować nowe wersje rozszerzenia platformy Azure dla SAP.

Po wdrożeniu nowej maszyny Wirtualnej systemu Windows agent maszyny wirtualnej platformy Azure jest automatycznie dodawany do maszyny Wirtualnej. Funkcja tego agenta jest koordynowanie ładowania i konfiguracji rozszerzenia platformy Azure maszyn wirtualnych. W przypadku maszyn wirtualnych z systemem Linux agent maszyny wirtualnej platformy Azure jest już częścią obrazu systemu operacyjnego portalu Azure Marketplace.

Istnieje jednak krok, który nadal musi być wykonany przez klienta. Jest to włączenie i konfiguracja kolekcji wydajności. Proces związany z konfiguracją jest zautomatyzowany przez skrypt programu PowerShell lub polecenie interfejsu wiersza polecenia. Skrypt programu PowerShell można pobrać w Centrum skryptów platformy Microsoft Azure zgodnie z opisem w [Przewodniku wdrażania][deployment-guide].

Ogólna architektura rozszerzenia platformy Azure dla sap wygląda następująco:

![rozszerzenie platformy Azure dla sap ][planning-guide-figure-2500]

**Aby uzyskać szczegółowe instrukcje i szczegółowe instrukcje korzystania z tych poleceń cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia podczas wdrożeń, postępuj zgodnie z instrukcjami podanymi w [Przewodniku wdrażania][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integracja wystąpienia SAP z usługą SAProuter

Sap wystąpień działających na platformie Azure muszą być dostępne z SAProuter, jak również.

![Połączenie sieciowe SAP-Router][planning-guide-figure-2600]

A SAProuter umożliwia komunikację TCP/IP między systemami uczestniczącymi, jeśli nie ma bezpośredniego połączenia IP. Zapewnia to tę zaletę, że na poziomie sieci nie jest konieczne połączenie end-to-end między partnerami komunikacyjnymi. SAProuter domyślnie nasłuchuje na porcie 3299.
Aby połączyć wystąpienia SAP za pośrednictwem SAProuter, należy podać ciąg SAProuter i nazwę hosta z każdą próbą nawiązania połączenia.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Do tej pory głównym tematem dokumentu był SAP NetWeaver w ogóle lub stos SAP NetWeaver ABAP. W tej małej sekcji znajdują się szczegółowe zagadnienia dotyczące stosu SAP Java. Jedną z najważniejszych aplikacji opartych wyłącznie na sap NetWeaver Java jest SAP Enterprise Portal. Inne aplikacje oparte na SAP NetWeaver, takie jak SAP PI i SAP Solution Manager, używają stosów SAP NetWeaver ABAP i Java. W związku z tym z pewnością istnieje potrzeba rozważenia konkretnych aspektów związanych ze stosem SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

Konfiguracja portalu SAP na maszynie wirtualnej platformy Azure nie różni się od instalacji lokalnej, jeśli wdrażasz w scenariuszach międzylokacyjnych. Ponieważ dns jest wykonywany przez lokalnie, ustawienia portu poszczególnych wystąpień można wykonać jako skonfigurowane lokalnie. Zalecenia i ograniczenia opisane w tym dokumencie do tej pory mają zastosowanie do aplikacji, takich jak SAP Enterprise Portal lub SAP NetWeaver Java stosu w ogóle.

![Odsłonięty portal SAP][planning-guide-figure-2700]

Specjalny scenariusz wdrażania przez niektórych klientów to bezpośrednia ekspozycja sap enterprise portal do Internetu, podczas gdy host maszyny wirtualnej jest podłączony do sieci firmy za pośrednictwem tunelu sieci VPN lokacja lokacja lub usługi ExpressRoute. W takim scenariuszu należy upewnić się, że określone porty są otwarte i nie są blokowane przez zaporę lub sieciową grupę zabezpieczeń. 

Początkowa wartość URI portalu`<Portalserver` to http(s):>:5XX00/irj, gdzie port jest <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>tworzony zgodnie z dokumentami SAP w .

![Konfiguracja punktu końcowego][planning-guide-figure-2800]

Jeśli chcesz dostosować adres URL i/lub porty usługi SAP Enterprise Portal, zapoznaj się z tą dokumentacją:

* [Zmień adres URL portalu](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Zmienianie domyślnych numerów portów, numery portów portalu](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Wysoka dostępność (HA) i odzyskiwanie po awarii (DR) dla sap NetWeaver uruchomionego na maszynach wirtualnych platformy Azure

### <a name="definition-of-terminologies"></a>Definicja terminologii

Termin **wysoka dostępność (HA)** jest zazwyczaj związany z zestawem technologii, które minimalizują zakłócenia it, zapewniając ciągłość działania usług IT za pośrednictwem nadmiarowych, odpornych na uszkodzenia lub składników chronionych przed awarią w **tym samym** centrum danych. W naszym przypadku w ramach jednego regionu platformy Azure.

**Odzyskiwanie po awarii (DR)** jest również ukierunkowane na minimalizację zakłóceń w usługach IT i ich odzyskiwanie, ale w **różnych** centrach danych, które zwykle znajdują się setki kilometrów od hotelu. W naszym przypadku zwykle między różnymi regionami platformy Azure w tym samym regionie geopolitycznym lub zgodnie z ustaleniami przez Ciebie jako klienta.

### <a name="overview-of-high-availability"></a>Omówienie wysokiej dostępności

Możemy podzielić dyskusję na temat wysokiej dostępności sap na platformie Azure na dwie części:

* **Infrastruktura platformy Azure wysoka dostępność**, na przykład ha obliczeń (maszyny wirtualne), sieć, magazyn itp.
* **Wysoka dostępność aplikacji SAP**, na przykład wysokiej dostępności składników oprogramowania SAP:
  * Serwery aplikacji SAP
  * Wystąpienie SAP ASCS/SCS
  * Serwer DB

i jak można go łączyć z wysokiej haw infrastruktury platformy Azure.

Wysoka dostępność sap na platformie Azure ma pewne różnice w porównaniu do wysokiej dostępności SAP w lokalnym środowisku fizycznym lub wirtualnym. W poniższym dokumencie firmy SAP opisano standardowe konfiguracje SAP <https://scn.sap.com/docs/DOC-44415>High Availability w środowiskach zwirtualizowanych w systemie Windows: . Nie ma sapinst zintegrowanej konfiguracji SAP-HA dla Linuksa, jak istnieje dla systemu Windows. W odniesieniu do SAP HA lokalnie dla <https://scn.sap.com/docs/DOC-8541>Linuksa znaleźć więcej informacji tutaj: .

### <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure

Obecnie istnieje jednolita umowy SLA o pojedynczej maszynie wirtualnej w wysokości 99,9%. Aby zorientować się, jak może wyglądać dostępność pojedynczej maszyny Wirtualnej, można utworzyć produkt różnych <https://azure.microsoft.com/support/legal/sla/>dostępnych zestawów SLA platformy Azure: .

Podstawą obliczeń jest 30 dni w miesiącu lub 43200 minut. W związku z tym 0.05% przestój odpowiada 21.6 minut. Jak zwykle dostępność różnych usług będzie mnożyć w następujący sposób:

(Usługa dostępności #1/100) * (usługa dostępności #2/100) * (usługa dostępności #3/100) 

Jak:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 lub ogólna dostępność 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Wysoka dostępność maszyny wirtualnej

Istnieją dwa typy zdarzeń platformy Azure, które mogą mieć wpływ na dostępność maszyn wirtualnych: planowana konserwacja i nieplanowana konserwacja.

* Zdarzenia planowanej konserwacji to okresowe aktualizacje tworzone przez firmę Microsoft dla podstawowej platformy Azure w celu zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy działania maszyn wirtualnych.
* Zdarzenia nieplanowanej konserwacji występują w przypadku wystąpienia awarii sprzętu lub infrastruktury fizycznej zawierającej maszynę wirtualną. Mogą być to awarie sieci lokalnej, błędy na dysku lokalnym lub inne awarie na poziomie regału. Po wykryciu takiego błędu platforma Azure automatycznie migruje maszynę wirtualną ze złej kondycji serwera fizycznego hostowanego maszyny wirtualnej na zdrowy serwer fizyczny. Te zdarzenia występują rzadko, ale mogą również spowodować ponowne uruchomienie maszyny wirtualnej.

Więcej szczegółów można znaleźć w tej dokumentacji:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage

Dane na koncie usługi Microsoft Azure Storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności, spełniając umowy SLA usługi Azure Storage nawet w przypadku przejściowych awarii sprzętu.

Ponieważ usługa Azure Storage domyślnie przechowuje trzy obrazy danych, RAID5 lub RAID1 na wielu dyskach platformy Azure nie są konieczne.

Więcej szczegółów można znaleźć w tym artykule:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Korzystanie z ponownego uruchamiania maszyny Wirtualnej infrastruktury platformy Azure w celu uzyskania większej dostępności aplikacji SAP

Jeśli zdecydujesz się nie używać funkcji, takich jak klastrowanie trybu failover systemu Windows Server (WSFC) lub Rozrusznik w systemie Linux (obecnie obsługiwany tylko dla SLES 12 lub nowszego), ponowne uruchomienie maszyny Wirtualnej platformy Azure jest wykorzystywane do ochrony systemu SAP przed planowanymi i nieplanowanymi przestojami platformy Azure infrastruktury serwera fizycznego i ogólnej podstawowej platformy Azure.

> [!NOTE]
> Należy wspomnieć, że ponowne uruchomienie maszyny wirtualnej platformy Azure chroni przede wszystkim maszyny wirtualne i nie aplikacje. Ponowne uruchomienie maszyny Wirtualnej nie oferuje wysokiej dostępności dla aplikacji SAP, ale oferuje pewien poziom dostępności infrastruktury, a zatem pośrednio wyższą dostępność systemów SAP. Nie ma również umowy SLA na czas, który zajmie ponowne uruchomienie maszyny wirtualnej po planowanej lub nieplanowanej awarii hosta. W związku z tym ta metoda wysokiej dostępności nie jest odpowiednia dla krytycznych składników systemu SAP, takich jak (A)SCS lub DBMS.
>
>

Innym ważnym elementem infrastruktury dla wysokiej dostępności jest magazyn. Na przykład usługa Azure Storage SLA jest dostępność 99,9 %. Jeśli jeden wdraża wszystkie maszyny wirtualne z dyskami do jednego konta usługi Azure Storage, potencjalna niedostępność usługi Azure Storage spowoduje niedostępność wszystkich maszyn wirtualnych, które są umieszczane na tym koncie usługi Azure Storage, a także wszystkie składniki SAP działające wewnątrz tych maszyn wirtualnych.  

Zamiast umieszczać wszystkie maszyny wirtualne w jednym koncie usługi Azure Storage, można również użyć dedykowanych kont magazynu dla każdej maszyny Wirtualnej i w ten sposób zwiększyć ogólną dostępność aplikacji maszyny Wirtualnej i SAP przy użyciu wielu niezależnych kont usługi Azure Storage.

Dyski zarządzane platformy Azure są automatycznie umieszczane w domenie błędów maszyny wirtualnej, do których są dołączone. Jeśli umieścisz dwie maszyny wirtualne w zestawie dostępności i użyjesz dysków zarządzanych, platforma zajmie się dystrybucją dysków zarządzanych do różnych domen błędów. Jeśli planujesz używać magazynu w wersji Premium, zdecydowanie zalecamy również korzystanie z funkcji Zarządzaj dyskami.

Przykładowa architektura systemu SAP NetWeaver, który używa wysokiej infrastruktury platformy Azure i kont magazynu może wyglądać następująco:

![Wykorzystanie wysokiej dostępności infrastruktury platformy Azure w celu uzyskania większej dostępności aplikacji SAP][planning-guide-figure-2900]

Przykładowa architektura systemu SAP NetWeaver, który używa wysokiej sieci operacyjnej infrastruktury platformy Azure i dysków zarządzanych, może wyglądać następująco:

![Wykorzystanie wysokiej dostępności infrastruktury platformy Azure w celu uzyskania większej dostępności aplikacji SAP][planning-guide-figure-2901]

W przypadku krytycznych komponentów SAP osiągnęliśmy do tej pory następujące osiągnięcia:

* Wysoka dostępność serwerów aplikacji SAP (AS)

  Wystąpienia serwera aplikacji SAP są składnikami nadmiarowymi. Każde wystąpienie SAP AS jest wdrażane na własnej maszynie wirtualnej, która jest uruchomiona w innej domenie błędów i uaktualnień platformy Azure (zobacz rozdziały [Domeny błędów][planning-guide-3.2.1] i [domeny uaktualnienia).][planning-guide-3.2.2] Jest to zapewnione przy użyciu zestawów dostępności platformy Azure (patrz rozdział [Zestawy dostępności platformy Azure).][planning-guide-3.2.3] Potencjalna planowana lub nieplanowana niedostępność domeny błędu lub uaktualnienia platformy Azure spowoduje niedostępność ograniczonej liczby maszyn wirtualnych z ich wystąpieniami SAP AS.

  Każde wystąpienie SAP AS jest umieszczane na własnym koncie usługi Azure Storage — potencjalna niedostępność jednego konta usługi Azure Storage spowoduje niedostępność tylko jednej maszyny Wirtualnej z jego wystąpieniem SAP AS. Należy jednak pamiętać, że istnieje limit kont usługi Azure Storage w ramach jednej subskrypcji platformy Azure. Aby zapewnić automatyczne uruchamianie (A)wystąpienia SCS po ponownym uruchomieniu maszyny Wirtualnej, upewnij się, że parametr Autostart w (A)profil startu wystąpienia SCS opisany w rozdziale [Korzystanie z autostartu dla wystąpień SAP][planning-guide-11.5].
  Aby uzyskać więcej informacji, zapoznaj się również z [rozdziałem Wysoka dostępność serwerów aplikacji SAP.][planning-guide-11.4.1]

  Nawet jeśli używasz dysków zarządzanych, te dyski są również przechowywane na koncie usługi Azure Storage i mogą być niedostępne w przypadku awarii magazynu.

* *Wyższa* Dostępność wystąpienia SAP (A)SCS

  W tym miejscu możemy korzystać z ponownego uruchomienia maszyny Wirtualnej platformy Azure, aby chronić maszynę wirtualną za pomocą zainstalowanego wystąpienia SAP (A)SCS. W przypadku planowanych lub nieplanowanych przestojów platformy Azure severs maszyny wirtualne zostaną ponownie uruchomione na innym dostępnym serwerze. Jak wspomniano wcześniej, ponowne uruchomienie maszyny wirtualnej platformy Azure przede wszystkim chroni maszyny wirtualne i nie aplikacje, w tym przypadku (A)SCS wystąpienie. Za pośrednictwem ponownego uruchamiania maszyny Wirtualnej osiągniemy pośrednio wyższą dostępność wystąpienia SAP (A)SCS. Aby ubezpieczyć automatyczne uruchamianie (A)wystąpienia SCS po ponownym uruchomieniu maszyny Wirtualnej, upewnij się, że parametr Autostart w (A)profil startowy wystąpienia SCS opisany w rozdziale [Korzystanie z autostartu dla wystąpień SAP][planning-guide-11.5]. Oznacza to, że (A)SCS wystąpienie jako pojedynczy punkt awarii (SPOF) uruchomione w jednej maszynie wirtualnej będzie czynnikiem determinującym dla dostępności całego środowiska SAP.

* *Wyższa* Dostępność serwera DBMS

  W tym miejscu, podobnie jak w przypadku wystąpienia SAP (A)SCS, używamy ponownego uruchamiania maszyny Wirtualnej platformy Azure do ochrony maszyny Wirtualnej za pomocą zainstalowanego oprogramowania DBMS i osiągamy wyższą dostępność oprogramowania DBMS za pośrednictwem ponownego uruchamiania maszyny Wirtualnej.
  DbMS uruchomiony w jednej maszynie wirtualnej jest również SPOF i jest czynnikiem decydującym o dostępności całego środowiska SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Wysoka dostępność aplikacji SAP na platformie Azure IaaS

Aby uzyskać pełną wysoką dostępność systemu SAP, musimy chronić wszystkie krytyczne składniki systemu SAP, na przykład nadmiarowe serwery aplikacji SAP i unikatowe składniki (na przykład pojedynczy punkt awarii), takie jak wystąpienie SAP (A)SCS i dbms.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Wysoka dostępność serwerów aplikacji SAP

W przypadku wystąpień serwerów aplikacji SAP/okna dialogowego nie jest konieczne myślenie o określonym rozwiązaniu o wysokiej dostępności. Wysoka dostępność jest osiągana przez nadmiarowość, a tym samym posiadanie ich wystarczającej ilości na różnych maszynach wirtualnych. Wszystkie powinny być umieszczone w tym samym zestawie dostępności platformy Azure, aby uniknąć, że maszyny wirtualne mogą być aktualizowane w tym samym czasie podczas planowanych przestojów konserwacji. Podstawowa funkcjonalność, która opiera się na różnych domenach uaktualnień i błędów w jednostce skalowania platformy Azure, została już wprowadzona w rozdziale [Domeny uaktualnienia][planning-guide-3.2.2]. Zestawy dostępności platformy Azure zostały przedstawione w rozdziale [Zestawy dostępności platformy Azure][planning-guide-3.2.3] tego dokumentu.

Nie ma nieskończonej liczby domen błędów i uaktualnień, które mogą być używane przez zestaw dostępności platformy Azure w jednostce skalowania platformy Azure. Oznacza to, że umieszczenie wielu maszyn wirtualnych w jednym zestawie dostępności, prędzej czy później więcej niż jedna maszyna wirtualna kończy się w tej samej domenie błędów lub uaktualnienia.

Wdrażanie kilku wystąpień serwera aplikacji SAP w dedykowanych maszynach wirtualnych i przy założeniu, że mamy pięć domen uaktualnienia, na końcu pojawia się następujący obraz. Rzeczywista maksymalna liczba błędów i aktualizacji domen w ramach zestawu dostępności może ulec zmianie w przyszłości:

![Ha serwerów aplikacji SAP na platformie Azure][planning-guide-figure-3000]

Więcej szczegółów można znaleźć w tej dokumentacji:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Wysoka dostępność usług SAP Central na platformie Azure

Aby uzyskać architekturę wysokiej dostępności usług SAP Central Services na platformie Azure, zapoznaj się z [artykułem Architektura wysokiej dostępności i scenariusze dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) jako informacje o wpisie. Artykuł wskazuje bardziej szczegółowe opisy dla poszczególnych systemów operacyjnych.

#### <a name="high-availability-for-the-sap-database-instance"></a>Wysoka dostępność wystąpienia bazy danych SAP

Typowa konfiguracja usługi SAP DBMS HA jest oparta na dwóch maszynach wirtualnych dbms, w których funkcja wysokiej dostępności systemu DBMS jest używana do replikowania danych z aktywnego wystąpienia DBMS do drugiej maszyny wirtualnej w pasywne wystąpienie dbms.

Wysoka dostępność i funkcje odzyskiwania po awarii dla systemu dbms w ogóle, jak również konkretne dbms są opisane w [Przewodniku wdrażania dbms][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Wysoka dostępność kompletnego systemu SAP

Oto dwa przykłady kompletnej architektury SAP NetWeaver HA na platformie Azure — jeden dla systemu Windows i jeden dla systemu Linux.

Tylko dyski niezarządzane: pojęcia opisane poniżej mogą wymagać naruszenia zabezpieczeń nieco podczas wdrażania wielu systemów SAP, a liczba wdrożonych maszyn wirtualnych przekracza maksymalny limit kont magazynu na subskrypcję. W takich przypadkach wirtualne maszyny wirtualne muszą być łączone w ramach jednego konta magazynu. Zwykle można to zrobić, łącząc veds z SAP warstwy aplikacji maszyn wirtualnych różnych systemów SAP.  Połączyliśmy również różne dyski wirtualne różnych maszyn wirtualnych DBMS różnych systemów SAP w jednym koncie usługi Azure Storage. W ten sposób należy pamiętać o granicach usług<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>We/Wy dla kont usługi Azure Storage ( )


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] Ha w systemie Windows

![Architektura wysokiej klasy aplikacji SAP NetWeaver z programem SQL Server w usłudze Azure IaaS][planning-guide-figure-3200]

Następujące konstrukcje platformy Azure są używane dla systemu SAP NetWeaver, aby zminimalizować wpływ problemów z infrastrukturą i poprawek hosta:

* Kompletny system jest wdrażany na platformie Azure (wymagane — warstwa DBMS, (A)wystąpienie SCS i pełna warstwa aplikacji muszą działać w tej samej lokalizacji).
* Kompletny system działa w ramach jednej subskrypcji platformy Azure (wymagane).
* Kompletny system działa w ramach jednej sieci wirtualnej platformy Azure (wymagane).
* Oddzielenie maszyn wirtualnych jednego systemu SAP na trzy zestawy dostępności jest możliwe nawet w przypadku wszystkich maszyn wirtualnych należących do tej samej sieci wirtualnej.
* Każda warstwa (na przykład DBMS, ASCS, Serwery aplikacji) musi używać dedykowanego zestawu dostępności.
* Wszystkie maszyny wirtualne z uruchomionymi wystąpieniami DBMS jednego systemu SAP znajdują się w jednym zestawie dostępności. Zakładamy, że istnieje więcej niż jedna maszyna wirtualna z uruchomionymi wystąpieniami DBMS na system, ponieważ używane są natywne funkcje wysokiej dostępności DBMS, takie jak SQL Server AlwaysOn lub Oracle Data Guard.
* Wszystkie maszyny wirtualne z uruchomionymi wystąpieniami DBMS używają własnego konta magazynu. Pliki danych i dzienników DBMS są replikowane z jednego konta magazynu do innego konta magazynu przy użyciu funkcji wysokiej dostępności DBMS, które synchronizują dane. Niedostępność jednego konta magazynu spowoduje niedostępność jednego węzła klastra systemu SQL Windows, ale nie całej usługi programu SQL Server.
* Wszystkie maszyny wirtualne uruchomione (A)SCS wystąpienia jednego systemu SAP są w jednym zestawie dostępności. Klaster trybu failover systemu Windows Server (WSFC) jest skonfigurowany wewnątrz tych maszyn wirtualnych w celu ochrony (A)wystąpienie SCS.
* Wszystkie uruchomione maszyny wirtualne (A)wystąpienia SCS używają własnego konta magazynu. 1lit. Pliki wystąpienia SCS i folder globalny SAP są replikowane z jednego konta magazynu na inne konto magazynu przy użyciu replikacji SIOS DataKeeper. Niedostępność jednego konta magazynu spowoduje niedostępność jednego węzła klastra systemu Windows (A)SCS, ale nie całej usługi SCS.
* Wszystkie maszyny wirtualne reprezentujące warstwę serwera aplikacji SAP znajdują się w trzecim zestawie dostępności.
* WSZYSTKIE maszyny wirtualne z uruchomionymi serwerami aplikacji SAP używają własnego konta magazynu. Niedostępność jednego konta magazynu spowoduje niedostępność jednego serwera aplikacji SAP, na którym nadal działają inne serwery aplikacji SAP.

Na poniższym rysunku przedstawiono ten sam poziom przy użyciu dysków zarządzanych.

![Architektura wysokiej klasy aplikacji SAP NetWeaver z programem SQL Server w usłudze Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA na Linuksie

Architektura sap ha na linuksie na platformie Azure jest w zasadzie taka sama jak dla systemu Windows, jak opisano powyżej. Lista obsługiwanych rozwiązań o wysokiej dostępności można znaleźć w obiekcie SAP Note [1928533.]

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Korzystanie z autostartu dla wystąpień SAP

Firma SAP zaoferowała funkcję uruchamiania wystąpień SAP natychmiast po uruchomieniu systemu operacyjnego w maszynie wirtualnej. Dokładne kroki zostały udokumentowane w artykule [1909114]bazy wiedzy SAP. Jednak SAP nie zaleca się już używać tego ustawienia, ponieważ nie ma kontroli w kolejności ponownego uruchamiania wystąpienia, przy założeniu, że więcej niż jedna maszyna wirtualna została naruszona lub wiele wystąpień uruchomionych na maszynę wirtualną. Zakładając, że typowy scenariusz platformy Azure jednego wystąpienia serwera aplikacji SAP na maszynie Wirtualnej i przypadku pojedynczej maszyny Wirtualnej po pewnym czasie zostanie ponownie uruchomiony, Autostart nie jest krytyczny i można go włączyć, dodając ten parametr:

    Autostart = 1

Do profilu początkowego wystąpienia SAP ABAP i/lub Java.

> [!NOTE]
> Autostart Parametr może mieć pewne upadki, jak również. Bardziej szczegółowo parametr wyzwala uruchomienie wystąpienia SAP ABAP lub Java po uruchomieniu powiązanej usługi Windows/Linux wystąpienia. Z pewnością jest tak, gdy system operacyjny uruchamia się. Jednak ponowne uruchomienie usług SAP są również częstą rzeczą dla funkcji zarządzania cyklem życia oprogramowania SAP, takich jak SUMA lub inne aktualizacje lub uaktualnienia. Te funkcje nie oczekują wystąpienia do ponownego uruchomienia automatycznie w ogóle. W związku z tym autostart parametr powinien być wyłączony przed uruchomieniem takich zadań. Parametr Autostart również nie powinien być używany dla wystąpień SAP, które są klastrowane, takich jak ASCS/SCS/CI.
>
>

Zobacz dodatkowe informacje dotyczące autostartu dla wystąpień SAP tutaj:

* [Start/Stop SAP wraz z uruchomieniem/zatrzymaniem serwera Unix](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Uruchamianie i zatrzymywanie agentów zarządzania SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Jak włączyć automatyczne uruchamianie bazy danych HANA](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Większe 3-warstwowe systemy SAP
Aspekty wysokiej dostępności konfiguracji SAP 3-warstwowych zostały omówione już we wcześniejszych sekcjach. Ale co z systemami, w których wymagania serwera DBMS są zbyt duże, aby można go było zlokalizować na platformie Azure, ale warstwa aplikacji SAP może zostać wdrożona na platformie Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Lokalizacja konfiguracji 3-warstwowych SAP
Nie jest obsługiwana do dzielenia warstwy aplikacji, sam lub warstwy aplikacji i dbms między lokalnie i platformy Azure. System SAP jest całkowicie wdrożony lokalnie lub na platformie Azure. Nie jest również obsługiwane, aby niektóre serwery aplikacji były uruchamiane lokalnie, a niektóre inne na platformie Azure. To jest punkt wyjścia dyskusji. Firma Microsoft nie obsługuje również, aby składniki dbms systemu SAP i warstwy serwera aplikacji SAP wdrożone w dwóch różnych regionach platformy Azure. Na przykład dbms w zachodnie stany USA i warstwy aplikacji SAP w centralnej części USA. Powodem nie obsługujących takich konfiguracji jest czułość opóźnienia architektury SAP NetWeaver.

Jednak w ciągu ostatniego roku partnerzy centrum danych opracowali współmiejsce w regionach platformy Azure. Te kolokości często znajdują się w pobliżu fizycznych centrów danych platformy Azure w regionie platformy Azure. Krótka odległość i połączenie zasobów w kolokacji za pośrednictwem usługi ExpressRoute do platformy Azure może spowodować opóźnienie, które jest mniejsze niż 2 ms. W takich przypadkach, aby zlokalizować warstwę DBMS (w tym magazynu SAN/NAS) w takiej kolokcji i warstwy aplikacji SAP na platformie Azure jest możliwe. [Hana duże wystąpienia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Tworzenie kopii zapasowych w trybie offline systemów SAP
W zależności od wybranej konfiguracji SAP (2-warstwowej lub 3-warstwowej) może być konieczne utworzenie kopii zapasowej. Zawartość maszyny Wirtualnej, a także mieć kopię zapasową bazy danych. Oczekuje się, że kopie zapasowe związane z usługą DBMS będą wykonywane za pomocą metod bazy danych. Szczegółowy opis różnych baz danych można znaleźć w [przewodniku dbms][dbms-guide]. Z drugiej strony można wyw.

Kopia zapasowa w trybie offline wymagałoby zamknięcia maszyny Wirtualnej za pośrednictwem witryny Azure portal i kopii dysku podstawowej maszyny Wirtualnej oraz wszystkich dołączonych dysków do maszyny Wirtualnej. Pozwoliłoby to zachować obraz punktu w czasie maszyny Wirtualnej i skojarzonego z nią dysku. Zaleca się skopiowanie kopii zapasowych do innego konta usługi Azure Storage. W związku z tym procedura opisana w rozdziale [Kopiowanie dysków między kontami usługi Azure Storage][planning-guide-5.4.2] tego dokumentu będzie miała zastosowanie.
Oprócz zamknięcia przy użyciu witryny Azure portal można również zrobić za pośrednictwem programu Powershell lub interfejsu wiersza polecenia, jak opisano w tym miejscu:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Przywracanie tego stanu polegałoby na usunięciu podstawowej maszyny Wirtualnej, a także oryginalnych dysków podstawowej maszyny Wirtualnej i dysków zainstalowanych, skopiowaniu zapisanych dysków do oryginalnego konta magazynu lub grupy zasobów dla dysków zarządzanych, a następnie ponownemu rozmieszczeniu systemu.
W tym artykule przedstawiono przykład sposobu skryptu tego procesu w programie PowerShell:<http://www.westerndevs.com/azure-snapshots/>

Upewnij się, że zainstalowano nową licencję SAP, ponieważ przywrócenie kopii zapasowej maszyny Wirtualnej, jak opisano powyżej, tworzy nowy klucz sprzętowy.

### <a name="online-backup-of-an-sap-system"></a>Kopia zapasowa systemu SAP online

Kopia zapasowa usługi DBMS jest wykonywana metodami specyficznymi dla DBMS, zgodnie z opisem w [przewodniku dbms][dbms-guide].

Kopie zapasowe innych maszyn wirtualnych w systemie SAP można wykonać przy użyciu funkcji tworzenia kopii zapasowej maszyny wirtualnej platformy Azure. Azure Virtual Machine Backup to standardowa metoda tworzenia kopii zapasowych pełnej maszyny wirtualnej na platformie Azure. Usługa Azure Backup przechowuje kopie zapasowe na platformie Azure i umożliwia ponowne przywracanie maszyny wirtualnej.

> [!NOTE]
> Od grudnia 2015 r. przy użyciu kopii zapasowej maszyny Wirtualnej NIE zachowuje unikatowy identyfikator maszyny Wirtualnej, który jest używany do licencjonowania SAP. Oznacza to, że przywracanie z kopii zapasowej maszyny Wirtualnej wymaga instalacji nowego klucza licencyjnego SAP, ponieważ przywrócona maszyna wirtualna jest uważana za nową maszynę wirtualną, a nie za zastąpienie poprzedniej, która została zapisana.
>
> ![Windows][Logo_Windows] Windows
>
> Teoretycznie maszyny wirtualne, które uruchamiają bazy danych, mogą być archiwizowane w spójny sposób, jak <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>również wtedy, gdy system DBMS obsługuje windows VSS (Volume Shadow Copy Service), jak na przykład SQL Server.
> Należy jednak pamiętać, że na podstawie kopii zapasowych platformy Azure VM przywracanie baz danych w czasie nie są możliwe. W związku z tym zalecenie jest wykonywanie kopii zapasowych baz danych z funkcją DBMS zamiast polegać na kopii zapasowej maszyny wirtualnej platformy Azure.
>
> Aby zapoznać się z tworzeniem <https://docs.microsoft.com/azure/backup/backup-azure-vms>kopii zapasowej maszyny wirtualnej platformy Azure, rozpocznij tutaj: .
>
> Inne możliwości to użycie kombinacji programu Microsoft Data Protection Manager zainstalowanej na maszynie Wirtualnej Platformy Azure i kopii zapasowej platformy Azure w celu tworzenia kopii zapasowych/przywracania baz danych. Więcej informacji można znaleźć <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>tutaj: .  
>
> ![Linux][Logo_Linux] Linux
>
> Nie ma odpowiednika systemu Windows VSS w systemie Linux. W związku z tym tylko kopie zapasowe spójne z plikami są możliwe, ale nie spójne z aplikacjami kopie zapasowe. Kopia zapasowa SAP DBMS powinna być wykonywana przy użyciu funkcji DBMS. System plików, który zawiera dane związane z SAP, można zapisać, na przykład przy użyciu smoły, jak opisano tutaj:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Platforma Azure jako lokacja odzyskiwania po awarii dla środowiska sap produkcji

Od połowy 2014 r. rozszerzenia różnych składników wokół funkcji Hyper-V, Programu System Center i platformy Azure umożliwiają korzystanie z platformy Azure jako lokacji odzyskiwania po awarii dla maszyn wirtualnych działających lokalnie na podstawie funkcji Hyper-V.

Blog szczegółowo jak wdrożyć to rozwiązanie jest <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>udokumentowane tutaj: .

## <a name="summary"></a>Podsumowanie

Kluczowe punkty wysokiej dostępności dla systemów SAP na platformie Azure to:

* W tym momencie pojedynczy punkt awarii sap nie może być zabezpieczony dokładnie tak samo, jak można to zrobić w wdrożeniach lokalnych. Powodem jest to, że klastry dysków udostępnionych nie można jeszcze zbudować na platformie Azure bez użycia oprogramowania innych firm.
* W warstwie DBMS należy użyć funkcji DBMS, która nie opiera się na technologii klastra dysków współużytkowych. Szczegóły są udokumentowane w [przewodniku dbms][dbms-guide].
* Aby zminimalizować wpływ problemów w domenach błędów w infrastrukturze platformy Azure lub konserwacji hosta, należy użyć zestawów dostępności platformy Azure:
  * Zaleca się, aby mieć jeden zestaw dostępności dla warstwy aplikacji SAP.
  * Zaleca się, aby mieć oddzielny zestaw dostępności dla warstwy SAP DBMS.
  * NIE zaleca się stosowania tego samego zestawu dostępności dla maszyn wirtualnych różnych systemów SAP.
  * Zaleca się używanie dysków zarządzanych w wersji Premium.
* Aby uzyskać kopię zapasową warstwy SAP DBMS, sprawdź [przewodnik dbms][dbms-guide].
* Tworzenie kopii zapasowej wystąpień okna dialogowego SAP nie ma większego sensu, ponieważ zwykle jest szybsze ponowne wdrożenie prostych wystąpień okna dialogowego.
* Tworzenie kopii zapasowej maszyny Wirtualnej, która zawiera globalny katalog systemu SAP, a wraz z nim wszystkie profile różnych wystąpień, ma sens i powinny być wykonywane za pomocą kopii zapasowej systemu Windows lub, na przykład, tar na Linuksie. Ponieważ istnieją różnice między systemami Windows Server 2008 (R2) i Windows Server 2012 (R2), które ułatwiają tworzenie kopii zapasowych przy użyciu nowszych wersji systemu Windows Server, zaleca się uruchamianie systemu Windows Server 2012 (R2) jako systemu operacyjnego windows guest.

## <a name="next-steps"></a>Następne kroki
Przeczytaj artykuły:

- [Wdrożenie maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Konfiguracje i operacje infrastruktury SAP HANA na platformie Azure] (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
