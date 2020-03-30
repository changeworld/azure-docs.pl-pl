---
title: Usługa Azure Service Bus — odroczenie komunikatu
description: W tym artykule wyjaśniono, jak odroczyć dostarczanie komunikatów usługi Azure Service Bus. Komunikat pozostanie w kolejce lub subskrypcji, ale zostanie odłożony.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538400"
---
# <a name="message-deferral"></a>Odraczanie komunikatów

Gdy klient kolejki lub subskrypcji odbiera komunikat, który jest chętny do przetworzenia, ale dla których przetwarzanie nie jest obecnie możliwe ze względu na szczególne okoliczności wewnątrz aplikacji, ma możliwość "odroczenie" pobierania wiadomości do późniejszego punktu. Komunikat pozostanie w kolejce lub subskrypcji, ale zostanie odłożony.

Odroczenie jest funkcją specjalnie utworzoną dla scenariuszy przetwarzania przepływu pracy. Struktury przepływu pracy może wymagać niektórych operacji do przetworzenia w określonej kolejności i może być konieczne odroczenie przetwarzania niektórych odebranych wiadomości, dopóki nie zostanie zakończona określona wcześniejsza praca, która jest informowana przez inne wiadomości.

Prostym przykładem jest sekwencja przetwarzania zamówień, w której powiadomienie o płatności od zewnętrznego dostawcy płatności pojawia się w systemie przed propagacją pasującego zamówienia zakupu z przodu sklepu do systemu realizacji. W takim przypadku system realizacji może odroczyć przetwarzanie powiadomienia o płatności, dopóki nie zostanie zrealizowane zlecenie, z którym można je skojarzyć. W scenariuszach spotkania, gdzie wiadomości z różnych źródeł dysk przepływu pracy do przodu, kolejność wykonywania w czasie rzeczywistym może rzeczywiście być poprawne, ale komunikaty odzwierciedlające wyniki mogą pojawić się poza kolejnością.

Ostatecznie pomoce odroczenia w ponownym uskoleniu wiadomości z kolejności przybycia do kolejności, w której mogą być przetwarzane, pozostawiając te wiadomości bezpiecznie w magazynie wiadomości, dla których przetwarzanie musi zostać odroczone.

## <a name="message-deferral-apis"></a>Interfejsy API odroczenia wiadomości

Interfejs API to [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) lub [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) w kliencie .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) w kliencie .NET Standard oraz [IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) lub [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) w kliencie Java. 

Odroczone wiadomości pozostają w kolejce głównej wraz ze wszystkimi innymi aktywnymi wiadomościami (w przeciwieństwie do wiadomości utraconych, które żyją w podo kolejce), ale nie mogą być odbierane przy użyciu regularnych funkcji Receive/ReceiveAsync. Odroczone wiadomości można odnajdować za pomocą [przeglądania wiadomości,](message-browsing.md) jeśli aplikacja traci ich ślad.

Aby pobrać odroczoną wiadomość, jej właściciel jest odpowiedzialny za zapamiętywanie [SequenceNumber,](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) ponieważ go odpiera. Każdy odbiorca, który zna numer sekwencyjny odroczonej `Receive(sequenceNumber)`wiadomości, może później otrzymać wiadomość jawnie za pomocą pliku .

Jeśli wiadomość nie może być przetworzona, ponieważ określony zasób do obsługi tej wiadomości jest tymczasowo niedostępny, ale przetwarzanie wiadomości nie powinno być liczone, sposobem na umieszczenie tej wiadomości na boku przez kilka minut jest zapamiętanie **sequenceNumber** w [zaplanowanej wiadomości,](message-sequencing.md) która ma zostać opublikowana w ciągu kilku minut, i ponowne pobranie odroczonej wiadomości po odebraniu zaplanowanej wiadomości. Jeśli program obsługi wiadomości zależy od bazy danych dla wszystkich operacji i że baza danych jest tymczasowo niedostępny, nie należy używać odroczenia, ale raczej zawiesić odbieranie wiadomości całkowicie, dopóki baza danych jest ponownie dostępna.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
