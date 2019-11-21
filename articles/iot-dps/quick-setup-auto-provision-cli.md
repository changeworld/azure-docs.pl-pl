---
title: Set up Azure IoT Hub Device Provisioning Service using Azure CLI
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service using Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6406929c3abc3612da2c27edc45e10fd84883d73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228547"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with Azure CLI

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. This quickstart details using the Azure CLI to create an IoT hub and an IoT Hub Device Provisioning Service, and to link the two services together. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Both the IoT hub and the provisioning service you create in this quickstart will be publicly discoverable as DNS endpoints. Pamiętaj, że należy unikać wszelkich poufnych informacji, jeśli zdecydujesz się zmienić nazwy używane dla tych zasobów.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *my-sample-resource-group* w lokalizacji *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy grupę zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając polecenie `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Utwórz centrum IoT za pomocą polecenia [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

Poniższy przykład tworzy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus*. An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Create a Device Provisioning Service

Create a Device Provisioning Service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. 

The following example creates a provisioning service named *my-sample-dps* in the *westus* location. You will also need to choose a globally unique name for your own provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy usługę aprowizowania w lokalizacji Zachodnie stany USA. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

Parametry połączenia centrum IoT są potrzebne do połączenia go z usługą aprowizacji urządzenia. Użyj polecenia [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string), aby pobrać parametry połączenia, i użyj jego danych wyjściowych, aby ustawić zmienną, która będzie używana podczas łączenia tych dwóch zasobów. 

The following example sets the *hubConnectionString* variable to the value of the connection string for the primary key of the hub's *iothubowner* policy (the `--policy-name` parameter can be used to specify a different policy). Trade out *my-sample-hub* for the unique IoT hub name you chose earlier. Polecenie używa opcji [query](/cli/azure/query-azure-cli) i [output](/cli/azure/format-output-azure-cli#tsv-output-format) interfejsu wiersza polecenia platformy Azure, aby wyodrębnić parametry połączenia z danych wyjściowych polecenia.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Aby wyświetlić parametry połączenia, możesz użyć polecenia `echo`.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Te dwa polecenia są prawidłowe dla hosta z powłoką Bash. Jeśli używasz lokalnej powłoki systemu Windows/CMD lub hosta programu PowerShell, musisz zmodyfikować te polecenia, aby stosować prawidłową składnię dla tego środowiska.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Łączenie centrum IoT z usługą aprowizowania

Połącz centrum IoT i usługę aprowizowania za pomocą polecenia [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

The following example links an IoT hub named *my-sample-hub* in the *westus* location and a Device Provisioning Service named *my-sample-dps*. Trade out these names for the unique IoT hub and Device Provisioning Service names you chose earlier. The command uses the connection string for your IoT hub that was stored in the *hubConnectionString* variable in the previous step.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

The command may take a few minutes to complete.

## <a name="verify-the-provisioning-service"></a>Sprawdzanie usługi aprowizowania

Pobierz szczegóły dotyczące usługi aprowizowania za pomocą polecenia [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Poniższy przykład pobiera szczegółowe dane usługi aprowizowania o nazwie *my-sample-dps*. Trade out this name for your own Device Provisioning Service name.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Połączone centrum IoT jest wyświetlane w kolekcji *properties.iotHubs*.

![Verify Provisioning Service](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuacji, możesz użyć następujących poleceń, aby usunąć usługę aprowizowania, centrum IoT lub grupę zasobów i wszystkie jej zasoby. Replace the names of the resources written below with the names of your own resources.

Aby usunąć usługę aprowizowania, uruchom polecenie [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Aby usunąć centrum IoT, uruchom polecenie [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom polecenie [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Następne kroki

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
