---
title: Wyświetlanie podglądu użycia dysków dla zadania eksportu Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak listę obiektów blob, wybranych dla zadania eksportu w usłudze Import/Eksport Azure w wersji preview.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873646"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Wyświetlanie podglądu użycia dysków przez zadanie eksportu
Przed utworzeniem zadania eksportu, musisz wybrać zestaw obiektów blob do wyeksportowania. Usługa Import/Eksport Microsoft Azure umożliwia za pomocą listy ścieżek obiektów blob lub prefiksy, do reprezentowania obiektów blob wybranego obiektu blob.  
  
Następnie należy określić, jak wiele dysków, musisz wysłać. Narzędzie importu/eksportu umożliwia `PreviewExport` polecenie, aby wyświetlić podgląd użycia dysków dla obiektów blob, należy wybrać na podstawie rozmiaru dysków zamierzasz używać.

## <a name="command-line-parameters"></a>Parametry wiersza polecenia

Można użyć poniższych parametrów, korzystając z `PreviewExport` polecenia narzędzia importu/eksportu.

|Parametr wiersza polecenia|Opis|  
|--------------------------|-----------------|  
|**/ logdir:**< LogDirectory\>|Opcjonalny. Katalog dziennika. Plików pełnego dziennika zostanie zapisany do tego katalogu. Jeśli katalog dziennika nie jest określony, bieżący katalog będzie używany jako katalog dziennika.|  
|**/SN:**< StorageAccountName\>|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
|**/SK:**< StorageAccountKey\>|Wymagany tylko wtedy, gdy nie określono kontenera sygnatury dostępu Współdzielonego. Klucz konta dla konta magazynu dla zadania eksportu.|  
|**/csas:**< ContainerSas\>|Wymagany tylko wtedy, gdy nie określono klucza konta magazynu. Kontener sygnatury dostępu Współdzielonego dla listę obiektów blob do wyeksportowania zadania eksportu.|  
|**/ ExportBlobListFile:**< ExportBlobListFile\>|Wymagany. Ścieżka do pliku XML pliku zawierającego listę obiektów blob ścieżek lub obiektu blob prefiksy ścieżki dla obiektów blob do wyeksportowania. Format pliku używany w `BlobListBlobPath` element [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji interfejsu API REST usługi Import/Eksport.|  
|**/ DriveSize:**< DriveSize\>|Wymagany. Rozmiar należy użyć dla zadania eksportu *np.*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Przykład wiersza polecenia

W poniższym przykładzie pokazano `PreviewExport` polecenia:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Wyeksportowany plik listy obiektów blob może zawierać nazwy obiektów blob i obiektu blob prefiksy, jak pokazano poniżej:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Narzędzie importu/eksportu Azure zawiera listę wszystkich obiektów blob do wyeksportowania oblicza porady pakietu ich na dyski o określonym rozmiarze, biorąc pod uwagę wszystkie niezbędne czynności, a następnie Szacuje liczbę dysków potrzebne do przechowywania obiektów blob i informacje na temat wykorzystania dysku.  
  
Oto przykład danych wyjściowych z dziennikami informacyjną pominięte:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Następne kroki

* [Odwołanie do usługi Azure narzędzie importu/eksportu](../storage-import-export-tool-how-to-v1.md)
