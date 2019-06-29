---
title: Zamiana tekstu na mowę przy użyciu usług przetwarzania mowy platformy Azure
titleSuffix: Azure Cognitive Services
description: Zamiana tekstu na mowę, z usług przetwarzania mowy platformy Azure to usługa, która umożliwia aplikacji, narzędzia lub urządzenia do konwertowania tekstu do naturalnym przypominającej ludzką syntezatora mowy. Wybierać standardowych i neuronowych głosów lub utworzyć niestandardowe głosu unikatowe dla produktu lub marki. ponad 75 standardowa głosy są dostępne w ponad 45 języków i ustawień regionalnych i 5 neuronowych głosy są dostępne w 4 języków i ustawień regionalnych.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: da7259585ad66ac9b58131ce834d82e7b3d4bcf2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466959"
---
# <a name="what-is-text-to-speech"></a>Co to jest zamiany tekstu na mowę?

Zamiana tekstu na mowę, z usług przetwarzania mowy platformy Azure to usługa, która umożliwia aplikacji, narzędzia lub urządzenia do konwertowania tekstu do naturalnym przypominającej ludzką syntezatora mowy. Wybierać standardowych i neuronowych głosów lub utworzyć niestandardowe głosu unikatowe dla produktu lub marki. ponad 75 standardowa głosy są dostępne w ponad 45 języków i ustawień regionalnych i 5 neuronowych głosy są dostępne w 4 języków i ustawień regionalnych. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Technologia zamiany tekstu na mowę umożliwia twórcom zawartości korzystaj z użytkowników na różne sposoby. Zamiana tekstu na mowę można poprawić dostępność, udostępniając użytkownikom możliwość interakcji z zawartością komputerowi. Czy użytkownik ma wzroku niepełnosprawności uczenia, wymaga informacji o nawigacji podczas kierowania, zamiany tekstu na mowę można ulepszyć istniejące. Zamiana tekstu na mowę jest także cenne dodatek dla programu Boty głosu i asystentów wirtualnego.


Dzięki wykorzystaniu mowy syntezy Markup Language (SSML), język znaczników oparty na formacie XML, deweloperzy korzystający z tekstu na mowę usługi można określić sposób wprowadzania tekstu jest konwertowana na syntezatora mowy. Za pomocą SSML można dostosować pomysłu, wymowa, wypowiedzi szybkości, woluminów i nie tylko. Aby uzyskać więcej informacji, zobacz [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Standardowa głosów

Standardowa głosy są tworzone przy użyciu technik statystycznych syntezy parametryczne i/lub syntezy łączenia. Te głosy są wysoce zrozumiałej i dźwięku naturalnego. Łatwo można włączyć aplikacji, aby porozmawiać w ponad 45 językach z szeroką gamę opcji głosu. Głosy te zapewniają wysoką Wymowa dokładności, łącznie z obsługą skróty, rozszerzenia akronim, interpretacji daty/godziny i polyphones. Użyj standardowego głosu, aby poprawić dostępność aplikacji i usług, pozwalając użytkownikom na interakcję z zawartością komputerowi.

### <a name="neural-voices"></a>Głosy neuronowych

Głosy neuronowych Użyj głębokich sieciach neuronowych przezwyciężenie limitów tradycyjnych systemów zamiany tekstu na mowę w pasujących do wzorców obciążenia i maksimów w mowy i Syntetyzujące jednostek mowy do głosowego komputera. Standardowa zamiany tekstu na mowę dzieli prosody w oddzielnych analizy językowej i kroki akustyczny prognoz, które podlegają niezależnie od modeli, które mogą skutkować syntezy muffled głosu. Nasze możliwości neuronowych wykonuje prosody prognoz i głosu syntezy równocześnie, które powoduje płynne i bardziej brzmiącą naturalnego głosu.

Neuronowych głosów można wprowadzić bardziej naturalne interakcje z czatbotów i asystentów wirtualnego i angażujące konwertować teksty cyfrowych, takich jak książki elektroniczne audiobooks i poprawić funkcjonalność z systemów nawigacji w samochodzie. Przypominającej ludzką prosody naturalnych i wyczyść ustaleniu słów głosy neuronowych znaczące zmęczenie nasłuchiwania podczas interakcji z systemami sztucznej Inteligencji.

Głosy neuronowych obsługuje różne style, takie jak neutralne i uśmiecha. Na przykład głosu Jessa (en US) można było mówić cheerfully, który jest zoptymalizowany pod kątem dostępu do ciepłych, wszystkiego konwersacji. Można dopasować dane wyjściowe głosowych, takich jak tonu, wysokość i szybkości za pomocą [język znaczników synteza mowy](speech-synthesis-markup.md). Aby uzyskać pełną listę dostępnych głosów, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Aby dowiedzieć się więcej o zaletach głosów neuronowej, zobacz [usługa nowe zamiany tekstu na mowę neuronowa firmy Microsoft pomaga maszyn, mowy, takich jak osoby](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Głosów niestandardowych

Dostosowywanie głosu umożliwia tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki. Aby utworzyć swoje niestandardowej czcionki głosowej, tworzenie nagrania studio i przekaż skojarzone skrypty jako dane szkoleniowe. Usługa tworzy następnie model unikatowy głosu dostosowana do Twojego nagrania. Można użyć tej niestandardowej czcionki głosowej syntetyzować mowy. Aby uzyskać więcej informacji, zobacz [głosów niestandardowych](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Mowy syntezy Markup Language (SSML) to język znaczników oparty na formacie XML, które umożliwiają deweloperom określenie sposobu wprowadzania tekstu jest konwertowana na syntezatora mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom dostosować wysokość wymowa, wypowiedzi szybkości, woluminów i nie tylko zamiany tekstu na mowę danych wyjściowych. Normalne znaki interpunkcyjne, takie jak przytrzymanie po upływie lub za pomocą poprawne maksimów, gdy dowolne zdanie kończy się znakiem zapytania są realizowane automatycznie.

Wszystkie tekstowe dane wejściowe wysyłane do zamiany tekstu na mowę usługi musi postacią SSML. Aby uzyskać więcej informacji, zobacz [język znaczników synteza mowy](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Cennik Uwaga

Podczas korzystania z usługi zamiany tekstu na mowę, są rozliczane dla każdego znaku, który jest konwertowany na mowę, w tym znaki interpunkcyjne. Podczas samego dokumentu SSML nie jest płatne, elementy opcjonalne, które są używane do dostosowywania, jak tekst jest konwertowany na mowę, takich jak fonemów i wysokością, są liczone jako płatne znaków. Poniżej przedstawiono listę co to jest płatny:

* Tekst jest przekazywany w usłudze zamiany tekstu na mowę w treści żądania SSML
* Wszystkich znaczników w polu tekstowym w treści żądania w formacie SSML, z wyjątkiem `<speak>` i `<voice>` tagów
* Litery, znaki interpunkcyjne, miejsca do magazynowania, karty, znaczników i wszystkie znaki odstępu
* Każdy punkt kodowy zdefiniowane w formacie Unicode

Aby uzyskać szczegółowe informacje, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Każdy znak języka chińskiego, japońskiego i koreańskiego jest liczony jako dwa znaki na potrzeby rozliczeń.

## <a name="core-features"></a>Podstawowe funkcje

Poniższa tabela zawiera listę podstawowych funkcji zamiany tekstu na mowę:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Konwertuj tekst na mowę. | Yes | Yes |
| Przekaż zestawów danych dostosowywania głosu. | Nie | Tak\* |
| Tworzenie i zarządzanie modelami czcionek głosowych. | Nie | Tak\* |
| Tworzenie i zarządzanie wdrożeniami czcionek głosowych. | Nie | Tak\* |
| Tworzenie i zarządzanie nimi testy czcionek głosowych. | Nie | Tak\* |
| Zarządzaj subskrypcjami. | Nie | Tak\* |

\* *Te usługi są dostępne przy użyciu punktu końcowego cris.ai. Zobacz [Swagger odwołania](https://westus.cris.ai/swagger/ui/index). Te szkolenia niestandardowe głosu i interfejsów API zarządzania można zaimplementować ograniczanie przepustowości, limity żądań do 25 na 5 sekund, natomiast synteza mowy sam interfejs API implementuje, ograniczanie przepustowości, która umożliwia 200 żądań na sekundę jako najwyższy. Gdy wystąpi ograniczanie przepustowości, zostanie wyświetlone powiadomienie za pośrednictwem nagłówków wiadomości.*

## <a name="get-started-with-text-to-speech"></a>Rozpoczynanie pracy z usługą zamiany tekstu na mowę

Firma Microsoft oferuje przewodników Szybki Start, zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Poniższa tabela zawiera listę zamiany tekstu na mowę przewodników Szybki Start, które są zorganizowane według języka.

### <a name="sdk-quickstarts"></a>Przewodniki szybkiego startu zestawu SDK

| Przewodnik Szybki Start (SDK) | Platforma | Dokumentacja interfejsów API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, Aparatu unity](quickstart-text-to-speech-csharp-unity.md) | Windows i Android | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Przeglądaj](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Przewodniki Szybki Start REST

| Przewodnik Szybki Start (REST) | Platforma | Dokumentacja interfejsów API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS i Linux | [Przeglądaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Okno systemu macOS, Linux | [Przeglądaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Okno systemu macOS, Linux | [Przeglądaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod zamiany tekstu na mowę jest dostępny w witrynie GitHub. Te przykłady obejmują Konwersja tekstu na mowę w najbardziej popularnych języków programowania.

* [Przykłady zamiany tekstu na mowę (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiany mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

* [Uzyskaj subskrypcję usługi mowy bezpłatna](get-started.md)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
