---
title: Zamiana tekstu na mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany tekstu na mowę w usłudze mowy umożliwia aplikacjom, narzędziom i urządzeniom Konwertowanie tekstu na rozpoznawanie mowy przez człowieka. Wybierz wstępnie ustawione głosy lub Utwórz własny niestandardowy głos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: f5929be58c5e2159e832005b0e0f8d01e014427e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923757"
---
# <a name="what-is-text-to-speech"></a>Co to jest zamiana tekstu na mowę?

Zamiana tekstu na mowę z usługi mowy umożliwia aplikacjom, narzędziom i urządzeniom Konwertowanie tekstu na rozpoznawanie mowy w postaci zwykłej, podobnej do ludzkiej. Wybieraj spośród standardowych i neuronowychych głosów lub twórz własny niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w 4 językach i ustawieniach regionalnych. Aby zapoznać się z pełną listą, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Technologia zamiany tekstu na mowę umożliwia twórcom zawartości współpracującie z użytkownikami na różne sposoby. Zamiana tekstu na mowę może zwiększyć dostępność, dostarczając użytkownikom możliwość korzystania z audibly zawartości. Bez względu na to, czy użytkownik ma niedowidzącość wizualną, nie ma możliwości uczenia się, czy wymaga informacji nawigacyjnych podczas prowadzenia pracy, funkcja zamiany tekstu na mowę może poprawić istniejące środowisko. Funkcja zamiany tekstu na mowę jest również cennym dodatkiem dla botów głosu i asystentów głosowych.

Korzystając z języka SSML (Speech Syntezing Language), języka znaczników opartych na języku XML, deweloperzy korzystający z usługi zamiany tekstu na mowę mogą określić, jak tekst wejściowy jest konwertowany na mowę. Dzięki SSML można dostosować wysokość, wymowę, częstotliwość mówienia, głośność i wiele więcej. Aby uzyskać więcej informacji, zobacz [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Głosy standardowe

Standardowe głosy są tworzone przy użyciu statystycznych parametrów syntezy i/lub łączenia technik syntezy. Głosy te są bardzo zrozumiałe i naturalne. Aplikacje można łatwo włączyć w więcej niż 45 językach z szeroką gamą opcji głosowych. Głosy te zapewniają wysoką dokładność odmowy, w tym obsługę skrótów, rozwinięcia akronimów, interpretacje daty/godziny, telefony i inne. Używaj standardowego głosu, aby ulepszyć ułatwienia dostępu dla aplikacji i usług, dzięki czemu użytkownicy mogą korzystać z audibly zawartości.

### <a name="neural-voices"></a>Głosy neuronowych

Głosy neuronowych używają głębokiej sieci neuronowych, aby przezwyciężyć limity tradycyjnych systemów zamiany tekstu na mowę w celu dopasowania wzorców obciążeniowych i intonation w języku mówionym oraz w syntezie jednostek mowy w głos komputerowy. Funkcja zamiany tekstu na mowę (Text-to-Speech) dzieli Prosody na oddzielne analizy językowe i rozłożenia akustyczne, które podlegają niezależnym modelom, co może spowodować przebicie głosu. W przypadku naszej funkcji syntezy neuronowej przewidywanie prozodii i synteza głosu odbywają się jednocześnie, dzięki czemu głos jest bardziej płynny i naturalny.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania podczas pracy z systemami AI.

Głosy neuronowych obsługują różne style, takie jak neutralne i Cheerful. Na przykład głos Jessa (EN-US) może mówić cheerfully, który jest zoptymalizowany pod kątem ciepłej i szczęśliwej konwersacji. Możesz dostosować dane wyjściowe głosu, takie jak tonowe, gęstość i szybkość, przy użyciu [języka znaczników do syntezy mowy](speech-synthesis-markup.md). Aby uzyskać pełną listę dostępnych głosów, zobacz [obsługiwane języki](language-support.md#text-to-speech).

Aby dowiedzieć się więcej na temat korzyści z używania głosów neuronowych, zobacz [Nowość usługa zamiany tekstu na mowę w firmie Microsoft neuronowych, która pomaga maszynom](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Głosy niestandardowe

Dostosowanie głosu umożliwia utworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Aby utworzyć niestandardową czcionkę głosową, nagranie i przekazanie skojarzonych skryptów jako danych szkoleniowych. Następnie usługa tworzy unikatowy model głosowy dostosowany do nagrania. Możesz użyć tej niestandardowej czcionki głosowej, aby wypróbować mowę. Aby uzyskać więcej informacji, zobacz [niestandardowe głosy](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Język SSML (Speech Syntezing Language) to język znaczników oparty na języku XML, który umożliwia deweloperom określenie sposobu konwersji tekstu wejściowego na funkcję syntezy mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom precyzyjne dostosowanie wartości, wymowy, natężenia głosu, ilości i większej liczby danych wyjściowych zamiany tekstu na mowę. Normalne znaki interpunkcyjne, takie jak Wstrzymywanie po upływie okresu lub używanie poprawnych intonation po zakończeniu zdania z znakiem zapytania, są automatycznie obsługiwane.

Wszystkie dane wejściowe wysyłane do usługi zamiany tekstu na mowę muszą być uporządkowane jako SSML. Aby uzyskać więcej informacji, zobacz [Język oznaczeń syntezy mowy](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Uwaga dotycząca cen

W przypadku korzystania z usługi zamiany tekstu na mowę naliczane są opłaty za każdy znak konwertowany na mowę, w tym znaki interpunkcyjne. Chociaż sam dokument SSML nie jest rozliczany, opcjonalne elementy, które są używane do dostosowywania sposobu konwersji tekstu na mowę, takie jak fonemów i gęstość, są zliczane jako znaki rozliczeniowe. Poniżej znajduje się lista rzeczy, które są rozliczane:

- Tekst przesłany do usługi zamiany tekstu na mowę w treści SSML żądania
- Wszystkie znaczniki w polu tekstowym treści żądania w formacie SSML, z wyjątkiem tagów `<speak>` i `<voice>`
- Litery, interpunkcja, spacje, tabulatory, znaczniki i wszystkie znaki odstępu
- Każdy punkt kodowy zdefiniowany w kodzie Unicode

Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Każdy znak w języku chińskim, japońskim i koreańskim jest traktowany jako dwa znaki do rozliczania.

## <a name="core-features"></a>Podstawowe funkcje

W tej tabeli przedstawiono podstawowe funkcje zamiany tekstu na mowę:

| Przypadek użycia                                  | SDK | REST  |
| ----------------------------------------- | --- | ----- |
| Konwertuj tekst na mowę.                   | Tak | Tak   |
| Przekazywanie zestawów danych do adaptacji głosowej.     | Nie  | Tak\* |
| Twórz modele czcionek głosowych i zarządzaj nimi.      | Nie  | Tak\* |
| Twórz wdrożenia czcionek głosowych i zarządzaj nimi. | Nie  | Tak\* |
| Twórz i Zarządzaj testami czcionek głosowych.       | Nie  | Tak\* |
| Zarządzanie subskrypcjami.                     | Nie  | Tak\* |

\*_te usługi są dostępne za pomocą punktu końcowego CRIS.AI. Zobacz [Dokumentacja struktury Swagger](https://westus.cris.ai/swagger/ui/index). Te niestandardowe interfejsy API szkolenia i zarządzania umożliwiają wdrożenie ograniczenia ograniczające liczbę żądań do 25 na 5 sekund, natomiast interfejs API syntezy mowy implementuje ograniczenie, które umożliwia 200 żądań na sekundę. W przypadku ograniczenia przepustowości użytkownik zostanie powiadomiony o nagłówkach wiadomości._

## <a name="get-started-with-text-to-speech"></a>Wprowadzenie do zamiany tekstu na mowę

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dotyczących zamiany tekstu na mowę uporządkowanych według języka.

### <a name="sdk-quickstarts"></a>Przewodniki Szybki Start dotyczące zestawu SDK

| Szybki Start (SDK) | Platforma | Dokumentacja interfejsu API |
| ---------------- | -------- | ------------- |
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)  | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, Platformy UWP](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=unity) | Windows, Android | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Przeglądaj](https://aka.ms/csspeech/cppref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=linux) | Linux | [Przeglądaj](https://aka.ms/csspeech/cppref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Przeglądaj](https://aka.ms/csspeech/javaref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android) | Android | [Przeglądaj](https://aka.ms/csspeech/javaref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-macos.md) | macOS | [Przeglądaj](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-ios.md) | iOS | [Przeglądaj](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-macos.md) | macOS | [Przeglądaj](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-ios.md) | iOS | [Przeglądaj](https://aka.ms/csspeech/objectivecref) |
| [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python) | Window, Linux, macOS | [Przeglądaj](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>Przewodniki Szybki Start

| Szybki Start (REST) | Platforma | Dokumentacja interfejsów API |
| ----------------- | -------- | ------------- |
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp) | Windows, macOS, Linux | [Przeglądaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Przeglądaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Przeglądaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zamiany tekstu na mowę jest dostępny w witrynie GitHub. Te przykłady obejmują konwersję zamiany tekstu na mowę w najpopularniejszych językach programowania.

- [Przykłady zamiany tekstu na mowę (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentacja

- [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
- [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

- [Pobierz bezpłatną subskrypcję usługi mowy](get-started.md)
- [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
