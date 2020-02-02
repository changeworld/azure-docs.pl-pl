---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961534"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na mowę. Usługa zamiany tekstu na mowę oferuje liczne opcje dla wydawanych głosów, w ramach [pomocy technicznej języka zamiany tekstu na mowę](../../../language-support.md#text-to-speech). Po spełnieniu kilku wymagań wstępnych, renderowanie mowy do domyślnych głośników obejmuje tylko cztery kroki:
> [!div class="checklist"]
> * Utwórz obiekt `SpeechConfig` z klucza subskrypcji i regionu.
> * Utwórz obiekt `SpeechSynthesizer` przy użyciu obiektu `SpeechConfig` z powyższych.
> * Używanie obiektu `SpeechSynthesizer`, aby mówić do tekstu.
> * Sprawdź `SpeechSynthesisResult` zwróconych pod kątem błędów.
