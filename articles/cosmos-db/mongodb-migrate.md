---
title: Używanie poleceń mongoimport i mongorestore z interfejsem API usługi Azure Cosmos DB na potrzeby bazy danych MongoDB
description: Dowiedz się, jak używać poleceń mongoimport i mongorestore do importowania danych do interfejsu API dla konta bazy danych MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 50bb34d86780dec003c63b5ff0a3884049dd47c1
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871017"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-mongodb-api-account"></a>Samouczek: migrowanie danych na konto interfejsu API bazy danych MongoDB w usłudze Cosmos DB

Ten samouczek zawiera instrukcje dotyczące sposobu migrowania danych przechowywanych w bazie danych MongoDB na konto interfejsu API bazy danych MongoDB w usłudze Cosmos DB. Jeśli dane są importowane z bazy danych MongoDB i mają być używane w obrębie interfejsu API SQL usługi Azure Cosmos DB, należy zaimportować dane przy użyciu [narzędzia migracji danych](import-data.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Planowanie migracji
> * Wymagania wstępne dotyczące migracji
> * Migrowanie danych przy użyciu polecenia mongoimport
> * Migrowanie danych przy użyciu polecenia mongorestore

Przed zmigrowaniem danych na konto interfejsu API bazy danych MongoDB upewnij się, że masz przykładowe dane bazy danych MongoDB. Jeśli nie masz przykładowej bazy danych MongoDB, możesz pobrać i zainstalować [serwer społeczności MongoDB](https://www.mongodb.com/download-center), utworzyć przykładową bazę danych i przekazać przykładowe dane za pomocą pliku mongoimport.exe lub mongorestore.exe. 

## <a name="plan-for-migration"></a>Planowanie migracji

1. Wstępnie utwórz kolekcje i przeprowadź ich skalowanie:
        
   * Domyślnie usługa Azure Cosmos DB aprowizuje nową kolekcję bazy danych MongoDB przy użyciu 1000 jednostek żądania na sekundę (RU/s). Przed rozpoczęciem migracji za pomocą polecenia mongoimport lub mongorestore wstępnie utwórz wszystkie swoje kolekcje przy użyciu witryny [Azure Portal](https://portal.azure.com) lub sterowników i narzędzi bazy danych MongoDB. Jeśli rozmiar danych jest większy niż 10 GB, pamiętaj, aby utworzyć [kolekcję podzieloną na partycje](partition-data.md) przy użyciu odpowiedniego klucza partycjonującego. Baza danych MongoDB zaleca przechowywanie danych jednostki w kolekcjach. Jednostki, które są podobnej wielkości, możesz umieścić w jednej lokalizacji i aprowizować przepływność na poziomie bazy danych Azure Cosmos.

   * W witrynie [Azure Portal](https://portal.azure.com) zwiększ przepływność kolekcji z 1000 RU/s dla kolekcji z jedną partycją i 2500 RU/s dla kolekcji pofragmentowanej, tylko na czas trwania migracji. Dzięki większej przepływności można uniknąć ograniczania przepustowości i przeprowadzać migrację w krótszym czasie. Możesz zmniejszyć przepływność natychmiast po migracji w celu ograniczenia kosztów.

   * Oprócz aprowizowania jednostek RU/s na poziomie kolekcji można również aprowizować jednostki RU/s dla zestawu kolekcji na poziomie nadrzędnej bazy danych. Wymaga to wstępnego utworzenia bazy danych i kolekcji, a także zdefiniowania klucza partycjonującego dla każdej kolekcji.

   * Kolekcje pofragmentowane można tworzyć za pomocą ulubionego narzędzia, sterownika lub zestawu SDK. W tym przykładzie tworzymy kolekcję pofragmentowaną przy użyciu powłoki Mongo:

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Wyniki:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. Oblicz przybliżoną opłatę za jednostkę RU na potrzeby zapisu pojedynczego dokumentu:

   a. Nawiąż połączenie z kontem interfejsu API bazy danych MongoDB w usłudze Azure Cosmos DB z poziomu powłoki bazy danych MongoDB. Instrukcje można znaleźć w temacie [Connect a MongoDB application to Azure Cosmos DB (Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB)](connect-mongodb-account.md).
    
   b. Uruchom przykładowe polecenie insert, używając jednego z przykładowych dokumentów z poziomu powłoki bazy danych MongoDB:
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   d. Uruchom polecenie ```db.runCommand({getLastRequestStatistics: 1})```. Otrzymasz odpowiedź podobną do następującej:
     
      ```bash
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
      ```
        
    d. Zanotuj opłatę za żądanie.
    
1. Określ opóźnienie między maszyną i usługą w usłudze w chmurze Azure Cosmos DB:
    
    a. Włącz pełne rejestrowanie z poziomu powłoki bazy danych MongoDB za pomocą tego polecenia: ```setVerboseShell(true)```
    
    b. Uruchom proste zapytanie w bazie danych: ```db.coll.find().limit(1)```. Otrzymasz odpowiedź podobną do następującej:

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. Usuń wstawiony dokument przed migracją, aby upewnić się, że nie ma zduplikowanych dokumentów. Dokumenty można usunąć przy użyciu tego polecenia: ```db.coll.remove({})```

1. Oblicz przybliżone wartości *batchSize* i *numInsertionWorkers*:

    * Aby uzyskać rozmiar *batchSize*, podziel łączną liczbę aprowizowanych jednostek RU przez liczbę jednostek RUs zużytych w ramach pojedynczego zapisu dokumentu w kroku 3.
    
    * Jeśli obliczona wartość *batchSize* <= 24, użyj tej liczby jako wartości *batchSize*.
    
    * Jeśli obliczona wartość *batchSize* > 24, ustaw wartość *batchSize* na 24.
    
    * Aby uzyskać wartość *numInsertionWorkers*, użyj równania: *numInsertionWorkers = (aprowizowana przepływność * opóźnienie w sekundach) / (rozmiar partii * jednostki RU użyte podczas pojedynczego zapisu)*.
        
    |Właściwość|Wartość|
    |--------|-----|
    |batchSize| 24 |
    |Aprowizowane jednostki RU | 10 000 |
    |Opóźnienie | 0,100 s |
    |Jednostki RU naliczane za 1 zapis dokumentu | 10 jednostek RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RU x 0,1 s) / (24 x 10 RU) = 4,1666*

1. Uruchom polecenie migracji. Opcje migrowania danych opisano w następnych sekcjach.

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Lub użyj polecenia mongorestore (upewnij się, wszystkie kolekcje mają przepływność ustawioną na liczbę jednostek RU używanych w poprzednich obliczeniach lub większą):
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>Wymagania wstępne dotyczące migracji

* **Zwiększenie przepływności:** czas trwania migracji danych zależy od przepływności skonfigurowanej dla pojedynczej kolekcji lub dla zestawu kolekcji. Pamiętaj o zwiększeniu przepływności w przypadku większych migracji danych. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Aby uzyskać więcej informacji na temat zwiększania przepływności w witrynie [Azure Portal](https://portal.azure.com), zobacz [Performance levels and pricing tiers in Azure Cosmos DB (Poziomy wydajności i warstwy cenowe w usłudze Azure Cosmos DB)](performance-levels.md).

* **Włączenie protokołu SSL:** usługa Azure Cosmos DB ma ścisłe wymagania i standardy dotyczące bezpieczeństwa. Pamiętaj, aby włączyć protokół SSL w przypadku interakcji z kontem. Procedury w pozostałej części tego artykułu obejmują włączanie protokołu SSL na potrzeby poleceń mongoimport i mongorestore.

* **Utworzenie zasobów usługi Azure Cosmos DB:** przed rozpoczęciem migracji danych utwórz wstępnie wszystkie kolekcje w witrynie Azure Portal. W przypadku migracji na konto usługi Azure Cosmos DB z przepływnością poziomu bazy danych pamiętaj o podaniu klucza partycji podczas tworzenia kolekcji usługi Azure Cosmos DB.

## <a name="get-your-connection-string"></a>Pobieranie parametrów połączenia 

1. W witrynie [Azure Portal](https://portal.azure.com) w okienku po lewej stronie kliknij wpis **Azure Cosmos DB**.
1. W okienku **Subskrypcje** wybierz nazwę konta.
1. W bloku **Parametry połączenia** kliknij pozycję **Parametry połączenia**.

   Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.

   ![Blok Parametry połączenia](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>Migrowanie danych przy użyciu polecenia mongoimport

Aby zaimportować dane do konta usługi Azure Cosmos DB, użyj poniższego szablonu. Wypełnij pola *host*, *nazwa użytkownika* i *hasło* wartościami specyficznymi dla swojego konta.  

Szablon:

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Przykład:  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>Migrowanie danych przy użyciu polecenia mongorestore

Aby przywrócić dane do interfejsu API dla konta bazy danych MongoDB, przeprowadź importowanie przy użyciu poniższego szablonu. Wypełnij pola *host*, *nazwa użytkownika* i *hasło* wartościami specyficznymi dla swojego konta.

Szablon:

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Przykład:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>Następne kroki

Możesz przejść do następnego samouczka i dowiedzieć się, jak wykonywać zapytania o dane MongoDB przy użyciu usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak wykonać zapytanie dotyczące danych bazy danych MongoDB?](../cosmos-db/tutorial-query-mongodb.md)
