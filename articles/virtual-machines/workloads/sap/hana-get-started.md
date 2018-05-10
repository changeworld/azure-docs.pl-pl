---
title: 'Szybki Start: Instalacja ręczna pojedynczym wystąpieniem SAP HANA na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft'
description: Przewodnik Szybki start dotyczący instalacji ręcznej z pojedynczym wystąpieniem SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 45c7e1b76f64db142fc8fdca85b1e1fa9aca6a42
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Szybki Start: Instalacja ręczna pojedynczym wystąpieniem SAP HANA na maszynach wirtualnych Azure
## <a name="introduction"></a>Wprowadzenie
Ten przewodnik pomaga skonfigurować pojedynczym wystąpieniem SAP HANA na maszynach wirtualnych platformy Azure (VM), instalując SAP NetWeaver 7.5 i SAP HANA 1.0 SP12 ręcznie. Ten przewodnik koncentruje się na temat wdrażania SAP HANA na platformie Azure. Nie zastępuje dokumentacji SAP. 

>[!Note]
>Ten przewodnik zawiera opis wdrożenia SAP HANA na maszynach wirtualnych platformy Azure. Informacji o wdrażaniu SAP HANA do wystąpień dużych HANA, zobacz [przy użyciu programu SAP na maszynach wirtualnych platformy Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że czytelnik zna takie infrastruktury jako podstawy usługi (IaaS), jako:
 * Jak wdrożyć maszyny wirtualne lub sieci wirtualnych za pośrednictwem portalu Azure lub programu PowerShell.
 * Azure i platform interfejsu wiersza polecenia (CLI), w tym możliwość użycia szablonów JavaScript Object Notation (JSON).

W tym przewodniku założono również, że czytelnik zna:
* SAP HANA i SAP NetWeaver i jak mają być instalowane lokalnie.
* Instalowanie i SAP HANA i SAP wystąpień aplikacji na platformie Azure.
* Następujące pojęcia i procedury:
   * Planowanie wdrożenia SAP na platformie Azure, w tym planowania sieci wirtualnej platformy Azure i użycia usługi Azure Storage. Zobacz [SAP NetWeaver na maszynach wirtualnych Azure (VM) — przewodnik dotyczący planowania i implementacji](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Zasady wdrażania i sposobów wdrażania maszyn wirtualnych na platformie Azure. Zobacz [wdrażania maszyn wirtualnych platformy Azure dla programu SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Wysoka dostępność dla programu SAP NetWeaver ASCS (ABAP SAP centralnej usługi), SCS (SAP centralnej usługi) i Wywołujących (obliczone rozliczenie) na platformie Azure. Zobacz [wysoka dostępność dla programu SAP NetWeaver na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Szczegółowe informacje na temat sposobów usprawnienia wydajności dzięki wykorzystaniu instalacji wielu SID ASCS/SCS na platformie Azure. Zobacz [tworzenia konfiguracji wielu SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Zasady uruchamiania programu SAP NetWeaver oparte na maszynach opartych na systemie Linux na platformie Azure. Zobacz [uruchomione na maszynach wirtualnych programu Microsoft Azure SUSE Linux SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ten przewodnik zawiera ustawienia specyficzne dla systemu Linux w maszynach wirtualnych platformy Azure i uzyskać szczegółowe informacje na temat sposobu prawidłowo dołączyć dyski magazynu Azure do maszyn wirtualnych systemu Linux.

W tej chwili maszynach wirtualnych platformy Azure certyfikowanych przez SAP SAP HANA skalowanie w pionie tylko konfiguracji. Konfiguracje skalowalnego w poziomie z obciążeniami SAP HANA nie są jeszcze obsługiwane. SAP HANA wysokiej dostępności w przypadku konfiguracji skalowania w górę, zobacz [wysoką dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Jeśli są próbująca uzyskać wystąpienia SAP HANA lub S/4HANA lub system BW/4HANA wdrożona w czasie bardzo szybko, należy rozważyć użycie [biblioteki urządzenia chmury SAP](http://cal.sap.com). Możesz znaleźć dokumentację dotyczącą wdrażania, na przykład S/4HANA systemu za pośrednictwem SAP CAL na platformie Azure w [w tym przewodniku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Trzeba będzie subskrypcji platformy Azure i użytkownika SAP, który może być zarejestrowane przy użyciu biblioteki urządzenia chmury SAP.

## <a name="additional-resources"></a>Zasoby dodatkowe
### <a name="sap-hana-backup"></a>Kopia zapasowa SAP HANA
Aby uzyskać informacji na temat tworzenia kopii zapasowej bazy danych SAP HANA na maszynach wirtualnych Azure zobacz:
* [Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup na poziomie plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [SAP HANA kopię zapasową pamięci masowej migawki](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Biblioteka urządzenia chmury SAP
Aby informacji na temat używania biblioteki urządzenia chmury SAP do wdrożenia S/4HANA lub BW/4HANA, zobacz [wdrożenia SAP S/4HANA lub BW/4HANA w systemie Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA obsługiwanych systemów operacyjnych
Aby uzyskać informacje na systemy operacyjne obsługiwane SAP HANA, zobacz [SAP Obsługa Uwaga #2235581 - SAP HANA: systemy operacyjne obsługiwane](https://launchpad.support.sap.com/#/notes/2235581/E). Maszyny wirtualne platformy Azure obsługuje tylko podzbiór tych systemów operacyjnych. Do wdrożenia SAP HANA na platformie Azure, obsługiwane są następujące systemy operacyjne: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Dodatkową dokumentację SAP SAP HANA o różnych systemów operacyjnych Linux zobacz:

* [Uwaga Obsługa SAP 171356 # - oprogramowania SAP w systemie Linux: informacje ogólne](https://launchpad.support.sap.com/#/notes/1984787)
* [Obsługa Uwaga 1944799 # - SAP HANA wskazówki dotyczące instalacji systemu operacyjnego SLES SAP](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Uwaga Obsługa SAP #2205917 - SAP HANA DB zalecane ustawienia systemu operacyjnego dla SLES 12 aplikacje SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Uwaga Obsługa SAP 1984787 # - SUSE Linux Enterprise Server 12: Informacje o instalacji](https://launchpad.support.sap.com/#/notes/1984787)
* [Uwaga Obsługa SAP 1391070 # - rozwiązań UUID systemu Linux](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP Uwaga Obsługa 2009879 # - SAP HANA wytyczne dotyczące systemu operacyjnego systemu Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: OS zalecane ustawienia dla systemów RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP monitorowania na platformie Azure
Aby uzyskać informacji na temat SAP monitorowania na platformie Azure zobacz:

* [Uwaga SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Ta uwaga omówiono SAP "rozszerzonego monitorowania" z maszyn wirtualnych systemu Linux na platformie Azure. 
* [Uwaga SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Ta uwaga omówiono SAPOSCOL w systemie Linux. 
* [Uwaga SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Ta uwaga omówiono kluczowe metryki monitorowania dla programu SAP w systemie Microsoft Azure.

### <a name="azure-vm-types"></a>Typy maszyny Wirtualnej platformy Azure
Typy maszyny Wirtualnej platformy Azure i scenariusze obsługiwane SAP obciążenia używane z SAP HANA są udokumentowane w artykule [IaaS platformy certyfikat SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure typów maszyny Wirtualnej, które certyfikowanych przez SAP SAP NetWeaver lub S/4HANA warstwy aplikacji są udokumentowane w artykule [1928533 Uwaga SAP — aplikacje SAP na platformie Azure: typy obsługiwanych produktów i maszyny Wirtualnej Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integracja SAP Linux Azure jest obsługiwana tylko dla usługi Azure Resource Manager, a nie klasycznego modelu wdrażania. 

## <a name="manual-installation-of-sap-hana"></a>Instalacja ręczna SAP HANA
W tym przewodniku opisano, jak ręcznie zainstalować SAP HANA na maszynach wirtualnych platformy Azure na dwa sposoby:

* Za pomocą Menedżera inicjowania obsługi oprogramowania SAP (SWPM) jako część instalacji rozproszonej NetWeaver w kroku "wystąpienia bazy danych instalacji"
* Za pomocą SAP HANA bazy danych narzędzie Menedżer cyklu życia, HDBLCM, a następnie zainstaluj NetWeaver

Umożliwia także SWPM instalację wszystkich składników (SAP HANA SAP serwera aplikacji i wystąpienia ASCS) w jedną maszynę Wirtualną z jednej, zgodnie z opisem w tym [anonsu blogu SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ta opcja nie jest opisane w tym przewodniku Szybki Start, ale problemy, które należy wziąć pod uwagę są takie same.

Przed rozpoczęciem instalacji, zaleca się przeczytanie "Preparing Azure maszyn wirtualnych dla ręcznej instalacji SAP HANA" dalszej części tego przewodnika. Dzięki temu można zapobiec kilka podstawowych błędów, które mogą wystąpić, gdy używasz tylko domyślną konfigurację maszyny Wirtualnej platformy Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Klucza kroki instalacji SAP HANA używania SAP SWPM
Ta sekcja zawiera klucza kroki ręczne, jednego wystąpienia instalacji SAP HANA, korzystając z programu SAP SWPM instalacji rozproszonej SAP NetWeaver 7.5. Poszczególne kroki są objaśnione szczegółowo zrzuty ekranu w dalszej części tego przewodnika.

1. Tworzenie sieci wirtualnej platformy Azure, zawierającą testu dwóch maszyn wirtualnych.
2. Wdróż dwie maszyny wirtualne Azure z systemami operacyjnymi (w naszym przykładzie SUSE Linux Enterprise Server (SLES) i SLES dla dodatku SP1 dla programu SAP aplikacji 12), zgodnie z modelem usługi Azure Resource Manager.
3. Dołącz dwa Azure standard lub premium magazynu dysków (na przykład dysków 75 GB lub 500 GB) serwerowi aplikacji maszyny Wirtualnej.
4. Dołącz dyski magazynu premium do serwera HANA DB maszyny Wirtualnej. Aby uzyskać więcej informacji zobacz sekcję "Dysk instalacji" w dalszej części tego przewodnika.
5. W zależności od wymagań dotyczących rozmiaru lub przepływności dołączyć wiele dysków, a następnie utwórz woluminy rozłożone przy użyciu zarządzania woluminu logicznego lub narzędzia administracyjnego wielu urządzeń (MDADM) na poziomie systemu operacyjnego w ramach maszyny Wirtualnej.
6. Utwórz XFS systemów plików na dołączone dyski lub woluminy logiczne.
7. Zainstaluj nowych systemów plików XFS na poziomie systemu operacyjnego. Użyj jednego systemu plików dla oprogramowania SAP. Użyć systemu plików dla katalogu /sapmnt i kopii zapasowych, na przykład. Na serwerze bazy danych programu SAP HANA zainstalować XFS systemów plików na dyski magazynu premium jako /hana i /usr/sap. Ten proces jest niezbędne zapobiec przepełnieniu głównego systemu plików, która nie jest duży na maszynach wirtualnych systemu Linux platformy Azure.
8. Wprowadź lokalne adresy IP maszyn wirtualnych testu w pliku/etc/hosts.
9. Wprowadź **nofail** parametru w pliku/etc/fstab.
10. Ustaw parametry jądra systemu Linux w zależności od używanej wersji systemu operacyjnego Linux. Aby uzyskać więcej informacji zobacz odpowiednie notatki SAP HANA i w sekcji "Jądra parametry" w tym przewodniku.
11. Zwiększ obszar wymiany.
12. Opcjonalnie zainstalować graficznego pulpitu na testowej maszyny wirtualnej. W przeciwnym razie użyj SAPinst instalacji zdalnej.
13. Pobieranie oprogramowania SAP z portalu Marketplace usługi SAP.
14. Zainstaluj wystąpienie SAP ASCS na serwerze aplikacji maszyny Wirtualnej.
15. Udostępnij katalogu /sapmnt między testu maszyn wirtualnych przy użyciu systemu plików NFS. Serwer aplikacji maszyny Wirtualnej jest serwer systemu plików NFS.
16. Zainstaluj wystąpienie bazy danych, w tym HANA, przy użyciu SWPM na serwerze bazy danych maszyny Wirtualnej.
17. Zainstaluj serwer aplikacji głównej (adresy) na serwerze aplikacji maszyny Wirtualnej.
18. Uruchom konsolę zarządzania SAP (SAP MC). Na przykład połączyć się z graficznym interfejsem użytkownika SAP lub HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Klucza kroki instalacji SAP HANA używania HDBLCM
Ta sekcja zawiera klucza kroki ręczne, jednego wystąpienia instalacji SAP HANA, korzystając z programu SAP HDBLCM instalacji rozproszonej SAP NetWeaver 7.5. Bardziej szczegółowo na zrzutach ekranu w tym przewodniku opisano poszczególne kroki.

1. Tworzenie sieci wirtualnej platformy Azure, zawierającą testu dwóch maszyn wirtualnych.
2. Wdróż dwóch maszyn wirtualnych platformy Azure z systemami operacyjnymi (w naszym przykładzie SLES i SLES SAP aplikacji 12 z dodatkiem SP1) zgodnie z modelem usługi Azure Resource Manager.
3. Dołącz dwa Azure standard lub premium magazynu dysków (na przykład dysków 75 GB lub 500 GB) serwerowi aplikacji maszyny Wirtualnej.
4. Dołącz dyski magazynu premium do serwera HANA DB maszyny Wirtualnej. Aby uzyskać więcej informacji zobacz sekcję "Dysk instalacji" w dalszej części tego przewodnika.
5. W zależności od wymagań dotyczących rozmiaru lub przepływności Dołącz wiele dysków i utworzyć woluminy rozłożone za pomocą zarządzania woluminu logicznego lub narzędzia administracyjnego wielu urządzeń (MDADM) na poziomie systemu operacyjnego w ramach maszyny Wirtualnej.
6. Utwórz XFS systemów plików na dołączone dyski lub woluminy logiczne.
7. Zainstaluj nowych systemów plików XFS na poziomie systemu operacyjnego. Używa jeden system plików dla oprogramowania SAP i jeden z nich do katalogu /sapmnt i kopii zapasowych, np. Na serwerze bazy danych programu SAP HANA zainstalować XFS systemów plików na dyski magazynu premium jako /hana i /usr/sap. Ten proces jest niezbędne zapobiec przepełnieniu głównego systemu plików, która nie jest duży na maszynach wirtualnych systemu Linux platformy Azure.
8. Wprowadź lokalne adresy IP maszyn wirtualnych testu w pliku/etc/hosts.
9. Wprowadź **nofail** parametru w pliku/etc/fstab.
10. Ustaw parametry jądra w zależności od używanej wersji systemu operacyjnego Linux. Aby uzyskać więcej informacji zobacz odpowiednie notatki SAP HANA i w sekcji "Jądra parametry" w tym przewodniku.
11. Zwiększ obszar wymiany.
12. Opcjonalnie zainstalować graficznego pulpitu na testowej maszyny wirtualnej. W przeciwnym razie użyj SAPinst instalacji zdalnej.
13. Pobieranie oprogramowania SAP z portalu Marketplace usługi SAP.
14. Utwórz grupę, sapsys, z grupą 1001 identyfikator na serwerze bazy danych HANA maszyny Wirtualnej.
15. Zainstaluj SAP HANA na serwerze bazy danych maszyny Wirtualnej za pomocą HANA bazy danych Lifecycle Manager (HDBLCM).
16. Zainstaluj wystąpienie SAP ASCS na serwerze aplikacji maszyny Wirtualnej.
17. Udostępnij katalogu /sapmnt między testu maszyn wirtualnych przy użyciu systemu plików NFS. Serwer aplikacji maszyny Wirtualnej jest serwer systemu plików NFS.
18. Zainstaluj wystąpienie bazy danych, w tym HANA, przy użyciu SWPM na serwerze bazy danych HANA maszyny Wirtualnej.
19. Zainstaluj serwer aplikacji głównej (adresy) na serwerze aplikacji maszyny Wirtualnej.
20. Uruchom SAP MC. Połącz za pomocą interfejsu GUI SAP lub HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Przygotowywanie maszyn wirtualnych platformy Azure do ręcznego instalowania SAP HANA
W tej sekcji omówiono następujące tematy:

* Aktualizacje systemu operacyjnego
* Konfiguracja dysków
* Parametry jądra
* systemy plików
* Pliku/etc/hosts
* Plik/etc/fstab

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Sprawdź, czy system operacyjny Linux aktualizacje i poprawki przed zainstalowaniem dodatkowego oprogramowania. Instalując poprawkę, można uniknąć wywołanie personelem pomocy technicznej.

Upewnij się, że używasz:
* SUSE Linux Enterprise Server dla programu SAP aplikacji.
* Red Hat Enterprise Linux dla aplikacje SAP lub Red Hat Enterprise Linux dla programu SAP HANA. 

Jeśli nie jest jeszcze z subskrypcją systemu Linux od dostawcy systemu Linux należy zarejestrować wdrażania systemu operacyjnego. Należy pamiętać, że SUSE ma obrazów systemu operacyjnego dla aplikacje SAP, które już zawierają usług i które są zarejestrowane automatycznie.

Oto przykład sprawdzania dostępnych poprawek dla systemu SUSE Linux przy użyciu **zypper** polecenia:

 `sudo zypper list-patches`

W zależności od rodzaju problem poprawki są klasyfikowane według kategorii i ważności. Są często używane wartości dla kategorii: **zabezpieczeń**, **zalecane**, **opcjonalne**, **funkcji**, **dokumentu**, lub **yast**.
Są często używane wartości ważności: **krytyczne**, **ważne**, **umiarkowane**, **niski**, lub **nieokreślony**.

**Zypper** polecenie sprawdza tylko aktualizacje, które wymagają zainstalowanych pakietów. Na przykład można użyć tego polecenia:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Można dodać parametr `--dry-run` testowania aktualizacji bez faktycznie aktualizowanie systemu.


### <a name="disk-setup"></a>Konfiguracja dysków
System plików głównego w Maszynę wirtualną systemu Linux na platformie Azure obowiązuje ograniczenie rozmiaru. W związku z tym należy dołączyć dodatkowe miejsce na dysku do maszyny Wirtualnej platformy Azure do uruchamiania programu SAP. Dla serwera aplikacji SAP maszynach wirtualnych platformy Azure używanie usługi Azure standard storage dysków może być wystarczające. Jednak dla SAP HANA DBMS maszynach wirtualnych platformy Azure, użycie dysków Azure Premium Storage wdrożenia produkcyjnego i nieprodukcyjnych jest obowiązkowe.

Na podstawie [SAP HANA TDI przestrzeni dyskowej jest potrzebne](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), proponowana jest następująca konfiguracja usługi Azure Premium Storage: 

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM |  / hana/danych i dziennika/hana / <br /> paski LVM lub MDADM | / hana/udostępnionych | wolumin/root | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

W konfiguracji dysku sugerowane HANA ilość danych i wolumin dziennika są umieszczane na tym samym zestawie dyski magazynu Azure premium, które są rozkładane LVM lub MDADM. Nie jest konieczne jest określenie dowolny poziom nadmiarowości RAID, ponieważ usługa Azure Premium Storage przechowuje trzy obrazy dysków w celu zapewnienia nadmiarowości. Aby upewnić się, aby skonfigurować niewystarczającego miejsca w magazynie, zapoznaj się [SAP HANA TDI przestrzeni dyskowej jest potrzebne](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) i [przewodnik aktualizacji i instalacji serwera SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Należy również rozważyć ilości przepływności innego wirtualnego dysku twardego (VHD) dyski magazynu różnych Azure premium zgodnie z opisem w [zarządzanych dysków dla maszyny wirtualne i Magazyn w warstwie Premium wysokiej wydajności](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Można dodać więcej dysków w warstwie premium magazynu z maszynami wirtualnymi systemu DBMS HANA do przechowywania kopii zapasowych dziennika bazy danych lub transakcji.

Aby uzyskać więcej informacji na temat dwa główne narzędzia używane do konfigurowania rozkładanie zobacz następujące artykuły:

* [Należy skonfigurować oprogramowanie RAID w systemie Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skonfiguruj LVM na Maszynę wirtualną systemu Linux na platformie Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji o związane dyski z systemem Linux jako system operacyjny gościa maszynach wirtualnych platformy Azure, zobacz [dodać dysk do maszyny Wirtualnej systemu Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Usługa Azure Premium Storage można zdefiniować dyskowej pamięci podręcznej trybów. Dla zestawu rozłożone /hana/data i /hana/log powinny być wyłączone buforowanie dysku. Inne woluminy (dysków) powinna być równa tryb buforowania **tylko do odczytu**.

Aby uzyskać więcej informacji, zobacz [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../../windows/premium-storage.md).

Aby znaleźć przykład szablony JSON do tworzenia maszyn wirtualnych, przejdź do [szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates).
Szablon maszyny wirtualnej — prosty sles jest podstawowy szablon. Obejmuje on sekcji magazynu, przy użyciu dysku dodatkowe 100 GB danych. Ten szablon może służyć jako podstawa. Szablon można dostosować do określonej konfiguracji.

>[!Note]
>Ważne jest, aby dołączyć dysku magazynu platformy Azure przy użyciu identyfikatora UUID, zgodnie z opisem w [systemem SAP NetWeaver na maszynach wirtualnych programu Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W środowisku testowym dwie usługi Azure storage standardowych dysków były podłączone do serwera aplikacji SAP maszyny Wirtualnej, jak pokazano na poniższym zrzucie ekranu. Jeden dysk przechowywane instalacji oprogramowania SAP (w tym NetWeaver 7.5 SAP graficznego interfejsu użytkownika i SAP HANA). Drugiego dysku zapewnić, że wystarczającą ilość wolnego miejsca będą dostępne dla dodatkowych wymagań (na przykład dane kopii zapasowej i testowych) i współdzielona przez wszystkie maszyny wirtualne, które należą do tej samej pozioma SAP katalog /sapmnt (to znaczy SAP profilów).

![Wyświetlanie danych dwóch dysków i ich rozmiary SAP HANA aplikacji serwera dyski okna](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametry jądra
SAP HANA wymaga określonych ustawień jądra systemu Linux, które nie są częścią obrazów standardowe galerii Azure i należy ręcznie ustawić. W zależności od tego, czy używasz SUSE lub Red Hat parametry mogą się różnić. Uwagi dotyczące SAP wymienione wcześniej nadaj informacji na temat tych parametrów. Pokazano na zrzutach ekranu użyto SUSE Linux 12 z dodatkiem SP1. 

SLES dla GA 12 aplikacje SAP i SLES dla dodatku SP1 dla programu SAP aplikacji 12 mają nowe narzędzie **dopasowane adm**, który zastępuje stary **sapconf** narzędzia. Jest dostępna dla profilu specjalnego SAP HANA **dopasowane adm**. Aby Dostrój systemu SAP HANA, wprowadź następujące jako użytkownik główny:

   `tuned-adm profile sap-hana`

Aby uzyskać więcej informacji na temat **dopasowane adm**, zobacz [SUSE dokumentację dotyczącą dopasowane adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na poniższym zrzucie ekranu widać, jak **dopasowane adm** zmienić `transparent_hugepage` i `numa_balancing` wartości zgodnie z wymaganych ustawień SAP HANA.

![Narzędzie dopasowane adm zmiany wartości zgodnie z ustawieniami SAP HANA wymagane](./media/hana-get-started/image005.jpg)

Aby ustawienia jądra SAP HANA były trwałe, należy użyć **grub2** na SLES 12. Aby uzyskać więcej informacji na temat **grub2**, przejdź do [struktury plików konfiguracji](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) części dokumentacji SUSE.

Poniższy zrzut ekranu przedstawia sposób ustawienia jądra zostały zmienione w pliku konfiguracji i następnie skompilować przy użyciu **grub2 mkconfig**:

![Ustawienia jądra zmienić w pliku konfiguracji i skompilowanych przy użyciu grub2 mkconfig](./media/hana-get-started/image006.jpg)

Innym rozwiązaniem jest zmiana ustawień za pomocą YaST i **moduł ładujący rozruchu** > **parametry jądra** ustawienia:

![Karta Ustawienia parametrów jądra w moduł ładujący rozruchu YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>systemy plików
Poniższy zrzut ekranu zawiera dwa systemy plików, które zostały utworzone na serwerze aplikacji SAP maszyny Wirtualnej na podstawie dwóch dysków dołączonych standardowego magazynu Azure. Systemów plików typu XFS i magazynowe /sapdata i /sapsoftware.

Nie jest to konieczne do struktury sieci systemów plików w ten sposób. Masz inne opcje struktury miejsce na dysku. Najbardziej ważnym zagadnieniem jest aby zapobiec głównego systemu plików po wyczerpaniu wolnego miejsca.

![Dwa systemy plików utworzonych na serwerze aplikacji programu SAP maszyny Wirtualnej](./media/hana-get-started/image008.jpg)

Dotyczące SAP HANA DB maszyny Wirtualnej, podczas instalacji bazy danych, korzystając z SAPinst (SWPM) i **typowe** opcji instalacji, wszystko jest zainstalowane w ramach /hana i /usr/sap. Domyślna lokalizacja kopii zapasowej dziennika SAP HANA podlega /usr/sap. Ponownie ponieważ ważne jest, aby zapobiec głównego systemu plików kończy się miejsce do magazynowania, upewnij się, że jest za mało wolnego miejsca, w obszarze /hana i /usr/sap przed rozpoczęciem instalacji przy użyciu SWPM SAP HANA.

Opis SAP HANA układ standardowy system plików, zobacz [przewodnik aktualizacji i instalacji serwera SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Systemy plików dodatkowe utworzona na serwerze aplikacji programu SAP maszyny Wirtualnej](./media/hana-get-started/image009.jpg)

Po zainstalowaniu programu SAP NetWeaver na standardowe SLES/SLES obrazu galerii Azure 12 aplikacje SAP, zostanie wyświetlony komunikat z informacją, że brakuje miejsca wymiany, jak pokazano na poniższym zrzucie ekranu. Aby odrzucić tę wiadomość, można ręcznie dodać plik wymiany przy użyciu **dd**, **mkswap**, i **swapon**. Aby dowiedzieć się, jak to zrobić, wyszukaj "Plik wymiany ręczne dodawanie" w [przy użyciu Partycjonera YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) części dokumentacji SUSE.

Innym rozwiązaniem jest skonfigurowanie obszar wymiany przy użyciu agenta maszyny Wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika agenta systemu Linux Azure](../../extensions/agent-linux.md).

![Udzielanie porad jest obszar wymiany niewystarczające komunikat podręczny](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Pliku/etc/hosts
Przed rozpoczęciem instalacji programu SAP, upewnij się, że uwzględnienia nazwy hosta i adresy IP maszyn wirtualnych SAP w pliku/etc/hosts. Wdrażanie VMs SAP w ramach jednej sieci wirtualnej platformy Azure, a następnie użyj adresów IP, jak pokazano poniżej:

![Nazwy hosta i adresy IP maszyn wirtualnych SAP wymienione w pliku/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Plik/etc/fstab

Warto dodać **nofail** parametru do pliku fstab. Dzięki temu, jeśli wystąpią problemy z dyskami, maszyny Wirtualnej nie wykraczać procesu rozruchu. Należy jednak pamiętać, że dodatkowe miejsce na dysku mogą nie być dostępne i procesów może zapełnić głównego systemu plików. Jeśli brakuje /hana, nie można uruchomić SAP HANA.

![Dodaj parametr nofail do pliku fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Graficzny GNOME pulpitu na SLES 12/SLES dla 12 aplikacje SAP
W tej sekcji omówiono następujące tematy:

* Instalowanie GNOME pulpitu i xrdp na SLES 12/SLES dla 12 aplikacje SAP
* Uruchomiona opartych na języku Java MC SAP przy użyciu przeglądarki Firefox na SLES 12/SLES 12 aplikacje SAP

Można również użyć rozwiązań alternatywnych, takich jak Xterminal lub VNC (nie opisano w tym przewodniku).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalowanie GNOME pulpitu i xrdp na SLES 12/SLES dla 12 aplikacje SAP
Jeśli masz tła systemu Windows, łatwo służy graficznego pulpitu bezpośrednio z poziomu maszyn wirtualnych systemu Linux SAP do uruchamiania przeglądarki Firefox, SAPinst, SAP graficznego interfejsu użytkownika, SAP MC lub HANA Studio i połączenie z maszyną Wirtualną za pomocą protokołu RDP (Remote Desktop) komputer z systemem Windows. W zależności od zasad firmy o dodawaniu graficzne interfejsy użytkownika do miejsca produkcyjnego i Linux nieprodukcyjnych systemów z procesorem, rozważ ponowne zainstalowanie GNOME na serwerze. Aby zainstalować pulpitu GNOME na 12/SLES Azure SLES dla maszyny Wirtualnej 12 aplikacje SAP:

1. Zainstaluj pulpitu GNOME, wprowadzając następujące polecenie (na przykład w oknie programu PuTTY):

   `zypper in -t pattern gnome-basic`

2. Zainstaluj xrdp do zezwalania na połączenie z maszyną wirtualną za pośrednictwem protokołu RDP:

   `zypper in xrdp`

3. Edytuj /etc/sysconfig/windowmanager i ustawienie domyślnego menedżera okien GNOME:

   `DEFAULT_WM="gnome"`

4. Uruchom **chkconfig** aby upewnić się, że xrdp jest uruchamiany automatycznie po ponownym uruchomieniu:

   `chkconfig -level 3 xrdp on`

5. Jeśli masz problem z połączeniem RDP, spróbuj ponownie uruchomić usługę (z okna programu PuTTY, na przykład):

   `/etc/xrdp/xrdp.sh restart`

6. Jeśli ponowne uruchomienie xrdp, opisane w poprzednim kroku nie rozwiąże problemu, sprawdź plik .pid:

   `check /var/run` 

   Wyszukaj `xrdp.pid`. Jeśli możesz znaleźć, usuń go, a następnie spróbuj ponownie uruchomić usługę ponownie.

### <a name="starting-sap-mc"></a>Uruchamianie SAP MC
Po zainstalowaniu na pulpicie GNOME graficznego MC SAP opartych na języku Java, począwszy od Firefox podczas pracy w usłudze Azure SLES 12/SLES dla maszyny Wirtualnej 12 aplikacje SAP może być wyświetlany błąd z powodu brakujących Java — dodatek plug-in dla przeglądarki.

Adres URL do uruchomienia SAP MC jest `<server>:5<instance_number>13`.

Aby uzyskać więcej informacji, zobacz [uruchamiania konsoli sieci Web zarządzania SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Poniższy zrzut ekranu przedstawia komunikat o błędzie, który jest wyświetlane, gdy brak Java przeglądarki wtyczki:

![Komunikat o błędzie wskazujący brak Java — dodatek plug-in dla przeglądarki](./media/hana-get-started/image013.jpg)

Jednym ze sposobów rozwiązania tego problemu jest zainstalowanie, brakujący wtyczki za pomocą YaST, jak pokazano na poniższym zrzucie ekranu:

![Brak wtyczka jest instalowany za pomocą YaST](./media/hana-get-started/image014.jpg)

Gdy ponownie wprowadzić adres URL konsoli zarządzania programu SAP, pojawi się komunikat z pytaniem, aby aktywować wtyczki:

![Okno dialogowe, które żądają aktywacji wtyczki](./media/hana-get-started/image015.jpg)

Może również pojawi się komunikat o błędzie dotyczący brakującego pliku javafx.properties. Dotyczy to wymaganie programu Oracle Java 1.8 dla programu SAP 7.4 graficznego interfejsu użytkownika. (Zobacz [Uwaga SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Wersja języka IBM Java ani pakietu openjdk dostarczone z SLES/SLES dla 12 aplikacje SAP zawiera plik javafx.properties potrzebne. Rozwiązanie jest pobrać i zainstalować Java SE 8 z programu Oracle.

Informacje o podobnych problem z openjdk na openSUSE, zobacz wątek dyskusji [Leap Java 7.4 SAPGui dla openSUSE 42.1](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalacja ręczna SAP HANA: SWPM
Serii zrzuty ekranu w tej sekcji przedstawiono klucza kroki dotyczące instalowania programu SAP NetWeaver 7.5 i SAP HANA SP12 używania SWPM (SAPinst). W ramach instalacji NetWeaver 7.5 SWPM można także zainstalować HANA bazy danych jako pojedynczego wystąpienia.

W środowisku testowym próbki zainstalowano tylko jeden serwer aplikacji zaawansowane biznesowych aplikacji programowania (ABAP). Jak pokazano na poniższym zrzucie ekranu, użyliśmy **rozproszony System** możliwość zainstalowania ASCS i wystąpień serwera aplikacji głównej w jednej maszynie Wirtualnej platformy Azure i SAP HANA jako system bazy danych w innej maszynie Wirtualnej platformy Azure.

![ASCS i wystąpień serwera aplikacji głównej instalowany przy użyciu opcji rozproszony System](./media/hana-get-started/image012.jpg)

Po wystąpieniu ASCS jest zainstalowany na serwerze aplikacji maszyny Wirtualnej i jest ustawiona na "green" w konsoli zarządzania programu SAP (przedstawiono na poniższym zrzucie ekranu), katalog /sapmnt (w tym katalogu profilu SAP) musi być udostępniona z serwerem SAP HANA DB maszyny Wirtualnej. Krok instalacji bazy danych musi mieć dostęp do tych informacji. Najlepszym sposobem zapewnienia dostępu jest używanie systemu NFS, które można skonfigurować za pomocą YaST.

![Konsola zarządzania SAP przedstawiający wystąpienia ASCS zainstalowane na serwerze aplikacji maszyny Wirtualnej i wartość "green"](./media/hana-get-started/image016.jpg)

Na serwerze aplikacji maszyny Wirtualnej należy udostępniony katalog /sapmnt za pomocą systemu plików NFS przy użyciu **rw** i **no_root_squash** opcje. Wartości domyślne są **ro** i **root_squash**, który może prowadzić do problemów podczas instalacji tego wystąpienia bazy danych.

![Udostępnianie katalogu /sapmnt za pomocą systemu plików NFS przy użyciu opcji rw i no_root_squash](./media/hana-get-started/image017b.jpg)

Jako dalej zrzut ekranu pokazuje, udziału /sapmnt z serwera aplikacji maszyny Wirtualnej musi być skonfigurowane na serwerze SAP HANA DB maszyny Wirtualnej za pomocą **klient systemu plików NFS** (i YaST).

![Udział /sapmnt skonfigurowane za pomocą klienta systemu plików NFS](./media/hana-get-started/image018b.jpg)

Do przeprowadzenia instalacji rozproszonej NetWeaver 7.5 (**wystąpienie bazy danych**), jak pokazano na poniższym zrzucie ekranu, zaloguj się do serwera SAP HANA DB maszyny Wirtualnej, i uruchom SWPM.

![Instalowanie wystąpienia bazy danych przez logowanie do serwera SAP HANA DB maszyny Wirtualnej i uruchamianie SWPM](./media/hana-get-started/image019.jpg)

Po wybraniu **typowe** instalacji i ścieżkę do nośnika instalacyjnego, wprowadź identyfikator SID bazy danych, nazwy hosta, liczby wystąpień i hasła administratora systemu bazy danych.

![SAP HANA bazy danych systemu administratora strony logowania](./media/hana-get-started/image035b.jpg)

Wprowadź hasło dla schematu DBACOCKPIT:

![Pole wprowadzania hasła dla schematu DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Wprowadź pytanie o hasło schematu SAPABAP1:

![Wprowadź pytanie o hasło schematu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu każdego zadania zielony znacznik wyboru jest wyświetlany obok każdej fazy procesu instalacji bazy danych. Komunikat "wykonywania... Baza danych została ukończona wystąpienia"jest wyświetlany.

![Zadanie zakończone okna komunikat](./media/hana-get-started/image023.jpg)

Po pomyślnej instalacji konsoli zarządzania programu SAP, należy również Pokaż wystąpienie bazy danych jako "green" i wyświetlić pełną listę procesów SAP HANA (hdbindexserver, hdbcompileserver i tak dalej).

![Okno konsoli zarządzania programu SAP z listą procesów SAP HANA](./media/hana-get-started/image024.jpg)

Poniższy zrzut ekranu przedstawia części struktury plików w katalogu /hana/shared SWPM utworzony podczas instalacji HANA. Ponieważ nie ma żadnych opcji, aby określić inną ścieżkę, należy zainstalować przy użyciu SWPM dodatkowe miejsce na dysku w katalogu /hana przed rozpoczęciem instalacji SAP HANA. Zapobiega to główny systemu plików z brakiem wolnego miejsca.

![Struktura pliku katalogów /hana/shared utworzony podczas instalacji HANA](./media/hana-get-started/image025.jpg)

Ten zrzut ekranu przedstawia struktury plików w katalogu /usr/sap:

![/ Usr/sap pliku struktura katalogów](./media/hana-get-started/image026.jpg)

Ostatni krok instalacją rozproszoną ABAP jest instalacji aplikacji głównej wystąpienia serwera:

![Wystąpienie serwera aplikacji głównej przedstawiający instalacji ABAP ostatni krok](./media/hana-get-started/image027b.jpg)

Po zainstalowaniu aplikacji głównej wystąpienie serwera i SAP graficznego interfejsu użytkownika, użyj **Panelu sterowania DBA** transakcji o potwierdzenie instalacji SAP HANA zakończył prawidłowo:

![Okno Panel sterowania DBA potwierdzenia pomyślnej instalacji](./media/hana-get-started/image028b.jpg)

Jako ostatni krok może chcesz wcześniejszej instalacji HANA Studio na serwerze aplikacji programu SAP maszyny Wirtualnej, a następnie połącz się z wystąpieniem SAP HANA, która jest uruchomiona na serwerze bazy danych maszyny Wirtualnej:

![Instalowanie programu SAP HANA Studio na serwerze aplikacji programu SAP maszyny Wirtualnej](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalacja ręczna SAP HANA: HDBLCM
Oprócz instalowania SAP HANA jako część instalacji rozproszonych za pomocą SWPM, autonomiczne HANA można zainstalować po pierwsze, za pomocą HDBLCM. Na przykład można zainstalować programu SAP NetWeaver 7.5 lub. Zrzuty ekranu w tej sekcji przedstawiono, jak działa ten proces.

Aby uzyskać więcej informacji o narzędziu HANA HDBLCM zobacz:

* [Wybieranie HDBLCM HANA SAP poprawne zadania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Narzędzia do zarządzania cyklem życia SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [Przewodnik dotyczący aktualizacji i instalacji serwera SAP HANA](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Aby uniknąć problemów z domyślnego ustawienia Identyfikatora grupy dla `\<HANA SID\>adm user` (utworzonego przez narzędzie HDBLCM), zdefiniuj nową grupę o nazwie `sapsys` przy użyciu Identyfikatora grupy `1001` przed zainstalowaniem SAP HANA za pośrednictwem HDBLCM:

![Nowe grupy "sapsys" zdefiniowane przy użyciu grupy o identyfikatorze 1001](./media/hana-get-started/image030.jpg)

Po uruchomieniu HDBLCM po raz pierwszy, zostanie wyświetlony menu start proste. Wybierz element 1, **zainstalować nowy system**, jak pokazano na poniższym zrzucie ekranu:

![Opcja "Zainstaluj nowego systemu" w oknie uruchamiania HDBLCM](./media/hana-get-started/image031.jpg)

Poniższy zrzut ekranu przedstawia wszystkie wcześniej wybrane opcje klucza.

> [!IMPORTANT]
> Katalogi, które są nazywane HANA dziennika i woluminów danych, a także ścieżki instalacji (/ hana/udostępnione w tym przykładzie) i /usr/sap, nie powinien być częścią głównego systemu plików. Te katalogi należy do dysków danych Azure, które zostały dołączone do maszyny Wirtualnej (opisany w sekcji "Dysk instalacji"). Takie podejście zapobiega głównego systemu plików wyczerpaniu miejsca. Na poniższym zrzucie ekranu widać, że administrator systemu HANA ma identyfikator użytkownika `1005` i jest częścią `sapsys` grupy (identyfikator `1001`) która została zdefiniowana przed rozpoczęciem instalacji.

![Lista wszystkich najważniejsze składniki SAP HANA wcześniej wybrany](./media/hana-get-started/image032.jpg)

Możesz sprawdzić `\<HANA SID\>adm user` (`azdadm` na poniższym zrzucie ekranu) szczegółowe informacje w katalogu/etc/haseł:

![HANA \<HANA SID\>szczegóły użytkownika adm wymienionych w katalogu/etc/haseł](./media/hana-get-started/image033.jpg)

Po zainstalowaniu SAP HANA przy użyciu HDBLCM widać struktury plików w SAP HANA Studio, jak pokazano na poniższym zrzucie ekranu. Schemat SAPABAP1, który zawiera wszystkie tabele SAP NetWeaver, nie jest jeszcze dostępna.

![Struktura pliku SAP HANA w SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po zainstalowaniu SAP HANA SAP NetWeaver można zainstalować na nim. Jak pokazano na poniższym zrzucie ekranu, instalacja została wykonana jako instalacją rozproszoną przy użyciu SWPM (zgodnie z opisem w poprzedniej sekcji). Po zainstalowaniu tego wystąpienia bazy danych przy użyciu SWPM, możesz wprowadzić te same dane przy użyciu HDBLCM (na przykład nazwy hosta, identyfikator SID HANA i liczby wystąpień). Następnie SWPM korzysta z istniejącej instalacji HANA i dodaje więcej schematów.

![Przeprowadzane przy użyciu SWPM instalacją rozproszoną](./media/hana-get-started/image035b.jpg)

Poniższy zrzut ekranu przedstawia SWPM kroku instalacji, w którym wprowadzić dane dotyczące schematu DBACOCKPIT:

![Krok instalacji SWPM, gdzie jest wprowadzana DBACOCKPIT schemat danych](./media/hana-get-started/image036b.jpg)

Wprowadź dane dotyczące schematu SAPABAP1:

![Wprowadzanie danych o schemacie SAPABAP1](./media/hana-get-started/image037b.jpg)

Po zakończeniu instalacji SWPM wystąpienie bazy danych, można wyświetlić schemat SAPABAP1 w SAP HANA Studio:

![Schemat SAPABAP1 w SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Na koniec, po ukończeniu SAP serwera aplikacji i instalacji graficznego interfejsu użytkownika programu SAP można zweryfikować wystąpienia HANA bazy danych, korzystając **Panelu sterowania DBA** transakcji:

![Wystąpienie HANA DB zweryfikowana przy użyciu Panelu sterowania DBA transakcji](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Pobieranie oprogramowania SAP
Oprogramowanie można pobrać z witryny Marketplace usługi SAP, jak pokazano na poniższych zrzutach ekranu.

Pobierz NetWeaver w wersji 7.5 dla systemu Linux/HANA:

 ![Okno SAP usługi instalacji i uaktualniania pobierania NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Pobierz wersję platformy SP12 HANA:

 ![Okno SAP usługi instalacji i uaktualniania wersji platformy SP12 HANA pobierania](./media/hana-get-started/image002.jpg)

