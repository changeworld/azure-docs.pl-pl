---
title: Uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 3 - usługi Azure Search
description: Migrowanie kodu do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 3 ze starszych wersji. Poznaj nowe funkcje i zmiany kodu, które są wymagane.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 488a9fe24fb8fca0b7309dea63d0640a6384f95c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024093"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Uaktualnianie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 3

> [!Important]
> Ta zawartość jest nadal w trakcie tworzenia. W wersji 9.0 zestawu .NET SDK usługi Azure Search jest dostępna dla narzędzia NuGet. Pracujemy nad tym artykule, aby wyjaśnić, jak uaktualnić do 9.0. 
> 

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Jeśli używasz wersji 2.0 w wersji zapoznawczej lub starszy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnić aplikację do używania w wersji 3.

Aby uzyskać ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [jak używać usługi Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Zestaw .NET SDK usługi Azure Search w wersji 3 zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim pomocnicza, dzięki czemu Zmienianie kodu należy wymagać tylko minimalnym nakładzie pracy. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) zawiera instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 1.0.2-preview lub starsze, należy uaktualnić system do wersji 1.1 pierwszy, a następnie uaktualnić do wersji 3. Zobacz [uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 1.1](search-dotnet-sdk-migration-version-1.md) instrukcje.
>
> Wystąpienia usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Można użyć wersji, gdy nie jest już najnowsze, ale zaleca się przeprowadzenie migracji kodu, aby użyć najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru api-version. Korzystając z zestawu .NET SDK, wersję zestawu SDK, w przypadku używania określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszy zestaw SDK, można nadal uruchomić ten kod bez konieczności wprowadzania zmian, nawet wtedy, gdy usługa zostanie uaktualniony do nowszej wersji interfejsu API do obsługi.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co nowego w wersji 3
Zestaw .NET SDK usługi Azure Search w wersji 3 jest przeznaczony dla najnowszej ogólnie dostępnej wersji Azure Search REST API, w szczególności 2016-09-01. Dzięki temu można używać wiele nowych funkcji usługi Azure Search z poziomu aplikacji .NET, w tym następujące:

* [Analizatory niestandardowe](https://aka.ms/customanalyzers)
* [Usługa Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) i [Azure Table Storage](search-howto-indexing-azure-tables.md) obsługi indeksatorów
* Dostosowywanie indeksatora za pośrednictwem [mapowania pól](search-indexer-field-mappings.md)
* Elementy etag obsługuje umożliwiające bezpieczne równoczesnych aktualizacji definicji indeksów, indeksatorów i źródeł danych
* Pomoc techniczna przy tworzeniu definicje pól indeksu deklaratywnie przez urządzanie klasy modelu i za pomocą nowego `FieldBuilder` klasy.
* Obsługa platformy .NET Core i .NET przenośne Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety i ich zależności, należy ponownie skompilować projekt. W zależności od struktury kodu jego może odbudować pomyślnie. Jeśli tak, jesteś gotowy!

Jeśli kompilacja nie powiedzie się, powinien zostać wyświetlony błąd kompilacji, jak pokazano poniżej:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Następnym krokiem jest, aby naprawić ten błąd kompilacji. Zobacz [przełomowe zmiany w wersji 3](#ListOfChanges) szczegółowe informacje o co powoduje błąd i jak go naprawić.

Może zostać wyświetlony ostrzeżenia kompilacji dodatkowe dotyczące przestarzałe metody lub właściwości. Ostrzeżenia zawierają instrukcje dotyczące rozwiązania do zastosowania zamiast przestarzałych funkcji. Na przykład, jeśli aplikacja używa `IndexingParameters.Base64EncodeKeys` właściwość powinna pojawić się ostrzeżenie informujące, `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Gdy problem został rozwiązany błędów kompilacji, można wprowadzić zmiany do aplikacji w taki sposób, aby móc korzystać z nowych funkcji, jeśli chcesz, aby. Nowe funkcje w zestawie SDK są szczegółowo opisane w [nowości w wersji 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Istotne zmiany w wersji 3
Istnieje niewielka liczba przełomowe zmiany w wersji 3, które mogą wymagać kodu zmienia oprócz ponownie skompilować aplikację.

### <a name="indexesgetclient-return-type"></a>Zwracany typ Indexes.GetClient
`Indexes.GetClient` Metoda ma nowy typ zwracany. Uprzednio była zwracana `SearchIndexClient`, ale ta zmiana została wprowadzona `ISearchIndexClient` w wersji 2.0 — w wersji zapoznawczej, a zmiany są stosowane do wersji 3. Ma to na celu zapewnienie obsługi dla klientów, które chcą testowanie `GetClient` metody testów jednostkowych, zwracając implementację testową z `ISearchIndexClient`.

#### <a name="example"></a>Przykład
Jeśli Twój kod wygląda następująco:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, typ danych i inne osoby nie są już niejawnie konwertowany na ciągi
Istnieje wiele typów w zestawie SDK .NET Azure wyszukiwania, który pochodzi od `ExtensibleEnum`. Wcześniej te typy były wszystkie niejawnej konwersji na typ `string`. Jednak błąd został wykryty w `Object.Equals` implementacji dla tych klas i naprawianie błędów wymagane wyłączenie tej konwersji niejawnych. Jawna konwersja na `string` nadal uzyskuje zezwolenie.

#### <a name="example"></a>Przykład
Jeśli Twój kod wygląda następująco:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Można go zmienić, na ten element, aby naprawić wszystkie błędy kompilacji:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Usunięto przestarzali członkowie

Może zostać wyświetlony, błędy kompilacji związane z metodami lub właściwościami, które zostały oznaczone jako przestarzałe w wersji 2.0 w wersji zapoznawczej, a następnie usunięte w wersji 3. Jeśli wystąpią błędy takie, w tym miejscu przedstawiono sposób ich rozwiązywania:

- W przypadku używania tego konstruktora: `ScoringParameter(string name, string value)`, zamiast tego użyj tego: `ScoringParameter(string name, IEnumerable<string> values)`
- Jeśli używano `ScoringParameter.Value` właściwości, należy zastosować `ScoringParameter.Values` właściwości lub `ToString` metody zamiast tego.
- Jeśli używano `SearchRequestOptions.RequestId` właściwości, należy zastosować `ClientRequestId` właściwości zamiast tego.

### <a name="removed-preview-features"></a>Usunięto w wersji zapoznawczej

W wersji 2.0 — w wersji zapoznawczej jest uaktualniane do wersji 3, należy pamiętać, że JSON i analizy obsługę indeksatory obiektów Blob CSV został usunięty, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególności następujące metody `IndexingParametersExtensions` klasy zostały usunięte:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardych zależność od tych funkcji, nie można uaktualnić do wersji 3 zestawu .NET SDK usługi Azure Search. Można nadal używać wersji 2.0 — w wersji zapoznawczej. Jednak należy pamiętać, że **nie zaleca się przy użyciu zestawów SDK wersji zapoznawczej w aplikacjach produkcyjnych**. Funkcje w wersji zapoznawczej są tylko do oceny i mogą ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat korzystania z zestawu .NET SDK usługi Azure Search zobacz [.NET porad](search-howto-dotnet-sdk.md).

Chętnie poznamy Twoją opinię na zestawie SDK. Jeśli napotkasz problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli znajdziesz błąd, możesz Prześlij zgłoszenie do [repozytorium Azure .NET SDK w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że prefiks tytuł problemu z usługą "[Azure Search]".

Dziękujemy za pomocą usługi Azure Search.
