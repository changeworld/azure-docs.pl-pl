---
title: Tworzenie tematów i subskrypcji usługi Service Bus za pomocą portalu Azure
description: 'Szybki start: W tym przewodniku Szybki start dowiesz się, jak utworzyć temat usługi Service Bus i subskrypcje tego tematu przy użyciu witryny Azure portal.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76260824"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Szybki start: tworzenie tematu usługi Service Bus i subskrypcji tematu za pomocą portalu Azure
W tym przewodniku Szybki start można użyć witryny Azure portal, aby utworzyć temat usługi Service Bus, a następnie utworzyć subskrypcje tego tematu. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do kolejek usługi Service Bus, w których każda wiadomość jest przetwarzana przez jednego konsumenta, tematy i subskrypcje zapewniają jedną do wielu form komunikacji przy użyciu wzorca publikowania/subskrybowania. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtrowania dla tematu na podstawie subskrypcji, co pozwala filtrować lub ograniczać, które wiadomości do tematu są odbierane przez subskrypcje tematu.

Tematów i subskrypcji usługi Service Bus umożliwiają skalowanie do przetwarzania dużej liczby wiadomości w dużej liczbie użytkowników i aplikacji.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak wysyłać wiadomości do tematu i odbierać te wiadomości za pośrednictwem subskrypcji, zobacz następujący artykuł: wybierz język programowania w toc. 

> [!div class="nextstepaction"]
> [Publikowanie i subskrybowanie komunikatów](service-bus-dotnet-how-to-use-topics-subscriptions.md)
