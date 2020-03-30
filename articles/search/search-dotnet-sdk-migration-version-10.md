---
title: Uaktualnienie do narzędzia Azure Cognitive Search .NET SDK w wersji 10
titleSuffix: Azure Cognitive Search
description: Migruj kod do narzędzia Azure Cognitive Search .NET SDK w wersji 10 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany kodu są wymagane.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847518"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Uaktualnienie do narzędzia Azure Cognitive Search .NET SDK w wersji 10

Jeśli używasz wersji 9.0 lub starszej z [usługi Azure Search .NET SDK,](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 10.

Usługa Azure Search została zmieniona na Azure Cognitive Search w wersji 10, ale obszary nazw i nazwy pakietów pozostają niezmienione. Poprzednie wersje SDK (9.0 i wcześniej) nadal używać poprzedniej nazwy. Aby uzyskać więcej informacji na temat korzystania z SDK, w tym przykłady, zobacz [Jak używać usługi Azure Cognitive Search z aplikacji .NET.](search-howto-dotnet-sdk.md)

Wersja 10 dodaje kilka funkcji i poprawek błędów, doprowadzając ją do `2019-05-06`tego samego poziomu funkcjonalnego, co najnowsza wersja wersji interfejsu API REST. W przypadkach, gdy zmiana łamie istniejący kod, przejdziemy przez [kroki wymagane do rozwiązania problemu.](#UpgradeSteps)

> [!NOTE]
> Jeśli używasz wersji 8.0-preview lub starszej, należy najpierw uaktualnić do wersji 9, a następnie uaktualnić do wersji 10. Aby uzyskać instrukcje, zobacz [uaktualnianie do narzędzia Azure Search .NET SDK w wersji 9.](search-dotnet-sdk-migration-version-9.md)
>
> Wystąpienie usługi wyszukiwania obsługuje kilka wersji interfejsu API REST, w tym najnowszą. Możesz nadal używać wersji, gdy nie jest już najnowsza, ale zaleca się migrację kodu w celu użycia najnowszej wersji. Podczas korzystania z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru wersji interfejsu api. Podczas korzystania z .NET SDK, wersja SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego sdk, można kontynuować uruchamianie tego kodu bez zmian, nawet jeśli usługa jest uaktualniany do obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Co nowego w wersji 10
Wersja 10 narzędzia Azure Cognitive Search .NET SDK jest przeznaczona`2019-05-06`dla najnowszej ogólnie dostępnej wersji interfejsu API REST ( ) z tymi aktualizacjami:

* Wprowadzenie dwóch nowych umiejętności - [umiejętność warunkowa](cognitive-search-skill-conditional.md) i [tłumaczenie tekstu.](cognitive-search-skill-text-translation.md)
* Dane wejściowe [umiejętności Shaper](cognitive-search-skill-shaper.md) zostały zrestrukturyzowane w celu uwzględnienia konsolidacji z kontekstów zagnieżdżonych. Aby uzyskać więcej informacji, zobacz ten [przykład definicji JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Dodanie dwóch nowych [funkcji mapowania pól:](search-indexer-field-mappings.md)
    - [Urlencode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [kod urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* W niektórych przypadkach błędy i ostrzeżenia, które pojawiają się w [stanie wykonywania indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) może mieć dodatkowe szczegóły, które pomagają w debugowaniu. `IndexerExecutionResult`został zaktualizowany w celu odzwierciedlenia tego zachowania.
* Poszczególne umiejętności zdefiniowane w [zamówieniu](cognitive-search-defining-skillset.md) można `name` opcjonalnie zidentyfikować, określając właściwość.
* `ServiceLimits`pokazuje limity dla `IndexerExecutionInfo` [typów złożonych](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) i pokazuje odpowiednie limity/przydziały indeksatora.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki uaktualnienia

1. Zaktualizuj `Microsoft.Azure.Search` odwołanie NuGet do użycia konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając opcję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

2. Po pobraniu nowych pakietów i ich zależności, odbuduj projekt. 

3. Jeśli kompilacja nie powiedzie się, należy naprawić każdy błąd kompilacji. Zobacz [Przerywanie zmian w wersji 10,](#ListOfChanges) aby uzyskać szczegółowe informacje na temat rozwiązywania każdego potencjalnego błędu kompilacji.

4. Po usunięciu błędów kompilacji lub ostrzeżeń, można wprowadzić zmiany w aplikacji, aby skorzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w SDK są szczegółowo opisane w [Co nowego w wersji 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Przełomowe zmiany w wersji 10

Istnieje kilka przełomowych zmian w wersji 10, które mogą wymagać zmian w kodzie oprócz przebudowy aplikacji.

> [!NOTE]
> Poniższa lista zmian nie jest wyczerpująca. Niektóre zmiany prawdopodobnie nie spowoduje błędów kompilacji, ale są technicznie łamanie, ponieważ łamią zgodność binarną z zestawami, które zależą od wcześniejszych wersji zestawów azure cognitive search .NET SDK. Istotne zmiany, które należą do tej kategorii są również wymienione wraz z zaleceniami. Należy odbudować aplikację podczas uaktualniania do wersji 10, aby uniknąć problemów ze zgodnością binarnych.

### <a name="custom-web-api-skill-definition"></a>Niestandardowa definicja umiejętności interfejsu API sieci Web

Definicja [umiejętności Niestandardowy interfejs API sieci Web](cognitive-search-custom-skill-web-api.md) została niepoprawnie określona w wersji 9 i starszej. 

Model określony `WebApiSkill` `HttpHeaders` jako właściwość obiektu, która _zawiera_ słownik. Tworzenie zestaw umiejętności z `WebApiSkill` skonstruowane w ten sposób spowodowałoby wyjątek, ponieważ interfejs API REST uzna żądanie źle utworzone. Ten problem został rozwiązany, `HttpHeaders` tworząc **właściwość słownika najwyższego poziomu** na samym `WebApiSkill` modelu — który jest uważany za prawidłowe żądanie z interfejsu API REST.

Na przykład, jeśli wcześniej podjęto próbę `WebApiSkill` wystąpienia wystąpienia w następujący sposób:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

aby uniknąć błędu sprawdzania poprawności z interfejsu API REST:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Umiejętność Shaper umożliwia konsolidację zagnieżdżonego kontekstu

Umiejętność Shaper umożliwia teraz konsolidację danych wejściowych z zagnieżdżonych kontekstów. Aby włączyć tę zmianę, `InputFieldMappingEntry` zmodyfikowaliśmy, tak aby można `Source` było utworzyć `SourceContext` wystąpienie, określając tylko właściwość lub właściwości i. `Inputs`

Najprawdopodobniej nie trzeba wprowadzać żadnych zmian w kodzie; należy jednak pamiętać, że tylko jedna z tych dwóch kombinacji jest dozwolona. Oznacza to:

- Tworzenie, `InputFieldMappingEntry` gdzie `Source` tylko jest inicjowany jest prawidłowy.
- Tworzenie tylko `InputFieldMappingEntry` gdzie `SourceContext` `Inputs` i są inicjowane jest prawidłowy.
- Wszystkie inne kombinacje obejmujące te trzy właściwości są nieprawidłowe.

Jeśli zdecydujesz się rozpocząć korzystanie z tej nowej funkcji, upewnij się, że wszyscy klienci są aktualizowane do korzystania z wersji 10, przed wprowadzeniem tej zmiany. W przeciwnym razie istnieje możliwość, że aktualizacja przez klienta (przy użyciu starszej wersji SDK) do Shaper umiejętności może spowodować błędy sprawdzania poprawności.

> [!NOTE]
> Mimo że `InputFieldMappingEntry` model bazowy został zmodyfikowany, aby umożliwić konsolidację z kontekstów zagnieżdżonych, jego użycie jest prawidłowe tylko w ramach definicji shaper umiejętności. Korzystanie z tej funkcji w innych umiejętności, podczas gdy prawidłowe w czasie kompilacji, spowoduje błąd sprawdzania poprawności w czasie wykonywania.

## <a name="skills-can-be-identified-by-a-name"></a>Umiejętności można identyfikować za pomocą nazwy

Każda umiejętność w zestaw umiejętności `Name`ma teraz nową właściwość, które mogą być zainicjowane w kodzie, aby pomóc zidentyfikować umiejętności. Jest to opcjonalne — gdy nieokreślone (co jest ustawieniem domyślnym, jeśli nie dokonano jawnej zmiany kodu), jest przypisywana domyślna nazwa przy użyciu indeksu 1 oparte umiejętności w skillset, poprzedzone znakiem "#". Na przykład w następującej definicji zestawprocesu (większość inicjacji pominięte dla zwięzłości):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`jest przypisana `#1`nazwa `WebApiSkill` , `#2`jest `ShaperSkill` przypisana `#3` , jest przypisana i tak dalej.

Jeśli zdecydujesz się zidentyfikować umiejętności za pomocą nazwy niestandardowej, upewnij się, że najpierw zaktualizuj wszystkie wystąpienia klientów do wersji 10 sdk. W przeciwnym razie istnieje możliwość, że klient przy użyciu starszej wersji zestawu SDK może `null` się `Name` właściwości umiejętności, powodując klienta do spadku z powrotem na domyślny schemat nazewnictwa.

## <a name="details-about-errors-and-warnings"></a>Szczegółowe informacje o błędach i ostrzeżeniach

`ItemError`i `ItemWarning` modele, które hermetyzują szczegóły błędów i ostrzeżeń (odpowiednio), które występują podczas wykonywania indeksatora zostały zmodyfikowane w celu uwzględnienia trzech nowych właściwości z celem pomocy w debugowaniu indeksatora. Te właściwości są następujące:

- `Name`: Nazwa źródła, z którego pochodzi błąd. Na przykład może odnosić się do określonej umiejętności w dołączonym zestawem umiejętności.
- `Details`: Dodatkowe szczegółowe informacje o błędzie lub ostrzeżeniu.
- `DocumentationLink`: łącze do przewodnika rozwiązywania problemów z określonym błędem lub ostrzeżeniem.

> [!NOTE]
> Zaczęliśmy struktury naszych błędów i ostrzeżeń, aby uwzględnić te przydatne szczegóły, gdy tylko jest to możliwe. Pracujemy nad tym, aby upewnić się, że w przypadku wszystkich błędów i ostrzeżeń te szczegóły są obecne, ale jest to praca w toku i te dodatkowe szczegóły nie zawsze mogą być wypełniane.

## <a name="next-steps"></a>Następne kroki

- Zmiany w umiejętności Shaper mają największy potencjalny wpływ na nowy lub istniejący kod. W następnym kroku należy ponownie przyjrzeć się temu przykładowi ilustrującemu strukturę wejściową: [Shaper skill JSON definition example](cognitive-search-skill-shaper.md)
- Przejdź przez [przegląd wzbogacania AI](cognitive-search-concept-intro.md).
- Czekamy na Wasze opinie na temat SDK. Jeśli napotkasz problemy, nie krępuj się poprosić nas o pomoc w [stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem za pomocą "[Azure Cognitive Search]".

