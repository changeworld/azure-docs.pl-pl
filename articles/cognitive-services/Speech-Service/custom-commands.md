---
title: Polecenia niestandardowe (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla poleceń niestandardowych (wersja zapoznawcza), rozwiązanie do tworzenia asystentów głosowych.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 62210bf480d09ce2a256a44b7554ac53aa06eb0c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579700"
---
# <a name="custom-commands-preview"></a>Polecenia niestandardowe (wersja zapoznawcza)

[Asystenci głosowani](voice-assistants.md) nasłuchują użytkowników i podejmują działania w odpowiedzi, często mówiąc z powrotem. Używają [zamiany mowy na tekst](speech-to-text.md) , aby transkrypcja mowę użytkownika, a następnie podejmować działania dotyczące interpretacji języka naturalnego tekstu. Ta akcja często obejmuje głosowanie danych wyjściowych wygenerowanego przez asystenta z funkcją [zamiany tekstu na mowę](text-to-speech.md). Urządzenia łączą się z asystentami przy użyciu obiektu `DialogServiceConnector` zestawu mowy SDK.

**Polecenia niestandardowe (wersja zapoznawcza)** to usprawnione rozwiązanie do tworzenia asystenta głosowego. Zapewnia ujednolicone środowisko tworzenia, automatyczny model hostingu i stosunkowo niższą złożoność, a także inne opcje tworzenia asystenta, takie jak [Direct line Speech](direct-line-speech.md). Upraszcza to jednak zmniejszenie elastyczności. W związku z tym polecenia niestandardowe (wersja zapoznawcza) najlepiej nadają się do wykonywania zadań lub scenariuszy kontroli poleceń. Jest to szczególnie dobrze dopasowane do Internet rzeczy (IoT) i urządzeń bezobsługowych.

W przypadku złożonej interakcji z konwersacjami i integracji z innymi rozwiązaniami, takimi jak [rozwiązanie dla Asystenta wirtualnego i szablon przedsiębiorstwa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , zaleca się korzystanie z bezpośredniej mowy wiersza.

Dobre kandydaci dla poleceń niestandardowych (wersja zapoznawcza) mają stałe słownictwo ze zdefiniowanymi zestawami zmiennych. Na przykład doskonałe zadania automatyzacji, takie jak kontrolowanie termostatu, są idealne.

   ![Przykłady scenariuszy uzupełniania zadań](media/voice-assistants/task-completion-examples.png "Przykłady uzupełniania zadań")

## <a name="getting-started-with-custom-commands-preview"></a>Wprowadzenie do poleceń niestandardowych (wersja zapoznawcza)

Pierwszym krokiem korzystania z poleceń niestandardowych (wersja zapoznawcza) w celu udostępnienia asystenta głosowego jest [uzyskanie klucza subskrypcji mowy](get-started.md) i uzyskanie dostępu do konstruktora poleceń niestandardowych (wersja zapoznawcza) w programie [Speech Studio](https://speech.microsoft.com). Z tego miejsca można utworzyć nową aplikację poleceń niestandardowych (wersja zapoznawcza) i opublikować ją, po której aplikacja na urządzeniu może komunikować się z nią przy użyciu zestawu Speech SDK.

   ![Przepływ tworzenia dla poleceń niestandardowych (wersja zapoznawcza)](media/voice-assistants/custom-commands-flow.png "Przepływ tworzenia poleceń niestandardowych (wersja zapoznawcza)")

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut.

* [Tworzenie aplikacji poleceń niestandardowych (wersja zapoznawcza)](quickstart-custom-speech-commands-create-new.md)
* [Tworzenie aplikacji poleceń niestandardowych (wersja zapoznawcza) z parametrami](quickstart-custom-speech-commands-create-parameters.md)
* [Nawiązywanie połączenia z aplikacją Custom Commands (wersja zapoznawcza) za pomocą zestawu Speech SDKC#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do tworzenia asystenta głosowego z poleceniami niestandardowymi (wersja zapoznawcza) jest dostępny w witrynie GitHub.

* [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Dostosowywanie

W przypadku asystentów głosowych utworzonych przy użyciu usługi Azure Speech Services można używać pełnego zakresu opcji dostosowywania dostępnych na potrzeby [zamiany mowy na tekst](speech-to-text.md), zamiany [tekstu na mowę](text-to-speech.md)oraz [niestandardowego słowa kluczowego](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
