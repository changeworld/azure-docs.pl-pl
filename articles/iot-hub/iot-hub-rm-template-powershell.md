---
title: Tworzenie usługi Azure IoT Hub przy użyciu szablonu (PowerShell) | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT Hub przy użyciu programu PowerShell za pomocą szablonu usługi Azure Resource Manager.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: fcc9af9e614b0a1b7977ba18f3147fddab8b7b7d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045058"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Tworzenie Centrum IoT przy użyciu szablonu usługi Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Usługi Azure Resource Manager służy do tworzenia i zarządzania usługi Azure IoT hubs programowo. W tym samouczku dowiesz się, jak utworzyć Centrum IoT hub przy użyciu programu PowerShell za pomocą szablonu usługi Azure Resource Manager.

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługa Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure PowerShell 1.0] [ lnk-powershell-install] lub nowszej.

> [!TIP]
> Artykuł [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager] [ lnk-powershell-arm] zawiera więcej informacji o tym, jak używać szablonów programu PowerShell i usługi Azure Resource Manager do tworzenia zasobów platformy Azure.

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

W wierszu polecenia programu PowerShell wpisz następujące polecenie, aby zalogować się do subskrypcji platformy Azure:

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji platformy Azure, logowanie do platformy Azure zapewnia dostęp do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami użytkownika. Aby wyświetlić listę subskrypcji platformy Azure, która jest dostępna do użycia, użyj następującego polecenia:

```powershell
Get-AzSubscription
```

Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

```powershell
Select-AzSubscription `
    -SubscriptionName "{your subscription name}"
```

Następujące polecenia służy do odnajdywania, którym można wdrożyć usługi IoT hub i aktualnie obsługiwanych wersji interfejsu API:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Utwórz grupę zasobów zawierającą Centrum IoT hub przy użyciu następującego polecenia w jednym z obsługiwanych lokalizacji usługi IoT Hub. W tym przykładzie tworzy grupę zasobów o nazwie **MyIoTRG1**:

```powershell
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Przesyłanie szablonu do utworzenia Centrum IoT

Szablon JSON do tworzenia Centrum IoT w grupie zasobów. Można również użyć szablonu usługi Azure Resource Manager do wprowadzania zmian w istniejącym Centrum IoT.

1. Użyj edytora tekstu, aby utworzyć szablon usługi Azure Resource Manager o nazwie **template.json** przy użyciu następującej definicji zasobu, aby utworzyć nowe standardowe Centrum IoT. Ten przykład dodaje Centrum IoT w **wschodnie stany USA** regionu, tworzy dwie grupy odbiorców (**cg1** i **cg2**) na punkt końcowy zgodny z Centrum zdarzeń i używa  **2016-02-03** wersji interfejsu API. Ten szablon oczekuje przekazania nazwy Centrum IoT, jako parametr o nazwie **hubName**. Dla bieżącej listy lokalizacje, które obsługują usługi IoT Hub, zobacz [stanu platformy Azure][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Zapisz plik szablonu usługi Azure Resource Manager na komputerze lokalnym. W tym przykładzie przyjęto założenie, zapisz go w folderze o nazwie **c:\templates**.

3. Uruchom następujące polecenie, aby wdrożyć nowe Centrum IoT hub, przekazując nazwę Centrum IoT hub jako parametr. W tym przykładzie nazwa usługi IoT hub jest `abcmyiothub`. Nazwa centrum IoT Hub musi być unikatowa w skali globalnej:

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Dane wyjściowe wyświetla klucze dla usługi IoT hub, który został utworzony.

5. Aby sprawdzić, aplikacja dodaje nowe Centrum IoT hub, odwiedź stronę [witryny Azure portal] [ lnk-azure-portal] i wyświetlanie listy zasobów. Można również użyć **Get AzResource** polecenia cmdlet programu PowerShell.

> [!NOTE]
> Ta przykładowa aplikacja dodaje S1 standardowa usługi IoT Hub dla którego stosowana jest stawka. Możesz usunąć Centrum IoT hub za pośrednictwem [witryny Azure portal] [ lnk-azure-portal] lub za pomocą **AzResource Usuń** polecenia cmdlet programu PowerShell po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu usługi IoT hub przy użyciu szablonu usługi Azure Resource Manager przy użyciu programu PowerShell, warto dokładniej:

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
