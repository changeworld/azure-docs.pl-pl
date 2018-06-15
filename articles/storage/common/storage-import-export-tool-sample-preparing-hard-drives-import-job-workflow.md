---
title: Przykładowy przepływ pracy do przygotowywanie dyski twarde dla zadania importu Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Zobacz Przewodnik ukończenia procesu przygotowywania dysków dla zadania importu w usłudze Import/Eksport Azure.
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
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873793"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu

W tym artykule przedstawiono Zakończ proces przygotowywania dysków dla zadania importu.

## <a name="sample-data"></a>Dane przykładowe

W tym przykładzie importuje następujące dane do konta magazynu platformy Azure o nazwie `mystorageaccount`:

|Lokalizacja|Opis|Rozmiar danych|
|--------------|-----------------|-----|
|H:\Video\ |Kolekcja filmów wideo|12 TB|
|H:\Photo\ |Kolekcja zdjęć|30 GB|
|K:\Temp\FavoriteMovie.ISO|Obraz dysku A Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Kolekcja plików muzycznych w udziale sieciowym|10 GB|

## <a name="storage-account-destinations"></a>Miejsca docelowe konto magazynu

Zadania importu będzie importowanie danych do następujących miejsc docelowych na koncie magazynu:

|Element źródłowy|Katalog wirtualny docelowego lub obiektu blob|
|------------|-------------------------------------------|
|H:\Video\ |wideo /|
|H:\Photo\ |zdjęcie /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Muzyka|

Z tego mapowania pliku `H:\Video\Drama\GreatMovie.mov` zostaną zaimportowane do obiektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Określenie wymagań dotyczących dysku twardego

Następnie aby ustalić, ile dyski twarde są potrzebne, obliczeń rozmiar danych:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Na przykład dwa 8TB dyski twarde powinny być wystarczające. Jednak ponieważ katalog źródłowy `H:\Video` ma 12TB danych i pojemności pojedynczego dysku twardego w tylko 8TB, można określić w poniższy sposób w **driveset.csv** pliku:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Narzędzie będzie rozpowszechniają danych dwóch dysków twardych w sposób zoptymalizowane.

## <a name="attach-drives-and-configure-the-job"></a>Dołącz dysków i skonfigurować zadania
Zostanie Dołącz obydwa dyski do maszyny i tworzyć woluminów. Następnie tworzyć **dataset.csv** pliku:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Ponadto można ustawić następujące metadane dla wszystkich plików:

* **UploadMethod:** usługi Import/Eksport systemu Windows Azure
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Aby ustawić metadane dla importowanych plików, Utwórz plik tekstowy `c:\WAImportExport\SampleMetadata.txt`, o następującej treści:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Można również ustawić niektórych właściwości `FavoriteMovie.ISO` obiektu blob:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==
* **Cache-Control:** no-cache

Aby ustawić te właściwości, należy utworzyć plik tekstowy `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Uruchom narzędzie Azure importu/eksportu (WAImportExport.exe)

Teraz można przystąpić do uruchomienia narzędzia importu/eksportu Azure przygotować dwóch dysków twardych.

**Podczas pierwszej sesji:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Jeśli więcej danych musi zostać dodany, należy utworzyć inny plik zestawu danych (tego samego formatu co Initialdataset).

**Dla drugiego sesji:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Po sesji kopiowania została ukończona, można odłączyć dwóch dysków z komputera, kopiowania i wysyłać je do centrum danych Azure. Będzie przekazać pliki dziennika dwóch `<FirstDriveSerialNumber>.xml` i `<SecondDriveSerialNumber>.xml`, podczas tworzenia zadania importu w portalu Azure.

## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Krótki przewodnik dla często używanych poleceń](../storage-import-export-tool-quick-reference.md)
