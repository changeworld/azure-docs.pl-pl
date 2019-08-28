---
title: Wdrażanie oprogramowania SAP ASE Azure Virtual Machines DBMS dla obciążeń SAP | Microsoft Docs
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101323"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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



W tym dokumencie omówiono kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania oprogramowania SAP ASE na platformie Azure IaaS. Jako warunek wstępny do tego dokumentu należy przeczytać zagadnienia dotyczące dokumentu [dotyczące wdrożenia systemu azure Virtual Machines DBMS dotyczące obciążeń SAP](dbms_guide_general.md) i innych przewodników w obciążeniu [SAP w dokumentacji platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Specyficzne dla oprogramowania SAP ASE w systemie Windows
Począwszy od Microsoft Azure można migrować istniejące aplikacje SAP ASE do usługi Azure Virtual Machines. System SAP ASE na maszynie wirtualnej platformy Azure umożliwia zredukowanie całkowitego kosztu posiadania wdrożenia, zarządzania i konserwacji aplikacji korporacyjnych przez łatwe Migrowanie tych aplikacji do Microsoft Azure. W przypadku oprogramowania SAP ASE na maszynie wirtualnej platformy Azure Administratorzy i deweloperzy mogą nadal korzystać z tych samych narzędzi programistycznych i administracyjnych, które są dostępne lokalnie.

Umowy SLA for Azure Virtual Machines można znaleźć tutaj:<https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure oferuje wiele różnych typów maszyn wirtualnych, które pozwalają uruchamiać najmniejsze systemy SAP i Landscapes do dużych systemów SAP i Landscapes z tysiącami użytkowników. Numery punktów SAP ustalania rozmiarów dla różnych jednostek SKU certyfikowanej maszyny wirtualnej SAP są udostępniane w programie SAP Note [1928533].

Instrukcje i zalecenia dotyczące użycia usługi Azure Storage, wdrożenia maszyn wirtualnych SAP lub monitorowania SAP zawarto w temacie [zagadnienia dotyczące wdrażania systemu azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md) dotyczą również wdrożeń oprogramowania SAP ASE.

### <a name="sap-ase-version-support"></a>Obsługa wersji oprogramowania SAP ASE
SAP obecnie obsługuje oprogramowanie SAP ASE w wersji 16,0 do użycia z produktami SAP Business Suite. Wszystkie aktualizacje dla serwera SAP ASE lub sterowników JDBC i ODBC, które mają być używane z produktami SAP Business Suite, są dostępne wyłącznie za pośrednictwem platformy SAP <https://support.sap.com/swdc>Service Marketplace w:.

Nie pobieraj aktualizacji dla serwera SAP ASE ani sterowników JDBC i ODBC bezpośrednio z witryn sieci Web programu Sybase. Aby uzyskać szczegółowe informacje na temat poprawek, które są obsługiwane do użytku z produktami SAP lokalnie i na platformie Virtual Machines Azure, zobacz następujące informacje dotyczące oprogramowania SAP:

* [1590719]
* [1973241]

Ogólne informacje na temat uruchamiania programu SAP Business Suite w systemie SAP ASE można znaleźć w temacie [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Wskazówki dotyczące konfiguracji oprogramowania SAP ASE dotyczące instalacji oprogramowania SAP ASE związanych z systemem SAP na maszynach wirtualnych platformy Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura wdrożenia oprogramowania SAP ASE
Pliki wykonywalne oprogramowania SAP ASE należy zlokalizować lub zainstalować na dysku systemowym dysku systemu operacyjnego maszyny wirtualnej (dysk c:\). Zazwyczaj większość baz danych systemu i narzędzi SAP ASE nie ma dużego obciążenia. W związku z tym bazy danych systemu i narzędzi (Master, model, saptools, sybmgmtdb, sybsystemdb) mogą pozostawać na C:\ litera. 

Wyjątkiem może być tymczasowa baza danych, która w przypadku niektórych obciążeń SAP ERP i wszystkie obciążenia z dużą ilością danych może wymagać wyższego woluminu lub woluminu operacji we/wy. Woluminy lub operacje we/wy, które nie mogą zostać dostarczone przez dysk systemu operacyjnego\)maszyny wirtualnej (dysk C:.

W zależności od wersji SAPInst/SWPM używanej do instalacji, konfiguracja wystąpienia oprogramowania SAP ASE może wyglądać następująco:

* Pojedyncza baza danych oprogramowania SAP ASE, która jest tworzona podczas instalacji oprogramowania SAP ASE
* Baza danych oprogramowania SAP ASE utworzona przez zainstalowanie oprogramowania SAP ASE i dodatkowych saptempdb utworzonych przez procedurę instalacji SAP
* System SAP ASE tempdb utworzony przez zainstalowanie oprogramowania SAP ASE i dodatkowej bazy danych tempdb, która została utworzona ręcznie (na przykład z uwagi na temat oprogramowania SAP [1752266]) w celu spełnienia wymagań dotyczących usługi w zakresie rozwiązań ERP/BW

Ze względu na wydajność dla konkretnych obciążeń ERP lub wszystkie obciążenia w sieci, warto mieć sens, aby przechowywać w bazie danych tempdb urządzenia z dodatkowo utworzoną tempdb na dysku innym niż C:\. Jeśli nie istnieje dodatkowa baza danych tempdb, zaleca się jej utworzenie (Uwaga: [1752266]).

W przypadku takich systemów należy wykonać następujące kroki dla dodatkowo utworzonej bazy danych tempdb:

* Przenoszenie pierwszego urządzenia z bazą danych tempdb
* Dodawanie urządzeń tempdb do każdego z dysków VHD zawierających urządzenie z bazą danych SAP

Ta konfiguracja powoduje, że baza danych tempdb zużywa więcej miejsca niż zapewnia dysk systemowy. Jako odniesienia można sprawdzić rozmiary urządzeń tempdb w istniejących systemach, które działają lokalnie. Lub taka konfiguracja włącza liczby IOPS dla bazy danych tempdb, która nie może zostać dostarczona z dyskiem systemowym. Systemy działające lokalnie mogą służyć do monitorowania obciążenia we/wy względem bazy danych tempdb.

Nigdy nie umieszczaj żadnych urządzeń z oprogramowaniem SAP ASE na D:\ dysk maszyny wirtualnej. W przypadku oprogramowania SAP ASE te porady dotyczą również bazy danych tempdb, nawet jeśli obiekty przechowywane w bazie danych tempdb są tylko tymczasowe.

W przypadku wdrożeń plików dziennika transakcji oraz instrukcji i sugestii dotyczących [wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). W przypadku wdrożeń opartych na systemie Windows zaleca się użycie funkcji miejsca do magazynowania systemu Windows do tworzenia zestawów rozłożonych z wystarczającą liczbą operacji we/wy na sekundę, przepływności i woluminu.  

#### <a name="impact-of-database-compression"></a>Wpływ kompresji bazy danych
W konfiguracjach, w których przepustowość operacji we/wy może stać się czynnikem ograniczającym, każda miara, która zmniejsza liczbę operacji wejścia/wyjścia, może pomóc w rozciągnięciu obciążenia. Dlatego zaleca się, aby przed przekazaniem istniejącej bazy danych SAP na platformę Azure była używana kompresja SAP ASE.

Zalecenie dotyczące zastosowania kompresji przed przekazaniem na platformę Azure wynika z kilku powodów:

* Ilość danych do przekazania na platformę Azure jest niższa
* Czas trwania wykonywania kompresji jest krótszy, przy założeniu, że jeden może korzystać z silniejszego sprzętu z większą liczbą procesorów CPU lub wyższą przepustowością operacji we/wy lub mniejszą zwłoką we/wy w środowisku lokalnym
* Mniejsze rozmiary baz danych mogą prowadzić do mniejszej ilości kosztów alokacji dysku

Dane i kompresja LOB są wykonywane na maszynie wirtualnej hostowanej na platformie Azure Virtual Machines jako lokalna. Aby uzyskać więcej informacji na temat sprawdzania, czy kompresja jest już używana w istniejącej bazie danych SAP ASE, należy sprawdzić, czy jest to rozwiązanie SAP Uwaga [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Monitorowanie wystąpień bazy danych za pomocą DBACockpit
W przypadku systemów SAP, które korzystają z oprogramowania SAP ASE jako platformy bazy danych, DBACockpit jest dostępny jako osadzone okna przeglądarki w transakcjach DBACockpit lub jako WebDynpro. Jednak Pełna funkcjonalność monitorowania i administrowania bazą danych jest dostępna tylko w implementacji WebDynpro tylko DBACockpit.

Zgodnie z systemami lokalnymi należy wykonać kilka kroków, aby włączyć wszystkie funkcje SAP NetWeaver używane przez implementację WebDynpro DBACockpit. Postępuj zgodnie z uwagami SAP [1245200] , aby umożliwić użycie webdynpros i wygenerować wymagane. Wykonując instrukcje podane w powyższych informacjach, należy również skonfigurować program Internet Communications Manager (ICM) wraz z portami używanymi na potrzeby połączeń HTTP i https. Domyślne ustawienie dla protokołu HTTP wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a linki wygenerowane w ramach transakcji DBACockpit wyglądają podobnie do:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od tego, jak maszyna wirtualna platformy Azure hostującym system SAP jest połączona z usługami AD i DNS, należy się upewnić, że ICM korzysta z w pełni kwalifikowanej nazwy hosta, którą można rozwiązać na komputerze, na którym jest otwierany DBACockpit. Zobacz temat SAP Uwaga [773830] , aby zrozumieć, jak ICM określa w pełni kwalifikowaną nazwę hosta na podstawie parametrów profilu i w razie potrzeby jawnie ustaw parametr ICM/host_name_full.

Jeśli maszyna wirtualna została wdrożona w scenariuszu obejmującym tylko chmurę bez połączenia między środowiskiem lokalnym i platformą Azure, musisz zdefiniować publiczny adres IP i domainlabel. Format publicznej nazwy DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Więcej szczegółów dotyczących nazwy DNS można znaleźć [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametrów profilu SAP/host_name_full na nazwę DNS maszyny wirtualnej platformy Azure link może wyglądać podobnie do:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

W takim przypadku należy upewnić się, że:

* Dodaj reguły ruchu przychodzącego do sieciowej grupy zabezpieczeń w Azure Portal dla portów TCP/IP używanych do komunikacji z funkcją ICM
* Dodawanie reguł ruchu przychodzącego do konfiguracji zapory systemu Windows dla portów TCP/IP używanych do komunikowania się z funkcją ICM

W przypadku zautomatyzowanego importowania wszystkich dostępnych poprawek zaleca się okresowe stosowanie do nich uwagi SAP dotyczącej kolekcji poprawek:

* [1558958]
* [1619967]
* [1882376]

Więcej informacji na temat panelu sterowania usługi DBA for SAP ASE można znaleźć w następujących informacjach o oprogramowaniu SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Uwagi dotyczące tworzenia kopii zapasowej/odzyskiwania dla oprogramowania SAP ASE
W przypadku wdrażania oprogramowania SAP ASE na platformie Azure metodologia tworzenia kopii zapasowych musi zostać sprawdzona. Nawet w przypadku systemów nieprodukcyjnych należy okresowo wykonywać kopie zapasowe baz danych SAP. Ponieważ usługa Azure Storage przechowuje trzy obrazy, kopia zapasowa może być mniej ważna w odniesieniu do kompensacji awarii magazynu. Głównym powodem, aby zachować właściwy plan tworzenia kopii zapasowych i przywracania, jest więcej informacji o błędach związanych z odzyskiwaniem do punktu w czasie. W związku z tym celem jest użycie kopii zapasowych do przywrócenia bazy danych z powrotem do określonego punktu w czasie lub do użycia kopii zapasowych na platformie Azure do wypełniania innego systemu przez skopiowanie istniejącej bazy danych. 

Tworzenie kopii zapasowej i przywracanie bazy danych na platformie Azure działa tak samo, jak w środowisku lokalnym. Zobacz uwagi dotyczące oprogramowania SAP:

* [1588316]
* [1585981]

Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji zrzutów i planowania kopii zapasowych. W zależności od strategii i potrzeb można skonfigurować zrzuty bazy danych i dziennika na dysk do jednego z istniejących dysków lub dodać dodatkowy dysk do tworzenia kopii zapasowej. Aby zmniejszyć niebezpieczeństwo utraty danych w przypadku wystąpienia błędu, zaleca się użycie dysku, na którym nie znajdują się pliki bazy danych.

Oprócz kompresji danych i obiektów LOB środowisko SAP ASE oferuje również kompresję kopii zapasowej. Aby używać mniejszej ilości miejsca w bazie danych i zrzutów dzienników zaleca się użycie kompresji kopii zapasowej. Aby uzyskać więcej informacji, zobacz temat SAP Note [1588316]. Kompresowanie kopii zapasowej jest również kluczowe, aby zmniejszyć ilość przesyłanych danych, jeśli planujesz pobrać kopie zapasowe lub dyski VHD zawierające zrzuty kopii zapasowych z maszyny wirtualnej platformy Azure do lokacji lokalnej.

Nie używaj D:\ dysków jako miejsce docelowe zrzutu bazy danych lub dziennika.

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące wydajności tworzenia kopii zapasowych/przywracania
Podobnie jak w przypadku wdrożeń bez systemu operacyjnego, wydajność tworzenia kopii zapasowej/przywracania zależy od liczby woluminów, które mogą być jednocześnie odczytywane i co może być przepływność tych woluminów. Należy pamiętać, że kompresja kopii zapasowych zużywa zasoby procesora CPU. Użycie procesora CPU przez kompresję kopii zapasowych może odgrywać znaczącą rolę na maszynach wirtualnych z niewielką liczbą wątków procesora. W związku z tym można założyć, że:

* Im mniejsza liczba dysków używanych do przechowywania urządzeń bazy danych, tym mniejsza ogólna przepływność podczas czytania
* Im mniejsza liczba wątków procesora w maszynie wirtualnej, tym trudniejsze jest wpływ kompresji kopii zapasowej.
* Mniejsza liczba elementów docelowych (katalogów rozłożonych, dysków), do których należy zapisywać kopie zapasowe, im mniejsza przepustowość

Aby zwiększyć liczbę obiektów docelowych do zapisu, dostępne są dwie opcje, które mogą być używane/połączone w zależności od potrzeb:

* Rozłożenie woluminu docelowego kopii zapasowej na wiele dysków zainstalowanych w celu zwiększenia przepływności operacji we/wy na woluminie rozłożonym
* Tworzenie konfiguracji zrzutu na poziomie oprogramowania SAP ASE, która używa więcej niż jednego katalogu docelowego do zapisywania zrzutu

Rozłożenie woluminu dysku na wiele zainstalowanych dysków zostało omówione w temacie [uwagi dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md). Więcej informacji o używaniu wielu katalogów w konfiguracji zrzutów SAP ASE można znaleźć w dokumentacji procedury składowanej sp_config_dump, która jest używana do tworzenia konfiguracji zrzutu w programie [Sybase InfoCenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Odzyskiwanie po awarii z maszynami wirtualnymi platformy Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikacja danych przy użyciu serwera replikacji SAP Sybase
Dzięki serwerowi replikacji SAP Sybase (SRS) system SAP ASE udostępnia rozwiązanie do rezerwy w stanie rozgrzanym na ciepło w celu asynchronicznego transferu transakcji bazy danych do odległej lokalizacji. 

Instalacja i działanie usługi SRS działa prawidłowo w przypadku maszyn wirtualnych hostowanych w usługach Azure Virtual Machine Services jako lokalnych.

Oprogramowanie SAP ASE HADR Cluster nie wymaga wewnętrznego Load Balancer platformy Azure i nie ma żadnych zależności dotyczących klastrowania na poziomie systemu operacyjnego. Działa on na maszynach wirtualnych z systemem Windows i Linux. Szczegółowe informacje na temat oprogramowania SAP ASE HADR Cluster można znaleźć w [podręczniku użytkowników programu SAP ASE HADR Cluster](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Specyficzne dla oprogramowania SAP ASE w systemie Linux
Począwszy od Microsoft Azure można łatwo migrować istniejące aplikacje SAP ASE do usługi Azure Virtual Machines. System SAP ASE na maszynie wirtualnej umożliwia zredukowanie całkowitego kosztu posiadania wdrożenia, zarządzania i konserwacji aplikacji korporacyjnych przez łatwe Migrowanie tych aplikacji do Microsoft Azure. W przypadku oprogramowania SAP ASE na maszynie wirtualnej platformy Azure Administratorzy i deweloperzy mogą nadal korzystać z tych samych narzędzi programistycznych i administracyjnych, które są dostępne lokalnie.

W przypadku wdrażania maszyn wirtualnych platformy Azure ważne jest, aby znać oficjalną umowy SLA, którą można znaleźć tutaj:<https://azure.microsoft.com/support/legal/sla>

Informacje o ustalaniu wielkości SAP i listę jednostek SKU certyfikowanej maszyny wirtualnej SAP są dostępne w programie SAP Note [1928533]. Dodatkowe dokumenty dotyczące ustalania rozmiarów SAP dla maszyn wirtualnych platformy Azure <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> można znaleźć tutaj i<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instrukcje i zalecenia dotyczące użycia usługi Azure Storage, wdrożenia maszyn wirtualnych SAP lub monitorowania SAP mają zastosowanie do wdrożeń oprogramowania SAP ASE w połączeniu z aplikacjami SAP zgodnie z opisem w pierwszym z czterech rozdziałów tego dokumentu.

Poniższe dwa uwagi SAP zawierają ogólne informacje dotyczące środowiska ASE w systemie Linux i ASE w chmurze:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Obsługa wersji oprogramowania SAP ASE
SAP obecnie obsługuje oprogramowanie SAP ASE w wersji 16,0 do użycia z produktami SAP Business Suite. Wszystkie aktualizacje dla serwera SAP ASE lub sterowników JDBC i ODBC, które mają być używane z produktami SAP Business Suite, są dostępne wyłącznie za pośrednictwem platformy SAP <https://support.sap.com/swdc>Service Marketplace w:.

Tak jak w przypadku instalacji lokalnych, nie pobieraj aktualizacji serwera SAP ASE ani sterowników JDBC i ODBC bezpośrednio z witryn sieci Web programu Sybase. Aby uzyskać szczegółowe informacje na temat poprawek, które są obsługiwane w przypadku produktów SAP Business Suite w środowisku lokalnym i na platformie Virtual Machines Azure, zobacz następujące informacje dotyczące oprogramowania SAP:

* [1590719]
* [1973241]

Ogólne informacje na temat uruchamiania programu SAP Business Suite w systemie SAP ASE można znaleźć w temacie [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Wskazówki dotyczące konfiguracji oprogramowania SAP ASE dotyczące instalacji oprogramowania SAP ASE związanych z systemem SAP na maszynach wirtualnych platformy Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura wdrożenia oprogramowania SAP ASE
Pliki wykonywalne oprogramowania SAP ASE powinny być zlokalizowane lub instalowane w głównym systemie plików maszyny wirtualnej (/Sybase). Zazwyczaj większość baz danych systemu i narzędzi SAP ASE nie ma dużego obciążenia. W związku z tym bazy danych systemu i narzędzi (Master, model, saptools, sybmgmtdb, sybsystemdb) mogą być przechowywane w głównym systemie plików. 

Wyjątkiem może być tymczasowa baza danych, która w przypadku niektórych obciążeń SAP ERP i wszystkie obciążenia z dużą ilością danych może wymagać wyższego woluminu lub woluminu operacji we/wy. Woluminy lub operacje we/wy, które nie mogą zostać dostarczone przez dysk systemu operacyjnego maszyny wirtualnej 

W zależności od wersji SAPInst/SWPM używanej do instalacji systemu baza danych może zawierać następujące:

* Pojedyncza baza danych oprogramowania SAP ASE utworzona podczas instalacji oprogramowania SAP ASE
* Baza danych oprogramowania SAP ASE utworzona przez zainstalowanie oprogramowania SAP ASE i dodatkowych saptempdb utworzonych przez procedurę instalacji SAP
* System SAP ASE tempdb utworzony przez zainstalowanie oprogramowania SAP ASE i dodatkowej bazy danych tempdb, która została utworzona ręcznie (na przykład z uwagi na temat oprogramowania SAP [1752266]) w celu spełnienia wymagań dotyczących usługi w zakresie rozwiązań ERP/BW

Ze względu na wydajność dla konkretnych obciążeń ERP lub wszystkie obciążenia w sieci, warto mieć sens, aby przechowywać urządzenia tempdb z dodatkowo utworzoną bazą danych tempdb (przez SWPM lub ręcznie) w osobnym systemie plików, który może być reprezentowany przez pojedynczy dysk z danymi platformy Azure lub macierz RAID z systemem Linux. l dyski danych platformy Azure. Jeśli nie istnieje dodatkowa baza danych tempdb, zaleca się jej utworzenie (Uwaga: [1752266]).

W przypadku takich systemów należy wykonać następujące kroki dla dodatkowo utworzonej bazy danych tempdb:

* Przenoszenie pierwszego katalogu tempdb do pierwszego systemu plików bazy danych SAP
* Dodaj katalogi tempdb do każdego z dysków zawierających system plików bazy danych SAP

Ta konfiguracja powoduje, że baza danych tempdb zużywa więcej miejsca niż zapewnia dysk systemowy. Jako odniesienia można sprawdzić rozmiary urządzeń tempdb w istniejących systemach, które działają lokalnie. Lub taka konfiguracja włącza liczby IOPS dla bazy danych tempdb, która nie może zostać dostarczona z dyskiem systemowym. Systemy działające lokalnie mogą służyć do monitorowania obciążenia we/wy względem bazy danych tempdb.

Nigdy nie umieszczaj żadnych katalogów SAP ASE na/mnt lub/mnt/Resource maszyny wirtualnej. W przypadku oprogramowania SAP ASE te porady dotyczą również bazy danych tempdb, nawet jeśli obiekty przechowywane w bazie danych tempdb są tylko tymczasowe. Ponieważ/mnt lub/mnt/Resource jest domyślnym miejscem na maszynę wirtualną platformy Azure, który nie jest trwały. Więcej szczegółów na temat miejsca na dysku maszyny wirtualnej platformy Azure można znaleźć w [tym artykule][virtual-machines-linux-how-to-attach-disk]

W przypadku wdrożeń plików dziennika transakcji oraz instrukcji i sugestii dotyczących [wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). W przypadku wdrożeń opartych na systemie Linux użycie LVM lub MDADM jest zalecane do tworzenia zestawów rozłożonych o wystarczającej liczbie operacji we/wy na sekundę, przepływności i woluminu. 

#### <a name="impact-of-database-compression"></a>Wpływ kompresji bazy danych
W konfiguracjach, w których przepustowość operacji we/wy może stać się czynnikem ograniczającym, każda miara, która zmniejsza liczbę operacji wejścia/wyjścia, może pomóc w rozciągnięciu obciążenia. Dlatego zaleca się, aby przed przekazaniem istniejącej bazy danych SAP na platformę Azure była używana kompresja SAP ASE.

Zalecenie dotyczące zastosowania kompresji przed przekazaniem na platformę Azure wynika z kilku powodów:

* Ilość danych do przekazania na platformę Azure jest niższa
* Czas trwania wykonywania kompresji jest krótszy, przy założeniu, że jeden może korzystać z silniejszego sprzętu z większą liczbą procesorów CPU lub wyższą przepustowością operacji we/wy lub mniejszą zwłoką we/wy w środowisku lokalnym
* Mniejsze rozmiary baz danych mogą prowadzić do mniejszej ilości kosztów alokacji dysku

Dane i kompresja LOB są wykonywane na maszynie wirtualnej hostowanej na platformie Azure Virtual Machines jako lokalna. Aby uzyskać więcej informacji na temat sprawdzania, czy kompresja jest już używana w istniejącej bazie danych SAP ASE, należy sprawdzić, czy jest to rozwiązanie SAP Uwaga [1750510]. Aby uzyskać więcej informacji na temat kompresji bazy danych, zobacz temat SAP Note [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Monitorowanie wystąpień bazy danych za pomocą DBACockpit
W przypadku systemów SAP, które korzystają z oprogramowania SAP ASE jako platformy bazy danych, DBACockpit jest dostępny jako osadzone okna przeglądarki w transakcjach DBACockpit lub jako WebDynpro. Jednak Pełna funkcjonalność monitorowania i administrowania bazą danych jest dostępna tylko w implementacji WebDynpro tylko DBACockpit.

Zgodnie z systemami lokalnymi należy wykonać kilka kroków, aby włączyć wszystkie funkcje SAP NetWeaver używane przez implementację WebDynpro DBACockpit. Postępuj zgodnie z uwagami SAP [1245200] , aby umożliwić użycie webdynpros i wygenerować wymagane. Wykonując instrukcje podane w powyższych informacjach, należy również skonfigurować program Internet Communications Manager (ICM) wraz z portami używanymi na potrzeby połączeń HTTP i https. Domyślne ustawienie protokołu HTTP wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a linki wygenerowane w ramach transakcji DBACockpit będą wyglądać podobnie do tego:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od tego, jak maszyna wirtualna platformy Azure hostującym system SAP jest połączona z usługami AD i DNS, należy się upewnić, że ICM korzysta z w pełni kwalifikowanej nazwy hosta, którą można rozwiązać na komputerze, na którym jest otwierany DBACockpit. Zobacz temat SAP Uwaga [773830] , aby zrozumieć, jak ICM określa w pełni kwalifikowaną nazwę hosta w zależności od parametrów profilu i w razie potrzeby jawnie ustaw parametr ICM/host_name_full.

Jeśli maszyna wirtualna została wdrożona w scenariuszu obejmującym tylko chmurę bez połączenia między środowiskiem lokalnym i platformą Azure, musisz zdefiniować publiczny adres IP i domainlabel. Format publicznej nazwy DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Więcej szczegółów dotyczących nazwy DNS można znaleźć [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametrów profilu SAP/host_name_full na nazwę DNS maszyny wirtualnej platformy Azure link może wyglądać podobnie do:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

W takim przypadku należy upewnić się, że:

* Dodaj reguły ruchu przychodzącego do sieciowej grupy zabezpieczeń w Azure Portal dla portów TCP/IP używanych do komunikacji z funkcją ICM
* Dodawanie reguł ruchu przychodzącego do konfiguracji zapory systemu Windows dla portów TCP/IP używanych do komunikowania się z funkcją ICM

W przypadku zautomatyzowanego importowania wszystkich dostępnych poprawek zaleca się okresowe stosowanie do nich uwagi SAP dotyczącej kolekcji poprawek:

* [1558958]
* [1619967]
* [1882376]

Więcej informacji na temat panelu sterowania usługi DBA for SAP ASE można znaleźć w następujących informacjach o oprogramowaniu SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Uwagi dotyczące tworzenia kopii zapasowej/odzyskiwania dla oprogramowania SAP ASE
W przypadku wdrażania oprogramowania SAP ASE na platformie Azure metodologia tworzenia kopii zapasowych musi zostać sprawdzona. Nawet w przypadku systemów nieprodukcyjnych należy okresowo wykonywać kopie zapasowe baz danych SAP. Ponieważ usługa Azure Storage przechowuje trzy obrazy, kopia zapasowa może być mniej ważna w odniesieniu do kompensacji awarii magazynu. Głównym powodem, aby zachować właściwy plan tworzenia kopii zapasowych i przywracania, jest więcej informacji o błędach związanych z odzyskiwaniem do punktu w czasie. W związku z tym celem jest użycie kopii zapasowych do przywrócenia bazy danych z powrotem do określonego punktu w czasie lub do użycia kopii zapasowych na platformie Azure do wypełniania innego systemu przez skopiowanie istniejącej bazy danych. 

Tworzenie kopii zapasowej i przywracanie bazy danych na platformie Azure działa tak samo, jak w środowisku lokalnym. Zobacz uwagi dotyczące oprogramowania SAP:

* [1588316]
* [1585981]

Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji zrzutów i planowania kopii zapasowych. W zależności od strategii i potrzeb można skonfigurować zrzuty bazy danych i dziennika na dysk do jednego z istniejących dysków lub dodać dodatkowy dysk do tworzenia kopii zapasowej. Aby zmniejszyć niebezpieczeństwo utraty danych w przypadku wystąpienia błędu, zaleca się użycie dysku, na którym nie znajduje się katalog/plik bazy danych.

Oprócz kompresji danych i obiektów LOB środowisko SAP ASE oferuje również kompresję kopii zapasowej. Aby używać mniejszej ilości miejsca w bazie danych i zrzutów dzienników zaleca się użycie kompresji kopii zapasowej. Aby uzyskać więcej informacji, zobacz temat SAP Note [1588316]. Kompresowanie kopii zapasowej jest również kluczowe, aby zmniejszyć ilość przesyłanych danych, jeśli planujesz pobrać kopie zapasowe lub dyski VHD zawierające zrzuty kopii zapasowych z maszyny wirtualnej platformy Azure do lokacji lokalnej.

Nie należy używać tymczasowego miejsca maszyny wirtualnej platformy Azure/mnt lub/mnt/Resource jako lokalizacji docelowej bazy danych lub dziennika.

#### <a name="performance-considerations-for-backupsrestores"></a>Zagadnienia dotyczące wydajności tworzenia kopii zapasowych/przywracania
Podobnie jak w przypadku wdrożeń bez systemu operacyjnego, wydajność tworzenia kopii zapasowej/przywracania zależy od liczby woluminów, które mogą być jednocześnie odczytywane i co może być przepływność tych woluminów. Należy pamiętać, że kompresja kopii zapasowych zużywa zasoby procesora CPU. Użycie procesora CPU przez kompresję kopii zapasowych może odgrywać znaczącą rolę na maszynach wirtualnych z niewielką liczbą wątków procesora.  W związku z tym można założyć, że:

* Im mniejsza liczba dysków używanych do przechowywania urządzeń bazy danych, tym mniejsza ogólna przepływność podczas czytania
* Im mniejsza liczba wątków procesora w maszynie wirtualnej, tym trudniejsze jest wpływ kompresji kopii zapasowej.
* Mniejsza liczba elementów docelowych (RAID oprogramowania w systemie Linux, dysków), do których należy zapisywać kopie zapasowe, im mniejsza przepustowość

Aby zwiększyć liczbę obiektów docelowych do zapisu, dostępne są dwie opcje, które mogą być używane/połączone w zależności od potrzeb:

* Rozłożenie woluminu docelowego kopii zapasowej na wiele dysków zainstalowanych w celu zwiększenia przepływności operacji we/wy na woluminie rozłożonym
* Tworzenie konfiguracji zrzutu na poziomie oprogramowania SAP ASE, która używa więcej niż jednego katalogu docelowego do zapisywania zrzutu

Rozłożenie woluminu dysku na wiele zainstalowanych dysków zostało omówione w temacie [uwagi dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md). Więcej informacji o używaniu wielu katalogów w konfiguracji zrzutów SAP ASE można znaleźć w dokumentacji procedury składowanej sp_config_dump, która jest używana do tworzenia konfiguracji zrzutu w programie [Sybase InfoCenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Odzyskiwanie po awarii z maszynami wirtualnymi platformy Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikacja danych przy użyciu serwera replikacji SAP Sybase
Dzięki serwerowi replikacji SAP Sybase (SRS) system SAP ASE udostępnia rozwiązanie do rezerwy w stanie rozgrzanym na ciepło w celu asynchronicznego transferu transakcji bazy danych do odległej lokalizacji. 

Instalacja i działanie usługi SRS działa prawidłowo w przypadku maszyn wirtualnych hostowanych w usługach Azure Virtual Machine Services jako lokalnych.

Środowisko ASE HADR Cluster za pośrednictwem serwera replikacji SAP jest obsługiwane. Zdecydowanie zaleca się używanie oprogramowania SAP ASE 16,03 do próby takiej konfiguracji. Więcej szczegółowych instrukcji dotyczących instalowania takich konfiguracji można znaleźć szczegółowo w tym [blogu](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
