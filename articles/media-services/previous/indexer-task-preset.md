---
title: Zadanie ustawienie wstępne dla usługi Azure Media Indexer
description: Ten temat zawiera omówienie zadań ustawienie wstępne dla usługi Azure Media Indexer.
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 02/08/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 8ce3ea3847e4c8c022f17375676d8415372dec85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466310"
---
# <a name="task-preset-for-azure-media-indexer"></a>Zadanie ustawienie wstępne dla usługi Azure Media Indexer 

Usługa Azure Media Indexer jest procesor multimediów, którego używasz do wykonywania następujących zadań: Dodaj plików multimedialnych i zawartości multimedialnej możliwość wyszukiwania, Generuj zamknięte śledzi napisów i słów kluczowych, indeksowanie plików zasobów, będące częścią element zawartości.

W tym temacie opisano zadania ustawienie wstępne, trzeba przekazać do zadania. Aby uzyskać kompletny przykład, zobacz [indeksowaniu plików multimedialnych przy użyciu usługi Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Plik XML konfiguracji indeksatora multimediów platformy Azure

W poniższej tabeli opisano elementy i atrybuty konfiguracji XML.

|Name (Nazwa)|Wymagane|Opis|
|---|---|---|
|Dane wejściowe|true|Pliki zasobów, które mają być indeksowane.<br/>Usługa Azure Media Indexer obsługuje następujące formaty plików multimediów: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Można określić nazwę pliku (s) w **nazwa** lub **listy** atrybutu **wejściowych** — element (jak pokazano poniżej). Jeśli nie określisz plik zasobu, który indeks, jest pobierany plik podstawowy. Jeśli nie podstawowego pliku zasobu jest ustawiona, pierwszego pliku wejściowego elementu jest indeksowana.<br/><br/>Aby jawnie określić nazwę pliku zasobów, należy wykonać:<br/>```<input name="TestFile.wmv" />```<br/><br/>Można również indeks wiele zasobów pliki jednocześnie (maksymalnie 10). W tym celu:<br/>— Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie .lst.<br/>-Dodaj listę wszystkich nazw plików zasobów w danych wejściowych elementów zawartości do tego pliku manifestu.<br/>— Dodać pliku (przekazywanie) manifestu do zasobu.<br/>— Określ nazwę pliku manifestu w atrybucie listy danych wejściowych.<br/>```<input list="input.lst">```<br/><br/>**Uwaga:** Jeśli dodasz więcej niż 10 plików do pliku manifestu indeksowania zadanie zakończy się niepowodzeniem z kodem błędu 2006.|
|metadane|false|Metadane dla plików określonego zasobu.<br/>```<metadata key="..." value="..." />```<br/><br/>Możesz podać wartości wstępnie zdefiniowane klucze. <br/><br/>Obecnie obsługiwane są następujące klucze:<br/><br/>**Tytuł** i **opis** — używany do aktualizacji modelu językowego w celu zwiększenia dokładności rozpoznawania mowy.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**Nazwa użytkownika** i **hasło** — używany do uwierzytelniania podczas pobierania plików internetowych za pośrednictwem protokołu http lub https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Wartości nazwy użytkownika i hasła dotyczą wszystkich adresy URL multimediów w manifeście danych wejściowych.|
|danych<br/><br/>Dodane w wersji 1.2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR").|false|Funkcja rozpoznawania mowy ma następujące klucze ustawień:<br/><br/>Język:<br/>-Język naturalny rozpoznawany w pliku multimedialnego.<br/>-Angielski, hiszpański<br/><br/>CaptionFormats:<br/>-rozdzieloną średnikami listę podpis żądany element wyjściowy formatuje (jeśli istnieje)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>-Flagę logiczną określenie, czy plik AIB jest wymagana (do użytku z programem SQL Server i klienta IFilter indeksatora). Aby uzyskać więcej informacji zobacz przy użyciu plików AIB za pomocą usługi Azure Media Indexer i programu SQL Server.<br/>- True; False<br/><br/>GenerateKeywords:<br/>-Flagę logiczną określenie, czy plik XML — słowo kluczowe jest wymagana.<br/>- True; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Przykład pliku XML konfiguracji w usłudze Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Kolejne kroki

Zobacz [indeksowaniu plików multimedialnych przy użyciu usługi Azure Media Indexer](media-services-index-content.md).

