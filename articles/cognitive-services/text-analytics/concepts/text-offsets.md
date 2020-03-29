---
title: Przesunięcie tekstu w interfejsie API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o przesunięciach spowodowanych przez kodowanie wielojęzyczne i emotikony.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219237"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Przesunięcie tekstu w danych wyjściowych interfejsu API analizy tekstu

Obsługa wielojęzyczna i emoji doprowadziła do kodowania Unicode, które używają więcej niż jednego [punktu kodu](https://wikipedia.org/wiki/Code_point) do reprezentowania pojedynczego wyświetlanego znaku, zwanego grafem. Na przykład emotikony, takie 👍 jak 🌷 i mogą używać kilku znaków do komponowania kształtu z dodatkowymi znakami dla atrybutów wizualnych, takich jak odcień karnacji. Podobnie słowo `अनुच्छेद` hindi jest zakodowane jako pięć liter i trzy znaki łączące.

Ze względu na różne długości możliwych kodowania wielojęzycznego i emoji interfejs API analizy tekstu może zwracać przesunięcia w odpowiedzi.

## <a name="offsets-in-the-api-response"></a>Przesunięcia w odpowiedzi interfejsu API. 

Za każdym razem, gdy są zwracane przesunięcia odpowiedzi interfejsu API, takich jak [rozpoznawanie nazwanych jednostek](../how-tos/text-analytics-how-to-entity-linking.md) lub [analiza tonacji,](../how-tos/text-analytics-how-to-sentiment-analysis.md)należy pamiętać o następujących:

* Elementy w odpowiedzi mogą być specyficzne dla punktu końcowego, który został wywołany. 
* Ładunki HTTP POST/GET są kodowane w [utf-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), które mogą lub nie mogą być domyślnym kodowaniem znaków w kompilatorze po stronie klienta lub systemie operacyjnym.
* Odsunięcia odnoszą się do liczby grafemów na podstawie standardu [Unicode 8.0.0,](https://unicode.org/versions/Unicode8.0.0) a nie liczby znaków.

## <a name="extracting-substrings-from-text-with-offsets"></a>Wyodrębnianie podciągów z tekstu z odsunięciami

Odsunięcia mogą powodować problemy podczas korzystania z metod podciągów opartych na znakach, na przykład metody [.NET substring().](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Jednym z problemów jest to, że przesunięcie może spowodować, że metoda podciągowa zakończy się w środku wieloznakowego kodowania grafemu zamiast końca.

W .NET należy rozważyć użycie [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) klasy, która umożliwia pracę z ciągiem jako serii elementów tekstowych, a nie poszczególnych obiektów znaków. W preferowanym środowisku oprogramowania można również wyszukać biblioteki rozdzielacza grafeme. 

Interfejs API analizy tekstu zwraca te elementy tekstowe, jak również dla wygody.

## <a name="see-also"></a>Zobacz też

* [Analiza tekstu — omówienie](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykrywanie języka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
