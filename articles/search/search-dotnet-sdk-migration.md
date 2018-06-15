---
title: Uaktualnianie do usługi Azure Search .NET SDK w wersji 3 | Dokumentacja firmy Microsoft
description: Uaktualnianie do usługi Azure Search .NET SDK w wersji 3
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: 161d22e0ff4ec4ab28107919a80ecc48cd027967
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793481"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Uaktualnianie do usługi Azure Search .NET SDK w wersji 3
Jeśli używasz wersji 2.0 — wersja zapoznawcza lub starszy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnienie aplikacji w wersji 3.

Aby uzyskać bardziej ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [sposobu korzystania z usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

Zestaw .NET SDK usługi Azure Search w wersji 3 zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim niewielkie, tak więc zmiana kodu powinny wymagać tylko minimalnym wysiłku. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 1.0.2-preview lub starsze, należy uaktualnić system do wersji 1.1 pierwszy, a następnie uaktualnić do wersji 3. Zobacz [uaktualnienie do zestawu .NET SDK w wersji 1.1 Azure Search](search-dotnet-sdk-migration-version-1.md) instrukcje.
>
> Wystąpienia usługi Azure Search obsługuje różne wersje interfejsu API REST, w tym najnowsze. Możesz użyć wersji, gdy nie jest już najnowszego, ale zaleca się przeprowadzenie migracji swój kod, aby używać najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każde żądanie za pośrednictwem parametru api-version. Podczas korzystania z zestawu .NET SDK, wersja zestawu SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszej zestawu SDK można kontynuować do uruchomienia kodu bez zmian, nawet jeśli usługa nie zostanie uaktualniony do nowszej wersji interfejsu API obsługuje.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Nowości w wersji 3
Wersja 3 zestawu .NET SDK usługi Azure Search jest przeznaczony dla najnowszej wersji ogólnie dostępna wyszukiwania interfejsu API REST Azure, w szczególności 2016-09-01. Dzięki temu można używać wielu nowych funkcji usługi Azure Search z aplikacji .NET, takie jak następujące:

* [Analizatory niestandardowe](https://aka.ms/customanalyzers)
* [Magazyn obiektów Blob Azure](search-howto-indexing-azure-blob-storage.md) i [Azure Table Storage](search-howto-indexing-azure-tables.md) indeksatorze obsługi
* Dostosowywanie indeksatora za pośrednictwem [mapowań pól](search-indexer-field-mappings.md)
* Elementy etag obsługuje umożliwienie bezpiecznego aktualizowania równoczesnych definicje indeksów, indeksatorów i źródeł danych
* Obsługę tworzenia indeksu definicje pól deklaratywnie dekoracji klasy modelu, a następnie użyć nowego `FieldBuilder` klasy.
* Obsługa platformy .NET Core i przenośnych profilu platformy .NET 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety oraz ich zależności, ponownie skompiluj projekt. W zależności od struktury kodu jego może odbudować pomyślnie. Jeśli tak, wszystko jest gotowe!

W przypadku niepowodzenia kompilacji powinien zostać wyświetlony błąd kompilacji podobne do poniższych:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Następnym krokiem jest, aby naprawić ten błąd kompilacji. Zobacz [fundamentalne zmiany w wersji 3](#ListOfChanges) szczegółowe informacje o to, co powoduje błąd i sposobu jego usunięcia.

Może pojawić się dodatkowe kompilacji ostrzeżenia dotyczące przestarzałe metody lub właściwości. Ostrzeżenia zawiera instrukcje dotyczące sposobu użycia zamiast przestarzałych funkcji. Na przykład, jeśli aplikacja używa `IndexingParameters.Base64EncodeKeys` właściwość, należy pobrać ostrzeżenie informujące, `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Gdy problem został rozwiązany błędy kompilacji, można wprowadzić zmiany do aplikacji, aby móc korzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w zestawie SDK wyszczególnione w [nowości w wersji 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Fundamentalne zmiany w wersji 3
Istnieje niewielkiej liczby fundamentalne zmiany w wersji 3, które mogą wymagać kod zmienia oprócz ponownie skompilować aplikację.

### <a name="indexesgetclient-return-type"></a>Zwracany typ Indexes.GetClient
`Indexes.GetClient` Metoda ma nowy typ zwracany. Wcześniej, zwracana `SearchIndexClient`, ale ta zmiana została wprowadzona `ISearchIndexClient` w wersji 2.0-preview, a zmiany są przenoszone do wersji 3. Jest to do obsługi klientów, którzy chcą mock `GetClient` metodę dla testów jednostkowych przez zwrócenie implementację testową z `ISearchIndexClient`.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, typ danych i inne osoby nie są już umożliwiają niejawnej konwersji na ciągi
Istnieje wiele typów w zestawie SDK .NET wyszukiwania Azure pochodzi od `ExtensibleEnum`. Poprzednio zostały wszystkie te typy umożliwiają niejawnej konwersji na typ `string`. Jednak usterki zostało odnalezione `Object.Equals` implementacji dla tych klas i naprawienie usterki wymagane wyłączenie niejawnej konwersji. Jawna konwersja na `string` jest nadal dozwolone.

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

Aby to naprawić wszystkie błędy kompilacji, można zmienić go:

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

### <a name="removed-obsolete-members"></a>Usunięte przestarzali członkowie

Może zostać wyświetlony kompilacji błędy związane z metodami lub właściwościami, które były oznaczone jako przestarzałe w wersji 2.0 w wersji zapoznawczej i później usunięte w wersji 3. Jeśli wystąpią błędy takie, poniżej przedstawiono sposób ich rozwiązywania:

- W przypadku używania tego konstruktora: `ScoringParameter(string name, string value)`, zamiast tego użyj tego: `ScoringParameter(string name, IEnumerable<string> values)`
- W przypadku używania `ScoringParameter.Value` właściwości, należy zastosować `ScoringParameter.Values` właściwości lub `ToString` metody zamiast tego.
- W przypadku używania `SearchRequestOptions.RequestId` właściwości, należy zastosować `ClientRequestId` właściwości zamiast tego.

### <a name="removed-preview-features"></a>Funkcje usunięte w wersji zapoznawczej

Jeśli uaktualniasz z wersji 2.0-preview do wersji 3, należy pamiętać, że JSON i analizowania pomocy technicznej dla obiekt Blob indeksatorów CSV została usunięta, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególności z następujących metod `IndexingParametersExtensions` klasy zostały usunięte:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardych zależność od tych funkcji, nie można uaktualnić do wersji 3 zestawu .NET SDK usługi Azure Search. Można nadal używać wersji 2.0-preview. Jednak należy należy pamiętać, że **zaleca się używania Podgląd zestawów SDK w aplikacji produkcyjnych**. Funkcje w wersji zapoznawczej są tylko do oceny i może ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat używania zestawu .NET SDK usługi Azure Search, zobacz [.NET porad](search-howto-dotnet-sdk.md).

Chętnie poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli napotkasz błąd, można pliku problemu w [repozytorium GitHub zestawu SDK .NET usługi Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu "[wyszukiwanie Azure]" z prefiksu.

Dziękujemy za skorzystanie z usługi Azure Search!
