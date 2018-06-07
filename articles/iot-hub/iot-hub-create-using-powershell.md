---
title: Tworzenie Centrum IoT Azure przy użyciu polecenia cmdlet programu PowerShell | Dokumentacja firmy Microsoft
description: Jak używać polecenia cmdlet programu PowerShell do tworzenia Centrum IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 78cf7844223b660eef3dea0a32cd7c325e88e083
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634050"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Tworzenie Centrum IoT przy użyciu polecenia cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Wprowadzenie

Tworzenie i zarządzanie nimi centra Azure IoT, można użyć poleceń cmdlet programu Azure PowerShell. W tym samouczku przedstawiono sposób tworzenia Centrum IoT przy użyciu programu PowerShell.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania usługi Azure Resource Manager.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Polecenia cmdlet programu PowerShell systemu Azure][lnk-powershell-install].

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

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Należy grupę zasobów do wdrożenia Centrum IoT. Można użyć istniejącej grupy zasobów lub Utwórz nową.

Następujące polecenie służy do odnajdywania lokalizacji, w którym można wdrożyć Centrum IoT:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Aby utworzyć grupę zasobów Centrum IoT w jednym z obsługiwanych lokalizacji dla Centrum IoT, użyj następującego polecenia. To przykładowe polecenie tworzy grupę zasobów o nazwie **MyIoTRG1** w **wschodnie stany USA** regionu:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Aby utworzyć Centrum IoT w grupie zasobów utworzonej w poprzednim kroku, użyj następującego polecenia. Ten przykład tworzy **S1** koncentratora o nazwie **MyTestIoTHub** w **wschodnie stany USA** regionu:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Nazwa centrum IoT musi być unikatowa.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Możesz wyświetlić listę wszystkich centra IoT w ramach subskrypcji, za pomocą następującego polecenia:

```powershell
Get-AzureRmIotHub
```

Poprzedni przykład dodaje Centrum IoT standardowe S1 dla której są rozliczane. Można usunąć Centrum IoT przy użyciu następującego polecenia:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternatywnie można usunąć grupy zasobów i wszystkie zasoby, które zawiera przy użyciu następującego polecenia:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu przy użyciu polecenia cmdlet programu PowerShell Centrum IoT, może zajść potrzeba dalszego zbadania:

* Odnajdywanie innych [poleceń cmdlet programu PowerShell do pracy z Centrum IoT][lnk-iothub-cmdlets].
* Przeczytaj informacje o możliwości [interfejsu API REST dostawcy zasobów Centrum IoT][lnk-rest-api].

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
