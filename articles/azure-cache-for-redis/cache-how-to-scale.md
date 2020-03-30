---
title: Jak skalować usługę Azure Cache for Redis
description: Dowiedz się, jak skalować pamięć podręczną usługi Azure dla wystąpień Redis przy użyciu witryny Azure portal i narzędzi, takich jak Azure PowerShell i interfejsu wiersza polecenia platformy Azure.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278001"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Jak skalować usługę Azure Cache for Redis
Usługa Azure Cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru pamięci podręcznej i funkcji. Po utworzeniu pamięci podręcznej można skalować rozmiar i warstwę cenową pamięci podręcznej, jeśli zmienią się wymagania aplikacji. W tym artykule pokazano, jak skalować pamięć podręczną przy użyciu witryny Azure portal i narzędzi, takich jak Azure PowerShell i interfejsu wiersza polecenia platformy Azure.

## <a name="when-to-scale"></a>Kiedy skalować
Za pomocą funkcji [monitorowania](cache-how-to-monitor.md) usługi Azure Cache for Redis można monitorować kondycję i wydajność pamięci podręcznej oraz określić, kiedy należy skalować pamięć podręczną. 

Można monitorować następujące metryki, aby ustalić, czy trzeba skalować.

* Obciążenie serwera Redis
* Użycie pamięci
* Przepustowość sieci
* Użycie procesora

Jeśli okaże się, że pamięć podręczna nie spełnia już wymagań aplikacji, można skalować do większej lub mniejszej warstwy cenowej pamięci podręcznej, która jest odpowiednia dla aplikacji. Aby uzyskać więcej informacji na temat określania warstwy cenowej pamięci podręcznej do użycia, [zobacz, co usługa Azure Cache dla oferty Redis i rozmiar powinienem używać](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Skalowanie pamięci podręcznej
Aby skalować pamięć [podręczną, przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w [witrynie Azure portal](https://portal.azure.com) i kliknij polecenie **Skaluj** z **menu Zasób**.

![Skalowanie](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Wybierz żądaną warstwę cenową z bloku **Wybierz warstwę cenową** i kliknij przycisk **Wybierz**.

![Warstwa cenowa][redis-cache-pricing-tier-blade]


Można skalować do innej warstwy cenowej z następującymi ograniczeniami:

* Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
  * Nie można skalować z pamięci podręcznej **Premium** do **standardowej** lub **podstawowej** pamięci podręcznej.
  * Nie można skalować z pamięci podręcznej **standardowej** w dół do **podstawowej** pamięci podręcznej.
* Można skalować z **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, można wykonać kolejną operację skalowania do żądanego rozmiaru.
* Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium.** Najpierw skaluj od **podstawowego** do **standardowego** w jednej operacji skalowania, a następnie od **standardowego** do **premium** w kolejnej operacji skalowania.
* Nie można skalować od większego rozmiaru do rozmiaru **C0 (250 MB).**
 
Podczas gdy pamięć podręczna jest skalowanie do nowej warstwy cenowej, stan **skalowania** jest wyświetlany w **pamięci podręcznej azure dla redis** bloku.

![Skalowanie][redis-cache-scaling]

Po zakończeniu skalowania stan zmienia się z **Skalowania** na **Uruchomiony**.

## <a name="how-to-automate-a-scaling-operation"></a>Jak zautomatyzować operację skalowania
Oprócz skalowania wystąpień pamięci podręcznej w witrynie Azure portal, można skalować przy użyciu poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure i przy użyciu bibliotek zarządzania platformy Microsoft Azure (MAML). 

* [Skalowanie przy użyciu programu PowerShell](#scale-using-powershell)
* [Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure](#scale-using-azure-cli)
* [Skalowanie przy użyciu maml](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skalowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można skalować pamięć podręczną platformy Azure dla wystąpień Redis za pomocą programu PowerShell `Size` `Sku`przy `ShardCount` użyciu polecenia cmdlet [Set-AzRedisCache,](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) gdy , lub właściwości są modyfikowane. W poniższym przykładzie pokazano, `myCache` jak skalować pamięć podręczną o nazwie do pamięci podręcznej 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Aby uzyskać więcej informacji na temat skalowania za pomocą programu PowerShell, zobacz [Skalowanie pamięci podręcznej platformy Azure dla programu Redis przy użyciu programu Powershell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure
Aby skalować pamięć podręczną platformy Azure dla `azure rediscache set` wystąpień Redis przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie i przekaż żądane zmiany konfiguracji, które zawierają nowy rozmiar, sku lub rozmiar klastra, w zależności od żądanej operacji skalowania.

Aby uzyskać więcej informacji na temat skalowania za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Zmienianie ustawień istniejącej pamięci podręcznej Azure cache for Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skalowanie przy użyciu maml
Aby skalować pamięć podręczną platformy Azure dla wystąpień Redis przy `IRedisOperations.CreateOrUpdate` użyciu [bibliotek zarządzania platformy Microsoft Azure (MAML),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)należy wywołać metodę i przekazać w nowym rozmiarze dla . `RedisProperties.SKU.Capacity`

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Aby uzyskać więcej informacji, zobacz [przykład zarządzania pamięcią podręczną azure dla programu Redis przy użyciu narzędzia MAML.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>Skalowanie często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania usługi Azure Cache for Redis.

* [Czy mogę skalować do, z lub w obrębie pamięci podręcznej Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Czy po skalowaniu muszę zmienić nazwę pamięci podręcznej lub klucze dostępu?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak działa skalowanie?](#how-does-scaling-work)
* [Czy podczas skalowania utracę dane z pamięci podręcznej?](#will-i-lose-data-from-my-cache-during-scaling)
* [Czy podczas skalowania ma wpływ na ustawienie niestandardowych baz danych?](#is-my-custom-databases-setting-affected-during-scaling)
* [Czy moja pamięć podręczna będzie dostępna podczas skalowania?](#will-my-cache-be-available-during-scaling)
* Dlaczego po skonfigurowaniu replikacji geograficznej nie mogę skalować pamięci podręcznej ani zmieniać fragmentów w klastrze?
* [Operacje, które nie są obsługiwane](#operations-that-are-not-supported)
* [Jak długo trwa skalowanie?](#how-long-does-scaling-take)
* [Jak sprawdzić, kiedy skalowanie jest zakończone?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Czy mogę skalować do, z lub w obrębie pamięci podręcznej Premium?
* Nie można skalować z pamięci podręcznej **Premium** do warstwy cenowej **Podstawowa** lub **Standardowa.**
* Można skalować z jednej warstwy cenowej pamięci podręcznej **Premium** do innej.
* Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium.** Najpierw skaluj od **podstawowego** do **standardowego** w jednej operacji skalowania, a następnie od **standardowego** do **premium** w kolejnej operacji skalowania.
* Jeśli podczas tworzenia pamięci podręcznej **Premium** włączono klastrowanie, można [zmienić rozmiar klastra](cache-how-to-premium-clustering.md#cluster-size). Jeśli pamięć podręczna została utworzona bez włączonego klastrowania, można skonfigurować klastrowanie w późniejszym czasie.
  
  Aby uzyskać więcej informacji, zobacz [Jak skonfigurować klastrowanie dla pamięci podręcznej premium Azure dla programu Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Czy po skalowaniu muszę zmienić nazwę pamięci podręcznej lub klucze dostępu?
Nie, nazwa pamięci podręcznej i klucze pozostają niezmienione podczas operacji skalowania.

### <a name="how-does-scaling-work"></a>Jak działa skalowanie?
* Gdy **podstawowa** pamięć podręczna jest skalowana do innego rozmiaru, jest zamykana, a nowa pamięć podręczna jest aprowizowana przy użyciu nowego rozmiaru. W tym czasie pamięć podręczna jest niedostępna, a wszystkie dane w pamięci podręcznej są tracone.
* Gdy **podstawowa** pamięć podręczna jest skalowana do **standardowej** pamięci podręcznej, pamięć podręczna repliki jest aprowizowana, a dane są kopiowane z podstawowej pamięci podręcznej do pamięci podręcznej repliki. Pamięć podręczna pozostaje dostępna podczas procesu skalowania.
* Gdy **standardowa** pamięć podręczna jest skalowana do innego rozmiaru lub do pamięci podręcznej **Premium,** jedna z replik jest zamykana i ponownie aprowizowana do nowego rozmiaru, a dane przesyłane za, a następnie inna replika wykonuje tryb failover przed ponownym aprowizjem, podobnie jak proces, który występuje podczas awarii jednego z węzłów pamięci podręcznej.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Czy podczas skalowania utracę dane z pamięci podręcznej?
* Gdy **podstawowa** pamięć podręczna jest skalowana do nowego rozmiaru, wszystkie dane są tracone, a pamięć podręczna jest niedostępna podczas operacji skalowania.
* Gdy **podstawowa** pamięć podręczna jest skalowana do **standardowej** pamięci podręcznej, dane w pamięci podręcznej są zazwyczaj zachowywane.
* Gdy **standardowa** pamięć podręczna jest skalowana do większego rozmiaru lub warstwy lub pamięć podręczna **w warstwie Premium** jest skalowana do większego rozmiaru, wszystkie dane są zazwyczaj zachowywane. Podczas skalowania pamięci podręcznej **standardowej** lub **premium** w dół do mniejszego rozmiaru, dane mogą zostać utracone w zależności od ilości danych w pamięci podręcznej związane z nowym rozmiarem, gdy jest skalowany. Jeśli dane zostaną utracone podczas skalowania w dół, klucze są eksmitowane przy użyciu zasad eksmisji [allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Czy podczas skalowania ma wpływ na ustawienie niestandardowych baz danych?
Jeśli skonfigurowano wartość niestandardową `databases` dla tego ustawienia podczas tworzenia pamięci podręcznej, należy pamiętać, że niektóre warstwy cenowe mają różne [limity baz danych](cache-configure.md#databases). Oto kilka zagadnień podczas skalowania w tym scenariuszu:

* Podczas skalowania do warstwy cenowej z niższym `databases` limitem niż bieżąca warstwa:
  * Jeśli używasz domyślnej `databases`liczby , która wynosi 16 dla wszystkich warstw cenowych, żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej `databases` liczby, która mieści się w granicach warstwy, `databases` do której są skalowanie, to ustawienie jest zachowywane i żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej `databases` liczby, która przekracza limity nowej `databases` warstwy, ustawienie jest obniżane do limitów nowej warstwy i wszystkie dane w usuniętych bazach danych zostaną utracone.
* Podczas skalowania do warstwy cenowej `databases` z takim samym lub `databases` wyższym limitem niż bieżąca warstwa ustawienie jest zachowywane i żadne dane nie są tracone.

Podczas gdy pamięci podręczne Standard i Premium mają 99,9% umowy SLA dla dostępności, nie ma umowy SLA dla utraty danych.

### <a name="will-my-cache-be-available-during-scaling"></a>Czy moja pamięć podręczna będzie dostępna podczas skalowania?
* **Standardowe** i **premium** pamięci podręczne pozostają dostępne podczas operacji skalowania. Jednak blips połączenia może wystąpić podczas skalowania pamięci podręcznych standard i Premium, a także podczas skalowania z podstawowej do standardowej pamięci podręcznej. Oczekuje się, że te połączenia blips będą małe i redis klienci powinni być w stanie przywrócić ich połączenie natychmiast.
* **Podstawowe** pamięci podręczne są w trybie offline podczas skalowania operacji do innego rozmiaru. Podstawowe pamięci podręczne pozostają dostępne podczas skalowania z **podstawowego** do **standardowego,** ale mogą wystąpić małe blip połączenia. Jeśli wystąpi blip połączenia, redis klienci powinni być w stanie przywrócić ich połączenie natychmiast.


### <a name="scaling-limitations-with-geo-replication"></a>Ograniczenia skalowania za pomocą replikacji geograficznej

Po dodaniu łącza replikacji geograficznej między dwiema pamięciami podręcznymi nie będzie już można zainicjować operacji skalowania ani zmienić liczby fragmentów w klastrze. Należy odłączyć pamięć podręczną, aby wydać te polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji geograficznej](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operacje, które nie są obsługiwane
* Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
  * Nie można skalować z pamięci podręcznej **Premium** do **standardowej** lub **podstawowej** pamięci podręcznej.
  * Nie można skalować z pamięci podręcznej **standardowej** w dół do **podstawowej** pamięci podręcznej.
* Można skalować z **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, można wykonać kolejną operację skalowania do żądanego rozmiaru.
* Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium.** Pierwsza skala od **podstawowej** do **standardowej** w jednej operacji skalowania, a następnie skaluj z **standardowej** do **premium** w kolejnej operacji.
* Nie można skalować od większego rozmiaru do rozmiaru **C0 (250 MB).**

Jeśli operacja skalowania nie powiedzie się, usługa próbuje przywrócić operację, a pamięć podręczna powróci do oryginalnego rozmiaru.


### <a name="how-long-does-scaling-take"></a>Jak długo trwa skalowanie?
Skalowanie czasu zależy od ilości danych w pamięci podręcznej, z większych ilości danych trwa dłużej, aby zakończyć. Skalowanie trwa około 20 minut. W przypadku klastrowanych pamięci podręcznych skalowanie trwa około 20 minut na fragment.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak sprawdzić, kiedy skalowanie jest zakończone?
W witrynie Azure portal można wyświetlić operację skalowania w toku. Po zakończeniu skalowania stan pamięci podręcznej zmienia się na **Uruchomiony**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
