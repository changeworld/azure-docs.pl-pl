---
title: Zarządzanie IoT Central z programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie IoT Central z programu Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 086c7d303fd199090de3be77b2456c4ebcd053a8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726940"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Zarządzanie usługą IoT Central z programu Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Zamiast tworzenia aplikacji i zarządzaniem nimi IoT Central z IoT Central [Menedżer aplikacji](https://aka.ms/iotcentral) strony, można użyć [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli chcesz uruchomić programu Azure PowerShell na komputerze lokalnym, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po uruchomieniu programu Azure PowerShell lokalnie, użyj **Connect AzAccount** polecenia cmdlet, aby zalogować się do platformy Azure, przed podjęciem próby wykonania polecenia cmdlet w tym artykule.

## <a name="install-the-iot-central-module"></a>Zainstaluj moduł IoT Central

Uruchom następujące polecenie, aby sprawdzić [moduł IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) jest zainstalowana w Twoim środowisku programu PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Jeśli nie zawiera listę zainstalowanych modułów **Az.IotCentral**, uruchom następujące polecenie:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj [New AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) polecenia cmdlet do tworzenia aplikacji IoT Central w subskrypcji platformy Azure. Na przykład:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skrypt najpierw tworzy grupę zasobów w regionie wschodnim regionie USA dla aplikacji. W poniższej tabeli opisano parametry używane w **New AzIotCentralApp** polecenia:

|Parametr         |Opis |
|------------------|------------|
|ResourceGroupName |Grupa zasobów, który zawiera aplikację. Ta grupa zasobów musi już istnieć w subskrypcji. |
|Lokalizacja |Domyślnie to polecenie cmdlet używa lokalizacji z grupy zasobów. Obecnie można tworzyć aplikację IoT Central w **wschodnie stany USA**, **zachodnie stany USA**, **Europa Północna**, lub **Europa Zachodnia** regionów. |
|Name (Nazwa)              |Nazwa aplikacji w witrynie Azure portal. |
|Poddomena         |Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji jest https://mysubdomain.azureiotcentral.com. |
|SKU               |Obecnie jest to jedyna wartość **S1** (warstwa standardowa). Zobacz [cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji zobacz w poniższej tabeli: |
|Nazwa wyświetlana       |Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

**Szablony aplikacji**

|Nazwa szablonu  |Opis |
|---------------|------------|
|iotc-default@1.0.0 |Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |
|iotc-demo@1.0.0    |Tworzy aplikację, która zawiera już utworzony szablon urządzenia typu Automat do sprzedaży z chłodziarką. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś deweloperem urządzenia eksperymentowanie z dowolnego z tych urządzeń. |

## <a name="view-your-iot-central-applications"></a>Wyświetl aplikacje IoT Central

Użyj [Get AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) polecenia cmdlet, aby wyświetlić aplikacje IoT Central oraz metadanych.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj [AzIotCentralApp zestaw](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) polecenia cmdlet w celu zaktualizowania metadanych aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj [AzIotCentralApp Usuń](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) polecenia cmdlet, aby usunąć aplikację IoT Central. Na przykład:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak zarządzać aplikacjami usługi Azure IoT Central programu Azure PowerShell, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)
