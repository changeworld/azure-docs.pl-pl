---
title: Samouczek — wdrażanie usługi Azure Machine Learning na urządzeniu przy użyciu usługi Azure IoT Edge
description: W tym samouczku utworzysz model usługi Azure Machine Learning, a następnie wdrożysz go jako moduł na urządzeniu brzegowym
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76773001"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Samouczek: wdrażanie usługi Azure Machine Learning jako modułu usługi IoT Edge (wersja zapoznawcza)

Użyj notesów platformy Azure, aby opracować moduł uczenia maszynowego i wdrożyć go na urządzeniu z systemem Linux z uruchomiona usługą Azure IoT Edge.
Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek zawiera opis sposobu wdrażania modułu usługi Azure Machine Learning, który przewiduje awarię urządzenia na podstawie symulowanych danych dotyczących temperatury maszyny. Aby uzyskać więcej informacji na temat usługi Azure Machine Learning w usłudze IoT Edge, zobacz [dokumentację usługi Azure Machine Learning.](../machine-learning/how-to-deploy-and-where.md)

>[!NOTE]
>Moduły usługi Azure Machine Learning w usłudze Azure IoT Edge są dostępne w publicznej wersji zapoznawczej.

Moduł usługi Azure Machine Learning tworzony w tym samouczku odczytuje dane środowiska wygenerowane przez urządzenie i oznacza, czy komunikaty wskazują wystąpienie anomalii, czy nie.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie modułu usługi Azure Machine Learning
> * Wypychanie kontenera modułów do usługi Azure Container Registry
> * Wdrażanie modułu usługi Azure Machine Learning na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Maszynę wirtualną platformy Azure można używać jako urządzenia usługi IoT Edge, wykonując kroki opisane w przewodniku Szybki start dla [systemu Linux.](quickstart-linux.md)
* Moduł usługi Azure Machine Learning nie obsługuje kontenerów systemu Windows.
* Moduł usługi Azure Machine Learning nie obsługuje procesorów ARM.

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Obszar roboczy usługi Azure Machine Learning. Postępuj zgodnie z instrukcjami w [witrynie Korzystanie z witryny Azure portal, aby rozpocząć korzystanie z usługi Azure Machine Learning,](../machine-learning/tutorial-1st-experiment-sdk-setup.md) aby go utworzyć i dowiedzieć się, jak go używać.
  * Zanotuj nazwę obszaru roboczego, grupę zasobów i identyfikator subskrypcji. Wszystkie te wartości są dostępne w omówienie obszaru roboczego w witrynie Azure portal. Te wartości użyjesz w dalszej części samouczka, aby połączyć notes platformy Azure z zasobami obszaru roboczego.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Tworzenie i wdrażanie modułu usługi Azure Machine Learning

W tej sekcji konwertujesz wyszkolone pliki modelu uczenia maszynowego i do kontenera usługi Azure Machine Learning. Wszystkie wymagane składniki obrazu platformy Docker znajdują się w [repozytorium zestawu narzędzi SI dla usługi Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Wykonaj następujące kroki, aby przekazać to repozytorium do notesów platformy Microsoft Azure, aby utworzyć kontener i wypchnąć go do rejestru kontenerów platformy Azure.

1. Przejdź do projektów notesów platformy Azure. Możesz się tam dostać z obszaru roboczego usługi Azure Machine Learning w [witrynie Azure portal](https://portal.azure.com) lub logując się do [notesów platformy Microsoft Azure](https://notebooks.azure.com/home/projects) za pomocą konta platformy Azure.

2. Wybierz **pozycję Przekaż repozytorium GitHub**.

3. Podaj następującą nazwę repozytorium GitHub: `Azure/ai-toolkit-iot-edge`. Wyeznańczyć pole **Publiczne,** jeśli chcesz zachować prywatność projektu. Wybierz pozycję **Import**.

4. Po zakończeniu importowania przejdź do nowego projektu **ai-toolkit-iot-edge** i otwórz folder **samouczka wykrywania anomalii usługi IoT Edge.**

5. Sprawdź, czy projekt jest uruchomiony. Jeśli nie, wybierz opcję **Uruchom na wolnym obliczeniu**.

   ![Uruchom na wolnym obliczeń](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Otwórz plik **aml_config/config.json.**

7. Edytuj plik konfiguracyjny, aby uwzględnić wartości identyfikatora subskrypcji platformy Azure, grupy zasobów w subskrypcji i nazwy obszaru roboczego usługi Azure Machine Learning. Wszystkie te wartości można uzyskać z sekcji **Przegląd** obszaru roboczego na platformie Azure.

8. Zapisz plik konfiguracyjny.

9. Otwórz plik **00-anomaly-detection-tutorial.ipynb.**

10. Po wyświetleniu monitu wybierz jądro **Pythona 3.6,** a następnie wybierz pozycję **Ustaw jądro**.

11. Edytuj pierwszą komórkę w notesie zgodnie z instrukcjami w komentarzach. Użyj tej samej grupy zasobów, identyfikatora subskrypcji i nazwy obszaru roboczego, które zostały dodane do pliku konfiguracji.

12. Uruchom komórki w notesie, zaznaczając je i `Shift + Enter`wybierając pozycję **Uruchom** lub naciskając .

    >[!TIP]
    >Niektóre komórki w notesie samouczka wykrywania anomalii są opcjonalne, ponieważ tworzą zasoby, które niektórzy użytkownicy mogą lub nie muszą jeszcze mieć, takie jak Centrum IoT Hub. Jeśli umieścisz istniejące informacje o zasobach w pierwszej komórce, otrzymasz błędy po uruchomieniu komórek, które tworzą nowe zasoby, ponieważ platforma Azure nie utworzy zduplikowanych zasobów. Jest to w porządku i można zignorować błędy lub pominąć te sekcje opcjonalne całkowicie.

Wykonując wszystkie kroki w notesie, przeszkolono model wykrywania anomalii, skonstytuowałeś go jako obraz kontenera platformy Docker i wypchnąłeś ten obraz do rejestru kontenerów platformy Azure. Następnie przetestowano model i ostatecznie wdrożono go na urządzeniu IoT Edge.

## <a name="view-container-repository"></a>Wyświetlanie repozytorium kontenerów

Sprawdź, czy obraz kontenera został pomyślnie utworzony i przechowywany w rejestrze kontenerów platformy Azure skojarzonym ze środowiskiem uczenia maszynowego. Notes używany w poprzedniej sekcji automatycznie dostarczył obraz kontenera i poświadczenia rejestru do urządzenia usługi IoT Edge, ale powinieneś wiedzieć, gdzie są przechowywane, aby można było znaleźć informacje samodzielnie później.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do obszaru roboczego usługi uczenia maszynowego.

2. Sekcja **Przegląd** zawiera listę szczegółów obszaru roboczego, a także skojarzonych z nim zasobów. Wybierz wartość **Rejestru,** która powinna być nazwą obszaru roboczego, po której następowały liczby losowe.

3. W rejestrze kontenerów wybierz **pozycję Repozytoria**. Powinno zostać wyświetlona repozytorium o nazwie **tempanomalydetection,** które zostało utworzone przez notes uruchomiony we wcześniejszej sekcji.

4. Wybierz **tempanomalydetection**. Powinieneś zobaczyć, że repozytorium ma jeden tag: **1**.

   Teraz, gdy znasz nazwę rejestru, nazwę repozytorium i tag, znasz pełną ścieżkę obrazu kontenera. Ścieżki obrazu wyglądają jak ** \<registry_name\>.azurecr.io/tempanomalydetection:1**. Możesz użyć ścieżki obrazu do wdrożenia tego kontenera na urządzeniach usługi IoT Edge.

5. W rejestrze kontenerów wybierz pozycję **Klawisze programu Access**. Powinien zostać wyświetlony szereg poświadczeń dostępu, w tym **serwer logowania** i **nazwa użytkownika**i **hasło** dla użytkownika administratora.

   Te poświadczenia można dołączyć do manifestu wdrożenia, aby umożliwić urządzeniu usługi IoT Edge dostęp do ściągania obrazów kontenera z rejestru.

Teraz wiesz, gdzie jest przechowywany obraz kontenera usługi Machine Learning. W następnej sekcji przechodzi przez kroki, aby wyświetlić kontener uruchomiony jako moduł na urządzeniu Usługi IoT Edge.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

Wyświetlać możesz komunikaty generowane przez poszczególne moduły usługi IoT Edge oraz dostarczane do Twojego centrum IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Wyświetlanie danych na urządzeniu usługi IoT Edge

Na urządzeniu usługi IoT Edge można wyświetlać komunikaty wysyłane z każdego pojedynczego modułu.

Może być konieczne `sudo` użycie uprawnień z `iotedge` podwyższonym poziomem uprawnień do uruchamiania poleceń. Wylogowanie się i zalogowanie się z powrotem na urządzeniu automatycznie aktualizuje twoje uprawnienia.

1. Wyświetl wszystkie moduły na urządzeniu usługi IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Wyświetl komunikaty wysyłane z określonego urządzenia. Użyj nazwy modułu z danych wyjściowych poprzedniego polecenia.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Wyświetlanie danych otrzymywanych przez centrum IoT

Można wyświetlić komunikaty urządzenia do chmury, które centrum IoT otrzymuje przy użyciu [rozszerzenia Usługi Azure IoT Hub dla programu Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

Poniższe kroki pokazują, jak skonfigurować program Visual Studio Code w celu monitorowania komunikatów urządzenie-chmura w centrum IoT.

1. W programie Visual Studio Code wybierz pozycję **IoT Hub Devices** (Urządzenia centrum IoT Hub).

2. Wybierz pozycję **... **, a następnie wybierz pozycję **Set IoT Hub Connection String** (Ustaw parametry połączenia centrum IoT Hub) z menu.

   ![Określanie parametrów połączenia centrum IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. W polu tekstowym, które zostanie otwarte w górnej części strony wprowadź ciąg połączenia iothubowner dla używanego centrum IoT Hub. Urządzenie usługi IoT Edge powinno zostać wyświetlone na liście IoT Hub Devices (Urządzenia centrum IoT Hub).

4. Wybierz **...** ponownie, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

5. Obserwuj komunikaty przychodzące z modułu tempSensor co pięć sekund. Treść wiadomości zawiera właściwość o nazwie **anomalia**, która machinelearningmodule zapewnia wartość true lub false. Właściwość **AzureMLResponse** zawiera wartość „OK”, jeśli model został uruchomiony pomyślnie.

   ![Odpowiedź usługi Azure Machine Learning w treści wiadomości](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono moduł usługi IoT Edge obsługiwany przez usługę Azure Machine Learning. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Klasyfikowanie obrazów za pomocą usługi Custom Vision](tutorial-deploy-custom-vision.md)
