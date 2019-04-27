---
title: Zainstaluj oprogramowanie SAP NetWeaver zaświadczanie o kondycji klastra pracy awaryjnej Windows i udostępnionym dyskiem na potrzeby wystąpienia SAP ASCS/SCS na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować oprogramowanie SAP NetWeaver zaświadczanie o kondycji klastra pracy awaryjnej Windows i udostępnionym dyskiem na potrzeby wystąpienia SAP ASCS/SCS.
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
ms.openlocfilehash: 1229b7f9e2a430a663a3e78bb457c03cf4a4a590
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714422"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Zainstaluj oprogramowanie SAP NetWeaver zaświadczanie o kondycji klastra pracy awaryjnej Windows i udostępnionym dyskiem na potrzeby wystąpienia SAP ASCS/SCS na platformie Azure

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

W tym artykule opisano, jak zainstalować i skonfigurować system SAP o wysokiej dostępności na platformie Azure przy użyciu klastra trybu failover systemu Windows Server oraz udostępniony dysk klastra dla klastra wystąpienie SAP ASCS/SCS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji, przejrzyj następujące dokumenty:

* [Przewodnik dotyczący architektury: Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk]

* [Przygotowanie infrastruktury platformy Azure do SAP wysokiej dostępności przy użyciu klastra pracy awaryjnej Windows i udostępnionego dysku dla wystąpienia usługi SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Instalator systemu DBMS, w tym artykule nie opisano konfiguracje różnić się w zależności od używanego systemu DBMS. Przyjęto założenie, że problemów o wysokiej dostępności z systemu DBMS są adresowane za pomocą funkcji, obsługujące przez różnych dostawców DBMS na platformie Azure. Przykładami są AlwaysOn lub dublowania bazy danych SQL Server i Oracle Data Guard dla baz danych Oracle. W tym scenariuszu, których używamy w tym artykule firma Microsoft nie dodawaj zwiększa ochronę dla systemu DBMS.

Istnieją uwzględniać żadnych szczególnych kwestii dotyczące różnych usług systemu DBMS na interakcję z klastrowanego SAP ASCS lub SCS konfiguracji na platformie Azure.

> [!NOTE]
> Procedury instalacji systemów SAP NetWeaver ABAP, Java i systemów ABAP + Java są niemal identyczne. Najbardziej znaczący różnica polega na tym, że system SAP ABAP zawiera jedno wystąpienie ASCS. System SAP języka Java zawiera jedno wystąpienie usług SCS. System SAP ABAP + Java zawiera jedno wystąpienie ASCS i jedno wystąpienie usług SCS uruchomione w tej samej grupie klastra trybu failover firmy Microsoft. Różnice instalacji dla poszczególnych stosów instalacji oprogramowania SAP NetWeaver jawnie wymienione. Można zakładać, że inne części są takie same.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalowanie SAP za pomocą wystąpienia ASCS/SCS wysokiej dostępności

> [!IMPORTANT]
> Pamiętaj, aby nie umieszczenia pliku stronicowania na oprogramowanie SIOS DataKeeper dublowanych woluminów. DataKeeper nie obsługuje woluminy dublowane. Można pozostawić pliku stronicowania na tymczasowym dysku D maszynie wirtualnej platformy Azure, co jest ustawieniem domyślnym. Jeśli nie jest już istnieje, należy przenieść plik stronicowania Windows na dysku D maszyn wirtualnych.
>
>

Instalowanie SAP za pomocą wystąpienia ASCS/SCS wysokiej dostępności obejmuje następujące zadania:

* Utwórz nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS.
* Zainstaluj oprogramowanie SAP pierwszym węźle klastra.
* Modyfikowanie profilu SAP ASCS/SCS wystąpienia.
* Dodaj port sondy.
* Otwórz port sondy zapory Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Utwórz nazwę hosta wirtualnego klastrowanego wystąpienia SAP ASCS/SCS

1. W Menedżerze Windows DNS utworzyć wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS.

   > [!IMPORTANT]
   > Adres IP, który zostanie przypisany do nazwy hosta wirtualnego wystąpienia ASCS/SCS musi być taka sama, jak adres IP, która została przypisana do usługi Azure Load Balancer (\<SID\>ascs - lb).  
   >
   >

   Adres IP wirtualnych SAP ASCS/SCS nazwy hosta (pr1-ascs sap) jest taki sam jak adres IP modułu równoważenia obciążenia platformy Azure (pr1-lb-ascs).

   ![Rysunek 1: Zdefiniuj wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Rysunek 1:** Zdefiniuj wpis DNS dla nazwy wirtualnego klastra SAP ASCS/SCS i adresu TCP/IP_

2. Aby zdefiniować adres IP, który jest przypisany do nazwy hostów wirtualnych, wybierz **Menedżera DNS** > **domeny**.

   ![Rysunek 2: Nowa nazwa wirtualnego i adresu TCP/IP dla konfiguracji klastra SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Rysunek 2:** Nowa nazwa wirtualnego i adresu TCP/IP dla konfiguracji klastra SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Zainstaluj oprogramowanie SAP pierwszym węźle klastra

1. Wykonanie pierwszej opcji węzła klastra w węźle klastra A. Na przykład na pr1 ascs 0 * hosta.
2. Aby pozostawić domyślne porty dla platformy Azure wewnętrznego modułu równoważenia obciążenia, wybierz:

   * **ABAP system**: **ASCS** numer wystąpienia **00**
   * **Java system**: **SCS** numer wystąpienia **01**
   * **System ABAP + Java**: **ASCS** numer wystąpienia **00** i **SCS** numer wystąpienia **01**

   Aby użyć wystąpienia liczb innych niż 00 wystąpienie ABAP ASCS i 01 dla wystąpienia Java SCS, najpierw należy zmienić reguły równoważenia obciążenia domyślnego modułu równoważenia obciążenia wewnętrznego platformy Azure. Aby uzyskać więcej informacji, zobacz [zmiany reguł dla modułu równoważenia obciążenia platformy Azure wewnętrznego równoważenia obciążenia domyślne ASCS/SCS][sap-ha-guide-8.9].

Dalej kilka zadań nie są opisane w standardowych dokumentacji instalacji SAP.

> [!NOTE]
> Dokumentacji instalacji SAP w tym artykule opisano sposób instalowania pierwszego węzła klastra ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modyfikowanie profilu SAP ASCS/SCS wystąpienia

Najpierw Dodaj nowy parametr profilu. Parametr profilu uniemożliwia zamknięcie, gdy są one bezczynne zbyt długo połączenia między procesów roboczych SAP i serwerem umieścić w kolejce. Firma Microsoft wspomina o identyfikatorach scenariusz problemu w [dodania wpisów rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji możemy również wprowadza dwie zmiany analizy niektóre podstawowe parametry połączenia TCP/IP. W drugim kroku, należy ustawić serwera umieścić w kolejce do wysłania `keep_alive` sygnał, tak aby połączenia nie trafień Próg bezczynności modułu równoważenia obciążenia wewnętrznego platformy Azure.

Aby zmodyfikować profil SAP ASCS/SCS wystąpienie:

1. Dodaj parametr ten profil do profilu wystąpienia SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   W naszym przykładzie ścieżka jest:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Na przykład aby profil wystąpienia SAP SCS i odpowiednie ścieżki:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Aby zastosować zmiany, ponownie uruchom wystąpienie SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Dodaj port sondy

Przy użyciu funkcji sondy modułu równoważenia obciążenia wewnętrznego konfigurację cały klaster pracy za pomocą usługi Azure Load Balancer. Azure wewnętrznego modułu równoważenia obciążenia dystrybuuje zwykle przychodzące obciążenie równo między uczestniczących w programie maszyn wirtualnych.

 Jednak to nie będzie działać w niektórych konfiguracjach klastra, ponieważ tylko jedno wystąpienie jest aktywny. Inne wystąpienie jest w stanie pasywnym i nie akceptuje dowolne obciążenie. Funkcja sondy pomaga w Azure wewnętrznego modułu równoważenia obciążenia przypisuje pracy tylko do aktywnego wystąpienia. Dzięki funkcji sondy wewnętrznego modułu równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie wskazać tylko wystąpienia z obciążeniem.

Aby dodać port sondy:

1. Sprawdź bieżące **ProbePort** wartość, uruchamiając następujące polecenie programu PowerShell:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Wykonaj polecenie z jednej z maszyn wirtualnych w konfiguracji klastra.

2. Zdefiniuj port sondy. Domyślny numer portu sondowania wynosi 0. W naszym przykładzie używamy port sondy 62000.

   ![Rysunek 3: Port sondy konfiguracji klastra jest równa 0, domyślnie][sap-ha-guide-figure-3048]

   _**Rysunek 3:** Domyślny port sondy konfiguracji klastra ma wartość 0_

   Numer portu jest zdefiniowana w systemie SAP usługi Azure Resource Manager. Można przypisać numer portu w programie PowerShell.

   Aby ustawić nową wartość ProbePort SAP \<SID\> adresu IP zasobu klastra, uruchom następujący skrypt programu PowerShell w celu aktualizowania zmiennych środowiska PowerShell dla danego środowiska:

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

   Po przekazaniu SAP \<SID\> klastra roli w tryb online, upewnij się, że **ProbePort** ustawiono nową wartość.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Po uruchomieniu skryptu, wyświetlany jest monit o ponowne uruchomienie Grupa klastra SAP do aktywowania zmian.

   ![Rysunek 4: Sonda portu klastra, po ustawieniu nowej wartości][sap-ha-guide-figure-3049]

   _**Rysunek 4:** Sonda portu klastra, po ustawieniu nowej wartości_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otwórz port sondy zapory Windows

Otwórz port sondy zapory Windows na obu węzłach klastra. Użyj następującego skryptu, aby otworzyć port sondy zapory Windows. Zaktualizuj zmienne programu PowerShell dla danego środowiska.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** ustawiono **62000**. Teraz, możesz uzyskać dostęp do udziału plików \\\ascsha-clsap\sapmnt od innych hostów, np. informacji od przetwarzający ascsha.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Zainstaluj wystąpienie bazy danych

Aby zainstalować wystąpienie bazy danych, wykonaj proces opisany w dokumentacji instalacji SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Zainstaluj na drugim węźle klastra

Aby zainstalować drugiego klastra, wykonaj kroki, które są opisane w przewodniku instalacji SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Zmień typ uruchomienia wystąpienia usługi Windows Wywołujących SAP

Zmień typ uruchomienia usługi Windows Wywołujących SAP **automatycznie (opóźnione uruchomienie)** na obu węzłach klastra.

![Rysunek 5: Zmień typ usługi dla wystąpienia SAP Wywołujących opóźnione automatycznie][sap-ha-guide-figure-3050]

_**Rysunek 5:** Zmień typ usługi dla wystąpienia SAP Wywołujących opóźnione automatycznie_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalowanie serwera podstawowego aplikacji SAP

Zainstaluj wystąpienie serwera aplikacji głównej (PAS) \<SID\>-di-0 na maszynie wirtualnej, który został wybrany do hostowania PAS. Czy nie występują żadne zależności na platformie Azure. Nie ma żadnych ustawień specyficznych dla DataKeeper.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalowanie serwera dodatkowych aplikacji SAP

Zainstaluj oprogramowanie SAP dodatkowych aplikacji serwera (AAS) na wszystkich maszynach wirtualnych, które zostały oznaczone do hostowania wystąpienia serwera aplikacji SAP. Na przykład na \<SID\>-di-1, aby \<SID\>- di -&lt;n&gt;.

> [!NOTE]
> Spowoduje to zakończenie instalacji systemu SAP NetWeaver wysokiej dostępności. Następnie należy kontynuować testowanie trybu failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testowanie trybu failover wystąpienia SAP ASCS/SCS i oprogramowanie SIOS replikacji
Jest łatwy do testowania i monitorowania usługi SAP ASCS/SCS wystąpienia w tryb failover i oprogramowanie SIOS replikacji dysku przy użyciu narzędzia Menedżera klastra trybu Failover i oprogramowanie SIOS DataKeeper zarządzania i konfiguracji.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Wystąpienia SAP ASCS/SCS jest uruchomiona w węźle klastra, A

Grupa klastra SAP PR1 jest uruchomiona w węźle klastra A. Na przykład na pr1-ascs-0. Przypisywanie udostępnionego dysku S, która jest częścią grupy klastra SAP PR1, do węzła klastra A. Wystąpienie ASCS/SCS również używa dysku S. 

![Rysunek 6: Menedżer klastra trybu failover: SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra, A][sap-ha-guide-figure-5000]

_**Rysunek 6:** Menedżer klastra trybu failover: SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra, A_

W narzędziu oprogramowanie SIOS DataKeeper zarządzania i konfiguracji możesz zobaczyć, czy udostępniony dysk synchronicznie replikacji danych z dysku woluminu źródłowego S w węźle klastra, A na dysku woluminu docelowego S w węźle klastra B. Na przykład jest replikowany z pr1 ascs 0 [10.0.0.40] do [10.0.0.41] pr1-ascs-1.

![Rysunek 7: W oprogramowanie SIOS DataKeeper replikowania woluminu lokalnego z węzła klastra, A do węzła klastra B][sap-ha-guide-figure-5001]

_**Rysunek 7:** W oprogramowanie SIOS DataKeeper replikowania woluminu lokalnego z węzła klastra, A do węzła klastra B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Tryb failover z węzła A węźle B

1. Wybierz jedną z tych opcji, aby zainicjować trybu failover SAP \<SID\> Grupa klastra z węzła klastra, A do węzła klastra B:
   - Menedżer klastra trybu failover  
   - Klaster trybu failover programu PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Uruchom ponownie, A węzeł klastra w systemie operacyjnym gościa Windows. Spowoduje to zainicjowanie automatycznej pracy awaryjnej SAP \<SID\> Grupa klastra z węzła A węzłem B.  
3. Uruchom ponownie element węzła klastra w witrynie Azure portal. Spowoduje to zainicjowanie automatycznej pracy awaryjnej SAP \<SID\> Grupa klastra z węzła A węzłem B.  
4. Uruchom ponownie element węzła klastra za pomocą programu Azure PowerShell. Spowoduje to zainicjowanie automatycznej pracy awaryjnej SAP \<SID\> Grupa klastra z węzła A węzłem B.

   Po przejściu w tryb failover SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra B. Na przykład, że działa on pr1-ascs-1.

   ![Rysunek 8: W Menedżerze klastra trybu Failover, SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra B][sap-ha-guide-figure-5002]

   _**Rysunek 8**: W Menedżerze klastra trybu Failover, SAP \<SID\> grupy klastra jest uruchomiona w węźle klastra B_

   Udostępniony dysk jest teraz zainstalowany w klastrze węzła B. oprogramowanie SIOS DataKeeper jest replikowanie danych z dysku woluminu źródłowego S w węźle klastra B na dysku woluminu docelowego S w węźle klastra A. Na przykład replikacja odbywa się z pr1 ascs 1 [10.0.0.41] pr1 ascs 0 [10.0.0.40].

   ![Rysunek 9: Oprogramowanie SIOS DataKeeper replikuje woluminu lokalnego z węzła klastra B do A węzłem klastra][sap-ha-guide-figure-5003]

   _**Rysunek 9:** Oprogramowanie SIOS DataKeeper replikuje woluminu lokalnego z węzła klastra B do A węzłem klastra_
