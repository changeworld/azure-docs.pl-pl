---
title: Wykrywanie zduplikowanych komunikatów usługi Azure Service Bus | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak można wykryć duplikaty w komunikatach usługi Azure Service Bus. Zduplikowana wiadomość może zostać zignorowana i upuszczona.
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
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760372"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja nie powiedzie się z powodu błędu krytycznego natychmiast po wysłaniu wiadomości, a ponowne wystąpienie aplikacji błędnie uważa, że wcześniejsze dostarczanie wiadomości nie wystąpiło, kolejne wysyłanie powoduje, że ten sam komunikat pojawia się w systemie dwa razy.

Jest również możliwe, że błąd na poziomie klienta lub sieci występuje chwilę wcześniej i dla wysłanej wiadomości do zatwierdzeniu w kolejce, z potwierdzeniem nie pomyślnie zwrócone do klienta. Ten scenariusz pozostawia klienta w wątpliwość co do wyniku operacji wysyłania.

Wykrywanie duplikatów usuwa wątpliwości z tych sytuacji, włączając nadawcę ponownie wysłać tę samą wiadomość, a kolejka lub temat odrzuca wszystkie zduplikowane kopie.

Włączenie wykrywania duplikatów pomaga śledzić *messageid* kontrolowane przez aplikację wszystkich wiadomości wysłanych do kolejki lub tematu w określonym przedziale czasu. Jeśli każda nowa wiadomość jest wysyłana z *MessageId,* który został zarejestrowany w oknie czasu, wiadomość jest zgłaszana jako zaakceptowana (operacja wysyłania zakończy się pomyślnie), ale nowo wysłana wiadomość jest natychmiast ignorowana i odrzucana. Żadne inne części wiadomości inne niż *MessageId* są brane pod uwagę.

Kontrola aplikacji identyfikatora jest niezbędna, ponieważ tylko to pozwala aplikacji powiązać *MessageId* do kontekstu procesu biznesowego, z którego można przewidzieć rekonstrukcji, gdy wystąpi błąd.

W przypadku procesu biznesowego, w którym wiele wiadomości są wysyłane w trakcie obsługi niektórych kontekstu aplikacji, *MessageId* może być złożony identyfikator kontekstu na poziomie aplikacji, takich jak numer zamówienia zakupu i temat wiadomości, na przykład **12345.2017/payment**.

*MessageId* zawsze może być jakiś identyfikator GUID, ale zakotwiczenie identyfikatora do procesu biznesowego daje przewidywalne powtarzalność, co jest pożądane do wykorzystania funkcji wykrywania duplikatów skutecznie.

> [!NOTE]
> Jeśli wykrywanie duplikatów jest włączone, a identyfikator sesji lub klucz partycji nie są ustawione, identyfikator wiadomości jest używany jako klucz partycji. Jeśli identyfikator wiadomości również nie jest ustawiony, biblioteki .NET i AMQP automatycznie generują identyfikator wiadomości dla wiadomości. Aby uzyskać więcej informacji, zobacz [Używanie kluczy partycji](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Włącz wykrywanie duplikatów

W portalu funkcja jest włączona podczas tworzenia encji z polem wyboru **Włącz wykrywanie duplikatów,** które jest domyślnie wyłączone. Ustawienie tworzenia nowych tematów jest równoważne.

![][1]

> [!IMPORTANT]
> Nie można włączyć/wyłączyć wykrywania duplikatów po utworzeniu kolejki. Można to zrobić tylko w momencie tworzenia kolejki. 

Programowo można ustawić flagę z [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) właściwości w pełnej ramach .NET API. Za pomocą interfejsu API usługi Azure Resource Manager wartość jest ustawiana za pomocą [właściwości queueProperties.requiresDuplicateDetection.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Historia czasu wykrywania duplikatów domyślnie wynosi 30 sekund dla kolejek i tematów, przy maksymalnej wartości siedmiu dni. To ustawienie można zmienić w oknie właściwości kolejki i tematu w witrynie Azure portal.

![][2]

Programowo można skonfigurować rozmiar okna wykrywania duplikatów, podczas którego są zachowywane identyfikatory wiadomości, używając właściwości [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) z pełnym interfejsem API programu .NET Framework. Za pomocą interfejsu API usługi Azure Resource Manager wartość jest ustawiana za pomocą [właściwości queueProperties.duplicateDetectionHistoryTimeWindow.](/azure/templates/microsoft.servicebus/namespaces/queues#property-values)

Włączenie wykrywania duplikatów i rozmiar okna bezpośrednio wpływa na przepływność kolejki (i tematu), ponieważ wszystkie zarejestrowane identyfikatory wiadomości muszą być dopasowane do nowo przesłanego identyfikatora wiadomości.

Utrzymywanie okna małe oznacza, że mniej identyfikatorów wiadomości muszą być zachowywane i dopasowane, a przepływność ma wpływ mniej. Dla jednostek o wysokiej przepływności, które wymagają wykrywania duplikatów, należy zachować okno tak małe, jak to możliwe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

W scenariuszach, w których kod klienta nie może ponownie przesłać wiadomości o tym samym *messageid* jak poprzednio, ważne jest, aby zaprojektować wiadomości, które mogą być bezpiecznie ponownie przetwarzane. Ten [wpis na blogu o idempotęt opisuje](https://particular.net/blog/what-does-idempotent-mean) różne techniki, jak to zrobić.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
