---
title: Plan Azure Functions Premium
description: Szczegóły i opcje konfiguracji (Sieć wirtualna, brak nieograniczonego czasu wykonywania) dla planu Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: 81db0889294360f74cb42d388e5d875de91c1019
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212473"
---
# <a name="azure-functions-premium-plan"></a>Plan Azure Functions Premium

Plan Azure Functions Premium (nazywany czasem elastycznym planem Premium) jest opcją hostingu dla aplikacji funkcji. Plan Premium oferuje funkcje, takie jak łączność sieci wirtualnej, sprzęt zimnego startu i Premium.  Wiele aplikacji funkcji można wdrożyć w tym samym planie Premium, a plan umożliwia skonfigurowanie rozmiaru wystąpienia obliczeniowego, rozmiaru planu bazowego i maksymalnego rozmiaru planu.  Aby zapoznać się z porównaniem planu Premium i innych typów planów i hostingu, zobacz [Funkcja skalowanie i opcje hostingu](functions-scale.md).

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Plan Premium można również utworzyć za pomocą polecenia [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) w interfejsie wiersza polecenia platformy Azure. W poniższym przykładzie jest tworzony plan warstwy _Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

W tym przykładzie Zastąp `<RESOURCE_GROUP>` w grupie zasobów i `<PLAN_NAME>` nazwą planu, który jest unikatowy w grupie zasobów. Określ [obsługiwaną `<REGION>`](#regions). Aby utworzyć plan Premium, który obsługuje system Linux, należy uwzględnić opcję `--is-linux`.

Po utworzeniu planu możesz użyć [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) , aby utworzyć aplikację funkcji. W portalu zarówno plan, jak i aplikacja są tworzone w tym samym czasie. Aby zapoznać się z przykładem kompletnego skryptu interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie aplikacji funkcji w planie Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funkcje

Następujące funkcje są dostępne dla aplikacji funkcjonalnych wdrożonych w planie Premium.

### <a name="pre-warmed-instances"></a>Wystąpienia przed wystąpieniem

Jeśli w planie zużycia nie wystąpiły żadne zdarzenia i wykonania, aplikacja może skalować się do zero wystąpień. Gdy pojawią się nowe zdarzenia, nowe wystąpienie musi być wyspecjalizowane dla działającej aplikacji.  Specjalizacja nowych wystąpień może zająć trochę czasu w zależności od aplikacji.  To dodatkowe opóźnienie pierwszego wywołania jest często nazywane aplikacją zimnego startu.

W planie Premium aplikacja została wstępnie przegrzana na określoną liczbę wystąpień do minimalnej wielkości planu.  Wystąpienia przed obciążeniem umożliwiają również wstępne skalowanie aplikacji. Gdy aplikacja jest skalowana w poziomie, najpierw skaluje się do wygrzanych wystąpień. Dodatkowe wystąpienia kontynuują buforowanie i podgrzewanie natychmiast po przygotowaniu dla następnej operacji skalowania. Dzięki buforowi wstępnie wygrzanych wystąpień można skutecznie uniknąć opóźnień zimnego uruchamiania.  Wystąpienia wstępnie działające to funkcja planu Premium, która wymaga, aby co najmniej jedno wystąpienie było uruchomione i dostępne we wszystkich przypadkach, gdy plan jest aktywny.

Liczbę wstępnie rozgrzanych wystąpień można skonfigurować w Azure Portal przez wybranie **aplikacja funkcji**, przejście na kartę **funkcje platformy** i wybranie opcji **skalowania w poziomie** . W oknie Edycja aplikacji funkcji, wstępnie rozgrzane wystąpienia są specyficzne dla tej aplikacji, ale minimalne i maksymalne wystąpienia są stosowane do całego planu.

![Ustawienia skalowania elastycznego](./media/functions-premium-plan/scale-out.png)

Możesz również skonfigurować wstępnie rozgrzane wystąpienia dla aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Łączność sieci prywatnej

Azure Functions wdrożone w planie Premium wykorzystuje [nową integrację sieci wirtualnej dla usługi Web Apps](../app-service/web-sites-integrate-with-vnet.md).  Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczonymi za pośrednictwem punktów końcowych usługi.  Ograniczenia adresów IP są również dostępne w aplikacji w celu ograniczenia ruchu przychodzącego.

Podczas przypisywania podsieci do aplikacji funkcji w planie Premium potrzebna jest podsieć z wystarczającą liczbą adresów IP dla każdego potencjalnego wystąpienia. Wymagamy bloku IP z co najmniej 100 dostępnych adresów.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Szybka Skala elastycznych

Dodatkowe wystąpienia obliczeniowe są automatycznie dodawane do aplikacji przy użyciu tej samej logiki szybkiego skalowania jako planu zużycia.  Aby dowiedzieć się więcej o tym, jak działa skalowanie, zobacz [Funkcja skalowanie i hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Dłuższy czas trwania

Azure Functions w planie zużycia są ograniczone do 10 minut w przypadku pojedynczego wykonania.  W planie Premium wartość czasu trwania przebiegu jest domyślnie równa 30 minut, aby uniemożliwić przemijające wykonania. Można jednak [zmodyfikować konfigurację pliku host. JSON](./functions-host-json.md#functiontimeout) w taki sposób, aby 60 minuty dla aplikacji planu Premium.

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Podczas tworzenia planu można skonfigurować dwa ustawienia: minimalną liczbę wystąpień (lub rozmiar planu) i maksymalny limit.  Minimalna liczba wystąpień jest zarezerwowana i zawsze uruchomiona.

> [!IMPORTANT]
> Opłaty są naliczane za każde wystąpienie przydzieloną w minimalnej liczbie wystąpień niezależnie od tego, czy funkcje są wykonywane, czy nie.

Jeśli aplikacja wymaga wystąpień poza rozmiarem planu, można nadal skalować w poziomie, dopóki liczba wystąpień osiągnie maksymalny limit.  Opłaty są naliczane za wystąpienia poza rozmiarem planu, gdy są one uruchomione i dzierżawione.  W celu skalowania aplikacji do zdefiniowanego maksymalnego limitu zostanie osiągnięty najlepszy nakład pracy, podczas gdy minimalne wystąpienia planu są gwarantowane dla aplikacji.

Rozmiar planu i maksymalne wartości można skonfigurować w Azure Portal, wybierając opcje **skalowania w poziomie** w ramach planu lub aplikacji funkcji wdrożonej w ramach tego planu (w obszarze **funkcje platformy**).

Możesz również zwiększyć maksymalny limit obciążeń z poziomu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostępne jednostki SKU wystąpienia

Podczas tworzenia lub skalowania planu można wybrać jeden z trzech rozmiarów wystąpień.  Opłaty zostaną naliczone za łączną liczbę rdzeni i zużywaną pamięć na sekundę.  Aplikacja może automatycznie skalować w poziomie do wielu wystąpień stosownie do potrzeb.  

|SKU|Rdzenie|Pamięć|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250|
|EP2|2|7GB|250|
|EP3|4|14 GB|250|

### <a name="memory-utilization-considerations"></a>Zagadnienia dotyczące wykorzystania pamięci
Uruchamianie na komputerze z większą ilością pamięci nie zawsze oznacza, że aplikacja funkcji będzie używać całej dostępnej pamięci.

Na przykład aplikacja funkcji JavaScript jest ograniczona przez domyślny limit pamięci w programie Node. js. Aby zwiększyć ten limit pamięci ustalonej, należy dodać ustawienie aplikacji `languageWorkers:node:arguments` z wartością `--max-old-space-size=<max memory in MB>`.

## <a name="regions"></a>Regiony

Poniżej znajdują się obecnie obsługiwane regiony dla każdego systemu operacyjnego.

|Region| Windows | Linux |
|--| -- | -- |
|Australia Środkowa| ✔<sup>1</sup> | |
|Australia Środkowa 2| ✔<sup>1</sup> | |
|Australia Wschodnia| ✔ | |
|Australia Południowo-Wschodnia | ✔ | ✔<sup>1</sup> |
|Brazylia Południowa| ✔<sup>2</sup> |  |
|Kanada Środkowa| ✔ |  |
|Środkowe stany USA| ✔ |  |
|Azja Wschodnia| ✔ |  |
|Wschodnie stany USA | ✔ | ✔<sup>1</sup> |
|Wschodnie stany USA 2| ✔ |  |
|Francja Środkowa| ✔ |  |
|Niemcy Środkowo-Zachodnie| ✔ | |
|Japonia Wschodnia| ✔ | ✔<sup>1</sup> |
|Japonia Zachodnia| ✔ | |
|Korea Środkowa| ✔ |  |
|Północno-środkowe stany USA| ✔ |  |
|Europa Północna| ✔ | ✔<sup>1</sup> |
|Południowo-środkowe stany USA| ✔ | ✔<sup>1</sup> |
|Indie Południowe | ✔ | |
|Azja Południowo-Wschodnia| ✔ | ✔<sup>1</sup> |
|Południowe Zjednoczone Królestwo| ✔ | |
|Zachodnie Zjednoczone Królestwo| ✔ |  |
|Europa Zachodnia| ✔ | ✔<sup>1</sup> |
|Indie Zachodnie| ✔ |  |
|Zachodnie stany USA| ✔ | ✔<sup>1</sup> |
|Zachodnie stany USA 2| ✔ |  |

<sup>1</sup> Maksymalna skalowanie w poziomie jest ograniczone do 20 wystąpień.  
<sup>2</sup> Maksymalna wartość skalowania w poziomie ograniczona do 60 wystąpień.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje na temat Azure Functions skalowanie i opcje hostingu](functions-scale.md)
