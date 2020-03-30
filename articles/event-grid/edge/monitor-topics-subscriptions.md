---
title: Monitorowanie tematów i subskrypcji zdarzeń — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Monitorowanie tematów i subskrypcji zdarzeń
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086681"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorowanie tematów i subskrypcji zdarzeń

Usługa Event Grid on Edge udostępnia szereg metryk dotyczących tematów i subskrypcji zdarzeń w [formacie ekspozycji Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). W tym artykule opisano dostępne metryki i sposób ich włączania.

## <a name="enable-metrics"></a>Włączanie danych

Skonfiguruj moduł do emitowania `metrics__reporterType` metryk, ustawiając zmienną środowiskową `prometheus` w kontenerze opcje tworzenia:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Metryki będą dostępne `5888/metrics` w module dla `4438/metrics` http i https. Na przykład `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` dla http. W tym momencie moduł metryki można sondować punkt końcowy do zbierania metryk, jak w tym [przykładzie architektury](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Dostępne metryki

Zarówno tematy, jak i subskrypcje zdarzeń emitują metryki, aby uzyskać wgląd w dostarczanie zdarzeń i wydajność modułu.

### <a name="topic-metrics"></a>Metryki tematu

| Metryka | Opis |
| ------ | ----------- |
| WydarzeniaReceived | Liczba wydarzeń opublikowanych w temacie
| NiezrównaneUchunki | Liczba zdarzeń opublikowanych w temacie, które nie pasują do subskrypcji zdarzeń i są odrzucane
| Wnioski o sukces | Liczba przychodzących żądań publikacji otrzymanych przez temat
| Prośby o oprogramowanie SystemErrorRequests | Liczba przychodzących żądań publikowania nie powiodła się z powodu wewnętrznego błędu systemu
| UserErrorRequests (Prośby użytkownika) | Liczba żądań publikowania przychodzącego nie powiodła się z powodu błędu użytkownika, takiego jak nieprawidłowo sformułowany JSON
| SuccessRequestLatencyMs | Publikowanie opóźnienia odpowiedzi żądania w milisekundach


### <a name="event-subscription-metrics"></a>Metryki subskrypcji zdarzeń

| Metryka | Opis |
| ------ | ----------- |
| Liczba poukładów | Liczba zdarzeń pomyślnie dostarczonych do skonfigurowanego punktu końcowego
| Liczniki DeliveryFailure | Liczba zdarzeń, których nie udało się dostarczyć do skonfigurowanego punktu końcowego
| DostawySukcjoweLatywy | Opóźnienie zdarzeń pomyślnie dostarczonych w milisekundach
| DeliveryFailureLatencyMs | Opóźnienie błędów dostarczania zdarzeń w milisekundach
| SystemDelayForFirstAttemptMs | Opóźnienie systemu zdarzeń przed pierwszą próbą dostarczenia w milisekundach
| DeliveryAttemptsCount | Liczba prób dostarczenia zdarzeń - sukces i porażka
| Wygasłe konta | Liczba zdarzeń, które wygasły i nie zostały dostarczone do skonfigurowanego punktu końcowego