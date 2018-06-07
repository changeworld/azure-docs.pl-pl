---
title: Zainstaluj SAP NetWeaver HA klastra pracy awaryjnej systemu Windows i udostępnionego dysku dla wystąpienia programu SAP ASCS/SCS na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o instalowaniu programu SAP NetWeaver HA klastra pracy awaryjnej systemu Windows i udostępnionego dysku dla wystąpienia programu SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5d52653d68c6ebfca7e35a134da263eee99fd3e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657081"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Zainstaluj SAP NetWeaver HA klastra pracy awaryjnej systemu Windows i udostępnionego dysku dla wystąpienia programu SAP ASCS/SCS na platformie Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

W tym artykule opisano sposób instalowania i konfigurowania systemu SAP wysokiej dostępności na platformie Azure przy użyciu klastra trybu failover systemu Windows Server i klastra udostępnionego dysku dla klastra z wystąpieniem SAP ASCS/SCS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji, przejrzyj tych dokumentów:

* [Przewodnik dotyczący architektury: klastra SAP ASCS/SCS wystąpienia klastra trybu failover z systemem Windows za pomocą dysku udostępnionego klastra][sap-high-availability-guide-wsfc-shared-disk]

* [Przygotowanie infrastruktury platformy Azure dla programu SAP HA za pomocą klastra pracy awaryjnej systemu Windows i udostępnionego dysku dla wystąpienia programu SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Instalator systemu DBMS w tym artykule nie opisano konfiguracje się różnić w zależności od używanego systemu DBMS. Przyjęto założenie, reagowania problemów o wysokiej dostępności z systemu DBMS z funkcje obsługujące przez różnych dostawców DBMS dla platformy Azure. Przykłady są zawsze włączone lub dublowania bazy danych programu SQL Server i Oracle Data Guard dla baz danych Oracle. W tym scenariuszu używanych w tym artykule firma Microsoft nie należy dodawać więcej ochrony dla systemu DBMS.

Istnieją specjalne uwagi dotyczące różnych usług systemu DBMS interakcji z klastrowanym ASCS SAP lub SCS konfiguracji w usłudze Azure.

> [!NOTE]
> Procedury instalacji programu SAP NetWeaver ABAP systemów, Java i systemami ABAP + Java są prawie takie same. Najbardziej znacząca różnica jest systemu SAP ABAP jedno wystąpienie ASCS. Systemu SAP Java ma jedno wystąpienie SCS. Systemu SAP ABAP + Java ma jedno wystąpienie ASCS i jedno wystąpienie SCS uruchomiony w tej samej grupie klastra trybu failover firmy Microsoft. Różnice instalacji dla każdej stosu instalacji SAP NetWeaver jawnie wymienione. Można założyć, że wszystkie inne elementy są takie same.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalowanie programu SAP przy użyciu wystąpienia ASCS/SCS wysokiej dostępności

> [!IMPORTANT]
> Pamiętaj, aby nie umieszczenia pliku stronicowania na DataKeeper SIOS dublowanych woluminów. DataKeeper nie obsługuje woluminy dublowane. Możesz pozostawić pliku stronicowania na dysku tymczasowym D maszyny wirtualnej platformy Azure, co jest ustawieniem domyślnym. Jeśli nie jest już istnieje, należy przenieść plik stronicowania systemu Windows na dysku D z maszyny wirtualnej platformy Azure.
>
>

Instalowanie SAP przy użyciu wystąpienia ASCS/SCS wysokiej dostępności obejmuje następujące zadania:

* Utwórz nazwę hosta wirtualnego wystąpienia klastra programu SAP ASCS/SCS.
* Zainstaluj SAP pierwszym węźle klastra.
* Modyfikowanie profilu SAP ASCS/SCS wystąpienia.
* Dodaj port sondy.
* Otwórz port sondy zapory systemu Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Utwórz nazwę hosta wirtualnego wystąpienia klastra programu SAP ASCS/SCS

1.  W Menedżerze DNS z systemem Windows utwórz wpis DNS dla nazwy hostów wirtualnych ASCS/SCS wystąpienia.

  > [!IMPORTANT]
  > Adres IP, który można przypisać nazwę hosta wirtualnego wystąpienia ASCS/SCS musi być taka sama jak adres IP, który został przypisany do usługi równoważenia obciążenia Azure (\<SID\>ascs - lb).  
  >
  >

  Adres IP nazwę hosta wirtualnego SAP ASCS/SCS (pr1-ascs-sap) jest taki sam jak adres IP usługi równoważenia obciążenia Azure (pr1-lb ascs).

  ![Rysunek 1: Definiowanie wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adres TCP/IP][sap-ha-guide-figure-3046]

  _**Rysunek 1.** zdefiniować wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adres TCP/IP_

2.  Aby zdefiniować adres IP, który jest przypisany do nazwy hostów wirtualnych, wybierz **Menedżera DNS** > **domeny**.

  ![Rysunek 2: Konfiguracja klastra nową nazwę wirtualnego i adres TCP/IP dla SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Rysunek 2.** nową nazwę wirtualnego i TCP/IP adresów dla SAP ASCS/SCS konfiguracji klastra_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Zainstaluj na pierwszym węźle klastra SAP

1.  Wykonanie pierwszej opcji węzła klastra w węźle klastra A. Na przykład na pr1 ascs 0 * hosta.
2.  Zachowaj ustawienie domyślne portów dla usługi równoważenia obciążenia wewnętrznego platformy Azure, wybierz:

  * **ABAP system**: **ASCS** wystąpienie numer **00**
  * **Java system**: **SCS** wystąpienie numer **01**
  * **System ABAP + Java**: **ASCS** wystąpienie numer **00** i **SCS** wystąpienie numer **01**

  Aby użyć wystąpienia liczb innych niż 00 dla wystąpienia ABAP ASCS i 01 dla wystąpienia Java SCS, najpierw zmień reguły równoważenia obciążenia domyślnej usługi równoważenia obciążenia wewnętrznego platformy Azure. Aby uzyskać więcej informacji, zobacz [Zmień zasady dla usługi równoważenia obciążenia Azure wewnętrzny równoważenia obciążenia domyślne ASCS/SCS][sap-ha-guide-8.9].

Następny kilka zadań nie są opisane w standardowe dokumentacji instalacji SAP.

> [!NOTE]
> Dokumentacja instalacji programu SAP opisuje sposób instalowania pierwszego węzła klastra ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modyfikowanie profilu SAP wystąpienia ASCS/SCS

Najpierw Dodaj nowy parametr profilu. Parametr profilu zapobiega połączeń między procesów roboczych SAP i umieścić w kolejce zamykania po okresie bezczynności wynoszącym zbyt długo. Firma Microsoft wspomina scenariusz problemu w [dodać wpisów rejestru na obu węzłach klastra z wystąpieniem SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji możemy również wprowadzić dwie zmiany do niektórych podstawowych parametrów połączenia TCP/IP. W drugim kroku, należy określić serwer umieścić w kolejce do wysłania `keep_alive` sygnału, dzięki czemu połączeń nie osiągnął Próg bezczynności Azure wewnętrznego modułu równoważenia obciążenia.

Aby zmodyfikować profil SAP wystąpienia ASCS/SCS:

1.  Dodaj parametr ten profil, do profilu wystąpienia SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  W naszym przykładzie ścieżka jest:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Na przykład do profilu wystąpienia SAP SCS i odpowiednie ścieżki:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Aby zastosować zmiany, ponownie uruchom wystąpienie programu SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Dodaj port sondy

Funkcja równoważenia obciążenia wewnętrznego sondy do pracy z usługą równoważenia obciążenia w Azure konfigurację całego klastra. Azure wewnętrznego modułu równoważenia obciążenia zwykle rozdziela przychodzące obciążenie równomiernie w poszczególnych uczestniczących maszyn wirtualnych.

 Jednak to nie będzie działać w niektórych konfiguracjach klastra, ponieważ jest aktywna tylko jedno wystąpienie. Inne wystąpienie jest w stanie pasywnym i nie akceptuje dowolne obciążenia. Funkcja badania pomaga Azure wewnętrznego modułu równoważenia obciążenia przypisuje pracy tylko do aktywnego wystąpienia. Z funkcją sondowania wewnętrznego modułu równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie ograniczyć tylko wystąpienia o obciążenie.

Aby dodać port sondy:

1.  Sprawdź bieżący **ProbePort** wartość za pomocą następującego polecenia programu PowerShell:

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   Wykonaj polecenie z jednej maszyny wirtualnej w konfiguracji klastra.

2.  Zdefiniuj port sondy. Domyślny numer portu sondowania wynosi 0. W tym przykładzie używamy port sondy 62000.

  ![Rysunek 3: 0 domyślnie jest port sondy konfiguracji klastra][sap-ha-guide-figure-3048]

  _**Rysunek 3.** domyślny port sondy konfiguracji klastra jest równa 0_

  Numer portu jest zdefiniowany w szablonach SAP usługi Azure Resource Manager. Można przypisać numer portu w programie PowerShell.

  Aby ustawić nową wartość ProbePort dla SAP \<SID\> IP zasobu klastra, uruchom następujący skrypt programu PowerShell, aby zaktualizować zmienne środowiska PowerShell:

  ```PowerShell
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

  Po przełączeniu SAP \<SID\> klastra roli w tryb online, upewnij się, że **ProbePort** jest ustawiona na nową wartość.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  Po uruchomieniu skryptu, monit o ponowne uruchomienie Grupa klastra SAP do aktywowania zmian.

  ![Rysunek 4: Sondowania portu klastra po zainstalowaniu nowej wartości][sap-ha-guide-figure-3049]

  _**Rysunek 4:** sondowania portu klastra po zainstalowaniu nowej wartości_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otwórz port sondy zapory systemu Windows

Otwórz port sondy zapory systemu Windows na obu węzłów klastra. Użyj następującego skryptu, aby otworzyć port sondy zapory systemu Windows. Zaktualizuj zmienne środowiska PowerShell.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** ustawiono **62000**. Teraz, można uzyskać dostępu do udziału pliku \\\ascsha-clsap\sapmnt od innych hostów, na przykład wyświetlanie ascsha dbas.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Zainstaluj wystąpienie bazy danych

Aby zainstalować wystąpienie bazy danych, wykonaj proces opisany w dokumentacji instalacji SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Zainstaluj drugiego węzła klastra

Aby zainstalować drugi klaster, wykonaj kroki opisane w przewodniku instalacji SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Zmień typ uruchomienia wystąpienia usługi Windows Wywołujących SAP

Zmień typ uruchomienia usługi systemu Windows Wywołujących SAP **automatycznie (opóźnione uruchomienie)** na obu węzłów klastra.

![Rysunek 5: Zmień typ usługi dla wystąpienia Wywołujących SAP do opóźnionego automatycznego][sap-ha-guide-figure-3050]

_**Rysunek 5.** Zmień typ usługi wystąpienie Wywołujących SAP do opóźnionego automatycznego_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Zainstaluj serwer aplikacji głównej SAP

Zainstaluj wystąpienie serwera aplikacji głównej (adresy) \<SID\>-podpisane-0 na maszynie wirtualnej, który został wybrany do obsługi adresy dostawcy. Nie ma żadnych zależności na platformie Azure. Nie ma żadnych ustawień specyficznych dla DataKeeper.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalowanie serwera aplikacji dodatkowe SAP

Zainstaluj SAP dodatkowych aplikacji serwera (AAS) na wszystkich maszynach wirtualnych, które zostały wyznaczone do obsługi wystąpienia serwera aplikacji SAP. Na przykład na \<identyfikatora SID\>-podpisane-1, aby \<identyfikatora SID\>- podpisane -&lt;n&gt;.

> [!NOTE]
> Zakończy się instalacja systemu SAP NetWeaver wysokiej dostępności. Następnie należy kontynuować testowanie trybu failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testowanie trybu failover wystąpienia programu SAP ASCS/SCS i SIOS replikacji
Jest łatwy do testowania i monitorowania za pomocą narzędzia Menedżera klastra trybu Failover oraz zarządzania DataKeeper SIOS i konfiguracji trybu failover wystąpienia programu SAP ASCS/SCS i SIOS replikacji dysku.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> W węźle klastra, A jest uruchomione wystąpienie SAP ASCS/SCS

Grupa klastra SAP PR1 jest uruchomiona w węźle klastra A. Na przykład na pr1-ascs-0. Przypisywanie udostępnionego dysku S, który jest częścią grupy klastra SAP PR1, do węzła klastra A. Wystąpienie ASCS/SCS również używa dysku S. 

![Rysunek 6: Menedżer klastra trybu Failover: SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra, A][sap-ha-guide-figure-5000]

_**Rysunek 6.** Menedżera klastra trybu Failover: SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra, A_

W narzędziu Zarządzanie DataKeeper SIOS i jego konfiguracja widać, że udostępniony dysk synchronicznie replikacji danych z dysku woluminu źródłowego S w węźle klastra, A na dysku woluminu docelowego S w węźle klastra B. Na przykład jest replikowany z pr1 ascs 0 [10.0.0.40] do [10.0.0.41] pr1-ascs-1.

![Rysunek 7: W SIOS DataKeeper replikowane lokalnym woluminie z węzła klastra, A do węzła klastra B][sap-ha-guide-figure-5001]

_**Rysunek 7.** w SIOS DataKeeper replikowane lokalnym woluminie z węzła klastra, A do węzła klastra B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Tryb failover z węzła A węzła B

1.  Wybierz jedną z tych opcji, aby zainicjować trybu failover SAP \<SID\> Grupa klastra z węzła klastra, A do węzła klastra B:
  - Menedżer klastra trybu failover  
  - Klaster trybu failover programu PowerShell

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Uruchom ponownie, A węzeł klastra w ramach systemu operacyjnego gościa. Powoduje to zainicjowanie automatycznej pracy awaryjnej SAP \<SID\> Grupa klastra z węzła A w węźle B.  
3.  Uruchom ponownie, A węzeł klastra przy użyciu portalu Azure. Powoduje to zainicjowanie automatycznej pracy awaryjnej SAP \<SID\> Grupa klastra z węzła A w węźle B.  
4.  Uruchom ponownie A węzła klastra za pomocą programu Azure PowerShell. Powoduje to zainicjowanie automatycznej pracy awaryjnej SAP \<SID\> Grupa klastra z węzła A w węźle B.

  Po przejściu w tryb failover, SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra B. Na przykład że jest uruchomiona w pr1-ascs-1.

  ![Rysunek 8: W Menedżerze klastra trybu Failover, SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra B][sap-ha-guide-figure-5002]

  _**Rysunek 8**: W Menedżerze klastra trybu Failover, SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra B_

  Udostępniony dysk jest teraz zainstalowany w klastrze węzła B. SIOS DataKeeper jest replikowanie danych z dysku woluminu źródłowego S w węźle klastra B na dysku woluminu docelowego S w węźle klastra A. Na przykład, że replikacja odbywa się z pr1 ascs 1 [10.0.0.41] pr1 ascs 0 [10.0.0.40].

  ![Rysunek 9: SIOS DataKeeper replikuje lokalnym woluminie z węzła klastra B do A węzła klastra][sap-ha-guide-figure-5003]

  _**Rysunek 9:** SIOS DataKeeper replikuje lokalnym woluminie z węzła klastra B do A węzła klastra_
