---
title: Zamiana tekstu na mowę przy użyciu usług przetwarzania mowy platformy Azure
titleSuffix: Azure Cognitive Services
description: Zamiana tekstu na mowę, z usług przetwarzania mowy platformy Azure jest oparte na protokole REST usługa, umożliwiająca aplikacje, narzędzia lub urządzenia do konwertowania tekstu do naturalnym przypominającej ludzką syntezatora mowy. Wybierać standardowych i neuronowych głosów lub utworzyć niestandardowe głosu unikatowe dla produktu lub marki. ponad 75 standardowa głosy są dostępne w ponad 45 języków i ustawień regionalnych i 5 neuronowych głosy są dostępne w 4 języków i ustawień regionalnych.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 52f74bb3cb9e460fc5c572079355f47b4b0bf0a3
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010451"
---
# <a name="what-is-text-to-speech"></a>Co to jest zamiany tekstu na mowę?

Zamiana tekstu na mowę, z usług przetwarzania mowy platformy Azure jest oparte na protokole REST usługa, umożliwiająca aplikacje, narzędzia lub urządzenia do konwertowania tekstu do naturalnym przypominającej ludzką syntezatora mowy. Wybierać standardowych i neuronowych głosów lub utworzyć niestandardowe głosu unikatowe dla produktu lub marki. ponad 75 standardowa głosy są dostępne w ponad 45 języków i ustawień regionalnych i 5 neuronowych głosy są dostępne w 4 języków i ustawień regionalnych. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Technologia zamiany tekstu na mowę umożliwia twórcom zawartości korzystaj z użytkowników na różne sposoby. Zamiana tekstu na mowę można poprawić dostępność, udostępniając użytkownikom możliwość interakcji z zawartością komputerowi. Czy użytkownik ma wzroku niepełnosprawności uczenia, wymaga informacji o nawigacji podczas kierowania, zamiany tekstu na mowę można ulepszyć istniejące. Zamiana tekstu na mowę jest także cenne dodatek dla programu Boty głosu i asystentów wirtualnego.

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych głosów można wprowadzić bardziej naturalne interakcje z czatbotów i asystentów wirtualnego i angażujące konwertować teksty cyfrowych, takich jak książki elektroniczne audiobooks i poprawić funkcjonalność z systemów nawigacji w samochodzie. Przypominającej ludzką prosody naturalnych i wyczyść ustaleniu słów głosy neuronowych znaczące zmęczenie nasłuchiwania podczas interakcji z systemami sztucznej Inteligencji. Aby uzyskać więcej informacji na temat neuronowych głosów zobacz [obsługiwane języki](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Głosów niestandardowych

Dostosowywanie głosu umożliwia tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki. Aby utworzyć swoje niestandardowej czcionki głosowej, tworzenie nagrania studio i przekaż skojarzone skrypty jako dane szkoleniowe. Usługa tworzy następnie model unikatowy głosu dostosowana do Twojego nagrania. Można użyć tej niestandardowej czcionki głosowej syntetyzować mowy. Aby uzyskać więcej informacji, zobacz [głosów niestandardowych](how-to-customize-voice-font.md).

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

\* *Te usługi są dostępne przy użyciu punktu końcowego cris.ai. Zobacz [Swagger odwołania](https://westus.cris.ai/swagger/ui/index).*

> [!NOTE]
> Punkt końcowy zamiany tekstu na mowę implementuje, ograniczanie przepustowości, która ogranicza żądania do 25 na 5 sekund. Gdy wystąpi ograniczanie przepustowości, zostanie wyświetlone powiadomienie za pośrednictwem nagłówków wiadomości.

## <a name="get-started-with-text-to-speech"></a>Rozpoczynanie pracy z usługą zamiany tekstu na mowę

Firma Microsoft oferuje przewodników Szybki Start, zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Poniższa tabela zawiera listę zamiany tekstu na mowę przewodników Szybki Start, które są zorganizowane według języka.

| Przewodnik Szybki Start (REST) | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS i Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Okno systemu macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Okno systemu macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

| Przewodnik Szybki Start (SDK) | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| [C#, .NET framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod zamiany tekstu na mowę jest dostępny w witrynie GitHub. Te przykłady obejmują Konwersja tekstu na mowę w najbardziej popularnych języków programowania.

* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Przykłady zamiany tekstu na mowę (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

* [Uzyskaj subskrypcję usługi mowy bezpłatna](get-started.md)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
