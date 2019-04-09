---
title: Przewodnik Szybki Start Uruchom Notes w chmurze
titleSuffix: Azure Machine Learning service
description: Rozpocznij pracę z usługą Azure Machine Learning. Używanie serwera zarządzanego Notes w chmurze, możesz wypróbować obszaru roboczego.  Twój obszar roboczy jest podstawowe bloku w chmurze, którego używasz do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd643185fb4647b19082980edfd333c507aab8a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266260"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwer opartych na chmurze notesu

Utwórz serwer opartych na chmurze Notes, a następnie użyj go, aby uruchomić kod, który rejestruje wartości w usłudze Azure Machine Learning [obszaru roboczego](concept-azure-machine-learning-architecture.md). Twój obszar roboczy jest podstawowe bloku w chmurze, którego używasz do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego przy użyciu usługi Machine Learning. 

Ten przewodnik Szybki Start przedstawiono sposób tworzenia zasobu chmury w obszarze roboczym usługi Azure Machine Learning skonfigurowany ze środowiskiem Python niezbędne do uruchomienia usługi Azure Machine Learning. Aby użyć własnego środowiska, zobacz [Szybki Start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwera notesu](quickstart-run-local-notebook.md).  
 
W tym przewodniku Szybki start wykonasz następujące czynności:

* Tworzenie stacji roboczej
* Uruchom serwer notesu Jupyter na stacji roboczej
* Otwórz notes, który zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji.
* Uruchamianie notesu.
* Wyświetl wartości błędów rejestrowane w obszarze roboczym.  W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Tworzenie obszaru roboczego usługi Azure Machine Learning](setup-create-workspace.md#portal) Jeśli nie masz.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  Jeśli nie masz pewności, jak znaleźć obszaru roboczego w portalu, zobacz temat jak [Znajdź obszar roboczy](how-to-manage-workspace.md#view).

## <a name="create-a-workstation"></a>Tworzenie stacji roboczej 

Stacja robocza Notes umożliwia platformy z opartych na chmurze dla notesów programu Jupyter, który został wstępnie skonfigurowany z wszystko, czego potrzebujesz, aby uruchomić usługę Azure Machine Learning. Z obszaru roboczego można utworzyć tej platformie, aby rozpocząć korzystanie z notesów programu Jupyter.

1. Na stronie obszaru roboczego w witrynie Azure portal, wybierz **stacji roboczej notesu** po lewej stronie.

1. Wybierz **tworzenie notesów w stacji roboczej usługi Azure Machine Learning (wersja zapoznawcza)**

   ![Eksplorowanie obszaru roboczego](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. **Stacje robocze notesu** sekcja pokazuje listę dostępnych serwerów opartych na chmurze Notes w obszarze roboczym.  W tym miejscu możesz zarządzać tymi zasobami i usuwać je, gdy nie są już potrzebne. 

1. Wybierz **Dodawanie stacji roboczej** Aby utworzyć notes stacji roboczej.

     ![Wybieranie opcji dodawania stacji roboczej](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. W sekcji Dodaj stacji roboczej notesu Nadaj swojej stacji roboczej **nazwa obliczeniowego** i wybierz **typ obliczeń**. Następnie wybierz przycisk **Utwórz**.

    ![Tworzenie nowej stacji roboczej](media/quickstart-run-cloud-notebook/create-new-workstation.png)

    > [!NOTE]
    > Swojej stacji roboczej zajmuje około dwóch minut, aby utworzyć. Po zakończeniu aktualizacji stanu na "Uruchomiona" i zawiera łącza do aplikacji Jupyter i JupyterLab są wyświetlane.

## <a name="launch-jupyter-web-interface"></a>Uruchom interfejs sieci web programu Jupyter

Po utworzeniu stacji roboczej za pomocą sekcji notesu stacji roboczych do uruchamiania interfejsu sieci web programu Jupyter.

* Wybierz **Jupyter** lub **laboratorium Jupyter** w **Uruchom** kolumny dla stacji roboczej.

    ![Uruchom serwer notesu Jupyter](./media/quickstart-run-cloud-notebook/start-notebook-server.png)

    Spowoduje to uruchomienie serwera Notes i zostanie otwarta strona główna serwera w nowej karcie przeglądarki.  Serwer zawiera przykładowe notesów, możesz użyć, aby rozpocząć pracę z usługą Azure Machine Learning.

### <a name="run-the-notebook"></a>Uruchamianie notesu

Uruchamianie notesu, szacuje pi, która rejestruje błąd w Twoim obszarze roboczym.

1. Wybierz plik **01.run experiment.ipynb**, aby otworzyć notes.

1. W obszarze stanu jest widoczna informacja o tym, że musisz zaczekać na uruchomienie jądra.  Komunikat zniknie, gdy jądro będzie gotowe.

    ![Oczekiwanie na uruchomienie jądra](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Gdy jądro zacznie działać, przy użyciu kombinacji klawiszy **Shift + Enter** uruchom kolejno poszczególne komórki. Możesz też wybrać kolejno pozycje **Komórki** > **Uruchom wszystkie** w celu uruchomienia całego notesu. Po wyświetleniu znak gwiazdki (__*__) obok komórki, komórki jest nadal uruchomiona. Po zakończeniu działania kodu tej komórki pojawi się liczba.  

Po zakończeniu uruchamiania wszystkich komórek w notesie możesz wyświetlić zarejestrowane wartości w swoim obszarze roboczym.

## <a name="view-logged-values"></a>Wyświetlanie zarejestrowanych wartości

1. Dane wyjściowe z komórki `run` zawierają link do witryny Azure Portal, dzięki któremu można wyświetlić wyniki eksperymentu w obszarze roboczym. 

    ![Wyświetlanie eksperymentów](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Kliknij przycisk **Link do witryny Azure portal** Aby wyświetlić informacje o przebiegu w obszarze roboczym.  Ten link powoduje otwarcie obszaru roboczego w witrynie Azure Portal.

1. Wykresy zarejestrowanych wartości, które zobaczysz, zostały automatycznie utworzone w obszarze roboczym. Zawsze w przypadku zarejestrowania wielu wartości z tym samym parametrem nazwa wykres jest generowany automatycznie.

   ![Wyświetlanie historii](./media/quickstart-run-cloud-notebook/web-results.png)

Kod obliczania przybliżonej liczby pi używa wartości losowych, dlatego wykresy będą przedstawiać różne wartości.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start możesz wykonać następujące czynności:

* Tworzenie stacji roboczej
* Uruchom serwer notesu Jupyter na stacji roboczej
* Otwórz notes, który zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji.
* Uruchamianie notesu.
* Wyświetl wartości błędów rejestrowane w obszarze roboczym.  W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym trenowania i wdrażania modelu w usłudze Machine Learning:  

> [!div class="nextstepaction"]
> [Samouczek: Szkolenie modeli klasyfikacji obrazów](tutorial-train-models-with-aml.md)
