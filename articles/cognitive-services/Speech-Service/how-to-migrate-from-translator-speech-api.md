---
title: Migrowanie z API mowy usługi Translator z usługą mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację aplikacji z interfejsu API tłumaczenia mowy do usług przetwarzania mowy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 1ed494cea1ccf8845a25a3ab49d3194cc6a55509
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "65785679"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrowanie z API mowy usługi Translator z usługą mowy

Skorzystaj z tego artykułu do migracji aplikacji z interfejsu API mowy usługi Translator firmy Microsoft do [usługa rozpoznawania mowy](index.yml). Ten przewodnik przedstawia różnice między interfejsu API tłumaczenia mowy i usługa rozpoznawania mowy i sugeruje Strategie migracji aplikacji.

> [!NOTE]
> Klucz interfejsu API tłumaczenia mowy subskrypcji nie zostanie zaakceptowane przez usługę rozpoznawania mowy. Należy utworzyć nową subskrypcję usług przetwarzania mowy.

## <a name="comparison-of-features"></a>Porównanie funkcji

| Cecha                                           | Interfejs API tłumaczenia mowy w usłudze Translator                                  | Usługi mowy | Szczegóły                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tłumaczenie tekstu                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tłumaczenie na mowę                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globalny punkt końcowy                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Globalny punkt końcowy nie ma możliwości usług przetwarzania mowy. Globalny punkt końcowy może automatycznie kierować ruch do najbliższego regionalnych punktu końcowego, zmniejsza opóźnienie w aplikacji.                                                    |
| Regionalne punktów końcowych                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limit czasu połączenia                             | 90 minut                                               | Bez ograniczeń za pomocą zestawu SDK. 10 minut z połączeniem Websocket.                                                                                                                                                                                                                                                                                   |
| Klucz uwierzytelniania w nagłówku                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Wiele języków przetłumaczyć w pojedyncze żądanie | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Dostępne są zestawy SDK                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Zobacz [dokumentacji usług przetwarzania mowy](index.yml) dla dostępnych zestawów SDK.                                                                                                                                                    |
| Gniazda Websocket połączeń                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Języki interfejsu API                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Usługi mowy obsługują tego samego zakresu opisanego w językach [Dokumentacja języków interfejsu API usługi Translator](../translator-speech/languages-reference.md) artykułu. |
| Filtr wulgaryzmów i znacznika                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM jako dane wejściowe                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Inne typy plików jako dane wejściowe                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Wyniki częściowe                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informacje o chronometrażu                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Identyfikator korelacji                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Niestandardowe modele mowy                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Usługi mowy oferują niestandardowe modele mowy dopasowane umożliwiają dostosowywanie rozpoznawania mowy do słownictwa unikatowy w Twojej organizacji.                                                                                                                                           |
| Modele tłumaczenia niestandardowych                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Subskrybowanie interfejsu API tłumaczenia tekstu firmy Microsoft pozwala na użycie [niestandardowe w usłudze Translator](https://www.microsoft.com/translator/business/customization/) do swoich danych na użytek bardziej precyzyjne tłumaczenia.                                                 |

## <a name="migration-strategies"></a>Strategie migracji

Jeśli Ty lub Twoja organizacja ma aplikacje w rozwoju i produkcji, które używają interfejsu API tłumaczenia mowy, należy zaktualizować je do korzystania z usługi rozpoznawania mowy. Zobacz [usługa rozpoznawania mowy](index.yml) dokumentacji dostępne zestawy SDK, przykłady kodu i samouczkom. Podczas migracji, należy wziąć pod uwagę następujące czynności:

* Globalny punkt końcowy nie ma możliwości usług przetwarzania mowy. Określ, jeśli aplikacja działa wydajnie po używa pojedynczego punktu końcowego regionalne dla wszystkich jego ruchu. W przeciwnym razie użyj geolokacji, aby określić najbardziej efektywny sposób punktu końcowego.

* Jeśli aplikacja używa długotrwałe połączeń i nie można używać dostępnych zestawów SDK, można użyć połączenia gniazda Websocket. Aby zarządzać limit czasu 10 minut, ponowne łączenie w odpowiednim czasie.

* Jeśli aplikacja korzysta z interfejsu API tłumaczenia tekstu i interfejsu API tłumaczenia mowy, aby umożliwić modele tłumaczenia niestandardowych, można dodać identyfikatorów kategorii bezpośrednio przy użyciu usługi mowy.

* W przeciwieństwie do interfejsu API tłumaczenia mowy usług przetwarzania mowy, można wykonać tłumaczenia na języki pojedyncze żądanie.

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj bezpłatnie usługi mowy](get-started.md)
* [Szybki start: Rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu Windows przy użyciu zestawu SDK rozpoznawania mowy](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usług przetwarzania mowy i zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
