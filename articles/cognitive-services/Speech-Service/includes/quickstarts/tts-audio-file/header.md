---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961599"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do konwersji tekstu na syntetyzowaną mowę w pliku audio. Usługa zamiany tekstu na mowę oferuje wiele opcji syntetyzowanych głosów, w obszarze [obsługa języka zamiany tekstu na mowę.](../../../language-support.md#text-to-speech) Po spełnieniu kilku wymagań wstępnych syntetyzowanie mowy w pliku wykonuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz obiekt Konfiguracja audio, który określa obiekt . Nazwa pliku WAV.
> * Utwórz `SpeechSynthesizer` obiekt przy użyciu obiektów konfiguracyjnych z góry.
> * Za `SpeechSynthesizer` pomocą obiektu przekonwertuj tekst na syntetyzowaną mowę, zapisując go w określonym pliku audio.
> * Sprawdź `SpeechSynthesizer` zwrócone błędy.
