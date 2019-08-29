---
title: Przychodzące/wychodzące adresy IP — Azure App Service | Microsoft Docs
description: Opisuje, w jaki sposób są używane przychodzące i wychodzące adresy IP w App Service oraz jak znaleźć informacje o nich dla aplikacji.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2508090fa8831c8fefb0e710c28e512ec0c94c6e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074153"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Przychodzące i wychodzące adresy IP w Azure App Service

[Azure App Service](overview.md) to usługa z wieloma dzierżawami, z wyjątkiem [środowisk App Service](environment/intro.md). Aplikacje, które nie znajdują się w środowisku App Service (nie w [warstwie izolowanej](https://azure.microsoft.com/pricing/details/app-service/)), współużytkują infrastrukturę sieci z innymi aplikacjami. W związku z tym przychodzące i wychodzące adresy IP aplikacji mogą być różne i nawet w pewnych sytuacjach mogą się zmieniać. 

[Środowiska App Service](environment/intro.md) używają dedykowanych infrastruktur sieciowych, więc aplikacje działające w środowisku App Service otrzymują statyczne, dedykowane adresy IP zarówno dla połączeń przychodzących, jak i wychodzących.

## <a name="when-inbound-ip-changes"></a>Gdy przychodzące zmiany adresów IP

Niezależnie od liczby wystąpień skalowanych w poziomie, każda aplikacja ma jeden adres IP ruchu przychodzącego. Adres IP ruchu przychodzącego może ulec zmianie podczas wykonywania jednej z następujących czynności:

- Usuń aplikację i utwórz ją ponownie w innej grupie zasobów.
- Usuń ostatnią aplikację w kombinacji grupy zasobów _i_ regionu i utwórz ją ponownie.
- Usuń istniejące powiązanie SSL, takie jak podczas odnawiania certyfikatu (zobacz [odnowienie certyfikatów](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="find-the-inbound-ip"></a>Znajdowanie przychodzącego adresu IP

Po prostu uruchom następujące polecenie w lokalnym terminalu:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Pobierz statyczny adres IP dla ruchu przychodzącego

Czasami może być potrzebny dedykowany, statyczny adres IP dla aplikacji. Aby uzyskać statyczny adres IP dla ruchu przychodzącego, należy skonfigurować [powiązanie protokołu SSL opartego na](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain)protokole IP. Jeśli w rzeczywistości nie potrzebujesz funkcji SSL do zabezpieczenia aplikacji, możesz nawet przekazać certyfikat z podpisem własnym dla tego powiązania. W przypadku wiązania SSL opartego na protokole IP certyfikat jest powiązany z samym adresem IP, dlatego App Service udostępnia statyczny adres IP w celu jego wystąpienia. 

## <a name="when-outbound-ips-change"></a>Po zmianie wychodzących adresów IP

Niezależnie od liczby wystąpień skalowanych w poziomie, każda aplikacja ma określoną liczbę wychodzących adresów IP w danym momencie. Każde połączenie wychodzące z aplikacji App Service, na przykład do bazy danych zaplecza, używa jednego z wychodzących adresów IP jako źródłowego adresu IP. Nie można wcześniej wiedzieć, który adres IP danego wystąpienia aplikacji będzie używany do nawiązywania połączenia wychodzącego, więc usługa zaplecza musi otworzyć Zaporę do wszystkich wychodzących adresów IP aplikacji.

Zestaw wychodzących adresów IP dla aplikacji zmienia się podczas skalowania aplikacji między niższą warstwą (**podstawowa**, **standardowa**i **Premium**) i warstwą **Premium v2** .

Można znaleźć zestaw wszystkich możliwych wychodzących adresów IP, które mogą być używane przez aplikację, niezależnie od warstw cenowych, szukając `possibleOutboundIPAddresses` właściwości lub w polu dodatkowe wychodzące **adresy IP** w bloku **Właściwości** w Azure Portal. Zobacz [Znajdź wychodzące adresy IP](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Znajdź wychodzące adresy IP

Aby znaleźć wychodzące adresy IP aktualnie używane przez aplikację w Azure Portal, kliknij przycisk **Właściwości** w nawigacji po lewej stronie aplikacji. Są one wymienione w polu **wychodzące adresy IP** .

Te same informacje można znaleźć, uruchamiając następujące polecenie w [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Aby znaleźć _wszystkie_ możliwe wychodzące adresy IP dla aplikacji, bez względu na warstwy cenowe, kliknij pozycję **Właściwości** w obszarze nawigacji po lewej stronie aplikacji. Są one wymienione w polu **dodatkowe wychodzące adresy IP** .

Te same informacje można znaleźć, uruchamiając następujące polecenie w [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak ograniczyć ruch przychodzący według źródłowych adresów IP.

> [!div class="nextstepaction"]
> [Ograniczenia statycznych adresów IP](app-service-ip-restrictions.md)
