---
title: Jak zarządzać IoT Hub Device Provisioning Service za pomocą wiersza polecenia platformy Azure i rozszerzenia usługi IoT | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać IoT Hub Device Provisioning Service przy użyciu wiersza polecenia platformy Azure i rozszerzenia IoT
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 59d2277bd99fac1e8357c1b0d7336ca7451bf8dc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122877"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Jak zarządzać IoT Hub Device Provisioning Service za pomocą wiersza polecenia platformy Azure i rozszerzenia usługi IoT

[Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) jest typu open-source wielu platformach narzędzia wiersza polecenia do zarządzania zasobami platformy Azure, takich jak usługi IoT Edge. Wiersza polecenia platformy Azure jest dostępna w Windows, Linux i MacOS. Wiersza polecenia platformy Azure umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi Device Provisioning i połączonymi centrami po gotowych.

Rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure przy użyciu funkcji, takich jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym samouczku najpierw wykonaj kroki konfigurowania interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie dowiesz się, jak uruchamiać polecenia interfejsu wiersza polecenia, aby wykonywać podstawowe operacje usługi Device Provisioning. 

## <a name="installation"></a>Instalacja 

### <a name="step-1---install-python"></a>Krok 1 — instalowanie języka Python

Wymagany jest język [Python 2.7x lub Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-the-azure-cli"></a>Krok 2 — Instalowanie interfejsu wiersza polecenia platformy Azure

Postępuj zgodnie z [instrukcji dotyczących instalacji](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) konfigurowania interfejsu wiersza polecenia platformy Azure w danym środowisku. Co najmniej z wiersza polecenia platformy Azure musi być w wersji 2.0.24 lub nowszej. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Krok 3 — instalowanie rozszerzenia IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-cli-iot-ext`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-cli-iot-ext` w celu wyświetlenia szczegółów rozszerzenia IoT. Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Podstawowe operacje usługi Device Provisioning
W przykładzie pokazano jak zalogować się do konta platformy Azure, Utwórz grupę zasobów platformy Azure (kontener, który zawiera powiązane zasoby rozwiązania platformy Azure), tworzenie Centrum IoT, utworzyć usługę Device Provisioning, wyświetlić listę istniejących usług Device Provisioning i Utwórz połączone Centrum IoT przy użyciu interfejsu wiersza polecenia. 

Przed rozpoczęciem wykonaj opisane wcześniej kroki instalacji. Jeśli nie masz jeszcze konta platformy Azure, możesz dzisiaj [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Logowanie do konta platformy Azure
  
    az login

![logowanie][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Tworzenie grupy zasobów IoTHubBlogDemo w regionie eastus

    az group create -l eastus -n IoTHubBlogDemo

![Tworzenie grupy zasobów][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Tworzenie dwóch usługi Device Provisioning

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Tworzenie usługi Device Provisioning][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Lista wszystkich istniejących Device Provisioning usług w tej grupie zasobów

    az iot dps list --resource-group IoTHubBlogDemo

![Lista usługi Device Provisioning][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Tworzenie centrum IoT Hub blogDemoHub w ramach nowo utworzonej grupy zasobów

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Tworzenie centrum IoT Hub][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Łączenie jednego centrum IoT Hub z usługą Device Provisioning

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Łączenie centrum Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Kolejne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować wiele urządzeń w obrębie centrów ze zrównoważonym obciążeniem. 

> [!div class="nextstepaction"]
> [Aprowizuj urządzenia w centrach IoT Hub ze zrównoważonym obciążeniem](./tutorial-provision-multiple-hubs.md)
