---
title: Wdrażanie usługi Azure Machine Learning za pomocą usługi Azure IoT Edge | Microsoft Docs
description: Wdrażanie usługi Azure Machine Learning jako modułu na urządzeniu usługi Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 248bc97c214c013d10f1839201ce2f572cb854ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631177"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Wdrażanie usługi Azure Machine Learning jako modułu usługi IoT Edge — wersja zapoznawcza

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek zawiera instrukcje dotyczące wdrażania modułu usługi Azure Machine Learning służącego do przewidywania awarii urządzenia na podstawie danych czujników na symulowanym urządzeniu usługi IoT Edge utworzonym przy użyciu informacji z samouczków dotyczących [wdrażania usługi Azure IoT Edge na symulowanym urządzeniu w systemie ][lnk-tutorial1-win] lub [Linux][lnk-tutorial1-lin].

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie modułu usługi Azure Machine Learning
> * Wypychanie kontenera modułów do usługi Azure Container Registry
> * Wdrażanie modułu usługi Azure Machine Learning na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

Moduł usługi Azure Machine Learning tworzony w tym samouczku odczytuje dane środowiska wygenerowane przez urządzenie i oznacza, czy komunikaty wskazują wystąpienie anomalii, czy nie.

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge utworzone w ramach przewodnika Szybki start lub pierwszego samouczka.
* Parametry połączenia centrum IoT Hub, z którym łączy się urządzenie usługi IoT Edge.
* Konto usługi Azure Machine Learning. Aby utworzyć konto, postępuj zgodnie z instrukcjami w temacie [Tworzenie kont usługi Azure Machine Learning i instalowanie aplikacji Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Na potrzeby tego samouczka nie jest konieczne instalowanie aplikacji Workbench. 
* Funkcja zarządzania modułami dla usługi Azure ML na Twojej maszynie. Aby skonfigurować środowisko i utworzyć konto, postępuj zgodnie z instrukcjami w temacie [Konfiguracja funkcji zarządzania modelami](../machine-learning/desktop-workbench/deployment-setup-configuration.md).

Moduł usługi Azure Machine Learning nie obsługuje procesorów ARM.

## <a name="create-the-azure-ml-container"></a>Tworzenie kontenera usługi Azure ML
W tej sekcji pobierzesz pliki uczonego modelu i przekonwertujesz je na kontener usługi Azure ML.

Na maszynie, na której działa funkcja zarządzania modułami dla usługi Azure ML, pobierz i zapisz pliki [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) i [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) z rozszerzenia IoT Toolkit usługi Azure ML w serwisie GitHub. Te pliki definiują uczony model uczenia maszynowego, który zostanie wdrożony na urządzeniu IoT Edge.

Użyj uczonego modelu do utworzenia kontenera, który można wdrożyć na urządzeniach usługi IoT Edge. Użyj następującego polecenia, aby:

   * Zarejestrować model.
   * Utwórz manifest.
   * Utworzyć obraz kontenera platformy Docker o nazwie *machinelearningmodule*.
   * Wdrożyć obraz w klastrze usługi Azure Kubernetes Service (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Wyświetlanie repozytorium kontenerów

Sprawdź, czy obraz kontenera został pomyślnie utworzony i zapisany w repozytorium kontenerów platformy Azure skojarzonym ze środowiskiem uczenia maszynowego.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do obszaru **Wszystkie usługi**, a następnie wybierz pozycję **Rejestry kontenerów**.
2. Wybierz rejestr. Nazwa powinna zaczynać się od łańcucha **mlcr** i należy do grupy zasobów, lokalizacji i subskrypcji użytych do skonfigurowania funkcji zarządzania modułami.
3. Wybierz pozycję **Klucze dostępu**.
4. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**.  Są one potrzebne do uzyskania dostępu do rejestru z urządzeń usługi Edge.
5. Wybierz pozycję **Repozytoria**.
6. Wybierz pozycję **machinelearningmodule**.
7. Masz teraz pełną ścieżkę do obrazu kontenera. Zanotuj tę ścieżkę w celu użycia w następnej sekcji. Powinna ona wyglądać następująco: **<nazwa_rejestru>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Dodawanie poświadczeń rejestru na urządzeniu usługi Edge

Dodaj poświadczenia dla rejestru do środowiska uruchomieniowego usługi Edge na komputerze, na którym jest uruchomione urządzenie usługi Edge. Ta komenda zapewnia dostęp do środowiska uruchomieniowego w celu ściągnięcia kontenera.

W systemie Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

W systemie Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

## <a name="run-the-solution"></a>Uruchamianie rozwiązania

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do centrum IoT Hub.
1. Przejdź do pozycji **IoT Edge (wersja zapoznawcza)** i wybierz urządzenie usługi IoT Edge.
1. Wybierz opcję **Ustaw moduły**.
1. Jeśli moduł tempSensor został już wcześniej wdrożony na urządzeniu usługi IoT Edge, może on zostać automatycznie wypełniony. Jeśli nie znajduje się on jeszcze na liście modułów, dodaj go.
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `tempSensor`.
    3. W polu **Identyfikator URI obrazu** wprowadź wartość `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Wybierz pozycję **Zapisz**.
1. Dodaj utworzony moduł uczenia maszynowego.
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    1. W polu **Nazwa** wprowadź wartość `machinelearningmodule`.
    1. W polu **Obraz** wprowadź adres obrazu, na przykład `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Wybierz pozycję **Zapisz**.
1. W kroku **Dodawanie modułów** wybierz opcję **Dalej**.
1. W kroku **Określanie tras** skopiuj poniższe dane JSON do pola tekstowego. Pierwsza trasa służy do transportu komunikatów z czujnika temperatury do modułu uczenia maszynowego za pośrednictwem punktu końcowego „amlInput”, który jest używany przez wszystkie moduły usługi Azure Machine Learning. Druga trasa służy do transportu komunikatów z modułu uczenia maszynowego do centrum IoT Hub. W ramach tej trasy „amlOutput” to punkt końcowy używany przez wszystkie moduły usługi Azure Machine Learning do wyprowadzania danych, a element „$upstream” wskazuje centrum IoT Hub.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. Wybierz opcję **Dalej**.
1. W kroku **Przegląd szablonu** wybierz opcję **Prześlij**.
1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**.  Powinien zostać wyświetlony nowy moduł **machinelearningmodule** uruchomiony wraz z modułem **tempSensor** i modułami środowiska uruchomieniowego usługi IoT Edge.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Komunikaty urządzenie-chmura wysyłane przez urządzenia usługi IoT Edge można wyświetlić przy użyciu narzędzia [IoT Hub Explorer](https://github.com/azure/iothub-explorer) lub rozszerzenia Azure IoT Toolkit dla programu Visual Studio Code.

1. W programie Visual Studio Code wybierz pozycję **IoT Hub Devices** (Urządzenia centrum IoT Hub).
2. Wybierz pozycję **...** , a następnie wybierz pozycję **Set IoT Hub Connection String** (Ustaw parametry połączenia centrum IoT Hub) z menu.

   ![Menu Więcej urządzeń centrum IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. W polu tekstowym, które zostanie otwarte w górnej części strony wprowadź ciąg połączenia iothubowner dla używanego centrum IoT Hub. Urządzenie usługi IoT Edge powinno zostać wyświetlone na liście IoT Hub Devices (Urządzenia centrum IoT Hub).
4. Ponownie wybierz pozycję **...** , a następnie wybierz pozycję **Start monitoring D2C message** (Rozpocznij monitorowanie komunikatu D2C).
5. Obserwuj komunikaty przychodzące z modułu tempSensor co pięć sekund. Treść komunikatu zawiera właściwość o nazwie **anomaly**, którą moduł machinelearningmodule ustawia na wartość true lub false. Właściwość **AzureMLResponse** zawiera wartość „OK”, jeśli model został uruchomiony pomyślnie.

   ![Odpowiedź usługi Azure ML w treści komunikatu](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono moduł usługi IoT Edge obsługiwany przez usługę Azure Machine Learning. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Functions jako modułu](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
