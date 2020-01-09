---
title: Opcje sieci Azure Functions
description: Przegląd wszystkich opcji sieciowych dostępnych w Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 1a9c058e590e5df9ab9ec82d900e22f7154d00a0
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561936"
---
# <a name="azure-functions-networking-options"></a>Opcje sieci Azure Functions

W tym artykule opisano funkcje sieciowe dostępne w obszarze Opcje hostingu dla Azure Functions. Wszystkie poniższe opcje sieciowe zapewniają pewne możliwości dostępu do zasobów bez używania adresów z obsługą sieci Internet lub w celu ograniczenia dostępu do Internetu do aplikacji funkcji.

Modele hostingu mają różne poziomy izolacji sieci. Wybranie odpowiedniej opcji pomoże Ci spełnić wymagania dotyczące izolacji sieci.

Aplikacje funkcji można hostować na kilka sposobów:

* Istnieje zestaw opcji planu, które są uruchamiane w ramach infrastruktury z wieloma dzierżawcami z różnymi poziomami łączności sieci wirtualnej i skalowania:
    * [Plan zużycia](functions-scale.md#consumption-plan), który jest skalowany dynamicznie w odpowiedzi na obciążenie i oferuje minimalne opcje izolacji sieci.
    * [Plan Premium](functions-scale.md#premium-plan), który również skaluje się dynamicznie, jednocześnie oferując bardziej kompleksową izolację sieci.
    * [Plan App Service](functions-scale.md#app-service-plan)platformy Azure, który działa w stałej skali i oferuje podobną izolację sieciową jako plan Premium.
* Funkcje można uruchamiać w [App Service Environment](../app-service/environment/intro.md). Ta metoda wdraża funkcję w sieci wirtualnej i oferuje pełną kontrolę sieci i izolację.

## <a name="matrix-of-networking-features"></a>Macierz funkcji sieciowych

|                |[Plan zużycia](functions-scale.md#consumption-plan)|[Plan Premium](functions-scale.md#premium-plan)|[Plan usługi App Service](functions-scale.md#app-service-plan)|[Środowisko usługi App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Ograniczenia przychodzącego adresu IP & dostęp do lokacji prywatnej](#inbound-ip-restrictions)|✅tak|✅tak|✅tak|✅tak|
|[Integracja sieci wirtualnej](#virtual-network-integration)|❌nie|✅tak (regionalne)|✅tak (regionalne i brama)|✅tak|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](#virtual-network-triggers-non-http)|❌nie| ✅tak |✅tak|✅tak|
|[Połączenia hybrydowe](#hybrid-connections) (tylko system Windows)|❌nie|✅tak|✅tak|✅tak|
|[Ograniczenia wychodzącego adresu IP](#outbound-ip-restrictions)|❌nie| ❌nie|❌nie|✅tak|

## <a name="inbound-ip-restrictions"></a>Ograniczenia przychodzącego adresu IP

Za pomocą ograniczeń adresów IP można zdefiniować uporządkowaną według priorytetu listę adresów IP, które są dozwolone lub odrzucane przez dostęp do aplikacji. Lista może zawierać adresy IPv4 i IPv6. W przypadku co najmniej jednego wpisu na końcu listy występuje niejawne "odmowa wszystkich". Ograniczenia adresów IP działają ze wszystkimi opcjami hostingu funkcji.

> [!NOTE]
> W przypadku ograniczeń sieci można używać edytora portalu tylko z poziomu sieci wirtualnej lub po umieszczeniu adresu IP komputera, którego używasz, aby uzyskać dostęp do Azure Portal na liście bezpiecznych adresatów. Można jednak nadal uzyskiwać dostęp do dowolnych funkcji na karcie **funkcje platformy** z dowolnego komputera.

Aby dowiedzieć się więcej, zobacz [Azure App Service ograniczenia dostępu statycznego](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Dostęp do witryn prywatnych

Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak sieć wirtualna platformy Azure.

* Dostęp do lokacji prywatnej jest dostępny w planach [Premium](./functions-premium-plan.md), [zużycia](functions-scale.md#consumption-plan)i [App Service](functions-scale.md#app-service-plan) w przypadku skonfigurowania punktów końcowych usługi.
    * Punkty końcowe usługi można skonfigurować dla poszczególnych aplikacji w obszarze **funkcje platformy** > **sieci** > **skonfigurować ograniczenia dostępu** > **Dodaj regułę**. Sieci wirtualne można teraz wybrać jako typ reguły.
    * Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Należy pamiętać, że dzięki punktom końcowym usługi funkcja nadal ma pełny dostęp wychodzący do Internetu, nawet z skonfigurowaną integracją sieci wirtualnej.
* Dostęp do lokacji prywatnej jest również dostępny w ramach App Service Environment, który jest skonfigurowany przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB). Aby uzyskać więcej informacji, zobacz [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia z App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integracja sieci wirtualnej

Integracja z siecią wirtualną umożliwia aplikacji funkcji dostęp do zasobów w sieci wirtualnej. Ta funkcja jest dostępna zarówno w planie Premium, jak i w planie App Service. Jeśli aplikacja znajduje się w App Service Environment, jest już w sieci wirtualnej i nie wymaga integracji z siecią wirtualną w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej.

Integracja z siecią wirtualną umożliwia dostęp z aplikacji do baz danych i usług sieci Web działających w sieci wirtualnej. Dzięki integracji z siecią wirtualną nie trzeba ujawniać publicznego punktu końcowego dla aplikacji na maszynie wirtualnej. Zamiast tego można używać prywatnych adresów bez obsługi sieci Internet.

Istnieją dwie formy integracji z siecią wirtualną:

+ **Integracja regionalnej sieci wirtualnej (wersja zapoznawcza)** : umożliwia integrację z sieciami wirtualnymi w tym samym regionie. Ten typ integracji wymaga podsieci w sieci wirtualnej w tym samym regionie. Ta funkcja jest nadal dostępna w wersji zapoznawczej, ale jest obsługiwana w przypadku aplikacji funkcji działających w systemie Windows z zastrzeżeniami opisanymi po następującej tabeli problemu/rozwiązania.
+ **Integracja sieci wirtualnej wymagana przez bramę**: umożliwia integrację z sieciami wirtualnymi w regionach zdalnych lub z klasycznymi sieciami wirtualnymi. Ten typ integracji wymaga wdrożenia bramy sieci wirtualnej w sieci wirtualnej. Jest to funkcja oparta na sieci VPN typu punkt-lokacja, która jest obsługiwana tylko w przypadku aplikacji funkcji działających w systemie Windows.

Aplikacja może jednocześnie korzystać tylko z jednego typu funkcji integracji sieci wirtualnej. Chociaż oba są przydatne w wielu scenariuszach, Poniższa tabela wskazuje, gdzie należy używać każdego z nich:

| Problem  | Rozwiązanie |
|----------|----------|
| Chcesz uzyskać dostęp do adresu RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) w tym samym regionie | Integracja regionalnej sieci wirtualnej |
| Chcesz uzyskać dostęp do zasobów w klasycznej sieci wirtualnej lub w sieci wirtualnej w innym regionie | Integracja sieci wirtualnej wymagana przez bramę |
| Chcesz uzyskać dostęp do punktów końcowych RFC 1918 na platformie Azure ExpressRoute | Integracja regionalnej sieci wirtualnej |
| Chcesz uzyskać dostęp do zasobów między punktami końcowymi usługi | Integracja regionalnej sieci wirtualnej |

Żadna z funkcji nie pozwala na dostęp do adresów innych niż RFC 1918 w ramach ExpressRoute. W tym celu należy obecnie użyć App Service Environment.

Użycie integracji regionalnej sieci wirtualnej nie powoduje połączenia sieci wirtualnej z lokalnymi punktami końcowymi ani konfigurowania punktów końcowych usługi. Jest to oddzielna konfiguracja sieciowa. Integracja regionalnej sieci wirtualnej umożliwia aplikacji wykonywanie wywołań w ramach tych typów połączeń.

Niezależnie od używanej wersji integracja z siecią wirtualną daje aplikacji funkcji dostęp do zasobów w sieci wirtualnej, ale nie udziela dostępu do aplikacji funkcji z sieci wirtualnej. Dostęp do lokacji prywatnej oznacza, że aplikacja jest dostępna tylko z sieci prywatnej, takiej jak sieć wirtualna platformy Azure. Integracja z siecią wirtualną służy tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej.

Funkcja integracji sieci wirtualnej:

* Wymaga planu App Service w warstwie Standardowa, Premium lub PremiumV2
* Obsługuje protokoły TCP i UDP
* Współdziałanie z aplikacjami App Service i aplikacjami funkcji

Istnieje kilka rzeczy, które nie są obsługiwane przez integrację sieci wirtualnej, w tym:

* Instalowanie dysku
* Integracja z usługą Active Directory
* NetBIOS

Integracja sieci wirtualnej w programie Azure Functions używa infrastruktury udostępnionej z App Service aplikacjami sieci Web. Aby dowiedzieć się więcej o dwóch typach integracji z siecią wirtualną, zobacz:

* [Integracja regionalnej sieci wirtualnej](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integracja sieci wirtualnej wymagana przez bramę](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Aby dowiedzieć się więcej o korzystaniu z integracji z siecią wirtualną, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną platformy Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Łączenie z zabezpieczonymi zasobami punktu końcowego usługi

> [!NOTE]
> Na razie może upłynąć do 12 godzin, zanim nowe punkty końcowe usługi staną się dostępne w aplikacji funkcji po skonfigurowaniu ograniczeń dostępu dla zasobu podrzędnego. W tym czasie zasób będzie całkowicie niedostępny dla aplikacji.

Aby zapewnić wyższy poziom zabezpieczeń, można ograniczyć liczbę usług platformy Azure do sieci wirtualnej za pomocą punktów końcowych usługi. Następnie należy zintegrować aplikację funkcji z tą siecią wirtualną, aby uzyskać dostęp do zasobu. Ta konfiguracja jest obsługiwana we wszystkich planach, które obsługują integrację z siecią wirtualną.

[Dowiedz się więcej o punktach końcowych usługi sieci wirtualnej.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Ograniczanie konta magazynu do sieci wirtualnej

Podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel. Na tym koncie nie można obecnie używać żadnych ograniczeń sieci wirtualnej. W przypadku skonfigurowania punktu końcowego usługi sieci wirtualnej na koncie magazynu używanym przez aplikację funkcji, która spowoduje przerwanie działania aplikacji.

[Dowiedz się więcej o wymaganiach dotyczących kont magazynu.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Korzystanie z odwołań Key Vault 

Odwołania Key Vault umożliwiają używanie wpisów tajnych z Azure Key Vault w aplikacji Azure Functions bez konieczności wprowadzania żadnych zmian w kodzie. Azure Key Vault to usługa zapewniająca scentralizowane zarządzanie kluczami tajnymi z pełną kontrolą nad zasadami dostępu i historią inspekcji.

Obecnie [Key Vault odwołania](../app-service/app-service-key-vault-references.md) nie będą działały, jeśli key Vault jest zabezpieczony za pomocą punktów końcowych usługi. Aby nawiązać połączenie z Key Vault przy użyciu integracji z siecią wirtualną, należy wywołać Magazyn kluczy w kodzie aplikacji.

## <a name="virtual-network-triggers-non-http"></a>Wyzwalacze sieci wirtualnej (bez protokołu HTTP)

Obecnie można używać funkcji wyzwalacza innego niż HTTP z poziomu sieci wirtualnej na jeden z dwóch sposobów: 
+ Uruchom aplikację funkcji w planie Premium i Włącz obsługę wyzwalacza sieci wirtualnej.
+ Uruchom aplikację funkcji w planie App Service lub App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plan Premium z wyzwalaczami sieci wirtualnej

W przypadku korzystania z planu Premium można połączyć funkcje wyzwalacza inne niż HTTP z usługami uruchomionymi w sieci wirtualnej. W tym celu należy włączyć obsługę wyzwalacza sieci wirtualnej dla aplikacji funkcji. Ustawienie **obsługi wyzwalacza sieci wirtualnej** znajduje się w [Azure Portal](https://portal.azure.com) w obszarze **Ustawienia aplikacji funkcji**.

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

Możesz również włączyć wyzwalacze sieci wirtualnej, korzystając z następującego polecenia interfejsu CLI platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.functionsRuntimeScaleMonitoringEnabled=1
```

Wyzwalacze sieci wirtualnej są obsługiwane w wersji 2. x i powyżej środowiska uruchomieniowego funkcji. Obsługiwane są następujące typy wyzwalaczy inne niż HTTP.

| Rozszerzenie | Minimalna wersja |
|-----------|---------| 
|[Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 lub nowszy |
|[Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 lub nowszy|
|[Microsoft. Azure. WebJobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 lub nowszy|
|[Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 lub nowszy|
|[Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 lub nowszy|

> [!IMPORTANT]
> Podczas włączania obsługi wyzwalacza sieci wirtualnej tylko typy wyzwalaczy powyżej są skalowane dynamicznie z aplikacją. Nadal można korzystać z wyzwalaczy, których nie wymieniono powyżej, ale nie są one skalowane poza liczbą wystąpień sprzed obgrzanych. Zobacz [wyzwalacze i powiązania](./functions-triggers-bindings.md#supported-bindings) , aby uzyskać pełną listę wyzwalaczy.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service planowanie i App Service Environment przy użyciu wyzwalaczy sieci wirtualnej

Gdy aplikacja funkcji działa w planie App Service lub App Service Environment, można użyć funkcji wyzwalacza innego niż HTTP. Aby funkcje zostały wyzwolone prawidłowo, musisz mieć połączenie z siecią wirtualną z dostępem do zasobu zdefiniowanego w połączeniu wyzwalacza. 

Załóżmy na przykład, że chcesz skonfigurować Azure Cosmos DB, aby akceptować tylko ruch z sieci wirtualnej. W takim przypadku należy wdrożyć aplikację funkcji w planie App Service, która zapewnia integrację sieci wirtualnej z tą siecią wirtualną. Umożliwia to wyzwolenie funkcji przez ten zasób Azure Cosmos DB. 

## <a name="hybrid-connections"></a>Hybrydowe

[Połączenia hybrydowe](../service-bus-relay/relay-hybrid-connections-protocol.md) to funkcja Azure Relay, z której można korzystać w celu uzyskania dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie można używać go do uzyskiwania dostępu do aplikacji. Połączenia hybrydowe jest dostępny dla funkcji działających w systemie Windows we wszystkich, ale w planie zużycia.

Jak w Azure Functions każde połączenie hybrydowe jest skorelowane z pojedynczym hostem TCP i kombinacją portów. Oznacza to, że punkt końcowy połączenia hybrydowego może znajdować się w dowolnym systemie operacyjnym i dowolnej aplikacji, o ile jest uzyskiwany dostęp do portu nasłuchiwania protokołu TCP. Funkcja Połączenia hybrydowe nie wie ani nie ma informacji o tym, czym jest lub do czego masz dostęp. Zapewnia dostęp do sieci.

Aby dowiedzieć się więcej, zapoznaj się z dokumentacją dotyczącą [App Service połączenia hybrydowe](../app-service/app-service-hybrid-connections.md). Te same kroki konfiguracji obsługują Azure Functions.

>[!IMPORTANT]
> Połączenia hybrydowe jest obsługiwana tylko w planach systemu Windows. System Linux nie jest obsługiwany

## <a name="outbound-ip-restrictions"></a>Ograniczenia wychodzącego adresu IP

Ograniczenia wychodzącego adresu IP są dostępne tylko dla funkcji wdrożonych w App Service Environment. Można skonfigurować ograniczenia ruchu wychodzącego dla sieci wirtualnej, w której wdrożono App Service Environment.

Po zintegrowaniu aplikacji funkcji w planie Premium lub planie App Service z siecią wirtualną aplikacja nadal może nawiązywać połączenia wychodzące do Internetu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sieci i Azure Functions:

* [Postępuj zgodnie z samouczkiem dotyczącym rozpoczynania pracy z integracją z siecią wirtualną](./functions-create-vnet.md)
* [Przeczytaj informacje o usłudze Functions — często zadawane pytania](./functions-networking-faq.md)
* [Dowiedz się więcej o integracji z siecią wirtualną za pomocą App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej o sieciach wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączanie większej liczby funkcji sieciowych i kontroli w środowiskach App Service](../app-service/environment/intro.md)
* [Nawiąż połączenie z lokalnymi zasobami bez zmian w zaporze przy użyciu Połączenia hybrydowe](../app-service/app-service-hybrid-connections.md)
