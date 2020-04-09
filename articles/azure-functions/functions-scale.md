---
title: Skalowanie i hosting usługi Azure Functions
description: Dowiedz się, jak wybierać między planem użycia usług Azure Functions a planem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3b000776c04550e1deb883039d94deeb735061ce
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985885"
---
# <a name="azure-functions-scale-and-hosting"></a>Skalowanie i hosting usługi Azure Functions

Podczas tworzenia aplikacji funkcji na platformie Azure, należy wybrać plan hostingu dla aplikacji. Istnieją trzy plany hostingu dostępne dla usługi Azure Functions: [Plan zużycia,](#consumption-plan) [Plan Premium](#premium-plan)i [Dedykowany (Usługa aplikacji).](#app-service-plan)

Plan hostingu, który wybierzesz, dyktuje następujące zachowania:

* Sposób skalowania aplikacji funkcji.
* Zasoby dostępne dla każdego wystąpienia aplikacji funkcji.
* Obsługa zaawansowanych funkcji, takich jak łączność usługi Azure Virtual Network.

Plany Zużycie i Premium automatycznie dodają moc obliczeniową, gdy kod jest uruchomiony. Aplikacja jest skalowana w poziomie, gdy jest to konieczne do obsługi obciążenia i skalowana, gdy kod przestaje działać. W przypadku planu zużycia nie trzeba również płacić za bezczynne maszyny wirtualne ani rezerwować zdolności produkcyjne z góry.  

Plan premium zapewnia dodatkowe funkcje, takie jak wystąpienia obliczeniowe w wersji premium, możliwość utrzymywanie ogrzania wystąpień przez czas nieokreślony i łączność sieci wirtualnej.

Plan usługi app service umożliwia korzystanie z dedykowanej infrastruktury, którą zarządzasz. Aplikacja funkcji nie skaluje się na podstawie zdarzeń, co oznacza, że nigdy nie jest skalowana do zera. (Wymaga, [aby opcja Zawsze włączone).](#always-on)

## <a name="hosting-plan-support"></a>Obsługa planu hostingowego

Obsługa funkcji należy do następujących dwóch kategorii:

* _Ogólnie dostępne (GA)_: w pełni obsługiwane i zatwierdzone do użytku produkcyjnego.
* _Wersja zapoznawcza:_ jeszcze nie jest w pełni obsługiwana ani zatwierdzona do użytku produkcyjnego.

Poniższa tabela wskazuje bieżący poziom obsługi trzech planów hostingu, gdy jest uruchomiony w systemie Windows lub Linux:

| | Plan Zużycie | Plan Premium | Plan dedykowany |
|-|:----------------:|:------------:|:----------------:|
| Windows | Ogólna dostępność | Ogólna dostępność | Ogólna dostępność |
| Linux | Ogólna dostępność | Ogólna dostępność | Ogólna dostępność |

## <a name="consumption-plan"></a>Plan Zużycie

Podczas korzystania z planu zużycia wystąpienia hosta usług Azure Functions są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących. Ten plan bezserwerowy wykorzystuje automatyczne skalowanie, a opłaty są naliczane za zasoby obliczeniowe tylko wtedy, gdy funkcje są uruchamiane. W ramach Planu użycia limit czasu wykonywania funkcji zostaje przekroczony po konfigurowalnym okresie czasu.

Rozliczenia zależą od liczby wykonań, czasu wykonania oraz użytej pamięci. Rozliczenia są agregowane ze wszystkich funkcji w aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [stronę cennika usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plan zużycia jest domyślnym planem hostingu i oferuje następujące korzyści:

* Płać tylko wtedy, gdy funkcje są uruchomione
* Skalowanie w poziomie automatycznie, nawet w okresach wysokiego obciążenia

Aplikacje funkcji w tym samym regionie można przypisać do tego samego planu zużycia. Nie ma żadnych wad ani wpływu na posiadanie wielu aplikacji działających w tym samym planie zużycia. Przypisywanie wielu aplikacji do tego samego planu zużycia nie ma wpływu na odporność, skalowalność lub niezawodność każdej aplikacji.

Aby dowiedzieć się więcej o szacowaniu kosztów podczas uruchamiania w planie zużycia, zobacz [Opis kosztów planu zużycia](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Plan Premium

Podczas korzystania z planu Premium wystąpienia hosta usług Azure Functions są dodawane i usuwane na podstawie liczby zdarzeń przychodzących, podobnie jak plan zużycia.  Plan Premium obsługuje następujące funkcje:

* Nieustannie ciepłe wystąpienia, aby uniknąć zimnego startu
* Łączność sieci wirtualnej
* Nieograniczony czas realizacji (gwarantowany 60 minut)
* Rozmiary wystąpień w wersji premium (jeden rdzeń, dwa podstawowe i cztery wystąpienia podstawowe)
* Bardziej przewidywalne ceny
* Alokacja aplikacji o wysokiej gęstości dla planów z wieloma aplikacjami funkcyjnymi

Informacje na temat konfigurowania tych opcji można znaleźć w [dokumencie planu Usługi Azure Functions Premium](functions-premium-plan.md).

Zamiast rozliczeń za wykonanie i zużytą pamięć, rozliczenia za plan Premium są oparte na liczbie sekund podstawowych i pamięci używanej w potrzebnych i wstępnie rozgrzanych wystąpieniach. Co najmniej jedno wystąpienie musi być ogrzewane przez cały czas na plan. Oznacza to, że istnieje minimalny miesięczny koszt aktywnego planu, niezależnie od liczby wykonań. Należy pamiętać, że wszystkie aplikacje funkcyjne w planie Premium współużytkują wstępnie rozgrzane i aktywne wystąpienia.

Rozważ plan usługi Azure Functions Premium w następujących sytuacjach:

* Aplikacje funkcyjne działają w sposób ciągły lub prawie w sposób ciągły.
* Masz dużą liczbę małych wykonań i masz wysoki rachunek wykonania, ale niski GB drugi rachunek w planie zużycia.
* Potrzebujesz więcej opcji procesora CPU lub pamięci niż to, co jest przewidziane w planie zużycia.
* Kod musi działać dłużej niż [maksymalny czas wykonywania dozwolony](#timeout) w planie zużycia.
* Wymagane są funkcje, które są dostępne tylko w planie Premium, takie jak łączność z siecią wirtualną.

Podczas uruchamiania funkcji JavaScript w planie Premium należy wybrać wystąpienie, które ma mniej procesorów wirtualnych. Aby uzyskać więcej informacji, zobacz [Wybieranie jednordzeniowych planów Premium](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Plan dedykowany (usługa aplikacji)

Aplikacje funkcji można również uruchomić na tych samych dedykowanych maszyn wirtualnych, jak inne aplikacje usługi App Service (podstawowe, standardowe, premium i izolowane jednostki SKU).

Rozważ plan usługi app service w następujących sytuacjach:

* Masz istniejących, niedostatecznie przywdzianych maszyn wirtualnych, które są już uruchomione inne wystąpienia usługi App Service.
* Chcesz podać niestandardowy obraz, na którym mają być uruchamiane funkcje.

Płacisz tyle samo za aplikacje funkcyjne w planie usługi app service, tak jak w przypadku innych zasobów usługi app service, takich jak aplikacje internetowe. Aby uzyskać szczegółowe informacje na temat działania planu usługi app service, zobacz [szczegółowe omówienie planów usługi Azure App Service.](../app-service/overview-hosting-plans.md)

Za pomocą planu usługi app service można ręcznie skalować w poziomie, dodając więcej wystąpień maszyn wirtualnych. Można również włączyć skalowanie automatyczne. Aby uzyskać więcej informacji, zobacz [Skalowanie liczby wystąpień ręcznie lub automatycznie](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Można również skalować w górę, wybierając inny plan usługi app service. Aby uzyskać więcej informacji, zobacz [Skalowanie aplikacji na platformie Azure](../app-service/manage-scale-up.md). 

Podczas uruchamiania funkcji JavaScript w planie usługi app service należy wybrać plan, który ma mniej procesorów wirtualnych. Aby uzyskać więcej informacji, zobacz [Wybieranie jednordzeniowych planów usługi app service](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Zawsze włączony

Jeśli uruchomisz plan usługi app service, należy włączyć **zawsze włączone** ustawienie, tak aby aplikacja funkcji działa poprawnie. W planie usługi app service środowisko uruchomieniowe funkcji przechodzi bezczynności po kilku minutach braku aktywności, więc tylko wyzwalacze HTTP będzie "wybudzić" funkcje. Zawsze włączony jest dostępny tylko w planie usługi app service. W planie zużycia platforma automatycznie aktywuje aplikacje funkcji.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Nawet z włączoną opcję Zawsze włączone limit czasu `functionTimeout` wykonywania dla poszczególnych funkcji jest kontrolowany przez ustawienie w pliku projektu [host.json.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Określanie planu hostingowego istniejącej aplikacji

Aby określić plan hostingu używany przez aplikację funkcji, zobacz **Plan usługi app service / warstwa cenowa** na karcie **Przegląd** aplikacji funkcji w [witrynie Azure portal](https://portal.azure.com). W przypadku planów usługi app service jest również wskazana warstwa cenowa.

![Wyświetlanie planu skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

Można również użyć interfejsu wiersza polecenia platformy Azure, aby określić plan, w następujący sposób:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Gdy dane wyjściowe `dynamic`z tego polecenia jest, aplikacja funkcji znajduje się w planie zużycia. Gdy dane wyjściowe `ElasticPremium`z tego polecenia jest, aplikacja funkcji jest w planie Premium. Wszystkie inne wartości wskazują różne warstwy planu usługi app service.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

W dowolnym planie aplikacja funkcji wymaga ogólnego konta usługi Azure Storage, które obsługuje usługi Azure Blob, queue, files i table storage. Dzieje się tak, ponieważ funkcje opiera się na usłudze Azure Storage dla operacji, takich jak zarządzanie wyzwalania i rejestrowanie wykonywania funkcji, ale niektóre konta magazynu nie obsługują kolejek i tabel. Te konta, które obejmują konta magazynu tylko dla obiektów blob (w tym magazynu w wersji premium) i konta magazynu ogólnego przeznaczenia z replikacją magazynu nadmiarowego strefy, są filtrowane z istniejących opcji **konta magazynu** podczas tworzenia aplikacji funkcji.

To samo konto magazynu używane przez aplikację funkcji może być również używane przez wyzwalacze i powiązania do przechowywania danych aplikacji. Jednak w przypadku operacji intensywnie korzystających z magazynu należy użyć oddzielnego konta magazynu.  

Z pewnością wiele aplikacji funkcji może udostępniać to samo konto magazynu bez żadnych problemów. (Dobrym przykładem jest to, gdy opracowywać wiele aplikacji w środowisku lokalnym przy użyciu emulatora usługi Azure Storage, który działa jak jedno konto magazynu). 

<!-- JH: Does using a Premium Storage account improve perf? -->

Aby dowiedzieć się więcej o typach kont magazynu, zobacz [Wprowadzenie usług Usługi Azure Storage](../storage/common/storage-introduction.md#core-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak działają plany konsumpcji i premii

W planach Zużycie i Premium infrastruktura usługi Azure Functions skaluje zasoby procesora CPU i pamięci, dodając dodatkowe wystąpienia hosta funkcji na podstawie liczby zdarzeń, na których są wyzwalane jego funkcje. Każde wystąpienie hosta funkcji w planie zużycia jest ograniczone do 1,5 GB pamięci i jednego procesora CPU.  Wystąpienie hosta jest cała aplikacja funkcji, co oznacza, że wszystkie funkcje w aplikacji funkcji współużytkują zasób w wystąpieniu i skalować w tym samym czasie. Aplikacje funkcyjne, które współużytkują ten sam plan zużycia są skalowane niezależnie.  W planie Premium rozmiar planu określi dostępną pamięć i procesor CPU dla wszystkich aplikacji w tym planie w tym wystąpieniu.  

Pliki kodu funkcji są przechowywane w udziałach usługi Azure Files na głównym koncie magazynu funkcji. Po usunięciu głównego konta magazynu aplikacji funkcji pliki kodu funkcji są usuwane i nie można ich odzyskać.

### <a name="runtime-scaling"></a>Skalowanie środowiska uruchomieniowego

Usługa Azure Functions używa składnika o nazwie *kontroler skalowania* do monitorowania szybkości zdarzeń i określenia, czy mają być skalowane w poziomie lub skalowania w. Kontroler skalowania używa heurystyki dla każdego typu wyzwalacza. Na przykład podczas korzystania z wyzwalacza magazynu kolejki platformy Azure skaluje się na podstawie długości kolejki i wieku najstarszej wiadomości kolejki.

Jednostką skalowania dla usługi Azure Functions jest aplikacja funkcji. Gdy aplikacja funkcji jest skalowany w poziomie, dodatkowe zasoby są przydzielane do uruchamiania wielu wystąpień hosta usługi Azure Functions. Z drugiej strony, ponieważ zmniejsza się zapotrzebowanie na obliczenia, kontroler skalowania usuwa wystąpienia hosta funkcji. Liczba wystąpień jest po pewnym czasie *skalowana do* zera, gdy w aplikacji funkcji nie są uruchomione żadne funkcje.

![Skalowanie zdarzeń monitorowania kontrolera i tworzenia wystąpień](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Opis zachowań skalowania

Skalowanie może się różnić w zależności od wielu czynników i skalować inaczej na podstawie wybranego wyzwalacza i języka. Istnieje kilka zawiłości zachowań skalowania, o których należy pamiętać:

* Aplikacja z jedną funkcją jest skalowana tylko do maksymalnie 200 wystąpień. Jedno wystąpienie może przetwarzać więcej niż jedną wiadomość lub żądanie w czasie jednak, więc nie ma ustawionego limitu liczby równoczesnych wykonań.
* W przypadku wyzwalaczy HTTP nowe wystąpienia są przydzielane co najwyżej raz na sekundę.
* W przypadku wyzwalaczy innych niż HTTP nowe wystąpienia są przydzielane co najwyżej raz na 30 sekund. Skalowanie jest szybsze podczas uruchamiania w [planie Premium.](#premium-plan)
* W przypadku wyzwalaczy usługi Service Bus użyj _funkcji Zarządzaj_ prawami zasobów, aby uzyskać najbardziej efektywne skalowanie. W przypadku praw _do nasłuchiwać_ skalowanie nie jest tak dokładne, ponieważ długość kolejki nie może służyć do informowania o decyzjach dotyczących skalowania. Aby dowiedzieć się więcej o ustawianiu praw w zasadach dostępu usługi Service Bus, zobacz [Zasady autoryzacji dostępu udostępnionego](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* W przypadku wyzwalaczy Centrum zdarzeń zobacz [wskazówki dotyczące skalowania](functions-bindings-event-hubs-trigger.md#scaling) w artykule referencyjnym. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Najważniejsze wskazówki i wzorce skalowalnych aplikacji

Istnieje wiele aspektów aplikacji funkcji, które będą miały wpływ na to, jak dobrze będzie skalowany, w tym konfiguracji hosta, środowisko uruchomieniowe i efektywne gospodarowanie zasobami.  Aby uzyskać więcej informacji, zobacz [sekcję skalowalności artykułu poświęcone wydajności](functions-best-practices.md#scalability-best-practices). Należy również pamiętać, jak połączenia zachowują się jak aplikacja funkcji skaluje. Aby uzyskać więcej informacji, zobacz [Jak zarządzać połączeniami w usłudze Azure Functions](manage-connections.md).

Aby uzyskać dodatkowe informacje na temat skalowania w językach Python i Node.js, zobacz [przewodnik dla deweloperów języka Azure Functions Python — skalowanie i współbieżność](functions-reference-python.md#scaling-and-concurrency) oraz [przewodnik dla deweloperów usługi Azure Functions Node.js — Skalowanie i współbieżność](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Model rozliczania

Rozliczenia za różne plany są szczegółowo opisane na [stronie cennika usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Użycie jest agregowane na poziomie aplikacji funkcji i liczy tylko czas, w której wykonywany jest kod funkcji. Poniżej przedstawiono jednostki do fakturowania:

* **Zużycie zasobów w gigabajtowych sekundach (GB-s)**. Oblicza się jako kombinację rozmiaru pamięci i czasu wykonywania dla wszystkich funkcji w aplikacji funkcji. 
* **Egzekucje**. Zliczane za każdym razem, gdy funkcja jest wykonywana w odpowiedzi na wyzwalacz zdarzenia.

Przydatne zapytania i informacje na temat zrozumienia rachunku za zużycie można znaleźć [w często zadawanych pytaniach dotyczących rozliczeń.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limity usługi

Poniższa tabela wskazuje limity, które mają zastosowanie do aplikacji funkcyjnych podczas uruchamiania w różnych planach hostingu:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
