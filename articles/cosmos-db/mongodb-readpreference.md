---
title: Korzystanie z preferencji odczytu bazy danych MongoDB przy użyciu interfejs API usługi MongoDB usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć preferencji odczytu bazy danych MongoDB przy użyciu interfejsu API MongoDB usługi Azure Cosmos DB
services: cosmos-db
author: vidhoonv
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: ''
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: sclyon
ms.openlocfilehash: b0af47f9ed72507fe9bc47023b456fcb157e25de
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091684"
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Jak globalnie dystrybuować odczytuje korzystanie z preferencji odczytu z interfejsem API MongoDB usługi Azure Cosmos DB 

W tym artykule pokazano, jak globalnie dystrybuować przy użyciu operacji odczytu [preferencji odczytu bazy danych MongoDB](https://docs.mongodb.com/manual/core/read-preference/) ustawienia z interfejsem API MongoDB usługi Azure Cosmos DB. 

## <a name="prerequisites"></a>Wymagania wstępne 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Zapoznaj się z tym [Szybki Start](tutorial-global-distribution-mongodb.md) artykuł, aby uzyskać instrukcje na temat konfigurowania konta usługi Azure Cosmos DB z globalnej dystrybucji, a następnie nawiązać połączenie przy użyciu interfejsu API usługi MongoDB przy użyciu witryny Azure portal.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.  

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium. Oparte na platformie interesujące, użyj jednej z następujących repozytoriów próbki:

1. [.NET przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS przykładowej aplikacji]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Platforma mongoose przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

W zależności od platformy, używane należy zainstalować wymagane pakiety i uruchom aplikację. Aby zainstalować zależności, postępuj zgodnie z plikiem README dołączonym w przykładowym repozytorium aplikacji. Na przykład w środowisku NodeJS przykładowej aplikacji, użyj następujących poleceń Zainstaluj wymagane pakiety i uruchomić aplikację.

```bash
cd mean
npm install
node index.js
```
Aplikacja próbuje połączyć się ze źródłem bazy danych MongoDB i kończy się niepowodzeniem, ponieważ parametry połączenia są nieprawidłowe. Postępuj zgodnie z instrukcjami w pliku README, aby zaktualizować parametry połączenia `url`. Ponadto aktualizacja `readFromRegion` odczytu w regionie w ramach konta usługi Azure Cosmos DB. Poniższe instrukcje dotyczą z przykładu NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

Po wykonaniu tych kroków, przykładowa aplikacja działa i generuje następujące wyniki:

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

## <a name="read-using-read-preference-mode"></a>Odczytane w trybie preferencji odczytu

Bazy danych MongoDB oferuje następujące tryby preferencji odczytu, aby klienci mogli używać:

1. PODSTAWOWY
2. PRIMARY_PREFERRED
3. POMOCNICZY
4. SECONDARY_PREFERRED
5. NAJBLIŻSZEJ

Zobacz szczegółowe [zachowanie preferencji odczytu bazy danych MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) dokumentacji szczegółowe informacje na temat zachowania każdy z nich odczytać tryby preferencji. W usłudze Azure Cosmos DB podstawowy mapowania region zapisu i pomocniczej mapy do regionu odczytu.

Oparte na typowych scenariuszy, zaleca się, używając następujących ustawień:

1. Jeśli **odczytuje małe opóźnienia** są wymagane, użyj **NEAREST** trybu preferencji odczytu. To ustawienie określa, że operacji odczytu do najbliższego dostępnego regionu. Należy pamiętać, że jeśli znajduje się najbliższy region to region zapisu, następnie te operacje są kierowane do tego regionu.
2. Jeśli **wysokiej dostępności i geograficznej dystrybucję odczyty** są wymagane (opóźnienie nie jest ograniczeniem), następnie za pomocą **preferowane pomocnicze** trybu preferencji odczytu. To ustawienie określa, że operacji odczytu do dostępnego regionu odczytu. Jeśli region odczytu, nie jest dostępny, żądania są kierowane do regionu zapisu.

Poniższy fragment kodu z przykładowej aplikacji przedstawia sposób konfigurowania preferencji odczytu NAJBLIŻSZEJ w środowisku NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobnie poniższy fragment kodu przedstawia sposób konfigurowania preferencji odczytu SECONDARY_PREFERRED w środowisku NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Można również ustawić preferencji odczytu, przekazując `readPreference` jako parametr w opcji identyfikatora URI ciąg połączenia:

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

Odnoszą się do odpowiednich repozytoriów aplikacji przykładowej w przypadku innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Przeczytaj, za pomocą tagów

Oprócz tryb preferencji odczytu bazy danych MongoDB umożliwia korzystanie z tagów w celu przekierowania operacji odczytu. W usłudze Azure Cosmos DB dla interfejsu API usługi MongoDB `region` tag jest domyślnie jako część `isMaster` odpowiedzi:

```json
"tags": {
         "region": "West US"
      }
```

W związku z tym, można użyć klucza MongoClient `region` tag wraz z nazwą regionu w celu przekierowania operacji odczytu do określonych regionów. Dla kont usługi Azure Cosmos DB można znaleźć w witrynie Azure portal po lewej stronie w obszarze nazw regionów **ustawienia globalnie -> dane repliki**. To ustawienie jest przydatne w przypadku osiągnięcia **odczytu izolacji** -przypadki, w którym aplikacja kliencka chcesz kierować operacji odczytu tylko w określonym regionie. To ustawienie jest idealny dla innych do produkcji i analizowanie wpisz scenariuszy, które zostały uruchomione w tle i nie są krytyczne usług produkcyjnych.

Poniższy fragment kodu z przykładowej aplikacji przedstawia sposób konfigurowania preferencji odczytu przy użyciu tagów w środowisku NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Odnoszą się do odpowiednich repozytoriów aplikacji przykładowej w przypadku innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

W tym artykule wyjaśniono sposób globalnie Dystrybuuj operacji odczytu, korzystanie z preferencji odczytu z interfejsem API MongoDB usługi Azure Cosmos DB.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, należy usunąć wszystkie zasoby utworzone w tym artykule w witrynie Azure portal wykonując następujące kroki:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

* [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
* [Konfigurowanie globalnie replikowanego konta usługi Azure Cosmos DB i używanie go z interfejsem API MongoDB](tutorial-global-distribution-mongodb.md)
* [Programowanie lokalnie za pomocą emulatora](local-emulator.md)
