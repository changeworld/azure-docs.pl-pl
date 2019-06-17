---
title: Naprawianie zadania eksportu Azure Import/Export - v1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak naprawić przez zadanie eksportu, które zostały utworzone, a następnie uruchomić przy użyciu usługi Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 915cf1e66ec400e0d2461873d9fb3d66be9883fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477947"
---
# <a name="repairing-an-export-job"></a>Naprawianie zadania eksportu
Po zakończeniu zadania eksportu można uruchomić narzędzie importu/eksportu platformy Microsoft Azure w środowisku lokalnym do:  
  
1.  Pobierz wszystkie pliki, które usługa Azure Import/Export nie może wyeksportować.  
  
2.  Sprawdź, czy pliki na dysku zostały poprawnie wyeksportowane.  
  
Musi mieć łączność z usługi Azure Storage, aby używać tej funkcji.  
  
To polecenie dla naprawianie zadania importu **RepairExport**.

## <a name="repairexport-parameters"></a>Parametry RepairExport

Można określić następujące parametry przy użyciu **RepairExport**:  
  
|Parametr|Opis|  
|---------------|-----------------|  
|**r: < RepairFile\>**|Wymagany. Ścieżka do pliku naprawy, śledzi postęp naprawy, która pozwala na wznowienie przerwane naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Podczas uruchamiania naprawy dla danego dysku będzie są przekazywane w ścieżce do pliku naprawy, który jeszcze nie istnieje. Aby wznowić przerwane naprawy, należy przekazać nazwę istniejącego pliku naprawy. Zawsze należy określić plik naprawy odpowiadający dysk docelowy.|  
|**/ logdir: < LogDirectory\>**|Opcjonalny. Katalog dziennika. Plików pełnego dziennika będą zapisywane do tego katalogu. Jeśli katalog dziennika nie jest określony, bieżący katalog będzie służyć jako katalog dziennika.|  
|**/ d: < TargetDirectory\>**|Wymagany. Katalog do sprawdzania poprawności i naprawy. Zazwyczaj jest to katalog główny dysku eksportu, ale może także być w sieciowym udziale plików zawierający kopię wyeksportowane pliki.|  
|**/bk:<BitLockerKey\>**|Opcjonalny. Należy określić klucz funkcji BitLocker, jeśli chcesz, aby narzędzie, aby odblokować zaszyfrowany, gdzie są przechowywane wyeksportowane pliki.|  
|**/SN: < StorageAccountName\>**|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
|**/sk:<StorageAccountKey\>**|**Wymagane** tylko wtedy, gdy nie określono sygnatury dostępu Współdzielonego kontenera. Klucz konta dla konta magazynu dla zadania eksportu.|  
|**/csas: < ContainerSas\>**|**Wymagane** tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatury dostępu Współdzielonego kontenera do uzyskiwania dostępu do obiektów blob skojarzony z zadaniem eksportu.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Wymagany. Ścieżka do pliku dziennika kopiowania dysku. Plik jest generowany przez usługę Windows Azure Import/Export i można je pobrać z magazynu obiektów blob, skojarzone z zadaniem. Kopiuj plik dziennika zawiera informacje dotyczące obiektów blob nie powiodło się lub pliki, które mają zostać naprawiony.|  
|**/ManifestFile:<DriveManifestFile\>**|Opcjonalny. Ścieżka do pliku manifestu dysku eksportu. Ten plik jest generowane przez usługę Windows Azure Import/Export i przechowywane na dysku eksportu, a opcjonalnie w obiekcie blob na koncie magazynu skojarzone z zadaniem.<br /><br /> Zawartość plików na dysku eksportu zostaną zweryfikowane skróty MD5 zawarte w tym pliku. Wszystkie pliki, które są określane w uszkodzona zostanie pobrana i przepisane, aby katalog docelowy.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Przy użyciu trybu RepairExport, aby poprawić eksportowanie nie powiodło się  
Narzędzie importu/eksportu platformy Azure służy do pobierania plików, których nie można wyeksportować. Kopiuj plik dziennika będzie zawierać listę plików, których nie można wyeksportować.  
  
Przyczyny błędów eksportu obejmują następujące możliwości:  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu, zmieniona w trakcie transferu  
  
Aby uruchomić narzędzie **RepairExport** trybu, najpierw musisz nawiązać dysk zawierający wyeksportowany plik do komputera. Następnie uruchom narzędzie importu/eksportu platformy Azure, określając ścieżkę do tego dysku przy użyciu `/d` parametru. Należy również określić ścieżkę do pliku dziennika kopiowania stacji dysków, który został pobrany. W poniższym przykładzie następujące dla wiersza polecenia uruchamia narzędzie, aby naprawić wszystkie pliki, których nie można wyeksportować:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Poniżej znajduje się przykład kopii pliku dziennika, który pokazuje, że jeden blok w obiekcie blob nie można wyeksportować:  
  
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
  
Kopiuj plik dziennika wskazuje wystąpił błąd podczas usługi Windows Azure Import/Export był jeden z bloków blob pobierania do pliku na dysku eksportu. Inne składniki plik został pobrany pomyślnie, a długość pliku została prawidłowo ustawiona. W tym przypadku narzędzie będzie otwierać plik na dysku, Pobierz bloku z konta magazynu i zapisz je w zakresie pliku, zaczynając od przesunięcia 65536 o długości 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Aby sprawdzić zawartość dysku przy użyciu RepairExport  
Można również użyć usługi Azure Import/Export, za pomocą **RepairExport** opcję, aby sprawdzić zawartość na dysku są poprawne. Plik manifestu na każdym dysku eksportu zawiera MD5s zawartość dysku.  
  
Usługa Azure Import/Export można także zapisać pliki manifestu do konta magazynu, podczas procesu eksportowania. Lokalizacja plików manifestu jest dostępna za pośrednictwem [pobrania zadania](/rest/api/storageimportexport/jobs) operacji po ukończeniu zadania. Zobacz [usługi Import/Export Format pliku manifestu](storage-import-export-file-format-metadata-and-properties.md) Aby uzyskać więcej informacji o formacie pliku manifestu dysku.  
  
Poniższy przykład pokazuje, jak uruchomić narzędzie importu/eksportu platformy Azure za pomocą **/ManifestFile** i **/CopyLogFile** parametry:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Oto przykładowy plik manifestu:  
  
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
  
Po zakończeniu procesu naprawy narzędzie zapoznaj się z artykułem każdego pliku, do którego odwołuje się plik manifestu i zweryfikować integralności plików za pomocą wartości skrótu MD5. Manifest powyżej zaczną za pomocą następujących składników.  

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

Dowolny składnik Niepowodzenie weryfikacji, zostanie pobrana przez narzędzie i przepisany do tego samego pliku na dysku.  
  
## <a name="next-steps"></a>Kolejne kroki
 
* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
