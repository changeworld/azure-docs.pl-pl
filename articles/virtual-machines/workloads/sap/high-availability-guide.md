---
title: Azure maszyn wirtualnych wysokiej dostępności dla programu SAP NetWeaver | Dokumentacja firmy Microsoft
description: Przewodnik wysokiej dostępności dla programu SAP NetWeaver na maszynach wirtualnych platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: goraco
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4d1088f786bdcfabb3095dfb8276c508f97bc23b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Wysoka dostępność dla programu SAP NetWeaver na maszynach wirtualnych Azure

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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Konfiguracja wysokiej dostępności identyfikatora SID multi SAP)


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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktu SAP)
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/resource-group-overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager
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


Maszyny wirtualne platformy Azure to rozwiązanie dla organizacji, które muszą obliczeniowych, magazynu i zasobów sieciowych w czasie minimalnego i bez cykle nabywania długie. Maszyny wirtualne Azure służy do wdrażania aplikacji klasycznej jak SAP NetWeaver na podstawie ABAP, Java i stosu ABAP + Java. Rozszerzenie, niezawodności i dostępności bez dodatkowych lokalnych zasobów. Maszyny wirtualne platformy Azure obsługuje łączności między lokalizacjami, maszynach wirtualnych platformy Azure można zintegrować lokalnej domeny Twojej organizacji, chmur prywatnych i pozioma systemu SAP.

W tym artykule firma Microsoft opisano kroki, które należy wykonać w celu wdrażania systemów SAP wysokiej dostępności na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager. Firma Microsoft przeprowadzi użytkownika przez proces te zadania główne:

* Znajdź prawo SAP uwagi i przewodniki dotyczące instalacji, na liście [zasobów] [ sap-ha-guide-2] sekcji. W tym artykule uzupełnia SAP instalacji dokumentacji i notatek SAP, które są głównej zasoby, które ułatwiają instalowanie i wdrażanie oprogramowania SAP na określonych platformach.
* Dowiedz się różnic między modelu wdrażania usługi Azure Resource Manager i model klasyczny wdrożenia usługi Azure.
* Więcej informacji na temat trybów kworum klastra trybu Failover systemu Windows Server, w którym można wybrać modelu, które jest odpowiednie dla danego wdrożenia usługi Azure.
* Więcej informacji na temat systemu Windows Server Failover Clustering udostępnionego magazynu w usług Azure.
* Dowiedz się, jak chronić pojedynczego punktu z awarii składników jak zaawansowane biznesowych aplikacji programowania (ABAP) SAP centralnej usług (ASCS) / SAP centralnej usługi (SCS) i systemy zarządzania bazami danych (DBMS) i działanie elementów nadmiarowych, takie jak SAP serwera aplikacji, na platformie Azure.
* Wykonaj krok przykład instalację i konfigurację systemu SAP wysokiej dostępności w klastrze systemu Windows Server Failover Clustering na platformie Azure przy użyciu usługi Azure Resource Manager.
* Więcej informacji na temat dodatkowych czynności wymaganych do używania systemu Windows Server Failover Clustering w Azure, ale nie są wymagane w przypadku lokalnego wdrożenia.

Aby uprościć wdrażanie i konfigurowanie, w tym artykule używamy szablony Menedżera zasobów systemu SAP trójwarstwowa wysokiej dostępności. Szablony zautomatyzować wdrożenie całej infrastruktury potrzebnym do systemu SAP wysokiej dostępności. Infrastruktura obsługuje również rozmiaru SAP aplikacji wydajności standardowe (protokoły SAP) systemu SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Wymagania wstępne
Przed rozpoczęciem upewnij się, że spełniają wymagania wstępne, które są opisane w poniższych sekcjach. Ponadto należy sprawdzić wszystkich zasobów wymienionych w [zasobów] [ sap-ha-guide-2] sekcji.

W tym artykule używamy szablonów usługi Azure Resource Manager dla [trójwarstwowa SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Przydatne Omówienie szablonów, zobacz [szablony Menedżera zasobów Azure SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Zasoby
Artykuły te obejmują wdrożenia SAP na platformie Azure:

* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP NetWeaver][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP NetWeaver][deployment-guide]
* [Azure wdrożenia SAP NetWeaver DBMS maszyny wirtualne][dbms-guide]
* [Azure maszyn wirtualnych wysokiej dostępności dla programu SAP NetWeaver (w tym przewodniku)][sap-ha-guide]

> [!NOTE]
> Jeśli to możliwe, możemy umożliwiają łącze do odwołującego się Przewodnik instalacji SAP (zobacz [Przewodnik po instalacji programu SAP][sap-installation-guides]). Wymagania wstępne i informacje o procesie instalacji jest dobrym pomysłem jest dokładnie przeczytać SAP NetWeaver przewodniki instalacji. W tym artykule opisano tylko określone zadania dla systemów opartych na procesorze SAP NetWeaver, których można użyć z maszyn wirtualnych platformy Azure.
>
>

Te informacje SAP są związane z tym tematem SAP na platformie Azure:

| Numer | Stanowisko |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produktów i zmiana rozmiaru |
| [2015553] |SAP na platformie Microsoft Azure: obsługuje wymagania wstępne |
| [1999351] |Rozszerzone monitorowanie Azure dla programu SAP |
| [2178632] |Klucz monitorowania metryki dla SAP na platformie Microsoft Azure |
| [1999351] |Wirtualizacji w systemie Windows: rozszerzonego monitorowania |
| [2243692] |Użycie magazynu SSD Azure — wersja Premium dla wystąpienia systemu DBMS SAP |

Dowiedz się więcej o [ograniczenia subskrypcji platformy Azure][azure-subscription-service-limits-subscription], w tym domyślne ogólne ograniczenia i ograniczenia maksymalnej.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP wysokiej dostępności z usługi Azure Resource Manager a Azure klasycznym modelu wdrażania
Usługi Azure Resource Manager i usługi Azure klasycznych modeli wdrażania różnią się w następujących obszarach:

- Grupy zasobów
- Zależności usługi równoważenia obciążenia wewnętrznego platformy Azure na grupy zasobów platformy Azure
- Obsługa scenariuszy identyfikatora SID multi SAP

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupy zasobów
W systemie Azure Resource Manager można użyć grup zasobów do zarządzania wszystkie zasoby aplikacji w Twojej subskrypcji platformy Azure. Podejście, w grupie zasobów, wszystkie zasoby mają tego samego cyklu życia. Na przykład wszystkie zasoby są tworzone w tym samym czasie i są usuwane w tym samym czasie. Dowiedz się więcej o [grupach zasobów](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Zależności usługi równoważenia obciążenia wewnętrznego platformy Azure na grupy zasobów platformy Azure

W modelu wdrażania klasycznego Azure istnieje zależność między Azure wewnętrznego modułu równoważenia obciążenia (usługa Azure Load Balancer) i grupy usługi chmury. Co wewnętrzny moduł równoważenia obciążenia wymaga jednej grupy usługi w chmurze.

W systemie Azure Resource Manager nie ma potrzeby grupy zasobów platformy Azure, aby użyć modułu równoważenia obciążenia Azure. Środowisko jest prostszy i bardziej elastyczne.

### <a name="support-for-sap-multi-sid-scenarios"></a>Obsługa scenariuszy identyfikatora SID multi SAP

W systemie Azure Resource Manager można zainstalować wielu SAP identyfikator ASCS/SCS wystąpień systemu w jednym klastrze. Identyfikator SID wielu wystąpień jest możliwe dzięki obsłudze wielu adresów IP dla każdego Azure wewnętrznego modułu równoważenia obciążenia.

Aby użyć Azure klasycznym modelu wdrażania, wykonaj procedury opisane w temacie [SAP NetWeaver na platformie Azure: wystąpień klastrowania SAP ASCS/SCS przy użyciu klastra trybu Failover systemu Windows Server na platformie Azure z SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Zdecydowanie zaleca się użycie modelu wdrażania usługi Azure Resource Manager dla instalacji programu SAP. Oferuje wiele korzyści, które nie są dostępne w klasycznym modelu wdrażania. Dowiedz się więcej o usłudze Azure [modele wdrażania][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Klaster trybu Failover serwera systemu Windows
Windows Server Failover Clustering jest podstawą SAP ASCS/SCS instalacji wysokiej dostępności i bazami danych w systemie Windows.

Klaster trybu failover to grupa 1 + n niezależnych serwerów (węzłów), które współpracują ze sobą w celu zwiększenia dostępności aplikacji i usług. W przypadku awarii węzła usługi Windows Server Failover Clustering oblicza liczbę błędów, które mogą wystąpić przy zachowaniu dobrej kondycji klastra zapewnienie aplikacji i usług. Istnieje możliwość z kworum różne tryby osiągnięcia klastra trybu failover.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Tryb kworum
Korzystając z usługi Windows Server Failover Clustering można wybrać z czterech trybów kworum:

* **Większość węzłów**. Głosować na każdym węźle klastra. Klaster działa tylko z większością głosów, czyli z więcej niż połowy głosów. Firma Microsoft zaleca tej opcji w przypadku klastrów nieparzysta liczba węzłów. Na przykład trzech węzłów w klastrze siedem może zakończyć się niepowodzeniem, a aparaturze klastra osiąga większości i kontynuuje działanie.  
* **Większość węzłów i dysków**. Każdy węzeł i wyznaczonych dysku (monitorem dysku) w magazynie klastra można głosowania, gdy są one dostępne i w komunikacie. Klaster działa tylko z większością głosów, oznacza to, z więcej niż połowy głosów. W tym trybie ma sens w środowisku klastra z parzystą liczbą węzłów. Jeśli połowa węzłów i dysk są w trybie online, gdy klaster będzie pozostawał w dobrej kondycji.
* **Węzeł i udziału plików większość**. Każdy węzeł plus udział plików wyznaczonych (monitora udziału plików), który administrator tworzy głosować, niezależnie od tego, czy są dostępne węzły i udziału plików i komunikacji. Klaster działa tylko z większością głosów, oznacza to, z więcej niż połowy głosów. W tym trybie ma sens w środowisku klastra z parzystą liczbą węzłów. Jest on podobny do trybu Większość węzłów i dysków, ale zamiast monitora dysku używa monitora udziału plików. Ten tryb jest łatwa do wdrożenia, ale jeśli udział plików sam nie ma wysokiej dostępności, mogą stać się pojedynczym punktem awarii.
* **Bez większości: Na dysku tylko**. Klaster ma kworum, jeśli jeden węzeł jest dostępny i komunikuje się z określonym dyskiem w magazynie klastra. Tylko węzły, które są także komunikuje się z tego dysku, można dołączyć do klastra. Zaleca się, że nie należy używać tego trybu.

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server awaryjnej lokalnej
Rysunek 1 pokazuje klastra z dwoma węzłami. Jeśli udostępnianie połączenie sieciowe między węzły kończy się niepowodzeniem i zarówno Pozostań węzłów w zapasowej i uruchamiania, dysku kworum lub pliku Określa, który węzeł będzie dostarczać aplikacji i usług klastra. Węzeł, który ma dostęp do udziału pliku lub dysku kworum jest węzeł, który zapewnia, że usługi.

Ponieważ w tym przykładzie użyto dwa węzły klastra, używamy Tryb kworum Większość węzłów i plików udziału. Większość węzłów i dysków również jest prawidłową opcją. W środowisku produkcyjnym zaleca się, że używasz dysku kworum. Można użyć technologii systemu sieci i magazynu o wysokiej dostępności.

![Rysunek 1: Na przykład Windows Server Failover Clustering konfiguracji dla programu SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-1000]

_**Rysunek 1.** przykład Windows Server Failover Clustering konfiguracji dla programu SAP ASCS/SCS na platformie Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Magazyn udostępniony
Rysunek 1 pokazuje również klastra magazynu udostępnionego z dwoma węzłami. W lokalnej klastra magazynu udostępnionego wszystkie węzły w klastrze wykryć magazynu udostępnionego. Mechanizm blokowania chroni dane przed uszkodzeniem. Wszystkie węzły mogą wykryć, jeśli inny węzeł nie powiedzie się. Jeśli jeden węzeł ulegnie awarii, pozostałe węzeł przejmuje zasobów magazynu i zapewnia dostępność usług.

> [!NOTE]
> Nie trzeba udostępnionych dysków wysokiej dostępności z niektórymi aplikacjami systemu DBMS, takich jak z programem SQL Server. SQL Server AlwaysOn replikuje DBMS danych i plików dziennika z lokalnego dysku jednego węzła klastra na lokalny dysk innego węzła klastra. W takim przypadku konfiguracji klastra systemu Windows nie wymaga udostępnionego dysku.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Sieci i rozpoznawanie nazw
Komputery klienckie osiągnąć klastra za pośrednictwem wirtualnego adresu IP i nazwy hostów wirtualnych, które udostępnia serwer DNS. Węzły lokalnymi i serwer DNS może obsługiwać wiele adresów IP.

W typowej instalacji można użyć dwóch lub więcej połączeń sieciowych:

* Dedykowane połączenie z magazynem
* Połączenie z siecią wewnętrzną klastra dla pulsu
* Używanego przez klientów Aby połączyć się z klastrem sieci publicznej

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server awaryjnej na platformie Azure
W porównaniu do bez systemu operacyjnego wdrożenia kompletnego stanu lub prywatnej chmury, maszyn wirtualnych Azure wymaga wykonania dodatkowych kroków do skonfigurowania klastra trybu Failover systemu Windows Server. Podczas tworzenia dysku udostępnionego klastra, należy ustawić kilka adresów IP i nazwy hostów wirtualnych dla wystąpienia programu SAP ASCS/SCS.

W tym artykule omówiono kluczowe założenia i kolejne kroki wymagane do utworzenia klastra usług o wysokiej dostępności centralnej SAP na platformie Azure. Firma Microsoft dowiesz się, jak skonfigurować narzędzia innej firmy SIOS DataKeeper oraz konfigurowania Azure wewnętrznego modułu równoważenia obciążenia. Te narzędzia służy do tworzenia klastra trybu failover systemu Windows z monitora udziału plików na platformie Azure.

![Rysunek 2: Windows Server Failover Clustering konfiguracji platformy Azure bez udostępnionego dysku][sap-ha-guide-figure-1001]

_**Rysunek 2.** Windows Server Failover Clustering konfiguracji na platformie Azure, bez udostępnionego dysku_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Udostępniony dysk na platformie Azure z SIOS DataKeeper
Należy klastra magazynu udostępnionego dla wystąpienia programu SAP ASCS/SCS wysokiej dostępności. Począwszy od września 2016 r. Azure nie oferuje udostępnionego magazynu, którego można używać do tworzenia klastra magazynu udostępnionego. Oprogramowanie innych firm SIOS DataKeeper Cluster Edition umożliwia utworzenie magazynu dublowanej, która symuluje magazyn udostępniony klastra. Rozwiązanie SIOS zapewnia Replikacja synchroniczna danych w czasie rzeczywistym. Jest to tworzenia zasobu dysku udostępnionego dla klastra:

1. Dołącz dodatkowe Azure wirtualnego dysku twardego (VHD) na wszystkich maszynach wirtualnych (VM) w konfiguracji klastra systemu Windows.
2. Uruchamianie SIOS DataKeeper Cluster Edition w obu węzłach maszyny wirtualnej.
3. Skonfiguruj SIOS DataKeeper Cluster Edition, aby go odzwierciedla zawartość dodatkowe wolumin dysku VHD dołączony ze źródłowej maszyny wirtualnej dodatkowe rozmiaru wirtualnego dysku twardego dołączony docelowej maszyny wirtualnej. SIOS DataKeeper abstracts lokalnych woluminów źródłowych i docelowych, a następnie je do systemu Windows Server Failover Clustering jako jeden dysk udostępniony.

Uzyskaj więcej informacji [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Rysunek 3: Windows Server Failover Clustering konfiguracji na platformie Azure z SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Rysunek 3.** konfiguracji klastra trybu Failover systemu Windows Server na platformie Azure z SIOS DataKeeper_

> [!NOTE]
> Nie potrzebujesz wysokiej dostępności z niektórych produktów, bazami danych, takich jak SQL Server udostępnione dyski. SQL Server AlwaysOn replikuje DBMS danych i plików dziennika z lokalnego dysku jednego węzła klastra na lokalny dysk innego węzła klastra. W takim przypadku konfiguracji klastra systemu Windows nie wymaga udostępnionego dysku.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Rozpoznawanie nazw w systemie Azure
Chmury Azure platforma nie oferuje opcję, aby skonfigurować wirtualne adresy IP, takie jak adresy IP zmiennoprzecinkowych. Należy rozwiązań alternatywnych, aby skonfigurować wirtualny adres IP do zasobu klastra w chmurze.
Platforma Azure ma wewnętrznego modułu równoważenia obciążenia w usłudze równoważenia obciążenia Azure. Z wewnętrznego modułu równoważenia obciążenia klienci osiągnąć klastra za pośrednictwem wirtualnego adresu IP klastra.
Należy wdrożyć wewnętrznego modułu równoważenia obciążenia w grupie zasobów, która zawiera węzły klastra. Następnie należy skonfigurować wszystkie niezbędne portu przekazywania reguły z badaniem porty wewnętrznego modułu równoważenia obciążenia.
Klienci mogą łączyć się za pomocą nazwy hostów wirtualnych. Serwer DNS rozpoznaje adres IP klastra, a port dojść modułu równoważenia obciążenia wewnętrznego przekazywania do aktywnego węzła klastra.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver wysokiej dostępności w Azure infrastruktury jako — usługa (IaaS)
Aby osiągnąć wysoką dostępność aplikacji SAP, takie jak dla składników oprogramowania SAP, musisz ochronę następujących składników:

* Wystąpienie serwera aplikacji SAP
* Wystąpienie programu SAP ASCS/SCS
* Serwer systemu DBMS

Aby uzyskać więcej informacji o ochronie składników SAP w scenariuszach wysokiej dostępności, zobacz [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver](planning-guide.md).

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Serwer aplikacji SAP wysokiej dostępności
Zwykle nie trzeba konkretnego rozwiązania wysokiej dostępności dla wystąpień serwera aplikacji SAP i okna dialogowe. Zapewniania wysokiej dostępności przez nadmiarowości i wiele wystąpień w oknie dialogowym należy skonfigurować w różnych wystąpień maszyn wirtualnych Azure. Powinien mieć co najmniej dwóch wystąpień aplikacji SAP zainstalowane w dwóch wystąpień maszyn wirtualnych Azure.

![Rysunek 4: SAP wysokiej dostępności aplikacji serwera][sap-ha-guide-figure-2000]

_**Rysunek 4:** SAP wysokiej dostępności serwera aplikacji_

Należy umieścić wszystkich maszyn wirtualnych, które ustawić wystąpień serwera aplikacji SAP hosta w tym samym dostępności Azure. Zestaw dostępności Azure upewnia się, że:

* Wszystkie maszyny wirtualne są częścią tej samej domeny uaktualnienia. Domeny uaktualnienia, na przykład upewnia się, że maszyny wirtualne nie są aktualizowane w tym samym czasie podczas zaplanowanej konserwacji przestoju.
* Wszystkie maszyny wirtualne są częścią tej samej domenie błędów. Domeny błędów, na przykład upewnia się, że maszyny wirtualne są wdrażane tak, aby nie pojedynczego punktu awarii wpływa na dostępność maszyn wirtualnych.

Dowiedz się więcej o sposobie [Zarządzaj dostępnością maszyn wirtualnych][virtual-machines-manage-availability].

Konto magazynu Azure jest potencjalnych pojedynczy punkt awarii, należy mieć co najmniej dwa konta magazynu platformy Azure, w których dystrybuowane są przynajmniej dwie maszyny wirtualne. W Instalatorze idealne dyski każdej maszyny wirtualnej, w którym jest uruchomione wystąpienie okna dialogowego SAP będzie można wdrożyć w innego konta magazynu.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Wystąpienie programu SAP ASCS/SCS wysokiej dostępności
Rysunek 5 jest przykładem wystąpienia SAP ASCS/SCS wysokiej dostępności.

![Rysunek 5: Wystąpienie wysokiej dostępności SAP ASCS/SCS][sap-ha-guide-figure-2001]

_**Rysunek 5.** SAP wysokiej dostępności ASCS/SCS wystąpienia_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS wystąpienie wysokiej dostępności z systemu Windows Server Failover Clustering na platformie Azure
W porównaniu do bez systemu operacyjnego wdrożenia kompletnego stanu lub prywatnej chmury, maszyn wirtualnych Azure wymaga wykonania dodatkowych kroków do skonfigurowania klastra trybu Failover systemu Windows Server. W celu zbudowania klastra pracy awaryjnej systemu Windows, należy dysku udostępnionego klastra, kilka adresów IP, kilka nazw hostów wirtualnych i Azure wewnętrznego modułu równoważenia obciążenia dla klastra z wystąpieniem SAP ASCS/SCS. Omówiono to bardziej szczegółowo w dalszej części tego artykułu.

![Rysunek 6: Windows Server Failover Clustering konfiguracji SAP ASCS/SCS na platformie Azure przy użyciu SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Rysunek 6.** Windows Server Failover Clustering konfiguracji SAP ASCS/SCS na platformie Azure z SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Wystąpienie systemu DBMS wysokiej dostępności
Systemu DBMS jest również pojedynczy punkt kontaktu w systemie SAP. Należy chronić go przy użyciu rozwiązania wysokiej dostępności. Rysunek nr 7 przedstawia rozwiązania wysokiej dostępności programu SQL Server zawsze na platformie Azure, Windows Server Failover Clustering i Azure wewnętrzne usługi równoważenia obciążenia. SQL Server AlwaysOn replikuje DBMS danych i plików dziennika przy użyciu własnego systemu DBMS replikacji. W takim przypadku można nie muszą klastra udostępnione dyski, które upraszcza całą konfigurację.

![Rysunek 7: Przykładem DBMS SAP wysokiej dostępności, z programu SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Rysunek 7.** przykład DBMS SAP wysokiej dostępności, z programu SQL Server AlwaysOn_

Aby uzyskać więcej informacji o klastrach programu SQL Server na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager zobacz następujące artykuły:

* [Zawsze włączone grupy dostępności w maszynach wirtualnych platformy Azure ręcznie skonfigurować za pomocą Menedżera zasobów][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurowanie usługi Azure wewnętrznego modułu równoważenia obciążenia dla grupy dostępności AlwaysOn na platformie Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scenariusze wdrażania wysokiej dostępności na trasie

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenariusz wdrażania przy użyciu architektury 1 szablonu

Rysunek nr 8 przedstawia przykład architektury wysokiej dostępności programu SAP NetWeaver Azure **jeden** systemu SAP. Ten scenariusz jest skonfigurowane w następujący sposób:

- Jeden dedykowanego klastra jest używany dla wystąpienia programu SAP ASCS/SCS.
- Jeden dedykowanego klastra jest używany dla wystąpienia systemu DBMS.
- SAP wystąpień serwera aplikacji są wdrażane w ich własnych dedykowanych maszyn wirtualnych.

![Rysunek 8: SAP wysokiej dostępności architektury szablonu 1, za pomocą dedykowanego klastra ASCS/SCS i bazami danych][sap-ha-guide-figure-2004]

_**Rysunek 8.** SAP architektury 1 szablonu wysokiej dostępności, dedykowane klastry ASCS/SCS i bazami danych_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenariusz wdrażania przy użyciu architektury 2 szablonu

Na rysunku nr 9 przedstawiono przykład architektury wysokiej dostępności programu SAP NetWeaver Azure **jeden** systemu SAP. Ten scenariusz jest skonfigurowane w następujący sposób:

- Jeden klaster dedykowanych służy do **zarówno** wystąpienia SAP ASCS/SCS i systemu DBMS.
- SAP wystąpień serwera aplikacji są wdrażane w własnych dedykowanych maszyn wirtualnych.

![Rysunek 9: SAP wysokiej dostępności architektury szablonu 2, dedykowanego klastra dla ASCS/SCS i dedykowanego klastra dla systemu DBMS][sap-ha-guide-figure-2005]

_**Rysunek 9:** SAP wysokiej dostępności architektury szablonu 2, dedykowanego klastra dla ASCS/SCS i dedykowanego klastra dla systemu DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenariusz wdrażania przy użyciu architektury 3 szablonu

Na rysunku nr 10 przedstawiono architekturę SAP NetWeaver wysokiej dostępności platformy Azure dla **dwóch** SAP systemy, z &lt;SID1&gt; i &lt;SID2&gt;. Ten scenariusz jest skonfigurowane w następujący sposób:

- Jeden dedykowanego klastra jest używany do **zarówno** z wystąpieniem SAP ASCS/SCS SID1 *i* z wystąpieniem SAP ASCS/SCS SID2 (jednego klastra).
- Jeden dedykowanego klastra jest używany dla systemu DBMS SID1 i innym dedykowanego klastra jest używany dla systemu DBMS SID2 (dwa klastry).
- SAP wystąpień serwera aplikacji dla systemu SAP SID1 ma swoje własne dedykowane maszyny wirtualne.
- SAP wystąpień serwera aplikacji dla systemu SAP SID2 ma swoje własne dedykowane maszyny wirtualne.

![Rysunek 10: Wysoka dostępność architektury szablonu 3, z dedykowanym klastrem w różnych wystąpieniach ASCS/SCS SAP][sap-ha-guide-figure-6003]

_**Rysunek 10:** wysokiej dostępności architektury szablonu 3, z dedykowanym klastrem w różnych wystąpieniach ASCS/SCS SAP_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Przygotowanie infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowanie infrastruktury dla architektury 1 szablonu
Szablony usługi Azure Resource Manager dla programu SAP uprościć wdrażanie wymaganych zasobów.

Trójwarstwowa szablonów usługi Azure Resource Manager obsługuje również scenariuszy wysokiej dostępności, takich jak w architektury 1 szablon, który ma dwa klastry. Każdy klaster jest SAP pojedynczego punktu awarii dla programu SAP ASCS/SCS i bazami danych.

Oto, gdzie można uzyskać szablonów usługi Azure Resource Manager przykładowy scenariusz, który opisano w tym artykule:

* [Azure Marketplace image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Aby przygotować infrastrukturę do architektury szablon 1:

- W portalu Azure na **parametry** bloku, w **SYSTEMAVAILABILITY** wybierz opcję **HA**.

  ![Rysunek 11: Ustaw parametry usługi Azure Resource Manager wysokiej dostępności SAP][sap-ha-guide-figure-3000]

_**Rysunek 11:** ustawić parametry usługi Azure Resource Manager wysokiej dostępności SAP_


  Tworzenie szablonów:

  * **Maszyny wirtualne**:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*> - podpisane — <*numer*>
    * Maszyny wirtualne klastra ASCS/SCS: <*SAPSystemSID*> - ascs - <*numer*>
    * Klaster systemu DBMS: <*SAPSystemSID*> - db - <*numer*>

  * **Sieci karty dla wszystkich maszyn wirtualnych adresów IP skojarzonych**:
    * <*SAPSystemSID*> - karta sieciowa - di - <*numer*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*> - karta sieciowa - db - <*numer*>

  * **Konta magazynu platformy Azure**

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*> - avset - podpisane
    * Maszyny wirtualne klastra SAP ASCS/SCS: <*SAPSystemSID*> - avset - ascs
    * System DBMS klastrowanie maszyn wirtualnych: <*SAPSystemSID*> - avset - db

  * **Azure wewnętrznego modułu równoważenia obciążenia**:
    * Z wszystkimi portami dla wystąpienia ASCS/SCS i adresu IP <*SAPSystemSID*> - lb - ascs
    * Z wszystkimi portami dla adresu IP i bazami danych programu SQL Server <*SAPSystemSID*> - lb - db

  * **Grupy zabezpieczeń sieci**: <*SAPSystemSID*> - nsg - ascs-0  
    * Z otwartego portu zewnętrznego protokołu RDP (Remote Desktop) do <*SAPSystemSID*> - ascs - 0 maszyny wirtualnej

> [!NOTE]
> Wszystkie adresy IP Azure wewnętrzne moduły równoważenia obciążenia i karty sieciowe są **dynamiczne** domyślnie. Zmień je, aby **statycznych** adresów IP. Firma Microsoft opisano, jak to zrobić w dalszej części tego artykułu.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Wdrażanie maszyn wirtualnych z połączeniem sieci firmowej (między lokalizacjami) do użycia w środowisku produkcyjnym
Dla systemów SAP produkcyjnych, wdrażanie maszyn wirtualnych platformy Azure z [łączności z siecią firmową (między lokalizacjami)] [ planning-guide-2.2] za pomocą usługi Azure Site to Site VPN lub rozwiązania Azure ExpressRoute.

> [!NOTE]
> Można użyć wystąpienia sieci wirtualnej platformy Azure. Sieć wirtualna i podsieć już zostały utworzone i przygotowane.
>
>

1.  W portalu Azure na **parametry** bloku, w **NEWOREXISTINGSUBNET** wybierz opcję **istniejących**.
2.  W **SUBNETID** Dodaj pełne ciąg przygotowanego sieci Azure SubnetID, w którym zamierzasz wdrożyć maszyny wirtualne Azure.
3.  Aby uzyskać listę wszystkich podsieci w sieci Azure, uruchom następujące polecenie programu PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  **Identyfikator** pola pokazuje **SUBNETID**.
4. Aby uzyskać listę wszystkich **SUBNETID** wartości, uruchom to polecenie programu PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  **SUBNETID** wygląda podobnie do następującej:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Wdrażanie wystąpień SAP tylko w chmurze dla testu i pokaz
Można wdrożyć systemu SAP wysokiej dostępności w modelu wdrażania tylko w chmurze. Tego rodzaju wdrożenia przede wszystkim jest przydatna do demo i test przypadki użycia. Nie nadaje się do przypadków użycia w środowisku produkcyjnym.

- W portalu Azure na **parametry** bloku, w **NEWOREXISTINGSUBNET** wybierz opcję **nowe**. Pozostaw **SUBNETID** pole puste.

  Szablon SAP usługi Azure Resource Manager automatycznie tworzy sieci wirtualnej platformy Azure i podsieć.

> [!NOTE]
> Należy również wdrożyć co najmniej jednej maszyny wirtualnej dedykowanego dla usługi Active Directory i DNS w tym samym wystąpieniu sieci wirtualnej platformy Azure. Szablon nie tworzy tych maszyn wirtualnych.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowanie infrastruktury dla architektury 2 szablonu

Ten szablon Menedżera zasobów Azure dla programu SAP służy do uproszczenia wdrażania zasobów wymaganej infrastruktury dla programu SAP architektury 2 szablonu.

Oto, gdzie można uzyskać szablonów usługi Azure Resource Manager dla tego scenariusza wdrażania:

* [Azure Marketplace image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowanie infrastruktury dla architektury 3 szablonu

Można przygotować infrastrukturę i skonfigurować SAP dla **multi-SID**. Na przykład można dodać dodatkowe wystąpienia SAP ASCS/SCS do *istniejących* konfiguracji klastra. Aby uzyskać więcej informacji, zobacz [skonfigurować dodatkowe wystąpienia SAP ASCS/SCS do istniejącej konfiguracji klastra do utworzenia konfiguracji wielu SID SAP w usłudze Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster multi-identyfikator SID, można użyć identyfikatora SID multi [szablony szybkiego startu w serwisie GitHub](https://github.com/Azure/azure-quickstart-templates).
Aby utworzyć nowy klaster multi-identyfikator SID, należy wdrożyć trzy następujące szablony:

* [ASCS/SCS szablonu](#ASCS-SCS-template)
* [Szablon bazy danych](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje mają więcej szczegółów na temat szablonów oraz parametry, które należy podać w szablonach.

#### <a name="ASCS-SCS-template"></a> ASCS/SCS szablonu

Szablon ASCS/SCS wdraża dwie maszyny wirtualne, których można utworzyć klaster trybu failover systemu Windows Server, który obsługuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon identyfikatora SID multi ASCS/SCS, w [szablon identyfikatora SID multi ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image], wprowadź wartości dla następujących parametrów:

  - **Prefiks zasobów**.  Ustaw prefiks zasobu, który jest używany do prefiksu wszystkie zasoby, które są tworzone podczas wdrażania. Ponieważ zasoby nie należą do tylko jednej systemu SAP, prefiks zasobu nie jest częścią identyfikatora SID jednego systemu SAP.  Prefiks musi należeć do zakresu od **trzech do sześciu znaków**.
  - **Na stosie typu**. Wybierz typ stosu systemu SAP. W zależności od typu stosu modułu równoważenia obciążenia Azure ma jeden (ABAP lub tylko Java) lub dwóch (ABAP + Java) prywatnych adresów IP na systemu SAP.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Liczba systemu SAP**. Wybierz liczbę systemów SAP, który ma zostać zainstalowany w tym klastrze.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika i hasło administratora**. Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
  -  **Nowy lub istniejący podsieci**. Określ, czy należy utworzyć nową sieć wirtualną i podsieć lub istniejącą podsieć powinna być używana. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz **istniejących**.
  -  **Identyfikator podsieci**. Ustawienia identyfikatorów podsieci, do którego powinny być połączone maszyn wirtualnych. Wybierz podsieć sieci wirtualnej sieci prywatnej (VPN) lub usługi sieci wirtualnej sieci lokalnej nawiązać połączenia z maszyną wirtualną. Identyfikator zwykle wygląda następująco:

   /Subscriptions/ <*identyfikator subskrypcji*> /resourceGroups/ <*Nazwa grupy zasobów*> /providers/Microsoft.Network/virtualNetworks/ <*wirtualnej nazwy sieciowej*> /subnets/ <*nazwy podsieci*>

Szablon wdraża jedno wystąpienie usługi równoważenia obciążenia Azure, która obsługuje wiele systemów SAP.

- Wystąpienia ASCS powinny być skonfigurowane dla liczby wystąpień 00, 10, 20...
- Wystąpienia SCS powinny być skonfigurowane dla liczby wystąpień 01 11, 21...
- Wystąpienia ASCS umieścić w kolejce replikacji serwera (Wywołujących) (tylko w systemie Linux) są skonfigurowane dla liczby wystąpień 02, 12, 22...
- Wystąpienia Wywołujących SCS (tylko w systemie Linux) są skonfigurowane dla liczby wystąpień 03, 13, 23...

Moduł równoważenia obciążenia zawiera 1 (2 dla systemu Linux) VIP(s), 1 x dla ASCS/SCS i 1 x adres VIP dla Wywołujących (tylko w systemie Linux).

Poniższa lista zawiera wszystkie reguły, o których (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...) równoważenia obciążenia:
- Porty właściwe dla systemu Windows dla każdego systemu SAP: 445, 5985
- Porty ASCS (liczby wystąpień x0): 32 x 0, 36 x 0, 39 x 0, 81 x 0, 5 x 013, 5 x 014, 5 x 016
- Porty SCS (liczby wystąpień x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- Wywołujących ASCS porty w systemie Linux (liczby wystąpień x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Wywołujących SCS porty w systemie Linux (liczby wystąpień x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Moduł równoważenia obciążenia jest skonfigurowana do używania następujące porty sondowania (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Port sondy modułu równoważenia obciążenia wewnętrznego ASCS/SCS: 620 x 0
- Wewnętrzny Wywołujących załadować port sondy modułu równoważenia (tylko w systemie Linux): 621 x 2

#### <a name="database-template"></a> Szablon bazy danych

Szablon bazy danych wdraża co najmniej dwóch maszyn wirtualnych, które służy do instalowania systemu zarządzania relacyjnej bazy danych (RDBMS) dla jednego systemu SAP. Na przykład jeśli wdrożono szablon ASCS/SCS pięć systemów SAP, należy wdrożyć tego szablonu pięć razy.

Aby skonfigurować szablon identyfikatora SID multi bazy danych, w [szablonu bazy danych wielu SID][sap-templates-3-tier-multisid-db-marketplace-image], wprowadź wartości dla następujących parametrów:

  -  **Identyfikator systemu SAP**. Wprowadź identyfikator systemu SAP systemu SAP, które chcesz zainstalować. Identyfikator będzie służyć jako prefiks dla zasobów, które zostały wdrożone.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Wartość DbType**. Wybierz typ bazy danych, którą chcesz zainstalować w klastrze. Wybierz **SQL** Jeśli chcesz zainstalować program Microsoft SQL Server. Wybierz **HANA** Jeśli zamierzasz zainstalować SAP HANA na maszynach wirtualnych. Upewnij się, że wybrano prawidłowy typ systemu operacyjnego: Wybierz **Windows** SQL i wybierz opcję dystrybucji systemu Linux HANA. Równoważenia obciążenia Azure, który jest podłączony do maszyny wirtualnej zostanie skonfigurowany do obsługi typu wybranej bazy danych:
    * **SQL**. Moduł równoważenia obciążenia będzie Równoważenie obciążenia portu 1433. Upewnij się użyć tego portu dla ustawień programu SQL Server AlwaysOn.
    * **HANA**. Moduł równoważenia obciążenia będzie Równoważenie obciążenia portów 35015 i 35017. Upewnij się zainstalować SAP HANA z liczby wystąpień **50**.
    Moduł równoważenia obciążenia będzie używać portu sondowania 62550.
  -  **Rozmiar systemu SAP**. Ustaw liczbę protokoły SAP zapewnia nowy system. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj SAP technologii partnera lub Integrator systemu.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika i hasło administratora**. Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
  -  **Identyfikator podsieci**. Wprowadź identyfikator podsieci, który został użyty podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony jako część wdrożenia szablonu ASCS/SCS.

#### <a name="application-servers-template"></a> Szablon serwerów aplikacji

Szablon aplikacji serwerów wdraża dwóch lub więcej maszyn wirtualnych, które mogą służyć jako wystąpień serwera aplikacji SAP jednego systemu SAP. Na przykład jeśli wdrożono szablon ASCS/SCS pięć systemów SAP, należy wdrożyć tego szablonu pięć razy.

Aby skonfigurować szablon identyfikatora SID wielu serwerów aplikacji, w [szablon identyfikatora SID wielu serwerów aplikacji][sap-templates-3-tier-multisid-apps-marketplace-image], wprowadź wartości dla następujących parametrów:

  -  **Identyfikator systemu SAP**. Wprowadź identyfikator systemu SAP systemu SAP, które chcesz zainstalować. Identyfikator będzie służyć jako prefiks dla zasobów, które zostały wdrożone.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu SAP**. Liczba protokoły SAP zapewnia nowy system. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj SAP technologii partnera lub Integrator systemu.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika i hasło administratora**. Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
  -  **Identyfikator podsieci**. Wprowadź identyfikator podsieci, który został użyty podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony jako część wdrożenia szablonu ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Sieć wirtualna platformy Azure
W naszym przykładzie przestrzeni adresowej sieci wirtualnej platformy Azure jest 10.0.0.0/16. Brak jednej podsieci o nazwie **podsieci**, z zakresu adresów 10.0.0.0/24. Wszystkie maszyny wirtualne i wewnętrzne moduły równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzono żadnych zmian w ustawieniach sieci w systemie operacyjnym gościa. W tym adresy IP, serwery DNS i podsieci. Skonfiguruj ustawienia sieci na platformie Azure. Usługa protokołu dynamicznej konfiguracji hosta (DHCP) propaguje ustawienia.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Adresy IP serwera DNS

Aby ustawić wymagany adres IP DNS adresów, wykonaj następujące kroki.

1.  W portalu Azure na **serwerów DNS** bloku, upewnij się, że sieci wirtualnej **serwerów DNS** ustawiono opcję **niestandardowe DNS**.
2.  Wybierz ustawienia, na podstawie typu sieci, z którą masz. Więcej informacji zawierają następujące zasoby:
    * [Łączność sieci firmowej (między lokalizacjami)][planning-guide-2.2]: Dodaj adresy IP lokalnych serwerów DNS.  
    Można rozszerzyć lokalnych serwerów DNS do maszyn wirtualnych, które są uruchomione na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których uruchomiono usługi DNS.
    * [Tylko w chmurze wdrożenia][planning-guide-2.1]: wdrożyć dodatkowe maszyny wirtualnej, w tym samym wystąpieniu sieci wirtualnej, która służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure, które zostały skonfigurowane do uruchamiania usługi DNS.

    ![Rysunek 12: Należy skonfigurować serwery DNS dla sieci wirtualnej platformy Azure][sap-ha-guide-figure-3001]

    _**Rysunek 12:** DNS Konfigurowanie serwerów na potrzeby sieci wirtualnej platformy Azure_

  > [!NOTE]
  > W przypadku zmiany adresów IP serwerów DNS, musisz ponownie uruchomić maszyn wirtualnych platformy Azure, aby zastosować zmiany oraz propagację nowych serwerów DNS.
  >
  >

W tym przykładzie usługa DNS jest zainstalowany i skonfigurowany na tych maszyn wirtualnych systemu Windows:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nazwy hosta i statyczne adresy IP dla SAP ASCS/SCS wystąpienia klastra i klastrowanego wystąpienia systemu DBMS

Dla wdrożenia lokalnego należy te hosta zarezerwowanych nazw i adresów IP:

| Rola nazwy hostów wirtualnych | Nazwy hostów wirtualnych | Wirtualne statycznego adresu IP |
| --- | --- | --- |
| Nazwa SAP ASCS/SCS do hostów wirtualnych pierwszej klastra (dla klastra zarządzania) |pr1-ascs-vir |10.0.0.42 |
| Nazwa hosta wirtualnego wystąpienia programu SAP ASCS/SCS |PR1 ascs sap |10.0.0.43 |
| Nazwa systemu DBMS SAP do hostów wirtualnych drugi klastra (klastra zarządzania) |pr1-dbms-vir |10.0.0.32 |

Podczas tworzenia klastra, można utworzyć nazwy hostów wirtualnych **pr1-ascs-vir** i **pr1-dbms-vir** i skojarzony adresy IP, które zarządzania klastrem. Aby dowiedzieć się, jak to zrobić, zobacz [zbieranie węzłów klastra w konfiguracji klastra][sap-ha-guide-8.12.1].

Inne dwie nazwy hostów wirtualnych, można ręcznie utworzyć **pr1 ascs sap** i **pr1 dbms sap**i skojarzony adresy IP na serwerze DNS. Wystąpienia SAP ASCS/SCS klastra i klastrowanego wystąpienia systemu DBMS używać tych zasobów. Aby dowiedzieć się, jak to zrobić, zobacz [Utwórz nazwę hosta wirtualnego dla klastrowanego wystąpienia programu SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ustaw statycznych adresów IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyny wirtualnej do użycia w klastrze, należy określić statycznych adresów IP dla wszystkich maszyn wirtualnych. W tym w konfiguracji sieci wirtualnej platformy Azure, a nie w systemie operacyjnym gościa.

1.  W portalu Azure wybierz **grupy zasobów** > **karta sieciowa** > **ustawienia** > **adres IP**.
2.  Na **adresów IP** bloku, w obszarze **przypisania**, wybierz pozycję **statycznych**. W **adres IP** wprowadź adres IP, który ma być używany.

  > [!NOTE]
  > W przypadku zmiany adresu IP karty sieciowej, musisz ponownie uruchomić maszyn wirtualnych platformy Azure, aby zastosować zmiany.  
  >
  >

  ![Rysunek 13: Zestaw statycznych adresów IP dla każdej maszyny wirtualnej karty sieciowej][sap-ha-guide-figure-3002]

  _**Rysunek 13:** ustawić statyczny adres IP dla każdej maszyny wirtualnej karty sieciowej_

  Powtórz ten krok dla wszystkich interfejsów sieciowych, dla wszystkich maszyn wirtualnych, włącznie ze maszyn wirtualnych, które ma być używany dla usługi Active Directory/serwera DNS.

W naszym przykładzie mamy tych maszyn wirtualnych i statycznymi adresami IP:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwsze wystąpienie serwera aplikacji SAP |PR1 podpisane 0 |PR1-nic podpisane-0 |10.0.0.50 |
| Drugie wystąpienie serwera aplikacji SAP |PR1 podpisane 1 |PR1-nic podpisane-1 |10.0.0.51 |
| Przyciski ... |Przyciski ... |Przyciski ... |Przyciski ... |
| Ostatnie wystąpienie serwera aplikacji SAP |PR1-podpisane-5 |PR1-nic podpisane-5 |10.0.0.55 |
| Pierwszym węźle klastra dla wystąpienia ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Drugi węzeł klastra dla wystąpienia ASCS/SCS |PR1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Pierwszym węźle klastra dla systemu DBMS wystąpienia |PR1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Drugi węzeł klastra dla systemu DBMS wystąpienia |PR1-db-1 |PR1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ustawianie statycznego adresu IP dla platformy Azure wewnętrznego modułu równoważenia obciążenia

Szablon SAP usługi Azure Resource Manager tworzy Azure wewnętrznego modułu równoważenia obciążenia używanego SAP ASCS/SCS wystąpienia klastra i klastrów systemu DBMS.

> [!IMPORTANT]
> Adres IP, nazwy hostów wirtualnych SAP ASCS/SCS jest taki sam jak adres IP usługi równoważenia obciążenia wewnętrznego SAP ASCS/SCS: **pr1-lb-ascs**.
> Adres IP, nazwy wirtualnego systemu DBMS jest taki sam jak adres IP usługi równoważenia obciążenia wewnętrznego systemu DBMS: **pr1-lb-dbms**.
>
>

Aby ustawić statyczny adres IP usługi równoważenia obciążenia wewnętrznego Azure:

1.  Początkowe wdrożenie ustawia adres IP usługi równoważenia obciążenia wewnętrznego **dynamiczne**. W portalu Azure na **adresów IP** bloku, w obszarze **przypisania**, wybierz pozycję **statycznych**.
2.  Ustawienie adresu IP usługi równoważenia obciążenia wewnętrznego **pr1-lb-ascs** adres IP nazwę hosta wirtualnego wystąpienia programu SAP ASCS/SCS.
3.  Ustawienie adresu IP usługi równoważenia obciążenia wewnętrznego **pr1-lb-dbms** adres IP nazwę hosta wirtualnego wystąpienia systemu DBMS.

  ![Rysunek 14: Ustaw statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla tego wystąpienia programu SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Rysunek 14:** ustawić statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla tego wystąpienia programu SAP ASCS/SCS_

W naszym przykładzie mamy dwa Azure wewnętrzne moduły równoważenia obciążenia mających te statycznych adresów IP:

| Rola usługi równoważenia obciążenia wewnętrznego Azure | Nazwa modułu równoważenia obciążenia wewnętrznego platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| SAP ASCS/SCS wystąpienia wewnętrznego modułu równoważenia obciążenia |PR1-lb-ascs |10.0.0.43 |
| System DBMS SAP wewnętrznego modułu równoważenia obciążenia |PR1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Domyślne reguły dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia ASCS/SCS

Szablon SAP usługi Azure Resource Manager tworzy portów, które są potrzebne:
* Wystąpienie ABAP ASCS z domyślnej liczby wystąpień **00**
* Wystąpienie Java SCS, numer wystąpienia domyślnego **01**

Po zainstalowaniu wystąpieniem SAP ASCS/SCS, należy użyć domyślnej liczby wystąpień **00** dla wystąpienia programu ABAP ASCS i numer wystąpienia domyślnego **01** dla swojego wystąpienia Java SCS.

Następnie należy utworzyć wymagane równoważenia punktów końcowych dla portów SAP NetWeaver obciążenia wewnętrznego.

Aby utworzyć wymagane obciążenia wewnętrznego równoważenia punktów końcowych, najpierw należy utworzyć te równoważenia punktów końcowych dla portów SAP NetWeaver ABAP ASCS obciążenia:

| Nazwa reguły równoważenia obciążenia/usługi | Domyślne numery portów | Konkretnych portów (ASCS wystąpienia z liczby wystąpień 00) (Wywołujących z 10) |
| --- | --- | --- |
| Umieścić serwer / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| Serwer komunikatów ABAP / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Komunikat wewnętrzny ABAP / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| Serwer HTTP / *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| SAP uruchamiania usługi ASCS HTTP / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP uruchamiania usługi ASCS HTTPS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Umieścić w kolejce replikacji / *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

_**Tabela 1:** portu liczby wystąpień programu SAP NetWeaver ABAP ASCS_

Następnie utwórz te równoważenia punktów końcowych dla portów SAP NetWeaver Java SCS obciążenia:

| Nazwa reguły równoważenia obciążenia/usługi | Domyślne numery portów | Konkretnych portów (SCS wystąpienia z liczby wystąpień 01) (Wywołujących z 11) |
| --- | --- | --- |
| Umieścić serwer / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Serwer bramy / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Serwer komunikatów Java / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| Serwer HTTP / *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| SAP uruchamiania usługi SCS HTTP / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| SAP uruchamiania usługi SCS HTTPS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Umieścić w kolejce replikacji / *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

_**Tabela 2:** portu liczby wystąpień programu SAP NetWeaver Java SCS_

![Rys. 15: Reguły dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia domyślne ASCS/SCS][sap-ha-guide-figure-3004]

_**Rysunek 15:** reguły dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia domyślne ASCS/SCS_

Ustawienie adresu IP usługi równoważenia obciążenia **pr1-lb-dbms** adres IP nazwę hosta wirtualnego wystąpienia systemu DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Zmień zasady dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia domyślne ASCS/SCS

Jeśli chcesz użyć innej liczby wystąpień SAP ASCS lub SCS, należy zmienić nazwy i wartości ich porty wartości domyślnych.

1.  W portalu Azure wybierz  **< *identyfikatora SID*> Usługa równoważenia obciążenia ascs - lb -** > **reguły równoważenia obciążenia**.
2.  Dla wszystkich reguł, które należą do wystąpienia SAP ASCS lub SCS równoważenia obciążenia Zmień wartości tych:

  * Name (Nazwa)
  * Port
  * Port zaplecza

  Na przykład jeśli chcesz zmienić domyślną liczbę wystąpień ASCS od 00 do 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

  Oto przykład aktualizacji dla portu *lbrule3200*.

  ![Rysunek 16: Zmień obciążenia domyślne ASCS/SCS równoważenia zasady Azure wewnętrznego modułu równoważenia obciążenia][sap-ha-guide-figure-3005]

  _**Rysunek 16:** Zmień zasady dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia domyślne ASCS/SCS_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Dodawanie maszyn wirtualnych systemu Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych, Dodaj maszyny wirtualne do domeny.

![Rysunek 17: Dodaj maszynę wirtualną do domeny][sap-ha-guide-figure-3006]

_**Rysunek 17:** Dodaj maszynę wirtualną do domeny_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Dodawanie wpisów rejestru na obu węzłach klastra z wystąpieniem SAP ASCS/SCS

Moduł równoważenia obciążenia Azure zawiera wewnętrzny moduł równoważenia obciążenia zamknięciu połączeń w przypadku połączenia są w stanie bezczynności pewien okres czasu (limit czasu bezczynności). Procesy robocze SAP w oknie dialogowym wystąpień otwarte połączenia można umieścić w kolejce SAP przetworzyć zaraz po pierwszym umieścić w kolejce/usuwania z kolejki żądania musi być wysyłane. Te połączenia zazwyczaj pozostają ustalonych dopóki proces pracy lub ponownego uruchomienia procesu umieścić w kolejce. Jednak jeśli połączenie jest bezczynne na wybrany okres czasu, Azure wewnętrznego modułu równoważenia obciążenia zamyka połączenia. Nie stanowi problemu, ponieważ proces roboczy SAP przywraca połączenie do procesu umieścić w kolejce, jeśli już nie istnieje. Te działania są udokumentowane w ślady developer procesów SAP, ale ich tworzyć dużą ilością zawartości dodatkowe w tych danych śledzenia. Zaleca się zmienić TCP/IP `KeepAliveTime` i `KeepAliveInterval` na obu węzłów klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP, opisane w dalszej części tego artykułu.

Aby dodać wpisów rejestru na obu węzłach klastra z wystąpieniem SAP ASCS/SCS, najpierw dodaj te wpisy rejestru systemu Windows na obu węzłów klastra systemu Windows dla programu SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (dziesiętna) |
| Wartość |120000 |
| Połącz się z dokumentacją |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** zmienić pierwszy parametr TCP/IP_

Następnie należy dodać ten wpisów rejestru systemu Windows na obu węzłów klastra systemu Windows dla programu SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (dziesiętna) |
| Wartość |120000 |
| Połącz się z dokumentacją |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabela 4:** zmienić drugi parametr TCP/IP_

**Aby zastosować zmiany, należy ponownie uruchomić oba węzły klastra**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurowanie klastra systemu Windows Server Failover Clustering dla wystąpienia programu SAP ASCS/SCS

Konfigurowanie klastra systemu Windows Server Failover Clustering dla wystąpienia programu SAP ASCS/SCS obejmuje następujące zadania:

- Zbieranie węzłów klastra w konfiguracji klastra
- Konfigurowanie monitora udziału plików klastra

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Zbieraj węzły klastra w konfiguracji klastra

1.  Dodawanie roli i funkcji — Kreator Dodaj klaster dla obu węzłów klastra trybu failover.
2.  Konfigurowanie klastra trybu failover za pomocą Menedżera klastra trybu Failover. W Menedżerze klastra trybu Failover wybierz **tworzenia klastrów**, a następnie dodaj tylko nazwę klastra pierwszy węzeł A. Nie dodawaj drugiego węzła jeszcze; drugi węzeł zostanie dodana w kolejnym kroku.

  ![Rysunek 18: Dodaj nazwę serwera lub maszyny wirtualnej na pierwszym węźle klastra][sap-ha-guide-figure-3007]

  _**Rysunek 18:** Dodaj nazwę serwera lub maszyny wirtualnej w pierwszym węźle klastra_

3.  Wprowadź nazwę sieci (nazwa hosta wirtualnego) klastra.

  ![Rysunek 19: Wprowadź nazwę klastra][sap-ha-guide-figure-3008]

  _**Rysunek 19:** wprowadź nazwę klastra_

4.  Po utworzeniu klastra, uruchom test sprawdzania poprawności klastra.

  ![Rysunek 20: Uruchom sprawdzenie poprawności klastra][sap-ha-guide-figure-3009]

  _**Rysunek 20:** uruchom sprawdzenie poprawności klastra_

  Możesz zignorować ostrzeżenia dotyczące dysków na tym etapie procesu. Zostanie dodana monitora udostępniania plików i SIOS udostępnionych dysków później. Na tym etapie nie trzeba martwić o kworum.

  ![Rysunek 21: Odnaleziono żadnego dysku kworum][sap-ha-guide-figure-3010]

  _**Rysunek 21:** odnaleziono żadnego dysku kworum_

  ![Rysunek 22: Zasobu klastra Core wymaga nowego adresu IP][sap-ha-guide-figure-3011]

  _**Rysunek 22:** zasobu klastra Core wymaga nowego adresu IP_

5.  Zmienianie adresu IP usługi klastrowania core. Klastra nie można uruchomić, dopóki nie zmieni adres IP klastra usługi podstawowej, ponieważ adres IP serwera wskazuje na jednym z węzłów maszyny wirtualnej. To zrobić na **właściwości** strony podstawowej usługi klastrowania IP zasobu.

  Na przykład, należy przypisać adres IP (w naszym przykładzie **10.0.0.42**) dla nazwy hostów wirtualnych klastra **pr1-ascs-vir**.

  ![Rysunek 23: W oknie dialogowym właściwości Zmień adres IP][sap-ha-guide-figure-3012]

  _**Rysunek 23:** w **właściwości** okno dialogowe Zmień adres IP_

  ![Rysunek 24: Przypisz adres IP, które jest zastrzeżone dla klastra][sap-ha-guide-figure-3013]

  _**Rysunek 24:** przypisać adres IP, które jest zastrzeżone dla klastra_

6.  Przełącz tryb online nazwę hosta wirtualnego klastra.

  ![25 rysunek: Usługi podstawowej klastra jest uruchomiony i działa prawidłowo i z poprawny adres IP adresów][sap-ha-guide-figure-3014]

  _**Rysunek 25:** usługi podstawowej klastra jest uruchomiony i działa prawidłowo i z poprawny adres IP adresów_

7.  Dodawanie drugiego węzła klastra.

  Teraz, podstawowa usługa klastrowania jest uruchomiona, można dodać drugiego węzła klastra.

  ![Rysunek 26: Dodanie drugiego węzła klastra][sap-ha-guide-figure-3015]

  _**Rysunek 26:** dodania drugiego węzła klastra_

8.  Wprowadź nazwę hosta drugiego węzła klastra.

  ![Rysunek 27: Wprowadź nazwę hosta drugiego węzła klastra][sap-ha-guide-figure-3016]

  _**Rysunek 27:** wprowadź nazwę hosta drugiego węzła klastra_

  > [!IMPORTANT]
  > Upewnij się, że **Dodaj wszystkie odpowiednie magazyny do klastra** pole wyboru jest **nie** wybrane.  
  >
  >

  ![Rysunek 28: Nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

  _**Rysunek 28:** czy **nie** zaznacz pole wyboru_

  Możesz zignorować ostrzeżenia dotyczące kworum i dysków. Należy ustawić kworum i udostępnić dysk później, zgodnie z opisem w [instalowanie SIOS DataKeeper Cluster Edition dla programu SAP ASCS/SCS dysku klastra, udziału][sap-ha-guide-8.12.3].

  ![Rysunek 29: Ignoruj ostrzeżenia dotyczące dysku kworum][sap-ha-guide-figure-3018]

  _**Rysunek 29:** Ignoruj ostrzeżenia dotyczące dysku kworum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Skonfiguruj Monitor udziału plików klastra

Konfigurowanie monitora udziału plików klastra obejmuje następujące zadania:

- Tworzenie udziału plików
- Ustawienia kworum monitora udziału plików w Menedżerze klastra trybu Failover

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Tworzenie udziału plików

1.  Wybierz monitor udziału plików, zamiast dysku kworum. SIOS DataKeeper obsługuje tę opcję.

  W przykładach w niniejszym artykule monitora udziału plików jest na serwerze/DNS usługi Active Directory, który działa na platformie Azure. Monitor udziału plików jest nazywany **domcontr 0**. Czy skonfigurować połączenia sieci VPN platformy Azure (za pośrednictwem sieci VPN typu lokacja-lokacja lub usługa Azure ExpressRoute), monitor udostępniania sieci/DNS usługi Active Directory usługi lokalnej i nie jest odpowiedni do uruchomienia pliku.

  > [!NOTE]
  > Jeśli usługa/DNS usługi Active Directory działa tylko na lokalne, nie skonfigurować Twoje monitora udziału plików na Active Directory/serwera DNS systemu operacyjnego działającej lokalnie. Opóźnienie sieci między węzłami klastra z systemem Azure i/DNS usługi Active Directory w lokalnym programie może być zbyt duży i spowodować problemy z połączeniem. Należy skonfigurować monitor udostępniania plików na maszynie wirtualnej platformy Azure, uruchomionym bliski węzeł klastra.  
  >
  >

  Stacja kworum wymaga co najmniej 1024 MB wolnego miejsca. Zaleca się 2048 MB wolnego miejsca na dysku kworum.

2.  Dodaj obiekt nazwy klastra.

  ![Rysunek 30: Przypisz uprawnienia udziału dla obiekt nazwy klastra][sap-ha-guide-figure-3019]

  _**Rysunek 30:** przypisać uprawnienia do udziału dla obiekt nazwy klastra_

  Pamiętaj, że uprawnienia obejmuje uprawnienia do zmiany danych w udziale dla obiekt nazwy klastra (w naszym przykładzie **pr1-ascs-vir$**).

3.  Aby dodać obiekt nazwy klastra do listy, wybierz opcję **Dodaj**. Zmień filtr, aby wyszukać obiektów komputerów, oprócz tych pokazano na rysunku 31.

  ![Rysunek 31: Zmień typy obiektów, aby obejmują komputery][sap-ha-guide-figure-3020]

  _**Rysunek 31:** zmienić typ obiektu do obejmują komputery_

  ![Rysunek 32: Zaznacz pole wyboru komputerów][sap-ha-guide-figure-3021]

  _**Rysunek 32:** wybierz **komputery** pole wyboru_

4.  Wprowadź obiektem nazwy klastra, tak jak pokazano na rysunku 31. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 30.

5.  Wybierz **zabezpieczeń** karcie udziału, a następnie ustawić bardziej szczegółowe uprawnienia dla obiekt nazwy klastra.

  ![Rysunek nr 33: Ustawić atrybutów zabezpieczeń dla obiekt nazwy klastra kworum udziału plików][sap-ha-guide-figure-3022]

  _**Rysunek nr 33:** ustawić atrybutów zabezpieczeń dla obiekt nazwy klastra kworum udziału plików_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ustaw kworum monitora udziału plików w Menedżerze klastra trybu Failover

1.  Otwórz kworum ustawienia kreatora konfiguracji.

  ![Rysunek 34: Uruchom Kreator ustawienia kworum klastra Konfiguruj][sap-ha-guide-figure-3023]

  _**Rysunek 34:** Uruchamianie Kreatora ustawienie Konfigurowanie kworum klastra_

2.  Na **wybrać konfigurację kworum** wybierz **Wybieranie monitora kworum**.

  ![Rysunek 35: Konfiguracji kworum, możesz wybrać z][sap-ha-guide-figure-3024]

  _**Rysunek 35:** konfiguracji kworum, możesz wybrać spośród_

3.  Na **Wybieranie monitora kworum** wybierz **Konfigurowanie monitora udziału plików**.

  ![Rysunek 36: Wybierz monitor udziału plików][sap-ha-guide-figure-3025]

  _**Rysunek 36:** Wybierz monitor udziału plików_

4.  Wprowadź ścieżkę UNC do udziału plików (w naszym przykładzie \\domcontr 0\FSW). Aby wyświetlić listę wprowadzania zmian, zaznacz **dalej**.

  ![Rysunek 37: Zdefiniuj lokalizację udziału plików monitora udziału][sap-ha-guide-figure-3026]

  _**Rysunek 37:** określić lokalizację udziału plików monitora udziału_

5.  Wybierz zmiany, a następnie wybierz **dalej**. Należy pomyślnie ponownie skonfigurować konfigurację klastra, jak pokazano na rysunku 38.  

  ![38 rysunek: Potwierdzenie, że po zmianie konfiguracji klastra][sap-ha-guide-figure-3027]

  _**Rysunek 38:** potwierdzenie, że po zmianie konfiguracji klastra_

Po pomyślnym zainstalowaniu klastra pracy awaryjnej systemu Windows, należy dokonać niektórych progi w celu dostosowania wykrywania trybu failover do warunków na platformie Azure zmiany. Można zmienić parametry są opisane w tym blogu: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Przy założeniu, że Twoje dwóch maszyn wirtualnych, które kompilacji konfiguracji klastra systemu Windows dla ASCS/SCS znajdują się w tej samej podsieci, wymaga zmiany tych wartości następujących parametrów:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Te ustawienia zostały przetestowane z klientami i dostępne dobrej naruszenia pozwala uzyskać odporność na jednej stronie. Z drugiej strony te ustawienia zostały udostępnianie fast wystarczająco dużo pracy awaryjnej w warunkach rzeczywistych błędu w przypadku awarii oprogramowania lub węzeł/wirtualna SAP. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Zainstaluj dla dysku udziału klastra SAP ASCS/SCS SIOS DataKeeper Cluster Edition

Masz teraz pracy konfiguracji klastra trybu Failover systemu Windows Server na platformie Azure. Jednak, aby zainstalować wystąpienie programu SAP ASCS/SCS, zasób udostępniony dysk. Nie można utworzyć o współużytkowane zasoby dyskowe potrzebne na platformie Azure. SIOS DataKeeper Cluster Edition jest rozwiązań innych firm, których można używać do tworzenia współużytkowane zasoby dyskowe.

Instalowanie SIOS DataKeeper Cluster Edition dla udziału dysk klastrowy SAP ASCS/SCS obejmuje następujące zadania:

- Dodawanie programu .NET Framework 3.5
- Instalowanie SIOS DataKeeper
- Konfigurowanie SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Dodaj programu .NET Framework 3.5
Microsoft .NET Framework 3.5 nie jest automatycznie aktywowane lub zainstalowany w systemie Windows Server 2012 R2. Ponieważ SIOS DataKeeper wymaga programu .NET Framework jako we wszystkich węzłach, które należy zainstalować na DataKeeper, należy zainstalować program .NET Framework 3.5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania programu .NET Framework 3.5:

- Użyj funkcje Kreatora dodawania ról i w systemie Windows, jak pokazano na rysunku 39.

  ![Rysunek 39: Zainstalować program .NET Framework 3.5 przy użyciu dodawania ról i funkcji — Kreator][sap-ha-guide-figure-3028]

  _**Rysunek 39:** zainstalować program .NET Framework 3.5 przy użyciu dodawania ról i funkcji — Kreator_

  ![40 rysunek: Postęp instalacji pasek po zainstalowaniu programu .NET Framework 3.5 przy użyciu dodawania ról i funkcji — Kreator][sap-ha-guide-figure-3029]

  _**Rysunek 40:** pasek po zainstalowaniu programu .NET Framework 3.5 przy użyciu funkcji Kreatora dodawania ról i postępu instalacji_

- Użyj narzędzia wiersza polecenia dism.exe. Ten typ instalacji należy uzyskać dostępu do katalogu SxS na nośniku instalacyjnym systemu Windows. W wierszu polecenia z podwyższonym poziomem uprawnień wpisz polecenie:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Zainstaluj SIOS DataKeeper

Zainstaluj SIOS DataKeeper Cluster Edition w każdym węźle w klastrze. Aby utworzyć wirtualny magazynu udostępnionego z SIOS DataKeeper, Utwórz zsynchronizowanej dublowania i następnie symulować magazyn udostępniony klastra.

Przed zainstalowaniem oprogramowania SIOS, Utwórz użytkownika domeny **DataKeeperSvc**.

> [!NOTE]
> Dodaj **DataKeeperSvc** użytkownikowi **administratora lokalnego** na obu węzłów klastra.
>
>

Aby zainstalować SIOS DataKeeper:

1.  Zainstaluj oprogramowanie SIOS na obu węzłów klastra.

  ![Instalator SIOS][sap-ha-guide-figure-3030]

  ![41 rysunku: Pierwsza strona instalacji SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Rysunek 41:** pierwszej strony instalacji SIOS DataKeeper_

2.  W oknie dialogowym pokazano na rysunku 42, wybierz **tak**.

  ![Rysunek 42: DataKeeper informuje, czy usługa zostanie wyłączona][sap-ha-guide-figure-3032]

  _**Rysunek 42:** DataKeeper informuje, że usługa zostanie wyłączona_

3.  W oknie dialogowym pokazano na rysunku 43, zaleca się wybranie **konta domeny lub tego serwera**.

  ![Rysunek 43: Wybór użytkownika dla SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Rysunek 43:** SIOS DataKeeper określonego użytkownika_

4.  Wprowadź nazwę użytkownika konta domeny i hasła, które zostały utworzone dla SIOS DataKeeper.

  ![Rysunek 44: Wprowadź nazwę użytkownika domeny i hasło dla instalacji SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Rysunek 44:** wprowadź nazwę użytkownika domeny i hasło dla instalacji SIOS DataKeeper_

5.  Zainstaluj klucz licencji dla swojego wystąpienia SIOS DataKeeper, jak pokazano na rysunku 45.

  ![Rysunek 45: Wprowadź klucz licencji SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Rysunek 45:** wprowadź klucz licencji SIOS DataKeeper_

6.  Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Konfigurowanie SIOS DataKeeper

Po zainstalowaniu SIOS DataKeeper na obu węzłach, należy uruchomić konfigurację. Cel konfiguracji ma synchroniczne dane replikacji między dodatkowe wirtualne dyski twarde dołączonych do poszczególnych maszyn wirtualnych.

1.  Uruchom narzędzie DataKeeper zarządzania i konfiguracji, a następnie wybierz **połączenia serwera**. (W 46 rysunku, ta opcja jest zaznaczona kółkiem na czerwono.)

  ![Rysunek 46: SIOS DataKeeper zarządzanie i narzędzia do konfiguracji][sap-ha-guide-figure-3036]

  _**Rysunek 46:** narzędzie SIOS DataKeeper zarządzania i konfiguracji_

2.  Wprowadź nazwę lub adres TCP/IP pierwszy węzeł, który narzędzia zarządzania i konfiguracji należy połączenia oraz, w drugim kroku drugiego węzła.

  ![Rysunek 47: Wstaw nazwę lub adres TCP/IP pierwszego węzła Zarządzanie i narzędzia do konfiguracji należy łączyć, a w drugim etapie drugiego węzła][sap-ha-guide-figure-3037]

  _**Rysunek 47:** Wstaw nazwę lub adres TCP/IP pierwszego węzła narzędzie zarządzania i konfiguracji należy łączyć, a w drugim etapie drugiego węzła_

3.  Utwórz zadanie replikacji między dwoma węzłami.

  ![Rysunek 48: Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

  _**Rysunek 48:** Utwórz zadanie replikacji_

  Kreator prowadzi przez proces tworzenia zadania replikacji.
4.  Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła źródłowego.

  ![Rysunek 49: Zdefiniuj nazwę zadania replikacji][sap-ha-guide-figure-3039]

  _**Rysunek 49:** Zdefiniuj nazwę zadania replikacji_

  ![Rysunek 50: Definiowanie danych podstawowych dla tego węzła, który powinien być bieżącego węzła źródłowego][sap-ha-guide-figure-3040]

  _**Rysunek 50:** zdefiniować podstawowe dane dla tego węzła, który powinien być bieżącego węzła źródłowego_

5.  Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

  ![Rysunek 51: Definiowanie danych podstawowych dla tego węzła, który powinien być bieżący węzeł docelowy][sap-ha-guide-figure-3041]

  _**Rysunek 51:** zdefiniować podstawowe dane dla tego węzła, który powinien być bieżący węzeł docelowy_

6.  Zdefiniuj algorytmy kompresji. W naszym przykładzie zaleca się, że kompresji strumienia replikacji. Szczególnie w sytuacjach, ponowna synchronizacja kompresji, replikacji strumienia znacznie skraca czas ponownej synchronizacji. Należy pamiętać, że kompresja używa zasobów Procesora i pamięci RAM maszyny wirtualnej. Jak zwiększa się częstotliwość kompresji, co powoduje ilości zasobów procesora CPU używanych. Można również dostosować to ustawienie później.

7.  Inne ustawienie, które należy sprawdzić to, czy replikacja odbywa się asynchronicznie lub synchronicznie. *W przypadku ochrony SAP ASCS/SCS konfiguracji, należy użyć Replikacja synchroniczna*.  

  ![Rysunek 52: Zdefiniuj szczegóły replikacji][sap-ha-guide-figure-3042]

  _**Rysunek 52:** Zdefiniuj szczegóły replikacji_

8.  Należy określić, czy wolumin, który jest replikowany za zadanie replikacji powinny być reprezentowane w konfiguracji klastra z systemem Windows Server Failover Clustering jako udostępniony dysk. SAP ASCS/SCS konfiguracji, wybierz **tak** tak, aby klaster systemu Windows widzi replikowanego woluminu jako udostępnionego dysku, której można użyć jako woluminu klastra.

  ![Rysunek 53: Wybierz tak, aby ustawić replikowanego woluminu jako woluminu klastra][sap-ha-guide-figure-3043]

  _**Rysunek 53:** wybierz **tak** można ustawić replikowanego woluminu jako woluminu klastra_

  Po utworzeniu woluminu narzędzie DataKeeper zarządzania i konfiguracji pokazuje, że zadanie replikacji jest aktywne.

  ![Rysunek 54: DataKeeper synchronicznego duplikowania dysku udziału SAP ASCS/SCS jest aktywny][sap-ha-guide-figure-3044]

  _**Rysunek 54:** DataKeeper duplikowanie synchroniczne dla ASCS/SCS programu SAP współużytkować dysku jest aktywny_

  Menedżer klastra trybu failover zawiera obecnie dysku jako dysku DataKeeper, jak pokazano na rysunku 55.

  ![Rysunek 55: Menedżer klastra trybu Failover zawiera dysk, który jest replikowany DataKeeper][sap-ha-guide-figure-3045]

  _**Rysunek 55:** Menedżera klastra trybu Failover zawiera dysk tego DataKeeper replikowane_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalowanie systemu SAP NetWeaver

Instalator systemu DBMS nie opisano ustawienia się różnić w zależności od używanego systemu DBMS. Jednak przyjęto założenie, reagowania problemów o wysokiej dostępności z systemu DBMS z funkcje obsługiwanych przez różnych dostawców DBMS dla platformy Azure. Na przykład zawsze włączone lub dublowania bazy danych programu SQL Server i Oracle Data Guard dla baz danych Oracle. W tym scenariuszu używanych w tym artykule firma Microsoft nie dodał zwiększają ochronę dla systemu DBMS.

Istnieją specjalne uwagi dotyczące różnych usług systemu DBMS interakcji z tego rodzaju konfiguracji klastra SAP ASCS/SCS na platformie Azure.

> [!NOTE]
> Procedury instalacji programu SAP NetWeaver ABAP systemów, Java i systemami ABAP + Java są prawie takie same. Najbardziej znacząca różnica jest systemu SAP ABAP jedno wystąpienie ASCS. Systemu SAP Java ma jedno wystąpienie SCS. Systemu SAP ABAP + Java ma jedno wystąpienie ASCS i jedno wystąpienie SCS uruchomiony w tej samej grupie klastra trybu failover firmy Microsoft. Różnice instalacji dla każdej stosu instalacji SAP NetWeaver jawnie wymienione. Można założyć, że wszystkie inne elementy są takie same.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalowanie programu SAP przy użyciu wystąpienia ASCS/SCS wysokiej dostępności

> [!IMPORTANT]
> Pamiętaj, aby nie umieszczenia pliku stronicowania na DataKeeper dublowanych woluminów. DataKeeper nie obsługuje woluminy dublowane. Możesz pozostawić pliku stronicowania na dysku tymczasowym D maszyny wirtualnej platformy Azure, co jest ustawieniem domyślnym. Jeśli nie jest już istnieje, należy przenieść plik stronicowania systemu Windows na dysku D z maszyny wirtualnej platformy Azure.
>
>

Instalowanie SAP przy użyciu wystąpienia ASCS/SCS wysokiej dostępności obejmuje następujące zadania:

- Tworzenie nazwę hosta wirtualnego wystąpienia klastra programu SAP ASCS/SCS
- Instalowanie programu SAP pierwszym węźle klastra
- Modyfikowanie profilu SAP wystąpienia ASCS/SCS
- Dodawanie portu sondy
- Otwarcie portu sondowania zapory systemu Windows

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Utwórz nazwę hosta wirtualnego wystąpienia klastra programu SAP ASCS/SCS

1.  W Menedżerze DNS z systemem Windows utwórz wpis DNS dla nazwy hostów wirtualnych ASCS/SCS wystąpienia.

  > [!IMPORTANT]
  > Adres IP, który można przypisać nazwę hosta wirtualnego wystąpienia ASCS/SCS musi być taka sama jak adres IP, który został przypisany do usługi równoważenia obciążenia Azure (**<*SID*> - lb - ascs**).  
  >
  >

  Adres IP nazwę hosta wirtualnego SAP ASCS/SCS (**pr1 ascs sap**) jest taka sama jak adres IP usługi równoważenia obciążenia Azure (**pr1-lb-ascs**).

  ![Rysunek 56: Definiowanie wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adres TCP/IP][sap-ha-guide-figure-3046]

  _**Rysunek 56:** zdefiniować wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adres TCP/IP_

2.  Aby zdefiniować adres IP przypisany do nazwy hostów wirtualnych, wybierz **Menedżera DNS** > **domeny**.

  ![57 rysunek: Nowa nazwa wirtualnego i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Rysunek 57:** nową nazwę wirtualnego i TCP/IP adresów dla SAP ASCS/SCS konfiguracji klastra_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Zainstaluj na pierwszym węźle klastra SAP

1.  Wykonanie pierwszej opcji węzła klastra w węźle klastra A. Na przykład na **pr1 ascs 0** hosta.
2.  Zachowaj ustawienie domyślne portów dla usługi równoważenia obciążenia wewnętrznego platformy Azure, wybierz:

  * **ABAP system**: **ASCS** wystąpienie numer **00**
  * **Java system**: **SCS** wystąpienie numer **01**
  * **System ABAP + Java**: **ASCS** wystąpienie numer **00** i **SCS** wystąpienie numer **01**

  Aby użyć wystąpienia liczb innych niż 00 dla wystąpienia ABAP ASCS i 01 dla wystąpienia Java SCS, najpierw należy zmienić reguły, opisane w równoważenia obciążenia domyślne usługi równoważenia obciążenia wewnętrznego Azure [Zmień zasady dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia domyślne ASCS/SCS][sap-ha-guide-8.9].

Następny kilka zadań nie są opisane w standardowe dokumentacji instalacji SAP.

> [!NOTE]
> Dokumentacja instalacji programu SAP opisuje sposób instalowania pierwszego węzła klastra ASCS/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modyfikowanie profilu SAP wystąpienia ASCS/SCS

Należy dodać nowy parametr profilu. Parametr profilu zapobiega połączeń między procesów roboczych SAP i umieścić w kolejce zamykania po okresie bezczynności wynoszącym zbyt długo. Wspomniano scenariusz problemu w [dodać wpisów rejestru na obu węzłach klastra z wystąpieniem SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji również wprowadzono dwie zmiany do niektórych podstawowych parametrów połączenia TCP/IP. W drugim kroku, należy określić serwer umieścić w kolejce do wysłania `keep_alive` sygnału, dzięki czemu połączeń nie osiągnął Próg bezczynności Azure wewnętrznego modułu równoważenia obciążenia.

Aby zmodyfikować profil SAP wystąpienia ASCS/SCS:

1.  Dodaj parametr ten profil, do profilu wystąpienia SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  W naszym przykładzie ścieżka jest:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Na przykład do profilu wystąpienia SAP SCS i odpowiednie ścieżki:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Aby zastosować zmiany, ponownie uruchom wystąpienie /SCS SAP ASCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Dodaj port sondy

Funkcja równoważenia obciążenia wewnętrznego sondy do pracy z usługą równoważenia obciążenia w Azure konfigurację całego klastra. Azure wewnętrznego modułu równoważenia obciążenia zwykle rozdziela przychodzące obciążenie równomiernie w poszczególnych uczestniczących maszyn wirtualnych. Jednak to nie będzie działać w niektórych konfiguracjach klastra, ponieważ jest aktywna tylko jedno wystąpienie. Inne wystąpienie jest w stanie pasywnym i nie akceptuje dowolne obciążenia. Funkcja badania pomaga Azure wewnętrznego modułu równoważenia obciążenia przypisuje pracy tylko do aktywnego wystąpienia. Z funkcją sondowania wewnętrznego modułu równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie ograniczyć tylko wystąpienia o obciążenie.

Aby dodać port sondy:

1.  Sprawdź bieżący **ProbePort** ustawienie za pomocą następującego polecenia programu PowerShell. Wykonanie go z jednej maszyny wirtualnej w konfiguracji klastra.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  Zdefiniuj port sondy. Domyślny numer portu sondowania to **0**. W tym przykładzie używamy port sondy **62000**.

  ![Rysunek 58: Port sondy konfiguracji klastra jest równa 0, domyślnie][sap-ha-guide-figure-3048]

  _**Rysunek 58:** domyślny port sondy konfiguracji klastra jest równa 0_

  Numer portu jest zdefiniowany w szablonach SAP usługi Azure Resource Manager. Można przypisać numer portu w programie PowerShell.

  Aby ustawić nową wartość ProbePort **SAP <*SID*> IP** zasób klastra, uruchom następujący skrypt programu PowerShell. Zaktualizuj zmienne środowiska PowerShell. Po uruchomieniu skryptu, zostanie wyświetlony monit o ponowne uruchomienie Grupa klastra SAP do aktywowania zmian.

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

  Po przełączeniu **SAP <*SID* >**  klastra roli w tryb online, upewnij się, że **ProbePort** jest ustawiona na nową wartość.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![Rysunek 59: Sondowania portu klastra po zainstalowaniu nowej wartości][sap-ha-guide-figure-3049]

  _**Rysunek 59:** sondowania portu klastra po zainstalowaniu nowej wartości_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otwórz port sondy zapory systemu Windows

Należy otworzyć port sondy zapory systemu Windows na obu węzłów klastra. Użyj następującego skryptu, aby otworzyć port sondy zapory systemu Windows. Zaktualizuj zmienne środowiska PowerShell.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** ustawiono **62000**. Teraz można uzyskać dostępu do udziału plików  **\\\ascsha-clsap\sapmnt** od innych hostów, takie jak z **ascsha dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Zainstaluj wystąpienie bazy danych

Aby zainstalować wystąpienie bazy danych, wykonaj proces opisany w dokumentacji instalacji SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Zainstaluj drugiego węzła klastra

Aby zainstalować drugi klaster, wykonaj czynności opisane w przewodniku instalacji SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Zmień typ uruchomienia wystąpienia usługi Windows Wywołujących SAP

Zmień typ uruchomienia usługi systemu Windows Wywołujących SAP **automatycznie (opóźnione uruchomienie)** na obu węzłów klastra.

![Rysunek 60: Zmień typ usługi dla wystąpienia Wywołujących SAP do opóźnionego automatycznego][sap-ha-guide-figure-3050]

_**Rysunek 60:** Zmień typ usługi wystąpienie Wywołujących SAP do opóźnionego automatycznego_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Zainstaluj serwer aplikacji głównej SAP

Zainstaluj wystąpienie serwera aplikacji głównej (adresy) <*SID*> - podpisane - 0 na maszynie wirtualnej, który został wybrany do obsługi adresy dostawcy. Nie ma żadnych zależności, Azure lub DataKeeper określonych ustawień.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalowanie serwera aplikacji dodatkowe SAP

Zainstaluj SAP dodatkowych aplikacji serwera (AAS) na wszystkich maszynach wirtualnych, które zostały wyznaczone do obsługi wystąpienia serwera aplikacji SAP. Na przykład na <*SID*> - podpisane - 1 do <*SID*> - podpisane -&lt;n&gt;.

> [!NOTE]
> Zakończy się instalacja systemu SAP NetWeaver wysokiej dostępności. Następnie należy kontynuować testowanie trybu failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testowanie trybu failover wystąpienia programu SAP ASCS/SCS i SIOS replikacji
Jest łatwy do testowania i monitorowania za pomocą narzędzia Menedżera klastra trybu Failover oraz zarządzania DataKeeper SIOS i konfiguracji trybu failover wystąpienia programu SAP ASCS/SCS i SIOS replikacji dysku.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> W węźle klastra, A jest uruchomione wystąpienie SAP ASCS/SCS

**SAP PR1** grupy klastra jest uruchomiona w węźle klastra A. Na przykład na **pr1 ascs 0**. Przypisywanie udostępnionego dysku S, który jest częścią programu **SAP PR1** Grupa klastra, i używający wystąpienia ASCS/SCS, do klastra z węzła A.

![61 rysunek: Menedżer klastra trybu Failover: SAP < SID > grupy klastra jest uruchomiona w węźle klastra, A][sap-ha-guide-figure-5000]

_**Rysunek 61:** Menedżera klastra trybu Failover: SAP <*SID*> grupy klastra jest uruchomiona w węźle klastra, A_

W narzędziu Zarządzanie DataKeeper SIOS i jego konfiguracja widać, że udostępniony dysk synchronicznie replikacji danych z dysku woluminu źródłowego S w węźle klastra, A na dysku woluminu docelowego S w węźle klastra B. Na przykład są replikowane z **pr1 ascs 0 [10.0.0.40]** do **pr1 ascs 1 [10.0.0.41]**.

![Rysunek 62: W SIOS DataKeeper replikowane lokalnym woluminie z węzła klastra, A do węzła klastra B][sap-ha-guide-figure-5001]

_**Rysunek 62:** w SIOS DataKeeper replikowane lokalnym woluminie z węzła klastra, A do węzła klastra B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Tryb failover z węzła A węzła B

1.  Wybierz jedną z tych opcji, aby zainicjować trybu failover SAP <*SID*> Grupa klastra z węzła klastra, A do węzła klastra B:
  - Menedżer klastra trybu Failover  
  - Za pomocą programu PowerShell klastra trybu Failover

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Uruchom ponownie, A węzeł klastra w ramach systemu operacyjnego gościa (powoduje to zainicjowanie automatycznej pracy awaryjnej SAP <*identyfikatora SID*> Grupa klastra z węzła A do węzła B).  
3.  Uruchom ponownie, A węzeł klastra przy użyciu portalu Azure (powoduje to zainicjowanie automatycznej pracy awaryjnej SAP <*identyfikatora SID*> Grupa klastra z węzła A do węzła B).  
4.  Uruchom ponownie A węzła klastra za pomocą programu Azure PowerShell (powoduje to zainicjowanie automatycznej pracy awaryjnej SAP <*identyfikatora SID*> Grupa klastra z węzła A do węzła B).

  Po przejściu w tryb failover, SAP <*SID*> grupy klastra jest uruchomiona w węźle klastra B. Na przykład, że jest uruchomiona w **pr1 ascs 1**.

  ![Rysunek 63: W Menedżerze klastra trybu Failover SAP < SID > grupy klastra jest uruchomiona w węźle klastra B][sap-ha-guide-figure-5002]

  _**Rysunek 63**: W Menedżerze klastra trybu Failover, SAP <*SID*> grupy klastra jest uruchomiona w węźle klastra B_

  Udostępniony dysk jest teraz zainstalowany w klastrze węzła B. SIOS DataKeeper jest replikowanie danych z dysku woluminu źródłowego S w węźle klastra B na dysku woluminu docelowego S w węźle klastra A. Na przykład jest replikowany z **pr1 ascs 1 [10.0.0.41]** do **pr1 ascs 0 [10.0.0.40]**.

  ![Rysunek 64: SIOS DataKeeper replikuje lokalnym woluminie z węzła klastra B do A węzła klastra][sap-ha-guide-figure-5003]

  _**Rysunek 64:** SIOS DataKeeper replikuje lokalnym woluminie z węzła klastra B do A węzła klastra_
