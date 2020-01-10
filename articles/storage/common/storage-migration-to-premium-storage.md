---
title: Migrowanie maszyn wirtualnych do usługi Azure Premium Storage | Microsoft Docs
description: Migruj istniejące maszyny wirtualne do Premium Storage platformy Azure. Premium Storage oferuje wysoką wydajność, obsługę dysków o niskich opóźnieniach dla obciążeń intensywnie korzystających z operacji we/wy działających na platformie Azure Virtual Machines.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748350"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrowanie do usługi Azure Premium Storage (dyski niezarządzane)

> [!NOTE]
> W tym artykule omówiono sposób migrowania maszyny wirtualnej korzystającej z niezarządzanych dysków standardowych do maszyny wirtualnej korzystającej z niezarządzanych dysków Premium. Zalecamy używanie Managed Disks platformy Azure dla nowych maszyn wirtualnych i konwertowanie wcześniejszych dysków niezarządzanych na dyski zarządzane. Managed Disks obsłużyć podstawowe konta magazynu, aby nie było konieczne. Aby uzyskać więcej informacji, zobacz nasze [omówienie Managed disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Usługa Azure Premium Storage zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych z intensywnymi obciążeniami we/wy. Aby skorzystać z szybkości i wydajności tych dysków, można przeprowadzić migrację dysków maszyny wirtualnej aplikacji do usługi Azure Premium Storage.

Celem tego przewodnika jest ułatwienie nowym użytkownikom platformy Azure Premium Storage lepszego przygotowania do zapewnienia sprawnego przejścia od bieżącego systemu do Premium Storage. Przewodnik dotyczy trzech najważniejszych składników w tym procesie:

* [Zaplanuj migrację do Premium Storage](#plan-the-migration-to-premium-storage)
* [Przygotuj i skopiuj wirtualne dyski twarde (VHD) do Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Tworzenie maszyny wirtualnej platformy Azure przy użyciu Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Możesz migrować maszyny wirtualne z innych platform do platformy Azure Premium Storage lub migrować istniejące maszyny wirtualne platformy Azure z magazynu w warstwie Standardowa do Premium Storage. Ten przewodnik obejmuje kroki dla obu dwóch scenariuszy. Wykonaj kroki określone w odpowiedniej sekcji w zależności od danego scenariusza.

> [!NOTE]
> Przegląd funkcji i Cennik usługi Premium dysków SSD można znaleźć w temacie: [Wybierz typ dysku dla maszyn wirtualnych IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Zalecamy Migrowanie dowolnego dysku maszyny wirtualnej wymagającego dużej liczby operacji we/wy na platformie Azure Premium Storage w celu uzyskania najlepszej wydajności aplikacji. Jeśli dysk nie wymaga dużej liczby operacji we/wy na sekundę, możesz ograniczyć koszty, utrzymując je w magazynie w warstwie Standardowa, która przechowuje dane dysku maszyny wirtualnej na dyskach twardych (HDD) zamiast dysków SSD.
>

Ukończenie procesu migracji w całości może wymagać dodatkowych akcji zarówno przed, jak i po wykonaniu czynności opisanych w tym przewodniku. Przykłady obejmują Konfigurowanie sieci wirtualnych lub punktów końcowych lub wprowadzanie zmian w kodzie samej aplikacji, co może wymagać pewnego przestoju w aplikacji. Te akcje są unikatowe dla każdej aplikacji i należy je wykonać wraz z krokami podanymi w tym przewodniku, aby zapewnić bezproblemowy przechodzenie do Premium Storage jak najszybciej.

## <a name="plan-the-migration-to-premium-storage"></a>Zaplanuj migrację do Premium Storage
Ta sekcja zapewnia gotowość do wykonania kroków migracji opisanych w tym artykule i pomaga w podejmowaniu najlepszej decyzji na temat maszyn wirtualnych i typów dysków.

### <a name="prerequisites"></a>Wymagania wstępne
* Konieczna będzie subskrypcja platformy Azure. Jeśli go nie masz, możesz utworzyć miesięczną subskrypcję [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) lub zapoznaj się z [cennikiem platformy Azure](https://azure.microsoft.com/pricing/) , aby uzyskać więcej opcji.
* Do wykonywania poleceń cmdlet programu PowerShell potrzebny jest moduł Microsoft Azure PowerShell. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Planując korzystanie z maszyn wirtualnych platformy Azure działających na Premium Storage, musisz użyć maszyn wirtualnych z Premium Storage. Można używać zarówno dysków standardowych, jak i Premium Storage z maszynami wirtualnymi Premium Storage. Dyski Premium Storage będą dostępne z większą liczbą typów maszyn wirtualnych w przyszłości. Aby uzyskać więcej informacji na temat wszystkich dostępnych typów i rozmiarów dysków maszyn wirtualnych platformy Azure, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [rozmiarów Cloud Services](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Zagadnienia do rozważenia
Maszyna wirtualna platformy Azure obsługuje dołączanie kilku dysków Premium Storage, dzięki czemu aplikacje mogą mieć do 256 TB magazynu na maszynę wirtualną. Dzięki Premium Storage Twoje aplikacje mogą osiągnąć 80 000 operacji we/wy na sekundę na maszynę wirtualną oraz przepływność dysku 2000 MB na sekundę na maszynę wirtualną z bardzo niskimi opóźnieniami operacji odczytu. Dostępne są różne opcje różnych maszyn wirtualnych i dysków. Ta sekcja zawiera informacje ułatwiające znalezienie opcji, która najlepiej odpowiada Twojemu obciążeniu.

#### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych
Specyfikacje rozmiaru maszyny wirtualnej platformy Azure są wymienione w obszarze [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zapoznaj się z charakterystyką wydajności maszyn wirtualnych, które działają z Premium Storage i wybierz najbardziej odpowiedni rozmiar maszyny wirtualnej, który najlepiej odpowiada Twojemu obciążeniu. Upewnij się, że na maszynie wirtualnej jest dostępna wystarczająca przepustowość do obsługi ruchu na dysku.

#### <a name="disk-sizes"></a>Rozmiary dysków
Istnieje pięć typów dysków, które mogą być używane z maszyną wirtualną, a każda z nich ma określone limity IOPs i przepływności. Należy wziąć pod uwagę te limity podczas wybierania typu dysku dla maszyny wirtualnej na podstawie potrzeb aplikacji w zakresie pojemności, wydajności, skalowalności i szczytowych obciążeń.

| Typ dysków w warstwie Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Rozmiar dysku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Liczba operacji we/wy na sekundę na dysk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Przepływność na dysk | 100 MB na sekundę | 150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę |

W zależności od obciążenia Ustal, czy dodatkowe dyski danych są niezbędne dla maszyny wirtualnej. Do maszyny wirtualnej można dołączyć kilka dysków danych trwałych. W razie potrzeby można rozdzielić dyski, aby zwiększyć pojemność i wydajność woluminu. (Zobacz, co to jest rozłożenie dysku w [tym miejscu](../../virtual-machines/windows/premium-storage-performance.md#disk-striping)). W przypadku rozłożenia dysków danych Premium Storage przy użyciu funkcji [miejsca do magazynowania][4]należy skonfigurować ją z jedną kolumną dla każdego używanego dysku. W przeciwnym razie ogólna wydajność woluminu rozłożonego może być niższa niż oczekiwano z powodu nierównomiernego rozkładu ruchu na dyskach. W przypadku maszyn wirtualnych z systemem Linux można użyć narzędzia *mdadm* do osiągnięcia tego samego. Aby uzyskać szczegółowe informacje, zobacz artykuł [Konfigurowanie oprogramowania RAID w systemie Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

#### <a name="storage-account-scalability-targets"></a>Cele skalowalności konta magazynu

Konta Premium Storage mają następujące elementy docelowe skalowalności. Jeśli wymagania dotyczące aplikacji przekraczają tarcze skalowalności pojedynczego konta magazynu, należy skompilować aplikację w celu korzystania z wielu kont magazynu i podzielić dane na te konta magazynu.

| Łączna pojemność konta | Łączna przepustowość dla lokalnego nadmiarowego konta magazynu |
|:--- |:--- |
| Pojemność dysku: 35TB<br />Pojemność migawki: 10 TB |Do 50 Gigabit na sekundę dla ruchu przychodzącego i wychodzącego |

Aby uzyskać więcej informacji na temat specyfikacji Premium Storage, zobacz [elementy docelowe skalowalności dla kont usługi BLOB Storage na stronie Premium](../blobs/scalability-targets-premium-page-blobs.md).

#### <a name="disk-caching-policy"></a>Zasady buforowania dysku
Domyślnie zasady buforowania dysku są tylko do *odczytu* dla wszystkich dysków danych w warstwie Premium oraz do *odczytu i zapisu* dla dysku systemu operacyjnego Premium dołączonego do maszyny wirtualnej. To ustawienie konfiguracji jest zalecane, aby osiągnąć optymalną wydajność aplikacji dla systemu IOs. W przypadku dysków z danymi zapisu lub zapisu (takich jak SQL Server plików dziennika) należy wyłączyć buforowanie dysków, aby zapewnić lepszą wydajność aplikacji. Ustawienia pamięci podręcznej dla istniejących dysków z danymi można aktualizować za pomocą [Azure Portal](https://portal.azure.com) lub parametru *-HostCaching* polecenia cmdlet *Set-AzureDataDisk* .

#### <a name="location"></a>Lokalizacja
Wybierz lokalizację, w której usługa Azure Premium Storage jest dostępna. Zobacz [usługi platformy Azure według regionów,](https://azure.microsoft.com/regions/#services) Aby uzyskać aktualne informacje dotyczące dostępnych lokalizacji. Maszyny wirtualne znajdujące się w tym samym regionie co konto magazynu, w którym są przechowywane dyski dla maszyny wirtualnej, zapewniają znacznie lepszą wydajność niż w przypadku, gdy znajdują się w osobnych regionach.

#### <a name="other-azure-vm-configuration-settings"></a>Inne ustawienia konfiguracji maszyny wirtualnej platformy Azure
Podczas tworzenia maszyny wirtualnej platformy Azure zostanie wyświetlony monit o skonfigurowanie niektórych ustawień maszyny wirtualnej. Należy pamiętać, że niektóre ustawienia są rozwiązane przez okres istnienia maszyny wirtualnej, podczas gdy można modyfikować lub dodawać inne osoby później. Przejrzyj te ustawienia konfiguracji maszyny wirtualnej platformy Azure i upewnij się, że są one odpowiednio skonfigurowane do wymagań związanych z obciążeniem.

### <a name="optimization"></a>Optymalizacja
[Azure Premium Storage: projektowanie pod kątem wysokiej wydajności](../../virtual-machines/windows/premium-storage-performance.md) zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Można przestrzegać wytycznych związanych z najlepszymi rozwiązaniami dotyczącymi wydajności, które dotyczą technologii używanych przez aplikację.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Przygotuj i skopiuj wirtualne dyski twarde (VHD) do Premium Storage
W poniższej sekcji przedstawiono wskazówki dotyczące przygotowywania dysków VHD z maszyny wirtualnej i kopiowania dysków VHD do usługi Azure Storage.

* [Scenariusz 1: "Migrowanie istniejących maszyn wirtualnych platformy Azure do usługi Azure Premium Storage".](#scenario1)
* [Scenariusz 2: "Migrowanie maszyn wirtualnych z innych platform do platformy Azure Premium Storage".](#scenario2)

### <a name="prerequisites"></a>Wymagania wstępne
Aby przygotować wirtualne dyski twarde do migracji, potrzebne są:

* Subskrypcja platformy Azure, konto magazynu i kontener na tym koncie magazynu, do którego można skopiować dysk VHD. Należy pamiętać, że docelowe konto magazynu może być kontem standardowym lub Premium Storage, w zależności od wymagań.
* Narzędzie służące do uogólniania dysku VHD, jeśli planujesz utworzyć wiele wystąpień maszyn wirtualnych. Na przykład program Sysprep dla systemu Windows lub virt — Sysprep dla Ubuntu.
* Narzędzie do przekazywania pliku VHD do konta magazynu. Zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md) lub [Eksploratora usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). W tym przewodniku opisano kopiowanie wirtualnego dysku twardego za pomocą narzędzia AzCopy.

> [!NOTE]
> Jeśli wybierzesz opcję kopiowania synchronicznego z AzCopy, aby uzyskać optymalną wydajność, Skopiuj wirtualny dysk twardy przez uruchomienie jednego z tych narzędzi z maszyny wirtualnej platformy Azure, która znajduje się w tym samym regionie co docelowe konto magazynu. Jeśli kopiujesz dysk VHD z maszyny wirtualnej platformy Azure w innym regionie, wydajność może być mniejsza.
>
> W przypadku kopiowania dużej ilości danych za pośrednictwem ograniczonej przepustowości należy rozważyć [użycie usługi Azure Import/Export do transferowania danych do BLOB Storage](../storage-import-export-service.md); pozwala to na przeniesienie danych przez wysyłkę dysków twardych do centrum danych platformy Azure. Możesz użyć usługi Azure Import/Export, aby skopiować dane tylko do konta magazynu w warstwie Standardowa. Gdy dane są na koncie magazynu w warstwie Standardowa, możesz użyć [interfejsu API kopiowania obiektu BLOB](https://msdn.microsoft.com/library/azure/dd894037.aspx) lub AzCopy do transferowania danych do konta magazynu w warstwie Premium.
>
> Należy pamiętać, że Microsoft Azure obsługuje tylko pliki VHD o stałym rozmiarze. Pliki VHDX lub dynamiczne wirtualne dyski twarde nie są obsługiwane. Jeśli masz dynamiczny dysk VHD, możesz go przekonwertować na stały rozmiar przy użyciu polecenia cmdlet [convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Scenariusz 1: "Migrowanie istniejących maszyn wirtualnych platformy Azure do usługi Azure Premium Storage".
W przypadku migrowania istniejących maszyn wirtualnych platformy Azure Zatrzymaj maszynę wirtualną, przygotuj wirtualne dyski twarde na żądany typ wirtualnego dysku twardego, a następnie skopiuj dysk VHD za pomocą AzCopy lub PowerShell.

Aby przeprowadzić migrację czystego stanu, maszyna wirtualna musi zostać całkowicie wyłączona. Zostanie wydłużony przestój do momentu zakończenia migracji.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Przygotowanie dysków VHD do migracji
W przypadku migrowania istniejących maszyn wirtualnych platformy Azure do programu Premium Storage wirtualny dysk twardy może być:

* Uogólniony obraz systemu operacyjnego
* Unikatowy dysk systemu operacyjnego
* Dysk z danymi

Poniżej opisano trzy scenariusze przygotowywania wirtualnego dysku twardego.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Tworzenie wielu wystąpień maszyn wirtualnych za pomocą uogólnionego wirtualnego dysku twardego systemu operacyjnego
W przypadku przekazywania dysku VHD, który będzie używany do tworzenia wielu ogólnych wystąpień maszyn wirtualnych platformy Azure, należy najpierw uogólnić dysk VHD przy użyciu narzędzia Sysprep. Dotyczy to wirtualnego dysku twardego, który jest lokalnie lub w chmurze. Program Sysprep usuwa wszystkie informacje specyficzne dla komputera z dysku VHD.

> [!IMPORTANT]
> Utwórz migawkę lub Utwórz kopię zapasową maszyny wirtualnej przed jej uogólnieniem. Uruchomienie narzędzia Sysprep spowoduje zatrzymanie i cofnięcie alokacji wystąpienia maszyny wirtualnej. Wykonaj poniższe czynności, aby użyć programu Sysprep dla wirtualnego dysku twardego systemu operacyjnego Windows. Należy pamiętać, że uruchomienie polecenia Sysprep wymaga wyłączenia maszyny wirtualnej. Aby uzyskać więcej informacji o programie Sysprep, zobacz [Omówienie narzędzia Sysprep](https://technet.microsoft.com/library/hh825209.aspx) lub [Informacje techniczne dotyczące programu Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otwórz okno wiersza polecenia jako administrator.
2. Wprowadź następujące polecenie, aby otworzyć program Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. W narzędziu Przygotowywanie systemu wybierz pozycję Wprowadź system out-of-Box Experience (OOBE), zaznacz pole wyboru generalize, wybierz pozycję **Zamknij**, a następnie kliknij przycisk **OK**, jak pokazano na poniższej ilustracji. Program Sysprep przeprowadzi uogólnienie systemu operacyjnego i zamknie system.

    ![][1]

W przypadku maszyny wirtualnej Ubuntu Użyj narzędzia virt-Sysprep, aby osiągnąć ten sam sposób. Aby uzyskać więcej informacji, zobacz [virt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Zapoznaj się również z oprogramowaniem typu open source systemu [Linux Server aprowizacji](https://www.cyberciti.biz/tips/server-provisioning-software.html) dla innych systemów operacyjnych Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Tworzenie pojedynczego wystąpienia maszyny wirtualnej przy użyciu unikatowego wirtualnego dysku twardego systemu operacyjnego
Jeśli na maszynie wirtualnej jest uruchomiona aplikacja, która wymaga danych specyficznych dla komputera, nie należy uogólniać dysku VHD. Nieuogólniony wirtualny dysk twardy można użyć do utworzenia unikatowego wystąpienia maszyny wirtualnej platformy Azure. Na przykład jeśli masz kontroler domeny na wirtualnym dysku twardym, wykonanie narzędzia Sysprep spowoduje jego nieskuteczną obsługę jako kontroler domeny. Przed uogólnieniem dysku VHD Przejrzyj aplikacje działające na maszynie wirtualnej i wpływ na ich uruchamianie programu Sysprep.

##### <a name="register-data-disk-vhd"></a>Rejestruj dysk VHD z danymi
Jeśli masz dyski danych na platformie Azure do migracji, musisz upewnić się, że maszyny wirtualne korzystające z tych dysków danych są zamknięte.

Wykonaj kroki opisane poniżej, aby skopiować wirtualny dysk twardy do platformy Azure Premium Storage i zarejestrować go jako dysk danych aprowizacji.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Utwórz miejsce docelowe dla wirtualnego dysku twardego
Utwórz konto magazynu do obsługi wirtualnych dysków twardych. Planując lokalizację przechowywania wirtualnych dysków twardych, należy wziąć pod uwagę następujące kwestie:

* Docelowe konto magazynu w warstwie Premium.
* Lokalizacja konta magazynu musi być taka sama jak Premium Storage maszyn wirtualnych platformy Azure, które zostaną utworzone w końcowym etapie. Możesz skopiować do nowego konta magazynu lub zaplanować użycie tego samego konta magazynu na podstawie Twoich potrzeb.
* Skopiuj i Zapisz klucz konta magazynu docelowego konta magazynu dla kolejnego etapu.

W przypadku dysków z danymi można wybrać opcję przechowywania niektórych dysków z danymi na koncie magazynu w warstwie Standardowa (na przykład na dyskach z magazynem chłodnicy), ale zdecydowanie zalecamy przeniesienie wszystkich danych na potrzeby obciążenia produkcyjnego w celu korzystania z usługi Premium Storage.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Kopiowanie dysku VHD za pomocą AzCopy lub programu PowerShell
Musisz znaleźć ścieżkę kontenera i klucz konta magazynu, aby przetworzyć jedną z tych dwóch opcji. Ścieżkę kontenera i klucz konta magazynu można znaleźć w **witrynie Azure Portal** > **Magazyn**. Adres URL kontenera będzie wyglądać podobnie do "https:\//myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opcja 1: Kopiowanie dysku VHD z AzCopy (kopia asynchroniczna)

Za pomocą AzCopy można łatwo przekazać wirtualny dysk twardy za pośrednictwem Internetu. W zależności od rozmiaru dysków VHD może to zająć trochę czasu. Należy pamiętać o sprawdzeniu limitów ruchu przychodzącego/wychodzącego kont magazynu przy użyciu tej opcji. Aby uzyskać szczegółowe informacje [, zobacz cele dotyczące skalowalności i wydajności dla kont magazynu w warstwie Standardowa](scalability-targets-standard-account.md) .

1. Pobierz i zainstaluj AzCopy z tego miejsca: [Najnowsza wersja AzCopy](https://aka.ms/downloadazcopy)
2. Otwórz Azure PowerShell i przejdź do folderu, w którym zainstalowano AzCopy.
3. Użyj poniższego polecenia, aby skopiować plik VHD z lokalizacji "source" do "Destination".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Przykład:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Poniżej przedstawiono opisy parametrów używanych w AzCopy polecenia:

   * **/Source:** _&lt;źródła&gt;:_ Lokalizacja folderu lub adresu URL kontenera magazynu zawierającego dysk VHD.
   * **/SourceKey:** _&lt;konto źródłowe — klucz&gt;:_ klucz konta magazynu źródłowego konta magazynu.
   * **/Dest:** _&lt;Destination&gt;:_ adres URL kontenera magazynu, do którego ma zostać skopiowany dysk VHD.
   * **/DestKey:** _&lt;klucz docelowy&gt;:_ klucz konta magazynu docelowego konta magazynu.
   * **/Pattern:** _&lt;nazwa pliku&gt;:_ Określ nazwę pliku wirtualnego dysku twardego do skopiowania.

Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia AzCopy, zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opcja 2: Kopiowanie dysku VHD za pomocą programu PowerShell (kopia zsynchronizowana)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Możesz również skopiować plik VHD przy użyciu polecenia cmdlet programu PowerShell Start-AzStorageBlobCopy. Użyj następującego polecenia na Azure PowerShell, aby skopiować dysk VHD. Zastąp wartości w < > odpowiednimi wartościami z konta magazynu źródłowego i docelowego. Aby użyć tego polecenia, musisz mieć kontener o nazwie VHD na docelowym koncie magazynu. Jeśli kontener nie istnieje, utwórz go przed uruchomieniem polecenia.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Przykład:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenariusz 2: "Migrowanie maszyn wirtualnych z innych platform do platformy Azure Premium Storage".
W przypadku migrowania wirtualnego dysku twardego z magazynu w chmurze spoza platformy Azure do platformy Azure należy najpierw wyeksportować dysk VHD do katalogu lokalnego. Pełna ścieżka źródłowa katalogu lokalnego, w którym jest przechowywany dysk VHD, a następnie użycie AzCopy do przekazania go do usługi Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Eksportowanie wirtualnego dysku twardego do katalogu lokalnego
##### <a name="copy-a-vhd-from-aws"></a>Kopiowanie wirtualnego dysku twardego z AWS
1. Jeśli używasz AWS, wyeksportuj wystąpienie usługi EC2 do dysku VHD w zasobniku usługi Amazon S3. Wykonaj kroki opisane w dokumentacji usługi Amazon, aby wyeksportować wystąpienia usługi Amazon EC2 w celu zainstalowania narzędzia interfejsu wiersza polecenia Amazon EC2 (CLI), a następnie uruchom polecenie CREATE-instance-Export-Task, aby wyeksportować wystąpienie EC2 do pliku VHD. Użyj **dysku VHD** dla zmiennej format obrazu&#95;&#95;dysku podczas uruchamiania polecenia **Create-instance-Export-Task** . Wyeksportowany plik VHD zostanie zapisany w zasobniku Amazon S3 wyznaczonym podczas tego procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Pobierz plik VHD z zasobnika S3. Wybierz plik VHD, a następnie **akcje** > **Pobierz**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiowanie wirtualnego dysku twardego z innej chmury spoza platformy Azure
W przypadku migrowania wirtualnego dysku twardego z magazynu w chmurze spoza platformy Azure do platformy Azure należy najpierw wyeksportować dysk VHD do katalogu lokalnego. Skopiuj pełną ścieżkę źródłową katalogu lokalnego, w którym jest przechowywany dysk VHD.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopiowanie wirtualnego dysku twardego z lokalnego
W przypadku migrowania wirtualnego dysku twardego z środowiska lokalnego potrzebna będzie pełna ścieżka źródłowa, w której jest przechowywany dysk VHD. Ścieżka źródłowa może być lokalizacją serwera lub udziałem plików.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Utwórz miejsce docelowe dla wirtualnego dysku twardego
Utwórz konto magazynu do obsługi wirtualnych dysków twardych. Planując lokalizację przechowywania wirtualnych dysków twardych, należy wziąć pod uwagę następujące kwestie:

* Docelowe konto magazynu może być magazynem w warstwie Standardowa lub Premium, w zależności od wymagań aplikacji.
* Region konta magazynu musi być taki sam jak Premium Storage maszyn wirtualnych platformy Azure, które zostaną utworzone w końcowym etapie. Możesz skopiować do nowego konta magazynu lub zaplanować użycie tego samego konta magazynu na podstawie Twoich potrzeb.
* Skopiuj i Zapisz klucz konta magazynu docelowego konta magazynu dla kolejnego etapu.

Zdecydowanie zalecamy przeniesienie wszystkich danych dla obciążenia produkcyjnego w celu korzystania z usługi Premium Storage.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Przekazywanie wirtualnego dysku twardego do usługi Azure Storage
Teraz, gdy masz wirtualny dysk twardy w katalogu lokalnym, możesz użyć AzCopy lub AzurePowerShell, aby przekazać plik VHD do usługi Azure Storage. Obie opcje są dostępne tutaj:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opcja 1: użycie Azure PowerShell Add-AzureVhd do przekazania pliku VHD

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Przykładem \<> URI może być **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_** . Przykładem \<FileInfo > może być **_"C:\path\to\upload.VHD"_** .

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opcja 2: używanie AzCopy do przekazywania pliku VHD

Za pomocą AzCopy można łatwo przekazać wirtualny dysk twardy za pośrednictwem Internetu. W zależności od rozmiaru dysków VHD może to zająć trochę czasu. Należy pamiętać o sprawdzeniu limitów ruchu przychodzącego/wychodzącego kont magazynu przy użyciu tej opcji. Aby uzyskać szczegółowe informacje [, zobacz cele dotyczące skalowalności i wydajności dla kont magazynu w warstwie Standardowa](scalability-targets-standard-account.md) .

1. Pobierz i zainstaluj AzCopy z tego miejsca: [Najnowsza wersja AzCopy](https://aka.ms/downloadazcopy)
2. Otwórz Azure PowerShell i przejdź do folderu, w którym zainstalowano AzCopy.
3. Użyj poniższego polecenia, aby skopiować plik VHD z lokalizacji "source" do "Destination".

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Przykład:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Poniżej przedstawiono opisy parametrów używanych w AzCopy polecenia:

   * **/Source:** _&lt;źródła&gt;:_ Lokalizacja folderu lub adresu URL kontenera magazynu zawierającego dysk VHD.
   * **/SourceKey:** _&lt;konto źródłowe — klucz&gt;:_ klucz konta magazynu źródłowego konta magazynu.
   * **/Dest:** _&lt;Destination&gt;:_ adres URL kontenera magazynu, do którego ma zostać skopiowany dysk VHD.
   * **/DestKey:** _&lt;klucz docelowy&gt;:_ klucz konta magazynu docelowego konta magazynu.
   * **/BlobType: Strona:** Określa, że miejsce docelowe jest stronicowym obiektem BLOB.
   * **/Pattern:** _&lt;nazwa pliku&gt;:_ Określ nazwę pliku wirtualnego dysku twardego do skopiowania.

Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia AzCopy, zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Inne opcje przekazywania dysku VHD
Możesz również przekazać dysk VHD do konta magazynu przy użyciu jednego z następujących sposobów:

* [Interfejs API kopiowania obiektów BLOB usługi Azure Storage](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Eksplorator usługi Azure Storage przekazywanie obiektów BLOB](https://azurestorageexplorer.codeplex.com/)
* [Dokumentacja interfejsu API REST usługi Magazyn importowania/eksportowania](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Zalecamy korzystanie z usługi Import/Export, jeśli szacowany czas przekazywania jest dłuższy niż 7 dni. Możesz użyć [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) , aby oszacować czas od rozmiaru danych i jednostki transferu.
>
> Za pomocą importu/eksportu można kopiować do konta magazynu w warstwie Standardowa. Należy skopiować ze standardowego magazynu do konta magazynu w warstwie Premium przy użyciu narzędzia, takiego jak AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Tworzenie maszyn wirtualnych platformy Azure przy użyciu Premium Storage
Po przekazaniu lub skopiowaniu wirtualnego dysku twardego do żądanego konta magazynu postępuj zgodnie z instrukcjami w tej sekcji, aby zarejestrować dysk VHD jako obraz systemu operacyjnego lub dysk systemu operacyjnego w zależności od danego scenariusza, a następnie utwórz z niego wystąpienie maszyny wirtualnej. Dysk VHD dysku danych można dołączyć do maszyny wirtualnej po jej utworzeniu.
Przykładowy skrypt migracji znajduje się na końcu tej sekcji. Ten prosty skrypt nie jest zgodny ze wszystkimi scenariuszami. Może być konieczne zaktualizowanie skryptu w celu dopasowania go do danego scenariusza. Aby sprawdzić, czy ten skrypt ma zastosowanie do danego scenariusza, zobacz poniżej [przykładowego skryptu migracji](#a-sample-migration-script).

### <a name="checklist"></a>Lista kontrolna
1. Poczekaj na ukończenie wszystkich kopii dysków VHD.
2. Upewnij się, że Premium Storage jest dostępny w regionie, do którego chcesz przeprowadzić migrację.
3. Wybierz nową serię maszyn wirtualnych, która będzie używana. Powinno to być możliwe Premium Storage, a rozmiar powinien być zależny od dostępności w regionie i zależnie od potrzeb.
4. Określ dokładny rozmiar maszyny wirtualnej, który będzie używany. Rozmiar maszyny wirtualnej musi być wystarczająco duży, aby można było obsługiwać liczbę posiadanych dysków z danymi. Przykład: Jeśli masz 4 dyski danych, maszyna wirtualna musi mieć co najmniej 2 rdzenie. Należy również wziąć pod uwagę wymagania dotyczące mocy obliczeniowej, pamięci i przepustowości sieci.
5. Utwórz konto Premium Storage w regionie docelowym. Jest to konto, które będzie używane dla nowej maszyny wirtualnej.
6. Szczegóły bieżącej maszyny wirtualnej są przydatne, w tym listę dysków i odpowiadające im obiekty blob wirtualnego dysku twardego.

Przygotuj swoją aplikację do przestoju. W celu przeprowadzenia czystej migracji należy zatrzymać wszystkie procesy w bieżącym systemie. Dopiero wtedy można uzyskać spójny stan, który można migrować do nowej platformy. Czas przestoju będzie zależeć od ilości danych na dyskach do migracji.

> [!NOTE]
> Jeśli tworzysz maszynę wirtualną Azure Resource Manager z wyspecjalizowanego dysku VHD, zapoznaj się z [tym szablonem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) , aby wdrożyć maszynę wirtualną Menedżer zasobów przy użyciu istniejącego dysku.
>
>

### <a name="register-your-vhd"></a>Rejestrowanie wirtualnego dysku twardego
Aby utworzyć maszynę wirtualną na podstawie wirtualnego dysku twardego systemu operacyjnego lub dołączyć dysk danych do nowej maszyny wirtualnej, należy najpierw zarejestrować ją. Wykonaj poniższe czynności w zależności od scenariusza wirtualnego dysku twardego.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony wirtualny dysk twardy systemu operacyjnego do tworzenia wielu wystąpień maszyn wirtualnych platformy Azure
Po przekazaniu uogólnionego dysku VHD obrazu systemu operacyjnego do konta magazynu zarejestruj go jako **obraz maszyny wirtualnej platformy Azure** , aby można było utworzyć co najmniej jedno wystąpienie maszyny wirtualnej. Użyj następujących poleceń cmdlet programu PowerShell do zarejestrowania wirtualnego dysku twardego jako obrazu systemu operacyjnego maszyny wirtualnej platformy Azure. Podaj pełny adres URL kontenera, do którego skopiowano wirtualny dysk twardy.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Skopiuj i Zapisz nazwę tego nowego obrazu maszyny wirtualnej platformy Azure. W powyższym przykładzie jest to *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowy wirtualny dysk twardy systemu operacyjnego do utworzenia pojedynczego wystąpienia maszyny wirtualnej platformy Azure
Po przekazaniu unikatowego wirtualnego dysku twardego systemu operacyjnego do konta magazynu zarejestruj go jako **dysk systemu operacyjnego Azure** , aby można było utworzyć wystąpienie maszyny wirtualnej. Użyj tych poleceń cmdlet programu PowerShell, aby zarejestrować dysk VHD jako dysk systemu operacyjnego Azure. Podaj pełny adres URL kontenera, do którego skopiowano wirtualny dysk twardy.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Skopiuj i Zapisz nazwę tego nowego dysku systemu operacyjnego Azure. W powyższym przykładzie jest to *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dysk VHD z danymi do dołączenia do nowych wystąpień maszyn wirtualnych platformy Azure
Po przekazaniu dysku VHD z danymi do konta magazynu zarejestruj go jako dysk danych platformy Azure, aby można było go dołączyć do nowej serii DS, serii DSv2 lub wystąpienia maszyn wirtualnych platformy Azure z serii GS.

Użyj tych poleceń cmdlet programu PowerShell, aby zarejestrować dysk VHD jako dysk danych platformy Azure. Podaj pełny adres URL kontenera, do którego skopiowano wirtualny dysk twardy.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Skopiuj i Zapisz nazwę tego nowego dysku danych platformy Azure. W powyższym przykładzie jest to *dysk*danych.

### <a name="create-a-premium-storage-capable-vm"></a>Tworzenie maszyny wirtualnej z możliwością Premium Storage
Po zarejestrowaniu obrazu systemu operacyjnego lub dysku systemu operacyjnego Utwórz nową maszynę wirtualną serii DS, DSv2 lub GS. Będziesz używać zarejestrowanego obrazu systemu operacyjnego lub nazwy dysku systemu operacyjnego. Wybierz typ maszyny wirtualnej z warstwy Premium Storage. W poniższym przykładzie używamy rozmiaru maszyny wirtualnej *Standard_DS2* .

> [!NOTE]
> Zaktualizuj rozmiar dysku, aby upewnić się, że spełnia on wymagania dotyczące pojemności i wydajności oraz dostępne rozmiary dysku platformy Azure.
>
>

Postępuj zgodnie z poleceniami cmdlet programu PowerShell krok po kroku, aby utworzyć nową maszynę wirtualną. Najpierw ustaw wspólne parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Najpierw Utwórz usługę w chmurze, w której będziesz obsługiwać nowe maszyny wirtualne.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Następnie w zależności od scenariusza Utwórz wystąpienie maszyny wirtualnej platformy Azure z poziomu zarejestrowanego obrazu systemu operacyjnego lub dysku systemu operacyjnego.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony wirtualny dysk twardy systemu operacyjnego do tworzenia wielu wystąpień maszyn wirtualnych platformy Azure
Utwórz co najmniej jedno nowe wystąpienie maszyny wirtualnej platformy Azure z serii DS za pomocą zarejestrowanego **obrazu systemu operacyjnego Azure** . Podaj nazwę tego obrazu systemu operacyjnego w konfiguracji maszyny wirtualnej podczas tworzenia nowej maszyny wirtualnej, jak pokazano poniżej.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowy wirtualny dysk twardy systemu operacyjnego do utworzenia pojedynczego wystąpienia maszyny wirtualnej platformy Azure
Utwórz nowe wystąpienie maszyny wirtualnej platformy Azure z serii DS przy użyciu zarejestrowanego **dysku systemu operacyjnego Azure** . Określ nazwę dysku systemu operacyjnego w konfiguracji maszyny wirtualnej podczas tworzenia nowej maszyny wirtualnej, jak pokazano poniżej.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Określ inne informacje o maszynie wirtualnej platformy Azure, takie jak usługa w chmurze, region, konto magazynu, zestaw dostępności i zasady buforowania. Należy pamiętać, że wystąpienie maszyny wirtualnej musi znajdować się wspólnie z skojarzonym systemem operacyjnym lub dyskami z danymi, więc wybrana usługa w chmurze, region i konto magazynu muszą znajdować się w tej samej lokalizacji co bazowe dyski VHD tych dysków.

### <a name="attach-data-disk"></a>Dołączanie dysku danych
Wreszcie, jeśli masz zarejestrowane dyski VHD z danymi, Dołącz je do nowej maszyny wirtualnej platformy Azure z możliwością Premium Storage.

Użyj następującego polecenia cmdlet programu PowerShell, aby dołączyć dysk danych do nowej maszyny wirtualnej i określić zasady buforowania. W przykładzie poniżej zasad buforowania ustawiono wartość tylko do *odczytu*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Może być konieczne wykonanie dodatkowych czynności w celu obsługi aplikacji, które nie są objęte tym przewodnikiem.
>
>

### <a name="checking-and-plan-backup"></a>Sprawdzanie i Planowanie kopii zapasowej
Gdy nowa maszyna wirtualna jest uruchomiona, uzyskuj dostęp do niej przy użyciu tego samego identyfikatora logowania i hasła jako oryginalnej maszyny wirtualnej i sprawdź, czy wszystko działa zgodnie z oczekiwaniami. Wszystkie ustawienia, w tym woluminy rozłożone, będą obecne w nowej maszynie wirtualnej.

Ostatnim krokiem jest zaplanowanie harmonogramu tworzenia kopii zapasowych i konserwacji dla nowej maszyny wirtualnej w zależności od potrzeb aplikacji.

### <a name="a-sample-migration-script"></a>Przykładowy skrypt migracji
Jeśli masz wiele maszyn wirtualnych do migracji, będzie przydatne Automatyzacja za pośrednictwem skryptów programu PowerShell. Poniżej znajduje się przykładowy skrypt, który automatyzuje migrację maszyny wirtualnej. Należy zauważyć, że Poniższy skrypt jest tylko przykładem i istnieje kilka założeń dotyczących bieżących dysków maszyny wirtualnej. Może być konieczne zaktualizowanie skryptu w celu dopasowania go do danego scenariusza.

Założenia są następujące:

* Tworzysz klasyczne maszyny wirtualne platformy Azure.
* Źródłowe dyski systemu operacyjnego i dyski danych źródłowych znajdują się na tym samym koncie magazynu i w tym samym kontenerze. Jeśli dyski systemu operacyjnego i dyski z danymi nie znajdują się w tym samym miejscu, możesz użyć AzCopy lub Azure PowerShell do kopiowania dysków VHD za pośrednictwem kont magazynu i kontenerów. Zapoznaj się z poprzednim krokiem: [Kopiuj dysk VHD za pomocą AzCopy lub PowerShell](#copy-vhd-with-azcopy-or-powershell). Edytowanie tego skryptu w celu spełnienia Twojego scenariusza jest kolejnym wyborem, ale zalecamy korzystanie z programu AzCopy lub PowerShell, ponieważ jest to łatwiejsze i szybsze.

Skrypt automatyzacji jest przedstawiony poniżej. Zastąp tekst informacjami i zaktualizuj skrypt tak, aby był zgodny z konkretnym scenariuszem.

> [!NOTE]
> Użycie istniejącego skryptu nie zachowuje konfiguracji sieci źródłowej maszyny wirtualnej. Należy ponownie zmienić konfigurację ustawień sieciowych na zmigrowanych maszynach wirtualnych.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optymalizacja
Bieżącą konfigurację maszyny wirtualnej można dostosować w taki sposób, aby działała prawidłowo z dyskami standardowymi. Na przykład, aby zwiększyć wydajność przy użyciu wielu dysków w woluminie rozłożonym. Na przykład zamiast korzystania z 4 dysków oddzielnie na Premium Storage, można zoptymalizować koszty, korzystając z jednego dysku. Optymalizacje podobne do tego muszą być obsługiwane w przypadku wielkości liter i wymagają wykonania niestandardowych czynności po migracji. Należy również pamiętać, że ten proces może nie współpracować z bazami danych i aplikacjami, które są zależne od układu dysku zdefiniowanego w instalatorze.

##### <a name="preparation"></a>Przygotowywanie
1. Wykonaj prostą migrację zgodnie z opisem w poprzedniej sekcji. Optymalizacje zostaną wykonane na nowej maszynie wirtualnej po migracji.
2. Zdefiniuj nowe rozmiary dysków, które są odpowiednie dla zoptymalizowanej konfiguracji.
3. Określ mapowanie bieżących dysków/woluminów na nowe specyfikacje dysków.

##### <a name="execution-steps"></a>Kroki wykonywania
1. Utwórz nowe dyski o odpowiednich rozmiarach na maszynie wirtualnej Premium Storage.
2. Zaloguj się do maszyny wirtualnej i skopiuj dane z bieżącego woluminu na nowy dysk, który jest mapowany na ten wolumin. Zrób to dla wszystkich bieżących woluminów, które muszą być mapowane na nowy dysk.
3. Następnie zmień ustawienia aplikacji, aby przełączyć się na nowe dyski i odłączyć stare woluminy.

Aby dostroić aplikację w celu uzyskania lepszej wydajności dysku, zapoznaj się z sekcją Optymalizacja wydajności aplikacji w artykule [projektowanie pod kątem wysokiej wydajności](../../virtual-machines/windows/premium-storage-performance.md) artykułu.

### <a name="application-migrations"></a>Migracje aplikacji
Bazy danych i inne złożone aplikacje mogą wymagać specjalnych kroków określonych przez dostawcę aplikacji do migracji. Zapoznaj się z dokumentacją odpowiedniej aplikacji. Przykład: zazwyczaj bazy danych można migrować poprzez wykonywanie kopii zapasowych i przywracanie.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi zasobami dla konkretnych scenariuszy migrowania maszyn wirtualnych:

* [Migrowanie Virtual Machines platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Utwórz i przekaż wirtualny dysk twardy systemu Windows Server na platformę Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tworzenie i przekazywanie wirtualnego dysku twardego z systemem Linux do platformy Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie Virtual Machines z usługi Amazon AWS do Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zobacz też następujące zasoby, aby dowiedzieć się więcej na temat usługi Azure Storage i platformy Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Wybierz typ dysku dla maszyn wirtualnych IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
