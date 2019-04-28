---
title: Uzyskiwanie dostępu do zmiany źródła danych w usłudze Azure Cosmos DB usługi Azure Cosmos DB
description: W tym artykule opisano różne opcje dostępne do odczytu i dostęp do zmiany źródła danych w usłudze Azure Cosmos DB usługi Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 459ed4b6f16d3cfe5bb792be7f063a1253a3006e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60927804"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Kanał informacyjny zmian czytania usługi Azure Cosmos DB

Możesz pracować z zestawienia zmian usługi Azure Cosmos DB przy użyciu dowolnej z następujących opcji:

* Użycie usługi Azure Functions
* Za pomocą zmiany źródła danych z biblioteką procesora
* Przy użyciu zestawu SDK do interfejsu API SQL usługi Azure Cosmos DB

## <a name="using-azure-functions"></a>Użycie usługi Azure Functions

Usługa Azure Functions to najprostszy i zalecana opcja. Po utworzeniu wyzwalacza usługi Azure Cosmos DB w aplikacji usługi Azure Functions, można wybrać kontener, aby połączyć, a funkcja platformy Azure pobiera wyzwalane, gdy brak zmian do kontenera. Wyzwalacze mogą być tworzone przy użyciu portalu Azure Functions w portalu usługi Azure Cosmos DB lub programowo przy użyciu zestawów SDK. Program Visual Studio i programu VS Code obsługuje do zapisania usługi Azure Functions, i interfejsu wiersza polecenia usługi Azure Functions nawet służy do tworzenia aplikacji dla wielu platform. Można napisać i debugować kod na pulpicie i następnie wdrażanie funkcji za pomocą jednego kliknięcia. Zobacz [obliczeniowych bez użycia serwera bazy danych, przy użyciu usługi Azure Functions](serverless-computing-database.md) i [przy użyciu zmian źródła danych za pomocą usługi Azure Functions](change-feed-functions.md)) artykuły, aby dowiedzieć się więcej.

## <a name="using-the-change-feed-processor-library"></a>Za pomocą zmiany źródła danych z biblioteką procesora

Zmiana źródła danych z biblioteką procesora ukrywa złożoność i wciąż daje pełną kontrolę nad zestawienia zmian. Biblioteka jest zgodny ze wzorcem obserwatora, gdzie funkcji przetwarzania jest wywoływana przez bibliotekę. Jeśli masz zestawienia zmian wysokiej przepływności, można utworzyć wystąpienie wielu klientów na odczytywanie zestawienia zmian. Ponieważ używasz bibliotece procesora zestawienia zmian go automatycznie dzieli obciążenie między różnych klientów bez konieczności implementowania tę logikę. Wszystkie złożoności odbywa się przez bibliotekę. Jeśli chcesz mieć własną usługę równoważenia obciążenia, a następnie można wdrożyć `IPartitionLoadBalancingStrategy` dla niestandardowych partycji strategię, aby przetworzyć zmiany źródła danych. Aby dowiedzieć się więcej, zobacz [przy użyciu zmian źródła danych z biblioteką procesora](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Przy użyciu zestawu SDK do interfejsu API SQL usługi Azure Cosmos DB

Umożliwia skorzystanie z zestawu SDK, formantu zestawienia zmian niskiego poziomu. Można zarządzać punktu kontrolnego, dostęp do określonej partycji logicznej itp klucza. Jeśli masz wielu elementów odczytujących, możesz użyć `ChangeFeedOptions` rozłożenie obciążenia odczytu w różnych wątkach lub różnych klientów. 

## <a name="next-steps"></a>Kolejne kroki

Można teraz kontynuować, aby dowiedzieć się więcej na temat zmiany źródła danych w następujących artykułach:

* [Omówienie Kanał informacyjny zmian](change-feed.md)
* [Za pomocą zmian źródła danych za pomocą usługi Azure Functions](change-feed-functions.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
