---
title: Ustawienie właściwości i metadanych za pomocą Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określić właściwości oraz metadanych ma być ustawiony na obiekty BLOB docelowego podczas uruchamiania narzędzia importu/eksportu Azure do przygotowania dysków. Odnosi się do v1 narzędzia importu/eksportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873737"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Ustawianie właściwości i metadanych podczas procesu importowania
Po uruchomieniu narzędzia importu/eksportu w usłudze Microsoft Azure do przygotowania dysków, można określić właściwości i metadanych ma być ustawiony na docelowym obiektów blob. Wykonaj następujące kroki:  
  
1.  Aby ustawić właściwości obiektu blob, Utwórz plik tekstowy na komputerze lokalnym, który określa nazwy i wartości właściwości.  
  
2.  Aby ustawić metadane obiektu blob, Utwórz plik tekstowy na komputerze lokalnym, określający, metadane nazwy i wartości.  
  
3.  Przekaż pełną ścieżkę do jednej lub obu tych plików do narzędzia importu/eksportu Azure jako część `PrepImport` operacji.  
  
> [!NOTE]
>  Po określeniu właściwości lub metadane pliku w ramach sesji kopiowania tych właściwości lub metadane są ustawione dla każdy obiekt blob importowanych w ramach tej sesji kopiowania. Jeśli chcesz określić inny zestaw właściwości lub metadanych dla niektórych importowanych obiektów blob, należy utworzyć sesję oddzielna kopia z różnych właściwości lub pliki metadanych.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Określ właściwości obiektu Blob w pliku tekstowym  
Aby określić właściwości obiektu blob, Utwórz plik tekstowy lokalnego i zawierać kod XML, który określa właściwości nazwy elementów i wartości właściwości jako wartości. Oto przykład, który określa niektórych wartości właściwości:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Zapisz plik do lokalnej lokalizacji, takiej jak `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Określ metadane obiektu Blob w pliku tekstowym  
Podobnie Aby określić metadane obiektu blob, należy utworzyć plik tekstowy lokalnego, który określa nazwy metadanych jako elementy, a wartości metadanych jako wartości. Oto przykład, który określa niektórych wartości metadanych:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Zapisz plik do lokalnej lokalizacji, takiej jak `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Tworzenie sesji kopiowania, włącznie z właściwości lub pliki metadanych  
W celu uruchomienia narzędzia importu/eksportu Azure, aby przygotować zadanie importu, określ plik właściwości przy użyciu wiersza polecenia `PropertyFile` parametru. Określ plik metadanych przy użyciu wiersza polecenia `/MetadataFile` parametru. Oto przykład, który określa oba pliki:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Następne kroki

* [Format pliku właściwości i metadanych usługi Import/Export](../storage-import-export-file-format-metadata-and-properties.md)
