---
title: "Zadanie ustawień dla indeksatora multimediów Azure"
description: "Ten temat zawiera omówienie zadań ustawień dla usługi Azure Media indeksatora."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: ae6c4da189cd6637b4e1fa9274473b62f6664e51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="task-preset-for-azure-media-indexer"></a>Zadanie ustawień dla indeksatora multimediów Azure

Azure Media indeksatora jest procesor multimediów, którego używasz do wykonywania następujących zadań: udostępnianie plików multimedialnych i treści można wyszukiwać, generowanie zamkniętego śledzi podpisów i słów kluczowych, indeksu pliki zasobów, które są częścią zawartości.

W tym temacie opisano zadania ustawienia wstępnego należy przekazać do indeksowania zadania. Aby uzyskać pełny przykład, zobacz [indeksowania plików multimedialnych na pliki z usługi Azure Media indeksatora](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Plik XML konfiguracji indeksatora multimediów Azure

W poniższej tabeli opisano elementy i atrybuty XML konfiguracji.

|Nazwa|Wymagane|Opis|
|---|---|---|
|Dane wejściowe|Wartość true|Pliki zasobów, które mają być indeksowane.<br/>Indeksator multimediów Azure obsługuje następujące formaty plików nośnika: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Należy określić nazwę pliku (s), w **nazwa** lub **listy** atrybutu **wejściowych** elementu (jak pokazano poniżej). Jeśli nie określisz, który plik zasobów do indeksu, jest pobierany plik podstawowy. Jeśli plik podstawowy zasobów nie jest ustawiona, pierwszy plik wejściowy zasobów jest indeksowany.<br/><br/>Aby jawnie określić nazwę pliku zasobów, należy wykonać:<br/>```<input name="TestFile.wmv" />```<br/><br/>Można również indeksu wiele zasobów plików jednocześnie (maksymalnie 10). W tym celu:<br/>-Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie .lst.<br/>-Dodaj listę wszystkich nazw plików zasobów w zawartości wejściowej do tego pliku manifestu.<br/>-Dodaj plik thanifest (przekazywanie) do elementu zawartości.<br/>— Określ nazwę pliku manifestu w atrybucie lista danych wejściowych.<br/>```<input list="input.lst">```<br/><br/>**Uwaga:** więcej niż 10 pliki dodane do pliku manifestu, zadania indeksowania zakończy się niepowodzeniem z kodem błędu 2006 roku.|
|Metadane|wartość false|Metadane dla plików określonym zasobie.<br/>```<metadata key="..." value="..." />```<br/><br/>Można podać wartości dla wstępnie zdefiniowanych kluczy. <br/><br/>Obecnie są obsługiwane następujące klucze:<br/><br/>**Tytuł** i **opis** — używany do aktualizacji modelu języka aby poprawić dokładność rozpoznawania mowy.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**Nazwa użytkownika** i **hasło** — używana do uwierzytelniania podczas pobierania plików internetowych za pośrednictwem protokołu http lub https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Wartości nazwy użytkownika i hasło mają zastosowanie do wszystkich nośników adresów URL w manifeście wejściowego.|
|danych<br/><br/>Dodany w wersji 1.2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR").|wartość false|Funkcja rozpoznawania mowy ma następujące klucze ustawień:<br/><br/>Język:<br/>Języka naturalnego zostać rozpoznany w pliku multimedialnego.<br/>-Angielski, hiszpański<br/><br/>CaptionFormats:<br/>-Rozdzielana średnikami lista podpis żądanego wyniku formatuje (jeśli istnieje)<br/>-ttml; sami; webvtt<br/><br/><br/>GenerateAIB:<br/>-Określenie, czy plik AIB jest wymagany (na potrzeby używania z programu SQL Server i klienta IFilter indeksatora) Flaga wartości logicznej. Aby uzyskać więcej informacji zobacz przy użyciu plików AIB za pomocą usługi Azure Media indeksatora i SQL Server.<br/>— Wartość PRAWDA; Wartość false<br/><br/>GenerateKeywords:<br/>-Określenie, czy wymagany jest plik XML — słowo kluczowe Flaga wartości logicznej.<br/>— Wartość PRAWDA; Wartość false.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Przykład XML konfiguracji w usłudze Azure indeksatora nośnika

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
  
## <a name="next-steps"></a>Następne kroki

Zobacz [indeksowania plików multimedialnych na pliki z usługi Azure Media indeksatora](media-services-index-content.md).

