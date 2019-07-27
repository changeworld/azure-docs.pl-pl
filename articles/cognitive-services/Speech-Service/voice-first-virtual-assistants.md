---
title: Niestandardowe Asystenci wirtualne pierwszego głosu (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla niestandardowych asystentów wirtualnych w pierwszej kolejności głosowej przy użyciu kanału bezpośredniej linii mowy w środowisku bot Framework i Cognitive Services Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 8a12c9e5d569092440552958554681c0b1d79590
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552547"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Informacje o niestandardowym Asystencie wirtualnym pierwszego głosu — wersja zapoznawcza

Niestandardowe Asystenci wirtualne korzystający z usług Azure Speech Services umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla swoich aplikacji i środowisk. Kanał mowy liniowej w systemie bot Framework rozszerza te możliwości przez zapewnienie skoordynowanego, zorganizowanego punktu wejścia do zgodnej bot, która umożliwia korzystanie z głosu w programie, prowadzenie komunikacji głosowej z małymi opóźnieniami i wysoką niezawodnością. Te botów mogą używać Language Understanding (LUIS) firmy Microsoft w celu interakcji z językiem naturalnym. Dostęp do bezpośredniej linii mowy jest uzyskiwany przez urządzenia za pomocą zestawu Speech Software Development Kit (SDK).

   ![Diagram koncepcyjny przepływu usługi aranżacji mowy liniowej](media/voice-first-virtual-assistants/overview.png "Przepływ kanału mowy")


Bezpośrednia obsługa mowy i skojarzonych z nimi funkcji dla niestandardowych asystentów wirtualnych w pierwszej kolejności jest idealnym uzupełnieniem [rozwiązania pomocnika wirtualnego i szablonu przedsiębiorstwa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Mimo że funkcja Direct line Speech może współdziałać z dowolnymi zgodnymi bot, te zasoby zapewniają linię bazową wielokrotnego użytku dla środowisk komunikacji o wysokiej jakości, a także często wspierane umiejętności i modele umożliwiające szybkie rozpoczęcie pracy.


## <a name="core-features"></a>Podstawowe funkcje

| Kategoria | Funkcje |
|----------|----------|
|[Niestandardowe słowo wznawiania](speech-devices-sdk-create-kws.md) | Można umożliwić użytkownikom rozpoczęcie konwersacji z botów przy użyciu niestandardowego słowa kluczowego, takiego jak "Hej contoso". To zadanie jest realizowane przy użyciu niestandardowego aparatu programu Microsoft Word w zestawie SDK mowy, który można skonfigurować za pomocą niestandardowego programu wznawiania, [który można wygenerować w tym miejscu](speech-devices-sdk-create-kws.md). Kanał mowy linii bezpośredniej zawiera weryfikację programu antywyrazowego po stronie usługi, która zwiększa dokładność aktywacji tekstu wznawiania w porównaniu z samym urządzeniem.
|[Zamiana mowy na tekst](speech-to-text.md) | Kanał mowy linii bezpośredniej zawiera transkrypcję audio w czasie rzeczywistym w rozpoznawanym tekście przy użyciu [zamiany mowy na tekst](speech-to-text.md) z usługi Azure Speech Services. Ten tekst jest dostępny zarówno dla programu bot, jak i aplikacji klienckiej.
|[Zamiana tekstu na mowę](text-to-speech.md) | Odpowiedzi na tekst z bot są wykryjene przy użyciu zamiany [tekstu na mowę](text-to-speech.md) z usług Azure Speech Services. Ta synteza zostanie następnie udostępniona aplikacji klienckiej jako strumień audio. Firma Microsoft oferuje możliwość tworzenia własnego, niestandardowego głosu neuronowych TTS, który przekazuje głos do Twojej marki, aby dowiedzieć się więcej o [kontaktach z nami](mailto:mstts@microsoft.com).
|[Bezpośredni wiersz mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Jako kanał w ramach platformy bot Framework funkcja Direct line Speech umożliwia bezproblemowe i bezproblemowe połączenie między aplikacją kliencką, zgodnym bot i możliwościami usług Azure Speech Services. Aby uzyskać więcej informacji na temat konfigurowania Bot na potrzeby korzystania z kanału mowy linii bezpośredniej, zobacz [jego stronę w dokumentacji programu bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do tworzenia głosu-pierwszego asystenta wirtualnego jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z bot w kilku popularnych językach programowania.

* [Przykłady wirtualnego asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)
* [Szybki Start: wirtualne Asystenci w pierwszej kolejności głosowej (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Szybki Start: wirtualne Asystenci z dźwiękiem (Java)](quickstart-virtual-assistant-java-jre.md)
* [Klient z bezpośrednią linią mowy (C#, WPF)](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client)

## <a name="customization"></a>Dostosowywanie

Wirtualne Asystenci do obsługi głosu są kompilowane przy użyciu usług Azure Speech Services mogą korzystać z pełnego zakresu opcji dostosowywania dostępnych na potrzeby [zamiany mowy na tekst](speech-to-text.md), zamiany [tekstu na mowę](text-to-speech.md)i [niestandardowego słowa kluczowego](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Pobierz rozwiązanie dla Asystenta wirtualnego i szablonu przedsiębiorstwa](https://github.com/Microsoft/AI)
