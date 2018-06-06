---
title: Przy użyciu preferencji odczytu bazy danych MongoDB w usłudze Azure rozwiązania Cosmos bazy danych MongoDB interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB preferencji odczytu bazy danych MongoDB
services: cosmos-db
author: vidhoonv
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: ''
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: viviswan
ms.openlocfilehash: f8c8d068a188052b5e8b66ccb4486a0fc75e2af9
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796669"
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Odczytuje sposobu dystrybucji globalnie Preferencje odczytu z interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB 

W tym artykule przedstawiono sposób globalnie rozpowszechniać za pomocą operacji odczytu [preferencji odczytu bazy danych MongoDB](https://docs.mongodb.com/manual/core/read-preference/) ustawień za pomocą interfejsu API programu Azure rozwiązania Cosmos DB bazy danych MongoDB. 

## <a name="prerequisites"></a>Wymagania wstępne 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Odwołuje się do tego [szybkiego startu](tutorial-global-distribution-mongodb.md) artykuł, aby uzyskać instrukcje dotyczące przy użyciu portalu Azure, aby skonfigurować konto bazy danych Azure rozwiązania Cosmos z globalnego dystrybucji, a następnie nawiąż połączenie przy użyciu interfejsu API bazy danych MongoDB.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.  

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium. W oparciu o platformy zainteresowań, użyj jednej z następujących repozytoria próbki:

1. [.NET przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Aplikacja przykładowa NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot przykładowej aplikacji](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

W zależności od platformy używać Zainstaluj wymagane pakiety i uruchom aplikację. Aby zainstalować zależności, wykonaj README zawarte w repozytorium przykładowej aplikacji. Na przykład w środowisku NodeJS przykładowej aplikacji, użyj następujących poleceń w celu zainstalowania wymaganych pakietów i uruchom aplikację.

```bash
cd mean
npm install
node index.js
```
Aplikacja próbuje połączyć się ze źródłem danych MongoDB i kończy się niepowodzeniem, ponieważ ciąg połączenia jest nieprawidłowy. Postępuj zgodnie z instrukcjami README, aby zaktualizować parametry połączenia `url`. Ponadto zaktualizuj `readFromRegion` w regionie odczytu konta bazy danych Azure rozwiązania Cosmos. Poniższe instrukcje dotyczą z próbki NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

Po wykonaniu tych kroków, przykładowej aplikacji działa i następujących danych wyjściowych:

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

## <a name="read-using-read-preference-mode"></a>Odczytane w trybie odczytu preferencji

Bazy danych MongoDB oferuje następujące tryby preferencji odczytu, aby klienci mogli używać:

1. GŁÓWNY
2. PRIMARY_PREFERRED
3. POMOCNICZY
4. SECONDARY_PREFERRED
5. NAJBLIŻSZEJ

Zobacz szczegółowe [zachowanie preferencji odczytu bazy danych MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) dokumentacji szczegółowe informacje dotyczące zachowania każdego z tych trybów preferencji do odczytu. W usłudze Azure DB rozwiązania Cosmos podstawowy mapuje regionu zapisu i dodatkowej mapy do regionu odczytu.

W oparciu o typowych scenariuszy, zaleca się przy użyciu następujących ustawień:

1. Jeśli **odczytuje małych opóźnieniach** są wymagane, użyj **NEAREST** preferencji trybu odczytu. To ustawienie określa, że operacje odczytu do najbliższej dostępnej region. Należy pamiętać, że jeśli region najbliższy region zapisu, następnie te operacje są kierowane do tego regionu.
2. Jeśli **wysokiej dostępności i geograficzna dystrybucji odczytów** są wymagane (opóźnienia nie jest ograniczenie), następnie użyć **dodatkowej preferowane** preferencji trybu odczytu. To ustawienie określa, że operacje odczytu dostępne regionu odczytu. Jeśli nie jest dostępny żaden region odczytu, żądania są przekierowywane do regionu zapisu.

Poniższy fragment kodu z przykładowej aplikacji przedstawiono sposób konfigurowania NAJBLIŻSZEJ preferencji odczytu w środowisku NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobnie poniższy fragment przedstawiono sposób konfigurowania preferencji odczytu SECONDARY_PREFERRED w środowisku NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Dotyczą odpowiednich repozytoriów aplikacji przykładowej dla innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Przy użyciu tagów do odczytu

Oprócz trybu preferencji odczytu bazy danych MongoDB umożliwia korzystanie z znaczniki, aby przekierować operacji odczytu. W usłudze Azure DB rozwiązania Cosmos dla interfejsu API bazy danych MongoDB `region` tag jest dołączany domyślnie jako część `isMaster` odpowiedzi:

```json
"tags": {
         "region": "West US"
      }
```

W związku z tym można użyć MongoClient `region` tagu wraz z nazwa regionu przekierować operacji odczytu dla określonych regionów. Dla bazy danych Azure rozwiązania Cosmos kont, można znaleźć w portalu Azure po lewej stronie w obszarze nazwy regionów **ustawienia globalnie -> dane repliki**. To ustawienie jest przydatne w przypadku osiągnięcia **odczytu izolacji** -przypadki, w których aplikacja kliencka chcesz bezpośrednie operacji odczytu tylko w określonym regionie. To ustawienie jest idealny dla innych niż do produkcji/analytics wpisz scenariuszy, które zostały uruchomione w tle i nie są usług krytycznych produkcji.

Poniższy fragment kodu z przykładowej aplikacji przedstawiono sposób konfigurowania preferencji odczytu tagów w środowisku NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Dotyczą odpowiednich repozytoriów aplikacji przykładowej dla innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

W tym artykule kiedy znasz już sposobu globalnie dystrybucji przy użyciu preferencji odczytu w usłudze Azure rozwiązania Cosmos DB bazy danych MongoDB API operacji odczytu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, należy usunąć wszystkie zasoby utworzone przez ten artykuł w portalu Azure następujące czynności:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

* [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
* [Skonfigurować konto bazy danych Azure rozwiązania Cosmos globalnie replikowanych i używać go z interfejsem API bazy danych MongoDB](tutorial-global-distribution-mongodb.md)
* [Programowanie lokalnie za pomocą emulatora](local-emulator.md)
