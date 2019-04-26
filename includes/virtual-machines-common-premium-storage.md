---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 09/24/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: 40ff2339ad34a72079109317bf0a89dfbc6458e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232758"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Magazyn w warstwie Premium o wysokiej wydajności i dysków zarządzanych dla maszyn wirtualnych

Usługa Azure Premium Storage zapewnia obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych (VM) przy użyciu wejścia/wyjścia (We/Wy)-intensywnych obciążeń. Dyski maszyny Wirtualnej, korzystających z usługi Premium Storage umożliwia przechowywanie danych na dyskach półprzewodnikowych (SSD). Aby móc korzystać z szybkości i wydajności dysków usługi premium storage, należy przeprowadzić migrację istniejących dysków maszyny Wirtualnej do usługi Premium Storage.

Na platformie Azure do maszyny Wirtualnej można dołączyć kilka dysków usługi premium storage. Używanie wielu dysków zapewnia aplikacji do 256 TB magazynu na maszynę Wirtualną, jeśli używasz rozmiarów (wersja zapoznawcza), Twoja aplikacja może mieć maksymalnie około 2 PiB magazynu na maszynę Wirtualną. Dzięki usłudze Premium Storage aplikacje mogą osiągnąć 80 000 operacji wejścia/wyjścia na sekundę (IOPS) na maszynę Wirtualną i przepływność dysków, maksymalnie 2000 MB na sekundę (MB/s) na maszynę Wirtualną. Operacje odczytu umożliwiają bardzo małe wartości opóźnienia.

Dzięki usłudze Premium Storage platforma Azure oferuje możliwość naprawdę lift-and-shift wymagających aplikacji przedsiębiorstwa takich jak Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite i programu SharePoint farmy serwerów w chmurze. Możesz uruchamiać obciążenia intensywnie bazy danych w aplikacji, takich jak SQL Server, Oracle, MongoDB, MySQL i Redis, wymagających spójnej wysokiej wydajności i małym opóźnieniu.

> [!NOTE]
> Uzyskać najlepszą wydajność aplikacji zaleca się migracji dysku maszyny Wirtualnej, który wymaga wysokiej operacje We/Wy do magazynu Premium Storage. Jeśli dysk nie wymaga wysokiego operacje We/Wy, może pomóc koszty limit, przechowując go w magazynie standard Storage platformy Azure. W magazynie standard storage dane dysku maszyny Wirtualnej są przechowywane dyski twarde (HDD) zamiast na dyskach SSD.

Platforma Azure oferuje dwa sposoby tworzenia dysków magazynu premium storage dla maszyn wirtualnych:

* **Dyski niezarządzane**

    Pierwotną metodą jest użycie dysków niezarządzanych. Dysk niezarządzany służy do zarządzania kont magazynu, używane do przechowywania plików wirtualnego dysku twardego (VHD), które odpowiadają dysków maszyn wirtualnych. Pliki VHD są przechowywane jako stronicowe obiekty BLOB na kontach magazynu Azure. 

* **Dyski zarządzane**

    Po wybraniu [usługi Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md), platforma Azure zarządza kont magazynu, których używasz dla dysków maszyny Wirtualnej. Należy określić typ dysku (Premium lub standardowa) i rozmiar dysku, które są potrzebne. Platforma Azure tworzy i zarządza dysku. Nie trzeba martwić się o umieszczenie dysków w ramach wielu kont magazynu, aby upewnić się, pozostają w ramach limitów skalowalności dla konta magazynu. Azure sobie z nimi poradzi dla Ciebie.

Firma Microsoft zaleca wybranie z dyskami zarządzanymi, aby móc korzystać z ich wiele funkcji.

Aby rozpocząć pracę z usługą Premium Storage [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 

Aby uzyskać informacje na temat migracji istniejących maszyn wirtualnych do usługi Premium Storage, zobacz [konwersji maszyny Wirtualnej z systemem Windows z dysków niezarządzanych do usługi managed disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) lub [Konwertuj Maszynę wirtualną systemu Linux z dysków niezarządzanych do usługi managed disks](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Usługa Premium Storage jest dostępna w większości regionów. Aby uzyskać listę dostępnych regionów, zobacz wiersz dotyczący **Magazyn dyskowy** w [Azure dostępność produktów według regionów](https://azure.microsoft.com/regions/#services).

## <a name="features"></a>Funkcje

Poniżej przedstawiono niektóre funkcje usługi Premium Storage:

* **Dysków magazynu Premium storage**

    Usługa Premium Storage obsługuje dyski maszyn wirtualnych, które mogą być dołączane do określonego rozmiaru maszyny wirtualne. Usługa Premium Storage obsługuje szeroką gamę maszyn wirtualnych platformy Azure. Masz do wyboru, rozmiarów dysków osiem GA:  P4 (32 GiB), P6 (64 GiB), P10 (128 GiB), P15 (256 GiB), P20 (512 GiB), P30 (1,024 GiB), P40 (2,048 GiB), P50 (4,095 GiB). A także trzech rozmiarach dysków (wersja zapoznawcza): P60 8192 GiB (8 TiB) P70 16,348 GiB (16 TiB) P80 32 767 GiB (32 TiB). Rozmiary dysków P4, P6, P15 P60, P70 i P80 są obecnie obsługiwane tylko w przypadku dysków zarządzanych. Rozmiar każdego dysku ma swój własny specyfikacje wydajności. W zależności od wymagań aplikacji możesz dołączyć co najmniej jeden dysk do maszyny Wirtualnej. Opisujemy specyfikacje bardziej szczegółowo w [cele dotyczące skalowalności i wydajności Usługa Premium Storage](#scalability-and-performance-targets).

* **Stronicowe obiekty BLOB w warstwie Premium**

    Usługa Premium Storage obsługuje stronicowych obiektów blob. Stronicowe obiekty BLOB umożliwiają przechowywanie trwałych, niezarządzanych dysków dla maszyn wirtualnych w usłudze Premium Storage. W przeciwieństwie do magazynu w warstwie standardowa usługi Azure Premium Storage obsługuje blokowe obiekty BLOB, nie uzupełnialnych obiektów blob, plików, tabel lub kolejek. Stronicowe obiekty BLOB w warstwie Premium obsługuje sześć rozmiarów z P10 P50 i P60 (8191GiB). P60 Premium stronicowych obiektów blob nie jest obsługiwana być dołączane jako dyski maszyn wirtualnych. 

    Dowolny obiekt umieścić na koncie usługi premium storage będzie stronicowych obiektów blob. Obiekt blob typu page przyciąganie do jednego z obsługiwanych rozmiarów elastycznie. Jest to, dlaczego nie ma konta usługi premium storage ma być używany do przechowywania obiektów blob niewielki.

* **Konto magazynu Premium storage**

    Aby rozpocząć korzystanie z usługi Premium Storage, należy utworzyć konto magazynu premium storage dla dysków niezarządzanych. W [witryny Azure portal](https://portal.azure.com), aby utworzyć konto magazynu premium storage, wybierz **Premium** warstwy wydajności. Wybierz **magazyn lokalnie nadmiarowy (LRS)** opcji replikacji. Możesz również utworzyć konto magazynu premium storage, ustawiając warstwy wydajności **Premium_LRS**. Aby zmienić warstwę wydajności, użyj jednej z następujących metod:
     
  - [Program PowerShell dla usługi Azure Storage](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
  - [Wiersza polecenia platformy Azure dla usługi Azure Storage](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
  - [Usługa Azure Storage REST interfejsu API dostawcy zasobów](https://docs.microsoft.com/rest/api/storagerp) (w przypadku wdrożeń usługi Azure Resource Manager) lub jeden z biblioteki klienta dostawcy zasobów usługi Azure Storage

    Aby dowiedzieć się więcej na temat limitów konta magazynu premium, zobacz [cele dotyczące skalowalności i wydajności](#scalability-and-performance-targets).

* **Magazyn lokalnie nadmiarowy w warstwie Premium**

    Konto magazynu premium storage obsługuje tylko lokalnie nadmiarowym jako opcję replikacji. Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych w jednym regionie. Do odzyskiwania po awarii regionalnej możesz należy wykonać kopię zapasową dysków maszyn wirtualnych w różnych regionach za pomocą [kopia zapasowa Azure](../articles/backup/backup-introduction-to-azure-backup.md). Możesz również użyć konta magazynu geograficznie nadmiarowego (GRS) jako magazyn kopii zapasowych. 

    Platforma Azure używa konta magazynu jako kontener dla niezarządzanych dysków. Po utworzeniu maszyny Wirtualnej platformy Azure, która obsługuje usługę Premium Storage z dyskami niezarządzanymi, i wybierz konto magazynu premium storage, systemu operacyjnego i dyski danych są przechowywane na tym koncie magazynu.

## <a name="supported-vms"></a>Obsługiwane maszyny wirtualne

Usługa Premium Storage jest obsługiwana na różnych maszynach wirtualnych platformy Azure. Korzystać z dysków standardowych i premium storage, z tymi typami maszyny Wirtualnej. Nie można używać dysków magazynu premium storage serię maszyn wirtualnych, które nie są w warstwie Premium zgodna z magazynem.


Aby uzyskać informacje o typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Windows, zobacz [Windows VM sizes (Rozmiary maszyn wirtualnych z systemem Windows)](../articles/virtual-machines/windows/sizes.md). Aby uzyskać informacje o typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Linux, zobacz [Linux VM sizes (Rozmiary maszyn wirtualnych z systemem Linux)](../articles/virtual-machines/linux/sizes.md).

Poniżej przedstawiono niektóre z funkcji obsługiwanych w warstwie premium magazyn maszyn wirtualnych z włączoną:

* **Zestaw dostępności**

    Korzystając z przykładu maszyny wirtualne z serii DS, można dodać maszyny Wirtualnej serii DS usługi w chmurze, która ma tylko maszyny wirtualne serii DS. Nie należy dodawać maszyny wirtualne z serii DS do istniejącej usługi w chmurze o typie innym niż maszyny wirtualne z serii DS. Do nowej usługi w chmurze, który jest uruchamiany tylko maszyny wirtualne serii DS, należy przeprowadzić migrację istniejących wirtualnych dysków twardych. Jeśli chcesz użyć tego samego wirtualnego adresu IP dla usługi w chmurze nowe, który jest hostem maszyny wirtualne serii DS, użyj [zastrzeżone adresy IP](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

* **Dysk systemu operacyjnego**

    Maszyny Wirtualnej usługi Premium Storage można skonfigurować do używania w warstwie premium lub dysk standardowy system operacyjny. Aby uzyskać najlepsze wyniki zaleca się przy użyciu dysku systemu operacyjnego na podstawie magazynu w warstwie Premium.

* **Dyski danych**

    W tej samej maszyny Wirtualnej — wersja Premium Storage można użyć disks premium i standardowa. Dzięki usłudze Premium Storage można aprowizować maszynę Wirtualną i dołączyć kilka dysków trwałych danych do maszyny Wirtualnej. Jeśli to konieczne w celu zwiększenia pojemności i wydajności woluminu, można stripe dla usługi dysków.

    > [!NOTE]
    > Jeśli stripe dysków magazynu premium storage danych przy użyciu [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx), skonfigurować pod kątem funkcji miejsca do magazynowania z 1 kolumnę dla każdego dysku, którego używasz. W przeciwnym razie ogólną wydajność rozłożone wolumin może być krótszy niż oczekiwano z powodu nierówna Dystrybucja ruchu między dyskami. Domyślnie w Menedżerze serwera można skonfigurować kolumny dla maksymalnie 8 dysków. Jeśli masz więcej niż 8 dysków, należy utworzyć wolumin za pomocą programu PowerShell. Ręcznie określ liczbę kolumn. W przeciwnym razie interfejsu użytkownika Menedżera serwera w dalszym ciągu używać 8 kolumn, nawet jeśli masz więcej dysków. Na przykład jeśli masz 32 dysków w zestawie pojedynczej usługi stripe, należy określić 32 kolumn. Aby określić liczbę kolumn w dysk wirtualny wykorzystuje [New-VirtualDisk](https://technet.microsoft.com/library/hh848643.aspx) polecenia cmdlet programu PowerShell, użyj *NumberOfColumns* parametru. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania — omówienie](https://technet.microsoft.com/library/hh831739.aspx) i [— często zadawane pytania dla miejsca do magazynowania magazynu](https://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

* **Pamięć podręczna**

    Virtual Machines (VMs) obsługujące usługę Premium Storage ma unikatową możliwość buforowania wyższego poziomu przepływności oraz zmniejszenie opóźnienia. Ich możliwości buforowania przekracza bazowego wydajność dysku usługi premium storage. Nie wszystkie maszyny wirtualne obsługę buforowania, więc zapoznaj się z tematem specyfikacje maszyny Wirtualnej rozmiarów maszyn wirtualnych interesuje Cię Aby uzyskać więcej informacji.  Maszyny wirtualne, które obsługują pamięć podręczna będzie to zasygnalizować w ich spec z pomiarem "Maksymalna przepływność magazynu buforowanego i tymczasowego".  Są one również określane bezpośrednio pod tytułem maszyny Wirtualnej.
    
    Za pomocą pamięci podręcznej, można ustawić dysku zasady na dysków magazynu premium storage do buforowania **tylko do odczytu**, **ReadWrite**, lub **Brak**. Dysk domyślny, zasady buforowania jest **tylko do odczytu** dla wszystkich dysków danych w warstwie premium, a **ReadWrite** dla dysków systemu operacyjnego. Aby uzyskać optymalną wydajność dla swojej aplikacji należy Pamiętaj, aby ustawienie odpowiedniej pamięci podręcznej. 
    
    Jako przykład dla dysków z danymi przy odczycie czy tylko do odczytu, takich jak pliki danych programu SQL Server, ustawia zasad buforowania **tylko do odczytu**. Dyski danych zapisu przy odczycie czy tylko do zapisu, takich jak pliki dziennika programu SQL Server, Ustaw zasady buforowania **Brak**. 
    
    Aby dowiedzieć się więcej na temat optymalizowania projektu dzięki usłudze Premium Storage, zobacz [projektowanie pod kątem wydajności Usługa Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analiza**

    Aby analizować wydajność maszyny Wirtualnej przy użyciu dysków w usłudze Premium Storage, włączyć diagnostykę maszyn wirtualnych w [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [monitorowanie maszyny Wirtualnej platformy Azure za pomocą rozszerzenia diagnostyki Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Aby wyświetlić wydajność dysku, użyj narzędzi opartych na systemie operacyjnym, takich jak [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) dla maszyn wirtualnych Windows i [iostat](http://linux.die.net/man/1/iostat) polecenia maszyn wirtualnych systemu Linux.

* **Limity skalowania maszyny Wirtualnej i wydajność**

    Każda usługa Premium Storage obsługiwana rozmiar maszyny Wirtualnej ma limity skalowania i specyfikacje wydajności operacji We/Wy, przepustowości i liczby dysków, które mogą być dołączane na maszynę Wirtualną. Korzystając z dysków magazynu premium storage przy użyciu maszyn wirtualnych, upewnij się, że istnieje wystarczająca operacje We/Wy i przepustowości na maszynie Wirtualnej stacji dysków ruchu.

    Na przykład STANDARD_DS1 maszyna wirtualna ma dedykowaną przepustowość 32 MB/s dla ruchu sieciowego dysku magazynu premium. Dysk magazynu premium P10 może zapewnić przepustowości 100 MB/s. Jeśli dysk magazynu premium P10 jest dołączony do tej maszyny Wirtualnej, ponieważ tylko przekazywane do 32 MB/s. Nie można go używać maksymalną 100 MB/s zapewniające dla dysku P10.

    Obecnie największą maszynę Wirtualną z serii DS jest standardowa_ds15_v2. Standardowa_ds15_v2 może zapewnić maksymalnie 960 MB/s dla wszystkich dysków. Największą maszynę Wirtualną z serii GS jest Standard_GS5. Standard_GS5 zapewnia do 2000 MB na sekundę na wszystkich dyskach.

    Limity te dotyczą tylko ruch dyskowy. Te limity nie uwzględniają trafień w pamięci podręcznej i ruchu sieciowego. Oddzielne przepustowość jest dostępna dla ruchu sieciowego maszyny Wirtualnej. Przepustowości dla ruchu sieciowego różni się od dedykowaną przepustowość, które korzystają z dysków magazynu premium storage.

    Aby uzyskać najbardziej aktualne informacje dotyczące maksymalnej operacje We/Wy i przepływność (przepustowość) dla maszyn wirtualnych z obsługiwanych przez usługę Premium Storage, zobacz [rozmiarów maszyn wirtualnych Windows](../articles/virtual-machines/windows/sizes.md) lub [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md).

    Aby uzyskać więcej informacji na temat dysków magazynu premium storage i ich ograniczeń operacje We/Wy i przepływność zobacz tabelę w następnej sekcji.

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności
W tej sekcji opisano cele skalowalności i wydajności, które należy uwzględnić podczas Użyj usługi Premium Storage.

Kont usługi Premium storage ma następujące cele skalowalności:

| Łączna liczba kont pojemności | Całkowitej przepustowości dla konta magazynu lokalnie nadmiarowego |
| --- | --- | 
| Pojemność dysku: 35 TB <br>Pojemność migawki: 10 TB | Się do 50 gigabity na sekundę dla ruchu przychodzącego<sup>1</sup> + wychodzącego<sup>2</sup> |

<sup>1</sup> wszystkich danych (żądań), które są wysyłane do konta magazynu

<sup>2</sup> wszystkich danych (żądań), które są odbierane z konta magazynu

Aby uzyskać więcej informacji, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](../articles/storage/common/storage-scalability-targets.md).

Jeśli używasz kont usługi premium storage dla dysków niezarządzanych aplikacji przekracza cele skalowalności z jednego konta magazynu, można migrować do usługi managed disks. Jeśli nie chcesz przeprowadzić migrację do usługi managed disks, tworzenie aplikacji na używanie wielu kont magazynu. Następnie podzielić dane na tych kontach magazynu. Na przykład jeśli chcesz dołączyć dyski 51 – TB na wielu maszynach wirtualnych, rozkładają się je na dwa konta magazynu. 35 TB jest limit dla konta magazynu premium jednego. Upewnij się, że konto magazynu premium pojedynczego nigdy nie ma więcej niż 35 TB aprowizowanego dysku.

### <a name="premium-storage-disk-limits"></a>Limity dysku usługi Premium Storage
Podczas aprowizowania dysku magazynu premium rozmiar dysku określa maksymalny operacje We/Wy i przepływność (przepustowość). Platforma Azure oferuje osiem typów usług ogólnie dostępnych dysków magazynu premium storage: P4 (tylko zarządzane dyski), P6 (tylko zarządzane dyski), P10, P15 (tylko zarządzany dysków), P20, P30, P40 i P50. A także trzech rozmiarach dysków (wersja zapoznawcza): P60 P70 i P80. Każdy typ dysku magazynu premium storage ma określone limity dla operacji We/Wy i przepływność. W poniższej tabeli opisano limity dla typów dysków:

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Typ magazynu dysków Premium  | P4    | P6    | P10    | P15    | P20    | P30              | P40             | P50             | P60 *            | P70 *               | P80 *               |
|---------------------|-------|-------|--------|--------|--------|------------------|-----------------|-----------------|-----------------|--------------------|--------------------|
| Rozmiar dysku           | 32 GiB| 64 GiB| 128 GiB| 256 GiB| 512 GiB| 1024 GiB (1 TiB) | 2048 GiB (2 TiB)| 4095 GiB (4 TiB)| 8192 GiB (8 TiB)| 16,384 GiB (16 TiB)| 32 767 giB (32 TiB)|
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500    | 1100   | 2300   | 5000             | 7500            | 7500            | 12 500          | 15 000             | 20,000             |
| Przepływność na dysk | 25 MB na sekundę | 50 MB na sekundę | 100 MB na sekundę | 125 MB na sekundę | 150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę | 480 MB na sekundę | 750 MB na sekundę | 750 MB na sekundę |

> [!NOTE]
> Upewnij się, że wystarczającą przepustowość jest dostępna na maszynie Wirtualnej, aby ruch dyskowy dysku, zgodnie z opisem w [obsługiwane maszyny wirtualne](#supported-vms). W przeciwnym razie swoje przepływność dysku i operacje We/Wy jest ograniczony do niższych wartości. Maksymalna przepływność i operacje We/Wy są oparte na limity maszyn wirtualnych, nie na limity dysku, opisano w powyższej tabeli.  
> Azure został zaprojektowany tak platformy usługi Premium Storage jako równoległe na wielką skalę. Projektowanie aplikacji wielowątkowych pomogą osiągnąć wartości docelowej o wysokiej wydajności, oferowany w większych rozmiarów dysków.

Poniżej przedstawiono niektóre ważne kwestie, aby dowiedzieć się o cele dotyczące skalowalności i wydajności Usługa Premium Storage:

* **Aprowizowana pojemność i wydajność**

    Podczas aprowizowania dysku magazynu premium, w przeciwieństwie do magazynu w warstwie standardowa jest gwarantowana pojemność, operacje We/Wy i przepływność dysku. Na przykład jeśli tworzysz P50 dysku, platforma Azure udostępnia 4095 GB pojemności, 7500 operacji We/Wy i 250-MB/s przepływności dla tego dysku. Aplikacja może używać w całości lub części pojemności i wydajności. Dyski SSD w warstwie Premium są przeznaczone do dostarczania element docelowy wydajności przez 99,9% czasu.

* **Rozmiar dysku**

    Usługi Azure maps rozmiar dysku (z zaokrągleniem) do najbliższej premium storage dysku opcji określonych w tabeli w poprzedniej sekcji. Na przykład dysk o rozmiarze 100 GB jest klasyfikowany jako opcję P10. Można wykonywać maksymalnie 500 operacji We/Wy z maksymalnie 100-MB/s przepływności. Podobnie dysk o rozmiarze, który 400 GB zostanie sklasyfikowany jako P20. Można wykonywać maksymalnie 2300 operacje We/Wy, za pomocą 150-MB/s przepływności.

    > [!NOTE]
    > Można łatwo zwiększyć rozmiaru istniejących dysków. Na przykład można zwiększyć rozmiar dysku 30 GB do 128 GB lub nawet do 1 TB. Możesz też przekonwertować dysku P20 na dysk P30, ponieważ będzie potrzebny więcej pojemności lub więcej operacji We/Wy i przepływność. 

* **Rozmiar operacji We/Wy**

    Rozmiar operacji We/Wy to 256 KB. Transferowanych danych jest mniejsza niż 256 KB, jest uznawane za 1 jednostkę operacji We/Wy. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KB. Na przykład KB 1100 operacji We/Wy jest traktowana jako 5 jednostek we/wy.

* **Przepływność**

    Limit przepływności obejmuje zapisywania na dysku i zawiera operacji odczytu na dysku, które nie są obsługiwane z pamięci podręcznej. Na przykład na dysku P10 jest przepływność 100-MB/s na dysk. W poniższej tabeli przedstawiono przykładowe prawidłowe przepływności dla dysku P10:

    | Maksymalna przepływność na dysk P10 | Inne niż pamięć podręczna odczytuje z dysku | Inne niż pamięć podręczna zapisu na dysku |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Trafienia w pamięci podręcznej**

    Trafienia w pamięci podręcznej nie są ograniczone przez przydzielonego na SEKUNDĘ lub przepływności dysku. Na przykład, kiedy używać dysk z danymi **tylko do odczytu** ustawienie pamięci podręcznej na maszynie Wirtualnej, która jest obsługiwana przez usługę Premium Storage odczytów, które są udostępniane z pamięci podręcznej nie podlegają operacji We/Wy i przepływność limity dysku. Jeśli obciążenie dysku jest przeważnie odczyty, możesz otrzymać bardzo wysokiej przepływności. Pamięć podręczna jest obowiązywać osobne operacje We/Wy i ograniczeniami przepływność na maszynę Wirtualną poziomu, na podstawie rozmiaru maszyny Wirtualnej. Maszyny wirtualne z serii DS ma około 4000 operacje We/Wy i 33-MB/s przepływności na rdzeń procesora, pamięci podręcznej i lokalny dysk SSD operacji We/Wy. Maszyny wirtualne z serii GS mają limit 5000 operacji We/Wy i 50-MB/s przepływności na rdzeń procesora, pamięci podręcznej i lokalny dysk SSD operacji We/Wy.

## <a name="throttling"></a>Ograniczanie przepływności

Ograniczanie mogą wystąpić, jeśli Twoja aplikacja na SEKUNDĘ lub przepływności przekracza limit przydzielonego dysku magazynu w warstwie premium. Także ograniczenie może wystąpić, jeśli ruch sieciowy całkowitego miejsca na dysku dla wszystkich dysków na maszynie Wirtualnej przekracza limit przepustowości dysku dostępne dla maszyny Wirtualnej. Aby uniknąć ograniczania przepustowości, zaleca się ograniczenie liczby oczekujących żądań We/Wy dysku. Użyj limit na podstawie cele dotyczące skalowalności i wydajności dla dysku, które zostały aprowizowane i przepustowości dysku dostępne dla maszyny Wirtualnej.  

Aplikację można osiągnąć najniższe opóźnienie, podczas zaprojektowano w celu uniknięcia ograniczania przepustowości. Jednakże, jeśli liczba oczekujących żądań We/Wy na dysku jest za mały, aplikacji nie mogą korzystać z maksymalna liczba IOPS i poziomów przepływności, które są dostępne dla dysku.

W poniższych przykładach pokazano sposób obliczania ograniczania poziomów. Wszystkie obliczenia są oparte na rozmiar jednostki we/wy wynoszący 256 KB.

### <a name="example-1"></a>Przykład 1

W ciągu jednej sekundy na dysku P10, aplikacji został przetworzony 495 jednostki we/wy o rozmiarze 16 KB. Jednostki We/Wy są liczone jako 495 operacje We/Wy. Jeśli spróbujesz drugi 2 MB operacji We/Wy w tym samym, łączna liczba operacji We/Wy jednostek jest równa 495 + 8 operacje We/Wy. Jest to spowodowane 2 MB operacji We/Wy = 256 KB-2048 KB = 8 operacji We/Wy, jednostki, gdy rozmiar jednostki we/wy to 256 KB. Ponieważ sumę 495 + 8 limit 500 operacji We/Wy dysku, ograniczanie występuje.

### <a name="example-2"></a>Przykład 2

Aplikacja została przetworzona 400 jednostek we/wy o rozmiarze 256 KB na dysku P10. Jest całkowitej przepustowości (400 &#215; 256) / 1024 KB = 100 MB/s. Dysku P10 ma limit przepustowości 100 MB/s. Jeśli Twoja aplikacja próbuje wykonać więcej operacji We/Wy w tym sekundy, jest ograniczona, ponieważ przekracza on limit przydzielone.

### <a name="example-3"></a>Przykład 3

Masz maszynę Wirtualną DS4 dwa dyski P30 dołączone. Każdy dysk P30 jest zdolny do 200-MB/s przepływności. Jednak DS4 maszyny Wirtualnej ma pojemność przepustowości całkowitego miejsca na dysku wynoszący 256 MB/s. Nie może sterować obydwa dyski dołączone do maksymalnej przepływności na tej maszynie Wirtualnej DS4 w tym samym czasie. Aby rozwiązać ten problem, może wytrzymać ruchu 200 MB/s na dysk i 56 MB/s na innym dysku. Jeśli suma dysku ruch sieciowy przechodzi przez 256 MB/s, jest ograniczany ruch dyskowy.

> [!NOTE]
> Jeśli ruch sieciowy dysku składa się przede wszystkim małych rozmiarów operacji We/Wy, prawdopodobnie aplikacja osiągnie limit operacji We/Wy przed limit przepływności. Jednak jeśli ruch dyskowy składa się przede wszystkim rozmiarów operacji We/Wy, prawdopodobnie aplikacja osiągnie limit przepływności po pierwsze, zamiast limit operacji We/Wy. Za pomocą optymalny rozmiar operacji We/Wy, mogą maksymalizować aplikacji operacje We/Wy i przepływność. Ponadto można ograniczyć liczbę oczekujących żądań We/Wy dysku.

Aby uzyskać więcej informacji na temat Projektowanie pod kątem wysokiej wydajności przy użyciu usługi Premium Storage, zobacz [projektowanie pod kątem wydajności Usługa Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Migawki i obiektu Blob kopiowania

Z usługą Magazyn plików wirtualnego dysku twardego jest stronicowych obiektów blob. Można wykonać migawki stronicowe obiekty BLOB i skopiuj je do innej lokalizacji, takich jak do innego konta magazynu.

### <a name="unmanaged-disks"></a>Dyski niezarządzane

Tworzenie [migawek przyrostowych](../articles/virtual-machines/linux/incremental-snapshots.md) for niezarządzanego premium dysków w taki sam sposób, możesz za pomocą migawek magazynu w warstwie standardowa. Usługa Premium Storage obsługuje tylko lokalnie nadmiarowym jako opcję replikacji. Firma Microsoft zaleca tworzenie migawek, a następnie skopiować migawki na konto magazynu geograficznie nadmiarowego magazynu w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [Opcje nadmiarowości usługi Azure Storage](../articles/storage/common/storage-redundancy.md).

Jeśli dysk jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API na dysku nie są dozwolone. Na przykład nie można wykonać [obiektu Blob kopiowania](/rest/api/storageservices/Copy-Blob) operacji na tym obiekcie blob, jeśli dysk jest podłączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę tego obiektu blob za pomocą [wykonanie migawki obiektu Blob](/rest/api/storageservices/Snapshot-Blob) interfejsu API REST. Następnie wykonaj [obiektu Blob kopiowania](/rest/api/storageservices/Copy-Blob) migawki, aby skopiować dysk dołączony. Alternatywnie można odłączyć dysk, a następnie wykonaj wszelkie niezbędne operacje.

Poniższe limity mają zastosowanie do migawki obiektów blob magazynu w warstwie premium:

| Limit magazynu w warstwie Premium | Wartość |
| --- | --- |
| Maksymalna liczba migawek na obiekt blob | 100 |
| Pojemności konta magazynu dla migawki<br>(Obejmuje dane zawiera tylko migawki. Nie ma danych w obiekcie blob podstawowego.) | 10 TB |
| Minimalny czas między kolejnymi migawek | 10 minut |

Aby zachować geograficznie nadmiarowych kopii usługi migawek, można skopiować migawki z konta usługi premium storage do konta magazynu geograficznie nadmiarowego magazynu w warstwie standardowa przy użyciu narzędzia AzCopy lub obiektu Blob kopiowania. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../articles/storage/common/storage-use-azcopy.md) i [obiektu Blob kopiowania](/rest/api/storageservices/Copy-Blob).

Aby uzyskać szczegółowe informacje dotyczące wykonywania operacji REST względem stronicowe obiekty BLOB na koncie usługi premium storage, zobacz [operacji usługi za pomocą usługi Azure Premium Storage Blob](https://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Dyski zarządzane

Migawkę dysku zarządzanego jest tylko do odczytu kopię dysku zarządzanego. Migawki są przechowywane jako standardowa dysku zarządzanego. Obecnie [migawek przyrostowych](../articles/virtual-machines/windows/incremental-snapshots.md) nie są obsługiwane w przypadku dysków zarządzanych. Aby dowiedzieć się, jak utworzyć migawkę dysku zarządzanego, zobacz [tworzenie kopii wirtualnego dysku twardego przechowywanego jako zarządzana przez platformę Azure dysku przy użyciu migawek zarządzanych w Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) lub [tworzenie kopii wirtualnego dysku twardego przechowywanego jako zarządzana przez platformę Azure dysku przy użyciu zarządzanych migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Jeśli dysk zarządzany jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API na dysku nie są dozwolone. Na przykład nie można wygenerować sygnaturę dostępu współdzielonego (SAS), można wykonać operacji kopiowania, gdy dysk jest podłączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę dysku, a następnie wykonaj kopię migawki. Można także Odłącz dysk, a następnie wygenerować sygnatury dostępu Współdzielonego do wykonania tej operacji kopiowania.


## <a name="premium-storage-for-linux-vms"></a>Usługa Premium Storage dla maszyn wirtualnych systemu Linux
Ułatwia konfigurowanie maszyn wirtualnych systemu Linux w usłudze Premium Storage umożliwia następujące informacje:

Aby osiągnąć cele skalowalności w usłudze Premium Storage dla wszystkich dysków magazynu premium storage z pamięcią podręczną ustawione na **tylko do odczytu** lub **Brak**, należy wyłączyć "bariery", podczas instalacji systemu plików. Nie potrzebujesz barier w tym scenariuszu, ponieważ operacje zapisu do dysków magazynu premium storage są trwałe dla tych ustawień pamięci podręcznej Podczas żądania zapisu zakończy się pomyślnie, dane został zapisany w magazynie trwałym. Aby wyłączyć "bariery", użyj jednej z następujących metod. Wybierz jeden dla systemu plików:
  
* Aby uzyskać **reiserFS**, aby wyłączyć bariery, użyj `barrier=none` opcji instalacji. (Aby włączyć bariery, użyj `barrier=flush`.)
* Aby uzyskać **ext3/ext4**, aby wyłączyć bariery, użyj `barrier=0` opcji instalacji. (Aby włączyć bariery, użyj `barrier=1`.)
* Aby uzyskać **XFS**, aby wyłączyć bariery, użyj `nobarrier` opcji instalacji. (Aby włączyć bariery, użyj `barrier`.)
* Dla usługi premium storage dysków z pamięcią podręczną równa **ReadWrite**, Włącz barier w celu zapewnienia trwałości zapisu.
* Dla etykiety woluminu utrwalić po ponownym uruchomieniu maszyny Wirtualnej należy zaktualizować/etc/fstab z odwołaniami powszechnie Unikatowy identyfikator (UUID) na dyskach. Aby uzyskać więcej informacji, zobacz [Dodawanie dysku zarządzanego do maszyny Wirtualnej z systemem Linux](../articles/virtual-machines/linux/add-disk.md).

Poniższe dystrybucje systemu Linux zostały zweryfikowane dla usługi Azure Premium Storage. Gwarantujące wysoką wydajność i stabilność dzięki usłudze Premium Storage zaleca się uaktualnienie maszyny wirtualne, jedną z tych wersji, co najmniej (lub nowszy). Najnowsze usługi LIS (Linux Integration), wersja 4.0, niektóre wersje wymagają dla platformy Azure. Aby pobrać i zainstalować dystrybucji, postępuj zgodnie z łączy wymienionych w poniższej tabeli. Obrazy możemy dodać do listy, podczas wykonywania sprawdzania poprawności. Pamiętaj, że nasze walidacji pokazują, że wydajność zmienia się dla każdego obrazu. Wydajność zależy od charakterystyki i ustawienia obrazu. Obrazy różnych dostosowanych do różnych rodzajów obciążeń.

| Dystrybucja | Wersja | Obsługiwane jądra | Szczegóły |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> SUSE — w systemie sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 wymagane](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Zobacz uwagi w następnej sekcji* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 zalecane](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Zobacz uwagi w następnej sekcji* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 lub RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 lub RHCK z[LIS 4.1 lub nowszym](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 lub RHCK z[LIS 4.1 lub nowszym](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Sterowników LIS systemie OpenLogic CentOS

Jeśli są uruchomione maszyny wirtualne CentOS OpenLogic, uruchom następujące polecenie, aby zainstalować najnowsze sterowniki:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Aby aktywować nowe sterowniki, uruchom ponownie maszynę Wirtualną.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Korzystając z usługi Premium Storage, zastosuj następujące zagadnienia dotyczące rozliczeń:

* **Rozmiar magazynu Premium disk i obiektów blob**

    Rozliczenia dla dysku magazynu premium lub obiekt blob jest zależna od aprowizowany rozmiar dysku lub obiektu blob. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej opcji dysku magazynu premium. Aby uzyskać szczegółowe informacje, zobacz tabelę w [cele dotyczące skalowalności i wydajności](#scalability-and-performance-targets). Każdy dysk mapowany rozmiar dysku obsługiwane i w związku z tym jest naliczana opłata. Rozliczenia dla dowolnego dysku jest proporcjonalnie do liczby godzin przy użyciu miesięczna oferta usługi Premium Storage. Na przykład jeśli zainicjowano obsługę administracyjną dysku P10, a następnie usunięte 20 godzin, stosowana jest stawka dla oferty P10 jest naliczany proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku lub operacji We/Wy i przepływność używana.

* **Usługa unmanaged disks w warstwie Premium migawek**

    Migawki dysków niezarządzanych w warstwie premium są rozliczane za dodatkową pojemność posługują się migawki. Aby uzyskać więcej informacji na temat migawek, zobacz [utworzyć migawkę obiektu blob](/rest/api/storageservices/Snapshot-Blob).

* **Migawki dysków zarządzanych w warstwie Premium**

    Migawki dysków zarządzanych jest tylko do odczytu kopię dysku. Dysk jest przechowywany jako standardowa dysku zarządzanego. Migawki koszty takie same, jak standardowy dysk zarządzany. Na przykład jeśli wykonujesz migawkę dysku zarządzanego 128 GB w warstwie premium, koszt migawki jest odpowiednikiem standardowego dysku zarządzanego 128 GB.  

* **Wychodzące transfery danych**

    [Wychodzące transfery danych](https://azure.microsoft.com/pricing/details/data-transfers/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

Aby uzyskać szczegółowe informacje na temat cen usługi Premium Storage, Usługa Premium Storage jest obsługiwana na maszynach wirtualnych i dysków zarządzanych zobacz następujące artykuły:

* [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Ceny usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Obsługa usługi Azure Backup

Do odzyskiwania po awarii regionalnej możesz należy wykonać kopię zapasową dysków maszyn wirtualnych w różnych regionach za pomocą [kopia zapasowa Azure](../articles/backup/backup-introduction-to-azure-backup.md) i konto magazynu GRS jako magazyn kopii zapasowych.

Aby utworzyć zadanie tworzenia kopii zapasowej z kopii zapasowych opartych na czasie, łatwe przywracanie maszyny Wirtualnej i zasad przechowywania kopii zapasowych, użyj usługi Azure Backup. Narzędzie Kopia zapasowa służy zarówno z dysków niezarządzanych i zarządzanych. Aby uzyskać więcej informacji, zobacz [usługi Azure Backup dla maszyn wirtualnych z dyskami niezarządzanymi](../articles/backup/backup-azure-vms-first-look-arm.md) i [usługi Azure Backup dla maszyn wirtualnych z dyskami zarządzanymi](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o usłudze Premium Storage zobacz następujące artykuły.
