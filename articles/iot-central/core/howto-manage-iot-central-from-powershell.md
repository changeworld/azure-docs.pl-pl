---
title: Zarządzaj IoT Central z Azure PowerShell | Microsoft Docs
description: Zarządzaj IoT Central z Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 269b8a6c2a90884b060b4fb916b77af919a63a63
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953345"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Zarządzanie usługą IoT Central z programu Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz uruchamiać Azure PowerShell na komputerze lokalnym, zobacz Install the [Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). Po uruchomieniu Azure PowerShell lokalnie Użyj polecenia cmdlet **Connect-AzAccount** , aby zalogować się do platformy Azure przed podjęciem próby wykonania poleceń cmdlet w tym artykule.

## <a name="install-the-iot-central-module"></a>Instalowanie modułu IoT Central

Uruchom następujące polecenie, aby sprawdzić, czy [moduł IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) jest zainstalowany w środowisku programu PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Jeśli lista zainstalowanych modułów nie zawiera polecenia **AZ. IotCentral**, uruchom następujące polecenie:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj polecenia cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) , aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Na przykład:

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

Skrypt najpierw tworzy grupę zasobów w regionie Wschodnie stany USA dla aplikacji. W poniższej tabeli opisano parametry używane z poleceniem **New-AzIotCentralApp** :

|Parametr         |Opis |
|------------------|------------|
|ResourceGroupName |Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w Twojej subskrypcji. |
|Lokalizacja |Domyślnie to polecenie cmdlet używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w regionach **Wschodnie stany USA**, **zachodnie stany USA**, **Europa Północna**lub **Europa Zachodnia** lub w **Australii** lub **Azja i Pacyfik** lokalizacje geograficzne.  |
|Nazwa              |Nazwa aplikacji w Azure Portal. |
|poddomeny         |Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji to https://mysubdomain.azureiotcentral.com. |
|Jednostka SKU               |Obecnie jedyną wartością jest **S1** (warstwa standardowa). Zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz następującą tabelę: |
|Nazwa       |Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

**Szablony aplikacji**

|Nazwa szablonu  |Opis |
|---------------|------------|
|iotc-default@1.0.0 |Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |
|iotc-demo@1.0.0    |Tworzy aplikację, która zawiera już utworzony szablon urządzenia typu Automat do sprzedaży z chłodziarką. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś programistą dla deweloperów urządzeń przy użyciu dowolnego z tych urządzeń. |

> [!NOTE]
> Szablon **aplikacji w wersji zapoznawczej** jest obecnie dostępny tylko w regionach **Europy Północnej** i **środkowe stany USA** .

## <a name="view-your-iot-central-applications"></a>Wyświetlanie aplikacji IoT Central

Użyj polecenia cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) , aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj polecenia cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) , aby zaktualizować metadane aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj polecenia cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) , aby usunąć aplikację IoT Central. Na przykład:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu Azure PowerShell, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administruj swoją aplikacją](howto-administer.md)
