---
title: Naprawianie zadania importowania/eksportowania platformy Azure — wersja 1 | Dokumenty firmy Microsoft
description: Dowiedz się, jak naprawić zadanie importu, które zostało utworzone i uruchomione przy użyciu usługi Azure Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973887"
---
# <a name="repairing-an-import-job"></a>Naprawianie zadania importu
Usługa importu/eksportu platformy Microsoft Azure może nie zostać skopiowana do usługi obiektów Blob systemu Windows Azure. Oto niektóre przyczyny awarii:  
  
-   Uszkodzone pliki  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu został zmieniony podczas przenoszenia pliku.  
  
Narzędzie importu/eksportu platformy Microsoft Azure można uruchomić za pomocą plików dziennika kopiowania zadania importu, a narzędzie przekaże brakujące pliki (lub części pliku) na konto magazynu systemu Windows Azure, aby zakończyć zadanie importowania.  
  
## <a name="repairimport-parameters"></a>Parametry narzędzia RepairImport

Za pomocą **narzędzia RepairImport**można określić następujące parametry: 
  
|||  
|-|-|  
|**/r:**<Plik naprawy\>|**Wymagane.** Ścieżka do pliku naprawy, który śledzi postęp naprawy i pozwala na wznowienie przerwanej naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po uruchomieniu naprawy dla danego dysku, przekazać w ścieżce do pliku naprawy, który jeszcze nie istnieje. Aby wznowić przerwaną naprawę, należy przekazać nazwę istniejącego pliku naprawy. Należy zawsze określić plik naprawy odpowiadający dyskowi docelowemu.|  
|**/logdir:**<LogDirectory\>|**Opcjonalne.** Katalog dziennika. Pełne pliki dziennika są zapisywane w tym katalogu. Jeśli nie określono katalogu dziennika, bieżący katalog jest używany jako katalog dziennika.|  
|**/d:**<dyrektorów docelowych\>|**Wymagane.** Co najmniej jeden katalog rozdzielony średnikami zawierający oryginalne pliki, które zostały zaimportowane. Dysk importu może być również używany, ale nie jest wymagany, jeśli dostępne są alternatywne lokalizacje oryginalnych plików.|  
|**/bk:**<Klawiszu funkcji BitLocker\>|**Opcjonalne.** Należy określić klawisz BitLocker, jeśli narzędzie ma odblokować zaszyfrowany dysk, na którym są dostępne oryginalne pliki.|  
|**/sn:**<StorageAccountName\>|**Wymagane.** Nazwa konta magazynu dla zadania importu.|  
|**/sk:**<StorageAccountKey\>|**Wymagane** wtedy i tylko wtedy, gdy nie określono sygnatury dostępu Współdzielonego kontenera. Klucz konta magazynu dla zadania importu.|  
|**/csas:**<ContainerSas\>|**Wymagane** wtedy i tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatura dostępu do kontenera do uzyskiwania dostępu do obiektów blob skojarzonych z zadaniem importu.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Wymagane.** Ścieżka do pliku dziennika kopiowania dysku (pełny dziennik lub dziennik błędów). Plik jest generowany przez usługę importu/eksportu systemu Windows Azure i można go pobrać z magazynu obiektów blob skojarzonego z zadaniem. Plik dziennika kopiowania zawiera informacje o nieudanych obiektach blob lub plikach, które mają zostać naprawione.|  
|**/PathMapFile:**<Pliku Mapy DrivePath\>|**Opcjonalne.** Ścieżka do pliku tekstowego, który może służyć do rozwiązywania niejasności, jeśli masz wiele plików o tej samej nazwie, które zostały zaimportowania w tym samym zadaniu. Przy pierwszym uruchomieniu narzędzia można wypełnić ten plik wszystkimi niejednoznacznymi nazwami. Kolejne przebiegi narzędzia używają tego pliku, aby rozwiązać niejednoznaczności.|  
  
## <a name="using-the-repairimport-command"></a>Korzystanie z polecenia RepairImport  
Aby naprawić importowane dane, przesyłając strumieniowo dane za pośrednictwem sieci, należy określić `/d` katalogi zawierające oryginalne pliki importowane przy użyciu parametru. Należy również określić plik dziennika kopiowania pobrany z konta magazynu. Typowy wiersz polecenia do naprawy zadania importu z częściowymi błędami wygląda następująco:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
W poniższym przykładzie pliku dziennika kopiowania jeden fragment pliku 64-K został uszkodzony na dysku, który został wysłany do zadania importu. Ponieważ jest to jedyny błąd wskazany, pozostałe obiekty blob w zadaniu zostały pomyślnie zaimportowane.  
  
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
  
Gdy ten dziennik kopiowania jest przekazywany do narzędzia Azure Import/Export Tool, narzędzie próbuje zakończyć importowanie dla tego pliku, kopiując brakującą zawartość w sieci. Zgodnie z powyższym przykładem narzędzie `\animals\koala.jpg` wyszukuje `C:\Users\bob\Pictures` oryginalny `X:\BobBackup\photos`plik w dwóch katalogach i . Jeśli plik `C:\Users\bob\Pictures\animals\koala.jpg` istnieje, narzędzie Importu/Eksportu platformy Azure kopiuje brakujący `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`zakres danych do odpowiedniego obiektu blob .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Rozwiązywanie konfliktów podczas korzystania z narzędzia RepairImport  
W niektórych sytuacjach narzędzie może nie być w stanie znaleźć lub otworzyć niezbędnego pliku z jednego z następujących powodów: nie można odnaleźć pliku, plik jest niedostępny, nazwa pliku jest niejednoznaczna lub zawartość pliku nie jest już poprawna.  
  
Niejednoznaczny błąd może wystąpić, jeśli narzędzie próbuje `\animals\koala.jpg` zlokalizować i istnieje plik `C:\Users\bob\pictures` o `X:\BobBackup\photos`tej nazwie pod obiema i . Oznacza to, `C:\Users\bob\pictures\animals\koala.jpg` `X:\BobBackup\photos\animals\koala.jpg` że oba i istnieją na dyskach zadań importu.  
  
Opcja `/PathMapFile` umożliwia rozwiązanie tych błędów. Można określić nazwę pliku, który zawiera listę plików, których narzędzie nie było w stanie poprawnie zidentyfikować. W poniższym przykładzie wiersza polecenia jest wypełniany: `9WM35C2V_pathmap.txt`  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Narzędzie zapisze problematyczne ścieżki plików `9WM35C2V_pathmap.txt`do , po jednej w każdym wierszu. Na przykład plik może zawierać następujące wpisy po uruchomieniu polecenia:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Dla każdego pliku na liście należy spróbować zlokalizować i otworzyć plik, aby upewnić się, że jest on dostępny dla narzędzia. Jeśli chcesz wyraźnie poinformować narzędzie, gdzie można znaleźć plik mapy ścieżki, możesz zmodyfikować plik mapy ścieżki i dodać ścieżkę do każdego pliku w tym samym wierszu, oddzieloną znakiem karty:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po udostępnieniu niezbędnych plików narzędziu lub zaktualizowaniu pliku mapy ścieżki można ponownie uruchomić narzędzie, aby zakończyć proces importowania.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Konfigurowanie narzędzia Importu/Eksportu platformy Azure](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
