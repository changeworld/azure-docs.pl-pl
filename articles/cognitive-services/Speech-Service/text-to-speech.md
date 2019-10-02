---
title: Zamiana tekstu na mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany tekstu na mowę w usłudze mowy to funkcja, która umożliwia aplikacjom, narzędziom i urządzeniom Konwertowanie tekstu na rozpoznawanie mowy w postaci zwykłej. Wybieraj spośród standardowych i neuronowychych głosów lub twórz własny niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w 4 językach i ustawieniach regionalnych.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: d12b952d298b41c8d06f0fcac141a45749de9051
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71799953"
---
# <a name="what-is-text-to-speech"></a>Co to jest zamiana tekstu na mowę?

Zamiana tekstu na mowę z usługi Azure Speech Services to usługa, która umożliwia aplikacjom, narzędziom i urządzeniom Konwertowanie tekstu na rozpoznawanie mowy w postaci zwykłej, podobnej do ludzkiej. Wybieraj spośród standardowych i neuronowychych głosów lub twórz własny niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w 4 językach i ustawieniach regionalnych. Aby zapoznać się z pełną listą, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Technologia zamiany tekstu na mowę umożliwia twórcom zawartości współpracującie z użytkownikami na różne sposoby. Zamiana tekstu na mowę może zwiększyć dostępność, dostarczając użytkownikom możliwość korzystania z audibly zawartości. Bez względu na to, czy użytkownik ma niedowidzącość wizualną, nie ma możliwości uczenia się, czy wymaga informacji nawigacyjnych podczas prowadzenia pracy, funkcja zamiany tekstu na mowę może poprawić istniejące środowisko. Funkcja zamiany tekstu na mowę jest również cennym dodatkiem dla botów głosu i wirtualnych asystentów.


Korzystając z języka SSML (Speech Syntezing Language), języka znaczników opartych na języku XML, deweloperzy korzystający z usługi zamiany tekstu na mowę mogą określić, jak tekst wejściowy jest konwertowany na mowę. Dzięki SSML można dostosować wysokość, wymowę, częstotliwość mówienia, głośność i wiele więcej. Aby uzyskać więcej informacji, zobacz [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Głosy standardowe

Standardowe głosy są tworzone przy użyciu statystycznych parametrów syntezy i/lub łączenia technik syntezy. Głosy te są bardzo zrozumiałe i naturalne. Aplikacje można łatwo włączyć w więcej niż 45 językach z szeroką gamą opcji głosowych. Głosy te zapewniają wysoką dokładność odmowy, w tym obsługę skrótów, rozwinięcia akronimów, interpretacje daty/godziny, telefony i inne. Używaj standardowego głosu, aby ulepszyć ułatwienia dostępu dla aplikacji i usług, dzięki czemu użytkownicy mogą korzystać z audibly zawartości.

### <a name="neural-voices"></a>Głosy neuronowych

Głosy neuronowych używają głębokiej sieci neuronowych, aby przezwyciężyć limity tradycyjnych systemów zamiany tekstu na mowę w celu dopasowania wzorców obciążeniowych i intonation w języku mówionym oraz w syntezie jednostek mowy w głos komputerowy. Funkcja zamiany tekstu na mowę (Text-to-Speech) dzieli Prosody na oddzielne analizy językowe i rozłożenia akustyczne, które podlegają niezależnym modelom, co może spowodować przebicie głosu. Nasza funkcja neuronowych umożliwia jednocześnie Prosody prognozowanie i głosowanie, co pozwala na bardziej płynny i naturalny dźwięk.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i wirtualnymi asystentami bardziej naturalnymi i atrakcyjnymi, konwertowanie cyfrowych tekstów, takich jak książki elektroniczne, na Audiobooks i ulepszanie systemów nawigacyjnych. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania podczas pracy z systemami AI.

Głosy neuronowych obsługują różne style, takie jak neutralne i Cheerful. Na przykład głos Jessa (EN-US) może mówić cheerfully, który jest zoptymalizowany pod kątem ciepłej i szczęśliwej konwersacji. Możesz dostosować dane wyjściowe głosu, takie jak tonowe, gęstość i szybkość, przy użyciu [języka znaczników do syntezy mowy](speech-synthesis-markup.md). Aby uzyskać pełną listę dostępnych głosów, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Aby dowiedzieć się więcej na temat korzyści z używania głosów neuronowych, zobacz [Nowość usługa zamiany tekstu na mowę w firmie Microsoft neuronowych, która pomaga maszynom](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Głosy niestandardowe

Dostosowanie głosu umożliwia utworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Aby utworzyć niestandardową czcionkę głosową, nagranie i przekazanie skojarzonych skryptów jako danych szkoleniowych. Następnie usługa tworzy unikatowy model głosowy dostosowany do nagrania. Możesz użyć tej niestandardowej czcionki głosowej, aby wypróbować mowę. Aby uzyskać więcej informacji, zobacz [niestandardowe głosy](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Język SSML (Speech Syntezing Language) to język znaczników oparty na języku XML, który umożliwia deweloperom określenie sposobu konwersji tekstu wejściowego na funkcję syntezy mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom precyzyjne dostosowanie wartości, wymowy, natężenia głosu, ilości i większej liczby danych wyjściowych zamiany tekstu na mowę. Normalne znaki interpunkcyjne, takie jak Wstrzymywanie po upływie okresu lub używanie poprawnych intonation po zakończeniu zdania z znakiem zapytania, są automatycznie obsługiwane.

Wszystkie dane wejściowe wysyłane do usługi zamiany tekstu na mowę muszą być uporządkowane jako SSML. Aby uzyskać więcej informacji, zobacz [Język oznaczeń syntezy mowy](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Uwaga dotycząca cen

W przypadku korzystania z usługi zamiany tekstu na mowę naliczane są opłaty za każdy znak konwertowany na mowę, w tym znaki interpunkcyjne. Chociaż sam dokument SSML nie jest rozliczany, opcjonalne elementy, które są używane do dostosowywania sposobu konwersji tekstu na mowę, takie jak fonemów i gęstość, są zliczane jako znaki rozliczeniowe. Poniżej znajduje się lista rzeczy, które są rozliczane:

* Tekst przesłany do usługi zamiany tekstu na mowę w treści SSML żądania
* Wszystkie znaczniki w polu tekstowym treści żądania w formacie SSML, z wyjątkiem tagów `<speak>` i `<voice>`
* Litery, interpunkcja, spacje, tabulatory, znaczniki i wszystkie znaki odstępu
* Każdy punkt kodowy zdefiniowany w kodzie Unicode

Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Każdy znak w języku chińskim, japońskim i koreańskim jest traktowany jako dwa znaki do rozliczania.

## <a name="core-features"></a>Podstawowe funkcje

W tej tabeli przedstawiono podstawowe funkcje zamiany tekstu na mowę:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Konwertuj tekst na mowę. | Tak | Tak |
| Przekazywanie zestawów danych do adaptacji głosowej. | Nie | Tak @ no__t-0 |
| Twórz modele czcionek głosowych i zarządzaj nimi. | Nie | Tak @ no__t-0 |
| Twórz wdrożenia czcionek głosowych i zarządzaj nimi. | Nie | Tak @ no__t-0 |
| Twórz i Zarządzaj testami czcionek głosowych. | Nie | Tak @ no__t-0 |
| Zarządzanie subskrypcjami. | Nie | Tak @ no__t-0 |

\* *te usługi są dostępne za pomocą punktu końcowego CRIS.AI. Zobacz [Dokumentacja struktury Swagger](https://westus.cris.ai/swagger/ui/index). Te niestandardowe interfejsy API szkolenia i zarządzania umożliwiają wdrożenie ograniczenia ograniczające liczbę żądań do 25 na 5 sekund, natomiast interfejs API syntezy mowy implementuje ograniczenie, które umożliwia 200 żądań na sekundę. W przypadku ograniczenia przepustowości użytkownik zostanie powiadomiony o nagłówkach wiadomości.*

## <a name="get-started-with-text-to-speech"></a>Wprowadzenie do zamiany tekstu na mowę

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dotyczących zamiany tekstu na mowę uporządkowanych według języka.

### <a name="sdk-quickstarts"></a>Przewodniki Szybki Start dotyczące zestawu SDK

| Szybki Start (SDK) | Platforma | Odwołanie API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Przycisku](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Przycisku](https://aka.ms/csspeech/csharpref) |
| [C#, Platformy UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Przycisku](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Przycisku](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Przycisku](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Przycisku](https://aka.ms/csspeech/cppref) |
| [Java](quickstart-text-to-speech-java-jre.md) | Windows, Linux, macOS | [Przycisku](https://aka.ms/csspeech/javaref) |
| [Java](quickstart-text-to-speech-java-android.md) | Android | [Przycisku](https://aka.ms/csspeech/javaref) |
| [Objective-C](quickstart-text-to-speech-objectivec-macos.md) | macOS | [Przycisku](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](quickstart-text-to-speech-objectivec-ios.md) | iOS | [Przycisku](https://aka.ms/csspeech/objectivecref) |
| [Adres](quickstart-text-to-speech-swift-macos.md) | macOS | [Przycisku](https://aka.ms/csspeech/objectivecref) |
| [Adres](quickstart-text-to-speech-swift-ios.md) | iOS | [Przycisku](https://aka.ms/csspeech/objectivecref) |
| [Python](quickstart-text-to-speech-python.md) | Window, Linux, macOS | [Przycisku](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>Przewodniki Szybki Start

| Szybki Start (REST) | Platforma | Odwołanie API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Przycisku](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Przycisku](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Przycisku](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zamiany tekstu na mowę jest dostępny w witrynie GitHub. Te przykłady obejmują konwersję zamiany tekstu na mowę w najpopularniejszych językach programowania.

* [Przykłady zamiany tekstu na mowę (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Zestaw Speech SDK](speech-sdk-reference.md)
* [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
* [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
* [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatną subskrypcję usługi Speech Services](get-started.md)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
