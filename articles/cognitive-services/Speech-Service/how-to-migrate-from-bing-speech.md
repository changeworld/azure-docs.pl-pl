---
title: Migracja z rozpoznawania mowy Bing do usług przetwarzania mowy platformy Azure
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację z istniejącej subskrypcji rozpoznawania mowy Bing do usług mowy platformy Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653720"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migracja z rozpoznawania mowy Bing do usługi rozpoznawania mowy

W tym artykule należy użyć do migracji aplikacji z modułu Speech API Bing do usługi rozpoznawania mowy.

W tym artykule opisano różnice między interfejsy API rozpoznawania mowy Bing i usług przetwarzania mowy i sugeruje Strategie migracji aplikacji. Klucz interfejsu API rozpoznawania mowy Bing subskrypcji nie będzie działać z usługą mowy; należy nowej subskrypcji usług przetwarzania mowy.

Jeden klucz subskrypcji usług przetwarzania mowy udziela dostępu do następujących funkcji. Każda z tych funkcji jest mierzona oddzielnie, dlatego opłaty są naliczane tylko za funkcje, których używasz.

* [Zamiana mowy na tekst](speech-to-text.md)
* [Niestandardowa zamiana mowy na tekst](https://cris.ai)
* [Zamiana tekstu na mowę](text-to-speech.md)
* [Niestandardowe głosy funkcji zamiany tekstu na mowę](how-to-customize-voice-font.md)
* [Tłumaczenie mowy](speech-translation.md) (nie obejmuje funkcji [Tłumaczenie tekstu](../translator/translator-info-overview.md))

[Zestaw SDK rozpoznawania mowy](speech-sdk.md) funkcjonalności zastępuje biblioteki klienta mowy Bing, ale używa innego interfejsu API.

## <a name="comparison-of-features"></a>Porównanie funkcji

Usług przetwarzania mowy są w przeważającej mierze podobny do rozpoznawania mowy Bing, z następującymi różnicami.

Cecha | Rozpoznawanie mowy Bing | Usługi mowy | Szczegóły
-|-|-|-
ZESTAW SDK JĘZYKA C++ | : heavy_minus_sign: | :heavy_check_mark: | Usługi mowy obsługują, Windows i Linux.
Zestaw SDK Java | :heavy_check_mark: | :heavy_check_mark: | Obsługuje usługi rozpoznawania mowy, Android i zestaw Speech Devices.
Zestaw SDK języka C# | :heavy_check_mark: | :heavy_check_mark: | Usługi mowy obsługują, Windows 10, Windows platformy Uniwersalnej i .NET Standard 2.0.
Rozpoznawanie mowy | 10 minut | Bez ograniczeń (przy użyciu zestawu SDK) | Protokoły WebSockets usług mowy i rozpoznawania mowy Bing obsługuje do 10 minut na wywołanie. Jednak zestaw SDK rozpoznawania mowy automatycznie ponownie nawiąże połączenie przekroczenia limitu czasu lub odłączyć.
Tymczasowy lub częściowe wyniki | :heavy_check_mark: | :heavy_check_mark: | Za pomocą protokołu Websocket lub zestawu SDK.
Niestandardowe modele mowy | :heavy_check_mark: | :heavy_check_mark: | Rozpoznawanie mowy Bing wymaga oddzielnej subskrypcji Custom Speech.
Czcionki głosowe niestandardowe | :heavy_check_mark: | :heavy_check_mark: | Rozpoznawanie mowy Bing wymaga oddzielnej subskrypcji Custom Voice.
Głosy 24 kHz | : heavy_minus_sign: | :heavy_check_mark:
Rozpoznawanie celu mowy | Wymaga wywołania interfejsu API usługi LUIS | Zintegrowane (przy użyciu zestawu SDK) |  Za pomocą klucza usługi LUIS z usługa mowy.
Proste rozpoznawanie intencji | : heavy_minus_sign: | :heavy_check_mark:
Batch transkrypcji pliki długo audio | : heavy_minus_sign: | :heavy_check_mark:
Tryb rozpoznawania | Ręcznie za pomocą identyfikatora URI punktu końcowego | Automatyczny | Tryb rozpoznawania nie jest dostępne w usłudze rozpoznawania mowy.
Lokalizacja punktu końcowego | Globalny | Regionalne | Punkty końcowe regionalnych Zmniejszaj opóźnienia.
Interfejsy API REST | :heavy_check_mark: | :heavy_check_mark: | Interfejsy API mowy usługi REST są zgodne z rozpoznawania mowy Bing (inny punkt końcowy). Interfejsy API REST obsługują funkcji zamiany mowy na tekst zamiany tekstu na mowę i ograniczony.
Protokoły WebSockets | :heavy_check_mark: | :heavy_check_mark: | Interfejs API mowy usługi WebSockets jest zgodny z rozpoznawania mowy Bing (inny punkt końcowy). Przeprowadź migrację do zestawu SDK rozpoznawania mowy, jeśli to możliwe, aby uprościć swój kod.
Wywołania interfejsu API do usługi | :heavy_check_mark: | : heavy_minus_sign: | Podana w rozpoznawania mowy Bing za pośrednictwem biblioteki usługi C#.
Zestaw SDK open source | :heavy_check_mark: | : heavy_minus_sign: |

Usługi mowy używają modelu cenowego opartego na czasie (zamiast transakcji modelu). Zobacz [cen usług przetwarzania mowy](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) Aby uzyskać szczegółowe informacje.

## <a name="migration-strategies"></a>Strategie migracji

Jeśli Ty lub Twoja organizacja ma aplikacje w rozwoju i produkcji, które korzysta z interfejsu API rozpoznawania mowy Bing, należy zaktualizować je do korzystania z usług mowy w możliwie najszybciej. Zobacz [dokumentacji usług przetwarzania mowy](index.yml) dostępnych zestawów SDK, przykłady kodu i samouczki.

Usługi mowy [interfejsów API REST](rest-apis.md) są zgodne z interfejsów API rozpoznawania mowy Bing. Jeśli używasz obecnie programu interfejsów API REST dla funkcji rozpoznawania mowy Bing, należy zmienić tylko punkt końcowy REST i przełącz się do usług przetwarzania mowy klucz subskrypcji.

Protokoły WebSockets usług mowy, są również zgodne ze stosowanymi przez rozpoznawania mowy Bing. Firma Microsoft zaleca w nowych wdrożeniach użycie zestawu SDK rozpoznawania mowy, a nie funkcji WebSockets. To dobry pomysł, aby migrować istniejący kod w zestawie SDK oraz. Jednak jako za pomocą interfejsów API REST istniejący kod, który używa rozpoznawania mowy Bing za pośrednictwem funkcji WebSockets wymaga tylko zmiana punktu końcowego i klucz zaktualizowane.

Jeśli używasz biblioteki klienta mowy Bing dla określonego języka programowania, migracja do [zestaw SDK rozpoznawania mowy](speech-sdk.md) wymaga wprowadzenia zmian w aplikacji, ponieważ różni się interfejsu API. Zestaw SDK rozpoznawania mowy, może uczynić kod prostsze, zapewniając również dostęp do nowych funkcji.

Obecnie zestaw SDK rozpoznawania mowy obsługuje C# (Windows 10, platformy uniwersalnej systemu Windows, platformy .NET Standard), Java (urządzenia z systemem Android i niestandardowych), Objective C (iOS), języka C++ (Windows i Linux) i języka JavaScript. Interfejsy API na wszystkich platformach są podobne, ułatwianie programowanie dla wielu platform.

Globalny punkt końcowy nie ma możliwości usług przetwarzania mowy. Określ, jeśli aplikacja działa wydajnie po używa pojedynczego punktu końcowego regionalne dla wszystkich jego ruchu. W przeciwnym razie użyj geolokacji, aby określić najbardziej efektywny sposób punktu końcowego. Konieczne jest oddzielną subskrypcję usług mowy w każdym z regionów, którego używasz.

Jeśli aplikacja używa długotrwałe połączeń i nie można użyć dostępnego zestawu SDK, można użyć połączenia gniazda Websocket. Aby zarządzać limit czasu 10 minut, ponowne łączenie w odpowiednim czasie.

Aby rozpocząć pracę z zestawem SDK mowy:

1. Pobierz [mowy SDK](speech-sdk.md).
1. Działania za pośrednictwem usług przetwarzania mowy [przewodniki szybkiego startu](quickstart-csharp-dotnet-windows.md) i [samouczki](how-to-recognize-intents-from-speech-csharp.md). Przyjrzyj się również [przykłady kodu](samples.md) do zapoznania się z nowych interfejsów API.
1. Zaktualizuj aplikację do korzystania z usług mowy.

## <a name="support"></a>Pomoc techniczna

Rozpoznawanie mowy Bing klienci powinni skontaktować się obsługą klienta, otwierając [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Możesz również skontaktować się nam jeśli wymaga Twoich potrzeb obsługi [planu pomocy technicznej](https://azure.microsoft.com/support/plans/).

Usługa rozpoznawania mowy, zestaw SDK i interfejsu API pomocy technicznej, odwiedź usług przetwarzania mowy [stronę pomocy technicznej](support.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj bezpłatnie usługi mowy](get-started.md)
* [Szybki start: Rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu Windows przy użyciu zestawu SDK rozpoznawania mowy](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Zobacz także
* [Informacje o wersji usługi mowy](releasenotes.md)
* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usług przetwarzania mowy i zestaw SDK rozpoznawania mowy](speech-sdk.md#get-the-sdk)
