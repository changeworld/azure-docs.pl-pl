---
title: Korzystania z interfejsu API Azure rozwiązania Cosmos bazy danych dla bazy danych MongoDB mongoimport i mongorestore | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać mongoimport i mongorestore do importowania danych do interfejsu API dla konta bazy danych MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 36d098a76e57b65ba82c24ed81ebbe3d21489a9f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Platformy Azure rozwiązania Cosmos bazy danych: Danych MongoDB importu 

Aby migrować dane z bazy danych MongoDB na konto usługi Azure Cosmos DB do użycia z interfejsem API dla bazy danych MongoDB, należy:

* Pobierz albo *mongoimport.exe* lub *mongorestore.exe* z [Centrum pobierania bazy danych MongoDB](https://www.mongodb.com/download-center).
* Pobrać [parametry połączenia interfejsu API dla bazy danych MongoDB](connect-mongodb-account.md).

Jeśli dane są importowane z bazy danych MongoDB i ma on korzystać z interfejsu API Azure rozwiązania Cosmos bazy danych SQL, należy użyć [narzędzia migracji danych](import-data.md) do importowania danych.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Podczas pobierania parametrów połączenia
> * Importowanie danych MongoDB przy użyciu mongoimport
> * Importowanie danych MongoDB przy użyciu mongorestore

## <a name="prerequisites"></a>Wymagania wstępne

* Zwiększyć przepływność: czas trwania migracji danych zależy od ilości przepływności, możesz skonfigurować dla poszczególnych kolekcji lub zestaw kolekcji. Pamiętaj zwiększyć przepływność większych migracji danych. Po zakończeniu migracji należy zmniejszyć przepustowość w celu ograniczenia kosztów. Aby uzyskać więcej informacji o zwiększenie przepływności w [portalu Azure](https://portal.azure.com), zobacz [poziomy wydajności i warstw cenowych w usłudze Azure DB rozwiązania Cosmos](performance-levels.md).

* Włącz protokół SSL: Azure DB rozwiązania Cosmos ma wymogi ograniczeniami zabezpieczeń. Pamiętaj włączyć protokół SSL w przypadku interakcji z Twoim kontem. Procedury w pozostałej części tego artykułu obejmują włączania protokołu SSL dla mongoimport i mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Znajdź informacje ciągu połączenia (host, port, nazwę użytkownika i hasło)

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie kliknij **bazy danych Azure rozwiązania Cosmos** wpisu.
2. W **subskrypcje** okienku, wybierz nazwę konta.
3. W **ciąg połączenia** bloku, kliknij przycisk **ciąg połączenia**.

   Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.

   ![Blok ciągu połączenia](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importowanie danych do interfejsu API dla bazy danych MongoDB przy użyciu mongoimport

Aby zaimportować dane do swojego konta bazy danych Azure rozwiązania Cosmos, szablon. Wypełnij *hosta*, *username*, i *hasło* z wartościami, które są specyficzne dla swojego konta.  

Szablon:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Przykład:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importowanie danych do interfejsu API dla bazy danych MongoDB przy użyciu mongorestore

Aby przywrócić dane do interfejsu API dla konta bazy danych MongoDB, należy użyć następującego szablonu można wykonać importu. Wypełnij *hosta*, *username*, i *hasło* z wartościami, które są specyficzne dla swojego konta.

Szablon:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Przykład:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Przewodnik po pomyślnej migracji

1. Wstępnie tworzyć i skalować kolekcji:
        
    * Domyślnie program Azure DB rozwiązania Cosmos inicjuje nową kolekcję bazy danych MongoDB z 1000 jednostek żądania (RUs/s). Przed rozpoczęciem migracji za pomocą mongoimport, mongorestore lub mongomirror wstępnego tworzenia Twojej kolekcji z [portalu Azure](https://portal.azure.com) lub z bazy danych MongoDB sterowników i narzędzi. Jeśli kolekcja jest większa niż 10 GB, upewnij się utworzyć [podzielonej/podzielona na partycje kolekcji](partition-data.md) przy użyciu klucza odpowiedni identyfikator niezależnego fragmentu.

    * Z [portalu Azure](https://portal.azure.com), zwiększyć przepływność sieci kolekcje z 1000 RUs na sekundę dla kolekcji jednej partycji i 2500 RUs/s dla podzielonej kolekcji tylko do migracji. Z wyższej przepustowości można uniknąć, ograniczania i migracji w krótszym czasie. Dzięki co godzinę rozliczeń w usłudze Azure DB rozwiązania Cosmos, można ograniczyć przepustowość natychmiast po migracji, w celu ograniczenia kosztów.

    * Oprócz udostępniania RUs na sekundę na poziomie kolekcji może również udostępniać RU/s dla zestawu kolekcji na poziomie nadrzędnym bazy danych. Wymaga to wstępne tworzenie bazy danych i kolekcji, a także do definiowania niezależnego fragmentu klucz dla każdej kolekcji.

    * Możesz utworzyć kolekcje podzielonej przez ulubionego narzędzia, sterownika lub zestawu SDK. W tym przykładzie używamy powłoki Mongo utworzyć kolekcję podzielonej:

        ```
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

2. Obliczania przybliżonej opłaty RU do zapisu pojedynczego dokumentu:

    a. Połączenia z bazą danych Azure rozwiązania Cosmos bazy danych MongoDB z poziomu powłoki bazy danych MongoDB. Instrukcje można znaleźć [połączyć aplikację bazy danych MongoDB do bazy danych Azure rozwiązania Cosmos](connect-mongodb-account.md).
    
    b. Polecenie insert próbki za pomocą jednego z przykładowych dokumentów z poziomu powłoki bazy danych MongoDB:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Uruchom ```db.runCommand({getLastRequestStatistics: 1})``` i otrzymasz odpowiedź podobnie do następującej:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Zanotuj opłata żądania.
    
3. Określ opóźnienie z komputera do usługi w chmurze Azure DB rozwiązania Cosmos:
    
    a. Włącz pełne rejestrowanie z poziomu powłoki bazy danych MongoDB za pomocą tego polecenia: ```setVerboseShell(true)```
    
    b. Uruchom proste zapytanie w bazie danych: ```db.coll.find().limit(1)```. Otrzymasz odpowiedź podobne do poniższych:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Usuń wstawianego dokumentu przed migracją, aby upewnić się, że nie ma żadnych zduplikowanych dokumentów. Należy usunąć dokumentów za pomocą tego polecenia: ```db.coll.remove({})```

5. Oblicz przybliżonej *batchSize* i *numInsertionWorkers* wartości:

    * Aby uzyskać *batchSize*, dzielenie łączną elastycznie RUs przez RUs używane z Twojej zapisu pojedynczego dokumentu w kroku 3.
    
    * Jeśli obliczane *batchSize* < = 24, użyj tego numeru jako sieci *batchSize* wartość.
    
    * Jeśli obliczane *batchSize* > 24, ustaw *batchSize* wartość do 24.
    
    * Dla *numInsertionWorkers*, użyj równanie: *numInsertionWorkers = (udostępnionej przepływności * czas oczekiwania w sekundach) / (rozmiar partii * używane dla pojedynczego zapisu RUs)*.
        
    |Właściwość|Wartość|
    |--------|-----|
    |batchSize| 24 |
    |RUs udostępnione | 10 000 |
    |Opóźnienie | 0.100 s |
    |RU naliczona opłata za zapisu 1 doc | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (RUs 10000 x 0,1 s) / (RUs 24 x 10) = 4.1666*

6. Uruchom polecenie końcowego migracji:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Kolejne kroki

Możesz przejść do następnego samouczek i Dowiedz się, jak wykonać zapytanie dotyczące bazy danych MongoDB danych przy użyciu bazy danych Azure rozwiązania Cosmos. 

> [!div class="nextstepaction"]
>[Jak wykonać zapytanie dotyczące bazy danych MongoDB danych?](../cosmos-db/tutorial-query-mongodb.md)
