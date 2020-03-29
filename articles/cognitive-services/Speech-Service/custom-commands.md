---
title: Polecenia niestandardowe (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla poleceń niestandardowych (Wersja zapoznawcza), rozwiązanie do tworzenia aplikacji głosowych.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367843"
---
# <a name="what-are-custom-commands-preview"></a>Co to są polecenia niestandardowe (wersja zapoznawcza)?

Aplikacje głosowe, takie jak [asystenci głosowi,](voice-assistants.md) słuchają użytkowników i podejmują działania w odpowiedzi, często przemawiając z powrotem. Używają [mowy do tekstu](speech-to-text.md) do transkrypcji mowy użytkownika, a następnie podjąć działania w języku naturalnym zrozumienia tekstu. Ta akcja często obejmuje dane wyjściowe wypowiedziane z asystenta wygenerowane za [pomocą zamiany tekstu na mowę.](text-to-speech.md) Urządzenia łączą się z asystentami za `DialogServiceConnector` pomocą obiektu SDK mowy.

**Niestandardowe polecenia (Wersja zapoznawcza)** to usprawnione rozwiązanie do tworzenia aplikacji głosowych. Zapewnia ujednolicone środowisko tworzenia, automatyczny model hostingu i stosunkowo mniejszą złożoność w porównaniu z innymi opcjami, takimi jak [Direct Line Speech](direct-line-speech.md). Uproszczenie to wiąże się jednak ze zmniejszeniem elastyczności. Tak, Niestandardowe polecenia (Wersja zapoznawcza) najlepiej nadaje się do ukończenia zadania lub scenariuszy polecenia i kontroli. Jest szczególnie dobrze dopasowany do internetu rzeczy (IoT) i urządzeń bezgłowych.

W przypadku złożonej interakcji konwersacyjnej i integracji z innymi rozwiązaniami, takimi jak [rozwiązanie Virtual Assistant i szablon enterprise,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) zachęcamy do korzystania z direct line speech.

Dobrzy kandydaci do poleceń niestandardowych (wersja zapoznawcza) mają stałe słownictwo z dobrze zdefiniowanymi zestawami zmiennych. Na przykład, zadania automatyki domowej, takie jak sterowanie termostatem, są idealne.

   ![Przykłady scenariuszy ukończenia zadania](media/voice-assistants/task-completion-examples.png "przykłady realizacji zadań")

## <a name="getting-started-with-custom-commands-preview"></a>Wprowadzenie do poleceń niestandardowych (wersja zapoznawcza)

Pierwszym krokiem do tworzenia aplikacji głosowej przy użyciu poleceń niestandardowych (Wersja zapoznawcza) jest [uzyskanie klucza subskrypcji mowy](get-started.md) i uzyskanie dostępu do Konstruktora poleceń niestandardowych (Podgląd) w Studio [mowy.](https://speech.microsoft.com) W tym miejscu można zasiebie w autorykadyjnie niestandardowej aplikacji poleceń (wersja zapoznawcza) i opublikować ją, po czym aplikacja na urządzeniu może komunikować się z nią za pomocą SDK mowy.

   ![Przepływ tworzenia dla poleceń niestandardowych (wersja zapoznawcza)](media/voice-assistants/custom-commands-flow.png "Przepływ tworzenia poleceń niestandardowych (podgląd)")

Oferujemy szybkie starty zaprojektowane tak, aby kod był uruchamiany w mniej niż 10 minut.

* [Tworzenie aplikacji Polecenia niestandardowe (wersja zapoznawcza)](quickstart-custom-speech-commands-create-new.md)
* [Tworzenie aplikacji Poleceń niestandardowych (Podgląd) z parametrami](quickstart-custom-speech-commands-create-parameters.md)
* [Łączenie się z aplikacją Niestandardowe polecenia (wersja zapoznawcza) za pomocą sdk mowy, C #](quickstart-custom-speech-commands-speech-sdk.md)

Gdy skończysz z przewodnikami Szybki start, zapoznaj się z naszymi instrukcjami.

- [Dodawanie weryfikacji do parametrów polecenia niestandardowego](./how-to-custom-speech-commands-validations.md)
- [Wypełnianie poleceń na kliencie za pomocą sdk mowy](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Dodawanie potwierdzenia do polecenia niestandardowego](./how-to-custom-speech-commands-confirmations.md)
- [Dodawanie korekty jednoetapowej do polecenia niestandardowego](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
* [Przejdź do studia mowy, aby wypróbować polecenia niestandardowe](https://speech.microsoft.com)
* [Pobierz sdk mowy](speech-sdk.md)
