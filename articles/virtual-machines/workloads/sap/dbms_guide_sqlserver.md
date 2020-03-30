---
title: Wdrożenie dbms maszyn wirtualnych platformy SQL Server Azure dla obciążenia SAP | Dokumenty firmy Microsoft
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP
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
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0fbed1f4dd62b2d75d39f475d2fe124c55a2b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645807"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Wdrożenie dbms maszyn wirtualnych platformy SQL Server Azure dla sap NetWeaver

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




Ten dokument obejmuje kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania sql server dla obciążenia SAP w usłudze Azure IaaS. Jako warunek wstępny tego dokumentu należy przeczytać informacje o dokumencie [Zagadnienia dotyczące wdrażania dbms maszyn wirtualnych platformy Azure dla obciążenia SAP,](dbms_guide_general.md) a także inne przewodniki w [obciążeniu SAP w dokumentacji platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 



> [!IMPORTANT]
> Zakres tego dokumentu jest wersja systemu Windows na SQL Server. Sap nie obsługuje wersji programu SQL Server dla systemu Linux z żadnym oprogramowaniem SAP. Dokument nie omawia bazy danych SQL Platformy Microsoft Azure, która jest ofertą platformy Platformy Microsoft Azure jako platformy Microsoft Azure. Omówienie w tym dokumencie dotyczy uruchamiania produktu programu SQL Server, ponieważ jest on znany z wdrożeń lokalnych na maszynach wirtualnych platformy Azure, wykorzystując możliwości infrastruktury jako usługi platformy Azure. Możliwości i funkcje bazy danych między tymi dwiema ofertami są różne i nie powinny być mieszane ze sobą. Zobacz też:<https://azure.microsoft.com/services/sql-database/>
> 
>

Ogólnie rzecz biorąc należy rozważyć użycie najnowszych wersji programu SQL Server do uruchamiania obciążenia SAP w usłudze Azure IaaS. Najnowsze wersje programu SQL Server oferują lepszą integrację z niektórymi usługami i funkcjami platformy Azure. Lub mają zmiany, które optymalizują operacje w infrastrukturze usługi Azure IaaS.

Zaleca się przejrzenie [tej][virtual-machines-sql-server-infrastructure-services] dokumentacji przed kontynuowaniem.

W poniższych sekcjach fragmenty części dokumentacji pod powyższym linkiem są agregowane i wymienione. Szczegóły dotyczące SAP są również wymienione, a niektóre pojęcia są opisane bardziej szczegółowo. Jednak zaleca się, aby najpierw pracować za pośrednictwem dokumentacji powyżej przed przeczytaniem dokumentacji specyficzne dla programu SQL Server.

Istnieje kilka SQL Server w IaaS określonych informacji, które należy wiedzieć przed kontynuowaniem:

* **Obsługa wersji SQL:** Dla klientów SAP program SQL Server 2008 R2 lub nowszy jest obsługiwany na maszynie wirtualnej platformy Microsoft Azure. Wcześniejsze wersje nie są obsługiwane. Zapoznaj się z tym ogólnym [oświadczeniem o pomocy technicznej,](https://support.microsoft.com/kb/956893) aby uzyskać więcej informacji. Ogólnie rzecz biorąc SQL Server 2008 jest obsługiwany przez firmę Microsoft, jak również. Jednak ze względu na znaczne funkcje sap, który został wprowadzony z SQL Server 2008 R2, SQL Server 2008 R2 jest minimalną wersją dla SAP. Ogólnie rzecz biorąc należy rozważyć użycie najnowszych wersji programu SQL Server do uruchamiania obciążenia SAP w usłudze Azure IaaS. Najnowsze wersje programu SQL Server oferują lepszą integrację z niektórymi usługami i funkcjami platformy Azure. Lub mają zmiany, które optymalizują operacje w infrastrukturze usługi Azure IaaS. W związku z tym papier jest ograniczony do PROGRAMÓW SQL Server 2016 i SQL Server 2017.
* **Wydajność SQL:** Maszyny wirtualne hostowane na platformie Microsoft Azure działają dobrze w porównaniu z innymi ofertami wirtualizacji chmury publicznej, ale poszczególne wyniki mogą się różnić. Zapoznaj się z artykułem [Najważniejsze wskazówki dotyczące wydajności programu SQL Server w programie Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Korzystanie z obrazów z portalu Azure Marketplace:** Najszybszym sposobem wdrożenia nowej maszyny wirtualnej platformy Microsoft Azure jest użycie obrazu z portalu Azure Marketplace. Istnieją obrazy w portalu Azure Marketplace, które zawierają najnowsze wersje programu SQL Server. Obrazy, w których program SQL Server jest już zainstalowany, nie mogą być natychmiast używane dla aplikacji SAP NetWeaver. Powodem jest domyślne sortowanie programu SQL Server jest instalowany w tych obrazów, a nie sortowanie wymagane przez systemy SAP NetWeaver. Aby korzystać z takich obrazów, sprawdź kroki opisane w rozdziale [Korzystanie z obrazu programu SQL Server z witryny Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Zalecenia dotyczące struktury VM/VHD dla wdrożeń programu SQL Server związanych z SAP
Zgodnie z ogólnym opisem pliki wykonywalne programu SQL Server powinny być zlokalizowane lub zainstalowane\)na dysku systemowym dysku systemu operacyjnego maszyny Wirtualnej (dysk C: .  Zazwyczaj większość baz danych systemu SQL Server nie są wykorzystywane na wysokim poziomie przez sap NetWeaver obciążenia. W rezultacie systemowe bazy danych programu SQL Server (master, msdb i model) mogą pozostać w C:\ napęd, jak również. Wyjątkiem powinien być tempdb, który w przypadku obciążeń SAP może wymagać większej ilości danych lub woluminu operacji we/wy. Obciążenie we/wy, które nie powinny być stosowane do dysku VHD systemu operacyjnego. W przypadku takich systemów należy wykonać następujące kroki:


* W przypadku wszystkich typów maszyn wirtualnych z certyfikatem SAP (patrz SAP Note [1928533]), z wyjątkiem maszyn wirtualnych serii A, dane tempdb i pliki dziennika mogą być umieszczane na nieutrwalonych plikach D:\ Dysku. 
* Niemniej jednak zaleca się używanie wielu plików danych tempdb. Należy pamiętać D:\ woluminy dysków są różne w zależności od typu maszyny Wirtualnej. Dla dokładnych rozmiarów D:\ dysk różnych maszyn wirtualnych, sprawdź artykuł [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Te konfiguracje umożliwiają tempdb zużywają więcej miejsca niż dysk systemowy jest w stanie zapewnić. Nietrwałe D:\ dysk oferuje również lepsze opóźnienie we/wy i przepustowość (z wyjątkiem maszyn wirtualnych serii A). Aby określić odpowiedni rozmiar tempdb, można sprawdzić rozmiary tempdb w istniejących systemach. 

>[!NOTE]
> w przypadku umieszczenia plików danych tempdb i pliku dziennika w folderze na D:\ utworzony dysk, należy upewnić się, że folder istnieje po ponownym uruchomieniu maszyny Wirtualnej. Ponieważ D:\ dysk jest świeżo zainicjowany po ponownym uruchomieniu maszyny Wirtualnej wszystkie struktury plików i katalogów są wymazane. Możliwość odtworzenia ewentualnych struktur katalogów na D:\ przed rozpoczęciem usługi SQL Server jest udokumentowany w [tym artykule](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Konfiguracja maszyny Wirtualnej, która uruchamia program SQL Server z bazą danych SAP i gdzie dane tempdb i plik dziennika tempdb są umieszczane na D:\ dysk będzie wyglądać tak:

![Diagram prostej konfiguracji dysku maszyny Wirtualnej dla programu SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Powyższy diagram przedstawia prostą obudowę. Jak wymyka się w [artykule Zagadnienia dotyczące wdrażania DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP,](dbms_guide_general.md)liczba i rozmiar dysków magazynu w wersji Premium zależy od różnych czynników. Ale ogólnie zalecamy:

- Używanie miejsca do magazynowania do utworzenia jednej lub niewielkiej liczby woluminów, które zawierają pliki danych programu SQL Server. Powodem tej konfiguracji jest to, że w prawdziwym życiu istnieje wiele baz danych SAP z różnymi plikami bazy danych o różnych rozmiarach z różnym obciążeniem we/wy.
- Używanie miejsca do magazynowania w celu dostarczenia wystarczającej liczby usług We/Wy i pliku dziennika transakcji programu SQL Server. Potencjalne obciążenie we/wy często jest linią przewodnią dla rozmiaru woluminu dziennika transakcji, a nie potencjalnego wolumenu transakcji programu SQL Server
- Użyj D:\drive dla tempdb tak długo, jak wydajność jest wystarczająco dobra. Jeśli ogólne obciążenie jest ograniczone wydajnością przez tmepdb znajduje się na D:\ dysku, który może być konieczne do przeniesienia tempdb do oddzielnych dysków magazynu premium, zgodnie z zaleceniami w [tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Specjalnie dla maszyn wirtualnych serii M
W przypadku maszyny Wirtualnej z serii M platformy Azure opóźnienie zapisu w dzienniku transakcji można zmniejszyć przez czynniki, w porównaniu do wydajności usługi Azure Premium Storage, podczas korzystania z akceleratora zapisu azure. W związku z tym należy wdrożyć akceleratora zapisu azure dla VHD(ów), które tworzą wolumin dziennika transakcji programu SQL Server. Szczegóły można odczytać w dokumencie [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formatowanie dysków
W przypadku programu SQL Server rozmiar bloku NTFS dla dysków zawierających dane i pliki dziennika programu SQL Server powinien wynosić 64 KB. Nie ma potrzeby formatowania D:\ Dysku. Ten dysk jest wstępnie sformatowany.

Aby upewnić się, że przywracanie lub tworzenie baz danych nie jest inicjowanie plików danych przez zerowanie zawartości plików, należy upewnić się, że kontekst użytkownika usługa SQL Server jest uruchomiona w ma pewne uprawnienia. Zazwyczaj użytkownicy w grupie Administrator systemu Windows mają te uprawnienia. Jeśli usługa SQL Server jest uruchamiana w kontekście użytkownika niebędącego administratorem systemu Windows, należy przypisać mu prawo użytkownika **Wykonywanie zadań konserwacji woluminów**.  Zobacz szczegóły w tym artykule z bazy wiedzy Microsoft Knowledge Base:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Wpływ kompresji bazy danych
W konfiguracjach, w których przepustowość we/wy może stać się czynnikiem ograniczającym, każda miara, która zmniejsza liczbę we/wy, może pomóc rozciągnąć obciążenie, które można uruchomić w scenariuszu usługi IaaS, takim jak platforma Azure. W związku z tym jeśli nie zostało jeszcze zrobione, stosowanie kompresji SQL Server PAGE jest zalecane przez SAP i microsoft przed przekazaniem istniejącej bazy danych SAP na platformę Azure.

Zalecenie, aby wykonać kompresję bazy danych przed przekazaniem na platformę Azure jest podane z dwóch powodów:

* Ilość danych do przesłania jest niższa.
* Czas trwania wykonywania kompresji jest krótszy przy założeniu, że można użyć silniejszego sprzętu z większą przepustowością we/wy lub większą przepustowością we/wy lub mniejszym opóźnieniem we/wy w środowisku lokalnym.
* Mniejsze rozmiary baz danych mogą prowadzić do zmniejszenia kosztów alokacji dysku

Kompresja bazy danych działa również w maszynach wirtualnych platformy Azure, podobnie jak lokalnie. Aby uzyskać więcej informacji na temat kompresji istniejących baz danych SAP NetWeaver SQL Server, zapoznaj się z artykułem [Ulepszone narzędzie do kompresji SAP MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 i nowsze — przechowywanie plików bazy danych bezpośrednio w usłudze Azure Blob Storage
Sql Server 2014 i nowsze wersje otwierają możliwość przechowywania plików bazy danych bezpośrednio w sklepie Azure Blob bez "otoki" dysku VHD wokół nich. Szczególnie przy użyciu standardowej usługi Azure Storage lub mniejszych typów maszyn wirtualnych tego typu wdrożenia umożliwia scenariusze, w których można przezwyciężyć limity usług We/Wy, które byłyby wymuszane przez ograniczoną liczbę dysków, które mogą być zainstalowane do niektórych mniejszych typów maszyn wirtualnych. Ten sposób wdrażania działa dla baz danych użytkowników, jednak nie dla systemowych baz danych programu SQL Server. Działa również dla plików danych i dzienników programu SQL Server. Jeśli chcesz wdrożyć bazę danych SAP SQL Server w ten sposób zamiast "zawijania" jej w dyski VHD, pamiętaj:

* Konto magazynu używane musi być w tym samym regionie platformy Azure, jak ten, który jest używany do wdrażania maszyny Wirtualnej SQL Server jest uruchomiony w.
* Zagadnienia wymienione wcześniej dotyczące dystrybucji dysków VHD za pomocą różnych kont usługi Azure Storage dotyczą również tej metody wdrożeń. Oznacza, że operacje we/wy są wliczane do limitów konta usługi Azure Storage.
* Zamiast księgowania względem przydziału we/wy magazynu maszyny Wirtualnej ruch względem obiektów blob magazynu reprezentujących pliki danych i dzienników programu SQL Server będą uwzględniane w przepustowości sieci maszyny Wirtualnej określonego typu maszyny Wirtualnej. W przypadku przepustowości sieci i magazynu określonego typu maszyny wirtualnej zapoznaj się z artykułem [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* W wyniku wypychania we/wy pliku przez przydział sieci, jesteś stranding przydziału magazynu głównie i z tym używać ogólnej przepustowości maszyny Wirtualnej tylko częściowo.
* Cele wydajności przepływności we/wy i we/wy, które ma usługa Azure Premium Storage dla różnych rozmiarów dysków, nie mają już zastosowania. Nawet jeśli utworzone obiekty BLOB znajdują się w usłudze Azure Premium Storage. Obiekty docelowe są udokumentowane w artykule [Magazyn premium o wysokiej wydajności i dyski zarządzane dla maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). W wyniku umieszczania plików danych programu SQL Server i plików dziennika bezpośrednio na obiektach blob, które są przechowywane w usłudze Azure Premium Storage, charakterystyka wydajności może być inna w porównaniu do dysków VHD w usłudze Azure Premium Storage.
* Buforowanie oparte na hoście dostępne dla dysków usługi Azure Premium Storage nie jest dostępne podczas umieszczania plików danych programu SQL Server bezpośrednio w obiektach blob platformy Azure.
* Na maszynach wirtualnych z serii M akceleratora zapisu usługi Azure Write accelerator nie może być używany do obsługi zapisów poniżej milisekundy względem pliku dziennika transakcji programu SQL Server. 

Szczegóły dotyczące tej funkcji można znaleźć w artykule [Pliki danych programu SQL Server na platformie Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Zalecenie dla systemów produkcyjnych jest, aby uniknąć tej konfiguracji i raczej wybrać miejsca docelowe danych i plików dziennika programu SQL Server w usłudze Azure Premium Storage VHDs zamiast bezpośrednio na obiektach blob platformy Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Rozszerzenie puli buforów programu SQL Server 2014
Program SQL Server 2014 wprowadził nową funkcję, która nosi nazwę [Rozszerzenie puli buforów.](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017) Ta funkcja rozszerza pulę buforów programu SQL Server, która jest przechowywana w pamięci z pamięcią podręczną drugiego poziomu, która jest wspierana przez lokalne dyski SSD serwera lub maszyny Wirtualnej. Rozszerzenie puli buforów umożliwia przechowywanie większego zestawu roboczego danych "w pamięci". W porównaniu do uzyskiwania dostępu do usługi Azure Standard Storage dostęp do rozszerzenia puli buforów, który jest przechowywany na lokalnych dysków SSD maszyny Wirtualnej platformy Azure jest wiele czynników szybciej. Porównanie rozszerzenia puli buforów z pamięcią podręczną odczytu usługi Azure Premium Storage, zgodnie z zaleceniami dla plików danych programu SQL Server, nie oczekuje się żadnych istotnych korzyści dla rozszerzeń puli buforów. Powodem jest to, że obie pamięci podręczne (rozszerzenie puli buforów programu SQL Server i pamięć podręczna odczytu magazynu w warstwie Premium) używają dysków lokalnych węzła obliczeniowego platformy Azure.

Doświadczenia zdobyte w międzyczasie z rozszerzeniem puli buforów programu SQL Server z obciążeniem SAP jest mieszany i nadal nie zezwala na jasne zalecenia dotyczące używania go we wszystkich przypadkach. Idealnym przypadkiem jest to, że zestaw roboczy wymaga aplikacji SAP mieści się w pamięci głównej. Z platformy Azure w międzyczasie oferuje maszyny wirtualne, które pochodzą z maksymalnie 4 TB pamięci, powinno być osiągalne, aby zachować zestaw roboczy w pamięci. W związku z tym użycie rozszerzenia puli buforów jest ograniczona do niektórych rzadkich przypadkach i nie powinna być głównym przypadkiem.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Zagadnienia dotyczące tworzenia kopii zapasowych/odzyskiwania dla programu SQL Server
Podczas wdrażania programu SQL Server na platformie Azure metodologia tworzenia kopii zapasowych musi zostać sprawdzona. Nawet jeśli system nie jest systemem produkcyjnym, baza danych SAP hostowana przez program SQL Server musi być okresowo archiwizowana. Ponieważ usługa Azure Storage przechowuje trzy obrazy, kopia zapasowa jest teraz mniej ważne w odniesieniu do kompensacji awarii magazynu. Priorytetem dla utrzymania prawidłowego planu tworzenia kopii zapasowych i odzyskiwania jest więcej, że można zrekompensować błędy logiczne/ ręczne, zapewniając możliwości odzyskiwania punktu w czasie. Dlatego celem jest albo użyć kopii zapasowych, aby przywrócić bazę danych z powrotem do pewnego punktu w czasie lub użyć kopii zapasowych na platformie Azure do inicjatora innego systemu przez skopiowanie istniejącej bazy danych. 

Aby przyjrzeć się różnym możliwościom tworzenia kopii zapasowych programu SQL Server na platformie Azure, przeczytaj artykuł [Kopia zapasowa i przywracanie dla programu SQL Server w maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Artykuł obejmuje kilka różnych możliwości.

### <a name="manual-backups"></a>Ręczne tworzenie kopii zapasowych
Masz kilka możliwości wykonywania "ręcznych" kopii zapasowych przez:

1. Wykonywanie konwencjonalnych kopii zapasowych programu SQL Server na bezpośrednio podłączonych dyskach platformy Azure. Ta metoda ma tę zaletę, że masz kopie zapasowe dostępne szybko dla odświeżania systemu i tworzenia nowych systemów jako kopie istniejących systemów SAP
2.  SQL Server 2012 CU4 i nowsze można kopii zapasowej baz danych do adresu URL magazynu platformy Azure.
3.  Kopie zapasowe migawki plików dla plików bazy danych w usłudze Azure Blob Storage. Ta metoda działa tylko wtedy, gdy pliki danych i dzienników programu SQL Server znajdują się w magazynie obiektów blob platformy Azure

Pierwsza metoda jest dobrze znana i stosowana w wielu przypadkach również w świecie lokalnym. Niemniej jednak, pozostawia ci zadanie, aby rozwiązać dłuższą lokalizację kopii zapasowej. Ponieważ nie chcesz przechowywać kopii zapasowych przez 30 lub więcej dni w lokalnie dołączonej usłudze Azure Storage, musisz użyć usług Azure Backup Services lub innego narzędzia do tworzenia kopii zapasowych/odzyskiwania innych firm, które obejmuje zarządzanie dostępem i przechowywaniem kopii zapasowych. Możesz też utworzyć duży serwer plików na platformie Azure przy użyciu miejsc do magazynowania systemu Windows.

Druga metoda jest opisana bliżej w artykule [SQL Server Backup do adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Różne wersje programu SQL Server mają pewne różnice w tej funkcji. W związku z tym należy sprawdzić dokumentację dla określonego sql server release check. Ważne jest, aby pamiętać, że w tym artykule wymieniono wiele ograniczeń. Masz albo możliwość wykonania kopii zapasowej przeciwko:

- Jeden pojedynczy obiekt blob strony platformy Azure, który następnie ogranicza rozmiar kopii zapasowej do 1000 GB. Ogranicza to również przepływność, którą można osiągnąć.
- Wiele (do 64) bloków bloków platformy Azure, które umożliwiają teoretyczny rozmiar kopii zapasowej 12 TB. Jednak testy z bazami danych klientów wykazały, że maksymalny rozmiar kopii zapasowej może być mniejszy niż teoretyczny limit. W takim przypadku użytkownik jest odpowiedzialny za zarządzanie przechowywaniem kopii zapasowych i dostęp do kopii zapasowych.


### <a name="automated-backup-for-sql-server"></a>Zautomatyzowane tworzenie kopii zapasowej programu SQL Server
Automatyczna kopia zapasowa zapewnia usługę automatycznego tworzenia kopii zapasowych dla wersji SQL Server Standard i Enterprise działających na maszynie Wirtualnej systemu Windows na platformie Azure. Ta usługa jest dostarczana przez [rozszerzenie agenta IaaS programu SQL Server,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)które jest automatycznie instalowane na obrazach maszyn wirtualnych systemu SQL Server windows w portalu Azure. Jeśli wdrożysz własne obrazy systemu operacyjnego z zainstalowanym programem SQL Server, musisz zainstalować rozszerzenia maszyn wirtualnych oddzielnie. Niezbędne kroki są opisane w tym [artykule](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Więcej szczegółów na temat możliwości tej metody można znaleźć w następujących artykułach:

- SQL Server 2014: [Automatyczna kopia zapasowa dla maszyn wirtualnych programu SQL Server 2014 (Menedżer zasobów)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure (Menedżer zasobów)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Przeglądając dokumentację, widać, że funkcje z nowszych wersji programu SQL Server ulepszone. Niektóre szczegóły dotyczące automatycznych kopii zapasowych programu SQL Server są publikowane w artykule [SQL Server Managed Backup na platformie Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Teoretyczny limit rozmiaru kopii zapasowej wynosi 12 TB.  Automatyczne kopie zapasowe mogą być dobrą metodą tworzenia kopii zapasowych o rozmiarze do 12 TB. Ponieważ wiele obiektów blob są zapisywane równolegle, można oczekiwać przepływności większej niż 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Maszyny wirtualne usługi Azure Backup dla programu SQL Server
Ta nowa metoda tworzenia kopii zapasowych programu SQL Server jest oferowana od czerwca 2018 r. jako publiczna wersja zapoznawcza przez usługi Azure Backup. Metoda tworzenia kopii zapasowych programu SQL Server jest taka sama, jak inne narzędzia innych firm, a mianowicie interfejs SQL Server VSS/VDI do przesyłania strumieniowego kopii zapasowych do lokalizacji docelowej. W tym przypadku lokalizacją docelową jest magazyn usługi Azure Recovery Service.

Bardziej niż szczegółowy opis tej metody tworzenia kopii zapasowych, która dodaje wiele zalet konfiguracji centralnych kopii zapasowych, monitorowania i administracji jest dostępny [tutaj](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Rozwiązania do tworzenia kopii zapasowych innych firm
Dla wielu klientów SAP nie było możliwości rozpoczęcia od nowa i wprowadzenia kompletnych nowych rozwiązań do tworzenia kopii zapasowych dla części ich środowiska SAP, która była uruchomiona na platformie Azure. W rezultacie istniejące rozwiązania do tworzenia kopii zapasowych musiały być używane i rozszerzane na platformę Azure. Rozszerzenie istniejących rozwiązań do tworzenia kopii zapasowych na platformę Azure zwykle działało dobrze z większością głównych dostawców w tym miejscu. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Używanie obrazu programu SQL Server z witryny Microsoft Azure Marketplace
Firma Microsoft oferuje maszyny wirtualne w portalu Azure Marketplace, które już zawierają wersje programu SQL Server. Dla klientów SAP, którzy wymagają licencji dla programu SQL Server i systemu Windows, korzystanie z tych obrazów może być okazją do pokrycia potrzeby licencji przez rozkręcenie maszyn wirtualnych z sql server już zainstalowany. Aby korzystać z takich obrazów dla sap, należy wziąć pod uwagę następujące kwestie:

* Wersje programu SQL Server nieo ewaluacyjne zyskują wyższe koszty niż maszyna wirtualna "tylko dla systemu Windows" wdrożona w witrynie Azure Marketplace. Zobacz te artykuły, <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> aby <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>porównać ceny: i . 
* Można używać tylko wersji programu SQL Server, które są obsługiwane przez SAP.
* Sortowanie wystąpienia programu SQL Server, które jest zainstalowane w maszynach wirtualnych oferowanych w portalu Azure Marketplace nie jest sortowanie SAP NetWeaver wymaga sql server wystąpienie do uruchomienia. Można zmienić sortowanie jednak ze wskazówkami w poniższej sekcji.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Zmienianie sortowania programu SQL Server maszyny Wirtualnej systemu Microsoft Windows/SQL Server
Ponieważ obrazy programu SQL Server w portalu Azure Marketplace nie są skonfigurowane do używania sortowania, które jest wymagane przez aplikacje SAP NetWeaver, należy je zmienić natychmiast po wdrożeniu. W przypadku programu SQL Server tę zmianę sortowania można wykonać za pomocą następujących kroków, gdy tylko maszyna wirtualna zostanie wdrożona, a administrator może zalogować się do wdrożonej maszyny Wirtualnej:

* Otwórz okno polecenia systemu Windows jako administrator.
* Zmień katalog na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Wykonaj polecenie: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> jest kontem, które zostało zdefiniowane jako konto administratora podczas wdrażania maszyny wirtualnej po raz pierwszy za pośrednictwem galerii.

Proces powinien potrwać tylko kilka minut. Aby upewnić się, czy krok zakończył się prawidłowym wynikiem, wykonaj następujące kroki:

* Otwórz program SQL Server Management Studio.
* Otwórz okno kwerendy.
* Wykonaj polecenie sp_helpsort w głównej bazie danych programu SQL Server.

Pożądany wynik powinien wyglądać następująco:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Jeśli wynik jest inny, zatrzymaj wdrażanie SAP i zbadaj, dlaczego polecenie konfiguracji nie działało zgodnie z oczekiwaniami. Wdrażanie aplikacji SAP NetWeaver w wystąpieniu programu SQL Server z różnymi stronami kodowymi programu SQL Server niż wymienione **powyżej, NIE** jest obsługiwane.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Wysoka dostępność programu SQL Server dla systemu SAP na platformie Azure
Korzystając z programu SQL Server w wdrożeniach usługi Azure IaaS dla sap, masz kilka różnych możliwości, aby dodać do wdrożenia warstwy DBMS wysokiej dostępności. Jak omówiono w [rozważaniach dotyczących wdrażania DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP,](dbms_guide_general.md) platforma Azure zapewnia różne łata w czasie pracy dla pojedynczej maszyny wirtualnej i parę maszyn wirtualnych wdrożonych w zestawie dostępności platformy Azure. Założenie jest, że dysk do up-time SLA dla wdrożeń produkcyjnych, który wymaga wdrożenia w zestawach dostępności platformy Azure. W takim przypadku należy wdrożyć co najmniej dwie maszyny wirtualne w takim zestawie dostępności. Jedna maszyna wirtualna uruchomi aktywne wystąpienie programu SQL Server. Druga maszyna wirtualna uruchomi pasywne wystąpienie

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Klastrowanie serwerów SQL przy użyciu skalowawki w poziomie serwera plików systemu Windows
W systemie Windows Server 2016 firma Microsoft wprowadziła [bezpośrednie miejsca do magazynowania.](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) Na podstawie bezpośredniego wdrażania miejsc do magazynowania obsługiwany jest klaster SQL Server FCI. Szczegóły można znaleźć w [artykule Konfigurowanie wystąpienia klastra trybu failover programu SQL Server na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). Rozwiązanie wymaga modułu równoważenia obciążenia platformy Azure, a także do radzenia sobie z wirtualnym adresem IP zasobów klastra. Pliki bazy danych programu SQL Server są przechowywane w miejscach do magazynowania. W związku z tym jest to, że jest wymagane do tworzenia miejsc do magazynowania systemu Windows na podstawie usługi Azure Premium Storage. Ponieważ to rozwiązanie jest obsługiwane jeszcze nie zbyt długo, nie ma znanych klientów SAP, którzy używają tego rozwiązania w scenariuszach produkcyjnych SAP.  

### <a name="sql-server-log-shipping"></a>Wysyłka dziennika programu SQL Server
Jedną z metod wysokiej dostępności (HA) jest SQL Server Log Shipping. Jeśli maszyny wirtualne uczestniczące w konfiguracji wysokiej jakości mają rozpoznawania nazw roboczych, nie ma problemu i instalacji na platformie Azure nie różni się od instalacji, która jest wykonywana lokalnie. W odniesieniu do konfigurowania log shipping i zasad wokół dziennika wysyłki. Szczegóły dotyczące wysyłania dzienników programu SQL Server można znaleźć w artykule [Informacje o wysyłce dziennika (SQL Server).](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017)

Funkcja wysyłania dzienników programu SQL Server prawie nie była używana na platformie Azure w celu osiągnięcia wysokiej dostępności w jednym regionie platformy Azure. Jednak w następujących scenariuszach klienci SAP używali pomyślnej wysyłki dziennika w połączeniu z platformą Azure:

- Scenariusze odzyskiwania po awarii z jednego regionu platformy Azure do innego regionu platformy Azure
- Konfiguracja odzyskiwania po awarii z lokalnego do regionu platformy Azure
- Scenariusze wycinania z lokalnego na platformę Azure. W takich przypadkach wysyłka dziennika jest używana do synchronizowania nowego wdrożenia systemu DBMS na platformie Azure z trwającym systemem produkcyjnym lokalnie. W momencie cięcia, produkcja jest zamykana i upewnij się, że ostatnie i najnowsze kopie zapasowe dziennika transakcji zostały przeniesione do wdrożenia usługi Azure DBMS. Następnie wdrożenie usługi Azure DBMS jest otwierane do produkcji.  



### <a name="database-mirroring"></a>Dublowanie bazy danych
Dublowanie bazy danych obsługiwane przez SAP (zobacz SAP Note [965908]) opiera się na definiowaniu partnera trybu failover w ciągu połączenia SAP. W przypadku między premises zakładamy, że dwie maszyny wirtualne znajdują się w tej samej domenie i że kontekst użytkownika dwa wystąpienia programu SQL Server są uruchomione w ramach użytkownika domeny, jak również i mają wystarczające uprawnienia w dwóch wystąpieniach programu SQL Server zaangażowanych. W związku z tym konfiguracja dublowania bazy danych na platformie Azure nie różni się między typowe konfiguracji/konfiguracji lokalnej.

W przypadku wdrożeń tylko w chmurze najprostszą metodą jest skonfigurowanie innej domeny na platformie Azure, aby te maszyny wirtualne dbms (i idealnie dedykowane maszyny wirtualne SAP) w jednej domenie.

Jeśli domena nie jest możliwa, można również użyć certyfikatów dla punktów końcowych dublowania bazy danych, jak opisano poniżej:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Samouczek, aby skonfigurować dublowanie baz danych na platformie Azure można znaleźć tutaj:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>Opcja Zawsze włączone programu SQL Server
Jak zawsze włączone jest obsługiwane dla SAP lokalnie (zobacz SAP Note [1772688]), jest obsługiwany w połączeniu z SAP na platformie Azure. Istnieje kilka specjalnych zagadnień dotyczących wdrażania modułu sieci Web modułu rozpoznawania dostępności programu SQL Server (nie należy mylić z zestawem dostępności platformy Azure), ponieważ platforma Azure w tym momencie nie zezwala na tworzenie obiektu usługi AD/DNS, ponieważ jest to możliwe lokalnie. W związku z tym niektóre kroki instalacji są niezbędne do przezwyciężenia określonego zachowania platformy Azure.

Niektóre zagadnienia przy użyciu odbiornika grupy dostępności są:

* Korzystanie z odbiornika grupy dostępności jest możliwe tylko w systemie Windows Server 2012 lub nowszym jako system operacyjny gościa maszyny Wirtualnej. W przypadku systemu Windows Server 2012 należy upewnić się, że ta poprawka została zastosowana:<https://support.microsoft.com/kb/2854082> 
* W przypadku systemu Windows Server 2008 R2 ta poprawka nie istnieje i zawsze włączone musi być używana w taki sam sposób jak dublowanie bazy danych, określając partnera trybu failover w ciągu połączeń (odbywa się za pośrednictwem parametru SAP default.pfl dbs/mss/server - patrz UWAGA SAP [965908]).
* Podczas korzystania z odbiornika grupy dostępności maszyny wirtualne bazy danych muszą być połączone z dedykowanym modułem równoważenia obciążenia. Aby uniknąć przypisywania nowych adresów IP przez platformę Azure w przypadkach, gdy obie maszyny wirtualne przypadkowo są zamykane, należy przypisać statyczne adresy IP do interfejsów sieciowych tych maszyn wirtualnych w konfiguracji Zawsze włączone (zdefiniowanie statycznego adresu IP opisano w [tym][virtual-networks-reserved-private-ip] artykule)
* Istnieją specjalne kroki wymagane podczas tworzenia konfiguracji klastra WSFC, gdzie klaster wymaga przypisanego specjalnego adresu IP, ponieważ platforma Azure z jego bieżącą funkcjonalnością przypisze nazwę klastra ten sam adres IP, co węzeł, na którym jest tworzony klaster. Oznacza to, że należy wykonać krok ręczny, aby przypisać inny adres IP do klastra.
* Odbiornik grupy dostępności zostanie utworzony na platformie Azure z punktami końcowymi TCP/IP, które są przypisane do maszyn wirtualnych z podstawowych i pomocniczych replik grupy Dostępność.
* Może być konieczne zabezpieczenie tych punktów końcowych za pomocą list ACL.

Szczegółowa dokumentacja dotycząca wdrażania always on z programem SQL Server na listach maszyn wirtualnych platformy Azure, takich jak:

- [Wprowadzenie grup dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
- [Skonfiguruj grupę dostępności zawsze włączone na maszynach wirtualnych platformy Azure w różnych regionach](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Skonfiguruj moduł równoważenia obciążenia dla grupy dostępności zawsze włączone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Jeśli konfigurujesz moduł równoważenia obciążenia platformy Azure dla wirtualnego adresu IP odbiornika grupy dostępności, upewnij się, że directserverReturn jest skonfigurowany. skonfigurowanie tej opcji zmniejszy opóźnienie sieci w obie strony między warstwą aplikacji SAP a warstwą DBMS. 

SQL Server Always On jest najczęściej używane wysokiej dostępności i odzyskiwania po awarii funkcje używane na platformie Azure dla wdrożeń obciążenia SAP. Większość klientów używa always on dla wysokiej dostępności w ramach jednego regionu platformy Azure. Jeśli wdrożenie jest ograniczone tylko do dwóch węzłów, masz dwie możliwości łączności:

- Korzystanie z odbiornika grupy dostępności. Za pomocą odbiornika grupy dostępności wymagane jest wdrożenie modułu równoważenia obciążenia platformy Azure. Zazwyczaj jest to domyślna metoda wdrażania. Aplikacje SAP będą skonfigurowane do łączenia się z detektorem grupy dostępności, a nie z jednym węzłem
- Korzystanie z parametrów łączności programu SQL Server Database Mirroring. W takim przypadku należy skonfigurować łączność aplikacji SAP w sposób, w którym nazwy obu węzłów są nazwane. Dokładne szczegóły takiej konfiguracji po stronie SAP są udokumentowane w sap Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Korzystając z tej opcji, nie trzeba będzie konfigurować odbiornika grupy dostępności. A z tym brak modułu równoważenia obciążenia platformy Azure dla wysokiej dostępności programu SQL Server. W rezultacie opóźnienie sieci między warstwą aplikacji SAP a warstwą DBMS jest niższa, ponieważ ruch przychodzący do wystąpienia programu SQL Server nie jest kierowany za pośrednictwem modułu równoważenia obciążenia platformy Azure. Ale przypomnijmy, ta opcja działa tylko wtedy, gdy ograniczysz grupę dostępności do zakresu dwóch wystąpień. 

Sporo klientów wykorzystuje funkcję SQL Server Always On do dodatkowych funkcji odzyskiwania po awarii między regionami platformy Azure. Kilku klientów korzysta również z możliwości wykonywania kopii zapasowych z repliki pomocniczej. 

## <a name="sql-server-transparent-data-encryption"></a>Szyfrowanie przezroczystych danych programu SQL Server
Istnieje wielu klientów, którzy używają SQL Server [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) podczas wdrażania ich baz danych PROGRAMU SAP SQL Server na platformie Azure. Funkcja TDE programu SQL Server jest w pełni obsługiwana przez sap (patrz SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Stosowanie TDE programu SQL Server
W przypadkach, gdy wykonujesz heterogenizową migrację z innego systemu DBMS, działającego lokalnie, do systemu Windows/SQL Server uruchomionego na platformie Azure, należy utworzyć pustą bazę danych docelową w programie SQL Server z wyprzedzeniem. W następnym kroku należy zastosować funkcje TDE programu SQL Server. Podczas gdy system produkcyjny jest nadal uruchomiony lokalnie. Powodem, dla którego chcesz wykonać w tej sekwencji jest to, że proces szyfrowania pustej bazy danych może zająć sporo czasu. Procesy importowania SAP następnie zaimportować dane do zaszyfrowanej bazy danych w fazie przestojów. Obciążenie związane z importowaniem do zaszyfrowanej bazy danych ma znacznie mniejszy wpływ na czas niż szyfrowanie bazy danych po fazie eksportowania w fazie czasu przestoju. Negatywne doświadczenia, gdy podczas próby zastosowania TDE z obciążeniem SAP uruchomiony na bazie danych. W związku z tym zalecenie jest traktowanie wdrożenia TDE jako działania, które należy wykonać bez obciążenia SAP w określonej bazie danych.

W przypadkach, gdy przenosisz bazy danych SAP SQL Server z lokalnego środowiska na platformę Azure, zalecamy przetestowanie infrastruktury, na której infrastrukturze można uzyskać szyfrowanie stosowane najszybciej. W tym celu należy pamiętać o tych faktach:

- Nie można zdefiniować, ile wątków jest używanych do stosowania szyfrowania danych do bazy danych. Liczba wątków jest zasadniczo zależna od liczby woluminów dysków, na których są dystrybuowane pliki danych i dzienników programu SQL Server. Oznacza, że bardziej wyraźne woluminy (litery dysków), tym więcej wątków będzie zaangażowanych równolegle do wykonywania szyfrowania. Taka konfiguracja jest nieco sprzeczna z wcześniejszą sugestią konfiguracji dysku dotyczącą tworzenia jednej lub mniejszej liczby miejsc do magazynowania plików bazy danych programu SQL Server na maszynach wirtualnych platformy Azure. Konfiguracja z niewielką liczbą woluminów prowadzi do niewielkiej liczby wątków wykonujących szyfrowanie. Szyfrowanie pojedynczego wątku odczytuje zakresy 64 KB, szyfruje je, a następnie zapisuje rekord w pliku dziennika transakcji, informując, że zakres został zaszyfrowany. W rezultacie obciążenie dziennika transakcji jest umiarkowane.
- W starszych wersjach programu SQL Server kompresja kopii zapasowych nie była już zwiększana po zaszyfrowaniu bazy danych programu SQL Server. To zachowanie może rozwinąć się w problem, gdy plan był do szyfrowania bazy danych programu SQL Server lokalnie, a następnie skopiować kopię zapasową na platformę Azure, aby przywrócić bazę danych na platformie Azure. Kompresja kopii zapasowej programu SQL Server zwykle osiąga stopień kompresji współczynnika 4.
- Dzięki programowi SQL Server 2016 program SQL Server wprowadził nową funkcję, która umożliwia efektywne kompresowanie zaszyfrowanych baz danych. Zobacz [ten blogi,](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) aby uzyskać szczegółowe informacje.
 
Traktując zastosowanie szyfrowania TDE bez tylko niewielkiego obciążenia SAP, należy przetestować w określonej konfiguracji, czy lepiej jest zastosować TDE do lokalnej bazy danych SAP lub zrobić to na platformie Azure. Na platformie Azure z pewnością masz większą elastyczność pod względem nadmiernej obsługi administracyjnej infrastruktury i zmniejszyć infrastrukturę po TDE został zastosowany.

### <a name="using-azure-key-vault"></a>Korzystanie z usługi Azure Key Vault
Platforma Azure oferuje usługę [usługi usługi usługi Usługi Magazynu kluczy](https://azure.microsoft.com/services/key-vault/) do przechowywania kluczy szyfrowania. SQL Server po drugiej stronie oferują łącznik do wykorzystania usługi Azure Key Vault jako magazyn dla certyfikatów TDE.

Więcej szczegółów, aby użyć listy TDE usługi Azure Key Vault dla programu SQL Server, takich jak:

- [Rozszerzalne zarządzanie kluczami przy użyciu usługi Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server TDE rozszerzalne zarządzanie kluczami przy użyciu usługi Azure Key Vault — kroki konfiguracji](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [Rozwiązywanie problemów z & obsługi łączników programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Więcej pytań od klientów dotyczących szyfrowania przezroczystych danych programu SQL Server — TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Korzystając z programu SQL Server TDE, szczególnie w przypadku usługi Azure key vault, zaleca się używanie najnowszych poprawek programu SQL Server 2014, SQL Server 2016 i SQL Server 2017. Powodem jest to, że na podstawie opinii klientów, optymalizacje i poprawki zostały zastosowane do kodu. Na przykład zaznacz #4058175 [KBA](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Ogólny program SQL Server for SAP w podsumowaniu platformy Azure
Istnieje wiele zaleceń w tym przewodniku i zaleca się przeczytać go więcej niż jeden raz przed planowaniem wdrożenia platformy Azure. Ogólnie rzecz biorąc, należy jednak wykonać przestrzeganie najlepszych ogólnych usług dbms w zaleceniach specyficznych dla platformy Azure:

1. Użyj najnowszej wersji dbms, takiej jak SQL Server 2017, która ma najwięcej zalet na platformie Azure. 
2. Starannie zaplanuj poziom systemu SAP na platformie Azure, aby zrównoważyć układ pliku danych i ograniczenia platformy Azure:
   * Nie masz zbyt wielu dysków, ale masz wystarczająco dużo, aby upewnić się, że można osiągnąć wymagane we/wy.
   * Jeśli nie używasz dysków zarządzanych, pamiętaj, że usługi We/Wy są również ograniczone dla konta usługi Azure Storage i że konta magazynu są ograniczone w ramach każdej subskrypcji platformy Azure[(więcej szczegółów).][azure-resource-manager/management/azure-subscription-service-limits] 
   * W przypadku konieczności uzyskania wyższej przepływności należy ować tylko na dyskach.
3. Nigdy nie instaluj oprogramowania ani nie umieszczaj plików wymagających wytrwałości w D:\ dysk, ponieważ jest nietrwałe i wszystko na tym dysku jest tracone przy ponownym uruchomieniu systemu Windows.
4. Nie używaj buforowania dysków dla usługi Azure Standard Storage.
5. Nie używaj geograficznych kont usługi Azure Standard Storage platformy Azure.  Użyj lokalnie nadmiarowe dla obciążeń dbms.
6. Użyj rozwiązania ha/odzyskiwania odzyskiwania/odzyskiwania bazy danych dostawcy usługi dbms do replikowania danych bazy danych.
7. Zawsze używaj rozpoznawania nazw, nie polegaj na adresach IP.
8. Za pomocą programu SQL Server TDE zastosuj najnowsze poprawki programu SQL Server.
9. Użyj najwyższej możliwej kompresji bazy danych. Czyli kompresja strony dla programu SQL Server.
10. Należy zachować ostrożność przy użyciu obrazów programu SQL Server z portalu Azure Marketplace. Jeśli używasz programu SQL Server jeden, należy zmienić sortowanie wystąpienia przed zainstalowaniem dowolnego systemu SAP NetWeaver na nim.
11. Zainstaluj i skonfiguruj monitorowanie hosta SAP dla platformy Azure zgodnie z opisem w [Przewodniku wdrażania][deployment-guide].
