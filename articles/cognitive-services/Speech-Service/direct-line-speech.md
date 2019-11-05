---
title: Direct line — mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla asystentów głosowych korzystających z bezpośredniej mowy wiersza z zestawem SDK (Speech Software Development Kit).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 72029d1d56c9d561e629cc750244fd7de8ec113a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507177"
---
# <a name="about-direct-line-speech"></a>Informacje o bezpośredniej mowie wiersza

[Asystenci głosowani](voice-assistants.md) nasłuchują użytkowników i podejmują działania w odpowiedzi, często mówiąc z powrotem. Używają [zamiany mowy na tekst](speech-to-text.md) , aby transkrypcja mowę użytkownika, a następnie podejmować działania dotyczące interpretacji języka naturalnego tekstu. Ta akcja często obejmuje głosowanie danych wyjściowych wygenerowanego przez asystenta z funkcją [zamiany tekstu na mowę](text-to-speech.md). Urządzenia łączą się z asystentami przy użyciu obiektu `DialogServiceConnector` zestawu mowy SDK.

**Bezpośrednia funkcja mowy w wierszu** to niezawodne, kompleksowe rozwiązanie umożliwiające tworzenie elastycznych, rozszerzalnych asystentów głosowych, opartych na platformie bot oraz kanału mowy z bezpośrednim wierszem, który jest zoptymalizowany pod kątem komunikacji głosowej z botów.

Funkcja bezpośrednie linie rozpoznawania mowy oferuje najwyższy poziom dostosowania i złożoności dla asystentów głosowych. Jest to również odpowiednie rozwiązanie dla scenariuszy konwersacji, które są otwartymi, naturalnymi lub hybrydowymi z tymi zadaniami. Taka wysoka elastyczność ma większą złożoność, a scenariusze, które są ograniczone do dobrze zdefiniowanych zadań przy użyciu danych wejściowych języka naturalnego, mogą chcieć rozważyć użycie [poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md) w celu usprawnienia rozwiązania.

## <a name="getting-started-with-direct-line-speech"></a>Wprowadzenie do bezpośredniej obsługi mowy w wierszu

Pierwszym etapem tworzenia asystenta głosowego za pomocą polecenia Direct line Speech jest [uzyskanie klucza subskrypcji mowy](get-started.md), utworzenie nowego bot skojarzonego z tą subskrypcją i połączenie bot z kanałem mowy linii bezpośredniej.

   ![Diagram koncepcyjny przepływu usługi aranżacji mowy liniowej](media/voice-assistants/overview-directlinespeech.png "Przepływ kanału mowy")

Aby zapoznać się z kompletnym przewodnikiem krok po kroku dotyczącym tworzenia prostego asystenta głosowego za pomocą bezpośredniego rozpoznawania mowy, zapoznaj [się z samouczkiem dotyczącym zamiany mowy na bot za pomocą zestawu Speech SDK i kanału mowy Direct line](tutorial-voice-enable-your-bot-speech-sdk.md).

Oferujemy również Przewodniki Szybki Start, które umożliwiają uruchamianie kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dla asystenta głosowego zorganizowanych według języka.

| Szybki start | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| C#, Platformy UWP | Windows | [Przycisku](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Przycisku](https://aka.ms/csspeech/javaref) |
| Java | Android | [Przycisku](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do tworzenia asystenta głosowego jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)
* [Samouczek: Włączanie głosu przez asystenta przy użyciu zestawu Speech SDKC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Dostosowywanie

W przypadku asystentów głosowych utworzonych przy użyciu usługi Azure Speech Services można używać pełnego zakresu opcji dostosowywania dostępnych na potrzeby [zamiany mowy na tekst](speech-to-text.md), zamiany [tekstu na mowę](text-to-speech.md)oraz [niestandardowego słowa kluczowego](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

Bezpośrednia obsługa mowy liniowej i skojarzonych z nią funkcji dla asystentów głosowych jest idealnym uzupełnieniem rozwiązania do obsługi [asystenta wirtualnego i szablonu przedsiębiorstwa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Mimo że funkcja Direct line Speech może współdziałać z dowolnymi zgodnymi bot, te zasoby zapewniają linię bazową wielokrotnego użytku dla środowisk komunikacji o wysokiej jakości, a także często wspierane umiejętności i modele umożliwiające szybkie rozpoczęcie pracy.

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Pobierz rozwiązanie dla Asystenta wirtualnego i szablonu przedsiębiorstwa](https://github.com/Microsoft/AI)
