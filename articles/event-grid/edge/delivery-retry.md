---
title: Dostarczanie i ponawianie — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Dostarczanie i ponawianie próby w usztywnienia zdarzeń na przeglądarce IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841716"
---
# <a name="delivery-and-retry"></a>Dostarczanie i ponawianie prób

Usługa Event Grid zapewnia trwałe dostarczanie. Próbuje dostarczyć każdą wiadomość co najmniej raz dla każdej pasującej subskrypcji natychmiast. Jeśli punkt końcowy subskrybenta nie potwierdza odbioru zdarzenia lub występuje błąd, usługa Event Grid ponawia próbę dostarczenia na podstawie ustalonego **harmonogramu ponawiania i** **zasad ponawiania ponawiania próby**.  Domyślnie moduł siatki zdarzeń dostarcza jedno zdarzenie naraz do subskrybenta. Ładunek jest jednak tablicy z jednego zdarzenia. Moduł może dostarczać więcej niż jedno zdarzenie naraz, włączając funkcję przetwarzania wsadowego danych wyjściowych. Aby uzyskać szczegółowe informacje na temat tej funkcji, zobacz [przetwarzanie danych wyjściowych](delivery-output-batching.md).  

> [!IMPORTANT]
>Nie ma obsługi trwałości dla danych zdarzeń. Oznacza to, że ponowne wdrożenie lub ponowne uruchomienie modułu siatki zdarzeń spowoduje utratę wszelkich zdarzeń, które nie zostały jeszcze dostarczone.

## <a name="retry-schedule"></a>Harmonogram ponawiania prób

Usługa Event Grid czeka do 60 sekund na odpowiedź po dostarczeniu wiadomości. Jeśli punkt końcowy subskrybenta nie ACK odpowiedzi, a następnie wiadomość zostanie w kolejce do jednego z naszych kolejek z powrotem do kolejnych ponownych prób.

Istnieją dwa wstępnie skonfigurowane kolejki cofania, które określają harmonogram, w którym zostanie podjęta próba ponawiania. Oto one:

| Harmonogram | Opis |
| ---------| ------------ |
| 1 min | Wiadomości, które kończą się tutaj, są podejmowane co minutę.
| 10 minut | Wiadomości, które kończą się tutaj, są podejmowane co 10 minut.

### <a name="how-it-works"></a>Jak to działa

1. Komunikat dociera do modułu siatki zdarzeń. Podjęto próbę natychmiastowego dostarczenia.
1. Jeśli dostarczenie nie powiedzie się, wiadomość jest umieszczana w kolejce 1-minutowej i ponawiana po minucie.
1. Jeśli dostarczenie nadal zakończy się niepowodzeniem, wiadomość jest umieszczana w kolejce 10-minutowej i ponawiana co 10 minut.
1. Dostawy są podejmowane do czasu osiągnięcia limitów zasad po pomyślnym lub ponowić próbę.

## <a name="retry-policy-limits"></a>Ponowij ponawianie limitów zasad

Istnieją dwie konfiguracje, które określają zasady ponawiania. Oto one:

* Maksymalna liczba prób
* Czas wygaśnięcia wydarzenia (TTL)

Zdarzenie zostanie przerwane, jeśli zostanie osiągnięty jeden z limitów zasad ponawiania. Sam harmonogram ponawiania prób został opisany w sekcji Harmonogram ponawiania prób. Konfiguracja tych limitów może być wykonana dla wszystkich subskrybentów lub dla podstawy subskrypcji. W poniższej sekcji opisano każdy z nich jest dalsze szczegóły.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurowanie ustawień domyślnych dla wszystkich subskrybentów

Istnieją dwie właściwości: `brokers__defaultMaxDeliveryAttempts` `broker__defaultEventTimeToLiveInSeconds` i które mogą być skonfigurowane jako część wdrożenia usługi Event Grid, która kontroluje domyślne zasady ponawiania dla wszystkich subskrybentów.

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maksymalna liczba prób dostarczenia zdarzenia. Wartość domyślna: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Czas wygaśnięcia zdarzenia w sekundach, po którym zdarzenie zostanie przerwane, jeśli nie zostanie dostarczone. Wartość domyślna: **7200** sekund

## <a name="configuring-defaults-per-subscriber"></a>Konfigurowanie ustawień domyślnych na subskrybenta

Można również określić limity zasad ponawiania próby dla na podstawie subskrypcji.
Zapoznaj się z naszą [dokumentacją interfejsu API,](api.md) aby uzyskać informacje na temat konfigurowania ustawień domyślnych na subskrybenta. Domyślne ustawienia poziomu subskrypcji zastępują konfiguracje poziomu modułu.

## <a name="examples"></a>Przykłady

W poniższym przykładzie konfiguruje zasady ponawiania prób w module Siatka zdarzeń z maxNumberOfAttempts = 3 i 30 minut czasu wygaśnięcia zdarzeń

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
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

W poniższym przykładzie konfiguruje subskrypcję haka sieci Web z maxNumberOfAttempts = 3 i czas wygaśnięcia zdarzenia 30 minut

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
