---
title: Wykluczanie dysków z replikacji za pomocą usługi Azure Site Recovery
description: Jak wykluczyć dyski z replikacji na platformę Azure za pomocą usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281849"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Wykluczanie dysków z odzyskiwania po awarii

W tym artykule opisano sposób wykluczania dysków z replikacji podczas odzyskiwania po awarii z lokalnego na platformę Azure za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md) Dyski można wykluczyć z replikacji z kilku powodów:

- Tak, że nieważne dane ubijane na wykluczonym dysku nie jest replikowany.
- Aby zoptymalizować przepustowość używanej replikacji lub zasoby po stronie docelowej.
- Aby zapisać zasoby magazynu i sieci, nie replikując danych, które nie są potrzebne.
- Maszyny wirtualne platformy Azure osiągnęły limity replikacji usługi Site Recovery.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Można wykluczyć dyski z replikacji, jak podsumowano w tabeli.

**Azure–Azure** | **Z programu VMware do platformy Azure** | **Z funkcji Hyper-V do platformy Azure** 
--- | --- | ---
Tak (przy użyciu programu PowerShell) | Tak | Tak 

## <a name="exclude-limitations"></a>Wykluczanie ograniczeń

**Ograniczenia** | **Maszyny wirtualne platformy Azure** | **Maszyny wirtualne VMware** | **Maszyny wirtualne funkcji Hyper-V**
--- | --- | ---
**Typy dysków** | Dyski podstawowe można wykluczyć z replikacji.<br/><br/> Nie można wykluczyć dysków systemu operacyjnego ani dysków dynamicznych. Dyski tymczasowe są domyślnie wykluczone. | Dyski podstawowe można wykluczyć z replikacji.<br/><br/> Nie można wykluczyć dysków systemu operacyjnego ani dysków dynamicznych. | Dyski podstawowe można wykluczyć z replikacji.<br/><br/> Nie możesz wykluczać dysków systemu operacyjnego. Nie zalecamy wykluczania dysków dynamicznych. Usługa Site Recovery nie może zidentyfikować, który program VHS jest podstawowy lub dynamiczny w maszynie wirtualnej gościa. Jeśli wszystkie zależne dyski woluminów dynamicznych nie są wykluczone, chroniony dysk dynamiczny staje się dyskiem awaryjnym na maszynie wirtualnej trybu failover, a dane na tym dysku nie są dostępne.
**Replikowanie dysku** | Nie można wykluczyć dysku, który jest replikowanie.<br/><br/> Wyłączanie i ponowne przydzielanie do replikacji dla maszyny Wirtualnej. |  Nie można wykluczyć dysku, który jest replikowanie. |  Nie można wykluczyć dysku, który jest replikowanie.
**Usługa mobilności (VMware)** | Nie dotyczy | Dyski można wykluczyć tylko na maszynach wirtualnych z zainstalowaną usługą mobilności.<br/><br/> Oznacza to, że należy ręcznie zainstalować usługę mobilności na maszynach wirtualnych, dla których chcesz wykluczyć dyski. Nie można użyć mechanizmu instalacji wypychanej, ponieważ usługa mobilności jest instalowana dopiero po włączeniu replikacji. | Nie istotne.
**Dodaj/Usuń** | Dyski można dodawać i usuwać na maszynach wirtualnych platformy Azure za pomocą dysków zarządzanych. | Nie można dodawać ani usuwać dysków po włączeniu replikacji. Wyłącz, a następnie ponownie można replikacji, aby dodać dysk. | Nie można dodawać ani usuwać dysków po włączeniu replikacji. Wyłącz, a następnie ponownie zmietrzyj replikację.
**Tryb failover** | Jeśli aplikacja potrzebuje dysku, który został wykluczony, po przełączeniu w tryb failover należy ręcznie utworzyć dysk, aby można było uruchomić zreplikowaną aplikację.<br/><br/> Alternatywnie można utworzyć dysk podczas pracy awaryjnej maszyny Wirtualnej, integrując automatyzację platformy Azure z planem odzyskiwania. | Jeśli wykluczysz dysk, który aplikacja potrzebuje, utwórz go ręcznie na platformie Azure po przełączeniu w tryb failover. | Jeśli wykluczysz dysk, który aplikacja potrzebuje, utwórz go ręcznie na platformie Azure po przełączeniu w tryb failover.
**Lokalne dyski po awarii utworzone ręcznie** | Nie dotyczy | **Maszyny wirtualne z systemem Windows:** Dyski utworzone ręcznie na platformie Azure nie są powiodą się z powrotem. Na przykład jeśli w pracy awaryjnej na trzech dyskach i utworzyć dwa dyski bezpośrednio na maszynie Wirtualnej platformy Azure, tylko trzy dyski, które zostały przejęte awaryjnie są następnie po awarii z powrotem.<br/><br/> **Maszyny wirtualne z systemem Linux:** dyski utworzone ręcznie na platformie Azure są przywracane po awarii. Na przykład jeśli po awarii trzy dyski i utworzyć dwa dyski na maszynie Wirtualnej platformy Azure, wszystkie pięć zostanie po awarii z powrotem. Dysków utworzonych ręcznie nie możesz wykluczyć z powrotu po awarii. | Dyski utworzone ręcznie na platformie Azure nie są powiodą się z powrotem. Na przykład jeśli po awarii trzy dyski i utworzyć dwa dyski bezpośrednio na maszynie Wirtualnej platformy Azure, tylko trzy dyski, które zostały przejęte awaryjnie zostaną po awarii z powrotem.
**Dyski wykluczone po awarii w środowisku lokalnym** | Nie dotyczy | Jeśli powrót do oryginalnego komputera nie powiedzie się, konfiguracja dysku maszyny wirtualnej po awarii nie zawiera wykluczonych dysków. Dyski, które zostały wykluczone z replikacji VMware do platformy Azure nie są dostępne na maszynie wirtualnej po awarii. | Po powięksłu jest do oryginalnej lokalizacji funkcji Hyper-V, konfiguracji dysku maszyny wirtualnej po awarii pozostaje taka sama jak oryginalnego źródłowego dysku maszyny Wirtualnej. Dyski, które zostały wykluczone z witryny funkcji Hyper-V do replikacji platformy Azure są dostępne na maszynie wirtualnej po awarii.



## <a name="typical-scenarios"></a>Typowe scenariusze

Przykłady zmian danych, które są doskonałymi kandydatami do wykluczenia obejmują zapisy do pliku stronicowania (pagefile.sys) i zapisuje do pliku tempdb programu Microsoft SQL Server. W zależności od obciążenia i podsystemu magazynu pliki stronicowania i tempdb można zarejestrować znaczną ilość zmian. Replikowanie tego typu danych na platformie Azure jest intensywnie korzystające z zasobów.

- Aby zoptymalizować replikację dla maszyny Wirtualnej za pomocą jednego dysku wirtualnego, który zawiera zarówno system operacyjny, jak i plik stronicowania, można:
    1. Podziel pojedynczy dysk wirtualny na dwa dyski wirtualne. Na jednym dysku wirtualnym umieść system operacyjny, a na drugim — plik stronicowania.
    2. Wyklucz dysk z plikiem stronicowania z replikacji.

- Aby zoptymalizować replikację dla dysku zawierającego zarówno plik tempdb programu Microsoft SQL Server, jak i plik systemowej bazy danych, można:
    1. Umieść systemową bazę danych i bazę danych tempdb na dwóch różnych dyskach.
    2. Wyklucz dysk z bazą danych tempdb z replikacji.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Przykład 1. Wykluczanie dysku bazy danych tempdb programu SQL Server

Przyjrzyjmy się, jak obsługiwać wykluczenie dysku, praca awaryjna i praca awaryjna dla źródłowej maszyny Wirtualnej systemu Windows programu SQL Server — **SalesDB***, dla której chcemy wykluczyć tempdb. 

### <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji

Mamy te dyski na źródłowej stronie windows VM SalesDB.

**Nazwa dysku** | **Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Dysk0 | C:\ | Dysk systemu operacyjnego.
DB-Disk1| Dysk1 | D:\ | Baza danych systemu SQL i baza danych użytkowników1.
DB-Disk2 (wykluczono dysk z ochrony) | Dysk2 | E:\ | Pliki tymczasowe.
DB-Disk3 (wykluczono dysk z ochrony) | Dysk3 | F:\ | Baza danych tempdb SQL.<br/><br/> Ścieżka folderu - F:\MSSQL\Data\. Zanotuj ścieżkę folderu przed przejściem w stan failover.
DB-Disk4 | Dysk4 |G:\ | Baza danych użytkownika 2

1. Umożliwiamy replikację maszyny Wirtualnej SalesDB.
2. Wykluczamy z replikacji dyski Disk2 i Disk3, ponieważ zmiany danych na tych dyskach są tymczasowe. 


### <a name="handle-disks-during-failover"></a>Obsługa dysków podczas pracy awaryjnej

Ponieważ dyski nie są replikowane, po przejściu w stan failover do platformy Azure te dyski nie są obecne na maszynie Wirtualnej platformy Azure utworzonej po przełączeniu w pracę awaryjną. Maszyna wirtualna platformy Azure ma dyski podsumowane w tej tabeli.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Dysk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | E:\ | Przechowywanie tymczasowe<br/><br/>Platforma Azure dodaje ten dysk. Ponieważ dyski Disk2 i Disk3 zostały wykluczone z replikacji, E: jest pierwszą literą dysku z dostępnej listy. Platforma Azure przypisuje literę E: woluminowi magazynu tymczasowego. Inne litery dysków dla dysków replikowanych pozostają takie same.
Dysk2 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
Dysk3 | G:\ | Baza danych użytkownika 2

W naszym przykładzie, ponieważ Disk3, dysk tempdb SQL, został wykluczony z replikacji i nie jest dostępny na maszynie Wirtualnej platformy Azure, usługa SQL jest w stanie zatrzymania i potrzebuje F:\MSSQL\Data path. Tę ścieżkę można utworzyć na kilka sposobów: 

- Dodaj nowy dysk po przemiń awaryjnych i przypisz ścieżkę folderu tempdb.
- Użyj istniejącego dysku magazynu tymczasowego na potrzeby ścieżki folderu bazy danych tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Dodawanie nowego dysku po przemijadaniu awaryjnym

1. Przed przełączeniem w tryb failover zanotuj ścieżki plików bazy danych SQL tempdb.mdf i tempdb.ldf.
2. W witrynie Azure portal dodaj nowy dysk do maszyny wirtualnej platformy Azure trybu failover. Dysk powinien mieć ten sam rozmiar (lub większy) co źródłowy dysk tempdb SQL (Disk3).
3. Zaloguj się do maszyny Wirtualnej platformy Azure.
4. Z poziomu konsoli zarządzania dyskami (diskmgmt.msc) zainicjuj i sformatuj nowo dodany dysk.
5. Przypisz tę samą literę dysku, która była używana przez dysk tempdb SQL (F:)
6. Utwórz folder bazy danych tempdb na woluminie F: (F:\MSSQL\Data).
7. Uruchom usługę SQL z poziomu konsoli usługi.

#### <a name="use-an-existing-temporary-storage-disk"></a>Używanie istniejącego tymczasowego dysku magazynu 

1. Otwórz wiersz polecenia.
2. Z poziomu wiersza polecenia uruchom program SQL Server w trybie odzyskiwania.

        Net start MSSQLSERVER /f / T3608

3. Uruchom następujące polecenie sqlcmd, aby zmienić ścieżkę bazy danych tempdb na nową ścieżkę.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Zatrzymaj usługę programu Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Uruchom usługę programu Microsoft SQL Server.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Maszyny wirtualne VMware: dyski podczas powrotu po awarii do pierwotnej lokalizacji

Teraz zobaczmy, jak obsługiwać dyski na maszynach wirtualnych VMware, gdy powrót po awarii do oryginalnej lokalizacji lokalnej.

- **Dyski utworzone na platformie Azure:** Ponieważ w naszym przykładzie użyto maszyny Wirtualnej systemu Windows, dyski tworzone ręcznie na platformie Azure nie są replikowane z powrotem do witryny po awarii lub ponownej kontroli maszyny Wirtualnej.
- Tymczasowy dysk magazynu na **platformie Azure:** tymczasowy dysk magazynu nie jest replikowany z powrotem do hostów lokalnych.
- **Wykluczone dyski:** dyski, które zostały wykluczone z replikacji VMware do platformy Azure, nie są dostępne na lokalnej maszynie wirtualnej po powiększenie po awarii.

Przed pozycją powrotu maszyn wirtualnych VMware do oryginalnej lokalizacji, ustawienia dysku maszyny Wirtualnej platformy Azure są następujące.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Dysk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | E:\ | Magazyn tymczasowy.
Dysk2 | D:\ | Baza danych systemu SQL i baza danych użytkowników1.
Dysk3 | G:\ | Baza danych użytkowników2.

Po 3-10 07.000.After failback, the VMware VM w the original location has the disks summarized in the table.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Dysk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | D:\ | Baza danych systemu SQL i baza danych użytkowników1.
Dysk2 | G:\ | Baza danych użytkowników2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Maszyny wirtualne funkcji Hyper-V: dyski podczas powrotu po awarii do pierwotnej lokalizacji

Teraz zobaczmy, jak obsługiwać dyski na maszynach wirtualnych funkcji Hyper-V, gdy powrót po awarii do oryginalnej lokalizacji lokalnej.

- **Dyski utworzone na platformie Azure:** dyski tworzone ręcznie na platformie Azure nie są replikowane z powrotem do witryny po awarii lub ponownego przeróbki maszyny Wirtualnej.
- Tymczasowy dysk magazynu na **platformie Azure:** tymczasowy dysk magazynu nie jest replikowany z powrotem do hostów lokalnych.
- **Wykluczone dyski:** Po powięksłu powiększanie konfiguracji dysku maszyny Wirtualnej jest taka sama jak oryginalna konfiguracja dysku maszyny Wirtualnej. Dyski, które zostały wykluczone z replikacji z funkcji Hyper-V na platformę Azure są dostępne na maszynie wirtualnej po awarii.

Przed pozycją powrotu maszyn wirtualnych funkcji Hyper-V do oryginalnej lokalizacji ustawienia dysku maszyny Wirtualnej platformy Azure są następujące.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Dysk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | E:\ | Magazyn tymczasowy.
Dysk2 | D:\ | Baza danych systemu SQL i baza danych użytkowników1.
Dysk3 | G:\ | Baza danych użytkowników2.

Po planowanym pracy awaryjnej (po awarii) z platformy Azure do lokalnego hyper-V, hyper-V maszyny Wirtualnej w oryginalnej lokalizacji ma dyski podsumowane w tabeli.

**Nazwa dysku** | **Numer dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
 --- | --- | --- | ---
DB-Disk0-OS | Dysk0 |   C:\ | Dysk systemu operacyjnego.
DB-Disk1 | Dysk1 | D:\ | Baza danych systemu SQL i baza danych użytkowników1.
DB-Disk2 (dysk wykluczony) | Dysk2 | E:\ | Pliki tymczasowe.
DB-Disk3 (dysk wykluczony) | Dysk3 | F:\ | Baza danych tempdb SQL<br/><br/> Ścieżka folderu (F:\MSSQL\Data\).
DB-Disk4 | Dysk4 | G:\ | Baza danych użytkownika 2


## <a name="example-2-exclude-the-paging-file-disk"></a>Przykład 2: Wykluczanie dysku pliku stronicowania

Przyjrzyjmy się, jak obsługiwać wykluczenie dysku, tryb failover i pracy awaryjnej dla źródłowej maszyny Wirtualnej systemu Windows, dla których chcemy wykluczyć pagefile.sys dysku pliku zarówno na dysku D, jak i na dysku alternatywnym.


### <a name="paging-file-on-the-d-drive"></a>Plik stronicowania na dysku D

Mamy te dyski na źródłowej maszynie wirtualnej.

**Nazwa dysku** | **Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Dysk0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (Wyklucz z replikacji) | Dysk1 | D:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na źródłowej maszynie wirtualnej są następujące:

![Ustawienia pliku stronicowania na źródłowej maszynie wirtualnej](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Umożliwiamy replikację dla maszyny Wirtualnej.
2. Wykluczamy DB-Disk1 z replikacji.

#### <a name="disks-after-failover"></a>Dyski po przemijaniu awaryjnym

Po przeliczeniu awaryjnym maszyna wirtualna platformy Azure ma dyski podsumowane w tabeli.

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | Dysk0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Przechowywanie tymczasowe/plik pagefile.sys <br/><br/> Ponieważ DB-Disk1 (D:) został wykluczony,D: jest pierwszą literą dysku z dostępnej listy.<br/><br/> Platforma Azure przypisuje literę D: woluminowi magazynu tymczasowego.<br/><br/> Ponieważ D: jest dostępna, ustawienie pliku stronicowania maszyny Wirtualnej pozostaje takie samo).
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na maszynie Wirtualnej platformy Azure są następujące:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Plik stronicowania na innym dysku (nie D:)

Spójrzmy na przykład, w którym plik stronicowania nie jest na dysku D.  

Mamy te dyski na źródłowej maszynie wirtualnej.

**Nazwa dysku** | **Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Dysk0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (Wyklucz z replikacji) | Dysk1 | G:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na lokalnej maszynie wirtualnej są następujące:

![Ustawienia pliku stronicowania na lokalnej maszynie wirtualnej](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Umożliwiamy replikację dla maszyny Wirtualnej.
2. Wykluczamy DB-Disk1 z replikacji.

#### <a name="disks-after-failover"></a>Dyski po przemijaniu awaryjnym

Po przeliczeniu awaryjnym maszyna wirtualna platformy Azure ma dyski podsumowane w tabeli.

**Nazwa dysku** | **Numer dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Dysk0  |C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Przechowywanie tymczasowe<br/><br/> Ponieważ litera D: jest pierwszą dostępną literą dysku na liście, platforma Azure przypisuje literę D: do woluminu magazynu tymczasowego.<br/><br/> Litery dysków wszystkich replikowanych dysków pozostają bez zmian.<br/><br/> Ponieważ dysk G: nie jest dostępny, system użyje dysku C: dla pliku stronicowania.
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na maszynie Wirtualnej platformy Azure są następujące:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wskazówkach dotyczących tymczasowego dysku magazynu:
    - [Dowiedz się więcej o](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) używaniu dysków SSD na maszynach wirtualnych platformy Azure do przechowywania rozszerzeń bazy danych tempdb i buforów sql server
    - [Przejrzyj](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) najlepsze rozwiązania dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure.
- Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](failover-failback-overview.md) o różnych typach trybu failover.

