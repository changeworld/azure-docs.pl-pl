---
title: Przygotowywanie dysków twardych do zadania importu platformy Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować dyski twarde, za pomocą narzędzia v1 WAImportExport, aby utworzyć zadanie importu dla usługi Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 03b504524b2f489f1ee042c6e825ccffe0a60bb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478474"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Przygotowywanie dysków twardych do zadania importu
Aby przygotować jeden lub więcej dysków twardych do zadania importu, wykonaj następujące kroki:

- Zidentyfikować dane, aby zaimportować do usługi Blob service

- Identyfikowanie katalogi wirtualne docelowego i obiektów blob w usłudze obiektów Blob

- Jak wiele dysków, należy określić

- Kopiuj dane do każdego dysku twardego

  Aby uzyskać przykładowy przepływ pracy, zobacz [przykładowy przepływ pracy przygotowywanie dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identyfikowanie danych do zaimportowania
 Pierwszym krokiem do tworzenie zadania importu jest ustalenie, które katalogów i plików, które chcesz zaimportować. Może to być listę katalogów, listę unikatowych plików lub kombinację tych dwóch. Gdy jest katalogiem, wszystkie pliki w katalogu i jego podkatalogach będzie częścią zadania importu.

> [!NOTE]
>  Ponieważ podkatalogach cyklicznie uwzględnione po katalogu nadrzędnego jest dołączony, należy określić tylko w katalogu nadrzędnego. Nie należy również określać jego podkatalogach.
>
>  Obecnie narzędzie importu/eksportu platformy Microsoft Azure ma następujące ograniczenia: Jeśli katalog zawiera więcej danych niż dysk twardy może zawierać, katalog musi być dzielone na mniejsze katalogów. Na przykład jeśli katalog zawiera 2,5 TB danych i miejsca na dysku twardym jest tylko 2TB, musisz przerwać katalogu 2,5 TB mniejszych katalogów. To ograniczenie zostanie rozwiązany w nowszej wersji narzędzia.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Określenie lokalizacji docelowej w usłudze obiektów blob
 Dla każdego katalogu lub pliku, który jest importowany należy zidentyfikować wirtualnego katalogu docelowego lub obiektu blob w usłudze Azure Blob. Te obiekty docelowe będzie używany jako dane wejściowe, aby narzędzie importu/eksportu platformy Azure. Należy pamiętać, że katalogi powinien rozdzielonych znakiem ukośnika "/".

 W poniższej tabeli przedstawiono przykłady celów obiektów blob:

|Pliku lub katalogu źródłowego|Docelowy obiekt blob lub katalogu wirtualnego|
|------------------------------|-------------------------------------------|
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Określić, ile dyski są potrzebne.
 Następnie należy określić:

- Liczba dysków twardych jest potrzebny do przechowywania danych.

- Katalogi i/lub plików autonomicznych, które zostaną skopiowane do każdego dysku twardego.

  Upewnij się, że liczba dysków twardych należy do przechowywania danych, z którego przenosisz.

## <a name="copy-data-to-your-hard-drive"></a>Kopiowanie danych na dysku twardym
 W tej sekcji opisano, jak wywołać narzędzie importu/eksportu platformy Azure, aby skopiować dane do jednego lub więcej dysków twardych. Zawsze należy wywołać narzędzie importu/eksportu platformy Azure, możesz utworzyć nową *skopiuj sesji*. Utwórz co najmniej jedna kopia sesji dla każdego dysku, do którego skopiować danych. w niektórych przypadkach może być konieczne więcej niż jedną sesję kopiowania do skopiowania wszystkich danych na jednym dysku. Oto kilka powodów, że wiele sesji kopia może być konieczne:

-   Należy utworzyć dla każdego dysku, które można skopiować do sesji oddzielna kopia.

-   Sesję kopiowania można skopiować jednego katalogu lub pojedynczego obiektu blob na dysk. Jeśli kopiujesz wiele katalogów, wielu obiektów blob lub jako kombinację obu tych należy utworzyć wiele kopii sesji.

-   Można określić właściwości i metadanych, które zostaną ustawione dla obiektów blob, importowany jak część zadania importu. Właściwości i metadanych, które można określić dla sesji kopiowania będą dotyczyć wszystkich obiektów blob, określony przez sesji kopiowania. Jeśli chcesz określić różne właściwości i metadanych dla niektórych obiektów blob, należy utworzyć sesję oddzielna kopia. Zobacz [Ustawianie właściwości i metadanych podczas procesu importowania](storage-import-export-tool-setting-properties-metadata-import-v1.md)Aby uzyskać więcej informacji.

> [!NOTE]
>  Jeśli masz wiele maszyn, które spełniają wymagania opisane w [ustawienie zapasowej Azure Import/Export narzędzie](storage-import-export-tool-setup-v1.md), możesz skopiować dane na wiele dysków twardych w sposób równoległy, uruchomione wystąpienie tego narzędzia na każdej maszynie.

 Dla każdego dysku twardego, który należy przygotować za pomocą narzędzia Azure Import/Export narzędzie utworzy plik jednego arkusza. Pliki dziennika, należy ze wszystkich dysków w taki sposób, aby utworzyć zadanie importu. Plik dziennika można również wznowić przygotowywania dysku, jeśli narzędzie zostanie przerwany.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Usługa Azure składnię narzędzia Import/Export do zadania importu
 Aby przygotować dyski do zadania importu, należy wywołać narzędzia Azure Import/Export, za pomocą **PrepImport** polecenia. Parametry, które możesz uwzględnić zależy, czy jest to pierwsza sesja kopiowania lub sesji kolejnych kopii.

 Pierwsza sesja kopii dysku wymaga pewnych dodatkowych parametrów, aby określić klucz konta magazynu; litera dysku docelowym; czy dysk jest sformatowany; czy należy zaszyfrować dysk, a jeśli tak, klucza funkcji BitLocker; i katalog dziennika. Poniżej przedstawiono składnię sesję kopii początkowej można skopiować katalogu lub pojedynczy plik:

 **Najpierw skopiuj sesji, aby skopiować jeden katalog**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Najpierw skopiuj sesji, aby skopiować jeden plik**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 W sesji kolejnych kopii nie należy określić parametry początkowe. Poniżej przedstawiono składnię sesję kolejnych kopiowania do skopiowania katalogu lub pojedynczy plik:

 **Sesje kolejnych kopiowania można skopiować jednego katalogu**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sesje kolejnych kopiowania do skopiowania pojedynczy plik**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametry dla pierwszej sesji kopiowania na dysk twardy
 Każdorazowo, uruchom narzędzie importu/eksportu platformy Azure, aby skopiować pliki na dysku twardym, narzędzie tworzy sesję kopiowania. Każda sesja kopiowania Kopiuje jeden katalog lub pojedynczy plik na dysku twardym. Stan sesji kopiowania są zapisywane do pliku dziennika. Jeśli sesja kopia zostanie przerwany (na przykład z powodu utraty zasilania systemu), może być wznowione przez ponownie uruchomienie narzędzia i określenie pliku dziennika, w wierszu polecenia.

> [!WARNING]
>  Jeśli określisz **/formatowanie** parametr dla pierwszej sesji kopiowania, dysk będzie sformatowany i zostaną usunięte wszystkie dane na dysku. Zaleca się używać puste dyski tylko dla sesji kopiowania.

 Polecenie używane podczas pierwszej sesji kopiowania dla każdego dysku wymaga różnych parametrów niż polecenia kopiowania kolejnych sesji. Poniższa tabela zawiera listę dodatkowych parametrów, które są dostępne dla pierwszej sesji kopii:

|Parametr wiersza polecenia|Opis|
|-----------------------------|-----------------|
|**/sk:** <StorageAccountKey\>|`Optional.` Klucz konta magazynu dla konta magazynu, do którego zostaną zaimportowane dane. Należy uwzględnić **/sk:** < StorageAccountKey\> lub **/csas:** < ContainerSas\> w poleceniu.|
|**/csas:** < ContainerSas\>|`Optional`. Kontener sygnatur dostępu Współdzielonego umożliwia importowanie danych do konta magazynu. Należy uwzględnić **/sk:** < StorageAccountKey\> lub **/csas:** < ContainerSas\> w poleceniu.<br /><br /> Wartość tego parametru musi zaczynać się od nazwy kontenera, następuje znak zapytania (?) i tokenu sygnatury dostępu Współdzielonego. Na przykład:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Uprawnienia, czy określone w polu adres URL lub przechowywanych zasad dostępu, musi zawierać odczytu, zapisu i usuwania zadań importu i odczytu, zapisu i listy dla zadania eksportu.<br /><br /> Jeśli ten parametr jest określony, wszystkie obiekty BLOB można zaimportować lub wyeksportować musi być w kontenerze, który został określony w sygnatury dostępu współdzielonego.|
|**/t:** <TargetDriveLetter\>|`Required.` Litera dysku docelowego dysku twardego dla bieżącej sesji kopiowania bez końcowych dwukropka.|
|**wystąpiły**|`Optional.` Określenia tego parametru, gdy dysk musi być sformatowany; w przeciwnym razie Pomiń ją. Zanim narzędzie formatuje dysk, pojawi się monit o potwierdzenie z konsoli. Aby pominąć potwierdzenie, należy określić parametr /silentmode.|
|**/silentmode**|`Optional.` Określenia tego parametru, aby pominąć potwierdzenie formatowania dysku docelowego.|
|**/ szyfrowanie**|`Optional.` Ten parametr należy określić, gdy dysk jeszcze nie został zaszyfrowany za pomocą funkcji BitLocker i musi być zaszyfrowane za pomocą narzędzia. Jeśli już został zaszyfrowany dysk z funkcją BitLocker, Pomiń ten parametr i określ `/bk` parametru, zapewniając istniejącego klucza funkcji BitLocker.<br /><br /> Jeśli określisz `/format` należy także określić parametr, następnie `/encrypt` parametru.|
|**/bk:** <BitLockerKey\>|`Optional.` Jeśli `/encrypt` jest określony, Pomiń ten parametr. Jeśli `/encrypt` jest pominięty, musisz już mieć zaszyfrowany dysk z funkcją BitLocker. Użyj tego parametru do określenia klucza funkcji BitLocker. Szyfrowanie funkcją BitLocker jest wymagana dla wszystkich dysków twardych do zadania importu.|
|**/ logdir:** < LogDirectory\>|`Optional.` Katalog dziennika określa katalog ma być używany do przechowywania pełne dzienniki, jak również tymczasowych plików manifestu. Jeśli nie zostanie określony, bieżący katalog będzie służyć jako katalog dziennika.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametry wymagane we wszystkich sesjach kopiowania
 Plik dziennika zawiera stan dla wszystkich sesji kopiowania na dysk twardy. Zawiera informacje potrzebne do utworzenia zadania importu. Zawsze należy określić plik dziennika, uruchamiając narzędzie importu/eksportu platformy Azure, a także identyfikator sesji kopii:

|||
|-|-|
|Parametr wiersza polecenia|Opis|
|**/j:** < JournalFile\>|`Required.` Ścieżka do pliku dziennika. Każdy dysk musi mieć dokładnie jeden plik dziennika. Należy pamiętać, że plik dziennika nie muszą znajdować się na dysku docelowego. Rozszerzenie pliku dziennika jest `.jrn`.|
|**/ Identyfikator:** < SessionId\>|`Required.` Identyfikator sesji identyfikuje sesji kopiowania. Służy do dokładnego odzyskiwanie sesji przerwane kopiowania. Pliki, które są kopiowane w ramach sesji kopiowania są przechowywane w katalogu o nazwie po identyfikator sesji na docelowym dysku.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametry w celu kopiowania pojedynczego katalogu
 Podczas kopiowania jeden katalog, zastosuj następujące wymaganych i opcjonalnych parametrów:

|Parametr wiersza polecenia|Opis|
|----------------------------|-----------------|
|**/srcdir:** <SourceDirectory\>|`Required.` Katalog źródłowy, który zawiera pliki będą kopiowane na dysk docelowy. Ścieżka katalogu musi być ścieżką bezwzględną (nie ścieżkę względną).|
|**/dstdir:** <DestinationBlobVirtualDirectory\>|`Required.` Ścieżka do katalogu wirtualnego docelowym w ramach konta magazynu platformy Windows Azure. Katalog wirtualny może lub nie może już istnieć.<br /><br /> Można wskazać kontener lub prefiks obiektu blob, takich jak `music/70s/`. Katalog docelowy musi zaczynać się od nazwy kontenera, następuje ukośnik "/" i opcjonalnie może zawierać katalogu wirtualnego obiektów blob, który kończy się ciągiem "/".<br /><br /> Gdy kontenera docelowego jest nadrzędny kontener, należy jawnie określić kontener głównego, łącznie z ukośnikiem, jako `$root/`. Ponieważ nie może zawierać obiekty BLOB w kontenerze katalogu głównego "/" w nazwach podkatalogów w katalogu źródłowym nie zostaną skopiowane, gdy katalog docelowy jest nadrzędny kontener.<br /><br /> Pamiętaj używać nazw prawidłowego kontenera, określając docelowy katalogi wirtualne czy za obiekty BLOB. Należy pamiętać, że nazwy kontenerów muszą być małymi literami. Reguły nazewnictwa kontenerów, zobacz [nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/Disposition:** <rename&#124;no-overwrite&#124;overwrite>|`Optional.` Określa zachowanie, gdy obiekt blob, o określonym adresie już istnieje. Prawidłowe wartości tego parametru to: `rename`, `no-overwrite` i `overwrite`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `rename`.<br /><br /> Wartość określona dla tego parametru ma wpływ na wszystkie pliki w katalogu określonym przez `/srcdir` parametru.|
|**/BlobType:** <BlockBlob&#124;PageBlob>|`Optional.` Określa typ obiektu blob dla obiektów blob docelowego. Prawidłowe wartości to: `BlockBlob` i `PageBlob`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `BlockBlob`.<br /><br /> W większości przypadków `BlockBlob` jest zalecane. Jeśli określisz `PageBlob`, długość każdego pliku w katalogu musi być wielokrotnością liczby 512, rozmiar strony dla stronicowych obiektów blob.|
|**/PropertyFile:** <PropertyFile\>|`Optional.` Ścieżka do pliku właściwości dla obiektów blob docelowego. Zobacz [metadanych i Format pliku właściwości usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|
|**/MetadataFile:** <MetadataFile\>|`Optional.` Ścieżka do pliku metadanych dla obiektów blob docelowego. Zobacz [metadanych i Format pliku właściwości usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|

### <a name="parameters-for-copying-a-single-file"></a>Parametry w celu kopiowania pojedynczego pliku
 Podczas kopiowania pojedynczego pliku, zastosowanie następujących wymaganych i opcjonalnych parametrów:

|Parametr wiersza polecenia|Opis|
|----------------------------|-----------------|
|**/srcfile:** <SourceFile\>|`Required.` Pełna ścieżka do pliku, który ma być skopiowany. Ścieżka katalogu musi być ścieżką bezwzględną (nie ścieżkę względną).|
|**/dstblob:** <DestinationBlobPath\>|`Required.` Ścieżka do docelowego obiektu blob na koncie magazynu platformy Windows Azure. Obiekt blob może lub nie może już istnieć.<br /><br /> Określ na początku nazwy obiektu blob o nazwie kontener. Nazwa obiektu blob nie może zaczynać "/" lub nazwa konta magazynu. Reguły nazewnictwa obiektów blob, zobacz [nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Kontener docelowy jest nadrzędny kontener, należy jawnie określić `$root` jako kontener, takich jak `$root/sample.txt`. Należy zauważyć, że obiekty BLOB w kontenerze głównym nie może zawierać "/" w nazwach.|
|**/Disposition:** <rename&#124;no-overwrite&#124;overwrite>|`Optional.` Określa zachowanie, gdy obiekt blob, o określonym adresie już istnieje. Prawidłowe wartości tego parametru to: `rename`, `no-overwrite` i `overwrite`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `rename`.|
|**/BlobType:** <BlockBlob&#124;PageBlob>|`Optional.` Określa typ obiektu blob dla obiektów blob docelowego. Prawidłowe wartości to: `BlockBlob` i `PageBlob`. Należy pamiętać, że te wartości jest rozróżniana wielkość liter. Jeśli wartość nie zostanie określona, wartością domyślną jest `BlockBlob`.<br /><br /> W większości przypadków `BlockBlob` jest zalecane. Jeśli określisz `PageBlob`, długość każdego pliku w katalogu musi być wielokrotnością liczby 512, rozmiar strony dla stronicowych obiektów blob.|
|**/PropertyFile:** <PropertyFile\>|`Optional.` Ścieżka do pliku właściwości dla obiektów blob docelowego. Zobacz [metadanych i Format pliku właściwości usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|
|**/MetadataFile:** <MetadataFile\>|`Optional.` Ścieżka do pliku metadanych dla obiektów blob docelowego. Zobacz [metadanych i Format pliku właściwości usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji.|

### <a name="resuming-an-interrupted-copy-session"></a>Wznawianie sesji przerwane kopiowania
 Jeśli sesja kopia zostanie przerwany jakiegokolwiek powodu, możesz je wznowić, uruchamiając narzędzie za pomocą tylko w pliku dziennika, które są określonym:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Tylko najbardziej aktualną kopię sesji, jeśli została zakończona nieprawidłowo, może być wznowione.

> [!IMPORTANT]
>  Po wznowieniu sesji kopii, nie należy modyfikować źródła danych plików i katalogów, dodając lub usuwając plików.

### <a name="aborting-an-interrupted-copy-session"></a>Przerywanie sesji przerwane kopiowania
 Jeśli sesja kopia zostanie przerwana i nie jest możliwe wznowienie (na przykład, jeśli katalog źródłowy okazały się niedostępny), musi przerwać bieżącą sesję, aby go można wycofać i można uruchomić nowej sesji kopii:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Tylko ostatnia kopia sesji, jeśli została zakończona nieprawidłowo, może przerwana. Należy pamiętać, że nie można przerwać pierwszej sesji kopiowania dla dysku. Zamiast tego należy ponownie uruchomić sesję kopiowania za pomocą nowego pliku dziennika.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Ustawianie właściwości i metadanych podczas procesu importowania](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Krótki przewodnik dotyczący często używanych poleceń](storage-import-export-tool-quick-reference-v1.md) 
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Naprawianie zadania eksportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
