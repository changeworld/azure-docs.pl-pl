---
title: Plan usługi Azure Functions Premium
description: Szczegóły i opcje konfiguracji (sieć wirtualna, brak zimnego rozruchu, nieograniczony czas wykonywania) dla planu Usługi Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276909"
---
# <a name="azure-functions-premium-plan"></a>Plan usługi Azure Functions Premium

Plan usługi Azure Functions Premium (czasami nazywany planem Elastic Premium) jest opcją hostingu dla aplikacji funkcji. Plan Premium zapewnia funkcje, takie jak łączność sieci wirtualnej, brak zimnego rozruchu i sprzęt premium.  Wiele aplikacji funkcji można wdrożyć w tym samym planie Premium, a plan umożliwia skonfigurowanie rozmiaru wystąpienia obliczeniowego, rozmiaru planu podstawowego i maksymalnego rozmiaru planu.  Aby zapoznać się z porównaniem planu Premium z innymi typami planu i hostingu, zobacz [skalę funkcji i opcje hostingu](functions-scale.md).

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Można również utworzyć plan Premium przy użyciu [planu az functionapp utworzyć](/cli/azure/functionapp/plan#az-functionapp-plan-create) w interfejsu wiersza polecenia platformy Azure. Poniższy przykład tworzy _plan warstwy Elastic Premium 1:_

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

W tym przykładzie zastąp `<RESOURCE_GROUP>` grupą zasobów i `<PLAN_NAME>` nazwą planu, która jest unikatowa w grupie zasobów. Określ [obsługiwany `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions)plik . Aby utworzyć plan Premium, który `--is-linux` obsługuje linuksa, dołącz opcję.

Po utworzeniu planu można użyć [az functionapp create,](/cli/azure/functionapp#az-functionapp-create) aby utworzyć aplikację funkcji. W portalu zarówno plan, jak i aplikacja są tworzone w tym samym czasie. Na przykład pełnego skryptu interfejsu wiersza polecenia platformy Azure zobacz [Tworzenie aplikacji funkcji w planie Premium.](scripts/functions-cli-create-premium-plan.md)

## <a name="features"></a>Funkcje

Następujące funkcje są dostępne dla aplikacji funkcji wdrożonych w planie Premium.

### <a name="pre-warmed-instances"></a>Wstępnie rozgrzane wystąpienia

Jeśli żadne zdarzenia i wykonania występują dzisiaj w planie zużycia, aplikacja może skalować w wystąpieniach do zera. Gdy pojawią się nowe zdarzenia, nowe wystąpienie musi być wyspecjalizowane przy uruchomionej aplikacji.  Specjalizacja nowych wystąpień może zająć trochę czasu w zależności od aplikacji.  To dodatkowe opóźnienie przy pierwszym wywołaniu jest często nazywany aplikacji zimny start.

W planie Premium możesz wstępnie rozgrzać aplikację w określonej liczbie wystąpień, do minimalnego rozmiaru planu.  Wstępnie rozgrzane wystąpienia umożliwiają również wstępne skalowanie aplikacji przed dużym obciążeniem. Gdy aplikacja jest skalowana w poziomie, najpierw skaluje się do wstępnie rozgrzanych wystąpień. Dodatkowe wystąpienia nadal buforować i ogrzewać natychmiast w ramach przygotowań do następnej operacji skalowania. Dzięki buforowi wstępnie rozgrzanych wystąpień można skutecznie uniknąć opóźnień rozruchu zimnego.  Wstępnie rozgrzane wystąpienia jest cechą planu Premium i trzeba zachować co najmniej jedno wystąpienie uruchomione i dostępne przez cały czas plan jest aktywny.

Liczbę wstępnie rozgrzanych wystąpień w witrynie Azure portal można skonfigurować, wybierając **aplikację funkcji,** przechodząc na kartę **Funkcje platformy** i wybierając opcje **Skaluj w poziomie.** W oknie edycji aplikacji funkcji wstępnie rozgrzane wystąpienia są specyficzne dla tej aplikacji, ale minimalne i maksymalne wystąpienia mają zastosowanie do całego planu.

![Ustawienia skali elastycznej](./media/functions-premium-plan/scale-out.png)

Można również skonfigurować wstępnie rozgrzane wystąpienia dla aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Łączność z siecią prywatną

Usługa Azure Functions wdrożona w planie Premium korzysta z [nowej integracji sieci wirtualnej dla aplikacji sieci web.](../app-service/web-sites-integrate-with-vnet.md)  Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczone za pośrednictwem punktów końcowych usługi.  Ograniczenia adresów IP są również dostępne w aplikacji, aby ograniczyć ruch przychodzący.

Podczas przypisywania podsieci do aplikacji funkcji w planie Premium, potrzebujesz podsieci z wystarczającą ilością adresów IP dla każdego potencjalnego wystąpienia. Wymagamy bloku IP z co najmniej 100 dostępnymi adresami.

Aby uzyskać więcej informacji, zobacz [integrowanie aplikacji funkcji z siecią wirtualną](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Szybka elastyczna waga

Dodatkowe wystąpienia obliczeniowe są automatycznie dodawane dla aplikacji przy użyciu tej samej logiki szybkiego skalowania, co plan zużycia.  Aby dowiedzieć się więcej o tym, jak działa skalowanie, zobacz [Skala funkcji i hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Dłuższy czas trwania biegu

Usługi Azure w planie zużycia są ograniczone do 10 minut dla pojedynczego wykonania.  W planie Premium czas trwania uruchomienia domyślnie wynosi 30 minut, aby zapobiec runaway egzekucji. Można jednak [zmodyfikować konfigurację host.json,](./functions-host-json.md#functiontimeout) aby to niezwiązane z aplikacjami planu Premium (gwarantowane 60 minut).

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Podczas tworzenia planu można skonfigurować dwa ustawienia: minimalną liczbę wystąpień (lub rozmiar planu) i maksymalny limit serii.  Minimalne wystąpienia są zarezerwowane i zawsze uruchomione.

> [!IMPORTANT]
> Opłaty są naliczane dla każdego wystąpienia przydzielonego w minimalnej liczbie wystąpień, niezależnie od tego, czy funkcje są wykonywane, czy nie.

Jeśli aplikacja wymaga wystąpień poza rozmiar planu, może nadal skalować w poziomie, aż liczba wystąpień osiągnie maksymalny limit serii.  Opłaty są naliczane za wystąpienia wykraczające poza rozmiar planu tylko wtedy, gdy są one uruchomione i wynajmowane.  Dołożymy wszelkich starań, aby skalować aplikację do zdefiniowanego maksymalnego limitu, podczas gdy wystąpienia planu minimalnego są gwarantowane dla aplikacji.

Rozmiar planu i wartości maksymalne można skonfigurować w witrynie Azure portal, wybierając opcje **Skalowania** w poziomie w planie lub aplikacji funkcji wdrożonej w tym planie (w obszarze **Funkcje platformy).**

Można również zwiększyć maksymalny limit serii z interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostępne jednostki SKU instancji

Podczas tworzenia lub skalowania planu, można wybrać jeden z trzech rozmiarów wystąpień.  Zostanie naliczona naliczona całkowitej liczby rdzeni i pamięci zużywanej na sekundę.  Aplikacja może automatycznie skalować w poziomie do wielu wystąpień w razie potrzeby.  

|SKU|Rdzenie|Memory (Pamięć)|Magazyn|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Zagadnienia dotyczące wykorzystania pamięci
Uruchamianie na komputerze z większą pamięcią nie zawsze oznacza, że aplikacja funkcji będzie używać wszystkich dostępnych pamięci.

Na przykład aplikacja funkcji JavaScript jest ograniczona domyślnym limitem pamięci w pliku Node.js. Aby zwiększyć ten stały limit pamięci, dodaj `languageWorkers:node:arguments` `--max-old-space-size=<max memory in MB>`ustawienie aplikacji z wartością .

## <a name="region-max-scale-out"></a>Maksymalna skala regionu w poziomie

Poniżej znajdują się aktualnie obsługiwane maksymalne wartości skalowania w poziomie dla pojedynczego planu w każdym regionie i konfiguracji systemu operacyjnego. Aby poprosić o zwiększenie, otwórz bilet pomocy technicznej.

Zobacz pełną regionalną dostępność funkcji tutaj: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Australia Środkowa| 20 | Niedostępne |
|Australia Środkowa 2| 20 | Niedostępne |
|Australia Wschodnia| 100 | 20 |
|Australia Południowo-Wschodnia | 100 | 20 |
|Brazylia Południowa| 60 | 20 |
|Kanada Środkowa| 100 | 20 |
|Środkowe stany USA| 100 | 20 |
|Azja Wschodnia| 100 | 20 |
|Wschodnie stany USA | 100 | 20 |
|Wschodnie stany USA 2| 100 | 20 |
|Francja Środkowa| 100 | 20 |
|Niemcy Zachodnio-Środkowe| 100 | Niedostępne |
|Japonia Wschodnia| 100 | 20 |
|Japonia Zachodnia| 100 | 20 |
|Korea Środkowa| 100 | 20 |
|Północno-środkowe stany USA| 100 | 20 |
|Europa Północna| 100 | 20 |
|Norwegia Wschodnia| 20 | 20 |
|Południowo-środkowe stany USA| 100 | 20 |
|Indie Południowe | 100 | Niedostępne |
|Azja Południowo-Wschodnia| 100 | 20 |
|Południowe Zjednoczone Królestwo| 100 | 20 |
|Zachodnie Zjednoczone Królestwo| 100 | 20 |
|Europa Zachodnia| 100 | 20 |
|Indie Zachodnie| 100 | 20 |
|Zachodnio-środkowe stany USA| 20 | 20 |
|Zachodnie stany USA| 100 | 20 |
|Zachodnie stany USA 2| 100 | 20 |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Opis opcji skalowania i hostingu usług Azure Functions](functions-scale.md)
