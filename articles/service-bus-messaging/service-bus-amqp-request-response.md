---
title: Protokołu AMQP 1.0 w operacjach na podstawie odpowiedzi żądań usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Lista operacji/odpowiedzi na podstawie żądań usługi Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c22ba0b57ed1161e1f7e2082d2ba21f27b656da1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402687"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Protokołu AMQP 1.0 w usłudze Microsoft Azure Service Bus: operacje na podstawie odpowiedzi żądań

W tym artykule definiuje listę operacji/odpowiedzi na podstawie żądań usługi Microsoft Azure Service Bus. Te informacje opiera się na temat protokołu AMQP 1.0 w wersji zarządzania pracy projektu.  
  
Szczegółowy poziom danych przesyłanych w sieci protokołu AMQP 1.0 protokołu przewodnik, co wyjaśnia, jak Usługa Service Bus implementuje i opiera się na specyfikacji technicznej OASIS protokołu AMQP, zobacz [protokołu AMQP 1.0 w przewodniku protokołu usługi Azure Service Bus i usługi Event Hubs][przewodnik dotyczący protokołu amqp 1.0].  
  
## <a name="concepts"></a>Pojęcia  
  
### <a name="entity-description"></a>Opis jednostki  

Opis jednostki odwołuje się do jednej usługi Service Bus [klasy QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [klasy TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription), lub [klasy SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) obiektu.  
  
### <a name="brokered-message"></a>Komunikatu obsługiwanego przez brokera  

Reprezentuje komunikat w usłudze Service Bus, która jest mapowana do wiadomości protokołu AMQP. Mapowanie jest zdefiniowany w [przewodnik dotyczący protokołu AMQP usługi Service Bus](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Dołącz do węzła zarządzania jednostki  

Wszystkie operacje opisane w niniejszym dokumencie wzorca żądanie/odpowiedź, są ograniczone do jednostki i wymagają dołączanie do węzła zarządzania obiektu.  
  
### <a name="create-link-for-sending-requests"></a>Utworzyć link do wysyłania żądań  

Tworzy łącze do węzła zarządzania, wysyłania żądań.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Utwórz łącze do odbierania odpowiedzi  

Tworzy łącze do odbierania odpowiedzi z węzła zarządzania.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Komunikat żądania przeniesienia  

Przesyłanie komunikatu żądania.  
Stan transakcji można opcjonalnie dodać dla operacji, które obsługuje transakcji.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Komunikat odpowiedzi  

Odbiera komunikat odpowiedzi z linku odpowiedzi.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Komunikat odpowiedzi jest w następującej postaci:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Adres jednostki usługi Service Bus  

Jednostki usługi Service Bus muszą być kierowane w następujący sposób:  
  
|Typ jednostki|Adres|Przykład|  
|-----------------|-------------|-------------|  
|kolejka|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|temat|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subskrypcja|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operacje dotyczące wiadomości  
  
### <a name="message-renew-lock"></a>Komunikat odnowienia blokady  

Rozszerza blokadę komunikatu przez czas określony w opis jednostki.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
 Treść żądania musi składać się z sekcji wartość amqp mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|Tablica identyfikator uuid|Yes|Tokeny blokady komunikatu do odnowienia.|  

> [!NOTE]
> Tokeny blokady są `DeliveryTag` właściwość Odebrane komunikaty. Zobacz poniższy przykład w [zestawu .NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) która pobiera je. Token może być również wyświetlane w DeliveryAnnotations jako "x-opt —-token blokady" jednak, to nie jest gwarantowana i `DeliveryTag` powinny być preferowane. 
> 
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się.|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z sekcji wartość amqp mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|wygasanie ważności poświadczeń|Tablica znacznik czasu:|Yes|Komunikat o blokady nowe wygaśnięcia tokenu odpowiadający żądania tokenów blokady.|  
  
### <a name="peek-message"></a>Wgląd do wiadomości  

Dokonuje wiadomości bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|długi|Yes|Numer sekwencji, który ma wyznaczać początek podglądu.|  
|`message-count`|int|Yes|Maksymalna liczba wiadomości do wglądu.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 204: Brak zawartości — dalszych komunikatów|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista mapy|Yes|Lista komunikatów, w których każdy mapy reprezentuje komunikat.|  
  
Mapa reprezentujący wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|message|Tablica bajtów|Yes|Komunikat o komunikacji sieciowej zakodowane w formacie protokołu AMQP 1.0.|  
  
### <a name="schedule-message"></a>Harmonogram wiadomości  

Planuje wiadomości. Ta operacja obsługuje transakcji.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista mapy|Yes|Lista komunikatów, w których każdy mapy reprezentuje komunikat.|  
  
Mapa reprezentujący wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|message-id|string|Yes|`amqpMessage.Properties.MessageId` jako ciąg|  
|session-id|string|Nie|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|Nie|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|string|Nie|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|Tablica bajtów|Yes|Komunikat o komunikacji sieciowej zakodowane w formacie protokołu AMQP 1.0.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się.|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcji mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica long|Yes|Numer sekwencyjny zaplanowane wiadomości. Numer sekwencji jest używany do anulowania.|  
  
### <a name="cancel-scheduled-message"></a>Anulowanie zaplanowanych wiadomości  

Anuluje zaplanowane wiadomości.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica long|Yes|Numery sekwencyjne zaplanowane wiadomości, aby anulować.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się.|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcji mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica long|Yes|Numer sekwencyjny zaplanowane wiadomości. Numer sekwencji jest używany do anulowania.|  
  
## <a name="session-operations"></a>Operacje sesji  
  
### <a name="session-renew-lock"></a>Sesja odnowienia blokady  

Rozszerza blokadę komunikatu przez czas określony w opis jednostki.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Yes|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 204: Brak zawartości — dalszych komunikatów|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcji mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|wygaśnięcie|timestamp|Yes|Nowe wygaśnięcia.|  
  
### <a name="peek-session-message"></a>Wgląd do wiadomości sesji  

Dokonuje komunikaty sesji bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|długi|Yes|Numer sekwencji, który ma wyznaczać początek podglądu.|  
|message-count|int|Yes|Maksymalna liczba wiadomości do wglądu.|  
|session-id|string|Yes|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 204: Brak zawartości — dalszych komunikatów|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcji mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista mapy|Yes|Lista komunikatów, w których każdy mapy reprezentuje komunikat.|  
  
 Mapa reprezentujący wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|message|Tablica bajtów|Yes|Komunikat o komunikacji sieciowej zakodowane w formacie protokołu AMQP 1.0.|  
  
### <a name="set-session-state"></a>Ustaw stan sesji  

Ustawia stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Yes|Identyfikator sesji.|  
|Stan sesji|Tablica bajtów|Yes|Nieprzezroczysty dane binarne.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|Opisstanu|string|Nie|Opis stanu.|  
  
### <a name="get-session-state"></a>Pobierz stan sesji  

Pobiera stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Yes|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Stan sesji|Tablica bajtów|Yes|Nieprzezroczysty dane binarne.|  
  
### <a name="enumerate-sessions"></a>Wyliczanie sesji  

Wylicza sesji na jednostki obsługi komunikatów.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|ostatnio zaktualizowane w czasie|timestamp|Yes|Filtr obejmujący tylko sesje zaktualizowane po określonym czasie.|  
|pomiń|int|Yes|Pomiń liczbę sesji.|  
|top|int|Yes|Maksymalna liczba sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 204: Brak zawartości — dalszych komunikatów|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|pomiń|int|Yes|Liczba pominiętych sesji, jeśli kod stanu 200.|  
|sessions-ids|Tablica ciągów|Yes|Tablica identyfikatorów, jeśli kod stanu 200.|  
  
## <a name="rule-operations"></a>Reguły operacji  
  
### <a name="add-rule"></a>Dodaj regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Nazwa reguły|string|Yes|Nazwa reguły, nie wliczając nazw subskrypcji i tematów.|  
|rule-description|map|Yes|Opis reguły określone w następnej sekcji.|  
  
**Opis reguły** Mapa musi zawierać następujące wpisy, których **filtrem sql** i **filtr korelacji** wzajemnie się wykluczają:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Yes|`sql-filter`, jak określono w następnej sekcji.|  
|correlation-filter|map|Yes|`correlation-filter`, jak określono w następnej sekcji.|  
|sql-rule-action|map|Yes|`sql-rule-action`, jak określono w następnej sekcji.|  
  
Mapa filtrem sql muszą znajdować się następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|expression|string|Yes|Wyrażenie filtru SQL.|  
  
**Filtr korelacji** Mapa musi zawierać co najmniej jeden z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|Nie||  
|message-id|string|Nie||  
|na|string|Nie||  
|Odpowiedz do|string|Nie||  
|label|string|Nie||  
|session-id|string|Nie||  
|reply-to-session-id|string|Nie||  
|Typ zawartości|string|Nie||  
|properties|map|Nie|Mapuje do usługi Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
**Akcji w przypadku reguły sql** Mapa musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|expression|string|Yes|Wyrażenie akcji SQL.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|Opisstanu|string|Nie|Opis stanu.|  
  
### <a name="remove-rule"></a>Usuń regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Nazwa reguły|string|Yes|Nazwa reguły, nie wliczając nazw subskrypcji i tematów.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|Opisstanu|string|Nie|Opis stanu.|  
  
### <a name="get-rules"></a>Pobierz reguły

#### <a name="request"></a>Żądanie

Komunikat żądania musi zawierać następujące właściwości aplikacji:

|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  

Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|top|int|Yes|Liczba reguł, które można pobrać na stronie.|  
|pomiń|int|Yes|Liczba reguł, aby pominąć. Definiuje indeks początkowy (+ 1) na liście reguł. | 

#### <a name="response"></a>Odpowiedź

Komunikat odpowiedzi zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|rules| Tablica mapy|Yes|Tablica reguły. Każda reguła jest reprezentowany przez mapę.|

Każdy wpis mapy w tablicy zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|rule-description|Tablica obiektów opisem|Yes|`com.microsoft:rule-description:list` z obsługą protokołu AMQP opisem kodu 0x0000013700000004| 

`com.microsoft.rule-description:list` jest tablicą obiektów opisem. Tablica obejmuje następujące funkcje:

|Indeks|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
| 0 | Tablica obiektów opisem | Yes | `filter` wyszczególnione poniżej. |
| 1 | Tablica obiektu opisem | Yes | `ruleAction` wyszczególnione poniżej. |
| 2 | string | Yes | Nazwa reguły. |

`filter` może być jedną z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr programu SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtr korelacji |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Wartość true, filtr reprezentujący 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | FALSE filtr reprezentujący 1 = 0 |

`com.microsoft:sql-filter:list` jest tablicą opisanych w tym:

|Indeks|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Yes | Wyrażenie filtru SQL |

`com.microsoft:correlation-filter:list` jest tablicą opisanych w tym:

|Indeks (jeśli istnieje)|Typ wartości|Wartość zawartości|  
|---------|----------------|--------------|
| 0 | string | Identyfikator korelacji |
| 1 | string | Identyfikator komunikatu |
| 2 | string | Do |
| 3 | string | Odpowiedz |
| 4 | string | Label |
| 5 | string | Identyfikator sesji |
| 6 | string | Identyfikator sesji dla odpowiedzi|
| 7 | string | Typ zawartości |
| 8 | Mapa | Mapy właściwości określone w aplikacji |

`ruleAction` może być jedną z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Akcja reguły pusty - nie istnieje akcja reguły |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Akcja reguły SQL |

`com.microsoft:sql-rule-action:list` jest tablicą obiektów opisany, którego pierwszy wpis jest to ciąg, który zawiera wyrażenie SQL Akcja reguły.

## <a name="deferred-message-operations"></a>Operacje odroczony komunikat  
  
### <a name="receive-by-sequence-number"></a>Odbieranie przez numer sekwencyjny  

Odbiera komunikaty odroczonego numerem sekwencji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica long|Yes|Numery sekwencyjne.|  
|receiver-settle-mode|ubyte|Yes|**Odbiornik rozliczania** trybu, jak to określono w AMQP core 1.0.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|Opisstanu|string|Nie|Opis stanu.|  
  
Treści komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista mapy|Yes|Lista komunikatów, gdzie każdy mapy reprezentuje komunikat.|  
  
Mapa reprezentujący wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|token blokady|uuid|Yes|Jeśli token blokady `receiver-settle-mode` 1.|  
|message|Tablica bajtów|Yes|Komunikat o komunikacji sieciowej zakodowane w formacie protokołu AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Zaktualizuj stan dyspozycji  

Aktualizuje stan dyspozycji odroczonej wiadomości. Ta operacja obsługuje transakcje.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|operacja|string|Yes|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Stan dyspozycji|string|Yes|completed<br /><br /> porzucone<br /><br /> Zawieszone|  
|tokeny blokady|Tablica identyfikator uuid|Yes|Tokeny blokady wiadomości do dyspozycji stanu aktualizacji.|  
|deadletter-reason|string|Nie|Może być ustawiona, jeśli ustawiono stan dyspozycji **zawieszone**.|  
|deadletter-description|string|Nie|Może być ustawiona, jeśli ustawiono stan dyspozycji **zawieszone**.|  
|właściwości modyfikować|map|Nie|Lista usługi Service Bus obsługiwanych przez brokera właściwości wiadomości, aby zmodyfikować.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|Opisstanu|string|Nie|Opis stanu.|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołów AMQP oraz usługi Service Bus, skorzystaj z następujących linków:

* [Omówienie AMQP usługi Service Bus]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [Protokół AMQP w usłudze Service Bus dla systemu Windows Server]

[Omówienie AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[Protokół AMQP w usłudze Service Bus dla systemu Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
