---
title: Tworzenie Centrum IoT przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum Azure IoT hub przy użyciu wiersza polecenia platformy Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 78ea9071f220b2a78c6d9260d47145f22284d760
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66166295"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Tworzenie Centrum IoT przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule przedstawiono sposób tworzenia Centrum IoT przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć Instruktaż, potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawianie konta platformy Azure

Jeśli korzystasz z wiersza polecenia platformy Azure lokalnie, zamiast korzystać z usługi Cloud Shell, musisz zalogować się do konta platformy Azure.

W wierszu polecenia Uruchom [polecenie logowania](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Użyj wiersza polecenia platformy Azure, Utwórz grupę zasobów, a następnie dodaj Centrum IoT hub.

1. Podczas tworzenia Centrum IoT hub, możesz ją utworzyć w grupie zasobów. Użyj istniejącej grupy zasobów albo uruchom następujące polecenie [polecenie, aby utworzyć grupę zasobów](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Możesz wyświetlić listę dostępnych lokalizacji, uruchamiając następujące polecenie: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Uruchom następujące polecenie [polecenie, aby utworzyć Centrum IoT hub](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) w swojej grupie zasobów za pomocą globalnie unikatową nazwę Centrum IoT:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Poprzednie polecenie tworzy usługi IoT hub w wersji S1 dla którego stosowana jest stawka warstwy cenowej. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Usunąć Centrum IoT Hub

Można użyć wiersza polecenia platformy Azure, aby [usunąć pojedynczy zasób](https://docs.microsoft.com/cli/azure/resource), takich jak usługa IoT hub lub Usuń grupę zasobów i wszystkie jej zasoby, łącznie z dowolnym centra IoT Hub.

Aby [usunąć Centrum IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Aby [Usuń grupę zasobów](https://docs.microsoft.com/cli/azure/group#az-group-delete) i wszystkie jej zasoby, uruchom następujące polecenie:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o korzystaniu z usługi IoT hub, zobacz następujące artykuły:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Zarządzanie usługą IoT Hub przy użyciu witryny Azure portal](iot-hub-create-through-portal.md)
