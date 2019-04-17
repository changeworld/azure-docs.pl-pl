---
title: Tworzenie usługi Azure IoT Hub przy użyciu szablonu (PowerShell) | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT Hub przy użyciu programu Azure PowerShell za pomocą szablonu usługi Azure Resource Manager.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609181"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Tworzenie Centrum IoT przy użyciu szablonu usługi Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Dowiedz się, jak utworzyć Centrum IoT i grupy odbiorców za pomocą szablonu usługi Azure Resource Manager. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [dokumentacji usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Szablon usługi Resource Manager używanych w tym przewodniku Szybki Start jest z [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Oto kopię szablonu:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Ten szablon tworzy usługi Azure Iot hub przy użyciu trzech punktów końcowych (Centrum zdarzeń, chmury do urządzenia i obsługi komunikatów) i grupy odbiorców. Aby uzyskać więcej przykładów szablonu, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Schemat szablonu usługi Iot Hub można znaleźć [tutaj](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Istnieje kilka metod wdrażania szablonu.  Użyjesz programu Azure PowerShell w ramach tego samouczka.

Aby uruchomić skrypt programu PowerShell, zaznacz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz Wklej:

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

Jak widać w skrypcie programu PowerShell, szablon używany jest przy użyciu szablonów szybkiego startu platformy Azure. Aby użyć własnych, należy najpierw przekazać plik szablonu do usługi Cloud shell, a następnie użyj `-TemplateFile` przełącznik, aby określić nazwę pliku.  Aby uzyskać przykład, zobacz [wdrożenia szablonu](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu usługi IoT hub przy użyciu szablonu usługi Azure Resource Manager możesz dokładniej zapoznać się:

* Przeczytaj o możliwości [interfejs API REST dostawcy zasobów usługi IoT Hub][lnk-rest-api].
* Odczyt [Omówienie usługi Azure Resource Manager] [ lnk-azure-rm-overview] Aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.
* Składnię JSON i właściwości do użycia w szablonach, zobacz [typów zasobów Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
