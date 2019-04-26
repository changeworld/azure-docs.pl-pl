---
title: Ustawianie właściwości i metadanych przy użyciu usługi Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określić właściwości oraz metadanych ma być ustawiony na docelowym obiektów blob podczas uruchamiania narzędzia Azure Import/Export do przygotowania stacje dysków. Dotyczy to v1 narzędzie importu/eksportu.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 66ae7045cfb94ec70f3b14046af736f784b88ea6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320554"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Ustawianie właściwości i metadanych podczas procesu importowania
Po uruchomieniu narzędzie importu/eksportu platformy Microsoft Azure do przygotowania stacje dysków, można określić właściwości oraz metadanych ma być ustawiony na docelowym obiektów blob. Wykonaj następujące kroki:  
  
1.  Aby ustawić właściwości obiektu blob, Utwórz plik tekstowy na komputerze lokalnym, który określa nazwy i wartości właściwości.  
  
2.  Aby ustawić metadane obiektu blob, Utwórz plik tekstowy na komputerze lokalnym, który określa metadane nazwy i wartości.  
  
3.  Przekazać pełną ścieżkę do jednego lub obu tych plików, aby narzędzie importu/eksportu platformy Azure w ramach `PrepImport` operacji.  
  
> [!NOTE]
>  Po określeniu właściwości lub plik metadanych w ramach sesji kopiowania te właściwości lub metadane są ustawiane dla każdego obiektu blob, który jest importowany jako część tej sesji kopii. Jeśli chcesz określić inny zbiór właściwości i metadanych dla niektórych obiektów blob zostały zaimportowane, należy utworzyć sesję oddzielna kopia przy użyciu różnych właściwości lub pliki metadanych.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Określ właściwości obiektu Blob w pliku tekstowym  
Aby określić właściwości obiektu blob, Utwórz plik tekstowy lokalnych i objęcie XML, który określa nazwy właściwości jako elementy, a wartości właściwości jako wartości. Oto przykład, który określa niektóre wartości właściwości:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Zapisz plik w lokalizacji lokalnej, takich jak `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Określ metadane obiektu Blob w pliku tekstowym  
Podobnie Aby określić metadane obiektu blob, należy utworzyć plik tekstowy lokalnego, który określa nazwy metadanych jako elementy, a wartości metadanych jako wartości. Oto przykład, który określa niektóre wartości metadanych:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Zapisz plik w lokalizacji lokalnej, takich jak `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Tworzenie sesji kopiowania, włącznie z właściwości lub pliki metadanych  
Po uruchomieniu narzędzie importu/eksportu platformy Azure, aby przygotować zadanie importu, określ plik właściwości w wierszu polecenia za pomocą `PropertyFile` parametru. Określ plik metadanych, w wierszu polecenia za pomocą `/MetadataFile` parametru. Oto przykład, który określa oba pliki:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Kolejne kroki

* [Format pliku właściwości i metadanych usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md)
