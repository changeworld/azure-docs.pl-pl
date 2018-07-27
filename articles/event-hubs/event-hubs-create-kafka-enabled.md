---
title: Tworzenie klastra Apache Kafka włączone usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Tworzenie platformy Kafka włączona przestrzeń nazw usługi Azure Event Hubs przy użyciu witryny Azure portal
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 79b6b879bd2332c044ce871e2c9a938c6b9c900c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285091"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Tworzenie usługi event hubs platformy Apache Kafka włączone

Azure Event Hubs to platforma do strumieniowego przesyłania jako usługa (PaaS), która pozyskuje miliony zdarzeń na sekundę i zapewnia małe opóźnienia i wysoką przepływność do analizy w czasie rzeczywistym i wizualizacji danych Big Data.

Usługa Azure Event Hubs zapewnia punkt końcowy platformy Kafka. Ten punkt końcowy umożliwia przestrzeni nazw usługi Event Hubs zrozumieć, natywnie [platformy Apache Kafka](https://kafka.apache.org/intro) komunikatu protokołu i interfejsów API. Dzięki tej możliwości możesz mogą komunikować się za pomocą usługi event hubs, co w przypadku tematów platformy Kafka bez zmieniania klientów protokołu lub działające własne klastry. Usługa Event Hubs obsługuje [platformy Apache Kafka w wersji 1.0](https://kafka.apache.org/10/documentation.html) i nowszych.

W tym artykule opisano sposób tworzenia przestrzeni nazw usługi Event Hubs i pobieranie parametrów połączenia wymagane do łączenia aplikacji platformy Kafka z centrów zdarzeń z obsługą platformy Kafka.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie platformy Kafka włączona przestrzeń nazw usługi Event Hubs

1. Zaloguj się do [witryny Azure portal][Azure portal]i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wyszukiwanie usługi Event Hubs, a następnie wybierz opcje pokazano poniżej:
    
    ![Wyszukaj hasło Event Hubs w portalu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Podaj unikatową nazwę, a następnie włącz platformy Kafka w przestrzeni nazw. Kliknij pozycję **Utwórz**.
    
    ![Tworzenie przestrzeni nazw](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Po utworzeniu przestrzeni nazw, na na **ustawienia** kliknij kartę **zasady dostępu współdzielonego** można pobrać parametry połączenia.

    ![Kliknij zasady dostępu współużytkowanego](./media/event-hubs-create/create-event-hub7.png)

5. Można wybrać domyślny **RootManageSharedAccessKey**, lub Dodaj nowe zasady. Kliknij nazwę zasad, a następnie skopiuj parametry połączenia. 
    
    ![Wybierz zasady](./media/event-hubs-create/create-event-hub8.png)
 
6. Dodaj te parametry połączenia do konfiguracji aplikacji platformy Kafka.

Można teraz przesyłać strumieniowo wydarzenia z poziomu aplikacji, które używają protokołu Kafka do usługi Event Hubs.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, skorzystaj z następujących linków:

* [Stream do usługi Event Hubs z poziomu aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Dowiedz się więcej o usłudze Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
