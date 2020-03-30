---
title: Zarządzanie usługą IoT Central z interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central przy użyciu interfejsu wiersza polecenia i zarządzania nią. Można wyświetlać, modyfikować i usuwać aplikację przy użyciu interfejsu wiersza polecenia.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365529"
---
# <a name="manage-iot-central-from-azure-cli"></a>Zarządzanie usługą IoT Central z interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie [Azure IoT Central manager,](https://aka.ms/iotcentral) można używać [interfejsu wiersza polecenia platformy Azure](/cli/azure/) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz uruchomić platformę Azure CLI na komputerze lokalnym, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po uruchomieniu interfejsu wiersza polecenia platformy Azure lokalnie, użyj polecenia **logowania az,** aby zalogować się na platformie Azure przed wypróbowaniem poleceń w tym artykule.

> [!TIP]
> Jeśli chcesz uruchomić polecenia interfejsu wiersza polecenia w innej subskrypcji platformy Azure, zobacz [Zmienianie aktywnej subskrypcji](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Polecenia w tym artykule są częścią rozszerzenia interfejsu wiersza polecenia **azure-iot.** Uruchom następujące polecenie, aby zainstalować rozszerzenie:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj polecenia [tworzenia aplikacji az iotcentral,](/cli/azure/iotcentral/app#az-iotcentral-app-create) aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Przykład:

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
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Te polecenia najpierw utworzyć grupę zasobów we wschodnim regionie STANÓW Zjednoczonych dla aplikacji. W poniższej tabeli opisano parametry używane za pomocą polecenia **tworzenia aplikacji az iotcentral:**

| Parametr         | Opis |
| ----------------- | ----------- |
| resource-group    | Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w ramach subskrypcji. |
| location          | Domyślnie to polecenie używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w regionach **Australia,** **Azja i Pacyfik,** **Europa,** **Stany Zjednoczone,** **Wielka Brytania**i **Japonia.** |
| name              | Nazwa aplikacji w witrynie Azure portal. |
| Poddomeny         | Poddomena w adresie URL aplikacji. W przykładzie adres URL `https://mysubdomain.azureiotcentral.com`aplikacji to . |
| sku               | Obecnie można użyć **st1** lub **ST2**. Zobacz [ceny usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz poniższą tabelę. |
| nazwa wyświetlana      | Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Wyświetl swoje aplikacje

Użyj polecenia [az iotcentral app list,](/cli/azure/iotcentral/app#az-iotcentral-app-list) aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj polecenia [az iotcentral app update,](/cli/azure/iotcentral/app#az-iotcentral-app-update) aby zaktualizować metadane aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj polecenia [az iotcentral app delete,](/cli/azure/iotcentral/app#az-iotcentral-app-delete) aby usunąć aplikację IoT Central. Przykład:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać aplikacjami Azure IoT Central z interfejsu wiersza polecenia platformy Azure, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)
