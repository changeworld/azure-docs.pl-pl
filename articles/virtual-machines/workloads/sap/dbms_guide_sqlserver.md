---
title: Wdrożenie programu SQL Server Azure Virtual Machines DBMS w przypadku obciążeń SAP | Dokumentacja firmy Microsoft
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP
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
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c12c75bd5c357613d55e04aed67c0cc901135e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835539"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Wdrożenie programu SQL Server Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver

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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




W tym dokumencie opisano kilka różnych obszarów, aby wziąć pod uwagę podczas wdrażania programu SQL Server w przypadku obciążeń SAP w modelu IaaS platformy Azure. Jako warunek wstępny do tego dokumentu, powinien mieć odczytu dokumentu [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md) oraz inne przewodniki w [obciążeń SAP nawdokumentacjiplatformyAzure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> Zakres tego dokumentu jest wersją Windows w programie SQL Server. SAP nie obsługuje wersji systemu Linux, programu SQL Server przy użyciu oprogramowania SAP. Dokument nie zawiera systemu Microsoft Azure SQL Database, która to platforma jako usługa ofertę platformy Microsoft Azure. Dyskusja w tym dokumencie jest o uruchamianiu produktu SQL Server, jaki jest znany dla wdrożeń lokalnych maszynach wirtualnych platformy Azure, korzystając z infrastruktury jako możliwości usługi platformy Azure. Funkcje bazy danych i funkcji między tych dwóch ofert są różne i nie może być mieszane ze sobą. Zobacz też: <https://azure.microsoft.com/services/sql-database/>
> 
>

Ogólnie rzecz biorąc należy rozważyć przy użyciu najbardziej aktualnych programu SQL Server wydania do uruchamiania obciążeń SAP w modelu IaaS platformy Azure. Zainstalowane najnowsze wersje programu SQL Server oferują lepszą integrację w niektórych usług platformy Azure i funkcji. Lub zawierają zmiany, które zoptymalizować operacji w infrastrukturze IaaS platformy Azure.

Zalecane jest aby przejrzeć [to] [ virtual-machines-sql-server-infrastructure-services] dokumentacji, przed kontynuowaniem.

W poniższych sekcjach rodzajów części dokumentacji w ramach powyższego łącza są agregowane i wymienione. Charakterystyka wokół SAP są również wymienione i niektóre pojęcia są opisane bardziej szczegółowo. Jednak zdecydowanie zalecane jest do pracy z dokumentacją powyżej pierwszy przed odczytaniem dokumentacji specyficzne dla programu SQL Server.

Brak niektórych programu SQL Server w modelu IaaS określone informacje, które należy znać przed kontynuowaniem:

* **Obsługa wersji programu SQL**: Dla klientów SAP, SQL Server 2008 R2 i nowszej jest obsługiwana na maszynie wirtualnej platformy Microsoft. Wcześniejsze wersje nie są obsługiwane. Przejrzyj ogólnej [oświadczenie pomocy technicznej](https://support.microsoft.com/kb/956893) Aby uzyskać więcej informacji. Ogólnie rzecz biorąc program SQL Server 2008 jest obsługiwana przez firmę Microsoft również. Jednak z powodu znaczącej funkcjonalności dla rozwiązania SAP, który został wprowadzony przy użyciu programu SQL Server 2008 R2, SQL Server 2008 R2 jest minimalna wersja dla rozwiązania SAP. Ogólnie rzecz biorąc należy rozważyć przy użyciu najbardziej aktualnych programu SQL Server wydania do uruchamiania obciążeń SAP w modelu IaaS platformy Azure. Zainstalowane najnowsze wersje programu SQL Server oferują lepszą integrację w niektórych usług platformy Azure i funkcji. Lub zawierają zmiany, które zoptymalizować operacji w infrastrukturze IaaS platformy Azure. W związku z tym ten dokument jest ograniczony do programu SQL Server 2016 i programu SQL Server 2017.
* **Wydajność programu SQL**: Microsoft Azure udostępnione maszyny wirtualne wykonywania oraz w porównaniu do innych ofert wirtualizacji chmury publicznej, ale poszczególne wyniki mogą się różnić. Zapoznaj się z artykułem [najlepsze rozwiązania dotyczące programu SQL Server w usłudze Azure Virtual Machines wydajności](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Przy użyciu obrazów z witryny Azure Marketplace**: Jest to najszybszy sposób, aby wdrożyć nową maszynę Wirtualną Azure firmy Microsoft można użyć obrazu z witryny Azure Marketplace. Brak obrazów w portalu Azure Marketplace, które zawierają najbardziej aktualne wersje programu SQL Server. Nie można od razu używać obrazów programu SQL Server już zainstalowanym dla aplikacji SAP NetWeaver. Przyczyną jest to domyślne sortowanie programu SQL Server jest zainstalowane w ramach tych obrazów i nie sortowania wymaganych przez systemy oprogramowania SAP NetWeaver. Aby można było używać tych obrazów, zobacz, jakie kroki opisane w rozdziale [przy użyciu obrazu programu SQL Server z witryny Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Zalecenia dotyczące maszyny Wirtualnej/VHD struktury dla wdrożeń SAP związane z programu SQL Server
Zgodnie z ogólny opis plików wykonywalnych programu SQL Server należy się lub zainstalowany na dysku systemowym dysku systemu operacyjnego maszyny Wirtualnej (dysk C:\).  Zazwyczaj większość systemowych baz danych programu SQL Server nie mogą być wykorzystane na wysokim poziomie obciążenia SAP NetWeaver. W rezultacie systemowych baz danych programu SQL Server (master, msdb i modelu) może pozostać na tym dysku C:\. Wyjątek powinien być bazy danych tempdb, co w przypadku obciążeń SAP mogą wymagać większą ilość danych lub woluminu operacji We/Wy. Obciążenie We/Wy nie powinny być stosowane do wirtualnego dysku twardego systemu operacyjnego. W tych systemach można wykonać następujące czynności:


* Za pomocą wszystkich SAP certyfikowane typy maszyn wirtualnych (patrz Uwaga SAP [1928533]), z wyjątkiem można umieścić maszyny wirtualne serii A, bazy danych tempdb i plików dziennika na dysku D:\ nietrwałe. 
* Niemniej jednak zalecane jest używanie wielu plików danych bazy danych tempdb. Należy pamiętać, że woluminy dysku D:\ różnią się na podstawie typu maszyny Wirtualnej. Dokładne rozmiary na dysku D:\, różnych maszyn wirtualnych na ten temat można znaleźć w artykule [rozmiary dla Windows maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Te konfiguracje włączyć bazę danych tempdb zużywać więcej miejsca niż dysk systemowy jest w stanie zapewnić. Nietrwałe dysku D:\ oferuje również lepszą opóźnienia operacji We/Wy i przepływność (z wyjątkiem maszyny wirtualne z serii A). Aby określić rozmiar odpowiednie bazy danych tempdb, możesz sprawdzić rozmiary bazy danych tempdb na istniejących systemów. 

>[!NOTE]
> w przypadku, gdy mają zostać umieszczone pliki danych bazy danych tempdb i pliku dziennika do folderu na dysku D:\, który został utworzony, należy się upewnić, że folder istnieje po ponownym uruchomieniu maszyny Wirtualnej. Ponieważ na dysku D:\ świeżo jest inicjowana po ponowny rozruch maszyny Wirtualnej są wyczyszczone wszystkie struktury plików i katalogów. Możliwość odtworzyć struktur ostatecznej katalogów na dysku D:\, zanim uruchamiania usługi programu SQL Server są udokumentowane we [w tym artykule](https://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Konfiguracja maszyny Wirtualnej, na którym działa program SQL Server z bazą danych SAP i gdzie bazy danych tempdb i bazy danych tempdb w pliku dziennika są umieszczane na dysku D:\ będzie wyglądać:

![Diagram przedstawiający prostą Konfiguracja dysku maszyny Wirtualnej dla programu SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Na powyższym diagramie Wyświetla prostym przypadku. Jak omijane się w artykule [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md), liczba i rozmiar dysków usługi Premium Storage jest zależny od różnych czynników. Jednak ogólnie zaleca się:

- Przy użyciu funkcji miejsca do magazynowania w celu utworzenia jednej lub niewielkiej liczby woluminów, które zawierają pliki danych programu SQL Server. Dlatego opracowano ta konfiguracja zakłada, że w realnym liczne SAP baz danych z plikami o innej wielkości bazy danych z różnych obciążeniem operacji We/Wy.
- Aby dostarczyć wystarczającej liczby operacji We/Wy przy użyciu funkcji miejsca do magazynowania i pliku dziennika transakcji programu SQL Server. Potencjalne obciążenia operacji We/Wy często jest wiodącą linii zmiany rozmiaru woluminu dziennika transakcji i nie potencjalnych woluminu woluminu transakcji programu SQL Server
- Na użytek D:\drive bazy danych tempdb tak długo, jak wydajność jest wystarczająco dobre. Jeśli całkowitego obciążenia jest ograniczona w wydajności tmepdb znajdujących się na dysku D:\ może być konieczne należy wziąć pod uwagę, aby przenieść bazę danych tempdb na różnych dyskach magazynu w warstwie Premium, zgodnie z zaleceniami w [w tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Specjalne dla maszyn wirtualnych serii M
Dla maszyn wirtualnych serii M na platformie Azure można zmniejszyć opóźnienia zapisanie w dzienniku transakcji przez czynników, w porównaniu do wydajności usługi Azure Premium Storage, korzystając z akceleratorem zapisu platformy Azure. Dlatego należy wdrożyć akcelerator zapisu platformy Azure dla wirtualnymi dyskami twardymi, które tworzą wolumin dziennika transakcji programu SQL Server. Szczegółowe informacje mogą być odczytywane w dokumencie [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formatowanie dysków
Dla programu SQL Server rozmiar bloku systemu plików NTFS dla dysków zawierających pliki danych i dziennika programu SQL Server należy 64KB. Nie ma potrzeby do sformatowania dysku D:\. Ten dysk jest wstępnie sformatowane.

Aby upewnić się, że przywracania lub tworzenia baz danych jest nie zainicjował pliki danych poprzez wyzerowanie zawartości tych plików, należy upewnić się, że kontekst użytkownika, którego usługi SQL Server jest uruchomiona w ma określonych uprawnień. Zazwyczaj użytkownicy w grupie administratorów Windows mają te uprawnienia. Jeśli usługi SQL Server uruchamiany w kontekście użytkownika bez uprawnień administratora na Windows użytkownika, musisz przypisać ten użytkownik uprawnienie użytkownika **wykonywanie zadań konserwacji woluminów**.  Zobacz szczegółowe informacje w tym artykule bazy wiedzy firmy Microsoft: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Wpływ kompresja bazy danych
W konfiguracji, gdy przepustowość operacji We/Wy może stać się czynnikiem ograniczającym Każda miara, co zmniejsza operacje We/Wy może przyczynić się do rozproszonego obciążenia, jakie można jedno uruchomienie w scenariuszu IaaS, takich jak platforma Azure. W związku z tym jeśli nie zostały jeszcze zrobione, zastosowanie kompresji strony serwera SQL jest zalecane zarówno przez SAP i Microsoft przed przekazaniem istniejącej bazy danych SAP na platformie Azure.

Zalecane do wykonania kompresji bazy danych przed przekazaniem do usługi Azure znajduje się z dwóch powodów:

* Ilość danych do przekazania jest niższa.
* Czas trwania wykonania kompresji jest krótszy, przy założeniu, że jeden służy silniejsze sprzętu z więcej procesorów ani większą przepustowość operacji We/Wy lub mniej operacji We/Wy opóźnienia w środowisku lokalnym.
* Mniejsze rozmiary bazy danych może prowadzić do mniejsze koszty przydział dysku

Kompresja bazy danych działa również na maszynach wirtualnych platformy Azure, jak w środowisku lokalnym. Aby uzyskać więcej informacji na temat do skompresowania istniejące bazy danych SAP NetWeaver programu SQL Server, przejrzyj artykuł [narzędzia kompresji ulepszone SAP MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>Pliki programu SQL Server 2014 i nowszej — przechowywanie bazy danych bezpośrednio w usłudze Azure Blob Storage
SQL Server 2014 i nowszych wersjach open możliwości mają być przechowywane pliki bazy danych bezpośrednio na Store obiektów Blob platformy Azure bez "otokę" wokół nich wirtualnego dysku twardego. Szczególnie w przypadku przy użyciu standardowego magazynu platformy Azure lub mniejsze typy maszyn wirtualnych tego typu wdrożenia umożliwia realizację scenariuszy, gdzie pozwala uniknąć limitów operacje We/Wy, które będzie wymuszane przez ograniczoną liczbę dysków, które mogą być instalowane na niektórych typów mniejszych maszyny Wirtualnej. Dzięki temu wdrożenia działa w przypadku baz danych użytkownika, ale nie dla systemowych baz danych programu SQL Server. Działa dla plików danych i dziennika programu SQL Server. Jeśli chcesz wdrożyć bazę danych programu SQL Server SAP w ten sposób zamiast "zawijania" je do wirtualnych dysków twardych, należy wziąć pod uwagę:

* Konto magazynu używane musi znajdować się w tym samym regionie platformy Azure jako taki, który jest używany do wdrażania maszyn wirtualnych SQL Server jest uruchomiony w.
* Wymienione wcześniej dotyczące dystrybucji wirtualnych dysków twardych na różnych kontach magazynu Azure kwestie dla tej metody, a także wdrożeń. Oznacza, że liczba operacji We/Wy na wartości konta usługi Azure Storage.
* Zamiast rachunku względem limit przydziału operacji We/Wy magazynu maszyny Wirtualnej, ruch z magazynu obiektów blob, reprezentujący plików danych i dziennika programu SQL Server, będzie brana pod uwagę na przepustowość sieci maszyny Wirtualnej z określonego typu maszyny Wirtualnej. Dotyczące przepustowości sieci i magazynu konkretnego typu maszyn wirtualnych, zapoznaj się z artykułem [rozmiary dla Windows maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* W wyniku wypychanie we/wy przez limit przydziału sieci, są mieliźnie głównie przydziału miejsca do magazynowania i korzystając z niego korzystać tylko częściowo ogólną przepustowość maszyny Wirtualnej.
* Cele wydajności przepływności operacji We/Wy i operacje We/Wy z usługi Azure Premium Storage rozmiarów inny dysk nie mają już zastosowania. Nawet jeśli obiekty BLOB, który został utworzony znajdują się w usłudze Azure Premium Storage. Obiekty docelowe są udokumentowane artykuł [High-performance Premium Storage i dysków zarządzanych dla maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). W wyniku umieszczenia plików danych programu SQL Server i plików dziennika bezpośrednio na obiekty BLOB, które są przechowywane w usłudze Azure Premium Storage, charakterystyki wydajności mogą być różne w porównaniu do wirtualnych dysków twardych w usłudze Azure Premium Storage.
* Na podstawie buforowania hosta jako dostępnej do dysków usługi Premium Storage dla platformy Azure nie jest dostępna w przypadku umieszczenia plików danych programu SQL Server bezpośrednio na obiektach blob platformy Azure.
* Na maszynach wirtualnych serii M akcelerator zapisu platformy Azure nie może służyć do obsługi liczone w ułamkach milisekund zapisu pliku dziennika transakcji programu SQL Server. 

Szczegółowe informacje o tej funkcji można znaleźć w artykule [pliki danych programu SQL Server w systemie Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Dla systemów produkcyjnych zaleca się unikać tej konfiguracji, a raczej wybierz rozmieszczenia danych programu SQL Server i pliki dziennika w usłudze Azure Premium Storage wirtualne dyski twarde zamiast bezpośrednio na obiektach blob platformy Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Rozszerzenie puli buforów programu SQL Server 2014
Program SQL Server 2014 wprowadzono nową funkcję, która jest wywoływana [rozszerzenia puli buforów](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Ta funkcja rozszerzenie puli buforów dla programu SQL Server, który jest przechowywany w pamięci z pamięcią podręczną drugiego poziomu, która jest wspierana przez lokalny SSD serwera lub maszyny Wirtualnej. Rozszerzenie puli buforów umożliwia przechowywanie większy zestaw roboczy danych "w pamięci". W porównaniu do uzyskiwania dostępu do usługi Azure Standard Storage dostęp do rozszerzenia puli buforów, które są przechowywane na lokalnych dyskach SSD maszyny wirtualnej portalu Azure jest wiele czynników szybciej. Porównywanie rozszerzenia puli buforów do usługi Azure Premium Storage pamięci podręcznej odczytu, zgodnie z zaleceniami dla plików danych programu SQL Server, powinny żadnych znaczących korzyści dla rozszerzeń puli buforów. Przyczyną jest to, że oba pamięci podręcznych (rozszerzenie puli buforów programu SQL Server i pamięci podręcznej odczytu magazynu Premium) używają dysków lokalnych w węźle obliczeniowym platformy Azure.

Doświadczenia zebrane w tym samym czasie za pomocą rozszerzenia puli buforów programu SQL Server z obciążeniami SAP mieszanym i nadal nie zezwala na zalecenia wyczyść od tego, czy z niego korzystać we wszystkich przypadkach. Idealnym przypadku jest to, czy zestaw roboczy, który wymaga aplikacji SAP znajdzie się w pamięci głównej. Z oferty platformy Azure w tym samym czasie maszyn wirtualnych, które są dostarczane z maksymalnie 4 TB pamięci należy się osiągalny przechowywania zestaw roboczy w pamięci. Dlatego użycie rozszerzenia puli buforów jest ograniczona do rzadkich przypadkach i nie powinny być mainstream przypadek.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Informacje dotyczące tworzenia kopii zapasowej/odzyskiwania programu SQL Server
W przypadku wdrażania programu SQL Server na platformę Azure, muszą być przejrzane w swojej metodologii kopii zapasowej. Nawet, jeśli system nie jest w systemie produkcyjnym, bazy danych SAP, obsługiwanej przez program SQL Server musi być kopię zapasową okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, kopia zapasowa jest teraz mniej ważne w odniesieniu do wyrównującej awarii magazynu. Przyczyna priorytet utrzymywania właściwego planu tworzenia kopii zapasowych i odzyskiwania jest inne, które można kompensowane błędy logiczne/ręczne, zapewniając punktu w czasie możliwości odzyskiwania. Dlatego celem jest albo użyj kopii zapasowych, aby przywrócić bazę danych do pewnego momentu w czasie lub używać kopie zapasowe na platformie Azure, aby zapełnić innego systemu przez skopiowanie istniejącej bazy danych. 

W celu Spójrz na inny serwer SQL możliwości tworzenia kopii zapasowej na platformie Azure, przeczytaj artykuł [kopia zapasowa i przywracanie programu SQL Server w usłudze Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Artykuł obejmuje kilka różnych możliwości.

### <a name="manual-backups"></a>Ręczne tworzenie kopii zapasowych
Istnieje kilka możliwości do tworzenia kopii zapasowych "manual" według:

1. Wykonywanie konwencjonalne wykonywanie kopii zapasowych programu SQL Server na bezpośrednie dołączonych dysków platformy Azure. Ta metoda ma tę zaletę, że dostępnych szybko uzyskać odświeża systemu kopii zapasowych i utworzenia nowych systemów jako kopie z istniejącymi systemami SAP
2.  SQL Server 2012 CU4 i nowszej utworzyć kopię zapasową bazy danych pod adresem URL usługi Azure storage.
3.  Plik — tworzenie kopii zapasowych migawki plików bazy danych w usłudze Azure Blob Storage. Ta metoda działa tylko w przypadku plików danych i dziennika programu SQL Server znajdują się w usłudze Azure blob storage

Pierwsza metoda jest dobrze znany i stosowane w wielu przypadkach na świecie lokalnych. Niemniej jednak go spowoduje pozostawienie zadań do rozwiązywania dłuższy okres lokalizacji kopii zapasowej. Ponieważ nie chcesz przechowywać kopie zapasowe co najmniej 30 dni w podłączonych lokalnie usługi Azure Storage, masz trzeba użyć usługi tworzenia kopii zapasowych platformy Azure lub inne narzędzie kopii zapasowej/odzyskiwania innych firm, które obejmuje zarządzanie dostępu i przechowywania kopii zapasowych. Lub Utwórz się duży serwer plików na platformie Azure przy użyciu Windows funkcji miejsca do magazynowania.

Druga metoda jest bliżej opisaną w artykule [kopię zapasową serwera SQL do adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Różne wersje programu SQL Server ma pewne różnice w tej funkcji. Należy zatem, zapoznaj się z dokumentacją, dla konkretnego ewidencjonowanie wersji programu SQL Server. Należy zauważyć, że w tym artykule wymieniono wiele ograniczeń. Możesz mieć możliwość tworzenia kopii zapasowej przed:

- Jeden pojedynczy Azure stronicowych obiektów blob, który następnie ogranicza rozmiar kopii zapasowej do 1000 GB. Ogranicza to przepływność, którą można osiągnąć.
- Wiele (maksymalnie 64) platformy Azure obiekty BLOB typu block, umożliwiających teoretycznych kopii zapasowych o rozmiarze 12 TB. Jednak testy z bazy danych klientów wykazało, że maksymalny rozmiar kopii zapasowej może być mniejsza niż jej teoretycznego limitu. W takim przypadku możesz są odpowiedzialni za zarządzanie przechowywania kopii zapasowych oraz o dostęp do kopii zapasowych także.


### <a name="automated-backup-for-sql-server"></a>Zautomatyzowane tworzenie kopii zapasowej programu SQL Server
Zautomatyzowane tworzenie kopii zapasowej umożliwia automatyczne usługi kopii zapasowej, w przypadku wersji SQL Server Standard i Enterprise działające w maszyny Wirtualnej z systemem Windows na platformie Azure. Ta usługa jest świadczona przez [rozszerzenie agenta IaaS programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), który jest automatycznie instalowany na obrazy maszyn wirtualnych SQL, Windows Server w witrynie Azure portal. W przypadku wdrożenia obrazów systemu operacyjnego zainstalowanym programem SQL Server, musisz oddzielnie zainstalować rozszerzenia maszyny Wirtualnej. Kroki niezbędne są opisane w tym [artykułu](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Więcej szczegółów na temat możliwości tej metody można znaleźć w następujących artykułach:

- SQL Server 2014: [Zautomatyzowane tworzenie kopii zapasowej dla maszyn wirtualnych programu SQL Server 2014 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatyczne v2 kopii zapasowych maszyn wirtualnych platformy Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Wyszukiwanie w dokumentacji, możesz zobaczyć, że ulepszone funkcje z nowszej wersji programu SQL Server. Niektóre szczegółowe informacje na temat programu SQL Server, automatyczne kopie zapasowe są wydawane w artykule [zarządzane kopię zapasową serwera SQL w systemie Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Limit teoretycznych rozmiar kopii zapasowej jest 12 TB.  Automatyczne kopie zapasowe mogą być dobry sposób tworzenia kopii zapasowej rozmiary, do 12 TB. Ponieważ wiele obiektów blob są zapisywane w sposób równoległy, można oczekiwać, że większy niż 100 MB/s przepustowości. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Usługa Azure Backup dla maszyn wirtualnych programu SQL Server
Ta nowa metoda tworzenia kopii zapasowych programu SQL Server jest oferowana począwszy od czerwca 2018 roku jako publiczna wersja zapoznawcza przez usługi Azure Backup. Metoda do tworzenia kopii zapasowych programu SQL Server jest taki sam, jak inne narzędzia innych firm korzystają mianowicie interfejsu programu SQL Server VSS/VDI strumienia tworzenia kopii zapasowych do lokalizacji docelowej. W tym przypadku lokalizacja docelowa jest magazyn usługi odzyskiwania platformy Azure.

Więcej niż szczegółowy opis tej metody wykonywania kopii zapasowej, która dodaje wieloma korzyściami centralnej konfiguracji kopii zapasowej, monitorowania, i administracji jest dostępna [tutaj](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Rozwiązania tworzenia kopii zapasowych innych firm
Dość liczbę klienci systemu SAP nie było możliwości można rozpocząć od nowa i wprowadzają nowe kompletnych rozwiązań kopii zapasowych dla części ich środowiskiem SAP, w którym był uruchomiony na platformie Azure. W rezultacie istniejącego rozwiązania tworzenia kopii zapasowych potrzebne używanych i rozszerzone na platformę Azure. Rozszerzanie istniejącego rozwiązania tworzenia kopii zapasowych do platformy Azure, zazwyczaj dobrze pracowano większość dostawców głównego, w tym miejscu. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Przy użyciu obrazu programu SQL Server z witryny Microsoft Azure Marketplace
Firma Microsoft oferuje maszyn wirtualnych w portalu Azure Marketplace, które już zawierają wersje programu SQL Server. Klienci systemu SAP, którzy wymagają licencji programu SQL Server i Windows przy użyciu tych obrazów może być szansą na pokrycie potrzebę licencji, która umożliwia uruchomienie maszyn wirtualnych z już zainstalowanym programem SQL Server. Aby można było używać tych obrazów dla rozwiązania SAP, należy wprowadzić następujące kwestie:

* Wersje inne niż w wersji ewaluacyjnej programu SQL Server uzyskują wyższe koszty niż "Windows-only" maszyny Wirtualnej wdrożonej w portalu Azure Marketplace. Zobacz następujące artykuły, aby porównać ceny: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> i <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Można używać tylko wersji programu SQL Server, które są obsługiwane przez firmę SAP.
* Sortowania wystąpienia programu SQL Server, który jest instalowany na maszynach wirtualnych oferowanych w portalu Azure Marketplace nie jest sortowanie oprogramowania SAP NetWeaver wymaga wystąpienia programu SQL Server do uruchomienia. Możesz zmienić sortowanie, jednak z instrukcjami w poniższej sekcji.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Zmiana sortowania programu SQL Server z maszyną Wirtualną z Microsoft Windows/SQL Server
Ponieważ obrazy programu SQL Server w witrynie Azure Marketplace nie jest skonfigurowany do korzystania z sortowania, który jest wymagany przez aplikacje środowiska SAP NetWeaver, trzeba zmienić natychmiast po wdrożeniu. Dla programu SQL Server tej zmiany sortowania można wykonać za pomocą poniższe czynności natychmiast po wdrożeniu maszyny Wirtualnej i administrator będzie mógł zalogować się do wdrożonej maszyny Wirtualnej:

* Otwórz okno poleceń programu Windows jako administrator.
* Zmień katalog na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Wykonaj polecenie: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> to konto, która została zdefiniowana jako konto administratora, podczas wdrażania maszyny Wirtualnej po raz pierwszy przy użyciu galerii.

Proces powinien potrwać kilka minut. Aby upewnić się, czy krok zakończył przy użyciu prawidłowego wyniku, wykonaj następujące czynności:

* Otwórz program SQL Server Management Studio.
* Otwórz okno zapytania.
* Wykonaj sp_helpsort polecenia w bazie danych master programu SQL Server.

Oczekiwany wynik powinien wyglądać podobnie jak:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Jeśli wynik jest inny, Zatrzymaj wdrażanie SAP i zbadać, dlaczego polecenia Instalatora nie działał zgodnie z oczekiwaniami. Wdrożenie oprogramowania SAP NetWeaver aplikacje na wystąpieniu programu SQL Server przy użyciu innego programu SQL Server strony kodowe niż wymienione powyżej jest **nie** obsługiwane.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server o wysokiej dostępności dla oprogramowania SAP na platformie Azure
W przypadku wdrożeń infrastruktury IaaS platformy Azure dla rozwiązania SAP, korzystając z programu SQL Server, masz kilka różne możliwości, aby dodać do wdrożenia o wysokiej dostępności warstwy system DBMS. Zgodnie z opisem w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md) , system Azure udostępnia różne czasu umowy SLA dla pojedynczej maszyny Wirtualnej i dwóch maszyn wirtualnych wdrożonych w zestawie dostępności platformy Azure. Zakłada się dysk do czasu umowy SLA dla wdrożeń produkcyjnych wymaga wdrożenia w zestawach dostępności Azure. W takim przypadku należy wdrożyć co najmniej dwie maszyny wirtualne w tych zestawie dostępności. Jedna maszyna wirtualna zostanie uruchomiony aktywnego wystąpienia programu SQL Server. Maszyna wirtualna zostanie uruchomiony pasywne wystąpienie

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Klaster serwera SQL za pomocą serwera plików skalowalnego w poziomie Windows
Windows Server 2016, firma Microsoft wprowadziła [bezpośrednimi miejscami do magazynowania](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Oparte na wdrożenia funkcji bezpośrednie miejsca do magazynowania magazynu, klaster infrastruktury klasyfikacji plików programu SQL Server jest obsługiwany. Szczegóły można znaleźć w artykule [Konfigurowanie trybu Failover klastra wystąpienie programu SQL Server na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). Rozwiązanie wymaga modułu równoważenia obciążenia platformy Azure, aby poradzić sobie z wirtualnym adresem IP w zasobach klastra. Pliki bazy danych programu SQL Server są przechowywane w funkcji miejsca do magazynowania. To dlatego, biorąc pod uwagę, że możesz potrzebować do utworzenia magazynowania systemu Windows, w oparciu o usługi Azure Premium Storage. Ponieważ to rozwiązanie obsługiwana dla zbyt długo jeszcze nie istnieją nie znane klienci systemu SAP, którzy korzystają z tego rozwiązania w scenariuszach produkcyjnych SAP.  

### <a name="sql-server-log-shipping"></a>Wysyłanie dziennika programu SQL Server
Jest jedną z metod wysokiej dostępności (HA), aby wysyłanie dziennika serwera SQL. Jeśli działa rozpoznawanie nazw maszyn wirtualnych w konfiguracji wysokiej dostępności, problem nie występuje i konfiguracji na platformie Azure różnią się od wszelkich konfiguracji, która jest wykonywane w środowisku lokalnym. W odniesieniu do konfigurowania wysyłania dzienników i zasady dotyczące wysyłania dziennika. Szczegóły wysyłania dzienników serwera SQL można znaleźć w artykule [o wysyłanie dziennika (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Wysyłania funkcji dziennika programu SQL Server nie został użyty na platformie Azure do zapewniania wysokiej dostępności w obrębie jednego regionu platformy Azure. Jednak w następujących scenariuszach klienci systemu SAP były używane wysyłanie dziennika pomyślne w połączeniu z platformą Azure:

- Scenariusze odzyskiwania po awarii między regionami platformy Azure do innego regionu platformy Azure
- Konfiguracja odzyskiwania po awarii ze środowiska lokalnego do regionu platformy Azure
- Scenariusze przez wycinanie ze środowiska lokalnego do platformy Azure. W takich przypadkach wysyłania dziennika jest używane do synchronizacji nowego wdrożenia systemu DBMS na platformie Azure za pomocą bieżące produkcyjne systemu lokalnego. W momencie cuttinga za pośrednictwem produkcji zostanie zamknięta i jest upewnienie się, że kopie zapasowe dziennika transakcji ostatniego i najnowszych stało się przeniesione do wdrażania systemu DBMS na platformie Azure. Następnie wdrażania systemu DBMS na platformie Azure jest otwarta w środowisku produkcyjnym.  



### <a name="database-mirroring"></a>Funkcja dublowania baz danych
Funkcja dublowania bazy danych, zgodnie z obsługiwanych przez oprogramowanie SAP (patrz Uwaga SAP [965908]) opiera się na temat definiowania partnerski trybu failover w parametrach połączenia SAP. W przypadkach między środowiskami lokalnymi przyjęto założenie, że dwie maszyny wirtualne znajdują się w tej samej domenie i że wystąpień programu SQL Server w kontekście dwóch użytkowników są uruchomione przez użytkownika domeny i masz wystarczające uprawnienia w związane dwa wystąpienia programu SQL Server. W związku z konfiguracją funkcji dublowania baz danych na platformie Azure nie różnią się typowe lokalnej/w konfiguracji.

Począwszy od wdrożeń tylko w chmurze to najłatwiejsza metoda jest zapewnienie innej konfiguracji domeny na platformie Azure, te maszyny wirtualne systemu DBMS (i najlepiej dedykowanych SAP maszyn wirtualnych) w ramach jednej domeny.

Jeśli domena nie jest możliwe, jeden certyfikatów można także użyć dla bazy danych, dublowanie punktów końcowych, zgodnie z opisem w tym miejscu: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Samouczek do skonfigurowania funkcji dublowania baz danych na platformie Azure można znaleźć tutaj: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>Zawsze włączone programu SQL Server
Jak zawsze włączone jest obsługiwana dla SAP w środowisku lokalnym (patrz Uwaga SAP [1772688]), jest obsługiwana w połączeniu z oprogramowaniem SAP na platformie Azure. Istnieją pewne specjalne zagadnienia dotyczące praktyki wdrażania odbiornika grupy dostępności programu SQL Server (nie należy mylić z zestawu dostępności platformy Azure), ponieważ platforma Azure w tym momencie nie zezwala na tworzenie obiektu AD/DNS, ponieważ jest to możliwe w środowisku lokalnym. Dlatego niektóre kroki instalacji różnych są niezbędne w celu wyeliminowania określone zachowanie platformy Azure.

Niektóre kwestie, przy użyciu odbiornika grupy dostępności są:

* Przy użyciu odbiornika grupy dostępności jest możliwe tylko w systemie Windows Server 2012 lub nowszego jako systemu operacyjnego gościa maszyny wirtualnej. Dla systemu Windows Server 2012 należy się upewnić, że ta poprawka jest stosowana: <https://support.microsoft.com/kb/2854082> 
* Ta poprawka dla systemu Windows Server 2008 R2, nie istnieje. i zawsze musi można używać w taki sam sposób jak dublowania bazy danych, określając partnerski trybu failover w parametrach połączenia (zrobić za pomocą default.pfl parametru baz danych/mss/serwerem SAP — patrz Uwaga SAP [965908]).
* Przy użyciu odbiornika grupy dostępności, maszyny wirtualne bazy danych muszą być podłączone do dedykowanych modułu równoważenia obciążenia. W celu uniknięcia, czy Azure przypisuje nowe adresy IP w przypadkach, gdy obie maszyny wirtualne przy okazji są zamykane, jeden należy przypisać statyczne adresy IP interfejsów sieci maszyn wirtualnych w konfiguracji Always On (Definiowanie statyczny adres IP jest opisane w [to] [ virtual-networks-reserved-private-ip] artykułu)
* Istnieją specjalne kroki wymagane w przypadku tworzenia konfiguracji klastra usługi WSFC, gdy klaster musi specjalny adres IP przypisany, ponieważ platformę Azure za pomocą jej bieżącą funkcjonalność przypisywanej nazwy klastra ten sam adres IP jako węzeł klastra jest tworzony na. Oznacza to, że czynność ręczna należy wykonać, aby przypisać inny adres IP do klastra.
* Odbiornik grupy dostępności, będzie można utworzyć na platformie Azure za pomocą punktów końcowych protokołu TCP/IP, które są przypisane do maszyn wirtualnych z systemem podstawowych i pomocniczych replik grupy dostępności.
* Być może trzeba zabezpieczyć tych punktów końcowych przy użyciu list kontroli dostępu.

Szczegółowa dokumentacja na temat wdrażania zawsze włączone programu SQL Server w maszynach wirtualnych platformy Azure zawiera listę takich jak:

- [Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Konfigurowanie zawsze włączonej grupy dostępności na maszynach wirtualnych platformy Azure w różnych regionach](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Konfigurowanie modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> W przypadku konfigurowania równoważenia obciążenia platformy Azure dla wirtualnego adresu IP odbiornika grupy dostępności, upewnij się, że skonfigurowano DirectServerReturn. skonfigurowanie tej opcji spowoduje obniżenie sieci, które \średni czas opóźnienia między warstwą aplikacji SAP i warstwy system DBMS Rundy. 

SQL Server Always On jest najczęściej używanych wysokiej dostępności i działania funkcji odzyskiwania po awarii umożliwiający wdrożeń obciążeń SAP na platformie Azure. Większość klientów używa Always On w celu zapewnienia wysokiej dostępności w obrębie jednego regionu platformy Azure. Jeśli wdrożenie jest ograniczony do tylko dwóch węzłów, dostępne są dwie opcje łączności:

- Przy użyciu odbiornika grupy dostępności. Przy użyciu odbiornika grupy dostępności są wymagane do wdrożenia usługi Azure load balancer. Zazwyczaj jest domyślną metodą wdrażania. Aplikacje SAP będzie można skonfigurować do łączenia z odbiornikiem grupy dostępności, a nie dla pojedynczego węzła
- Przy użyciu parametrów połączenia dublowania bazy danych serwera SQL. W takim przypadku należy skonfigurować łączność aplikacje SAP w taki sposób, w którym noszą nazwy obu węzłów. Szczegółowymi informacjami na temat takiej konfiguracji po stronie SAP jest udokumentowany w Uwaga SAP [#965908](https://launchpad.support.sap.com/#/notes/965908). Za pomocą tej opcji, trzeba nie trzeba konfigurować odbiornika grupy dostępności. A korzystając z niego nie Azure równoważenia obciążenia do programu SQL Server o wysokiej dostępności. W rezultacie opóźnienie sieci między warstwą aplikacji SAP i warstwy system DBMS jest mniejszy, ponieważ ruch przychodzący do wystąpienia programu SQL Server nie jest kierowany za pośrednictwem modułu równoważenia obciążenia platformy Azure. Ale pamiętaj o tej opcji tylko wtedy, gdy ograniczenia grupy dostępności na dwa wystąpienia. 

Sporo klientów korzysta z funkcji programu SQL Server Always On do działania funkcji odzyskiwania po awarii dodatkowe między regionami platformy Azure. Wielu klientów również użyć zdolność do tworzenia kopii zapasowych w replice pomocniczej. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server przezroczyste szyfrowanie danych
Liczba klientów, którzy korzystają z programu SQL Server jest [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) podczas wdrażania programu SQL Server ich SAP baz danych na platformie Azure. Funkcja TDE serwera SQL jest w pełni obsługiwany przez firmę SAP (patrz Uwaga SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Zastosowanie serwera SQL TDE
W przypadkach, w którym heterogenicznych migracja jest przeprowadzana z innego systemu DBMS, systemem Windows/programu SQL Server uruchomionego na platformie Azure w środowisku lokalnym, należy utworzyć pusty docelowej bazy danych w programie SQL Server wcześniej. Następnym krokiem będzie zastosowanie funkcji TDE serwera SQL. Chociaż nadal działają usługi produkcji systemu lokalnego. Powodów, dla którego ma zostać wykonana w tej sekwencji jest, że proces szyfrowania pustej bazy danych może potrwać bardzo długo. Procesów importu SAP będzie następnie zaimportować dane do szyfrowanej bazy danych podczas fazy przestojów. Koszty związane z importowaniem do szyfrowanej bazy danych ma sposób mniejszy wpływ na czas niż szyfrowanie bazy danych po fazie eksportu w dół fazy czasu. Ujemna środowisk, w przypadku podczas próby zastosowania funkcji TDE z obciążenia SAP, na podstawie bazy danych. W związku z tym zalecenie jest traktowanie wdrożenie funkcji TDE jako działanie, które należy wykonać bez obciążenia SAP na konkretnej bazy danych.

W przypadkach, w którym przeniesiesz bazy danych programu SQL Server SAP ze środowiska lokalnego na platformę Azure zaleca się testowanie na infrastrukturę, których można uzyskać zastosowano najszybszych szyfrowanie. W tym pamiętać następujące fakty:

- Nie można zdefiniować, jak wiele wątków umożliwiają zastosowanie szyfrowania danych w bazie danych. Liczba wątków majorly zależy od liczby woluminy na dyskach, które są rozprowadzone plików danych i dziennika programu SQL Server. Oznacza, że woluminy znacznie (litery dysku), więcej wątków będzie zaangażowane w sposób równoległy, przeprowadzić szyfrowanie. Taka konfiguracja jest nieco sprzeczne z wcześniejszej sugestii konfiguracji dysku na tworzeniu co najmniej mniejszą liczbę funkcji miejsca do magazynowania plików bazy danych programu SQL Server na maszynach wirtualnych Azure. Konfiguracji z mniejszą liczbą woluminów doprowadziłoby do niewielkiej liczby wątków wykonania szyfrowania. Szyfrowanie pojedynczego wątku jest odczytywanie zakresów 64KB, szyfruje je i następnie zapisywać rekord w pliku dziennika transakcji, informuje, że zakres został zaszyfrowany. W efekcie obciążenie dziennik transakcji jest umiarkowane.
- W starszych wersjach programu SQL Server kompresja kopii zapasowej nie pobrały wydajność, już, podczas szyfrowania bazy danych programu SQL Server. To zachowanie można opracować wystąpił problem podczas planu szyfrowania programu SQL Server database i lokalną, a następnie skopiować kopii zapasowej na platformie Azure, aby przywrócić bazę danych na platformie Azure. Kompresja kopii zapasowych programu SQL Server zwykle uzyskuje współczynnik 4 stopień kompresji.
- Za pomocą programu SQL Server 2016 SQL Server wprowadził nową funkcję, która umożliwia także kompresowanie szyfrowanymi bazami danych w sposób efektywny. Zobacz [to blogi](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) niektóre szczegółowe informacje.
 
Traktowanie zastosowanie szyfrowania funkcji TDE bez niewielkie obciążenia SAP, należy przetestować w określonej konfiguracji czy jest lepsze, dotyczą funkcji TDE SAP bazy danych lokalnych lub aby to zrobić na platformie Azure. Na platformie Azure bez obaw masz większą elastyczność w zakresie infrastruktury nadmiernej aprowizacji i zmniejszyć infrastruktury po zastosowaniu stało się funkcji TDE.

### <a name="using-azure-key-vault"></a>Za pomocą usługi Azure Key Vault
Platforma Azure oferuje usługa [usługi Key Vault](https://azure.microsoft.com/services/key-vault/) do przechowywania kluczy szyfrowania. Program SQL Server na drugiej stronie oferują łącznik, aby korzystać z usługi Azure Key Vault jako magazyn certyfikatów funkcji TDE.

Więcej szczegółów, aby używać usługi Azure Key Vault dla funkcji TDE serwera SQL zawiera listę takich jak:

- [Rozszerzalne zarządzanie kluczami za pomocą usługi Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server TDE Rozszerzalne zarządzanie kluczami za pomocą usługi Azure Key Vault — kroki instalacji](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server Connector Konserwacja i rozwiązywanie problemów z](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Więcej pytania klientów dotyczące programu SQL Server przezroczyste szyfrowanie danych — TDE + usługi Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Za pomocą funkcji TDE serwera SQL, szczególnie przy użyciu platformy Azure key Vault, zaleca się używać najnowszych poprawek programu SQL Server 2014, SQL Server 2016 i programu SQL Server 2017. Przyczyna jest to, że na podstawie opinii klientów, optymalizacji i poprawki stało się zastosowane do kodu. Na przykład sprawdzić [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Ogólne programu SQL Server dla SAP w systemie Azure podsumowanie
Istnieje wiele zaleceń w tym przewodniku, a zalecane jest przeczytanie on więcej niż jeden raz przed rozpoczęciem Planowanie wdrożenia usługi Azure. Ogólnie rzecz biorąc, należy postępować zgodnie najważniejsze ogólne systemu DBMS na zalecenia specyficzne dla platformy Azure:

1. Użyj najnowszej wersji systemu DBMS, takich jak SQL Server 2017, który ma większość korzyści na platformie Azure. 
2. Należy dokładnie zaplanować środowiska systemu SAP na platformie Azure równoważenia układu plików danych i ograniczeń platformy Azure:
   * Nie ma zbyt wiele dysków, ale wystarczająco dużo, aby upewnić się, że możesz połączyć swoje wymagane operacje We/Wy.
   * Jeśli nie używasz dysków Managed Disks, należy pamiętać, że operacje We/Wy, również są ograniczone na konto usługi Azure Storage i, konta magazynów znajdują się w ramach każdej subskrypcji platformy Azure ([szczegółowe][azure-subscription-service-limits]). 
   * Tylko rozłożonej na dyskach, jeśli potrzebujesz do uzyskania większej przepływności.
3. Nigdy nie instaluj oprogramowania lub umieścić wszystkie pliki, które wymaga trwałości na dysku D:\, jest inne niż stałe i wszystko na tym dysku zostaną utracone w przypadku ponownego uruchomienia systemu Windows.
4. Nie należy używać buforowania dysku dla usługi Azure Standard Storage.
5. Nie używaj replikowanej geograficznie Azure standardowego konta usługi Azure Storage.  Użyj lokalnie nadmiarowy na potrzeby obciążeń systemu DBMS.
6. Za pomocą rozwiązania wysokiej dostępności i odzyskiwania po awarii z dostawcą systemu DBMS replikować dane z bazy danych.
7. Zawsze używaj rozpoznawania nazw, nie należy polegać na adresy IP.
8. Przy użyciu programu SQL Server TDE, zastosować najnowszych poprawek programu SQL Server.
9. Użyj najwyższy możliwe kompresja bazy danych. Jest to strona kompresji programu SQL Server.
10. Zachowaj ostrożność przy użyciu obrazów programu SQL Server w witrynie Azure Marketplace. Jeśli używasz programu SQL Server, jeden, należy zmienić sortowania wystąpienia przed zainstalowaniem dowolnego systemu SAP NetWeaver na nim.
11. Instalowanie i konfigurowanie monitorowania hosta SAP na platformie Azure, zgodnie z opisem w [przewodnik wdrażania][deployment-guide].
