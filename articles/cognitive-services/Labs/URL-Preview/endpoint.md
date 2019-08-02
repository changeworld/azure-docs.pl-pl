---
title: Punkt końcowy podglądu adresu URL projektu
titlesuffix: Azure Cognitive Services
description: Podsumowanie punktu końcowego wersji zapoznawczej adresu URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 3ef5ebd4ec88deac8c49430f36956d3711c8c535
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706953"
---
# <a name="project-url-preview-endpoint"></a>Punkt końcowy podglądu adresu URL projektu

Interfejs API podglądu adresu URL zawiera jeden punkt końcowy.

## <a name="endpoint"></a>Endpoint
Aby uzyskać podgląd adresu URL, Wyślij żądanie do poniższego punktu końcowego. Użyj nagłówków i parametrów adresu URL dla innych specyfikacji.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parametry zapytania
|Name (Nazwa)|Value|Type|Wymagane|  
|----------|-----------|----------|--------------|  
|q|Adres URL do podglądu|String |Tak|
|Bezpieczne wyszukiwanie|Niedozwolona zawartość dla dorosłych lub zawartość pirackia jest blokowana z kodem błędu 400 i flaga *isFamilyFriendly* nie jest zwracana. <p>W przypadku legalnej zawartości dla dorosłych poniżej przedstawiono zachowanie. Kod stanu zwraca 200, a flaga *isFamilyFriendly* jest ustawiona na false.<ul><li>Bezpieczne wyszukiwanie = Strict: Tytuł, opis, adres URL i obraz nie zostaną zwrócone.</li><li>Bezpieczne wyszukiwanie = umiarkowany; Pobierz tytuł, adres URL i opis, ale nie obraz opisowy.</li><li>Bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie obiekty/elementy odpowiedzi — tytuł, adres URL, opis i obraz.</li></ul> |Ciąg|Nie jest wymagane. </br> Wartość domyślna to bezpieczne wyszukiwanie = Strict.| 

## <a name="response-object"></a>Obiekt odpowiedzi

Odpowiedź obejmuje nagłówki HTTP i obiekt strony sieci Web z atrybutami, jak pokazano w poniższym `name`przykładzie `url`: `description`, `isFamilyFriendly`,, `primaryImageOfPage`i.

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

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](csharp.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka JavaScript](javascript.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
