---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659050"
---
W tym przewodniku Szybki start można użyć [zestawu SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego rozpoznawania mowy z wejścia mikrofonu i uzyskania transkrypcji tekstu z przechwyconego dźwięku. Łatwo jest zintegrować tę funkcję z aplikacjami lub urządzeniami w celu wykonywania typowych zadań rozpoznawania, takich jak transkrypcja konwersacji. Może być również używany do bardziej złożonych integracji, takich jak za pomocą bot framework z SDK mowy do tworzenia asystentów głosowych.

Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy z mikrofonu wymaga tylko czterech kroków:

> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechRecognizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Za `SpeechRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `SpeechRecognitionResult` zwrócony.
