---
title: Uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 5 - usługi Azure Search
description: Migrowanie kodu do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 5 ze starszych wersji. Poznaj nowe funkcje i zmiany kodu, które są wymagane.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7684aa79ac9f58c2a047b01a6d9f5263795221d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291711"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Uaktualnianie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 5
Jeśli używasz wersji 4.0 preview lub starszy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnić aplikację do używania w wersji 5.

Aby uzyskać ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [jak używać usługi Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Zestaw .NET SDK usługi Azure Search w wersji 5 zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim pomocnicza, dzięki czemu Zmienianie kodu należy wymagać tylko minimalnym nakładzie pracy. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) zawiera instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 2.0 w wersji zapoznawczej lub starszej, należy najpierw uaktualnić do wersji 3 i dokona uaktualnienia do wersji 5. Zobacz [uaktualnienie do zestawu .NET SDK rozszerzenia usługi Azure Search w wersji 3](search-dotnet-sdk-migration.md) instrukcje.
>
> Wystąpienia usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Można użyć wersji, gdy nie jest już najnowsze, ale zaleca się przeprowadzenie migracji kodu, aby użyć najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru api-version. Korzystając z zestawu .NET SDK, wersję zestawu SDK, w przypadku używania określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszy zestaw SDK, można nadal uruchomić ten kod bez konieczności wprowadzania zmian, nawet wtedy, gdy usługa zostanie uaktualniony do nowszej wersji interfejsu API do obsługi.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co nowego w wersji 5
Zestaw .NET SDK usługi Azure Search w wersji 5 jest przeznaczony dla najnowszej ogólnie dostępnej wersji Azure Search REST API, w szczególności 2017-11-11. Dzięki temu można korzystać z nowych funkcji usługi Azure Search z poziomu aplikacji .NET, w tym następujące:

* [Synonimy](search-synonyms.md).
* Teraz dostępny programowo ostrzeżeń w historię wykonywania indeksatora (zobacz `Warning` właściwość `IndexerExecutionResult` w [dokumentacja platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) Aby uzyskać więcej informacji).
* Obsługa platformy .NET Core 2.
* Nowa struktura pakietu obsługuje przy użyciu tylko te części zestawu SDK, które są potrzebne (zobacz [przełomowe zmiany w wersji 5](#ListOfChanges) Aby uzyskać szczegółowe informacje).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety i ich zależności, należy ponownie skompilować projekt. W zależności od struktury kodu jego może odbudować pomyślnie. Jeśli tak, jesteś gotowy!

Jeśli kompilacja nie powiedzie się, powinien zostać wyświetlony błąd kompilacji, jak pokazano poniżej:

    The name 'SuggesterSearchMode' does not exist in the current context

Następnym krokiem jest, aby naprawić ten błąd kompilacji. Zobacz [przełomowe zmiany w wersji 5](#ListOfChanges) szczegółowe informacje o co powoduje błąd i jak go naprawić.

Należy pamiętać, że z powodu zmian w opakowanie zestawu .NET SDK usługi Azure Search, należy ponownie skompilować aplikację, aby użyć wersji 5. Zmiany te są szczegółowo opisane w [przełomowe zmiany w wersji 5](#ListOfChanges).

Może zostać wyświetlony ostrzeżenia kompilacji dodatkowe dotyczące przestarzałe metody lub właściwości. Ostrzeżenia zawierają instrukcje dotyczące rozwiązania do zastosowania zamiast przestarzałych funkcji. Na przykład, jeśli aplikacja używa `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metody powinna pojawić się ostrzeżenie, że jest wyświetlany komunikat "to zachowanie jest teraz włączone domyślnie, więc wywołanie tej metody nie jest już konieczne."

Gdy problem został rozwiązany żadnych ostrzeżeń ani błędów kompilacji, można wprowadzić zmiany do aplikacji w taki sposób, aby móc korzystać z nowych funkcji, jeśli chcesz, aby. Nowe funkcje w zestawie SDK są szczegółowo opisane w [nowości w wersji 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Istotne zmiany w wersji 5

### <a name="new-package-structure"></a>Nowa struktura pakietu

Najbardziej znaczące istotnej zmiany w wersji 5 jest to, że `Microsoft.Azure.Search` zestawu i jego zawartość, ma został podzielony na cztery oddzielne zestawy, które są obecnie dystrybuowane jako cztery oddzielne pakiety NuGet:

 - `Microsoft.Azure.Search`: Jest to meta pakiet, który zawiera wszystkie inne pakiety usługi Azure Search jako zależności. Jeśli wykonujesz uaktualnienie z wcześniejszej wersji zestawu SDK, po prostu uaktualnianie tego pakietu i ponowne utworzenie powinny być wystarczająco dużo, aby rozpocząć korzystanie z nowej wersji.
 - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli tworzysz aplikację .NET za pomocą usługi Azure Search i wystarczy tylko zapytania lub aktualizowanie dokumentów w indeksów. Jeśli trzeba będzie również tworzenie lub aktualizowanie indeksów, map synonimów lub innych zasobów dotyczących poziomu usług, należy użyć `Microsoft.Azure.Search` zamiast tego pakietu.
 - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli tworzysz automatyzacji na platformie .NET do zarządzania indeksów usługi Azure Search, map synonimów, indeksatory, źródła danych lub innych zasobów na poziomie usługi. Jeśli wymagane jest tylko do dokumentów zapytania lub aktualizacji w indeksów, użyj `Microsoft.Azure.Search.Data` zamiast tego pakietu. Jeśli potrzebujesz wszystkich funkcji usługi Azure Search, użyj `Microsoft.Azure.Search` zamiast tego pakietu.
 - `Microsoft.Azure.Search.Common`: Popularne typy wymagane przez bibliotekami .NET usługi Azure Search. Nie należy używać tego pakietu bezpośrednio w aplikacji; Jest przeznaczone tylko do użycia jako zależność.
 
Ta zmiana z technicznego punktu widzenia jest istotne, ponieważ wiele typów zostały przeniesione między zestawami. Jest to, dlaczego ponownie skompilować aplikację jest niezbędne w celu uaktualniania do wersji 5 zestawu SDK.

Istnieje niewielka liczba innych przełomowych zmian wprowadzonych w wersji 5, które mogą wymagać kodu zmienia oprócz ponownie skompilować aplikację.

### <a name="change-to-suggesters"></a>Zmień na Sugestory 

`Suggester` Konstruktor nie ma już `enum` parametr `SuggesterSearchMode`. To wyliczenie tylko miał jedną wartością i dlatego został nadmiarowe. Jeśli widzisz błędy w wyniku tego kompilacji, po prostu usunąć odwołania do `SuggesterSearchMode` parametru.

### <a name="removed-obsolete-members"></a>Usunięto przestarzali członkowie

Może zostać wyświetlony błędy związane z metodami lub właściwościami, które zostały oznaczone jako przestarzałe w starszych wersjach, a później usunięte w wersji 5 kompilacji. Jeśli wystąpią błędy takie, w tym miejscu przedstawiono sposób ich rozwiązywania:

- Jeśli używano `IndexingParametersExtensions.IndexStorageMetadataOnly` metody, użyj `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` zamiast tego.
- Jeśli używano `IndexingParametersExtensions.SkipContent` metody, użyj `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` zamiast tego.

### <a name="removed-preview-features"></a>Usunięto w wersji zapoznawczej

Jeśli uaktualniasz od wersji 4.0-preview w wersji 5, należy pamiętać, że tablicę JSON i analizy obsługę indeksatory obiektów Blob CSV został usunięty, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególności następujące metody `IndexingParametersExtensions` klasy zostały usunięte:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardych zależność od tych funkcji, nie można uaktualnić do wersji 5 zestawu .NET SDK usługi Azure Search. Można nadal używać wersji 4.0-preview. Jednak należy pamiętać, że **nie zaleca się przy użyciu zestawów SDK wersji zapoznawczej w aplikacjach produkcyjnych**. Funkcje w wersji zapoznawczej są tylko do oceny i mogą ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat korzystania z zestawu .NET SDK usługi Azure Search zobacz [.NET porad](search-howto-dotnet-sdk.md).

Chętnie poznamy Twoją opinię na zestawie SDK. Jeśli napotkasz problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli znajdziesz błąd, możesz Prześlij zgłoszenie do [repozytorium Azure .NET SDK w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że prefiks tytuł problemu z usługą "[Azure Search]".

Dziękujemy za pomocą usługi Azure Search.
