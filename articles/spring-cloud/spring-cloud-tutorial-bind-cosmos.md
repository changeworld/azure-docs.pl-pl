---
title: Powiąż Azure Cosmos DB ze swoją aplikacją w chmurze Azure wiosną | Microsoft Docs
description: Dowiedz się, jak powiązać Azure Cosmos DB ze swoją aplikacją w chmurze platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607127"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Samouczek: Powiąż Azure Cosmos DB ze swoją aplikacją w chmurze platformy Azure

Chmura sprężynowa platformy Azure umożliwia automatyczne powiązanie wybranych usług platformy Azure z aplikacjami zamiast ręcznego konfigurowania aplikacji do rozruchu ze sprężyną. W tym artykule pokazano, jak powiązać aplikację z Azure Cosmos DB.

Wymagania wstępne:
* Wdrożone wystąpienie chmury Azure wiosennej.  Skorzystaj z naszego [przewodnika Szybki Start](spring-cloud-quickstart-launch-app-cli.md) , aby rozpocząć pracę.
* Konto Azure Cosmos DB z minimalnym poziomem uprawnień współautor.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB powiązania

Azure Cosmos DB ma pięć różnych typów interfejsów API, które obsługują powiązanie:

1. Tworzy bazę danych usługi Azure Cosmos DB. Zapoznaj się z [tym artykułem,](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) Aby uzyskać pomoc dotyczącą tworzenia bazy danych. Zapisz nazwę bazy danych. Nasza nazwa jest `testdb`.

1. Dodaj jedną z następujących zależności w `pom.xml` aplikacji w chmurze, zgodnie z typem interfejsu API.
    
    #### <a name="api-type-core-sql"></a>Typ interfejsu API: rdzeń (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Typ interfejsu API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Typ interfejsu API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Typ interfejsu API: Gremlin (Graph)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Typ interfejsu API: tabela platformy Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Zaktualizuj bieżące wdrożenie przy użyciu `az spring-cloud app update` lub Utwórz nowe wdrożenie dla tej zmiany przy użyciu `az spring-cloud app deployment create`.  Te polecenia spowodują aktualizację lub utworzenie aplikacji z nową zależnością.

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Ta sama aplikacja została zaktualizowana lub wdrożona w poprzednim kroku. Znajdź **pulpit nawigacyjny aplikacji** i wybierz aplikację, która ma zostać powiązana z Cosmos DB. Następnie wybierz pozycję `Service binding` i wybierz przycisk `Create service binding`. Wypełnij formularz, wybierając **Typ powiązania** `Azure Cosmos DB`, typ interfejsu API, nazwę bazy danych i konto Azure Cosmos DB.

    > [!NOTE]
    > Jeśli używasz Cassandra, użyj przestrzeni kluczy dla nazwy bazy danych.

1. Uruchom ponownie aplikację, wybierając przycisk **restart (Uruchom ponownie** ) na stronie aplikacji.

1. Aby upewnić się, że usługa jest powiązana prawidłowo, wybierz nazwę powiązania i sprawdź jej szczegóły. Pole `property` powinno wyglądać podobnie do tego:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób powiązania aplikacji w chmurze platformy Azure z usługą CosmosDB.  Aby dowiedzieć się, jak powiązać aplikację z Azure Redis Cache, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Powiąż aplikację w chmurze platformy Azure z usługą Azure Redis Cache](spring-cloud-tutorial-bind-redis.md).
