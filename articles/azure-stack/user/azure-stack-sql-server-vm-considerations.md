---
title: Wydajność najlepsze rozwiązania dotyczące programu SQL Server w maszynach wirtualnych platformy Azure stosu
description: Zawiera najlepsze rozwiązania dla optymalizacji wydajności programu SQL Server w programie Microsoft Azure stosu maszyn wirtualnych.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701387"
---
# <a name="optimize-sql-server-performance"></a>Optymalizacja wydajności programu SQL Server

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności programu SQL Server na maszynach wirtualnych Microsoft Azure stosu. Podczas uruchamiania programu SQL Server na maszynach wirtualnych Azure stosu, opcje tej samej bazy danych dostrajania wydajności mające zastosowanie do programu SQL Server w środowisku serwera lokalnego. Wydajność relacyjnej bazy danych w chmurze Azure stosu zależy od wielu czynników. Czynniki obejmują rodziny rozmiar maszyny wirtualnej, a konfiguracja dysków z danymi.

Podczas tworzenia obrazów programu SQL Server [należy wziąć pod uwagę inicjowania obsługi administracyjnej maszyn wirtualnych w portalu Azure stosu](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Pobierz rozszerzenie IaaS SQL z witryny Marketplace zarządzania w portalu administracyjnym stosu Azure i Pobierz wybór SQL maszyny wirtualnej twardych dysków wirtualnych (VHD). Obejmują one SQL2014SP2, SQL2016SP1 i SQL2017.

> [!NOTE]  
> Gdy artykuł opisuje sposób aprowizowanie maszyny wirtualnej programu SQL Server przy użyciu portalu Azure globalnych, wskazówki dotyczą również stosu Azure z następującymi różnicami: SSD nie jest dostępna dla dysku systemu operacyjnego, zarządzanych dyski nie są dostępne, i występują niewielkie różnice w konfiguracji magazynu.

Pobieranie *najlepsze* wydajności programu SQL Server na maszynach wirtualnych Azure stosu jest fokus w tym artykule. Jeżeli obciążenie jest mniej wymagająca, nie mogą wymagać co zalecane optymalizacji. Twoje potrzeby w zakresie wydajności i obciążenia wzorce wziąć pod uwagę oceny tych zaleceń.

> [!NOTE]  
> Wytyczne dotyczące wydajności programu SQL Server na maszynach wirtualnych Azure, można znaleźć w temacie [w tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Przed rozpoczęciem
Poniższa lista kontrolna jest do uzyskania optymalnej wydajności programu SQL Server na maszynach wirtualnych Azure stosu:


|Obszar|Optymalizacje|
|-----|-----|
|Rozmiar maszyny wirtualnej |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) lub wyższą, aby program SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) lub wyższą, aby program SQL Server Standard edition i Web edition.|
|Magazyn |Użyj rodziny maszyny wirtualnej, która obsługuje [magazyn w warstwie Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Dyski |Użyj co najmniej dwóch dysków (jeden dla plików dziennika) i jeden dla pliku danych i bazy danych TempDB, a następnie wybierz rozmiar dysku, na podstawie Twoich potrzeb pojemności. Ustaw domyślne lokalizacje pliku danych tych dysków podczas instalacji programu SQL Server.<br><br>Unikaj używania systemu operacyjnego lub dysków tymczasowego magazynu bazy danych lub rejestrowania.<br>Paskowych wiele dysków danych Azure, aby uzyskać większą przepływność We/Wy przy użyciu funkcji miejsca do magazynowania.<br><br>Format z opisem rozmiarów alokacji.|
|WE/WY|Włącz inicjowanie błyskawicznych plików dla danych plików.<br><br>Ogranicz automatycznego przyrostu w bazach danych o rozsądnych niewielkie wartości stałych (64 MB - 256 MB).<br><br>Wyłącz autoshrink w bazie danych.<br><br>Ustawienie domyślne lokalizacje plików kopii zapasowej i bazy danych na dyskach danych, nie dysku systemu operacyjnego.<br><br>Włącz zablokowanych stron.<br><br>Zastosuj dodatków service pack programu SQL Server i aktualizacje zbiorcze.|
|Właściwych dla funkcji|Utwórz kopię zapasową bezpośrednio do magazynu obiektów blob (jeśli jest obsługiwana przez wersję programu SQL Server używane).|
|||

Aby uzyskać więcej informacji na temat *jak* i *Dlaczego* aby te optymalizacje, zapoznaj się z tematem szczegółowe informacje i wskazówki zamieszczone w poniższych sekcjach.

## <a name="virtual-machine-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny wirtualnej

Dla wrażliwych wydajności aplikacji, następujące [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) są zalecane:

- **SQL Server Enterprise edition:** DS3 lub nowszej

- **SQL Server Standard edition i Web edition:** DS2 lub nowszej

Stos Azure jest różnic w wydajności między seriami rodziny maszyny wirtualnej, a DS_v2 DS.

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Maszyny wirtualne: seria DS (wraz z serii DSv2) w stosie usługi Azure Podaj maksymalny system operacyjny dysku i danych przepływność dysku (IOPS). Maszyny wirtualnej z serii DS lub DSv2 zawiera maksymalnie 1000 IOPS dla dysku systemu operacyjnego i maksymalnie 2300 IOPS dla każdego dysku danych, niezależnie od tego typu lub rozmiaru wybranego dysku.

Przepływność dysku danych jest określana jednoznacznie oparte na serii rodziny maszyny wirtualnej. Możesz [znajduje się w tym artykule](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) do identyfikowania przepływność dysku danych w serii rodziny maszyny wirtualnej.

> [!NOTE]  
> W przypadku obciążeń produkcyjnych wybierz maszynę wirtualną serii DS lub serii DSv2 zapewnienie maksymalna możliwa liczba IOPS w systemie operacyjnym dysku i dysków z danymi.

Podczas tworzenia konta magazynu Azure stosu, ponieważ ta funkcja nie jest dostępna w stosie usługi Azure opcja — replikacja geograficzna nie ma wpływu.

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Istnieją trzy typy głównego dysku na maszynie wirtualnej platformy Azure stosu:

- **Dysk systemu operacyjnego:** podczas tworzenia maszyny wirtualnej platformy Azure stosu platformy dołącza co najmniej jednego dysku (oznaczone jako **C** dysku) do maszyny wirtualnej dla dysku systemu operacyjnego. Ten dysk jest plik VHD przechowywany jako stronicowy obiekt blob w magazynie.

- **Dysku tymczasowym:** maszyny wirtualne Azure stosu zawiera inny dysk o nazwie dysku tymczasowym (oznaczone jako **D** dysku). Jest to dysk na węźle, który może służyć do miejsce na pliki tymczasowe.

- **Dyski danych:** dodatkowych dysków do maszyny wirtualnej można dołączyć jako dyski danych, a te dyski są przechowywane w magazynie jako stronicowych obiektów blob.

W poniższych sekcjach opisano zalecenia dotyczące używania tych dyskach.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego jest wirtualnego dysku twardego, który można rozruchu i instalacji uruchomiona wersja systemu operacyjnego i jest oznaczony jako **C** dysku.

### <a name="temporary-disk"></a>Tymczasowe dysku

Dysk magazyn tymczasowy, oznaczone jako **D** dysku, nie jest trwały. Nie należy przechowywać żadnych danych, nie chcesz stracić, takie jak pliki bazy danych użytkowników lub plików dziennika transakcji użytkownika, na **D** dysku.

Zalecamy przechowywanie bazy danych TempDB na dysk z danymi, a każdy dysk danych maksymalnie do 2300 IOPS dla każdego dysku danych.

### <a name="data-disks"></a>Dyski z danymi

- **Użyj dysków danych na potrzeby plików danych i dziennika.** Jeśli nie używasz rozkładanie, należy użyć dwóch dysków danych z maszyny wirtualnej, który obsługuje magazyn w warstwie Premium, gdy jeden dysk zawiera pliki dziennika, a drugi zawiera dane i pliki bazy danych TempDB. Każdy dysk danych zawiera pewną liczbę IOPS i przepustowości (MB/s) w zależności od rodziny maszyny wirtualnej, zgodnie z opisem w [rozmiarów maszyn wirtualnych obsługiwanych w stosie Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Jeśli korzystasz z techniki Rozkładanie dysku, takich jak funkcja miejsca do magazynowania, umieść wszystkie pliki danych i dziennika na jednym dysku (w tym TempDB). Ta konfiguracja zapewnia maksymalną liczbę IOPS dostępne dla programu SQL Server do, niezależnie od tego, które musi pliku mogły ich używać w dowolnym momencie konkretnego.

> [!NOTE]  
> Podczas obsługi administracyjnej maszyny wirtualnej programu SQL Server w portalu, istnieje możliwość edytowania konfigurację magazynu. W zależności od konfiguracji stos Azure umożliwia skonfigurowanie co najmniej jeden dysk. Wiele dysków są łączone w puli magazynu jednego. Pliki danych i dziennika znajdują się ze sobą w tej konfiguracji.

- **Rozkładanie:** więcej przepustowości, można dodać dodatkowego dysku z danymi i używać rozkładanie. Aby określić liczbę dysków z danymi należy przeanalizować liczbę IOPS i przepustowość wymagana dla plików dzienników i danych i plików bazy danych TempDB. Należy zauważyć, że są limity liczby operacji na jednym dysku danych oparta na rodziny serii maszyny wirtualnej, a nie zależnie od rozmiaru maszyny wirtualnej. Limity przepustowości sieci, jednak są oparte na rozmiar maszyny wirtualnej. Zobacz tabelę w [rozmiarów maszyny wirtualnej w stosie Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) uzyskać więcej szczegółowych informacji. Skorzystaj z poniższych wskazówek:

    - Dla systemu Windows Server 2012 lub nowszym, użyj [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wytycznymi:

        1.  Wartość przeplotu (rozmiar przeplotu) 64 KB (65 536 bajtów) dla transakcji online przetwarzania obciążeń (OLTP) i 256 KB (262 144 bajty) dla obciążeń magazynowania danych, aby uniknąć wpływu na wydajność ze względu na niezgodność partycji. To musi być ustawiona przy użyciu programu PowerShell.

        2.  Ustaw liczbę kolumn = Liczba dysków fizycznych. W przypadku konfigurowania więcej niż osiem dysków (nie interfejsu użytkownika Menedżera serwera) za pomocą programu PowerShell.

            Na przykład następujące programu PowerShell jest tworzona nowa pula magazynu o rozmiarze przeplotu wartość 64 KB, a liczba kolumn do 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Określ liczbę dysków skojarzone z puli magazynów oparte na Twoich oczekiwań obciążenia. Należy pamiętać, że rozmiary maszyn wirtualnych różnych zezwalają na różne liczby dysków dołączonych danych. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych obsługiwanych w stosie Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Aby uzyskać maksymalna możliwa liczba IOPS dla dysków z danymi, zalecane jest dodanie maksymalna liczba dysków danych obsługiwane przez użytkownika [rozmiar maszyny wirtualnej](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) i użyj rozkładanie.
- **Rozmiar jednostki alokacji systemu plików NTFS:** podczas formatowania dysku danych, zaleca się użyć rozmiar jednostki alokacji 64 KB danych i plików dziennika, jak również w bazie danych TempDB.
- **Rozwiązań z zakresu zarządzania na dysku:** podczas usuwania dysku danych, Zatrzymaj usługę programu SQL Server podczas zmiany. Ponadto nie zmieniają ustawień pamięci podręcznej na dyskach, jak nie zapewnia żadnych ulepszenia wydajności.

> [!WARNING]  
> Nie można zatrzymać usługi programu SQL podczas tych czynności może spowodować uszkodzenie bazy danych.

## <a name="io-guidance"></a>Wskazówki dotyczące operacji We/Wy

- Rozważ włączenie inicjowanie błyskawicznych plików w celu skrócenia czasu wymaganego do przydzielenia pierwszy plik. Przeprowadzać inicjowanie błyskawicznych plików, można przyznać konto usługi programu SQL Server (MSSQLSERVER) z **SE_MANAGE_VOLUME_NAME** i dodaj go do **wykonywać zadania konserwacji woluminów** zabezpieczeń zasady. Jeśli używasz obrazu platformy programu SQL Server na platformie Azure, domyślne konto usługi (**NT Service\MSSQLSERVER**) nie jest dodawany do **wykonywać zadania konserwacji woluminów** zasady zabezpieczeń. Innymi słowy inicjowanie błyskawicznych plików nie jest włączona w obrazie platformy Azure serwera SQL. Po dodaniu konta usługi programu SQL Server do **wykonywać zadania konserwacji woluminów** zasady zabezpieczeń, uruchom ponownie usługę SQL Server. Może to być zagadnienia dotyczące zabezpieczeń dla tej funkcji. Aby uzyskać więcej informacji, zobacz [Inicjowanie plików bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatyczne zwiększanie** jest awaryjnych nieoczekiwany wzrost. Nie zarządzania rozwojem danych i dziennika na podstawie bieżącego z automatycznego przyrostu. Jeśli używane jest automatyczne zwiększanie, wstępnie powiększania plików przy użyciu **rozmiar** przełącznika.
- Upewnij się, że **autoshrink** jest wyłączona, aby uniknąć niepotrzebnych zadań, które mogą negatywnie wpłynąć na wydajność.
- Ustawienia domyślne lokalizacje plików kopii zapasowej i bazy danych. Użyj zaleceń w tym artykule i wprowadź zmiany w oknie właściwości serwera. Aby uzyskać instrukcje, zobacz [wyświetlić lub zmienić domyślne lokalizacje dla danych i plików dziennika (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie można wprowadzić te zmiany:

    > ![Wyświetl lub zmień domyślne lokalizacje](./media/sql-server-vm-considerations/image1.png)

- Włącz zablokowanych stron we/wy i żadnych działań stronicowania. Aby uzyskać więcej informacji, zobacz [włączyć blokowania stron w pamięci (system Windows) — opcja](https://msdn.microsoft.com/library/ms190730.aspx).

- Należy rozważyć kompresowania wszystkie pliki danych podczas przesyłania lub brak stosu Azure, w tym kopie zapasowe.

## <a name="feature-specific-guidance"></a>Wskazówki dotyczące funkcji

Niektóre wdrożenia może osiągnięcia korzyści dodatkowe wydajności za pomocą bardziej zaawansowanych technik konfiguracji. Poniższa lista zawiera opis niektóre funkcje programu SQL Server, które mogą pomóc osiągnąć większą wydajność:

- **Tworzenie kopii zapasowych Azure** **magazynu.** Podczas wykonywania kopii zapasowych programu SQL Server uruchomionego na maszynach wirtualnych Azure stosu, używając kopii zapasowej serwera SQL do adresu URL. Ta funkcja jest dostępnych w programie SQL Server 2012 z dodatkiem SP1 CU2 i zalecane w przypadku tworzenie kopii zapasowych na dyskach danych dołączonych.

    Gdy z kopii zapasowej lub przywracania przy użyciu usługi Azure storage, wykonaj zalecenia zawarte w [kopii zapasowych programu SQL Server do adresu URL najlepsze rozwiązania i rozwiązywanie problemów](https://msdn.microsoft.com/library/jj919149.aspx) i [Przywracanie z kopii zapasowych przechowywanych na platformie Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Można również zautomatyzować te kopie zapasowe przy użyciu [automatyczna usługa Backup dla programu SQL Server w usłudze Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Utwórz kopię zapasową do magazynu Azure stosu.** Można tworzyć kopie zapasowe do magazynu Azure stosu w podobny sposób jak w przypadku wykonywanie kopii zapasowej na magazyn Azure. Podczas tworzenia kopii zapasowej wewnątrz programu SQL Server Management Studio (SSMS), należy ręcznie wprowadzić informacje o konfiguracji. Nie można użyć narzędzia SSMS, można utworzyć kontenera magazynu lub sygnatura dostępu współdzielonego. SSMS tylko łączy się z subskrypcji platformy Azure, nie subskrypcji Azure stosu. Zamiast tego należy utworzyć konta magazynu, kontenera i sygnatura dostępu współdzielonego w portalu Azure stosu lub przy użyciu programu PowerShell.

    Po umieszczeniu informacje w oknie dialogowym kopii zapasowej programu SQL Server:

    ![Kopia zapasowa programu SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Sygnatura dostępu współdzielonego to token sygnatury dostępu Współdzielonego z portalu Azure stosu bez znaku "?" w ciągu. Jeśli używasz funkcji kopiowania z portalu, należy usunąć znaku "?" dla tokenu do pracy w programie SQL Server.

    Raz ma miejsce docelowe kopii zapasowej, konfigurowanie i skonfigurowane w programie SQL Server, następnie kopię zapasową można wykonywać do magazynu obiektów blob Azure stosu.

## <a name="next-steps"></a>Kolejne kroki

[Za pomocą usług lub tworzenia aplikacji dla platformy Azure stosu](azure-stack-considerations.md)