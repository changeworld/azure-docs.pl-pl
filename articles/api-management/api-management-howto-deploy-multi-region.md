---
title: Wdrażanie usługi Azure API Management w wielu regionach platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058043"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure

Usługa Azure API Management obsługuje wdrożenie w wielu regionach, która pozwala wydawcy interfejsów API rozprowadzić pojedynczą usługą zarządzania interfejsem API usługi Azure na dowolnej liczbie regionów platformy Azure żądaną. Zmniejsza to żądanie opóźnienie postrzegane przez rozproszone geograficznie konsumentów interfejsu API i zwiększa również dostępność usługi, jeśli jeden region przejdzie do trybu offline.

Nowa usługa Azure API Management początkowo zawiera tylko jeden [jednostki] [ unit] w jednym regionie platformy Azure w regionie podstawowym. Dodatkowych regionów można łatwo dodawać za pośrednictwem witryny Azure portal. Serwer bramy usługi API Management jest wdrażany do każdego regionu, a ruch połączenia będą kierowane do najbliższego bramy pod kątem opóźnień. Jeśli region przejdzie do trybu offline, ruch jest automatycznie przekierowywane do następnego najbliższej bramy.

> [!NOTE]
> Usługa Azure API Management są replikowane tylko składnik bramy interfejsu API w regionach. Składnik Usługa zarządzania jest obsługiwana tylko w regionie podstawowym. W razie awarii w regionie podstawowym zastosowanie zmiany konfiguracji do wystąpienia usługi Azure API Management jest niemożliwe — w tym ustawienia lub aktualizacji zasad.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Wdrażanie wystąpienia usługi API Management w nowym regionie

> [!NOTE]
> Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management][Create an API Management service instance].

W witrynie Azure portal przejdź do **skalowanie i cennik** strony dla swojego wystąpienia usługi API Management. 

![Kartę Skala][api-management-scale-service]

Aby wdrożyć nowy region, kliknąć **+ Dodaj region** na pasku narzędzi.

![Dodaj region][api-management-add-region]

Wybierz lokalizację z listy rozwijanej, a następnie ustaw liczbę jednostek za pomocą suwaka.

![Określ jednostki][api-management-select-location-units]

Kliknij przycisk **Dodaj** umieścić wybór w tabeli lokalizacje. 

Powtórz ten proces, dopóki nie uzyskasz wszystkie lokalizacje skonfigurowane, a następnie kliknij przycisk **Zapisz** na pasku narzędzi, aby rozpocząć proces wdrażania.

## <a name="remove-region"> </a>Usuń wystąpienie usługi API Management z lokalizacji

W witrynie Azure portal przejdź do **skalowanie i cennik** strony dla swojego wystąpienia usługi API Management. 

![Kartę Skala][api-management-scale-service]

Dla lokalizacji, czy chcesz usunąć, Otwórz za pomocą menu kontekstowego **...**  przycisk po prawej stronie tabeli. Wybierz **Usuń** opcji.

Potwierdzenie usunięcia, a następnie kliknij przycisk **Zapisz** Aby zastosować zmiany.

## <a name="route-backend"> </a>Trasy interfejsu API wywołania usług regionalnych zaplecza

Usługa Azure API Management zawiera tylko jeden adres URL usługi wewnętrznej bazy danych. Mimo że istnieją wystąpienia usługi Azure API Management, w różnych regionach, brama interfejsu API będzie nadal przekazywać żądania do tej samej usługi wewnętrznej bazy danych, która jest wdrażana w tylko jednym regionie. W tym przypadku przyrost wydajności będą pochodzić tylko z pamięci podręcznej w usłudze Azure API Management w danym regionie, które są specyficzne dla żądania odpowiedzi, ale nawiązywanie kontaktu z wewnętrznej bazy danych na całym świecie nadal może spowodować duże opóźnienie.

W pełni wykorzystać rozkład geograficzny systemu, powinien mieć usług zaplecza wdrożonych na tych samych regionach jako wystąpienia usługi Azure API Management. Następnie przy użyciu zasad i `@(context.Deployment.Region)` właściwości, można kierować ruch do lokalnego wystąpienia usługi wewnętrznej bazy danych.

1. Przejdź do swojego wystąpienia usługi Azure API Management i kliknij **interfejsów API** menu po lewej stronie.
2. Wybierz żądany interfejs API.
3. Kliknij przycisk **Edytor kodu** z strzałkę listy rozwijanej w **przychodzące przetwarzanie**.

    ![Edytor kodu interfejsu API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Użyj `set-backend` w połączeniu z warunkowego `choose` zasad w celu utworzenia odpowiednich zasad routingu w `<inbound> </inbound>` części pliku.

    Na przykład poniżej XML pliku będzie działać w regionach zachodnie stany USA oraz Azja Wschodnia:

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
> Może również frontonu usługi wewnętrznej bazy danych za pomocą [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)bezpośrednie wywołania interfejsu API do usługi Traffic Manager i przejdź do niego rozwiązać automatycznie routingu.

## <a name="custom-routing"> </a>Użyj niestandardowych tras regionalnych bramy usługi API Management

Usługa API Management przekierowywać żądania do regionalny *bramy* na podstawie [najmniejszego opóźnienia](../traffic-manager/traffic-manager-routing-methods.md#performance). Chociaż nie jest to możliwe zastąpić to ustawienie w usłudze API Management, można użyć Menedżera ruchu za pomocą niestandardowych reguł routingu.

1. Utwórz swoje własne [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Jeśli używasz domeny niestandardowej [używać go z usługą Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) zamiast usługi API Management.
1. [Konfigurowanie regionalnej punktów końcowych usługi API Management w usłudze Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Regionalne punkty końcowe oparte na wzorcu adresu URL `https://<service-name>-<region>-01.regional.azure-api.net`, na przykład `https://contoso-westus2-01.regional.azure-api.net`.
1. [Konfigurowanie punktów końcowych z regionalnym stan usługi API Management w usłudze Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Punkty końcowe regionalnych stan oparte na wzorcu adresu URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, na przykład `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Określ [metody routingu](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manager.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
