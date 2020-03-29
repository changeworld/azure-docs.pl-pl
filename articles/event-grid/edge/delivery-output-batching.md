---
title: Przetwarzanie danych w usłudze Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Przetwarzanie danych w sieci zdarzeń na podstawie usługi IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841751"
---
# <a name="output-batching"></a>Dzielenie na partie danych wyjściowych

Usługa Event Grid ma obsługę dostarczania więcej niż jednego zdarzenia w jednym żądaniu dostarczenia. Ta funkcja umożliwia zwiększenie ogólnej przepływności dostawy bez płacenia kosztów ogólnych HTTP na żądanie. Przetwarzanie wsadowe jest domyślnie wyłączone i można je włączyć dla każdej subskrypcji.

> [!WARNING]
> Maksymalny dozwolony czas trwania do przetworzenia każdego żądania dostawy nie zmienia się, nawet jeśli kod subskrybenta potencjalnie musi wykonać więcej pracy na żądanie wsadowe. Limit czasu dostawy domyślnie 60 sekund.

## <a name="batching-policy"></a>Zasady przetwarzania wsadowego

Zachowanie przetwarzania wsadowego w uścisnąć w ucho podczas zdarzeń można dostosować na subskrybenta, dostosowując następujące dwa ustawienia:

* Maksymalna liczba zdarzeń na partię

  To ustawienie ustawia górny limit liczby zdarzeń, które można dodać do żądania dostawy wsadowej.

* Preferowany rozmiar partii w kilobajtach

  To pokrętło służy do dalszego kontrolowania maksymalnej liczby kilobajtów, które mogą być wysyłane na żądanie dostawy

## <a name="batching-behavior"></a>Zachowanie przetwarzania wsadowego

* Wszystkie lub żadne

  Usługa Event Grid działa z semantyką typu "wszystko albo brak". Nie obsługuje częściowego sukcesu dostarczania partii. Subskrybenci powinni uważać, aby poprosić tylko o tyle zdarzeń na partię, jak mogą rozsądnie obsługiwać w 60 sekund.

* Optymistyczne przetwarzanie wsadowe

  Ustawienia zasad przetwarzania wsadowego nie są ścisłe granice zachowania przetwarzania wsadowego i są przestrzegane na podstawie najlepszych starań. Przy niskich szybkościach zdarzeń często można zaobserwować rozmiar partii jest mniejszy niż żądana maksymalna liczba zdarzeń na partię.

* Wartość domyślna jest ustawiona na WYŁ.

  Domyślnie usługa Event Grid dodaje tylko jedno zdarzenie do każdego żądania dostarczenia. Sposób, aby włączyć przetwarzanie wsadowe jest ustawienie jednego z ustawień wymienionych wcześniej w artykule w subskrypcji zdarzeń JSON.

* Wartości domyślne

  Nie jest konieczne określenie zarówno ustawień (Maksymalna liczba zdarzeń na partię, jak i przybliżony rozmiar partii w kilo bajtach) podczas tworzenia subskrypcji zdarzeń. Jeśli ustawiono tylko jedno ustawienie, siatka zdarzeń używa (konfigurowalnych) wartości domyślnych. Zobacz poniższe sekcje dla wartości domyślnych i jak je zastąpić.

## <a name="turn-on-output-batching"></a>Włączanie przetwarzania wsadowego

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Konfigurowanie maksymalnych dozwolonych wartości

Następujące ustawienia czasu wdrożenia określają maksymalną wartość dozwoloną podczas tworzenia subskrypcji zdarzeń.

| Nazwa właściwości | Opis |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maksymalna dopuszczalna `PreferredBatchSizeInKilobytes` wartość dla pokrętła. Domyślnie `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maksymalna dopuszczalna `MaxEventsPerBatch` wartość dla pokrętła. Domyślnie `50`.

## <a name="configuring-runtime-default-values"></a>Konfigurowanie wartości domyślnych środowiska uruchomieniowego

Następujące ustawienia czasu wdrożenia sterują domyślną wartością środowiska uruchomieniowego każdego pokrętła, gdy nie jest ona określona w subskrypcji zdarzeń. Aby powtórzyć, co najmniej jedno pokrętło musi być ustawione w subskrypcji zdarzeń, aby włączyć zachowanie przetwarzania wsadowego.

| Nazwa właściwości | Opis |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maksymalny rozmiar żądania `MaxEventsPerBatch` dostawy, gdy jest określony tylko. Domyślnie `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maksymalna liczba zdarzeń do dodania `MaxBatchSizeInBytes` do partii, gdy jest określona tylko. Domyślnie `10`.
