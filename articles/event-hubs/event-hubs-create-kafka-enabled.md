---
title: Tworzenie centrum zdarzeń z włączonym Apache Kafka — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące tworzenia Apache Kafka włączonej przestrzeni nazw platformy Azure Event Hubs przy użyciu Azure Portal.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555800"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Utwórz Centra zdarzeń z włączonym Apache Kafka

Azure Event Hubs to platforma przesyłania strumieniowego danych Big Data jako usługa (PaaS), która umożliwia pozyskiwanie milionów zdarzeń na sekundę oraz zapewnia małe opóźnienia i wysoką przepływność dla analiz i wizualizacji w czasie rzeczywistym.

Usługa Azure Event Hubs udostępnia punkt końcowy Kafka. Ten punkt końcowy umożliwia Event Hubsom przestrzeni nazw natywne zrozumienie protokołu komunikatów [Apache Kafka](https://kafka.apache.org/intro) i interfejsów API. Korzystając z tej możliwości, można komunikować się z centrami zdarzeń w taki sam sposób, jak w przypadku Kafka tematów bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Event Hubs obsługuje [Apache Kafka wersje 1,0](https://kafka.apache.org/10/documentation.html) i nowsze.

W tym artykule opisano sposób tworzenia przestrzeni nazw Event Hubs i uzyskiwania parametrów połączenia wymaganych do połączenia aplikacji Kafka z centrami zdarzeń z obsługą Kafka.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka

1. Zaloguj się w witrynie [Azure Portal][Azure portal], a następnie kliknij pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wyszukaj usługę Event Hubs i wybierz pokazane tutaj opcje:
    
    ![Wyszukiwanie usługi Event Hubs w portalu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Podaj unikatową nazwę i włącz platformę Kafka w przestrzeni nazw. Kliknij przycisk **Utwórz**.
    
    ![Tworzenie przestrzeni nazw](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Po utworzeniu przestrzeni nazw na karcie **Ustawienia** kliknij pozycję **Zasady dostępu współdzielonego** w celu pobrania parametrów połączenia.

    ![Klikanie pozycji Zasady dostępu współużytkowanego](./media/event-hubs-create/create-event-hub7.png)

5. Można wybrać domyślną wartość **RootManageSharedAccessKey** lub dodać nowe zasady. Kliknij nazwę zasad, a następnie skopiuj parametry połączenia. 
    
    ![Wybieranie zasad](./media/event-hubs-create/create-event-hub8.png)
 
6. Dodaj te parametry połączenia do konfiguracji aplikacji platformy Kafka.

Teraz możesz przesyłać strumieniowo zdarzenia z aplikacji, które używają protokołu platformy Kafka, do usługi Event Hubs.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Event Hubs, odwiedź następujące linki:

* [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
