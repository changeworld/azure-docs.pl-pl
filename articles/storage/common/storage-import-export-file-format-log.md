---
title: Format pliku dziennika Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o formacie pliki dziennika utworzone podczas czynności są wykonywane zadania usługi Import/Eksport.
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
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874101"
---
# <a name="azure-importexport-service-log-file-format"></a>Format pliku dziennika usług Azure Import/Eksport
Gdy usługa Import/Eksport Microsoft Azure wykonuje akcję na dysku jako część zadania importu lub eksportu, dzienniki są zapisywane w blokowe obiekty BLOB na koncie magazynu skojarzonego z tym zadaniem.  
  
Istnieją dwa dzienniki, które mogą być zapisywane przez usługę importu/eksportu:  
  
-   Dziennik błędów jest zawsze generowany w przypadku wystąpienia błędu.  
  
-   Pełny dziennik nie jest domyślnie włączona, ale może być włączona `EnableVerboseLog` właściwość [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs#Jobs_Update) operacji.  
  
## <a name="log-file-location"></a>Lokalizacja pliku dziennika  
Dzienniki są zapisywane w blokowe obiekty BLOB w kontenerze lub określony przez katalog wirtualny `ImportExportStatesPath` ustawienie, które można ustawić na `Put Job` operacji. Lokalizacja, do której zapisywane są dzienniki zależy od sposobu uwierzytelniania jest określona dla zadania, wraz z wartość określona dla `ImportExportStatesPath`. Uwierzytelnianie dla tego zadania można określić za pomocą klucza konta magazynu lub kontener SAS (sygnatury dostępu współdzielonego).  
  
Nazwa kontenera lub katalog wirtualny może być domyślną nazwę `waimportexport`, lub innego kontenera ani nazwy katalogu wirtualnego, który określisz.  
  
W poniższej tabeli przedstawiono możliwe opcje:  
  
|Metoda uwierzytelniania|Wartość `ImportExportStatesPath`elementu|Lokalizacja dziennika obiektów blob|  
|---------------------------|----------------------------------------------|---------------------------|  
|Klucz konta magazynu|Wartość domyślna|Kontener o nazwie `waimportexport`, który jest domyślnym kontenerem. Na przykład:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Klucz konta magazynu|Wartość określona przez użytkownika|Kontener o nazwie przez użytkownika. Na przykład:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Kontener SAS|Wartość domyślna|Katalog wirtualny o nazwie `waimportexport`, która jest nazwą domyślną poniżej kontener określony w sygnatury dostępu Współdzielonego.<br /><br /> Na przykład, jeśli sygnatury dostępu Współdzielonego określone dla zadania jest `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, następnie będzie lokalizacja dziennika`https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Kontener SAS|Wartość określona przez użytkownika|Katalog wirtualny o nazwie przez użytkownika, poniżej kontener określony w sygnatury dostępu Współdzielonego.<br /><br /> Na przykład, jeśli sygnatury dostępu Współdzielonego określone dla zadania jest `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, a określony katalog wirtualny nosi nazwę `mylogblobs`, następnie będzie lokalizacja dziennika `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Adres URL błąd i pełne dzienniki można pobrać przez wywołanie metody [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji. Dzienniki są dostępne po zakończeniu przetwarzania dysku.  
  
## <a name="log-file-format"></a>Format pliku dziennika  
Format dla obu dzienników jest taki sam: obiektu blob zawierającego XML opisy zdarzeń, które wystąpiły podczas kopiowania obiektów blob między dysk twardy i konta klienta.  
  
Pełny dziennik zawiera pełne informacje na temat stanu operacji kopiowania dla każdego obiektu blob (w przypadku zadania importu) lub pliku (dla zadania eksportu), w dzienniku błędów zawiera tylko informacje dotyczące obiektów blob lub plików, które napotkały błędy podczas importowania lub zadanie eksportu.  
  
Format pełny dziennik jest pokazany poniżej. Dziennik błędów ma taką samą strukturę, ale odfiltrowuje pomyślnych operacji.  

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

W poniższej tabeli opisano elementy pliku dziennika.  
  
|XML Element|Typ|Opis|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML Element|Reprezentuje dziennika dysku.|  
|`Version`|Atrybut ciągu|Wersja formatu dziennika.|  
|`DriveId`|Ciąg|Numer seryjny sprzętu stacji.|  
|`Status`|Ciąg|Stan przetwarzania dysku. Zobacz `Drive Status Codes` tabela poniżej, aby uzyskać więcej informacji.|  
|`Blob`|Zagnieżdżone — element XML|Reprezentuje obiektu blob.|  
|`Blob/BlobPath`|Ciąg|Identyfikator URI obiektu blob.|  
|`Blob/FilePath`|Ciąg|Ścieżka względna do pliku na dysku.|  
|`Blob/Snapshot`|Data i godzina|Wersja migawki obiektu blob dla tylko zadania eksportu.|  
|`Blob/Length`|Liczba całkowita|Całkowita długość obiektu blob w bajtach.|  
|`Blob/LastModified`|Data i godzina|Data/godzina ostatniej modyfikacji obiektu blob, tylko zadania eksportu.|  
|`Blob/ImportDisposition`|Ciąg|Dyspozycji importu obiektu blob dla zadania importu tylko.|  
|`Blob/ImportDisposition/@Status`|Atrybut ciągu|Stan dyspozycji importu.|  
|`PageRangeList`|Zagnieżdżone — element XML|Reprezentuje listę zakresów stron dla stronicowych obiektów blob.|  
|`PageRange`|XML element|Reprezentuje zakres stron.|  
|`PageRange/@Offset`|Atrybut, liczba całkowita|Przesunięcie początkowe zakres stron w obiekcie blob.|  
|`PageRange/@Length`|Atrybut, liczba całkowita|Długość w bajtach zakres stron.|  
|`PageRange/@Hash`|Atrybut ciągu|Kodowany w formacie Base16 Skrót MD5 zakresu strony.|  
|`PageRange/@Status`|Atrybut ciągu|Stan przetwarzania zakres stron.|  
|`BlockList`|Zagnieżdżone — element XML|Reprezentuje listę bloków dla blokowego obiektu blob.|  
|`Block`|XML element|Reprezentuje blok.|  
|`Block/@Offset`|Atrybut, liczba całkowita|Przesunięcie początkowe bloku w obiekcie blob.|  
|`Block/@Length`|Atrybut, liczba całkowita|Długość w bajtach bloku.|  
|`Block/@Id`|Atrybut ciągu|Identyfikator bloku.|  
|`Block/@Hash`|Atrybut ciągu|Kodowany w formacie Base16 Skrót MD5 bloku.|  
|`Block/@Status`|Atrybut ciągu|Stan przetwarzania bloku.|  
|`Metadata`|Zagnieżdżone — element XML|Reprezentuje metadane obiektu blob.|  
|`Metadata/@Status`|Atrybut ciągu|Stan przetwarzania metadane obiektu blob.|  
|`Metadata/GlobalPath`|Ciąg|Ścieżka względna do pliku globalnych metadanych.|  
|`Metadata/GlobalPath/@Hash`|Atrybut ciągu|Kodowany w formacie Base16 Skrót MD5 pliku globalnych metadanych.|  
|`Metadata/Path`|Ciąg|Ścieżka względna do pliku metadanych.|  
|`Metadata/Path/@Hash`|Atrybut ciągu|Kodowany w formacie Base16 Skrót MD5 pliku metadanych.|  
|`Properties`|Zagnieżdżone — element XML|Reprezentuje właściwości obiektu blob.|  
|`Properties/@Status`|Atrybut ciągu|Stan przetwarzania właściwości obiektu blob, np. nie można odnaleźć pliku, ukończone.|  
|`Properties/GlobalPath`|Ciąg|Ścieżka względna do pliku właściwości globalne.|  
|`Properties/GlobalPath/@Hash`|Atrybut ciągu|Kodowany w formacie Base16 Skrót MD5 globalnych właściwości pliku.|  
|`Properties/Path`|Ciąg|Ścieżka względna do pliku właściwości.|  
|`Properties/Path/@Hash`|Atrybut ciągu|Kodowany w formacie Base16 MD5 skrótu właściwości pliku.|  
|`Blob/Status`|Ciąg|Stan przetwarzania obiektu blob.|  
  
# <a name="drive-status-codes"></a>Kody stanu dysku  
W poniższej tabeli przedstawiono kody stanu do przetworzenia dysku.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Dysk zakończył przetwarzanie bez żadnych błędów.|  
|`CompletedWithWarnings`|Dysk zakończył przetwarzanie z ostrzeżeniami w jeden lub więcej obiektów blob na przepisy importu określony dla obiektów blob.|  
|`CompletedWithErrors`|Dysk zostało zakończone z błędami obiektów blob lub fragmentów.|  
|`DiskNotFound`|Dysk nie znajduje się na dysku.|  
|`VolumeNotNtfs`|Pierwszy ilość danych na dysku nie jest w formacie NTFS.|  
|`DiskOperationFailed`|Wystąpił nieznany błąd podczas wykonywania operacji na dysku.|  
|`BitLockerVolumeNotFound`|Znaleziono encryptable woluminu funkcji BitLocker.|  
|`BitLockerNotActivated`|Nie włączono funkcji BitLocker w woluminie.|  
|`BitLockerProtectorNotFound`|Ochrony klucza numerycznym hasłem nie istnieje na tym woluminie.|  
|`BitLockerKeyInvalid`|Podane hasło numeryczne nie można odblokować woluminu.|  
|`BitLockerUnlockVolumeFailed`|Nieznany błąd wystąpił podczas próby odblokowania woluminu.|  
|`BitLockerFailed`|Wystąpił nieznany błąd podczas wykonywania operacji funkcji BitLocker.|  
|`ManifestNameInvalid`|Nazwa pliku manifestu jest nieprawidłowa.|  
|`ManifestNameTooLong`|Nazwa pliku manifestu jest za długa.|  
|`ManifestNotFound`|Nie znaleziono pliku manifestu.|  
|`ManifestAccessDenied`|Odmowa dostępu do pliku manifestu.|  
|`ManifestCorrupted`|Plik manifestu jest uszkodzony (zawartość nie odpowiada jego skrót).|  
|`ManifestFormatInvalid`|Zawartość manifestu jest niezgodny z wymaganym formatem.|  
|`ManifestDriveIdMismatch`|Identyfikator dysku w pliku manifestu jest niezgodna z jednego odczytu z dysku.|  
|`ReadManifestFailed`|Wystąpił błąd We/Wy dysku podczas odczytu z manifestu.|  
|`BlobListFormatInvalid`|Eksport listy obiektów blob z obiektu blob jest niezgodny z wymaganym formatem.|  
|`BlobRequestForbidden`|Dostęp do obiektów blob na koncie magazynu jest zabroniony. Może to być spowodowane klucz konta magazynu nieprawidłowy lub kontener sygnatury dostępu Współdzielonego.|  
|`InternalError`|I wystąpił błąd wewnętrzny podczas przetwarzania na dysku.|  
  
## <a name="blob-status-codes"></a>Kody stanu obiektu blob  
W poniższej tabeli przedstawiono kody stanu do przetwarzania obiektu blob.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Obiekt blob zakończył przetwarzanie bez błędów.|  
|`CompletedWithErrors`|Obiekt blob zakończył przetwarzanie z błędami w jednej lub więcej zakresów stron lub bloków metadanych i właściwości.|  
|`FileNameInvalid`|Nazwa pliku jest nieprawidłowa.|  
|`FileNameTooLong`|Nazwa pliku jest zbyt długa.|  
|`FileNotFound`|Nie znaleziono pliku.|  
|`FileAccessDenied`|Odmowa dostępu do pliku.|  
|`BlobRequestFailed`|Żądanie usługi Blob do obiektu blob nie powiodło się.|  
|`BlobRequestForbidden`|Żądanie usługi Blob do obiektu blob jest zabronione. Może to być spowodowane klucz konta magazynu nieprawidłowy lub kontener sygnatury dostępu Współdzielonego.|  
|`RenameFailed`|Nie można zmienić nazwy obiektów blob (w przypadku zadania importu) lub pliku (dla zadania eksportu).|  
|`BlobUnexpectedChange`|Nieoczekiwana zmiana wystąpił błąd związany z obiektów blob (w przypadku zadania eksportu).|  
|`LeasePresent`|Brak dzierżawę istnieje w obiekcie blob.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania obiektu blob.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania obiektu blob.|  
  
## <a name="import-disposition-status-codes"></a>Kody stanu dyspozycji importu  
W poniższej tabeli przedstawiono kody stanu rozpoznawania dyspozycji importu.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Created`|Utworzono obiektu blob.|  
|`Renamed`|Nazwa obiektu blob została zmieniona na dyspozycji importu zmiany nazwy. `Blob/BlobPath` Element zawiera identyfikator URI obiektu blob zmienioną nazwę.|  
|`Skipped`|Obiekt blob zostało pominięte na `no-overwrite` zaimportować dyspozycji.|  
|`Overwritten`|Obiekt blob zastąpiły istniejącym obiektem blob na `overwrite` zaimportować dyspozycji.|  
|`Cancelled`|Błąd przed została zatrzymana, dalsze przetwarzanie dyspozycji importu.|  
  
## <a name="page-rangeblock-status-codes"></a>Kody stanu zakres/blok strony  
W poniższej tabeli przedstawiono kody stanu do przetworzenia strony zakres lub blok.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Strona zakres lub blok zakończył przetwarzanie bez żadnych błędów.|  
|`Committed`|Blok został przekazany, ale nie w bloku pełnej listy ponieważ inne bloki mają nie powiodło się lub umieść samej listy pełny blok nie powiodło się.|  
|`Uncommitted`|Blok jest przekazany, ale nie zostało zatwierdzone.|  
|`Corrupted`|Strona zakres lub blok jest uszkodzony (zawartość nie odpowiada jego skrót).|  
|`FileUnexpectedEnd`|Napotkano nieoczekiwany koniec pliku.|  
|`BlobUnexpectedEnd`|Napotkano nieoczekiwany koniec obiektu blob.|  
|`BlobRequestFailed`|Żądanie usługi Blob do uzyskania dostępu do strony zakres lub blok nie powiodło się.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania strony zakres lub blok.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania strony zakres lub blok.|  
|`Cancelled`|Błąd przed została zatrzymana, dalsze przetwarzanie strony zakres lub blok.|  
  
## <a name="metadata-status-codes"></a>Kody stanu metadanych  
W poniższej tabeli przedstawiono kody stanu do przetworzenia metadane obiektu blob.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Metadane zakończył przetwarzanie bez błędów.|  
|`FileNameInvalid`|Nazwa pliku metadanych jest nieprawidłowa.|  
|`FileNameTooLong`|Nazwa pliku metadanych jest zbyt długa.|  
|`FileNotFound`|Nie znaleziono pliku metadanych.|  
|`FileAccessDenied`|Odmowa dostępu do pliku metadanych.|  
|`Corrupted`|Plik metadanych jest uszkodzony (zawartość nie odpowiada jego skrót).|  
|`XmlReadFailed`|Zawartość metadanych jest niezgodny z wymaganym formatem.|  
|`XmlWriteFailed`|Zapisywanie metadanych XML nie powiodło się.|  
|`BlobRequestFailed`|Żądanie obsługi obiektów Blob można uzyskać dostępu do metadanych nie powiodła się.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania metadanych.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania metadanych.|  
|`Cancelled`|Błąd przed została zatrzymana, dalsze przetwarzanie metadanych.|  
  
## <a name="properties-status-codes"></a>Kody stanu właściwości  
W poniższej tabeli przedstawiono kody stanu do przetworzenia właściwości obiektu blob.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|`Completed`|Właściwości Zakończono przetwarzanie bez żadnych błędów.|  
|`FileNameInvalid`|Nazwa pliku właściwości jest nieprawidłowa.|  
|`FileNameTooLong`|Nazwa właściwości pliku jest zbyt długa.|  
|`FileNotFound`|Nie znaleziono pliku właściwości.|  
|`FileAccessDenied`|Odmowa dostępu do pliku właściwości.|  
|`Corrupted`|Plik właściwości jest uszkodzony (zawartość nie odpowiada jego skrót).|  
|`XmlReadFailed`|Właściwości zawartości jest niezgodny z wymaganym formatem.|  
|`XmlWriteFailed`|Zapisywanie właściwości XML nie powiodło się.|  
|`BlobRequestFailed`|Żądanie usługi obiektów Blob, aby uzyskać dostęp do właściwości nie powiodło się.|  
|`IOFailed`|Wystąpił błąd We/Wy dysku lub w sieci podczas przetwarzania właściwości.|  
|`Failed`|Wystąpił nieznany błąd podczas przetwarzania właściwości.|  
|`Cancelled`|Błąd przed została zatrzymana, dalsze przetwarzanie właściwości.|  
  
## <a name="sample-logs"></a>Dzienniki próbki  
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

 W dzienniku błędów wykonaj zadania importu zawiera błąd o pliku nie znaleziono na dysku importu. Należy pamiętać, że jest w stanie kolejnych składników `Cancelled`.  
  
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

Dziennik błędów następujące zadania eksportu wskazuje, że zawartości obiektu blob został pomyślnie zapisany na dysku, ale wystąpił błąd podczas eksportowania właściwości obiektu blob.  
  
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
  
## <a name="next-steps"></a>Następne kroki
 
* [Interfejs API REST importu/eksportu magazynu](/rest/api/storageimportexport/)
