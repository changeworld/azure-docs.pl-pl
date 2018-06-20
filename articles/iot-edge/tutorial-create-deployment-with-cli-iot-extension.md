---
title: Wdrażanie modułów na urządzeniach usługi IoT Edge przy użyciu rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0 | Microsoft Docs
description: Wdrażanie modułów na urządzeniu usługi IoT Edge przy użyciu rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0
author: chrissie926
manager: ''
ms.author: menchi
ms.date: 03/02/2018
ms.topic: tutorial
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
md.custom: mvc
ms.openlocfilehash: deee54fe5d11d6d1cf5485357f853b1cb078f96d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631585"
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Wdrażanie modułów na urządzeniu usługi IoT Edge przy użyciu rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0

[Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to międzyplatformowe narzędzie wiersza polecenia typu open-source służące do zarządzania zasobami platformy Azure, takimi jak usługa IoT Edge. Interfejs wiersza polecenia platformy Azure 2.0 jest dostępny dla systemów Windows, Linux i MacOS.

Interfejs wiersza polecenia platformy Azure 2.0 umożliwia zarządzanie zasobami centrum Hub, wystąpieniami usługi Device Provisioning i połączonymi centrami po pierwszej instalacji. Nowe rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure 2.0 o funkcje, takie jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

W tym artykule skonfigurujesz interfejs wiersza polecenia platformy Azure w wersji 2.0 i rozszerzenie IoT. Następnie poznasz sposób wdrażania modułów na urządzeniu usługi IoT Edge przy użyciu dostępnych poleceń interfejsu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure. Jeśli go jeszcze nie masz, możesz już dziś [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?v=17.39a). 

* [Python 2.7x lub Python 3.x](https://www.python.org/downloads/).

* Zainstalowany w środowisku [interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Potrzebujesz co najmniej interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.24 lub nowszej. Użyj polecenia `az –-version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack. Prostym sposobem instalowania w systemie Windows jest pobranie i zainstalowanie instalatora [MSI](https://aka.ms/InstallAzureCliWindows).

* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Uruchom polecenie `az extension add --name azure-cli-iot-ext`. 
   2. Po zakończeniu instalacji użyj polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-cli-iot-ext` w celu wyświetlenia szczegółów rozszerzenia IoT.
   3. Aby usunąć rozszerzenie, użyj polecenia `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge
Ten artykuł zawiera instrukcje dotyczące tworzenia wdrożenia usługi IoT Edge. W przykładzie pokazano, jak zalogować się do konta platformy Azure, utworzyć grupę zasobów platformy Azure (kontener zawierający powiązane zasoby rozwiązania platformy Azure), utworzyć centrum IoT Hub, utworzyć trzy tożsamości urządzeń usługi IoT Edge, ustawić tagi, a następnie utworzyć wdrożenie usługi IoT Edge ukierunkowane na te urządzenia. 

Zaloguj się do konta platformy Azure. Po wprowadzeniu poniższego polecenia logowania zostanie wyświetlony monit o zalogowanie się przy użyciu jednorazowego kodu w przeglądarce internetowej: 

   ```cli
   az login
   ```

Utwórz nową grupę zasobów o nazwie **IoTHubCLI** w regionie Wschodnie stany USA: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Tworzenie grupy zasobów][2]

Utwórz centrum IoT o nazwie **CLIDemoHub** w nowo utworzonej grupie zasobów:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Do każdej subskrypcji jest przydzielone jedno bezpłatne centrum IoT. Aby utworzyć bezpłatne centrum za pomocą polecenia interfejsu wiersza polecenia, zastąp wartość jednostki SKU parametrem `--sku F1`. Jeśli masz już bezpłatne centrum w ramach swojej subskrypcji, podczas próby utworzenia drugiego centrum zostanie wyświetlony komunikat o błędzie. 

Utwórz urządzenie usługi IoT Edge:

   ```cli
   az iot hub device-identity create --device-id edge001 --hub-name CLIDemoHub --edge-enabled
   ```

   ![Tworzenie urządzenia usługi IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Utwórz szablon wdrażania w formacie JSON i zapisz go lokalnie jako plik txt. Ścieżkę do tego pliku będzie trzeba podać podczas uruchamiania polecenia apply-configuration.

Szablony wdrażania w formacie JSON powinny zawsze zawierać dwa moduły systemowe: edgeAgent i edgeHub. Za pomocą tego pliku można wdrożyć na urządzeniu usługi IoT Edge dodatkowe moduły oprócz dwóch wymienionych. Użyj następującego przykładu, aby skonfigurować urządzenie usługi IoT Edge z jednym modułem tempSensor:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": ""
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {},
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

Zastosuj konfigurację do urządzenia usługi IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Wyświetl moduły znajdujące się na urządzeniu usługi IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Wyświetlanie listy modułów][6]

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [użyć urządzenia usługi IoT Edge jako bramy](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

