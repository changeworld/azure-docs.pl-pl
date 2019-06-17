---
title: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver | Dokumentacja firmy Microsoft
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
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
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 226986fb7c41c19b58f0163414628ad08ddeda15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409967"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure

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
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Konfiguracja wysokiej dostępności — wiele identyfikatorów SID SAP)


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
[resource-group-overview]:../../../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]: https://support.sap.com/pam (Macierz dostępności produktów SAP)
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


Usługa Azure Virtual Machines to rozwiązanie dla organizacji, które potrzebują obliczeń, magazynu i zasobów sieciowych w krótkim czasie i bez wydłużonych cykli. Azure Virtual Machines umożliwia wdrażanie klasycznych aplikacji opartych na oprogramowanie SAP NetWeaver ABAP, Java i stosu ABAP + Java. Rozszerz niezawodność i dostępność bez dodatkowych zasobów lokalnych. Maszyny wirtualne platformy Azure obsługuje łączność między wieloma lokalizacjami, dzięki czemu możesz zintegrować usługę Azure Virtual Machines domen lokalnych w organizacji, chmur prywatnych i środowisko systemu SAP.

W tym artykule omówione są kroki, które można wykonać w celu wdrażania systemów SAP o wysokiej dostępności na platformie Azure za pomocą modelu wdrażania usługi Azure Resource Manager. W jaki sposób można za pomocą tych głównych zadań:

* Znajdź odpowiednie SAP Notes i przewodników po instalacji, na liście [zasobów] [ sap-ha-guide-2] sekcji. Ten artykuł stanowi uzupełnienie dokumentacji instalacji SAP i SAP Notes, które są podstawowe zasoby, które mogą pomóc Ci instalować i wdrażać oprogramowanie SAP na określonych platformach.
* Dowiedz się, różnice w modelu wdrażania usługi Azure Resource Manager i modelem klasycznym wdrożeniu platformy Azure.
* Więcej informacji na temat trybów kworum klastra trybu Failover w systemie Windows Server, można wybrać model, który jest odpowiedni dla danego wdrożenia platformy Azure.
* Więcej informacji na temat systemu Windows Server Failover Clustering udostępnionego magazynu z usługami platformy Azure.
* Dowiedz się, jak chronić pojedynczego punktu z awarii składników takich jak Advanced Business Application programowania (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) i systemy zarządzania bazami danych (DBMS) oraz nadmiarowych elementów, takich jak aplikacji SAP Serwer, na platformie Azure.
* Wykonaj przykład krok po kroku instalacji i konfiguracji systemu SAP o wysokiej dostępności w klastrze systemu Windows Server Failover Clustering na platformie Azure przy użyciu usługi Azure Resource Manager.
* Poznaj dodatkowe kroki wymagane do użycia w systemie Windows Server Failover Clustering w Azure, ale nie są wymagane we wdrożeniu w środowisku lokalnym.

Aby uprościć wdrażanie i konfigurację, w tym artykule używamy szablonów usługi Resource Manager SAP trójwarstwowej wysokiej dostępności. Szablony Automatyzowanie wdrażania całej infrastruktury niezbędnych do systemu SAP o wysokiej dostępności. Infrastruktura obsługuje również zmianę rozmiaru SAP aplikacji wydajności Standard (protokoły SAP) systemu SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Wymagania wstępne
Przed rozpoczęciem upewnij się, że spełniają wymagania wstępne, które są opisane w poniższych sekcjach. Ponadto należy koniecznie sprawdzić wszystkich zasobów wymienionych w [zasobów] [ sap-ha-guide-2] sekcji.

W tym artykule używamy szablonów usługi Azure Resource Manager dla [trójwarstwowej oprogramowanie SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Omówienie przydatne szablonów, zobacz [SAP usługi Azure Resource Manager](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Zasoby
Artykuły te obejmują wdrożenia SAP na platformie Azure:

* [Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver][dbms-guide]
* [Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver (w tym przewodniku)][sap-ha-guide]

> [!NOTE]
> Jeśli to możliwe, udostępniamy łącze odwołujący się Podręcznik instalacji SAP (zobacz [przewodników po instalacji SAP][sap-installation-guides]). Wymagania wstępne i informacje o procesie instalacji jest dobry pomysł, aby uważnie przeczytaj przewodników po instalacji SAP NetWeaver. W tym artykule opisano tylko określone zadania dla systemów opartych na oprogramowanie SAP NetWeaver, których można użyć z usługą Azure Virtual Machines.
>
>

Te informacje SAP są związane z tym tematem SAP na platformie Azure:

| Numer | Stanowisko |
| --- | --- |
| [1928533] |Aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i zmianę rozmiaru |
| [2015553] |SAP na platformie Microsoft Azure: Wymagania wstępne dotyczące obsługi |
| [1999351] |Ulepszone monitorowania platformy Azure dla rozwiązania SAP |
| [2178632] |Klucz metryki monitorowania dla rozwiązania SAP na platformie Microsoft Azure |
| [1999351] |Wirtualizacja na Windows: Rozszerzone monitorowanie |
| [2243692] |Korzystanie z usługi Azure Premium Storage SSD dla systemu SAP DBMS wystąpienia |

Dowiedz się więcej o [ograniczenia subskrypcji platformy Azure][azure-subscription-service-limits-subscription], w tym domyślne ogólne ograniczenia i ograniczenia maksymalnej.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP o wysokiej dostępności przy użyciu usługi Azure Resource Manager i modelem klasycznym wdrożeniu platformy Azure
Usługi Azure Resource Manager i modeli klasycznym wdrożeniu platformy Azure różnią się w następujących obszarach:

- Grupy zasobów
- Zależności usługi równoważenia obciążenia wewnętrznego platformy Azure w grupie zasobów platformy Azure
- Obsługa scenariuszy z wieloma identyfikatorami SID SAP

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupy zasobów
W usłudze Azure Resource Manager, można użyć grup zasobów do zarządzania wszystkimi zasobami aplikacji w ramach subskrypcji platformy Azure. Zintegrowanego podejścia, w grupie zasobów, wszystkie zasoby mają ten sam cykl życia. Na przykład wszystkie zasoby są tworzone w tym samym czasie i są usuwane w tym samym czasie. Dowiedz się więcej o [grupach zasobów](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Zależności usługi równoważenia obciążenia wewnętrznego platformy Azure w grupie zasobów platformy Azure

W modelu klasycznym wdrożeniu platformy Azure istnieje zależność między Azure wewnętrznego modułu równoważenia obciążenia (usługa Azure Load Balancer) a grupą usługi w chmurze. Co wewnętrznego modułu równoważenia obciążenia wymaga jednej grupy usługi w chmurze.

W usłudze Azure Resource Manager, nie trzeba grupę zasobów platformy Azure przy użyciu modułu równoważenia obciążenia platformy Azure. Środowisko jest prostszy i bardziej elastycznym.

### <a name="support-for-sap-multi-sid-scenarios"></a>Obsługa scenariuszy z wieloma identyfikatorami SID SAP

W usłudze Azure Resource Manager można zainstalować wiele systemu identyfikator (SID) ASCS/SCS wystąpieniami platformy SAP w jednym klastrze. — Wiele identyfikatorów SID jest możliwe dzięki obsłudze wielu adresów IP dla każdej platformy Azure wewnętrznego modułu równoważenia obciążenia.

Aby użyć modelu klasycznym wdrożeniu platformy Azure, wykonaj procedury opisane w temacie [oprogramowanie SAP NetWeaver na platformie Azure: Klastrowanie wystąpienia SAP ASCS/SCS przy użyciu systemu Windows Server Failover Clustering na platformie Azure przy użyciu oprogramowanie SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Zdecydowanie zalecamy użycie modelu wdrażania usługi Azure Resource Manager dla instalacji SAP. Oferuje wiele korzyści, które nie są dostępne w klasycznym modelu wdrażania. Dowiedz się więcej o usłudze Azure [modeli wdrażania][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
Windows Server Failover Clustering jest podstawą SAP ASCS/SCS instalacji o wysokiej dostępności i systemu DBMS na platformie Windows.

Klaster trybu failover to grupa 1 + n niezależnych serwerów (węzłów), które współpracują ze sobą, aby zwiększyć dostępność aplikacji i usług. Jeśli wystąpi awaria węzła, Windows Server Failover Clustering oblicza liczbę błędów, które mogą wystąpić przy zachowaniu dobrej kondycji klastra, aby zapewnić aplikacji i usług. Istnieje możliwość z trybów kworum różnych osiągnąć klastra trybu failover.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Trybów kworum
Korzystając z systemu Windows Server Failover Clustering, można wybrać z czterech trybów kworum:

* **Większość węzłów**. Głosować na każdym węźle klastra. Klaster działa tylko z większością głosów, oznacza to, z ponad połowę głosów. Zaleca się tej opcji w przypadku klastrów nieparzysta liczba węzłów. Na przykład trzy węzły w klastrze z siedmiu węzłów może zakończyć się niepowodzeniem, a aparaturze klastra pozwala uzyskać większość i kontynuuje działanie.  
* **Większość węzłów i dysków**. Każdy węzeł i wyznaczonym dysku (monitorem dysku) w magazynie klastra głosować gdy są one dostępne i komunikacji. Klaster działa tylko z większością głosów, oznacza to, z ponad połowę głosów. W tym trybie ma sens w środowisku klastra z parzystą liczbą węzłów. Jeśli połowa węzłów i dysków są w trybie online, klaster będzie pozostawał w dobrej kondycji.
* **Węzeł i udziału plików większość**. Każdy węzeł, a także udziału plików wyznaczonym (monitora udziału plików), który administrator tworzy głosować, niezależnie od tego, czy są dostępne węzły i udziału plików i komunikacji. Klaster działa tylko z większością głosów, oznacza to, z ponad połowę głosów. W tym trybie ma sens w środowisku klastra z parzystą liczbą węzłów. Jest on podobny do trybu Większość węzłów i dysków, ale używa monitora udziału plików zamiast dysku monitora. Ten tryb jest łatwy do wdrożenia, ale jeśli udział plików nie jest sam o wysokiej dostępności, mogą stać się pojedynczym punktem awarii.
* **Bez większości: Tylko dysk**. Klaster ma kworum, jeśli jeden węzeł jest dostępny i komunikuje się z określonym dyskiem w magazynie klastra. Tylko węzły, które są także komunikuje się z tym dysku mogą dołączyć do klastra. Zaleca się, że nie należy używać tego trybu.

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server Failover Clustering w środowisku lokalnym
Rysunek 1 pokazuje klastra z dwoma węzłami. Jeśli mają połączenie sieciowe między węzłami kończy się niepowodzeniem i zarówno pobytu węzłów w górę i uruchomiona, dysku kworum lub pliku Określa, który węzeł będzie w dalszym ciągu usługi i aplikacje klastra. Węzeł, który ma dostęp do udziału pliku lub dysku kworum jest węzeł, który zapewnia, że usługi.

Ponieważ w tym przykładzie użyto dwa węzły klastra, używamy Tryb kworum Większość węzłów i plików udziału. Większość węzłów i dysków również jest prawidłową opcją. W środowisku produkcyjnym zaleca się, że używasz dysku kworum. Za pomocą technologii systemu i sieciowych o wysokiej dostępności.

![Rysunek 1: Przykład konfiguracji systemu Windows Server Failover Clustering for SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-1000]

_**Rysunek 1:** Przykład konfiguracji systemu Windows Server Failover Clustering for SAP ASCS/SCS na platformie Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Magazyn udostępniony
Rysunek 1 pokazuje również klastra magazynu udostępnionego z dwoma węzłami. W klastrze magazynu udostępnionego w środowisku lokalnym wszystkie węzły w klastrze wykryć magazynu udostępnionego. Mechanizm blokowania chroni dane przed uszkodzeniem. Wszystkie węzły może wykryć, jeśli inny węzeł nie powiedzie się. Jeśli jeden węzeł ulegnie awarii, drugi węzeł przejmuje na własność zasobów magazynu i zapewnia dostępność usług.

> [!NOTE]
> Nie trzeba udostępnionych dysków w celu zapewnienia wysokiej dostępności z niektórymi aplikacjami systemu DBMS, takich jak program SQL Server. SQL Server Always On replikuje plików danych i dziennika systemu DBMS na dysku lokalnym, w jednym węźle klastra, na lokalny dysk w innym węźle klastra. W takim przypadku konfiguracji klastra Windows nie wymaga udostępnionego dysku.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Sieć i rozpoznawanie nazw
Komputery klienckie dotrzeć do klastra za pośrednictwem wirtualnego adresu IP i nazwę hosta wirtualnego, który zapewnia serwer DNS. Węzły lokalne i serwer DNS może obsługiwać wiele adresów IP.

W przypadku typowej instalacji można użyć co najmniej dwóch połączeń sieciowych:

* Dedykowane połączenie z magazynem
* Połączenie sieci wewnętrznej klastra dla pulsu
* Sieć publiczna, używanego przez klientów do łączenia z klastrem

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering na platformie Azure
W porównaniu do bez wdrożenia systemu operacyjnego lub prywatnej chmury, maszyn wirtualnych platformy Azure wymaga dodatkowych kroków, aby skonfigurować systemu Windows Server Failover Clustering. W przypadku tworzenia dysku udostępnionego klastra, musisz ustawić kilka adresów IP i nazwy hostów wirtualnych na potrzeby wystąpienia SAP ASCS/SCS.

W tym artykule omówiono kluczowe założenia i dodatkowe kroki wymagane do utworzenia klastra usług o wysokiej dostępności centralne SAP na platformie Azure. Firma Microsoft dowiesz się, jak skonfigurować narzędzia innej firmy, oprogramowanie SIOS DataKeeper i sposobie konfigurowania Azure wewnętrznego modułu równoważenia obciążenia. Te narzędzia można użyć do utworzenia klastra pracy awaryjnej Windows za pomocą monitora udziału plików na platformie Azure.

![Rysunek 2: Windows Server Failover Clustering konfiguracji na platformie Azure, bez udostępnionego dysku][sap-ha-guide-figure-1001]

_**Rysunek 2:** Windows Server Failover Clustering konfiguracji na platformie Azure, bez udostępnionego dysku_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Oprogramowanie SIOS DataKeeper udostępniony dysk na platformie Azure
Potrzebujesz klastra magazynu udostępnionego dla wystąpienia SAP ASCS/SCS wysokiej dostępności. Od września 2016 r. platforma Azure nie oferuje magazyn udostępniony, który można użyć do utworzenia klastra magazynu udostępnionego. Za pomocą oprogramowania innych firm oprogramowanie SIOS DataKeeper Cluster Edition utworzyć dublowany magazynu, która symuluje sieć magazynu udostępnionego klastra. Rozwiązanie oprogramowanie SIOS udostępnia w czasie rzeczywistym synchronicznej replikacji danych. Jest to, jak można utworzyć zasobu udostępnionego dysku dla klastra:

1. Dołącz dodatkowe Azure wirtualnego dysku twardego (VHD) do wszystkich maszyn wirtualnych (VM) w konfiguracji klastra Windows.
2. Uruchom oprogramowanie SIOS DataKeeper Cluster Edition na obu węzłach maszyny wirtualnej.
3. Oprogramowanie SIOS DataKeeper Cluster Edition należy skonfigurować tak, aby go odzwierciedla zawartość dodatkowe wolumin wirtualny dysk twardy dołączony ze źródłowej maszyny wirtualnej do wirtualnego dysku twardego dołączone dodatkowe ilości docelowej maszyny wirtualnej. Oprogramowanie SIOS DataKeeper przenosi źródłowe i docelowe woluminy lokalne, a następnie wyświetla je do systemu Windows Server Failover Clustering jako jeden dysk udostępniony.

Uzyskaj więcej informacji o [oprogramowanie SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Rysunek 3: Windows Server Failover Clustering konfiguracji na platformie Azure przy użyciu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Rysunek 3:** Windows Server Failover Clustering konfiguracji na platformie Azure przy użyciu oprogramowanie SIOS DataKeeper_

> [!NOTE]
> Udostępnione dyski nie ma potrzeby wysokiej dostępności przy użyciu niektórych produktów systemu DBMS, takich jak program SQL Server. SQL Server Always On replikuje plików danych i dziennika systemu DBMS na dysku lokalnym, w jednym węźle klastra, na lokalny dysk w innym węźle klastra. W tym przypadku konfiguracji klastra Windows nie wymaga udostępnionego dysku.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Rozpoznawanie nazw na platformie Azure
Platforma usług w chmurze platformy Azure nie oferuje opcję, aby skonfigurować wirtualne adresy IP, takie jak adresy IP zmiennoprzecinkowy. Należy skonfigurować wirtualny adres IP, aby dotrzeć do zasobu klastra w chmurze alternatywnym rozwiązaniem.
Platforma Azure oferuje wewnętrznego modułu równoważenia obciążenia w usłudze Azure Load Balancer. Przy użyciu wewnętrznego modułu równoważenia obciążenia klienci dotrzeć do klastra za pośrednictwem wirtualnego adresu IP klastra.
Należy wdrożyć wewnętrzny moduł równoważenia obciążenia w grupie zasobów, która zawiera węzły klastra. Następnie należy skonfigurować wszystkie niezbędne portu reguły przekazywania za pomocą sondy porty wewnętrznego modułu równoważenia obciążenia.
Klienci mogą łączyć się za pomocą nazwy hosta wirtualnego. Serwer DNS jest rozpoznawana jako adres IP klastra i port uchwyty modułu równoważenia obciążenia wewnętrznego, przekazywania do aktywnego węzła klastra.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver wysokiej dostępności w Azure infrastruktury as-a-Service (IaaS)
Aby osiągnąć wysoką dostępność aplikacji SAP, takich jak dla składników oprogramowania SAP, należy chronić następujące składniki:

* Wystąpienie serwera aplikacji SAP
* Wystąpienia SAP ASCS/SCS
* Serwer systemu DBMS

Aby uzyskać więcej informacji o ochronie składniki SAP w scenariuszach wysokiej dostępności, zobacz [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver](planning-guide.md).

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Serwer aplikacji SAP o wysokiej dostępności
Zazwyczaj nie trzeba konkretnego rozwiązania o wysokiej dostępności dla wystąpienia serwera aplikacji SAP i okno dialogowe. Uzyskiwanie wysokiej dostępności dzięki nadmiarowości i należy skonfigurować wiele wystąpień okna dialogowego w różnych wystąpieniach maszyn wirtualnych platformy Azure. Należy mieć co najmniej dwa wystąpienia aplikacji SAP, zainstalowane w dwa wystąpienia maszyn wirtualnych platformy Azure.

![Rysunek 4: Serwer aplikacji SAP o wysokiej dostępności][sap-ha-guide-figure-2000]

_**Rysunek 4:** Serwer aplikacji SAP o wysokiej dostępności_

Należy umieścić wszystkie maszyny wirtualne, które ustawione wystąpień serwera aplikacji SAP hosta, w tym samym dostępności platformy Azure. Zestawu dostępności platformy Azure zapewnia, że:

* Wszystkie maszyny wirtualne są częścią tej samej domenie uaktualnienia. Domeny uaktualnienia, na przykład sprawia, że się upewnić, że maszyny wirtualne nie zostaną zaktualizowane w tym samym czasie podczas planowanej konserwacji przestojów.
* Wszystkie maszyny wirtualne są częścią tej samej domenie błędów. Domeny błędów, na przykład sprawia, że się upewnić, że maszyny wirtualne są wdrażane tak, aby żadnego pojedynczego punktu awarii wpływa na dostępność maszyn wirtualnych.

Dowiedz się więcej na temat [Zarządzanie dostępnością maszyn wirtualnych][virtual-machines-manage-availability].

Ponieważ konto usługi Azure storage jest potencjalnym pojedynczym punktem awarii, należy mieć co najmniej dwa konta magazynu platformy Azure, w których co najmniej dwie maszyny wirtualne są rozproszone. W idealnym konfiguracji dysków w każdej maszyny wirtualnej, na którym jest uruchomione wystąpienie okna dialogowego SAP będzie można wdrożyć w innego konta magazynu.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Wystąpienia SAP ASCS/SCS wysokiej dostępności
Rysunek 5 jest przykładem wystąpienia SAP ASCS/SCS wysokiej dostępności.

![Rysunek 5: Wystąpienia SAP ASCS/SCS wysokiej dostępności][sap-ha-guide-figure-2001]

_**Rysunek 5:** Wystąpienia SAP ASCS/SCS wysokiej dostępności_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Wystąpienia SAP ASCS/SCS wysokiej dostępności z systemu Windows Server Failover Clustering na platformie Azure
W porównaniu do bez wdrożenia systemu operacyjnego lub prywatnej chmury, maszyn wirtualnych platformy Azure wymaga dodatkowych kroków, aby skonfigurować systemu Windows Server Failover Clustering. Aby utworzyć klaster pracy awaryjnej Windows, należy udostępnionym dysku klastra, kilka adresów IP, nazwy hostów wirtualnych kilka i Azure wewnętrznego modułu równoważenia obciążenia dla klastra wystąpienie SAP ASCS/SCS. Omówimy to bardziej szczegółowo w dalszej części tego artykułu.

![Rysunek 6: Windows Server Failover Clustering dla konfiguracji SAP ASCS/SCS na platformie Azure przy użyciu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Rysunek 6:** Windows Server Failover Clustering konfiguracji SAP ASCS/SCS na platformie Azure przy użyciu oprogramowanie SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Wystąpienie DBMS wysokiej dostępności
Systemu DBMS również jest pojedynczym punktem kontaktu w systemie SAP. Należy chronić go przy użyciu rozwiązania wysokiej dostępności. Rysunek nr 7 przedstawia rozwiązania o wysokiej dostępności programu SQL Server Always On na platformie Azure, za pomocą systemu Windows Server Failover Clustering i Azure wewnętrznego modułu równoważenia obciążenia. SQL Server Always On replikuje DBMS danych i plików dziennika przy użyciu funkcji replikacji swój własny system DBMS. W tym przypadku możesz nie muszą klastra udostępnione dyski, który upraszcza całą konfigurację.

![Rysunek 7: Przykład wysokiej dostępności SAP DBMS przy użyciu programu SQL Server Always On][sap-ha-guide-figure-2003]

_**Rysunek 7:** Przykład wysokiej dostępności SAP DBMS przy użyciu programu SQL Server Always On_

Aby uzyskać więcej informacji na temat klastrów programu SQL Server na platformie Azure przy użyciu modelu wdrażania usługi Azure Resource Manager zobacz następujące artykuły:

* [Konfigurowanie zawsze włączonej grupy dostępności w usłudze Azure Virtual Machines ręcznie przy użyciu usługi Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurowanie usługi Azure wewnętrznego modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scenariusze end-to-end wysokiej dostępności wdrażania

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenariusz wdrożenia przy użyciu architektury 1 szablonu

Rysunek 8 przedstawiono przykładową architekturę wysokiej dostępności oprogramowanie SAP NetWeaver na platformie Azure **jeden** systemu SAP. Ten scenariusz jest skonfigurowane w następujący sposób:

- Jeden klaster dedykowanych jest używany na potrzeby wystąpienia SAP ASCS/SCS.
- Jeden klaster dedykowany jest używany w ramach wystąpienia systemu DBMS.
- Wystąpień serwera aplikacji SAP są wdrażane w ich własnych dedykowanych maszyn wirtualnych.

![Rysunek 8: SAP o wysokiej dostępności architektury szablon 1, za pomocą dedykowanego klastra ASCS/SCS i DBMS][sap-ha-guide-figure-2004]

_**Rysunek 8:** SAP 1 szablonu architektury wysokiej dostępności, dedykowane klastry ASCS/SCS i DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenariusz wdrażania przy użyciu architektury 2 szablonu

Rysunek 9 przedstawiono architekturę wysokiej dostępności oprogramowanie SAP NetWeaver na platformie Azure **jeden** systemu SAP. Ten scenariusz jest skonfigurowane w następujący sposób:

- Jeden klaster dedykowanych służy do **zarówno** wystąpienia SAP ASCS/SCS i systemu DBMS.
- Wystąpień serwera aplikacji SAP są wdrażane w własnej dedykowanych maszyn wirtualnych.

![Rysunek 9: SAP o wysokiej dostępności architektury szablon 2, z klastrem dedykowany dla ASCS/SCS i klastrem dedykowany dla systemu DBMS][sap-ha-guide-figure-2005]

_**Rysunek 9:** SAP o wysokiej dostępności architektury szablon 2, z klastrem dedykowany dla ASCS/SCS i klastrem dedykowany dla systemu DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenariusz wdrażania przy użyciu architektury 3 szablonu

Na rysunku nr 10 przedstawiono przykładową architekturę wysokiej dostępności oprogramowanie SAP NetWeaver na platformie Azure **dwóch** SAP systemów, za pomocą &lt;SID1&gt; i &lt;SID2&gt;. Ten scenariusz jest skonfigurowane w następujący sposób:

- Jeden klaster dedykowanych służy do **zarówno** wystąpienia SAP ASCS/SCS SID1 *i* wystąpienia SAP ASCS/SCS SID2 (jeden klaster).
- Jeden klaster dedykowanych służy do DBMS SID1 i inny dedykowany klaster jest używany dla systemu DBMS SID2 (dwóch klastrach).
- Wystąpień serwera aplikacji SAP w systemie SAP SID1 ma swoje własne dedykowane maszyny wirtualne.
- Wystąpień serwera aplikacji SAP w systemie SAP SID2 ma swoje własne dedykowane maszyny wirtualne.

![Rysunek 10. SAP o wysokiej dostępności — architektury szablon 3, z klastrem dedykowany dla różnych wystąpień ASCS/SCS][sap-ha-guide-figure-6003]

_**Rysunek 10.** SAP o wysokiej dostępności — architektury szablon 3, z klastrem dedykowany dla różnych wystąpień ASCS/SCS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Przygotowywanie infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowywanie infrastruktury dla architektury 1 szablonu
Szablony usługi Azure Resource Manager dla programu SAP ułatwiają wdrażanie wymaganych zasobów.

Trójwarstwowa szablonów usługi Azure Resource Manager obsługują także scenariuszy wysokiej dostępności, takich jak architektury 1 szablon, który ma dwa klastry. Każdy klaster jest SAP pojedynczego punktu awarii SAP ASCS/SCS i DBMS.

Poniżej przedstawiono, gdzie można uzyskać szablony usługi Azure Resource Manager przykładowy scenariusz, który opisano w tym artykule:

* [Azure Marketplace image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Aby przygotować infrastrukturę do architektury 1 szablonu:

- W witrynie Azure portal na **parametry** bloku, w **SYSTEMAVAILABILITY** wybierz opcję **HA**.

  ![Rysunek 11: Ustaw parametry programu SAP o wysokiej dostępności usługi Azure Resource Manager][sap-ha-guide-figure-3000]

_**Rysunek 11:** Ustaw parametry programu SAP o wysokiej dostępności usługi Azure Resource Manager_


  Utwórz szablony:

  * **Maszyny wirtualne**:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*> - di - <*numer*>
    * ASCS/SCS klastra maszyn wirtualnych: <*SAPSystemSID*> - ascs — <*numer*>
    * Klaster systemu DBMS: <*SAPSystemSID*> - db - <*numer*>

  * **Karty dla wszystkich maszyn wirtualnych za pomocą adresów IP skojarzone sieciowe**:
    * <*SAPSystemSID*> - nic - di - <*numer*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*> - nic - db - <*numer*>

  * **Konta usługi Azure storage**

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*> - avset - di
    * SAP ASCS/SCS klastra maszyn wirtualnych: <*SAPSystemSID*> - avset - ascs
    * System DBMS klastra maszyn wirtualnych: <*SAPSystemSID*> - avset - db

  * **Usługa Azure wewnętrznego modułu równoważenia obciążenia**:
    * Z wszystkimi portami dla wystąpienia ASCS/SCS i adresu IP <*SAPSystemSID*> - lb - ascs
    * Z wszystkimi portami dla adresu IP i bazami danych programu SQL Server <*SAPSystemSID*> - lb - db

  * **Sieciowa grupa zabezpieczeń**: <*SAPSystemSID*> - nsg - ascs-0  
    * Za pomocą zewnętrznego otwartego portu protokołu RDP (Remote Desktop) do <*SAPSystemSID*> - ascs - 0, maszyny wirtualnej

> [!NOTE]
> Wszystkie adresy IP kart sieciowych i Azure wewnętrznych modułów równoważenia obciążenia są **dynamiczne** domyślnie. Zmień ich **statyczne** adresów IP. Opisano, jak to zrobić w dalszej części tego artykułu.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Wdrażanie maszyn wirtualnych z połączeniem sieci firmowej (cross-premises) do użycia w środowisku produkcyjnym
Dla systemów SAP w środowisku produkcyjnym, wdrażanie maszyn wirtualnych platformy Azure za pomocą [łączności sieci firmowej (cross-premises)] [ planning-guide-2.2] przy użyciu usługi Azure Site-to-Site VPN lub usługi Azure ExpressRoute.

> [!NOTE]
> Możesz użyć swojego wystąpienia usługi Azure Virtual Network. Sieć wirtualną i podsieć już zostały utworzone i przygotowane.
>
>

1. W witrynie Azure portal na **parametry** bloku, w **NEWOREXISTINGSUBNET** wybierz opcję **istniejących**.
2. W **SUBNETID** Dodaj pełny ciąg sieci platformy Azure przygotowany SubnetID, w którym planujesz wdrożyć usługi Azure virtual machines.
3. Aby uzyskać listę wszystkich podsieci w sieci platformy Azure, uruchom następujące polecenie programu PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Identyfikator** pola pokazuje **SUBNETID**.
4. Aby uzyskać listę wszystkich **SUBNETID** wartości, uruchom następujące polecenie programu PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** wygląda podobnie do następującego:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Wdrożenie oparte tylko na chmurze wystąpień SAP na potrzeby pokazu i testowania
Możesz wdrożyć systemu SAP o wysokiej dostępności w modelu wdrażania tylko w chmurze. Tego rodzaju wdrożenia przede wszystkim jest przydatne w przypadku pokaz i testowania przypadków użycia. Nie jest odpowiedni dla produkcyjne przypadki użycia.

- W witrynie Azure portal na **parametry** bloku, w **NEWOREXISTINGSUBNET** wybierz opcję **nowe**. Pozostaw **SUBNETID** pole puste.

  Szablon SAP usługi Azure Resource Manager automatycznie tworzy sieć wirtualną platformy Azure i podsieć.

> [!NOTE]
> Należy również wdrożyć co najmniej jedną specjalną maszynę wirtualną dla usługi Active Directory i DNS w tym samym wystąpieniu usługi Azure Virtual Network. Szablon nie tworzy tych maszyn wirtualnych.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowywanie infrastruktury dla architektury 2 szablonu

Można użyć tego szablonu usługi Azure Resource Manager dla rozwiązania SAP na celu uproszczenie wdrażania zasobów wymaganej infrastruktury SAP architektury szablon 2.

Poniżej przedstawiono, gdzie można uzyskać szablony usługi Azure Resource Manager, w tym scenariuszu wdrażania:

* [Azure Marketplace image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowywanie infrastruktury dla architektury 3 szablonu

Można przygotować infrastrukturę i skonfigurować SAP dla **— wiele identyfikatorów SID**. Na przykład można dodać dodatkowego wystąpienia SAP ASCS/SCS do *istniejących* konfiguracji klastra. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dodatkowego wystąpienia SAP ASCS/SCS do istniejącej konfiguracji klastra do utworzenia konfiguracji usługi SAP — wiele identyfikatorów SID w usłudze Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster — wiele identyfikatorów SID, możesz użyć wieloma identyfikatorami SID [szablony szybkiego startu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates).
Do utworzenia nowego klastra z wieloma identyfikatorami SID, należy wdrożyć trzy następujące szablony:

* [Szablon ASCS/SCS](#ASCS-SCS-template)
* [Baza danych szablonów](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje mają więcej szczegółów na temat szablonów oraz parametry, które należy podać w szablonach.

#### <a name="ASCS-SCS-template"></a> Szablon ASCS/SCS

Szablon ASCS/SCS umożliwia wdrożenie dwóch maszyn wirtualnych, które służą do tworzenia klastra trybu failover systemu Windows Server, który obsługuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon — wiele identyfikatorów SID ASCS/SCS, w [szablonu — wiele identyfikatorów SID ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image], wprowadź wartości dla następujących parametrów:

  - **Prefiks zasobów**.  Ustaw prefiks zasobu, który służy jako prefiks wszystkie zasoby, które są tworzone podczas wdrażania. Ponieważ zasoby, które nie należą do tylko jednego systemu SAP, prefiks zasobu nie jest identyfikator SID jednego systemu SAP.  Prefiks musi należeć do zakresu od **trzy do sześciu znaków**.
  - **Stos typu**. Wybierz typ stosu systemu SAP. W zależności od typu stosu usługi Azure Load Balancer ma jedną (ABAP lub tylko dla środowiska Java) lub dwóch (ABAP + Java) prywatnych adresów IP na systemie SAP.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Liczba systemów SAP**. Wybierz liczbę systemów SAP, który chcesz zainstalować w tym klastrze.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
  -  **Nowej lub istniejącej podsieci**. Określanie, czy należy utworzyć nową sieć wirtualną i podsieć lub używać istniejącej podsieci. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz opcję **istniejących**.
  -  **Identyfikator podsieci**. Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda następująco: /subscriptions/ <*identyfikator subskrypcji*> /resourceGroups/ <*nazwy grupy zasobów*> /providers/Microsoft.Network/virtualNetworks/ < *Nazwa sieci wirtualnej*> /subnets/ <*Nazwa podsieci*>

Ten szablon wdraża jedno wystąpienie usługi Azure Load Balancer, która obsługuje wiele systemów SAP.

- Wystąpienia ASCS powinny być skonfigurowane dla liczby wystąpień 00, 10, 20...
- Wystąpienia usług SCS powinny być skonfigurowane dla liczby wystąpień 01, 11, 21...
- Wystąpienia ASCS umieścić w kolejce replikacji serwera (Wywołujących) (tylko system Linux) powinny być skonfigurowane dla liczby wystąpień 02, 12, 22...
- Wystąpienia usług SCS Wywołujących (tylko system Linux) powinny być skonfigurowane dla liczby wystąpień 03, 13, 23...

Moduł równoważenia obciążenia zawiera 1 (2 dla systemu Linux) VIP(s), 1 x dla ASCS/SCS i 1 x adresów VIP dla Wywołujących (tylko system Linux).

Poniższa lista zawiera wszystkie reguły, o których (gdzie x jest numer systemu SAP, na przykład 1, 2, 3...) równoważenia obciążenia:
- Porty Windows specyficznych dla wszystkich systemów SAP: 445, 5985
- Porty ASCS (liczba wystąpień x0): 32 x 0, 36 x 0, 39 x 0, 81 x 0, 5 x 013, 5 x 014, 5 x 016
- Porty SCS (liczba wystąpień x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- Porty ASCS Wywołujących w systemie Linux (liczba wystąpień x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Porty SCS Wywołujących w systemie Linux (liczba wystąpień x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Moduł równoważenia obciążenia jest skonfigurowany do używania następujące porty sondy (gdzie x jest numer systemu SAP, na przykład 1, 2, 3...):
- Port sondy modułu równoważenia obciążenia wewnętrznego ASCS/SCS: 620x0
- Wewnętrzny Wywołujących obciążenia równoważenia porcie sondowania (tylko system Linux): 621x2

#### <a name="database-template"></a> Baza danych szablonów

Szablon bazy danych umożliwia wdrożenie co najmniej dwóch maszyn wirtualnych, które można użyć, aby zainstalować system zarządzania relacyjnymi bazami (danych RDBMS) dla jednego systemu SAP. Na przykład jeśli wdrożono szablon pięć systemów SAP ASCS/SCS, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon — wiele identyfikatorów SID bazy danych, w [bazy danych — wiele identyfikatorów SID szablonu][sap-templates-3-tier-multisid-db-marketplace-image], wprowadź wartości dla następujących parametrów:

- **Identyfikator systemu SAP**. Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Identyfikator będzie służyć jako prefiks dla zasobów, które są wdrażane.
- **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
- **Atrybut DbType**. Wybierz typ bazy danych, które chcesz zainstalować w klastrze. Wybierz **SQL** Jeśli chcesz zainstalować program Microsoft SQL Server. Wybierz **HANA** Jeśli planowane jest instalowanie platformy SAP HANA na maszynach wirtualnych. Upewnij się, że wybrano prawidłowy typ systemu operacyjnego: Wybierz **Windows** dotyczącymi usług SQL i wybierz opcję dystrybucji systemu Linux dla platformy HANA. Usługi Azure Load Balancer jest podłączonych do maszyn wirtualnych zostanie skonfigurowana do obsługi typu wybranej bazy danych:
  * **SQL**. Moduł równoważenia obciążenia będzie równoważyć obciążenie portu 1433. Upewnij się, że ten port jest używany do instalacji programu SQL Server Always On.
  * **HANA**. Moduł równoważenia obciążenia będzie równoważyć obciążenie porty 35015 i 35017. Upewnij się, że instalowanie platformy SAP HANA z liczbą wystąpień **50**.
  Moduł równoważenia obciążenia będzie używać port sondy 62550.
- **Rozmiar systemu SAP**. Ustaw liczbę protokoły SAP zapewni nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP, będą wymagać systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
- **Dostępność systemu**. Wybierz **HA**.
- **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
- **Identyfikator podsieci**. Wprowadź identyfikator podsieci, które były używane podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony jako część wdrożenia szablonu ASCS/SCS.

#### <a name="application-servers-template"></a> Szablon serwerów aplikacji

Szablon serwerów aplikacji umożliwia wdrożenie co najmniej dwóch maszyn wirtualnych, które może służyć jako wystąpień serwera aplikacji SAP dla jednego systemu SAP. Na przykład jeśli wdrożono szablon pięć systemów SAP ASCS/SCS, należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon — wiele identyfikatorów SID serwerów aplikacji, w [szablon — wiele identyfikatorów SID serwerów aplikacji][sap-templates-3-tier-multisid-apps-marketplace-image], wprowadź wartości dla następujących parametrów:

  -  **Identyfikator systemu SAP**. Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Identyfikator będzie służyć jako prefiks dla zasobów, które są wdrażane.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu SAP**. Liczba protokoły SAP zapewni nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP, będą wymagać systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
  -  **Dostępność systemu**. Wybierz **HA**.
  -  **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, który może służyć do logowania się na tym komputerze.
  -  **Identyfikator podsieci**. Wprowadź identyfikator podsieci, które były używane podczas wdrażania szablonu ASCS/SCS lub identyfikator podsieci, który został utworzony jako część wdrożenia szablonu ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Usługa Azure virtual network
W naszym przykładzie przestrzeni adresowej sieci wirtualnej platformy Azure to 10.0.0.0/16. Istnieje jedna podsieć o nazwie **podsieci**, przy użyciu zakresu adresów 10.0.0.0/24. Wszystkich maszyn wirtualnych i wewnętrznych modułów równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzaj żadnych zmian, ustawienia sieciowe w systemie operacyjnym gościa. W tym adresy IP, serwery DNS i podsieci. Konfigurowanie ustawień sieciowych na platformie Azure. Usługa dynamicznej konfiguracji hosta (DHCP, Dynamic Host Configuration Protocol) propaguje ustawienia.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Adresy IP serwera DNS

Aby ustawić wymagany adres IP DNS adresy, wykonaj następujące czynności.

1. W witrynie Azure portal na **serwerów DNS** bloku, upewnij się, że Twoja sieć wirtualna **serwerów DNS** opcja jest ustawiona na **niestandardowych serwerów DNS**.
2. Wybierz ustawienia, na podstawie typu sieci, do których masz. Więcej informacji zawierają następujące zasoby:
   * [Łączność sieci firmowej (cross-premises)][planning-guide-2.2]: Dodaj adresy IP serwerów DNS w środowisku lokalnym.  
   Możesz rozszerzyć lokalne serwery DNS do maszyn wirtualnych, które działają na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których uruchomiono usługę DNS.
   * W przypadku wdrożeń odizolowane na platformie Azure: Należy wdrożyć dodatkowe maszyny wirtualnej, w tym samym wystąpieniu sieci wirtualnej, która służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure, które zostały skonfigurowane do uruchamiania usługi DNS.

   ![Ilustracja 12 Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure][sap-ha-guide-figure-3001]

   _**Ilustracja 12** Konfigurowanie serwerów DNS dla sieci wirtualnej platformy Azure_

   > [!NOTE]
   > W przypadku zmiany adresów IP serwerów DNS, konieczne jest ponowne uruchomienie maszyn wirtualnych platformy Azure, aby zastosować zmiany i propagowanie nowych serwerów DNS.
   >
   >

W naszym przykładzie usługa DNS jest zainstalowany i skonfigurowany na tych maszynach wirtualnych Windows:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nazwy hostów i statyczne adresy IP dla klastrowanego wystąpienia SAP ASCS/SCS i klastrowane wystąpienie systemu DBMS

Do wdrożenia w środowisku lokalnym należy te hosta zastrzeżonych nazw i adresów IP:

| Rola nazwy hostów wirtualnych | Nazwa hosta wirtualnego | Wirtualne statyczny adres IP |
| --- | --- | --- |
| SAP ASCS/SCS klaster wirtualny host na imię (w przypadku klastra zarządzania) |pr1-ascs-vir |10.0.0.42 |
| Nazwa hosta wirtualnego wystąpienia SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nazwa systemu SAP DBMS do hostów wirtualnych drugiego klastra (Zarządzanie klastrem) |pr1-dbms-vir |10.0.0.32 |

Podczas tworzenia klastra, można utworzyć nazwy hostów wirtualnych **pr1-ascs-vir** i **pr1-dbms-vir** i skojarzony adresy IP, które zarządzania klastrem. Aby dowiedzieć się, jak to zrobić, zobacz [zbieranie węzłów klastra w konfiguracji klastra][sap-ha-guide-8.12.1].

Pozostałe dwie nazwy hostów wirtualnych, można ręcznie utworzyć **pr1 ascs sap** i **pr1 dbms sap**i skojarzony adresy IP na serwerze DNS. Dzięki tym zasobom, wystąpienie SAP ASCS/SCS klastra i klastrowanego wystąpienia systemu DBMS. Aby dowiedzieć się, jak to zrobić, zobacz [Utwórz nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ustawianie statycznych adresów IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyn wirtualnych do użycia w klastrze, należy ustawić statycznych adresów IP dla wszystkich maszyn wirtualnych. W tym w konfiguracji usługi Azure Virtual Network, a nie w systemie operacyjnym gościa.

1. W witrynie Azure portal wybierz **grupy zasobów** > **karty sieciowej** > **ustawienia** > **adresu IP**.
2. Na **adresów IP** bloku, w obszarze **przypisania**, wybierz opcję **statyczne**. W **adresu IP** wprowadź adres IP, który chcesz użyć.

   > [!NOTE]
   > Jeśli zmienisz adres IP karty sieciowej, musisz ponownie uruchomić maszyn wirtualnych platformy Azure, aby zastosować zmiany.  
   >
   >

   ![Ilustracja 13 Ustawianie statycznych adresów IP dla każdej maszyny wirtualnej karty sieciowej][sap-ha-guide-figure-3002]

   _**Ilustracja 13** Ustawianie statycznych adresów IP dla każdej maszyny wirtualnej karty sieciowej_

   Powtórz ten krok dla wszystkich interfejsów sieciowych, dla wszystkich maszyn wirtualnych, włącznie ze maszyn wirtualnych, które chcesz użyć dla usługi/DNS usługi Active Directory.

W tym przykładzie mamy tych maszyn wirtualnych i statyczne adresy IP:

| Roli maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwsze wystąpienie serwera aplikacji SAP |PR1-di-0 |PR1-karta sieciowa di-0 |10.0.0.50 |
| Drugie wystąpienie serwera aplikacji SAP |pr1-di-1 |PR1-karta sieciowa di-1 |10.0.0.51 |
| Przyciski ... |Przyciski ... |Przyciski ... |Przyciski ... |
| Ostatnie wystąpienie serwera aplikacji SAP |PR1-di-5 |PR1-karta sieciowa di-5 |10.0.0.55 |
| Pierwszym węźle klastra na potrzeby wystąpienia ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Drugim węźle klastra na potrzeby wystąpienia ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Pierwszym węźle klastra dla wystąpienia systemu DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Drugim węźle klastra dla wystąpienia systemu DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ustawianie statycznego adresu IP dla modułu równoważenia obciążenia wewnętrznego platformy Azure

Szablon SAP usługi Azure Resource Manager umożliwia utworzenie Azure wewnętrznego modułu równoważenia obciążenia używanego dla wystąpienia SAP ASCS/SCS i klastrze DBMS.

> [!IMPORTANT]
> Adres IP, nazwy hostów wirtualnych SAP ASCS/SCS jest taki sam jak adres IP modułu równoważenia obciążenia wewnętrznego SAP ASCS/SCS: **pr1-lb-ascs**.
> Adres IP, nazwy wirtualnego systemu DBMS jest taki sam jak adres IP modułu równoważenia obciążenia wewnętrznego systemu DBMS: **pr1-lb-dbms**.
>
>

Aby ustawić statyczny adres IP dla modułu równoważenia obciążenia wewnętrznego platformy Azure:

1. Początkowe wdrożenie ustawia adres IP modułu równoważenia obciążenia wewnętrznego **dynamiczne**. W witrynie Azure portal na **adresów IP** bloku, w obszarze **przypisania**, wybierz opcję **statyczne**.
2. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-ascs** adres IP, nazwy hostów wirtualnych wystąpienia SAP ASCS/SCS.
3. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **pr1-lb-dbms** adres IP, nazwy hostów wirtualnych wystąpienia systemu DBMS.

   ![Ilustracja 14 Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia na potrzeby wystąpienia SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Ilustracja 14** Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia na potrzeby wystąpienia SAP ASCS/SCS_

W tym przykładzie mamy dwóch Azure wewnętrznych modułów równoważenia obciążenia, które mają te statyczne adresy IP:

| Rola modułu równoważenia obciążenia wewnętrznego platformy Azure | Nazwa modułu równoważenia obciążenia wewnętrznego platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| SAP ASCS/SCS wystąpienia wewnętrznego modułu równoważenia obciążenia |pr1-lb-ascs |10.0.0.43 |
| Systemu SAP DBMS wewnętrznego modułu równoważenia obciążenia |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Domyślne ASCS/SCS reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia

Szablon SAP usługi Azure Resource Manager umożliwia utworzenie porty, które są potrzebne:
* Wystąpienie ABAP ASCS o numerze wystąpienia domyślnego **00**
* Wystąpienie usług SCS Java, przy użyciu domyślnej liczby wystąpień **01**

Po zainstalowaniu wystąpienia usługi SAP ASCS/SCS, należy użyć domyślnej liczby wystąpień **00** wystąpienia ABAP ASCS i numer wystąpienia domyślnego **01** wystąpienia SCS języka Java.

Następnie należy utworzyć wymagane wewnętrznego równoważenia obciążenia, punkty końcowe dla portów oprogramowanie SAP NetWeaver.

Aby utworzyć wymagane wewnętrznego równoważenia obciążenia, punktów końcowych, najpierw utwórz tych końcowych dla portów SAP NetWeaver ABAP ASCS równoważenia obciążenia:

| Nazwa reguły z równoważenia obciążenia/usługi | Domyślne numery portów | Konkretnych portów dla (ASCS wystąpienia o numerze wystąpienia 00) (Wywołujących z 10) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32 <*Numerwystąpienia*> |3200 |
| ABAP Message Server / *lbrule3600* |36 <*Numerwystąpienia*> |3600 |
| Internal ABAP Message / *lbrule3900* |39 <*Numerwystąpienia*> |3900 |
| Serwer HTTP komunikatów / *Lbrule8100* |81 <*Numerwystąpienia*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5 <*Numerwystąpienia*> 13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5 <*Numerwystąpienia*> 14 |50014 |
| Umieścić w kolejce replikacji / *Lbrule50016* |5 <*Numerwystąpienia*> 16 |50016 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51013* |5 <*Numerwystąpienia*> 13 |51013 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51014* |5 <*Numerwystąpienia*> 14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

_**Tabela 1:** Numery portów wystąpień oprogramowania SAP NetWeaver ABAP ASCS_

Następnie utwórz tych końcowych dla portów SAP NetWeaver Java SCS równoważenia obciążenia:

| Nazwa reguły z równoważenia obciążenia/usługi | Domyślne numery portów | Konkretnych portów dla (SCS wystąpienia o numerze wystąpienia 01) (Wywołujących z 11) |
| --- | --- | --- |
| Enqueue Server / *lbrule3201* |32 <*Numerwystąpienia*> |3201 |
| Serwer bramy / *lbrule3301* |33 <*Numerwystąpienia*> |3301 |
| Java Message Server / *lbrule3900* |39 <*Numerwystąpienia*> |3901 |
| Serwer HTTP komunikatów / *Lbrule8101* |81 <*Numerwystąpienia*> |8101 |
| SAP Start usług SCS HTTP / *Lbrule50113* |5 <*Numerwystąpienia*> 13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5 <*Numerwystąpienia*> 14 |50114 |
| Umieścić w kolejce replikacji / *Lbrule50116* |5 <*Numerwystąpienia*> 16 |50116 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51113* |5 <*Numerwystąpienia*> 13 |51113 |
| SAP uruchamiania usługi Wywołujących HTTP *Lbrule51114* |5 <*Numerwystąpienia*> 14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Udział plików *Lbrule445* | |445 |

_**Tabela 2:** Numery portów wystąpień SAP NetWeaver Java SCS_

![Ilustracja 15 Domyślne ASCS/SCS reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia][sap-ha-guide-figure-3004]

_**Ilustracja 15** Domyślne ASCS/SCS reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia_

Ustaw adres IP modułu równoważenia obciążenia **pr1-lb-dbms** adres IP, nazwy hostów wirtualnych wystąpienia systemu DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Zmień ASCS/SCS domyślne reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia

Jeśli chcesz użyć innej liczby wystąpień SAP ASCS lub SCS, możesz zmienić nazwy i wartości ich portów z wartościami domyślnymi.

1. W witrynie Azure portal wybierz  **< *SID*> moduł równoważenia obciążenia ascs - lb -**  > **załadować reguł równoważenia**.
2. W przypadku reguł, które należą do wystąpienia SAP ASCS lub SCS równoważenia obciążenia wszystkie zmieniać tych wartości:

   * Name (Nazwa)
   * Port
   * Port zaplecza

   Na przykład jeśli chcesz zmienić domyślny numer wystąpienia ASCS z zakresu od 00 do 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

   Oto przykład aktualizacji dla portu *lbrule3200*.

   ![Ilustracja 16 Zmień ASCS/SCS domyślne reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia][sap-ha-guide-figure-3005]

   _**Ilustracja 16** Zmień ASCS/SCS domyślne reguł równoważenia obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Dodawanie maszyn wirtualnych Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych, należy dodać maszyn wirtualnych do domeny.

![Ilustracja 17 Dodaj maszynę wirtualną do domeny][sap-ha-guide-figure-3006]

_**Ilustracja 17** Dodaj maszynę wirtualną do domeny_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Dodawanie wpisów rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS

Usługa Azure Load Balancer ma wewnętrznego modułu równoważenia obciążenia, zamyka połączeń w przypadku połączeń są w stanie bezczynności pewien okres czasu (limit czasu bezczynności). Procesy robocze SAP w oknie dialogowym wystąpień otwartych połączeń można umieścić w kolejce SAP przetwarzać tak szybko, jak pierwszy dodawania/usuwania z kolejki żądania musi być wysyłane. Te połączenia zazwyczaj pozostaje ustanowionych do procesu roboczego lub ponowne uruchomienie procesu umieścić w kolejce. Jednakże jeśli połączenie jest bezczynne na pewien okres czasu, Azure wewnętrznego modułu równoważenia obciążenia zamyka połączenia. Nie stanowi problemu, ponieważ proces roboczy SAP przywraca połączenie do procesu umieścić w kolejce, jeśli już nie istnieje. Te działania są udokumentowane w śladach projektanta procesów SAP, ale mogą tworzyć dużą ilością dodatkowa zawartość w tych śladów. To dobry pomysł, aby zmienić TCP/IP `KeepAliveTime` i `KeepAliveInterval` na obu węzłach klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP, opisane w dalszej części tego artykułu.

Aby dodać wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS, najpierw dodaj te wpisy rejestru Windows na obu węzłach klastra Windows SAP ASCS/SCS programu:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (Decimal) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** Zmień pierwszy parametr TCP/IP_

Następnie należy dodać wpisy rejestru w tym Windows na obu węzłach klastra Windows SAP ASCS/SCS programu:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (Decimal) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabela 4:** Zmień drugi parametr TCP/IP_

**Aby zastosować zmiany, ponownie uruchom oba węzły klastra**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurowanie klastra systemu Windows Server Failover Clustering dla wystąpienia SAP ASCS/SCS

Konfigurowanie klastra systemu Windows Server Failover Clustering dla wystąpienia SAP ASCS/SCS obejmuje następujące zadania:

- Zbieranie węzłów klastra w konfiguracji klastra
- Konfigurowanie monitora udostępniania plików klastra

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Zbieraj węzłów klastra w konfiguracji klastra

1. Dodawanie roli i funkcje kreatora Dodaj klaster do obu węzłów klastra trybu failover.
2. Konfigurowanie klastra trybu failover przy użyciu Menedżera klastra trybu Failover. W Menedżerze klastra trybu Failover wybierz **tworzenia klastrów**, a następnie dodaj tylko nazwę pierwszego klastra węzeł A. Nie należy dodawać drugiego węzła jeszcze; należy dodać drugi węzeł w późniejszym kroku.

   ![Ilustracja 18 Dodaj nazwę serwera lub maszyny wirtualnej w pierwszym węźle klastra][sap-ha-guide-figure-3007]

   _**Ilustracja 18** Dodaj nazwę serwera lub maszyny wirtualnej w pierwszym węźle klastra_

3. Wprowadź nazwę sieci (nazwa hosta wirtualnego) klastra.

   ![Ilustracja 19 Wprowadź nazwę klastra][sap-ha-guide-figure-3008]

   _**Ilustracja 19** Wprowadź nazwę klastra_

4. Po utworzeniu klastra, uruchom test sprawdzania poprawności klastra.

   ![Ilustracja 20 Uruchom sprawdzenie poprawności klastra][sap-ha-guide-figure-3009]

   _**Ilustracja 20** Uruchom sprawdzenie poprawności klastra_

   Możesz zignorować ostrzeżenia dotyczące dysków na tym etapie w procesie. Należy dodać monitora udostępniania plików i oprogramowanie SIOS udostępnionych dysków później. Na tym etapie nie trzeba martwić o kworum.

   ![Ilustracja 21 Nie dysku kworum zostanie znaleziony.][sap-ha-guide-figure-3010]

   _**Ilustracja 21** Nie dysku kworum zostanie znaleziony._

   ![Ilustracja 22 Zasób klastra Core wymaga nowego adresu IP][sap-ha-guide-figure-3011]

   _**Ilustracja 22** Zasób klastra Core wymaga nowego adresu IP_

5. Zmień adres IP podstawowej usługi klastra. Klaster nie można uruchomić, dopóki nie zmieni adres IP podstawowej usługi klastra, ponieważ adres IP serwera wskazuje na jednym z węzłów maszyny wirtualnej. To zrobić na **właściwości** strony podstawowej usługi klastrowania zasób adresu IP.

   Na przykład, należy przypisać adres IP (w naszym przykładzie **10.0.0.42**) dla nazwy hosta wirtualnego klastra **pr1-ascs-vir**.

   ![Ilustracja 23: W oknie dialogowym właściwości zmiana adresu IP][sap-ha-guide-figure-3012]

   _**Ilustracja 23:** W **właściwości** okna dialogowego pola, zmiana adresu IP_

   ![Rysunek 24: Przypisanie adresu IP, która jest zarezerwowana dla klastra][sap-ha-guide-figure-3013]

   _**Rysunek 24:** Przypisanie adresu IP, która jest zarezerwowana dla klastra_

6. Przenieś nazwę hosta wirtualnego klastra online.

   ![Rysunek 25: Usługa core klastrowania jest uruchomiona i działa i poprawne IP adresów][sap-ha-guide-figure-3014]

   _**Rysunek 25:** Usługa core klastrowania jest uruchomiona i działa i poprawne IP adresów_

7. Dodaj drugi węzeł klastra.

   Teraz, że podstawowa usługa klastrowania jest uruchomiona, można dodać drugi węzeł klastra.

   ![Ilustracja 26 Dodaj drugi węzeł klastra][sap-ha-guide-figure-3015]

   _**Ilustracja 26** Dodaj drugi węzeł klastra_

8. Wprowadź nazwę dla drugiego hosta węzła klastra.

   ![Ilustracja 27 Wprowadź nazwę hosta drugiego węzła klastra][sap-ha-guide-figure-3016]

   _**Ilustracja 27** Wprowadź nazwę hosta drugiego węzła klastra_

   > [!IMPORTANT]
   > Upewnij się, że **Dodaj wszystkie odpowiednie magazyny do klastra** pole wyboru jest **nie** wybrane.  
   >
   >

   ![Ilustracja 28 Nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

   _**Ilustracja 28** Czy **nie** zaznacz pole wyboru_

   Możesz zignorować ostrzeżenia dotyczące kworum i dysków. Można ustawić kworum i później współużytkować dysku, zgodnie z opisem w [instalacji oprogramowanie SIOS DataKeeper Cluster Edition dla dysku udział klastra SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Ilustracja 29 Ignoruj ostrzeżenia dotyczące dysku kworum][sap-ha-guide-figure-3018]

   _**Ilustracja 29** Ignoruj ostrzeżenia dotyczące dysku kworum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurowanie monitora udziału plików klastra

Konfigurowanie monitora udostępniania plików klastra obejmuje następujące zadania:

- Tworzenie udziału plików
- Ustawienia kworum monitora udziału plików w Menedżerze klastra trybu Failover

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Utwórz udział plików

1. Wybierz monitor udziału plików, zamiast dysku kworum. Oprogramowanie SIOS DataKeeper obsługuje tę opcję.

   W przykładach w tym artykule monitora udostępniania plików znajduje się na serwerze/DNS usługi Active Directory, który działa w systemie Azure. Monitor udziału plików jest nazywany **domcontr 0**. Czy skonfigurować połączenia sieci VPN na platformie Azure (za pośrednictwem sieci VPN typu lokacja-lokacja lub ExpressRoute systemu Azure), monitor udostępniania swojej/DNS usługi Active Directory w środowisku lokalnym i usługi nie jest odpowiednia do uruchamiania pliku.

   > [!NOTE]
   > Jeśli usługa/DNS usługi Active Directory działa tylko w środowisku lokalnym, nie należy konfigurować usługi monitora udziału plików w systemie operacyjnym Windows usługi Active Directory/DNS, który jest uruchomiony w środowisku lokalnym. Opóźnienie sieci między węzłami klastra z systemem Azure i/DNS usługi Active Directory w lokalnym programie może być za duży i spowodować problemy z połączeniem. Pamiętaj skonfigurować monitor udostępniania plików na maszynie wirtualnej platformy Azure, które jest uruchamiane w pobliżu węzła klastra.  
   >
   >

   Dysku kworum wymaga przynajmniej 1024 MB wolnego miejsca na dysku. Zaleca się 2048 MB wolnego miejsca na dysku kworum.

2. Dodaj obiekt nazwy klastra.

   ![Ilustracja 30 Przypisz uprawnienia w udziale obiektu nazwy klastra][sap-ha-guide-figure-3019]

   _**Ilustracja 30** Przypisz uprawnienia w udziale obiektu nazwy klastra_

   Pamiętaj, że uprawnienia obejmują uprawnień do zmiany danych w udziale dla obiektu nazwy klastra (w naszym przykładzie **pr1-ascs-vir$** ).

3. Aby dodać obiekt nazwy klastra, do listy, wybierz **Dodaj**. Zmień filtr, aby wyszukać obiekty komputerów, oprócz tych pokazano na rysunku 31.

   ![Ilustracja 31 Zmień typy obiektów, aby uwzględnić komputery][sap-ha-guide-figure-3020]

   _**Ilustracja 31** Zmień typy obiektów, aby uwzględnić komputery_

   ![Ilustracja 32 Zaznacz pole wyboru komputerów][sap-ha-guide-figure-3021]

   _**Ilustracja 32** Wybierz **komputerów** pola wyboru_

4. Wprowadź obiekt nazwy klastra, jak pokazano na rysunku 31. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 30.

5. Wybierz **zabezpieczeń** na karcie udziału, a następnie ustaw bardziej szczegółowe uprawnienia dla obiektu nazwy klastra.

   ![Ilustracja 33 Ustaw atrybuty zabezpieczeń dla obiektu nazwy klastra w kworum udziału plików][sap-ha-guide-figure-3022]

   _**Ilustracja 33** Ustaw atrybuty zabezpieczeń dla obiektu nazwy klastra w kworum udziału plików_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ustaw kworum monitora udziału plików w Menedżerze klastra trybu Failover

1. Otwórz kworum ustawienia kreatora konfiguracji.

   ![Ilustracja 34 Uruchamianie Kreatora ustawienie Konfigurowanie kworum klastra][sap-ha-guide-figure-3023]

   _**Ilustracja 34** Uruchamianie Kreatora ustawienie Konfigurowanie kworum klastra_

2. Na **wybierz konfigurację kworum** wybierz **Wybieranie monitora kworum**.

   ![Ilustracja 35 Konfiguracji kworum, w których mogą wybierać][sap-ha-guide-figure-3024]

   _**Ilustracja 35** Konfiguracji kworum, w których mogą wybierać_

3. Na **Wybieranie monitora kworum** wybierz **Konfigurowanie monitora udziału plików**.

   ![Ilustracja 36 Wybierz monitor udziału plików][sap-ha-guide-figure-3025]

   _**Ilustracja 36** Wybierz monitor udziału plików_

4. Wprowadź ścieżkę UNC do udziału plików (w naszym przykładzie \\domcontr 0\FSW). Aby wyświetlić listę wprowadzania zmian, wybierz **dalej**.

   ![Ilustracja 37 Zdefiniuj lokalizację udziału plików monitora udziału][sap-ha-guide-figure-3026]

   _**Ilustracja 37** Zdefiniuj lokalizację udziału plików monitora udziału_

5. Wybierz zmiany, a następnie wybierz **dalej**. Należy pomyślnie ponownie skonfigurować konfigurację klastra, jak pokazano na rysunku 38.  

   ![Ilustracja 38 Potwierdzenie, że został ponownie skonfigurowany klaster][sap-ha-guide-figure-3027]

   _**Ilustracja 38** Potwierdzenie, że został ponownie skonfigurowany klaster_

Po zainstalowaniu klastra pracy awaryjnej Windows pomyślnie, zmiany trzeba dokonać niektóre wartości progowe w celu dostosowania trybu failover wykrywania warunków na platformie Azure. Parametry, które mają być zmienione są opisane w tym blogu: [ https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834 ](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Przy założeniu, że usługi dwie maszyny wirtualne, które są kompilowane w konfiguracji klastra Windows for ASCS/SCS znajdują się w tej samej podsieci, należy można zmienić tych wartości następujących parametrów:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Te ustawienia zostały przetestowane z klientami i podano dobre naruszenia zabezpieczeń, aby była odporna na jednej stronie. Z drugiej strony te ustawienia zostały materiały szybkiego wystarczająco dużo pracy awaryjnej w warunkach rzeczywistych błędu w przypadku awarii oprogramowania lub węzeł/maszyna wirtualna SAP. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Zainstaluj oprogramowanie SIOS DataKeeper Cluster Edition dla udziału dysk klastrowy SAP ASCS/SCS

Masz teraz działającą konfiguracji systemu Windows Server Failover Clustering na platformie Azure. Jednak, aby zainstalować wystąpienie SAP ASCS/SCS, zasób udostępniony dysk. Nie można utworzyć współużytkowane zasoby dyskowe potrzebne na platformie Azure. Oprogramowanie SIOS DataKeeper Cluster Edition jest rozwiązań innych firm, których można używać do tworzenia współużytkowane zasoby dyskowe.

Instalowanie oprogramowanie SIOS DataKeeper Cluster Edition dla udziału dysk klastrowy SAP ASCS/SCS obejmuje następujące zadania:

- Dodawanie programu .NET Framework 3.5
- Instalowanie oprogramowanie SIOS DataKeeper
- Konfigurowanie oprogramowanie SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Dodawanie programu .NET Framework 3.5
Microsoft .NET Framework 3.5 nie jest automatycznie aktywowane lub zainstalowane w systemie Windows Server 2012 R2. Ponieważ oprogramowanie SIOS DataKeeper wymaga programu .NET Framework na wszystkie węzły, które należy zainstalować na DataKeeper, należy zainstalować program .NET Framework 3.5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania programu .NET Framework 3.5:

- Użyj funkcji Kreatora dodawania ról i w Windows, jak pokazano na rysunku 39.

  ![Ilustracja 39 Instalowanie programu .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora][sap-ha-guide-figure-3028]

  _**Ilustracja 39** Instalowanie programu .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora_

  ![Ilustracja 40 Postęp instalacji pasek po zainstalowaniu programu .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora][sap-ha-guide-figure-3029]

  _**Ilustracja 40** Postęp instalacji pasek po zainstalowaniu programu .NET Framework 3.5 przy użyciu Dodaj role i funkcje kreatora_

- Za pomocą dism.exe narzędzie wiersza polecenia. Ten typ instalacji musisz uzyskać dostęp do katalogu SxS, na nośniku instalacyjnym programu Windows. W wierszu polecenia z podwyższonym poziomem uprawnień wpisz polecenie:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Zainstaluj oprogramowanie SIOS DataKeeper

Zainstaluj oprogramowanie SIOS DataKeeper Cluster Edition w każdym węźle w klastrze. Aby utworzyć wirtualny magazynu udostępnionego z oprogramowanie SIOS DataKeeper, utworzyć duplikat zsynchronizowane, a następnie symulować magazyn udostępniony klastra.

Przed zainstalowaniem oprogramowania oprogramowanie SIOS należy utworzyć użytkownika domeny **DataKeeperSvc**.

> [!NOTE]
> Dodaj **DataKeeperSvc** użytkownikowi **administratora lokalnego** grupy na obu węzłach klastra.
>
>

Aby zainstalować oprogramowanie SIOS DataKeeper:

1. Zainstaluj oprogramowanie oprogramowanie SIOS na obu węzłach klastra.

   ![Oprogramowanie SIOS Instalatora][sap-ha-guide-figure-3030]

   ![Ilustracja 41 Pierwsza strona instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Ilustracja 41** Pierwsza strona instalacji oprogramowanie SIOS DataKeeper_

2. W oknie dialogowym pokazano na rysunku 42 wybierz **tak**.

   ![Ilustracja 42 DataKeeper informuje, że usługa zostanie wyłączona][sap-ha-guide-figure-3032]

   _**Ilustracja 42** DataKeeper informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym pokazano na rysunku 43 zaleca się wybranie **konta domeny lub serwer**.

   ![Ilustracja 43 Oprogramowanie SIOS DataKeeper wybór użytkownika][sap-ha-guide-figure-3033]

   _**Ilustracja 43** Oprogramowanie SIOS DataKeeper wybór użytkownika_

4. Wprowadź nazwę użytkownika konta domeny i hasła, które utworzono dla oprogramowanie SIOS DataKeeper.

   ![Ilustracja 44 Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Ilustracja 44** Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper_

5. Zainstaluj klucz licencji dla swojego wystąpienia oprogramowanie SIOS DataKeeper, jak pokazano na rysunku 45.

   ![Ilustracja 45 Podaj klucz licencji na oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Ilustracja 45** Podaj klucz licencji na oprogramowanie SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Skonfiguruj oprogramowanie SIOS DataKeeper

Po zainstalowaniu oprogramowanie SIOS DataKeeper na obu węzłach, należy uruchomić konfigurację. Celem konfiguracji jest synchronicznej replikacji danych między dodatkowe dyski VHD dołączone do wszystkich maszyn wirtualnych.

1. Uruchom narzędzie DataKeeper zarządzania i konfiguracji, a następnie wybierz **Połącz serwerem**. (Rysunek 46 — w tej opcji jest zakreślony na czerwono.)

   ![Ilustracja 46 Oprogramowanie SIOS DataKeeper zarządzania i konfiguracji narzędzia][sap-ha-guide-figure-3036]

   _**Ilustracja 46** Oprogramowanie SIOS DataKeeper zarządzania i konfiguracji narzędzia_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, w których narzędzie zarządzania i konfiguracji należy połączenia oraz, w drugim kroku drugiego węzła.

   ![Ilustracja 47 Wstaw nazwę lub adres TCP/IP pierwszego węzła zarządzania i narzędzia do konfiguracji należy łączyć, a w drugim kroku drugiego węzła][sap-ha-guide-figure-3037]

   _**Ilustracja 47** Wstaw nazwę lub adres TCP/IP pierwszego węzła zarządzania i narzędzia do konfiguracji należy łączyć, a w drugim kroku drugiego węzła_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Ilustracja 48 Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _**Ilustracja 48** Tworzenie zadania replikacji_

   Kreator prowadzi przez proces tworzenia zadania replikacji.
4. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła źródłowego.

   ![Rysunek 49: Zdefiniuj nazwę zadania replikacji][sap-ha-guide-figure-3039]

   _**Rysunek 49:** Zdefiniuj nazwę zadania replikacji_

   ![50 rysunku: Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącego węzła źródłowego][sap-ha-guide-figure-3040]

   _**50 rysunku:** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącego węzła źródłowego_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Ilustracja 51 Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącego węzła docelowego][sap-ha-guide-figure-3041]

   _**Ilustracja 51** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącego węzła docelowego_

6. Zdefiniuj algorytmy kompresji. W tym przykładzie firma Microsoft zaleca kompresowania strumienia replikacji. Szczególnie w sytuacjach, podczas ponownej synchronizacji Kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Należy zauważyć, że kompresja używa zasobów procesora CPU i pamięci RAM maszyny wirtualnej. W miarę zwiększania szybkości kompresji, co powoduje woluminów używanych zasobów procesora CPU. Możesz również dostosować to ustawienie, później.

7. Inne ustawienie, które należy sprawdzić to, czy replikacja jest wykonywana asynchronicznie lub synchronicznie. *W przypadku ochrony konfiguracji SAP ASCS/SCS, należy użyć Replikacja synchroniczna*.  

   ![Ilustracja 52: Zdefiniuj szczegóły replikacji][sap-ha-guide-figure-3042]

   _**Ilustracja 52:** Zdefiniuj szczegóły replikacji_

8. Zdefiniuj, czy wolumin, który jest replikowany za zadanie replikacji powinny być reprezentowane w konfiguracji klastra z systemem Windows Server Failover Clustering jako udostępniony dysk. W przypadku konfiguracji SAP ASCS/SCS wybierz **tak** tak, aby klastra Windows uznaje replikowanego woluminu dysku udostępnionego, który można użyć jako woluminu klastra.

   ![Ilustracja 53 Wybierz pozycję Tak, aby ustawić replikowanego woluminu jako woluminu klastra][sap-ha-guide-figure-3043]

   _**Ilustracja 53** Wybierz **tak** można ustawić replikowanego woluminu jako woluminu klastra_

   Po jego utworzeniu, narzędzie DataKeeper zarządzania i konfiguracji pokazuje, że zadanie replikacji jest aktywne.

   ![Ilustracja 54 DataKeeper synchronicznego duplikowania dysku udziału SAP ASCS/SCS jest aktywny][sap-ha-guide-figure-3044]

   _**Ilustracja 54** DataKeeper synchronicznego duplikowania dysku udziału SAP ASCS/SCS jest aktywny_

   Menedżer klastra trybu failover jest teraz wyświetlana dysku jako dysku DataKeeper, jak pokazano na rysunku 55.

   ![Ilustracja 55 Menedżer klastra trybu failover zawiera dysk czy DataKeeper zreplikowane][sap-ha-guide-figure-3045]

   _**Ilustracja 55** Menedżer klastra trybu failover zawiera dysk czy DataKeeper zreplikowane_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalowanie systemu SAP NetWeaver

Nie będzie opisujemy Instalatora systemu DBMS, ponieważ ustawienia różnią się w zależności od używanego systemu DBMS Jednak przyjęto założenie, że problemów o wysokiej dostępności z systemu DBMS są adresowane za pomocą funkcji, które różnych dostawców DBMS pomoc techniczna systemu Azure. Na przykład AlwaysOn lub dublowania bazy danych SQL Server i Oracle Data Guard dla baz danych Oracle. W tym scenariuszu, których używamy w tym artykule firma Microsoft nie został dodany zwiększa ochronę dla systemu DBMS.

Istnieją uwzględniać żadnych szczególnych kwestii, gdy różne usługi systemu DBMS na interakcję z tego rodzaju konfiguracji SAP ASCS/SCS klastra na platformie Azure.

> [!NOTE]
> Procedury instalacji systemów SAP NetWeaver ABAP, Java i systemów ABAP + Java są niemal identyczne. Najbardziej znaczący różnica polega na tym, że system SAP ABAP zawiera jedno wystąpienie ASCS. System SAP języka Java zawiera jedno wystąpienie usług SCS. System SAP ABAP + Java zawiera jedno wystąpienie ASCS i jedno wystąpienie usług SCS uruchomione w tej samej grupie klastra trybu failover firmy Microsoft. Różnice instalacji dla poszczególnych stosów instalacji oprogramowania SAP NetWeaver jawnie wymienione. Można zakładać, że inne części są takie same.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalowanie SAP za pomocą wystąpienia ASCS/SCS wysokiej dostępności

> [!IMPORTANT]
> Pamiętaj, aby nie umieszczenia pliku stronicowania na woluminach DataKeeper dublowanych. DataKeeper nie obsługuje woluminy dublowane. Można pozostawić pliku stronicowania na tymczasowym dysku D maszynie wirtualnej platformy Azure, co jest ustawieniem domyślnym. Jeśli nie jest już istnieje, należy przenieść plik stronicowania Windows na dysku D maszyn wirtualnych.
>
>

Instalowanie SAP za pomocą wystąpienia ASCS/SCS wysokiej dostępności obejmuje następujące zadania:

- Tworzenie nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS
- Instalowanie na pierwszym węźle klastra SAP
- Modyfikowanie profilu SAP ASCS/SCS wystąpienia
- Dodawanie port sondy
- Otwieranie portu sondy zapory Windows

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Utwórz nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS

1. W Menedżerze Windows DNS utworzyć wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS.

   > [!IMPORTANT]
   > Adres IP, który zostanie przypisany do nazwy hosta wirtualnego wystąpienia ASCS/SCS musi być taka sama, jak adres IP, która została przypisana do usługi Azure Load Balancer ( **<*SID*> - lb - ascs**).  
   >
   >

   Adres IP, nazwy wirtualnego hosta SAP ASCS/SCS (**pr1 ascs sap**) jest taka sama jak adres IP modułu równoważenia obciążenia platformy Azure (**pr1-lb-ascs**).

   ![Ilustracja 56 Zdefiniuj wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Ilustracja 56** Zdefiniuj wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adresu TCP/IP_

2. Aby zdefiniować adres IP przypisany do nazwy hostów wirtualnych, wybierz **Menedżera DNS** > **domeny**.

   ![Rysunek 57: Nowa nazwa wirtualnego i adresu TCP/IP dla konfiguracji klastra SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Rysunek 57:** Nowa nazwa wirtualnego i adresu TCP/IP dla konfiguracji klastra SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Zainstaluj oprogramowanie SAP pierwszym węźle klastra

1. Wykonanie pierwszej opcji węzła klastra w węźle klastra A. Na przykład na **pr1 ascs 0** hosta.
2. Aby pozostawić domyślne porty dla platformy Azure wewnętrznego modułu równoważenia obciążenia, wybierz:

   * **ABAP system**: **ASCS** numer wystąpienia **00**
   * **Java system**: **SCS** numer wystąpienia **01**
   * **System ABAP + Java**: **ASCS** numer wystąpienia **00** i **SCS** numer wystąpienia **01**

   Aby użyć wystąpienia liczb innych niż 00 wystąpienie ABAP ASCS i 01 dla wystąpienia Java SCS, najpierw należy zmienić reguły, opisane w równoważenia obciążenia domyślnego modułu równoważenia obciążenia wewnętrznego platformy Azure [zmienić ASCS/SCS domyślne reguł równoważenia obciążenia dla Usługa Azure wewnętrznego modułu równoważenia obciążenia][sap-ha-guide-8.9].

Dalej kilka zadań nie są opisane w standardowych dokumentacji instalacji SAP.

> [!NOTE]
> Dokumentacji instalacji SAP w tym artykule opisano sposób instalowania pierwszego węzła klastra ASCS/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modyfikowanie profilu SAP ASCS/SCS wystąpienia

Należy dodać nowy parametr profilu. Parametr profilu uniemożliwia zamknięcie, gdy są one bezczynne zbyt długo połączenia między procesów roboczych SAP i serwerem umieścić w kolejce. Wspomnieliśmy scenariusz problemu w [dodania wpisów rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji także wprowadziliśmy dwie zmiany do niektórych podstawowych parametrów połączenia TCP/IP. W drugim kroku, należy ustawić serwera umieścić w kolejce do wysłania `keep_alive` sygnał, tak aby połączenia nie trafień Próg bezczynności modułu równoważenia obciążenia wewnętrznego platformy Azure.

Aby zmodyfikować profil SAP ASCS/SCS wystąpienie:

1. Dodaj parametr ten profil do profilu wystąpienia SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   W naszym przykładzie ścieżka jest:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Na przykład aby profil wystąpienia SAP SCS i odpowiednie ścieżki:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Aby zastosować zmiany, ponownie uruchom wystąpienie SAP ASCS /SCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Dodaj port sondy

Przy użyciu funkcji sondy modułu równoważenia obciążenia wewnętrznego konfigurację cały klaster pracy za pomocą usługi Azure Load Balancer. Azure wewnętrznego modułu równoważenia obciążenia dystrybuuje zwykle przychodzące obciążenie równo między uczestniczących w programie maszyn wirtualnych. Jednak to nie będzie działać w niektórych konfiguracjach klastra, ponieważ tylko jedno wystąpienie jest aktywny. Inne wystąpienie jest w stanie pasywnym i nie akceptuje dowolne obciążenie. Funkcja sondy pomaga w Azure wewnętrznego modułu równoważenia obciążenia przypisuje pracy tylko do aktywnego wystąpienia. Dzięki funkcji sondy wewnętrznego modułu równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie wskazać tylko wystąpienia z obciążeniem.

Aby dodać port sondy:

1. Sprawdź bieżące **ProbePort** ustawienie, uruchamiając następujące polecenie programu PowerShell. Wykonaj go z jednej z maszyn wirtualnych w konfiguracji klastra.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Zdefiniuj port sondy. Domyślny numer portu sondy to **0**. W naszym przykładzie używamy port sondy **62000**.

   ![Ilustracja 58: Port sondy konfiguracji klastra jest równa 0, domyślnie][sap-ha-guide-figure-3048]

   _**Ilustracja 58:** Domyślny port sondy konfiguracji klastra ma wartość 0_

   Numer portu jest zdefiniowana w systemie SAP usługi Azure Resource Manager. Można przypisać numer portu w programie PowerShell.

   Aby ustawić nową wartość ProbePort **SAP <*SID*> IP** zasób klastra, uruchom następujący skrypt programu PowerShell. Zaktualizuj zmienne programu PowerShell dla danego środowiska. Po uruchomieniu skryptu zostanie wyświetlony monit o ponowne uruchomienie Grupa klastra SAP do aktywowania zmian.

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

   Po przekazaniu **SAP <*SID*>** klastra roli w tryb online, upewnij się, że **ProbePort** ustawiono nową wartość.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Ilustracja 59 Sonda portu klastra, po ustawieniu nowej wartości][sap-ha-guide-figure-3049]

   _**Ilustracja 59** Sonda portu klastra, po ustawieniu nowej wartości_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otwórz port sondy zapory Windows

Musisz otworzyć port sondy zapory Windows na obu węzłach klastra. Użyj następującego skryptu, aby otworzyć port sondy zapory Windows. Zaktualizuj zmienne programu PowerShell dla danego środowiska.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** ustawiono **62000**. Teraz można uzyskać dostęp do udziału plików  **\\\ascsha-clsap\sapmnt** od innych hostów, takie jak z **przetwarzający ascsha**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Zainstaluj wystąpienie bazy danych

Aby zainstalować wystąpienie bazy danych, wykonaj proces opisany w dokumentacji instalacji SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Zainstaluj na drugim węźle klastra

Aby zainstalować drugi klaster, postępuj zgodnie z instrukcjami w przewodniku instalacji SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Zmień typ uruchomienia wystąpienia usługi Windows Wywołujących SAP

Zmień typ uruchomienia usługi Windows Wywołujących SAP **automatycznie (opóźnione uruchomienie)** na obu węzłach klastra.

![Ilustracja 60 Zmień typ usługi dla wystąpienia SAP Wywołujących opóźnione automatycznie][sap-ha-guide-figure-3050]

_**Ilustracja 60** Zmień typ usługi dla wystąpienia SAP Wywołujących opóźnione automatycznie_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalowanie serwera podstawowego aplikacji SAP

Zainstaluj wystąpienie serwera aplikacji głównej (PAS) <*SID*> - di - 0 na maszynie wirtualnej, który został wybrany do hostowania PAS. Nie występują żadne zależności na temat platformy Azure lub DataKeeper określonych ustawień.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalowanie serwera dodatkowych aplikacji SAP

Zainstaluj oprogramowanie SAP dodatkowych aplikacji serwera (AAS) na wszystkich maszynach wirtualnych, które zostały oznaczone do hostowania wystąpienia serwera aplikacji SAP. Na przykład w <*SID*> - di - 1 do <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Spowoduje to zakończenie instalacji systemu SAP NetWeaver wysokiej dostępności. Następnie należy kontynuować testowanie trybu failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testowanie trybu failover wystąpienia SAP ASCS/SCS i oprogramowanie SIOS replikacji
Jest łatwy do testowania i monitorowania usługi SAP ASCS/SCS wystąpienia w tryb failover i oprogramowanie SIOS replikacji dysku przy użyciu narzędzia Menedżera klastra trybu Failover i oprogramowanie SIOS DataKeeper zarządzania i konfiguracji.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Wystąpienia SAP ASCS/SCS jest uruchomiona w węźle klastra, A

**SAP PR1** grupy klastra jest uruchomiona w węźle klastra A. Na przykład na **pr1 ascs 0**. Przypisywanie udostępnionego dysku S, który jest częścią programu **SAP PR1** Grupa klastra i który używa wystąpienia ASCS/SCS, do klastra węzeł A.

![Ilustracja 61 Menedżer klastra trybu failover: SAP < SID > grupy klastra jest uruchomiona w węźle klastra, A][sap-ha-guide-figure-5000]

_**Ilustracja 61** Menedżer klastra trybu failover: SAP <*SID*> grupy klastra jest uruchomiona w węźle klastra, A_

W narzędziu oprogramowanie SIOS DataKeeper zarządzania i konfiguracji możesz zobaczyć, czy udostępniony dysk synchronicznie replikacji danych z dysku woluminu źródłowego S w węźle klastra, A na dysku woluminu docelowego S w węźle klastra B. Na przykład jest replikowany z **pr1 ascs 0 [10.0.0.40]** do **pr1 ascs 1 [10.0.0.41]** .

![Ilustracja 62 W oprogramowanie SIOS DataKeeper replikowania woluminu lokalnego z węzła klastra, A do węzła klastra B][sap-ha-guide-figure-5001]

_**Ilustracja 62** W oprogramowanie SIOS DataKeeper replikowania woluminu lokalnego z węzła klastra, A do węzła klastra B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Tryb failover z węzła A węźle B

1. Wybierz jedną z tych opcji, aby zainicjować trybu failover SAP <*SID*> Grupa klastra z węzła klastra, A do węzła klastra B:
   - Menedżer klastra trybu Failover  
   - Za pomocą programu PowerShell klastra trybu Failover

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Uruchom ponownie element węzła klastra w systemie operacyjnym gościa Windows (spowoduje to zainicjowanie automatycznej pracy awaryjnej programu SAP <*SID*> Grupa klastra z węzła A węzłem B).  
3. Uruchom ponownie element węzła klastra w witrynie Azure portal (spowoduje to zainicjowanie automatycznej pracy awaryjnej programu SAP <*SID*> Grupa klastra z węzła A węzłem B).  
4. Uruchom ponownie element węzła klastra za pomocą programu Azure PowerShell (spowoduje to zainicjowanie automatycznej pracy awaryjnej programu SAP <*SID*> Grupa klastra z węzła A węzłem B).

   Po przejściu w tryb failover SAP <*SID*> grupy klastra jest uruchomiona w węźle klastra B. Na przykład, jest uruchomiona na **pr1 ascs 1**.

   ![Ilustracja 63 W Menedżerze klastra trybu Failover SAP < SID > grupy klastra jest uruchomiona w węźle klastra B][sap-ha-guide-figure-5002]

   _**Rysunek 63**: W Menedżerze klastra trybu Failover, SAP <*SID*> grupy klastra jest uruchomiona w węźle klastra B_

   Udostępniony dysk jest teraz zainstalowany w klastrze węzła B. oprogramowanie SIOS DataKeeper jest replikowanie danych z dysku woluminu źródłowego S w węźle klastra B na dysku woluminu docelowego S w węźle klastra A. Na przykład jest replikowany z **pr1 ascs 1 [10.0.0.41]** do **pr1 ascs 0 [10.0.0.40]** .

   ![Ilustracja 64 Oprogramowanie SIOS DataKeeper replikuje woluminu lokalnego z węzła klastra B do A węzłem klastra][sap-ha-guide-figure-5003]

   _**Ilustracja 64** Oprogramowanie SIOS DataKeeper replikuje woluminu lokalnego z węzła klastra B do A węzłem klastra_
