---
title: Konfiguracja — IoT Edge Azure Event Grid | Microsoft Docs
description: Konfiguracja w Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992433"
---
# <a name="event-grid-configuration"></a>Konfiguracja Event Grid

Event Grid udostępnia wiele konfiguracji, które można modyfikować dla poszczególnych środowisk. Poniższa sekcja zawiera odwołania do wszystkich dostępnych opcji i ich wartości domyślnych.

## <a name="tls-configuration"></a>Konfiguracja protokołu TLS

Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady ich użycia można znaleźć w [tym artykule](configure-api-protocol.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Zasady protokołu TLS modułu Event Grid. Wartość domyślna to tylko HTTPS.
|`inbound:serverAuth:serverCert:source`| Źródło certyfikatu serwera używane przez moduł Event Grid do konfiguracji protokołu TLS. Wartość domyślna to IoT Edge.

## <a name="incoming-client-authentication"></a>Przychodzące uwierzytelnianie klienta

Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-client-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Aby włączyć/wyłączyć uwierzytelnianie klienta oparte na certyfikatach. Wartość domyślna to true.
|`inbound:clientAuth:clientCert:source`| Źródło do sprawdzania poprawności certyfikatów klienta. Wartość domyślna to IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Zasady zezwalające na certyfikat klienta z podpisem własnym. Wartość domyślna to true.
|`inbound:clientAuth:sasKeys:enabled`| Aby włączyć/wyłączyć uwierzytelnianie klienta oparte na kluczu SAS. Wartość domyślna to off.
|`inbound:clientAuth:sasKeys:key1`| Jedna z wartości, aby zweryfikować przychodzące żądania.
|`inbound:clientAuth:sasKeys:key2`| Opcjonalna druga wartość do walidacji żądań przychodzących.

## <a name="outgoing-client-authentication"></a>Uwierzytelnianie klienta wychodzącego
Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-identity-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Aby włączyć/wyłączyć dołączanie certyfikatu tożsamości dla żądań wychodzących. Wartość domyślna to true.
|`outbound:clientAuth:clientCert:source`| Źródło pobierania certyfikatu wychodzącego modułu Event Grid. Wartość domyślna to IoT Edge.

## <a name="webhook-event-handlers"></a>Procedury obsługi zdarzeń elementu webhook

Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-webhook-subscriber-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Zasady umożliwiające kontrolowanie, czy dozwolone są tylko subskrybenci HTTPS. Wartość domyślna to true (tylko HTTPS).
|`outbound:webhook:skipServerCertValidation`| Oflaguj, aby kontrolować certyfikat subskrybenta. Wartość domyślna to true.
|`outbound:webhook:allowUnknownCA`| Zasady umożliwiające kontrolowanie, czy certyfikat z podpisem własnym może być prezentowany przez subskrybenta. Wartość domyślna to true. 

## <a name="delivery-and-retry"></a>Dostarczanie i ponawianie prób

Aby uzyskać ogólne informacje na temat tej funkcji, zobacz [dostarczanie i ponawianie próby](delivery-retry.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Maksymalna liczba prób dostarczenia zdarzenia. Wartość domyślna to 30.
| `broker:defaultEventTimeToLiveInSeconds` | Czas wygaśnięcia (TTL) w sekundach, po upływie którego zdarzenie zostanie usunięte, jeśli nie zostanie dostarczone. Wartość domyślna to **7200** sekund

## <a name="output-batching"></a>Przetwarzanie wsadowe danych wyjściowych

Aby uzyskać ogólne informacje na temat tej funkcji, zobacz [dostarczanie i wyprowadzanie wsadowe](delivery-output-batching.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Maksymalna dozwolona wartość dla pokrętła `ApproxBatchSizeInBytes`. Wartość domyślna to `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Maksymalna dozwolona wartość dla pokrętła `MaxEventsPerBatch`. Wartość domyślna to `50`.
| `broker:defaultMaxBatchSizeInBytes` | Maksymalny rozmiar żądania dostarczania, gdy jest określony tylko `MaxEventsPerBatch`. Wartość domyślna to `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Maksymalna liczba zdarzeń do dodania do partii, gdy zostanie określona tylko `MaxBatchSizeInBytes`. Wartość domyślna to `10`.
