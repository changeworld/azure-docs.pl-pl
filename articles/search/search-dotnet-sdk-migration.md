---
title: Uaktualnienie do usługi Azure Search .NET SDK w wersji 3
titleSuffix: Azure Cognitive Search
description: Migruj kod do narzędzia Azure Search .NET SDK w wersji 3 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany kodu są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792978"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Uaktualnienie do usługi Azure Search .NET SDK w wersji 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Jeśli używasz wersji 2.0-preview lub starszych [usługi Azure Search .NET SDK,](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 3.

Aby uzyskać bardziej ogólne wskazówki dotyczące sdk, w tym przykłady, zobacz [Jak używać usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 3 narzędzia Azure Search .NET SDK zawiera pewne zmiany z wcześniejszych wersji. Są one w większości niewielkie, więc zmiana kodu powinna wymagać tylko minimalnego wysiłku. Zobacz [Kroki uaktualnienia, aby uzyskać](#UpgradeSteps) instrukcje dotyczące zmiany kodu, aby użyć nowej wersji SDK.

> [!NOTE]
> Jeśli używasz wersji 1.0.2-preview lub starszej, należy najpierw uaktualnić do wersji 1.1, a następnie uaktualnić do wersji 3. Aby uzyskać instrukcje, zobacz [uaktualnianie do narzędzia Azure Search .NET SDK w wersji 1.1.](search-dotnet-sdk-migration-version-1.md)
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowszą. Możesz nadal używać wersji, gdy nie jest już najnowsza, ale zaleca się migrację kodu w celu użycia najnowszej wersji. Podczas korzystania z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru wersji interfejsu api. Podczas korzystania z .NET SDK, wersja SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego sdk, można kontynuować uruchamianie tego kodu bez zmian, nawet jeśli usługa jest uaktualniany do obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co nowego w wersji 3
Wersja 3 narzędzia Azure Search .NET SDK jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST usługi Azure Search, w szczególności 2016-09-01. Dzięki temu można korzystać z wielu nowych funkcji usługi Azure Search z aplikacji .NET, w tym następujące:

* [Analizatory niestandardowe](https://aka.ms/customanalyzers)
* [Obsługa usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) i [usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
* Dostosowywanie indeksatora za pomocą [mapowań pól](search-indexer-field-mappings.md)
* Obsługa tagów ETagów w celu umożliwienia bezpiecznej równoczesnego aktualizowania definicji indeksów, indeksatorów i źródeł danych
* Obsługa definicji pól indeksu budynku deklaratywnie przez `FieldBuilder` dekorowanie klasy modelu i przy użyciu nowej klasy.
* Obsługa profilu przenośnego .NET Core i .NET 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki uaktualnienia
Najpierw zaktualizuj `Microsoft.Azure.Search` odwołanie NuGet do użycia konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając opcję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu nowych pakietów i ich zależności, odbuduj projekt. W zależności od struktury kodu może pomyślnie odbudować. Jeśli tak, jesteś gotowy do pracy!

Jeśli kompilacja nie powiedzie się, powinien zostać wyświetlony błąd kompilacji, podobny do następującego:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Następnym krokiem jest naprawienie tego błędu kompilacji. Zobacz [Łamanie zmian w wersji 3,](#ListOfChanges) aby uzyskać szczegółowe informacje o tym, co powoduje błąd i jak go naprawić.

Mogą zostać wyświetlone dodatkowe ostrzeżenia kompilacji związane z przestarzałych metod lub właściwości. Ostrzeżenia będą zawierać instrukcje dotyczące tego, czego należy użyć zamiast przestarzałej funkcji. Na przykład, jeśli aplikacja `IndexingParameters.Base64EncodeKeys` korzysta z właściwości, należy otrzymasz ostrzeżenie, które mówi`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Po usunięciu błędów kompilacji można wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w SDK są szczegółowo opisane [w Co nowego w wersji 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Przełomowe zmiany w wersji 3
Istnieje niewielka liczba przełomowych zmian w wersji 3, które mogą wymagać zmian kodu oprócz przebudowy aplikacji.

### <a name="indexesgetclient-return-type"></a>Typ zwrotu Indexes.GetClient
Metoda `Indexes.GetClient` ma nowy typ zwracany. Wcześniej wrócił `SearchIndexClient`, ale to zostało `ISearchIndexClient` zmienione w wersji 2.0-preview, a ta zmiana przenosi się do wersji 3. Ma to na celu obsługę `GetClient` klientów, którzy chcą szydzić z `ISearchIndexClient`metody testów jednostkowych, zwracając makietę implementacji .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType i inne nie są już niejawnie konwertowane na ciągi
Istnieje wiele typów w usłudze Azure Search .NET `ExtensibleEnum`SDK, które pochodzą od . Wcześniej wszystkie te typy były niejawnie konwertowane na typ `string`. Jednak błąd został wykryty `Object.Equals` w implementacji dla tych klas i naprawienie błędu wymaga wyłączenia tej niejawnej konwersji. Jawna `string` konwersja jest nadal dozwolona.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

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

Można go zmienić w ten sposób, aby naprawić błędy kompilacji:

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

### <a name="removed-obsolete-members"></a>Usunięto przestarzałych członków

Mogą zostać wyświetlone błędy kompilacji związane z metodami lub właściwościami, które zostały oznaczone jako przestarzałe w wersji 2.0-preview, a następnie usunięte w wersji 3. Jeśli wystąpią takie błędy, oto jak je rozwiązać:

- Jeśli używasz tego konstruktora: `ScoringParameter(string name, string value)`, użyj tego zamiast:`ScoringParameter(string name, IEnumerable<string> values)`
- Jeśli używasz `ScoringParameter.Value` właściwości, użyj `ScoringParameter.Values` właściwości `ToString` lub metody zamiast.
- Jeśli używasz `SearchRequestOptions.RequestId` właściwości, użyj `ClientRequestId` właściwości zamiast.

### <a name="removed-preview-features"></a>Usunięto funkcje podglądu

W przypadku uaktualniania z wersji 2.0-preview do wersji 3, należy pamiętać, że JSON i CSV analizy obsługi indeksatorów obiektów blob został usunięty, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególności usunięto następujące metody `IndexingParametersExtensions` klasy:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardą zależność od tych funkcji, nie będzie można uaktualnić do wersji 3 usługi Azure Search .NET SDK. Można nadal korzystać z wersji 2.0-preview. Należy jednak pamiętać, że **nie zaleca się używania podglądu SDK w aplikacjach produkcyjnych.** Funkcje w wersji zapoznawczej służą wyłącznie do oceny i mogą ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz więcej informacji na temat korzystania z narzędzia Azure Search .NET SDK, zobacz [instrukcje .NET](search-howto-dotnet-sdk.md).

Czekamy na Wasze opinie na temat SDK. Jeśli napotkasz problemy, nie krępuj się poprosić nas o pomoc w [stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem za pomocą "[Usługi Azure Search]".

Dziękujemy za korzystanie z usługi Azure Search!
