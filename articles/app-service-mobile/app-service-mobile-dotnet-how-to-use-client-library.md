---
title: Praca z biblioteką zarządzanych klientów
description: Dowiedz się, jak korzystać z biblioteki klienta platformy .NET dla aplikacji mobilnych usługi Azure App Service w aplikacjach windows i xamarin.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249375"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak używać zarządzanego klienta usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu biblioteki zarządzanych klientów dla aplikacji mobilnych usługi Azure App Service dla systemu Windows i aplikacji platformy Xamarin. Jeśli jesteś nowym użytkownikiem aplikacji mobilnych, należy rozważyć ukończenie samouczka szybki start w [usłudze Azure Mobile Apps.][1] W tym przewodniku koncentrujemy się na sdk zarządzanym po stronie klienta. Aby dowiedzieć się więcej o skusikach SDK po stronie serwera dla aplikacji mobilnych, zobacz dokumentację [sdk .NET Server SDK][2] lub [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dla sdk klienta znajduje się tutaj: [Odwołanie do klienta usługi Azure Mobile Apps .NET][4].
Można również znaleźć kilka przykładów klienta w [repozytorium GitHub przykładów platformy Azure.][5]

## <a name="supported-platforms"></a>Obsługiwane platformy
Platforma .NET obsługuje następujące platformy:

* Xamarin Android zwalnia dla API 19 do 24 (KitKat przez Nougat)
* Wersje systemu Xamarin dla systemu iOS w wersji 8.0 i nowszej
* Platforma uniwersalna systemu Windows
* Windows Phone 8,1
* Windows Phone 8.0 z wyjątkiem aplikacji Silverlight

Uwierzytelnianie "przepływ serwera" używa WebView dla przedstawionego interfejsu użytkownika.  Jeśli urządzenie nie jest w stanie przedstawić interfejsu użytkownika WebView, potrzebne są inne metody uwierzytelniania.  Ten SDK nie jest zatem odpowiedni dla urządzeń typu Watch lub podobnie ograniczonych.

## <a name="setup-and-prerequisites"></a><a name="setup"></a>Ustawienia i wymagania wstępne
Zakładamy, że utworzono i opublikowano już projekt zaplecza aplikacji mobilnej, który zawiera co najmniej jedną tabelę.  W kodzie użytym w tym `TodoItem` temacie tabela ma nazwę `Id` `Text`i `Complete`zawiera następujące kolumny: , , i . Ta tabela jest tą samą tabelą utworzoną po zakończeniu [przewodnika Szybki start usługi Azure Mobile Apps][1].

Odpowiedni typ po stronie klienta w języku C# jest następującą klasą:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[Atrybut JsonPropertyAttribute][6] służy do definiowania mapowania *PropertyName* między polem klienta a polem tabeli.

Aby dowiedzieć się, jak tworzyć tabele w wewnętrznej cei aplikacji mobilnych, zobacz [temat SDK serwera .NET][7] lub [SDK serwera Node.js][8]. Jeśli utworzono zaplecze aplikacji mobilnej w witrynie Azure portal przy użyciu narzędzia Szybki start, można również użyć ustawienia **Łatwe tabele** w [witrynie Azure portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Jak: Instalowanie pakietu zestawu SDK klienta zarządzanego
Użyj jednej z następujących metod, aby zainstalować pakiet SDK zarządzanego klienta dla aplikacji mobilnych z [NuGet:][9]

* **Program Visual Studio** Kliknij prawym przyciskiem myszy projekt, kliknij polecenie `Microsoft.Azure.Mobile.Client` **Zarządzaj pakietami NuGet**, wyszukaj pakiet, a następnie kliknij polecenie **Zainstaluj**.
* **Studio Xamarin** Kliknij prawym przyciskiem myszy projekt, kliknij polecenie `Microsoft.Azure.Mobile.Client` **Dodaj** > **pakiety NuGet**, wyszukaj pakiet, a następnie kliknij polecenie Dodaj **pakiet**.

W głównym pliku aktywności pamiętaj o dodaniu następującej **instrukcji:**

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Pamiętaj, że wszystkie pakiety dla pomocy technicznej, do których odwołuje się projekt systemu Android, muszą mieć tę samą wersję. Zestawu SDK `Xamarin.Android.Support.CustomTabs` ma zależność dla platformy systemu Android, więc jeśli projekt używa nowszych pakietów pomocy technicznej, musisz zainstalować ten pakiet z wymaganą wersją bezpośrednio, aby uniknąć konfliktów.

### <a name="how-to-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>Jak: Praca z symbolami debugowania w programie Visual Studio
Symbole obszaru nazw Microsoft.Azure.Mobile są dostępne w [witrynie SymbolSource][10].  Zapoznaj się z [instrukcjami SymbolSource,][11] aby zintegrować SymbolSource z programem Visual Studio.

## <a name="create-the-mobile-apps-client"></a><a name="create-client"></a>Tworzenie klienta aplikacji mobilnych
Poniższy kod tworzy [MobileServiceClient][12] obiektu, który jest używany do uzyskiwania dostępu do wewnętrznej bazy danych aplikacji mobilnej.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

W poprzednim kodzie `MOBILE_APP_URL` zastąp adresem URL zaplecza aplikacji mobilnej, który znajduje się w bloku zaplecza aplikacji mobilnej w [witrynie Azure portal]. MobileServiceClient obiekt powinien być singleton.

## <a name="work-with-tables"></a>Praca z tabelami
W poniższej sekcji opisano sposób wyszukiwania i pobierania rekordów oraz modyfikowania danych w tabeli.  Poruszane są następujące tematy:

* [Tworzenie odwołania do tabeli](#instantiating)
* [Zapytania o dane](#querying)
* [Filtr zwracane dane](#filtering)
* [Sortowanie zwróconych danych](#sorting)
* [Zwracanie danych na stronach](#paging)
* [Zaznaczanie określonych kolumn](#selecting)
* [Szukaj rekordu według identyfikatora](#lookingup)
* [Radzenie sobie z zapytaniami bez typu](#untypedqueries)
* [Wstawianie danych](#inserting)
* Aktualizowanie danych
* [Usuwanie danych](#deleting)
* [Rozwiązywanie konfliktów i optymistyczna współbieżność](#optimisticconcurrency)
* [Powiązanie z interfejsem użytkownika systemu Windows](#binding)
* [Zmiana rozmiaru strony](#pagesize)

### <a name="how-to-create-a-table-reference"></a><a name="instantiating"></a>Jak: Tworzenie odwołania do tabeli
Cały kod, który uzyskuje dostęp do danych lub modyfikuje dane w tabeli wewnętrznej bazy danych wywołuje funkcje obiektu. `MobileServiceTable` Uzyskaj odwołanie do tabeli, wywołując [GetTable] metody, w następujący sposób:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Zwrócony obiekt używa typizowanego modelu serializacji. Obsługiwany jest również model serializacji bez typu. Poniższy przykład [tworzy odwołanie do tabeli bez typu:]

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

W kwerendach bez typu należy określić podstawowy ciąg zapytania OData.

### <a name="how-to-query-data-from-your-mobile-app"></a><a name="querying"></a>Jak: Zapytanie o dane z aplikacji mobilnej
W tej sekcji opisano sposób wystawiania zapytań do zaplecza aplikacji mobilnej, która zawiera następujące funkcje:

* [Filtr zwracane dane](#filtering)
* [Sortowanie zwróconych danych](#sorting)
* [Zwracanie danych na stronach](#paging)
* [Zaznaczanie określonych kolumn](#selecting)
* [Wyszukuj dane według identyfikatora](#lookingup)

> [!NOTE]
> Rozmiar strony sterowany serwerem jest wymuszany, aby zapobiec zwracaniu wszystkich wierszy.  Stronicowanie utrzymuje domyślne żądania dla dużych zestawów danych z negatywnie wpływając na usługę.  Aby zwrócić więcej niż 50 `Skip` wierszy, użyj metody i, `Take` zgodnie z opisem w Return data in [pages](#paging).

### <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Jak: Filtrowanie zwróconych danych
Poniższy kod ilustruje sposób filtrowania danych przez dołączenie klauzuli `Where` w kwerendzie. Zwraca wszystkie elementy, z `todoTable` których `Complete` właściwość jest równa `false`. Funkcja [Where] stosuje predykat filtrowania wiersza do kwerendy względem tabeli.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Identyfikator URI żądania wysłanego do wewnętrznej bazy danych można wyświetlić za pomocą oprogramowania do inspekcji wiadomości, takiego jak narzędzia programistyczne przeglądarki lub [fiddler]. Jeśli spojrzysz na identyfikator URI żądania, należy zauważyć, że ciąg zapytania jest modyfikowany:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

To żądanie OData jest tłumaczone na zapytanie SQL przez SDK serwera:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkcja, która jest `Where` przekazywana do metody może mieć dowolną liczbę warunków.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

W tym przykładzie można przetłumaczyć na kwerendę SQL przez SDK serwera:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Tę kwerendę można również podzielić na wiele klauzul:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Te dwie metody są równoważne i mogą być stosowane zamiennie.  Pierwsza opcja&mdash;łączenia wielu predykatów&mdash;w jednej kwerendzie jest bardziej zwarta i zalecana.

Klauzula `Where` obsługuje operacje, które mają być tłumaczone na podzbiór OData. Operacje obejmują:

* Operatory relacyjne (==, !=, <, <=, >, >=),
* Operatory arytmetyczne (+, -, /, *, %),
* Precyzja liczb (Math.Floor, Math.Ceiling),
* Funkcje ciągu (długość, podciąg, zamień, indeksowanie, starty, kończy),
* Właściwości daty (rok, miesiąc, dzień, godzina, minuta, sekunda),
* Dostęp do właściwości obiektu i
* Wyrażenia łączące dowolną z tych operacji.

Rozważając to, co obsługuje zestaw SDK serwera, można wziąć pod uwagę [dokumentację OData v3].

### <a name="how-to-sort-returned-data"></a><a name="sorting"></a>Jak: Sortowanie zwróconych danych
Poniższy kod ilustruje sposób sortowania danych przez włączenie [funkcji OrderBy] lub [OrderByDescending] w kwerendzie. Zwraca elementy `todoTable` z posortowanych `Text` rosnących według pola.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="how-to-return-data-in-pages"></a><a name="paging"></a>Jak: Zwracanie danych na stronach
Domyślnie wewnętrznej bazy danych zwraca tylko pierwsze 50 wierszy. Można zwiększyć liczbę zwróconych wierszy, wywołując [Take] metody. Użyj `Take` wraz z [Skip] metody, aby zażądać określonej "strony" całkowitego zestawu danych zwróconego przez kwerendę. Następująca kwerenda, po wykonaniu zwraca trzy pierwsze elementy w tabeli.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Następująca poprawiona kwerenda pomija pierwsze trzy wyniki i zwraca następne trzy wyniki. Ta kwerenda tworzy drugą "stronę" danych, gdzie rozmiar strony jest trzy elementy.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Metoda [IncludeTotalCount] żąda całkowitej liczby *wszystkich* rekordów, które zostałyby zwrócone, ignorując określoną klauzulę stronicowania/limitu:

```csharp
query = query.IncludeTotalCount();
```

W aplikacji rzeczywistych można użyć zapytań podobnych do poprzedniego przykładu z formantem pagera lub porównywalnym interfejsem użytkownika do nawigacji między stronami.

> [!NOTE]
> Aby zastąpić limit 50 wierszy w wewnętrznej bazy danych aplikacji mobilnej, należy również zastosować [enablequeryattribute] do publicznej metody GET i określić zachowanie stronicowania. Po zastosowaniu do metody, następujące ustawia maksymalną zwracane wiersze do 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="how-to-select-specific-columns"></a><a name="selecting"></a>Jak: Zaznaczanie określonych kolumn
Można określić, który zestaw właściwości do uwzględnienia w wynikach, dodając [Select] klauzuli do kwerendy. Na przykład poniższy kod pokazuje, jak wybrać tylko jedno pole, a także jak wybrać i sformatować wiele pól:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Wszystkie opisane do tej pory funkcje są addytywne, dzięki czemu możemy je kontynuować. Każde wywołanie łańcuchowe wpływa na więcej kwerendy. Jeszcze jeden przykład:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="how-to-look-up-data-by-id"></a><a name="lookingup"></a>Jak: Wyszukuj dane według identyfikatora
Funkcja [LookupAsync] może służyć do wyszukiwania obiektów z bazy danych o określonym identyfikatorze.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="how-to-execute-untyped-queries"></a><a name="untypedqueries"></a>Jak: Wykonywanie kwerend bez typu
Podczas wykonywania kwerendy przy użyciu obiektu tabeli bez typu należy jawnie określić ciąg zapytania OData, wywołując [ReadAsync], jak w poniższym przykładzie:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Można uzyskać z powrotem wartości JSON, które można użyć jak worek właściwości. Aby uzyskać więcej informacji na temat Json.NET JToken i Newtonsoft, zobacz witrynę [Json.NET.]

### <a name="how-to-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Jak: wstawianie danych do zaplecza aplikacji mobilnej
Wszystkie typy klientów muszą zawierać element członkowski o nazwie **Id**, który jest domyślnie ciągiem. Ten **identyfikator** jest wymagany do wykonywania operacji CRUD i synchronizacji w trybie offline. Poniższy kod ilustruje, jak użyć [InsertAsync] metody wstawić nowe wiersze do tabeli. Parametr zawiera dane, które mają być wstawione jako obiekt .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Jeśli unikatowa wartość identyfikatora niestandardowego `todoItem` nie jest uwzględniona w wstawianie, identyfikator GUID jest generowany przez serwer.
Wygenerowany identyfikator można pobrać, sprawdzając obiekt po powrocie wywołania.

Aby wstawić dane bez typu, można skorzystać z Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Oto przykład przy użyciu adresu e-mail jako unikatowy identyfikator ciągu:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Praca z wartościami identyfikatorów
Aplikacje mobilne obsługują unikatowe niestandardowe wartości ciągów dla kolumny **identyfikatora** tabeli. Wartość ciągu umożliwia aplikacjom używanie niestandardowych wartości, takich jak adresy e-mail lub nazwy użytkowników dla identyfikatora.  Identyfikatory ciągów zapewniają następujące korzyści:

* Identyfikatory są generowane bez dokonywania podróży w obie strony do bazy danych.
* Rekordy są łatwiejsze do scalania z różnych tabel lub baz danych.
* Wartości identyfikatorów można lepiej zintegrować z logiką aplikacji.

Gdy wartość identyfikatora ciągu nie jest ustawiona na wstawionym rekordzie, wewnętrznej bazy danych aplikacji mobilnej generuje unikatową wartość dla identyfikatora. Za pomocą [metody Guid.NewGuid] można wygenerować własne wartości identyfikatorów, na kliencie lub w wewnętrznej bazy danych.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="how-to-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Jak: modyfikowanie danych w wewnętrznej cemie aplikacji mobilnej
Poniższy kod ilustruje, jak użyć [UpdateAsync] metody, aby zaktualizować istniejący rekord o tym samym identyfikatorze z nowymi informacjami. Parametr zawiera dane, które mają zostać zaktualizowane jako obiekt .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Aby zaktualizować dane bez typu, możesz skorzystać z [Json.NET] w następujący sposób:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Podczas `id` dokonywania aktualizacji należy określić pole. Wewnętrznej bazy danych `id` używa pola do określenia, który wiersz do aktualizacji. Pole `id` można uzyskać z wyniku `InsertAsync` wywołania. Jest `ArgumentException` wywoływana, jeśli spróbujesz zaktualizować `id` element bez podawania wartości.

### <a name="how-to-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Jak: Usuwanie danych w wewnętrznej cemie aplikacji mobilnej
Poniższy kod ilustruje, jak użyć [DeleteAsync] metody, aby usunąć istniejące wystąpienie. Wystąpienie jest identyfikowane przez `id` `todoItem`pole ustawione na pliku .

```csharp
await todoTable.DeleteAsync(todoItem);
```

Aby usunąć dane bez typu, możesz skorzystać z Json.NET w następujący sposób:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Podczas żądania usunięcia należy określić identyfikator. Inne właściwości nie są przekazywane do usługi lub są ignorowane w usłudze. Wynikiem `DeleteAsync` połączenia jest zwykle `null`. Identyfikator do przekazania można uzyskać z wyniku `InsertAsync` wywołania. A `MobileServiceInvalidOperationException` jest generowany podczas próby usunięcia elementu `id` bez określania pola.

### <a name="how-to-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Jak: Użyj optymistycznej współbieżności do rozwiązywania konfliktów
Dwóch lub więcej klientów może zapisywać zmiany w tym samym elemencie w tym samym czasie. Bez wykrywania konfliktów ostatni zapis zastąpi wszystkie poprzednie aktualizacje. **Optymistyczna kontrola współbieżności** zakłada, że każda transakcja może zatwierdzić i dlatego nie używa blokowania zasobów.  Przed zatwierdzeniem transakcji optymistyczna kontrola współbieżności sprawdza, czy żadna inna transakcja nie zmodyfikowała danych. Jeśli dane zostały zmodyfikowane, transakcja zatwierdzania jest przywracana.

Aplikacje mobilne obsługuje optymistyczne kontroli współbieżności `version` przez śledzenie zmian do każdego elementu przy użyciu kolumny właściwości systemu, który jest zdefiniowany dla każdej tabeli w wewnętrznej bazy danych aplikacji mobilnej. Za każdym razem, gdy rekord `version` jest aktualizowany, aplikacje mobilne ustawia właściwość dla tego rekordu na nową wartość. Podczas każdego żądania `version` aktualizacji właściwość rekordu dołączonego do żądania jest porównywana z tą samą właściwością rekordu na serwerze. Jeśli wersja przekazana z żądaniem nie jest zgodna z wewnętrznej `MobileServicePreconditionFailedException<T>` bazy danych, biblioteka klienta zgłasza wyjątek. Typ dołączony z wyjątkiem jest rekord z wewnętrznej bazy danych zawierający wersję serwera rekordu. Aplikacja może następnie użyć tych informacji, aby zdecydować, czy `version` ponownie wykonać żądanie aktualizacji z poprawną wartością z wewnętrznej bazy danych, aby zatwierdzić zmiany.

Zdefiniuj kolumnę `version` w klasie tabeli dla właściwości systemu, aby włączyć optymistyczną współbieżność. Przykład:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Aplikacje używające tabel bez typu umożliwiają optymistyczną współbieżność, ustawiając `Version` flagę w `SystemProperties` tabeli w następujący sposób.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Oprócz włączania optymistycznej współbieżności, należy również `MobileServicePreconditionFailedException<T>` złapać wyjątek w kodzie podczas wywoływania [UpdateAsync].  Rozwiąż konflikt, stosując `version` poprawność do zaktualizowanego rekordu i [wywołaj UpdateAsync] z rozwiązanym rekordem. Poniższy kod pokazuje, jak rozwiązać konflikt zapisu po wykryciu:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Aby uzyskać więcej informacji, zobacz [temat Synchronizacja danych w trybie offline w usłudze Azure Mobile Apps.]

### <a name="how-to-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>Jak: Powiązać dane aplikacji mobilnych z interfejsem użytkownika systemu Windows
W tej sekcji pokazano sposób wyświetlania zwróconych obiektów danych przy użyciu elementów interfejsu użytkownika w aplikacji systemu Windows.  Poniższy przykładowy kod wiąże się ze źródłem listy z kwerendą dla elementów niekompletnych. [MobileServiceCollection] tworzy kolekcję powiązań obsługujących aplikacje mobilne.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Niektóre formanty w zarządzanym czasie wykonywania obsługują interfejs o nazwie [ISupportIncrementalLoading]. Ten interfejs umożliwia formanty do żądania dodatkowych danych, gdy użytkownik przewija. Istnieje wbudowana obsługa tego interfejsu dla uniwersalnych aplikacji systemu Windows za pośrednictwem [MobileServiceIncrementalLoadingCollection], który automatycznie obsługuje wywołania z formantów. Użyj `MobileServiceIncrementalLoadingCollection` w aplikacjach systemu Windows w następujący sposób:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Aby korzystać z nowej kolekcji w aplikacjach dla `ToCollection` systemów Windows `IMobileServiceTableQuery<T>` `IMobileServiceTable<T>`Phone 8 i "Silverlight", użyj włączonych i . Aby załadować `LoadMoreItemsAsync()`dane, zadzwoń .

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Podczas korzystania z kolekcji `ToCollectionAsync` `ToCollection`utworzonej przez wywołanie lub , otrzymasz kolekcję, która może być powiązana z formantami interfejsu użytkownika.  Ta kolekcja jest stronicowania.This collection is aging-aware.  Ponieważ kolekcja ładuje dane z sieci, ładowanie czasami kończy się niepowodzeniem. Aby obsłużyć takie `OnException` błędy, `MobileServiceIncrementalLoadingCollection` należy zastąpić metodę obsługi `LoadMoreItemsAsync`wyjątków wynikających z wywołań do .

Należy wziąć pod uwagę, jeśli tabela ma wiele pól, ale chcesz wyświetlić tylko niektóre z nich w formancie. Można użyć wskazówek w poprzedniej sekcji["Wybierz określone kolumny](#selecting)", aby wybrać określone kolumny do wyświetlenia w interfejsie użytkownika.

### <a name="change-the-page-size"></a><a name="pagesize"></a>Zmienianie rozmiaru strony
Usługa Azure Mobile Apps domyślnie zwraca maksymalnie 50 elementów na żądanie.  Rozmiar stronicowania można zmienić, zwiększając maksymalny rozmiar strony zarówno na kliencie, jak i na serwerze.  Aby zwiększyć żądany rozmiar `PullOptions` strony, `PullAsync()`należy określić podczas korzystania z:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Przy założeniu, `PageSize` że zostały wykonane równe lub większe niż 100 w ramach serwera, żądanie zwraca maksymalnie 100 elementów.

## <a name="work-with-offline-tables"></a><a name="#offlinesync"></a>Praca z tabelami offline
Tabele trybu offline używają lokalnego magazynu SQLite do przechowywania danych do użytku w trybie offline.  Wszystkie operacje tabeli są wykonywane w lokalnym magazynie SQLite zamiast zdalnego magazynu serwerów.  Aby utworzyć tabelę w trybie offline, najpierw przygotuj projekt:

1. W programie Visual Studio kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzaj pakietami NuGet dla rozwiązania...**, a następnie wyszukaj i zainstaluj pakiet NuGet **firmy Microsoft.Azure.Mobile.Client.SQLiteStore** dla wszystkich projektów w rozwiązaniu.
2. (Opcjonalnie) Aby obsługiwać urządzenia z systemem Windows, zainstaluj jeden z następujących pakietów środowiska wykonawczego SQLite:

   * **Środowisko wykonawcze systemu Windows 8.1:** Zainstaluj [SQLite dla Windows 8.1][3].
   * **Windows Phone 8.1:** Zainstaluj [SQLite dla Windows Phone 8.1][4].
   * **Uniwersalna platforma systemu Windows** Zainstaluj [SQLite dla systemu Uniwersalnego Systemu Windows][5].
3. (Opcjonalnie). W przypadku urządzeń z systemem Windows kliknij pozycję **Odwołania Dodaj** > **odwołanie...**, rozwiń folder **systemu Windows** > **rozszerzeniami**, a następnie włącz odpowiedni **moduł SQLite dla systemu Windows** SDK wraz ze **środowiskiem visual C++ 2013 Runtime for Windows** SDK.
    Nazwy SQLite SDK różnią się nieznacznie w zależności od platformy systemu Windows.

Przed utworzeniem odwołania do tabeli należy przygotować magazyn lokalny:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicjowanie magazynu odbywa się zwykle natychmiast po utworzeniu klienta.  **OfflineDbPath** powinien być nazwa pliku nadaje się do użytku na wszystkich obsługiwanych platformach.  Jeśli ścieżka jest w pełni kwalifikowaną ścieżką (oznacza to, że zaczyna się od ukośnika), używana jest ta ścieżka.  Jeśli ścieżka nie jest w pełni kwalifikowana, plik jest umieszczany w lokalizacji specyficznej dla platformy.

* W przypadku urządzeń z systemem iOS i Android domyślną ścieżką jest folder "Pliki osobiste".
* W przypadku urządzeń z systemem Windows domyślną ścieżką jest folder "AppData" specyficzny dla aplikacji.

Odwołanie do tabeli można `GetSyncTable<>` uzyskać przy użyciu metody:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Nie trzeba uwierzytelniać, aby używać tabeli w trybie offline.  Wystarczy uwierzytelnić tylko podczas komunikowania się z usługą wewnętrznej bazy danych.

### <a name="syncing-an-offline-table"></a><a name="syncoffline"></a>Synchronizowanie tabeli w trybie offline
Tabele trybu offline nie są domyślnie synchronizowane z zapleczem.  Synchronizacja jest podzielona na dwie części.  Zmiany można wypychać oddzielnie od pobierania nowych elementów.  Oto typowa metoda synchronizacji:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Jeśli pierwszy argument `PullAsync` ma wartość null, synchronizacja przyrostowa nie jest używana.  Każda operacja synchronizacji pobiera wszystkie rekordy.

SDK wykonuje niejawne `PushAsync()` przed ciągnięciem rekordów.

Obsługa konfliktów odbywa `PullAsync()` się na metodę.  Możesz radzić sobie z konfliktami w taki sam sposób, jak tabele online.  Konflikt jest wywoływany, gdy `PullAsync()` jest wywoływana zamiast podczas wstawiania, aktualizowania lub usuwania. Jeśli wystąpi wiele konfliktów, są one powiązane w jednym MobileServicePushFailedException.  Obsługiwać każdy błąd oddzielnie.

## <a name="work-with-a-custom-api"></a><a name="#customapi"></a>Praca z niestandardowym interfejsem API
Niestandardowy interfejs API umożliwia definiowanie niestandardowych punktów końcowych, które udostępniają funkcje serwera, który nie jest mapowany na operację wstawiania, aktualizowania, usuwania lub odczytu. Za pomocą niestandardowego interfejsu API, można mieć większą kontrolę nad wiadomości, w tym odczytu i ustawiania nagłówków wiadomości HTTP i definiowanie formatu treści wiadomości innych niż JSON.

Wywołanie niestandardowego interfejsu API przez wywołanie jednej z [InvokeApiAsync] metody na kliencie. Na przykład następujący wiersz kodu wysyła żądanie POST do **completeAll** interfejsu API w wewnętrznej bazy danych:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ten formularz jest wywołaniem metody wpisanej i wymaga zdefiniowania typu zwracanego **MarkAllResult.** Obsługiwane są zarówno metody typizowane, jak i nietypowane.

InvokeApiAsync() Metoda poprzedza "/api/" do interfejsu API, który chcesz wywołać, chyba że interfejs API rozpoczyna się od '/'.
Przykład:

* `InvokeApiAsync("completeAll",...)`wywołania /api/completeWszystko na wewnętrznej stronie wewnętrznej
* `InvokeApiAsync("/.auth/me",...)`połączenia /.auth/me na wewnętrznej stronie

InvokeApiAsync służy do wywoływania dowolnego webapi, w tym tych WebAPI, które nie są zdefiniowane za pomocą aplikacji azure mobile.  W przypadku korzystania z invokeApiAsync(), odpowiednie nagłówki, w tym nagłówki uwierzytelniania, są wysyłane z żądaniem.

## <a name="authenticate-users"></a><a name="authentication"></a>Uwierzytelnianie użytkowników
Aplikacje mobilne obsługują uwierzytelnianie i autoryzowanie użytkowników aplikacji przy użyciu różnych zewnętrznych dostawców tożsamości: Facebooka, Google, konta Microsoft, Twittera i usługi Azure Active Directory. Uprawnienia do tabel można ustawić w celu ograniczenia dostępu dla określonych operacji tylko do uwierzytelnionych użytkowników. Można również użyć tożsamości uwierzytelnionych użytkowników do zaimplementowania reguł autoryzacji w skryptach serwera. Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [Dodawanie uwierzytelniania do aplikacji].

Obsługiwane są dwa przepływy uwierzytelniania: przepływ *zarządzany przez klienta* i zarządzany przez *serwer.* Przepływ zarządzany przez serwer zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się na interfejsie uwierzytelniania sieci web dostawcy. Przepływ zarządzany przez klienta umożliwia głębszą integrację z możliwościami specyficznymi dla urządzenia, ponieważ opiera się na sdkach SDK specyficznych dla dostawcy.

> [!NOTE]
> Zalecamy użycie przepływu zarządzanego przez klienta w aplikacjach produkcyjnych.

Aby skonfigurować uwierzytelnianie, należy zarejestrować aplikację u jednego lub większej liczby dostawców tożsamości.  Dostawca tożsamości generuje identyfikator klienta i klucz tajny klienta dla aplikacji.  Te wartości są następnie ustawiane w wewnętrznej bazy danych, aby włączyć uwierzytelnianie/autoryzację usługi Azure App Service.  Aby uzyskać więcej informacji, postępuj zgodnie ze szczegółowymi instrukcjami w samouczku [Dodaj uwierzytelnianie do aplikacji].

W tej sekcji przedstawiono następujące tematy:

* [Uwierzytelnianie zarządzane przez klienta](#clientflow)
* [Uwierzytelnianie zarządzane przez serwer](#serverflow)
* [Buforowanie tokenu uwierzytelniania](#caching)

### <a name="client-managed-authentication"></a><a name="clientflow"></a>Uwierzytelnianie zarządzane przez klienta
Aplikacja może niezależnie skontaktować się z dostawcą tożsamości, a następnie podać zwrócony token podczas logowania z zaplecza. Ten przepływ klienta umożliwia zapewnienie środowiska logowania jednokrotnego dla użytkowników lub pobrać dodatkowe dane użytkownika z dostawcy tożsamości. Uwierzytelnianie przepływu klienta jest preferowane przy użyciu przepływu serwera, ponieważ sdk dostawcy tożsamości zapewnia bardziej natywne działanie środowiska użytkownika i umożliwia dodatkowe dostosowanie.

Przykłady są podane dla następujących wzorców uwierzytelniania przepływu klienta:

* [Biblioteka uwierzytelniania usługi Active Directory](#adal)
* [Facebook lub Google](#client-facebook)

#### <a name="authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Uwierzytelnianie użytkowników za pomocą biblioteki uwierzytelniania usługi Active Directory
Biblioteka uwierzytelniania usługi Active Directory (ADAL) służy do inicjowania uwierzytelniania użytkowników od klienta przy użyciu uwierzytelniania usługi Azure Active Directory.

1. Skonfiguruj zaplecze aplikacji mobilnej dla logowania usługi AAD, wykonując samouczek [Jak skonfigurować usługę App Service dla logowania usługi Active Directory.] Upewnij się, aby wykonać opcjonalny krok rejestrowania natywnej aplikacji klienckiej.
2. W programie Visual Studio lub Xamarin Studio otwórz `Microsoft.IdentityModel.Clients.ActiveDirectory` projekt i dodaj odwołanie do pakietu NuGet. Podczas wyszukiwania dołącz wersje wersji wstępnej.
3. Dodaj następujący kod do aplikacji, zgodnie z platformą, której używasz. W każdym z nich dokonaj następujących zamienników:

   * Zamień **INSERT-AUTHORITY-HERE** na nazwę dzierżawy, w której zainicjowano aprowizacji aplikacji. Format powinien https://login.microsoftonline.com/contoso.onmicrosoft.combyć . Tę wartość można skopiować z karty Domena w usłudze Azure Active Directory w [witrynie Azure portal].
   * Zamień **INSERT-RESOURCE-ID-HERE** na identyfikator klienta dla wewnętrznej bazy danych aplikacji mobilnej. Identyfikator klienta można uzyskać na karcie **Zaawansowane** w obszarze **Ustawienia usługi Azure Active Directory** w portalu.
   * Zastąp **INSERT-CLIENT-ID-HERE** identyfikatorem klienta skopiowanym z natywnej aplikacji klienckiej.
   * Zastąp **INSERT-REDIRECT-URI-HERE** punktem końcowym *witryny /.auth/login/done,* używając schematu HTTPS. Ta wartość powinna *https://contoso.azurewebsites.net/.auth/login/done*być podobna do .

     Kod wymagany dla każdej platformy jest następujący:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Logowanie jednokrotne przy użyciu tokenu z Facebooka lub Google
Możesz użyć przepływu klienta, jak pokazano w tym urywek dla Facebooka lub Google.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="server-managed-authentication"></a><a name="serverflow"></a>Uwierzytelnianie zarządzane przez serwer
Po zarejestrowaniu dostawcy tożsamości, zadzwoń [LoginAsync] metody na [MobileServiceClient] z [MobileServiceAuthenticationProvider] wartość dostawcy. Na przykład poniższy kod inicjuje logowanie przepływu serwera za pomocą Facebooka.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Jeśli używasz dostawcy tożsamości innego niż Facebook, zmień wartość [MobileServiceAuthenticationProvider] na wartość dla dostawcy.

W przepływie serwera usługa Azure App Service zarządza przepływem uwierzytelniania OAuth, wyświetlając stronę logowania wybranego dostawcy.  Gdy dostawca tożsamości zwraca, usługa Azure App Service generuje token uwierzytelniania usługi App Service. [LoginAsync] Metoda zwraca [MobileServiceUser], który zapewnia zarówno [UserId] uwierzytelnionego użytkownika i [MobileServiceAuthenticationToken], jako token internetowy JSON (JWT). Ten token można zapisać w pamięci podręcznej i ponownie go używać, dopóki nie wygaśnie. Aby uzyskać więcej informacji, zobacz [Buforowanie tokenu uwierzytelniania](#caching).

### <a name="caching-the-authentication-token"></a><a name="caching"></a>Buforowanie tokenu uwierzytelniania
W niektórych przypadkach można uniknąć wywołania metody logowania po pierwszym pomyślnym uwierzytelnianiu, przechowując token uwierzytelniania od dostawcy.  Aplikacje Microsoft Store i platformy uniwersalnej systemu Windows mogą używać [funkcji PasswordVault] do buforowania bieżącego tokenu uwierzytelniania po pomyślnym zalogowaniu się w następujący sposób:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserId Wartość jest przechowywana jako UserName poświadczeń i token jest przechowywany jako hasło. W kolejnych uruchomieniach można sprawdzić **passwordVault** dla poświadczeń w pamięci podręcznej. W poniższym przykładzie użyto poświadczeń buforowanych po ich znalezieniu i w przeciwnym razie próbuje ponownie uwierzytelnić się za pomocą wewnętrznej bazy danych:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Po wylogowywanie się użytkownika, należy również usunąć zapisane poświadczenia, w następujący sposób:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Aplikacje platformy Xamarin używają interfejsów API [platformy Xamarin.Auth] do bezpiecznego przechowywania poświadczeń w **obiekcie Konto.** Na przykład przy użyciu tych interfejsów API zobacz plik kodu [AuthStore.cs] w [przykładzie udostępniania zdjęć ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Korzystając z uwierzytelniania zarządzanego przez klienta, można również buforować token dostępu uzyskany od dostawcy, takiego jak Facebook lub Twitter. Ten token można podać do żądania nowego tokenu uwierzytelniania z wewnętrznej bazy danych, w następujący sposób:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="push-notifications"></a><a name="pushnotifications"></a>Powiadomienia wypychane
Następujące tematy obejmują powiadomienia wypychane:

* [Zarejestruj się w celu otrzymywani powiadomień wypychanych](#register-for-push)
* [Uzyskiwanie identyfikatora SID pakietu Microsoft Store](#package-sid)
* [Zarejestruj się za pomocą szablonów międzyplatformowych](#register-xplat)

### <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Jak: Zarejestruj się w powiadomieniach wypychanych
Klient aplikacji mobilnych umożliwia rejestrowanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs. Podczas rejestracji można uzyskać dojście, które można uzyskać z usługi powiadomień wypychanych (PNS) specyficzne dla platformy. Następnie należy podać tę wartość wraz z tagami podczas tworzenia rejestracji. Poniższy kod rejestruje aplikację systemu Windows do powiadomień wypychanych w usłudze powiadomień systemu Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Jeśli naciskasz do usługi WNS, musisz [uzyskać identyfikator SID pakietu Microsoft Store](#package-sid).  Aby uzyskać więcej informacji na temat aplikacji systemu Windows, w tym sposobu rejestrowania się w celu rejestracji szablonów, zobacz [Dodawanie powiadomień wypychanych do aplikacji].

Żądanie tagów od klienta nie jest obsługiwane.  Żądania tagów są po cichu usuwane z rejestracji.
Jeśli chcesz zarejestrować urządzenie za pomocą tagów, utwórz niestandardowy interfejs API, który używa interfejsu API centrum powiadomień do przeprowadzenia rejestracji w Twoim imieniu.  Wywołanie niestandardowego interfejsu `RegisterNativeAsync()` API zamiast metody.

### <a name="how-to-obtain-a-microsoft-store-package-sid"></a><a name="package-sid"></a>Jak: Uzyskiwanie identyfikatora SID pakietu Microsoft Store
Identyfikator SID pakietu jest potrzebny do włączania powiadomień wypychanych w aplikacjach Microsoft Store.  Aby otrzymać identyfikator SID pakietu, zarejestruj aplikację w sklepie Microsoft Store.

Aby uzyskać tę wartość:

1. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy projekt aplikacji Microsoft Store, kliknij polecenie **Skojarzenie sklepu** > **ze Sklepem...**.
2. W kreatorze kliknij przycisk **Dalej**, zaloguj się za pomocą konta Microsoft, wpisz nazwę aplikacji w **pozycji Rezerwuj nową nazwę aplikacji,** a następnie kliknij pozycję **Zarezerwuj**.
3. Po pomyślnym utworzeniu rejestracji aplikacji wybierz nazwę aplikacji, kliknij przycisk **Dalej**, a następnie kliknij przycisk **Skojarz**.
4. Zaloguj się do [Centrum deweloperów systemu Windows] przy użyciu konta Microsoft. W **obszarze Moje aplikacje**kliknij utworzoną rejestrację aplikacji.
5. Kliknij pozycję**Tożsamość aplikacji** **do zarządzania aplikacjami,** > a następnie przewiń w dół, aby znaleźć **identyfikator SID pakietu**.

Wiele zastosowań identyfikatora SID pakietu traktuje go jako identyfikator URI, w którym to przypadku należy użyć *ms-app://* jako schematu. Zanotuj wersję identyfikatora SID pakietu utworzonego przez łączenie tej wartości jako prefiksu.

Aplikacje platformy Xamarin wymagają dodatkowego kodu, aby móc zarejestrować aplikację działającą na platformach iOS lub Android. Aby uzyskać więcej informacji, zobacz temat platformy:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>Jak: Rejestrowanie szablonów wypychanych w celu wysyłania powiadomień między platformami
Aby zarejestrować szablony, użyj `RegisterAsync()` metody z szablonami, w następujący sposób:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Szablony powinny `JObject` być typami i mogą zawierać wiele szablonów w następującym formacie JSON:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

Metoda **RegisterAsync()** akceptuje również płytki pomocnicze:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Wszystkie tagi są usuwane podczas rejestracji dla bezpieczeństwa. Aby dodać tagi do instalacji lub szablonów w instalacjach, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps].

Aby wysyłać powiadomienia z wykorzystaniem tych zarejestrowanych szablonów, zapoznaj się z [interfejsami API Centrum powiadomień].

## <a name="miscellaneous-topics"></a><a name="misc"></a>Różne tematy
### <a name="how-to-handle-errors"></a><a name="errors"></a>Jak: Obsługa błędów
Gdy wystąpi błąd w wewnętrznej bazy danych, sdk klienta podnosi `MobileServiceInvalidOperationException`.  W poniższym przykładzie pokazano, jak obsługiwać wyjątek, który jest zwracany przez wewnętrznej bazy danych:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Inny przykład radzenia sobie z warunkami błędu można znaleźć w [przykładzie plików aplikacji mobilnych]. [LoggingHandler] przykład zawiera obsługi delegata rejestrowania do rejestrowania żądań składanych do wewnętrznej bazy danych.

### <a name="how-to-customize-request-headers"></a><a name="headers"></a>Jak: Dostosowywanie nagłówków żądań
Aby obsługiwać określony scenariusz aplikacji, może być konieczne dostosowanie komunikacji z zapleczem aplikacji mobilnej. Na przykład można dodać niestandardowy nagłówek do każdego żądania wychodzącego, a nawet zmienić kody stanu odpowiedzi. Można użyć niestandardowego [delegatinghandler], jak w poniższym przykładzie:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Dodawanie uwierzytelniania do aplikacji]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Dodawanie powiadomień wypychanych do aplikacji]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Jak skonfigurować usługę App Service do logowania w usłudze Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[Mobilna usługaCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[Usługa mobilnaUthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[Gettable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[tworzy odwołanie do tabeli bez typu]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[UsuńSync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[Liczba tych]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[Wstawianiesync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync (LoginAsync)]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[WyszukiwanieAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Orderby]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[Orderbydescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[Readasync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Weź]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Wybierz]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Pominąć]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[Updateasync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Userid]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Gdzie]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Portal Azure]: https://portal.azure.com/
[Atrybut EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid (Guid.NewGuid)]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading (Ładowanie przyrostowe)]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centrum deweloperów systemu Windows]: https://dev.windows.com/overview
[Przekazanie ręczne]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault (Własnomić hasło]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Interfejsy API centrów powiadomień]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Przykład plików aplikacji mobilnych]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[Uchwyt rejestrowania]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Dokumentacja OData v3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
