---
title: Tworzenie usługi Azure IoT Hub przy użyciu polecenia cmdlet programu PowerShell | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT hub przy użyciu polecenia cmdlet programu PowerShell.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: fd376728a1ebdf769c7f2ae76d61a60703e13711
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146607"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Tworzenie Centrum IoT przy użyciu polecenia cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Wprowadzenie

Polecenia cmdlet programu Azure PowerShell umożliwia tworzenie i zarządzanie usługą Azure IoT hubs. W tym samouczku dowiesz się, jak utworzyć Centrum IoT hub przy użyciu programu PowerShell.

Aby ukończyć Instruktaż, potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Jeśli używasz powłoki Cloud już zalogowano Cię do Twojej subskrypcji. Jeśli używasz programu PowerShell lokalnie zamiast tego, wprowadź następujące polecenie, aby zalogować się do subskrypcji platformy Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Należy grupę zasobów, aby wdrożyć usługi IoT hub. Można użyć istniejącej grupy zasobów lub Utwórz nową.

Aby utworzyć grupę zasobów dla Centrum IoT, użyj [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) polecenia. W tym przykładzie tworzy grupę zasobów o nazwie **MyIoTRG1** w **wschodnie stany USA** regionu:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Aby utworzyć Centrum IoT w grupie zasobów utworzonej w poprzednim kroku, należy użyć [New AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) polecenia. Ten przykład tworzy **S1** Centrum o nazwie **MyTestIoTHub** w **wschodnie stany USA** regionu:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Nazwa centrum IoT hub musi być globalnie unikatowa.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Możesz wyświetlić listę wszystkich centrów IoT w subskrypcji przy użyciu [Get AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) polecenia:

```azurepowershell-interactive
Get-AzIotHub
```

Ten przykład przedstawia standardowa usługi IoT Hub S1 utworzonego w poprzednim kroku.

Możesz usunąć Centrum IoT przy użyciu [AzIotHub Usuń](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) polecenia:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternatywnie możesz usunąć grupę zasobów i wszystkie zasoby zawiera przy użyciu [AzResourceGroup Usuń](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) polecenia:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Kolejne kroki

Teraz wdrożeniu Centrum IoT hub przy użyciu polecenia cmdlet programu PowerShell, jeśli chcesz dokładniej, zapoznaj się z następującymi artykułami:

* [Polecenia cmdlet programu PowerShell do pracy z Centrum IoT hub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Interfejs API REST dostawcy zasobów usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
