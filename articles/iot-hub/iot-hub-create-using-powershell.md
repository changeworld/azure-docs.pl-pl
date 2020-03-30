---
title: Tworzenie centrum Usługi Azure IoT Hub przy użyciu polecenia cmdlet programu PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć grupę zasobów za pomocą poleceń cmdlet programu PowerShell, a następnie utworzyć centrum IoT w grupie zasobów. Dowiedz się również, jak usunąć koncentrator.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890607"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Tworzenie centrum IoT przy użyciu polecenia cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Wprowadzenie

Polecenia cmdlet usługi Azure PowerShell umożliwiają tworzenie centrów IoT usługi Azure i zarządzanie nimi. W tym samouczku pokazano, jak utworzyć centrum IoT z programem PowerShell.

Aby wykonać ten sposób, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Jeśli używasz usługi Cloud Shell, jesteś już zalogowany do subskrypcji. Jeśli zamiast tego korzystasz lokalnie z programem PowerShell, wprowadź następujące polecenie, aby zalogować się do subskrypcji platformy Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Do wdrożenia centrum IoT hub potrzebna jest grupa zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć grupę zasobów dla centrum IoT, należy użyć polecenia [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) W tym przykładzie tworzy grupę zasobów o nazwie **MyIoTRG1** w regionie **Wschodnie stany USA:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Aby utworzyć centrum IoT hub w grupie zasobów utworzonej w poprzednim kroku, należy użyć polecenia [New-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) W tym przykładzie tworzy centrum **S1** o nazwie **MyTestIoTHub** w regionie **wschodnich stanów USA:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Nazwa centrum IoT musi być unikatowa globalnie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Za pomocą polecenia [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) można wyświetlić listę wszystkich centrów IoT w ramach subskrypcji:

```azurepowershell-interactive
Get-AzIotHub
```

W tym przykładzie przedstawiono centrum IoT standard S1 utworzone w poprzednim kroku.

Centrum IoT można usunąć za pomocą polecenia [Usuń-AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternatywnie można usunąć grupę zasobów i wszystkie zasoby, które zawiera za pomocą polecenia [Usuń-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Następne kroki

Teraz wdrożono centrum IoT przy użyciu polecenia cmdlet programu PowerShell, jeśli chcesz zbadać dalej, zapoznaj się z następującymi artykułami:

* [Polecenia cmdlet programu PowerShell do pracy z koncentratorem IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Interfejs API REST dostawcy zasobów usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
