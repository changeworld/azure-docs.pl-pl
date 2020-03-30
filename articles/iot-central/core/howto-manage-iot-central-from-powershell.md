---
title: Zarządzanie usługą IoT Central z usługi Azure PowerShell | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central z programu Azure PowerShell i zarządzania nimi.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 42d853c9cf53c1c6921fbd1816ec2298c9c3583e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365541"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Zarządzanie usługą IoT Central z programu Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie [Azure IoT Central manager,](https://aka.ms/iotcentral) można używać [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz uruchomić program Azure PowerShell na komputerze lokalnym, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po uruchomieniu programu Azure PowerShell lokalnie, użyj polecenia cmdlet **Connect-AzAccount,** aby zalogować się na platformie Azure przed wypróbowaniem poleceń cmdlet w tym artykule.

> [!TIP]
> Jeśli chcesz uruchomić polecenia programu PowerShell w innej subskrypcji platformy Azure, zobacz [Zmienianie aktywnej subskrypcji](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instalowanie modułu IoT Central

Uruchom następujące polecenie, aby sprawdzić, czy [moduł IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) jest zainstalowany w środowisku programu PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Jeśli lista zainstalowanych modułów nie zawiera **az.iotCentral,** uruchom następujące polecenie:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj polecenia cmdlet [New-AzIotCentralApp,](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Przykład:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skrypt najpierw tworzy grupę zasobów we wschodnim regionie STANÓW Zjednoczonych dla aplikacji. W poniższej tabeli opisano parametry używane za pomocą polecenia **New-AzIotCentralApp:**

|Parametr         |Opis |
|------------------|------------|
|ResourceGroupName |Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w ramach subskrypcji. |
|Lokalizacja |Domyślnie to polecenie cmdlet używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w regionach **Australia,** **Azja i Pacyfik,** **Europa,** **Stany Zjednoczone,** **Wielka Brytania**i **Japonia.** |
|Nazwa              |Nazwa aplikacji w witrynie Azure portal. |
|Poddomena         |Poddomena w adresie URL aplikacji. W przykładzie adres URL `https://mysubdomain.azureiotcentral.com`aplikacji to . |
|SKU               |Obecnie można użyć **st1** lub **ST2**. Zobacz [ceny usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz poniższą tabelę. |
|DisplayName       |Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Wyświetlanie aplikacji IoT Central

Użyj polecenia cmdlet [Get-AzIotCentralApp,](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Polecenie cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) służy do aktualizowania metadanych aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj polecenia cmdlet [Remove-AzIotCentralApp,](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) aby usunąć aplikację IoT Central. Przykład:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać aplikacjami Usługi Azure IoT Central z usługi Azure PowerShell, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)
