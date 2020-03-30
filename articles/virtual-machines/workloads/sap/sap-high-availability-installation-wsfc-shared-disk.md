---
title: Instalowanie usługi SAP NetWeaver HA w klastrze trybu failover systemu Windows i dysku współdzielonym dla wystąpienia SAP ASCS/SCS na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować usługę SAP NetWeaver HA w klastrze trybu failover systemu Windows i dysku współdzielonym dla wystąpienia SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e50733c843dfd21e35572f00fc6690e1e84aba97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279834"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instalowanie usługi SAP NetWeaver HA w klastrze trybu failover systemu Windows i dysku współdzielonym dla wystąpienia SAP ASCS/SCS na platformie Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

W tym artykule opisano sposób instalowania i konfigurowania systemu SAP o wysokiej dostępności na platformie Azure przy użyciu klastra trybu failover systemu Windows Server i udostępnionego dysku klastra do klastrowania wystąpienia SAP ASCS/SCS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji zapoznaj się z tymi dokumentami:

* [Przewodnik po architekturze: klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk]

* [Przygotowywanie infrastruktury platformy Azure dla usługi SAP HA przy użyciu klastra trybu failover systemu Windows i udostępnionego dysku dla wystąpienia SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Nie opisujemy konfiguracji systemu dbms w tym artykule, ponieważ konfiguracje różnią się w zależności od używanego systemu DBMS. Zakładamy, że problemy z wysoką dostępnością systemu dbms są rozwiązywane z funkcjami, które obsługują różnych dostawców dbms dla platformy Azure. Przykładami są AlwaysOn lub dublowanie bazy danych dla sql server i Oracle Data Guard dla baz danych Oracle. W scenariuszu, którego używamy w tym artykule, nie dodawaj więcej ochrony do dbms.

Nie ma żadnych specjalnych zagadnień, gdy różne usługi DBMS współdziałają z klastrową konfiguracją SAP ASCS lub SCS na platformie Azure.

> [!NOTE]
> Procedury instalacji systemów SAP NetWeaver ABAP, Systemów Java i ABAP+Java są niemal identyczne. Najważniejszą różnicą jest to, że system SAP ABAP ma jedno wystąpienie ASCS. System SAP Java ma jedno wystąpienie SCS. System SAP ABAP+Java ma jedno wystąpienie ASCS i jedno wystąpienie SCS uruchomione w tej samej grupie klastrów trybu failover firmy Microsoft. Wszelkie różnice w instalacji dla każdego stosu instalacji SAP NetWeaver są wyraźnie wymienione. Można założyć, że wszystkie pozostałe części są takie same.  
>
>

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalowanie systemu SAP z wystąpieniem ASCS/SCS o wysokiej dostępności

> [!IMPORTANT]
> Pamiętaj, aby nie umieszczać pliku stronicowego na woluminach dublowanych SIOS DataKeeper. DataKeeper nie obsługuje woluminów dublowanych. Możesz pozostawić plik strony na dysku tymczasowym D maszyny wirtualnej platformy Azure, która jest domyślna. Jeśli jeszcze go tam nie ma, przenieś plik strony systemu Windows, aby prowadzić D maszyny wirtualnej platformy Azure.
>
>

Instalowanie sap z wysokiej dostępności ASCS/SCS wystąpienie obejmuje następujące zadania:

* Utwórz nazwę hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS.
* Zainstaluj pierwszy węzeł klastra SAP.
* Zmodyfikuj profil SAP wystąpienia ASCS/SCS.
* Dodaj port sondy.
* Otwórz port sondy zapory systemu Windows.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS

1. W Menedżerze DNS systemu Windows utwórz wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS.

   > [!IMPORTANT]
   > Adres IP przypisany do nazwy hosta wirtualnego wystąpienia ASCS/SCS musi być taki sam jak adres\<IP\>przypisany do modułu Równoważenia obciążenia platformy Azure ( SID -lb-ascs).  
   >
   >

   Adres IP nazwy hosta wirtualnego SAP ASCS/SCS (pr1-ascs-sap) jest taki sam jak adres IP modułu Równoważenia obciążenia platformy Azure (pr1-lb-ascs).

   ![Rysunek 1: Definiowanie wpisu DNS dla nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Rysunek 1:** Definiowanie wpisu DNS dla nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP_

2. Aby zdefiniować adres IP przypisany do nazwy hosta wirtualnego, wybierz pozycję**Domena** **Menedżera** > DNS .

   ![Rysunek 2: Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Rysunek 2:** Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalowanie pierwszego węzła klastra SAP

1. Wykonaj pierwszą opcję węzła klastra w węźle klastra A. Na przykład na hostie pr1-ascs-0*.
2. Aby zachować domyślne porty wewnętrznego modułu równoważenia obciążenia platformy Azure, wybierz:

   * **System ABAP**: numer wystąpienia **ASCS** **00**
   * **System Java**: numer wystąpienia **SCS** **01**
   * **System ABAP+Java:** numer wystąpienia **ASCS** **00** i numer wystąpienia **SCS** **01**

   Aby użyć numerów wystąpień innych niż 00 dla wystąpienia ABAP ASCS i 01 dla wystąpienia Java SCS, najpierw zmień domyślne reguły równoważenia obciążenia modułu azure. Aby uzyskać więcej informacji, zobacz [Zmienianie domyślnych reguł równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-8.9].

Kilka następnych zadań nie jest opisanych w standardowej dokumentacji instalacji SAP.

> [!NOTE]
> W dokumentacji instalacji sap opisano sposób instalowania pierwszego węzła klastra ASCS/SCS.
>
>

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modyfikowanie profilu SAP wystąpienia ASCS/SCS

Najpierw dodaj nowy parametr profilu. Parametr profilu zapobiega zamykaniu połączeń między procesami roboczymi SAP a serwerem w kolejce, gdy są one bezczynne przez zbyt długi czas. Wspominamy o scenariuszu problemu w [Dodaj wpisy rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji wprowadzamy również dwie zmiany w niektórych podstawowych parametrach połączenia TCP/IP. W drugim kroku należy ustawić serwer enqueue, `keep_alive` aby wysłać sygnał, tak aby połączenia nie osiągnęły progu bezczynności modułu równoważenia obciążenia platformy Azure.

Aby zmodyfikować profil SAP wystąpienia ASCS/SCS:

1. Dodaj ten parametr profilu do profilu wystąpienia SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   W naszym przykładzie ścieżka jest:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Na przykład do profilu wystąpienia SAP SCS i odpowiedniej ścieżki:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Aby zastosować zmiany, uruchom ponownie wystąpienie SAP ASCS/SCS.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Dodawanie portu sondy

Użyj funkcji sondy modułu wewnętrznego modułu równoważenia obciążenia, aby cała konfiguracja klastra działała z modułem równoważenia obciążenia platformy Azure. Wewnętrzny moduł równoważenia obciążenia platformy Azure zwykle rozdziela przychodzące obciążenie równo między uczestniczące maszyny wirtualne.

 Jednak to nie będzie działać w niektórych konfiguracjach klastra, ponieważ tylko jedno wystąpienie jest aktywne. Inne wystąpienie jest pasywne i nie można zaakceptować żadnego obciążenia. Funkcja sondy pomaga, gdy wewnętrzny moduł równoważenia obciążenia platformy Azure przypisuje pracę tylko do aktywnego wystąpienia. Dzięki funkcji sondy wewnętrzny moduł równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie kierować tylko wystąpienie z obciążeniem.

Aby dodać port sondy:

1. Sprawdź bieżącą wartość **ProbePort,** uruchamiając następujące polecenie programu PowerShell:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Wykonaj polecenie z poziomu jednej z maszyn wirtualnych w konfiguracji klastra.

2. Zdefiniuj port sondy. Domyślny numer portu sondy to 0. W naszym przykładzie używamy portu sondy 62000.

   ![Rysunek 3: Port sondy konfiguracji klastra jest domyślnie 0][sap-ha-guide-figure-3048]

   _**Rysunek 3:** Domyślny port sondy konfiguracji klastra to 0_

   Numer portu jest zdefiniowany w szablonach usługi SAP Azure Resource Manager. Numer portu można przypisać w programie PowerShell.

   Aby ustawić nową wartość ProbePort \<\> dla zasobu klastra IP identyfikatora SAP SID, uruchom następujący skrypt programu PowerShell, aby zaktualizować zmienne programu PowerShell dla twojego środowiska:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

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

   Po przesunieniu roli klastra IDENTYFIKATORÓW SID \<\> SAP w tryb online sprawdź, czy **probeport** jest ustawiony na nową wartość.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Po uruchomieniu skryptu zostanie wyświetlony monit o ponowne uruchomienie grupy klastra SAP w celu aktywowania zmian.

   ![Rysunek 4: Sondowanie portu klastra po ustawieniu nowej wartości][sap-ha-guide-figure-3049]

   _**Rysunek 4:** Sondowanie portu klastra po ustawieniu nowej wartości_

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Otwieranie portu sondy zapory systemu Windows

Otwórz port sondy zapory systemu Windows w obu węzłach klastra. Użyj następującego skryptu, aby otworzyć port sondy zapory systemu Windows. Zaktualizuj zmienne programu PowerShell dla swojego środowiska.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** jest ustawiony na **62000**. Teraz można uzyskać dostęp \\do udziału plików \ascsha-clsap\sapmnt z innych hostów, takich jak ascsha-dbas.

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalowanie wystąpienia bazy danych

Aby zainstalować wystąpienie bazy danych, postępuj zgodnie z procesem opisanym w dokumentacji instalacji SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalowanie drugiego węzła klastra

Aby zainstalować drugi klaster, wykonaj kroki opisane w przewodniku instalacji SAP.

## <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Zmienianie typu początkowego wystąpienia usługi SAP ERS Windows

Zmień typ początkowy usługi SAP ERS Windows na **Automatyczny (opóźniony start)** w obu węzłach klastra.

![Rysunek 5: Zmień typ usługi dla wystąpienia SAP ERS na opóźnione automatyczne][sap-ha-guide-figure-3050]

_**Rysunek 5:** Zmień typ usługi dla wystąpienia SAP ERS na opóźnione automatyczne_

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalowanie podstawowego serwera aplikacji SAP

Zainstaluj identyfikator SID \<\>-di-0 wystąpienia serwera aplikacji podstawowej (PAS) na maszynie wirtualnej wyznaczonej do obsługi pasu. Nie ma żadnych zależności na platformie Azure. Nie ma żadnych ustawień specyficznych dla datakeepera.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalowanie dodatkowego serwera aplikacji SAP

Zainstaluj serwer aplikacji dodatkowej SAP (AAS) na wszystkich maszynach wirtualnych wyznaczonych do obsługi wystąpienia serwera aplikacji SAP. Na przykład \<na\>SID -di-1 do \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> To kończy instalację systemu SAP NetWeaver o wysokiej dostępności. Następnie należy przystąpić do testowania trybu failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testowanie trybu failover wystąpienia SAP ASCS/SCS i replikacji SIOS
Testowanie i monitorowanie trybu failover wystąpienia SAP ASCS/SCS i replikacji dysku SIOS można łatwo przetestować i monitorować za pomocą Menedżera klastra trybu failover oraz narzędzia SIOS DataKeeper Management and Configuration.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Wystąpienie SAP ASCS/SCS jest uruchomione w węźle klastra A

Grupa klastra SAP PR1 jest uruchomiona w węźle klastra A. Na przykład na pr1-ascs-0. Przypisz współdzielony dysk S, który jest częścią grupy klastra SAP PR1, do węzła klastra A. Wystąpienie ASCS/SCS używa również dysku S. 

![Rysunek 6: Menedżer klastra \<\> trybu failover: Grupa klastra SID SAP jest uruchomiona w węźle klastra A][sap-ha-guide-figure-5000]

_**Rysunek 6:** Menedżer klastrów trybu \<failover: Grupa klastrów SAP SID\> działa w węźle klastra A_

W narzędziu SIOS DataKeeper Zarządzanie i konfiguracja można zobaczyć, że udostępnione dane dysku są synchronicznie replikowane z dysku zbiorczego źródłowego S w węźle klastra A do docelowego dysku woluminu S w węźle klastra B. Na przykład jest replikowany z pr1-ascs-0 [10.0.0.40] do pr1-ascs-1 [10.0.0.41].

![Rysunek 7: W sios datakeeper replikuj wolumin lokalny z węzła klastra A do węzła klastra B][sap-ha-guide-figure-5001]

_**Rysunek 7:** W aplikacji SIOS DataKeeper replikuj wolumin lokalny z węzła klastra A do węzła klastra B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Przewijaniu w sposób failover z węzła A do węzła B

1. Wybierz jedną z tych opcji, aby zainicjować\> przejście awaryjne grupy klastra SID SAP \<z węzła klastra A do węzła klastra B:
   - Menedżer klastra trybu failover  
   - Program PowerShell klastra trybu failover

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Uruchom ponownie węzeł klastra A w systemie operacyjnym windows guest. Inicjuje to automatyczną \<tryb\> failover grupy klastrów IDENTYFIKATORÓW SID SAP z węzła A do węzła B.  
3. Uruchom ponownie węzeł A klastra z witryny Azure portal. Inicjuje to automatyczną \<tryb\> failover grupy klastrów IDENTYFIKATORÓW SID SAP z węzła A do węzła B.  
4. Uruchom ponownie węzeł klastra A przy użyciu programu Azure PowerShell. Inicjuje to automatyczną \<tryb\> failover grupy klastrów IDENTYFIKATORÓW SID SAP z węzła A do węzła B.

   Po przemienniu \<\> awaryjnym grupa klastra IDENTYFIKATORÓW SID SAP jest uruchomiona w węźle klastra B. Na przykład działa na pr1-ascs-1.

   ![Rysunek 8: W Menedżerze \<klastrów trybu failover grupa klastra SAP SID\> jest uruchomiona w węźle klastra B][sap-ha-guide-figure-5002]

   _**Rysunek 8:** W Menedżerze \<klastrów trybu failover grupa klastra SAP SID\> jest uruchomiona w węźle klastra B_

   Dysk udostępniony jest teraz zamontowany w węźle klastra B. SIOS DataKeeper replikuje dane z dysku woluminu źródłowego S w węźle klastra B do docelowego dysku woluminu S w węźle klastra A. Na przykład replikuje się z pr1-ascs-1 [10.0.0.41] do pr1-ascs-0 [10.0.0.40].

   ![Rysunek 9: Moduł danych SIOS replikuje wolumin lokalny z węzła klastra B do węzła klastra A][sap-ha-guide-figure-5003]

   _**Rysunek 9:** Moduł danych SIOS replikuje wolumin lokalny z węzła klastra B do węzła klastra A_
