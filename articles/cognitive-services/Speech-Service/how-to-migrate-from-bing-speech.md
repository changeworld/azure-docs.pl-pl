---
title: Migracja z usługi mowy do mowy usługi Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację z istniejącej subskrypcji mowy Bing do usługi Mowy z usług Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: nitinme
ms.openlocfilehash: b95e16f2d8257bfffcaf2524fe7f8ce6be565689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366590"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migracja z mowy bing do usługi mowy

Ten artykuł służy do migracji aplikacji z interfejsu API mowy Bing do usługi mowy.

W tym artykule opisano różnice między interfejsami API mowy Bing a usługą mowy i sugeruje strategie migracji aplikacji. Klucz subskrypcji interfejsu API mowy Bing nie będzie działać z usługą Mowy; potrzebna jest nowa subskrypcja usługi mowy.

Pojedynczy klucz subskrypcji usługi mowy zapewnia dostęp do następujących funkcji. Każda z tych funkcji jest mierzona oddzielnie, dlatego opłaty są naliczane tylko za funkcje, których używasz.

* [Zamiana mowy na tekst](speech-to-text.md)
* [Niestandardowa zamiana mowy na tekst](https://cris.ai)
* [Zamiana tekstu na mowę](text-to-speech.md)
* [Niestandardowe głosy funkcji zamiany tekstu na mowę](how-to-customize-voice-font.md)
* [Tłumaczenie mowy](speech-translation.md) (nie obejmuje funkcji [Tłumaczenie tekstu](../translator/translator-info-overview.md))

[SDK mowy](speech-sdk.md) jest funkcjonalne zastąpienie bibliotek klienta mowy Bing, ale używa innego interfejsu API.

## <a name="comparison-of-features"></a>Porównanie funkcji

Usługa mowy jest w dużej mierze podobna do mowy Bing, z następującymi różnicami.

| Funkcja | Rozpoznawanie mowy Bing | Usługa rozpoznawania mowy | Szczegóły |
|--|--|--|--|
| Zestaw SDK języka C# | :heavy_check_mark: | :heavy_check_mark: | Usługa mowy obsługuje system Windows 10, uniwersalną platformę systemu Windows (UWP) i .NET Standard 2.0. |
| K.P.C. | :heavy_minus_sign: | :heavy_check_mark: | Usługa mowy obsługuje systemy Windows i Linux. |
| Zestaw SDK Java | :heavy_check_mark: | :heavy_check_mark: | Usługa mowy obsługuje urządzenia z systemem Android i speech. |
| Ciągłe rozpoznawanie mowy | 10 minut | Nieograniczony (z SDK) | Protokoły WebSockets usługi mowy i mowy Bing obsługują do 10 minut na wywołanie. Jednak SDK mowy automatycznie łączy się ponownie po przełączeniu limitu czasu lub rozłączyć. |
| Wyniki częściowe lub okresowe | :heavy_check_mark: | :heavy_check_mark: | Z protokołem WebSockets lub SDK. |
| Niestandardowe modele mowy | :heavy_check_mark: | :heavy_check_mark: | Bing Speech wymaga oddzielnej subskrypcji mowy niestandardowej. |
| Niestandardowe czcionki głosowe | :heavy_check_mark: | :heavy_check_mark: | Bing Speech wymaga oddzielnej subskrypcji niestandardowego głosu. |
| Głosy 24 kHz | :heavy_minus_sign: | :heavy_check_mark: |
| Rozpoznawanie intencji mowy | Wymaga oddzielnego wywołania interfejsu API usługi LUIS | Zintegrowany (z SDK) | Klucz usługi LUIS można użyć z usługą mowy. |
| Proste rozpoznawanie intencji | :heavy_minus_sign: | :heavy_check_mark: |
| Transkrypcja wsadowa długich plików audio | :heavy_minus_sign: | :heavy_check_mark: |
| Tryb rozpoznawania | Instrukcja za pomocą identyfikatora URI punktu końcowego | Automatyczny | Tryb rozpoznawania nie jest dostępny w usłudze Mowy. |
| Lokalizacja punktu końcowego | Globalny | Regionalny | Regionalne punkty końcowe zwiększają opóźnienie. |
| Interfejsy API REST | :heavy_check_mark: | :heavy_check_mark: | Interfejsy API REST usługi mowy są zgodne z Bing Speech (inny punkt końcowy). Interfejsy API REST obsługują zamianę tekstu na mowę i ograniczone funkcje zamiany mowy na tekst. |
| Protokoły WebSockets | :heavy_check_mark: | :heavy_check_mark: | Usługa mowy WebSockets interfejs API jest zgodny z Bing Speech (inny punkt końcowy). Migracji do SDK mowy, jeśli to możliwe, aby uprościć kod. |
| Wywołania interfejsu API usługi do usługi | :heavy_check_mark: | :heavy_minus_sign: | Dostępne w usłudze Bing Speech za pośrednictwem biblioteki usług języka C#. |
| SDK typu open source | :heavy_check_mark: | :heavy_minus_sign: |

Usługa Mowy używa modelu cenowego opartego na czasie (a nie modelu opartego na transakcjach). Szczegółowe informacje można znaleźć w [cenniku usługi mowy.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="migration-strategies"></a>Strategie migracji

Jeśli ty lub Twoja organizacja masz aplikacje w rozwoju lub produkcji, które używają interfejsu API mowy Bing, należy zaktualizować je do korzystania z usługi mowy tak szybko, jak to możliwe. Zobacz [dokumentację usługi mowy,](index.yml) aby uzyskać dostępne zestawy SDK, przykłady kodu i samouczki.

Interfejsy API REST usługi mowy są zgodne z [interfejsami](rest-apis.md) API mowy Bing. Jeśli obecnie używasz interfejsów API odpoczynku mowy Bing, musisz tylko zmienić punkt końcowy REST i przełączyć się na klucz subskrypcji usługi mowy.

Protokół WebSockets usługi mowy są również zgodne z protokołami używanymi przez usługę Bing Speech. Zaleca się, aby w przypadku nowego rozwoju używać SDK mowy, a nie WebSockets. Dobrym pomysłem jest również migracja istniejącego kodu do sdk. Jednak podobnie jak w przypadku interfejsów API REST, istniejący kod, który używa mowy Bing za pośrednictwem websockets wymaga tylko zmiany punktu końcowego i zaktualizowany klucz.

Jeśli używasz biblioteki klienta mowy Bing dla określonego języka programowania, migracja do [SDK mowy](speech-sdk.md) wymaga zmian w aplikacji, ponieważ interfejs API jest inny. Zestaw SDK mowy może uprościć kod, a jednocześnie daje dostęp do nowych funkcji. Pakiet Speech SDK jest dostępny w wielu różnych językach programowania. Interfejsy API na wszystkich platformach są podobne, co ułatwia tworzenie wielu platform.

Usługa Mowy nie oferuje globalnego punktu końcowego. Określ, czy aplikacja działa wydajnie, gdy używa jednego regionalnego punktu końcowego dla całego ruchu. Jeśli nie, użyj geolokalizacji, aby określić najbardziej efektywny punkt końcowy. Potrzebujesz oddzielnej subskrypcji usługi mowy w każdym regionie, którego używasz.

Jeśli aplikacja używa połączeń długotrwałych i nie można użyć dostępnego sdk, można użyć połączenia WebSockets. Zarządzaj 10-minutowym limitem czasu, łącząc się ponownie w odpowiednim czasie.

Aby rozpocząć korzystanie z sdk mowy:

1. Pobierz [sdk mowy](speech-sdk.md).
1. Pracuj nad [przewodnikami szybki startowymi](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) usługi mowy i [samouczkami](how-to-recognize-intents-from-speech-csharp.md). Również spojrzeć na [przykłady kodu,](samples.md) aby uzyskać doświadczenie z nowych interfejsów API.
1. Zaktualizuj aplikację, aby korzystać z usługi Mowy.

## <a name="support"></a>Pomoc techniczna

Klienci Bing Speech powinni skontaktować się z działem obsługi klienta, otwierając [bilet pomocy technicznej.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Możesz również skontaktować się z nami, jeśli twoja pomoc techniczna wymaga [planu pomocy technicznej.](https://azure.microsoft.com/support/plans/)

Aby uzyskać pomoc techniczną usługi mowy, SDK i interfejsu API, odwiedź [stronę pomocy technicznej](support.md)usługi mowy .

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj usługę mowy za darmo](get-started.md)
* [Szybki start: rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu uniwersalnego przy użyciu sdk mowy](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zobacz też
* [Informacje o wydaniu usługi mowy](releasenotes.md)
* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi mowy i sdk mowy](speech-sdk.md#get-the-sdk)
