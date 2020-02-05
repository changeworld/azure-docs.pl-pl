---
title: Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central i zarządzania nią przy użyciu interfejsu wiersza polecenia. Możesz wyświetlać, modyfikować i usuwać aplikację przy użyciu interfejsu wiersza polecenia.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8526eb50faf300892c66ac186eac25adecf62231
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019030"
---
# <a name="manage-iot-central-from-azure-cli"></a>Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz uruchomić interfejs wiersza polecenia platformy Azure na komputerze lokalnym, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po uruchomieniu interfejsu wiersza polecenia platformy Azure lokalnie Użyj polecenia **AZ login** , aby zalogować się do platformy Azure przed podjęciem próby wykonania poleceń w tym artykule.

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj polecenia [AZ iotcentral App Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) , aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Przykład:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Te polecenia najpierw tworzą grupę zasobów w regionie Wschodnie stany USA dla aplikacji. W poniższej tabeli opisano parametry używane z poleceniem **AZ iotcentral App Create** :

| Parametr         | Opis |
| ----------------- | ----------- |
| resource-group    | Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w Twojej subskrypcji. |
| location          | Domyślnie to polecenie używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w regionach **Wschodnie stany USA**, **zachodnie stany USA**, **Europa Północna**lub **Europa Zachodnia** lub w **Australii** lub **Azja i Pacyfik** lokalizacje geograficzne. |
| name              | Nazwa aplikacji w Azure Portal. |
| poddomeny         | Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji jest https://mysubdomain.azureiotcentral.com. |
| sku               | Obecnie można użyć opcji **ST1** lub **ST2**. Zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz następującą tabelę: |
| Nazwa wyświetlana      | Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

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

## <a name="view-your-applications"></a>Wyświetl swoje aplikacje

Użyj polecenia [AZ iotcentral App list](/cli/azure/iotcentral/app#az-iotcentral-app-list) , aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj polecenia [AZ iotcentral App Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) , aby zaktualizować metadane aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj polecenia [AZ iotcentral App Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) , aby usunąć aplikację IoT Central. Przykład:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administruj swoją aplikacją](howto-administer.md)
