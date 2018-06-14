---
title: Azure wykrywania zduplikowanych komunikatów usługi Service Bus | Dokumentacja firmy Microsoft
description: Wykrywanie zduplikowanych komunikatów usługi Service Bus
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
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: efc5608d4812edbb3f477dffbc2b495b331bd787
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28198557"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja napotka błąd krytyczny natychmiast po wysyła komunikat i uruchomić ją ponownie aplikację błędnie uznaje, że dostarczanie komunikatów wcześniejsze nie zostało przeprowadzone, wysyłanie kolejnych powoduje, że ten sam komunikat do pojawiają się dwukrotnie w systemie.

Istnieje również możliwość wystąpił błąd na poziomie klienta lub w sieci występuje wcześniej chwilę, a na zadeklarowane w kolejce o potwierdzenie nie zostało pomyślnie wysłane wiadomości zwracana do klienta. W tym scenariuszu sprawi, że klient wątpliwości dotyczących wyniku operacji wysyłania.

Wykrywanie duplikatów przyjmuje wątpliwości z tych sytuacji, należy włączyć ponownie wysłać tę samą wiadomość nadawcy, a kolejka lub temat spowoduje odrzucenie wszystkich duplikatów kopii.

Włączanie wykrywania duplikatów pomaga zachować informacje o aplikacji kontrolowane *MessageId* wszystkich wiadomości wysłanych do kolejki lub tematu w oknie określonego czasu. Jeśli wszystkie nowe wiadomości jest wykonywanie *MessageId* który już został zarejestrowany podczas przedział czasu, wiadomość zostaje zgłoszone jako zaakceptowane (operacji wysyłania zakończy się powodzeniem), ale nowo wysłanej wiadomości błyskawiczne jest ignorowany i porzucony. Żadna część wiadomości innych niż *MessageId* są traktowane jako.

Sterowanie aplikacjami identyfikatora jest istotne, ponieważ tylko który umożliwia powiązanie aplikacji *MessageId* do kontekst proces biznesowy, w którym go można jednoznacznie odtworzyć w przypadku awarii.

Dla proces biznesowy, w której wiele komunikatów są wysyłane w trakcie obsługi niektóre kontekst aplikacji *MessageId* może składać się identyfikator kontekstu na poziomie aplikacji, takich jak numer zamówienia zakupu i temat wiadomości; na przykład **12345.2017/płatności**.

*MessageId* zawsze może być niektórych identyfikatora GUID, ale Zakotwiczanie identyfikator, aby proces biznesowy daje powtarzalność przewidywalne, które jest potrzebne do korzystania z funkcji wykrywania duplikatów efektywnie.

## <a name="enable-duplicate-detection"></a>Włącz wykrywanie duplikatów

W portalu, funkcja jest włączona podczas tworzenia jednostki z **Włącz wykrywanie duplikatów** pole wyboru jest domyślnie wyłączone. Odpowiada ustawienie do tworzenia nowych tematów.

![][1]

Programowo, ustaw wartość flagi z [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) właściwości na pełne interfejs API .NET framework. Przy użyciu interfejsu API Menedżera zasobów Azure ustawiono wartość [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) właściwości.

Historia czasu wykrywania duplikatów domyślnie 30 sekund w przypadku kolejek i tematów z maksymalną wartość 7 dni. Można zmienić to ustawienie w oknie właściwości kolejki i tematu w portalu Azure.

![][2]

Programowo, można skonfigurować rozmiaru okna wykrywania duplikatów, w którym są przechowywane identyfikatory komunikatów, za pomocą [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) właściwości z pełną Framework interfejsu API programu .NET . Przy użyciu interfejsu API Menedżera zasobów Azure ustawiono wartość [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) właściwości.

Należy pamiętać, że włączenie wykrywania duplikatów i rozmiaru okna bezpośrednio wpływ przepływności kolejki (i tematu), ponieważ wszystkie zarejestrowane identyfikatory komunikatów musi być dopasowana identyfikator nowo przesłanych wiadomości.

Utrzymywanie okna small oznacza, że mniej identyfikatory komunikatów muszą być przechowywane i dopasować i Przepływność wynosi wpływ na mniejsze. Dla jednostek wysokiej przepływności, które wymagają wykrywania duplikatów należy dysponować okna możliwie jak najmniejszy.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
