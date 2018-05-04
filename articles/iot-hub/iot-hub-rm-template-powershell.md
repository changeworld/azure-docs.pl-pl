---
title: Tworzenie Centrum IoT Azure przy użyciu szablonu (PowerShell) | Dokumentacja firmy Microsoft
description: Jak szablon Menedżera zasobów Azure umożliwia tworzenie Centrum IoT przy użyciu programu PowerShell.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0e5f95d98f772b226e162f601939bc94bf8fb78b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Tworzenie Centrum IoT przy użyciu szablonu usługi Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tworzenie i zarządzanie nimi centra Azure IoT programowo, można użyć usługi Azure Resource Manager. Ten samouczek pokazuje, jak używać szablonu usługi Azure Resource Manager do tworzenia Centrum IoT przy użyciu programu PowerShell.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania usługi Azure Resource Manager.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure PowerShell 1.0] [ lnk-powershell-install] lub nowszym.

> [!TIP]
> Artykuł [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager] [ lnk-powershell-arm] zawiera więcej informacji na temat sposobu korzystania z szablonów programu PowerShell i usługi Azure Resource Manager tworzenie zasobów Azure.

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

W wierszu polecenia programu PowerShell wpisz następujące polecenie, aby zalogować się do subskrypcji platformy Azure:

```powershell
Connect-AzureRmAccount
```

Jeśli masz wiele subskrypcji Azure, logowanie do platformy Azure udziela dostępu do subskrypcji platformy Azure skojarzone z poświadczeniami użytkownika. Aby wyświetlić listę dostępnych przy użyciu subskrypcji platformy Azure, użyj następującego polecenia:

```powershell
Get-AzureRMSubscription
```

Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

Następujące polecenia służy do odnajdywania, w którym można wdrożyć Centrum IoT i aktualnie obsługiwane wersje interfejsu API:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Utwórz grupę zasobów zawiera Centrum IoT przy użyciu następującego polecenia w jednym z obsługiwanych lokalizacji dla Centrum IoT. To przykładowe polecenie tworzy grupę zasobów o nazwie **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Przesyłanie szablonu do tworzenia Centrum IoT

Użyj szablonu JSON do tworzenia Centrum IoT w grupie zasobów. Aby wprowadzić zmiany w istniejących Centrum IoT umożliwia także szablonu usługi Azure Resource Manager.

1. Użyj edytora tekstów, aby utworzyć szablon usługi Azure Resource Manager o nazwie **template.json** z następującą definicję zasobu do utworzenia nowego centrum IoT standardowa. W tym przykładzie dodaje Centrum IoT w **wschodnie stany USA** regionu, tworzy dwie grupy konsumentów (**cg1** i **cg2**) na punkt końcowy zgodnego Centrum zdarzeń i używa  **2016-02-03** wersja interfejsu API. Ten szablon również oczekuje Podaj nazwę Centrum IoT jako parametr o nazwie **hubName**. Dla bieżącej listy lokalizacje, które obsługują Centrum IoT [stan Azure][lnk-status].

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

3. Uruchom następujące polecenie, aby wdrożyć nowe Centrum IoT, przekazując nazwę Centrum IoT jako parametr. W tym przykładzie nazwa Centrum IoT jest `abcmyiothub`. Nazwa centrum IoT musi być globalnie unikatowe:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Dane wyjściowe wyświetla klucze utworzonego Centrum IoT.

5. Aby sprawdzić aplikacji dodane nowe Centrum IoT, odwiedź stronę [portalu Azure] [ lnk-azure-portal] i wyświetlanie listy zasobów. Można również użyć **Get-AzureRmResource** polecenia cmdlet programu PowerShell.

> [!NOTE]
> Ta przykładowa aplikacja dodaje Centrum IoT standardowe S1 dla której są rozliczane. Można usunąć Centrum IoT za pośrednictwem [portalu Azure] [ lnk-azure-portal] lub za pomocą **AzureRmResource Usuń** polecenia cmdlet programu PowerShell po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu Centrum IoT przy użyciu szablonu usługi Azure Resource Manager przy użyciu programu PowerShell, może zajść potrzeba dalszego zbadania:

* Przeczytaj informacje o możliwości [interfejsu API REST dostawcy zasobów Centrum IoT][lnk-rest-api].
* Odczyt [Omówienie usługi Azure Resource Manager] [ lnk-azure-rm-overview] Aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
