---
title: Wdrożenie systemu DBMS maszyn wirtualnych oracle Azure dla obciążenia SAP | Dokumenty firmy Microsoft
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a23fb981e24f6152d99b76bd72115f8159f5d60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645848"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
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


Ten dokument obejmuje kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania bazy danych Oracle Database dla obciążenia SAP w usłudze Azure IaaS. Przed przeczytaniem tego dokumentu zaleca się [zapoznanie się z zagadnieniami dotyczących wdrażania dbms maszyn wirtualnych platformy Azure dla obciążenia SAP.](dbms_guide_general.md) Zaleca się również zapoznanie się z innymi przewodnikami w [pracy SAP w dokumentacji platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 

Informacje na temat wersji Oracle i odpowiednich wersji systemu operacyjnego, które są obsługiwane w przypadku uruchamiania systemu SAP na platformie Oracle na platformie Azure, można znaleźć w programie SAP Note [2039619.]

Ogólne informacje na temat uruchamiania pakietu SAP Business Suite w oracle można znaleźć w [sap na Oracle](https://www.sap.com/community/topic/oracle.html).
Oprogramowanie Oracle jest obsługiwane przez oracle do uruchamiania na platformie Microsoft Azure. Aby uzyskać więcej informacji na temat ogólnej pomocy technicznej dla systemu Windows Hyper-V i platformy Azure, zapoznaj się z [często zadawanymi pytaniami dotyczącymi oracle i microsoft azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Uwagi SAP istotne dla oracle, SAP i platformy Azure 

Następujące uwagi SAP są związane z SAP na platformie Azure.

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |SAP na platformie Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z rozszerzonym monitorowaniem platformy Azure dla systemu SAP |
| [2178632] |Kluczowe metryki monitorowania sap na platformie Microsoft Azure |
| [2191498] |SAP na Linuksie z platformą Azure: Ulepszone monitorowanie |
| [2039619] |Aplikacje SAP na platformie Microsoft Azure przy użyciu bazy danych Oracle: Obsługiwane produkty i wersje |
| [2243692] |Maszyna wirtualna systemu Linux na platformie Microsoft Azure (IaaS): problemy z licencją SAP |
| [2069760] |Instalacja i uaktualnienie systemu Sap oracle Linux 7.x |
| [1597355] |Zalecenie dotyczące przestrzeni wymiany dla systemu Linux |
| [2171857] |Oracle Database 12c - obsługa systemu plików w systemie Linux |
| [1114181] |Oracle Database 11g - obsługa systemu plików w systemie Linux |

Dokładne konfiguracje i funkcje obsługiwane przez oracle i SAP na platformie Azure są udokumentowane w sap Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows i Oracle Linux są jedynymi systemami operacyjnymi obsługiwanymi przez Oracle i SAP na platformie Azure. Powszechnie używane dystrybucje SLES i RHEL Linux nie są obsługiwane do wdrażania składników Oracle na platformie Azure. Składniki Oracle obejmują klienta bazy danych Oracle Database, który jest używany przez aplikacje SAP do łączenia się z Oracle DBMS. 

Wyjątki, zgodnie z SAP Note [#2039619,](https://launchpad.support.sap.com/#/notes/2039619)są składnikami SAP, które nie używają klienta bazy danych Oracle Database. Takie składniki SAP to autonomiczna kolejka elektroniczna SAP, serwer komunikatów, usługi replikacji enqueue, WebDispatcher i SAP Gateway.  

Nawet jeśli korzystasz z wystąpień aplikacji Oracle DBMS i SAP w systemie Oracle Linux, możesz uruchomić usługi SAP Central Services w SLES lub RHEL i chronić je za pomocą klastra opartego na rozruszniku serca. Rozrusznik serca jako struktura o wysokiej dostępności nie jest obsługiwana w systemie Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Szczegółowe dane dotyczące bazy danych Oracle Database w systemie Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Wskazówki dotyczące konfiguracji rozwiązania Oracle dla instalacji SAP na maszynach wirtualnych platformy Azure w systemie Windows

Zgodnie z instrukcją instalacji SAP pliki związane z Oracle nie powinny być instalowane ani znajdować się w sterowniku systemu dla dysku systemu operacyjnego maszyny Wirtualnej (dysk c:). Maszyny wirtualne o różnych rozmiarach mogą obsługiwać różną liczbę dołączonych dysków. Mniejsze typy maszyn wirtualnych mogą obsługiwać mniejszą liczbę dołączonych dysków. 

Jeśli masz mniejsze maszyny wirtualne, zalecamy zainstalowanie/zlokalizowanie oracle home, stage, "saptrace", "saparch", "sapbackup", "sapcheck" lub "sapreorg" na dysku systemu operacyjnego. Te części składników Oracle DBMS nie są intensywne w przepływocie we/wy i we/wy. Oznacza to, że dysk systemu operacyjnego może obsługiwać wymagania we/wy. Domyślny rozmiar dysku systemu operacyjnego to 127 GB. 

Jeśli nie ma wystarczającej ilości wolnego miejsca, można [zmieścić jego liczbę do](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) 2048 GB. Oracle Database i ponowić pliki dziennika muszą być przechowywane na oddzielnych dyskach z danymi. Istnieje wyjątek dla tymczasowego obszaru tabel Oracle. Pliki tempfiles można tworzyć na D:/ (dysk nietrwały). Nietrwałe D:\ dysk oferuje również lepsze opóźnienie we/wy i przepustowość (z wyjątkiem maszyn wirtualnych serii A). 

Aby określić odpowiednią ilość miejsca dla plików tymczasowych, można sprawdzić rozmiary plików tempfiles w istniejących systemach.

### <a name="storage-configuration"></a>Konfiguracja usługi Storage
Obsługiwane są tylko pojedyncze wystąpienia Oracle korzystające z dysków sformatowanych w systemie NTFS. Wszystkie pliki bazy danych muszą być przechowywane w systemie plików NTFS na dyskach zarządzanych (zalecane) lub na dyskach VHD. Te dyski są zainstalowane na maszynie Wirtualnej platformy Azure i są oparte na [magazynie obiektów blob strony platformy Azure](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) lub [dyskach zarządzanych platformy Azure.](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview) 

Zdecydowanie zalecamy korzystanie z [dysków zarządzanych platformy Azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). Zdecydowanie zalecamy również używanie [najwyższej jakości dysku SSD](../../windows/disks-types.md) do wdrożeń bazy danych Oracle Database.

Dyski sieciowe lub udziały zdalne, takie jak usługi plików platformy Azure, nie są obsługiwane w przypadku plików bazy danych Oracle Database. Aby uzyskać więcej informacji, zobacz:

- [Introducing Microsoft Azure File Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Wprowadzenie do usługi plików platformy Microsoft Azure)

- [Persisting connections to Microsoft Azure Files (Utrwalanie połączeń z plikami na platformie Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Jeśli używasz dysków, które są oparte na magazynie obiektów blob strony platformy Azure lub dysków zarządzanych, instrukcje w [rozważaniach dotyczących wdrożenia DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md) dotyczą również wdrożeń z bazą danych Oracle Database.

Istnieją przydziały przepływności usług We/Wy dla dysków platformy Azure. Ta koncepcja jest wyjaśniona w [zagadnieniach dotyczących wdrażania dbms maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md). Dokładne przydziały zależą od typu maszyny Wirtualnej, którego używasz. Listę typów maszyn wirtualnych z ich przydziałami można znaleźć w [rozmiarach maszyn wirtualnych systemu Windows na platformie Azure][virtual-machines-sizes-windows].

Aby zidentyfikować obsługiwane typy maszyn wirtualnych platformy Azure, zobacz SAP Note [1928533].

Minimalna konfiguracja jest następująca: 

| Składnik | Dysk | Buforowanie | Pula magazynu |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Premium | Brak | Nie jest wymagany |
| \oracle\<SID>\origlogaB & mirrlogA | Premium | Brak | Nie jest wymagany |
| \wyrocznia\<SID>\sapdata1... N | Premium | Tylko odczyt | Może być używany |
| \wyrocznia\<SID>\oraarch | Standardowa | Brak | Nie jest wymagany |
| Oracle Home, saptrace, ... | Dysk systemu operacyjnego | | Nie jest wymagany |


Wybór dysków do hostingu dzienników ponawienie online powinien być oparty na wymaganiach IOP. Możliwe jest przechowywanie wszystkich sapdata1... n (tablespaces) na jednym zamontowanym dysku, o ile rozmiar, IOPS i przepustowość spełniają wymagania. 

Konfiguracja wydajności jest następująca:

| Składnik | Dysk | Buforowanie | Pula magazynu |
| --- | ---| --- | --- |
| \wyrocznia\<SID>\origlogaA | Premium | Brak | Może być używany  |
| \wyrocznia\<SID>\origlogaB | Premium | Brak | Może być używany |
| \wyrocznia\<SID>\mirrlogAB | Premium | Brak | Może być używany |
| \wyrocznia\<SID>\mirrlogBA | Premium | Brak | Może być używany |
| \wyrocznia\<SID>\sapdata1... N | Premium | Tylko odczyt | Zalecane  |
| \oracle\SID\sapdata(n+1)* | Premium | Brak | Może być używany |
| \wyrocznia\<SID>\oraarch* | Premium | Brak | Nie jest wymagany |
| Oracle Home, saptrace, ... | Dysk systemu operacyjnego | Nie jest wymagany |

*(n+1): hostowanie obszarów tabel SYSTEM, TEMP i COFNIJ. Wzorzec we/wy obszarów tabel System i Cofnij różnią się od innych obszarów tabel obsługujących dane aplikacji. Brak buforowania jest najlepszym rozwiązaniem dla wydajności przestrzeni tabel System i Cofnij.

*oraarch: pula pamięci nie jest konieczna z punktu widzenia wydajności. Może być używany, aby uzyskać więcej miejsca.

Jeśli wymagana jest większa ilość we/wy, zaleca się utworzenie jednego dużego urządzenia logicznego na wielu zainstalowanych dyskach przy użyciu pul magazynu systemu Windows (dostępnych tylko w systemie Windows Server 2012 i nowszych). Takie podejście upraszcza obciążenie administracyjne związane z zarządzaniem miejscem na dysku i pomaga uniknąć wysiłku ręcznego rozmieszczania plików na wielu dyskach zainstalowanych.


#### <a name="write-accelerator"></a>Akcelerator zapisu
W przypadku maszyn wirtualnych z serii M platformy Azure opóźnienie zapisu w dziennikach ponawiania online można zmniejszyć o czynniki w porównaniu z usługą Azure Premium Storage. Włącz akceleratora zapisu azure dla dysków (VHD) na podstawie usługi Azure Premium Storage, które są używane do plików dziennika ponownego składania zapisu w trybie online. Aby uzyskać więcej informacji, zobacz [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Tworzenie kopii zapasowych/przywracanie
W przypadku funkcji tworzenia kopii zapasowych/przywracania narzędzia SAP BR*Tools for Oracle są obsługiwane w taki sam sposób, jak w standardowych systemach operacyjnych Windows Server. Oracle Recovery Manager (RMAN) jest również obsługiwany w przypadku tworzenia kopii zapasowych na dysku i przywracanych z dysku.

Można również użyć usługi Azure Backup do uruchomienia kopii zapasowej maszyny wirtualnej spójne aplikacji. W [artykule Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej na platformie Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) wyjaśnia, jak usługa Azure Backup używa funkcji usługi Windows VSS do wykonywania kopii zapasowych spójnych z aplikacjami. Wersje Oracle DBMS, które są obsługiwane na platformie Azure przez SAP można wykorzystać funkcje usługi VSS do tworzenia kopii zapasowych. Aby uzyskać więcej informacji, zobacz dokumentację Oracle [Podstawowe pojęcia tworzenia kopii zapasowych i odzyskiwania bazy danych za pomocą usługi VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Wysoka dostępność
Oracle Data Guard jest obsługiwany w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii. Aby uzyskać automatyczną pracę awaryjną w funkcji Data Guard, należy użyć funkcji Szybkiego uruchamiania w trybie failover (FSFA). Obserwator (FSFA) wyzwala pracy awaryjnej. Jeśli nie używasz FSFA, można użyć tylko ręcznej konfiguracji trybu failover.

Aby uzyskać więcej informacji na temat odzyskiwania po awarii dla baz danych Oracle na platformie Azure, zobacz [Odzyskiwanie po awarii dla bazy danych Oracle Database 12c w środowisku platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)

### <a name="accelerated-networking"></a>Wydajniejsze sieci
W przypadku wdrożeń Oracle w systemie Windows zdecydowanie zalecamy przyspieszone tworzenie sieci zgodnie z opisem w [obszarze Przyspieszona sieć platformy Azure.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Należy również wziąć pod uwagę zalecenia, które są zawarte w [zagadnieniach dotyczących wdrażania DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP.](dbms_guide_general.md) 
### <a name="other"></a>Inne
[Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md) opisują inne ważne pojęcia związane z wdrożeniem maszyn wirtualnych z bazą danych Oracle Database, w tym zestawami dostępności platformy Azure i monitorowaniem SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Szczegóły dotyczące bazy danych Oracle Database w systemie Oracle Linux
Oprogramowanie Oracle jest obsługiwane przez Oracle do uruchamiania na platformie Microsoft Azure z systemem Oracle Linux jako systemem operacyjnym gościa. Aby uzyskać więcej informacji na temat ogólnej pomocy technicznej dla systemu Windows Hyper-V i platformy Azure, zobacz [często zadawane pytania dotyczące platformy Azure i Oracle](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Obsługiwany jest również konkretny scenariusz aplikacji SAP wykorzystujących bazy danych Oracle Databases. Szczegóły są omówione w następnej części dokumentu.

### <a name="oracle-version-support"></a>Obsługa wersji Oracle
Aby uzyskać informacje o wersjach Oracle i odpowiednich wersjach systemu operacyjnego obsługiwanych w przypadku uruchamiania rozwiązania SAP na platformie Oracle na maszynach wirtualnych platformy Azure, zobacz uwaga SAP [2039619].

Ogólne informacje na temat uruchamiania pakietu SAP Business Suite w oracle można znaleźć w [sap na stronie społeczności Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Wskazówki dotyczące konfiguracji rozwiązania Oracle dla instalacji SAP na maszynach wirtualnych platformy Azure w systemie Linux

Zgodnie z podręcznikami instalacji SAP pliki związane z Oracle nie powinny być instalowane ani umieszczone w sterownikach systemowych dysku rozruchowego maszyny Wirtualnej. Różne rozmiary maszyn wirtualnych obsługują różną liczbę dołączonych dysków. Mniejsze typy maszyn wirtualnych mogą obsługiwać mniejszą liczbę dołączonych dysków. 

W takim przypadku zalecamy zainstalowanie/zlokalizowanie oracle home, stage, saptrace, saparch, sapbackup, sapcheck lub sapreorg do rozruchu dysku. Te części składników Oracle DBMS nie są intensywne w przepływocie we/wy i we/wy. Oznacza to, że dysk systemu operacyjnego może obsługiwać wymagania we/wy. Domyślny rozmiar dysku systemu operacyjnego to 30 GB. Dysk rozruchowy można rozwinąć przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia. Po rozwinięciu dysku rozruchowego można dodać dodatkową partycję dla plików binarnych Oracle.


### <a name="storage-configuration"></a>Konfiguracja usługi Storage

Systemy plików ext4, xfs lub Oracle ASM są obsługiwane dla plików bazy danych Oracle database na platformie Azure. Wszystkie pliki bazy danych muszą być przechowywane w tych systemach plików opartych na dyskach VHD lub managed disks. Te dyski są zainstalowane na maszynie Wirtualnej platformy Azure i są oparte na [magazynie obiektów blob strony platformy Azure](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) lub [dyskach zarządzanych platformy Azure.](../../windows/managed-disks-overview.md)

W przypadku jąder UEK systemu Oracle Linux do obsługi [najwyższej jakości identyfikatorów SSD platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching)wymagane jest minimum UEK w wersji 4.

Zdecydowanie zaleca się używanie [dysków zarządzanych platformy Azure.](../../windows/managed-disks-overview.md) Zaleca się również używanie [najwyższej jakości dysku SSD platformy Azure](../../windows/disks-types.md) do wdrożeń bazy danych Oracle Database.

Dyski sieciowe lub udziały zdalne, takie jak usługi plików platformy Azure, nie są obsługiwane w przypadku plików bazy danych Oracle Database. Aby uzyskać więcej informacji, zobacz następujące tematy: 

- [Introducing Microsoft Azure File Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Wprowadzenie do usługi plików platformy Microsoft Azure)

- [Persisting connections to Microsoft Azure Files (Utrwalanie połączeń z plikami na platformie Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Jeśli używasz dysków opartych na magazynie obiektów blob strony platformy Azure lub dyskach zarządzanych, instrukcje zawarte w [uwagach dotyczących wdrażania DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md) dotyczą również wdrożeń z bazą danych Oracle Database.

 Istnieją przydziały przepływności usług We/Wy dla dysków platformy Azure. Ta koncepcja jest wyjaśniona w [zagadnieniach dotyczących wdrażania dbms maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md). Dokładne przydziały zależą od typu maszyny Wirtualnej, który jest używany. Aby uzyskać listę typów maszyn wirtualnych z ich przydziałami, zobacz [Rozmiary maszyn wirtualnych systemu Linux na platformie Azure][virtual-machines-sizes-linux].

Aby zidentyfikować obsługiwane typy maszyn wirtualnych platformy Azure, zobacz SAP Note [1928533].

Minimalna konfiguracja:

| Składnik | Dysk | Buforowanie | Rozbiórka* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA & mirrlogB | Premium | Brak | Nie jest wymagany |
| /oracle/\<SID>/origlogaB & mirrlogA | Premium | Brak | Nie jest wymagany |
| /oracle/\<SID>/sapdata1... N | Premium | Tylko odczyt | Może być używany |
| /oracle/\<SID>/oraarch | Standardowa | Brak | Nie jest wymagany |
| Oracle Home, saptrace, ... | Dysk systemu operacyjnego | | Nie jest wymagany |

* Rozbiórka: pasek LVM lub MDADM za pomocą RAID0

Wybór dysku do obsługi dzienników online Oracle powinien być oparty na wymaganiach IOPS. Możliwe jest przechowywanie wszystkich sapdata1... n (tablespaces) na jednym dysku zamontowanym, o ile wolumin, IOPS i przepustowość spełniają wymagania. 

Konfiguracja wydajności:

| Składnik | Dysk | Buforowanie | Rozbiórka* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Premium | Brak | Może być używany  |
| /oracle/\<SID>/origlogaB | Premium | Brak | Może być używany |
| /oracle/\<SID>/mirrlogAB | Premium | Brak | Może być używany |
| /oracle/\<SID>/mirrlogBA | Premium | Brak | Może być używany |
| /oracle/\<SID>/sapdata1... N | Premium | Tylko odczyt | Zalecane  |
| /oracle/\<SID>/sapdata(n+1)* | Premium | Brak | Może być używany |
| /oracle/\<SID>/oraarch* | Premium | Brak | Nie jest wymagany |
| Oracle Home, saptrace, ... | Dysk systemu operacyjnego | Nie jest wymagany |

* Rozbiórka: pasek LVM lub MDADM za pomocą RAID0

*(n+1): hostowanie obszarów tabel SYSTEM, TEMP i COF: Wzorzec we/wy obszarów tabel System i Cofnij różnią się od innych obszarów tabel obsługujących dane aplikacji. Brak buforowania jest najlepszym rozwiązaniem dla wydajności przestrzeni tabel System i Cofnij.

*oraarch: pula pamięci nie jest konieczna z punktu widzenia wydajności.


Jeśli wymagana jest większa liczba funkcji We/Wy, zalecamy utworzenie jednego dużego woluminu logicznego na wielu dyskach zainstalowanych za pomocą LVM (Logical Volume Manager) lub MDADM. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące wdrażania dbms maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md) dotyczące wskazówek i wskaźników dotyczących sposobu korzystania z LVM lub MDADM. Takie podejście upraszcza obciążenie administracyjne związane z zarządzaniem miejscem na dysku i pomaga uniknąć wysiłku ręcznego rozmieszczania plików na wielu dyskach zainstalowanych.


#### <a name="write-accelerator"></a>Akcelerator zapisu
W przypadku maszyn wirtualnych z serii M platformy Azure podczas korzystania z akceleratora zapisu usługi Azure Write opóźnienie zapisu w dziennikach ponawiania online można zmniejszyć o czynniki w porównaniu z wydajnością usługi Azure Premium Storage. Włącz akceleratora zapisu azure dla dysków (VHD) na podstawie usługi Azure Premium Storage, które są używane do plików dziennika ponownego składania zapisu w trybie online. Aby uzyskać więcej informacji, zobacz [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Tworzenie kopii zapasowych/przywracanie
W przypadku funkcji tworzenia kopii zapasowych/przywracania narzędzia SAP BR*Tools for Oracle są obsługiwane w taki sam sposób, jak na gołym metalu i technologii Hyper-V. Oracle Recovery Manager (RMAN) jest również obsługiwany w przypadku tworzenia kopii zapasowych na dysku i przywracanych z dysku.

Aby uzyskać więcej informacji na temat korzystania z usług Azure Backup and Recovery do tworzenia kopii zapasowych i odzyskiwania baz danych Oracle, zobacz [Tworzenie kopii zapasowych i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej systemu Azure Linux.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery)

### <a name="high-availability"></a>Wysoka dostępność
Oracle Data Guard jest obsługiwany w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii. Aby uzyskać automatyczną pracę awaryjną w funkcji Data Guard, należy użyć funkcji szybkiego uruchamiania w trybie failover (FSFA). Funkcja Obserwatora (FSFA) wyzwala tryb failover. Jeśli nie używasz FSFA, można użyć tylko ręcznej konfiguracji trybu failover. Aby uzyskać więcej informacji, zobacz [Implementowanie oracle data guard na maszynie wirtualnej systemu Azure Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).


Aspekty odzyskiwania po awarii dla baz danych Oracle na platformie Azure są przedstawione w artykule [Odzyskiwanie po awarii bazy danych Oracle Database 12c w środowisku platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)

### <a name="accelerated-networking"></a>Wydajniejsze sieci
Obsługa przyspieszonej sieci platformy Azure w systemie Oracle Linux jest dostępna w systemie Oracle Linux 7 Update 5 (Oracle Linux 7.5). Jeśli nie możesz uaktualnić do najnowszej wersji Oracle Linux 7.5, może istnieć obejście przy użyciu jądra zgodnego z RedHat (RHCK) zamiast jądra Oracle UEK. 

Korzystanie z jądra RHEL w systemie Oracle Linux jest obsługiwane zgodnie z SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179). W przypadku usługi Azure Accelerated Networking minimalna wersja jądra RHCKL musi wynosić 3.10.0-862.13.1.el7. Jeśli używasz jądra UEK w systemie Oracle Linux w połączeniu z [platformą Azure Accelerated Networking,](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)musisz użyć jądra Oracle UEK w wersji 5.

Jeśli wdrażasz maszyny wirtualne z obrazu, który nie jest oparty na portalu Azure Marketplace, musisz skopiować dodatkowe pliki konfiguracyjne do maszyny wirtualnej, uruchamiając następujący kod: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Inne
[Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md) opisują inne ważne pojęcia związane z wdrożeniem maszyn wirtualnych z bazą danych Oracle Database, w tym zestawami dostępności platformy Azure i monitorowaniem SAP.
