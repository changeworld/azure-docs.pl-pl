---
title: Utwórz Kafka włączone usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Utwórz Kafka włączona przestrzeń nazw usługi Azure Event Hubs przy użyciu portalu Azure
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 4f1d21be3c19dfbc764485fea47b6d4cb2171b3c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942550"
---
# <a name="create-kafka-enabled-event-hubs"></a>Utwórz Kafka włączone usługi Event Hubs

Usługa Azure Event Hubs jest danych Big Data przesyłania strumieniowego platforma jako usługa (PaaS) wysyła strumień miliony zdarzeń na sekundę, która zapewnia małymi opóźnieniami i wysoką wydajnością analiz w czasie rzeczywistym i wizualizacji.

Usługa Azure Event Hubs dla ekosystemami Kafka zapewnia punkt końcowy. Ten punkt końcowy umożliwia przestrzeni nazw usługi Event Hubs natywnie zrozumienie [Apache Kafka](https://kafka.apache.org/intro) komunikatów protokołu i interfejsów API. Dzięki tej możliwości użytkownik może komunikować się z centrów zdarzeń, jak Kafka tematy bez konieczności zmiany klientów protokołu lub systemem własnych klastrów. Centra zdarzeń dla obsługuje ekosystemami Kafka [Apache Kafka w wersji 1.0](https://kafka.apache.org/10/documentation.html) i nowszych.

W tym artykule opisano sposób tworzenia przestrzeni nazw usługi Event Hubs i uzyskiwanie połączenia wymagane do połączenia aplikacji Kafka Kafka ciąg włączone usługi Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Utwórz Kafka włączona przestrzeń nazw usługi Event Hubs

1. Zaloguj się do [portalu Azure][Azure portal]i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wyszukaj centra zdarzeń, a następnie wybierz opcje pokazano poniżej:
    
    ![Wyszukaj centra zdarzeń w portalu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. **Tworzenie przestrzeni nazw**, przez podanie unikatowe nazwy i Włącz Kafka w przestrzeni nazw. Kliknij przycisk **Utwórz**.
    
    ![Tworzenie przestrzeni nazw](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Po utworzeniu przestrzeni nazw, na na **ustawienia** , kliknij pozycję **zasady dostępu współużytkowanego** można pobrać ciągu połączenia.

    ![Kliknij pozycję zasady dostępu współużytkowanego](./media/event-hubs-create/create-event-hub7.png)

5. Można wybrać domyślny **RootManageSharedAccessKey**, lub Dodaj nowe zasady. Kliknij nazwę zasady i skopiuj parametry połączenia. 
    
    ![Wybierz zasadę](./media/event-hubs-create/create-event-hub8.png)
 
6. Dodaj te parametry połączenia do Kafka konfiguracji aplikacji.

Można teraz strumienia zdarzeń od aplikacji korzystających z protokołu Kafka w usłudze Event Hubs.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, odwiedź te linki:

* [Strumień do usługi Event Hubs aplikacje Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Więcej informacji na temat usługi Event Hubs dla ekosystemu Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Więcej informacji na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
