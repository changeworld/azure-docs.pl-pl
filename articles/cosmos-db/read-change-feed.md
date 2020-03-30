---
title: Uzyskiwanie dostępu do kanału informacyjnego usługi Azure Cosmos DB usługi Azure Cosmos DB
description: W tym artykule opisano różne opcje dostępne do odczytu i dostępu do źródła danych zmian w usłudze Azure Cosmos DB usługi Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688127"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Odczytywanie zestawienia zmian w usłudze Azure Cosmos DB

Można pracować z kanału informacyjnego usługi Azure Cosmos DB zmian przy użyciu dowolnej z następujących opcji:

* Korzystanie z usługi Azure Functions
* Korzystanie z biblioteki procesora zestawienia zmian
* Korzystanie z zestawu SDK interfejsu API SQL usługi Azure Cosmos DB

## <a name="using-azure-functions"></a>Korzystanie z usługi Azure Functions

Usługa Azure Functions jest najprostszą i zalecaną opcją. Podczas tworzenia wyzwalacza usług Azure Functions dla usługi Cosmos DB, można wybrać kontener do połączenia, a funkcja platformy Azure zostanie wyzwolona za każdym razem, gdy nastąpi zmiana w kontenerze. Wyzwalacze można tworzyć przy użyciu portalu usługi Azure Functions, portalu usługi Azure Cosmos DB lub programowo przy użyciu zestawów SDK. Visual Studio i VS Code zapewniają obsługę pisania usług Azure Functions, a nawet można użyć interfejsu wiersza polecenia usługi Azure Functions do tworzenia wielu platform. Można napisać i debugować kod na pulpicie, a następnie wdrożyć funkcję za pomocą jednego kliknięcia. Zobacz [bezserwerowe przetwarzanie bazy danych przy użyciu funkcji azure i](serverless-computing-database.md) przy użyciu źródła danych zmian z [usługą Azure Functions](change-feed-functions.md)), aby dowiedzieć się więcej.

## <a name="using-the-change-feed-processor-library"></a>Korzystanie z biblioteki procesora zestawienia zmian

Biblioteka procesora pliku danych zmian ukrywa złożoność i nadal zapewnia pełną kontrolę nad kanałem informacyjnym zmian. Biblioteka jest zgodna ze wzorcem obserwatora, gdzie funkcja przetwarzania jest wywoływana przez bibliotekę. Jeśli masz źródło danych o wysokiej przepływności, możesz utworzyć wystąpienie wielu klientów, aby odczytać plik danych o zmianach. Ponieważ używasz biblioteki procesora kanału informacyjnego zmian, automatycznie podzieli obciążenie między różnych klientów bez konieczności implementowania tej logiki. Cała złożoność jest obsługiwana przez bibliotekę. Jeśli chcesz mieć własny moduł równoważenia `IPartitionLoadBalancingStrategy` obciążenia, możesz zaimplementować dla niestandardowej strategii partycji do przetwarzania pliku danych o zmianach. Aby dowiedzieć się więcej, zobacz [korzystanie z biblioteki procesorów kanału informacyjnego](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Korzystanie z zestawu SDK interfejsu API SQL usługi Azure Cosmos DB

Dzięki pakietowi SDK uzyskasz niską kontrolę nad kanałem informacyjnym zmian. Można zarządzać punktem kontrolnym, uzyskać dostęp do określonego logicznego klucza partycji itp. Jeśli masz wielu czytelników, `ChangeFeedOptions` można użyć do dystrybucji obciążenia odczytu do różnych wątków lub różnych klientów.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Zmiana kanału informacyjnego w interfejsach API dla Cassandra i MongoDB

Funkcja kanału informacyjnego zmian jest pojawiana jako strumień zmian w interfejsie API i zapytaniu MongoDB z predykatem w api Cassandra. Aby dowiedzieć się więcej o szczegółach implementacji interfejsu API usługi MongoDB, zobacz [Zmiany strumieni w interfejsie API bazy danych usługi Azure Cosmos DB dla usługi MongoDB.](mongodb-change-streams.md)

Natywny Apache Cassandra zapewnia przechwytywanie danych zmian (CDC), mechanizm do oznaczania określonych tabel do archiwizacji, a także odrzuca zapisy do tych tabel po osiągnięciu konfigurowalny rozmiar na dysku dla dziennika CDC. Funkcja kanału informacyjnego zmian w interfejsie API bazy danych usługi Azure Cosmos dla bazy danych dla firmy Cassandra zwiększa możliwość wykonywania zapytań o zmiany za pomocą predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej o szczegółach implementacji, zobacz [Zmienianie źródła danych w interfejsie API usługi Azure Cosmos DB dla firmy Cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o pliku danych o zmianach w następujących artykułach:

* [Omówienie kanału informacyjnego zmian](change-feed.md)
* [Korzystanie z kanału informacyjnego zmian za pomocą funkcji azure](change-feed-functions.md)
* [Korzystanie z biblioteki procesora kanału informacyjnego zmian](change-feed-processor.md)
