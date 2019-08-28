---
title: 'Szybki start: Ręczna instalacja SAP HANA pojedynczego wystąpienia na platformie Azure Virtual Machines | Microsoft Docs'
description: Przewodnik Szybki Start dotyczący ręcznej instalacji SAP HANA pojedynczego wystąpienia na platformie Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 8d4e7b7056f4d5e53785366818fad05e24cfc605
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100051"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Szybki start: Ręczna instalacja SAP HANA pojedynczego wystąpienia na platformie Azure Virtual Machines
## <a name="introduction"></a>Wprowadzenie
Ten przewodnik ułatwia skonfigurowanie pojedynczego wystąpienia SAP HANA na platformie Azure Virtual Machines podczas ręcznego instalowania oprogramowania SAP NetWeaver 7,5 i SAP HANA 1,0 SP12. Ten przewodnik zawiera informacje na temat wdrażania SAP HANA na platformie Azure. Nie zastępuje ona dokumentacji SAP. 

> [!NOTE]
> W tym przewodniku opisano wdrożenia SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać informacje na temat sposobu wdrażania SAP HANA w dużych wystąpieniach platformy HANA, zobacz [Korzystanie z oprogramowania SAP na platformie Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, że znasz takie podstawowe informacje dotyczące infrastruktury jako usługi (IaaS):
 * Jak wdrażać maszyny wirtualne lub sieci wirtualne za pośrednictwem Azure Portal lub programu PowerShell.
 * Międzyplatformowy interfejs wiersza polecenia (CLI) platformy Azure, który obejmuje opcję używania szablonów JavaScript Object Notation (JSON).

W tym przewodniku przyjęto również założenie, że znasz:
* SAP HANA i SAP NetWeaver oraz sposób instalacji lokalnych.
* Jak zainstalować i obsługiwać wystąpienia aplikacji SAP HANA i SAP na platformie Azure.
* Następujące pojęcia i procedury:
   * Planowanie wdrożenia oprogramowania SAP na platformie Azure, w tym usługi Azure Virtual Network Planning i Azure Storage. Zobacz temat [SAP NetWeaver na platformie Virtual Machines Azure — przewodnik planowania i implementacji](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Zasady wdrażania i sposoby wdrażania maszyn wirtualnych na platformie Azure. Zobacz [wdrożenie usługi Azure Virtual Machines dla oprogramowania SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Wysoka dostępność dla SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) i serwer replikacji (wykres WYWOŁUJĄCYCH) na platformie Azure. Zobacz [wysoką dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Szczegółowe informacje na temat ulepszania wydajności w przypadku instalacji z użyciem usługi ASCS/SCS na platformie Azure. Zobacz [Tworzenie konfiguracji oprogramowania SAP NetWeaver z obsługą wieloidentyfikatorem SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Zasady uruchamiania oprogramowania SAP NetWeaver oparte na maszynach wirtualnych opartych na systemie Linux na platformie Azure. Zobacz [Uruchamianie oprogramowania SAP NetWeaver na maszynach wirtualnych Microsoft Azure z systemem SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ten przewodnik zawiera szczegółowe ustawienia systemu Linux na maszynach wirtualnych platformy Azure. Zawiera również informacje dotyczące sposobu poprawnego dołączania dysków usługi Azure Storage do maszyn wirtualnych z systemem Linux.

Typy maszyn wirtualnych platformy Azure, które mogą być używane na potrzeby scenariuszy produkcyjnych, są wymienione w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy nieprodukcyjnych dostępna jest szeroka gama natywnych typów maszyn wirtualnych platformy Azure.
Aby uzyskać więcej informacji na temat konfiguracji i operacji maszyny wirtualnej, zobacz [SAP HANA konfiguracje i operacje związane z infrastrukturą na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Aby uzyskać SAP HANA wysoką dostępność, zobacz [SAP HANA wysokiej dostępności dla Virtual Machines platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Jeśli chcesz szybko uzyskać SAP HANA wystąpienia usługi 4HANA lub S/4HANA, rozważ użycie [biblioteki urządzeń w chmurze SAP](https://cal.sap.com). W [tym przewodniku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)znajdziesz dokumentację dotyczącą sposobu wdrażania systemu S/4HANA za pomocą biblioteki urządzeń SAP w chmurze na platformie Azure. Wystarczy, że jest to subskrypcja platformy Azure i użytkownik SAP, który może być zarejestrowany w bibliotece urządzeń SAP Cloud.

## <a name="additional-resources"></a>Dodatkowe zasoby
### <a name="sap-hana-backup"></a>SAP HANA kopia zapasowa
Aby uzyskać informacje na temat tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure, zobacz:
* [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup na poziomie pliku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP HANA kopii zapasowej na podstawie migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Biblioteka urządzeń w chmurze SAP
Aby uzyskać informacje o sposobach wdrażania usługi S/4HANA lub BW/4HANA przy użyciu biblioteki urządzeń w chmurze SAP, zobacz [wdrażanie oprogramowania SAP s/4HANA lub BW/4HANA na Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Obsługiwane systemy operacyjne SAP HANA
Aby uzyskać informacje o obsługiwanych systemach operacyjnych SAP HANA, zobacz [temat SAP Note 2235581-SAP HANA: Obsługiwane systemy](https://launchpad.support.sap.com/#/notes/2235581/E)operacyjne. Maszyny wirtualne platformy Azure obsługują tylko podzbiór tych systemów operacyjnych. W przypadku wdrażania SAP HANA na platformie Azure obsługiwane są następujące systemy operacyjne: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Aby uzyskać dodatkową dokumentację SAP dotyczącą SAP HANA i innych systemów operacyjnych Linux, zobacz:

* [Uwaga dotycząca oprogramowania SAP 171356: Oprogramowanie SAP w systemie Linux: Informacje](https://launchpad.support.sap.com/#/notes/1984787)ogólne.
* [Uwaga dotycząca oprogramowania SAP 1944799: SAP HANA wskazówki dotyczące instalacji](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)systemu operacyjnego SLES.
* [Uwaga dotycząca oprogramowania SAP 2205917: SAP HANA DB zaleca ustawienia systemu operacyjnego dla SLES 12 dla aplikacji](https://launchpad.support.sap.com/#/notes/2205917/E)SAP.
* [Uwaga dotycząca oprogramowania SAP 1391070: Rozwiązania](https://launchpad.support.sap.com/#/notes/1391070)UUID systemu Linux.
* [SAP Note 2009879: Wytyczne SAP HANA dla systemu](https://launchpad.support.sap.com/#/notes/2009879)operacyjnego Red Hat Enterprise Linux (RHEL).
* [Uwaga dotycząca oprogramowania SAP 2292690: SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL](https://launchpad.support.sap.com/#/notes/2292690/E)7.

### <a name="sap-monitoring-in-azure"></a>Monitorowanie SAP na platformie Azure
Aby uzyskać informacje o monitorowaniu oprogramowania SAP na platformie Azure:

* W przypadku [oprogramowania SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) omówiono ulepszone monitorowanie SAP z maszynami wirtualnymi z systemem Linux na platformie Azure. 
* W przypadku [oprogramowania SAP uwaga 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) omówiono informacje dotyczące SAPOSCOL w systemie Linux. 
* W przypadku [oprogramowania SAP uwaga 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) omówiono kluczowe metryki monitorowania dla oprogramowania SAP na Microsoft Azure.

### <a name="azure-vm-types"></a>Typy maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure i scenariusze obciążeń obsługiwane przez SAP używane z SAP HANA są udokumentowane na [platformach IaaS Certified z certyfikatem SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Typy maszyn wirtualnych platformy Azure certyfikowane przez oprogramowanie SAP dla oprogramowania SAP NetWeaver lub warstwa aplikacji S/4HANA zostały [udokumentowane w programie SAP Note 1928533: Aplikacje SAP na platformie Azure: Obsługiwane produkty i typy](https://launchpad.support.sap.com/#/notes/1928533/E)maszyn wirtualnych platformy Azure.

> [!NOTE]
> SAP-Linux — integracja z platformą Azure jest obsługiwana tylko w Azure Resource Manager, a nie w klasycznym modelu wdrażania. 

## <a name="manual-installation-of-sap-hana"></a>Ręczna instalacja SAP HANA

> [!IMPORTANT]
> Upewnij się, że wybrany system operacyjny to SAP Certified for SAP HANA na określonych typach maszyn wirtualnych. Listę SAP HANA certyfikowane typy maszyn wirtualnych i wersje systemu operacyjnego dla tych typów maszyn wirtualnych można wyszukać w [SAP HANA certyfikowanych platformach IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Pamiętaj, aby kliknąć na liście szczegóły typu maszyny wirtualnej, aby uzyskać pełną listę obsługiwanych przez SAP HANA wydań systemu operacyjnego dla określonego typu maszyny wirtualnej. W przykładzie w tym dokumencie użyto wersji systemu operacyjnego SUSE Linux Enterprise Server (SLES), która nie jest obsługiwana przez oprogramowanie SAP dla SAP HANA na maszynach wirtualnych z serii M.
>

W tym przewodniku opisano, jak ręcznie instalować SAP HANA na maszynach wirtualnych platformy Azure na dwa różne sposoby:

* Użyj Menedżera aprowizacji oprogramowania SAP (SWPM) w ramach instalacji rozproszonej NetWeaver w kroku "Zainstaluj wystąpienie bazy danych".
* Użyj narzędzia Menedżer cyklu życia SAP HANA Database (HDBLCM), a następnie zainstaluj NetWeaver.

Programu SWPM można także użyć do zainstalowania wszystkich składników, takich jak SAP HANA, serwer aplikacji SAP i wystąpienie ASCS, w jednej pojedynczej maszynie wirtualnej. Kroki te są opisane w tym [SAP HANA ogłoszeniu w blogu](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ta opcja nie została opisana w tym przewodniku Szybki Start, ale problemy, które należy wziąć pod uwagę, są takie same.

Przed rozpoczęciem instalacji zaleca się przeczytanie sekcji "Przygotowywanie maszyn wirtualnych platformy Azure do ręcznej instalacji SAP HANA" w dalszej części tego przewodnika. Wykonanie tej czynności może pomóc zapobiec wystąpieniu kilku podstawowych błędów, które mogą wystąpić w przypadku korzystania tylko z domyślnej konfiguracji maszyny wirtualnej platformy Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Najważniejsze kroki instalacji SAP HANA w przypadku korzystania z oprogramowania SAP SWPM
W tej sekcji przedstawiono podstawowe kroki ręcznej instalacji SAP HANA pojedynczego wystąpienia podczas korzystania z oprogramowania SAP SWPM do wykonywania rozproszonej instalacji oprogramowania SAP NetWeaver 7,5. Poszczególne kroki zostały omówione bardziej szczegółowo na zrzutach ekranu w dalszej części tego przewodnika.

1. Utwórz sieć wirtualną platformy Azure obejmującą dwie testowe maszyny wirtualne.
2. Wdróż dwie maszyny wirtualne platformy Azure z systemami operacyjnymi zgodnie z modelem Azure Resource Manager. Ten przykład używa SUSE Linux Enterprise Server i SLES dla aplikacji SAP 12 z dodatkiem SP1. 
3. Dołącz dwa dyski usługi Azure Standard lub Premium Storage, na przykład dyski 75-GB lub 500-GB, do maszyny wirtualnej serwera aplikacji.
4. Dołącz dyski magazynu Premium Storage do maszyny wirtualnej serwera platformy HANA. Aby uzyskać więcej informacji, zobacz sekcję "Konfiguracja dysku" w dalszej części tego przewodnika.
5. W zależności od rozmiaru lub przepływności należy dołączyć wiele dysków. Następnie utwórz woluminy rozłożone. Użyj narzędzia do zarządzania woluminami logicznymi (LVM) lub narzędzi do administrowania wieloma urządzeniami (mdadm) na poziomie systemu operacyjnego w ramach maszyny wirtualnej.
6. Utwórz systemy plików XFS na podłączonych dyskach lub woluminach logicznych.
7. Zainstaluj nowe systemy plików XFS na poziomie systemu operacyjnego. Użyj jednego systemu plików dla wszystkich programów SAP. Użyj innego systemu plików dla katalogu/sapmnt i kopii zapasowych, na przykład. Na serwerze SAP HANA DB Zainstaluj systemy plików XFS na dyskach magazynu Premium Storage jako/Hana i/usr/SAP. Ten proces jest niezbędny, aby zapobiec wypełnieniu głównego systemu plików. Główny system plików nie jest duży na maszynach wirtualnych platformy Azure z systemem Linux. 
8. Wprowadź lokalne adresy IP testów maszyn wirtualnych w pliku/etc/hosts.
9. Wprowadź parametr **nofail** w pliku/etc/fstab.
10. Ustaw parametry jądra systemu Linux zgodnie z używaną wersją z systemem operacyjnym Linux. Aby uzyskać więcej informacji, zobacz uwagi dotyczące oprogramowania SAP, które omawiają platformę HANA i sekcję "parametry jądra" w tym przewodniku.
11. Dodaj obszar wymiany.
12. Opcjonalnie Zainstaluj graficzny pulpit na testowych maszynach wirtualnych. W przeciwnym razie należy użyć instalacji zdalnej SAPinst.
13. Pobierz oprogramowanie SAP z witryny SAP Service Marketplace.
14. Zainstaluj wystąpienie SAP ASCS na maszynie wirtualnej serwera aplikacji.
15. Udostępnij katalog/sapmnt między testowymi maszynami wirtualnymi przy użyciu systemu plików NFS. Maszyna wirtualna serwera aplikacji jest serwerem NFS.
16. Zainstaluj wystąpienie bazy danych, w tym HANA, za pomocą SWPM na maszynie wirtualnej serwera bazy danych.
17. Zainstaluj podstawowy serwer aplikacji (PAS) na maszynie wirtualnej serwera aplikacji.
18. Uruchom konsolę zarządzania SAP (SAP MC). Można na przykład połączyć się z graficznym interfejsem użytkownika SAP lub HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Najważniejsze kroki instalacji SAP HANA podczas korzystania z HDBLCM
W tej sekcji przedstawiono podstawowe kroki ręcznej instalacji SAP HANA pojedynczego wystąpienia podczas korzystania z oprogramowania SAP HDBLCM do wykonywania rozproszonej instalacji oprogramowania SAP NetWeaver 7,5. Poszczególne czynności zostały omówione bardziej szczegółowo na zrzutach ekranu w tym przewodniku.

1. Utwórz sieć wirtualną platformy Azure obejmującą dwie testowe maszyny wirtualne.
2. Wdróż dwie maszyny wirtualne platformy Azure z systemami operacyjnymi zgodnie z modelem Azure Resource Manager. W tym przykładzie zastosowano SLES i SLES dla aplikacji SAP 12 z dodatkiem SP1.
3. Dołącz dwa dyski usługi Azure Standard lub Premium Storage, na przykład dyski 75-GB lub 500-GB, do maszyny wirtualnej serwera aplikacji.
4. Dołącz dyski magazynu Premium Storage do maszyny wirtualnej serwera platformy HANA. Aby uzyskać więcej informacji, zobacz sekcję "Konfiguracja dysku" w dalszej części tego przewodnika.
5. W zależności od rozmiaru lub przepływności należy dołączyć wiele dysków. Utwórz woluminy rozłożone przy użyciu funkcji zarządzania woluminami logicznymi lub narzędzia mdadm na poziomie systemu operacyjnego w ramach maszyny wirtualnej.
6. Utwórz systemy plików XFS na podłączonych dyskach lub woluminach logicznych.
7. Zainstaluj nowe systemy plików XFS na poziomie systemu operacyjnego. Użyj jednego systemu plików dla wszystkich programów SAP. Użyj innego systemu plików dla katalogu/sapmnt i kopii zapasowych, na przykład. Na serwerze SAP HANA DB Zainstaluj systemy plików XFS na dyskach magazynu Premium Storage jako/Hana i/usr/SAP. Ten proces jest niezbędny, aby zapobiec wypełnieniu głównego systemu plików. Główny system plików nie jest duży na maszynach wirtualnych platformy Azure z systemem Linux.
8. Wprowadź lokalne adresy IP testów maszyn wirtualnych w pliku/etc/hosts.
9. Wprowadź parametr **nofail** w pliku/etc/fstab.
10. Ustaw parametry jądra zgodnie z używaną wersją systemu operacyjnego Linux. Aby uzyskać więcej informacji, zobacz uwagi dotyczące oprogramowania SAP, które omawiają platformę HANA i sekcję "parametry jądra" w tym przewodniku.
11. Dodaj obszar wymiany.
12. Opcjonalnie Zainstaluj graficzny pulpit na testowych maszynach wirtualnych. W przeciwnym razie należy użyć instalacji zdalnej SAPinst.
13. Pobierz oprogramowanie SAP z witryny SAP Service Marketplace.
14. Utwórz grupę sapsys z IDENTYFIKATORem grupy 1001 na maszynie wirtualnej serwera platformy HANA.
15. Zainstaluj SAP HANA na maszynie wirtualnej serwera bazy danych za pomocą Menedżera cyklu życia bazy danych HANA.
16. Zainstaluj wystąpienie SAP ASCS na maszynie wirtualnej serwera aplikacji.
17. Udostępnij katalog/sapmnt między testowymi maszynami wirtualnymi przy użyciu systemu plików NFS. Maszyna wirtualna serwera aplikacji jest serwerem NFS.
18. Zainstaluj wystąpienie bazy danych, w tym HANA, za pomocą SWPM na maszynie wirtualnej serwera usługi HANA DB.
19. Zainstaluj podstawowy serwer aplikacji na maszynie wirtualnej serwera aplikacji.
20. Uruchom oprogramowanie SAP MC. Nawiązywanie połączenia za pomocą interfejsu GUI oprogramowania SAP lub HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Przygotowywanie maszyn wirtualnych platformy Azure do ręcznej instalacji SAP HANA
W tej sekcji omówiono następujące tematy:

* Aktualizacje systemu operacyjnego
* Konfiguracja dysku
* Parametry jądra
* Systemy plików
* Plik/etc/hosts
* Plik/etc/fstab

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Przed zainstalowaniem dodatkowego oprogramowania Sprawdź aktualizacje systemu operacyjnego Linux i poprawki. Instalując poprawkę, możesz uniknąć rozmowy z pomocą techniczną.

Upewnij się, że używasz:
* SUSE Linux Enterprise Server dla aplikacji SAP.
* Red Hat Enterprise Linux dla aplikacji SAP lub Red Hat Enterprise Linux do SAP HANA. 

Jeśli jeszcze tego nie zrobiono, zarejestruj wdrożenie systemu operacyjnego w ramach subskrypcji z systemem Linux z poziomu dostawcy systemu Linux. SUSE zawiera obrazy systemu operacyjnego dla aplikacji SAP, które zawierają już usługi, które są rejestrowane automatycznie.

Oto przykład sposobu sprawdzania dostępności dostępnych poprawek dla systemu SUSE Linux przy użyciu polecenia **użyciu narzędzia zypper** :

 `sudo zypper list-patches`

W zależności od rodzaju problemu poprawki są klasyfikowane według kategorii i ważności. Najczęściej używane wartości dla kategorii są następujące: 
- Bezpieczeństwo
- Zalecane
- Optional
- Cecha
- Dokument
- Yast

Najczęściej używane wartości dla ważności to:

- Krytyczny
- Ważne
- Umiarkowane
- Małe
- Nieokreślona

**Użyciu narzędzia zypper** polecenie szuka tylko aktualizacji wymaganych przez zainstalowane pakiety. Można na przykład użyć tego polecenia:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Można dodać parametr `--dry-run` w celu przetestowania aktualizacji bez faktycznego aktualizowania systemu.


### <a name="disk-setup"></a>Konfiguracja dysku
Główny system plików na maszynie wirtualnej z systemem Linux na platformie Azure ma ograniczenie rozmiaru. Aby uruchomić oprogramowanie SAP, należy dołączyć dodatkowe miejsce na dysku do maszyny wirtualnej platformy Azure. W przypadku maszyn wirtualnych platformy Azure z serwerem aplikacji SAP użycie dysków usługi Azure Storage w warstwie Standardowa może być wystarczające. W przypadku maszyn wirtualnych platformy Azure z systemem SAP HANA DBMS użycie dysków usługi Azure Premium Storage na potrzeby implementacji produkcji i nieprodukcyjnej jest obowiązkowe.

W oparciu o [wymagania dotyczące magazynu TDI SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)zasugerowana jest następująca konfiguracja usługi Azure Premium Storage: 

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM |  /Hana/Data i/Hana/log <br /> rozłożone z LVM lub mdadm | /hana/shared | wolumin/root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

W przypadku sugerowanej konfiguracji dysków wolumin danych i dziennik platformy HANA są umieszczane w tym samym zestawie dysków usługi Azure Premium Storage, które są rozłożone na LVM lub mdadm. Nie jest konieczne zdefiniowanie żadnego poziomu nadmiarowości RAID, ponieważ usługa Azure Premium Storage utrzymuje trzy obrazy dysków w celu zapewnienia nadmiarowości. 

Aby upewnić się, że skonfigurowano wystarczającą ilość miejsca, zobacz [SAP HANA wymagania dotyczące magazynu TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) i [instalacji serwera SAP HANA i podręczniku aktualizacji](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Należy również wziąć pod uwagę różne woluminy przepływności wirtualnych dysków twardych (VHD) różnych dysków usługi Azure Premium Storage, które opisano w [magazynach Premium Storage o wysokiej wydajności i dyskach zarządzanych dla maszyn wirtualnych](../../windows/disks-types.md). 

Do maszyn wirtualnych systemu HANA DBMS można dodać więcej dysków magazynu w warstwie Premium w celu przechowywania kopii zapasowych bazy danych lub dziennika transakcji.

Aby uzyskać więcej informacji na temat dwóch głównych narzędzi służących do konfigurowania rozłożenia, zobacz:

* [Skonfiguruj oprogramowanie RAID w systemie Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Konfigurowanie LVM na maszynie wirtualnej z systemem Linux na platformie Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać więcej informacji na temat dołączania dysków do maszyn wirtualnych platformy Azure z systemem Linux jako systemu operacyjnego gościa, zobacz [Dodawanie dysku do maszyny wirtualnej z systemem Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Za pomocą usługi Azure Premium dysków SSD można definiować tryby buforowania dysku. W przypadku zestawu rozłożonego, który zawiera/Hana/Data i/Hana/log, Wyłącz buforowanie dysków. W przypadku innych woluminów, czyli dysków, ustaw tryb buforowania na **tylko do odczytu**.

Aby znaleźć przykładowe szablony JSON do użycia podczas tworzenia maszyn wirtualnych, zobacz [Szablony szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
Szablon VM-Simple-SLES jest podstawowym szablonem. Zawiera sekcję magazynu z dodatkowym dyskiem danych 100 GB. Użyj tego szablonu jako podstawy. Szablon można dostosować do określonej konfiguracji.

> [!NOTE]
> Ważne jest, aby dołączyć dysk usługi Azure Storage przy użyciu identyfikatora UUID, zgodnie z opisem w temacie [Uruchamianie oprogramowania SAP NetWeaver Microsoft Azure na maszynach wirtualnych z systemem SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W środowisku testowym dwa dyski usługi Azure Standard Storage są dołączone do maszyny wirtualnej serwera aplikacji SAP, jak pokazano na poniższym zrzucie ekranu. Na jednym dysku jest przechowywane wszystkie oprogramowanie SAP, takie jak NetWeaver 7,5, graficzny interfejs użytkownika oprogramowania SAP i SAP HANA, na potrzeby instalacji. Drugi dysk zapewnia wystarczającą ilość wolnego miejsca na potrzeby dodatkowych wymagań. Przykładowo dane kopii zapasowej i testów oraz katalog/sapmnt, czyli profile SAP, muszą być współużytkowane przez wszystkie maszyny wirtualne, które należą do tego samego środowiska SAP.

![Okno SAP HANA dysków aplikacji zawierające dwa dyski z danymi i ich rozmiary](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametry jądra
SAP HANA wymaga określonych ustawień jądra systemu Linux. Te ustawienia jądra nie są częścią standardowych obrazów galerii platformy Azure i muszą być ustawione ręcznie. W zależności od tego, czy używasz SUSE, czy Red Hat, parametry mogą być różne. Wymienione wcześniej uwagi SAP zawierają informacje o tych parametrach. Na wyświetlone zrzuty ekranu użyto systemu SUSE Linux 12 SP1. 

SLES for SAP Applications 12 General Availability i SLES for SAP Applications 12 SP1 mają nowe narzędzie, **dostrojony-adm**, które zastępuje stare narzędzie **sapconf** . Specjalny profil SAP HANA jest dostępny dla dostrajania **-adm**. Aby dostosować system do SAP HANA, wprowadź następujący profil jako użytkownik główny:

   `tuned-adm profile sap-hana`

Aby uzyskać więcej informacji na temat dostrajania **-adm**, zobacz [dokumentację SUSE na temat dostrajania-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na poniższym zrzucie ekranu można zobaczyć, jak **dostrojony/adm** zmienił `transparent_hugepage` wartości i `numa_balancing` , zgodnie z wymaganymi SAP HANA ustawieniami:

![Dostrajane-adm Narzędzie zmienia wartości zgodnie z wymaganiami wymaganych SAP HANA ustawień](./media/hana-get-started/image005.jpg)

Aby ustawienia jądra SAP HANA trwałe, należy użyć **grub2** w SLES 12. Aby uzyskać więcej informacji na temat **grub2**, zobacz sekcję dotyczącą [struktury plików konfiguracji](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) w dokumentacji SUSE.

Poniższy zrzut ekranu pokazuje, w jaki sposób ustawienia jądra zostały zmienione w pliku konfiguracji, a następnie skompilowane przy użyciu polecenia **grub2-mkconfig**:

![Ustawienia jądra zostały zmienione w pliku konfiguracji i skompilowane przy użyciu polecenia grub2-mkconfig](./media/hana-get-started/image006.jpg)

Inną opcją jest zmiana ustawień przy użyciu YaST i**parametrów jądra** **modułu ładującego** > rozruchu:

![Karta Ustawienia parametrów jądra w module ładującym YaST Boot](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systemy plików
Poniższy zrzut ekranu przedstawia dwa systemy plików, które zostały utworzone na maszynie wirtualnej programu SAP App Server, na podstawie dwóch dołączonych dysków usługi Azure Storage w warstwie Standardowa. Oba systemy plików są typu XFS i są zainstalowane do/sapdata i/sapsoftware.

Nie jest to wymagane do struktury systemów plików w ten sposób. Dostępne są inne opcje struktury miejsca na dysku. Najważniejszym zagadnieniem jest uniemożliwienie braku wolnego miejsca w głównym systemie plików.

![Dwa systemy plików utworzone na maszynie wirtualnej serwera aplikacji SAP](./media/hana-get-started/image008.jpg)

W przypadku maszyny wirtualnej SAP HANA DB podczas instalacji bazy danych w przypadku używania SAPinst z SWPM i typową opcją instalacji wszystko jest instalowane w obszarze/Hana i/usr/SAP. Domyślna lokalizacja SAP HANA kopii zapasowej dziennika znajduje się w obszarze/usr/SAP. Należy ponownie pamiętać, aby zapobiec wykorzystaniu miejsca do magazynowania przez główny system plików. Przed zainstalowaniem SAP HANA przy użyciu SWPM upewnij się, że jest wystarczająca ilość wolnego miejsca pod/Hana i/usr/SAP.

Aby uzyskać opis standardowego układu systemu plików SAP HANA, zobacz [Przewodnik instalacji i aktualizacji serwera SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Dodatkowe systemy plików utworzone na maszynie wirtualnej serwera aplikacji SAP](./media/hana-get-started/image009.jpg)

Po zainstalowaniu oprogramowania SAP NetWeaver na standardowym obrazie SLES/SLES for SAP Applications 12 Azure Gallery zostanie wyświetlony komunikat z informacją, że nie ma obszaru wymiany, jak pokazano na poniższym zrzucie ekranu. Aby odrzucić ten komunikat, można ręcznie dodać plik wymiany przy użyciu **DD**, **mkswap**i **swapon**. Aby dowiedzieć się, jak znaleźć "Dodawanie pliku wymiany ręcznie" w sekcji [using YaST Partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) dokumentacji SUSE.

Innym rozwiązaniem jest skonfigurowanie obszaru wymiany przy użyciu agenta maszyny wirtualnej z systemem Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../../extensions/agent-linux.md).

![Komunikat podręczny informujący o braku wystarczającej ilości miejsca w wymianie](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Plik/etc/hosts
Przed rozpoczęciem instalacji SAP upewnij się, że w pliku/etc/hosts zostały uwzględnione nazwy hostów i adresy IP maszyn wirtualnych SAP. Wdróż wszystkie maszyny wirtualne SAP w jednej sieci wirtualnej platformy Azure. Następnie użyj wewnętrznych adresów IP, jak pokazano poniżej:

![Nazwy hostów i adresy IP maszyn wirtualnych SAP wymienionych w pliku/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Plik/etc/fstab

Warto dodać parametr nofail do pliku fstab. W ten sposób, jeśli coś się nie powiedzie z dyskami, maszyna wirtualna nie przestanie odpowiadać w procesie rozruchu. Należy jednak pamiętać, że dodatkowe miejsce na dysku może nie być dostępne i procesy mogą wypełnić główny system plików. Jeśli brakuje/Hana, SAP HANA nie zostanie uruchomiona.

![Dodaj parametr nofail do pliku fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Graficzny Pulpit GNOME na SLES 12/SLES dla aplikacji SAP 12
W tej sekcji wyjaśniono, jak:

* Zainstaluj Pulpit GNOME i xrdp na SLES 12/SLES dla aplikacji SAP 12.
* Uruchamianie opartego na języku Java platformy SAP MC przy użyciu przeglądarki Firefox na SLES 12/SLES dla aplikacji SAP 12.

Można również użyć alternatyw, takich jak Xterminal lub VNC, które nie zostały opisane w tym przewodniku.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Zainstaluj Pulpit GNOME i xrdp na SLES 12/SLES dla aplikacji SAP 12
Jeśli masz tło systemu Windows, możesz łatwo użyć graficznego pulpitu bezpośrednio w maszynach wirtualnych SAP Linux do uruchamiania programu Firefox, SAPinst, interfejsu GUI SAP, SAP MC lub HANA Studio. Następnie można nawiązać połączenie z maszyną wirtualną za pośrednictwem Remote Desktop Protocol (RDP) z komputera z systemem Windows. W zależności od zasad firmy dotyczących dodawania GUI do systemów produkcyjnych i niezwiązanych z systemem Linux możesz chcieć zainstalować GNOME na serwerze. Wykonaj następujące kroki, aby zainstalować Pulpit GNOME na platformie Azure SLES 12/SLES for SAP Applications 12 VM.

1. Zainstaluj Pulpit GNOME, wprowadzając następujące polecenie, na przykład w oknie polecenia:

   `zypper in -t pattern gnome-basic`

2. Zainstaluj xrdp, aby zezwolić na połączenie z maszyną wirtualną za pomocą protokołu RDP:

   `zypper in xrdp`

3. Edytuj/etc/sysconfig/Windowmanager i ustaw domyślnego menedżera okien na GNOME:

   `DEFAULT_WM="gnome"`

4. Uruchom **chkconfig** , aby upewnić się, że xrdp jest uruchamiany automatycznie po ponownym uruchomieniu:

   `chkconfig -level 3 xrdp on`

5. Jeśli masz problem z połączeniem RDP, spróbuj ponownie uruchomić program, na przykład z okna wydawania:

   `/etc/xrdp/xrdp.sh restart`

6. Jeśli ponowne uruchomienie xrdp wymienione w poprzednim kroku nie działa, sprawdź, czy jest to plik. PID:

   `check /var/run` 

   `xrdp.pid`Wyszukaj. Jeśli go znajdziesz, usuń go, a następnie ponów próbę ponownego uruchomienia.

### <a name="start-sap-mc"></a>Uruchom oprogramowanie SAP MC
Po zainstalowaniu pulpitu GNOME należy uruchomić graficzny program SAP MC oparty na języku Java z przeglądarki Firefox. Jeśli działa na maszynie wirtualnej Azure SLES 12/SLES for SAP Applications 12, może zostać wyświetlony komunikat o błędzie. Błąd występuje z powodu braku wtyczki przeglądarki Java.

Adres URL do uruchomienia oprogramowania SAP MC to `<server>:5<instance_number>13`.

Aby uzyskać więcej informacji, zobacz [Uruchamianie konsoli zarządzania SAP opartej na sieci Web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Poniższy zrzut ekranu przedstawia komunikat o błędzie wyświetlany, gdy brakuje wtyczki przeglądarki Java:

![Komunikat o błędzie informujący o braku wtyczki Java-Browser](./media/hana-get-started/image013.jpg)

Jednym ze sposobów rozwiązania problemu jest zainstalowanie brakującej wtyczki przy użyciu YaST, jak pokazano na poniższym zrzucie ekranu:

![Instalowanie brakującej wtyczki przy użyciu YaST](./media/hana-get-started/image014.jpg)

Po ponownym wprowadzeniu adresu URL konsoli zarządzania SAP zostanie wyświetlony monit o aktywację wtyczki:

![Okno dialogowe z żądaniem aktywacji wtyczki](./media/hana-get-started/image015.jpg)

Może również pojawić się komunikat o błędzie dotyczący brakującego pliku JavaFX. Properties. Odnosi się do wymogu programu Oracle Java 1,8 for SAP graficzny interfejs użytkownika 7,4. Aby uzyskać więcej informacji, zobacz temat [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424).
Wersja programu IBM Java i pakiet OpenJDK, który jest dostarczany z SLES/SLES dla aplikacji SAP 12 nie obejmuje potrzebnego pliku JavaFX. Properties. Rozwiązaniem jest pobranie i zainstalowanie języka Java SE 8 z firmy Oracle.

Aby uzyskać informacje o podobnym problemie związanym z OpenJDK na openSUSE, zobacz wątek dyskusji [SAPGui 7,4 Java dla openSUSE 42,1](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Ręczna instalacja SAP HANA: SWPM
W serii zrzutów ekranu w tej sekcji przedstawiono podstawowe kroki instalacji oprogramowania SAP NetWeaver 7,5 i SAP HANA SP12 w przypadku używania SWPM z SAPinst. W ramach instalacji programu NetWeaver 7,5, SWPM także może zainstalować bazę danych HANA jako pojedyncze wystąpienie.

W przykładowym środowisku testowym zainstalowano jeden zaawansowany serwer aplikacji programistycznych (ABAP). Jak pokazano na poniższym zrzucie ekranu, użyto opcji **systemu rozproszonego** do zainstalowania wystąpień ASCS i głównych serwerów aplikacji na jednej maszynie wirtualnej platformy Azure. Użyto SAP HANA jako systemu bazy danych na innej maszynie wirtualnej platformy Azure.

![ASCS i główne wystąpienia serwera aplikacji instalowane przy użyciu opcji systemu rozproszonego](./media/hana-get-started/image012.jpg)

Po zainstalowaniu wystąpienia ASCS na maszynie wirtualnej serwera aplikacji jest ono identyfikowane przez zieloną ikonę w konsoli zarządzania SAP. Katalog/sapmnt, który obejmuje katalog profilu SAP, musi być współużytkowany z maszyną wirtualną serwera SAP HANA DB. Krok instalacji bazy danych wymaga dostępu do tych informacji. Najlepszym sposobem zapewnienia dostępu jest użycie systemu plików NFS, który można skonfigurować za pomocą YaST.

![Konsola zarządzania SAP pokazująca wystąpienie ASCS zainstalowane na maszynie wirtualnej serwera aplikacji przy użyciu zielonej ikony](./media/hana-get-started/image016.jpg)

Na maszynie wirtualnej serwera aplikacji katalog/sapmnt jest udostępniany za pośrednictwem systemu plików NFS przy użyciu opcji **RW** i **no_root_squash** . Wartości domyślne to **ro** i **root_squash**, co może prowadzić do problemów podczas instalacji wystąpienia bazy danych.

![Udostępnianie katalogu/sapmnt za pośrednictwem systemu plików NFS przy użyciu opcji RW i no_root_squash](./media/hana-get-started/image017b.jpg)

Jak następny zrzut ekranu pokazuje, udział/sapmnt z maszyny wirtualnej serwera aplikacji musi być skonfigurowany na maszynie wirtualnej serwera SAP HANA DB przy użyciu **klienta NFS** i YaST:

![Udział/sapmnt skonfigurowany przy użyciu klienta NFS](./media/hana-get-started/image018b.jpg)

Aby przeprowadzić instalację rozproszonego NetWeaver 7,5, czyli **wystąpienie bazy danych**, zaloguj się do maszyny wirtualnej serwera SAP HANA DB i uruchom SWPM:

![Instalowanie wystąpienia bazy danych przez zalogowanie się do maszyny wirtualnej serwera SAP HANA DB i uruchomienie SWPM](./media/hana-get-started/image019.jpg)

Po wybraniu opcji instalacja **standardowa** i ścieżka do nośnika instalacyjnego wprowadź identyfikator SID bazy danych, nazwę hosta, numer wystąpienia i hasło administratora systemu bazy danych:

![Strona logowania administratora systemu bazy danych SAP HANA](./media/hana-get-started/image035b.jpg)

Wprowadź hasło dla schematu DBACOCKPIT:

![Pole wpisu hasła dla schematu DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Wprowadź pytanie dotyczące hasła schematu SAPABAP1:

![Wprowadź pytanie dotyczące hasła schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu każdego zadania zostanie wyświetlony zielony znacznik wyboru obok każdej fazy procesu instalacji bazy danych. Komunikat "wykonywanie... Wystąpienie bazy danych zostało zakończone.

![Okno zadania zakończone z komunikatem potwierdzającym](./media/hana-get-started/image023.jpg)

Po pomyślnej instalacji Konsola zarządzania SAP również pokazuje wystąpienie bazy danych z zieloną ikoną. Wyświetla pełną listę procesów SAP HANA, takich jak hdbindexserver i hdbcompileserver.

![Okno konsoli zarządzania SAP z listą procesów SAP HANA](./media/hana-get-started/image024.jpg)

Poniższy zrzut ekranu przedstawia część struktury plików w katalogu/Hana/Shared, która SWPM utworzona podczas instalacji platformy HANA. Ponieważ nie ma opcji określania innej ścieżki, ważne jest, aby zainstalować dodatkowe miejsce na dysku w katalogu/Hana przed instalacją SAP HANA przy użyciu SWPM. Ten krok uniemożliwia brak wolnego miejsca w głównym systemie plików.

![Struktura pliku/Hana/Shared Directory utworzona podczas instalacji platformy HANA](./media/hana-get-started/image025.jpg)

Ten zrzut ekranu przedstawia strukturę plików katalogu/usr/SAP:

![Struktura pliku katalogu/usr/SAP](./media/hana-get-started/image026.jpg)

Ostatnim krokiem instalacji rozproszonej ABAP jest zainstalowanie podstawowego wystąpienia serwera aplikacji:

![Instalacja ABAP pokazująca wystąpienie podstawowego serwera aplikacji jako ostatni krok](./media/hana-get-started/image027b.jpg)

Po zainstalowaniu podstawowego wystąpienia serwera aplikacji i graficznego interfejsu użytkownika SAP Użyj transakcji **Panelu sterowania dba** , aby upewnić się, że instalacja SAP HANA zakończyła się pomyślnie:

![Okno Panel sterowania DBA o pomyślne zainstalowanie](./media/hana-get-started/image028b.jpg)

Ostatnim krokiem może być zainstalowanie platformy HANA Studio na maszynie wirtualnej serwera aplikacji SAP. Następnie połącz się z wystąpieniem SAP HANA uruchomionym na maszynie wirtualnej serwera bazy danych.

![Instalowanie SAP HANA Studio na maszynie wirtualnej serwera aplikacji SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Ręczna instalacja SAP HANA: HDBLCM
Oprócz instalowania SAP HANA w ramach instalacji rozproszonej przy użyciu programu SWPM, można najpierw zainstalować autonomiczną platformę HANA przy użyciu HDBLCM. Następnie można zainstalować oprogramowanie SAP NetWeaver 7,5, na przykład. Zrzuty ekranu przedstawione w tej sekcji przedstawiają sposób działania tego procesu.

Więcej informacji o narzędziu HANA HDBLCM można znaleźć w temacie:

* [Wybierz poprawną SAP HANA HDBLCM dla zadania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP HANA narzędzia do zarządzania cyklem życia](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Przewodnik instalacji i aktualizacji serwera SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Chcesz uniknąć problemów z domyślnym ustawieniem identyfikatora grupy dla `\<HANA SID\>adm user`, które jest tworzone przez narzędzie HDBLCM. Przed zainstalowaniem SAP HANA za pośrednictwem HDBLCM należy zdefiniować nową `sapsys` grupę o nazwie przy `1001`użyciu identyfikatora grupy:

![Nowa grupa "sapsys" zdefiniowana za pomocą grupy o IDENTYFIKATORze 1001](./media/hana-get-started/image030.jpg)

Po pierwszym uruchomieniu HDBLCM zostanie wyświetlone proste menu Start. Wybierz pozycję element 1, **Zainstaluj nowy system**:

![Opcja "Zainstaluj nowy system" w oknie uruchamiania HDBLCM](./media/hana-get-started/image031.jpg)

Poniższy zrzut ekranu przedstawia wszystkie opcje klucza wybrane wcześniej.

> [!IMPORTANT]
> Katalogi, które są nazywane woluminami dzienników i danych platformy HANA, a ścieżką instalacji, która jest/Hana/Shared w tym przykładzie, i/usr/SAP nie mogą być częścią głównego systemu plików. Te katalogi należą do dysków danych platformy Azure dołączonych do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz sekcję "Konfiguracja dysku". 

Takie podejście pomaga zapobiec braku miejsca w głównym systemie plików. Zwróć uwagę, że administrator systemu Hana ma identyfikator `1005` użytkownika i jest częścią `sapsys` grupy o identyfikatorze `1001`, która została zdefiniowana przed instalacją.

![Lista wszystkich wybranych wcześniej składników SAP HANA najważniejszych](./media/hana-get-started/image032.jpg)

`\<HANA SID\>adm user` Sprawdź szczegóły w katalogu/etc/passwd. `azdadm`Wyszukaj, jak pokazano na poniższym zrzucie ekranu:

![Informacje o użytkowniku adm\> HanaSIDusługiwkatalogu/etc/passwd\<](./media/hana-get-started/image033.jpg)

Po zainstalowaniu SAP HANA za pomocą HDBLCM można zobaczyć strukturę plików w programie SAP HANA Studio, jak pokazano na poniższym zrzucie ekranu. Schemat SAPABAP1, który zawiera wszystkie tabele SAP NetWeaver, nie jest jeszcze dostępny.

![Struktura plików SAP HANA w programie SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po zainstalowaniu SAP HANA można zainstalować na nim oprogramowanie SAP NetWeaver. Jak pokazano na poniższym zrzucie ekranu, instalacja została przeprowadzona jako Instalacja rozproszona przy użyciu SWPM. Ten proces jest opisany w poprzedniej sekcji. Podczas instalowania wystąpienia bazy danych za pomocą SWPM należy wprowadzić te same dane przy użyciu HDBLCM. Na przykład wprowadź nazwę hosta, identyfikator SID HANA i numer wystąpienia. SWPM następnie używa istniejącej instalacji platformy HANA i dodaje więcej schematów.

![Instalacja rozproszona wykonywana przy użyciu SWPM](./media/hana-get-started/image035b.jpg)

Poniższy zrzut ekranu przedstawia krok instalacji SWPM, w którym wprowadzane są dane dotyczące schematu DBACOCKPIT:

![Krok instalacji SWPM, w którym wprowadzono dane schematu DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Wprowadź dane dotyczące schematu SAPABAP1:

![Wprowadzanie danych dotyczących schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu instalacji wystąpienia bazy danych SWPM można zobaczyć schemat SAPABAP1 w programie SAP HANA Studio:

![Schemat SAPABAP1 w programie SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Na koniec po zakończeniu instalacji serwera aplikacji SAP i graficznego interfejsu użytkownika SAP Sprawdź wystąpienie bazy danych HANA przy użyciu transakcji **Panelu sterowania** usługi firmy dba:

![Wystąpienie bazy danych HANA zostało zweryfikowane przy użyciu transakcji panelu sterowania DBA](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Pobieranie oprogramowania SAP
Oprogramowanie można pobrać z witryny Marketplace usługi SAP, jak pokazano na poniższych zrzutach ekranu.

Pobierz NetWeaver 7,5 dla systemu Linux/HANA:

 ![Okno instalacji i uaktualniania usługi SAP na potrzeby pobierania NetWeaver 7,5](./media/hana-get-started/image001.jpg)

Pobieranie platformy HANA SP12 platform Edition:

 ![Okno instalacji i uaktualniania usługi SAP na potrzeby pobierania programu HANA SP12 platform Edition](./media/hana-get-started/image002.jpg)

