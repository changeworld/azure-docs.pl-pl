---
title: Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP NetWeaver | Dokumentacja firmy Microsoft
description: Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7848aa2e620218463bbe3faa325b4589ae6ac3b5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657502"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP NetWeaver
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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
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
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure umożliwia uzyskanie zasobów obliczeniowych i magazynu w minimalny czas bez cykle nabywania długie. Maszyny wirtualne platformy Azure umożliwia firmom wdrażania klasycznego aplikacji, takie jak SAP NetWeaver na podstawie aplikacji na platformie Azure i rozszerzyć ich niezawodności i dostępności bez konieczności dalszego zasoby dostępne lokalnie. Usługi platformy Azure na maszynie wirtualnej obsługuje również łączności między lokalizacjami, które umożliwia przedsiębiorstwom aktywnie zintegrować maszyny wirtualne Azure ich domenami lokalnymi ich chmur prywatnych i ich pozioma systemu SAP.
Ten dokument w tym artykule opisano podstawy Azure maszyny wirtualnej platformy Microsoft i zawiera omówienie zagadnienia dotyczące planowania i wdrażania dla instalacji programu SAP NetWeaver na platformie Azure i jako taki powinny być dokument, aby przeczytać przed rozpoczęciem rzeczywistego wdrożenia SAP NetWeaver na platformie Azure.
Papieru uzupełnia SAP instalacji dokumentacji i uwagi SAP, reprezentujące głównej zasobów dla instalacji i wdrożenia oprogramowania SAP na podany platform.

## <a name="summary"></a>Podsumowanie
Chmura obliczeniowa warunek powszechnie używany jest uzyskanie coraz więcej znaczenie w branży IT z małych firm do dużych i międzynarodowej firmy.

Microsoft Azure to platforma usługi w chmurze firmy Microsoft, która oferuje szeroką gamę nowe możliwości. Klienci są teraz możliwość szybkiego udostępniania i usuwanie aplikacji jako usługa w chmurze, więc nie są one ograniczone do ograniczeń technicznych lub budżetowych. Zamiast inwestowanie czas i pieniądze na infrastrukturę sprzętu, firmy mogą skupić się na aplikacji, procesy biznesowe i korzyści dla klientów i użytkowników.

Wraz z usługami Microsoft Azure Virtual Machines firma Microsoft oferuje kompleksową platformę typu infrastruktura jako usługa (IaaS). Aplikacje oparte na oprogramowaniu SAP NetWeaver są obsługiwane w usłudze Azure Virtual Machines (IaaS). Ten dokument zawiera opis sposobu planowania i implementacji SAP NetWeaver na podstawie aplikacji w systemie Microsoft Azure jako platforma wybór.

Dokument, sam koncentruje się na dwóch aspektach:

* Pierwsza część opisano dwa wzorce wdrożenia obsługiwany dla aplikacji programu SAP NetWeaver oparte na platformie Azure. Omówiono także ogólne Obsługa platformy Azure z wdrożenia SAP pamiętać.
* Drugi szczegóły części wykonania dwóch różnych scenariuszy opisanych w pierwszej części.

Dodatkowe zasoby można znaleźć w rozdziale [zasobów] [ planning-guide-1.2] w tym dokumencie.

### <a name="definitions-upfront"></a>Definicje z wyprzedzeniem
W dokumencie używane następujące pojęcia:

* IaaS: Infrastruktura jako usługa
* PaaS: Platforma jako usługa
* SaaS: Oprogramowanie jako usługa
* ARM: Usługa Azure Resource Manager
* Składnik programu SAP: poszczególnych SAP aplikację taką jak ECC, BW, Menedżer rozwiązania lub EP.  Składniki programu SAP może bazować na tradycyjnych technologii ABAP lub Java lub aplikacji z systemem innym niż NetWeaver na podstawie takich jak obiektów biznesowych.
* Środowisko SAP: jeden lub więcej składników programu SAP logicznie pogrupowane funkcji biznesowych, takich jak projektowanie, QAS, szkolenia, odzyskiwania po awarii lub produkcji.
* Poziomo SAP: Odnosi się do całego zasoby SAP w klienta IT w orientacji poziomej. Poziomo SAP obejmuje wszystkie produkcyjnych i środowiskach nieprodukcyjnych.
* Systemu SAP: Kombinacja systemu DBMS i warstwy aplikacji programu, na przykład programistycznej SAP ERP, programu SAP BW testu systemu SAP CRM produkcji systemu, itp. W przypadku wdrożeń platformy Azure nie jest obsługiwane dzielenia te dwie warstwy między lokalną i platformą Azure. To oznacza, że systemu SAP jest wdrożona lokalnie lub jest wdrożony na platformie Azure. Jednak można wdrożyć różnych systemów pozioma SAP do platformy Azure lub lokalnie. Na przykład możesz wdrożyć programowanie SAP CRM i systemy testowe w Azure, ale SAP CRM produkcji systemu lokalnego.
* Wdrożenie tylko w chmurze: wdrożenia, w którym subskrypcji platformy Azure nie jest połączony za pośrednictwem lokacja lokacja lub połączenia ExpressRoute do infrastruktury sieci lokalnej. Wspólne dokumentacji platformy Azure rodzaju wdrożenia są także opisane jako wdrożenia "Tylko w chmurze". Maszyny wirtualne wdrażane z tej metody są dostępne za pośrednictwem Internetu oraz publicznego adresu IP i/lub publicznej nazwy DNS przypisane do maszyn wirtualnych na platformie Azure. Systemu Microsoft Windows w lokalnej usłudze Active Directory (AD) i DNS nie zostanie rozszerzony na platformie Azure w tych typach wdrożeń. Dlatego maszyn wirtualnych nie są częścią lokalnej usługi Active Directory. Dotyczy to także implementacji systemu Linux przy użyciu, na przykład OpenLDAP + protokołu Kerberos.

> [!NOTE]
> Tylko w chmurze wdrożenia w tym dokumencie jest zdefiniowany jako pełną krajobrazów SAP działają wyłącznie na platformie Azure, bez rozszerzenia usługi Active Directory / OpenLDAP lub rozpoznawania nazw z lokalnymi do chmury publicznej. Konfiguracje tylko w chmurze nie są obsługiwane dla systemów produkcyjnych SAP lub konfiguracje, których SAP STMS lub innymi zasobami lokalnymi trzeba używać między systemami SAP hostowane na platformie Azure i zasobów znajdującej się na lokalnym.
>
>

* Między lokalizacjami: Opisano scenariusz wdrożonym maszyn wirtualnych z subskrypcją platformy Azure, lokacja lokacja, obejmujący wiele lokacji lub połączenia ExpressRoute między datacenter(s) lokalną i platformą Azure. Dokumentacja wspólnych Azure rodzaju wdrożenia również są opisane jako scenariusze między lokalizacjami. Przyczyna połączenia jest rozszerzenie domenami lokalnymi, Active Directory/OpenLDAP lokalnymi i DNS lokalnej na platformie Azure. Pozioma lokalnej jest rozszerzony do platformy Azure zasobów subskrypcji. Występuje to rozszerzenie, maszyn wirtualnych może być częścią domeny lokalnej. Użytkownicy domeny w domenie lokalnej mogą uzyskiwać dostęp do serwerów i usługi można uruchamiać na tych maszynach wirtualnych (takie jak usługi systemu DBMS). Możliwe jest komunikacji i rozpoznawania nazw między maszyn wirtualnych wdrożonych lokalnie i wdrożone maszyny wirtualne platformy Azure. Jest to scenariusz oczekujemy zasobów SAP do wdrożenia w. Aby uzyskać więcej informacji, zobacz [to] [ vpn-gateway-cross-premises-options] artykułu i [to][vpn-gateway-site-to-site-create].

> [!NOTE]
> Między różnymi lokalizacjami wdrożeń systemów SAP, gdzie systemami SAP maszynach wirtualnych platformy Azure są członkami domeny lokalnej są obsługiwane dla systemów produkcyjnych SAP. Konfiguracje między lokalizacjami są obsługiwane w przypadku wdrażania części lub zakończyć krajobrazów SAP do platformy Azure. Nawet działające na platformie Azure pełną pozioma SAP wymaga o te maszyny wirtualne są częścią domeny lokalnej i REKLAM/OpenLDAP. W poprzednich wersjach dokumentacji zajmowaliśmy scenariuszy hybrydowych IT, których termin *hybrydowego* jest ścieżką do katalogu głównego z faktu, że istnieje łączność między lokalizacjami, między lokalną i platformą Azure. Ponadto fakt, że maszyny wirtualne na platformie Azure są częścią lokalnej usługi Active Directory / OpenLDAP.
>
>

Niektóre dokumentacji firmy Microsoft opisano scenariusze między lokalizacjami nieco inaczej, szczególnie w przypadku konfiguracji HA systemu DBMS. W przypadku dokumentów związanych z SAP w scenariuszu obejmującym różne pomieszczenia rozpoczęcia wrzenia w dół do lokacji do lokacji lub łączności prywatne (ExpressRoute) i fakt, że pozioma SAP jest rozdzielona między lokalną i platformą Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Zasoby
Temat wdrożenia SAP na platformie Azure dostępne są następujące przewodniki dodatkowe:

* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP NetWeaver (w tym dokumencie)][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP NetWeaver][deployment-guide]
* [Azure wdrożenia SAP NetWeaver DBMS maszyny wirtualne][dbms-guide]

> [!IMPORTANT]
> Wszędzie tam, gdzie jest używane możliwe łącze do odwołującego się Przewodnik instalacji SAP (odwołanie InstGuide-01, zobacz <http://service.sap.com/instguides>). Po przejściu do wymagań wstępnych i procesu instalacji, przewodniki instalacji programu SAP NetWeaver zawsze są odczytywane ostrożnie, ponieważ w tym dokumencie opisano tylko określonych zadań dla systemów SAP NetWeaver w maszynie wirtualnej Microsoft Azure.
>
>

Poniższe uwagi SAP są związane z tym tematem SAP na platformie Azure:

| Numer | Stanowisko |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produktów i zmiana rozmiaru |
| [2015553] |SAP na platformie Microsoft Azure: obsługuje wymagania wstępne |
| [1999351] |Rozwiązywanie problemów z rozszerzonego monitorowania Azure dla programu SAP |
| [2178632] |Klucz monitorowania metryki dla SAP na platformie Microsoft Azure |
| [1409604] |Wirtualizacji w systemie Windows: rozszerzonego monitorowania |
| [2191498] |SAP w systemie Linux przy użyciu platformy Azure: rozszerzonego monitorowania |
| [2243692] |Linux w systemie Microsoft Azure (IaaS) maszyny Wirtualnej: problemów licencji SAP |
| [1984787] |Systemu SUSE LINUX Enterprise Server 12: Informacje o instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: instalacji i uaktualniania |
| [2069760] |Oracle Linux 7.x SAP instalacji i uaktualniania |
| [1597355] |Zalecenie obszaru wymiany w systemie Linux |

Również przeczytanie [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zawierający wszystkie notatki SAP dla systemu Linux.

Ograniczenia ogólne domyślne i maksymalnego ograniczenia subskrypcji platformy Azure można znaleźć w [w tym artykule][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Możliwe scenariusze
SAP jest często postrzegane jako jedną z kluczowych aplikacji w obrębie przedsiębiorstwa. Architektura i operacje te aplikacje przede wszystkim jest bardzo skomplikowane i ważne jest zapewnienie, że spełniają wymagania dotyczące wydajności i dostępności.

W związku z tym firmom trzeba myśleć dokładnie o tym, które aplikacje mogą być uruchamiane w środowisku chmury publicznej, niezależnie od dostawcy w wybranej chmurze.

Typy systemu możliwych do wdrożenia SAP NetWeaver na podstawie aplikacji w obrębie chmur publicznych, środowisk są wymienione poniżej:

1. Systemy średniej wielkości produkcji
2. Programowanie systemów
3. Testowanie systemów
4. Systemy prototypu
5. Learning / pokaz systemów

W celu pomyślnego ogólnie wdrożenia systemów SAP do platformy Azure IaaS lub IaaS, ważne jest zrozumienie istotnych różnic między ofert tradycyjnych uzyskanie programu lub dostawcy usług hostingowych i oferty IaaS. Natomiast tradycyjnego dostawcy usług hostingowych lub dostawców dostosowuje infrastruktury (typ sieci, magazynu i server), obciążenia, które odbiorca chce hosta, odpowiada zamiast tego klienta wybierz obciążenie prawo wdrożeń IaaS.

Pierwszym krokiem klienci muszą sprawdź następujące elementy:

* SAP obsługiwane typy maszyny Wirtualnej platformy Azure
* SAP obsługiwane produkty/wersjach na platformie Azure
* Obsługiwany system operacyjny i DBMS zwalnia w określonych wersjach SAP na platformie Azure
* Protokoły SAP przepływności udostępniane przez różne jednostki magazynowe Azure

Odpowiedzi na te pytania mogą być odczytywane w Uwaga SAP [1928533].

Jako drugi etap Azure ograniczenia zasobów i przepustowości należy porównać do zużycia zasobów rzeczywistych systemów lokalnych. W związku z tym klienci muszą znać różne możliwości typów Azure obsługiwane z programu SAP w zakresie:

* Zasoby Procesora i pamięci o różnych typach maszyn wirtualnych i
* Przepustowość IOPS o różnych typach maszyn wirtualnych i
* Możliwości różnych typach maszyn wirtualnych w sieci.

Większość tych danych można znaleźć [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (system Windows)][virtual-machines-sizes-windows].

Należy pamiętać, że limity na liście powyżej łącze są górne limity. Nie oznacza to, że limity dla zasobów, na przykład IOPS można podać we wszystkich okolicznościach. Wyjątki są jednak zasobów Procesora i pamięci wybranego typu maszyny Wirtualnej. Typy maszyn wirtualnych obsługiwanych przez program SAP zasobów Procesora i pamięci są zarezerwowane i jako taki dostępna w dowolnym momencie w czasie korzystania z poziomu maszyny Wirtualnej.

Platforma Microsoft Azure, takich jak innych platform IaaS to platforma wielodostępnej. Oznacza to, że magazynu, sieci i inne zasoby są współużytkowane przez dzierżawców. Inteligentnego logiki ograniczania przepustowości i przydziału służy do uniemożliwić wpływające na wydajność innego dzierżawcę (zakłócenia sąsiada) w sposób radykalne jednego dzierżawcy. Chociaż logiki w usłudze Azure próbuje zachować odchylenia przepustowości wystąpił mały, wysokiej udostępnionego platformy często wprowadzenie wariancje większą dostępność zasobów/przepustowość niż w przypadku ich wdrożeń lokalnych służą do wielu klientów. W związku z tym mogą wystąpić różne poziomy przepustowości dotyczących sieci lub magazynu we/wy (woluminu także opóźnienia) minutę minutę. Prawdopodobieństwo systemu SAP na platformie Azure mogą wystąpić różnice większych niż w systemie lokalnym musi być brane pod uwagę.

Ostatnim krokiem jest do oceny wymagań dotyczących dostępności. Może się zdarzyć, że podstawowej infrastruktury platformy Azure wymaga aktualizowany oraz hostów działających maszyn wirtualnych, należy ponownie uruchomić. W takich przypadkach maszyny wirtualne na tych hostach może być zamknięte i ponownie uruchomione również. Czas takiej obsługi odbywa się w dodatkowych godzinach pracy dla danego regionu, ale okno potencjalnych kilka godzin, podczas którego nastąpi ponowne uruchomienie jest stosunkowo szerokości. Istnieją różne technologie w ramach platformy Azure, które można skonfigurować w celu złagodzenia niektórych lub wszystkich wpływu tych aktualizacji. Przyszłe ulepszenia platformy Azure, system DBMS i SAP mają zminimalizować wpływ takich ponownego uruchomienia aplikacji.

W celu pomyślnego wdrożenia systemu SAP do platformy Azure, lokalną SAP systemy System operacyjny, baza danych, a aplikacje SAP muszą znajdować się na macierz obsługi SAP Azure, mieści się w obrębie zasobów platformy Azure zapewniają infrastrukturę i który może współpracować z ofertami dostępności SLA Microsoft Azure. Określone tych systemów należy zdecydować się na jednym z następujących scenariuszy wdrażania dwa.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Tylko w chmurze — wdrożenia maszyny wirtualnej na platformie Azure bez zależności w sieci lokalnej klienta
![Jednej maszyny Wirtualnej z pokaz SAP lub scenariusza szkolenia wdrożona na platformie Azure][planning-guide-figure-100]

Ten scenariusz jest typowa dla szkolenia lub systemów demonstracyjnej, którym są zainstalowane wszystkie składniki programu SAP i oprogramowania z systemem innym niż SAP w jednej maszynie Wirtualnej. Systemów produkcyjnych SAP nie są obsługiwane w tym scenariuszu wdrażania. Ogólnie rzecz biorąc w tym scenariuszu spełnia następujące wymagania:

* Maszyny wirtualne, same są dostępne w sieci publicznej. Łączność z siecią bezpośrednie dla aplikacji działających w ramach maszyn wirtualnych do sieci lokalnej firmy będącej właścicielem zawartości pokazy lub szkolenia lub klienta nie jest konieczne.
* W przypadku reprezentujący szkolenia lub scenariusza pokaz wielu maszyn wirtualnych sieci łączności i rozpoznawania nazw musi współpracować między maszynami wirtualnymi. Ale komunikacji między zestaw maszyn wirtualnych muszą być izolowane, tak aby kilka zestawów maszyn wirtualnych można wdrożyć obok siebie bez zakłóceń.  
* Połączenie z Internetem jest wymagany dla użytkownika końcowego do logowania zdalnego do maszyn wirtualnych hostowanych na platformie Azure. W zależności od gościa systemu operacyjnego, usług terminalowych usług pulpitu zdalnego lub VNC/ssh umożliwia dostęp do maszyny Wirtualnej do wykonania zadania szkolenia lub w trakcie wykonywania. Jeśli SAP porty takich jak 3200, 3300 & 3600 może również być uwidaczniane SAP wystąpienia aplikacji można uzyskać z dowolnego pulpitu połączonych Internet.
* Systemy SAP (i reprezentują VM(s)) scenariusza autonomiczny na platformie Azure, który tylko wymaga łączności z Internetem publicznego dostępu użytkownika końcowego i nie wymagają połączenia z innych maszyn wirtualnych na platformie Azure.
* SAPGUI i przeglądarką są zainstalowane i uruchomić bezpośrednio na Maszynie wirtualnej.
* Wymagana jest szybkie zresetowanie maszyny Wirtualnej do pierwotnego stanu i ponownie nowego wdrożenia tego oryginalnego stanu.
* W przypadku demo i scenariusze szkoleniowe, które są realizowane w wielu maszyn wirtualnych, usługi Active Directory / OpenLDAP i/lub DNS usługa jest wymagana dla każdego zestawu maszyn wirtualnych.

![Grupa maszyny Wirtualnej reprezentuje jeden pokaz lub szkolenia scenariusz usługi w chmurze platformy Azure][planning-guide-figure-200]

Należy pamiętać, że wirtualne w poszczególnych zestawów muszą zostać wdrożone równolegle, w którym nazw maszyn wirtualnych w każdym zestawie są takie same.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Między różnymi lokalizacjami - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure z wymaganiami jest w pełni zintegrowany sieci lokalnej
![Sieć VPN z połączeniem lokacja-lokacja (między lokalizacjami)][planning-guide-figure-300]

Ten scenariusz jest scenariusza między lokalizacjami z wielu wzorców możliwe wdrożenie rozwiązania. Można również opisać tylko uruchamianie niektórych części SAP poziomą lokalnego i innymi częściami SAP poziomą na platformie Azure. Wszystkie aspekty fakt, że część SAP składniki są uruchomione na platformie Azure powinna być niewidoczny dla użytkowników końcowych. Dlatego SAP transportu korekty systemu (STMS), RFC komunikacji, drukowania, zabezpieczeń (na przykład SSO), itp. współdziała bezproblemowo dla systemów SAP działających na platformie Azure. Jednak w scenariuszu obejmującym różne pomieszczenia również opisano scenariusz, w którym pełną pozioma SAP działa na platformie Azure z domeną klienta i rozszerzone DNS na platformie Azure.

> [!NOTE]
> Jest to scenariusz wdrażania, która jest obsługiwana w przypadku produktywności systemami SAP.
>
>

Odczyt [w tym artykule] [ vpn-gateway-create-site-to-site-rm-powershell] Aby uzyskać więcej informacji na temat połączyć sieć lokalną do systemu Microsoft Azure

> [!IMPORTANT]
> Gdy firma Microsoft mówimy więc o scenariuszach między lokalizacjami między wdrożeniami klienta usługi Azure i lokalnymi, czekamy na poziom szczegółowości całego systemu SAP. Scenariusze, które są *nieobsługiwane* między lokalizacjami scenariusze są:
>
> * Uruchamianie różnych warstw aplikacje SAP w różne metody wdrażania. Na przykład uruchomiony system DBMS warstwy lokalną, ale warstwy aplikacji SAP w maszyn wirtualnych wdrożonych jako maszynach wirtualnych platformy Azure lub na odwrót.
> * Niektóre składniki warstwy SAP w Azure i niektórych lokalnych. Na przykład podzielić wystąpienia warstwy aplikacji SAP między lokalnymi i maszyn wirtualnych platformy Azure.
> * Rozkład maszyn wirtualnych uruchomionych wystąpień SAP jednego systemu w wielu regionach platformy Azure nie jest obsługiwane.
>
> Przyczyna te ograniczenia jest wymagane dla bardzo małych opóźnień sieci wysokiej wydajności w ramach jednego systemu SAP, szczególnie między wystąpieniami aplikacji a warstwy DBMS systemu SAP.
>
>

### <a name="supported-os-and-database-releases"></a>Obsługiwane systemu operacyjnego i wersji bazy danych
* Obsługiwane w przypadku usług maszyny wirtualnej platformy Azure to wymienione w tym artykule oprogramowanie serwera firmy Microsoft: <http://support.microsoft.com/kb/2721672>.
* Obsługiwane wersje systemu operacyjnego, obsługiwany na usługi na maszynie wirtualnej platformy Azure w połączeniu z oprogramowania SAP wydaniach systemu bazy danych są udokumentowane w artykule Uwaga SAP [1928533].
* Aplikacje SAP i wersje obsługiwane na usługi na maszynie wirtualnej Azure opisano w Uwaga SAP [1928533].
* Do uruchamiania jako maszyny wirtualne gości na platformie Azure dla programu SAP scenariusze obsługiwane są tylko obrazy 64-bitowym. Oznacza to również, że obsługiwane są tylko aplikacje SAP 64-bitowe i baz danych.

## <a name="microsoft-azure-virtual-machine-services"></a>Usługi Microsoft Azure na maszynie wirtualnej
Platforma Microsoft Azure jest internet skali usługi platformy w chmurze hostowanej i zarządzane w centrach danych firmy Microsoft. Ta platforma obejmuje usługi Microsoft Azure maszyny wirtualnej (infrastruktura jako usługa lub IaaS) oraz zestaw zaawansowanych platforma jako możliwości usługa (PaaS).

Platforma Azure ogranicza potrzebę początkowych technologii i zakupi infrastruktury. Takie rozwiązanie upraszcza, obsługi i działania aplikacji przez podanie obliczeń na żądanie i magazynu do hosta, skalowania i zarządzania nimi połączonych aplikacji i aplikacji sieci web. Zarządzanie infrastrukturą jest zautomatyzowane przy użyciu platformy, które zaprojektowano pod kątem wysokiej dostępności i dynamiczne skalowanie do dopasowania na potrzeby użycia z opcją z modelu cenowego.

![Pozycjonowanie usługi na maszynie wirtualnej platformy Microsoft Azure][planning-guide-figure-400]

Za pomocą usług Azure maszyny wirtualnej firmy Microsoft jest umożliwiając wdrażanie obrazów niestandardowego serwera na platformie Azure jako wystąpień IaaS (patrz rysunek 4). Maszyny wirtualne na platformie Azure są oparte na funkcji Hyper-V wirtualnych dysków twardych (VHD) i mogą działać z różnymi systemami operacyjnymi jako systemu operacyjnego gościa.

Z perspektywy operacyjnej usługa maszynami wirtualnymi Azure oferuje podobne funkcje jako maszyn wirtualnych wdrożonych lokalnie. Ma jednak znaczących korzyści, który nie jest konieczne nabywania, Administruj i Zarządzaj infrastruktury. Deweloperzy i Administratorzy mają pełną kontrolę nad obrazu systemu operacyjnego w ramach tych maszyn wirtualnych. Administratorzy mogą zalogować się zdalnie w do tych maszyn wirtualnych do wykonywania konserwacji i rozwiązywanie problemów z zadaniami, jak również zadania wdrażania oprogramowania. W odniesieniu do wdrożenia tylko ograniczenia są rozmiary i możliwości maszyn wirtualnych platformy Azure. To może nie być poprawnie jako szczegółowe w konfiguracji, można to zrobić lokalnie. Brak wyboru stanową kombinację typów maszyny Wirtualnej:

* Liczba Vcpu,
* Pamięci,
* Liczba wirtualnych dysków twardych, które można dołączyć,
* Przepustowości sieci i magazynu.

Rozmiar i ograniczenia o różnych rozmiarach różnych maszyn wirtualnych oferowanych są widoczne w tabeli w [w tym artykule (Linux)] [ virtual-machines-sizes-linux] i [w tym artykule (system Windows)][virtual-machines-sizes-windows].

Jak można zrealizować, istnieją różnych rodzin lub serii maszyn wirtualnych. Można rozróżnić następujących rodzin maszyn wirtualnych:

* Maszyna wirtualna a0 A7 typów: nie wszystkie te są certyfikowane dla SAP. Pierwszy serii maszyny Wirtualnej, która Azure IaaS otrzymano wprowadzone w systemie.
* Maszyna wirtualna a8 A11 typy: Wysoka wydajność przetwarzania danych wystąpienia. Uruchomione na różnych lepsze wykonywanie obliczeń hostów od innych maszyn wirtualnych, A-series.
* Typy Dv2-D-Series maszyny Wirtualnej: wykonywanie lepiej niż A0 A7. Nie wszystkie typy wirtualna certyfikowanych SAP.
* Typy serii DS/DSv2 maszyny Wirtualnej: podobne do Dv2-D-series, ale są można nawiązać połączenia z usługi Azure Premium Storage (zobacz rozdział [Azure Premium Storage] [ planning-guide-3.3.2] tego dokumentu). Ponownie certyfikowanych nie wszystkie typy maszyny Wirtualnej z programu SAP.
* Typy wirtualna serii G: typach maszyn wirtualnych o wysokiej pamięci.
* Typy serii GS maszyny Wirtualnej: jak serii G, ale w tym możliwość użycia usługi Azure Premium Storage (zobacz rozdział [Azure Premium Storage] [ planning-guide-3.3.2] tego dokumentu). Korzystając z maszyn wirtualnych GS-Series jako serwery baz danych, jest to konieczne do użycia magazyn w warstwie Premium dla pliki dziennika bazy danych i transakcji

Możesz znaleźć tego samego procesora CPU i pamięci konfiguracji w innej serii maszyn wirtualnych. Niemniej jednak gdy wyszukiwanie przepustowość tych maszyn wirtualnych z innej serii może różnią się one znacznie. Pomimo mających taką samą konfigurację Procesora i pamięci. Przyczyną jest to, czy używany sprzęt serwera hosta na wprowadzenie różnych typach maszyn wirtualnych ma parametry różnych przepływności.  Zazwyczaj różnica pokazano przepustowość również znajduje odzwierciedlenie w cenie różnych maszyn wirtualnych.

Nie wszystkie innej serii maszyn wirtualnych może być oferowana w poszczególnych regionach platformy Azure (dla następnego rozdziału Zobacz regiony platformy Azure). Wziąć pod uwagę, że nie wszystkie maszyny wirtualne lub serii maszyn wirtualnych są certyfikowane dla programu SAP.

> [!IMPORTANT]
> Do korzystania z programu SAP NetWeaver na podstawie aplikacji, tylko podzbiór typach maszyn wirtualnych i konfiguracji na liście Uwaga SAP [1928533] są obsługiwane.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiony platformy Azure
Microsoft umożliwia wdrażanie maszyn wirtualnych do tak zwane *regiony platformy Azure*. Region platformy Azure może być jeden lub wiele centrów danych, które znajdują się w pobliżu. W większości regionów geograficznymi na świecie Microsoft ma co najmniej dwóch regionach platformy Azure. Na przykład w Europie istnieje Region platformy Azure z *Europa Północna, Europa* i jeden z *Europa Zachodnia*. Tych dwóch regionach platformy Azure w obrębie regionu geograficznymi są oddzielone odległość tyle istotne, aby awarii naturalnych i technicznych nie wpływają na obu regionów platformy Azure, w tym samym regionie geograficznymi. Ponieważ Microsoft stopniowo buduje się nowych regionów platformy Azure w różnych regionach geograficznymi globalnie, liczba tych regionów stopniowo rośnie i począwszy od grudnia 2015 maksymalną liczbę 20 regiony platformy Azure z regionami dodatkowe ogłoszenia już. Klientów można wdrożyć systemy SAP do tych regionów, w tym dwóch regionach platformy Azure w Chinach. Dla bieżącego aktualne informacje na temat regiony platformy Azure, zobacz tę witrynę sieci Web: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Pojęcie maszyny wirtualnej platformy Microsoft Azure
Microsoft Azure oferuje infrastrukturę jako rozwiązanie usługi (IaaS) na host maszyny wirtualnej z podobne funkcje jako rozwiązania do wirtualizacji lokalnymi. Jest możliwość tworzenia maszyn wirtualnych w portalu Azure, programu PowerShell lub interfejsu wiersza polecenia, które oferują także wdrażania i możliwości zarządzania.

Usługa Azure Resource Manager pozwala inicjować obsługę aplikacji za pomocą deklaratywnych szablonów. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Wielokrotnie wdrażania aplikacji podczas każdego etapu cyklu życia aplikacji przy użyciu tego samego szablonu.

Więcej informacji na temat przy użyciu szablonów usługi Resource Manager można znaleźć tutaj:

* [Wdrażania i zarządzania maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Inna funkcja interesujące jest możliwość tworzenia obrazów z maszyn wirtualnych, dzięki czemu można przygotować niektórych repozytoriów, z których będą mogli szybko wdrożyć wystąpień maszyn wirtualnych, które spełniają wymagania.

Więcej informacji na temat tworzenia obrazów z maszyn wirtualnych można znaleźć w [w tym artykule (Linux)] [ virtual-machines-linux-capture-image-resource-manager] i [w tym artykule (system Windows)][virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domen błędów
Domen błędów reprezentują fizyczną jednostkę awarii, bardzo zbliżona do infrastruktury fizycznej zawarte w centrach danych i gdy bloku fizycznego lub stojak jest uznawana za domeny błędów, nie istnieje żadne bezpośrednie mapowanie jeden do jednego między nimi.

Podczas wdrażania wielu maszyn wirtualnych w ramach jednego systemu SAP w usług Microsoft Azure maszyny wirtualnej, może mieć wpływ z kontrolerem sieci szkieletowej Azure wdrażania aplikacji w różnych domen błędów, w tym samym spełniając wymagania umowy SLA platformy Azure firmy Microsoft. Rozkład domen błędów przez jednostkę skalowania Azure (kolekcja setki węzłów obliczeniowych lub węzłów magazynu i sieci) lub przydziału maszyn wirtualnych do określonej domeny błędów jest jednak coś, w którym nie ma bezpośredniej kontroli. Aby skierować kontrolera sieci szkieletowej Azure wdrażanie zestaw maszyn wirtualnych za pośrednictwem różnych domenach awarii, należy przypisać Azure zestawu dostępności z maszynami wirtualnymi w czasie wdrażania. Aby uzyskać więcej informacji dotyczących zestawami dostępności Azure, zobacz rozdział [zestawami dostępności Azure] [ planning-guide-3.2.3] w tym dokumencie.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domen uaktualnienia
Domen uaktualnienia reprezentują jednostki logicznej, która pomoc, aby określić sposób aktualizowania maszyny Wirtualnej w ramach systemu SAP, składający się z wystąpień SAP w wielu maszyn wirtualnych. W przypadku uaktualnienia programu Microsoft Azure przechodzi przez proces aktualizowania te domeny uaktualnienia pojedynczo. Dzięki rozproszeniu maszyn wirtualnych w czasie wdrażania w różnych domenach uaktualnienia, można chronić systemu SAP częściowo z potencjalny Przestój. Aby wymusić Azure, aby wdrożyć maszyny wirtualne systemu SAP zlokalizowane w różnych domenach uaktualnienia, musisz ustawić określony atrybut w czasie wdrażania każdej maszyny wirtualnej. Podobnie jak domen błędów, jednostki skalowania usługi Azure jest podzielony na wiele domen uaktualnienia. Aby skierować kontrolera sieci szkieletowej Azure wdrażanie zestaw maszyn wirtualnych za pośrednictwem różnych domen uaktualnienia, należy przypisać Azure zestawu dostępności z maszynami wirtualnymi w czasie wdrażania. Aby uzyskać więcej informacji dotyczących zestawami dostępności Azure, zobacz rozdział [zestawami dostępności Azure] [ planning-guide-3.2.3] poniżej.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Zestawy dostępności Azure
Maszyny wirtualne platformy Azure w ramach jednego zestawu dostępności Azure są dystrybuowane przez kontroler sieci szkieletowej Azure za pośrednictwem różnych usterek i domen uaktualnienia. Dystrybucji za pośrednictwem różnych awarii i uaktualniania domeny ma na celu zapobieganie wszystkich maszyn wirtualnych systemu SAP zamykany w przypadku infrastruktury obsługi lub awaria w jednej domenie błędów. Domyślnie maszyny wirtualne nie są częścią zestawu dostępności. Udział maszyny wirtualnej w zestawie dostępności jest zdefiniowany w czasie wdrażania lub nowszego przez zmianę konfiguracji i ponownego wdrażania maszyny wirtualnej.

Aby zrozumieć pojęcie zestawami dostępności Azure i w przypadku zestawów dostępności odnoszą się do awarii i uaktualniania domen, przeczytaj [w tym artykule][virtual-machines-manage-availability]

Aby zdefiniować Zobacz zbiór dostępności dla ARM przy użyciu szablonu json [specyfikacji interfejsu api rest](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) i wyszukaj "availability".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Pamięć: Usługi Microsoft Azure Storage i dysków z danymi
Maszyny wirtualne Microsoft Azure korzystać z różnymi typami magazynów. Podczas implementowania SAP na usługi na maszynie wirtualnej Azure, jest ważne poznać różnice między tymi dwoma rodzajami głównego magazynu:

* Inne niż stałe i nietrwałe magazynu.
* Magazynu trwałego.

Trwałe magazyn jest podłączony bezpośrednio do maszyn wirtualnych i znajduje się w węzłach obliczeniowych, lokalne wystąpienie magazynu tymczasowego. Rozmiar zależy od rozmiaru maszyny wirtualnej po uruchomieniu wdrożenia. Ten typ magazynu jest nietrwała i w związku z tym dysk został zainicjowany po ponownym uruchomieniu wystąpienia maszyny wirtualnej. Zazwyczaj plik stronicowania systemu operacyjnego znajduje się na tym dysku tymczasowym.

- - -
> ![Windows][Logo_Windows] Windows
>
> Na maszynach wirtualnych z systemem Windows jako dysku D:\ w wdrożonej maszyny Wirtualnej został zainstalowany dysk tymczasowego.
>
> ![Linux][Logo_Linux] Linux
>
> Na maszynach wirtualnych systemu Linux jest zainstalowany jako /mnt/resource lub katalogu/mnt. Dowiedz się więcej tutaj:
>
> * [Jak można dołączyć dysku danych do maszyny wirtualnej systemu Linux][virtual-machines-linux-how-to-attach-disk]
> * <https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux#temporary-disk>
>
>

- - -
Rzeczywiste dysk jest volatile, ponieważ jest ona pobieranie przechowywana na serwerze hosta. Jeśli przenieść maszynę Wirtualną w przypadku ponownego wdrażania (na przykład z powodu konserwacji na hoście lub zamknięcia i ponownego uruchomienia) zawartości dysku zostaną utracone. W związku z tym nie jest opcją do przechowywania wszystkich ważnych danych na tym dysku. Typ nośnika użytego dla tego typu magazynu różni się od innej serii maszyn wirtualnych z bardzo różnych charakterystyki, których począwszy od czerwca 2015 wygląda jak:

* A5 A7: Bardzo ograniczony wydajności. Nie jest zalecane dla wszystkich elementów poza pliku stronicowania
* A8 A11: Charakterystyki wydajności bardzo dobre z niektórych IOPS dziesięć tysięcy i > 1GB/s przepustowości.
* D-Series: Charakterystyki wydajności bardzo dobre z niektórych, a następnie tysięcy IOPS i > 1GB/s przepustowości.
* Seria DS: Charakterystyki wydajności bardzo dobre z niektórych IOPS dziesięć tysięcy i > 1GB/s przepustowości.
* Seria G: Charakterystyki wydajności bardzo dobre z niektórych IOPS dziesięć tysięcy i > 1GB/s przepustowości.
* GS-Series: Charakterystyki wydajności bardzo dobre z niektórych IOPS dziesięć tysięcy i > 1GB/s przepustowości.

Instrukcje powyżej jest stosowane do typów maszyny Wirtualnej, które są certyfikowane z SAP. Seria maszyn wirtualnych z doskonałym IOPS i przepływności kwalifikować się do korzystanie przez niektóre funkcje systemu DBMS. Aby uzyskać więcej informacji, zobacz [DBMS Deployment Guide][dbms-guide].

Magazyn Microsoft Azure udostępnia utrwalonego magazynu i typowych poziomu ochrony i nadmiarowość widoczne w magazynie SAN. Dyski oparte na usłudze Azure Storage to wirtualny dysk twardy (VHD) znajduje się w usług magazynu Azure. Lokalny dysk systemu operacyjnego (Windows C:\, Linux/dev/sda1) są przechowywane w magazynie Azure i dodatkowych woluminów/dysków zainstalowanych na maszynie wirtualnej pobrać przechowywane, zbyt.

Istnieje możliwość Przekaż istniejącego dysku VHD z lokalnymi lub Utwórz pustych od w obrębie platformy Azure i dołączyć je na wdrożonych maszyn wirtualnych.

Po utworzeniu lub przekazywanie wirtualnego dysku twardego do usługi Azure Storage, jest możliwe, aby zainstalować i dołącz je na istniejącej maszyny wirtualnej i skopiować istniejącego dysku VHD (odinstalowane).

W tych wirtualne dyski twarde są trwałe, danych i zmiany w tych są bezpieczne podczas ponownego uruchamiania i ponowne utworzenie wystąpienia maszyny wirtualnej. Nawet, jeśli wystąpienie zostanie usunięty, te wirtualne dyski twarde bezpieczeństwo i może zostać wdrożone lub w przypadku dysków z systemem innym niż systemu operacyjnego może być instalowany na innych maszynach wirtualnych.

W ramach sieci usługi Azure Storage, można skonfigurować nadmiarowość różnych poziomów:

* Minimalny poziom, który można wybrać *lokalnego nadmiarowość*, który jest odpowiednikiem trzy repliki danych w tym samym centrum danych z regionu platformy Azure (zobacz rozdział [regiony platformy Azure] [ planning-guide-3.1]).
* Magazyn geograficznie nadmiarowy strefy, który rozprzestrzenia się trzy obrazy za pośrednictwem różnych danych koncentruje się w tym samym regionie Azure.
* Domyślny poziom nadmiarowości jest nadmiarowości geograficznej, które asynchronicznie replikuje zawartość do innego trzy obrazy danych w innym regionie Azure, który jest obsługiwany w tym samym regionie geograficznymi.

Zobacz też tabeli u góry tego artykułu dotyczącej nadmiarowość różne opcje: <https://azure.microsoft.com/pricing/details/storage/>

Więcej informacji na temat usługi Azure Storage można znaleźć tutaj:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Azure Standard storage był typu miejsca do magazynowania IaaS platformy Azure został zwolniony. Znaleziono IOPS limitami na jednym dysku. Opóźnienie wystąpił nie był w tej samej klasie jak zwykle wdrażane systemu SAP wysokiej jakości urządzeniach SAN/NAS obsługiwanego lokalnie. Niemniej jednak usługi Azure Standard Storage potwierdza, że wystarczające do kilkuset wiele systemów SAP, w tym samym czasie wdrożona na platformie Azure.

Dyski, które są przechowywane na standardowe konta magazynu Azure są naliczane na podstawie na rzeczywiste dane przechowywane, wielkość transakcji magazynowych, transfer danych wychodzących i nadmiarowość opcja wybrana. Można tworzyć wiele dysków na maksymalną 1TB, rozmiar, ale tak długo, jak te pozostać pusta nie bez dodatkowych opłat. Jeśli następnie uzupełnij jeden wirtualny dysk twardy z 100GB, naliczane są opłaty do przechowywania 100GB, a nie nominalny, które utworzono z dysku VHD.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Magazyn w warstwie Premium systemu Azure
W kwietnia 2015 r. Firma Microsoft wprowadziła Azure Premium Storage. Magazyn w warstwie Premium został wprowadzony w celu zapewnienie:

* Lepsze opóźnienia we/wy.
* Większa przepustowość.
* Mniejsza zmienność, opóźnienia we/wy.

W tym celu wprowadzono wiele zmian których są dwa najważniejszych:

* Użycie dysków SSD w węzłów magazynu Azure
* Nowy odczytu pamięci podręcznej, która nie jest obsługiwana przez lokalny dysk SSD węzła obliczeń platformy Azure

W przeciwnym Standard storage, którym nie został zmieniony możliwości zależy od rozmiaru dysku (lub dysku VHD), Magazyn w warstwie Premium obecnie ma trzy kategorie inny dysk, które są wyświetlane w tym artykule: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Zobacz, czy IOPS/dysku i przepływności/dysku są zależne od kategorii rozmiaru dysków

Podstawa kosztu w przypadku magazyn w warstwie Premium nie jest woluminem rzeczywiste dane przechowywane w tych dysków, ale kategorii rozmiar takiego dysku, niezależnie od ilości danych przechowywanych na tym dysku.

Można również tworzyć dyski na magazyn w warstwie Premium nie są bezpośrednio mapowania na kategorie rozmiar wyświetlany. Może to być case, szczególnie w przypadku kopiowania dysków z magazynu w warstwie standardowa do magazyn w warstwie Premium. W takich przypadkach odbywa się mapowania do następnej opcji największy dysk magazyn w warstwie Premium.

Należy pamiętać, że niektóre serii maszyn wirtualnych mogą korzystać z usługi Azure Premium Storage. Począwszy od grudnia 2015 r. są serii GS i DS. Serii DS jest zasadniczo taki sam jak D-series, z wyjątkiem serii DS zdolność do instalacji Premium magazynu maszyn wirtualnych dodatkowo do dysków, które znajdują się na podstawie usługi Azure Standard Storage. Samo jest prawidłowy dla serii G, w porównaniu do serii GS.

Jeśli jest wyewidencjonowywany część serii DS maszyn wirtualnych w [w tym artykule (Linux)] [ virtual-machines-sizes-linux] i [w tym artykule (system Windows)][virtual-machines-sizes-windows], należy pamiętać, że są ograniczenia woluminu danych na dyskach magazyn w warstwie Premium na stopień szczegółowości poziom maszyny Wirtualnej. Innej serii DS lub GS-series maszyn wirtualnych także mieć różne ograniczenia w odniesieniu do liczby dysków z danymi, które można zainstalować. Te limity opisano w artykule również wymienione powyżej. Jednak w zasadzie oznacza to, że jeśli, na przykład zainstalować 32 x P30 dysków do maszyny Wirtualnej z jednym DS14 nie uzyskasz 32 x maksymalną przepływność dysku P30. Zamiast tego maksymalną przepustowość na poziomie maszyny Wirtualnej zgodnie z opisem w artykule ogranicza przepływność danych.

Więcej informacji na temat magazyn w warstwie Premium można znaleźć tutaj: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Dyski zarządzane
Dyski zarządzane są nowego typu zasobu w usłudze Azure Resource Manager używany zamiast wirtualne dyski twarde, które są przechowywane na kontach magazynu Azure. Dyski zarządzane automatycznie Dopasuj z zestawu dostępności maszyny wirtualnej, które są dołączone i w związku z tym zwiększenia dostępności maszyny wirtualnej i usług, które są uruchomione na maszynie wirtualnej. Aby uzyskać więcej informacji, przeczytaj [artykuł z omówieniem](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Firma Microsoft zaleca, aby użyć dysku zarządzanego, ponieważ ich uprościć wdrażanie i zarządzanie maszyn wirtualnych.
SAP aktualnie obsługuje tylko dysków zarządzanych w warstwie Premium. Aby uzyskać więcej informacji, przeczytaj Uwaga SAP [1928533].

#### <a name="azure-storage-accounts"></a>Konta usługi Azure Storage
W przypadku wdrażania usług lub maszynami wirtualnymi na platformie Azure, wdrożenie wirtualne dyski twarde i obrazów maszyn wirtualnych można organizować w jednostki o nazwie konta magazynu Azure. Podczas planowania wdrożenia usługi Azure, należy rozważyć ograniczenia Azure. Po jednej stronie istnieje ograniczona liczba kont magazynu dla subskrypcji platformy Azure. Mimo że każdego konta magazynu Azure może zawierać dużą liczbę plików VHD, na całkowitą liczbę IOPS na konto magazynu jest stały limit. W przypadku wdrażania setki SAP maszyn wirtualnych z systemami DBMS tworzenia znaczących wywołań we/wy, zalecane jest dystrybucji wysokiej maszyn wirtualnych systemu DBMS IOPS między wieloma kontami magazynu Azure. Należy uważać, aby nie przekroczyć bieżącego limitu konta magazynu Azure dla subskrypcji. Ponieważ Magazyn jest to ważna część wdrożenia bazy danych systemu SAP, to pojęcie omówiono bardziej szczegółowo w już przywoływana [DBMS Deployment Guide][dbms-guide].

Więcej informacji o kontach magazynu Azure można znaleźć w [w tym artykule][storage-scalability-targets]. Odczyt w tym artykule, okazuje się, że ma różnic w ograniczeniach standardowe konta magazynu Azure i kont magazynu w warstwie Premium. Najważniejsze różnice są ilość danych, które mogą być przechowywane w ramach konta magazynu. W magazynie standardowe wolumin jest wielkości większy niż z magazyn w warstwie Premium. Z drugiej strony, standardowe konto magazynu jest znacznie ograniczone w IOPS (zobacz kolumnę **całkowita liczba żądań**), natomiast takie ograniczenie nie ma konta magazynu Azure Premium. Omówimy szczegóły oraz wyniki tych różnic w opisach wdrożenia SAP systemów, zwłaszcza na serwerach systemu DBMS.

W ramach konta magazynu możesz mieć możliwość tworzenia różnych kontenerów do organizowaniu i klasyfikacji różnych wirtualne dyski twarde. Kontenery są zwykle używane do, na przykład oddzielnych wirtualne dyski twarde różnych maszyn wirtualnych. Nie ma wpływu na wydajność przy użyciu kontenera tylko jednego lub wielu kontenerów poniżej jedno konto magazynu Azure.

W systemie Azure Nazwa wirtualnego dysku twardego obejmuje następujące nazewnictwa połączenie, które należy podać unikatową nazwę dysku VHD w obrębie platformy Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Jak wspomniano powyżej ciąg musi jednoznacznie zidentyfikować wirtualnego dysku twardego, który jest przechowywany w magazynie Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Sieć platformy Microsoft Azure
Microsoft Azure udostępnia infrastrukturę sieci, dzięki czemu mapowanie wszystkie scenariusze, które chcesz zrealizować przy użyciu oprogramowania SAP. Dostępne są następujące możliwości:

* Dostęp z zewnątrz, bezpośrednio do maszyn wirtualnych za pomocą usług terminalowych systemu Windows lub ssh/VNC
* Dostęp do usług i określone porty używane przez aplikacje w obrębie maszyn wirtualnych
* Wewnętrznej komunikacji i rozpoznawanie nazw między grupą maszyn wirtualnych wdrożonych jako maszynach wirtualnych platformy Azure
* Łączność między lokalizacjami sieci lokalnej klienta i sieć platformy Azure
* Krzyżowe regionu Azure lub łączności centrum danych między lokacjami systemu Azure

Więcej informacji można znaleźć tutaj: <https://azure.microsoft.com/documentation/services/virtual-network/>

Istnieje wiele różnych możliwości skonfiguruj nazwę i rozpoznawania adresu IP na platformie Azure. W tym dokumencie scenariusze tylko w chmurze korzystają z domyślnego za pomocą usługi Azure DNS (w przeciwieństwie do definiowania własnych usługi DNS). Istnieje również nową usługę Azure DNS, którego można użyć zamiast konfigurowania serwera DNS. Więcej informacji można znaleźć w [w tym artykule] [ virtual-networks-manage-dns-in-vnet] i na [tej strony](https://azure.microsoft.com/services/dns/).

W scenariuszach między różnymi lokalizacjami, możemy polegania na fakt, że lokalną DNS-AD/OpenLDAP został rozszerzony za pośrednictwem sieci VPN lub prywatną na platformie Azure. Dla niektórych scenariuszy zgodnie z opisem w tym miejscu, może być konieczne repliki AD/OpenLDAP zainstalowane na platformie Azure.

Ponieważ sieci i rozpoznawanie nazw jest to ważna część wdrożenia bazy danych systemu SAP, to pojęcie jest omówiona bardziej szczegółowo w [DBMS Deployment Guide][dbms-guide].

##### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure
Przez tworzenie sieci wirtualnej platformy Azure, można zdefiniować zakres adresów prywatnych adresów IP przydzielone przez funkcje protokołu DHCP Azure. W scenariuszach między różnymi lokalizacjami zakres adresów IP zdefiniowanych wciąż jest przydzielony przy użyciu protokołu DHCP przez platformę Azure. Jednak rozpoznawania nazw domeny odbywa się lokalnie (przy założeniu, że maszyny wirtualne są częścią domeny lokalnej) i dlatego można rozwiązać adresów poza różnych usługach w chmurze Azure.

Każdej maszyny wirtualnej platformy Azure musi być podłączony do sieci wirtualnej.

Więcej informacji można znaleźć w [w tym artykule] [ resource-groups-networking] i na [tej strony](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO można znaleźć artykuł, w tym temacie OpenLDAP + ARM; )
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> Domyślnie po wdrożeniu maszyny Wirtualnej nie można zmienić konfiguracji sieci wirtualnej. Ustawienia protokołu TCP/IP musi pozostać serwerowi Azure DHCP. Domyślnym zachowaniem jest przypisanie dynamicznego adresu IP.
>
>

Adres MAC karty sieci wirtualnej mogą ulec zmianie, na przykład po zmiany rozmiaru i gościa Windows lub Linux, OS przejmuje nowej karty sieciowej i automatycznie używa protokołu DHCP do przypisywania adresów IP i DNS w takim przypadku.

##### <a name="static-ip-assignment"></a>Przypisanie statycznego adresu IP
Istnieje możliwość Przypisz fixed lub zastrzeżone adresy IP do maszyn wirtualnych w ramach sieci wirtualnej platformy Azure. Uruchamianie maszyn wirtualnych w sieci wirtualnej platformy Azure otwiera dużą możliwość wykorzystać tę funkcję, jeśli potrzebne lub wymagane w przypadku niektórych scenariuszy. Przypisanie IP pozostaje ważny w całym istnienie maszyny Wirtualnej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona lub zamknięcie. W związku z tym które należy wykonać ogólną liczbę maszyn wirtualnych (maszyny Wirtualne uruchomione i zatrzymane) pod uwagę podczas definiowania zakresu adresów IP dla sieci wirtualnej. Adres IP jest przypisana do momentu usunięcia maszyny Wirtualnej i jej interfejsu sieciowego lub dopóki adres IP cofnąć pobiera ponownie przypisany. Aby uzyskać więcej informacji, przeczytaj [w tym artykule][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Wiele kart sieciowych dla maszyny Wirtualnej
Można zdefiniować wiele karty interfejsu sieci wirtualnej (vNIC) dla maszyny wirtualnej platformy Azure. Z możliwością ma wiele vNICs, które można uruchomić, aby skonfigurować ruchu sieciowego rozdzielenie w przypadku gdy, na przykład klient ruch jest kierowany przez jeden ruchu vNIC i wewnętrznej bazy danych jest kierowany przez vNIC drugiego. Zależne od typu maszyny Wirtualnej są różne ograniczenia w odniesieniu do liczby vNICs. Dokładne szczegóły, funkcje i ograniczenia można znaleźć w następujących artykułach:

* [Tworzenie maszyny Wirtualnej systemu Windows z wieloma kartami sieciowymi][virtual-networks-multiple-nics-windows]
* [Utwórz Maszynę wirtualną systemu Linux z wieloma kartami sieciowymi][virtual-networks-multiple-nics-linux]
* [Wdrażanie maszyn wirtualnych kart Sieciowych multi przy użyciu szablonu][virtual-network-deploy-multinic-arm-template]
* [Wdrażanie maszyn wirtualnych kart Sieciowych multi przy użyciu programu PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Wdrażanie maszyn wirtualnych kart Sieciowych multi przy użyciu wiersza polecenia platformy Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Połączenie lokacja lokacja
Między lokalizacjami jest maszynach wirtualnych platformy Azure i lokalnymi połączone przejrzyste i trwałe połączenie sieci VPN. Oczekuje się stać się najbardziej typowych wzorzec wdrożenia SAP na platformie Azure. Zakłada się, że procedury działania i procesy z wystąpieniem SAP na platformie Azure powinny działać przezroczysty. To oznacza, że powinno być możliwe do drukowania poza te systemy za pomocą systemu zarządzania transportu (TMS) SAP, aby transportu zmienia się z systemu Programowanie w usłudze Azure systemu testowego jest wdrożona lokalnie. Więcej dokumentacji wokół lokacja lokacja można znaleźć w [w tym artykule][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Urządzenia tunel sieci VPN
Aby utworzyć połączenie lokacja lokacja (lokalnego centrum danych do centrum danych Azure), należy uzyskać i skonfigurowanie urządzenia sieci VPN lub użyć usługi Routing i dostęp zdalny (RRAS), która została wprowadzona jako składnik oprogramowania w systemie Windows Server 2012.

* [Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja za pomocą programu PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [O urządzeniach sieci VPN dla połączenia bramy sieci VPN typu lokacja-lokacja][vpn-gateway-about-vpn-devices]
* [Brama sieci VPN — często zadawane pytania][vpn-gateway-vpn-faq]

![Połączenie lokacja lokacja między lokalną i platformą Azure][planning-guide-figure-600]

Na rysunku powyżej przedstawiono dwie subskrypcji platformy Azure mają podzakresów adres IP zarezerwowany do użycia w sieciach wirtualnych platformy Azure. Łączność z siecią lokalną Azure jest nawiązywane za pośrednictwem sieci VPN.

#### <a name="point-to-site-vpn"></a>Sieć VPN punkt lokacja
Sieć VPN punkt lokacja wymaga każdy komputer kliencki nawiązać połączenia z własną sieci VPN na platformie Azure. W scenariuszach SAP, który czekamy na połączenie punkt lokacja nie jest praktyczne. W związku z tym nie dalsze odwołania mają połączenie sieci VPN typu punkt lokacja.

Więcej informacji można znaleźć tutaj
* [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Obejmujący wiele lokacji sieci VPN
System Azure oferuje również dzisiaj możliwości, aby utworzyć połączenie z siecią VPN obejmujący wiele lokacji dla jedną subskrypcją platformy Azure. Wcześniej jedną subskrypcją były ograniczone do jednego połączenia sieci VPN lokacja lokacja. To ograniczenie zostało usunięte z połączenia sieci VPN z wieloma lokacjami dla jednej subskrypcji. Dzięki temu można korzystać z więcej niż jeden Region platformy Azure dla określonej subskrypcji za pomocą konfiguracji między lokalizacjami.

Więcej informacji dotyczących dokumentacji, zobacz [w tym artykule][vpn-gateway-create-site-to-site-rm-powershell]

[comment]: <> (MShermannd TODO nie znaleziono żadnego łącza doku ARM)

#### <a name="vnet-to-vnet-connection"></a>Sieć wirtualna połączenia sieci wirtualnej
Przy użyciu sieci VPN w wielu lokacjach, należy skonfigurować oddzielny sieci wirtualnej platformy Azure w poszczególnych regionach. Należy jednak bardzo często z wymaganiem, aby skontaktować się ze składnikami oprogramowania w różnych regionach ze sobą. W idealnym przypadku komunikacji powinny być kierowane z jednego regionu Azure do środowiska lokalnego, a w regionie Azure. Skrót Azure oferuje możliwości, aby skonfigurować połączenie z jedną sieć wirtualną platformy Azure w jednym regionie do innej sieci wirtualnej platformy Azure hostowanej w innym regionie. Ta funkcja jest wywoływana połączenia do wirtualnymi. Więcej informacji na temat tej funkcji można znaleźć tutaj: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Prywatne połączenia usługi Azure expressroute
Microsoft Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrach danych platformy Azure i infrastruktury lokalnej przez klienta lub w środowisku wspólnej lokalizacji. ExpressRoute jest oferowanych przez różnych MPLS dostawców sieci VPN (przełączaniem pakietów) lub innych dostawców usług sieciowych. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Połączenia ExpressRoute oferują wyższy poziom zabezpieczeń, niezawodności więcej za pomocą wielu równoległych obwodów, szybkości szybsze i opóźnienia niższa niż typowe połączenia za pośrednictwem Internetu.

Znajdź więcej informacji na temat usługi Azure ExpressRoute i ofert tutaj:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Usługi Express Route umożliwia wielu subskrypcji platformy Azure za pośrednictwem jednego obwodu ExpressRoute, zgodnie z opisem w tym miejscu

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Wymuszone tunelowanie w przypadku między lokalizacjami
Dla maszyn wirtualnych, przyłączanie do domeny lokalnej za pośrednictwem lokacja lokacja, punkt lokacja lub ExpressRoute musisz upewnij się, że wdrożono pobieranie ustawień internetowego serwera proxy dla wszystkich użytkowników w tych maszyn wirtualnych oraz. Domyślnie oprogramowanie uruchomione na tych maszynach wirtualnych lub użytkownikom dostęp do Internetu za pomocą przeglądarki sieci nie przejdzie za pośrednictwem serwera proxy firmy, ale może zostać nawiązane bezpośrednio za pośrednictwem platformy Azure z Internetem. Ale nawet ustawienie serwera proxy nie jest rozwiązaniem 100% do kierowania ruchu za pośrednictwem serwera proxy firmy, ponieważ jest on odpowiedzialny za oprogramowania i usług, aby sprawdzić, czy serwer proxy. Jeśli administrator manipuluje ustawienia oprogramowania uruchomionego na maszynie wirtualnej nie jest operacją, ruch do Internetu można ponownie detoured bezpośrednio za pośrednictwem platformy Azure z Internetem.

Aby tego uniknąć, można skonfigurować tunelowania wymuszonego połączenie lokacja lokacja między lokalną i platformą Azure. Szczegółowy opis funkcji tunelowania wymuszonego zostanie opublikowana tutaj <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Tunelowanie wymuszone z ExpressRoute jest włączana przez klientów anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Podsumowanie sieci platformy Azure
Ten rozdział zawiera wiele ważne kwestie dotyczące sieci platformy Azure. Poniżej przedstawiono podsumowanie głównych punktów:

* Umożliwia konfigurowanie sieci według własnych potrzeb sieci wirtualnych platformy Azure
* Sieci wirtualnych platformy Azure można użyć do przypisania zakresów adresów IP do maszyn wirtualnych lub przypisanie stałych adresów IP do maszyn wirtualnych
* Aby skonfigurować połączenie lokacja-lokacja lub punkt-lokacja należy najpierw utworzyć sieci wirtualnej platformy Azure
* Po wdrożeniu maszyny wirtualnej nie jest już możliwość zmiany przypisane do maszyny Wirtualnej sieci wirtualnej

### <a name="quotas-in-azure-virtual-machine-services"></a>Przydziały w usługach Azure maszyny wirtualnej
Musimy jednoznaczne fakt, że infrastruktury magazynu i sieci jest współużytkowana przez maszyny wirtualne z systemami wielu usług w infrastrukturze Azure. I tak jak w przypadku centrów danych przez klienta, przerostu niektórych zasobów infrastruktury została wykonana w stopniu. Platformy Microsoft Azure używa dysku, Procesora, sieci i innych przydziałów, aby ograniczyć zużycie zasobów i zachować spójność i deterministyczna wydajności.  Różne typy maszyny Wirtualnej (A5, A6 itp.) mają różne przydziały na potrzeby liczba dysków, Procesora, pamięci RAM i sieci.

> [!NOTE]
> Zasoby Procesora i pamięci typów maszyn wirtualnych obsługiwanych przez SAP są wstępnie przydzielić na węzłach hostów. Oznacza to, że po wdrożeniu maszyny Wirtualnej na hoście dostępnych zasobów zgodnie z definicją typu maszyny Wirtualnej.
>
>

Podczas planowania i zmiany rozmiaru SAP rozwiązania Azure należy uwzględnić limity przydziału dla każdego rozmiaru maszyny wirtualnej. Przydziały maszyny Wirtualnej są opisane [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (system Windows)][virtual-machines-sizes-windows].

Przydziały opisane reprezentują teoretyczna maksymalna.  Limit IOPS dla każdego dysku może zostać osiągnięty przy małych IOs (8kb), ale prawdopodobnie nie może zostać osiągnięty przy dużych IOs (1Mb).  Wartości IOPS są wymuszane na poziom szczegółowości jednego dysku.

Poniżej drzewa decyzyjnego mogą być używane jako drzewo decyzyjne nierównej zdecydować, czy systemu SAP dopasowuje się do usług Azure maszyny wirtualnej i jej możliwości lub czy istniejący system musi być inaczej skonfigurowana w celu wdrożenia systemu Azure:

![Drzewo decyzyjne podjęcie decyzji, możliwość wdrożenia SAP na platformie Azure][planning-guide-figure-700]

**Krok 1**: najważniejsze informacje się rozpoczynać wymaganie protokoły SAP dla danego systemu SAP. Wymagania dotyczące punktu SAP należy rozdzielać do systemu DBMS i SAP część aplikacji, nawet jeśli systemu SAP jest już wdrożony w firmie w konfiguracji warstwy 2. Istniejących systemów punktu SAP, często powiązane z używany sprzęt można ustalić lub w oparciu o istniejących testów porównawczych SAP. Wyniki można znaleźć tutaj: <http://global.sap.com/campaigns/benchmark/index.epx>.
W nowo wdrożonym systemach SAP powinien przejściu ćwiczeniu zmiany rozmiaru powinien ustalić wymagania protokoły SAP systemu.
Zobacz też ten blog i dołączonego dokumentu dla programu SAP rozmiaru na platformie Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Krok 2**: istniejących systemów powinno być mierzone we/wy woluminu i operacji We/Wy na sekundę na serwerze systemu DBMS. Dla nowo planowane systemów wykonywania zmiany rozmiaru do nowego systemu również nadać nierównej pomysły wymagań operacji We/Wy na stronie system DBMS. Jeśli nie wiesz, należy po pewnym czasie przeprowadzania Weryfikacja koncepcji.

**Krok 3**: porównanie wymaganie protokoły SAP dla serwera systemu DBMS z punktu SAP zapewniają różne typy maszyny Wirtualnej Azure. Informacje na temat protokoły SAP różnych typach maszyn wirtualnych Azure opisano w Uwaga SAP [1928533]. Należy się skoncentrować na Maszynie wirtualnej systemu DBMS najpierw od warstwy bazy danych jest warstwą w systemie SAP NetWeaver, która nie skalować w poziomie w większości wdrożeń. Z kolei SAP warstwy aplikacji może być skalowana w poziomie. Jeśli brak SAP obsługiwane typy maszyn wirtualnych Azure zapewnia wymaganego punktu SAP, obciążenie systemu SAP planowane nie można uruchomić na platformie Azure. Należy albo wdrożyć system lokalny lub musisz zmienić woluminu obciążenie systemu.

**Krok 4**: zgodnie z opisem [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (system Windows)][virtual-machines-sizes-windows], Azure wymusza przydziału IOPS dla każdego dysku niezależnie od tego, czy używasz magazynu w warstwie standardowa lub Premium Storage. Zależne od typu maszyny Wirtualnej, liczba dysków danych, które można zainstalować zależy. W związku z tym można obliczyć maksymalną liczbę IOPS, który może zostać osiągnięty przy każdej z różnych typach maszyn wirtualnych. Zależne od układ pliku bazy danych, można paskowych dysków, aby stać się jeden wolumin w system operacyjny gościa. Jednak jeśli bieżącego woluminu IOPS wdrożonego systemu SAP przekracza limity obliczeniowej największy typu maszyny Wirtualnej platformy Azure i jeśli nie ma możliwość kompensacji więcej pamięci, obciążenie systemu SAP może mieć wpływ na poważnie. W takich przypadkach można trafiony punkt, w których nie należy wdrażać systemu Azure.

**Krok 5**: szczególnie w systemach SAP, które są wdrożone lokalnie w konfiguracji warstwy 2, prawdopodobnie systemu może być konieczne można skonfigurować na platformie Azure w konfiguracji warstwy 3. W tym kroku należy sprawdzić, czy w warstwie aplikacji SAP, który nie może być skalowana w poziomie i nie będzie dopasowane do zasobów Procesora i pamięci, które oferują różne typy maszyny Wirtualnej platformy Azure jest składnikiem. Jeśli w rzeczywistości jest takich składników, systemu SAP i jego obciążenie nie można wdrożyć na platformie Azure. Ale jeśli można skalować w poziomie składników aplikacji SAP do wielu maszyn wirtualnych platformy Azure, system można wdrożyć na platformie Azure.

**Krok 6**: Jeśli system DBMS i SAP składników warstwy aplikacji mogą być uruchamiane na maszynach wirtualnych Azure, konfiguracji musi być zdefiniowana w odniesieniu do:

* Liczba maszyn wirtualnych platformy Azure
* Typy maszyn wirtualnych dla poszczególnych składników
* Liczba wirtualnych dysków twardych w maszynie Wirtualnej systemu DBMS zapewnienie wystarczającej liczby IOPS

## <a name="managing-azure-assets"></a>Zarządzanie zasobami Azure
### <a name="azure-portal"></a>Azure Portal
Azure portal to jeden z trzech interfejsów Zarządzanie wdrożeniami maszyny Wirtualnej platformy Azure. Zadania podstawowe możliwości zarządzania, takich jak wdrażanie maszyn wirtualnych z obrazów, może odbywać się za pośrednictwem portalu Azure. Ponadto Tworzenie konta magazynu, sieci wirtualnych i inne składniki platformy Azure są również zadania, które portalu Azure może obsługiwać bardzo dobrze. Jednak funkcji takich jak przekazywanie wirtualne dyski twarde z lokalnej na platformie Azure lub kopiowanie dysku VHD w obrębie platformy Azure są zadania, które wymagają narzędzi innych firm lub administrowanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.

![Portal Microsoft Azure — omówienie maszyny wirtualnej][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Zadania zarządzania i konfiguracji dla wystąpienia maszyny wirtualnej są możliwe od w portalu Azure.

Oprócz ponowne uruchamianie i zamykanie maszyny wirtualnej można również dołączyć, odłącz i tworzyć dyski danych dla wystąpienia maszyny wirtualnej do przechwytywania wystąpienia obrazu przygotowania i skonfiguruj rozmiar wystąpienie maszyny wirtualnej.

Azure portal zapewnia podstawowe funkcje do wdrożenia i skonfigurowania maszyn wirtualnych i wiele innych usług Azure. Jednak nie wszystkie dostępne funkcje uwzględniona w portalu Azure. W portalu Azure nie jest możliwe do wykonania zadania, takie jak:

* Przekazywanie wirtualne dyski twarde w systemie Azure
* Kopiowanie maszyn wirtualnych

[comment]: <> (MShermannd TODO co temat automatyzacji usługi dla maszyn wirtualnych SAP? )
[comment]: <> (MSSedusch wdrażania wielu maszyn wirtualnych systemu operacyjnego w tym samym czasie możliwe)
[comment]: <> (MSSedusch również dowolnego typu automatyzacji dotyczące wdrożenia nie jest możliwe za pomocą portalu Azure. Zadania, takie jak inicjowanych przez skrypty wdrażania wielu maszyn wirtualnych nie jest możliwe za pośrednictwem portalu Azure.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Zarządzanie za pomocą poleceń cmdlet programu PowerShell usługi Microsoft Azure
Windows PowerShell to zaawansowany i rozszerzalny platforma, która ma zostały powszechnie zaakceptowany przez klientów wdrażanie większej liczby systemów na platformie Azure. Po zakończeniu instalacji poleceń cmdlet programu PowerShell na pulpitu, laptopów i dedykowanych stację można zdalnie uruchamiać polecenia cmdlet programu PowerShell.

Proces do włączenia lokalnego pulpitu/laptop do użycia polecenia cmdlet programu PowerShell Azure i sposobu konfigurowania tych do użycia z subskrypcji platformy Azure jest opisany w [w tym artykule][powershell-install-configure].

Szczegółowe kroki dotyczące instalowania, aktualizowanie i konfigurowanie programu Azure PowerShell, polecenia cmdlet można także znaleźć w [ten rozdział przewodnika wdrażania programu][deployment-guide-4.1].

Obsługi klienta do tej pory została środowiska PowerShell (PS) jest oczywiście bardziej zaawansowane narzędzia do wdrażania maszyn wirtualnych oraz do tworzenia niestandardowych krokach wdrażanie maszyn wirtualnych. Wszyscy klienci uruchomione wystąpienia programu SAP na platformie Azure są przy użyciu poleceń cmdlet środowiska PS uzupełnienie zadań zarządzania w portalu Azure lub nawet są przy użyciu poleceń cmdlet środowiska PS wyłącznie do wdrożeń na platformie Azure. Ponieważ polecenia cmdlet dotyczące usługi Azure mają tej samej konwencji nazewnictwa jak ponad 2000 poleceń cmdlet związanych z systemu Windows, jest łatwe zadań administratorów systemu Windows należy korzystać z tych poleceń cmdlet.

Zobacz przykład tutaj: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO opisano nowe polecenia interfejsu wiersza polecenia podczas testowania )
Wdrażanie rozszerzenia monitorowania Azure dla programu SAP (zobacz rozdział [rozwiązanie monitorowanie Azure dla programu SAP] [ planning-guide-9.1] w tym dokumencie) jest możliwe tylko za pomocą programu PowerShell lub interfejsu wiersza polecenia. W związku z tym jest to konieczne do instalowania i konfigurowania programu PowerShell lub interfejsu wiersza polecenia podczas wdrażania i administrowania systemem SAP NetWeaver na platformie Azure.  

Jako platforma Azure oferuje więcej funkcji, nowe polecenia cmdlet PS są mają zostać dodane, który wymaga aktualizacji poleceń cmdlet. W związku z tym warto sprawdzić witrynę Azure Pobierz co najmniej raz w miesiącu <https://azure.microsoft.com/downloads/> nowej wersji poleceń cmdlet. Nowa wersja jest zainstalowany na starszej wersji.

Listę ogólne dotyczące usługi Azure PowerShell polecenia Sprawdź tutaj: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Zarządzanie za pomocą polecenia interfejsu wiersza polecenia programu Microsoft Azure
Dla klientów, którzy systemu Linux i chce zarządzać Azure zasoby programu Powershell nie mogą być opcję. Firma Microsoft oferuje wiersza polecenia platformy Azure jako alternatywę.
Wiersza polecenia platformy Azure oferuje zestaw typu open source, obsługujący wiele platform polecenia dotyczące pracy z platformą Azure. Interfejsu wiersza polecenia Azure oferuje wiele funkcji w portalu Azure.

Informacje o instalacji, konfiguracji i sposób użycia interfejsu wiersza polecenia Zobacz polecenia do wykonania zadań Azure

* [Instalowanie interfejsu wiersza polecenia platformy Azure][xplat-cli]
* [Wdrażania i zarządzania maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Użyj interfejsu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows za pomocą Menedżera zasobów Azure][xplat-cli-azure-resource-manager]

Również przeczytanie rozdziału [wiersza polecenia platformy Azure dla maszyn wirtualnych systemu Linux] [ deployment-guide-4.5.2] w [Deployment Guide] [ planning-guide] na temat sposobu Użyj interfejsu wiersza polecenia Azure, aby wdrożyć rozszerzenie monitorowania Azure dla programu SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Różne sposoby wdrażania maszyn wirtualnych dla programu SAP na platformie Azure
W tym rozdziale dowiesz się różne sposoby, aby wdrożyć Maszynę wirtualną na platformie Azure. W tym rozdziale opisano procedury dodatkowych przygotowań, a także obsługę wirtualnych dysków twardych i maszyny wirtualne na platformie Azure.

### <a name="deployment-of-vms-for-sap"></a>Wdrażanie maszyn wirtualnych dla programu SAP
Microsoft Azure oferuje wiele sposobów wdrażania maszyn wirtualnych i skojarzone dyski. W związku z tym jest bardzo ważne poznać różnice, od przygotowania maszyn wirtualnych może się różnić w zależności od metody wdrażania. Ogólnie rzecz biorąc firma Microsoft zapoznaj się z informacjami w następujących scenariuszach:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Przenoszenie maszyny Wirtualnej z lokalnej na platformie Azure przy użyciu dysku z systemem innym niż uogólniony
Ma zostać przeniesiona do określonego systemu SAP z lokalnej na platformie Azure. Można to zrobić przez przekazanie dysku VHD zawierającego system operacyjny, plików binarnych programu SAP i plików binarnych systemu DBMS oraz wirtualne dyski twarde plikami danych i dziennika systemu DBMS na platformie Azure. Contrast do [scenariusza #2 poniżej][planning-guide-5.1.2], Zachowaj nazwę hosta, SAP SID i kont użytkowników SAP w Maszynie wirtualnej Azure, ponieważ zostały one skonfigurowane w środowisku lokalnym. W związku z tym uogólnianie obrazu nie jest konieczne. Patrz rozdział [przygotowania do przenoszenia maszyny Wirtualnej z lokalnej na platformie Azure przy użyciu dysku z systemem innym niż uogólniony] [ planning-guide-5.2.1] tego dokumentu procedury przygotowania lokalnych i przekazywania-uogólniony maszyn wirtualnych lub wirtualne dyski twarde w systemie Azure. Rozdział odczytu [Scenariusz 3: przenoszenie maszyny Wirtualnej z lokalnymi przy użyciu-uogólniony wirtualny dysk twardy Azure z SAP] [ deployment-guide-3.4] w [Deployment Guide] [ deployment-guide] szczegółowy opis kroków wdrażania obrazu na platformie Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Wdrażanie maszyny Wirtualnej z obrazu właściwe dla klienta
Z powodu szczególne wymagania dotyczące tej wersji systemu operacyjnego lub DBMS podane obrazów w portalu Azure Marketplace może nie odpowiadają potrzebom. W związku z tym konieczne może utworzyć Maszynę wirtualną przy użyciu własnego prywatnej obrazu systemu operacyjnego/DBMS maszyny Wirtualnej, można wdrożyć kilka razy później. Aby przygotować obraz prywatnych do duplikacji, powinny być traktowane następujące elementy:

- - -
> ![Windows][Logo_Windows] Windows
>
> Dowiedz się więcej tutaj: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> ustawień systemu Windows (takich jak Windows SID i nazwy hosta) musi być pobieranej/uogólniony na lokalnej maszynie Wirtualnej za pomocą polecenia sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Wykonaj kroki opisane w tych artykułach dla [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], lub [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], aby przygotować dysk VHD do przekazania do platformy Azure.
>
>

- - -
Jeśli zainstalowano już SAP zawartości w lokalnej maszyny Wirtualnej (szczególnie w przypadku systemów warstwy 2), można dostosować ustawienia systemu SAP po wdrożenia maszyny Wirtualnej platformy Azure za pomocą wystąpienia nazwy procedury obsługiwana przez Menedżera inicjowania obsługi oprogramowania SAP (Uwaga SAP [1619720]). Patrz rozdział [przygotowania do wdrożenia maszyny Wirtualnej z obrazu specyficzne dla programu SAP] [ planning-guide-5.2.2] i [przekazywanie wirtualnego dysku twardego z lokalnej na platformie Azure] [ planning-guide-5.3.2] tego dokumentu procedury przygotowania lokalnych i przekazywania ogólnych maszyny wirtualnej na platformie Azure. Rozdział odczytu [Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazu niestandardowego dla SAP] [ deployment-guide-3.3] w [Deployment Guide] [ deployment-guide] szczegółowy opis kroków wdrażania obrazu na platformie Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Wdrażanie maszyn wirtualnych z poziomu portalu Azure Marketplace
Czy chcesz użyć programu Microsoft lub innych firm pod warunkiem obrazu maszyny Wirtualnej z portalu Azure Marketplace do wdrożenia maszyny Wirtualnej. Po wdrożeniu maszyny Wirtualnej na platformie Azure, wykonaj te same wskazówki i narzędzia do zainstalowania oprogramowania SAP i/lub DBMS wewnątrz maszyny Wirtualnej, jak w środowisku lokalnym. Aby uzyskać bardziej szczegółowe opis wdrożenia, zobacz rozdział [scenariusz 1: Wdrażanie maszyny Wirtualnej poza Azure Marketplace w celu SAP] [ deployment-guide-3.2] w [Deployment Guide][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Przygotowywanie maszyn wirtualnych z programu SAP do platformy Azure
Przed przekazaniem maszyn wirtualnych na platformie Azure, należy upewnić się, że maszyny wirtualne i wirtualne dyski twarde spełnienia określonych wymagań. Istnieją niewielkie różnice w zależności od metody wdrażania, który jest używany.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Przygotowanie do przenoszenia maszyny Wirtualnej z lokalnej na platformie Azure przy użyciu dysku z systemem innym niż uogólniony
Częstą metodą wdrażania jest aby przenieść istniejącą maszynę Wirtualną, w którym jest uruchomiona systemie SAP z lokalnej na platformie Azure. Tej maszyny Wirtualnej i systemu SAP na maszynie wirtualnej, po prostu należy uruchomić na platformie Azure przy użyciu tej samej nazwy hosta i najprawdopodobniej ten sam identyfikator SID SAP. W takim przypadku gościa systemu operacyjnego maszyny Wirtualnej nie można uogólnić dla wielu wdrożeń. Jeśli sieć lokalną został rozszerzony na platformie Azure (zobacz rozdział [między lokalizacjami - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure z wymaganiami jest w pełni zintegrowany z siecią lokalną] [ planning-guide-2.2] w tym dokumencie), wówczas nawet tego samego konta domeny może być używana w maszyny Wirtualnej, ponieważ zostały one wykorzystywane przed lokalnymi.

Wymagania dotyczące podczas przygotowywania własne dysku maszyny Wirtualnej Azure są:

* Pierwotnie dysku VHD zawierającego system operacyjny może mieć maksymalnie rozmiar wynosi 127GB tylko. To ograniczenie został wyeliminowany z końcem marca 2015 roku. Teraz dysku VHD zawierającego system operacyjny może być również inne usługi Azure Storage hostowanej wirtualnego dysku twardego o rozmiarze do 1TB.
* Musi on być stały format wirtualnego dysku twardego. Dynamicznych wirtualnych dysków twardych lub wirtualnych dysków twardych w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statycznych wirtualne dyski twarde podczas przekazywania wirtualnego dysku twardego z apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Wirtualne dyski twarde, które są zainstalowane na maszynie wirtualnej i powinien być instalowany ponownie na platformie Azure na potrzeby maszyny Wirtualnej można również stały format wirtualnego dysku twardego. Przeczytaj [w tym artykule (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) i [w tym artykule (system Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) limitów rozmiaru dysków danych. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statycznych wirtualne dyski twarde podczas przekazywania wirtualnego dysku twardego z apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Dodawanie innego lokalnego konta z uprawnieniami administratora, które mogą być używane przez pomocy technicznej firmy Microsoft lub które można przypisać jako kontekst dla usług i aplikacji do uruchamiania w dopóki maszyna wirtualna jest wdrożona i bardziej odpowiednie użytkowników może zostać użyty.
* W przypadku scenariusz wdrożenia tylko w chmurze (zobacz rozdział [tylko w chmurze — wdrożenia maszyny wirtualnej na platformie Azure bez zależności w sieci lokalnej klienta] [ planning-guide-2.1] tego dokumentu) w połączeniu z tej metody wdrażania, kont domeny może nie działać po wdrożeniu dysku platformy Azure na platformie Azure. Jest to szczególnie ważne dla konta, które są używane do uruchamiania usług, takich jak aplikacje systemu DBMS lub SAP. W związku z tym należy zastąpić takich kont domeny kont lokalnych maszyn wirtualnych i usunąć lokalnych kont domeny na maszynie wirtualnej. Utrzymywanie lokalnych użytkowników domeny w obrazie maszyny Wirtualnej nie jest problemem, gdy maszyna wirtualna jest wdrażany w scenariuszu obejmującym różne pomieszczenia, zgodnie z opisem w rozdziale [między lokalizacjami - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure z wymaganiami jest w pełni zintegrowany z siecią lokalną] [ planning-guide-2.2] w tym dokumencie.
* Jeśli konta domeny były używane jako nazwy logowania systemu DBMS lub użytkowników, podczas uruchamiania systemu lokalną i tych maszyn wirtualnych powinny zostać wdrożony w scenariuszach tylko w chmurze, użytkownicy domeny trzeba usunąć. Należy się upewnić, że administrator lokalny oraz innego użytkownika lokalnego maszyny Wirtualnej jest dodawany jako logowania/użytkownika do systemu DBMS jako administratorzy.
* Dodać inne konta lokalnego, jak te mogą być wymagane dla scenariusza wdrażania.

- - -
> ![Windows][Logo_Windows] Windows
>
> W tym scenariuszu nie Generalizacja (sysprep) maszyny wirtualnej jest wymagany, aby przekazać i wdrożyć maszynę Wirtualną na platformie Azure.
> Upewnij się, tego dysku D:\ nie jest używana.
> Ustaw automatycznej instalacji dysków dołączonych dysków, zgodnie z opisem w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski] [ planning-guide-5.5.3] w tym dokumencie.
>
> ![Linux][Logo_Linux] Linux
>
> W tym scenariuszu nie Generalizacja (agenta waagent-deprovision) maszyny wirtualnej jest wymagana do Przekaż i wdróż maszynę Wirtualną na platformie Azure.
> Upewnij się, czy/mnt/zasobu nie jest używany i czy wszystkie dyski są instalowane za pomocą identyfikatora uuid. Dla dysku systemu operacyjnego upewnij się, że wpis programu inicjującego również odzwierciedla instalacji na podstawie identyfikatora uuid.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Przygotowanie do wdrożenia maszyny Wirtualnej z obrazu specyficzne dla programu SAP
Pliki VHD, które zawierają uogólniony systemu operacyjnego są przechowywane w kontenery na kontach magazynu Azure lub jako obrazy dysku zarządzanego. Można wdrożyć nową maszynę Wirtualną z takich obrazu za pomocą odwołań do obrazu wirtualnego dysku twardego lub dysk zarządzane jako źródło w plikach szablonu wdrażania, zgodnie z opisem w rozdziale [Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazu niestandardowego dla SAP] [ deployment-guide-3.3] z [Deployment Guide][deployment-guide].

Wymagania dotyczące podczas przygotowywania własny obraz maszyny Wirtualnej platformy Azure są:

* Pierwotnie dysku VHD zawierającego system operacyjny może mieć maksymalnie rozmiar wynosi 127GB tylko. To ograniczenie został wyeliminowany z końcem marca 2015 roku. Teraz dysku VHD zawierającego system operacyjny może być również inne usługi Azure Storage hostowanej wirtualnego dysku twardego o rozmiarze do 1TB.
* Musi on być stały format wirtualnego dysku twardego. Dynamicznych wirtualnych dysków twardych lub wirtualnych dysków twardych w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statycznych wirtualne dyski twarde podczas przekazywania wirtualnego dysku twardego z apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Wirtualne dyski twarde, które są zainstalowane na maszynie wirtualnej i powinien być instalowany ponownie na platformie Azure na potrzeby maszyny Wirtualnej można również stały format wirtualnego dysku twardego. Przeczytaj [w tym artykule (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) i [w tym artykule (system Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) limitów rozmiaru dysków danych. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statycznych wirtualne dyski twarde podczas przekazywania wirtualnego dysku twardego z apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Ponieważ wszyscy użytkownicy domeny zarejestrowaną jako użytkowników na maszynie wirtualnej, nie będzie istnieć w scenariuszu tylko w chmurze (zobacz rozdział [tylko w chmurze — wdrożenia maszyny wirtualnej na platformie Azure bez zależności w sieci lokalnej klienta] [ planning-guide-2.1] tego dokumentu), usługi przy użyciu takiej domeny konta mogą nie działać po wdrożeniu obrazu na platformie Azure. Jest to szczególnie ważne dla konta, które są używane do uruchamiania usług, takich jak aplikacje systemu DBMS lub SAP. W związku z tym należy zastąpić takich kont domeny kont lokalnych maszyn wirtualnych i usunąć lokalnych kont domeny na maszynie wirtualnej. Utrzymywanie lokalnych użytkowników domeny w obrazie maszyny Wirtualnej może nie być wystąpił problem podczas wdrażania maszyny Wirtualnej w scenariuszu obejmującym różne pomieszczenia zgodnie z opisem w rozdziale [między lokalizacjami - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure z wymaganiami jest w pełni zintegrowany z siecią lokalną] [ planning-guide-2.2] w tym dokumencie.
* Dodawanie innego lokalnego konta z uprawnieniami administratora, które mogą być używane przez pomocy technicznej firmy Microsoft w dochodzenia problem lub które można przypisać jako kontekst dla usług i aplikacji do uruchamiania w dopóki maszyna wirtualna jest wdrożona i bardziej odpowiednie użytkowników może zostać użyty.
* Tylko w chmurze wdrożeń i gdzie kont domeny były używane jako nazwy logowania systemu DBMS lub użytkowników, podczas uruchamiania systemu lokalnego należy je usunąć użytkownicy domeny. Należy się upewnić, że administrator lokalny oraz innego użytkownika lokalnego maszyny Wirtualnej został dodany jako logowania/użytkownika systemu DBMS jako administratorzy.
* Dodać inne konta lokalnego, jak te mogą być wymagane dla scenariusza wdrażania.
* Jeśli obraz zawiera instalację programu SAP NetWeaver i zmianę nazwy hosta z oryginalnej nazwy w punkcie wdrożenia usługi Azure jest prawdopodobne, zalecane jest skopiowany najnowsze wersje oprogramowania SAP inicjowania obsługi administracyjnej DVD Menedżera do szablonu. Umożliwi to łatwo korzystać z funkcji zmiany nazwy SAP dostarczone do dostosowania zmienione nazwy hosta i/lub zmiany identyfikatora SID systemu SAP we wdrożonym obrazie maszyny Wirtualnej, jak najszybciej po uruchomieniu nowej kopii.

- - -
> ![Windows][Logo_Windows] Windows
>
> Upewnij się, że dysku D:\ nie jest używany zestaw automatycznej instalacji z dysku dołączonych dysków, zgodnie z opisem w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski] [ planning-guide-5.5.3] w tym dokumencie.
>
> ![Linux][Logo_Linux] Linux
>
> Upewnij się, czy/mnt/zasobu nie jest używany i czy wszystkie dyski są instalowane za pomocą identyfikatora uuid. Dla dysku systemu operacyjnego upewnij się, że wpis programu inicjującego również odzwierciedla instalacji na podstawie identyfikatora uuid.
>
>

- - -
* SAP graficznego interfejsu użytkownika (dla administracyjne i ustawienia celów) można zainstalować wstępnie takie szablonu.
* Tak długo, jak to oprogramowanie może współpracować z Zmień nazwę maszyny wirtualnej można zainstalować inne oprogramowanie niezbędne do uruchomienia maszyny wirtualnej pomyślnie w scenariuszach między lokalizacjami.

Jeśli maszyna wirtualna jest gotowa wystarczająco być rodzajowa i ostatecznie niezależnie od kont użytkowników/nie jest dostępna w scenariuszu docelowe wdrożenie usługi Azure, odbywa się ostatni krok przygotowania uogólnianie taki obraz.

##### <a name="generalizing-a-vm"></a>Uogólnianie maszyny Wirtualnej
- - -
> ![Windows][Logo_Windows] Windows
>
> Ostatni etap polega na logowanie się do maszyny Wirtualnej przy użyciu konta administratora. Otwórz okno poleceń programu Windows jako *administratora*. Przejdź do %windir%\windows\system32\sysprep i wykonać sysprep.exe.
> Zostanie wyświetlone okno mała. Ważne jest, aby sprawdzić **Generalize** opcja (wartość domyślna to wyczyszczone) i zmień opcję zamknięcia domyślne "Ponowne uruchomienie", "Zamknij". W tej procedurze założono, że proces programu sysprep jest wykonywane lokalnie w systemie operacyjnym gościa maszyny wirtualnej.
> Jeśli chcesz wykonać procedurę z maszyny Wirtualnej już uruchomione na platformie Azure, wykonaj czynności opisane w [w tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Przechwytywanie maszyny wirtualnej systemu Linux, aby pełnić rolę szablonu usługi Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Przenoszenie maszyny wirtualne i wirtualne dyski twarde między lokalnymi na platformie Azure
Ponieważ przekazywanie obrazów maszyn wirtualnych i dysków Azure nie jest możliwe za pośrednictwem portalu Azure, należy użyć polecenia cmdlet programu Azure PowerShell lub interfejsu wiersza polecenia. Inną możliwością jest użycie narzędzia "Narzędzie AzCopy". Narzędzie można skopiować wirtualne dyski twarde między lokalną i platformą Azure (w obu kierunkach). On również skopiować wirtualne dyski twarde między regiony platformy Azure. Przejrzyj [tej dokumentacji] [ storage-use-azcopy] do pobrania i użycia programu AzCopy.

Trzeci alternatywą jest za pomocą różnych narzędzi zorientowane na graficznego interfejsu użytkownika innych firm. Jednakże upewnij się, że te narzędzia są obsługiwane Azure stronicowych obiektów blob. Dla naszych celów należy użyć magazynu obiektów Blob platformy Azure strony (w tym miejscu opisano różnice: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Bardzo wydajny w kompresowania maszyny wirtualne i wirtualne dyski twarde, które należy przekazać są także narzędzi dostarczanych przez platformę Azure. Jest to ważne, ponieważ takie zwiększenie efektywności kompresja skraca czas przekazywania (która zależy od mimo to łącze przekazywania do Internetu z lokalnymi, jak i regionu Azure wdrożenia docelowe). Jest odpowiedni założenie, że przekazywanie maszyny Wirtualnej lub wirtualnego dysku twardego z Europejskiego lokalizacji amerykańskiej danych Azure, które centrów będzie trwać dłużej niż przekazywania tej samej maszyny wirtualne/wirtualne dyski twarde Europejskiego Azure w centrach danych.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Przekazywanie wirtualnego dysku twardego z lokalnej na platformie Azure
Można przekazać istniejącej maszyny Wirtualnej lub wirtualnego dysku twardego z sieci lokalnej maszyny Wirtualnej lub wirtualnego dysku twardego musi spełniać wymagania wymienione w rozdziale [przygotowania do przenoszenia maszyny Wirtualnej z lokalnej na platformie Azure przy użyciu dysku z systemem innym niż uogólniony] [ planning-guide-5.2.1] tego dokumentu.

Maszyna wirtualna nie musi być uogólniony i mogą być przekazywane w stan i kształt, który ma po zamknięciu po stronie lokalnymi. Dotyczy to także dodatkowe wirtualne dyski twarde, które nie mogą zawierać żadnego systemu operacyjnego.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Przekazywanie wirtualnego dysku twardego i co dysku platformy Azure
W takim przypadku chcemy przekazania dysku VHD, lub bez systemu operacyjnego i zainstalować go na Maszynę wirtualną jako dysk danych lub użyj go jako dysk systemu operacyjnego. Jest to proces wieloetapowych

**PowerShell**

* Zaloguj się do subskrypcji z *Connect-AzureRmAccount*
* Ustaw subskrypcji kontekstu za pomocą *Set-AzureRmContext* i parametr identyfikator subskrypcji lub Nazwa subskrypcji — zobacz <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Przekazywanie wirtualnego dysku twardego z *AzureRmVhd Dodaj* do konta magazynu platformy Azure — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Opcjonalnie) Tworzenie dysku zarządzanego z wirtualnego dysku twardego z *New AzureRmDisk* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Ustaw dla dysku systemu operacyjnego nowa konfiguracja maszyny Wirtualnej do wirtualnego dysku twardego lub dysk zarządzane z *AzureRmVMOSDisk zestaw* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Utwórz nową maszynę Wirtualną na podstawie konfiguracji maszyny Wirtualnej z *New AzureRmVM* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Dodaj dysk danych do nowej maszyny Wirtualnej z *AzureRmVMDataDisk Dodaj* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Interfejs wiersza polecenia platformy Azure 2.0**

* Zaloguj się do subskrypcji z *az logowania*
* Wybierz subskrypcję z *skonfigurowane konto az — subskrypcji `<subscription name or id`>*
* Przekazywanie wirtualnego dysku twardego z *az magazynu obiektów blob przekazywania* — zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage][storage-azure-cli]
* (Opcjonalnie) Tworzenie dysku zarządzanego z wirtualnego dysku twardego z *Tworzenie dysku az* — zobacz https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Tworzenie nowej maszyny Wirtualnej, określając przekazane wirtualnego dysku twardego lub dysk zarządzane jako dysk systemu operacyjnego z *tworzenia maszyny wirtualnej az* i parametru *--attach-os-disk*
* Dodaj dysk danych do nowej maszyny Wirtualnej z *dołączyć dysku maszyny wirtualnej az* i parametru *— nowy*

**Szablon**

* Przekazywanie wirtualnego dysku twardego z interfejsu wiersza polecenia programu Powershell lub Microsoft Azure
* (Opcjonalnie) Tworzenie dysku zarządzanego z wirtualnego dysku twardego za pomocą programu Powershell, interfejsu wiersza polecenia Azure lub portalu Azure
* Wdróż maszynę Wirtualną z szablonu JSON odwołujące się do wirtualnego dysku twardego, jak pokazano w [tego szablonu JSON przykład](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json) lub za pomocą dysków zarządzanych, jak pokazano w [tego szablonu JSON przykład](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-disk-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Wdrożenie obrazu maszyny Wirtualnej
Można przekazać istniejącej maszyny Wirtualnej lub wirtualnego dysku twardego z sieci lokalnej, aby mógł zostać użyty jako obraz maszyny Wirtualnej Azure maszyny Wirtualnej lub wirtualnego dysku twardego muszą spełniać wymagania wymienione w rozdziale [przygotowania do wdrożenia maszyny Wirtualnej z obrazu specyficzne dla programu SAP] [ planning-guide-5.2.2] tego dokumentu.

* Użyj *sysprep* w systemie Windows lub *agenta waagent-deprovision* w systemie Linux w celu uogólnienia maszyny Wirtualnej — zobacz [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla systemu Windows lub [Przechwytywanie maszyny wirtualnej systemu Linux, aby pełnić rolę szablonu usługi Resource Manager] [ capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Zaloguj się do subskrypcji z *Connect-AzureRmAccount*
* Ustaw subskrypcji kontekstu za pomocą *Set-AzureRmContext* i parametr identyfikator subskrypcji lub Nazwa subskrypcji — zobacz <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Przekazywanie wirtualnego dysku twardego z *AzureRmVhd Dodaj* do konta magazynu platformy Azure — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego z wirtualnego dysku twardego z *New AzureRmImage* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Ustawić dla nowej konfiguracji maszyny Wirtualnej do dysku systemu operacyjnego
  * Wirtualny dysk twardy z *Set AzureRmVMOSDisk - SourceImageUri fromImage - CreateOption* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Zarządzane obrazu dysku *AzureRmVMSourceImage zestaw* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Utwórz nową maszynę Wirtualną na podstawie konfiguracji maszyny Wirtualnej z *New AzureRmVM* — zobacz <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Interfejs wiersza polecenia platformy Azure 2.0**

* Użyj *sysprep* w systemie Windows lub *agenta waagent-deprovision* w systemie Linux w celu uogólnienia maszyny Wirtualnej — zobacz [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla systemu Windows lub [Przechwytywanie maszyny wirtualnej systemu Linux, aby pełnić rolę szablonu usługi Resource Manager] [ capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Zaloguj się do subskrypcji z *az logowania*
* Wybierz subskrypcję z *skonfigurowane konto az — subskrypcji `<subscription name or id`>*
* Przekazywanie wirtualnego dysku twardego z *az magazynu obiektów blob przekazywania* — zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage][storage-azure-cli]
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego z wirtualnego dysku twardego z *tworzenia obrazu az* — zobacz https://docs.microsoft.com/cli/azure/image#az_image_create
* Tworzenie nowej maszyny Wirtualnej, określając przekazane wirtualnego dysku twardego lub obrazu dysku twardego zarządzanego jako dysk systemu operacyjnego z *tworzenia maszyny wirtualnej az* i parametru *--obrazu*

**Szablon**

* Użyj *sysprep* w systemie Windows lub *agenta waagent-deprovision* w systemie Linux w celu uogólnienia maszyny Wirtualnej — zobacz [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla systemu Windows lub [Przechwytywanie maszyny wirtualnej systemu Linux, aby pełnić rolę szablonu usługi Resource Manager] [ capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Przekazywanie wirtualnego dysku twardego z interfejsu wiersza polecenia programu Powershell lub Microsoft Azure
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego z wirtualnego dysku twardego za pomocą programu Powershell, interfejsu wiersza polecenia Azure lub portalu Azure
* Wdróż maszynę Wirtualną z szablonu JSON odwołujące się do obrazu wirtualnego dysku twardego, jak pokazano w [tego szablonu JSON przykład](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-image/azuredeploy.json) lub przy użyciu obrazu dysku zarządzanych, jak pokazano w [tego szablonu JSON przykład](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Pobieranie dysków VHD lub dysków zarządzanych do lokalnego
Azure infrastruktura jako usługa nie jest jednokierunkowa ulicy o tylko możliwość przekazania wirtualne dyski twarde i SAP systemów. Można przenieść SAP systemów z platformy Azure z powrotem do na świecie lokalnych.

Podczas pobierania dysków VHD lub dysków zarządzanych nie może być aktywne. Nawet w przypadku pobierania dysków, które są instalowane na maszynach wirtualnych, maszyny Wirtualnej musi zostać zamknięta i alokację. Jeśli chcesz pobrać zawartość bazy danych które następnie powinien służyć do konfigurowania nowego systemu lokalnego i jest dopuszczalny podczas czas pobierania i instalacji nowego systemu systemu na platformie Azure można nadal operacyjnej można unikanie długie przestoje, wykonując kopię zapasową skompresowanej bazy danych na dysk i po prostu pobrać tego dysku, a także pobieranie podstawowej maszyny Wirtualnej systemu operacyjnego.

#### <a name="powershell"></a>PowerShell

  * Pobieranie z dyskiem zarządzanym  
  Należy najpierw uzyskać dostęp do źródłowego obiektu blob dysku zarządzanego. Następnie można skopiować źródłowego obiektu blob do nowego konta magazynu i pobieranie obiektu blob z tego konta magazynu.

  ```powershell
  $access = Grant-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzureRmStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzureStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzureStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzureStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzureRmVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

  * Pobieranie wirtualnego dysku twardego  
  Po zatrzymaniu systemu SAP i maszyna wirtualna jest zamknięta, służy polecenie cmdlet programu PowerShell AzureRmVhd Zapisz w celu lokalnego podczas pobierania dysków VHD na świecie lokalnych. Aby to zrobić, należy wirtualnego dysku twardego, który można znaleźć w "magazynu sekcji" adres URL portalu Azure (trzeba przejdź do konta magazynu i kontener magazynu, w której został utworzony dysk VHD), a trzeba wiedzieć, gdzie dysku VHD powinien zostać skopiowany do.

  Następnie można wykorzystać polecenie definiując po prostu parametru SourceUri jako adres URL dysku VHD do pobrania i LocalFilePath jak fizyczną lokalizację wirtualnego dysku twardego (w tym swojej nazwy). Polecenie może wyglądać podobnie do:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Więcej szczegółów Zapisz AzureRmVhd polecenia cmdlet, sprawdź, czy w tym miejscu <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
  * Pobieranie z dyskiem zarządzanym  
  Należy najpierw uzyskać dostęp do źródłowego obiektu blob dysku zarządzanego. Następnie można skopiować źródłowego obiektu blob do nowego konta magazynu i pobieranie obiektu blob z tego konta magazynu.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Pobieranie wirtualnego dysku twardego   
  Po zatrzymaniu systemu SAP i maszyna wirtualna jest zamknięta, możesz użyć polecenia interfejsu wiersza polecenia Azure _pobieranie obiektu blob magazynu azure_ w celu lokalnego do pobrania dysku VHD dysków z powrotem na świecie lokalnych. Aby można było należy, nazwę i kontener wirtualnego dysku twardego, które można copi Znajdź w "magazynu sekcji" portalu Azure (trzeba przejdź do konta magazynu i kontener magazynu, w której został utworzony dysk VHD) i trzeba wiedzieć, gdzie powinien być wirtualnego dysku twardego ED do.

  Następnie można wykorzystać polecenie definiując po prostu parametry obiektów blob i kontener wirtualnego dysku twardego do pobierania i docelowy jako lokalizacja docelowa fizycznych wirtualnego dysku twardego (w tym swojej nazwy). Polecenie może wyglądać podobnie do:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Przenoszenie maszyn wirtualnych i dyskami w systemie Azure
#### <a name="copying-sap-systems-within-azure"></a>Kopiowanie systemów SAP w obrębie platformy Azure
Systemu SAP lub nawet DBMS serwera dedykowanego obsługi warstwy aplikacji SAP prawdopodobnie będą obejmować kilka dysków, które zawierać systemu operacyjnego przy użyciu plików binarnych lub plików danych i dziennika bazy danych SAP. Azure funkcji kopiowania dysków z użyciem ani Azure funkcji zapisywania dysków na dysk lokalny nie posiada mechanizm synchronizacji, który mógłby migawki synchronicznie wiele dysków. W związku z tym stan dysków skopiowany lub zapisany nawet wtedy, gdy te są zainstalowane na tej samej maszyny Wirtualnej będzie inny. Oznacza to, że w przypadku konkretnych o różnych danych i logfile(s) zawarte w innych dyskach, bazy danych w końcu byłoby niezgodne.

**Wniosek: Aby można było skopiować lub zapisać dysków, które są częścią konfiguracji systemu SAP należy zatrzymać systemu SAP i konieczność zamykania wdrożonej maszyny Wirtualnej. Następnie możesz skopiować lub pobrać zestaw dysków, aby utworzyć kopię systemu SAP w Azure lub lokalnie.**

Dyski danych mogą być przechowywane jako pliki wirtualnego dysku twardego na koncie magazynu Azure i może być podłączona bezpośrednio do maszyny wirtualnej lub zostać użyty jako obraz. W takim przypadku wirtualnego dysku twardego jest skopiowany do innej lokalizacji przed dołączony do maszyny wirtualnej. Pełna nazwa pliku VHD na platformie Azure, musi być unikatowa w obrębie platformy Azure. Jak wspomniano wcześniej już, nazwa jest rodzaj nazwy trzyczęściowej, która wygląda następująco:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Dyski danych może być także zarządzane dysków. W takim przypadku zarządzane dysk jest używany do tworzenia nowego dysku zarządzanego przed dołączony do maszyny wirtualnej. Nazwa dysku zarządzanych muszą być unikatowe w grupie zasobów.

##### <a name="powershell"></a>PowerShell
Polecenia cmdlet programu Azure PowerShell umożliwiają kopiować dysku VHD, jak pokazano w [w tym artykule][storage-powershell-guide-full-copy-vhd]. Aby utworzyć nowy dysk zarządzany, jak pokazano w poniższym przykładzie należy użyć AzureRmDiskConfig nowy i AzureRmDisk nowy.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
Interfejsu wiersza polecenia Azure umożliwia skopiowanie wirtualnego dysku twardego, jak pokazano w [w tym artykule][storage-azure-cli-copy-blobs]. Aby utworzyć nowy dysk zarządzany, użyj *Tworzenie dysku az* jak pokazano w poniższym przykładzie.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Narzędzia do magazynu Azure
* <http://storageexplorer.com/>

Wersje Professional eksploratory usługi Azure Storage można znaleźć tutaj:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

Kopię samego dysku VHD w ramach konta magazynu jest procesu, który zajmuje tylko kilka sekund (podobnie jak na sprzęcie sieci SAN, tworzenie migawek z opóźnieniem kopiowania i skopiuj podczas zapisu). Po utworzeniu kopii pliku wirtualnego dysku twardego można dołączyć do maszyny wirtualnej lub użyć jej jako obraz do dołączyć kopii wirtualnego dysku twardego do maszyny wirtualnej.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzureRmVM

# attach a copy of the managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzureRmDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzureRmDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzureRmVM
```
##### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
```

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

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopiowanie dysków między kontami magazynu Azure
Nie można wykonać tego zadania w portalu Azure. Można użyć poleceń cmdlet programu Azure PowerShell, interfejsu wiersza polecenia Azure lub przeglądarki magazynu innych firm. Polecenia cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia można tworzyć i zarządzać obiektów blob, które obejmują możliwość asynchronicznie kopiowania obiektów blob na kontach magazynu, a w regionach w ramach subskrypcji platformy Azure.

##### <a name="powershell"></a>PowerShell
Można również skopiować wirtualne dyski twarde między subskrypcjami. Aby uzyskać więcej informacji, przeczytaj [w tym artykule][storage-powershell-guide-full-copy-vhd].

Podstawowy przepływ PS logiki polecenia cmdlet wygląda następująco:

* Tworzenie kontekstu konta magazynu **źródła** konta magazynu z *New AzureStorageContext* — zobacz <https://msdn.microsoft.com/library/dn806380.aspx>
* Tworzenie kontekstu konta magazynu **docelowej** konta magazynu z *New AzureStorageContext* — zobacz <https://msdn.microsoft.com/library/dn806380.aspx>
* Uruchom kopię z

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Sprawdź stan kopiowania w pętli z

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej, zgodnie z powyższym opisem.

Przykłady można znaleźć [w tym artykule][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0
* Uruchom kopię z

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Sprawdź stan, jeśli kopia w pętli z

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej, zgodnie z powyższym opisem.

Przykłady można znaleźć [w tym artykule][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Obsługa dysku
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Maszyna wirtualna lub dysk struktury wdrożenia SAP
W idealnym przypadku Obsługa struktury Maszynę wirtualną i skojarzone dyski powinny być bardzo proste. W przypadku instalacji lokalnych klientów opracowany struktury instalacji serwera na wiele sposobów.

* Jeden dysk podstawowy, który zawiera system operacyjny i wszystkich plików binarnych systemu DBMS i/lub SAP. Ponieważ marca 2015 ten dysk może być maksymalnie do 1TB, rozmiar zamiast wcześniejszych ograniczenia, które maksymalnie 127 GB.
* Plik bazy danych SAP dziennika jednego lub wielu dysków, które zawiera systemu DBMS i pliku dziennika systemu DBMS tymczasowego obszaru magazynu (Jeśli systemu DBMS obsługuje tę funkcję). Wymagania dotyczące IOPS dziennika bazy danych są wysokie, należy najpierw paskowych wiele dysków w celu osiągnięcia woluminu IOPS wymagane.
* Liczba dysków zawierających jeden lub dwa pliki bazy danych SAP bazy danych i również pliki dane tymczasowe DBMS (Jeśli systemu DBMS obsługuje tę funkcję).

![Odwołanie do konfiguracji IaaS maszyny Wirtualnej platformy Azure dla programu SAP][planning-guide-figure-1300]

[comment]: <> (MShermannd TODO opisania struktury systemu Linux  )

- - -
> ![Windows][Logo_Windows] Windows
>
> Z wielu klientów widzieliśmy konfiguracje where, na przykład SAP i bazami danych binarnych nie zostały zainstalowane na dysku c:\, w którym zainstalowano system operacyjny. Wystąpiły różne przyczyny to go, ale podczas próby wstecz katalogu głównego, zwykle czy dyski zostały małe i uaktualnień systemu operacyjnego potrzebne dodatkowe miejsce, 10 – 15 lat temu. Oba te warunki nie są stosowane te zbyt często już dni. Obecnie można mapować dysku c:\ na dyskach duże lub maszyn wirtualnych. Aby zachować wdrożeń proste w ich struktury, zaleca się następujące wzorcem wdrożenia SAP NetWeaver systemów na platformie Azure
>
> Plik stronicowania systemu operacyjnego Windows powinny znajdować się na dysku D: (nietrwałe dysku)
>
> ![Linux][Logo_Linux] Linux
>
> Umieść swapfile systemu Linux w katalogu/mnt/mnt/zasobu w systemie Linux, zgodnie z opisem w [w tym artykule][virtual-machines-linux-agent-user-guide]. Plik wymiany można skonfigurować w pliku konfiguracji /etc/waagent.conf agenta systemu Linux. Dodaj lub Zmień następujące ustawienia:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Aby aktywować zmiany, należy ponownie uruchomić agenta systemu Linux z

```
sudo service waagent restart
```

Przeczytaj uwagi SAP [1597355] uzyskać więcej informacji dotyczących zalecanych wymiany (MB) rozmiar pliku

- - -
Liczba dysków systemu DBMS pliki danych i typ magazynu Azure tych dysków znajdują się w powinien ustalić wymagania IOPS i opóźnienia wymagane. Dokładne przydziały są opisane w [w tym artykule (Linux)] [ virtual-machines-sizes-linux] i [w tym artykule (system Windows)][virtual-machines-sizes-windows].

Środowisko wdrożenia SAP w ciągu ostatnich 2 lat nauczanych nam niektórych — lekcje, które mogą być podsumowywane jako:

* IOPS ruch do plików danych nie jest zawsze taki sam od istniejących systemów klienta może mieć inaczej o rozmiarze pliki danych reprezentujący ich baz danych SAP. W związku z tym on Twoją lepiej używa konfiguracji RAID na wiele dysków można umieścić pliki danych, które jednostek LUN używać poza tymi. Znaleziono sytuacji, w szczególności z usługi Azure Standard Storage, gdzie szybkość IOPS osiągnęła przydział jednego dysku dla systemu DBMS dziennika transakcji. W takich scenariuszach zaleca się użycie magazyn w warstwie Premium lub też agregowanie wielu Standard Storage RAID dysków przy użyciu oprogramowania.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Wydajność najlepsze rozwiązania dotyczące programu SQL Server w maszynach wirtualnych platformy Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Należy skonfigurować oprogramowanie RAID w systemie Linux][virtual-machines-linux-configure-raid]
> * [Skonfiguruj LVM na Maszynę wirtualną systemu Linux na platformie Azure][virtual-machines-linux-configure-lvm]
> * [Azure magazynu kluczy tajnych i optymalizacje We/Wy systemu Linux](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Magazyn w warstwie Premium jest pokazywany znaczących lepszą wydajność, szczególnie w przypadku zapisów dziennika transakcji krytyczne. W przypadku scenariuszy SAP oczekiwanych dostarczania produkcji, takich jak wydajność zaleca Użyj serii maszyn wirtualnych, które mogą korzystać z usługi Azure Premium Storage.

Należy pamiętać, że dysk, który zawiera system operacyjny, i jak firma Microsoft zaleca plików binarnych programu SAP i bazy danych (podstawowa maszyna wirtualna), również nie jest już ograniczona do 127GB. Teraz może mieć maksymalnie 1TB w rozmiarze. Powinno to być za mało miejsca, aby zachować niezbędnego pliku, w tym, na przykład SAP partii z dziennikami zadań.

Więcej wskazówek i więcej szczegółów, w szczególności DBMS maszyn wirtualnych, należy zapoznać się [DBMS Deployment Guide][dbms-guide]

#### <a name="disk-handling"></a>Obsługa dysku
W większości przypadków należy utworzyć dodatkowe dyski w celu wdrażania bazy danych SAP do maszyny Wirtualnej. Wspomnieliśmy o kwestiach związanych z liczby dysków w rozdziale [maszyna wirtualna lub dysk struktury wdrożenia SAP] [ planning-guide-5.5.1] tego dokumentu. Azure portal umożliwia dołączanie lub odłączanie dysków, po wdrożeniu podstawowej maszyny Wirtualnej. Dyski może być dołączony/odłączony, gdy maszyna wirtualna jest uruchomiony i działa oraz gdy zostanie zatrzymana. Podczas podłączania na dysku, portalu Azure oferuje dołączyć pusty dysk lub istniejącego dysku, który w tym momencie nie jest dołączony do innej maszyny Wirtualnej.

**Uwaga**: dysków może zostać dołączona tyko do jednej maszyny Wirtualnej w danym momencie.

![Dołączanie / odłączanie dysków przy użyciu usługi Azure Standard Storage][planning-guide-figure-1400]

Podczas wdrażania nowej maszyny wirtualnej można zdecydować, czy mają być zarządzane dysków lub umieść dysków na kontach magazynu Azure. Jeśli chcesz użyć magazyn w warstwie Premium, zalecamy używanie dysków zarządzanych.

Następnie należy zdecydować, czy chcesz utworzyć nowe i pusty dysk lub czy chcesz wybrać istniejący dysk, który został wcześniej przekazany i musi być podłączona do maszyny Wirtualnej teraz.

**WAŻNE**: możesz **nie** mają być używane buforowanie hosta z usługi Azure Standard Storage. ŻADNA wartość domyślną, należy pozostawić preferencji hosta pamięci podręcznej. Usługa Azure Premium Storage należy włączyć buforowanie odczytu Jeśli cechy operacji We/Wy jest przede wszystkim do odczytu, takich jak typowy ruch we/wy dla plików danych bazy danych. Buforowanie nie jest zalecane w przypadku pliku dziennika transakcji bazy danych.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Jak dołączyć dysk danych w portalu Azure][virtual-machines-linux-attach-disk-portal]
>
> Jeśli są dołączone dyski, należy zalogować się do maszyny Wirtualnej, aby otworzyć Menedżera dysków systemu Windows. Jeśli nie włączono automatycznej instalacji, zgodnie z zaleceniami w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski][planning-guide-5.5.3], nowo dołączona woluminu należy podjąć online i została zainicjowana.
>
> ![Linux][Logo_Linux] Linux
>
> Jeśli są dołączone dyski, należy zalogować się do maszyny Wirtualnej i zainicjuj dyski, zgodnie z opisem w [w tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Jeśli nowy dysk jest pusty dysk, należy sformatować dysk, a także. Formatowanie, szczególnie w przypadku plików danych i dziennika systemu DBMS tego samego zalecenia, jak w przypadku wdrożenia bez systemu operacyjnego systemu DBMS zastosować.

Jak już wspomniano w rozdziale [Microsoft Azure maszyny wirtualnej koncepcji][planning-guide-3.2], konta usługi Azure Storage udostępnia nieskończone zasobów w postaci liczby operacji We/Wy woluminu, wolumin IOPS i danych. Zazwyczaj maszyn wirtualnych systemu DBMS najczęściej dotyczy to. Może być najlepiej użyć oddzielnego konta magazynu dla każdej maszyny Wirtualnej, jeśli masz kilka dużej liczby operacji We/Wy maszyn wirtualnych do wdrożenia, aby pozostać w limit wielkości konta magazynu Azure. W przeciwnym razie należy sprawdzić, jak można równoważyć tych maszyn wirtualnych między różnymi kontami magazynu bez naciśnięcie limit każdego pojedynczego konta magazynu. Więcej szczegółów, omówiono w [DBMS Deployment Guide][dbms-guide]. Ograniczenia te powinny również należy pamiętać czysty aplikacji SAP serwera maszyn wirtualnych lub innych maszyn wirtualnych, które po pewnym czasie może wymagać dodatkowych dysków VHD. Ograniczenia te nie są stosowane, jeśli używasz dysku zarządzanego. Jeśli planujesz użyć magazyn w warstwie Premium, zalecamy użycie dysku zarządzanego.

Innym temacie, który ma zastosowanie w przypadku kont magazynu to, czy wirtualne dyski twarde na koncie magazynu jest pobierany z replikacją geograficzną. Replikacja geograficzna jest włączona lub wyłączona na poziomie konta magazynu, a nie na poziomie maszyny Wirtualnej. Jeśli jest włączona replikacja geograficzna, wirtualne dyski twarde w ramach konta magazynu będą replikowane do innego centrum danych Azure, w tym samym regionie. Przed podjęciem decyzji o tym, możesz pomyśleć o następujące ograniczenia:

Azure — replikacja geograficzna działa lokalnie na każdym dysku VHD na maszynie wirtualnej i nie jest replikowany z systemem IOs w kolejności chronologicznej w wielu dysków VHD na maszynie wirtualnej. W związku z tym wirtualnego dysku twardego, który reprezentuje podstawowej maszyny Wirtualnej, a także wszelkie dodatkowe wirtualne dyski twarde dołączonych do maszyny Wirtualnej są replikowane od siebie niezależne. Oznacza to, że nie ma synchronizacji między zmianami w różnych dysków VHD. Fakt, że IOs zostały zreplikowane niezależnie od kolejności, w którym są zapisywane oznacza tego replikacja geograficzna nie jest wartość dla serwerów baz danych, które mają swoje bazy danych rozproszone na wielu dysków VHD. Oprócz bazami danych również mogą istnieć inne aplikacje, gdzie procesy zapisu lub modyfikowania różnych wirtualne dyski twarde i gdzie ważne jest zachować kolejność zmian danych. Jeśli jest to wymagane, nie można włączyć replikację geograficzną na platformie Azure. Zależne muszą czy mają — replikacja geograficzna zestaw maszyn wirtualnych, ale nie dla innego zestawu, można już przydzielić maszyn wirtualnych i ich powiązane wirtualne dyski twarde do różnych kont magazynu mające replikacja geograficzna włączone lub wyłączone.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Ustawienie automatycznej instalacji dołączonych dysków
- - -
> ![Windows][Logo_Windows] Windows
>
> Dla maszyn wirtualnych, które są tworzone na podstawie własnych obrazów lub dyski należy do sprawdzenia i prawdopodobnie ustaw dla parametru automatycznej instalacji. Ustawienie tego parametru umożliwi maszyny Wirtualnej po ponownym uruchomieniu lub ponownego wdrażania na platformie Azure, należy zainstalować ponownie automatycznie dołączony zainstalowanych dysków.
> Parametr ma wartość obrazów obsługiwane przez firmę Microsoft w witrynie Azure Marketplace.
>
> Aby ustawić automatycznej instalacji, sprawdź dokumentację tutaj wiersza polecenia diskpart.exe pliku wykonywalnego:
>
> * [Opcje wiersza polecenia DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatycznej instalacji](http://technet.microsoft.com/library/cc753703.aspx)
>
> Powinny zostać otwarte okno wiersza polecenia systemu Windows jako administrator.
>
> Jeśli są dołączone dyski, należy zalogować się do maszyny Wirtualnej, aby otworzyć Menedżera dysków systemu Windows. Jeśli nie włączono automatycznej instalacji, zgodnie z zaleceniami w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski][planning-guide-5.5.3], nowo dołączona woluminu > należy podjąć w trybie online i została zainicjowana.
>
> ![Linux][Logo_Linux] Linux
>
> Należy zainicjować nowo dołączona pusty dysk, zgodnie z opisem w [w tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Należy również dodawanie nowych dysków do /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Końcowe wdrożenia
Dla wdrożenia końcowego i kolejnych kroków, szczególnie w odniesieniu do wdrożenia SAP rozszerzone monitorowanie, zapoznaj się [Deployment Guide][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Uzyskiwanie dostępu do systemów SAP uruchomionych w maszynach wirtualnych platformy Azure
W przypadku scenariuszy tylko w chmurze można nawiązać połączenia z tymi systemami SAP publicznego Internetu, przy użyciu graficznego interfejsu użytkownika programu SAP. W tych przypadkach należy zastosować następujące procedury.

W dalszej części dokumentu omówimy innych głównych scenariuszy łączących się z systemami SAP we wdrożeniach między różnymi lokalizacjami, których połączenia lokacja lokacja (tunel VPN) lub Azure ExpressRoute połączenia między lokalnymi i systemami Azure.

### <a name="remote-access-to-sap-systems"></a>Dostęp zdalny do systemów SAP
Za pomocą Menedżera zasobów Azure istnieją domyślne punkty końcowe nie już takie jak wcześniejsze klasycznego modelu. Wszystkie porty maszyny Wirtualnej platformy Azure ARM są otwarte tak długo, jak:

1. Żadna grupa zabezpieczeń sieci jest zdefiniowany dla podsieci lub interfejsu sieciowego. Ruch sieciowy na maszynach wirtualnych platformy Azure mogą być zabezpieczone za pomocą tak zwane "grup zabezpieczeń sieci". Aby uzyskać więcej informacji, zobacz [co to jest grupa zabezpieczeń sieci (NSG)?][virtual-networks-nsg]
2. Brak usługi równoważenia obciążenia Azure jest zdefiniowany dla interfejsu sieciowego   

Zobacz Architektura rozróżnia klasycznego modelu i ARM, zgodnie z opisem w [w tym artykule][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Konfiguracja połączenia systemu SAP i SAP graficznego interfejsu użytkownika dla scenariusza tylko w chmurze
Przeczytaj ten artykuł opisujący szczegóły, aby w tym temacie: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Zmiana ustawień zapory na maszynie wirtualnej
Może być konieczne skonfigurowanie zapory na maszynach wirtualnych, aby zezwalać na ruch przychodzący do systemu SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> Domyślnie Zapora systemu Windows w maszynie Wirtualnej platformy Azure wdrożonej jest włączona. Teraz muszą zezwalać na Port SAP do otwierania, w przeciwnym razie SAP graficznego interfejsu użytkownika nie będzie można nawiązać połączenia.
> W tym celu:
>
> * Otwórz panel sterowania\system i zabezpieczenia\zapora systemu Windows do **Zaawansowane ustawienia**.
> * Teraz kliknij prawym przyciskiem myszy na reguły ruchu przychodzącego i wybrać **nową regułę**.
> * W Kreatorze następujące wybrał opcję utworzenia nowej **portu** reguły.
> * W następnym kroku kreatora pozostaw ustawienie TCP i wpisz numer portu, który chcesz otworzyć. Ponieważ identyfikator wystąpienia naszych SAP 00, wybraliśmy 3200. Jeśli wystąpienie ma numer innego wystąpienia, powinien zostać otwarty port zdefiniowanych wcześniej według liczby wystąpień.
> * W następnej części kreatora, należy pozostawić element **Zezwalaj na połączenia** zaznaczone.
> * W następnym kroku kreatora należy określić, czy ta reguła ma zastosowanie do domeny, prywatne i publiczne sieci. Dostosuj go w razie potrzeby do własnych potrzeb. Jednak połączenie z graficznym interfejsem użytkownika SAP z zewnątrz za pośrednictwem sieci publicznej, musisz mieć reguły stosowane do sieci publicznej.
> * W ostatnim kroku kreatora, nazwę reguły i zapisać, naciskając **Zakończ**.
>
> Reguła zaczyna obowiązywać natychmiast.
>
> ![Definicja reguły portu][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Obrazy systemu Linux w portalu Azure Marketplace nie należy włączać zapory iptables domyślnie i połączenie z systemu SAP powinny działać. Jeśli włączono iptables lub inna zapora, zapoznaj się dokumentacją iptables lub używanych zapory zezwalająca na ruch przychodzący tcp na porcie 32xx (gdzie xx jest numer systemu systemu SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń
Graficzny interfejs użytkownika programu SAP nie połączenia bezpośrednio z wystąpień SAP (port 32xx), które są uruchomione, ale pierwszy raz łączy się za pośrednictwem portu otwarty do procesu serwera SAP komunikatu (port 36xx). W przeszłości bardzo tego samego portu był używany przez serwer komunikat do wewnętrznej komunikacji do wystąpienia aplikacji. Aby zapobiec lokalnych serwerów aplikacji z przypadkowo komunikację z serwerem wiadomości na platformie Azure, można zmienić portów komunikacji wewnętrznej. Zdecydowanie zaleca się zmieniać wewnętrznej komunikacji między serwerem SAP wiadomości i jego wystąpienia aplikacji na inny numer portu na komputerach został sklonowany z systemami lokalnymi, takimi jak Sklonowanie programowanie dla projektu testowego itp. Można to zrobić za pomocą parametru profil domyślny:

> rdisp/msserv_internal
>
>

zgodnie z opisem w [ustawienia zabezpieczeń dla serwera komunikat SAP ](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Pojęcia dotyczące tylko w chmurze wdrożenia SAP wystąpień
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Pojedyncze maszyny z programem SAP NetWeaver pokaz/szkolenia scenariusza
![Systemami jednej maszyny Wirtualnej SAP pokaz pod tą samą nazwą maszyny Wirtualnej, samodzielnie w usług Azure Cloud Services][planning-guide-figure-1700]

W tym scenariuszu (zobacz rozdział [tylko w chmurze] [ planning-guide-2.1] tego dokumentu) firma Microsoft wdrażania scenariusza systemu typowe szkolenia/pokaz gdzie scenariusz pełną szkolenia/pokaz znajduje się w jednej maszyny Wirtualnej. Przyjęto założenie, że wdrożenie jest przeprowadzane za pomocą szablonów obrazu maszyny Wirtualnej. Również przyjmie wiele z tych pokaz/szkolenia potrzebę maszyn wirtualnych można wdrożyć maszyn wirtualnych o tej samej nazwie.

Zakłada się utworzony obraz maszyny Wirtualnej, zgodnie z opisem w sekcjach rozdziału [przygotowywanie maszyn wirtualnych z programu SAP do platformy Azure] [ planning-guide-5.2] w tym dokumencie.

Kolejność zdarzeń do zaimplementowania scenariusza wygląda następująco:

##### <a name="powershell"></a>PowerShell
* Utwórz nową grupę zasobów dla każdego pozioma szkolenia/pokaz

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Utwórz nowe konto magazynu, jeśli nie chcesz używać dysków zarządzanych

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Utwórz nową sieć wirtualną dla pozioma co szkolenia/pokaz w taki sposób, aby umożliwić użycie tej samej nazwy hosta i adresu IP. Sieć wirtualna jest chroniona przez grupę zabezpieczeń sieci, umożliwiający tylko ruch skierowany do portu 3389, aby umożliwić dostęp do usług pulpitu zdalnego i port 22 protokołu SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Tworzenie nowego publicznego adresu IP, który umożliwia dostęp do maszyny wirtualnej z Internetu

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Tworzenie nowego interfejsu sieciowego dla maszyny wirtualnej

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Tworzy maszynę wirtualną. W scenariuszu tylko w chmurze co maszyna wirtualna ma taką samą nazwę. Identyfikator SID SAP wystąpienie SAP NetWeaver w tych maszyn wirtualnych będą takie same jak również. W ramach grupy zasobów platformy Azure musi być unikatowa nazwa maszyny Wirtualnej, ale w różnych grupach zasobów platformy Azure można uruchomić maszyny wirtualne o takiej samej nazwie. Domyślnego konta "Administrator" systemu Windows lub "główny" dla systemu Linux nie są prawidłowe. W związku z tym nową nazwę użytkownika administratora musi być zdefiniowany wraz z hasłem. Rozmiar maszyny wirtualnej musi być zdefiniowany.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcjonalnie dodaj dodatkowe dyski i przywrócić wymaganej zawartości. Należy pamiętać, że wszystkie nazwy obiektów blob (adresy URL do obiektów blob) muszą być unikatowe w obrębie platformy Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM

# Optional: Attach additional Managed Disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzureRmVM
```

##### <a name="cli"></a>Interfejs wiersza polecenia
Poniższy przykładowy kod może służyć w systemie Linux. Dla systemu Windows, albo użyj programu PowerShell opisanych powyżej lub dostosowania przykład rgName % zamiast $rgName i ustaw zmienną środowiskową za pomocą polecenia systemu Windows *ustawić*.

* Utwórz nową grupę zasobów dla każdego pozioma szkolenia/pokaz

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Tworzenie nowego konta magazynu

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Utwórz nową sieć wirtualną dla pozioma co szkolenia/pokaz w taki sposób, aby umożliwić użycie tej samej nazwy hosta i adresu IP. Sieć wirtualna jest chroniona przez grupę zabezpieczeń sieci, umożliwiający tylko ruch skierowany do portu 3389, aby umożliwić dostęp do usług pulpitu zdalnego i port 22 protokołu SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Tworzenie nowego publicznego adresu IP, który umożliwia dostęp do maszyny wirtualnej z Internetu

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Tworzenie nowego interfejsu sieciowego dla maszyny wirtualnej

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Tworzy maszynę wirtualną. W scenariuszu tylko w chmurze co maszyna wirtualna ma taką samą nazwę. Identyfikator SID SAP wystąpienie SAP NetWeaver w tych maszyn wirtualnych będą takie same jak również. W ramach grupy zasobów platformy Azure musi być unikatowa nazwa maszyny Wirtualnej, ale w różnych grupach zasobów platformy Azure można uruchomić maszyny wirtualne o takiej samej nazwie. Domyślnego konta "Administrator" systemu Windows lub "główny" dla systemu Linux nie są prawidłowe. W związku z tym nową nazwę użytkownika administratora musi być zdefiniowany wraz z hasłem. Rozmiar maszyny wirtualnej musi być zdefiniowany.

```
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

```
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

* Opcjonalnie dodaj dodatkowe dyski i przywrócić wymaganej zawartości. Należy pamiętać, że wszystkie nazwy obiektów blob (adresy URL do obiektów blob) muszą być unikatowe w obrębie platformy Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Szablon
Szablony przykładowe można użyć w repozytorium azure-Szybki Start — szablony w witrynie github.

* [Proste maszyny Wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Proste systemu Windows maszyny Wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Maszyny Wirtualnej z obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementuje zestaw maszyn wirtualnych, które muszą się w obrębie platformy Azure
Ten scenariusz tylko w chmurze jest typowy scenariusz w szkolenia i pokaz celów where oprogramowania reprezentujący pokaz/szkolenia scenariusz jest rozłożona na wiele maszyn wirtualnych. Różne składniki zainstalowane w różnych maszyn wirtualnych muszą komunikować się ze sobą. Ponownie w tym scenariuszu komunikacja sieciowa braku lokalnej lub scenariuszu obejmującym różne pomieszczenia jest wymagana.

Ten scenariusz jest rozszerzeniem instalacji w rozdziale opisano [jednej maszyny Wirtualnej z programem SAP NetWeaver pokaz/szkolenia scenariusza] [ planning-guide-7.1] tego dokumentu. W takim przypadku jedna maszyna wirtualna zostanie dodany do istniejącej grupy zasobów. W poniższym przykładzie pozioma szkolenia składa się z programu SAP ASCS/SCS maszynę Wirtualną, maszyny Wirtualnej z systemem, system DBMS i wystąpienia serwera aplikacji SAP maszyny Wirtualnej.

Przed utworzeniem tego scenariusza należy traktować jak już wykonywane w scenariuszu przed ustawienia podstawowe.

#### <a name="resource-group-and-virtual-machine-naming"></a>Grupy zasobów i nazwy maszyny wirtualnej
Wszystkie nazwy grupy zasobów musi być unikatowa. Tworzenie własnych schemat nazewnictwa zasobów, takich jak `<rg-name`>-sufiks.

Nazwa maszyny wirtualnej musi być unikatowa w ramach grupy zasobów.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurowanie sieci dla komunikacji między różnych maszyn wirtualnych
![Zestaw maszyn wirtualnych w ramach sieci wirtualnej platformy Azure][planning-guide-figure-1900]

Aby uniknąć kolizji z klony o tej samej krajobrazów szkolenia/pokaz nazw, należy utworzyć sieci wirtualnej platformy Azure dla każdego orientacji poziomej. Rozpoznawanie nazw DNS, które będą udostępniane przez usługi Azure lub skonfigurować własny serwer DNS poza Azure (nie w celu dalszego dokładniej omówione w tym miejscu). W tym scenariuszu firma Microsoft nie Konfiguruj własnej DNS. Dla wszystkich maszyn wirtualnych w jednej sieci wirtualnej platformy Azure komunikację za pomocą nazwy hostów zostaną włączone.

Możliwe przyczyny do oddzielania szkolenia lub pokaz krajobrazów sieci wirtualnych, a nie tylko grupy zasobów:

* Poziomo SAP, jak skonfigurować wymaga własnego AD/OpenLDAP oraz serwer domeny musi być częścią każdego krajobrazy.  
* Poziomo SAP ustanowionych ma składników, które muszą pracować z stałe adresy IP.

Więcej informacji o sieciach wirtualnych platformy Azure i sposobu definiowania ich znajdują się w [w tym artykule][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Wdrażanie SAP maszyn wirtualnych z połączeniem w sieci firmowej (między lokalizacjami)
Uruchom pozioma SAP i dzielenia wdrażania od zera dla serwerów systemu DBMS wysokiej jakości, lokalnych zwirtualizowanych środowisk dla warstwy aplikacji i mniejszych warstwy 2 skonfigurowane SAP systemów i IaaS platformy Azure. Podstawowe założenia jest systemów SAP w jednym pozioma SAP muszą komunikować się ze sobą i wiele innych składników oprogramowania wdrożone w firmie, niezależnie od ich formularza wdrożenia. Również powinno być żadnych różnic wprowadzone przez formularz wdrożenia dla użytkownika końcowego połączenie przy użyciu graficznego interfejsu użytkownika programu SAP lub innych interfejsów. Te warunki można spełnić tylko gdy mamy lokalnymi Active Directory/OpenLDAP i rozszerzone systemów Azure za pośrednictwem połączenia lokacja do witryny/kilku lokalizacji lub połączeń prywatnych, takich jak usługa Azure ExpressRoute usługi DNS.

Aby uzyskać więcej tła w szczegółach wdrożenia SAP na platformie Azure, firma Microsoft zachęca do odczytu rozdział [pojęcia Cloud-Only wdrożenia SAP wystąpień] [ planning-guide-7] tego dokumentu, który omówiono konstrukcji podstawy Azure oraz jak te używane aplikacje SAP Azure.

### <a name="scenario-of-an-sap-landscape"></a>Scenariusz pozioma SAP
W scenariuszu obejmującym różne pomieszczenia można około opisana jak grafiki poniżej:

![Połączenie lokacja-lokacja między lokalnych i dostępnych zasobów platformy Azure][planning-guide-figure-2100]

Scenariusz przedstawiony powyżej opisano scenariusz, gdzie lokalną AD/OpenLDAP i DNS zostały rozszerzone na platformie Azure. Na stronie lokalnymi określonego zakresu adresów IP jest zarezerwowany dla subskrypcji platformy Azure. Zakres adresów IP zostanie przypisana do sieci wirtualnej platformy Azure na stronie platformy Azure.

#### <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Minimalna wymagana wartość to użycia protokoły komunikacyjne takie jak SSL/TLS dla dostęp za pomocą przeglądarki lub opartych na sieci VPN połączenia dla dostępu do systemu do usług platformy Azure. Zakłada się firm różnie obsługiwać połączenia sieci VPN między siecią firmową i Azure. Niektóre firmy mogą otworzyć blankly wszystkie porty. Niektóre inne firmy może być bardzo dokładne należeć portów należy otworzyć itp.

W tabeli poniżej SAP typowe porty komunikacyjne są wyświetlane. Zasadniczo jest odpowiednia do otwarcia portu bramą SAP.

| Usługa | Nazwa portu | Przykład `<nn`> = 01 | Domyślny zakres (maksymalna liczba min) | Komentarz |
| --- | --- | --- | --- | --- |
| Dyspozytor |sapdp`<nn>` zobacz * |3201 |3200 - 3299 |Dyspozytor SAP, używany przez SAP graficznego interfejsu użytkownika dla systemu Windows i języka Java |
| Serwer komunikatów |sapms`<sid`> zobacz ** |3600 |bezpłatne sapms`<anySID`> |Identyfikator SID = Identyfikatorem systemu SAP |
| Brama |sapgw`<nn`> zobacz * |3301 |bezpłatna |Bramą SAP, używany do komunikacji CPIC i RFC |
| SAP router |sapdp99 |3299 |bezpłatna |Tylko nazwy usługi CI (centralnej wystąpienia) można przypisać ponownie w /etc/services dowolne wartości po zakończeniu instalacji. |

*) nn = liczby wystąpień programu SAP

*) identyfikatora sid = Identyfikatorem systemu SAP

Bardziej szczegółowe informacje dotyczące portów wymaganych przez różne produkty SAP lub usługi za pomocą produktów SAP można znaleźć tutaj <http://scn.sap.com/docs/DOC-17124>.
Z tego dokumentu można otworzyć porty dedykowanych w niezbędne dla określonych produktów SAP i scenariusze urządzenia sieci VPN.

Mierzy innych zabezpieczeń podczas wdrażania maszyn wirtualnych w takiej sytuacji można utworzyć [sieciowej grupy zabezpieczeń] [ virtual-networks-nsg] do definiowania reguł dostępu.

### <a name="dealing-with-different-virtual-machine-series"></a>Dotyczących różnych serii maszyny wirtualnej
W ciągu ostatnich 12 miesięcy firma Microsoft dodała wiele więcej typów maszyny Wirtualnej, które różnią się w liczba Vcpu, pamięci lub większe znaczenie na sprzęcie jest uruchomiona w. Nie wszystkie te maszyny wirtualne są obsługiwane z programu SAP (zobacz obsługiwane typy maszyny Wirtualnej w Uwaga SAP [1928533]). Uruchom niektóre z tych maszyn wirtualnych na inny host generacje sprzętu. W szczegółowości z Azure-jednostki skalowania wdrożono uzyskiwania tych generacje sprzętu hosta. Oznacza przypadkach mogą wystąpić podczas gdy różnych rozmiarów maszyn wirtualnych, którą wybrano nie można uruchomić w tej samej jednostce skali. Zestawu dostępności jest ograniczona możliwość span jednostki skalowania w zależności od sprzętu.  Na przykład jeśli chcesz uruchomić systemu DBMS A5 A11 maszyn wirtualnych i warstwy aplikacji SAP na maszynach wirtualnych serii G powodowałoby to wymuszenie do wdrożenia pojedynczego systemu SAP lub w innych systemach SAP w różnych zestawów dostępności.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Drukowania na drukarce lokalnej sieci z wystąpieniem SAP na platformie Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Drukowanie za pośrednictwem protokołu TCP/IP w scenariuszu obejmującym różne pomieszczenia
Ustawienie drukarek sieciowych TCP/IP, na podstawie lokalnych w maszynie Wirtualnej platformy Azure ogólnego jest taka sama jak w sieci firmowej, przy założeniu, że masz tunelu VPN lokacja-lokacja lub nawiązano połączenie ExpressRoute.

- - -
> ![Windows][Logo_Windows] Windows
>
> W tym celu:
>
> * Niektóre drukarki sieciowe dostarczane z Kreatora konfiguracji, który ułatwia konfigurowanie drukarki w maszynie Wirtualnej platformy Azure. Jeśli żadne oprogramowanie kreator został rozesłany drukarki, ręczne sposobem skonfigurowania drukarki jest utworzenie nowego portu drukarki TCP/IP.
> * Otwórz Panel sterowania -> urządzenia i drukarki -> Dodaj drukarkę
> * Wybierz polecenie Dodaj drukarki za pomocą adresu TCP/IP lub nazwy hosta
> * Wpisz adres IP drukarki
> * Port drukarki 9100 standardowe
> * W razie potrzeby ręcznie zainstaluj odpowiedni sterownik drukarki.
>
> ![Linux][Logo_Linux] Linux
>
> * jak dla systemu Windows po prostu wykonaj standardową procedurę, aby zainstalować drukarki sieciowej
> * Wykonaj publicznego przewodniki Linux dla [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) lub [Red Hat i Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) na temat sposobu dodawania drukarki.
>
>

- - -
![Drukowanie w sieci][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Drukarki oparta na hoście za pośrednictwem protokołu SMB (drukarki udostępnionej) w scenariuszu obejmującym różne pomieszczenia
Drukarki oparta na hoście nie są zgodne sieci zgodnie z projektem. Jednak drukarki oparta na hoście mogą być współużytkowane przez komputery w sieci, tak długo, jak drukarka jest podłączony do zasilania na komputerze. Połącz z firmowej sieci lokacja-lokacja lub ExpressRoute i udostępnianie drukarki lokalnej. Protokół SMB używa NetBIOS zamiast DNS jako nazwa usługi. Nazwa hosta NetBIOS może być inna niż nazwa hosta DNS. Standardowa przypadek jest nazwą hosta NetBIOS i nazwę hosta DNS są identyczne. Domena DNS nie ma sensu w obszarze nazw NetBIOS. W związku z tym w pełni kwalifikowana nazwa hosta DNS, nazwy hosta DNS i domeny DNS nie można używać w obszarze nazw NetBIOS.

Udziału drukarki jest identyfikowane przez nazwę unikatową w sieci:

* Nazwa hosta hostów protokołu SMB (zawsze wymagane).
* Nazwa udziału (zawsze wymagane).
* Nazwa domeny, jeśli udostępnianie drukarki nie jest w tej samej domenie co systemu SAP.
* Ponadto nazwa użytkownika i hasło może wymagać dostępu do udziału drukarki.

Instrukcje:

- - -
> ![Windows][Logo_Windows] Windows
>
> Udostępnianie drukarki lokalnej.
> W Maszynie wirtualnej Azure Otwórz Eksploratora Windows i wprowadź nazwę udziału drukarki.
> Kreator instalacji drukarki przeprowadzi Cię przez proces instalacji.
>
> ![Linux][Logo_Linux] Linux
>
> Oto kilka przykładów dokumentację dotyczącą konfigurowania drukarek sieciowych w systemie Linux lub w tym rozdziale dotyczące drukowania w systemie Linux. Będzie ona działają tak samo maszyny Wirtualnej systemu Linux Azure tak długo, jak maszyna wirtualna jest częścią sieci VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL lub Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Drukarki USB (drukarki przekazywania)
Na platformie Azure możliwości usług pulpitu zdalnego, aby zapewnić użytkownikom dostęp do swoich urządzeń drukarki lokalnej w sesji zdalnej nie jest dostępna.

- - -
> ![Windows][Logo_Windows] Windows
>
> Więcej informacji dotyczących drukowania w systemie Windows można znaleźć tutaj: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integracja programu SAP Azure systemów do transportu systemu (TMS) w między lokalizacjami i korekty
Zmiana SAP i System transportu (TMS) musi być skonfigurowana do eksportowania i importowania żądania transportu we wszystkich systemach w orientacji poziomej. Przyjęto założenie, że wystąpień rozwoju systemu SAP (deweloperów) znajdują się na platformie Azure jakości (QA) i systemów produkcyjnych (PRD) są lokalne. Ponadto przyjęto założenie, że istnieje transportu centralnego katalogu.

##### <a name="configuring-the-transport-domain"></a>Konfigurowanie domeny transportu
Konfigurowanie domeny transportu w systemie wyznaczone jako kontroler domeny transportu, zgodnie z opisem w [konfigurowania kontrolera domeny transportu](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Użytkownik systemu TMSADM zostanie utworzone i wymagane docelowego RFC zostanie wygenerowany. Można sprawdzić te połączenia RFC przy użyciu transakcji SM59. Rozpoznawanie nazwy hosta musi być włączony w Twojej domenie transportu.

Instrukcje:

* W naszym scenariuszu zdecydowaliśmy się, że system QAS lokalnej będzie CTS kontrolera domeny. Wywołanie STMS transakcji. Zostanie wyświetlone okno dialogowe TMS. Wyświetlane jest okno dialogowe Konfigurowanie domeny transportu. (To okno dialogowe pojawia się tylko, jeśli nie skonfigurowano jeszcze domeny transportu.)
* Upewnij się, że jest autoryzowane automatycznie utworzony użytkownik TMSADM (SM59 -> połączenia ABAP -> TMSADM@E61.DOMAIN_E61 -> Szczegóły -> Utilities(M) -> Test autoryzacji). Wstępnego ekranu transakcji STMS powinny wskazywać, że tego systemu SAP będzie działać jako kontroler domeny transportu w sposób pokazany poniżej:

![Ekran początkowy transakcji STMS na kontrolerze domeny][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>W tym systemów SAP w domenie transportu
Kolejność, w tym systemie SAP w domenie transportu wygląda następująco:

* W systemie deweloperów na platformie Azure przejdź do systemu transportu (klient 000) i wywołać STMS transakcji. Wybierz inne konfigurację z okna dialogowego i kontynuować obejmują systemu w domenie. Określ kontroler domeny jako hosta docelowego ([w tym systemów SAP w domenie transportu](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). System oczekuje się teraz do uwzględnienia w domenie transportu.
* Ze względów bezpieczeństwa należy następnie wróć do kontrolera domeny, aby potwierdzić żądanie. Wybierz Przegląd systemu i zatwierdź systemu oczekiwania. Potwierdź monit i konfiguracji będą przesyłane.

Ten system SAP zawiera teraz niezbędne informacje dotyczące wszystkich innych systemów SAP w domenie transportu. W tym samym czasie nowego systemu SAP dane są wysyłane do innych systemów SAP, a systemu SAP została wprowadzona w profilu transportu programu kontroli transportu. Sprawdź, czy działają dokumenty RFC i dostępu do katalogu transportu domeny.

Kontynuuj konfiguracji systemu transportu w zwykły sposób zgodnie z opisem w dokumentacji [zmianami i System transportu](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Instrukcje:

* Upewnij się, że Twoje STMS lokalnie jest poprawnie skonfigurowany.
* Upewnij się, że nazwa hosta kontrolera domeny transportu można rozwiązać przez maszynę wirtualną na visa Azure i na.
* Wywołanie transakcji STMS -> inne konfiguracji -> obejmują systemu w domenie.
* Upewnij się, połączenie w systemie TMS na lokalnym.
* Skonfiguruj trasy transportu, grup i warstw w zwykły sposób.

W lokacja lokacja połączenia między lokalizacjami scenariuszy, opóźnienia między lokalną i platformą Azure nadal może być istotne. Jeśli firma Microsoft wykonaj sekwencji transportu obiektów za pomocą programowanie i testowanie systemów w środowisku produkcyjnym lub pomyśleć o zastosowaniu transportów lub obsługuje pakietów różnych systemów, pamiętaj, że zależą od lokalizacji katalogu transport centralny, niektóre systemy o wystąpi duże opóźnienie odczytu lub zapisu danych w katalogu centralnym transportu. Sytuacja jest podobny do konfiguracji pozioma SAP gdzie różnych systemów rozprzestrzenia się w różnych centrach danych z znacznej odległość między centrami danych.

Aby obejść takie opóźnienia i systemów, pracy szybkiego podczas zapisu lub odczytu do lub z katalogu transportu, można skonfigurować dwie domeny transportu STMS (jeden dla lokalnych i jeden z systemów na platformie Azure i połącz domen transportu. Sprawdź, czy ta dokumentacja wyjaśniającego zasad za tę koncepcję w SAP TMS: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Instrukcje:

* Konfigurowanie domeny transportu w każdej lokalizacji (lokalne i Azure) przy użyciu transakcji STMS <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Link domen z łączem domeny i Potwierdź łącza między tymi dwiema domenami.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Przekazują konfigurację do połączonego systemu.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC ruchu między wystąpieniami programu SAP znajdujący się w usłudze Azure i lokalnego (między lokalizacjami)
RFC ruchu między systemami, które są lokalne i na platformie Azure musi działać. Aby skonfigurować połączenie transakcji wywołania SM59 w systemie źródłowym których należy zdefiniować połączenie RFC na komputerze docelowym. Konfiguracja jest podobne do ustawiania standard RFC połączenia.

Firma Microsoft zakładać, że w scenariuszu obejmującym różne pomieszczenia maszyn wirtualnych, które wykonywania systemów SAP, które muszą komunikować się ze sobą znajdują się w tej samej domenie. W związku z tym Instalator RFC połączenia między systemami SAP różnią się od ustawienia i dane wejściowe w scenariuszach lokalnych.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Podczas uzyskiwania dostępu do lokalnego fileshares z wystąpień SAP znajdującego się na platformie Azure lub na odwrót
SAP wystąpień znajdujących się na platformie Azure muszą uzyskać dostęp do udziałów plików, które znajdują się w siedzibie firmy. Ponadto lokalnego wystąpienia programu SAP chce korzystać z udziałów plików, które znajdują się na platformie Azure. Aby włączyć udziały plików należy skonfigurować uprawnienia i opcje udostępniania w systemie lokalnym. Upewnij się otworzyć porty dla połączenia sieci VPN lub usługi platformy Azure i centrum danych.

## <a name="supportability"></a>Możliwości obsługi
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure monitorowania rozwiązania dla programu SAP
Aby włączyć monitorowanie SAP znaczeniu krytycznym na platformie Azure SAP narzędzia SAPOSCOL lub Agent hosta SAP pobieranie danych hosta usługi maszyny wirtualnej platformy Azure za pośrednictwem rozszerzenia monitorowania Azure dla programu SAP do monitorowania. Ponieważ zapotrzebowanie przez SAP były bardzo specyficzne dla aplikacji SAP, Microsoft zrezygnowała objęty wdrożenie wymaganych funkcji na platformie Azure, ale pozostawić ją klientom wdrożenie niezbędne składniki monitorowania i konfiguracje maszyn wirtualnych działających na platformie Azure. Jednak wdrożenia i cyklu życia zarządzania monitorowania składników będzie przede wszystkim zautomatyzowany przez platformę Azure.

#### <a name="solution-design"></a>Projekt rozwiązania
Rozwiązanie opracowany, aby włączyć monitorowanie SAP jest oparta na architekturę Agent maszyny Wirtualnej i środowiska rozszerzenia. Agent maszyny Wirtualnej i rozszerzenia będzie Pozwól na instalację aplikacji oprogramowania dostępne w galerii Azure rozszerzenia maszyny Wirtualnej w maszynie Wirtualnej. Zasada ideą to pojęcie jest umożliwienie (w takich przypadkach rozszerzenie monitorowania Azure dla programu SAP), wdrożenie specjalne funkcje do maszyny Wirtualnej oraz konfiguracji oprogramowania w czasie wdrażania.

"Agent maszyny Wirtualnej" umożliwiającą obsługę określonych rozszerzeń maszyny Wirtualnej platformy Azure w ramach maszyny Wirtualnej jest dodane do maszyn wirtualnych systemu Windows domyślnie po utworzeniu maszyny Wirtualnej w portalu Azure. W przypadku SUSE Red Hat lub Oracle Linux agent maszyny Wirtualnej jest już częścią obrazu portalu Azure Marketplace. W przypadku, gdy jeden czy przekazać Maszynę wirtualną systemu Linux z lokalnej na platformie Azure, agent maszyny Wirtualnej musi być zainstalowany ręcznie.

Podstawowe bloki konstrukcyjne rozwiązania monitorowanie na platformie Azure dla programu SAP wygląda podobnie do następującej:

![Składniki Microsoft Azure rozszerzenia][planning-guide-figure-2400]

Jak pokazano na powyższym diagramie bloku, jednej części rozwiązanie monitorowania dla programu SAP znajduje się w obrazie maszyny Wirtualnej platformy Azure i galerii rozszerzeń Azure, który jest replikowany globalnie repozytorium, który jest zarządzany przez operacje Azure. Jest odpowiedzialny za wspólnego SAP/MS zespołu nad Azure wdrożenia SAP do pracy z operacjami Azure, aby opublikować nową wersję rozszerzenia monitorowania Azure dla programu SAP.

Podczas wdrażania nowej maszyny Wirtualnej systemu Windows, Agent maszyny Wirtualnej Azure jest automatycznie dodawany do maszyny Wirtualnej. Funkcja tego agenta jest do koordynowania ładowania i konfiguracja rozszerzeń Azure do monitorowania systemów SAP NetWeaver. Dla maszyn wirtualnych systemu Linux Agent maszyny Wirtualnej Azure jest już częścią obrazu systemu operacyjnego programu Azure Marketplace.

Istnieje jednak krok, który musi być wykonywane przez klienta. Jest to aktywacji i konfiguracji zbierania wydajności. Proces powiązanych z konfiguracją jest zautomatyzowany skrypt programu PowerShell lub polecenia interfejsu wiersza polecenia. Skrypt programu PowerShell można pobrać w Microsoft Azure Script Center zgodnie z opisem w [Deployment Guide][deployment-guide].

Ogólna architektura Azure rozwiązanie monitorowania dla programu SAP wygląda następująco:

![Monitorowanie rozwiązania dla programu SAP NetWeaver Azure][planning-guide-figure-2500]

**Dokładne instrukcje i szczegółowy opis kroków przy użyciu tych poleceń cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia, podczas wdrażania, postępuj zgodnie z instrukcjami [Deployment Guide][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integracja Azure znajduje się wystąpienie SAP do SAProuter
Wystąpień SAP działające na platformie Azure konieczne jest również dostępny z SAProuter.

![Połączenie routera SAP][planning-guide-figure-2600]

SAProuter umożliwia komunikację TCP/IP między systemów uczestniczących w przypadku braku bezpośredniego połączenia IP. Dzięki temu można korzystać, że żadne połączenie na trasie między partnerami komunikacja nie jest konieczne na poziomie sieci. SAProuter nasłuchuje na porcie 3299 domyślnie.
Nawiązywanie połączeń za pomocą SAProuter wystąpień SAP należy zapewnić SAProuter ciągu i nazwy hosta z próby nawiązania połączenia.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Do tej pory fokus dokument został SAP NetWeaver ogólnie lub SAP NetWeaver ABAP stosu. W tej sekcji małych szczególne zagadnienia dotyczące stosu SAP Java są wyświetlane. Jedną z najważniejszych aplikacji SAP NetWeaver Java wyłącznie na podstawie jest SAP Enterprise Portal. Inne SAP NetWeaver aplikacji opartych na jak SAP PI i Menedżera rozwiązania SAP SAP NetWeaver ABAP i stosy Java. W związku z tym Oczywiście istnieje potrzeba wziąć pod uwagę zagadnień związanych z na stosie SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal
Ustawienia portalu SAP w maszynie wirtualnej platformy Azure różnią się od w instalacji lokalnej, jeśli są wdrażane w scenariuszach między lokalizacjami. Ponieważ DNS jest realizowane przez lokalną, ustawienia portu pojedynczych wystąpień może odbywać się jako skonfigurowane lokalnymi. Zalecenia i ograniczenia opisane w tym dokumencie dotąd się ogólnie dla aplikacji, takie jak SAP Enterprise Portal lub stos SAP NetWeaver Java.

![Portal narażonych SAP][planning-guide-figure-2700]

Scenariusza wdrażania specjalnych niektórych klientów jest bezpośredniego ujawnienia SAP Enterprise Portal do Internetu, gdy host maszyny wirtualnej jest połączony z siecią firmową za pośrednictwem tunelu VPN lokacja lokacja lub ExpressRoute. Takiej sytuacji należy upewnić się, że określone porty są otwarte i nie jest blokowany przez zaporę lub w sieci grupy zabezpieczeń. Tego samego sposobu należy zastosować, jeśli chcesz połączyć się z wystąpieniem SAP Java z lokalnymi w scenariuszu tylko w chmurze.

Portal początkowy identyfikator URI jest http (s):`<Portalserver`>: 5XX00/irj, gdy port jest tworzony przez 50000 plus (Systemnumber? 100). Domyślny system identyfikatora URI SAP portalu 00 to `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. Aby uzyskać więcej informacji, należy mieć przyjrzeć się <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Konfiguracja punktu końcowego][planning-guide-figure-2800]

Jeśli chcesz dostosować adresu URL i/lub porty programu SAP Enterprise Portal, sprawdź, czy w tej dokumentacji:

* [Zmień adres URL portalu](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Zmienić domyślne numery portów, numery portów portalu](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Wysokiej dostępności i odzyskiwania awaryjnego (DR) dla programu SAP NetWeaver uruchomione na maszynach wirtualnych platformy Azure
### <a name="definition-of-terminologies"></a>Definicja terminologii
Termin **wysokiej dostępności (HA)** powiązany zazwyczaj jest to zestaw technologii minimalizujące zakłóceń IT, zapewniając ciągłość prowadzenia działalności biznesowej usługi IT za pośrednictwem nadmiarowe, odpornej na uszkodzenia lub pracy awaryjnej chronione składniki wewnątrz **tego samego** centrum danych. W tym przypadku w ramach jednego regionu Azure.

**Odzyskiwania awaryjnego (DR)** również jest docelowo zminimalizować zakłócenia usług IT i ich odzyskiwania ale wielu **różnych** centrów danych, które są zazwyczaj znajduje się setki kilometrów optymalizacji. W tym przypadku zwykle od różnych regionach platformy Azure, w tym samym regionie geograficznymi lub jako ustalonych przez użytkownika jako klient.

### <a name="overview-of-high-availability"></a>Przegląd informacji o wysokiej dostępności
Można oddzielić omówienie SAP wysokiej dostępności na platformie Azure na dwie części:

* **Wysoka dostępność infrastruktury platformy Azure**, na przykład wysokiej dostępności mocy obliczeniowej (maszyn wirtualnych), sieci, magazynu itp. oraz korzyści zwiększenia dostępności aplikacji SAP.
* **Wysoka dostępność aplikacji SAP**, na przykład HA programu SAP składników oprogramowania:
  * Serwery aplikacji SAP
  * Wystąpienie programu SAP ASCS/SCS
  * Serwer bazy danych

i jak można je łączyć z infrastrukturą systemu Azure wysokiej dostępności.

SAP wysokiej dostępności na platformie Azure ma pewne różnice w porównaniu do programu SAP wysokiej dostępności w lokalnym środowisku fizycznym lub wirtualnym. Następujący dokument z SAP opisano konfiguracje standardowe SAP wysokiej dostępności w środowiskach wirtualnych w systemie Windows: <http://scn.sap.com/docs/DOC-44415>. Nie istnieje zintegrowane sapinst SAP-HA konfiguracja dla systemu Linux, takich jak istnieje dla systemu Windows. Dotyczące SAP HA lokalnymi dla systemu Linux informacje więcej tutaj: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure
Obecnie jest umowy SLA dla maszyn wirtualnych na jednym z wartością 99,9%. Aby poznać jak dostępności jednej maszyny wirtualnej może wyglądać tak, jak można po prostu utworzyć produktu różnych dostępnych umowy SLA platformy Azure: <https://azure.microsoft.com/support/legal/sla/>.

Podstawą obliczeń jest 30 dni w miesiącu lub 43200 minut. W związku z tym przestoju 0,05% odpowiada 21,6 minut. Dostępność różnych usług będzie w zwykły sposób, należy pomnożyć w następujący sposób:

(Usługa dostępności #1/100) * (dostępności usługi #2/100) * (dostępności usługi #3/100) 

Na przykład:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 lub ogólnej dostępności 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Wysoka dostępność maszyn wirtualnych (VM)
Istnieją dwa typy zdarzeń platformy Azure, które mogą wpłynąć na dostępność maszyn wirtualnych: planowanych konserwacji i nieplanowanych konserwacji.

* Zdarzeń planowanych konserwacji są okresowych aktualizacji utworzone przez firmę Microsoft podstawowej platformy Azure, aby zwiększyć ogólną niezawodność, wydajność i bezpieczeństwo infrastruktury platformy, które są uruchamiane maszyny wirtualne.
* Nieplanowana konserwacja zdarzenia wystąpić, gdy sprzęt lub infrastruktury fizycznej podstawowej maszyny wirtualnej wystąpił błąd w określony sposób. Mogą być to awarie sieci lokalnej, błędy na dysku lokalnym lub inne awarie na poziomie regału. W przypadku wykrycia takiej awarii platformy Azure automatycznie migracji maszyny wirtualnej z złej kondycji serwera fizycznego obsługującego maszyny wirtualnej do dobrej kondycji serwera fizycznego. Te zdarzenia występują rzadko, ale mogą również spowodować ponowne uruchomienie maszyny wirtualnej.

Więcej informacji można znaleźć w dokumentacji: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Nadmiarowość magazynu Azure
Dane na koncie magazynu Microsoft Azure jest zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności, spotkania SLA magazynu platformy Azure, nawet w wypadku przejściowych awarii sprzętu.

Ponieważ Magazyn Azure może być utrzymywanie trzy obrazy danych domyślnie, RAID5 lub RAID1 na wielu dyskach platformy Azure nie jest konieczne.

Więcej informacji można znaleźć w tym artykule: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Przy użyciu ponownego uruchomienia maszyny Wirtualnej infrastruktury platformy Azure w celu osiągnięcia wyższej dostępności aplikacje SAP
Jeśli nie chcesz używać funkcji takich jak Windows Server Failover Clustering (WSFC) lub rozrusznik w systemie Linux (obecnie obsługiwana tylko dla SLES 12 lub nowszym), ponowne uruchomienie maszyny Wirtualnej Azure jest wykorzystywany do ochrony systemu SAP przed planowanych lub nieplanowanych przestojów infrastruktury Azure serwera fizycznego i ogólnej podstawowej platformy Azure.

> [!NOTE]
> Należy podać, czy ponowne uruchomienie maszyny Wirtualnej Azure przede wszystkim chroni maszyn wirtualnych i nie aplikacje. Ponowne uruchomienie maszyny Wirtualnej nie zapewnia wysoką dostępność aplikacji SAP, ale oferuje pewien poziom dostępności infrastruktury i w związku z tym pośrednio wyższej dostępności systemu SAP. Dostępna jest również nie umowy SLA dla czas potrzebny na ponowne uruchomienie maszyny Wirtualnej po awarii hosta planowane lub nieplanowane. W związku z tym ta metoda wysokiej dostępności nie jest odpowiedni dla krytycznych składników systemu SAP, takich jak (A) SCS lub systemu DBMS.
>
>

Inny element ważne infrastruktury wysokiej dostępności jest magazynu. Na przykład umowy SLA magazynu platformy Azure jest 99,9% dostępności. Jeśli jeden wdraża wszystkich maszyn wirtualnych z jego dysków do jednego konta magazynu Azure, potencjalne magazynu Azure niedostępności spowoduje niedostępność wszystkich maszyn wirtualnych, które są umieszczone na tym koncie magazynu Azure, a także wszystkie SAP składniki działają w ramach tych maszyn wirtualnych.  

Zamiast wprowadzania wszystkich maszyn wirtualnych do konta magazynu Azure pojedynczego, umożliwia także dedykowanych dla magazynu kont dla każdej maszyny Wirtualnej i w ten sposób zwiększenia ogólnej dostępności maszyny Wirtualnej i SAP aplikacji przy użyciu wielu niezależnych konta magazynu Azure.

Zarządzane dyskach platformy Azure są automatycznie umieszczane w domenie awarii maszyny wirtualnej, które są dołączone. Jeśli umieścisz dwóch maszyn wirtualnych w funkcji dostępność ustawić i dysków zarządzanych, platforma zajmie się dystrybucji dysków zarządzanych w różnych domenach awarii również. Jeśli planujesz użyć magazyn w warstwie Premium, zdecydowanie zaleca się również za pomocą zarządzania dyskami.

Przykładowa architektura systemu SAP NetWeaver, który używa konta wysokiej dostępności i magazynu infrastruktury platformy Azure może wyglądać następująco:

![Przy użyciu infrastruktury platformy Azure wysokiej dostępności w celu osiągnięcia wyższej dostępności aplikacji SAP][planning-guide-figure-2900]

Przykładowa architektura systemu SAP NetWeaver, który korzysta z infrastrukturą systemu Azure wysokiej dostępności i zarządzane dysków może wyglądać następująco:

![Przy użyciu infrastruktury platformy Azure wysokiej dostępności w celu osiągnięcia wyższej dostępności aplikacji SAP][planning-guide-figure-2901]

W przypadku krytycznych składników SAP możemy uzyskać następujące wykonanej do tej pory:

* Wysoką dostępność serwerów aplikacji SAP (AS)

  Wystąpień serwera aplikacji SAP są działanie elementów nadmiarowych. Każdy SAP, ponieważ wystąpienie jest wdrażana na jego własnej maszynie Wirtualnej, działającej w innej Azure usterek i domeny uaktualnienia (zobacz rozdział [domen błędów] [ planning-guide-3.2.1] i [domen uaktualnienia][planning-guide-3.2.2]). To jest zapewniana przez przy użyciu zestawów dostępności Azure (zobacz rozdział [zestawami dostępności Azure][planning-guide-3.2.3]). Potencjalne niedostępności planowane lub nieplanowane Azure awarii lub uaktualnienia domeny spowoduje niedostępność ograniczonej liczby maszyn wirtualnych z ich jako SAP wystąpień.

  Każdy SAP, ponieważ wystąpienie jest umieszczany w własnego konta usługi Azure Storage — potencjalnych niedostępność jedno konto magazynu Azure niedostępności tylko jednej maszyny wirtualnej spowoduje, że z jego SAP wystąpienia. Należy jednak pamiętać, że istnieje limit kont magazynu Azure w ramach jednej subskrypcji platformy Azure. Aby zapewnić automatyczne uruchamianie wystąpienia () SCS po ponownym uruchomieniu maszyny Wirtualnej, upewnij się, że można ustawić parametru Autostart w wystąpieniu () SCS start profil w rozdziale opisano [przy użyciu Autostart dla wystąpień SAP][planning-guide-11.5].
  Przeczytaj również rozdział [wysokiej dostępności dla serwerów aplikacji SAP] [ planning-guide-11.4.1] więcej szczegółów.

  Nawet jeśli w przypadku używania dysków zarządzanych tych dysków są także przechowywane informacje o koncie magazynu Azure i mogą być niedostępne w przypadku awarii magazynu.

* *Wyższy* wystąpienia SCS dostępności SAP (A)

  W tym miejscu możemy korzystać z Azure VM Uruchom ponownie, aby chronić maszyny Wirtualnej za pomocą zainstalowanego wystąpienia SCS SAP (A). W przypadku planowane lub nieplanowane przestoje Azure serwery, maszyny wirtualne zostanie uruchomiona ponownie na innym serwerze dostępne. Jak wspomniano wcześniej, przede wszystkim ponownego uruchomienia maszyny Wirtualnej Azure chroni maszyny wirtualne i nie aplikacje, w tym wystąpieniu SCS liter (A). Do ponownego uruchomienia maszyny Wirtualnej będziemy pośrednio wyższej dostępności wystąpienia SCS SAP (A). Na ułatwieniu zapewnienia automatyczne uruchamianie wystąpienia () SCS po ponownym uruchomieniu maszyny Wirtualnej, należy ustawić parametr automatycznego uruchamiania (do) SCS profilu start wystąpienia w rozdziale opisano [przy użyciu Autostart dla wystąpień SAP][planning-guide-11.5]. Oznacza to, (A) SCS wystąpienia jako pojedynczego punktu awarii (SPOF) w jednej maszyny Wirtualnej będzie dominującego współczynnik dostępności całego poziomej SAP.

* *Wyższy* dostępności serwera z bazami danych

  W tym miejscu, podobnie jak w przypadku użycia wystąpienia SCS SAP (A), firma Microsoft korzystania Azure VM Uruchom ponownie ochronę maszyny Wirtualnej z zainstalowanego oprogramowania systemu DBMS i możemy osiągnąć większą dostępność systemu DBMS dotycząca oprogramowania przez ponowne uruchomienie maszyny Wirtualnej.
  DBMS uruchomionych w jednej maszyny Wirtualnej jest również SPOF i jest współczynnik dominującego dostępności całego poziomej SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP wysoką dostępność aplikacji na IaaS platformy Azure
Aby osiągnąć pełną SAP systemu wysoką dostępność, należy chronić wszystkie krytyczne składników systemu SAP, na przykład nadmiarowe SAP aplikacji serwerów i unikatowe składników (na przykład pojedynczy punkt awarii), takich jak wystąpienie SCS SAP (A) i systemu DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Wysoka dostępność dla serwerów aplikacji SAP
Wystąpienia serwerów/okna dialogowego SAP aplikacji nie jest konieczne wziąć pod uwagę rozwiązanie określonych wysokiej dostępności. Wysoka dostępność po prostu odbywa się przez nadmiarowości i tym samym mających wystarczającą ilość je w różnych maszyn wirtualnych. One powinien wszystkie umieszczone w tej samej Azure zestawu dostępności w celu uniknięcia czy maszyny wirtualne mogły zostać zaktualizowane w tym samym czasie podczas zaplanowanej konserwacji przestoju. Podstawowe funkcje, które opiera się na różnych uaktualnienie i domen błędów w ramach jednostki skalowania Azure już została wprowadzona w rozdziale [domen uaktualnienia][planning-guide-3.2.2]. Zestawy dostępności Azure były widoczne w rozdziale [zestawami dostępności Azure] [ planning-guide-3.2.3] tego dokumentu.

Brak nie nieograniczoną liczbę usterek i domen uaktualnienia, używanego przez Azure zestawu dostępności w ramach jednostki skalowania Azure. Oznacza to, że wprowadzenia liczby maszyn wirtualnych do jednego zestawu dostępności, wcześniej lub później więcej niż jednej maszyny Wirtualnej kończy się w tej samej domeny uaktualnienia lub błędów.

Wdrażanie kilka wystąpień serwera aplikacji SAP w ich dedykowanych maszyn wirtualnych i przy założeniu, że dotarliśmy pięć uaktualnienia domen, na końcu pojawia się poniżej. Rzeczywiste maksymalna liczba domen błędów i aktualizacji w ramach zestawu dostępności może zmieniać się w przyszłości:

![HA SAP serwerów aplikacji na platformie Azure][planning-guide-figure-3000]

Więcej informacji można znaleźć w dokumentacji: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Wysoka dostępność dla wystąpienia SCS SAP (A) w systemie Windows
Windows Server Failover Cluster (WSFC) jest rozwiązaniem często używane, aby chronić wystąpienie SCS SAP (A). On również jest zintegrowany sapinst w postaci "Instalacja HA". W tym momencie infrastruktury platformy Azure nie jest zapewnienie funkcji, aby skonfigurować wymagane klastra pracy awaryjnej systemu Windows Server w taki sam sposób co ma wykonywane lokalnie.

Począwszy od stycznia 2016 roku platformy chmury Azure z systemem operacyjnym Windows nie zapewnia możliwość na dysku współużytkowane dwóch maszyn wirtualnych platformy Azure przy użyciu udostępnionego woluminu klastra.

Gdy prawidłowym rozwiązaniem jest użycie 3rd firm, co zapewnia udostępniony wolumin za pomocą replikacji synchroniczne i przezroczysty dysku, który może zostać zintegrowane usługi WSFC. Tej metody oznacza, że tylko aktywnego węzła klastra jest w stanie uzyskać dostępu do jednego z kopii dysku w punkcie w czasie. Począwszy od stycznia 2016 tego HA konfiguracja jest obsługiwana Aby chronić wystąpienie SCS SAP (A) na Windows System operacyjny gościa na maszynach wirtualnych Azure w połączeniu z 3rd firm SIOS DataKeeper.

Rozwiązanie SIOS DataKeeper zapewnia zasób klastra udostępnionego dysku do klastrów pracy awaryjnej systemu Windows, dzięki użyciu:

* Dodatkowe Azure VHD dołączony do poszczególnych maszyn wirtualnych (VM), które znajdują się w konfiguracji klastra systemu Windows
* SIOS DataKeeper Cluster Edition uruchomione w obu węzłach maszyny Wirtualnej
* Dołączone wolumin ze źródła maszyn wirtualnych do dodatkowych wirtualnego dysku twardego o SIOS DataKeeper Cluster Edition skonfigurowane w taki sposób, że synchronicznie odzwierciedla zawartość dodatkowy wirtualny dysk twardy dołączony woluminu docelowego maszyny Wirtualnej.
* SIOS DataKeeper jest abstrakcyjność źródłowa i docelowa woluminy lokalne i przedstawiające ich do klastra trybu Failover z systemem Windows jako pojedynczy udostępniony dysk.

Wszystkie szczegółowe informacje można znaleźć na temat Instalowanie klastra pracy awaryjnej systemu Windows przy użyciu SIOS DataKeeper i SAP w [klastrowanie SAP ASCS wystąpienia przy użyciu klastra trybu Failover systemu Windows Server na platformie Azure za pomocą SIOS DataKeeper] [ ha-guide-classic] oficjalny dokument.

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Wysoka dostępność dla wystąpienia SCS SAP (A) w systemie Linux
Począwszy od grudnia 2015 r. nie ma również odpowiednika do udostępnionego dysku usługi WSFC dla maszyn wirtualnych systemu Linux na platformie Azure. Alternatywne rozwiązania z wykorzystaniem 3rd firm, takich jak SIOS dla systemu Windows nie są weryfikowane jeszcze uruchamiania SAP w systemie Linux na platformie Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Wysoka dostępność dla tego wystąpienia bazy danych SAP
Typowy SAP DBMS HA Instalatora opiera się na dwóch maszyn wirtualnych systemu DBMS, gdzie DBMS wysokiej dostępności funkcja jest używana do replikacji danych z aktywnego wystąpienia systemu DBMS do drugiej maszyny Wirtualnej do pasywnej wystąpienia systemu DBMS.

Wysoka dostępność i odzyskiwaniem po awarii funkcji odzyskiwania systemu DBMS ogólnie DBMS także jako określone zostały opisane w [DBMS Deployment Guide][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Wysoką dostępność systemu SAP pełną end-to-End
Poniżej przedstawiono dwa przykłady pełną SAP NetWeaver HA architektury na platformie Azure — jeden dla systemu Windows i jeden dla systemu Linux.

Niezarządzane tylko dysków: pojęcia opisane poniżej może być konieczne naruszenia nieco podczas wdrażania wielu systemów SAP i liczba wdrożonych maszyn wirtualnych są przekracza limit maksymalnej liczby kont magazynu dla subskrypcji. W takich przypadkach konieczne można łączyć w jedno konto magazynu wirtualnych dysków twardych maszyn wirtualnych. Zwykle będzie to nie łącząc warstwy aplikacji wirtualnych dysków twardych SAP maszyn wirtualnych różnych systemów SAP.  Możemy również łączyć różnych wirtualne dyski twarde różnych DBMS maszyn wirtualnych w różnych systemów SAP w jedno konto magazynu Azure. Tym samym pamiętając o granicach IOPS konta magazynu Azure (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA w systemie Windows
![Architektura HA SAP NetWeaver aplikacji z programem SQL Server w IaaS platformy Azure][planning-guide-figure-3200]

Następujące elementy platformy Azure są używane w systemie SAP NetWeaver do zminimalizować wpływ problemami związanymi z infrastrukturą i stosowanie poprawek:

* Całego systemu jest wdrażana na platformie Azure (wymagane — system DBMS, () wystąpienia SCS i kompletna aplikacja warstwy muszą działać w tej samej lokalizacji).
* Całego systemu jest uruchamiany w ramach jednej subskrypcji platformy Azure (wymagane).
* Całego systemu jest uruchamiany w ramach jednej sieci wirtualnej platformy Azure (wymagane).
* Rozdzielenie maszyn wirtualnych z jednego systemu SAP do trzech zestawów dostępności jest możliwe nawet w przypadku wszystkich maszyn wirtualnych należących do tej samej sieci wirtualnej.
* Wszystkich maszyn wirtualnych uruchomionych wystąpień systemu DBMS jednego systemu SAP znajdują się w jednym zestawie dostępności. Przyjęto założenie, że istnieje więcej niż jedna maszyna wirtualna uruchomionych wystąpień systemu DBMS na system od macierzystego systemu DBMS wysokiej dostępności, które są używane funkcje, takie jak AlwaysOn programu SQL Server i Oracle Data Guard.
* Wszystkich maszyn wirtualnych uruchomionych wystąpień systemu DBMS użyć własnych konta magazynu. System DBMS plików dziennika i dane są replikowane z jedno konto magazynu na inne konto magazynu przy użyciu systemu DBMS wysokiej dostępności funkcji, które synchronizują dane. Niedostępność jedno konto magazynu spowoduje niedostępność jednego węzła klastra systemu Windows SQL, ale nie całej usługi SQL Server.
* Wszystkie maszyny wirtualne uruchomione wystąpienie jednego systemu SAP () SCS są w jednym zestawie dostępności. Windows Server Failover Cluster (WSFC) jest skonfigurowany w ramach tych maszyn wirtualnych w celu ochrony (A) SCS wystąpienia.
* Wszystkie maszyny wirtualne uruchomione wystąpienia () SCS Użyj własne konta magazynu. (A) SCS wystąpienia pliki i foldery globalne SAP są replikowane z jednego konta magazynu na inne konto magazynu przy użyciu replikacji SIOS DataKeeper. Niedostępność jedno konto magazynu spowoduje, że niedostępności jednego () systemu Windows SCS węzła klastra, ale nie całości () SCS usługi.
* Wszystkie maszyny wirtualne reprezentujący warstwa serwera SAP aplikacji są w trzecim zestawu dostępności.
* WSZYSTKICH serwerów aplikacji SAP uruchomionych maszyn wirtualnych użyć własnych konta magazynu. Niedostępność jedno konto magazynu spowoduje niedostępność serwera aplikacji SAP, gdy inne AS SAP nadal działać.

Poniższej ilustracji przedstawiono tej samej orientacji poziomej za pomocą zarządzania dyskami.

![Architektura HA SAP NetWeaver aplikacji z programem SQL Server w IaaS platformy Azure][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA w systemie Linux
Architektura SAP HA w systemie Linux na platformie Azure jest zasadniczo taki sam, jak w przypadku systemu Windows zgodnie z powyższym opisem. Zapoznaj się Uwaga SAP [1928533] listę rozwiązań obsługiwanych wysokiej dostępności.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Przy użyciu Autostart dla wystąpień SAP
SAP oferowane funkcjonalność do uruchomienia wystąpień SAP natychmiast po rozpoczęciu systemu operacyjnego w Maszynie wirtualnej. Dokładne kroki zostały udokumentowane w artykule bazy wiedzy SAP [1909114]. Jednak SAP nie jest rekomendowania ustawienia już ponieważ nie kontrolka nie kolejności ponowne uruchomienie wystąpienia, przy założeniu więcej niż jedna maszyna wirtualna otrzymano wpływ lub uruchomienia wielu wystąpień dla maszyny Wirtualnej. Zakładając, że typowy scenariusz Azure jednego wystąpienia serwera SAP aplikacji w maszynie Wirtualnej i w przypadku jednej maszyny Wirtualnej po pewnym czasie ponownie uruchomić pobieranie, Autostart nie są naprawdę ważne i można ją włączyć przez dodanie tego parametru:

    Autostart = 1

W profilu uruchamiania wystąpienia SAP ABAP i/lub Java.

> [!NOTE]
> Parametr Autostart może mieć także niektóre downfalls. Bardziej szczegółowo parametr wyzwalane start SAP ABAP lub wystąpienia Java, po uruchomieniu pokrewne usługi systemu Windows i Linux wystąpienia. Czy na pewno jest to możliwe po rozruchu systemu operacyjnego. Jednak ponowne uruchomienie usługi SAP są również wspólnej operacją dla zarządzania cyklem życia oprogramowania SAP funkcji, takich jak SUM lub innych aktualizacji lub uaktualnienia. Te funkcje nie są oczekiwane jest wystąpienie automatycznie uruchamiany ponownie wcale. W związku z tym parametrze Autostart powinno zostać wyłączone przed uruchomieniem takie zadania. Parametr Autostart powinien nie również w wystąpieniach programu SAP, które są klastrowane, takich jak ASCS/SCS/CI.
>
>

Można znaleźć dodatkowe informacje na temat autostart dla SAP wystąpień w tym miejscu:

* [Uruchomienie/zatrzymanie SAP wraz z systemem Unix serwera uruchamiania/zatrzymywania](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Uruchamianie i zatrzymywanie SAP NetWeaver zarządzania agentami](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Włączanie automatycznego uruchomienia z HANA bazy danych](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Większe systemów SAP 3-warstwowej
Aspekty wysokiej dostępności konfiguracje SAP 3-warstwowej otrzymano omówione już we wcześniejszych sekcjach. Ale co systemy, gdzie wymagania serwera DBMS są zbyt duże, aby została ona znajduje się w usłudze Azure, ale SAP warstwy aplikacji mogą być wdrożone na platformie Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Lokalizacja 3-warstwowej SAP konfiguracji
Nie jest obsługiwana w warstwie aplikacji podziału się lub aplikacji i systemu DBMS warstwy między lokalną i platformą Azure. Systemu SAP jest całkowicie wdrożonych lokalnie lub na platformie Azure. Również nie jest obsługiwane na niektóre serwery aplikacji, uruchom lokalną i inne na platformie Azure. To punkt początkowy dyskusji. Możemy również nie są obsługiwane do składniki systemu DBMS systemu SAP i SAP warstwa serwera aplikacji, wdrożonych w dwóch różnych regionach platformy Azure. Na przykład DBMS w warstwie aplikacji zachodnie stany USA i SAP w środkowe stany USA. Przyczyna nie obsługuje takiej konfiguracji jest czułości opóźnienia architektury SAP NetWeaver.

Jednak w ciągu ostatniego roku center partnerów opracowany wspólnej lokalizacji danych na regiony platformy Azure. Tych wspólnej lokalizacji są w bardzo pobliżu fizycznych danych Azure często centrów w obrębie regionu Azure. Połączenia trwałe z wspólnej lokalizacji za pośrednictwem usługi na platformie Azure i niedaleko może spowodować opóźnienia, które jest mniejszy niż 2 ms. W takich przypadkach do zlokalizowania warstwy DBMS (łącznie z magazynem sieci SAN/NAS) w wspólnej lokalizacji i SAP warstwy aplikacji na platformie Azure jest możliwe. Począwszy od grudnia 2015 r. nie mamy żadnych wdrożeń tak jak. Ale różnych klientów z wdrożenia aplikacji z systemem innym niż SAP korzystają z takiego podejścia już.

### <a name="offline-backup-of-sap-systems"></a>Systemy kopii zapasowej SAP w trybie offline
W zależności od konfiguracji SAP wybierze (warstwy 2 lub 3-warstwowej) może być konieczne tworzenie kopii zapasowej. Zawartość samej maszyny Wirtualnej i poproś go o kopii zapasowej bazy danych. Tworzenie kopii zapasowych związane z bazami danych powinny można zrobić za pomocą metody bazy danych. Szczegółowy opis różnych baz danych można znaleźć w [przewodnik DBMS][dbms-guide]. Z drugiej strony danych SAP utworzeniem kopii zapasowej w sposób w trybie offline (w tym również zawartości bazy danych) zgodnie z opisem w tej sekcji lub w trybie online zgodnie z opisem w następnej sekcji.

Kopia zapasowa offline zasadniczo wymagają wyłączenia maszyny Wirtualnej za pośrednictwem portalu Azure i kopię podstawowego dysku maszyny Wirtualnej oraz wszystkich dołączonych dysków do maszyny Wirtualnej. Czy to zachowanie punktu w czasie obrazu maszyny Wirtualnej i jej skojarzone dyski. Zaleca się skopiowanie kopii zapasowych do innego konta magazynu Azure. Dlatego procedura opisana w rozdziale [kopiowanie dysków między kontami magazynu Azure] [ planning-guide-5.4.2] tego dokumentu będą miały zastosowania.
Oprócz zamykania przy użyciu portalu Azure, co można również wykonać go za pomocą programu Powershell lub interfejsu wiersza polecenia, zgodnie z opisem w tym miejscu: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Przywrócenie tego stanu może składać się z usuwanie podstawowej maszyny Wirtualnej, a także oryginalnych dysków podstawowej maszyny wirtualnej i zainstalować dysków, kopiowanie ponownie zapisane dysków do oryginalnego konta magazynu lub grupę zasobów do zarządzanych dysków i ponowne wdrożenie systemu.
W tym artykule przedstawiono przykład skryptu ten proces w programie Powershell: <http://www.westerndevs.com/azure-snapshots/>

Upewnij się, że zainstalowano nową licencję SAP, ponieważ przywracanie kopii zapasowej maszyny Wirtualnej, zgodnie z powyższym opisem powoduje utworzenie nowego klucza sprzętu.

### <a name="online-backup-of-an-sap-system"></a>Kopii zapasowych online systemu SAP
Zgodnie z opisem w kopii zapasowej systemu DBMS odbywa się za pomocą metod specyficznych dla systemu DBMS [przewodnik DBMS][dbms-guide].

Pozostałe maszyny wirtualne w ramach systemu SAP utworzeniem kopii zapasowej za pomocą funkcji Kopia zapasowa maszyny wirtualnej Azure. Kopia zapasowa maszyny wirtualnej platformy Azure został wprowadzony na początku 2015 i w tym samym czasie jest standardowe metody do tworzenia kopii zapasowych pełną maszyny Wirtualnej na platformie Azure. Kopia zapasowa Azure przechowuje kopie zapasowe na platformie Azure i pozwala ponownie przywracania maszyny wirtualnej.

> [!NOTE]
> Począwszy od grudnia 2015 przy użyciu kopii zapasowej maszyny Wirtualnej nie przechowuje unikatowy identyfikator maszyny Wirtualnej, która jest stosowana do programu SAP licencjonowania. Oznacza to, że przywracanie z kopii zapasowej maszyny Wirtualnej wymaga instalacji klucza licencji SAP, zgodnie z przywróconą maszyną Wirtualną jest traktowany jako nowej maszyny Wirtualnej i nie może zastąpić ten wcześniejsze, który został zapisany.
>
> ![Windows][Logo_Windows] Windows
>
> Teoretycznie maszyn wirtualnych, które wykonywania może być kopie zapasowe baz danych w sposób ciągły, a także jeśli DBMS system obsługuje usługi VSS systemu Windows (usługa kopiowania woluminów w tle <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>), na przykład program SQL Server wykonuje.
> Należy jednak pamiętać, który oparty na wykonywanie kopii zapasowych maszyny Wirtualnej platformy Azure, które przywraca w momencie baz danych nie są możliwe. Dlatego zaleca się wykonywanie kopii zapasowych baz danych z funkcjami systemu DBMS zdejmując to zadanie kopii zapasowej maszyny Wirtualnej Azure.
>
> Aby zapoznać się z kopii zapasowej maszyny wirtualnej platformy Azure Uruchom tutaj: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Innych możliwości mają zastosowanie kombinacji programu Microsoft Data Protection Manager zainstalowane na maszynie Wirtualnej platformy Azure i kopia zapasowa Azure do wykonywania kopii zapasowej i przywracania bazy danych. Więcej informacji można znaleźć tutaj: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Nie ma odpowiednika do usługi VSS systemu Windows w systemie Linux. W związku z tym tylko plik spójne kopie zapasowe są możliwe, ale nie spójnych z aplikacją kopii zapasowych. Kopia zapasowa systemu DBMS SAP ma się odbywać za pomocą funkcji systemu DBMS. Systemu plików, która zawiera dane dotyczące SAP może zostać zapisana, na przykład za pomocą tar zgodnie z opisem w tym miejscu: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure jako lokacja DR dla krajobrazów SAP produkcji
Od Mid 2014 rozszerzenia do różnych składników funkcji Hyper-V, programu System Center i Azure Włącz użycie Azure jako lokacja DR dla maszyny wirtualne z systemami lokalnymi oparte na funkcji Hyper-V.

Blog informacji o sposobach wdrażania tego rozwiązania opisano w tym miejscu: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Podsumowanie
Najważniejsze wysokiej dostępności dla systemów SAP na platformie Azure są:

* W tym momencie SAP pojedynczego punktu awarii nie może zostać wysłana dokładnie tak samo, jak można zrobić w przypadku wdrożeń lokalnych. Przyczyną jest to dysk udostępnionych klastrów jeszcze nie można utworzyć na platformie Azure, bez korzystania z oprogramowania 3.
* Dla warstwy DBMS należy korzystać z funkcji systemu DBMS, które nie korzystają z technologii klastra udostępnionego dysku. Szczegółowe informacje są udokumentowane w artykule [przewodnik DBMS][dbms-guide].
* Aby zminimalizować wpływ problemów w obrębie domen błędów w Azure konserwacja infrastruktury lub hosta, należy korzystać z zestawami dostępności Azure:
  * Zalecane jest mają jeden zbiór dostępności dla warstwy aplikacji SAP.
  * Zalecane jest mają oddzielne zestaw dostępności dla warstwy SAP DBMS.
  * NIE zaleca się zastosowanie tego samego zestawu dostępności dla maszyn wirtualnych różnych systemów SAP.
  * Zalecane jest, aby użyć dysków zarządzanych w warstwie Premium.
* Do celów tworzenia kopii zapasowej warstwy SAP DBMS, sprawdź, czy [przewodnik DBMS][dbms-guide].
* Tworzenie kopii zapasowej wystąpienia okna dialogowego SAP ma sens małego ponieważ jest zwykle szybsze ponownie wdrożyć prosty okna dialogowego wystąpień.
* Tworzenie kopii zapasowej maszyny Wirtualnej zawierający katalogu globalnego systemu SAP, z nim wszystkie profile z różnymi wystąpieniami sensu i powinno odbywać się przy użyciu usługi Kopia zapasowa Windows lub, na przykład tar w systemie Linux. Ponieważ istnieją różnice między systemem Windows Server 2008 (R2) i Windows Server 2012 (R2), które ułatwiają tworzenie kopii zapasowej za pomocą nowszej systemu Windows Server zwalnia, firma Microsoft zaleca, aby uruchomić system Windows Server 2012 (R2) jako systemu operacyjnego gościa.
