---
title: Azure odroczenia komunikatów usługi Service Bus | Dokumentacja firmy Microsoft
description: Odroczenie dostarczenia komunikatów usługi Service Bus
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: bece2be88a020610dfd3d22f15f7d276d99bb153
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196979"
---
# <a name="message-deferral"></a>Komunikat odroczenia

Gdy kolejka lub subskrypcji klient otrzymuje komunikat informujący o tym, że jest było jego przetworzenie, ale dla których przetwarzanie nie jest obecnie możliwe z powodu specjalne okoliczności wewnątrz aplikacji, ma możliwość pobierania wiadomości późniejszym "odroczenia". Komunikat pozostanie w kolejce lub subskrypcji, ale jest zarezerwowane.

Opóźnienia jest funkcją utworzone specjalnie dla scenariuszy przetwarzanie przepływu pracy. Struktury przepływu pracy może wymagać pewnych operacji, które mają być przetwarzane w określonej kolejności i może być konieczne odłożyć przetwarzania niektórych odebranej wiadomości do momentu zakończenia wyznaczonych wcześniejsze pracy, który zostanie poinformowany przez inne komunikaty.

Prosty przykład opisowy dla tego jest kolejność przetwarzania sekwencji, w którym płatności od dostawcy płatności zewnętrznych pojawi się powiadomienie w systemie zanim pasującego zamówienia zakupu zostały przekazane z przodu magazynu do systemu realizacji. W takim przypadku system realizacji może odroczyć przetwarzania powiadomienia o płatności, momentu kolejność, z którą ją skojarzyć. W scenariuszach spotkania, w którym wiadomości z różnych źródeł znajduje się dysk przepływu pracy do przodu, kolejność wykonywania w czasie rzeczywistym w rzeczywistości może być poprawne, ale wiadomości w czasie wykonywania odbicia wyniki może pojawić się poza kolejnością.

Ostatecznie odroczenia pomaga zmiana kolejności wiadomości z kolejności przyjęcia w kolejności, w którym mogą być przetworzone, pozostawiając bezpiecznie tych wiadomości w magazynie komunikatów na potrzeby przetwarzania, które opóźnienia.

## <a name="message-deferral-apis"></a>Komunikat odroczenia interfejsów API

Interfejs API jest [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) lub [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) w kliencie programu .NET Framework [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) w kliencie programu .NET Standard i **mesageReceiver.defer** lub **messageReceiver.deferSync** w kliencie programu Java. 

Odroczone wiadomości pozostaną w kolejki głównej oraz wszystkie inne komunikaty active (w przeciwieństwie do wiadomości utraconych przebywających w kolejce podrzędne), ale ich nie otrzyma przy użyciu funkcji odbioru/metody ReceiveAsync regularne. Odnalezione wiadomości odroczonego [przeglądania wiadomości](message-browsing.md) Jeśli aplikacja traci informacje o nich.

Pobierania z opóźnieniem wiadomości, jego właścicielem jest odpowiedzialny za zapamiętywanie [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) zgodnie z jego różni się go. Żadnych odbiornika, który zna numer sekwencyjny komunikatu odroczonego później może odbierać wiadomości jawnie z `Receive(sequenceNumber)`.

Jeśli nie można przetworzyć komunikatu, ponieważ określonego zasobu obsługi wiadomości jest tymczasowo niedostępny, ale przetwarzanie komunikatu nie summarily zawiesza się, sposób umieszczenia wiadomości na stronie kilka minut jest do zapamiętania  **SequenceNumber** w [zaplanowane komunikat](message-sequencing.md) można opublikować za kilka minut, a następnie ponownie pobrać odroczonego komunikat po odebraniu wiadomości zaplanowane. Należy pamiętać, że jeśli program obsługi komunikatów zależy od bazy danych dla wszystkich operacji i że baza danych jest tymczasowo niedostępny, należy nie używać opóźnienia, ale raczej zawiesić odbieranie komunikatów całkowicie aż baza danych jest ponownie dostępny.

Odkładanie wiadomości nie wpływa na czas wygaśnięcia wiadomości, co oznacza, że komunikaty odroczonego nadal wygaśnie początkowo zaplanowanej godzinie i są przenoszone do kolejki utraconych wiadomości po odpowiednim skonfigurowaniu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)