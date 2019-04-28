---
title: Wdrażanie maszyn wirtualnych platformy Azure środowiska ASE systemu SAP DBMS w przypadku obciążeń SAP | Dokumentacja firmy Microsoft
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f50f013020c704ddc294a59f8c6c5dac24bbd5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835281"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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



W tym dokumencie opisano kilka różnych obszarów, aby wziąć pod uwagę podczas wdrażania SAP ASE w modelu IaaS platformy Azure. Jako warunek wstępny do tego dokumentu, powinien mieć odczytu dokumentu [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md) i inne przewodniki w [obciążeń SAP w dokumentacji platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Charakterystyki SAP ASE na Windows
Począwszy od programu Microsoft Azure, można migrować istniejące aplikacje SAP ASE na maszynach wirtualnych platformy Azure. SAP ASE w maszynie wirtualnej platformy Azure pozwala zmniejszyć całkowity koszt posiadania, wdrażania, zarządzania i konserwacji aplikacji dla przedsiębiorstw szeroki zakres poprzez łatwe migrowanie tych aplikacji w systemie Microsoft Azure. Za pomocą SAP ASE w maszynie wirtualnej platformy Azure Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie.

Umowy SLA dla maszyn wirtualnych platformy Azure można znaleźć tutaj: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Platforma Microsoft Azure oferuje wiele typów inną maszynę wirtualną, które umożliwiają uruchamianie najmniejszą systemów SAP i krajobrazów do dużych systemów SAP i krajobrazów tysiące użytkowników. Oprogramowanie SAP, ustalanie rozmiaru protokoły SAP liczby różnych SAP certyfikowane, jednostek SKU maszyn wirtualnych znajduje się w Uwaga SAP [1928533].

Instrukcje i zalecenia dotyczące użytkowania usługi Azure Storage, wdrożenia SAP maszyn wirtualnych lub SAP monitorowanie w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md) zbyt dotyczą wdrożeń SAP ASE.

### <a name="sap-ase-version-support"></a>Obsługa wersji środowiska ASE SAP
SAP obecnie obsługuje SAP ASE wersji 16.0 do użytku z produktów SAP Business Suite. Wszystkie aktualizacje dla serwera SAP ASE i sterowników JDBC i ODBC do użycia z produktów SAP Business Suite znajdują się wyłącznie za pośrednictwem SAP Service Marketplace na: <https://support.sap.com/swdc>.

Nie pobieraj aktualizacje serwera SAP ASE lub sterowników JDBC i ODBC bezpośrednio z witryny sieci Web programu Sybase. Szczegółowe informacje na temat poprawek, które są obsługiwane do użytku z programem SAP produktów lokalnych i w usłudze Azure Virtual Machines, zobacz następujące uwagi SAP:

* [1590719]
* [1973241]

Ogólne informacje na temat systemem SAP Business Suite SAP ASE znajdują się w [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania środowiska ASE SAP dla powiązanych SAP SAP ASE instalacji w maszynach wirtualnych platformy Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura wdrożenia SAP ASE
Pliki wykonywalne SAP ASE powinny być znajduje się lub zainstalowany na dysku systemowym dysku systemu operacyjnego maszyny Wirtualnej (dysk c:\). Zazwyczaj większość baz danych narzędzia i system SAP ASE nie występuje wysokie obciążenie. Dlatego system i narzędzia baz danych (master, model, saptools, sybmgmtdb, sybsystemdb) może pozostawać na dysku C:\. 

Wyjątek może być tymczasowej bazy danych, która w przypadku niektórych SAP ERP oraz wszystkich obciążeń BW może wymagać większą ilość danych lub woluminu operacji We/Wy. Woluminy lub operacji We/Wy, które nie mogą być określane przez dysk systemu operacyjnego maszyny Wirtualnej (dysk C:\).

Zależne od wersji SAPInst/SWPM używany do instalacji, konfiguracji wystąpienia SAP ASE może wyglądać następująco:

* Pojedynczy tempdb SAP ASE, która jest tworzona podczas instalacji SAP ASE
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowe saptempdb utworzone przez procedury instalacyjnej SAP
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowych danych tempdb, który został utworzony ręcznie (na przykład następujące Uwaga SAP [1752266]) do określonej bazy danych tempdb ERP i przeniesieniu jej/BW wymagań

Poza ze względu na wydajność systemu ERP określonych lub wszystkich obciążeń BW może sprawić, że warto przechowywać urządzeń bazy danych tempdb, dodatkowo utworzonej bazy danych TempDB na dysku innym niż C:\. Jeśli istnieje nie dodatkowe bazy danych tempdb, zalecane jest utworzenie (Uwaga SAP [1752266]).

Dla takich systemów poniższe kroki mają zostać wykonane dodatkowo utworzonej bazy danych TempDB:

* Przenieś jako pierwsze urządzenie bazy danych tempdb na pierwszym urządzeniu bazy danych SAP
* Dodawanie urządzeń bazy danych tempdb do każdego z wirtualnych dysków twardych, zawierające urządzenia bazy danych SAP

Ta konfiguracja umożliwia bazy danych tempdb zużyć więcej miejsca niż dysk systemowy. Jako odwołanie jeden Sprawdź rozmiary urządzenia bazy danych tempdb na istniejących systemów, które działają lokalnie. Lub taka konfiguracja umożliwia liczby operacji We/Wy względem bazy danych tempdb, które nie mogą być określane przy użyciu dysku systemowego. Systemy, które są uruchomione w środowisku lokalnym, może służyć do monitorowania obciążenia operacji We/Wy względem bazy danych tempdb.

Nigdy nie umieścić wszelkie urządzenia SAP ASE na dysku D:\, maszyny wirtualnej. SAP ASE ta informacja dotyczy również bazy danych tempdb, nawet jeśli obiekty przechowywane w bazie danych tempdb są one jedynie tymczasowe.

Dla danych i wdrożeń plików dziennika transakcji, instrukcje i sugestie w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). W przypadku wdrożenia oparte na Windows użycie funkcji miejsca do magazynowania systemu Windows zaleca się używać do tworzenia zestawów usługi stripe z wystarczającą operacje We/Wy, przepływność i woluminu.  

#### <a name="impact-of-database-compression"></a>Wpływ kompresja bazy danych
W konfiguracji, gdy przepustowość operacji We/Wy może stać się czynnikiem ograniczającym Każda miara, co zmniejsza operacje We/Wy może przyczynić się do rozproszonego obciążenia, jakie można jedno uruchomienie w scenariuszu IaaS, takich jak platforma Azure. Dlatego zalecane jest aby upewnić się, że kompresja SAP ASE jest używana przed przekazaniem istniejącej bazy danych SAP na platformie Azure.

Zalecenie, aby zastosować kompresję przed przekazaniem do usługi Azure znajduje się z kilku powodów:

* Ilość danych do przesłania na platformę Azure jest niższa
* Czas trwania wykonania kompresji jest krótszy, przy założeniu, że jeden służy silniejsze sprzętu z więcej procesorów ani większą przepustowość operacji We/Wy lub mniej operacji We/Wy opóźnienia w środowisku lokalnym
* Mniejsze rozmiary bazy danych może prowadzić do mniejsze koszty przydział dysku

Kompresja danych i obiektów LOB działa na maszynie wirtualnej hostowanej w usłudze Azure Virtual Machines, jak w środowisku lokalnym. Szczegółowe informacje na temat sposobu sprawdzania, jeśli kompresja jest już do używania w istniejącej bazy danych SAP ASE, zapoznaj się uwagę [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Aby monitorować wystąpienia bazy danych przy użyciu DBACockpit
W przypadku systemów SAP, które używają SAP ASE jako platformę bazy danych, DBACockpit jest dostępne jako okna przeglądarki osadzone w transakcji DBACockpit lub Webdynpro. Jednak pełnej funkcjonalności do monitorowania i administrowania nim bazy danych jest dostępna w implementacji Webdynpro DBACockpit, tylko.

Jako z lokalnymi systemami kilka czynności, aby włączyć wszystkie funkcje oprogramowania SAP NetWeaver używane przez implementację Webdynpro DBACockpit. Postępuj zgodnie z Uwaga SAP [1245200] Włącz użycie webdynpros i generować wymagane te. Gdy postępując zgodnie z instrukcjami w powyższym notes, również skonfigurować Internet Communication Manager (icm) oraz porty, które mają zostać użyte w przypadku połączeń http i https. Ustawieniem domyślnym dla protokołu http, wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

i łącza wygenerowane w transakcji, które DBACockpit wygląda podobnie do:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od tego, jak maszyny wirtualnej platformy Azure, hosting systemu SAP jest podłączony do usługi AD i DNS, należy się upewnić, że ICM korzysta z w pełni kwalifikowana nazwa hosta, która może zostać rozpoznana na komputerze, na którym otwierasz DBACockpit z. Patrz Uwaga SAP [773830] zrozumieć, jak ICM określa pełni kwalifikowaną nazwę hosta na podstawie parametrów profilu i ustawić parametr icm/host_name_full jawnie, jeśli to konieczne.

Jeśli wdrożono maszynę Wirtualną w scenariuszu tylko w chmurze bez łączności między środowiskami lokalnymi między lokalną i platformą Azure, musisz zdefiniować publiczny adres IP i domainlabel. Format jej publiczną nazwę DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Można znaleźć więcej szczegółów dotyczących nazwy DNS [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametru profilu icm/host_name_full SAP na nazwę DNS maszyny Wirtualnej platformy Azure link może wyglądać podobnie do:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

W takim przypadku musisz upewnić się, że:

* Dodawanie reguł dla ruchu przychodzącego do sieciowej grupy zabezpieczeń w witrynie Azure portal, w którym są porty TCP/IP używane do komunikacji z ICM
* Dodawanie reguł dla ruchu przychodzącego do konfiguracji zapory Windows porty TCP/IP używane do komunikacji z ICM

Dla zautomatyzowanych zaimportowane dla wszystkich dostępnych poprawek zalecane jest okresowo stosowanie korekcji kolekcji Uwaga SAP mające zastosowanie do używanej wersji SAP:

* [1558958]
* [1619967]
* [1882376]

Więcej informacji na temat DBA Cockpit SAP ASE znajdują się w następujących SAP Notes:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Informacje dotyczące tworzenia kopii zapasowej/odzyskiwania SAP ASE
Podczas wdrażania środowiska ASE SAP na platformie Azure, muszą być przejrzane w swojej metodologii kopii zapasowej. Nawet w przypadku systemów nieprodukcyjnych baz danych SAP należy wykonać kopię zapasową okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, tworzenie kopii zapasowej może być mniej ważne w odniesieniu do wyrównującej awarii magazynu. Głównym powodem utrzymywania właściwego planu tworzenia kopii zapasowych i przywracania jest inne, które można kompensowane błędy logiczne/ręczne, zapewniając punktu w czasie możliwości odzyskiwania. Dlatego celem jest albo użyj kopii zapasowych, aby przywrócić bazę danych do pewnego momentu w czasie lub używać kopie zapasowe na platformie Azure, aby zapełnić innego systemu przez skopiowanie istniejącej bazy danych. 

Wykonywanie kopii zapasowych i przywracania bazy danych na platformie Azure działa tak samo, jak w środowisku lokalnym. Zobacz uwagi SAP:

* [1588316]
* [1585981]

Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji zrzutu i planowania kopii zapasowych. W zależności od potrzeb, które można skonfigurować i strategii bazy danych i dziennika zrzuty na dysku do jednego z istniejących dysków lub należy dodać dodatkowy dysk dla kopii zapasowej. Aby ograniczyć ryzyko utraty danych w przypadku błędu, zaleca się używania dysku, w którym znajdują się pliki bazy danych, nie.

Oprócz danych i kompresja LOB SAP ASE oferuje również kompresja kopii zapasowej. Korzystanie z mniej miejsca przy użyciu bazy danych i dziennika zrzuty zaleca kompresja kopii zapasowej. Aby uzyskać więcej informacji, patrz Uwaga SAP [1588316]. Kompresja kopii zapasowej jest również zmniejszyć ilość danych do przeniesienia, jeśli planujesz pobierania kopii zapasowych lub wirtualnych dyskach twardych zawierających zrzuty kopii zapasowej z maszyny wirtualnej platformy Azure do środowiska lokalnego.

Nie należy używać dysku D:\ jako miejsce docelowe zrzutu bazy danych lub dziennika.

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące tworzenia kopii zapasowych/przywracania
Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania jest zależna od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Należy pamiętać, że kompresja kopii zapasowej wykorzystuje zasoby procesora CPU. Tym użycia procesora CPU przez kompresja kopii zapasowej można odtworzyć istotną rolę na maszynach wirtualnych za pomocą niewielkiej liczby wątków procesora CPU. W związku z tym można założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych mniejszy ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (katalogi usługi Stripe, dyski) można zapisać kopii zapasowej do mniejszej przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu czy dostępne są dwie opcje, które mogą być używane/łączyć w zależności od potrzeb:

* Stosowanie wolumin docelowy kopii zapasowej za pośrednictwem wielu dysków zainstalowanych w celu zwiększenia wydajności operacji We/Wy na tego woluminu rozłożonego
* Tworzenie konfiguracji zrzutu na poziomie SAP ASE, który używa więcej niż jeden katalog docelowy można zapisać zrzutu do

Stosowanie woluminu dysku na wiele dysków zainstalowanych została omówiona w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). Więcej informacji na temat korzystania z wieloma katalogami w konfiguracji zrzutu SAP ASE, można znaleźć w dokumentacji dotyczącej sp_config_dump procedury składowanej, który jest używany do tworzenia konfiguracji zrzutu na [Centrum Sybase informacyjne](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Odzyskiwanie po awarii z maszyn wirtualnych platformy Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikacja danych z serwerem replikacji bazy danych Sybase SAP
Za pomocą SAP Sybase replikacji serwera (SRS), SAP ASE rozwiązaniem jest ciepło wstrzymania asynchronicznie przesyłać transakcji bazy danych do odległych lokalizacji. 

Instalacji i działania SRS działa również funkcjonalnie na maszynie wirtualnej hostowane w usługach maszyny wirtualnej platformy Azure, jak w środowisku lokalnym.

SAP ASE HADR nie wymaga usługi Azure wewnętrznego modułu równoważenia obciążenia i nie ma zależności na poziomie klastrowania systemu operacyjnego. Działa na Windows Azure i maszyn wirtualnych systemu Linux. Aby uzyskać szczegółowe informacje na temat SAP ASE HADR przeczytaj [przewodnika użytkownicy SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Charakterystyki SAP ASE w systemie Linux
Począwszy od programu Microsoft Azure, możesz z łatwością migrować istniejące aplikacje SAP ASE na maszynach wirtualnych platformy Azure. SAP ASE na maszynie wirtualnej, można zmniejszyć całkowity koszt posiadania, wdrażania, zarządzania i konserwacji aplikacji dla przedsiębiorstw szeroki zakres, łatwe migrowanie tych aplikacji w systemie Microsoft Azure. Za pomocą SAP ASE w maszynie wirtualnej platformy Azure Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie.

Do wdrażania maszyn wirtualnych platformy Azure jest musisz znać oficjalne umowy SLA, które można znaleźć tutaj: <https://azure.microsoft.com/support/legal/sla>

Dane zmiany rozmiaru SAP oraz listę jednostek SKU maszyn wirtualnych z certyfikatem SAP jest podawany jako Uwaga SAP [1928533]. Dodatkowe SAP rozmiaru dokumentów dla maszyn wirtualnych platformy Azure można znaleźć tutaj <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> i w tym miejscu <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instrukcje i zalecenia dotyczące użycia usługi Azure Storage, wdrożenia SAP maszyn wirtualnych lub SAP monitorowanie dotyczą wdrożeń SAP ASE w połączeniu z aplikacjami SAP opisany w całym rozdziały pierwsze cztery części tego dokumentu.

W poniższych dwóch SAP o wersji obejmują ogólne informacje dotyczące środowiska ASE w systemie Linux i środowisko ASE w chmurze:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Obsługa wersji środowiska ASE SAP
SAP obecnie obsługuje SAP ASE wersji 16.0 do użytku z produktów SAP Business Suite. Wszystkie aktualizacje dla serwera SAP ASE i sterowników JDBC i ODBC do użycia z produktów SAP Business Suite znajdują się wyłącznie za pośrednictwem SAP Service Marketplace na: <https://support.sap.com/swdc>.

Jak w przypadku instalacji lokalnej nie pobieraj aktualizacje serwera SAP ASE lub sterowników JDBC i ODBC bezpośrednio z witryny sieci Web programu Sybase. Szczegółowe informacje na temat poprawek, które są obsługiwane przez oprogramowanie SAP Business Suite produktów lokalnych i w usłudze Azure Virtual Machines, zobacz następujące uwagi SAP:

* [1590719]
* [1973241]

Ogólne informacje na temat systemem SAP Business Suite SAP ASE znajdują się w [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania środowiska ASE SAP dla powiązanych SAP SAP ASE instalacji w maszynach wirtualnych platformy Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura wdrożenia SAP ASE
Pliki wykonywalne SAP ASE należy znajduje się lub zainstalowane w głównym systemie plików maszyny wirtualnej (/sybase). Zazwyczaj większość baz danych narzędzia i system SAP ASE nie występuje wysokie obciążenie. Dlatego system i narzędzia baz danych (master, model, saptools, sybmgmtdb, sybsystemdb) mogą być przechowywane w systemie plików w katalogu głównego. 

Wyjątek może być tymczasowej bazy danych, która w przypadku niektórych SAP ERP oraz wszystkich obciążeń BW może wymagać większą ilość danych lub woluminu operacji We/Wy. Woluminy lub operacji We/Wy, które nie mogą być określane przez dysk systemu operacyjnego maszyny Wirtualnej 

W zależności od wersji SAPInst/SWPM używane do instalowania systemu bazy danych może zawierać:

* Pojedynczy tempdb SAP ASE, utworzony podczas instalowania SAP ASE
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowe saptempdb utworzone przez procedury instalacyjnej SAP
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowych danych tempdb, który został utworzony ręcznie (na przykład następujące Uwaga SAP [1752266]) do określonej bazy danych tempdb ERP i przeniesieniu jej/BW wymagań

Poza ze względu na wydajność ERP określonych lub wszystkich obciążeń BW może sprawić, że warto przechowywać urządzeń bazy danych tempdb, dodatkowo utworzonej bazy danych tempdb (przez SWPM lub ręcznie) w systemie osobny plik może być reprezentowany przez dysk danych platformy Azure z jednego lub RAID systemu Linux, obejmujące m ss dyski danych na platformie Azure. Jeśli istnieje nie dodatkowe bazy danych tempdb, zalecane jest utworzenie (Uwaga SAP [1752266]).

Dla takich systemów dodatkowo utworzonej bazy danych tempdb należy przeprowadzić następujące czynności:

* Przenieś pierwszy katalog bazy danych tempdb na pierwszy system plików bazy danych SAP
* Dodawanie katalogów bazy danych tempdb na każdy z tych dysków, zawierający system plików bazy danych SAP

Ta konfiguracja umożliwia bazy danych tempdb zużyć więcej miejsca niż dysk systemowy. Jako odwołanie jeden Sprawdź rozmiary urządzenia bazy danych tempdb na istniejących systemów, które działają lokalnie. Lub taka konfiguracja umożliwia liczby operacji We/Wy względem bazy danych tempdb, które nie mogą być określane przy użyciu dysku systemowego. Systemy, które są uruchomione w środowisku lokalnym, może służyć do monitorowania obciążenia operacji We/Wy względem bazy danych tempdb.

Nigdy nie umieścić wszelkie katalogi SAP ASE na katalogu/mnt lub /mnt/resource maszyny wirtualnej. SAP ASE ta informacja dotyczy również bazy danych tempdb, nawet jeśli obiekty przechowywane w bazie danych tempdb są one jedynie tymczasowe. Ponieważ katalogu/mnt lub /mnt/resource jest domyślna maszyny Wirtualnej platformy Azure tymczasowego miejsca, który nie jest trwały. Więcej informacji na temat tymczasowego miejsca na maszynie Wirtualnej platformy Azure można znaleźć w [w tym artykule][virtual-machines-linux-how-to-attach-disk]

Dla danych i wdrożeń plików dziennika transakcji, instrukcje i sugestie w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). W przypadku wdrożeń opartych na systemie Linux użycie LVM lub MDADM zaleca się używać do tworzenia zestawów usługi stripe z wystarczającą operacje We/Wy, przepływność i woluminu. 

#### <a name="impact-of-database-compression"></a>Wpływ kompresja bazy danych
W konfiguracji, gdy przepustowość operacji We/Wy może stać się czynnikiem ograniczającym Każda miara, co zmniejsza operacje We/Wy może przyczynić się do rozproszonego obciążenia, jakie można jedno uruchomienie w scenariuszu IaaS, takich jak platforma Azure. Dlatego zalecane jest aby upewnić się, że kompresja SAP ASE jest używana przed przekazaniem istniejącej bazy danych SAP na platformie Azure.

Zalecenie, aby zastosować kompresję przed przekazaniem do usługi Azure znajduje się z kilku powodów:

* Ilość danych do przesłania na platformę Azure jest niższa
* Czas trwania wykonania kompresji jest krótszy, przy założeniu, że jeden służy silniejsze sprzętu z więcej procesorów ani większą przepustowość operacji We/Wy lub mniej operacji We/Wy opóźnienia w środowisku lokalnym
* Mniejsze rozmiary bazy danych może prowadzić do mniejsze koszty przydział dysku

Kompresja danych i obiektów LOB działa na maszynie wirtualnej hostowanej w usłudze Azure Virtual Machines, jak w środowisku lokalnym. Szczegółowe informacje na temat sposobu sprawdzania, jeśli kompresja jest już do używania w istniejącej bazy danych SAP ASE, zapoznaj się uwagę [1750510]. Aby uzyskać więcej informacji na temat kompresja bazy danych, patrz Uwaga SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Aby monitorować wystąpienia bazy danych przy użyciu DBACockpit
W przypadku systemów SAP, które używają SAP ASE jako platformę bazy danych, DBACockpit jest dostępne jako okna przeglądarki osadzone w transakcji DBACockpit lub Webdynpro. Jednak pełnej funkcjonalności do monitorowania i administrowania nim bazy danych jest dostępna w implementacji Webdynpro DBACockpit, tylko.

Jako z lokalnymi systemami kilka czynności, aby włączyć wszystkie funkcje oprogramowania SAP NetWeaver używane przez implementację Webdynpro DBACockpit. Postępuj zgodnie z Uwaga SAP [1245200] Włącz użycie webdynpros i generować wymagane te. Gdy postępując zgodnie z instrukcjami w powyższym notes, również skonfigurować Internet Communication Manager (icm) oraz porty, które mają zostać użyte w przypadku połączeń http i https. Ustawieniem domyślnym dla protokołu http wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

i łącza generowane w transakcji DBACockpit będą wyglądać mniej więcej tak:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od tego, jak maszyny wirtualnej platformy Azure, hosting systemu SAP jest podłączony do usługi AD i DNS, należy się upewnić, że ICM korzysta z w pełni kwalifikowana nazwa hosta, która może zostać rozpoznana na komputerze, na którym otwierasz DBACockpit z. Patrz Uwaga SAP [773830] Aby zrozumieć, jak ICM określa pełni kwalifikowaną nazwę hosta w zależności od parametrów profilu i ustaw parametr icm/host_name_full jawnie w razie potrzeby.

Jeśli wdrożono maszynę Wirtualną w scenariuszu tylko w chmurze bez łączności między środowiskami lokalnymi między lokalną i platformą Azure, musisz zdefiniować publiczny adres IP i domainlabel. Format jej publiczną nazwę DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Można znaleźć więcej szczegółów dotyczących nazwy DNS [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametru profilu icm/host_name_full SAP na nazwę DNS maszyny Wirtualnej platformy Azure link może wyglądać podobnie do:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

W takim przypadku musisz upewnić się, że:

* Dodawanie reguł dla ruchu przychodzącego do sieciowej grupy zabezpieczeń w witrynie Azure portal, w którym są porty TCP/IP używane do komunikacji z ICM
* Dodawanie reguł dla ruchu przychodzącego do konfiguracji zapory Windows porty TCP/IP używane do komunikacji z ICM

Dla zautomatyzowanych zaimportowane dla wszystkich dostępnych poprawek zalecane jest okresowo stosowanie korekcji kolekcji Uwaga SAP mające zastosowanie do używanej wersji SAP:

* [1558958]
* [1619967]
* [1882376]

Więcej informacji na temat DBA Cockpit SAP ASE znajdują się w następujących SAP Notes:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Informacje dotyczące tworzenia kopii zapasowej/odzyskiwania SAP ASE
Podczas wdrażania środowiska ASE SAP na platformie Azure, muszą być przejrzane w swojej metodologii kopii zapasowej. Nawet w przypadku systemów nieprodukcyjnych baz danych SAP należy wykonać kopię zapasową okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, tworzenie kopii zapasowej może być mniej ważne w odniesieniu do wyrównującej awarii magazynu. Głównym powodem utrzymywania właściwego planu tworzenia kopii zapasowych i przywracania jest inne, które można kompensowane błędy logiczne/ręczne, zapewniając punktu w czasie możliwości odzyskiwania. Dlatego celem jest albo użyj kopii zapasowych, aby przywrócić bazę danych do pewnego momentu w czasie lub używać kopie zapasowe na platformie Azure, aby zapełnić innego systemu przez skopiowanie istniejącej bazy danych. 

Wykonywanie kopii zapasowych i przywracania bazy danych na platformie Azure działa tak samo, jak w środowisku lokalnym. Zobacz uwagi SAP:

* [1588316]
* [1585981]

Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji zrzutu i planowania kopii zapasowych. W zależności od potrzeb i strategii można skonfigurować bazy danych i dziennika zbierania zrzutów przy użyciu dysku do jednego z istniejących dysków lub dodać dodatkowy dysk dla kopii zapasowej. Aby ograniczyć ryzyko utraty danych w przypadku błędu, zaleca się używania dysku, na którym znajduje się nie bazy danych pliku lub katalogu.

Oprócz danych i kompresja LOB SAP ASE oferuje również kompresja kopii zapasowej. Korzystanie z mniej miejsca przy użyciu bazy danych i dziennika zrzuty zaleca kompresja kopii zapasowej. Aby uzyskać więcej informacji, patrz Uwaga SAP [1588316]. Kompresja kopii zapasowej jest również zmniejszyć ilość danych do przeniesienia, jeśli planujesz pobierania kopii zapasowych lub wirtualnych dyskach twardych zawierających zrzuty kopii zapasowej z maszyny wirtualnej platformy Azure do środowiska lokalnego.

Nie należy używać maszyny Wirtualnej platformy Azure, miejsca tymczasowe katalogu/mnt lub /mnt/resource jako miejsce docelowe zrzutu bazy danych lub dziennika.

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące tworzenia kopii zapasowych/przywracania
Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania jest zależna od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Należy pamiętać, że kompresja kopii zapasowej wykorzystuje zasoby procesora CPU. Tym użycia procesora CPU przez kompresja kopii zapasowej można odtworzyć istotną rolę na maszynach wirtualnych za pomocą niewielkiej liczby wątków procesora CPU.  W związku z tym można założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych mniejszy ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (oprogramowania w systemie Linux RAID, dyski) można zapisać kopii zapasowej do mniejszej przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu czy dostępne są dwie opcje, które mogą być używane/łączyć w zależności od potrzeb:

* Stosowanie wolumin docelowy kopii zapasowej za pośrednictwem wielu dysków zainstalowanych w celu zwiększenia wydajności operacji We/Wy na tego woluminu rozłożonego
* Tworzenie konfiguracji zrzutu na poziomie SAP ASE, który używa więcej niż jeden katalog docelowy można zapisać zrzutu do

Stosowanie woluminu dysku na wiele dysków zainstalowanych została omówiona w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). Więcej informacji na temat korzystania z wieloma katalogami w konfiguracji zrzutu SAP ASE, można znaleźć w dokumentacji dotyczącej sp_config_dump procedury składowanej, który jest używany do tworzenia konfiguracji zrzutu na [Centrum Sybase informacyjne](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Odzyskiwanie po awarii z maszyn wirtualnych platformy Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikacja danych z serwerem replikacji bazy danych Sybase SAP
Za pomocą SAP Sybase replikacji serwera (SRS), SAP ASE rozwiązaniem jest ciepło wstrzymania asynchronicznie przesyłać transakcji bazy danych do odległych lokalizacji. 

Instalacji i działania SRS działa również funkcjonalnie na maszynie wirtualnej hostowane w usługach maszyny wirtualnej platformy Azure, jak w środowisku lokalnym.

Środowisko ASE HADR za pośrednictwem programu SAP replikacji serwera jest obsługiwane. Zdecydowanie zaleca się używać SAP ASE 16.03 próby takiej konfiguracji. Bardziej szczegółowe instrukcje instalowania tych konfiguracji znajdują się w szczegóły, w tym [blogu](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
