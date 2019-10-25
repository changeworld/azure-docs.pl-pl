---
title: Uaktualnianie do Azure Search .NET SDK wersja 9
titleSuffix: Azure Cognitive Search
description: Migruj kod do Azure Search .NET SDK wersja 9 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793017"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Uaktualnianie do Azure Search .NET SDK wersja 9

W przypadku korzystania z wersji 7,0-Preview lub starszej [Azure Search .NET SDK](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 9.

> [!NOTE]
> Jeśli chcesz użyć wersji 8,0-Preview, aby oszacować funkcje, które nie są jeszcze ogólnie dostępne, możesz również wykonać instrukcje przedstawione w tym artykule, aby uaktualnić program do wersji 8,0 — wersja zapoznawcza z wcześniejszych wersji.

Aby zapoznać się z bardziej ogólnym przewodnikiem dotyczącym zestawu SDK, łącznie z przykładami, zobacz [jak używać Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 9 zestawu SDK Azure Search .NET zawiera wiele zmian z wcześniejszych wersji. Niektóre z nich są istotne, ale powinny wymagać jedynie relatywnie drobnych zmian w kodzie. Zobacz [procedurę uaktualniania](#UpgradeSteps) , aby uzyskać instrukcje dotyczące zmiany kodu w celu użycia nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 4,0-Preview lub starszej, należy najpierw uaktualnić do wersji 5, a następnie uaktualnić do wersji 9. Aby uzyskać instrukcje [, zobacz Uaktualnianie do Azure Search .NET SDK w wersji 5](search-dotnet-sdk-migration-version-5.md) .
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Możesz nadal korzystać z wersji, gdy nie jest już Najnowsza, ale zalecamy przeprowadzenie migracji kodu w celu użycia najnowszej wersji. W przypadku korzystania z interfejsu API REST należy określić wersję interfejsu API w każdym żądaniu za pośrednictwem parametru API-Version. Podczas korzystania z zestawu SDK platformy .NET wersja zestawu SDK określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego zestawu SDK, możesz nadal uruchamiać ten kod bez zmian, nawet jeśli usługa zostanie uaktualniona w celu obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Co nowego w wersji 9
Wersja 9 zestawu SDK Azure Search .NET jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST Azure Search, w tym 2019-05-06. Dzięki temu można korzystać z nowych funkcji Azure Search z aplikacji .NET, w tym:

* [Wzbogacanie AI](cognitive-search-concept-intro.md) to możliwość wyodrębnienia tekstu z obrazów, obiektów blob i innych źródeł danych bez struktury — wzbogacanie zawartości, aby ułatwić wyszukiwanie w Azure Search indeksie.
* Obsługa [typów złożonych](search-howto-complex-data-types.md) pozwala modelować niemal dowolną zagnieżdżoną strukturę JSON w indeksie Azure Search.
* [Funkcja autouzupełniania](search-autocomplete-tutorial.md) stanowi alternatywę dla interfejsu API **sugerowania** w celu zaimplementowania zachowania typu "wyszukiwanie jako użytkownik". Autouzupełnianie "kończy się" wyraz lub frazę, która jest aktualnie wpisywana przez użytkownika.
* [Tryb analizy JsonLines](search-howto-index-json-blobs.md), część indeksowania obiektów blob platformy Azure, tworzy jeden dokument wyszukiwania na jednostkę JSON rozdzieloną wierszem.

### <a name="new-preview-features-in-version-80-preview"></a>Nowe funkcje wersji zapoznawczej w wersji 8,0 — wersja zapoznawcza
W wersji 8,0 — wersja zapoznawcza interfejsu API programu Azure Search .NET SDK wersja 2017-11-11-Preview. Ta wersja zawiera wszystkie te same funkcje wersji 9 oraz:

* [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) dla szyfrowania po stronie usługi — w trybie REST są nową funkcją w wersji zapoznawczej. Oprócz wbudowanego szyfrowania, które jest zarządzane przez firmę Microsoft, można zastosować dodatkową warstwę szyfrowania, w której jesteś jedynym właścicielem kluczy.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia
Najpierw należy zaktualizować odwołanie NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając pozycję "Zarządzaj pakietami NuGet...". w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. W zależności od tego, jak kod jest strukturalny, może zostać pomyślnie ponownie odbudowany. Jeśli tak, wszystko jest gotowe!

Jeśli kompilacja zakończy się niepowodzeniem, należy naprawić każdy błąd kompilacji. Zobacz istotne [zmiany w wersji 9](#ListOfChanges) , aby uzyskać szczegółowe informacje na temat sposobu rozwiązywania każdego potencjalnego błędu kompilacji.

Mogą pojawić się dodatkowe ostrzeżenia kompilacji dotyczące przestarzałych metod lub właściwości. Ostrzeżenia będą zawierać instrukcje dotyczące użycia zamiast przestarzałej funkcji. Na przykład jeśli aplikacja używa właściwości `DataSourceType.DocumentDb`, należy otrzymać ostrzeżenie z informacją o tym, że ten element członkowski jest przestarzały. Zamiast tego użyj CosmosDb ".

Po naprawieniu błędów kompilacji lub ostrzeżeń możesz wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. Nowe funkcje zestawu SDK są szczegółowo opisane w programie [co nowego w wersji 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Istotne zmiany w wersji 9

Istnieje kilka istotnych zmian w wersji 9, które mogą wymagać zmiany kodu oprócz ponownego kompilowania aplikacji.

> [!NOTE]
> Lista zmian poniżej nie jest wyczerpująca. Niektóre zmiany prawdopodobnie nie spowodują błędów kompilacji, ale są technicznie przerywane, ponieważ przerwają zgodność binarną z zestawami, które są zależne od wcześniejszych wersji zestawów Azure Search .NET SDK. Takie zmiany nie są wymienione poniżej. Skompiluj ponownie aplikację podczas uaktualniania do wersji 9, aby uniknąć jakichkolwiek binarnych problemów ze zgodnością.

### <a name="immutable-properties"></a>Właściwości niemodyfikowalne

Właściwości publiczne kilku klas modelu są teraz niezmienne. Jeśli trzeba utworzyć niestandardowe wystąpienia tych klas do testowania, można użyć nowych, sparametryzowanych konstruktorów:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Zmiany pola

Klasa `Field` została teraz zmieniona, ponieważ może również reprezentować pola złożone.

Następujące właściwości `bool` są teraz nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Dzieje się tak dlatego, że te właściwości muszą być teraz `null` w przypadku pól złożonych. Jeśli masz kod, który odczytuje te właściwości, należy przygotować się do obsługi `null`. Zwróć uwagę, że wszystkie inne właściwości `Field` były zawsze i nadal dopuszczają wartości null, a niektóre z nich również będą `null` w przypadku pól złożonych — w następujący sposób:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

`internal`o konstruktora bez parametrów `Field`. Od teraz, każdy `Field` wymaga jawnej nazwy i typu danych w czasie konstruowania.

### <a name="simplified-batch-and-results-types"></a>Uproszczone typy partii i wyników

W wersji 7,0 — wersja zapoznawcza i wcześniejsze, różne klasy, które hermetyzują grupy dokumentów, zostały podzielone na hierarchie klas równoległych:

  -  `DocumentSearchResult` i `DocumentSearchResult<T>` dziedziczone z `DocumentSearchResultBase`
  -  `DocumentSuggestResult` i `DocumentSuggestResult<T>` dziedziczone z `DocumentSuggestResultBase`
  -  `IndexAction` i `IndexAction<T>` dziedziczone z `IndexActionBase`
  -  `IndexBatch` i `IndexBatch<T>` dziedziczone z `IndexBatchBase`
  -  `SearchResult` i `SearchResult<T>` dziedziczone z `SearchResultBase`
  -  `SuggestResult` i `SuggestResult<T>` dziedziczone z `SuggestResultBase`

Typy pochodne bez parametru typu ogólnego zostały przeznaczone do użycia w scenariuszach "dynamicznie wpisanych" i przyjęto użycie typu `Document`.

Począwszy od wersji 8,0-Preview, klasy bazowe i inne niż ogólne klasy pochodne zostały usunięte. W przypadku scenariuszy typu dynamicznego można użyć `IndexBatch<Document>`, `DocumentSearchResult<Document>`i tak dalej.
 
### <a name="removed-extensibleenum"></a>Usunięto ExtensibleEnum

Klasa bazowa `ExtensibleEnum` została usunięta. Wszystkie klasy pochodzące od niej są teraz strukturami, takimi jak `AnalyzerName`, `DataType`i `DataSourceType`. Ich metody `Create` również zostały usunięte. Można po prostu usunąć wywołania do `Create`, ponieważ te typy są niejawnie konwertowane z ciągów. Jeśli spowoduje to błędy kompilatora, można jawnie wywołać Operator konwersji poprzez rzutowanie na typy niejednoznaczne. Można na przykład zmienić kod podobny do tego:

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

na taki:

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

Właściwości, które posiadały wartości opcjonalne tych typów, są teraz jawnie wpisane jako nullable, aby nadal były opcjonalne.

### <a name="removed-facetresults-and-hithighlights"></a>Usunięto FacetResults i HitHighlights

Klasy `FacetResults` i `HitHighlights` zostały usunięte. Wyniki aspektów są teraz wpisywane jako `IDictionary<string, IList<FacetResult>>` i trafień jako `IDictionary<string, IList<string>>`. Szybkie rozwiązanie błędów kompilacji wprowadzonych przez tę zmianę polega na dodaniu aliasów `using` u góry każdego pliku, który używa usuniętych typów. Na przykład:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Zmień na SynonymMap 

Konstruktor `SynonymMap` nie ma już parametru `enum` do `SynonymMapFormat`. Ta Wyliczenie ma tylko jedną wartość i dlatego była nadmiarowa. Jeśli w wyniku tego wystąpią błędy kompilacji, wystarczy usunąć odwołania do parametru `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Zmiany klasy modelu różnego

Właściwość `AutocompleteMode` `AutocompleteParameters` nie dopuszcza wartości null. Jeśli masz kod przypisujący tę właściwość do `null`, możesz po prostu usunąć ją, a właściwość zostanie automatycznie zainicjowana do wartości domyślnej.

Kolejność parametrów konstruktora `IndexAction` została zmieniona teraz, gdy ten konstruktor został wygenerowany automatycznie. Zamiast korzystać z konstruktora, zalecamy użycie metod fabryki `IndexAction.Upload`, `IndexAction.Merge`i tak dalej.

### <a name="removed-preview-features"></a>Usunięte funkcje w wersji zapoznawczej

W przypadku uaktualniania z wersji 8,0-Preview do wersji 9 należy pamiętać, że szyfrowanie z kluczami zarządzanymi przez klienta zostało usunięte, ponieważ ta funkcja jest nadal w wersji zapoznawczej. W szczególnych przypadkach `EncryptionKey` właściwości `Index` i `SynonymMap` zostały usunięte.

Jeśli aplikacja ma twardą zależność od tej funkcji, nie będzie można przeprowadzić uaktualnienia do wersji 9 zestawu SDK Azure Search .NET. Możesz nadal używać wersji 8,0-Preview. Należy jednak pamiętać, że **nie zalecamy korzystania z wersji zapoznawczej zestawów SDK w aplikacjach produkcyjnych**. Funkcje w wersji zapoznawczej służą wyłącznie do oceny i mogą ulec zmianie.

> [!NOTE]
> Jeśli utworzono zaszyfrowane indeksy lub mapy synonimów przy użyciu wersji 8,0-Preview zestawu SDK, nadal będzie można ich używać i modyfikować ich definicje przy użyciu wersji 9 zestawu SDK bez negatywnego wpływu na ich stan szyfrowania. Wersja 9 zestawu SDK nie będzie wysyłać właściwości `encryptionKey` do interfejsu API REST, a w efekcie interfejs API REST nie zmieni stanu szyfrowania zasobu. 

### <a name="behavioral-change-in-data-retrieval"></a>Zmiana zachowania podczas pobierania danych

Jeśli używasz interfejsów API `Search`, `Suggest`lub `Get`, które zwracają wystąpienia typu `Document`, pamiętaj, że teraz deserializacji puste tablice JSON do `object[]` zamiast `string[]`.

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz więcej szczegółowych informacji na temat korzystania z zestawu SDK Azure Search platformy .NET, zobacz [temat jak to zrobić](search-howto-dotnet-sdk.md).

Poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, skontaktuj się z nami, aby uzyskać pomoc dotyczącą [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem "[Azure Search]".

Dziękujemy za korzystanie z Azure Search!
