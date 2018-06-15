---
title: Naprawianie zadania importu Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak naprawić zadania importu, który został utworzony i uruchamiany za pomocą usługi Import/Eksport Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c837713fd9e7d03287ae5a3644fd6bb47714c9d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927467"
---
# <a name="repairing-an-import-job"></a>Naprawianie zadania importu
Usługa Import/Eksport Microsoft Azure może nie można skopiować niektórych plików lub części pliku do usługi Windows Azure Blob. Niektóre błędy przyczyny:  
  
-   Uszkodzone pliki  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu, zmieniona podczas przesyłania pliku.  
  
Można uruchomić narzędzie Import/Eksport Microsoft Azure z importowanie plików dziennika zadanie kopiowania, a narzędzie przekazuje brakujących plików (lub części pliku) do konta magazynu systemu Windows Azure, aby zakończyć to zadanie importu.  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Można określić następujące parametry z **RepairImport**: 
  
|||  
|-|-|  
|**/ r:**< RepairFile\>|**Wymagane.** Ścieżka do pliku naprawy, który śledzi postęp naprawy i umożliwia wznowienie naprawę przerwania. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po uruchomieniu naprawy dla danego dysku przekazać w ścieżce do pliku naprawy, które jeszcze nie istnieje. Aby wznowić naprawę przerwania, należy przekazać nazwę istniejącego pliku naprawy. Zawsze należy określać plik naprawy odpowiadający dysk docelowy.|  
|**/ logdir:**< LogDirectory\>|**Opcjonalne.** Katalog dziennika. Plików pełnego dziennika są zapisywane do tego katalogu. Jeśli katalog dziennika nie jest określony, bieżący katalog jest używany jako katalog dziennika.|  
|**/ d:**< TargetDirectories\>|**Wymagane.** Jeden lub więcej oddzielonych średnikami katalogów zawierające oryginalnych plików, które zostały zaimportowane. Importowanie dysku mogą być również używane, ale nie jest wymagane, jeśli alternatywnych lokalizacji oryginalnych plików są dostępne.|  
|**/BK:**< BitLockerKey\>|**Opcjonalne.** Należy określić klucza funkcji BitLocker, jeśli chcesz użyć narzędzia do odblokowywania zaszyfrowanego dysku, której oryginalnych plików są dostępne.|  
|**/SN:**< StorageAccountName\>|**Wymagane.** Nazwa konta magazynu dla zadania importu.|  
|**/SK:**< StorageAccountKey\>|**Wymagane** tylko wtedy, gdy nie określono kontenera sygnatury dostępu Współdzielonego. Klucz konta dla konta magazynu dla zadania importu.|  
|**/csas:**< ContainerSas\>|**Wymagane** tylko wtedy, gdy nie określono klucza konta magazynu. Kontener SAS do uzyskiwania dostępu do obiektów blob skojarzony z zadaniem importu.|  
|**/ CopyLogFile:**< DriveCopyLogFile\>|**Wymagane.** Ścieżka do pliku dziennika kopii dysku (albo pełnego dziennika lub błędu dziennika). Plik jest generowany przez usługę Windows Azure importu/eksportu i można pobrać z magazynu obiektów blob, skojarzone z zadaniem. Kopiuj plik dziennika zawiera informacje dotyczące obiektów blob nie powiodło się lub pliki, które mają zostać naprawione.|  
|**/ PathMapFile:**< DrivePathMapFile\>|**Opcjonalne.** Ścieżka do pliku tekstowego, który może służyć do rozpoznawania niejednoznaczności, jeśli masz wiele plików o takiej samej nazwie, które zostały importowania w tym samym zadaniu. Uruchomienie narzędzia po raz pierwszy go wypełnić ten plik ze wszystkimi niejednoznacznych nazw. Kolejnych uruchomieniach narzędzia użyć tego pliku w celu rozwiązania niejednoznaczności.|  
  
## <a name="using-the-repairimport-command"></a>Za pomocą polecenia RepairImport  
Napraw importowanie danych przez strumienia danych za pośrednictwem sieci, należy określić katalogi, które zawierają oryginalnych plików zostały importowanie przy użyciu `/d` parametru. Należy również określić kopii pliku dziennika, który został pobrany z konta magazynu. Typowe wiersza polecenia, aby naprawić zadania importu z częściowa błędami wygląda następująco:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
W poniższym przykładzie plik dziennika kopii 64 KB część plik został uszkodzony na dysku, która została dostarczona dla zadania importu. Ponieważ jest to tylko niepowodzenia wskazane, pozostałe obiekty BLOB w ramach zadania zostały pomyślnie zaimportowane.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Gdy ten dziennik kopiowania jest przekazywany do narzędzia importu/eksportu Azure, próbuje zakończenia importowania tego pliku przez skopiowanie Brak zawartości w sieci. Po powyższym przykładzie narzędzie szuka oryginalnego pliku `\animals\koala.jpg` w dwóch katalogów `C:\Users\bob\Pictures` i `X:\BobBackup\photos`. Jeśli plik `C:\Users\bob\Pictures\animals\koala.jpg` istnieje, narzędzie importu/eksportu Azure kopiuje Brak zakres danych do odpowiedniego obiektu blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Rozwiązywanie konfliktów, korzystając z RepairImport  
W niektórych sytuacjach narzędzia nie można znaleźć lub otworzyć pliku niezbędne dla jednego z następujących powodów: nie można odnaleźć pliku, plik nie jest dostępny, nazwa pliku jest niejednoznaczna lub zawartość pliku nie jest już prawidłowe.  
  
Niejednoznaczny błąd mógł wystąpić, jeśli narzędzie próbuje zlokalizować `\animals\koala.jpg` i istnieje plik o tej samej nazwie w obu `C:\Users\bob\pictures` i `X:\BobBackup\photos`. Oznacza to, że oba `C:\Users\bob\pictures\animals\koala.jpg` i `X:\BobBackup\photos\animals\koala.jpg` istnieje na dyskach zadania importu.  
  
`/PathMapFile` Opcja umożliwia Rozwiąż te problemy. Można określić nazwę pliku, który zawiera listę plików, które narzędzie nie może poprawnie zidentyfikować. W poniższym przykładzie wiersza polecenia `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Narzędzie zapisuje ścieżki pliku problemy `9WM35C2V_pathmap.txt`, jeden w każdym wierszu. Na przykład plik może zawierać następujące wpisy po uruchomieniu polecenia:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Dla każdego pliku na liście należy podjąć próbę zlokalizuj i Otwórz plik, aby upewnić się, że jest ona dostępna do narzędzia. Jeśli chcesz sprawdzić narzędzie jawnie, gdzie można znaleźć pliku, możesz zmodyfikować ścieżki pliku mapy i Dodaj ścieżkę do każdego pliku, w tym samym wierszu rozdzielone znak tabulacji:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po udostępniając pliki niezbędne do narzędzia lub ścieżki pliku mapy aktualizacji, należy ponownie uruchomić narzędzie, aby ukończyć proces importowania.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Trwa konfigurowanie narzędzia Azure Import/Eksport](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
