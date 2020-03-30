---
title: Opcje sieciowe usługi Azure Functions
description: Omówienie wszystkich opcji sieci dostępnych w usłudze Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: d8c3357325eadefec7bb97faba5d600e9c6793a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276714"
---
# <a name="azure-functions-networking-options"></a>Opcje sieciowe usługi Azure Functions

W tym artykule opisano funkcje sieciowe dostępne w opcjach hostingu dla usługi Azure Functions. Wszystkie następujące opcje sieci umożliwiają dostęp do zasobów bez użycia adresów internetowych lub ograniczenie dostępu do Internetu do aplikacji funkcyjnej.

Modele hostingu mają różne poziomy izolacji sieci dostępne. Wybór odpowiedniego pomoże Ci spełnić wymagania dotyczące izolacji sieci.

Aplikacje funkcyjne można hostować na kilka sposobów:

* Istnieje zestaw opcji planu, które działają w infrastrukturze wielodostępnej, z różnymi poziomami łączności sieci wirtualnej i opcji skalowania:
    * [Plan zużycie](functions-scale.md#consumption-plan), który skaluje się dynamicznie w odpowiedzi na obciążenie i oferuje minimalne opcje izolacji sieci.
    * [Plan Premium](functions-scale.md#premium-plan), który również skaluje się dynamicznie, oferując jednocześnie bardziej kompleksową izolację sieci.
    * Plan [usługi azure app service](functions-scale.md#app-service-plan), który działa w stałej skali i oferuje podobną izolację sieci jak plan Premium.
* Funkcje można uruchamiać w [środowisku usługi aplikacji](../app-service/environment/intro.md). Ta metoda wdraża funkcję w sieci wirtualnej i oferuje pełną kontrolę sieci i izolacji.

## <a name="matrix-of-networking-features"></a>Matryca funkcji sieciowych

|                |[Plan Zużycie](functions-scale.md#consumption-plan)|[Plan Premium](functions-scale.md#premium-plan)|[Plan usługi aplikacji](functions-scale.md#app-service-plan)|[Środowisko usługi aplikacji](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Ograniczenia przychodzącego adresu IP & dostęp do witryn prywatnych](#inbound-ip-restrictions)|✅Tak|✅Tak|✅Tak|✅Tak|
|[Integracja sieci wirtualnej](#virtual-network-integration)|❌№|✅Tak (regionalne)|✅Tak (regionalne i bramowe)|✅Tak|
|[Wyzwalacze sieci wirtualnej (inne niż HTTP)](#virtual-network-triggers-non-http)|❌№| ✅Tak |✅Tak|✅Tak|
|[Połączenia hybrydowe](#hybrid-connections) (tylko windows)|❌№|✅Tak|✅Tak|✅Tak|
|[Wychodzące ograniczenia adresów IP](#outbound-ip-restrictions)|❌№| ✅Tak|✅Tak|✅Tak|

## <a name="inbound-ip-restrictions"></a>Ograniczenia przychodzącego adresu IP

Ograniczenia adresów IP służą do definiowania uporządkowanej priorytetowo listy adresów IP, które są dozwolone lub mają odmowę dostępu do aplikacji. Lista może zawierać adresy IPv4 i IPv6. Gdy istnieje jeden lub więcej wpisów, niejawne "odmówić wszystkich" istnieje na końcu listy. Ograniczenia IP działają ze wszystkimi opcjami hostingu funkcji.

> [!NOTE]
> Z ograniczeniami sieciowymi można używać edytora portalu tylko z poziomu sieci wirtualnej lub po umieszczeniu adresu IP komputera, którego używasz, aby uzyskać dostęp do witryny Azure portal na liście Bezpiecznych adresatów. Jednak nadal można uzyskać dostęp do wszystkich funkcji na karcie **Funkcje platformy** z dowolnego komputera.

Aby dowiedzieć się więcej, zobacz [Ograniczenia dostępu statycznego usługi Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Dostęp do witryn prywatnych

Dostęp do witryny prywatnej odnosi się do udostępnienia aplikacji tylko z sieci prywatnej, takiej jak sieć wirtualna platformy Azure.

* Dostęp do witryny prywatnej jest dostępny w planach [Premium,](./functions-premium-plan.md) [Consumption](functions-scale.md#consumption-plan)i [App Service,](functions-scale.md#app-service-plan) gdy skonfigurowane są punkty końcowe usługi.
    * Punkty końcowe usługi można skonfigurować na podstawie aplikacji w obszarze **Funkcje** > platformy**Konfiguracja** > sieci**Ograniczenia** > dostępu**Dodaj regułę**. Sieci wirtualne można teraz wybrać jako typ reguły.
    * Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Należy pamiętać, że w punktach końcowych usługi funkcja nadal ma pełny dostęp wychodzący do Internetu, nawet przy skonfigurowaniu integracji sieci wirtualnej.
* Dostęp do witryny prywatnej jest również dostępny w środowisku usługi app service skonfigurowany z wewnętrznym modułem równoważenia obciążenia (ILB). Aby uzyskać więcej informacji, zobacz [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi aplikacji](../app-service/environment/create-ilb-ase.md).

Aby dowiedzieć się, jak skonfigurować dostęp do witryny prywatnej, zobacz [Ustanawianie dostępu do witryny prywatnej usług Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integracja sieci wirtualnej

Integracja z siecią wirtualną umożliwia aplikacji funkcji dostęp do zasobów wewnątrz sieci wirtualnej. Usługa Azure Functions obsługuje dwa rodzaje integracji z siecią wirtualną:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integracja sieci wirtualnej w usłudze Azure Functions korzysta z infrastruktury udostępnionej z aplikacjami sieci Web usługi App Service. Aby dowiedzieć się więcej o dwóch typach integracji sieci wirtualnej, zobacz:

* [Integracja regionalnej sieci wirtualnej](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Wymagana integracja sieci wirtualnej przez bramę](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Aby dowiedzieć się, jak skonfigurować integrację sieci [wirtualnej, zobacz Integrowanie aplikacji funkcji z siecią wirtualną platformy Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regionalna integracja sieci wirtualnej

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>Łączenie się z zasobami zabezpieczonymi punktu końcowego usługi

Aby zapewnić wyższy poziom zabezpieczeń, można ograniczyć liczbę usług platformy Azure do sieci wirtualnej przy użyciu punktów końcowych usługi. Następnie należy zintegrować aplikację funkcji z tej sieci wirtualnej, aby uzyskać dostęp do zasobu. Ta konfiguracja jest obsługiwana we wszystkich planach, które obsługują integrację sieci wirtualnej.

[Dowiedz się więcej o punktach końcowych usługi sieci wirtualnej.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>Ograniczanie konta magazynu do sieci wirtualnej

Podczas tworzenia aplikacji funkcji, należy utworzyć lub łącze do ogólnego przeznaczenia konta usługi Azure Storage, który obsługuje blob, kolejki i magazynu tabel. Obecnie nie można używać żadnych ograniczeń sieci wirtualnej na tym koncie. Jeśli skonfigurujesz punkt końcowy usługi sieci wirtualnej na koncie magazynu, którego używasz dla aplikacji funkcji, spowoduje to przerwanie aplikacji.

[Dowiedz się więcej o wymaganiach dotyczących konta magazynu.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>Korzystanie z odwołań do magazynu kluczy 

Odwołania do usługi Key Vault umożliwiają używanie wpisów tajnych z usługi Azure Key Vault w aplikacji usługi Azure Functions bez konieczności zmiany kodu. Usługa Azure Key Vault to usługa zapewniająca scentralizowane zarządzanie wpisami tajnymi, z pełną kontrolą nad zasadami dostępu i historią inspekcji.

Obecnie [odwołania do usługi Key Vault](../app-service/app-service-key-vault-references.md) nie będą działać, jeśli magazyn kluczy jest zabezpieczony punktami końcowymi usługi. Aby połączyć się z magazynem kluczy przy użyciu integracji sieci wirtualnej, należy wywołać magazyn kluczy w kodzie aplikacji.

## <a name="virtual-network-triggers-non-http"></a>Wyzwalacze sieci wirtualnej (inne niż HTTP)

Obecnie można używać funkcji wyzwalania innych niż HTTP z sieci wirtualnej na jeden z dwóch sposobów: 
+ Uruchom aplikację funkcji w planie Premium i włącz obsługę wyzwalaczy sieci wirtualnej.
+ Uruchom aplikację funkcji w planie usługi app service lub środowisku usługi aplikacji.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plan premium z wyzwalaczami sieci wirtualnej

Podczas uruchamiania w planie Premium można połączyć funkcje wyzwalania inne niż HTTP z usługami działającymi w sieci wirtualnej. Aby to zrobić, należy włączyć obsługę wyzwalaczy sieci wirtualnej dla aplikacji funkcji. Ustawienie **obsługi wyzwalacza sieci wirtualnej** znajduje się w [witrynie Azure portal](https://portal.azure.com) w obszarze Ustawienia aplikacji **Funkcji**.

![Przełącznik sieci wirtualnej](media/functions-networking-options/virtual-network-trigger-toggle.png)

Można również włączyć wyzwalacze sieci wirtualnej przy użyciu następującego polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Wyzwalacze sieci wirtualnej są obsługiwane w wersji 2.x i powyżej środowiska wykonawczego Functions. Obsługiwane są następujące typy wyzwalaczy innych niż HTTP.

| Wewnętrzny | Minimalna wersja |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 lub więcej |
|[Witryna Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 lub więcej|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 lub więcej|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 lub więcej|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 lub więcej|

> [!IMPORTANT]
> Podczas włączania obsługi wyzwalaczy sieci wirtualnej tylko typy wyzwalaczy powyżej skali dynamicznie z aplikacją. Nadal można używać wyzwalaczy niewymienionych powyżej, jednak nie są one skalowane poza ich wstępnie rozgrzaną liczbą wystąpień. Zobacz [wyzwalacze i powiązania](./functions-triggers-bindings.md#supported-bindings) dla pełnej listy wyzwalaczy.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plan usługi app service i środowisko usługi aplikacji z wyzwalaczami sieci wirtualnej

Gdy aplikacja funkcji działa w planie usługi aplikacji lub środowisku usługi aplikacji, można użyć funkcji wyzwalania innych niż HTTP. Aby funkcje zostały poprawnie wyzwolone, musisz być połączony z siecią wirtualną z dostępem do zasobu zdefiniowanego w połączeniu wyzwalającym. 

Załóżmy na przykład, że chcesz skonfigurować usługę Azure Cosmos DB do akceptowania ruchu tylko z sieci wirtualnej. W takim przypadku należy wdrożyć aplikację funkcji w planie usługi App Service, który zapewnia integrację sieci wirtualnej z tej sieci wirtualnej. Dzięki temu funkcja ma być wyzwalane przez ten zasób usługi Azure Cosmos DB. 

## <a name="hybrid-connections"></a>Połączenia hybrydowe

[Połączenia hybrydowe](../service-bus-relay/relay-hybrid-connections-protocol.md) to funkcja usługi Azure Relay, której można używać do uzyskiwania dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie można go użyć do uzyskania dostępu do aplikacji. Połączenia hybrydowe są dostępne dla funkcji działających w systemie Windows we wszystkich oprócz planu zużycia.

Zgodnie z usługą Azure Functions każde połączenie hybrydowe jest skorelowane z kombinacją pojedynczego hosta i portu TCP. Oznacza to, że punkt końcowy połączenia hybrydowego może znajdować się w dowolnym systemie operacyjnym i dowolnej aplikacji, o ile uzyskujesz dostęp do portu nasłuchującego TCP. Funkcja Połączenia hybrydowe nie wie ani nie dba o to, czym jest protokół aplikacji ani do czego uzyskujesz dostęp. Po prostu zapewnia dostęp do sieci.

Aby dowiedzieć się więcej, zobacz [dokumentację usługi app service dla połączeń hybrydowych](../app-service/app-service-hybrid-connections.md). Te same kroki konfiguracji obsługują usługi Azure Functions.

>[!IMPORTANT]
> Połączenia hybrydowe są obsługiwane tylko w planach systemu Windows. Linux nie jest obsługiwany

## <a name="outbound-ip-restrictions"></a>Wychodzące ograniczenia adresów IP

Wychodzące ograniczenia ip są dostępne w planie Premium, planie usługi app service lub środowisku usługi aplikacji. Ograniczenia ruchu wychodzącego można skonfigurować dla sieci wirtualnej, w której jest wdrażane środowisko usługi App Service.

Po zintegrowaniu aplikacji funkcji w planie Premium lub planie usługi app service z siecią wirtualną, aplikacja może domyślnie nawiązywać połączenia wychodzące z Internetem. Dodając ustawienie `WEBSITE_VNET_ROUTE_ALL=1`aplikacji, można wymusić cały ruch wychodzący do wysłania do sieci wirtualnej, gdzie reguły sieciowej grupy zabezpieczeń mogą być używane do ograniczania ruchu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sieci i usłudze Azure Functions:

* [Postępuj zgodnie z samouczkiem na temat rozpoczynania integracji z siecią wirtualną](./functions-create-vnet.md)
* [Przeczytaj często zadawane pytania dotyczące funkcji sieciowych](./functions-networking-faq.md)
* [Dowiedz się więcej o integracji sieci wirtualnej z usługą/funkcjami aplikacji](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej o sieciach wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włącz więcej funkcji sieciowych i kontroluj za pomocą środowisk usługi app service](../app-service/environment/intro.md)
* [Łączenie się z poszczególnymi zasobami lokalnymi bez zmian zapory przy użyciu połączeń hybrydowych](../app-service/app-service-hybrid-connections.md)
