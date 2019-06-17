---
title: Przykładowy przepływ pracy przygotowywanie dysków twardych do zadania importu platformy Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Zobacz wskazówki dla kompletnego procesu przygotowywania dysków do zadania importu w usłudze Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: b80ba1cbe168270ec591bdd38859408eae387bbf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320588"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu
W tym temacie przedstawiono pełny proces przygotowywania dysków do zadania importu.  
  
W tym przykładzie importuje następujące dane do konta magazynu Windows Azure o nazwie `mystorageaccount`:  
  
|Lokalizacja|Opis|  
|--------------|-----------------|  
|H:\Video|Kolekcja filmów wideo, 5 TB, w sumie.|  
|H:\Photo|Kolekcja zdjęć, 30 GB miejsca w sumie.|  
|K:\Temp\FavoriteMovie.ISO|Obraz dysku A Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Kolekcja plików muzycznych w udziale sieciowym, 10 GB, w sumie.|  
  
Zadania importu importuje dane do następujących miejsc docelowych w ramach konta magazynu:  
  
|source|Katalog wirtualny docelowego lub obiektu blob|  
|------------|-------------------------------------------|  
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|  
  
Z tego mapowania pliku `H:\Video\Drama\GreatMovie.mov` jest importowany HTTPS obiektów blob:\//mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov.  
  
Następnie aby ustalić, ile dyski twarde są potrzebne, obliczenia rozmiar danych:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Na przykład dwa 3 TB, dyski twarde powinny być wystarczające. Jednak ponieważ katalog źródłowy `H:\Video` 5 TB danych i wydajności pojedynczego dysku twardego firmy jest tylko 3 TB jest przerwać `H:\Video` pod dwoma katalogami mniejszych: `H:\Video1` i `H:\Video2`, przed uruchomieniem Microsoft Azure Narzędzie importu/eksportu. W tym kroku daje następujące katalogi źródłowe:  
  
|Lokalizacja|Rozmiar|Katalog wirtualny docelowego lub obiektu blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https:\//mystorageaccount.blob.core.windows.net/music|  
  
 Mimo że `H:\Video`katalogu została podzielona w celu dwa katalogi, które one wskazują ten sam katalog wirtualny docelowym koncie magazynu. Dzięki temu wszystkie pliki wideo są obsługiwane w ramach pojedynczej `video` kontenera na koncie magazynu.  
  
 Następnie poprzedniego katalogi źródłowe są dystrybuowane równomiernie na dwa dyski twarde:  
  
||||  
|-|-|-|  
|Dysk twardy|Katalogi źródłowe|Całkowity rozmiar|  
|Pierwszy dysk|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Dysku na sekundę|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Ponadto można ustawić następujące metadane dla wszystkich plików:  
  
-   **UploadMethod:** Usługa Windows Azure Import/Export  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
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
  
-   **Content-Type:** application/octet-stream.  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Aby ustawić te właściwości, Utwórz plik tekstowy `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Teraz można przystąpić do uruchomienia narzędzia Azure Import/Export przygotowywania dwóch dysków twardych. Należy pamiętać o następujących kwestiach:  
  
-   Pierwszy dysk jest zainstalowany jako dysku X.  
  
-   Drugi dysk jest zainstalowany jako dysk Y.  
  
-   Klucz konta magazynu `mystorageaccount` jest `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Trwa przygotowywanie dysku do importowania, gdy dane są wstępnie ładowane.
 
 Jeśli dane do zaimportowania jest już obecny na dysku, należy użyć /skipwrite flagi. Wartość/t i /srcdir powinna zarówno punkt na dysku jest przygotowywane do zaimportowania. Jeśli wszystkie dane do zaimportowania nie ma tego samego wirtualnego katalogu docelowego lub katalogu głównego konta magazynu, uruchom to samo polecenie dla każdego katalogu docelowego oddzielnie, utrzymywanie wartość/identyfikator, taka sama we wszystkich przebiegach aktualizacji wszystkich.

>[!NOTE] 
>Nie należy określać wystąpiły, gdyż go spowoduje wyczyszczenie danych na dysku. Można określić / szyfrowanie lub /bk w zależności od tego, czy dysk jest już zaszyfrowany. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Skopiuj sesje — najpierw dysku

Dla pierwszego dysku Uruchom narzędzie importu/eksportu platformy Azure, dwa razy, aby skopiować katalogi dwa źródła:  

**Najpierw skopiuj sesji**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Po drugie skopiuj sesji**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Skopiuj sesje — drugi dysk
 
Dla drugiego dysku, uruchom narzędzie importu/eksportu platformy Azure trzy razy, gdy każda dla katalogi źródłowe i jeden raz w przypadku autonomicznego Blu-Ray™ pliku obrazu):  
  
**Najpierw skopiuj sesji** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Po drugie skopiuj sesji**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Trzeci sesji kopiowania**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Skopiuj zakończenia sesji

Po sesji kopiowania została ukończona, można odłączyć dwa dyski z komputera, kopiowania i odeślemy je do odpowiedniego centrum danych platformy Windows Azure. Przekaż pliki dziennika dwóch `FirstDrive.jrn` i `SecondDrive.jrn`, podczas tworzenia zadania importu w [witryny Azure portal](https://portal.azure.com).  
  
## <a name="next-steps"></a>Kolejne kroki

* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Krótki przewodnik dotyczący często używanych poleceń](../storage-import-export-tool-quick-reference-v1.md) 
