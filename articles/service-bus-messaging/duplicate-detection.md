---
title: Wykrywanie zduplikowanych wiadomości w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Możliwe wykrycie zduplikowanych komunikatów usługi Service Bus
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
ms.author: spelluru
ms.openlocfilehash: 7402fcf01078ea3934d1b6794a9190947fe339c2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696631"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja napotyka błąd krytyczny natychmiast po nim jest wysyłana wiadomość i uruchomiono ponownie aplikację błędnie uważa, że dostarczanie komunikatów wcześniejsze nie wystąpił, Wyślij kolejne powoduje, że ten sam komunikat pojawi się w systemie dwa razy.

Użytkownik może również dla błędu na poziomie klienta lub w sieci występuje chwilę wcześniej, a na zostać zatwierdzony w kolejce, o potwierdzenie nie zostało pomyślnie wysłane wiadomości są zwracane do klienta. W tym scenariuszu pozostawia klienta w stanie wątpliwości wyniku operacji wysyłania.

Wykrywanie duplikatów przyjmuje wątpliwości z tych sytuacji, należy włączyć ponownie wysłać tę samą wiadomość nadawcy i kolejki lub tematu spowoduje odrzucenie wszystkich duplikatów kopii.

Włączenie wykrywania duplikatów pomaga zachować informacje o aplikacji kontrolowane *MessageId* wszystkich komunikatów wysłanych do kolejki lub tematu w określonym przedziale czasu. Jeśli każdy nowy komunikat jest wysyłany wykonywania *MessageId* który został już zalogowany w przedziale czasu, wiadomość zostanie zgłoszony jako zaakceptowane (operacji wysyłania zakończy się powodzeniem), ale nowo wysłaną wiadomością natychmiast jest ignorowany i usunąć. Nie części wiadomości, innej niż *MessageId* są traktowane jako.

Sterowanie aplikacjami identyfikatora istotne jest, ponieważ pozwala jedynie na aplikacji powiązać *MessageId* do kontekst proces biznesowy, z którego go można być przewidywalny odtworzone w razie awarii.

W procesie biznesowym, w której wiele komunikatów są wysyłane w trakcie obsługi niektórych kontekst aplikacji *MessageId* mogą być złożone identyfikator kontekstu na poziomie aplikacji, takich jak numerów zamówień zakupu, a temat wiadomości; na przykład **12345.2017/płatności**.

*MessageId* zawsze może być kilka identyfikatora GUID, ale Zakotwiczanie identyfikator, aby proces biznesowy daje powtarzalność przewidywalne, którego pożądany jest skutecznie za pomocą funkcji wykrywania duplikatów.

## <a name="enable-duplicate-detection"></a>Włącz wykrywanie duplikatów

W portalu ta funkcja jest włączona podczas tworzenia jednostki za pomocą **Włącz wykrywanie duplikatów** pole wyboru jest domyślnie wyłączona. Odpowiada ustawienie do tworzenia nowych tematów.

![][1]

Programowo, Ustaw flagę [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) właściwość pełny interfejs API programu .NET framework. Za pomocą interfejsu API usługi Azure Resource Manager, wartość została ustawiona za pomocą [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) właściwości.

Historia czasu wykrywania duplikatów. wartość domyślna to 30 sekund w przypadku kolejek i tematów o wartości maksymalnej wynoszący 7 dni. Można zmienić to ustawienie w oknie dialogowym właściwości kolejki i tematu w witrynie Azure portal.

![][2]

Programowo, można skonfigurować rozmiar okna wykrywania duplikatów, w którym są przechowywane identyfikatory komunikatów, za pomocą [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) właściwość o pełny interfejs API platformy .NET Framework . Za pomocą interfejsu API usługi Azure Resource Manager, wartość została ustawiona za pomocą [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) właściwości.

Należy zauważyć, że włączenie wykrywania duplikatów i rozmiaru okna bezpośrednio wpływ na przepływność kolejki (i tematu), ponieważ wszystkie identyfikatory zarejestrowanych komunikatów muszą się zgadzać względem identyfikator nowo przesłanych wiadomości.

Utrzymywanie okna small oznacza, że mniej identyfikatory komunikatów muszą być przechowywane i dopasowane i przepływność to wpływ na mniejsze. W przypadku jednostek wysokiej przepływności, które wymagają wykrywania duplikatów Zachowaj okna tak małej, jak to możliwe.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
