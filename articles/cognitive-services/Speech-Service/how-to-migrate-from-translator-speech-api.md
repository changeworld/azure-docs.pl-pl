---
title: Migrowanie z interfejsu API mowy tłumacza do usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację aplikacji z interfejsu API mowy tłumacza do usługi mowy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560902"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrowanie z interfejsu API mowy tłumacza do usługi mowy

Ten artykuł służy do migracji aplikacji z interfejsu API mowy usługi Microsoft Translator do [usługi Mowy](index.yml). W tym przewodniku opisano różnice między interfejsem API mowy tłumacza a usługą mowy i sugeruje strategie migracji aplikacji.

> [!NOTE]
> Klucz subskrypcji interfejsu API mowy tłumacza nie będzie akceptowany przez usługę mowy. Musisz utworzyć nową subskrypcję usługi mowy.

## <a name="comparison-of-features"></a>Porównanie funkcji

| Funkcja                                           | Interfejs API tłumaczenia mowy w usłudze Translator                                  | Usługa rozpoznawania mowy | Szczegóły                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tłumaczenie na tekst                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tłumaczenie na mowę                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globalny punkt końcowy                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Usługa Mowy nie oferuje globalnego punktu końcowego. Globalny punkt końcowy może automatycznie kierować ruch do najbliższego regionalnego punktu końcowego, zmniejszając opóźnienie w aplikacji.                                                    |
| Regionalne punkty końcowe                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limit czasu połączenia                             | 90 min.                                               | Nieograniczony z SDK. 10 minut z połączeniem WebSockets.                                                                                                                                                                                                                                                                                   |
| Klucz emocie w nagłówku                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Wiele języków przetłumaczonych na jedno żądanie | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Dostępne sdk                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Zobacz [dokumentację usługi mowy](index.yml) dla dostępnych sdków.                                                                                                                                                    |
| Połączenia WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Interfejs API języków                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Usługa mowy obsługuje ten sam zakres języków opisanych w [artykule odwołania do języków interfejsu API translatora.](../translator-speech/languages-reference.md) |
| Filtr wulgaryzmów i znacznik                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM jako wejście                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Inne typy plików jako dane wejściowe                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Częściowe wyniki                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informacje o chronometrażu                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| Identyfikator korelacji                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Niestandardowe modele mowy                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Usługa Mowy oferuje niestandardowe modele mowy, które umożliwiają dostosowanie rozpoznawania mowy do unikatowego słownictwa w organizacji.                                                                                                                                           |
| Niestandardowe modele tłumaczenia                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Subskrybowanie interfejsu API tłumaczenia tekstu firmy Microsoft umożliwia używanie [translatora niestandardowego](https://www.microsoft.com/translator/business/customization/) do używania własnych danych w celu dokładniejszego tłumaczenia.                                                 |

## <a name="migration-strategies"></a>Strategie migracji

Jeśli ty lub twoja organizacja masz aplikacje w rozwoju lub produkcji, które używają interfejsu API mowy translatora, należy zaktualizować je do korzystania z usługi mowy. Zobacz dokumentację [usługi mowy,](index.yml) aby uzyskać dostępne zestawy SDK, przykłady kodu i samouczki. Podczas migracji należy wziąć pod uwagę następujące kwestie:

* Usługa Mowy nie oferuje globalnego punktu końcowego. Określ, czy aplikacja działa wydajnie, gdy używa jednego regionalnego punktu końcowego dla całego ruchu. Jeśli nie, użyj geolokalizacji, aby określić najbardziej efektywny punkt końcowy.

* Jeśli aplikacja używa połączeń długotrwałych i nie można używać dostępnych zestawy SDK, można użyć połączenia WebSockets. Zarządzaj 10-minutowym limitem czasu, łącząc się ponownie w odpowiednim czasie.

* Jeśli aplikacja używa interfejsu API tekstu tłumacza i interfejsu API mowy tłumacza, aby włączyć niestandardowe modele tłumaczenia, można dodać identyfikatory kategorii bezpośrednio przy użyciu usługi mowy.

* W przeciwieństwie do interfejsu API mowy tłumaczenia usługa mowy można wykonać tłumaczenia na wiele języków w jednym żądaniu.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj usługę mowy za darmo](get-started.md)
* [Szybki start: rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu uniwersalnego przy użyciu sdk mowy](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zobacz też

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi mowy i sdk mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
