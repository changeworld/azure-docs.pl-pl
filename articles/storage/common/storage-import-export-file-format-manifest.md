---
title: Format pliku manifestu usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o formacie dysku pliku manifestu który opisuje mapowanie między obiektów blob w usłudze Azure Blob storage i plików na dysku podczas importowania lub eksportowania zadania w usłudze Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478627"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Format pliku manifestu usługi w usłudze Azure Import/Export
Plik manifestu dysku opisuje mapowanie między obiektów blob w usłudze Azure Blob storage i plików na dysku wchodzących w skład zadania importu lub eksportu. Dla operacji importowania pliku manifestu jest tworzony jako część procesu przygotowywania dysku i są przechowywane na dysku przed wysłaniem dysk do centrum danych platformy Azure. Podczas operacji eksportowania manifest jest tworzone i przechowywane na dysku przez usługę Azure Import/Export.  
  
Dla obu Importuj i Eksportuj zadania pliku manifestu na dysku jest przechowywany na dysku importu lub eksportu; nie zostaną przesłane do usługi za pomocą dowolnej operacji interfejsu API.  
  
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

W poniższej tabeli podano elementów danych i atrybuty format XML manifestu dysku.  
  
|XML Element|Typ|Opis|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Element główny|Element główny pliku manifestu. Wszystkie inne elementy w pliku są poniżej tego elementu.|  
|`Version`|Atrybut ciągu|Wersja pliku manifestu.|  
|`Drive`|Zagnieżdżone — element XML|Zawiera manifest dla każdego dysku.|  
|`DriveId`|String|Identyfikator unikatowy dysku dla dysku. Identyfikator dysku zostanie znaleziony, badając dysku jego numeru seryjnego. Numer seryjny dysku jest zazwyczaj wydrukowany na zewnątrz dysku, jak również. `DriveID` Elementów musi występować przed każdą `BlobList` elementu w pliku manifestu.|  
|`StorageAccountKey`|String|Wymagane dla zadania importu, jeśli i tylko wtedy, gdy `ContainerSas` nie zostanie określony. Klucz konta dla konta magazynu platformy Azure skojarzone z zadaniem.<br /><br /> Ten element jest pomijany z manifestu dla operacji eksportu.|  
|`ContainerSas`|String|Wymagane dla zadania importu, jeśli i tylko wtedy, gdy `StorageAccountKey` nie zostanie określony. Sygnatury dostępu Współdzielonego kontenera do uzyskiwania dostępu do obiektów blob, skojarzone z zadaniem. Zobacz [umieścić zadania](/rest/api/storageimportexport/jobs) dla jego format. Ten element jest pomijany z manifestu dla operacji eksportu.|  
|`ClientCreator`|String|Określa klienta, która utworzyła plik XML. Ta wartość nie jest interpretowany przez usługę Import/Export.|  
|`BlobList`|Zagnieżdżone — element XML|Zawiera listę obiektów blob, które są dostępne w ramach importowania lub eksportowania zadania. Każdy obiekt blob z listy obiektów blob udostępnia te same właściwości i metadanych.|  
|`BlobList/MetadataPath`|String|Opcjonalny. Określa ścieżkę względną w pliku na dysku, który zawiera metadane domyślne, które zostaną ustawione dla obiektów blob do listy obiektów blob dla operacji importowania. Te metadane można opcjonalnie zastąpić na podstawie obiektu blob przez obiekt blob.<br /><br /> Ten element jest pomijany z manifestu dla operacji eksportu.|  
|`BlobList/MetadataPath/@Hash`|Atrybut ciągu|Określa wartość skrótu MD5 zakodowane w formacie Base16 dla pliku metadanych.|  
|`BlobList/PropertiesPath`|String|Opcjonalny. Określa ścieżkę względną w pliku na dysku, który zawiera właściwości domyślne, które zostaną ustawione dla obiektów blob do listy obiektów blob dla operacji importowania. Te właściwości można opcjonalnie zastąpić na podstawie obiektu blob przez obiekt blob.<br /><br /> Ten element jest pomijany z manifestu dla operacji eksportu.|  
|`BlobList/PropertiesPath/@Hash`|Atrybut ciągu|Określa wartość skrótu MD5 Base16 algorytmem Base64 pliku właściwości.|  
|`Blob`|Zagnieżdżone — element XML|Zawiera informacje o poszczególnych obiektów blob na wszystkich listach obiektów blob.|  
|`Blob/BlobPath`|String|Względny identyfikator URI do obiektu blob, począwszy od nazwy kontenera. Jeśli obiekt blob w kontenerze głównego musi zaczynać się `$root`.|  
|`Blob/FilePath`|String|Określa ścieżkę względną do pliku na dysku. Dla zadań eksportowania ścieżka obiektu blob stosowanych w odniesieniu do ścieżki pliku, jeśli jest to możliwe. *np.* , `pictures/bob/wild/desert.jpg` zostaną wyeksportowane do `\pictures\bob\wild\desert.jpg`. Jednak ze względu na ograniczenia nazw systemu plików NTFS, obiekt blob może być eksportowany do pliku ze ścieżką, która nie przypominają ścieżka obiektu blob.|  
|`Blob/ClientData`|String|Opcjonalny. Zawiera komentarze od klienta. Ta wartość nie jest interpretowany przez usługę Import/Export.|  
|`Blob/Snapshot`|DateTime|Opcjonalnie na potrzeby zadań eksportu. Określa identyfikator migawki migawki eksportowanego obiektu blob.|  
|`Blob/Length`|Integer|Łączna długość obiektu blob określa w bajtach. Wartość może być maksymalnie 200 GB dla blokowych obiektów blob i do 1 TB dla stronicowych obiektów blob. Dla stronicowych obiektów blob ta wartość musi być wielokrotnością liczby 512.|  
|`Blob/ImportDisposition`|String|Opcjonalnie na potrzeby zadań importu, pominąć w przypadku zadań eksportu. To ustawienie określa, jak usługa Import/Export powinna obsługiwać w przypadku zadania importu gdy obiekt blob z tej samej nazwie już istnieje. Jeśli ta wartość zostanie pominięty z manifestu importu, wartość domyślna to `rename`.<br /><br /> Wartości dla tego elementu:<br /><br /> -   `no-overwrite`: Jeśli docelowy obiekt blob znajduje się już o takiej samej nazwie, operacja importowania zostanie pominięta, importowania tego pliku.<br />-   `overwrite`: Wszelkie istniejący docelowy obiekt blob jest całkowicie zastąpiona przez nowo zaimportowany plik.<br />-   `rename`: Nowy obiekt blob zostaną przekazane o nazwie zmodyfikowane.<br /><br /> Zmiana nazwy reguły jest następująca:<br /><br /> — Jeśli nazwę obiektu blob nie zawiera kropkę, Nowa nazwa jest generowany przez dołączenie `(2)` na oryginalną nazwę obiektu blob; Jeśli jest to nowa nazwa również powoduje konflikt z istniejącą nazwą obiektu blob, następnie `(3)` jest dołączany zamiast `(2)`; i tak dalej.<br />— Jeśli nazwę obiektu blob zawiera kropkę, część po ostatniej kropka jest uważany za Nazwa rozszerzenia. Podobne do powyższej procedury `(2)` jest wstawiany przed próbą ostatniego kropkę, aby wygenerować nową nazwę; Jeśli nowa nazwa nadal powoduje konflikt z istniejącym blob nazwę, a następnie usługa `(3)`, `(4)`i tak dalej, aż do znalezienia bezkonfliktowe nazwę.<br /><br /> Kilka przykładów:<br /><br /> Obiekt blob `BlobNameWithoutDot` zostanie zmieniona na:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Obiekt blob `Seattle.jpg` zostanie zmieniona na:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Zagnieżdżone — element XML|Wymagana w przypadku stronicowych obiektów blob.<br /><br /> Do zaimportowania operacji określa listę zakresów bajtów w pliku do zaimportowania. Każdy z zakresów stron jest opisana przez przesunięcia i długości w pliku źródłowego, który opisuje zakres stron, wraz z Skrót MD5 regionu. `Hash` Atrybut zakres stron jest wymagany. Usługa zostanie przeprowadzona Weryfikacja, czy skrót danych w obiekcie blob odpowiada obliczony Skrót MD5 z zakresu stron. Dowolną liczbę zakresów stron może służyć do opisywania plik do zaimportowania, za pomocą łączny rozmiar do 1 TB. Wszystkie zakresy stron musi zostać określona przez przesunięcie i mogą nie nakładają się.<br /><br /> Eksportu usługi operacji określa zestaw zakresów bajtów obiektu blob, które zostały wyeksportowane do dysku.<br /><br /> Zakresy stron razem może obejmować tylko zakresy podrzędne obiektu blob lub pliku.  Oczekuje się, pozostała część pliku nie pasuje do żadnego dowolny zakres strony i jego zawartość może być niezdefiniowana.|  
|`PageRange`|— Element XML|Reprezentuje zakres stron.|  
|`PageRange/@Offset`|Atrybut, liczba całkowita|Określa przesunięcie w pliku transferu i obiektów blob, gdzie rozpoczyna się w zakresie określonej strony. Ta wartość musi być wielokrotnością liczby 512.|  
|`PageRange/@Length`|Atrybut, liczba całkowita|Określa długość zakresu stron. Ta wartość musi być wielokrotnością liczby 512 i nie więcej niż 4 MB.|  
|`PageRange/@Hash`|Atrybut ciągu|Określa wartość skrótu MD5 zakodowane w formacie Base16 zakresu stron.|  
|`BlockList`|Zagnieżdżone — element XML|Wymagane na potrzeby blokowych obiektów blob przy użyciu bloków nazwanych.<br /><br /> Dla operacji importowania listy zablokowanych określa zestaw bloków, które zostaną zaimportowane do usługi Azure Storage. Dla operacji eksportowania zablokowanych Określa, gdzie każdy blok zostały zapisane w pliku na dysku eksportu. Każdy blok jest opisana przez przesunięcie w pliku i długość bloku; Każdy blok Ponadto jest o nazwie określonej przez atrybut ID bloku i zawiera skrót MD5 dla bloku. Maksymalnie 50 000 bloków może służyć do opisu obiektu blob.  Wszystkie bloki muszą być uporządkowane według przesunięcie, a razem powinny obejmować pełen zakres pliku *tj*, może być żadnych odstępów między blokami. Jeśli obiekt blob jest nie więcej niż 64 MB, identyfikatory bloków dla każdego bloku musi być wszystkie nieobecne albo wszystkie obecne. Blok identyfikatory muszą być zakodowane w formacie Base64 ciągów. Zobacz [umieszczania bloku](/rest/api/storageservices/put-block) dalsze wymagania dotyczące identyfikatory bloków.|  
|`Block`|— Element XML|Reprezentuje blok.|  
|`Block/@Offset`|Atrybut, liczba całkowita|Określa przesunięcie, gdzie rozpoczyna się określony blok.|  
|`Block/@Length`|Atrybut, liczba całkowita|Określa liczbę bajtów w bloku; Ta wartość musi być nie więcej niż 4MB.|  
|`Block/@Id`|Atrybut ciągu|Określa ciąg reprezentujący identyfikator bloku dla bloku.|  
|`Block/@Hash`|Atrybut ciągu|Określa Skrót MD5 zakodowane w formacie Base16 bloku.|  
|`Blob/MetadataPath`|String|Opcjonalny. Określa ścieżkę względną w pliku metadanych. Podczas importowania metadanych jest ustawiona na docelowego obiektu blob. Podczas operacji eksportowania metadanych obiektu blob są przechowywane w pliku metadanych na dysku.|  
|`Blob/MetadataPath/@Hash`|Atrybut ciągu|Określa Skrót MD5 Base16 algorytmem Base64 pliku metadanych obiektu blob.|  
|`Blob/PropertiesPath`|String|Opcjonalny. Określa względną ścieżkę do pliku właściwości. Podczas importowania właściwości są ustawione na docelowego obiektu blob. Podczas operacji eksportowania właściwości obiektu blob są przechowywane w pliku właściwości na dysku.|  
|`Blob/PropertiesPath/@Hash`|Atrybut ciągu|Określa Skrót MD5 Base16 algorytmem Base64 pliku właściwości obiektu blob.|  
  
## <a name="next-steps"></a>Kolejne kroki
 
* [Interfejsu API REST usługi Storage importu/eksportu](/rest/api/storageimportexport/)
