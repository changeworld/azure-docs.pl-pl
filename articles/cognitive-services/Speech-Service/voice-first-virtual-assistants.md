---
title: Niestandardowych voice pierwszego wirtualnego asystentów (wersja zapoznawcza) — usług przetwarzania mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczenia dotyczące niestandardowych asystentów wirtualnych pierwszej głosu przy użyciu kanału Direct mowy wiersza na platformy Bot Framework i Cognitive Services mowy Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: c9b85895e701ff74e025d6f7895bc32513bc29a1
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806906"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>O niestandardowych asystentów wirtualnego głosu pierwszy (wersja zapoznawcza)

Niestandardowe asystentów wirtualnego przy użyciu usług przetwarzania mowy platformy Azure zwiększenie możliwości dostępnych dla deweloperów do tworzenia naturalnych, przypominającej ludzką interfejsy konwersacyjne na potrzeby swoich aplikacji i środowisk. Kanał bezpośredniego wiersza mowy platformy Bot Framework rozszerza te możliwości, zapewniając punktem wejścia skoordynowane, zorganizowane do zgodnych bot, który umożliwia głosu w głosu się interakcji z małymi opóźnieniami i wysoką niezawodnością. Te Boty służy firmy Microsoft Language Understanding (LUIS) do interakcji z języka naturalnego. Bezpośrednie mowy wiersza jest dostęp do urządzenia za pomocą mowy Software Development Kit (SDK).

   ![Diagram pojęciowy przepływu usługa aranżacji mowy linii bezpośredniej](media/voice-first-virtual-assistants/overview.png "przepływ kanału mowy")


Bezpośrednie mowy wiersza i jej funkcjami dla niestandardowych asystentów wirtualnych pierwszej głosowych są idealne dodatku [wirtualny Asystent rozwiązanie i szablonie](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Chociaż bezpośrednie mowy wiersza można pracować z dowolnego zgodne bot, te zasoby zawierają wielokrotnego użytku linii bazowej środowiska konwersacji wysokiej jakości, a także wspólnego umiejętności obsługi i modele umożliwiające szybkie rozpoczęcie.


## <a name="core-features"></a>Podstawowe funkcje

| Kategoria | Funkcje |
|----------|----------|
|[Niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md) | Można umożliwić użytkownikom rozpoczynać rozmowy Boty przy użyciu niestandardowych słów kluczowych, takich jak "Hey Contoso." To zadanie jest realizowane za pomocą niestandardowego aparatu programu word, w zestawie SDK rozpoznawania mowy, które można skonfigurować niestandardowe słowo aktywujące [mogący generować, w tym miejscu](speech-devices-sdk-create-kws.md). Kanał bezpośredniego mowy wiersz zawiera weryfikacji word wznawiania po stronie usługi, która zwiększa dokładność aktywacji word wznawiania, a samo urządzenie.
|[Zamiana mowy na tekst](speech-to-text.md) | Kanał bezpośredniego mowy wiersz zawiera w czasie rzeczywistym transkrypcji audio do rozpoznany tekst przy użyciu [mowy na tekst](speech-to-text.md) z usług przetwarzania mowy platformy Azure. Ten tekst jest dostępna zarówno w przypadku bota, jak i aplikację kliencką, ponieważ jest on transkrybowanego.
|[Zamiana tekstu na mowę](text-to-speech.md) | Tekstowe odpowiedzi z botem zostanie przekształcony, za pomocą [zamiany tekstu na mowę](text-to-speech.md) z usług przetwarzania mowy platformy Azure. Syntezy następnie będą dostępne do aplikacji klienta w postaci strumienia audio. Firma Microsoft oferuje możliwość tworzenia własnych niestandardowych, wysokiej jakości głos TTS Neuronowej, zapewniająca głosu na marki, aby dowiedzieć się więcej [skontaktuj się z nami](mailto:mstts@microsoft.com).
|[Direct Line mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Jako kanał w ramach platformy Bot Framework bezpośrednie mowy wiersza umożliwia płynne i bezproblemowe połączenie między usługą aplikacji klienckiej, bot zgodne i możliwości usług przetwarzania mowy platformy Azure. Aby uzyskać więcej informacji na temat konfigurowania bota przy użyciu kanału Direct mowy wiersza, zobacz [jej stronę w dokumentacji platformy Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod do tworzenia Asystenta ustawień wirtualnego głosu pierwszy jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację klienta do łączenia się z bota w kilku popularnych języków programowania.

* [Próbki głosu pierwszego wirtualnego Asystenta ustawień (SDK)](https://aka.ms/csspeech/samples)
* [Szybki Start: głosu zdominowanym asystentów wirtualnego (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Szybki Start: pierwszy głosu wirtualnego asystentów (Java)](quickstart-virtual-assistant-java-jre.md)
* [Bezpośrednie klienta mowy wiersza (C#, WPF)](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client)

## <a name="customization"></a>Dostosowywanie

Pierwszy głosu asystentów wirtualnych utworzonych przy użyciu usług przetwarzania mowy platformy Azure można użyć pełnego zakresu opcje dostosowywania dostępne dla [mowy na tekst](speech-to-text.md), [zamiany tekstu na mowę](text-to-speech.md), i [custom — słowo kluczowe wybór](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opcje dostosowywania zależy od języka/ustawienia regionalne (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
* [Pobierz zestaw SDK funkcji rozpoznawania mowy](speech-sdk.md)
* [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Wirtualny Asystent rozwiązania i szablonu](https://github.com/Microsoft/AI)
