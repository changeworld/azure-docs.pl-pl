---
title: Wskazówki dotyczące ograniczania usługi Azure Key Vault
description: Ograniczania usługi Key Vault ogranicza liczbę równoczesnych wywołań, aby zapobiec nadmiernemu zużyciu zasobów.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 28756cf28305927246d82f1f006f02b2e9b96469
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054974"
---
# <a name="azure-key-vault-throttling-guidance"></a>Wskazówki dotyczące ograniczania usługi Azure Key Vault

Ograniczenie to proces zainicjowanych przez, który ogranicza liczbę równoczesnych wywołań do usługi Azure zapobiega nadmiernemu zużyciu zasobów. Usługa Azure Key Vault (AKV) została zaprojektowana do obsługi dużej liczby żądań. W przypadku zbyt dużej liczby żądań ograniczanie żądań klienta pomaga utrzymać optymalną wydajność i niezawodność usługi AKV.

Limity ograniczania różnią się zależnie od scenariusza. Na przykład jeśli przeprowadzasz dużej liczby zapisów możliwości ograniczania jest wyższa niż jeśli są wykonywane tylko operacje odczytu.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak usługa Key Vault obsługuje zmuszenia?

Limity usługi w usłudze Key Vault, istnieją zapobiegania niewłaściwemu użyciu zasobów i zapewnienia dobrej jakości usługi dla wszystkich klientów usługi Key Vault. Po przekroczeniu progu usługi Key Vault ogranicza wszystkie dalsze żądania z tego klienta w okresie czasu. W takiej sytuacji usługa Key Vault zwraca kod stanu HTTP 429 (zbyt wiele żądań), i Niepowodzenie żądania. Ponadto żądań zakończonych niepowodzeniem, które zwracają 429 liczba kierunku limitów ograniczania przepływności śledzone przez usługę Key Vault. 

Jeśli masz biznesowe przypadku wyższych limitów ograniczania przepływności, skontaktuj się z nami.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak ograniczyć aplikację w odpowiedzi na limity usługi

Poniżej przedstawiono **najlepsze praktyki** ograniczania aplikacji:
- Zmniejsz liczbę operacji na żądanie.
- Zmniejsz częstotliwość żądań.
- Należy unikać bezpośredniego ponownych prób. 
    - Wszystkie żądania są naliczane względem limitów użycia.

Podczas implementowania obsługi błędów aplikacji, należy użyć kod błędu HTTP 429 można wykrywać ograniczania po stronie klienta. Jeśli żądanie nie powiedzie się ponownie z kodem błędu HTTP 429, nadal występują ograniczenia usług platformy Azure. Nadal używać zalecanych klienta ograniczania metody, ponowieniem próby żądania, aż do skutku.

### <a name="recommended-client-side-throttling-method"></a>Zalecana metoda ograniczania przepływności po stronie klienta

Na kod błędu HTTP 429 Rozpocznij ograniczanie klienta przy użyciu podejścia wykładniczego wycofywania:

1. Poczekaj 1 sekundę, ponów próbę żądania
2. Jeśli nadal ograniczona poczekaj 2 sekundy, ponów próbę wykonania żądania
3. Jeśli nadal ograniczona oczekiwania 4 sekundy, ponów próbę wykonania żądania
4. Jeśli nadal ograniczona oczekiwania 8 sekund, ponów próbę wykonania żądania
5. Jeśli nadal ograniczona oczekiwania 16 sekund, ponów próbę wykonania żądania

W tym momencie użytkownik powinien nie uzyskać kody odpowiedzi HTTP 429.

## <a name="see-also"></a>Zobacz także

Dla bardziej orientację ograniczania przepustowości na Microsoft Cloud, zobacz [wzorzec ograniczania](https://docs.microsoft.com/azure/architecture/patterns/throttling).

