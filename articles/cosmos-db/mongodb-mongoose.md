---
title: Łączenie aplikacji Node.js Mongoose z usługą Azure Cosmos DB
description: Dowiedz się, jak używać mongoose framework do przechowywania danych i zarządzania nimi w usłudze Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: ff4455571aa5cfa5c9214bdf18af1853b0cef352
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585399"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Łączenie aplikacji Node.js Mongoose z usługą Azure Cosmos DB

W tym samouczku pokazano, jak używać [struktury Mongoose](https://mongoosejs.com/) podczas przechowywania danych w usłudze Cosmos DB. Używamy interfejsu API usługi Azure Cosmos DB dla mongodb dla tego przewodnika. Mongoose to platforma modelowania obiektów usługi MongoDB w środowisku Node.js. Udostępnia ona także proste, bazujące na schematach rozwiązanie do modelowania danych aplikacji.

Cosmos DB to globalnie rozproszona wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) wersja v0.10.29 lub nowsza.

## <a name="create-a-cosmos-account"></a>Tworzenie konta usługi Cosmos

Utwórzmy konto usługi Cosmos. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku Konfigurowanie aplikacji Node.js. Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj kroki w [emulatorze usługi Azure Cosmos DB,](local-emulator.md) aby skonfigurować emulator i przejdź do przodu, aby skonfigurować aplikację Node.js.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Tworzenie bazy danych 
W tej aplikacji omówimy dwa sposoby tworzenia kolekcji w usłudze Azure Cosmos DB: 
- **Przechowywanie każdego modelu obiektu w osobnej kolekcji:** Zalecamy [utworzenie bazy danych z dedykowaną przepływnością.](set-throughput.md#set-throughput-on-a-database) Korzystanie z tego modelu pojemności zapewni lepszą efektywność kosztową.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Samouczek node.js — zrzut ekranu przedstawiający sposób tworzenia bazy danych w Eksploratorze danych dla konta usługi Azure Cosmos DB do użycia z modułem węzła Mongoose Node":::

- **Przechowywanie wszystkich modeli obiektów w pojedynczej kolekcji usługi Cosmos DB:** Jeśli wolisz przechowywać wszystkie modele w pojedynczej kolekcji, możesz po prostu utworzyć nową bazę danych bez wybierania opcji Przepływność aprowizowania. Za pomocą tego modelu pojemności utworzy każdej kolekcji z własną przepustowość pojemności dla każdego modelu obiektu.

Po utworzeniu bazy danych użyjesz nazwy `COSMOSDB_DBNAME` w zmiennej środowiskowej poniżej.

## <a name="set-up-your-nodejs-application"></a>Konfigurowanie aplikacji Node.js

>[!Note]
> Jeśli chcesz tylko prześledzić przykładowy kod bez konfigurowania samej aplikacji, sklonuj [przykład](https://github.com/Azure-Samples/Mongoose_CosmosDB) używany na potrzeby tego samouczka i skompiluj aplikację Mongoose napisaną w języku Node.js w usłudze Azure Cosmos DB.

1. Aby utworzyć aplikację Node.js w wybranym folderze, uruchom następujące polecenie w wierszu polecenia węzła.

    ```npm init```

    Po odpowiedzeniu na wyświetlone pytania projekt będzie gotowy.

2. Dodaj nowy plik do folderu i nadaj mu nazwę ```index.js```.
3. Zainstaluj wymagane pakiety przy użyciu jednej z opcji polecenia ```npm install```:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > Mongoose przykład połączenia poniżej opiera się na Mongoose 5 +, który zmienił się od wcześniejszych wersji.
    
   * Dotenv (jeśli chcesz załadować wpisy tajne z pliku env): ```npm install dotenv --save```

     >[!Note]
     > Parametr ```--save``` powoduje dodanie zależności do pliku package.json.

4. Zaimportuj zależności w pliku index.js.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Dodaj parametry połączenia usługi Cosmos DB i nazwę bazy danych Cosmos DB do pliku ```.env```. Zastąp symbole zastępcze {cosmos-account-name} i {dbname} własną nazwą konta i nazwy bazy danych usługi Cosmos bez symboli nawiasów klamrowych.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMODDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Połącz się z usługi Cosmos DB przy użyciu struktury Mongoose, dodając następujący kod na końcu index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > W tym miejscu zmienne środowiskowe są ładowane przy użyciu metody process.env.{nazwa_zmiennej} i pakietu npm „dotenv”.

    Teraz, po nawiązaniu połączenia z usługą Azure Cosmos DB, możesz rozpocząć konfigurowanie modeli obiektów na platformie Mongoose.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Najważniejsze wskazówki dotyczące korzystania z mongoose z cosmos DB

Dla każdego tworzonego modelu Mongoose tworzy nową kolekcję. Jest to najlepiej rozwiązać przy użyciu [opcji przepływność poziomu bazy danych](set-throughput.md#set-throughput-on-a-database), który został wcześniej omówione. Aby użyć jednej kolekcji, należy użyć Mongoose [Discriminators](https://mongoosejs.com/docs/discriminators.html). Dyskryminatory to mechanizm dziedziczenia schematów. Umożliwiają one posiadanie wielu modeli z nakładającymi się schematami w jednej bazowej kolekcji usługi MongoDB.

Dzięki temu można przechowywać różne modele danych w tej samej kolekcji, a następnie, korzystając z klauzuli filtru w czasie przetwarzania zapytania, pobierać tylko potrzebne dane. Przejdźmy przez każdy z modeli.

### <a name="one-collection-per-object-model"></a>Jedna kolekcja na model obiektów

W tej sekcji opisano, jak to osiągnąć za pomocą interfejsu API usługi Azure Cosmos DB dla mongodb. Ta metoda jest naszym zalecanym podejściem, ponieważ pozwala kontrolować koszty i pojemność. W rezultacie ilość jednostek żądania w bazie danych nie zależy od liczby modeli obiektów. Jest to domyślny model operacyjny dla Mongoose, więc możesz być o tym zaznajomiony.

1. Otwórz ponownie plik ```index.js```.

1. Utwórz definicję schematu modelu „Family” (Rodzina).

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Utwórz obiekt w modelu „Family” (Rodzina).

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Na koniec zapiszmy obiekt do usługi Cosmos DB. Spowoduje to utworzenie kolekcji w obiekcie nadrzędnym.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Teraz utwórzmy jeszcze jeden schemat i obiekt. Tym razem utwórzmy te elementy dla modelu „Vacation Destinations” (Miejsca spędzania wakacji) zawierającego miejsca atrakcyjne dla rodziny.
   1. Podobnie jak wcześniej utwórz schemat.
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Utwórz przykładowy obiekt (do tego schematu można dodać wiele obiektów) i zapisz go.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Teraz przechodząc do witryny Azure portal, można zauważyć dwie kolekcje utworzone w usłudze Cosmos DB.

    ![Samouczek node.js — zrzut ekranu przedstawiający witrynę Azure Portal, przedstawiającą konto usługi Azure Cosmos DB z wyróżnioną wieloma nazwami kolekcji — baza danych węzłów][multiple-coll]

1. Na koniec przeczytajmy dane z usługi Cosmos DB. Ponieważ korzystamy z domyślnego modelu działania platformy Mongoose, operacje odczytywania są takie same jak inne operacje odczytywania na platformie Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Korzystanie z dyskryminatorów platformy Mongoose w celu umożliwienia przechowywania danych w pojedynczej kolekcji

W tej metodzie używamy [Mongoose Discriminators,](https://mongoosejs.com/docs/discriminators.html) aby pomóc zoptymalizować pod kątem kosztów każdej kolekcji. Dyskryminatory umożliwiają zdefiniowanie różnicującego „klucza”, który umożliwi przechowywanie, różnicowanie i filtrowanie różnych modeli obiektów.

W tym miejscu utworzymy bazowy model obiektów, zdefiniujemy klucz różnicujący i dodamy modele „Family” i „VacationDestinations” jako rozszerzenia modelu bazowego.

1. Ustawmy podstawową konfigurację i zdefiniujmy klucz dyskryminatora.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Następnie zdefiniujmy wspólny model obiektów.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Teraz definiujemy model „Family”. Zauważ, że teraz używamy metody ```commonModel.discriminator``` zamiast ```mongoose.model```. Ponadto dodajemy również podstawową konfigurację do schematu mongoose. W tym przypadku kluczem dyskryminatora jest więc ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Podobnie dodajmy kolejny schemat, tym razem dla modelu „VacationDestinations”. Tutaj kluczem dyskryminatora jest ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Na koniec utworzymy obiekty modelu i zapiszemy go.
   1. Dodajmy obiekty do modelu „Family”.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Następnie dodajmy obiekty do modelu „VacationDestinations” i zapiszmy go.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Jeśli teraz wrócisz do witryny Azure Portal, zauważysz, że znajduje się tam tylko jedna kolekcja o nazwie ```alldata```, która zawiera dane zarówno z modelu „Family”, jak i „VacationDestinations”.

    ![Samouczek node.js — zrzut ekranu przedstawiający witrynę Azure Portal, przedstawiającą konto usługi Azure Cosmos DB z wyróżnioną nazwą kolekcji — baza danych węzłów][alldata]

1. Zauważ również, że każdy obiekt ma jeszcze jeden atrybut o nazwie ```__type```, który pomaga rozróżnić te dwa różne modele obiektów.

1. Na koniec spróbujmy odczytać dane przechowywane w usłudze Azure Cosmos DB. Platforma Mongoose dba o odpowiednie filtrowanie danych na podstawie modelu. Podczas odczytywania danych nie trzeba robić niczego więcej. Wystarczy tylko określić model (w tym przypadku ```Family_common```), a platforma Mongoose zajmie się filtrowaniem według klucza „DiscriminatorKey”.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Jak widać, praca z dyskryminatorami platformy Mongoose jest prosta. Tak jeśli masz aplikację, która używa struktury Mongoose, ten samouczek jest sposobem, aby uzyskać aplikację i działa przy użyciu interfejsu API usługi Azure Cosmos dla MongoDB bez konieczności zbyt wielu zmian.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
