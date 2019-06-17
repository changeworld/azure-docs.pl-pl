---
title: 'Szybki start: Ręczna instalacja jednego wystąpienia SAP HANA na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft'
description: Przewodnik Szybki start dotyczący instalacji ręcznej jednego wystąpienia oprogramowania SAP Hana na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60205055"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Szybki start: Ręczna instalacja jednego wystąpienia SAP HANA na maszynach wirtualnych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Ten przewodnik ułatwia konfigurowanie jednego wystąpienia SAP HANA na maszynach wirtualnych platformy Azure, gdy należy zainstalować oprogramowanie SAP NetWeaver w wersji 7.5 i SAP HANA 1.0 SP12 ręcznie. Ten przewodnik koncentruje się na temat wdrażania oprogramowania SAP HANA na platformie Azure. Go nie zastępuje dokumentacji SAP. 

> [!NOTE]
> W tym przewodniku opisano wdrażanie oprogramowania SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać informacje na temat wdrażania oprogramowania SAP HANA w dużych wystąpień HANA, zobacz [użycia adapterów SAP na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że znasz takich infrastruktury jako podstawy usługi (IaaS), jako:
 * Jak wdrożyć maszyny wirtualne (VM) lub sieci wirtualnych za pośrednictwem witryny Azure portal lub programu PowerShell.
 * Azure dla wielu platform interfejsu wiersza polecenia (CLI), która zawiera opcję, aby użyć szablonów JavaScript Object Notation (JSON).

W tym przewodniku założono również, że znasz:
* SAP HANA i oprogramowania SAP NetWeaver i sposobie ich instalowania w środowisku lokalnym.
* Jak do zainstalowania i obsługi oprogramowania SAP HANA i wystąpieniom aplikacji SAP na platformie Azure.
* Następujące pojęcia i procedury:
   * Planowanie wdrożenia SAP na platformie Azure, która obejmuje usługę Azure Virtual Network planowania i użytkowania usługi Azure Storage. Zobacz [oprogramowanie SAP NetWeaver na maszynach wirtualnych platformy Azure — przewodnik planowania i implementowania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Wdrażanie zasad i sposoby wdrażania maszyn wirtualnych na platformie Azure. Zobacz [wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Wysoka dostępność dla SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) i umieścić w kolejce replikacji serwera (Wywołujących) na platformie Azure. Zobacz [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Szczegółowe informacje na temat sposobu poprawy wydajności w przypadku instalacji — wiele identyfikatorów SID ASCS/SCS na platformie Azure. Zobacz [tworzenia konfiguracji — wiele identyfikatorów SID oprogramowanie SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Zasady z oprogramowaniem SAP NetWeaver na podstawie opartych na systemie Linux maszyn wirtualnych na platformie Azure. Zobacz [uruchamiania oprogramowania SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ten przewodnik zawiera konkretne ustawienia dla systemu Linux na maszynach wirtualnych Azure. Daje ona również informacje o tym, jak poprawnie dołączyć dyski usługi Azure storage do maszyn wirtualnych systemu Linux.

Typy maszyn wirtualnych platformy Azure, których można użyć na potrzeby scenariuszy produkcyjnych są wymienione w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy obniżenia szerszy zakres natywnych typów maszyn wirtualnych platformy Azure jest dostępna.
Aby uzyskać więcej informacji na temat konfiguracji maszyny Wirtualnej i operacje, zobacz [konfiguracje infrastruktury SAP HANA i operacji na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
SAP HANA wysoką dostępność, zobacz [platformy SAP HANA wysoką dostępność usługi Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Jeśli chcesz uzyskać wystąpienie SAP HANA lub S/4HANA lub BW/4HANA system szybko wdrożone, należy rozważyć użycie [SAP Cloud Appliance Library](https://cal.sap.com). Można znaleźć dokumentację, jak wdrożyć system oprogramowania S/4HANA, przy użyciu biblioteki SAP Cloud Appliance Library na platformie Azure, na przykład w [tego przewodnika](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). To wszystko, czego potrzebujesz subskrypcji platformy Azure i użytkownik SAP, który może być zarejestrowane przy użyciu biblioteki SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Dodatkowe zasoby
### <a name="sap-hana-backup"></a>Kopia zapasowa oprogramowania SAP HANA
Aby uzyskać informacje na temat sposobu tworzenia kopii zapasowej baz danych SAP HANA na maszynach wirtualnych platformy Azure zobacz:
* [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA usługi Azure Backup na poziomie plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Biblioteki SAP Cloud Appliance Library
Aby uzyskać informacje dotyczące sposobu używania biblioteki SAP Cloud Appliance Library do wdrażania oprogramowania S/4HANA lub BW/4HANA, zobacz [wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA w systemie Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Systemy operacyjne obsługiwane HANA SAP
Aby uzyskać informacji na temat oprogramowania SAP HANA, obsługiwane systemy operacyjne, zobacz [2235581 Uwaga SAP — SAP HANA: Obsługiwane systemy operacyjne](https://launchpad.support.sap.com/#/notes/2235581/E). Maszyny wirtualne platformy Azure obsługuje tylko podzbiór tych systemów operacyjnych. Do wdrażania oprogramowania SAP HANA na platformie Azure są obsługiwane następujące systemy operacyjne: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Aby uzyskać dodatkowej dokumentacji SAP o platformy SAP HANA i różnych systemów operacyjnych Linux zobacz:

* [SAP Note 171356: SAP Software on Linux: Informacje ogólne](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP Note 1944799: SAP HANA wskazówki dotyczące instalacji systemu operacyjnego w systemie SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [Uwaga SAP 2205917: Bazy danych SAP HANA zalecane ustawienia systemu operacyjnego systemu SLES 12 dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP Note 1391070: Identyfikator UUID systemu Linux rozwiązania](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP Note 2009879: SAP HANA wytyczne dotyczące systemu operacyjnego Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP Note 2292690: SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP monitorowanie na platformie Azure
Aby dowiedzieć się, jak SAP monitorowanie na platformie Azure:

* [2191498 Uwaga SAP](https://launchpad.support.sap.com/#/notes/2191498/E) w tym artykule omówiono SAP rozszerzone monitorowanie za pomocą maszyn wirtualnych systemu Linux na platformie Azure. 
* [1102124 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1102124/E) omówiono SAPOSCOL w systemie Linux. 
* [2178632 Uwaga SAP](https://launchpad.support.sap.com/#/notes/2178632/E) omówiono kluczowe metryki monitorowania dla rozwiązania SAP w systemie Microsoft Azure.

### <a name="azure-vm-types"></a>Typy maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure i scenariusze SAP, obsługiwane obciążenie używane z platformą SAP HANA są udokumentowane w artykule [platform IaaS certyfikat SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Usługa Azure typy maszyn wirtualnych, które są certyfikowane przez firmę SAP dla oprogramowania SAP NetWeaver i S/4HANA warstwy aplikacji są udokumentowane w artykule [1928533 Uwaga SAP: Aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> Integracja SAP, Linux, Azure jest obsługiwana tylko w usłudze Azure Resource Manager i nie klasycznego modelu wdrażania. 

## <a name="manual-installation-of-sap-hana"></a>Instalacja ręczna oprogramowania SAP Hana

> [!IMPORTANT]
> Upewnij się, że system operacyjny, należy wybrać w określonych typów maszyn wirtualnych, których można używać z certyfikatem platformy SAP Hana SAP. Lista oprogramowania SAP HANA certyfikowane typy maszyn wirtualnych i system operacyjny w wersji dla tych typów maszyn wirtualnych można wyszukiwać [platform IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Upewnij się, że kliknij przycisk Szczegóły typu maszyny Wirtualnej, aby uzyskać pełną listę wersji obsługiwane platformy SAP HANA systemu operacyjnego dla określonego typu maszyny Wirtualnej na liście. Na przykład w tym dokumencie użyliśmy wersji SUSE Linux Enterprise Server (SLES) systemu operacyjnego, która nie jest obsługiwana przez firmę SAP dla oprogramowania SAP HANA na maszynach wirtualnych serii M.
>

Ten przewodnik zawiera opis sposobu ręcznie zainstalować oprogramowanie SAP HANA na maszynach wirtualnych platformy Azure na dwa sposoby:

* Użyj Menedżera inicjowania obsługi oprogramowania SAP (SWPM) jako część instalacji rozproszonej NetWeaver w kroku "Instalacja wystąpienia bazy danych".
* Narzędzie Menedżer (HDBLCM) cyklu życia bazy danych SAP HANA, a następnie zainstalowanie NetWeaver.

SWPM umożliwia także instalację wszystkich składników, takich jak SAP HANA, serwera aplikacji SAP i wystąpienia ASCS, w jedną pojedynczą maszynę Wirtualną. Kroki opisane w tym [ogłoszenia na blogu platformy SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ta opcja nie jest opisane w tym przewodniku Szybki Start, ale problemy, które należy wziąć pod uwagę są takie same.

Przed rozpoczęciem instalacji, zalecamy przeczytanie "Przygotowanie maszyn wirtualnych platformy Azure dotyczące ręcznej instalacji oprogramowania SAP Hana" sekcję w dalszej części tego przewodnika. Ten sposób może pomóc w uniknięciu kilka podstawowych błędów, które mogą wystąpić, gdy używasz tylko domyślną konfigurację maszyny Wirtualnej platformy Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Kluczowe kroki instalacji oprogramowania SAP HANA, korzystając z SAP SWPM
W tej sekcji przedstawiono kluczowe kroki ręczne, jednego wystąpienia instalacji oprogramowania SAP HANA, korzystając z SAP SWPM do przeprowadzenia instalacji rozproszonej SAP NetWeaver w wersji 7.5. Poszczególne kroki zostały omówione bardziej szczegółowo w zrzuty ekranu w dalszej części tego przewodnika.

1. Tworzenie sieci wirtualnej platformy Azure, który zawiera test dwóch maszyn wirtualnych.
2. Wdrażanie dwóch maszyn wirtualnych platformy Azure z systemami operacyjnymi, zgodnie z modelem usługi Azure Resource Manager. W tym przykładzie używa SUSE Linux Enterprise Server i SLES for SAP aplikacji 12 z dodatkiem SP1. 
3. Dołącz dwa standardowe platformy Azure lub dysków magazynu premium storage, na przykład dysków 75 GB i 500 GB, serwerowi aplikacji maszyny Wirtualnej.
4. Dołączanie dysków magazynu premium storage do serwera bazy danych HANA maszyny Wirtualnej. Aby uzyskać więcej informacji zobacz sekcję "Dysku setup" w dalszej części tego przewodnika.
5. W zależności od wymagań rozmiar lub przepływność dołączając wiele dysków. Następnie można utworzyć woluminy rozłożone. Wewnątrz maszyny Wirtualnej na poziomie systemu operacyjnego, należy użyć zarządzania woluminów logicznych (LVM) lub narzędzia administracji (mdadm) wielu urządzeń.
6. Utwórz XFS systemy plików na woluminach logicznych i dołączonych dysków.
7. Zainstaluj nowych systemów plików XFS na poziomie systemu operacyjnego. Używaj jednego systemu plików dla oprogramowania SAP. Użyj systemu plików do katalogu /sapmnt i kopie zapasowe, na przykład. Na serwerze bazy danych SAP HANA należy zainstalować XFS systemy plików na dyskach magazynu premium storage jako /hana i /usr/sap. Ten proces jest niezbędne zapobiec zapełnianiu głównego systemu plików. Główny system plików nie jest duży na maszynach wirtualnych Azure z systemem Linux. 
8. Wprowadź lokalne adresy IP testowych maszyn wirtualnych w pliku/etc/hosts.
9. Wprowadź **nofail** parametru w pliku/etc/fstab.
10. Ustaw parametry jądra systemu Linux, zgodnie z wersji systemu operacyjnego Linux, którego używasz. Aby uzyskać więcej informacji zobacz informacje o programu SAP HANA i w sekcji "Jądra parametry" w tym przewodniku.
11. Zwiększ obszar wymiany.
12. Ewentualnie Zainstaluj graficzny pulpitu na testowych maszyn wirtualnych. W przeciwnym razie użyj zdalną instalację SAPinst.
13. Pobierz oprogramowanie SAP z SAP Service Marketplace.
14. Zainstaluj wystąpienie SAP ASCS na serwerze aplikacji maszyny Wirtualnej.
15. Udostępnij katalogu /sapmnt między testowych maszyn wirtualnych przy użyciu systemu plików NFS. Serwer aplikacji maszyny Wirtualnej jest serwer systemu plików NFS.
16. Zainstaluj wystąpienie bazy danych, w tym platformy HANA przy użyciu SWPM na serwerze bazy danych maszyny Wirtualnej.
17. Zainstaluj serwer aplikacji głównej (PAS) na serwerze aplikacji maszyny Wirtualnej.
18. Uruchom konsolę zarządzania SAP (SAP MC). Na przykład połączyć się z graficznym interfejsem użytkownika SAP i HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Podstawowe etapy instalacji oprogramowania SAP HANA, korzystając z HDBLCM
W tej sekcji przedstawiono kluczowe kroki ręczne, jednego wystąpienia instalacji oprogramowania SAP HANA, korzystając z SAP HDBLCM do przeprowadzenia instalacji rozproszonej SAP NetWeaver w wersji 7.5. Poszczególne kroki zostały omówione bardziej szczegółowo w zrzuty ekranu, w tym przewodniku.

1. Tworzenie sieci wirtualnej platformy Azure, który zawiera test dwóch maszyn wirtualnych.
2. Wdrażanie dwóch maszyn wirtualnych platformy Azure z systemami operacyjnymi, zgodnie z modelem usługi Azure Resource Manager. W tym przykładzie używa SLES i SLES for SAP aplikacji 12 z dodatkiem SP1.
3. Dołącz dwa standardowe platformy Azure lub dysków magazynu premium storage, na przykład dysków 75 GB i 500 GB, serwerowi aplikacji maszyny Wirtualnej.
4. Dołączanie dysków magazynu premium storage do serwera bazy danych HANA maszyny Wirtualnej. Aby uzyskać więcej informacji zobacz sekcję "Dysku setup" w dalszej części tego przewodnika.
5. W zależności od wymagań rozmiar lub przepływność dołączając wiele dysków. Utworzyć woluminy rozłożone przy użyciu Zarządzanie woluminami logicznych lub narzędzia mdadm wewnątrz maszyny Wirtualnej na poziomie systemu operacyjnego.
6. Utwórz XFS systemy plików na woluminach logicznych i dołączonych dysków.
7. Zainstaluj nowych systemów plików XFS na poziomie systemu operacyjnego. Używaj jednego systemu plików dla oprogramowania SAP. Użyj systemu plików do katalogu /sapmnt i kopie zapasowe, na przykład. Na serwerze bazy danych SAP HANA należy zainstalować XFS systemy plików na dyskach magazynu premium storage jako /hana i /usr/sap. Ten proces jest niezbędne zapobiec zapełnianiu głównego systemu plików. Główny system plików nie jest duży na maszynach wirtualnych Azure z systemem Linux.
8. Wprowadź lokalne adresy IP testowych maszyn wirtualnych w pliku/etc/hosts.
9. Wprowadź **nofail** parametru w pliku/etc/fstab.
10. Ustaw parametry jądra zgodnie z wersji systemu operacyjnego Linux, którego używasz. Aby uzyskać więcej informacji zobacz informacje o programu SAP HANA i w sekcji "Jądra parametry" w tym przewodniku.
11. Zwiększ obszar wymiany.
12. Ewentualnie Zainstaluj graficzny pulpitu na testowych maszyn wirtualnych. W przeciwnym razie użyj zdalną instalację SAPinst.
13. Pobierz oprogramowanie SAP z SAP Service Marketplace.
14. Utwórz grupę, sapsys, za pomocą grupy 1001 Identyfikatora na serwerze bazy danych HANA maszyny Wirtualnej.
15. Instalowanie platformy SAP HANA na serwerze bazy danych maszyny Wirtualnej za pomocą Menedżera cyklem życia bazy danych HANA.
16. Zainstaluj wystąpienie SAP ASCS na serwerze aplikacji maszyny Wirtualnej.
17. Udostępnij katalogu /sapmnt między testowych maszyn wirtualnych przy użyciu systemu plików NFS. Serwer aplikacji maszyny Wirtualnej jest serwer systemu plików NFS.
18. Zainstaluj wystąpienie bazy danych, w tym platformy HANA przy użyciu SWPM na serwerze bazy danych HANA maszyny Wirtualnej.
19. Zainstaluj na serwerze podstawowym aplikacji na serwerze aplikacji maszyny Wirtualnej.
20. Rozpocznij SAP MC. Połącz przy użyciu graficznego interfejsu użytkownika SAP i HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Przygotowywanie maszyn wirtualnych platformy Azure do ręcznej instalacji oprogramowania SAP HANA
W tej sekcji omówiono następujące tematy:

* Aktualizacje systemu operacyjnego
* Ustawienia dysku
* Jądra parametry
* Systemy plików
* Plik/etc/hosts
* Plik/etc/fstab

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Sprawdź, czy system operacyjny Linux aktualizacje i poprawki, przed zainstalowaniem dodatkowego oprogramowania. Instalując poprawkę, może uniknąć wywołania do działu pomocy technicznej.

Upewnij się, że używasz:
* SUSE Linux Enterprise Server dla aplikacji SAP.
* Red Hat Enterprise Linux dla aplikacji SAP i Red Hat Enterprise Linux for SAP HANA. 

Jeśli użytkownik jeszcze tego nie zrobiono, zarejestruj wdrożenia systemu operacyjnego z Twoją subskrypcją systemu Linux, od dostawcy systemu Linux. SUSE zawiera obrazy systemu operacyjnego dla aplikacji SAP zawierające usług i które są zarejestrowane automatycznie.

Poniżej przedstawiono przykładowy sposób pod kątem dostępnych poprawek w systemie SUSE Linux przy użyciu **zypper** polecenia:

 `sudo zypper list-patches`

W zależności od rodzaju problemu poprawki są klasyfikowane według kategorii i ważności. Są często używane wartości dla kategorii: 
- Bezpieczeństwo
- Zalecane
- Optional (Opcjonalność)
- Cecha
- Dokument
- yast

Są często używane wartości na poziomie ważności:

- Krytyczny
- Ważne
- Średnia
- Małe
- Nie określono tego parametru

**Zypper** polecenie sprawdza tylko w przypadku aktualizacji wymagających zainstalowane pakiety. Na przykład można użyć tego polecenia:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Można dodać parametr `--dry-run` przetestowania aktualizacji bez faktycznego aktualizowanie systemu.


### <a name="disk-setup"></a>Ustawienia dysku
Główny system plików na maszynie Wirtualnej z systemem Linux na platformie Azure obowiązuje ograniczenie rozmiaru. Dlatego należy dołączyć dodatkowe miejsce na dysku do maszyny Wirtualnej platformy Azure, aby uruchomić SAP. Serwer aplikacji SAP maszyn wirtualnych platformy Azure korzystanie z dysków magazynu w warstwie standardowa usługi Azure może być wystarczająca. SAP HANA DBMS maszyn wirtualnych platformy Azure korzystanie z dysków magazynu Azure premium storage dla środowiska produkcyjnego i obniżenia implementacji jest obowiązkowe.

Na podstawie [wymagania dotyczące magazynu SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), następującą konfigurację magazynu platformy Azure — wersja premium jest zalecane: 

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM |  / hana/danych i dziennika/hana / <br /> Rozłożona z LVM lub mdadm | / hana/udostępnione | wolumin/root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

W konfiguracji dysku sugerowane ilości danych HANA i wolumin dziennika są umieszczane w ten sam zestaw dysków magazynu Azure premium storage, które są rozkładane LVM lub mdadm. Nie trzeba zdefiniować dowolny poziom nadmiarowości RAID, ponieważ usługi Azure premium storage zapewnia trzy obrazy dysków w celu zapewnienia nadmiarowości. 

Aby upewnić się, że skonfigurować wystarczająco dużego magazynu, zobacz [wymagania dotyczące magazynu SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) i [Przewodnik instalacji i aktualizacji serwera SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Należy również rozważyć woluminy przepływności inny wirtualny dysk twardy (VHD) z dysków magazynu innej usługi Azure premium zgodnie z opisem w [High-performance premium storage i dysków zarządzanych dla maszyn wirtualnych](../../windows/disks-types.md). 

Możesz dodać więcej dysków magazynu premium storage do platformy HANA DBMS maszyn wirtualnych do przechowywania kopii zapasowych dziennika bazy danych lub transakcji.

Aby uzyskać więcej informacji na temat dwa główne narzędzia używane do konfigurowania rozkładanie zobacz:

* [Konfigurowanie programowej macierzy RAID w systemie Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać więcej informacji o tym, jak dołączyć dyski do maszyn wirtualnych platformy Azure z systemem Linux jako system operacyjny gościa, zobacz [dodać dysk do maszyny Wirtualnej z systemem Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Za pomocą usługi Azure premium SSD można zdefiniować tryby buforowania dysku. Dla rozłożonego zestawu, który zawiera /hana/data i /hana/log wyłączenie buforowania dysku. Inne woluminy, oznacza to, że dyski, ustaw buforowania tryb **tylko do odczytu**.

Aby uzyskać przykładowe szablony JSON na potrzeby tworzenia maszyn wirtualnych, zobacz [szablony szybkiego startu platformy](https://github.com/Azure/azure-quickstart-templates).
Szablon maszyny wirtualnej — prosty — sles jest podstawowy szablon. Zawiera on sekcję magazynu przy użyciu dysku dodatkowe 100 GB danych. Użyj tego szablonu jako podstawy. Można dostosować szablon do określonej konfiguracji.

> [!NOTE]
> Ważne jest, aby dołączyć dysku usługi Azure storage przy użyciu identyfikatora UUID, zgodnie z opisem w [uruchomić oprogramowanie SAP NetWeaver na maszynach wirtualnych systemu Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W środowisku testowym dysków dwóch standardowego magazynu platformy Azure dołączonych do serwera aplikacji SAP maszyny Wirtualnej, jak pokazano na poniższym zrzucie ekranu. Jeden dysk przechowuje wszystkie SAP oprogramowania, takie jak NetWeaver w wersji 7.5, graficznego interfejsu użytkownika SAP i SAP HANA do instalacji. Drugiego dysku gwarantuje, że za mało wolnego miejsca jest dostępna dla dodatkowych wymagań. Na przykład dane kopii zapasowej i testowania i katalogu /sapmnt, oznacza to, że profile SAP, muszą być współużytkowane przez wszystkie maszyny wirtualne, które należą do tej samej środowiskiem SAP.

![Serwer aplikacji SAP HANA dysków okna wyświetlania dwa dyski danych i ich rozmiary](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Jądra parametry
SAP HANA wymaga określonych ustawień jądra systemu Linux. Te ustawienia jądra nie są częścią obrazów w galerii usługi Azure standard i należy ręcznie ustawić. W zależności od tego, czy używasz SUSE lub Red Hat parametry mogą się różnić. SAP Notes wymienione wcześniej zapewniają informacje na temat tych parametrów. Na zrzutach ekranu przedstawiono SUSE Linux 12 z dodatkiem SP1 został użyty. 

Za usługi ogólnie dostępne 12 aplikacje SAP w systemie SLES i z systemem SLES for SAP aplikacji 12 z dodatkiem SP1 ma nowe narzędzie **dostrojone adm**, który zastępuje stare **sapconf** narzędzia. Z profilu specjalnego oprogramowania SAP HANA jest dostępna dla **dostrojone adm**. Należy dostosować system for SAP HANA, należy wprowadzić poniższy profil jako użytkownik główny:

   `tuned-adm profile sap-hana`

Aby uzyskać więcej informacji na temat **dostrojone adm**, zobacz [SUSE dokumentacji dotyczącej dostrojone adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na poniższym zrzucie ekranu widać jak **dostrojone adm** zmienione `transparent_hugepage` i `numa_balancing` wartości zgodnie z wymaganych ustawień platformy SAP HANA:

![Narzędzie dostrojone adm zmienia wartości zgodnie z wymaganych ustawień platformy SAP HANA](./media/hana-get-started/image005.jpg)

Utrwalenie ustawienia jądra platformy SAP HANA, należy użyć **grub2** w systemie SLES 12. Aby uzyskać więcej informacji na temat **grub2**, zobacz [struktura plików konfiguracji](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) sekcji dokumentacji SUSE.

Poniższy zrzut ekranu przedstawia, jak ustawienia jądra zostały zmienione w pliku konfiguracji, a następnie skompilowane przy użyciu **grub2 mkconfig**:

![Ustawienia jądra zmiany w pliku konfiguracji i kompilowany przy użyciu grub2 mkconfig](./media/hana-get-started/image006.jpg)

Innym rozwiązaniem jest zmiana ustawień za pomocą YaST i **moduł ładujący rozruchu** > **jądra parametry** ustawienia:

![Na karcie Ustawienia jądra parametry w moduł ładujący rozruchu YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systemy plików
Poniższy zrzut ekranu przedstawia dwa systemy plików, które zostały utworzone na serwerze aplikacji SAP maszyny Wirtualnej na podstawie dwóch dysków dołączonych standardowego magazynu platformy Azure. Systemów plików typu XFS i są instalowane /sapdata i /sapsoftware.

Nie jest wymagane struktury w ten sposób swoje systemy plików. Masz inne opcje struktury miejsca na dysku. Najważniejszą kwestią jest zapobiec głównego systemu plików po wyczerpaniu wolnego miejsca.

![Dwa systemy plików utworzony na serwerze aplikacji SAP maszyny Wirtualnej](./media/hana-get-started/image008.jpg)

SAP HANA DB maszyny wirtualnej, podczas instalacji bazy danych, gdy używasz SAPinst SWPM i **typowe** opcji instalacji wszystko zostało zainstalowane w ramach /hana i /usr/sap. Domyślna lokalizacja dla kopii zapasowej dziennika platformy SAP HANA podlega /usr/sap. Ponownie jest ważne, aby zapobiec głównego systemu plików z brakiem miejsca do magazynowania. Upewnij się, że jest za mało wolnego miejsca w obszarze /hana i /usr/sap przed zainstalowaniem oprogramowania SAP HANA przy użyciu SWPM.

Opis układ standardowy system plików środowiska SAP Hana, zobacz [Przewodnik instalacji i aktualizacji serwera SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Systemy plików dodatkowych, utworzony na serwerze aplikacji SAP maszyny Wirtualnej](./media/hana-get-started/image009.jpg)

Po zainstalowaniu oprogramowania SAP NetWeaver na standard w systemie SLES/SLES dla obrazu z galerii Azure 12 aplikacje SAP, zostanie wyświetlony komunikat informujący, że nie ma już miejsca wymiany, jak pokazano na poniższym zrzucie ekranu. Aby odrzucić ten komunikat, można ręcznie dodać plik wymiany przy użyciu **dd**, **mkswap**, i **swapon**. Aby dowiedzieć się, jak to zrobić, wyszukaj "Pliku wymiany ręczne dodawanie" w [przy użyciu partycjonera YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) sekcji dokumentacji SUSE.

Innym rozwiązaniem jest, aby skonfigurować obszar wymiany przy użyciu agenta maszyny Wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../../extensions/agent-linux.md).

![Komunikat podręczny wniosku, że jest wymiany za mało miejsca](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Plik/etc/hosts
Przed rozpoczęciem instalacji SAP, upewnij się, że obejmują nazwy hostów i adresów IP maszyn wirtualnych SAP w pliku/etc/hosts. Wdróż VMs SAP w ramach jednej sieci wirtualnej platformy Azure. Następnie użyj wewnętrznych adresów IP, jak pokazano poniżej:

![Nazwy hostów i adresów IP maszyn wirtualnych SAP, wymienione w pliku/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Plik/etc/fstab

Warto dodać **nofail** parametrów w pliku fstab. Dzięki temu, jeśli coś pójdzie nie tak z dyskami maszyny Wirtualnej nie przestać odpowiadać w procesie rozruchu. Należy jednak pamiętać, że nie mogą być dostępne dodatkowe miejsce na dysku i procesów może zapełnić głównego systemu plików. Jeśli brakuje /hana, nie można uruchomić oprogramowanie SAP HANA.

![Dodaj parametr nofail do pliku fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Graficzny pulpit gnome na SLES 12/z systemem SLES for SAP aplikacji 12
W tej sekcji opisano sposób:

* Zainstaluj pulpit gnome i xrdp w systemie SLES 12/SLES 12 aplikacji SAP.
* Uruchom MC SAP oparte na języku Java za pomocą przeglądarki Firefox w systemie SLES 12/SLES dla 12 aplikacji SAP.

Możesz także użyć rozwiązań alternatywnych, takich jak Xterminal lub VNC, które nie są opisane w tym przewodniku.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Zainstaluj pulpit gnome i xrdp w systemie SLES 12/SLES for SAP aplikacji 12
W przypadku tło Windows łatwo umożliwia graficzne pulpitu bezpośrednio z poziomu maszyn wirtualnych z systemem Linux SAP uruchamianie Firefox, SAPinst, SAP graficznego interfejsu użytkownika, SAP MC i HANA Studio. Następnie można Połącz z maszyną wirtualną za pośrednictwem protokołu RDP (Remote Desktop) z komputera Windows. W zależności od zasady firmy dotyczące dodawania służące do środowiska produkcyjnego i obniżenia systemów opartych na systemie Linux można zainstalować GNOME na serwerze. Wykonaj następujące kroki, aby zainstalować pulpit GNOME na platformie Azure w systemie SLES 12/w systemie SLES dla maszyny Wirtualnej 12 aplikacji SAP.

1. Zainstaluj pulpit GNOME, wprowadzając następujące polecenie, na przykład w oknie programu PuTTY:

   `zypper in -t pattern gnome-basic`

2. Zainstaluj xrdp, aby zezwolić na połączenia z maszyną wirtualną za pośrednictwem protokołu RDP:

   `zypper in xrdp`

3. Edytuj /etc/sysconfig/windowmanager i Ustaw domyślnego menedżera okien GNOME:

   `DEFAULT_WM="gnome"`

4. Uruchom **chkconfig** aby upewnić się, że xrdp jest uruchamiany automatycznie po ponownym uruchomieniu:

   `chkconfig -level 3 xrdp on`

5. Jeśli masz problem z połączeniem RDP, spróbuj uruchomić ponownie, na przykład z okna programu PuTTY:

   `/etc/xrdp/xrdp.sh restart`

6. Jeśli ponowne uruchomienie xrdp, opisane w poprzednim kroku nie rozwiąże problemu, sprawdź, czy plik .pid:

   `check /var/run` 

   Wyszukaj `xrdp.pid`. Jeśli możesz znaleźć, usuń go, a następnie spróbuj ponownie.

### <a name="start-sap-mc"></a>Rozpocznij SAP MC
Po zainstalowaniu pulpit GNOME, należy uruchomić graficzny MC SAP oparte na języku Java z przeglądarki Firefox. Uruchomieniu na platformie Azure w systemie SLES 12/w systemie SLES dla maszyny Wirtualnej 12 aplikacje SAP, może zawierać błąd. Ten błąd występuje ze względu na brak Java wtyczkę dla przeglądarki.

Adres URL, aby rozpocząć SAP MC jest `<server>:5<instance_number>13`.

Aby uzyskać więcej informacji, zobacz [uruchamiania konsoli sieci web zarządzania SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Poniższy zrzut ekranu przedstawia komunikat o błędzie, która jest wyświetlana podczas brakuje Java przeglądarki wtyczki:

![Komunikat o błędzie, brak Java-wtyczkę dla przeglądarki](./media/hana-get-started/image013.jpg)

Jednym ze sposobów rozwiązania tego problemu jest zainstalować brakiem wtyczki za pomocą YaST, jak pokazano na poniższym zrzucie ekranu:

![Instalowanie, brakiem wtyczki za pomocą YaST](./media/hana-get-started/image014.jpg)

Gdy należy ponownie wprowadzić adres URL konsoli zarządzania SAP, użytkownik jest proszony o aktywowanie wtyczki:

![Okno dialogowe, które żądają aktywacji wtyczki](./media/hana-get-started/image015.jpg)

Można również otrzymać komunikat o błędzie dotyczący brakujący plik javafx.properties. Dotyczy potrzebę SAP graficznego interfejsu użytkownika w wersji 7.4 Oracle Java 1.8. Aby uzyskać więcej informacji, zobacz [2059429 Uwaga SAP](https://launchpad.support.sap.com/#/notes/2059424).
Wersja IBM Java i pakietu openjdk, dostarczanego z systemem SLES/SLES 12 aplikacji SAP nie zawierają pliku javafx.properties potrzebne. Jest to rozwiązanie do pobrania i zainstalowania Java SE 8 od firmy Oracle.

Aby uzyskać informacje na temat podobnych z openjdk na openSUSE, zobacz wątek dyskusji [Leap SAPGui 7.4 Java dla openSUSE 42.1](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalacja ręczna oprogramowania SAP Hana: SWPM
Seria zrzutów ekranu w tej sekcji przedstawiono kroki klucza jak zainstalować oprogramowanie SAP NetWeaver w wersji 7.5 i SAP HANA SP12 w przypadku użycia SWPM z SAPinst. W ramach instalacji NetWeaver w wersji 7.5 SWPM również można zainstalować bazy danych HANA jako pojedyncze wystąpienie.

W środowisku testowym przykładowe zainstalowanej jeden serwer aplikacji Advanced Business Application programowania (ABAP). Jak pokazano na poniższym zrzucie ekranu, użyliśmy **rozproszony System** możliwość zainstalowania ASCS i wystąpień serwera aplikacji głównej w jednej maszynie Wirtualnej platformy Azure. Użyliśmy platformy SAP HANA jako system bazy danych w innej maszyny Wirtualnej platformy Azure.

![ASCS i wystąpień serwera aplikacji głównej zainstalowany przy użyciu opcji rozproszonego systemu](./media/hana-get-started/image012.jpg)

Po zainstalowaniu wystąpienia ASCS na serwerze aplikacji maszyny Wirtualnej okaże się przez zieloną ikonę w konsoli zarządzania SAP. Directory /sapmnt, która zawiera katalog profilu SAP, muszą zostać udostępnione z maszyną Wirtualną serwera bazy danych SAP HANA. Krok instalacji bazy danych musi mieć dostęp do tych informacji. Najlepszym sposobem, aby zapewnić dostęp jest używanie systemu NFS, którego można skonfigurować za pomocą YaST.

![Konsola zarządzania SAP pokazująca, że wystąpienie ASCS zainstalowane na serwerze aplikacji maszyny Wirtualnej przy użyciu zieloną ikonę](./media/hana-get-started/image016.jpg)

Na serwerze aplikacji maszyny Wirtualnej, katalog /sapmnt jest udostępniony za pośrednictwem systemu plików NFS przy użyciu **rw** i **no_root_squash** opcje. Wartości domyślne to **ro** i **root_squash**, który może prowadzić do problemów podczas instalacji wystąpienia bazy danych.

![Udostępnianie katalogu /sapmnt za pośrednictwem systemu plików NFS przy użyciu opcji rw i no_root_squash](./media/hana-get-started/image017b.jpg)

Jak pokazano na następnym zrzucie ekranu, udział /sapmnt z maszyną Wirtualną serwera aplikacji musi być skonfigurowany na serwerze bazy danych SAP HANA maszyny Wirtualnej przy użyciu **klient systemu plików NFS** i YaST:

![Udostępnij /sapmnt skonfigurowane za pomocą klienta systemu plików NFS](./media/hana-get-started/image018b.jpg)

Do wykonania NetWeaver 7.5 instalacją rozproszoną, oznacza to, **wystąpienie bazy danych**, zaloguj się do maszyny Wirtualnej serwera bazy danych SAP HANA i uruchom SWPM:

![Instalowanie wystąpienia bazy danych logowania do serwera bazy danych SAP HANA maszyny Wirtualnej, a następnie uruchomiona SWPM](./media/hana-get-started/image019.jpg)

Po wybraniu **typowe** instalacji i ścieżkę do nośnika instalacyjnego, wprowadź identyfikator SID bazy danych, nazwę hosta, liczby wystąpień i hasła administratora systemu bazy danych:

![SAP HANA bazy danych systemu administratora strony logowania](./media/hana-get-started/image035b.jpg)

Wprowadź hasło dla schematu DBACOCKPIT:

![Pole wprowadzenia hasła dla schematu DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Wprowadź pytanie o hasło schematu SAPABAP1:

![Wprowadź pytanie o hasło schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu każdego zadania obok każdej fazie procesu instalacji bazy danych jest wyświetlany zielony znacznik wyboru. Komunikat "wykonywania... Wystąpienie zostało ukończone. Baza danych"jest wyświetlany.

![Zadanie ukończone okno z komunikat z potwierdzeniem](./media/hana-get-started/image023.jpg)

Po pomyślnej instalacji konsoli zarządzania SAP zawiera zieloną ikonę wystąpienia bazy danych. Wyświetla pełną listę procesów platformy SAP HANA, takich jak hdbindexserver i hdbcompileserver.

![Okno konsoli zarządzania SAP z listy procesów platformy SAP HANA](./media/hana-get-started/image024.jpg)

Poniższy zrzut ekranu przedstawia części struktury plików w katalogu /hana/shared, który SWPM utworzony podczas instalowania platformy HANA. Ponieważ nie ma możliwości, aby określić inną ścieżkę, należy zainstalować dodatkowe miejsce na dysku w katalogu /hana przed rozpoczęciem instalacji oprogramowania SAP HANA przy użyciu SWPM. Ten krok zapobiega wyczerpaniu wolnego miejsca na główny systemu plików.

![Struktura pliku katalogu /hana/shared tworzone podczas instalacji oprogramowania HANA](./media/hana-get-started/image025.jpg)

Ten zrzut ekranu przedstawia strukturę pliku katalogu /usr/sap:

![/ Usr/sap pliku struktury katalogów](./media/hana-get-started/image026.jpg)

Ostatnim krokiem instalacją rozproszoną ABAP jest zainstalować wystąpienie usługi głównej aplikacji serwera:

![Wystąpienie serwera ABAP instalacji przedstawiający głównej aplikacji jako ostatni krok](./media/hana-get-started/image027b.jpg)

Po zainstalowaniu aplikacji podstawowego wystąpienia serwera i SAP graficznego interfejsu użytkownika, użyj **DBA Cockpit** transakcji, aby upewnić się, poprawnie Zakończono instalację oprogramowania SAP HANA:

![Okno DBA Cockpit potwierdzenia pomyślnej instalacji](./media/hana-get-started/image028b.jpg)

W ostatnim kroku możesz chcieć wcześniejszej instalacji oprogramowania HANA Studio w maszyną Wirtualną serwera aplikacji SAP. Następnie należy nawiązać wystąpienia platformy SAP HANA, czy jest uruchomiona na serwerze bazy danych maszyny Wirtualnej.

![Instalowanie programu SAP HANA Studio serwera aplikacji SAP maszyny Wirtualnej](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalacja ręczna oprogramowania SAP Hana: HDBLCM
Oprócz instalowania oprogramowania SAP HANA jako część instalacji rozproszonej, za pomocą SWPM, autonomiczny HANA można zainstalować najpierw, przy użyciu HDBLCM. Następnie możesz zainstalować oprogramowanie SAP NetWeaver w wersji 7.5, na przykład. Zrzuty ekranu w tej sekcji pokazano, jak działa ten proces.

Aby uzyskać więcej informacji o narzędziu HANA HDBLCM zobacz:

* [Wybierz prawidłowe HDBLCM HANA SAP zadania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Narzędzia do zarządzania cyklem życia oprogramowania SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Przewodnik instalacji i aktualizacji serwera SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Aby uniknąć problemów związanych z domyślnego ustawienia Identyfikatora grupy dla `\<HANA SID\>adm user`, którym zostanie utworzony przez narzędzie HDBLCM. Przed zainstalowaniem oprogramowania SAP HANA za pośrednictwem HDBLCM należy zdefiniować nową grupę o nazwie `sapsys` za pomocą Identyfikatora grupy `1001`:

![Nowe grupy "sapsys" zdefiniowane przy użyciu grupy o identyfikatorze 1001](./media/hana-get-started/image030.jpg)

Po uruchomieniu HDBLCM po raz pierwszy, prostą start menu zawiera. Wybierz element 1, **zainstalować nowy system**:

![Opcja "Zainstaluj nowy system" w oknie uruchamiania HDBLCM](./media/hana-get-started/image031.jpg)

Poniższy zrzut ekranu przedstawia wszystkie wcześniej wybrane opcje klucza.

> [!IMPORTANT]
> Katalogi, które są nazywane dziennika platformy HANA i ilości danych i ścieżki instalacji, czyli /hana/shared w tej przykładowej i /usr/sap nie może być częścią głównego systemu plików. Te katalogi należą do dysków z danymi platformy Azure, które zostały dołączone do maszyny Wirtualnej. Aby uzyskać więcej informacji zobacz sekcję "Setup dysku". 

Takie podejście pomaga zapobiec głównego systemu plików korzystającym z miejsca. Zwróć uwagę, że administrator systemu HANA ma identyfikator użytkownika `1005` i jest częścią `sapsys` grupy o identyfikatorze `1001`, która została zdefiniowana przed rozpoczęciem instalacji.

![Listę wszystkich składników oprogramowania SAP HANA klucza wybrana wcześniej](./media/hana-get-started/image032.jpg)

Sprawdź `\<HANA SID\>adm user` szczegółowe informacje w katalogu/etc/haseł. Wyszukaj `azdadm`, jak pokazano na poniższym zrzucie ekranu:

![HANA \<HANA SID\>adm szczegóły użytkownika na liście w katalogu/etc/haseł](./media/hana-get-started/image033.jpg)

Po zainstalowaniu oprogramowania SAP HANA przy użyciu HDBLCM można wyświetlić struktury plików w systemie SAP HANA Studio, jak pokazano na poniższym zrzucie ekranu. Schemat SAPABAP1, który zawiera wszystkie tabele oprogramowanie SAP NetWeaver, nie jest jeszcze dostępna.

![Struktura pliku oprogramowania SAP HANA w systemie SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po zainstalowaniu oprogramowania SAP HANA, SAP NetWeaver można zainstalować na jego podstawie. Jak pokazano na poniższym zrzucie ekranu, instalacja została wykonana przy użyciu SWPM jako instalacją rozproszoną. Ten proces jest opisany w poprzedniej sekcji. Po zainstalowaniu wystąpienie bazy danych przy użyciu SWPM możesz wprowadzić te same dane przy użyciu HDBLCM. Na przykład wprowadź nazwę hosta, identyfikator SID HANA i liczby wystąpień. SWPM następnie używa istniejącej instalacji platformy HANA i dodaje więcej schematów.

![Wykonywane przy użyciu SWPM instalacją rozproszoną](./media/hana-get-started/image035b.jpg)

Poniższy zrzut ekranu przedstawia SWPM kroku instalacji, w którym możesz wprowadzić dane dotyczące schematu DBACOCKPIT:

![Krok instalacji SWPM, gdzie wprowadzeniu DBACOCKPIT schematu danych](./media/hana-get-started/image036b.jpg)

Wprowadź dane dotyczące schematu SAPABAP1:

![Wprowadzając dane dotyczące schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu instalacji wystąpienia bazy danych SWPM można wyświetlić schemat SAPABAP1 na oprogramowanie SAP HANA Studio:

![Schemat SAPABAP1 na oprogramowanie SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Na koniec, po zakończeniu serwer aplikacji SAP i SAP graficznego interfejsu użytkownika instalacji, Sprawdź wystąpienie bazy danych HANA przy użyciu **DBA Cockpit** transakcji:

![Wystąpienie bazy danych HANA sprawdzić przy użyciu transakcji DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Pobierania oprogramowania SAP
Oprogramowanie można pobrać z SAP Service Marketplace, jak pokazano na poniższych zrzutach ekranu.

Pobierz NetWeaver w wersji 7.5 dla systemu Linux/HANA:

 ![SAP instalacji i uaktualniania przedział czasu usługi do pobrania NetWeaver w wersji 7.5](./media/hana-get-started/image001.jpg)

Pobierz wersję platformy SP12 HANA:

 ![SAP instalacji i uaktualniania przedział czasu usługi do pobrania wersji platformy SP12 HANA](./media/hana-get-started/image002.jpg)

