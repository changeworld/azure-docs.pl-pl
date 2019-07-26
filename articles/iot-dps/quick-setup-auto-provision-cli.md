---
title: Konfigurowanie usługi Device Provisioning przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — Konfigurowanie usługi Azure IoT Hub Device Provisioning przy użyciu interfejsu wiersza polecenia platformy Azure
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3062fb640985498ba35e23f6310828a2bd59bfed
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "60363720"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Konfigurowanie usługi IoT Hub Device Provisioning Service przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start szczegółowo omówiono używanie interfejsu wiersza polecenia platformy Azure do utworzenia centrum IoT i usługi IoT Hub Device Provisioning oraz do połączenia tych dwóch usług ze sobą. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Zarówno centrum IoT, jak i aprowizowanie usługi utworzonej w tym przewodniku Szybki start będzie można publicznie wykryć jako punkty końcowe DNS. Pamiętaj, że należy unikać wszelkich poufnych informacji, jeśli zdecydujesz się zmienić nazwy używane dla tych zasobów.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

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

Poniższy przykład tworzy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Tworzenie usługi aprowizowania

Utwórz usługę aprowizowania za pomocą polecenia [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create). 

Poniższy przykład obejmuje tworzenie usługi aprowizowania o nazwie *my-sample-dps* w lokalizacji *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy usługę aprowizowania w lokalizacji Zachodnie stany USA. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach można określić lokalizacje za pomocą jednego słowa lub w formacie wielu słów, na przykład: westus, West US, WEST US itp. W wartości nie jest uwzględniana wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

Parametry połączenia centrum IoT są potrzebne do połączenia go z usługą aprowizacji urządzenia. Użyj polecenia [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string), aby pobrać parametry połączenia, i użyj jego danych wyjściowych, aby ustawić zmienną, która będzie używana podczas łączenia tych dwóch zasobów. 

Poniższy przykład ustawia zmienną *hubConnectionString* na wartość parametrów połączenia dla klucza podstawowego zasad *iothubowner* centrum. Za pomocą parametru `--policy-name` możesz określić różne zasady. Polecenie używa opcji [query](/cli/azure/query-azure-cli) i [output](/cli/azure/format-output-azure-cli#tsv-output-format) interfejsu wiersza polecenia platformy Azure, aby wyodrębnić parametry połączenia z danych wyjściowych polecenia.

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

Poniższy przykład łączy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus* z usługą aprowizacji urządzenia o nazwie *my-sample-dps*. Zostały użyte parametry połączenia dla centrum *my-sample-hub* zapisane w zmiennej *hubConnectionString* w poprzednim kroku.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Sprawdzanie usługi aprowizowania

Pobierz szczegóły dotyczące usługi aprowizowania za pomocą polecenia [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Poniższy przykład pobiera szczegółowe dane usługi aprowizowania o nazwie *my-sample-dps*. Połączone centrum IoT jest wyświetlane w kolekcji *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuacji, możesz użyć następujących poleceń, aby usunąć usługę aprowizowania, centrum IoT lub grupę zasobów i wszystkie jej zasoby.

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

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start zostały wdrożone centrum IoT i wystąpienie usługi Device Provisioning Service, po czym te dwa zasoby zostały połączone ze sobą. Aby dowiedzieć się, jak za pomocą tej konfiguracji zaaprowizować symulowane urządzenie, przejdź do przewodnika Szybki start omawiającego tworzenie symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start z tworzeniem symulowanego urządzenia](./quick-create-simulated-device.md)

