---
title: Przygotowywanie dyski twarde dla zadania importu Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować Tworzenie zadania importu dla usługi Import/Eksport Azure przy użyciu narzędzia v1 WAImportExport dysków twardych.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 361e16262e528c7dea1bab4b9d945a28af8be399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874094"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Przygotowywanie dysków twardych do zadania importu
Aby przygotować co najmniej jeden dyski twarde dla zadania importu, wykonaj następujące kroki:

-   Identyfikowanie danych można zaimportować do usługi Blob

-   Identyfikowanie katalogi wirtualne docelowego i obiektów blob w usłudze obiektów Blob

-   Jak wiele dysków, należy określić

-   Kopiowanie danych do każdego dysku twardego

 Dla przykładowego przepływu pracy, zobacz [przykładowego przepływu pracy, aby przygotować dyski twarde dla zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Zidentyfikować dane do zaimportowania
 Pierwszym krokiem tworzenia zadania importu jest ustalenie, który katalogów i plików, które chcesz zaimportować. Może to być lista katalogów, lista plików unikatowy lub kombinacji tych dwóch. Jeśli katalog, wszystkie pliki w katalogu i jego podkatalogach będzie częścią zadania importu.

> [!NOTE]
>  Ponieważ podkatalogi są dołączone rekursywnie gdy katalog nadrzędny jest uwzględniona, należy określić tylko w katalogu nadrzędnym. Nie należy również określać jego podkatalogach.
>
>  Obecnie narzędzia Import/Eksport Microsoft Azure ma następujące ograniczenia: Jeśli katalog zawiera więcej danych niż dysk twardy może zawierać, następnie katalogu musi być dzielony na mniejsze katalogów. Na przykład jeśli katalog zawiera 2,5 TB danych i miejsca na dysku twardym jest tylko 2TB, następnie należy podzielić katalogu 2,5 TB na mniejsze katalogów. To ograniczenie zostaną rozwiązane w nowszej wersji narzędzia.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Określenie lokalizacji docelowej usługi blob
 Dla każdego katalogu lub pliku, który jest importowany trzeba będzie zidentyfikować wirtualnego katalogu docelowego lub obiektu blob w usłudze obiektów Blob platformy Azure. Jako wejścia do narzędzia importu/eksportu Azure użyje następujących elementów docelowych. Należy pamiętać, że katalogi powinien rozdzielone znakiem ukośnika "/".

 W poniższej tabeli przedstawiono niektóre przykłady celów obiektów blob:

|Pliku lub katalogu źródłowego|Docelowego obiektu blob lub katalogu wirtualnego|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.Core.Windows.NET/Video|
|H:\Photo|https://mystorageaccount.blob.Core.Windows.NET/Photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.Core.Windows.NET/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.Core.Windows.NET/Music|

## <a name="determine-how-many-drives-are-needed"></a>Określ liczbę dysków są potrzebne
 Następnie należy określić:

-   Liczba dysków twardych niezbędne do przechowywania danych.

-   Katalogi i/lub plików autonomicznych, które zostaną skopiowane do każdego dysku twardego.

 Upewnij się, że liczba dysków twardych należy do przechowywania danych, które są transferu danych.

## <a name="copy-data-to-your-hard-drive"></a>Kopiowanie danych na dysku twardym
 W tej sekcji opisano sposób wywołania narzędzie importu/eksportu Azure, aby skopiować dane do jednego lub więcej dysków twardych. Zawsze należy wywołać narzędzia importu/eksportu Azure, możesz utworzyć nową *skopiuj sesji*. Utwórz co najmniej jedną kopię sesji dla każdego dysku, do którego skopiowane danych; w niektórych przypadkach może wymagać więcej niż jedną sesję kopiowania do skopiowania wszystkich danych na jednym dysku. Poniżej przedstawiono przyczyny, że wiele kopii sesji może być konieczne:

-   Należy utworzyć dla każdego dysku, który został skopiowany do sesji osobnej kopii.

-   Sesja kopii można skopiować jednego katalogu lub pojedynczego obiektu blob na dysku. Jeśli kopiujesz wiele katalogów, wiele obiektów blob lub obie te grupy, należy utworzyć wiele kopii sesji.

-   Można określić właściwości i metadanych, które zostaną ustawione na obiekty BLOB zaimportowana jako część zadania importu. Właściwości lub metadane, które można określić dla sesji kopia zostanie zastosowana do wszystkich obiektów blob, określony przez sesji kopiowania. Jeśli chcesz określić różne właściwości lub metadane dla niektórych obiektów blob, należy utworzyć sesję osobnej kopii. Zobacz [metadanych podczas procesu importowania i ustawienie właściwości](storage-import-export-tool-setting-properties-metadata-import-v1.md)Aby uzyskać więcej informacji.

> [!NOTE]
>  Jeśli masz wiele komputerów, które spełniają wymagania opisane w temacie [ustawienia zapasowej Azure importu/eksportu narzędzie](storage-import-export-tool-setup-v1.md), można skopiować danych do wielu dysków twardych jednocześnie uruchomione wystąpienie tego narzędzia na każdym komputerze.

 Dla każdego dysku twardego, czyli przygotowanie za pomocą narzędzia importu/eksportu Azure narzędzie utworzy plik jednego arkusza. Pliki dziennika, należy ze wszystkich dysków w taki sposób, aby utworzyć zadanie importu. Plik dziennika można również wznowienie przygotowywania dysków, jeśli narzędzie zostało przerwane.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Azure Składnia narzędzia importu/eksportu dla zadania importu
 Aby przygotować dyski dla zadania importu, należy wywołać narzędzie importu/eksportu Azure z **PrepImport** polecenia. Parametrów, które należy uwzględnić zależy, czy jest to pierwsza sesja kopiowania lub sesję kolejnych kopii.

 Pierwsza sesja kopii dysku wymaga niektóre dodatkowe parametry, aby określić klucz konta magazynu; litera dysku docelowym; Określa, czy dysk jest sformatowany; czy należy zaszyfrować dysk, a jeśli tak, klucza funkcji BitLocker; i katalogu dziennika. Oto składnia sesji kopii początkowej można skopiować katalogu lub pojedynczy plik:

 **Najpierw skopiować sesji, aby skopiować jeden katalog**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Najpierw skopiować sesji, aby skopiować pojedynczego pliku**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 W przypadku kolejnych kopii sesji nie należy określić parametrów początkowych. Oto składnia sesji kolejnych kopii można skopiować katalogu lub pojedynczy plik:

 **Kolejnych kopii sesji, aby skopiować jeden katalog**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sesje kolejnych kopii można skopiować jednego pliku**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametry dla pierwszej sesji kopiowania na dysku twardym
 Zawsze należy uruchomić narzędzie importu/eksportu Azure do kopiowania plików na dysku twardym, narzędzie tworzy sesji kopiowania. Każdej sesji kopiowania kopiuje jednego katalogu lub pojedynczy plik na dysku twardym. Stan sesji kopiowania są zapisywane w pliku dziennika. Jeśli sesja kopiowania zostanie przerwana (na przykład z powodu utraty zasilania systemu), może zostać wznowiony ponownie uruchamiając narzędzie i określenie pliku dziennika w wierszu polecenia.

> [!WARNING]
>  Jeśli określisz **/format** parametr dla pierwszej sesji kopiowania, dysk zostanie sformatowany i zostaną usunięte wszystkie dane na dysku. Zaleca się używania puste dysków tylko dla sesji kopiowania.

 Polecenie używane podczas pierwszej kopii sesji dla każdego dysku wymaga różnych parametrów niż polecenia kopiowania kolejnych sesjach. W poniższej tabeli wymieniono dodatkowe parametry, które są dostępne dla sesji pierwszego kopiowania:

|Parametr wiersza polecenia|Opis|
|-----------------------------|-----------------|
|**/SK:**< StorageAccountKey\>|`Optional.`Klucz konta magazynu dla konta magazynu, do którego dane mają zostać zaimportowane. Należy uwzględnić **/sk:**< StorageAccountKey\> lub **/csas:**< ContainerSas\> w poleceniu.|
|**/csas:**< ContainerSas\>|`Optional`. Kontener SAS służące do importowania danych do konta magazynu. Należy uwzględnić **/sk:**< StorageAccountKey\> lub **/csas:**< ContainerSas\> w poleceniu.<br /><br /> Wartość tego parametru musi zaczynać się od nazwy kontenera, następuje znak zapytania (?) i tokenu sygnatury dostępu Współdzielonego. Na przykład:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Uprawnienia, czy określony adres URL lub w zasadach dostępu przechowywane, musi zawierać odczytu, zapisu i usuwania dla zadania importu i odczytu, zapisu i listy zadań eksportu.<br /><br /> Jeśli ten parametr jest określony, wszystkie obiekty BLOB do importowania lub wyeksportować musi być w kontenerze, który został określony w sygnatury dostępu współdzielonego.|
|**/ t:**< TargetDriveLetter\>|`Required.`Litera dysku docelowego dysku twardego dla bieżącej sesji kopiowania, bez końcowej dwukropkiem.|
|**/ Format**|`Optional.`Określ ten parametr, gdy dysk musi być sformatowany; w przeciwnym razie Pomiń go. Zanim narzędzie formatuje dysk, pojawi się monit o potwierdzenie z konsoli. Aby pominąć potwierdzenie, należy określić parametr /silentmode.|
|**/silentmode**|`Optional.`Określ ten parametr, aby pominąć potwierdzenie formatowania dysku targert.|
|**/ szyfrowania**|`Optional.`Ten parametr należy określić, gdy dysk nie ma jeszcze zaszyfrowany za pomocą funkcji BitLocker i musi być szyfrowane przez narzędzie. Jeśli dysk już został zaszyfrowany za pomocą funkcji BitLocker, Pomiń ten parametr i określ `/bk` parametru, zapewniając istniejącego klucza funkcji BitLocker.<br /><br /> Jeśli określisz `/format` parametru, możesz również określić `/encrypt` parametru.|
|**/BK:**< BitLockerKey\>|`Optional.`Jeśli `/encrypt` jest określony, Pomiń ten parametr. Jeśli `/encrypt` jest pominięty, musisz mieć już zaszyfrowanych dysków funkcją BitLocker. Ten parametr umożliwia określenie klucza funkcji BitLocker. Szyfrowanie funkcją BitLocker jest wymagany dla wszystkich dysków twardych dla zadania importu.|
|**/ logdir:**< LogDirectory\>|`Optional.`Katalog dziennika określa katalog, który będzie używany do przechowywania dzienników pełne, jak również tymczasowe pliki manifestu. Jeśli nie zostanie określony, bieżący katalog będzie używany jako katalog dziennika.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametry wymagane we wszystkich sesjach kopiowania
 Plik dziennika zawiera stan dla wszystkich sesji kopiowania na dysku twardym. Zawiera informacje potrzebne do utworzenia zadania importu. Należy zawsze określić w pliku dziennika podczas uruchamiania narzędzia importu/eksportu Azure, jak również identyfikator sesji kopii:

|||
|-|-|
|Parametr wiersza polecenia|Opis|
|**/j:**< JournalFile\>|`Required.`Ścieżka do pliku dziennika. Każdy dysk musi mieć dokładnie jeden plik dziennika. Należy pamiętać, że plik dziennika nie musi znajdować się na dysku docelowym. Rozszerzenie pliku dziennika jest `.jrn`.|
|**/ Identyfikator:**< identyfikator sesji\>|`Required.`Identyfikator sesji określa sesji kopiowania. Służy do dokładnych odzyskiwanie kopii przerwania sesji. Pliki, które są kopiowane w ramach sesji kopiowania są przechowywane w katalogu o nazwie po identyfikator sesji na docelowym dysku.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametry w celu kopiowania jednego katalogu
 Po skopiowaniu jednego katalogu, zastosuj następujące wymaganych i opcjonalnych parametrów:

|Parametr wiersza polecenia|Opis|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.`Katalog źródłowy, który zawiera pliki, które ma zostać skopiowany na dysk docelowy. Ścieżka do katalogu musi być ścieżką bezwzględną (nie ścieżkę względną).|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.`Ścieżka do katalogu wirtualnego docelowego na koncie magazynu systemu Windows Azure. Katalog wirtualny może lub już nie istnieje.<br /><br /> Możesz określić kontener lub prefiks obiektu blob, takich jak `music/70s/`. Katalog docelowy musi rozpoczynać się od nazwy kontenera, następuje ukośnik "/" i opcjonalnie mogą obejmować katalogu wirtualnego obiektów blob, w którym kończy się wyrazem "/".<br /><br /> Gdy kontenera docelowego jest nadrzędny kontener, należy jawnie określić nadrzędny kontener, łącznie z ukośnikiem, jako `$root/`. Ponieważ nie może zawierać obiekty BLOB w kontenerze głównym "/" w ich nazw, gdy katalog docelowy jest nadrzędny kontener nie zostaną skopiowane podkatalogów w katalogu źródłowym.<br /><br /> Należy użyć nazwy prawidłowego kontenera podczas określania katalogu wirtualnego w docelowym lub obiektów blob. Należy pamiętać, że nazwy kontenerów muszą być małymi literami. Kontener nazewnictwa reguł, zobacz [nazewnictwa i odwołuje się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Dyspozycji:**< zmienić &#124; zastąpić nie &#124; zastąpić >|`Optional.`Określa zachowanie, gdy obiektu blob o określonym adresie już istnieje. Prawidłowe wartości tego parametru to: `rename`, `no-overwrite` i `overwrite`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `rename`.<br /><br /> Wartość określona dla tego parametru dotyczy wszystkich plików w katalogu określonym przez `/srcdir` parametru.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Określa typ obiektu blob dla docelowego obiektów blob. Prawidłowe wartości to: `BlockBlob` i `PageBlob`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `BlockBlob`.<br /><br /> W większości przypadków `BlockBlob` jest zalecane. Jeśli określisz `PageBlob`, długość każdego pliku w katalogu musi być wielokrotnością 512, rozmiar strony dla stronicowych obiektów blob.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Ścieżka do pliku właściwości dla obiektów blob docelowego. Zobacz [Import/Eksport usługi metadanych i Format pliku właściwości](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Ścieżka do pliku metadanych dla obiektów blob docelowego. Zobacz [Import/Eksport usługi metadanych i Format pliku właściwości](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|

### <a name="parameters-for-copying-a-single-file"></a>Parametry w celu kopiowania pojedynczego pliku
 Podczas kopiowania pojedynczy plik, zastosuj następujące wymaganych i opcjonalnych parametrów:

|Parametr wiersza polecenia|Opis|
|----------------------------|-----------------|
|**/srcfile:**< SourceFile\>|`Required.`Pełna ścieżka do pliku, który ma zostać skopiowany. Ścieżka do katalogu musi być ścieżką bezwzględną (nie ścieżkę względną).|
|**/dstblob:**< DestinationBlobPath\>|`Required.`Ścieżka do docelowego obiektu blob na koncie magazynu systemu Windows Azure. Obiekt blob może lub już nie istnieje.<br /><br /> Określ na początku nazwy obiektów blob, wprowadzając nazwę kontenera. Nazwa obiektu blob nie może rozpoczynać się od "/" lub nazwa konta magazynu. W przypadku reguł nazewnictwa obiektów blob, zobacz [nazewnictwa i odwołuje się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Docelowy kontener jest nadrzędny kontener, należy jawnie określić `$root` jako kontener, takich jak `$root/sample.txt`. Należy pamiętać, że obiekty BLOB w kontenerze głównego nie może zawierać "/" w ich nazwy.|
|**/ Dyspozycji:**< zmienić &#124; zastąpić nie &#124; zastąpić >|`Optional.`Określa zachowanie, gdy obiektu blob o określonym adresie już istnieje. Prawidłowe wartości tego parametru to: `rename`, `no-overwrite` i `overwrite`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `rename`.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Określa typ obiektu blob dla docelowego obiektów blob. Prawidłowe wartości to: `BlockBlob` i `PageBlob`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `BlockBlob`.<br /><br /> W większości przypadków `BlockBlob` jest zalecane. Jeśli określisz `PageBlob`, długość każdego pliku w katalogu musi być wielokrotnością 512, rozmiar strony dla stronicowych obiektów blob.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Ścieżka do pliku właściwości dla obiektów blob docelowego. Zobacz [Import/Eksport usługi metadanych i Format pliku właściwości](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Ścieżka do pliku metadanych dla obiektów blob docelowego. Zobacz [Import/Eksport usługi metadanych i Format pliku właściwości](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|

### <a name="resuming-an-interrupted-copy-session"></a>Wznawianie sesji przerwania kopiowania
 Jeśli sesja kopiowania zostanie przerwana jakiejkolwiek przyczyny, będzie możliwe wznowienie, uruchamiając narzędzie z określony tylko plik dziennika:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Tylko najnowsze kopii, jeśli została zakończona nieprawidłowo, może można wznowić sesji.

> [!IMPORTANT]
>  Po wznowieniu sesji kopiowania, nie należy modyfikować źródła danych plików i katalogów przez dodanie lub usunięcie plików.

### <a name="aborting-an-interrupted-copy-session"></a>Trwa przerywanie sesji przerwania kopiowania
 Jeśli sesja kopiowania zostanie przerwana i nie jest możliwe wznowienie (na przykład, jeżeli katalog źródłowy niedostępny), można przerwać w bieżącej sesji, aby go można wycofać i będzie można uruchomić nowej sesji kopiowania:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Tylko ostatniej kopii sesji, jeśli została zakończona nieprawidłowo, może przerwana. Należy pamiętać, że nie można przerwać pierwszej sesji kopii dysku. Zamiast tego należy ponownie uruchomić sesję kopiowania przy użyciu nowego pliku dziennika.

## <a name="next-steps"></a>Następne kroki

* [Trwa konfigurowanie narzędzia Azure Import/Eksport](storage-import-export-tool-setup-v1.md)
* [Ustawianie właściwości i metadanych podczas procesu importowania](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Krótki przewodnik dla często używanych poleceń](storage-import-export-tool-quick-reference-v1.md) 
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Naprawianie zadania eksportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
