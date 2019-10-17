---
title: Praca z zarządzaną biblioteką klienta App Service Mobile Apps | Microsoft Docs
description: Dowiedz się, jak używać biblioteki klienckiej .NET na potrzeby Azure App Service Mobile Apps z aplikacjami Windows i Xamarin.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 1a56c6eed8b6a78587ea272c44f01d16e8a2b88d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388714"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak używać zarządzanego klienta usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu zarządzanej biblioteki klienta Azure App Service Mobile Apps dla aplikacji dla systemu Windows i platformy Xamarin. Jeśli dopiero zaczynasz Mobile Apps, należy rozważyć pierwsze zakończenie samouczka [szybkiego startu platformy Azure Mobile Apps][1] . W tym przewodniku koncentrujemy się na zarządzanym przez klienta zestawie SDK. Aby dowiedzieć się więcej na temat zestawów SDK po stronie serwera dla Mobile Apps, zapoznaj się z dokumentacją [zestawu SDK programu .NET Server][2] lub [zestawu SDK serwera Node. js][3].

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dotycząca zestawu SDK klienta znajduje się tutaj: [odwołanie klienta platformy Azure Mobile Apps .NET][4].
Kilka przykładów klienta można również znaleźć w [repozytorium Azure-Samples][5]w witrynie GitHub.

## <a name="supported-platforms"></a>Obsługiwane platformy
Platforma .NET obsługuje następujące platformy:

* Wersje platformy Xamarin Android dla interfejsu API 19 do 24 (KitKat do Nougat)
* Wersje platformy Xamarin iOS dla systemu iOS w wersji 8,0 lub nowszej
* Platforma uniwersalna systemu Windows
* Windows Phone 8,1
* Windows Phone 8,0 z wyjątkiem aplikacji Silverlight

Uwierzytelnianie "serwer-przepływ" używa widoku WebView dla prezentowanego interfejsu użytkownika.  Jeśli urządzenie nie może przedstawić interfejsu użytkownika WebView, są konieczne inne metody uwierzytelniania.  Ten zestaw SDK jest w tym przypadku nieodpowiedni do typu czujka lub urządzeń z ograniczeniami.

## <a name="setup"></a>Instalacja i wymagania wstępne
Przyjęto założenie, że został już utworzony i opublikowany projekt zaplecza aplikacji mobilnej, który zawiera co najmniej jedną tabelę.  W kodzie użytym w tym temacie tabela ma nazwę `TodoItem` i zawiera następujące kolumny: `Id`, `Text` i `Complete`. Ta tabela jest tą samą tabelą utworzoną po zakończeniu [przewodnika Szybki Start dotyczącego usługi Azure Mobile Apps][1].

Odpowiadający wpisany typ po stronie klienta w C# programie jest następującą klasą:

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

[JsonPropertyAttribute][6] jest używany do definiowania mapowania *PropertyName* między polem klienta a polem tabeli.

Aby dowiedzieć się, jak tworzyć tabele w zapleczu Mobile Apps, zobacz [temat zestaw SDK programu .NET Server][7] lub [temat zestaw SDK serwera Node. js][8]. Jeśli zaplecze aplikacji mobilnej zostały utworzone w Azure Portal przy użyciu przewodnika Szybki Start, możesz również użyć ustawienia **łatwe tabele** w [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Instrukcje: Instalowanie pakietu zarządzanego zestawu SDK klienta
Użyj jednej z następujących metod, aby zainstalować pakiet zarządzanego zestawu SDK klienta dla Mobile Apps z programu [NuGet][9]:

* **Program Visual Studio** Kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Zarządzaj pakietami NuGet**, wyszukaj pakiet `Microsoft.Azure.Mobile.Client`, a następnie kliknij przycisk **Instaluj**.
* **Xamarin Studio** Kliknij prawym przyciskiem myszy projekt, kliknij polecenie **dodaj** > **Dodaj pakiety NuGet**, Wyszukaj pakiet `Microsoft.Azure.Mobile.Client`, a następnie kliknij przycisk **Dodaj pakiet**.

W głównym pliku działania Pamiętaj o dodaniu następującej instrukcji **using** :

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Pamiętaj, że wszystkie pakiety dla pomocy technicznej, do których odwołuje się projekt systemu Android, muszą mieć tę samą wersję. Zestaw SDK ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów obsługi, należy zainstalować ten pakiet bezpośrednio w wymaganej wersji, aby uniknąć konfliktów.

### <a name="symbolsource"></a>Instrukcje: korzystanie z symboli debugowania w programie Visual Studio
Symbole dla przestrzeni nazw Microsoft. Azure. Mobile są dostępne w witrynie [SymbolSource][10].  Zapoznaj się z [instrukcjami SymbolSource][11] , aby zintegrować SymbolSource z programem Visual Studio.

## <a name="create-client"></a>Tworzenie klienta Mobile Apps
Poniższy kod tworzy obiekt [MobileServiceClient][12] , który jest używany w celu uzyskania dostępu do zaplecza aplikacji mobilnej.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

W poprzednim kodzie Zastąp wartość `MOBILE_APP_URL` adresem URL zaplecza aplikacji mobilnej, która znajduje się w bloku dla zaplecza aplikacji mobilnej w [Azure Portal]. Obiekt MobileServiceClient musi być klasą pojedynczą.

## <a name="work-with-tables"></a>Pracuj z tabelami
W poniższej sekcji opisano sposób wyszukiwania i pobierania rekordów oraz modyfikowania danych w tabeli.  Omówiono następujące tematy:

* [Utwórz odwołanie do tabeli](#instantiating)
* [Dane zapytania](#querying)
* [Filtrowanie zwróconych danych](#filtering)
* [Sortuj zwrócone dane](#sorting)
* [Zwróć dane na stronach](#paging)
* [Wybierz określone kolumny](#selecting)
* [Wyszukiwanie rekordu według identyfikatora](#lookingup)
* [Postępowanie z niewpisanymi zapytaniami](#untypedqueries)
* [Wstawianie danych](#inserting)
* Aktualizowanie danych
* [Usuwanie danych](#deleting)
* [Rozwiązywanie konfliktów i współbieżność optymistyczna](#optimisticconcurrency)
* [Powiązanie z interfejsem użytkownika systemu Windows](#binding)
* [Zmiana rozmiaru strony](#pagesize)

### <a name="instantiating"></a>Instrukcje: Tworzenie odwołania do tabeli
Cały kod, który uzyskuje dostęp do danych w tabeli zaplecza lub modyfikuje je w ramach obiektu `MobileServiceTable`. Uzyskaj odwołanie do tabeli, wywołując metodę [GetTable] w następujący sposób:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Zwrócony obiekt używa określonego modelu serializacji. Obsługiwany jest również niewpisany model serializacji. Poniższy przykład [tworzy odwołanie do tabeli bez typu]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

W zapytaniach niez typem należy określić źródłowy ciąg zapytania OData.

### <a name="querying"></a>Instrukcje: wykonywanie zapytań dotyczących danych z aplikacji mobilnej
W tej sekcji opisano, jak wystawiać zapytania do zaplecza aplikacji mobilnej, która obejmuje następujące funkcje:

* [Filtrowanie zwróconych danych](#filtering)
* [Sortuj zwrócone dane](#sorting)
* [Zwróć dane na stronach](#paging)
* [Wybierz określone kolumny](#selecting)
* [Wyszukiwanie danych według identyfikatora](#lookingup)

> [!NOTE]
> Rozmiar strony oparty na serwerze jest wymuszany, aby zapobiec zwracaniu wszystkich wierszy.  Stronicowanie zachowuje domyślne żądania dla dużych zestawów danych, które nie mają negatywnego wpływu na usługę.  Aby zwrócić więcej niż 50 wierszy, użyj metody `Skip` i `Take`, jak opisano w [zwracanych danych na stronach](#paging).

### <a name="filtering"></a>Instrukcje: filtrowanie zwróconych danych
Poniższy kod ilustruje sposób filtrowania danych, dołączając klauzulę `Where` w zapytaniu. Zwraca wszystkie elementy z `todoTable`, których właściwość `Complete` jest równa `false`. Funkcja [Miejscu] stosuje predykat filtrowania wierszy do zapytania względem tabeli.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Identyfikator URI żądania wysłanego do zaplecza można wyświetlić przy użyciu oprogramowania do inspekcji komunikatów, takiego jak narzędzia deweloperskie przeglądarki lub [Fiddler]. Jeśli sprawdzasz identyfikator URI żądania, Zauważ, że ciąg zapytania jest modyfikowany:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

To żądanie OData jest tłumaczone na zapytanie SQL przez zestaw SDK serwera:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkcja, która jest przenoszona do metody `Where` może mieć dowolną liczbę warunków.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Ten przykład zostanie przetłumaczony na zapytanie SQL według zestawu SDK serwera:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

To zapytanie można również podzielić na wiele klauzul:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Dwie metody są równoważne i mogą być używane zamiennie.  Dawna opcja @ no__t-0oF łącząca wiele predykatów w jednym zapytaniu @ no__t-1Is bardziej zwarte i zalecane.

Klauzula `Where` obsługuje operacje, które są tłumaczone na podzestaw OData. Operacje obejmują:

* Operatory relacyjne (= =,! =, <, < =, >, > =),
* Operatory arytmetyczne (+,-,/, *,%),
* Precyzja liczb (Math. Floor, Math. sufit),
* Funkcje ciągów (długość, podciąg, Zastąp, IndexOf, StartsWith, EndsWith),
* Właściwości daty (rok, miesiąc, dzień, godzina, minuta, sekunda)
* Właściwości dostępu obiektu i
* Wyrażenia łączące dowolne z tych operacji.

Biorąc pod uwagę sposób obsługi zestawu SDK serwera, można rozważyć [Dokumentacja OData V3].

### <a name="sorting"></a>Instrukcje: sortowanie zwracanych danych
Poniższy kod ilustruje sposób sortowania danych przez dołączenie do zapytania funkcji [OrderBy] lub [OrderByDescending] . Zwraca elementy z `todoTable` posortowane rosnąco według pola `Text`.

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

### <a name="paging"></a>Instrukcje: Zwracanie danych na stronach
Domyślnie zaplecze zwraca tylko pierwsze 50 wierszy. Można zwiększyć liczbę zwracanych wierszy, wywołując metodę [Czasochłonn] . Użyj `Take` wraz z metodą [Skocz] , aby zażądać określonej "strony" całkowitego zestawu danych zwróconego przez zapytanie. Następujące zapytanie, gdy wykonywane, zwraca trzy pierwsze elementy z tabeli.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Następujące poprawione zapytanie pomija pierwsze trzy wyniki i zwraca następne trzy wyniki. To zapytanie tworzy drugą "stronę" danych, gdzie rozmiar strony to trzy elementy.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Metoda [IncludeTotalCount] żąda całkowitej liczby *wszystkich* rekordów, które zostałyby zwrócone, ignorując określoną klauzulę stronicowania/ograniczania:

```csharp
query = query.IncludeTotalCount();
```

W aplikacji Real World można używać zapytań podobnych do poprzedniego przykładu z kontrolką modułu stronicowania lub porównywalnym interfejsem użytkownika w celu nawigowania między stronami.

> [!NOTE]
> Aby zastąpić limit 50 wierszy w zapleczu aplikacji mobilnej, należy również zastosować [EnableQueryAttribute] do publicznej metody get i określić zachowanie stronicowania. Po zastosowaniu do metody, następujące ustawia maksymalną liczbę zwracanych wierszy do 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Instrukcje: Wybieranie określonych kolumn
Możesz określić zestaw właściwości do uwzględnienia w wynikach przez dodanie klauzuli [Wybierz] do zapytania. Na przykład poniższy kod pokazuje, jak wybrać tylko jedno pole, a także jak zaznaczyć i sformatować kilka pól:

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

Wszystkie funkcje opisane do tej pory są dodatkiem, dzięki czemu możemy kontynuować łączenie się z nimi. Każde wywołanie łańcuchowe ma wpływ na więcej zapytań. Jeden przykład:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Instrukcje: wyszukiwanie danych według identyfikatora
Funkcja [LookupAsync] może służyć do wyszukiwania obiektów z bazy danych o określonym identyfikatorze.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Instrukcje: wykonywanie zapytań nietypu
Podczas wykonywania zapytania przy użyciu obiektu tabeli nietypu, należy jawnie określić ciąg zapytania OData, wywołując [ReadAsync], jak w poniższym przykładzie:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Uzyskasz wartości JSON, których można użyć jak zbioru właściwości. Aby uzyskać więcej informacji na temat JToken i Newtonsoft Json.NET, zobacz witrynę [JSON.NET] .

### <a name="inserting"></a>Instrukcje: Wstawianie danych do zaplecza aplikacji mobilnej
Wszystkie typy klientów muszą zawierać składową o nazwie **ID**, która jest domyślnie ciągiem. Ten **Identyfikator** jest wymagany do wykonywania operacji CRUD i synchronizacji w trybie offline. Poniższy kod ilustruje sposób używania metody [InsertAsync] do wstawiania nowych wierszy do tabeli. Parametr zawiera dane, które mają zostać wstawione jako obiekt .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Jeśli unikatowa wartość identyfikatora niestandardowego nie jest uwzględniona w `todoItem` podczas wstawiania, na serwerze zostanie wygenerowany identyfikator GUID.
Wygenerowany identyfikator można pobrać, sprawdzając obiekt po powrocie wywołania.

Aby wstawić dane, które nie są typu, możesz skorzystać z Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Oto przykład użycia adresu e-mail jako unikatowego identyfikatora ciągu:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Praca z wartościami identyfikatora
Mobile Apps obsługuje unikatowe wartości ciągu niestandardowego dla kolumny **Identyfikator** tabeli. Wartość ciągu umożliwia aplikacjom używanie wartości niestandardowych, takich jak adresy e-mail lub nazwy użytkowników dla identyfikatora.  Identyfikatory ciągów zapewniają następujące korzyści:

* Identyfikatory są generowane bez przeprowadzenia rundy do bazy danych.
* Rekordy są łatwiejsze do scalenia z różnych tabel lub baz danych.
* Wartości identyfikatorów można zintegrować z logiką aplikacji.

Jeśli wartość identyfikatora ciągu nie jest ustawiona w wstawionym rekordzie, zaplecze aplikacji mobilnej generuje unikatową wartość dla tego identyfikatora. Możesz użyć metody [GUID. NewGuid] , aby wygenerować własne wartości identyfikatora na kliencie lub w zapleczu.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Instrukcje: modyfikowanie danych w zapleczu aplikacji mobilnej
Poniższy kod ilustruje sposób używania metody [UpdateAsync] do aktualizowania istniejącego rekordu o takim samym identyfikatorze z nowymi informacjami. Parametr zawiera dane, które mają zostać zaktualizowane jako obiekt .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Aby zaktualizować dane, które nie są typu, można skorzystać z [JSON.NET] w następujący sposób:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Podczas wykonywania aktualizacji należy określić pole `id`. Zaplecze używa pola `id` w celu określenia wiersza do zaktualizowania. Pole `id` można uzyskać z wyniku wywołania `InsertAsync`. @No__t-0 jest zgłaszane w przypadku próby zaktualizowania elementu bez podawania wartości `id`.

### <a name="deleting"></a>Instrukcje: usuwanie danych w zapleczu aplikacji mobilnej
Poniższy kod ilustruje sposób używania metody [DeleteAsync] w celu usunięcia istniejącego wystąpienia. Wystąpienie jest identyfikowane przez pole `id` ustawione dla `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Aby usunąć dane, które nie są typu, można skorzystać z Json.NET w następujący sposób:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Po wprowadzeniu żądania usunięcia należy określić identyfikator. Inne właściwości nie są przenoszone do usługi lub są ignorowane w usłudze. Wynik wywołania `DeleteAsync` jest zazwyczaj `null`. Identyfikator do przekazania można uzyskać z wyniku wywołania `InsertAsync`. W przypadku próby usunięcia elementu bez określenia pola `id` jest generowany `MobileServiceInvalidOperationException`.

### <a name="optimisticconcurrency"></a>Instrukcje: użycie optymistycznej współbieżności do rozwiązywania konfliktów
Co najmniej dwa komputery klienckie mogą zapisywać zmiany w tym samym elemencie w tym samym czasie. Bez wykrywania konfliktów Ostatni zapis spowoduje zastąpienie wszystkich poprzednich aktualizacji. **Optymistyczna kontrola współbieżności** zakłada, że każda transakcja może zostać zatwierdzona i w związku z tym nie używa żadnego blokowania zasobów.  Przed zatwierdzeniem transakcji optymistyczne kontrolki współbieżności sprawdzają, czy żadne inne transakcje nie spowodowały modyfikacji danych. Jeśli dane zostały zmodyfikowane, transakcja zatwierdzania jest wycofywana.

Mobile Apps obsługuje optymistyczną kontrolę współbieżności, śledząc zmiany poszczególnych elementów za pomocą kolumny właściwości systemu `version` zdefiniowanej dla każdej tabeli w zapleczu aplikacji mobilnej. Za każdym razem, gdy rekord jest aktualizowany, Mobile Apps ustawia właściwość `version` dla tego rekordu na nową wartość. Podczas każdego żądania aktualizacji Właściwość `version` rekordu zawartego w żądaniu jest porównywana z tą samą właściwością rekordu na serwerze. Jeśli wersja przeniesiona z żądaniem nie jest zgodna z zapleczem, Biblioteka klienta zgłasza wyjątek `MobileServicePreconditionFailedException<T>`. Typ zawarty w wyjątku jest rekordem z zaplecza zawierającego wersje serwerów rekordu. Następnie aplikacja może użyć tych informacji, aby zdecydować, czy ponownie wykonać żądanie aktualizacji z poprawną wartością `version` z zaplecza, aby zatwierdzić zmiany.

Zdefiniuj kolumnę klasy tabeli dla właściwości systemu `version`, aby umożliwić optymistyczną współbieżność. Na przykład:

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

Aplikacje korzystające z tabel nieopartych na typie, które umożliwiają optymistyczną współbieżność, ustawiając flagę `Version` na `SystemProperties` tabeli w następujący sposób.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Oprócz włączenia optymistycznej współbieżności, należy również przechwycić wyjątek `MobileServicePreconditionFailedException<T>` w kodzie podczas wywoływania [UpdateAsync].  Rozwiąż konflikt, stosując poprawną `version` do zaktualizowanego rekordu i Wywołaj [UpdateAsync] z rozwiązanym rekordem. Poniższy kod ilustruje sposób rozwiązywania konfliktu zapisu po wykryciu:

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

Aby uzyskać więcej informacji, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps] .

### <a name="binding"></a>Instrukcje: powiązywanie danych Mobile Apps z interfejsem użytkownika systemu Windows
W tej sekcji przedstawiono sposób wyświetlania zwracanych obiektów danych przy użyciu elementów interfejsu użytkownika w aplikacji systemu Windows.  Poniższy przykładowy kod wiąże się ze źródłem listy z zapytaniem dotyczącym niekompletnych elementów. [MobileServiceCollection] tworzy kolekcję powiązań obsługującą Mobile Apps.

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

Niektóre kontrolki w zarządzanym środowisku uruchomieniowym obsługują interfejs o nazwie [ISupportIncrementalLoading]. Ten interfejs umożliwia kontrolki żądanie dodatkowych danych podczas przewijania użytkownika. Istnieje Wbudowana obsługa tego interfejsu dla aplikacji uniwersalnych systemu Windows za pośrednictwem usługi [MobileServiceIncrementalLoadingCollection], która automatycznie obsługuje wywołania z kontrolek. Użyj `MobileServiceIncrementalLoadingCollection` w aplikacjach systemu Windows w następujący sposób:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Aby użyć nowej kolekcji dla aplikacji Windows Phone 8 i "Silverlight", użyj metod rozszerzenia `ToCollection` w `IMobileServiceTableQuery<T>` i `IMobileServiceTable<T>`. Aby załadować dane, wywołaj `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Gdy używasz kolekcji utworzonej przez wywołanie `ToCollectionAsync` lub `ToCollection`, uzyskasz kolekcję, którą można powiązać z kontrolkami interfejsu użytkownika.  Ta kolekcja jest oparta na stronicowaniu.  Ponieważ kolekcja ładuje dane z sieci, ładowanie czasami kończy się niepowodzeniem. Aby obsłużyć takie niepowodzenia, Zastąp metodę `OnException` na `MobileServiceIncrementalLoadingCollection` w celu obsługi wyjątków wynikających z wywołań `LoadMoreItemsAsync`.

Zastanów się, czy tabela zawiera wiele pól, ale w kontrolce chcesz wyświetlić tylko niektóre z nich. Aby wybrać konkretne kolumny, które mają być wyświetlane w interfejsie użytkownika, możesz użyć wskazówek w poprzedniej sekcji "[Wybierz konkretne kolumny](#selecting)".

### <a name="pagesize"></a>Zmień rozmiar strony
Usługa Azure Mobile Apps domyślnie zwraca maksymalnie 50 elementów dla żądania.  Rozmiar stronicowania można zmienić przez zwiększenie maksymalnego rozmiaru strony zarówno na kliencie, jak i na serwerze.  Aby zwiększyć żądany rozmiar strony, określ `PullOptions` podczas korzystania z `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Przy założeniu, że `PageSize` jest równa lub większa niż 100 na serwerze, żądanie zwróci do 100 elementów.

## <a name="#offlinesync"></a>Pracuj z tabelami trybu offline
Tabele trybu offline używają lokalnego magazynu programu SQLite do przechowywania danych do użycia w trybie offline.  Wszystkie operacje na tabeli są wykonywane względem lokalnego magazynu oprogramowania SQLite zamiast zdalnego magazynu serwerów.  Aby utworzyć tabelę w trybie offline, najpierw przygotuj projekt:

1. W programie Visual Studio kliknij prawym przyciskiem myszy rozwiązanie > **Zarządzanie pakietami NuGet dla rozwiązania...** , a następnie wyszukaj i zainstaluj pakiet NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** dla wszystkich projektów w rozwiązaniu.
2. Obowiązkowe Aby zapewnić obsługę urządzeń z systemem Windows, zainstaluj jeden z następujących pakietów środowiska uruchomieniowego oprogramowania SQLite:

   * **Windows 8.1 środowiska uruchomieniowego:** Zainstaluj program [SQLite dla Windows 8.1][3].
   * **Windows Phone 8,1:** Zainstaluj program [SQLite dla Windows Phone 8,1][4].
   * **Platforma uniwersalna systemu Windows** Zainstaluj program [SQLite dla uniwersalnego systemu Windows][5].
3. (Opcjonalnie). W przypadku urządzeń z systemem Windows kliknij pozycję **odwołuje**się  > **Dodaj odwołanie...** , rozwiń węzeł **rozszerzenia**> folderu **systemu Windows** , a następnie Włącz odpowiedni zestaw **oprogramowania SQLite dla systemu Windows** SDK oraz **środowisko uruchomieniowe Visual C++ 2013 dla zestawu Windows** SDK.
    Nazwy zestawów SDK oprogramowania SQLite różnią się nieco w zależności od platformy systemu Windows.

Aby można było utworzyć odwołanie do tabeli, należy przygotować magazyn lokalny:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicjowanie magazynu jest zwykle wykonywane natychmiast po utworzeniu klienta.  **OfflineDbPath** powinna być nazwą pliku odpowiednią do użycia na wszystkich obsługiwanych platformach.  Jeśli ścieżka jest w pełni kwalifikowaną ścieżką (oznacza to, że zaczyna się od ukośnika), ta ścieżka jest używana.  Jeśli ścieżka nie jest w pełni kwalifikowana, plik zostanie umieszczony w lokalizacji specyficznej dla platformy.

* W przypadku urządzeń z systemem iOS i Android domyślną ścieżką jest folder "pliki osobiste".
* W przypadku urządzeń z systemem Windows domyślną ścieżką jest folder "AppData" specyficzny dla aplikacji.

Odwołanie do tabeli można uzyskać za pomocą metody `GetSyncTable<>`:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Nie musisz uwierzytelniać się w celu korzystania z tabeli trybu offline.  Musisz uwierzytelnić się tylko w przypadku komunikacji z usługą zaplecza.

### <a name="syncoffline"></a>Synchronizowanie tabeli w trybie offline
Tabele w trybie offline nie są domyślnie synchronizowane z zapleczem.  Synchronizacja jest podzielona na dwie części.  Można wypchnąć zmiany oddzielnie od pobierania nowych elementów.  Poniżej przedstawiono typową metodę synchronizacji:

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

Jeśli pierwszy argument `PullAsync` ma wartość null, synchronizacja przyrostowa nie zostanie użyta.  Każda operacja synchronizacji pobiera wszystkie rekordy.

Zestaw SDK wykonuje niejawną `PushAsync()` przed ściąganiem rekordów.

Obsługa konfliktów odbywa się na metodzie `PullAsync()`.  Konflikty mogą być rozwiązywane w taki sam sposób jak w przypadku tabel online.  Konflikt jest tworzony, gdy zostanie wywołane `PullAsync()` zamiast podczas wstawiania, aktualizowania lub usuwania. W przypadku wystąpienia wielu konfliktów są one powiązane z jednym MobileServicePushFailedException.  Obsługa każdej awarii osobno.

## <a name="#customapi"></a>Współpraca z niestandardowym interfejsem API
Niestandardowy interfejs API umożliwia Definiowanie niestandardowych punktów końcowych, które uwidaczniają funkcjonalność serwera, która nie jest mapowana na operację wstawiania, aktualizowania, usuwania lub odczytu. Korzystając z niestandardowego interfejsu API, można mieć większą kontrolę nad wiadomościami, w tym odczytywanie i Ustawianie nagłówków wiadomości HTTP i Definiowanie formatu treści wiadomości innego niż JSON.

Możesz wywołać niestandardowy interfejs API, wywołując jedną z metod [InvokeApiAsync] na kliencie. Na przykład poniższy wiersz kodu wysyła żądanie POST do interfejsu API **completeAll** w zapleczu:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ten formularz jest typem wywołania metody i wymaga zdefiniowania typu zwracanego przez **MarkAllResult** . Obsługiwane są zarówno typy, jak i nietypu metody.

Metoda InvokeApiAsync () dołącza element "/API/" do interfejsu API, który ma zostać wywołany, chyba że interfejs API zaczyna się od znaku "/".
Na przykład:

* `InvokeApiAsync("completeAll",...)` wywołań/api/completeAll w zapleczu
* `InvokeApiAsync("/.auth/me",...)` wywołań/.auth/Me w zapleczu

Można użyć InvokeApiAsync do wywołania dowolnego WebAPI, w tym tych interfejsów API, które nie są zdefiniowane za pomocą usługi Azure Mobile Apps.  W przypadku używania InvokeApiAsync () odpowiednie nagłówki, w tym nagłówki uwierzytelniania, są wysyłane wraz z żądaniem.

## <a name="authentication"></a>Uwierzytelnianie użytkowników
Mobile Apps obsługuje uwierzytelnianie i Autoryzowanie użytkowników aplikacji przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, konta Microsoft, Twitter i Azure Active Directory. Możesz ustawić uprawnienia dla tabel, aby ograniczyć dostęp do określonych operacji tylko do użytkowników uwierzytelnionych. Aby zaimplementować reguły autoryzacji w skryptach serwera, można także użyć tożsamości uwierzytelnionych użytkowników. Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [Dodawanie uwierzytelniania do aplikacji].

Obsługiwane są dwa przepływy uwierzytelniania: przepływ zarządzany *przez klienta* i *serwer* . Przepływ zarządzany przez serwer zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się on na interfejsie uwierzytelniania sieci Web dostawcy. Przepływ zarządzany przez klienta umożliwia dokładniejszą integrację z funkcjami specyficznymi dla konkretnego urządzenia, zależnie od specyficznych dla dostawcy zestawów SDK specyficznych dla urządzeń.

> [!NOTE]
> Zalecamy używanie przepływu zarządzanego przez klienta w aplikacjach produkcyjnych.

Aby skonfigurować uwierzytelnianie, należy zarejestrować aplikację z co najmniej jednym dostawcą tożsamości.  Dostawca tożsamości generuje identyfikator klienta i klucz tajny klienta dla aplikacji.  Te wartości są następnie ustawiane w zapleczu, aby umożliwić Azure App Service uwierzytelnianie/autoryzację.  Aby uzyskać więcej informacji, postępuj zgodnie ze szczegółowymi instrukcjami w samouczku [Dodawanie uwierzytelniania do aplikacji].

W tej sekcji omówiono następujące tematy:

* [Uwierzytelnianie zarządzane przez klienta](#clientflow)
* [Uwierzytelnianie zarządzane przez serwer](#serverflow)
* [Buforowanie tokenu uwierzytelniania](#caching)

### <a name="clientflow"></a>Uwierzytelnianie zarządzane przez klienta
Twoja aplikacja może niezależnie skontaktować się z dostawcą tożsamości, a następnie podać zwracany token podczas logowania z zapleczem. Ten przepływ klienta umożliwia korzystanie z logowania jednokrotnego dla użytkowników lub pobieranie dodatkowych danych użytkownika od dostawcy tożsamości. Uwierzytelnianie przepływu klienta jest preferowane przy użyciu przepływu serwera, ponieważ zestaw SDK dostawcy tożsamości zapewnia bardziej natywny sposób działania środowiska użytkownika i umożliwia dodatkowe dostosowanie.

Przykłady są dostępne dla następujących wzorców uwierzytelniania w przepływie klienta:

* [Active Directory Authentication Library](#adal)
* [Facebook lub Google](#client-facebook)

#### <a name="adal"></a>Uwierzytelnianie użytkowników za pomocą Active Directory Authentication Library
Za pomocą Active Directory Authentication Library (ADAL) można inicjować uwierzytelnianie użytkowników z poziomu klienta przy użyciu uwierzytelniania Azure Active Directory.

1. Skonfiguruj zaplecze aplikacji mobilnej na potrzeby logowania do usługi AAD, wykonując czynności opisane w samouczku [Jak skonfigurować App Service logowania Active Directory] . Pamiętaj, aby ukończyć opcjonalny krok rejestracji natywnej aplikacji klienckiej.
2. W programie Visual Studio lub Xamarin Studio Otwórz projekt i Dodaj odwołanie do pakietu NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory`. Podczas wyszukiwania należy uwzględnić wersje wstępne.
3. Dodaj następujący kod do aplikacji zgodnie z platformą, z której korzystasz. W każdym z nich należy wykonać następujące zamiany:

   * Zastąp **ciąg INSERT-Authority-** in nazwą dzierżawy, w której została zainicjowana aplikacja. Format powinien być https://login.microsoftonline.com/contoso.onmicrosoft.com. Tę wartość można skopiować z karty domena w Azure Active Directory w [Azure Portal].
   * Zastąp wartość **INSERT-Resource-ID w tym miejscu** identyfikatorem klienta dla zaplecze aplikacji mobilnej. Identyfikator klienta można uzyskać z karty **Zaawansowane** w obszarze **Ustawienia Azure Active Directory** w portalu.
   * Zastąp **ciąg INSERT-Client-ID w tym miejscu** identyfikatorem klienta skopiowanym z natywnej aplikacji klienckiej.
   * Zastąp ciąg **INSERT-redirect-URI — tutaj** z punktem końcowym */.auth/login/done* Twojej witryny przy użyciu schematu https. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done* .

     Kod wymagany dla każdej platformy:

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

#### <a name="client-facebook"></a>Logowanie jednokrotne przy użyciu tokenu z serwisu Facebook lub Google
Możesz użyć przepływu klienta, jak pokazano w tym fragmencie kodu w serwisie Facebook lub Google.

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

### <a name="serverflow"></a>Uwierzytelnianie zarządzane przez serwer
Po zarejestrowaniu dostawcy tożsamości Wywołaj metodę [LoginAsync] na [MobileServiceClient] przy użyciu wartości [MobileServiceAuthenticationProvider] dostawcy. Na przykład poniższy kod inicjuje logowanie przepływu serwera przy użyciu usługi Facebook.

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

Jeśli używasz dostawcy tożsamości innego niż Facebook, Zmień wartość [MobileServiceAuthenticationProvider] na wartość dla dostawcy.

W przepływie serwera Program Azure App Service zarządza przepływem uwierzytelniania OAuth przez wyświetlenie strony logowania wybranego dostawcy.  Gdy dostawca tożsamości zwróci wartość, Azure App Service generuje token uwierzytelniania App Service. Metoda [LoginAsync] zwraca [MobileServiceUser], który zapewnia zarówno [Nazwa] uwierzytelnionego, jak i [MobileServiceAuthenticationToken], jako token sieci Web JSON (JWT). Ten token można zapisać w pamięci podręcznej i ponownie go używać, dopóki nie wygaśnie. Aby uzyskać więcej informacji, zobacz [buforowanie tokenu uwierzytelniania](#caching).

### <a name="caching"></a>Buforowanie tokenu uwierzytelniania
W niektórych przypadkach można uniknąć wywołania metody logowania po pierwszym pomyślnym uwierzytelnieniu przez zapisanie tokenu uwierzytelniania od dostawcy.  Aplikacje Microsoft Store i platformy UWP mogą używać [PasswordVault] do buforowania bieżącego tokenu uwierzytelniania po pomyślnym zalogowaniu się w następujący sposób:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Wartość identyfikatora użytkownika jest przechowywana jako nazwa użytkownika poświadczenia, a token jest przechowywany jako hasło. W kolejnych uruchomieniach możesz sprawdzić **PasswordVault** , aby uzyskać poświadczenia w pamięci podręcznej. W poniższym przykładzie podczas ich znajdowania są stosowane buforowane poświadczenia, a w przeciwnym razie próby uwierzytelnienia ponownie z zapleczem:

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

Po wylogowaniu użytkownika należy również usunąć przechowywane poświadczenia w następujący sposób:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Aplikacje Xamarin używają interfejsów API platformy [Xamarin. auth] do bezpiecznego przechowywania poświadczeń w obiekcie **konta** . Przykład korzystania z tych interfejsów API można znaleźć w pliku [AuthStore.cs] Code w [próbce udostępniania zdjęć ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

W przypadku korzystania z uwierzytelniania zarządzanego przez klienta można również buforować token dostępu uzyskany od dostawcy, takiego jak Facebook lub Twitter. Ten token może zostać dostarczony w celu zażądania nowego tokenu uwierzytelniania z zaplecza w następujący sposób:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Powiadomienia wypychane
W poniższych tematach omówiono powiadomienia wypychane:

* [Zarejestruj się w celu otrzymywania powiadomień wypychanych](#register-for-push)
* [Uzyskaj identyfikator SID pakietu Microsoft Store](#package-sid)
* [Rejestrowanie w szablonach dla wielu platform](#register-xplat)

### <a name="register-for-push"></a>Instrukcje: rejestrowanie w celu otrzymywania powiadomień wypychanych
Klient Mobile Apps umożliwia rejestrowanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs. Podczas rejestrowania otrzymujesz uchwyt uzyskany z usługi powiadomień wypychanych (PNS) specyficznych dla platformy. Następnie należy podać tę wartość wraz ze wszystkimi tagami podczas tworzenia rejestracji. Poniższy kod rejestruje aplikację systemu Windows na potrzeby powiadomień wypychanych za pomocą usługi powiadomień systemu Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

W przypadku wypychania do WNS należy [uzyskać Microsoft Store identyfikator SID pakietu](#package-sid).  Aby uzyskać więcej informacji o aplikacjach systemu Windows, w tym o sposobie rejestrowania rejestracji szablonów, zobacz [Dodawanie powiadomień wypychanych do aplikacji].

Żądanie tagów od klienta nie jest obsługiwane.  Żądania tagów zostały dyskretnie usunięte z rejestracji.
Jeśli chcesz zarejestrować urządzenie ze znacznikami, Utwórz niestandardowy interfejs API, który używa interfejsu API Notification Hubs do przeprowadzenia rejestracji w Twoim imieniu.  Wywoływanie niestandardowego interfejsu API zamiast metody `RegisterNativeAsync()`.

### <a name="package-sid"></a>Instrukcje: uzyskiwanie identyfikatora SID pakietu Microsoft Store
Aby włączyć powiadomienia wypychane w aplikacjach Microsoft Store, wymagany jest identyfikator SID pakietu.  Aby odebrać identyfikator SID pakietu, zarejestruj swoją aplikację w Microsoft Store.

Aby uzyskać tę wartość:

1. W programie Visual Studio Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji Microsoft Store, kliknij pozycję **zapisz** > **Skojarz aplikację ze sklepem...** .
2. W Kreatorze kliknij przycisk **dalej**, zaloguj się przy użyciu konto Microsoft, wpisz nazwę aplikacji w polu **Zarezerwuj nową nazwę aplikacji**, a następnie kliknij pozycję **Zarezerwuj**.
3. Po pomyślnym utworzeniu rejestracji aplikacji wybierz nazwę aplikacji, kliknij przycisk **dalej**, a następnie kliknij pozycję **Skojarz**.
4. Zaloguj się do [Centrum deweloperów systemu Windows] przy użyciu konta Microsoft. W obszarze **Moje aplikacje**kliknij utworzoną rejestrację aplikacji.
5. Kliknij pozycję **Zarządzanie aplikacjami** > **tożsamość aplikacji**, a następnie przewiń w dół, aby znaleźć **Identyfikator SID pakietu**.

Wiele użycia identyfikatora SID pakietu traktuje go jako identyfikator URI, w takim przypadku należy użyć *MS-App://* jako schematu. Zanotuj wersję identyfikatora SID pakietu utworzoną przez połączenie tej wartości jako prefiksu.

Aplikacje Xamarin wymagają dodatkowego kodu, aby można było zarejestrować aplikację działającą na platformach z systemem iOS lub Android. Aby uzyskać więcej informacji, zobacz temat dotyczący Twojej platformy:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Instrukcje: rejestrowanie szablonów wypychania do wysyłania powiadomień na wielu platformach
Aby zarejestrować szablony, użyj metody `RegisterAsync()` z szablonami w następujący sposób:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Szablony powinny mieć `JObject` typów i mogą zawierać wiele szablonów w następującym formacie JSON:

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

Metoda **RegisterAsync ()** również akceptuje kafelki pomocnicze:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Wszystkie Tagi są odrzucane podczas rejestracji w celu zabezpieczenia. Aby dodać tagi do instalacji lub szablonów w ramach instalacji, zobacz [Pracuj z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps].

Aby wysyłać powiadomienia przy użyciu tych zarejestrowanych szablonów, zapoznaj się z [Interfejsy API Notification Hubs].

## <a name="misc"></a>Różne tematy
### <a name="errors"></a>Instrukcje: obsługa błędów
Po wystąpieniu błędu w zapleczu zestaw SDK klienta zgłasza `MobileServiceInvalidOperationException`.  Poniższy przykład pokazuje, jak obsłużyć wyjątek, który jest zwracany przez zaplecze:

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

Innym przykładem postępowania z warunkami błędów można znaleźć w [Przykład plików Mobile Apps]. Przykład [LoggingHandler] zapewnia procedurę obsługi delegowania rejestrowania w celu rejestrowania żądań wykonywanych w zapleczu.

### <a name="headers"></a>Instrukcje: Dostosowywanie nagłówków żądań
Aby zapewnić obsługę określonego scenariusza aplikacji, może być konieczne dostosowanie komunikacji z zapleczem aplikacji mobilnej. Na przykład możesz chcieć dodać nagłówek niestandardowy do każdego żądania wychodzącego, a nawet zmienić kody stanu odpowiedzi. Możesz użyć niestandardowego [DelegatingHandler], jak w poniższym przykładzie:

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
[Jak skonfigurować App Service logowania Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[tworzy odwołanie do tabeli bez typu]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Czasochłonn]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Wybierz]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Skocz]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Nazwa]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Miejscu]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[GUID. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centrum deweloperów systemu Windows]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Interfejsy API Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Przykład plików Mobile Apps]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Dokumentacja OData V3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
