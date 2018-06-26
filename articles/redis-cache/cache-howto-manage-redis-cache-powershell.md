---
title: Zarządzanie pamięć podręczna Azure Redis przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zadania administracyjne dla pamięci podręcznej Redis Azure za pomocą programu Azure PowerShell.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: fcadac344e2e05c3f6cdd9003b87b819d7933fba
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937438"
---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Zarządzanie przy użyciu programu Azure PowerShell pamięć podręczna Azure Redis
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
> 
> 

W tym temacie przedstawiono sposób typowych zadań do wykonywania takich jak tworzenie, aktualizacją, a skalowania swoich wystąpień w pamięci podręcznej Redis Azure, jak można ponownie wygenerować klucze dostępu i sposób wyświetlania informacji o pamięci podręczne. Aby uzyskać pełną listę poleceń cmdlet programu PowerShell pamięci podręcznej Redis Azure, zobacz [poleceń cmdlet pamięć podręczna Redis Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Aby uzyskać więcej informacji na temat klasycznym modelu wdrażania, zobacz [usługi Azure Resource Manager, a wdrożenie klasyczne: zrozumienie modele wdrażania i stan zasobów](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli użytkownik zainstalował już programu Azure PowerShell, musi mieć program Azure PowerShell w wersji 1.0.0 lub nowszej. Można sprawdzić wersji programu Azure PowerShell, zainstalowanym za pomocą tego polecenia w wierszu polecenia programu PowerShell systemu Azure.

    Get-Module azure | format-table version


Najpierw należy logujesz się na platformie Azure za pomocą tego polecenia.

    Connect-AzureRmAccount

Określ adres e-mail konta platformy Azure i jego hasło w oknie dialogowym logowania Microsoft Azure.

Następnie Jeśli masz wiele subskrypcji Azure, należy ustawić subskrypcji platformy Azure. Aby wyświetlić listę bieżące subskrypcje, uruchom następujące polecenie.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie Nazwa subskrypcji jest `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Przed użyciem programu Windows PowerShell z usługą Azure Resource Manager, potrzebne są następujące elementy:

* Środowiska Windows PowerShell w wersji 3.0 lub 4.0. Aby znaleźć wersję środowiska Windows PowerShell, wpisz:`$PSVersionTable` i sprawdź wartość `PSVersion` jest 3.0 lub 4.0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Aby uzyskać szczegółową pomoc dla każdego polecenia cmdlet, które są widoczne w tym samouczku, użyj polecenia cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Na przykład, aby uzyskać pomoc dotyczącą `New-AzureRmRedisCache` polecenia cmdlet, wpisz:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Łączenie z innych chmur
Domyślnie platformy Azure to środowisko `AzureCloud`, reprezentuje wystąpienie globalne chmury Azure. Aby nawiązać połączenie z innym wystąpieniem, użyj `Connect-AzureRmAccount` z `-Environment` lub -`EnvironmentName` przełącznik wiersza polecenia o nazwie środowiska lub wymagane środowisko.

Aby wyświetlić listę dostępnych środowisk, należy uruchomić `Get-AzureRmEnvironment` polecenia cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Do nawiązania połączenia chmury Azure dla instytucji rządowych
Aby połączyć do chmury Azure dla instytucji rządowych, użyj jednej z następujących poleceń.

    Connect-AzureRmAccount -EnvironmentName AzureUSGovernment

lub

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Aby utworzyć pamięć podręczną w chmurze Azure dla instytucji rządowych, użyj jednej z następujących lokalizacji.

* USGov Virginia
* USGov Iowa

Aby uzyskać więcej informacji o chmurze platformy Azure dla instytucji rządowych, zobacz [Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/) i [Podręczniku dewelopera programu Microsoft Azure dla instytucji rządowych](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Do nawiązania połączenia chmury Azure w Chinach
Aby połączyć się z chmury Azure w Chinach, użyj jednej z następujących poleceń.

    Connect-AzureRmAccount -EnvironmentName AzureChinaCloud

lub

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Aby utworzyć pamięć podręczną w chmurze platformy Azure w Chinach, użyj jednej z następujących lokalizacji.

* Chiny Wschodnie
* Chiny Północne

Aby uzyskać więcej informacji o chmurze Chin platformy Azure, zobacz [AzureChinaCloud dla platformy Azure obsługiwane przez 21Vianet w Chinach](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Do nawiązania połączenia platformy Azure w Niemczech firmy Microsoft
Aby połączyć się Microsoft platformy Azure w Niemczech, użyj jednej z następujących poleceń.

    Connect-AzureRmAccount -EnvironmentName AzureGermanCloud


lub

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

Aby utworzyć pamięć podręczną w Microsoft Azure Niemczech, użyj jednej z następujących lokalizacji.

* Niemcy Środkowe
* Niemcy Północno-Wschodnie

Aby uzyskać więcej informacji o Microsoft platformy Azure w Niemczech, zobacz [Microsoft Azure Niemcy](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Właściwości używanej do pamięci podręcznej Redis Azure PowerShell
Poniższa tabela zawiera właściwości i opisy parametrów często używane podczas tworzenia i zarządzania nimi z wystąpień pamięci podręcznej Redis Azure za pomocą programu Azure PowerShell.

| Parametr | Opis | Domyślne |
| --- | --- | --- |
| Name (Nazwa) |Nazwa pamięci podręcznej | |
| Lokalizacja |Lokalizacja pamięci podręcznej | |
| ResourceGroupName |Nazwa grupy zasobów, w której chcesz utworzyć pamięci podręcznej | |
| Rozmiar |Rozmiar pamięci podręcznej. Prawidłowe wartości to: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount |Liczba fragmentów, aby utworzyć podczas tworzenia usługi pamięć podręczna premium z włączoną funkcją klastrowania. Prawidłowe wartości to: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Określa numer pamięci podręcznej. Prawidłowe wartości to: Basic, Standard, Premium |Standardowa (Standard) |
| RedisConfiguration |Określa ustawienia konfiguracji pamięci podręcznej Redis. Aby uzyskać szczegółowe informacje o każdym ustawieniu, zobacz następujące tematy [RedisConfiguration właściwości](#redisconfiguration-properties) tabeli. | |
| EnableNonSslPort |Wskazuje, czy włączono port bez protokołu SSL. |False |
| MaxMemoryPolicy |Ten parametr jest przestarzały — zamiast tego użyj RedisConfiguration. | |
| StaticIP |Podczas obsługi pamięci podręcznej w sieci Wirtualnej, określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podana, co zostanie wybrany dla Ciebie podsieci. | |
| Podsieć |Podczas obsługi pamięci podręcznej w sieci Wirtualnej, określa nazwę podsieci, w której chcesz wdrożyć w pamięci podręcznej. | |
| VirtualNetwork |Podczas obsługi pamięci podręcznej w sieci Wirtualnej, określa identyfikator zasobu sieci wirtualnej, w której chcesz wdrożyć w pamięci podręcznej. | |
| Właściwość KeyType |Określa klucz, do którego dostęp można ponownie wygenerować podczas odnawiania klucze dostępu. Prawidłowe wartości to: podstawowej, dodatkowej | |

### <a name="redisconfiguration-properties"></a>Właściwości RedisConfiguration
| Właściwość | Opis | Warstwy cenowe |
| --- | --- | --- |
| włączone kopii zapasowej RDB |Czy [trwałość danych Redis](cache-how-to-premium-persistence.md) jest włączone |Tylko w warstwie Premium |
| rdb-storage-connection-string |Parametry połączenia z kontem magazynu dla [trwałość danych Redis](cache-how-to-premium-persistence.md) |Tylko w warstwie Premium |
| rdb-backup-frequency |Częstotliwość wykonywania kopii zapasowych dla [trwałość danych Redis](cache-how-to-premium-persistence.md) |Tylko w warstwie Premium |
| maxmemory-reserved |Konfiguruje [pamięci zarezerwowanej](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) -cache procesów |Standardowa i Premium |
| maxmemory-policy |Konfiguruje [zasad eksmisji](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla pamięci podręcznej |Wszystkie warstwy cenowe |
| powiadomienia przestrzeni kluczy zdarzenia |Konfiguruje [powiadomienia przestrzeni kluczy](cache-configure.md#keyspace-notifications-advanced-settings) |Standardowa i Premium |
| hash-max-ziplist-entries |Konfiguruje [optymalizacji pamięci](http://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| hash-max-ziplist-value |Konfiguruje [optymalizacji pamięci](http://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| zestaw max-intset wpisów |Konfiguruje [optymalizacji pamięci](http://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| zset-max-ziplist-entries |Konfiguruje [optymalizacji pamięci](http://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| zset-max-ziplist-value |Konfiguruje [optymalizacji pamięci](http://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| bazy danych |Konfiguruje liczbę baz danych. Tej właściwości można skonfigurować tylko na tworzenie pamięci podręcznej. |Standardowa i Premium |

## <a name="to-create-a-redis-cache"></a>Aby utworzyć pamięci podręcznej Redis
Nowe wystąpienia pamięci podręcznej Redis Azure są tworzone przy użyciu [AzureRmRedisCache nowy](https://msdn.microsoft.com/library/azure/mt634517.aspx) polecenia cmdlet.

> [!IMPORTANT]
> Tworzenie pamięci podręcznej Redis w ramach subskrypcji przy użyciu portalu Azure po raz pierwszy portalu rejestruje `Microsoft.Cache` przestrzeni nazw dla tej subskrypcji. Przy próbie utworzenia pierwszej pamięci podręcznej Redis w ramach subskrypcji przy użyciu programu PowerShell, najpierw należy zarejestrować tej przestrzeni nazw przy użyciu następującego polecenia; w przeciwnym razie poleceń cmdlet, takich jak `New-AzureRmRedisCache` i `Get-AzureRmRedisCache` się nie powieść.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy `New-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Aby utworzyć pamięć podręczną z domyślnych parametrów, uruchom następujące polecenie.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, i `Location` są wymagane parametry, ale pozostałe są opcjonalne i mają przypisane wartości domyślne. Poprzednie polecenia tworzy wystąpienie standardowy SKU pamięć podręczna Redis Azure z określoną nazwą, lokalizacji i grupy zasobów o rozmiarze z portu bez protokołu SSL, wyłączona 1 GB.

Aby utworzyć pamięć podręczną premium, określ rozmiar P1 (6 GB — 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB), lub P4 (53 GB - 530 GB). Aby włączyć klastra, określ liczby niezależnych za pomocą `ShardCount` parametru. Poniższy przykład tworzy pamięć podręczna premium P1 z odłamków 3. Pamięć podręczna premium P1 w rozmiar 6 GB, a ponieważ firma Microsoft określone trzy odłamków całkowity rozmiar jest 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Aby określić wartości dla `RedisConfiguration` parametru, ujmij wartości wewnątrz `{}` jako klucza i wartości, takich jak pary `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Poniższy przykład tworzy standardowe 1 GB pamięci podręcznej z `allkeys-random` maxmemory zasad i przestrzeni kluczy powiadomienia skonfigurowane z `KEA`. Aby uzyskać więcej informacji, zobacz [powiadomienia przestrzeni kluczy (Zaawansowane ustawienia)](cache-configure.md#keyspace-notifications-advanced-settings) i [zasady pamięci](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Aby skonfigurować ustawienie podczas tworzenia pamięci podręcznej bazy danych
`databases` Ustawienie można skonfigurować tylko podczas tworzenia pamięci podręcznej. Poniższy przykład tworzy — warstwa premium P3 (26 GB) pamięci podręcznej z 48 baz danych przy użyciu [AzureRmRedisCache nowy](https://msdn.microsoft.com/library/azure/mt634517.aspx) polecenia cmdlet.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Aby uzyskać więcej informacji na temat `databases` właściwości, zobacz [konfiguracji serwera domyślna pamięci podręcznej Redis Azure](cache-configure.md#default-redis-server-configuration). Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej z użyciem [AzureRmRedisCache nowy](https://msdn.microsoft.com/library/azure/mt634517.aspx) polecenia cmdlet, zobacz poprzedni [do utworzenia pamięci podręcznej Redis](#to-create-a-redis-cache) sekcji.

## <a name="to-update-a-redis-cache"></a>Aby zaktualizować pamięci podręcznej Redis
Wystąpienia pamięci podręcznej Redis Azure są aktualizowane przy użyciu [AzureRmRedisCache zestaw](https://msdn.microsoft.com/library/azure/mt634518.aspx) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów i ich opisy `Set-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzureRmRedisCache` Polecenia cmdlet można używać do aktualizowania właściwości, takie jak `Size`, `Sku`, `EnableNonSslPort`i `RedisConfiguration` wartości. 

Polecenie aktualizacji pamięci podręcznej Redis, o nazwie myCache maxmemory-policy.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Aby skalować pamięci podręcznej Redis
`Set-AzureRmRedisCache` może służyć do skalowania pamięci podręcznej Azure Redis wystąpienia, kiedy `Size`, `Sku`, lub `ShardCount` są modyfikowane właściwości. 

> [!NOTE]
> Skalowanie pamięci podręcznej przy użyciu programu PowerShell podlega tego samego ograniczenia i wytyczne jako skalowanie pamięci podręcznej z portalu Azure. Można skalować do innej warstwy cenowej z następującymi ograniczeniami.
> 
> * Nie można skalować z wyższej warstwy cenowej do dolnej warstwy cenowej.
> * Nie można skalować z **Premium** pamięci podręcznej w dół do **standardowe** lub **podstawowe** pamięci podręcznej.
> * Nie można skalować z **standardowe** pamięci podręcznej w dół do **podstawowe** pamięci podręcznej.
> * Możesz skalować z **podstawowe** pamięci podręcznej do **standardowe** pamięci podręcznej, ale nie można zmienić rozmiar w tym samym czasie. Jeśli potrzebujesz zmienić rozmiar, należy na żądany rozmiar kolejnych operacji skalowania.
> * Nie można skalować z **podstawowe** bezpośrednio do pamięci podręcznej **Premium** pamięci podręcznej. Należy skalować z **podstawowe** do **standardowe** w jednej operacji skalowania, a następnie z **standardowe** do **Premium** kolejnych skalowania Operacja.
> * Nie można skalować z większego rozmiaru w dół do **C0 (250 MB)** rozmiar.
> 
> Aby uzyskać więcej informacji, zobacz [jak pamięć podręczna Redis Azure skali](cache-how-to-scale.md).
> 
> 

Poniższy przykład przedstawia sposób skalowania pamięci podręcznej o nazwie `myCache` do 2,5 GB pamięci podręcznej. Należy pamiętać, że to polecenie działa zarówno Basic lub Standard pamięci podręcznej.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Po uruchomieniu tego polecenia, zwracany jest stan pamięci podręcznej (podobny do wywoływania `Get-AzureRmRedisCache`). Należy pamiętać, że `ProvisioningState` jest `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Po zakończeniu operacji skalowania, `ProvisioningState` zmienia się na `Succeeded`. Jeśli konieczne jest wprowadzenie kolejnych operacji skalowania, takich jak zmiana z podstawowego na standardowy, a następnie zmienić rozmiar, należy zaczekać, aż poprzednia operacja została ukończona lub komunikat o błędzie podobny do następującego.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Aby uzyskać informacje o pamięci podręcznej Redis
Można pobrać informacji o pamięci podręcznej z użyciem [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów i ich opisy `Get-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Zwraca informacje dotyczące wszystkich usług pamięć podręczna w bieżącej subskrypcji, uruchom `Get-AzureRmRedisCache` bez żadnych parametrów.

    Get-AzureRmRedisCache

Zwraca informacje dotyczące wszystkich usług pamięć podręczna w określonej grupy zasobów, uruchom `Get-AzureRmRedisCache` z `ResourceGroupName` parametru.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Zwraca informacje dotyczące określonego pamięci podręcznej, uruchom `Get-AzureRmRedisCache` z `Name` parametr zawierający nazwę pamięci podręcznej i `ResourceGroupName` parametru z grupą zasobów, zawierającą tej pamięci podręcznej.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Aby pobrać klucze dostępu pamięci podręcznej Redis
Aby pobrać klucze dostępu pamięci podręcznej, można użyć [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów i ich opisy `Get-AzureRmRedisCacheKey`, uruchom następujące polecenie.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Aby uzyskać dostęp do kluczy dla pamięci podręcznej, należy wywołać `Get-AzureRmRedisCacheKey` polecenia cmdlet i przekaż nazwę pamięci podręcznej Nazwa grupy zasobów, która zawiera pamięci podręcznej.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Można ponownie wygenerować klucze dostępu pamięci podręcznej Redis
Aby ponownie wygenerować klucze dostępu pamięci podręcznej, można użyć [AzureRmRedisCacheKey nowy](https://msdn.microsoft.com/library/azure/mt634512.aspx) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów i ich opisy `New-AzureRmRedisCacheKey`, uruchom następujące polecenie.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Aby ponownie wygenerować klucz podstawowy lub pomocniczy dla pamięci podręcznej, należy wywołać `New-AzureRmRedisCacheKey` polecenia cmdlet i podaj nazwę grupy zasobów oraz określ `Primary` lub `Secondary` dla `KeyType` parametru. W poniższym przykładzie jest ponownie wygenerować pomocniczy klucz dostępu dla pamięci podręcznej.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Aby usunąć pamięci podręcznej Redis
Aby usunąć pamięci podręcznej Redis, użyj [AzureRmRedisCache Usuń](https://msdn.microsoft.com/library/azure/mt634515.aspx) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów i ich opisy `Remove-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

W poniższym przykładzie pamięci podręcznej o nazwie `myCache` zostanie usunięta.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Aby zaimportować pamięci podręcznej Redis
Można zaimportować danych do wystąpienia pamięci podręcznej Redis Azure za pomocą `Import-AzureRmRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Narzędzie importu/eksportu jest dostępna tylko dla [warstwy premium](cache-premium-tier-intro.md) przechowuje w pamięci podręcznej. Aby uzyskać więcej informacji na temat importowania i eksportowania, zobacz [importować i eksportować dane w pamięci podręcznej Redis Azure](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy `Import-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Następujące polecenie importuje dane z obiektu blob, określony przez identyfikator uri sygnatury dostępu Współdzielonego w pamięci podręcznej Redis Azure.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Aby wyeksportować pamięci podręcznej Redis
Można eksportować dane z wystąpienia pamięci podręcznej Redis Azure za pomocą `Export-AzureRmRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Narzędzie importu/eksportu jest dostępna tylko dla [warstwy premium](cache-premium-tier-intro.md) przechowuje w pamięci podręcznej. Aby uzyskać więcej informacji na temat importowania i eksportowania, zobacz [importować i eksportować dane w pamięci podręcznej Redis Azure](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy `Export-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Polecenie eksportuje dane z wystąpienia pamięci podręcznej Redis Azure do kontenera określony przez identyfikator uri sygnatury dostępu Współdzielonego.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Ponowne uruchomienie z pamięci podręcznej Redis
Możesz ponownie, używając wystąpienia pamięci podręcznej Redis Azure `Reset-AzureRmRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Ponowne uruchomienie jest dostępna tylko dla [warstwy premium](cache-premium-tier-intro.md) przechowuje w pamięci podręcznej. Aby uzyskać więcej informacji o ponowne uruchomienie z pamięci podręcznej, zobacz [pamięci podręcznej administracji — ponowny rozruch](cache-administration.md#reboot).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy `Reset-AzureRmRedisCache`, uruchom następujące polecenie.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Polecenie wykonuje ponowny rozruch oba węzły określonego pamięci podręcznej.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat korzystania z platformy Azure, programu Windows PowerShell, zobacz następujące zasoby:

* [Azure dokumentację poleceń cmdlet pamięci podręcznej Redis w witrynie MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Polecenia cmdlet Menedżera zasobów systemu Azure](http://go.microsoft.com/fwlink/?LinkID=394765): Dowiedz się, jak używać poleceń cmdlet w module usługi Azure Resource Manager.
* [Używanie grup zasobów do zarządzania zasobami Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): informacje o sposobie tworzenia i obsługi grup zasobów w portalu Azure.
* [Azure blog](https://azure.microsoft.com/en-us/blog/): więcej informacji na temat nowych funkcji w systemie Azure.
* [Blog dotyczący programu Windows PowerShell](http://blogs.msdn.com/powershell): więcej informacji na temat nowych funkcji w programie Windows PowerShell.
* ["Witaj, Twórco skryptów!" Blog](http://blogs.technet.com/b/heyscriptingguy/): Pobierz rzeczywistych porady i wskazówki związane z społeczności programu Windows PowerShell.

