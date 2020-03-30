---
title: Skrypty programu PowerShell przy użyciu modułu Az.Search
titleSuffix: Azure Cognitive Search
description: Tworzenie i konfigurowanie usługi Azure Cognitive Search za pomocą programu PowerShell. Można skalować usługę w górę lub w dół, zarządzać admin i zapytania api-keys i kwerendy dla informacji o systemie.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209300"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Zarządzanie usługą Azure Cognitive Search za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Polecenia cmdlet i skrypty programu PowerShell można uruchamiać w systemie Windows, Linux lub w [usłudze Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) w celu utworzenia i skonfigurowania usługi Azure Cognitive Search. Moduł **Az.Search** rozszerza [program Azure PowerShell](https://docs.microsoft.com/powershell/) o pełną parzystość do [interfejsów API REST zarządzania wyszukiwaniem](https://docs.microsoft.com/rest/api/searchmanagement) i możliwość wykonywania następujących zadań:

> [!div class="checklist"]
> * [Lista usług wyszukiwania w ramach subskrypcji](#list-search-services)
> * [Informacje o usłudze zwrotu](#get-search-service-information)
> * [Tworzenie lub usuwanie usługi](#create-or-delete-a-service)
> * [Ponowne generowanie kluczy interfejsu API administratora](#regenerate-admin-keys)
> * [Tworzenie lub usuwanie kluczy interfejsu api kwerendy](#create-or-delete-query-keys)
> * [Skalowanie w górę lub w dół za pomocą replik i partycji](#scale-replicas-and-partitions)

Od czasu do czasu zadawane są pytania dotyczące *zadań,* których nie ma na powyższej liście. Obecnie nie można użyć modułu **Az.Search** lub interfejsu API REST zarządzania do zmiany nazwy serwera, regionu lub warstwy. Dedykowane zasoby są przydzielane podczas tworzenia usługi. W związku z tym zmiana podstawowego sprzętu (lokalizacja lub typ węzła) wymaga nowej usługi. Podobnie nie ma żadnych narzędzi lub interfejsów API do przesyłania zawartości, takich jak indeks, z jednej usługi do innej.

W ramach usługi tworzenie zawartości i zarządzanie nimi odbywa się za pośrednictwem [interfejsu API REST usługi wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) lub [sdk .NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search). Chociaż nie ma żadnych dedykowanych poleceń programu PowerShell dla zawartości, można napisać skrypt programu PowerShell, który wywołuje interfejsy API REST lub .NET w celu tworzenia i ładowania indeksów.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Sprawdź wersje i moduły ładowania

Przykłady w tym artykule są interaktywne i wymagają podwyższonych uprawnień. Program Azure PowerShell (moduł **Az)** musi zostać zainstalowany. Aby uzyskać więcej informacji, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Sprawdzanie wersji programu PowerShell (5.1 lub nowsze)

Lokalny program PowerShell musi mieć wymiary 5.1 lub nowszy w dowolnym obsługiwanym systemie operacyjnym.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Ładowanie programu Azure PowerShell

Jeśli nie masz pewności, czy **Az** jest zainstalowany, uruchom następujące polecenie jako krok weryfikacji. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Niektóre systemy nie ładują automatycznie modułów. Jeśli pojawi się błąd w poprzednim poleceniu, spróbuj załadować moduł, a jeśli to się nie powiedzie, wróć do instrukcji instalacji, aby sprawdzić, czy pominięto krok.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Łączenie się z platformą Azure za pomocą tokenu logowania do przeglądarki

Poświadczenia logowania portalu można użyć do łączenia się z subskrypcją w programie PowerShell. Alternatywnie można [uwierzytelnić nieinteraktywnie za pomocą jednostki usługi.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

```azurepowershell-interactive
Connect-AzAccount
```

Jeśli posiadasz wiele subskrypcji platformy Azure, ustaw subskrypcję platformy Azure. Aby wyświetlić listę bieżących subskrypcji, uruchom to polecenie.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie nazwa `ContosoSubscription`subskrypcji to .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Lista usług w ramach subskrypcji

Następujące polecenia pochodzą z [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), zwracając informacje o istniejących zasobach i usługach już aprowizowane w ramach subskrypcji. Jeśli nie wiesz, ile usług wyszukiwania zostało już utworzonych, te polecenia zwracają te informacje, zapisując podróż do portalu.

Pierwsze polecenie zwraca wszystkie usługi wyszukiwania.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Z listy usług zwróć informacje o określonym zasobie.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importuj Az.Search

Polecenia z [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) nie są dostępne, dopóki nie załadujesz modułu.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Wyświetl wszystkie polecenia Az.Search

Jako krok weryfikacji zwróć listę poleceń podanych w module.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Uzyskaj informacje o usłudze wyszukiwania

Po zaimportowaniu **Az.Search** i znasz grupę zasobów zawierającą usługę wyszukiwania, uruchom [get-AzSearchService,](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) aby zwrócić definicję usługi, w tym nazwę, region, warstwę oraz liczbę replik i partycji.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Tworzenie lub usuwanie usługi

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) służy do [tworzenia nowej usługi wyszukiwania](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Ponowne generowanie kluczy administratora

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) służy do przewijania [kluczy interfejsu API administratora](search-security-api-keys.md). Dwa klucze administratora są tworzone z każdej usługi dla uwierzytelnionego dostępu. Klucze są wymagane na każde życzenie. Oba klucze administracyjne są funkcjonalnie równoważne, przyznając pełny dostęp do zapisu do usługi wyszukiwania z możliwością pobierania dowolnych informacji lub tworzenia i usuwania dowolnego obiektu. Istnieją dwa klucze, dzięki czemu można użyć jednego podczas zastępowania drugiego. 

Można ponownie wygenerować tylko jeden na `primary` raz, określone jako klucz lub. `secondary` Dla nieprzerwanej usługi należy pamiętać, aby zaktualizować cały kod klienta, aby użyć klucza pomocniczego podczas przetaczania klucza podstawowego. Unikaj zmiany kluczy podczas operacji w locie.

Zgodnie z oczekiwaniami, jeśli ponownie wygenerujesz klucze bez aktualizowania kodu klienta, żądania przy użyciu starego klucza zakończy się niepowodzeniem. Ponowne generowanie wszystkich nowych kluczy nie powoduje trwałego zablokowania użytkownika z usługi i nadal można uzyskać dostęp do usługi za pośrednictwem portalu. Po wygenerowaniu kluczy podstawowych i pomocniczych można zaktualizować kod klienta, aby użyć nowych kluczy, a operacje zostaną odpowiednio wznowione.

Wartości dla kluczy interfejsu API są generowane przez usługę. Nie można podać klucza niestandardowego dla usługi Azure Cognitive Search do użycia. Podobnie nie ma zdefiniowanej przez użytkownika nazwy dla kluczy interfejsu API administratora. Odwołania do klucza są stałymi `primary` ciągami, albo lub `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych. Oba klucze są zwracane, nawet jeśli zmieniasz tylko jeden na raz.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Tworzenie lub usuwanie kluczy kwerend

[**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) służy do tworzenia [kluczy interfejsu API kwerendy](search-security-api-keys.md) dla dostępu tylko do odczytu z aplikacji klienckich do indeksu usługi Azure Cognitive Search. Klucze kwerendy są używane do uwierzytelniania do określonego indeksu w celu pobierania wyników wyszukiwania. Klucze kwerend nie udzielają dostępu tylko do odczytu do innych elementów w usłudze, takich jak indeks, źródło danych lub indeksator.

Nie można podać klucza do użycia w usłudze Azure Cognitive Search. Klucze interfejsu API są generowane przez usługę.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skalowanie replik i partycji

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) służy do [zwiększania lub zmniejszania replik i partycji](search-capacity-planning.md) w celu ponownego skorygowania rozliczanych zasobów w ramach usługi. Zwiększenie replik lub partycji dodaje do rachunku, który ma zarówno stałych i zmiennych opłat. Jeśli masz tymczasowe zapotrzebowanie na dodatkową moc obliczeniową, można zwiększyć repliki i partycje do obsługi obciążenia. Obszar monitorowania na stronie portalu przegląd ma kafelki dotyczące opóźnienia kwerendy, zapytań na sekundę i ograniczania przepustowości, co wskazuje, czy bieżąca pojemność jest odpowiednia.

Może upłynąć trochę czasu, aby dodać lub usunąć pozyskiwanie środków. W tle występują dostosowania pojemności, umożliwiając kontynuowanie istniejących obciążeń. Dodatkowa pojemność jest używana dla żądań przychodzących, gdy tylko będzie gotowa, bez konieczności dodatkowej konfiguracji. 

Usuwanie pojemności może być uciążliwe. Zatrzymywanie wszystkich zadań indeksowania i indeksatora przed zmniejszeniem pojemności jest zalecane, aby uniknąć porzuconych żądań. Jeśli nie jest to możliwe, można rozważyć zmniejszenie pojemności przyrostowo, jedną replikę i partycję naraz, aż do osiągnięcia nowych poziomów docelowych.

Po przesłaniu polecenia nie ma możliwości zakończenia go w połowie. Przed poprawą liczby trzeba będzie poczekać, aż polecenie zostanie zakończone.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Następne kroki

Tworzenie [indeksu](search-what-is-an-index.md), [kwerendy indeksu](search-query-overview.md) za pomocą portalu, interfejsów API REST lub .NET SDK.

* [Tworzenie indeksu usługi Azure Cognitive Search w witrynie Azure portal](search-create-index-portal.md)
* [Konfigurowanie indeksatora do ładowania danych z innych usług](search-indexer-overview.md)
* [Zapytanie o indeks usługi Azure Cognitive Search przy użyciu Eksploratora wyszukiwania w portalu Platformy Azure](search-explorer.md)
* [Jak korzystać z usługi Azure Cognitive Search w sieci .NET](search-howto-dotnet-sdk.md)