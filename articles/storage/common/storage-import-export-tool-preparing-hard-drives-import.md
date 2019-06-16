---
title: Przygotowywanie dysków twardych do zadania importu platformy Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować dyski twarde, za pomocą narzędzia WAImportExport, aby utworzyć zadanie importu dla usługi Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 777e0aac46dbffb1e491874b5889667a888aadf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478530"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Przygotowywanie dysków twardych do zadania importu

Narzędzie WAImportExport jest dysk przygotowania i napraw narzędzia które można używać z [usługi Microsoft Azure Import/Export](../storage-import-export-service.md). To narzędzie służy do kopiowania danych do dysków twardych, które są przesyłane do wysłania do centrum danych platformy Azure. Po zakończeniu zadania importu służy narzędzie to naprawić wszystkie obiekty BLOB, które zostały uszkodzone, brakuje lub konflikt z innymi obiektami blob. Po otrzymaniu dysków z zadania Zakończono eksport, umożliwia to narzędzie napraw wszystkie pliki, które zostały uszkodzone lub nie istnieją na dyskach. W tym artykule, firma Microsoft przechodzi przez zastosowanie tego narzędzia.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="requirements-for-waimportexportexe"></a>Wymagania dotyczące WAImportExport.exe

- **Konfiguracja maszyny**
  - Windows 7, Windows Server 2008 R2 lub nowszy system operacyjny Windows
  - Musi być zainstalowany program .NET framework 4. Zobacz [— często zadawane pytania](#faq) na temat sposobu Sprawdź, czy .NET Framework jest zainstalowana na komputerze.
- **Klucz konta magazynu** — należy co najmniej jeden z kluczy konta dla konta magazynu.

### <a name="preparing-disk-for-import-job"></a>Przygotowywanie dysku do zadania importu

- **BitLocker —** funkcji BitLocker musi być włączona na komputerze, w którym działa narzędzie WAImportExport. Zobacz [— często zadawane pytania](#faq) jak włączyć funkcję BitLocker.
- **Dyski** dostępne z komputera, na którym jest uruchomione narzędzie WAImportExport. Zobacz [— często zadawane pytania](#faq) specyfikacji dysku.
- **Pliki źródłowe** -musi być dostępne z tego komputera kopiowania plików, których chcesz zaimportować, czy znajdują się w udziale sieciowym lub na lokalnym dysku twardym.

### <a name="repairing-a-partially-failed-import-job"></a>Naprawianie zadania importu zakończyło się częściowym niepowodzeniem

- **Kopiuj plik dziennika** , jest generowany, gdy usługa Azure Import/Export kopiuje dane między konta magazynu i dysku. Znajduje się na koncie magazynu docelowego.

### <a name="repairing-a-partially-failed-export-job"></a>Naprawianie zadania eksportu zakończyło się częściowym niepowodzeniem

- **Kopiuj plik dziennika** , jest generowany, gdy usługa Azure Import/Export kopiuje dane między konta magazynu i dysku. Znajduje się na koncie magazynu źródła.
- **Plik manifestu** — [opcjonalnie] znajdują się w na wyeksportowanej dysku, który został zwrócony przez firmę Microsoft.

## <a name="download-and-install-waimportexport"></a>Pobierz i zainstaluj WAImportExport

Pobierz [najnowszą wersję WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Wyodrębnij spakowany zawartość do katalogu na komputerze.

Następnym zadaniem jest tworzenie plików CSV.

## <a name="prepare-the-dataset-csv-file"></a>Przygotowanie pliku CSV zestawu danych

### <a name="what-is-dataset-csv"></a>Co to jest zestaw danych CSV

Plik CSV zestaw danych jest wartość flagi/DataSet jest plik CSV zawierający listę katalogów i/lub listę plików do skopiowania do dysków docelowych. Pierwszym krokiem do tworzenie zadania importu jest ustalenie, które katalogów i plików, które chcesz zaimportować. Może to być listę katalogów, listę unikatowych plików lub kombinację tych dwóch. Gdy jest katalogiem, wszystkie pliki w katalogu i jego podkatalogach będzie częścią zadania importu.

Dla każdego katalogu lub pliku do zaimportowania należy zidentyfikować wirtualnego katalogu docelowego lub obiektu blob w usłudze Azure Blob. Te obiekty docelowe będzie używany jako dane wejściowe do narzędzia WAImportExport. Katalogi powinny być rozdzielane znakiem ukośnika "/".

W poniższej tabeli przedstawiono przykłady celów obiektów blob:

| Pliku lub katalogu źródłowego | Docelowy obiekt blob lub katalogu wirtualnego |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Przykładowy dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Pola z pliku CSV zestawu danych

| Pole | Opis |
| --- | --- |
| BasePath | **[Wymagane]**<br/>Wartość tego parametru reprezentuje źródła, w którym znajduje się dane do zaimportowania. Narzędzie będzie cyklicznie kopii wszystkich danych znajdujących się w tej ścieżce.<br><br/>**Dozwolone wartości**: To musi być prawidłową ścieżką na komputerze lokalnym lub prawidłową ścieżkę udziału i powinna być dostępna przez użytkownika. Ścieżka katalogu musi być ścieżką bezwzględną (nie ścieżkę względną). Jeśli ścieżka kończy się ciągiem "\\", reprezentuje ścieżkę, kończenie bez katalogu else"\\" reprezentuje plik.<br/>Nie wyrażenia regularnego jest dozwolone w tym polu. Jeśli ścieżka zawiera spacje, umieść ją w "".<br><br/>**Przykład**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Wymagane]**<br/> Ścieżka do katalogu wirtualnego docelowym w ramach konta magazynu platformy Windows Azure. Katalog wirtualny może lub nie może już istnieć. Jeśli nie istnieje, zostanie utworzyć usługi Import/Export.<br/><br/>Pamiętaj używać nazw prawidłowego kontenera, określając docelowy katalogi wirtualne czy za obiekty BLOB. Należy pamiętać, że nazwy kontenerów muszą być małymi literami. Reguły nazewnictwa kontenerów, zobacz [nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Jeśli tylko katalogu głównego jest określony, struktura katalogów źródła jest replikowany w docelowy kontener obiektów blob. Jeśli pożądane jest struktura innego katalogu niż w źródle, wiele wierszy mapowania w woluminie CSV<br/><br/>Można określić kontener lub prefiks obiektu blob, takich jak utworów muzycznych/70s /. Katalog docelowy musi zaczynać się od nazwy kontenera, następuje ukośnik "/" i opcjonalnie może zawierać katalogu wirtualnego obiektów blob, który kończy się ciągiem "/".<br/><br/>Gdy kontenera docelowego jest nadrzędny kontener, należy jawnie określić kontener głównego, łącznie z ukośnikiem jako $root /. Ponieważ nie może zawierać obiekty BLOB w kontenerze katalogu głównego "/" w nazwach podkatalogów w katalogu źródłowym nie zostaną skopiowane, gdy katalog docelowy jest nadrzędny kontener.<br/><br/>**Przykład**<br/>Jeśli ścieżka docelowa obiektu blob jest https://mystorageaccount.blob.core.windows.net/video, wartość tego pola może być wideo /  |
| BlobType | **[Opcjonalnie]**  bloku &#124; strony<br/>Obecnie usługa Import/Export obsługuje 2 typy obiektów blob. Strona, obiekty BLOB i domyślne BlobsBy bloku, wszystkie pliki zostaną zaimportowane jako blokowe obiekty BLOB. I \*VHD i \*vhdx zostaną zaimportowane, jak BlobsThere strony jest ograniczenie blokowych obiektów blob i — obiekt blob typu page dozwolony rozmiar. Zobacz [cele usługi Storage dotyczące skalowalności](storage-scalability-targets.md) Aby uzyskać więcej informacji.  |
| Dyspozycja | **[Opcjonalnie]**  Zmień nazwę &#124; zastąpić nie &#124; zastępowania <br/> To pole określa zachowania dotyczącego kopiowania podczas importowania tj gdy data jest przekazywany do konta magazynu z dysku. Dostępne są następujące opcje: zmiana nazwy&#124;zastąpić&#124;zastąpić nie. Wartość domyślna to "Zmień nazwę" Jeśli niczego nie określono. <br/><br/>**Zmień nazwę**: Jeśli obiekt o takiej samej nazwie jest obecny, tworzona jest kopia w miejscu docelowym.<br/>Zastąpienie: zastępuje plik nowszy plik. Plik z ostatniej modyfikacji usługi wins.<br/>**Zastąpienie nie**: Pomija zapisywania pliku, jeśli już istnieje.|
| MetadataFile | **[Opcjonalnie]** <br/>Wartość do tego pola jest plik metadanych, które mogą być dostarczone, jeśli ten musi zachować metadane obiektów lub niestandardowych metadanych. Ścieżka do pliku metadanych dla obiektów blob docelowego. Zobacz [metadanych i Format pliku właściwości usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji |
| PropertiesFile | **[Opcjonalnie]** <br/>Ścieżka do pliku właściwości dla obiektów blob docelowego. Zobacz [metadanych i Format pliku właściwości usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Przygotowanie pliku InitialDriveSet lub AdditionalDriveSet CSV

### <a name="what-is-driveset-csv"></a>Co to jest driveset CSV

Wartość flagi /InitialDriveSet lub /AdditionalDriveSet jest plik CSV, który zawiera listę dysków, do których są mapowane litery dysku, tak, aby narzędzie poprawnie można pobrać listy dysków, które mają zostać przygotowane. Jeśli rozmiar danych jest większy niż rozmiar pojedynczego dysku, narzędzie WAImportExport będzie dystrybuować dane na wielu dyskach zarejestrowany w tym pliku CSV w sposób zoptymalizowany.

Nie ma żadnego limitu liczby dysków, które mogą być zapisywane dane do podczas jednej sesji. Narzędzie dystrybuowania danych na podstawie rozmiaru dysku i rozmiar folderu. Wybierz dysk który jest zoptymalizowany pod kątem rozmiar obiektu. Dane przekazywane do konta magazynu będzie zbieżne do struktury katalogów, która została określona w pliku zestawu danych. Aby można było utworzyć driveset CSV, wykonaj poniższe kroki.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Utwórz wolumin podstawowy i przypisz literę dysku

Aby utworzyć wolumin podstawowy i przypisać jej literę dysku, postępując zgodnie z instrukcjami dotyczącymi "Łatwiejsze tworzenie partycji" podana w [Przegląd Zarządzanie dyskami](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Plik przykładowy InitialDriveSet i AdditionalDriveSet CSV

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Pola z pliku Driveset CSV

| Pola | Wartość |
| --- | --- |
| DriveLetter | **[Wymagane]**<br/> Każdego dysku, które są udostępniane do narzędzia, ponieważ miejsce docelowe musi mieć prosty wolumin NTFS na go i literę dysku przypisaną do niego.<br/> <br/>**Przykład**: R lub r |
| FormatOption | **[Wymagane]**  Format &#124; AlreadyFormatted<br/><br/> **Format**: Określenie tej sformatuje wszystkie dane na dysku. <br/>**AlreadyFormatted**: Narzędzie zostanie pominięta, formatowanie, gdy ta wartość jest określona. |
| SilentOrPromptOnFormat | **[Wymagane]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: Podanie tej wartości spowoduje włączenie użytkownika uruchomić narzędzie w trybie dyskretnym. <br/>**PromptOnFormat**: Narzędzie zostanie wyświetlony monit o potwierdzenie, czy akcja naprawdę jest przeznaczony na każdym formatu.<br/><br/>Jeśli nie został ustawiony, polecenie Przerwij i wyświetlony komunikat o błędzie: "Nieprawidłowa wartość parametru SilentOrPromptOnFormat: Brak" |
| Szyfrowanie | **[Wymagane]**  Szyfrowania &#124; AlreadyEncrypted<br/> Wartość tego pola decyduje o który dysk do zaszyfrowania, które nie. <br/><br/>**Szyfruj**: Narzędzie sformatuje dysk. Jeśli wartość pola "FormatOption" jest "Format" Ta wartość jest wymagana jako "Szyfruj". Jeśli "AlreadyEncrypted" jest określony w tym przypadku, spowoduje błąd "Jeśli określono Format szyfrowania musi być także określona".<br/>**AlreadyEncrypted**: Narzędzie spowoduje odszyfrowanie dysku przy użyciu BitLockerKey podany w polu "ExistingBitLockerKey". Jeśli wartość pola "FormatOption" to "AlreadyFormatted", następnie ta wartość może być "Szyfruj" lub "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Wymagane]**  w przypadku wartości "Szyfrowanie" pola "AlreadyEncrypted"<br/> Wartość tego pola jest klucza funkcji BitLocker, który jest skojarzony z określonym dysku. <br/><br/>To pole powinno pozostać puste, jeśli wartość pola "Szyfrowanie" to "Szyfruj".  Jeśli klucz funkcji BitLocker jest określony w tym przypadku, spowoduje błąd "Nie należy określać klucz funkcji BitLocker".<br/>  **Przykład**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Przygotowywanie dysku do zadania importu

Aby przygotować dyski do zadania importu, wywołaj narzędzie WAImportExport z **PrepImport** polecenia. Parametry, które możesz uwzględnić zależy, czy jest to pierwsza sesja kopiowania lub sesji kolejnych kopii.

### <a name="first-session"></a>Pierwsza sesja

Pierwsza sesja kopiowania można skopiować katalogu Single/Multiple do pojedynczego/wielu WAImportExport dysku (zależnie od tego, co określono w pliku CSV) Narzędzie PrepImport polecenia dla pierwszej sesji kopiowania do skopiowania katalogów i/lub plików w nowej sesji kopii:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Dodawanie danych podczas kolejnych sesji.

W sesji kolejnych kopii nie należy określić parametry początkowe. Należy użyć tego samego pliku dziennika, aby narzędzie do zapamiętania, gdzie pozostanie w poprzedniej sesji. Stan sesji kopiowania są zapisywane do pliku dziennika. Poniżej przedstawiono składnię sesję kolejnych kopiowania do skopiowania dodatkowe katalogi i pliki:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Dodawanie dysków do najnowszych sesji

Jeśli dane nie zmieściły się w określone dyski w InitialDriveset, jeden służy narzędzie można dodać dodatkowe dyski do tej samej sesji kopiowania. 

> [!NOTE]
> Identyfikator sesji powinien być zgodny z identyfikatorem poprzedniej sesji. Plik dziennika powinien być zgodny określona w poprzedniej sesji.
> 
> ```
> WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
> ```

**Przykład:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Przerwij najnowszych sesji:

Jeśli sesja kopia zostanie przerwana i nie jest możliwe wznowienie (na przykład, jeśli katalog źródłowy okazały się niedostępny), musi przerwać bieżącą sesję, aby go można wycofać i można uruchomić nowej sesji kopii:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Tylko ostatnia kopia sesji, jeśli została zakończona nieprawidłowo, może przerwana. Należy pamiętać, że nie można przerwać pierwszej sesji kopiowania dla dysku. Zamiast tego należy ponownie uruchomić sesję kopiowania za pomocą nowego pliku dziennika.

### <a name="resume-a-latest-interrupted-session"></a>Wznowienie sesji przerwane najnowsza wersja

Jeśli sesja kopia zostanie przerwany jakiegokolwiek powodu, możesz je wznowić, uruchamiając narzędzie za pomocą tylko w pliku dziennika, które są określonym:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Po wznowieniu sesji kopii, nie należy modyfikować źródła danych plików i katalogów, dodając lub usuwając plików.

## <a name="waimportexport-parameters"></a>Parametry WAImportExport

| Parametry | Opis |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Wymagane**<br/> Ścieżka do pliku dziennika. W pliku dziennika śledzi zestaw dysków i rekordy postępu w ramach przygotowywania tych dysków. Zawsze należy określać plików dziennika.  |
|     /logdir:&lt;LogDirectory&gt;  | **Opcjonalnie**. Katalog dziennika.<br/> Plików pełnego dziennika, a także niektóre pliki tymczasowe będą zapisywane do tego katalogu. W przeciwnym razie zostanie użyty określony, bieżący katalog jako katalog dziennika. Katalog dziennika można określić tylko raz dla tego samego pliku dziennika.  |
|     / Identyfikator:&lt;SessionId&gt;  | **Wymagane**<br/> Sesji, której identyfikator jest używany do identyfikowania sesji kopiowania. Służy do dokładnego odzyskiwanie sesji przerwane kopiowania.  |
|     / ResumeSession  | Opcjonalny. Jeśli ostatniej sesji kopiowania zostało nieprawidłowo zakończone, można określić ten parametr można wznowić sesji.   |
|     / AbortSession  | Opcjonalny. Jeśli ostatniej sesji kopiowania zostało nieprawidłowo zakończone, można określić ten parametr do przerwania sesji.  |
|     /sn:&lt;StorageAccountName&gt;  | **Wymagane**<br/> Dotyczy tylko RepairImport i RepairExport. Nazwa konta magazynu.  |
|     /sk:&lt;StorageAccountKey&gt;  | **Wymagane**<br/> Klucz konta magazynu. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **Wymagane** podczas uruchamiania sesji pierwszego kopiowania<br/> Plik CSV, który zawiera listę dysków, aby przygotować.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **Wymagane**. Podczas dodawania dysków do bieżącej sesji kopiowania. <br/> Plik CSV, który zawiera listę dodatkowych dysków do dodania.  |
|      /r:&lt;RepairFile&gt; | **Wymagane** dotyczy wyłącznie RepairImport i RepairExport.<br/> Ścieżka do pliku do śledzenia postępu naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy.  |
|     / d:&lt;TargetDirectories&gt; | **Wymagane**. Dotyczy tylko RepairImport i RepairExport. Aby uzyskać RepairImport jeden lub więcej katalogów średnikami naprawić; RepairExport, jeden katalog do naprawienia, np. się w głównym katalogu na dysku.  |
|     /CopyLogFile:&lt;DriveCopyLogFile&gt; | **Wymagane** dotyczy wyłącznie RepairImport i RepairExport. Ścieżka do pliku dziennika kopiowania dysku (pełne lub błędów).  |
|     /ManifestFile:&lt;DriveManifestFile&gt; | **Wymagane** dotyczy wyłącznie RepairExport.<br/> Ścieżka do pliku manifestu na dysku.  |
|     /PathMapFile:&lt;DrivePathMapFile&gt; | **Opcjonalnie**. Dotyczy tylko RepairImport.<br/> Ścieżka do pliku zawierającego mapowania ścieżek plików względem katalogu głównego dysku do lokalizacji rzeczywiste pliki (rozdzielany znakami tabulacji). Jeśli najpierw zostanie określony, jego zostanie wypełniony ścieżki do plików za pomocą pustych elementów docelowych, oznacza to, nie występują one w TargetDirectories, odmowa dostępu, o nieprawidłowej nazwie albo istnieją one w wielu katalogach. Ręcznie dodać ścieżki docelowy o poprawnej ścieżki pliku mapy i ponownie określić dla narzędzia poprawnie rozpoznać ścieżki do plików.  |
|     /ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Wymagane**. Dotyczy tylko PreviewExport.<br/> Ścieżka do pliku XML plik zawierający listę ścieżek obiektów blob lub obiektu blob prefiksy ścieżki dla obiektów blob do wyeksportowania. Format pliku jest taki sam jak format obiektów blob listy obiektów blob w operacji Put zadanie interfejsu API REST usługi Import/Export.  |
|     /DriveSize:&lt;DriveSize&gt; | **Wymagane**. Dotyczy tylko PreviewExport.<br/>  Rozmiar dysków, które ma być używany do eksportu. Na przykład, 500 GB, 1,5 TB. Uwaga: TB bytes1 1 GB = 1 000 000 000 = 1,000,000,000,000 bajtów  |
|     /DataSet:&lt;dataset.csv&gt; | **Wymagane**<br/> Plik CSV, który zawiera listę katalogów i/lub listę plików do skopiowania do dysków docelowych.  |
|     /silentmode  | **Opcjonalnie**.<br/> Jeśli nie zostanie określony, spowoduje to przypomnieć o wymogu użycia dysków i potrzebujesz potwierdzenie w taki sposób, aby kontynuować.  |

## <a name="tool-output"></a>Dane wyjściowe narzędzia

### <a name="sample-drive-manifest-file"></a>Przykładowy plik manifestu dysku

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Przykładowy plik dziennika (XML) dla każdego dysku

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Przykładowy plik dziennika (JRN) dla sesji, która zawiera rekordy dziennika sesji

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>Często zadawane pytania

### <a name="general"></a>Ogólne

#### <a name="what-is-waimportexport-tool"></a>Co to jest narzędzie WAImportExport?

Narzędzie WAImportExport to dysk przygotowania i napraw narzędzia które można użyć z usługą Microsoft Azure Import/Export. To narzędzie służy do kopiowania danych do dysków twardych, które są przesyłane do wysłania do centrum danych platformy Azure. Po zakończeniu zadania importu służy narzędzie to naprawić wszystkie obiekty BLOB, które zostały uszkodzone, brakuje lub konflikt z innymi obiektami blob. Po otrzymaniu dysków z zadania Zakończono eksport, umożliwia to narzędzie napraw wszystkie pliki, które zostały uszkodzone lub nie istnieją na dyskach.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Jak działa narzędzie WAImportExport o wielu dir źródła i dyski

Jeśli rozmiar danych jest większy niż rozmiar dysku, narzędzie WAImportExport będzie dystrybuować dane na dyskach w sposób zoptymalizowany. Kopiowanie danych na wiele dysków może odbywać się równolegle lub sekwencyjnie. Nie ma żadnego limitu liczby dysków, które mogą być zapisywane dane do jednocześnie. Narzędzie dystrybuowania danych na podstawie rozmiaru dysku i rozmiar folderu. Wybierz dysk który jest zoptymalizowany pod kątem rozmiar obiektu. Dane przekazywane do konta magazynu będzie zbieżność do struktury określonego katalogu.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Gdzie można znaleźć wcześniejszej wersji narzędzia WAImportExport?

Narzędzie WAImportExport obejmuje wszystkie funkcje, których narzędzie WAImportExport V1. Narzędzie WAImportExport pozwala użytkownikom na określanie wielu źródeł i zapisu związane z wieloma dyskami. Ponadto jeden łatwe zarządzanie wiele lokalizacji źródłowej, z których dane trzeba skopiować w jednym pliku CSV. W przypadku, gdy potrzebujesz pomocy technicznej sygnatury dostępu Współdzielonego lub chcesz skopiować pojedyncze źródło do jednego dysku, można jednak [Pobierz narzędzie V1 WAImportExport](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) i odnoszą się do [odwołania V1 WAImportExport](storage-import-export-tool-how-to-v1.md) Aby uzyskać pomoc dotyczącą użycia WAImportExport V1 .

#### <a name="what-is-a-session-id"></a>Co to jest identyfikator sesji?

Narzędzie oczekuje sesji kopiowania (/ id) parametr będzie taka sama, jeśli celem jest podzielenie danych na wielu dyskach. Utrzymanie tej samej nazwie sesji kopiowania umożliwi użytkownikowi kopiowanie danych z jednego lub wielu lokalizacji źródłowej do jednego lub wielu dysków/katalogów docelowych. Obsługa tego samego identyfikatora sesji umożliwia narzędzia pobrać kopię plików, z którym pozostawało ostatniego.

Jednak tej samej sesji kopiowania nie umożliwia importowanie danych do różnych kont magazynu.

Podczas kopiowania sesji nazwa jest taka sama wielu uruchomień narzędzia pliku dziennika (/ logdir) i klucz konta magazynu (/ sk) również powinien być taki sam.

SessionId może zawierać litery, 0 ~ 9, podkreślenie (\_), myślnik (-) lub skrótu (#), a jego długość musi być 3 ~ 30.

np. 1 sesji lub sesji #1 lub sesji\_1

#### <a name="what-is-a-journal-file"></a>Co to jest plik dziennika?

Każdym przy uruchamianiu narzędzia WAImportExport do kopiowania plików na dysku twardym, narzędzie tworzy sesję kopiowania. Stan sesji kopiowania są zapisywane do pliku dziennika. Jeśli sesja kopia zostanie przerwany (na przykład z powodu utraty zasilania systemu), może być wznowione przez ponownie uruchomienie narzędzia i określenie pliku dziennika, w wierszu polecenia.

Dla każdego dysku twardego, który należy przygotować za pomocą narzędzia Azure Import/Export, narzędzie utworzy plik pojedynczego dziennika o nazwie "&lt;Identyfikator_dysku&gt;.xml" gdzie identyfikator dysku jest numer seryjny, skojarzone z dysku, który narzędzie odczytuje z dysku. Pliki dziennika, należy ze wszystkich dysków w taki sposób, aby utworzyć zadanie importu. Plik dziennika można również wznowić przygotowywania dysku, jeśli narzędzie zostanie przerwany.

#### <a name="what-is-a-log-directory"></a>Co to jest katalog dziennika?

Katalog dziennika określa katalog ma być używany do przechowywania pełne dzienniki, jak również tymczasowych plików manifestu. Jeśli nie zostanie określony, bieżący katalog będzie służyć jako katalog dziennika. Dzienniki są pełne dzienniki.

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="what-are-the-specifications-of-my-disk"></a>Co to są specyfikacje dysku?

Co najmniej jeden pusty cala 2.5 lub 3,5 cala SATA II o lub III lub dysków SSD dyski twarde nawiązaniu połączenia z maszyną kopiowania.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Jak włączyć funkcję BitLocker na moim komputerze?

Jest prosty sposób, aby sprawdzić, klikając prawym przyciskiem myszy na dysku systemowym. Przedstawiono w nim opcji funkcji BitLocker, jeśli ta funkcja jest włączona. Jeśli jest wyłączone, nie zobaczysz go.

![Sprawdź funkcji BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Poniżej przedstawiono artykułu [jak włączyć funkcję BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Istnieje możliwość, że komputer nie ma moduł TPM. Jeśli nie otrzymasz dane wyjściowe przy użyciu tpm.msc, Przyjrzyj się dalej często zadawane pytania.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Jak wyłączyć Trusted Platform Module (TPM) w funkcji BitLocker?
> [!NOTE]
> Tylko wtedy, gdy nie ma żadnych modułu TPM w swoich serwerów, należy wyłączyć zasady modułu TPM. Nie jest konieczne wyłączenie modułu TPM, w przypadku zaufanej modułu TPM na serwerze przez użytkownika. 
> 

W celu wyłączenia modułu TPM w funkcji BitLocker, należy przejść przez następujące kroki:<br/>
1. Uruchom **Edytor zasad grupy** , wpisując gpedit.msc w wierszu polecenia. Jeśli **Edytor zasad grupy** wydaje się być niedostępne dla najpierw włączeniem funkcji BitLocker. Zobacz poprzednie — często zadawane pytania.
2. Otwórz **lokalne zasady komputera &gt; konfiguracji komputera &gt; Szablony administracyjne &gt; składników Windows&gt; szyfrowanie dysków funkcją BitLocker &gt; dyskizsystememoperacyjnym**.
3. Edytuj **wymagają dodatkowego uwierzytelniania przy uruchamianiu** zasad.
4. Ustaw zasady **włączone** i upewnij się, **Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM** jest zaznaczone.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Jak sprawdzić, czy na moim komputerze zainstalowano .NET 4 lub nowszej wersji?

Wszystkie wersje programu Microsoft .NET Framework są zainstalowane w następującym katalogu: %windir%\Microsoft.NET\Framework\

Przejdź do części wyżej wymienionych na komputerze docelowym, gdzie narzędzie musi zostać uruchomiony. Wyszukaj nazwę folderu, począwszy od wersji "4". Brak takiego katalogu oznacza, że .NET 4 nie jest zainstalowany na tym komputerze. .NET 4 można pobrać przy użyciu [Microsoft .NET Framework 4 (Instalator internetowy)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Limits

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Jak wiele dysków może mogę przygotować/wysyłania w tym samym czasie?

Nie ma żadnego limitu liczby dysków, które można przygotować narzędzie. Jednak narzędzie oczekuje, że litery dysku jako dane wejściowe. Która ogranicza ją do 25 przygotowywania dysków jednocześnie. Pojedyncze zadanie może obsługiwać maksymalnie 10 dysków w danym momencie. Jeśli więcej niż 10 dysków przeznaczonych dla tego samego konta magazynu należy przygotować, dyski mogą być dystrybuowane w wielu zadaniach.

#### <a name="can-i-target-more-than-one-storage-account"></a>Czy można wskazać więcej niż jedno konto magazynu?

Tylko jedno konto magazynu można przesyłać na zadanie, jak i w sesji pojedynczej kopii.

### <a name="capabilities"></a>Możliwości

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Czy WAImportExport.exe szyfruje Moje dane?

Tak. Szyfrowanie funkcją BitLocker jest włączona i wymagane dla tego procesu.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Jaka będzie hierarchii Moje dane, gdy się pojawi się na koncie magazynu?

Mimo, że dane są rozproszone na dyskach, danych, gdy przekazywane do konta magazynu będzie zbieżne do struktury katalogów określonych w pliku CSV zestawu danych.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Ile danych wejściowych z dysków będzie miał aktywnych we/wy w sposób równoległy, gdy Trwa kopiowanie?

Narzędzie rozkłada dane na dyskach danych wejściowych, na podstawie rozmiaru plików wejściowych. Inaczej mówiąc, liczba aktywnych dysków w sposób równoległy całkowicie jest zależna od charakteru danych wejściowych. W zależności od rozmiaru poszczególnych plików w zestawie danych wejściowych co najmniej jednego dysku mogą być wyświetlane aktywnych we/wy równolegle. Zobacz następne pytanie, aby uzyskać więcej informacji.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Jak narzędzie rozdystrybuować pliki dyski?

Narzędzie WAImportExport odczytuje i zapisuje pliki partiami, w jednej partii zawiera maksymalnie 100000 plików. Oznacza to, że maksymalna 100000 można zapisywać pliki równoległych. Wiele dysków są zapisywane jednocześnie, jeśli te pliki 100000 są rozproszone na wielu dyskach. Jednak czy narzędzie zapisuje na wiele dysków jednocześnie lub jednego dysku zależy od całkowity rozmiar partii. Na przykład w przypadku mniejszych plików, jeśli wszystkie pliki 10,0000 mieści się w jednym dysku, narzędzie zapisuje do tylko jednego dysku podczas przetwarzania tej partii.

### <a name="waimportexport-output"></a>Dane wyjściowe WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Istnieją dwa pliki dziennika, który z nich należy przekazać do witryny Azure portal?

**XML** — dla każdego dysku twardego, który należy przygotować za pomocą narzędzia WAImportExport, narzędzie utworzy plik pojedynczego dziennika o nazwie `<DriveID>.xml` gdzie Identyfikator_dysku jest numer seryjny, skojarzone z dysku, który narzędzie odczytuje z dysku. Pliki dziennika, należy ze wszystkich dysków w taki sposób, aby utworzyć zadanie importu w witrynie Azure portal. Ten plik dziennika można również wznowić przygotowywania dysku, jeśli narzędzie zostanie przerwany.

**jrn** — plik dziennika z sufiksem `.jrn` zawiera stan dla wszystkich sesji kopiowania na dysk twardy. Zawiera informacje potrzebne do utworzenia zadania importu. Zawsze należy określić w pliku dziennika podczas uruchamiania narzędzia WAImportExport, a także Kopiuj identyfikator sesji.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup.md)
* [Ustawianie właściwości i metadanych podczas procesu importowania](storage-import-export-tool-setting-properties-metadata-import.md)
* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Krótki przewodnik dotyczący często używanych poleceń](storage-import-export-tool-quick-reference.md) 
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Naprawianie zadania eksportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
