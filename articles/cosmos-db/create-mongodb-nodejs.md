---
title: Łączenie aplikacji MongoDB na platformie Node.js z usługą Azure Cosmos DB
description: W tym przewodniku Szybki start pokazano, jak połączyć istniejącą aplikację MongoDB utworzoną na platformie Node.js z usługą Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.openlocfilehash: 0ff15c29fb7d71919b54cbcd73ead30a8673e0f7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543076"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Szybki start: Migracja istniejącej aplikacji internetowej MongoDB na platformie Node.js do usługi Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów. 

Ten przewodnik Szybki start wyjaśnia, jak skorzystać z istniejącej aplikacji MongoDB utworzonej na platformie Node.js i połączyć ją z bazą danych usługi Cosmos obsługującą klienta MongoDB. W takiej sytuacji dla aplikacji nie jest widoczne, że dane są przechowywane w bazie danych usługi Cosmos.

Gdy wszystko będzie gotowe, aplikacja MEAN (MongoDB, Express, Angular i Node.js) będzie uruchomiona dla bazy danych [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Aplikacja MEAN.js uruchomiona w usłudze Azure App Service](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Wymagania wstępne 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Oprócz interfejsu wiersza polecenia platformy Azure należy mieć lokalnie zainstalowane środowisko [Node.js](https://nodejs.org/) i usługę [Git](https://www.git-scm.com/downloads) do uruchamiania `npm` i poleceń `git`.

Niezbędna jest praktyczna wiedza na temat Node.js. Ten przewodnik Szybki start nie służy do wyjaśnienia ogólnych zasad tworzenia aplikacji w języku Node.js.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium. To przykładowe repozytorium zawiera domyślną aplikację [MEAN.js](https://meanjs.org/).

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminalu usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Zainstaluj wymagane pakiety i uruchom aplikację.

```bash
cd mean
npm install
npm start
```
Aplikacja podejmie próbę połączenia się ze źródłem bazy danych MongoDB, która zakończy się niepowodzeniem. Zamknij aplikację w przypadku zwrócenia przez dane wyjściowe błędu „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Jeśli używasz zainstalowanego interfejsu wiersza polecenia platformy Azure, zaloguj się do subskrypcji platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Ten krok możesz pominąć, jeśli używasz powłoki Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Dodawanie modułu Azure Cosmos DB

Jeśli używasz zainstalowanego interfejsu wiersza polecenia platformy Azure, sprawdź, czy składnik `cosmosdb` jest już zainstalowany, uruchamiając polecenie `az`. Jeśli `cosmosdb` znajduje się na liście podstawowych poleceń, przejdź do następnego polecenia. Ten krok możesz pominąć, jeśli używasz powłoki Azure Cloud Shell.

Jeśli `cosmosdb` nie znajduje się na liście podstawowych poleceń, zainstaluj ponownie [Interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure, takich jak aplikacje internetowe, bazy danych i konta magazynu, oraz zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów w regionie Europa Zachodnia. Wybierz unikatową nazwę grupy zasobów.

Jeśli używasz Azure Cloud Shell, wybierz opcję **Wypróbuj**, postępuj zgodnie z monitami Onscreen, aby zalogować się, a następnie skopiuj polecenie do wiersza polecenia.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórz konto usługi Cosmos za pomocą polecenia [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

W poniższym poleceniu w miejsce symbolu zastępczego `<cosmosdb-name>` wstaw swoją unikatową nazwę konta usługi Cosmos. Ta nazwa będzie służyć jako część Twojego punktu końcowego usługi Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), dlatego musi być unikatowa w obrębie wszystkich kont usługi Cosmos na platformie Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr `--kind MongoDB` umożliwia tworzenie połączeń klienckich MongoDB.

Po utworzeniu konta usługi Azure Cosmos DB w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących. 

> [!NOTE]
> W tym przykładzie format JSON jest używany jako format wyjściowy interfejsu wiersza polecenia platformy Azure. Jest to ustawienie domyślne. Aby użyć innego formatu wyjściowego, zobacz [Formaty danych wyjściowych dla poleceń interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Połączenie aplikacji Node.js z bazą danych

W tym kroku połączysz swoją przykładową aplikację MEAN.js z nowo utworzoną bazą danych Cosmos. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurowanie parametrów połączenia w aplikacji Node.js

W repozytorium MEAN.js otwórz plik `config/env/local-development.js`.

Zastąp zawartość tego pliku następującym kodem. Należy również zastąpić dwa symbole zastępcze `<cosmosdb-name>` nazwą konta usługi Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Pobieranie klucza

Aby połączyć się z bazą danych usługi Cosmos, niezbędny jest klucz bazy danych. Aby pobrać klucz podstawowy, użyj polecenia [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys).

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następującego przykładu. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Skopiuj wartość `primaryMasterKey`. Wklej ją zamiast `<primary_master_key>` w `local-development.js`.

Zapisz zmiany.

### <a name="run-the-application-again"></a>Uruchom ponownie aplikację.

Uruchom ponownie polecenie `npm start`. 

```bash
npm start
```

Komunikat na konsoli powinien stwierdzać, że środowisko programistyczne jest uruchomione i gotowe do pracy. 

Przejdź do `http://localhost:3000` programu w przeglądarce. Wybierz pozycję Utwórz **konto** w górnym menu i spróbuj utworzyć dwóch fikcyjnych użytkowników. 

Przykładowa aplikacja MEAN.js przechowuje dane użytkowników w bazie danych. Jeśli wszystko przebiega poprawnie i aplikacja MEAN.js automatycznie zaloguje się do utworzonego użytkownika, oznacza to, że połączenie z usługą Azure Cosmos DB działa. 

![Pomyślne połączenie MEAN.js z MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Wyświetlanie danych w Eksploratorze danych

Dane przechowywane w bazie danych usługi Cosmos są dostępne do wyświetlania i wykonywania zapytań w witrynie Azure Portal.

Aby wyświetlać dane użytkownika utworzone w poprzednim kroku, a także pracować z nimi i wykonywać na nich zapytania, zaloguj się do witryny [Azure Portal](https://portal.azure.com) w przeglądarce sieci Web.

W górnym polu wyszukiwania wprowadź **Azure Cosmos DB**. Po otwarciu bloku konta usługi Cosmos wybierz swoje konto usługi Cosmos. Na lewym pasku nawigacyjnym wybierz pozycję **Eksplorator danych**. Rozwiń kolekcję w okienku Kolekcje. Następnie możesz wyświetlić dokumenty w kolekcji, wysłać zapytanie dotyczące danych, a nawet tworzyć i uruchamiać procedury składowane, wyzwalacze i funkcje definiowane przez użytkownika (UDF). 

![Eksplorator danych w witrynie Azure Portal](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Wdrażanie aplikacji Node.js na platformie Azure

W tym kroku należy wdrożyć aplikację w języku Node.js w usłudze Cosmos DB.

Można było zauważyć, że plik konfiguracji, który został zmodyfikowany wcześniej, jest przeznaczony dla środowiska programistycznego (`/config/env/local-development.js`). Po wdrożeniu aplikacji w usłudze App Service domyślnie będzie ona uruchamiana w środowisku produkcyjnym. Teraz musisz wprowadzić tę samą zmianę w odpowiednim pliku konfiguracji.

W repozytorium MEAN.js otwórz plik `config/env/production.js`.

W obiekcie `db` zastąp wartość `uri` tak jak pokazano w poniższym przykładzie. Pamiętaj, aby zamienić symbole zastępcze tak jak poprzednio.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Opcja `ssl=true` jest ważna, ponieważ [usługa Cosmos DB wymaga protokołu SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

Na terminalu zatwierdź wszystkie zmiany w środowisku Git. Możesz skopiować oba polecenia, aby uruchomić je razem.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Cosmos, tworzenia kolekcji i uruchamiania aplikacji konsolowej. Teraz możesz zaimportować dodatkowe dane do swojej bazy danych usługi Cosmos. 

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
