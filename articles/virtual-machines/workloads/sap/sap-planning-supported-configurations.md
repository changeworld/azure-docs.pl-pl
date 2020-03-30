---
title: 'SAP na platformie Azure: obsługiwane scenariusze z maszynami wirtualnymi platformy Azure'
description: Maszyny wirtualne platformy Azure obsługiwane scenariusze z obciążeniem SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137632"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Obsługiwane scenariusze dotyczące obciążenia SAP na maszynie wirtualnej platformy Azure
Projektowanie architektury systemów SAP NetWeaver, Business one `Hybris` lub S/4HANA na platformie Azure otwiera wiele różnych możliwości dla różnych architektur i narzędzi do użycia, aby uzyskać skalowalne, wydajne i wysoce dostępne wdrożenie. Chociaż zależy to od używanego systemu operacyjnego lub systemu DBMS, istnieją ograniczenia. Ponadto nie wszystkie scenariusze, które są obsługiwane lokalnie są obsługiwane w taki sam sposób na platformie Azure. Ten dokument będzie prowadzić przez obsługiwane konfiguracje non-high-dostępności i konfiguracji o wysokiej dostępności i architektury przy użyciu maszyn wirtualnych platformy Azure wyłącznie. W przypadku scenariuszy obsługiwanych za pomocą [dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)zapoznaj się z artykułem [Obsługiwane scenariusze dla dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario). 


## <a name="2-tier-configuration"></a>Konfiguracja dwuwarstwowa
Konfiguracja SAP 2-warstwowa jest uważana za zbudowaną z połączonej warstwy SAP DBMS i warstwy aplikacji, które działają na tym samym serwerze lub jednostce maszyny Wirtualnej. Druga warstwa jest uważana za warstwę interfejsu użytkownika. W przypadku konfiguracji 2-warstwowej warstwy aplikacji DBMS i SAP współużytkować zasoby maszyny Wirtualnej platformy Azure. W rezultacie należy skonfigurować różne składniki w taki sposób, aby te nie konkurować o zasoby. Należy również uważać, aby nie przesubżyć zasobów maszyny Wirtualnej. Taka konfiguracja nie zapewnia żadnej wysokiej dostępności, poza [umowami dotyczących poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/) różnych składników platformy Azure.

Graficzna reprezentacja takiej konfiguracji może wyglądać następująco:

![Prosta konfiguracja 2-warstwowa](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Takie konfiguracje są obsługiwane przez systemy Windows, Red Hat, SUSE i Oracle Linux dla systemów DBMS SQL Server, Oracle, Db2, maxDB i SAP ASE dla przypadków produkcyjnych i nieprodukcyjnych. W przypadku sap HANA jako DBMS takie typy konfiguracji są obsługiwane tylko w przypadkach nieprodukcyjnych. Obejmuje to przypadek wdrożenia [dużych wystąpień platformy Azure HANA,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jak również.
Dla wszystkich kombinacji systemu operacyjnego/dbms obsługiwanych na platformie Azure ten typ konfiguracji jest obsługiwany. Jednak jest obowiązkowe, aby ustawić konfigurację dbms i składników SAP w taki sposób, że składniki DBMS i SAP nie konkurować o zasoby pamięci i procesora CPU, a tym samym przekracza fizyczne dostępne zasoby. Należy to zrobić, ograniczając pamięć dbms może przydzielić. Należy również ograniczyć pamięć rozszerzoną SAP w wystąpieniach aplikacji. Należy również monitorować zużycie procesora CPU maszyny Wirtualnej, aby upewnić się, że składniki nie są maksymalizacji zasobów procesora CPU. 

> [!NOTE]
> W przypadku systemów SAP w produkcji zalecamy dodatkową wysoką dostępność i ewentualne konfiguracje odzyskiwania po awarii, jak opisano w dalszej części tego dokumentu


## <a name="3-tier-configuration"></a>Konfiguracja 3-warstwowa
W takich konfiguracjach warstwa aplikacji SAP i warstwa DBMS są od różnych maszyn wirtualnych. Zwykle można to zrobić dla większych systemów i ze względu na bardziej elastyczne zasoby warstwy aplikacji SAP. W najprostszej konfiguracji nie ma wysokiej dostępności poza [umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/) różnych składników platformy Azure zaangażowanych. 

Graficzna reprezentacja wygląda następująco:

![Prosta konfiguracja 2-warstwowa](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Ten typ konfiguracji jest obsługiwany w systemach Windows, Red Hat, SUSE i Oracle Linux dla systemów DBMS SQL Server, Oracle, Db2, SAP HANA, maxDB i SAP ASE dla przypadków produkcyjnych i nieprodukcyjnych. Jest to domyślna konfiguracja wdrażania dla [dużych wystąpień platformy Azure HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). W celu uproszczenia nie rozróżniliśmy wystąpień okien dialogowych SAP Central Services i SAP w warstwie aplikacji SAP. W tej prostej konfiguracji 3-warstwowej nie będzie wysokiej dostępności ochrony sap central services.

> [!NOTE]
> W przypadku systemów SAP w produkcji zalecamy dodatkową wysoką dostępność i ewentualne konfiguracje odzyskiwania po awarii, jak opisano w dalszej części tego dokumentu


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Wiele wystąpień DBMS na jednostkę dużych wystąpień maszyny Wirtualnej lub HANA
W tym typie konfiguracji hostujesz wiele wystąpień DBMS na jednostkę dużych wystąpień maszyny Wirtualnej platformy Azure lub HANA. Motywacją może być mieć mniej systemów operacyjnych do utrzymania, a przy tym niższe koszty. Inne motywacje może być mieć większą elastyczność i większą wydajność, dzieląc zasoby większej jednostki VM lub HANA duże wystąpienie wśród wielu wystąpień dbms. Do tej pory konfiguracje te były wyświetlane głównie dla systemów nieprodukcyjnych.

Konfiguracja taka może wyglądać następująco:

![Wiele wystąpień usługi DBMS w jednej jednostce](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Ten typ wdrożenia systemu dbms jest obsługiwany dla:

- Program SQL Server w systemie Windows
- IBM Db2. Szczegóły w artykule [Wiele wystąpień (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Dla Oracle. Aby uzyskać szczegółowe informacje, zobacz [uwaga dotycząca pomocy technicznej SAP #1778431](https://launchpad.support.sap.com/#/notes/1778431) i powiązanych notatek SAP
- W przypadku sap HANA obsługiwanych jest wiele wystąpień na jednej maszynie wirtualnej, SAP wywołuje tę metodę wdrażania MCOS. Szczegółowe informacje można znaleźć w artykule SAP [Wiele systemów SAP HANA na jednym hoście (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Uruchamianie wielu wystąpień bazy danych na jednym hoście, należy upewnić się, że różne wystąpienia nie konkurują o zasoby, a tym samym przekraczają limity zasobów fizycznych maszyny Wirtualnej. Jest to szczególnie ważne dla pamięci, gdzie należy ograniczyć pamięć każdy z wystąpień współużytkowania maszyny Wirtualnej można przydzielić. To również może być prawdziwe dla zasobów procesora CPU różnych wystąpień bazy danych można wykorzystać. Wszystkie dbms wymienione mają konfiguracje, które umożliwiają ograniczenie alokacji pamięci i zasobów procesora CPU na poziomie wystąpienia.
Aby mieć obsługę takiej konfiguracji dla maszyn wirtualnych platformy Azure, oczekuje się, że dyski lub woluminy, które są używane dla danych i log/redo plików dziennika baz danych zarządzanych przez różne wystąpienia są oddzielne. Innymi słowy, pliki dziennika lub dzienniki dziennika logów danych zarządzanych przez różne wystąpienia DBMS nie powinny współużytkować tych samych dysków lub woluminów. 

Konfiguracja dysku dla dużych wystąpień HANA jest dostarczana skonfigurowana i jest szczegółowo opisana w [obsługiwanych scenariuszach dla dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos). 

> [!NOTE]
> W przypadku systemów SAP dla środowiska produkcyjnego zaleca się dodatkową wysoką dostępność i ewentualne konfiguracje odzyskiwania po awarii, zgodnie z opisem w dalszej części tego dokumentu. Maszyny wirtualne z wieloma wystąpieniami systemu DBMS nie są obsługiwane przy użyciu konfiguracji o wysokiej dostępności opisanych w dalszej części tego dokumentu.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Wiele wystąpień okna dialogowego SAP w jednej maszynie wirtualnej
W wielu przypadkach wiele wystąpień okien dialogowych został wdrożony na serwerach bez systemu operacyjnego lub nawet na maszynach wirtualnych działających w chmurach prywatnych. Powodem takich konfiguracji było dostosowanie niektórych wystąpień okna dialogowego SAP do określonych typów obciążenia, funkcji biznesowych lub obciążenia. Powodem nie izolowania tych wystąpień do oddzielnych maszyn wirtualnych był wysiłek konserwacji systemu operacyjnego i operacji. Lub w wielu przypadkach koszty w przypadku, gdy hoster lub operator maszyny Wirtualnej prosi o miesięczną opłatę za maszynę wirtualną obsługiwaną i administrowaną. Na platformie Azure scenariusz hostowania wielu wystąpień okna dialogowego SAP w ramach jednej maszyny Wirtualnej obsługiwanej przez nas do celów produkcyjnych i nieprodukcyjnych w systemach operacyjnych Windows, Red Hat, SUSE i Oracle Linux. Parametr jądra SAP PHYS_MEMSIZE, dostępny w systemie Windows i nowoczesnych jądrach systemu Linux, powinien być ustawiony, jeśli wiele wystąpień serwera aplikacji SAP jest uruchomionych na jednej maszynie wirtualnej. Zaleca się również ograniczenie rozszerzania pamięci rozszerzonej SAP w systemach operacyjnych, takich jak Windows, w których zaimplementowano automatyczny wzrost pamięci rozszerzonej SAP. Można to zrobić za pomocą `em/max_size_MB`parametru profilu SAP .

W konfiguracji 3-warstwowej, gdzie wiele wystąpień okna dialogowego SAP są uruchamiane w ramach maszyn wirtualnych platformy Azure może wyglądać:

![Wiele wystąpień usługi DBMS w jednej jednostce](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

W celu uproszczenia nie rozróżniliśmy wystąpień okien dialogowych SAP Central Services i SAP w warstwie aplikacji SAP. W tej prostej konfiguracji 3-warstwowej nie będzie wysokiej dostępności ochrony sap central services. W przypadku systemów produkcyjnych nie zaleca się pozostawiania usług SAP Central Services bez ochrony. Szczegółowe informacje na temat tak zwanych konfiguracji wielu identyfikatorów SID wokół wystąpień centralnych SAP i wysokiej dostępności takich konfiguracji z wieloma identyfikatorami SID można znaleźć w późniejszych sekcjach tego dokumentu.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Ochrona o wysokiej dostępności dla warstwy SAP DBMS
W miarę wdrażania systemów produkcyjnych SAP należy wziąć pod uwagę konfiguracje o wysokiej dostępności typu gorąca w trybie gotowości. Szczególnie w przypadku sap HANA, gdzie dane muszą być ładowane do pamięci, zanim będzie można uzyskać pełną wydajność i skalowalność z powrotem, leczenie usługi platformy Azure nie jest idealnym środkiem dla wysokiej dostępności. 

Ogólnie firma Microsoft obsługuje tylko konfiguracje o wysokiej dostępności i pakiety oprogramowania, które są opisane w sekcji obciążenia SAP w docs.microsoft.com. Tę samą instrukcję można odczytać w [#1928533](https://launchpad.support.sap.com/#/notes/1928533)notatki SAP . Firma Microsoft nie zapewni pomocy technicznej dla innych platform oprogramowania innych firm o wysokiej dostępności, które nie są dokumentowane przez firmę Microsoft w połączeniu z obciążeniem SAP. W takich przypadkach zewnętrzny dostawca struktury wysokiej dostępności jest stroną wspierającą konfigurację o wysokiej dostępności, która musi być zaangażowana przez Ciebie jako klienta w proces pomocy technicznej. Wyjątki zostaną wymienione w tym artykule. 

Ogólnie firma Microsoft obsługuje ograniczony zestaw konfiguracji wysokiej dostępności na maszynach wirtualnych platformy Azure lub jednostkach dużych wystąpień HANA. W przypadku obsługiwanych scenariuszy dużych wystąpień HANA przeczytaj dokument [Obsługiwane scenariusze dla dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

W przypadku maszyn wirtualnych platformy Azure na poziomie dbms są obsługiwane następujące konfiguracje o wysokiej dostępności:

- Replikacja systemu SAP HANA oparta na linuxowym rozruszniku serca na SUSE i Red Hat. Zobacz szczegółowe artykuły:
    - [Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Wysoka dostępność maszyn wirtualnych SAP HANA na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- Sap HANA skalować w poziomie n +m konfiguracji przy użyciu [plików NetApp Azure](https://azure.microsoft.com/services/netapp/) na SUSE i Red Hat. Szczegóły są wymienione w następujących artykułach:
    - [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp platformy Azure na serwerze SUSE Linux Enterprise Server}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp azure w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- Klaster trybu failover programu SQL Server oparty na programach Windows Scale-Out File Services. Chociaż zalecenie dla systemów produkcyjnych jest użycie programu SQL Server Always On zamiast klastrowania. SQL Server Always On zapewnia lepszą dostępność przy użyciu oddzielnego magazynu. Szczegóły są opisane w tym artykule: 
    - [Konfigurowanie wystąpienia klastra trybu failover programu SQL Server na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- Program SQL Server Always On jest obsługiwany przez system operacyjny Windows dla programu SQL Server na platformie Azure. Jest to domyślne zalecenie dla produkcyjnych wystąpień programu SQL Server na platformie Azure. Szczegóły są opisane w następujących artykułach:
    - [Wprowadzenie grup dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
    - [Skonfiguruj grupę dostępności zawsze włączone na maszynach wirtualnych platformy Azure w różnych regionach](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
    - [Skonfiguruj moduł równoważenia obciążenia dla grupy dostępności zawsze włączone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).
- Oracle Data Guard dla Windows i Oracle Linux. Szczegóły dotyczące systemu Oracle Linux można znaleźć w tym artykule:
    - [Wdrażanie oracle data guard na maszynie wirtualnej systemu Azure Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR na SUSE i RHEL Szczegółowa dokumentacja dla SUSE i RHEL za pomocą rozrusznika serca znajduje się tutaj:
    - [Wysoka dostępność środowiska IBM Db2 LUW na maszynach wirtualnych platformy Azure na suse linux enterprise server z rozrusznikiem serca](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Konfiguracja SAP ASE i SAP maxDB, jak opisano w tych dokumentach:
    - [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [Wdrożenie SAP MaxDB, usługi liveCache i content server na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Hana dużych wystąpień scenariusze wysokiej dostępności są szczegółowo w:
    - [Obsługiwane scenariusze dla dużych wystąpień HANA- HSR z STONITH dla wysokiej dostępności](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Obsługiwane scenariusze dla dużych wystąpień HANA — automatyczne tryb failover hosta (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> W żadnym ze scenariuszy opisanych powyżej obsługujemy konfiguracje wielu wystąpień systemu DBMS w jednej maszynie wirtualnej. Oznacza, że w każdym przypadku tylko jedno wystąpienie bazy danych można wdrożyć na maszynie wirtualnej i chronione za pomocą opisanych metod wysokiej dostępności. Ochrona wielu wystąpień systemu DBMS w tym samym klastrze trybu failover systemu Windows lub rozrusznika serca **NIE** jest obsługiwana w tym momencie. Również Oracle Data Guard jest obsługiwany tylko dla pojedynczego wystąpienia na przypadki wdrażania maszyny Wirtualnej. 

Różne systemy baz danych umożliwiają hostowanie wielu baz danych w ramach jednego wystąpienia DBMS. Podobnie jak w przypadku SAP HANA, wiele baz danych może być hostowanych w wielu kontenerach bazy danych (MDC). W przypadkach, gdy te konfiguracje wielu baz danych działają w ramach jednego zasobu klastra trybu failover, te konfiguracje są obsługiwane. Konfiguracje, które nie są obsługiwane, to przypadki, w których wymagane byłoby wiele zasobów klastra. Jeśli chodzi o konfiguracje, w których można zdefiniować wiele grup dostępności programu SQL Server, w ramach jednego wystąpienia programu SQL Server.


![Konfiguracja usługi DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

W zależności od systemu DBMS/lub systemów operacyjnych składniki, takie jak moduł równoważenia obciążenia platformy Azure może lub nie mogą być wymagane jako część architektury rozwiązania. 

W szczególności dla maxDB konfiguracja magazynu musi być inna. W maxDB pliki danych i dzienników muszą znajdować się w magazynie współdzielonym, aby uzyskać konfiguracje o wysokiej dostępności. Tylko w przypadku maxDB współdzielonego magazynu jest obsługiwany dla wysokiej dostępności. Dla wszystkich innych dbms oddzielne stosy magazynu na węzeł są tylko obsługiwane konfiguracje dysku.

Inne struktury wysokiej dostępności są znane istnieją i są znane do uruchamiania na platformie Microsoft Azure, jak również. Firma Microsoft nie przetestowała jednak tych struktur. Jeśli chcesz zbudować konfigurację wysokiej dostępności z tymi strukturami, musisz współpracować z dostawcą tego oprogramowania, aby:

- Tworzenie architektury wdrażania
- Wdrażanie architektury
- Wsparcie architektury

> [!IMPORTANT]
> Witryna Microsoft Azure Marketplace oferuje wiele urządzeń miękkich, które zapewniają rozwiązania magazynu na platformie Azure. Te miękkie urządzenia mogą być używane do tworzenia udziałów NFS, jak również, które teoretycznie mogą być używane w SAP HANA skalowania w poziomie wdrożeń, gdzie wymagany jest węzeł rezerwowy. Z różnych powodów żadne z tych urządzeń programowych magazynu jest obsługiwane dla żadnego z wdrożeń dbms przez firmę Microsoft i SAP na platformie Azure. Wdrożenia dbms na udziały SMB nie jest obsługiwany w ogóle w tym momencie. Wdrożenia dbms na udziały NFS jest ograniczona do nfs 4.1 udziałów w [usłudze Azure NetApp Files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Wysoka dostępność usługi SAP Central
SAP Central Services to drugi pojedynczy punkt awarii konfiguracji SAP. W rezultacie należy również chronić te procesy usług centralnych. Oferta obsługiwana i udokumentowana dla obciążenia SAP brzmi następująco:

- Serwer klastra trybu failover systemu Windows przy użyciu programów Windows Skal w poziomie usług plików dla katalogu sapmnt i transportu globalnego. Szczegóły są opisane w artykule:
    - [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Przygotowywanie infrastruktury platformy Azure dla wysokiej dostępności systemu SAP przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Serwer klastra trybu failover systemu Windows przy użyciu udziału SMB opartego na [plikach NetApp usługi Azure](https://azure.microsoft.com/services/netapp/) dla sapmnt i globalnego katalogu transportu. Szczegóły są wymienione w artykule:
    - [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z plikami NetApp(SMB) platformy Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Serwer klastra trybu failover `Datakeeper`systemu Windows oparty na programie SIOS . Chociaż jest to udokumentowane przez firmę Microsoft, potrzebujesz relacji pomocy technicznej z SIOS, dzięki czemu można korzystać z pomocy technicznej SIOS podczas korzystania z tego rozwiązania. Szczegóły są opisane w artykule:
    - [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Przygotowanie infrastruktury platformy Azure dla usługi SAP HA przy użyciu klastra trybu failover systemu Windows i udostępnionego dysku dla SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Rozrusznik serca w systemie operacyjnym SUSE z utworzeniem wysoce dostępnego udziału `drdb` NFS przy użyciu dwóch maszyn wirtualnych SUSE i replikacji plików. Szczegóły są udokumentowane w artykule
    - [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na suse Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- System operacyjny Pacemaker SUSE z wykorzystaniem udziałów NFS dostarczanych przez [usługi Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Szczegóły są udokumentowane w
    - [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server z plikami NetApp usługi Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Rozrusznik serca w systemie operacyjnym Red Hat z `glusterfs` udziałem NFS hostowanym w klastrze. Szczegóły można znaleźć w artykułach
    - [Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Rozrusznik serca w systemie operacyjnym Red Hat z udziałem NFS hostowanym na [platformie Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Szczegóły są opisane w artykule
    - [Wysoka dostępność maszyn wirtualnych platformy Azure dla systemu SAP NetWeaver w systemie Red Hat Enterprise Linux z plikami NetApp usługi Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Z wymienionych rozwiązań, potrzebujesz relacji wsparcia z SIOS, aby wspierać `Datakeeper` produkt i bezpośrednio współpracować z SIOS w przypadku problemów. W zależności od sposobu licencjonowania systemu Windows, Red Hat i/lub systemu operacyjnego SUSE może być również wymagana umowa pomocy technicznej z dostawcą systemu operacyjnego, aby mieć pełną obsługę wymienionych konfiguracji wysokiej dostępności.

Konfiguracja może być również wyświetlana w następujący sposób:

![Konfiguracja dbms i ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Po prawej stronie grafiki wyświetlana jest wysoce dostępna sap central services. Oprócz ochrony usług SAP Central za pomocą struktury klastra trybu failover, która może zakończyć się awaryjnie w przypadku problemu, istnieje konieczność posiadania wysoce dostępnego udziału NFS lub SMB lub udostępnionego dysku systemu Windows, aby upewnić się, że sapmnt i globalny katalog transportowy są niezależnie od istnienia jednej maszyny Wirtualnej. Dodatkowe niektóre rozwiązania, takie jak serwer klastra trybu failover systemu Windows i rozrusznik serca będą wymagać modułu równoważenia obciążenia platformy Azure do kierowania lub ponownego kierowania ruchu do w dobrej kondycji węzła.

Na pokazanej liście nie ma wzmianki o systemie operacyjnym Oracle Linux. Oracle Linux nie obsługuje rozrusznika jako struktury klastra. Jeśli chcesz wdrożyć system SAP w systemie Oracle Linux i potrzebujesz wysokiej dostępności dla systemu Oracle Linux, musisz współpracować z dostawcami zewnętrznymi. Jednym z dostawców jest SIOS z pakietem Protection Suite dla systemu Linux, który jest obsługiwany przez sap na platformie Azure. Aby uzyskać więcej informacji, przeczytaj notatkę SAP [#1662610 — szczegółowe informacje dotyczące pomocy technicznej dla pakietu SIOS Protection Suite dla systemu Linux,](https://launchpad.support.sap.com/#/notes/1662610) aby uzyskać więcej informacji.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Obsługiwane magazynowanie w scenariuszach SAP Central Services wymienionych powyżej
Ponieważ tylko podzbiór typów magazynu platformy Azure zapewnia wysoce dostępne udziały NFS lub SMB tej jakości dla użycia w naszych scenariuszach klastra SAP Central Services lista obsługiwanych typów magazynu

- Serwer klastra trybu failover systemu Windows z programem Windows Scale-wy rozbił się na serwerze plików w poziomie, można wdrożyć we wszystkich natywnych typach magazynu platformy Azure, z wyjątkiem plików NetApp platformy Azure. Jednak zalecenie jest wykorzystanie magazynu w wersji Premium ze względu na najwyższej poziomu usług umów w przepływności i we/wy.
- Serwer klastra trybu failover systemu Windows zmb na usługi Azure NetApp Files jest obsługiwany w plikach NetApp platformy Azure. Udziały SMB w usługach plików azure **nie** są obsługiwane w tym momencie.
- Serwer klastra trybu failover systemu Windows `Datakeeper` z udostępnionym dyskiem systemu Windows opartym na sios można wdrożyć na wszystkich natywnych typach magazynu platformy Azure, z wyjątkiem plików NetApp platformy Azure. Jednak zalecenie jest wykorzystanie magazynu w wersji Premium ze względu na najwyższej poziomu usług umów w przepływności i we/wy.
- SUSE lub Red Hat Pacemaker przy użyciu udziałów NFS w usłudze Azure NetApp Files jest obsługiwany w plikach NetApp platformy Azure. 
- SUSE Pacemaker `drdb` przy użyciu konfiguracji między dwoma maszynami wirtualnymi jest obsługiwany przy użyciu natywnych typów magazynu platformy Azure, z wyjątkiem usługi Azure NetApp Files. Jednak zalecenie jest wykorzystanie magazynu w wersji Premium ze względu na najwyższej poziomu usług umów w przepływności i we/wy.
- Red Hat Pacemaker przy użyciu `glusterfs` do dostarczania udziału NFS jest obsługiwany przy użyciu natywnych typów magazynu platformy Azure, z wyjątkiem usługi Azure NetApp Files. Jednak zalecenie jest wykorzystanie magazynu w wersji Premium ze względu na najwyższej poziomu usług umów w przepływności i we/wy.

> [!IMPORTANT]
> Witryna Microsoft Azure Marketplace oferuje wiele urządzeń miękkich, które zapewniają rozwiązania magazynu na platformie Azure. Te miękkie urządzenia mogą być używane do tworzenia udziałów NFS lub SMB, jak również, że teoretycznie może być używany w pracy awaryjnej klastrowane SAP Central Services, jak również. Te rozwiązania nie są bezpośrednio obsługiwane dla obciążenia SAP przez firmę Microsoft. Jeśli użytkownik zdecyduje się użyć takiego rozwiązania do utworzenia pakietu NFS lub SMB, pomoc techniczną dla konfiguracji sap central service musi być zapewniona przez stronę trzecią, która jest właścicielem oprogramowania w miękkim urządzeniu pamięci masowej.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Klastry trybu failover sap sap z wieloma identyfikatorami SID
Aby zmniejszyć liczbę maszyn wirtualnych, które są potrzebne w dużych krajobrazów SAP, SAP umożliwia uruchamianie sap central services wystąpień wielu różnych systemów SAP w konfiguracji klastra trybu failover. Wyobraź sobie przypadki, w których masz 30 lub więcej systemów produkcyjnych NetWeaver lub S/4HANA. Bez klastrowania wielu identyfikatorów SID te konfiguracje wymagałyby 60 lub więcej maszyn wirtualnych w 30 lub więcej konfiguracjach klastra trybu failover systemu Windows lub rozrusznika serca. Oprócz klastrów trybu failover dbms konieczne. Wdrażanie wielu usług centralnych SAP w dwóch węzłach w konfiguracji klastra trybu failover może znacznie zmniejszyć liczbę maszyn wirtualnych. Jednak wdrażanie wielu wystąpień usług SAP Central w konfiguracji klastra z jednym węzłem ma również pewne wady. Problemy związane z pojedynczą maszyną wirtualną w konfiguracji klastra dotyczą wielu systemów SAP. Konserwacja systemu operacyjnego gościa uruchomionego w konfiguracji klastra wymaga większej koordynacji, ponieważ dotyczy to wielu produkcyjnych systemów SAP. Narzędzia takie jak SAP LaMa nie obsługują klastrowania wielu identyfikatorów SID w procesie klonowania systemu.

Na platformie Azure konfiguracja klastra z wieloma identyfikatorami SID jest obsługiwana dla systemu operacyjnego Windows z ENSA1 i ENSA2. Zalecenie nie polega na połączeniu starszej architektury usługi enqueue replication service (ENSA1) z nową architekturą (ENSA2) w jednym klastrze wielosuwowym. Szczegóły dotyczące takiej architektury są udokumentowane w artykułach

- [Wysoka dostępność wystąpienia SAP ASCS/SCS z klastrem trybu failover systemu Windows Server i dyskiem współdzielonym na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Sap ASCS/SCS wystąpienie wielu identyfikatorów SID wysokiej dostępności z klastrów trybu failover systemu Windows Server i udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

W przypadku SUSE obsługiwany jest również klaster wieloustawowy oparty na rozruszniku serca. Do tej pory konfiguracja jest obsługiwana dla:

- Maksymalnie pięć wystąpień SAP ASCS/SCS
- Stara architektura lodu serwera replikacji w kolejce (ENSA1)
- Konfiguracje klastra rozrusznika dwuwęzłowego

Konfiguracja jest udokumentowana w [przewodniku O wysokiej dostępności dla sap NetWeaver na maszynach wirtualnych platformy Azure na suse Linux Enterprise Server for SAP applications multi-SID guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Klaster wieloustawowy z serwerem enqueue replication schematycznie wygląda

![Konfiguracja dbms i ASCS HA](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Scenariusze skalowania w poziomie SAP HANA
Scenariusze skalowania w poziomie SAP HANA są obsługiwane dla podzbioru maszyn wirtualnych platformy Azure z certyfikatem HANA, wymienionych w [katalogu sprzętowym SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Wszystkie maszyny wirtualne oznaczone "Tak" w kolumnie "Klastrowanie" mogą być używane do skalowania OLAP lub S/4HANA w poziomie. Konfiguracje bez stanu wstrzymania są obsługiwane za pomocą typów usługi Azure Storage: 

- Usługa Azure Premium Storage, w tym akcelerator usługi Azure Write dla woluminu /hana/log
- [Dysk w warstwie Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Konfiguracje skalowania w poziomie SAP HANA dla OLAP lub S/4HANA z węzłami wstrzymania są obsługiwane wyłącznie za pomocą usług NFS udostępnionych w plikach NetApp.

Aby uzyskać więcej informacji na temat dokładnych konfiguracji magazynu z węzłem wstrzymania lub bez, zapoznaj się z artykułami:

- [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp platformy Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp azure w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Uwaga dotycząca obsługi sap #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Aby uzyskać szczegółowe informacje na temat dużych wystąpień HANA obsługiwanych konfiguracji skalowania w poziomie HANA, stosuje się następującą dokumentację:

- [Obsługiwane scenariusze skalowania w poziomie dużych wystąpień HANA ze wstrzymanie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Obsługiwane scenariusze skalowania w poziomie dużych wystąpień HANA bez gotowości](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenariusz odzyskiwania po awarii
Istnieje wiele scenariuszy odzyskiwania po awarii, które są obsługiwane. Definiujemy architektury awarii jako architektury, które powinny zrekompensować pełne regiony platformy Azure wychodzące z sieci. Oznacza to, że potrzebujemy docelowego odzyskiwania po awarii, aby być innym regionem platformy Azure jako celem do uruchamiania środowiska SAP. Oddzielamy metody i konfiguracje w warstwie DBMS i warstwie innej niż DBMS. 

### <a name="dbms-layer"></a>Warstwa DBMS
W przypadku warstwy DBMS obsługiwane są konfiguracje przy użyciu natywnych mechanizmów replikacji DBMS, takich jak Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On lub HANA System Replication. Jest to obowiązkowe, że strumień replikacji w takich przypadkach jest asynchroniczne, a nie synchroniczne, jak w typowych scenariuszach wysokiej dostępności, które są wdrażane w jednym regionie platformy Azure. Typowy przykład takiej obsługiwanej konfiguracji odzyskiwania po awarii systemu DBMS opisano w artykule [Dostępność SAP HANA w regionach platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions) Druga grafika w tej sekcji opisuje scenariusz z HANA jako przykład. Główne bazy danych obsługiwane dla aplikacji SAP są w stanie wdrożyć w takim scenariuszu.

Jest obsługiwany do korzystania z mniejszej maszyny Wirtualnej jako wystąpienie docelowe w regionie odzyskiwania po awarii, ponieważ maszyna wirtualna nie występuje pełny ruch obciążenia. W ten sposób należy pamiętać o następujących kwestiach:

- Mniejsze typy maszyn wirtualnych nie zezwalają na podłączenie wielu dysków niż mniejsze maszyny wirtualne
- Mniejsze maszyny wirtualne mają mniejszą przepustowość sieci i pamięci masowej
- Zmiana rozmiaru w rodzinach maszyn wirtualnych może być problemem, gdy różne maszyny wirtualne są zbierane w jednym zestawie dostępności platformy Azure lub gdy zmiana rozmiaru powinna nastąpić między rodziną maszyn wirtualnych z serii M i rodziną maszyn wirtualnych Mv2
- Zużycie procesora i pamięci dla wystąpienia bazy danych jest w stanie odbierać strumień zmian z minimalnym opóźnieniem i wystarczającą ilością zasobów procesora i pamięci, aby zastosować te zmiany z minimalnym opóźnieniem do danych  

Więcej informacji na temat ograniczeń różnych rozmiarów maszyn wirtualnych można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Inną obsługiwaną metodą wdrażania obiektu docelowego odzyskiwania po awarii jest zainstalowanie drugiego wystąpienia DBMS na maszynie Wirtualnej, która uruchamia nieprodukcyjne wystąpienie DBMS nieprodukcyjnego wystąpienia SAP. Może to być nieco trudniejsze, ponieważ trzeba dowiedzieć się, co na pamięci, zasobów procesora, przepustowości sieci i przepustowości magazynu jest potrzebne dla poszczególnych wystąpień docelowych, które powinny działać jako główne wystąpienie w scenariuszu odzyskiwania po awarii. Szczególnie w HANA zaleca się konfigurowanie wystąpienia, które działa jako miejsce docelowe odzyskiwania po awarii na udostępnionym hoście, tak aby dane nie są wstępnie ładowane do wystąpienia docelowego odzyskiwania po awarii.

W przypadku scenariuszy odzyskiwania po awarii dużych wystąpień HANA sprawdź te dokumenty:

- [Pojedynczy węzeł z odzyskiwania po awarii przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Pojedynczy węzeł z modułem ODZYSKIWANIA PO (wielofunkcyjnym) przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Pojedynczy węzeł z modułem ODZYSKIWANIA PO (wielofunkcyjnym) przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Wysoka dostępność dzięki hsr i odzyskiwania po awarii z replikacją pamięci masowej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Skalowanie w poziomie przy użyciu odzyskiwania po awarii przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Pojedynczy węzeł z dr przy użyciu HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Pojedynczy węzeł HSR do odzyskiwania po awarii (zoptymalizowany pod kątem kosztów)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Wysoka dostępność i odzyskiwanie po awarii dzięki HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Wysoka dostępność i odzyskiwanie po awarii dzięki HSR (zoptymalizowane pod względem kosztów)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Skalowanie w poziomie z dr przy użyciu HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> Użycie [usługi Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nie zostało przetestowane dla wdrożeń dbms w ramach obciążenia SAP. W rezultacie nie jest obsługiwany dla warstwy DBMS systemów SAP w tym momencie. Inne metody replikacji przez firmy Microsoft i SAP, które nie są wymienione, nie są obsługiwane. Używanie oprogramowania innych firm do replikowania warstwy DBMS systemów SAP między różnymi regionami platformy Azure musi być obsługiwane przez dostawcę oprogramowania i nie będzie obsługiwane za pośrednictwem kanałów pomocy technicznej firmy Microsoft i SAP. 
 
## <a name="non-dbms-layer"></a>Warstwa nieobjęta dbms
W przypadku warstwy aplikacji SAP i ewentualnych udziałów lub lokalizacji magazynu, które są potrzebne, dwa główne scenariusze są wykorzystywane przez klientów:

- Obiekty docelowe odzyskiwania po awarii w drugim regionie platformy Azure nie są używane do żadnych celów produkcyjnych lub nieprodukcyjnych. W tym scenariuszu maszyny wirtualne, które działają jako miejsce docelowe odzyskiwania po awarii, nie są idealnie wdrożone, a obraz i zmiany w obrazach warstwy aplikacji SAP produkcji jest replikowany do regionu odzyskiwania po awarii. Funkcją, która może wykonać takie zadanie, jest [usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview). Usługa Azure Site Recovery obsługuje takie scenariusze replikacji platformy Azure na platformę Azure. 
- Obiekty docelowe odzyskiwania po awarii są maszynami wirtualnymi, które są faktycznie używane przez systemy nieprodukcyjne. Cały krajobraz SAP jest rozłożony na dwa różne regiony platformy Azure z systemami produkcyjnymi zwykle w jednym regionie i systemami nieprodukcyjnymi w innym regionie. W wielu wdrożeniach klientów klient ma system nieprodukcyjny, który jest odpowiednikiem systemu produkcyjnego. Klient ma wstępnie zainstalowane wystąpienia aplikacji produkcyjnych w systemach nieprodukcyjnych warstwy aplikacji. W przypadku pracy awaryjnej wystąpienia nieprodukcyjne zostaną zamknięte, wirtualne nazwy maszyn wirtualnych produkcyjnych przeniesione na nieprodukcyjne maszyny wirtualne (po przypisaniu nowych adresów IP w systemie DNS) i rozpoczynają się preinstalowane wystąpienia produkcyjne.

### <a name="sap-central-services-clusters"></a>Klastry usług SAP Central Services
Klastry sap central services korzystające z dysków udostępnionych (Windows), udziałów SMB (Windows) lub NFS są nieco trudniejsze do replikacji. Po stronie systemu Windows replikacja magazynu systemu Windows jest możliwym rozwiązaniem. Na Linuksie rsync jest realnym rozwiązaniem.



## <a name="non-supported-scenario"></a>Scenariusz nieobjęty wsparciem
Istnieje lista scenariuszy, które nie są obsługiwane dla obciążenia SAP na architekturach platformy Azure. **Nie obsługiwane** oznacza, że sap i microsoft nie będzie w stanie obsługiwać tych konfiguracji i trzeba odroczyć do ewentualnego zaangażowanych osób trzecich, które dostarczyły oprogramowanie do ustanowienia takich architektur. Dwie kategorie to:

- Urządzenia miękkie magazynu: istnieje wiele urządzeń miękkich magazynu oferowanych w witrynie Azure Marketplace. Niektórzy dostawcy oferują własną dokumentację dotyczącą używania tych urządzeń programowych magazynu na platformie Azure związanych z oprogramowaniem SAP. Dostawca tych urządzeń miękkich do przechowywania musi zapewnić obsługę konfiguracji lub wdrożeń obejmujących takie urządzenia programowe do magazynowania. Fakt ten objawia się również w [nocie technicznej SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Struktury wysokiej dostępności: tylko program Pacemaker i klaster trybu failover systemu Windows Server są obsługiwane struktury wysokiej dostępności dla obciążenia SAP na platformie Azure. Jak wspomniano wcześniej, rozwiązanie SIOS `Datakeeper` jest opisane i udokumentowane przez firmę Microsoft. Niemniej jednak składniki SIOS `Datakeeper` muszą być obsługiwane przez SIOS jako dostawca dostarczający te składniki. Firma SAP wymieniła również inne certyfikowane struktury wysokiej dostępności w różnych notatkach SAP. Niektóre z nich zostały certyfikowane przez zewnętrznego dostawcę platformy Azure. Niemniej jednak obsługa konfiguracji korzystających z tych produktów musi być świadczona przez dostawcę produktu. Różni dostawcy mają inną integrację z procesami obsługi SAP. Przed podjęciem decyzji o użyciu produktu w konfiguracjach SAP wdrożonych na platformie Azure należy wyjaśnić, który proces pomocy technicznej jest najlepszy dla określonego dostawcy.
- Udostępnione klastry dysków, w których pliki bazy danych znajdują się na dyskach udostępnionych, nie są obsługiwane z wyjątkiem bazy danych maxDB. W przypadku wszystkich innych baz danych obsługiwane rozwiązanie ma mieć oddzielne lokalizacje magazynu zamiast udziału SMB lub NFS lub udostępnionego dysku w celu skonfigurowania scenariuszy wysokiej dostępności

Inne scenariusze, które nie są obsługiwane, to scenariusze takie jak:

- Scenariusze wdrażania, które wprowadzają większe opóźnienie sieci między warstwą aplikacji SAP a warstwą SAP DBMS we wspólnej architekturze SAP, jak `Hybris`pokazano w netweaver, S/4HANA i np. . Obejmuje to:
    - Wdrażanie jednej z warstw w środowisku lokalnym, podczas gdy druga warstwa jest wdrażana na platformie Azure
    - Wdrażanie warstwy aplikacji SAP systemu w innym regionie platformy Azure niż warstwa DBMS
    - Wdrażanie jednej warstwy w centrach danych, które znajdują się na platformie Azure i innej warstwie na platformie Azure, z wyjątkiem sytuacji, gdy takie wzorce architektury są dostarczane przez natywną usługę platformy Azure
    - Wdrażanie sieciowych urządzeń wirtualnych między warstwą aplikacji SAP a warstwą DBMS
    - Wykorzystanie magazynu hostowanego w centrach danych zlokalizowanych w centrum danych platformy Azure dla warstwy SAP DBMS lub globalnego katalogu transportu SAP
    - Wdrażanie dwóch warstw z dwoma różnymi dostawcami chmury. Na przykład wdrażanie warstwy DBMS w infrastrukturze Oracle Cloud Infrastructure i warstwy aplikacji na platformie Azure
- Konfiguracje klastra rozrusznika gwiazd hana z wieloma wystąpieniami
- Konfiguracje klastra systemu Windows z dyskami udostępnionymi za pośrednictwem SOFS lub SMB w bazie danych ANF dla SAP obsługiwane w systemie Windows. Zamiast tego zaleca się użycie natywnej replikacji wysokiej dostępności poszczególnych baz danych i użyj oddzielnych stosów magazynu
- Wdrażanie baz danych SAP obsługiwanych w systemie Linux z plikami baz danych znajdującymi się w akcjach NFS na szczycie ANF, z wyjątkiem SAP HANA
- Wdrożenie oracle dbms na dowolnym innym systemie operacyjnym gościa niż Windows i Oracle Linux. Zobacz też [uwaga dotycząca obsługi sap #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scenariusz (s) że nie testowaliśmy i dlatego nie mają doświadczenia z listy, takich jak:

- Usługa Azure Site Recovery replikuje maszyny wirtualne warstwy DBMS. W związku z tym zalecamy wykorzystanie natywnej funkcji replikacji asynchroniiowej bazy danych do potencjalnej konfiguracji odzyskiwania po awarii
 

## <a name="next-steps"></a>Następne kroki
Przeczytaj kolejne kroki w [planowaniu i implementacji maszyn wirtualnych platformy Azure dla sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



