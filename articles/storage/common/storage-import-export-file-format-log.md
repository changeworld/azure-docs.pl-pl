---
title: Format pliku dziennika w usłudze Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o formacie pliki dziennika utworzone podczas czynności są wykonywane przez zadanie usługi Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 00e226134039d29efd744290c4bc63abd50adc89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478610"
---
# <a name="azure-importexport-service-log-file-format"></a>Format pliku dziennika usługi Azure Import/Export
Gdy usługa Microsoft Azure Import/Export wykonuje akcję na dysku jako część zadania importu lub eksportu, dzienniki są zapisywane w blokowe obiekty BLOB na koncie magazynu skojarzonego z tym zadaniem.  
  
Istnieją dwa dzienniki, które mogą być zapisywane przez usługę Import/Export:  
  
-   Dziennik błędów programu był zawsze generowany w przypadku wystąpienia błędu.  
  
-   Pełny dziennik nie jest domyślnie włączona, ale może być włączona `EnableVerboseLog` właściwość [umieścić zadania](/rest/api/storageimportexport/jobs) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs) operacji.  
  
## <a name="log-file-location"></a>Lokalizacja pliku dziennika  
Dzienniki są zapisywane w blokowe obiekty BLOB w kontenerze lub określony przez katalog wirtualny `ImportExportStatesPath` ustawienie, które można ustawić na `Put Job` operacji. Lokalizacja, w dziennikach zależy od tego, jak uwierzytelniania jest określony dla zadania, wraz z wartość określona dla `ImportExportStatesPath`. Uwierzytelnianie dla zadania może być określona za pomocą klucza konta magazynu lub kontenera sygnatury dostępu Współdzielonego (sygnatura dostępu współdzielonego).  
  
Nazwa kontenera lub wirtualnego katalogu może być nazwą domyślną `waimportexport`, lub innego kontenera lub nazwą katalogu wirtualnego, który określisz.  
  
W poniższej tabeli przedstawiono możliwe opcje:  
  
|Metoda uwierzytelniania|Wartość atrybutu `ImportExportStatesPath`— Element|Lokalizacja dziennika obiektów blob|  
|---------------------------|----------------------------------------------|---------------------------|  
|Klucz konta magazynu|Wartość domyślna|Kontener o nazwie `waimportexport`, który jest domyślnym kontenerem. Na przykład:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Klucz konta magazynu|Wartość określona przez użytkownika|Kontener o nazwie określonej przez użytkownika. Na przykład:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Sygnatury dostępu Współdzielonego kontenera|Wartość domyślna|Katalog wirtualny nosi nazwę `waimportexport`, która jest nazwą domyślną poniżej określonego w sygnatury dostępu Współdzielonego kontenera.<br /><br /> Na przykład, jeśli sygnatury dostępu Współdzielonego określony dla zadania jest `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, wyniósłby lokalizacja dziennika `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Sygnatury dostępu Współdzielonego kontenera|Wartość określona przez użytkownika|Katalog wirtualny o nazwie określonej przez użytkownika, poniżej określonego w sygnatury dostępu Współdzielonego kontenera.<br /><br /> Na przykład, jeśli sygnatury dostępu Współdzielonego określony dla zadania jest `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, i określony katalog wirtualny nosi nazwę `mylogblobs`, wyniósłby lokalizacja dziennika `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Adres URL dla błędów i pełne dzienniki można pobrać przez wywołanie metody [pobrania zadania](/rest/api/storageimportexport/jobs) operacji. Dzienniki są dostępne po zakończeniu przetwarzania dysku.  
  
## <a name="log-file-format"></a>Format pliku dziennika  
Format dla obu dzienników jest taki sam: obiektów blob zawierający XML opisy zdarzeń, które wystąpiły podczas kopiowania obiektów blob między dysk twardy i konta klienta.  
  
Pełny dziennik zawiera pełne informacje na temat stanu operacji kopiowania dla każdego obiektu blob (w przypadku zadania importu) lub pliku (przez zadanie eksportu), w dzienniku błędów zawiera tylko informacje dotyczące obiektów blob lub pliki, które napotkały błędy podczas importowania lub zadanie eksportu.  
  
Format pełnego dziennika znajdują się poniżej. Dziennik błędów ma tę samą strukturę, ale odfiltrowuje operacje zakończone powodzeniem.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

W poniższej tabeli opisano elementy w pliku dziennika.  
  
|XML Element|Type|Opis|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML Element|Reprezentuje dziennika dysku.|  
|`Version`|Atrybut ciągu|Wersja format dziennika.|  
|`DriveId`|String|Numer seryjny sprzętu stacji dysków.|  
|`Status`|String|Stan przetwarzania dysku. Zobacz `Drive Status Codes` poniższą tabelę, aby uzyskać więcej informacji.|  
|`Blob`|Zagnieżdżone — element XML|Reprezentuje obiekt blob.|  
|`Blob/BlobPath`|String|Identyfikator URI obiektu blob.|  
|`Blob/FilePath`|String|Ścieżka względna do pliku na dysku.|  
|`Blob/Snapshot`|DateTime|Wersja migawki obiektu blob przez zadanie eksportu.|  
|`Blob/Length`|Liczba całkowita|Łączna długość obiektu blob w bajtach.|  
|`Blob/LastModified`|DateTime|Data/godzina ostatniej modyfikacji obiektu blob, przez zadanie eksportu.|  
|`Blob/ImportDisposition`|String|Dyspozycja import obiektu blob do zadania importu tylko.|  
|`Blob/ImportDisposition/@Status`|Atrybut ciągu|Stan dyspozycji importu.|  
|`PageRangeList`|Zagnieżdżone — element XML|Reprezentuje listę zakresów stron dla stronicowych obiektów blob.|  
|`PageRange`|— Element XML|Reprezentuje zakres stron.|  
|`PageRange/@Offset`|Atrybut, liczba całkowita|Początkowe przesunięcie zakres stron w obiekcie blob.|  
|`PageRange/@Length`|Atrybut, liczba całkowita|Długość w bajtach zakres stron.|  
|`PageRange/@Hash`|Atrybut ciągu|Zakodowane w formacie Base16 skrótu MD5 zakres stron.|  
|`PageRange/@Status`|Atrybut ciągu|Stan przetwarzania zakres stron.|  
|`BlockList`|Zagnieżdżone — element XML|Reprezentuje listę bloków dla blokowego obiektu blob.|  
|`Block`|— Element XML|Reprezentuje blok.|  
|`Block/@Offset`|Atrybut, liczba całkowita|Przesunięcia początkowego bloku w obiekcie blob.|  
|`Block/@Length`|Atrybut, liczba całkowita|Długość w bajtach bloku.|  
|`Block/@Id`|Atrybut ciągu|Identyfikator bloku.|  
|`Block/@Hash`|Atrybut ciągu|Zakodowane w formacie Base16 Skrót MD5 bloku.|  
|`Block/@Status`|Atrybut ciągu|Stan przetwarzania bloku.|  
|`Metadata`|Zagnieżdżone — element XML|Reprezentuje metadane obiektu blob.|  
|`Metadata/@Status`|Atrybut ciągu|Stan przetwarzania metadane obiektu blob.|  
|`Metadata/GlobalPath`|String|Ścieżka względna do pliku metadanych globalnego.|  
|`Metadata/GlobalPath/@Hash`|Atrybut ciągu|Zakodowane w formacie Base16 Skrót MD5 pliku metadanych globalnego.|  
|`Metadata/Path`|String|Ścieżka względna do pliku metadanych.|  
|`Metadata/Path/@Hash`|Atrybut ciągu|Zakodowane w formacie Base16 Skrót MD5 pliku metadanych.|  
|`Properties`|Zagnieżdżone — element XML|Reprezentuje właściwości obiektu blob.|  
|`Properties/@Status`|Atrybut ciągu|Stan przetwarzania właściwości obiektu blob, np. nie można odnaleźć pliku, zakończone.|  
|`Properties/GlobalPath`|String|Ścieżka względna do pliku właściwości globalne.|  
|`Properties/GlobalPath/@Hash`|Atrybut ciągu|Zakodowane w formacie Base16 Skrót MD5 pliku globalne właściwości.|  
|`Properties/Path`|String|Ścieżka względna do pliku właściwości.|  
|`Properties/Path/@Hash`|Atrybut ciągu|Zakodowane w formacie Base16 Skrót MD5 pliku właściwości.|  
|`Blob/Status`|String|Stan przetwarzania obiektu blob.|  
  
## <a name="drive-status-codes"></a>Kody stanu dysku  
Poniższa tabela zawiera listę kodów stanu przetwarzania na dysku.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Dysk zakończył przetwarzanie bez żadnych błędów.|  
|`CompletedWithWarnings`|Dysk zakończył przetwarzanie z ostrzeżeniami w jeden lub więcej obiektów blob na przepisy importu określona dla obiektów blob.|  
|`CompletedWithErrors`|Dysk zostało zakończone z błędami w jedną lub więcej obiektów blob lub fragmenty.|  
|`DiskNotFound`|Dysk nie znajduje się na dysku.|  
|`VolumeNotNtfs`|Pierwszy wolumin danych na dysku nie jest w formacie NTFS.|  
|`DiskOperationFailed`|Wystąpił nieznany błąd podczas wykonywania operacji na dysku.|  
|`BitLockerVolumeNotFound`|Brak encryptable woluminu funkcji BitLocker zostanie znaleziony.|  
|`BitLockerNotActivated`|Nie włączono funkcji BitLocker na woluminie.|  
|`BitLockerProtectorNotFound`|Na woluminie nie ma klucza funkcję ochrony hasło numeryczne.|  
|`BitLockerKeyInvalid`|Podane hasło numeryczne, nie można odblokować wolumin.|  
|`BitLockerUnlockVolumeFailed`|Nieznany błąd miało miejsce podczas próby odblokowania woluminu.|  
|`BitLockerFailed`|Wystąpił nieznany błąd podczas wykonywania operacji w funkcji BitLocker.|  
|`ManifestNameInvalid`|Nazwa pliku manifestu jest nieprawidłowy.|  
|`ManifestNameTooLong`|Nazwa pliku manifestu jest zbyt długa.|  
|`ManifestNotFound`|Nie znaleziono pliku manifestu.|  
|`ManifestAccessDenied`|Odmowa dostępu do pliku manifestu.|  
|`ManifestCorrupted`|Plik manifestu jest uszkodzony (zawartość jest niezgodna jego skrót).|  
|`ManifestFormatInvalid`|Zawartość manifestu nie jest zgodny z wymaganym formatem.|  
|`ManifestDriveIdMismatch`|Identyfikator dysku w pliku manifestu jest niezgodna z jednego odczytu z dysku.|  
|`ReadManifestFailed`|Wystąpił błąd We/Wy dysku podczas odczytu z manifestu.|  
|`BlobListFormatInvalid`|Wyświetlenie listy obiektów blob eksportu obiektów blob nie jest zgodny z wymaganym formatem.|  
|`BlobRequestForbidden`|Dostęp do obiektów blob na koncie magazynu jest zabronione. Może to być spowodowane klucza konta magazynu nieprawidłowe lub sygnatury dostępu Współdzielonego kontenera.|  
|`InternalError`|I wystąpił błąd wewnętrzny podczas przetwarzania na dysku.|  
  
## <a name="blob-status-codes"></a>Kody stanu obiektu blob  
Poniższa tabela zawiera listę kodów stanu do przetwarzania obiektu blob.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Obiekt blob zakończył przetwarzanie bez błędów.|  
|`CompletedWithErrors`|Obiekt blob zakończył przetwarzanie z błędami w jeden lub więcej zakresów stron lub bloków, metadanych lub właściwości.|  
|`FileNameInvalid`|Nazwa pliku jest nieprawidłowa.|  
|`FileNameTooLong`|Nazwa pliku jest zbyt długa.|  
|`FileNotFound`|Nie odnaleziono pliku.|  
|`FileAccessDenied`|Odmowa dostępu do pliku.|  
|`BlobRequestFailed`|Wystąpił błąd żądania usługi obiektów Blob do dostępu do obiektu blob.|  
|`BlobRequestForbidden`|Żądanie usługi obiektów Blob do dostępu do obiektu blob jest niedozwolone. Może to być spowodowane klucza konta magazynu nieprawidłowe lub sygnatury dostępu Współdzielonego kontenera.|  
|`RenameFailed`|Nie można zmienić nazwy obiektu blob (w przypadku zadania importu) lub pliku (w przypadku zadania eksportu).|  
|`BlobUnexpectedChange`|Nieoczekiwana zmiana wystąpił błąd związany z obiektów blob (w przypadku zadania eksportu).|  
|`LeasePresent`|Jest obecny w obiekcie blob dzierżawy.|  
|`IOFailed`|Wystąpił błąd operacji We/Wy dysku lub w sieci podczas przetwarzania obiektu blob.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania obiektu blob.|  
  
## <a name="import-disposition-status-codes"></a>Kody stanu dyspozycji importu  
Poniższa tabela zawiera listę kodów stanu rozpoznawania dyspozycji importu.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Created`|Obiekt blob został utworzony.|  
|`Renamed`|Obiekt blob została zmieniona na dyspozycji importu zmiany nazwy. `Blob/BlobPath` Element zawiera identyfikator URI, dla których nazwy zostały zmienione obiektów blob.|  
|`Skipped`|Obiekt blob został pominięty na `no-overwrite` zaimportować dyspozycji.|  
|`Overwritten`|Obiekt blob został zastąpiony istniejącego obiektu blob na `overwrite` zaimportować dyspozycji.|  
|`Cancelled`|Niepowodzenia wcześniejszego została zatrzymana, dalsze przetwarzanie dyspozycji importu.|  
  
## <a name="page-rangeblock-status-codes"></a>Kody stanu zakres/blok strony  
Poniższa tabela zawiera listę kodów stanu przetwarzania zakres stron lub blok.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Strona zakres lub blok zakończył przetwarzanie bez żadnych błędów.|  
|`Committed`|Blok został przekazany, ale nie w bloku pełnej listy ponieważ innych bloków nie powiodło się lub umieścić pełną zablokowanych, sama nie powiodło się.|  
|`Uncommitted`|Blok jest przekazany, ale nie zostały przyznane.|  
|`Corrupted`|Strona zakres lub blok jest uszkodzony (zawartość jest niezgodna jego skrót).|  
|`FileUnexpectedEnd`|Napotkano nieoczekiwany koniec pliku.|  
|`BlobUnexpectedEnd`|Napotkano nieoczekiwany koniec obiektu blob.|  
|`BlobRequestFailed`|Wystąpił błąd żądania usługi obiektów Blob, uzyskać dostęp do strony zakres lub blok.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania strony zakres lub blok.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania strony zakres lub blok.|  
|`Cancelled`|Niepowodzenia wcześniejszego została zatrzymana, dalsze przetwarzanie strony zakres lub blok.|  
  
## <a name="metadata-status-codes"></a>Kody stanu metadanych  
Poniższa tabela zawiera listę kodów stanu przetwarzania metadane obiektu blob.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Metadane zakończył przetwarzanie bez błędów.|  
|`FileNameInvalid`|Nazwa pliku metadanych jest nieprawidłowy.|  
|`FileNameTooLong`|Nazwa pliku metadanych jest za długa.|  
|`FileNotFound`|Nie odnaleziono pliku metadanych.|  
|`FileAccessDenied`|Odmowa dostępu do pliku metadanych.|  
|`Corrupted`|Plik metadanych jest uszkodzony (zawartość jest niezgodna jego skrót).|  
|`XmlReadFailed`|Zawartość metadanych jest niezgodny z wymaganym formatem.|  
|`XmlWriteFailed`|Zapisywanie metadanych XML nie powiodło się.|  
|`BlobRequestFailed`|Żądanie usługi obiektów Blob, dostęp do metadanych nie powiodło się.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania metadanych.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania metadanych.|  
|`Cancelled`|Niepowodzenia wcześniejszego została zatrzymana, dalsze przetwarzanie metadanych.|  
  
## <a name="properties-status-codes"></a>Kody stanu właściwości  
Poniższa tabela zawiera listę kodów stanu przetwarzania właściwości obiektu blob.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Właściwości Zakończono przetwarzanie bez żadnych błędów.|  
|`FileNameInvalid`|Nazwa pliku właściwości jest nieprawidłowa.|  
|`FileNameTooLong`|Właściwości nazwy pliku jest za długa.|  
|`FileNotFound`|Nie znaleziono pliku właściwości.|  
|`FileAccessDenied`|Odmowa dostępu do pliku właściwości.|  
|`Corrupted`|Plik właściwości jest uszkodzony (zawartość jest niezgodna jego skrót).|  
|`XmlReadFailed`|Zawartość właściwości nie jest zgodny z wymaganym formatem.|  
|`XmlWriteFailed`|Zapisywanie właściwości XML nie powiodło się.|  
|`BlobRequestFailed`|Wystąpił błąd żądania usługi obiektów Blob, aby uzyskać dostęp do właściwości.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania właściwości.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania właściwości.|  
|`Cancelled`|Niepowodzenia wcześniejszego została zatrzymana, dalsze przetwarzanie właściwości.|  
  
## <a name="sample-logs"></a>Przykładowe dzienniki  
Oto przykład pełnego dziennika.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Poniżej przedstawiono odpowiedni dziennik błędów.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Postępuj zgodnie z dziennika błędów do zadania importu zawiera błąd o pliku nie można odnaleźć na dysku importu. Należy zauważyć, że jest w stanie kolejnych składników `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

Następujący dziennik błędów przez zadanie eksportu wskazuje, że zawartość obiektu blob zostały pomyślnie zapisane na dysku, ale że wystąpił błąd podczas eksportowania właściwości obiektu blob.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Kolejne kroki
 
* [Interfejsu API REST usługi Storage importu/eksportu](/rest/api/storageimportexport/)
