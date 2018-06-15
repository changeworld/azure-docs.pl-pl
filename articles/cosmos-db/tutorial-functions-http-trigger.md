---
title: Tworzenie wyzwalacza HTTP przy użyciu powiązania danych wejściowych usługi Azure Cosmos DB
description: Dowiedz się, jak używać usługi Azure Functions z wyzwalaczami HTTP do tworzenia zapytań względem usługi Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 3ea102d56df1e47fd1d1c35bd23a3e987fa45145
ms.sourcegitcommit: 00e875607372517b4b93ca4b6baa915cbbad8064
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34818903"
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Tworzenie wyzwalacza HTTP usługi Azure Functions przy użyciu powiązania danych wejściowych usługi Azure Cosmos DB

Azure Cosmos DB to globalnie dystrybuowana, wielomodelowa baza danych, która nie korzysta ze schematów ani serwerów. Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie. Połączenie tych dwóch usług platformy Azure zapewnia podstawę dla architektury bezserwerowej, która pozwala skupić się na tworzeniu atrakcyjnych aplikacji, nie zajmując się aprowizacją i konserwacją serwerów związaną z potrzebami dotyczącymi obliczeń i bazy danych.

Ten samouczek opiera się na kodzie utworzonym w temacie [Przewodnik Szybki start dotyczący interfejsu API dla platformy .NET](create-graph-dotnet.md). W tym samouczku opisano sposób dodawania funkcji platformy Azure, która zawiera [wyzwalacz HTTP](../azure-functions/functions-bindings-http-webhook.md). Wyzwalacz HTTP używa [powiązania danych wejściowych](../azure-functions/functions-triggers-bindings.md) usługi Azure Cosmos DB do pobierania danych z bazy danych programu Graph utworzonej w ramach tego przewodnika Szybki start. Ten konkretny wyzwalacz HTTP służy do wykonywania zapytań względem usługi Azure Cosmos DB dotyczących danych, ale powiązań danych wejściowych z bazy danych usługi Azure Cosmos DB można użyć do pobierania wartości danych wejściowych, gdy jest to wymagane przez funkcję.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie projektu funkcji platformy Azure 
> * Tworzenie wyzwalacza HTTP
> * Publikowanie funkcji platformy Azure
> * Łączenie funkcji platformy Azure z bazą danych Azure Cosmos DB

## <a name="prerequisites"></a>Wymagania wstępne

- [Program Visual Studio 2017 w wersji 15.3](https://www.visualstudio.com/vs/preview/) zawierający obciążenie **Programowanie na platformie Azure**.

    ![Instalowanie programu Visual Studio 2017 z obciążeniem Programowanie na platformie Azure](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- Po zainstalowaniu programu Visual Studio 2017 w wersji 15.3 lub przeprowadzeniu uaktualnienia do tej wersji, należy ręcznie zaktualizować narzędzia programu Visual Studio 2017 dla usługi Azure Functions. Narzędzia można zaktualizować z poziomu menu **Narzędzia** w obszarze **Rozszerzenia i aktualizacje...** > **Aktualizacje** > **Visual Studio Marketplace** > **Usługa Azure Functions i narzędzia WebJob** > **Aktualizuj**.

- Wykonaj instrukcje zawarte w samouczku [Tworzenie aplikacji .NET za pomocą interfejsu API programu Graph](tutorial-develop-graph-dotnet.md) lub pobierz przykładowy kod z repozytorium GitHub [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started), a następnie skompiluj projekt.
 
## <a name="build-a-function-in-visual-studio"></a>Tworzenie funkcji w programie Visual Studio

1. Dodaj projekt **Azure Functions** do rozwiązania, klikając prawym przyciskiem myszy węzeł rozwiązania w **Eksploratorze rozwiązań**, a następnie wybierz pozycję **Dodaj** > **Nowy projekt**. Wybierz projekt **Azure Functions** w oknie dialogowym i nazwij go **PeopleDataFunctions**.

   ![Dodawanie projektu funkcji platformy Azure do rozwiązania](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. Po utworzenia projektu usługi Azure Functions należy wykonać kilka aktualizacji i instalacji związanych z narzędziem NuGet. 

    a. Aby upewnić się, że zainstalowano najnowszy zestaw SDK usługi Functions, użyj menedżera pakietów NuGet, aby zaktualizować pakiet **Microsoft.NET.Sdk.Functions**. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**. Na karcie **Zainstalowane** wybierz pakiet Microsoft.NET.Sdk.Functions, a następnie kliknij pozycję **Aktualizuj**.

   ![Aktualizowanie pakietów NuGet](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. Na karcie **Przeglądaj** wprowadź wartość **azure.graphs**, aby znaleźć pakiet **Microsoft.Azure.Graphs**, a następnie kliknij pozycję **Zainstaluj**. Ten pakiet zawiera zestaw SDK klienta interfejsu API programu Graph.

   ![Instalowanie interfejsu API programu Graph](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    d. Na karcie **Przeglądaj** wprowadź wartość **mono.csharp**, aby znaleźć pakiet **Mono.CSharp**, a następnie kliknij pozycję **Zainstaluj**.

   ![Instalowanie pakietu Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. W Eksploratorze rozwiązań powinny znajdować się teraz zainstalowane pakiety, jak pokazano poniżej. 
   
   Następną czynnością, którą należy wykonać, jest napisanie kodu, więc do projektu zostanie dodany nowy element **Funkcja platformy Azure**. 

    a. Kliknij prawym przyciskiem myszy węzeł projektu w **Eksploratorze rozwiązań**, a następnie wybierz pozycję **Dodaj** > **Nowy element**.   
    b. W oknie dialogowym **Dodawanie nowego elementu** wybierz pozycję **Elementy Visual C#**, wybierz pozycję **Funkcja platformy Azure**, wpisz wartość **Search** jako nazwę projektu, a następnie Kliknij pozycję **Dodaj**.  
 
   ![Tworzenie nowej funkcji o nazwie Search](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Funkcja platformy Azure będzie odpowiadać na żądania HTTP, więc szablon wyzwalacza HTTP jest tutaj odpowiedni.
   
   W oknie **Nowa funkcja platformy Azure** wybierz pozycję **Wyzwalacz HTTP**. Ta funkcja platformy Azure ma być także „w pełni otwarta”, więc opcję **Prawa dostępu** należy ustawić na wartość **Anonimowe**, co pozwoli uzyskać dostęp wszystkim użytkownikom. Kliknij przycisk **OK**.

   ![Ustawianie praw dostępu na anonimowe](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. Po dodaniu pliku Search.cs do projektu funkcji platformy Azure skopiuj poniższe instrukcje **using** nad istniejącymi instrukcjami using:

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. Następnie zastąp kod klasy funkcji platformy Azure poniższym kodem. Ten kod służy do przeszukiwania bazy danych Azure Cosmos DB przy użyciu interfejsu API programu Graph dla wszystkich użytkowników lub dla określonej osoby identyfikowanej za pomocą parametru ciągu zapytania `name`.

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   Kod to w zasadzie taka sama logika połączenia, jak w przypadku oryginalnej aplikacji konsolowej służącej do inicjowania bazy danych, z dodatkiem prostego zapytania do pobierania zgodnych rekordów.

## <a name="debug-the-azure-function-locally"></a>Lokalne debugowanie funkcji platformy Azure

Teraz, gdy kod jest gotowy, możesz użyć lokalnych narzędzi do debugowania funkcji platformy Azure oraz emulatora w celu uruchomienia kodu lokalnie i jego przetestowania.

1. Aby kod działał prawidłowo, należy najpierw skonfigurować go pod kątem lokalnego wykonania za pomocą informacji dotyczących połączenia z usługą Azure Cosmos DB. Aby skonfigurować funkcję platformy Azure na potrzeby lokalnych wykonań możesz użyć pliku local.settings.json w podobny sposób, co pliku App.config do skonfigurowania oryginalnej aplikacji konsolowej na potrzeby wykonania.

    Aby to zrobić, dodaj następujące wiersze kodu do pliku local.settings.json, a następnie skopiuj właściwości Endpoint i AuthKey z pliku App.Config w projekcie GraphGetStarted, jak pokazano na poniższym obrazie.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![Ustawianie punktu końcowego i klucza autoryzacji w pliku local.settings.json](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Zmień projekt StartUp na nową aplikację usługi Functions. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt **PeopleDataFunctions**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

3. W **Eksploratora rozwiązań** kliknij prawym przyciskiem myszy pozycję **Zależności** w projekcie **PeopleDataFunctions**, a następnie kliknij pozycję **Dodaj odwołanie**. Z listy wybierz pozycję System.Configuration, a następnie kliknij przycisk **OK**.

3. Teraz uruchommy aplikację. Naciśnij klawisz F5, aby uruchomić lokalne narzędzie do debugowania, func.exe, z hostowanym i gotowym do użycia kodem funkcji platformy Azure.

   Na końcu wstępnych danych wyjściowych z narzędzia func.exe widoczne jest, że funkcja platformy Azure jest hostowana pod adresem localhost:7071. Jest to przydatne pod kątem testowania tej funkcji w ramach klienta.

   ![Testowanie klienta](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. Aby przetestować funkcję platformy Azure, użyj programu [Visual Studio Code](http://code.visualstudio.com/) z rozszerzeniem [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client), którego autorem jest Huachao Mao. Rozszerzenie REST Client umożliwia lokalne lub zdalne wykonywanie żądań HTTP za pomocą jednego kliknięcia prawym przyciskiem myszy. 

    Aby to zrobić, utwórz nowy plik o nazwie test-function-locally.http, a następnie dodaj następujący kod:

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Teraz kliknij prawym przyciskiem myszy pierwszy wiersz kodu, a następnie wybierz pozycję **Wyślij żądanie**, jak pokazano na poniższym obrazie.

   ![Wysyłanie żądania REST z programu Visual Studio Code](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   Zostanie wyświetlona nieprzetworzona odpowiedź HTTP z nagłówków lokalnie uruchomionej funkcji platformy Azure, treść JSON i pozostałe elementy.

   ![Odpowiedź REST](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Teraz wybierz drugi wiersz kodu, a następnie wybierz pozycję **Wyślij żądanie**. Dzięki dodaniu parametru ciągu zapytania `name` z wartością, która na pewno istnieje w bazie danych, można odfiltrować wyniki zwracane przez funkcję platformy Azure.

   ![Filtrowanie wyników funkcji platformy Azure](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

Jeśli po zweryfikowaniu funkcja platformy Azure wydaje się działać prawidłowo, ostatnim krokiem jest opublikowanie jej w usłudze Azure App Service i skonfigurowanie pod kątem uruchamiania w chmurze.

## <a name="publish-the-azure-function"></a>Publikowanie funkcji platformy Azure

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Publikuj**.

   ![Publikowanie nowego projektu](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Teraz możemy opublikować tę funkcję w chmurze w celu przetestowania jej w ramach publicznie dostępnego scenariusza. Na karcie **Publikowanie** wybierz pozycję **Aplikacja funkcji platformy Azure**, wybierz pozycję **Utwórz nową**, aby utworzyć funkcję platformy Azure w ramach subskrypcji platformy Azure, a następnie kliknij pozycję **Publikuj**.

   ![Tworzenie nowej aplikacji funkcji platformy Azure](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. W oknie dialogowym **Publikowanie** wykonaj następujące czynności:
   
    a. W polu **Nazwa aplikacji** wpisz unikatową nazwę funkcji.

    b. W polu **Subskrypcja** wybierz subskrypcję platformy Azure, która ma być używana.
   
    d. W polu **Grupa zasobów** utwórz nową grupę zasobów i użyj takiej samej nazwy, co nazwa aplikacji.
   
    d. W przypadku pola **Plan usługi App Service** kliknij przycisk **Nowy**, aby utworzyć nowy plan usługi App Service na podstawie użycia, ponieważ planowane jest korzystanie z metody rozliczeń polegającej na płatności za użycie dla bezserwerowej funkcji platformy Azure. Na stronie **Konfigurowanie planu usługi App Service** użyj ustawień domyślnych, a następnie kliknij przycisk **OK**.
   
    e. W przypadku pola **Konto magazynu** również kliknij przycisk **Nowe**, aby utworzyć nowe konto magazynu, które będzie używane z funkcją platformy Azure, gdy wystąpi potrzeba obsługi obiektów blob, tabel lub kolejek w celu wyzwalania wykonania innych funkcji. Na stronie **Konto magazynu** użyj ustawień domyślnych, a następnie kliknij przycisk **OK**.

    f. Następnie w oknie dialogowym kliknij przycisk **Utwórz**, aby utworzyć wszystkie zasoby w ramach subskrypcji platformy Azure. W programie Visual Studio pobierany jest profil publikowania (prosty plik XML), który zostanie użyty po następnym opublikowaniu kodu funkcji platformy Azure.

   ![Tworzenie konta magazynu](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    W programie Visual Studio zostanie wyświetlona strona Publikowanie, której można użyć w przypadku wprowadzania zmian w funkcji i konieczności ich ponownego opublikowania. Na tej stronie nie trzeba teraz podejmować żadnych działań.

4. Po opublikowaniu funkcji platformy Azure możesz przejść do strony [Azure Portal](https://portal.azure.com/) dla funkcji platformy Azure. Będzie na niej wyświetlony link do **ustawień aplikacji** funkcji platformy Azure. Otwórz ten link, aby skonfigurować funkcję platformy Azure pod kątem łączności z bazą danych Cosmos DB za pomocą swoich danych osobowych.

   ![Przeglądanie ustawień aplikacji](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Podobnie jak wcześniej w przypadku pliku App.config aplikacji konsolowej i pliku local.settings.json aplikacji funkcji platformy Azure, należy dodać właściwości Endpoint i AuthKey do bazy danych Azure Cosmos DB w opublikowanej funkcji. Dzięki temu nie będzie w ogóle konieczne sprawdzanie kodu konfiguracji zawierającego klucze — można skonfigurować je w portalu i być pewnym, że nie są one przechowywane w ramach kontroli kodu źródłowego. Aby dodać te wartości, kliknij przycisk **Dodaj nowe ustawienie**, dodaj właściwość **Endpoint** i wartość z pliku app.config, następnie ponownie kliknij przycisk **Dodaj nowe ustawienie** i dodaj właściwość **AuthKey**  oraz wartość niestandardową. Po dodaniu i zapisaniu tych wartości ustawienia powinny wyglądać, tak jak na poniższym obrazie.

   ![Konfigurowanie właściwości Endpoint i AuthKey](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. Gdy funkcja platformy Azure jest prawidłowo skonfigurowana w ramach subskrypcji platformy Azure, możesz ponownie użyć rozszerzenia REST Client programu Visual Studio Code w celu wykonania zapytania względem publicznie dostępnego adresu URL funkcji platformy Azure. Dodaj następujące dwa wiersze kodu do pliku test-function-locally.http, a następnie uruchom każdy z tych wierszy, aby przetestować funkcję. Zamień nazwę funkcji w adresie URL na nazwę używanej funkcji.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    Funkcja odpowiada za pomocą danych pobranych z usługi Azure Cosmos DB.

    ![Używanie rozszerzenia REST Client do tworzenia zapytań względem funkcji platformy Azure](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Utworzono projekt funkcji platformy Azure 
> * Utworzono wyzwalacz HTTP
> * Opublikowano funkcję platformy Azure
> * Połączono funkcję z bazą danych Azure Cosmos DB

Teraz możesz przejść do sekcji Pojęcia, aby uzyskać więcej informacji na temat usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Dystrybucja globalna](distribute-data-globally.md) 

Ten artykuł jest oparty na cyklu wpisów w blogu [Schemaless & Serverless Brady’ego Gastera](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless). Odwiedź tego bloga, aby uzyskać dodatkowe wpisy z tego cyklu.
