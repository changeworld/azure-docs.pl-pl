---
title: Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Zarządzaj IoT Central z poziomu interfejsu wiersza polecenia platformy Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 421d41f95eff0ba0fdbca02c588d4a9a0c461a84
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381057"
---
# <a name="manage-iot-central-from-azure-cli"></a>Zarządzanie IoT Central z poziomu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Te polecenia najpierw tworzą grupę zasobów w regionie Wschodnie stany USA dla aplikacji. W poniższej tabeli opisano parametry używane z poleceniem **AZ iotcentral App Create** :

| Parametr         | Opis |
| ----------------- | ----------- |
| resource-group    | Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w Twojej subskrypcji. |
| location          | Domyślnie to polecenie używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w regionach **Wschodnie stany USA**, **zachodnie stany USA**, **Europa Północna**lub **Europa Zachodnia** . |
| name              | Nazwa aplikacji w Azure Portal. |
| poddomeny         | Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji to https://mysubdomain.azureiotcentral.com. |
| sku               | Obecnie jedyną wartością jest **S1** (warstwa standardowa). Zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz następującą tabelę: |
| Nazwa wyświetlana      | Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

**Szablony aplikacji**

| Nazwa szablonu            | Opis |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |
| iotc-demo@1.0.0          | Tworzy aplikację, która zawiera już utworzony szablon urządzenia typu Automat do sprzedaży z chłodziarką. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś programistą dla deweloperów urządzeń przy użyciu dowolnego z tych urządzeń. |

> [!NOTE]
> Szablon **aplikacji w wersji zapoznawczej** jest obecnie dostępny tylko w regionach **Europy Północnej** i **środkowe stany USA** .

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
