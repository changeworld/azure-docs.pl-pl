---
title: Wykluczanie dysków maszyny wirtualnej VMware z odzyskiwania po awarii na platformę Azure za pomocą Azure Site Recovery
description: Opisuje, dlaczego i jak wykluczać dyski maszyny wirtualnej z replikacji na potrzeby odzyskiwania po awarii oprogramowania VMware na platformę Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.date: 3/3/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c003620420611f3416e6481c575f987fbd1bd05f
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622387"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Wykluczanie dysków z replikacji maszyny wirtualnej VMware do platformy Azure

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych VMware na platformę Azure. Takie wykluczenie może zoptymalizować przepustowość używaną przez replikację lub zoptymalizować zasoby po stronie docelowej, z których korzystają takie dyski. Jeśli potrzebujesz informacji na temat wykluczania dysków dla funkcji Hyper-V, zapoznaj się z [tym artykułem](hyper-v-exclude-disk.md)


## <a name="prerequisites"></a>Wymagania wstępne

Domyślnie wszystkie dyski na maszynie są replikowane. Aby wykluczyć dysk z replikacji, w przypadku replikacji z programu VMware do platformy Azure musisz ręcznie zainstalować usługę Mobility na maszynie przed włączeniem replikacji.


## <a name="why-exclude-disks-from-replication"></a>Dlaczego wykluczać dyski z replikacji?
Wykluczenie dysków z replikacji jest często konieczne, ponieważ:

- Dane modyfikowane na wykluczonym dysku nie są istotne lub nie ma konieczności ich replikacji.

- Chcesz oszczędzić zasoby magazynu i zasoby sieciowe, pomijając replikowanie tych zmian.

## <a name="what-are-the-typical-scenarios"></a>Jak wyglądają typowe scenariusze?
Możliwe jest wskazanie konkretnych przykładów zmian danych, które świetnie nadają się do wykluczenia. Są to na przykład dane zapisywane w pliku stronicowania (pagefile.sys) i w pliku tempdb programu Microsoft SQL Server. W zależności od obciążenia i podsystemu magazynu w pliku stronicowania może być wykonywana znacząca ilość zmian. Jednak replikowanie tych danych z lokacji głównej do platformy Azure wymagałoby dużej ilości zasobów. W związku z tym za pomocą następujących kroków możesz zoptymalizować replikację maszyny wirtualnej z pojedynczym dyskiem wirtualnym, na którym znajduje się zarówno system operacyjny, jak i plik stronicowania:

1. Podziel pojedynczy dysk wirtualny na dwa dyski wirtualne. Na jednym dysku wirtualnym umieść system operacyjny, a na drugim — plik stronicowania.
2. Wyklucz dysk z plikiem stronicowania z replikacji.

Podobnie za pomocą następujących kroków możesz zoptymalizować dysk zawierający zarówno plik tempdb programu Microsoft SQL Server, jak i plik systemowej bazy danych:

1. Umieść systemową bazę danych i bazę danych tempdb na dwóch różnych dyskach.
2. Wyklucz dysk z bazą danych tempdb z replikacji.

## <a name="how-to-exclude-disks-from-replication"></a>Jak wykluczać dyski z replikacji?

Postępuj zgodnie z przepływem pracy [Włączanie replikacji](vmware-azure-enable-replication.md), aby chronić maszynę wirtualną z portalu usługi Azure Site Recovery. W czwartym kroku przepływu pracy wyklucz dyski z replikacji za pomocą kolumny **DYSK DO REPLIKACJI**. Domyślnie do replikacji są wybierane wszystkie dyski. Usuń zaznaczenie pola wyboru dysków, które chcesz wykluczyć z replikacji, a następnie wykonaj kroki w celu włączenia replikacji.

![Wykluczanie dysków z replikacji i włączanie replikacji na potrzeby powrotu po awarii z programu VMware do platformy Azure](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)


>[!NOTE]
>
> * Na maszynach wirtualnych, na których jest już zainstalowana usługa mobilności, można wykluczać tylko dyski. Należy ręcznie zainstalować usługę mobilności, ponieważ jest ona instalowana tylko przy użyciu mechanizmu wypychania po włączeniu replikacji.
> * Tylko dyski podstawowe można wyłączyć z replikacji. Nie możesz wykluczać dysków systemu operacyjnego ani dysków dynamicznych.
> * Po włączeniu replikacji nie możesz dodawać dysków do replikacji ani ich usuwać. Jeśli chcesz dodać lub wykluczyć dysk, musisz wyłączyć ochronę maszyny, a następnie włączyć ją ponownie.
> * Jeśli wykluczysz dysk wymagany do działania aplikacji, po przełączeniu w tryb failover na platformie Azure musisz utworzyć go na tej platformie ręcznie, aby można było uruchomić replikowaną aplikację. Alternatywnie możesz zintegrować usługę Azure Automation z planem odzyskiwania, aby utworzyć dysk podczas przełączania maszyny w tryb failover.
> * Maszyna wirtualna z systemem Windows: Dyski utworzone ręcznie na platformie Azure nie mają możliwości powrotu po awarii. Jeśli na przykład przełączysz w tryb failover trzy dyski i utworzysz dwa bezpośrednio w usłudze Azure Virtual Machines, powrót po awarii nastąpi tylko dla trzech dysków przełączonych w tryb failover. Dysków utworzonych ręcznie nie możesz uwzględnić podczas powrotu po awarii ani ponownego włączania ochrony z zasobów lokalnych do platformy Azure.
> * Maszyna wirtualna z systemem Linux: Dyski utworzone ręcznie na platformie Azure są uwzględniane podczas powrotu po awarii. Jeśli na przykład przełączysz w tryb failover trzy dyski i utworzysz dwa bezpośrednio w usłudze Azure Virtual Machines, powrót po awarii nastąpi dla wszystkich pięciu dysków. Dysków utworzonych ręcznie nie możesz wykluczyć z powrotu po awarii.
>


## <a name="end-to-end-scenarios-of-exclude-disks"></a>Kompleksowe scenariusze wykluczania dysków
Rozważmy dwa scenariusze, aby zrozumieć funkcję wykluczania dysku:

- Dysk bazy danych tempdb programu SQL Server
- Dysk pliku stronicowania (pagefile.sys)

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Przykład 1. Wykluczanie dysku bazy danych tempdb programu SQL Server
Rozważmy maszynę wirtualną programu SQL Server z bazą danych tempdb, którą można wykluczyć.

Nazwa dysku wirtualnego to SalesDB.

Na źródłowej maszynie wirtualnej są następujące dyski:


**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 | C:\ | Dysk systemu operacyjnego
DB-Disk1| Dysk1 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
DB-Disk2 (wykluczono dysk z ochrony) | Dysk2 | E:\ | Pliki tymczasowe
DB-Disk3 (wykluczono dysk z ochrony) | Dysk3 | F:\ | Baza danych SQL tempdb — ścieżka folderu (F:\MSSQL\Data\) <br /> <br />Zapisz ścieżkę folderu przed przejściem w tryb failover.
DB-Disk4 | Dysk4 |G:\ |Baza danych użytkownika 2

Ponieważ zmiany danych na dwóch dyskach maszyny wirtualnej dotyczą danych tymczasowych, podczas włączania ochrony maszyny wirtualnej bazy danych SalesDB, wyklucz dyski Dysk2 i Dysk3 z replikacji. Usługa Azure Site Recovery nie będzie replikować tych dysków. Po przełączeniu w tryb failover te dyski nie będą istnieć na maszynie wirtualnej w trybie failover na platformie Azure.

Dyski na maszynie wirtualnej platformy Azure po przełączeniu w tryb failover będą następujące:

**Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | ---
DYSK0 | C:\ | Dysk systemu operacyjnego
Dysk1 | E:\ | Magazyn tymczasowy<br /> <br />Platforma Azure dodaje ten dysk i przypisuje mu pierwszą dostępną literę dysku.
Dysk2 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
Dysk3 | G:\ | Baza danych użytkownika 2

Ponieważ dyski Dysk2 i Dysk3 zostały wykluczone z maszyny wirtualnej bazy danych SalesDB, E: jest pierwszą dostępną literą dysku. Platforma Azure przypisuje literę E: woluminowi magazynu tymczasowego. Litery dysków wszystkich replikowanych dysków pozostają bez zmian.

Dysk3, czyli dysk bazy danych SQL tempdb (ścieżka folderu tempdb F:\MSSQL\Data\), został wykluczony z replikacji. Dysk nie jest dostępny na maszynie wirtualnej w trybie failover. W efekcie usługa SQL przechodzi w stan zatrzymania i potrzebuje ścieżki F:\MSSQL\Data.

Istnieją dwa sposoby utworzenia tej ścieżki:

- Dodaj nowy dysk i przypisz ścieżkę folderu bazy danych tempdb.
- Użyj istniejącego dysku magazynu tymczasowego na potrzeby ścieżki folderu bazy danych tempdb.

### <a name="add-a-new-disk"></a>Dodaj nowy dysk:

1. Przed przełączeniem w tryb failover zanotuj ścieżki plików bazy danych SQL tempdb.mdf i tempdb.ldf.
2. Z poziomu witryny Azure Portal dodaj nowy dysk do maszyny wirtualnej w trybie failover o takim samym lub większym rozmiarze jak źródłowy dysk bazy danych SQL tempdb (Dysk3).
3. Zaloguj się do maszyny wirtualnej platformy Azure. Z poziomu konsoli zarządzania dyskami (diskmgmt.msc) zainicjuj i sformatuj nowo dodany dysk.
4. Przypisz tę samą literę dysku, która była przypisana do dysku bazy danych SQL tempdb (F:).
5. Utwórz folder bazy danych tempdb na woluminie F: (F:\MSSQL\Data).
6. Uruchom usługę SQL z poziomu konsoli usługi.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>Użyj istniejącego dysku magazynu tymczasowego na potrzeby ścieżki folderu bazy danych SQL tempdb:

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

Zapoznaj się z następującymi wskazówkami dla platformy Azure dotyczącymi dysku magazynu tymczasowego:

* [Przechowywanie bazy danych TempDB programu SQL Server i rozszerzeń puli buforów przy użyciu dysków SSD na platformie Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>Powrót po awarii (z platformy Azure do hosta lokalnego)
Teraz omówimy, które dyski zostaną zreplikowane po przełączeniu w tryb failover z platformy Azure do lokalnego programu VMware. Dyski utworzone ręcznie na platformie Azure nie będą replikowane. Jeśli na przykład przełączysz w tryb failover trzy dyski i utworzysz dwa bezpośrednio w usłudze Azure Virtual Machines, powrót po awarii nastąpi tylko dla trzech dysków przełączonych w tryb failover. Dysków utworzonych ręcznie nie możesz uwzględnić podczas powrotu po awarii ani ponownego włączania ochrony z zasobów lokalnych do platformy Azure. Dysk magazynu tymczasowego także nie będzie replikowany do hosta lokalnego.

### <a name="failback-to-original-location-recovery"></a>Powrót po awarii do odzyskiwania oryginalnej lokalizacji

W poprzednim przykładzie konfiguracja dysków maszyny wirtualnej platformy Azure była następująca:

**Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | ---
DYSK0 | C:\ | Dysk systemu operacyjnego
Dysk1 | E:\ | Magazyn tymczasowy<br /> <br />Platforma Azure dodaje ten dysk i przypisuje mu pierwszą dostępną literę dysku.
Dysk2 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
Dysk3 | G:\ | Baza danych użytkownika 2

Po powrocie po awarii do oryginalnej lokalizacji konfiguracja dysków maszyny wirtualnej powrotu po awarii nie zawiera wykluczonych dysków. Dyski wykluczone z replikacji z programu VMware do platformy Azure nie będą dostępne na maszynie wirtualnej powrotu po awarii.

Dyski na maszynie wirtualnej programu VMware (oryginalna lokalizacja) po zaplanowanym powrocie po awarii z platformy Azure do lokalnych zasobów programu VMware:

**Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | ---
DYSK0 | C:\ | Dysk systemu operacyjnego
Dysk1 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
Dysk2 | G:\ | Baza danych użytkownika 2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>Przykład 2. Wykluczanie dysku pliku stronicowania (pagefile.sys)

Rozważmy maszynę wirtualną zawierającą dysk z plikiem stronicowania, który można wykluczyć.
Są dwa przypadki.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>Przypadek 1. Plik stronicowania skonfigurowano na dysku D:
Konfiguracja dysków:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (wykluczono dysk z ochrony) | Dysk1 | D:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Poniżej przedstawiono ustawienia pliku stronicowania na źródłowej maszynie wirtualnej:

![Ustawienia pliku stronicowania na źródłowej maszynie wirtualnej](./media/vmware-azure-exclude-disk/pagefile-on-d-drive-sourcevm.png)


Po przełączeniu maszyny wirtualnej w tryb failover z programu VMware do platformy Azure maszyna wirtualna platformy Azure będzie mieć następujące dyski:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Magazyn tymczasowy<br /> <br />pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Ponieważ Dysk1 (D:) został wykluczony, D: jest pierwszą dostępną literą dysku na liście. Platforma Azure przypisuje literę D: woluminowi magazynu tymczasowego. Ponieważ dysk D: jest dostępny na maszynie wirtualnej platformy Azure, ustawienia pliku stronicowania maszyny wirtualnej pozostają takie same.

Poniżej przedstawiono ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/vmware-azure-exclude-disk/pagefile-on-azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Przypadek 2. Plik stronicowania skonfigurowano na innym dysku (innym niż D:)

Poniżej przedstawiono konfigurację dysków źródłowej maszyny wirtualnej:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (wykluczono dysk z ochrony) | Dysk1 | G:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Poniżej przedstawiono ustawienia pliku stronicowania na lokalnej maszynie wirtualnej:

![Ustawienia pliku stronicowania na lokalnej maszynie wirtualnej](./media/vmware-azure-exclude-disk/pagefile-on-g-drive-sourcevm.png)

Po przełączeniu maszyny wirtualnej w tryb failover z programu VMware do platformy Azure maszyna wirtualna platformy Azure będzie mieć następujące dyski:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0  |C:\ |Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Magazyn tymczasowy<br /> <br />pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Ponieważ litera D: jest pierwszą dostępną literą dysku na liście, platforma Azure przypisuje literę D: do woluminu magazynu tymczasowego. Litery dysków wszystkich replikowanych dysków pozostają bez zmian. Ponieważ dysk G: nie jest dostępny, system użyje dysku C: na potrzeby pliku stronicowania.

Poniżej przedstawiono ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/vmware-azure-exclude-disk/pagefile-on-azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](site-recovery-failover.md) o różnych typach trybu failover.
