# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Magazyn w warstwie Premium wysokiej wydajności i zarządzane dyski dla maszyn wirtualnych
Usługa Azure Premium Storage oferuje obsługę dysków o wysokiej wydajności i małych opóźnieniach maszynach wirtualnych (VM) z wejścia/wyjścia (We/Wy)-intensywnych obciążeń. Dyski maszyn wirtualnych, które używają magazyn w warstwie Premium przechowywania danych na dyskach półprzewodnikowych (SSD). Podjęcie wykorzystują szybkości i wydajności dysków w warstwie premium magazynu, można migrować istniejące dyski maszyny Wirtualnej do magazyn w warstwie Premium.

Na platformie Azure możesz dołączyć kilka dysków w warstwie premium magazynu do maszyny Wirtualnej. Za pomocą wielu dysków daje aplikacji maksymalnie 256 TB pamięci masowej dla maszyny Wirtualnej. Z magazyn w warstwie Premium aplikacje można osiągnąć 80 000 operacji We/Wy na sekundę (IOPS) dla maszyny Wirtualnej i przepływność dysku, maksymalnie 2000 MB na sekundę (MB/s) dla maszyny Wirtualnej. Operacje odczytu zapewniają bardzo małych opóźnień.

Z magazyn w warstwie Premium Azure oferuje możliwość naprawdę przyrostu i shift wymagających przedsiębiorstwa aplikacjach, takich jak farm Dynamics AX, Dynamics CRM, Exchange Server, SAP Business pakietu i programu SharePoint do chmury. Można uruchomić obciążeń intensywnie wydajności bazy danych w aplikacjach, takich jak SQL Server, Oracle, bazy danych MongoDB, MySQL i pamięci podręcznej Redis, wymagających wysoką wydajność i małe opóźnienia.

> [!NOTE]
> Aby uzyskać najlepszą wydajność aplikacji zaleca się przeprowadzenie migracji dysku maszyny Wirtualnej, który wymaga wysoką wartość IOPS dla magazyn w warstwie Premium. Jeśli dysk nie wymaga wysokiej IOPS, może pomóc limit kosztów, przechowując go w usłudze Azure standard Storage. W magazynie standardowa maszyna wirtualna dysku dane są przechowywane na dysków twardych (HDD) zamiast na dyskach SSD.
> 

Platforma Azure oferuje dwa sposoby tworzenia dysków w warstwie premium magazynu dla maszyn wirtualnych:

* **Dyski niezarządzane**

    Oryginalnej metody ma używać dysków niezarządzane. Niezarządzane dysku zarządzania kont magazynu, które służy do przechowywania plików wirtualnego dysku twardego (VHD), które odpowiadają dysków maszyny Wirtualnej. Pliki VHD są przechowywane jako stronicowe obiekty BLOB na kontach magazynu Azure. 

* **Dyski zarządzane**

    Po wybraniu [dysków zarządzanych Azure](../articles/virtual-machines/windows/managed-disks-overview.md), Azure zarządza kontami magazynu, których używasz dla dysków maszyny Wirtualnej. Należy określić typ dysku (Premium lub Standard) oraz rozmiar dysku, które są potrzebne. Azure tworzy i którymi zarządza dysku dla Ciebie. Nie trzeba martwić umieszczenie dyski w wielu kont magazynu, aby upewnić się, komputery pozostaną w granicach skalowalność dla kont magazynu. Azure obsługuje który dla Ciebie.

Zaleca się wybranie zarządzanych dysków, aby skorzystać z ich wiele funkcji.

Aby rozpocząć korzystanie z magazynu Premium [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 

Aby uzyskać informacje na temat migracji istniejących maszyn wirtualnych do magazyn w warstwie Premium, zobacz [Konwertuj Maszynę wirtualną systemu Windows z dysków niezarządzanych do zarządzanych dysków](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) lub [Konwertuj Maszynę wirtualną systemu Linux z dysków niezarządzanych do zarządzanych dysków](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Magazyn w warstwie Premium jest dostępna w większości regionów. Aby uzyskać listę dostępnych regionów, zobacz wiersz **Magazyn dyskowy** w [produkty Azure dostępne według regionu](https://azure.microsoft.com/regions/#services).
> 

## <a name="features"></a>Funkcje

Oto niektóre z funkcji Premium Storage:

* **Dyski magazynu w warstwie Premium**

    Magazyn w warstwie Premium obsługuje dyski maszyn wirtualnych, które można dołączyć do określonego rozmiaru serii maszyn wirtualnych. Magazyn w warstwie Premium obsługuje serii DS, DSv2 serii GS-series, Ls serii, serie Fs i Esv3 serii maszyn wirtualnych. Masz do wyboru rozmiary siedmiu dysków: P4 (32 GB) P6 (64 GB), P10 (128 MB), P20 (512 GB), P30 (1024 GB), P40 (2048 GB), P50 (4095 GB). P4 i rozmiary dysków P6 są jeszcze obsługiwane tylko w przypadku dysków zarządzanych. Rozmiar każdego dysku ma specyfikacjami wydajności. W zależności od wymagań aplikacji można dołączyć jeden lub więcej dysków do maszyny Wirtualnej. Specyfikacje szczegółowo opisano [magazyn w warstwie Premium cele wydajności i skalowalności](#scalability-and-performance-targets).

* **Stronicowe — wersja Premium**

    Magazyn w warstwie Premium obsługuje stronicowych obiektów blob. Używa stronicowych obiektów blob do przechowywania dysków trwałe, niezarządzany dla maszyn wirtualnych w warstwie Premium Storage. W przeciwieństwie do standardowego magazynu Azure magazyn w warstwie Premium nie obsługuje blokowe obiekty BLOB, Dołącz obiektów blob, plików, tabel lub kolejek. Stronicowe Premium obsługuje sześciu rozmiarów z P10 P50 i P60 (8191GiB). Być dołączane jako dyski maszyny Wirtualnej — wersja Premium P60 stronicowych obiektów blob nie jest obsługiwane. 

    Dowolny obiekt umieszczony na koncie magazynu premium będzie stronicowych obiektów blob. Stronicowych obiektów blob przyciąganie do jednego z obsługiwanych rozmiarów elastycznie. Jest to, dlaczego konto magazynu w warstwie premium nie jest przeznaczona do używany do przechowywania obiektów blob niewielki rozmiar.

* **Konto magazynu w warstwie Premium**

    Aby rozpocząć korzystanie z magazyn w warstwie Premium, Utwórz konto magazynu premium dla niezarządzanego dysków. W [portalu Azure](https://portal.azure.com), aby utworzyć konto magazynu premium, wybierz **Premium** warstwę wydajności. Wybierz **magazyn lokalnie nadmiarowy (LRS)** opcji replikacji. Można również tworzyć konta magazynu premium ustawiając warstwę wydajności **Premium_LRS**. Aby zmienić warstwę wydajności, użyj jednej z następujących metod:
     
    - [Programu PowerShell dla usługi Azure Storage](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure CLI magazynem systemu Azure](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure API REST dostawcy zasobów magazynu](https://docs.microsoft.com/rest/api/storagerp) (w przypadku wdrożenia usługi Azure Resource Manager) lub jednej z bibliotek klienta dostawcy zasobów usługi Azure Storage

    Aby dowiedzieć się więcej na temat limitów konta magazynu premium, zobacz [magazyn w warstwie Premium cele wydajności i skalowalności](#premium-storage-scalability-and-performance-targets).

* **Magazyn lokalnie nadmiarowy — wersja Premium**

    Konto magazynu premium obsługuje tylko lokalnie nadmiarowego magazynu jako opcję replikacji. Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych w pojedynczym regionie. Regionalnej awarii, musisz należy wykonać kopię zapasową dysków maszyny Wirtualnej w innym regionie przy użyciu [kopia zapasowa Azure](../articles/backup/backup-introduction-to-azure-backup.md). Możesz również użyć konta magazynu geograficznie nadmiarowego (GRS) jako magazynu kopii zapasowych. 

    Azure używa konta magazynu jako kontener dla niezarządzanego dysków. Podczas tworzenia maszyny Wirtualnej platformy Azure obsługuje magazyn w warstwie Premium z dyskami niezarządzane, a następnie wybierz konto magazynu w warstwie premium, systemu operacyjnego i dyski danych są przechowywane na tym koncie magazynu.

## <a name="supported-vms"></a>Obsługiwane maszyny wirtualne

Magazyn w warstwie Premium obsługuje B-series, serii DS, DSv2-series, DSv3 serii GS-series, Ls serii, serie M i Fs serii maszyn wirtualnych. Z tych typów maszyny Wirtualnej służy dyski magazynu standard i premium. Nie można używać dysków premium magazynu serii maszyn wirtualnych, które nie są Premium zgodnych z magazynu.


Aby uzyskać informacje o typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Windows, zobacz [Windows VM sizes (Rozmiary maszyn wirtualnych z systemem Windows)](../articles/virtual-machines/windows/sizes.md). Aby uzyskać informacje o typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Linux, zobacz [Linux VM sizes (Rozmiary maszyn wirtualnych z systemem Linux)](../articles/virtual-machines/linux/sizes.md).

Są to niektóre funkcje serii GS: seria DS, DSv2 serii, serie Ls i Fs serii maszyn wirtualnych:

* **Usługi w chmurze**

    Maszyny wirtualne z serii DS można dodać do usługi w chmurze, zawierający tylko maszyny wirtualne serii DS. Maszyny wirtualne z serii DS nie należy dodawać do istniejącej usługi w chmurze o typie innym niż maszyny wirtualne z serii DS. Istniejące dyski VHD można migrować do nowej usługi w chmurze, uruchamiana tylko maszyny wirtualne serii DS. Jeśli chcesz użyć tego samego wirtualnego adresu IP dla nowej usługi w chmurze hostujący maszyny wirtualne z serii DS, użyj [zastrzeżonych adresów IP](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). GS-series maszyny wirtualne można dodać do istniejącej usługi chmury, zawierający tylko GS-series maszyn wirtualnych.

* **Dysk systemu operacyjnego**

    Magazyn maszyny Wirtualnej — wersja Premium można skonfigurować, premium lub dysku standardowym systemie operacyjnym. Aby uzyskać najlepsze wyniki zaleca się przy użyciu dysku systemu operacyjnego magazyn w warstwie Premium.

* **Dyski danych**

    W tej samej maszyny Wirtualnej magazynu Premium służy premium i standardowa dysków. Magazyn w warstwie Premium możesz udostępnić Maszynę wirtualną i dołączyć kilka dysków danych trwałych do maszyny Wirtualnej. Jeśli to konieczne, aby zwiększyć pojemność i wydajność woluminu, można paskowych na dyskach.

    > [!NOTE]
    > Jeśli paskowych dysków w warstwie premium magazynu danych przy użyciu [miejsca do magazynowania](http://technet.microsoft.com/library/hh831739.aspx), skonfiguruj miejsca do magazynowania za pomocą 1 kolumny dla każdego dysku, którego używasz. W przeciwnym razie ogólną wydajność woluminów rozłożonych może być niższe niż oczekiwano z powodu nierówna Dystrybucja ruchu między dyskami. Domyślnie w Menedżerze serwera można skonfigurować kolumn dla maksymalnie 8 dysków. Jeśli masz więcej niż 8 dysków, należy utworzyć wolumin za pomocą programu PowerShell. Ręcznie określ liczbę kolumn. W przeciwnym razie interfejsu użytkownika Menedżera serwera w dalszym ciągu używać 8 kolumn, nawet, jeśli masz więcej dysków. Na przykład jeśli masz 32 dysków w jednej rozłożony określić 32 kolumny. Aby określić liczbę kolumn używa dysku wirtualnego, [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) polecenia cmdlet programu PowerShell, użyj *NumberOfColumns* parametru. Aby uzyskać więcej informacji, zobacz [omówienie funkcji miejsca do magazynowania](http://technet.microsoft.com/library/hh831739.aspx) i [miejsca do magazynowania — często zadawane pytania](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    Maszyny wirtualne w serii rozmiar, który obsługuje magazyn w warstwie Premium ma unikatowy możliwość buforowania wysokiej przepustowości i opóźnień. Możliwość buforowania przekracza podstawowej wydajności dysku magazynu premium. Można ustawić dysku zasad na dyskach magazynu premium do buforowania **tylko do odczytu**, **ReadWrite**, lub **Brak**. Dysk domyślne zasad buforowania jest **tylko do odczytu** dla wszystkich dysków danych — warstwa premium i **ReadWrite** dysków systemu operacyjnego. Aby uzyskać optymalną wydajność aplikacji użyj ustawienia poprawne pamięci podręcznej. Na przykład dla dysków z danymi ciężki odczytu lub w trybie tylko do odczytu, takich jak pliki danych programu SQL Server, ustaw dysku zasad do buforowania **tylko do odczytu**. Dla danych intensywnie zapisu lub w trybie tylko do zapisu dysków, takich jak pliki dziennika programu SQL Server, ustaw dysku zasad do buforowania **Brak**. Aby dowiedzieć się więcej na temat optymalizacji projektu z magazyn w warstwie Premium, zobacz [projektu dla wydajności z magazyn w warstwie Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analiza**

    Do analizy wydajności maszyny Wirtualnej przy użyciu dysków w warstwie Premium Storage, Włącz diagnostyki maszyny Wirtualnej w [portalu Azure](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [maszyny Wirtualnej Azure monitorowania z rozszerzeniem diagnostyki Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Aby wyświetlić wydajność dysku, użyj narzędzi systemu operacyjnego, takich jak [Monitor wydajności systemu Windows](https://technet.microsoft.com/library/cc749249.aspx) dla maszyn wirtualnych systemu Windows i [iostat](http://linux.die.net/man/1/iostat) polecenia dla maszyn wirtualnych systemu Linux.

* **Limity skalowania maszyny Wirtualnej i wydajność**

    Każdy magazyn w warstwie Premium, obsługiwany rozmiar maszyny Wirtualnej ma limity skalowania i wydajności specyfikacje IOPS, przepustowości i liczby dysków, które można dołączyć dla maszyny Wirtualnej. Korzystając z dysków w warstwie premium magazynu maszyn wirtualnych, upewnij się, że istnieje wystarczająca IOPS i przepustowość na maszynie Wirtualnej stacji dysków ruchu.

    Na przykład STANDARD_DS1 maszyny Wirtualnej ma dedykowany przepustowości 32 MB/s dla ruchu dysku magazynu premium. Dysk magazynu premium P10 zapewniają przepustowości 100 MB/s. Jeśli dysk magazynu premium P10 jest dołączony do tej maszyny Wirtualnej, go tylko przejdź do 32 MB/s. Nie może używać maksymalną 100 MB/s zapewnić P10 dysku.

    Największy maszyny Wirtualnej z serii DS jest obecnie Standard_DS15_v2. Standard_DS15_v2 można podać maksymalnie 960 MB/s na wszystkich dyskach. Największy maszyny Wirtualnej w serii GS to Standard_GS5. Standard_GS5 można podać maksymalnie 2000 MB/s na wszystkich dyskach.

    Te limity dotyczą tylko ruchu dysku. Ograniczenia te nie zawierają trafień w pamięci podręcznej i ruchu sieciowego. Oddzielne przepustowości dostępnej dla ruchu sieciowego maszyn wirtualnych. Przepustowości dla ruchu sieciowego różni się od dedykowanych przepustowość wykorzystywaną przez dyski magazynu premium.

    Aby uzyskać najbardziej aktualne informacje o maksymalne IOPS i przepływności (przepustowość) dla maszyn wirtualnych, obsługiwane przez Magazyn w warstwie Premium, zobacz [rozmiarów maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/sizes.md) lub [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md).

    Aby uzyskać więcej informacji o dyski magazynu premium i limity ich IOPS i przepływność zobacz tabelę w następnej sekcji.

## <a name="scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności
W tej sekcji opisano elementy docelowe skalowalności i wydajności wziąć pod uwagę w przypadku używania magazynu Premium.

Konta Premium magazynu są następujące wartości docelowe skalowalności:

| Konto łączna pojemność | Przepustowość konto magazyn lokalnie nadmiarowy |
| --- | --- | 
| Pojemność dysku: 35 TB <br>Migawki pojemności: 10 TB | Się do 50 GB na sekundę dla ruchu przychodzącego<sup>1</sup> + wychodzących<sup>2</sup> |

<sup>1</sup> wszystkich danych (liczba żądań), które są wysyłane do konta magazynu

<sup>2</sup> wszystkich danych (odpowiedzi), które są odbierane z konta magazynu

Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności usługi Azure Storage](../articles/storage/common/storage-scalability-targets.md).

Jeśli używasz konta premium magazynu dysków niezarządzanego, aplikacja przekracza wartości docelowe skalowalności konta magazynu jednego, można migrować do zarządzanych dysków. Jeśli nie chcesz przeprowadzić migrację do zarządzanych dysków, należy skompilować aplikację do korzystania z wielu kont magazynu. Następnie partycji danych przez tych kont magazynu. Na przykład jeśli chcesz dołączyć dyski 51 TB między wieloma maszynami wirtualnymi rozłożyć je na dwóch kont magazynu. 35 TB stanowi limit dla konta magazynu premium pojedynczego. Upewnij się, że konto magazynu premium pojedynczego nigdy nie ma ponad 35 TB elastycznie dyski.

### <a name="premium-storage-disk-limits"></a>Limity dysku magazynu Premium
Podczas obsługi administracyjnej dysku magazynu premium rozmiar dysku określa maksymalną liczbę IOPS i przepływność (przepustowość). Platforma Azure oferuje siedem typów dysków w warstwie premium magazynu: P4 P6 (zarządzane tylko dysków), (zarządzane tylko dysków), P10, P20 P30, P40 i P50. Każdy typ dysku magazynu premium ma określone limity IOPS i przepływności. W poniższej tabeli opisano limity dla typów dysku:

| Typ dysków Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GB| 64 GB| 128 GB| 256 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 1100   | 2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB / s  | 100 MB na sekundę | 125 MB na sekundę | 150 MB na sekundę | 200 MB / s | 250 MB na sekundę | 250 MB na sekundę | 

> [!NOTE]
> Upewnij się, że wystarczającą przepustowość jest dostępne na maszynie Wirtualnej stacji dysków ruchu, zgodnie z opisem w [obsługiwane magazyn w warstwie Premium maszyn wirtualnych](#premium-storage-supported-vms). W przeciwnym razie przepływność dysku, a IOPS jest ograniczony do zmniejszenia wartości. Maksymalna przepustowość i IOPS są oparte na granicach maszyny Wirtualnej, nie znajduje się w granicach dysku opisane w powyższej tabeli.  
> 
> 

Oto kilka ważnych rzeczy wiedzieć o magazyn w warstwie Premium cele wydajności i skalowalności:

* **Udostępnione pojemność i wydajność**

    Podczas obsługi administracyjnej dysku magazynu premium, w przeciwieństwie do standardowego magazynu ma gwarancji pojemności, IOPS i przepływności tego dysku. Na przykład w przypadku tworzenia dysku P50 Azure udostępnia 4,095 GB pojemności, 7500 IOPS i 250-MB/s przepustowości dla tego dysku. Aplikacja może używać całość lub część pojemność i wydajność.

* **Rozmiar dysku**

    Azure mapuje rozmiar dysku (zaokrąglona w górę) do najbliższej premium dysku opcji magazynu, jak określono w tabeli w poprzedniej sekcji. Na przykład rozmiar 100 GB na dysku jest sklasyfikowany jako opcja P10. Może wykonywać maksymalnie 500 IOPS z maksymalnie 100-MB/s przepustowości. Podobnie dysk 400 GB jest sklasyfikowany jako P20 rozmiar. Może wykonywać maksymalnie 2300 IOPS, o 150-MB/s przepustowości.
    
    > [!NOTE]
    > Można łatwo zwiększyć rozmiar istniejącej dysków. Na przykład możesz zwiększyć rozmiar dysku 30 GB do 128 GB lub nawet do 1 TB. Można też dokonać konwersji dysku P20 dysku P30 ponieważ potrzebujesz większej pojemności lub więcej IOPS i przepustowość. 
    > 
 
* **Rozmiar operacji We/Wy**

    Rozmiar operacji We/Wy jest 256 KB. Jeśli przesyłane dane są mniej niż 256 KB, jest uznawany za 1 jednostka we/wy. Większe rozmiary We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KB. Na przykład 1100 KB we/wy jest liczony jako 5 jednostki we/wy.

* **Przepływność**

    Limit przepustowości dotyczy operacji zapisu na dysku i zawiera operacji odczytu na dysku, które nie są obsługiwane z pamięci podręcznej. Na przykład dysk P10 ma 100-MB/s przepustowości dla każdego dysku. W poniższej tabeli przedstawiono przykładowe prawidłowe przepływności dysku P10:

    | Maksymalna przepustowość dla każdego dysku P10 | Pamięć podręczna nie odczytuje z dysku | Spoza pamięci podręcznej zapisu na dysku |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Trafień w pamięci podręcznej**

    Trafień w pamięci podręcznej nie są ograniczone przez przydzielony IOPS lub przepływności dysku. Na przykład, jeśli używasz dysku danych o **tylko do odczytu** ustawienia pamięci podręcznej na maszynie Wirtualnej, która jest obsługiwana przez Magazyn w warstwie Premium, odczytów, które są obsługiwane z pamięci podręcznej nie podlegają IOPS i przepływność caps dysku. Jeśli obciążenie dysku jest głównie operacje odczytu i może spowodować, że bardzo wysokiej przepływności. Pamięć podręczna podlega oddzielnych IOPS i limity przepustowości w Maszynie wirtualnej poziomu, zależnie od rozmiaru maszyny Wirtualnej. Maszyny wirtualne z serii DS ma około 4000 IOPS i przepływność 33-MB/s na podstawowe dla lokalnych dysków SSD operacji We/Wy i pamięci podręcznej. Maszyny wirtualne z serii GS mają limit 5000 IOPS i przepływności 50-MB/s na podstawowe dla lokalnych dysków SSD operacji We/Wy i pamięci podręcznej. 

## <a name="throttling"></a>Ograniczanie przepływności
Ograniczanie mogą wystąpić, jeśli aplikacji IOPS lub przepływności przekracza limity przydzielone dla dysku magazynu premium. Również ograniczanie może wystąpić, jeśli ruchu dysku na wszystkich dyskach na maszynie Wirtualnej przekracza limit przepustowości dysku dostępne dla maszyny Wirtualnej. Aby uniknąć ograniczania przepustowości, firma Microsoft zaleca, aby ograniczyć liczbę oczekujących żądań We/Wy dysku. Użyj limit na podstawie celów skalowalność i wydajność dysku, który po uprzednim udostępnieniu i na dysku przepustowość do maszyny Wirtualnej.  

Aplikację można osiągnąć najniższym opóźnieniu zaprojektowano w celu uniknięcia ograniczania. Jednak jeśli liczba oczekujących żądań We/Wy dysku jest za mały, aplikacja nie może korzystać z maksymalna liczba IOPS i poziomu przepływności, które są dostępne na dysku.

Poniższe przykłady przedstawiają sposób obliczania poziomów ograniczania przepustowości. Wszystkie obliczenia są oparte na rozmiar jednostki we/wy 256 KB.

### <a name="example-1"></a>Przykład 1
Aplikacja została przetworzona 495 jednostki we/wy o rozmiarze 16 KB w jednej sekundy na dysku P10. Jednostki We/Wy są liczone jako 495 IOPS. Jeśli spróbujesz 2 MB we/wy w tym samym drugie, łącznie z jednostki we/wy jest równa 495 + 8 IOPS. Jest to spowodowane We/Wy 2 MB = jednostki 2048 KB / 256 KB = 8 we/wy po 256 KB, rozmiar jednostki we/wy. Ponieważ sumę 495 8 limit 500 IOPS dla dysku, ograniczanie występuje.

### <a name="example-2"></a>Przykład 2
Aplikacja została przetworzona 400 jednostek o rozmiarze 256 KB na dysku P10 we/wy. Całkowita liczba przepustowości jest (400 &#215; 256) / 1024 KB = 100 MB/s. Dysk P10 ma limit przepustowości 100 MB/s. Jeśli aplikacja próbuje wykonuje więcej operacji We/Wy w tym sekundy, jest to ograniczenie, ponieważ osiągnął limit przydzielone.

### <a name="example-3"></a>Przykład 3
Masz DS4 maszyny Wirtualnej z dwóch dysków P30 dołączony. Każdy dysk P30 jest w stanie 200-MB/s przepustowości. Jednak DS4 maszyny Wirtualnej ma pojemność dysku przepustowości 256 MB/s. Nie może obsłużyć obydwa dyski podłączone do maksymalną przepustowość na tej maszynie Wirtualnej DS4 w tym samym czasie. Aby rozwiązać ten problem, może wytrzymać ruchu 200 MB/s na jednym dysku i 56 MB/s na innym dysku. Jeśli sumę ruchu dysku odbywa się za pośrednictwem 256 MB/s, jest ograniczany ruch sieciowy dysku.

> [!NOTE]
> Jeśli ruchu dysku przede wszystkim składa się z małych rozmiarów we/wy, prawdopodobnie aplikacji będzie osiągnął limit IOPS przed limit przepływności. Jednak jeśli ruch dysku zawiera głównie duże rozmiary we/wy, prawdopodobnie aplikacji będzie osiągnęła limit przepływności najpierw zamiast limitu IOPS. Można zmaksymalizować IOPS aplikacji i przepustowości przy użyciu optymalny rozmiar operacji We/Wy. Ponadto można ograniczyć liczba oczekujących żądań We/Wy dysku.
> 

Aby dowiedzieć się więcej na temat projektowania o wysokiej wydajności przy użyciu magazyn w warstwie Premium, zobacz [projektu dla wydajności z magazyn w warstwie Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Migawki i kopii obiektu Blob

Usługa Magazyn plików wirtualnego dysku twardego jest stronicowych obiektów blob. Twórz migawki stronicowe obiekty BLOB i skopiuj je do innej lokalizacji, takich jak do innego konta magazynu.

### <a name="unmanaged-disks"></a>Dyski niezarządzane

Utwórz [przyrostowe migawki](../articles/virtual-machines/linux/incremental-snapshots.md) dla niezarządzanego premium dyski tak samo jak migawki z magazynu w warstwie standardowa. Magazyn w warstwie Premium obsługuje tylko lokalnie nadmiarowego magazynu jako opcję replikacji. Zaleca się tworzenie migawki, a następnie skopiować migawki do konta geograficznie nadmiarowego magazynu w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [opcje nadmiarowość magazynu Azure](../articles/storage/common/storage-redundancy.md).

Jeśli dysk jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API na dysku nie są dozwolone. Na przykład nie można wykonać [kopiowania obiektu Blob](/rest/api/storageservices/Copy-Blob) operacji dla tego obiektu blob, jeśli dysk jest dołączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę tego obiektu blob przy użyciu [migawki obiektu Blob](/rest/api/storageservices/Snapshot-Blob) interfejsu API REST. Następnie należy wykonać [kopiowania obiektu Blob](/rest/api/storageservices/Copy-Blob) migawki, aby skopiować dołączono dysk. Alternatywnie można odłączyć dysk, a następnie wykonaj wszystkie niezbędne operacje.

Do migawki obiektu blob magazynu premium Zastosuj następujące ograniczenia:

| Limit magazynu w warstwie Premium | Wartość |
| --- | --- |
| Maksymalna liczba migawek na obiektów blob | 100 |
| Pojemności konta magazynu dla migawki<br>(Dotyczy danych tylko migawki. Nie zawiera danych w podstawowej obiektu blob.) | 10 TB |
| Minimalny czas między kolejnymi migawki | 10 minut |

Aby zachować geograficznie nadmiarowego kopie z migawki, możesz skopiować migawki z konta magazynu premium do konta geograficznie nadmiarowego magazynu w warstwie standardowa przy użyciu narzędzia AzCopy lub kopiowania obiektu Blob. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą wiersza polecenia azcopy](../articles/storage/common/storage-use-azcopy.md) i [kopiowania obiektu Blob](/rest/api/storageservices/Copy-Blob).

Aby uzyskać szczegółowe informacje dotyczące operacji REST względem stronicowe obiekty BLOB na koncie magazynu premium, zobacz [obiektu Blob operacji usługi z usługą Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Dyski zarządzane

Migawek dla dysków zarządzanych jest kopię zarządzanego dysku tylko do odczytu. Migawki są przechowywane jako standardowych dysków zarządzanych. Obecnie [przyrostowe migawki](../articles/virtual-machines/windows/incremental-snapshots.md) nie są obsługiwane w przypadku dysków zarządzanych. Aby dowiedzieć się, jak migawki dysków zarządzanych, zobacz [Utwórz kopię plik VHD przechowywany jako zarządzany Azure dysku przy użyciu migawek zarządzane w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) lub [Utwórz kopię plik VHD przechowywany jako zarządzany platformy Azure dysku przy użyciu migawek zarządzane w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Jeśli dysków zarządzanych jest dołączony do maszyny Wirtualnej, niektóre operacje interfejsu API na dysku nie są dozwolone. Na przykład nie można wygenerować sygnaturę dostępu współdzielonego (SAS), można wykonać operacji kopiowania, gdy dysk jest dołączony do maszyny Wirtualnej. Zamiast tego należy najpierw utworzyć migawkę dysku, a następnie wykonaj kopię migawki. Alternatywnie można odłączyć dysk, a następnie wygeneruj dostępu Współdzielonego w celu wykonania operacji kopiowania.


## <a name="premium-storage-for-linux-vms"></a>Magazyn w warstwie Premium dla maszyn wirtualnych systemu Linux
Skorzystaj z poniższych informacji, mogą pomóc Ci skonfigurować Twoje maszyn wirtualnych systemu Linux w magazyn w warstwie Premium:

Do osiągnięcia wartości docelowe skalowalności w magazynie Premium dla wszystkich dysków w warstwie premium magazynu z pamięci podręcznej należy ustawić na **tylko do odczytu** lub **Brak**, należy wyłączyć "bariery" w przypadku zainstalowania systemu plików. Ponieważ zapisy na dyski premium magazynu są trwałe dla tych ustawień pamięci podręcznej nie jest konieczne bariery w tym scenariuszu. Po pomyślnym zakończeniu żądania zapisu, danych został zapisany w magazynie trwałym. Aby wyłączyć "bariery", użyj jednej z poniższych metod. Wybierz dla systemu plików:
  
* Aby uzyskać **reiserFS**, aby wyłączyć bariery, użyj `barrier=none` opcji instalacji. (Aby włączyć bariery, należy użyć `barrier=flush`.)
* Aby uzyskać **ext3/ext4**, aby wyłączyć bariery, użyj `barrier=0` opcji instalacji. (Aby włączyć bariery, należy użyć `barrier=1`.)
* Aby uzyskać **XFS**, aby wyłączyć bariery, użyj `nobarrier` opcji instalacji. (Aby włączyć bariery, należy użyć `barrier`.)
* Dla usługi premium storage dysków z pamięci podręcznej ustawioną **ReadWrite**, Włącz bariery dla zapisu trwałości.
* Dla etykiety woluminów można utrwalić po ponownym uruchomieniu maszyny Wirtualnej należy zaktualizować /etc/fstab z odwołaniami do unikatowego identyfikatora uniwersalnego (UUID) na dyskach. Aby uzyskać więcej informacji, zobacz [dodać zarządzane dysk do maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/add-disk.md).

Następujące dystrybucje systemu Linux zostały zatwierdzone dla usługi Azure Premium Storage. Lepszą wydajność i stabilności magazyn w warstwie Premium zaleca się uaktualnienie maszyn wirtualnych do jednej z tych wersji, co najmniej (lub nowszy). Najnowsze Linux integracji usług (LIS), wersja 4.0, niektórych wersji wymagają dla platformy Azure. Aby pobrać i zainstalować dystrybucji, kliknij link wymienione w poniższej tabeli. Obrazy możemy dodać do listy, podczas wykonywania sprawdzania poprawności. Należy pamiętać, że naszego operacje sprawdzania poprawności, Pokaż, że wydajność może być różna dla każdego obrazu. Wydajność zależy od obciążenia właściwości i ustawienia obrazu. Obrazy różnych dostosowanych do różnych rodzajów obciążeń.

| Dystrybucja | Wersja | Obsługiwane jądra | Szczegóły |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 Z DODATKIEM SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 wymagane](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Patrz Uwaga w następnej sekcji* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 zalecane](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Patrz Uwaga w następnej sekcji* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 lub RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 lub RHCK z[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 lub RHCK z[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Sterowniki LIS OpenLogic CentOS

Jeśli korzystasz z maszyny wirtualnej CentOS OpenLogic, uruchom następujące polecenie, aby zainstalować najnowsze sterowniki:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Aby aktywować nowe sterowniki, uruchom ponownie komputer.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Gdy używasz magazyn w warstwie Premium, zastosuj następujące zagadnienia dotyczące rozliczeń:

* **Rozmiar dysku i obiektów blob magazynu Premium**

    Rozliczeń dla dysku magazynu premium lub obiekt blob jest zależna od elastycznie rozmiar dysku lub obiektu blob. Azure mapuje elastycznie rozmiar (zaokrąglona w górę) do najbliższej opcji dysku magazynu premium. Aby uzyskać więcej informacji, zobacz tabelę w [magazyn w warstwie Premium cele wydajności i skalowalności](#premium-storage-scalability-and-performance-targets). Każdy dysk mapuje do obsługiwanych inicjowania obsługi rozmiar dysku i jest on rozliczany odpowiednio. Rozliczeń dla dowolnego dysku inicjowana jest proporcjonalna co godzinę przy użyciu miesięcznej cenie oferty magazyn w warstwie Premium. Na przykład jeśli elastycznie dysk P10 i usunięto go po 20 godzin, rozliczenie jest przeprowadzane dla oferty P10 obliczone proporcjonalnie do 20 godzin. Jest to niezależnie od ilości danych rzeczywistych zapisywane na dysku lub IOPS, przepływności używane.

* **Premium niezarządzane dysków migawek**

    Migawki na dyski premium niezarządzanych są rozliczane dodatkowej pojemności używanych przez migawki. Aby uzyskać więcej informacji na temat migawek, zobacz [utworzenia migawki obiektu blob](/rest/api/storageservices/Snapshot-Blob).

* **Migawek dysków zarządzanych w warstwie Premium**

    Migawki dysków zarządzanych jest tylko do odczytu kopię dysku. Dysk jest przechowywana jako standardowych dysków zarządzanych. Migawki koszty takie same, jako standard zarządzany dysku. Na przykład jeśli migawki z dysków zarządzanych w warstwie premium 128 GB koszt migawki jest odpowiednikiem 128 GB standardowych dysków zarządzanych.  

* **Transfer danych wychodzących**

    [Transfer danych wychodzących](https://azure.microsoft.com/pricing/details/data-transfers/) (danych wychodzących z centrach danych platformy Azure) powodują Naliczanie opłat za zużycie przepustowości.

Aby uzyskać szczegółowe informacje o cenach dla magazyn w warstwie Premium, obsługiwane przez Magazyn w warstwie Premium maszyn wirtualnych i dysków zarządzanych zobacz następujące artykuły:

* [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Cennik maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Dyski zarządzane ceny](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Obsługa kopii zapasowej systemu Azure 

Regionalnej awarii, musisz należy wykonać kopię zapasową dysków maszyny Wirtualnej w innym regionie przy użyciu [kopia zapasowa Azure](../articles/backup/backup-introduction-to-azure-backup.md) i konto magazynu GRS jako magazyn kopii zapasowych.

Aby utworzyć zadanie tworzenia kopii zapasowej z kopii zapasowych opartych na czas, łatwe przywrócenie maszyny Wirtualnej, a zasady przechowywania kopii zapasowych, użyj kopia zapasowa Azure. Narzędzie Kopia zapasowa służy zarówno z dyskami niezarządzane i zarządzane. Aby uzyskać więcej informacji, zobacz [kopia zapasowa Azure dla maszyn wirtualnych z dyskami niezarządzane](../articles/backup/backup-azure-vms-first-look-arm.md) i [kopia zapasowa Azure dla maszyn wirtualnych z dyskami zarządzanych](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat magazyn w warstwie Premium zobacz następujące artykuły.