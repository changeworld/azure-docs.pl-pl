---
title: Operacje żądania/odpowiedzi usługi AMQP 1.0 w usłudze Azure Service Bus
description: Ten artykuł definiuje listę operacji opartych na żądaniu/odpowiedzi protokołu AMQP w usłudze Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761087"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Usługa AMQP 1.0 w usłudze Microsoft Azure Service Bus: operacje oparte na odpowiedzi na żądanie

W tym artykule zdefiniowano listę operacji opartych na żądaniu/odpowiedzi usługi Microsoft Azure Service Bus. Te informacje są oparte na wersji roboczej roboczej zarządzania amqp w wersji 1.0.  
  
Aby uzyskać szczegółowy przewodnik protokołu AMQP 1.0 na poziomie przewodu, w którym wyjaśniono, w jaki sposób usługa Service Bus implementuje i opiera się na specyfikacji technicznej OASIS AMQP, zobacz[przewodnik protokołu AMQP] [1.0 usługi AMQP 1.0 w usłudze Azure Service Bus and Event Hubs].  
  
## <a name="concepts"></a>Pojęcia  
  
### <a name="entity-description"></a>Opis jednostki  

Opis jednostki odnosi się do klasy kolejki usługi Service [BusDescription,](/dotnet/api/microsoft.servicebus.messaging.queuedescription) [TopicDescription class](/dotnet/api/microsoft.servicebus.messaging.topicdescription)lub [SubscriptionDescription.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)  
  
### <a name="brokered-message"></a>Wiadomość brokera  

Reprezentuje komunikat w usłudze Service Bus, który jest mapowany na komunikat USŁUGI AMQP. Mapowanie jest zdefiniowane w [przewodniku protokołu AMQP usługi Service Bus](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Dołączanie do węzła zarządzania encją  

Wszystkie operacje opisane w tym dokumencie są zgodne ze wzorcem żądania/odpowiedzi, są ograniczone do jednostki i wymagają dołączania do węzła zarządzania jednostkami.  
  
### <a name="create-link-for-sending-requests"></a>Tworzenie łącza do wysyłania żądań  

Tworzy łącze do węzła zarządzania do wysyłania żądań.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Tworzenie łącza do odbierania odpowiedzi  

Tworzy łącze do odbierania odpowiedzi z węzła zarządzania.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Przenoszenie wiadomości z prośbą  

Przesyła wiadomość z żądaniem.  
Stan transakcji można dodać opcjonalnie dla operacji, które obsługuje transakcji.

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
  
### <a name="receive-a-response-message"></a>Odbierz wiadomość z odpowiedzią  

Odbiera wiadomość odpowiedzi z łącza odpowiedzi.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Komunikat odpowiedzi jest w następującej formie:
  
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

Jednostki usługi Service Bus muszą być adresowane w następujący sposób:  
  
|Typ jednostki|Adres|Przykład|  
|-----------------|-------------|-------------|  
|kolejka|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|temat|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subskrypcja|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operacje wiadomości  
  
### <a name="message-renew-lock"></a>Blokada odnawiania wiadomości  

Rozszerza blokadę wiadomości o czas określony w opisie jednostki.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
 Treść wiadomości żądania musi składać się z sekcji wartości amqp zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|tablica uuid|Tak|Tokeny blokady wiadomości do odnowienia.|  

> [!NOTE]
> Tokeny blokady `DeliveryTag` są właściwością w odebranych wiadomościach. Zobacz poniższy przykład w pliku [.NET SDK,](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) który je pobiera. Token może również pojawić się w "DeliveryAnnotations" jako "x-opt-lock-token", `DeliveryTag` jednak nie jest to gwarantowane i powinno być preferowane. 
> 
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się.|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji wartości amqp zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Wygasania|tablica sygnatury czasowej|Tak|Token blokady wiadomości nowy wygaśnięcia odpowiadający tokenów blokady żądania.|  
  
### <a name="peek-message"></a>Zajrzyj wiadomość  

Wgląd w wiadomości bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Tak|Numer sekwencyjny, od którego można rozpocząć wgląd.|  
|`message-count`|int|Tak|Maksymalna liczba wiadomości do wglądu.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – ma więcej wiadomości<br /><br /> 204: Brak treści – nie więcej wiadomości|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|lista map|Tak|Lista wiadomości, w których każda mapa reprezentuje wiadomość.|  
  
Mapa reprezentująca wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|message|tablica bajtów|Tak|Komunikat zakodowany przewodem AMQP 1.0.|  
  
### <a name="schedule-message"></a>Zaplanuj wiadomość  

Planuje wiadomości. Ta operacja obsługuje transakcję.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|lista map|Tak|Lista wiadomości, w których każda mapa reprezentuje wiadomość.|  
  
Mapa reprezentująca wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|identyfikator wiadomości|ciąg|Tak|`amqpMessage.Properties.MessageId`jako ciąg znaków|  
|identyfikator sesji|ciąg|Nie|`amqpMessage.Properties.GroupId as string`|  
|klucz partycji|ciąg|Nie|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|przez klucz partycji|ciąg|Nie|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|tablica bajtów|Tak|Komunikat zakodowany przewodem AMQP 1.0.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się.|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencowe|tablica długich|Tak|Numer sekwencyjny zaplanowanych wiadomości. Numer sekwencyjny służy do anulowania.|  
  
### <a name="cancel-scheduled-message"></a>Anulowanie zaplanowanej wiadomości  

Anuluje zaplanowane wiadomości.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencowe|tablica długich|Tak|Numery sekwencji zaplanowanych wiadomości do anulowania.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się.|  
|Statusdescription|ciąg|Nie|Opis stanu.|   
  
## <a name="session-operations"></a>Operacje sesji  
  
### <a name="session-renew-lock"></a>Blokada odnawiania sesji  

Rozszerza blokadę wiadomości o czas określony w opisie jednostki.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – ma więcej wiadomości<br /><br /> 204: Brak treści – nie więcej wiadomości|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Wygaśnięcia|sygnatura czasowa|Tak|Nowe wygaśnięcie.|  
  
### <a name="peek-session-message"></a>Komunikat sesji wglądu  

Wgląd w wiadomości sesji bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|od-sekwencja-numer|long|Tak|Numer sekwencyjny, od którego można rozpocząć wgląd.|  
|liczba wiadomości|int|Tak|Maksymalna liczba wiadomości do wglądu.|  
|identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – ma więcej wiadomości<br /><br /> 204: Brak treści – nie więcej wiadomości|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|lista map|Tak|Lista wiadomości, w których każda mapa reprezentuje wiadomość.|  
  
 Mapa reprezentująca wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|message|tablica bajtów|Tak|Komunikat zakodowany przewodem AMQP 1.0.|  
  
### <a name="set-session-state"></a>Ustawianie stanu sesji  

Ustawia stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
|stan sesji|tablica bajtów|Tak|Nieprzezroczyste dane binarne.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
### <a name="get-session-state"></a>Pobierz stan sesji  

Pobiera stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|stan sesji|tablica bajtów|Tak|Nieprzezroczyste dane binarne.|  
  
### <a name="enumerate-sessions"></a>Wyliczaj sesje  

Wylicza sesje w jednostce obsługi wiadomości.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|ostatnia aktualizacja|sygnatura czasowa|Tak|Filtruj, aby uwzględnić tylko sesje zaktualizowane po danym czasie.|  
|Pomiń|int|Tak|Pomiń liczbę sesji.|  
|top|int|Tak|Maksymalna liczba sesji.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – ma więcej wiadomości<br /><br /> 204: Brak treści – nie więcej wiadomości|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Pomiń|int|Tak|Liczba pominiętych sesji, jeśli kod stanu wynosi 200.|  
|sesje-identyfikatory|tablica ciągów|Tak|Tablica identyfikatorów sesji, jeśli kod stanu wynosi 200.|  
  
## <a name="rule-operations"></a>Operacje reguł  
  
### <a name="add-rule"></a>Dodaj regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|nazwa reguły|ciąg|Tak|Nazwa reguły, z wyłączeniem nazw subskrypcji i tematów.|  
|opis reguły|map|Tak|Opis reguły określony w następnej sekcji.|  
  
Mapa **opis reguły** musi zawierać następujące wpisy, gdzie **sql-filter** i **correlation-filter** wzajemnie się wykluczają:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|sql-filtr|map|Tak|`sql-filter`, jak określono w następnej sekcji.|  
|filtr korelacji|map|Tak|`correlation-filter`, jak określono w następnej sekcji.|  
|sql-rule-action|map|Tak|`sql-rule-action`, jak określono w następnej sekcji.|  
  
Mapa filtru sql musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|wyrażenie|ciąg|Tak|Wyrażenie filtru SQL.|  
  
Mapa **filtru korelacji** musi zawierać co najmniej jeden z następujących wpisów:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|correlation-id|ciąg|Nie||  
|identyfikator wiadomości|ciąg|Nie||  
|na|ciąg|Nie||  
|odpowiedź na|ciąg|Nie||  
|label|ciąg|Nie||  
|identyfikator sesji|ciąg|Nie||  
|identyfikator odpowiedzi na sesję|ciąg|Nie||  
|typ zawartości|ciąg|Nie||  
|properties|map|Nie|Mapy do usługi Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Mapa **sql-rule-action** musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|wyrażenie|ciąg|Tak|Wyrażenie akcji SQL.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
### <a name="remove-rule"></a>Usuń regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|nazwa reguły|ciąg|Tak|Nazwa reguły, z wyłączeniem nazw subskrypcji i tematów.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
### <a name="get-rules"></a>Pobierz reguły

#### <a name="request"></a>Żądanie

Komunikat żądania musi zawierać następujące właściwości aplikacji:

|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  

Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|top|int|Tak|Liczba reguł do pobrania na stronie.|  
|Pomiń|int|Tak|Liczba reguł do pominięcia. Definiuje indeks początkowy (+1) na liście reguł. | 

#### <a name="response"></a>Odpowiedź

Komunikat odpowiedzi zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|rules| tablica mapy|Tak|Tablica reguł. Każda reguła jest reprezentowana przez mapę.|

Każdy wpis mapy w tablicy zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|opis reguły|tablica opisanych obiektów|Tak|`com.microsoft:rule-description:list`z kodem opisanym przez AMQP 0x0000013700000004| 

`com.microsoft.rule-description:list`jest tablicą opisanych obiektów. Tablica zawiera następujące elementy:

|Indeks|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
| 0 | tablica opisanych obiektów | Tak | `filter`jak określono poniżej. |
| 1 | tablica opisanego obiektu | Tak | `ruleAction`jak określono poniżej. |
| 2 | ciąg | Tak | nazwę reguły. |

`filter`może być jednym z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtr korelacji |
| `com.microsoft:true-filter:list` | 0x0000013700000007 | Prawdziwy filtr reprezentujący 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtr false reprezentujący 1=0 |

`com.microsoft:sql-filter:list`jest opisaną tablicą, która zawiera:

|Indeks|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
| 0 | ciąg | Tak | Wyrażenie filtru SQL |

`com.microsoft:correlation-filter:list`jest opisaną tablicą, która zawiera:

|Indeks (jeśli istnieje)|Typ wartości|Zawartość wartości|  
|---------|----------------|--------------|
| 0 | ciąg | Identyfikator korelacji |
| 1 | ciąg | Identyfikator wiadomości |
| 2 | ciąg | Do |
| 3 | ciąg | Odpowiedz na |
| 4 | ciąg | Label |
| 5 | ciąg | Identyfikator sesji |
| 6 | ciąg | Odpowiadanie na identyfikator sesji|
| 7 | ciąg | Typ zawartości |
| 8 | Mapa | Mapa właściwości zdefiniowanych przez aplikację |

`ruleAction`może być jednym z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Pusta akcja reguły — brak akcji reguły |
| `com.microsoft:sql-rule-action:list` | 0x00000137000000006 | Akcja reguły SQL |

`com.microsoft:sql-rule-action:list`jest tablicą opisanych obiektów, których pierwszy wpis jest ciągiem zawierającym wyrażenie akcji reguły SQL.

## <a name="deferred-message-operations"></a>Odroczone operacje wiadomości  
  
### <a name="receive-by-sequence-number"></a>Odbieranie według numeru sekwencyjnyego  

Odbiera odroczone wiadomości według numeru sekwencyjnyego.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencowe|tablica długich|Tak|Numery sekwencji.|  
|odbiornik-tryb rozliczania|ubyt|Tak|**Tryb rozliczania odbiornika** określony w rdzeniu AMQP v1.0.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|Statusdescription|ciąg|Nie|Opis stanu.|  
  
Treść wiadomości odpowiedzi musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|lista map|Tak|Lista wiadomości, w których każda mapa reprezentuje wiadomość.|  
  
Mapa reprezentująca wiadomość musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Tak|Token blokady, jeśli `receiver-settle-mode` jest 1.|  
|message|tablica bajtów|Tak|Komunikat zakodowany przewodem AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Aktualizowanie stanu dyspozycji  

Aktualizuje stan dyspozycji odroczonych wiadomości. Ta operacja obsługuje transakcje.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Operacji|ciąg|Tak|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji w milisekundach.|  
  
Treść wiadomości żądania musi składać się z sekcji **wartości amqp** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|status dyspozycji|ciąg|Tak|Zakończone<br /><br /> Opuszczony<br /><br /> Zawieszone|  
|tokeny blokady|tablica uuid|Tak|Tokeny blokady wiadomości, aby zaktualizować stan dyspozycji.|  
|deadletter-powód|ciąg|Nie|Można ustawić, jeśli stan dyspozycji jest ustawiony na **zawieszony**.|  
|deadletter-opis|ciąg|Nie|Można ustawić, jeśli stan dyspozycji jest ustawiony na **zawieszony**.|  
|właściwości do zmodyfikowania|map|Nie|Lista właściwości wiadomości obsługiwanych przez usługę Service Bus do zmodyfikowania.|  
  
#### <a name="response"></a>Odpowiedź  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagany|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK – sukces, inaczej nie powiodło się|  
|Statusdescription|ciąg|Nie|Opis stanu.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o amqp i usługi Service Bus, odwiedź następujące łącza:

* [Omówienie usługi AMQP usługi Service Bus]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [Usługa AMQP w magistrali usług dla systemu Windows Server]

[Omówienie usługi AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[Usługa AMQP w magistrali usług dla systemu Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
