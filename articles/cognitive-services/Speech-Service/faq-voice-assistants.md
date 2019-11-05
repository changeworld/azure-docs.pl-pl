---
title: Często zadawane pytania dotyczące asystentów głosowych
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące asystentów głosowych przy użyciu poleceń niestandardowych (wersja zapoznawcza) lub kanału mowy z linią bezpośrednią.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 7ad3f932e9a10723d6cc1bae2fc4854c932d4c64
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507078"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Asystenci głosowa: często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym dokumencie, zapoznaj się z [innymi opcjami pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: co to jest asystent głosowy?**

Odp **.:** Podobnie jak Cortana, asystent głosowy to rozwiązanie, które nasłuchuje w odniesieniu do wyrażenia długości użytkownika, analizuje zawartość tych wyrażenia długości w znaczenie, wykonuje co najmniej jedną akcję w odpowiedzi na zamiar wypowiedź, a następnie dostarcza odpowiedzi użytkownikowi, który często zawiera składnik mówiony. Jest to "głosowanie", co prowadzi do korzystania z systemu. Autorzy asystentów głosowych tworzą aplikację na urządzeniu przy użyciu `DialogServiceConnector` w zestawie mowy SDK do komunikowania się z asystentem utworzonym za pomocą [poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md) lub kanałem [mowy liniowej](direct-line-speech.md) w środowisku bot Framework. Ci Asystenci mogą używać niestandardowych słów kluczowych, niestandardowej mowy i niestandardowego głosu, aby zapewnić środowisko dostosowane do Twojej marki lub produktu.

**P: czy należy używać poleceń niestandardowych (wersja zapoznawcza) czy bezpośredniej linii mowy? Jaka jest różnica?**

Odp. **:** [polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) to zestaw narzędzi, które ułatwiają tworzenie i obsługę asystenta, który jest dobrze dostosowany do scenariuszy ukończenia zadań. [Bezpośredni wiersz mowy](direct-line-speech.md) zapewnia bogatsze i bardziej zaawansowane funkcje, które mogą umożliwić niezawodne scenariusze konwersacji. Zobacz [porównanie rozwiązań asystenta](voice-assistants.md#comparing-assistant-solutions) , aby uzyskać więcej informacji.

**P: Jak mogę zacząć?**

Odp **.:** Najlepszym sposobem, aby zacząć od tworzenia niestandardowych poleceń (wersja zapoznawcza) lub Basic bot Framework bot.

* [Tworzenie aplikacji poleceń niestandardowych (wersja zapoznawcza)](quickstart-custom-speech-commands-create-new.md)
* [Tworzenie podstawowego bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Łączenie bot z kanałem mowy w linii bezpośredniej](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debugowanie

**P: otrzymuję błąd 401 podczas nawiązywania połączenia i nic nie działa. Wiem, że klucz subskrypcji mowy jest prawidłowy. Co się dzieje?**

Odp **.:** Podczas zarządzania subskrypcją w Azure Portal upewnij się, że używasz zasobu **mowy** (Microsoft. CognitiveServicesSpeechServices, "Speech"), a *nie* zasobu **Cognitive Services** ( Microsoft. CognitiveServicesAllInOne, "wszystkie Cognitive Services"). Sprawdź również, czy [w regionie usługi mowy są obsługiwane Asystenci głosowa](regions.md#voice-assistants).

![poprawna subskrypcja dla bezpośredniej linii mowy](media/voice-assistants/faq-supported-subscription.png "przykład zgodnej subskrypcji mowy")

**P: otrzymuję tekst rozpoznawania z `DialogServiceConnector`, ale widzę komunikat o błędzie "1011" i niczego nie bot. Zalet?**

Odp **.:** Ten błąd wskazuje na problem z komunikacją między asystentem a usługą asystenta głosowego.

* W przypadku poleceń niestandardowych (wersja zapoznawcza) Upewnij się, że aplikacja poleceń niestandardowych (wersja zapoznawcza) została opublikowana
* W przypadku bezpośredniej obsługi mowy w wierszu upewnij się, że [nawiązano połączenie z usługą bot w kanale bezpośredniego wiersza mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [Dodano obsługę protokołu przesyłania strumieniowego](https://aka.ms/botframework/addstreamingprotocolsupport) do bot (z obsługą obsługiwanego gniazda sieci Web), a następnie sprawdź, czy bot odpowiada na przychodzące żądania z kanału.

**P: ten kod nadal nie działa i/lub podczas korzystania z `DialogServiceConnector`występuje inny błąd. Co mam zrobić?**

Odp **.:** Rejestrowanie na podstawie plików zapewnia znacznie bardziej szczegółowe informacje i może ułatwić przyspieszenie żądań pomocy technicznej. Aby włączyć tę funkcję, zobacz [jak korzystać z rejestrowania plików](how-to-use-logging.md).

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)
