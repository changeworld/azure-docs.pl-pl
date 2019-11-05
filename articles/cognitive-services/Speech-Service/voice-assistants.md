---
title: Asystenci głosu — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla asystentów głosowych przy użyciu zestawu Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507738"
---
# <a name="about-voice-assistants"></a>Asystenci głosu — informacje

Asystenci głosowani korzystający z usług Azure Speech Services umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla swoich aplikacji i środowisk.

Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która używa albo (1) bezpośredniego kanału mowy w programie bot Framework lub (2) zintegrowanej usługi poleceń niestandardowych (wersja zapoznawcza) w celu ukończenia zadania.

Aplikacje nawiązują połączenie z usługą asystenta głosowego za pomocą zestawu Speech Software Development Kit (SDK).

   ![Diagram koncepcyjny przepływu usługi aranżacji asystenta głosowego](media/voice-assistants/overview.png "Przepływ asystenta głosowego")

## <a name="core-features"></a>Podstawowe funkcje

| Kategoria | Funkcje |
|----------|----------|
|[Custom — słowo kluczowe](speech-devices-sdk-create-kws.md) | Użytkownicy mogą uruchamiać konwersacje z asystentami przy użyciu niestandardowego słowa kluczowego, takiego jak "Hej contoso". Aplikacja wykonuje tę funkcję za pomocą niestandardowego aparatu słów kluczowych w zestawie mowy SDK, który można skonfigurować za pomocą niestandardowego słowa kluczowego [, które można wygenerować w tym miejscu](speech-devices-sdk-create-kws.md). Asystenci głosu mogą korzystać z weryfikacji słowa kluczowego po stronie usługi, aby poprawić dokładność aktywacji słowa kluczowego (w przeciwieństwie do samego urządzenia).
|[Zamiana mowy na tekst](speech-to-text.md) | Asystenci głosu konwertują dźwięk w czasie rzeczywistym na rozpoznany tekst przy użyciu [zamiany mowy na tekst](speech-to-text.md) z usług Azure Speech Services. Ten tekst jest dostępny, ponieważ jest uzyskanego, zarówno w implementacji asystenta, jak i aplikacji klienckiej.
|[Zamiana tekstu na mowę](text-to-speech.md) | Odpowiedzi na tekst od asystenta są syntezą przy użyciu zamiany [tekstu na mowę](text-to-speech.md) z usług Azure Speech Services. Ta synteza jest następnie udostępniana aplikacji klienckiej jako strumień audio. Firma Microsoft oferuje możliwość tworzenia własnego, niestandardowego głosu neuronowych TTS, który daje głos do marki. Aby dowiedzieć się więcej, [skontaktuj się z nami](mailto:mstts@microsoft.com).

## <a name="comparing-assistant-solutions"></a>Porównywanie rozwiązań Asystenta

Usługa asystenta głosowego nawiązuje połączenie aplikacji na urządzeniu z unikatową implementacją asystenta. Deweloperzy tworzą asystentów głosowych przy użyciu jednego (1) kanału [bezpośredniej linii mowy](direct-line-speech.md) lub (2) rozwiązania [poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md) .

   ![Porównanie rozwiązań Asystenta](media/voice-assistants/assistant-solution-comparison.png "Porównanie rozwiązań Asystenta")

| Rozwiązanie | Funkcje |
|----------|----------|
|[Polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) | Polecenia niestandardowe (wersja zapoznawcza) zapewniają usprawnione rozwiązanie do tworzenia i hostowania dla asystentów głosowych. Jest to dostosowane do potrzeb scenariuszy uzupełniania zadań i kontroli poleceń.
|[Bezpośredni wiersz mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Bezpośredni tryb mowy umożliwia bezproblemowe i bezproblemowe połączenie między (1) aplikacją kliencką, (2) zgodnym bot i (3) możliwościami usług Azure Speech Services. Aby uzyskać więcej informacji na temat konfigurowania Bot do korzystania z kanału mowy z linią bezpośrednią, zobacz [jego stronę w dokumentacji programu bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

Po utworzeniu asystenta głosowego przy użyciu jednego z tych rozwiązań Połącz swoją aplikację na urządzeniu przy użyciu `DialogServiceConnector` w zestawie mowy SDK. Aby uzyskać więcej informacji, zobacz Przewodniki Szybki Start i przykłady dla każdego rozwiązania.

## <a name="getting-started-with-voice-assistants"></a>Wprowadzenie do asystentów głosowych

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dla asystenta głosowego uporządkowanych według języka.

| Szybki start | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| C#, Platformy UWP | Windows | [Przycisku](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Przycisku](https://aka.ms/csspeech/javaref) |
| Java | Android | [Przycisku](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do tworzenia asystenta głosowego jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)
* [Samouczek: Włączanie głosu przez asystenta przy użyciu zestawu Speech SDKC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Samouczek

Samouczek dotyczący sposobu [obsługi głosu — Włącz asystenta przy użyciu zestawu mowy SDK i kanału mowy Direct line](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Dostosowywanie

W przypadku asystentów głosowych utworzonych przy użyciu usługi Azure Speech Services można używać pełnego zakresu opcji dostosowywania dostępnych na potrzeby [zamiany mowy na tekst](speech-to-text.md), zamiany [tekstu na mowę](text-to-speech.md)oraz [niestandardowego słowa kluczowego](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Dowiedz się więcej na temat poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md)
* [Dowiedz się więcej na temat bezpośredniej obsługi mowy w wierszu](direct-line-speech.md)