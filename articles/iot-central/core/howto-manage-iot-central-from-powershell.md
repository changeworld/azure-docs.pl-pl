---
title: Zarządzaj IoT Central z Azure PowerShell | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central z Azure PowerShell i zarządzania nimi.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: a95b59c6cc0d486c1d4b10f39d0d272dd4b34f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018996"
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

Użyj polecenia cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) , aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Przykład:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skrypt najpierw tworzy grupę zasobów w regionie Wschodnie stany USA dla aplikacji. W poniższej tabeli opisano parametry używane z poleceniem **New-AzIotCentralApp** :

|Parametr         |Opis |
|------------------|------------|
|ResourceGroupName |Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w Twojej subskrypcji. |
|Lokalizacja |Domyślnie to polecenie cmdlet używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w regionach **Wschodnie stany USA**, **zachodnie stany USA**, **Europa Północna**lub **Europa Zachodnia** lub w **Australii** lub **Azja i Pacyfik** lokalizacje geograficzne.  |
|Nazwa              |Nazwa aplikacji w Azure Portal. |
|poddomeny         |Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji jest https://mysubdomain.azureiotcentral.com. |
|Jednostka SKU               |Obecnie można użyć opcji **ST1** lub **ST2**. Zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz następującą tabelę: |
|DisplayName       |Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

**Szablony aplikacji**

| Nazwa szablonu            | Opis |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń.
| iotc-pnp-preview@1.0.0   | Tworzy pustą aplikację Plug and Play (wersja zapoznawcza) do zapełniania własnymi szablonami urządzeń i urządzeniami. |
| iotc-condition@1.0.0     | Tworzy aplikację z szablonem monitorowania stanu w sklepie. Ten szablon służy do łączenia i monitorowania środowiska magazynu. |
| iotc-consumption@1.0.0   | Tworzy aplikację z szablonem monitorowania zużycia wody. Ten szablon służy do monitorowania i kontrolowania przepływu wody. |
| iotc-distribution@1.0.0  | Tworzy aplikację z szablonem dystrybucji cyfrowej. Ten szablon umożliwia zwiększenie wydajności produkcji magazynu przez zasoby i akcje kluczy digitalizing. |
| iotc-inventory@1.0.0     | Tworzy aplikację z szablonem zarządzania magazynem inteligentnym. Ten szablon służy do automatyzowania otrzymywania, przenoszenia produktów, zliczania cykli i śledzenia czujników. |
| iotc-logistics@1.0.0     | Tworzy aplikację z połączonym szablonem logistyki. Ten szablon służy do śledzenia wysyłki w czasie rzeczywistym między powietrzem, wodą i ziemią oraz monitorowaniem lokalizacji i warunków. |
| iotc-meter@1.0.0         | Tworzy aplikację z szablonem monitorowania z użyciem miernika inteligentnego. Ten szablon służy do monitorowania zużycia energii, stanu sieci i identyfikowania trendów pozwalających ulepszyć obsługę klienta i inteligentne zarządzanie pomiarami.  |
| iotc-patient@1.0.0       | Tworzy aplikację z szablonem monitorowania ciągłego pacjenta. Użyj tego szablonu, aby zwiększyć opiekę pacjenta, ponowne przyjmowanie i zarządzanie chorobami. |
| iotc-power@1.0.0         | Tworzy aplikację z szablonem monitorowania panelu słonecznym. Ten szablon służy do monitorowania stanu panelu słonecznego i trendów generowania energii. |
| iotc-quality@1.0.0       | Tworzy aplikację z szablonem monitorowania jakości wody. Ten szablon służy do cyfrowego monitorowania jakości wody.|
| iotc-store@1.0.0         | Tworzy aplikację z szablonem analizy w sklepie — wyewidencjonowywanie. Ten szablon służy do monitorowania i zarządzania przepływem wyewidencjonowania w sklepie. |
| iotc-waste@1.0.0         | Tworzy aplikację z połączonym szablonem zarządzania odpadami. Ten szablon służy do monitorowania pojemników odpadów i operatorów pól wysyłania. |

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

Użyj polecenia cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) , aby usunąć aplikację IoT Central. Przykład:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu Azure PowerShell, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administruj swoją aplikacją](howto-administer.md)
