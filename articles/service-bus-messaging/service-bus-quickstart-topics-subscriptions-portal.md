---
title: Przewodnik Szybki Start — użyj witryny Azure portal do utworzenia subskrypcji i tematów usługi Service Bus | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak utworzyć tematu usługi Service Bus i subskrypcji tego tematu przy użyciu witryny Azure portal.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: a392f8b11a7ab1ad72f4da289c54e34b022f1ea6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990308"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Szybki start: Tworzenie tematu usługi Service Bus i subskrypcji do tematu za pomocą witryny Azure portal
W tym przewodniku Szybki Start używasz witryny Azure portal tworzenie tematu usługi Service Bus, a następnie utworzenie subskrypcji tego tematu. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do kolejek usługi Service Bus, w których każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji przy użyciu wzorca publikowania/subskrybowania, jeden do wielu. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtrów dla tematu na podstawie każdej subskrypcji, co pozwala na filtrowanie, lub ograniczyć zakres komunikatów w temacie odbieranych przez określone subskrypcje tematów.

Subskrypcji i tematów usługi Service Bus umożliwiają skalowanie do przetwarzania dużej liczby komunikatów na dużą liczbę użytkowników i aplikacji.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Możesz zarządzać zasobami usługi Service Bus przy użyciu [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus pozwala użytkownikom na łączenie do przestrzeni nazw usługi Service Bus i administrować jednostek obsługi komunikatów w łatwy sposób. To narzędzie zawiera zaawansowane funkcje, takie jak funkcja Importuj/Eksportuj lub możliwość testowania tematu, kolejek, subskrypcji, usługi przekazywania, usługi notification hubs i centrów zdarzeń. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak wysyłać komunikaty do tematu i odebrania tych komunikatów za pośrednictwem subskrypcji, zapoznaj się z następującym artykułem: Wybierz język programowania w spisie treści. 

> [!div class="nextstepaction"]
> [Publikuj i Subskrybuj wiadomości](service-bus-dotnet-how-to-use-topics-subscriptions.md)
