---
title: Zarządzanie usługą inicjowania obsługi administracyjnej urządzeń usługi IoT Hub przy użyciu rozszerzenia interfejsu wiersza polecenia platformy Azure & IoT
description: Dowiedz się, jak zarządzać usługą aprowizacji urządzeń usługi DPS za pomocą interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674511"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Jak zarządzać usługą inicjowania obsługi administracyjnej urządzeń usługi IoT Hub i rozszerzenieM IoT Hub

[Narzędzie interfejsu wiersza](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) polecenia platformy Azure to narzędzie wiersza polecenia platformy open source do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Interfejs wiersza polecenia platformy Azure jest dostępny w systemach Windows, Linux i MacOS. Narzędzie interfejsu wiersza polecenia platformy Azure umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi inicjowania obsługi administracyjnej urządzeń i połączonymi koncentratorami po wyjęciu z pudełka.

Rozszerzenie IoT wzbogaca interfejs wiersza polecenia platformy Azure o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym samouczku należy najpierw wykonać kroki, aby skonfigurować interfejsu wiersza polecenia platformy Azure i rozszerzenie IoT. Następnie dowiesz się, jak uruchamiać polecenia interfejsu wiersza polecenia w celu wykonywania podstawowych operacji usługi inicjowania obsługi administracyjnej urządzeń. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Instalacja 

### <a name="install-python"></a>Instalacja języka Python

Wymagany jest język [Python 2.7x lub Python 3.x](https://www.python.org/downloads/).

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Postępuj zgodnie z [instrukcją instalacji,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby skonfigurować platformę Azure CLI w swoim środowisku. Co najmniej wersja interfejsu wiersza polecenia platformy Azure musi mieć wartość 2.0.70 lub nowszy. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Instalowanie rozszerzenia IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-iot` w celu wyświetlenia szczegółów rozszerzenia IoT. Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Operacje usługi aprowizacji urządzeń podstawowych

W przykładzie pokazano, jak zalogować się do konta platformy Azure, utworzyć grupę zasobów platformy Azure (kontener, który przechowuje powiązane zasoby dla rozwiązania platformy Azure), utworzyć Centrum IoT, utworzyć usługę inicjowania obsługi urządzeń, wyświetlić listę istniejących usług inicjowania obsługi administracyjnej urządzeń i utworzyć połączony koncentrator IoT z poleceniami interfejsu wiersza polecenia. 

Przed rozpoczęciem wykonaj opisane wcześniej kroki instalacji. Jeśli nie masz jeszcze konta platformy Azure, możesz dzisiaj [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Zaloguj się do konta platformy Azure
  
    az login

![logowanie](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Tworzenie grupy zasobów IoTHubBlogDemo w eastus

    az group create -l eastus -n IoTHubBlogDemo

![Tworzenie grupy zasobów](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Tworzenie dwóch usług inicjowania obsługi urządzeń

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Tworzenie usługi inicjowania obsługi administracyjnej urządzeń](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Wyliczenie wszystkich istniejących usług inicjowania obsługi urządzeń w ramach tej grupy zasobów

    az iot dps list --resource-group IoTHubBlogDemo

![Lista usług inicjowania obsługi administracyjnej urządzeń](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Tworzenie bloga Usługi IoT HubDemoHub w nowo utworzonej grupie zasobów

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Tworzenie centrum IoT Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Łączenie jednego istniejącego centrum IoT Hub z usługą inicjowania obsługi urządzeń

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Łączenie centrum Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować wiele urządzeń w obrębie centrów ze zrównoważonym obciążeniem. 

> [!div class="nextstepaction"]
> [Aprowizuj urządzenia w centrach IoT Hub ze zrównoważonym obciążeniem](./tutorial-provision-multiple-hubs.md)
