---
title: Tworzenie centrum Usługi Azure IoT hub przy użyciu szablonu (PowerShell) | Dokumenty firmy Microsoft
description: Jak użyć szablonu usługi Azure Resource Manager do utworzenia usługi IoT Hub za pomocą programu Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976623"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Tworzenie centrum IoT przy użyciu szablonu usługi Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Dowiedz się, jak utworzyć centrum Usługi IoT i grupę odbiorców za pomocą szablonu usługi Azure Resource Manager. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby uzyskać więcej informacji na temat tworzenia szablonów Usługi Resource Manager, zobacz [dokumentację usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Szablon Menedżera zasobów używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Oto kopia szablonu:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Szablon tworzy centrum Usługi Azure Iot z trzema punktami końcowymi (eventhub, cloud-to-device i wiadomości) i grupą konsumentów. Aby uzyskać więcej przykładów szablonów, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Schemat szablonu Iot Hub można znaleźć [tutaj](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Istnieje kilka metod wdrażania szablonu.  Program Azure PowerShell jest używany w tym samouczku.

Aby uruchomić skrypt programu PowerShell, wybierz pozycję **Spróbuj,** aby otworzyć powłokę usługi Azure Cloud. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie Wklej:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Jak widać na skrypcie programu PowerShell, używany szablon pochodzi z szablonów szybki start platformy Azure. Aby użyć własnego, należy najpierw przekazać plik szablonu do powłoki `-TemplateFile` chmury, a następnie użyć przełącznika, aby określić nazwę pliku.  Na przykład zobacz [Wdrażanie szablonu](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Następne kroki

Teraz wdrożono centrum IoT hub przy użyciu szablonu Usługi Azure Resource Manager, można zbadać dalej:

* Przeczytaj o możliwościach [interfejsu API REST dostawcy zasobów usługi IoT Hub][lnk-rest-api].
* Przeczytaj [omówienie usługi Azure Resource Manager,][lnk-azure-rm-overview] aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.
* Aby uzyskać składnię JSON i właściwości używane w szablonach, zobacz [Typy zasobów Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do C SDK][lnk-c-sdk]
* [Zestawy SDK usługi Azure IoT][lnk-sdks]

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
