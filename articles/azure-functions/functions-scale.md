---
title: Usługa Azure Functions, skalowanie i hosting | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dokonać wyboru między planu usługi Azure Functions zużycie i plan w warstwie Premium.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkcje, planu zużycie, plan w warstwie premium, przetwarzanie zdarzeń, elementów webhook, obliczanie dynamiczne, architektury bezserwerowej
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459758142c94ddfd244df1427dd5632186efc0de
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266941"
---
# <a name="azure-functions-scale-and-hosting"></a>Funkcje Azure podlegają skalowaniu i hosting

Usługa Azure Functions jest uruchamiany w dwóch różnych planów: Plan zużycie i plan w warstwie Premium (publiczna wersja zapoznawcza). Plan zużycie automatycznie dodaje moc obliczeniową, gdy kod jest uruchomiony. Twoja aplikacja jest skalowana w poziomie, gdy trzeba obsłużyć obciążenie i skalowane w dół, gdy kod przestanie działać. Nie trzeba płacić za bezczynnych maszyn wirtualnych lub zarezerwować pojemności z wyprzedzeniem.  Plan w warstwie Premium będzie również automatycznie skalować i Dodaj moc obliczeniową dodatkowe, gdy kod jest uruchomiony.  Plan w warstwie Premium jest powiązana z dodatkowych funkcji, takich jak wystąpienia obliczeniowe — wersja premium, możliwość przechowywania bez wyłączania zasilania wystąpień na czas nieokreślony i połączenie między sieciami.  Jeśli masz istniejący Plan usługi App Service, można również uruchomić aplikacji funkcji w nich.

> [!NOTE]  
> Zarówno [plan w warstwie Premium](https://aka.ms/functions-premiumplan) i [planu zużycie dla systemu Linux](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/) są obecnie dostępne w wersji zapoznawczej.

Jeśli nie znasz usługi Azure Functions, zobacz [omówienia usługi Azure Functions](functions-overview.md).

Podczas tworzenia aplikacji funkcji, możesz wybrać plan hostingu dla funkcji w aplikacji. W dowolnym planie wystąpienie *hosta usługi Azure Functions* wykonuje funkcje. Typ kontrolki plan:

* Jak wystąpień hosta są skalowane do wewnątrz.
* Zasoby, które są dostępne dla każdego hosta.
* Wystąpienie funkcje, takie jak połączenie między sieciami.

> [!NOTE]
> Możesz przełączać się między planów zużycia i Premium, zmieniając właściwości planu zasobu aplikacji funkcji.

## <a name="consumption-plan"></a>Plan Zużycie

Podczas korzystania z planu zużycie wystąpień hosta usługi Azure Functions są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących. Ten plan bez użycia serwera skaluje się automatycznie, a opłaty są naliczane za zasoby obliczeniowe tylko wtedy, gdy funkcje są uruchomione. W ramach planu zużycie wykonywania funkcji upłynie limit czasu po upływie czasu określonego można konfigurować.

Rozliczanie jest na podstawie liczby wykonań, czas wykonywania i używanej pamięci. Opłaty są agregowane w obrębie wszystkich funkcji w ramach aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [Usługa Azure Functions, cennik].

Plan zużycie jest domyślny plan hostingu i zapewnia następujące korzyści:

* Płać tylko, gdy funkcje są uruchomione.
* Skalować automatycznie, nawet w okresach wysokiego obciążenia.

## <a name="premium-plan-public-preview"></a>Plan w warstwie Premium (publiczna wersja zapoznawcza)

Podczas korzystania z planu Premium wystąpień hosta usługi Azure Functions są szybko dodawane i usuwane na podstawie liczby zdarzeń przychodzących, podobnie jak w planie zużycie.  Jednak plan w warstwie Premium oferuje również:

* Zawsze ciepło wystąpienia, aby uniknąć wszelkich zimny start.
* Łączność z siecią wirtualną.
* Czas trwania wykonywania bez ograniczeń.
* Rozmiary wystąpień — wersja Premium (jeden rdzeń, dwa podstawowe i cztery podstawowego wystąpienia).
* Przewidywalny cennik.
* Alokacja aplikacji o wysokiej gęstości w przypadku planów z wielu aplikacji funkcji.

Informacje, w jaki sposób można skonfigurować te opcje można znaleźć w [plan w warstwie premium usługi Azure Functions](functions-premium-plan.md).

Zamiast rozliczeń za wykonywanie i zużywanej pamięci, rozliczeń zależy od liczby sekundy pracy rdzenia i gigabajtosekundy używane w wymaganych i zarezerwowanych wystąpieniach.  Co najmniej jedno wystąpienie odpowiada musi być ciepło przez cały czas, dlatego ma stałej opłaty miesięczne za plan, który jest aktywny (niezależnie od liczby wykonań).

Należy wziąć pod uwagę plan w warstwie premium usługi Azure Functions w następujących przypadkach:
* Twoje aplikacje funkcji Uruchom stale albo w praktycznie w sposób ciągły. W tym przypadku Plan usługi App Service może być bardziej ekonomiczne.
* Potrzebujesz więcej opcji procesora CPU lub pamięci niż jest podana w planu zużycie.
* Twój kod musi zostać uruchomiony dłużej niż [maksymalny dozwolony czas wykonywania](#timeout) w planu zużycie.
* Wymagane funkcje, które są dostępne tylko na plan usługi App Service, takie jak obsługa środowiska App Service Environment, łączność w sieci Wirtualnej/sieci VPN i większych rozmiarów maszyn wirtualnych.

> [!NOTE]
> Plan premium w wersji zapoznawczej obsługuje obecnie funkcje działające na platformie .NET, Node lub Java za pomocą infrastruktury Windows.

Podczas uruchamiania funkcji języka JavaScript w ramach planu Premium, należy wybrać wystąpienie, który ma mniejszą liczbę procesorów wirtualnych. Aby uzyskać więcej informacji, zobacz [wybierz plany Premium jednordzeniowy](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Plan usługi App Service

Twoje aplikacje funkcji można również uruchomić na tym samym dedykowanych maszyn wirtualnych jako inne aplikacje usługi App Service (Basic, Standard, Premium i jednostek SKU izolowanej). Plany usługi App Service obsługuje systemu Linux.

Należy wziąć pod uwagę plan usługi App Service w następujących przypadkach:

* Masz istniejące niedostatecznie używanych maszyn wirtualnych, które zostały już uruchomione inne wystąpienia usługi App Service.
* Aby uruchomić aplikację funkcji w systemie Linux lub chcesz udostępnić obraz niestandardowy, na którym chcesz uruchamiać swoje funkcje.

Płaci się takie same dla aplikacji funkcji w planie usługi App Service tak jak w przypadku innych zasobów usługi App Service, takich jak aplikacje sieci web. Aby uzyskać szczegółowe informacje dotyczące sposobu działania planu usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service](../app-service/overview-hosting-plans.md). 

Plan usługi App Service można ręcznie skalować w poziomie, dodając kolejne wystąpienia maszyn wirtualnych lub włączyć Skalowanie automatyczne. Aby uzyskać więcej informacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Możesz również skalować w górę, wybierając inny plan usługi App Service. Aby uzyskać więcej informacji, zobacz [skalowanie aplikacji na platformie Azure](../app-service/web-sites-scale.md). 

Podczas uruchamiania funkcji JavaScript na plan usługi App Service, należy wybrać plan, który ma mniejszą liczbę procesorów wirtualnych. Aby uzyskać więcej informacji, zobacz [wybierz plany usługi App Service jednordzeniowy](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Zawsze włączone

Jeśli zostanie uruchomione na plan usługi App Service, należy włączyć **zawsze na** ustawienie, aby aplikacja funkcji zostanie uruchomiona poprawnie. Plan usługi App Service środowisko uruchomieniowe usługi functions zacznie bezczynności, po kilku minutach braku aktywności, dzięki czemu tylko wyzwalaczy HTTP "wzbudzania" funkcji. Zawsze na jest dostępna tylko na plan usługi App Service. W ramach planu zużycie platformy aktywuje automatycznie aplikacji funkcji.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>Co to jest Mój plan hostingu

Aby określić plan hostingu używane przez aplikację funkcji, zobacz **planu usługi App Service / warstwa cenowa** w **Przegląd** karty dla aplikacji funkcji w [witryny Azure portal](https://portal.azure.com). Dla planów usługi App Service wskazane jest także warstwy cenowej. 

![Wyświetl plan skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

Interfejs wiersza polecenia platformy Azure umożliwia również określić plan, w następujący sposób:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Gdy dane wyjściowe tego polecenia jest `dynamic`, aplikacja funkcji znajduje się w planie zużycie. Gdy dane wyjściowe tego polecenia jest `ElasticPremium`, aplikacja funkcji jest plan w warstwie Premium.  Wszystkie pozostałe wartości wskazują warstw planu usługi App Service.

Nawet w przypadku zawsze na włączone, limit czasu wykonywania poszczególnych funkcji jest kontrolowana przez `functionTimeout` w [host.json](functions-host-json.md#functiontimeout) pliku projektu.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

W dowolnym planie aplikacja funkcji wymaga konto usługi Azure Storage ogólnego, który obsługuje usługi Azure Blob, kolejki, pliki i Table storage. Jest to spowodowane funkcje dla operacji, takich jak Zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji polegać na usłudze Azure Storage, ale niektóre konta magazynu nie obsługują kolejek i tabel. Te konta, które obejmują kont usługi storage tylko do obiektów blob (w tym usługi premium storage) i kont magazynu ogólnego przeznaczenia z replikacją magazynu strefowo nadmiarowego, są filtrowane w poziomie z istniejących **konta magazynu** wybrane opcje, po utworzeniu aplikacji funkcji.

<!-- JH: Does using a Premium Storage account improve perf? -->

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak działają planów zużycia i premium

W przypadku użycia i plany premium kontroler skalowania automatycznie skaluje zasobów Procesora i pamięci przez dodanie dodatkowych wystąpień hosta funkcji, w oparciu o liczbę zdarzeń, które funkcje są wyzwalane na. Każde wystąpienie hosta funkcji w planie zużycie jest ograniczona do 1,5 GB pamięci i 1 procesora CPU.  Wystąpienie hosta jest aplikacja całą funkcję, co oznacza wszystkich funkcji w ramach funkcji zasób udziału dla aplikacji w ramach wystąpienia i skalowania, w tym samym czasie. Aplikacje funkcji, które współużytkują ten sam plan zużycie są skalowane niezależnie.  W planie premium rozmiar planu określają ilość dostępnej pamięci i procesora CPU dla wszystkich aplikacji w tym planie, w tym wystąpieniu.  

Pliki kodu funkcji są przechowywane w udziałach plików platformy Azure przez funkcję głównego konta magazynu. Po usunięciu głównego konta magazynu aplikacji funkcji w plikach kodu funkcji zostaną usunięte i nie można odzyskać.

> [!NOTE]
> Podczas korzystania z wyzwalacz obiektu blob w ramach planu zużycie, może istnieć maksymalnie 10 minut opóźnienia w przetwarzaniu nowe obiekty BLOB. To opóźnienie występuje, gdy aplikacja funkcji przeszedł bezczynności. Po uruchomieniu aplikacji funkcji, obiekty BLOB są przetwarzane od razu. Aby uniknąć tego opóźnienia zimnego startu, przy użyciu planu w warstwie Premium lub [wyzwalacza usługi Event Grid](functions-bindings-event-grid.md). Aby uzyskać więcej informacji, zobacz [artykuł odwołanie do obiektu blob wyzwalacza powiązania](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Skalowanie środowiska uruchomieniowego

Usługa Azure Functions używa składnik o nazwie *kontrolera skalowania* monitorować współczynnik zdarzeń i ustalenia, czy należy skalować w poziomie lub w. Kontroler skalowania korzysta z algorytmów heurystycznych dla każdego typu wyzwalacza. Na przykład podczas korzystania z wyzwalacza usługi Azure Queue storage, zostanie przeprowadzone skalowanie na podstawie długości kolejki i wiek najstarsze komunikatu w kolejce.

Jednostka skalowania jest aplikacja funkcji. Gdy aplikacja funkcji jest skalowana w poziomie, dodatkowe zasoby są przydzielane do uruchamiania wielu wystąpień hosta usługi Azure Functions. Z drugiej strony jak moc obliczeniowa, czy żądanie jest krótszy, kontroler skalowania usuwa wystąpień hosta funkcji. Liczba wystąpień jest ostatecznie skalować w dół od zera. gdy żadne funkcje nie są uruchomione w ramach aplikacji funkcji.

![Kontroler skalowania zdarzenia dotyczące monitorowania i tworzenia wystąpień](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Opis zachowania skalowania

Skalowanie może się różnić od szeregu czynników, a inaczej w zależności od języka wybranego i wyzwalaczy skalowania. Jednak istnieje kilka aspektów skalowania, które już dziś istnieją w systemie:

* Pojedynczą aplikację funkcji można skalować w górę do maksymalnie 200 wystąpień. Pojedyncze wystąpienie może przetwarzać więcej niż jednego komunikatu lub żądania naraz, więc nie ma ustawiony limit na liczbę współbieżnych wykonań.
* Wyzwalaczy HTTP nowe wystąpienia zostaną tylko przydzielone co najwyżej raz na 1 sekundę.
* Wyzwalaczy protokołu HTTP nowe wystąpienia zostaną tylko przydzielone co najwyżej raz na 30 sekund.

Różnymi wyzwalaczami mogą także mieć różne, w ograniczonym zakresie, a także udokumentowanego poniżej:

* [Centrum zdarzeń](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Najlepsze rozwiązania i wzorce skalowalne aplikacje

Istnieje wiele aspektów aplikacji funkcji, które mają wpływ na sposób również zostanie przeprowadzone skalowanie, łącznie z konfiguracją hosta, obciążenie środowiska uruchomieniowego i wydajności zasobów.  Aby uzyskać więcej informacji, zobacz [skalowalności w dalszej części artykułu zagadnienia dotyczące wydajności](functions-best-practices.md#scalability-best-practices). Należy także pamiętać o zachowanie połączenia miarę skalowania aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [sposób zarządzania połączeniami w usłudze Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Model rozliczania

Okres rozliczeniowy za plan zużycie został szczegółowo opisany na [Usługa Azure Functions, cennik]. Sposób użycia są agregowane na poziomie aplikacji funkcji i zlicza czas, który jest wykonywany kod funkcji. Jednostki do rozliczeń są następujące:

* **Użycie zasobów w gigabajtosekundach (GB-s)**. Obliczona jako rozmiar pamięci i czas wykonywania dla wszystkich funkcji w ramach aplikacji funkcji. 
* **Liczba wykonań**. Zliczane każdorazowo, gdy funkcja jest wykonywana w odpowiedzi na wyzwalacz zdarzenia.

Przydatne zapytania i uzyskać informacje na temat opis zawartości rachunku użycia można znaleźć [na często zadawane pytania rozliczeń](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Usługa Azure Functions, cennik]: https://azure.microsoft.com/pricing/details/functions
