---
title: Omówienie obsługi komunikatów w usłudze Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera omówienie wysokiego poziomu usługi Azure Service Bus, w pełni zarządzanego brokera komunikatów integracji przedsiębiorstwa.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240632"
---
# <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?

Usługa Microsoft Azure Service Bus jest w pełni zarządzanym brokerem komunikatów do integracji przedsiębiorstw. Usługa Service Bus może oddzielać aplikacje i usługi. Usługa Service Bus oferuje niezawodną i bezpieczną platformę do asynchronicznie transferu danych i stanu.

Dane są przesyłane między różnymi aplikacjami i usługami przy użyciu *komunikatów*. Wiadomość jest w formacie binarnym i może zawierać JSON, XML lub po prostu tekst. Aby uzyskać więcej informacji, zobacz [Integration Services](https://azure.com/integration).

Niektóre typowe scenariusze obsługi komunikatów:

* *Wiadomości .* Przenoszenie danych biznesowych, takich jak zamówienia sprzedaży lub zakupu, arkusze lub przemieszczenia zapasów.
* *Odłączyć aplikacje*. Zwiększ niezawodność i skalowalność aplikacji i usług. Klient i usługa nie muszą być w trybie online w tym samym czasie.
* *Tematy i subskrypcje*. Włącz 1:*n* relacje między wydawcami i subskrybentami.
* *Sesje wiadomości*. Zaimplementuj przepływy pracy, które wymagają zamawiania wiadomości lub odroczenia wiadomości.

## <a name="namespaces"></a>Namespaces

Obszar nazw jest kontenerem dla wszystkich składników obsługi wiadomości. Wiele kolejek i tematów może znajdować się w jednej przestrzeni nazw, a przestrzenie nazw często służą jako kontenery aplikacji.

## <a name="queues"></a>Kolejki

Komunikaty są wysyłane do *kolejek* i odbierane z nich. Kolejki przechowują wiadomości, dopóki aplikacja odbierająca nie będzie dostępna do ich odbierania i przetwarzania.

![Kolejka](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Wiadomości w kolejkach są uporządkowane i sygnatury czasowe po przyjeździe. Po zaakceptowaniu komunikat jest bezpiecznie przechowywany w magazynie nadmiarowym. Wiadomości są dostarczane w trybie *ściągania,* tylko dostarczanie wiadomości na żądanie.

## <a name="topics"></a>Tematy

Do wysyłania i odbierania komunikatów można również używać *tematów*. Kolejka jest często używana do komunikacji typu punkt-punkt, natomiast tematy są przydatne w scenariuszach publikowania/subskrypcji.

![Temat](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Tematy mogą mieć wiele niezależnych subskrypcji. Subskrybent tematu może otrzymywać kopie wszystkich komunikatów wysłanych do tego tematu. Subskrypcje są nazwane jednostki. Subskrypcje utrzymują się, ale może wygasnąć lub autodelete.

Możesz nie chcieć, aby poszczególne subskrypcje odbierać wszystkie wiadomości wysyłane do tematu. Jeśli tak, można użyć *reguł* i *filtrów* do definiowania warunków, które wyzwalają *akcje*opcjonalne . Można filtrować określone komunikaty i ustawiać lub modyfikować właściwości wiadomości. Aby uzyskać więcej informacji, zobacz [Filtry tematów i akcje](topic-filters.md).

## <a name="advanced-features"></a>Funkcje zaawansowane

Usługa Service Bus zawiera zaawansowane funkcje, które umożliwiają rozwiązywanie bardziej złożonych problemów z wiadomościami. W poniższych sekcjach opisano kilka z tych funkcji.

### <a name="message-sessions"></a>Sesje komunikatów

Aby utworzyć gwarancję pierwszego w, pierwszego wyjścia (FIFO) w usłudze Service Bus, należy użyć sesji. Sesje komunikatów umożliwiają wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów. Aby uzyskać więcej informacji, zobacz [Sesje wiadomości: pierwszy w, pierwszy na zewnątrz (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Automatyczne doowanie

Funkcja automatycznego dokładania łańcuchów kolejki lub subskrypcji do innej kolejki lub tematu. Muszą one być częścią tej samej przestrzeni nazw. W przypadku automatycznego włączania usługa Service Bus automatycznie usuwa wiadomości z kolejki lub subskrypcji i umieszcza je w innej kolejce lub temacie. Aby uzyskać więcej informacji, zobacz [Łączenie jednostek usługi Service Bus z automatycznym dopasowywaniem](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kolejka utraconych wiadomości

Usługa Service Bus obsługuje kolejkę utraconych wiadomości (DLQ). DlQ zawiera komunikaty, które nie mogą być dostarczone do dowolnego odbiornika. Przechowuje wiadomości, które nie mogą być przetwarzane. Usługa Service Bus umożliwia usuwanie wiadomości z bibliotekidlach i ich sprawdzanie. Aby uzyskać więcej informacji, zobacz [Omówienie kolejek utraconych wiadomości usługi Service Bus](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Zaplanowane dostarczanie

Możesz przesyłać wiadomości do kolejki lub tematu w celu opóźnionego przetwarzania. Można zaplanować zadanie, aby stać się dostępne do przetwarzania przez system w określonym czasie. Aby uzyskać więcej informacji, zobacz [Zaplanowane wiadomości](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Odraczanie komunikatów

Kolejka lub klient subskrypcji można odroczyć pobieranie wiadomości do późniejszego czasu. To odroczenie może być spowodowane szczególnymi okolicznościami we wniosku. Wiadomość pozostaje w kolejce lub subskrypcji, ale jest odłożona. Aby uzyskać więcej informacji, zobacz [Odroczenie wiadomości](message-deferral.md).

### <a name="batching"></a>Tworzenie partii

Dzielenie na partie po stronie klienta umożliwia klientowi kolejki lub tematu opóźnienie wysłania komunikatu na pewien czas. Jeśli klient wysyła dodatkowe komunikaty w tym okresie, przesyła komunikaty w jednej partii. Aby uzyskać więcej informacji, zobacz [wsadowanie po stronie klienta](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transakcje

Transakcja grupuje dwie lub więcej operacji razem w *zakresie wykonania*. Usługa Service Bus obsługuje operacje grupowania względem pojedynczej jednostki obsługi wiadomości w zakresie pojedynczej transakcji. Encja wiadomości może być kolejką, tematem lub subskrypcją. Aby uzyskać więcej informacji, zobacz [Omówienie przetwarzania transakcji usługi Service Bus](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtrowanie i akcje

Subskrybenci mogą zdefiniować, które komunikaty chcą odbierać z tematu. Komunikaty te są określone w formie co najmniej jednej nazwanej reguły subskrypcji. Dla każdego pasującego warunku reguły subskrypcja tworzy kopię wiadomości, która może być inaczej opisywana dla każdej reguły dopasowania. Aby uzyskać więcej informacji, zobacz [Filtry tematów i akcje](topic-filters.md).

### <a name="autodelete-on-idle"></a>Autodelete na stanie bezczynnym

Autodelete na bezczynności umożliwia określenie interwału bezczynności, po którym kolejka jest automatycznie usuwana. Minimalny czas trwania wynosi 5 minut. Aby uzyskać więcej informacji, zobacz [właściwość QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Wykrywanie duplikatów

Błąd może spowodować, że klient ma wątpliwości co do wyniku operacji wysyłania. Wykrywanie duplikatów umożliwia nadawcy ponowne wysłanie tej samej wiadomości. Inną opcją jest odrzucenie przez kolejkę lub temat wszelkich zduplikowanych kopii. Aby uzyskać więcej informacji, zobacz [Wykrywanie duplikatów](duplicate-detection.md).

### <a name="security-protocols"></a>Protokoły zabezpieczeń
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Usługa Service Bus obsługuje protokoły zabezpieczeń, np. [sygnatury dostępu współdzielonego](service-bus-sas.md) (SAS), [kontrolę dostępu na podstawie ról](authenticate-application.md) (RBAC) i [tożsamości zarządzane dla zasobów platformy Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geograficzne odzyskiwanie po awarii

Jeśli w regionach lub centrach danych Azure dojdzie do przestoju, geograficzne odzyskiwanie po awarii umożliwia kontynuowanie przetwarzania danych w innym regionie lub centrum danych. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii geograficznej usługi Azure Service Bus](service-bus-geo-dr.md).

### <a name="security"></a>Zabezpieczenia

Usługa Service Bus obsługuje standardowe protokoły [AMQP 1.0](service-bus-amqp-overview.md) i [HTTP/REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Biblioteki klienta

Usługa Service Bus obsługuje biblioteki klienckie dla [.NET,](https://github.com/Azure/azure-service-bus-dotnet/tree/master) [Java](https://github.com/Azure/azure-service-bus-java/tree/master)i [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integracja

Usługa Service Bus w pełni integruje się z następującymi usługami platformy Azure:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z obsługi komunikatów usługi Service Bus, zobacz następujące artykuły:

* Aby porównać usługi obsługi wiadomości platformy Azure, zobacz [Porównanie usług](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Wypróbuj przewodniki Szybki start dla [platformy .NET,](service-bus-dotnet-get-started-with-queues.md) [Java](service-bus-java-how-to-use-queues.md)lub [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Aby zarządzać zasobami usługi Service Bus, zobacz [Eksplorator magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Aby dowiedzieć się więcej o warstwach Standard i Premium oraz ich cenach, zobacz [Ceny usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Aby dowiedzieć się więcej o wydajności i opóźnieniach w warstwie Premium, zobacz [Wiadomości w warstwie Premium.](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)
