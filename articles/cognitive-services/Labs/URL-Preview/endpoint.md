---
title: Projekt punkt końcowy adres URL podglądu - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Podsumowanie punktu końcowego adresu URL podglądu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ddd53aa49db01d7a6db397eb285d0854edc59388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348616"
---
# <a name="project-url-preview-endpoint"></a>Punkt końcowy adres URL podglądu projektu

Interfejs API w wersji zapoznawczej adres URL zawiera jeden punkt końcowy.

## <a name="endpoint"></a>Endpoint
Aby uzyskać adres URL w wersji zapoznawczej, Wyślij żądanie do następujący punkt końcowy. Użyj nagłówków i parametry adresu URL dla innych specyfikacji.

GET:
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>Parametry zapytania
|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|Q|Adres URL do podglądu|Ciąg |Yes|
|bezpieczne wyszukiwanie|Zawartość dla dorosłych niedozwolony lub pirackich zawartość, jest zablokowany z kodem błędu 400 i *isFamilyFriendly* flagi nie są zwracane. <p>Treści dla dorosłych prawnych poniżej jest to zachowanie. Zwraca wartość Kod stanu 200 i *isFamilyFriendly* flaga jest ustawiona na wartość false.<ul><li>bezpieczne wyszukiwanie = strict: tytuł, opis i adres URL obrazu nie zostaną zwrócone.</li><li>bezpieczne wyszukiwanie = umiarkowany; Pobierz tytuł, adres URL i opis, ale nie opis obrazu.</li><li>bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie odpowiedzi obiektów/elementy — tytuł, adres URL, opis i obraz.</li></ul> |Ciąg|Nie jest wymagane. </br> Domyślnie bezpieczne wyszukiwanie = strict.| 

## <a name="response-object"></a>Obiekt odpowiedzi

Odpowiedź zawiera nagłówki HTTP i obiekt strony sieci Web z atrybutami, jak pokazano w poniższym przykładzie: `name`, `url`, `description`, `isFamilyFriendly`, i `primaryImageOfPage`.

````
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

````

## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](csharp.md)
- [Szybki Start Java](java-quickstart.md)
- [JavaScript — Szybki Start](javascript.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)
