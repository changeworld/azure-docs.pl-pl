---
title: Szybki start — tworzenie bazy danych dokumentów przy użyciu usługi Azure Cosmos DB za pomocą języka Java
description: Ten przewodnik Szybki start przedstawia przykładowy kod Java, za pomocą którego można nawiązać połączenie z interfejsem API SQL usługi Azure Cosmos DB DB i zbadać go
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 489f241453436190213b99cb4e7be0688a8b6237
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240177"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Szybki start: tworzenie aplikacji Java do zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start można utworzyć konto interfejsu API SQL usługi Azure Cosmos DB i zarządzać nim z witryny Azure portal i przy użyciu aplikacji Java sklonowanej z usługi GitHub. Najpierw należy utworzyć konto interfejsu API usługi Azure Cosmos DB SQL przy użyciu portalu Azure portal, a następnie utworzyć aplikację Java przy użyciu narzędzia SQL Java SDK, a następnie dodać zasoby do konta usługi Cosmos DB przy użyciu aplikacji Java. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora bazy danych usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Zestaw java development (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Skieruj `JAVA_HOME` zmienną środowiskową do folderu, w którym jest zainstalowany JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). Na Ubuntu `apt-get install maven` uruchom, aby zainstalować Maven.
- [Git](https://www.git-scm.com/downloads). Na Ubuntu `sudo apt-get install git` uruchom, aby zainstalować Git.

## <a name="introductory-notes"></a>Uwagi wprowadzające

*Struktura konta usługi Cosmos DB.* Niezależnie od interfejsu API lub języka programowania *konto* usługi Cosmos DB zawiera zero lub więcej *baz danych,* *baza danych* (DB) zawiera zero lub więcej *kontenerów,* a *kontener* zawiera zero lub więcej elementów, jak pokazano na poniższym diagramie:

![Jednostki kont usługi Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

Więcej informacji na temat baz danych, kontenerów i elementów można znaleźć [tutaj.](databases-containers-items.md) Kilka ważnych właściwości są zdefiniowane na poziomie kontenera, wśród nich *aprowizowana przepływność* i *klucz partycji*. 

Aprowizowana przepływność jest mierzona w jednostkach żądań *(RUs),* które mają cenę pieniężną i stanowią istotny czynnik decydujący w koszcie operacyjnym rachunku. Aprowizowana przepływność można wybrać z dokładnością do kontenera lub ziarnistość na bazę danych, jednak specyfikacja przepływności na poziomie kontenera jest zazwyczaj preferowana. Więcej informacji na temat obsługi administracyjnej przepływności można znaleźć [tutaj.](set-throughput.md)

Jak elementy są wstawiane do kontenera usługi Cosmos DB, baza danych rośnie poziomo, dodając więcej magazynu i obliczeń do obsługi żądań. Pojemność magazynu i mocy obliczeniowej są dodawane w *jednostkach dyskretnych*nazywanych partycjami i należy wybrać jedno pole w dokumentach, aby być kluczem partycji, który mapuje każdy dokument na partycję. Sposób, w jaki partycje są zarządzane jest, że każda partycja jest przypisany mniej więcej równy wycinek z zakresu wartości klucza partycji; dlatego zaleca się, aby wybrać klucz partycji, który jest stosunkowo losowy lub równomiernie rozłożone. W przeciwnym razie niektóre partycje zobaczą znacznie więcej żądań *(partycja gorąca),* podczas gdy inne partycje widzą znacznie mniej żądań *(partycja zimna),* i należy tego unikać. Możesz dowiedzieć się więcej o partycjonowaniu [tutaj](partitioning-overview.md).

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów należy utworzyć konto interfejsu API SQL za pomocą usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujmy aplikację interfejsu API SQL z serwisu GitHub, ustawmy parametry połączenia i uruchommy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji](#run-the-app). 

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Zarządzanie zasobami bazy danych przy użyciu synchroniowego (synchronizacji) interfejsu API

* Inicjowanie klienta `CosmosClient`. Zapewnia `CosmosClient` logiczną reprezentację po stronie klienta dla usługi bazy danych usługi Azure Cosmos. Ten klient jest używany do konfigurowania i wykonywania żądań dotyczących usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase`Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer`Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Tworzenie elementu przy `createItem` użyciu metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Odczyty punktowe `readItem` są wykonywane przy użyciu metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Zapytania SQL za pośrednictwem JSON `queryItems` są wykonywane przy użyciu metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Zarządzanie zasobami bazy danych przy użyciu asynchronickiego (asynchronickiego) interfejsu API

* Wywołania asynchronicznego interfejsu API zwracają natychmiast, bez oczekiwania na odpowiedź z serwera. W związku z tym następujące fragmenty kodu pokazują odpowiednie wzorce projektowe do wykonywania wszystkich poprzednich zadań zarządzania przy użyciu asynchronii interfejsu API.

* Inicjowanie klienta `CosmosAsyncClient`. Zapewnia `CosmosAsyncClient` logiczną reprezentację po stronie klienta dla usługi bazy danych usługi Azure Cosmos. Ten klient jest używany do konfigurowania i wykonywania żądań asynchronicznych względem usługi.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase`Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer`Tworzenia.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Podobnie jak w przypadku interfejsu API synchronizacji, tworzenie elementu odbywa się przy użyciu `createItem` metody. W tym przykładzie pokazano, jak `createItem` skutecznie wystawiać wiele żądań asynchronii, subskrybując strumień reaktywny, który wystawia żądania i drukuje powiadomienia. Ponieważ ten prosty przykład jest uruchamiany do zakończenia i kończy, wystąpienia są używane w celu zapewnienia, `CountDownLatch` że program nie kończy się podczas tworzenia elementu. **Właściwa praktyka programowania asynchroniiowego nie jest blokowanie wywołań asynchnowych — w realistycznych przypadkach użycia żądania są generowane z pętli main(), która wykonuje przez czas nieokreślony, eliminując konieczność zatrzaśnięcia się na wywołania asynchowe.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Podobnie jak w przypadku interfejsu API `readItem` synchronizacji, odczyty punktowe są wykonywane przy użyciu metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Podobnie jak w przypadku interfejsu API synchronizacji, `queryItems` zapytania SQL za pośrednictwem JSON są wykonywane przy użyciu metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i uruchomić aplikację, korzystając z informacji o punkcie końcowym. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W oknie terminalu usługi git wpisz polecenie `cd`, aby przejść do folderu z przykładowym kodem.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. W oknie terminala usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety języka Java.

    ```bash
    mvn package
    ```

3. W oknie terminala git użyj następującego polecenia, aby uruchomić aplikację Java `sync` `async` (zastąp SYNCASYNCMODE lub w zależności od tego, który przykładowy kod chcesz uruchomić, zastąp YOUR_COSMOS_DB_HOSTNAME cytowaną wartością URI z portalu i zastąp YOUR_COSMOS_DB_MASTER_KEY cytowanym kluczem podstawowym z portalu)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Okno terminala wyświetli powiadomienie o utworzeniu bazy danych FamilyDB. 
    
4. Aplikacja tworzy bazę danych o nazwie`AzureSampleFamilyDB`
5. Aplikacja tworzy kontener o nazwie`FamilyContainer`
6. Aplikacja będzie wykonywać odczyty punktowe przy użyciu identyfikatorów obiektów i wartości klucza partycji (która jest lastName w naszym przykładzie). 
7. Aplikacja będzie wysyłać zapytania do elementów, aby pobrać wszystkie rodziny z nazwiskiem w ("Andersen", "Wakefield", "Johnson")

7. Aplikacja nie usuwa utworzonych zasobów. Przełącz się do portalu, aby [wyczyścić zasoby](#clean-up-resources).  z poziomu konta, aby nie spowodować naliczenia opłat.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto interfejsu API SQL usługi Azure Cosmos DB, utworzyć bazę danych i kontener dokumentów przy użyciu Eksploratora danych i uruchomić aplikację Java, aby zrobić to samo programowo. Teraz można importować dodatkowe dane do konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
