---
title: Ustawienia predefiniowane zadań dla indeksatora multimediów platformy Azure
description: W tym temacie przedstawiono omówienie ustawień predefiniowanych zadań dla indeksatora multimediów usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896039"
---
# <a name="task-preset-for-azure-media-indexer"></a>Ustawienia predefiniowane zadań dla indeksatora multimediów platformy Azure 

Usługa Azure Media Indexer to procesor multimediów używany do wykonywania następujących zadań: umożliwianie przeszukiwania plików multimedialnych i zawartości, generowanie utworów i słów kluczowych podpisów kodujących, indeksowanie plików zasobów, które są częścią zasobu.

W tym temacie opisano ustawienia predefiniowane zadania, które należy przekazać do zadania indeksowania. Na przykład zobacz [Indeksowanie plików multimedialnych za pomocą programu Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML konfiguracji indeksatora multimediów azure

W poniższej tabeli wyjaśniono elementy i atrybuty konfiguracji XML.

|Nazwa|Wymagane|Opis|
|---|---|---|
|Dane wejściowe|true|Pliki zasobów, które chcesz indeksować.<br/>Usługa Azure Media Indexer obsługuje następujące formaty plików multimedialnych: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Nazwę pliku można określić w **atrybucie nazwa** lub **lista** elementu **wejściowego** (jak pokazano poniżej). Jeśli nie określisz, który plik zasobu ma być indeksowane, plik podstawowy jest pobierany. Jeśli nie ustawiono żadnego pliku zasobu podstawowego, pierwszy plik w zasobie wejściowym jest indeksowany.<br/><br/>Aby jawnie określić nazwę pliku zasobu, wykonaj następujące polecenie:<br/>```<input name="TestFile.wmv" />```<br/><br/>Można również indeksować wiele plików zasobów jednocześnie (maksymalnie 10 plików). W tym celu:<br/>- Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie .lst.<br/>- Dodaj listę wszystkich nazw plików zasobów w zasobie wejściowym do tego pliku manifestu.<br/>- Dodaj (prześlij) plik manifestu do zasobu.<br/>- Określ nazwę pliku manifestu w atrybucie listy danych wejściowych.<br/>```<input list="input.lst">```<br/><br/>**Uwaga:** Jeśli dodasz więcej niż 10 plików do pliku manifestu, zadanie indeksowania zakończy się niepowodzeniem z kodem błędu 2006.|
|metadane|false|Metadane dla określonych plików zasobów.<br/>```<metadata key="..." value="..." />```<br/><br/>Można podać wartości dla wstępnie zdefiniowanych kluczy. <br/><br/>Obecnie obsługiwane są następujące klucze:<br/><br/>**tytuł** i **opis** — służy do aktualizacji modelu języka w celu poprawy dokładności rozpoznawania mowy.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**nazwa użytkownika** i **hasło** - używane do uwierzytelniania podczas pobierania plików internetowych za pośrednictwem http lub https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Wartości nazwy użytkownika i hasła mają zastosowanie do wszystkich adresów URL multimediów w manifeście wejściowym.|
|funkcje<br/><br/>Dodano w wersji 1.2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR").|false|Funkcja rozpoznawania mowy ma następujące klawisze ustawień:<br/><br/>Język:<br/>- Język naturalny, który ma być rozpoznawany w pliku multimedialnym.<br/>- angielski, hiszpański<br/><br/>PodpisFormaty:<br/>- oddzielona średnikami lista żądanych formatów podpisów wyjściowych (jeśli istnieje)<br/>- ttml;webvtt<br/><br/><br/>GenerateKeywords:<br/>- Flaga logiczna określająca, czy plik XML słowa kluczowego jest wymagany.<br/>- To prawda; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Przykład XML konfiguracji indeksatora multimediów azure

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
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Następne kroki

Zobacz [Indeksowanie plików multimedialnych za pomocą programu Azure Media Indexer](media-services-index-content.md).

