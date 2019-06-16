---
title: Uczenie i wdrażanie modelu — Machine Learning na platformie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Szkolenie modelu uczenia maszynowego przy użyciu usługi Azure Machine Learning, a następnie pakietu modelu jako obraz kontenera, który może być wdrożony jako moduł usługi Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a94560bcb66c2ed59f78eef4f6a08b3f0227dc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057638"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: Nauczanie i wdrożyć model uczenia maszynowego Azure

> [!NOTE]
> Ten artykuł jest częścią serii, samouczek dotyczący przy użyciu usługi Azure Machine Learning w usłudze IoT Edge. Jeśli została wyświetlona bezpośrednio w tym artykule, firma Microsoft zachęca rozpoczynać się [najpierw artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii, aby uzyskać najlepsze wyniki.

W tym artykule używamy notesów usługi Azure najpierw szkolenie przy użyciu usługi Azure Machine Learning model uczenia maszynowego i następnie pakietu tego modelu w postaci obrazu kontenera, który może być wdrożony jako moduł usługi Azure IoT Edge. Notesy platformy Azure z zalet usługi Azure Machine Learning usługi obszar roboczy, który jest używany do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego blok podstawowe.

Działania w tej części samouczka są dzielone między dwa notesy.

* **01 turbofan\_regression.ipynb:** Ten notes przedstawiono kroki, aby uczenie i publikowanie model przy użyciu usługi Azure Machine Learning. Ogólnie etapy są:

  1. Pobierz, przygotowywania i eksplorować dane szkoleniowe
  2. Użyj obszaru roboczego usługi do tworzenia i uruchamiania eksperymentu uczenia maszynowego
  3. Ocena wyników modelu z eksperymentu
  4. Publikowanie najlepszy model z obszarem roboczym usługi

* **02 turbofan\_wdrażanie\_model.ipynb:** Ten notes przyjmuje model utworzony w poprzednim Notes i używa jej do utworzenia obrazu kontenera, gotowa do wdrożenia na urządzeniu z systemem Azure IoT Edge.

  1. Utwórz skrypt oceniania modelu
  2. Tworzenie i publikowanie obrazu
  3. Wdrożyć obraz jako usługę sieci web w wystąpieniu kontenera platformy Azure
  4. Walidacja modelu i pracy obrazu zgodnie z oczekiwaniami przy użyciu usługi sieci web

Kroki opisane w tym artykule mogą być wykonywane zwykle przez analityków danych.

## <a name="set-up-azure-notebooks"></a>Konfigurowanie notesów usługi Azure

Notesy platformy Azure są używane do hostowania dwa notesy Jupyter i plików pomocniczych. W tym miejscu utworzymy i skonfigurowanie projektu notesy platformy Azure. Jeśli nie używano Jupyter i/lub Azure notesów, poniżej przedstawiono kilka dokumentów wprowadzających:

* **Szybki Start:** [Tworzenie i udostępnianie notesu](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Samouczek:** [Tworzenie i uruchamianie notesu programu Jupyter przy użyciu języka Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Jako maszyny wirtualnej dla deweloperów, za pomocą notesów usługi Azure zapewnia spójne środowisko wykonywania.

> [!NOTE]
> Po skonfigurowaniu usługi notesy platformy Azure są dostępne z dowolnego komputera. Podczas instalacji należy użyć rozwoju maszynę wirtualną, która zawiera wszystkie pliki, które będą potrzebne.

### <a name="create-an-azure-notebooks-account"></a>Tworzenie konta usługi Azure notesów

Kont notesu platformy Azure są niezależne od subskrypcji platformy Azure. Aby korzystanie z notesów usługi Azure, musisz utworzyć konto.

1. Przejdź do [notesy platformy Azure](http://notebooks.azure.com).

2. Kliknij przycisk **Sign In** w górnej, prawej części strony.

3. Zarejestruj się w pracy lub konta służbowego (Azure Active Directory) lub konto osobiste (Account Microsoft).

4. Notesy platformy Azure przed nie były używane, zostanie wyświetlony monit udzielić dostępu aplikacji notesy platformy Azure.

5. Utwórz identyfikator użytkownika dla notesów usługi Azure.

### <a name="upload-jupyter-notebooks-files"></a>Przekazywanie plików do notesów programu Jupyter

W tym kroku będziemy Utwórz nowy projekt platformy Azure, notesy i przekaż pliki do niego. W szczególności są pliki, które możemy przekazać:

* **01 turbofan\_regression.ipynb**: Plik notesu programu Jupyter, który przeprowadzi proces pobierania danych generowanych przez kontroler urządzenia z kontem usługi Azure storage; Eksplorowanie i przygotowywanie danych szkoleniowych klasyfikatora; szkolenie modelu; Testowanie danych przy użyciu zestawu danych testowych znalezione w teście\_plik FD003.txt; i, ostatecznie zapisywanie klasyfikatora modelu w obszarze roboczym usługi Machine Learning.

* **02 turbofan\_wdrażanie\_model.ipynb:** Notesu programu Jupyter, który przeprowadzi Cię przez proces przy użyciu modelu klasyfikatora, zapisane w obszarze roboczym usługi Machine Learning, aby utworzyć obraz kontenera. Po utworzeniu obrazu przeszukiwania Notes, które Cię przez proces wdrażania obrazu jako usługę sieci web, dzięki czemu można go zweryfikować działa zgodnie z oczekiwaniami. Zweryfikowane obraz zostanie wdrożony na naszych urządzenia Edge w [tworzenie i wdrażanie niestandardowych modułów usługi IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) części tego samouczka.

* **Test\_FD003.txt:** Ten plik zawiera dane, które zostaną użyte jako naszym teście ustawiana, jeśli sprawdzanie poprawności naszych uczonego klasyfikatora. Wybraliśmy na korzystanie z danych testowych, zgodnie z oryginalnym konkursu jako naszym teście, ustaw dla uproszczenia w przykładzie.

* **Pozostały czas eksploatacji\_FD003.txt:** Ten plik zawiera pozostałego czasu eksploatacji dla ostatniego cyklu każdego urządzenia w teście\_FD003.txt pliku. Zobacz **readme.txt** i **Modeling.pdf propagacji szkody** pliki w C:\\źródła\\IoTEdgeAndMlSample\\danych\\Turbofan dla szczegółowy opis danych.

* **Utils.py:** Zawiera zestaw funkcji narzędzia języka Python do pracy z danymi. Pierwszy notes zawiera szczegółowy opis funkcji.

* **README.md:** Plik Readme opisujący sposób użycia notesów.

Utwórz nowy projekt i przekaż pliki do Notatnika.

1. Wybierz **Moje projekty** na pasku menu u góry.

1. Wybierz **+ nowy projekt**. Podaj nazwę i identyfikator. Nie ma potrzeby dla projektu jawne lub pliku readme.

1. Wybierz **przekazywanie** i wybierz polecenie **z komputera**.

1. Wybierz **wybierz pliki**.

1. Przejdź do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Zaznacz wszystkie pliki, a następnie kliknij przycisk **Otwórz**.

1. Wybierz **przekazywanie** Rozpocznij przekazywanie, a następnie wybierz pozycję **gotowe** po zakończeniu procesu.

## <a name="run-azure-notebooks"></a>Uruchamianie notesów usługi Azure

Teraz, że projekt jest tworzony, uruchom **01 turbofan\_regression.ipynb** notesu.

1. Na stronie projektu turbofan wybierz **01 turbofan\_regression.ipynb**.

    ![Wybierz pierwszy Notes do uruchomienia](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Po wyświetleniu monitu wybierz jądra 3.6 języka Python z okna dialogowego i wybierz przycisk **Ustaw jądra**.

3. Jeśli notes jest wymieniony jako **niezaufany**, kliknij pozycję **niezaufany** widget w prawym górnym rogu notesu. Po wyświetleniu okna dialogowego wybierz **zaufania**.

4. Postępuj zgodnie z instrukcjami w notesie.

    * `Ctrl + Enter` Uruchamia komórki.
    * `Shift + Enter` działa w komórce i przechodzi do następnej komórki.
    * Komórki jest uruchomiona, ma gwiazdka między nawiasami kwadratowymi, takie jak **[\*]** . Po zakończeniu, gwiazdkę zostanie zastąpiony numeru i odpowiednie dane wyjściowe mogą być pojawią się poniżej. Od komórek kompilacji często we współpracy z poprzednim, uruchomić tylko jedną komórkę w danym momencie.

5. Po zakończeniu uruchamiania **01 turbofan\_regression.ipynb** Notes, wróć do strony projektu.

6. Otwórz **02 turbofan\_wdrażanie\_model.ipynb** i powtórz kroki opisane w tej sekcji, aby uruchomić Notes drugiego.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule użyliśmy dwa notesy Jupyter w notesach Azure, aby użyć danych z urządzeń turbofan to w opracowywaniu pozostałe klasyfikatora okres eksploatacji (RUL), aby zapisać klasyfikatora jako model, aby utworzyć obraz kontenera oraz do wdrażania i testowania obrazu jako se sieci web SP.

Przejdź do następnego artykułu, aby utworzyć urządzenia usługi IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia usługi IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
