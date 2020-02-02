---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961599"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na polecenie "Speech" w pliku audio. Usługa zamiany tekstu na mowę oferuje liczne opcje dla wydawanych głosów, w ramach [pomocy technicznej języka zamiany tekstu na mowę](../../../language-support.md#text-to-speech). Po spełnieniu kilku wymagań wstępnych, synteza mowy do pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt `SpeechConfig` z klucza subskrypcji i regionu.
> * Utwórz obiekt konfiguracji audio, który określa. Nazwa pliku WAV.
> * Utwórz obiekt `SpeechSynthesizer` przy użyciu obiektów konfiguracji z powyższych.
> * Za pomocą obiektu `SpeechSynthesizer` przekonwertuj swój tekst na mowę, zapisując go w określonym pliku audio.
> * Sprawdź `SpeechSynthesizer` zwróconych pod kątem błędów.
