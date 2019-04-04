---
title: Należy stosować najlepsze rozwiązania programu SQL Server i zwiększyć wydajność w przypadku maszyn wirtualnych usługi Azure Stack | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera programu SQL server najlepsze rozwiązania ułatwiające zwiększenie wydajności i optymalizowanie programu SQL Server na maszynach wirtualnych platformy Azure Stack.
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
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 03a354a7d670033fa86ebbb094710a836b6219c4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879068"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>SQL server najlepsze rozwiązania w celu optymalizacji wydajności w usłudze Azure Stack

Ten artykuł zawiera najlepsze rozwiązania programu SQL server do optymalizacji programu SQL Server i zwiększyć wydajność w przypadku maszyn wirtualnych Microsoft Azure Stack. Podczas uruchamiania programu SQL Server na maszynach wirtualnych usługi Azure Stack, użyj tej samej bazy danych dostrajanie wydajności opcji mające zastosowanie do programu SQL Server w środowisku serwera w środowisku lokalnym. Wydajność relacyjnej bazy danych w chmurze usługi Azure Stack, zależy od wielu czynników. Czynniki obejmują rodziny rozmiaru maszyny wirtualnej, a konfiguracja dysków z danymi.

Podczas tworzenia obrazów programu SQL Server [należy wziąć pod uwagę inicjowania obsługi administracyjnej maszyn wirtualnych w portalu usługi Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Pobierz rozszerzenie SQL IaaS z zarządzania w portalu Marketplace w portalu administracyjnym usługi Azure Stack i Pobierz swój wybór w opcji SQL maszyny wirtualne dyski twarde (VHD). Obejmują one SQL2014SP2 SQL2016SP1 i SQL2017.

> [!NOTE]  
> W artykule opisano sposób aprowizacji maszyny wirtualnej programu SQL Server przy użyciu portalu Azure globalnego, wskazówki również ma zastosowanie do usługi Azure Stack z następującymi różnicami: Dyski SSD nie jest dostępna dla dysku systemu operacyjnego, dyski zarządzane nie są dostępne i występują niewielkie różnice w konfiguracji magazynu.

Wprowadzenie *najlepsze* wydajności dla programu SQL Server na maszynach wirtualnych usługi Azure Stack jest celem tego artykułu. Jeżeli obciążenie jest mniej wymagających, może nie wymagać co zalecanych optymalizacji. Twoje potrzeby związane z wydajnością i wzorce obciążenia wziąć pod uwagę oceny tych zaleceń.

> [!NOTE]  
> Aby uzyskać wskazówki dotyczące wydajności dla programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [w tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Lista kontrolna dotycząca najlepsze rozwiązania dotyczące serwera SQL

Poniższa lista kontrolna jest przeznaczony dla uzyskania optymalnej wydajności programu SQL Server na maszynach wirtualnych usługi Azure Stack:


|Obszar|Optymalizacje|
|-----|-----|
|Rozmiar maszyny wirtualnej |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) lub nowsze dla programu SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) lub nowsze dla programu SQL Server Standard edition i Web edition.|
|Magazyn |Użyj rodziny maszyn wirtualnych, który obsługuje [usługi Premium storage](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Dyski |Użyj co najmniej dwa dyski danych (jeden dla plików dziennika) i jeden dla pliku danych i bazy danych TempDB, a następnie wybierz rozmiar dysku, w oparciu o potrzeby związane z pojemnością. Ustaw domyślne lokalizacje plików danych tych dysków podczas instalacji programu SQL Server.<br><br>Należy unikać używania systemu operacyjnego oraz dyski tymczasowe do przechowywania bazy danych lub rejestrowania.<br>STRIPE wiele dysków danych platformy Azure, aby uzyskać większą przepustowość operacji We/Wy przy użyciu funkcji miejsca do magazynowania.<br><br>Formatuj przy użyciu rozmiarów udokumentowanego alokacji.|
|OPERACJE WE/WY|Włącz inicjowanie błyskawiczne plików dla danych plików.<br><br>Ogranicz automatycznego zwiększania dla baz danych, z przyrostami co stały rozsądnie małe (64 MB — 256 MB).<br><br>Wyłącz zmniejszania w bazie danych.<br><br>Zdefiniować domyślne lokalizacje plików bazy danych i kopii zapasowych na dyskach danych, nie dysk systemu operacyjnego.<br><br>Włącz zablokowanych stron.<br><br>Zastosuj dodatków service pack programu SQL Server i aktualizacjami zbiorczymi.|
|Specyficzne dla funkcji|Utwórz kopię zapasową bezpośrednio do magazynu obiektów blob (jeśli jest obsługiwany przez wersję programu SQL Server).|
|||

Aby uzyskać więcej informacji na temat *jak* i *Dlaczego* Aby wprowadzić te optymalizacje, można znaleźć szczegółowe informacje i wskazówki można znaleźć w następujących sekcjach.

## <a name="virtual-machine-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny wirtualnej

W przypadku następujących wrażliwego na wydajność aplikacji [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) są zalecane:

- **SQL Server Enterprise edition:** DS3 lub nowszej

- **SQL Server Standard edition i wersji Web edition:** DS2 lub nowszej

Dzięki usłudze Azure Stack nie ma żadnej różnicy wydajności między serię rodziny maszyn wirtualnych DS i DS_v2.

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Maszyny wirtualne serii DS (wraz z serii DSv2) w usłudze Azure Stack oferują maksymalnej wersji systemu operacyjnego dysku i danych przepływność dysków (IOPS). Maszynę wirtualną z serii DS lub DSv2 zawiera maksymalnie 1000 operacji We/Wy dysku systemu operacyjnego i maksymalnie 2300 operacje We/Wy na dysku danych, niezależnie od tego typu lub rozmiaru wybranego dysku.

Przepływność dysku danych jest określany na serię rodziny maszyn wirtualnych jednoznacznie podstawie. Możesz [znajdują się w tym artykule](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) do identyfikowania przepływność dysków danych na serię rodziny maszyn wirtualnych.

> [!NOTE]  
> W przypadku obciążeń produkcyjnych wybierz maszynę wirtualną serii DS lub serii DSv2, aby zapewnić maksymalną wartość IOPS możliwe w systemie operacyjnym dysku i dysków z danymi.

Podczas tworzenia konta magazynu w usłudze Azure Stack, dla opcji replikacji geograficznej jest ignorowany, ponieważ ta funkcja nie jest dostępna w usłudze Azure Stack.

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Istnieją trzy typy głównego dysku na maszynie wirtualnej usługi Azure Stack:

- **Dysk systemu operacyjnego:** Podczas tworzenia maszyny wirtualnej usługi Azure Stack platformy dołącza co najmniej jeden dysk (oznaczone jako **C** dysku) do maszyny wirtualnej dla dysku systemu operacyjnego. Ten dysk jest dyskiem VHD przechowywanym jako stronicowy obiekt blob w magazynie.

- **Dysk tymczasowy:** Maszyny wirtualne w usłudze Azure Stack zawiera inny dysk o nazwie dysku tymczasowego (oznaczone jako **D** dysku). Jest to dysk na węźle, który może służyć do miejsca na pliki tymczasowe.

- **Dyski z danymi:** Dodatkowe dyski do maszyny wirtualnej można dołączyć jako dyski z danymi, a te dyski są przechowywane w magazynach jako stronicowe obiekty BLOB.

W poniższych sekcjach opisano zalecenia dotyczące używania tych różnych dyskach.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego jest wirtualny dysk twardy, który można uruchomić i zainstalować jako uruchomionej wersji systemu operacyjnego i jest oznaczony jako **C** dysku.

### <a name="temporary-disk"></a>Dysk tymczasowy

Dysk magazynu tymczasowego, oznaczone jako **D** dysku, nie jest trwały. Nie należy przechowywać żadnych danych, które nie chcesz stracić, takich jak pliki bazy danych użytkownika lub plików dziennika transakcji użytkownika, na **D** dysku.

Zalecamy przechowywanie bazy danych TempDB na dysk z danymi, ponieważ każdy dysk z danymi zapewnia maksymalnie do 2300 operacje We/Wy na dysk z danymi.

### <a name="data-disks"></a>Dyski z danymi

- **Korzystanie z dysków danych dla plików danych i dziennika.** Jeśli nie używasz rozkładanie, należy użyć dwóch dysków danych z maszyny wirtualnej, która obsługuje usługę Premium storage, gdzie jeden dysk zawiera pliki dziennika, a drugi zawiera dane i pliki bazy danych TempDB. Każdy dysk z danymi oferuje pewną liczbę operacji We/Wy i przepustowość (MB/s) w zależności od rodziny maszyn wirtualnych, zgodnie z opisem w [rozmiarów maszyn wirtualnych obsługiwanych w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Jeśli używasz techniki Rozkładanie dysku, takich jak miejsca do magazynowania, należy umieścić wszystkie pliki danych i dziennika na dysku (w tym bazy danych TempDB). Ta konfiguracja zapewnia maksymalną liczbę operacji We/Wy dostępne dla programu SQL Server, niezależnie od tego, który musi pliku ich używać w danym momencie.

> [!NOTE]  
> Podczas aprowizowania maszyny wirtualnej programu SQL Server w portalu, istnieje możliwość edytowania konfigurację magazynu. W zależności od konfiguracji usługi Azure Stack umożliwia skonfigurowanie co najmniej jeden dysk. Wiele dysków są łączone w pulę magazynu jednego. Pliki danych i dziennika znajdują się ze sobą w tej konfiguracji.

- **Rozkładanie:** Większą przepustowość można dodać dodatkowego dysku z danymi i używać Rozkładanie dysku. Aby określić liczbę dysków z danymi, których potrzebujesz, analizę liczby operacji We/Wy i przepustowości wymaganej dla plików dziennika i danych i plików bazy danych TempDB. Należy zauważyć, że obowiązują limity operacji We/Wy na dysku danych oparte na rodzina serii maszyny wirtualnej, a nie na podstawie rozmiaru maszyny wirtualnej. Limity przepustowości sieci, jednak są oparte na rozmiar maszyny wirtualnej. Zobacz tabele w [maszyny wirtualne o rozmiarach w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) Aby uzyskać więcej szczegółów. Skorzystaj z poniższych wskazówek:

  - Dla systemu Windows Server 2012 lub nowszym, użyj [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wytycznymi:

    1. Ustaw przeplotu (rozmiar woluminu rozłożonego) 64 KB (65 536 bajtów) do przetwarzania obciążeń OLTP i 256 KB (262 144 bajty) dla obciążeń magazynowania danych, aby uniknąć wpływu na wydajność ze względu na niezgodność partycji transakcji online. Należy to określić za pomocą programu PowerShell.

    2. Ustaw liczbę kolumn = Liczba dysków fizycznych. Podczas konfigurowania więcej niż ośmiu dysków (nie interfejs użytkownika Menedżera serwera) przy użyciu programu PowerShell.

       Na przykład następujące polecenie programu PowerShell tworzy nową pulę magazynów z rozmiarem przeplotu wartość 64 KB i liczbę kolumn do 2:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Określ liczbę dysków skojarzonych z puli magazynów, w oparciu o Twoim oczekiwaniom obciążenia. Należy pamiętać, że różne rozmiary maszyny wirtualnej zezwalają na różne liczby dołączonych dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych obsługiwanych w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Aby uzyskać maksymalną wartość IOPS możliwe dla dysków z danymi, zalecane jest dodanie maksymalna liczba dysków danych obsługiwanych przez usługi [rozmiar maszyny wirtualnej](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) i użyj Rozkładanie dysku.
- **Rozmiar jednostki alokacji systemu plików NTFS:** Podczas formatowania dysku danych, zaleca się użycie rozmiaru jednostki alokacji 64 KB dla plików danych i dziennika, a także bazy danych TempDB.
- **Rozwiązania z zakresu zarządzania dysku:** Podczas usuwania dysku z danymi, należy zatrzymać usługi programu SQL Server podczas zmiany. Ponadto nie zmieniaj ustawienia pamięci podręcznej na dyskach, ponieważ nie zapewnia żadnych ulepszenia wydajności.

> [!WARNING]  
> Nie można zatrzymać usługi programu SQL podczas wykonywania tych operacji może spowodować uszkodzenie bazy danych.

## <a name="io-guidance"></a>Wskazówki dotyczące operacji We/Wy

- Rozważ włączenie inicjowanie błyskawiczne plików skrócić czas wymagany do przydzielania pierwszy plik. Z zalet inicjowanie błyskawiczne plików, można przyznać konto usługi programu SQL Server (MSSQLSERVER) za pomocą **SE_MANAGE_VOLUME_NAME** i dodać go do **wykonywania zadań konserwacji woluminów** zabezpieczeń zasady. Jeśli używasz obrazu platformy programu SQL Server na platformie Azure, domyślne konto usługi (**NT Service\MSSQLSERVER**) nie jest dodawany do **wykonywania zadań konserwacji woluminów** zasady zabezpieczeń. Innymi słowy inicjowanie błyskawiczne plików nie jest włączona w obrazie platformy programu SQL Server Azure. Po dodaniu konta usługi programu SQL Server, aby **wykonywania zadań konserwacji woluminów** zasady zabezpieczeń, uruchom ponownie usługę programu SQL Server. Może to być zagadnienia dotyczące zabezpieczeń dla tej funkcji. Aby uzyskać więcej informacji, zobacz [Inicjowanie plików bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatyczne zwiększanie** jest awaryjny nieoczekiwany wzrost. Nie zarządzać wzrostem ilości danych i dziennika na podstawie codziennych zwiększania. Jeśli jest używana opcja automatycznego zwiększania, wstępnie rozwój plików przy użyciu **rozmiar** przełącznika.
- Upewnij się, że **pewnego** jest wyłączona, aby uniknąć niepotrzebnych nakładów pracy, który może negatywnie wpłynąć na wydajność.
- Skonfiguruj domyślne lokalizacje plików bazy danych i kopii zapasowych. Użyj zaleceń w tym artykule, a następnie wprowadź zmiany w oknie dialogowym właściwości serwera. Aby uzyskać instrukcje, zobacz [wyświetlić lub zmienić domyślne lokalizacje dla danych i plików dziennika (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie można wprowadzić te zmiany:

    > ![Wyświetl lub zmień domyślne lokalizacje](./media/sql-server-vm-considerations/image1.png)

- Włącz zablokowanych stron, które mają zmniejszyć We/Wy i każde działanie stronicowania. Aby uzyskać więcej informacji, zobacz [Włącz blokowanie stron w pamięci (Windows) — opcja](https://msdn.microsoft.com/library/ms190730.aspx).

- Należy wziąć pod uwagę kompresowanie wszystkich plików danych podczas przesyłania wchodzącym/wychodzącym usługi Azure Stack, w tym kopie zapasowe.

## <a name="feature-specific-guidance"></a>Ze wskazówek specyficznych dla funkcji

Niektóre wdrożenia mogą osiągać korzyści wyższą wydajność przy użyciu bardziej zaawansowanych technik konfiguracji. Poniższa lista wyróżnione niektóre funkcje programu SQL Server, które mogą pomóc Ci osiągnąć lepszą wydajność:

- **Tworzenie kopii zapasowej na platformie Azure** **magazynu.** Podczas wykonywania kopii zapasowych programu SQL Server uruchomionego na maszynach wirtualnych usługi Azure Stack, można użyć narzędzia Kopia zapasowa programu SQL Server do adresu URL. Ta funkcja jest dostępna, począwszy od pakietu CU2 programu SQL Server 2012 z dodatkiem SP1 i zalecane w przypadku wykonywania kopii zapasowych dołączonych dysków z danymi.

    Po usługi tworzenia kopii zapasowej lub przywracania przy użyciu usługi Azure storage, wykonaj zalecenia zawarte w [kopia zapasowa programu SQL Server do adresu URL najlepszych rozwiązań i rozwiązywanie problemów](https://msdn.microsoft.com/library/jj919149.aspx) i [Przywracanie z kopii zapasowych przechowywanych w systemie Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Możesz też zautomatyzować te kopie zapasowe przy użyciu [automatyczne kopie zapasowe dla programu SQL Server w usłudze Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Tworzenie kopii zapasowej do magazynu usługi Azure Stack.** Kopię zapasową można wykonywać w magazynie usługi Azure Stack w podobny sposób jak w przypadku wykonywania kopii zapasowych usługi Azure Storage. Podczas tworzenia kopii zapasowej w SQL Server Management Studio (SSMS), należy ręcznie wprowadzić informacje o konfiguracji. Za pomocą programu SSMS nie można utworzyć kontenera magazynu lub sygnatura dostępu współdzielonego. SSMS tylko łączy się z subskrypcjami platformy Azure, nie subskrypcji usługi Azure Stack. Zamiast tego musisz utworzyć konto magazynu, kontener i sygnaturę dostępu współdzielonego w portalu usługi Azure Stack lub przy użyciu programu PowerShell.

    Po umieszczeniu informacje do okna dialogowego kopii zapasowej programu SQL Server:

    ![SQL Server Backup](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Sygnatura dostępu współdzielonego to token sygnatury dostępu Współdzielonego z portalu usługi Azure Stack znaczeniem bez "?" w ciągu. Jeśli używasz funkcji kopiowania z poziomu portalu, musisz usunąć wiodące "?" Aby uzyskać token, który działa w programie SQL Server.

    Gdy ma miejsce docelowe kopii zapasowej, konfigurowanie i skonfigurowane w programie SQL Server, następnie kopii zapasowych do magazynu obiektów blob usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Za pomocą usług lub tworzenia aplikacji dla usługi Azure Stack](azure-stack-considerations.md)