---
title: Format pliku manifestu Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o formacie pliku manifestu dysku opisuje mapowanie między obiekty BLOB w magazynie obiektów Blob platformy Azure i plików na dysku podczas importowania lub eksportowania zadania w usłudze importu i eksportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873905"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure format pliku manifestu usługi Import/Eksport
Plik manifestu dysku opisuje mapowanie między obiekty BLOB w magazynie obiektów Blob platformy Azure i plików na dysku obejmujące zadania importu lub eksportu. Dla operacji importowania pliku manifestu jest tworzony jako część procesu przygotowywania dysków i są przechowywane na dysku przed wysłaniem dysku do centrum danych Azure. Podczas operacji eksportowania manifestu jest tworzone i przechowywane na dysku przez usługę Azure importu/eksportu.  
  
Dla obu importowanie i eksportowanie zadań, pliku manifestu dysku jest przechowywana na dysku importu lub eksportu; nie jest przekazywane do usługi za pośrednictwem każdej operacji interfejsu API.  
  
Poniżej opisano ogólny format pliku manifestu dysku:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Manifest elementów XML oraz atrybuty

W poniższej tabeli podano danych elementów i atrybutów w formacie XML manifestu dysku.  
  
|XML Element|Typ|Opis|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Element główny|Element główny pliku manifestu. Wszystkie elementy w pliku są poniżej tego elementu.|  
|`Version`|Atrybut ciągu|Wersja pliku manifestu.|  
|`Drive`|Zagnieżdżone — element XML|Zawiera manifest dla każdego dysku.|  
|`DriveId`|Ciąg|Identyfikator unikatowy dysku dla dysku. Identyfikator dysku znaleziono badając dysku dla jego numeru seryjnego. Numer seryjny dysku jest zazwyczaj wydrukowany na zewnątrz również dysku. `DriveID` Elementu musi występować przed każdą `BlobList` elementu w pliku manifestu.|  
|`StorageAccountKey`|Ciąg|Wymagany dla zadania importu, jeśli, a tylko wtedy, gdy `ContainerSas` nie jest określona. Klucz konta dla konta magazynu platformy Azure skojarzone z zadaniem.<br /><br /> Ten element został pominięty manifestu dla operacji eksportowania.|  
|`ContainerSas`|Ciąg|Wymagany dla zadania importu, jeśli, a tylko wtedy, gdy `StorageAccountKey` nie jest określona. Kontener SAS do uzyskiwania dostępu do obiektów blob skojarzony z zadaniem. Zobacz [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) dla jego format. Ten element został pominięty manifestu dla operacji eksportowania.|  
|`ClientCreator`|Ciąg|Określa klienta, który utworzony plik XML. Ta wartość nie jest interpretowany przez usługę importu i eksportu.|  
|`BlobList`|Zagnieżdżone — element XML|Zawiera listę obiektów blob, które są częścią importu lub eksportu zadania. Każdy obiekt blob w postaci listy obiektów blob może mieć te same właściwości i metadanych.|  
|`BlobList/MetadataPath`|Ciąg|Opcjonalny. Określa względną ścieżkę pliku na dysku, który zawiera metadane domyślnej zostanie ustawiona na obiekty BLOB na liście obiektów blob dla operacji importowania. Te metadane można opcjonalnie zastąpić na podstawie obiektu blob przez obiekt blob.<br /><br /> Ten element został pominięty manifestu dla operacji eksportowania.|  
|`BlobList/MetadataPath/@Hash`|Atrybut ciągu|Określa wartość skrótu kodowany w formacie Base16 MD5 dla pliku metadanych.|  
|`BlobList/PropertiesPath`|Ciąg|Opcjonalny. Określa względną ścieżkę pliku na dysku, który zawiera właściwości domyślnych, które zostaną ustawione na obiekty BLOB na liście obiektów blob dla operacji importowania. Te właściwości można opcjonalnie zastąpić na podstawie obiektu blob przez obiekt blob.<br /><br /> Ten element został pominięty manifestu dla operacji eksportowania.|  
|`BlobList/PropertiesPath/@Hash`|Atrybut ciągu|Określa wartość skrótu MD5 kodowany w formacie Base16 dla właściwości pliku.|  
|`Blob`|Zagnieżdżone — element XML|Zawiera informacje dotyczące każdego obiektu blob na wszystkich listach obiektów blob.|  
|`Blob/BlobPath`|Ciąg|Względny identyfikator URI do obiektu blob, począwszy od nazwy kontenera. W przypadku obiektu blob w kontenerze głównego, musi zaczynać się od `$root`.|  
|`Blob/FilePath`|Ciąg|Określa ścieżkę względną do pliku na dysku. Dla zadań eksportowania ścieżka obiektu blob będzie służyć do ścieżkę pliku, jeśli jest to możliwe. *np.*, `pictures/bob/wild/desert.jpg` zostaną wyeksportowane do `\pictures\bob\wild\desert.jpg`. Jednak ze względu na ograniczenia nazw systemu plików NTFS obiektu blob może zostać wyeksportowany do pliku za pomocą ścieżki, której nie przypominać ścieżka obiektu blob.|  
|`Blob/ClientData`|Ciąg|Opcjonalny. Zawiera komentarz z klienta. Ta wartość nie jest interpretowany przez usługę importu i eksportu.|  
|`Blob/Snapshot`|Data i godzina|Opcjonalny w przypadku zadań eksportu. Określa identyfikator migawki dla migawki wyeksportowany obiekt blob.|  
|`Blob/Length`|Liczba całkowita|Określa całkowita długość obiektu blob w bajtach. Wartość może być maksymalnie 200 GB dla blokowych obiektów blob i do 1 TB dla stronicowych obiektów blob. Dla stronicowych obiektów blob ta wartość musi być wielokrotnością 512.|  
|`Blob/ImportDisposition`|Ciąg|Opcjonalne dla zadania importu, pominąć w przypadku zadań eksportu. To ustawienie określa, jak usługa Import/Eksport powinna obsługiwać w przypadku zadania importu gdzie obiektu blob o takiej samej nazwie już istnieje. W przypadku pominięcia tej wartości z importu manifestu wartość domyślna to `rename`.<br /><br /> Wartości dla tego elementu:<br /><br /> -   `no-overwrite`: Jeśli docelowego obiektu blob jest już obecny o takiej samej nazwie, operacji importowania pominie importowania tego pliku.<br />-   `overwrite`: Wszystkie istniejące docelowego obiektu blob jest całkowicie zastąpiona nowo zaimportowany plik.<br />-   `rename`: Nowego obiektu blob zostanie przekazany z nazwą zmodyfikowane.<br /><br /> Zmiana nazwy reguły jest następujący:<br /><br /> — Jeśli nazwa obiektu blob nie zawiera kropkę, Nowa nazwa jest generowany przez dołączenie `(2)` oryginalną nazwę obiektu blob; Jeśli ta nowa nazwa również powoduje konflikt z istniejącą nazwą obiektu blob, następnie `(3)` jest dołączany zamiast `(2)`; i tak dalej.<br />— Jeśli nazwa obiektu blob zawiera kropkę, część po ostatnim kropki (.) jest uznawany za Nazwa rozszerzenia. Podobne do powyższej procedury `(2)` dodaje się przed ostatnim kropki (.) aby wygenerować nową nazwę; Jeśli nowa nazwa nadal powoduje konflikt z istniejącym obiektu blob nazwę, a następnie usługa próbuje `(3)`, `(4)`i tak dalej, aż do znalezienia niekolidujących nazwę.<br /><br /> Oto niektóre przykłady:<br /><br /> Obiekt blob `BlobNameWithoutDot` zostanie zmieniona na:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Obiekt blob `Seattle.jpg` zostanie zmieniona na:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Zagnieżdżone — element XML|Wymagany dla stronicowych obiektów blob.<br /><br /> Do zaimportowania operacji określa listę zakresów bajtów pliku do zaimportowania. Każdy zakres stron jest opisany przez przesunięcie i długość w plik źródłowy, który opisuje zakres strony, wraz z skrótu MD5 regionu. `Hash` Wymagany jest atrybut zakresu stron. Usługa zostanie przeprowadzona Weryfikacja, czy skrót danych w obiekcie blob odpowiada obliczoną wartość mieszania MD5 z zakresu stron. Dowolna liczba zakresów stron może służyć do opisują plik do zaimportowania, z łączny rozmiar maksymalnie 1 TB. Wszystkie zakresy strony musi zostać określona przez przesunięcie, a nie nakłada się jest niedozwolone.<br /><br /> Eksportu operacji określa zestaw zakresów bajtów obiektu blob, które zostały wyeksportowane do dysku.<br /><br /> Razem zakresów stron może obejmować tylko zakresy podrzędne obiektów blob lub pliku.  Oczekiwano pozostałej części pliku nie pasuje do żadnego zakresu żadnych stron i jego zawartość może być niezdefiniowana.|  
|`PageRange`|XML element|Reprezentuje zakres stron.|  
|`PageRange/@Offset`|Atrybut, liczba całkowita|Określa przesunięcie w pliku transferu i obiektów blob, gdzie rozpoczyna się w zakresie określonej strony. Ta wartość musi być wielokrotnością 512.|  
|`PageRange/@Length`|Atrybut, liczba całkowita|Określa długość zakresu stron. Ta wartość musi być wielokrotnością 512 i nie więcej niż 4 MB.|  
|`PageRange/@Hash`|Atrybut ciągu|Określa wartość skrótu MD5 kodowany w formacie Base16 zakresu stron.|  
|`BlockList`|Zagnieżdżone — element XML|Wymagany w przypadku blokowego obiektu blob z nazwanym bloków.<br /><br /> Dla operacji importowania listy zablokowanych określa zestaw bloków, które zostaną zaimportowane do magazynu Azure. W przypadku operacji eksportowania listy zablokowanych Określa, gdzie każdy blok przechowywanych w pliku na dysku eksportu. Każdy blok jest opisane przez przesunięcie w pliku i długość bloku; Każdy blok jest ponadto o nazwie atrybutu ID bloku i zawiera wyznaczania wartości skrótu MD5 dla bloku. Maksymalnie 50 000 bloków może służyć do opisu obiektu blob.  Wszystkie bloki muszą być uporządkowane przez przesunięcie, a jednocześnie powinno obejmować pełną gamę pliku *tj*, musi istnieć bez przerw między bloków. Jeśli obiekt blob jest nie więcej niż 64 MB, identyfikatory bloków dla każdego bloku musi być wszystkie nieobecne albo wszystkie. Identyfikatory bloku muszą być ciągami algorytmem Base64. Zobacz [Put bloku](/rest/api/storageservices/put-block) dodatkowe wymagania dotyczące identyfikatory bloków.|  
|`Block`|XML element|Reprezentuje blok.|  
|`Block/@Offset`|Atrybut, liczba całkowita|Określa przesunięcie, w którym rozpoczyna się określony blok.|  
|`Block/@Length`|Atrybut, liczba całkowita|Określa liczbę bajtów w bloku; Ta wartość musi być nie więcej niż 4MB.|  
|`Block/@Id`|Atrybut ciągu|Określa ciąg reprezentujący identyfikator bloku dla bloku.|  
|`Block/@Hash`|Atrybut ciągu|Określa Skrót MD5 kodowany w formacie Base16 bloku.|  
|`Blob/MetadataPath`|Ciąg|Opcjonalny. Względna ścieżka pliku metadanych. Podczas importowania metadanych jest ustawiona na docelowego obiektu blob. Podczas operacji eksportowania metadane obiektu blob są przechowywane w pliku metadanych na dysku.|  
|`Blob/MetadataPath/@Hash`|Atrybut ciągu|Określa Skrót MD5 kodowany w formacie Base16 pliku metadanych obiektu blob.|  
|`Blob/PropertiesPath`|Ciąg|Opcjonalny. Określa względną ścieżkę pliku właściwości. Podczas importowania właściwości są ustawiane na docelowego obiektu blob. Podczas operacji eksportowania właściwości obiektów blob są przechowywane w pliku właściwości na dysku.|  
|`Blob/PropertiesPath/@Hash`|Atrybut ciągu|Określa Skrót MD5 kodowany w formacie Base16 plik właściwości obiektu blob.|  
  
## <a name="next-steps"></a>Następne kroki
 
* [Interfejs API REST importu/eksportu magazynu](/rest/api/storageimportexport/)
