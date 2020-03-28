---
title: Samouczek — powiązanie usługi Azure Cosmos DB z aplikacją usługi Azure Spring Cloud
description: W tym samouczku dowiesz się, jak powiązać usługę Azure Cosmos DB z aplikacją usługi Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277564"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Powiąż bazę danych usługi Azure Cosmos DB z aplikacją usługi Azure Spring Cloud

Zamiast ręcznie konfigurować aplikacje spring boot, można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami przy użyciu usługi Azure Spring Cloud. W tym artykule pokazano, jak powiązać aplikację z bazą danych usługi Azure Cosmos DB.

Wymagania wstępne:

* Wdrożone wystąpienie usługi Azure Spring Cloud. Postępuj zgodnie z naszym [przewodnikiem Szybki start podczas wdrażania za pośrednictwem interfejsu wiersza polecenia platformy Azure,](spring-cloud-quickstart-launch-app-cli.md) aby rozpocząć pracę.
* Konto usługi Azure Cosmos DB z minimalnym poziomem uprawnień współautora.

## <a name="bind-azure-cosmos-db"></a>Powiąż usługę Azure Cosmos DB

Usługa Azure Cosmos DB ma pięć różnych typów interfejsu API, które obsługują powiązania. Poniższa procedura pokazuje, jak z nich korzystać:

1. Tworzy bazę danych usługi Azure Cosmos DB. Aby uzyskać pomoc, zapoznaj się z przewodnikiem Szybki start, aby uzyskać pomoc dotyczącą [tworzenia bazy danych.](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) 

1. Zapisz nazwę bazy danych. W przypadku tej procedury nazwa bazy danych to **testdb**.

1. Dodaj jedną z następujących zależności do pliku pom.xml aplikacji usługi Azure Spring Cloud. Wybierz zależność, która jest odpowiednia dla typu interfejsu API.

    * Typ interfejsu API: Rdzeń (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Typ INTERFEJSU API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Typ interfejsu API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Typ INTERFEJSU API: Gremlin (wykres)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Typ interfejsu API: tabela platformy Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Służy `az spring-cloud app update` do aktualizowania bieżącego `az spring-cloud app deployment create` wdrożenia lub do utworzenia nowego wdrożenia. Te polecenia zaktualizują lub utworzy aplikację z nową zależnością.

1. Przejdź do strony usługi Azure Spring Cloud w witrynie Azure portal. Przejdź do **pulpitu nawigacyjnego aplikacji** i wybierz aplikację do powiązania z usługą Azure Cosmos DB. Ta aplikacja jest taka sama, która została zaktualizowana lub wdrożona w poprzednim kroku.

1. Wybierz polecenie **Powiązanie usługi**i wybierz pozycję **Utwórz powiązanie usługi**. Aby wypełnić formularz, wybierz:
   * Wartość **typu powiązania** usługi Azure **Cosmos DB**.
   * Typ interfejsu API.
   * Nazwa bazy danych.
   * Konto usługi Azure Cosmos DB.

    > [!NOTE]
    > Jeśli używasz Cassandra, użyj miejsca na klucze dla nazwy bazy danych.

1. Uruchom ponownie aplikację, wybierając **pozycję Uruchom ponownie** na stronie aplikacji.

1. Aby upewnić się, że usługa jest powiązana poprawnie, wybierz nazwę powiązania i sprawdź jej szczegóły. Pole `property` powinno być podobne do tego przykładu:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak powiązać aplikację usługi Azure Spring Cloud z bazą danych usługi Azure Cosmos DB. Aby dowiedzieć się, jak powiązać aplikację z pamięcią podręczną usługi Azure Cache for Redis, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać pamięć podręczną usługi Azure Cache for Redis](spring-cloud-tutorial-bind-redis.md)
