---
title: Naprawianie zadania eksportu Import/Eksport Azure - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak naprawić zadanie eksportu, który został utworzony i uruchamiany za pomocą usługi Import/Eksport Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873877"
---
# <a name="repairing-an-export-job"></a>Naprawianie zadania eksportu
Po zakończeniu zadania eksportu, możesz uruchomić narzędzie importu/eksportu pakietu Microsoft Azure lokalnej do:  
  
1.  Pobierz wszystkie pliki, które usługi Import/Eksport Azure nie mógł wyeksportować.  
  
2.  Sprawdź, czy zostały poprawnie wyeksportowane pliki na dysku.  
  
Musisz mieć połączenie z magazynem Azure, aby używać tej funkcji.  
  
Polecenie naprawy zadania importu jest **RepairExport**.

## <a name="repairexport-parameters"></a>Parametry RepairExport

Można określić następujące parametry z **RepairExport**:  
  
|Parametr|Opis|  
|---------------|-----------------|  
|**/ r: < RepairFile\>**|Wymagany. Ścieżka do pliku naprawy, który śledzi postęp naprawy i umożliwia wznowienie naprawę przerwania. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po uruchomieniu naprawy dla danego dysku zostaną spełnione w ścieżce do pliku naprawy, które jeszcze nie istnieje. Aby wznowić naprawę przerwania, należy przekazać nazwę istniejącego pliku naprawy. Zawsze należy określać plik naprawy odpowiadający dysk docelowy.|  
|**/ logdir: < LogDirectory\>**|Opcjonalny. Katalog dziennika. Plików pełnego dziennika zostanie zapisany do tego katalogu. Jeśli katalog dziennika nie jest określony, bieżący katalog będzie używany jako katalog dziennika.|  
|**/ d: < TargetDirectory\>**|Wymagany. Katalog do sprawdzania poprawności i napraw. Zazwyczaj jest to katalog główny dysku eksportu, ale może też być sieciowego udziału plików zawierający kopię wyeksportowane pliki.|  
|**/BK: < BitLockerKey\>**|Opcjonalny. Należy określić klucza funkcji BitLocker, jeśli chcesz użyć narzędzia, aby odblokować zaszyfrowany, gdzie znajdują się wyeksportowane pliki.|  
|**/SN: < StorageAccountName\>**|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
|**/SK: < StorageAccountKey\>**|**Wymagane** tylko wtedy, gdy nie określono kontenera sygnatury dostępu Współdzielonego. Klucz konta dla konta magazynu dla zadania eksportu.|  
|**/csas: < ContainerSas\>**|**Wymagane** tylko wtedy, gdy nie określono klucza konta magazynu. Kontener SAS do uzyskiwania dostępu do obiektów blob skojarzony z zadaniem eksportu.|  
|**/ CopyLogFile: < DriveCopyLogFile\>**|Wymagany. Ścieżka do pliku dziennika kopii dysku. Plik jest generowany przez usługę Windows Azure importu/eksportu i można pobrać z magazynu obiektów blob, skojarzone z zadaniem. Kopiuj plik dziennika zawiera informacje dotyczące obiektów blob nie powiodło się lub pliki, które mają zostać naprawione.|  
|**/ ManifestFile: < DriveManifestFile\>**|Opcjonalny. Ścieżka do pliku manifestu dysku eksportu. Ten plik jest generowany przez usługę Windows Azure importu/eksportu i przechowywane na dysku eksportu, a opcjonalnie w obiekcie blob na koncie magazynu skojarzone z zadaniem.<br /><br /> Zawartość plików na dysku eksportu zostaną zweryfikowane wartości skrótu MD5 zawarte w tym pliku. Wszystkie pliki, które są określane uszkodzone zostanie pobrany i ulegną do katalogów docelowych.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Aby rozwiązać eksportowanie nie powiodło się w trybie RepairExport  
Narzędzie importu/eksportu Azure służy do pobierania plików, których nie można wyeksportować. Kopiuj plik dziennika będzie zawierać listę plików, których nie można wyeksportować.  
  
Przyczyny niepowodzeń eksportu obejmują następujące możliwości:  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu została zmieniona w trakcie procesu transferu  
  
Aby uruchomić narzędzie **RepairExport** trybie, należy najpierw połączyć dysk zawierający wyeksportowane pliki na komputerze. Następnie uruchom narzędzie importu/eksportu Azure, podając ścieżkę do tego dysku z `/d` parametru. Należy również określić ścieżkę do pliku dziennika kopii dysku pobranego. W poniższym przykładzie wiersza polecenia, poniżej uruchamiane narzędzie, aby naprawić wszystkie pliki, które nie można wyeksportować:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Poniżej znajduje się przykład kopii pliku dziennika, który zawiera ten jeden blok w obiekcie blob nie można wyeksportować:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Kopiuj plik dziennika wskazuje, że wystąpił błąd podczas usługi Import/Eksport systemu Windows Azure została pobierania jedną bloków blob do pliku na dysku eksportu. Inne składniki pliku pobrane pomyślnie, a długość pliku została poprawnie ustawiona. W takim przypadku narzędzie będzie Otwórz plik na dysku, Pobierz bloku z konta magazynu i zapisz je w zakresie pliku, zaczynając od przesunięcia 65536 o długości 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Aby sprawdzić poprawność zawartości dysku przy użyciu RepairExport  
Można również użyć Import/Eksport Azure z **RepairExport** opcję, aby sprawdzić poprawność zawartości na dysku są poprawne. Plik manifestu na każdym dysku eksportu zawiera MD5s zawartość dysku.  
  
Usługa Import/Eksport Azure można także zapisać pliki manifestu do konta magazynu podczas procesu eksportowania. Lokalizacja pliku manifestu jest dostępna za pośrednictwem [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji po ukończeniu zadania. Zobacz [usługi Import/Eksport Format pliku manifestu](storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji o formacie pliku manifestu dysku.  
  
Poniższy przykład pokazuje, jak uruchomić narzędzie importu/eksportu Azure z **/ManifestFile** i **/CopyLogFile** parametry:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Poniżej przedstawiono przykładowy plik manifestu:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Po zakończeniu procesu naprawy, narzędzie zapoznaj się z artykułem każdego pliku, do którego odwołuje się plik manifestu i zweryfikować integralność plików z wartości skrótu MD5. Dla manifest powyżej przechodzą przez następujące składniki.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Dowolny składnik Niepowodzenie weryfikacji zostanie pobrana przez narzędzie i ponownie zapisać do tego samego pliku na dysku.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Trwa konfigurowanie narzędzia Azure Import/Eksport](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
