---
title: Zarządzanie pamięcią podręczną platformy Azure dla programu Redis za pomocą programu Azure PowerShell
description: Dowiedz się, jak wykonywać zadania administracyjne dla usługi Azure Cache for Redis przy użyciu programu Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: a385d3ed7ef46389f96de72c98ffc29cebf60ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278534"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Zarządzanie pamięcią podręczną platformy Azure dla programu Redis za pomocą programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie pokazano, jak wykonywać typowe zadania, takie jak tworzenie, aktualizowanie i skalowanie pamięci podręcznej platformy Azure dla wystąpień Redis, jak ponownie wygenerować klucze dostępu i jak wyświetlać informacje o pamięci podręcznej. Aby uzyskać pełną listę poleceń cmdlet usługi Azure Cache for Redis PowerShell, zobacz [Polecenia cmdlet usługi Azure Cache for Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Aby uzyskać więcej informacji na temat klasycznego modelu wdrażania, zobacz [Usługa Azure Resource Manager a wdrożenie klasyczne: Poznaj modele wdrażania i stan zasobów.](../azure-resource-manager/management/deployment-models.md)

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli program Azure PowerShell został już zainstalowany, musi mieć program Azure PowerShell w wersji 1.0.0 lub nowszej. Wersję programu Azure PowerShell zainstalowaną za pomocą tego polecenia można sprawdzić w wierszu polecenia programu Azure PowerShell.

    Get-Module Az | format-table version


Najpierw należy zalogować się do platformy Azure za pomocą tego polecenia.

    Connect-AzAccount

Określ adres e-mail konta platformy Azure i jego hasło w oknie dialogowym logowania platformy Microsoft Azure.

Następnie, jeśli masz wiele subskrypcji platformy Azure, musisz ustawić subskrypcję platformy Azure. Aby wyświetlić listę bieżących subskrypcji, uruchom to polecenie.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie nazwa `ContosoSubscription`subskrypcji to .

    Select-AzSubscription -SubscriptionName ContosoSubscription

Przed rozpoczęciem korzystania z programu Windows PowerShell z usługą Azure Resource Manager potrzebne są następujące elementy:

* Program Windows PowerShell, wersja 3.0 lub 4.0. Aby znaleźć wersję programu Windows PowerShell,`$PSVersionTable` wpisz: `PSVersion` i sprawdź wartość 3.0 lub 4.0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Aby uzyskać szczegółową pomoc dotyczącą każdego polecenia cmdlet, które widzisz w tym samouczku, użyj polecenia cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Na przykład, aby uzyskać `New-AzRedisCache` pomoc dotyczącą polecenia cmdlet, wpisz:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Jak połączyć się z innymi chmurami
Domyślnie środowisko platformy `AzureCloud`Azure jest , który reprezentuje globalne wystąpienie chmury platformy Azure. Aby połączyć się z `Connect-AzAccount` innym wystąpieniem, użyj polecenia z przełącznikiem `-Environment` wiersza polecenia lub -`EnvironmentName` o żądanej nazwie środowiska lub środowiska.

Aby wyświetlić listę dostępnych środowisk, `Get-AzEnvironment` uruchom polecenie cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Aby połączyć się z chmurą dla instytucji rządowych platformy Azure
Aby połączyć się z usługą Azure Government Cloud, użyj jednego z następujących poleceń.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

lub

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Aby utworzyć pamięć podręczną w chmurze azure dla instytucji rządowych, użyj jednej z następujących lokalizacji.

* USGov Virginia
* USGov Iowa

Aby uzyskać więcej informacji na temat chmury Azure dla instytucji rządowych, zobacz [Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/) i Microsoft Azure Government Developer [Guide](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Aby połączyć się z usługą Azure China Cloud
Aby połączyć się z usługą Azure China Cloud, użyj jednego z następujących poleceń.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

lub

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Aby utworzyć pamięć podręczną w usłudze Azure China Cloud, użyj jednej z następujących lokalizacji.

* Chiny Wschodnie
* Chiny Północne

Aby uzyskać więcej informacji na temat usługi Azure China Cloud, zobacz [AzureChinaCloud for Azure obsługiwane przez 21Vianet w Chinach.](https://www.windowsazure.cn/)

### <a name="to-connect-to-microsoft-azure-germany"></a>Aby połączyć się z platformą Microsoft Azure Germany
Aby połączyć się z usługą Microsoft Azure Germany, użyj jednego z następujących poleceń.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


lub

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Aby utworzyć pamięć podręczną w niemczech platformy Microsoft Azure, użyj jednej z następujących lokalizacji.

* Niemcy Środkowe
* Niemcy Północno-Wschodnie

Aby uzyskać więcej informacji o platformie Microsoft Azure w Niemczech, zobacz [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Właściwości używane w pamięci podręcznej platformy Azure dla programu Redis PowerShell
Poniższa tabela zawiera właściwości i opisy często używanych parametrów podczas tworzenia pamięci podręcznej platformy Azure i zarządzania nimi dla wystąpień programu Redis przy użyciu programu Azure PowerShell.

| Parametr | Opis | Domyślne |
| --- | --- | --- |
| Nazwa |Nazwa pamięci podręcznej | |
| Lokalizacja |Lokalizacja pamięci podręcznej | |
| ResourceGroupName |Nazwa grupy zasobów, w której ma być utworzona pamięć podręczna | |
| Rozmiar |Rozmiar pamięci podręcznej. Prawidłowe wartości to: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1 GB. |
| Liczba odłamków |Liczba fragmentów do utworzenia podczas tworzenia pamięci podręcznej premium z włączonym klastrem. Prawidłowe wartości to: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Określa jednostkę SKU pamięci podręcznej. Prawidłowe wartości to: Podstawowe, Standardowe, Premium |Standardowa |
| RedisConfiguration (RedisConfiguration) |Określa ustawienia konfiguracji redis. Aby uzyskać szczegółowe informacje na temat każdego ustawienia, zobacz następującą [tabelę właściwości redisconfiguration.](#redisconfiguration-properties) | |
| EnableNonSslPort |Wskazuje, czy port nieobjęty SSL jest włączony. |False |
| MaxMemoryPolicy (Polityka MaxMemory) |Ten parametr został przestarzały - zamiast tego należy użyć funkcji RedisConfiguration. | |
| StatyczneIP |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie podano, jeden jest wybrany dla Ciebie z podsieci. | |
| Podsieć |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa nazwę podsieci, w którym można wdrożyć pamięć podręczną. | |
| VirtualNetwork |Podczas hostowania pamięci podręcznej w sieci wirtualnej, określa identyfikator zasobu sieci wirtualnej, w którym można wdrożyć pamięć podręczną. | |
| Keytype |Określa, który klucz dostępu ma być ponownie generowany podczas odnawiania kluczy dostępu. Prawidłowe wartości to: Podstawowy, Pomocniczy | |

### <a name="redisconfiguration-properties"></a>Właściwości redisConfiguration
| Właściwość | Opis | Warstwy cenowe |
| --- | --- | --- |
| rdb-kopia zapasowa z włączoną obsługą |Czy [trwałość danych Redis](cache-how-to-premium-persistence.md) jest włączona |Tylko premium |
| rdb-storage-connection-string |Parametry połączenia z kontem magazynu dla [trwałości danych Redis](cache-how-to-premium-persistence.md) |Tylko premium |
| rdb-backup-częstotliwość |Częstotliwość tworzenia kopii zapasowych dla [trwałości danych Redis](cache-how-to-premium-persistence.md) |Tylko premium |
| maxmemory zastrzeżone |Konfiguruje [pamięć zarezerwowaną](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla procesów innych niż pamięć podręczna |Standard i Premium |
| maxmemory-polityka |Konfiguruje [zasady eksmisji](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla pamięci podręcznej |Wszystkie warstwy cenowe |
| notify-keyspace-events |Konfiguruje [powiadomienia o przestrzeni kluczy](cache-configure.md#keyspace-notifications-advanced-settings) |Standard i Premium |
| hash-max-ziplist-wpisy |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów danych agregujących |Standard i Premium |
| hash-max-ziplist-wartość |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów danych agregujących |Standard i Premium |
| set-max-intset-entries set-max-intset-entries set-max-intset-entries set- |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów danych agregujących |Standard i Premium |
| zset-max-ziplist-wpisy |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów danych agregujących |Standard i Premium |
| zset-max-ziplist-wartość |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów danych agregujących |Standard i Premium |
| bazy danych |Konfiguruje liczbę baz danych. Tę właściwość można skonfigurować tylko podczas tworzenia pamięci podręcznej. |Standard i Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Aby utworzyć pamięć podręczną platformy Azure dla redis
Nowa pamięć podręczna azure dla wystąpień Redis są tworzone przy użyciu polecenia cmdlet [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> Przy pierwszym utworzeniu usługi Azure Cache for Redis w ramach subskrypcji `Microsoft.Cache` przy użyciu portalu Azure portal rejestruje obszar nazw dla tej subskrypcji. Jeśli spróbujesz utworzyć pierwszą pamięć podręczną Azure dla redis w ramach subskrypcji przy użyciu programu PowerShell, należy najpierw zarejestrować ten obszar nazw przy użyciu następującego polecenia; w przeciwnym razie polecenia `New-AzRedisCache` `Get-AzRedisCache` cmdlet, takie jak i nie.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Aby wyświetlić listę dostępnych parametrów `New-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Aby utworzyć pamięć podręczną z parametrami domyślnymi, uruchom następujące polecenie.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`i `Location` są wymagane parametry, ale pozostałe są opcjonalne i mają wartości domyślne. Uruchomienie poprzedniego polecenia tworzy standardową pamięć podręczną Azure Cache dla wystąpienia Redis o określonej nazwie, lokalizacji i grupie zasobów, która ma rozmiar 1 GB z wyłączonym portem bez SSL.

Aby utworzyć pamięć podręczną premium, określ rozmiar P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) lub P4 (53 GB - 530 GB). Aby włączyć klastrowanie, należy określić `ShardCount` liczbę fragmentów przy użyciu parametru. Poniższy przykład tworzy pamięć podręczną premium P1 z 3 fragmentami. P1 pamięci podręcznej premium jest 6 GB w rozmiarze, a ponieważ określiliśmy trzy fragmenty całkowity rozmiar wynosi 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Aby określić `RedisConfiguration` wartości dla parametru, `{}` należy ująć `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`wartości wewnątrz jako pary klucz/wartość, takie jak . Poniższy przykład tworzy standardową pamięć `allkeys-random` podręczną 1 GB z zasadami `KEA`maxmemory i powiadomieniami o przestrzeni kluczy skonfigurowane za pomocą programu . Aby uzyskać więcej informacji, zobacz [Powiadomienia dotyczące przestrzeni kluczy (ustawienia zaawansowane)](cache-configure.md#keyspace-notifications-advanced-settings) i [Zasady pamięci](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Aby skonfigurować ustawienie baz danych podczas tworzenia pamięci podręcznej
Ustawienie `databases` można skonfigurować tylko podczas tworzenia pamięci podręcznej. Poniższy przykład tworzy pamięć podręczną premium P3 (26 GB) z 48 bazami danych przy użyciu polecenia cmdlet [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache)

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Aby uzyskać więcej `databases` informacji na temat właściwości, zobacz [Domyślna pamięć podręczna azure dla konfiguracji serwera Redis](cache-configure.md#default-redis-server-configuration). Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej przy użyciu polecenia cmdlet [New-AzRedisCache,](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) zobacz poprzednia sekcja Aby utworzyć pamięć podręczną Azure dla redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Aby zaktualizować pamięć podręczną platformy Azure dla redis
Usługa Azure Cache dla wystąpień Redis są aktualizowane przy użyciu polecenia cmdlet [Set-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache)

Aby wyświetlić listę dostępnych parametrów `Set-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Polecenie `Set-AzRedisCache` cmdlet może służyć do aktualizowania `Sku` `EnableNonSslPort`właściwości, `RedisConfiguration` takich jak `Size`, , i wartości. 

Następujące polecenie aktualizuje zasady maxmemory dla pamięci podręcznej azure dla redis o nazwie myCache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Aby skalować pamięć podręczną platformy Azure dla redis
`Set-AzRedisCache`może służyć do skalowania pamięci podręcznej `Size`azure `Sku`dla `ShardCount` wystąpienia Redis, gdy , lub właściwości są modyfikowane. 

> [!NOTE]
> Skalowanie pamięci podręcznej przy użyciu programu PowerShell podlega tym samym ograniczeniom i wskazówkom, co skalowanie pamięci podręcznej z witryny Azure Portal. Można skalować do innej warstwy cenowej z następującymi ograniczeniami.
> 
> * Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
> * Nie można skalować z pamięci podręcznej **Premium** do **standardowej** lub **podstawowej** pamięci podręcznej.
> * Nie można skalować z pamięci podręcznej **standardowej** w dół do **podstawowej** pamięci podręcznej.
> * Można skalować z **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, można wykonać kolejną operację skalowania do żądanego rozmiaru.
> * Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium.** Należy skalować od **podstawowego** do **standardowego** w jednej operacji skalowania, a następnie od **standardowego** do **premium** w kolejnej operacji skalowania.
> * Nie można skalować od większego rozmiaru do rozmiaru **C0 (250 MB).**
> 
> Aby uzyskać więcej informacji, zobacz [Jak skalować pamięć podręczną platformy Azure dla redis](cache-how-to-scale.md).
> 
> 

W poniższym przykładzie pokazano, `myCache` jak skalować pamięć podręczną o nazwie do pamięci podręcznej 2,5 GB. Należy zauważyć, że to polecenie działa zarówno dla podstawowej, jak i standardowej pamięci podręcznej.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Po wydaniu tego polecenia zwracany jest stan pamięci `Get-AzRedisCache`podręcznej (podobny do wywoływania). Należy zauważyć, że `ProvisioningState` jest `Scaling`.

    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


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

Po zakończeniu operacji skalowania `ProvisioningState` zmienia `Succeeded`się na . Jeśli trzeba wykonać kolejną operację skalowania, na przykład zmiana z podstawowego na standardowy, a następnie zmianę rozmiaru, należy poczekać, aż poprzednia operacja zostanie zakończona lub zostanie wyświetlony błąd podobny do następującego.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Aby uzyskać informacje o pamięci podręcznej platformy Azure dla redis
Informacje o pamięci podręcznej można pobrać za pomocą polecenia cmdlet [Get-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache)

Aby wyświetlić listę dostępnych parametrów `Get-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Aby zwrócić informacje o wszystkich pamięci podręcznych w bieżącej subskrypcji, uruchom `Get-AzRedisCache` bez żadnych parametrów.

    Get-AzRedisCache

Aby zwrócić informacje o wszystkich pamięci podręcznych `Get-AzRedisCache` w `ResourceGroupName` określonej grupie zasobów, uruchom z parametrem.

    Get-AzRedisCache -ResourceGroupName myGroup

Aby zwrócić informacje o określonej pamięci podręcznej, uruchom `Get-AzRedisCache` z parametrem `Name` zawierającym nazwę pamięci podręcznej i parametrem `ResourceGroupName` z grupą zasobów zawierającą tę pamięć podręczną.

    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Aby pobrać klucze dostępu do pamięci podręcznej platformy Azure dla redis
Aby pobrać klucze dostępu do pamięci podręcznej, można użyć polecenia cmdlet [Get-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey)

Aby wyświetlić listę dostępnych parametrów `Get-AzRedisCacheKey`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Aby pobrać klucze pamięci podręcznej, wywołaj `Get-AzRedisCacheKey` polecenie cmdlet i przekaż w nazwie pamięci podręcznej nazwę grupy zasobów zawierającej pamięć podręczną.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Aby ponownie wygenerować klucze dostępu do pamięci podręcznej usługi Azure dla programu Redis
Aby ponownie wygenerować klucze dostępu do pamięci podręcznej, można użyć polecenia cmdlet [New-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey)

Aby wyświetlić listę dostępnych parametrów `New-AzRedisCacheKey`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

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
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Aby ponownie wygenerować klucz podstawowy lub `New-AzRedisCacheKey` pomocniczy dla pamięci podręcznej, należy wywołać `Primary` `Secondary` polecenie `KeyType` cmdlet i przekazać w nazwie, grupie zasobów i określić albo lub dla parametru. W poniższym przykładzie pomocniczy klucz dostępu dla pamięci podręcznej jest generowany ponownie.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Aby usunąć pamięć podręczną platformy Azure dla redis
Aby usunąć pamięć podręczną platformy Azure dla programu Redis, należy użyć polecenia cmdlet [Remove-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache)

Aby wyświetlić listę dostępnych parametrów `Remove-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

W poniższym przykładzie `myCache` pamięć podręczna o nazwie jest usuwany.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Aby zaimportować pamięć podręczną platformy Azure dla redis
Dane można zaimportować do pamięci podręcznej platformy Azure dla wystąpienia Redis przy użyciu polecenia `Import-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Import/Eksport jest dostępny tylko dla pamięci podręcznych [warstwy premium.](cache-premium-tier-intro.md) Aby uzyskać więcej informacji na temat importu/eksportowania, zobacz [Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów `Import-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


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
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Następujące polecenie importuje dane z obiektu blob określonego przez identyfikator sygnatury dostępu Współdzielonego do pamięci podręcznej azure dla usługi Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Aby wyeksportować pamięć podręczną platformy Azure dla redis
Dane można wyeksportować z usługi `Export-AzRedisCache` Azure Cache dla wystąpienia Redis przy użyciu polecenia cmdlet.

> [!IMPORTANT]
> Import/Eksport jest dostępny tylko dla pamięci podręcznych [warstwy premium.](cache-premium-tier-intro.md) Aby uzyskać więcej informacji na temat importu/eksportowania, zobacz [Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów `Export-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


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
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Następujące polecenie eksportuje dane z usługi Azure Cache for Redis wystąpienie do kontenera określonego przez identyfikator sygnatury dostępu Współdzielonego.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Aby ponownie uruchomić pamięć podręczną platformy Azure dla redis
Możesz ponownie uruchomić pamięć podręczną usługi `Reset-AzRedisCache` Azure cache dla wystąpienia Redis przy użyciu polecenia cmdlet.

> [!IMPORTANT]
> Ponowne uruchomienie jest dostępne tylko dla pamięci podręcznych [warstwy premium.](cache-premium-tier-intro.md) Aby uzyskać więcej informacji na temat ponownego uruchamiania pamięci podręcznej, zobacz [Administracja pamięci podręcznej — ponowne uruchomienie .](cache-administration.md#reboot)
> 
> 

Aby wyświetlić listę dostępnych parametrów `Reset-AzRedisCache`i ich opisy dla , uruchom następujące polecenie.

    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


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
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Następujące polecenie uruchamia ponownie oba węzły określonej pamięci podręcznej.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat korzystania z programu Windows PowerShell z platformą Azure, zobacz następujące zasoby:

* [Dokumentacja polecenia cmdlet usługi Azure Cache for Redis w usłudze MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Polecenia cmdlet usługi Azure Resource Manager:](https://go.microsoft.com/fwlink/?LinkID=394765)Naucz się używać poleceń cmdlet w module usługi Azure Resource Manager.
* [Używanie grup zasobów do zarządzania zasobami platformy Azure:](../azure-resource-manager/templates/deploy-portal.md)dowiedz się, jak tworzyć grupy zasobów i zarządzać nimi w witrynie Azure Portal.
* [Blog platformy Azure](https://azure.microsoft.com/blog/): Dowiedz się więcej o nowych funkcjach na platformie Azure.
* [Blog programu Windows PowerShell](https://blogs.msdn.com/powershell): Dowiedz się więcej o nowych funkcjach programu Windows PowerShell.
* ["Hej, Scripting Guy!" Blog](https://blogs.technet.com/b/heyscriptingguy/): Uzyskaj porady i wskazówki dotyczące rzeczywistych światów od społeczności programu Windows PowerShell.

