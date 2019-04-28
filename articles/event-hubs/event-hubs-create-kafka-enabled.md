---
title: Tworzenie Centrum zdarzeń włączone platformy Apache Kafka — Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia klastra Apache Kafka włączony przestrzeni nazw usługi Azure Event Hubs przy użyciu witryny Azure portal.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 125da95349fce0e75b44b5619baba28d34a74be1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822482"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Tworzenie usługi event hubs platformy Apache Kafka włączone

Azure Event Hubs to platforma do strumieniowego przesyłania jako usługa (PaaS), która pozyskuje miliony zdarzeń na sekundę i zapewnia małe opóźnienia i wysoką przepływność do analizy w czasie rzeczywistym i wizualizacji danych Big Data.

Usługa Azure Event Hubs zapewnia punkt końcowy platformy Kafka. Ten punkt końcowy umożliwia przestrzeni nazw usługi Event Hubs zrozumieć, natywnie [platformy Apache Kafka](https://kafka.apache.org/intro) komunikatu protokołu i interfejsów API. Dzięki tej możliwości możesz mogą komunikować się za pomocą usługi event hubs, co w przypadku tematów platformy Kafka bez zmieniania klientów protokołu lub działające własne klastry. Usługa Event Hubs obsługuje [platformy Apache Kafka w wersji 1.0](https://kafka.apache.org/10/documentation.html) i nowszych.

W tym artykule opisano sposób tworzenia przestrzeni nazw usługi Event Hubs i pobieranie parametrów połączenia wymagane do łączenia aplikacji platformy Kafka z centrów zdarzeń z obsługą platformy Kafka.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka

1. Zaloguj się do [witryny Azure portal][Azure portal]i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wyszukaj usługę Event Hubs i wybierz pokazane tutaj opcje:
    
    ![Wyszukiwanie usługi Event Hubs w portalu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Podaj unikatową nazwę i włącz platformę Kafka w przestrzeni nazw. Kliknij pozycję **Utwórz**.
    
    ![Tworzenie przestrzeni nazw](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Po utworzeniu przestrzeni nazw na karcie **Ustawienia** kliknij pozycję **Zasady dostępu współdzielonego** w celu pobrania parametrów połączenia.

    ![Klikanie pozycji Zasady dostępu współużytkowanego](./media/event-hubs-create/create-event-hub7.png)

5. Można wybrać domyślną wartość **RootManageSharedAccessKey** lub dodać nowe zasady. Kliknij nazwę zasad, a następnie skopiuj parametry połączenia. 
    
    ![Wybieranie zasad](./media/event-hubs-create/create-event-hub8.png)
 
6. Dodaj te parametry połączenia do konfiguracji aplikacji platformy Kafka.

Teraz możesz przesyłać strumieniowo zdarzenia z aplikacji, które używają protokołu platformy Kafka, do usługi Event Hubs.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, skorzystaj z następujących linków:

* [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
