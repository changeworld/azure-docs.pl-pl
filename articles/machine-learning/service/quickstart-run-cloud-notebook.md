---
title: 'Szybki start: Uruchamianie notesu w chmurze'
titleSuffix: Azure Machine Learning service
description: Rozpocznij pracę z usługą Azure Machine Learning. Używanie serwera zarządzanego Notes w chmurze, możesz wypróbować obszaru roboczego.  Twój obszar roboczy jest podstawowe bloku w chmurze, którego używasz do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 68f24d4d019af873a0ca45792a3cbcc3dd3c3c3f
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476039"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwer opartych na chmurze notesu

Nie jest wymagana instalacja.  Wprowadzenie do usługi Azure Machine Learning w chmurze przy użyciu serwera zarządzanego notesu. Jeśli chcesz zamiast tego Zainstaluj zestaw SDK środowiska Python, zobacz [Szybki Start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwera notesu](quickstart-run-local-notebook.md).

Ten przewodnik Szybki Start pokazano, jak za pomocą [obszarze roboczym usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md) do śledzenia Twojej eksperymentów uczenia maszynowego.  Utworzysz [notesu maszyny Wirtualnej (wersja zapoznawcza)](how-to-configure-environment.md#notebookvm), bezpieczne i oparte na chmurze platformy Azure stacja robocza, która zawiera serwer notesu Jupyter, JupyterLab i w pełni przygotowany środowisku ML. Następnie należy uruchomić Notes Python na tej maszynie Wirtualnej, dziennika wartości do obszaru roboczego.

W tym przewodniku Szybki start wykonasz następujące czynności:

* Tworzenie obszaru roboczego
* Utwórz notes maszyny Wirtualnej w obszarze roboczym.
* Uruchom interfejs sieci web programu Jupyter.
* Otwórz notes, który zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji.
* Uruchamianie notesu.
* Wyświetl wartości błędów rejestrowane w obszarze roboczym. W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Jeśli masz obszar roboczy usługi Azure Machine Learning, przejdź do [następnej sekcji](#create-notebook). W przeciwnym razie utworzyć teraz.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Tworzenie notesu maszyny Wirtualnej

 Z obszaru roboczego utworzysz zasób chmury, aby rozpocząć korzystanie z notesów programu Jupyter. Ten zasób zapewnia oparte na chmurze platforma, wstępnie skonfigurowane na wszystko, czego potrzebujesz, aby uruchomić usługę Azure Machine Learning.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  Jeśli nie masz pewności, jak znaleźć obszaru roboczego w portalu, zobacz temat jak [Znajdź obszar roboczy](how-to-manage-workspace.md#view).

1. Na stronie obszaru roboczego w witrynie Azure portal, wybierz **maszyn wirtualnych z notesu** po lewej stronie.

1. Wybierz **+ nowy** Aby utworzyć notes maszyny Wirtualnej.

     ![Wybierz nową maszynę Wirtualną](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Podaj nazwę dla maszyny Wirtualnej. Następnie wybierz przycisk **Utwórz**.

    > [!NOTE]
    > Nazwę notesu maszyny Wirtualnej musi być pomiędzy 2 do 16 znaków. Prawidłowe znaki to litery, cyfry, a znak.  Nazwa również musi być unikatowa w ramach subskrypcji platformy Azure.

    ![Utwórz nową maszynę Wirtualną](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Odczekaj około 4 – 5 minut, aż stan zmieni się na **systemem**.


## <a name="launch-jupyter-web-interface"></a>Uruchom interfejs sieci web programu Jupyter

Po uruchomieniu maszyny Wirtualnej, użyj **maszyn wirtualnych z notesu** sekcji, aby uruchomić interfejs sieci web programu Jupyter.

1. Wybierz **Jupyter** w **URI** kolumny dla maszyny Wirtualnej.  

    ![Uruchom serwer notesu Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Link uruchamia serwer notesu i otwiera stronę notesu Jupyter w nowej karcie przeglądarki.  To łącze będzie działać tylko przez osobę, która tworzy maszynę Wirtualną.

1. Na stronie internetowej notesu programu Jupyter najważniejsze nazwa_folderu jest swoją nazwę użytkownika.  Wybierz ten folder.

    > [!TIP]
    > Ten folder znajduje się na [kontenera magazynu](concept-workspace.md#resources) w obszarze roboczym, a nie w notesie samej maszyny Wirtualnej.  Możesz usunąć notesu maszyny Wirtualnej i nadal utrzymuje swoją pracę.  Podczas tworzenia nowego notesu maszyn wirtualnych później, będzie on ładować się tego samego folderu.

1. Nazwa_folderu przykładów zawiera numer wersji, na przykład **1.0.33.1 przykłady**.  Wybierz folder przykładów.

1. Wybierz **Szybki Start** folderu.

## <a name="run-the-notebook"></a>Uruchamianie notesu

Uruchamianie notesu, szacuje pi, która rejestruje błąd w Twoim obszarze roboczym.

1. Wybierz plik **01.run experiment.ipynb**, aby otworzyć notes.

1. Jeśli zostanie wyświetlony alert "Jądra nie można odnaleźć", wybierz jądra **3.6 języka Python — Azure ml** (około połowie sposób, w dół na liście) i ustaw jądra.

1. Kliknij przycisk do pierwszej komórki kodu, a następnie wybierz pozycję **Uruchom**.

    > [!NOTE]
    > Komórki kodu mają nawiasy przed nimi. Jeśli nawiasy kwadratowe są puste ( __[__ ), kod nie został uruchomiony. Gdy kod jest uruchomiona, zobaczysz znak gwiazdki ( __[*]__ ). Po zakończeniu kod, liczbą **[1]** pojawia się.  Liczba informuje kolejność, w którym uruchomiono komórki.
    >
    > Użyj **wprowadź Shift** jako skrót, aby uruchomić komórkę.

    ![Uruchamianie pierwszej komórki kodu](media/quickstart-run-cloud-notebook/cell1.png)

1. Uruchom drugie komórkę kodu. Jeśli widzisz instrukcjami w celu uwierzytelnienia, skopiuj kod i skorzystaj z linku do logowania. Po zalogowaniu przeglądarce zapamięta tego ustawienia.  

    ![Uwierzytelnianie](media/quickstart-run-cloud-notebook/authenticate.png)

1. Po zakończeniu, liczby komórek __[2]__ pojawia się.  Gdyby trzeba było Zaloguj się, zobaczysz komunikat o stanie pomyślnym uwierzytelnieniu.   Jeśli nie masz do logowania, nie zobaczysz żadnych danych wyjściowych dla tej komórki, tylko numer pojawi się pomyślnie uruchomiono komórki.

    ![Komunikat o powodzeniu](media/quickstart-run-cloud-notebook/success.png)

1. Uruchom pozostałą część komórki kodu.  Ponieważ każda komórka na zakończenie działania zostaną wyświetlone jego numer komórki, które są wyświetlane. Ostatnią komórkę wyświetla żadnych danych wyjściowych.  

    W największych komórce kodu, zobacz `run.log` używany w kilku miejscach. Każdy `run.log` dodaje wartość do swojego obszaru roboczego.

## <a name="view-logged-values"></a>Wyświetlanie zarejestrowanych wartości

1. Dane wyjściowe z komórki `run` zawierają link do witryny Azure Portal, dzięki któremu można wyświetlić wyniki eksperymentu w obszarze roboczym.

    ![Wyświetlanie eksperymentów](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Kliknij przycisk **Link do witryny Azure portal** Aby wyświetlić informacje o przebiegu w obszarze roboczym.  Ten link powoduje otwarcie obszaru roboczego w witrynie Azure Portal.

1. Wykresy zarejestrowanych wartości, które zobaczysz, zostały automatycznie utworzone w obszarze roboczym. Zawsze w przypadku zarejestrowania wielu wartości z tym samym parametrem nazwa wykres jest generowany automatycznie. Oto przykład:

   ![Wyświetlanie historii](./media/quickstart-run-cloud-notebook/web-results.png)

Ponieważ kod do pi przybliżony używa wartości losowych, Twoje wykresy mogą wyglądać inaczej.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="stop-the-notebook-vm"></a>Zatrzymaj notesu maszyny Wirtualnej

Zatrzymaj notesu maszyny Wirtualnej, gdy nie jest ona używana do zmniejszenia kosztów.  

1. W obszarze roboczym, wybierz **maszyn wirtualnych z notesu**.

   ![Zatrzymywanie maszyny Wirtualnej serwera](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Z listy wybierz maszynę wirtualną.

1. Wybierz **zatrzymać**.

1. Gdy wszystko będzie gotowe do ponownego użycia serwera, wybierz pozycję **Start**.

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start możesz wykonać te zadania:

* Tworzenie obszaru roboczego
* Utwórz notes maszyny Wirtualnej.
* Uruchom interfejs sieci web programu Jupyter.
* Otwórz notes, który zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji.
* Uruchamianie notesu.
* Wyświetl wartości błędów rejestrowane w obszarze roboczym.  W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

Na stronie sieci Web notesu Jupyter w **Szybki Start** folder, Otwórz i uruchom **02.deploy-web-service.ipynb** Notes, aby dowiedzieć się, jak wdrożyć usługę sieci web.

Również w witrynie internetowej notesu programu Jupyter, przeglądać inne notesów w folderze samples, aby dowiedzieć się więcej na temat usługi Azure Machine Learning.

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym trenowania i wdrażania modelu w usłudze Machine Learning:  

> [!div class="nextstepaction"]
> [Samouczek: trenowanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)
