---
title: Konfigurowanie usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure
description: Szybki start — konfigurowanie usługi inicjowania obsługi administracyjnej usługi usługi Azure IoT Hub (DPS) przy użyciu interfejsu wiersza polecenia platformy Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ea1cae1f5a30d4cd76df39fec43f3818178fc213
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77484200"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Szybki start: konfigurowanie usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi Usługi Usługi IoT Hub za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki start zawiera szczegółowe informacje przy użyciu interfejsu wiersza polecenia platformy Azure do utworzenia centrum IoT hub i usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub oraz połączenia dwóch usług. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

> [!IMPORTANT]
> Zarówno centrum IoT hub, jak i usługa inicjowania obsługi administracyjnej utworzona w tym przewodniku Szybki start będą publicznie wykrywalne jako punkty końcowe DNS. Pamiętaj, że należy unikać wszelkich poufnych informacji, jeśli zdecydujesz się zmienić nazwy używane dla tych zasobów.
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

Poniższy przykład tworzy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus*. Nazwa centrum IoT musi być globalnie unikatowa na platformie Azure, więc możesz dodać unikatowy prefiks lub sufiks do przykładowej nazwy lub wybrać zupełnie nową nazwę. Upewnij się, że nazwa jest zgodna z odpowiednimi konwencjami nazewnictwa centrum IoT hub: powinna mieć długość od 3 do 50 znaków i może zawierać tylko wielkie lub małe litery alfanumeryczne lub łączniki ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Tworzenie usługi inicjowania obsługi administracyjnej urządzeń

Utwórz usługę inicjowania obsługi administracyjnej urządzeń za pomocą polecenia [az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) 

Poniższy przykład tworzy usługę inicjowania obsługi administracyjnej o nazwie *my-sample-dps* w lokalizacji *westus.* Należy również wybrać globalnie unikatową nazwę dla własnej usługi inicjowania obsługi administracyjnej. Upewnij się, że jest zgodna z odpowiednimi konwencjami nazewnictwa usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub: powinna mieć długość od 3 do 64 znaków i może zawierać tylko wielkie lub małe litery alfanumeryczne lub łączniki ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy usługę aprowizowania w lokalizacji Zachodnie stany USA. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach lokalizacje można określić w formacie jednego wyrazu lub wielu wyrazów; na przykład: westus, zachodnie stany USA, zachodnie stany USA itp. W wartości nie jest rozróżniana wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

Parametry połączenia centrum IoT są potrzebne do połączenia go z usługą aprowizacji urządzenia. Użyj polecenia [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string), aby pobrać parametry połączenia, i użyj jego danych wyjściowych, aby ustawić zmienną, która będzie używana podczas łączenia tych dwóch zasobów. 

W poniższym przykładzie ustawia *hubConnectionString* zmiennej do wartości ciągu połączenia dla klucza podstawowego `--policy-name` zasad *iothubowner* koncentratora (parametr może służyć do określenia innej zasady). Wymień *koncentrator my-sample-hub* dla unikatowej nazwy centrum IoT, którą wybrałeś wcześniej. Polecenie używa opcji [query](/cli/azure/query-azure-cli) i [output](/cli/azure/format-output-azure-cli#tsv-output-format) interfejsu wiersza polecenia platformy Azure, aby wyodrębnić parametry połączenia z danych wyjściowych polecenia.

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

Poniższy przykład łączy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus* i usługi inicjowania obsługi urządzeń o nazwie *my-sample-dps*. Wymień te nazwy dla unikatowych nazw centrum IoT hub i usługi inicjowania obsługi urządzeń wybranych wcześniej. Polecenie używa ciągu połączenia dla centrum IoT hub, który był przechowywany w *hubConnectionString* zmiennej w poprzednim kroku.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Polecenie może potrwać kilka minut.

## <a name="verify-the-provisioning-service"></a>Sprawdzanie usługi aprowizowania

Pobierz szczegóły dotyczące usługi aprowizowania za pomocą polecenia [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Poniższy przykład pobiera szczegółowe dane usługi aprowizowania o nazwie *my-sample-dps*. Wymioń tę nazwę na własną nazwę usługi inicjowania obsługi urządzeń.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Połączone centrum IoT jest wyświetlane w kolekcji *properties.iotHubs*.

![Weryfikowanie usługi inicjowania obsługi administracyjnej](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuacji, możesz użyć następujących poleceń, aby usunąć usługę aprowizowania, centrum IoT lub grupę zasobów i wszystkie jej zasoby. Zastąp nazwy zasobów zapisanych poniżej nazwami własnych zasobów.

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

W tym przewodniku Szybki start wdrożono centrum IoT hub i wystąpienie usługi inicjowania obsługi administracyjnej urządzeń i połączono te dwa zasoby. Aby dowiedzieć się, jak użyć tej konfiguracji do aprowizowania symulowanego urządzenia, przejdź do przewodnika Szybki start w celu utworzenia symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Szybki start w celu utworzenia symulowanego urządzenia](./quick-create-simulated-device.md)
