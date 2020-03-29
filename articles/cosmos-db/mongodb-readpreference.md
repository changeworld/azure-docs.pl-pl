---
title: Użyj preferencji odczytu z interfejsem API usługi Azure Cosmos DB dla usługi MongoDB
description: Dowiedz się, jak korzystać z preferencji odczytu mongodb za pomocą interfejsu API usługi Azure Cosmos DB dla usługi MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 10e6ed556abe8f8c438e5436fbb93c1b70b85d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445156"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Jak globalnie rozpowszechniać odczyty przy użyciu interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

W tym artykule pokazano, jak globalnie dystrybuować operacje odczytu z [ustawieniami preferencji odczytu mongodb](https://docs.mongodb.com/manual/core/read-preference/) przy użyciu interfejsu API usługi Azure Cosmos DB dla mongodb.

## <a name="prerequisites"></a>Wymagania wstępne 
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Zapoznaj się z tym [artykułem przewodnika Szybki start,](tutorial-global-distribution-mongodb.md) aby uzyskać instrukcje dotyczące korzystania z witryny Azure Portal w celu skonfigurowania konta usługi Cosmos z dystrybucją globalną, a następnie nawiązania z nim połączenia.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.  

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium. Na podstawie interesującej platformy użyj jednego z następujących przykładowych repozytoriów:

1. [Przykładowa aplikacja .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Przykładowa aplikacja NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose przykładowe zastosowanie](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Przykładowa aplikacja Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Aplikacja próbki SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

W zależności od użytej platformy zainstaluj wymagane pakiety i uruchom aplikację. Aby zainstalować zależności, postępuj zgodnie z readme zawarte w przykładowym repozytorium aplikacji. Na przykład w przykładowej aplikacji NodeJS użyj następujących poleceń, aby zainstalować wymagane pakiety i uruchomić aplikację.

```bash
cd mean
npm install
node index.js
```
Aplikacja próbuje połączyć się ze źródłem MongoDB i kończy się niepowodzeniem, ponieważ parametry połączenia są nieprawidłowe. Postępuj zgodnie z instrukcjami w `url`pliku README, aby zaktualizować parametry połączenia . Ponadto zaktualizuj `readFromRegion` do regionu odczytu na koncie usługi Cosmos. Poniższe instrukcje pochodzą z przykładu NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Po wykonać następujące kroki, przykładowa aplikacja uruchamia i generuje następujące dane wyjściowe:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Odczytywanie w trybie preferencji odczytu

Protokół MongoDB udostępnia klientom następujące tryby preferencji odczytu:

1. Podstawowy
2. PRIMARY_PREFERRED
3. Pomocniczy
4. SECONDARY_PREFERRED
5. Najbliższe

Szczegółowe informacje na temat zachowania każdego z tych trybów preferencji odczytu można znaleźć w szczegółowej dokumentacji zachowania preferencji odczytu [w mongodb.](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) W usłudze Cosmos DB: podstawowe mapy regionu zapisu i mapy pomocnicze do regionu ODCZYTu.

Na podstawie typowych scenariuszy zaleca się użycie następujących ustawień:

1. Jeśli wymagane są **odczyty o małym opóźnieniu,** należy użyć trybu preferencji **odczytu NEAREST.** To ustawienie kieruje operacje odczytu do najbliższego dostępnego regionu. Należy zauważyć, że jeśli najbliższym regionem jest region WRITE, operacje te są kierowane do tego regionu.
2. Jeśli wymagana jest **wysoka dostępność i dystrybucja geograficzna odczytów** (opóźnienie nie jest ograniczeniem), należy użyć trybu preferencji odczytu **WTÓRNEGO PREFEROWANEGO.** To ustawienie kieruje operacje odczytu do dostępnego regionu ODCZYTu. Jeśli nie region READ jest dostępny, a następnie żądania są kierowane do regionu WRITE.

Poniższy fragment kodu z przykładowej aplikacji pokazuje, jak skonfigurować preferencje odczytu NEAREST w NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobnie fragment kodu poniżej pokazuje, jak skonfigurować SECONDARY_PREFERRED preferencji odczytu w NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Preferencje odczytu można również `readPreference` ustawić, przekazując jako parametr w opcjach identyfikatora URI ciągu połączenia:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Zapoznaj się z odpowiednimi przykładowymi reponami aplikacji dla innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Czytanie przy użyciu znaczników

Oprócz trybu preferencji odczytu protokół MongoDB umożliwia używanie tagów do bezpośrednich operacji odczytu. W interfejsie API usługi Cosmos DB `region` dla MongoDB tag jest `isMaster` domyślnie uwzględniony jako część odpowiedzi:

```json
"tags": {
         "region": "West US"
      }
```

W związku z tym MongoClient można użyć tagu `region` wraz z nazwą regionu do kierowania operacji odczytu do określonych regionów. W przypadku kont usługi Cosmos nazwy regionów można znaleźć w witrynie Azure portal po lewej stronie w obszarze **Dane >Repliki ustawień globalnie**. To ustawienie jest przydatne do osiągnięcia **izolacji odczytu** — przypadki, w których aplikacja kliencka chce kierować operacje odczytu tylko do określonego regionu. To ustawienie jest idealne dla scenariuszy typu nieprodukcyjnego/analitycznego, które działają w tle i nie są usługami krytycznymi dla produkcji.

Poniższy fragment kodu z przykładowej aplikacji pokazuje, jak skonfigurować preferencje odczytu za pomocą tagów w NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Zapoznaj się z odpowiednimi przykładowymi reponami aplikacji dla innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

W tym artykule dowiesz się, jak globalnie rozpowszechniać operacje odczytu przy użyciu preferencji odczytu z interfejsem API usługi Azure Cosmos DB dla usługi MongoDB.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń wszystkie zasoby utworzone przez ten artykuł w witrynie Azure portal, wykonując następujące kroki:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
* [Konfigurowanie globalnie rozproszonej bazy danych za pomocą interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](tutorial-global-distribution-mongodb.md)
* [Rozwijaj lokalnie za pomocą emulatora usługi Azure Cosmos DB](local-emulator.md)
