---
title: Zarządzanie pamięć podręczna systemu Azure dla pamięci podręcznej Redis przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zadania administracyjne dla usługi Azure Cache dla pamięci podręcznej Redis przy użyciu programu Azure PowerShell.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: f7f4f9ae6a80052e06b2cafa68cb5c11dfa1333a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097933"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Zarządzanie pamięć podręczna systemu Azure dla pamięci podręcznej Redis przy użyciu programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie pokazano, jak przeprowadzić takie jak tworzenie typowych zadań, aktualizacją, a skalować pamięć podręczną Azure dla usługi Redis, wystąpień, jak ponownie wygenerować klucze dostępu i sposób wyświetlania informacji na temat pamięci podręczne. Aby uzyskać pełną listę pamięć podręczna systemu Azure dla poleceń cmdlet programu PowerShell z Redis, zobacz [usługi Azure Cache dla poleceń cmdlet usługi Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Aby uzyskać więcej informacji na temat klasycznego modelu wdrażania, zobacz [usługi Azure Resource Manager a klasycznym wdrożeniu: Modele wdrażania i stanu zasobów](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli zainstalowano już Azure PowerShell, konieczne jest posiadanie programu Azure PowerShell w wersji 1.0.0 lub nowszym. Aby sprawdzić wersję programu Azure PowerShell, który został zainstalowany przy użyciu następującego polecenia w wierszu polecenia programu Azure PowerShell.

    Get-Module Az | format-table version


Najpierw musisz zalogować się do platformy Azure za pomocą tego polecenia.

    Connect-AzAccount

W oknie dialogowym logowania w usłudze Microsoft Azure, należy określić adres e-mail swojego konta platformy Azure i jego hasło.

Następnie Jeśli masz wiele subskrypcji platformy Azure, musisz ustawić subskrypcję platformy Azure. Aby wyświetlić listę bieżące subskrypcje, uruchom następujące polecenie.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie jest nazwa subskrypcji `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

Zanim użyjesz środowiska Windows PowerShell z usługą Azure Resource Manager, potrzebne są następujące elementy:

* Program Windows PowerShell w wersji 3.0 lub 4.0. Aby znaleźć wersję środowiska Windows PowerShell, wpisz:`$PSVersionTable` i sprawdź wartość `PSVersion` jest 3.0 lub 4.0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Aby uzyskać szczegółową pomoc dla każdego polecenia cmdlet, które widać w ramach tego samouczka, należy użyć polecenia cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Na przykład, aby uzyskać pomoc dotyczącą `New-AzRedisCache` polecenia cmdlet, wpisz:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Jak połączyć się z innych chmur
Domyślnie platformy Azure to środowisko `AzureCloud`, która reprezentuje wystąpienie globalne chmury platformy Azure. Aby połączyć się do innego wystąpienia, użyj `Connect-AzAccount` polecenia `-Environment` lub -`EnvironmentName` przełącznik wiersza polecenia przy użyciu żądanego środowiska lub nazwę środowiska.

Aby wyświetlić listę dostępnych środowisk, uruchom `Get-AzEnvironment` polecenia cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Aby nawiązać połączenie chmury dla instytucji rządowych platformy Azure
Aby połączyć chmury dla instytucji rządowych platformy Azure, użyj jednej z następujących poleceń.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

lub

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Aby utworzyć pamięć podręczną w chmurze Azure dla instytucji rządowych, użyj jednej z następujących lokalizacji.

* USGov Wirginia
* USGov Iowa

Aby uzyskać więcej informacji na temat chmury dla instytucji rządowych platformy Azure, zobacz [platformy Microsoft Azure Government](https://azure.microsoft.com/features/gov/) i [Microsoft Azure dla instytucji rządowych — przewodnik dewelopera](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Aby połączyć do chmury platformy Azure w Chinach
Aby połączyć chmury Azure w Chinach, użyj jednej z następujących poleceń.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

lub

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Aby utworzyć pamięć podręczną w chmurze platformy Azure w Chinach, użyj jednej z następujących lokalizacji.

* Chiny Wschodnie
* Chiny Północne

Aby uzyskać więcej informacji na temat platformy Azure albo chmura zobacz [AzureChinaCloud dla platformy Azure jest świadczona przez firmę 21Vianet w Chinach](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Aby nawiązać połączenie z Microsoft Azure (Niemcy)
Połączyć się z Microsoft Azure (Niemcy), użyj jednej z następujących poleceń.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


lub

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Aby utworzyć pamięć podręczną w Microsoft Azure (Niemcy), użyj jednej z następujących lokalizacji.

* Niemcy Środkowe
* Niemcy Północno-Wschodnie

Aby uzyskać więcej informacji na temat programu Microsoft Azure (Niemcy), zobacz [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Właściwości używane dla usługi Azure Cache Redis środowiska PowerShell
Poniższa tabela zawiera właściwości i opisy parametrów często używane podczas tworzenia i zarządzania usługi Azure Cache dla wystąpień pamięci podręcznej Redis przy użyciu programu Azure PowerShell.

| Parametr | Opis | Domyślne |
| --- | --- | --- |
| Name |Nazwa pamięci podręcznej | |
| Location |Lokalizacja pamięci podręcznej | |
| ResourceGroupName |Nazwa grupy zasobów, w której chcesz utworzyć pamięci podręcznej | |
| Size |Rozmiar pamięci podręcznej. Prawidłowe wartości to: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1GB |
| ShardCount |Liczba fragmentów, aby utworzyć podczas tworzenia cache w warstwie premium przy włączonym klastrowaniu. Prawidłowe wartości to: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Określa jednostki SKU w pamięci podręcznej. Prawidłowe wartości to: Podstawowa, Standardowa i Premium |Standardowa (Standard) |
| RedisConfiguration |Określa ustawienia konfiguracji pamięci podręcznej Redis. Aby uzyskać szczegółowe informacje na temat poszczególnych ustawień, zobacz następującą [właściwości RedisConfiguration](#redisconfiguration-properties) tabeli. | |
| EnableNonSslPort |Wskazuje, czy port bez protokołu SSL jest włączony. |False |
| MaxMemoryPolicy |Ten parametr jest przestarzały — zamiast tego użyj RedisConfiguration. | |
| StaticIP |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podana, jeden jest wybierany automatycznie z podsieci. | |
| Subnet |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa nazwę podsieci, w której ma zostać wdrożony w pamięci podręcznej. | |
| VirtualNetwork |W przypadku hostowania w sieci Wirtualnej pamięci podręcznej, określa identyfikator zasobu sieci Wirtualnej, w której ma zostać wdrożony w pamięci podręcznej. | |
| KeyType |Określa, które klucz dostępu, można wygenerować ponownie podczas odnawiania klucze dostępu. Prawidłowe wartości to: Podstawowy i pomocniczy | |

### <a name="redisconfiguration-properties"></a>Właściwości RedisConfiguration
| Właściwość | Opis | Warstwy cenowe |
| --- | --- | --- |
| rdb-backup-enabled |Czy [trwałość danych Redis](cache-how-to-premium-persistence.md) jest włączona |Tylko w warstwie Premium |
| rdb-storage-connection-string |Parametry połączenia konta magazynu dla [trwałość danych Redis](cache-how-to-premium-persistence.md) |Tylko w warstwie Premium |
| rdb-backup-frequency |Częstotliwość wykonywania kopii zapasowych dla [trwałość danych Redis](cache-how-to-premium-persistence.md) |Tylko w warstwie Premium |
| maxmemory-reserved |Konfiguruje [pamięci zarezerwowanej](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla procesów pamięć podręczna |Standardowa i Premium |
| maxmemory-policy |Konfiguruje [zasady eksmisji](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla pamięci podręcznej |Wszystkie warstwy cenowe |
| notify-keyspace-events |Konfiguruje [powiadomienia przestrzeni kluczy](cache-configure.md#keyspace-notifications-advanced-settings) |Standardowa i Premium |
| hash-max-ziplist-entries |Konfiguruje [optymalizacji pamięci](https://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| hash-max-ziplist-value |Konfiguruje [optymalizacji pamięci](https://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| set-max-intset-entries |Konfiguruje [optymalizacji pamięci](https://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| zset-max-ziplist-entries |Konfiguruje [optymalizacji pamięci](https://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| zset-max-ziplist-value |Konfiguruje [optymalizacji pamięci](https://redis.io/topics/memory-optimization) dla typów małych agregowanie danych |Standardowa i Premium |
| bazy danych |Umożliwia skonfigurowanie liczby baz danych. Tej właściwości można skonfigurować tylko podczas tworzenia pamięci podręcznej. |Standardowa i Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Aby utworzyć pamięć podręczna systemu Azure dla usługi Redis
Nowej usługi Azure Cache dla wystąpień usługi Redis są tworzone przy użyciu [New AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) polecenia cmdlet.

> [!IMPORTANT]
> Przy pierwszym utworzeniu pamięć podręczna systemu Azure dla usługi Redis w subskrypcji przy użyciu witryny Azure portal, rejestruje portalu `Microsoft.Cache` przestrzeni nazw dla tej subskrypcji. Jeśli spróbujesz utworzyć pierwszej pamięci podręcznej Azure dla pamięci podręcznej Redis w subskrypcji przy użyciu programu PowerShell, najpierw należy zarejestrować tego obszaru nazw, używając następującego polecenia; w przeciwnym razie poleceń cmdlet, takich jak `New-AzRedisCache` i `Get-AzRedisCache` się nie powieść.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `New-AzRedisCache`, uruchom następujące polecenie.

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

Aby utworzyć pamięć podręczną za pomocą parametrów domyślnych, uruchom następujące polecenie.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, i `Location` parametry są wymagane, ale pozostałe są opcjonalne i mają przypisane wartości domyślne. Poprzednie polecenia tworzy standardowych jednostek SKU usługi Azure Cache dla wystąpienia pamięci podręcznej Redis przy użyciu określonej nazwy, lokalizacji i zasobów grupy, jest 1 GB w rozmiarze port bez protokołu SSL, wyłączona.

Aby utworzyć cache w warstwie premium, określ rozmiar P1 (6 GB do 60 GB), P2 (13 GB 130 GB), P3 (26 GB - 260 GB), lub P4 (53 GB do 530 GB). Do włączenia klastrowania, określ liczbę fragmentów za pomocą `ShardCount` parametru. Poniższy przykład tworzy cache w warstwie premium P1 z 3 fragmentów. Cache w warstwie premium P1 6 GB ma rozmiar, a ponieważ trzy fragmentów łączny rozmiar jest 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Aby określić wartości `RedisConfiguration` parametru, należy ująć wartości wewnątrz `{}` jako klucz/wartość pary, takich jak `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Poniższy przykład tworzy standardowe 1 GB pamięci podręcznej za pomocą `allkeys-random` maxmemory zasad i przestrzeń kluczy powiadomień skonfigurowano `KEA`. Aby uzyskać więcej informacji, zobacz [powiadomienia przestrzeni kluczy (Zaawansowane ustawienia)](cache-configure.md#keyspace-notifications-advanced-settings) i [zasad pamięci](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Aby skonfigurować ustawienia podczas tworzenia pamięci podręcznej bazy danych
`databases` Ustawienie można skonfigurować tylko podczas tworzenia pamięci podręcznej. Poniższy przykład tworzy premium P3 (26 GB) pamięci podręcznej z 48 baz danych przy użyciu [New AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) polecenia cmdlet.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Aby uzyskać więcej informacji na temat `databases` właściwości, zobacz [domyślne usługi Azure Cache konfiguracji serwera Redis](cache-configure.md#default-redis-server-configuration). Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej przy użyciu [New AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) polecenia cmdlet, zobacz poprzedni do utworzenia usługi Azure Cache dla sekcji pamięci podręcznej Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Aby zaktualizować pamięć podręczna systemu Azure dla usługi Redis
Pamięć podręczna systemu Azure dla wystąpienia usługi Redis są aktualizowane przy użyciu [AzRedisCache zestaw](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Set-AzRedisCache`, uruchom następujące polecenie.

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

`Set-AzRedisCache` Polecenia cmdlet można używać do aktualizacji właściwości, takie jak `Size`, `Sku`, `EnableNonSslPort`i `RedisConfiguration` wartości. 

Następujące polecenie aktualizacji zasad dotyczących maksymalnego rozmiaru pamięci dla usługi Azure Cache o nazwie myCache pamięci podręcznej Redis.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Aby skalować pamięć podręczna systemu Azure dla usługi Redis
`Set-AzRedisCache` można skalować pamięć podręczna systemu Azure na potrzeby pamięci podręcznej Redis wystąpienia, gdy `Size`, `Sku`, lub `ShardCount` właściwości są modyfikowane. 

> [!NOTE]
> Skalowanie pamięci podręcznej przy użyciu programu PowerShell jest zastrzeżeniem te same ograniczenia i wytycznymi dotyczącymi Skalowanie pamięci podręcznej w witrynie Azure portal. Można przeprowadzać skalowanie do innej warstwy cenowej, z następującymi zastrzeżeniami.
> 
> * Nie można skalować z wyższej warstwy cenowej, do niższej warstwy cenowej.
> * Nie można skalować z **Premium** w pamięci podręcznej w dół **standardowa** lub **podstawowe** pamięci podręcznej.
> * Nie można skalować z **standardowa** w pamięci podręcznej w dół **podstawowe** pamięci podręcznej.
> * Możesz skalować z **podstawowe** w pamięci podręcznej **standardowa** pamięci podręcznej, ale nie można zmienić rozmiar w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, można zrobić na żądany rozmiar kolejnych operacji skalowania.
> * Nie można skalować z **podstawowe** bezpośrednio do pamięci podręcznej **Premium** pamięci podręcznej. Musisz przeprowadzać Skalowanie z **podstawowe** do **standardowa** w ramach jednej operacji skalowania, a następnie **standardowa** do **Premium** w kolejnych opcji skalowania Operacja.
> * Nie można skalować z większy rozmiar w dół do **C0 (250 MB)** rozmiar.
> 
> Aby uzyskać więcej informacji, zobacz [jak skalowanie pamięci podręcznej Azure dla usługi Redis](cache-how-to-scale.md).
> 
> 

Poniższy przykład pokazuje, jak skalować pamięć podręczną o nazwie `myCache` do 2,5 GB pamięci podręcznej. Należy pamiętać, że to polecenie działa zarówno podstawowa lub standardowa pamięci podręcznej.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Po wydaniu tego polecenia, zwracany jest stan pamięci podręcznej (podobne do wywoływania `Get-AzRedisCache`). Należy pamiętać, że `ProvisioningState` jest `Scaling`.

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

Po zakończeniu operacji skalowania `ProvisioningState` zmieni się na `Succeeded`. Jeśli trzeba dokonać kolejna operacja skalowania, takich jak zmiana z podstawowa na standardowa, a następnie zmieniając rozmiar, należy zaczekać, aż operacja poprzedniej została zakończona lub komunikat o błędzie podobny do następującego.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Aby uzyskać informacje na temat usługi Azure Cache dla usługi Redis
Można pobrać informacji o pamięć podręczną za pomocą [Get AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Get-AzRedisCache`, uruchom następujące polecenie.

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

Do zwracania informacji dotyczących wszystkich pamięci podręcznych w bieżącej subskrypcji, uruchom `Get-AzRedisCache` bez żadnych parametrów.

    Get-AzRedisCache

Do zwracania informacji dotyczących wszystkich pamięci podręcznych w określonej grupie zasobów, należy uruchomić `Get-AzRedisCache` z `ResourceGroupName` parametru.

    Get-AzRedisCache -ResourceGroupName myGroup

Do zwracania informacji dotyczących określonych pamięci podręcznej, należy uruchomić `Get-AzRedisCache` z `Name` parametr zawierający nazwę pamięci podręcznej, a `ResourceGroupName` parametrem grupę zasobów zawierającą tej pamięci podręcznej.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Aby pobrać klucze dostępu dla usługi Azure Cache dla usługi Redis
Aby pobrać klucze dostępu pamięci podręcznej, można użyć [Get AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Get-AzRedisCacheKey`, uruchom następujące polecenie.

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

Aby pobrać klucze służące do pamięci podręcznej, należy wywołać `Get-AzRedisCacheKey` polecenia cmdlet i przekazać nazwę pamięci podręcznej nazwę grupy zasobów, która zawiera pamięci podręcznej.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Aby ponownie wygenerować klucze dostępu pamięci podręcznej Azure dla usługi Redis
Aby ponownie wygenerować klucze dostępu pamięci podręcznej, można użyć [New AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `New-AzRedisCacheKey`, uruchom następujące polecenie.

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

Aby ponownie wygenerować klucz podstawowy lub pomocniczy dla pamięci podręcznej, należy wywołać `New-AzRedisCacheKey` polecenia cmdlet i przekaż nazwę grupy zasobów oraz określ `Primary` lub `Secondary` dla `KeyType` parametru. W poniższym przykładzie pomocniczy klucz dostępu dla pamięci podręcznej zostanie ponownie wygenerowany.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Aby usunąć pamięć podręczna systemu Azure dla usługi Redis
Aby usunąć pamięć podręczna systemu Azure dla usługi Redis, użyj [AzRedisCache Usuń](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) polecenia cmdlet.

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Remove-AzRedisCache`, uruchom następujące polecenie.

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

W poniższym przykładzie nazwę pamięci podręcznej `myCache` zostanie usunięty.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Aby zaimportować pamięć podręczna systemu Azure dla usługi Redis
Możesz zaimportować dane do usługi Azure Cache dotyczące korzystania z wystąpienia usługi Redis `Import-AzRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Import/Export jest dostępna tylko dla [w warstwie premium](cache-premium-tier-intro.md) zapisuje w pamięci podręcznej. Aby uzyskać więcej informacji o usłudze Import/Export, zobacz [importowanie i eksportowanie danych w pamięci podręcznej Azure dla usługi Redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Import-AzRedisCache`, uruchom następujące polecenie.

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


Następujące polecenie importuje dane z magazynu obiektów blob, określony przez identyfikator uri sygnatury dostępu Współdzielonego do usługi Azure Cache dla usługi Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Aby wyeksportować pamięć podręczna systemu Azure dla usługi Redis
Możesz wyeksportować dane z usługi Azure Cache dotyczące korzystania z wystąpienia usługi Redis `Export-AzRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Import/Export jest dostępna tylko dla [w warstwie premium](cache-premium-tier-intro.md) zapisuje w pamięci podręcznej. Aby uzyskać więcej informacji o usłudze Import/Export, zobacz [importowanie i eksportowanie danych w pamięci podręcznej Azure dla usługi Redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Export-AzRedisCache`, uruchom następujące polecenie.

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


Następujące polecenie eksportuje dane z usługi Azure Cache dla wystąpienia usługi Redis do określonego przez identyfikator uri sygnatury dostępu Współdzielonego kontenera.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Ponowne uruchomienie pamięć podręczna systemu Azure dla usługi Redis
Użytkownik może uruchomić usługi Azure Cache dotyczące korzystania z wystąpienia usługi Redis `Reset-AzRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Ponowne uruchomienie jest dostępna tylko dla [w warstwie premium](cache-premium-tier-intro.md) zapisuje w pamięci podręcznej. Aby uzyskać więcej informacji na temat ponownego uruchomienia pamięci podręcznej, zobacz [administracji w pamięci podręcznej — ponowny rozruch](cache-administration.md#reboot).
> 
> 

Aby wyświetlić listę dostępnych parametrów oraz ich opisy `Reset-AzRedisCache`, uruchom następujące polecenie.

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


Poniższe polecenie wykonuje ponowny rozruch oba węzły określonego pamięci podręcznej.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat programu Windows PowerShell przy użyciu platformy Azure, zobacz następujące zasoby:

* [Pamięć podręczna systemu Azure dla usługi Redis dokumentacji poleceń cmdlet w witrynie MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Polecenia cmdlet usługi Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkID=394765): Dowiedz się używać poleceń cmdlet w module usługi Azure Resource Manager.
* [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): Dowiedz się, jak utworzyć i zarządzanie grupami zasobów w witrynie Azure portal.
* [Blog dotyczący platformy Azure](https://azure.microsoft.com/blog/): Więcej informacji na temat nowych funkcji w systemie Azure.
* [Blog programu Windows PowerShell](https://blogs.msdn.com/powershell): Dowiedz się więcej o nowych funkcjach w programie Windows PowerShell.
* ["Witaj, twórco skryptów!" Blog](https://blogs.technet.com/b/heyscriptingguy/): Uzyskaj praktyczne wskazówki i porady od społeczności programu Windows PowerShell.

