---
title: Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażanie oprogramowania SAP na maszynach wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: sedusch
ms.openlocfilehash: c93bca14d9385eaf9f79f69d76e9e704796da7a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154061"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver

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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Buforowanie dla maszyn wirtualnych i wirtualnych dysków twardych)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Programowej macierzy RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura wdrożenia RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Wysoka dostępność i odzyskiwanie po awarii z maszyn wirtualnych platformy Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 lub nowszy)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 z dodatkiem SP1 CU3 i jego starszych wersji)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Przy użyciu obrazu programu SQL Server w witrynie Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Ogólne programu SQL Server dla SAP w systemie Azure podsumowanie)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Charakterystyka do programu SQL Server w systemie RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfiguracja magazynu)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Kopia zapasowa i przywracanie)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Inne)
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

[deployment-guide]:deployment-guide.md (Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Zasoby SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenariusz 1: Wdrażanie maszyny Wirtualnej portalu Azure Marketplace dla rozwiązania SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenariusz 2: Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu dla rozwiązania SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenariusz 3: Przenoszenie maszyny Wirtualnej ze środowiska lokalnego przy użyciu-uogólniony wirtualny dysk twardy platformy Azure z oprogramowaniem SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenariusze wdrażania maszyn wirtualnych dla rozwiązania SAP w systemie Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Wdrażanie polecenia cmdlet programu Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Pobierz i zaimportuj polecenia cmdlet programu PowerShell związanych z SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Dołącz Maszynę wirtualną do domeny w środowisku lokalnym — tylko Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Pobieranie, instalowanie i Włącz agenta maszyny Wirtualnej platformy Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Wiersza polecenia platformy Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Skonfiguruj rozszerzenie monitorowania rozszerzonej platformy Azure dla rozwiązania SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Sprawdzanie gotowości dla platformy Azure Enhanced Monitoring dla rozwiązania SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Sprawdzanie kondycji dla monitorowania infrastruktury platformy Azure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Rozwiązywanie problemów z usługi Azure monitoring dla rozwiązania SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurowanie monitorowania)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Skonfiguruj serwer proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Zasoby)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Wysoka dostępność i odzyskiwanie po awarii środowiska SAP NetWeaver uruchomione na maszynach wirtualnych platformy Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Wysoka dostępność dla serwerów aplikacji SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Za pomocą funkcji Autostart dla wystąpieniami platformy SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Tylko w chmurze — wdrażanie maszyn wirtualnych na platformie Azure bez uzależnienia od sieci klienta w środowisku lokalnym)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Między środowiskami lokalnymi - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure w pełni zintegrowana z sieci lokalnej)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiony platformy Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domeny błędów)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Uaktualnij domeny)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Zestawy dostępności platformy Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncepcja maszyn wirtualnych Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Usługi Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Przenoszenie maszyny Wirtualnej ze środowiska lokalnego do platformy Azure z dyskiem uogólniony)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Wdrażanie maszyny Wirtualnej przy użyciu określonego obrazu klienta)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Przygotowanie do przenoszenia maszyny Wirtualnej ze środowiska lokalnego na platformę Azure z dyskiem uogólniony)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Przygotowanie do wdrożenia maszyny Wirtualnej z określonego obrazu klienta dla rozwiązania SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Przygotowywanie maszyn wirtualnych z oprogramowaniem SAP na platformie Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Różnica między dysku platformy Azure i obrazów systemu Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Przekazywanie wirtualnego dysku twardego ze środowiska lokalnego do platformy Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopiowanie dysków między kontami magazynu Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura maszyny Wirtualnej/wirtualnego dysku twardego dla wdrożeń SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Ustawienie automatycznej instalacji dla dołączonych dysków)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Pojedyncza maszyna wirtualna z oprogramowaniem SAP NetWeaver pokaz/szkolenia scenariusza)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Pojęcia dotyczące tylko na chmurze wdrożenia wystąpieniami platformy SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure rozwiązanie do monitorowania dla rozwiązania SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Usługi Azure Premium Storage)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Managed Disks)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Sieć Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Miejsca do magazynowania: Usługa Microsoft Azure Storage i dysków z danymi)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktów SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Wdrażanie i zarządzanie maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Zarządzanie maszynami wirtualnymi za pomocą usługi Azure Resource Manager i programu PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Maszyny wirtualne platformy Azure to rozwiązanie dla organizacji, które potrzebują zasoby obliczeniowe i magazynowe, w krótkim czasie i bez wydłużonych cykli. Usługi Azure Virtual Machines umożliwia wdrażanie klasycznego aplikacji, takich jak aplikacje oparte na oprogramowanie SAP NetWeaver, na platformie Azure. Rozszerzanie aplikacji niezawodność i dostępność bez dodatkowych zasobów lokalnych. Maszyny wirtualne platformy Azure obsługuje łączność między wieloma lokalizacjami, dzięki czemu możesz zintegrować usługę Azure Virtual Machines domen lokalnych w organizacji, chmur prywatnych i środowisko systemu SAP.

W tym artykule omówione są kroki, aby wdrożyć aplikacje SAP na maszynach wirtualnych (VM) na platformie Azure, łącznie z opcjami wdrożenia alternatywnej i rozwiązywania problemów. W tym artykule opiera się na informacjach w [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide]. Uzupełnia również dokumentacji instalacji SAP i SAP Notes, które są podstawowe zasoby dotyczące instalowania i wdrażania oprogramowania SAP.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Konfigurowanie maszyny wirtualnej platformy Azure w celu wdrażania oprogramowania SAP obejmuje wiele kroków i zasobów. Przed rozpoczęciem upewnij się, że spełniają wymagania wstępne dotyczące instalowania oprogramowania SAP na maszynach wirtualnych platformy Azure.

### <a name="local-computer"></a>Komputer lokalny

Aby zarządzać, Windows lub maszyny wirtualne systemu Linux, można użyć skryptu programu PowerShell i witryny Azure portal. Oba narzędzia wymaga komputera lokalnego z systemem Windows 7 lub nowszej wersji systemu Windows. Jeśli chcesz zarządzać tylko maszyny wirtualne systemu Linux, którego chcesz użyć komputera z systemem Linux dla tego zadania można użyć wiersza polecenia platformy Azure.

### <a name="internet-connection"></a>Połączenie z Internetem

Aby pobrać i uruchomić narzędzia i skrypty, które są wymagane w celu wdrażania oprogramowania SAP, musi mieć połączenie z Internetem. Maszynę Wirtualną platformy Azure, która działa rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP również musi mieć dostęp do Internetu. Jeśli maszyna wirtualna platformy Azure jest częścią sieci wirtualnej platformy Azure lub lokalnej domeny, upewnij się, że właściwych ustawień serwera proxy są ustawione, zgodnie z opisem w [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subskrypcja platformy Microsoft Azure

Konieczne jest aktywne konto platformy Azure.

### <a name="topology-and-networking"></a>Topologia i sieci

Musisz zdefiniować topologii i architektura wdrożenia SAP na platformie Azure:

* Konta usługi Azure storage ma być używany
* Sieć wirtualna, której chcesz wdrożyć SAP system
* Grupa zasobów, do której chcesz wdrożyć SAP system
* Region platformy Azure, w której chcesz wdrożyć SAP system
* Konfiguracji SAP (dwuwarstwowej lub trójwarstwowej)
* Rozmiary maszyn wirtualnych i liczby dysków dodatkowe dane ma zostać zainstalowany na maszynach wirtualnych
* Konfiguracja korekcji SAP i System transportu (CTS)

Tworzenie i konfigurowanie konta usługi Azure storage (jeśli jest to wymagane) lub sieci wirtualne platformy Azure, przed rozpoczęciem procesu wdrażania oprogramowania SAP. Aby uzyskać informacje o tym, jak utworzyć i skonfigurować te zasoby, zobacz [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Ustalanie rozmiaru SAP

Znajomość następujących informacji w przypadku ustalania rozmiaru SAP:

* Przewidywane obciążenia SAP, na przykład za pomocą narzędzia Szybkie Rozmiar symboli SAP i SAP aplikacji wydajności Standard (punktu SAP) numer
* Wymagane Procesora zasobów i pamięci przez SAP system
* Wymagane we/wy (operacje We/Wy) na sekundę
* Wymagana przepustowość sieci wynosi ostatecznej komunikacji między maszynami wirtualnymi na platformie Azure
* Wymagana przepustowość sieci między zasobami lokalnymi i system SAP wdrożeniu platformy Azure

### <a name="resource-groups"></a>Grupy zasobów

W usłudze Azure Resource Manager, można użyć grup zasobów do zarządzania wszystkimi zasobami aplikacji w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Zasoby

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby SAP

Podczas konfigurowania wdrożenia oprogramowania SAP, potrzebne są następujące zasoby SAP:

* Uwaga SAP [1928533], która zawiera:
  * Listę rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Informacje o pojemności ważne w przypadku rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP i systemu operacyjnego (OS) i kombinacji bazy danych
  * Wymagana wersja jądra SAP dla Windows i Linux w systemie Microsoft Azure

* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP obsługiwane przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2178632] zawiera szczegółowe informacje o metrykach wszystkie funkcje monitorowania zgłoszone dla rozwiązania SAP na platformie Azure.
* Uwaga SAP [1409604] ma wymaganą wersję agenta hosta SAP dla Windows na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP, w systemie Linux na platformie Azure.
* Uwaga SAP [1984787] zawiera ogólne informacje o systemie SUSE Linux Enterprise Server 12.
* Uwaga SAP [2002167] zawiera ogólne informacje o systemie Red Hat Enterprise Linux 7.x.
* Uwaga SAP [2069760] zawiera ogólne informacje o systemie Oracle Linux 7.x.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP.
* Uwaga SAP [1597355] zawiera ogólne informacje na temat obszaru wymiany w systemie Linux.
* [SAP na stronie Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) ma wiadomości i zbiór przydatnych zasobów.
* [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux.
* SAP specyficzne dla poleceń cmdlet programu PowerShell, które są częścią [programu Azure PowerShell][azure-ps].
* SAP specyficzne dla platformy Azure polecenia interfejsu wiersza polecenia, które są częścią [wiersza polecenia platformy Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby Windows

Te artykuły Microsoft obejmuje wdrożenia SAP na platformie Azure:

* [Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver (w tym artykule)][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenariusze wdrażania oprogramowania SAP na maszynach wirtualnych platformy Azure

Istnieje wiele opcji do wdrażania maszyn wirtualnych i skojarzone dyski na platformie Azure. Ważne jest zrozumienie różnic między opcjami wdrożenia, ponieważ może potrwać innych czynności, aby przygotować maszyny wirtualne do wdrożenia na podstawie typu wdrożenia wybranej.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenariusz 1: Wdrażanie maszyny Wirtualnej portalu Azure Marketplace dla rozwiązania SAP

Obraz dostarczane przez firmę Microsoft lub innych firm w witrynie Azure Marketplace można użyć do wdrożenia maszyny Wirtualnej. Portal Marketplace oferuje kilka standardowych obrazów systemu operacyjnego, systemu Windows Server i różnymi dystrybucjami systemu Linux. Ponadto można wdrożyć obraz, który obejmuje zarządzanie bazą danych jednostek SKU system (DBMS), na przykład Microsoft SQL Server. Aby uzyskać więcej informacji o używaniu obrazów z jednostkami SKU DBMS, zobacz [wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver][dbms-guide].

Poniższy schemat przedstawia SAP specyficzne sekwencji kroki wdrażania maszyny Wirtualnej w portalu Azure Marketplace:

![Schemat blokowy wdrożenia maszyny Wirtualnej dla systemów SAP za pomocą obrazu maszyny Wirtualnej w portalu Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Utwórz maszynę wirtualną przy użyciu witryny Azure portal

Najprostszym sposobem utworzenia nowej maszyny wirtualnej za pomocą obrazu z witryny Azure Marketplace jest przy użyciu witryny Azure portal.

1.  Przejdź do pozycji <https://portal.azure.com/#create/hub> (Plik > Nowy > Inny).  Lub, w menu portalu Azure wybierz **+ nowy**.
1.  Wybierz **obliczenia**, a następnie wybierz typ systemu operacyjnego, którą chcesz wdrożyć. Na przykład Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (w systemie SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) lub Oracle Linux 7.2. Domyślny widok listy nie są widoczne wszystkie obsługiwane systemy operacyjne. Wybierz **holograficznych** pełną listę. Aby uzyskać więcej informacji o obsługiwanych systemach operacyjnych w celu wdrażania oprogramowania SAP, patrz Uwaga SAP [1928533].
1.  Na następnej stronie Przejrzyj warunki i postanowienia.
1.  W **wybierz model wdrożenia** wybierz **usługi Resource Manager**.
1.  Wybierz pozycję **Utwórz**.

Kreator poprowadzi ustawiania parametrów wymaganych do utworzenia maszyny wirtualnej, oprócz wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i konta magazynu. Niektóre z tych parametrów są:

1. **Podstawy**:
   * **Nazwa**: Nazwa zasobu (Nazwa maszyny wirtualnej).
   * **Typ dysku maszyny wirtualnej**: Wybierz typ dysku, dysku systemu operacyjnego. Jeśli chcesz użyć usługi Premium Storage dla dysków z danymi, zalecamy użycie magazynu Premium Storage dla dysku systemu operacyjnego również.
   * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH**: Wprowadź nazwę użytkownika i hasło użytkownika, który jest tworzony podczas inicjowania obsługi. Dla maszyny wirtualnej systemu Linux możesz wprowadzić klucz publiczny protokołu Secure Shell (SSH), którego używasz do logowania się do maszyny.
   * **Subskrypcja**: Wybierz subskrypcję, której chcesz użyć do aprowizacji nowej maszyny wirtualnej.
   * **Grupa zasobów**: Nazwa grupy zasobów dla maszyny Wirtualnej. Można wprowadzić nazwę nowej grupy zasobów lub nazwę grupy zasobów, która już istnieje.
   * **Lokalizacja**: Gdzie można wdrożyć nową maszynę wirtualną. Jeśli chcesz połączyć maszyny wirtualnej z siecią lokalną, upewnij się, że wybierz lokalizację sieci wirtualnej, który nawiązuje połączenie platformy Azure z sieci lokalnej. Aby uzyskać więcej informacji, zobacz [sieci Microsoft Azure] [ planning-guide-microsoft-azure-networking] w [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver] [ planning-guide].
1. **Rozmiar**:

     Aby uzyskać listę obsługiwanych typów maszyn wirtualnych, patrz Uwaga SAP [1928533]. Pamiętaj, że wybrano poprawny typ maszyny Wirtualnej, jeśli chcesz używać usługi Azure Premium Storage. Nie wszystkie typy maszyn wirtualnych obsługują magazyn w warstwie Premium. Aby uzyskać więcej informacji, zobacz [magazynu: Usługa Microsoft Azure Storage i dysków z danymi] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] i [usługi Azure Premium Storage] [ planning-guide-azure-premium-storage] w [maszyn wirtualnych platformy Azure, planowania i implementacji środowiska SAP NetWeaver][planning-guide].

1. **Ustawienia**:
   * **Storage**
     * **Typ dysku**: Wybierz typ dysku, dysku systemu operacyjnego. Jeśli chcesz użyć usługi Premium Storage dla dysków z danymi, zalecamy użycie magazynu Premium Storage dla dysku systemu operacyjnego również.
     * **Użyj usługi managed disks**: Jeśli chcesz używać usługi Managed Disks, wybierz pozycję Tak. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz rozdział [Managed Disks] [ planning-guide-managed-disks] w przewodniku planowania.
     * **Konto magazynu**: Wybierz istniejące konto magazynu lub Utwórz nową. Nie wszystkie typy magazynu działa w przypadku uruchamiania aplikacji SAP. Aby uzyskać więcej informacji na temat typów magazynu, zobacz [struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Sieci**
     * **Sieć wirtualna** i **podsieci**: Aby zintegrować maszyny wirtualnej z sieci intranet, wybierz sieć wirtualną, która jest połączona z siecią lokalną.
     * **Publiczny adres IP**: Wybierz publiczny adres IP, którego chcesz używać, lub wprowadź parametry, aby utworzyć nowy publiczny adres IP. Publiczny adres IP umożliwia dostęp do maszyny wirtualnej za pośrednictwem Internetu. Upewnij się również utworzyć sieciowej grupy zabezpieczeń, aby zabezpieczać dostęp do maszyny wirtualnej.
     * **Sieciowa grupa zabezpieczeń**: Aby uzyskać więcej informacji, zobacz [sterowaniu przepływem ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń][virtual-networks-nsg].
   * **Rozszerzenia**: Można zainstalować rozszerzenia maszyny wirtualnej przez dodanie ich do wdrożenia. Nie trzeba dodać rozszerzenia, w tym kroku. Wymagane dla pomocy technicznej SAP są zainstalowane rozszerzenia, później. Zobacz rozdział [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP] [ deployment-guide-4.5] w tym przewodniku.
   * **Wysoka dostępność**: Wybierz zestaw dostępności lub wprowadź parametry, aby utworzyć nowy zestaw dostępności. Aby uzyskać więcej informacji, zobacz [zestawami dostępności platformy Azure][planning-guide-3.2.3].
   * **Monitorowanie**
     * **Diagnostyka rozruchu**: Możesz wybrać **wyłączyć** potrzeby diagnostyki rozruchu.
     * **Diagnostyka systemu operacyjnego gościa**: Możesz wybrać **wyłączyć** monitorowania diagnostyki.

1. **Podsumowanie**:

   Przejrzyj wybrane opcje, a następnie wybierz **OK**.

Maszyna wirtualna zostanie wdrożona w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Utwórz maszynę wirtualną przy użyciu szablonu

Można utworzyć maszynę wirtualną przy użyciu jednego z szablonów SAP, opublikowane w [repozytorium GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Możesz również ręcznie utworzyć maszynę wirtualną przy użyciu [witryny Azure portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], lub [wiersza polecenia platformy Azure] [virtual-machines-linux-tutorial].

* [**Szablon dwuwarstwowej konfiguracji (tylko jedna maszyna wirtualna)** (sap-2-warstwy — marketplace — obraz)][sap-templates-2-tier-marketplace-image]

  Aby utworzyć system dwuwarstwowej przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Szablon dwuwarstwowej konfiguracji (tylko jedna maszyna wirtualna) - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Aby utworzyć system dwuwarstwowej przy użyciu tylko jednej maszyny wirtualnej i dyski Managed Disks, użyj tego szablonu.
* [**Szablon trójwarstwowej konfiguracji (wiele maszyn wirtualnych)** (sap-3-warstwy — marketplace — obraz)][sap-templates-3-tier-marketplace-image]

  Aby utworzyć system trójwarstwową za pomocą wielu maszyn wirtualnych, użyj tego szablonu.
* [**Szablon trójwarstwowej konfiguracji (wiele maszyn wirtualnych) - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Aby utworzyć system trójwarstwową za pomocą wielu maszyn wirtualnych i dyski Managed Disks, użyj tego szablonu.

W witrynie Azure portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: Subskrypcję, który ma być używana do wdrożenia szablonu.
   * **Grupa zasobów**: Grupa zasobów, można użyć do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów, w ramach subskrypcji.
   * **Lokalizacja**: Gdzie można wdrożyć szablon. Jeśli wybrano istniejącą grupę zasobów, lokalizację grupy zasobów jest używany.

1. **Ustawienia**:
   * **Identyfikator systemu SAP**: Identyfikator systemu SAP (SID).
   * **Typ systemu operacyjnego**: System operacyjny, którą chcesz wdrożyć, na przykład Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (w systemie SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) lub Oracle Linux 7.2.

     Widok listy nie są widoczne wszystkie obsługiwane systemy operacyjne. Aby uzyskać więcej informacji o obsługiwanych systemach operacyjnych w celu wdrażania oprogramowania SAP, patrz Uwaga SAP [1928533].
   * **Rozmiar systemu SAP**: Rozmiar systemu SAP.

     Liczba protokoły SAP udostępnia nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
   * **Dostępność systemu** (tylko szablony trójwarstwowej): Dostępność systemu.

     Wybierz **HA** konfiguracji, które jest odpowiednie dla instalacji o wysokiej dostępności. Tworzone są dwa serwery baz danych i dwa serwery dla ABAP SAP Central Services (ASCS).
   * **Typ magazynu** (tylko szablony dwuwarstwowej): Typ magazynu do użycia.

     Dla większych systemów zdecydowanie zalecamy użycie magazynu Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu zawierają następujące zasoby:
      * [Korzystanie z usługi Azure Premium Storage SSD dla systemu SAP DBMS wystąpienia][2367194]
      * [Struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Nazwa użytkownika administratora** i **hasło administratora**: Nazwa użytkownika i hasło.
     Nowy użytkownik jest tworzony w przypadku logowania się do maszyny wirtualnej.
   * **Nowej lub istniejącej podsieci**: Określa, czy tworzenia nowej sieci wirtualnej i podsieci, czy istniejąca podsieć jest używana. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz opcję **istniejące**.
   * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda następująco: /subscriptions/&lt;identyfikator subskrypcji > /resourceGroups/&lt;nazwy grupy zasobów > /providers/Microsoft.Network/virtualNetworks/&lt;nazwa sieci wirtualnej > /subnets/&lt;Nazwa podsieci >

1. **Warunki i postanowienia**:  
    Przejrzyj i zaakceptuj postanowienia prawne.

1. Wybierz pozycję **Kup**.

Agent maszyny Wirtualnej platformy Azure jest wdrażany domyślnie, korzystając z obrazu z witryny Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy

W zależności od konfiguracji sieci w środowisku lokalnym może być konieczne ustawienie serwera proxy na maszynie Wirtualnej. Jeśli maszyna wirtualna jest podłączona do sieci lokalnej za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna może nie móc uzyskać dostęp do Internetu, a nie będzie mógł pobrać wymagane rozszerzenia lub zbierania danych monitorowania. Aby uzyskać więcej informacji, zobacz [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko Windows)

Jeśli wdrożenie systemu Azure jest połączona z wystąpienia usługi Active Directory i DNS w środowisku lokalnym za pośrednictwem platformy Azure połączenia sieci VPN lokacja lokacja lub ExpressRoute (jest to nazywane *między środowiskami lokalnymi* w [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna jest przyłączania do domeny w środowisku lokalnym. Aby uzyskać więcej informacji dotyczących tego zadania, zobacz [dołączyć Maszynę wirtualną do domeny lokalnej (tylko Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurowanie monitorowania

Mieć pewność, że oprogramowanie SAP obsługuje środowiska, aby skonfigurować rozszerzenie monitorowania platformy Azure dla rozwiązania SAP, zgodnie z opisem w [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-4.5]. Sprawdź wymagania wstępne dotyczące monitorowania SAP i wymagane minimalne wersje jądra SAP i SAP agenta hosta, w zasobach na liście [zasobów SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Kontrola monitorowania

Sprawdź, czy monitorowanie działa, zgodnie z opisem w [sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Czynności po wdrożeniu

Po utworzenia maszyny Wirtualnej i wdrożono maszynę Wirtualną, należy zainstalować składniki oprogramowania wymagane na maszynie wirtualnej. Ze względu na sekwencji instalacji wdrożenia i oprogramowania w tym typie wdrożenia maszyny Wirtualnej zainstalowanie oprogramowanie musi być dostępna, albo na platformie Azure, na innej maszynie Wirtualnej lub jako dysk, który można dołączyć. Alternatywnie należy rozważyć użycie scenariusz obejmujących wiele lokalizacji, w których łączność z lokalnym otrzymuje zasoby (Akcje instalacji).

Po wdrożeniu maszyny Wirtualnej na platformie Azure, wykonaj te same wskazówki i narzędzia do zainstalowania oprogramowania SAP na maszynie Wirtualnej, tak jak w środowisku lokalnym. Aby zainstalować oprogramowanie SAP na Maszynie wirtualnej platformy Azure, zarówno SAP i Microsoft zalecamy przekazywanie i przechowywanie nośnika instalacyjnego programu SAP na Azure wirtualnych dysków twardych lub dysków Managed Disks lub, możesz utworzyć Maszynę wirtualną platformy Azure, działa jako serwer plików, który ma wszystkie wymagane nośnika instalacyjnego SAP.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenariusz 2: Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu dla rozwiązania SAP

Ponieważ różne wersje systemu operacyjnego lub DBMS mają różne wymagania dotyczące, obrazy, które możesz znaleźć w witrynie Azure Marketplace nie spełnia Twoich potrzeb. Zamiast tego można utworzyć maszynę Wirtualną przy użyciu własnego obrazu maszyny Wirtualnej systemu operacyjnego/DBMS, którą można wdrożyć ponownie później.
Używasz różnych kroków do utworzenia obrazu prywatnego dla systemu Linux niż tworzenie jednego dla Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> Aby przygotować obraz Windows, który umożliwia wdrożenie wielu maszyn wirtualnych, ustawienia Windows (na przykład Windows SID lub nazwa hosta) być wyodrębnione lub uogólniony na maszynie Wirtualnej w środowisku lokalnym. Możesz użyć [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) w tym celu.
>
> ![Linux][Logo_Linux] Linux
>
> Aby przygotować obraz systemu Linux, który umożliwia wdrożenie wielu maszyn wirtualnych, niektóre ustawienia systemu Linux muszą wyodrębnione lub uogólniony na maszynie Wirtualnej w środowisku lokalnym. Możesz użyć `waagent -deprovision` w tym celu. Aby uzyskać więcej informacji, zobacz [Przechwytywanie maszyny wirtualnej systemu Linux na platformie Azure] [ virtual-machines-linux-capture-image] i [przewodnik użytkownika agenta platformy Azure w systemie Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Można przygotować i tworzenie niestandardowego obrazu, a następnie użyć go do utworzenia wielu nowych maszyn wirtualnych. Jest to opisane w [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide]. Konfigurowanie zawartości bazy danych za pomocą Menedżera inicjowania obsługi oprogramowania SAP do instalacji nowego systemu SAP (przywrócenie kopii zapasowej bazy danych z dysku, który jest dołączony do maszyny wirtualnej) lub bezpośrednio przywracanie kopii zapasowej bazy danych z usługi Azure storage, jeśli systemu DBMS obsługuje tę funkcję. Aby uzyskać więcej informacji, zobacz [wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver][dbms-guide]. Jeszcze zainstalowany z systemem SAP na lokalnej maszynie Wirtualnej (szczególnie w przypadku systemów dwuwarstwowej), można dostosować ustawienia systemu SAP po wdrożeniu maszyny Wirtualnej platformy Azure, wykonując procedurę podaną zmienić System, obsługiwane przez oprogramowanie SAP oprogramowania inicjowania obsługi administracyjnej Menedżera (SAP Należy pamiętać, [1619720]). W przeciwnym razie można zainstalować oprogramowanie SAP, po wdrożeniu maszyny Wirtualnej platformy Azure.

Poniższy schemat przedstawia SAP specyficzne sekwencji kroki wdrażania za pomocą niestandardowego obrazu maszyny Wirtualnej:

![Schemat blokowy wdrożenia maszyny Wirtualnej dla systemów SAP za pomocą obrazu maszyny Wirtualnej w witrynie Marketplace prywatne][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Utwórz maszynę wirtualną przy użyciu witryny Azure portal

Jest najprostszym sposobem utworzenia nowej maszyny wirtualnej z obrazu dysku zarządzanego przy użyciu witryny Azure portal. Aby uzyskać więcej informacji na temat tworzenia obrazu dysku Zarządzanie przeczytaj [przechwytywania obrazu zarządzanego uogólnionej maszyny Wirtualnej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Przejdź do pozycji <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> (Plik > Nowy > Inny). Lub, w menu portalu Azure wybierz **obrazów**.
1.  Wybieranie obrazu dysku zarządzanego, chcesz wdrożyć, a następnie kliknij przycisk na **Utwórz maszynę Wirtualną**

Kreator poprowadzi ustawiania parametrów wymaganych do utworzenia maszyny wirtualnej, oprócz wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i konta magazynu. Niektóre z tych parametrów są:

1. **Podstawy**:
   * **Nazwa**: Nazwa zasobu (Nazwa maszyny wirtualnej).
   * **Typ dysku maszyny wirtualnej**: Wybierz typ dysku, dysku systemu operacyjnego. Jeśli chcesz użyć usługi Premium Storage dla dysków z danymi, zalecamy użycie magazynu Premium Storage dla dysku systemu operacyjnego również.
   * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH**: Wprowadź nazwę użytkownika i hasło użytkownika, który jest tworzony podczas inicjowania obsługi. Dla maszyny wirtualnej systemu Linux możesz wprowadzić klucz publiczny protokołu Secure Shell (SSH), którego używasz do logowania się do maszyny.
   * **Subskrypcja**: Wybierz subskrypcję, której chcesz użyć do aprowizacji nowej maszyny wirtualnej.
   * **Grupa zasobów**: Nazwa grupy zasobów dla maszyny Wirtualnej. Można wprowadzić nazwę nowej grupy zasobów lub nazwę grupy zasobów, która już istnieje.
   * **Lokalizacja**: Gdzie można wdrożyć nową maszynę wirtualną. Jeśli chcesz połączyć maszyny wirtualnej z siecią lokalną, upewnij się, że wybierz lokalizację sieci wirtualnej, który nawiązuje połączenie platformy Azure z sieci lokalnej. Aby uzyskać więcej informacji, zobacz [sieci Microsoft Azure] [ planning-guide-microsoft-azure-networking] w [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver] [ planning-guide].
1. **Rozmiar**:

     Aby uzyskać listę obsługiwanych typów maszyn wirtualnych, patrz Uwaga SAP [1928533]. Pamiętaj, że wybrano poprawny typ maszyny Wirtualnej, jeśli chcesz używać usługi Azure Premium Storage. Nie wszystkie typy maszyn wirtualnych obsługują magazyn w warstwie Premium. Aby uzyskać więcej informacji, zobacz [magazynu: Usługa Microsoft Azure Storage i dysków z danymi] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] i [usługi Azure Premium Storage] [ planning-guide-azure-premium-storage] w [maszyn wirtualnych platformy Azure, planowania i implementacji środowiska SAP NetWeaver][planning-guide].

1. **Ustawienia**:
   * **Storage**
     * **Typ dysku**: Wybierz typ dysku, dysku systemu operacyjnego. Jeśli chcesz użyć usługi Premium Storage dla dysków z danymi, zalecamy użycie magazynu Premium Storage dla dysku systemu operacyjnego również.
     * **Użyj usługi managed disks**: Jeśli chcesz używać usługi Managed Disks, wybierz pozycję Tak. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz rozdział [Managed Disks] [ planning-guide-managed-disks] w przewodniku planowania.
   * **Sieci**
     * **Sieć wirtualna** i **podsieci**: Aby zintegrować maszyny wirtualnej z sieci intranet, wybierz sieć wirtualną, która jest połączona z siecią lokalną.
     * **Publiczny adres IP**: Wybierz publiczny adres IP, którego chcesz używać, lub wprowadź parametry, aby utworzyć nowy publiczny adres IP. Publiczny adres IP umożliwia dostęp do maszyny wirtualnej za pośrednictwem Internetu. Upewnij się również utworzyć sieciowej grupy zabezpieczeń, aby zabezpieczać dostęp do maszyny wirtualnej.
     * **Sieciowa grupa zabezpieczeń**: Aby uzyskać więcej informacji, zobacz [sterowaniu przepływem ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń][virtual-networks-nsg].
   * **Rozszerzenia**: Można zainstalować rozszerzenia maszyny wirtualnej przez dodanie ich do wdrożenia. Nie trzeba dodać rozszerzenie, w tym kroku. Wymagane dla pomocy technicznej SAP są zainstalowane rozszerzenia, później. Zobacz rozdział [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP] [ deployment-guide-4.5] w tym przewodniku.
   * **Wysoka dostępność**: Wybierz zestaw dostępności lub wprowadź parametry, aby utworzyć nowy zestaw dostępności. Aby uzyskać więcej informacji, zobacz [zestawami dostępności platformy Azure][planning-guide-3.2.3].
   * **Monitorowanie**
     * **Diagnostyka rozruchu**: Możesz wybrać **wyłączyć** potrzeby diagnostyki rozruchu.
     * **Diagnostyka systemu operacyjnego gościa**: Możesz wybrać **wyłączyć** monitorowania diagnostyki.

1. **Podsumowanie**:

   Przejrzyj wybrane opcje, a następnie wybierz **OK**.

Maszyna wirtualna zostanie wdrożona w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Utwórz maszynę wirtualną przy użyciu szablonu

Aby utworzyć wdrożenie przy użyciu prywatnego obrazu systemu operacyjnego w witrynie Azure portal, użyj jednej z poniższych szablonów SAP. Te szablony są publikowane w [repozytorium GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Możesz również ręcznie utworzyć maszynę wirtualną przy użyciu [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Szablon dwuwarstwowej konfiguracji (tylko jedna maszyna wirtualna)** (sap-2-warstwy — — obraz użytkownika)][sap-templates-2-tier-user-image]

  Aby utworzyć system dwuwarstwowej przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Szablon dwuwarstwowej konfiguracji (tylko jedna maszyna wirtualna) - obrazu dysku zarządzanego** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Aby utworzyć system dwuwarstwowej przy użyciu tylko jednej maszyny wirtualnej i obrazu dysku zarządzanego, użyj tego szablonu.
* [**Szablon trójwarstwowej konfiguracji (wiele maszyn wirtualnych)** (sap-3-warstwy — — obraz użytkownika)][sap-templates-3-tier-user-image]

  Aby utworzyć system trójwarstwową za pomocą wielu maszyn wirtualnych lub własnego obrazu systemu operacyjnego, użyj tego szablonu.
* [**Szablon trójwarstwowej konfiguracji (wiele maszyn wirtualnych) - obrazu dysku zarządzanego** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Aby utworzyć system trójwarstwową za pomocą wielu maszyn wirtualnych lub własnego obrazu systemu operacyjnego i obrazu dysku zarządzanego, użyj tego szablonu.

W witrynie Azure portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: Subskrypcję, który ma być używana do wdrożenia szablonu.
   * **Grupa zasobów**: Grupa zasobów, można użyć do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybierz istniejącą grupę zasobów w subskrypcji.
   * **Lokalizacja**: Gdzie można wdrożyć szablon. Jeśli wybrano istniejącą grupę zasobów, lokalizację grupy zasobów jest używany.
1. **Ustawienia**:
   * **Identyfikator systemu SAP**: Identyfikator systemu SAP
   * **Typ systemu operacyjnego**: Typ systemu operacyjnego, którą chcesz wdrożyć (Windows lub Linux).
   * **Rozmiar systemu SAP**: Rozmiar systemu SAP.

     Liczba protokoły SAP udostępnia nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
   * **Dostępność systemu** (tylko szablony trójwarstwowej): Dostępność systemu.

     Wybierz **HA** konfiguracji, które jest odpowiednie dla instalacji o wysokiej dostępności. Tworzone są dwa serwery baz danych i dwa serwery dla ASCS.
   * **Typ magazynu** (tylko szablony dwuwarstwowej): Typ magazynu do użycia.

     Dla większych systemów zdecydowanie zalecamy użycie magazynu Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu zobacz następujące zasoby:
      * [Korzystanie z usługi Azure Premium Storage SSD dla systemu SAP DBMS wystąpienia][2367194]
      * [Struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Obraz użytkownika identyfikatora URI dysku VHD** (tylko szablony obrazu dysku niezarządzanego): Identyfikator URI prywatnej systemu operacyjnego obrazu wirtualnego dysku twardego, na przykład https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
   * **Konto magazynu obrazu użytkownika** (tylko szablony obrazu dysku niezarządzanego): Nazwa konta magazynu, w którym przechowywany jest prywatny obrazu systemu operacyjnego, na przykład &lt;accountname > w https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (tylko szablony obrazu dysku zarządzanego): Identyfikator obrazu dysku zarządzanego, którego chcesz użyć
   * **Nazwa użytkownika administratora** i **hasło administratora**: Nazwa użytkownika i hasło.

     Nowy użytkownik jest tworzony w przypadku logowania się do maszyny wirtualnej.
   * **Nowej lub istniejącej podsieci**: Określa, czy utworzono nową sieć wirtualną i podsieć, czy istniejąca podsieć jest używana. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz opcję **istniejące**.
   * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda następująco: /subscriptions/&lt;identyfikator subskrypcji > /resourceGroups/&lt;nazwy grupy zasobów > /providers/Microsoft.Network/virtualNetworks/&lt;nazwa sieci wirtualnej > /subnets/&lt;Nazwa podsieci >

1. **Warunki i postanowienia**:  
    Przejrzyj i zaakceptuj postanowienia prawne.

1. Wybierz pozycję **Kup**.

#### <a name="install-the-vm-agent-linux-only"></a>Zainstaluj agenta maszyny Wirtualnej (tylko system Linux)

Aby użyć szablony, zgodnie z opisem w poprzedniej sekcji, agenta systemu Linux muszą być zainstalowane w obrazie użytkownika lub wdrożenie zakończy się niepowodzeniem. Pobierz i zainstaluj agenta maszyny Wirtualnej w obrazie użytkownika, zgodnie z opisem w [pobrać, zainstalować i włączyć agenta maszyny Wirtualnej platformy Azure][deployment-guide-4.4]. Jeśli nie korzystasz z szablonów, również można zainstalować agenta maszyny Wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko Windows)

Jeśli wdrożenie systemu Azure jest połączona z wystąpienia usługi Active Directory i DNS w środowisku lokalnym za pośrednictwem platformy Azure połączenia sieci VPN lokacja lokacja lub ExpressRoute systemu Azure (jest to nazywane *między środowiskami lokalnymi* w [maszyn wirtualnych platformy Azure Planowanie i wdrażanie środowiska SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna jest przyłączania do domeny w środowisku lokalnym. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego kroku, zobacz [dołączyć Maszynę wirtualną do domeny lokalnej (tylko Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy

W zależności od konfiguracji sieci w środowisku lokalnym może być konieczne ustawienie serwera proxy na maszynie Wirtualnej. Jeśli maszyna wirtualna jest podłączona do sieci lokalnej za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna może nie móc uzyskać dostęp do Internetu, a nie będzie mógł pobrać wymagane rozszerzenia lub zbierania danych monitorowania. Aby uzyskać więcej informacji, zobacz [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurowanie monitorowania

Mieć pewność, że oprogramowanie SAP obsługuje środowiska, aby skonfigurować rozszerzenie monitorowania platformy Azure dla rozwiązania SAP, zgodnie z opisem w [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-4.5]. Sprawdź wymagania wstępne dotyczące monitorowania SAP i wymagane minimalne wersje jądra SAP i SAP agenta hosta, w zasobach na liście [zasobów SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Kontrola monitorowania

Sprawdź, czy monitorowanie działa, zgodnie z opisem w [sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenariusz 3: Przenoszenie lokalnej maszyny Wirtualnej przy użyciu-uogólniony wirtualny dysk twardy Azure SAP

W tym scenariuszu ma zostać przeniesiona określonego systemu SAP ze środowiska lokalnego do platformy Azure. Można to zrobić, przekazując wirtualny dysk twardy ma system operacyjny, pliki binarne SAP i ostatecznie plików binarnych systemu DBMS, a także wirtualne dyski twarde, plikami danych i dziennika systemu DBMS na platformie Azure. W odróżnieniu od scenariusza opisanego w [Scenariusz 2: Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu dla rozwiązania SAP][deployment-guide-3.3], w tym przypadku Zachowaj nazwę hosta, SAP SID i SAP użytkownika konta w maszynie Wirtualnej platformy Azure, ponieważ zostały skonfigurowane w środowisku lokalnym. Nie trzeba do uogólnienia systemu operacyjnego. W tym scenariuszu najczęściej stosuje się do scenariuszy obejmujących wiele lokalizacji, gdzie część środowiskiem SAP działa lokalnie i części działa na platformie Azure.

W tym scenariuszu jest Agent maszyny Wirtualnej **nie** automatycznie zainstalowany podczas wdrażania. Ponieważ Agent maszyny Wirtualnej i rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP są wymagane do uruchamiania oprogramowania SAP NetWeaver na platformie Azure, należy pobrać, zainstalować i włączyć oba te składniki ręcznie, po utworzeniu maszyny wirtualnej.

Aby uzyskać więcej informacji na temat agenta maszyny Wirtualnej platformy Azure zobacz następujące zasoby.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Omówienie agenta maszyny wirtualnej na platformie Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Przewodnik użytkownika agenta platformy Azure][virtual-machines-linux-agent-user-guide]
>
>

- - -

Poniższy schemat przedstawia sekwencję czynności przenoszenia lokalnej maszyny Wirtualnej przy użyciu-uogólniony wirtualny dysk twardy platformy Azure:

![Schemat blokowy wdrożenia maszyny Wirtualnej dla systemów SAP za pomocą dysku maszyny Wirtualnej][deployment-guide-figure-400]

Jeśli dysk jest już przekazany i określone na platformie Azure (zobacz [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide]), wykonać zadania opisane w ciągu następnych kilka sekcji.

#### <a name="create-a-virtual-machine"></a>Utwórz maszynę wirtualną

Aby utworzyć wdrożenie, używając prywatnego dysku systemu operacyjnego za pośrednictwem witryny Azure portal, należy użyć szablonu SAP, opublikowane w [repozytorium GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Możesz również ręcznie utworzyć maszynę wirtualną przy użyciu programu PowerShell.

* [**Szablon dwuwarstwowej konfiguracji (tylko jedna maszyna wirtualna)** (sap-2-warstwy — użytkownik dysk)][sap-templates-2-tier-os-disk]

  Aby utworzyć system dwuwarstwowej przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Szablon dwuwarstwowej konfiguracji (tylko jedna maszyna wirtualna) - dysku zarządzanego** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Aby utworzyć system dwuwarstwowej przy użyciu tylko jednej maszyny wirtualnej i dysku zarządzanego, użyj tego szablonu.

W witrynie Azure portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: Subskrypcję, który ma być używana do wdrożenia szablonu.
   * **Grupa zasobów**: Grupa zasobów, można użyć do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybierz istniejącą grupę zasobów w subskrypcji.
   * **Lokalizacja**: Gdzie można wdrożyć szablon. Jeśli wybrano istniejącą grupę zasobów, lokalizację grupy zasobów jest używany.
1. **Ustawienia**:
   * **Identyfikator systemu SAP**: Identyfikator systemu SAP
   * **Typ systemu operacyjnego**: Typ systemu operacyjnego, którą chcesz wdrożyć (Windows lub Linux).
   * **Rozmiar systemu SAP**: Rozmiar systemu SAP.

     Liczba protokoły SAP udostępnia nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
   * **Typ magazynu** (tylko szablony dwuwarstwowej): Typ magazynu do użycia.

     Dla większych systemów zdecydowanie zalecamy użycie magazynu Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu zobacz następujące zasoby:
      * [Korzystanie z usługi Azure Premium Storage SSD dla systemu SAP DBMS wystąpienia][2367194]
      * [Struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Identyfikator URI wirtualnego dysku twardego dysku systemu operacyjnego** (tylko szablony dysku niezarządzanego): Identyfikator URI prywatnej dysku systemu operacyjnego, na przykład https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
   * **Identyfikator dysku zarządzanego dysku systemu operacyjnego** (tylko szablony dysków zarządzanych): Identyfikator dysku zarządzanego systemu operacyjnego z dysku /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Nowej lub istniejącej podsieci**: Określa, czy tworzenia nowej sieci wirtualnej i podsieci, czy istniejąca podsieć jest używana. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz opcję **istniejące**.
   * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda następująco: /subscriptions/&lt;identyfikator subskrypcji > /resourceGroups/&lt;nazwy grupy zasobów > /providers/Microsoft.Network/virtualNetworks/&lt;nazwa sieci wirtualnej > /subnets/&lt;Nazwa podsieci >

1. **Warunki i postanowienia**:  
    Przejrzyj i zaakceptuj postanowienia prawne.

1. Wybierz pozycję **Kup**.

#### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej

Aby użyć szablony, zgodnie z opisem w poprzedniej sekcji, musi być zainstalowany Agent maszyny Wirtualnej, na dysku systemu operacyjnego lub wdrożenie zakończy się niepowodzeniem. Pobierz i zainstaluj agenta maszyny Wirtualnej na maszynie wirtualnej, zgodnie z opisem w [pobrać, zainstalować i włączyć agenta maszyny Wirtualnej platformy Azure][deployment-guide-4.4].

Jeśli nie używasz szablony, zgodnie z opisem w poprzedniej sekcji, można także zainstalować agenta maszyny Wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko Windows)

Jeśli wdrożenie systemu Azure jest połączona z wystąpienia usługi Active Directory i DNS w środowisku lokalnym za pośrednictwem platformy Azure połączenia sieci VPN lokacja lokacja lub ExpressRoute (jest to nazywane *między środowiskami lokalnymi* w [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna jest przyłączania do domeny w środowisku lokalnym. Aby uzyskać więcej informacji dotyczących tego zadania, zobacz [dołączyć Maszynę wirtualną do domeny lokalnej (tylko Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy

W zależności od konfiguracji sieci w środowisku lokalnym może być konieczne ustawienie serwera proxy na maszynie Wirtualnej. Jeśli maszyna wirtualna jest podłączona do sieci lokalnej za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna może nie móc uzyskać dostęp do Internetu, a nie będzie mógł pobrać wymagane rozszerzenia lub zbierania danych monitorowania. Aby uzyskać więcej informacji, zobacz [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurowanie monitorowania

Mieć pewność, że oprogramowanie SAP obsługuje środowiska, aby skonfigurować rozszerzenie monitorowania platformy Azure dla rozwiązania SAP, zgodnie z opisem w [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-4.5]. Sprawdź wymagania wstępne dotyczące monitorowania SAP i wymagane minimalne wersje jądra SAP i SAP agenta hosta, w zasobach na liście [zasobów SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Kontrola monitorowania

Sprawdź, czy monitorowanie działa, zgodnie z opisem w [sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Aktualizowanie konfiguracji monitorowania dla rozwiązania SAP

Aktualizowanie konfiguracji monitorowania SAP w dowolnym z następujących scenariuszy:
* Wspólnego zespołu Microsoft/SAP rozszerza możliwości monitorowania i żąda liczniki więcej lub mniej.
* Firma Microsoft przedstawia nową wersję podstawową infrastrukturą platformy Azure, która dostarcza dane monitorowania i rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP wymaga dostosowania tych zmian.
* Instalowanie dodatkowego dysku z danymi do maszyny Wirtualnej platformy Azure lub Usuń dysk z danymi. W tym scenariuszu należy zaktualizować zbiór związane z magazynowaniem danych. Zmiana konfiguracji przez dodawanie lub usuwanie punktów końcowych lub przypisanie adresów IP do maszyny Wirtualnej nie wpływa na konfiguracji monitorowania.
* Możesz zmienić rozmiar maszyny wirtualnej platformy Azure, na przykład z rozmiar A5 do dowolnego rozmiaru maszyny Wirtualnej.
* Możesz dodać nowe interfejsy sieciowe z maszyną wirtualną platformy Azure.

Aktualizowanie ustawień monitorowania, należy zaktualizować, wykonując kroki opisane w monitorowania infrastruktury [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Szczegółowe zadania w celu wdrażania oprogramowania SAP

Ta sekcja zawiera szczegółowe kroki dotyczące wykonywania określonych zadań w procesie konfiguracji i wdrażania.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Wdrażanie polecenia cmdlet programu Azure PowerShell

1. Przejdź do [platformy Microsoft Azure pobiera](https://azure.microsoft.com/downloads/).
1. W obszarze **narzędzia wiersza polecenia**w obszarze **PowerShell**, wybierz opcję **zainstalować Windows**.
1. W oknie dialogowym Menedżer pobierania firmy Microsoft dla pobranego pliku (na przykład WindowsAzurePowershellGet.3f.3f.3fnew.exe), wybierz **Uruchom**.
1. Aby uruchomić Instalatora platformy sieci Web firmy Microsoft (Microsoft Web PI), zaznacz **tak**.
1. Strona, która wygląda na to:

   ![Strona instalacji dla poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Wybierz **zainstalować**, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
1. Środowisko PowerShell jest zainstalowane. Wybierz **Zakończ** aby zamknąć kreatora instalacji.

Często sprawdzaj aktualizacje poleceń cmdlet programu PowerShell, które zwykle są aktualizowane co miesiąc. Najprostszym sposobem, aby sprawdzał dostępność aktualizacji jest wykonanie poprzedniej instalacji, aż strona instalacji przedstawionym w kroku 5. Daty i wersji numer wersji poleceń cmdlet znajdują się na stronie przedstawionym w kroku 5. O ile nie wskazano inaczej w Uwaga SAP [1928533] lub Uwaga SAP [2015553], zaleca się, że pracujesz z najnowszą wersję poleceń cmdlet programu Azure PowerShell.

Aby sprawdzić wersję poleceń cmdlet programu Azure PowerShell, które są zainstalowane na komputerze, uruchom następujące polecenie programu PowerShell:
```powershell
(Get-Module Az.Compute).Version
```
Wynik wygląda następująco:

![Wynik kontroli wersji polecenia cmdlet programu Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Jeśli wersja polecenia cmdlet platformy Azure zainstalowany na tym komputerze jest bieżącą wersję, pierwszej stronie Kreatora instalacji wskazuje, że przez dodanie **(zainstalowane)** tytuł produktu (patrz poniższy zrzut ekranu). Poleceń cmdlet programu PowerShell Azure są aktualne. Aby zamknąć kreatora instalacji, wybierz **zakończenia**.

![Strona instalacji dla poleceń cmdlet programu Azure PowerShell, wskazujący, że zainstalowano najnowszą wersję poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Wdrażanie z wiersza polecenia platformy Azure

1. Przejdź do [platformy Microsoft Azure pobiera](https://azure.microsoft.com/downloads/).
1. W obszarze **narzędzia wiersza polecenia**w obszarze **interfejsu wiersza polecenia platformy Azure**, wybierz opcję **zainstalować** łącze w systemie operacyjnym.
1. W oknie dialogowym Menedżer pobierania firmy Microsoft dla pobranego pliku (na przykład WindowsAzureXPlatCLI.3f.3f.3fnew.exe), wybierz **Uruchom**.
1. Aby uruchomić Instalatora platformy sieci Web firmy Microsoft (Microsoft Web PI), zaznacz **tak**.
1. Strona, która wygląda na to:

   ![Strona instalacji dla poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Wybierz **zainstalować**, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
1. Wiersza polecenia platformy Azure jest zainstalowany. Wybierz **Zakończ** aby zamknąć kreatora instalacji.

Często sprawdzaj aktualizacje do wiersza polecenia platformy Azure, która zwykle jest aktualizowana co miesiąc. Najprostszym sposobem, aby sprawdzał dostępność aktualizacji jest wykonanie poprzedniej instalacji, aż strona instalacji przedstawionym w kroku 5.

Aby sprawdzić wersję interfejsu wiersza polecenia Azure, który jest zainstalowany na komputerze, uruchom następujące polecenie:
```
azure --version
```

Wynik wygląda następująco:

![Wynik kontroli wersji wiersza polecenia platformy Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Dołącz Maszynę wirtualną do domeny lokalnej (tylko Windows)

W przypadku wdrożenia SAP maszyn wirtualnych w scenariuszu obejmujących wiele lokalizacji, gdzie w lokalnej usłudze Active Directory i DNS zostały rozszerzone na platformie Azure, zakłada się, że maszyny wirtualne są przyłączania do domeny w środowisku lokalnym. Szczegółowy opis kroków, które możesz wykonać, aby dołączyć Maszynę wirtualną do domeny lokalnej i dodatkowego oprogramowania, trzeba należeć do domeny lokalnej jest zależna od klienta. Zwykle Aby dołączyć Maszynę wirtualną do domeny w środowisku lokalnym, należy zainstalować dodatkowe oprogramowanie, np. ochrony przed złośliwym oprogramowaniem i oprogramowanie kopii zapasowej lub monitorowania.

W tym scenariuszu, należy również upewnij się, że maszyna wirtualna zostanie przyłączony do domeny, w danym środowisku wymuszenie ustawień internetowego serwera proxy Windows konta systemu lokalnego (S-1-5-18) na maszynie Wirtualnej gościa ma tych samych ustawień serwera proxy. Opcja najłatwiejsza jest wymuszenie serwera proxy przy użyciu domeny zasad grupy, które mają zastosowanie do systemów w domenie.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Pobieranie, instalowanie i Włącz agenta maszyny Wirtualnej platformy Azure

Dla maszyn wirtualnych, które są wdrażane z obrazu systemu operacyjnego, który nie jest uogólniona (na przykład obraz, który nie pochodzą z narzędzia przygotowywania systemu Windows lub programu sysprep,) musisz ręcznie pobrać, zainstalować i włączyć agenta maszyny Wirtualnej platformy Azure.

W przypadku wdrożenia maszyny Wirtualnej w portalu Azure Marketplace, ten krok nie jest wymagane. Obrazy z witryny Azure Marketplace już agenta maszyny Wirtualnej platformy Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Pobierz agenta maszyny Wirtualnej platformy Azure:
   1.  Pobierz [pakiet Instalatora agenta maszyny Wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Lokalnie Store pakietu MSI agenta maszyny Wirtualnej na serwerze lub komputerze osobistym.
1. Zainstaluj agenta maszyny Wirtualnej platformy Azure:
   1.  Łączenie z wdrożonych maszyn wirtualnych platformy Azure za pomocą protokołu RDP (Remote Desktop).
   1.  Otwórz okno Eksploratora Windows na maszynie Wirtualnej, a następnie wybierz katalog docelowy dla pliku MSI agenta maszyny Wirtualnej.
   1.  Przeciągnij plik MSI Instalatora agenta maszyny Wirtualnej platformy Azure z lokalnego komputera/serwera do katalogu docelowego agenta maszyny Wirtualnej na maszynie Wirtualnej.
   1.  Kliknij dwukrotnie plik MSI na maszynie Wirtualnej.
1. Dla maszyn wirtualnych, które są przyłączone do domeny w środowisku lokalnym, upewnij się, że ostateczna ustawień internetowego serwera proxy dotyczą również Windows lokalnego konta systemowego (S-1-5-18) na maszynie Wirtualnej, zgodnie z opisem w [Skonfiguruj serwer proxy][deployment-guide-configure-proxy]. Agent maszyny Wirtualnej jest uruchamiany w tym kontekście i musi mieć możliwość połączenia z platformą Azure.

Interakcja użytkownika nie jest wymagany do zaktualizowania agenta maszyny Wirtualnej platformy Azure. Agent maszyny Wirtualnej jest automatycznie aktualizowana, a nie wymaga ponownego uruchomienia maszyny Wirtualnej.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Aby zainstalować agenta maszyny Wirtualnej dla systemu Linux, użyj następujących poleceń:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) lub Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Jeśli agent jest już zainstalowany, aby zaktualizować agenta systemu Linux dla platformy Azure, wykonaj czynności opisane w [zaktualizuj agenta systemu Linux dla platformy Azure na maszynie Wirtualnej do najnowszej wersji z witryny GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Skonfiguruj serwer proxy

Kroki, które należy wykonać, aby skonfigurować serwer proxy w Windows różnią się od sposobu konfigurowania serwera proxy w systemie Linux.

#### <a name="windows"></a>Windows

Ustawienia serwera proxy musi być prawidłowo skonfigurowany dla konta System lokalny, aby uzyskać dostęp do Internetu. Jeśli ustawienia serwera proxy nie są ustawione przez zasady grupy, można skonfigurować ustawienia dla lokalnego konta systemowego.

1. Przejdź do **Start**, wprowadź **gpedit.msc**, a następnie wybierz pozycję **Enter**.
1. Wybierz **konfiguracji komputera** > **Szablony administracyjne** > **składników Windows**  >   **Program Internet Explorer**. Upewnij się, że ustawienie **wprowadzić serwera proxy, ustawienia dla poszczególnych komputerów (a nie na użytkownika)** zostało wyłączone lub nieskonfigurowane.
1. W **Panelu sterowania**, przejdź do **Centrum sieci i udostępniania** > **Opcje internetowe**.
1. Na **połączeń** zaznacz **ustawienia sieci LAN** przycisku.
1. Wyczyść **Automatycznie wykryj ustawienia** pole wyboru.
1. Wybierz **Użyj serwera proxy dla sieci LAN** pole wyboru, a następnie wprowadź adres serwera proxy i port.
1. Wybierz **zaawansowane** przycisku.
1. W **wyjątki** wprowadź adres IP **168.63.129.16**. Kliknij przycisk **OK**.

#### <a name="linux"></a>Linux

Skonfiguruj prawidłowy serwer proxy w pliku konfiguracji agenta gościa platformy Azure firmy Microsoft, który znajduje się w folderze \\itp\\waagent.conf.

Ustaw następujące parametry:

1. **Hosta serwera proxy HTTP**. Na przykład ustaw ją na **proxy.corp.local**.
   ```
   HttpProxy.Host=<proxy host>

   ```
1. **Port serwera proxy HTTP**. Na przykład ustaw ją na **80**.
   ```
   HttpProxy.Port=<port of the proxy host>

   ```
1. Uruchom ponownie agenta.

   ```
   sudo service waagent restart
   ```

Ustawienia serwera proxy w \\itp\\waagent.conf mają zastosowanie również do wymaganego rozszerzenia maszyny Wirtualnej. Jeśli chcesz użyć repozytoriów platformy Azure, upewnij się, że ruch do tych repozytoriów nie będzie za pośrednictwem sieci intranet w środowisku lokalnym. Jeśli utworzone trasy zdefiniowane przez użytkownika, aby włączyć tunelowania wymuszonego, upewnij się, że dodano trasę która kieruje ruch do repozytoriów bezpośrednio do Internetu, a nie za pośrednictwem połączenia sieci VPN typu lokacja lokacja.

* **SLES**

  Należy również dodać trasy dla adresów IP wymienionych w \\itp\\regionserverclnt.cfg. Na poniższej ilustracji przedstawiono przykład:

  ![Wymuszone tunelowanie][deployment-guide-figure-50]


* **RHEL**

  Należy również dodać trasy dla adresów IP, hosty, na liście \\itp\\yum.repos.d\\rhui modułów równoważenia obciążenia. Aby uzyskać przykład zobacz poprzednim rysunku.

* **Oracle Linux**

  Nie ma żadnych repozytoriów, Oracle Linux na platformie Azure. Musisz skonfigurować własnych repozytoriów dla Oracle Linux lub użyć publicznych repozytoriów.

Aby uzyskać więcej informacji o trasach definiowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłaniu dalej IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Skonfiguruj rozszerzenie monitorowania rozszerzonej platformy Azure dla rozwiązania SAP

Po przygotowaniu maszyny Wirtualnej zgodnie z opisem w [scenariusze wdrażania maszyn wirtualnych dla rozwiązania SAP na platformie Azure][deployment-guide-3], Agent maszyny Wirtualnej platformy Azure jest zainstalowany na maszynie wirtualnej. Następnym krokiem jest, aby wdrożyć rozszerzenie platformy Azure Enhanced Monitoring dla oprogramowania SAP, który jest dostępny w repozytorium rozszerzeń platformy Azure w globalnych centrach danych platformy Azure. Aby uzyskać więcej informacji, zobacz [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver][planning-guide-9.1].

Aby zainstalować i skonfigurować rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP, można użyć programu PowerShell lub wiersza polecenia platformy Azure. Aby zainstalować rozszerzenia maszyny Wirtualnej systemu Linux lub Windows przy użyciu maszyny Windows, zobacz [programu Azure PowerShell][deployment-guide-4.5.1]. Aby zainstalować rozszerzenia na maszynie Wirtualnej z systemem Linux przy użyciu pulpitu systemu Linux, zobacz [wiersza polecenia platformy Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Program Azure PowerShell dla systemów Linux i Windows maszyn wirtualnych

Aby zainstalować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP za pomocą programu PowerShell:

1. Upewnij się, że zainstalowano najnowszą wersję programu Azure PowerShell polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [poleceń cmdlet wdrażania programu Azure PowerShell][deployment-guide-4.1].  
1. Uruchom następujące polecenie programu PowerShell.
    Aby uzyskać listę dostępnych środowisk, należy uruchomić `commandlet Get-AzEnvironment`. Czy chcesz korzystać z globalnej platformy Azure, Twoje środowisko jest **AzureCloud**. Dla platformy Azure w Chinach, wybierz **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po wprowadź dane konta usługi i zidentyfikować maszyny wirtualnej platformy Azure, skrypt wdraża wymaganego rozszerzenia i umożliwia wymaganych funkcji. Może to potrwać kilka minut.
Aby uzyskać więcej informacji na temat `Set-AzVMAEMExtension`, zobacz [AzVMAEMExtension zestaw][msdn-set-Azvmaemextension].

![Pomyślne wykonanie SAP specyficzne dla platformy Azure polecenia cmdlet Set-AzVMAEMExtension][deployment-guide-figure-900]

`Set-AzVMAEMExtension` Konfiguracji jest wszystkie kroki, aby skonfigurować hosta monitorowania dla rozwiązania SAP.

Dane wyjściowe skryptu zawierają następujące informacje:

* Potwierdzenie, że monitorowania dla dysku systemu operacyjnego i wszystkich dysków dodatkowych danych został skonfigurowany.
* Następne dwa komunikaty upewnij się, konfiguracja metryk usługi Storage dla konkretnym kontem magazynu.
* Jeden wiersz danych wyjściowych zawiera stan rzeczywista aktualizacja konfiguracji monitorowania.
* Kolejny wiersz danych wyjściowych potwierdza, że konfiguracji został wdrożony lub zaktualizowana.
* Ostatni wiersz danych wyjściowych ma charakter informacyjny. Pokazuje opcje testowania konfiguracji monitorowania.
* Aby sprawdzić, czy Azure Enhanced Monitoring wszystkie kroki zostały wykonane pomyślnie i czy infrastruktury systemu Azure dostarcza niezbędnych danych, wykonaj sprawdzanie gotowości Azure Enhanced Monitoring rozszerzenia dla oprogramowania SAP, zgodnie z opisem w [Sprawdzanie gotowości dla platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-5.1].
* Poczekaj 15 – 30 minut dla usługi Azure Diagnostics zgromadzić odpowiednią ilość danych.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux

Aby zainstalować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP za pomocą wiersza polecenia platformy Azure:

   1. Zainstaluj klasycznego wiersza polecenia platformy Azure, zgodnie z opisem w [zainstalować Azure klasyczny interfejs wiersza polecenia][azure-cli].
   1. Zaloguj się przy użyciu konta platformy Azure:

      ```
      azure login
      ```

   1. Przełącz do trybu usługi Azure Resource Manager:

      ```
      azure config mode arm
      ```

   1. Włącz Azure Enhanced Monitoring:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalowanie przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0

   1. Zainstaluj Azure CLI 2.0, zgodnie z opisem w [instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0][azure-cli-2].
   1. Zaloguj się przy użyciu konta platformy Azure:

      ```
      az login
      ```

   1. Zainstaluj rozszerzenie Azure AEM interfejsu wiersza polecenia
  
      ```
      az extension add --name aem
      ```
  
   1. Zainstaluj rozszerzenie za pomocą
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Sprawdź, czy rozszerzenie platformy Azure Enhanced Monitoring jest aktywny na maszynie Wirtualnej z systemem Linux platformy Azure. Sprawdź, czy plik \\var\\lib\\AzureEnhancedMonitor\\PerfCounters istnieje. Jeśli istnieje, w wierszu polecenia, uruchom następujące polecenie, aby wyświetlić informacje zebrane w ramach usługi Azure Monitor rozszerzonych:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Dane wyjściowe wyglądają następująco:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Sprawdzanie i rozwiązywanie problemów dotyczących monitorowania end-to-end

Po wdrożeniu maszyny Wirtualnej platformy Azure i skonfiguruj odpowiednie monitorowania infrastruktury platformy Azure, sprawdź, czy wszystkie składniki rozszerzenie platformy Azure Enhanced Monitoring działają zgodnie z oczekiwaniami.

Uruchom sprawdzanie gotowości Azure Enhanced Monitoring rozszerzenia dla rozwiązania SAP, zgodnie z opisem w [sprawdzanie gotowości Azure Enhanced Monitoring rozszerzenia dla rozwiązania SAP][deployment-guide-5.1]. Jeśli wszystkie wyniki sprawdzania gotowości są pozytywne, wszystkie liczniki wydajności odpowiednie pojawiają się OK monitorowania platformy Azure ma została pomyślnie skonfigurowana. Możesz kontynuować instalację agenta hosta SAP zgodnie z opisem w sekcji SAP Notes w [zasobów SAP][deployment-guide-2.2]. Jeśli sprawdzanie gotowości wskazuje, że liczniki są nieobecne, uruchom sprawdzanie kondycji infrastruktury monitorowania platformy Azure, zgodnie z opisem w [sprawdzenie kondycji dla konfiguracji monitorowania infrastruktury platformy Azure] [ deployment-guide-5.2]. Aby uzyskać więcej opcji rozwiązywania problemów, zobacz [Rozwiązywanie problemów z monitorowania platformy Azure dla rozwiązania SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Sprawdzanie gotowości Azure Enhanced Monitoring rozszerzenia dla rozwiązania SAP

Ten test sprawdza, czy wszystkich metryk wydajności, które są wyświetlane w aplikacji SAP są dostarczane przez podstawową infrastrukturą monitorowania platformy Azure.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Uruchom sprawdzenie gotowości maszyny Wirtualnej z systemem Windows

1. Zaloguj się do maszyny wirtualnej platformy Azure (przy użyciu konta administratora nie jest to konieczne).
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia Zmień katalog na folder instalacji Azure Enhanced Monitoring rozszerzenia dla rozwiązania SAP: C:\\pakietów\\wtyczek\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;wersji >\\drop

   *Wersji* w ścieżce do rozszerzonego monitorowania może się różnić. Jeśli widzisz folderów dla różnych wersji rozszerzonego monitorowania w folderze instalacyjnym, sprawdź konfigurację usługi Windows AzureEnhancedMonitoring, a następnie przejść do folderu, który został wskazany jako *ścieżka do pliku wykonywalnego* .

   ![Właściwości usługi Azure Enhanced Monitoring rozszerzenie dla rozwiązania SAP][deployment-guide-figure-1000]

1. W wierszu polecenia Uruchom **azperflib.exe** bez żadnych parametrów.

   > [!NOTE]
   > Azperflib.exe jest uruchamiane w pętli i aktualizuje zebrane liczniki co 60 sekund. Aby zakończyć pętli, zamknij okno wiersza polecenia.
   >
   >

Jeśli nie zainstalowano rozszerzenie platformy Azure Enhanced Monitoring lub usługa AzureEnhancedMonitoring nie jest uruchomiona, rozszerzenie nie został skonfigurowany poprawnie. Aby uzyskać szczegółowe informacje o sposobie wdrażania rozszerzenia, zobacz [Rozwiązywanie problemów z infrastruktury monitorowania platformy Azure dla rozwiązania SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe jest składnikiem, którego nie można użyć do własnych celów. Jest składnikiem, który dostarcza dane związane z maszyny Wirtualnej agenta hosta SAP do monitorowania platformy Azure.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Sprawdź dane wyjściowe azperflib.exe

Dane wyjściowe Azperflib.exe pokazują, że wszystkie zostały wypełnione liczniki wydajności platformy Azure dla rozwiązania SAP. W dolnej części listy liczników zbieranych wskaźnik Podsumowanie i kondycji Pokaż stan monitorowania platformy Azure.

![Sprawdzenie kondycji, wykonując azperflib.exe, co oznacza, że nie istnieją problemy z danych wyjściowych][deployment-guide-figure-1100]
<a name="figure-11"></a>

Sprawdź wynik zwracany dla **łącznie liczniki** danych wyjściowych, który jest zgłaszany jako puste, a dla **stan kondycji**, jak pokazano na poprzednim rysunku.

Interpretuj wyniki w następujący sposób:

| Wartości wynikowe Azperflib.exe | Stan kondycji monitorowania platformy Azure |
| --- | --- |
| **Wywołania interfejsu API — nie jest dostępna** | Liczniki, które nie są dostępne, może być albo nie ma zastosowania do konfiguracji maszyny wirtualnej lub błędów. Zobacz **stan kondycji**. |
| **Łącznie liczniki — pusty** |Następujące dwa liczniki usługi Azure storage może być pusta: <ul><li>Opóźnienie operacji odczytu z magazynu serwera (MS)</li><li>Opóźnienie operacji odczytu z magazynu E2E (MS)</li></ul>Wszystkie inne liczniki muszą mieć wartości. |
| **Stan kondycji** |Jeśli tylko OK zwracają pokazuje stan **OK**. |
| **Diagnostyka** |Szczegółowe informacje o stanie kondycji. |

Jeśli **stan kondycji** wartość nie jest **OK**, postępuj zgodnie z instrukcjami w [sprawdzenie kondycji dla konfiguracji monitorowania infrastruktury platformy Azure] [ deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Uruchom sprawdzanie gotowości na maszynie Wirtualnej systemu Linux

1. Łączenie maszyny wirtualnej platformy Azure przy użyciu protokołu SSH.

1. Sprawdź dane wyjściowe rozszerzenie platformy Azure Enhanced Monitoring.

   a.  Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Oczekiwany wynik**: Zwraca listę liczników wydajności. Plik nie powinien być pusty.

   b. Uruchom polecenie `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Oczekiwany wynik**: Zwraca jeden wiersz, gdzie błąd jest **Brak**, na przykład **3; konfiguracji; Błąd; 0; 0; Brak 0; 1456416792; tst servercs;**

   c. Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Oczekiwany wynik**: Zwraca jako pusta lub nie istnieje.

Jeśli poprzedni wyboru zakończyła się niepowodzeniem, należy uruchomić następujące dodatkowe kontrole:

1. Upewnij się, że waagent jest zainstalowane i włączone.

   a.  Uruchom polecenie `sudo ls -al /var/lib/waagent/`

     **Oczekiwany wynik**: Wyświetla zawartość katalogu waagent.

   b.  Uruchom polecenie `ps -ax | grep waagent`

   **Oczekiwany wynik**: Wyświetla jeden wpis podobny do: `python /usr/sbin/waagent -daemon`

1. Upewnij się, że rozszerzenie platformy Azure Enhanced Monitoring jest zainstalowana i uruchomiona.

   a.  Uruchom polecenie `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Oczekiwany wynik**: Wyświetla zawartość katalogu rozszerzenie rozszerzone monitorowanie platformy Azure.

   b. Uruchom polecenie `ps -ax | grep AzureEnhanced`

   **Oczekiwany wynik**: Wyświetla jeden wpis podobny do: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Zainstaluj agenta hosta SAP, zgodnie z opisem w Uwaga SAP [1031096]i sprawdzić wynik działania `saposcol`.

   a.  Uruchom polecenie `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Uruchom polecenie `dump ccm`

   c.  Sprawdź, czy **Virtualization_Configuration\Enhanced monitorowania dostępu** Metryka to **true**.

Jeśli masz już zainstalowany serwer aplikacji SAP NetWeaver ABAP, otwórz transakcji ST06 i sprawdź, czy jest włączone monitorowanie rozszerzonej.

Jeśli dowolny kontroli zakończyć się niepowodzeniem, a aby uzyskać szczegółowe informacje o tym, jak można wdrożyć ponownie rozszerzenie, zobacz [Rozwiązywanie problemów z infrastruktury monitorowania platformy Azure dla rozwiązania SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Sprawdzanie kondycji dla konfiguracji monitorowania infrastruktury platformy Azure

Jeśli niektóre monitorowania dane nie są poprawnie, wskazane przez test opisany w dostarczane [sprawdzanie gotowości dla platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-5.1]Uruchom `Test-AzVMAEMExtension` polecenia cmdlet, aby sprawdzić, czy Do monitorowania infrastruktury i rozszerzonego monitorowania dla rozwiązania SAP platformy Azure są poprawnie skonfigurowane.

1. Upewnij się, że zainstalowano najnowszą wersję programu Azure PowerShell polecenia cmdlet, zgodnie z opisem w [poleceń cmdlet wdrażania programu Azure PowerShell][deployment-guide-4.1].
1. Uruchom następujące polecenie programu PowerShell. Aby uzyskać listę dostępnych środowisk, uruchom polecenie cmdlet `Get-AzEnvironment`. Aby korzystać z globalnej platformy Azure, wybierz **AzureCloud** środowiska. Dla platformy Azure w Chinach, wybierz **AzureChinaCloud**.
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Wprowadź dane konta usługi i zidentyfikować maszyny wirtualnej platformy Azure.

   ![Wejściowy stronie SAP specyficzne dla platformy Azure polecenia cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Skrypt sprawdza konfigurację maszyny wirtualnej, którą wybierzesz.

   ![Dane wyjściowe pomyślnego testowego monitorowania infrastruktury platformy Azure dla rozwiązania SAP][deployment-guide-figure-1300]

Upewnij się, że każdy wynik sprawdzania kondycji jest **OK**. Jeśli niektóre testy nie są wyświetlane **OK**, uruchom polecenie cmdlet update zgodnie z opisem w [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-4.5]. Poczekaj 15 minut, a następnie powtórz sprawdzania opisanych w [sprawdzanie gotowości dla platformy Azure Enhanced Monitoring dla rozwiązania SAP] [ deployment-guide-5.1] i [sprawdzenie kondycji dla konfiguracji infrastruktury monitorowania platformy Azure] [deployment-guide-5.2]. Jeśli testy nadal wskazywać na problem z niektórych lub wszystkich liczników, zobacz [Rozwiązywanie problemów z infrastruktury monitorowania platformy Azure dla rozwiązania SAP][deployment-guide-5.3].

> [!Note]
> Mogą występować pewne ostrzeżenia w przypadkach, w których są używane zarządzane dyski w warstwie standardowa usługi Azure. Ostrzeżenia będzie wyświetlany zamiast testy, zwracając "OK". Jest to normalne i planowanym w przypadku tego typu dysku. Zobacz również [Rozwiązywanie problemów z infrastruktury monitorowania platformy Azure dla rozwiązania SAP][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Rozwiązywanie problemów z infrastruktury monitorowania platformy Azure dla rozwiązania SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Liczniki wydajności platformy Azure nie są wyświetlane w każdym

Usługa Windows AzureEnhancedMonitoring zbiera metryki wydajności na platformie Azure. Jeśli usługa nie została poprawnie zainstalowana lub nie jest uruchomiona na maszynie wirtualnej, mogą być zbierane nie metryki wydajności.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Katalog instalacji rozszerzenia platformy Azure Enhanced Monitoring jest pusty

###### <a name="issue"></a>Problem

Katalog instalacyjny C:\\pakietów\\wtyczek\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;wersji >\\docelowej jest puste.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowany. Ustal, czy jest to problem z serwerem proxy, (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie maszyny lub ponownie uruchomić `Set-AzVMAEMExtension` skrypt konfiguracji.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Usługi dla usługi Azure Enhanced Monitoring nie istnieje.

###### <a name="issue"></a>Problem

Usługa Windows AzureEnhancedMonitoring nie istnieje.

Dane wyjściowe Azperflib.exe zgłasza błąd:

![Wykonanie azperflib.exe oznacza, że usługę Azure Enhanced Monitoring rozszerzenia dla oprogramowania SAP nie jest uruchomiony][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Rozwiązanie

Jeśli usługa nie istnieje, rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP nie została poprawnie zainstalowana. Ponowne wdrażanie rozszerzenia za pomocą procedury opisanej dla danego scenariusza wdrażania w [scenariusze wdrażania maszyn wirtualnych dla rozwiązania SAP na platformie Azure][deployment-guide-3].

Po wdrożeniu rozszerzenie, po upływie godziny, ponownie sprawdzić czy liczniki wydajności platformy Azure znajdują się w maszynie Wirtualnej platformy Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Usługi dla usługi Azure Enhanced Monitoring istnieje, ale nie można uruchomić

###### <a name="issue"></a>Problem

Usługa Windows AzureEnhancedMonitoring istnieje i jest włączona, ale nie została uruchomiona. Aby uzyskać więcej informacji Sprawdź dziennik zdarzeń aplikacji.

###### <a name="solution"></a>Rozwiązanie

Konfiguracja jest nieprawidłowa. Uruchom ponownie rozszerzonego monitorowania dla maszyny Wirtualnej, zgodnie z opisem w [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Brak niektórych liczników wydajności platformy Azure

Usługa Windows AzureEnhancedMonitoring zbiera metryki wydajności na platformie Azure. Usługa pobiera dane z wielu źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z usługi Azure Diagnostics. Liczniki magazynu są używane z Twojej rejestracji na poziomie subskrypcji magazynu.

Jeśli Rozwiązywanie problemów za pomocą Uwaga SAP [1999351] nie rozwiązać ten problem, uruchom ponownie `Set-AzVMAEMExtension` skrypt konfiguracji. Trzeba będzie trzeba odczekać godzinę, ponieważ liczniki analiz i diagnostyki magazynu nie może utworzyć natychmiast, po ich włączeniu. Jeśli problem będzie się powtarzać, Otwórz wiadomość pomocy technicznej klienta SAP, BC-OP-NT-AZR składnika dla Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej systemu Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Liczniki wydajności platformy Azure nie są wyświetlane w każdym

Metryki wydajności na platformie Azure są zbierane przez demon. Jeśli demon nie jest uruchomiona, mogą być zbierane nie metryki wydajności.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Katalog instalacji rozszerzenia Azure Enhanced Monitoring jest pusty

###### <a name="issue"></a>Problem

Katalog \\var\\lib\\waagent\\ nie ma podkatalogu dla rozszerzenia Azure Enhanced Monitoring.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowany. Ustal, czy jest to problem z serwerem proxy, (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie maszyny i/lub uruchom ponownie `Set-AzVMAEMExtension` skrypt konfiguracji.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Wykonywanie zestawu AzVMAEMExtension i AzVMAEMExtension testu Pokaż komunikaty ostrzegawcze z informacją, że dyski zarządzane w warstwie standardowa nie są obsługiwane

###### <a name="issue"></a>Problem

Podczas wykonywania zestawu AzVMAEMExtension lub AzVMAEMExtension testu wiadomości, takie jak te są wyświetlane:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Wykonywanie azperfli.exe zgodnie z wcześniejszym opisem można uzyskać wynik, który wskazuje-dobrej kondycji. 

###### <a name="solution"></a>Rozwiązanie

Komunikaty są spowodowane przez fakt, że dyski zarządzane w warstwie standardowa nie zapewniają interfejsy API używane przez rozszerzenie monitorowania do zapoznania się na statystyki standardowe konta magazynu platformy Azure. Nie jest przedmiotem obaw. Przyczyna wprowadzenie do monitorowania dla kont usługi Standard Disk Storage została ograniczania operacji We/Wy, które wystąpiły często. Dyski zarządzane pozwoli uniknąć takich ograniczania przepustowości poprzez ograniczenie liczby dysków na koncie magazynu. W związku z tym nie ma tego rodzaju danych monitorowania nie jest krytyczny.


#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Brak niektórych liczników wydajności platformy Azure

Metryki wydajności na platformie Azure są zbierane przez demona, który pobiera dane z wielu źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z usługi Azure Diagnostics. Liczniki pamięci masowej pochodzą z dzienników w ramach subskrypcji magazynu.

Aby uzyskać pełną i aktualną listę znanych problemów, zobacz Uwaga SAP [1999351], która zawiera dodatkowe informacje dotyczące rozwiązywania problemów dla rozszerzonego monitorowania platformy Azure dla rozwiązania SAP.

Jeśli Rozwiązywanie problemów za pomocą Uwaga SAP [1999351] nie rozwiązać ten problem, uruchom ponownie `Set-AzVMAEMExtension` skryptu konfiguracji zgodnie z opisem w [skonfigurować rozszerzenie platformy Azure Enhanced Monitoring dla rozwiązania SAP] [deployment-guide-4.5]. Trzeba czekać na godzinę, ponieważ liczniki analiz i diagnostyki magazynu nie może utworzyć natychmiast, po ich włączeniu. Jeśli problem będzie się powtarzać, Otwórz wiadomość pomocy technicznej klienta SAP, BC-OP-NT-AZR składnika dla Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej systemu Linux.
