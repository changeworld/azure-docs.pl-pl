---
title: Podgląd adresu URL punktu końcowego projektu
titlesuffix: Azure Cognitive Services
description: Podsumowanie Podgląd adresu URL punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 985e08a9622e08d2a4b52dae996952cd6a7e7ad4
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207458"
---
# <a name="project-url-preview-endpoint"></a>Podgląd adresu URL punktu końcowego projektu

Adres URL interfejsu API (wersja zapoznawcza) zawiera jeden punkt końcowy.

## <a name="endpoint"></a>Endpoint
Aby uzyskać adres URL w wersji zapoznawczej, Wyślij żądanie do następujący punkt końcowy. Na użytek nagłówki i parametry adresu URL inne specyfikacje.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parametry zapytania
|Name|Value|Type|Wymagany|  
|----------|-----------|----------|--------------|  
|q|Adres URL, aby wyświetlić podgląd|Ciąg |Yes|
|bezpieczne wyszukiwanie|Nielegalnych treści dla dorosłych lub pirackich zawartości jest zablokowany kod błędu: 400 i *isFamilyFriendly* flaga nie jest zwracana. <p>Treści dla dorosłych prawnych poniżej jest to zachowanie. Zwraca wartość Kod stanu 200, a *isFamilyFriendly* flaga jest ustawiona na wartość false.<ul><li>bezpieczne wyszukiwanie = ograniczeniami: Tytuł i opis, adres URL obrazu nie zostaną zwrócone.</li><li>bezpieczne wyszukiwanie = średni; Uzyskaj tytuł, adres URL i opis, ale nie opisowy obraz.</li><li>bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie odpowiedzi obiektów/elementy — tytuł, adres URL, opis i obraz.</li></ul> |Ciąg|Nie jest wymagane. </br> Wartość domyślna to bezpieczne wyszukiwanie = strict.| 

## <a name="response-object"></a>Obiekt odpowiedzi

Odpowiedź zawiera nagłówki HTTP i obiekt strony sieci Web za pomocą atrybutów, jak pokazano w poniższym przykładzie: `name`, `url`, `description`, `isFamilyFriendly`, i `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka C#](csharp.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka JavaScript](javascript.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
