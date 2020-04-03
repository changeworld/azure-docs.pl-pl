---
title: Samouczek - end-to-end Async Java SQL API przykład aplikacji z change feed
description: W tym samouczku przeprowadzi Cię prosta aplikacja java SQL API, która wstawia dokumenty do kontenera usługi Azure Cosmos DB, zachowując zmaterializowany widok kontenera przy użyciu źródła danych.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586701"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Samouczek - end-to-end Async Java SQL API przykład aplikacji z change feed

W tym samouczku przeprowadzi Cię przez prostą aplikację java SQL API, która wstawia dokumenty do kontenera usługi Azure Cosmos DB, zachowując zmaterializowany widok kontenera przy użyciu źródła danych zmiany.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputer osobisty

* Identyfikator URI i klucz dla konta usługi Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Tło

Kanał informacyjny zmian usługi Azure Cosmos DB zawiera interfejs oparty na zdarzeniach, aby wyzwolić akcje w odpowiedzi na wstawianie dokumentu. Ma to wiele zastosowań. Na przykład w aplikacjach, które są zarówno do odczytu i zapisu ciężkie, głównym użyciem change feed jest utworzenie w czasie rzeczywistym **zmaterializowany widok** kontenera, ponieważ jest pozyskiwania dokumentów. Zmaterializowany kontener widoku będzie przechowywać te same dane, ale podzielony na partycje w celu wydajnego odczytu, dzięki czemu aplikacja jest wydajna zarówno do odczytu, jak i zapisu.

Praca nad zarządzaniem zdarzeniami kanału informacyjnego zmian jest w dużej mierze opiekuje się biblioteką procesora kanału informacyjnego zmian wbudowaną w sdk. Ta biblioteka jest wystarczająco wydajna, aby rozpowszechniać zdarzenia kanału informacyjnego zmian wśród wielu pracowników, jeśli jest to pożądane. Wszystko, co musisz zrobić, to zapewnić biblioteki zdań biblioteki wywołania zwrotnego.

W tym prostym przykładzie pokazano bibliotekę procesora kanału informacyjnego zmian z pojedynczym pracownikiem tworzącym i usuwającym dokumenty z zmaterializowanego widoku.

## <a name="setup"></a>Konfiguracja

Jeśli jeszcze tego nie zrobiono, sklonuj przykładowe repozytorium aplikacji:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Możesz pracować nad tym przewodnikiem Szybki start z java SDK 4.0 lub Java SDK 3.7.0. **Jeśli chcesz używać Java SDK 3.7.0, w ```git checkout SDK3.7.0```typie terminala **. W przeciwnym razie ```master``` pozostań w gałęzi, która domyślnie ma wartość Java SDK 4.0.

Otwórz terminal w katalogu repozytorium. Tworzenie aplikacji przez uruchomienie

```bash
mvn clean package
```

## <a name="walkthrough"></a>Przewodnik

1. Jako pierwszy czek powinieneś mieć konto usługi Azure Cosmos DB. Otwórz **witrynę Azure Portal** w przeglądarce, przejdź do konta usługi Azure Cosmos DB, a w lewym okienku przejdź do **Eksploratora danych.**

    ![Konto usługi Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Uruchom aplikację w terminalu za pomocą następującego polecenia:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Naciskaj enter, gdy zobaczysz

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    następnie wróć do Eksploratora danych usługi Azure Portal w przeglądarce. Zobaczysz bazę danych **GroceryStoreDatabase** został dodany z trzech pustych kontenerów: 

    * **InventoryContainer** — rekord zapasów dla naszego przykładowego sklepu spożywczego, podzielony na partycje na towar, ```id``` który jest identyfikatorem UUID.
    * **InventoryContainer-pktype** — zmaterializowany widok rekordu zapasów zoptymalizowany pod kątem kwerend nad towarem```type```
    * **InventoryContainer-leases** — kontener dzierżawy jest zawsze potrzebny do pliku danych o zmianach; dzierżawy śledzą postępy aplikacji w czytaniu kanału zmian.


    ![Puste pojemniki](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. W terminalu powinien zostać wyświetlony monit

    ```bash
    Press enter to start creating the materialized view...
    ```

    Naciśnij klawisz Enter. Teraz następujący blok kodu wykona i zainiszczy procesor Źródła danych zmian w innym wątku: 


    **Java SDK 4.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"```to nazwa pracownika procesora przemijadnik. ```changeFeedProcessorInstance.start()```jest to, co faktycznie uruchamia procesor zmienić plik danych.

    Wróć do Eksploratora danych usługi Azure Portal w przeglądarce. W kontenerze **InventoryContainer-leases** kliknij pozycję **towary,** aby wyświetlić jego zawartość. Zobaczysz, że procesor kanału informacyjnego zmian wypełnił kontener dzierżawy, ```SampleHost_1``` czyli procesor przypisał pracownikowi dzierżawę na niektórych partycjach **magazynu.**

    ![Dzierżawy](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Naciśnij ponownie enter w terminalu. Spowoduje to uruchomienie 10 dokumentów, które mają zostać wstawione do **magazynu.** Każde wstawienie dokumentu jest wyświetlane w kanale informacyjnym zmiany jako JSON; następujący kod wywołania zwrotnego obsługuje te zdarzenia, dublując dokumenty JSON w widoku zmaterializowanym:

    **Java SDK 4.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. Zezwalaj na uruchamianie kodu w ciągu 5-10 sek. Następnie wróć do Eksploratora danych usługi Azure Portal i przejdź do **pozycji InventoryContainer > items**. Powinieneś zobaczyć, że towary są wstawiane do kontenera magazynowego; zanotuj```id```klucz partycji ( ).

    ![Pojemnik na paszę](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Teraz w Eksploratorze danych przejdź do **pozycji InventoryContainer-pktype > items**. Jest to widok zmaterializowany — elementy w tym kontenerze **dublowane InventoryContainer,** ponieważ zostały wstawione programowo przez źródło danych zmiany. Zanotuj```type```klucz partycji ( ). Tak więc ten zmaterializowany widok jest ```type```zoptymalizowany pod kątem filtrowania zapytań, co byłoby ```id```nieefektywne w **inventorycontainer,** ponieważ jest podzielony na partycje .

    ![Zmaterializowany widok](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Usuniemy dokument zarówno z **InventoryContainer,** jak i **InventoryContainer-pktype** przy użyciu tylko jednego ```upsertItem()``` wywołania. Najpierw zapoznaj się z Eksploratorem danych usługi Azure Portal. Usuniemy dokument, ```/type == "plums"```dla którego ; jest otoczony na czerwono poniżej

    ![Zmaterializowany widok](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Naciśnij enter ponownie, ```deleteDocument()``` aby wywołać funkcję w przykładowym kodzie. Ta funkcja, pokazana poniżej, wprowadza nową wersję ```/ttl == 5```dokumentu za pomocą programu , która ustawia dokument Time-To-Live (TTL) na 5 sek. 
    
    **Java SDK 4.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    **Java SDK 3.7.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    Plik danych ```feedPollDelay``` o zmianie jest ustawiony na 100 ms; w związku z tym, Change Feed reaguje ```updateInventoryTypeMaterializedView()``` na tę aktualizację niemal natychmiast i wywołania pokazane powyżej. To ostatnie wywołanie funkcji spowoduje upsert nowego dokumentu z TTL 5sec do **InventoryContainer-pktype**.

    Efekt jest taki, że po około 5 sekundach dokument wygaśnie i zostanie usunięty z obu kontenerów.

    Ta procedura jest konieczna, ponieważ plik danych o zmianie powoduje tylko problemy z zdarzeniami podczas wstawiania lub aktualizowania towaru, a nie przy usuwaniu zapasu.

1. Naciśnij przycisk Wprowadź jeszcze raz, aby zamknąć program i oczyścić jego zasoby.
