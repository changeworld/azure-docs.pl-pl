---
title: Uaktualnienie do usługi Azure Search .NET SDK w wersji 9
titleSuffix: Azure Cognitive Search
description: Migruj kod do narzędzia Azure Search .NET SDK w wersji 9 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany kodu są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793017"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Uaktualnienie do usługi Azure Search .NET SDK w wersji 9

Jeśli używasz wersji 7.0-preview lub starszych [usługi Azure Search .NET SDK,](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 9.

> [!NOTE]
> Jeśli chcesz użyć wersji 8.0-preview do oceny funkcji, które nie są jeszcze ogólnie dostępne, można również wykonać instrukcje w tym artykule, aby uaktualnić do wersji 8.0-preview z poprzednich wersji.

Aby uzyskać bardziej ogólne wskazówki dotyczące sdk, w tym przykłady, zobacz [Jak używać usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 9 narzędzia Azure Search .NET SDK zawiera wiele zmian z wcześniejszych wersji. Niektóre z nich są przełomowe zmiany, ale powinny one wymagać tylko stosunkowo niewielkie zmiany w kodzie. Zobacz [Kroki uaktualnienia, aby uzyskać](#UpgradeSteps) instrukcje dotyczące zmiany kodu, aby użyć nowej wersji SDK.

> [!NOTE]
> Jeśli używasz wersji 4.0-preview lub starszej, należy najpierw uaktualnić do wersji 5, a następnie uaktualnić do wersji 9. Aby uzyskać instrukcje, zobacz [uaktualnianie do narzędzia Azure Search .NET SDK w wersji 5.](search-dotnet-sdk-migration-version-5.md)
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowszą. Możesz nadal używać wersji, gdy nie jest już najnowsza, ale zaleca się migrację kodu w celu użycia najnowszej wersji. Podczas korzystania z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru wersji interfejsu api. Podczas korzystania z .NET SDK, wersja SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego sdk, można kontynuować uruchamianie tego kodu bez zmian, nawet jeśli usługa jest uaktualniany do obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Co nowego w wersji 9
Wersja 9 narzędzia Azure Search .NET SDK jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST usługi Azure Search, w szczególności 2019-05-06. Dzięki temu można używać nowych funkcji usługi Azure Search z aplikacji .NET, w tym następujące:

* [Wzbogacanie sztucznej inteligencji](cognitive-search-concept-intro.md) to możliwość wyodrębniania tekstu z obrazów, obiektów blob i innych nieustrukturyzowanych źródeł danych — wzbogacając zawartość, aby uczynić go bardziej przeszukiwalnym w indeksie usługi Azure Search.
* Obsługa [typów złożonych](search-howto-complex-data-types.md) umożliwia modelowanie prawie każdej zagnieżdżonej struktury JSON w indeksie usługi Azure Search.
* [Autouzupełnianie](search-autocomplete-tutorial.md) stanowi alternatywę dla interfejsu API **sugestii** do implementowania zachowania wyszukiwania zgodnie z typem. Autouzupełnianie "kończy" wyraz lub frazę, którą użytkownik aktualnie wpisuje.
* [JsonLines analizowanie tryb](search-howto-index-json-blobs.md), część indeksowania obiektów Blob platformy Azure, tworzy jeden dokument wyszukiwania dla jednostki JSON, który jest oddzielony przez nowy obiekt.

### <a name="new-preview-features-in-version-80-preview"></a>Nowe funkcje podglądu w wersji 8.0-preview
Wersja 8.0-preview usługi Azure Search .NET SDK dotyczy interfejsu API w wersji 2017-11-11-Preview. Ta wersja zawiera wszystkie te same funkcje wersji 9, plus:

* [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) do szyfrowania po stronie usługi w spoczynku to nowa funkcja w wersji zapoznawczej. Oprócz wbudowanego szyfrowania w spoczynku zarządzanego przez firmę Microsoft, można zastosować dodatkową warstwę szyfrowania, w której jesteś jedynym właścicielem kluczy.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki uaktualnienia
Najpierw zaktualizuj `Microsoft.Azure.Search` odwołanie NuGet do użycia konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając opcję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu nowych pakietów i ich zależności, odbuduj projekt. W zależności od struktury kodu może pomyślnie odbudować. Jeśli tak, jesteś gotowy do pracy!

Jeśli kompilacja nie powiedzie się, należy naprawić każdy błąd kompilacji. Zobacz [Przełamywanie zmian w wersji 9,](#ListOfChanges) aby uzyskać szczegółowe informacje na temat rozwiązywania każdego potencjalnego błędu kompilacji.

Mogą zostać wyświetlone dodatkowe ostrzeżenia kompilacji związane z przestarzałych metod lub właściwości. Ostrzeżenia będą zawierać instrukcje dotyczące tego, czego należy użyć zamiast przestarzałej funkcji. Na przykład jeśli aplikacja używa `DataSourceType.DocumentDb` właściwości, należy otrzymasz ostrzeżenie z napisem "Ten element członkowski jest przestarzały. Zamiast tego użyj cosmosdb".

Po usunięciu błędów kompilacji lub ostrzeżeń, można wprowadzić zmiany w aplikacji, aby skorzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w SDK są szczegółowo opisane w [Co nowego w wersji 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Przełomowe zmiany w wersji 9

Istnieje kilka przełomowych zmian w wersji 9, które mogą wymagać zmian w kodzie oprócz przebudowy aplikacji.

> [!NOTE]
> Poniższa lista zmian nie jest wyczerpująca. Niektóre zmiany prawdopodobnie nie spowoduje błędów kompilacji, ale są technicznie łamanie, ponieważ są one złamać zgodność binarną z zestawami, które zależą od wcześniejszych wersji zestawów SDK usługi Azure Search .NET. Takie zmiany nie są wymienione poniżej. Należy odbudować aplikację podczas uaktualniania do wersji 9, aby uniknąć problemów ze zgodnością binarnych.

### <a name="immutable-properties"></a>Właściwości niezmienne

Właściwości publiczne kilku klas modelu są teraz niezmienne. Jeśli chcesz utworzyć niestandardowe wystąpienia tych klas do testowania, można użyć nowych konstruktorów sparametryzowanych:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Zmiany w polu

Klasa `Field` zmieniła się teraz, że może również reprezentować złożone pola.

Następujące `bool` właściwości są teraz nullable:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Dzieje się tak, ponieważ `null` te właściwości muszą być teraz w przypadku złożonych pól. Jeśli masz kod, który odczytuje te właściwości, `null`musi być przygotowany do obsługi . Należy zauważyć, że `Field` wszystkie inne właściwości zawsze były i nadal są nie `null` do unieważnienia, a niektóre z nich będą również w przypadku złożonych pól — w szczególności następujące:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Wykonano bez parametrów `Field` `internal`konstruktor. Od teraz każdy `Field` wymaga jawnej nazwy i typu danych w czasie budowy.

### <a name="simplified-batch-and-results-types"></a>Uproszczone typy partii i wyników

W wersji 7.0-preview i wcześniejszych, różne klasy, które hermetyzują grupy dokumentów zostały skonstruowane w równoległych hierarchii klas:

  -  `DocumentSearchResult`i `DocumentSearchResult<T>` odziedziczone po`DocumentSearchResultBase`
  -  `DocumentSuggestResult`i `DocumentSuggestResult<T>` odziedziczone po`DocumentSuggestResultBase`
  -  `IndexAction`i `IndexAction<T>` odziedziczone po`IndexActionBase`
  -  `IndexBatch`i `IndexBatch<T>` odziedziczone po`IndexBatchBase`
  -  `SearchResult`i `SearchResult<T>` odziedziczone po`SearchResultBase`
  -  `SuggestResult`i `SuggestResult<T>` odziedziczone po`SuggestResultBase`

Typy pochodne bez parametru typu ogólnego miały być używane w scenariuszach typu "dynamicznie typizowane" i zakładane użycie `Document` typu.

Począwszy od wersji 8.0-preview, klasy podstawowe i niegeneryczne klasy pochodne zostały usunięte. W przypadku scenariuszy typu dynamicznego `IndexBatch<Document>`można `DocumentSearchResult<Document>`użyć programu , i tak dalej.
 
### <a name="removed-extensibleenum"></a>Usunięto ExtensibleEnum

Klasa `ExtensibleEnum` podstawowa została usunięta. Wszystkie klasy, które pochodzą z niego są `AnalyzerName`teraz `DataType`struktury, takie jak , i `DataSourceType` na przykład. Ich `Create` metody również zostały usunięte. Można po prostu `Create` usunąć wywołania, ponieważ te typy są niejawnie konwertowane z ciągów. Jeśli to powoduje błędy kompilatora, można jawnie wywołać operatora konwersji za pośrednictwem rzutowania, aby odróżnić typy. Na przykład można zmienić kod w ten sposób:

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

wprowadź następujące zmiany:

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

Właściwości, które posiadały wartości opcjonalne tych typów są teraz jawnie wpisywane jako nullable, więc nadal są opcjonalne.

### <a name="removed-facetresults-and-hithighlights"></a>Usunięto Wyniki i HitYwyświetlacza

I `FacetResults` `HitHighlights` klasy zostały usunięte. Wyniki facet są teraz `IDictionary<string, IList<FacetResult>>` wpisywane `IDictionary<string, IList<string>>`jako i hit podkreśla jako . Szybkim sposobem rozwiązania błędów kompilacji wprowadzonych `using` przez tę zmianę jest dodanie aliasów w górnej części każdego pliku, który używa usuniętych typów. Przykład:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Zmień na SynonimMap 

Konstruktor `SynonymMap` nie ma `enum` już `SynonymMapFormat`parametru dla . Wyliczenie to miało tylko jedną wartość i dlatego było zbędne. Jeśli widzisz błędy kompilacji w wyniku tego, po `SynonymMapFormat` prostu usuń odwołania do parametru.

### <a name="miscellaneous-model-class-changes"></a>Różne zmiany klasy modelu

Właściwość `AutocompleteMode` `AutocompleteParameters` nie jest już nullable. Jeśli masz kod, który przypisuje `null`tę właściwość do , można po prostu usunąć go i właściwość zostanie automatycznie zainicjowana do wartości domyślnej.

Kolejność parametrów do `IndexAction` konstruktora zmieniła się teraz, gdy ten konstruktor jest generowany automatycznie. Zamiast używać konstruktora, zalecamy użycie `IndexAction.Upload` `IndexAction.Merge`metod fabrycznych , i tak dalej.

### <a name="removed-preview-features"></a>Usunięto funkcje podglądu

W przypadku uaktualniania z wersji 8.0-preview do wersji 9 należy pamiętać, że szyfrowanie za pomocą kluczy zarządzanych przez klienta zostało usunięte, ponieważ ta funkcja jest nadal w wersji zapoznawczej. W szczególności `EncryptionKey` właściwości `Index` i `SynonymMap` zostały usunięte.

Jeśli aplikacja ma twardą zależność od tej funkcji, nie będzie można uaktualnić do wersji 9 usługi Azure Search .NET SDK. Można nadal korzystać z wersji 8.0-preview. Należy jednak pamiętać, że **nie zaleca się używania podglądu SDK w aplikacjach produkcyjnych.** Funkcje w wersji zapoznawczej służą wyłącznie do oceny i mogą ulec zmianie.

> [!NOTE]
> Jeśli utworzono zaszyfrowane indeksy lub mapy synonimów przy użyciu wersji 8.0-preview sdk, nadal będzie można ich używać i modyfikować ich definicje przy użyciu wersji 9 SDK bez negatywnego wpływu na ich stan szyfrowania. Wersja 9 zestawu SDK `encryptionKey` nie wyśle właściwości do interfejsu API REST, w związku z czym interfejs API REST nie zmieni stanu szyfrowania zasobu. 

### <a name="behavioral-change-in-data-retrieval"></a>Zmiana zachowań w pobieraniu danych

Jeśli używasz "dynamicznie `Search`wpisywanych" lub `Suggest` `Get` interfejsów API zwracających `Document`wystąpienia typu, należy pamiętać, że teraz `object[]` deserializują `string[]`puste tablice JSON zamiast .

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz więcej informacji na temat korzystania z narzędzia Azure Search .NET SDK, zobacz [instrukcje .NET](search-howto-dotnet-sdk.md).

Czekamy na Wasze opinie na temat SDK. Jeśli napotkasz problemy, nie krępuj się poprosić nas o pomoc w [stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem za pomocą "[Usługi Azure Search]".

Dziękujemy za korzystanie z usługi Azure Search!
