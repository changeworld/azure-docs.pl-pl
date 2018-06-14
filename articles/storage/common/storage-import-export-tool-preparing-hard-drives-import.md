---
title: Przygotowywanie dyski twarde dla zadania importu Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować dyski twarde można utworzyć zadania importu dla usługi Import/Eksport Azure za pomocą narzędzia WAImportExport.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 2854822907e818297c8d2f74cab48b0afa0d646c
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931723"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Przygotowywanie dyski twarde dla zadania importu

Narzędzie WAImportExport jest dysk przygotowania i napraw narzędzie, które można używać z [usługi Import/Eksport Microsoft Azure](../storage-import-export-service.md). Za pomocą tego narzędzia, aby skopiować dane do dysków twardych, które ma do wysłania do centrum danych Azure. Po zakończeniu zadania importu za pomocą tego narzędzia do naprawy żadnych obiektów blob, które zostały uszkodzone, brakuje lub konflikt z innymi obiektami blob. Po otrzymaniu dysków z zadania eksportu ukończone, za pomocą tego narzędzia, aby naprawić wszystkie pliki, które zostały uszkodzone lub nie istnieją na dyskach. W tym artykule rozszerzana korzystania z tego narzędzia.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="requirements-for-waimportexportexe"></a>Wymagania dotyczące WAImportExport.exe

- **Konfiguracja maszyny**
  - Windows 7, Windows Server 2008 R2 lub nowszego systemu operacyjnego Windows
  - Musi być zainstalowany program .NET framework 4. Zobacz [— często zadawane pytania](#faq) w sposób sprawdzania, czy .net Framework jest zainstalowana na tym komputerze.
- **Klucz konta magazynu** — należy co najmniej jeden z kluczy konta dla konta magazynu.

### <a name="preparing-disk-for-import-job"></a>Przygotowywanie dysku dla zadania importu

- **BitLocker —** na komputerze, na którym działa narzędzie WAImportExport musi być włączona funkcja BitLocker. Zobacz [— często zadawane pytania](#faq) do włączania funkcji BitLocker.
- **Dyski** dostępny z komputera, na którym jest uruchamiane narzędzie WAImportExport. Zobacz [— często zadawane pytania](#faq) do określenia dysku.
- **Pliki źródłowe** -planujesz importowanie plików musi być dostępny z komputera kopiowania, czy znajdują się w udziale sieciowym lub na lokalnym dysku twardym.

### <a name="repairing-a-partially-failed-import-job"></a>Naprawianie zadania importu częściowo nie powiodło się

- **Kopiuj plik dziennika** generowany po usługi Import/Eksport Azure umożliwia skopiowanie danych między konta magazynu i dysku. Znajduje się na koncie magazynu docelowego.

### <a name="repairing-a-partially-failed-export-job"></a>Naprawianie zadania eksportu częściowo nie powiodło się

- **Kopiuj plik dziennika** generowany po usługi Import/Eksport Azure umożliwia skopiowanie danych między konta magazynu i dysku. Znajduje się na koncie magazynu źródłowego.
- **Plik manifestu** -znajdują się [opcjonalnie] na dysku wyeksportowany, który został zwrócony przez firmę Microsoft.

## <a name="download-and-install-waimportexport"></a>Pobierz i zainstaluj WAImportExport

Pobierz [najnowszej wersji WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Wyodrębnij zawartość zip do katalogu na komputerze.

Następnym zadaniem jest tworzenie plików CSV.

## <a name="prepare-the-dataset-csv-file"></a>Przygotowanie pliku CSV zestawu danych

### <a name="what-is-dataset-csv"></a>Co to jest zestaw danych CSV

Plik CSV zestawu danych jest wartość flagi /dataset jest plik CSV zawierający listę katalogów i/lub listę plików do skopiowania do dysków docelowych. Pierwszym krokiem tworzenia zadania importu jest ustalenie, który katalogów i plików, które chcesz zaimportować. Może to być lista katalogów, lista plików unikatowy lub kombinacji tych dwóch. Jeśli katalog, wszystkie pliki w katalogu i jego podkatalogach będzie częścią zadania importu.

Dla każdego katalogu lub pliku do zaimportowania należy określić katalog wirtualny docelowego lub obiektu blob w usłudze obiektów Blob platformy Azure. Jako dane wejściowe do narzędzia WAImportExport zostanie użyty następujących elementów docelowych. Katalogi powinien rozdzielone znakiem ukośnika "/".

W poniższej tabeli przedstawiono niektóre przykłady celów obiektów blob:

| Pliku lub katalogu źródłowego | Docelowego obiektu blob lub katalogu wirtualnego |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.Core.Windows.NET/Video |
| H:\Photo | https://mystorageaccount.blob.Core.Windows.NET/Photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.Core.Windows.NET/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.Core.Windows.NET/Music |

### <a name="sample-datasetcsv"></a>Dataset.csv próbki

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Pola z pliku CSV zestawu danych

| Pole | Opis |
| --- | --- |
| Nieistniejący | **[Wymagane]**<br/>Wartość tego parametru reprezentuje źródło, gdzie znajdują się dane do zaimportowania. Narzędzie będzie kopiowania rekursywnie wszystkie dane znajdujące się w tej ścieżce.<br><br/>**Dozwolone wartości**: to musi być prawidłową ścieżką na komputerze lokalnym lub prawidłową ścieżką udziału i powinna być dostępna przez użytkownika. Ścieżka do katalogu musi być ścieżką bezwzględną (nie ścieżkę względną). Jeśli ścieżka kończy się wyrazem "\\", reprezentuje ścieżkę, kończenie bez katalogu else"\\" reprezentuje plik.<br/>Nie wyrażenia regularnego jest dozwolony w tym polu. Jeśli ścieżka zawiera spacje, umieść ją w "".<br><br/>**Przykład**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Wymagane]**<br/> Ścieżka do katalogu wirtualnego docelowego na koncie magazynu systemu Windows Azure. Katalog wirtualny może lub już nie istnieje. Jeśli nie istnieje, zostanie utworzyć usługi Import/Eksport.<br/><br/>Należy użyć nazwy prawidłowego kontenera podczas określania katalogu wirtualnego w docelowym lub obiektów blob. Należy pamiętać, że nazwy kontenerów muszą być małymi literami. Kontener nazewnictwa reguł, zobacz [nazewnictwa i odwołuje się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Jeśli główny jest określony, tylko struktura katalogów źródła są replikowane w docelowy kontener obiektów blob. Jeśli wymagane jest struktury katalogów inny niż w źródle, wiele wierszy mapowania w formacie CSV<br/><br/>Można określić kontener lub prefiks obiektu blob, jak muzyka/70s /. Katalog docelowy musi rozpoczynać się od nazwy kontenera, następuje ukośnik "/" i opcjonalnie mogą obejmować katalogu wirtualnego obiektów blob, w którym kończy się wyrazem "/".<br/><br/>Gdy kontenera docelowego jest nadrzędny kontener, należy jawnie określić nadrzędny kontener, łącznie z ukośnikiem jako $root /. Ponieważ nie może zawierać obiekty BLOB w kontenerze głównym "/" w ich nazw, gdy katalog docelowy jest nadrzędny kontener nie zostaną skopiowane podkatalogów w katalogu źródłowym.<br/><br/>**Przykład**<br/>Jeśli ścieżka docelowa obiektu blob jest https://mystorageaccount.blob.core.windows.net/video, wartość w tym polu może być wideo /  |
| BlobType | **[Opcjonalnie]**  bloku &#124; strony<br/>Obecnie usługa Import/Eksport obsługuje 2 rodzaje obiektów blob. Strona obiekty BLOB i domyślne BlobsBy bloku wszystkie pliki zostaną zaimportowane jako blokowych obiektów blob. I \*VHD i \*vhdx zostanie zaimportowana jako BlobsThere strony jest limit na obiekt blob blokowy i dozwolony rozmiar obiektu blob na stronie. Zobacz [wartości docelowe skalowalności magazynu](storage-scalability-targets.md) Aby uzyskać więcej informacji.  |
| Dyspozycji | **[Opcjonalnie]**  zmienić &#124; zastąpić nie &#124; zastąpić <br/> To pole określa zachowanie kopii podczas importowania tj gdy danych jest przekazywanych do konta magazynu z dysku. Dostępne są następujące opcje: Zmień nazwę &#124; czy chcesz go zastąpić &#124; zastąpić nie. Domyślnie "rename", jeśli nie określono. <br/><br/>**Zmień nazwę**: obiekt o tej samej nazwie jest obecny, tworzona jest kopia w lokalizacji docelowej.<br/>Zastąp: zastępuje pliku nowszy plik. Plik z ostatniej modyfikacji usługi wins.<br/>**Zastąp nr**: pomija zapisywania pliku, jeśli jeszcze nie istnieje.|
| MetadataFile | **[Opcjonalnie]** <br/>Wartość do tego pola jest jego pliku metadanych, który może zostać podany, jeśli ten musi zachować metadanych obiektów lub podaj niestandardowych metadanych. Ścieżka do pliku metadanych dla obiektów blob docelowego. Zobacz [Import/Eksport usługi metadanych i Format pliku właściwości](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji |
| PropertiesFile | **[Opcjonalnie]** <br/>Ścieżka do pliku właściwości dla obiektów blob docelowego. Zobacz [Import/Eksport usługi metadanych i Format pliku właściwości](../storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Przygotuj plik InitialDriveSet lub AdditionalDriveSet CSV

### <a name="what-is-driveset-csv"></a>Co to jest driveset CSV

Wartość flagi /InitialDriveSet lub /AdditionalDriveSet jest plik CSV, który zawiera listę dysków, do których litery dysków są mapowane tak, aby narzędzie poprawnie można pobrać listy dysków, aby przygotować. Jeśli rozmiar danych jest większy niż rozmiar pojedynczego dysku, narzędzie WAImportExport roześle dane na wielu dyskach zarejestrowany w tym pliku CSV w sposób zoptymalizowane.

Nie ma żadnego limitu liczby dysków, które można zapisać danych w jednej sesji. Narzędzie przeprowadził dystrybucję danych na podstawie rozmiaru dysku i rozmiar folderu. Będzie wybierać dysku, która została zoptymalizowana pod kątem rozmiar obiektu. Dane po przekazaniu do konta magazynu będzie zbieżność do struktury katalogów, która została określona w pliku zestawu danych. Aby można było utworzyć driveset CSV, wykonaj poniższe kroki.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Tworzenie woluminu podstawowego i przypisz literę dysku

Aby można było utworzyć woluminu podstawowego i przypisać literę dysku, zgodnie z instrukcjami dla "Łatwiejsze tworzenie partycji" podany w [omówienie zarządzania dyskami](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Plik przykładowy InitialDriveSet i AdditionalDriveSet CSV

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Pola z pliku Driveset CSV

| Pola | Wartość |
| --- | --- |
| Litera dysku | **[Wymagane]**<br/> Każdy dysk jest świadczona w narzędziu, jako miejsce docelowe musi mieć prosty wolumin NTFS na go i przypisane do niej literę dysku.<br/> <br/>**Przykład**: R lub r |
| FormatOption | **[Wymagane]**  Format &#124; AlreadyFormatted<br/><br/> **Format**: określenie tej sformatuje wszystkie dane na dysku. <br/>**AlreadyFormatted**: narzędzie pominie formatowanie, gdy ta wartość jest określona. |
| SilentOrPromptOnFormat | **[Wymagane]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: podanie tej wartości spowoduje włączenie użytkownika uruchomić narzędzie w trybie dyskretnym. <br/>**PromptOnFormat**: narzędzie pojawi się monit o potwierdzenie, czy akcja naprawdę służy w każdym format.<br/><br/>Jeśli nie jest ustawiona, polecenie przerwania i wyświetlony komunikat o błędzie: "Nieprawidłowa wartość parametru SilentOrPromptOnFormat: none" |
| Szyfrowanie | **[Wymagane]**  Szyfrowania &#124; AlreadyEncrypted<br/> Wartość tego pola decyduje o tym, który dysk do zaszyfrowania, a nie do. <br/><br/>**Szyfrowanie**: narzędzie sformatuje dysk. Jeśli wartość pola "FormatOption" to "Format" następnie ta wartość musi być "Szyfruj". Jeśli "AlreadyEncrypted" jest określona w tym przypadku, spowoduje wystąpił błąd "W przypadku Format Szyfruj należy także określić".<br/>**AlreadyEncrypted**: narzędzie powoduje odszyfrowania dysku przy użyciu BitLockerKey pole "ExistingBitLockerKey". Jeśli wartość pola "FormatOption" to "AlreadyFormatted", następnie ta wartość może być "Szyfruj" lub "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Wymagane]**  Jeśli wartość pola "Szyfrowanie" to "AlreadyEncrypted"<br/> Wartość tego pola jest klucza funkcji BitLocker, który jest skojarzony z określonym dysku. <br/><br/>To pole powinno pozostać puste, jeśli wartość pola "Szyfrowanie" to "Szyfruj".  W takim przypadku określono klucza funkcji BitLocker, go spowoduje wystąpił błąd "Nie należy określać klucza funkcji Bitlocker".<br/>  **Przykład**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Przygotowywanie dysku dla zadania importu

Aby przygotować dyski dla zadania importu, należy wywołać narzędzia WAImportExport z **PrepImport** polecenia. Parametrów, które należy uwzględnić zależy, czy jest to pierwsza sesja kopiowania lub sesję kolejnych kopii.

### <a name="first-session"></a>Pierwsza sesja

Pierwsza sesja kopiowania do skopiowania katalogu Single/Multiple na jednym/wiele WAImportExport dysku (w zależności od tego, co jest określony w pliku CSV) Narzędzie PrepImport polecenia dla pierwszej sesji kopiowania do skopiowania katalogów i/lub pliki w nowej sesji kopiowania:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Dodawanie danych w kolejnych sesji

W przypadku kolejnych kopii sesji nie należy określić parametrów początkowych. Należy użyć tego samego pliku dziennika, aby narzędzie do zapamiętania, gdzie pozostanie w poprzedniej sesji. Stan sesji kopiowania są zapisywane w pliku dziennika. Oto składnia sesji kolejnych kopii skopiować dodatkowe katalogi i pliki:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Dodawanie dysków do najnowszych sesji

Jeśli dane nie zmieściły się w określone dyski w InitialDriveset, co można użyć narzędzia do dodawania dodatkowych dysków do tej samej sesji kopiowania. 

>[!NOTE] 
>Identyfikator sesji powinien być zgodny identyfikator poprzedniej sesji. Plik dziennika powinien być zgodny określona w poprzedniej sesji.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Przerwij najnowszych sesji:

Jeśli sesja kopiowania zostanie przerwana i nie jest możliwe wznowienie (na przykład, jeżeli katalog źródłowy niedostępny), można przerwać w bieżącej sesji, aby go można wycofać i będzie można uruchomić nowej sesji kopiowania:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Tylko ostatniej kopii sesji, jeśli została zakończona nieprawidłowo, może przerwana. Należy pamiętać, że nie można przerwać pierwszej sesji kopii dysku. Zamiast tego należy ponownie uruchomić sesję kopiowania przy użyciu nowego pliku dziennika.

### <a name="resume-a-latest-interrupted-session"></a>Wznów najnowszych przerwania sesji

Jeśli sesja kopiowania zostanie przerwana jakiejkolwiek przyczyny, będzie możliwe wznowienie, uruchamiając narzędzie z określony tylko plik dziennika:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Przykład:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Po wznowieniu sesji kopiowania, nie należy modyfikować źródła danych plików i katalogów przez dodanie lub usunięcie plików.

## <a name="waimportexport-parameters"></a>Parametry WAImportExport

| Parametry | Opis |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Wymagane**<br/> Ścieżka do pliku dziennika. W pliku dziennika śledzi zestaw dysków i rejestruje postęp w celu przygotowania tych dysków. Zawsze należy określać plików dziennika.  |
|     / logdir:&lt;LogDirectory&gt;  | **Opcjonalnie**. Katalog dziennika.<br/> Plików pełnego dziennika, a także niektóre pliki tymczasowe będą zapisywane do tego katalogu. Jeśli nie jest określony, bieżącego katalogu będzie używany jako katalog dziennika. Katalog dziennika można określić tylko raz dla tego samego pliku dziennika.  |
|     / Identyfikator:&lt;SessionId&gt;  | **Wymagane**<br/> Identyfikator jest używany do identyfikowania sesji kopiowania sesji. Służy do dokładnych odzyskiwanie kopii przerwania sesji.  |
|     / ResumeSession  | Opcjonalny. Jeśli ostatnia sesja kopiowania zostało nieprawidłowo zakończone, można określić tego parametru można wznowić sesji.   |
|     / AbortSession  | Opcjonalny. Jeśli ostatnia sesja kopiowania zostało nieprawidłowo zakończone, ten parametr można określić przerwania sesji.  |
|     /SN:&lt;StorageAccountName&gt;  | **Wymagane**<br/> Dotyczy tylko RepairImport i RepairExport. Nazwa konta magazynu.  |
|     /SK:&lt;StorageAccountKey&gt;  | **Wymagane**<br/> Klucz konta magazynu. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Wymagane** podczas uruchamiania sesji pierwszego kopiowania<br/> Plik CSV, który zawiera listę dysków, aby przygotować.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Wymagane**. Podczas dodawania dysków do bieżącej sesji kopiowania. <br/> Plik CSV zawiera listę dodatkowych dysków, które ma zostać dodana.  |
|      / r:&lt;RepairFile&gt; | **Wymagane** dotyczy tylko RepairImport i RepairExport.<br/> Ścieżka do pliku śledzenia postępu naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy.  |
|     / d:&lt;TargetDirectories&gt; | **Wymagane**. Dotyczy tylko RepairImport i RepairExport. Dla RepairImport jeden lub więcej katalogów średnikami naprawić; Dla RepairExport, jeden katalog do naprawienia, np. głównego katalogu na dysku.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Wymagane** dotyczy tylko RepairImport i RepairExport. Ścieżka do pliku dziennika kopii dysku (pełne lub błędów).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Wymagane** dotyczy tylko RepairExport.<br/> Ścieżka do pliku manifestu dysku.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Opcjonalnie**. Dotyczy tylko RepairImport.<br/> Ścieżka do pliku zawierającego mapowania ścieżek plików względem katalogu głównego dysku do lokalizacji rzeczywistymi plikami (rozdzielany znakami tabulacji). W przypadku najpierw go zostanie wypełniona ścieżki plików z obiektami docelowymi puste, co oznacza nie zostały znalezione w TargetDirectories, odmowa dostępu, o nieprawidłowej nazwie albo istnieją w wielu katalogach. Ręcznie dodać ścieżki docelowy o poprawnej ścieżki pliku mapy i ponownie określić narzędzia można poprawnie rozpoznać ścieżki do plików.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Wymagane**. Dotyczy tylko PreviewExport.<br/> Ścieżka do pliku XML pliku zawierającego listę obiektów blob ścieżek lub obiektu blob prefiksy ścieżki dla obiektów blob do wyeksportowania. Format pliku jest taki sam jak format obiektów blob listę obiektów blob w operacji Put zadania z interfejsu API REST usługi Import/Eksport.  |
|     / DriveSize:&lt;DriveSize&gt; | **Wymagane**. Dotyczy tylko PreviewExport.<br/>  Rozmiar dysków, które mają służyć do eksportu. Na przykład, 500 GB, 1,5 TB. Uwaga: 1 GB = 1 000 000 000 bytes1 TB = 1,000,000,000,000 bajtów  |
|     / DataSet:&lt;dataset.csv&gt; | **Wymagane**<br/> Plik CSV, który zawiera listę katalogów i/lub listę plików do skopiowania do dysków docelowych.  |
|     /silentmode  | **Opcjonalnie**.<br/> Jeśli nie zostanie określony, spowoduje przypominać wymaganie dysków i konieczne potwierdzenie w taki sposób, aby kontynuować.  |

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

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Przykładowy plik dziennika (JRN) dla sesji, który rejestruje dziennik sesji

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

Narzędzie WAImportExport jest dysk przygotowania i napraw narzędzie, które za pomocą usługi Import/Eksport Microsoft Azure. Za pomocą tego narzędzia, aby skopiować dane do dysków twardych, które ma do wysłania do centrum danych platformy Azure. Po zakończeniu zadania importu za pomocą tego narzędzia do naprawy żadnych obiektów blob, które zostały uszkodzone, brakuje lub konflikt z innymi obiektami blob. Po otrzymaniu dysków z zadania eksportu ukończone, za pomocą tego narzędzia, aby naprawić wszystkie pliki, które zostały uszkodzone lub nie istnieją na dyskach.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Jak działa narzędzie WAImportExport w wielu dir źródła i dysków

Jeśli rozmiar danych jest większy niż rozmiar dysku, narzędzie WAImportExport dystrybucji danych na dyskach w sposób zoptymalizowane. Kopiowanie danych na wiele dysków może odbywać się równolegle lub sekwencyjnie. Nie ma żadnego limitu liczby dysków, które dane mogą być zapisywane na jednocześnie. Narzędzie przeprowadził dystrybucję danych na podstawie rozmiaru dysku i rozmiar folderu. Będzie wybierać dysku, która została zoptymalizowana pod kątem rozmiar obiektu. Dane po przekazaniu do konta magazynu będzie zbieżność do określonego katalogu struktury.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Gdzie można znaleźć wcześniejszej wersji narzędzia WAImportExport?

Narzędzie WAImportExport zawiera wszystkie funkcje, których zastosowano narzędzie WAImportExport V1. Narzędzie WAImportExport umożliwia użytkownikom określanie wielu źródeł i zapisać na wielu dyskach. Ponadto jedną łatwe zarządzanie wiele lokalizacji źródłowej, z których dane muszą być kopiowane w pojedynczym pliku CSV. Jednak w przypadku należy SAS pomocy technicznej lub chcesz skopiować jednego źródła do jednego dysku, możesz [narzędzie można pobrać WAImportExport V1] (http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid = 0x409) i zapoznaj się z [odwołania V1 WAImportExport](storage-import-export-tool-how-to-v1.md) Aby uzyskać pomoc dotyczącą użycia WAImportExport V1.

#### <a name="what-is-a-session-id"></a>Co to jest identyfikator sesji?

Narzędzie oczekuje sesji kopiowania (/ id) parametr być takie same, jeśli chcesz, aby rozłożyć dane na wielu dyskach. Utrzymanie tej samej nazwie sesji kopiowania spowoduje włączenie użytkownika skopiować dane z jednego lub wielu lokalizacji źródłowej do jednego lub wielu dysków/katalogów docelowych. Obsługa tego samego identyfikatora sesji umożliwia narzędzie pobrać kopię plików, z którym pozostawało ostatniego.

Jednak tej samej sesji kopiowania nie może służyć do importowania danych do różnych kont magazynu.

Gdy nazwa sesji kopiowania jest taka sama przez wiele przebiegów narzędzia pliku dziennika (/ logdir) i klucz konta magazynu (/ sk) również może być taka sama.

SessionId może składać się z liter, 0 ~ 9, understore (\_), myślnik (-) lub skrótu (#), a jego długość musi wynosić 3 ~ 30.

np. 1 sesji lub sesji #1 lub sesji\_1

#### <a name="what-is-a-journal-file"></a>Co to jest plik dziennika?

Zawsze należy uruchomić narzędzie WAImportExport do kopiowania plików na dysku twardym, narzędzie tworzy sesji kopiowania. Stan sesji kopiowania są zapisywane w pliku dziennika. Jeśli sesja kopiowania zostanie przerwana (na przykład z powodu utraty zasilania systemu), może zostać wznowiony ponownie uruchamiając narzędzie i określenie pliku dziennika w wierszu polecenia.

Dla każdego dysku twardego, czyli przygotowanie za pomocą narzędzia importu/eksportu Azure, narzędzie utworzy plik dziennika jednego o nazwie "&lt;Identyfikator_dysku&gt;.xml" gdzie dysk identyfikator jest numer seryjny skojarzonego z dysk, na którym narzędzie odczytuje z dysku. Pliki dziennika, należy ze wszystkich dysków w taki sposób, aby utworzyć zadanie importu. Plik dziennika można również wznowienie przygotowywania dysków, jeśli narzędzie zostało przerwane.

#### <a name="what-is-a-log-directory"></a>Co to jest katalog dziennika?

Katalog dziennika określa katalog, który będzie używany do przechowywania dzienników pełne, jak również tymczasowe pliki manifestu. Jeśli nie zostanie określony, bieżący katalog będzie używany jako katalog dziennika. Dzienniki są pełne dzienniki.

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="what-are-the-specifications-of-my-disk"></a>Co to są specyfikacje dysku?

Co najmniej jeden pusty 2.5 cala lub 3,5 cala SATA II o lub III lub dysków SSD dyski twarde połączony z maszyną kopiowania.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Jak włączyć funkcję BitLocker na moim komputerze?

Jest prosty sposób sprawdzić, klikając prawym przyciskiem myszy na dysku systemowym. Przedstawiono w nim opcje przez funkcję Bitlocker, jeśli ta funkcja jest włączona. Jeśli jest wyłączone, nie zobaczysz go.

![Sprawdź funkcji BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Oto artykułu na [jak włączyć funkcję BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Istnieje możliwość, że ten komputer nie ma moduł TPM. Jeśli nie są dane wyjściowe na podstawie tpm.msc, sprawdzić dalej — często zadawane pytania.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Jak wyłączyć modułu (TPM) w funkcji BitLocker?
> [!NOTE]
> Tylko wtedy, gdy w swoich serwerów nie ma żadnych modułu TPM, należy wyłączyć zasady modułu TPM. Nie jest konieczne wyłączenie modułu TPM, jeśli istnieje zaufanych modułu TPM na serwerze użytkownika. 
> 

W celu wyłączenia modułu TPM w funkcji BitLocker, przejdź przez następujące kroki:<br/>
1. Uruchom **Edytor zasad grupy** , wpisując w wierszu polecenia gpedit.msc. Jeśli **Edytor zasad grupy** wydaje się być niedostępne dla najpierw włączenie funkcji BitLocker. Zobacz poprzednie — często zadawane pytania.
2. Otwórz **lokalnych zasad komputera &gt; Konfiguracja komputera &gt; Szablony administracyjne &gt; składniki systemu Windows&gt; szyfrowanie dysków funkcją BitLocker &gt; dyski z systemem operacyjnym**.
3. Edytuj **wymaga dodatkowego uwierzytelniania przy uruchamianiu** zasad.
4. Ustawić zasady **włączone** i upewnij się, że **Zezwalaj na funkcję BitLocker bez zgodny moduł TPM** jest zaznaczony.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Jak sprawdzić, czy na komputerze zainstalowano .NET 4 lub nowszej wersji?

Wszystkie wersje programu Microsoft .NET Framework są zainstalowane w następującym katalogu: %windir%\Microsoft.NET\Framework\

Przejdź do części wyżej na komputerze docelowym, gdzie musi uruchomić narzędzie. Wyszukaj nazwę folderu, począwszy od wersji "4". Brak takiego katalogu oznacza, że .NET 4 nie jest zainstalowany na tym komputerze. Możesz pobrać .net 4 na przy użyciu maszyny [Microsoft .NET Framework 4 (Instalator internetowy)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Limity

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Jak wiele dysków może I przygotowanie/wysyłania w tym samym czasie?

Nie ma żadnego limitu liczby dysków, które można przygotować narzędzie. Jednak narzędzie oczekuje litery dysku jako dane wejściowe. Który ogranicza ją do 25 przygotowywania dysków jednocześnie. Pojedyncze zadanie może obsługiwać maksymalnie 10 dysków jednocześnie. Przygotowując dyski więcej niż 10 przeznaczonych dla tego samego konta magazynu, dyski mogą być rozproszone na wielu zadań.

#### <a name="can-i-target-more-than-one-storage-account"></a>Czy mogę wskazać więcej niż jedno konto magazynu?

Może zostać przesłane tylko jedno konto magazynu, na zadanie i w sesji pojedynczej kopii.

### <a name="capabilities"></a>Możliwości

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe szyfrują dane?

Tak. Szyfrowanie funkcją BitLocker jest włączona i jest wymagane dla tego procesu.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Jaki będzie hierarchii danych, gdy pojawi się on na koncie magazynu?

Mimo że dane są przesyłane na dyskach, dane po przekazaniu do konta magazynu będzie zbieżność do struktury katalogów, określone w pliku CSV zestawu danych.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Ile danych wejściowych dyski będą mieć aktywne we/wy równolegle, podczas kopiowania jest w toku?

Narzędzie dystrybuuje danych na dyskach wejściowego, zależnie od rozmiaru plików wejściowych. Inaczej mówiąc, liczba aktywnych dysków równolegle całkowicie delends od rodzaju danych wejściowych. W zależności od rozmiaru poszczególnych plików w zestawie danych wejściowych co najmniej jednego dysku mogą być wyświetlane we/wy active równolegle. Zobacz następne pytanie, aby uzyskać więcej informacji.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Jak narzędzie rozpowszechniają pliki dyski?

Narzędzie WAImportExport odczytuje i zapisuje pliki partiami, jedna partia zawiera maksymalnie 100000 plików. Oznacza to, że maksymalna 100000 można zapisywać pliki równoległe. Wiele dysków są zapisywane jednocześnie, jeśli te pliki 100000 są dystrybuowane do wielu dysków. Czy narzędzie zapisuje na wielu dyskach jednocześnie lub jednego dysku zależy od całkowity rozmiar partii. Na przykład w przypadku mniejszych plików, jeśli wszystkie pliki 10,0000 mieści się w pojedynczej stacji, narzędzie zapisze tylko jeden dysk podczas przetwarzania tej partii.

### <a name="waimportexport-output"></a>Dane wyjściowe WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Istnieją dwa pliki dziennika, które z nich należy przekazać do portalu Azure?

**.XML** — dla każdego dysku twardym, którego można przygotować za pomocą narzędzia WAImportExport, narzędzie utworzy plik dziennika jednego o nazwie `<DriveID>.xml` gdzie Identyfikator_dysku jest numer seryjny skojarzonego z dysk, na którym narzędzie odczytuje z dysku. Pliki dziennika, należy ze wszystkich dysków w taki sposób, aby utworzyć zadanie importu w portalu Azure. Ten plik dziennika można również wznowienie przygotowywania dysków, jeśli narzędzie zostało przerwane.

**.jrn** -pliku dziennika z sufiksem `.jrn` zawiera stan dla wszystkich sesji kopiowania na dysku twardym. Zawiera informacje potrzebne do utworzenia zadania importu. Należy zawsze określić w pliku dziennika podczas uruchamiania narzędzia WAImportExport, a także identyfikatora sesji kopiowania

## <a name="next-steps"></a>Następne kroki

* [Trwa konfigurowanie narzędzia Azure Import/Eksport](storage-import-export-tool-setup.md)
* [Ustawianie właściwości i metadanych podczas procesu importowania](storage-import-export-tool-setting-properties-metadata-import.md)
* [Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Krótki przewodnik dla często używanych poleceń](storage-import-export-tool-quick-reference.md) 
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Naprawianie zadania eksportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
