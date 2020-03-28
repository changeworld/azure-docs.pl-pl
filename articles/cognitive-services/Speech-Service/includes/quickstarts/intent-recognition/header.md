---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900304"
---
W tym przewodniku Szybki start użyjesz [zestawu SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi rozumienie języka (LUIS) do rozpoznawania intencji z danych audio przechwyconych z mikrofonu. W szczególności użyjesz zestawu SDK mowy do przechwytywania mowy, a wstępnie utworzonej domeny z usługi LUIS do identyfikowania intencji automatyzacji domowej, takich jak włączanie i wyłączanie światła. 

Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy i identyfikowanie intencji z mikrofonu wymaga tylko kilku kroków:

> [!div class="checklist"]
>
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `IntentRecognizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Za `IntentRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `IntentRecognitionResult` zwrócony.
