---
title: Wytyczne dotyczące wydajności dla programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera wskazówki dotyczące optymalizacji wydajności programu SQL Server na maszynach wirtualnych Azure firmy Microsoft.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3fda34e46ddb7ea17c98795ad6632841b79764eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076911"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Wytyczne dotyczące wydajności dla programu SQL Server na maszynach wirtualnych platformy Azure

## <a name="overview"></a>Omówienie

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności programu SQL Server na maszynie wirtualnej platformy Azure firmy Microsoft. Podczas uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, firma Microsoft zaleca, będziesz nadal korzystać z tej samej bazy danych opcje dostrajania wydajności, które mają zastosowanie do programu SQL Server w środowisku serwera w środowisku lokalnym. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej i konfiguracja dysków z danymi.

[Obrazy programu SQL Server aprowizowane w witrynie Azure portal](quickstart-sql-vm-create-portal.md) postępuj zgodnie z najlepszymi rozwiązaniami konfiguracyjnymi magazynu ogólnego (Aby uzyskać więcej informacji na temat konfiguracji magazynu, zobacz [konfigurację magazynu dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Po zainicjowaniu obsługi administracyjnej, należy rozważyć stosowanie inne optymalizacje omówionych w tym artykule. Podstawowa wybrane opcje na obciążeniu, a następnie sprawdź za pomocą testowania.

> [!TIP]
> Zazwyczaj jest kompromis między optymalizacji kosztów i optymalizacji wydajności. Ten artykuł koncentruje się na wprowadzenie *najlepsze* wydajności dla programu SQL Server na maszynach wirtualnych platformy Azure. Jeżeli obciążenie jest mniej wymagających, może nie wymagać co optymalizacji wymienione poniżej. Twoje potrzeby związane z wydajnością, koszty i wzorce obciążenia wziąć pod uwagę oceny tych zaleceń.

## <a name="quick-check-list"></a>Szybkie sprawdzenie listy

Poniżej znajduje się lista szybkie sprawdzenie w celu uzyskania optymalnej wydajności programu SQL Server na maszynach wirtualnych platformy Azure:

| Obszar | Optymalizacje |
| --- | --- |
| [Rozmiar maszyny wirtualnej](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) lub nowsze dla programu SQL Enterprise edition.<br/><br/> - [DS2_v2](../sizes-general.md) lub nowszej wersji programu SQL Standard i sieci Web. |
| [Storage](#storage-guidance) | -Użyj [premium SSD](../disks-types.md). Magazynu w warstwie standardowa jest zalecane tylko na potrzeby tworzenia i testowania.<br/><br/> -Zachować [konta magazynu](../../../storage/common/storage-create-storage-account.md) i maszyn wirtualnych serwera SQL, w tym samym regionie.<br/><br/> * Wyłączenie Azure [magazyn geograficznie nadmiarowy](../../../storage/common/storage-redundancy.md) (replikacja geograficzna) na koncie magazynu. |
| [Dyski](#disks-guidance) | -Użyj co najmniej 2 [dyski P30](../disks-types.md#premium-ssd) (1 dla plików dziennika i 1 dla plików danych, w tym bazy danych TempDB). Obciążenia wymagające ~ 50 000 operacji We/Wy Rozważ użycie Ultra dyski SSD. <br/><br/> -Należy unikać systemu operacyjnego oraz dyski tymczasowe do przechowywania bazy danych lub rejestrowania.<br/><br/> -Włącz odczytu, buforowanie na dyskach hostowania plików danych i plików danych bazy danych TempDB.<br/><br/> -Nie włącza buforowanie na dyskach hostingu w pliku dziennika.  **Ważne**: Zatrzymaj usługi programu SQL Server, gdy zmiana ustawień pamięci podręcznej dysku maszyny Wirtualnej platformy Azure.<br/><br/> -Stripe wielu dysków z danymi platformy Azure można pobrać zwiększać przepustowość operacji We/Wy.<br/><br/> — Format z udokumentowane rozmiarów alokacji. <br/><br/> -Miejsce bazy danych TempDB na lokalny dysk SSD dla obciążeń o znaczeniu krytycznym programu SQL Server (po wybraniu właściwego rozmiaru maszyny Wirtualnej). |
| [OPERACJE WE/WY](#io-guidance) |-Włącz kompresji strony bazy danych.<br/><br/> -Włącz inicjowanie błyskawiczne plików dla danych plików.<br/><br/> — Ograniczenia wzrostu bazy danych.<br/><br/> -Wyłącz zmniejszania bazy danych.<br/><br/> -Przenieść wszystkie bazy danych na dyskach danych, tym systemowych baz danych.<br/><br/> -Przenieś programu SQL Server błąd dziennika śledzenia pliku katalogów i dysków z danymi.<br/><br/> — Skonfiguruj domyślne lokalizacje plików bazy danych i kopii zapasowych.<br/><br/> -Włącz zablokowanych stron.<br/><br/> -Zastosowania poprawki wydajności programu SQL Server. |
| [Specyficzne dla funkcji](#feature-specific-guidance) | — Wykonaj kopię zapasową, bezpośrednio do magazynu obiektów blob. |

Aby uzyskać więcej informacji na temat *jak* i *Dlaczego* Aby wprowadzić te optymalizacje, można znaleźć szczegółowe informacje i wskazówki można znaleźć w następujących sekcjach.

## <a name="vm-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny Wirtualnej

W przypadku aplikacji poufnych wydajności zalecane jest, należy używać następującej [rozmiarów maszyn wirtualnych](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 lub nowszej
* **Wersje programu SQL Server Standard i Web**: DS2_v2 lub nowszej

[Seria DSv2](../sizes-general.md#dsv2-series) maszyny wirtualne obsługuje usługi premium storage, co jest zalecane w celu uzyskania najlepszej wydajności. Rozmiary zalecane Oto linii bazowych, ale rozmiar rzeczywisty maszyny, którą wybierzesz zależy od wymagań obciążenia. Maszyny wirtualne z serii DSv2 są ogólnego przeznaczenia maszyn wirtualnych, które są odpowiednie dla różnych obciążeń, natomiast inne rozmiary maszyn są zoptymalizowane dla typów określonego obciążenia. Na przykład [serii M](../sizes-memory.md#m-series) oferuje największą liczbę procesorów wirtualnych i ilość pamięci w przypadku największych obciążeń programu SQL Server. [Serii GS](../sizes-previous-gen.md#gs-series) i [DSv2 serii 11-15](../sizes-memory.md#dsv2-series-11-15) są zoptymalizowane pod kątem wymagań dużej ilości pamięci. Oba te serie są również dostępne w [ograniczone rozmiary core](../../windows/constrained-vcpu.md), co pozwala na zaoszczędzenie pieniędzy dla obciążeń o małych obliczenia zapotrzebowania. [Serii Ls](../sizes-storage.md) maszyn są zoptymalizowane dla Wysoka przepływność dysku i we/wy. Należy wziąć pod uwagę określonego obciążenia programu SQL Server i zastosować ją do wybranych serię maszyn wirtualnych i rozmiar.

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Obsługa maszyn wirtualnych z serii DS (wraz z serii DSv2 i GS-) [premium SSD](../disks-types.md). Dyski SSD w warstwie Premium są zalecane w przypadku wszystkich obciążeń produkcyjnych.

> [!WARNING]
> Standardowa HDD i SSD mają różne wartości opóźnienia i przepustowości i są zalecane tylko na potrzeby obciążeń deweloperskich/testowych. Obciążeń produkcyjnych należy używać dysków SSD w warstwie premium.

Ponadto firma Microsoft zaleca tworzenie konta usługi Azure storage w tym samym centrum danych jako maszyn wirtualnych programu SQL Server w celu zmniejszenia opóźnień transferu. Podczas tworzenia konta magazynu, należy wyłączyć replikację geograficzną, ponieważ nie jest gwarantowana kolejność zapisu spójne na wielu dyskach. Zamiast tego należy wziąć pod uwagę konfigurowania technologii odzyskiwania po awarii programu SQL Server między centrami danych platformy Azure dwa. Aby uzyskać więcej informacji, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Istnieją trzy typy głównego dysku na Maszynie wirtualnej platformy Azure:

* **Dysk systemu operacyjnego**: Gdy tworzysz maszynę wirtualną platformy Azure, platformy będzie dołączyć co najmniej jeden dysk (oznaczone jako **C** dysku) do maszyny Wirtualnej dla dysku systemu operacyjnego. Ten dysk jest dyskiem VHD przechowywanym jako stronicowy obiekt blob w magazynie.
* **Dysk tymczasowy**: Maszyny wirtualne platformy Azure zawiera inny dysk o nazwie dysku tymczasowego (oznaczone jako **D**: dysk). Jest to dysk na węźle, który może służyć do miejsca na pliki tymczasowe.
* **Dyski z danymi**: Można również dołączyć dodatkowe dyski do maszyny wirtualnej, jako dysków z danymi, a te będą przechowywane w magazynie jako stronicowe obiekty BLOB.

W poniższych sekcjach opisano zalecenia dotyczące używania tych różnych dyskach.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego jest wirtualny dysk twardy, który można uruchomić i zainstalować jako uruchomionej wersji systemu operacyjnego i jest oznaczony jako **C** dysku.

Domyślnie zasady na dysku systemu operacyjnego buforowania jest **odczytu/zapisu**. W przypadku aplikacji poufnych wydajności zalecamy korzystanie z dysków danych zamiast dysku systemu operacyjnego. Zobacz sekcję dotyczącą poniżej dysków z danymi.

### <a name="temporary-disk"></a>Dysk tymczasowy

Dysk magazynu tymczasowego, oznaczone jako **D**: dysku, nie są utrwalane w magazynie obiektów blob platformy Azure. Nie należy przechowywać plików bazy danych użytkownika i pliki dziennika transakcji użytkownika na **D**: dysk.

Seria D, zalecamy używanie serii Dv2 i maszyny wirtualne z serii G dysku tymczasowego na tych maszynach wirtualnych jest oparty na dyskach SSD. Jeśli obciążenie powoduje, że intensywne użycie bazy danych TempDB (np. obiektów tymczasowych lub złożonych sprzężeń) przechowywania bazy danych TempDB na **D** dysku może spowodować większej przepływności bazy danych TempDB i zmniejszyć czas oczekiwania bazy danych TempDB. Przykładowy scenariusz Zobacz Omówienie bazy danych TempDB w następującym wpisie: [Wskazówki dotyczące konfigurowania magazynu dla programu SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

W przypadku maszyn wirtualnych, które obsługują dyski premium SSD (seria DS, seria DSv2 i GS-series), zaleca się przechowywanie bazy danych TempDB na dysku, który obsługuje dyski SSD w warstwie premium z buforowaniem odczytu włączone.

Istnieje jeden wyjątek od tej rekomendacji: _Jeśli użycie bazy danych TempDB jest intensywnie korzystających z zapisu, można uzyskać lepszą wydajność dzięki przechowywaniu bazy danych TempDB na lokalnym **D** dysk, który jest również, dysk SSD — na podstawie tych rozmiarów maszyn._

### <a name="data-disks"></a>Dyski z danymi

* **Korzystanie z dysków danych dla plików danych i dziennika**: Jeśli nie używasz rozkładanie, należy użyć dwóch dysków SSD P30 premium gdzie jeden dysk zawiera pliki dziennika, a drugi zawiera dane i pliki bazy danych TempDB. Każdego premium SSD oferuje pewną liczbę operacji We/Wy i przepustowość (MB/s) w zależności od rozmiaru, jak pokazano w artykule [wybierz typ dysku](../disks-types.md). Jeśli używasz technika Rozkładanie dysku, takich jak miejsca do magazynowania, można osiągnąć optymalną wydajność przez dwie pule, jeden dla pliku lub plików dziennika i inne pliki danych. Jednak jeśli użytkownik chce użyć wystąpienia klastra trybu Failover (FCI) programu SQL Server, należy skonfigurować jedną pulę.

   > [!TIP]
   > - Dla wyników testów w różnych konfiguracjach dysku i obciążenia zobacz następujący wpis w blogu: [Wskazówki dotyczące konfigurowania magazynu dla programu SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Dla misji wydajności dla serwerów SQL, które wymagają ~ 50 000 operacji We/Wy, rozważ zastąpienie 10 - dyski P30 przy użyciu najwyższej dyski SSD. Aby uzyskać więcej informacji zobacz następujący wpis w blogu: [O znaczeniu strategicznym, krytyczne wydajność dzięki najwyższej SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Podczas aprowizowania maszyny Wirtualnej z programu SQL Server w portalu, istnieje możliwość edytowania konfigurację magazynu. W zależności od konfiguracji platforma Azure konfiguruje co najmniej jeden dysk. Wiele dysków są łączone w pulę magazynu jednego z rozkładanie. Pliki danych i dziennika znajdują się ze sobą w tej konfiguracji. Aby uzyskać więcej informacji, zobacz [konfigurację magazynu dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Rozkładanie**: Większą przepustowość można dodać dodatkowego dysku z danymi i używać Rozkładanie dysku. Aby określić liczbę dysków z danymi, należy przeanalizować liczbę operacji We/Wy i przepustowości wymaganej dla Twojego pliku lub plików dziennika i Twoje dane i pliki bazy danych TempDB. Należy zauważyć, że różne rozmiary maszyn wirtualnych mają różne limity liczby operacji We/Wy i przepustowości obsługiwane, zobacz tabele na operacje We/Wy na [rozmiar maszyny Wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Skorzystaj z poniższych wskazówek:

  * W przypadku systemu Windows 8/Windows Server 2012 lub nowszym, użyj [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wytycznymi:

      1. Ustaw przeplotu (rozmiar woluminu rozłożonego) do 64 KB (65536 bajtów) dla obciążeń OLTP i 256 KB (262 144 bajty) dla obciążeń magazynowania danych, aby uniknąć wpływu na wydajność ze względu na niezgodność partycji. Należy to określić za pomocą programu PowerShell.
      2. Ustaw liczbę kolumn = Liczba dysków fizycznych. Podczas konfigurowania więcej niż 8 dysków (nie interfejs użytkownika Menedżera serwera) przy użyciu programu PowerShell. 

    Na przykład następujące polecenie programu PowerShell tworzy nową pulę magazynów z rozmiarem przeplotu 64 KB i liczbę kolumn do 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * W przypadku systemu Windows 2008 R2 lub starszym można używać dysków dynamicznych (woluminy rozłożone systemu operacyjnego) i rozmiar woluminu rozłożonego zawsze wynosi 64 KB. Należy pamiętać, że ta opcja jest przestarzały począwszy od systemu Windows 8/Windows Server 2012. Informacji znajduje się w instrukcji pomocy technicznej w [usługi dysków wirtualnych są przenoszone do interfejsu API zarządzania magazynami systemu Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Jeśli używasz [bezpośrednimi miejscami do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) z [wystąpienia klastra trybu Failover programu SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), należy skonfigurować jedną pulę. Należy pamiętać, że chociaż różnych woluminach można utworzyć w tym jednej puli, wszystkie współużytkują one te same właściwości, takie jak te same zasady buforowania.

  * Określ liczbę dysków skojarzonych z puli magazynów, w oparciu o Twoim oczekiwaniom obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych pozwalają różne liczby dołączonych dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Jeśli nie używasz premium SSD (scenariusze tworzenia i testowania), zalecane jest dodanie maksymalna liczba dysków danych obsługiwanych przez usługi [rozmiar maszyny Wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użyj Rozkładanie dysku.

* **Zasady buforowania**: Należy zwrócić uwagę na poniższe zalecenia dotyczące zasad, w zależności od konfiguracji magazynu buforowania.

  * W przypadku korzystania z oddzielnych dysków dla plików danych i dziennika Włącz buforowanie odczytu na dyskach danych, hostowanie plików danych i plików danych bazy danych TempDB. Może to spowodować, że korzyści istotnie poprawiającą wydajność. Nie włączaj pamięci podręcznej na dysku, przechowujący plik dziennika, ponieważ powoduje to pomocnicza spadek wydajności.

  * Jeśli używasz Rozkładanie dysku w puli magazynu z jednej, większości obciążeń będą mogli korzystać z pamięci podręcznej odczytu. Jeśli masz pule magazynów oddzielnych plików dziennika i danych, Włącz buforowanie odczytu tylko w puli magazynu dla plików danych. W niektórych obciążeń wysokie obciążenia podczas zapisu może osiągnąć lepszą wydajność przy użyciu nie buforowania. To tylko można określić za pomocą testowania.

  * Powyższe zalecenia dotyczą dysków SSD w warstwie premium. Jeśli nie używasz dysków SSD w warstwie premium, nie należy włączać wszelkie buforowanie na wszelkich dysków z danymi.

  * Aby uzyskać instrukcje na temat konfigurowania buforowania dysku zobacz następujące artykuły. Klasyczny (ASM) można znaleźć modelu wdrażania przy użyciu: [Zestaw AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) i [AzureDataDisk zestaw](https://msdn.microsoft.com/library/azure/jj152851.aspx). Wdrożenia usługi Azure Resource Manager zawiera temat modelu: [Zestaw AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) i [AzVMDataDisk zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Zatrzymaj usługę programu SQL Server, zmieniając ustawienia pamięci podręcznej dysków maszyny Wirtualnej platformy Azure, aby uniknąć możliwości ewentualne uszkodzenia bazy danych.

* **Rozmiar jednostki alokacji systemu plików NTFS**: Podczas formatowania dysku danych, zaleca się użycie rozmiaru jednostki alokacji 64 KB dla plików danych i dziennika, a także bazy danych TempDB.

* **Najlepsze rozwiązania dotyczące zarządzania dysku**: Podczas usuwania dysku z danymi lub zmianę jego typu pamięci podręcznej, należy zatrzymać usługę programu SQL Server podczas zmiany. Zmiana ustawień pamięci podręcznej na dysku systemu operacyjnego Azure zatrzyma maszynę Wirtualną, zmieni typ pamięci podręcznej i ponowne uruchomienie maszyny Wirtualnej. Zmiana ustawień pamięci podręcznej dysku danych maszyny Wirtualnej nie zostanie zatrzymana, ale dysk z danymi jest odłączona od maszyny Wirtualnej podczas zmiany, a następnie ponownie dołączyć.

  > [!WARNING]
  > Nie można zatrzymać usługi programu SQL Server podczas wykonywania tych operacji może spowodować uszkodzenie bazy danych.


## <a name="io-guidance"></a>Wskazówki dotyczące operacji We/Wy

* Najlepsze wyniki z dyskami SSD w warstwie premium są się uzyskuje, gdy zrównoleglić żądań i aplikacji. Dyski SSD w warstwie Premium są przeznaczone dla scenariuszy, w których głębokość kolejki operacji We/Wy jest większa niż 1, więc zobaczysz niewielkiego lub żadnego wzrost wydajności w jednowątkowym serial żądania (nawet jeśli są one o znacznym wykorzystaniu pamięci masowej). Na przykład to może mieć wpływ na wyniki badań jednowątkowe narzędzi do analizy wydajności, takich jak SQLIO.

* Należy rozważyć użycie [bazy danych kompresji strony](https://msdn.microsoft.com/library/cc280449.aspx) ponieważ może to pomóc zwiększyć wydajność obciążeń z intensywnym wykorzystaniem we/wy. Kompresja danych może jednak zwiększyć użycie procesora CPU na serwerze bazy danych.

* Rozważ włączenie inicjowanie błyskawiczne plików skrócić czas wymagany do przydzielania pierwszy plik. Można skorzystać z pliku natychmiastowe inicjowanie, przyznawanie kontu usługi programu SQL Server (MSSQLSERVER) przy użyciu SE_MANAGE_VOLUME_NAME i dodać go do **wykonywania zadań konserwacji woluminów** zasady zabezpieczeń. Jeśli używasz obrazu platformy programu SQL Server na platformie Azure, domyślne konto usługi (NT Service\MSSQLSERVER) nie jest dodawany do **wykonywania zadań konserwacji woluminów** zasady zabezpieczeń. Innymi słowy inicjowanie błyskawiczne plików nie jest włączona w obrazie platformy programu SQL Server Azure. Po dodaniu konta usługi programu SQL Server, aby **wykonywania zadań konserwacji woluminów** zasady zabezpieczeń, uruchom ponownie usługę programu SQL Server. Może to być zagadnienia dotyczące zabezpieczeń dla tej funkcji. Aby uzyskać więcej informacji, zobacz [Inicjowanie plików bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).

* **Automatyczne zwiększanie** jest uważany za jedynie rozwiązanie awaryjne na wypadek nieoczekiwanego zwiększania. Nie zarządzać wzrostem ilości danych i dziennika na podstawie codziennych zwiększania. Jeśli jest używana opcja automatycznego zwiększania, wstępnie Rozwijaj go przy użyciu przełącznika rozmiar.

* Upewnij się, że **pewnego** jest wyłączona, aby uniknąć niepotrzebnych nakładów pracy, który może negatywnie wpłynąć na wydajność.

* Przenieś wszystkie bazy danych do dysków danych, w tym systemowych baz danych. Aby uzyskać więcej informacji, zobacz [Przenieś systemowych baz danych](https://msdn.microsoft.com/library/ms345408.aspx).

* Przenieś programu SQL Server błąd dziennika śledzenia pliku katalogów i dysków z danymi. Można to zrobić w programie SQL Server Configuration Manager kliknij prawym przyciskiem myszy wystąpienie programu SQL Server i wybierając pozycję Właściwości. Można zmienić ustawienia pliku dziennika i śledzenia błędów w programie **Parametry uruchamiania** kartę. Katalog zrzutu jest określona w **zaawansowane** kartę. Poniższy zrzut ekranu przedstawia gdzie szukać parametru uruchamiania dziennika błędów.

    ![Zrzut ekranu w dzienniku błędów programu SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Skonfiguruj domyślne lokalizacje plików bazy danych i kopii zapasowych. Użyj zaleceń w tym artykule, a następnie wprowadź zmiany w oknie dialogowym właściwości serwera. Aby uzyskać instrukcje, zobacz [wyświetlić lub zmienić domyślne lokalizacje dla danych i plików dziennika (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie do wprowadzenia tych zmian.

    ![Pliki dziennika danych SQL i tworzenie kopii zapasowych](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Włącz zablokowanych stron, które mają zmniejszyć We/Wy i każde działanie stronicowania. Aby uzyskać więcej informacji, zobacz [Włącz blokowanie stron w pamięci (Windows) — opcja](https://msdn.microsoft.com/library/ms190730.aspx).

* Jeśli używasz programu SQL Server 2012, należy zainstalować Service Pack 1 aktualizacją zbiorczą 10. Ta aktualizacja zawiera poprawki niską wydajność operacji We/Wy podczas wykonywania select w tabeli tymczasowej instrukcji w programie SQL Server 2012. Aby uzyskać informacje, zobacz [artykuł bazy wiedzy](https://support.microsoft.com/kb/2958012).

* Należy wziąć pod uwagę kompresowanie wszystkich plików danych podczas przesyłania wchodzącym/wychodzącym platformy Azure.

## <a name="feature-specific-guidance"></a>Ze wskazówek specyficznych dla funkcji

Niektóre wdrożenia mogą osiągać korzyści wyższą wydajność przy użyciu bardziej zaawansowanych technik konfiguracji. Poniższa lista wyróżnione niektóre funkcje programu SQL Server, które mogą pomóc Ci osiągnąć większą wydajność:

### <a name="backup-to-azure-storage"></a>Kopia zapasowa w usłudze Azure Storage
Podczas wykonywania kopii zapasowych programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure, możesz użyć [kopię zapasową serwera SQL do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Ta funkcja jest dostępna, począwszy od pakietu CU2 programu SQL Server 2012 z dodatkiem SP1 i zalecane w przypadku wykonywania kopii zapasowych dołączonych dysków z danymi. Po użytkownik/przywracania kopii zapasowej do/z usługi Azure storage, postępuj zgodnie z zaleceniami, przy zachowaniu [SQL Server kopii zapasowej do adresu URL najlepszych rozwiązań i rozwiązywanie problemów i przywracanie z kopii zapasowych przechowywanych w usłudze Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Możesz też zautomatyzować te kopie zapasowe przy użyciu [automatyczne kopie zapasowe dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

Starszych niż SQL Server 2012, można użyć [kopię zapasową serwera SQL do narzędzia Azure](https://www.microsoft.com/download/details.aspx?id=40740). To narzędzie może pomóc w celu zwiększenia przepływności kopii zapasowej przy użyciu wielu celów tworzenia kopii zapasowej usługi stripe.

### <a name="sql-server-data-files-in-azure"></a>Pliki danych programu SQL Server na platformie Azure

Ta nowa funkcja [pliki danych programu SQL Server na platformie Azure](https://msdn.microsoft.com/library/dn385720.aspx), jest dostępna, począwszy od programu SQL Server 2014. Uruchomiony program SQL Server przy użyciu plików danych na platformie Azure pokazuje charakterystyki wydajności porównywalnej jako korzystają z dysków danych na platformie Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Wystąpienia klastra trybu failover i miejsca do magazynowania

Jeśli używasz funkcji miejsca do magazynowania, podczas dodawania węzłów do klastra na **potwierdzenie** strony, usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**. 

![Usuń zaznaczenie pola wyboru odpowiednie magazyny](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Jeśli używasz funkcji miejsca do magazynowania, a nie Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**, Windows powoduje odłączenie dysków wirtualnych podczas procesu klastrowania. W rezultacie nie są wyświetlane w Menedżerze dysków lub Eksploratorze aż miejsca do magazynowania są usuwane z klastra i ponownie dołączyć przy użyciu programu PowerShell. Miejsca do magazynowania grupuje wielu dysków w pule magazynu. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat magazynu i wydajności, zobacz [wskazówki dotyczące konfigurowania magazynu dla programu SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Najlepsze rozwiązania dotyczące zabezpieczeń, zobacz [zagadnienia dotyczące zabezpieczeń dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Przejrzyj inne artykuły dotyczące maszyny wirtualnej programu SQL Server, na [programu SQL Server w usłudze Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).
