---
title: 'Utwórz obwód usługi ExpressRoute — szablon usługi Resource Manager: Azure | Microsoft Docs'
description: Tworzenie, aprowizować, usuwanie i anulować aprowizację obwodu usługi ExpressRoute.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659686"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Utwórz obwód usługi ExpressRoute za pomocą szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Dowiedz się, jak utworzyć obwód usługi ExpressRoute przez wdrożenie szablonu usługi Azure Resource Manager przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [dokumentacji usługi Resource Manager](/azure/azure-resource-manager/) i [odwołanie do szablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* Możesz [klip wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create"></a>Tworzenie i aprowizowanie obwodu usługi ExpressRoute

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) ma dobrą kolekcję szablonu usługi Resource Manager. Możesz użyć jednej z [istniejących szablonów](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) utworzyć obwód usługi ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Aby wyświetlić więcej powiązanych szablonów, wybierz [tutaj](https://azure.microsoft.com/resources/templates/?term=expressroute).

Aby utworzyć obwód usługi ExpressRoute przez wdrożenie szablonu:

1. Wybierz **wypróbuj** z następujący blok kodu i postępuj zgodnie z instrukcjami, aby zarejestrować się w usłudze Azure Cloud shell.

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

   * **Warstwa** Określa, czy włączona jest standardem usługi ExpressRoute lub dodatek ExpressRoute premium. Można określić **standardowa** można pobrać standardowej jednostki SKU lub **Premium** używania dodatku premium.

   * **Lokalizacja komunikacji równorzędnej** jest lokalizacji fizycznej, w którym są komunikacji równorzędnej z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [lokalizacji fizycznej](expressroute-locations.md) gdzie są komunikacji równorzędnej z firmą Microsoft. Jest to **nie** połączone z właściwością "Lokalizacja", która odwołuje się do lokalizacji geograficznej, w którym znajduje się dostawca zasobów sieciowych platformy Azure. Gdy nie są powiązane, jest dobrym rozwiązaniem, aby wybrać dostawca zasobów sieciowych w lokalizacji geograficznej blisko lokalizacji komunikacji równorzędnej obwodu.

    Nazwa grupy zasobów jest nazwa przestrzeni nazw magistrali usług przy użyciu **rg** dołączane.

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsoli shell, a następnie wybierz **Wklej**.

Może potrwać kilka minut, aby utworzyć Centrum zdarzeń.

Program Azure PowerShell jest używany do wdrożenia szablonu w ramach tego samouczka. Aby uzyskać inne metody wdrażania szablonu zobacz:

* [Za pomocą witryny Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Przy użyciu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Za pomocą interfejsu API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Możesz usunąć obwód usługi ExpressRoute, wybierając **Usuń** ikony. Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli operacja zakończy się niepowodzeniem, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli dostawca usług obwodu usługi ExpressRoute, w stanie inicjowania obsługi **aprowizacji** lub **Aprowizowana** należy skontaktować się z dostawcą usługi, aby anulować aprowizację obwodu po ich stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Jeśli dostawca usług ma anulowanie aprowizacji obwodu (stan aprowizacji dostawcy usług jest równa **nie zainicjowano obsługi administracyjnej**), można usunąć obwodu. Spowoduje to zatrzymanie naliczania opłat za obwód.

Możesz usunąć obwód usługi ExpressRoute, uruchamiając następujące polecenie programu PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu, Kontynuuj z następujących czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej na obwód usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
