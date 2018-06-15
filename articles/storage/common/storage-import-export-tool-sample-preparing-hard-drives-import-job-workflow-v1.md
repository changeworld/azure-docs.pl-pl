---
title: Przykładowy przepływ pracy do przygotowywanie dyski twarde dla zadania importu Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Zobacz Przewodnik ukończenia procesu przygotowywania dysków dla zadania importu w usłudze Import/Eksport Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 66e85bd3e9e43ae360d0507f5bdf3596abbeb7d1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2017
ms.locfileid: "26692027"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Przykładowy przepływ pracy przygotowywania dysków twardych do zadania importu
W tym temacie przedstawiono Zakończ proces przygotowywania dysków dla zadania importu.  
  
W tym przykładzie importuje następujące dane do konta magazynu Azure okna o nazwie `mystorageaccount`:  
  
|Lokalizacja|Opis|  
|--------------|-----------------|  
|H:\Video|Kolekcja filmów wideo, 5 TB w sumie.|  
|H:\Photo|Kolekcja zdjęć, 30 GB w sumie.|  
|K:\Temp\FavoriteMovie.ISO|Obraz dysku A Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Kolekcja plików muzycznych w udziale sieciowym, 10 GB w sumie.|  
  
Zadania importu importuje dane do następujących miejsc docelowych na koncie magazynu:  
  
|Element źródłowy|Katalog wirtualny docelowego lub obiektu blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.Core.Windows.NET/Video|  
|H:\Photo|https://mystorageaccount.blob.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.Core.Windows.NET/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.Core.Windows.NET/Music|  
  
Z tego mapowania pliku `H:\Video\Drama\GreatMovie.mov` jest importowany do obiektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Następnie aby ustalić, ile dyski twarde są potrzebne, obliczeń rozmiar danych:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Na przykład dwa 3 TB dyski twarde powinny być wystarczające. Jednak ponieważ katalog źródłowy `H:\Video` ma 5 TB danych i pojemności pojedynczego dysku twardego w tylko 3 TB, konieczne jest przerwać `H:\Video` na dwa katalogi mniejszych: `H:\Video1` i `H:\Video2`, przed uruchomieniem programu Microsoft Azure Narzędzie importu/eksportu. Ten krok daje następujące katalogi źródłowe:  
  
|Lokalizacja|Rozmiar|Katalog wirtualny docelowego lub obiektu blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.blob.Core.Windows.NET/Video|  
|H:\Video2|2,5 TB|https://mystorageaccount.blob.Core.Windows.NET/Video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.Core.Windows.NET/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.Core.Windows.NET/Music|  
  
 Mimo że `H:\Video`katalog został podzielony na dwa katalogi wskazywać tego samego wirtualnego katalogu docelowego na koncie magazynu. Dzięki temu wszystkie pliki wideo są obsługiwane pod jedną `video` kontenera na koncie magazynu.  
  
 Następnie poprzedniej katalogi źródłowe jest rozmieszczana równomiernie do dwóch dysków twardych:  
  
||||  
|-|-|-|  
|Dysk twardy|Katalogi źródłowe|Całkowity rozmiar|  
|Pierwszy dysku|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Dysku na sekundę|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Ponadto można ustawić następujące metadane dla wszystkich plików:  
  
-   **UploadMethod:** usługi Import/Eksport systemu Windows Azure  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
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
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==  
  
-   **Cache-Control:** no-cache  
  
Aby ustawić te właściwości, należy utworzyć plik tekstowy `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Teraz można przystąpić do uruchomienia narzędzia importu/eksportu Azure przygotować dwóch dysków twardych. Należy pamiętać, że:  
  
-   Pierwszy dysk jest zainstalowany jako dysku X.  
  
-   Drugi dysk jest zainstalowany jako dysk Y.  
  
-   Klucz konta magazynu `mystorageaccount` jest `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Przygotowywanie dysku do zaimportowania podczas wstępnego ładowania danych
 
 Jeśli dane do zaimportowania występuje już na dysku, użyj /skipwrite flagi. Wartość /t i /srcdir powinna zarówno punktu dysku przygotowanym do importu. Jeśli wszystkie dane do zaimportowania nie mają tego samego katalogu wirtualnego w docelowym lub głównego konta magazynu, uruchomić to samo polecenie dla każdego katalogu docelowego oddzielnie, utrzymywanie taka sama wartość /id przez wszystkie elementy.

>[!NOTE] 
>Nie określaj/format, ponieważ będzie ona czyszczenia danych na dysku. Można określić / szyfrowania lub /bk w zależności od tego, czy dysk jest już zaszyfrowane lub nie. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Skopiuj sesje — najpierw dysków

Dla pierwszego dysku Uruchom narzędzie importu/eksportu Azure dwa razy, aby skopiować katalogi dwa źródła:  

**Najpierw skopiować sesji**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Druga kopia sesji**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Skopiuj sesji — w drugim dysku
 
Dla drugiego dysku, uruchom narzędzie importu/eksportu Azure trzy razy, jeden raz każdego dla katalogów źródła i raz dla autonomicznego Blu-Ray™ pliku obrazu):  
  
**Najpierw skopiować sesji** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Druga kopia sesji**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Trzeci sesji kopiowania**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Skopiuj zakończenia sesji

Po sesji kopiowania została ukończona, można odłączyć dwóch dysków z komputera, kopiowania i wysyłać je do odpowiednich centrum danych systemu Windows Azure. Przekazywanie plików dziennika dwóch `FirstDrive.jrn` i `SecondDrive.jrn`, podczas tworzenia zadania importu w [portalu Azure](https://portal.azure.com).  
  
## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Krótki przewodnik dla często używanych poleceń](../storage-import-export-tool-quick-reference-v1.md) 
