---
title: Jak zarządzać usługami Device Provisioning przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 i rozszerzenia usługi IoT | Microsoft Docs
description: Dowiedz się, jak zarządzać usługami Device Provisioning przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 i rozszerzenia usługi IoT
services: iot-dps
keywords: ''
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8e8bbf5808c11709a49f1cb6ebeba410837e5810
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Jak zarządzać usługami Device Provisioning przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 i rozszerzenia usługi IoT

[Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to międzyplatformowe narzędzie wiersza polecenia typu open-source służące do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Interfejs wiersza polecenia platformy Azure 2.0 jest dostępny dla systemów Windows, Linux i MacOS. Interfejs wiersza polecenia platformy Azure 2.0 umożliwia zarządzanie zasobami centrum Hub, wystąpieniami usługi Device Provisioning i połączonymi centrami po pierwszej instalacji.

Rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure 2.0 o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym samouczku najpierw wykonaj kroki konfigurowania interfejsu wiersza polecenia platformy Azure 2.0 i usługi rozszerzenia IoT. Następnie dowiesz się, jak uruchamiać polecenia interfejsu wiersza polecenia w celu wykonywania podstawowych operacji usługi Device Provisioning. 

## <a name="installation"></a>Instalacja 

### <a name="step-1---install-python"></a>Krok 1 — instalowanie języka Python

Wymagany jest język [Python 2.7x lub Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Krok 2 — instalowanie interfejsu wiersza polecenia platformy Azure 2.0

Postępuj zgodnie z [instrukcjami instalacji](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), aby skonfigurować interfejs wiersza polecenia platformy Azure 2.0 w swoim środowisku. Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Krok 3 — instalowanie rozszerzenia IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-cli-iot-ext`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-cli-iot-ext` w celu wyświetlenia szczegółów rozszerzenia IoT. Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Podstawowe operacje usługi Device Provisioning
W przykładzie pokazano, jak zalogować się do konta platformy Azure, utworzyć grupę zasobów platformy Azure (kontener zawierający powiązane zasoby rozwiązania platformy Azure), utworzyć centrum IoT Hub, utworzyć usługę Device Provisioning, wyświetlić listę istniejących usług Device Provisioning i utworzyć połączone centrum IoT Hub przy użyciu poleceń interfejsu wiersza polecenia. 

Przed rozpoczęciem wykonaj opisane wcześniej kroki instalacji. Jeśli nie masz jeszcze konta platformy Azure, możesz dzisiaj [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Logowanie do konta platformy Azure
  
    az login

![logowanie][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Tworzenie grupy zasobów IoTHubBlogDemo w regionie eastus

    az group create -l eastus -n IoTHubBlogDemo

![Tworzenie grupy zasobów][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Tworzenie dwóch usługi Device Provisioning

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Tworzenie usługi DPS][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Wyświetlanie listy istniejących usług Device Provisioning w ramach tej grupy zasobów

    az iot dps list --resource-group IoTHubBlogDemo

![Wyświetlanie listy usług DPS][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Tworzenie centrum IoT Hub blogDemoHub w ramach nowo utworzonej grupy zasobów

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Tworzenie centrum IoT Hub][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Łączenie centrum IoT Hub z usługą Device Provisioning

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Łączenie centrum Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować wiele urządzeń w obrębie centrów ze zrównoważonym obciążeniem. 

> [!div class="nextstepaction"]
> [Aprowizuj urządzenia w centrach IoT Hub ze zrównoważonym obciążeniem](./tutorial-provision-multiple-hubs.md)
