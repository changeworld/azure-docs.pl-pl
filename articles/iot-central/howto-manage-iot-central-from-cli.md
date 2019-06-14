---
title: Zarządzanie IoT Central z wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie IoT Central z wiersza polecenia platformy Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151954"
---
# <a name="manage-iot-central-from-azure-cli"></a>Zarządzanie IoT Central z wiersza polecenia platformy Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Zamiast tworzenia aplikacji i zarządzaniem nimi IoT Central z IoT Central [Menedżer aplikacji](https://aka.ms/iotcentral) strony, można użyć [wiersza polecenia platformy Azure](/cli/azure/) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli chcesz uruchomić z wiersza polecenia platformy Azure na komputerze lokalnym, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po uruchomieniu wiersza polecenia platformy Azure lokalnie, użyj **az login** polecenie, aby zalogować się do platformy Azure, przed podjęciem próby wykonania polecenia, w tym artykule.

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj [tworzenie aplikacji iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-create) polecenia do tworzenia aplikacji IoT Central w Twojej subskrypcji platformy Azure. Na przykład:

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

Te polecenia najpierw utworzyć grupę zasobów w regionie wschodnim regionie USA dla aplikacji. W poniższej tabeli opisano parametry używane w **tworzenie aplikacji iotcentral az** polecenia:

| Parametr         | Opis |
| ----------------- | ----------- |
| resource-group    | Grupa zasobów, który zawiera aplikację. Ta grupa zasobów musi już istnieć w subskrypcji. |
| location          | Domyślnie to polecenie używa lokalizacji z grupy zasobów. Obecnie można tworzyć aplikację IoT Central w **wschodnie stany USA**, **zachodnie stany USA**, **Europa Północna**, lub **Europa Zachodnia** regionów. |
| name              | Nazwa aplikacji w witrynie Azure portal. |
| Poddomena         | Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji jest https://mysubdomain.azureiotcentral.com. |
| sku               | Obecnie jest to jedyna wartość **S1** (warstwa standardowa). Zobacz [cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji zobacz w poniższej tabeli: |
| display-name      | Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

**Szablony aplikacji**

| Nazwa szablonu            | Opis |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |
| iotc-demo@1.0.0          | Tworzy aplikację, która zawiera już utworzony szablon urządzenia typu Automat do sprzedaży z chłodziarką. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś deweloperem urządzenia eksperymentowanie z dowolnego z tych urządzeń. |

## <a name="view-your-applications"></a>Wyświetl swoje aplikacje

Użyj [listy aplikacji iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-list) polecenie, aby wyświetlić aplikacje IoT Central oraz metadanych.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj [aktualizacji aplikacji iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-update) polecenia w celu zaktualizowania metadanych aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj [az iotcentral aplikacji Usuń](/cli/azure/iotcentral/app#az-iotcentral-app-delete) polecenie, aby usunąć aplikację IoT Central. Na przykład:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak zarządzać aplikacjami usługi Azure IoT Central z wiersza polecenia platformy Azure, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)
