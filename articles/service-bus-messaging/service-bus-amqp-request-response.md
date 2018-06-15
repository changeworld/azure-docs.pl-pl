---
title: Protokołu AMQP 1.0 w operacjach opartych na protokole żądanie odpowiedź usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Lista operacji oparte na żądanie/odpowiedź systemu Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: 847fe0c08d442388cfa506042272bb358058cb4c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194705"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Protokołu AMQP 1.0 w systemie Microsoft Azure Service Bus: operacje na podstawie odpowiedzi żądań

W tym artykule definiuje listę operacji oparte na żądanie/odpowiedź systemu Microsoft Azure Service Bus. Te informacje opiera się na projekt pracy protokołu AMQP 1.0 wersji zarządzania.  
  
Szczegółowy poziom danych przesyłanych w sieci protokołu AMQP 1.0 protokołu przewodnik, co wyjaśnia, jak Usługa Service Bus implementuje i opiera się na specyfikacji technicznej OASIS protokołu AMQP, zobacz [protokołu AMQP 1.0 w przewodniku protokołu usługi Azure Service Bus i usługi Event Hubs][Przewodnik dotyczący protokołu AMQP 1.0].  
  
## <a name="concepts"></a>Pojęcia  
  
### <a name="entity-description"></a>Opis jednostki  

Opis jednostki odnosi się do albo usługi Service Bus [klasy QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [klasy TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription), lub [klasy SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) obiektu.  
  
### <a name="brokered-message"></a>Obsługiwane przez brokera komunikatów.  

Reprezentuje komunikat w usłudze Service Bus, która jest mapowana na komunikat protokołu AMQP. Mapowanie jest zdefiniowany w [przewodnik protokołu AMQP magistrali usługi](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Dołączanie do węzła zarządzania jednostki  

Wszystkie czynności opisane w tym dokumencie wykonaj wzorzec żądanie/odpowiedź, ograniczone do jednostki i wymagają dołączanie do węzła zarządzania jednostki.  
  
### <a name="create-link-for-sending-requests"></a>Utwórz łącze do wysyłania żądań  

Tworzy łącze do węzła zarządzania do wysyłania żądań.  
  
```  
requestLink = session.attach(     
role: SENDER,   
    target: { address: "<entity address>/$management" },   
    source: { address: ""<my request link unique address>" }   
)  
  
```  
  
### <a name="create-link-for-receiving-responses"></a>Utwórz łącze do odbierania odpowiedzi  

Tworzy łącze do odbierania odpowiedzi od węzła zarządzania.  
  
```  
responseLink = session.attach(    
role: RECEIVER,   
    source: { address: "<entity address>/$management" }   
    target: { address: "<my response link unique address>" }   
)  
  
```  
  
### <a name="transfer-a-request-message"></a>Komunikat żądania przeniesienia  

Przesyła komunikat żądania.  
Stan transakcji można dodawać opcjonalnie dla działań, które obsługuje transakcji.

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

Odbiera komunikat odpowiedzi z łącza odpowiedzi.  
  
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
  
### <a name="service-bus-entity-address"></a>Adres jednostki magistrali usług  

Jednostki usługi Service Bus muszą być kierowane w następujący sposób:  
  
|Typ jednostki|Adres|Przykład|  
|-----------------|-------------|-------------|  
|Kolejki|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|Temat|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subskrypcja|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operacje dotyczące komunikatów  
  
### <a name="message-renew-lock"></a>Komunikat odnowienia blokady  

Rozszerza blokady wiadomości przez czas określony w opisie jednostki.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
 Treść komunikatu żądania musi składać się z sekcji wartość amqp mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|Tablica uuid|Yes|Tokeny blokady komunikat odnowienia.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się.|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z sekcji wartość amqp mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|wygasanie|Tablica sygnatury czasowej|Yes|Komunikat blokady nowe wygaśnięcia tokenu odpowiadający żądania tokenów blokady.|  
  
### <a name="peek-message"></a>Wgląd do wiadomości  

Dokonuje wiadomości bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|długa|Yes|Numer sekwencji, w którym można uruchomić podglądu.|  
|`message-count`|int|Yes|Maksymalna liczba komunikatów do wglądu.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 0xcc: Brak zawartości — dalszych komunikatów|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Yes|Lista komunikatów, w których każdy mapy reprezentuje komunikat.|  
  
Mapa reprezentujący wiadomości musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|message|Tablica bajtów|Yes|Komunikat protokołu AMQP 1.0 kodowany w formacie danych przesyłanych w sieci.|  
  
### <a name="schedule-message"></a>Komunikat harmonogramu  

Planuje wiadomości. Ta operacja obsługuje transakcji.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Yes|Lista komunikatów, w których każdy mapy reprezentuje komunikat.|  
  
Mapa reprezentujący wiadomości musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator komunikatu|ciąg|Yes|`amqpMessage.Properties.MessageId` jako ciąg|  
|Identyfikator sesji|ciąg|Nie|`amqpMessage.Properties.GroupId as string`|  
|Klucz partycji|ciąg|Nie|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|za pomocą —-klucza partycji|ciąg|Nie|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|Tablica bajtów|Yes|Komunikat protokołu AMQP 1.0 kodowany w formacie danych przesyłanych w sieci.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się.|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcja mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numerów sekwencji.|Tablica long|Yes|Numer sekwencyjny zaplanowane wiadomości. Numer sekwencji jest używany do anulowania.|  
  
### <a name="cancel-scheduled-message"></a>Anulowanie zaplanowanego wiadomości  

Anuluje zaplanowane wiadomości.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numerów sekwencji.|Tablica long|Yes|Numerów sekwencyjnych zaplanowane wiadomości, aby anulować.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się.|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcja mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numerów sekwencji.|Tablica long|Yes|Numer sekwencyjny zaplanowane wiadomości. Numer sekwencji jest używany do anulowania.|  
  
## <a name="session-operations"></a>Operacje sesji  
  
### <a name="session-renew-lock"></a>Sesja odnawiania blokady  

Rozszerza blokady wiadomości przez czas określony w opisie jednostki.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator sesji|ciąg|Yes|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 0xcc: Brak zawartości — dalszych komunikatów|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcja mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|wygaśnięcie|sygnatura czasowa|Yes|Nowe wygaśnięcia.|  
  
### <a name="peek-session-message"></a>Wgląd do wiadomości sesji  

Dokonuje komunikaty sesji bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|długa|Yes|Numer sekwencji, w którym można uruchomić podglądu.|  
|message-count|int|Yes|Maksymalna liczba komunikatów do wglądu.|  
|Identyfikator sesji|ciąg|Yes|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 0xcc: Brak zawartości — dalszych komunikatów|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **amqp wartość** sekcja mapy z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Yes|Lista komunikatów, w których każdy mapy reprezentuje komunikat.|  
  
 Mapa reprezentujący wiadomości musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|message|Tablica bajtów|Yes|Komunikat protokołu AMQP 1.0 kodowany w formacie danych przesyłanych w sieci.|  
  
### <a name="set-session-state"></a>Ustaw stan sesji  

Ustawia stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator sesji|ciąg|Yes|Identyfikator sesji.|  
|Stan sesji|Tablica bajtów|Yes|Nieprzezroczysta danych binarnych.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
### <a name="get-session-state"></a>Pobierz stan sesji  

Pobiera stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator sesji|ciąg|Yes|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Stan sesji|Tablica bajtów|Yes|Nieprzezroczysta danych binarnych.|  
  
### <a name="enumerate-sessions"></a>Wyliczenie sesji  

Wylicza sesji na jednostki obsługi komunikatów.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|ostatnio zaktualizowano czasu|sygnatura czasowa|Yes|Filtrowane w celu uwzględniania tylko sesje zaktualizowane po określonym czasie.|  
|Pomiń|int|Yes|Pomiń liczbę sesji.|  
|top|int|Yes|Maksymalna liczba sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — ma więcej wiadomości<br /><br /> 0xcc: Brak zawartości — dalszych komunikatów|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Pomiń|int|Yes|Liczba pominiętych sesji w przypadku kodu stanu 200.|  
|identyfikatory sesji|Tablica ciągów|Yes|Tablica sesji identyfikatory przypadku kodu stanu 200.|  
  
## <a name="rule-operations"></a>Reguły operacji  
  
### <a name="add-rule"></a>Dodaj regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Nazwa reguły|ciąg|Yes|Nazwa reguły nie tym nazwy subskrypcji i tematu.|  
|Opis reguły|map|Yes|Opis reguły określone w następnej sekcji.|  
  
**Opis reguły** Mapa musi zawierać następujące wpisy, gdzie **filtrem sql** i **filtru korelacji** wykluczają się wzajemnie:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Yes|`sql-filter`, jak określono w następnej sekcji.|  
|Filtr korelacji|map|Yes|`correlation-filter`, jak określono w następnej sekcji.|  
|sql-rule-action|map|Yes|`sql-rule-action`, jak określono w następnej sekcji.|  
  
Mapa filtrem sql musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|wyrażenie|ciąg|Yes|Wyrażenie filtru SQL.|  
  
**Filtru korelacji** Mapa musi zawierać co najmniej jedną z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator korelacji|ciąg|Nie||  
|Identyfikator komunikatu|ciąg|Nie||  
|na|ciąg|Nie||  
|Odpowiedz do|ciąg|Nie||  
|Etykiety|ciąg|Nie||  
|Identyfikator sesji|ciąg|Nie||  
|Odpowiedz do sesji id|ciąg|Nie||  
|Typ zawartości|ciąg|Nie||  
|properties|map|Nie|Mapowany do usługi Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties).|  
  
**Sql reguły akcji** Mapa musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|wyrażenie|ciąg|Yes|Wyrażenie akcji SQL.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
### <a name="remove-rule"></a>Usuń regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Nazwa reguły|ciąg|Yes|Nazwa reguły nie tym nazwy subskrypcji i tematu.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
### <a name="get-rules"></a>Pobieranie reguł

#### <a name="request"></a>Żądanie

Komunikat żądania musi obejmować następujące właściwości aplikacji:

|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  

Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|top|int|Yes|Liczba reguł do pobrania na stronie.|  
|Pomiń|int|Yes|Liczba reguł do pominięcia. Określa indeks początkowy (+ 1) na liście reguł. | 

#### <a name="response"></a>Odpowiedź

Komunikat odpowiedzi zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|rules| Tablica mapy|Yes|Tablica reguły. Każda reguła jest reprezentowana przez mapy.|

Każdy wpis mapy w tablicy ma następujące właściwości:

|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Opis reguły|Tablica obiektów opisem|Yes|`com.microsoft:rule-description:list` z protokołu AMQP opisano kod 0x0000013700000004| 

`com.microsoft.rule-description:list` to jest Tablica obiektów opisem. Tablica obejmuje następujące funkcje:

|Indeks|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
| 0 | Tablica obiektów opisem | Yes | `filter` wyszczególnione poniżej. |
| 1 | Tablica opisane obiektu | Yes | `ruleAction` wyszczególnione poniżej. |
| 2 | ciąg | Yes | Nazwa reguły. |

`filter` może być jedną z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtr korelacji |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Wartość true, filtr reprezentujący 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | FALSE filtr reprezentujący 1 = 0 |

`com.microsoft:sql-filter:list` jest tablicą opisane w tym:

|Indeks|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
| 0 | ciąg | Yes | Wyrażenie filtru SQL |

`com.microsoft:correlation-filter:list` jest tablicą opisane w tym:

|Indeks (jeśli istnieje)|Typ wartości|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
| 0 | ciąg | Identyfikator korelacji |
| 1 | ciąg | Identyfikator komunikatu |
| 2 | ciąg | Do |
| 3 | ciąg | Odpowiedz |
| 4 | ciąg | Etykieta |
| 5 | ciąg | Identyfikator sesji |
| 6 | ciąg | Odpowiedz na identyfikator sesji|
| 7 | ciąg | Typ zawartości |
| 8 | Mapa | Mapy właściwości aplikacji |

`ruleAction` może być jedną z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Pusta akcja reguły — nie istnieje akcja reguły |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Akcja reguły SQL |

`com.microsoft:sql-rule-action:list` to jest Tablica obiektów opisane, którego pierwsza pozycja to ciąg, który zawiera wyrażenie SQL Akcja reguły.

## <a name="deferred-message-operations"></a>Operacje dotyczące komunikatów odroczone  
  
### <a name="receive-by-sequence-number"></a>Odbieranie numerem sekwencji  

Odbiera komunikaty odroczonego numerem sekwencji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|numerów sekwencji.|Tablica long|Yes|Numerów sekwencji.|  
|odbiornik rozliczania trybu|ubyte|Yes|**Odbiornik rozliczania** tryb zgodnie z protokołu AMQP 1.0 core.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|StatusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi muszą składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Yes|Lista komunikatów, gdzie każdy mapy reprezentuje komunikat.|  
  
Mapa reprezentujący wiadomości musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|token blokady|Identyfikator UUID|Yes|Jeśli token blokady `receiver-settle-mode` 1.|  
|message|Tablica bajtów|Yes|Komunikat protokołu AMQP 1.0 kodowany w formacie danych przesyłanych w sieci.|  
  
### <a name="update-disposition-status"></a>Aktualizacja stanu dyspozycji  

Aktualizuje stan dyspozycji odroczonego wiadomości. Ta operacja obsługuje transakcji.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi obejmować następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Operacja|ciąg|Yes|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu operacji serwera w milisekundach.|  
  
Treść komunikatu żądania musi składać się z **wartość amqp** sekcji zawierającej **mapy** z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|Stan dyspozycji|ciąg|Yes|ukończono<br /><br /> porzucone<br /><br /> Zawieszone|  
|tokeny blokady|Tablica uuid|Yes|Tokeny blokady komunikat do dyspozycji stanu aktualizacji.|  
|Przyczyna utraconych wiadomości|ciąg|Nie|Jeśli ustawiono stan dyspozycji można ustawić **zawieszone**.|  
|Opis utraconych wiadomości|ciąg|Nie|Jeśli ustawiono stan dyspozycji można ustawić **zawieszone**.|  
|właściwości modyfikować|map|Nie|Wykaz usługi Service Bus obsługiwanych przez brokera właściwości wiadomości do zmodyfikowania.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi muszą zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Wartość zawartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kod odpowiedzi HTTP [specyfikacją RFC2616]<br /><br /> 200: OK — Powodzenie, w przeciwnym razie nie powiodło się|  
|StatusDescription|ciąg|Nie|Opis stanu.|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołu AMQP i usługi Service Bus, skorzystaj z następujących łączy:

* [Omówienie protokołu AMQP magistrali usług]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [Protokół AMQP w usłudze Service Bus dla systemu Windows Server]

[Omówienie protokołu AMQP magistrali usług]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[Protokół AMQP w usłudze Service Bus dla systemu Windows Server]: https://msdn.microsoft.com/library/dn574799.asp