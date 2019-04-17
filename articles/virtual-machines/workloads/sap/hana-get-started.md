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
ms.openlocfilehash: 7d46e2047debe5546c6d36f245ae076cec6f73a3
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618128"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Szybki start: Ręczna instalacja jednego wystąpienia oprogramowania SAP HANA na maszynach wirtualnych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Ten przewodnik ułatwia konfigurowanie jednego wystąpienia oprogramowania SAP HANA na maszynach wirtualnych platformy Azure (maszyny wirtualne), gdy należy zainstalować oprogramowanie SAP NetWeaver w wersji 7.5 i SAP HANA 1.0 SP12 ręcznie. Ten przewodnik koncentruje się na temat wdrażania oprogramowania SAP HANA na platformie Azure. Nie zastępuje w dokumentacji SAP. 

>[!Note]
>W tym przewodniku opisano wdrażanie oprogramowania SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać informacje na temat wdrażania oprogramowania SAP HANA w dużych wystąpień HANA, zobacz [używanie rozwiązań SAP na maszynach wirtualnych platformy Azure (maszyny wirtualne)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że czytelnik zna takie infrastruktury jako podstawy usługi (IaaS), jako:
 * Jak wdrożyć maszyny wirtualne lub sieci wirtualnych za pośrednictwem witryny Azure portal lub programu PowerShell.
 * Azure dla wielu platform interfejsu wiersza polecenia (CLI), w tym możliwość użycia szablonów JavaScript Object Notation (JSON).

W tym przewodniku założono również, że znasz:
* SAP HANA i oprogramowania SAP NetWeaver i sposobie ich instalowania w środowisku lokalnym.
* Instalowanie i operacyjne platformy SAP HANA i wystąpieniami aplikacji SAP na platformie Azure.
* Następujące pojęcia i procedury:
   * Planowanie wdrożenia SAP na platformie Azure, w tym usługi Azure Virtual Network planowania i użytkowania usługi Azure Storage. Zobacz [oprogramowanie SAP NetWeaver na maszynach wirtualnych Azure (maszyny wirtualne) — Podręcznik planowania i implementacji](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Wdrażanie zasad i sposoby wdrażania maszyn wirtualnych na platformie Azure. Zobacz [wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Wysoka dostępność dla programu SAP NetWeaver ASCS (ABAP SAP Central Services), SCS (usług SAP Central Services) i Wywołujących (umieścić w kolejce replikacji serwera) na platformie Azure. Zobacz [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Szczegółowe informacje na temat sposobu poprawy efektywności dzięki wykorzystaniu instalacji — wiele identyfikatorów SID ASCS/SCS na platformie Azure. Zobacz [tworzenia konfiguracji — wiele identyfikatorów SID oprogramowanie SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Zasady z oprogramowaniem SAP NetWeaver na podstawie opartych na systemie Linux maszyn wirtualnych na platformie Azure. Zobacz [z oprogramowaniem SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ten przewodnik zawiera konkretne ustawienia dla systemu Linux w maszynach wirtualnych platformy Azure i uzyskać szczegółowe informacje na temat prawidłowo dołączyć dyski usługi Azure storage do maszyn wirtualnych systemu Linux.

Typy maszyn wirtualnych platformy Azure, których można użyć na potrzeby scenariuszy produkcyjnych są wymienione w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy nieprodukcyjnych szerszy zakres natywnych typów maszyn wirtualnych platformy Azure jest dostępna.
Aby uzyskać szczegółowe informacje na temat maszyny Wirtualnej konfiguracji i operacje zapoznaj się z dokumentem [konfiguracje infrastruktury SAP HANA i operacji na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
SAP HANA wysoką dostępność, zobacz [platformy SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Jeśli są zamierzających uzyskać wystąpienie SAP HANA lub S/4HANA lub BW/4HANA system wdrożone w bardzo krótki czas, należy rozważyć użycie [SAP Cloud Appliance Library](https://cal.sap.com). Można znaleźć dokumentację na temat wdrażania, na przykład systemu S/4HANA, za pośrednictwem SAP CAL na platformie Azure w [tego przewodnika](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). To wszystko, co musisz mieć subskrypcję platformy Azure i użytkownik oprogramowania SAP, który może być zarejestrowane przy użyciu biblioteki SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Dodatkowe zasoby
### <a name="sap-hana-backup"></a>Kopia zapasowa oprogramowania SAP HANA
Aby uzyskać informacji na temat tworzenia kopii zapasowej baz danych SAP HANA na maszynach wirtualnych platformy Azure zobacz:
* [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA usługi Azure Backup na poziomie plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Biblioteki SAP Cloud Appliance Library
Aby uzyskać informacji na temat korzystania z biblioteki SAP Cloud Appliance Library w celu wdrożenia oprogramowania S/4HANA lub BW/4HANA, zobacz [wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA w systemie Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Systemy operacyjne obsługiwane HANA SAP
Aby uzyskać informacji na temat oprogramowania SAP HANA, obsługiwane systemy operacyjne, zobacz [2235581 # Uwaga SAP pomocy technicznej — SAP HANA: Obsługiwane systemy operacyjne](https://launchpad.support.sap.com/#/notes/2235581/E). Maszyny wirtualne platformy Azure obsługuje tylko podzbiór tych systemów operacyjnych. Do wdrażania oprogramowania SAP HANA na platformie Azure są obsługiwane następujące systemy operacyjne: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Aby uzyskać dodatkowej dokumentacji SAP o platformy SAP HANA i różnych systemów operacyjnych Linux zobacz:

* [SAP Uwaga pomocy technicznej 171356 # - oprogramowania SAP w systemie Linux:  Informacje ogólne](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP Uwaga pomocy technicznej 1944799 # — SAP HANA wskazówki dotyczące instalacji systemu operacyjnego w systemie SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Uwaga pomocy technicznej SAP #2205917 - bazy danych SAP HANA zalecane ustawienia systemu operacyjnego dla systemu SLES 12 dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Obsługa uwagę #1984787 — SUSE Linux Enterprise Server 12:  Uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787)
* [Uwaga pomocy technicznej SAP 1391070 # — Linux UUID rozwiązania](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP Uwaga pomocy technicznej 2009879 # — SAP HANA wytyczne dotyczące systemu operacyjnego systemu Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: Zalecane ustawienia systemu operacyjnego RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP monitorowanie na platformie Azure
Aby uzyskać informacji na temat SAP monitorowanie na platformie Azure zobacz:

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Ta uwaga w tym artykule omówiono SAP "rozszerzonego monitorowania" przy użyciu maszyn wirtualnych systemu Linux na platformie Azure. 
* [Uwaga SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Ta uwaga omówiono SAPOSCOL w systemie Linux. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Ta uwaga w tym artykule omówiono kluczowe metryki monitorowania dla rozwiązania SAP na Microsoft Azure.

### <a name="azure-vm-types"></a>Typy maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure i scenariusze SAP, obsługiwane obciążenie używane z platformą SAP HANA są udokumentowane w artykule [platform IaaS certyfikat SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Usługa Azure typy maszyn wirtualnych, które są certyfikowane przez firmę SAP dla oprogramowania SAP NetWeaver i S/4HANA warstwy aplikacji są udokumentowane w artykule [Uwaga SAP, 1928533 — aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integracja SAP, Linux, Azure jest obsługiwana tylko w usłudze Azure Resource Manager i nie klasycznego modelu wdrażania. 

## <a name="manual-installation-of-sap-hana"></a>Instalacja ręczna oprogramowania SAP Hana

> [!IMPORTANT]
> Upewnij się, że system operacyjny, należy wybrać w określonych typów maszyn wirtualnych, którego używasz z certyfikatem platformy SAP Hana SAP. Lista oprogramowania SAP HANA certyfikowane typy maszyn wirtualnych i system operacyjny w wersji dla tych można wyszukiwać [platform certyfikowane IaaS programu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Upewnij się, że kliknij przycisk Szczegóły typu maszyny Wirtualnej, aby uzyskać pełną listę oprogramowania SAP HANA na liście obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny Wirtualnej. Należy pamiętać, że w przykładzie w tym dokumencie użyto wersji systemu operacyjnego w systemie SLES, która nie jest obsługiwana przez firmę SAP dla oprogramowania SAP HANA na maszynach wirtualnych serii M.
>

Ten przewodnik zawiera opis sposobu ręcznie zainstalować oprogramowanie SAP HANA na maszynach wirtualnych platformy Azure na dwa sposoby:

* Za pomocą Menedżera inicjowania obsługi oprogramowania SAP (SWPM) jako część instalacji rozproszonej NetWeaver w kroku "Instalacja wystąpienia bazy danych"
* Przy użyciu oprogramowania SAP HANA bazy danych narzędzie Menedżer cyklu życia, HDBLCM, a następnie zainstaluj NetWeaver

Umożliwia także SWPM zainstalować wszystkie składniki (SAP HANA, serwera aplikacji SAP i wystąpienia ASCS) w jedną pojedynczą maszynę Wirtualną, zgodnie z opisem w tym [ogłoszenia na blogu platformy SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ta opcja nie jest opisane w tym przewodniku Szybki Start, ale problemy, które należy wziąć pod uwagę są takie same.

Przed rozpoczęciem instalacji, zalecamy przeczytanie "Azure przygotowanie maszyn wirtualnych na ręczną instalację oprogramowania SAP HANA" sekcję w dalszej części tego przewodnika. Ten sposób może pomóc w uniknięciu kilka podstawowych błędów, które mogą wystąpić, gdy używasz tylko domyślną konfigurację maszyny Wirtualnej platformy Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Kluczowe kroki instalacji oprogramowania SAP HANA, korzystając z SAP SWPM
W tej sekcji przedstawiono kluczowe kroki ręczne, jednego wystąpienia instalacji oprogramowania SAP HANA, korzystając z SAP SWPM do przeprowadzenia instalacji rozproszonej SAP NetWeaver w wersji 7.5. Poszczególne kroki zostały omówione bardziej szczegółowo w zrzuty ekranu w dalszej części tego przewodnika.

1. Tworzenie sieci wirtualnej platformy Azure, który zawiera test dwóch maszyn wirtualnych.
2. Wdrażanie dwóch maszyn wirtualnych platformy Azure z systemami operacyjnymi (w tym przykładzie, SUSE Linux Enterprise Server (SLES) i z systemem SLES for SAP aplikacji 12 z dodatkiem SP1), zgodnie z modelem usługi Azure Resource Manager.
3. Dołącz dwa standardowe platformy Azure lub dysków magazynu premium storage (na przykład dysków 75 GB i 500 GB) serwerowi aplikacji maszyny Wirtualnej.
4. Dołączanie dysków magazynu premium storage do serwera bazy danych HANA maszyny Wirtualnej. Aby uzyskać szczegółowe informacje Zobacz sekcję "Dysku setup" w dalszej części tego przewodnika.
5. W zależności od wymagań rozmiar lub przepływność dołączając wiele dysków, a następnie utworzyć woluminy rozłożone przy użyciu Zarządzanie woluminami logicznych lub narzędzia administracyjnego wielu urządzeń (MDADM) wewnątrz maszyny Wirtualnej na poziomie systemu operacyjnego.
6. Utwórz XFS systemy plików na woluminach logicznych i dołączonych dysków.
7. Zainstaluj nowych systemów plików XFS na poziomie systemu operacyjnego. Używaj jednego systemu plików dla oprogramowania SAP. Użyj systemu plików do katalogu /sapmnt i kopie zapasowe, na przykład. Na serwerze bazy danych SAP HANA należy zainstalować XFS systemy plików na dyskach magazynu premium storage jako /hana i /usr/sap. Ten proces jest niezbędne zapobiec zapełnianiu główny system plików, który jest zbyt na maszynach wirtualnych Azure z systemem Linux.
8. Wprowadź lokalne adresy IP testowych maszyn wirtualnych w pliku/etc/hosts.
9. Wprowadź **nofail** parametru w pliku/etc/fstab.
10. Ustaw parametry jądra systemu Linux, zgodnie z wersji systemu operacyjnego Linux, którego używasz. Aby uzyskać więcej informacji zobacz odpowiednią SAP notes, które mówią o HANA i w sekcji "Jądra parametry" w tym przewodniku.
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
2. Wdrażanie dwóch maszyn wirtualnych platformy Azure z systemami operacyjnymi (w naszym przykładzie SLES i z systemem SLES for SAP aplikacji 12 z dodatkiem SP1), zgodnie z modelem usługi Azure Resource Manager.
3. Dołącz dwa standardowe platformy Azure lub dysków magazynu premium storage (na przykład dysków 75 GB i 500 GB) serwerowi aplikacji maszyny Wirtualnej.
4. Dołączanie dysków magazynu premium storage do serwera bazy danych HANA maszyny Wirtualnej. Aby uzyskać szczegółowe informacje Zobacz sekcję "Dysku setup" w dalszej części tego przewodnika.
5. W zależności od wymagań rozmiar lub przepływność dołączając wiele dysków, a następnie utworzyć woluminy rozłożone przy użyciu Zarządzanie woluminami logicznych lub narzędzia administracyjnego wielu urządzeń (MDADM) wewnątrz maszyny Wirtualnej na poziomie systemu operacyjnego.
6. Utwórz XFS systemy plików na woluminach logicznych i dołączonych dysków.
7. Zainstaluj nowych systemów plików XFS na poziomie systemu operacyjnego. Używać jednego systemu plików dla oprogramowania SAP i jeden z nich do katalogu /sapmnt i kopie zapasowe, na przykład. Na serwerze bazy danych SAP HANA należy zainstalować XFS systemy plików na dyskach magazynu premium storage jako /hana i /usr/sap. Ten proces jest niezbędne zapobiec zapełnianiu główny system plików, który jest zbyt na maszynach wirtualnych Azure z systemem Linux.
8. Wprowadź lokalne adresy IP testowych maszyn wirtualnych w pliku/etc/hosts.
9. Wprowadź **nofail** parametru w pliku/etc/fstab.
10. Ustaw parametry jądra zgodnie z wersji systemu operacyjnego Linux, którego używasz. Aby uzyskać więcej informacji zobacz odpowiednią SAP notes, które mówią o HANA i w sekcji "Jądra parametry" w tym przewodniku.
11. Zwiększ obszar wymiany.
12. Ewentualnie Zainstaluj graficzny pulpitu na testowych maszyn wirtualnych. W przeciwnym razie użyj zdalną instalację SAPinst.
13. Pobierz oprogramowanie SAP z SAP Service Marketplace.
14. Utwórz grupę, sapsys, za pomocą grupy 1001 Identyfikatora na serwerze bazy danych HANA maszyny Wirtualnej.
15. Instalowanie platformy SAP HANA na serwerze bazy danych maszyny Wirtualnej przy użyciu platformy HANA bazy danych Lifecycle Manager (HDBLCM).
16. Zainstaluj wystąpienie SAP ASCS na serwerze aplikacji maszyny Wirtualnej.
17. Udostępnij katalogu /sapmnt między testowych maszyn wirtualnych przy użyciu systemu plików NFS. Serwer aplikacji maszyny Wirtualnej jest serwer systemu plików NFS.
18. Zainstaluj wystąpienie bazy danych, w tym platformy HANA przy użyciu SWPM na serwerze bazy danych HANA maszyny Wirtualnej.
19. Zainstaluj serwer aplikacji głównej (PAS) na serwerze aplikacji maszyny Wirtualnej.
20. Rozpocznij SAP MC. Połącz przy użyciu graficznego interfejsu użytkownika SAP i HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Przygotowywanie maszyn wirtualnych platformy Azure do ręcznej instalacji oprogramowania SAP HANA
W tej sekcji omówiono następujące tematy:

* Aktualizacje systemu operacyjnego
* Ustawienia dysku
* Jądra parametry
* Systemy plików
* Plik/etc/hosts
* Plik/etc/fstab

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Wyszukaj aktualizacje systemu operacyjnego Linux i poprawki, przed zainstalowaniem dodatkowego oprogramowania. Instalując poprawkę, można uniknąć wywołania do działu pomocy technicznej.

Upewnij się, że używasz:
* SUSE Linux Enterprise Server dla aplikacji SAP.
* Red Hat Enterprise Linux dla aplikacji SAP i Red Hat Enterprise Linux for SAP HANA. 

Jeśli jeszcze nie, należy zarejestrować wdrożenia systemu operacyjnego z Twoją subskrypcją systemu Linux, od dostawcy systemu Linux. Należy pamiętać, że SUSE zawiera obrazy systemu operacyjnego dla aplikacji SAP zawierające usług i które są zarejestrowane automatycznie.

Oto przykład sprawdzanie dostępnych poprawek w systemie SUSE Linux przy użyciu **zypper** polecenia:

 `sudo zypper list-patches`

W zależności od rodzaju problemu poprawki są klasyfikowane według kategorii i ważności. Są często używanymi wartościami dla kategorii: **zabezpieczeń**, **zalecane**, **opcjonalne**, **funkcji**, **dokumentu**, lub **yast**.
Są często używane wartości na poziomie ważności: **krytyczne**, **ważne**, **umiarkowane**, **niski**, lub **nieokreślony**.

**Zypper** polecenie sprawdza tylko w przypadku aktualizacji wymagających zainstalowane pakiety. Na przykład można użyć tego polecenia:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Można dodać parametr `--dry-run` przetestowania aktualizacji bez faktycznego aktualizowanie systemu.


### <a name="disk-setup"></a>Ustawienia dysku
Główny system plików na maszynie Wirtualnej z systemem Linux na platformie Azure obowiązuje ograniczenie rozmiaru. W związku z tym należy dołączyć dodatkowe miejsce na dysku do maszyny Wirtualnej platformy Azure na uruchamianie oprogramowania SAP. Serwer aplikacji SAP maszyn wirtualnych platformy Azure korzystanie z dysków magazynu w warstwie standardowa usługi Azure może być wystarczające. Jednakże SAP HANA DBMS maszyn wirtualnych platformy Azure, użycie dysków usługi Premium Storage dla platformy Azure dla środowiska produkcyjnego i nieprodukcyjnych implementacji jest obowiązkowy.

Na podstawie [SAP HANA TDI dyskowej](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), zalecane jest następująca konfiguracja usługi Azure Premium Storage: 

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM |  / hana/danych i dziennika/hana / <br /> Rozłożona z LVM lub MDADM | / hana/udostępnione | wolumin/root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

W konfiguracji dysku sugerowane ilości danych HANA i wolumin dziennika są umieszczane w ten sam zestaw dysków magazynu Azure premium storage, które są rozkładane LVM lub MDADM. Nie jest konieczne jest określenie dowolny poziom nadmiarowości RAID, ponieważ usługi Azure Premium Storage zapewnia trzy obrazy dysków w celu zapewnienia nadmiarowości. Aby upewnić się, że skonfigurować wystarczająco dużego magazynu, zapoznaj się z [SAP HANA TDI dyskowej](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) i [przewodnika po aktualizacji i instalacji serwera SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Należy również rozważyć woluminy przepływności inny wirtualny dysk twardy (VHD) z dysków magazynu innej usługi Azure premium zgodnie z opisem w [High-performance Premium Storage i dysków zarządzanych dla maszyn wirtualnych](../../windows/disks-types.md). 

Możesz dodać więcej dysków magazynu premium storage do platformy HANA DBMS maszyn wirtualnych do przechowywania kopii zapasowych dziennika bazy danych lub transakcji.

Więcej informacji na temat dwa główne narzędzia używane do konfigurowania rozkładanie na ten temat można znaleźć w następujących artykułach:

* [Konfigurowanie programowej macierzy RAID w systemie Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat dołączania dysków do maszyn wirtualnych platformy Azure z systemem Linux jako system operacyjny gościa, zobacz [dodać dysk do maszyny Wirtualnej z systemem Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Usługa Azure premium SSD umożliwia definiowanie tryby buforowania dysku. Dla zestawu rozłożone /hana/data i /hana/log powinno być wyłączone buforowanie dysku. Dla innych woluminów (dyski), powinna być równa buforowania tryb **tylko do odczytu**.

Aby znaleźć przykładowe szablony JSON do tworzenia maszyn wirtualnych, przejdź do [szablony szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
Szablon maszyny wirtualnej — prosty — sles jest podstawowy szablon. Zawiera on sekcję magazynu przy użyciu dysku dodatkowe 100 GB danych. Ten szablon może służyć jako podstawa. Można dostosować szablon do określonej konfiguracji.

>[!Note]
>Ważne jest, aby dołączyć dysku usługi Azure storage przy użyciu identyfikatora UUID, zgodnie z opisem w [uruchomione oprogramowanie SAP NetWeaver na maszynach wirtualnych systemu Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W środowisku testowym dwóch standardowego magazynu platformy Azure dyski zostały dołączone do serwera aplikacji SAP maszyny Wirtualnej, jak pokazano na poniższym zrzucie ekranu. Jeden dysk przechowywana instalacji oprogramowania SAP (w tym NetWeaver 7.5, graficznego interfejsu użytkownika SAP i SAP HANA). Drugi dysk zapewnić wystarczającą ilość wolnego miejsca będzie dostępnych dla dodatkowych wymagań (na przykład dane kopii zapasowej i testowania) i katalog /sapmnt (czyli profilów SAP), aby być współużytkowane przez wszystkie maszyny wirtualne, które należą do tej samej środowiskiem SAP.

![Serwer aplikacji SAP HANA dysków okna wyświetlania dwa dyski danych i ich rozmiary](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Jądra parametry
SAP HANA wymaga określonych ustawień jądra systemu Linux, które nie są częścią obrazów w galerii usługi Azure standard i należy ręcznie ustawić. W zależności od tego, czy używasz SUSE lub Red Hat parametry mogą się różnić. SAP Notes wymienionych wcześniej zapewniają informacje na temat tych parametrów. Na zrzutach ekranu przedstawiono SUSE Linux 12 z dodatkiem SP1 został użyty. 

GA 12 aplikacje SAP w systemie SLES i z systemem SLES for SAP aplikacji 12 z dodatkiem SP1 ma nowe narzędzie **dostrojone adm**, który zastępuje stare **sapconf** narzędzia. Z profilu specjalnego oprogramowania SAP HANA jest dostępna dla **dostrojone adm**. Należy dostosować system for SAP HANA, wprowadź następujące jako użytkownik główny:

   `tuned-adm profile sap-hana`

Aby uzyskać więcej informacji na temat **dostrojone adm**, zobacz [SUSE dokumentacji dotyczącej dostrojone adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na poniższym zrzucie ekranu widać jak **dostrojone adm** zmienione `transparent_hugepage` i `numa_balancing` wartości zgodnie z wymaganych ustawień platformy SAP HANA.

![Narzędzie dostrojone adm zmienia wartości zgodnie z wymaganych ustawień platformy SAP HANA](./media/hana-get-started/image005.jpg)

Utrwalenie ustawienia jądra platformy SAP HANA, należy użyć **grub2** w systemie SLES 12. Aby uzyskać więcej informacji na temat **grub2**, przejdź do [struktura plików konfiguracji](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) sekcji dokumentacji SUSE.

Poniższy zrzut ekranu przedstawia, jak ustawienia jądra zostały zmienione w pliku konfiguracji, a następnie skompilowane przy użyciu **grub2 mkconfig**:

![Ustawienia jądra zmiany w pliku konfiguracji i kompilowany przy użyciu grub2 mkconfig](./media/hana-get-started/image006.jpg)

Innym rozwiązaniem jest zmiana ustawień za pomocą YaST i **moduł ładujący rozruchu** > **jądra parametry** ustawienia:

![Na karcie Ustawienia jądra parametry w moduł ładujący rozruchu YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systemy plików
Poniższy zrzut ekranu przedstawia dwa systemy plików, które zostały utworzone na serwerze aplikacji SAP maszyny Wirtualnej na podstawie dwóch dysków dołączonych standardowego magazynu platformy Azure. Systemów plików typu XFS i są instalowane /sapdata i /sapsoftware.

Nie jest wymagane, aby struktury swoje systemy plików w ten sposób. Masz inne opcje tworzenia struktury miejsca na dysku. Najważniejszą kwestią jest zapobiec głównego systemu plików po wyczerpaniu wolnego miejsca.

![Dwa systemy plików utworzony na serwerze aplikacji SAP maszyny Wirtualnej](./media/hana-get-started/image008.jpg)

Dotyczące VM bazy danych SAP HANA, podczas instalacji bazy danych, korzystając z SAPinst (SWPM) i **typowe** opcji instalacji wszystko zostało zainstalowane w ramach /hana i /usr/sap. Domyślna lokalizacja dla kopii zapasowej dziennika platformy SAP HANA podlega /usr/sap. Ponownie ponieważ ważne jest, aby zapobiec głównego systemu plików ilość miejsca do magazynowania, upewnij się, że istnieje wystarczająca ilość wolnego miejsca, w obszarze /hana i /usr/sap przed zainstalowaniem oprogramowania SAP HANA przy użyciu SWPM.

Opis układ standardowy system plików środowiska SAP Hana, zobacz [przewodnika po aktualizacji i instalacji serwera SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Systemy plików dodatkowych, utworzony na serwerze aplikacji SAP maszyny Wirtualnej](./media/hana-get-started/image009.jpg)

Po zainstalowaniu oprogramowania SAP NetWeaver na standard w systemie SLES/SLES dla obrazu z galerii Azure 12 aplikacje SAP, zostanie wyświetlony komunikat informujący, że nie ma już miejsca wymiany, jak pokazano na poniższym zrzucie ekranu. Aby odrzucić ten komunikat, można ręcznie dodać plik wymiany przy użyciu **dd**, **mkswap**, i **swapon**. Aby dowiedzieć się, jak to zrobić, wyszukaj "Pliku wymiany ręczne dodawanie" w [przy użyciu Partycjonera YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) sekcji dokumentacji SUSE.

Innym rozwiązaniem jest, aby skonfigurować obszar wymiany przy użyciu agenta maszyny Wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [przewodnik użytkownika agenta platformy Azure w systemie Linux](../../extensions/agent-linux.md).

![Komunikat podręczny wniosku, że jest wymiany za mało miejsca](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Plik/etc/hosts
Przed rozpoczęciem instalacji SAP, upewnij się, że obejmują nazwy hostów i adresów IP maszyn wirtualnych SAP w pliku/etc/hosts. Wdrażanie VMs SAP w ramach jednej sieci wirtualnej platformy Azure, a następnie użyj wewnętrznych adresów IP, jak pokazano poniżej:

![Nazwy hostów i adresów IP maszyn wirtualnych SAP, wymienione w pliku/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Plik/etc/fstab

Warto dodać **nofail** parametrów w pliku fstab. Dzięki temu, jeśli coś pójdzie nie tak z dyskami, maszyna wirtualna przestanie odpowiadać procesu rozruchu. Należy jednak pamiętać, że nie mogą być dostępne dodatkowe miejsce na dysku i procesów może zapełnić głównego systemu plików. Jeśli brakuje /hana, nie można uruchomić oprogramowanie SAP HANA.

![Dodaj parametr nofail do pliku fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Graficzny pulpit gnome na SLES 12/z systemem SLES for SAP aplikacji 12
W tej sekcji omówiono następujące tematy:

* Instalowanie pulpit gnome i xrdp w systemie SLES 12/SLES for SAP aplikacji 12
* Uruchamianie MC SAP oparte na języku Java za pomocą przeglądarki Firefox w systemie SLES 12/SLES dla 12 aplikacje SAP

Można również użyć rozwiązań alternatywnych, takich jak Xterminal lub VNC (nie opisanych w tym przewodniku).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalowanie pulpit gnome i xrdp w systemie SLES 12/SLES for SAP aplikacji 12
W przypadku tło Windows łatwo umożliwia graficzne pulpitu bezpośrednio z poziomu maszyn wirtualnych z systemem Linux SAP do uruchamiania przeglądarki Firefox SAPinst, SAP graficznego interfejsu użytkownika, SAP MC i HANA Studio i Połącz z maszyną wirtualną za pośrednictwem protokołu RDP (Remote Desktop) z komputera, Windows. W zależności od zasady firmy dotyczące dodawania graficznego interfejsu użytkownika do środowiska produkcyjnego i systemem Linux spoza środowiska produkcyjnego systemów z procesorem, warto zainstalować GNOME na serwerze. Aby zainstalować pulpit GNOME platformy Azure w systemie SLES 12/systemu SLES dla maszyny Wirtualnej 12 aplikacje SAP:

1. Zainstaluj pulpit GNOME, wprowadzając następujące polecenie (na przykład w oknie programu PuTTY):

   `zypper in -t pattern gnome-basic`

2. Zainstaluj xrdp, aby zezwolić na połączenia z maszyną wirtualną za pośrednictwem protokołu RDP:

   `zypper in xrdp`

3. Edytuj /etc/sysconfig/windowmanager i Ustaw domyślnego menedżera okien GNOME:

   `DEFAULT_WM="gnome"`

4. Uruchom **chkconfig** aby upewnić się, że xrdp jest uruchamiany automatycznie po ponownym uruchomieniu:

   `chkconfig -level 3 xrdp on`

5. Jeśli masz problem z połączeniem RDP, spróbuj uruchomić ponownie (z okna programu PuTTY, na przykład):

   `/etc/xrdp/xrdp.sh restart`

6. Jeśli ponowne uruchomienie xrdp, opisane w poprzednim kroku nie rozwiąże problemu, sprawdź, czy plik .pid:

   `check /var/run` 

   Wyszukaj `xrdp.pid`. Jeśli możesz znaleźć, usuń go, a następnie spróbuj ponownie.

### <a name="starting-sap-mc"></a>Począwszy od MC SAP
Po zainstalowaniu pulpit GNOME, Uruchamianie graficznego MC SAP oparte na języku Java w przeglądarce Firefox podczas uruchamiania w platformy Azure w systemie SLES 12/systemu SLES dla maszyny Wirtualnej 12 aplikacje SAP może być wyświetlany błąd ze względu na brak Java-wtyczkę dla przeglądarki.

Adres URL, aby rozpocząć SAP MC jest `<server>:5<instance_number>13`.

Aby uzyskać więcej informacji, zobacz [uruchamiania konsoli sieci Web zarządzania SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Poniższy zrzut ekranu przedstawia komunikat o błędzie, która jest wyświetlana, gdy brakuje Java przeglądarki wtyczki:

![Komunikat o błędzie, brak Java-wtyczkę dla przeglądarki](./media/hana-get-started/image013.jpg)

Jednym ze sposobów rozwiązania tego problemu jest zainstalować brakiem wtyczki za pomocą YaST, jak pokazano na poniższym zrzucie ekranu:

![Instalowanie, brakiem wtyczki za pomocą YaST](./media/hana-get-started/image014.jpg)

Po ich ponownym wprowadzeniu adresu URL konsoli zarządzania SAP, pojawi się komunikat z prośbą o aktywowanie wtyczki:

![Okno dialogowe, które żądają aktywacji wtyczki](./media/hana-get-started/image015.jpg)

Można również otrzymać komunikat o błędzie dotyczący brakujący plik javafx.properties. Dotyczy to potrzebę SAP graficznego interfejsu użytkownika w wersji 7.4 Oracle Java 1.8. (Zobacz [Uwaga SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Wersja IBM Java ani pakiet openjdk dostarczane z SLES/SLES for SAP aplikacji 12 zawiera plik javafx.properties potrzebne. Jest to rozwiązanie do pobrania i zainstalowania Java SE 8 od firmy Oracle.

Aby uzyskać informacje na temat podobnych z openjdk na openSUSE, zobacz wątek dyskusji [Leap SAPGui 7.4 Java dla openSUSE 42.1](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalacja ręczna oprogramowania SAP Hana: SWPM
Serii zrzuty ekranu w tej sekcji przedstawiono podstawowe etapy instalacji SAP NetWeaver w wersji 7.5 i SAP HANA SP12, gdy używasz SWPM (SAPinst). W ramach instalacji NetWeaver w wersji 7.5 SWPM można także zainstalować jako pojedyncze wystąpienie bazy danych HANA.

W środowisku testowym przykładowe zainstalowanej tylko jeden serwer aplikacji Advanced Business Application programowania (ABAP). Jak pokazano na poniższym zrzucie ekranu, użyliśmy **rozproszony System** możliwość zainstalowania ASCS i wystąpień serwera aplikacji głównej w jednej maszynie Wirtualnej platformy Azure i platforma SAP HANA jako system bazy danych w innej maszyny Wirtualnej platformy Azure.

![ASCS i wystąpień serwera aplikacji głównej zainstalowany przy użyciu opcji rozproszonego systemu](./media/hana-get-started/image012.jpg)

Po wystąpieniu ASCS jest zainstalowany na serwerze aplikacji maszyny Wirtualnej i ustawiona na "green" w konsoli zarządzania SAP (pokazano na poniższym zrzucie ekranu), katalog /sapmnt (w tym katalogu profilu SAP) muszą być udostępnione z maszyną Wirtualną serwera bazy danych SAP HANA. Krok instalacji bazy danych musi mieć dostęp do tych informacji. Najlepszym sposobem, aby zapewnić dostęp jest używanie systemu NFS, którego można skonfigurować za pomocą YaST.

![Konsola zarządzania SAP pokazująca, że wystąpienie ASCS zainstalowane na serwerze aplikacji maszyny Wirtualnej, a wartość "green"](./media/hana-get-started/image016.jpg)

Na serwerze aplikacji maszyny Wirtualnej katalogu /sapmnt powinien udostępniane za pośrednictwem systemu plików NFS przy użyciu funkcji **rw** i **no_root_squash** opcje. Wartości domyślne to **ro** i **root_squash**, który może prowadzić do problemów podczas instalacji wystąpienia bazy danych.

![Udostępnianie katalogu /sapmnt za pośrednictwem systemu plików NFS przy użyciu opcji rw i no_root_squash](./media/hana-get-started/image017b.jpg)

Jak pokazano na następnym zrzucie ekranu, udział /sapmnt z maszyną Wirtualną serwera aplikacji musi być skonfigurowany na serwerze bazy danych SAP HANA maszyny Wirtualnej przy użyciu **klient systemu plików NFS** (i YaST).

![Udostępnij /sapmnt skonfigurowane za pomocą klienta systemu plików NFS](./media/hana-get-started/image018b.jpg)

Do przeprowadzenia instalacji rozproszonej NetWeaver w wersji 7.5 (**wystąpienie bazy danych**), jak pokazano na poniższym zrzucie ekranu, zaloguj się do maszyny Wirtualnej serwera bazy danych SAP HANA i uruchom SWPM.

![Instalowanie wystąpienia bazy danych logowania do serwera bazy danych SAP HANA maszyny Wirtualnej, a następnie uruchomiona SWPM](./media/hana-get-started/image019.jpg)

Po wybraniu **typowe** instalacji i ścieżkę do nośnika instalacyjnego, wprowadź identyfikator SID bazy danych, nazwę hosta, liczby wystąpień i hasła administratora systemu bazy danych.

![SAP HANA bazy danych systemu administratora strony logowania](./media/hana-get-started/image035b.jpg)

Wprowadź hasło dla schematu DBACOCKPIT:

![Pole wprowadzenia hasła dla schematu DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Wprowadź pytanie o hasło schematu SAPABAP1:

![Wprowadź pytanie o hasło schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu każdego zadania obok każdej fazie procesu instalacji bazy danych jest wyświetlany zielony znacznik wyboru. Komunikat "wykonywania... Wystąpienie zostało ukończone. Baza danych"jest wyświetlany.

![Zadanie ukończone okno z komunikat z potwierdzeniem](./media/hana-get-started/image023.jpg)

Po pomyślnej instalacji SAP konsoli zarządzania należy również wyświetlić wystąpienia bazy danych jako "green" i wyświetlić pełną listę procesów platformy SAP HANA (hdbindexserver, hdbcompileserver itd.).

![Okno konsoli zarządzania SAP z listy procesów platformy SAP HANA](./media/hana-get-started/image024.jpg)

Poniższy zrzut ekranu przedstawia części struktury plików w katalogu /hana/shared, który SWPM utworzony podczas instalowania platformy HANA. Ponieważ nie ma możliwości, aby określić inną ścieżkę, należy zainstalować dodatkowe miejsce na dysku w katalogu /hana przed rozpoczęciem instalacji oprogramowania SAP HANA przy użyciu SWPM. Zapobiega to ilość wolnego miejsca na głównym systemie plików.

![Struktura pliku katalogu /hana/shared tworzone podczas instalacji oprogramowania HANA](./media/hana-get-started/image025.jpg)

Ten zrzut ekranu przedstawia strukturę pliku katalogu /usr/sap:

![/ Usr/sap pliku struktury katalogów](./media/hana-get-started/image026.jpg)

Ostatnim krokiem instalacją rozproszoną ABAP jest zainstalować wystąpienie usługi głównej aplikacji serwera:

![Wystąpienie serwera ABAP instalacji przedstawiający głównej aplikacji jako ostatni krok](./media/hana-get-started/image027b.jpg)

Po zainstalowaniu aplikacji podstawowego wystąpienia serwera i SAP graficznego interfejsu użytkownika, użyj **DBA Cockpit** transakcji, aby upewnić się, poprawnie zakończenie instalacji oprogramowania SAP HANA:

![Okno DBA Cockpit potwierdzenia pomyślnej instalacji](./media/hana-get-started/image028b.jpg)

W ostatnim kroku może ma wcześniejszej instalacji oprogramowania HANA Studio w serwer aplikacji SAP maszyny Wirtualnej, a następnie połącz z wystąpieniem platformy SAP HANA, które jest uruchomiona na serwerze bazy danych maszyny Wirtualnej:

![Instalowanie programu SAP HANA Studio serwera aplikacji SAP maszyny Wirtualnej](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalacja ręczna oprogramowania SAP Hana: HDBLCM
Oprócz instalowania oprogramowania SAP HANA jako część instalacji rozproszonej, za pomocą SWPM, autonomiczny HANA można zainstalować najpierw, przy użyciu HDBLCM. Następnie możesz zainstalować oprogramowanie SAP NetWeaver w wersji 7.5, na przykład. Zrzuty ekranu w tej sekcji pokazano, jak działa ten proces.

Aby uzyskać więcej informacji o narzędziu HANA HDBLCM zobacz:

* [Wybieranie HDBLCM poprawne oprogramowanie SAP HANA, zadania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Narzędzia do zarządzania cyklem życia SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [Przewodnika po aktualizacji i instalacji serwera SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Aby uniknąć problemów z domyślnego ustawienia Identyfikatora grupy dla `\<HANA SID\>adm user` (utworzonego przez narzędzie HDBLCM), określić nową grupę o nazwie `sapsys` za pomocą Identyfikatora grupy `1001` przed zainstalowaniem oprogramowania SAP HANA za pośrednictwem HDBLCM:

![Nowe grupy "sapsys" zdefiniowane przy użyciu grupy o identyfikatorze 1001](./media/hana-get-started/image030.jpg)

Po HDBLCM zostanie uruchomiony po raz pierwszy, zostanie wyświetlone menu start proste. Wybierz element 1, **zainstalować nowy system**, jak pokazano na poniższym zrzucie ekranu:

![Opcja "Zainstaluj nowy system" w oknie uruchamiania HDBLCM](./media/hana-get-started/image031.jpg)

Poniższy zrzut ekranu przedstawia wszystkie wcześniej wybrane opcje klucza.

> [!IMPORTANT]
> Katalogi, które są nazywane dziennika platformy HANA i ilości danych, a także ścieżki instalacji (/ hana/udostępnione w tym przykładzie) i /usr/sap, nie powinien być częścią głównego systemu plików. Te katalogi należą do dysków z danymi platformy Azure, które zostały dołączone do maszyny Wirtualnej (opisanej w sekcji "Konfiguracja dysku"). Takie podejście pomaga zapobiec głównego systemu plików korzystającym z miejsca. Na poniższym zrzucie ekranu widać, że administrator systemu HANA ma identyfikator użytkownika `1005` i jest częścią `sapsys` grupy (identyfikator `1001`) który został zdefiniowany przed rozpoczęciem instalacji.

![Listę wszystkich składników oprogramowania SAP HANA klucza wybrana wcześniej](./media/hana-get-started/image032.jpg)

Możesz sprawdzić `\<HANA SID\>adm user` (`azdadm` na następującym zrzucie ekranu) szczegółowe informacje w katalogu/etc/haseł:

![HANA \<HANA SID\>adm szczegóły użytkownika na liście w katalogu/etc/haseł](./media/hana-get-started/image033.jpg)

Po zainstalowaniu oprogramowania SAP HANA przy użyciu HDBLCM można wyświetlić struktury plików w systemie SAP HANA Studio, jak pokazano na poniższym zrzucie ekranu. Schemat SAPABAP1, który zawiera wszystkie tabele oprogramowanie SAP NetWeaver, nie jest jeszcze dostępna.

![Struktura pliku oprogramowania SAP HANA w systemie SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po zainstalowaniu oprogramowania SAP HANA, SAP NetWeaver można zainstalować na jego podstawie. Jak pokazano na poniższym zrzucie ekranu, instalacja została wykonana jak instalacją rozproszoną przy użyciu SWPM (zgodnie z opisem w poprzedniej sekcji). Po zainstalowaniu wystąpienie bazy danych przy użyciu SWPM możesz wprowadzić te same dane przy użyciu HDBLCM (na przykład nazwy hosta, identyfikator SID HANA i liczby wystąpień). SWPM następnie używa istniejącej instalacji platformy HANA i dodaje więcej schematów.

![Wykonywane przy użyciu SWPM instalacją rozproszoną](./media/hana-get-started/image035b.jpg)

Poniższy zrzut ekranu przedstawia SWPM kroku instalacji, w którym możesz wprowadzić dane dotyczące schematu DBACOCKPIT:

![Krok instalacji SWPM, gdzie wprowadzeniu DBACOCKPIT schematu danych](./media/hana-get-started/image036b.jpg)

Wprowadź dane dotyczące schematu SAPABAP1:

![Wprowadzając dane dotyczące schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu instalacji wystąpienia bazy danych SWPM można wyświetlić schemat SAPABAP1 na oprogramowanie SAP HANA Studio:

![Schemat SAPABAP1 na oprogramowanie SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Na koniec, po ukończeniu instalacji SAP graficznego interfejsu użytkownika i serwera aplikacji SAP możesz sprawdzić wystąpienia bazy danych HANA przy użyciu **DBA Cockpit** transakcji:

![Wystąpienie bazy danych HANA sprawdzić przy użyciu transakcji DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Pobierania oprogramowania SAP
Oprogramowanie można pobrać z SAP Service Marketplace, jak pokazano na poniższych zrzutach ekranu.

Pobierz NetWeaver w wersji 7.5 dla systemu Linux/HANA:

 ![Instalacja usługi SAP i uaktualnianie okna pobierania NetWeaver w wersji 7.5](./media/hana-get-started/image001.jpg)

Pobierz wersję platformy SP12 HANA:

 ![Instalacja usługi SAP i uaktualnianie okna pobierania wersji platformy SP12 HANA](./media/hana-get-started/image002.jpg)

