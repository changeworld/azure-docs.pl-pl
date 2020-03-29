---
title: Naprawianie zadania eksportu importu/eksportu platformy Azure — wersja 1 | Dokumenty firmy Microsoft
description: Dowiedz się, jak naprawić zadanie eksportu, które zostało utworzone i uruchomione przy użyciu usługi Azure Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b2ba30bddfc6364c79e1bb01d30cde63b261a07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978019"
---
# <a name="repairing-an-export-job"></a>Naprawianie zadania eksportu
Po zakończeniu zadania eksportu można uruchomić narzędzie importu/eksportu platformy Microsoft Azure lokalnie, aby:  
  
1.  Pobierz wszystkie pliki, których nie można wyeksportować w usłudze Azure Import/Export.  
  
2.  Sprawdź, czy pliki na dysku zostały poprawnie wyeksportowane.  
  
Aby korzystać z tej funkcji, musisz mieć łączność z usługą Azure Storage.  
  
Poleceniem naprawy zadania importu jest **RepairExport**.

## <a name="repairexport-parameters"></a>Naprawawydaj parametry portu

Za **pomocą narzędzia RepairExport**można określić następujące parametry:  
  
|Parametr|Opis|  
|---------------|-----------------|  
|**/r:<Plik naprawy\>**|Wymagany. Ścieżka do pliku naprawy, który śledzi postęp naprawy i pozwala na wznowienie przerwanej naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po uruchomieniu naprawy dla danego dysku, przejdziesz w ścieżce do pliku naprawy, który jeszcze nie istnieje. Aby wznowić przerwaną naprawę, należy przekazać nazwę istniejącego pliku naprawy. Należy zawsze określić plik naprawy odpowiadający dyskowi docelowemu.|  
|**/logdir:<LogDirectory\>**|Element opcjonalny. Katalog dziennika. Pełne pliki dziennika zostaną zapisane w tym katalogu. Jeśli nie określono katalogu dziennika, bieżący katalog będzie używany jako katalog dziennika.|  
|**/d:<TargetDirectory\>**|Wymagany. Katalog do sprawdzania poprawności i naprawy. Zazwyczaj jest to katalog główny dysku eksportującego, ale może być również udziałem plików sieciowych zawierającym kopię eksportowanych plików.|  
|**/bk:<Klawisz funkcji BitLocker\>**|Element opcjonalny. Należy określić klawisz BitLocker, jeśli narzędzie ma odblokować zaszyfrowane miejsce przechowywania eksportowanych plików.|  
|**/sn:<Nazwa konta magazynu\>**|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
|**/sk:<StorageAccountKey\>**|**Wymagane** wtedy i tylko wtedy, gdy nie określono sygnatury dostępu Współdzielonego kontenera. Klucz konta magazynu dla zadania eksportu.|  
|**/csas:<ContainerSas\>**|**Wymagane** wtedy i tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatura dostępu do kontenera do uzyskiwania dostępu do obiektów blob skojarzonych z zadaniem eksportu.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Wymagany. Ścieżka do pliku dziennika kopiowania dysku. Plik jest generowany przez usługę importu/eksportu systemu Windows Azure i można go pobrać z magazynu obiektów blob skojarzonego z zadaniem. Plik dziennika kopiowania zawiera informacje o nieudanych obiektach blob lub plikach, które mają zostać naprawione.|  
|**/ManifestFile:<DriveManifestFile\>**|Element opcjonalny. Ścieżka do pliku manifestu dysku eksportu. Ten plik jest generowany przez usługę importu/eksportu systemu Windows Azure i przechowywany na dysku eksportu, a opcjonalnie w obiekcie blob na koncie magazynu skojarzonym z zadaniem.<br /><br /> Zawartość plików na dysku eksportowym zostanie zweryfikowana za pomocą skrótów MD5 zawartych w tym pliku. Wszystkie pliki, które są uznane za uszkodzone zostaną pobrane i przepisane do katalogów docelowych.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Używanie trybu RepairExport do korygowania nieudanego eksportu  
Narzędzia Importu/Eksportuj platformy Azure można użyć do pobrania plików, których nie udało się wyeksportować. Plik dziennika kopiowania będzie zawierał listę plików, których nie udało się wyeksportować.  
  
Przyczyny niepowodzeń eksportowych obejmują następujące możliwości:  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu zmieniony podczas procesu transferu  
  
Aby uruchomić narzędzie w trybie **RepairExport,** należy najpierw podłączyć dysk zawierający wyeksportowane pliki do komputera. Następnie uruchom narzędzie Azure Import/Export Tool, określając ścieżkę `/d` do tego dysku z parametrem. Należy również określić ścieżkę do pobranego pliku dziennika kopii dysku. Poniższy przykład wiersza polecenia uruchamia narzędzie do naprawy plików, których nie udało się wyeksportować:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Poniżej przedstawiono przykład pliku dziennika kopiowania, który pokazuje, że jeden blok w obiekcie blob nie można wyeksportować:  
  
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
  
Plik dziennika kopiowania wskazuje, że wystąpił błąd podczas pobierania przez usługę Import/Eksportuj systemu Windows Azure do pliku na dysku eksportującym. Pozostałe składniki pliku zostały pobrane pomyślnie, a długość pliku została poprawnie ustawiona. W takim przypadku narzędzie otworzy plik na dysku, pobierze blok z konta magazynu i zapisze go w zakresie plików, zaczynając od przesunięcia 65536 o długości 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Używanie narzędzia RepairExport do sprawdzania poprawności zawartości dysku  
Można również użyć narzędzia Azure Import/Export z **RepairExport** opcji, aby sprawdzić poprawność zawartości na dysku są poprawne. Plik manifestu na każdym dysku eksportowym zawiera md5 dla zawartości dysku.  
  
Usługa Azure Import/Export można również zapisać pliki manifestu na koncie magazynu podczas procesu eksportowania. Lokalizacja plików manifestu jest dostępna za pośrednictwem operacji [Pobierz zadanie](/rest/api/storageimportexport/jobs) po zakończeniu zadania. Aby uzyskać więcej informacji na temat formatu pliku manifestu dysku, zobacz [Importowanie/Eksportowanie usługi Manifest Formatu.](storage-import-export-file-format-metadata-and-properties.md)  
  
W poniższym przykładzie pokazano, jak uruchomić narzędzie Importowanie/Eksportowanie platformy Azure przy za pomocą parametrów **/ManifestFile** i **/CopyLogFile:**  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Poniżej przedstawiono przykład pliku manifestu:  
  
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
  
Po zakończeniu procesu naprawy narzędzie odczyta każdy plik, do którego odwołuje się plik manifestu i weryfikuje integralność pliku za pomocą skrótów MD5. Dla manifestu powyżej przejdzie przez następujące składniki.  

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

Każdy składnik, który nie przejdzie weryfikacji, zostanie pobrany przez narzędzie i przepisany do tego samego pliku na dysku.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Konfigurowanie narzędzia Importu/Eksportu platformy Azure](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
