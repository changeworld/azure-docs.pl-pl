---
title: Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB za pomocą narzędzia .Net V4 SDK
description: Szybki start do tworzenia aplikacji konsoli przy użyciu .Net V4 SDK do zarządzania zasobami konta interfejsu API SQL usługi Azure Cosmos DB.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77585938"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Szybki start: Tworzenie aplikacji konsoli przy użyciu pliku .Net V4 SDK do zarządzania zasobami konta interfejsu API usługi Azure Cosmos DB SQL.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Wprowadzenie do biblioteki klienta interfejsu API sql usługi Azure Cosmos DB dla platformy .NET. Wykonaj kroki opisane w tym doc, aby zainstalować pakiet .NET V4 (Azure.Cosmos), utworzyć aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w usłudze Azure Cosmos DB. 

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Usługi Azure Cosmos DB można używać do szybkiego tworzenia i wykonywania zapytań o bazy danych klucz/wartość, dokumenty i wykresy. Użyj biblioteki klienta interfejsu SQL usługi Azure Cosmos DB dla platformy .NET, aby:

* Tworzenie bazy danych usługi Azure Cosmos i kontenera
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[Pakiet kodu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | źródłowego biblioteki[(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz ją bezpłatnie](https://azure.microsoft.com/free/) lub wypróbuj [usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) bez bezpłatnej subskrypcji platformy Azure i zobowiązań. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Możesz sprawdzić, która wersja jest dostępna `dotnet --version`w twoim środowisku, uruchamiając program .

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji można przejść przez tworzenie konta usługi Azure Cosmos i konfigurowanie projektu, który używa biblioteki klienta interfejsu API SQL usługi Azure Cosmos DB dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym `FamilyDatabase` artykule tworzy bazę danych i członków rodziny (każdy członek rodziny jest elementem) w tej bazie danych. Każdy członek rodziny ma `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`właściwości, takie jak . Właściwość `LastName` jest używana jako klucz partycji dla kontenera. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Tworzenie konta usługi Azure Cosmos

Jeśli używasz [opcji Wypróbuj usługę Azure Cosmos DB dla bezpłatnej](https://azure.microsoft.com/try/cosmosdb/) opcji tworzenia konta usługi Azure Cosmos, musisz utworzyć konto usługi Azure Cosmos DB typu SQL **API**. Konto testowe usługi Azure Cosmos DB jest już utworzone dla Ciebie. Nie musisz jawnie tworzyć konta, więc możesz pominąć tę sekcję i przejść do następnej sekcji.

Jeśli masz własną subskrypcję platformy Azure lub utworzyłeś subskrypcję za darmo, należy utworzyć konto usługi Azure Cosmos jawnie. Poniższy kod utworzy konto usługi Azure Cosmos z spójnością sesji. Konto jest replikowane `South Central US` `North Central US`w i .  

Za pomocą usługi Azure Cloud Shell można utworzyć konto usługi Azure Cosmos. Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. W przypadku tego szybkiego startu wybierz tryb **Bash.** Usługa Azure Cloud Shell wymaga również konta magazynu, można je utworzyć po wyświetleniu monitu.

Wybierz przycisk **Wypróbuj** obok następującego kodu, wybierz tryb **Bash,** wybierz **pozycję utwórz konto magazynu** i zaloguj się do usługi Cloud Shell. Następnie skopiuj i wklej następujący kod do powłoki chmury platformy Azure i uruchom go. Nazwa konta usługi Azure Cosmos musi być unikatowa globalnie, upewnij się, że `mysqlapicosmosdb` aktualizuje wartość przed uruchomieniem polecenia.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Utworzenie konta usługi Azure Cosmos zajmuje trochę czasu, gdy operacja zakończy się pomyślnie, można zobaczyć dane wyjściowe potwierdzenia. Po pomyślnym zakończeniu polecenia zaloguj się do [witryny Azure portal](https://portal.azure.com/) i sprawdź, czy istnieje konto usługi Azure Cosmos o określonej nazwie. Okno usługi Azure Cloud Shell można zamknąć po utworzeniu zasobu. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji .NET

Utwórz nową aplikację platformy .NET w preferowanym edytorze lub ide. Otwórz wiersz polecenia systemu Windows lub okno terminala na komputerze lokalnym. Wszystkie polecenia w następnych sekcjach zostaną uruchomione w wierszu polecenia lub terminalu.  Uruchom następujące polecenie dotnet new, aby utworzyć `todo`nową aplikację o nazwie . Parametr --langVersion ustawia właściwość LangVersion w utworzonym pliku projektu.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

   ```bash
   cd todo
   dotnet build
   ```

Oczekiwane dane wyjściowe z kompilacji powinny wyglądać mniej więcej tak:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalowanie pakietu usługi Azure Cosmos DB

Będąc jeszcze w katalogu aplikacji, zainstaluj bibliotekę klienta usługi Azure Cosmos DB dla platformy .NET Core przy użyciu polecenia dotnet add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń konta usługi Azure Cosmos z witryny Azure portal

Przykładowa aplikacja musi uwierzytelnić się na koncie usługi Azure Cosmos. Aby uwierzytelnić, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Przejdź do konta usługi Azure Cosmos.

1. Otwórz okienko **Klucze** i skopiuj **identyfikator URI** i **klucz podstawowy** konta. Wartości identyfikatora URI i kluczy zostaną dodane do zmiennej środowiskowej w następnym kroku.

## <a name="object-model"></a><a id="object-model"></a>Model obiektu

Przed rozpoczęciem tworzenia aplikacji przyjrzyjmy się hierarchii zasobów w usłudze Azure Cosmos DB i modelu obiektów używanego do tworzenia tych zasobów i uzyskiwania do nich dostępu. Usługa Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Kontenery 
* Items

Aby dowiedzieć się więcej na temat hierarchii różnych jednostek, zobacz [pracy z bazami danych, kontenerów i elementów w usłudze Azure Cosmos DB](databases-containers-items.md) artykułu. Do interakcji z tymi zasobami użyjesz następujących klas .NET:

* CosmosClient — ta klasa zapewnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos DB. Obiekt klienta jest używany do konfigurowania i wykonywania żądań względem usługi.
* CreateDatabaseIfNotExistsAsync — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczna. 
* CreateContainerIfNotExistsAsync — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) kontener jako operację asynchroniczna. Można sprawdzić kod stanu z odpowiedzi, aby ustalić, czy kontener został nowo utworzony (201) lub istniejący kontener został zwrócony (200). 
* CreateItemAsync — ta metoda tworzy element w kontenerze.
* UpsertItemAsync — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje lub zastępuje element, jeśli już istnieje. 
* GetItemQueryIterator — ta metoda tworzy kwerendę dla elementów w kontenerze w bazie danych usługi Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowanymi. 
* DeleteAsync — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync`metoda usuwa tylko bazę danych.

 ## <a name="code-examples"></a><a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy rodzinną bazę danych w usłudze Azure Cosmos DB. Rodzinna baza danych zawiera szczegóły rodziny, takie jak imię i nazwisko, adres, lokalizacja, skojarzone z nimi elementy rodziców, dzieci i zwierzęta domowe. Przed zapełnieniem danych do konta usługi Azure Cosmos należy zdefiniować właściwości elementu rodziny. Utwórz nową `Family.cs` klasę o nazwie na poziomie głównym przykładowej aplikacji i dodaj do niej następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie używanych dyrektyw & definiowanie obiektu klienta

W katalogu projektu otwórz `Program.cs` plik w edytorze i dodaj następujące elementy, korzystając z dyrektyw u góry aplikacji:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Dodaj następujące zmienne globalne `Program` w swojej klasie. Będą one obejmować klucze punktu końcowego i autoryzacji, nazwę bazy danych i kontener, który zostanie utworzony. Pamiętaj, aby zastąpić wartości kluczy punktu końcowego i autoryzacji zgodnie z twoim środowiskiem. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Na koniec wymień `Main` metodę:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Tworzenie bazy danych 

`CreateDatabaseAsync` Zdefiniuj `program.cs` metodę w klasie. Ta metoda `FamilyDatabase` tworzy, jeśli jeszcze nie istnieje.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Tworzenie kontenera

`CreateContainerAsync` Zdefiniuj `Program` metodę w klasie. Ta metoda `FamilyContainer` tworzy, jeśli jeszcze nie istnieje. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Tworzenie elementu

Utwórz element rodziny, `AddItemsToContainerAsync` dodając metodę z następującym kodem. Do utworzenia `CreateItemAsync` `UpsertItemAsync` elementu można użyć metody lub:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Kwerenda elementów

Po wstawieniu elementu można uruchomić kwerendę, aby uzyskać szczegółowe informacje o rodzinie "Andersen". Poniższy kod pokazuje, jak wykonać kwerendę bezpośrednio przy użyciu kwerendy SQL. Zapytanie SQL, aby uzyskać "Anderson" `SELECT * FROM c WHERE c.LastName = 'Andersen'`szczegóły rodziny jest: . `QueryItemsAsync` Zdefiniuj `Program` metodę w klasie i dodaj do niej następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Zastępowanie elementu 

Przeczytaj element rodziny, a następnie `ReplaceFamilyItemAsync` zaktualizuj go, dodając metodę z następującym kodem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Usuwanie elementu 

Usuń element rodziny, `DeleteFamilyItemAsync` dodając metodę z następującym kodem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Usuwanie bazy danych 

Na koniec można usunąć bazę danych dodając `DeleteDatabaseAndCleanupAsync` metodę z następującym kodem:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Po dodaniu wszystkich wymaganych metod `Program` zapisz plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następna kompilacja i uruchom aplikację, aby utworzyć zasoby usługi Azure Cosmos DB.

   ```bash
   dotnet run
   ```

Następujące dane wyjściowe są generowane po uruchomieniu aplikacji. Można również zalogować się do witryny Azure portal i sprawdzić, czy zasoby są tworzone:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Można sprawdzić, czy dane są tworzone przez zalogowanie się do witryny Azure portal i zobacz wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby usunąć konto usługi Azure Cosmos i odpowiednią grupę zasobów. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos, utworzyć bazę danych i kontener przy użyciu aplikacji .NET Core. Teraz można zaimportować dodatkowe dane do konta usługi Azure Cosmos z instrukcjami int poniższy artykuł. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
