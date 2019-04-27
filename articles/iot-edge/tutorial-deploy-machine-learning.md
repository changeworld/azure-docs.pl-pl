---
title: Wdrażanie usługi Azure Machine Learning na urządzeniu — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W ramach tego samouczka możesz utworzyć model usługi Azure Machine Learning, a następnie wdrożyć go jako moduł na urządzeniu usługi edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 8b1036128755a5218afc35648dfd16f09f559908
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611774"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Samouczek: Wdrażanie usługi Azure Machine Learning jako modułu usługi IoT Edge (wersja zapoznawcza)

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek zawiera opis sposobu wdrażania modułu usługi Azure Machine Learning, który przewiduje awarię urządzenia na podstawie symulowanych danych dotyczących temperatury maszyny. Więcej informacji na temat usługi Azure Machine Learning Service na platformie IoT Edge można znaleźć w [dokumentacji usługi Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

Moduł usługi Azure Machine Learning tworzony w tym samouczku odczytuje dane środowiska wygenerowane przez urządzenie i oznacza, czy komunikaty wskazują wystąpienie anomalii, czy nie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie modułu usługi Azure Machine Learning
> * Wypychanie kontenera modułów do usługi Azure Container Registry
> * Wdrażanie modułu usługi Azure Machine Learning na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

>[!NOTE]
>Moduły usługi Azure Machine Learning w usłudze Azure IoT Edge są dostępne w publicznej wersji zapoznawczej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Maszynę wirtualną platformy Azure można użyć jako urządzenia usługi IoT Edge, wykonując kroki opisane w przewodniku Szybki Start dla [Linux](quickstart-linux.md).
* Moduł usługi Azure Machine Learning nie obsługuje kontenery Windows.
* Moduł usługi Azure Machine Learning nie obsługuje procesory ARM.

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Obszar roboczy usługi Azure Machine Learning. Postępuj zgodnie z instrukcjami w [Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu witryny Azure portal](../machine-learning/service/quickstart-get-started.md) ją utworzyć i Dowiedz się, jak z niego korzystać.
   * Zanotuj nazwę obszaru roboczego, grupy zasobów i identyfikatora subskrypcji. Te wartości są dostępne w obszarze Przegląd obszaru roboczego w witrynie Azure portal. Użyjesz tych wartości w dalszej części tego samouczka do łączenia z notesu platformy Azure do zasobów obszaru roboczego. 


### <a name="disable-process-identification"></a>Wyłączanie identyfikacji procesu

>[!NOTE]
>
> Usługa Azure Machine Learning w wersji zapoznawczej nie obsługuje funkcji zabezpieczeń identyfikacji procesu włączonej domyślnie w usłudze IoT Edge.
> Poniżej przedstawiono kroki, które umożliwiają jej wyłączenie. Nie są one jednak odpowiednie do użycia w środowisku produkcyjnym. Te kroki są tylko niezbędne na urządzeniach z systemem Linux. 

Aby wyłączyć identyfikacji procesów na urządzeniu usługi IoT Edge, musisz podać adres IP i portu dla **workload_uri** i **management_uri** w **połączyć** Sekcja konfiguracji demona usługi IoT Edge.

Najpierw uzyskaj adres IP. Wprowadź polecenie `ifconfig` w wierszu polecenia i skopiuj adres IP interfejsu **docker0**.

Edytuj plik konfiguracji demona usługi IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Zaktualizuj sekcję **zawartości** konfiguracji przy użyciu adresu IP. Na przykład:
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Wprowadź te same adresy w sekcji **listen** konfiguracji. Na przykład:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Zapisz i zamknij plik konfiguracji.

Utwórz zmienną środowiskową IOTEDGE_HOST za pomocą adresu management_uri (Aby ustawić trwale, dodaj go do `/etc/environment`). Na przykład:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```

Uruchom ponownie usługę IoT Edge, aby zmiany zaczęły obowiązywać.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="create-and-deploy-azure-machine-learning-module"></a>Tworzenie i wdrażanie modułu usługi Azure Machine Learning

W tej sekcji Konwertuj maszynę uczonego modelu uczenia i do usługi Azure Machine Learning usługi kontenera. Wszystkie wymagane składniki obrazu platformy Docker znajdują się w [repozytorium zestawu narzędzi SI dla usługi Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Wykonaj następujące kroki, aby przekazać tego repozytorium do notesów usługi Azure firmy Microsoft do tworzenia kontenera i Wypchnij go do usługi Azure Container Registry.


1. Przejdź do swoich projektów notesy platformy Azure. Użytkownik może dostać się tam z obszaru roboczego usługi Azure Machine Learning w [witryny Azure portal](https://portal.azure.com) lub logując się do [Microsoft Azure notesów](https://notebooks.azure.com/home/projects) z kontem platformy Azure.

2. Wybierz **repozytorium GitHub, aby przekazać**.

3. Podaj następującą nazwę repozytorium usługi GitHub: `Azure/ai-toolkit-iot-edge`. Usuń zaznaczenie pola wyboru **publicznych** polu, aby zapewnić prywatność projektu. Wybierz **importu**. 

4. Po zakończeniu importowania, przejdź do nowego **sztucznej inteligencji — zestaw narzędzi iot-edge** projektu, a następnie otwórz **samouczek wykrywania anomalii w usłudze IoT Edge** folderu. 

5. Sprawdź, czy projekt jest uruchomiony. Jeśli nie, wybierz **systemem bezpłatne obliczenia**.

   ![Uruchom na bezpłatnych zasobów obliczeniowych](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Otwórz **aml_config/config.json** pliku.

7. Edytuj plik konfiguracji, aby uwzględnić wartości dla Identyfikatora subskrypcji platformy Azure, grupę zasobów w subskrypcji i nazwę obszaru roboczego usługi Azure Machine Learning. Możesz uzyskać te wartości z **Przegląd** części obszaru roboczego na platformie Azure. 

8. Zapisz plik konfiguracji.

9. Otwórz **00-anomalii — wykrywanie tutorial.ipynb** pliku.

10. Po wyświetleniu monitu wybierz **środowiska Python 3.6** jądra polecenie **Ustaw jądra**.

11. Edytuj pierwszej komórki w notesie zgodnie z instrukcjami w komentarzach. Użyj tej samej grupie zasobów, identyfikator subskrypcji i nazwę obszaru roboczego, który został dodany do pliku konfiguracji.

12. Uruchom komórki w notesie, wybierając je i wybierając **Uruchom** lub naciskając klawisz `Shift + Enter`.

    >[!TIP]
    >Niektóre z komórek w notesie samouczek wykrywania anomalii są opcjonalne, ponieważ mogą one tworzyć zasoby, które niektórzy użytkownicy mogą lub nie może mieć jeszcze, takich jak usługi IoT Hub. Jeśli istniejące informacje o zasobach przełączyć się do pierwszej komórki, otrzymasz błędy po uruchomieniu komórki, które tworzyć nowe zasoby, ponieważ platforma Azure nie będzie utworzyć powielonych zasobów. Jest to poprawne, i można zignorować błędy lub całkowicie pominąć te opcjonalne sekcje. 

Po wykonaniu wszystkich kroków w notesie możesz będzie mieć uczony model wykrywania anomalii, powstała jako obraz kontenera Docker i wypchnięto obraz do usługi Azure Container Registry. Następnie przetestować model, a na koniec wdrożone do urządzenia usługi IoT Edge. 

## <a name="view-container-repository"></a>Widok repozytorium kontenerów

Sprawdź, czy obraz kontenera został pomyślnie tworzone i przechowywane w rejestrze kontenerów platformy Azure, który jest skojarzony z wymaganiami środowiska szkoleniowego maszyny. Notes, które było używane w poprzedniej sekcji automatycznie udostępniane obrazu kontenera i poświadczenia rejestru, aby urządzenia usługi IoT Edge, ale należy wiedzieć, gdzie są one przechowywane, aby informacje można znaleźć samodzielnie później. 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do obszaru roboczego usługi Machine Learning. 

2. **Przegląd** sekcja zawiera szczegóły obszaru roboczego, jak również skojarzone z nią zasoby. Wybierz **rejestru** wartość, która powinna być nazwy swojego obszaru roboczego, a następnie liczb losowych. 

3. W rejestrze kontenerów, wybierz **repozytoriów**. Powinien zostać wyświetlony repozytorium o nazwie **tempanomalydetection** utworzony przez notesu uruchomiono we wcześniejszej sekcji. 

4. Wybierz **tempanomalydetection**. Powinny pojawić się, że repozytorium ma jeden tag: **1**. 

   Skoro znasz nazwę rejestru, Nazwa repozytorium oraz tag, wiesz, ścieżka pełnego obrazu kontenera. Obraz ścieżek wyglądać  **\<registry_name\>.azurecr.io/tempanomalydetection:1**. Aby wdrożyć ten kontener na urządzeniach usługi IoT Edge, można użyć ścieżki obrazu. 

5. W rejestrze kontenerów, wybierz **klucze dostępu**. Powinna zostać wyświetlona liczba poświadczenia dostępu, w tym **serwer logowania** i **Username**, i **hasło** dla użytkownika administracyjnego.

   Te poświadczenia mogą być dołączane w pliku manifestu wdrożenia, aby zapewnić usługi IoT Edge dostęp urządzenia do Ściągnij obrazy kontenerów z rejestru. 

Teraz wiesz, gdzie jest przechowywany obraz kontenera usługi Machine Learning. Następnej sekcji przedstawiono kroki, aby zobaczyć, jak działa jako moduł wdrożonego na urządzeniu usługi IoT Edge. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Wyświetlać możesz komunikaty generowane przez poszczególne moduły usługi IoT Edge oraz dostarczane do Twojego centrum IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Wyświetlanie danych na urządzeniu usługi IoT Edge

Na urządzeniu usługi IoT Edge można wyświetlać komunikaty wysyłane z każdego pojedynczego modułu.

Może być konieczne użycie `sudo` podwyższonym poziomem uprawnień do uruchomienia `iotedge` poleceń. Wylogowanie i zalogowanie do Twojego urządzenia automatycznie aktualizuje swoje uprawnienia.

1. Wyświetl wszystkie moduły na urządzeniu usługi IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Wyświetl komunikaty wysyłane z określonego urządzenia. Użyj nazwy modułu z danych wyjściowych poprzedniego polecenia.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Wyświetlanie danych otrzymywanych przez centrum IoT

Komunikaty urządzenie-chmura odbierane przez centrum IoT można wyświetlić przy użyciu [rozszerzenia Azure IoT Hub Toolkit dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dawniej rozszerzenia Azure IoT Toolkit).

Poniższe kroki pokazują, jak skonfigurować program Visual Studio Code w celu monitorowania komunikatów urządzenie-chmura w centrum IoT.

1. W programie Visual Studio Code wybierz pozycję **IoT Hub Devices** (Urządzenia centrum IoT Hub).

2. Wybierz pozycję **...** , a następnie wybierz pozycję **Set IoT Hub Connection String** (Ustaw parametry połączenia centrum IoT Hub) z menu.

   ![Określanie parametrów połączenia centrum IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. W polu tekstowym, które zostanie otwarte w górnej części strony wprowadź ciąg połączenia iothubowner dla używanego centrum IoT Hub. Urządzenie usługi IoT Edge powinno zostać wyświetlone na liście IoT Hub Devices (Urządzenia centrum IoT Hub).

4. Ponownie wybierz pozycję **...** , a następnie wybierz pozycję **Start monitoring D2C message** (Rozpocznij monitorowanie komunikatu D2C).

5. Obserwuj komunikaty przychodzące z modułu tempSensor co pięć sekund. Treść wiadomości zawiera właściwość o nazwie **anomalii**, oferujący machinelearningmodule o wartości true lub false. Właściwość **AzureMLResponse** zawiera wartość „OK”, jeśli model został uruchomiony pomyślnie.

   ![Odpowiedź usługi Azure Machine Learning Service w treści komunikatu](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wdrożono moduł usługi IoT Edge obsługiwany przez usługę Azure Machine Learning. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Klasyfikowanie obrazów za pomocą usługi Custom Vision](tutorial-deploy-custom-vision.md)

