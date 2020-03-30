---
title: Uaktualnienie do usługi Azure Search .NET SDK w wersji 5
titleSuffix: Azure Cognitive Search
description: Migruj kod do narzędzia Azure Search .NET SDK w wersji 5 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany kodu są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793038"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Uaktualnienie do usługi Azure Search .NET SDK w wersji 5

Jeśli używasz wersji 4.0-preview lub starszych [usługi Azure Search .NET SDK,](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 5.

Aby uzyskać bardziej ogólne wskazówki dotyczące sdk, w tym przykłady, zobacz [Jak używać usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 5 narzędzia Azure Search .NET SDK zawiera pewne zmiany z wcześniejszych wersji. Są one w większości niewielkie, więc zmiana kodu powinna wymagać tylko minimalnego wysiłku. Zobacz [Kroki uaktualnienia, aby uzyskać](#UpgradeSteps) instrukcje dotyczące zmiany kodu, aby użyć nowej wersji SDK.

> [!NOTE]
> Jeśli używasz wersji 2.0-preview lub starszej, należy najpierw uaktualnić do wersji 3, a następnie uaktualnić do wersji 5. Aby uzyskać instrukcje, zobacz [uaktualnianie do narzędzia Azure Search .NET SDK w wersji 3.](search-dotnet-sdk-migration.md)
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowszą. Możesz nadal używać wersji, gdy nie jest już najnowsza, ale zaleca się migrację kodu w celu użycia najnowszej wersji. Podczas korzystania z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru wersji interfejsu api. Podczas korzystania z .NET SDK, wersja SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego sdk, można kontynuować uruchamianie tego kodu bez zmian, nawet jeśli usługa jest uaktualniany do obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co nowego w wersji 5
Wersja 5 narzędzia Azure Search .NET SDK jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST usługi Azure Search, w szczególności 2017-11-11. Dzięki temu można używać nowych funkcji usługi Azure Search z aplikacji .NET, w tym następujące:

* [Synonimy](search-synonyms.md).
* Teraz można programowo uzyskać dostęp do ostrzeżeń `Warning` w `IndexerExecutionResult` historii wykonywania indeksatora (zobacz właściwość w [.NET odwołania, aby](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) uzyskać więcej szczegółów).
* Obsługa platformy .NET Core 2.
* Nowa struktura pakietu obsługuje przy użyciu tylko części zestawu SDK, które są potrzebne (zobacz [Podziały zmian w wersji 5,](#ListOfChanges) aby uzyskać szczegółowe informacje).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki uaktualnienia
Najpierw zaktualizuj `Microsoft.Azure.Search` odwołanie NuGet do użycia konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając opcję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu nowych pakietów i ich zależności, odbuduj projekt. W zależności od struktury kodu może pomyślnie odbudować. Jeśli tak, jesteś gotowy do pracy!

Jeśli kompilacja nie powiedzie się, powinien zostać wyświetlony błąd kompilacji, podobny do następującego:

    The name 'SuggesterSearchMode' does not exist in the current context

Następnym krokiem jest naprawienie tego błędu kompilacji. Zobacz [Łamanie zmian w wersji 5,](#ListOfChanges) aby uzyskać szczegółowe informacje o tym, co powoduje błąd i jak go naprawić.

Należy pamiętać, że ze względu na zmiany w opakowaniu usługi Azure Search .NET SDK, należy odbudować aplikację, aby korzystać z wersji 5. Zmiany te są szczegółowo opisane w [Breaking changes in version 5](#ListOfChanges).

Mogą zostać wyświetlone dodatkowe ostrzeżenia kompilacji związane z przestarzałych metod lub właściwości. Ostrzeżenia będą zawierać instrukcje dotyczące tego, czego należy użyć zamiast przestarzałej funkcji. Na przykład jeśli aplikacja używa `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metody, należy otrzymasz ostrzeżenie, które mówi"To zachowanie jest teraz domyślnie włączone, więc wywołanie tej metody nie jest już konieczne."

Po usunięciu błędów kompilacji lub ostrzeżeń, można wprowadzić zmiany w aplikacji, aby skorzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w SDK są szczegółowo opisane w [Co nowego w wersji 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Przełomowe zmiany w wersji 5

### <a name="new-package-structure"></a>Nowa struktura pakietów

The most substantial breaking change in version 5 is that the `Microsoft.Azure.Search` assembly and its contents have been divided into four separate assemblies that are now distributed as four separate NuGet packages:

 - `Microsoft.Azure.Search`: Jest to meta-pakiet, który zawiera wszystkie inne pakiety usługi Azure Search jako zależności. Jeśli uaktualniasz z wcześniejszej wersji zestawu SDK, po prostu uaktualnienie tego pakietu i ponowne tworzenie powinno wystarczyć, aby rozpocząć korzystanie z nowej wersji.
 - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli tworzysz aplikację .NET przy użyciu usługi Azure Search i wystarczy tylko kwerendy lub zaktualizować dokumenty w indeksach. Jeśli trzeba również utworzyć lub zaktualizować indeksy, mapy synonimów lub inne `Microsoft.Azure.Search` zasoby na poziomie usługi, użyj pakietu zamiast tego.
 - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli tworzysz automatyzację w .NET do zarządzania indeksami usługi Azure Search, mapami synonimów, indeksatorami, źródłami danych lub innymi zasobami na poziomie usługi. Jeśli trzeba tylko kwerendy lub zaktualizować dokumenty w `Microsoft.Azure.Search.Data` indeksach, należy użyć pakietu zamiast tego. Jeśli potrzebujesz wszystkich funkcji usługi Azure Search, użyj `Microsoft.Azure.Search` pakietu zamiast tego.
 - `Microsoft.Azure.Search.Common`: Typowe typy wymagane przez biblioteki platformy Azure Search .NET. Nie należy używać tego pakietu bezpośrednio w aplikacji; Jest przeznaczony tylko do użycia jako zależność.
 
Ta zmiana jest technicznie łamanie, ponieważ wiele typów zostały przeniesione między zestawami. Dlatego przebudowa aplikacji jest konieczne w celu uaktualnienia do wersji 5 sdk.

Istnieje niewielka liczba innych przełomowych zmian w wersji 5, które mogą wymagać zmian kodu oprócz przebudowy aplikacji.

### <a name="change-to-suggesters"></a>Zmień na Sugestatorów 

Konstruktor `Suggester` nie ma `enum` już `SuggesterSearchMode`parametru dla . Wyliczenie to miało tylko jedną wartość i dlatego było zbędne. Jeśli widzisz błędy kompilacji w wyniku tego, po `SuggesterSearchMode` prostu usuń odwołania do parametru.

### <a name="removed-obsolete-members"></a>Usunięto przestarzałych członków

Mogą być widoczne błędy kompilacji związane z metodami lub właściwości, które zostały oznaczone jako przestarzałe we wcześniejszych wersjach, a następnie usunięte w wersji 5. Jeśli wystąpią takie błędy, oto jak je rozwiązać:

- Jeśli używasz `IndexingParametersExtensions.IndexStorageMetadataOnly` tej metody, użyj zamiast tego. `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)`
- Jeśli używasz `IndexingParametersExtensions.SkipContent` tej metody, użyj zamiast tego. `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)`

### <a name="removed-preview-features"></a>Usunięto funkcje podglądu

W przypadku uaktualniania z wersji 4.0-preview do wersji 5, należy pamiętać, że macierz JSON i obsługa analizowania CSV dla indeksatorów obiektów Blob została usunięta, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególności usunięto następujące metody `IndexingParametersExtensions` klasy:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardą zależność od tych funkcji, nie będzie można uaktualnić do wersji 5 usługi Azure Search .NET SDK. Można nadal korzystać z wersji 4.0-preview. Należy jednak pamiętać, że **nie zaleca się używania podglądu SDK w aplikacjach produkcyjnych.** Funkcje w wersji zapoznawczej służą wyłącznie do oceny i mogą ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz więcej informacji na temat korzystania z narzędzia Azure Search .NET SDK, zobacz [instrukcje .NET](search-howto-dotnet-sdk.md).

Czekamy na Wasze opinie na temat SDK. Jeśli napotkasz problemy, nie krępuj się poprosić nas o pomoc w [stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem za pomocą "[Usługi Azure Search]".

Dziękujemy za korzystanie z usługi Azure Search!
