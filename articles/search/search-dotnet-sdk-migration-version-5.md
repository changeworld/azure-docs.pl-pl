---
title: Uaktualnianie do usługi Azure Search .NET SDK w wersji 5 | Dokumentacja firmy Microsoft
description: Uaktualnianie do usługi Azure Search .NET SDK w wersji 5
author: brjohnstmsft
manager: jlembicz
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: brjohnst
ms.openlocfilehash: 88949f4cf0e4408f5d1e4d9c9a5833b041b5a5ab
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Uaktualnianie do usługi Azure Search .NET SDK w wersji 5
Jeśli używasz wersji 4.0 preview lub starszy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk), ten artykuł pomoże Ci uaktualnienie aplikacji w wersji 5.

Aby uzyskać bardziej ogólne wskazówki zestawu SDK, wraz z przykładami, zobacz [sposobu korzystania z usługi Azure Search z aplikacji .NET](search-howto-dotnet-sdk.md).

Zestaw SDK usługi Azure Search .NET w wersji 5 zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim niewielkie, tak więc zmiana kodu powinny wymagać tylko minimalnym wysiłku. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 2.0 — wersja zapoznawcza lub starsze, należy najpierw uaktualnić do wersji 3, a następnie Uaktualnij w wersji 5. Zobacz [uaktualnienie do usługi Azure Search .NET SDK w wersji 3](search-dotnet-sdk-migration.md) instrukcje.
>
> Wystąpienia usługi Azure Search obsługuje różne wersje interfejsu API REST, w tym najnowsze. Możesz użyć wersji, gdy nie jest już najnowszego, ale zaleca się przeprowadzenie migracji swój kod, aby używać najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każde żądanie za pośrednictwem parametru api-version. Podczas korzystania z zestawu .NET SDK, wersja zestawu SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszej zestawu SDK można kontynuować do uruchomienia kodu bez zmian, nawet jeśli usługa nie zostanie uaktualniony do nowszej wersji interfejsu API obsługuje.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Nowości w wersji 5
Zestaw SDK usługi Azure Search .NET w wersji 5 jest przeznaczony dla najnowszej wersji ogólnie dostępna wyszukiwania interfejsu API REST Azure, w szczególności 2017-11-11. Dzięki temu można korzystać z nowych funkcji usługi Azure Search w z aplikacji .NET, takie jak następujące:

* [Synonimy](search-synonyms.md).
* Teraz można programowo dostęp ostrzeżeń w historię wykonywania indeksatora (zobacz `Warning` właściwość `IndexerExecutionResult` w [dokumentacja platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) więcej szczegółów).
* Obsługa platformy .NET Core 2.
* Obsługuje nowej struktury pakietów przy użyciu tylko części zestawu SDK, który należy (zobacz [fundamentalne zmiany w wersji 5](#ListOfChanges) szczegółowe informacje).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety oraz ich zależności, ponownie skompiluj projekt. Chyba że funkcja w wersji zapoznawczej, który nie znajduje się w nowej SDK GA, należy odbudować pomyślnie (Daj nam znać, jeśli nie, na [GitHub](https://github.com/azure/azure-sdk-for-net/issues)). Jeśli tak, wszystko jest gotowe!

Należy pamiętać, że z powodu zmian w opakowaniu zestawu .NET SDK usługi Azure Search, należy ponownie zbudować aplikację, aby użyć wersji 5. Te zmiany są szczegółowo opisane w [fundamentalne zmiany w wersji 5](#ListOfChanges).

Może pojawić się dodatkowe kompilacji ostrzeżenia dotyczące przestarzałe metody lub właściwości. Ostrzeżenia zawiera instrukcje dotyczące sposobu użycia zamiast przestarzałych funkcji. Na przykład, jeśli aplikacja używa `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metody, należy pobrać ostrzeżenie informujące "to zachowanie jest teraz domyślnie włączony, dlatego wywołanie tej metody nie jest już konieczne."

Gdy problem został rozwiązany ostrzeżenia kompilacji, można wprowadzić zmiany do aplikacji, aby móc korzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w zestawie SDK wyszczególnione w [nowości w wersji 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Fundamentalne zmiany w wersji 5
Najbardziej znaczących istotne zmiany w wersji 5 jest to, że `Microsoft.Azure.Search` zestaw i jego zawartość ma został podzielony na cztery oddzielne zestawy, które są obecnie dystrybuowane jako cztery oddzielne pakiety NuGet:

 - `Microsoft.Azure.Search`: To metapakiet, która obejmuje wszystkie inne pakiety usługi Azure Search jako zależności. Jeśli uaktualniasz z wcześniejszej wersji zestawu SDK, po prostu uaktualniania tego pakietu i ponowne utworzenie powinny być za mało, aby rozpocząć korzystanie z nowej wersji.
 - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli projektujesz aplikację .NET przy użyciu usługi Azure Search i należy do zapytań, lub zaktualizować dokumentów w Twojej indeksów. Jeśli należy utworzyć lub zaktualizować indeksów, użyj mapy synonimu lub innych zasobów poziomu usług, `Microsoft.Azure.Search` zamiast tego pakietu.
 - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli projektujesz automatyzacji w .NET do zarządzania indeksów usługi Azure Search, mapy synonim, indeksatorów, źródła danych lub innych zasobów na poziomie usługi. Jeśli wymagane jest tylko do dokumentów zapytania lub aktualizacji w Twojej indeksów, użyj `Microsoft.Azure.Search.Data` zamiast tego pakietu. Wszystkie funkcje usługi Azure Search, należy użyć `Microsoft.Azure.Search` zamiast tego pakietu.
 - `Microsoft.Azure.Search.Common`: Wymagane przez bibliotek usługi Azure Search .NET popularnych typów. Nie należy do tego pakietu można użyć bezpośrednio w aplikacji; Jest przeznaczone tylko do użycia jako zależność.
 
Ta zmiana jest technicznie przerywanie, ponieważ wiele typów zostały przeniesione między zestawami. Jest to, dlaczego odbudowywania aplikacji są niezbędne do uaktualnienia do zestawu SDK w wersji 5.

Istnieje niewielka liczba innych zmian podziału w wersji 5, które mogą wymagać kod zmienia oprócz ponownie skompilować aplikację.

### <a name="removed-obsolete-members"></a>Usunięte przestarzali członkowie

Może zostać wyświetlony błędy związane z metody lub właściwości, które zostały oznaczone jako przestarzałe w starszych wersjach, a później usunięte w wersji 5 kompilacji. Jeśli wystąpią błędy takie, poniżej przedstawiono sposób ich rozwiązywania:

- W przypadku używania `IndexingParametersExtensions.IndexStorageMetadataOnly` metody, użyj `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` zamiast tego.
- W przypadku używania `IndexingParametersExtensions.SkipContent` metody, użyj `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` zamiast tego.

### <a name="removed-preview-features"></a>Funkcje usunięte w wersji zapoznawczej

W wersji 5 jest uaktualniana w wersji 4.0-preview, należy pamiętać, że tablica JSON i analizowania pomocy technicznej dla obiekt Blob indeksatorów CSV została usunięta, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególności z następujących metod `IndexingParametersExtensions` klasy zostały usunięte:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardych zależność od tych funkcji, nie można uaktualnić do wersji 5 zestawu .NET SDK usługi Azure Search. Można nadal używać wersji 4.0-preview. Jednak należy należy pamiętać, że **zaleca się używania Podgląd zestawów SDK w aplikacji produkcyjnych**. Funkcje w wersji zapoznawczej są tylko do oceny i może ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat używania zestawu .NET SDK usługi Azure Search, zobacz [.NET porad](search-howto-dotnet-sdk.md).

Chętnie poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli napotkasz błąd, można pliku problemu w [repozytorium GitHub zestawu SDK .NET usługi Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu "[wyszukiwanie Azure]" z prefiksu.

Dziękujemy za skorzystanie z usługi Azure Search!
