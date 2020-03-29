---
title: Jak używać znaczników dekoracji do wyróżniania tekstu - Interfejs API wyszukiwania w sieci Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać dekoracji tekstu i wyróżniania w wynikach wyszukiwania za pomocą interfejsu API wyszukiwania w sieci Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854007"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Wyróżnianie tekstu za pomocą znaczników dekoracyjnych

Bing obsługuje wyróżnianie trafień, które oznacza terminy zapytania (lub inne terminy, które Bing uważa za istotne) w ciągach wyświetlania niektórych odpowiedzi. Na przykład wynik strony sieci `name` `displayUrl`Web `snippet` , a pola mogą zawierać zaznaczone terminy kwerendy. 

Domyślnie Bing nie zawiera znaczników wyróżniania w ciągach wyświetlania. Aby włączyć znaczniki, `textDecorations` dołącz parametr zapytania do żądania `true`i ustaw go na .

## <a name="hit-highlighting-example"></a>Trafienie w przykładzie podświetlania

W poniższym przykładzie `Sailing Dinghy`przedstawiono wynik sieci Web dla . Bing oznaczał początek i koniec terminu zapytania przy użyciu znaków Unicode E000 i E001.
  
![Podświetlanie trafień](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Przed wyświetleniem wyniku w interfejsie użytkownika zastąp znaki Unicode znakami, które są odpowiednie dla formatu wyświetlania.

## <a name="marker-formatting"></a>Formatowanie znaczników

Bing umożliwia używanie znaków Unicode lub znaczników HTML jako znaczników. Aby określić, których znaczników użyć, dołącz parametr [textFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) query: 

| Wartość             | Znacznik                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Znaki Unicode (domyślnie) |
| `textFormat=HTML` | Znaki HTML              |

## <a name="additional-text-decorations"></a>Dodatkowe dekoracje tekstowe

Bing może zwrócić kilka różnych dekoracji tekstowych. Na przykład `Computation` odpowiedź może zawierać znaczniki indeksu `log(2)` dolnego dla terminu zapytania w `expression` polu.

![znaczniki obliczeniowe](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Jeśli wniosek nie określił dekoracji, `expression` pole `log10(2)`będzie zawierać . 

Jeśli `textDecorations` `true`tak, Bing może zawierać następujące znaczniki w ciągach wyświetlania odpowiedzi. Jeśli nie ma równoważnego znacznika HTML, komórka tabeli jest pusta.

|Unicode|HTML|Opis
|-|-|-
|U+E000|\<b>|Oznacza początek terminu kwerendy (wyróżnianie trafień)
|U+E001|\</b>|Oznacza koniec terminu kwerendy
|U+E002|\<i>|Oznacza początek kursywy zawartości 
|U+E003|\</i>|Oznacza koniec kursywy zawartości
|U+E004|\<br/>|Oznacza podział wiersza
|U+E005||Oznacza początek numeru telefonu
|U+E006||Oznacza koniec numeru telefonu
|U+E007||Oznacza początek adresu
|U+E008||Oznacza koniec adresu
|U+E009|\&nbsp;|Oznacza przestrzeń nierozłamającą
|U+E00C|\<silne>|Oznacza początek pogrubienia
|U+E00D|\</silny>|Oznacza koniec pogrubienia
|U+E00E||Oznacza początek treści, której tło powinno być jaśniejsze niż otaczające je tło
|U+E00F||Oznacza koniec treści, której tło powinno być jaśniejsze niż otaczające je tło
|U+E010||Oznacza początek treści, której tło powinno być ciemniejsze niż otaczające je tło
|U+E011||Oznacza koniec treści, której tło powinno być ciemniejsze niż otaczające je tło
|U+E012|\<del>|Oznacza początek treści, które powinny być
|U+E013|\</del>|Oznacza koniec treści, które powinny zostać
|U+E016|\<> sub|Oznacza początek zawartości indeksu dolnego
|U+E017|\</sub>|Oznacza koniec zawartości indeksu dolnego
|U+E018|\<> sup|Oznacza początek zawartości indeksu górnego
|U+E019|\</sup>|Oznacza koniec zawartości indeksu górnego

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API wyszukiwania w sieci Web Bing?](overview.md) 
* [Zmiana rozmiaru i przycinanie miniatur](resize-and-crop-thumbnails.md)