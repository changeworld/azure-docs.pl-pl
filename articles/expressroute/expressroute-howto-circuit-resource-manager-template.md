---
title: 'Szablon usługi Azure ExpressRoute: tworzenie obwodu usługi ExpressRoute'
description: Tworzenie, inicjowania obsługi administracyjnej, usuwania i usuwania obsługi administracyjnej obwodu usługi ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: charwen
ms.openlocfilehash: ac2fb2f884feb0fbe6fbb1731ebe7c8bdad5114b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616504"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Tworzenie obwodu usługi ExpressRoute przy użyciu szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Dowiedz się, jak utworzyć obwód usługi ExpressRoute, wdrażając szablon usługi Azure Resource Manager przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji na temat tworzenia szablonów Menedżera zasobów, zobacz [Dokumentacja Menedżera zasobów](/azure/azure-resource-manager/) i odwołanie do [szablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* Możesz [obejrzeć film](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i udostępnianie obwodu usługi ExpressRoute

[Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/) ma dobrą kolekcję szablonu Menedżera zasobów. Jeden z [istniejących szablonów](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) służy do tworzenia obwodu usługi ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Aby wyświetlić więcej powiązanych szablonów, wybierz [tutaj](https://azure.microsoft.com/resources/templates/?term=expressroute).

Aby utworzyć obwód usługi ExpressRoute przez wdrożenie szablonu:

1. Wybierz **pozycję Wypróbuj z** następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do powłoki usługi Azure Cloud.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **Warstwa SKU** określa, czy obwód usługi ExpressRoute to [Local,](expressroute-faqs.md#expressroute-local)Standard lub [Premium](expressroute-faqs.md#expressroute-premium). Można określić *lokalny*, *Standard lub *Premium*.
   * **Rodzina jednostek SKU** określa typ rozliczeń. Można określić *Dane metereddata* dla taryfowego planu danych i *nieograniczone dane* dla nieograniczonego planu danych. Typ rozliczeń można zmienić z *Metereddata* na *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* na *Metereddata*. Obwód *lokalny* jest tylko *nieograniczonedane.*
   * **Lokalizacja komunikacji równorzędnej** to fizyczna lokalizacja, w której można komunikacji równorzędnej z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [fizyczną lokalizację,](expressroute-locations.md) w której można komunikacji równorzędnej z firmą Microsoft. **Nie** jest to połączone z właściwością "Lokalizacja", która odwołuje się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrą praktyką jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

    Nazwa grupy zasobów jest nazwą obszaru nazw magistrali usług z **dołączeną rg.**

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.

Utworzenie centrum zdarzeń zajmuje kilka chwil.

Program Azure PowerShell jest używany do wdrażania szablonu w tym samouczku. Aby uzyskać inne metody wdrażania szablonów, zobacz:

* [Za pomocą portalu Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Za pomocą narzędzia Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Za pomocą interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Obwód usługi ExpressRoute można usunąć, wybierając ikonę **usuwania.** Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwódem.
* Jeśli stan inicjowania obsługi administracyjnej dostawcy usług obwodu usługi ExpressRoute to **inicjowanie obsługi administracyjnej** lub **inicjowana obsługa,** należy współpracować z dostawcą usług w celu anulowania obsługi administracyjnej obwodu po ich stronie. Nadal rezerwujemy zasoby i rozliczamy cię do czasu zakończenia przez usługodawcę anulowania obsługi administracyjnej obwodu i powiadomi nas o tym.
* Jeśli dostawca usług wyrejestrował obwód (stan inicjowania obsługi administracyjnej dostawcy usług jest ustawiony na **Nie aprowizowana),** można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód usługi ExpressRoute można usunąć, uruchamiając następujące polecenie programu PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu należy wykonać następujące czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
