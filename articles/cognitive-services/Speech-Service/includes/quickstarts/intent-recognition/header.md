---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900304"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi Language UNDERSTANDING (Luis) do rozpoznawania intencji z danych audio przechwytywanych z mikrofonu. W tym celu należy użyć zestawu Speech SDK do przechwytywania mowy i prekompilowanej domeny z LUIS, aby identyfikować intencje dla automatyzacji domowej, takie jak Włączanie i wyłączanie oświetlenia. 

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy i identyfikowanie intencji z mikrofonu trwa tylko kilka kroków:

> [!div class="checklist"]
>
> * Utwórz obiekt `SpeechConfig` z klucza subskrypcji i regionu.
> * Utwórz obiekt `IntentRecognizer` przy użyciu obiektu `SpeechConfig` z powyższych.
> * Przy użyciu obiektu `IntentRecognizer` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą `IntentRecognitionResult`.
