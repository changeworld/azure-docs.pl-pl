---
title: Często zadawane pytania przez asystentów głosowych
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące asystentów głosowych za pomocą poleceń niestandardowych (podgląd) lub kanału Direct Line Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110348"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Często zadawane pytania przez asystentów głosowych

Jeśli nie możesz znaleźć odpowiedzi na swoje pytania w tym dokumencie, zapoznaj się z [innymi opcjami pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: Co to jest asystent głosowy?**

**Odp.:** Podobnie jak Cortana, asystent głosowy jest rozwiązaniem, które nasłuchuje wypowiedzi mówionego użytkownika, analizuje zawartość tych wypowiedzi dla znaczenia, wykonuje jedną lub więcej akcji w odpowiedzi na intencje wypowiedź, a następnie zapewnia odpowiedź do użytkownika, który często zawiera składnik mówiony. Jest to "voice-in, voice-out" doświadczenie interakcji z systemem. autorzy asystenta głosowego tworzą `DialogServiceConnector` aplikację na urządzeniu używającą w kole mowy do komunikowania się z asystentem utworzonym za pomocą [poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md) lub kanału [Direct Line Speech](direct-line-speech.md) w programie Bot Framework. Asystenci ci mogą używać niestandardowych słów kluczowych, niestandardowej mowy i niestandardowego głosu, aby zapewnić środowisko dostosowane do Twojej marki lub produktu.

**P: Czy należy używać poleceń niestandardowych (podgląd) lub direct line speech? Jaka jest różnica?**

**Odp.:** [Polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) to zestaw narzędzi o mniejszej złożoności, które można łatwo utworzyć i hostować asystenta, który jest dobrze dostosowany do scenariuszy ukończenia zadania. [Direct Line Speech](direct-line-speech.md) zapewnia bogatsze, bardziej zaawansowane funkcje, które mogą umożliwić niezawodne scenariusze konwersacyjne. Zobacz [porównanie rozwiązań asystenta,](voice-assistants.md#choosing-an-assistant-solution) aby uzyskać więcej informacji.

**P: Jak zacząć?**

**Odp.:** Najlepszym sposobem, aby rozpocząć od tworzenia niestandardowych poleceń (wersja zapoznawcza) aplikacji lub podstawowego bot framework bot bot.

- [Tworzenie aplikacji Polecenia niestandardowe (wersja zapoznawcza)](quickstart-custom-speech-commands-create-new.md)
- [Tworzenie podstawowego bota frameworka](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Łączenie bota z kanałem Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debugowanie

**P: Gdzie jest mój tajny kanał?**

**Odp.:** Jeśli używasz wersji zapoznawczej direct line speech lub czytasz powiązaną dokumentację, możesz spodziewać się znalezienia klucza tajnego na stronie rejestracji kanału direct line speech. Metoda `FromBotSecret` fabryczna w `DialogServiceConfig` wersji 1.7 w SDK mowy również oczekuje tej wartości.

Najnowsza wersja direct line speech upraszcza proces kontaktowania się z botem z urządzenia. Na stronie rejestracji kanału rozwijana w górnej części kojarzy rejestrację kanału Direct Line Speech z zasobem mowy. Po skojarzeniu zestaw SDK mowy w wersji `BotFrameworkConfig::FromSubscription` 1.8 `DialogServiceConnector` zawiera metodę fabryczną, która skonfiguruje, aby skontaktować się z botem skojarzonym z subskrypcją.

Jeśli nadal migrujesz aplikację kliencką z wersji 1.7 do wersji 1.8, `DialogServiceConfig::FromBotSecret` możesz kontynuować pracę z niepustą wartością niekwaśną, niezerową dla parametru tajnego kanału, na przykład poprzedniego używanego klucza tajnego. Będzie po prostu ignorowane podczas korzystania z subskrypcji mowy skojarzone z nowszej rejestracji kanału. Należy pamiętać, że wartość _musi_ być nienajmowa i niepusta, ponieważ są one sprawdzane na urządzeniu, zanim skojarzenie po stronie usługi jest istotne.

Aby uzyskać bardziej szczegółowy przewodnik, zobacz [sekcję samouczka,](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) która przechodzi przez rejestrację kanału.

**P: Podczas łączenia pojawia się błąd 401 i nic nie działa. Wiem, że mój klucz subskrypcji mowy jest prawidłowy. Co się dzieje?**

**Odp.:** Podczas zarządzania subskrypcją w witrynie Azure portal, upewnij się, że używasz **zasobu mowy** (Microsoft.CognitiveServicesSpeechServices, "Speech"), a _nie_ zasób **usług Cognitive Services** (Microsoft.CognitiveServicesAllInOne, "Wszystkie usługi Cognitive Services"). Sprawdź również [obsługę regionu usługi mowy dla asystentów głosowych](regions.md#voice-assistants).

![poprawna subskrypcja dla bezpośredniej mowy liniowej](media/voice-assistants/faq-supported-subscription.png "przykład zgodnej subskrypcji mowy")

**P: Dostaję tekst rozpoznawania `DialogServiceConnector`z powrotem z mojego , ale widzę błąd "1011" i nic z mojego bota. Dlaczego?**

**Odp.:** Ten błąd wskazuje na problem z komunikacją między asystentem a usługą asystenta głosowego.

- W przypadku poleceń niestandardowych (wersja zapoznawcza) upewnij się, że aplikacja poleceń niestandardowych (wersja zapoznawcza) jest publikowana
- W przypadku direct line speech upewnij się, że [bot został połączony z kanałem Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), dodano obsługę protokołu przesyłania [strumieniowego](https://aka.ms/botframework/addstreamingprotocolsupport) do bota (z obsługą powiązanego gniazda sieci Web), a następnie sprawdź, czy bot odpowiada na przychodzące żądania z kanału.

**P: Ten kod nadal nie działa i/lub podczas korzystania z `DialogServiceConnector`programu pojawia się inny błąd. Co należy zrobić?**

**Odp.:** Rejestrowanie oparte na plikach zapewnia znacznie więcej szczegółów i może pomóc przyspieszyć żądania pomocy technicznej. Aby włączyć tę funkcję, [zobacz, jak używać rejestrowania plików](how-to-use-logging.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](troubleshooting.md)
- [Informacje o wersji](releasenotes.md)
