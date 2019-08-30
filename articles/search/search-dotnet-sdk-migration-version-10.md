---
title: Uaktualnianie do Azure Search .NET SDK w wersji 10 — Azure Search
description: Migruj kod do Azure Search .NET SDK wersja 10 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
author: arv100kri
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: arjagann
ms.custom: seodec2018
ms.openlocfilehash: e4633a1c0543331b0ea9820703ed685fb99f2130
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182371"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-10"></a>Uaktualnianie do Azure Search .NET SDK wersja 10

Jeśli korzystasz z wersji 9,0 lub starszej [Azure Search .NET SDK](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnić aplikację pod kątem korzystania z wersji 10.

Aby zapoznać się z bardziej ogólnym przewodnikiem dotyczącym zestawu SDK, łącznie z przykładami, zobacz [jak używać Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 10 dodaje kilka funkcji i poprawek błędów, umożliwiając ich przekazanie na ten sam poziom funkcjonalności co Najnowsza wersja `2019-05-06`interfejsu API REST. W przypadkach, gdy zmiana przerywa istniejący kod, przeprowadzimy Cię przez [kroki wymagane do rozwiązania problemu](#UpgradeSteps).

> [!NOTE]
> Jeśli używasz wersji 8,0-Preview lub starszej, należy najpierw uaktualnić do wersji 9, a następnie uaktualnić do wersji 10. Aby uzyskać instrukcje [, zobacz Uaktualnianie do Azure Search .NET SDK w wersji 9](search-dotnet-sdk-migration-version-9.md) .
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Możesz nadal korzystać z wersji, gdy nie jest już Najnowsza, ale zalecamy przeprowadzenie migracji kodu w celu użycia najnowszej wersji. W przypadku korzystania z interfejsu API REST należy określić wersję interfejsu API w każdym żądaniu za pośrednictwem parametru API-Version. Podczas korzystania z zestawu SDK platformy .NET wersja zestawu SDK określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego zestawu SDK, możesz nadal uruchamiać ten kod bez zmian, nawet jeśli usługa zostanie uaktualniona w celu obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Co nowego w wersji 10
Wersja 10 zestawu SDK Azure Search .NET jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST Azure Search (`2019-05-06`) z tymi aktualizacjami:

* Wprowadzenie dwóch nowych umiejętności związanych z [](cognitive-search-skill-conditional.md) umiejętnościami i [tłumaczeniami tekstu](cognitive-search-skill-text-translation.md).
* Dane wejściowe [umiejętności kształtu](cognitive-search-skill-shaper.md) zostały poddane restrukturyzacji w celu uwzględnienia konsolidacji z zagnieżdżonych kontekstów. Aby uzyskać więcej informacji, zobacz tę [przykładową definicję JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Dodanie dwóch nowych [funkcji mapowania pól](search-indexer-field-mappings.md):
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* W niektórych przypadkach błędy i ostrzeżenia, które są wyświetlane w [stanie wykonywania indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) , mogą zawierać dodatkowe szczegóły, które pomagają w debugowaniu. `IndexerExecutionResult`została zaktualizowana w celu odzwierciedlenia tego zachowania.
* Poszczególne umiejętności zdefiniowane w ramach [zestawu umiejętności](cognitive-search-defining-skillset.md) można opcjonalnie zidentyfikować przez określenie `name` właściwości.
* `ServiceLimits`pokazuje limity dla [typów złożonych](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) i `IndexerExecutionInfo` pokazuje odpowiednie limity indeksowania/przydziały.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia

1. Zaktualizuj odwołanie do `Microsoft.Azure.Search` programu NuGet za pomocą konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając pozycję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

2. Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. 

3. Jeśli kompilacja zakończy się niepowodzeniem, należy naprawić każdy błąd kompilacji. Zobacz istotne [zmiany w wersji 10](#ListOfChanges) , aby uzyskać szczegółowe informacje na temat sposobu rozwiązywania każdego potencjalnego błędu kompilacji.

4. Po naprawieniu błędów kompilacji lub ostrzeżeń możesz wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. Nowe funkcje zestawu SDK są szczegółowo opisane w temacie [co nowego w wersji 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Istotne zmiany w wersji 10

Istnieje kilka istotnych zmian w wersji 10, które mogą wymagać zmiany kodu oprócz ponownego kompilowania aplikacji.

> [!NOTE]
> Lista zmian poniżej nie jest wyczerpująca. Niektóre zmiany prawdopodobnie nie spowodują błędów kompilacji, ale są technicznie przerywane, ponieważ przerwają zgodność binarną z zestawami, które są zależne od wcześniejszych wersji zestawów Azure Search .NET SDK. Istotne zmiany, które znajdują się w tej kategorii, znajdują się również na liście wraz z zaleceniami. Skompiluj ponownie aplikację podczas uaktualniania do wersji 10, aby uniknąć problemów ze zgodnością binarną.

### <a name="custom-web-api-skill-definition"></a>Niestandardowa definicja umiejętności internetowego interfejsu API

Definicja [niestandardowej umiejętności internetowego interfejsu API](cognitive-search-custom-skill-web-api.md) została niepoprawnie określona w wersji 9 i starszych. 

Model `WebApiSkill` określony `HttpHeaders` jako właściwość obiektu, która _zawiera_ słownik. Utworzenie zestawu umiejętności z `WebApiSkill` skonstruowaną w ten sposób spowoduje wyjątek, ponieważ interfejs API REST rozważy niewłaściwie sformułowane żądanie. Ten problem został poprawiony, tworząc `HttpHeaders` `WebApiSkill` **Właściwość słownika najwyższego poziomu** w samym modelu, która jest uznawana za prawidłowe żądanie z interfejsu API REST.

Na przykład, jeśli wcześniej podjęto próbę utworzenia wystąpienia `WebApiSkill` w następujący sposób:

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

Zmień go na następujące, aby uniknąć błędu walidacji z interfejsu API REST:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Umiejętność kształtu pozwala na konsolidację kontekstów zagnieżdżonych

Umiejętność kształtu może teraz zezwalać na konsolidację danych wejściowych z kontekstów zagnieżdżonych. Aby włączyć tę zmianę, należy ją `InputFieldMappingEntry` zmodyfikować, aby można było utworzyć jej wystąpienie przez określenie `Source` tylko właściwości lub `SourceContext` właściwości i `Inputs` .

Najprawdopodobniej nie musisz wprowadzać żadnych zmian w kodzie; należy jednak zauważyć, że dozwolone są tylko jedną z tych dwóch kombinacji. Oznacza to:

- Tworzenie elementu `InputFieldMappingEntry` , `Source` gdzie jest inicjowany, jest prawidłowe.
- Tworzenie elementu `InputFieldMappingEntry` `SourceContext` Where i`Inputs` -initializes jest prawidłowy.
- Wszystkie inne kombinacje obejmujące te trzy właściwości są nieprawidłowe.

Jeśli zdecydujesz się rozpocząć korzystanie z tej nowej funkcji, przed wprowadzeniem tej zmiany upewnij się, że wszyscy klienci zostali zaktualizowani do wersji 10. W przeciwnym razie istnieje możliwość, że aktualizacja przez klienta (przy użyciu starszej wersji zestawu SDK) do umiejętności kształtu może spowodować błędy walidacji.

> [!NOTE]
> Mimo że model źródłowy `InputFieldMappingEntry` został zmodyfikowany w celu umożliwienia konsolidacji z kontekstów zagnieżdżonych, jego użycie jest prawidłowe tylko w ramach definicji umiejętności kształtu. Korzystanie z tej możliwości w innych umiejętnościach, gdy jest to ważne w czasie kompilacji, spowoduje błąd walidacji w czasie wykonywania.

## <a name="skills-can-be-identified-by-a-name"></a>Kwalifikacje mogą być identyfikowane przez nazwę

Każda umiejętność w zestawu umiejętności ma teraz nową właściwość `Name`, która może zostać zainicjowana w kodzie, aby ułatwić identyfikację umiejętności. Jest to opcjonalne — Jeśli nie określono tego parametru (co jest ustawieniem domyślnym, jeśli nie wprowadzono żadnej jawnej zmiany kodu), jest przypisywana nazwa domyślna przy użyciu indeksu "zestawu umiejętności" w ramach umiejętności, poprzedzonej znakiem "#". Na przykład w następującej definicji zestawu umiejętności (większość inicjacji pominiętych dla zwięzłości):

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

`SentimentSkill`ma przypisaną nazwę `#1`, `WebApiSkill` `#2` jest`ShaperSkill` przypisana`#3` i tak dalej.

W przypadku wybrania opcji identyfikacji umiejętności według nazwy niestandardowej upewnij się, że wszystkie wystąpienia klientów zostały najpierw zaktualizowane do wersji 10 zestawu SDK. W przeciwnym razie istnieje możliwość, że klient korzystający ze starszej wersji zestawu SDK może `null` `Name` wyróżnić Właściwość umiejętności, powodując, że klient powróci do domyślnego schematu nazewnictwa.

## <a name="details-about-errors-and-warnings"></a>Szczegóły dotyczące błędów i ostrzeżeń

`ItemError`i `ItemWarning` modele, które hermetyzują szczegóły błędów i ostrzeżeń (odpowiednio), które wystąpiły podczas wykonywania indeksatora, są modyfikowane w celu uwzględnienia trzech nowych właściwości, które mają na celu pomoc w debugowaniu indeksatora. Te właściwości są następujące:

- `Name`: Nazwa źródła, z którego pochodzi błąd. Na przykład może odnosić się do określonej umiejętności w dołączonym zestawu umiejętności.
- `Details`: Dodatkowe pełne szczegóły dotyczące błędu lub ostrzeżenia.
- `DocumentationLink`: Link do przewodnika rozwiązywania problemów dotyczących określonego błędu lub ostrzeżenia.

> [!NOTE]
> Rozpoczęto tworzenie struktury błędów i ostrzeżeń w celu uwzględnienia tych przydatnych szczegółowych informacji, gdy jest to możliwe. Pracujemy nad tym, że w przypadku wszystkich błędów i ostrzeżeń te szczegóły są obecne, ale jest to praca w toku i te dodatkowe szczegóły mogą nie zawsze zostać wypełnione.

## <a name="next-steps"></a>Następne kroki

- Zmiany w umiejętnościach kształtu mają największy wpływ na nowy lub istniejący kod. W następnym kroku Pamiętaj, aby ponownie odwiedzić ten przykład ilustrujący strukturę wejściową: [Przykład definicji JSON kwalifikacji kształtu](cognitive-search-skill-shaper.md)
- Przejdź do [przewodnika Wprowadzenie do wyszukiwania poznawczego](cognitive-search-concept-intro.md).
- Poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, skontaktuj się z nami, aby uzyskać pomoc dotyczącą [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem "[Azure Search]".

