---
title: Importowanie danych Cassandra do bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak polecenie kopiowania CQL skopiować Cassandra danych do bazy danych Azure rozwiązania Cosmos.
services: cosmos-db
author: govindk
manager: kfile
documentationcenter: ''
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 64f60e6beb5451d8f5acd382ca8e5672a2d096f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure DB rozwiązania Cosmos: Cassandra importowania danych

Ten samouczek zawiera instrukcje dotyczące importowania danych Cassandra do bazy danych rozwiązania Cosmos Azure za pomocą polecenia Kopiuj Cassandra Query Language (CQL). 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Podczas pobierania parametrów połączenia
> * Importowanie danych przy użyciu polecenia Kopiuj cqlsh
> * Importowanie przy użyciu łącznika Spark 

# <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Apache Cassandra](http://cassandra.apache.org/download/) i upewnij się, w szczególności *cqlsh* jest obecny.
* Zwiększyć przepływność: czas trwania migracji danych zależy od ilości zainicjowanej tabel przepływności. Pamiętaj zwiększyć przepływność większych migracji danych. Po zakończeniu migracji należy zmniejszyć przepustowość w celu ograniczenia kosztów. Aby uzyskać więcej informacji o zwiększenie przepływności w [portalu Azure](https://portal.azure.com), zobacz [przepływności zestawu dla kontenerów bazy danych Azure rozwiązania Cosmos](set-throughput.md).
* Włącz protokół SSL: Azure DB rozwiązania Cosmos ma wymogi ograniczeniami zabezpieczeń. Pamiętaj włączyć protokół SSL w przypadku interakcji z Twoim kontem. Gdy używasz CQL przy użyciu protokołu SSH, istnieje możliwość udzielenia informacji protokołu SSL. 

## <a name="find-your-connection-string"></a>Znajdź parametry połączenia

1. W [portalu Azure](https://portal.azure.com), lewo, kliknij **bazy danych Azure rozwiązania Cosmos**.

2. W **subskrypcje** okienku, wybierz nazwę konta.

3. Kliknij przycisk **ciąg połączenia**. Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.

    ![Strona parametry połączenia](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Użyj cqlsh KOPIOWANIA

Aby zaimportować Cassandra danych do bazy danych Azure rozwiązania Cosmos do użycia przy użyciu interfejsu API Cassandra, użyj poniższych wskazówek:

1. Zaloguj się do cqhsh przy użyciu informacje o połączeniu z portalu.
2. Użyj [polecenia Kopiuj CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) do skopiowania do punktu końcowego interfejsu API Cassandra Apache danych lokalnych. Upewnij się, źródłowa i docelowa są w tym samym centrum danych, aby zminimalizować problemy z opóźnieniem.

### <a name="guide-for-moving-data-with-cqlsh"></a>Przewodnik przenoszenia danych za pomocą cqlsh

1. Wstępnie tworzyć i skalować tabeli:
    * Domyślnie obsługuje bazy danych Azure rozwiązania Cosmos nowy interfejs API Cassandra tabeli z 1000 jednostek żądania na sekundę (RU/s) (Tworzenie na podstawie CQL jest udostępniane z 400 RU/s). Przed rozpoczęciem migracji za pomocą cqlsh Utwórz wstępnie wszystkie tabele z [portalu Azure](https://portal.azure.com) lub cqlsh. 

    * Z [portalu Azure](https://portal.azure.com), zwiększyć przepływność tabele przepustowość domyślne (400 lub 1000 RU/s) do 10 000 RU/s na czas trwania migracji. Z wyższej przepustowości można uniknąć, ograniczania i migracji w krótszym czasie. Dzięki co godzinę rozliczeń w usłudze Azure DB rozwiązania Cosmos, można ograniczyć przepustowość natychmiast po migracji, w celu ograniczenia kosztów.

2. Określ opłata RU dla operacji. Można to zrobić przy użyciu rozwiązania Cosmos DB Cassandra interfejsu API zestawu SDK usługi Azure wybranych przez użytkownika. Ten przykład przedstawia programu .NET w wersji pobierania RU opłat. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Określ opóźnienie z komputera z usługą Azure DB rozwiązania Cosmos. Jeśli jesteś w obrębie centrum danych Azure, opóźnienie musi być liczbą milisekund niski pojedynczą cyfrą. Jeśli użytkownik znajduje się poza centrum danych Azure, następnie służy narzędzia psping lub azurespeed.com można pobrać z lokalizacji przybliżony czas oczekiwania.   

4. Oblicz odpowiednie wartości dla parametrów (NUMPROCESS INGESTRATE, MAXBATCHSIZE albo MINBATCHSIZE), które zapewniają dobrą wydajność. 

5. Uruchom polecenie końcowego migracji. Uruchomienie tego polecenia założono uruchomieniu cqlsh przy użyciu ciągu połączenia.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Korzystanie z platformy Spark do importowania danych

Dla danych znajdujących się w istniejącym klastrze w maszynach wirtualnych platformy Azure importowanie danych przy użyciu platformy Spark jest również możliwe. Wymaga to Spark skonfigurować jako pośrednik jeden raz lub wprowadzanie regularne. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku kiedy znasz już sposób wykonania następujących zadań:

> [!div class="checklist"]
> * Pobranie parametrów połączenia
> * Importuj dane przy użyciu polecenia Kopiuj cql
> * Import za pomocą łącznika Spark 

Możesz teraz przejść do sekcji pojęcia, aby uzyskać więcej informacji na temat bazy danych Azure rozwiązania Cosmos. 

> [!div class="nextstepaction"]
>[Poziomy spójności danych dostosowywalne w usłudze Azure DB rozwiązania Cosmos](../cosmos-db/consistency-levels.md)
