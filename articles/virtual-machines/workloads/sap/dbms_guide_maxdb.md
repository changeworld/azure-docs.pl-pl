---
title: SAP MaxDB liveCache i serwer zawartości wdrożenia na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: SAP MaxDB, liveCache i wdrażania serwera zawartości na platformie Azure
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83319118c778d89749b1eb5d5fd792a5200c19c5
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849854"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB, liveCache i serwer zawartości wdrożenia na maszynach wirtualnych platformy Azure

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




W tym dokumencie opisano kilka różnych obszarów, aby wziąć pod uwagę podczas wdrażania MaxDB liveCache i serwer zawartości w usłudze Azure IaaS. Jako warunek wstępny do tego dokumentu, powinien mieć odczytu dokumentu [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md) oraz inne przewodniki w [obciążeń SAP nawdokumentacjiplatformyAzure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Szczegółowe informacje o wdrożeniach SAP MaxDB na Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Obsługa wersji MaxDB SAP na platformie Azure
SAP obsługuje obecnie MaxDB SAP wersji 7.9 lub nowszej do użytku z produktów opartych na oprogramowanie SAP NetWeaver na platformie Azure. Wszystkie aktualizacje dla serwera SAP MaxDB lub sterowników JDBC i ODBC do użycia z produktów opartych na oprogramowanie SAP NetWeaver znajdują się wyłącznie za pośrednictwem SAP Service Marketplace na <https://support.sap.com/swdc>.
Ogólne informacje na temat uruchamiania oprogramowania SAP NetWeaver na SAP MaxDB znajduje się w temacie <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Obsługiwane typy wersje Windows firmy Microsoft i maszyny Wirtualnej platformy Azure dla systemu DBMS MaxDB SAP
Aby znaleźć obsługiwanej wersji programu Microsoft Windows dla systemu MaxDB SAP DBMS na platformie Azure, zobacz:

* [Macierz dostępności produktów SAP (PAM)][sap-pam]
* Uwaga SAP [1928533]

Zdecydowanie zaleca się używać najnowszej wersji systemu operacyjnego Microsoft Windows, która jest Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Dostępne SAP dokumentacji MaxDB MaxDB
Zaktualizowaną listę SAP MaxDB dokumentację można znaleźć w następujących SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania MaxDB SAP dla instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Konfiguracja magazynu
Usługa Azure storage najlepsze rozwiązania dotyczące SAP MaxDB wykonaj ogólne zalecenia, o których wspomniano w rozdziale [struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Podobnie jak inne bazy danych SAP MaxDB ma plików danych i dziennika. Jednak w terminologii SAP MaxDB prawidłowy termin jest "wolumin" (nie "plik"). Na przykład istnieją SAP MaxDB woluminów danych i woluminy dziennika. Nie należy mylić ich z woluminów dysku systemu operacyjnego. 
> 
> 

W skrócie należy:

* Jeśli używasz konta usługi Azure Storage, równa kontem magazynu platformy Azure, w którym przechowywane są woluminy danych i dziennika SAP MaxDB (dane i pliki dziennika) **lokalny magazyn nadmiarowy (LRS)** określonej [zagadnienia dotyczące wirtualnych platformy Azure Maszyny do wdrażania systemu DBMS, w przypadku obciążeń SAP](dbms_guide_general.md).
* Ścieżki we/wy SAP MaxDB woluminy danych (pliki danych) należy oddzielić od ścieżki we/wy dziennika woluminów (pliki dziennika). Oznacza, że woluminy danych SAP MaxDB (pliki danych), muszą być zainstalowane na jednym dysku logicznego, jak i woluminy dziennika SAP MaxDB (pliki dziennika) musi być zainstalowany na innym dysku logicznego.
* Ustaw odpowiednie typu buforowania dla każdego dysku, w zależności od tego, czy zostanie on użyty do SAP MaxDB danych lub dziennika woluminów (dane i pliki dziennika), czy korzystasz, Standard platformy Azure lub usługi Azure Premium Storage, zgodnie z opisem w [zagadnienia dotyczące systemu DBMS na maszynach wirtualnych platformy Azure wdrożenie w przypadku obciążeń SAP](dbms_guide_general.md).
* Tak długo, jak bieżący limit przydziału operacji We/Wy na dysku spełnia wymagania, jest możliwe, aby przechowywać wszystkie woluminy danych na jednym dysku zainstalowanego i również przechowywać wszystkie woluminy dziennika bazy danych na innym dysku zainstalowanego pojedynczego.
* Jeśli więcej operacji We/Wy i/lub miejsca są wymagane, zaleca się utworzyć jedno urządzenie logiczne dużych przez wiele dysków zainstalowanych za pomocą Microsoft okna pul magazynów (tylko dostępne w systemie Microsoft Windows Server 2012 lub nowszy). Aby uzyskać więcej informacji, zobacz też [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). Takie podejście ułatwia zmniejszenie administracyjnych kosztów ogólnych zarządzać miejscem na dysku i pozwala uniknąć nakład pracy ręcznie dystrybucji plików na wielu dyskach zainstalowane.
* Zdecydowanie zaleca się korzystanie z usługi Azure Premium Storage MaxDB wdrożeń. 

![Odwołanie do konfiguracji maszyny wirtualnej IaaS platformy Azure dla systemu DBMS MaxDB SAP](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Kopia zapasowa i przywracanie
W przypadku wdrażania SAP MaxDB na platformie Azure, należy przejrzeć swoje metodologii kopii zapasowej. Nawet jeśli system nie jest wydajność systemu, bazy danych SAP hostowanej przez oprogramowanie SAP MaxDB musi kopii zapasowej okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, kopia zapasowa jest teraz mniej ważne pod kątem ochrony systemu przed wystąpił błąd magazynu i niezwykle ważne awariami operacyjnych lub administracyjnych. Głównym powodem utrzymywania odpowiednich kopii zapasowej i przywracania plan jest tak, aby można kompensowane błędy logiczne, czy ręcznie, przez udostępnienie możliwości w momencie odzyskiwania. Celem jest więc użyć kopii zapasowych do przywrócenia bazy danych do pewnego momentu w czasie albo na potrzeby tworzenia kopii zapasowych na platformie Azure umieszczenia innego systemu przez skopiowanie istniejącej bazy danych. 

Wykonywanie kopii zapasowych i przywracania bazy danych na platformie Azure działa tak samo jak w przypadku systemów lokalnych, aby można było używać standardowych narzędzi/przywracania kopii zapasowej SAP MaxDB, które są opisane w jednym z dokumentów dokumentacji SAP MaxDB Uwaga SAP na liście [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania
Tak jak w przypadku wdrożenia bez systemu operacyjnego wydajności tworzenia kopii zapasowych i przywracania są zależne od liczby woluminów mogą być odczytywane w równoległych i przepływność tych woluminów. W związku z tym jeden założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych, dolna ogólną przepływność odczytu
* Mniejszą liczbę elementów docelowych (katalogi usługi Stripe, dyski) można zapisać kopii zapasowej do dolnego przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu, istnieją dwie opcje, których można, prawdopodobnie w połączeniu, w zależności od potrzeb:

* Przypisywanie oddzielnych woluminach kopii zapasowej
* Stosowanie wolumin docelowy kopii zapasowej za pośrednictwem wielu dysków zainstalowanych w celu zwiększenia przepływności operacji We/Wy na woluminie dysku rozłożonego
* Posiadanie oddzielnych dedykowanych dysku logicznego urządzeń:
  * SAP MaxDB woluminach kopii zapasowej (tj. plików)
  * SAP MaxDB woluminy danych (np. plików)
  * SAP woluminy dziennika MaxDB (tj. plików)

Stosowanie woluminu na wiele dysków zainstalowanych została omówiona wcześniej w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Inne zagadnienia
Wszystkie inne ogólne obszary, takich jak zestawy dostępności platformy Azure lub SAP monitorowanie stosuje się także, zgodnie z opisem w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md).  w przypadku wdrożeń maszyn wirtualnych z bazą danych SAP MaxDB.
Inne ustawienia specyficzne dla MaxDB SAP są niewidoczne dla maszyn wirtualnych platformy Azure i są opisane w różnych dokumentach wymienionych w Uwaga SAP [767598] w tych SAP Notes:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Szczegółowe informacje o wdrożeniach liveCache SAP w Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache Obsługa wersji
Minimalna wersja liveCache SAP obsługiwane w usłudze Azure Virtual Machines jest **SAP LC/LCAPPS 10.0 SP 25** w tym **liveCache 7.9.08.31** i **25 LCA kompilacji**, wydanej dla **2 EhP SAP SCM 7.0** i nowszych wersjach.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Obsługiwane typy wersje Windows firmy Microsoft i maszyny Wirtualnej platformy Azure dla SAP liveCache DBMS
Aby znaleźć obsługiwanej wersji programu Microsoft Windows liveCache SAP na platformie Azure, zobacz:

* [Macierz dostępności produktów SAP (PAM)][sap-pam]
* Uwaga SAP [1928533]

Zdecydowanie zaleca się używać najnowszej wersji systemu operacyjnego Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache wskazówki dotyczące konfigurowania instalacji SAP na maszynach wirtualnych Azure
#### <a name="recommended-azure-vm-types-for-livecache"></a>Zalecane liveCache typy maszyn wirtualnych platformy Azure
SAP liveCache się aplikacja, która wykonuje obliczenia ogromna ilość i szybkość pamięci RAM i procesora CPU ma poważny wpływ na wydajność liveCache SAP. 

Dla typów maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP (Uwaga SAP [1928533]), wszystkie wirtualne zasobów procesora CPU przydzielonych do maszyny Wirtualnej są objęte dedykowanych zasobów Procesora fizycznego funkcji hypervisor. Nie celi (i w związku z tym nie konkurencję w odniesieniu do zasobów procesora CPU) odbywa się.

Podobnie dla wszystkich typów wystąpień maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP, pamięci maszyny Wirtualnej jest 100% mapowane w pamięci fizycznej - przerostu (zobowiązanie nadmiernego), na przykład nie jest używany.

Z tego punktu widzenia zaleca używania najnowszych Dv2, Dv3 i Ev3 oraz maszyny wirtualne serii M. Wybór różnych typów maszyn wirtualnych jest zależna od pamięci, czego potrzebujesz do liveCache i zasobów procesora CPU, które są potrzebne. Jako za pomocą wszystkich innych wdrożeń systemu DBMS wskazane jest korzystanie z usługi Azure Premium Storage na potrzeby wydajności woluminy krytyczne.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Konfiguracja usługi Storage dla liveCache na platformie Azure
Jak SAP liveCache jest oparty na technologii SAP MaxDB, wszystkie usługi Azure storage zalecenia dotyczące najlepszych rozwiązań wymienionych dla MaxDB SAP opisane w niniejszym dokumencie również są prawidłowe dla SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedykowane maszyny Wirtualnej platformy Azure w scenariuszu liveCache
Jak SAP liveCache intensywnie korzysta z mocy obliczeniowej, wydajne użycie zaleca do wdrożenia na dedykowanej maszynie wirtualnej platformy Azure. 

![Dedykowany liveCache dla przypadku użycia wydajność maszyny Wirtualnej platformy Azure](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Kopia zapasowa i przywracanie programu liveCache na platformie Azure
Kopia zapasowa i przywracanie, w tym zagadnienia związane z wydajnością już zostały opisane w odpowiednich rozdziałów SAP MaxDB, w tym dokumencie. 

#### <a name="other-considerations"></a>Inne zagadnienia
Inne ogólne obszary już zostały opisane w odpowiednim rozdziałem SAP MaxDB. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Szczegółowe informacje o wdrożenia na serwerze zawartości SAP na Windows na platformie Azure
Serwer zawartości SAP jest składnikiem oddzielny, oparte na serwerze, do przechowywania zawartości takiej jak elektronicznych dokumentów w różnych formatach. Serwer zawartości SAP jest świadczona przez rozwoju technologii i ma być używany między platformami w przypadku aplikacji SAP. Jest on instalowany w oddzielnym systemie. Typowe zawartość jest materiały szkoleniowe i dokumentacja z magazynu wiedzy lub rysunki techniczne pochodzące z mySAP System zarządzania dokumentami elementu. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Obsługa wersji zawartości serwera SAP na maszynach wirtualnych platformy Azure
SAP obecnie obsługuje:

* **Serwer zawartości SAP** wersją **6.50 (lub nowszy)**
* **SAP MaxDB version 7.9**
* **Microsoft IIS (Internet Information Server) w wersji 8.0 (i nowsze)**

Zaleca się używać najnowszej wersji zawartości serwera SAP i najnowsza wersja **Microsoft IIS**. 

Sprawdź najnowszych obsługiwanych wersjach zawartości serwer SAP i Microsoft IIS w [SAP produktu dostępności macierzy (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Obsługiwane typy Microsoft Windows i maszyn wirtualnych platformy Azure dla serwera zawartości SAP
Aby dowiedzieć się, obsługiwaną wersję Windows dla serwera zawartości SAP na platformie Azure, zobacz:

* [Macierz dostępności produktów SAP (PAM)][sap-pam]
* Uwaga SAP [1928533]

Zdecydowanie zaleca się używać najnowszej wersji systemu Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania serwera zawartości SAP dla instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration-for-content-server-in-azure"></a>Konfigurację magazynu dla serwera zawartości na platformie Azure
Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w bazie danych SAP MaxDB, wszystkich usługi Azure storage najlepsze rozwiązania w zakresie zaleceń wymienionych w niniejszym dokumencie SAP MaxDB również są prawidłowe dla scenariusza SAP serwer zawartości. 

Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w systemie plików, zaleca się użyć dedykowanego dysku logicznego. Przy użyciu funkcji miejsca do magazynowania systemu Windows umożliwia również zwiększyć rozmiar dysku logicznego i przepływności operacji We/Wy, zgodnie z opisem w [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>SAP Content Server Location
Serwer zawartości SAP musi zostać wdrożony w tym samym regionie platformy Azure i siecią Wirtualną platformy Azure, w którym jest wdrażany SAP system. Mogą zdecydować, czy chcesz wdrożyć składniki serwera zawartości SAP na dedykowanej maszynie Wirtualnej platformy Azure lub w tej samej maszyny Wirtualnej, którym jest uruchomiony SAP system. 

![Maszyna wirtualna platformy Azure w wersji dedykowanej dla serwera zawartości SAP](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP Cache Server Location
Serwer pamięci podręcznej SAP jest dodatkowych składników na serwerze, aby zapewnić dostęp do dokumentów (buforowanej), lokalnie. Serwer SAP w pamięci podręcznej buforuje dokumentów serwera zawartości SAP. To, aby zoptymalizować ruch sieciowy, jeśli dokumenty mają być pobierane więcej niż jeden raz z różnych lokalizacji. Ogólną zasadą jest, że serwer SAP w pamięci podręcznej musi być fizycznie blisko klienta, który uzyskuje dostęp do serwera SAP w pamięci podręcznej. 

Poniżej dostępne są dwie opcje:

1. **Klient znajduje się w wewnętrznej bazie danych systemu SAP** jest klientem, jeśli system SAP wewnętrznej bazy danych skonfigurowano na dostęp do zawartości serwera SAP, tego systemu SAP. Jak SAP system i SAP serwera zawartości są wdrażane w tym samym regionie platformy Azure, w tym samym centrum danych platformy Azure, są fizycznie blisko siebie. W związku z tym nie ma potrzeby dedykowanego serwera pamięci podręcznej SAP. Bezpośredni dostęp klientów SAP interfejsu użytkownika (SAP GUI lub przeglądarki sieci web) w systemie SAP i SAP system pobiera dokumenty z serwera zawartości SAP.
2. **Klient jest w przeglądarce sieci web w środowisku lokalnym** można skonfigurować serwer zawartości programu SAP były dostępne bezpośrednio z poziomu przeglądarki sieci web. W tym przypadku przeglądarki sieci web działających lokalnie to klient serwera zawartości SAP. W lokalnym centrum danych i centrum danych platformy Azure są umieszczane w różnych lokalizacjach fizycznych (najlepiej blisko siebie). W lokalnym centrum danych jest podłączony do platformy Azure za pomocą usługi Azure VPN lokacja-lokacja lub ExpressRoute. Mimo że obie opcje oferują bezpiecznego połączenia sieci VPN na platformie Azure, połączenie lokacja lokacja nie oferuje SLA przepustowości i opóźnienia sieci między lokalnym centrum danych i centrum danych platformy Azure. Aby przyspieszyć dostęp do dokumentów, wykonaj jedną z następujących czynności:
   1. Zainstaluj serwer pamięci podręcznej SAP w środowisku lokalnym, blisko przeglądarki sieci web w środowisku lokalnym (opcja na rysunku poniżej)
   2. Konfigurowanie usługi Azure ExpressRoute, która oferuje szybki i małym opóźnieniu dedykowane połączenie sieciowe między lokalnym centrum danych i centrum danych platformy Azure.

![Opcja instalacji serwera pamięci podręcznej SAP w środowisku lokalnym](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Wykonywanie kopii zapasowych i ich przywracanie
Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w bazie danych SAP MaxDB, zagadnienia dotyczące procedury i wydajność/przywracania kopii zapasowej już zostały opisane w rozdziałach SAP MaxDB tego dokumentu. 

Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w systemie plików, jedną z opcji jest wykonywanie ręczne/przywracania kopii zapasowej całego pliku struktury gdzie znajdują się dokumentów. Podobnie jak SAP MaxDB kopii zapasowej/przywracania, zaleca się mieć dedykowany wolumin w celu tworzenia kopii zapasowej. 

#### <a name="other"></a>Inne
Inne ustawienia zawartości SAP specyficzne dla serwera są niewidoczne dla maszyn wirtualnych platformy Azure i są opisane w różnych dokumentach i SAP Notes:

* <https://service.sap.com/contentserver> 
* Uwaga SAP [1619726]  
