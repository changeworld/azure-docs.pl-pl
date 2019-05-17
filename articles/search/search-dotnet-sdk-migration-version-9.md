---
title: Uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 9 — usługa Azure Search
description: Migrowanie kodu do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 9 ze starszych wersji. Poznaj nowe funkcje i zmiany kodu, które są wymagane.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a59deed4ac0cec669ddc5e0335f7274586c702e8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541759"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>Uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 9

Jeśli używasz wersji 7.0 preview lub starszy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnić aplikację do używania w wersji 9.

> [!NOTE]
> Jeśli chcesz użyć wersji 8.0-preview, można obliczyć wartości funkcji, które nie są jeszcze ogólnie dostępne, można również wykonać instrukcje w tym artykule, aby uaktualnić do wersji zapoznawczej 8.0 z wcześniejszych wersji.

Aby uzyskać ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [jak używać usługi Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Zestaw .NET SDK usługi Azure Search w wersji 9 zawiera wiele zmian z wcześniejszych wersji. Niektóre z nich są istotne zmiany, ale powinien wymagają one tylko względnie niewielkich zmian w kodzie. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) zawiera instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 4.0 preview lub starszej, należy najpierw uaktualnić do wersji 5 i dokona uaktualnienia do wersji 9. Zobacz [uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 5](search-dotnet-sdk-migration-version-5.md) instrukcje.
>
> Wystąpienia usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Można użyć wersji, gdy nie jest już najnowsze, ale zaleca się przeprowadzenie migracji kodu, aby użyć najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru api-version. Korzystając z zestawu .NET SDK, wersję zestawu SDK, w przypadku używania określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszy zestaw SDK, można nadal uruchomić ten kod bez konieczności wprowadzania zmian, nawet wtedy, gdy usługa zostanie uaktualniony do nowszej wersji interfejsu API do obsługi.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Co nowego w wersji 9
R jest przeznaczony dla zestawu .NET SDK usługi Azure Search w wersji 9 ogólnie dostępnej wersji Azure Search REST API, w szczególności 2019-05-06. Dzięki temu można korzystać z nowych funkcji usługi Azure Search z poziomu aplikacji .NET, w tym następujące:

* [Usługa cognitive search](cognitive-search-concept-intro.md) jest funkcją sztucznej Inteligencji w usłudze Azure Search, używany do wyodrębniania tekstu z obrazów, obiekty BLOB i innych źródeł danych bez struktury — wzbogaca zawartość, aby wprowadzić więcej można wyszukiwać w indeksie usługi Azure Search.
* Obsługa [typy złożone](search-howto-complex-data-types.md) pozwala użytkownikom na modelowanie niemal wszystkich zagnieżdżonych strukturę JSON do indeksu usługi Azure Search.
* [Autouzupełnianie](search-autocomplete-tutorial.md) stanowi alternatywę dla **sugerowanej** API wykonywania wyszukiwania jako użytkownik typ-zachowanie. Autouzupełnianie "zakończy się" wyraz lub frazę, która obecnie wpisywany przez użytkownika.
* [Tryb analizy JsonLines](search-howto-index-json-blobs.md)częścią usługi Azure Blob indeksowania, tworzy jeden wyszukiwania dokumentów na jednostkę JSON, oddzielonych znakami nowego wiersza.

### <a name="new-preview-features-in-version-80-preview"></a>Nowe funkcje w wersji zapoznawczej w wersji 8.0-preview
W wersji 8.0 — wersja zapoznawcza zestawu .NET SDK usługi Azure Search jest przeznaczona dla interfejsu API w wersji 2017-11-11-Preview. Ta wersja zawiera te same funkcje w wersji 9, a także:

* [Klucze szyfrowania zarządzanego przez klienta](search-security-manage-encryption-keys.md) po stronie usługi szyfrowania podczas spoczynku to nowa funkcja w wersji zapoznawczej. Oprócz wbudowanych szyfrowanie w spoczynku zarządzany przez firmę Microsoft można zastosować dodatkową warstwę, gdzie jesteś jedynym właścicielem kluczy szyfrowania.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety i ich zależności, należy ponownie skompilować projekt. W zależności od struktury kodu jego może odbudować pomyślnie. Jeśli tak, jesteś gotowy!

Jeśli kompilacja zakończy się niepowodzeniem, należy rozwiązać każdy błąd kompilacji. Zobacz [przełomowe zmiany w wersji 9](#ListOfChanges) dla szczegółowe informacje na temat sposobu rozwiązania każdego potencjał błąd kompilacji.

Może zostać wyświetlony ostrzeżenia kompilacji dodatkowe dotyczące przestarzałe metody lub właściwości. Ostrzeżenia zawierają instrukcje dotyczące rozwiązania do zastosowania zamiast przestarzałych funkcji. Na przykład, jeśli aplikacja używa `DataSourceType.DocumentDb` właściwość powinna pojawić się ostrzeżenie informujące o tym, "tego elementu członkowskiego jest przestarzały. Użyj bazy danych cosmos DB".

Gdy problem został rozwiązany żadnych ostrzeżeń ani błędów kompilacji, można wprowadzić zmiany do aplikacji w taki sposób, aby móc korzystać z nowych funkcji, jeśli chcesz, aby. Nowe funkcje w zestawie SDK są szczegółowo opisane w [nowości w wersji 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Istotne zmiany w wersji 9

Istnieje kilka istotne zmiany w wersji 9, które mogą wymagać zmian w kodzie oprócz ponownie skompilować aplikację.

> [!NOTE]
> Lista zmian poniżej nie jest wyczerpująca. Niektóre zmiany prawdopodobnie nie powoduje błędy kompilacji, ale z technicznego punktu widzenia są istotne, ponieważ dzielą się one zgodność binarną za pomocą zestawów, które są zależne od wcześniejszej wersje zestawów zestawu .NET SDK usługi Azure Search. Takie zmiany nie zostały wymienione poniżej. Skompiluj aplikację ponownie po uaktualnieniu do wersji 9, aby uniknąć jakichkolwiek problemów zgodność binarną.

### <a name="immutable-properties"></a>Niezmienialnych właściwości

Właściwości publicznych z kilku klas modelu teraz są niezmienne. Jeśli potrzebujesz do tworzenia niestandardowych wystąpień tych klas na potrzeby testowania, można użyć nowego konstruktory sparametryzowane:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Zmiany pola

`Field` Klasy zmienił się teraz, może również reprezentować złożonych pola.

Następujące `bool` teraz dopuszczają właściwości:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Jest to spowodowane właściwości te muszą być teraz `null` w przypadku złożonych pól. Jeśli masz kod, który odczytuje te właściwości, musi ona być przygotowany do obsługi `null`. Należy pamiętać, że wszystkie inne właściwości `Field` wcześniej i może dopuszczać wartości null, w dalszym ciągu i niektóre z nich będzie także `null` w przypadku złożonych pola — w szczególności następujące czynności:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Konstruktor bez parametrów `Field` dokonano `internal`. Od teraz każdy `Field` wymaga jawną nazwę i typ danych w czasie produkcji.

### <a name="simplified-batch-and-results-types"></a>Uproszczone typy przetwarzania wsadowego i wyniki

W wersji 7.0 preview i starszych różnymi klasami, które hermetyzują grup dokumentów zostały strukturę w hierarchii klas równoległych:

  -  `DocumentSearchResult` i `DocumentSearchResult<T>` odziedziczone po `DocumentSearchResultBase`
  -  `DocumentSuggestResult` i `DocumentSuggestResult<T>` odziedziczone po `DocumentSuggestResultBase`
  -  `IndexAction` i `IndexAction<T>` odziedziczone po `IndexActionBase`
  -  `IndexBatch` i `IndexBatch<T>` odziedziczone po `IndexBatchBase`
  -  `SearchResult` i `SearchResult<T>` odziedziczone po `SearchResultBase`
  -  `SuggestResult` i `SuggestResult<T>` odziedziczone po `SuggestResultBase`

Typy pochodne, parametr typu generycznego nie były przeznaczone do użycia w scenariuszach "dynamiczne typowanie" i zakłada, że użycie `Document` typu.

Począwszy od wersji 8.0-preview klasy podstawowe i pochodne nieogólnego wszystkie zostały usunięte. W przypadku scenariuszy dynamiczne typowanie, można użyć `IndexBatch<Document>`, `DocumentSearchResult<Document>`i tak dalej.
 
### <a name="removed-extensibleenum"></a>Usunięto ExtensibleEnum

`ExtensibleEnum` Klasy bazowej został usunięty. Wszystkie klasy, które od niego pochodzi są teraz struktur, takich jak `AnalyzerName`, `DataType`, i `DataSourceType` na przykład. Ich `Create` metody również zostały usunięte. Możesz po prostu usunąć wywołania `Create` ponieważ te typy są niejawnej konwersji z ciągów znaków. Które powoduje błędy kompilatora, możesz jawnie wywołać operatora konwersji za pośrednictwem rzutowanie do rozróżniania typów. Na przykład można zmienić kod w następujący sposób:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

Następujące zmiany:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Właściwości, które są przechowywane wartości opcjonalne typy teraz są jawnie wpisane jako dopuszczającego wartość null, aby nadal być opcjonalny.

### <a name="removed-facetresults-and-hithighlights"></a>Usunięto FacetResults i HitHighlights

`FacetResults` i `HitHighlights` klasy zostały usunięte. Wyniki aspekt teraz o typach `IDictionary<string, IList<FacetResult>>` i naciśnij klawisz jako `IDictionary<string, IList<string>>`. Szybkie rozwiązywanie błędów kompilacji, wynikające z tej zmiany jest dodanie `using` aliasów w górnej części każdego pliku, który korzysta z usuniętych typów. Na przykład:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Zmień na SynonymMap 

`SynonymMap` Konstruktor nie ma już `enum` parametr `SynonymMapFormat`. To wyliczenie tylko miał jedną wartością i dlatego został nadmiarowe. Jeśli widzisz błędy w wyniku tego kompilacji, po prostu usunąć odwołania do `SynonymMapFormat` parametru.

### <a name="miscellaneous-model-class-changes"></a>Zmiany w modelu różnych klas

`AutocompleteMode` Właściwość `AutocompleteParameters` nie jest już dopuszczającego wartość null. Jeśli masz kod, który przypisuje tę właściwość, aby `null`, można po prostu usunąć go i właściwość zostanie automatycznie zainicjowane do wartości domyślnej.

Kolejność parametrów `IndexAction` Konstruktor zmienił się teraz, gdy ten konstruktor jest generowany automatycznie. Zamiast przy użyciu konstruktora, zaleca się użycie metod fabryki `IndexAction.Upload`, `IndexAction.Merge`i tak dalej.

### <a name="removed-preview-features"></a>Usunięto w wersji zapoznawczej

Jeśli uaktualniasz z wersji 8.0-preview do wersji 9, należy pamiętać, że szyfrowanie za pomocą kluczy zarządzanych przez klienta została usunięta, ponieważ ta funkcja jest nadal w wersji zapoznawczej. W szczególności `EncryptionKey` właściwości `Index` i `SynonymMap` zostały usunięte.

Jeśli aplikacja ma zależność twardych na temat tej funkcji, nie można uaktualnić do wersji 9 zestawu .NET SDK usługi Azure Search. Można nadal używać wersji 8.0-preview. Jednak należy pamiętać, że **nie zaleca się przy użyciu zestawów SDK wersji zapoznawczej w aplikacjach produkcyjnych**. Funkcje w wersji zapoznawczej są tylko do oceny i mogą ulec zmianie.

> [!NOTE]
> Jeśli utworzono zaszyfrowanych indeksów lub synonim mapy za pomocą wersji 8.0-preview zestawu SDK, nadal można ich używać i modyfikowania ich definicji przy użyciu zestawu SDK w wersji 9 bez negatywnego wpływu na ich stanu szyfrowania. 9 wersję zestawu SDK będzie wysyłał `encryptionKey` właściwości interfejsu API REST, co w efekcie interfejsu API REST nie spowoduje zmiany stanu szyfrowania zasobu. 

### <a name="behavioral-change-in-data-retrieval"></a>Zmiana zachowania programu pobieranie danych

Jeśli używasz "o typach określanych dynamicznie" `Search`, `Suggest`, lub `Get` interfejsów API, która zwraca jedno wystąpienie typu `Document`, należy pamiętać, że deserializacji teraz pusty tablic JSON do `object[]` zamiast `string[]`.

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat korzystania z zestawu .NET SDK usługi Azure Search zobacz [.NET porad](search-howto-dotnet-sdk.md).

Chętnie poznamy Twoją opinię na zestawie SDK. Jeśli napotkasz problemy, możesz poprosić nas o pomoc na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz Prześlij zgłoszenie do [repozytorium Azure .NET SDK w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że prefiks tytuł problemu z usługą "[Azure Search]".

Dziękujemy za pomocą usługi Azure Search.
