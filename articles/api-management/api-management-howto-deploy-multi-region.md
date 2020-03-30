---
title: Wdrażanie usług usługi Azure API Management w wielu regionach platformy Azure
titleSuffix: Azure API Management
description: Dowiedz się, jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442666"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure

Usługa Azure API Management obsługuje wdrażanie w wielu regionach, co umożliwia wydawcom interfejsu API rozpowszechnianie jednej usługi zarządzania interfejsami API platformy Azure w dowolnej liczbie obsługiwanych regionów platformy Azure. Funkcja wieloregionalna pomaga zmniejszyć opóźnienie żądania postrzegane przez geograficznie rozproszonych konsumentów interfejsu API i zwiększa dostępność usług, jeśli jeden region przejdzie w tryb offline.

Nowa usługa Azure API Management początkowo zawiera tylko jedną [jednostkę][unit] w jednym regionie platformy Azure, regionu podstawowego. Dodatkowe regiony można dodać do regionów podstawowej lub pomocniczej. Składnik bramy zarządzania interfejsami API jest wdrażany w każdym wybranym regionie podstawowym i pomocniczym. Przychodzące żądania interfejsu API są automatycznie kierowane do najbliższego regionu. Jeśli region przejdzie w tryb offline, żądania interfejsu API będą automatycznie kierowane wokół regionu, który nie powiódł się do następnej najbliższej bramy.

> [!NOTE]
> Tylko składnik bramy usługi API Management jest wdrażany we wszystkich regionach. Składnik zarządzania usługami i portal dewelopera są hostowane tylko w regionie podstawowym. W związku z tym w przypadku awarii regionu podstawowego dostęp do portalu dewelopera i możliwość zmiany konfiguracji (np. dodawanie interfejsów API, stosowanie zasad) zostaną naruszone, dopóki region podstawowy nie powróci do trybu online. Gdy region podstawowy jest dostępny w trybie offline regionów pomocniczych będzie nadal służyć ruchu interfejsu API przy użyciu najnowszej konfiguracji dostępne dla nich.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Wdrażanie wystąpienia usługi zarządzania interfejsami API w nowym regionie

> [!NOTE]
> Jeśli nie utworzono jeszcze wystąpienia usługi zarządzanie interfejsami API, zobacz [Tworzenie wystąpienia usługi zarządzania interfejsami API][create an api management service instance].

W witrynie Azure Portal przejdź do strony **Skalowanie i cennik** wystąpienia usługi zarządzania interfejsami API.

![Karta Skalowanie][api-management-scale-service]

Aby wdrożyć w nowym regionie, kliknij przycisk **+ Dodaj region** z paska narzędzi.

![Dodaj region][api-management-add-region]

Wybierz lokalizację z listy rozwijanej i ustaw liczbę jednostek za pomocą suwaka.

![Określ jednostki][api-management-select-location-units]

Kliknij **przycisk Dodaj,** aby umieścić zaznaczenie w tabeli Lokalizacje.

Powtarzaj ten proces, dopóki nie skonfigurowano wszystkich lokalizacji, a następnie kliknij przycisk **Zapisz** na pasku narzędzi, aby rozpocząć proces wdrażania.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Usuwanie wystąpienia usługi zarządzania interfejsami API z lokalizacji

W witrynie Azure Portal przejdź do strony **Skalowanie i cennik** wystąpienia usługi zarządzania interfejsami API.

![Karta Skalowanie][api-management-scale-service]

Dla lokalizacji, którą chcesz usunąć, otwórz menu kontekstowe za pomocą **przycisku ...** po prawej stronie tabeli. Wybierz opcję **Usuń.**

Potwierdź usunięcie i kliknij przycisk **Zapisz,** aby zastosować zmiany.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Rozsyłanie wywołań interfejsu API do regionalnych usług zaplecza

Usługa Azure API Management zawiera tylko jeden adres URL usługi wewnętrznej bazy danych. Mimo że istnieją wystąpienia usługi Azure API Management w różnych regionach, brama interfejsu API będzie nadal przesyłać dalej żądania do tej samej usługi wewnętrznej bazy danych, która jest wdrażana tylko w jednym regionie. W takim przypadku przyrost wydajności będzie pochodzić tylko z odpowiedzi buforowanych w ramach usługi Azure API Management w regionie specyficznym dla żądania, ale kontaktowanie się z zaplecza na całym świecie może nadal powodować duże opóźnienia.

Aby w pełni wykorzystać rozkład geograficzny systemu, należy wdrożyć usługi wewnętrznej bazy danych wdrożone w tych samych regionach, co wystąpienia usługi Azure API Management. Następnie za pomocą `@(context.Deployment.Region)` zasad i właściwości, można kierować ruch do lokalnych wystąpień wewnętrznej bazy danych.

1. Przejdź do wystąpienia usługi Azure API Management i kliknij **interfejsy API** z lewego menu.
2. Wybierz żądany interfejs API.
3. Kliknij **pozycję Edytor kodu** z listy rozwijanej strzałki w obszarze Przetwarzanie **przychodzące**.

    ![Edytor kodu interfejsu API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Użyj `set-backend` zasad połączonych `choose` z zasadami warunkowymi, `<inbound> </inbound>` aby utworzyć odpowiednie zasady routingu w sekcji pliku.

    Na przykład poniższy plik XML będzie działać w regionach Zachodnie stany USA i Azji Wschodniej:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Możesz również kierować usługi wewnętrznej bazy danych za pomocą [usługi Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)kierować wywołania interfejsu API do usługi Traffic Manager i umożliwiać automatyczne rozwiązywanie routingu.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Używanie routingu niestandardowego do bram regionalnych usługi API Management

Usługa API Management kieruje żądania do _bramy_ regionalnej na podstawie [najniższego opóźnienia.](../traffic-manager/traffic-manager-routing-methods.md#performance) Chociaż nie jest możliwe zastąpienie tego ustawienia w usłudze API Management, można użyć własnego menedżera ruchu z niestandardowymi regułami routingu.

1. Utwórz własny [program Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Jeśli używasz domeny niestandardowej, [użyj jej z Menedżerem ruchu](../traffic-manager/traffic-manager-point-internet-domain.md) zamiast usługi Zarządzanie interfejsami API.
1. [Skonfiguruj regionalne punkty końcowe usługi API Management w usłudze Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Regionalne punkty końcowe są zgodne `https://<service-name>-<region>-01.regional.azure-api.net`ze `https://contoso-westus2-01.regional.azure-api.net`wzorcem adresu URL , na przykład .
1. [Skonfiguruj regionalne punkty końcowe stanu usługi API Management w usłudze Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Punkty końcowe stanu regionalnego są `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`zgodne ze `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`wzorcem adresu URL , na przykład .
1. Określ [metodę routingu](../traffic-manager/traffic-manager-routing-methods.md) Menedżera ruchu.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
