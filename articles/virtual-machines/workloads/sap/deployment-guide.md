---
title: Maszyny wirtualne Azure wdrożenia SAP NetWeaver | Dokumentacja firmy Microsoft
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
ms.date: 11/08/2016
ms.author: sedusch
ms.openlocfilehash: cb402f51ca20551bf34140322f3f259470a637ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656401"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Maszyny wirtualne Azure wdrożenia SAP NetWeaver
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
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Wdrożenia usługi Azure DBMS maszyny wirtualnej dla programu SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Buforowanie maszyny wirtualne i wirtualne dyski twarde)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Oprogramowaniem RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Magazyn Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura RDBMS wdrożenia)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Wysoka dostępność i odzyskiwanie po awarii z maszynami wirtualnymi Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 lub nowszy)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 z dodatkiem SP1 CU3 i wcześniejszych wersjach)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Przy użyciu obrazu programu SQL Server w witrynie Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Ogólne programu SQL Server dla programu SAP w podsumowaniu Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Szczegóły programu SQL Server RDBMS)
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

[deployment-guide]:deployment-guide.md (Maszyny wirtualne Azure wdrożenia SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Zasoby SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Wdrażanie maszyny Wirtualnej przy użyciu niestandardowego obrazu)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenariusz 1: Wdrażanie maszyny Wirtualnej z poziomu portalu Azure Marketplace dla SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazu niestandardowego dla SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenariusz 3: Przenoszenie maszyny Wirtualnej z lokalnymi przy użyciu-uogólniony wirtualny dysk twardy Azure z SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenariusze wdrażania maszyn wirtualnych dla programu SAP w systemie Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Wdrażanie poleceń cmdlet programu Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Pobieranie i importowanie poleceń cmdlet programu PowerShell odpowiednich SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Dołącz Maszynę wirtualną do domeny lokalnej — tylko w systemie Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Pobrać, zainstalować i włączyć agenta maszyny Wirtualnej Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interfejs wiersza polecenia platformy Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Skonfiguruj rozszerzenie Azure monitorowania rozszerzonego dla programu SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Sprawdzanie gotowości do rozszerzonego monitorowania Azure dla programu SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Sprawdzanie kondycji Azure monitorowania infrastruktury)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Rozwiązywanie problemów z Azure monitorowania dla programu SAP)

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

[planning-guide]:planning-guide.md (Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Zasoby)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Wysoka dostępność i odzyskiwanie po awarii dla programu SAP NetWeaver uruchomione na maszynach wirtualnych platformy Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Wysoka dostępność dla serwerów aplikacji SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Przy użyciu Autostart dla wystąpień SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Tylko w chmurze — wdrożenia maszyny wirtualnej na platformie Azure bez zależności w sieci lokalnej klienta)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Między różnymi lokalizacjami - wdrażania jednego lub wielu SAP maszyn wirtualnych na platformie Azure w pełni zintegrowana z sieci lokalnej)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiony platformy Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domen błędów)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domen uaktualnienia)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Zestawy dostępności Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncepcja maszyny wirtualne Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Magazyn w warstwie Premium systemu Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Przenoszenie maszyny Wirtualnej z lokalnej na platformie Azure przy użyciu dysku z systemem innym niż uogólniony)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Wdrażanie maszyny Wirtualnej z określonego obrazu klienta)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Przygotowanie do przenoszenia maszyny Wirtualnej z lokalnej na platformie Azure przy użyciu dysku z systemem innym niż uogólniony)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Przygotowanie do wdrożenia maszyny Wirtualnej z określonego obrazu klienta dla programu SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Przygotowywanie maszyn wirtualnych z programu SAP do platformy Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Różnica między dysku platformy Azure i obrazu platformy Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Przekazywanie wirtualnego dysku twardego z lokalnej na platformie Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopiowanie dysków między kontami magazynu Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura maszyny Wirtualnej/wirtualnego dysku twardego dla wdrożenia SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Ustawienie automatycznej instalacji dołączonych dysków)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Pojedyncze maszyny z programem SAP NetWeaver pokaz/szkolenia scenariusza)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Pojęcia dotyczące tylko w chmurze wdrożenia SAP wystąpień)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitorowania rozwiązania dla programu SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Magazyn w warstwie Premium systemu Azure)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Dyski zarządzane)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Sieci Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Dyski magazynu i danych Microsoft Azure)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktu SAP)
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Wdrażanie i zarządzanie maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i wiersza polecenia platformy Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Zarządzania maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell)
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

Maszyny wirtualne platformy Azure to rozwiązanie dla organizacji, które muszą zasobów obliczeniowych i magazynu, w czasie minimalnego i bez cykle nabywania długie. Maszyny wirtualne Azure umożliwia wdrażanie klasycznego aplikacji, takie jak SAP NetWeaver aplikacji, w usłudze Azure. Rozszerzanie niezawodności i dostępności bez dodatkowych lokalnych zasobów aplikacji. Maszyny wirtualne platformy Azure obsługuje łączności między lokalizacjami, maszynach wirtualnych platformy Azure można zintegrować lokalnej domeny Twojej organizacji, chmur prywatnych i pozioma systemu SAP.

W tym artykule firma Microsoft opisano kroki, aby wdrożyć aplikacje SAP na maszynach wirtualnych (VM) na platformie Azure, włącznie z opcjami wdrożenia alternatywny i rozwiązywania problemów. W tym artykule opiera się na informacje zawarte w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide]. Uzupełnia on również SAP instalacji dokumentacji i notatki SAP głównej zasoby umożliwiające instalowanie i wdrażanie oprogramowania SAP.

## <a name="prerequisites"></a>Wymagania wstępne
Konfigurowanie maszyny wirtualnej platformy Azure dla wdrożenia oprogramowania SAP obejmuje wiele kroków i zasobów. Przed rozpoczęciem upewnij się, że spełniają wymagania wstępne dotyczące instalowania oprogramowania SAP na maszynach wirtualnych platformy Azure.

### <a name="local-computer"></a>Komputer lokalny
Aby zarządzać systemem Windows lub maszyn wirtualnych systemu Linux, można użyć skryptu programu PowerShell i portalu Azure. Dla obu narzędzia należy komputera lokalnego z systemem Windows 7 lub nowszej wersji systemu Windows. Jeśli chcesz zarządzać tylko maszyn wirtualnych systemu Linux i chcesz użyć komputera z systemem Linux dla tego zadania, można użyć wiersza polecenia platformy Azure.

### <a name="internet-connection"></a>Połączenie internetowe
Do pobierania i uruchamiania narzędzia i skrypty, które są wymagane do wdrożenia oprogramowania SAP, komputer połączony z Internetem. Maszyna wirtualna Azure z systemem Azure rozszerzone monitorowanie rozszerzenia dla programu SAP musi też mieć dostęp do Internetu. Jeśli maszyny Wirtualnej Azure jest częścią sieci wirtualnej platformy Azure lub lokalnymi domeny, upewnij się, że właściwych ustawień serwera proxy są ustawione, zgodnie z opisem w [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subskrypcja platformy Microsoft Azure
Potrzebne aktywne konto platformy Azure.

### <a name="topology-and-networking"></a>Topologia i sieci
Musisz zdefiniować topologii i architektura wdrożenia SAP na platformie Azure:

* Konta magazynu Azure do użycia
* Sieć wirtualną, której chcesz wdrożyć systemu SAP
* Grupy zasobów, do której chcesz wdrożyć systemu SAP
* Region platformy Azure, w której chcesz wdrożyć systemu SAP
* Konfiguracja programu SAP (dwuwarstwowa i trójwarstwowa)
* Rozmiary maszyn wirtualnych i liczby dysków dodatkowych danych ma zostać zainstalowany z maszynami wirtualnymi
* Konfiguracja korekty SAP i System transportu (CTS)

Tworzenie i konfigurowanie kont magazynu Azure (jeśli jest to wymagane) lub sieci wirtualnych platformy Azure, przed rozpoczęciem procesu wdrażania oprogramowania SAP. Aby uzyskać informacje o sposobie tworzenia i konfigurowania tych zasobów, zobacz [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Zmiana rozmiaru SAP
Należy sprawdzić następujące informacje, do zmiany rozmiaru SAP:

* Planowane obciążenie SAP, na przykład za pomocą narzędzia SAP szybkie Rozmiar symboli i SAP aplikacji wydajności standardowe (punktu SAP) numer
* Wymagany zasób i pamięci użycie Procesora systemu SAP
* Wymagane operacje wejścia/wyjścia (We/Wy), na sekundę
* Wymagana przepustowość sieci wynosi ostatecznego komunikacji między maszynami wirtualnymi na platformie Azure
* Przepustowość sieci wymagany między zasoby lokalne i systemu SAP wdrożone Azure

### <a name="resource-groups"></a>Grupy zasobów
W systemie Azure Resource Manager można użyć grup zasobów do zarządzania wszystkie zasoby aplikacji w Twojej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Zasoby

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby SAP
Podczas konfigurowania wdrożenia oprogramowania SAP, potrzebne są następujące zasoby SAP:

* Uwaga SAP [1928533], który zawiera:
  * Lista rozmiary maszyn wirtualnych Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Informacje o rozmiary maszyn wirtualnych Azure ważne pojemności
  * Obsługiwane oprogramowanie SAP i kombinacji bazy danych i systemu operacyjnego (OS)
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure

* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP SAP, obsługiwane na platformie Azure.
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkie funkcje monitorowania metryki zgłoszone dla SAP na platformie Azure.
* Uwaga SAP [1409604] ma wymaganą wersję agenta hosta SAP dla systemu Windows na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Uwaga SAP [1984787] zawiera ogólne informacje o systemie SUSE Linux Enterprise Server 12.
* Uwaga SAP [2002167] zawiera ogólne informacje na temat Red Hat Enterprise Linux 7.x.
* Uwaga SAP [2069760] zawiera ogólne informacje na temat Oracle Linux 7.x.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów Azure rozszerzone monitorowanie rozszerzenia dla programu SAP.
* Uwaga SAP [1597355] zawiera ogólne informacje na temat obszaru wymiany w systemie Linux.
* [SAP na stronie Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) ma wiadomości i kolekcję przydatne zasoby.
* [SAP społeczności WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje SAP dla systemu Linux.
* Polecenia cmdlet programu PowerShell specyficzne dla programu SAP, które są częścią [programu Azure PowerShell][azure-ps].
* Specyficzne dla programu SAP polecenia interfejsu wiersza polecenia Azure, które są częścią [interfejsu wiersza polecenia Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby systemu Windows
Te artykuły Microsoft obejmuje wdrożenia SAP na platformie Azure:

* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP NetWeaver][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP NetWeaver (w tym artykule)][deployment-guide]
* [Azure wdrożenia SAP NetWeaver DBMS maszyny wirtualne][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenariusze wdrażania oprogramowania SAP na maszynach wirtualnych Azure
Istnieje wiele opcji wdrażania maszyn wirtualnych i skojarzone dyski na platformie Azure. Jest on wziąć pod uwagę różnice między opcje wdrażania, ponieważ może wykonać różne czynności, aby przygotować maszyn wirtualnych do wdrożenia na podstawie wybranego typu wdrożenia.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenariusz 1: Wdrażanie maszyny Wirtualnej z poziomu portalu Azure Marketplace dla SAP
Obraz, który został dostarczony przez firmę Microsoft lub innej firmy w portalu Azure Marketplace można użyć do wdrożenia maszyny Wirtualnej. Witryny Marketplace udostępnia pewne standardowe obrazów systemu operacyjnego Windows Server i różne dystrybucje systemu Linux. Również można wdrożyć obraz, który obejmuje zarządzanie bazą danych jednostki SKU systemu (danych DBMS), na przykład Microsoft SQL Server. Aby uzyskać więcej informacji o korzystaniu z obrazów z wersji systemu DBMS dotycząca produktu, zobacz [DBMS maszyny wirtualne Azure wdrożenia SAP NetWeaver][dbms-guide].

Poniższy schemat przedstawia specyficzne dla programu SAP sekwencji kroki w celu wdrożenia maszyny Wirtualnej z poziomu portalu Azure Marketplace:

![Schemat blokowy wdrożenia maszyny Wirtualnej dla systemów SAP przy użyciu obrazu maszyny Wirtualnej z portalu Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Utwórz maszynę wirtualną za pomocą portalu Azure
Najprostszym sposobem tworzenia nowej maszyny wirtualnej z obrazu z portalu Azure Marketplace jest przy użyciu portalu Azure.

1.  Przejdź do pozycji <https://portal.azure.com/#create/hub> (Plik > Nowy > Inny).  Lub, w menu portalu Azure wybierz **+ nowy**.
2.  Wybierz **obliczeniowe**, a następnie wybierz typ systemu operacyjnego, którą chcesz wdrożyć. Na przykład Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) lub Oracle Linux 7.2. Domyślny widok listy nie są wyświetlane wszystkie obsługiwane systemy operacyjne. Wybierz **zobaczyć wszystkie** pełną listę. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych dla wdrożenia oprogramowania SAP, patrz Uwaga SAP [1928533].
3.  Na następnej stronie Przejrzyj warunki i postanowienia.
4.  W **wybierz model wdrożenia** wybierz **Resource Manager**.
5.  Wybierz pozycję **Utwórz**.

Kreator przeprowadzi Cię przez ustawienie wymagane parametry, aby utworzyć maszynę wirtualną, oprócz wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i kont magazynu. Niektóre z tych parametrów, to:

1. **Podstawy**:
 * **Nazwa**: Nazwa zasobu (nazwę maszyny wirtualnej).
 * **Typ dysku maszyny Wirtualnej**: Wybierz typ dysku dysk systemu operacyjnego. Jeśli chcesz użyć Premium Storage dla dysków z danymi, firma Microsoft zaleca magazyn w warstwie Premium dla na dysku systemu operacyjnego.
 * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH**: Wprowadź nazwę użytkownika i hasło użytkownika, który jest tworzony podczas inicjowania obsługi. Dla maszyny wirtualnej systemu Linux można wprowadzić klucz publiczny Secure Shell (SSH), którego używasz do logowania się na tym komputerze.
 * **Subskrypcja**: Wybierz subskrypcję, która ma być używany do udostępnienia nowej maszyny wirtualnej.
 * **Grupa zasobów**: Nazwa grupy zasobów dla maszyny Wirtualnej. Można wprowadzić nazwę nowej grupy zasobów lub nazwa grupy zasobów, która już istnieje.
 * **Lokalizacja**: gdzie wdrażania nowej maszyny wirtualnej. Jeśli chcesz się połączyć z maszyną wirtualną do sieci lokalnej, upewnij się, że możesz wybrać lokalizację sieć wirtualną, która łączy się z siecią lokalną Azure. Aby uzyskać więcej informacji, zobacz [sieci Microsoft Azure] [ planning-guide-microsoft-azure-networking] w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide].
2. **Rozmiar**:

     Aby uzyskać listę obsługiwanych typów maszyny Wirtualnej, zobacz Uwaga SAP [1928533]. Upewnij się, że wybierz poprawny typ maszyny Wirtualnej, jeśli chcesz używać usługi Azure Premium Storage. Nie wszystkie typy maszyny Wirtualnej obsługuje usługi Premium Storage. Aby uzyskać więcej informacji, zobacz [magazynu: Microsoft Azure Storage i dysków z danymi] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] i [Azure Premium Storage] [ planning-guide-azure-premium-storage] w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide].

3. **Ustawienia**:
  * **Storage**
    * **Typ dysku**: Wybierz typ dysku dysk systemu operacyjnego. Jeśli chcesz użyć Premium Storage dla dysków z danymi, firma Microsoft zaleca magazyn w warstwie Premium dla na dysku systemu operacyjnego.
    * **Użyj zarządzanego dysków**: Jeśli chcesz używać dysków zarządzanych, wybierz opcję Tak. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz rozdział [dysków zarządzanych] [ planning-guide-managed-disks] w przewodniku planowania.
    * **Konto magazynu**: Wybierz istniejące konto magazynu lub Utwórz nową. Nie wszystkie typy magazynu działa w przypadku uruchamiania aplikacji SAP. Aby uzyskać więcej informacji na temat typów magazynu, zobacz [magazyn Microsoft Azure] [ dbms-guide-2.3] w [DBMS maszyny wirtualne Azure wdrożenia SAP NetWeaver][dbms-guide].
  * **Sieć**
    * **Sieć wirtualna** i **podsieci**: Aby zintegrować maszyny wirtualnej z sieci intranet, wybierz sieć wirtualną, która jest połączona z siecią lokalną.
    * **Publiczny adres IP**: Wybierz publiczny adres IP, którego chcesz użyć, lub wprowadź parametrów do utworzenia nowego publicznego adresu IP. Publiczny adres IP umożliwia dostęp do sieci maszyny wirtualnej za pośrednictwem Internetu. Upewnij się również utworzyć grupę zabezpieczeń sieci, aby zabezpieczać dostęp do maszyny wirtualnej.
    * **Grupy zabezpieczeń sieci**: Aby uzyskać więcej informacji, zobacz [sterowaniu przepływem ruchu sieciowego z grup zabezpieczeń sieci][virtual-networks-nsg].
  * **Rozszerzenia**: można zainstalować rozszerzenia maszyny wirtualnej przez dodanie ich do wdrożenia. Dodawanie rozszerzeń w tym kroku nie jest konieczne. Rozszerzenia do obsługi SAP wymagane są instalowane później. Patrz rozdział [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP] [ deployment-guide-4.5] w tym przewodniku.
  * **Wysoka dostępność**: Wybierz zestaw dostępności lub wprowadź parametrów do utworzenia nowego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [zestawami dostępności Azure][planning-guide-3.2.3].
  * **Monitorowanie**
    * **Diagnostyka rozruchu**: można wybrać **wyłączyć** dla diagnostyki rozruchu.
    * **Diagnostyka systemu operacyjnego gościa**: można wybrać **wyłączyć** monitorowania diagnostyki.

4. **Podsumowanie**:

  Przejrzyj wybrane opcje, a następnie wybierz **OK**.

Maszyna wirtualna jest wdrażana w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Utwórz maszynę wirtualną za pomocą szablonu
Można utworzyć maszynę wirtualną za pomocą jednego z szablonów SAP opublikowane w [repozytorium GitHub azure — Szybki Start — szablony][azure-quickstart-templates-github]. Również można ręcznie utworzyć maszynę wirtualną za pomocą [portalu Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], lub [interfejsu wiersza polecenia Azure][virtual-machines-linux-tutorial].

* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna)** (sap-2-warstwy —-obrazu z witryny marketplace)][sap-templates-2-tier-marketplace-image]

  Aby utworzyć dwuwarstwowy system przy użyciu tylko jednej maszyny wirtualnej, należy użyć tego szablonu.
* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna) - dysków zarządzanych** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Aby utworzyć dwuwarstwowy system przy użyciu tylko jednej maszyny wirtualnej i zarządzane dyski, należy użyć tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych)** (sap-3-warstwy —-obrazu z witryny marketplace)][sap-templates-3-tier-marketplace-image]

  Aby utworzyć systemie trójwarstwowej przy użyciu wielu maszyn wirtualnych, należy użyć tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych) - dysków zarządzanych** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Aby utworzyć systemie trójwarstwowej przy użyciu wielu maszyn wirtualnych i zarządzane dyski, należy użyć tego szablonu.

W portalu Azure wprowadź następujące parametry szablonu:

1. **Podstawy**:
  * **Subskrypcja**: subskrypcji na potrzeby wdrażania szablonu.
  * **Grupa zasobów**: grupy zasobów można użyć do wdrożenia szablonu. Można utworzyć nową grupę zasobów, lub wybrać istniejącą grupę zasobów w subskrypcji.
  * **Lokalizacja**: gdzie do wdrożenia szablonu. W przypadku wybrania istniejącej grupy zasobów jest używany lokalizacji tej grupy zasobów.

2. **Ustawienia**:
  * **Identyfikator systemu SAP**: identyfikator systemu SAP (SID).
  * **Typ systemu operacyjnego**: systemu operacyjnego, które mają zostać wdrożone, na przykład, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) lub Oracle Linux 7.2.

    Widok listy nie są wyświetlane wszystkie obsługiwane systemy operacyjne. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych dla wdrożenia oprogramowania SAP, patrz Uwaga SAP [1928533].
  * **Rozmiar systemu SAP**: rozmiar systemu SAP.

    Liczba protokoły SAP zapewnia nowy system. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, skontaktuj się z partnerem technologii SAP lub Integrator systemu.
  * **Dostępność systemu** (tylko szablon trójwarstwowa): dostępność systemu.

    Wybierz **HA** konfiguracji, który jest odpowiedni dla instalacji wysokiej dostępności. Tworzone są dwa serwery baz danych i dwóch serwerów dla ABAP SAP centralnej usług (ASCS).
  * **Typ magazynu** (tylko szablon dwuwarstwowa): typ magazynu do użycia.

    Dla większych systemów zdecydowanie zaleca się przy użyciu usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu zawierają następujące zasoby:
      * [Użycie magazynu SSD Azure — wersja Premium dla wystąpienia systemu DBMS SAP][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] w [wdrożenia SAP NetWeaver DBMS maszyn wirtualnych Azure][dbms-guide]
      * [Magazyn w warstwie Premium: Magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
  * **Nazwa użytkownika administratora** i **hasło administratora**: nazwa użytkownika i hasło.
    Nowy użytkownik jest tworzony do logowania się na maszynie wirtualnej.
  * **Nowy lub istniejący podsieci**: Określa, czy tworzenia nowej sieci wirtualnej i podsieci, czy jest używany przez istniejącą podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz **istniejące**.
  * **Identyfikator podsieci**: identyfikator podsieci maszyny wirtualnej będzie łączyć się. Wybierz podsieć sieci wirtualnej sieci prywatnej (VPN) lub sieci wirtualnej Azure ExpressRoute na potrzeby podłącz maszynę wirtualną do sieci lokalnej. Identyfikator zwykle wygląda następująco: /subscriptions/&lt;identyfikator subskrypcji > /resourceGroups/&lt;Nazwa grupy zasobów > /providers/Microsoft.Network/virtualNetworks/&lt;wirtualnej nazwy sieciowej > /subnets/&lt;nazwy podsieci >

3. **Warunki i postanowienia**:  
    Przeczytaj i zaakceptuj postanowienia prawne.

4.  Wybierz **zakupu**.

Agent maszyny Wirtualnej Azure jest wdrażany domyślnie, korzystając z obrazu z portalu Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy
W zależności od konfiguracji sieci lokalnej może być konieczne ustawienie serwera proxy na maszynie Wirtualnej. Maszyny Wirtualnej jest podłączony do sieci lokalnej za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna nie może mieć możliwość uzyskania dostępu do Internetu i nie można pobrać wymagane rozszerzenia lub zbierania danych monitorowania. Aby uzyskać więcej informacji, zobacz [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko system Windows)
Jeśli wdrożenie usługi Azure jest podłączony do lokalnego wystąpienia usługi Active Directory i DNS za pośrednictwem połączenia sieci VPN lokacja lokacja platformy Azure lub usługa ExpressRoute (jest to *między lokalizacjami* w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna przyłączania do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego zadania, zobacz [przyłącz Maszynę wirtualną do domeny usługi lokalnej (tylko system Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurowanie monitorowania
Aby upewnić się, że SAP obsługuje, skonfiguruj środowisko rozszerzenie monitorowania Azure dla programu SAP zgodnie z opisem w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5]. Sprawdź wymagania wstępne dotyczące monitorowania SAP, a wymagane minimalne wersje programu SAP jądra i agenta hosta SAP w zasobach na liście [zasobów SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Monitorowanie wyboru
Sprawdź, czy monitorowanie działa, zgodnie z opisem w [sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Czynności po wdrożeniu
Po utworzenia maszyny Wirtualnej i maszyna wirtualna jest wdrożona, należy zainstalować składniki oprogramowania wymagane na maszynie wirtualnej. Z powodu sekwencji instalacji programowy wdrożenia w tym typie wdrożenia maszyny Wirtualnej zainstalowanie oprogramowania musi być dostępna, albo na platformie Azure na innej maszynie Wirtualnej lub jako dysk, który można dołączyć. Lub, rozważ użycie scenariusza między różnymi lokalizacjami, w których łączność z lokalną podano zasobów (udziałów instalacji).

Po wdrożeniu maszyny Wirtualnej na platformie Azure, wykonaj te same wskazówki i narzędzia do zainstalowania oprogramowania SAP na maszynie Wirtualnej, tak jak w środowisku lokalnym. Do zainstalowania oprogramowania SAP na maszynie Wirtualnej platformy Azure, zarówno SAP, jak i Microsoft zaleca przekazywanie i przechowywać nośnika instalacyjnego programu SAP na wirtualne dyski twarde Azure lub zarządzane dysków lub utworzenie maszyny Wirtualnej platformy Azure który działa jako serwer plików, który ma wszystkie wymagane nośnik instalacyjny programu SAP.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazu niestandardowego dla SAP
Ponieważ różne wersje systemu operacyjnego lub DBMS mają różne wymagania dotyczące, obrazów, które możesz znaleźć w portalu Azure Marketplace nie spełnia Twoje potrzeby. Zamiast tego można utworzyć Maszynę wirtualną przy użyciu własnego obrazu systemu operacyjnego/DBMS maszyny Wirtualnej, który można wdrożyć ponownie później.
Różne kroki umożliwiają utworzenie prywatnej obrazu w systemie Linux niż można utworzyć dla systemu Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> Aby przygotować obraz systemu Windows, którego można użyć do wdrożenia wielu maszyn wirtualnych, ustawień systemu Windows (takich jak Windows SID i nazwy hosta) musi pobieranej lub uogólniony na lokalnej maszynie Wirtualnej. Można użyć [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) w tym celu.
>
> ![Linux][Logo_Linux] Linux
>
> Aby przygotować obraz systemu Linux, który umożliwia wdrożenie wielu maszyn wirtualnych, niektóre ustawienia Linux muszą pobieranej lub uogólniony na lokalnej maszynie Wirtualnej. Można użyć `waagent -deprovision` w tym celu. Aby uzyskać więcej informacji, zobacz [Przechwytywanie maszyny wirtualnej systemu Linux działających na platformie Azure] [ virtual-machines-linux-capture-image] i [Podręcznik użytkownika agenta systemu Linux Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Można przygotowanie i utworzyć niestandardowy obraz, a następnie użyć jej do utworzenia wielu maszyn wirtualnych. Jest to opisane w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide]. Konfigurowanie zawartości bazy danych przy użyciu Menedżera inicjowania obsługi oprogramowania SAP do instalacji nowego systemu SAP (przywrócenie kopii zapasowej bazy danych z dysku, który jest dołączony do maszyny wirtualnej) lub przez bezpośrednio przywracanie kopii zapasowej bazy danych z magazynu Azure, jeśli obsługuje systemu DBMS. Aby uzyskać więcej informacji, zobacz [DBMS maszyny wirtualne Azure wdrożenia SAP NetWeaver][dbms-guide]. Zainstalowano SAP system na lokalnej maszynie Wirtualnej (szczególnie w przypadku systemów dwuwarstwowa) można dostosować ustawienia systemu SAP po wdrożeniu maszyny Wirtualnej Azure, za pomocą procedury zmienić System obsługiwana przez Menedżera inicjowania obsługi oprogramowania SAP (Uwaga SAP [1619720]). W przeciwnym razie można zainstalować oprogramowania SAP, po wdrożeniu maszyny Wirtualnej platformy Azure.

Poniższy schemat przedstawia specyficzne dla programu SAP sekwencji kroki w celu wdrożenia maszyny Wirtualnej z obrazu niestandardowego:

![Schemat blokowy wdrożenia maszyny Wirtualnej dla systemów SAP przy użyciu obrazu maszyny Wirtualnej w prywatnej Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Utwórz maszynę wirtualną za pomocą portalu Azure
Najprostszym sposobem tworzenia nowej maszyny wirtualnej z obrazu dysku zarządzanego jest przy użyciu portalu Azure. Aby uzyskać więcej informacji na temat tworzenia obrazu dysku Zarządzanie odczytu [Przechwyć obraz zarządzanych uogólniony maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Przejdź do pozycji <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> (Plik > Nowy > Inny). Lub, w menu portalu Azure wybierz **obrazów**.
2.  Wybierz obraz dysku zarządzanego chcesz wdrożyć, a następnie kliknij przycisk **Utwórz maszynę Wirtualną**

Kreator przeprowadzi Cię przez ustawienie wymagane parametry, aby utworzyć maszynę wirtualną, oprócz wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i kont magazynu. Niektóre z tych parametrów, to:

1. **Podstawy**:
 * **Nazwa**: Nazwa zasobu (nazwę maszyny wirtualnej).
 * **Typ dysku maszyny Wirtualnej**: Wybierz typ dysku dysk systemu operacyjnego. Jeśli chcesz użyć Premium Storage dla dysków z danymi, firma Microsoft zaleca magazyn w warstwie Premium dla na dysku systemu operacyjnego.
 * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH**: Wprowadź nazwę użytkownika i hasło użytkownika, który jest tworzony podczas inicjowania obsługi. Dla maszyny wirtualnej systemu Linux można wprowadzić klucz publiczny Secure Shell (SSH), którego używasz do logowania się na tym komputerze.
 * **Subskrypcja**: Wybierz subskrypcję, która ma być używany do udostępnienia nowej maszyny wirtualnej.
 * **Grupa zasobów**: Nazwa grupy zasobów dla maszyny Wirtualnej. Można wprowadzić nazwę nowej grupy zasobów lub nazwa grupy zasobów, która już istnieje.
 * **Lokalizacja**: gdzie wdrażania nowej maszyny wirtualnej. Jeśli chcesz się połączyć z maszyną wirtualną do sieci lokalnej, upewnij się, że możesz wybrać lokalizację sieć wirtualną, która łączy się z siecią lokalną Azure. Aby uzyskać więcej informacji, zobacz [sieci Microsoft Azure] [ planning-guide-microsoft-azure-networking] w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide].
2. **Rozmiar**:

     Aby uzyskać listę obsługiwanych typów maszyny Wirtualnej, zobacz Uwaga SAP [1928533]. Upewnij się, że wybierz poprawny typ maszyny Wirtualnej, jeśli chcesz używać usługi Azure Premium Storage. Nie wszystkie typy maszyny Wirtualnej obsługuje usługi Premium Storage. Aby uzyskać więcej informacji, zobacz [magazynu: Microsoft Azure Storage i dysków z danymi] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] i [Azure Premium Storage] [ planning-guide-azure-premium-storage] w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide].

3. **Ustawienia**:
  * **Storage**
    * **Typ dysku**: Wybierz typ dysku dysk systemu operacyjnego. Jeśli chcesz użyć Premium Storage dla dysków z danymi, firma Microsoft zaleca magazyn w warstwie Premium dla na dysku systemu operacyjnego.
    * **Użyj zarządzanego dysków**: Jeśli chcesz używać dysków zarządzanych, wybierz opcję Tak. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz rozdział [dysków zarządzanych] [ planning-guide-managed-disks] w przewodniku planowania.
  * **Sieć**
    * **Sieć wirtualna** i **podsieci**: Aby zintegrować maszyny wirtualnej z sieci intranet, wybierz sieć wirtualną, która jest połączona z siecią lokalną.
    * **Publiczny adres IP**: Wybierz publiczny adres IP, którego chcesz użyć, lub wprowadź parametrów do utworzenia nowego publicznego adresu IP. Publiczny adres IP umożliwia dostęp do sieci maszyny wirtualnej za pośrednictwem Internetu. Upewnij się również utworzyć grupę zabezpieczeń sieci, aby zabezpieczać dostęp do maszyny wirtualnej.
    * **Grupy zabezpieczeń sieci**: Aby uzyskać więcej informacji, zobacz [sterowaniu przepływem ruchu sieciowego z grup zabezpieczeń sieci][virtual-networks-nsg].
  * **Rozszerzenia**: można zainstalować rozszerzenia maszyny wirtualnej przez dodanie ich do wdrożenia. Dodawanie rozszerzenia w tym kroku nie jest konieczne. Rozszerzenia do obsługi SAP wymagane są instalowane później. Patrz rozdział [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP] [ deployment-guide-4.5] w tym przewodniku.
  * **Wysoka dostępność**: Wybierz zestaw dostępności lub wprowadź parametrów do utworzenia nowego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [zestawami dostępności Azure][planning-guide-3.2.3].
  * **Monitorowanie**
    * **Diagnostyka rozruchu**: można wybrać **wyłączyć** dla diagnostyki rozruchu.
    * **Diagnostyka systemu operacyjnego gościa**: można wybrać **wyłączyć** monitorowania diagnostyki.

4. **Podsumowanie**:

  Przejrzyj wybrane opcje, a następnie wybierz **OK**.

Maszyna wirtualna jest wdrażana w wybranej grupie zasobów.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Utwórz maszynę wirtualną za pomocą szablonu
Aby utworzyć wdrożenie przy użyciu prywatnych obrazu systemu operacyjnego z portalu Azure, użyj jednej z następujących szablonów SAP. Te szablony są publikowane w [repozytorium GitHub azure — Szybki Start — szablony][azure-quickstart-templates-github]. Możesz również można ręcznie utworzyć maszynę wirtualną za pomocą [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna)** (sap-2-warstwy —-obrazu użytkownika)][sap-templates-2-tier-user-image]

  Aby utworzyć dwuwarstwowy system przy użyciu tylko jednej maszyny wirtualnej, należy użyć tego szablonu.
* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna) - zarządzanego obrazu dysku** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Aby utworzyć dwuwarstwowy system przy użyciu tylko jednej maszyny wirtualnej i obraz dysku zarządzane, należy użyć tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych)** (sap-3-warstwy —-obrazu użytkownika)][sap-templates-3-tier-user-image]

  Aby utworzyć systemie trójwarstwowej przy użyciu wielu maszyn wirtualnych lub obrazu systemu operacyjnego, należy użyć tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych) - zarządzanego obrazu dysku** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Aby utworzyć systemie trójwarstwowej przy użyciu wielu maszyn wirtualnych lub obrazu systemu operacyjnego i obraz dysku zarządzane, należy użyć tego szablonu.

W portalu Azure wprowadź następujące parametry szablonu:

1. **Podstawy**:
  * **Subskrypcja**: subskrypcji na potrzeby wdrażania szablonu.
  * **Grupa zasobów**: grupy zasobów można użyć do wdrożenia szablonu. Możesz utworzyć nową grupę zasobów lub wybierz istniejącą grupę zasobów w subskrypcji.
  * **Lokalizacja**: gdzie do wdrożenia szablonu. W przypadku wybrania istniejącej grupy zasobów jest używany lokalizacji tej grupy zasobów.
2. **Ustawienia**:
  * **Identyfikator systemu SAP**: identyfikator systemu SAP.
  * **Typ systemu operacyjnego**: typ systemu operacyjnego, którą chcesz wdrożyć (Windows lub Linux).
  * **Rozmiar systemu SAP**: rozmiar systemu SAP.

    Liczba protokoły SAP zapewnia nowy system. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, skontaktuj się z partnerem technologii SAP lub Integrator systemu.
  * **Dostępność systemu** (tylko szablon trójwarstwowa): dostępność systemu.

    Wybierz **HA** konfiguracji, który jest odpowiedni dla instalacji wysokiej dostępności. Dwa serwery baz danych i dwóch serwerów dla ASCS są tworzone.
  * **Typ magazynu** (tylko szablon dwuwarstwowa): typ magazynu do użycia.

    Dla większych systemów zdecydowanie zaleca się przy użyciu usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu zobacz następujące zasoby:
      * [Użycie magazynu SSD Azure — wersja Premium dla wystąpienia systemu DBMS SAP][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] w [wdrożenia SAP NetWeaver DBMS maszyn wirtualnych Azure][dbms-guide]
      * [Magazyn w warstwie Premium: Magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
  * **Obraz użytkownika identyfikator URI dysku VHD** (tylko szablon obrazu dysku niezarządzanego): identyfikator URI prywatnej systemu operacyjnego obrazu wirtualnego dysku twardego, na przykład https://&lt;nazwa konta >.blob.core.windows.net/vhds/userimage.vhd.
  * **Konto magazynu obrazu użytkownika** (tylko szablon obrazu dysku niezarządzanego): Nazwa konta magazynu prywatnej obrazu systemu operacyjnego przechowywania, na przykład &lt;accountname > w https://&lt;nazwa konta >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (tylko szablon obrazu dysku zarządzanego): identyfikator obrazu zarządzane dysku, którego chcesz użyć
  * **Nazwa użytkownika administratora** i **hasło administratora**: nazwa użytkownika i hasło.

    Nowy użytkownik jest tworzony do logowania się na maszynie wirtualnej.
  * **Nowy lub istniejący podsieci**: Określa, czy utworzono nową sieć wirtualną i podsieć, czy jest używany przez istniejącą podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz **istniejące**.
  * **Identyfikator podsieci**: identyfikator podsieci, do której maszyny wirtualne będą łączyć się. Wybierz podsieć sieci VPN i ExpressRoute wirtualnej na potrzeby podłącz maszynę wirtualną do sieci lokalnej. Identyfikator zwykle wygląda następująco:

    /Subscriptions/&lt;identyfikator subskrypcji > /resourceGroups/&lt;Nazwa grupy zasobów > /providers/Microsoft.Network/virtualNetworks/&lt;wirtualnej nazwy sieciowej > /subnets/&lt;nazwy podsieci >

3. **Warunki i postanowienia**:  
    Przeczytaj i zaakceptuj postanowienia prawne.

4.  Wybierz **zakupu**.

#### <a name="install-the-vm-agent-linux-only"></a>Zainstaluj agenta maszyny Wirtualnej (tylko w systemie Linux)
Aby użyć szablonów opisanych w poprzedniej sekcji, agenta systemu Linux muszą być zainstalowane z obrazu użytkownika lub wdrożenie zakończy się niepowodzeniem. Pobierz i zainstaluj agenta maszyny Wirtualnej z obrazu użytkownika, zgodnie z opisem w [pobrać, zainstalować i włączyć agenta maszyny Wirtualnej Azure][deployment-guide-4.4]. Jeśli nie używasz szablony, również można zainstalować agenta maszyny Wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko system Windows)
Jeśli wdrożenie usługi Azure jest podłączony do lokalnego wystąpienia usługi Active Directory i DNS za pośrednictwem połączenia sieci VPN lokacja lokacja platformy Azure lub usługa Azure ExpressRoute (jest to *między lokalizacjami* w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna przyłączania do domeny lokalnej. Aby uzyskać więcej informacji dotyczących tego kroku, zobacz [przyłącz Maszynę wirtualną do domeny usługi lokalnej (tylko system Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy
W zależności od konfiguracji sieci lokalnej może być konieczne ustawienie serwera proxy na maszynie Wirtualnej. Maszyny Wirtualnej jest podłączony do sieci lokalnej za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna nie może mieć możliwość uzyskania dostępu do Internetu i nie można pobrać wymagane rozszerzenia lub zbierania danych monitorowania. Aby uzyskać więcej informacji, zobacz [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurowanie monitorowania
Aby upewnić się, że SAP obsługuje, skonfiguruj środowisko rozszerzenie monitorowania Azure dla programu SAP zgodnie z opisem w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5]. Sprawdź wymagania wstępne dotyczące monitorowania SAP, a wymagane minimalne wersje programu SAP jądra i agenta hosta SAP w zasobach na liście [zasobów SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Monitorowanie wyboru
Sprawdź, czy monitorowanie działa, zgodnie z opisem w [sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenariusz 3: Przenoszenie maszyny Wirtualnej z lokalnymi przy użyciu-uogólniony wirtualny dysk twardy Azure SAP
W tym scenariuszu ma zostać przeniesiona do określonego systemu SAP ze środowiska lokalnego do platformy Azure. Można to zrobić, przekazując wirtualny dysk twardy systemu operacyjnego, plików binarnych programu SAP, i po pewnym czasie plików binarnych systemu DBMS oraz wirtualne dyski twarde z plikami danych i dziennika systemu DBMS na platformie Azure. W odróżnieniu od scenariusz opisany w [Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazu niestandardowego dla SAP][deployment-guide-3.3], w tym przypadku zachować nazwa hosta, SAP SID i kont użytkowników SAP w maszyny Wirtualnej Azure, ponieważ zostały one skonfigurowane w środowisku lokalnym. Nie należy do uogólnienia systemu operacyjnego. Ten scenariusz dotyczy najczęściej scenariusze między różnymi lokalizacjami, gdzie część pozioma SAP działa lokalnie, a jego część nie działa na platformie Azure.

W tym scenariuszu, Agent maszyny Wirtualnej jest **nie** automatycznie zainstalowany podczas wdrażania. Ponieważ agenta maszyny Wirtualnej i Azure rozszerzone monitorowanie rozszerzenia dla programu SAP są wymagane do uruchomienia programu SAP NetWeaver na platformie Azure, należy pobrać, zainstalować i włączyć oba te składniki ręcznie po utworzeniu maszyny wirtualnej.

Aby uzyskać więcej informacji na temat agenta maszyny Wirtualnej Azure zobacz następujące zasoby.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Omówienie usługi Azure agenta maszyny wirtualnej][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Podręcznik użytkownika Azure agenta systemu Linux][virtual-machines-linux-agent-user-guide]
>
>

- - -

Poniższy schemat przedstawia sekwencja kroków do przenoszenia maszyny Wirtualnej z lokalnymi przy użyciu-uogólniony wirtualny dysk twardy Azure:

![Schemat blokowy wdrożenia maszyny Wirtualnej dla programu SAP systemów za pomocą dysku maszyny Wirtualnej][deployment-guide-figure-400]

Jeśli dysk jest już przekazany i określone na platformie Azure (zobacz [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide]), czy zadania opisane w następnej sekcji kilka.

#### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby utworzyć wdrożenie przy użyciu prywatnych dysku systemu operacyjnego za pośrednictwem portalu Azure, należy użyć szablonu SAP opublikowane w [repozytorium GitHub azure — Szybki Start — szablony][azure-quickstart-templates-github]. Możesz również można ręcznie utworzyć maszynę wirtualną za pomocą programu PowerShell.

* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna)** (sap-2-warstwy użytkownika dysk)][sap-templates-2-tier-os-disk]

  Aby utworzyć dwuwarstwowy system przy użyciu tylko jednej maszyny wirtualnej, należy użyć tego szablonu.
* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna) - zarządzane dysku** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Aby utworzyć dwuwarstwowy system przy użyciu tylko jednej maszyny wirtualnej i dysku zarządzane, należy użyć tego szablonu.

W portalu Azure wprowadź następujące parametry szablonu:

1. **Podstawy**:
  * **Subskrypcja**: subskrypcji na potrzeby wdrażania szablonu.
  * **Grupa zasobów**: grupy zasobów można użyć do wdrożenia szablonu. Możesz utworzyć nową grupę zasobów lub wybierz istniejącą grupę zasobów w subskrypcji.
  * **Lokalizacja**: gdzie do wdrożenia szablonu. W przypadku wybrania istniejącej grupy zasobów jest używany lokalizacji tej grupy zasobów.
2. **Ustawienia**:
  * **Identyfikator systemu SAP**: identyfikator systemu SAP.
  * **Typ systemu operacyjnego**: typ systemu operacyjnego, którą chcesz wdrożyć (Windows lub Linux).
  * **Rozmiar systemu SAP**: rozmiar systemu SAP.

    Liczba protokoły SAP zapewnia nowy system. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, skontaktuj się z partnerem technologii SAP lub Integrator systemu.
  * **Typ magazynu** (tylko szablon dwuwarstwowa): typ magazynu do użycia.

    Dla większych systemów zdecydowanie zaleca się przy użyciu usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu zobacz następujące zasoby:
      * [Użycie magazynu SSD Azure — wersja Premium dla wystąpienia systemu DBMS SAP][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] w [wdrożenia SAP NetWeaver DBMS maszyny wirtualnej Azure][dbms-guide]
      * [Magazyn w warstwie Premium: Magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
  * **Identyfikator URI dysku VHD na dysku systemu operacyjnego** (tylko szablon niezarządzane dysku): identyfikator URI prywatnej dysku systemu operacyjnego, na przykład https://&lt;nazwa konta >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Identyfikator dysku zarządzanego na dysku systemu operacyjnego** (tylko szablon dysków zarządzanych): identyfikator dysku zarządzanego systemu operacyjnego z dysku /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Nowy lub istniejący podsieci**: Określa, czy tworzenia nowej sieci wirtualnej i podsieci, czy jest używany przez istniejącą podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz **istniejące**.
  * **Identyfikator podsieci**: identyfikator podsieci, do której maszyny wirtualne będą łączyć się. Wybierz podsieć sieci VPN lub rozwiązania Azure ExpressRoute wirtualnej na potrzeby podłącz maszynę wirtualną do sieci lokalnej. Identyfikator zwykle wygląda następująco:

    /Subscriptions/&lt;identyfikator subskrypcji > /resourceGroups/&lt;Nazwa grupy zasobów > /providers/Microsoft.Network/virtualNetworks/&lt;wirtualnej nazwy sieciowej > /subnets/&lt;nazwy podsieci >

3. **Warunki i postanowienia**:  
    Przeczytaj i zaakceptuj postanowienia prawne.

4.  Wybierz **zakupu**.

#### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej
Aby użyć szablonów opisanych w poprzedniej sekcji, musi być zainstalowany Agent maszyny Wirtualnej, na dysku systemu operacyjnego lub wdrożenie zakończy się niepowodzeniem. Pobierz i zainstaluj agenta maszyny Wirtualnej w maszynie Wirtualnej, zgodnie z opisem w [pobrać, zainstalować i włączyć agenta maszyny Wirtualnej Azure][deployment-guide-4.4].

Jeśli nie używasz szablony opisanych w poprzedniej sekcji, można także zainstalować agenta maszyny Wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko system Windows)
Jeśli wdrożenie usługi Azure jest podłączony do lokalnego wystąpienia usługi Active Directory i DNS za pośrednictwem połączenia sieci VPN lokacja lokacja platformy Azure lub usługa ExpressRoute (jest to *między lokalizacjami* w [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna przyłączania do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego zadania, zobacz [przyłącz Maszynę wirtualną do domeny usługi lokalnej (tylko system Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy
W zależności od konfiguracji sieci lokalnej może być konieczne ustawienie serwera proxy na maszynie Wirtualnej. Maszyny Wirtualnej jest podłączony do sieci lokalnej za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna nie może mieć możliwość uzyskania dostępu do Internetu i nie można pobrać wymagane rozszerzenia lub zbierania danych monitorowania. Aby uzyskać więcej informacji, zobacz [Skonfiguruj serwer proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurowanie monitorowania
Aby upewnić się, że SAP obsługuje, skonfiguruj środowisko rozszerzenie monitorowania Azure dla programu SAP zgodnie z opisem w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5]. Sprawdź wymagania wstępne dotyczące monitorowania SAP, a wymagane minimalne wersje programu SAP jądra i agenta hosta SAP w zasobach na liście [zasobów SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Monitorowanie wyboru
Sprawdź, czy monitorowanie działa, zgodnie z opisem w [sprawdzanie i rozwiązywanie problemów dotyczących konfigurowania monitorowania end-to-end][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Aktualizacja konfiguracji monitorowania dla programu SAP
Aktualizacja konfiguracji monitorowania SAP w jednym z następujących scenariuszy:
* Zespół Microsoft/SAP wspólnego rozszerza możliwości monitorowania i żąda liczniki więcej lub mniej.
* Microsoft wprowadziła nową wersję podstawowej infrastruktury platformy Azure, który dostarcza dane monitorowania i Azure rozszerzone monitorowanie rozszerzenia dla programu SAP należy dostosować tych zmian.
* Instalowanie dodatkowego dysku z danymi do maszyny Wirtualnej Azure, lub Usuń dysk z danymi. W tym scenariuszu należy zaktualizować zbierania związane z magazynowaniem danych. Zmiana konfiguracji przez dodawanie lub usuwanie punktów końcowych lub przypisanie adresów IP do maszyny Wirtualnej nie ma wpływu na konfiguracji monitorowania.
* Zmień rozmiar maszyny Wirtualnej Azure, są na przykład od rozmiaru A5 do innych rozmiar maszyny Wirtualnej.
* Możesz dodać nowe interfejsów sieciowych do maszyny Wirtualnej Azure.

Aby zaktualizować ustawienia monitorowania, należy zaktualizować monitorowania infrastruktury wykonując kroki opisane w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Szczegółowe zadania dla wdrożenia oprogramowania SAP
W tej sekcji przedstawiono szczegółowe kroki dotyczące wykonywania określonych zadań w procesie konfiguracji i wdrażania.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Wdrażanie poleceń cmdlet programu Azure PowerShell
1.  Przejdź do [platformy Microsoft Azure pobiera](https://azure.microsoft.com/downloads/).
2.  W obszarze **narzędzia wiersza polecenia**, w obszarze **PowerShell**, wybierz pozycję **Windows zainstaluj**.
3.  W oknie dialogowym Menedżera pobierania firmy Microsoft dla pobranego pliku (na przykład WindowsAzurePowershellGet.3f.3f.3fnew.exe), wybierz **Uruchom**.
4.  Aby uruchomić Instalatora platformy sieci Web firmy Microsoft (Microsoft Web PI), zaznacz **tak**.
5.  Strona, która wygląda jak pojawia się:

  ![Strona instalacji dla poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Wybierz **zainstalować**, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
7.  Środowisko PowerShell jest zainstalowane. Wybierz **Zakończ** aby zamknąć kreatora instalacji.

Często sprawdzaj aktualizacje do poleceń cmdlet programu PowerShell, które zwykle są aktualizowane co miesiąc. Najprostszym sposobem, aby sprawdzić dostępność aktualizacji jest przeprowadzenie powyższych kroków instalacji, aż strona instalacji przedstawionym w kroku 5. Data i wersji numer wersji poleceń cmdlet znajdują się na stronie przedstawionym w kroku 5. Jeżeli nie określono inaczej w Uwaga SAP [1928533] lub Uwaga SAP [2015553], zaleca się pracy z najnowszą wersją poleceń cmdlet programu Azure PowerShell.

Aby sprawdzić wersję poleceń cmdlet programu Azure PowerShell, które są zainstalowane na komputerze, uruchom następujące polecenie programu PowerShell:
```powershell
(Get-Module AzureRm.Compute).Version
```
Wynik wygląda następująco:

![Wynik sprawdzenie wersji polecenia cmdlet programu Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Jeśli zainstalowana na komputerze wersja Azure polecenia cmdlet jest bieżącą wersję, pierwszej stronie Kreatora instalacji wskazuje on, dodając **(zainstalować)** do tytułu produktu (zobacz poniższy zrzut ekranu). Polecenia cmdlet programu PowerShell Azure są aktualne. Aby zamknąć kreatora instalacji, wybierz **zakończenia**.

![Strona instalacji dla poleceń cmdlet programu Azure PowerShell wskazujący, że zainstalowano najnowszą wersję poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Wdrażanie usługi Azure CLI
1.  Przejdź do [platformy Microsoft Azure pobiera](https://azure.microsoft.com/downloads/).
2.  W obszarze **narzędzia wiersza polecenia**w obszarze **interfejsu wiersza polecenia platformy Azure**, wybierz pozycję **zainstalować** link dla systemu operacyjnego.
3.  W oknie dialogowym Menedżera pobierania firmy Microsoft dla pobranego pliku (na przykład WindowsAzureXPlatCLI.3f.3f.3fnew.exe), wybierz **Uruchom**.
4.  Aby uruchomić Instalatora platformy sieci Web firmy Microsoft (Microsoft Web PI), zaznacz **tak**.
5.  Strona, która wygląda jak pojawia się:

  ![Strona instalacji dla poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Wybierz **zainstalować**, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
7.  Interfejs wiersza polecenia platformy Azure jest zainstalowany. Wybierz **Zakończ** aby zamknąć kreatora instalacji.

Często sprawdzaj aktualizacje interfejsu wiersza polecenia Azure, która zwykle jest aktualizowana co miesiąc. Najprostszym sposobem, aby sprawdzić dostępność aktualizacji jest przeprowadzenie powyższych kroków instalacji, aż strona instalacji przedstawionym w kroku 5.

Aby sprawdzić wersję interfejsu wiersza polecenia Azure, która jest zainstalowana na komputerze, uruchom następujące polecenie:
```
azure --version
```

Wynik wygląda następująco:

![Wynik sprawdzania wersji interfejsu wiersza polecenia Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Przyłącz Maszynę wirtualną do domeny usługi lokalnej (tylko system Windows)
W przypadku wdrożenia SAP maszyn wirtualnych w scenariuszu obejmującym różne pomieszczenia, gdzie w lokalnej usłudze Active Directory i DNS zostały rozszerzone na platformie Azure, oczekuje się, że maszyny wirtualne są przyłączania do domeny lokalnej. Szczegółowy opis kroków, które należy wykonać w celu dołączenia do domeny lokalnej i dodatkowe oprogramowanie musi być członkiem domeny lokalnej maszyny Wirtualnej jest zależna od klienta. Zwykle Aby dołączyć Maszynę wirtualną do domeny lokalnej, należy zainstalować dodatkowe oprogramowanie, takie jak ochrony przed złośliwym oprogramowaniem i tworzenia kopii zapasowej lub monitorowania oprogramowania.

W tym scenariuszu należy również upewnij się, że maszyna wirtualna jest dołączany do domeny w środowisku wymuszenie ustawień internetowego serwera proxy systemu Windows konta systemu lokalnego (S-1-5-18) na maszynie wirtualnej gościa ma tych samych ustawień serwera proxy. Opcja najprostszym jest wymuszenie serwer proxy przy użyciu zasad grupy, które ma zastosowanie do systemów w domenie domeny.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Pobrać, zainstalować i włączyć agenta maszyny Wirtualnej Azure
Dla maszyn wirtualnych, które zostały wdrożone z obrazu systemu operacyjnego, który nie jest uogólniona (na przykład obraz, który nie pochodzą z narzędzia przygotowywania systemu Windows lub programu sysprep,) musisz ręcznie pobrać, zainstalować i włączyć agenta maszyny Wirtualnej Azure.

W przypadku wdrożenia maszyny Wirtualnej z poziomu portalu Azure Marketplace, ten krok nie jest wymagane. Obrazy z portalu Azure Marketplace już agenta maszyny Wirtualnej Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Pobierz agenta maszyny Wirtualnej platformy Azure:
  1.  Pobierz [pakiet Instalatora agenta maszyny Wirtualnej Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
  2.  Lokalnie przechowywane pakiet MSI agenta maszyny Wirtualnej na serwerze lub komputerze osobistym.
2.  Zainstaluj agenta maszyny Wirtualnej platformy Azure:
  1.  Nawiązać wdrożonej maszyny Wirtualnej platformy Azure przy użyciu protokołu RDP (Remote Desktop).
  2.  Otwórz okno Eksploratora Windows na maszynie Wirtualnej, a następnie wybierz katalog docelowy dla pliku MSI agenta maszyny Wirtualnej.
  3.  Przeciągnij plik MSI Instalatora agenta maszyny Wirtualnej Azure z komputera/serwera lokalnego do katalogu docelowego agenta maszyny Wirtualnej na maszynie Wirtualnej.
  4.  Kliknij dwukrotnie plik MSI w maszynie Wirtualnej.
3.  Dla maszyn wirtualnych, które są przyłączone do domeny lokalnej, upewnij się, że ostatecznego ustawień internetowego serwera proxy dotyczą także konta systemu lokalnego (S-1-5-18) na maszynie wirtualnej, zgodnie z opisem w [Skonfiguruj serwer proxy][deployment-guide-configure-proxy]. Agent maszyny Wirtualnej działa w tym kontekście i musi mieć możliwość połączenia z platformą Azure.

Interakcja użytkownika nie jest wymagana w celu zaktualizowania agenta maszyny Wirtualnej Azure. Agent maszyny Wirtualnej jest aktualizowany automatycznie i nie wymaga ponownego uruchomienia maszyny Wirtualnej.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Aby zainstalować agenta maszyny Wirtualnej dla systemu Linux, należy użyć następujących poleceń:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) lub Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Jeśli agent jest już zainstalowany, aby uaktualnić agenta systemu Linux platformy Azure, wykonaj kroki opisane w [zaktualizować agenta systemu Linux platformy Azure na maszynie Wirtualnej do najnowszej wersji z serwisu GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Skonfiguruj serwer proxy
Czynności, które należy wykonać w celu skonfigurowania serwera proxy w systemie Windows różnią się od sposobu skonfigurowania serwera proxy w systemie Linux.

#### <a name="windows"></a>Windows
Ustawienia serwera proxy musi być poprawnie skonfigurowane dla konta System lokalny, aby uzyskać dostęp do Internetu. Jeśli ustawienia serwera proxy nie są skonfigurowane przy użyciu zasad grupy, można skonfigurować ustawienia dla lokalnego konta systemowego.

1. Przejdź do **Start**, wprowadź **gpedit.msc**, a następnie wybierz **Enter**.
2. Wybierz **Konfiguracja komputera** > **Szablony administracyjne** > **składniki systemu Windows** > **programu Internet Explorer**. Upewnij się, że ustawienie **upewnij proxy ustawienia dla poszczególnych komputerów (a nie dla poszczególnych użytkowników)** zostało wyłączone lub nieskonfigurowane.
3. W **Panelu sterowania**, przejdź do **Centrum sieci i udostępniania** > **Opcje internetowe**.
4. Na **połączeń** wybierz opcję **ustawienia sieci LAN** przycisku.
5. Wyczyść **Automatycznie wykryj ustawienia** pole wyboru.
6. Wybierz **Użyj serwera proxy dla sieci LAN** pole wyboru, a następnie wprowadź adres i port proxy.
7. Wybierz **zaawansowane** przycisku.
8. W **wyjątki** wprowadź adres IP **168.63.129.16**. Kliknij przycisk **OK**.

#### <a name="linux"></a>Linux
Skonfiguruj serwer proxy poprawne w pliku konfiguracji agenta gościa Microsoft Azure, która znajduje się w \\itp\\waagent.conf.

Ustaw następujące parametry:

1.  **Hosta serwera proxy HTTP**. Na przykład ustawić ją na **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **Port serwera proxy HTTP**. Na przykład ustawić ją na **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  Uruchom ponownie agenta.

  ```
  sudo service waagent restart
  ```

Ustawienia serwera proxy w \\itp\\waagent.conf mają zastosowanie również do wymaganego rozszerzenia maszyny Wirtualnej. Jeśli chcesz używać repozytoria Azure, upewnij się, że nie będzie ruch do tych repozytoria za pośrednictwem sieci intranet lokalnymi. Jeśli utworzono zdefiniowane przez użytkownika tras, aby włączyć tunelowanie wymuszone, upewnij się, że należy dodać trasę który kieruje ruchem repozytoria bezpośrednio do Internetu, a nie przez połączenie VPN lokacja lokacja.

* **SLES**

  Należy również dodać trasy dla adresów IP na liście \\itp\\regionserverclnt.cfg. Na poniższej ilustracji przedstawiono przykład:

  ![Wymuszone tunelowanie][deployment-guide-figure-50]


* **RHEL**

  Należy również dodać trasy dla hostów, na liście adresów IP \\itp\\yum.repos.d\\rhui usługi równoważenia obciążenia. Na przykład zobacz powyższej ilustracji.

* **Oracle Linux**

  Nie ma żadnych repozytoria Oracle Linux na platformie Azure. Należy skonfigurować własne repozytoria Oracle Linux lub użyj publicznego repozytoriów.

Aby uzyskać więcej informacji dotyczących tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłanie dalej IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Skonfiguruj rozszerzenie Azure monitorowania rozszerzonego dla programu SAP
Jeśli zostały przygotowane maszyny Wirtualnej zgodnie z opisem w [scenariuszy wdrażania maszyn wirtualnych dla programu SAP na platformie Azure][deployment-guide-3], Agent maszyny Wirtualnej Azure jest zainstalowany na maszynie wirtualnej. Następnym krokiem jest do wdrożenia usługi Azure rozszerzone monitorowanie rozszerzenie dla SAP, który jest dostępny w repozytorium Azure rozszerzenia w globalnej centrach danych platformy Azure. Aby uzyskać więcej informacji, zobacz [maszyny wirtualne Azure planowania i wdrażania dla programu SAP NetWeaver][planning-guide-9.1].

Aby zainstalować i skonfigurować Azure rozszerzone monitorowanie rozszerzenia dla programu SAP, można użyć programu PowerShell lub interfejsu wiersza polecenia Azure. Aby zainstalować rozszerzenie systemu Windows lub maszyny Wirtualnej systemu Linux przy użyciu komputera z systemem Windows, zobacz [programu Azure PowerShell][deployment-guide-4.5.1]. Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Linux przy użyciu pulpitu systemu Linux, zobacz [interfejsu wiersza polecenia Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Program Azure PowerShell dla systemu Linux i maszyn wirtualnych systemu Windows
Aby zainstalować Azure rozszerzone monitorowanie rozszerzenia dla programu SAP przy użyciu programu PowerShell:

1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [poleceń cmdlet wdrażania programu Azure PowerShell][deployment-guide-4.1].  
2. Uruchom następujące polecenie programu PowerShell.
    Aby uzyskać listę dostępnych środowisk, należy uruchomić `commandlet Get-AzureRmEnvironment`. Jeśli chcesz używać globalnego platformy Azure, środowisko jest **AzureCloud**. Dla platformy Azure w Chinach, wybierz **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po wprowadzeniu danych konta i zidentyfikować maszyny wirtualnej platformy Azure, skrypt wdraża wymaganego rozszerzenia i umożliwia wymaganych funkcji. Może to potrwać kilka minut.
Aby uzyskać więcej informacji na temat `Set-AzureRmVMAEMExtension`, zobacz [AzureRmVMAEMExtension zestaw][msdn-set-azurermvmaemextension].

![Pomyślne wykonanie specyficzne dla programu SAP Azure polecenia cmdlet Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

`Set-AzureRmVMAEMExtension` Konfiguracji jest wszystkie czynności, aby skonfigurować hosta monitorowania dla programu SAP.

Dane wyjściowe skryptu zawiera następujące informacje:

* Potwierdzenie, że monitorowanie dla dysku systemu operacyjnego i wszystkich dysków dodatkowych danych został skonfigurowany.
* Następne dwa komunikaty Potwierdź konfigurację magazynu metryki dla konta określonego magazynu.
* Jeden wiersz danych wyjściowych zawiera stan rzeczywistej aktualizacji konfiguracji monitorowania.
* Kolejny wiersz danych wyjściowych potwierdza, że konfiguracji została wdrożona lub zaktualizowana.
* Ostatni wiersz danych wyjściowych ma charakter informacyjny. Przedstawia on opcje testowania konfiguracji monitorowania.
* Aby sprawdzić, czy wszystkie kroki rozszerzonego monitorowania Azure zostały wykonane pomyślnie i czy infrastruktury platformy Azure udostępnia niezbędne dane, kontynuować sprawdzanie gotowości Azure rozszerzone monitorowanie rozszerzenia dla programu SAP, zgodnie z opisem w [sprawdzanie gotowości do rozszerzonego monitorowania Azure dla programu SAP][deployment-guide-5.1].
* Poczekaj 15 do 30 minut diagnostyki Azure do zbierania danych.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI dla maszyn wirtualnych systemu Linux
Aby zainstalować Azure rozszerzone monitorowanie rozszerzenia dla programu SAP przy użyciu wiersza polecenia platformy Azure:

1. Zainstaluj interfejs wiersza polecenia Azure 1.0, zgodnie z opisem w [zainstalować 1.0 interfejsu wiersza polecenia Azure][azure-cli].
2. Zaloguj się przy użyciu konta platformy Azure:

  ```
  azure login
  ```

3. Przełącz do trybu usługi Azure Resource Manager:

  ```
  azure config mode arm
  ```

4. Aby włączyć monitorowanie rozszerzonej platformy Azure:

  ```
  azure vm enable-aem <resource-group-name> <vm-name>
  ```

5. Sprawdź, czy Azure rozszerzone monitorowanie rozszerzenia jest aktywny w maszyny Wirtualnej systemu Linux platformy Azure. Sprawdź, czy plik \\var\\lib\\AzureEnhancedMonitor\\PerfCounters istnieje. Jeśli istnieje, w wierszu polecenia, uruchom to polecenie, aby wyświetlić informacje zebrane przez Monitor rozszerzone Azure:
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

Dane wyjściowe wyglądają następująco:
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
???
???
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Sprawdzanie i rozwiązywanie problemów dotyczących monitorowania end-to-end
Po wdrożeniu maszyny Wirtualnej platformy Azure i skonfigurować odpowiednie monitorowania infrastruktury platformy Azure, należy sprawdzić, czy wszystkie składniki Azure rozszerzone monitorowanie rozszerzenia działają zgodnie z oczekiwaniami.

Uruchom sprawdzanie gotowości Azure rozszerzone monitorowanie rozszerzenia dla programu SAP, zgodnie z opisem w [sprawdzanie gotowości Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-5.1]. Jeśli wszystkie wyniki sprawdzania gotowości są dodatnie OK są wyświetlane wszystkie liczniki wydajności istotne, Azure monitorowania nie został skonfigurowany pomyślnie. Można kontynuować instalację agenta hosta SAP, zgodnie z opisem w uwagach SAP w [zasobów SAP][deployment-guide-2.2]. Jeśli sprawdzanie gotowości wskazuje brak liczników, uruchom sprawdzanie kondycji dla monitorowania infrastruktury platformy Azure, zgodnie z opisem w [sprawdzenie kondycji dla konfiguracji monitorowania infrastruktury platformy Azure][deployment-guide-5.2]. Aby uzyskać więcej opcji rozwiązywania problemów, zobacz [monitorowania Rozwiązywanie problemów z Azure dla programu SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Sprawdzanie gotowości Azure rozszerzone monitorowanie rozszerzenia dla programu SAP
Ten test sprawdza, czy wszystkie metryki wydajności, które są wyświetlane w aplikacji SAP są dostarczane przez podstawowego monitorowania infrastruktury platformy Azure.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Uruchom sprawdzanie gotowości na maszynie Wirtualnej systemu Windows

1.  Zaloguj się do maszyny wirtualnej platformy Azure (przy użyciu konta administratora nie jest to konieczne).
2.  Otwórz okno wiersza polecenia.
3.  W wierszu polecenia Zmień katalog na folder instalacji Azure rozszerzone monitorowanie rozszerzenia dla programu SAP: C:\\pakiety\\wtyczek\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;wersji >\\upuść

  *Wersji* w ścieżce do rozszerzonego monitorowania może się różnić. Jeśli widzisz folderów dla różnych wersji rozszerzenia monitorowania w folderze instalacyjnym, sprawdź konfigurację usługi systemu AzureEnhancedMonitoring Windows, a następnie przejdź do folderu, który został oznaczony jako *ścieżka do pliku wykonywalnego*.

  ![Właściwości usługi systemem Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-figure-1000]

4.  W wierszu polecenia Uruchom **azperflib.exe** bez żadnych parametrów.

  > [!NOTE]
  > Azperflib.exe działa w pętli i aktualizuje zebranych liczników co 60 sekund. Aby zakończyć pętli, zamknij okno wiersza polecenia.
  >
  >

Jeśli Azure rozszerzone monitorowanie rozszerzenie nie jest zainstalowane lub nie działa usługa AzureEnhancedMonitoring, rozszerzenia ma nie został prawidłowo skonfigurowany. Aby uzyskać szczegółowe informacje o sposobie wdrażania rozszerzenia, zobacz [Rozwiązywanie problemów z infrastrukturą systemu Azure monitorowania dla programu SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Sprawdź dane wyjściowe azperflib.exe
Dane wyjściowe Azperflib.exe pokazano, że wszystkie wypełnione liczniki wydajności platformy Azure dla programu SAP. W dolnej części listy zebranych liczników wskaźnik Podsumowanie i kondycji stan monitorowania Azure.

![Sprawdzenie kondycji, wykonując azperflib.exe, co oznacza, że nie istnieją problemy z danych wyjściowych][deployment-guide-figure-1100]
<a name="figure-11"></a>

Sprawdź wynik zwracany dla **łącznie liczniki** dane wyjściowe, które jest raportowane jako pusty, a także **stan kondycji**, w tym przykładzie pokazano.

Interpretuj wyniki w następujący sposób:

| Wartości wyników Azperflib.exe | Stan kondycji monitorowania Azure |
| --- | --- |
| **Wywołania API — nie jest dostępna** | Liczniki, które nie są dostępne, może być albo nie dotyczy konfiguracji maszyny wirtualnej lub błędów. Zobacz **stan kondycji**. |
| **Łącznie liczniki — pusty** |Następujące dwa liczniki magazynu Azure może być pusta: <ul><li>Op opóźnienie odczytu magazynu serwera (MS)</li><li>Op opóźnienie odczytu magazynu E2E (MS)</li></ul>Wszystkie inne liczniki muszą mieć wartości. |
| **Stan kondycji** |Jeśli tylko OK zwracać pokazuje stan **OK**. |
| **Diagnostyka** |Szczegółowe informacje o stanie kondycji. |

Jeśli **stan kondycji** wartość nie jest **OK**, postępuj zgodnie z instrukcjami [sprawdzenie kondycji dla konfiguracji monitorowania infrastruktury platformy Azure][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Uruchom sprawdzanie gotowości na Maszynę wirtualną systemu Linux

1.  Podłącz maszynę wirtualną platformy Azure przy użyciu protokołu SSH.

2.  Sprawdź dane wyjściowe Azure rozszerzone monitorowanie rozszerzenia.

  a.  Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Oczekiwany wynik**: zwraca listę liczników wydajności. Plik nie powinien być pusty.

 b. Uruchom polecenie `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Oczekiwano wyniku**: zwraca jeden wiersz w przypadku błędu **Brak**, na przykład **3; konfiguracji; Błąd; 0; 0; Brak; 0; 1456416792; tst servercs;**

  c. Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

    **Oczekiwany wynik**: zwraca jako pusta lub nie istnieje.

Jeśli poprzednie wyboru zakończyła się niepowodzeniem, uruchom następujące dodatkowe kontrole:

1.  Upewnij się, że agenta waagent jest zainstalowany i włączony.

  a.  Uruchom polecenie `sudo ls -al /var/lib/waagent/`

      **Oczekiwany wynik**: Wyświetla zawartość katalogu agenta waagent.

  b.  Uruchom polecenie `ps -ax | grep waagent`

   **Oczekiwany wynik**: Wyświetla jednego wpisu podobnego do: `python /usr/sbin/waagent -daemon`

3.   Upewnij się, że Azure rozszerzone monitorowanie rozszerzenia jest zainstalowana i uruchomiona.

  a.  Uruchom polecenie `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

    **Oczekiwany wynik**: Wyświetla zawartość katalogu Azure rozszerzone monitorowanie rozszerzenia.

  b. Uruchom polecenie `ps -ax | grep AzureEnhanced`

     **Oczekiwany wynik**: Wyświetla jednego wpisu podobnego do: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

3. Zainstaluj agenta hosta SAP, zgodnie z opisem w Uwaga SAP [1031096]i sprawdź dane wyjściowe `saposcol`.

  a.  Uruchom polecenie `/usr/sap/hostctrl/exe/saposcol -d`

  b.  Uruchom polecenie `dump ccm`

  c.  Sprawdź, czy **Virtualization_Configuration\Enhanced monitorowania dostępu** Metryka to **true**.

Jeśli masz już zainstalowany serwer aplikacji SAP NetWeaver ABAP, otwórz transakcji ST06 i sprawdź, czy jest włączone monitorowanie rozszerzonej.

Jeśli żadnego z wymienionych testów zakończyć się niepowodzeniem i uzyskać szczegółowe informacje o tym, jak można wdrożyć ponownie rozszerzenia, zobacz [Rozwiązywanie problemów z infrastrukturą systemu Azure monitorowania dla programu SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Sprawdzanie kondycji dla konfiguracji monitorowania infrastruktury platformy Azure
Jeśli niektóre monitorowania danych nie jest poprawnie wskazany przez test opisany w dostarczyć [sprawdzanie gotowości do rozszerzonego monitorowania Azure dla programu SAP][deployment-guide-5.1]Uruchom `Test-AzureRmVMAEMExtension` polecenia cmdlet, aby sprawdzić, czy Azure Monitorowanie infrastruktury i monitorowania rozszerzenia dla programu SAP są poprawnie skonfigurowane.

1.  Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet programu Azure PowerShell, zgodnie z opisem w [poleceń cmdlet wdrażania programu Azure PowerShell][deployment-guide-4.1].
2.  Uruchom następujące polecenie programu PowerShell. Aby uzyskać listę dostępnych środowisk, należy uruchomić polecenie cmdlet `Get-AzureRmEnvironment`. Aby korzystać z globalnej Azure, wybierz **AzureCloud** środowiska. Dla platformy Azure w Chinach, wybierz **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Connect-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  Wprowadź dane konta i zidentyfikować maszyny wirtualnej platformy Azure.

  ![Wejściowy strony specyficzne dla programu SAP Azure polecenia cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

4. Skrypt testy konfiguracji maszyny wirtualnej, którą wybierzesz.

  ![Dane wyjściowe testu pomyślnego monitorowania infrastruktury platformy Azure dla programu SAP][deployment-guide-figure-1300]

Upewnij się, że każdy wynik sprawdzania kondycji jest **OK**. Jeśli niektóre testy nie są wyświetlane **OK**, uruchom polecenie cmdlet update zgodnie z opisem w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5]. Poczekaj 15 minut, a następnie powtórz kontroli opisanego w [sprawdzanie gotowości do rozszerzonego monitorowania Azure dla programu SAP] [ deployment-guide-5.1] i [sprawdzenie kondycji dla konfiguracji monitorowania infrastruktury Azure][deployment-guide-5.2]. Jeśli kontroli nadal wskazywać na problem z niektórych lub wszystkich liczników, zobacz [Rozwiązywanie problemów z infrastrukturą systemu Azure monitorowania dla programu SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Rozwiązywanie problemów z infrastrukturą systemu Azure monitorowania dla programu SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Liczniki wydajności usługi Azure nie są wyświetlane w ogóle
Usługa systemu AzureEnhancedMonitoring Windows zbiera metryki wydajności na platformie Azure. Jeśli usługa nie została poprawnie zainstalowana lub nie jest uruchomiona maszyna wirtualna nie metryki wydajności mogą być zbierane.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Katalog instalacyjny Azure rozszerzone monitorowanie rozszerzenia jest pusta

###### <a name="issue"></a>Problem
Katalog instalacyjny C:\\pakiety\\wtyczek\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;wersji >\\upuszczania jest pusta.

###### <a name="solution"></a>Rozwiązanie
Rozszerzenie nie jest zainstalowany. Ustal, czy jest to problem serwera proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera lub uruchom ponownie `Set-AzureRmVMAEMExtension` skryptu konfiguracji.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Usługa Azure rozszerzonego monitorowania nie istnieje.

###### <a name="issue"></a>Problem
Usługa systemu AzureEnhancedMonitoring Windows nie istnieje.

Dane wyjściowe Azperflib.exe zwraca błąd:

![Wykonanie azperflib.exe wskazuje, czy usługa Azure rozszerzone monitorowanie rozszerzenia dla programu SAP nie jest uruchomiona][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Rozwiązanie
Jeśli usługa nie istnieje, Azure rozszerzone monitorowanie rozszerzenia dla programu SAP ma nie został poprawnie zainstalowany. Należy ponownie wdrożyć rozszerzenia przy użyciu kroków opisanych dla danego scenariusza wdrażania w [scenariuszy wdrażania maszyn wirtualnych dla programu SAP na platformie Azure][deployment-guide-3].

Po wdrożeniu rozszerzenia, po upływie godziny, sprawdź ponownie czy liczniki wydajności usługi Azure znajdują się w maszynie Wirtualnej platformy Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Usługa Azure rozszerzonego monitorowania istnieje, ale nie można uruchomić

###### <a name="issue"></a>Problem
Usługa systemu AzureEnhancedMonitoring Windows istnieje i jest włączona, ale nie powiedzie się. Aby uzyskać więcej informacji Sprawdź dziennik zdarzeń aplikacji.

###### <a name="solution"></a>Rozwiązanie
Konfiguracja jest nieprawidłowa. Uruchom ponownie rozszerzonego monitorowania dla maszyny Wirtualnej, zgodnie z opisem w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Brak niektóre liczniki wydajności usługi Azure
Usługa systemu AzureEnhancedMonitoring Windows zbiera metryki wydajności na platformie Azure. Usługa pobiera dane z wielu źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z diagnostyki Azure. Liczniki magazynu są używane z Twojej rejestrowania na poziomie subskrypcji magazynu.

Jeśli Rozwiązywanie problemów przy użyciu Uwaga SAP [1999351] nie rozwiąże problemu, ponownie uruchom `Set-AzureRmVMAEMExtension` skryptu konfiguracji. Może być konieczne Zaczekaj godzinę, ponieważ liczniki analityka lub diagnostyki magazynu nie można tworzyć bezpośrednio po są włączone. Jeśli problem będzie się powtarzał, należy otworzyć SAP klienta obsługi wiadomości BC-OP-NT-AZR składnika dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej systemu Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Liczniki wydajności usługi Azure nie są wyświetlane w ogóle
Metryki wydajności na platformie Azure są zbierane przez demon. Jeśli demon nie działa, nie metryki wydajności mogą być zbierane.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Katalog instalacji rozszerzenia rozszerzonego monitorowania Azure jest pusty

###### <a name="issue"></a>Problem
Katalog \\var\\lib\\agenta waagent\\ nie ma podkatalogu dla rozszerzenia rozszerzonego monitorowania Azure.

###### <a name="solution"></a>Rozwiązanie
Rozszerzenie nie jest zainstalowany. Ustal, czy jest to problem serwera proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera i/lub uruchom ponownie `Set-AzureRmVMAEMExtension` skryptu konfiguracji.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Brak niektóre liczniki wydajności usługi Azure
Metryki wydajności na platformie Azure są zbierane przez demona, która pobiera dane z wielu źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z diagnostyki Azure. Liczniki magazynu pochodzą z dzienników w ramach subskrypcji magazynu.

Pełne i aktualne listę znanych problemów, zobacz Uwaga SAP [1999351], która zawiera dodatkowe informacje dotyczące rozwiązywania problemów do rozszerzonego monitorowania Azure dla programu SAP.

Jeśli Rozwiązywanie problemów przy użyciu Uwaga SAP [1999351] nie rozwiązać ten problem, ponownie uruchom `Set-AzureRmVMAEMExtension` skryptu konfiguracji zgodnie z opisem w [skonfigurowanie Azure rozszerzone monitorowanie rozszerzenia dla programu SAP][deployment-guide-4.5]. Może być konieczne Zaczekaj godzinę, ponieważ liczniki analityka lub diagnostyki magazynu nie można tworzyć bezpośrednio po są włączone. Jeśli problem będzie się powtarzał, należy otworzyć SAP klienta obsługi wiadomości BC-OP-NT-AZR składnika dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej systemu Linux.
