---
title: Przychodzące/wychodzące adresy IP
description: Dowiedz się, jak przychodzące i wychodzące adresy IP są używane w usłudze Azure App Service, gdy się zmieniają i jak znaleźć adresy aplikacji.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279210"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Przychodzące i wychodzące adresy IP w usłudze Azure App Service

[Usługa Azure App Service](overview.md) jest usługą wielodostępną, z wyjątkiem [środowisk usługi app service.](environment/intro.md) Aplikacje, które nie znajdują się w środowisku usługi app service (nie w [warstwie Izolowane)](https://azure.microsoft.com/pricing/details/app-service/)współużytkują infrastrukturę sieciową z innymi aplikacjami. W rezultacie przychodzące i wychodzące adresy IP aplikacji mogą być różne, a nawet mogą ulec zmianie w pewnych sytuacjach. 

[Środowiska usługi app service](environment/intro.md) używają dedykowanej infrastruktury sieciowej, dzięki czemu aplikacje działające w środowisku usługi app service otrzymują statyczne, dedykowane adresy IP zarówno dla połączeń przychodzących, jak i wychodzących.

## <a name="when-inbound-ip-changes"></a>Po zmianie przychodzącego adresu IP

Niezależnie od liczby wystąpień skalowane w poziomie każda aplikacja ma jeden przychodzący adres IP. Przychodzący adres IP może ulec zmianie podczas wykonywania jednej z następujących czynności:

- Usuń aplikację i ponownie stwórz ją w innej grupie zasobów.
- Usuń ostatnią aplikację w kombinacji grupy _zasobów i_ regionu i ponownie ją utworzyć.
- Usuwanie istniejącego powiązania SSL, na przykład podczas odnawiania certyfikatu (zobacz [Odnawianie certyfikatu).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Znajdowanie przychodzącego adresu IP

Wystarczy uruchomić następujące polecenie w terminalu lokalnym:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Uzyskaj statyczny przychodzący adres IP

Czasami możesz chcieć dedykowanego, statycznego adresu IP dla aplikacji. Aby uzyskać statyczny przychodzący adres IP, należy skonfigurować [powiązanie SSL oparte na protoke.](configure-ssl-bindings.md#secure-a-custom-domain) Jeśli faktycznie nie potrzebujesz funkcji SSL do zabezpieczenia aplikacji, możesz nawet przekazać certyfikat z podpisem własnym dla tego powiązania. W powiązaniu SSL opartym na protoke ip certyfikat jest powiązany z samym adresem IP, więc usługa App Service udostępnia statyczny adres IP, aby to się stało. 

## <a name="when-outbound-ips-change"></a>Gdy wychodzące ip zmiany

Niezależnie od liczby wystąpień skalowane w poziomie, każda aplikacja ma określoną liczbę wychodzących adresów IP w danym momencie. Każde połączenie wychodzące z aplikacji Usługi App Service, takie jak baza danych zaplecza, używa jednego z wychodzących adresów IP jako źródłowego adresu IP. Nie można wcześniej wiedzieć, który adres IP danego wystąpienia aplikacji będzie używać do nawiązywania połączenia wychodzącego, więc usługa zaplecza musi otworzyć zaporę na wszystkie wychodzące adresy IP aplikacji.

Zestaw wychodzących adresów IP dla aplikacji zmienia się podczas skalowania aplikacji między niższymi warstwami **(Podstawowa,** **Standardowa**i **Premium)** i warstwą **Premium V2.**

Zestaw wszystkich możliwych wychodzących adresów IP, których aplikacja może używać, niezależnie od warstw `possibleOutboundIpAddresses` cenowych, wyszukując właściwość lub w polu **Dodatkowe wychodzące adresy IP** w bloku **Właściwości** w witrynie Azure portal. Zobacz [Znajdowanie wychodzących usług IP](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Znajdowanie wychodzących usług IP

Aby znaleźć wychodzące adresy IP aktualnie używane przez aplikację w witrynie Azure portal, kliknij **pozycję Właściwości** w nawigacji po lewej stronie aplikacji. Są one wyświetlane w polu **Wychodzące adresy IP.**

Te same informacje można znaleźć, uruchamiając następujące polecenie w [usłudze Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Aby znaleźć _wszystkie_ możliwe wychodzące adresy IP aplikacji, niezależnie od warstw cenowych, kliknij **pozycję Właściwości** w nawigacji po lewej stronie aplikacji. Są one wymienione w polu **Dodatkowe wychodzące adresy IP.**

Te same informacje można znaleźć, uruchamiając następujące polecenie w [usłudze Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak ograniczyć ruch przychodzący według źródłowych adresów IP.

> [!div class="nextstepaction"]
> [Statyczne ograniczenia adresów IP](app-service-ip-restrictions.md)
