---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver | Dokumenty firmy Microsoft
description: Przewodnik po wysokiej dostępności dla systemu SAP NetWeaver na maszynach wirtualnych platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa0810818bf7cfea21f925ee639b4b5a50dcb23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246125"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Wysoka dostępność dla sap NetWeaver na maszynach wirtualnych platformy Azure

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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md
[dbms-guide-2.1]:../../virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:../../virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:../../virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:../../virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:../../virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:../../virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:../../virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:../../virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:../../virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:../../virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:../../virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:../../virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:../../virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:../../virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:../../virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:../../virtual-machines-windows-sap-deployment-guide.md
[deployment-guide-2.2]:../../virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:../../virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:../../virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:../../virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:../../virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:../../virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:../../virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:../../virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:../../virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:../../virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:../../virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:../../virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:../../virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:../../virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:../../virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:../../virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:../../virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:../../virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:../../virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:../../virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:../../virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:../../virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:../../virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:../../virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:../../virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:../../virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:../../virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:../../virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Konfiguracja wysokiej dostępności sap multi-SID)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktów SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Maszyny wirtualne platformy Azure to rozwiązanie dla organizacji, które potrzebują zasobów obliczeniowych, magazynowych i sieciowych, w minimalnym czasie i bez długich cykli zaopatrzenia. Maszyny wirtualne platformy Azure umożliwiają wdrażanie klasycznych aplikacji, takich jak ABAP, Java i stos ABAP+Java oparty na sap NetWeaver. Zwiększ niezawodność i dostępność bez dodatkowych zasobów lokalnych. Maszyny wirtualne platformy Azure obsługuje łączność między lokalizacjami, dzięki czemu można zintegrować maszyny wirtualne platformy Azure z domen lokalnych organizacji, chmur prywatnych i środowiska systemowego SAP.

W tym artykule omówimy kroki, które można wykonać w celu wdrożenia systemów SAP o wysokiej dostępności na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager. Prowadzimy Cię przez te główne zadania:

* Znajdź odpowiednie uwagi SAP i przewodniki instalacyjne wymienione w sekcji [Zasoby.][sap-ha-guide-2] Ten artykuł uzupełnia dokumentację instalacji SAP i notatki SAP, które są podstawowymi zasobami, które mogą pomóc w instalowaniu i wdrażaniu oprogramowania SAP na określonych platformach.
* Poznaj różnice między modelem wdrażania usługi Azure Resource Manager a klasycznym modelem wdrażania platformy Azure.
* Dowiedz się więcej o trybach kworum klastrowania trybu awaryjnego klastrowania systemu Windows Server, aby wybrać model odpowiedni dla wdrożenia platformy Azure.
* Dowiedz się więcej o udostępnionym magazynie współdzielonym klastrowania trybu failover systemu Windows Server w usługach platformy Azure.
* Dowiedz się, jak chronić składniki pojedynczego punktu awarii, takie jak zaawansowane programowanie aplikacji biznesowych (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) i systemy zarządzania bazami danych (DBMS) oraz nadmiarowe składniki, takie jak SAP Application Server, na platformie Azure.
* Postępuj zgodnie z przykładem krok po kroku instalacji i konfiguracji systemu SAP o wysokiej dostępności w klastrze klastra trybu failover systemu Windows Server na platformie Azure przy użyciu usługi Azure Resource Manager.
* Dowiedz się więcej o dodatkowych krokach wymaganych do korzystania z klastra trybu failover systemu Windows Server na platformie Azure, ale które nie są potrzebne we wdrożeniu lokalnym.

Aby uprościć wdrażanie i konfigurację, w tym artykule używamy szablonów Menedżera zasobów wysokiej dostępności SAP trzywarstwowych. Szablony automatyzują wdrażanie całej infrastruktury potrzebnej do korzystania z systemu SAP o wysokiej dostępności. Infrastruktura obsługuje również rozmiary SAP Application Performance Standard (SAPS) systemu SAP.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że spełniasz wymagania wstępne, które są opisane w poniższych sekcjach. Ponadto należy sprawdzić wszystkie zasoby wymienione w sekcji [Zasoby.][sap-ha-guide-2]

W tym artykule używamy szablonów usługi Azure Resource Manager dla [trzywarstwowego programu SAP NetWeaver.](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/) Aby uzyskać przydatne omówienie szablonów, zobacz [szablony usługi SAP Azure Resource Manager](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Zasobów
Te artykuły obejmują wdrożenia SAP na platformie Azure:

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver][dbms-guide]
* [Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver (ten przewodnik)][sap-ha-guide]

> [!NOTE]
> Jeśli to możliwe, dajemy Ci link do przewodnika po instalacji SAP (patrz [instrukcje instalacji SAP).][sap-installation-guides] Aby uzyskać wymagania wstępne i informacje o procesie instalacji, warto uważnie przeczytać przewodniki instalacji SAP NetWeaver. W tym artykule omówiono tylko określone zadania dla systemów opartych na sap NetWeaver, których można używać z maszynami wirtualnymi platformy Azure.
>
>

Te uwagi SAP są związane z tematem SAP na platformie Azure:

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i rozmiary |
| [2015553] |SAP na platformie Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Ulepszone monitorowanie platformy Azure dla sap |
| [2178632] |Metryki monitorowania kluczy dla sap na platformie Microsoft Azure |
| [1999351] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2243692] |Korzystanie z usługi Azure Premium SSD Storage dla wystąpienia SAP DBMS |

Dowiedz się więcej o [ograniczeniach subskrypcji platformy Azure,][azure-resource-manager/management/azure-subscription-service-limits-subscription]w tym ogólnych ograniczeniach domyślnych i maksymalnych ograniczeniach.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Sap o wysokiej dostępności z usługą Azure Resource Manager a modelem wdrażania klasycznego platformy Azure
Usługi Azure Resource Manager i klasyczne modele wdrażania platformy Azure różnią się w następujących obszarach:

- Grupy zasobów
- Zależność wewnętrznego modułu równoważenia obciążenia platformy Azure w grupie zasobów platformy Azure
- Obsługa scenariuszy SAP dla wielu identyfikatorów SID

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Grupy zasobów
W usłudze Azure Resource Manager można używać grup zasobów do zarządzania wszystkimi zasobami aplikacji w ramach subskrypcji platformy Azure. Zintegrowane podejście w grupie zasobów wszystkie zasoby mają ten sam cykl życia. Na przykład wszystkie zasoby są tworzone w tym samym czasie i są usuwane w tym samym czasie. Dowiedz się więcej o [grupach zasobów](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Zależność wewnętrznego modułu równoważenia obciążenia platformy Azure w grupie zasobów platformy Azure

W klasycznym modelu wdrażania platformy Azure istnieje zależność między wewnętrznym modułem równoważenia obciążenia platformy Azure (usługa Azure Load Balancer) a grupą usług w chmurze. Każdy wewnętrzny moduł równoważenia obciążenia potrzebuje jednej grupy usług w chmurze.

W usłudze Azure Resource Manager nie potrzebujesz grupy zasobów platformy Azure, aby używać modułu równoważenia obciążenia platformy Azure. Środowisko jest prostsze i bardziej elastyczne.

### <a name="support-for-sap-multi-sid-scenarios"></a>Obsługa scenariuszy SAP dla wielu identyfikatorów SID

W usłudze Azure Resource Manager można zainstalować wiele wystąpień ASCS/SCS identyfikatora systemu SAP w jednym klastrze. Wystąpienia wielu identyfikatorów SID są możliwe ze względu na obsługę wielu adresów IP dla każdego modułu równoważenia obciążenia wewnętrznego platformy Azure.

Aby użyć klasycznego modelu wdrażania platformy Azure, postępuj zgodnie z procedurami opisanymi w [programie SAP NetWeaver na platformie Azure: Klastrowanie wystąpień SAP ASCS/SCS przy użyciu klastra trybu failover systemu Windows Server na platformie Azure za pomocą programu SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Zdecydowanie zaleca się użycie modelu wdrażania usługi Azure Resource Manager dla instalacji SAP. Oferuje wiele korzyści, które nie są dostępne w klasycznym modelu wdrażania. Dowiedz się więcej o [modelach wdrażania][virtual-machines-azure-resource-manager-architecture-benefits-arm] platformy Azure.   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Klastrowanie trybu failover systemu Windows Server
Klastrowanie trybu failover systemu Windows Server jest podstawą instalacji SAP ASCS/SCS o wysokiej dostępności i systemu DBMS w systemie Windows.

Klaster trybu failover to grupa niezależnych serwerów (węzłów) o 1+n, które współpracują ze sobą w celu zwiększenia dostępności aplikacji i usług. Jeśli wystąpi błąd węzła, klastrowanie trybu failover systemu Windows Server oblicza liczbę błędów, które mogą wystąpić przy zachowaniu w dobrej kondycji klastra w celu zapewnienia aplikacji i usług. Można wybrać jeden z różnych trybów kworum, aby osiągnąć klastrowanie trybu failover.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Tryby kworum
Korzystając z klastrowania trybu failover systemu Windows Server, można wybrać jeden z czterech trybów kworum:

* **Większość węzłów**. Każdy węzeł klastra może głosować. Klaster działa tylko z większością głosów, czyli z ponad połową głosów. Zaleca się tę opcję dla klastrów, które mają nierówną liczbę węzłów. Na przykład trzy węzły w klastrze z siedmiowęzłami mogą zakończyć się niepowodzeniem, a fotosy klastra osiągają większość i będą nadal działać.  
* **Większość węzłów i dysków**. Każdy węzeł i wyznaczony dysk (monitor dysku) w magazynie klastra mogą głosować, gdy są dostępne i w komunikacji. Klaster działa tylko z większością głosów, czyli z ponad połową głosów. Ten tryb ma sens w środowisku klastra z parzyste węzły. Jeśli połowa węzłów i dysku są w trybie online, klaster pozostaje w dobrej kondycji.
* **Większość udziałów w węzłach i plikach**. Każdy węzeł plus wyznaczony udział plików (monitor udziału plików), który tworzy administrator, może głosować, niezależnie od tego, czy węzły i udział plików są dostępne, oraz w komunikacji. Klaster działa tylko z większością głosów, czyli z ponad połową głosów. Ten tryb ma sens w środowisku klastra z parzyste węzły. Jest podobny do trybu większość węzła i dysku, ale używa udziału pliku świadka zamiast dysku świadka. Ten tryb jest łatwy do zaimplementowania, ale jeśli sam udział plików nie jest wysoce dostępny, może stać się pojedynczym punktem awarii.
* **Brak większości: Tylko dysk**. Klaster ma kworum, jeśli jeden węzeł jest dostępny i w komunikacji z określonym dyskiem w magazynie klastra. Tylko węzły, które również są w komunikacji z tym dyskiem, mogą dołączyć do klastra. Zaleca się, aby nie korzystać z tego trybu.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Klastrowanie trybu failover systemu Windows Server lokalnie
Rysunek 1 przedstawia klaster dwóch węzłów. Jeśli połączenie sieciowe między węzłami ulegnie awarii, a oba węzły pozostaną uruchomione, dysk kworum lub udział plików określa, który węzeł będzie nadal dostarczał aplikacje i usługi klastra. Węzeł, który ma dostęp do dysku kworum lub udziału plików jest węzłem, który zapewnia, że usługi są kontynuowane.

Ponieważ w tym przykładzie użyto klastra dwuwęzłowego, używamy trybu kworum większościowego udziału w węzłach i plikach. Większość węzłów i dysków jest również prawidłową opcją. W środowisku produkcyjnym zaleca się użycie dysku kworum. Możesz użyć technologii sieci i systemu pamięci masowej, aby była wysoce dostępna.

![Rysunek 1: Przykład konfiguracji klastrowania trybu failover systemu Windows Server dla SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-1000]

_**Rysunek 1:** Przykład konfiguracji klastrowania trybu failover systemu Windows Server dla sap ASCS/SCS na platformie Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Magazyn udostępniony
Rysunek 1 przedstawia również dwuwęzłowy klaster magazynu udostępnionego. W lokalnym klastrze magazynu udostępnionego wszystkie węzły w klastrze wykrywają magazyn udostępniony. Mechanizm blokowania chroni dane przed uszkodzeniem. Wszystkie węzły mogą wykryć, jeśli inny węzeł ulegnie awarii. Jeśli jeden węzeł ulegnie awarii, pozostały węzeł przejmuje na własność zasoby magazynu i zapewnia dostępność usług.

> [!NOTE]
> Nie potrzebujesz dysków udostępnionych, aby uzyskać wysoką dostępność w niektórych aplikacjach DBMS, takich jak SQL Server. Program SQL Server Always On replikuje pliki danych i dzienników DBMS z dysku lokalnego jednego węzła klastra na dysk lokalny innego węzła klastra. W takim przypadku konfiguracja klastra systemu Windows nie wymaga dysku udostępnionego.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Rozwiązywanie problemów sieciowych i nazw
Komputery klienckie docierają do klastra za pośrednictwem wirtualnego adresu IP i nazwy hosta wirtualnego, którą udostępnia serwer DNS. Węzły lokalne i serwer DNS mogą obsługiwać wiele adresów IP.

W typowej konfiguracji można użyć co najmniej dwóch połączeń sieciowych:

* Dedykowane połączenie z pamięcią masową
* Wewnętrzne połączenie sieciowe klastra dla pulsu
* Sieć publiczna używana przez klientów do łączenia się z klastrem

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Klastrowanie trybu failover systemu Windows Server na platformie Azure
W porównaniu z wdrożeniami w chmurze nieosłoniowej lub prywatnej maszyny wirtualne platformy Azure wymagają dodatkowych kroków w celu skonfigurowania klastrowania trybu failover systemu Windows Server. Podczas tworzenia udostępnionego dysku klastrowego należy ustawić kilka adresów IP i nazwy hostów wirtualnych dla wystąpienia SAP ASCS/SCS.

W tym artykule omówimy kluczowe pojęcia i dodatkowe kroki wymagane do utworzenia klastra usług centralnych o wysokiej dostępności SAP na platformie Azure. Pokażemy Ci, jak skonfigurować narzędzie innej firmy SIOS DataKeeper i jak skonfigurować wewnętrzny moduł równoważenia obciążenia platformy Azure. Za pomocą tych narzędzi można utworzyć klaster trybu failover systemu Windows z monitorem udziału plików na platformie Azure.

![Rysunek 2: Konfiguracja klastrowania trybu failover systemu Windows Server na platformie Azure bez dysku udostępnionego][sap-ha-guide-figure-1001]

_**Rysunek 2:** Konfiguracja klastrowania trybu failover systemu Windows Server na platformie Azure bez dysku udostępnionego_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Dysk udostępniony na platformie Azure z usługą SIOS DataKeeper
Potrzebujesz magazynu udostępnionego klastra dla wystąpienia SAP ASCS/SCS o wysokiej dostępności. Od września 2016 r. platforma Azure nie oferuje udostępnionego magazynu, którego można użyć do utworzenia udostępnionego klastra magazynu. Za pomocą oprogramowania innej firmy SIOS DataKeeper Cluster Edition można utworzyć dublowany magazyn, który symuluje współdzieloną pamięć masową klastra. Rozwiązanie SIOS zapewnia synchronikową replikację danych w czasie rzeczywistym. W ten sposób można utworzyć zasób dysku udostępnionego dla klastra:

1. Dołącz dodatkowy wirtualny dysk twardy platformy Azure (VHD) do każdej z maszyn wirtualnych (VM) w konfiguracji klastra systemu Windows.
2. Uruchom SIOS DataKeeper Cluster Edition na obu węzłach maszyny wirtualnej.
3. Skonfiguruj SIOS DataKeeper Cluster Edition tak, aby odzwierciedlała zawartość dodatkowego woluminu podłączonego vhd ze źródłowej maszyny wirtualnej do dodatkowego woluminu podłączonego do dysku twardego docelowej maszyny wirtualnej. Program SIOS DataKeeper wyodrębnia woluminy lokalne źródłowe i docelowe, a następnie przedstawia je klastrowaniu trybu failover systemu Windows Server jako jeden dysk udostępniony.

Uzyskaj więcej informacji o [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Rysunek 3: Konfiguracja klastrowania trybu failover systemu Windows Server na platformie Azure za pomocą menedżera danych SIOS][sap-ha-guide-figure-1002]

_**Rysunek 3:** Konfiguracja klastrowania trybu failover systemu Windows Server na platformie Azure za pomocą modułu danych SIOS_

> [!NOTE]
> Nie potrzebujesz dysków udostępnionych, aby uzyskać wysoką dostępność z niektórymi produktami DBMS, takimi jak SQL Server. Program SQL Server Always On replikuje pliki danych i dzienników DBMS z dysku lokalnego jednego węzła klastra na dysk lokalny innego węzła klastra. W takim przypadku konfiguracja klastra systemu Windows nie wymaga dysku udostępnionego.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Rozpoznawanie nazw na platformie Azure
Platforma w chmurze platformy Azure nie oferuje opcji konfigurowania wirtualnych adresów IP, takich jak przestawne adresy IP. Potrzebujesz alternatywnego rozwiązania, aby skonfigurować wirtualny adres IP, aby dotrzeć do zasobu klastra w chmurze.
Platforma Azure ma wewnętrzny moduł równoważenia obciążenia w usłudze Azure Load Balancer. Dzięki wewnętrznej równoważce obciążenia klienci docierają do klastra za pomocą wirtualnego adresu IP klastra.
Należy wdrożyć wewnętrzny moduł równoważenia obciążenia w grupie zasobów, która zawiera węzły klastra. Następnie skonfiguruj wszystkie niezbędne reguły przekazywania portów za pomocą portów sondy wewnętrznego modułu równoważenia obciążenia.
Klienci mogą łączyć się za pośrednictwem nazwy hosta wirtualnego. Serwer DNS rozpoznaje adres IP klastra, a wewnętrzny moduł równoważenia obciążenia obsługuje przekazywanie portów do aktywnego węzła klastra.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>Wysoka dostępność SAP NetWeaver w usłudze Azure Infrastructure-as-a-Service (IaaS)
Aby uzyskać wysoką dostępność aplikacji SAP, na przykład dla składników oprogramowania SAP, należy chronić następujące składniki:

* Wystąpienie serwera aplikacji SAP
* Wystąpienie SAP ASCS/SCS
* Serwer DBMS

Aby uzyskać więcej informacji na temat ochrony składników SAP w scenariuszach wysokiej dostępności, zobacz [Planowanie i implementacja maszyn wirtualnych platformy Azure dla SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Serwer aplikacji SAP o wysokiej dostępności
Zazwyczaj nie potrzebujesz określonego rozwiązania o wysokiej dostępności dla serwera aplikacji SAP i wystąpień okien dialogowych. Osiągnięcie wysokiej dostępności przez nadmiarowość i skonfigurujesz wiele wystąpień okien dialogowych w różnych wystąpieniach maszyn wirtualnych platformy Azure. Powinien mieć co najmniej dwa wystąpienia aplikacji SAP zainstalowane w dwóch wystąpieniach maszyn wirtualnych platformy Azure.

![Rysunek 4: Serwer aplikacji SAP o wysokiej dostępności][sap-ha-guide-figure-2000]

_**Rysunek 4:** Serwer aplikacji SAP o wysokiej dostępności_

Należy umieścić wszystkie maszyny wirtualne, które obsługują wystąpienia serwera aplikacji SAP w tym samym zestawie dostępności platformy Azure. Zestaw dostępności platformy Azure zapewnia, że:

* Wszystkie maszyny wirtualne są częścią tej samej domeny uaktualnienia. Domeny uaktualnienia, na przykład upewnia się, że maszyny wirtualne nie są aktualizowane w tym samym czasie podczas planowanych przestojów konserwacji.
* Wszystkie maszyny wirtualne są częścią tej samej domeny błędów. Domena błędów, na przykład, upewnia się, że maszyny wirtualne są wdrażane tak, że żaden pojedynczy punkt awarii wpływa na dostępność wszystkich maszyn wirtualnych.

Dowiedz się więcej o [zarządzaniu dostępnością maszyn wirtualnych][virtual-machines-manage-availability].

Ponieważ konto magazynu platformy Azure jest potencjalnym pojedynczym punktem awarii, ważne jest, aby mieć co najmniej dwa konta magazynu platformy Azure, na których są dystrybuowane co najmniej dwie maszyny wirtualne. W idealnej konfiguracji dyski każdej maszyny wirtualnej z uruchomionym wystąpieniem okna dialogowego SAP zostaną wdrożone na innym koncie magazynu.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Wystąpienie SAP ASCS/SCS o wysokiej dostępności
Rysunek 5 jest przykładem wystąpienia SAP ASCS/SCS o wysokiej dostępności.

![Rysunek 5: Wystąpienie SAP ASCS/SCS o wysokiej dostępności][sap-ha-guide-figure-2001]

_**Rysunek 5:** Wystąpienie SAP ASCS/SCS o wysokiej dostępności_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>Wysoka dostępność wystąpienia SAP ASCS/SCS dzięki klastrowaniu trybu failover systemu Windows Server na platformie Azure
W porównaniu z wdrożeniami w chmurze nieosłoniowej lub prywatnej maszyny wirtualne platformy Azure wymagają dodatkowych kroków w celu skonfigurowania klastrowania trybu failover systemu Windows Server. Aby utworzyć klaster trybu failover systemu Windows, potrzebny jest udostępniony dysk klastrowy, kilka adresów IP, kilka nazw hostów wirtualnych i wewnętrzny moduł równoważenia obciążenia platformy Azure do klastrowania wystąpienia SAP ASCS/SCS. Omówimy to bardziej szczegółowo w dalszej części artykułu.

![Rysunek 6: Klastrowanie trybu failover systemu Windows Server dla konfiguracji SAP ASCS/SCS na platformie Azure przy użyciu menedżera danych SIOS][sap-ha-guide-figure-1002]

_**Rysunek 6:** Klastrowanie trybu failover systemu Windows Server dla konfiguracji SAP ASCS/SCS na platformie Azure z modułem danych SIOS_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Wystąpienie usługi DBMS o wysokiej dostępności
DbMS jest również pojedynczym punktem kontaktowym w systemie SAP. Należy go chronić za pomocą rozwiązania o wysokiej dostępności. Rysunek 7 przedstawia rozwiązanie SQL Server Always On o wysokiej dostępności na platformie Azure z klastrem trybu failover systemu Windows Server i wewnętrznym modułem równoważenia obciążenia platformy Azure. Program SQL Server Always On replikuje pliki danych i dzienników DBMS przy użyciu własnej replikacji dbms. W takim przypadku nie są potrzebne dyski współużytkowane klastra, co upraszcza całą konfigurację.

![Rysunek 7: Przykład wysokiej dostępności SAP DBMS z sql server zawsze włączony][sap-ha-guide-figure-2003]

_**Rysunek 7:** Przykład wysokiej dostępności sap dbms, z SQL Server zawsze włączony_

Aby uzyskać więcej informacji na temat klastrowania programu SQL Server na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, zobacz następujące artykuły:

* [Ręczne konfigurowanie grupy dostępności zawsze włączone w maszynach wirtualnych platformy Azure przy użyciu Menedżera zasobów][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurowanie wewnętrznego modułu równoważenia obciążenia platformy Azure dla grupy dostępności zawsze włączone na platformie Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Kompleksowe scenariusze wdrażania wysokiej dostępności

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenariusz wdrażania przy użyciu szablonu architektonicznego 1

Rysunek 8 przedstawia przykład architektury wysokiej dostępności SAP NetWeaver na platformie Azure dla **jednego** systemu SAP. Ten scenariusz jest skonfigurowany w następujący sposób:

- Jeden dedykowany klaster jest używany dla wystąpienia SAP ASCS/SCS.
- Jeden dedykowany klaster jest używany dla wystąpienia usługi DBMS.
- Wystąpienia serwera aplikacji SAP są wdrażane na własnych dedykowanych maszynach wirtualnych.

![Rysunek 8: Szablon architektoniczny sap o wysokiej dostępności 1 z dedykowanym klastrem dla ASCS/SCS i DBMS][sap-ha-guide-figure-2004]

_**Rysunek 8:** Szablon architektoniczny SAP o wysokiej dostępności 1, dedykowane klastry dla ASCS/SCS i DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenariusz wdrażania przy użyciu szablonu architektonicznego 2

Rysunek 9 przedstawia przykład architektury wysokiej dostępności SAP NetWeaver na platformie Azure dla **jednego** systemu SAP. Ten scenariusz jest skonfigurowany w następujący sposób:

- Jeden dedykowany klaster jest używany **zarówno** dla wystąpienia SAP ASCS/SCS, jak i systemu dbms.
- Wystąpienia serwera aplikacji SAP są wdrażane we własnych dedykowanych maszynach wirtualnych.

![Rysunek 9: Szablon architektoniczny sap o wysokiej dostępności 2 z dedykowanym klastrem dla ASCS/SCS i dedykowanym klastrem dla DBMS][sap-ha-guide-figure-2005]

_**Rysunek 9:** Szablon architektoniczny sap o wysokiej dostępności 2 z dedykowanym klastrem dla ASCS/SCS i dedykowanym klastrem dla systemu DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenariusz wdrażania przy użyciu szablonu architektonicznego 3

Rysunek 10 przedstawia przykład architektury wysokiej dostępności SAP NetWeaver na platformie&gt; Azure &lt;dla&gt; **dwóch** systemów SAP, z &lt;SID1 i SID2 . Ten scenariusz jest skonfigurowany w następujący sposób:

- Jeden dedykowany klaster jest używany **zarówno** dla wystąpienia SAP ASCS/SCS SID1, *jak i* wystąpienia SAP ASCS/SCS SID2 (jeden klaster).
- Jeden dedykowany klaster jest używany dla identyfikatora SID1 usługi DBMS, a inny dedykowany klaster jest używany dla identyfikatora SID2 DBMS (dwa klastry).
- Wystąpienia serwera aplikacji SAP dla systemu SAP SID1 mają własne dedykowane maszyny wirtualne.
- Wystąpienia serwera aplikacji SAP dla systemu SAP SID2 mają własne dedykowane maszyny wirtualne.

![Rysunek 10: Szablon architektoniczny sap o wysokiej dostępności 3 z dedykowanym klastrem dla różnych wystąpień ASCS/SCS][sap-ha-guide-figure-6003]

_**Rysunek 10:** Szablon architektury o wysokiej dostępności SAP 3 z dedykowanym klastrem dla różnych wystąpień ASCS/SCS_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Przygotowanie infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowanie infrastruktury do szablonu architektonicznego 1
Szablony usługi Azure Resource Manager dla systemu SAP ułatwiają wdrażanie wymaganych zasobów.

Szablony trzywarstwowe w usłudze Azure Resource Manager obsługują również scenariusze wysokiej dostępności, takie jak szablon architektury 1, który ma dwa klastry. Każdy klaster jest pojedynczym punktem awarii SAP dla SAP ASCS/SCS i DBMS.

Oto, gdzie można uzyskać szablony usługi Azure Resource Manager dla przykładowego scenariusza, który opisujemy w tym artykule:

* [Obraz portalu Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Aby przygotować infrastrukturę dla szablonu architektonicznego 1:

- W witrynie Azure portal na bloku **Parametry** w polu **DOSTĘPNOŚĆ SYSTEMU** wybierz pozycję **HA**.

  ![Rysunek 11: Ustawianie parametrów usługi Azure Resource Manager o wysokiej dostępności SAP][sap-ha-guide-figure-3000]

_**Wykres 11.** Ustawianie parametrów usługi Azure Resource Manager o wysokiej dostępności sap_


  Szablony tworzą:

  * **Maszyny wirtualne:**
    * Maszyny wirtualne serwera aplikacji SAP: <*numer*>-di-<*SAP*>
    * Maszyny wirtualne klastra ASCS/SCS: <numer <*SAPSystemSID*>-ascs *Number*>
    * Klaster dbms: <*numer*>>-db-<*Number*>

  * **Karty sieciowe dla wszystkich maszyn wirtualnych ze skojarzonymi adresami IP:**
    * <*Numer* *> SAPSystemSID-nic-di-<*>
    * <*Numer sapsystemsid*>-nic-ascs-<*Number*>
    * <*Numer> SAPSystemSID*>-nic-db-<*Number*>

  * **Konta usługi Azure Storage**

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*>-avset-di
    * Maszyny wirtualne klastra SAP ASCS/SCS: <*SAPSystemSID*>-avset-ascs
    * Maszyny wirtualne klastra DBMS: <*SAPSystemSID*>-avset-db

  * **Wewnętrzny moduł równoważenia obciążenia platformy Azure:**
    * Ze wszystkimi portami wystąpienia ASCS/SCS i adresu IP <*SAPSystemSID*>-lb-ascs
    * Ze wszystkimi portami dla programu SQL Server DBMS i adres IP <*SAPSystemSID*>-lb-db

  * **Grupa zabezpieczeń sieci**: <*SAPSystemSID*>-nsg-ascs-0  
    * Z otwartym zewnętrznym portem protokołu RDP (Remote Desktop Protocol) do <*maszyny wirtualnej SAPSystemSID*>-ascs-0

> [!NOTE]
> Wszystkie adresy IP kart sieciowych i wewnętrznych modułów równoważenia obciążenia platformy Azure są domyślnie **dynamiczne.** Zmień je na **statyczne** adresy IP. Opisujemy, jak to zrobić w dalszej części artykułu.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Wdrażanie maszyn wirtualnych z łącznością sieciową firmy (między lokalizacjami) do użycia w środowisku produkcyjnym
W przypadku systemów SAP do produkcji wdrażaj maszyny wirtualne platformy Azure z łącznością sieciową firmy przy użyciu sieci VPN między lokacjami platformy Azure lub usługi Azure ExpressRoute.

> [!NOTE]
> Można użyć wystąpienia sieci wirtualnej platformy Azure. Sieć wirtualna i podsieć zostały już utworzone i przygotowane.
>
>

1. W witrynie Azure portal na **parametry** bloku, w **NEWOREXISTINGSUBNET** pole, wybierz **istniejące**.
2. W polu **PODSIECI** dodaj pełny ciąg przygotowanego podsieci sieci platformy Azure, w którym planujesz wdrożyć maszyny wirtualne platformy Azure.
3. Aby uzyskać listę wszystkich podsieci sieci platformy Azure, uruchom to polecenie programu PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Pole **Identyfikator** zawiera **podsieć**.
4. Aby uzyskać listę wszystkich wartości **PODSIECI,** uruchom to polecenie programu PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **PODSIEĆ** WYGLĄDA NASTĘPUJĄCO:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Wdrażanie wystąpień SAP tylko w chmurze do testowania i demonstracji
System SAP o wysokiej dostępności można wdrożyć w modelu wdrażania tylko w chmurze. Ten rodzaj wdrożenia jest przede wszystkim przydatne w przypadku użycia demo i test. Nie nadaje się do zastosowań produkcyjnych.

- W witrynie Azure portal na **parametry** bloku, w **NEWOREXISTINGSUBNET** pole, wybierz **nowy**. Pozostaw pole **PODSIEĆ** puste.

  Szablon usługi SAP Azure Resource Manager automatycznie tworzy sieć wirtualną platformy Azure i podsieć.

> [!NOTE]
> Należy również wdrożyć co najmniej jedną dedykowaną maszynę wirtualną dla usługi Active Directory i DNS w tym samym wystąpieniu sieci wirtualnej platformy Azure. Szablon nie tworzy tych maszyn wirtualnych.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowanie infrastruktury do szablonu architektonicznego 2

Za pomocą tego szablonu usługi Azure Resource Manager dla usługi SAP można uprościć wdrażanie wymaganych zasobów infrastruktury dla szablonu architektury SAP 2.

Oto, gdzie można uzyskać szablony usługi Azure Resource Manager dla tego scenariusza wdrażania:

* [Obraz portalu Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowanie infrastruktury do szablonu architektonicznego 3

Można przygotować infrastrukturę i skonfigurować sap dla **wielu identyfikatorów SID**. Na przykład można dodać dodatkowe wystąpienie SAP ASCS/SCS do *istniejącej* konfiguracji klastra. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dodatkowego wystąpienia SAP ASCS/SCS w istniejącej konfiguracji klastra w celu utworzenia konfiguracji sap wieloustawowych w usłudze Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster wieloustawowy, możesz użyć szablonów szybkiego startu z wieloma identyfikatorami SID [w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates).
Aby utworzyć nowy klaster wieloustawowy, należy wdrożyć następujące trzy szablony:

* [Szablon ASCS/SCS](#ASCS-SCS-template)
* [Szablon bazy danych](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje zawierają więcej szczegółów na temat szablonów i parametrów, które należy podać w szablonach.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Szablon ASCS/SCS

Szablon ASCS/SCS wdraża dwie maszyny wirtualne, których można użyć do utworzenia klastra trybu failover systemu Windows Server, który obsługuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon wielosuwowy ASCS/SCS, w [szablonie wielosuwowym ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image]wprowadź wartości następujących parametrów:

  - **Prefiks zasobu**.  Ustaw prefiks zasobu, który jest używany do prefiksu wszystkich zasobów, które są tworzone podczas wdrażania. Ponieważ zasoby nie należą tylko do jednego systemu SAP, prefiks zasobu nie jest identyfikatorem SID jednego systemu SAP.  Prefiks musi zawierać od trzech do **sześciu znaków.**
  - **Typ stosu**. Wybierz typ stosu systemu SAP. W zależności od typu stosu moduł równoważenia obciążenia platformy Azure ma jeden (tylko ABAP lub Java) lub dwa (ABAP + Java) prywatne adresy IP na system SAP.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Liczba systemów SAP**. Wybierz liczbę systemów SAP, które chcesz zainstalować w tym klastrze.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
  -  **Nowa lub istniejąca podsieć**. Ustaw, czy należy utworzyć nową sieć wirtualną i podsieć, czy też należy użyć istniejącej podsieci. Jeśli masz już sieć wirtualną połączoną z siecią lokalną, wybierz **istniejący**program .
  -  **Identyfikator podsieci**. Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda następująco: /subscriptions/<*subscription id*>/resourceGroups/<*resource group name*>/providers/Microsoft.Network/virtualNetworks/<virtual *network name*>/subnets/<nazwa *podsieci*>

Szablon wdraża jedno wystąpienie modułu równoważenia obciążenia platformy Azure, które obsługuje wiele systemów SAP.

- Wystąpienia ASCS są skonfigurowane dla numer 00, 10, 20...
- Wystąpienia SCS są skonfigurowane dla numer 01, 11, 21...
- Wystąpienia serwera replikacji formatu ENCS Enqueue (ERS) (tylko w systemie Linux) są skonfigurowane na przykład numer 02, 12, 22...
- Wystąpienia SCS ERS (tylko w systemie Linux) są skonfigurowane na przykład numer 03, 13, 23...

Moduł równoważenia obciążenia zawiera 1 (2 dla linuksa) VIP(ów), 1x VIP dla ASCS/SCS i 1x VIP dla ERS (tylko Linux).

Poniższa lista zawiera wszystkie reguły równoważenia obciążenia (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Porty specyficzne dla systemu Windows dla każdego systemu SAP: 445, 5985
- Porty ASCS (numer instancji x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (numer instancji x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porty ASCS ERS w systemie Linux (numer instancji x2): 33x2, 5x213, 5x214, 5x216
- Porty SCS ERS w systemie Linux (numer instancji x3): 33x3, 5x313, 5x314, 5x316

Moduł równoważenia obciążenia jest skonfigurowany do używania następujących portów sondy (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Wewnętrzny port sondy modułu równoważenia obciążenia ASCS/SCS: 620x0
- Wewnętrzny port sondy modułu równoważenia obciążenia ERS (tylko Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a>Szablon bazy danych

Szablon bazy danych wdraża jedną lub dwie maszyny wirtualne, których można użyć do zainstalowania relacyjnego systemu zarządzania bazami danych (RDBMS) dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon multi-SID bazy danych, w [szablonie multi-SID bazy danych][sap-templates-3-tier-multisid-db-marketplace-image]wprowadź wartości dla następujących parametrów:

- **Sap System Id**. Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator będzie używany jako prefiks dla zasobów, które są wdrażane.
- **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
- **Dbtype**. Wybierz typ bazy danych, którą chcesz zainstalować w klastrze. Wybierz **sql,** jeśli chcesz zainstalować program Microsoft SQL Server. Wybierz **HANA,** jeśli planujesz zainstalować SAP HANA na maszynach wirtualnych. Upewnij się, że wybierz odpowiedni typ systemu operacyjnego: wybierz **windows** dla języka SQL i wybierz dystrybucję systemu Linux dla HANA. Moduł równoważenia obciążenia platformy Azure połączony z maszynami wirtualnymi zostanie skonfigurowany do obsługi wybranego typu bazy danych:
  * **SQL**. Moduł równoważenia obciążenia będzie wyważać obciążenie portu 1433. Upewnij się, że ten port jest używany do konfiguracji programu SQL Server Always On.
  * **HANA**. Moduł równoważenia obciążenia będzie równoważenia obciążenia portów 35015 i 35017. Upewnij się, że zainstalowano SAP HANA o numerze wystąpienia **50**.
  Moduł równoważenia obciążenia użyje portu sondy 62550.
- **Rozmiar systemu Sap**. Ustaw liczbę SAPS, które zapewni nowy system. Jeśli nie masz pewności, ile saps system będzie wymagać, zapytaj partnera sap technologii lub integratora systemu.
- **Dostępność systemu**. Wybierz **HA**.
- **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
- **Identyfikator podsieci**. Wprowadź identyfikator podsieci użytej podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony w ramach wdrożenia szablonu ASCS/SCS.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a>Szablon serwerów aplikacji

Szablon serwerów aplikacji wdraża co najmniej dwie maszyny wirtualne, które mogą być używane jako wystąpienia serwera aplikacji SAP dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon wielu identyfikatorów SID serwerów aplikacji, w [szablonie wielu identyfikatorów SID serwerów aplikacji][sap-templates-3-tier-multisid-apps-marketplace-image]wprowadź wartości dla następujących parametrów:

  -  **Sap System Id**. Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator będzie używany jako prefiks dla zasobów, które są wdrażane.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu Sap**. Liczba SAPS nowy system zapewni. Jeśli nie masz pewności, ile saps system będzie wymagać, zapytaj partnera sap technologii lub integratora systemu.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
  -  **Identyfikator podsieci**. Wprowadź identyfikator podsieci użytej podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony w ramach wdrożenia szablonu ASCS/SCS.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Sieć wirtualna platformy Azure
W naszym przykładzie przestrzeń adresowa sieci wirtualnej platformy Azure jest 10.0.0.0/16. Istnieje jedna podsieć o nazwie **Podsieć**, z zakresem adresów 10.0.0.0/24. Wszystkie maszyny wirtualne i wewnętrzne moduły równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzać żadnych zmian w ustawieniach sieciowych wewnątrz systemu operacyjnego gościa. Obejmuje to adresy IP, serwery DNS i podsieć. Skonfiguruj wszystkie ustawienia sieciowe na platformie Azure. Usługa DHCP (Dynamic Host Configuration Protocol) propaguje ustawienia.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Adresy IP DNS

Aby ustawić wymagane adresy IP DNS, wykonaj następujące czynności.

1. W witrynie Azure portal w bloku **serwerów DNS** upewnij się, że opcja **serwerów DNS** w sieci wirtualnej jest ustawiona na **Niestandardowy system DNS**.
2. Wybierz ustawienia na podstawie typu posiadanej sieci. Więcej informacji zawierają następujące zasoby:
   * Dodaj adresy IP lokalnych serwerów DNS.  
   Lokalne serwery DNS można rozszerzyć na maszyny wirtualne uruchomione na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których uruchomisz usługę DNS.
   * W przypadku wdrożeń izolowanych na platformie Azure: Wdrażanie dodatkowej maszyny wirtualnej w tym samym wystąpieniu sieci wirtualnej, które służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure skonfigurowanych do uruchamiania usługi DNS.

   ![Rysunek 12: Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure][sap-ha-guide-figure-3001]

   _**Wykres 12.** Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure_

   > [!NOTE]
   > Jeśli zmienisz adresy IP serwerów DNS, musisz ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować zmianę i propagować nowe serwery DNS.
   >
   >

W naszym przykładzie usługa DNS jest instalowana i konfigurowana na tych maszynach wirtualnych systemu Windows:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nazwy hostów i statyczne adresy IP dla wystąpienia klastrowanego SAP ASCS/SCS i wystąpienia klastrowanego DBMS

W przypadku wdrażania lokalnego potrzebne są te zastrzeżone nazwy hostów i adresy IP:

| Rola nazwy hosta wirtualnego | Nazwa hosta wirtualnego | Wirtualny statyczny adres IP |
| --- | --- | --- |
| Sap ASCS/SCS pierwsza nazwa hosta wirtualnego klastra (do zarządzania klastrem) |pr1-ascs-vir |10.0.0.42 |
| Nazwa wirtualnego hosta wystąpienia SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nazwa hosta wirtualnego drugiego klastra SAP DBMS (zarządzanie klastrem) |pr1-dbms-vir |10.0.0.32 |

Podczas tworzenia klastra należy utworzyć nazwy hostów wirtualnych **pr1-ascs-vir** i **pr1-dbms-vir** oraz skojarzone adresy IP, które zarządzają samym klastrem. Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [Zbieranie węzłów klastra w konfiguracji klastra][sap-ha-guide-8.12.1].

Pozostałe dwie nazwy hostów wirtualnych, **pr1-ascs-sap** i **pr1-dbms-sap**oraz skojarzone adresy IP, można utworzyć na serwerze DNS. Klastrowane wystąpienie SAP ASCS/SCS i klastrowane wystąpienie DBMS używają tych zasobów. Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ustawianie statycznych adresów IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyn wirtualnych do użycia w klastrze należy ustawić statyczne adresy IP dla wszystkich maszyn wirtualnych. Zrób to w konfiguracji sieci wirtualnej platformy Azure, a nie w systemie operacyjnym gościa.

1. W witrynie Azure portal wybierz pozycję Adres**IP****Ustawienia** > **karty sieciowej** >  **grupy** > zasobów .
2. Na bloku **Adresy IP** w obszarze **Przydział**wybierz pozycję **Statyczne**. W polu **Adres IP** wprowadź adres IP, którego chcesz użyć.

   > [!NOTE]
   > Jeśli zmienisz adres IP karty sieciowej, musisz ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować zmianę.  
   >
   >

   ![Rysunek 13: Ustawianie statycznych adresów IP dla karty sieciowej każdej maszyny wirtualnej][sap-ha-guide-figure-3002]

   _**Wykres 13.** Ustawianie statycznych adresów IP dla karty sieciowej każdej maszyny wirtualnej_

   Powtórz ten krok dla wszystkich interfejsów sieciowych, czyli dla wszystkich maszyn wirtualnych, w tym maszyn wirtualnych, których chcesz użyć w usłudze Active Directory/DNS.

W naszym przykładzie mamy te maszyny wirtualne i statyczne adresy IP:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwsze wystąpienie serwera aplikacji SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Drugie wystąpienie serwera aplikacji SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Ostatnie wystąpienie serwera aplikacji SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Pierwszy węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Drugi węzeł klastra dla wystąpienia ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Pierwszy węzeł klastra dla wystąpienia usługi DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Drugi węzeł klastra dla wystąpienia usługi DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ustawianie statycznego adresu IP wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon usługi SAP Azure Resource Manager tworzy wewnętrzny moduł równoważenia obciążenia platformy Azure, który jest używany dla klastra wystąpień SAP ASCS/SCS i klastra DBMS.

> [!IMPORTANT]
> Adres IP nazwy hosta wirtualnego SAP ASCS/SCS jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia SAP ASCS/SCS: **pr1-lb-ascs**.
> Adres IP wirtualnej nazwy systemu dbms jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia DBMS: **pr1-lb-dbms**.
>
>

Aby ustawić statyczny adres IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure:

1. Początkowe wdrożenie ustawia adres IP wewnętrznego modułu równoważenia obciążenia na **dynamiczny**. W witrynie Azure portal w bloku **Adresy IP** w obszarze **Przydział**wybierz pozycję **Statyczne**.
2. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-ascs** na adres IP nazwy hosta wirtualnego wystąpienia SAP ASCS/SCS.
3. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-dbms** na adres IP nazwy hosta wirtualnego wystąpienia DBMS.

   ![Rysunek 14: Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Wykres 14.** Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS_

W naszym przykładzie mamy dwa wewnętrzne moduły równoważenia obciążenia platformy Azure, które mają te statyczne adresy IP:

| Rola wewnętrznego modułu równoważenia obciążenia platformy Azure | Nazwa wewnętrznego modułu równoważenia obciążenia platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| Wewnętrzny moduł równoważenia obciążenia wystąpienia SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon sap Azure Resource Manager tworzy potrzebne porty:
* Wystąpienie ASCS aap z domyślnym numerem wystąpienia **00**
* Wystąpienie Java SCS z domyślnym numerem wystąpienia **01**

Podczas instalowania wystąpienia SAP ASCS/SCS należy użyć domyślnego numeru wystąpienia **00** dla wystąpienia ASCS ABAP i domyślnego numeru wystąpienia **01** dla wystąpienia java SCS.

Następnie należy utworzyć wymagane wewnętrzne punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver.

Aby utworzyć wymagane wewnętrzne punkty końcowe równoważenia obciążenia, najpierw należy utworzyć te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver ABAP ASCS:

| Nazwa reguły równoważenia usługi/obciążenia | Domyślne numery portów | Betonowe porty dla (wystąpienie ASCS z numerem wystąpienia 00) (ERS z 10) |
| --- | --- | --- |
| Serwer enqueue / *lbrule3200* |32<*numer instancji*> |3200 |
| Serwer wiadomości ABAP / *lbrule3600* |36<*numer instancji*> |3600 |
| Wewnętrzna wiadomość ABAP / *lbrule3900* |39<*numer instancji*> |3900 |
| Serwer wiadomości HTTP / *Lbrule8100* |81<*numer instancji*> |8100 |
| Sap Start Service ASCS HTTP / *Lbrule50013* |5<*numer instancji Numer*>13 |50013 |
| Sap Uruchom usługę ASCS HTTPS / *Lbrule50014* |5<*numer instancji numer*>14 |50014 |
| Replikacja w kolejce / *Lbrule50016* |5<*numer instancji numer*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*numer instancji Numer*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*numer instancji numer*>14 |51014 |
| Wygraj RM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

_**Tabela 1:** Numery portów wystąpień SAP NetWeaver ABAP ASCS_

Następnie należy utworzyć te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver Java SCS:

| Nazwa reguły równoważenia usługi/obciążenia | Domyślne numery portów | Betonowe porty dla (wystąpienie SCS o numerze wystąpienia 01) (ERS z 11) |
| --- | --- | --- |
| Serwer enqueue / *lbrule3201* |32<*numer instancji*> |3201 |
| Serwer bramy / *lbrule3301* |33<*numer instancji*> |3301 |
| Serwer wiadomości Java / *lbrule3900* |39<*numer instancji*> |3901 |
| Serwer wiadomości HTTP / *Lbrule8101* |81<*numer instancji*> |8101 |
| Sap Uruchom usługę SCS HTTP / *Lbrule50113* |5<*numer instancji Numer*>13 |50113 |
| Sap Uruchom usługę SCS HTTPS / *Lbrule50114* |5<*numer instancji numer*>14 |50114 |
| Replikacja w kolejce / *Lbrule50116* |5<*numer instancji numer*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*numer instancji Numer*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*numer instancji numer*>14 |51114 |
| Wygraj RM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

_**Tabela 2:** Numery portów wystąpień SAP NetWeaver Java SCS_

![Rysunek 15: Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3004]

_**Wykres 15:** Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

Ustaw adres IP modułu równoważenia obciążenia **pr1-lb-dbms** na adres IP nazwy hosta wirtualnego wystąpienia dbms.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Jeśli chcesz użyć różnych numerów dla wystąpień SAP ASCS lub SCS, musisz zmienić nazwy i wartości ich portów z wartości domyślnych.

1. W witrynie Azure portal wybierz pozycję >  ** < *Sid*>-lb-ascs Load Balancing****Rules**.
2. W przypadku wszystkich reguł równoważenia obciążenia należących do wystąpienia SAP ASCS lub SCS zmień następujące wartości:

   * Nazwa
   * Port
   * Port zaplecza

   Na przykład, jeśli chcesz zmienić domyślny numer wystąpienia ASCS z 00 na 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

   Oto przykład aktualizacji dla portu *lbrule3200*.

   ![Rysunek 16: Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3005]

   _**Wykres 16.** Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Dodawanie maszyn wirtualnych systemu Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych dodaj maszyny wirtualne do domeny.

![Rysunek 17: Dodawanie maszyny wirtualnej do domeny][sap-ha-guide-figure-3006]

_**Wykres 17:** Dodawanie maszyny wirtualnej do domeny_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Dodawanie wpisów rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS

Moduł równoważenia obciążenia platformy Azure ma wewnętrzny moduł równoważenia obciążenia, który zamyka połączenia, gdy połączenia są bezczynne przez określony czas (limit czasu bezczynności). Procesy robocze SAP w wystąpieniach okien dialogowych otwierają połączenia z procesem enqueue SAP, gdy tylko pierwsze żądanie enqueue/dequeue musi zostać wysłane. Połączenia te zwykle pozostają nawiązane do czasu ponownego uruchomienia procesu pracy lub procesu w kolejce. Jednak jeśli połączenie jest bezczynne przez określony czas, wewnętrzny moduł równoważenia obciążenia platformy Azure zamyka połączenia. Nie stanowi to problemu, ponieważ proces pracy SAP przywraca połączenie z procesem enqueue, jeśli już nie istnieje. Te działania są udokumentowane w ślady dewelopera procesów SAP, ale tworzą dużą ilość dodatkowej zawartości w tych śladów. Warto zmienić protokół TCP/IP `KeepAliveTime` i `KeepAliveInterval` oba węzły klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP, opisanymi w dalszej części artykułu.

Aby dodać wpisy rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS, najpierw dodaj te wpisy rejestru systemu Windows w obu węzłach klastra systemu Windows dla SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (dziesiętne) |
| Wartość |120000 |
| Łącze do dokumentacji |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** Zmienianie pierwszego parametru TCP/IP_

Następnie dodaj te wpisy rejestru systemu Windows w obu węzłach klastra systemu Windows dla SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (dziesiętne) |
| Wartość |120000 |
| Łącze do dokumentacji |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabela 4:** Zmienianie drugiego parametru TCP/IP_

**Aby zastosować zmiany, uruchom ponownie oba węzły klastra**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurowanie klastra klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS

Konfigurowanie klastra klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS obejmuje następujące zadania:

- Zbieranie węzłów klastra w konfiguracji klastra
- Konfigurowanie monitora udziału plików klastra

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Zbieranie węzłów klastra w konfiguracji klastra

1. W Kreatorze dodawania roli i funkcji dodaj klastrowanie trybu failover do obu węzłów klastra.
2. Konfigurowanie klastra trybu failover przy użyciu Menedżera klastrów trybu failover. W Menedżerze klastrów trybu failover wybierz pozycję **Utwórz klaster**, a następnie dodaj tylko nazwę pierwszego klastra, węzeł A. Nie należy jeszcze dodawać drugiego węzła; dodasz drugi węzeł w późniejszym kroku.

   ![Rysunek 18: Dodawanie nazwy serwera lub maszyny wirtualnej pierwszego węzła klastra][sap-ha-guide-figure-3007]

   _**Wykres 18:** Dodawanie nazwy serwera lub maszyny wirtualnej pierwszego węzła klastra_

3. Wprowadź nazwę sieci (nazwę hosta wirtualnego) klastra.

   ![Rysunek 19: Wprowadź nazwę klastra][sap-ha-guide-figure-3008]

   _**Wykres 19.** Wprowadź nazwę klastra_

4. Po utworzeniu klastra uruchom test sprawdzania poprawności klastra.

   ![Rysunek 20: Uruchamianie sprawdzania poprawności klastra][sap-ha-guide-figure-3009]

   _**Wykres 20.** Uruchamianie sprawdzania poprawności klastra_

   W tym momencie można zignorować wszelkie ostrzeżenia dotyczące dysków. Później dodasz monitor udziału plików i dyski udostępnione SIOS. Na tym etapie nie musisz się martwić o kworum.

   ![Rysunek 21: Nie znaleziono dysku kworum][sap-ha-guide-figure-3010]

   _**Wykres 21.** Nie znaleziono dysku kworum_

   ![Rysunek 22: Podstawowy zasób klastra wymaga nowego adresu IP][sap-ha-guide-figure-3011]

   _**Wykres 22.** Podstawowy zasób klastra potrzebuje nowego adresu IP_

5. Zmień adres IP podstawowej usługi klastrowania. Klaster nie może się uruchomić, dopóki nie zmienisz adresu IP podstawowej usługi klastrowania, ponieważ adres IP serwera wskazuje jeden z węzłów maszyny wirtualnej. W tym celu na stronie **Właściwości** zasobu IP podstawowej usługi klastrowania.

   Na przykład musimy przypisać adres IP (w naszym przykładzie **10.0.0.42)** dla nazwy hosta wirtualnego klastra **pr1-ascs-vir**.

   ![Rysunek 23: W oknie dialogowym Właściwości zmień adres IP][sap-ha-guide-figure-3012]

   _**Wykres 23.** W oknie dialogowym **Właściwości** zmień adres IP_

   ![Rysunek 24: Przypisywanie adresu IP zarezerwowanego dla klastra][sap-ha-guide-figure-3013]

   _**Wykres 24.** Przypisywanie adresu IP zarezerwowanego dla klastra_

6. Przewiń nazwę hosta wirtualnego klastra do trybu online.

   ![Rysunek 25: Podstawowa usługa klastra jest uruchomiona i ma prawidłowy adres IP][sap-ha-guide-figure-3014]

   _**Wykres 25.** Podstawowa usługa klastra jest uruchomiona i z poprawnym adresem IP_

7. Dodaj drugi węzeł klastra.

   Teraz, gdy podstawowa usługa klastrowania jest uruchomiona, można dodać drugi węzeł klastra.

   ![Rysunek 26: Dodawanie drugiego węzła klastra][sap-ha-guide-figure-3015]

   _**Wykres 26.** Dodawanie drugiego węzła klastra_

8. Wprowadź nazwę drugiego hosta węzła klastra.

   ![Rysunek 27: Wprowadź nazwę hosta drugiego węzła klastra][sap-ha-guide-figure-3016]

   _**Wykres 27.** Wprowadź nazwę hosta drugiego węzła klastra_

   > [!IMPORTANT]
   > Upewnij się, że pole wyboru **Dodaj wszystkie kwalifikujące się miejsca do magazynu do klastra** **NIE** jest zaznaczone.  
   >
   >

   ![Rysunek 28: Nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

   _**Wykres 28:** **Nie** zaznaczaj pola wyboru_

   Można zignorować ostrzeżenia dotyczące kworum i dysków. Możesz ustawić kworum i udostępnić dysk później, zgodnie z opisem w [instalowanie SIOS DataKeeper Cluster Edition dla dysku współużytkowania klastra SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Rysunek 29: Ignorowanie ostrzeżeń dotyczących kworum dysku][sap-ha-guide-figure-3018]

   _**Wykres 29.** Ignoruj ostrzeżenia dotyczące kworum dysku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurowanie monitora udziału plików klastra

Konfigurowanie monitora udziału plików klastra obejmuje następujące zadania:

- Tworzenie udziału plików
- Ustawianie kworum monitora udziału plików w Menedżerze klastrów trybu failover

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Tworzenie udziału plików

1. Wybierz monitor udziału plików zamiast dysku kworum. SIOS DataKeeper obsługuje tę opcję.

   W przykładach w tym artykule monitor udziału plików znajduje się na serwerze Active Directory/DNS, który jest uruchomiony na platformie Azure. Monitor udziału plików nosi nazwę **domcontr-0**. Ponieważ skonfigurowano połączenie sieci VPN z platformą Azure (za pośrednictwem sieci VPN lokacja lokacja lub usługi Azure ExpressRoute), usługa Active Directory/DNS jest lokalna i nie jest odpowiednia do uruchamiania monitora udziału plików.

   > [!NOTE]
   > Jeśli usługa Active Directory/DNS działa tylko lokalnie, nie konfiguruj monitora udziału plików w lokalnym systemie operacyjnym Active Directory/DNS Windows. Opóźnienie sieci między węzłami klastra działającymi na platformie Azure i w usłudze Active Directory/DNS lokalnie może być zbyt duże i powodować problemy z łącznością. Należy skonfigurować monitora udziału plików na maszynie wirtualnej platformy Azure, która działa w pobliżu węzła klastra.  
   >
   >

   Dysk kworum potrzebuje co najmniej 1024 MB wolnego miejsca. Zalecamy 2048 MB wolnego miejsca na dysk kworum.

2. Dodaj obiekt nazwy klastra.

   ![Rysunek 30: Przypisywanie uprawnień do udziału dla obiektu nazwy klastra][sap-ha-guide-figure-3019]

   _**Rysunek 30:** Przypisywanie uprawnień do udziału dla obiektu nazwy klastra_

   Upewnij się, że uprawnienia obejmują uprawnienia do zmiany danych w udziale obiektu nazwy klastra (w naszym przykładzie **pr1-ascs-vir$**).

3. Aby dodać obiekt nazwy klastra do listy, wybierz pozycję **Dodaj**. Zmień filtr, aby sprawdzić, czy obiekty komputera, oprócz tych pokazanych na rysunku 31.

   ![Rysunek 31: Zmień typy obiektów na komputery][sap-ha-guide-figure-3020]

   _**Wykres 31:** Zmienianie typów obiektów na komputery_

   ![Rysunek 32: Zaznacz pole wyboru Komputery][sap-ha-guide-figure-3021]

   _**Wykres 32.** Zaznacz pole wyboru **Komputery**_

4. Wprowadź obiekt nazwy klastra, jak pokazano na rysunku 31. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 30.

5. Wybierz kartę **Zabezpieczenia** udziału, a następnie ustaw bardziej szczegółowe uprawnienia dla obiektu nazwy klastra.

   ![Rysunek 33: Ustawianie atrybutów zabezpieczeń obiektu nazwy klastra w kworum udziału plików][sap-ha-guide-figure-3022]

   _**Wykres 33.** Ustawianie atrybutów zabezpieczeń obiektu nazwy klastra w kworum udziału plików_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ustawianie kworum monitora udziału plików w Menedżerze klastrów trybu failover

1. Otwórz Kreatora konfigurowania ustawienia kworum.

   ![Rysunek 34: Uruchamianie Kreatora ustawiania konfigurowania kworum klastra][sap-ha-guide-figure-3023]

   _**Wykres 34:** Uruchamianie Kreatora konfigurowania ustawienia kworum klastra_

2. Na stronie **Wybierz konfigurację kworum** wybierz pozycję **Wybierz monitor kworum**.

   ![Rysunek 35: Konfiguracje kworum, które można wybrać][sap-ha-guide-figure-3024]

   _**Wykres 35:** Konfiguracje kworum, które można wybrać_

3. Na stronie **Wybierz monitor kworum** wybierz pozycję **Konfigurowanie monitora udziału plików**.

   ![Rysunek 36: Wybierz monitor udziału plików][sap-ha-guide-figure-3025]

   _**Wykres 36.** Wybieranie monitora udziału plików_

4. Wprowadź ścieżkę UNC do udziału plików \\(w naszym przykładzie domcontr-0\FSW). Aby wyświetlić listę zmian, które można wprowadzić, wybierz przycisk **Dalej**.

   ![Rysunek 37: Definiowanie lokalizacji udziału plików dla udziału świadka][sap-ha-guide-figure-3026]

   _**Wykres 37:** Definiowanie lokalizacji udziału plików dla udziału świadka_

5. Zaznacz żądane zmiany, a następnie wybierz pozycję **Dalej**. Należy pomyślnie skonfigurować konfigurację klastra, jak pokazano na rysunku 38.  

   ![Rysunek 38: Potwierdzenie ponownej konfiguracji klastra][sap-ha-guide-figure-3027]

   _**Wykres 38:** Potwierdzenie ponownej konfiguracji klastra_

Po pomyślnym zainstalowaniu klastra trybu failover systemu Windows należy w wprowadzonych zmiany w niektórych progach, aby dostosować wykrywanie pracy awaryjnej do warunków na platformie Azure. Parametry, które należy zmienić, są [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)udokumentowane w tym blogu: . Zakładając, że dwie maszyny wirtualne, które budują konfigurację klastra systemu Windows dla ASCS/SCS, znajdują się w tej samej podsieci, należy zmienić następujące parametry na następujące wartości:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Te ustawienia zostały przetestowane z klientami i pod warunkiem, że dobry kompromis być wystarczająco odporne z jednej strony. Z drugiej strony te ustawienia zapewniały wystarczająco szybkie przejście awaryjne w rzeczywistych warunkach błędu w oprogramowaniu SAP lub awarii węzła/maszyny wirtualnej. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalowanie programu SIOS DataKeeper Cluster Edition dla dysku współużytkowego klastra SAP ASCS/SCS

Masz teraz działającą konfigurację klastrowania trybu failover systemu Windows Server na platformie Azure. Jednak do zainstalowania wystąpienia SAP ASCS/SCS potrzebny jest zasób dysku współdzielonego. Nie można utworzyć zasobów dysku udostępnionego, których potrzebujesz na platformie Azure. SIOS DataKeeper Cluster Edition to rozwiązanie innej firmy, którego można używać do tworzenia zasobów dysku współdzielonego.

Instalowanie programu SIOS DataKeeper Cluster Edition dla dysku współużytkowego klastra SAP ASCS/SCS obejmuje następujące zadania:

- Dodawanie programu .NET Framework 3.5
- Instalowanie programu SIOS DataKeeper
- Konfigurowanie sios datakeeper

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Dodawanie programu .NET Framework 3.5
Program Microsoft .NET Framework 3.5 nie jest automatycznie aktywowany ani instalowany w systemie Windows Server 2012 R2. Ponieważ program SIOS DataKeeper wymaga, aby program .NET Framework był włączony do wszystkich węzłów, na których można zainstalować program DataKeeper, należy zainstalować program .NET Framework 3.5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania programu .NET Framework 3.5:

- Użyj Kreatora dodawania ról i funkcji w systemie Windows, jak pokazano na rysunku 39.

  ![Rysunek 39: Instalowanie programu .NET Framework 3.5 za pomocą Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3028]

  _**Wykres 39:** Instalowanie programu .NET Framework 3.5 za pomocą Kreatora dodawania ról i funkcji_

  ![Rysunek 40: Pasek postępu instalacji podczas instalowania programu .NET Framework 3.5 przy użyciu Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3029]

  _**Rysunek 40:** Pasek postępu instalacji podczas instalowania programu .NET Framework 3.5 przy użyciu Kreatora dodawania ról i funkcji_

- Użyj narzędzia wiersza polecenia dism.exe. W przypadku instalacji tego typu należy uzyskać dostęp do katalogu SxS na nośniku instalacyjnym systemu Windows. W wierszu polecenia z podwyższonym poziomem uprawnień wpisz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalowanie programu SIOS DataKeeper

Zainstaluj SIOS DataKeeper Cluster Edition w każdym węźle w klastrze. Aby utworzyć wirtualny magazyn udostępniony za pomocą programu SIOS DataKeeper, utwórz zsynchronizowany plik dublowany, a następnie symuluj współdzielony magazyn klastra.

Przed zainstalowaniem oprogramowania SIOS należy utworzyć użytkownika domeny **DataKeeperSvc**.

> [!NOTE]
> Dodaj użytkownika **DataKeeperSvc** do grupy **Administrator lokalny** w obu węzłach klastra.
>
>

Aby zainstalować SIOS DataKeeper:

1. Zainstaluj oprogramowanie SIOS w obu węzłach klastra.

   ![Instalator SIOS][sap-ha-guide-figure-3030]

   ![Rysunek 41: Pierwsza strona instalacji SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Wykres 41:** Pierwsza strona instalacji SIOS DataKeeper_

2. W oknie dialogowym pokazanym na rysunku 42 wybierz pozycję **Tak**.

   ![Rysunek 42: DataKeeper informuje, że usługa zostanie wyłączona][sap-ha-guide-figure-3032]

   _**Wykres 42.** DataKeeper informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym pokazanym na rysunku 43 zaleca się wybranie **konta domeny lub serwera**.

   ![Rysunek 43: Wybór użytkownika dla SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Wykres 43.** Wybór użytkownika dla SIOS DataKeeper_

4. Wprowadź nazwę użytkownika konta domeny i hasła utworzone dla identyfikatora SIOS DataKeeper.

   ![Rysunek 44: Wprowadź nazwę użytkownika domeny i hasło do instalacji SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Wykres 44:** Wprowadź nazwę użytkownika domeny i hasło do instalacji SIOS DataKeeper_

5. Zainstaluj klucz licencyjny dla wystąpienia SIOS DataKeeper, jak pokazano na rysunku 45.

   ![Rysunek 45: Wprowadź klucz licencyjny SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Wykres 45.** Wprowadź klucz licencyjny SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Konfigurowanie sios datakeeper

Po zainstalowaniu SIOS DataKeeper w obu węzłach należy uruchomić konfigurację. Celem konfiguracji jest synchroniczne replikacji danych między dodatkowych dysków VHD dołączonych do każdej z maszyn wirtualnych.

1. Uruchom narzędzie Zarządzanie i konfiguracja menedżera danych, a następnie wybierz pozycję **Połącz serwer**. (Na rysunku 46 ta opcja jest zakreślona na czerwono).

   ![Rysunek 46: Narzędzie do zarządzania i konfiguracji danych SIOS][sap-ha-guide-figure-3036]

   _**Wykres 46:** Narzędzie SIOS DataKeeper Zarządzanie i konfiguracja_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, z który powinno się połączyć narzędzie Zarządzanie i konfiguracja, a w drugim kroku z drugim węzłem.

   ![Rysunek 47: Wstaw nazwę lub adres TCP/IP pierwszego węzła, z który powinno się połączyć narzędzie Zarządzanie i konfiguracja, a w drugim kroku drugi węzeł][sap-ha-guide-figure-3037]

   _**Wykres 47:** Wstaw nazwę lub adres TCP/IP pierwszego węzła, z który powinno się połączyć narzędzie Zarządzanie i konfiguracja, a w drugim kroku drugi węzeł_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Rysunek 48: Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _**Wykres 48:** Tworzenie zadania replikacji_

   Kreator prowadzi użytkownika przez proces tworzenia zadania replikacji.
4. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła źródłowego.

   ![Rysunek 49: Zdefiniuj nazwę zadania replikacji][sap-ha-guide-figure-3039]

   _**Wykres 49:** Definiowanie nazwy zadania replikacji_

   ![Rysunek 50: Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem źródłowym][sap-ha-guide-figure-3040]

   _**Rysunek 50:** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem źródłowym_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Rysunek 51: Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem docelowym][sap-ha-guide-figure-3041]

   _**Wykres 51:** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem docelowym_

6. Zdefiniuj algorytmy kompresji. W naszym przykładzie zaleca się skompresowanie strumienia replikacji. Szczególnie w sytuacjach ponownej synchronizacji kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Należy zauważyć, że kompresja używa zasobów procesora CPU i pamięci RAM maszyny wirtualnej. Wraz ze wzrostem szybkości kompresji zwiększa się również ilość używanych zasobów procesora CPU. Można również dostosować to ustawienie później.

7. Innym ustawieniem, które należy sprawdzić, jest to, czy replikacja odbywa się asynchronicznie lub synchronicznie. *Podczas ochrony konfiguracji SAP ASCS/SCS należy użyć replikacji synchroniczowej*.  

   ![Rysunek 52: Definiowanie szczegółów replikacji][sap-ha-guide-figure-3042]

   _**Wykres 52.** Definiowanie szczegółów replikacji_

8. Określ, czy wolumin replikowany przez zadanie replikacji powinien być reprezentowany w konfiguracji klastra klastra trybu failover systemu Windows Server jako dysk udostępniony. W przypadku konfiguracji SAP ASCS/SCS wybierz opcję **Tak,** aby klaster systemu Windows postrzegał wolumin replikowany jako dysk udostępniony, którego może używać jako wolumin klastra.

   ![Rysunek 53: Wybierz tak, aby ustawić wolumin replikowany jako wolumin klastra][sap-ha-guide-figure-3043]

   _**Wykres 53.** Wybierz **tak,** aby ustawić wolumin replikowany jako wolumin klastra_

   Po utworzeniu woluminu narzędzie Zarządzanie i konfiguracja datakeeper pokazuje, że zadanie replikacji jest aktywne.

   ![Rysunek 54: Synchronizaczne dublowanie DataKeeper dla dysku współużytkującego SAP ASCS/SCS jest aktywne][sap-ha-guide-figure-3044]

   _**Wykres 54.** Aktywne jest synchroniczne dublowanie datakeeper dla dysku współużytkującego SAP ASCS/SCS_

   Menedżer klastrów trybu failover pokazuje teraz dysk jako dysk DataKeeper, jak pokazano na rysunku 55.

   ![Rysunek 55: Menedżer klastra trybu failover pokazuje dysk, który program DataKeeper został zreplikowany][sap-ha-guide-figure-3045]

   _**Wykres 55.** Menedżer klastrów trybu failover pokazuje dysk, który program DataKeeper został zreplikowany_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Zainstaluj system SAP NetWeaver

Nie opiszemy konfiguracji systemu DBMS, ponieważ konfiguracje różnią się w zależności od używanego systemu DBMS. Zakładamy jednak, że problemy z wysoką dostępnością systemu dbms są rozwiązywane z funkcjami różnych dostawców dbms obsługi platformy Azure. Na przykład zawsze włączone lub dublowanie bazy danych dla programu SQL Server i Oracle Data Guard dla baz danych Oracle. W scenariuszu, którego używamy w tym artykule, nie dodaliśmy więcej ochrony do dbms.

Nie ma żadnych specjalnych zagadnień, gdy różne usługi DBMS współdziałają z tego rodzaju klastrowane SAP ASCS/SCS konfiguracji na platformie Azure.

> [!NOTE]
> Procedury instalacji systemów SAP NetWeaver ABAP, Systemów Java i ABAP+Java są niemal identyczne. Najważniejszą różnicą jest to, że system SAP ABAP ma jedno wystąpienie ASCS. System SAP Java ma jedno wystąpienie SCS. System SAP ABAP+Java ma jedno wystąpienie ASCS i jedno wystąpienie SCS uruchomione w tej samej grupie klastrów trybu failover firmy Microsoft. Wszelkie różnice w instalacji dla każdego stosu instalacji SAP NetWeaver są wyraźnie wymienione. Można założyć, że wszystkie pozostałe części są takie same.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalowanie systemu SAP z wystąpieniem ASCS/SCS o wysokiej dostępności

> [!IMPORTANT]
> Pamiętaj, aby nie umieszczać pliku stronicowego na woluminach dublowanych DataKeeper. DataKeeper nie obsługuje woluminów dublowanych. Możesz pozostawić plik strony na dysku tymczasowym D maszyny wirtualnej platformy Azure, która jest domyślna. Jeśli jeszcze go tam nie ma, przenieś plik strony systemu Windows, aby prowadzić D maszyny wirtualnej platformy Azure.
>
>

Instalowanie sap z wysokiej dostępności ASCS/SCS wystąpienie obejmuje następujące zadania:

- Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS
- Instalowanie pierwszego węzła klastra SAP
- Modyfikowanie profilu SAP wystąpienia ASCS/SCS
- Dodawanie portu sondy
- Otwieranie portu sondy zapory systemu Windows

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS

1. W Menedżerze DNS systemu Windows utwórz wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS.

   > [!IMPORTANT]
   > Adres IP przypisany do nazwy hosta wirtualnego wystąpienia ASCS/SCS musi być taki sam jak adres IP przypisany do modułu Równoważenia obciążenia platformy Azure**<*(SID*>-lb-ascs).**  
   >
   >

   Adres IP wirtualnej nazwy hosta SAP ASCS/SCS **(pr1-ascs-sap)** jest taki sam jak adres IP modułu Równoważenia obciążenia platformy Azure (**pr1-lb-ascs**).

   ![Rysunek 56: Definiowanie wpisu DNS dla nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Wykres 56.** Definiowanie wpisu DNS dla nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP_

2. Aby zdefiniować adres IP przypisany do nazwy hosta wirtualnego, wybierz pozycję**Domena** **menedżera** > DNS .

   ![Rysunek 57: Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Wykres 57:** Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalowanie pierwszego węzła klastra SAP

1. Wykonaj pierwszą opcję węzła klastra w węźle klastra A. Na przykład na hoście **pr1-ascs-0.**
2. Aby zachować domyślne porty wewnętrznego modułu równoważenia obciążenia platformy Azure, wybierz:

   * **System ABAP**: numer wystąpienia **ASCS** **00**
   * **System Java**: numer wystąpienia **SCS** **01**
   * **System ABAP+Java:** numer wystąpienia **ASCS** **00** i numer wystąpienia **SCS** **01**

   Aby użyć numerów wystąpień innych niż 00 dla wystąpienia ABAP ASCS i 01 dla wystąpienia Java SCS, należy najpierw zmienić domyślne reguły równoważenia obciążenia wewnętrznego modułu równoważenia obciążenia platformy Azure, opisane w [zmień domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-8.9].

Kilka następnych zadań nie jest opisanych w standardowej dokumentacji instalacji SAP.

> [!NOTE]
> W dokumentacji instalacji sap opisano sposób instalowania pierwszego węzła klastra ASCS/SCS.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modyfikowanie profilu SAP wystąpienia ASCS/SCS

Należy dodać nowy parametr profilu. Parametr profilu zapobiega zamykaniu połączeń między procesami roboczymi SAP a serwerem w kolejce, gdy są one bezczynne przez zbyt długi czas. Wspomnieliśmy o scenariuszu problemu w [Dodaj wpisy rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji wprowadziliśmy również dwie zmiany w niektórych podstawowych parametrach połączenia TCP/IP. W drugim kroku należy ustawić serwer enqueue, `keep_alive` aby wysłać sygnał, tak aby połączenia nie osiągnęły progu bezczynności modułu równoważenia obciążenia platformy Azure.

Aby zmodyfikować profil SAP wystąpienia ASCS/SCS:

1. Dodaj ten parametr profilu do profilu wystąpienia SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   W naszym przykładzie ścieżka jest:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Na przykład do profilu wystąpienia SAP SCS i odpowiedniej ścieżki:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Aby zastosować zmiany, uruchom ponownie wystąpienie SAP ASCS /SCS.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Dodawanie portu sondy

Użyj funkcji sondy modułu wewnętrznego modułu równoważenia obciążenia, aby cała konfiguracja klastra działała z modułem równoważenia obciążenia platformy Azure. Wewnętrzny moduł równoważenia obciążenia platformy Azure zwykle rozdziela przychodzące obciążenie równo między uczestniczące maszyny wirtualne. Jednak to nie będzie działać w niektórych konfiguracjach klastra, ponieważ tylko jedno wystąpienie jest aktywne. Inne wystąpienie jest pasywne i nie można zaakceptować żadnego obciążenia. Funkcja sondy pomaga, gdy wewnętrzny moduł równoważenia obciążenia platformy Azure przypisuje pracę tylko do aktywnego wystąpienia. Dzięki funkcji sondy wewnętrzny moduł równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie kierować tylko wystąpienie z obciążeniem.

Aby dodać port sondy:

1. Sprawdź bieżące ustawienie **ProbePort,** uruchamiając następujące polecenie programu PowerShell. Wykonaj go z poziomu jednej z maszyn wirtualnych w konfiguracji klastra.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Zdefiniuj port sondy. Domyślny numer portu sondy to **0**. W naszym przykładzie używamy portu sondy **62000**.

   ![Rysunek 58: Port sondy konfiguracji klastra jest domyślnie 0][sap-ha-guide-figure-3048]

   _**Wykres 58:** Domyślny port sondy konfiguracji klastra to 0_

   Numer portu jest zdefiniowany w szablonach usługi SAP Azure Resource Manager. Numer portu można przypisać w programie PowerShell.

   Aby ustawić nową wartość ProbePort dla zasobu klastra ** *SAP* <SID> IP,** uruchom następujący skrypt programu PowerShell. Zaktualizuj zmienne programu PowerShell dla swojego środowiska. Po uruchomieniu skryptu zostanie wyświetlony monit o ponowne uruchomienie grupy klastrów SAP w celu aktywowania zmian.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Po przesunieniu roli klastra **SAP <*SID* > ** w tryb online sprawdź, czy **probeport** jest ustawiony na nową wartość.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Rysunek 59: Sondowanie portu klastra po ustawieniu nowej wartości][sap-ha-guide-figure-3049]

   _**Wykres 59.** Sondowanie portu klastra po ustawieniu nowej wartości_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Otwieranie portu sondy zapory systemu Windows

Należy otworzyć port sondy zapory systemu Windows w obu węzłach klastra. Użyj następującego skryptu, aby otworzyć port sondy zapory systemu Windows. Zaktualizuj zmienne programu PowerShell dla swojego środowiska.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** jest ustawiony na **62000**. Teraz możesz uzyskać dostęp do udziału ** \\plików \ascsha-clsap\sapmnt** z innych hostów, takich jak **ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalowanie wystąpienia bazy danych

Aby zainstalować wystąpienie bazy danych, postępuj zgodnie z procesem opisanym w dokumentacji instalacji SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalowanie drugiego węzła klastra

Aby zainstalować drugi klaster, wykonaj kroki opisane w podręczniku instalacji SAP.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Zmienianie typu początkowego wystąpienia usługi SAP ERS Windows

Zmień typ początkowy usługi SAP ERS Windows na **Automatyczny (opóźniony start)** w obu węzłach klastra.

![Rysunek 60: Zmień typ usługi dla wystąpienia SAP ERS na opóźnione automatyczne][sap-ha-guide-figure-3050]

_**Rysunek 60:** Zmień typ usługi dla wystąpienia SAP ERS na opóźnione automatyczne_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalowanie podstawowego serwera aplikacji SAP

Zainstaluj wystąpienie serwera aplikacji podstawowej (PAS) <*identyfikator SID*>-di-0 na maszynie wirtualnej wyznaczonej do obsługi pasu. Nie ma żadnych zależności na platformie Azure lub DataKeeper ustawienia specyficzne dla.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalowanie dodatkowego serwera aplikacji SAP

Zainstaluj serwer aplikacji dodatkowej SAP (AAS) na wszystkich maszynach wirtualnych wyznaczonych do obsługi wystąpienia serwera aplikacji SAP. Na przykład na <*SID*>-di-1 do <*SID*>-di-&lt;n&gt;.

> [!NOTE]
> To kończy instalację systemu SAP NetWeaver o wysokiej dostępności. Następnie należy przystąpić do testowania trybu failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testowanie trybu failover wystąpienia SAP ASCS/SCS i replikacji SIOS
Testowanie i monitorowanie trybu failover wystąpienia SAP ASCS/SCS i replikacji dysku SIOS można łatwo przetestować i monitorować za pomocą Menedżera klastra trybu failover oraz narzędzia SIOS DataKeeper Management and Configuration.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Wystąpienie SAP ASCS/SCS jest uruchomione w węźle klastra A

Grupa klastra **SAP PR1** jest uruchomiona w węźle klastra A. Na przykład na **pr1-ascs-0**. Przypisz współdzielony dysk S, który jest częścią grupy klastrów **SAP PR1** i którego używa wystąpienie ASCS/SCS, do węzła klastra A.

![Rysunek 61: Menedżer klastra trybu failover: grupa>> klastra SAP <SID jest uruchomiona w węźle klastra A][sap-ha-guide-figure-5000]

_**Wykres 61:** Menedżer klastra trybu failover: grupa>> klasteru SAP <*SID* jest uruchomiona w węźle klastra A_

W narzędziu SIOS DataKeeper Zarządzanie i konfiguracja można zobaczyć, że udostępnione dane dysku są synchronicznie replikowane z dysku zbiorczego źródłowego S w węźle klastra A do docelowego dysku woluminu S w węźle klastra B. Na przykład jest replikowany z **pr1-ascs-0 [10.0.0.40]** do **pr1-ascs-1 [10.0.0.41]**.

![Rysunek 62: W sios datakeeper replikuj wolumin lokalny z węzła klastra A do węzła klastra B][sap-ha-guide-figure-5001]

_**Wykres 62.** W aplikacji SIOS DataKeeper replikuj wolumin lokalny z węzła klastra A do węzła klastra B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Przewijaniu w sposób failover z węzła A do węzła B

1. Wybierz jedną z tych opcji, aby zainicjować przebój pracy awaryjnej grupy> klasteru SAP <*SID* z węzła klastra A do węzła klastra B:
   - Korzystanie z Menedżera klastra trybu failover  
   - Korzystanie z programu PowerShell klastra trybu failover

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Uruchom ponownie węzeł klastra A w systemie operacyjnym gościa systemu Windows (inicjuje to automatyczną tryb failover grupy> *klasteru* SAP <SID z węzła A do węzła B).  
3. Uruchom ponownie węzeł A klastra z witryny Azure portal (inicjuje to automatyczną usługę failover <GRUPY klastra SAP> <*SID* z węzła A do węzła B).  
4. Uruchom ponownie węzeł klastra A przy użyciu programu Azure PowerShell (inicjuje to automatyczną usługę failover <grupy klastrów SAP> <*SID* z węzła A do węzła B).

   Po przemienniu w stan failover grupa> klastra SAP <*SID* jest uruchomiona w węźle klastra B. Na przykład działa na **pr1-ascs-1**.

   ![Rysunek 63: W Menedżerze klastra trybu failover grupa <> klastra SAP jest uruchomiona w węźle klastra B][sap-ha-guide-figure-5002]

   _**Rysunek 63**: W Menedżerze klastrów trybu failover grupa> *>* klastra SAP <jest uruchomiona w węźle klastra B_

   Dysk udostępniony jest teraz zamontowany w węźle klastra B. SIOS DataKeeper replikuje dane z dysku woluminu źródłowego S w węźle klastra B do docelowego dysku woluminu S w węźle klastra A. Na przykład replikuje się z **pr1-ascs-1 [10.0.0.41]** do **pr1-ascs-0 [10.0.0.40]**.

   ![Rysunek 64: SIOS DataKeeper replikuje wolumin lokalny z węzła klastra B do węzła klastra A][sap-ha-guide-figure-5003]

   _**Wykres 64.** Moduł danych SIOS replikuje wolumin lokalny z węzła klastra B do węzła klastra A_
