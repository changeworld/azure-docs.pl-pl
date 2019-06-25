---
title: Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver | Dokumentacja firmy Microsoft
description: Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver
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
ms.date: 05/07/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e983a3f8e29108bd642fd23d5afcb564065d9fc1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203897"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
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
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure umożliwia firmom nabyć zasoby obliczeniowe i magazynowe w krótkim czasie bez wydłużonych cykli. Usługi maszyny wirtualnej platformy Azure umożliwia firmom wdrożenia klasycznego aplikacje, takie jak oprogramowanie SAP NetWeaver na podstawie aplikacji do platformy Azure i rozszerzać ich niezawodności i dostępności bez konieczności dalszej zasoby dostępne lokalnie. Usługi maszyny wirtualnej platformy Azure obsługuje również łączności między środowiskami lokalnymi, które umożliwia przedsiębiorstwom aktywnie integracji usługi Azure Virtual Machines w swoich domen lokalnych ich chmur prywatnych i ich środowisko systemu SAP.
Ten dokument w tym artykule opisano podstawowe informacje dotyczące maszyny wirtualnej platformy Azure firmy Microsoft i zawiera omówienie zagadnienia dotyczące instalacje oprogramowania SAP NetWeaver na platformie Azure planowania i implementacji i jako takie powinny być dokument, aby przeczytać przed rozpoczęciem rzeczywiste wdrożenia oprogramowania SAP NetWeaver na platformie Azure.
Ten dokument stanowi uzupełnienie dokumentacji instalacji SAP i SAP Notes, którą reprezentują podstawowe zasoby dla instalacji i wdrożenia oprogramowania SAP na podanych platformach.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Podsumowanie
Chmura obliczeniowa jest szeroko używanych terminem, który zyskuje coraz większe znaczenie w branży IT, od małych firm po maksymalnie duże i międzynarodowe korporacje.

Microsoft Azure to platforma usług w chmurze firmy Microsoft, oferująca szerokie spektrum nowych możliwości. Teraz klienci mogą szybko aprowizować i anulować obsługę aplikacji jako usługi w chmurze, więc nie są ograniczone do ograniczeniami technicznymi czy budżetowymi. Zamiast inwestować czas i budżet w infrastrukturę sprzętową, firmy mogą skoncentrować się na aplikację, procesy biznesowe i korzyści dla klientów i użytkowników.

Wraz z usługami Microsoft Azure Virtual Machines firma Microsoft oferuje kompleksową platformę typu infrastruktura jako usługa (IaaS). Aplikacje oparte na oprogramowaniu SAP NetWeaver są obsługiwane w usłudze Azure Virtual Machines (IaaS). Tym oficjalnym dokumencie opisano, jak planować i implementować oprogramowanie SAP NetWeaver na podstawie aplikacji w systemie Microsoft Azure jako wybranej platformie.

Dokument, sama koncentruje się na dwie główne aspekty:

* Pierwsza część w tym artykule opisano dwa wzorce obsługiwane wdrożenia dla aplikacji SAP NetWeaver oparte na platformie Azure. Omówiono także ogólne obsługi platformy Azure wdrożeń SAP na uwadze.
* Drugi szczegóły część wykonania dwóch różnych scenariuszy opisanych w pierwszej części.

Aby uzyskać dodatkowe zasoby, zobacz rozdział [zasobów] [ planning-guide-1.2] w tym dokumencie.

### <a name="definitions-upfront"></a>Definicje ponoszonych z góry kosztów
W dokumencie Stosujemy następujące warunki:

* IaaS: Infrastruktura jako usługa
* PaaS: Platforma jako usługa
* SaaS: Oprogramowanie jako usługa
* Składnik SAP: poszczególnych aplikacji SAP ECC, BW, Menedżer rozwiązania lub S/4HANA.  Składniki SAP mogą być oparte na tradycyjnych technologii ABAP i Java lub aplikacji innych niż NetWeaver na podstawie takich jak obiekty biznesowych.
* Środowisko SAP: co najmniej jednego składnika SAP logicznie pogrupowane do wykonywania funkcji biznesowych, takich jak rozwój, QAS, szkolenia, odzyskiwania po awarii lub produkcji.
* Środowisko SAP: Określenie to odnosi się do całego zasoby SAP klientów pozioma IT. Środowisko SAP obejmuje wszystkie produkcji i środowisk nieprodukcyjnych.
* SAP System: Kombinacja warstwy system DBMS i warstwy aplikacji, na przykład SAP ERP i przeniesieniu jej rozwoju systemu SAP BW system testowy, system produkcyjny SAP CRM, itp. W przypadku wdrożeń platformy Azure go nie jest obsługiwane do dzielenia tych dwóch warstw między lokalną i platformą Azure. Oznacza, że system SAP jest wdrożony w środowisku lokalnym lub jest ona wdrożona na platformie Azure. Można jednak wdrożyć różnych systemów środowisko SAP na platformie Azure lub lokalnie. Na przykład możesz wdrożyć rozwoju SAP CRM i systemy testowe platformie Azure, ale SAP CRM produkcji systemu lokalnego.
* Między środowiskami lokalnymi lub hybrydowej: W tym artykule opisano scenariusz, w której maszyny wirtualne są wdrażane z subskrypcją platformy Azure, site to site, obejmujące wiele lokacji lub połączenia usługi ExpressRoute między zasobom w środowisku lokalnym i platformą Azure. Dokumentacji wspólnych platformy Azure, tego rodzaju wdrożenia są także opisane między środowiskami lokalnymi lub hybrydowe scenariusze. Przyczyna połączenie ma rozszerzone na platformę Azure lokalnych domen Active Directory/OpenLDAP w środowisku lokalnym i DNS w środowisku lokalnym. Pozioma w środowisku lokalnym jest rozszerzony do zasobów platformy Azure w subskrypcji. Problemy to rozszerzenie, maszyn wirtualnych może być częścią domeny w środowisku lokalnym. Użytkownicy domeny lokalnej domeny mogą uzyskiwać dostęp do serwerów i można uruchomić usługi na tych maszynach wirtualnych (np. usługi DBMS). Komunikacja i rozpoznawanie nazw między maszyny wirtualne wdrożone w środowisku lokalnym i wdrożone maszyny wirtualne platformy Azure jest możliwe. Dotyczy to najczęściej używanych i niemal wyłączne wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [to] [ vpn-gateway-cross-premises-options] artykułu i [to][vpn-gateway-site-to-site-create].

> [!NOTE]
> Między lokalizacjami i hybrydowych wdrożeń systemów SAP, w których członkowie lokalnej domeny usługi Azure Virtual Machines z systemami SAP są obsługiwane w przypadku systemów SAP w środowisku produkcyjnym. Między środowiskami lokalnymi lub hybrydowe konfiguracje są obsługiwane w przypadku wdrażania części lub zakończyć krajobrazów SAP na platformie Azure. Jeszcze uruchomione pełne środowisko SAP na platformie Azure wymaga posiadanie tych maszyn wirtualnych, które są częścią lokalnej domeny i REKLAM/OpenLDAP. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Zasoby
Punkt wejścia w przypadku obciążeń SAP w dokumentacji platformy Azure zostanie znaleziony [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Począwszy od tego punktu wejścia możesz znaleźć wiele artykułów obejmujących tematy od:

- SAP NetWeaver i firm jeden na platformie Azure
- Przewodniki systemu SAP DBMS dla różnych systemów DBMS na platformie Azure
- Wysoka dostępność i odzyskiwanie awaryjne w przypadku obciążeń SAP na platformie Azure
- Dokładne wskazówki dotyczące uruchamiania oprogramowania SAP HANA na platformie Azure
- Wskazówki, które są specyficzne dla dużych wystąpień HANA na platformie Azure dla systemu SAP HANA DBMS 


> [!IMPORTANT]
> Wszędzie tam, gdzie możliwe łącze do odwołujących się przewodników po instalacji SAP lub innej dokumentacji SAP jest używana (dokumentacja InstGuide-01, zobacz <http://service.sap.com/instguides>). Jeśli chodzi o wymagania wstępne dotyczące procesu instalacji i szczegółowe informacje o określonych funkcji SAP, SAP, dokumentacja i przewodniki dotyczące należy zawsze przeczytać uważnie, jako Microsoft dokumentów obejmuje tylko określone zadania dla oprogramowania SAP zainstalowana i działa w Maszyna wirtualna platformy Microsoft Azure.
>
>

Poniższe uwagi SAP są związane z tym tematem SAP na platformie Azure:

| Numer | Stanowisko |
| --- | --- |
| [1928533] |Aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i zmianę rozmiaru |
| [2015553] |SAP na platformie Microsoft Azure: Wymagania wstępne dotyczące obsługi |
| [1999351] |Rozwiązywanie problemów z rozszerzonego monitorowania platformy Azure dla rozwiązania SAP |
| [2178632] |Klucz metryki monitorowania dla rozwiązania SAP na platformie Microsoft Azure |
| [1409604] |Wirtualizacja na Windows: Rozszerzone monitorowanie |
| [2191498] |SAP w systemie Linux przy użyciu platformy Azure: Rozszerzone monitorowanie |
| [2243692] |Systemu Linux na platformie Microsoft Azure (IaaS) maszyny Wirtualnej: Problemy dotyczące licencji SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalowanie i uaktualnianie |
| [2069760] |Oracle Linux 7.x SAP instalacji i uaktualniania |
| [1597355] |Zalecenie obszaru wymiany w systemie Linux |

Przeczytaj również [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zawierający wszystkie uwagi SAP dla systemu Linux.

Ograniczenia ogólne domyślną i maksymalną ograniczenia subskrypcji platformy Azure można znaleźć w [w tym artykule][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Możliwe scenariusze
SAP często jest postrzegana jako jedną z kluczowych aplikacji w obrębie przedsiębiorstwa. Architektura i operacje te aplikacje przede wszystkim jest złożona i ważne jest zapewnienie, że są spełnione wymagania dotyczące dostępności i wydajności.

Dlatego w przedsiębiorstwach mają myśleć dokładnie o których dostawcy chmury, aby wybrać do uruchamiania takich firm biznesowych przetwarza na. Platforma Azure to idealne rozwiązanie publicznej platformy w chmurze dla firm krytyczne aplikacje SAP i procesów biznesowych. Mając szeroką gamę infrastruktury platformy Azure, niemal we wszystkich istniejących systemów SAP NetWeaver i S/4HANA, może być hostowana na platformie Azure już dziś. System Azure oferuje maszyn wirtualnych przy użyciu wielu terabajtów pamięci i procesorów ponad 200. Poza nim, że platforma Azure oferuje [dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), które umożliwiają wdrażanie HANA skalowalnego w poziomie do 24 TB i skalowalnego w poziomie ANA wdrożeń do 120 TB. 


W celu wdrażania systemów SAP w modelu IaaS platformy Azure lub IaaS ogólnie rzecz biorąc, ważne jest zrozumienie znaczne różnice między ofert tradycyjnych uzyskanie programu lub dostawcy usług hostingowych i oferty IaaS. Natomiast tradycyjnych dostawcę usług hostingowych lub dostawców dostosowuje infrastruktury (typ sieci, magazynu i serwer) na obciążenie, które klient chce hostować, odpowiada za zamiast tego klienta lub partnera charakteryzuje obciążenia, a następnie wybierz poprawny platformy Azure składniki maszyn wirtualnych, magazynu i sieci w przypadku wdrożeń rozwiązań IaaS.

Pierwszym krokiem klienci muszą sprawdź następujące elementy:

* SAP obsługiwane typy maszyn wirtualnych platformy Azure
* SAP obsługiwane produkty/wersji na platformie Azure
* Obsługiwany system operacyjny i DBMS wersje dla określonych wydaniach SAP na platformie Azure
* Przepływność protokoły SAP, dostarczone przez różne jednostki magazynowe Azure

Odpowiedzi na te pytania mogą być odczytywane w Uwaga SAP [1928533].

W drugim kroku ograniczenia zasobów i przepustowości platformy Azure muszą być porównane do zużycia zasobów rzeczywistych w systemach lokalnych. W związku z tym klienci muszą należy zapoznać się z różnych możliwości obsługiwane z oprogramowaniem SAP, w obszarze typy platformy Azure:

* Zasoby Procesora i pamięci różnych typów maszyn wirtualnych i
* Przepustowość operacji We/Wy o różnych typach maszyn wirtualnych i
* Możliwości sieciowych o różnych typach maszyn wirtualnych.

Większość tych danych można znaleźć [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Należy pamiętać o tym, które ograniczenia wymienione w powyższy link są górne limity. Nie oznacza to, że limity dla zasobów, na przykład operacje We/Wy może być udostępniane we wszystkich okolicznościach. Wyjątki są jednak zasobów Procesora i pamięci wybranego typu maszyny Wirtualnej. Typy maszyn wirtualnych obsługiwanych przez oprogramowanie SAP zasobów Procesora i pamięci są zarezerwowane i w związku z tym dostępna w dowolnym momencie w czasie do użycia na maszynie wirtualnej.

Platforma Microsoft Azure to platforma wielodostępnych. W rezultacie magazynu, sieci i inne zasoby są udostępniane między dzierżawami. Inteligentne logika przydziału i ograniczanie jest stosowana do uniemożliwić wpływ na wydajność innej dzierżawy (hałaśliwego sąsiada) w sposób drastycznym jednej dzierżawy. Szczególnie w przypadku certyfikowania platformy Azure dla oprogramowania SAP HANA, firma Microsoft musi potwierdzić izolację zasobów w przypadkach, gdzie wiele maszyn wirtualnych można uruchomić na tym samym hoście regularnie SAP. Chociaż logiki na platformie Azure próbuje zachować wariancji w przepustowości wystąpił mały, wysoce udostępnionych platformy zwykle wprowadzenie większych odchyleń dostępności zasobów/przepustowości niż klienci mogą wystąpić w swoich lokalnych wdrożeń. Prawdopodobieństwo, że system SAP na platformie Azure mogą wystąpić wariancji większych niż w lokalnych system musi być brane pod uwagę.

Ostatnim krokiem jest do oceny wymagań dotyczących dostępności. Może się zdarzyć, że podstawową infrastrukturą platformy Azure musi zostać zaktualizowana i wymaga hosty działających maszyn wirtualnych, należy ponownie uruchomić. Microsoft dokumenty różne przypadki [Konserwacja maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates). Aby uniknąć rzadkich przypadkach gdzie maszyny wirtualne są wymuszone ponowne uruchomienie, ale jeszcze bardziej ważne w przypadkach, która należy do systemu operacyjnego gościa poprawki lub składniki systemu DBMS należy opracowywanie pojęcia prawidłowe wysoką dostępność systemów produkcyjnych SAP. To wymaganie nie jest inaczej niż wymagania, że twarzy w środowisku lokalnym. Microsoft jest stale przesuwania platformy Azure, aby zredukować przestoje spowodowane zmianami platformy. 

W celu pomyślnego wdrożenia systemu SAP na platformie Azure, lokalnych SAP systemy systemu operacyjnego, bazy danych i aplikacji SAP musi znajdować się na macierz obsługi usługi Azure SAP, mieści się w ramach zasobów platformy Azure może zapewnić infrastruktury i które mogą działać w przypadku ofert dostępność umowy SLA firmy Microsoft Azure. Jak te systemy są identyfikowane, musisz zdecydować się na jednym z następujących scenariuszy wdrażania dwóch.





### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Między środowiskami lokalnymi - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure z wymaganiami w pełni zintegrowane sieci lokalnej
![Sieć VPN z połączeniem lokacja-lokacja (cross-premises)][planning-guide-figure-300]

Ten scenariusz jest scenariusz między środowiskami lokalnymi przy użyciu wielu wzorców możliwe wdrożenie rozwiązania. Można go opisać jako uruchamiania niektórych części SAP pozioma lokalnych i innych części środowisko SAP na platformie Azure. Wszystkie aspekty fakt, że część składniki SAP działają na platformie Azure powinny być przezroczyste dla użytkowników końcowych. Dlatego SAP transportu korekcji systemu (STMS), komunikacja RFC, drukowanie, zabezpieczeń (takich jak logowanie Jednokrotne), itp. współpracują bezproblemowo systemów SAP na platformie Azure. Ale scenariusza obejmującego również w tym artykule opisano scenariusz, w którym działa pełne środowisko SAP na platformie Azure z domeną klienta i DNS rozszerzony na platformę Azure.

> [!NOTE]
> Jest to scenariusz wdrażania, który jest obsługiwany w przypadku uruchamiania systemów SAP produktywności.
>
>

Odczyt [w tym artykule] [ vpn-gateway-create-site-to-site-rm-powershell] Aby uzyskać więcej informacji na temat sposobu łączenia sieci lokalnej w systemie Microsoft Azure

> [!IMPORTANT]
> Gdy obejmuje następujące informacje o scenariuszach obejmujących wiele lokalizacji między wdrożeniami klienta platformy Azure i w środowisku lokalnym, czekamy na poziomie szczegółowości całego systemów SAP. Scenariusze, które są *nieobsługiwane* obejmujące scenariusze są:
>
> * Uruchamianie różnych warstw aplikacji SAP w różne metody wdrażania. Na przykład systemem DBMS warstwy w środowisku lokalnym, ale w warstwie aplikacji SAP w maszyny wirtualne wdrożone jako maszyny wirtualne platformy Azure lub na odwrót.
> * Niektóre składniki warstwy SAP na platformie Azure i niektóre w środowisku lokalnym. Na przykład podział wystąpień w warstwie aplikacji SAP między magazynami lokalnymi i maszynami wirtualnymi platformy Azure.
> * Rozkład maszyn wirtualnych uruchomionych wystąpień jednego systemu SAP w wielu regionach platformy Azure nie jest obsługiwane.
>
> Przyczyną tych ograniczeń jest konieczność sieć o małym opóźnieniu o wysokiej wydajności w ramach jednego systemu SAP, szczególnie między wystąpieniami aplikacji a warstwy system DBMS systemu SAP.
>
> Specjalne planowania systemów i regiony musi wystąpić podczas korzystania z wielu systemów SAP, które są wysoce zintegrowane. Upewnij się, że wdrażanie tych systemów, jak najbliżej siebie, aby zminimalizować opóźnienie sieci. Wysoce zintegrowanych systemów SAP należą do nich:
> * System SAP BW, odczytywanie danych z systemów SAP OLTP, takich jak ERP i CRM lub SRM; lub
> * SLT SAP on używany do replikacji danych między wieloma systemami SPA lub nawet pomiędzy firmami SAP i systemów innych niż SAP. lub
> * SAP S/4 nawiązanie połączenia z systemem SAP ERP; itp.


### <a name="supported-os-and-database-releases"></a>Obsługiwane systemu operacyjnego i wersji bazy danych
* Oprogramowanie serwerowe firmy Microsoft, obsługiwane w przypadku usług Azure Virtual Machine znajduje się w tym artykule: <https://support.microsoft.com/kb/2721672>.
* Obsługiwane wersje systemu operacyjnego, wersji systemu bazy danych obsługiwane w usługach maszyny wirtualnej platformy Azure w połączeniu z oprogramowaniem SAP są udokumentowane w artykule Uwaga SAP [1928533].
* Aplikacje SAP i wersje obsługiwane w usługach maszyny wirtualnej platformy Azure są udokumentowane w artykule Uwaga SAP [1928533].
* Do uruchamiania jako gość maszyn wirtualnych na platformie Azure dla scenariuszy SAP obsługiwane są tylko obrazy 64-bitowych. W rezultacie tylko 64-bitowych aplikacji SAP i bazy danych są obsługiwane.

## <a name="microsoft-azure-virtual-machine-services"></a>Usługi maszyny wirtualnej platformy Microsoft Azure
Platforma Microsoft Azure jest skali Internetu platforma usług w chmurze hostowanej i działania w centrach danych firmy Microsoft. Ta platforma obejmuje usług Microsoft Azure Virtual Machine (infrastruktury jako usługi lub IaaS) oraz zestaw zaawansowanych Platform jako możliwości usługi (PaaS).

Platforma Azure ogranicza potrzebę technologii ponoszonych z góry i zakupów infrastruktury. Upraszcza ona obsługi i działania aplikacji, zapewniając zasoby obliczeniowe na żądanie i Magazyn, hostowanie i skalowanie oraz zarządzać nimi, połączonych aplikacji i aplikacji sieci web. Zarządzanie infrastrukturą jest automatyczne przy użyciu platformy, które zaprojektowano z myślą o wysokiej dostępności i dynamiczne skalowanie odpowiednio do potrzeb użycie z opcją zgodnie z rzeczywistym użyciem modelu cenowego.

![Pozycjonowanie usługi maszyny wirtualnej platformy Microsoft Azure][planning-guide-figure-400]

Za pomocą usług maszyny wirtualnej platformy Azure Microsoft jest pozwala wdrażać obrazy niestandardowe server na platformie Azure jako wystąpienia IaaS (zobacz rysunek 4). Maszyny wirtualne na platformie Azure są oparte na funkcji Hyper-V wirtualnych dysków twardych (VHD) i są w stanie uruchamiania różnych systemów operacyjnych jako systemu operacyjnego gościa.

Z perspektywy operacyjnej do platformy Azure usługi Virtual Machines oferuje podobnego środowiska jako maszyny wirtualne wdrożone w środowisku lokalnym. Jednak jest znaczącą korzyścią, który nie jest potrzebny do nabywania, administrowania i zarządzania infrastrukturą. Deweloperzy i Administratorzy mają pełną kontrolę nad obrazu systemu operacyjnego w ramach tych maszyn wirtualnych. Administratorzy mogą Zaloguj zdalnie tych maszyn wirtualnych przeprowadzić konserwację i rozwiązywanie problemów z zadaniami, a także zadania związane z wdrażaniem oprogramowania. W odniesieniu do wdrożenia tylko ograniczenia są rozmiary i możliwości maszyn wirtualnych platformy Azure. Te rozmiary może nie być poprawnie jako szczegółowe w konfiguracji, może być wykonana w środowisku lokalnym. Istnieje wiele typów maszyn wirtualnych, które stanową kombinację:

* Liczba procesorów wirtualnych
* Memory (Pamięć)
* Liczba wirtualnych dysków twardych, które mogą być dołączone
* Przepustowości sieci i magazynu

Rozmiar i ograniczenia dotyczące poszczególnych rozmiarów różnych maszyn wirtualnych oferowane są widoczne w tabeli w [w tym artykule (Linux)] [ virtual-machines-sizes-linux] i [w tym artykule (Windows)] [virtual-machines-sizes-windows].

Nie wszystkie innej serii maszyny Wirtualnej może być oferowana na każdym z regionów platformy Azure (w przypadku regionów platformy Azure zobacz następny rozdział). Również należy pamiętać, że nie wszystkie maszyny wirtualne lub maszyny Wirtualnej serii mają certyfikat dla rozwiązania SAP.

> [!IMPORTANT]
> Korzystanie z oprogramowania SAP NetWeaver na podstawie aplikacji, tylko podzbiór typy maszyn wirtualnych i konfiguracji na liście Uwaga SAP [1928533] są obsługiwane.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiony platformy Azure
Maszyny wirtualne są wdrażane w tak zwane *regionów platformy Azure*. Region świadczenia usługi Azure może być jednego lub wielu centrów danych, które znajdują się w pobliżu. W większości regionów geopolitycznych na całym świecie firma Microsoft ma co najmniej dwa regiony platformy Azure. Na przykład w Europie istnieje Region platformy Azure z *Europa Północna* i jeden z *Europa Zachodnia*. Takie dwa regiony platformy Azure w regionie geopolitycznym są oddzielone odległość tyle znaczące, tak, aby naturalnych i technicznych awarii nie wpływają na obu regionów platformy Azure, w tym samym regionie geopolitycznym. Od firmy Microsoft jest stale kompilowania nowe regiony platformy Azure w różnych regionach geopolitycznych globalnie, liczba tych regionów stale rośnie, a począwszy od grudnia 2015 możliwą liczbę 20 regionach platformy Azure za pomocą dodatkowych regionach ogłoszono już. Jako klient korzystający z wdrożeniem systemów SAP do wszystkich tych regionów, w tym dwóch regionów platformy Azure w Chinach. Bieżący aktualne informacje na temat regionów platformy Azure można zobaczyć w tej witrynie sieci Web: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Koncepcja maszyny wirtualnej platformy Microsoft Azure
Platforma Microsoft Azure oferuje infrastrukturę jako rozwiązanie usługi (IaaS) na host maszyny wirtualne z podobne funkcje jako rozwiązania do wirtualizacji w środowisku lokalnym. Masz możliwość tworzenia maszyn wirtualnych w witrynie Azure portal, programu PowerShell lub interfejsu wiersza polecenia, które oferują także wdrożenia i możliwości zarządzania.

Usługa Azure Resource Manager pozwala inicjować obsługę aplikacji za pomocą deklaratywnych szablonów. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Używasz tego samego szablonu można wielokrotnie wdrażać aplikację w każdym etapie cyklu życia aplikacji.

Więcej informacji na temat przy użyciu szablonów usługi Resource Manager można znaleźć tutaj:

* [Wdrażanie i zarządzanie maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure](../../linux/create-ssh-secured-vm-from-template.md)
* [Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Kolejną funkcją interesujące jest możliwość tworzenia obrazów z maszyn wirtualnych, dzięki czemu można przygotować niektóre repozytoria, z których jesteś w stanie szybko wdrożyć wystąpień maszyn wirtualnych, które spełniają Twoje wymagania.

Więcej informacji na temat tworzenia obrazów z maszyn wirtualnych można znaleźć w [w tym artykule (Linux)] [ virtual-machines-linux-capture-image-resource-manager] i [w tym artykule (Windows)] [ virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domeny błędów
Domeny błędów reprezentują fizyczną jednostkę awarii, ściśle powiązane z infrastruktury fizycznej, znajdujących się w centrach danych, a choć bloku fizycznego lub stojaka, można traktować domeny błędów, nie istnieje żadne bezpośrednie mapowanie jeden do jednego między tymi dwoma.

Podczas wdrażania wielu maszyn wirtualnych w ramach jednego systemu SAP w usług Microsoft Azure Virtual Machine, mogą mieć wpływ na kontroler sieci szkieletowej platformy Azure, aby wdrożyć aplikację w różnych domenach błędów, w tym samym wymaganiom Umowa SLA na platformie Microsoft Azure. Dystrybucja domeny awarii jednostki skalowania Azure (kolekcja setek węzłów obliczeniowych lub węzły magazynu i sieci) lub przydziału maszyn wirtualnych do określonej domeny błędów jest jednak coś nad tym, którzy nie masz bezpośrednią kontrolę. Aby skierować Kontroler sieci szkieletowej platformy Azure do wdrożenia zestawu maszyn wirtualnych w różnych domenach błędów, należy przypisać zestawu dostępności platformy Azure do maszyn wirtualnych w czasie wdrażania. Aby uzyskać więcej informacji na temat zestawów dostępności platformy Azure, zobacz rozdział [zestawami dostępności Azure] [ planning-guide-3.2.3] w tym dokumencie.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Uaktualnij domeny
Domen uaktualnienia reprezentują jednostki logicznej, która pomaga w określeniu, jak jest aktualizowany na maszynie Wirtualnej w systemie SAP, składający się z wystąpieniami platformy SAP działające w wielu maszyn wirtualnych. W przypadku uaktualniania Microsoft Azure przechodzi przez proces aktualizowania te domeny uaktualnienia pojedynczo. Dzięki rozmieszczeniu maszyn wirtualnych w czasie wdrażania za pośrednictwem różnych domen uaktualnienia, można chronić systemu SAP częściowo z potencjalny Przestój. Aby wymusić platformy Azure, aby wdrożyć maszyny wirtualne z systemu SAP rozprzestrzeniać się różnymi domenami uaktualnienia, musisz ustawić określony atrybut w czasie wdrażania każdej maszyny wirtualnej. Podobnie jak w domenach błędów, jednostki skalowania Azure jest podzielony na wiele domen uaktualnienia. Aby skierować Kontroler sieci szkieletowej platformy Azure do wdrażania zestawu maszyn wirtualnych za pośrednictwem różnych domen uaktualnienia, należy przypisać zestawu dostępności platformy Azure do maszyn wirtualnych w czasie wdrażania. Aby uzyskać więcej informacji na temat zestawów dostępności platformy Azure, zobacz rozdział [zestawami dostępności Azure] [ planning-guide-3.2.3] poniżej.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Zestawy dostępności platformy Azure
Maszyny wirtualne platformy Azure w ramach jednego zestawu dostępności platformy Azure są dystrybuowane przez kontroler sieci szkieletowej platformy Azure za pośrednictwem różnych domenach błędów i domenach uaktualniania. Dystrybucji za pośrednictwem różnych domenach błędów i uaktualnienia ma na celu uniemożliwić zamykany konieczność konserwacji infrastruktury lub awaria w jednej domenie błędów w przypadku wszystkich maszyn wirtualnych z systemem SAP. Domyślnie maszyny wirtualne nie są częścią zestawu dostępności. Udział maszyny Wirtualnej w zestawie dostępności jest zdefiniowany w czasie wdrażania lub później na zmianę konfiguracji i ponowne wdrażanie maszyny wirtualnej.

Aby zrozumieć pojęcia zestawy dostępności platformy Azure oraz sposób zestawy dostępności odnoszą się do błędów i domenach uaktualniania, przeczytaj [w tym artykule][virtual-machines-manage-availability]

Aby zdefiniować zestawy dostępności dla usługi Azure Resource Manager za pomocą szablonu json, zobacz [specyfikacji interfejsu api rest](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) i poszukaj pozycji "availability".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Miejsca do magazynowania: Microsoft Azure Storage i dysków z danymi
Microsoft Azure Virtual Machines korzystanie z różnych typach pamięci masowej. Podczas implementowania SAP w usługach maszyny wirtualnej platformy Azure, jest ważne zrozumieć różnice między tymi dwoma typami głównego magazynu:

* -Trwałe i nietrwałe magazyn.
* Z magazynu trwałego.

Maszyny wirtualne platformy Azure oferują dyski trwałe, po wdrożeniu maszyny Wirtualnej. W przypadku ponownego uruchomienia maszyny Wirtualnej zostaną wyczyszczone całą zawartość na tych dyskach. To dlatego, biorąc pod uwagę, że pliki danych i pliki dziennika/ponów baz danych powinien w żadnym wypadku nie znajdować się na tych dyskach nietrwałe. Mogą wystąpić wyjątki dotyczące niektórych baz danych, gdzie te dyski nieutrwaloną może być odpowiednie dla bazy danych tempdb i obszary tabel tymczasowych. Należy jednak unikać przy użyciu tych dysków dla maszyn wirtualnych z serii A, ponieważ te dyski nieutrwaloną jest ograniczona przepływność z tej rodziny maszyn wirtualnych. Aby uzyskać więcej informacji, przeczytaj artykuł [opis dysku tymczasowego na maszynach wirtualnych z Windows na platformie Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> Dysku D:\ w Maszynie wirtualnej platformy Azure jest nieutrwaloną dysku, która jest wspierana przez niektóre dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną, oznacza to, że wszelkie zmiany wprowadzone do zawartości na dysku D:\, zostaną utracone, gdy maszyna wirtualna jest uruchamiany ponownie. Przez "wszelkie zmiany", takich jak pliki przechowywane, katalogi utworzone, zainstalowane, aplikacje itd.
> 
> ![Linux][Logo_Linux] Linux
> 
> Maszyny wirtualne systemu Linux platformy Azure automatycznie zainstalować dysk w /mnt/resource, który jest dyskiem nietrwałych wspierana przez dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną, oznacza to, wszelkie zmiany wprowadzone do zawartości w /mnt/resource zostaną utracone, gdy maszyna wirtualna jest uruchamiany ponownie. Za wszelkie zmiany, takie jak pliki przechowywane, katalogi utworzone, zainstalowane, aplikacje itd.
> 
> 

---

Microsoft Azure Storage zapewnia magazynu trwałego i typowe poziomy ochrony i nadmiarowości na magazyn sieci SAN. Dyski oparte na usłudze Azure Storage to wirtualny dysk twardy (VHD) znajduje się w usłudze Azure Storage Services. System operacyjny — dysk lokalny (Windows C:\, Linux/dev/sda1) są przechowywane w usłudze Azure Storage i dodatkowych woluminów dyski/zainstalowanego na maszynie wirtualnej Pobierz tam przechowywane, zbyt.

Istnieje możliwość przekazania istniejącego wirtualnego dysku twardego ze środowiska lokalnego lub Utwórz pusty, które były w obrębie platformy Azure i dołączać do tych, które mają wdrożone maszyny wirtualne.

Po utworzeniu lub przekazywanie wirtualnego dysku twardego do usługi Azure Storage, możliwe jest, aby zainstalować i dołączyć te do istniejącej maszyny wirtualnej i kopiowania istniejącego wirtualnego dysku twardego (odinstalowane).

Zgodnie z tych wirtualnych dysków twardych są zachowywane, danych i zmiany w nich są bezpieczne, po ponownym uruchomieniu i ponowne utworzenie wystąpienia maszyny wirtualnej. Nawet, jeśli wystąpienie zostanie usunięte, tych wirtualnych dysków twardych bezpieczeństwo i może być ponownie wdrażana lub w przypadku dysków bez systemu operacyjnego mogą być instalowane do innych maszyn wirtualnych.

Więcej informacji na temat usługi Azure Storage można znaleźć tutaj:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Usługi Azure Standard storage był typ miejsca do magazynowania IaaS platformy Azure został wydany. Wystąpiły operacje We/Wy limitami na jednym dysku. Opóźnienie reprezentatywne nie jest w tej samej klasy jako urządzenia SAN/NAS zazwyczaj wdrożone dla profesjonalnych systemów SAP hostowanej w środowisku lokalnym. Niemniej jednak usługi Azure Standard Storage okazały się wystarczająca dla wielu setek systemów SAP w międzyczasie wdrożonych na platformie Azure.

Dyski, które są przechowywane na kontach usługi Azure Standard Storage są naliczane w oparciu o rzeczywiste dane, które są przechowywane, ilości transakcji magazynu, transferów danych wychodzących i wybrany wybranej opcji nadmiarowości. Można utworzyć wiele dysków o maksymalnej 1TB, rozmiar, ale tak długo, jak te pozostaną puste nie są pobierane opłaty. Jeśli następnie wypełnić jeden wirtualny dysk twardy o 100GB, opłata jest naliczana dla przechowywania 100GB i nie nominalny, które utworzono za pomocą wirtualnego dysku twardego.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Usługi Azure Premium Storage
Usługa Azure Premium Storage stało się wprowadzone w celu zapewnienie:

* Lepsze opóźnienie operacji We/Wy.
* Większa przepustowość.
* Mniejszą zmienność opóźnień we/wy.

W tym celu wprowadzono wiele zmian, których są dwie najbardziej znaczące:

* Użycie dysków SSD w węzłach usługi Azure Storage
* Nowej pamięci podręcznej odczytu, która jest wspierana przez lokalny dysk SSD w węźle obliczeniowym platformy Azure

W przeciwnym do której nie zmieniono możliwości magazynu w warstwie standardowa zależy od rozmiaru dysku (lub wirtualnego dysku twardego), Magazyn w warstwie Premium obecnie ma trzy kategorie inny dysk, które są wyświetlane w tym artykule: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Zobaczysz, że przepustowość operacji We/Wy/dysku i/dysk są zależne od kategorii rozmiaru dysków

Podstawa kosztu w przypadku usługi Premium Storage nie jest woluminem rzeczywistych danych przechowywanych w tych dysków, ale kategorii rozmiar takich dysku, niezależnie od ilości danych, które są przechowywane w usłudze dysk.

Można również tworzyć dysków w magazynie Premium Storage, które nie są bezpośrednie mapowanie na kategorie rozmiar wyświetlane. Tak, może to być szczególnie w przypadku kopiowania dysków z magazynu Standard Storage do usługi Premium Storage. W takich przypadkach odbywa się mapowanie do następnej największy opcji dysku usługi Premium Storage.

Większość rodzin maszyn wirtualnych platformy Azure, certyfikat z oprogramowaniem SAP będą mogli pracować z magazynu w warstwie Premium i/lub mieszankę między platformą Azure, standardowa i Premium Storage.

Jeśli są wyewidencjonowywanie część maszyny wirtualne serii DS, w [w tym artykule (Linux)] [ virtual-machines-sizes-linux] i [w tym artykule (Windows)][virtual-machines-sizes-windows], możesz należy pamiętać, że Istnieją pewne ograniczenia woluminu danych do dysków usługi Premium Storage na stopień szczegółowości poziomu maszyny Wirtualnej. Różnych serii DS lub GS-series VMs również mieć różne ograniczenia względem liczby dysków danych, które mogą być instalowane. Te limity są opisane w artykule, o których wspomniano powyżej, jak również. Jednak w zasadzie oznacza to, że jeśli, na przykład zainstalować 32 dyski x P30 z jedną maszyną wirtualną DS14 nie uzyskasz 32 x maksymalna przepływność dysków P30. Zamiast tego maksymalna przepływność na poziomie maszyny Wirtualnej, zgodnie z opisem w artykule ogranicza przepływność danych.

Więcej informacji na temat usługi Premium Storage można znaleźć tutaj: <https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

Podczas wdrażania usług lub maszynami wirtualnymi na platformie Azure, można organizować wdrożenia wirtualne dyski twarde i obrazy maszyn wirtualnych w jednostce o nazwie kont usługi Azure Storage. Podczas planowania wdrożenia usługi Azure, należy dokładnie rozważyć ograniczenia platformy Azure. Po jednej stronie istnieje ograniczona liczba kont magazynu na subskrypcję platformy Azure. Mimo że każde konto magazynu platformy Azure może zawierać dużą liczbę plików wirtualnego dysku twardego, na łączna liczba operacji We/Wy na konto magazynu jest stały limit. W przypadku wdrażania setek SAP maszyn wirtualnych z systemami DBMS tworzenia znaczące wywołań operacji We/Wy, zalecane jest rozpraszanie wysokiej maszyn wirtualnych systemu DBMS na operacje We/Wy między wieloma kontami magazynu Azure. Należy uważać, aby nie przekroczyć bieżący limit kont usługi Azure Storage na subskrypcję. Ponieważ magazynu jest to ważna część wdrożenia bazy danych z systemem SAP, to pojęcie omówiono bardziej szczegółowo w już występujących w odwołaniu [przewodnik wdrażania systemu DBMS][dbms-guide].

Więcej informacji o kontach magazynu Azure znajdują się w [w tym artykule][storage-scalability-targets]. Przeczytaniu tego artykułu, możesz należy pamiętać, że istnieją różnice w ograniczenia kont usługi Azure Standard Storage i kont usługi Premium Storage. Najważniejsze różnice są ilości danych, które mogą być przechowywane w ramach konta magazynu. W magazynie Standard Storage wolumin jest większy niż dzięki usłudze Premium Storage powstanie. Na tej stronie, standardowe konto magazynu jest poważnie ograniczony operacje We/Wy (zobacz kolumnę **całkowita liczba żądań**), a konto usługi Azure Premium Storage jest takie ograniczenie nie. Omówimy szczegóły i wyniki tych różnic Omawiając wdrożeń systemów SAP, zwłaszcza na serwerach systemu DBMS.

W ramach konta magazynu masz możliwość tworzenia różnych kontenerów na potrzeby organizowania i kategoryzowania różnych wirtualnych dysków twardych. Te kontenery są używane do, na przykład osobne wirtualne dyski twarde różnych maszyn wirtualnych. Nie ma wpływu na wydajność przy użyciu kontenera tylko jeden lub wiele kontenerów poniżej jednego konta usługi Azure Storage.

W ramach platformy Azure Nazwa wirtualnego dysku twardego następujące następujące połączenia nazewnictwa, które trzeba podać unikatową nazwę dysku VHD w obrębie platformy Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Ciąg, który powyżej musi jednoznacznie zidentyfikować wirtualnego dysku twardego, który jest przechowywany w usłudze Azure Storage.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

Dyski zarządzane są nowego typu zasobu w usłudze Azure Resource Manager używany zamiast wirtualnych dysków twardych, które są przechowywane na kontach magazynu Azure. Dyski zarządzane automatycznie wyrównane z zestawu dostępności maszyny wirtualnej, które są dołączone i w związku z tym zwiększyć dostępność maszyny wirtualnej i usługi, które są uruchomione na maszynie wirtualnej. Aby uzyskać więcej informacji, przeczytaj [artykuł z omówieniem](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Zaleca się używanie dysków zarządzanych, ponieważ mogą uprościć wdrażanie i zarządzanie maszynami wirtualnymi.
SAP aktualnie obsługuje tylko dyski zarządzane w warstwie Premium. Aby uzyskać więcej informacji, przeczytaj Uwaga SAP [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Odporność usługi Microsoft Azure Storage

Usługa Microsoft Azure Storage przechowuje podstawowy dysk VHD (z systemem operacyjnym) i dołączonych dysków lub obiektów blob na co najmniej trzech oddzielnych węzłów do magazynowania. Fakt ten nosi nazwę lokalny magazyn nadmiarowy (LRS). Magazyn LRS jest domyślnie dla wszystkich typów magazynu na platformie Azure. 

Dostępnych jest kilka więcej nadmiarowości metod, które są wszystkie opisane w artykule [replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Począwszy od usługi Azure Premium Storage, która jest zalecany typ magazynu dla systemu DBMS na maszynach wirtualnych i dysków, w których są przechowywane pliki bazy danych i dziennika/ponawiania, dostępny tylko metody to LRS. W rezultacie musisz skonfigurować metody bazy danych, takich jak SQL Server Always On, środowiska Oracle Data Guard i replikacji systemu HANA do włączenia replikacji danych bazy danych do innego regionu platformy Azure lub do innej strefy dostępności platformy Azure.


> [!NOTE]
> W przypadku wdrożeń systemu DBMS użycie magazyn geograficznie nadmiarowy jako dostępny za pomocą usługi Azure Standard Storage nie jest zalecane, ponieważ ma wpływ na zmniejszenie wydajności, a nie przestrzegał kolejności zapisu w różnych wirtualnych dysków twardych, które są dołączone do maszyny Wirtualnej. Fakt nie zapewniane kolejności zapisu w różnych wirtualnych dysków twardych ponosi duże ryzyko do znajdą się w niespójnym baz danych na stronie docelowej replikacji, jeśli pliki bazy danych i dziennika/ponawiania zostały rozmieszczone w obrębie wielu dysków VHD (jako przede wszystkim przypadek) na maszynę Wirtualną po stronie "source".


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Sieci platformy Microsoft Azure

Microsoft Azure udostępnia infrastrukturę sieciową, która umożliwia mapowanie wszystkich scenariuszy, które chcemy weź pod uwagę przy użyciu oprogramowania SAP. Dostępne są następujące możliwości:

* Dostęp z zewnątrz, bezpośrednio do maszyn wirtualnych za pomocą usług terminalowych Windows lub protokołu ssh/VNC
* Dostęp do usług i określone porty używane przez aplikacje w ramach maszyn wirtualnych
* Wewnętrznej komunikacji i rozpoznawanie nazw między grupą maszyny wirtualne wdrożone jako maszyny wirtualne platformy Azure
* Łączność między wieloma lokalizacjami między klienta między siecią lokalną i siecią platformy Azure
* Krzyżowe regionu platformy Azure lub łączności centrum danych między lokacjami platformy Azure

Więcej informacji można znaleźć tutaj: <https://azure.microsoft.com/documentation/services/virtual-network/>

Istnieje wiele różnych możliwości, aby skonfigurować nazwę i rozpoznawania adresu IP na platformie Azure. Istnieje również usługa Azure DNS, który może być używany zamiast konfigurowania serwera DNS. Więcej informacji można znaleźć w [w tym artykule] [ virtual-networks-manage-dns-in-vnet] i [tę stronę](https://azure.microsoft.com/services/dns/).

Między środowiskami lokalnymi lub hybrydowe scenariusze, firma Microsoft korzysta z faktu, lokalne DNS-AD/OpenLDAP został rozszerzony za pośrednictwem połączenia VPN lub prywatnego na platformie Azure. W przypadku niektórych scenariuszy, zgodnie z opisem w tym miejscu, może być konieczne replikę AD/OpenLDAP zainstalowane w systemie Azure.

Ponieważ sieć i rozpoznawanie nazw to ważna część wdrożenia bazy danych z systemem SAP, to pojęcie jest omówiona bardziej szczegółowo w [przewodnik wdrażania systemu DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

Tworząc się z siecią wirtualną platformy Azure, można zdefiniować zakres adresów prywatnych adresów IP przydzielony przez funkcje Azure DHCP. W scenariuszach obejmujących zakres adresów IP, które są zdefiniowane wciąż jest przydzielony przy użyciu protokołu DHCP przez platformę Azure. Jednak rozpoznawanie nazw domeny jest wykonywane w środowisku lokalnym (przy założeniu, że maszyny wirtualne są częścią domeny w środowisku lokalnym) i dlatego może rozpoznać adresów poza różnych usługach w chmurze platformy Azure.

Do każdej maszyny wirtualnej na platformie Azure musi być podłączony do sieci wirtualnej.

Więcej szczegółów można znaleźć w [w tym artykule] [ resource-groups-networking] i [tę stronę](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Domyślnie po wdrożeniu maszyny Wirtualnej nie można zmienić konfiguracji sieci wirtualnej. Ustawienia TCP/IP musi pozostać do serwera DHCP na platformie Azure. Domyślnym zachowaniem jest przypisanie dynamicznego adresu IP.
>
>

Adres MAC karty sieci wirtualnej mogą ulec zmianie, na przykład po zmieniania rozmiaru i gościa Windows lub Linux, systemu operacyjnego przejmuje nowej karty sieciowej i automatycznie używa protokołu DHCP do przypisywania adresów IP i DNS w tym przypadku.

##### <a name="static-ip-assignment"></a>Przypisania statycznego adresu IP
Istnieje możliwość przypisać stałe lub zastrzeżone adresy IP do maszyn wirtualnych w ramach usługi Azure Virtual Network. Uruchamianie maszyn wirtualnych w usłudze Azure Virtual Network otwiera doskonałe możliwości można korzystać z tej funkcji, czy potrzebne wymagane w niektórych scenariuszach. Przypisanie adresu IP pozostaje ważny przez cały istnienie maszyn wirtualnych, niezależnie od tego, czy maszyna wirtualna jest uruchomiona lub zamykania. W rezultacie należy wykonać ogólną liczbę maszyn wirtualnych (maszyny wirtualne uruchomione i zatrzymane) pod uwagę podczas definiowania zakresu adresów IP dla sieci wirtualnej. Adres IP pozostanie przydzielony do momentu usunięcia maszyny Wirtualnej i jej interfejsu sieciowego lub do momentu przypisany adres IP pobiera cofnąć ponownie. Aby uzyskać więcej informacji, przeczytaj [w tym artykule][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Należy przypisać statyczne adresy IP za pośrednictwem platformy Azure oznacza, że do poszczególnych kart sieciowych. Nie należy przypisywać statyczne adresy IP w ramach systemu operacyjnego gościa do wirtualnej karty sieciowej. Niektórych usług platformy Azure, takich jak usługa Kopia zapasowa Azure opierają się na fakcie, że w co najmniej podstawowego wirtualnej karty sieciowej jest ustawiony, DHCP, a nie statycznych adresów IP. Zobacz też dokumentu [kopii zapasowej maszyny wirtualnej Azure Rozwiązywanie problemów z](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Wiele kart sieciowych na maszynę Wirtualną

Można zdefiniować wiele kart sieci wirtualnej (vNIC) na maszynie wirtualnej platformy Azure. Dzięki możliwości mają wiele kart sieciowych, które można uruchomić, aby skonfigurować ruch sieciowy rozdzielenie w przypadku gdy, na przykład klient ruch jest kierowany przez jeden ruch w sieci wirtualnej karty sieciowej i wewnętrznej bazy danych odbywa się za pomocą drugiego wirtualnej karty sieciowej. Zależne od typu maszyny Wirtualnej są różne ograniczenia związane z liczbą kart sieciowych. Szczegółowymi informacjami na temat, funkcje i ograniczenia dotyczące można znaleźć w następujących artykułach:

* [Tworzenie maszyny Wirtualnej z systemem Windows z wieloma kartami sieciowymi][virtual-networks-multiple-nics-windows]
* [Tworzenie maszyny Wirtualnej z systemem Linux z wieloma kartami sieciowymi][virtual-networks-multiple-nics-linux]
* [Wdrażanie maszyn wirtualnych kart Sieciowych multi przy użyciu szablonu][virtual-network-deploy-multinic-arm-template]
* [Wdrażanie maszyn wirtualnych kart Sieciowych multi przy użyciu programu PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Wdrażanie maszyn wirtualnych kart Sieciowych multi przy użyciu wiersza polecenia platformy Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Połączenie lokacja lokacja

Między lokalizacjami jest maszyn wirtualnych platformy Azure i lokalnymi powiązanym z przejrzystych i trwałe połączenie sieci VPN. Oczekuje się stać się Najczęstszym wzorcem wdrożenia SAP na platformie Azure. Zakłada się, że procedury operacyjne i procesów przy użyciu wystąpieniami platformy SAP na platformie Azure należy działają w sposób przezroczysty. To oznacza, że powinno być możliwe do drukowania z tych systemów, a także korzystanie z systemu zarządzania transportu (TMS) SAP, transportem zmieni się z systemem programowanie na platformie Azure na system testowy, który jest wdrożony w środowisku lokalnym. Więcej dokumentacji wokół lokacja lokacja można znaleźć w [w tym artykule][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Urządzenia z tunelu sieci VPN

Aby utworzyć połączenie lokacja lokacja (w środowisku lokalnym centrum danych do centrum danych platformy Azure), należy uzyskać i skonfigurować urządzenie sieci VPN albo użyj usługi Routing i dostęp zdalny (RRAS), która została wprowadzona jako składnika oprogramowania w systemie Windows Server 2012.

* [Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja przy użyciu programu PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informacje o urządzeniach sieci VPN dla połączeń bramy VPN typu lokacja-lokacja][vpn-gateway-about-vpn-devices]
* [Brama sieci VPN — często zadawane pytania][vpn-gateway-vpn-faq]

![Połączenie lokacja lokacja między lokalną i platformą Azure][planning-guide-figure-600]

Powyższy rysunek przedstawia, że obie subskrypcje platformy Azure mają podzakresów adres IP zarezerwowany do użycia w sieciach wirtualnych na platformie Azure. Łączność z siecią lokalną na platformę Azure zostanie nawiązane za pośrednictwem sieci VPN.

#### <a name="point-to-site-vpn"></a>Sieci VPN typu punkt lokacja

Sieci VPN typu punkt lokacja wymaga do każdej maszyny klienta do łączenia z własnej sieci VPN na platformie Azure. W przypadku scenariuszy SAP czekamy na, połączenia punkt lokacja nie jest możliwe. W związku z tym otrzymują żadnych dalszych odwołań do połączeń sieci VPN punkt lokacja.

Więcej informacji można znaleźć tutaj
* [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Połączenia obejmujące wiele lokacji sieci VPN

Platforma Azure oferuje również dzisiaj możliwość tworzenia połączenia sieci VPN z wieloma lokacjami dla jednej subskrypcji platformy Azure. Wcześniej pojedynczej subskrypcji została ograniczona do jednego połączenia sieci VPN typu lokacja lokacja. To ograniczenie zostało usunięte z połączenia obejmujące wiele lokacji sieci VPN dla jednej subskrypcji. Umożliwia korzystanie z więcej niż jeden Region platformy Azure dla określonej subskrypcji za pomocą konfiguracji między środowiskami lokalnymi.

Aby uzyskać więcej dokumentacji, zobacz [w tym artykule][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Połączenie między sieciami wirtualnymi

Przy użyciu połączenia obejmujące wiele lokacji sieci VPN, należy skonfigurować oddzielne sieci wirtualnej platformy Azure we wszystkich regionach. Często jednak masz wymagania, które składniki oprogramowania w różnych regionach skontaktować się ze sobą. Najlepiej tej komunikacji powinny być kierowane z jednego regionu platformy Azure do środowiska lokalnego i z tego miejsca do innego regionu platformy Azure. Ze skrótem platforma Azure oferuje możliwość, aby skonfigurować połączenie z jednej sieci wirtualnej platformy Azure w jednym regionie do innej sieci wirtualnej platformy Azure hostowanej w innym regionie. Ta funkcja nosi nazwę połączenia sieć wirtualna-sieć wirtualna. Szczegółowe informacje na temat tej funkcji można znaleźć tutaj: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Prywatnego połączenia z usługą Azure ExpressRoute

Usługa Microsoft Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure i przez klienta na infrastrukturę lokalną lub działającą we wspólnej lokalizacji. Usługa ExpressRoute jest oferowany przez MPLS różnych dostawców sieci VPN (przełączaniem pakietów,) lub innych dostawców usług sieciowych. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Połączenia ExpressRoute zapewniają wyższy poziom zabezpieczeń, więcej niezawodność aplikacji przy użyciu wielu obwodów równoległych, większe szybkości i krótsze opóźnienia niż typowe połączenia przez za pośrednictwem Internetu.

Znajdź szczegółowe informacje na temat usługi Azure ExpressRoute i ofert poniżej:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Expressroute umożliwia wiele subskrypcji platformy Azure za pośrednictwem jednego obwodu usługi ExpressRoute, zgodnie z opisem w tym miejscu

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Wymuszone tunelowanie przypadku między środowiskami lokalnymi
W przypadku maszyn wirtualnych, przyłączanie do domen lokalnych za pośrednictwem lokacja lokacja, punkt lokacja lub ExpressRoute musisz upewnij się, że wdrożonych wprowadzenie ustawień internetowego serwera proxy dla wszystkich użytkowników w tych maszyn wirtualnych również. Domyślnie oprogramowanie działające w tych maszyn wirtualnych lub użytkownicy uzyskują dostęp do Internetu za pomocą przeglądarki nie przejdzie za pośrednictwem serwera proxy firmy, ale łączą się bezpośrednio za pośrednictwem platformy Azure, aby przez internet. Ale nawet ustawienia serwera proxy nie jest rozwiązaniem 100% do kierowania ruchu za pośrednictwem serwera proxy firmy, ponieważ jest odpowiedzialny za oprogramowanie i usługi, aby sprawdzić, czy serwer proxy. Jeśli oprogramowanie uruchomione na maszynie wirtualnej nie jest tą operacją, lub administrator zmienia ustawienia, ruch do Internetu mogą mógł zostać ominięty ponownie bezpośrednio za pomocą platformy Azure, aby przez Internet.

Aby uniknąć takich bezpośrednie połączenie z Internetem, można skonfigurować wymuszone tunelowanie z połączeniem lokacja lokacja między lokalną i platformą Azure. Szczegółowy opis funkcji tunelowania jest opublikowane w tym miejscu <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Wymuszone tunelowanie przy użyciu usługi ExpressRoute jest włączona, klienci anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Podsumowanie sieci platformy Azure

Ten rozdział zawiera wiele ważnych punktów o sieci platformy Azure. Poniżej przedstawiono podsumowanie głównych punktów:

* Sieci wirtualne platformy Azure umożliwiają umieść struktury sieci wdrożenie systemu Azure. Sieci wirtualne mogą być odizolowane od siebie wzajemnie lub za pomocą sieciowych grup zabezpieczeń ruchu między sieciami wirtualnymi można sterować.
* Sieci wirtualne platformy Azure mogą być wykorzystywane do przypisywania zakresów adresów IP do maszyn wirtualnych lub przypisanie stałych adresów IP do maszyn wirtualnych
* Aby skonfigurować połączenie lokacja-lokacja lub punkt-lokacja należy najpierw utworzyć sieć wirtualną platformy Azure
* Po wdrożeniu maszyny wirtualnej nie jest już można zmienić sieci wirtualnej przypisany do maszyny Wirtualnej

### <a name="quotas-in-azure-virtual-machine-services"></a>Przydziały w usługach maszyny wirtualnej platformy Azure
Musimy być niejasne o tym, że infrastruktura sieci i magazynu jest współużytkowana przez maszyny wirtualne z różnych usług w infrastrukturze platformy Azure. I podobnie jak w przypadku centrów danych przez klienta lub przerostu niektórych zasobów infrastruktury odbywać stopniu. Platforma Microsoft Azure używa dysku, Procesora, sieci i inne limity przydziału, aby ograniczyć zużycie zasobów i zachować spójną i jednoznaczna wydajności.  Różne typy maszyn wirtualnych (A5, A6 itp.) mają różne limity przydziału dla liczby dysków, Procesora, pamięci RAM i sieci.

> [!NOTE]
> Zasoby Procesora i pamięci typów maszyn wirtualnych obsługiwanych przez oprogramowanie SAP są wstępnie przydzielić w węzłach hosta. Oznacza to, że po wdrożeniu maszyny Wirtualnej na hoście dostępnych zasobów zgodnie z definicją typu maszyny Wirtualnej.
>
>

Podczas planowania i zmiany rozmiaru SAP rozwiązań platformy Azure, należy uwzględnić limity przydziału dla każdego rozmiaru maszyny wirtualnej. Limity przydziału maszyny Wirtualnej są opisane [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Limity przydziału opisane reprezentują teoretycznych wartości maksymalnej.  Limit operacji We/Wy na dysku można osiągnąć za pomocą małych dla systemu IOs (8kb), ale prawdopodobnie nie można osiągnąć przy użyciu dużych dla systemu IOs (1Mb).  Limit operacji We/Wy są wymuszane na stopień szczegółowości jednego dysku.

Jako drzewo decyzyjne nierównej zdecydować, czy system SAP dopasowuje się do usług maszyny wirtualnej platformy Azure i jej możliwości lub tego, czy istniejący system musi być skonfigurowana w różny sposób w celu wdrożenia systemu Azure mogą być używane drzewa decyzyjnego poniżej:

![Drzewo decyzyjne do określania możliwości wdrażania oprogramowania SAP na platformie Azure][planning-guide-figure-700]

**Krok 1**: Najważniejsze informacje się rozpoczynać wymaganie protokoły SAP dla danego systemu SAP. Wymagania dotyczące punktu SAP konieczne można podzielić na systemu DBMS i część aplikacji SAP, nawet jeśli SAP system jest już wdrożony lokalnie w konfiguracji warstwy 2. Istniejących systemów punktu SAP związanym ze sprzętem używany często można określić lub w oparciu o istniejące testy porównawcze SAP. Wyniki można znaleźć tutaj: <https://sap.com/about/benchmark.html>.
Nowo wdrożonych systemów SAP powinien wykonano już instrukcje ćwiczeniu zmiany rozmiaru należy określić wymagania dotyczące punktu SAP systemu.
Zobacz też ten blog i dołączony dokument w przypadku ustalania rozmiaru SAP na platformie Azure: <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Krok 2**: Dla istniejących systemów liczby operacji We/Wy i operacje We/Wy na sekundę na serwerze systemu DBMS powinno być mierzone. Nowo planowane systemów nowego systemu w tym ćwiczeniu rozmiaru również powinien zapewnić nierównej pomysły wymagań dotyczących operacji We/Wy systemu DBMS na stronie. Jeśli nie wiesz, trzeba będzie do przeprowadzenia weryfikacji koncepcji.

**Krok 3**: Porównaj wymaganie protokoły SAP DBMS serwera przy użyciu różnych typów maszyn wirtualnych platformy Azure może zapewnić punktu SAP. Informacje na temat punktu SAP różnych typów maszyn wirtualnych platformy Azure jest udokumentowany w Uwaga SAP [1928533]. Należy się skoncentrować na maszynie Wirtualnej z systemem DBMS najpierw warstwę bazy danych jest warstwy w systemie SAP NetWeaver, która nie skalowanie w poziomie w większości wdrożeń. Z kolei może być skalowana w warstwie aplikacji SAP. Jeśli jest obsługiwany przez żaden z SAP typy maszyn wirtualnych platformy Azure może zapewnić wymagany protokoły SAP, obciążenie planowane systemu SAP nie można uruchomić na platformie Azure. Należy albo wdrożenia systemu lokalnego lub zachodzi potrzeba zmiany woluminu obciążenia systemu.

**Krok 4**: Zgodnie z opisem [(Linux) w tym miejscu] [ virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows], Azure wymusza przydziału operacji We/Wy na dysk niezależnie od, czy są używane standardowe Magazyn lub magazyn w warstwie Premium. Zależne od typu maszyny Wirtualnej, różni się liczba dysków danych, które mogą być instalowane. W rezultacie można obliczyć maksymalną liczbę operacji We/Wy, który można osiągnąć przy użyciu różnych typów maszyn wirtualnych. Zależne od układu plików bazy danych, można stripe dysków, aby stać się jeden wolumin w systemie operacyjnym gościa. Jednak jeśli bieżące operacje We/Wy ilości wdrożony system SAP przekroczy limity obliczeniowe największych typu maszyny Wirtualnej platformy Azure i czy istnieje możliwość kompensacji z większą ilością pamięci, obciążeń systemu SAP mogą wpływać na poważnie. W takich przypadkach zostanie trafiony punkt, w którym nie należy wdrażać system na platformie Azure.

**Krok 5**: Szczególnie w przypadku systemów SAP, które są wdrożone lokalnie w konfiguracji warstwy 2, jest szansa, system może być konieczne można skonfigurować na platformie Azure w ramach konfiguracji 3-warstwowej. W tym kroku należy sprawdzić, czy jest składnikiem w warstwie aplikacji SAP, który nie może być skalowana w poziomie i który nie pasuje do zasobów Procesora i pamięci, oferowanych przez różnych typów maszyn wirtualnych platformy Azure. Jeśli rzeczywiście występuje takiego składnika, nie można wdrożyć jej obciążenie i SAP system na platformie Azure. Ale jeśli składniki aplikacji SAP można skalować do wielu maszyn wirtualnych platformy Azure, system można wdrożyć na platformie Azure.

**Krok 6**: Jeśli system DBMS i SAP składniki warstwy aplikacji, można uruchomić w maszynach wirtualnych platformy Azure, konfiguracji musi być zdefiniowany w odniesieniu do:

* Liczba maszyn wirtualnych platformy Azure
* Typy maszyn wirtualnych dla poszczególnych składników
* Liczba dysków VHD systemu DBMS na maszynie wirtualnej w celu zapewnienia wystarczającej liczby operacji We/Wy

## <a name="managing-azure-assets"></a>Zarządzanie zasobami platformy Azure

### <a name="azure-portal"></a>Azure Portal

Azure portal to jeden z trzech interfejsów zarządzania wdrożeniami maszyn wirtualnych platformy Azure. Podstawowe zadania zarządzania, takich jak wdrażanie maszyn wirtualnych z obrazów, może odbywać się za pośrednictwem witryny Azure portal. Ponadto Tworzenie konta magazynu, sieci wirtualnych i innymi składnikami platformy Azure są również zadania, które witryny Azure portal może również obsługiwać. Jednak funkcji, takich jak przekazywanie wirtualne dyski twarde ze środowiska lokalnego do platformy Azure lub kopiowania wirtualnego dysku twardego w ramach platformy Azure są zadania, które wymagają narzędzi innych firm lub administracyjnej za pomocą programu PowerShell lub interfejsu wiersza polecenia.

![Portal Microsoft Azure — omówienie maszyny wirtualnej][planning-guide-figure-800]


Zadań administracyjnych i konfiguracji dla wystąpienia maszyny wirtualnej są możliwe w witrynie Azure Portal.

Oprócz ponowne uruchamianie i zamykanie maszyny wirtualnej można również dołączyć, odłączyć i tworzyć dyski danych dla tego wystąpienia maszyny wirtualnej do przechwytywania wystąpienia obrazu przygotowania i skonfiguruj rozmiar wystąpienia maszyny wirtualnej.

Azure portal udostępnia podstawowe funkcje wdrażania i konfigurowania maszyn wirtualnych i wiele innych usług platformy Azure. Jednak nie wszystkie dostępne funkcje jest ujęty w witrynie Azure portal. W witrynie Azure portal nie jest możliwe do wykonania zadania, takie jak:

* Przekazywanie wirtualnych dysków twardych na platformie Azure
* Kopiowanie maszyn wirtualnych


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Zarządzanie za pomocą poleceń cmdlet programu PowerShell Azure firmy Microsoft

Windows PowerShell to wydajna i rozszerzalna struktura, która ma powszechnie zaakceptowany przez klientów wdrażających większą liczbą systemów na platformie Azure. Po zakończeniu instalacji poleceń cmdlet programu PowerShell na komputerze stacjonarnym, laptop lub dedykowane stacji można zdalnie uruchamiać polecenia cmdlet programu PowerShell.

Proces w celu włączenia pulpitu/laptopie lokalnym użycia poleceń cmdlet programu Azure PowerShell i sposobie konfigurowania tych, do użycia z subskrypcji platformy Azure jest opisana w [w tym artykule][powershell-install-configure].

Bardziej szczegółowe instrukcje dotyczące sposobu instalowania, aktualizowania i konfigurowanie programu Azure PowerShell, poleceń cmdlet można także znaleźć w [w tym rozdziale przewodnika wdrażania][deployment-guide-4.1].

Komfort do tej pory było programu PowerShell (PS) to oczywiście bardziej zaawansowane narzędzia do wdrażania maszyn wirtualnych oraz tworzenie niestandardowych krokach we wdrożeniu maszyn wirtualnych. Wszystkich klientów działających wystąpień SAP na platformie Azure są przy użyciu poleceń cmdlet PS uzupełnienie zadań zarządzania, nie w witrynie Azure portal lub są a nawet przeznaczona wyłącznie do wdrożeń na platformie Azure przy użyciu poleceń cmdlet PS. Ponieważ polecenia cmdlet specyficzne dla platformy Azure mają tej samej konwencji nazewnictwa jak więcej niż 2000 polecenia cmdlet związane z Windows, jest łatwym zadaniem administratorów Windows, należy skorzystać z tych poleceń cmdlet.

Zobacz przykład poniżej: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Wdrażanie rozszerzenia monitorowania platformy Azure dla rozwiązania SAP (zobacz rozdział [Azure rozwiązanie do monitorowania dla rozwiązania SAP] [ planning-guide-9.1] w tym dokumencie) jest możliwe tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia. W związku z tym jest konieczne instalowanie i konfigurowanie programu PowerShell lub interfejsu wiersza polecenia podczas wdrażania i administrowania systemem SAP NetWeaver na platformie Azure.  

Jak platforma Azure oferuje więcej funkcji, nowe polecenia cmdlet PS są mają zostać dodane wymagającym aktualizacja poleceń cmdlet. W związku z tym warto Sprawdź witrynę Azure Pobierz co najmniej raz w miesiącu <https://azure.microsoft.com/downloads/> nowej wersji poleceń cmdlet. Nowa wersja jest instalowana na starszej wersji.

Ogólną listę dotyczących platformy Azure PowerShell polecenia znaleźć tutaj: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Zarządzanie za pomocą poleceń interfejsu wiersza polecenia Microsoft Azure

Dla klientów korzystających z systemu Linux i chcesz zarządzać Azure zasobów programu Powershell może nie być opcję. Firma Microsoft oferuje wiersza polecenia platformy Azure jako alternatywa.
Interfejs wiersza polecenia platformy Azure udostępnia zestaw typu "open source", międzyplatformowych poleceń do pracy z platformą Azure. Interfejs wiersza polecenia platformy Azure udostępnia wiele funkcji w witrynie Azure portal.

Aby uzyskać informacje dotyczące instalacji, konfiguracji i jak używać interfejsu wiersza polecenia platformy Zobacz polecenia do wykonywania zadań platformy Azure

* [Instalowanie platformy Azure klasyczny interfejs wiersza polecenia][xplat-cli]
* [Wdrażania i zarządzania maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Użyj klasycznego wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows za pomocą usługi Azure Resource Manager][xplat-cli-azure-resource-manager]

Przeczytaj również rozdział [wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux] [ deployment-guide-4.5.2] w [przewodnik wdrażania] [ planning-guide] na temat wdrażania, monitorowania platformy Azure przy użyciu wiersza polecenia platformy Azure Rozszerzenie dla rozwiązania SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Różne sposoby, aby wdrożyć maszyny wirtualne dla rozwiązania SAP na platformie Azure

W tym rozdziale dowiesz się różne sposoby, aby wdrożyć Maszynę wirtualną na platformie Azure. Obsługa wirtualnych dysków twardych i maszyny wirtualne na platformie Azure, a także procedury dodatkowych przygotowań zostały omówione w tym rozdziale.

### <a name="deployment-of-vms-for-sap"></a>Wdrażanie maszyn wirtualnych dla rozwiązania SAP

Platforma Microsoft Azure oferuje wiele sposobów, aby wdrożyć maszyny wirtualne i skojarzone dyski. Dlatego ważne jest zrozumienie różnic, ponieważ przygotowań maszyny wirtualne mogą się różnić w zależności od metody wdrażania. Ogólnie rzecz biorąc firma Microsoft, zapoznaj się w następujących scenariuszach:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Przenoszenie maszyny Wirtualnej ze środowiska lokalnego do platformy Azure z dyskiem uogólniony

Zamierzasz przenieść określonego systemu SAP ze środowiska lokalnego na platformę Azure. Można to zrobić przez przekazanie wirtualnego dysku twardego, który zawiera system operacyjny i pliki binarne SAP, pliki binarne systemu DBMS oraz wirtualne dyski twarde, plikami danych i dziennika systemu DBMS na platformie Azure. W przeciwieństwie do [scenariusza #2 poniżej][planning-guide-5.1.2]Zachowaj nazwę hosta, SAP SID i SAP użytkownika konta w maszynie Wirtualnej platformy Azure, jak zostały skonfigurowane w środowisku lokalnym. W związku z tym uogólnianie obrazu nie jest konieczne. Zobacz rozdziały [przygotowania do przenoszenia maszyny Wirtualnej ze środowiska lokalnego na platformę Azure z dyskiem uogólniony] [ planning-guide-5.2.1] niniejszego dokumentu procedury przygotowania w środowisku lokalnym i przekazywania — uogólniony maszyn wirtualnych lub wirtualnych dysków twardych na platformie Azure. Rozdział odczytu [Scenariusz 3: Przenoszenie maszyny Wirtualnej ze środowiska lokalnego przy użyciu-uogólniony wirtualny dysk twardy platformy Azure z oprogramowaniem SAP] [ deployment-guide-3.4] w [przewodnik wdrażania] [ deployment-guide] szczegółowy opis kroków wdrażania obrazu w Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Wdrażanie maszyny Wirtualnej przy użyciu obrazu właściwe dla klienta

Ze względu na wymagania określone poprawki wersji systemu operacyjnego lub DBMS podana obrazów w portalu Azure Marketplace może nie spełniać Twoich wymagań. W związku z tym może być konieczne Utwórz Maszynę wirtualną przy użyciu własnych maszyn wirtualnych systemu operacyjnego/DBMS obrazu prywatnego, który można wdrożyć w kilka razy później. Aby przygotować obrazu prywatnego pod kątem duplikatów, powinny być traktowane następujące elementy:

---
> ![Windows][Logo_Windows] Windows
>
> Tutaj znajdziesz więcej informacji: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> Ustawienia Windows (na przykład Windows SID lub nazwa hosta) musi być wyodrębnione/uogólniony na lokalną maszynę Wirtualną za pomocą polecenia sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Wykonaj kroki opisane w poniższych artykułach [SUSE][virtual-machines-linux-create-upload-vhd-suse], [firmy Red Hat][virtual-machines-linux-redhat-create-upload-vhd], lub [Oracle Linux] [ virtual-machines-linux-create-upload-vhd-oracle], aby przygotować wirtualny dysk twardy do przesłania na platformę Azure.
>
>

---
Jeśli zainstalowano już SAP zawartość w swojej lokalnej maszyny Wirtualnej (szczególnie w przypadku systemów warstwy 2), możesz dostosować ustawienia systemu SAP po wdrożenie maszyny Wirtualnej platformy Azure za pośrednictwem wystąpienia nazwy procedury obsługiwane przez oprogramowanie SAP oprogramowania inicjowania obsługi administracyjnej Menedżera (SAP Należy pamiętać, [1619720]). Zobacz rozdziały [przygotowania do wdrożenia maszyny Wirtualnej przy użyciu obrazu właściwe dla klienta dla rozwiązania SAP] [ planning-guide-5.2.2] i [przekazywanie wirtualnego dysku twardego ze środowiska lokalnego na platformę Azure] [ planning-guide-5.3.2]tego dokumentu dla lokalnej procedury przygotowywania i przekazywania uogólnionej maszyny Wirtualnej na platformie Azure. Rozdział odczytu [Scenariusz 2: Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu dla rozwiązania SAP] [ deployment-guide-3.3] w [przewodnik wdrażania] [ deployment-guide] szczegółowy opis kroków wdrażania obrazu na platformie Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Wdrażanie maszyny Wirtualnej z portalu Azure Marketplace

Czy chcesz korzystać z usługi Microsoft lub innych firm podany obraz maszyny Wirtualnej w portalu Azure Marketplace, aby wdrożyć maszynę Wirtualną. Po wdrożeniu maszyny Wirtualnej na platformie Azure, wykonaj te same wskazówki i narzędzia do zainstalowania oprogramowania SAP i/lub DBMS wewnątrz maszyny Wirtualnej, tak jak w środowisku lokalnym. Aby uzyskać bardziej szczegółowe opis wdrożenia, zobacz rozdział [scenariusz 1: Wdrażanie maszyny Wirtualnej z portalu Azure Marketplace dla rozwiązania SAP] [ deployment-guide-3.2] w [przewodnik wdrażania][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Przygotowywanie maszyn wirtualnych z oprogramowaniem SAP na platformie Azure

Przed przekazaniem maszyn wirtualnych na platformie Azure, musisz upewnij się, że maszyny wirtualne i wirtualne dyski twarde spełnienia określonych wymagań. Istnieją niewielkie różnice w zależności od metody wdrażania, który jest używany.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Przygotowanie do przenoszenia maszyny Wirtualnej ze środowiska lokalnego na platformę Azure z dyskiem uogólniony

Typowe metody wdrożenia jest przenoszenie istniejącej maszyny Wirtualnej, która jest uruchamiana z systemem SAP ze środowiska lokalnego do platformy Azure. Tej maszyny Wirtualnej i systemem SAP na maszynie wirtualnej po prostu należy uruchamiać na platformie Azure przy użyciu tej samej nazwy hosta i prawdopodobnie tego samego identyfikatora SID SAP. W takim przypadku system operacyjny maszyny Wirtualnej gościa nie powinien być uogólniony podczas wielu wdrożeń. Jeśli sieć lokalna został rozszerzony na platformę Azure (zobacz rozdział [między środowiskami lokalnymi - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure przy użyciu wymaganie jest w pełni zintegrowany z siecią lokalną] [ planning-guide-2.2] w tym dokumencie), a następnie nawet tego samego konta domeny mogą być używane w maszynie Wirtualnej, zgodnie z tymi były używane przed w środowisku lokalnym.

Wymagania, przygotowując swoje własne dysku maszyny Wirtualnej platformy Azure są następujące:

* Pierwotnie wirtualny dysk twardy zawierający system operacyjny może mieć maksymalny rozmiar 127GB tylko. To ograniczenie stało się wyeliminować z końcem marca 2015 r. Teraz wirtualny dysk twardy zawierający system operacyjny może być maksymalnie 1TB, jak również inne usługi Azure Storage hostowanym wirtualnego dysku twardego.
* Musi to być stały format wirtualnego dysku twardego. Dynamicznych wirtualnych dysków twardych lub wirtualnych dysków twardych w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamicznych wirtualnych dysków twardych zostaną przekonwertowane na statyczne wirtualnych dysków twardych podczas przekazywania wirtualnego dysku twardego za pomocą apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Wirtualne dyski twarde, które są zainstalowane na maszynie wirtualnej i powinny być zainstalowane ponownie na platformie Azure na potrzeby maszyn wirtualnych w także stały format VHD. Przeczytaj [w tym artykule (Linux)](../../linux/managed-disks-overview.md) i [w tym artykule (Windows)](../../windows/managed-disks-overview.md)) dla limitów rozmiaru dysków z danymi. Dynamicznych wirtualnych dysków twardych zostaną przekonwertowane na statyczne wirtualnych dysków twardych podczas przekazywania wirtualnego dysku twardego za pomocą apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Dodaj inne konto lokalne z uprawnieniami administratora, które mogą być używane w pomocy technicznej firmy Microsoft lub które mogą być przypisane jako kontekst dla usług i aplikacji do uruchamiania w, dopóki nie wdrożono maszynę Wirtualną, a użytkownicy bardziej odpowiednie może być używana.
* Dodaj innych kont lokalnych, jak te mogą być wymagane dla scenariusza wdrażania.

---
> ![Windows][Logo_Windows] Windows
>
> W tym scenariuszu nie Generalizacja (sysprep) maszyny wirtualnej jest wymagany, aby przekazać i wdrożyć maszynę Wirtualną na platformie Azure.
> Upewnij się, dysku D:\, nie jest używana.
> Ustaw dysku automatycznej instalacji, dla dołączonych dysków, zgodnie z opisem w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski] [ planning-guide-5.5.3] w tym dokumencie.
>
> ![Linux][Logo_Linux] Linux
>
> W tym scenariuszu nie Generalizacja (waagent-deprovision) maszyny wirtualnej musi przekazać i wdrożyć maszynę Wirtualną na platformie Azure.
> Upewnij się, że wolumin/mnt/zasób nie jest używany i czy wszystkie dyski są instalowane za pomocą identyfikatora uuid. Dla dysku systemu operacyjnego upewnij się, że wpis programu inicjującego również odzwierciedla instalacji na podstawie identyfikatora uuid.
>
>

---
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Przygotowanie do wdrożenia maszyny Wirtualnej przy użyciu obrazu właściwe dla klienta dla rozwiązania SAP

Pliki wirtualnego dysku twardego, które zawierają uogólnionego systemu operacyjnego są przechowywane w kontenerach na kontach magazynu Azure lub obrazy dysków zarządzanych. Można wdrożyć nową maszynę Wirtualną z takiego obrazu, odwołując się do obrazu wirtualnego dysku twardego lub dysku zarządzanego jako źródło w plikach szablonu wdrożenia zgodnie z opisem w rozdziale [Scenariusz 2: Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu dla rozwiązania SAP] [ deployment-guide-3.3] z [przewodnik wdrażania][deployment-guide].

Wymagania podczas przygotowywania swój własny obraz maszyny Wirtualnej platformy Azure są następujące:

* Pierwotnie wirtualny dysk twardy zawierający system operacyjny może mieć maksymalny rozmiar 127GB tylko. To ograniczenie stało się wyeliminować z końcem marca 2015 r. Teraz wirtualny dysk twardy zawierający system operacyjny może być maksymalnie 1TB, jak również inne usługi Azure Storage hostowanym wirtualnego dysku twardego.
* Musi to być stały format wirtualnego dysku twardego. Dynamicznych wirtualnych dysków twardych lub wirtualnych dysków twardych w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamicznych wirtualnych dysków twardych zostaną przekonwertowane na statyczne wirtualnych dysków twardych podczas przekazywania wirtualnego dysku twardego za pomocą apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Wirtualne dyski twarde, które są zainstalowane na maszynie wirtualnej i powinny być zainstalowane ponownie na platformie Azure na potrzeby maszyn wirtualnych w także stały format VHD. Przeczytaj [w tym artykule (Linux)](../../windows/managed-disks-overview.md) i [w tym artykule (Windows)](../../linux/managed-disks-overview.md) limitów rozmiaru dysków danych. Dynamicznych wirtualnych dysków twardych zostaną przekonwertowane na statyczne wirtualnych dysków twardych podczas przekazywania wirtualnego dysku twardego za pomocą apletów poleceń programu PowerShell lub interfejsu wiersza polecenia
* Dodaj innych kont lokalnych, jak te mogą być wymagane dla scenariusza wdrażania.
* Jeśli obraz zawiera instalację oprogramowania SAP NetWeaver i zmianę nazwy hosta z oryginalną nazwę punkcie wdrażania platformy Azure jest prawdopodobne, zaleca się skopiuj najnowszymi wersjami programów SAP oprogramowania inicjowania obsługi administracyjnej Menedżera dysków DVD do szablonu. Spowoduje to włączenie pozwala łatwo zaadaptować zmienione nazwy hosta i/lub zmiany identyfikatora SID systemu SAP w ramach wdrożonym obrazie maszyny Wirtualnej, zaraz po uruchomieniu nowej kopii przy użyciu funkcji zmiany nazwy SAP, pod warunkiem.

---
> ![Windows][Logo_Windows] Windows
>
> Upewnij się, że dysku D:\ nie jest używany zestaw automatycznej instalacji z dysku dla dołączonych dysków, zgodnie z opisem w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski] [ planning-guide-5.5.3] w tym dokumencie.
>
> ![Linux][Logo_Linux] Linux
>
> Upewnij się, że wolumin/mnt/zasób nie jest używany i czy wszystkie dyski są instalowane za pomocą identyfikatora uuid. Dla dysku systemu operacyjnego upewnij się, że pozycja programu inicjującego również odzwierciedla instalacji na podstawie identyfikatora uuid.
>
>

---
* SAP graficznego interfejsu użytkownika (dla administracyjnych i konfiguracji celów) mogą być wstępnie zainstalowany w taki szablon.
* Tak długo, jak to oprogramowanie może współpracować z Zmień nazwę maszyny wirtualnej można zainstalować inne oprogramowanie, które są niezbędne pomyślne uruchomienie maszyn wirtualnych w scenariuszach obejmujących wiele lokalizacji.

Jeśli maszyna wirtualna jest gotowa wystarczająco ogólny i po pewnym czasie niezależnie od kont użytkowników/nie jest dostępna w tym scenariuszu wdrażania platformy docelowej, ostatnim krokiem przygotowania uogólniania taki obraz jest przeprowadzane.

##### <a name="generalizing-a-vm"></a>Uogólnianie maszyny Wirtualnej
---
> ![Windows][Logo_Windows] Windows
>
> Ostatnim krokiem jest zalogować się do maszyny Wirtualnej przy użyciu konta administratora. Otwórz okno poleceń programu Windows jako *administratora*. Przejdź do %windir%\windows\system32\sysprep i wykonaj sysprep.exe.
> Zostanie wyświetlone okno małe. Jest ważne sprawdzić **Generalize** opcja (Domyślnie zaznaczenie jest usunięte) i zmień opcję zamykania z jego domyślnej "Ponowne uruchomienie" "Zamknij". W tej procedurze założono, że proces programu sysprep jest wykonywany lokalnie w systemie operacyjnym gościa maszyny wirtualnej.
> Jeśli chcesz wykonać procedurę z maszyn wirtualnych już działających na platformie Azure, wykonaj czynności opisane w [w tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Jak przechwycić maszynę wirtualną systemu Linux do użycia jako szablon usługi Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferowanie maszyn wirtualnych i wirtualnych dyskach twardych między środowiska lokalnego do platformy Azure
Ponieważ przekazywania obrazów maszyn wirtualnych i dysków na platformie Azure nie jest możliwe za pośrednictwem witryny Azure portal, należy użyć polecenia cmdlet programu Azure PowerShell lub interfejsu wiersza polecenia. Inną możliwością jest użycie narzędzia "Narzędzie AzCopy". Narzędzie można skopiować wirtualne dyski twarde między lokalną i platformą Azure (w obu kierunkach). Również może kopiować pliki VHD, między regionami platformy Azure. Zapoznaj się [tej dokumentacji] [ storage-use-azcopy] do pobrania i użycia narzędzia AzCopy.

Trzeci alternatywą jest za pomocą różnych narzędzi korzystający z graficznego interfejsu użytkownika innych firm. Jednak należy się upewnić, że te narzędzia znajdują się pomocne Azure stronicowych obiektów blob. Dla naszych potrzeb, należy użyć magazynu obiektów Blob platformy Azure (różnice są opisane tutaj: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Swoją wydajność WE kompresowania maszyny wirtualne i wirtualne dyski twarde, które należy przekazać są także narzędzi dostarczanych przez platformę Azure. Jest to ważne, ponieważ wydajność w kompresji skraca czas przekazywania, (co różni się od mimo to w zależności od połączenia przekazywania w Internecie z funkcji w środowisku lokalnym i region wdrażania platformy docelowe). Jest uczciwe założenie, że, przekazywanie maszyny Wirtualnej lub wirtualnego dysku twardego z lokalizacji Europejskiego amerykańskiej dane platformy Azure, które centra będzie trwać dłużej niż przekazywanie tych samych maszyn wirtualnych/wirtualne dyski twarde na platformie Azure w europejskich centrach danych.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Przekazywanie wirtualnego dysku twardego ze środowiska lokalnego do platformy Azure
Do przekazania istniejącej maszyny Wirtualnej lub wirtualnego dysku twardego z sieci lokalnej maszyny Wirtualnej lub wirtualnego dysku twardego musi spełniać wymagania zgodnie z opisem w rozdziale [przygotowania do przenoszenia maszyny Wirtualnej ze środowiska lokalnego na platformę Azure z dyskiem uogólniony] [ planning-guide-5.2.1]tego dokumentu.

Maszyna wirtualna nie musi być uogólniony i mogą być przekazywane w stanie i kształt, który posiada po zamknięciu systemu lokalnego po stronie. Dotyczy to także dodatkowe wirtualne dyski twarde, które nie zawierają dowolnego systemu operacyjnego.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Przekazywanie wirtualnego dysku twardego i oznaczania go jako dysk platformy Azure
W tym przypadku chcemy przekazywanie wirtualnego dysku twardego, z lub bez systemu operacyjnego i zainstalować go na Maszynę wirtualną jako dysk z danymi lub używać go jako dysk systemu operacyjnego. Jest to proces obejmujący wiele kroków

**PowerShell**

* Zaloguj się do subskrypcji za pomocą *Connect AzAccount*
* Ustaw subskrypcję kontekstu z *AzContext zestaw* i parametr SubscriptionId lub SubscriptionName — zobacz <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Przekazywanie wirtualnego dysku twardego z *AzVhd Dodaj* na konto usługi Azure Storage — zobacz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcjonalnie) Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego z *New AzDisk* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Ustaw dysk systemu operacyjnego nowa konfiguracja maszyny Wirtualnej do wirtualnego dysku twardego lub dysk zarządzany przy użyciu *AzVMOSDisk zestaw* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Tworzenie nowej maszyny Wirtualnej na podstawie konfiguracji maszyny Wirtualnej za pomocą *New-AzVM* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Dodaj dysk danych do nowej maszyny Wirtualnej za pomocą *AzVMDataDisk Dodaj* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Interfejs wiersza polecenia platformy Azure**

* Zaloguj się do subskrypcji za pomocą *logowania az*
* Wybierz swoją subskrypcję z *set konta az--subskrypcji `<subscription name or id`>*
* Przekazywanie wirtualnego dysku twardego z *az storage blob upload* — zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage][storage-azure-cli]
* (Opcjonalnie) Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego z *tworzenia dysku az* — zobacz https://docs.microsoft.com/cli/azure/disk
* Utwórz nową maszynę Wirtualną, określając przekazanych wirtualnego dysku twardego lub dysku zarządzanego jako dysk systemu operacyjnego za pomocą *tworzenie az vm* i parametru *— dołączanie os-disk*
* Dodaj dysk danych do nowej maszyny Wirtualnej za pomocą *dołączanie dysku maszyny wirtualnej az* i parametru *— nowe*

**Szablon**

* Przekazywanie wirtualnego dysku twardego z interfejsem wiersza polecenia programu Powershell lub na platformie Azure
* (Opcjonalnie) Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego za pomocą programu Powershell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal
* Wdróż maszynę Wirtualną przy użyciu szablonu JSON odwołujące się do wirtualnego dysku twardego, jak pokazano na [ten przykładowy szablon JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) lub za pomocą dysków zarządzanych, jak pokazano w [ten przykładowy szablon JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Wdrażanie obrazu maszyny Wirtualnej
Do przekazania istniejącej maszyny Wirtualnej lub wirtualnego dysku twardego z siecią lokalną, aby można było używać go jako obraz maszyny Wirtualnej platformy Azure tworzenia maszyny Wirtualnej lub wirtualnego dysku twardego muszą spełniać wymagania wymienione w rozdziale [przygotowania do wdrożenia maszyny Wirtualnej przy użyciu obrazu właściwe dla klienta dla rozwiązania SAP] [ planning-guide-5.2.2] tego dokumentu.

* Użyj *sysprep* na Windows lub *waagent-deprovision* w systemie Linux w celu uogólnienia maszyny Wirtualnej — zobacz [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla Windows lub [sposobu przechwytywania Maszyny wirtualnej systemu Linux do użycia jako szablon usługi Resource Manager] [ capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Zaloguj się do subskrypcji za pomocą *Connect AzAccount*
* Ustaw subskrypcję kontekstu z *AzContext zestaw* i parametr SubscriptionId lub SubscriptionName — zobacz <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Przekazywanie wirtualnego dysku twardego z *AzVhd Dodaj* na konto usługi Azure Storage — zobacz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego na podstawie wirtualnego dysku twardego z *New AzImage* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Dysk systemu operacyjnego nowa konfiguracja maszyny Wirtualnej do zestawu
  * Wirtualny dysk twardy z *Set AzVMOSDisk - SourceImageUri fromImage - CreateOption* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Obraz dysku zarządzanego *AzVMSourceImage zestaw* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Tworzenie nowej maszyny Wirtualnej na podstawie konfiguracji maszyny Wirtualnej za pomocą *New-AzVM* — zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Interfejs wiersza polecenia platformy Azure**

* Użyj *sysprep* na Windows lub *waagent-deprovision* w systemie Linux w celu uogólnienia maszyny Wirtualnej — zobacz [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla Windows lub [sposobu przechwytywania Maszyny wirtualnej systemu Linux do użycia jako szablon usługi Resource Manager] [ capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Zaloguj się do subskrypcji za pomocą *logowania az*
* Wybierz swoją subskrypcję z *set konta az--subskrypcji `<subscription name or id`>*
* Przekazywanie wirtualnego dysku twardego z *az storage blob upload* — zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage][storage-azure-cli]
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego na podstawie wirtualnego dysku twardego z *utworzyć obraz az* — zobacz https://docs.microsoft.com/cli/azure/image
* Utwórz nową maszynę Wirtualną, określając przekazanych wirtualnego dysku twardego lub obrazu dysku zarządzanego jako dysk systemu operacyjnego za pomocą *tworzenie az vm* i parametru *— obraz*

**Szablon**

* Użyj *sysprep* na Windows lub *waagent-deprovision* w systemie Linux w celu uogólnienia maszyny Wirtualnej — zobacz [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx) dla Windows lub [sposobu przechwytywania Maszyny wirtualnej systemu Linux do użycia jako szablon usługi Resource Manager] [ capture-image-linux-step-2-create-vm-image] dla systemu Linux
* Przekazywanie wirtualnego dysku twardego z interfejsem wiersza polecenia programu Powershell lub na platformie Azure
* (Opcjonalnie) Tworzenie obrazu dysku zarządzanego na podstawie wirtualnego dysku twardego za pomocą programu Powershell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal
* Wdróż maszynę Wirtualną przy użyciu szablonu JSON odwołujące się do obrazu wirtualnego dysku twardego, jak pokazano na [ten przykładowy szablon JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) lub przy użyciu obrazu dysku zarządzanego, jak pokazano na [ten przykładowy szablon JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Pobieranie wirtualnych dysków twardych lub dysków zarządzanych do środowiska lokalnego
Infrastruktura platformy Azure jako usługa nie jest jednokierunkowa ulicy o tylko możliwość przekazywania wirtualne dyski twarde i SAP systemów. Możesz przenieść SAP systemów z platformy Azure z powrotem do na świecie lokalnych.

Podczas pobierania wirtualnych dysków twardych lub dysków Managed Disks nie może być aktywne. Nawet wtedy, gdy pobieranie dyski, które są instalowane na maszynach wirtualnych, maszyna wirtualna musi zostać zamknięta i z cofniętą alokacją. Jeśli chcesz pobrać zawartość bazy danych, który następnie powinien być używany do konfigurowania nowego systemu w środowisku lokalnym i jeśli jest dopuszczalne działać podczas ich pobierania i instalacji nowego systemu, nadal możesz systemu na platformie Azure , może długo przestojom, wykonując kopię zapasową bazy danych skompresowanych w postaci dysku i wystarczy pobrać ten dysk, zamiast również pobieranie maszyny Wirtualnej podstawowego systemu operacyjnego.

#### <a name="powershell"></a>PowerShell

* Pobieranie dysku zarządzanego  
  Należy najpierw uzyskać dostęp do bazowego obiektu blob dysku zarządzanego. Następnie możesz skopiować podstawowego obiektu blob do nowego konta magazynu i pobrać obiekt blob z tego konta magazynu.

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

* Pobieranie wirtualnego dysku twardego  
  Po zatrzymaniu systemu SAP i maszyna wirtualna jest zamknięta, służy polecenie cmdlet programu PowerShell AzVhd Zapisz w elemencie docelowym w środowisku lokalnym podczas pobierania dyski VHD w środowisku lokalnym. Aby to zrobić, wymagany jest adres URL wirtualnego dysku twardego, który można znaleźć w "magazynem sekcji" i witryny Azure portal (wymagana, aby przejść do konta magazynu i kontener magazynu, w której został utworzony wirtualny dysk twardy) musi wiedzieć, gdzie wirtualny dysk twardy powinien zostać skopiowany do.

  Następnie możesz wykorzystać polecenie przez zdefiniowanie parametru sourceuri i jako adres URL wirtualnego dysku twardego do pobrania i LocalFilePath jako fizyczną lokalizację wirtualnego dysku twardego (w tym jej nazwę). Polecenie może wyglądać podobnie do:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Aby uzyskać więcej informacji na polecenia cmdlet Save-AzVhd Sprawdź tutaj <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
* Pobieranie dysku zarządzanego  
  Należy najpierw uzyskać dostęp do bazowego obiektu blob dysku zarządzanego. Następnie możesz skopiować podstawowego obiektu blob do nowego konta magazynu i pobrać obiekt blob z tego konta magazynu.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Pobieranie wirtualnego dysku twardego   
  Po zatrzymaniu systemu SAP i maszyna wirtualna jest zamknięta, można użyć polecenia wiersza polecenia platformy Azure _usługi azure storage blob download_ w elemencie docelowym w środowisku lokalnym, do pobierania wirtualnego dysku twardego dyski z powrotem do świata w środowisku lokalnym. Aby to zrobić, potrzebna jest nazwa i kontener wirtualnego dysku twardego, który można znaleźć w sekcji magazyn w witrynie Azure Portal (wymagana, aby przejść do konta magazynu i kontener magazynu, w której został utworzony wirtualny dysk twardy) i chcesz wiedzieć, gdzie wirtualny dysk twardy powinien być cop iowane do.

  Następnie możesz wykorzystać polecenie przez zdefiniowanie parametrów obiektu blob i kontener wirtualnego dysku twardego umożliwiające pobranie i miejsce docelowe jako lokalizacja docelowa fizycznego wirtualnego dysku twardego (w tym jej nazwę). Polecenie może wyglądać podobnie do:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Przenoszenie maszyn wirtualnych i dysków w ramach platformy Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopiowanie systemów SAP na platformie Azure

System SAP lub nawet dedykowanego systemu DBMS na serwer obsługujący warstwy aplikacji SAP prawdopodobnie będą obejmować kilka dysków, które zawierają system operacyjny przy użyciu plików binarnych albo plików danych i dziennika bazy danych SAP. Funkcjonalność platformy Azure kopiowania dysków ani funkcjonalność platformy Azure, zapisywać dysków na dysku lokalnym nie posiada mechanizm synchronizacji, które migawek wielu dysków w spójny sposób. W związku z tym stan dysków skopiowane i zapisane nawet wtedy, gdy te są instalowane z tej samej maszyny Wirtualnej będzie inny. Oznacza to, że w przypadku konkretnego wystąpienia w różnych danych i logfile(s) znajdujących się na różnych dyskach, bazy danych w końcu byłoby niezgodne.

**Podsumowanie: Aby skopiować lub zapisać dyski, które są częścią konfiguracji systemu SAP należy zatrzymać systemu SAP i trzeba także zamykanie wdrożonej maszyny Wirtualnej. Dopiero wtedy można skopiować lub pobrać zestaw dysków, aby utworzyć kopię systemu SAP na platformie Azure lub lokalnie.**

Dyski danych mogą być przechowywane jako pliki VHD na koncie usługi Azure Storage i może być podłączona bezpośrednio do maszyny wirtualnej lub być używany jako obraz. W tym przypadku wirtualnego dysku twardego jest kopiowany do innej lokalizacji przed dołączony do maszyny wirtualnej. Pełna nazwa pliku VHD na platformie Azure musi być unikatowa na platformie Azure. Jak wspomniano wcześniej już, nazwa jest rodzaju nazwę trzyczęściowej serii, która wygląda następująco:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Dyski danych mogą być również Managed Disks. W takim przypadku dysk zarządzany jest używany do utworzenia nowego dysku zarządzanego przed dołączony do maszyny wirtualnej. Nazwa dysku zarządzanego musi być unikatowa w obrębie grupy zasobów.

##### <a name="powershell"></a>PowerShell

Polecenia cmdlet programu Azure PowerShell umożliwiają Skopiuj wirtualny dysk twardy, jak pokazano na [w tym artykule][storage-powershell-guide-full-copy-vhd]. Aby utworzyć nowy dysk zarządzany, jak pokazano w poniższym przykładzie należy użyć AzDiskConfig nowy i New-AzDisk.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wiersza polecenia platformy Azure umożliwia skopiowanie wirtualnego dysku twardego, jak pokazano na [w tym artykule][storage-azure-cli-copy-blobs]. Aby utworzyć nowy dysk zarządzany, użyj *tworzenia dysku az* jak pokazano w poniższym przykładzie.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Narzędzia usługi Azure Storage

* <https://storageexplorer.com/>

Profesjonalne wersje Eksploratory usługi Azure Storage można znaleźć tutaj:

* <https://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

Kopię samego dysku VHD na koncie magazynu jest procesem, który zajmuje tylko kilka sekund (podobnie jak na sprzęcie sieci SAN, Tworzenie migawki z opóźnioną kopię i kopii przy zapisie). Po utworzeniu kopii pliku wirtualnego dysku twardego, możesz dołączyć do maszyny wirtualnej lub go użyć jako obraz, aby dołączyć kopie dysk VHD do maszyn wirtualnych.

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
Nie można wykonać tego zadania w witrynie Azure portal. Można użyć poleceń cmdlet programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przeglądarki magazynu innych firm. Polecenia cmdlet programu PowerShell lub interfejsu wiersza polecenia można tworzyć i zarządzać obiektami blob, które obejmują możliwość asynchronicznie kopiować obiekty BLOB na kontach magazynu i w wielu regionach w ramach subskrypcji platformy Azure.

##### <a name="powershell"></a>PowerShell
Można także skopiować pliki VHD, między subskrypcjami. Aby uzyskać więcej informacji, przeczytaj [w tym artykule][storage-powershell-guide-full-copy-vhd].

Podstawowy przepływ PS logiki polecenia cmdlet wygląda następująco:

* Utwórz kontekst konta magazynu **źródła** konto magazynu przy użyciu *New AzStorageContext* — zobacz <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Utwórz kontekst konta magazynu **docelowej** konto magazynu przy użyciu *New AzStorageContext* — zobacz <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Rozpocznij kopiowanie z

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Sprawdź stan kopii w pętli z

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Dołączanie nowego wirtualnego dysku twardego do maszyny wirtualnej, zgodnie z powyższym opisem.

Aby uzyskać przykłady zobacz [w tym artykule][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
* Rozpocznij kopiowanie z

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Sprawdź stan, jeśli kopia jest nadal w pętli za pomocą usługi

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Dołączanie nowego wirtualnego dysku twardego do maszyny wirtualnej, zgodnie z powyższym opisem.

Aby uzyskać przykłady zobacz [w tym artykule][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Obsługa dysku

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Maszyna wirtualna/dysk struktury dla wdrożeń SAP

W idealnym obsługi struktury Maszynę wirtualną i skojarzone dyski powinny być łatwe. W przypadku instalacji lokalnej klienci opracowała wiele sposobów tworzenia struktury instalacji na serwerze.

* Jeden dysk podstawowy, który zawiera systemu operacyjnego i wszystkich plików binarnych systemu DBMS i/lub SAP. Od marca 2015 r. ten dysk może być maksymalnie 1TB w rozmiarze zamiast wcześniejszych ograniczeń, które maksymalnie 127 GB.
* Jeden lub wiele dysków, które zawiera plik dziennika systemu DBMS bazy danych SAP i pliku dziennika systemu DBMS obszaru tymczasowego magazynu (o ile systemu DBMS obsługuje tę funkcję). Wymagania operacji We/Wy dziennika bazy danych są wysokie, należy stripe wielu dysków w celu osiągnięcia wymagana objętość operacji We/Wy.
* Liczba dysków zawierających jeden lub dwa pliki bazy danych, bazy danych SAP DBMS dane tymczasowe również i plików (Jeśli systemu DBMS obsługuje tę funkcję).

![Odwołanie do konfiguracji maszyny wirtualnej IaaS platformy Azure dla rozwiązania SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Za pomocą wielu klientów widzieliśmy konfiguracje, na przykład, SAP i DBMS pliki binarne nie zostały zainstalowane na dysku c:\, w którym zainstalowano system operacyjny. Istnieją różne powody, dla tego, ale gdy powrócimy do katalogu głównego, zwykle nadszedł czy dyski są małe i uaktualnień systemu operacyjnego wymagane dodatkowe miejsce na 10 – 15 lat temu. Oba te warunki są stosowane, zbyt często już w te dni. Już dziś można mapować dysku c:\ na maszynach wirtualnych lub dyski dużą woluminu. W celu uproszczenia wdrożenia w ich struktury, zalecane jest zgodny z następującym wzorem wdrażania systemów SAP NetWeaver na platformie Azure
>
> Plik stronicowania systemu operacyjnego Windows powinna znajdować się na dysku D: (dysk nietrwałe)
>
> ![Linux][Logo_Linux] Linux
>
> Umieść swapfile systemu Linux w katalogu/mnt/mnt/zasobów w systemie Linux, zgodnie z opisem w [w tym artykule][virtual-machines-linux-agent-user-guide]. Plik wymiany można skonfigurować w pliku konfiguracji w ścieżce/etc/waagent.conf agenta systemu Linux. Dodać lub zmienić następujące ustawienia:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Aby aktywować zmiany, należy ponownie uruchomić agenta systemu Linux przy użyciu

```
sudo service waagent restart
```

Zapoznaj się uwagę [1597355] więcej informacji na temat wymiany zalecany rozmiar pliku

---
Liczbę dysków używanych dla systemu DBMS plików danych i typ magazynu platformy Azure, te dyski znajdują się w powinna zależeć z wymaganiami dotyczącymi operacji We/Wy i opóźnienia wymagane. Dokładne przydziały są opisane w [w tym artykule (Linux)] [ virtual-machines-sizes-linux] i [w tym artykule (Windows)][virtual-machines-sizes-windows].

Środowisko SAP wdrożeń w ciągu ostatnich dwóch lat prowadzone nam pewne lekcje, które mogą być podsumowywane jako:

* Ruch operacje We/Wy do plików danych różnych nie zawsze jest taki sam od istniejących systemów klientów może mieć inaczej o rozmiarze pliki danych reprezentujących ich baz danych SAP. W rezultacie jego okazała się lepiej korzystać z konfiguracji RAID na wiele dysków do umieszczenia plików danych, które jednostki LUN używać poza tymi. Wystąpiły sytuacjach, szczególnie w przypadku usługi Azure Standard Storage, gdzie szybkości operacji We/Wy osiągnięty przydział jednego dysku dla dziennika transakcji DBMS. W takich przypadkach zaleca się korzystanie z usługi Premium Storage, lub też agregowanie wielu magazynu w warstwie standardowa stripe dysków przy użyciu oprogramowania.

---
> ![Windows][Logo_Windows] Windows
>
> * [Najlepsze rozwiązania dotyczące programu SQL Server w usłudze Azure Virtual Machines wydajności][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurowanie programowej macierzy RAID w systemie Linux][virtual-machines-linux-configure-raid]
> * [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure][virtual-machines-linux-configure-lvm]
> * [Usługa Azure Storage wpisów tajnych i optymalizacje operacji We/Wy w systemie Linux](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Usługa Premium Storage jest wyświetlane znaczące lepszą wydajność, szczególnie w przypadku zapisów dziennika transakcji krytycznych. W przypadku scenariuszy SAP, które mają dostarczać produkcji, takich jak wydajność zaleca używania serię maszyn wirtualnych, które mogą korzystać z usługi Azure Premium Storage.

Należy pamiętać, że dysk, który zawiera system operacyjny, a firma Microsoft zaleca plików binarnych, SAP i bazy danych (podstawowa maszyna wirtualna), nie jest już ograniczona do 127GB. Teraz może mieć maksymalnie 1TB w rozmiarze. Powinna to być za mało miejsca, aby zachować niezbędnego pliku, w tym, na przykład dzienniki zadania usługi batch SAP.

Aby uzyskać więcej sugestii i uzyskać więcej informacji, specjalnie dla systemu DBMS na maszynach wirtualnych, zapoznaj się [przewodnik wdrażania systemu DBMS][dbms-guide]

#### <a name="disk-handling"></a>Obsługa dysku

W większości przypadków należy utworzyć dodatkowe dyski w celu wdrożenia bazy danych SAP do maszyny Wirtualnej. Wspomnieliśmy o kwestiach związanych z liczby dysków w rozdziale [struktury maszyna wirtualna/dysk dla wdrożeń SAP] [ planning-guide-5.5.1] tego dokumentu. Witryna Azure portal umożliwia dołączanie lub odłączanie dysków, po wdrożeniu podstawowej maszyny Wirtualnej. Dysków może być dołączony/odłączony po ponownym uruchomieniu maszyny Wirtualnej i uruchomione oraz kiedy została zatrzymana. Podczas podłączania na dysku, witryny Azure portal oferuje Dołącz pusty dysk lub istniejącego dysku, który w tym momencie nie jest dołączony do innej maszyny Wirtualnej.

**Uwaga**: Dyski można powiązać tylko z jednej maszyny Wirtualnej w danym momencie.

![Dołączanie / odłączanie dysków za pomocą usługi Azure Standard Storage][planning-guide-figure-1400]

Podczas wdrażania nowej maszyny wirtualnej można zdecydować, czy chcesz używać usługi Managed Disks, albo umieść dyski na kontach magazynu Azure. Jeśli chcesz korzystać z magazynu Premium, firma Microsoft zaleca, korzystają z dysków zarządzanych.

Następnie należy zdecydować, czy chcesz utworzyć nowy i pustego dysku lub tego, czy chcesz wybrać istniejący dysk, który został wcześniej przekazany i musi być podłączona do maszyny Wirtualnej teraz.

**WAŻNE**: Możesz **nie** do użycia buforowania hosta za pomocą usługi Azure Standard Storage. Preferencje pamięci podręcznej hosta należy pozostawić domyślne ustawienie NONE. Usługa Azure Premium Storage należy włączyć buforowanie odczytu Jeśli cechy operacji We/Wy jest przede wszystkim do odczytu, takie jak typowa ruch we/wy dla plików danych bazy danych. W przypadku pliku dziennika transakcji bazy danych zaleca się bez buforowania.

---
> ![Windows][Logo_Windows] Windows
>
> [Jak dołączyć dysk danych w witrynie Azure portal][virtual-machines-linux-attach-disk-portal]
>
> Jeśli są dołączone dyski, należy do logowania się do maszyny Wirtualnej, aby otworzyć Menedżera dysków Windows. Jeśli nie włączono automatycznej instalacji, zgodnie z zaleceniami w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski][planning-guide-5.5.3], nowo dołączona wolumin należy podjąć w trybie online i jest zainicjowane.
>
> ![Linux][Logo_Linux] Linux
>
> Jeśli są dołączone dyski, musisz zalogować się do maszyny Wirtualnej i inicjowanie dysków, zgodnie z opisem w [w tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Jeśli nowy dysk jest pusty dysk, należy sformatować dysk, jak również. Formatowania, szczególnie w przypadku plików danych i dziennika systemu DBMS na tym samym zalecenia, jak w przypadku wdrożenia bez systemu operacyjnego systemu DBMS mają zastosowanie.

Jak już wspomniano w rozdziale [koncepcji maszyny wirtualnej platformy Azure w usłudze Microsoft][planning-guide-3.2], konto usługi Azure Storage nie zapewnia nieograniczony zasoby pod względem liczby operacji We/Wy, operacje We/Wy i ilości danych. Zazwyczaj systemu DBMS na maszynach wirtualnych są najbardziej zagrożone to. Może być najlepiej użyć oddzielnego konta magazynu dla każdej maszyny Wirtualnej, jeśli masz kilka dużej liczby operacji We/Wy maszyn wirtualnych, aby wdrożyć, aby pozostać w ramach limitu woluminu konta usługi Azure Storage. W przeciwnym razie chcesz zobaczyć, jak mogą równoważyć procesy te maszyny wirtualne między różnymi kontami magazynu bez osiągnięciu limitu każdego pojedynczego konta magazynu. Szczegółowe informacje zostały omówione w [przewodnik wdrażania systemu DBMS][dbms-guide]. Należy również zachować te ograniczenia należy pamiętać o czyste aplikacji SAP maszyn wirtualnych z serwera lub innych maszyn wirtualnych, które ostatecznie mogą wymagać dodatkowych wirtualnych dysków twardych. Te ograniczenia nie są stosowane, jeśli używasz dysków zarządzanych. Jeśli planowane jest używanie usługi Premium Storage, zaleca się przy użyciu dysków zarządzanych.

Innego tematu, który jest odpowiedni dla kont magazynu jest to, czy wirtualne dyski twarde na koncie magazynu są objęte replikacją geograficzną. Replikacja geograficzna jest włączone lub wyłączone na poziomie konta magazynu, a nie na poziomie maszyny Wirtualnej. Jeśli włączono replikację geograficzną, wirtualne dyski twarde na koncie magazynu będą replikowane do innego centrum danych platformy Azure, w tym samym regionie. Przed podjęciem decyzji o tym, należy rozważać następujące ograniczenia:

Replikacja geograficzna platformy Azure działa lokalnie na poszczególnych wirtualnych dysków Twardych na maszynie wirtualnej i nie jest replikowany z systemem IOs w kolejności chronologicznej w wielu dysków VHD na maszynie wirtualnej. W związku z tym wirtualnego dysku twardego, który reprezentuje podstawowej maszyny Wirtualnej, jak również wszelkie dodatkowe wirtualnych dysków twardych dołączonych do maszyny Wirtualnej są replikowane od siebie niezależne. Oznacza to, że jest brak synchronizacji między zmianami w różnych wirtualnych dysków twardych. Fakt, że systemu IOs zostały zreplikowane niezależnie od kolejności, w którym są one zapisywane oznacza, że ten replikacja geograficzna nie jest wartość dla serwerów bazy danych, które mają swoje bazy danych, rozproszone na wielu dysków VHD. Oprócz DBMS również mogą istnieć inne aplikacje, których procesów zapisu lub wykonywać operacje na danych w różnych wirtualnych dysków twardych i gdzie ważne jest, aby zachować kolejność zmiany. Jeśli jest to wymagane, nie można włączyć replikacji geograficznej na platformie Azure. Zależne od tego, czy potrzebujesz lub mają replikacji geograficznej dla zestawu maszyn wirtualnych, ale nie do innego zestawu, umożliwia już klasyfikowanie maszyn wirtualnych i ich powiązane wirtualnych dysków twardych do różnych kont magazynu, które mają replikację geograficzną, włączone lub wyłączone.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Ustawienie automatycznej instalacji dla dołączonych dysków
---
> ![Windows][Logo_Windows] Windows
>
> Dla maszyn wirtualnych, które są tworzone na podstawie własnych obrazów lub dyski, należy sprawdzić i ewentualnie Ustaw parametr automatycznej instalacji. Ustawienie tego parametru umożliwi maszyny Wirtualnej po ponownym uruchomieniu lub ponowne wdrożenie na platformie Azure, aby automatycznie ponownie zainstalować dyski dołączone zainstalowany.
> Parametr jest ustawiony na samych obrazach udostępnianych przez firmę Microsoft w witrynie Azure Marketplace.
>
> Aby można było ustawić automatycznej instalacji, sprawdź dokumentację tutaj wiersza polecenia diskpart.exe pliku wykonywalnego:
>
> * [Opcje wiersza polecenia narzędzia DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatycznej instalacji](https://technet.microsoft.com/library/cc753703.aspx)
>
> Windows, które powinny zostać otwarte okno wiersza polecenia jako administrator.
>
> Jeśli są dołączone dyski, należy do logowania się do maszyny Wirtualnej, aby otworzyć Menedżera dysków Windows. Jeśli nie włączono automatycznej instalacji, zgodnie z zaleceniami w rozdziale [ustawienie automatycznej instalacji dla dołączone dyski][planning-guide-5.5.3], nowo dołączony woluminu > nie trzeba wykonywać w trybie online i jest zainicjowane.
>
> ![Linux][Logo_Linux] Linux
>
> Należy zainicjować nowo dołączona pusty dysk, zgodnie z opisem w [w tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Należy również dodawanie nowych dysków do / etc/fstab.
>
>

---
### <a name="final-deployment"></a>Ostateczny wdrożenia

Ostateczny wdrożenia i konkretne kroki, szczególnie w odniesieniu do wdrożenia SAP rozszerzone monitorowanie, można znaleźć [przewodnik wdrażania][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Uzyskiwanie dostępu do systemów SAP w ramach maszyn wirtualnych platformy Azure

W przypadku scenariuszy, w którym chcesz nawiązać połączenia z tych systemów SAP w publicznej sieci internet, przy użyciu graficznego interfejsu użytkownika SAP, należy zastosować następujące procedury.

W dalszej części dokumentu omówimy innych głównych scenariuszy nawiązywania połączenia z systemów SAP w przypadku wdrożeń obejmujących wiele lokalizacji połączenia lokacja lokacja (s2s) lub połączenia usługi Azure ExpressRoute między systemami lokalnymi a systemami platformy Azure.

### <a name="remote-access-to-sap-systems"></a>Zdalny dostęp do systemów SAP

Za pomocą usługi Azure Resource Manager istnieją domyślne punkty końcowe nie już podobnie jak w poprzedniej wersji portalu klasycznego modelu. Wszystkie porty Maszynie wirtualnej platformy Azure Resource Manager są otwarte tak długo, jak:

1. Nie sieciowej grupy zabezpieczeń jest zdefiniowany dla podsieci lub interfejsu sieciowego. Ruch sieciowy do maszyn wirtualnych platformy Azure mogą być chronione za pomocą tak zwane "sieciowe grupy zabezpieczeń". Aby uzyskać więcej informacji, zobacz [co to jest grupa zabezpieczeń sieci (NSG)?][virtual-networks-nsg]
2. Brak usługi równoważenia obciążenia Azure jest zdefiniowany dla interfejsu sieciowego   

Zobacz architektury różnica między klasycznym modelem i ARM, zgodnie z opisem w [w tym artykule][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfiguracja systemu SAP i SAP graficznego interfejsu użytkownika łączności przez internet

Można znaleźć w tym artykule opisano szczegóły, aby w tym temacie: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Zmiana ustawień zapory na maszynie wirtualnej

Może być konieczne skonfigurowanie zapory na maszynach wirtualnych, aby zezwolić na ruch przychodzący do systemu SAP.

---
> ![Windows][Logo_Windows] Windows
>
> Domyślnie Zapora Windows w ramach platformy Azure wdrożonej maszyny Wirtualnej jest włączona. Teraz musisz zezwolić na użycie portu SAP ma zostać otwarty, w przeciwnym razie SAP graficznego interfejsu użytkownika nie będzie można połączyć.
> W tym celu:
>
> * Otwórz z kontroli Panel\System i zapory Security\Windows **Zaawansowane ustawienia**.
> * Teraz kliknij prawym przyciskiem myszy na reguły ruchu przychodzącego i wybrać **nową regułę**.
> * W Kreatorze następujące wybrany do utworzenia nowego **portu** reguły.
> * W następnym kroku kreatora pozostaw ustawienie TCP i wpisz numer portu, który chcesz otworzyć. Ponieważ identyfikator wystąpienia naszej SAP 00, Skorzystaliśmy 3200. Jeśli wystąpienie ma numer innego wystąpienia, powinien zostać otwarty port zdefiniowanych przez Ciebie wcześniej zgodnie z liczbą wystąpień.
> * W następnej części tego kreatora, należy pozostawić element **Zezwalaj na połączenia** zaznaczone.
> * W następnym kroku w kreatorze należy zdefiniować, czy reguła dotyczy sieci domeny, prywatne i publiczne. Dostosuj ją w razie potrzeby do Twoich potrzeb. Jednak połączenie z interfejsem GUI SAP z zewnątrz za pośrednictwem publicznej sieci, musisz mieć reguły będą stosowane do publicznej sieci.
> * W ostatnim kroku kreatora nazwę reguły i zapisać, naciskając klawisz **Zakończ**.
>
> Reguła zaczyna obowiązywać natychmiast.
>
> ![Definicja reguły portu][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Obrazy systemu Linux w witrynie Azure Marketplace nie należy włączać zaporę iptables domyślnie i połączenia z systemem SAP powinny działać. Jeśli włączono iptables lub inna zapora, zapoznaj się z dokumentacją programu iptables lub używanych zapory, aby zezwolić na ruch przychodzący protokołu tcp do portu 32xx (gdzie xx jest numer systemu systemu SAP).
>
>

---
#### <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Graficzny interfejs użytkownika SAP nie natychmiast połączyć się z dowolnego z wystąpień SAP (port 32xx), które są uruchomione, ale najpierw łączy się za pośrednictwem portu otwarty do procesu serwera SAP komunikat (port 36xx). W przeszłości tego samego portu został użyty przez serwer wiadomości do komunikacji wewnętrznej w wystąpieniach aplikacji. Aby zapobiec przypadkowo podczas komunikowania się z serwerem wiadomości na platformie Azure serwerów aplikacji w środowisku lokalnym, można zmienić portów komunikacji wewnętrznej. Zdecydowanie zaleca się zmienić wewnętrznej komunikacji między serwerem komunikat SAP i jego wystąpienia aplikacji na inny numer portu w systemach, które zostały sklonowane z systemów lokalnych, takich jak klon opracowywania rozwiązań dla projektów testowych itp. Można to zrobić przy użyciu domyślnego parametru profilu:

> rdisp/msserv_internal
>
>

zgodnie z opisem w [ustawienia zabezpieczeń dla serwera SAP wiadomości](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Pojedyncza maszyna wirtualna z oprogramowaniem SAP NetWeaver pokaz/szkolenia scenariusza

![Systemami pojedynczego SAP maszyny Wirtualnej pokazu za pomocą tej samej nazwy maszyn wirtualnych, samodzielnie w usługach Azure Cloud Services][planning-guide-figure-1700]

W tym scenariuszu firma Microsoft wdraża typowe szkolenia/pokaz scenariusz systemu, gdzie scenariusz pełną szkolenia/wersję demonstracyjną znajduje się w pojedynczej maszyny Wirtualnej. Przyjęto założenie, że wdrożenie jest przeprowadzane za pomocą szablonów obrazu maszyny Wirtualnej. Przyjęto również założenie, że wiele z tych wersji demonstracyjnej/szkoleniach konieczność maszyny wirtualne można wdrażać maszyny wirtualne o takiej samej nazwie. Systemy całego szkolenia nie ma łączności z zasobami lokalnymi i są przeciwieństwem do wdrożenia hybrydowego.

Zakłada się, utworzyć obraz maszyny Wirtualnej, zgodnie z opisem w sekcjach rozdziału [przygotowanie maszyn wirtualnych z oprogramowaniem SAP na platformie Azure] [ planning-guide-5.2] w tym dokumencie.

Kolejność zdarzeń, aby zaimplementować scenariusz wygląda następująco:

##### <a name="powershell"></a>PowerShell

* Utwórz nową grupę zasobów dla każdego pozioma szkolenia/pokaz

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Utwórz nowe konto magazynu, jeśli nie chcesz używać usługi Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Utwórz nową sieć wirtualną dla co pozioma szkolenia/pokaz umożliwić użycie tej samej nazwy hosta i adresów IP. Sieć wirtualna jest chroniona przez sieciową grupę zabezpieczeń, która tylko zezwala na ruch do portu 3389, umożliwiając dostęp do usług pulpitu zdalnego i port 22 dla protokołu SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Tworzenie nowego publicznego adresu IP, którego można uzyskać dostęp do maszyny wirtualnej z Internetu

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Tworzenie nowego interfejsu sieciowego dla maszyny wirtualnej

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Tworzy maszynę wirtualną. W tym scenariuszu każda maszyna wirtualna będzie mieć taką samą nazwę. Identyfikator SID SAP wystąpień oprogramowania SAP NetWeaver na tych maszynach wirtualnych będą takie same jak również. W ramach grupy zasobów platformy Azure musi być unikatowa nazwa maszyny Wirtualnej, ale w różnych grupach zasobów platformy Azure można uruchomić maszyny wirtualne o takiej samej nazwie. Domyślnego konta "Administrator" Windows lub "root" dla systemu Linux nie są prawidłowe. W związku z tym nową nazwę użytkownika administratora musi być zdefiniowany hasła. Rozmiar maszyny Wirtualnej musi być zdefiniowany.

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

* Opcjonalnie dodanie dodatkowych dysków i przywrócić niezbędną zawartość. Wszystkie nazwy obiektów blob (adresy URL, do obiektów blob) musi być unikatowa w obrębie platformy Azure.

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

Poniższy przykład kodu może służyć w systemie Linux. Dla Windows, zgodnie z powyższym opisem przy użyciu programu PowerShell lub dostosować przykład użycia rgName % zamiast $rgName i ustawić zmienną środowiskową przy użyciu polecenia Windows *ustaw*.

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

* Utwórz nową sieć wirtualną dla co pozioma szkolenia/pokaz umożliwić użycie tej samej nazwy hosta i adresów IP. Sieć wirtualna jest chroniona przez sieciową grupę zabezpieczeń, która tylko zezwala na ruch do portu 3389, umożliwiając dostęp do usług pulpitu zdalnego i port 22 dla protokołu SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Tworzenie nowego publicznego adresu IP, którego można uzyskać dostęp do maszyny wirtualnej z Internetu

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Tworzenie nowego interfejsu sieciowego dla maszyny wirtualnej

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Tworzy maszynę wirtualną. W tym scenariuszu każda maszyna wirtualna będzie mieć taką samą nazwę. Identyfikator SID SAP wystąpień oprogramowania SAP NetWeaver na tych maszynach wirtualnych będą takie same jak również. W ramach grupy zasobów platformy Azure musi być unikatowa nazwa maszyny Wirtualnej, ale w różnych grupach zasobów platformy Azure można uruchomić maszyny wirtualne o takiej samej nazwie. Domyślnego konta "Administrator" Windows lub "root" dla systemu Linux nie są prawidłowe. W związku z tym nową nazwę użytkownika administratora musi być zdefiniowany hasła. Rozmiar maszyny Wirtualnej musi być zdefiniowany.

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

* Opcjonalnie dodanie dodatkowych dysków i przywrócić niezbędną zawartość. Wszystkie nazwy obiektów blob (adresy URL, do obiektów blob) musi być unikatowa w obrębie platformy Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Szablon

Można użyć przykładowych szablonów w repozytorium azure-quickstart-templates w witrynie GitHub.

* [Proste maszyny Wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Proste Windows maszyny Wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Maszynę Wirtualną z obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementowanie zestawu maszyn wirtualnych, które komunikują się w obrębie platformy Azure

Tego scenariusza hybrydowego niż jest to typowy scenariusz, szkolenia i pokaz do celów gdzie oprogramowania reprezentujący pokaz/szkolenia scenariusza jest rozłożona na wiele maszyn wirtualnych. Różne składniki, które są zainstalowane na różnych maszynach wirtualnych muszą komunikować się ze sobą. Ponownie w tym scenariuszu komunikacja sieciowa braku lokalnej lub scenariuszy obejmujących wiele lokalizacji jest wymagana.

Ten scenariusz jest rozszerzeniem instalacji opisane w rozdziale [pojedyncza maszyna wirtualna z oprogramowaniem SAP NetWeaver pokaz/szkolenia scenariusza] [ planning-guide-7.1] tego dokumentu. W tym przypadku jedna maszyna wirtualna zostanie dodany do istniejącej grupy zasobów. W poniższym przykładzie pozioma szkolenia składa się z SAP ASCS/SCS maszynę Wirtualną, maszynę Wirtualną z systemem DBMS i wystąpienia serwera aplikacji SAP maszyny Wirtualnej.

Przed utworzeniem tego scenariusza, należy wziąć pod uwagę ustawienia podstawowe, jak już wykonywane w scenariuszu przed.

#### <a name="resource-group-and-virtual-machine-naming"></a>Grupa zasobów i nazwy maszyny wirtualnej

Wszystkie nazwy grupy zasobów musi być unikatowa. Twórz własny schemat nazewnictwa zasobów, takich jak `<rg-name`>-sufiks.

Nazwa maszyny wirtualnej musi być unikatowa w obrębie grupy zasobów.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurowanie sieci dla komunikacji między różnych maszyn wirtualnych

![Zestaw maszyn wirtualnych w ramach sieci wirtualnej platformy Azure][planning-guide-figure-1900]

Aby zapobiec nazw kolizji z klony tego samego krajobrazów szkolenia/pokaz, musisz utworzyć usługi Azure Virtual Network dla każdego poziomym. Rozpoznawanie nazw DNS będzie świadczona przez platformę Azure lub własnego serwera DNS, poza systemem Azure (nie w celu dalszego dokładniej omówione w tym miejscu) można skonfigurować. W tym scenariuszu firma Microsoft nie Konfiguruj własną DNS. W przypadku wszystkich maszyn wirtualnych w jednej sieci wirtualnej platformy Azure komunikację za pomocą nazwy hostów zostaną włączone.

Powody do oddzielania szkolenia lub pokaz krajobrazów, sieciami wirtualnymi i nie tylko grupy zasobów może być:

* Środowisko SAP, jak skonfigurować musi swój własny AD/OpenLDAP i serwer domeny musi być częścią wszystkich zapewniały realizację niezbędnych zadań.  
* Środowisko SAP, jak skonfigurować zawiera składniki, które są potrzebne do pracy stałe adresy IP.

Więcej informacji o usłudze Azure Virtual Networks i sposób definiowania ich można znaleźć w [w tym artykule][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Wdrażanie maszyn wirtualnych SAP z połączeniem sieci firmowej (Cross-Premises)

Uruchom środowisko SAP i dzielenia wdrożenia od zera dla serwerów z systemem DBMS wysokiej klasy, dla zwirtualizowanych środowisk lokalnych dla warstw aplikacji i niższy poziom 2 skonfigurowane systemów SAP i IaaS platformy Azure. Przyjmowane jest założenie podstawowych systemów SAP w jednym środowiskiem SAP muszą komunikować się ze sobą i z wieloma innymi składnikami oprogramowania wdrożone w firmie, niezależnie od ich formularza wdrożenia. Również powinno być żadnych różnic wprowadzone przez formularz wdrożenia dla użytkownika końcowego, łączenie z interfejsem GUI SAP lub innych interfejsów. Te warunki mogą zostać spełnione tylko, gdy mamy Active Directory/OpenLDAP lokalnych i usług DNS rozszerzona na systemy platformy Azure za pośrednictwem połączenia lokacja do witryny/wielu lokalizacji lub prywatnych połączeń, takich jak usługi Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenariusz środowisko SAP

Między lokalizacjami lub hybrydowy scenariusz może być mniej więcej opisany takich jak w poniższej grafice:

![Połączenie lokacja-lokacja między magazynami lokalnymi i zasobów platformy Azure][planning-guide-figure-2100]

Scenariusz powyżej opisano scenariusz, gdzie w środowisku lokalnym

Minimalnym wymaganiem jest użycie protokołów bezpiecznej komunikacji, takich jak protokół SSL/TLS dla opartych na sieci VPN połączeń, aby uzyskać dostęp do systemu usług platformy Azure lub dostęp za pomocą przeglądarki. Zakłada się, firm inaczej obsługi połączenia sieci VPN między siecią firmową i platformy Azure. Niektóre firmy może zostać otwarta blankly wszystkie porty. Niektóre innych firm może być była precyzyjna, w które porty należy otworzyć itp.

W tabeli poniżej SAP typowe porty komunikacyjne są wyświetlane. Zasadniczo jest wystarczające, aby otworzyć port bramy SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Usługa | Nazwa portu | Przykład `<nn`> = 01 | Domyślny zakres (minimum maksimum) | Komentarz |
| --- | --- | --- | --- | --- |
| Dyspozytor |sapdp`<nn>` zobacz * |3201 |3200 - 3299 |Dyspozytor SAP, SAP graficznego interfejsu użytkownika dla Windows i języka Java |
| Serwer komunikatów |sapms`<sid`> zobacz ** |3600 |bezpłatne sapms`<anySID`> |Identyfikator SID = Identyfikatorem systemu SAP |
| Brama |sapgw`<nn`> zobacz * |3301 |Bezpłatne |Bramy SAP, używany do komunikacji CPIC i RFC |
| SAP router |sapdp99 |3299 |Bezpłatne |Tylko nazwy usług ciągłej integracji (centralna wystąpienia), można je przypisać w /etc/services do dowolnych wartości po zakończeniu instalacji. |

*) nn = numer wystąpienia SAP

*) identyfikator sid = Identyfikatorem systemu SAP

Bardziej szczegółowe informacje dotyczące portów wymaganych dla różnych produktów SAP lub usług według produktów SAP można znaleźć tutaj <https://scn.sap.com/docs/DOC-17124>.
Z tym dokumentem można otworzyć porty dedykowanych w niezbędne dla określonych produkty i scenariusze SAP urządzenia sieci VPN.

Inne zabezpieczeń mierzy podczas wdrażania maszyn wirtualnych w takiej sytuacji można utworzyć [sieciowej grupy zabezpieczeń] [ virtual-networks-nsg] do zdefiniowania reguł dostępu.

### <a name="dealing-with-different-virtual-machine-series"></a>Zajmowanie się innej serii maszyny wirtualnej

Firma Microsoft dodała wiele więcej typów maszyn wirtualnych, które różnią się w liczba wirtualnych procesorów CPU, pamięci, lub ważniejsze na sprzęcie, którym jest uruchomiony. Nie wszystkie te maszyny wirtualne są obsługiwane z oprogramowaniem SAP (zobacz obsługiwane typy maszyn wirtualnych w Uwaga SAP [1928533]). Niektóre z tych maszyn wirtualnych uruchamiane na inny host generacji sprzętu. Te generacji sprzętu hosta wdrożonych wprowadzenie w stopień szczegółowości z platformy Azure — jednostka skalowania. Przypadki mogą wystąpić, gdy różnych typach maszyn wirtualnych, które wybrałeś nie można uruchomić w tej samej jednostce skali. Zestaw dostępności, jest ograniczona możliwość span jednostek skalowania w zależności od innego sprzętu.  Na przykład jeśli korzystasz z warstwy systemu SAP DBMS na maszynie Wirtualnej E64s_v3, który znajduje się w zestawie dostępności wraz z maszyną Wirtualną uruchomione dodatkowej wystąpienie DBMS w konfiguracji wysokiej dostępności, możesz nie wystarczy zatrzymać i ponownie uruchomić pomocniczej maszyny Wirtualnej jako maszyna wirtualna serii M ponieważ warto u aktualnij maszyny Wirtualnej. Przyczyną jest to, że maszyny wirtualne serii M i maszyny wirtualne z serii Ev3 są uruchomione na różnym sprzęcie i korzystając z niego w różnych jednostkach skalowania. Musisz utworzyć nowy zestaw dostępności, Usuń pomocniczej maszyny Wirtualnej serii Ev3 bez usuwania magazynu i ponowne wdrażanie maszyny Wirtualnej jako maszyna wirtualna serii M do nowego zestawu dostępności.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Drukowanie na drukarce lokalnej sieci z wystąpienia SAP na platformie Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Drukowanie za pośrednictwem protokołu TCP/IP w scenariuszu między środowiskami lokalnymi

Skonfigurowanie lokalnych drukarek sieciowych na podstawie protokołu TCP/IP w Maszynie wirtualnej platformy Azure ogólnie jest taki sam jak w sieci firmowej, przy założeniu, że masz tunelu sieci VPN lokacja-lokacja lub nawiązano połączenie usługi ExpressRoute.

---
> ![Windows][Logo_Windows] Windows
>
> W tym celu:
>
> * Niektóre drukarki sieciowej są dostarczane z Kreatora konfiguracji, który ułatwia konfigurowanie drukarki w Maszynie wirtualnej platformy Azure. Jeśli żadne oprogramowanie kreator został rozesłany drukarki, ręczne sposób konfigurowania drukarki jest do utworzenia nowego portu drukarki TCP/IP.
> * Otwórz Panel sterowania -> urządzenia i drukarki -> Dodaj drukarkę
> * Kliknij przycisk Dodaj drukarki za pomocą adresu TCP/IP lub nazwy hosta
> * Wpisz adres IP drukarki
> * Portu drukarki 9100 standardowe
> * W razie potrzeby ręcznie zainstaluj odpowiedni sterownik drukarki.
>
> ![Linux][Logo_Linux] Linux
>
> * takich jak dla Windows po prostu wykonaj standardową procedurę instalacji drukarki sieciowej
> * po prostu wykonaj publicznych przewodniki systemu Linux dla [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) lub [Red Hat i Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) na temat sposobu dodawania drukarki.
>
>

---
![Drukowanie w sieci][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Drukarki oparte na hoście przy użyciu protokołu SMB (drukarki udostępnionej) w scenariuszu między środowiskami lokalnymi

Drukarki oparte na hoście nie są zgodne z sieci zgodnie z projektem. Ale drukarki oparte na hoście mogą być współużytkowane przez komputery w sieci, tak długo, jak drukarka jest podłączona do zasilania na komputerze. Łączenie sieci firmowej sieci lokacja-lokacja lub ExpressRoute i udostępnianie drukarki lokalnej. Protokół SMB używa NetBIOS zamiast DNS jako nazwa usługi. Nazwę NetBIOS hosta może różnić się od nazwy hosta DNS. Standardowa przypadek jest nazwą hosta NetBIOS i nazwę hosta DNS są identyczne. Domena DNS nie ma sensu w przestrzeni nazw NetBIOS. W związku z tym w pełni kwalifikowana nazwa hosta DNS, składające się z nazwy hosta DNS i domeny DNS nie stosuje się w przestrzeni nazw NetBIOS.

Udziału drukarki jest identyfikowane przez unikatową nazwę w sieci:

* Nazwa hosta hosta protokołu SMB (zawsze wymagane).
* Nazwa udziału (zawsze wymagane).
* Nazwa domeny drukarki, któremu udostępniono nie jest w tej samej domenie co SAP system.
* Ponadto nazwa użytkownika i hasło może być konieczne dostępu do udziału drukarki.

Instrukcje:

---
> ![Windows][Logo_Windows] Windows
>
> Udostępnianie drukarki lokalnej.
> W maszynie Wirtualnej platformy Azure Otwórz Eksploratora Windows i wpisz nazwę udziału drukarki.
> Kreator instalacji drukarki przeprowadzi Cię przez proces instalacji.
>
> ![Linux][Logo_Linux] Linux
>
> Poniżej przedstawiono kilka przykładów dokumentację na temat konfigurowania drukarek sieciowych w systemie Linux lub w tym rozdziale dotyczące drukowania w systemie Linux. Będzie ona działają tak samo w maszynie Wirtualnej systemu Linux platformy Azure, tak długo, jak maszyna wirtualna jest częścią sieci VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL lub Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB drukarki (drukarki przekazywania)

Na platformie Azure możliwości usług pulpitu zdalnego do udzielania dostępu do swoich urządzeń drukarki lokalnej w sesji zdalnej nie jest dostępna.

---
> ![Windows][Logo_Windows] Windows
>
> Szczegółowe informacje na temat Drukowanie z Windows można znaleźć tutaj: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integracja oprogramowania SAP systemy platformy Azure do korekcji i Transport systemu (TMS) między środowiskami lokalnymi

Zmiana SAP i System transportu (TMS) musi być skonfigurowana do eksportowania i importowania transportu żądania we wszystkich systemach w orientacji poziomej. Przyjęto założenie, że wystąpienia rozwoju systemu SAP (DEV) znajdują się na platformie Azure, systemów produktywność (PRD) i zapewniania jakości (QA) są w środowisku lokalnym. Ponadto przyjęto założenie, że jest katalogiem transportu centralnego.

##### <a name="configuring-the-transport-domain"></a>— Konfigurowanie domeny transportu

Skonfiguruj domenę transportu w systemie wyznaczony jako kontroler domeny transportu, zgodnie z opisem w [konfigurowania kontrolera domeny transportu](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Użytkownik systemu TMSADM zostanie utworzony i wymagane miejsce docelowe RFC zostanie wygenerowany. Można sprawdzać te połączenia RFC przy użyciu transakcji SM59. Rozpoznawanie nazwy hosta musi być włączona w Twojej domenie transportu.

Instrukcje:

* W naszym scenariuszu zdecydowaliśmy się, że i lokalnym systemem QAS CTS kontrolera domeny. Wywołaj STMS transakcji. Zostanie wyświetlone okno dialogowe TMS. Zostanie wyświetlone okno dialogowe Konfigurowanie domeny transportu. (To okno dialogowe pojawia się tylko, jeśli nie skonfigurowano jeszcze domeny transportu.)
* Upewnij się, czy jest autoryzowany użytkownik automatycznie utworzone TMSADM (SM59 -> połączenie ABAP -> TMSADM@E61.DOMAIN_E61 -> Szczegóły -> Utilities(M) -> Test autoryzacji). Ekran początkowy transakcji STMS powinien być wyświetlony czy ten System SAP teraz działa jako kontroler domeny transportu jak pokazano poniżej:

![Ekran początkowy transakcji STMS na kontrolerze domeny][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>W tym systemów SAP w domenie transportu

Sekwencja, w tym z systemem SAP w domenie transportu wygląda następująco:

* W systemie Deweloperskim na platformie Azure przejdź do systemu transportu (klient 000) i wywoływać transakcji STMS. Wybierz inne konfigurację z okna dialogowego, a następnie kontynuuj obejmują systemu w domenie. Określ kontroler domeny jako hosta docelowego ([w tym systemów SAP w domenie transportu](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). System oczekuje się teraz do uwzględnienia w domenie transportu.
* Ze względów bezpieczeństwa należy następnie wróć do kontrolera domeny, aby upewnić się, Twoje żądanie. Wybierz pozycję Przegląd systemu i zatwierdź systemu oczekiwania. Potwierdź będą dystrybuowane monit i konfiguracji.

Ten system SAP teraz zawiera niezbędne informacje o wszystkich innych systemów SAP w domenie transportu. W tym samym czasie dane nowego systemu SAP są wysyłane do innych systemów SAP i SAP system jest wprowadzana w profilu transportu programu kontrolki transportu. Sprawdź, czy dokumenty RFC i dostęp do katalogu transportu domeny działa.

Kontynuuj konfiguracji systemu transportu w zwykły sposób zgodnie z opisem w dokumentacji [zmian i System transportu](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Instrukcje:

* Upewnij się, że Twoje STMS w środowisku lokalnym jest poprawnie skonfigurowana.
* Upewnij się, że nazwę hosta kontrolera domeny, Transport, może zostać rozpoznany przez maszyny wirtualnej na visa platformy Azure i odwrotnie.
* Wywołanie transakcji STMS -> inne konfiguracji -> obejmują systemu w domenie.
* Upewnij się, połączenie na systemu TMS lokalnie.
* Skonfiguruj trasy transportu, grup i warstw w zwykły sposób.

W połączonych lokacja lokacja między wieloma lokalizacjami scenariuszy, opóźnienie między lokalną i platformą Azure nadal może być istotne. Jeśli postępujemy zgodnie z sekwencji transportu obiektów w systemach deweloperskie i testowe do środowiska produkcyjnego lub Pomyśl o zastosowaniu transportu lub pakietów pomocy technicznej do różnych systemów, okazuje się, że zależą od lokalizacji katalogu transportu centralnego Niektóre systemy będą napotykać duże opóźnienie odczytu lub zapisu danych w katalogu centralnym transportu. Sytuacja jest podobny do SAP poziomym konfiguracji, gdzie różnych systemów rozprzestrzenia się w różnych centrach danych za pomocą znacznej odległość między centrami danych.

Aby obejść takie opóźnienie i systemami działać szybko podczas zapisu lub odczytu do lub z katalogu transportu, można skonfigurować dwie domeny transportu STMS (jeden dla środowiska lokalnego i jeden z systemów na platformie Azure i połączyć domen transportu. Sprawdź, czy niniejszej dokumentacji opisano zasady dotyczące tę koncepcję w SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Instrukcje:

* Konfigurowanie domeny transportu w poszczególnych lokalizacjach (w środowisku lokalnym i platformą Azure) przy użyciu transakcji STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Link domen z linkiem umożliwiającym domeny i upewnij się, łącze między dwiema domenami.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Przekazują konfigurację do połączonego systemu.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC ruchu między wystąpieniami platformy SAP znajdujących się na platformie Azure i w środowisku lokalnym (Cross-Premises)

RFC ruchu między systemami, które są w środowisku lokalnym i na platformie Azure potrzebne do pracy. Aby skonfigurować połączenie transakcji wywołania SM59 w systemie źródłowym gdzie należy zdefiniować połączeniu RFC do systemu docelowego. Konfiguracja jest podobna do ustawienia standardowe połączenia RFC.

Firma Microsoft przyjęto założenie, że w tym scenariuszu obejmujących maszyny wirtualne, które uruchamiania systemów SAP, które muszą komunikować się ze sobą znajdują się w tej samej domenie. W związku z tym instalacji połączeniu RFC między systemami SAP nie różni się od kroków instalacji i dane wejściowe w scenariuszach lokalnych.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Uzyskiwanie dostępu do lokalnych udziałów plików z wystąpieniami platformy SAP znajdujących się na platformie Azure lub na odwrót

Wystąpienia SAP znajdujących się na platformie Azure konieczne dostęp do udziałów plików, które znajdują się w siedzibie firmy. Ponadto w środowisku lokalnym wystąpieniami platformy SAP konieczne dostęp do udziałów plików, które znajdują się na platformie Azure. Aby włączyć udziałów plików, należy skonfigurować uprawnienia i opcje udostępniania w systemie lokalnym. Upewnij się otworzyć porty dla połączenia sieci VPN lub usługi ExpressRoute między platformą Azure i Twoim centrum danych.

## <a name="supportability"></a>Możliwości obsługi

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure rozwiązanie do monitorowania dla rozwiązania SAP

Aby włączyć monitorowanie Najważniejsze systemy SAP na platformie Azure SAP monitorowanie narzędzi SAPOSCOL lub Agent hosta SAP uzyskać danych poza hosta usługi maszyny wirtualnej platformy Azure za pomocą rozszerzenia monitorowania platformy Azure dla rozwiązania SAP. Ponieważ żądania przez firmę SAP były specyficzne dla aplikacji SAP, Microsoft zrezygnowała z ogólną implementacji wymaganej funkcji na platformie Azure, ale pozostawienie jej klienci będą mogli wdrażać wymagane składniki monitorowania i konfiguracje w ich wirtualnej Komputery działające na platformie Azure. Jednak wdrożenie i zarządzanie cyklem życia składników monitorowania będzie najczęściej zautomatyzowany przez platformę Azure.

#### <a name="solution-design"></a>Projekt rozwiązania

Rozwiązanie opracowany, aby włączyć monitorowanie SAP zależy od architektury agenta maszyny Wirtualnej platformy Azure i platformy rozszerzeń. Chodzi agenta maszyny Wirtualnej platformy Azure i rozszerzenie platformy o to, aby zezwolić na instalację aplikacji oprogramowania dostępnych w galerii rozszerzenia maszyny Wirtualnej platformy Azure na maszynie wirtualnej. Zasada idei to pojęcie jest umożliwienie (w takich przypadkach rozszerzenie monitorowania platformy Azure dla oprogramowania SAP), wdrożenie specjalne funkcje do maszyny Wirtualnej i konfiguracji oprogramowania w czasie wdrażania.

"Azure agenta maszyny Wirtualnej" umożliwiający obsługę określonych rozszerzeń maszyn wirtualnych platformy Azure na maszynie wirtualnej są wstrzykiwane do maszyn wirtualnych Windows domyślnie po utworzeniu maszyny Wirtualnej w witrynie Azure portal. W przypadku SUSE, Red Hat lub Oracle Linux agent maszyny Wirtualnej jest już częścią obrazu z witryny Azure Marketplace. W przypadku, gdy jeden czy przekazać Maszynę wirtualną systemu Linux ze środowiska lokalnego do platformy Azure, agent maszyny Wirtualnej musi być zainstalowany ręcznie.

Podstawowe bloki konstrukcyjne rozwiązania monitorowania na platformie Azure w ramach SAP wygląda podobnie do następującego:

![Składniki programu Microsoft Azure rozszerzenia][planning-guide-figure-2400]

Jak pokazano na powyższym diagramie bloku, jedną część rozwiązania do monitorowania dla rozwiązania SAP znajduje się w obrazie maszyny Wirtualnej platformy Azure i galerii rozszerzeń platformy Azure, która jest globalnie replikowanego repozytorium, który jest zarządzany przez operacje usługi Azure. Jest odpowiedzialny za wspólnego zespołu SAP/MS nad Azure wdrożenia SAP do pracy z operacjami platformy Azure, publikowanie nowych wersji rozszerzenia monitorowania platformy Azure dla rozwiązania SAP.

Podczas wdrażania nowej maszyny Wirtualnej Windows Agent maszyny Wirtualnej platformy Azure są automatycznie dodawane do maszyny Wirtualnej. Funkcja tego agenta jest koordynowanie ładowanie i konfiguracji rozszerzeń Azure do monitorowania SAP NetWeaver systemów. W przypadku maszyn wirtualnych systemu Linux Agent maszyny Wirtualnej platformy Azure jest już częścią obrazu systemu operacyjnego platformy Azure Marketplace.

Jednak jest krokiem, który musi być wykonywane przez klienta. Jest to włączania i konfigurowania kolekcji danych wydajności. Proces związane z konfiguracją jest automatycznego przez skrypt programu PowerShell lub interfejsu wiersza polecenia. Skrypt programu PowerShell można pobrać w Azure Microsoft Script Center zgodnie z opisem w [przewodnik wdrażania][deployment-guide].

Ogólna Architektura rozwiązania do monitorowania platformy Azure dla rozwiązania SAP wygląda następująco:

![Azure rozwiązanie do monitorowania środowiska SAP NetWeaver][planning-guide-figure-2500]

**Aby uzyskać dokładne instrukcje i aby uzyskać szczegółowe instrukcje korzystania z tych poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia podczas wdrażania, postępuj zgodnie z instrukcjami, podany w [przewodnik wdrażania][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integracja usługi Azure znajduje się wystąpienie SAP do SAProuter

Wystąpieniami platformy SAP działające na platformie Azure muszą być dostępne z SAProuter także.

![Połączenie sieciowe routera SAP][planning-guide-figure-2600]

SAProuter umożliwia komunikację protokołu TCP/IP między systemami uczestniczących w przypadku braku bezpośredniego połączenia IP. Zapewnia to tę zaletę, że połączenia end-to-end między partnerami komunikacji jest niezbędne, na poziomie sieci. SAProuter nasłuchuje na porcie 3299 domyślnie.
Do łączenia z wystąpieniami platformy SAP za pośrednictwem SAProuter należy nadać nazwę ciągu i hosta SAProuter z próba nawiązania połączenia.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Do tej pory fokus dokument został oprogramowanie SAP NetWeaver ogólnie rzecz biorąc lub SAP NetWeaver ABAP stosu. W tej sekcji małe są wyświetlane określone informacje dotyczące stosu SAP Java. Jednym z najważniejszych aplikacji SAP NetWeaver wyłącznie opartymi na języku Java jest SAP Enterprise Portal. Inne oprogramowanie SAP NetWeaver aplikacje oparte na jak SAP PI i SAP Menedżerze rozwiązania SAP NetWeaver ABAP i stosy języka Java. W związku z tym bez obaw istnieje potrzeba należy wziąć pod uwagę określone aspekty związane ze stosu SAP NetWeaver w języku Java.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

Instalator portalu SAP w maszynie wirtualnej platformy Azure nie różnią się od w instalacji lokalnej, jeśli są wdrażane w scenariuszach obejmujących wiele lokalizacji. Ponieważ DNS odbywa się przez lokalną, ustawienia portów dla poszczególnych wystąpień może odbywać się jako skonfigurowanych w środowisku lokalnym. Zalecenia i ograniczenia opisane w niniejszym dokumencie do tej pory zastosowanie ogólnie rzecz biorąc dla aplikacji, takich jak SAP Enterprise Portal lub stos SAP NetWeaver w języku Java.

![Portal narażonych SAP][planning-guide-figure-2700]

Scenariusz wdrożenia specjalne przez niektórych klientów jest przed nawiązywaniem bezpośredniego połączenia z portalu dla przedsiębiorstw SAP do Internetu, gdy host maszyny wirtualnej jest połączony z siecią firmową za pośrednictwem tunelu VPN typu lokacja lokacja lub ExpressRoute. Takiej sytuacji należy upewnić się, że określone porty są otwarte i nie jest blokowany przez zapory lub sieciowej grupy zabezpieczeń. 

Portal początkowego identyfikatora URI jest http (s):`<Portalserver`>: 5XX00/irj, gdzie numer portu jest sformułowany, zgodnie z opisem SAP w <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Konfiguracja punktu końcowego][planning-guide-figure-2800]

Jeśli chcesz dostosować adresu URL i/lub porty witryny Enterprise Portal SAP, sprawdź tej dokumentacji:

* [Zmień adres URL portalu](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Zmień domyślne numery portów, numery portów portalu](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Wysoka dostępność (HA) i odzyskiwania po awarii (DR) dla oprogramowania SAP NetWeaver uruchomione na maszynach wirtualnych platformy Azure

### <a name="definition-of-terminologies"></a>Definicja terminologii

Termin **wysokiej dostępności (HA)** powiązany ogólnie jest to zestaw technologii minimalizujące zakłócenia IT, zapewniając ciągłość usług IT za pomocą nadmiarowe, odpornej na uszkodzenia, lub pracy awaryjnej chronione składników wewnątrz **tego samego** centrum danych. W naszym przypadku w ramach jednego regionu platformy Azure.

**Odzyskiwanie po awarii (DR)** jest także przeznaczonych dla zminimalizować zakłócenia usług IT oraz ich odzyskiwanie ale na **różnych** centrów danych, które są zazwyczaj znajduje się setki kilometrów natychmiast. W naszym przypadku zwykle od różnych regionów platformy Azure, w tym samym regionie geograficznymi lub określone przez Ciebie jako klient.

### <a name="overview-of-high-availability"></a>Omówienie wysokiej dostępności

Firma Microsoft można oddzielić dyskusji o SAP o wysokiej dostępności na platformie Azure na dwie części:

* **Wysoka dostępność infrastruktury platformy Azure**, na przykład HA obliczeniowych (maszyn wirtualnych), sieć, Magazyn itp. oraz korzyści dla zwiększenia dostępności aplikacji SAP.
* **Wysoka dostępność aplikacji SAP**, na przykład składniki oprogramowania wysoka dostępność programu SAP:
  * Serwery aplikacji SAP
  * Wystąpienia SAP ASCS/SCS
  * Serwer bazy danych

i jak można ją połączyć z infrastrukturą platformy Azure o wysokiej dostępności.

SAP wysokiej dostępności na platformie Azure ma pewne różnice w porównaniu do SAP o wysokiej dostępności w lokalnym środowisku fizycznym lub wirtualnym. Poniższy dokument z systemu SAP w tym artykule opisano standardowy SAP konfiguracjach wysokiej dostępności w środowiskach zwirtualizowanych na Windows: <https://scn.sap.com/docs/DOC-44415>. Brak zintegrowane sapinst SAP-zaświadczanie o kondycji konfiguracji dla systemu Linux takie jak istnieje Windows. Dotyczące SAP HA lokalnych dla systemu Linux znaleźć artykuły zawierają więcej informacji: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure

Obecnie jest maszyn wirtualnych na jednym umów SLA na poziomie 99,9%. Aby uzyskać pomysł, jak może wyglądać dostępności jednej maszyny Wirtualnej, możesz tworzyć produktu różne dostępne umowy SLA platformy Azure: <https://azure.microsoft.com/support/legal/sla/>.

Podstawą obliczeń jest 30 dni, każdego miesiąca lub 43200 minut. Dlatego przestój 0,05% odnosi się do 21,6 minut. Dostępność różnych usług będą w zwykły sposób, należy pomnożyć w następujący sposób:

(Usługa dostępności #1/100) * (Usługa dostępności #2/100) * (Usługa dostępności #3/100) 

Na przykład:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 lub ogólnej dostępności 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Maszyna wirtualna (VM) o wysokiej dostępności

Istnieją dwa typy zdarzeń platformy Azure, które mogą wpłynąć na dostępność maszyn wirtualnych: Planowana Konserwacja i nieplanowana konserwacja.

* Zdarzenia planowanej konserwacji są okresowe aktualizacje przeprowadzane przez firmę Microsoft dla podstawowej platformy Azure w celu zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy, które są uruchamiane maszyny wirtualne.
* Zdarzenia nieplanowanej konserwacji wystąpić, gdy sprzętu lub infrastruktury fizycznej maszyny wirtualnej wystąpił błąd w jakiś sposób. Mogą być to awarie sieci lokalnej, błędy na dysku lokalnym lub inne awarie na poziomie regału. Po wykryciu awarii tego typu platformy Azure będą automatycznie migrować maszynę wirtualną z złej kondycji serwera fizycznego obsługującego maszynę wirtualną na serwerze fizycznym dobrej kondycji. Te zdarzenia występują rzadko, ale mogą również spowodować ponowne uruchomienie maszyny wirtualnej.

Więcej szczegółów można znaleźć w tej dokumentacji: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Nadmiarowość magazynu platformy Azure

Dane konta usługi Microsoft Azure Storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności, spełnia wymagania umowy SLA magazynu platformy Azure nawet w wypadku przejściowych awarii sprzętu.

Ponieważ usługi Azure Storage może być utrzymywanie trzy obrazy danych domyślnie, RAID5 lub RAID1 na wielu dyskach platformy Azure nie są konieczne.

Więcej szczegółów można znaleźć w tym artykule: <https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Przy użyciu ponownego uruchamiania maszyny Wirtualnej infrastruktury platformy Azure, aby osiągnąć wyższą dostępność aplikacji SAP

Jeśli nie chcesz używać funkcje, takie jak Windows Server Failover Clustering (WSFC) lub program Pacemaker w systemie Linux (obecnie obsługiwany tylko dla systemu SLES 12 lub nowszym), uruchom ponownie maszynę Wirtualną platformy Azure służy do ochrony systemu SAP przed planowanych lub nieplanowanych przestojów platformy Azure Serwer fizyczny, infrastruktury i ogólną podstawowej platformy Azure.

> [!NOTE]
> Warto wspomnieć, że przede wszystkim ponowne uruchomienie maszyny Wirtualnej platformy Azure chroni maszyny wirtualne i aplikacje nie. Ponowne uruchomienie maszyny Wirtualnej nie oferuje wysoką dostępność aplikacji SAP, ale oferują, pewien poziom dostępności infrastruktury i w związku z tym pośrednio wyższą dostępność systemów SAP. Ponadto nie ma umowy SLA dla czas potrzebny na ponowne uruchomienie maszyny Wirtualnej po awarii hosta planowane lub nieplanowane. W związku z tym ta metoda wysokiej dostępności nie jest odpowiednia dla krytycznych składników z systemem SAP, takich jak (A) SCS lub DBMS.
>
>

Inny element ważne infrastruktury wysokiej dostępności jest magazyn. Na przykład umowę SLA usługi Azure Storage jest przez 99,9% czasu. Jeśli jeden służy do wdrażania wszystkich maszyn wirtualnych z dyskami jej do jednego konta usługi Azure Storage, potencjalne niedostępność spowoduje niedostępność wszystkich maszyn wirtualnych, które są umieszczane na tym koncie magazynu platformy Azure i również wszystkie SAP składniki działają w ramach tych maszyn wirtualnych usługi Azure Storage.  

Zamiast umieszczenie wszystkich maszyn wirtualnych do jednego pojedynczego konta magazynu platformy Azure, możesz również użyć dedykowanych dla magazynu kont dla każdej maszyny Wirtualnej i w ten sposób zwiększyć ogólną dostępność aplikacji maszyny Wirtualnej i SAP, używając wielu niezależnych kont usługi Azure Storage.

Usługa Azure Managed Disks są automatycznie umieszczane w domenie błędów maszyny wirtualnej, które są dołączone. Jeśli umieścisz dwie maszyny wirtualne w dostępności zestawu Managed Disks i używać, platformie zajmie się dystrybucji Managed Disks w różnych domenach błędów także. Jeśli planujesz używanie usługi Premium Storage, zdecydowanie zaleca się również za pomocą zarządzania dyskami.

Przykładowa architektura systemu SAP NetWeaver, który korzysta z konta o wysokiej dostępności i magazynu infrastruktury platformy Azure może wyglądać następująco:

![Przy użyciu infrastruktury platformy Azure o wysokiej dostępności, aby osiągnąć wyższą dostępność aplikacji SAP][planning-guide-figure-2900]

Przykładowa architektura systemu SAP NetWeaver, który korzysta z infrastruktury platformy Azure o wysokiej dostępności i dyski Managed Disks może wyglądać następująco:

![Przy użyciu infrastruktury platformy Azure o wysokiej dostępności, aby osiągnąć wyższą dostępność aplikacji SAP][planning-guide-figure-2901]

Dla krytycznych składników SAP możemy osiągnąć następujące do tej pory:

* Wysoką dostępność serwerów aplikacji SAP (AS)

  Wystąpień serwera aplikacji SAP są składnikom nadmiarowym. Każdy SAP, ponieważ wystąpienie jest wdrażana na własnej maszynie Wirtualnej, działający w innej platformy Azure w domenach błędów i domeny uaktualnienia (zobacz rozdziały [domen błędów] [ planning-guide-3.2.1] i [domen uaktualnienia] [ planning-guide-3.2.2]). To jest zapewniana przez przy użyciu zestawów dostępności platformy Azure (zobacz rozdział [zestawami dostępności Azure][planning-guide-3.2.3]). Potencjalne niedostępność planowane lub nieplanowane błędów platformy Azure lub do domeny uaktualnienia spowoduje niedostępność ograniczonej liczby maszyn wirtualnych z ich jako SAP wystąpień.

  Każdy SAP, ponieważ wystąpienie jest umieszczany w swoje własne konta usługi Azure Storage — potencjalnych niedostępność jedno konto magazynu platformy Azure spowoduje niedostępność tylko jednej maszyny Wirtualnej z jej SAP wystąpienia. Należy jednak pamiętać, że istnieje limit kont magazynu platformy Azure w ramach jednej subskrypcji platformy Azure. Aby zapewnić automatyczne uruchamianie wystąpienia (A) SCS po ponownym uruchomieniu maszyny Wirtualnej, upewnij się, że ustawić parametr Autostart wystąpienia (A) SCS start profilu opisane w rozdziale [przy użyciu funkcji Autostart dla wystąpień SAP][planning-guide-11.5].
  Przeczytaj również rozdział [wysokiej dostępności dla serwerów aplikacji SAP] [ planning-guide-11.4.1] Aby uzyskać więcej informacji.

  Nawet w przypadku używania dysków zarządzanych te dyski są także przechowywane na koncie usługi Azure Storage i może być niedostępna w przypadku awarii usługi storage.

* *Wyższe* wystąpienia dostępności SAP (A) SCS

  W tym miejscu możemy korzystanie z platformy Azure do ochrony maszyny Wirtualnej za pomocą zainstalowanego wystąpienia SAP (A) SCS ponowne uruchomienie maszyny Wirtualnej. W przypadku planowanych lub nieplanowanych przestojów platformy Azure do serwerów, maszyn wirtualnych zostanie uruchomiona ponownie na innym serwerze dostępne. Jak wspomniano wcześniej, przede wszystkim ponowne uruchomienie maszyny Wirtualnej platformy Azure zapewnia ochronę maszyn wirtualnych i nie aplikacji, w tym przypadku (A) SCS. Za pomocą uruchomić maszynę Wirtualną skontaktujemy się, pośrednio wyższą dostępność wystąpienia SAP (A) SCS. Na ułatwieniu zapewnienia automatyczne uruchamianie wystąpienia (A) SCS, po ponownym uruchomieniu maszyny Wirtualnej, upewnij się ustawić parametr Autostart () SCS profilu uruchamiania wystąpienia opisane w rozdziale [przy użyciu funkcji Autostart dla wystąpień SAP][planning-guide-11.5]. Oznacza to, (A) SCS wystąpienia jako pojedynczy punkt awarii (SPOF) działające w pojedynczej maszyny Wirtualnej będzie dominującego współczynnik dostępność całe środowisko SAP.

* *Wyższe* dostępności serwera systemu DBMS

  W tym miejscu podobnie jak w przypadku użycia wystąpienia SAP (A) SCS, firma Microsoft korzystanie z platformy Azure maszyna wirtualna po ponownym uruchomieniu ochronę maszyny Wirtualnej za pomocą zainstalowanego oprogramowania systemu DBMS i możemy osiągnąć wyższą dostępność oprogramowania systemu DBMS poprzez ponowne uruchomienie maszyny Wirtualnej.
  System DBMS, uruchomiony w jednej maszyny Wirtualnej jest również SPOF i jest dominującego współczynnik dostępność całe środowisko SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Wysoka dostępność aplikacji SAP w usłudze IaaS platformy Azure

Uzyskanie pełnej SAP system wysokiej dostępności, należy chronić wszystkie krytyczne składniki systemu SAP, na przykład nadmiarowych serwerów aplikacji SAP i unikatowy składników (na przykład pojedynczy punkt awarii), takich jak wystąpienia SAP (A) SCS i DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Wysoka dostępność dla serwerów aplikacji SAP

Wystąpienia serwerów/okna dialogowego aplikacji SAP nie jest konieczne wziąć pod uwagę rozwiązanie określonych wysokiej dostępności. Wysoka dostępność jest osiągana przez nadmiarowości i tym samym masz wystarczającą ilość je na różnych maszynach wirtualnych. One wszystkie będzie umieszczona w tej samej platformy Azure zestawie dostępności w celu uniknięcia czy maszyny wirtualne mogły zostać zaktualizowane w tym samym czasie podczas planowanej konserwacji przestojów. Podstawowe funkcje, która opiera się na różnych uaktualniania i domen błędów w ramach jednostki skalowania Azure już została wprowadzona w rozdziale [domen uaktualnienia][planning-guide-3.2.2]. Zestawy dostępności platformy Azure były widoczne w rozdziale [zestawami dostępności Azure] [ planning-guide-3.2.3] tego dokumentu.

Nie ma żadnych nieskończona liczba domenach błędów i uaktualnienia, które mogą być używane przez zestawu dostępności platformy Azure w ramach jednostki skalowania Azure. Oznacza to, że umieszczenie liczbę maszyn wirtualnych w jednym zestawie dostępności, wcześniej czy później więcej niż jednej maszyny Wirtualnej kończy się w tej samej domeny uaktualnienia lub błędów.

Wdrażanie kilka wystąpień serwera aplikacji SAP w ich dedykowanych maszyn wirtualnych i przy założeniu, że mamy pięć domen uaktualnienia, poniższej ilustracji wynika z końcem. Rzeczywista maksymalna liczba domen aktualizacji i usterek w zestawie dostępności mogą ulec zmianie w przyszłości:

![Zaświadczanie o kondycji serwerów aplikacji SAP na platformie Azure][planning-guide-figure-3000]

Więcej szczegółów można znaleźć w tej dokumentacji: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Wysoka dostępność dla usług SAP Central Services na platformie Azure

Architektura wysokiej dostępności usług SAP Central Services na platformie Azure, można znaleźć w artykule [architektury wysokiej dostępności i scenariusze SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) jako informacje do wpisu. Wskazuje artykułu bardziej szczegółowe opisy dla systemów operacyjnych.

#### <a name="high-availability-for-the-sap-database-instance"></a>Wysoka dostępność dla wystąpienia bazy danych SAP

Typowe SAP DBMS zaświadczanie o kondycji konfiguracji opiera się na dwóch systemu DBMS na maszynach wirtualnych z użycia funkcji wysokiej dostępności systemu DBMS na replikowanie danych z aktywnego wystąpienia systemu DBMS na drugą maszynę Wirtualną do pasywne wystąpienia systemu DBMS.

Wysoka dostępność i odzyskiwanie po awarii z funkcji odzyskiwania systemu DBMS, ogólnie rzecz biorąc również jako określonego systemu DBMS są opisane w [przewodnik wdrażania systemu DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>End-to-End wysokiej dostępności dla systemu SAP ukończone

Poniżej przedstawiono dwa przykłady pełnej SAP NetWeaver HA architektury na platformie Azure — jeden dla Windows i jeden dla systemu Linux.

Tylko dyski niezarządzane: Pojęcia, jak wyjaśniono poniżej może być konieczne być narażone na ataki nieco podczas wdrażania wielu systemów SAP i liczba wdrożonych maszyn wirtualnych przekraczają maksymalny limit kont magazynu na subskrypcję. W takich przypadkach wirtualnych dysków twardych maszyn wirtualnych konieczne można łączyć w ramach jednego konta magazynu. Zwykle będzie tak zrobisz, łącząc warstwy aplikacji wirtualnych dysków twardych SAP maszyn wirtualnych z różnych systemów SAP.  Firma Microsoft również łączyć różne wirtualne dyski twarde różnych systemu DBMS na maszynach wirtualnych w różnych systemów SAP na jednym koncie magazynu platformy Azure. Dzięki temu pamiętając o granicach operacje We/Wy kont usługi Azure Storage (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Zaświadczanie o kondycji na Windows

![Architektura SAP NetWeaver aplikacji o wysokiej dostępności programu SQL Server w usłudze Azure IaaS][planning-guide-figure-3200]

Następujące konstrukcje platformy Azure są używane w systemie SAP NetWeaver do zminimalizowania wpływu problemami związanymi z infrastrukturą i stosowanie poprawek:

* Kompletny system jest wdrażana na platformie Azure (wymagane — DBMS, () SCS wystąpienia i kompletna aplikacja warstwy muszą działać w tej samej lokalizacji).
* Kompletny system jest uruchamiany w ramach jednej subskrypcji platformy Azure (wymagane).
* Kompletny system jest uruchamiany w ramach jednej sieci wirtualnej platformy Azure (wymagane).
* Rozdzielenie maszyn wirtualnych z jednego systemu SAP w trzech zestawach dostępności jest możliwe nawet w przypadku wszystkich maszyn wirtualnych należących do tej samej sieci wirtualnej.
* Każda warstwa (na przykład system DBMS, ASCS, serwery aplikacji), należy użyć dedykowanego zestawu dostępności.
* Wszystkie maszyny wirtualne uruchomione wystąpienia jednego systemu SAP DBMS znajdują się w jednym zestawie dostępności. Przyjęto założenie, że istnieje więcej niż jednej maszyny Wirtualnej z systemem wystąpień systemu DBMS na system od natywnych DBMS wysokiej dostępności, które są używane funkcje, takie jak AlwaysOn programu SQL Server lub środowiska Oracle Data Guard.
* Wszystkie maszyny wirtualne uruchomione wystąpienia systemu DBMS na korzystanie z tego konta magazynu. System DBMS dane i pliki dziennika są replikowane z jednego konta magazynu do innego konta magazynu przy użyciu funkcji wysokiej dostępności systemu DBMS, które synchronizują dane. Niedostępność jedno konto magazynu spowoduje niedostępność jednego węzła klastra programu SQL, Windows, ale nie całą usługę programu SQL Server.
* Wszystkie maszyny wirtualne uruchomione wystąpienia (A) SCS jednego systemu SAP znajdują się w jednym zestawie dostępności. Windows Server Failover klastra (WSFC) jest skonfigurowana wewnątrz tych maszyn wirtualnych w celu ochrony (A) SCS wystąpienia.
* Wszystkie maszyny wirtualne uruchomione wystąpienia () SCS korzystanie z tego konta magazynu. (A) SCS wystąpienia pliki i foldery globalnego SAP są replikowane do innego konta magazynu przy użyciu replikacji oprogramowanie SIOS DataKeeper z jednego konta magazynu. Niedostępność jedno konto magazynu spowoduje niedostępność jednego węzła klastra () SCS Windows, ale nie całość usługi () SCS.
* Wszystkie maszyny wirtualne reprezentujące warstwa serwera aplikacji SAP są w trzecim zestawu dostępności.
* Wszystkie maszyny wirtualne z serwerów aplikacji SAP korzystanie z tego konta magazynu. Niedostępność jedno konto magazynu spowoduje niedostępność jednego serwera aplikacji SAP, w której inne serwery aplikacji SAP nadal działać.

Poniższej ilustracji przedstawiono pozioma tego samego, przy użyciu dysków zarządzanych.

![Architektura SAP NetWeaver aplikacji o wysokiej dostępności programu SQL Server w usłudze Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Wysoka dostępność w systemie Linux

Architektura SAP wysokiej dostępności w systemie Linux na platformie Azure jest zasadniczo takie same jak dla Windows zgodnie z powyższym opisem. Zapoznaj się uwagę [1928533] listę rozwiązań obsługiwanych wysokiej dostępności.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Za pomocą funkcji Autostart dla wystąpieniami platformy SAP

SAP oferuje funkcje, które można uruchomić wystąpień SAP natychmiast po rozpoczęciu systemu operacyjnego na maszynie wirtualnej. Konkretne kroki zostały opisane w artykule bazy wiedzy SAP [1909114]. Jednak SAP nie jest zalecania ustawienia dłużej, ponieważ nie ma żadnej kontroli zgodnie z kolejnością ponownego uruchomienia wystąpienia, zakładając, że więcej niż jedna maszyna wirtualna stało się narażony lub wiele wystąpień uruchomiono na maszynę Wirtualną. Zakładając, że typowy scenariusz Azure jednego wystąpienia serwera aplikacji SAP w Maszynę wirtualną i wielkość liter jednej maszyny Wirtualnej, po pewnym czasie ponownie uruchomić pobieranie, Autostart nie jest krytyczna i może zostać włączona przez dodanie tego parametru:

    Autostart = 1

W profilu uruchamiania wystąpienia SAP ABAP i/lub Java.

> [!NOTE]
> Parametr Autostart może mieć również niektóre downfalls. Bardziej szczegółowo parametr wyzwalane początek SAP ABAP i Java wystąpienia, po uruchomieniu usługi systemu Windows/Linux powiązane wystąpienia. Czy na pewno dotyczy sytuacji, gdy jest uruchamiany system operacyjny. Jednak ponowne uruchomienie usługi SAP są również typowe kolejności dla funkcji zarządzania cyklem życia oprogramowania SAP, takich jak SUM lub innych aktualizacji lub uaktualnienia. Te funkcje nie są oczekiwane automatycznie uruchamiany ponownie wszystkie wystąpienia. W związku z tym parametr Autostart powinno być wyłączone przed uruchomieniem takie zadania. Parametr Autostart powinna nie również dla wystąpień SAP, które są klastrowane, takich jak ASCS/SCS/ciągłej integracji.
>
>

Zobacz, że dodatkowe informacje na temat funkcji autostart dla rozwiązania SAP wystąpień w tym miejscu:

* [Uruchamianie/zatrzymywanie SAP oraz usługi Unix serwera uruchomień/zatrzymań](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Uruchamianie i zatrzymywanie SAP NetWeaver na zarządzanie agentów](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Jak włączyć automatyczne Start z bazy danych HANA](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Większych systemów SAP 3-warstwowej
Aspekty wysokiej dostępności w 3-warstwowej konfiguracje rozwiązań SAP stało się już omówione we wcześniejszych sekcjach. Ale co systemy, w których zbyt duży, aby go na terenie platformy Azure, ale w warstwie aplikacji SAP DBMS wymagania serwera można wdrożyć na platformie Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Lokalizacja konfiguracji SAP 3-warstwowej
Podziel warstwy aplikacji nie jest obsługiwane samego, lub aplikacji i systemu DBMS warstwy między lokalną i platformą Azure. System SAP jest całkowicie wdrożone lokalnie lub na platformie Azure. On również nie jest obsługiwane niektóre serwery aplikacji, uruchom na platformie Azure w środowisku lokalnym i inne. To punkt początkowy dyskusji. Możemy również nie są obsługiwane na składniki systemu DBMS systemu SAP i SAP warstwa serwera aplikacji, wdrożonych w dwóch różnych regionach platformy Azure. Na przykład DBMS w warstwie aplikacji w regionie zachodnie stany USA i SAP w środkowych stanach USA. Nie obsługuje takie konfiguracje są czułości opóźnienie architektury oprogramowania SAP NetWeaver.

Jednak w ciągu ostatniego roku danych Centrum partnerów opracowany wspólnej lokalizacji do regionów platformy Azure. Te wspólnej lokalizacje często znajdują się w pobliżu danych platformy Azure fizycznych centrów w regionie świadczenia usługi Azure. Krótką odległość i połączenie trwałe z wspólnej lokalizacji za pośrednictwem usługi ExpressRoute na platformę Azure może powodować opóźnienia, która jest mniejsza niż 2 ms. W takich przypadkach aby zlokalizować warstwę DBMS (w tym magazyn SAN/NAS) wspólnej lokalizacji i SAP warstwy aplikacji na platformie Azure jest możliwe. [Dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Systemy kopii zapasowej SAP offline
W zależności od konfiguracji SAP wybrane (Warstwa 2 lub 3-warstwowej) może być, aby utworzyć kopię zapasową. Zawartość samą maszynę Wirtualną oraz mieć kopię zapasową bazy danych. Tworzenie kopii zapasowych związanych z bazami danych powinny odbywać się za pomocą metod bazy danych. Szczegółowy opis różnych baz danych można znaleźć w [przewodnik systemu DBMS][dbms-guide]. Z drugiej strony danych SAP utworzeniem kopii zapasowej w trybie offline sposób (w tym także zawartość bazy danych) zgodnie z opisem w tej sekcji lub online zgodnie z opisem w następnej sekcji.

Kopie zapasowe offline zasadniczo wymaga wyłączenia maszyny Wirtualnej w witrynie Azure portal i kopia podstawowej dysku maszyny Wirtualnej, a także wszystkich dołączonych dysków do maszyny Wirtualnej. Spowoduje to zachować punkt w czasie obrazu maszyny Wirtualnej i jej skojarzone dyski. Zaleca się skopiowanie kopii zapasowych do innego konta magazynu platformy Azure. Dlatego procedury opisane w rozdziale [kopiowanie dysków między kontami magazynu Azure] [ planning-guide-5.4.2] naliczona zostałaby tego dokumentu.
Oprócz zamknięcie za pomocą witryny Azure portal, jedną też zrobić to za pomocą programu Powershell lub interfejsu wiersza polecenia, tak jak opisano tutaj: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Przywrócenie tego stanu może składać się z usuwanie podstawowej maszyny Wirtualnej, a także oryginalnych dysków podstawowej maszyny Wirtualnej i zainstalować dyski, kopiowanie ponownie zapisane dysków do oryginalnej grupy zasobów lub kontem magazynu za dyski zarządzane i ponowne wdrożenie systemu.
W tym artykule przedstawiono przykładowy skrypt ten proces w programie Powershell: <http://www.westerndevs.com/azure-snapshots/>

Upewnij się zainstalować nową licencję na oprogramowanie SAP, ponieważ przywracanie kopii zapasowej maszyny Wirtualnej, zgodnie z powyższym opisem powoduje utworzenie nowego klucza sprzętowego.

### <a name="online-backup-of-an-sap-system"></a>Kopii zapasowych online systemu SAP

Kopia zapasowa systemu DBMS odbywa się za pomocą metod specyficznych dla systemu DBMS, zgodnie z opisem w [przewodnik systemu DBMS][dbms-guide].

Inne maszyny wirtualne w systemie SAP utworzeniem kopii zapasowej za pomocą funkcji Kopia zapasowa maszyny wirtualnej platformy Azure. Kopia zapasowa maszyny wirtualnej platformy Azure jest standardową metodę, aby utworzyć kopię zapasową całej maszyny Wirtualnej na platformie Azure. Usługa Azure Backup przechowuje kopie zapasowe na platformie Azure i umożliwia przywrócenie maszyny wirtualnej ponownie.

> [!NOTE]
> Począwszy od grudnia 2015 za pomocą kopii zapasowych maszyn wirtualnych nie przechowuje unikatowy identyfikator maszyny Wirtualnej, który jest używany dla rozwiązania SAP licencjonowania. Oznacza to, że przywrócona maszyna wirtualna jest uważany za nowej maszyny Wirtualnej i nie mogą zastąpić ten poprzedniej wersji portalu, który został zapisany przywracania z kopii zapasowej maszyny Wirtualnej wymaga instalacji klucza licencji SAP.
>
> ![Windows][Logo_Windows] Windows
>
> Teoretycznie maszyn wirtualnych, które wykonywania może być kopie zapasowe baz danych w sposób ciągły, a także, gdy DBMS system obsługuje usługi VSS Windows (usługi kopiowania woluminów w tle <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>), na przykład program SQL Server wykonuje.
> Należy jednak pamiętać, który oparty na kopii zapasowych maszyn wirtualnych platformy Azure, który w momencie przywrócenie baz danych nie są możliwe. Dlatego zaleca się wykonywanie kopii zapasowych baz danych z funkcjami systemu DBMS, zamiast polegania na kopii zapasowych maszyn wirtualnych platformy Azure.
>
> Aby zapoznać się z kopiami zapasowymi maszyn wirtualnych platformy Azure Rozpocznij tutaj: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Inne możliwości mają używać kombinacji programu Microsoft Data Protection Manager zainstalowane w maszynie Wirtualnej platformy Azure i usługi Azure Backup do wykonywania kopii zapasowej i przywracania baz danych. Więcej informacji można znaleźć tutaj: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Nie ma odpowiednika w usłudze VSS. Windows w systemie Linux. W związku z tym tylko spójna na poziomie plików kopie zapasowe są możliwe, ale nie spójnych z aplikacją kopii zapasowych. Kopia zapasowa systemu SAP DBMS powinno się odbywać za pomocą funkcji systemu DBMS. System plików, który zawiera dane dotyczące SAP może zostać zapisana, na przykład za pomocą tar zgodnie z opisem w tym miejscu: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Platformy Azure jako lokacji odzyskiwania po awarii dla krajobrazów SAP w środowisku produkcyjnym

Od średniej 2014 r. rozszerzenia do różnych składników funkcji Hyper-V, programu System Center i Azure umożliwiają użycie platformy Azure jako lokacji odzyskiwania po awarii dla maszyn wirtualnych uruchomionych w środowisku lokalnym oparte na funkcji Hyper-V.

Blog szczegółowych informacji na temat sposobu wdrażania tego rozwiązania opisano tutaj: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Podsumowanie

Kluczowe punkty wysoką dostępność dla systemów SAP na platformie Azure są:

* W tym momencie SAP pojedynczego punktu awarii nie może zostać zabezpieczona dokładnie tak samo, jak jest możliwe w przypadku wdrożeń lokalnych. Przyczyną jest ten dysk udostępnionych klastrów jeszcze nie można utworzyć na platformie Azure, bez 3 oprogramowania.
* Dla warstwy system DBMS, musisz korzystać z funkcji systemu DBMS, które nie korzystają z technologii klastra udostępnionego dysku. Szczegółowe informacje są udokumentowane w artykule [przewodnik systemu DBMS][dbms-guide].
* Aby zminimalizować problemy w domenach błędów w konserwacji hosta lub infrastruktury platformy Azure, należy korzystać z zestawami dostępności Azure:
  * Zaleca się mieć jeden zestaw dostępności dla warstwy aplikacji SAP.
  * Zaleca się mieć oddzielne zestaw dostępności dla warstwy systemu SAP DBMS.
  * NIE zaleca się stosowanie tego samego zestawu dostępności dla maszyn wirtualnych z różnych systemów SAP.
  * Zalecane jest użycie usługi Managed Disks — wersja Premium.
* Na potrzeby kopii zapasowej warstwy systemu SAP DBMS, sprawdź [przewodnik systemu DBMS][dbms-guide].
* Tworzenie kopii zapasowych wystąpienia SAP w oknie dialogowym sens nieco, ponieważ jest zwykle szybsze ponowne wdrażanie wystąpień proste okno dialogowe.
* Tworzenie kopii zapasowej maszynę Wirtualną, która zawiera katalog globalnego systemu SAP i z niego wszystkie profile z różnymi wystąpieniami sensu i powinny być wykonywane przy użyciu kopii zapasowej Windows lub, na przykład celowa w systemie Linux. Ponieważ różnią się od systemu Windows Server 2008 (R2) i Windows Server 2012 (R2), które ułatwiają tworzenie kopii zapasowej przy użyciu nowszego systemu Windows Server zwalnia, zaleca się uruchomienie systemu Windows Server 2012 (R2) jako systemu operacyjnego gościa Windows.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj artykuły:

- [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Konfiguracje infrastruktury SAP HANA i operacji na platformie Azure] (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
