---
title: Podgląd adresu URL punktu końcowego projektu
titlesuffix: Azure Cognitive Services
description: Podsumowanie Podgląd adresu URL punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 43254db734a48f3e7aaa5a26a7fbf3981c9e9d87
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592869"
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
|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|q|Adres URL, aby wyświetlić podgląd|String |Yes|
|bezpieczne wyszukiwanie|Nielegalnych treści dla dorosłych lub pirackich zawartości jest zablokowany kod błędu: 400 i *isFamilyFriendly* flaga nie jest zwracana. <p>Treści dla dorosłych prawnych poniżej jest to zachowanie. Zwraca wartość Kod stanu 200, a *isFamilyFriendly* flaga jest ustawiona na wartość false.<ul><li>bezpieczne wyszukiwanie = ograniczeniami: Tytuł i opis, adres URL obrazu nie zostaną zwrócone.</li><li>bezpieczne wyszukiwanie = średni; Uzyskaj tytuł, adres URL i opis, ale nie opisowy obraz.</li><li>bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie odpowiedzi obiektów/elementy — tytuł, adres URL, opis i obraz.</li></ul> |String|Nie jest wymagane. </br> Wartość domyślna to bezpieczne wyszukiwanie = strict.| 

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
