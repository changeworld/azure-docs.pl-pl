---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 4b823a8dc15d33e0537fae348b34c3a3fee84558
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330921"
---
<!-- N.B. no header, language-agnostic -->

[Zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia tłumaczenia mowy w aplikacji.
Zestaw SDK udostępnia pełną funkcjonalność usługi.
Podstawowy proces podczas wykonywania tłumaczeń mowy dokonywanych obejmuje następujące kroki:

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi rozpoznawania mowy i [region](~/articles/cognitive-services/speech-service/regions.md).
   Możesz również skonfigurować języków tłumaczeń źródłowym i docelowym, a także określenie, czy chcesz, aby dane wyjściowe tekstu i mowy.

1. Pobierz aparat rozpoznawania tłumaczenia z fabryki mowy.
   Istnieją różne podtypy aparatów rozpoznawania tłumaczenia na podstawie źródła audio, którego używasz.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne.
   Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe.
   W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawanie i tłumaczenia.

Poniżej przedstawiono kilka fragmentów kodu dla scenariuszy osobistego tłumaczenia mowy przy użyciu zestawu SDK rozpoznawania mowy.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
