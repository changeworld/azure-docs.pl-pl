---
title: Migrowanie danych bazy danych MongoDB do usługi Azure Cosmos DB przy użyciu poleceń mongoimport i mongorestore
description: Dowiesz się, jak używać poleceń mongoimport i mongorestore do importowania danych do usługi Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039054"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>Migrowanie danych MongoDB do usługi Azure Cosmos DB

 Ten samouczek zawiera instrukcje dotyczące sposobu migrowania danych przechowywanych w bazie danych MongoDB do usługi Azure Cosmos DB skonfigurowanej do korzystania z interfejsu API usługi Cosmos DB dla bazy danych MongoDB. Jeśli dane są importowane z bazy danych MongoDB i mają być używane w obrębie interfejsu API SQL usługi Azure Cosmos DB, należy zaimportować dane przy użyciu [narzędzia migracji danych](import-data.md).

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Przygotowanie planu migracji
> * Migrowanie danych przy użyciu polecenia mongoimport
> * Migrowanie danych przy użyciu polecenia mongorestore

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migracji sprawdź i spełnij następujące wymagania wstępne.

### <a name="plan-for-the-migration"></a>Planowanie migracji

W tej sekcji opisano sposób planowania migracji danych. Oszacujemy opłaty RU, określimy opóźnienie między maszyną i usługą w chmurze oraz obliczymy rozmiar partii i liczbę procesów roboczych wstawiania.


#### <a name="pre-create-and-scale-your-collections"></a>Wstępne tworzenie i skalowanie kolekcji

Przed rozpoczęciem migracji za pomocą polecenia mongoimport lub mongorestore wstępnie utwórz wszystkie swoje kolekcje przy użyciu witryny [Azure Portal](https://portal.azure.com) lub sterowników i narzędzi bazy danych MongoDB. 

W witrynie [Azure Portal](https://portal.azure.com) zwiększ przepływność kolekcji do migracji. Dzięki większej przepływności można uniknąć ograniczania przepustowości i przeprowadzić migrację w krótszym czasie. Możesz zmniejszyć przepływność natychmiast po migracji w celu ograniczenia kosztów.

Oprócz aprowizacji przepływności na poziomie kolekcji można również aprowizować przepływność na poziomie bazy danych dla zestawu kolekcji, aby udostępnić aprowizowaną przepływność. Należy wstępnie utworzyć bazę danych i kolekcje oraz zdefiniować klucz partycjonujący dla każdej kolekcji w bazie danych udostępnionej przepływności.

Kolekcje pofragmentowane można tworzyć za pomocą preferowanego narzędzia, sterownika lub zestawu SDK. W tym przykładzie tworzymy kolekcję pofragmentowaną przy użyciu powłoki Mongo:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Polecenie zwraca następujące wyniki:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Obliczanie przybliżonej opłaty za jednostkę żądania na potrzeby zapisu pojedynczego dokumentu

Z poziomu powłoki bazy danych MongoDB połącz się z kontem usługi Cosmos skonfigurowanym do korzystania z interfejsu API usługi Cosmos DB dla bazy danych MongoDB. Instrukcje można znaleźć w temacie [Connect a MongoDB application to Cosmos DB](connect-mongodb-account.md) (Łączenie aplikacji bazy danych MongoDB z usługą Cosmos DB).

Następnie uruchom przykładowe polecenie insert, używając jednego z przykładowych dokumentów:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Uruchom polecenie `db.runCommand({getLastRequestStatistics: 1})`.

Otrzymasz odpowiedź podobną do następującej:
     
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
        
Zanotuj opłatę za żądanie.
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>Określanie opóźnienia między maszyną i usługą Cosmos DB
    
Włącz pełne rejestrowanie z poziomu powłoki bazy danych MongoDB za pomocą polecenia `setVerboseShell(true)`.
    
Uruchom podstawowe zapytanie względem bazy danych za pomocą polecenia `db.coll.find().limit(1)`.

Otrzymasz odpowiedź podobną do następującej:

```bash
Fetched 1 record(s) in 100(ms)
```
        
Przed rozpoczęciem migracji usuń wstawiony dokument i upewnij się, że nie ma zduplikowanych dokumentów. Możesz usunąć dokumenty za pomocą polecenia `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Obliczanie przybliżonych wartości dla właściwości batchSize i numInsertionWorkers

Dla właściwości **batchSize** podziel łączną aprowizowaną przepływność (jednostki żądania/s) przez liczbę jednostek żądania wykorzystaną podczas zapisu pojedynczego dokumentu, jak opisano w sekcji „Określanie opóźnienia między maszyną i usługą Cosmos DB”. Jeśli obliczona wartość jest równa 24 lub mniejsza, należy użyć tej liczby jako wartości właściwości. Jeśli obliczona wartość jest większa niż 24, ustaw wartość właściwości na 24.
    
Dla wartości właściwości **numInsertionWorkers** użyj tego równania:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

Aby obliczyć wartość dla właściwości **numInsertionWorkers**, można użyć następujących wartości:

| Właściwość | Wartość |
|--------|-----|
| **batchSize** | 24 |
| Aprowizowane jednostki RU | 10 000 |
| Opóźnienie | 0,100 s |
| Wykorzystane jednostki RU | 10 jednostek RU |
| **numInsertionWorkers** | (10 000 jednostek RU x 0,100 s) / (24 x 10 jednostek RU) = **4,1666** |

Uruchom polecenie migracji **monogoimport**. Parametry polecenia są opisane w dalszej części tego artykułu.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Można również użyć polecenia **monogorestore**. Upewnij się, że wszystkie kolekcje mają przepływność ustawioną na liczbę jednostek RU używaną w poprzednich obliczeniach lub większą.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Spełnienie wymagań wstępnych

Po zaplanowaniu migracji wykonaj następujące kroki: 

* **Pobierz dane przykładowe**: przed rozpoczęciem migracji upewnij się, że masz pewne dane przykładowe. 

* **Zwiększ przepływność**: czas trwania migracji danych zależy od przepływności aprowizowanej dla pojedynczej kolekcji lub bazy danych. Pamiętaj o zwiększeniu przepływności w przypadku większych migracji danych. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. 

* **Włącz protokół SSL**:  usługa Cosmos DB ma ścisłe wymagania i standardy dotyczące bezpieczeństwa. Pamiętaj, aby włączyć protokół SSL w przypadku interakcji z kontem Cosmos. Procedury przedstawione w tym artykule obejmują włączanie protokołu SSL na potrzeby poleceń mongoimport i mongorestore.

* **Utwórz zasoby usługi Cosmos DB**: Przed rozpoczęciem migracji utwórz wstępnie wszystkie kolekcje w witrynie Azure Portal. W przypadku przeprowadzania migracji do konta Cosmos, które ma przepływność aprowizowaną na poziomie bazy danych, podczas tworzenia kolekcji pamiętaj, aby podać klucz partycji.

* **Pobierz parametry połączenia**: W witrynie [Azure Portal](https://portal.azure.com) wybierz wpis **Azure Cosmos DB** po lewej stronie. W obszarze **Subskrypcje** wybierz nazwę konta. W obszarze **Parametry połączenia** wybierz opcję **Parametry połączenia**. Prawa strona portalu zawiera informacje potrzebne do łączenia się z kontem:

    ![Informacje o parametrach połączenia](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Użycie polecenia mongoimport

Aby zaimportować dane do konta Cosmos, użyj poniższego szablonu.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Zastąp parametry \<your_hostname>, \<your_username> i \<your_password> wartościami odpowiednimi dla swojego konta. W poniższym przykładzie użyto **sampleDB** jako wartości dla \<your_database> i **sampleColl** jako wartości dla \<your_collection>:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Użycie polecenia mongorestore

Aby przywrócić dane do konta Cosmos skonfigurowanego za pomocą interfejsu API usługi Cosmos DB dla bazy danych MongoDB, użyj do importu poniższego szablonu.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Zastąp parametry \<your_hostname>, \<your_username> i \<your_password> wartościami odpowiednimi dla swojego konta. W poniższym przykładzie użyto **./dumps/dump-2016-12-07** jako wartości dla \<path_to_backup>:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć grupę zasobów, konto Cosmos oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby usunąć grupę zasobów, wykonaj następujące kroki:

1. Przejdź do grupy zasobów, w której zostało utworzone konto Cosmos.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Potwierdź nazwę grupy zasobów do usunięcia i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak wykonywać zapytania dotyczące danych za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. 

> [!div class="nextstepaction"]
> [Jak wykonać zapytanie dotyczące danych bazy danych MongoDB](../cosmos-db/tutorial-query-mongodb.md)
