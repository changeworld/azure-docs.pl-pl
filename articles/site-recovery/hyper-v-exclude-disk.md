---
title: Wykluczanie dysków z replikacji w ramach odzyskiwania po awarii za pomocą Azure Site Recovery
description: Opisuje sposób wykluczania dysków maszyny wirtualnej z replikacji podczas odzyskiwania po awarii na platformie Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mayg
ms.openlocfilehash: 12304067e1a92559c2313fd7382f271249a8c784
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961452"
---
# <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji
W tym artykule opisano sposób wykluczania dysków z replikacji. Takie wykluczenie może zoptymalizować przepustowość używaną przez replikację lub zoptymalizować zasoby po stronie docelowej, z których korzystają takie dyski.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Funkcja** | **Z programu VMware do platformy Azure** | **Z funkcji Hyper-V do platformy Azure** | **Z platformy Azure do platformy Azure**| **Z funkcji Hyper-V do funkcji Hyper-V** 
--|--|--|--|--
Wykluczanie dysku | Tak | Tak | Nie | Nie

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

## <a name="how-to-exclude-disks"></a>Jak wykluczać dyski
Postępuj zgodnie z przepływem pracy [Włączanie replikacji](site-recovery-hyper-v-site-to-azure.md), aby chronić maszynę wirtualną z portalu usługi Azure Site Recovery. W czwartym kroku przepływu pracy wyklucz dyski z replikacji za pomocą kolumny **DYSK DO REPLIKACJI**. Domyślnie do replikacji są wybierane wszystkie dyski. Usuń zaznaczenie pola wyboru dysków, które chcesz wykluczyć z replikacji, a następnie wykonaj kroki w celu włączenia replikacji.

![Wykluczanie dysków z replikacji i włączanie replikacji na potrzeby powrotu po awarii z funkcji Hyper-V do platformy Azure](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * Z replikacji możesz wykluczyć tylko dyski podstawowe. Nie możesz wykluczać dysków systemu operacyjnego. Nie zalecamy wykluczania dysków dynamicznych. Usługa Azure Site Recovery nie może zidentyfikować, który wirtualny dysk twardy jest podstawowy, a który dynamiczny, w maszynie wirtualnej gościa.  Jeśli nie wszystkie zależne dyski woluminu dynamicznego zostaną wykluczone, chroniony dysk dynamiczny będzie uszkodzonym dyskiem maszyny wirtualnej w trybie failover, a dane na tym dysku będą niedostępne.
> * Po włączeniu replikacji nie możesz dodawać dysków do replikacji ani ich usuwać. Jeśli chcesz dodać lub wykluczyć dysk, musisz wyłączyć ochronę maszyny wirtualnej, a następnie włączyć ją ponownie.
> * Jeśli wykluczysz dysk wymagany do działania aplikacji, po przełączeniu w tryb failover na platformie Azure musisz utworzyć go na tej platformie ręcznie, aby można było uruchomić replikowaną aplikację. Alternatywnie możesz zintegrować usługę Azure Automation z planem odzyskiwania, aby utworzyć dysk podczas przełączania maszyny w tryb failover.
> * Dyski utworzone ręcznie na platformie Azure nie mają możliwości powrotu po awarii. Jeśli na przykład przełączysz w tryb failover trzy dyski i utworzysz dwa bezpośrednio w usłudze Azure Virtual Machines, tylko dla trzech dysków przełączonych w tryb failover nastąpi powrót po awarii z platformy Azure do funkcji Hyper-V. Dysków utworzonych ręcznie nie możesz uwzględnić podczas powrotu po awarii ani replikacji odwrotnej z funkcji Hyper-V do platformy Azure.

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
Teraz omówimy, które dyski zostaną zreplikowane po przełączeniu w tryb failover z platformy Azure do lokalnego hosta funkcji Hyper-V. Dyski utworzone ręcznie na platformie Azure nie będą replikowane. Jeśli na przykład przełączysz w tryb failover trzy dyski i utworzysz dwa bezpośrednio w usłudze Azure Virtual Machines, powrót po awarii nastąpi tylko dla trzech dysków przełączonych w tryb failover. Dysków utworzonych ręcznie nie możesz uwzględnić podczas powrotu po awarii ani ponownego włączania ochrony z zasobów lokalnych do platformy Azure. Dysk magazynu tymczasowego także nie będzie replikowany do hosta lokalnego.

### <a name="failback-to-original-location-recovery"></a>Powrót po awarii do odzyskiwania oryginalnej lokalizacji

W poprzednim przykładzie konfiguracja dysków maszyny wirtualnej platformy Azure była następująca:

**Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | ---
DYSK0 | C:\ | Dysk systemu operacyjnego
Dysk1 | E:\ | Magazyn tymczasowy<br /> <br />Platforma Azure dodaje ten dysk i przypisuje mu pierwszą dostępną literę dysku.
Dysk2 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
Dysk3 | G:\ | Baza danych użytkownika 2

Jeśli powrót po awarii jest wykonywany do oryginalnej lokalizacji, konfiguracja dysków maszyny wirtualnej powrotu po awarii pozostaje taka sama jak w przypadku oryginalnej konfiguracji dysków maszyny wirtualnej dla funkcji Hyper-V. Dyski wykluczone z replikacji z lokacji funkcji Hyper-V do platformy Azure nie będą dostępne na maszynie wirtualnej powrotu po awarii.

Dyski na maszynie wirtualnej funkcji Hyper-V (oryginalna lokalizacja) po zaplanowanym przełączeniu w tryb failover z platformy Azure do lokalnych zasobów funkcji Hyper-V:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 |   C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
DB-Disk2 (dysk wykluczony) | Dysk2 | E:\ | Pliki tymczasowe
DB-Disk3 (dysk wykluczony) | Dysk3 | F:\ | Baza danych SQL tempdb — ścieżka folderu (F:\MSSQL\Data\)
DB-Disk4 | Dysk4 | G:\ | Baza danych użytkownika 2

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

![Ustawienia pliku stronicowania na źródłowej maszynie wirtualnej](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

Po przełączeniu maszyny wirtualnej w tryb failover z funkcji Hyper-V do platformy Azure maszyna wirtualna platformy Azure będzie mieć następujące dyski:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Magazyn tymczasowy<br /> <br />pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Ponieważ Dysk1 (D:) został wykluczony, D: jest pierwszą dostępną literą dysku na liście. Platforma Azure przypisuje literę D: woluminowi magazynu tymczasowego. Ponieważ dysk D: jest dostępny na maszynie wirtualnej platformy Azure, ustawienia pliku stronicowania maszyny wirtualnej pozostają takie same.

Poniżej przedstawiono ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Przypadek 2. Plik stronicowania skonfigurowano na innym dysku (innym niż D:)

Poniżej przedstawiono konfigurację dysków źródłowej maszyny wirtualnej:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (wykluczono dysk z ochrony) | Dysk1 | G:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Poniżej przedstawiono ustawienia pliku stronicowania na lokalnej maszynie wirtualnej:

![Ustawienia pliku stronicowania na lokalnej maszynie wirtualnej](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Po przełączeniu maszyny wirtualnej w tryb failover z funkcji Hyper-V do platformy Azure maszyna wirtualna platformy Azure będzie mieć następujące dyski:

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | DYSK0  |C:\ |Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Magazyn tymczasowy<br /> <br />pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Ponieważ litera D: jest pierwszą dostępną literą dysku na liście, platforma Azure przypisuje literę D: do woluminu magazynu tymczasowego. Litery dysków wszystkich replikowanych dysków pozostają bez zmian. Ponieważ dysk G: nie jest dostępny, system użyje dysku C: na potrzeby pliku stronicowania.

Poniżej przedstawiono ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](site-recovery-failover.md) o różnych typach trybu failover.
