---
title: Konfiguracja — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Konfiguracja w siatce zdarzeń na ioT edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846475"
---
# <a name="event-grid-configuration"></a>Konfiguracja siatki zdarzeń

Usługa Event Grid udostępnia wiele konfiguracji, które można modyfikować w pogoń za środowisko. Poniższa sekcja jest odwołaniem do wszystkich dostępnych opcji i ich ustawień domyślnych.

## <a name="tls-configuration"></a>Konfiguracja protokołu TLS

Aby dowiedzieć się więcej o uwierzytelniania klienta w ogóle, zobacz [Zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady jego użycia można znaleźć w [tym artykule](configure-api-protocol.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Zasady TLS modułu siatki zdarzeń. Wartość domyślna to tylko protokół HTTPS.
|`inbound__serverAuth__serverCert__source`| Źródło certyfikatu serwera używanego przez moduł siatki zdarzeń dla jego konfiguracji TLS. Wartością domyślną jest IoT Edge.

## <a name="incoming-client-authentication"></a>Uwierzytelnianie klienta przychodzącego

Aby dowiedzieć się więcej o uwierzytelniania klienta w ogóle, zobacz [Zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-client-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Aby włączyć/wyłączyć uwierzytelnianie klienta oparte na certyfikatach. Wartość domyślna jest true.
|`inbound__clientAuth__clientCert__source`| Źródło sprawdzania poprawności certyfikatów klientów. Wartością domyślną jest IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Zasady zezwalania na certyfikat klienta z podpisem własnym. Wartość domyślna jest true.
|`inbound__clientAuth__sasKeys__enabled`| Aby włączyć/wyłączyć uwierzytelnianie klienta oparte na kluczu SAS. Wartość domyślna jest wyłączona.
|`inbound__clientAuth__sasKeys__key1`| Jedna z wartości do sprawdzania poprawności żądań przychodzących.
|`inbound__clientAuth__sasKeys__key2`| Opcjonalna druga wartość do sprawdzania poprawności żądań przychodzących.

## <a name="outgoing-client-authentication"></a>Uwierzytelnianie klienta wychodzącego
Aby dowiedzieć się więcej o uwierzytelniania klienta w ogóle, zobacz [Zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-identity-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Aby włączyć/wyłączyć dołączanie certyfikatu tożsamości dla żądań wychodzących. Wartość domyślna jest true.
|`outbound__clientAuth__clientCert__source`| Źródło pobierania certyfikatu wychodzącego modułu Event Grid. Wartością domyślną jest IoT Edge.

## <a name="webhook-event-handlers"></a>Programy obsługi zdarzeń elementu Webhook

Aby dowiedzieć się więcej o uwierzytelniania klienta w ogóle, zobacz [Zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-webhook-subscriber-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Zasady, aby kontrolować, czy tylko subskrybenci HTTPS będą dozwolone. Wartość domyślna jest true (tylko HTTPS).
|`outbound__webhook__skipServerCertValidation`| Flaga, aby kontrolować, czy sprawdzić poprawność certyfikatu subskrybenta. Wartość domyślna jest true.
|`outbound__webhook__allowUnknownCA`| Zasady do kontrolowania, czy certyfikat z podpisem własnym mogą być prezentowane przez subskrybenta. Wartość domyślna jest true. 

## <a name="delivery-and-retry"></a>Dostarczanie i ponawianie prób

Aby dowiedzieć się więcej o tej funkcji, zobacz [Dostarczanie i Ponów próbę](delivery-retry.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maksymalna liczba prób dostarczenia zdarzenia. Wartość domyślna to 30.
| `broker__defaultEventTimeToLiveInSeconds` | Czas wygaśnięcia (TTL) w sekundach, po czym zdarzenie zostanie przerwane, jeśli nie zostanie dostarczone. Wartość domyślna to **7200** sekund

## <a name="output-batching"></a>Dzielenie na partie danych wyjściowych

Aby dowiedzieć się więcej o tej funkcji w ogóle, zobacz [Dostarczanie i produkcja wsadowa](delivery-output-batching.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maksymalna dopuszczalna `ApproxBatchSizeInBytes` wartość dla pokrętła. Wartością `1_058_576`domyślną jest .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maksymalna dopuszczalna `MaxEventsPerBatch` wartość dla pokrętła. Wartością `50`domyślną jest .
| `broker__defaultMaxBatchSizeInBytes` | Maksymalny rozmiar żądania `MaxEventsPerBatch` dostawy, gdy jest określony tylko. Wartością `1_058_576`domyślną jest .
| `broker__defaultMaxEventsPerBatch` | Maksymalna liczba zdarzeń do dodania `MaxBatchSizeInBytes` do partii, gdy jest określona tylko. Wartością `10`domyślną jest .

## <a name="metrics"></a>Metryki

Aby dowiedzieć się więcej o korzystaniu z danych z siatką zdarzeń w usłudze IoT Edge, zobacz [tematy monitoru i subskrypcje](monitor-topics-subscriptions.md)

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `metrics__reporterType` | Typ reportera dla punktu metryki. Domyślnie `none` jest i wyłącza metryki. Ustawienie `prometheus` umożliwiające wyświetlanie danych w formacie ekspozycji Prometheus.