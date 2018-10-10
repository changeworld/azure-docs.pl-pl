---
title: Migracja z rozpoznawania mowy Bing do usługi rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Poznaj różnice między rozpoznawania mowy Bing i usługi mowy z punktu widzenia projektanta i migracji aplikacji do korzystania z usługi rozpoznawania mowy.
services: cognitive-services
author: wsturman
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: e72cf547ac911b22a03cae6032351c8c0f22de8e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884853"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migracja z rozpoznawania mowy Bing do usługi rozpoznawania mowy

W tym artykule należy użyć do migracji aplikacji z modułu Speech API Bing do usługi rozpoznawania mowy.

W tym artykule opisano różnice między interfejsy API rozpoznawania mowy Bing i usługi rozpoznawania mowy i sugeruje Strategie migracji aplikacji. Klucz interfejsu API rozpoznawania mowy Bing subskrypcji nie zostanie zaakceptowane przez usługę rozpoznawania mowy; należy nową subskrypcję usługi mowy.

Jeden klucz subskrypcji usługa rozpoznawania mowy udziela dostępu do następujących funkcji. Każdy odbywa się oddzielnie, dlatego opłaty są naliczane tylko w przypadku funkcji, których używasz.

* [Zamiany mowy na tekst](speech-to-text.md)
* [Niestandardowe mowy na tekst](https://cris.ai)
* [Zamiana tekstu na mowę](text-to-speech.md)
* [Niestandardowe głosów zamiany tekstu na mowę](how-to-customize-voice-font.md)
* [Tłumaczenie mowy](speech-translation.md) (nie obejmuje [tłumaczenie tekstu](../translator/translator-info-overview.md))

[Zestaw SDK rozpoznawania mowy](speech-sdk.md) funkcjonalności zastępuje biblioteki klienta mowy Bing, ale używa innego interfejsu API.

## <a name="comparison-of-features"></a>Porównanie funkcji

Usługa rozpoznawania mowy jest na funkcję, platformy i programowania parzystość języka przy użyciu rozpoznawania mowy Bing z następującymi różnicami.

Cecha | Rozpoznawanie mowy Bing | Usługa rozpoznawania mowy | Szczegóły
-|-|-|-
ZESTAW SDK JĘZYKA C++ | : heavy_minus_sign: | : heavy_check_mark: | Usługa rozpoznawania mowy obsługuje Windows i Linux
Zestaw SDK Java | : heavy_check_mark: | : heavy_check_mark: | Usługa rozpoznawania mowy obsługuje systemów Android, jak i zestaw Speech Devices
Zestaw SDK języka C# | : heavy_check_mark: | : heavy_check_mark: | Usługa rozpoznawania mowy obsługuje system Windows 10, platformy uniwersalnej systemu Windows i .NET Standard 2.0
Rozpoznawanie mowy | 10 minut | Bez ograniczeń (przy użyciu zestawu SDK) | Protokoły WebSockets usług mowy i rozpoznawania mowy Bing obsługuje do 10 minut na wywołanie. Jednak zestaw SDK rozpoznawania mowy automatycznie ponownie nawiąże połączenie przekroczenia limitu czasu lub odłączyć.
Tymczasowy lub częściowe wyniki | : heavy_check_mark: | : heavy_check_mark: | Za pomocą protokołu Websocket lub zestawu SDK
Niestandardowe modele mowy | : heavy_check_mark: | : heavy_check_mark: | Rozpoznawanie mowy Bing wymaga oddzielnej subskrypcji Custom Speech
Czcionki głosowe niestandardowe | : heavy_check_mark: | : heavy_check_mark: | Rozpoznawanie mowy Bing wymaga oddzielnej subskrypcji Custom Voice
Głosy 24 kHz | : heavy_minus_sign: | : heavy_check_mark: 
Rozpoznawanie celu mowy | Wymaga wywołania interfejsu API usługi LUIS | Zintegrowane (przy użyciu zestawu SDK) |  Klucz usługi LUIS może być używany z usługą mowy.
Proste rozpoznawanie intencji | : heavy_minus_sign: | : heavy_check_mark: 
Batch transkrypcji pliki długo audio | : heavy_minus_sign: | : heavy_check_mark:
Tryb rozpoznawania | Ręcznie za pomocą identyfikatora URI punktu końcowego | Automatyczny | Tryb rozpoznawania nie jest dostępny w usłudze rozpoznawania mowy
Lokalizacja punktu końcowego | Globalny | Regionalne | Punkty końcowe regionalnych Zmniejszaj opóźnienia. Globalny punkt końcowy jest pod uwagę usługi mowy.
Interfejsy API REST | : heavy_check_mark: | : heavy_check_mark: | Interfejs API REST usługi mowy jest zgodny z rozpoznawania mowy Bing (inny punkt końcowy). Interfejsy API REST obsługują funkcji zamiany mowy na tekst zamiany tekstu na mowę i ograniczony.
Protokoły WebSockets | : heavy_check_mark: | : heavy_check_mark: | Interfejs API Websocket usługi mowy jest zgodny z rozpoznawania mowy Bing (inny punkt końcowy). Przeprowadź migrację do SDK rozpoznawania mowy, jeśli jest to możliwe uprościć kod.
Wywołania interfejsu API do usługi | : heavy_check_mark: | : heavy_minus_sign: | Podana w rozpoznawania mowy Bing za pośrednictwem biblioteki usługi C#. 
Zestaw SDK open source | : heavy_check_mark: | : heavy_minus_sign: |

Usługa rozpoznawania mowy korzysta z modelu cenowego opartego na czasie (zamiast transakcji modelu). Zobacz [cennik usług mowy](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) Aby uzyskać szczegółowe informacje.

## <a name="migration-strategies"></a>Strategie migracji

Jeśli Ty lub Twoja organizacja ma aplikacje w rozwoju i produkcji, które korzysta z interfejsu API rozpoznawania mowy Bing, należy zaktualizować je tak szybko, jak to możliwe korzystanie z usługi rozpoznawania mowy. Zobacz [dokumentacji usługi mowy](index.yml) dostępnych zestawów SDK, przykłady kodu i samouczki.

Usługa rozpoznawania mowy [interfejsów API REST](rest-apis.md) są zgodne z interfejsów API rozpoznawania mowy Bing. Jeśli używasz obecnie programu interfejsów API REST dla funkcji rozpoznawania mowy Bing, należy zmienić tylko punkt końcowy REST, a następnie przejdź na klucz subskrypcji usługa rozpoznawania mowy.

Protokoły WebSockets usług mowy, są również zgodne z tych używanych przez rozpoznawania mowy Bing. Zaleca się nowych wdrożeń docelowa zestawu SDK usługi rozpoznawania mowy, a nie używa funkcji WebSockets, i zachęcamy do migrowania istniejącego kodu do zestawu SDK, jak również. Jednak jako za pomocą interfejsów API REST istniejący kod, który używa rozpoznawania mowy Bing za pośrednictwem funkcji WebSockets wymaga tylko zmiana punktu końcowego i klucz zaktualizowane.

Jeśli używasz biblioteki klienta mowy Bing dla określonego języka programowania, migracja do [zestaw SDK rozpoznawania mowy](speech-sdk.md) będzie wymagać zmian w aplikacji, ponieważ różni się interfejsu API. Zestaw SDK rozpoznawania mowy, może uczynić kod przyspiesza również zapewniając Ci dostęp do nowych funkcji.

Obecnie zestaw SDK rozpoznawania mowy obsługuje C# (Windows 10, platformy uniwersalnej systemu Windows, platformy .NET Standard), Java (urządzenia z systemem Android i niestandardowych), Objective C (iOS), języka C++ (Windows i Linux) i języka JavaScript. Interfejsy API na wszystkich platformach są podobne, ułatwianie programowanie dla wielu platform.

Usługa rozpoznawania mowy nie oferuje obecnie globalny punkt końcowy. Należy określić, jeśli aplikacja będzie działać wydajnie za pomocą pojedynczego punktu końcowego regionalne dla wszystkich jego ruchu. W przeciwnym razie użyj geolokacji, aby określić najbardziej efektywny sposób punktu końcowego. Konieczne będzie oddzielnej subskrypcji usługa rozpoznawania mowy w każdym z regionów, którego używasz.

Jeśli aplikacja używa długotrwałe połączeń, nie można użyć dostępnego zestawu SDK można używać połączenia WebsSockets i zarządzać limit czasu 10 minut przez ponowne połączenie się w odpowiednim czasie.

Aby rozpocząć pracę z zestawem SDK mowy:

1. Pobierz [mowy SDK](speech-sdk.md).
1. Pracy za pośrednictwem usługi mowy [przewodniki szybkiego startu](quickstart-csharp-dotnet-windows.md), [samouczki](how-to-recognize-intents-from-speech-csharp.md)i przyjrzyj się [przykłady kodu](samples.md) do zapoznania się z nowych interfejsów API.
1. Aktualizacja aplikacji umożliwiająca użycie usługi rozpoznawania mowy i interfejsów API.

## <a name="support"></a>Pomoc techniczna

Rozpoznawanie mowy Bing klienci powinni skontaktować się obsługą klienta, otwierając [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Możesz również skontaktować się nam jeśli wymaga Twoich potrzeb obsługi [Plan pomocy technicznej techniczne](https://azure.microsoft.com/support/plans/).

Usługa rozpoznawania mowy, zestaw SDK i interfejsu API pomocy technicznej można znaleźć w Speech Service [stronę pomocy technicznej](support.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md)
* [Szybki Start: Rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu Windows przy użyciu zestawu SDK rozpoznawania mowy](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Zobacz także
* [Informacje o wersji usługi mowy](releasenotes.md)
* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi rozpoznawania mowy i zestawu SDK](speech-sdk.md#get-the-sdk)
