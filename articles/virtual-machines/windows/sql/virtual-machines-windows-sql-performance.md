---
title: Wydajność najlepsze rozwiązania dotyczące programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera najlepsze rozwiązania dla optymalizacji wydajności programu SQL Server na maszynach wirtualnych Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/19/2018
ms.author: jroth
ms.openlocfilehash: 9d3fbbab76f16a8546c431d5acf913bf419edeb4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798159"
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines

## <a name="overview"></a>Przegląd

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności programu SQL Server w maszynie wirtualnej platformy Azure firmy Microsoft. Podczas uruchamiania programu SQL Server w maszynach wirtualnych platformy Azure, zaleca się kontynuować przy użyciu tej samej bazy danych opcje dostrajania wydajności mających zastosowanie do programu SQL Server w środowisku serwera lokalnego. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej, a konfiguracja dysków z danymi.

[Obrazy SQL Server udostępnione w portalu Azure](quickstart-sql-vm-create-portal.md) postępuj zgodnie z najlepszymi rozwiązaniami konfiguracyjnymi magazynu. Aby uzyskać więcej informacji o konfiguracji magazynu, zobacz [konfiguracji magazynu dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Po zainicjowaniu obsługi administracyjnej, należy rozważyć stosowanie inne optymalizacje omówione w tym artykule. Podstawowa wybrane opcje na obciążenie i sprawdź testy.

> [!TIP]
> Ten artykuł koncentruje się na pobieranie *najlepsze* wydajności programu SQL Server na maszynach wirtualnych Azure. Jeżeli obciążenie jest mniej wymagająca, nie mogą wymagać co optymalizacji wymienionych poniżej. Twoje potrzeby w zakresie wydajności i obciążenia wzorce wziąć pod uwagę oceny tych zaleceń.

## <a name="quick-check-list"></a>Szybkie sprawdzenie listy

Oto lista szybkie sprawdzenie do uzyskania optymalnej wydajności programu SQL Server na maszynach wirtualnych platformy Azure:

| Obszar | Optymalizacje |
| --- | --- |
| [Rozmiar maszyny Wirtualnej](#vm-size-guidance) |[DS3](../sizes-general.md) lub nowszej wersji Enterprise programu SQL.<br/><br/>[DS2](../sizes-general.md) lub nowszej wersji Standard programu SQL i sieci Web. |
| [Storage](#storage-guidance) |Użyj [magazyn w warstwie Premium](../premium-storage.md). Magazynu w warstwie standardowa zaleca się tylko do programowania i testowania.<br/><br/>Zachowaj [konta magazynu](../../../storage/common/storage-create-storage-account.md) i maszyny Wirtualnej serwera SQL, w tym samym regionie.<br/><br/>Wyłącz Azure [magazynu geograficznie nadmiarowego](../../../storage/common/storage-redundancy.md) (replikacja geograficzna) na koncie magazynu. |
| [Dyski](#disks-guidance) |Użyj co najmniej 2 [dysków P30](../premium-storage.md#scalability-and-performance-targets) (1 dla plików dziennika i 1 dla danych plików i bazy danych TempDB; lub pasek dwóch lub większej liczby dysków i wszystkie pliki w jednym woluminie magazynu).<br/><br/>Unikaj używania systemu operacyjnego lub dysków tymczasowego magazynu bazy danych lub rejestrowania.<br/><br/>Włącz buforowanie odczytu na dyskach hosting plików danych i plików danych bazy danych TempDB.<br/><br/>Nie należy włączać buforowanie na dyskach hosting pliku dziennika.<br/><br/>Ważne: Zatrzymaj usługę programu SQL Server, w przypadku zmiany ustawień pamięci podręcznej dysku maszyny Wirtualnej platformy Azure.<br/><br/>Paskowych wiele dysków danych Azure, aby uzyskać większą przepływność we/wy.<br/><br/>Format z opisem rozmiarów alokacji. |
| [WE/WY](#io-guidance) |Włączanie kompresji strony bazy danych.<br/><br/>Włącz inicjowanie błyskawicznych plików dla danych plików.<br/><br/>Limit autogrowing w bazie danych.<br/><br/>Wyłącz autoshrink w bazie danych.<br/><br/>Przenieś wszystkie bazy danych do dysków z danymi, w tym systemowych baz danych.<br/><br/>Przenieś programu SQL Server błąd dziennika śledzenia pliku katalogów i dysków z danymi.<br/><br/>Ustawienia domyślne lokalizacje plików kopii zapasowej i bazy danych.<br/><br/>Włącz zablokowanych stron.<br/><br/>Zastosuj poprawki wydajności programu SQL Server. |
| [Właściwych dla funkcji](#feature-specific-guidance) |Utwórz kopię zapasową bezpośrednio do magazynu obiektów blob. |

Aby uzyskać więcej informacji na temat *jak* i *Dlaczego* aby te optymalizacje, zapoznaj się z tematem szczegółowe informacje i wskazówki zamieszczone w poniższych sekcjach.

## <a name="vm-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny Wirtualnej

W przypadku aplikacji poufnych wydajności zaleca się użycie następujących [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 lub nowszej
* **SQL Server Standard i Web Edition**: DS2 lub nowszej

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Obsługa maszyn wirtualnych: seria DS (wraz z serii DSv2 i GS-series) [magazyn w warstwie Premium](../premium-storage.md). Magazyn w warstwie Premium jest zalecana dla wszystkich obciążeń produkcyjnych.

> [!WARNING]
> Standard Storage ma różnych opóźnienia i przepustowości i jest zalecane tylko dla obciążeń i testowania. Obciążeń produkcyjnych należy używać magazyn w warstwie Premium.

Ponadto zaleca się tworzenie konta magazynu Azure w tym samym centrum danych jako maszyn wirtualnych programu SQL Server w celu zmniejszenia opóźnień transferu. Podczas tworzenia konta magazynu, należy wyłączyć — replikacja geograficzna jako kolejność spójne zapisu na wielu dyskach nie jest gwarantowana. Zamiast tego należy wziąć pod uwagę konfigurowania technologii odzyskiwania po awarii programu SQL Server, między dwiema danych Azure. Aby uzyskać więcej informacji, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Istnieją trzy typy głównego dysku na maszynie Wirtualnej platformy Azure:

* **Dysk systemu operacyjnego**: podczas tworzenia maszyny wirtualnej platformy Azure, platforma dołączą co najmniej jednego dysku (oznaczone jako **C** dysku) do maszyny Wirtualnej dla dysku systemu operacyjnego. Ten dysk jest plik VHD przechowywany jako stronicowy obiekt blob w magazynie.
* **Dysku tymczasowym**: maszyn wirtualnych platformy Azure zawiera inny dysk o nazwie dysku tymczasowym (oznaczone jako **D**: dysku). Jest to dysk na węźle, który może służyć do miejsce na pliki tymczasowe.
* **Dyski danych**: do maszyny wirtualnej można również dołączyć dodatkowych dysków, jako dyski danych i będą one przechowywane w magazynie jako stronicowych obiektów blob.

W poniższych sekcjach opisano zalecenia dotyczące używania tych dyskach.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego jest wirtualnego dysku twardego, który można rozruchu i instalacji uruchomiona wersja systemu operacyjnego i jest oznaczony jako **C** dysku.

Domyślnie buforowanie zasad na dysku systemu operacyjnego jest **odczytu/zapisu**. W przypadku aplikacji poufnych wydajności zalecane jest użycie dysków z danymi zamiast dysku systemu operacyjnego. Zobacz sekcję dotyczącą poniżej dysków z danymi.

### <a name="temporary-disk"></a>Tymczasowe dysku

Dysk magazyn tymczasowy, oznaczone jako **D**: dysk, nie są utrwalane w magazynie obiektów blob platformy Azure. Nie należy przechowywać plików bazy danych użytkownika lub plików dziennika transakcji użytkownika w **D**: dysku.

D-series, Dv2 serii i G serii maszyn wirtualnych jest tymczasowej stacji na tych maszynach wirtualnych na dyskach SSD. Jeżeli obciążenie intensywnie korzysta z bazy danych TempDB (np. obiekty tymczasowe lub złożonych sprzężeń), przechowywania bazy danych TempDB na **D** dysku może spowodować wyższej przepustowości TempDB i zmniejszyć opóźnienia bazy danych TempDB.

Dla maszyn wirtualnych, które obsługują magazyn w warstwie Premium (serii DS, DSv2 serii i serii GS) zalecamy przechowywanie bazy danych TempDB na dysku, który obsługuje magazyn w warstwie Premium z buforowaniem odczytu włączona. Istnieje jeden wyjątek do tego zalecenia; Jeśli użycie bazy danych TempDB jest intensywnie wykonujących operacje zapisu, można osiągnąć większą wydajność dzięki przechowywaniu bazy danych TempDB na lokalnym **D** dysk, który jest również na dyskach SSD na rozmiary maszyny.

### <a name="data-disks"></a>Dyski z danymi

* **Użyć dysków danych dla plików danych i dziennika**: Jeśli nie używasz rozkładanie, użyj dwóch magazyn w warstwie Premium [dysków P30](../premium-storage.md#scalability-and-performance-targets) gdzie jeden dysk zawiera pliki dziennika, a drugi zawiera dane i pliki bazy danych TempDB. Każdy dysk magazyn w warstwie Premium zawiera pewną liczbę IOPs i przepustowości (MB/s) w zależności od rozmiaru, zgodnie z opisem w artykule [przy użyciu magazyn w warstwie Premium dla dysków](../premium-storage.md). Jeśli używasz technika Rozkładanie dysku, takich jak funkcja miejsca do magazynowania, zaleca się umieścić wszystkie dane i pliki dziennika w tym samym dysku.

   > [!NOTE]
   > Podczas obsługi administracyjnej maszyny Wirtualnej programu SQL Server w portalu, istnieje możliwość edytowania konfigurację magazynu. W zależności od konfiguracji Azure umożliwia skonfigurowanie co najmniej jeden dysk. Wiele dysków są łączone w puli magazynu jednego z rozkładanie. Pliki danych i dziennika znajdują się ze sobą w tej konfiguracji. Aby uzyskać więcej informacji, zobacz [konfiguracji magazynu dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Rozkładanie**: więcej przepustowości, można dodać dodatkowego dysku z danymi i używać rozkładanie. Aby określić liczbę dysków z danymi, należy przeanalizować liczbę IOPS i przepustowość wymagana Twoje pliki dziennika i dane i pliki bazy danych TempDB. Zwróć uwagę, że różne rozmiary maszyny Wirtualnej mają różne limity liczby IOPs, jak i przepustowość obsługiwane, zobacz tabel na IOPS na [rozmiar maszyny Wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Skorzystaj z poniższych wskazówek:

  * Dla systemu Windows 8/Windows Server 2012 lub nowszym, użyj [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wytycznymi:

      1. Ustaw przeplotu (rozmiar przeplotu) do 64 KB (65536 bajtów) dla obciążeń OLTP i 256 KB (262 144 bajty) dla obciążeń magazynowania danych, aby uniknąć wpływu na wydajność ze względu na niezgodność partycji. To musi być ustawiona przy użyciu programu PowerShell.
      1. Ustaw liczbę kolumn = Liczba dysków fizycznych. W przypadku konfigurowania więcej niż 8 dysków (nie interfejsu użytkownika Menedżera serwera) za pomocą programu PowerShell. 

    Na przykład następujące programu PowerShell jest tworzona nowa pula magazynu o rozmiarze przeplotu do 64 KB i liczba kolumn do 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Dla systemu Windows 2008 R2 lub starszym można używać dysków dynamicznych (woluminy rozłożone systemu operacyjnego) oraz rozmiar przeplotu zawsze wynosi 64 KB. Należy pamiętać, że ta opcja jest przestarzały począwszy od systemu Windows 8/Windows Server 2012. Aby uzyskać informacje, zobacz oświadczenie pomocy technicznej w [usługi dysków wirtualnych są przenoszone do interfejsu API zarządzania magazynami systemu Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Jeśli używasz [bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) ze scenariuszem, takich jak [wystąpienia klastra trybu Failover programu SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), należy skonfigurować jednej puli. Należy pamiętać, że chociaż różnych woluminach można utworzyć w tej właśnie puli, jednego, będą wszystkie miały te same parametry, takie jak zasad buforowania w tym samym.

  * Określ liczbę dysków skojarzone z puli magazynów oparte na Twoich oczekiwań obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych pozwala różne liczby dysków dołączonych danych. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Jeśli nie używasz magazyn w warstwie Premium (wszystkie scenariusze tworzenia/testowania), zalecane jest dodanie maksymalna liczba dysków danych obsługiwane przez użytkownika [rozmiar maszyny Wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użyj rozkładanie.

* **Buforowanie zasad**: należy zwrócić uwagę na poniższe zalecenia dotyczące buforowanie zasad w zależności od konfiguracji magazynu.

  * Jeśli korzystasz z osobnych dyskach plików danych i dziennika, należy włączyć odczytu buforowanie na dyskach danych hosting plików danych i plików danych bazy danych TempDB. Może to spowodować wydajności znaczących korzyści. Nie należy włączać buforowanie na dysku przechowywania plików dziennika, ponieważ powoduje to niewielkie obniżenie wydajności.

  * Jeśli używasz rozkładanie większości obciążeń będą korzystać z buforowaniem odczytu. Ze względu na bardziej wydajne z rozkładanie nawet wtedy, gdy plik dziennika znajduje się na tym samym dysku ma zastosowanie tego zalecenia. W niektórych obciążeń duże zapisu lepszą wydajność może zostać osiągnięty przy bez buforowania. To można określić tylko testy.

  * Powyższe zalecenia dotyczą dysków Premium Storage. Jeśli nie używasz magazyn w warstwie Premium, nie należy włączać buforowania na wszelkich dyskach danych.

  * Aby uzyskać instrukcje dotyczące konfiguracji pamięci podręcznej dysku zobacz następujące artykuły. Dla klasycznego modelu wdrażania (ASM): [AzureOSDisk zestaw](https://msdn.microsoft.com/library/azure/jj152847) i [AzureDataDisk zestawu](https://msdn.microsoft.com/library/azure/jj152851.aspx). Dla modelu wdrażania usługi Azure Resource Manager: [AzureRMOSDisk zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) i [AzureRMVMDataDisk zestawu](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

     > [!WARNING]
     > Zatrzymaj usługę programu SQL Server, zmieniając ustawienie pamięci podręcznej dysków maszyny Wirtualnej platformy Azure, aby uniknąć ewentualne uszkodzenia bazy danych.

* **Rozmiar jednostki alokacji systemu plików NTFS**: podczas formatowania dysku danych, zaleca się użyć rozmiar jednostki alokacji 64 KB danych i plików dziennika, jak również w bazie danych TempDB.

* **Najlepsze rozwiązania dotyczące zarządzania dysku**: podczas usuwania dysku danych lub zmianę jej typu pamięci podręcznej, Zatrzymaj usługę programu SQL Server podczas zmiany. Po zmianie ustawienia buforowania na dysku systemu operacyjnego, Azure zatrzymuje maszynę Wirtualną, zmienia typ pamięci podręcznej i ponownym uruchomieniu maszyny Wirtualnej. Zmiana ustawień pamięci podręcznej dysku danych maszyny Wirtualnej nie został on zatrzymany, ale dysk danych jest odłączona od maszyny Wirtualnej podczas zmiany, a następnie ponownie nałożona.

  > [!WARNING]
  > Nie można zatrzymać usługi programu SQL Server podczas tych czynności może spowodować uszkodzenie bazy danych.

## <a name="io-guidance"></a>Wskazówki dotyczące operacji We/Wy

* Najbardziej efektywne magazyn w warstwie Premium są uzyskuje, gdy parallelize żądań i aplikacji. Magazyn w warstwie Premium jest przeznaczony do scenariuszy, których głębokość kolejki we/wy jest większa niż 1, dlatego niewielkiego lub żadnego wzrost wydajności jednowątkowe serial żądań (nawet jeśli są one znacznym magazynu). Na przykład to może mieć wpływ na wyniki testu jednowątkowe narzędzia analizy wydajności, takich jak SQLIO.

* Należy rozważyć użycie [bazy danych kompresji strony](https://msdn.microsoft.com/library/cc280449.aspx) jak może pomóc zwiększyć wydajność obciążeń intensywnie korzystających z operacji We/Wy. Jednak kompresji danych może zwiększyć użycie Procesora na serwerze bazy danych.

* Rozważ włączenie inicjowanie błyskawicznych plików w celu skrócenia czasu wymaganego do przydzielenia pierwszy plik. Przeprowadzać inicjowanie błyskawicznych plików, przyznawanie kontu usługi SQL Server (MSSQLSERVER) z SE_MANAGE_VOLUME_NAME i dodaj go do **wykonywać zadania konserwacji woluminów** zasady zabezpieczeń. Jeśli używasz obrazu platformy programu SQL Server na platformie Azure, domyślnego konta usługi (NT Service\MSSQLSERVER) nie jest dodawany do **wykonywać zadania konserwacji woluminów** zasady zabezpieczeń. Innymi słowy inicjowanie błyskawicznych plików nie jest włączona w obrazie platformy Azure serwera SQL. Po dodaniu konta usługi programu SQL Server do **wykonywać zadania konserwacji woluminów** zasady zabezpieczeń, uruchom ponownie usługę SQL Server. Może to być zagadnienia dotyczące zabezpieczeń dla tej funkcji. Aby uzyskać więcej informacji, zobacz [Inicjowanie plików bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).

* **Automatyczne zwiększanie** uważa się jedynie awaryjnych nieoczekiwany wzrost. Nie zarządzania rozwojem danych i dziennika na podstawie bieżącego z automatycznego przyrostu. Jeśli używane jest automatyczne zwiększanie, wstępnie rosnąć za pomocą przełącznika rozmiaru pliku.

* Upewnij się, że **autoshrink** jest wyłączona, aby uniknąć niepotrzebnych zadań, które mogą negatywnie wpłynąć na wydajność.

* Przenieś wszystkie bazy danych do dysków z danymi, w tym systemowych baz danych. Aby uzyskać więcej informacji, zobacz [przenoszenia baz danych systemu](https://msdn.microsoft.com/library/ms345408.aspx).

* Przenieś programu SQL Server błąd dziennika śledzenia pliku katalogów i dysków z danymi. Można to zrobić w programie SQL Server Configuration Manager przez kliknięcie prawym przyciskiem myszy wystąpienie programu SQL Server i wybierz opcję Właściwości. Ustawienia pliku dziennika i śledzenia błędów można zmienić w **Parametry uruchamiania** kartę. Katalog zrzutu jest określona w **zaawansowane** kartę. Poniższy zrzut ekranu przedstawia miejsce wyszukiwania parametr uruchamiania dziennika błędów.

    ![Zrzut ekranu dziennik błędów programu SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Ustawienia domyślne lokalizacje plików kopii zapasowej i bazy danych. Użyj zaleceń w tym artykule i wprowadź zmiany w oknie właściwości serwera. Aby uzyskać instrukcje, zobacz [wyświetlić lub zmienić domyślne lokalizacje dla danych i plików dziennika (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie do wprowadzenia tych zmian.

    ![Pliki dziennika danych SQL i tworzenie kopii zapasowych](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Włącz zablokowanych stron we/wy i żadnych działań stronicowania. Aby uzyskać więcej informacji, zobacz [włączyć blokowania stron w pamięci (system Windows) — opcja](https://msdn.microsoft.com/library/ms190730.aspx).

* Jeśli używasz programu SQL Server 2012, należy zainstalować Service Pack 1 aktualizacją zbiorczą 10. Ta aktualizacja zawiera poprawkę pogorszenie wydajności na We/Wy podczas wykonywania wybierz w instrukcji tabeli tymczasowej w programie SQL Server 2012. Aby uzyskać informacje, zobacz [artykułu bazy wiedzy](http://support.microsoft.com/kb/2958012).

* Należy rozważyć kompresowania wszystkie pliki danych podczas przesyłania We/Wy systemu Azure.

## <a name="feature-specific-guidance"></a>Wskazówki dotyczące funkcji

Niektóre wdrożenia może osiągnięcia korzyści dodatkowe wydajności za pomocą bardziej zaawansowanych technik konfiguracji. Poniższa lista zawiera opis niektóre funkcje programu SQL Server, które mogą pomóc osiągnąć większą wydajność:

* **Wykonywanie kopii zapasowej na magazyn Azure**: podczas wykonywania kopii zapasowych programu SQL Server uruchomionego na maszynach wirtualnych Azure, można użyć [kopii zapasowej serwera SQL do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Ta funkcja jest dostępnych w programie SQL Server 2012 z dodatkiem SP1 CU2 i zalecane w przypadku tworzenie kopii zapasowych na dyskach danych dołączonych. Gdy możesz wykonywania kopii zapasowej/przywracania do/z magazynu Azure, wykonaj zalecenia zawarte w [serwera kopii zapasowej SQL do adresu URL najlepsze rozwiązania i rozwiązywanie problemów i przywracanie z kopii zapasowych przechowywanych w magazynie Azure](https://msdn.microsoft.com/library/jj919149.aspx). Można również zautomatyzować te kopie zapasowe przy użyciu [automatyczna usługa Backup dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

    Starszych niż SQL Server 2012, można użyć [kopii zapasowej serwera SQL do narzędzia Azure](https://www.microsoft.com/download/details.aspx?id=40740). To narzędzie może pomóc zwiększyć przepływność kopii zapasowej za pomocą wielu cele stripe kopii zapasowej.

* **Pliki danych programu SQL Server na platformie Azure**: Ta nowa funkcja [plików danych programu SQL Server na platformie Azure](https://msdn.microsoft.com/library/dn385720.aspx), jest dostępnych w programie SQL Server 2014. Programem SQL Server z plików danych na platformie Azure przedstawiono charakterystyki wydajności można porównywać pod względem jako za pomocą dysków danych Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać najlepsze rozwiązania w zakresie zabezpieczeń, zobacz [zagadnienia dotyczące zabezpieczeń dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Przejrzyj inne artykuły maszyny wirtualnej programu SQL Server na [programu SQL Server na omówienia maszyn wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).
