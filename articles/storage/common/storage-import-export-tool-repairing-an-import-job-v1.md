---
title: Naprawianie zadania importu platformy Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak naprawić zadania importu, które zostały utworzone, a następnie uruchomić przy użyciu usługi Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fda1d3d626c91ba984f08b96c79ab6a2fd2ec74b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477590"
---
# <a name="repairing-an-import-job"></a>Naprawianie zadania importu
Usługa Microsoft Azure Import/Export może zakończyć się niepowodzeniem do skopiowania niektórych części pliku lub plików w usłudze Windows Azure Blob. Niektóre przyczyny błędów obejmują:  
  
-   Uszkodzone pliki  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu, zmieniona podczas przesyłania pliku.  
  
Można uruchomić narzędzie importu/eksportu platformy Microsoft Azure z importowanie plików dziennika kopiowania zadania, a narzędzie przekazanie brakujących plików (lub części pliku) do konta usługi Windows Azure storage do ukończenia zadania importu.  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Można określić następujące parametry przy użyciu **RepairImport**: 
  
|||  
|-|-|  
|**/r:** <RepairFile\>|**Wymagane.** Ścieżka do pliku naprawy, śledzi postęp naprawy, która pozwala na wznowienie przerwane naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po rozpoczęciu naprawy dla danego dysku są przekazywane w ścieżce do pliku naprawy, który jeszcze nie istnieje. Aby wznowić przerwane naprawy, należy przekazać nazwę istniejącego pliku naprawy. Zawsze należy określić plik naprawy odpowiadający dysk docelowy.|  
|**/ logdir:** < LogDirectory\>|**Opcjonalnie.** Katalog dziennika. Plików pełnego dziennika są zapisywane do tego katalogu. Jeśli katalog dziennika nie jest określony, bieżący katalog jest używany jako katalog dziennika.|  
|**/ d:** < TargetDirectories\>|**Wymagane.** Co najmniej jeden rozdzielonych średnikami katalogi, które zawierają oryginalnych plików, które zostały zaimportowane. Importowanie dysku mogą być również używane, ale nie jest wymagane, jeśli alternatywnych lokalizacji oryginalnych plików są dostępne.|  
|**/bk:** <BitLockerKey\>|**Opcjonalnie.** Należy określić klucz funkcji BitLocker, jeśli chcesz, aby narzędzie w celu odblokowania zaszyfrowanego dysku, której oryginalnych plików są dostępne.|  
|**/SN:** < StorageAccountName\>|**Wymagane.** Nazwa konta magazynu dla zadania importu.|  
|**/sk:** <StorageAccountKey\>|**Wymagane** tylko wtedy, gdy nie określono sygnatury dostępu Współdzielonego kontenera. Klucz konta dla konta magazynu dla zadania importu.|  
|**/csas:** < ContainerSas\>|**Wymagane** tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatury dostępu Współdzielonego kontenera do uzyskiwania dostępu do obiektów blob, skojarzone z zadaniem importowania.|  
|**/CopyLogFile:** <DriveCopyLogFile\>|**Wymagane.** Ścieżka do pliku dziennika kopiowania dysku (albo pełnego dziennika lub błąd dziennika). Plik jest generowany przez usługę Windows Azure Import/Export i można je pobrać z magazynu obiektów blob, skojarzone z zadaniem. Kopiuj plik dziennika zawiera informacje dotyczące obiektów blob nie powiodło się lub pliki, które mają zostać naprawiony.|  
|**/PathMapFile:** <DrivePathMapFile\>|**Opcjonalnie.** Ścieżka do pliku tekstowego, który może służyć do rozwiązywania niejednoznaczności, jeśli masz wiele plików o takiej samej nazwie, która importowania w ramach tego samego zadania. Przy pierwszym uruchomieniu narzędzie jest uruchamiane, można go wypełnić ten plik ze wszystkimi niejednoznacznych nazw. Kolejnych przebiegów narzędzia użyć tego pliku w ustalaniu niejednoznaczności.|  
  
## <a name="using-the-repairimport-command"></a>Za pomocą polecenia RepairImport  
Aby naprawić importu danych przez przesyłanie strumieniowe danych za pośrednictwem sieci, należy określić katalogi, które zawierają oryginalne pliki zostały importowanie, przy użyciu `/d` parametru. Należy także określić pliku dziennika kopiowania, który został pobrany z konta magazynu. Typowe wiersza polecenia do naprawy zadania importu z błędami częściowe wygląda następująco:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
W poniższym przykładzie plik dziennika kopiowania 64 KB część plik został uszkodzony na dysku, który został wysłany do zadania importu. Ponieważ jest to błąd tylko wskazane, pozostałe obiekty BLOB w ramach zadania zostały pomyślnie zaimportowane.  
  
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
  
Gdy ten dziennik kopiowania zostaną przekazane do narzędzia Azure Import/Export, narzędzie spróbuje zakończenia importowania tego pliku przez skopiowanie Brak zawartości za pośrednictwem sieci. W powyższym przykładzie narzędzie szuka oryginalnego pliku `\animals\koala.jpg` w dwa katalogi `C:\Users\bob\Pictures` i `X:\BobBackup\photos`. Jeśli plik `C:\Users\bob\Pictures\animals\koala.jpg` istnieje, narzędzie importu/eksportu platformy Azure kopiuje Brak zakres danych do odpowiedniego obiektu blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Rozwiązywanie konfliktów, korzystając z RepairImport  
W niektórych sytuacjach, narzędzie może nie móc znaleźć lub otworzyć pliku niezbędne dla jednego z następujących powodów: nie można odnaleźć pliku, plik nie jest dostępny, nazwa pliku jest niejednoznaczna lub zawartość pliku nie są już prawidłowe.  
  
Niejednoznaczny błąd mógł wystąpić, jeśli narzędzie próbuje zlokalizować `\animals\koala.jpg` i istnieje plik o tej nazwie w ramach obu `C:\Users\bob\pictures` i `X:\BobBackup\photos`. Oznacza to, że oba `C:\Users\bob\pictures\animals\koala.jpg` i `X:\BobBackup\photos\animals\koala.jpg` istnieje na dyskach zadania importu.  
  
`/PathMapFile` Pozwala wyeliminować te błędy. Można określić nazwę pliku, który zawiera listę plików, które narzędzie nie może poprawnie zidentyfikować. Poniższy przykład wiersza polecenia wypełni `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Narzędzie zapisuje ścieżki problematyczne plik do `9WM35C2V_pathmap.txt`, jeden w każdym wierszu. Na przykład plik może zawierać następujące wpisy po uruchomieniu polecenia:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Dla każdego pliku na liście należy podjąć próbę Znajdź i Otwórz plik, aby upewnić się, że jest ona dostępna do narzędzia. Jeśli chcesz przekazać do narzędzia informacje jawnie gdzie można znaleźć pliku można zmodyfikować ścieżki pliku mapy i dodać ścieżkę do każdego pliku, w tym samym wierszu, oddzielone znak tabulacji:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po udostępnia narzędzia niezbędne pliki lub aktualizowanie ścieżki pliku mapy, możesz ponownie uruchomić narzędzie aby zakończyć proces importowania.  
  
## <a name="next-steps"></a>Kolejne kroki
 
* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
