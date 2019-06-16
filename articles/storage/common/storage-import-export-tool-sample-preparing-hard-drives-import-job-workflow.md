---
title: Przykładowy przepływ pracy przygotowywanie dysków twardych do zadania importu platformy Azure Import/Export | Dokumentacja firmy Microsoft
description: Zobacz wskazówki dla kompletnego procesu przygotowywania dysków do zadania importu w usłudze Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 42da285fbb55df43959506996bcde9cf547c2a22
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320571"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu

W tym artykule przedstawiono pełny proces przygotowywania dysków do zadania importu.

## <a name="sample-data"></a>Dane przykładowe

W tym przykładzie importuje następujące dane do konta magazynu platformy Azure o nazwie `mystorageaccount`:

|Lokalizacja|Opis|Rozmiar danych|
|--------------|-----------------|-----|
|H:\Video\ |Kolekcja filmów wideo|12 TB|
|H:\Photo\ |Kolekcja zdjęć|30 GB|
|K:\Temp\FavoriteMovie.ISO|Obraz dysku A Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Kolekcja plików muzycznych w udziale sieciowym|10 GB|

## <a name="storage-account-destinations"></a>Miejsca docelowe konto magazynu

Zadania importu zostaną zaimportowane dane do następujących miejsc docelowych w ramach konta magazynu:

|source|Katalog wirtualny docelowego lub obiektu blob|
|------------|-------------------------------------------|
|H:\Video\ |wideo /|
|H:\Photo\ |zdjęcie /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Muzyka|

Z tego mapowania pliku `H:\Video\Drama\GreatMovie.mov` zostaną zaimportowane do obiektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Określanie wymagań dotyczących dysku twardego

Następnie aby ustalić, ile dyski twarde są potrzebne, obliczenia rozmiar danych:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Na przykład dwa 8TB, dyski twarde powinny być wystarczające. Jednak ponieważ katalog źródłowy `H:\Video` 12TB danych i wydajności pojedynczego dysku twardego firmy jest tylko 8TB, można określić w następujący sposób **driveset.csv** pliku:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Narzędzie będzie dystrybuować dane dwa dyski twarde w sposób zoptymalizowany.

## <a name="attach-drives-and-configure-the-job"></a>Dołączanie dysków i konfigurowanie zadania
Będzie dołączyć obydwa dyski do maszyny, a następnie utworzenie woluminów. Następnie możesz tworzyć **dataset.csv** pliku:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Ponadto można ustawić następujące metadane dla wszystkich plików:

* **UploadMethod:** Usługa Windows Azure Import/Export
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Aby ustawić metadanych dla importowanych plików, Utwórz plik tekstowy `c:\WAImportExport\SampleMetadata.txt`, o następującej zawartości:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Można również ustawić niektóre właściwości `FavoriteMovie.ISO` obiektów blob:

* **Content-Type:** application/octet-stream.
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Aby ustawić te właściwości, Utwórz plik tekstowy `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Uruchom narzędzie Azure Import/Export (WAImportExport.exe)

Teraz można przystąpić do uruchomienia narzędzia Azure Import/Export przygotowywania dwóch dysków twardych.

**Podczas pierwszej sesji:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Jeśli więcej danych musi zostać dodany, należy utworzyć inny plik zestawu danych (tego samego formatu co początkowego zestawu danych).

**Dla drugiego sesji:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Po sesji kopiowania została ukończona, można odłączyć dwa dyski z komputera, kopiowania i odeślemy je do centrum danych platformy Azure. Należy przekazać plików dziennika dwóch `<FirstDriveSerialNumber>.xml` i `<SecondDriveSerialNumber>.xml`, podczas tworzenia zadania importu w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki

* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Krótki przewodnik dotyczący często używanych poleceń](../storage-import-export-tool-quick-reference.md)
