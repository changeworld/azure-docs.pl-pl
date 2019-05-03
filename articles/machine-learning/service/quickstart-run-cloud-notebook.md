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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3d4127226037bf28ba677a49f6444ca987118cb9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023922"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwer opartych na chmurze notesu

Utwórz serwer opartych na chmurze Notes, a następnie użyć go.  W tym przewodniku Szybki Start uruchamianie kodu w języku Python, który loguje się wartości [obszaru roboczego usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md). Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich trenowanie oraz wdrażanie za pomocą usługi Machine Learning. 

Ten przewodnik Szybki Start przedstawiono sposób tworzenia zasobu chmury w obszarze roboczym usługi Azure Machine Learning skonfigurowany ze środowiskiem Python niezbędne do uruchomienia usługi Azure Machine Learning. Aby użyć własnego środowiska, zobacz [Szybki Start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwera notesu](quickstart-run-local-notebook.md).  
 
W tym przewodniku Szybki start wykonasz następujące czynności:

* Utwórz nowy serwer opartych na chmurze Notes w obszarze roboczym
* Uruchom interfejs sieci web programu Jupyter
* Otwórz notes, który zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji.
* Uruchamianie notesu.
* Wyświetl wartości błędów rejestrowane w obszarze roboczym.  W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning.  [Tworzenie obszaru roboczego usługi](setup-create-workspace.md#portal) teraz, jeśli nie masz.

## <a name="create-a-cloud-based-notebook-server"></a>Utwórz serwer opartych na chmurze notesu

 Z obszaru roboczego utworzysz zasób chmury, aby rozpocząć korzystanie z notesów programu Jupyter. Ten zasób zapewnia oparte na chmurze platforma, wstępnie skonfigurowane na wszystko, czego potrzebujesz, aby uruchomić usługę Azure Machine Learning.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  Jeśli nie masz pewności, jak znaleźć obszaru roboczego w portalu, zobacz temat jak [Znajdź obszar roboczy](how-to-manage-workspace.md#view).

1. Na stronie obszaru roboczego w witrynie Azure portal, wybierz **maszyn wirtualnych z notesu** po lewej stronie.

1. Wybierz **+ nowy** Aby utworzyć notes maszyny Wirtualnej.

     ![Wybierz nową maszynę Wirtualną](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Podaj nazwę dla maszyny Wirtualnej. Następnie wybierz przycisk **Utwórz**. 

    ![Utwórz nową maszynę Wirtualną](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Odczekaj około 4 – 5 minut, a następnie wybierz **Odśwież**.  Spróbuj odświeżyć co 30 sekund lub tak, aż stan zmieni się **systemem**.

    ![Odświeżanie](media/quickstart-run-cloud-notebook/refresh.png)

## <a name="launch-jupyter-web-interface"></a>Uruchom interfejs sieci web programu Jupyter

Po uruchomieniu maszyny Wirtualnej, użyj **maszyn wirtualnych z notesu** sekcji, aby uruchomić interfejs sieci web programu Jupyter.

1. Wybierz **Jupyter** w **URI** kolumny dla maszyny Wirtualnej.  

    ![Uruchom serwer notesu Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Link uruchamia serwer notesu i otwiera stronę notesu Jupyter w nowej karcie przeglądarki.  To łącze będzie działać tylko przez osobę, która tworzy maszynę Wirtualną.

1. Na stronie internetowej notesu programu Jupyter, wybierz **samples/quickstart** folderze, aby zobaczyć notesu Szybki Start.

## <a name="run-the-notebook"></a>Uruchamianie notesu

Uruchamianie notesu, szacuje pi, która rejestruje błąd w Twoim obszarze roboczym.

1. Wybierz plik **01.run experiment.ipynb**, aby otworzyć notes.

1. Może zostać wyświetlony komunikat, który nie został ustawiony jądra.  Wybierz **3.6 języka Python — Azure ml**, a następnie wybierz **Ustaw jądra**.

   ![Ustaw jądra](./media/quickstart-run-cloud-notebook/set-kernel.png)

1. W obszarze stanu jest widoczna informacja o tym, że musisz zaczekać na uruchomienie jądra. Komunikat zniknie, gdy jądro będzie gotowe.

    ![Oczekiwanie na uruchomienie jądra](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1.  Kliknij przycisk do pierwszej komórki kodu, a następnie wybierz pozycję **Uruchom**.

    > [!NOTE]
    > Komórki kodu mają nawiasy przed nimi. Jeśli nawiasy kwadratowe są puste (__[__), kod nie został uruchomiony. Gdy kod jest uruchomiona, zobaczysz znak gwiazdki (__[*]__). Po zakończeniu kod, liczbą **[1]** pojawia się.  Liczba informuje kolejność, w którym uruchomiono komórki.
    >
    > Użyj **wprowadź Shift** jako skrót, aby uruchomić komórkę.

    ![Uruchamianie pierwszej komórki kodu](media/quickstart-run-cloud-notebook/cell1.png)

1. Uruchom drugie komórkę kodu. Jeśli widzisz instrukcjami w celu uwierzytelnienia, skopiuj kod i skorzystaj z linku do logowania. Po zalogowaniu przeglądarce zapamięta tego ustawienia.  

    > [!TIP]
    > Pamiętaj, aby nie Kopiuj miejsca po kodzie.  

    ![Uwierzytelnianie](media/quickstart-run-cloud-notebook/authenticate.png)

1. Gdy wszystko będzie gotowe, liczby komórek __[2]__ pojawia się.  Gdyby trzeba było Zaloguj się, zobaczysz komunikat o stanie pomyślnym uwierzytelnieniu.   Jeśli nie masz do logowania, nie zobaczysz żadnych danych wyjściowych dla tej komórki, tylko numer pojawi się pomyślnie uruchomiono komórki.

    ![Komunikat o powodzeniu](media/quickstart-run-cloud-notebook/success.png)

1. Uruchom pozostałą część komórki kodu.  Ponieważ każda komórka na zakończenie działania zostaną wyświetlone jego numer komórki, które są wyświetlane. Ostatnią komórkę wyświetla żadnych danych wyjściowych.  W największych komórce kodu, zobacz `run.log` używany w kilku miejscach. Każdy `run.log` dodaje wartość do swojego obszaru roboczego.


## <a name="view-logged-values"></a>Wyświetlanie zarejestrowanych wartości

1. Dane wyjściowe z komórki `run` zawierają link do witryny Azure Portal, dzięki któremu można wyświetlić wyniki eksperymentu w obszarze roboczym. 

    ![Wyświetlanie eksperymentów](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Kliknij przycisk **Link do witryny Azure portal** Aby wyświetlić informacje o przebiegu w obszarze roboczym.  Ten link powoduje otwarcie obszaru roboczego w witrynie Azure Portal.

1. Wykresy zarejestrowanych wartości, które zobaczysz, zostały automatycznie utworzone w obszarze roboczym. Zawsze w przypadku zarejestrowania wielu wartości z tym samym parametrem nazwa wykres jest generowany automatycznie.

   ![Wyświetlanie historii](./media/quickstart-run-cloud-notebook/web-results.png)

Kod obliczania przybliżonej liczby pi używa wartości losowych, dlatego wykresy będą przedstawiać różne wartości.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

### <a name="stop-the-notebook-vm"></a>Zatrzymaj notesu maszyny Wirtualnej

Zatrzymaj notesu maszyny Wirtualnej, gdy nie jest ona używana do zmniejszenia kosztów.  

1. W obszarze roboczym, wybierz **maszyn wirtualnych z notesu**.

   ![Zatrzymywanie maszyny Wirtualnej serwera](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Z listy wybierz maszynę wirtualną.

1. Wybierz **zatrzymać**.

1. Gdy skończysz użytkownikowi serwer ponownie, wybierz **Start**.

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start możesz wykonać te zadania:

* Tworzenie notesu maszyny Wirtualnej
* Uruchom serwer notesu Jupyter w notesie maszyny Wirtualnej
* Otwórz notes, który zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji.
* Uruchamianie notesu.
* Wyświetl wartości błędów rejestrowane w obszarze roboczym.  W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym trenowania i wdrażania modelu w usłudze Machine Learning:  

> [!div class="nextstepaction"]
> [Samouczek: trenowanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)