---
title: Skrypty programu PowerShell przy użyciu modułu Az.Search — usługa Azure Search
description: Utwórz i skonfiguruj usługę Azure Search przy użyciu programu PowerShell. Można skalować usługi w górę lub w dół, zarządzanie administratora i klucze interfejsu api zapytań oraz informacje o systemie zapytania.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: heidist
ms.openlocfilehash: 7a91ad691089ac816b31ebe1fce202110e580f71
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520568"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Zarządzanie usługą Azure Search przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Program PowerShell](search-manage-powershell.md)
> * [Interfejs API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Można uruchomić polecenia cmdlet programu PowerShell i skryptów na Windows, Linux, lub w [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) do tworzenia i konfigurowania [usługi Azure Search](https://docs.microsoft.com/azure/search/). [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) rozszerza modułu [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.4.0) z parzystością pełną do [interfejsów API REST zarządzania usługi Azure Search](https://docs.microsoft.com/rest/api/searchmanagement). Za pomocą programu Azure PowerShell i **Az.Search**, można wykonywać następujące zadania:

> [!div class="checklist"]
> * [Wyświetlić listę wszystkich usług wyszukiwania w ramach subskrypcji](#list-search-services)
> * [Uzyskaj informacje o usłudze wyszukiwania określonego](#get-search-service-information)
> * [Tworzenie lub usuwanie usługi](#create-or-delete-a-service)
> * Wygeneruj ponownie klucze API-Key administratora
> * [Tworzenie lub usuwanie kluczy interfejsu api zapytań](#create-or-delete-query-keys)
> * [Skalowanie usługi przez zwiększenie lub zmniejszenie replik i partycji](#scale-replicas-and-partitions)

Program PowerShell nie można zmienić nazwę, region lub warstwy usługi. Dedykowane zasoby są przydzielane podczas tworzenia usługi. Zmiana bazowego sprzętu (typ lokalizacji lub węzła) wymaga nowej usługi. Brak narzędzia i interfejsy API do transferowania zawartości. Całe Zarządzanie zawartości jest użycie [REST](https://docs.microsoft.com/rest/api/searchservice/) lub [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) interfejsów API, a jeśli chcesz przenieść indeksów, należy ponownie utworzyć i załadować je ponownie na nową usługę. 

Gdy nie ma żadnych dedykowanych poleceń programu PowerShell dotyczące zarządzania zawartością, można napisać skrypt programu PowerShell, który wywołuje REST lub .NET, aby utworzyć i załadować indeksów. **Az.Search** modułem samodzielnie nie zapewnia tych operacji.

Inne zadania, które nie są obsługiwane za pośrednictwem programu PowerShell lub dowolnego innego interfejsu API (tylko do portalu) to m.in.:
+ [Dołącz zasobu usług cognitive services](cognitive-search-attach-cognitive-services.md) dla [sztucznej Inteligencji, wzbogacone indeksowanie](cognitive-search-concept-intro.md). Usługi cognitive Services jest dołączona do zestawu umiejętności, nie subskrypcji lub usługi.
+ [Dodatek rozwiązania do monitorowania](search-monitor-usage.md#add-on-monitoring-solutions) lub [analiza ruchu wyszukiwania](search-traffic-analytics.md) służący do monitorowania usługi Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Sprawdzanie wersji i ładowania modułów

Przykłady w niniejszym artykule są interaktywne i wymagają podniesionego poziomu uprawnień. Program Azure PowerShell ( **Az** modułu) musi być zainstalowany. Aby uzyskać więcej informacji, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Sprawdzenie wersji programu PowerShell (5.1 lub nowszej)

Musi być lokalnym programie PowerShell, 5.1 i nowszych wersjach na dowolnym obsługiwanym systemie operacyjnym.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Obciążenia programu Azure PowerShell

Jeśli nie masz pewności, czy **Az** jest zainstalowany, uruchom następujące polecenie, w ramach kroku weryfikacyjnego. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Niektóre systemy zrobić nie automatyczne ładowanie modułów. Jeśli błąd pojawia się poprzednie polecenie spróbuj załadować moduł, a jeśli ono zawiedzie, wrócić do instrukcje dotyczące instalacji, aby zobaczyć, jeśli zdarzyło Ci się przeoczyć krok.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Łączenie z platformą Azure za pomocą tokenu logowania w przeglądarce

Nawiązać połączenia z subskrypcją w programie PowerShell, można użyć portalu poświadczeń logowania. Alternatywnie możesz [uwierzytelniania nieinterakcyjny przy użyciu jednostki usługi](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Jeśli przytrzymasz wiele subskrypcji platformy Azure, ustaw swoją subskrypcję platformy Azure. Aby wyświetlić listę bieżące subskrypcje, uruchom następujące polecenie.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie jest nazwa subskrypcji `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Lista wszystkich usług Azure Search w ramach subskrypcji

Następujące polecenia są z [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), zwracaniem informacji o istniejących zasobów i usług, które już są aprowizowane w Twojej subskrypcji. Jeśli nie wiesz, ile usługi wyszukiwania zostały już utworzone, te polecenia zwracają te informacje, zapisywanie podróż do portalu.

Pierwsze polecenie zwraca wszystkie usługi wyszukiwania.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Z listy usług zwrócone informacje na temat określonego zasobu.

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

## <a name="import-azsearch"></a>Import Az.Search

Polecenia z [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) nie są dostępne, dopóki nie można załadować modułu.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Lista wszystkich poleceń Az.Search

Jako kroku weryfikacji zwrócić listę poleceń dostępnych w module.

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

## <a name="get-search-service-information"></a>Pobieranie informacji o usłudze wyszukiwania

Po **Az.Search** jest importowany i wiesz, grupę zasobów zawierającą usługi search uruchomienie [Get AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) do zwrócenia definicji usługi, w tym nazwę, region, warstwy i repliki oraz Liczba partycji.

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

[**Nowe AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) służy do [Utwórz nową usługę wyszukiwania](search-create-service-portal.md).

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

## <a name="regenerate-admin-keys"></a>Wygeneruj ponownie klucze administratora

[**Nowe AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) jest używany przez administratora [klucze interfejsu API](search-security-api-keys.md). Dwa klucze administratora są tworzone za pomocą poszczególnych usług na potrzeby dostępu uwierzytelnionego. Klucze są wymagane na każde żądanie. Oba klucze administratora są funkcjonalnie równoważne udzielanie pełnej zapisu do usługi wyszukiwania z możliwością pobrania żadnych informacji lub tworzenia i usuwania dowolnych obiektów. Dwa klucze istnieje, dzięki czemu można użyć podczas zastępowania drugiego. 

Można tylko ponownie wygenerować pojedynczo, określony jako `primary` lub `secondary` klucza. Dla ciągłości usługi Pamiętaj, aby zaktualizować wszystkie kod klienta do użycia klucza pomocniczego podczas stopniowego za pośrednictwem klucza podstawowego. Należy unikać zmianę kluczy, podczas gdy operacje są w locie.

Zgodnie z oczekiwaniami, jeśli ponowne generowanie kluczy bez aktualizowania kodu klienta, żądania przy użyciu starego klucza nie powiedzie się. Ponowne wygenerowanie wszystkich nowych kluczy nie prowadzić do zablokowania możliwości usługi i usługi możesz uzyskiwać dostęp za pośrednictwem portalu. Po ponownym wygenerowaniu klucze podstawowe i pomocnicze można zaktualizować kod klienta do używania nowych kluczy i operacje wznowi odpowiednio.

Wartości kluczy interfejsu API są generowane przez usługę. Nie można podać klucz niestandardowy usługi Azure Search do użycia. Podobnie jest nie zdefiniowana przez użytkownika nazwa dla administratora klucze interfejsu API. Odwołania do klucza są stałe ciągów, albo `primary` lub `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych. Oba klucze są zwracane, mimo że można zmienić tylko jedno w danej chwili.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Tworzenie lub usuwanie klucze zapytania

[**Nowe AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) służy do tworzenia zapytań [klucze interfejsu API](search-security-api-keys.md) dostępu tylko do odczytu z aplikacji klienta do indeksu usługi Azure Search. Klucze zapytania są używane do uwierzytelniania do konkretnego indeksu na potrzeby pobierania wyników wyszukiwania. Klucze zapytania nie przyznawaj dostęp tylko do odczytu do innych elementów w usłudze, np. indeks, indeksator lub źródła danych.

Nie można podać klucz dla usługi Azure Search do użycia. Klucze interfejsu API są generowane przez usługę.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skala replik i partycji

[**Zestaw AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) służy do [zwiększyć lub zmniejszyć, replik i partycji](search-capacity-planning.md) aby skorygować płatnych zasobów w ramach usługi. Zwiększenie repliki lub partycje dodaje na rachunku, która ma zarówno opłaty stałe i zmienne. W przypadku tymczasowego potrzeby dodatkowej mocy obliczeniowej, można zwiększyć, replik i partycji w celu obsługi obciążenia. Obszar monitorowania na stronie portalu omówienie ma Kafelki kwerendami, zapytań na sekundę i ograniczania przepustowości, wskazującą, czy bieżąca pojemność jest odpowiednie.

Może upłynąć trochę czasu, aby dodać lub usunąć zasoby. Dostosowania do pojemności występują w tle, co istniejące obciążenia kontynuować. Pojemność jest używana do żądań przychodzących, jak tylko będzie gotowa, bez konieczności dodatkowej konfiguracji. 

Usuwanie pojemności może być szkodliwe. Zatrzymać wszystkie zadania indeksowania i indeksatora przed zmniejsza dostępną pojemność jest zalecane w celu uniknięcia żądań zakończonych niepowodzeniem. Jeśli nie jest to możliwe, można rozważyć stopniowo zmniejsza dostępną pojemność jednej repliki i partycji w czasie, aż do osiągnięcia są Twoje nowe poziomy docelowe.

Po przesłaniu polecenia nie ma możliwości jej zakończyć midway za pośrednictwem. Należy poczekać, aż polecenie zostanie zakończona przed zmiana liczby elementów.

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


## <a name="next-steps"></a>Kolejne kroki

Tworzenie [indeksu](search-what-is-an-index.md), [tworzenie zapytań względem indeksu](search-query-overview.md) przy użyciu portalu, interfejsów API REST lub zestawu .NET SDK.

* [Tworzenie indeksu usługi Azure Search w witrynie Azure portal](search-create-index-portal.md)
* [Skonfiguruj indeksator do ładowania danych z innych usług](search-indexer-overview.md)
* [Tworzenie zapytań względem indeksu usługi Azure Search przy użyciu Eksploratora wyszukiwania w witrynie Azure portal](search-explorer.md)
* [Jak używać usługi Azure Search na platformie .NET](search-howto-dotnet-sdk.md)
