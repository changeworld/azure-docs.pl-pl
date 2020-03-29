---
title: Asystenci głosowi - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla asystentów głosowych korzystających z zestawu SDK (Speech Software Development Kit).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369900"
---
# <a name="what-is-a-voice-assistant"></a>Co to jest asystent głosowy?

Asystenci głosowi korzystający z usługi Mowy umożliwiają deweloperom tworzenie naturalnych, podobnych do człowieka interfejsów konwersacyjnych dla swoich aplikacji i środowisk.

Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która używa (1) kanału direct line speech programu Bot Framework lub (2) zintegrowanej usługi polecenia niestandardowe (wersja zapoznawcza) do ukończenia zadania.

Aplikacje łączą się z usługą asystenta głosowego za pomocą zestawu SDK (Speech Software Development Kit).

   ![Diagram koncepcyjny przepływu usługi aranżacji asystenta głosowego](media/voice-assistants/overview.png "Przepływ asystenta głosowego")

## <a name="choosing-an-assistant-solution"></a>Wybór rozwiązania asystenta

Pierwszym krokiem do utworzenia asystenta głosowego jest podjęcie decyzji, co powinien zrobić. Usługa Mowy oferuje wiele, uzupełniających się rozwiązań do tworzenia interakcji asystenta. Niezależnie od tego, czy chcesz mieć elastyczność i wszechstronność, które zapewnia kanał [Direct Line Speech](direct-line-speech.md) programu Bot Framework, czy też prostotę [poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md) w prostych scenariuszach, wybranie odpowiednich narzędzi będzie łatwiejsze.

| Jeśli chcesz... | Następnie należy rozważyć... | Na przykład... |
|-------------------|------------------|----------------|
|Otwarta rozmowa z solidną integracją umiejętności i pełną kontrolą wdrażania | Kanał [bezpośredniej mowy linii](direct-line-speech.md) programu Bot Framework | <ul><li>"Muszę pojechać do Seattle"</li><li>"Jakiego rodzaju pizzę mogę zamówić?"</li></ul>
|Sterowanie i sterowanie lub konwersacja zorientowana na zadania z uproszczonym tworzeniem i hostingiem | [Polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) | <ul><li>"Włącz światło nad głową"</li><li>"O 5 stopni cieplej"</ul>

Zalecamy [direct line speech](direct-line-speech.md) jako najlepszy domyślny wybór, jeśli nie masz jeszcze pewności, z czym ma się poradzić asystent. Oferuje integrację z bogatym zestawem narzędzi i narzędzi ułatwiających tworzenie, takich jak [rozwiązanie Virtual Assistant i szablon enterprise oraz](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) usługa [QnA Maker,](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) aby opierać się na wspólnych wzorcach i korzystać z istniejących źródeł wiedzy.

[Polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) zapewnia usprawnione tworzenie i hosting dostosowane specjalnie do scenariuszy poleceń i kontroli języka naturalnego.

   ![Porównanie rozwiązań asystentów](media/voice-assistants/assistant-solution-comparison.png "Porównanie rozwiązań asystentów")

## <a name="core-features"></a>Podstawowe funkcje

Niezależnie od tego, czy wybierzesz [opcję Direct Line Speech](direct-line-speech.md) czy Custom [Commands (Preview),](custom-commands.md) aby utworzyć interakcje z asystentem, możesz użyć bogatego zestawu funkcji dostosowywania, aby dostosować asystenta do marki, produktu i osobowości.

| Kategoria | Funkcje |
|----------|----------|
|[Niestandardowe słowo kluczowe](speech-devices-sdk-create-kws.md) | Użytkownicy mogą rozpoczynać konwersacje z asystentami za pomocą niestandardowego słowa kluczowego, takiego jak "Hey Contoso". Aplikacja robi to za pomocą niestandardowego aparatu słów kluczowych w SDK mowy, który można skonfigurować za pomocą niestandardowego słowa [kluczowego, które można wygenerować tutaj](speech-devices-sdk-create-kws.md). Asystenci głosowi mogą korzystać z weryfikacji słów kluczowych po stronie usługi, aby poprawić dokładność aktywacji słów kluczowych (w porównaniu z samym urządzeniem).
|[Mowa do tekstu](speech-to-text.md) | Asystenci głosowi konwertuje dźwięk w czasie rzeczywistym na rozpoznany tekst za pomocą [zamiany mowy na tekst](speech-to-text.md) z usługi Mowa. Ten tekst jest dostępny, jak jest przepisyowany, do implementacji asystenta i aplikacji klienckiej.
|[Zamiana tekstu na mowę](text-to-speech.md) | Odpowiedzi tekstowe od asystenta są syntetyzowane przy użyciu [zamiany tekstu na mowę](text-to-speech.md) z usługi mowy. Ta synteza jest następnie udostępniana aplikacji klienckiej jako strumień audio. Firma Microsoft oferuje możliwość tworzenia własnego niestandardowego, wysokiej jakości głosu Neural TTS, który daje głos twojej marce. Aby dowiedzieć się więcej, [skontaktuj się z nami](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Pierwsze kroki z asystentami głosowymi

Oferujemy szybkie starty zaprojektowane tak, aby kod był uruchamiany w mniej niż 10 minut. Ta tabela zawiera listę przewodników Szybki start asystenta głosowego, uporządkowanych według języka.

| Szybki start | Platforma | Odwołanie API |
|------------|----------|---------------|
| C#, UWP | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Przeglądaj](https://aka.ms/csspeech/javaref) |
| Java | Android | [Przeglądaj](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod do tworzenia asystenta głosowego jest dostępny w usłudze GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Próbki asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)
* [Samouczek: Głos włącz asystenta za pomocą zestawu SDK mowy, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Samouczek

Samouczek dotyczący [włączania głosu asystenta za pomocą zestawu SDK mowy i kanału Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Dostosowywanie

Asystenci głosowi zbudowani przy użyciu usługi Mowa mogą korzystać z pełnego zakresu opcji dostosowywania dostępnych dla [zamiany mowy na tekst,](speech-to-text.md) [zamiany tekstu na mowę](text-to-speech.md)i [niestandardowego wyboru słów kluczowych.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (patrz [Obsługiwane języki).](supported-languages.md)

## <a name="reference-docs"></a>Dokumenty referencyjne

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
* [Pobierz sdk mowy](speech-sdk.md)
* [Dowiedz się więcej o poleceniach niestandardowych (wersja zapoznawcza)](custom-commands.md)
* [Dowiedz się więcej o bezpośredniej mowie liniowej](direct-line-speech.md)
