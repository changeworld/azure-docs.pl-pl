---
title: Jak skalować pamięć podręczna systemu Azure dla usługi Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować pamięć podręczną Azure dla wystąpienia usługi Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: yegu
ms.openlocfilehash: 495fc031150d04f253279606baebb5d64d52bce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132965"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Jak skalować pamięć podręczna systemu Azure dla usługi Redis
Pamięć podręczna systemu Azure dla usługi Redis zawiera pamięci podręcznej różnych ofert, które zapewniają elastyczność przy wyborze funkcji i rozmiar pamięci podręcznej. Po utworzeniu pamięci podręcznej możesz skalować rozmiar i warstwę cenową w pamięci podręcznej, jeśli zmienią się wymagania dotyczące aplikacji. W tym artykule pokazano, jak skalować pamięć podręczną przy użyciu witryny Azure portal i narzędzi, takich jak Azure PowerShell i wiersza polecenia platformy Azure.

## <a name="when-to-scale"></a>Kiedy skalować
Możesz użyć [monitorowania](cache-how-to-monitor.md) funkcje pamięci podręcznej Azure redis Cache monitorować kondycję i wydajność swojej pamięci podręcznej i określić, kiedy skalować pamięć podręczną. 

Można monitorować następujące metryki, aby określić, jeśli chcesz skalować.

* Obciążenie serwera redis
* Użycie pamięci
* Przepustowość sieci
* Użycie procesora CPU

Jeśli okaże się pamięci podręcznej nie jest już osiąga spełnić wymagania aplikacji, można skalować do pamięci podręcznej większy lub mniejszy ceny warstwy, która jest odpowiednia dla twojej aplikacji. Aby uzyskać więcej informacji na temat określania której pamięci podręcznej warstwy cenowej na użycia, zobacz [jakich pamięć podręczna systemu Azure, oferty pamięci podręcznej Redis i rozmiaru należy używać](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Skalowanie pamięci podręcznej
Skalowanie pamięci podręcznej, [przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **skalowania** z **menu zasobów**.

![Skalowanie](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Wybierz żądaną warstwę cenową z **wybierz warstwę cenową** bloku i kliknij przycisk **wybierz**.

![Warstwa cenowa][redis-cache-pricing-tier-blade]


Można przeprowadzać skalowanie do innej warstwy cenowej, z następującymi zastrzeżeniami:

* Nie można skalować z wyższej warstwy cenowej, do niższej warstwy cenowej.
  * Nie można skalować z **Premium** w pamięci podręcznej w dół **standardowa** lub **podstawowe** pamięci podręcznej.
  * Nie można skalować z **standardowa** w pamięci podręcznej w dół **podstawowe** pamięci podręcznej.
* Możesz skalować z **podstawowe** w pamięci podręcznej **standardowa** pamięci podręcznej, ale nie można zmienić rozmiar w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, można zrobić na żądany rozmiar kolejnych operacji skalowania.
* Nie można skalować z **podstawowe** bezpośrednio do pamięci podręcznej **Premium** pamięci podręcznej. Najpierw skalowanie usług od **podstawowe** do **standardowa** w ramach jednej operacji skalowania, a następnie **standardowa** do **Premium** w kolejnych opcji skalowania Operacja.
* Nie można skalować z większy rozmiar w dół do **C0 (250 MB)** rozmiar.
 
Gdy pamięć podręczna jest skalowanie do nowej warstwy cenowej **skalowanie** stan jest wyświetlany w **pamięci podręcznej Redis Azure** bloku.

![Skalowanie][redis-cache-scaling]

Po zakończeniu skalowanie stan zmieni się z **skalowanie** do **systemem**.

## <a name="how-to-automate-a-scaling-operation"></a>Jak zautomatyzować operacji skalowania
Oprócz skalowanie wystąpień pamięci podręcznej w witrynie Azure portal, można skalować przy użyciu poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure i przy użyciu programu Microsoft Azure Management bibliotek (MAML). 

* [Skalowanie przy użyciu programu PowerShell](#scale-using-powershell)
* [Skalowanie przy użyciu wiersza polecenia platformy Azure](#scale-using-azure-cli)
* [Skalowanie przy użyciu MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skalowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz skalować pamięć podręczną Azure wystąpień pamięci podręcznej Redis przy użyciu programu PowerShell przy użyciu [AzRedisCache zestaw](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) polecenia cmdlet podczas `Size`, `Sku`, lub `ShardCount` właściwości są modyfikowane. Poniższy przykład pokazuje, jak skalować pamięć podręczną o nazwie `myCache` do 2,5 GB pamięci podręcznej. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Aby uzyskać więcej informacji na temat skalowania przy użyciu programu PowerShell, zobacz [skalować pamięć podręczna systemu Azure dla pamięci podręcznej Redis przy użyciu programu Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skalowanie przy użyciu wiersza polecenia platformy Azure
Aby skalować pamięć podręczną Azure wystąpień pamięci podręcznej Redis przy użyciu wiersza polecenia platformy Azure, należy wywołać `azure rediscache set` poleceń i przekazać żądane zmiany w konfiguracji, które zawierają nowy rozmiar, jednostki sku lub rozmiaru klastra, w zależności od żądanej operacji skalowania.

Aby uzyskać więcej informacji na temat skalowania przy użyciu wiersza polecenia platformy Azure, zobacz [zmienić ustawienia istniejących pamięć podręczna systemu Azure dla usługi Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skalowanie przy użyciu MAML
Skalować pamięć podręczną Azure wystąpień pamięci podręcznej Redis przy użyciu [usługi Microsoft Azure Management bibliotek (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), wywołaj `IRedisOperations.CreateOrUpdate` metody i Przekaż nowy rozmiar `RedisProperties.SKU.Capacity`.

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

Aby uzyskać więcej informacji, zobacz [zarządzana pamięć podręczna Azure dla pamięci podręcznej Redis przy użyciu MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) próbki.

## <a name="scaling-faq"></a>Skalowanie — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cache skalowania usługi Redis.

* [Do z lub w ramach cache w warstwie Premium można skalować?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Po skalowania, czy muszę zmienić klucze nazwy lub dostępu do pamięci podręcznej?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak działa skalowania?](#how-does-scaling-work)
* [Spowoduje utratę danych z przepełnieniu pamięci podręcznej podczas skalowania?](#will-i-lose-data-from-my-cache-during-scaling)
* [Moje niestandardowe bazy danych ustawia dotyczy podczas skalowania?](#is-my-custom-databases-setting-affected-during-scaling)
* [Przepełnieniu pamięci podręcznej będą dostępne podczas skalowania?](#will-my-cache-be-available-during-scaling)
* Dzięki replikacji geograficznej, skonfigurowany, dlaczego nie mogę skalować przepełnieniu pamięci podręcznej lub zmienić fragmentów w klastrze?
* [Operacje, które nie są obsługiwane](#operations-that-are-not-supported)
* [Jak skalowanie długo?](#how-long-does-scaling-take)
* [Jak rozpoznać, kiedy jest ukończone skalowania?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Do z lub w ramach cache w warstwie Premium można skalować?
* Nie można skalować z **Premium** w pamięci podręcznej w dół **podstawowe** lub **standardowa** warstwy cenowej.
* Możesz skalować z jednego **Premium** pamięć podręczna warstwy cenowej na inny.
* Nie można skalować z **podstawowe** bezpośrednio do pamięci podręcznej **Premium** pamięci podręcznej. Najpierw skalowanie usług od **podstawowe** do **standardowa** w ramach jednej operacji skalowania, a następnie **standardowa** do **Premium** w kolejnych opcji skalowania Operacja.
* Jeśli włączono klastra podczas tworzenia usługi **Premium** pamięci podręcznej, możesz [zmienić rozmiar klastra](cache-how-to-premium-clustering.md#cluster-size). Jeśli pamięć podręczna została utworzona bez włączone klastrowanie, można skonfigurować, klastrowania w późniejszym czasie.
  
  Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Po skalowania, czy muszę zmienić klucze nazwy lub dostępu do pamięci podręcznej?
Nie, Twoja nazwa pamięci podręcznej i klucze są bez zmian podczas operacji skalowania.

### <a name="how-does-scaling-work"></a>Jak działa skalowania?
* Gdy **podstawowe** skalowania do innego rozmiaru pamięci podręcznej, zostanie zamknięta i nowa pamięć podręczna jest aprowizowane za pomocą nowego rozmiaru. W tym czasie niedostępności pamięci podręcznej, a wszystkie dane w pamięci podręcznej zostaną utracone.
* Gdy **podstawowe** skalowania pamięci podręcznej do **standardowa** pamięci podręcznej pamięć podręczna repliki jest aprowizowana i dane są kopiowane z głównej pamięci podręcznej w pamięci podręcznej repliki. Pamięć podręczna pozostaje dostępna podczas skalowania.
* Gdy **standardowa** pamięci podręcznej jest skalowana, aby zmienić rozmiar lub do **Premium** pamięci podręcznej, jednej z replik zostanie zamknięty i ponownie udostępnić nowy rozmiar danych przesyłanych przez., a następnie innej repliki wykonuje przejścia w tryb failover, zanim zostanie aprowizowany ponownie, podobny do procesu, który występuje podczas awarii jednego z węzłów pamięci podręcznej.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Spowoduje utratę danych z przepełnieniu pamięci podręcznej podczas skalowania?
* Gdy **podstawowe** skalowania na nowy rozmiar pamięci podręcznej, wszystkie dane zostaną utracone i pamięć podręczna jest niedostępna podczas operacji skalowania.
* Gdy **podstawowe** skalowania pamięci podręcznej do **standardowa** pamięci podręcznej, dane w pamięci podręcznej są zwykle zachowywane.
* Gdy **standardowa** skalowania pamięci podręcznej do warstwy, lub rozmiar większy lub **Premium** skalowania na większy rozmiar pamięci podręcznej, wszystkie dane są zwykle zachowywane. Podczas skalowania **standardowa** lub **Premium** pamięci podręcznej do mniejszego rozmiaru, dane zostaną utracone w zależności od tego, jak dużo danych znajduje się w pamięci podręcznej, związane z nowy rozmiar, gdy jest on skalowany. Jeśli dane zostaną utracone podczas skalowania w dół, klucze obrazuje przy użyciu [allkeys lru](https://redis.io/topics/lru-cache) zasady eksmisji. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Moje niestandardowe bazy danych ustawia dotyczy podczas skalowania?
Jeśli skonfigurowano niestandardowej wartości dla `databases` ustawienia podczas tworzenia pamięci podręcznej, należy pamiętać, że niektóre ceny warstwy mają różne [baz danych limity](cache-configure.md#databases). Poniżej przedstawiono kilka kwestii, podczas skalowania w tym scenariuszu:

* Podczas skalowania do warstwy cenowej o niższych `databases` limit niż bieżąca warstwa:
  * Jeśli używasz domyślną liczbę `databases`, czyli 16 dla wszystkich warstw cenowych, zostaną utracone żadne dane.
  * Jeśli używasz niestandardowego szereg `databases` , znajduje się w granicach dla warstwy, do którego jest skalowana, to `databases` ustawienie jest zachowywane i zostaną utracone żadne dane.
  * Jeśli używasz niestandardowego szereg `databases` przekraczający limity nową warstwę `databases` ustawienie jest obniżony limitów nową warstwę, a wszystkie dane w usunięte bazy danych zostaną utracone.
* Podczas skalowania do warstwy cenowej przy użyciu tej samej lub wyższej `databases` limit niż bieżąca warstwa Twojej `databases` ustawienie jest zachowywane i zostaną utracone żadne dane.

Gdy pamięci podręcznych Standard i Premium ma wartość SLA 99,9% dla dostępności, nie ma umowy SLA utraty danych.

### <a name="will-my-cache-be-available-during-scaling"></a>Przepełnieniu pamięci podręcznej będą dostępne podczas skalowania?
* **Standardowa** i **Premium** pamięci podręcznych pozostają dostępne podczas operacji skalowania. Jednak blips połączenia może wystąpić podczas skalowania pamięci podręcznych Standard i Premium, a także możliwości skalowania rozwiązania z podstawowa do standardowa pamięci podręcznych. Te blips połączenia powinny być mały i klientów redis powinno być możliwe natychmiast ponownie ustanowić połączenia.
* **Podstawowe** podczas skalowania operacji do innego rozmiaru pamięci podręcznej jest w trybie offline. Podstawowe pamięci podręcznych pozostają dostępne podczas skalowania z **podstawowe** do **standardowa** , ale mogą wystąpić blip małych połączenia. W przypadku blip połączeń klientów usługi redis powinien móc natychmiast ponownie ustanowić połączenia.


### <a name="scaling-limitations-with-geo-replication"></a>Ograniczenia skalowania dzięki replikacji geograficznej

Po dodaniu łącze replikacji geograficznej między dwoma pamięci podręcznych, nie będzie zainicjować operację skalowania lub zmień liczbę fragmentów w klastrze. Należy odłączyć pamięci podręcznej do wysyłania tych poleceń. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji geograficznej](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operacje, które nie są obsługiwane
* Nie można skalować z wyższej warstwy cenowej, do niższej warstwy cenowej.
  * Nie można skalować z **Premium** w pamięci podręcznej w dół **standardowa** lub **podstawowe** pamięci podręcznej.
  * Nie można skalować z **standardowa** w pamięci podręcznej w dół **podstawowe** pamięci podręcznej.
* Możesz skalować z **podstawowe** w pamięci podręcznej **standardowa** pamięci podręcznej, ale nie można zmienić rozmiar w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, można zrobić na żądany rozmiar kolejnych operacji skalowania.
* Nie można skalować z **podstawowe** bezpośrednio do pamięci podręcznej **Premium** pamięci podręcznej. Najpierw skalowanie usług od **podstawowe** do **standardowa** w jednej operacji skalowania, a następnie skalowanie od **standardowa** do **Premium** w kolejnej Operacja.
* Nie można skalować z większy rozmiar w dół do **C0 (250 MB)** rozmiar.

W przypadku niepowodzenia operacji skalowania, usługa próbuje wykonać operację przywracania i pamięci podręcznej zostaną przywrócone do oryginalnego rozmiaru.


### <a name="how-long-does-scaling-take"></a>Jak skalowanie długo?
Skalowanie zajmuje około 20 minut, w zależności od tego, jak dużo danych znajduje się w pamięci podręcznej.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak rozpoznać, kiedy jest ukończone skalowania?
W witrynie Azure portal zobacz temat operację skalowania w toku. Po zakończeniu skalowania zmiany stanu pamięci podręcznej na **systemem**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



