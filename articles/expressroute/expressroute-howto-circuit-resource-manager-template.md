---
title: 'Utwórz ExpressRoute obwód Menedżer zasobów: Azure | Microsoft Docs'
description: Tworzenie, Inicjowanie obsługi, usuwanie i cofanie aprowizacji obwodu ExpressRoute.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 103c61b6ad244bf4b140f897c070ce5bfd54cded
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849218"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Tworzenie obwodu usługi ExpressRoute przy użyciu szablonu Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Dowiedz się, jak utworzyć obwód usługi ExpressRoute, wdrażając szablon Azure Resource Manager przy użyciu Azure PowerShell. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](/azure/azure-resource-manager/) [szablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* Możesz [klip wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create"></a>Tworzenie i aprowizowanie obwodu usługi ExpressRoute

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) mają dobrą kolekcję Menedżer zasobów szablonu. Użyj jednego z [istniejących szablonów](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) do utworzenia obwodu ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Aby wyświetlić więcej powiązanych szablonów, wybierz [tutaj](https://azure.microsoft.com/resources/templates/?term=expressroute).

Aby utworzyć obwód ExpressRoute przez wdrożenie szablonu:

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do usługi Azure Cloud Shell.

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

    Nazwa grupy zasobów to nazwa przestrzeni nazw usługi Service Bus z dołączoną **RG** .

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.

Utworzenie centrum zdarzeń trwa kilka chwil.

Azure PowerShell służy do wdrażania szablonu w tym samouczku. Aby poznać inne metody wdrażania szablonów, zobacz:

* Za [pomocą Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* Za [pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* Za [pomocą interfejsu API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Możesz usunąć obwód usługi ExpressRoute, wybierając **Usuń** ikony. Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli operacja zakończy się niepowodzeniem, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli dostawca usług obwodu usługi ExpressRoute, w stanie inicjowania obsługi **aprowizacji** lub **Aprowizowana** należy skontaktować się z dostawcą usługi, aby anulować aprowizację obwodu po ich stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Jeśli dostawca usług ma anulowanie aprowizacji obwodu (stan aprowizacji dostawcy usług jest równa **nie zainicjowano obsługi administracyjnej**), można usunąć obwodu. Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód ExpressRoute można usunąć, uruchamiając następujące polecenie programu PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu obwodu, Kontynuuj z następujących czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej na obwód usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
