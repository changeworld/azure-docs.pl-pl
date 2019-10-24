---
title: Plan Azure Functions Premium (wersja zapoznawcza) | Microsoft Docs
description: Szczegóły i opcje konfiguracji (Sieć wirtualna, brak nieograniczonego czasu wykonywania) dla planu Azure Functions Premium.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: ce83d521d5bc986be7bb24ef874f1f0e1051e3ae
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755410"
---
# <a name="azure-functions-premium-plan-preview"></a>Plan Azure Functions Premium (wersja zapoznawcza)

Plan Azure Functions Premium to opcja hostingu dla aplikacji funkcji. Plan Premium oferuje funkcje, takie jak łączność sieci wirtualnej, sprzęt zimnego startu i Premium.  Wiele aplikacji funkcji można wdrożyć w tym samym planie Premium, a plan umożliwia skonfigurowanie rozmiaru wystąpienia obliczeniowego, rozmiaru planu bazowego i maksymalnego rozmiaru planu.  Aby zapoznać się z porównaniem planu Premium i innych typów planów i hostingu, zobacz [Funkcja skalowanie i opcje hostingu](functions-scale.md).

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Plan Premium można również utworzyć za pomocą polecenia [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) w interfejsie wiersza polecenia platformy Azure. W poniższym przykładzie jest tworzony plan warstwy _Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

W tym przykładzie Zastąp `<RESOURCE_GROUP>` w grupie zasobów i `<PLAN_NAME>` nazwą planu, który jest unikatowy w grupie zasobów. Określ [obsługiwaną `<REGION>`](#regions). Aby utworzyć plan Premium, który obsługuje system Linux, należy uwzględnić opcję `--is-linux`.

Po utworzeniu planu możesz użyć [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) , aby utworzyć aplikację funkcji. W portalu zarówno plan, jak i aplikacja są tworzone w tym samym czasie. 

## <a name="features"></a>Funkcje

Następujące funkcje są dostępne dla aplikacji funkcjonalnych wdrożonych w planie Premium.

### <a name="pre-warmed-instances"></a>Wystąpienia przed wystąpieniem

Jeśli w planie zużycia nie wystąpiły żadne zdarzenia i wykonania, aplikacja może skalować w dół do wartości zero. Gdy pojawią się nowe zdarzenia, nowe wystąpienie musi być wyspecjalizowane dla działającej aplikacji.  Specjalizacja nowych wystąpień może zająć trochę czasu w zależności od aplikacji.  To dodatkowe opóźnienie pierwszego wywołania jest często nazywane aplikacją zimnego startu.

W planie Premium aplikacja została wstępnie przegrzana na określoną liczbę wystąpień do minimalnej wielkości planu.  Wystąpienia przed obciążeniem umożliwiają również wstępne skalowanie aplikacji. Gdy aplikacja jest skalowana w poziomie, najpierw skaluje się do wygrzanych wystąpień. Dodatkowe wystąpienia kontynuują buforowanie i podgrzewanie natychmiast po przygotowaniu dla następnej operacji skalowania. Dzięki buforowi wstępnie wygrzanych wystąpień można skutecznie uniknąć opóźnień zimnego uruchamiania.  Wystąpienia wstępnie działające to funkcja planu Premium, która wymaga, aby co najmniej jedno wystąpienie było uruchomione i dostępne we wszystkich przypadkach, gdy plan jest aktywny.

Liczbę wstępnie rozgrzanych wystąpień można skonfigurować w Azure Portal przez wybranie **aplikacja funkcji**, przejście na kartę **funkcje platformy** i wybranie opcji **skalowania w poziomie** . W oknie Edycja aplikacji funkcji, wstępnie rozgrzane wystąpienia są specyficzne dla tej aplikacji, ale minimalne i maksymalne wystąpienia są stosowane do całego planu.

![Ustawienia skalowania elastycznego](./media/functions-premium-plan/scale-out.png)

Możesz również skonfigurować wystąpienia wstępnie zagrzane dla aplikacji za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Łączność sieci prywatnej

Azure Functions wdrożone w planie Premium wykorzystuje [nową integrację sieci wirtualnej dla usługi Web Apps](../app-service/web-sites-integrate-with-vnet.md).  Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczonymi za pośrednictwem punktów końcowych usługi.  Ograniczenia adresów IP są również dostępne w aplikacji w celu ograniczenia ruchu przychodzącego.

Podczas przypisywania podsieci do aplikacji funkcji w planie Premium potrzebna jest podsieć z wystarczającą liczbą adresów IP dla każdego potencjalnego wystąpienia. Chociaż maksymalna liczba wystąpień może się różnić w wersji zapoznawczej, wymagamy bloku IP z co najmniej 100 dostępnych adresów.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Szybka Skala elastycznych

Dodatkowe wystąpienia obliczeniowe są automatycznie dodawane do aplikacji przy użyciu tej samej logiki szybkiego skalowania jako planu zużycia.  Aby dowiedzieć się więcej o tym, jak działa skalowanie, zobacz [Funkcja skalowanie i hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Niezwiązany czas trwania przebiegu

Azure Functions w planie zużycia są ograniczone do 10 minut w przypadku pojedynczego wykonania.  W planie Premium wartość czasu trwania przebiegu jest domyślnie równa 30 minut, aby uniemożliwić przemijające wykonania. Można jednak [zmodyfikować konfigurację pliku host. JSON](./functions-host-json.md#functiontimeout) w taki sposób, aby nie były one powiązane z aplikacjami planu Premium.

W wersji zapoznawczej czas trwania nie jest gwarantowany w ciągu ostatnich 12 minut i będzie miał najlepszą szansę uruchomienia ponad 30 minut, jeśli Twoja aplikacja nie będzie skalowana poza minimalną liczbą procesów roboczych.

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Podczas tworzenia planu można skonfigurować dwa ustawienia: minimalną liczbę wystąpień (lub rozmiar planu) i maksymalny limit.  Minimalne wystąpienia planu Premium to 1, a maksymalna liczba serii w wersji zapoznawczej to 20.  Minimalna liczba wystąpień jest zarezerwowana i zawsze uruchomiona.

> [!IMPORTANT]
> Opłaty są naliczane za każde wystąpienie przydzieloną w minimalnej liczbie wystąpień niezależnie od tego, czy funkcje są wykonywane, czy nie.

Jeśli aplikacja wymaga wystąpień poza rozmiarem planu, można nadal skalować w poziomie, dopóki liczba wystąpień osiągnie maksymalny limit.  Opłaty są naliczane za wystąpienia poza rozmiarem planu, gdy są one uruchomione i dzierżawione.  W celu skalowania aplikacji do zdefiniowanego maksymalnego limitu zostanie osiągnięty najlepszy nakład pracy, podczas gdy minimalne wystąpienia planu są gwarantowane dla aplikacji.

Rozmiar planu i maksymalne wartości można skonfigurować w Azure Portal przez wybranie opcji **skalowania w poziomie** w ramach planu lub aplikacji funkcji wdrożonej w ramach tego planu (w obszarze **funkcje platformy**).

Możesz również zwiększyć maksymalny limit obciążeń z poziomu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostępne jednostki SKU wystąpienia

Podczas tworzenia lub skalowania planu można wybrać jeden z trzech rozmiarów wystąpień.  Opłaty zostaną naliczone za łączną liczbę rdzeni i zużywaną pamięć na sekundę.  Aplikacja może automatycznie skalować w poziomie do wielu wystąpień stosownie do potrzeb.  

|JSZ|Rdzenie|Pamięć|Usługa Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Regiony

Poniżej znajdują się obecnie obsługiwane regiony dla publicznej wersji zapoznawczej dla każdego systemu operacyjnego.

|Region| Windows | Linux |
|--| -- | -- |
|Australia Środkowa| ✔ * | |
|Australia Środkowa 2| ✔ * | |
|Australia Wschodnia| ✔ | |
|Australia Południowo-Wschodnia | ✔ | ✔ |
|Brazylia Południowa| ✔ * * |  |
|Kanada Środkowa| ✔ |  |
|Środkowe stany USA| ✔ |  |
|Azja Wschodnia| ✔ |  |
|Wschodnie stany USA | ✔ | ✔ |
|Wschodnie stany USA 2| ✔ |  |
|Francja Środkowa| ✔ |  |
|Japonia Wschodnia| ✔ | ✔ |
|Japonia Zachodnia| ✔ | |
|Korea Środkowa| ✔ |  |
|Północno-środkowe stany USA| ✔ |  |
|Europa Północna| ✔ | ✔ |
|Południowo-środkowe stany USA| ✔ |  |
|Indie Południowe | ✔ | |
|Azja Południowo-Wschodnia| ✔ | ✔ |
|Południowe Zjednoczone Królestwo| ✔ | |
|Zachodnie Zjednoczone Królestwo| ✔ |  |
|Europa Zachodnia| ✔ | ✔ |
|Indie Zachodnie| ✔ |  |
|Zachodnie stany USA| ✔ | ✔ |

\* maksymalna skala w poziomie ograniczona do 20 wystąpień

\** Maksymalna długość skalowania w poziomie ograniczona do 60 wystąpień

## <a name="known-issues"></a>Znane problemy

Stan znanych problemów z [publicznej wersji zapoznawczej można śledzić w witrynie GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje na temat Azure Functions skalowanie i opcje hostingu](functions-scale.md)
