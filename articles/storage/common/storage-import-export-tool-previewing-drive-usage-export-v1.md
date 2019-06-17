---
title: Wyświetlanie podglądu użycia dysków przez zadanie eksportu Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić podgląd listy obiektów blob, wybranej przez zadanie eksportu w usłudze Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 53ab1e28c5864b403d52bf5e73f0c5c41b8f18a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478457"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Wyświetlanie podglądu użycia dysków przez zadanie eksportu
Przed przystąpieniem do tworzenia zadania eksportu, musisz wybrać zestaw obiektów blob do wyeksportowania. Usługa Microsoft Azure Import/Export umożliwia za pomocą listy ścieżek, obiektów blob lub obiektu blob prefiksów do reprezentowania obiektów blob, które wybrałeś.  
  
Następnie należy określić, jak wiele dysków, musisz wysłać. Narzędzie importu/eksportu umożliwia `PreviewExport` polecenia do podglądu użycia dysków dla obiektów blob, został wybrany, na podstawie rozmiaru dysków będą korzystać.

## <a name="command-line-parameters"></a>Parametry wiersza polecenia

Można użyć poniższych parametrów, korzystając z `PreviewExport` polecenia narzędzia Import/Export.

|Parametr wiersza polecenia|Opis|  
|--------------------------|-----------------|  
|**/ logdir:** < LogDirectory\>|Opcjonalny. Katalog dziennika. Plików pełnego dziennika będą zapisywane do tego katalogu. Jeśli katalog dziennika nie jest określony, bieżący katalog będzie służyć jako katalog dziennika.|  
|**/SN:** < StorageAccountName\>|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
|**/sk:** <StorageAccountKey\>|Wymagane tylko wtedy, gdy nie określono sygnatury dostępu Współdzielonego kontenera. Klucz konta dla konta magazynu dla zadania eksportu.|  
|**/csas:** < ContainerSas\>|Wymagane tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatury dostępu Współdzielonego kontenera do wyświetlania listy obiektów blob, które mają zostać wyeksportowane przez zadanie eksportu.|  
|**/ExportBlobListFile:** <ExportBlobListFile\>|Wymagany. Ścieżka do pliku XML plik zawierający listę ścieżek obiektów blob lub obiektu blob prefiksy ścieżki dla obiektów blob do wyeksportowania. Format pliku używany w `BlobListBlobPath` element [umieścić zadania](/rest/api/storageimportexport/jobs) operacji interfejs API REST usługi Import/Export.|  
|**/DriveSize:** <DriveSize\>|Wymagany. Rozmiar dysków do użycia przez zadanie eksportu *np.* , 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Przykład wiersza polecenia

W poniższym przykładzie pokazano `PreviewExport` polecenia:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Pliku listy obiektów blob eksportu może zawierać nazwy obiektów blob i obiektów blob prefiksy, jak pokazano poniżej:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Narzędzie importu/eksportu platformy Azure zawiera listę wszystkich obiektów blob do wyeksportowania oblicza jak umieszczenie ich na dyski o określonym rozmiarze, biorąc pod uwagę wszystkie niezbędne czynności, a następnie Szacuje liczbę dysków wymaganych do przechowywania obiektów blob oraz informacje o użyciu dysku.  
  
Oto przykład danych wyjściowych za pomocą dzienników informacyjny pominięte:  
  
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
  
## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja usługi Azure narzędzie importu/eksportu](../storage-import-export-tool-how-to-v1.md)
