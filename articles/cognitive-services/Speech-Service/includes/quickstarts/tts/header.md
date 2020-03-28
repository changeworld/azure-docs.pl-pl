---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961534"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do konwersji tekstu na syntetyzowaną mowę. Usługa zamiany tekstu na mowę oferuje wiele opcji syntetyzowanych głosów, w obszarze [obsługa języka zamiany tekstu na mowę.](../../../language-support.md#text-to-speech) Po spełnieniu kilku wymagań wstępnych renderowanie syntetyzowanej mowy do domyślnych głośników wymaga tylko czterech kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechSynthesizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Używanie `SpeechSynthesizer` obiektu do mówienia tekstu.
> * Sprawdź `SpeechSynthesisResult` zwrócone błędy.
