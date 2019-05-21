---
title: 'Samouczek platformy ASP.NET MVC dla usługi Azure Cosmos DB: opracowywanie aplikacji internetowych'
description: Samouczek platformy ASP.NET MVC, który umożliwia utworzenie aplikacji internetowych MVC za pomocą usługi Azure Cosmos DB. Zapiszesz dane w postaci kodu JSON i uzyskasz do nich dostęp za pomocą aplikacji listy rzeczy do zrobienia hostowanej w usłudze Azure Websites — szczegółowy samouczek dla platformy ASP.NET MVC.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 15cf3b1316cc35e22538ca353302c4a82d2d418b
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979016"
---
# <a name="_Toc395809351"></a>Samouczek platformy ASP.NET MVC: Opracowywanie aplikacji internetowych w usłudze Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Aby podkreślić, jak możesz efektywnie wykorzystać usługę Azure Cosmos DB do zapisywania i odpytywania dokumentów JSON, ten artykuł przedstawia kompletny przewodnik tworzenia aplikacji listy rzeczy do zrobienia używającej usługi Azure Cosmos DB. Zadania będą przechowywane jako dokumenty JSON w usłudze Azure Cosmos DB.

![Zrzut ekranu przedstawiający listę zadań do wykonania utworzone w ramach tego samouczka - ASP platformy ASP.NET MVC, samouczek krok po kroku, aplikacja sieci web MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Ten przewodnik przedstawia, w jaki sposób należy korzystać z usługi Azure Cosmos DB do zapisywania danych i uzyskiwania do nich dostępu za pomocą aplikacji internetowej platformy ASP.NET MVC hostowanej na platformie Azure. Jeśli szukasz samouczka, który koncentruje się tylko na usłudze Azure Cosmos DB, a nie na składnikach platformy ASP.NET MVC, zobacz [Build an Azure Cosmos DB C# console application](sql-api-get-started.md) (Tworzenie aplikacji konsolowej w języku C# dla usługi Azure Cosmos DB).

> [!TIP]
> Ten samouczek zakłada, że masz już pewne doświadczenie w korzystaniu z platformy ASP.NET MVC i usługi Azure Websites. Jeśli nie znasz platformy ASP.NET lub [wstępnie wymaganych narzędzi](#_Toc395637760), zalecamy pobranie kompletnego przykładowego projektu z usługi [GitHub][GitHub] i postępowanie zgodnie z instrukcjami zawartymi w tym przykładzie. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.
> 
> 

## <a name="_Toc395637760"></a>Wymagania wstępne dotyczące tego samouczka bazy danych
Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące elementy:

* Aktywne konto platformy Azure.  Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Zestaw Azure SDK dla platformy .NET dla programu Visual Studio 2017 dostępny za pośrednictwem Instalatora programu Visual Studio.

Wszystkie zrzuty ekranu w tym artykule wykonano za pomocą programu Microsoft Visual Studio Community 2017. Jeśli w Twoim systemie jest skonfigurowana inna wersja, możliwe, że Twoje ekrany i opcje nie będą całkiem zgodne, lecz jeśli spełniasz powyższe wymagania wstępne, to rozwiązanie powinno działać.

## <a name="_Toc395637761"></a>Krok 1. Tworzenie konta bazy danych usługi Azure Cosmos DB
Zacznijmy od utworzenia konta usługi Azure Cosmos DB. Jeśli masz już konto SQL dla usługi Azure Cosmos DB lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Tworzenie nowej aplikacji platformy ASP.NET MVC](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Teraz przeprowadzimy Cię przez proces tworzenia nowej aplikacji platformy ASP.NET MVC od podstaw. 

## <a name="_Toc395637762"></a>Krok 2. Tworzenie nowej aplikacji platformy ASP.NET MVC

1. W menu **Plik** programu Visual Studio wskaż pozycję **Nowy**, a następnie kliknij pozycję **Projekt**. Zostanie wyświetlone okno dialogowe **Nowy projekt**.

2. W okienku **Typy projektów** rozwiń element **Szablony**, **Visual C#**, **Internet**, a następnie wybierz pozycję **Aplikacja internetowa ASP.NET**.

      ![Zrzut ekranu przedstawiający okno dialogowe Nowy projekt z wyróżnionym typem projektu aplikacja sieci Web ASP.NET](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. W polu **Nazwa** wpisz nazwę projektu. W tym samouczku jest używana nazwa „todo”. Jeśli wybierzesz inną nazwę, to wszędzie tam, gdzie jest wspomniana przestrzeń nazw todo, musisz dostosować podane przykłady kodu tak, aby używały nazwy Twojej aplikacji. 
4. Kliknij polecenie **Przeglądaj**, aby przejść do folderu, w którym chcesz utworzyć projekt, a następnie kliknij przycisk **OK**.
   
      Zostanie wyświetlone okno dialogowe **Nowa aplikacja internetowa platformy ASP.NET**.
   
    ![Zrzut ekranu przedstawiający okno dialogowe nowej aplikacji sieci Web platformy ASP.NET z wyróżnionym szablonem aplikacji MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. W okienku szablonów wybierz pozycję **MVC**.

6. Kliknij przycisk **OK**, aby umożliwić programowi Visual Studio przygotowanie szkieletu na podstawie pustego szablonu platformy ASP.NET MVC. 

          
7. Po zakończeniu tworzenia standardowej aplikacji MVC przez program Visual Studio będzie dostępna pusta aplikacja platformy ASP.NET, którą można uruchomić lokalnie.
   
    Pominiemy uruchamianie projektu lokalnie, ponieważ na pewno wszyscy widzieliśmy aplikację „Hello World” platformy ASP.NET. Przejdźmy prosto do dodawania usługi Azure Cosmos DB do tego projektu i tworzenia aplikacji.

## <a name="_Toc395637767"></a>Krok 3. Dodawanie usługi Azure Cosmos DB do projektu aplikacji internetowej MVC
Teraz większość podstaw dotyczących platformy ASP.NET MVC potrzebnych dla rozwiązania jest już gotowa. Możemy przejść do rzeczywistego celu tego samouczka, to znaczy dodania usługi Azure Cosmos DB do naszej aplikacji internetowej MVC.

1. Zestaw SDK platformy .NET dla usługi Azure Cosmos DB ma postać pakietu NuGet i jest dystrybuowany jako taki pakiet. Aby pobrać pakiet NuGet w programie Visual Studio, użyj menedżera pakietów NuGet w programie Visual Studio, klikając prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**, a następnie klikając pozycję **Zarządzaj pakietami NuGet**.
   
    ![Zrzut ekranu przedstawiający opcje prawego przycisku myszy dla projektu aplikacji sieci web w Eksploratorze rozwiązań z NuGet wyróżnioną pozycją Zarządzaj pakietami.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Zostanie wyświetlone okno dialogowe **Zarządzanie pakietami NuGet**.
2. W polu **Przeglądaj** wpisz ciąg ***Azure DocumentDB***. (Nazwa pakietu nie została zaktualizowana na Azure Cosmos DB).
   
    Korzystając z wyników, zainstaluj pakiet **Microsoft.Azure.DocumentDB firmy Microsoft**. Pakiet usługi Azure Cosmos DB zostanie pobrany i zainstalowany razem z wszystkimi zależnościami, takimi jak pakiet Newtonsoft.Json. Kliknij przycisk **OK** w oknie **Podgląd** i **Akceptuję** w oknie **Akceptacja licencji**, aby zakończyć instalację.
   
    ![Sreenshot okna Zarządzanie pakietami NuGet z Microsoft Azure Cosmos DB biblioteki klienta wyróżniony](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      Inna możliwość to zainstalowanie pakietu za pomocą konsoli menedżera pakietów. W tym celu w menu **Narzędzia** kliknij pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**. W wierszu polecenia wpisz następujące polecenie.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. Po zainstalowaniu pakietu rozwiązanie Visual Studio powinno przypominać następujące — z dodanymi dwoma odwołaniami: Microsoft.Azure.Documents.Client i Newtonsoft.Json.
   
    ![Zrzut ekranu przedstawiający dwa odwołania dodane do projektu danych JSON w Eksploratorze rozwiązań](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Krok 4. Konfigurowanie aplikacji platformy ASP.NET MVC
Teraz możemy dodać modele, widoki i kontrolery do aplikacji MVC:

* [Dodaj model](#_Toc395637764).
* [Dodaj kontroler](#_Toc395637765).
* [Dodaj widoki](#_Toc395637766).

### <a name="_Toc395637764"></a>Dodawanie modelu danych JSON
Zacznijmy od utworzenia części reprezentowanej przez literę **M** w nazwie wzorca MVC, modelu. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Modele**, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**.
   
      Zostanie wyświetlone okno dialogowe **Dodawanie nowego elementu**.
2. Nadaj nowej klasie nazwę **Item.cs** i kliknij polecenie **Dodaj**. 
3. W nowym pliku **Item.cs** dodaj następujący wpis po ostatniej instrukcji *using*.
   
        using Newtonsoft.Json;
4. Teraz zastąp ten kod 
   
        public class Item
        {
        }
   
    następującym kodem.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Wszystkie dane w usłudze Azure Cosmos DB zostaną przekazane i zapisane w formacie JSON. Aby kontrolować sposób serializacji/deserializacji obiektów przez program JSON.NET, możesz użyć atrybutu **JsonProperty**, tak jak przedstawiono w klasie **Item**, którą właśnie utworzyliśmy. Nie **musisz** tego robić, lecz chcemy się upewnić, że właściwości są zgodne z konwencją nazewnictwa formatu JSON (camelCase). 
   
    Możesz nie tylko kontrolować format nazwy właściwości umieszczanej w kodzie JSON, ale także całkowicie zmienić nazwy właściwości platformy .NET, tak jak to zrobiono w przypadku właściwości **Description**. 

### <a name="_Toc395637765"></a>Dodawanie kontrolera
Część **M** jest gotowa, teraz utwórzmy część reprezentowaną przez literę **C** w nazwie wzorca MVC, klasę kontrolera (ang. controller).

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Kontrolery**, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Kontroler**.
   
    Zostanie wyświetlone okno dialogowe **Dodawanie szkieletu**.
2. Wybierz pozycję **Kontroler MVC 5 — pusty**, a następnie kliknij polecenie **Dodaj**.
   
    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu z kontroler MVC 5 — pusty wyróżnioną](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Nadaj nowemu kontrolerowi nazwę **ItemController**.
   
    ![Zrzut ekranu okna dialogowego Dodawanie kontrolera](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    Po utworzeniu pliku rozwiązanie Visual Studio powinno przypominać następujące — z nowym plikiem ItemController.cs w **Eksploratorze rozwiązań**. Nowy plik Item.cs utworzony wcześniej jest także pokazany.
   
    ![Zrzut ekranu przedstawiający Eksploratora rozwiązań z nowym plikiem ItemController.cs i Item.cs programu Visual Studio rozwiązania](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Możesz zamknąć plik ItemController.cs, wrócimy do niego później. 

### <a name="_Toc395637766"></a>Dodawanie widoków
Teraz utwórzmy część reprezentowaną przez literę **V** w nazwie wzorca MVC, widoki (ang. view):

* [Dodaj widok indeksu elementów](#AddItemIndexView).
* [Dodaj widok nowego elementu](#AddNewIndexView).
* [Dodaj widok edycji elementu](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Dodawanie widoku indeksu elementów
1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, kliknij prawym przyciskiem myszy pusty folder **Item** utworzony wcześniej przez program Visual Studio po dodaniu elementu **ItemController**, kliknij polecenie **Dodaj**, a następnie pozycję **Widok**.
   
    ![Zrzut ekranu z Eksploratora rozwiązań przedstawiający folder Item utworzony w programie Visual Studio z wyróżnionymi poleceniami Dodaj widok](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. W oknie dialogowym **Dodawanie widoku** wykonaj następujące czynności:
   
   * W polu **Nazwa widoku** wpisz ***Index*** (Indeks).
   * W polu **Szablon** wybierz pozycję ***Lista***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
     
   ![Zrzut ekranu przedstawiający okno dialogowe dodawania widoku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Gdy wszystkie te wartości są ustawione, kliknij przycisk **Dodaj**. Program Visual Studio utworzy nowy widok szablonu. Po zakończeniu otworzy utworzony plik cshtml. Możemy zamknąć ten plik w programie Visual Studio, ponieważ wrócimy do niego później.

#### <a name="AddNewIndexView"></a>Dodawanie widoku nowego elementu
Podobnie jak w przypadku **indeksu elementów** teraz utworzymy nowy widok na potrzeby tworzenia nowych elementów **Item**.

1. W **Eksploratorze rozwiązań** kliknij ponownie prawym przyciskiem myszy folder **Item**, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Widok**.
2. W oknie dialogowym **Dodawanie widoku** wykonaj następujące czynności:
   
   * W polu **Nazwa widoku** wpisz ***Create*** (Tworzenie).
   * W polu **Szablon** wybierz pozycję ***Tworzenie***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
   * Kliknij pozycję **Add** (Dodaj).
   
#### <a name="_Toc395888515"></a>Dodawanie widoku edycji elementu
I w końcu dodaj ostatni widok — na potrzeby edytowania elementu **Item** — w taki sam sposób jak wcześniej.

1. W **Eksploratorze rozwiązań** kliknij ponownie prawym przyciskiem myszy folder **Item**, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Widok**.
2. W oknie dialogowym **Dodawanie widoku** wykonaj następujące czynności:
   
   * W polu **Nazwa widoku** wpisz ***Edit*** (Edycja).
   * W polu **Szablon** wybierz pozycję ***Edycja***.
   * W polu **Klasa modelu** wybierz pozycję ***Item (todo.Models)***.
   * W polu strony układu wpisz wartość ***~/Views/Shared/_Layout.cshtml***.
   * Kliknij pozycję **Add** (Dodaj).

Po zakończeniu zamknij wszystkie dokumenty cshtml w programie Visual Studio. Wrócimy do tych widoków później.

## <a name="_Toc395637769"></a>Krok 5. Podłączanie usługi Azure Cosmos DB
Po przygotowaniu standardowych zasobów wzorca MVC możemy zacząć dodawać kod dla usługi Azure Cosmos DB. 

W tej sekcji dodamy kod obsługujący następujące operacje:

* [Wyświetlanie niezakończonych elementów](#_Toc395637770).
* [Dodawanie elementów](#_Toc395637771).
* [Edytowanie elementów](#_Toc395637772).

### <a name="_Toc395637770"></a>Wyświetlanie niezakończonych elementów w aplikacji internetowej MVC
Najpierw musisz tutaj dodać klasę, która zawiera całą logikę umożliwiającą połączenie z usługą Azure Cosmos DB i używanie jej. Na potrzeby tego samouczka umieściliśmy całą tę logikę w klasie repozytorium o nazwie DocumentDBRepository. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**. Nadaj nowej klasie nazwę **DocumentDBRepository** i kliknij polecenie **Dodaj**.
2. W nowo utworzonej klasie **DocumentDBRepository** dodaj następujące instrukcje *using* powyżej deklaracji *namespace*.
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net;
        
    Teraz zastąp ten kod 
   
        public class DocumentDBRepository
        {
        }
   
    następującym kodem.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Odczytujemy niektóre wartości z konfiguracji, więc otwórz plik **Web.config** aplikacji i dodaj następujące wiersze w sekcji `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Teraz zaktualizuj wartości dla elementów *endpoint* i *authKey* za pomocą bloku Klucze w witrynie Azure Portal. Użyj wartości **URI** z bloku Klucze jako wartości ustawienia endpoint oraz wartości **KLUCZ PODSTAWOWY** lub **KLUCZ POMOCNICZY** z bloku Klucze jako wartości ustawienia authKey.

    W ten sposób podłączyliśmy repozytorium usługi Azure Cosmos DB. Teraz możemy dodać logikę aplikacji.

1. Pierwsza rzecz, którą chcemy udostępnić w aplikacji listy rzeczy do zrobienia, jest wyświetlanie niezakończonych elementów.  Skopiuj i wklej poniższy fragment kodu w dowolnym miejscu klasy **DocumentDBRepository**.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Otwórz dodany wcześniej element **ItemController** i dodaj następujące instrukcje *using* powyżej deklaracji namespace.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Jeśli projekt nie ma nazwy „todo”, zaktualizuj pozycję „todo.Models” tak, aby odpowiadała nazwie Twojego projektu.
   
    Teraz zastąp ten kod
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    następującym kodem.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Otwórz plik **Global.asax.cs** i dodaj następujący wiersz do metody **Application_Start**. 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

W tym momencie rozwiązanie powinno być możliwe do skompilowania bez żadnych błędów.

Jeśli uruchomisz aplikację teraz, przejdziesz do kontrolera **HomeController** i widoku **Index** (Index) tego kontrolera. Jest to domyślne zachowanie dla projektu szablonu MVC wybranego na początku, lecz nie jest to zachowanie, o które nam chodzi. Zmieńmy routing aplikacji MVC, aby zmienić to zachowanie.

Otwórz plik ***App\_Start\RouteConfig.cs***, odszukaj wiersz rozpoczynający się od ciągu „defaults:” i zmień go na podobny do następującego.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

W ten sposób platforma ASP.NET MVC zostanie poinformowana, że jeśli nie określisz wartości elementu URL na potrzeby kontrolowania zachowania routingu, to zamiast elementu **Home** zostanie użyty element **Item** jako kontroler i element **Index** użytkownika jako widok.

Teraz po uruchomieniu aplikacji zostanie wywołany element **ItemController**, który wywoła klasę repozytorium i użyje metody GetItems do zwrócenia wszystkich niezakończonych elementów do widoku **Views**\\**Item**\\**Index**. 

Jeśli skompilujesz i uruchomisz projekt teraz, zobaczysz stronę podobną do następującej.    

![Zrzut ekranu przedstawiający aplikację sieci web listy zadań do wykonania utworzone w ramach tego samouczka bazy danych](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Dodawanie elementów
Umieśćmy kilka elementów w naszej bazie danych, dzięki czemu będziemy mogli zobaczyć coś więcej niż pustą siatkę.

Dodajmy trochę kodu do elementów DBRepository i ItemController w usłudze Azure Cosmos DB, aby utrwalić rekord w tej usłudze.

1. Dodaj następującą metodę do klasy **DocumentDBRepository**.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Ta metoda po prostu przyjmuje przekazany obiekt i utrwala go w usłudze Azure Cosmos DB.
2. Otwórz plik ItemController.cs i dodaj następujący fragment kodu w klasie. W ten sposób platforma ASP.NET MVC wie, co robić w przypadku wywołania akcji **Create** (Utwórz). W tym przypadku po prostu renderuje skojarzony widok Create.cshtml utworzony wcześniej.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Teraz potrzebujemy kodu w tym kontrolerze, który zaakceptuje dane przesłane z widoku **Create** (Tworzenie).
3. Dodaj kolejny blok kodu do klasy ItemController.cs, który poinformuje platformę ASP.NET MVC o tym, co należy zrobić w przypadku tego kontrolera po wysłaniu do niego formularza za pomocą akcji POST.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Ten kod wywołuje element DocumentDBRepository i używa metody CreateItemAsync, aby utrwalić nowy element zadania do zrobienia w bazie danych. 
   
    **Uwagi dotyczące zabezpieczeń**: Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Do tego jest wymagane nie tylko dodanie tego atrybutu, lecz także obsługa tokenu zapobiegającego fałszerstwu przez widoki. Aby uzyskać więcej informacji na ten temat i przykłady poprawnej implementacji, zobacz [Preventing Cross-Site Request Forgery][Preventing Cross-Site Request Forgery] (Zapobieganie fałszerstwom żądania międzywitrynowego). Kod źródłowy dostępny w usłudze [GitHub][GitHub] zawiera pełną implementację.
   
    **Uwagi dotyczące zabezpieczeń**: Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby poznać więcej szczegółów, zobacz [Basic CRUD Operations in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC] (Podstawowe operacje CRUD na platformie ASP.NET MVC).

Teraz kod wymagany do dodawania nowych elementów do bazy jest kompletny.

### <a name="_Toc395637772"></a>Edytowanie elementów
Ostatnia rzecz do zrobienia to dodanie możliwości edytowania elementów **Item** w bazie danych i oznaczania ich jako zakończonych. Widok edycji został już dodany do projektu, więc musimy tylko ponownie dodać kod do kontrolera i klasy **DocumentDBRepository**.

1. Dodaj następujący kod do klasy **DocumentDBRepository**.
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    Pierwsza z tych metod, **GetItem**, pobiera z usługi Azure Cosmos DB element Item, który jest przekazywany z powrotem do kontrolera **ItemController**, a następnie do widoku **Edit** (Edycja).
   
    Druga metoda, którą właśnie dodaliśmy, zastępuje element **Document** w usłudze Azure Cosmos DB wersją elementu **Document** przekazaną z kontrolera **ItemController**.
2. Dodaj następujący kod do klasy **ItemController**.
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    Pierwsza metoda obsługuje akcję GET protokołu HTTP, która jest wywoływana po kliknięciu przez użytkownika linku **Edit** (Edytuj) w widoku **Index** (Indeks). Ta metoda pobiera element [**Document**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) z usługi Azure Cosmos DB i przekazuje go do widoku **Edit** (Edycja).
   
    Widok **Edit** (Edycja) wykona następnie akcję POST protokołu HTTP dla kontrolera **IndexController**. 
   
    Druga dodana metoda obsługuje przekazywanie zaktualizowanego obiektu do usługi Azure Cosmos DB w celu jego utrwalenia w bazie danych.

I gotowe — to wszystko, czego potrzebujemy, aby uruchomić aplikację, wyświetlić niezakończone **elementy**, dodać nowe **elementy** i edytować **elementy**.

## <a name="_Toc395637773"></a>Krok 6. Uruchamianie aplikacji lokalnie
Aby przetestować aplikację na lokalnej maszynie, wykonaj następujące czynności:

1. Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:
   
    ![Zrzut ekranu przedstawiający aplikację sieci web listy zadań do wykonania utworzone w ramach tego samouczka bazy danych](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Kliknij link **Create new** (Utwórz nowy) i dodaj wartości w polach **Name** (Nazwa) i **Description** (Opis). Pozostaw pole wyboru **Completed** (Zakończono) niezaznaczone — w przeciwnym przypadku nowy **element** zostanie dodany jako zakończony i nie zostanie wyświetlony na początkowej liście.
   
    ![Zrzut ekranu widoku Create](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Kliknij przycisk **Create** (Utwórz) — nastąpi przekierowanie z powrotem do widoku **Index** (Indeks) i **element** zostanie wyświetlony na liście.
   
    ![Zrzut ekranu przedstawiający widok Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Możesz także dodać kilka kolejnych **elementów** do listy rzeczy do zrobienia.
    
4. Kliknij pozycję **Edit** (Edytuj) obok **elementu** na liście. Zostanie wyświetlony widok **Edit** (Edycja), w którym można zaktualizować dowolną właściwość obiektu, w tym flagę **Completed** (Zakończono). Po zaznaczeniu flagi **Complete** (Zakończono) i kliknięciu przycisku **Save** (Zapisz) **element** zostanie usunięty z listy niezakończonych zadań.
   
    ![Zrzut ekranu przedstawiający widok indeksu przy użyciu zaznaczone pole ukończone](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Po przetestowaniu aplikacji naciśnij klawisze Ctrl+F5, aby zatrzymać jej debugowanie. Wszystko jest gotowe do wdrożenia.

## <a name="_Toc395637774"></a>Krok 7. Wdrażanie aplikacji w usłudze Azure App Service 
Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować aplikację, musisz tylko kliknąć prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i kliknąć polecenie **Publikuj**.
   
    ![Zrzut ekranu opcji Publikuj w Eksploratorze rozwiązań](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. W oknie dialogowym **Publikowanie** kliknij pozycję **Microsoft Azure App Service**, a następnie wybierz pozycję **Utwórz nowy**, aby utworzyć profil usługi App Service, lub kliknij pozycję **Wybierz istniejący**, aby skorzystać z istniejącego profilu.

    ![Okno dialogowe Publikowanie w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Jeśli masz istniejący profil usługi Azure App Service, wprowadź nazwę subskrypcji. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu, a następnie wybierz swoją usługę Azure App Service. 
   
    ![Okno dialogowe usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Aby utworzyć nowy profil usługi Azure App Service, kliknij pozycję **Utwórz nowy** w oknie dialogowym **Publikowanie**. W oknie dialogowym **Tworzenie usługi App Service** wprowadź nazwę aplikacji internetowej oraz odpowiednią subskrypcję, grupę zasobów i plan usługi App Service, a następnie kliknij pozycję **Utwórz**.

    ![Okno dialogowe Tworzenie usługi App Service w programie Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

W ciągu kilku sekund program Visual Studio zakończy publikowanie aplikacji internetowej i uruchomi przeglądarkę, w której będzie można zobaczyć swoje dzieło działające na platformie Azure.



## <a name="_Toc395637775"></a>Następne kroki
Gratulacje! Udało Ci się utworzyć Twoją pierwszą aplikację internetową dla platformy ASP.NET MVC używającą usługi Azure Cosmos DB i opublikować ją na platformie Azure. Kod źródłowy kompletnej aplikacji, w tym funkcji szczegółów i usuwania, które nie zostały uwzględnione w tym samouczku, można pobrać lub sklonować z usługi [GitHub][GitHub]. Jeśli chcesz dodać go do swojej aplikacji, wystarczy pobrać kod i to zrobić.

Aby dodać kolejne funkcje do aplikacji, zapoznaj się z interfejsami API dostępnymi w [bibliotece usługi Azure Cosmos DB dla platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet). Możesz ją także współtworzyć za pomocą usługi [GitHub][GitHub]. 

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
