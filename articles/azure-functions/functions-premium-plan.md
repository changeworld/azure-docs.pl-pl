---
title: Plan usługi Azure Functions — wersja Premium (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Szczegółowe informacje i opcje konfiguracji (sieć wirtualna, nie zimnego, czas trwania wykonywania bez ograniczeń) usługi Azure Functions w wersji Premium planu.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: dab7561db8f223bff87f41ef756605359c3478e4
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492697"
---
# <a name="azure-functions-premium-plan-preview"></a>Plan usługi Azure Functions — wersja Premium (wersja zapoznawcza)

Plan usługi Azure Functions w wersji Premium jest opcji hostingu dla aplikacji funkcji. Plan w warstwie Premium zapewnia funkcje, takie jak połączenia sieci wirtualnej, nie zimnego startu i sprzętu wysokiej klasy.  Wiele aplikacji funkcyjnych, którą można wdrożyć ten sam plan w warstwie Premium, a plan umożliwia skonfigurowanie rozmiaru wystąpienia obliczeniowe, plan podstawowy rozmiar i rozmiar maksymalny planu.  Dla porównania plan w warstwie Premium i inny plan i hosting typów, zobacz [funkcji skalowania i opcje hostowania](functions-scale.md).

> [!NOTE]
> Plan Premium w wersji zapoznawczej obsługuje obecnie funkcje działające na platformie .NET, Node lub Java za pomocą infrastruktury Windows.

## <a name="create-a-premium-plan"></a>Utwórz plan w warstwie Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Można również utworzyć plan w warstwie Premium z wiersza polecenia platformy Azure

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Funkcje

Następujące funkcje są dostępne dla aplikacji funkcji wdrożonych na plan w warstwie Premium.

### <a name="pre-warmed-instances"></a>Wstępnie przygotowaniu wystąpień

Jeśli nie zdarzenia i wykonania występują obecnie planu zużycie, aplikacja może Skaluj w dół do zera wystąpień. Gdy nowe zdarzenia są dostępne w, nowe wystąpienie musi można wyspecjalizować z aplikacji uruchomionej na nim.  Wyspecjalizowanym nowych wystąpień może potrwać pewien czas w zależności od aplikacji.  To dodatkowe opóźnienie przy pierwszym wywołaniu jest często nazywana zimny start aplikacji.

Plan w warstwie Premium mogą mieć aplikację wstępnie przygotowaniu na określoną liczbę wystąpień, z maksymalnie rozmiar minimalny planu.  Wstępnie przygotowaniu wystąpień pozwalają również wstępnie skalować aplikację przed dużym obciążeniem. Ponieważ aplikacja jest skalowana w poziomie, najpierw skaluje do wstępnie warmed wystąpień. Dodatkowe wystąpienia w dalszym ciągu buforu na zewnątrz i do dostępu do ciepłych bezpośrednio w ramach przygotowania do następnej operacji skalowania. Dzięki bufor o przygotowaniu wstępnie wystąpień, można skutecznie uniknąć opóźnienia zimny start.  Przygotowaniu wstępnie wystąpień jest funkcją plan w warstwie Premium i należy zachować co najmniej jedno wystąpienie, uruchomione i dostępne pod adresem cały czas plan jest aktywny.

Liczba wystąpień przygotowaniu wstępnie można skonfigurować w witrynie Azure portal przez wybrany usługi **aplikacji funkcji**, które trafiają na **funkcji platformy** kartę, a następnie wybierając **skalowanie w poziomie**opcje. W oknie edycji aplikacji funkcji przygotowaniu wstępnie wystąpień jest specyficzne dla danej aplikacji, ale minimalne i maksymalne wystąpień dotyczą całego planu.

![Ustawienia elastycznego skalowania](./media/functions-premium-plan/scale-out.png)

Można również skonfigurować przygotowaniu wstępnie wystąpienia aplikacji przy użyciu wiersza polecenia platformy Azure

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Łączność sieci prywatnej

Usługa Azure Functions wdrożyć plan w warstwie Premium wykorzystuje [nową opcję integracji sieci wirtualnej dla aplikacji sieci web](../app-service/web-sites-integrate-with-vnet.md).  Po skonfigurowaniu aplikacja może komunikować się z zasobami w sieci wirtualnej lub zabezpieczone za pomocą punktów końcowych usługi.  Ograniczenia adresów IP są również dostępne w aplikacji, aby ograniczyć ruch przychodzący.

Podczas przypisywania podsieci do aplikacji funkcji w ramach planu Premium, należy podsieci z wystarczającą liczbę adresów IP dla każdego potencjalnego wystąpienia. Chociaż maksymalna liczba wystąpień, mogą się różnić w trakcie okresu zapoznawczego, wymagamy blok z co najmniej 100 dostępnymi adresami IP.

Aby uzyskać więcej informacji, zobacz [zintegrować swoją aplikację funkcji z siecią wirtualną](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Szybkie, elastyczne skalowanie

Dodatkowe zasoby obliczeniowe wystąpień są automatycznie dodawane do swojej aplikacji za pomocą tej samej logiki szybkiego skalowania jako planu zużycie.  Aby dowiedzieć się, jak działa jak skalowania, zobacz [funkcji skalowanie i hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Niepowiązane czas trwania przebiegu

Usługa Azure Functions w ramach planu zużycie są ograniczone do 10 minut na pojedyncze wykonanie.  W planie Premium czas trwania testu wartość domyślna to 30 minut, aby zapobiec wykonań niekontrolowany. Można jednak [zmodyfikować konfigurację host.json](./functions-host-json.md#functiontimeout) się to bez ograniczeń dla aplikacji plan w warstwie Premium.

W wersji zapoznawczej czasu trwania nie ma żadnej gwarancji, ostatnie 12 minut i będzie mieć najlepsze prawdopodobieństwo uruchomiony dłużej niż 30 minut, jeśli aplikacja nie jest skalowana poza jego liczba minimalne procesu roboczego.

## <a name="plan-and-sku-settings"></a>Ustawienia planu i jednostki SKU

Po utworzeniu planu możesz skonfigurować dwa ustawienia: Określa minimalną liczbę wystąpień (lub rozmiar planu) i limit maksymalny dużego ruchu.  Minimalna wystąpienia planu Premium to 1, a maksymalna dużego ruchu w trakcie okresu zapoznawczego wynosi 20.  Minimalna wystąpienia są zarezerwowane i zawsze działa.

> [!IMPORTANT]
> Opłata jest naliczana za każde wystąpienie przydzielone w minimalnej liczby wystąpień niezależnie od tego, jeśli są wykonywane funkcje, czy nie.

Jeśli aplikacja wymaga wystąpień poza rozmiar planu, może kontynuować skalowanie w poziomie, dopóki liczba wystąpień osiąga limit maksymalny dużego ruchu.  Opłaty są naliczane dla wystąpień poza rozmiar planu tylko podczas są uruchomione i Wydzierżawionym do Ciebie.  Podejmiemy najlepszy nakład pracy na skalowanie aplikacji na swoim maksymalnym ograniczeniu zdefiniowanych wystąpień planu minimalne są gwarantowanych dla aplikacji.

Można skonfigurować rozmiar planu i maksymalne wartości w witrynie Azure portal wybrano **skalowanie w poziomie** opcje w planie lub funkcja wdrożenie aplikacji w tym planie (w obszarze **funkcje platformy**).

Można również zwiększyć limit maksymalnej dużego ruchu z wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Dostępnego wystąpienia jednostki SKU

Podczas tworzenia lub skalowania planu, możesz wybrać trzy rozmiarów wystąpień.  Użytkownik jest naliczana dla całkowitej liczby rdzeni i pamięci, użycie na sekundę.  Aplikacja może automatycznie skalować w poziomie do wielu wystąpień zgodnie z potrzebami.  

|SKU|Rdzenie|Memory (Pamięć)|Magazyn|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

## <a name="regions"></a>Regiony

Poniżej są obecnie obsługiwane regiony, w publicznej wersji zapoznawczej.

|Region|
|--|
|Australia Wschodnia|
|Australia Południowo-Wschodnia|
|Kanada Środkowa|
|Środkowe stany USA|
|Azja Wschodnia|
|Wschodnie stany USA 2|
|Francja Środkowa|
|Japonia Zachodnia|
|Korea Środkowa|
|Środkowo-północne stany USA|
|Europa Północna|
|Środkowo-południowe stany USA|
|Indie Południowe|
|Azja Południowo-Wschodnia|
|Zachodnie Zjednoczone Królestwo|
|Europa Zachodnia|
|Indie Zachodnie|
|Zachodnie stany USA|

## <a name="known-issues"></a>Znane problemy

Można śledzić stan znanych problemów z [publicznej wersji zapoznawczej w witrynie GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Omówienie skalowania usługi Azure Functions i opcje hostowania.](functions-scale.md)
