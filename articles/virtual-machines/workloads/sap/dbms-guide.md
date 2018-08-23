---
title: Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver | Dokumentacja firmy Microsoft
description: Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6b0ea7479910f7026974e37f8c05099453c0b26
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42061065"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ten przewodnik stanowi część dokumentacji na implementowanie i wdrażanie oprogramowania SAP w systemie Microsoft Azure. Przed przeczytaniu tego przewodnika przeczytaj [Podręcznik planowania i implementacji][planning-guide]. W tym dokumencie opisano wdrażanie różnych relacyjnej bazy danych zarządzania systemów (RDBMS) i pokrewnych produktów w połączeniu z oprogramowaniem SAP na Microsoft Azure Virtual Machines (VMs) przy użyciu infrastruktury platformy Azure jako możliwości usługi (IaaS).

Ten dokument stanowi uzupełnienie dokumentacji instalacji SAP i SAP Notes, którą reprezentują podstawowe zasoby dla instalacji i wdrożenia oprogramowania SAP na podanych platformach.

## <a name="general-considerations"></a>Zagadnienia ogólne
W tym rozdziale kwestii związanych z systemami powiązane SAP DBMS na maszynach wirtualnych Azure zostały wprowadzone. Istnieje kilka odwołań do określonych systemów DBMS, w tym rozdziale. Zamiast tego określonych systemów DBMS są obsługiwane w tym dokumencie po w tym rozdziale.

### <a name="definitions-upfront"></a>Definicje ponoszonych z góry kosztów
W dokumencie Stosujemy następujące warunki:

* IaaS: Infrastruktura jako usługa.
* PaaS: Platforma jako usługa.
* SaaS: Oprogramowanie jako usługa.
* SAP składnik: poszczególnych aplikacji SAP ECC, BW, Menedżer rozwiązania lub EP.  Składniki SAP mogą być oparte na tradycyjnych technologii ABAP i Java lub aplikacji innych niż NetWeaver na podstawie takich jak obiekty biznesowych.
* Środowisko SAP: co najmniej jednego składnika SAP logicznie pogrupowane do wykonywania funkcji biznesowych, takich jak rozwój, QAS, szkolenia, odzyskiwania po awarii lub produkcji.
* Środowisko SAP: Odnosi się do całego zasobów SAP klientów pozioma IT. Środowisko SAP obejmuje wszystkie produkcji i środowisk nieprodukcyjnych.
* SAP System: Kombinacja warstwy system DBMS i warstwy aplikacji, na przykład SAP ERP i przeniesieniu jej rozwoju systemu SAP BW system testowy, system produkcyjny SAP CRM, itp. W przypadku wdrożeń platformy Azure go nie jest obsługiwane do dzielenia tych dwóch warstw między lokalną i platformą Azure. To oznacza, że system SAP jest wdrożony w środowisku lokalnym lub jest ona wdrożona na platformie Azure. Można jednak wdrożyć różnych systemów środowisko SAP na platformie Azure lub lokalnie. Na przykład możesz wdrożyć rozwoju SAP CRM i systemy testowe platformie Azure, ale SAP CRM produkcji systemu lokalnego.
* Wdrożenie oparte tylko na chmurze: wdrożenia, w którym subskrypcji platformy Azure nie jest połączony za pośrednictwem lokacja lokacja lub połączenia ExpressRoute do infrastruktury sieci w środowisku lokalnym. W typowych dokumentacji platformy Azure tego rodzaju wdrożenia są również opisać jako "tylko na chmurze". Maszyn wirtualnych wdrożonych przy użyciu tej metody są dostępne za pośrednictwem Internetu oraz publiczne internetowe punkty końcowe przypisane do maszyn wirtualnych na platformie Azure. Lokalne usługi Active Directory (AD) i DNS nie zostanie rozszerzony na platformę Azure w tych typów wdrożeń. Dlatego maszyny wirtualne nie są częścią lokalnej usługi Active Directory. Uwaga: Wdrożeń tylko w chmurze, w tym dokumencie są definiowane jako ukończone SAP zapewniały realizację niezbędnych zadań, które działają wyłącznie na platformie Azure, bez rozszerzenia usługi Active Directory lub rozpoznawanie nazw ze środowiska lokalnego do chmury publicznej. Konfiguracje tylko w chmurze nie są obsługiwane dla systemów SAP w środowisku produkcyjnym lub konfiguracje, których SAP STMS i innych zasobów w środowisku lokalnym trzeba było używać między systemami SAP hostowanych na platformie Azure i zasobami znajdującymi się w środowisku lokalnym.
* Między lokalizacjami: w tym artykule opisano scenariusz, w której maszyny wirtualne są wdrażane z subskrypcją platformy Azure, site to site, obejmujące wiele lokacji lub połączenia usługi ExpressRoute między zasobom w środowisku lokalnym i platformą Azure. Dokumentacja wspólnych platformy Azure, tego rodzaju wdrożenia są również opisać jako scenariuszy obejmujących wiele lokalizacji. Przyczyna połączenie ma rozszerzone domen lokalnych, w lokalnej usłudze Active Directory i DNS w środowisku lokalnym na platformę Azure. Pozioma w środowisku lokalnym jest rozszerzony do zasobów platformy Azure w subskrypcji. Problemy to rozszerzenie, maszyn wirtualnych może być częścią domeny w środowisku lokalnym. Użytkownicy domeny lokalnej domeny mogą uzyskiwać dostęp do serwerów i można uruchomić usługi na tych maszynach wirtualnych (np. usługi DBMS). Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożony w środowisku lokalnym i maszyn wirtualnych wdrożonych na platformie Azure jest możliwe. Oczekuje się, żeby było to najbardziej typowym scenariuszem wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [w tym artykule] [ vpn-gateway-cross-premises-options] i [w tym artykule][vpn-gateway-site-to-site-create].

> [!NOTE]
> Wdrożenia obejmujące systemów SAP, w których członkowie lokalnej domeny usługi Azure Virtual Machines z systemami SAP są obsługiwane dla systemów SAP w środowisku produkcyjnym. Konfiguracje obejmujące są obsługiwane w przypadku wdrażania części lub zakończyć krajobrazów SAP na platformie Azure. Jeszcze uruchomione pełne środowisko SAP na platformie Azure wymaga posiadanie tych maszyn wirtualnych, które są częścią lokalnej domeny i pokazywania REKLAM. W wcześniejsze wersje dokumentacji Omówiliśmy scenariuszy hybrydowych IT, gdy termin *hybrydowego* jest ścieżką z faktu, że istnieje łączność między lokalizacjami w środowisku lokalnym i platformą Azure. W tym przypadku *hybrydowego* oznacza, że maszyny wirtualne na platformie Azure należą do lokalnej usługi Active Directory.
> 
> 

Dokumentacji firmy Microsoft w tym artykule opisano scenariusze obejmujące nieco inaczej, szczególnie w przypadku systemu DBMS zaświadczanie o kondycji konfiguracji. W przypadku dokumentów związanych z SAP wrzenia scenariusz między lokalizacjami w dół o lokacja lokacja lub prywatnej łączności (ExpressRoute) i na fakt, że SAP landscape są rozproszone między lokalną i platformą Azure.

### <a name="resources"></a>Zasoby
Dla wdrożeń SAP na platformie Azure dostępne są następujące przewodniki:

* [Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver (w tym dokumencie)][dbms-guide]

Poniższe uwagi SAP są powiązane z SAP na platformie Azure:

| Numer | Stanowisko |
| --- | --- |
| [1928533] |Aplikacje środowiska SAP na platformie Azure: obsługiwane produkty i maszyny Wirtualnej platformy Azure |
| [2015553] |SAP na platformie Microsoft Azure: wymagania wstępne dotyczące obsługi |
| [1999351] |Rozwiązywanie problemów z rozszerzonego monitorowania platformy Azure dla rozwiązania SAP |
| [2178632] |Klucz metryki monitorowania dla rozwiązania SAP na platformie Microsoft Azure |
| [1409604] |Wirtualizacji na Windows: Enhanced Monitoring |
| [2191498] |SAP w systemie Linux przy użyciu platformy Azure: Enhanced Monitoring |
| [2039619] |Aplikacje środowiska SAP na Microsoft Azure przy użyciu bazy danych programu Oracle: obsługiwane produkty i wersje |
| [2233094] |DB6: Aplikacje środowiska SAP na platformie Azure przy użyciu programu IBM DB2 dla systemów Linux, UNIX i Windows — informacje dodatkowe |
| [2243692] |Systemu Linux w systemie Microsoft Azure (IaaS) maszyn wirtualnych: problemy dotyczące licencji SAP |
| [1984787] |Systemie SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalowanie i uaktualnianie |
| [2069760] |Oracle Linux 7.x SAP instalacji i uaktualniania |
| [1597355] |Zalecenie obszaru wymiany w systemie Linux |
| [2171857] |Oracle Database 12c - Obsługa systemu plików w systemie Linux |
| [1114181] |Bazą danych Oracle 11g - Obsługa systemu plików w systemie Linux |


Przeczytaj również [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zawierający wszystkie uwagi SAP dla systemu Linux.

Należy mieć praktyczną wiedzę na temat o architektura platformy Microsoft Azure i sposób wdrożenia i działania systemu Microsoft Azure Virtual Machines. Więcej informacji można znaleźć <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Jesteśmy **nie** Omawiając platforma Microsoft Azure jako usługa (PaaS) oferty platformy Microsoft Azure. Ten dokument jest o uruchamianiu system zarządzania bazy danych (DBMS) w Microsoft Azure Virtual Machines (IaaS) systemu DBMS będą uruchamiane w danym środowisku w środowisku lokalnym. Funkcje bazy danych i funkcji między tych dwóch ofert bardzo różnią się i nie może być mieszane ze sobą. Zobacz też: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Ponieważ firma Microsoft dyskusji IaaS, ogólnie rzecz biorąc Windows, Linux i DBMS instalacji i konfiguracji są zasadniczo taka sama jak dowolnej maszynie wirtualnej lub bez systemu operacyjnego, możesz zainstalować w środowisku lokalnym. Istnieją jednak pewne architekturę i system zarządzania implementacji decyzji, które są inne w przypadku modelu IaaS. Ten dokument ma na celu wyjaśnić dotyczące architektury i systemu zarządzania różnice, które należy być przygotowanym dla przypadku w modelu IaaS.

Ogólnie rzecz biorąc ogólne obszary różnicą, że w tym artykule omówiono w tym dokumencie są:

* Planowanie właściwego układu maszyna wirtualna/dysk systemów SAP, aby upewnić się, że masz odpowiednie dane pliku układu i osiągnąć wystarczającej liczby operacji We/Wy dla danego obciążenia.
* Sieć uwagi dotyczące korzystania z modelu IaaS.
* Funkcje konkretnej bazy danych do użycia w celu zoptymalizowania układu bazy danych.
* Uwagi i przywracania kopii zapasowych w modelu IaaS.
* Przy użyciu różnych typów obrazów na potrzeby wdrożenia.
* Wysoka dostępność w modelu IaaS platformy Azure.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura wdrożenia RDBMS
Aby można było wykonać instrukcje opisane w tym rozdziale, jest to konieczne zrozumieć, co zostało przedstawione [to] [ deployment-guide-3] rozdziału [przewodnik wdrażania][deployment-guide]. Wiedzę na temat różnych serię maszyn wirtualnych oraz różnice i różnice Standard platformy Azure i Magazyn w warstwie Premium należy rozumieć i znane przed odczytaniem w tym rozdziale.

Do marca 2015 r. dyski, które zawierają system operacyjny były one ograniczone do 127 GB, rozmiar. To ograniczenie został podniesiony w marcu 2015 (Aby uzyskać więcej informacji o wyboru <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Z tego miejsca na dyskach, zawierający system operacyjny może mieć taki sam rozmiar jak pozostałe dysku. Niemniej jednak firma Microsoft nadal preferuje strukturę wdrażania, w którym system operacyjny, system DBMS i ostateczną SAP pliki binarne są niezależne od plików bazy danych. W związku z tym oczekujemy, że SAP na maszynach wirtualnych platformy Azure z systemami mają maszyny Wirtualnej podstawowego (lub dysku) zainstalowane z systemem operacyjnym, pliki wykonywalne systemu zarządzania bazy danych i plików wykonywalnych SAP. System DBMS plików danych i dziennika są przechowywane w usłudze Azure Storage (wersja standardowa lub Premium Storage) w oddzielnych dyskach i dołączone jako dyski logiczne w systemie do oryginalnego obrazu systemu operacyjnego platformy Azure maszyna wirtualna. 

W zależności od korzystanie z usługi Azure Standard lub Premium Storage (na przykład korzystając z serii DS lub GS-series VMs) są inne limity przydziału na platformie Azure, które są udokumentowane [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [ tutaj (Windows)][virtual-machines-sizes-windows]. Podczas planowania układ dysku, należy znaleźć najlepszą równowagę między przydziały dla następujących elementów:

* Liczba plików danych.
* Liczba dysków, które zawierają pliki.
* Przydziały operacje We/Wy pojedynczego dysku.
* Przepływność danych na dysku.
* Liczba dysków z danymi dodatkowych możliwości na rozmiar maszyny Wirtualnej.
* Ogólną przepływność magazynu maszyny Wirtualnej może zapewnić.

Azure wymusza przydziału operacji We/Wy na dysk z danymi. Te przydziały dotyczą różnych dysków w serwisie Azure magazynu w warstwie standardowa i Premium Storage. Wartości opóźnienia operacji We/Wy są również bardzo różnią się między tymi dwoma typami magazynu dzięki usłudze Premium Storage dostarczanie czynniki lepsze opóźnienia operacji We/Wy. Każda z różnych typów maszyn wirtualnych ma ograniczoną liczbę dysków z danymi, które można dołączyć. Ograniczenie innego jest, że niektóre typy maszyn wirtualnych mogą korzystać z usługi Azure Premium Storage. Oznacza to, że decyzji dla określonego typu maszyny Wirtualnej mogą nie tylko być wynikiem wymagań Procesora i pamięci, ale także przez operacje We/Wy, opóźnienia i dysku wymagań w zakresie przepływności, które zwykle są skalowane za pomocą liczbę dysków lub typu dysków usługi Premium Storage. Szczególnie w przypadku usługi Premium Storage rozmiaru dysku również mogą być definiowane według liczby operacji We/Wy i przepływność, który ma być osiągnięte przez każdego dysku.

Fakt, że ogólną szybkość operacji We/Wy zamontowany liczba dysków, a rozmiar maszyny Wirtualnej są wszystkie powiązane ze sobą, może spowodować konfiguracji platformy Azure z systemem SAP, może być inny niż wdrożenie w środowisku lokalnym. Limity operacji We/Wy dla jednostki LUN są zwykle konfigurowane, w przypadku wdrożeń lokalnych. Za pomocą usługi Azure Storage te limity są stałe, lub tak jak magazyn w warstwie Premium w zależności od typu dysku. Dlatego przy użyciu lokalnych wdrożeń widzimy konfiguracji klienta z serwerów baz danych, które korzystają z wielu różnych woluminach dla specjalnych plików wykonywalnych, takich jak SAP DBMS lub i woluminów specjalnych tymczasowej bazy danych lub tabeli miejsca do magazynowania. Po przeniesieniu systemu lokalnego do platformy Azure, może prowadzić do marnowania potencjalnych przepustowość operacji We/Wy przez marnowania dysku dla plików wykonywalnych i bazy danych, które nie należy wykonywać dowolne albo nie wiele operacji We/Wy. W związku z tym, na maszynach wirtualnych Azure firma Microsoft zaleca pliki wykonywalne systemu DBMS i SAP zainstalowania na dysku systemu operacyjnego, jeśli jest to możliwe.

Umieszczanie plików bazy danych i plików dziennika i typ magazynu platformy Azure, powinien być zdefiniowany przez operacje We/Wy, opóźnienia i wymagań w zakresie przepływności. Aby mogła mieć wystarczającej liczby operacji We/Wy dla dziennika transakcji, może być zmuszony do korzystać z wielu dysków w pliku dziennika transakcji, lub użyj większych dysku usługi Premium Storage. W takim przypadku jeden będzie tworzyć oprogramowanie RAID (na przykład Windows magazynu puli dla Windows lub MDADM i LVM (Menedżer woluminów logicznych) dla systemu Linux) z dyskami, które zawierają dziennika transakcji.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Dysku D:\ w Maszynie wirtualnej platformy Azure jest nieutrwaloną dysku, która jest wspierana przez niektóre dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną, oznacza to, że wszelkie zmiany wprowadzone do zawartości na dysku D:\, zostaną utracone, gdy maszyna wirtualna jest uruchamiany ponownie. Przez "wszelkie zmiany" Mamy na myśli zapisane pliki, katalogi utworzone, zainstalowane aplikacje, itp.
> 
> ![Linux][Logo_Linux] Linux
> 
> Maszyny wirtualne systemu Linux platformy Azure automatycznie zainstalować dysk w /mnt/resource, który jest dyskiem nietrwałych wspierana przez dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną, oznacza to, wszelkie zmiany wprowadzone do zawartości w /mnt/resource zostaną utracone, gdy maszyna wirtualna jest uruchamiany ponownie. Wszelkie zmiany mamy na myśli plików zapisanych, katalogi utworzone, zainstalowane aplikacje, itp.
> 
> 

- - -
Zależny od platformy Azure serię maszyn wirtualnych, dysków lokalnych w węźle obliczeniowym Pokaż wydajności, które mogą zostać podzielone, takich jak:

* A0 – A7: Bardzo małej wydajności. Nie można używać dla wszystkich elementów poza plik stronicowania systemu windows
* Wystąpienia a8 – A11: Charakterystyki wydajności bardzo dobre z niektórych 10 000 operacji We/Wy i > 1GB/s przepustowości
* Seria D: Charakterystyki wydajności bardzo dobre z niektórych 10 000 operacji We/Wy i > 1GB/s przepustowości
* Seria DS: Charakterystyki wydajności bardzo dobre z niektórych 10 000 operacji We/Wy i > 1GB/s przepustowości
* Seria G: Charakterystyki wydajności bardzo dobre z niektórych 10 000 operacji We/Wy i > 1GB/s przepustowości
* Seria GS: Charakterystyki wydajności bardzo dobre z niektórych 10 000 operacji We/Wy i > 1GB/s przepustowości

Instrukcje powyżej są stosowane do typów maszyn wirtualnych, które są certyfikowane z oprogramowaniem SAP. Maszyny Wirtualne serii doskonałą operacje We/Wy i przepływność kwalifikować się do korzystanie przez niektóre funkcje systemu DBMS, takich jak bazy danych tempdb lub miejsce tabeli tymczasowej.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Buforowanie dla maszyn wirtualnych i dysków z danymi
Podczas tworzenia dysków z danymi za pośrednictwem portalu lub możemy zainstalować dysków przekazanych do maszyn wirtualnych, możemy wybrać, czy ruch we/wy między te dyski, znajduje się w usłudze Azure storage i maszyny Wirtualnej są buforowane. Usługa Azure Standard i Premium Storage na użytek dwa różne technologie ten typ pamięci podręcznej. W obu przypadkach pamięci podręcznej, sama będzie dysku kopię na tych samych dysków używanych przez dysk tymczasowy (D:\ na Windows) lub /mnt/resource w systemie Linux maszyny Wirtualnej.

Dla usługi Azure Standard Storage dostępne są następujące typy możliwe pamięci podręcznej:

* Brak buforowania
* Odczyt z pamięci podręcznej
* Odczyt i zapis w pamięci podręcznej

Aby uzyskać spójny i jednoznaczna wydajności, należy skonfigurować buforowanie na usługi Azure Standard Storage dla wszystkich dysków zawierających **pliki danych związanych z bazami danych, plików dziennika i miejsca do tabeli "NONE"**. Buforowanie maszyny Wirtualnej może pozostać przy użyciu domyślnego.

Usługa Azure Premium Storage istnieją następujące opcje pamięci podręcznej:

* Brak buforowania
* Odczyt z pamięci podręcznej

Zalecenia dotyczące usługi Azure Premium Storage jest wykorzystanie **buforowania plików danych na potrzeby zapisu** bazy danych SAP i wybierz opcję **Brak buforowania dysków pliki dziennika**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Programowej macierzy RAID
Jak już wspomniano powyżej, musisz równomierny podział liczby operacji We/Wy jest wymagany dla plików bazy danych w liczbie dysków, które można skonfigurować, a maksymalna liczba IOPS Maszynie wirtualnej platformy Azure udostępnia na dysku lub typ dysku magazynu Premium Storage. Najprostszym sposobem, aby poradzić sobie z obciążeniem operacji We/Wy za pośrednictwem dysków jest tworzenie macierzy RAID oprogramowania za pośrednictwem różnych dyskach. Następnie umieścić pliki danych z systemu SAP DBMS na jednostkach LUN używać poza programowej macierzy RAID. Zależy od wymagań, że warto wziąć pod uwagę użycie magazynu w warstwie Premium oraz od dwa z trzech różnych dysków usługi Premium Storage zapewnia wyższy przydziału operacji We/Wy niż dyski magazynu w warstwie standardowa w oparciu. Oprócz znaczne opóźnienia operacji We/Wy lepsze udostępniane przez usługi Azure Premium Storage. 

Dotyczy to różnych systemów DBMS dziennika transakcji. Dzięki wielu z nich dodanie większej liczby plików Tlog nie pomocy od czasu systemów DBMS zapisu do jednego z plików tylko w czasie. W razie potrzeby wyższej szybkości operacji We/Wy niż jeden dysk na podstawie magazynu w warstwie standardowa może dostarczać można stripe przez wiele dysków magazynu w warstwie standardowa możesz też większych typ dysku magazynu Premium Storage oferującym poza wyższej szybkości operacji We/Wy również czynniki mniejsze opóźnienia zapisu czy / System operacyjny do dziennika transakcji.

Są sytuacje, napotkała w przypadku wdrożeń platformy Azure, które favor, za pomocą macierzy RAID oprogramowania:

* Dziennik dziennika/ponów transakcji wymagają IOPS więcej niż system Azure oferuje jeden dysk. Jak wspomniano powyżej to będzie możliwe przez utworzenie jednostki LUN przez wiele dysków za pomocą macierzy RAID oprogramowania.
* Nierówna operacji We/Wy obciążenia dystrybucja pliki z różnymi danymi bazy danych SAP. W takich przypadkach jeden możesz skorzystać z jednego pliku danych, lecz często osiągnięcia limitu przydziału. Podczas gdy inne pliki danych nie są jeszcze Zbliżasz przydziału operacji We/Wy pojedynczego dysku. W takich przypadkach Najprostszym rozwiązaniem jest tworzyć wiele dysków za pomocą macierzy RAID oprogramowania dla jednej jednostki LUN. 
* Nie wiadomo dokładnie obciążenia We/Wy na pliku danych, a czego tylko około znać całkowitego obciążenia operacji We/Wy względem systemu DBMS. Najłatwiej wykonać jest tworzenie jednej jednostki LUN za pomocą macierzy RAID oprogramowania. Sumę kwot wiele dysków pod tą jednostką LUN powinny następnie spełnić znanych szybkości operacji We/Wy.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Zalecamy używanie funkcji miejsca do magazynowania systemu Windows po uruchomieniu w systemie Windows Server 2012 lub nowszym. Jest bardziej wydajne niż Windows rozkładanie starszych wersjach Windows. Należy utworzyć pule magazynów systemu Windows i funkcji miejsca do magazynowania za pomocą poleceń programu PowerShell, korzystając z systemu Windows Server 2012 jako systemu operacyjnego. Polecenia programu PowerShell można znaleźć tutaj <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Tworzenie macierzy RAID oprogramowania w systemie Linux są obsługiwane tylko MDADM i LVM (Menedżer woluminów logicznych). Aby uzyskać więcej informacji przeczytaj następujące artykuły:
> 
> * [Konfigurowanie programowej macierzy RAID w systemie Linux] [ virtual-machines-linux-configure-raid] (w przypadku MDADM)
> * [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Zagadnienia dotyczące korzystania z maszyny Wirtualnej serii, które są zazwyczaj pracować z usługą Azure Premium Storage to:

* Zapotrzebowanie na opóźnienia operacji We/Wy, które znajdują się blisko urządzeniach SAN/NAS dostarczania.
* Zapotrzebowanie na czynniki lepsze opóźnień we/wy niż dostarczać usługi Azure Standard Storage.
* Wyższe operacje We/Wy na maszynę Wirtualną niż co można osiągnąć z wieloma dyskami magazynu w warstwie standardowa do typu maszyny Wirtualnej.

Ponieważ podstawowej usługi Azure Storage replikuje każdego dysku co najmniej trzy węzły magazynu, rozkładanie mogą być używane proste RAID 0. Nie ma potrzeby zaimplementować RAID5 lub RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Usługa Microsoft Azure Storage przechowuje podstawowej maszyny Wirtualnej (przy użyciu systemu operacyjnego) oraz dyski lub obiektów blob do co najmniej trzech oddzielnych węzłów do magazynowania. Podczas tworzenia konta magazynu lub dysk zarządzany, możliwy jest wybór ochrony, jak pokazano poniżej:

![Replikacja geograficzna włączany dla konta usługi Azure Storage][dbms-guide-figure-100]

Lokalne replikacja usługi Azure Storage (lokalnie nadmiarowy) zapewnia poziomy ochrony przed utratą danych z powodu błędu infrastruktury, która niewielu klientów można sobie pozwolić, aby wdrożyć. Jak wspomniano powyżej, że dostępne są cztery różne opcje z piąty jest odmianą jednego z trzech pierwszy. Trwa wyszukiwanie bliżej ich można wyróżnia:

* **Premium magazyn lokalnie nadmiarowy (LRS)**: Azure Premium Storage zapewnia obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych z systemem wyjścia — dużych obciążeń wejścia /. Istnieją trzy repliki danych w ramach tego samego centrum danych platformy Azure z regionu platformy Azure. Kopie znajdują się w różnych domenach błędów i uaktualnienia (pojęć można znaleźć [to] [ planning-guide-3.2] rozdziału w [Planning Guide][planning-guide]). W przypadku repliki dane wychodzące z usługi z powodu awarii węzła magazynu lub awaria dysku nowej repliki jest generowany automatycznie.
* **Magazyn lokalnie nadmiarowy (LRS)**: W tym przypadku istnieją trzy repliki danych w ramach tego samego centrum danych platformy Azure z regionu platformy Azure. Kopie znajdują się w różnych domenach błędów i uaktualnienia (pojęć można znaleźć [to] [ planning-guide-3.2] rozdziału w [Planning Guide][planning-guide]). W przypadku repliki dane wychodzące z usługi z powodu awarii węzła magazynu lub awaria dysku nowej repliki jest generowany automatycznie. 
* **Magazyn geograficznie nadmiarowy (GRS)**: W tym przypadku jest Replikacja asynchroniczna, energetyczna dodatkowe trzech replik danych w innym regionie platformy Azure, który znajduje się w większości przypadków, w tym samym regionie geograficznym (takich jak Europa Północna i Europa Zachodnia ). Skutkuje to trzy dodatkowe repliki, tak aby suma są sześciu replik. Odmiana tej jest dodatkiem, gdzie dane w regionie platformy Azure replikowanych geograficznie może służyć do celów odczytu (Read-Access geograficznie nadmiarowy).
* **Strefa magazyn nadmiarowy (ZRS)**: W tym przypadku trzech replik danych pozostają w tym samym regionie platformy Azure. Jak wyjaśniono w [to] [ planning-guide-3.1] rozdziału [Planning Guide] [ planning-guide] region platformy Azure może być liczbą centrów danych w pobliżu. W przypadku LRS repliki może być dystrybuowana za pośrednictwem różnych centrach danych, które ułatwiają jednego regionu platformy Azure.

Więcej informacji można znaleźć [tutaj][storage-redundancy].

> [!NOTE]
> W przypadku wdrożeń systemu DBMS nie jest zalecane użycie magazyn geograficznie nadmiarowy
> 
> Usługa Azure Storage replikacji geograficznej jest asynchroniczna. Replikacja poszczególnych dysków zainstalowany z jedną maszyną wirtualną nie są zsynchronizowane w kroku blokady. W związku z tym nie jest odpowiednia do replikacji plików systemu DBMS, które są rozproszone na różnych dyskach lub wdrożone przed oprogramowaniem RAID oparte na wielu dyskach. Oprogramowanie systemu DBMS wymaga, że magazynu dysku trwałego dokładnie synchronizację różnych jednostek LUN i podstawowych dysków/osi. Oprogramowanie systemu DBMS korzysta z różnych mechanizmów sekwencji operacji We/Wy zapisu działań i DBMS zgłasza, że magazyn dyskowy objęte replikacji jest uszkodzony, jeśli różnią się one nawet przez kilka milisekund. Dlatego jeśli chce jednej konfiguracji bazy danych, z bazą danych rozciągnięte na wielu dyskach replikacją geograficzną, takie replikacji musi zostać wykonana z oznacza, że bazy danych i funkcji. Jeden nie należy polegać na usłudze Azure Storage replikacji geograficznej do wykonania tego zadania. 
> 
> Problem polega na najprostszej wyjaśnić z systemem przykład. Załóżmy, że masz system SAP, przekazany na platformę Azure, która ma osiem dyski zawierające pliki danych z systemu DBMS, a także jeden dysk z plikiem dziennika transakcji. Każdej z nich te dziewięć dyski mają danych zapisanych w spójny sposób zgodnie z bazami danych, czy dane są zapisywane w plikach dziennika danych lub transakcji.
> 
> W celu prawidłowo replikacja geograficzna danych i obsługa obraz spójnej bazy danych, zawartość wszystkich dysków dziewięć konieczne będzie replikowana geograficznie dokładnie w takiej kolejności, operacje We/Wy zostały wykonane na dziewięć różnych dyskach. Jednak replikacji geograficznej usługi Azure Storage nie zezwala na zadeklarowanie zależności między dyskami. Oznacza to, że replikacja geograficzna usługi Microsoft Azure Storage nie wie o tym, że zawartość w te dziewięć różnych dyskach są ze sobą powiązane i zmiany danych są spójne tylko wtedy, gdy replikacja w kolejności operacji We/Wy wystąpiły dla wszystkich dziewięć dysków.
> 
> Oprócz szanse jest wysoka, że obrazy replikacją geograficzną, w tym scenariuszu nie są oferowane obraz spójnej bazy danych również jest spadek wydajności, wyświetlana z geograficznie nadmiarowym, może poważnie obniżyć wydajność. Podsumowując nie należy używać tego typu nadmiarowość magazynu dla obciążeń typu systemu DBMS.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapowanie wirtualnych dysków twardych na kontach magazynu usługi maszyny wirtualnej platformy Azure
Ten rozdział ma zastosowanie tylko do kont usługi Azure Storage. Jeśli planujesz używać usługi Managed Disks, ograniczenia, o których wspomniano w tym rozdziale nie mają zastosowania. Więcej informacji na temat dysków zarządzanych na ten temat można znaleźć w rozdziale [Managed Disks] [ dbms-guide-managed-disks] tego przewodnika.

Konto usługi Azure Storage jest nie tylko konstrukcja administracyjne, ale również przedmiotem ograniczeń. Natomiast ograniczenia być różne od tego, czy będziemy wyjaśniać, konto usługi Azure Standard Storage lub konta usługi Azure Premium Storage. Dokładnych możliwości i ograniczenia są wymienione [tutaj][storage-scalability-targets]

Dlatego standardowego magazynu platformy Azure należy pamiętać, operacje We/Wy na konto magazynu jest ograniczona do (wiersz zawierający **całkowita liczba żądań** w [artykuł][storage-scalability-targets]). Ponadto jest początkowa limit 100 kont magazynu na subskrypcję platformy Azure (począwszy od lipca 2015 r.). Dlatego zalecane jest równoważenie operacje We/Wy z maszyn wirtualnych między wieloma kontami magazynu przy użyciu usługi Azure Standard Storage. Podczas gdy pojedyncza maszyna wirtualna używa najlepiej jedno konto magazynu, jeśli jest to możliwe. Dlatego jeśli omówione wdrożeń systemu DBMS, gdzie każdego wirtualnego dysku twardego, który znajduje się w usłudze Azure Standard Storage można skontaktować się z jego limit przydziału, należy wdrożyć tylko 30 – 40 wirtualne dyski twarde na konto magazynu platformy Azure, która używa usługi Azure Standard Storage. Z drugiej strony Jeśli możesz korzystać z usługi Azure Premium Storage i mają być przechowywane w woluminach dużych baz danych może być dobrym rozwiązaniem w zakresie operacji We/Wy. Ale konto usługi Azure Premium Storage jest w sposób bardziej restrykcyjny ilości danych niż konto usługi Azure Standard Storage. W rezultacie można wdrażać tylko w ograniczonej liczbie wirtualnych dysków twardych w ramach konta usługi Azure Premium Storage przed osiągnięcia limitu woluminów danych. Na koniec Pomyśl o konto usługi Azure Storage jako "Wirtualną sieć SAN", który ma ograniczone możliwości w operacji We/Wy i/lub pojemności. W wyniku zadania pozostaje, tak jak w lokalnych wdrożeń, aby zdefiniować układ wirtualne dyski twarde różnych systemów SAP za pośrednictwem różnych "urojone urządzeniami sieci SAN" lub kont usługi Azure Storage.

Dla usługi Azure Standard Storage nie zaleca się zaprezentowanie magazynu z różnych kont magazynu dla pojedynczej maszyny Wirtualnej, jeśli jest to możliwe.

Korzystając z DS lub GS maszyn wirtualnych platformy Azure, istnieje możliwość instalacji wirtualne dyski twarde poza standardowe konta magazynu platformy Azure i kont usługi Premium Storage. Zastosowań, takich jak zapisywania kopii zapasowych do magazynu w warstwie standardowa kopii, wirtualne dyski twarde i zawierającej dane bazami danych i plików dziennika na usługę Premium Storage wyniknąć gdzie taki magazyn heterogeniczny można wykorzystać. 

Na podstawie wdrożeń klienta i testowanie około 30-40 wirtualnych dyskach twardych zawierających pliki danych bazy danych i pliki dziennika mogą być udostępniane w jednym standardowego konta usługi Azure Storage za pomocą akceptowalny poziom wydajności. Jak wspomniano wcześniej, ograniczenie konta usługi Azure Premium Storage jest prawdopodobnie pojemność danych, które można przechowywać i operacje We/Wy nie.

Jak za pomocą sieci SAN urządzeniami lokalnymi, udostępnianie wymaga pewne informacje monitorowania w celu wykrycia ostatecznie wąskich gardeł na konto usługi Azure Storage. Rozszerzenie monitorowania platformy Azure, SAP i witryny Azure portal to narzędzia, które mogą służyć do wykrywania zajęty kont magazynu platformy Azure, które mogą dostarczać nieoptymalne wydajności operacji We/Wy.  W przypadku wykrycia takiej sytuacji zaleca się przenieść obciążonych maszyn wirtualnych do innego konta magazynu platformy Azure. Zapoznaj się [przewodnik wdrażania] [ deployment-guide] dla szczegółowe informacje na temat aktywowania SAP hostować możliwości monitorowania.

Inny artykuł podsumowywanie najlepsze rozwiązania dotyczące usługi Azure Standard Storage i standardowe konta magazynu platformy Azure można znaleźć tutaj <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Dyski zarządzane
Dyski zarządzane są nowego typu zasobu w usłudze Azure Resource Manager używany zamiast wirtualnych dysków twardych, które są przechowywane na kontach magazynu Azure. Dyski zarządzane automatycznie wyrównane z zestawu dostępności maszyny wirtualnej, które są dołączone i w związku z tym zwiększyć dostępność maszyny wirtualnej i usługi, które są uruchomione na maszynie wirtualnej. Aby dowiedzieć się więcej, przeczytaj [artykuł z omówieniem](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP aktualnie obsługuje tylko dyski zarządzane w warstwie Premium. Uwaga SAP odczytu [1928533] Aby uzyskać więcej informacji.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Przenoszenie wdrożonych maszyn wirtualnych systemu DBMS za pomocą usługi Azure Standard Storage do usługi Azure Premium Storage
Można napotkać dość sytuacje, w którym jako klient korzystający z chcesz przenieść wdrożonej maszyny Wirtualnej z usługi Azure Standard Storage do usługi Azure Premium Storage. Jeśli Twoje dyski są przechowywane na kontach magazynu Azure, nie jest to możliwe bez fizycznego przenoszenia danych. Istnieje kilka sposobów, aby osiągnąć:

* Wszystkie wirtualne dyski twarde, podstawowy dysk VHD także wirtualne dyski twarde danych można skopiować do nowego konta usługi Azure Premium Storage. Często wybranej liczby wirtualnych dysków twardych w ramach usługi Azure Standard Storage nie ze względu na fakt, że potrzebna jest ilość danych. Ale oznacza wiele wirtualnych dysków twardych jest wymagane ze względu na operacje We/Wy. Teraz, gdy przeniesiesz się do usługi Azure Premium Storage, czy użytkownik może zawsze pod ręką sposób mniejszą liczbą wirtualnych dysków twardych, aby osiągnąć ten sam przepustowość operacji We/Wy. Biorąc pod uwagę fakt, że w usłudze Azure płacisz używanych danych i nie rozmiar dysku nominalna Standard Storage, liczba dysków VHD niezależnie od tego, pod względem kosztów. Jednak za pomocą usługi Azure Premium Storage, zapłacisz dla rozmiaru dysku symboliczną cenę. W związku z tym większość klientów, należy próbować zachować liczba wirtualnych dysków twardych platformy Azure w usłudze Premium Storage pod numerem potrzebnych do uzyskania przepływności operacji We/Wy niezbędne. Tak większość klientów zdecyduj, przed jej proste 1:1 kopia.
* Jeśli nie została jeszcze zainstalowana, możesz zainstalować pojedynczego wirtualnego dysku twardego, który może zawierać kopię zapasową bazy danych, bazy danych SAP. Po utworzeniu kopii zapasowej Odinstaluj wszystkie dyski VHD, w tym wirtualny dysk twardy zawierający kopię zapasową i skopiuj podstawowy dysk VHD i wirtualny dysk twardy z kopią zapasową do konta usługi Azure Premium Storage. Następnie będzie wdrożyć maszynę Wirtualną, oparte na podstawowy dysk VHD i zainstalować dysk VHD z kopią zapasową. Teraz utworzysz dodatkowe pustych dysków magazynu Premium Storage dla maszyny Wirtualnej, która umożliwia przywracanie bazy danych do. Przy założeniu, że systemu DBMS umożliwia zmianę ścieżki do plików danych i dziennika w ramach procesu przywracania.
* Inną możliwością jest odmianą poprzedniego procesu, gdzie pliku kopii zapasowej dysku VHD do usługi Azure Premium Storage i dołączyć go na maszynie Wirtualnej, która nowo wdrożeniu i zainstalowaniu.
* Czwarty możliwości należy wybrać podczas pracy potrzebujących Aby zmienić liczbę plików danych bazy danych. W takim przypadku przeprowadza się kopia jednorodnego systemu SAP za pomocą eksportu/importu. Umieść te eksportowania plików do wirtualnego dysku twardego, który jest kopiowana do konta usługi Azure Premium Storage i dołączyć je do maszyny Wirtualnej, którego używasz do uruchamiania procesów importu. Klienci używać tej możliwości, głównie w przypadku, gdy chce zmniejszyć liczbę plików danych.

Jeśli używasz dysków Managed Disks, można migrować do usługi Premium Storage przez:

1. Cofnij Przydział maszyny wirtualnej
1. W razie potrzeby zmień rozmiar maszyny wirtualnej na rozmiar, który obsługuje usługę Premium Storage (na przykład DS lub GS)
1. Zmień typ konta dysku zarządzanego w warstwie Premium (SSD)
1. Zmień buforowania dysków z danymi, zgodnie z zaleceniami w rozdziale [buforowania dla maszyn wirtualnych i dysków z danymi][dbms-guide-2.1]
1. Uruchom maszynę wirtualną

### <a name="deployment-of-vms-for-sap-in-azure"></a>Wdrażanie maszyn wirtualnych dla rozwiązania SAP na platformie Azure
Platforma Microsoft Azure oferuje wiele sposobów, aby wdrożyć maszyny wirtualne i skojarzone dyski. Tym samym ważne jest zrozumienie różnic, ponieważ przygotowań maszyny wirtualne mogą różnić się zależy od sposobu wdrożenia. Ogólnie rzecz biorąc mamy możliwość przejrzenia opisanych w następujących rozdziałach scenariuszy.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Wdrażanie maszyny Wirtualnej portalu Azure Marketplace
Chcesz wykonać firmy Microsoft lub innych firm, pod warunkiem obrazu z witryny Azure Marketplace do wdrożenia maszyny Wirtualnej. Po wdrożeniu maszyny Wirtualnej na platformie Azure, wykonaj te same wskazówki i narzędzia do zainstalowania oprogramowania SAP wewnątrz maszyny Wirtualnej, tak jak w środowisku lokalnym. Instalowania oprogramowania SAP wewnątrz maszyny Wirtualnej platformy Azure, SAP i Microsoft zaleca się przekazanie i przechowywać nośnika instalacyjnego programu SAP na dyskach lub utworzyć Maszynę wirtualną platformy Azure działa jako "serwera plików", który zawiera wszystkie niezbędne SAP nośniku instalacyjnym programu.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Wdrażanie maszyny Wirtualnej z uogólnionym obrazem właściwe dla klienta
Ze względu na wymagania określone poprawki dotyczące tej wersji systemu operacyjnego lub DBMS podana obrazów w portalu Azure Marketplace może nie spełniać Twoich wymagań. W związku z tym może być konieczne Utwórz Maszynę wirtualną przy użyciu własnego "private" obrazu systemu operacyjnego/DBMS maszyny Wirtualnej, który można wdrożyć w kilka razy później. Aby przygotować obraz "private" pod kątem duplikatów, system operacyjny musi być uogólniony na maszynie Wirtualnej w środowisku lokalnym. Zapoznaj się [przewodnik wdrażania] [ deployment-guide] Aby uzyskać szczegółowe informacje na temat sposobu uogólnianie maszyny Wirtualnej.

Jeśli zainstalowano już SAP zawartość w swojej lokalnej maszyny Wirtualnej (szczególnie w przypadku systemów warstwy 2), możesz dostosować ustawienia systemu SAP po wdrożenie maszyny Wirtualnej platformy Azure za pośrednictwem wystąpienia nazwy procedury obsługiwane przez oprogramowanie SAP oprogramowania inicjowania obsługi administracyjnej Menedżera (SAP Należy pamiętać, [1619720]). W przeciwnym razie można zainstalować oprogramowania SAP później po wdrożeniu maszyny Wirtualnej platformy Azure.

Bazy danych zawartości używana przez aplikację SAP, można wygenerować zawartość świeżo za pomocą instalacji SAP lub zaimportować zawartość na platformę Azure przy użyciu wirtualnego dysku twardego z kopii zapasowej bazy danych systemu DBMS lub dzięki wykorzystaniu możliwości systemu DBMS bezpośrednio utworzyć kopię zapasową do  Microsoft Azure Storage. W takim przypadku można również przygotować dyski VHD za pomocą systemu DBMS danych i dziennika pliki lokalne, a następnie zaimportować je jako dysków na platformie Azure. Ale transferu danych systemu DBMS, który jest ładowany ze środowiska lokalnego do platformy Azure będzie działać przez dyski VHD, które muszą być przygotowane w środowisku lokalnym.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Przenoszenie maszyny Wirtualnej ze środowiska lokalnego do platformy Azure z dyskiem uogólniony
Zamierzasz przenieść określonego systemu SAP ze środowiska lokalnego do platformy Azure (lift- and -shift). Można to zrobić przez przekazanie dysku, który zawiera system operacyjny, pliki binarne SAP i ostateczną DBMS plików binarnych, a także dyski z plikami danych i dziennika systemu DBMS na platformie Azure. W przeciwnym do scenariusza #2 powyżej, możesz pozostawić nazwę hosta, identyfikator SID SAP i SAP kont użytkowników w maszynie Wirtualnej platformy Azure, jak zostały skonfigurowane w środowisku lokalnym. W związku z tym uogólnianie obrazu nie jest konieczne. Ten przypadek dotyczy głównie dla scenariuszy obejmujących wiele lokalizacji, gdzie część środowiskiem SAP jest uruchomiona lokalnie i części na platformie Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Wysoka dostępność i odzyskiwanie po awarii z maszyn wirtualnych platformy Azure
Platforma Azure oferuje następujące funkcje wysokiej dostępności (HA) i odzyskiwania po awarii (DR), które dotyczą różnych składników, której używamy dla wdrożeń SAP i DBMS

### <a name="vms-deployed-on-azure-nodes"></a>Maszyny wirtualne wdrożone w węzłach usługi Azure
Platforma Azure nie oferuje funkcje, takie jak migracja na żywo dla wdrożonych maszyn wirtualnych. Oznacza to, jeśli w klastrze serwerów, na którym wdrożono Maszynę wirtualną niezbędne jest konserwacji, maszyny Wirtualnej musi uzyskać zatrzymana i uruchomiona ponownie. Konserwacji na platformie Azure odbywa się przy użyciu tak zwane domen uaktualnienia w ramach klastrów serwerów. Jest rejestrowana tylko jedną domenę uaktualnienia w danym momencie. Podczas ponownego ma przerwy w świadczeniu usług podczas, gdy maszyna wirtualna jest zamknięta, konserwacja jest przeprowadzana i ponownego uruchomienia maszyny Wirtualnej. Większość dostawców DBMS jednak udostępniają funkcje wysokiej dostępności i odzyskiwania po awarii, które szybko uruchamia usługi DBMS w innym węźle, jeśli węzeł podstawowy jest niedostępny. Platforma Azure oferuje rozdystrybuować uaktualnić domen, aby upewnić się, że planowanej konserwacji lub infrastruktury awarii będzie mają wpływ tylko na małego podzbioru maszyn wirtualnych ani usług maszyn wirtualnych, magazynu i innymi usługami platformy Azure.  Za pomocą dokładnego planowania, jest możliwe uzyskanie poziomy dostępności porównywalne do infrastruktury lokalnej.

Zestawy dostępności usługi Microsoft Azure to logiczne grupowanie maszyn wirtualnych lub usług, które zapewnia maszyn wirtualnych i innych usług są dystrybuowane do różnych domenach błędów i domenach uaktualniania w ramach klastra taki sposób, że może istnieć tylko jeden zamknięcia węzła w każdym punkcie w czasie (odczyt [tego (Linux)] [ virtual-machines-manage-availability-linux] lub [tego (Windows)] [ virtual-machines-manage-availability-windows] artykuł, aby uzyskać więcej informacji).

Należy go skonfigurować cel podczas wdrażania maszyn wirtualnych, jak pokazano tutaj:

![Definicja zestawu dostępności dla systemu DBMS zaświadczanie o kondycji konfiguracji][dbms-guide-figure-200]

Jeśli chcemy utworzyć (niezależnie od poszczególnych DBMS zaświadczanie o kondycji funkcji używanych) o wysokiej dostępności konfiguracje wdrażania systemu DBMS, maszyny wirtualne systemu DBMS należy:

* Dodaj maszyny wirtualne do tej samej sieci wirtualnej platformy Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* Maszyny wirtualne w konfiguracji wysokiej dostępności należy się również w tej samej podsieci. Rozpoznawanie nazw między różnych podsieci nie jest możliwe w przypadku wdrożeń tylko w chmurze działa rozpoznawanie adresu IP. W przypadku wdrożeń obejmujących wiele lokalizacji za pomocą lokacja lokacja lub połączenia usługi ExpressRoute, sieci z co najmniej jedną podsieć jest już ustanowione. Rozpoznawanie nazw odbywa się zgodnie z lokalnej usługi AD zasady i infrastruktury sieciowej. 



#### <a name="ip-addresses"></a>Adresy IP
Zdecydowanie zaleca się konfigurowanie maszyn wirtualnych w przypadku konfiguracji o wysokiej dostępności w taki sposób, odporne na błędy. Opierając się na adresy IP do rozwiązania partnerów wysokiej dostępności w ramach konfiguracji o wysokiej dostępności nie jest niezawodnej platformie Azure, chyba że statyczne adresy IP są używane. Istnieją dwa pojęcia "Zamknij" w systemie Azure:

* Zamykanie systemu za pośrednictwem witryny Azure portal lub programu Azure PowerShell polecenia cmdlet Stop-AzureRmVM: W tym przypadku maszyna wirtualna pobiera Zamknij i ponownie alokowane. Twoje konto platformy Azure nie jest już jest rozliczane dla tej maszyny Wirtualnej, więc tylko opłat, które naliczane są za używany Magazyn. Jeśli jednak nie statyczny prywatny adres IP interfejsu sieciowego, adres IP jest zwalniany i nie gwarantuje, że interfejs sieciowy pobiera stary adres IP, przypisać ponownie po ponownym uruchomieniu maszyny wirtualnej. Wykonywanie zamykania w dół, za pomocą witryny Azure portal lub wywołując automatycznie Stop-AzureRmVM powoduje, że dezalokacji. Jeśli nie chcesz cofnąć alokację maszyny Użyj Stop-AzureRmVM - StayProvisioned 
* Wyłączenie maszyny Wirtualnej z poziomu systemu operacyjnego maszyny Wirtualnej pobiera zamknięta i nie cofać przydziału. Jednak w tym przypadku konta platformy Azure jest nadal są naliczane za maszynę Wirtualną, pomimo faktu, że zamknięcie jest. W takim przypadku przypisanie adresu IP, aby zatrzymana maszyna wirtualna pozostaje niezmieniony. Zamykanie maszyny Wirtualnej z w obrębie nie automatycznie wymusza dezalokacji.

Nawet w przypadku scenariuszy obejmujących wiele lokalizacji domyślnie zamykania i dezalokacji oznacza cofnięcia przypisania adresów IP z maszyny Wirtualnej, nawet jeśli lokalne zasady w ustawieniach protokołu DHCP są różne. 

* Jeśli jeden przypisuje statyczny adres IP do interfejsu sieciowego, jako opisano wyjątek [tutaj][virtual-networks-reserved-private-ip].
* W takim przypadku adres IP zmienia się tak długo, jak interfejs sieciowy nie jest usuwany.

> [!IMPORTANT]
> Aby zachować wdrażania całej proste i łatwe w zarządzaniu, wyczyść zaleca się konfigurowanie maszyn wirtualnych partnerstwo w DBMS wysokiej dostępności lub w konfiguracji odzyskiwania po awarii w ramach platformy Azure w sposób, że działa rozpoznawanie nazw między różnych maszyn wirtualnych związane.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Wdrożenie hosta monitorowania
Wydajność użycia aplikacji SAP na maszynach wirtualnych platformy Azure środowisko SAP wymaga możliwość skorzystania z hosta dane monitorowania z hosty fizyczne uruchomionych maszyn wirtualnych platformy Azure. Określonego poziomu poprawki SAP Agent hosta jest wymagana, pozwalający tę funkcję w SAPOSCOL i Agent hosta SAP. Poziom poprawki dokładnie jest udokumentowany w Uwaga SAP [1409604].

Aby uzyskać szczegółowe informacje dotyczące wdrażania składników, które dostarczają dane hosta SAPOSCOL i Agent hosta SAP i zarządzanie cyklem życia tych składników, zapoznaj się [Podręcznik wdrażania][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Charakterystyka programem Microsoft SQL Server
### <a name="sql-server-iaas"></a>SQL Server w modelu IaaS
Począwszy od programu Microsoft Azure można łatwo zmigrować Twoje istniejące aplikacje programu SQL Server, oparta na platformie systemu Windows Server na maszynach wirtualnych platformy Azure. SQL Server na maszynie wirtualnej, można zmniejszyć całkowity koszt posiadania, wdrażania, zarządzania i konserwacji aplikacji dla przedsiębiorstw szeroki zakres, łatwe migrowanie tych aplikacji w systemie Microsoft Azure. Z programem SQL Server w maszynie wirtualnej platformy Azure Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie. 

> [!IMPORTANT]
> Firma Microsoft nie dyskusji Microsoft Azure SQL Database, która to platforma jako usługa ofertę platformy Microsoft Azure. Dyskusja w tym dokumencie jest o uruchamianiu produktu SQL Server, jaki jest znany dla wdrożeń lokalnych maszynach wirtualnych platformy Azure, korzystając z infrastruktury jako możliwości usługi platformy Azure. Funkcje bazy danych i funkcji między tych dwóch ofert są różne i nie może być mieszane ze sobą. Zobacz też: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Zalecane jest aby przejrzeć [to] [ virtual-machines-sql-server-infrastructure-services] dokumentacji, przed kontynuowaniem.

W poniższych sekcjach rodzajów części dokumentacji w ramach powyższego łącza są agregowane i wymienione. Charakterystyka wokół SAP są również wymienione i niektóre pojęcia są opisane bardziej szczegółowo. Jednak zdecydowanie zalecane jest do pracy z dokumentacją powyżej pierwszy przed odczytaniem dokumentacji specyficzne dla programu SQL Server.

Brak niektórych programu SQL Server w modelu IaaS określone informacje, które należy znać przed kontynuowaniem:

* **Umowę SLA maszyn wirtualnych**: ma umowy SLA dla maszyn wirtualnych działających na platformie Azure, który można znaleźć tutaj: <https://azure.microsoft.com/support/legal/sla/>  
* **Obsługa wersji programu SQL**: dla klientów SAP, firma Microsoft wspiera program SQL Server 2008 R2 lub nowszy na maszynie wirtualnej platformy Microsoft. Wcześniejsze wersje nie są obsługiwane. Przejrzyj ogólnej [oświadczenie pomocy technicznej](https://support.microsoft.com/kb/956893) Aby uzyskać więcej informacji. Należy pamiętać, że ogólnie rzecz biorąc programu SQL Server 2008 jest obsługiwana przez firmę Microsoft również. Jednak z powodu znaczącej funkcjonalności dla rozwiązania SAP, który został wprowadzony przy użyciu programu SQL Server 2008 R2, SQL Server 2008 R2 jest minimalna wersja dla rozwiązania SAP. Należy pamiętać o tym, czy program SQL Server 2012 i 2014 stało się rozszerzony zapewniając lepszą integrację w scenariuszu IaaS (np. wykonywanie kopii zapasowych bezpośrednio w odniesieniu do usługi Azure Storage). W związku z tym będziemy ograniczać przydziału obowiązków w tym dokumencie programu SQL Server 2012 i 2014 z jego poziom ostatniej poprawki dla platformy Azure.
* **Obsługa funkcji SQL**: funkcje najbardziej programu SQL Server są obsługiwane na Microsoft Azure Virtual Machines z pewnymi wyjątkami. **SQL Server Failover Clustering korzystają z udostępnionych dysków nie jest obsługiwana**.  Rozproszone technologii, takich jak dublowania bazy danych, zawsze włączonych grup dostępności, replikacji, aby wysyłanie dziennika i brokera usług, które są obsługiwane w jednym regionie platformy Azure. SQL Server AlwaysOn również jest obsługiwana między różnymi regionami platformy Azure zgodnie z opisem w tym miejscu: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Przegląd [oświadczenie pomocy technicznej](https://support.microsoft.com/kb/956893) Aby uzyskać więcej informacji. Przykład na temat sposobu wdrażania konfiguracji funkcji AlwaysOn znajduje się w [to] [ virtual-machines-workload-template-sql-alwayson] artykułu. Ponadto zapoznaj się z najlepszych rozwiązań, które są udokumentowane [tutaj][virtual-machines-sql-server-infrastructure-services] 
* **Wydajność programu SQL**: mamy pewność, że Microsoft Azure udostępnione maszyny wirtualne wykonywać bardzo dobrze porównaniu innych ofert wirtualizacji chmury publicznej, ale poszczególne wyniki mogą się różnić. Zapoznaj się z [to] [ virtual-machines-sql-server-performance-best-practices] artykułu.
* **Przy użyciu obrazów z portalu Azure Marketplace**: najszybszy sposób, aby wdrożyć nową maszynę Wirtualną Azure firmy Microsoft jest użycie obrazu z witryny Azure Marketplace. Brak obrazów w portalu Azure Marketplace, które zawierają programu SQL Server. Nie można od razu używać obrazów programu SQL Server już zainstalowanym dla aplikacji SAP NetWeaver. Przyczyną jest to domyślne sortowanie programu SQL Server jest zainstalowane w ramach tych obrazów i nie sortowania wymaganych przez systemy oprogramowania SAP NetWeaver. Aby można było używać tych obrazów, zobacz, jakie kroki opisane w rozdziale [przy użyciu obrazu programu SQL Server z witryny Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Zapoznaj się z [— szczegóły cennika](https://azure.microsoft.com/pricing/) Aby uzyskać więcej informacji. [Przewodnik licencjonowania programu SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) i [wskazówki dotyczące licencjonowania programu SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) są również ważnych zasobów.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania programu SQL Server dla instalacji SAP związane z programu SQL Server w maszynach wirtualnych platformy Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Zalecenia dotyczące maszyny Wirtualnej/VHD struktury dla wdrożeń SAP związane z programu SQL Server
Zgodnie z ogólny opis plików wykonywalnych programu SQL Server należy się lub zainstalowany na dysku systemowym dysku systemu operacyjnego maszyny Wirtualnej (dysk C:\).  Zazwyczaj większość systemowych baz danych programu SQL Server nie mogą być wykorzystane na wysokim poziomie obciążenia SAP NetWeaver. Dlatego systemowych baz danych programu SQL Server (master, msdb i modelu) może pozostać na tym dysku C:\. Wyjątek może być bazy danych tempdb, co w przypadku niektórych SAP ERP i wszystkich obciążeń BW, mogą wymagać większą ilość danych lub operacji We/Wy operacji wolumin, który nie mieści się w oryginalnej maszyny Wirtualnej. W tych systemach można wykonać następujące czynności:

* Przenieś pliki danych głównej bazy danych tempdb na tym samym dysku logicznego, pliki danych podstawowych bazy danych SAP.
* Dodaj wszelkie dodatkowe bazy danych tempdb pliki danych do każdego z innych dysków logicznych zawierającego plik danych bazy danych SAP.
* Dodaj plik dziennika bazy danych tempdb na dysk logiczny, który zawiera plik dziennika bazy danych użytkownika.
* **Przeznaczona wyłącznie do celów typy maszyn wirtualnych, korzystających z lokalne dyski SSD** na węzeł obliczeń bazy danych tempdb i dziennika pliki mogą zostać umieszczone na dysku D:\. Niemniej jednak może być zalecane użycie wiele plików danych bazy danych tempdb. Należy pamiętać, że woluminy dysku D:\ różnią się na podstawie typu maszyny Wirtualnej.

Te konfiguracje włączyć bazę danych tempdb zużywać więcej miejsca niż dysk systemowy jest w stanie zapewnić. Aby określić rozmiar odpowiednie bazy danych tempdb, jeden Sprawdź rozmiary bazy danych tempdb na istniejących systemów, które działają lokalnie. Ponadto taka konfiguracja umożliwiłby liczby operacji We/Wy względem bazy danych tempdb, które nie mogą być określane przy użyciu dysku systemowego. Ponownie systemy, które są uruchomione w siedzibie firmy może służyć do monitorowania obciążenia We/Wy względem bazy danych tempdb, tak aby może pochodzić liczby operacji We/Wy, które powinny być widoczne na bazy danych tempdb.

Konfiguracja maszyny Wirtualnej, na którym działa program SQL Server z bazą danych SAP i gdzie bazy danych tempdb i bazy danych tempdb w pliku dziennika są umieszczane na dysku D:\ będzie wyglądać:

![Odwołanie do konfiguracji maszyny wirtualnej IaaS platformy Azure dla rozwiązania SAP][dbms-guide-figure-300]

Należy pamiętać, że na dysku D:\ ma zależne od typu maszyny Wirtualnej o różnych rozmiarach. Zależne od wymagany rozmiar bazy danych tempdb użytkownik może zostać wymuszone parę plików danych i dziennika bazy danych tempdb z SAP bazy danych plików danych i dziennika w przypadku gdy dysku D:\ jest za mały.

#### <a name="formatting-the-disks"></a>Formatowanie dysków
Dla programu SQL Server NTFS block rozmiar dysków zawierających dane programu SQL Server i pliki dziennika powinny być 64 KB. Nie ma potrzeby do sformatowania dysku D:\. Ten dysk jest wstępnie sformatowane.

Aby upewnić się, że przywracania lub tworzenia baz danych jest nie zainicjował pliki danych poprzez wyzerowanie zawartości tych plików, jeden upewnij się, że kontekst użytkownika, którego usługi SQL Server jest uruchomiona w określonych uprawnień. Zazwyczaj użytkownicy w grupie administratorów Windows mają te uprawnienia. Jeśli usługi SQL Server uruchamiany w kontekście użytkownika bez uprawnień administratora na Windows użytkownika, musisz przypisać ten użytkownik uprawnienie użytkownika **wykonywanie zadań konserwacji woluminów**.  Zobacz szczegółowe informacje w tym artykule bazy wiedzy firmy Microsoft: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Wpływ kompresja bazy danych
W konfiguracji, gdy przepustowość operacji We/Wy może stać się czynnikiem ograniczającym Każda miara, co zmniejsza operacje We/Wy może przyczynić się do rozproszonego obciążenia, jakie można jedno uruchomienie w scenariuszu IaaS, takich jak platforma Azure. W związku z tym jeśli nie zostały jeszcze zrobione, zastosowanie kompresji strony serwera SQL jest zalecane zarówno przez SAP i Microsoft przed przekazaniem istniejącej bazy danych SAP na platformie Azure.

Zalecane do wykonania kompresji bazy danych przed przekazaniem do usługi Azure znajduje się z dwóch powodów:

* Ilość danych do przekazania jest niższa.
* Czas trwania wykonania kompresji jest krótszy, przy założeniu, że jeden służy silniejsze sprzętu z więcej procesorów ani większą przepustowość operacji We/Wy lub mniej operacji We/Wy opóźnienia w środowisku lokalnym.
* Mniejsze rozmiary bazy danych może prowadzić do mniejsze koszty przydział dysku

Kompresja bazy danych działa również na maszynach wirtualnych platformy Azure, jak w środowisku lokalnym. Aby uzyskać więcej informacji na temat sposobu skompresować istniejącej bazy danych programu SQL Server SAP Sprawdź tutaj: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>Program SQL Server 2014 — zapisywanie plików bazy danych bezpośrednio w usłudze Azure Blob Storage
Program SQL Server 2014 otwiera możliwości, które mają być przechowywane pliki bazy danych bezpośrednio na Store obiektów Blob platformy Azure bez "otokę" wokół nich wirtualnego dysku twardego. Szczególnie w przypadku przy użyciu standardowego magazynu platformy Azure lub mniejsze typy maszyn wirtualnych dzięki temu scenariuszy, w którym można rozwiązać, limity operacji We/Wy, które będzie wymuszane przez ograniczoną liczbę dysków, które mogą być instalowane na niektórych typów mniejszych maszyny Wirtualnej. Działa to w przypadku baz danych użytkownika, ale nie dla systemowych baz danych programu SQL Server. Działa dla plików danych i dziennika programu SQL Server. Jeśli chcesz wdrożyć bazę danych programu SQL Server SAP w ten sposób zamiast "zawijania" je do wirtualnych dysków twardych, pamiętać o następujących:

* Konto magazynu używane musi znajdować się w tym samym regionie platformy Azure jako taki, który jest używany do wdrażania maszyn wirtualnych SQL Server jest uruchomiony w.
* Wymienione wcześniej dotyczące dystrybucji wirtualnych dysków twardych na różnych kontach magazynu Azure kwestie dla tej metody, a także wdrożeń. Oznacza, że liczba operacji We/Wy na wartości konta usługi Azure Storage.

[comment]: <> (MSSedusch TODO, ale zostaną użyte przepustowości sieci i nie magazynu przepustowości, PRAWDA?)

Poniżej przedstawiono szczegółowe informacje o tym typie wdrożenia: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Aby można było przechowywać pliki danych programu SQL Server bezpośrednio w usłudze Azure Premium Storage, musisz mieć minimalną wydaniu poprawek programu SQL Server 2014, który opisano tutaj: <https://support.microsoft.com/kb/3063054>. Przechowywanie plików danych programu SQL Server w usłudze Azure Storage Standard działa z wydanej wersji programu SQL Server 2014. Jednak bardzo same poprawki zawierać innej serii poprawki, które powodują, że bezpośredniego użycia usługi Azure Blob Storage dla programu SQL Server, plikami danych i kopii zapasowych jest bardziej niezawodna. Dlatego zaleca się, ogólnie rzecz biorąc przy użyciu tych poprawek.

### <a name="sql-server-2014-buffer-pool-extension"></a>Rozszerzenie puli buforów programu SQL Server 2014
Program SQL Server 2014 wprowadzono nową funkcję, która nosi nazwę rozszerzenia puli buforów. Ta funkcja rozszerzenie puli buforów dla programu SQL Server, który jest przechowywany w pamięci z drugiego poziomu pamięcią podręczną, która jest wspierana przez lokalny SSD serwera lub maszyny Wirtualnej. Umożliwia to zapewnienie większy zestaw roboczy danych "w pamięci". W porównaniu do uzyskiwania dostępu do usługi Azure Standard Storage dostęp do rozszerzenia puli buforów, które są przechowywane na lokalnych dyskach SSD maszyny wirtualnej portalu Azure jest wiele czynników szybciej.  W związku z tym korzystając z lokalnego dysku D:\ typów maszyn wirtualnych mających doskonałą operacje We/Wy i przepływność może być bardzo racjonalny sposób, aby zmniejszyć obciążenie operacje We/Wy w ramach usługi Azure Storage i znacznie poprawić czasy odpowiedzi dla zapytań. Dotyczy to zwłaszcza w przypadku, gdy nie używa magazynu w warstwie Premium. W przypadku usługi Premium Storage i użycie pamięci podręcznej odczytu platformy Azure — wersja Premium w węźle obliczeniowym zgodnie z zaleceniami dla plików danych, powinny nie między nimi istotne różnice. Przyczyną jest to, że zarówno pamięci podręcznych (rozszerzenie puli buforów programu SQL Server i pamięci podręcznej odczytu magazynu Premium) używają dysków lokalnych węzłów obliczeniowych.
Aby uzyskać więcej informacji na temat tej funkcji w dokumentacji tego: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Informacje dotyczące tworzenia kopii zapasowej/odzyskiwania programu SQL Server
W przypadku wdrażania programu SQL Server na platformę Azure muszą być przejrzane w swojej metodologii kopii zapasowej. Nawet, jeśli system nie jest wydajność systemu, bazy danych SAP, obsługiwanej przez program SQL Server musi być kopię zapasową okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, kopia zapasowa jest teraz mniej ważne w odniesieniu do wyrównującej awarii magazynu. Przyczyna priorytet utrzymywania właściwego planu tworzenia kopii zapasowych i odzyskiwania jest inne, które można kompensowane błędy logiczne/ręczne, zapewniając punktu w czasie możliwości odzyskiwania. Dlatego celem jest albo użyj kopii zapasowych, aby przywrócić bazę danych do pewnego momentu w czasie lub używać kopie zapasowe na platformie Azure, aby zapełnić innego systemu przez skopiowanie istniejącej bazy danych. Na przykład można przeniesiesz z konfiguracji SAP warstwy 2 do 3-warstwowej systemu BIOS tego samego systemu przez przywrócenie kopii zapasowej.

Istnieją trzy różne sposoby tworzenia kopii zapasowych programu SQL Server do usługi Azure Storage:

1. SQL Server 2012 CU4 i nowszej natywnie utworzyć kopię zapasową bazy danych do adresu URL. To jest szczegółowo opisane w blogu [nową funkcję rozszerzeń i przywracania kopii zapasowych programu SQL Server 2014 — część 5 —](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Zobacz rozdział [programu SQL Server 2012 SP1 CU4 lub nowszy][dbms-guide-5.5.1].
2. Wersje programu SQL Server przed SQL 2012 CU4 umożliwia funkcji przekierowania kopii zapasowej na dysku VHD i po prostu przejścia strumienia zapisu do lokalizacji usługi Azure Storage, który został skonfigurowany. Zobacz rozdział [programu SQL Server 2012 z dodatkiem SP1 CU3 i jego starszych wersji][dbms-guide-5.5.2].
3. Końcowe metoda polega na wykonanie konwencjonalnych kopii zapasowej programu SQL Server do polecenia dysku na dysk. To jest taka sama jak wzorca wdrażania w środowisku lokalnym i nie omówiono szczegółowo w tym dokumencie.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 lub nowszy
Ta funkcja umożliwia bezpośrednie wykonywanie kopii zapasowej na usłudze Azure BLOB storage. Bez tej metody należy kopii zapasowej na innych dyskach, które będą używać dysku i pojemność operacji We/Wy. Chodzi o to, po prostu to:

 ![Tworzenie kopii zapasowych programu SQL Server 2012 Microsoft Azure Storage BLOB][dbms-guide-figure-400]

Zaletą jest w tym przypadku, że jeden nie musieli poświęcać dysków do przechowywania kopii zapasowych programu SQL Server na. Dlatego należy mniejszą liczbę dysków przydzielonych oraz przepustowość całego dysku, operacje We/Wy może być używana dla plików danych i dziennika. Należy pamiętać, że maksymalny rozmiar kopii zapasowej jest ograniczona do maksymalnie 1 TB, zgodnie z opisem w sekcji **ograniczenia** w tym artykule: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Jeśli rozmiar kopii zapasowej, pomimo kompresję kopii zapasowej programu SQL Server spowoduje przekroczenie 1 TB, rozmiar, funkcje opisane w rozdziale [programu SQL Server 2012 z dodatkiem SP1 CU3 i jego starszych wersji] [ dbms-guide-5.5.2] w tym dokumencie musi być używane.

[Dokumentację pokrewną](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) opisujące przywracania baz danych z kopii zapasowych dla usługi Azure Blob Store zaleca, aby nie można przywrócić bezpośrednio z magazynu obiektów BLOB platformy Azure, jeśli kopia zapasowa jest > 25 GB. Zalecenia w tym artykule jest oparty na zagadnienia związane z wydajnością, a nie ze względu na ograniczenia funkcjonalności. W związku z tym różne warunki mogą stosować na podstawie przypadku.

Dokumentację dotyczącą sposobu konfigurowania i wykorzystać tego typu kopii zapasowej można znaleźć w [to](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) samouczek

Przykład sekwencji kroków, które mogą być odczytywane [tutaj](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Automatyzacja tworzenia kopii zapasowych, jest najwyższej ważności, aby upewnić się, że obiekty BLOB dla każdej kopii zapasowej są nazwane w różny sposób. W przeciwnym razie zostaną zastąpione i łańcuch restore został przerwany.

Aby nie mieszały się elementy między trzy różne typy kopii zapasowych zaleca się tworzenie różnych kontenerów poniżej konto magazynu używane do przechowywania kopii zapasowych. Kontenery można przez maszynę Wirtualną tylko lub przez maszynę Wirtualną i typ kopii zapasowej. Schemat może wyglądać następująco:

 ![Tworzenie kopii zapasowych programu SQL Server 2012 Microsoft Azure Storage BLOB - różnych kontenerów w ramach oddzielnego konta magazynu][dbms-guide-figure-500]

W powyższym przykładzie nie jest wykonywane kopie zapasowe do tego samego konta magazynu, w której maszyny wirtualne są wdrażane. Może to być nowe konto magazynu dla kopii zapasowych. W ramach konta magazynu będzie różnych kontenerów utworzone za pomocą macierzy typu kopii zapasowej i nazwę maszyny Wirtualnej. Takie Segmentacja ułatwia administrowanie kopii zapasowych różnych maszyn wirtualnych.

Obiekty BLOB, który bezpośrednio zapisu kopii zapasowych, nie dodawania do łącznej liczby danych dyski maszyny Wirtualnej. Dlatego jeden może zwiększyć maksymalną liczbę dysków z danymi zainstalowany z określonej jednostki SKU maszyny Wirtualnej dla danych i pliku dziennika transakcji i nadal wykonywać kopii zapasowej z kontenera magazynu. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 z dodatkiem SP1 CU3 i jego starszych wersji
Pierwszym krokiem, należy wykonać w celu uzyskania kopii zapasowej bezpośrednio w odniesieniu do usługi Azure Storage będzie można pobrać plik msi, która jest połączona z [to](https://www.microsoft.com/download/details.aspx?id=40740) KBA artykułu.

Plik instalacyjny pobierania x64 i dokumentację. Plik instaluje program o nazwie: **kopii zapasowej programu Microsoft SQL Server, aby narzędzie platformy Microsoft Azure**. Dokładnie zapoznaj się z dokumentacją produktu.  Narzędzie jest zasadniczo działa w następujący sposób:

* Ze strony serwera SQL jest zdefiniowany lokalizacji na dysku przeznaczonego na kopie zapasowe programu SQL Server (nie używaj dysku D:\ jako lokalizacji).
* To narzędzie umożliwia definiowanie zasad, które mogą służyć do kierowania poszczególnych typów kopii zapasowych do różnych kontenerów usługi Azure Storage.
* Gdy zasady są w miejscu, narzędzie przekierowuje strumienia zapisu kopii zapasowej do jednego z wirtualnych dysków twardych/dysków do lokalizacji magazynu platformy Azure, która została wcześniej zdefiniowana.
* Narzędzie nie powoduje usunięcia pliku skrótowa małe kilka KB rozmiaru wirtualnego dysku twardego/dysku, który został zdefiniowany dla programu SQL Server kopii zapasowej. **Ten plik powinien pozostać w lokalizacji magazynu, ponieważ jest wymagana do przywrócenia ponownie z usługi Azure Storage.**
  * Jeśli wybrano opcję tworzenia kopii zapasowych do konta usługi Microsoft Azure Storage możesz utracić plik szczątkowy (na przykład za pośrednictwem utratą nośnika magazynowania, który zawiera plik szczątkowy), może odzyskać plik szczątkowy za pośrednictwem usługi Microsoft Azure Storage, pobierając ją z kontenera magazynu, w którym został umieszczony. Umieść plik szczątkowy do folderu na komputerze lokalnym, w którym narzędzie jest skonfigurowane do wykrywania i przekazać do tego samego kontenera przy użyciu tego samego hasła szyfrowania, jeśli szyfrowanie był używany z oryginalnej reguły. 

Oznacza to, że schemat, jak opisano powyżej dla nowszej wersji programu SQL Server, które można umieścić w miejscu, także dla wersji programu SQL Server, które nie zezwalają na bezpośredni adres lokalizacji usługi Azure Storage.

Nie należy używać tej metody za pomocą nowszej wersji programu SQL Server, które obsługuje wykonywania kopii zapasowych natywnie w ramach usługi Azure Storage. Wyjątki są, gdzie ograniczenia natywnego wykonywania kopii zapasowych na platformie Azure blokują natywnego wykonywania kopii zapasowej na platformie Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Inne możliwości, aby utworzyć kopię zapasową bazy danych programu SQL Server
Inne możliwości, aby utworzyć kopię zapasową bazy danych jest dołączaj dodatkowe dyski danych do maszyny Wirtualnej, którego używasz do przechowywania kopii zapasowych na. W takim przypadku należałoby upewnij się, że dyski nie są uruchomione pełne. Jeśli tak jest rzeczywiście, będziesz potrzebować odinstaluj dyski i dlatego do speak "Archiwizuj" go i zastąp go nowego pustego dysku. Jeśli w tej ścieżce są wyłączane, chcesz zachować te wirtualne dyski twarde na oddzielnych kontach magazynu Azure z tymi, wirtualne dyski twarde z plikami bazy danych.

Druga możliwość polega na użyciu dużą maszynę Wirtualną, która może mieć wiele dysków dołączonych, na przykład D14 o 32VHDs. Użyj funkcji miejsca do magazynowania do tworzenia elastyczne środowisko, w którym można utworzyć udziały, które są następnie używane jako obiekty docelowe kopii zapasowej dla różnych serwerów systemu DBMS.

Stało się opisano niektóre z najważniejszych wskazówek [tutaj](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) także. 

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące tworzenia kopii zapasowych/przywracania
Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania jest zależna od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Ponadto użycie procesora CPU, używane przez kompresja kopii zapasowej może odtwarzać istotną rolę na maszynach wirtualnych z maksymalnie osiem wątki procesora CPU. W związku z tym można założyć:

* Mniej liczbę dysków używanych do przechowywania danych plików, mniejsze ogólną przepustowość podczas odczytu.
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej.
* Mniejszą liczbę elementów docelowych, (obiekty BLOB, wirtualnych dysków twardych lub dysków), które można zapisać kopii zapasowej do mniejszej przepływności.
* Im mniejszy rozmiar maszyny Wirtualnej, mniejszy przepływności przydział magazynowania zapisywania i odczytywania danych z usługi Azure Storage. Niezależnie od tego, czy kopie zapasowe są przechowywane bezpośrednio w usłudze Azure Blob lub czy są one przechowywane w plikach VHD, które ponownie są przechowywane w obiektach blob platformy Azure.

Korzystając z programu Microsoft Azure Storage BLOB jako cel kopii zapasowej w nowszej wersji, możesz są ograniczone do wyznaczania tylko jeden obiekt docelowy adresu URL dla każdej określonej kopii zapasowej.

Jednak korzystając z "Microsoft SQL Server kopii zapasowej do narzędzia Microsoft Azure" w starszych wersjach, można zdefiniować więcej niż jeden element docelowy pliku. Więcej niż jeden cel kopii zapasowej można skalować i przepływność kopii zapasowej jest większa. To spowoduje następnie w wielu plikach, jak również w ramach konta usługi Azure Storage. Podczas testowania, przy użyciu wielu plików miejsc docelowych można zdecydowanie osiągnąć przepustowości, który można osiągnąć przy użyciu rozszerzeń kopii zapasowej, zaimplementowane w z programu SQL Server 2012 SP1 CU4 na. Możesz również nie są blokowane przez limit 1TB, tak jak natywnego wykonywania kopii zapasowych na platformie Azure.

Jednak należy pamiętać, przepływność również zależy od lokalizacji konta magazynu platformy Azure, użyj przeznaczonego na kopie zapasowe. Pomysł, może być Znajdź konto magazynu w regionie innym niż maszyny wirtualne działają. Na przykład czy Uruchom konfigurację maszyny Wirtualnej w Europie Zachodniej, ale Umieść konto magazynu, która umożliwia wykonywanie kopii zapasowej przed Europa Północna. Bez obaw, ma wpływ na wydajność tworzenia kopii zapasowych i prawdopodobnie nie można wygenerować przepływność 150MB/s, ponieważ wydaje się być możliwe w przypadku, gdy docelowy magazyn i maszyny wirtualne są uruchomione w tym samym regionalnym centrum danych.

#### <a name="managing-backup-blobs"></a>Zarządzanie obiektami blob kopii zapasowej
Istnieje konieczność zarządzania kopiami zapasowymi na własną rękę. Ponieważ oczekuje się, że wiele obiektów blob są tworzone, wykonując kopie zapasowe dziennika transakcji częste, administracja tych obiektów blob można łatwo przeciążenie witryny Azure portal. Dlatego jest recommendable korzystanie z Eksploratora usługi Azure storage. Istnieje kilka dobre dostępnymi, które mogą ułatwić zarządzanie kontem usługi Azure storage

* Microsoft Visual Studio przy użyciu zestawu SDK platformy Azure zainstalowany (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* Narzędzia innych firm

Aby uzyskać bardziej szczegółowe omówienie tworzenia kopii zapasowej i SAP na platformie Azure, zapoznaj się [przewodniku kopia zapasowa SAP](sap-hana-backup-guide.md) Aby uzyskać więcej informacji.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Przy użyciu obrazu programu SQL Server z witryny Microsoft Azure Marketplace
Firma Microsoft oferuje maszyn wirtualnych w portalu Azure Marketplace, które już zawierają wersje programu SQL Server. Klienci systemu SAP, którzy wymagają licencji programu SQL Server i Windows może to być szansą na zasadzie pokrycie potrzebę licencji, która umożliwia uruchomienie maszyn wirtualnych z już zainstalowanym programem SQL Server. Aby można było używać tych obrazów dla rozwiązania SAP, należy wprowadzić następujące kwestie:

* Wersje inne niż w wersji ewaluacyjnej programu SQL Server uzyskują wyższe koszty niż "Windows-only" maszyny Wirtualnej wdrożonej w portalu Azure Marketplace. Zobacz następujące artykuły, aby porównać ceny: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> i <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Można używać tylko wersji programu SQL Server, które są obsługiwane przez oprogramowanie SAP, takich jak SQL Server 2012.
* Sortowania wystąpienia programu SQL Server, który jest instalowany na maszynach wirtualnych oferowanych w portalu Azure Marketplace nie jest sortowanie oprogramowania SAP NetWeaver wymaga wystąpienia programu SQL Server do uruchomienia. Możesz zmienić sortowanie, jednak z instrukcjami w poniższej sekcji.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Zmiana sortowania programu SQL Server z maszyną Wirtualną z Microsoft Windows/SQL Server
Ponieważ obrazy programu SQL Server w witrynie Azure Marketplace nie jest skonfigurowany do korzystania z sortowania, który jest wymagany przez aplikacje środowiska SAP NetWeaver, trzeba zmienić natychmiast po wdrożeniu. Dla programu SQL Server 2012 można to zrobić wykonując następujące kroki natychmiast po wdrożeniu maszyny Wirtualnej i administrator będzie mógł zalogować się do wdrożonej maszyny Wirtualnej:

* Otwórz okno poleceń programu Windows jako administrator.
* Zmień katalog na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Wykonaj polecenie: Setup.exe/quiet/Action = REBUILDDATABASE InstanceName = parametr/SQLSYSADMINACCOUNTS MSSQLSERVER =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> to konto, która została zdefiniowana jako konto administratora, podczas wdrażania maszyny Wirtualnej po raz pierwszy przy użyciu galerii.

Proces powinien potrwać kilka minut. Aby upewnić się, czy krok zakończył przy użyciu prawidłowego wyniku, wykonaj następujące czynności:

* Otwórz program SQL Server Management Studio.
* Otwórz okno zapytania.
* Wykonaj sp_helpsort polecenia w bazie danych master programu SQL Server.

Oczekiwany wynik powinien wyglądać podobnie jak:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Jeśli nie jest to wynik, Zatrzymaj wdrażanie SAP i zbadać, dlaczego polecenia Instalatora nie działał zgodnie z oczekiwaniami. Wdrożenie oprogramowania SAP NetWeaver aplikacje na wystąpieniu programu SQL Server przy użyciu innego programu SQL Server strony kodowe niż wymienione powyżej jest **nie** obsługiwane.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server o wysokiej dostępności dla oprogramowania SAP na platformie Azure
Jak wspomniano wcześniej w tym dokumencie, nie ma możliwości do utworzenia magazynu udostępnionego, co jest niezbędne do użycia najstarsze funkcji wysokiej dostępności programu SQL Server. Te funkcje zostaną zainstalowane co najmniej dwóch wystąpień programu SQL Server w Windows Server Failover klastra (WSFC) dla baz danych użytkownika (i ostatecznie bazy danych tempdb) przy użyciu udostępnionego dysku. Jest to metoda standardowa wysokiej dostępności dużo czasu, co jest obsługiwane przez firmę SAP. Ponieważ platforma Azure nie obsługuje magazynu udostępnionego, konfiguracjach wysokiej dostępności programu SQL Server w konfiguracji klastra udostępnionego dysku nie rzędu milionów dolarów. Wiele metod o wysokiej dostępności są jednak nadal możliwe i są opisane w poniższych sekcjach.

#### <a name="sql-server-log-shipping"></a>Wysyłanie dziennika programu SQL Server
Jest jedną z metod wysokiej dostępności (HA), aby wysyłanie dziennika serwera SQL. Jeśli działa rozpoznawanie nazw maszyn wirtualnych w konfiguracji wysokiej dostępności, problem nie występuje i konfiguracji na platformie Azure różnią się od wszelkich konfiguracji, która jest wykonywane w środowisku lokalnym. Nie zaleca się opierać się na tylko rozpoznawania adresu IP. W odniesieniu do konfigurowania wysyłania dzienników i zasady dotyczące wysyłania dzienników, sprawdź tej dokumentacji:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

W celu osiągnięcia wysokiej dostępności, należy wdrożyć maszyny wirtualne, które są w ramach takich wysyłanie dziennika konfiguracji mieścić się w tym samym zestawie dostępności platformy Azure.

#### <a name="database-mirroring"></a>Funkcja dublowania baz danych
Funkcja dublowania bazy danych, zgodnie z obsługiwanych przez oprogramowanie SAP (patrz Uwaga SAP [965908]) opiera się na temat definiowania partnerski trybu failover w parametrach połączenia SAP. W przypadkach między środowiskami lokalnymi przyjęto założenie, że dwie maszyny wirtualne znajdują się w tej samej domenie i że wystąpień programu SQL Server w kontekście dwóch użytkowników są uruchomione przez użytkownika domeny i masz wystarczające uprawnienia w związane dwa wystąpienia programu SQL Server. W związku z konfiguracją funkcji dublowania baz danych na platformie Azure nie różnią się typowe lokalnej/w konfiguracji.

Począwszy od wdrożeń tylko w chmurze to najłatwiejsza metoda jest zapewnienie innej konfiguracji domeny na platformie Azure, te maszyny wirtualne systemu DBMS (i najlepiej dedykowanych SAP maszyn wirtualnych) w ramach jednej domeny.

Jeśli domena nie jest możliwe, jeden certyfikatów można także użyć dla bazy danych, dublowanie punktów końcowych, zgodnie z opisem w tym miejscu: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Samouczek do skonfigurowania funkcji dublowania baz danych na platformie Azure można znaleźć tutaj: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>Zawsze włączone programu SQL Server
Jak zawsze włączone jest obsługiwana dla SAP w środowisku lokalnym (patrz Uwaga SAP [1772688]), jest ona obsługiwana ma być używany w połączeniu z oprogramowaniem SAP na platformie Azure. Fakt, że nie jesteś w stanie tworzenia udostępnionych dysków na platformie Azure nie oznacza, że nie można utworzyć jedną konfigurację zawsze na Windows Server Failover klastra (WSFC), między różnych maszyn wirtualnych. Oznacza jedynie, że nie masz możliwość używania udostępnionego dysku jako kworum w konfiguracji klastra. Dlatego można skompilować konfiguracji zawsze na usługi WSFC na platformie Azure i nie wybierz typ kworum, która korzysta z udostępnionego dysku. Środowisko platformy Azure te maszyny wirtualne są wdrażane w powinien resolve maszyn wirtualnych według nazwy i maszyn wirtualnych powinna być w tej samej domenie. Dotyczy to tylko Azure i wdrożeń obejmujących wiele lokalizacji. Istnieją pewne specjalne zagadnienia dotyczące praktyki wdrażania odbiornika grupy dostępności programu SQL Server (nie należy mylić z zestawu dostępności platformy Azure), ponieważ platforma Azure w tym momencie nie zezwala na do utworzenia obiektu AD/DNS, ponieważ jest to możliwe w środowisku lokalnym. Dlatego niektóre kroki instalacji różnych są niezbędne w celu wyeliminowania określone zachowanie platformy Azure.

Niektóre kwestie, przy użyciu odbiornika grupy dostępności są:

* Przy użyciu odbiornika grupy dostępności jest możliwe tylko w systemie Windows Server 2012 lub nowszego jako systemu operacyjnego gościa maszyny wirtualnej. Dla systemu Windows Server 2012 należy się upewnić, że ta poprawka jest stosowana: <https://support.microsoft.com/kb/2854082> 
* Ta poprawka dla systemu Windows Server 2008 R2, nie istnieje. i zawsze musi można używać w taki sam sposób jak dublowania bazy danych, określając partnerski trybu failover w parametrach połączenia (zrobić za pomocą default.pfl parametru baz danych/mss/serwerem SAP — patrz Uwaga SAP [965908]).
* Przy użyciu odbiornika grupy dostępności, maszyny wirtualne bazy danych muszą być podłączone do dedykowanych modułu równoważenia obciążenia. Do rozpoznawania nazw w przypadku wdrożeń tylko w chmurze albo wymagałoby wszystkich maszyn wirtualnych z systemem SAP (serwery aplikacji, bazami danych serwera i serwera () SCS) znajdują się w tej samej sieci wirtualnej lub z warstwy aplikacji SAP wymaga obsługi plików etc\host w kolejności Aby uzyskać nazwy maszyn wirtualnych maszyn wirtualnych serwera SQL, które zostały rozwiązane. W celu uniknięcia, czy Azure przypisuje nowe adresy IP w przypadku obu maszyn wirtualnych przy okazji zamykania, jeden należy przypisać statyczne adresy IP interfejsów sieciowych maszyn wirtualnych w konfiguracji Always On (Definiowanie statyczny adres IP jest opisane w [to] [ virtual-networks-reserved-private-ip] artykułu)

[comment]: <> (Stary blogi)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Istnieją specjalne kroki wymagane w przypadku tworzenia konfiguracji klastra usługi WSFC, gdy klaster musi specjalny adres IP przypisany, ponieważ platformę Azure za pomocą jej bieżącą funkcjonalność przypisywanej nazwy klastra ten sam adres IP jako węzeł klastra jest tworzony na. Oznacza to, że czynność ręczna należy wykonać, aby przypisać inny adres IP do klastra.
* Odbiornik grupy dostępności, będzie można utworzyć na platformie Azure za pomocą punktów końcowych protokołu TCP/IP, które są przypisane do maszyn wirtualnych z systemem podstawowych i pomocniczych replik grupy dostępności.
* Być może trzeba zabezpieczyć tych punktów końcowych przy użyciu list kontroli dostępu.

[comment]: <> (Blog stare TODO)
[comment]: <> (Szczegółowy opis kroków i artykuły pierwszej potrzeby instalowania konfiguracji funkcji AlwaysOn na platformie Azure najbardziej doświadczonych podczas Instruktaż samouczek dostępny [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Wstępnie skonfigurowane instalacji funkcji AlwaysOn za pośrednictwem galerii systemu Azure <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Tworzenie odbiornika grupy dostępności jest najlepiej opisać w samouczku [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comment]: <> (Zabezpieczanie punktów końcowych sieci za pomocą list ACL są wyjaśnione najlepiej tutaj:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Istnieje możliwość wdrażania programu SQL Server zawsze włączonej grupy dostępności za pośrednictwem różnych regionów platformy Azure również. Ta funkcja korzysta z połączenia platformy Azure sieci wirtualnej między sieciami wirtualnymi ([szczegółowe][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Blog stare TODO)
[comment]: <> (Ustawienia grup dostępności AlwaysOn programu SQL Server, w tym scenariuszu opisano tutaj: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Podsumowanie informacji na temat wysokiej dostępności programu SQL Server na platformie Azure
Biorąc pod uwagę fakt, że usługi Azure Storage chroni zawartość, jest mniej powodem nalegać na obrazie stałej gotowości. Oznacza to, że scenariusz wysokiej dostępności trzeba chronić tylko względem następujących przypadkach:

* Niedostępność maszyny wirtualnej jako całości z powodu konserwacji na serwerze klastra na platformie Azure lub inne przyczyny
* Problemów z oprogramowaniem w wystąpieniu programu SQL Server
* Ochrona przed ręczne błędów, gdzie danych zostaje usunięta, a w momencie odzyskiwania jest wymagana

Patrząc dopasowania technologii, w których jeden można dokumentu uważają, pierwsze dwa przypadki mogą być objęte dublowania bazy danych lub zawsze na trzeci przypadek tylko mogą być objęte wysyłania dziennika.

Muszą równoważyć bardziej złożonych konfiguracji zawsze włączone i dublowania bazy danych, z zalet zawsze włączone. Mogą być wyświetlane te korzyści, takich jak:

* Repliki pomocnicze możliwe.
* Tworzenie kopii zapasowych z replik pomocniczych.
* Lepszą skalowalność.
* Więcej niż jedną replikę pomocniczą.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Ogólne programu SQL Server dla SAP w systemie Azure podsumowanie
Istnieje wiele zaleceń w tym przewodniku, a zalecane jest przeczytanie on więcej niż jeden raz przed rozpoczęciem Planowanie wdrożenia usługi Azure. Ogólnie rzecz biorąc, należy postępować zgodnie dziesięć najważniejszych ogólne systemu DBMS na Azure określonych punktów:

[comment]: <> (2.3 wyższą przepływność niż co? Niż jeden wirtualny dysk twardy?)
1. Użyj najnowszej wersji systemu DBMS, takich jak SQL Server 2014, który ma większość korzyści na platformie Azure. Dla programu SQL Server to SQL Server 2012 SP1 CU4, który zamieści funkcji wykonywania kopii w odniesieniu do usługi Azure Storage. Jednak w połączeniu z oprogramowaniem SAP zaleca się korzystanie co najmniej SQL Server 2014 SP1 pakietu CU1 lub SQL Server 2012 z dodatkiem SP2 i najnowszej aktualizacji zbiorczej.
2. Należy dokładnie zaplanować środowiska systemu SAP na platformie Azure równoważenia układu plików danych i ograniczeń platformy Azure:
   * Nie ma zbyt wiele dysków, ale wystarczająco dużo, aby upewnić się, że możesz połączyć swoje wymagane operacje We/Wy.
   * Jeśli nie używasz dysków Managed Disks, należy pamiętać, że operacje We/Wy, również są ograniczone na konto usługi Azure Storage i, konta magazynów znajdują się w ramach każdej subskrypcji platformy Azure ([szczegółowe][azure-subscription-service-limits]). 
   * Tylko rozłożonej na dyskach, jeśli potrzebujesz do uzyskania większej przepływności.
3. Nigdy nie instaluj oprogramowania lub umieścić wszystkie pliki, które wymaga trwałości na dysku D:\, jest inne niż stałe i wszystko na tym dysku zostaną utracone w przypadku ponownego uruchomienia systemu Windows.
4. Nie należy używać buforowania dysku dla usługi Azure Standard Storage.
5. Nie używaj replikowanej geograficznie konta usługi Azure Storage.  Użyj lokalnie nadmiarowy na potrzeby obciążeń systemu DBMS.
6. Za pomocą rozwiązania wysokiej dostępności i odzyskiwania po awarii z dostawcą systemu DBMS replikować dane z bazy danych.
7. Zawsze używaj rozpoznawania nazw, nie należy polegać na adresy IP.
8. Użyj najwyższy możliwe kompresja bazy danych. Jest to strona kompresji programu SQL Server.
9. Zachowaj ostrożność przy użyciu obrazów programu SQL Server w witrynie Azure Marketplace. Jeśli używasz programu SQL Server, jeden, należy zmienić sortowania wystąpienia przed zainstalowaniem dowolnego systemu SAP NetWeaver na nim.
10. Instalowanie i konfigurowanie monitorowania hosta SAP na platformie Azure, zgodnie z opisem w [przewodnik wdrażania][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Charakterystyki SAP ASE na Windows
Począwszy od programu Microsoft Azure, możesz z łatwością migrować istniejące aplikacje SAP ASE na maszynach wirtualnych platformy Azure. SAP ASE na maszynie wirtualnej, można zmniejszyć całkowity koszt posiadania, wdrażania, zarządzania i konserwacji aplikacji dla przedsiębiorstw szeroki zakres, łatwe migrowanie tych aplikacji w systemie Microsoft Azure. Za pomocą SAP ASE w maszynie wirtualnej platformy Azure Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie.

Brak umowy SLA usługi Azure Virtual Machines, które można znaleźć tutaj: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Mamy pewność, że Microsoft Azure udostępnione maszyny wirtualne, wykonuje również w porównaniu do innych ofert wirtualizacji chmury publicznej, ale poszczególne wyniki mogą się różnić. Oprogramowanie SAP, ustalanie rozmiaru protokoły SAP liczby różnych SAP certyfikowane, jednostek SKU maszyn wirtualnych znajduje się w oddzielnych Uwaga SAP [1928533].

Instrukcje i zalecenia w zakresie użycia usługi Azure Storage, wdrożenia SAP maszyn wirtualnych lub SAP monitorowanie dotyczą wdrożeń SAP ASE w połączeniu z aplikacjami SAP opisany w całym rozdziały pierwsze cztery części tego dokumentu.

### <a name="sap-ase-version-support"></a>Obsługa wersji środowiska ASE SAP
SAP obecnie obsługuje SAP ASE wersji 16.0 do użytku z produktów SAP Business Suite. Wszystkie aktualizacje dla serwera SAP ASE i sterowników JDBC i ODBC do użycia z produktów SAP Business Suite znajdują się wyłącznie za pośrednictwem SAP Service Marketplace na: <https://support.sap.com/swdc>.

Jak w przypadku instalacji lokalnej nie pobieraj aktualizacje serwera SAP ASE lub sterowników JDBC i ODBC bezpośrednio z witryny sieci Web programu Sybase. Szczegółowe informacje na temat poprawek, które są obsługiwane przez oprogramowanie SAP Business Suite produktów lokalnych i w usłudze Azure Virtual Machines, zobacz następujące uwagi SAP:

* [1590719]
* [1973241]

Ogólne informacje na temat systemem SAP Business Suite SAP ASE znajdują się w [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania środowiska ASE SAP dla powiązanych SAP SAP ASE instalacji w maszynach wirtualnych platformy Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura wdrożenia SAP ASE
Zgodnie z ogólny opis pliki wykonywalne SAP ASE powinny być znajduje się lub zainstalowany na dysku systemowym dysku systemu operacyjnego maszyny Wirtualnej (dysk c:\). Zazwyczaj większość baz danych narzędzia i system SAP ASE nie używają ciężko obciążenia SAP NetWeaver. Dlatego system i narzędzia baz danych (master, model, saptools, sybmgmtdb, sybsystemdb) może pozostać na tym dysku C:\. 

Wyjątek może być tymczasowa baza danych zawierająca wszystkie tabele pracy i tabel tymczasowych utworzonych przez SAP ASE, która w przypadku niektórych SAP ERP oraz wszystkich obciążeń BW może wymagać większą ilość danych lub operacji We/Wy operacji wolumin, który nie mieści się w oryginalnej maszyny Wirtualnej systemu operacyjnego dysku (dysk c:\).

W zależności od wersji SAPInst/SWPM używane do instalowania systemu bazy danych może zawierać:

* Pojedynczy tempdb SAP ASE, która jest tworzona podczas instalacji SAP ASE
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowe saptempdb utworzone przez procedury instalacyjnej SAP
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowych danych tempdb, który został utworzony ręcznie (na przykład następujące Uwaga SAP [1752266]) do określonej bazy danych tempdb ERP i przeniesieniu jej/BW wymagań

W przypadku ERP określonych lub wszystkich obciążeń BW dobrym pomysłem, w odniesieniu do wydajności, aby urządzenia bazy danych tempdb, dodatkowo utworzonej bazy danych tempdb (przez SWPM lub ręcznie) na dysku innym niż C:\. Jeśli istnieje nie dodatkowe bazy danych tempdb, zalecane jest utworzenie (Uwaga SAP [1752266]).

Dla takich systemów dodatkowo utworzonej bazy danych tempdb należy przeprowadzić następujące czynności:

* Przenieś jako pierwsze urządzenie bazy danych tempdb na pierwszym urządzeniu bazy danych SAP
* Dodawanie urządzeń bazy danych tempdb do każdego z wirtualnych dysków twardych, zawierające urządzenia bazy danych SAP

Ta konfiguracja umożliwia bazy danych tempdb, albo używać więcej miejsca niż dysk systemowy jest w stanie zapewnić. Jako odwołanie jeden Sprawdź rozmiary urządzenia bazy danych tempdb na istniejących systemów, które działają lokalnie. Lub taka konfiguracja umożliwiłby liczby operacji We/Wy względem bazy danych tempdb, które nie mogą być określane przy użyciu dysku systemowego. Ponownie systemów, które są uruchomione w siedzibie firmy może służyć do monitorowania obciążenia operacji We/Wy względem bazy danych tempdb.

Nigdy nie umieścić wszelkie urządzenia SAP ASE na dysku D:\, maszyny wirtualnej. Dotyczy to również do bazy danych tempdb, nawet jeśli obiekty przechowywane w bazie danych tempdb są one jedynie tymczasowe.

#### <a name="impact-of-database-compression"></a>Wpływ kompresja bazy danych
W konfiguracji, gdy przepustowość operacji We/Wy może stać się czynnikiem ograniczającym Każda miara, co zmniejsza operacje We/Wy może przyczynić się do rozproszonego obciążenia, jakie można jedno uruchomienie w scenariuszu IaaS, takich jak platforma Azure. Dlatego zalecane jest aby upewnić się, że kompresja SAP ASE jest używana przed przekazaniem istniejącej bazy danych SAP na platformie Azure.

Zalecane do wykonania kompresji przed przekazaniem do usługi Azure, jeśli jeszcze nie zaimplementowano znajduje się z kilku powodów:

* Ilość danych do przesłania na platformę Azure jest niższa
* Czas trwania wykonania kompresji jest krótszy, przy założeniu, że jeden służy silniejsze sprzętu z więcej procesorów ani większą przepustowość operacji We/Wy lub mniej operacji We/Wy opóźnienia w środowisku lokalnym
* Mniejsze rozmiary bazy danych może prowadzić do mniejsze koszty przydział dysku

Kompresja danych i obiektów LOB działa na maszynie wirtualnej hostowanej w usłudze Azure Virtual Machines, jak w środowisku lokalnym. Szczegółowe informacje na temat sposobu sprawdzania, jeśli kompresja jest już do używania w istniejącej bazy danych SAP ASE, zapoznaj się uwagę [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Aby monitorować wystąpienia bazy danych przy użyciu DBACockpit
W przypadku systemów SAP, które używają SAP ASE jako platformę bazy danych, DBACockpit jest dostępne jako okna przeglądarki osadzone w transakcji DBACockpit lub Webdynpro. Jednak pełnej funkcjonalności do monitorowania i administrowania nim bazy danych jest dostępna w implementacji Webdynpro DBACockpit, tylko.

Jako z lokalnymi systemami kilka czynności, aby włączyć wszystkie funkcje oprogramowania SAP NetWeaver używane przez implementację Webdynpro DBACockpit. Postępuj zgodnie z Uwaga SAP [1245200] Włącz użycie webdynpros i generować wymagane te. Gdy postępując zgodnie z instrukcjami w powyższym notes, również skonfigurować Internet Communication Manager (icm) oraz porty, które mają zostać użyte w przypadku połączeń http i https. Ustawieniem domyślnym dla protokołu http wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

i łącza generowane w transakcji DBACockpit wygląda podobnie do następującej:

> https://`<fullyqualifiedhostname`>: 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od czy i jak maszyny wirtualnej platformy Azure, hosting systemu SAP jest połączona za pośrednictwem lokacja lokacja, obejmujące wiele lokacji lub usługi ExpressRoute (wdrożenie obejmujących wiele lokalizacji), musisz upewnić się, że ICM korzysta w pełni kwalifikowana nazwa hosta, która może zostać rozpoznana na komputerze gdzie Próbujesz otworzyć DBACockpit z. Patrz Uwaga SAP [773830] Aby zrozumieć, jak ICM określa pełni kwalifikowaną nazwę hosta w zależności od parametrów profilu i ustaw parametr icm/host_name_full jawnie w razie potrzeby.

Jeśli wdrożono maszynę Wirtualną w scenariuszu tylko w chmurze bez łączności między środowiskami lokalnymi między lokalną i platformą Azure, musisz zdefiniować publiczny adres IP i domainlabel. Format jej publiczną nazwę DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Można znaleźć więcej szczegółów dotyczących nazwy DNS [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametru profilu icm/host_name_full SAP na nazwę DNS maszyny Wirtualnej platformy Azure link może wyglądać podobnie do:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

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
Podczas wdrażania środowiska ASE SAP na platformie Azure, muszą być przejrzane w swojej metodologii kopii zapasowej. Nawet jeśli system nie jest systemów produkcyjnych, bazy danych SAP, SAP ASE na użytek musi kopii zapasowej okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, kopia zapasowa jest teraz mniej ważne w odniesieniu do wyrównującej awarii magazynu. Głównym powodem utrzymywania właściwego planu tworzenia kopii zapasowych i przywracania jest inne, które można kompensowane błędy logiczne/ręczne, zapewniając punktu w czasie możliwości odzyskiwania. Dlatego celem jest albo użyj kopii zapasowych, aby przywrócić bazę danych do pewnego momentu w czasie lub używać kopie zapasowe na platformie Azure, aby zapełnić innego systemu przez skopiowanie istniejącej bazy danych. Na przykład można przeniesiesz z konfiguracji SAP warstwy 2 do 3-warstwowej systemu BIOS tego samego systemu przez przywrócenie kopii zapasowej.

Wykonywanie kopii zapasowych i przywracania bazy danych na platformie Azure działa tak samo, jak w środowisku lokalnym. Zobacz uwagi SAP:

* [1588316]
* [1585981]

Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji zrzutu i planowania kopii zapasowych. W zależności od potrzeb, które można skonfigurować i strategii bazy danych i dziennika zrzuty dysku na jeden z istniejących dysków lub dodać dodatkowy dysk dla kopii zapasowej. Aby ograniczyć ryzyko utraty danych w przypadku błędu, zaleca się używania dysku, na którym znajduje się urządzenie nie bazy danych.

Oprócz danych i biznesowych kompresji SAP ASE oferuje również kompresja kopii zapasowej. Zajmuje mniej miejsca przy użyciu zrzuty dziennika i bazy danych zaleca się używać kompresja kopii zapasowej. Aby uzyskać więcej informacji, patrz Uwaga SAP [1588316]. Kompresja kopii zapasowej jest również zmniejszyć ilość danych do przeniesienia, jeśli planujesz pobierania kopii zapasowych lub wirtualnych dyskach twardych zawierających zrzuty kopii zapasowej z maszyny wirtualnej platformy Azure do środowiska lokalnego.

Nie należy używać dysku D:\ jako miejsce docelowe zrzutu bazy danych lub dziennika.

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące tworzenia kopii zapasowych/przywracania
Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania jest zależna od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Ponadto użycie procesora CPU, używane przez kompresja kopii zapasowej może odtwarzać istotną rolę na maszynach wirtualnych z maksymalnie osiem wątki procesora CPU. W związku z tym jeden założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych mniejszy ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (katalogi usługi Stripe, dyski) można zapisać kopii zapasowej do mniejszej przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu czy dostępne są dwie opcje, które mogą być używane/łączyć w zależności od potrzeb:

* Stosowanie wolumin docelowy kopii zapasowej za pośrednictwem wielu dysków zainstalowanych, aby zwiększyć przepływność operacji We/Wy na tego woluminu rozłożonego
* Tworzenie konfiguracji zrzutu na poziomie SAP ASE, który używa więcej niż jeden katalog docelowy można zapisać zrzutu do

Stosowanie woluminu na wiele dysków zainstalowanych zostały omówione wcześniej w tym przewodniku. Więcej informacji na temat korzystania z wieloma katalogami w konfiguracji zrzutu SAP ASE, można znaleźć w dokumentacji dotyczącej sp_config_dump procedury składowanej, który jest używany do tworzenia konfiguracji zrzutu na [Centrum Sybase informacyjne](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Odzyskiwanie po awarii z maszyn wirtualnych platformy Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikacja danych z serwerem replikacji bazy danych Sybase SAP
Za pomocą SAP Sybase replikacji serwera (SRS) SAP ASE rozwiązaniem jest ciepło wstrzymania asynchronicznie przesyłać transakcji bazy danych do odległych lokalizacji. 

Instalacji i działania SRS działa również funkcjonalnie na maszynie wirtualnej hostowane w usługach maszyny wirtualnej platformy Azure, jak w środowisku lokalnym.

SAP ASE HADR nie wymaga usługi Azure wewnętrznego modułu równoważenia obciążenia nie są zależne od poziomu klastrowania systemu operacyjnego i działa w systemach Windows Azure i maszyn wirtualnych systemu Linux. Aby uzyskać szczegółowe informacje na temat SAP ASE HADR przeczytaj [przewodnika użytkownicy SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Charakterystyki SAP ASE w systemie Linux
Począwszy od programu Microsoft Azure, możesz z łatwością migrować istniejące aplikacje SAP ASE na maszynach wirtualnych platformy Azure. SAP ASE na maszynie wirtualnej, można zmniejszyć całkowity koszt posiadania, wdrażania, zarządzania i konserwacji aplikacji dla przedsiębiorstw szeroki zakres, łatwe migrowanie tych aplikacji w systemie Microsoft Azure. Za pomocą SAP ASE w maszynie wirtualnej platformy Azure Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie.

Do wdrażania maszyn wirtualnych platformy Azure jest musisz znać oficjalne umowy SLA, które można znaleźć tutaj: <https://azure.microsoft.com/support/legal/sla>

Dane zmiany rozmiaru SAP oraz listę jednostek SKU maszyn wirtualnych z certyfikatem SAP jest podawany jako Uwaga SAP [1928533]. Dodatkowe SAP rozmiaru dokumentów dla maszyn wirtualnych platformy Azure można znaleźć tutaj <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> i w tym miejscu <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instrukcje i zalecenia w zakresie użycia usługi Azure Storage, wdrożenia SAP maszyn wirtualnych lub SAP monitorowanie dotyczą wdrożeń SAP ASE w połączeniu z aplikacjami SAP opisany w całym rozdziały pierwsze cztery części tego dokumentu.

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
Zgodnie z ogólny opis pliki wykonywalne SAP ASE należy znajduje się lub zainstalowane w głównym systemie plików maszyny wirtualnej (/sybase). Zazwyczaj większość baz danych narzędzia i system SAP ASE nie są w ciężko wykorzystywane przez oprogramowanie SAP NetWeaver obciążenia. Dlatego system i narzędzia baz danych (master, model, saptools, sybmgmtdb, sybsystemdb) może pozostać w systemie plików głównego także. 

Wyjątek może być tymczasowa baza danych zawierająca wszystkie tabele pracy i tabel tymczasowych utworzonych przez SAP ASE, która w przypadku niektórych SAP ERP oraz wszystkich obciążeń BW może wymagać większą ilość danych lub operacji We/Wy woluminu, który nie mieści się w oryginalnej maszyny Wirtualnej systemu operacyjnego dysk.

W zależności od wersji SAPInst/SWPM używane do instalowania systemu bazy danych może zawierać:

* Pojedynczy tempdb SAP ASE, która jest tworzona podczas instalacji SAP ASE
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowe saptempdb utworzone przez procedury instalacyjnej SAP
* Bazy danych tempdb SAP ASE utworzone przez zainstalowanie SAP ASE i dodatkowych danych tempdb, który został utworzony ręcznie (na przykład następujące Uwaga SAP [1752266]) do określonej bazy danych tempdb ERP i przeniesieniu jej/BW wymagań

W przypadku ERP określonych lub wszystkich obciążeń BW sens, w odniesieniu do wydajności do zachowania urządzenia bazy danych tempdb, dodatkowo utworzonej bazy danych tempdb (przez SWPM lub ręcznie) w systemie osobny plik może być reprezentowany przez dysk danych platformy Azure z jednego lub spannin RAID systemu Linux g wielu dysków z danymi platformy Azure. Jeśli istnieje nie dodatkowe bazy danych tempdb, zalecane jest utworzenie (Uwaga SAP [1752266]).

Dla takich systemów dodatkowo utworzonej bazy danych tempdb należy przeprowadzić następujące czynności:

* Przenieś pierwszy katalog bazy danych tempdb na pierwszy system plików bazy danych SAP
* Dodawanie katalogów bazy danych tempdb na każdy z tych dysków, zawierający system plików bazy danych SAP

Ta konfiguracja umożliwia bazy danych tempdb, albo używać więcej miejsca niż dysk systemowy jest w stanie zapewnić. Jako odwołanie jeden Sprawdź rozmiary katalogu bazy danych tempdb na istniejących systemów, które działają lokalnie. Lub taka konfiguracja umożliwiłby liczby operacji We/Wy względem bazy danych tempdb, które nie mogą być określane przy użyciu dysku systemowego. Ponownie systemów, które są uruchomione w siedzibie firmy może służyć do monitorowania obciążenia operacji We/Wy względem bazy danych tempdb.

Nigdy nie umieścić wszelkie katalogi SAP ASE na katalogu/mnt lub /mnt/resource maszyny wirtualnej. Dotyczy to również do bazy danych tempdb, nawet jeśli obiekty przechowywane w bazie danych tempdb są one jedynie tymczasowe, ponieważ katalogu/mnt lub /mnt/resource jest domyślna maszyny Wirtualnej platformy Azure tymczasowego miejsca, który nie jest trwały. Więcej informacji na temat tymczasowego miejsca na maszynie Wirtualnej platformy Azure można znaleźć w [w tym artykule][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Wpływ kompresja bazy danych
W konfiguracji, gdy przepustowość operacji We/Wy może stać się czynnikiem ograniczającym Każda miara, co zmniejsza operacje We/Wy może przyczynić się do rozproszonego obciążenia, jakie można jedno uruchomienie w scenariuszu IaaS, takich jak platforma Azure. Dlatego zalecane jest aby upewnić się, że kompresja SAP ASE jest używana przed przekazaniem istniejącej bazy danych SAP na platformie Azure.

Zalecane do wykonania kompresji przed przekazaniem do usługi Azure, jeśli jeszcze nie zaimplementowano znajduje się z kilku powodów:

* Ilość danych do przesłania na platformę Azure jest niższa
* Czas trwania wykonania kompresji jest krótszy, przy założeniu, że jeden służy silniejsze sprzętu z więcej procesorów ani większą przepustowość operacji We/Wy lub mniej operacji We/Wy opóźnienia w środowisku lokalnym
* Mniejsze rozmiary bazy danych może prowadzić do mniejsze koszty przydział dysku

Kompresja danych i obiektów LOB działa na maszynie wirtualnej hostowanej w usłudze Azure Virtual Machines, jak w środowisku lokalnym. Szczegółowe informacje na temat sposobu sprawdzania, jeśli kompresja jest już do używania w istniejącej bazy danych SAP ASE, zapoznaj się uwagę [1750510]. Aby uzyskać dodatkowe informacje na temat kompresja bazy danych, patrz Uwaga SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Aby monitorować wystąpienia bazy danych przy użyciu DBACockpit
W przypadku systemów SAP, które używają SAP ASE jako platformę bazy danych, DBACockpit jest dostępne jako okna przeglądarki osadzone w transakcji DBACockpit lub Webdynpro. Jednak pełnej funkcjonalności do monitorowania i administrowania nim bazy danych jest dostępna w implementacji Webdynpro DBACockpit, tylko.

Jako z lokalnymi systemami kilka czynności, aby włączyć wszystkie funkcje oprogramowania SAP NetWeaver używane przez implementację Webdynpro DBACockpit. Postępuj zgodnie z Uwaga SAP [1245200] Włącz użycie webdynpros i generować wymagane te. Gdy postępując zgodnie z instrukcjami w powyższym notes, również skonfigurować Internet Communication Manager (icm) oraz porty, które mają zostać użyte w przypadku połączeń http i https. Ustawieniem domyślnym dla protokołu http wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

i łącza generowane w transakcji DBACockpit będą wyglądać mniej więcej tak:

> https://`<fullyqualifiedhostname`>: 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od czy i jak maszyny wirtualnej platformy Azure, hosting systemu SAP jest połączona za pośrednictwem lokacja lokacja, obejmujące wiele lokacji lub usługi ExpressRoute (wdrożenie obejmujących wiele lokalizacji), musisz upewnić się, że ICM korzysta w pełni kwalifikowana nazwa hosta, która może zostać rozpoznana na komputerze gdzie Próbujesz otworzyć DBACockpit z. Patrz Uwaga SAP [773830] Aby zrozumieć, jak ICM określa pełni kwalifikowaną nazwę hosta w zależności od parametrów profilu i ustaw parametr icm/host_name_full jawnie w razie potrzeby.

Jeśli wdrożono maszynę Wirtualną w scenariuszu tylko w chmurze bez łączności między środowiskami lokalnymi między lokalną i platformą Azure, musisz zdefiniować publiczny adres IP i domainlabel. Format jej publiczną nazwę DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Można znaleźć więcej szczegółów dotyczących nazwy DNS [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametru profilu icm/host_name_full SAP na nazwę DNS maszyny Wirtualnej platformy Azure link może wyglądać podobnie do:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

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
W przypadku wdrażania środowiska ASE SAP na platformie Azure muszą być przejrzane w swojej metodologii kopii zapasowej. Nawet jeśli system nie jest systemów produkcyjnych, bazy danych SAP, SAP ASE na użytek musi kopii zapasowej okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, kopia zapasowa jest teraz mniej ważne w odniesieniu do wyrównującej awarii magazynu. Głównym powodem utrzymywania właściwego planu tworzenia kopii zapasowych i przywracania jest inne, które można kompensowane błędy logiczne/ręczne, zapewniając punktu w czasie możliwości odzyskiwania. Dlatego celem jest albo użyj kopii zapasowych, aby przywrócić bazę danych do pewnego momentu w czasie lub używać kopie zapasowe na platformie Azure, aby zapełnić innego systemu przez skopiowanie istniejącej bazy danych. Na przykład można przeniesiesz z konfiguracji SAP warstwy 2 do 3-warstwowej systemu BIOS tego samego systemu przez przywrócenie kopii zapasowej.

Wykonywanie kopii zapasowych i przywracania bazy danych na platformie Azure działa tak samo, jak w środowisku lokalnym. Zobacz uwagi SAP:

* [1588316]
* [1585981]

Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji zrzutu i planowania kopii zapasowych. W zależności od potrzeb, które można skonfigurować i strategii bazy danych i dziennika zrzuty dysku na jeden z istniejących dysków lub dodać dodatkowy dysk dla kopii zapasowej. Aby ograniczyć ryzyko utraty danych w przypadku błędu, zaleca się używania dysku, na którym znajduje się nie bazy danych pliku lub katalogu.

Oprócz danych i biznesowych kompresji SAP ASE oferuje również kompresja kopii zapasowej. Zajmuje mniej miejsca przy użyciu zrzuty dziennika i bazy danych zaleca się używać kompresja kopii zapasowej. Aby uzyskać więcej informacji, patrz Uwaga SAP [1588316]. Kompresja kopii zapasowej jest również zmniejszyć ilość danych do przeniesienia, jeśli planujesz pobierania kopii zapasowych lub wirtualnych dyskach twardych zawierających zrzuty kopii zapasowej z maszyny wirtualnej platformy Azure do środowiska lokalnego.

Nie należy używać maszyny Wirtualnej platformy Azure, miejsca tymczasowe katalogu/mnt lub /mnt/resource jako miejsce docelowe zrzutu bazy danych lub dziennika.

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące tworzenia kopii zapasowych/przywracania
Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania jest zależna od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Ponadto użycie procesora CPU, używane przez kompresja kopii zapasowej może odtwarzać istotną rolę na maszynach wirtualnych z maksymalnie osiem wątki procesora CPU. W związku z tym jeden założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych mniejszy ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (oprogramowania w systemie Linux RAID, dyski) można zapisać kopii zapasowej do mniejszej przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu czy dostępne są dwie opcje, które mogą być używane/łączyć w zależności od potrzeb:

* Stosowanie wolumin docelowy kopii zapasowej za pośrednictwem wielu dysków zainstalowanych, aby zwiększyć przepływność operacji We/Wy na tego woluminu rozłożonego
* Tworzenie konfiguracji zrzutu na poziomie SAP ASE, który używa więcej niż jeden katalog docelowy można zapisać zrzutu do

Stosowanie woluminu na wiele dysków zainstalowanych zostały omówione wcześniej w tym przewodniku. Więcej informacji na temat korzystania z wieloma katalogami w konfiguracji zrzutu SAP ASE, można znaleźć w dokumentacji dotyczącej sp_config_dump procedury składowanej, który jest używany do tworzenia konfiguracji zrzutu na [Centrum Sybase informacyjne](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Odzyskiwanie po awarii z maszyn wirtualnych platformy Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikacja danych z serwerem replikacji bazy danych Sybase SAP
Za pomocą SAP Sybase replikacji serwera (SRS) SAP ASE rozwiązaniem jest ciepło wstrzymania asynchronicznie przesyłać transakcji bazy danych do odległych lokalizacji. 

Instalacji i działania SRS działa również funkcjonalnie na maszynie wirtualnej hostowane w usługach maszyny wirtualnej platformy Azure, jak w środowisku lokalnym.

Środowisko ASE HADR za pośrednictwem programu SAP replikacji serwera nie jest obsługiwany w tym momencie. Może być przetestowany przy użyciu i wydane dla platformy Microsoft Azure w przyszłości.

## <a name="specifics-to-oracle-database-on-windows"></a>Charakterystyka do bazy danych Oracle na Windows
Oprogramowanie Oracle jest obsługiwana przez oprogramowanie Oracle, aby systemem Microsoft Windows Hyper-V i platformą Azure. 

Następujące ogólne obsługi konkretnego scenariusza aplikacji SAP z wykorzystaniem baz danych Oracle obsługiwane jest również. Szczegółowe informacje są nazywane w tej części dokumentu.

### <a name="oracle-version-support"></a>Obsługa wersji programu Oracle
Wersje bazy danych Oracle i odpowiednie wersje systemu operacyjnego, które są obsługiwane dla uruchamianie oprogramowania SAP w bazie danych Oracle na maszynach wirtualnych platformy Azure można znaleźć w uwadze SAP [2039619].

Ogólne informacje o systemie SAP Business Suite Oracle znajdują się w 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania oprogramowania Oracle w przypadku instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration"></a>Konfiguracja usługi Storage
Tylko pojedyncze wystąpienie bazy danych Oracle przy użyciu dysków sformatowanym w systemie NTFS jest obsługiwana. Wszystkie pliki bazy danych muszą być przechowywane w systemie plików NTFS, na podstawie wirtualnych dysków twardych lub dysków Managed Disks. Te dyski są zainstalowane na maszynie Wirtualnej platformy Azure, a także są oparte na usłudze Azure Storage BLOB strony (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) lub dyski Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Każdy rodzaj dyski sieciowe lub udziałach zdalnych, takich jak usługi plików platformy Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

czy **nie** obsługiwane dla plików bazy danych Oracle!

Przy użyciu dysków na podstawie usługi Azure Page BLOB Storage lub Managed Disks instrukcji zawartych w tym dokumencie w rozdziale [buforowania dla maszyn wirtualnych i dysków z danymi] [ dbms-guide-2.1] i [usługi Microsoft Azure Storage] [ dbms-guide-2.3] dotyczą wdrożeń z bazy danych programu Oracle także.

Jak wyjaśniono wcześniej w głównej części dokumentu, istnieje przydziały na przepustowość operacji We/Wy na dyskach platformy Azure. Dokładny w zależności od typu maszyny Wirtualnej służą przydziały. Lista typów maszyn wirtualnych z ich limitami przydziału można znaleźć [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Aby zidentyfikować obsługiwane typy maszyn wirtualnych platformy Azure, zapoznaj się uwagę [1928533].

Tak długo, jak bieżący limit przydziału operacji We/Wy na dysku spełnia wymagania, istnieje możliwość przechowywania wszystkich plików bazy danych na jednym z pojedynczego dysku zainstalowanego. 

Jeśli więcej operacji We/Wy są wymagane, zaleca się użycie okna pul magazynów (tylko dostępne w systemie Windows Server 2012 lub nowszy) lub Windows stosowanie dla Windows 2008 R2 tworzenia jedno urządzenie logiczne dużych dysków zainstalowanych wiele (Zobacz też rozdział [ RAID oprogramowania] [ dbms-guide-2.2] tego dokumentu). Takie podejście ułatwia zmniejszenie administracyjnych kosztów ogólnych zarządzać miejscem na dysku i pozwala uniknąć nakład pracy, aby ręcznie przekazywać pliki na wielu dyskach zainstalowanego.

#### <a name="backup--restore"></a>Wykonywanie kopii zapasowych i ich przywracanie
Do utworzenia kopii zapasowej / przywracania funkcjonalności, SAP BR * narzędzia dla oprogramowania Oracle są obsługiwane w taki sam sposób jak w standardowych systemów operacyjnych Windows Server i funkcji Hyper-V. Menedżer odzyskiwania bazy danych Oracle (RMAN) jest również obsługiwana dla kopii zapasowych na dysku, a następnie Przywróć z dysku.

#### <a name="high-availability"></a>Wysoka dostępność
Środowiska Oracle Data Guard jest obsługiwana dla wysokiej dostępności i celów odzyskiwania po awarii. Szczegółowe informacje można znaleźć w [to] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentacji.

#### <a name="other"></a>Inne
Wszystkie inne ogólnych zagadnień, takich jak zestawy dostępności platformy Azure lub SAP monitorowanie stosowane zgodnie z opisem w pierwszych trzech rozdziały ten dokument w celu wdrożenia maszyn wirtualnych przy użyciu bazy danych programu Oracle także.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Charakterystyka do bazy danych Oracle w systemie Oracle Linux
Oprogramowanie Oracle jest obsługiwana przez oprogramowanie Oracle, aby systemem Microsoft Windows Hyper-V i platformą Azure. 

Następujące ogólne obsługi konkretnego scenariusza aplikacji SAP z wykorzystaniem baz danych Oracle obsługiwane jest również. Szczegółowe informacje są nazywane w tej części dokumentu.

### <a name="oracle-version-support"></a>Obsługa wersji programu Oracle
Wersje bazy danych Oracle i odpowiednie wersje systemu operacyjnego, które są obsługiwane dla uruchamianie oprogramowania SAP w bazie danych Oracle na maszynach wirtualnych platformy Azure można znaleźć w uwadze SAP [2039619].

Ogólne informacje o systemie SAP Business Suite Oracle znajdują się w 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania oprogramowania Oracle w przypadku instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration"></a>Konfiguracja usługi Storage
Obsługiwane jest tylko pojedyncze wystąpienie bazy danych Oracle przy użyciu ext3, ext4 i xfs sformatowane dysków. Wszystkie pliki bazy danych muszą być przechowywane na tych systemów plików, na podstawie wirtualnych dysków twardych lub dysków Managed Disks. Te dyski są zainstalowane na maszynie Wirtualnej platformy Azure, a także są oparte na usłudze Azure Storage BLOB strony (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) lub dyski Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Każdy rodzaj dyski sieciowe lub udziałach zdalnych, takich jak usługi plików platformy Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

czy **nie** obsługiwane dla plików bazy danych Oracle!

Przy użyciu dysków na podstawie usługi Azure Page BLOB Storage lub Managed Disks instrukcji zawartych w tym dokumencie w rozdziale [buforowania dla maszyn wirtualnych i dysków z danymi] [ dbms-guide-2.1] i [usługi Microsoft Azure Storage] [ dbms-guide-2.3] dotyczą wdrożeń z bazy danych programu Oracle także.

Jak wyjaśniono wcześniej w głównej części dokumentu, istnieje przydziały na przepustowość operacji We/Wy na dyskach platformy Azure. Dokładny w zależności od typu maszyny Wirtualnej służą przydziały. Lista typów maszyn wirtualnych z ich limitami przydziału można znaleźć [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Aby zidentyfikować obsługiwane typy maszyn wirtualnych platformy Azure, zapoznaj się uwagę [1928533]

Tak długo, jak bieżący limit przydziału operacji We/Wy na dysku spełnia wymagania, istnieje możliwość przechowywania wszystkich plików bazy danych na jednym z pojedynczego dysku zainstalowanego. 

Więcej operacji We/Wy są wymagane, zalecane jest używanie LVM (Menedżer woluminów logicznych) lub MDADM do tworzenia logicznej jedną dużą przez wiele dysków zainstalowanych. Zobacz też rozdział [macierzy RAID oprogramowania] [ dbms-guide-2.2] tego dokumentu. Takie podejście ułatwia zmniejszenie administracyjnych kosztów ogólnych zarządzać miejscem na dysku i pozwala uniknąć nakład pracy, aby ręcznie przekazywać pliki na wielu dyskach zainstalowanego.

#### <a name="backup--restore"></a>Wykonywanie kopii zapasowych i ich przywracanie
Do utworzenia kopii zapasowej / przywracania funkcjonalności, SAP BR * narzędzia dla oprogramowania Oracle są obsługiwane w taki sam sposób, jak na komputerach bez systemu operacyjnego i funkcji Hyper-V. Menedżer odzyskiwania bazy danych Oracle (RMAN) jest również obsługiwana dla kopii zapasowych na dysku, a następnie Przywróć z dysku.

#### <a name="high-availability"></a>Wysoka dostępność
Środowiska Oracle Data Guard jest obsługiwana dla wysokiej dostępności i celów odzyskiwania po awarii. Szczegółowe informacje można znaleźć w [to] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentacji.

#### <a name="other"></a>Inne
Wszystkie inne ogólnych zagadnień, takich jak zestawy dostępności platformy Azure lub SAP monitorowanie stosowane zgodnie z opisem w pierwszych trzech rozdziały ten dokument w celu wdrożenia maszyn wirtualnych przy użyciu bazy danych programu Oracle także.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Szczegółowe informacje o MaxDB bazy danych SAP na Windows
### <a name="sap-maxdb-version-support"></a>Obsługa wersji MaxDB SAP
SAP obecnie obsługuje MaxDB SAP wersji 7,9 do użytku z produktów opartych na oprogramowanie SAP NetWeaver na platformie Azure. Wszystkie aktualizacje dla serwera SAP MaxDB lub sterowników JDBC i ODBC do użycia z produktów opartych na oprogramowanie SAP NetWeaver znajdują się wyłącznie za pośrednictwem SAP Service Marketplace na <https://support.sap.com/swdc>.
Ogólne informacje na temat uruchamiania oprogramowania SAP NetWeaver na SAP MaxDB znajduje się w temacie <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Obsługiwane typy wersje Windows firmy Microsoft i maszyny Wirtualnej platformy Azure dla systemu DBMS MaxDB SAP
Aby znaleźć obsługiwanej wersji programu Microsoft Windows dla systemu MaxDB SAP DBMS na platformie Azure, zobacz:

* [Macierz dostępności produktów SAP (PAM)][sap-pam]
* Uwaga SAP [1928533]

Zdecydowanie zaleca się używać najnowszej wersji systemu operacyjnego Microsoft Windows, która jest Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Dokumentacja MaxDB dostępne SAP
Zaktualizowaną listę SAP MaxDB dokumentację można znaleźć w następujących SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania MaxDB SAP dla instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Konfiguracja magazynu
Usługa Azure storage najlepsze rozwiązania dotyczące SAP MaxDB wykonaj ogólne zalecenia, o których wspomniano w rozdziale [struktury wdrożenia RDBMS][dbms-guide-2].

> [!IMPORTANT]
> Podobnie jak inne bazy danych SAP MaxDB ma plików danych i dziennika. Jednak w terminologii SAP MaxDB prawidłowy termin jest "wolumin" (nie "plik"). Na przykład istnieją SAP MaxDB woluminów danych i woluminy dziennika. Nie należy mylić ich z woluminów dysku systemu operacyjnego. 
> 
> 

W skrócie należy:

* Jeśli używasz konta usługi Azure Storage, równa kontem magazynu platformy Azure, w którym przechowywane są woluminy danych i dziennika SAP MaxDB (np. pliki) **lokalny magazyn nadmiarowy (LRS)** jak określono w rozdziale [usługi Microsoft Azure Storage][dbms-guide-2.3].
* Ścieżki we/wy SAP MaxDB woluminy danych (np. pliki) należy oddzielić od ścieżki we/wy dziennika woluminów (czyli pliki). Oznacza to, że SAP MaxDB woluminy danych (np. pliki) musi być zainstalowana na jednym dysku logicznego, a woluminy dziennika SAP MaxDB (np. pliki) musi być zainstalowany na innym dysku logicznego.
* Ustaw odpowiednie typu buforowania dla każdego dysku, w zależności od tego, czy zostanie on użyty do SAP MaxDB danych lub dziennika woluminów (czyli pliki), czy korzystasz, Standard platformy Azure lub usługi Azure Premium Storage, zgodnie z opisem w rozdziale [buforowania dla maszyn wirtualnych i dysków z danymi] [dbms-guide-2.1].
* Tak długo, jak bieżący limit przydziału operacji We/Wy na dysku spełnia wymagania, jest możliwe, aby przechowywać wszystkie woluminy danych na jednym dysku zainstalowanego i również przechowywać wszystkie woluminy dziennika bazy danych na innym dysku zainstalowanego pojedynczego.
* Jeśli więcej operacji We/Wy i/lub miejsca są wymagane, zaleca się umożliwia utworzenie jednego urządzenia logicznego dużych za pośrednictwem wielu pule magazynów okna Microsoft (tylko dostępne w systemie Microsoft Windows Server 2012 lub nowszy) lub rozkładanie Microsoft Windows dla systemu Microsoft Windows 2008 R2 zainstalowanych dysków. Zobacz też rozdział [macierzy RAID oprogramowania] [ dbms-guide-2.2] tego dokumentu. Takie podejście ułatwia zmniejszenie administracyjnych kosztów ogólnych zarządzać miejscem na dysku i pozwala uniknąć nakład pracy ręcznie dystrybucji plików na wielu dyskach zainstalowane.
* Dla najwyższe wymagania operacje We/Wy można użyć usługi Azure Premium Storage, który jest dostępny na serii DS i maszyny wirtualne z serii GS.

![Odwołanie do konfiguracji maszyny wirtualnej IaaS platformy Azure dla systemu DBMS MaxDB SAP][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Kopia zapasowa i przywracanie
W przypadku wdrażania SAP MaxDB na platformie Azure, należy przejrzeć swoje metodologii kopii zapasowej. Nawet jeśli system nie jest wydajność systemu, bazy danych SAP hostowanej przez oprogramowanie SAP MaxDB musi kopii zapasowej okresowo. Ponieważ usługi Azure Storage przechowywane są trzy obrazy, kopia zapasowa jest teraz mniej ważne pod kątem ochrony systemu przed wystąpił błąd magazynu i niezwykle ważne awariami operacyjnych lub administracyjnych. Głównym powodem utrzymywania odpowiednich kopii zapasowej i przywracania plan jest tak, aby można kompensowane błędy logiczne, czy ręcznie, przez udostępnienie możliwości w momencie odzyskiwania. Celem jest więc użyć kopii zapasowych do przywrócenia bazy danych do pewnego momentu w czasie albo na potrzeby tworzenia kopii zapasowych na platformie Azure umieszczenia innego systemu przez skopiowanie istniejącej bazy danych. Na przykład można przeniesiesz z konfiguracji SAP warstwy 2 do 3-warstwowej systemu BIOS tego samego systemu przez przywrócenie kopii zapasowej.

Wykonywanie kopii zapasowych i przywracania bazy danych na platformie Azure działa tak samo jak w przypadku systemów lokalnych, aby można było używać standardowych narzędzi/przywracania kopii zapasowej SAP MaxDB, które są opisane w jednym z dokumentów dokumentacji SAP MaxDB Uwaga SAP na liście [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania
Jak wdrożenia bez systemu operacyjnego wydajności tworzenia kopii zapasowych i przywracania jest zależna od liczby woluminów mogą być odczytywane w równoległych i przepływność tych woluminów. Ponadto użycie procesora CPU, używane przez kompresja kopii zapasowej można odtworzyć istotną rolę na maszynach wirtualnych z maksymalnie osiem wątki procesora CPU. W związku z tym jeden założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych, dolna ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (katalogi usługi Stripe, dyski) można zapisać kopii zapasowej do dolnego przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu, istnieją dwie opcje, których można, prawdopodobnie w połączeniu, w zależności od potrzeb:

* Przypisywanie oddzielnych woluminach kopii zapasowej
* Stosowanie wolumin docelowy kopii zapasowej za pośrednictwem wielu dysków zainstalowanych w celu zwiększenia przepływności operacji We/Wy na woluminie dysku rozłożonego
* Posiadanie oddzielnych dedykowanych dysku logicznego urządzeń:
  * SAP MaxDB woluminach kopii zapasowej (tj. plików)
  * SAP MaxDB woluminy danych (np. plików)
  * SAP woluminy dziennika MaxDB (tj. plików)

Stosowanie woluminu na wiele dysków zainstalowanych zostały omówione wcześniej w rozdziale [macierzy RAID oprogramowania] [ dbms-guide-2.2] tego dokumentu. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Inne
Wszystkie inne ogólne obszary, takich jak zestawy dostępności platformy Azure lub SAP monitorowanie mają również zastosowanie, zgodnie z opisem w pierwszych trzech rozdziały ten dokument w celu wdrożenia maszyn wirtualnych za pomocą bazy danych SAP MaxDB.
Inne ustawienia specyficzne dla MaxDB SAP są niewidoczne dla maszyn wirtualnych platformy Azure i są opisane w różnych dokumentach wymienionych w Uwaga SAP [767598] w tych SAP notes:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Szczegółowe informacje o liveCache SAP na Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache Obsługa wersji
Minimalna wersja liveCache SAP obsługiwane w usłudze Azure Virtual Machines jest **SAP LC/LCAPPS 10.0 SP 25** w tym **liveCache 7.9.08.31** i **25 LCA kompilacji**, wydanej dla **2 EhP SAP SCM 7.0** lub nowszy.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Obsługiwane typy wersje Windows firmy Microsoft i maszyny Wirtualnej platformy Azure dla SAP liveCache DBMS
Aby znaleźć obsługiwanej wersji programu Microsoft Windows liveCache SAP na platformie Azure, zobacz:

* [Macierz dostępności produktów SAP (PAM)][sap-pam]
* Uwaga SAP [1928533]

Zdecydowanie zaleca się używać najnowszej wersji systemu operacyjnego Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache wskazówki dotyczące konfigurowania instalacji SAP na maszynach wirtualnych Azure
#### <a name="recommended-azure-vm-types"></a>Zaleca się typy maszyn wirtualnych platformy Azure
SAP liveCache się aplikacja, która wykonuje obliczenia ogromna ilość i szybkość pamięci RAM i procesora CPU ma poważny wpływ na wydajność liveCache SAP. 

Dla typów maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP (Uwaga SAP [1928533]), wszystkie wirtualne zasobów procesora CPU przydzielonych do maszyny Wirtualnej są objęte dedykowanych zasobów Procesora fizycznego funkcji hypervisor. Nie celi (i w związku z tym nie konkurencję w odniesieniu do zasobów procesora CPU) odbywa się.

Podobnie dla wszystkich typów wystąpień maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP, pamięci maszyny Wirtualnej jest 100% mapowane w pamięci fizycznej - celi (zobowiązanie nadmiernego), na przykład nie jest używany.

Z tego punktu widzenia zaleca użycie nowego typu serii D lub maszyny Wirtualnej platformy Azure serii DS (w połączeniu z usługą Azure Premium Storage), mają 60% szybsze procesory niż w przypadku serii. Dla największego obciążenia pamięci RAM i Procesora za pomocą serii G i maszyny wirtualne z serii GS (w połączeniu z usługą Azure Premium Storage) najnowsze Intel? Xeon? rodziny E5 v3, która ma dwa razy pamięci i cztery razy magazyn dysków półprzewodnikowych (SSD) D/DS serii.

#### <a name="storage-configuration"></a>Konfiguracja magazynu
Jak SAP liveCache jest oparty na technologii SAP MaxDB, usługa Azure storage najlepsze praktyki zaleceń wymienionych w rozdziale SAP MaxDB [konfiguracji magazynu] [ dbms-guide-8.4.1] również są prawidłowe dla SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedykowane maszyny Wirtualnej platformy Azure dla liveCache
Jak SAP liveCache intensywnie korzysta z mocy obliczeniowej, wydajne użycie zaleca do wdrożenia na dedykowanej maszynie wirtualnej platformy Azure. 

![Dedykowany liveCache dla przypadku użycia wydajność maszyny Wirtualnej platformy Azure][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Wykonywanie kopii zapasowych i przywracanie
Kopia zapasowa i przywracanie, w tym zagadnienia związane z wydajnością już są opisane w odpowiednich rozdziałów SAP MaxDB [kopia zapasowa i przywracanie] [ dbms-guide-8.4.2] i [zagadnienia dotyczące wydajności dla kopii zapasowej Tworzenie i przywracanie][dbms-guide-8.4.3]. 

#### <a name="other"></a>Inne
Inne ogólne obszary już są opisane w odpowiednich MaxDB SAP [to] [ dbms-guide-8.4.4] rozdziale. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Szczegółowe informacje o zawartości serwer SAP na Windows
Serwer zawartości SAP jest składnikiem oddzielny, oparte na serwerze, do przechowywania zawartości takiej jak elektronicznych dokumentów w różnych formatach. Serwer zawartości SAP jest świadczona przez rozwoju technologii i ma być używany między platformami w przypadku aplikacji SAP. Jest on instalowany w oddzielnym systemie. Typowe zawartość jest materiały szkoleniowe i dokumentacja z magazynu wiedzy lub rysunki techniczne pochodzące z mySAP System zarządzania dokumentami elementu. 

### <a name="sap-content-server-version-support"></a>Obsługa wersji zawartości serwera SAP
SAP obecnie obsługuje:

* **Serwer zawartości SAP** wersją **6.50 (lub nowszy)**
* **SAP MaxDB wersji 7,9**
* **Microsoft IIS (Internet Information Server) w wersji 8.0 (i nowsze)**

Zdecydowanie zaleca się używać najnowszej wersji zawartości serwera SAP, czyli w czasie pisania tego dokumentu **6.50 SP4**, a najnowsza wersja **Microsoft IIS 8.5**. 

Sprawdź najnowszych obsługiwanych wersjach zawartości serwer SAP i Microsoft IIS w [SAP produktu dostępności macierzy (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Obsługiwane typy Microsoft Windows i maszyn wirtualnych platformy Azure dla serwera zawartości SAP
Aby dowiedzieć się, obsługiwaną wersję Windows dla serwera zawartości SAP na platformie Azure, zobacz:

* [Macierz dostępności produktów SAP (PAM)][sap-pam]
* Uwaga SAP [1928533]

Zdecydowanie zaleca się używać najnowszej wersji systemu Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfigurowania serwera zawartości SAP dla instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration"></a>Konfiguracja magazynu
Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w bazie danych SAP MaxDB, wszystkie usługi Azure storage najlepsze praktyki zaleceń wymienionych w rozdziale SAP MaxDB [konfiguracji magazynu] [ dbms-guide-8.4.1] są również prawidłowe w scenariuszu serwer zawartości SAP. 

Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w systemie plików, zaleca się użyć dedykowanego dysku logicznego. Przy użyciu funkcji miejsca do magazynowania systemu Windows umożliwia również zwiększyć rozmiar dysku logicznego i przepływności operacji We/Wy, zgodnie z opisem w rozdziale [macierzy RAID oprogramowania][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>Lokalizacja zawartości serwera SAP
Serwer zawartości SAP musi zostać wdrożony w tym samym regionie platformy Azure i siecią Wirtualną platformy Azure, w którym jest wdrażany SAP system. Mogą zdecydować, czy chcesz wdrożyć składniki serwera zawartości SAP na dedykowanej maszynie Wirtualnej platformy Azure lub w tej samej maszyny Wirtualnej, którym jest uruchomiony SAP system. 

![Maszyna wirtualna platformy Azure w wersji dedykowanej dla serwera zawartości SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Lokalizacja serwera pamięci podręcznej SAP
Serwer pamięci podręcznej SAP jest dodatkowych składników na serwerze, aby zapewnić dostęp do dokumentów (buforowanej), lokalnie. Serwer SAP w pamięci podręcznej buforuje dokumentów serwera zawartości SAP. To, aby zoptymalizować ruch sieciowy, jeśli dokumenty mają być pobierane więcej niż jeden raz z różnych lokalizacji. Ogólną zasadą jest, że serwer SAP w pamięci podręcznej musi być fizycznie blisko klienta, który uzyskuje dostęp do serwera SAP w pamięci podręcznej. 

Poniżej dostępne są dwie opcje:

1. **Klient znajduje się w wewnętrznej bazie danych systemu SAP** jest klientem, jeśli system SAP wewnętrznej bazy danych skonfigurowano na dostęp do zawartości serwera SAP, tego systemu SAP. Jak SAP system i SAP serwera zawartości są wdrażane w tym samym regionie platformy Azure, w tym samym centrum danych platformy Azure, są fizycznie blisko siebie. W związku z tym nie ma potrzeby dedykowanego serwera pamięci podręcznej SAP. Bezpośredni dostęp klientów SAP interfejsu użytkownika (SAP GUI lub przeglądarki sieci web) w systemie SAP i SAP system pobiera dokumenty z serwera zawartości SAP.
2. **Klient jest w przeglądarce sieci web w środowisku lokalnym** można skonfigurować serwer zawartości programu SAP były dostępne bezpośrednio z poziomu przeglądarki sieci web. W tym przypadku przeglądarki sieci web działających lokalnie to klient serwera zawartości SAP. W lokalnym centrum danych i centrum danych platformy Azure są umieszczane w różnych lokalizacjach fizycznych (najlepiej blisko siebie). W lokalnym centrum danych jest podłączony do platformy Azure za pomocą usługi Azure VPN lokacja-lokacja lub ExpressRoute. Mimo że obie opcje oferują bezpiecznego połączenia sieci VPN na platformie Azure, połączenie lokacja lokacja nie oferuje SLA przepustowości i opóźnienia sieci między lokalnym centrum danych i centrum danych platformy Azure. Aby przyspieszyć dostęp do dokumentów, wykonaj jedną z następujących czynności:
   1. Instalacja serwera pamięci podręcznej SAP w środowisku lokalnym, Zamknij, aby przeglądarki sieci web na lokalnym (opcja [to] [ dbms-guide-900-sap-cache-server-on-premises] rysunek)
   2. Konfigurowanie usługi Azure ExpressRoute, która oferuje szybki i małym opóźnieniu dedykowane połączenie sieciowe między lokalnym centrum danych i centrum danych platformy Azure.

![Opcja instalacji serwera pamięci podręcznej SAP w środowisku lokalnym][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Wykonywanie kopii zapasowych i ich przywracanie
Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w bazie danych SAP MaxDB, zagadnienia dotyczące procedury i wydajność/przywracania kopii zapasowej już są opisane w rozdziale SAP MaxDB [kopia zapasowa i przywracanie] [ dbms-guide-8.4.2]i rozdział [zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania][dbms-guide-8.4.3]. 

Jeśli konfigurujesz serwer zawartości SAP do przechowywania plików w systemie plików, jedną z opcji jest wykonywanie ręczne/przywracania kopii zapasowej całego pliku struktury gdzie znajdują się dokumentów. Podobnie jak SAP MaxDB kopii zapasowej/przywracania, zaleca się mieć dedykowany wolumin w celu tworzenia kopii zapasowej. 

#### <a name="other"></a>Inne
Inne ustawienia zawartości SAP specyficzne dla serwera są niewidoczne dla maszyn wirtualnych platformy Azure i są opisane w różnych dokumentach i SAP Notes:

* <https://service.sap.com/contentserver> 
* Uwaga SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Charakterystyka programem IBM DB2 LUW na Windows
Platforma Microsoft Azure możesz z łatwością migrować istniejącą aplikację SAP systemem IBM DB2 dla systemów Linux, UNIX i Windows (LUW) do maszyn wirtualnych platformy Azure. Korzystając z oprogramowania SAP, IBM DB2 for LUW Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie.
Ogólne informacje o systemie SAP Business Suite IBM DB2 dla LUW można znaleźć w sieci społeczności SAP (SCN) na <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Aby uzyskać dodatkowe informacje i aktualizacjach oprogramowania SAP na bazy danych DB2 for LUW na platformie Azure, zobacz Uwaga SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 dla systemów Linux, UNIX i obsługa wersji Windows
Środowisko SAP na IBM DB2 for LUW na usług Microsoft Azure Virtual Machine jest obsługiwane począwszy od wersji bazy danych DB2 10.5.

Informacje dotyczące obsługiwanych produktów SAP i typów maszyn wirtualnych platformy Azure, zapoznaj się uwagę [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 dla systemów Linux, UNIX i wskazówki dotyczące konfigurowania Windows w przypadku instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration"></a>Konfiguracja magazynu
Wszystkie pliki bazy danych muszą być przechowywane w systemie plików NTFS, oparte na bezpośrednio dołączonych dysków. Te dyski są zainstalowane na maszynie Wirtualnej platformy Azure i opierają się w usłudze Azure Storage BLOB strony (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) lub dyski Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Każdy rodzaj dyski sieciowe lub udziałach zdalnych, takich jak następujące usługi plików platformy Azure są **nie** obsługiwane dla plików bazy danych: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Jeśli używasz dysków, na podstawie usługi Azure Page BLOB Storage lub dyski Managed Disks instrukcje wprowadzone w tym dokumencie w rozdziale [struktury wdrożenia RDBMS] [ dbms-guide-2] mają zastosowanie również do wdrożenia przy użyciu programu IBM DB2 for LUW Baza danych. 

Jak wyjaśniono wcześniej w głównej części dokumentu, istnieje przydziały na przepustowość operacji We/Wy dysków. Dokładne przydziały zależą od typu maszyny Wirtualnej, używane. Lista typów maszyn wirtualnych z ich limitami przydziału można znaleźć [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Tak długo, jak bieżący limit przydziału operacji We/Wy na dysku jest wystarczająca, jest możliwość przechowywania wszystkich plików bazy danych na jednym z pojedynczego dysku zainstalowanego. 

Wydajności zagadnienia dotyczące również można znaleźć w rozdziale "Bezpieczeństwa danych i zagadnienia dotyczące wydajności w przypadku katalogów bazy danych" w przewodników po instalacji SAP.

Alternatywnie można użyć pule magazynu systemu Windows (tylko dostępne w systemie Windows Server 2012 lub nowszy) lub rozkładanie Windows dla systemu Windows 2008 R2 jako opisane w rozdziale [macierzy RAID oprogramowania] [ dbms-guide-2.2] tego dokumentu Utwórz jedno urządzenie logiczne dużych przez wiele dysków.
Dyski zawierające ścieżki magazynu bazy danych DB2 katalogów sapdata i saptmp katalogów należy określić rozmiar sektora dysku fizycznego, 512 KB. Gdy używane są pule magazynu systemu Windows, należy utworzyć pule magazynów ręcznie za pomocą interfejsu wiersza polecenia za pomocą parametru `-LogicalSectorSizeDefault`. Aby uzyskać więcej informacji, zobacz <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Tworzenie i przywracanie kopii zapasowych
Funkcja wykonywania kopii zapasowej i przywracania programu IBM DB2 for LUW jest obsługiwana w taki sam sposób, jak standardowy systemów operacyjnych Windows Server i funkcji Hyper-V.

Upewnij się, że strategii tworzenia kopii zapasowej prawidłową bazę danych w miejscu. 

Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania zależy od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Ponadto użycie procesora CPU, używane przez kompresja kopii zapasowej może odtwarzać istotną rolę na maszynach wirtualnych z maksymalnie osiem wątki procesora CPU. W związku z tym jeden założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych mniejszy ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (katalogi usługi Stripe, dyski) można zapisać kopii zapasowej do dolnego przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu, dwie opcje może być używany/łączyć w zależności od potrzeb:

* Stosowanie wolumin docelowy kopii zapasowej przez wiele dysków, aby zwiększyć przepływność operacji We/Wy na tego woluminu rozłożonego
* Pisanie kopii zapasowej w celu przy użyciu więcej niż jeden katalog docelowy

#### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii
Serwera klastrów firmy Microsoft (MSCS) nie jest obsługiwane.

Odzyskiwanie po awarii wysoką dostępność bazy danych DB2 (HADR) jest obsługiwane. Jeśli działa rozpoznawanie nazw maszyn wirtualnych w konfiguracji wysokiej dostępności, konfiguracji na platformie Azure nie różni się od wszelkich konfiguracji, która jest wykonywane w środowisku lokalnym. Nie zaleca się opierać się na tylko rozpoznawania adresu IP.

Nie należy używać replikacji geograficznej dla kont magazynu, w których są przechowywane dyski bazy danych. Aby uzyskać więcej informacji można znaleźć w rozdziale [usługi Microsoft Azure Storage] [ dbms-guide-2.3] i rozdział [wysoką dostępność i odzyskiwanie po awarii z maszyn wirtualnych platformy Azure] [ dbms-guide-3].

#### <a name="other"></a>Inne
Wszystkie inne ogólnych zagadnień, takich jak zestawy dostępności platformy Azure lub SAP monitorowanie stosowane zgodnie z opisem w pierwszych trzech rozdziały ten dokument w celu wdrożenia maszyn wirtualnych przy użyciu programu IBM DB2 for LUW także. 

Również znaleźć w rozdziale [ogólne programu SQL Server dla SAP w systemie Azure Podsumowanie][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Charakterystyka programem IBM DB2 LUW w systemie Linux
Platforma Microsoft Azure możesz z łatwością migrować istniejącą aplikację SAP systemem IBM DB2 dla systemów Linux, UNIX i Windows (LUW) do maszyn wirtualnych platformy Azure. Korzystając z oprogramowania SAP, IBM DB2 for LUW Administratorzy i deweloperzy można nadal używać tych samych programowania i narzędzi administracyjnych, które są dostępne lokalnie. Ogólne informacje o systemie SAP Business Suite IBM DB2 dla LUW można znaleźć w sieci społeczności SAP (SCN) na <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Aby uzyskać dodatkowe informacje i aktualizacjach oprogramowania SAP na bazy danych DB2 for LUW na platformie Azure, zobacz Uwaga SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 dla systemów Linux, UNIX i obsługa wersji Windows
Środowisko SAP na IBM DB2 for LUW na usług Microsoft Azure Virtual Machine jest obsługiwane począwszy od wersji bazy danych DB2 10.5.

Informacje dotyczące obsługiwanych produktów SAP i typów maszyn wirtualnych platformy Azure, zapoznaj się uwagę [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 dla systemów Linux, UNIX i wskazówki dotyczące konfigurowania Windows w przypadku instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration"></a>Konfiguracja magazynu
Wszystkie pliki bazy danych muszą być przechowywane w systemie plików, w oparciu o bezpośrednio dołączonych dysków. Te dyski są zainstalowane na maszynie Wirtualnej platformy Azure i opierają się w usłudze Azure Storage BLOB strony (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) lub dyski Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Każdy rodzaj dyski sieciowe lub udziałach zdalnych, takich jak następujące usługi plików platformy Azure są **nie** obsługiwane dla plików bazy danych:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Jeśli używasz dysków, w oparciu o usługi Azure Page BLOB Storage, instrukcji zawartych w tym dokumencie w rozdziale [struktury wdrożenia RDBMS] [ dbms-guide-2] mają zastosowanie również do wdrożenia przy użyciu programu IBM DB2 LUW bazy danych.

Jak wyjaśniono wcześniej w głównej części dokumentu, istnieje przydziały na przepustowość operacji We/Wy dysków. Dokładne przydziały zależą od typu maszyny Wirtualnej, używane. Lista typów maszyn wirtualnych z ich limitami przydziału można znaleźć [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Tak długo, jak bieżący limit przydziału operacji We/Wy na dysku jest wystarczająca, jest możliwość przechowywania wszystkich plików bazy danych na jednym dysku jednego.

Wydajności zagadnienia dotyczące również można znaleźć w rozdziale "Bezpieczeństwa danych i zagadnienia dotyczące wydajności w przypadku katalogów bazy danych" w przewodników po instalacji SAP.

Alternatywnie, można użyć LVM (Menedżer woluminów logicznych) lub MDADM zgodnie z opisem w rozdziale [macierzy RAID oprogramowania] [ dbms-guide-2.2] tego dokumentu, aby utworzyć jeden duży urządzenia logicznego przez wiele dysków.
Dyski zawierające ścieżki magazynu bazy danych DB2 katalogów sapdata i saptmp katalogów należy określić rozmiar sektora dysku fizycznego, 512 KB.

#### <a name="backuprestore"></a>Tworzenie i przywracanie kopii zapasowych
Funkcja/przywracania kopii zapasowej programu IBM DB2 for LUW jest obsługiwana w taki sam sposób, jak lokalnie standardowa Linux instalacji.

Upewnij się, że strategii tworzenia kopii zapasowej prawidłową bazę danych w miejscu.

Jak wdrożenia bez systemu operacyjnego wydajność tworzenia/przywracania zależy od liczby woluminów mogą być odczytywane w sposób równoległy i co przepływność tych woluminów może być. Ponadto użycie procesora CPU, używane przez kompresja kopii zapasowej może odtwarzać istotną rolę na maszynach wirtualnych z maksymalnie osiem wątki procesora CPU. W związku z tym jeden założyć:

* Mniej liczbę dysków używanych do przechowywania urządzenia bazy danych mniejszy ogólną przepływność odczytu
* Im mniejsza liczba procesorów wątki na maszynie Wirtualnej, przeprowadzanie bardziej dotkliwych wpływ kompresja kopii zapasowej
* Mniejszą liczbę elementów docelowych (katalogi usługi Stripe, dyski) można zapisać kopii zapasowej do dolnego przepływności

Aby zwiększyć liczbę elementów docelowych do zapisu, dwie opcje może być używany/łączyć w zależności od potrzeb:

* Stosowanie wolumin docelowy kopii zapasowej przez wiele dysków, aby zwiększyć przepływność operacji We/Wy na tego woluminu rozłożonego
* Pisanie kopii zapasowej w celu przy użyciu więcej niż jeden katalog docelowy

#### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii
Odzyskiwanie po awarii wysoką dostępność bazy danych DB2 (HADR) jest obsługiwane. Jeśli działa rozpoznawanie nazw maszyn wirtualnych w konfiguracji wysokiej dostępności, konfiguracji na platformie Azure nie różni się od wszelkich konfiguracji, która jest wykonywane w środowisku lokalnym. Nie zaleca się opierać się na tylko rozpoznawania adresu IP.

Nie należy używać replikacji geograficznej dla kont magazynu, w których są przechowywane dyski bazy danych. Aby uzyskać więcej informacji można znaleźć w rozdziale [usługi Microsoft Azure Storage] [ dbms-guide-2.3] i rozdział [wysoką dostępność i odzyskiwanie po awarii z maszyn wirtualnych platformy Azure] [ dbms-guide-3].

#### <a name="other"></a>Inne
Wszystkie inne ogólne tematów, takich jak zestawy dostępności platformy Azure lub SAP monitorowanie stosowane zgodnie z opisem w pierwszych trzech rozdziały ten dokument w celu wdrożenia maszyn wirtualnych przy użyciu programu IBM DB2 for LUW także.

Również znaleźć w rozdziale [ogólne programu SQL Server dla SAP w systemie Azure Podsumowanie][dbms-guide-5.8].

