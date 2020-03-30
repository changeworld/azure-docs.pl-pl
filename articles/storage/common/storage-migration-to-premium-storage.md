---
title: Migrowanie maszyn wirtualnych do usługi Azure Premium Storage | Dokumenty firmy Microsoft
description: Migrowanie istniejących maszyn wirtualnych do usługi Azure Premium Storage. Magazyn w wersji Premium oferuje wysokowydajną obsługę dysku o niskim opóźnieniu dla obciążeń intensywnie korzystających z we/wy uruchomionych na maszynach wirtualnych platformy Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748350"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migracja do usługi Azure Premium Storage (dyski niezarządzane)

> [!NOTE]
> W tym artykule omówiono sposób migracji maszyny Wirtualnej, która używa niezarządzanych dysków standardowych do maszyny Wirtualnej, która używa niezarządzanych dysków premium. Zaleca się używanie dysków zarządzanych platformy Azure dla nowych maszyn wirtualnych i konwertowanie poprzednich dysków niezarządzanych na dyski zarządzane. Dyski zarządzane obsługują podstawowe konta magazynu, więc nie musisz tego robić. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem dysków zarządzanych.](../../virtual-machines/windows/managed-disks-overview.md)
>

Usługa Azure Premium Storage zapewnia wysoką wydajność i małe opóźnienia obsługi dysku dla maszyn wirtualnych z obciążeniami intensywnie korzystających z we/wy. Możesz skorzystać z szybkości i wydajności tych dysków, migrując dyski maszyn wirtualnych aplikacji do usługi Azure Premium Storage.

Celem tego przewodnika jest pomoc nowym użytkownikom usługi Azure Premium Storage w lepszym przygotowaniu się do płynnego przejścia z bieżącego systemu do magazynu w ramach usługi Premium. Przewodnik dotyczy trzech kluczowych składników w tym procesie:

* [Planowanie migracji do magazynu w wersji premium](#plan-the-migration-to-premium-storage)
* [Przygotowywanie i kopiowanie wirtualnych dysków twardych (VHD) do magazynu w wersji premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Tworzenie maszyny wirtualnej platformy Azure przy użyciu magazynu w wersji Premium](#create-azure-virtual-machine-using-premium-storage)

Maszyny wirtualne z innych platform można migrować z innych platform do usługi Azure Premium Storage lub migrować istniejące maszyny wirtualne platformy Azure ze standardowego magazynu do magazynu w wersji Premium. W tym przewodniku opisano kroki dla obu scenariuszy. Wykonaj kroki określone w odpowiedniej sekcji w zależności od scenariusza.

> [!NOTE]
> Przegląd funkcji i ceny dysków SSD premium można znaleźć w: [Wybierz typ dysku dla maszyn wirtualnych IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Firma Microsoft zaleca migrację dowolnego dysku maszyny wirtualnej wymagającego wysokiej usługi We/Wy do usługi Azure Premium Storage w celu uzyskania najlepszej wydajności dla aplikacji. Jeśli dysk nie wymaga wysokich we/wy, można ograniczyć koszty, utrzymując go w magazynie standardowym, który przechowuje dane dysku maszyny wirtualnej na dyskach twardych (HDD) zamiast dysków SSD.
>

Ukończenie procesu migracji w całości może wymagać dodatkowych działań zarówno przed, jak i po wykonaniu kroków podanych w tym przewodniku. Przykłady obejmują konfigurowanie sieci wirtualnych lub punktów końcowych lub wprowadzanie zmian kodu w samej aplikacji, które mogą wymagać pewnego przestoju w aplikacji. Te akcje są unikatowe dla każdej aplikacji i należy je wykonać wraz z krokami podanymi w tym przewodniku, aby pełne przejście do magazynu w wersji Premium było tak bezproblemowe, jak to możliwe.

## <a name="plan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>Planowanie migracji do magazynu w wersji Premium
W tej sekcji upewnij się, że można wykonać kroki migracji w tym artykule i pomaga w podejmowaniu najlepszych decyzji w sprawie typów maszyn wirtualnych i dysków.

### <a name="prerequisites"></a>Wymagania wstępne
* Konieczna będzie subskrypcja platformy Azure. Jeśli go nie masz, możesz utworzyć miesięczną [bezpłatną](https://azure.microsoft.com/pricing/free-trial/) subskrypcję próbną lub odwiedzić stronę [Cennik platformy Azure, aby](https://azure.microsoft.com/pricing/) uzyskać więcej opcji.
* Aby wykonać polecenia cmdlet programu PowerShell, potrzebny będzie moduł programu Microsoft Azure PowerShell. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Gdy planujesz używać maszyn wirtualnych platformy Azure uruchomionych w magazynie w klasie premium, musisz użyć maszyn wirtualnych obsługujących magazyn w klasie premium. Można używać zarówno dysków magazynu standardowego, jak i magazynu w wersji premium z maszynami wirtualnymi obsługującymi magazyn premium. Dyski magazynu w wersji premium będą dostępne z większą liczcią typów maszyn wirtualnych w przyszłości. Aby uzyskać więcej informacji na temat wszystkich dostępnych typów i rozmiarów dysków maszyn wirtualnych platformy Azure, zobacz [Rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [rozmiary usług w chmurze.](../../cloud-services/cloud-services-sizes-specs.md)

### <a name="considerations"></a>Zagadnienia do rozważenia
Maszyna wirtualna platformy Azure obsługuje dołączanie kilku dysków magazynu w wersji Premium, dzięki czemu aplikacje mogą mieć maksymalnie 256 TB magazynu na maszynę wirtualną. Dzięki magazynowi w wersji Premium aplikacje mogą osiągnąć 80 000 operacji we/wydziale we/wydziale we/wydziale informacji na sekundę na maszynę wirtualną i 2000 MB na sekundę przepływności dysku na maszynę wirtualną przy bardzo niskich opóźnieniach dla operacji odczytu. Dostępne są opcje w różnych maszynach wirtualnych i dyskach. W tej sekcji warto znaleźć opcję, która najlepiej odpowiada twojemu obciążeniu.

#### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych
Specyfikacje rozmiaru maszyny wirtualnej platformy Azure są wymienione w [rozmiary dla maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Przejrzyj charakterystykę wydajności maszyn wirtualnych, które współpracują z magazynem w wersji Premium i wybierz najbardziej odpowiedni rozmiar maszyny wirtualnej, który najlepiej odpowiada twojemu obciążeniu. Upewnij się, że na maszynie wirtualnej jest dostępna wystarczająca przepustowość, aby zwiększyć ruch na dysku.

#### <a name="disk-sizes"></a>Rozmiary dysków
Istnieje pięć typów dysków, które mogą być używane z maszyną wirtualną i każdy ma określone IOP i limity przepływności. Należy wziąć pod uwagę te limity przy wyborze typu dysku dla maszyny Wirtualnej na podstawie potrzeb aplikacji pod względem pojemności, wydajności, skalowalności i obciążenia szczytowego.

| Typ dysków premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Rozmiar dysku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Przepływność na dysk | 100 MB na sekundę | 150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę |

W zależności od obciążenia należy określić, czy dodatkowe dyski danych są niezbędne dla maszyny Wirtualnej. Do maszyny wirtualnej można dołączyć kilka dysków z danymi trwałymi. W razie potrzeby można rozbierać się po dyskach, aby zwiększyć pojemność i wydajność woluminu. (Zobacz, co to jest Disk Striping [tutaj](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) W przypadku rozbierania dysków danych magazynu w wersji Premium przy użyciu [funkcji Miejsca do magazynowania][4]należy skonfigurować je z jedną kolumną dla każdego używanego dysku. W przeciwnym razie ogólna wydajność woluminu rozłożonego może być niższa niż oczekiwano z powodu nierównomiernego rozkładu ruchu na dyskach. W przypadku maszyn wirtualnych z systemem Linux można użyć narzędzia *mdadm,* aby osiągnąć to samo. Szczegółowe informacje można [znaleźć w artykule Konfigurowanie macierzy RAID oprogramowania w systemie Linux.](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

#### <a name="storage-account-scalability-targets"></a>Cele skalowalności konta magazynu

Konta magazynu w wersji Premium mają następujące cele skalowalności. Jeśli wymagania aplikacji przekraczają cele skalowalności pojedynczego konta magazynu, skompiluj aplikację do używania wielu kont magazynu i rozdzielaj dane na te konta magazynu.

| Całkowita zdolność produkcyjna konta | Całkowita przepustowość dla lokalnie nadmiarowego konta magazynu |
|:--- |:--- |
| Pojemność dysku: 35 TB<br />Pojemność migawki: 10 TB |Do 50 gigabitów na sekundę dla ruchu przychodzącego + wychodzącego |

Aby uzyskać więcej informacji na temat specyfikacji magazynu w wersji Premium, zobacz [Cele skalowalności dla kont magazynu obiektów blob strony premium.](../blobs/scalability-targets-premium-page-blobs.md)

#### <a name="disk-caching-policy"></a>Zasady buforowania dysków
Domyślnie zasady buforowania dysków to *Tylko do odczytu* dla wszystkich dysków z danymi w układzie premium i *odczyt i zapis* dla dysku systemu operacyjnego Premium podłączonego do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane w celu osiągnięcia optymalnej wydajności dla aplikacji we/wy. W przypadku dysków danych z dużą wydajnością lub tylko do zapisu (takich jak pliki dziennika programu SQL Server) należy wyłączyć buforowanie dysków, aby uzyskać lepszą wydajność aplikacji. Ustawienia pamięci podręcznej dla istniejących dysków danych można zaktualizować przy użyciu [portalu Azure](https://portal.azure.com) lub *parametru -HostCaching* polecenia cmdlet *Set-AzureDataDisk.*

#### <a name="location"></a>Lokalizacja
Wybierz lokalizację, w której usługa Azure Premium Storage jest dostępna. Aby uzyskać aktualne informacje o dostępnych lokalizacjach, zobacz Usługi platformy Azure według [regionów.](https://azure.microsoft.com/regions/#services) Maszyny wirtualne znajdujące się w tym samym regionie co konto magazynu, które przechowuje dyski dla maszyny Wirtualnej, zapewni znacznie lepszą wydajność niż w oddzielnych regionach.

#### <a name="other-azure-vm-configuration-settings"></a>Inne ustawienia konfiguracji maszyny Wirtualnej platformy Azure
Podczas tworzenia maszyny Wirtualnej platformy Azure zostanie wyświetlony monit o skonfigurowanie niektórych ustawień maszyny Wirtualnej. Pamiętaj, że kilka ustawień są stałe dla okresu istnienia maszyny Wirtualnej, podczas gdy można modyfikować lub dodawać inne później. Przejrzyj te ustawienia konfiguracji maszyny Wirtualnej platformy Azure i upewnij się, że są one skonfigurowane odpowiednio do wymagań obciążenia.

### <a name="optimization"></a>Optymalizacja
[Usługa Azure Premium Storage: Projektowanie dla wysokiej wydajności](../../virtual-machines/windows/premium-storage-performance.md) zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Można postępować zgodnie z wytycznymi w połączeniu z najlepszymi praktykami dotyczącymi wydajności mającymi zastosowanie do technologii używanych przez aplikację.

## <a name="prepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Przygotowywanie i kopiowanie wirtualnych dysków twardych (VHD) do magazynu premium
Poniższa sekcja zawiera wskazówki dotyczące przygotowywania dysków wirtualnych z maszyny wirtualnej i kopiowania dysków wirtualnych do usługi Azure Storage.

* [Scenariusz 1: "Migruję istniejące maszyny wirtualne platformy Azure do usługi Azure Premium Storage".](#scenario1)
* [Scenariusz 2: "Migruję maszyny wirtualne z innych platform do usługi Azure Premium Storage".](#scenario2)

### <a name="prerequisites"></a>Wymagania wstępne
Aby przygotować ved do migracji, należy:

* Subskrypcja platformy Azure, konto magazynu i kontener na tym koncie magazynu, do którego można skopiować dysk VHD. Należy pamiętać, że docelowe konto magazynu może być kontem magazynu standardowego lub magazynu w wersji Premium w zależności od wymagań.
* Narzędzie do uogólniania dysku VHD, jeśli planujesz utworzyć wiele wystąpień maszyn wirtualnych z niego. Na przykład sysprep dla systemu Windows lub virt-sysprep dla Ubuntu.
* Narzędzie do przekazywania pliku VHD na konto Magazynu. Zobacz [Przenoszenie danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md) lub użyj [Eksploratora magazynu platformy Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). W tym przewodniku opisano kopiowanie dysku VHD za pomocą narzędzia AzCopy.

> [!NOTE]
> Jeśli wybierzesz opcję kopiowania synchronicznego z AzCopy, aby uzyskać optymalną wydajność, skopiuj dysk VHD, uruchamiając jedno z tych narzędzi z maszyny Wirtualnej platformy Azure, która znajduje się w tym samym regionie co konto magazynu docelowego. Jeśli kopiujesz dysk wirtualny z maszyny Wirtualnej platformy Azure w innym regionie, wydajność może być wolniejsza.
>
> Aby skopiować dużą ilość danych o ograniczonej przepustowości, należy rozważyć [użycie usługi Azure Import/Export do przesyłania danych do magazynu obiektów Blob;](../storage-import-export-service.md) umożliwia to przesyłanie danych przez wysyłkę dysków twardych do centrum danych platformy Azure. Za pomocą usługi Azure Import/Export można kopiować dane tylko na standardowe konto magazynu. Gdy dane są na koncie magazynu standardowego, można użyć [interfejsu API kopiowania obiektów blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) lub AzCopy do przeniesienia danych na konto magazynu w wersji premium.
>
> Należy zauważyć, że platforma Microsoft Azure obsługuje tylko pliki VHD o stałym rozmiarze. Pliki VHDX lub dynamiczne dyski VHD nie są obsługiwane. Jeśli masz dynamiczny dysk VHD, możesz go przekonwertować na stały rozmiar za pomocą polecenia cmdlet [Convert-VHD.](https://technet.microsoft.com/library/hh848454.aspx)
>
>

### <a name="scenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>Scenariusz 1: "Migruję istniejące maszyny wirtualne platformy Azure do usługi Azure Premium Storage".
Jeśli przeprowadzasz migrację istniejących maszyn wirtualnych platformy Azure, zatrzymaj maszynę wirtualną, przygotuj dyski VHD według typu dysku VHD, a następnie skopiuj dysk VHD za pomocą programu AzCopy lub Programu PowerShell.

Maszyna wirtualna musi być całkowicie w dół, aby przeprowadzić migrację czystego stanu. Nastąpi przestój do czasu zakończenia migracji.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Przygotowanie ved do migracji
Jeśli przeprowadzasz migrację istniejących maszyn wirtualnych platformy Azure do magazynu w wersji Premium, dysk VHD może być:

* Uogólniony obraz systemu operacyjnego
* Unikatowy dysk systemu operacyjnego
* Dysk danych

Poniżej przedstawiamy te 3 scenariusze przygotowania dysku VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Użyj uogólnionego dysku VHD systemu operacyjnego, aby utworzyć wiele wystąpień maszyn wirtualnych
Jeśli przekazujesz dysk wirtualny, który będzie używany do tworzenia wielu ogólnych wystąpień maszyn wirtualnych platformy Azure, należy najpierw uogólnić dysk VHD przy użyciu narzędzia sysprep. Dotyczy to dysku VHD, który jest lokalny lub w chmurze. Program Sysprep usuwa wszelkie informacje specyficzne dla komputera z dysku VHD.

> [!IMPORTANT]
> Wykonaj migawkę lub utwórz kopię zapasową maszyny Wirtualnej przed uogólnieniem jej. Uruchamianie sysprep zatrzyma i cofnięto alokację wystąpienia maszyny Wirtualnej. Wykonaj poniższe czynności, aby sysprep windows OS VHD. Należy zauważyć, że uruchomienie polecenia Sysprep będzie wymagać zamknięcia maszyny wirtualnej. Aby uzyskać więcej informacji na temat sysprep, zobacz [Przegląd Sysprep](https://technet.microsoft.com/library/hh825209.aspx) lub [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otwórz okno wiersza polecenia jako administrator.
2. Wprowadź następujące polecenie, aby otworzyć program Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. W narzędziu Przygotowanie systemu wybierz pozycję Wprowadź środowisko po wyjęciu z pola wyboru (OOBE), zaznacz pole wyboru Generalize, wybierz pozycję **Zamknięcie,** a następnie kliknij przycisk **OK**, jak pokazano na poniższej ilustracji. Sysprep uogólni system operacyjny i wyłączy system.

    ![][1]

W przypadku maszyny Wirtualnej Ubuntu użyj virt-sysprep, aby osiągnąć to samo. Zobacz [virt-sysprep,](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) aby uzyskać więcej informacji. Zobacz też niektóre oprogramowanie do [inicjowania obsługi administracyjnej serwera linux](https://www.cyberciti.biz/tips/server-provisioning-software.html) open source dla innych systemów operacyjnych Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Tworzenie pojedynczego wystąpienia maszyny Wirtualnej za pomocą unikatowego dysku twardego systemu operacyjnego
Jeśli masz aplikację uruchomioną na maszynie Wirtualnej, która wymaga danych specyficznych dla komputera, nie uogólniaj dysku VHD. Nieogólniony dysk wirtualny może służyć do tworzenia unikatowego wystąpienia maszyny Wirtualnej platformy Azure. Na przykład jeśli masz kontroler domeny na dysku VHD, wykonanie sysprep spowoduje, że będzie on nieskuteczny jako kontroler domeny. Przejrzyj aplikacje uruchomione na maszynie wirtualnej i wpływ uruchamiania sysprep na nich przed uogólnieniem dysku VHD.

##### <a name="register-data-disk-vhd"></a>Rejestrowanie dysku danych VHD
Jeśli masz dyski danych na platformie Azure do migracji, należy upewnić się, że maszyny wirtualne, które używają tych dysków danych są zamknięte.

Wykonaj kroki opisane poniżej, aby skopiować dysk WIRTUALNY do usługi Azure Premium Storage i zarejestrować go jako aprowizowany dysk danych.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Tworzenie miejsca docelowego dla dysku VHD
Utwórz konto magazynu do obsługi dysków VHD. Podczas planowania przechowywania vhdów należy wziąć pod uwagę następujące kwestie:

* Docelowe konto magazynu w klasie Premium.
* Lokalizacja konta magazynu musi być taka sama jak maszyny wirtualne platformy Azure obsługujące usługę Azure z obsługą usługi Premium Storage, które zostaną utworzone w końcowym etapie. Możesz skopiować na nowe konto magazynu lub zaplanować użycie tego samego konta magazynu w zależności od potrzeb.
* Skopiuj i zapisz klucz konta magazynu docelowego dla następnego etapu.

W przypadku dysków z danymi można zachować niektóre dyski danych na standardowym koncie magazynu (na przykład dyski, które mają chłodniejszy magazyn), ale zdecydowanie zaleca się przeniesienie wszystkich danych do obciążenia produkcyjnego w celu użycia magazynu w stanie Premium.

#### <a name="step-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Kopiowanie dysku VHD za pomocą programu AzCopy lub PowerShell
Aby przetworzyć jedną z tych dwóch opcji, należy znaleźć ścieżkę kontenera i klucz konta magazynu. Ścieżka kontenera i klucz konta magazynu można znaleźć w **usłudze Azure Portal** > **Storage**. Adres URL kontenera będzie podobny\/do "https: /myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opcja 1: Kopiowanie VHD z AzCopy (kopia asynchronicznie)

Za pomocą AzCopy, można łatwo przesłać VHD przez Internet. W zależności od wielkości VHD, może to zająć trochę czasu. Pamiętaj, aby sprawdzić limity ruchu przychodzącego/wychodzącego konta magazynu podczas korzystania z tej opcji. Szczegółowe informacje można [znaleźć w przypadku celów skalowalności i wydajności dla standardowych kont magazynu.](scalability-targets-standard-account.md)

1. Pobierz i zainstaluj AzCopy stąd: [Najnowsza wersja AzCopy](https://aka.ms/downloadazcopy)
2. Otwórz program Azure PowerShell i przejdź do folderu, w którym jest zainstalowany program AzCopy.
3. Użyj następującego polecenia, aby skopiować plik VHD z "Source" do "Destination".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Przykład:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Oto opisy parametrów używanych w poleceniu AzCopy:

   * **/Source:** _ &lt;&gt;source :_ Lokalizacja adresu URL folderu lub kontenera magazynu zawierającego dysk VHD.
   * **/SourceKey:** _ &lt;source-account-key:&gt;_ Klucz konta magazynu na źródłowym koncie magazynu.
   * **/Dest:** _ &lt;&gt;destination :_ Adres URL kontenera magazynu, do aby skopiować dysk VHD.
   * **/DestKey:** _ &lt;dest-account-key:&gt;_ Klucz konta magazynu docelowego konta magazynu.
   * **/Pattern:** _ &lt;&gt;nazwa pliku :_ Określ nazwę pliku VHD do skopiowania.

Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia AzCopy, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opcja 2: Kopiowanie dysku VHD za pomocą programu PowerShell (kopia zsynchronizowana)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Plik VHD można również skopiować za pomocą polecenia cmdlet PowerShell Start-AzStorageBlobCopy. Użyj następującego polecenia w programie Azure PowerShell, aby skopiować dysk VHD. Zastąp wartości w <> odpowiednimi wartościami z konta magazynu źródłowego i docelowego. Aby użyć tego polecenia, musisz mieć kontener o nazwie vhds na koncie magazynu docelowego. Jeśli kontener nie istnieje, utwórz go przed uruchomieniem polecenia.

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

### <a name="scenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>Scenariusz 2: "Migruję maszyny wirtualne z innych platform do usługi Azure Premium Storage".
W przypadku migracji dysku twardego z usługi Azure Storage niena platformy Azure należy najpierw wyeksportować dysk VHD do katalogu lokalnego. Mieć pełną ścieżkę źródłową katalogu lokalnego, w którym jest przechowywany dysk VHD, a następnie za pomocą AzCopy przekazać go do usługi Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Eksportowanie dysku twardego do katalogu lokalnego
##### <a name="copy-a-vhd-from-aws"></a>Kopiowanie dysku twardego z AWS
1. Jeśli używasz AWS, wyeksportuj wystąpienie EC2 do dysku VHD w wiadrze Amazon S3. Wykonaj kroki opisane w dokumentacji Amazon do eksportowania wystąpień Amazon EC2, aby zainstalować narzędzie interfejsu wiersza polecenia Amazon EC2 (CLI) i uruchomić polecenie create-instance-export-task, aby wyeksportować wystąpienie EC2 do pliku VHD. Pamiętaj, aby podczas uruchamiania polecenia **create-instance-export-task** używać **dysku VHD** dla zmiennej DISK&#95;IMAGE&#95;FORMAT. Wyeksportowany plik VHD jest zapisywany w wiadrze Amazon S3, które wyznaczasz podczas tego procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Pobierz plik VHD z wiadra S3. Wybierz plik VHD, a następnie **akcje** > **pobierz**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiowanie dysku twardego vhd z innej chmury innej niż azure
W przypadku migracji dysku twardego z usługi Azure Storage niena platformy Azure należy najpierw wyeksportować dysk VHD do katalogu lokalnego. Skopiuj pełną ścieżkę źródłową katalogu lokalnego, w którym jest przechowywany dysk VHD.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopiowanie dysku VHD z lokalnego
W przypadku migracji dysku twardego z środowiska lokalnego potrzebna jest pełna ścieżka źródłowsza, w której jest przechowywany dysk VHD. Ścieżka źródłona może być lokalizacją serwera lub udziałem plików.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Tworzenie miejsca docelowego dla dysku VHD
Utwórz konto magazynu do obsługi dysków VHD. Podczas planowania przechowywania vhdów należy wziąć pod uwagę następujące kwestie:

* Docelowe konto magazynu może być magazynem standardowym lub w wersji premium w zależności od wymagań aplikacji.
* Region konta magazynu musi być taki sam jak maszyny wirtualne platformy Azure obsługujące usługę Azure z obsługą usługi Premium Storage, które zostaną utworzone w końcowym etapie. Możesz skopiować na nowe konto magazynu lub zaplanować użycie tego samego konta magazynu w zależności od potrzeb.
* Skopiuj i zapisz klucz konta magazynu docelowego dla następnego etapu.

Zdecydowanie zaleca się przeniesienie wszystkich danych dla obciążenia produkcyjnego w celu użycia magazynu w wersji premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Przekazywanie dysku VHD do usługi Azure Storage
Teraz, gdy masz dysk VHD w katalogu lokalnym, możesz użyć AzCopy lub AzurePowerShell, aby przekazać plik vhd do usługi Azure Storage. Obie opcje są dostępne tutaj:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opcja 1: Przekazywanie pliku vhd za pomocą dodatku Azure PowerShell

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Przykładem \<> Uri może być **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Przykładowe \<> FileInfo mogą być **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opcja 2: Przesyłanie pliku vhd za pomocą AzCopy

Za pomocą AzCopy, można łatwo przesłać VHD przez Internet. W zależności od wielkości VHD, może to zająć trochę czasu. Pamiętaj, aby sprawdzić limity ruchu przychodzącego/wychodzącego konta magazynu podczas korzystania z tej opcji. Szczegółowe informacje można [znaleźć w przypadku celów skalowalności i wydajności dla standardowych kont magazynu.](scalability-targets-standard-account.md)

1. Pobierz i zainstaluj AzCopy stąd: [Najnowsza wersja AzCopy](https://aka.ms/downloadazcopy)
2. Otwórz program Azure PowerShell i przejdź do folderu, w którym jest zainstalowany program AzCopy.
3. Użyj następującego polecenia, aby skopiować plik VHD z "Source" do "Destination".

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Przykład:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Oto opisy parametrów używanych w poleceniu AzCopy:

   * **/Source:** _ &lt;&gt;source :_ Lokalizacja adresu URL folderu lub kontenera magazynu zawierającego dysk VHD.
   * **/SourceKey:** _ &lt;source-account-key:&gt;_ Klucz konta magazynu na źródłowym koncie magazynu.
   * **/Dest:** _ &lt;&gt;destination :_ Adres URL kontenera magazynu, do aby skopiować dysk VHD.
   * **/DestKey:** _ &lt;dest-account-key:&gt;_ Klucz konta magazynu docelowego konta magazynu.
   * **/BlobType: strona:** Określa, że miejscem docelowym jest obiekt blob strony.
   * **/Pattern:** _ &lt;&gt;nazwa pliku :_ Określ nazwę pliku VHD do skopiowania.

Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia AzCopy, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Inne opcje przesyłania dysku VHD
Dysk VHD można również przesłać na swoje konto magazynu, korzystając z jednego z następujących środków:

* [Interfejs API obiektów Blob kopiowania usługi Azure Storage](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Przekazywanie obiektów blob przez Eksploratora usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)
* [Odwołanie do interfejsu API interfejsu REST usługi importowania/eksportowania magazynu](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Zalecamy korzystanie z usługi importu/eksportu, jeśli szacowany czas przesyłania jest dłuższy niż 7 dni. Za pomocą [funkcji DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) można oszacować czas od jednostki rozmiaru danych i transferu.
>
> Import/Eksport można użyć do kopiowania na standardowe konto magazynu. Musisz skopiować ze standardowego magazynu do konta magazynu premium za pomocą narzędzia takiego jak AzCopy.
>
>

## <a name="create-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Tworzenie maszyn wirtualnych platformy Azure przy użyciu magazynu w wersji Premium
Po przekazaniu lub skopiowaniu dysku VHD na żądane konto magazynu postępuj zgodnie z instrukcjami w tej sekcji, aby zarejestrować dysk VHD jako obraz systemu operacyjnego lub dysk systemu operacyjnego w zależności od scenariusza, a następnie utworzyć z niego wystąpienie maszyny Wirtualnej. Dysk VHD dysku danych można podłączyć do maszyny Wirtualnej po jej utworzeniu.
Przykładowy skrypt migracji znajduje się na końcu tej sekcji. Ten prosty skrypt nie pasuje do wszystkich scenariuszy. Może być konieczne zaktualizowanie skryptu, aby dopasować go do określonego scenariusza. Aby sprawdzić, czy ten skrypt ma zastosowanie do scenariusza, zobacz poniżej [Przykładowy skrypt migracji](#a-sample-migration-script).

### <a name="checklist"></a>Lista kontrolna
1. Poczekaj, aż wszystkie dyski VHD kopiowanie zostanie zakończone.
2. Upewnij się, że magazyn w wersji Premium jest dostępny w regionie, do którego przeprowadzasz migrację.
3. Zdecyduj, że nowa seria maszyn wirtualnych będzie korzystać. Powinien być w stanie magazynu w wersji Premium, a rozmiar powinien zależeć od dostępności w regionie i na podstawie twoich potrzeb.
4. Zdecyduj dokładny rozmiar maszyny Wirtualnej, którego będziesz używać. Rozmiar maszyny Wirtualnej musi być wystarczająco duży, aby obsługiwać liczbę dysków z danymi. Na przykład Jeśli masz 4 dyski danych, maszyna wirtualna musi mieć 2 lub więcej rdzeni. Należy również wziąć pod uwagę zapotrzebowanie na moc obliczeniową, pamięć i przepustowość sieci.
5. Utwórz konto magazynu w klasie premium w regionie docelowym. Jest to konto, które będzie używane dla nowej maszyny Wirtualnej.
6. Mieć pod ręką bieżące szczegóły maszyny Wirtualnej, w tym listę dysków i odpowiadające im obiekty blob VHD.

Przygotuj aplikację na przestoje. Aby wykonać czystą migrację, musisz zatrzymać całe przetwarzanie w bieżącym systemie. Tylko wtedy można uzyskać spójny stan, który można przeprowadzić migrację do nowej platformy. Czas przestoju zależy od ilości danych na dyskach do migracji.

> [!NOTE]
> Jeśli tworzysz maszynę wirtualną usługi Azure Resource Manager ze specjalistycznego dysku VHD, zapoznaj się z [tym szablonem,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) aby wdrożyć maszynę wirtualną Menedżera zasobów przy użyciu istniejącego dysku.
>
>

### <a name="register-your-vhd"></a>Zarejestruj swój VHD
Aby utworzyć maszynę wirtualną z dysku VHD systemu operacyjnego lub dołączyć dysk danych do nowej maszyny Wirtualnej, należy je najpierw zarejestrować. Wykonaj poniższe czynności w zależności od scenariusza dysku VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony dysk VHD systemu operacyjnego do tworzenia wielu wystąpień maszyn wirtualnych platformy Azure
Po uogólnione go vHD obrazu systemu operacyjnego jest przekazywana do konta magazynu, zarejestruj go jako **obraz maszyny Wirtualnej platformy Azure,** dzięki czemu można utworzyć jedno lub więcej wystąpień maszyny Wirtualnej z niego. Użyj następujących poleceń cmdlet programu PowerShell, aby zarejestrować dysk VHD jako obraz systemu maszyn wirtualnych platformy Azure. Podaj pełny adres URL kontenera, do którego skopiowano dysk VHD.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Skopiuj i zapisz nazwę tego nowego obrazu maszyny wirtualnej platformy Azure. W powyższym przykładzie jest to *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowy dysk VHD systemu operacyjnego do tworzenia pojedynczego wystąpienia maszyny Wirtualnej platformy Azure
Po przekazaniu unikatowego dysku wirtualnego systemu operacyjnego na konto magazynu zarejestruj go jako **dysk systemu operacyjnego Azure,** aby utworzyć z niego wystąpienie maszyny Wirtualnej. Użyj tych poleceń cmdlet programu PowerShell, aby zarejestrować dysk VHD jako dysk systemu operacyjnego Azure. Podaj pełny adres URL kontenera, do którego skopiowano dysk VHD.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Skopiuj i zapisz nazwę tego nowego dysku systemu operacyjnego Azure. W powyższym przykładzie jest to *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dysk VHD dysku danych, który ma być dołączony do nowych wystąpień maszyn wirtualnych platformy Azure
Po przekazaniu dysku danych VHD do konta magazynu, zarejestruj go jako dysk danych platformy Azure, dzięki czemu można dołączyć do nowej serii DS, serii DSv2 lub GS Series Wystąpienia maszyny Wirtualnej Platformy Azure.

Użyj tych poleceń cmdlet programu PowerShell, aby zarejestrować dysk VHD jako dysk danych platformy Azure. Podaj pełny adres URL kontenera, do którego skopiowano dysk VHD.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Skopiuj i zapisz nazwę tego nowego dysku danych platformy Azure. W powyższym przykładzie jest *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Tworzenie maszyny wirtualnej obsługującej magazyn premium
Po zarejestrowaniu obrazu systemu operacyjnego lub dysku systemu operacyjnego należy utworzyć nową maszynę wirtualną z serii DS, serii DSv2 lub GS. Będziesz używać obrazu systemu operacyjnego lub nazwy dysku systemu operacyjnego, który został zarejestrowany. Wybierz typ maszyny Wirtualnej z warstwy Magazynu w warstwie Premium. W poniższym przykładzie używamy *Standard_DS2* rozmiar maszyny Wirtualnej.

> [!NOTE]
> Zaktualizuj rozmiar dysku, aby upewnić się, że spełnia wymagania dotyczące pojemności i wydajności oraz dostępne rozmiary dysków platformy Azure.
>
>

Postępuj zgodnie z krok po kroku poleceń cmdlet programu PowerShell poniżej, aby utworzyć nową maszynę wirtualną. Najpierw ustaw wspólne parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Najpierw utwórz usługę w chmurze, w której będziesz hostować nowe maszyny wirtualne.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Następnie, w zależności od scenariusza, utwórz wystąpienie maszyny Wirtualnej platformy Azure z obrazu systemu operacyjnego lub dysku systemu operacyjnego, który został zarejestrowany.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony dysk VHD systemu operacyjnego do tworzenia wielu wystąpień maszyn wirtualnych platformy Azure
Utwórz jedno lub więcej nowych wystąpień maszyn wirtualnych platformy Azure z serii DS przy użyciu **zarejestrowanego obrazu systemu operacyjnego Azure.** Określ tę nazwę obrazu systemu operacyjnego w konfiguracji maszyny Wirtualnej podczas tworzenia nowej maszyny Wirtualnej, jak pokazano poniżej.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowy dysk VHD systemu operacyjnego do tworzenia pojedynczego wystąpienia maszyny Wirtualnej platformy Azure
Utwórz nowe wystąpienie maszyny wirtualnej platformy Azure z serii DS przy użyciu zarejestrowanego **dysku systemu operacyjnego Azure.** Określ tę nazwę dysku systemu operacyjnego w konfiguracji maszyny Wirtualnej podczas tworzenia nowej maszyny Wirtualnej, jak pokazano poniżej.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Określ inne informacje o maszynach wirtualnych platformy Azure, takie jak usługa w chmurze, region, konto magazynu, zestaw dostępności i zasady buforowania. Należy zauważyć, że wystąpienie maszyny Wirtualnej musi znajdować się wspólnie z skojarzonym systemem operacyjnym lub dyskami danych, więc wybrane konto usługi w chmurze, regionu i magazynu musi znajdować się w tej samej lokalizacji, co podstawowe dyski wirtualne tych dysków.

### <a name="attach-data-disk"></a>Dołączanie dysku danych
Wreszcie, jeśli masz zarejestrowane dyski VHD, dołącz je do nowej maszyny Wirtualnej platformy Azure obsługującej usługę Azure w usłudze Premium Storage.

Użyj następującemu polecenia cmdlet programu PowerShell, aby dołączyć dysk danych do nowej maszyny Wirtualnej i określić zasady buforowania. W przykładzie poniżej zasady buforowania jest ustawiona na *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Mogą istnieć dodatkowe kroki niezbędne do obsługi aplikacji, która nie jest objęta tym przewodnikiem.
>
>

### <a name="checking-and-plan-backup"></a>Sprawdzanie i planowanie tworzenia kopii zapasowych
Gdy nowa maszyna wirtualna jest uruchomiona, dostęp do niej przy użyciu tego samego identyfikatora logowania i hasło jest jak oryginalna maszyna wirtualna i sprawdź, czy wszystko działa zgodnie z oczekiwaniami. Wszystkie ustawienia, w tym woluminy rozłożone, będą obecne w nowej maszynie wirtualnej.

Ostatnim krokiem jest zaplanowanie harmonogramu tworzenia kopii zapasowych i konserwacji dla nowej maszyny Wirtualnej na podstawie potrzeb aplikacji.

### <a name="a-sample-migration-script"></a><a name="a-sample-migration-script"></a>Przykładowy skrypt migracji
Jeśli masz wiele maszyn wirtualnych do migracji, automatyzacja za pomocą skryptów programu PowerShell będzie przydatna. Poniżej przedstawiono przykładowy skrypt, który automatyzuje migrację maszyny Wirtualnej. Należy zauważyć, że poniższy skrypt jest tylko przykładem i istnieje kilka założeń dotyczących bieżących dysków maszyn wirtualnych. Może być konieczne zaktualizowanie skryptu, aby dopasować go do określonego scenariusza.

Założenia są następujące:

* Tworzysz klasyczne maszyny wirtualne platformy Azure.
* Źródłowe dyski systemu operacyjnego i źródłowe dyski danych znajdują się na tym samym koncie magazynu i w tym samym kontenerze. Jeśli dyski systemu operacyjnego i dyski z danymi nie znajdują się w tym samym miejscu, można użyć AzCopy lub Azure PowerShell do kopiowania dysków VHD za pomocą kont magazynu i kontenerów. Zapoznaj się z poprzednim krokiem: [Kopiowanie dysku VHD za pomocą programu AzCopy lub Programu PowerShell](#copy-vhd-with-azcopy-or-powershell). Edytowanie tego skryptu w celu spełnienia scenariusza jest inny wybór, ale zaleca się użycie AzCopy lub PowerShell, ponieważ jest to łatwiejsze i szybsze.

Skrypt automatyzacji znajduje się poniżej. Zamień tekst na informacje i zaktualizuj skrypt, aby dopasować go do określonego scenariusza.

> [!NOTE]
> Użycie istniejącego skryptu nie powoduje zachowania konfiguracji sieciowej źródłowej maszyny Wirtualnej. Należy ponownie skonfigurować ustawienia sieciowe na zmigrowanych maszynach wirtualnych.
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

#### <a name="optimization"></a><a name="optimization"></a>Optymalizacji
Bieżąca konfiguracja maszyny Wirtualnej może być dostosowana specjalnie do pracy z dyskami standardowymi. Na przykład, aby zwiększyć wydajność przy użyciu wielu dysków w woluminie rozłożonym. Na przykład zamiast używać 4 dysków oddzielnie w magazynie w wersji Premium, można zoptymalizować koszt, mając jeden dysk. Optymalizacje takie jak ten muszą być obsługiwane indywidualnie dla każdego przypadku i wymagają niestandardowych kroków po migracji. Należy również zauważyć, że ten proces może nie działać dobrze dla baz danych i aplikacji, które zależą od układu dysku zdefiniowanego w instalacji.

##### <a name="preparation"></a>Przygotowywanie
1. Ukończ migrację prostą zgodnie z opisem we wcześniejszej sekcji. Optymalizacje zostaną wykonane na nowej maszynie wirtualnej po migracji.
2. Zdefiniuj nowe rozmiary dysków potrzebne do zoptymalizowanej konfiguracji.
3. Określ mapowanie bieżących dysków/woluminów do nowych specyfikacji dysków.

##### <a name="execution-steps"></a>Kroki wykonania
1. Utwórz nowe dyski o odpowiednich rozmiarach na maszynie wirtualnej magazynu w wersji Premium.
2. Zaloguj się do maszyny Wirtualnej i skopiuj dane z bieżącego woluminu na nowy dysk, który jest mapowana na ten wolumin. Zrób to dla wszystkich bieżących woluminów, które muszą być mapowane na nowy dysk.
3. Następnie zmień ustawienia aplikacji, aby przełączyć się na nowe dyski i odłącz stare woluminy.

Aby dostroić aplikację, aby uzyskać lepszą wydajność dysku, zapoznaj się z sekcją optymalizacji wydajności aplikacji w naszym [artykule projektowym dla wysokiej wydajności.](../../virtual-machines/windows/premium-storage-performance.md)

### <a name="application-migrations"></a>Migracje aplikacji
Bazy danych i inne złożone aplikacje mogą wymagać specjalnych kroków zdefiniowanych przez dostawcę aplikacji dla migracji. Zapoznaj się z odpowiednią dokumentacją aplikacji. Na przykład zazwyczaj bazy danych mogą być migrowane za pomocą kopii zapasowej i przywracania.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące zasoby dla określonych scenariuszy migracji maszyn wirtualnych:

* [Migrowanie maszyn wirtualnych platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Tworzenie i przekazywanie dysku VHD systemu Windows Server na platformę Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tworzenie i przekazywanie dysku VHD systemu Linux na platformę Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie maszyn wirtualnych z usług Amazon AWS na platformę Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Ponadto zobacz następujące zasoby, aby dowiedzieć się więcej o usłudze Azure Storage i maszynach wirtualnych platformy Azure:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Maszyny wirtualne platformy Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Wybieranie typu dysku dla maszyn wirtualnych IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
