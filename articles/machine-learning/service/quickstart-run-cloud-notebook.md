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
ms.openlocfilehash: 0672d90a25bc4c879d28512ab212f98f29efbf3b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358221"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwer opartych na chmurze notesu

W tym artykule używasz notesów usługi Azure, aby uruchomić kod, który jest zalogowany w usłudze Azure Machine Learning [obszaru roboczego](concept-azure-machine-learning-architecture.md). Twój obszar roboczy jest podstawowe bloku w chmurze, którego używasz do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego przy użyciu usługi Machine Learning. 

Ten przewodnik Szybki start korzysta z zasobów w chmurze i nie wymaga żadnej instalacji. Aby użyć własnego środowiska, zobacz [Szybki Start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwera notesu](quickstart-run-local-notebook.md).  
 
W tym przewodniku Szybki start wykonasz następujące czynności:

* Połącz się z obszarem roboczym przy użyciu języka Python w notesie Jupyter. Notes, zawiera kod, aby oszacować pi i rejestruje błędy w każdej iteracji. 
* Wyświetl wartości błędów rejestrowane w obszarze roboczym.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisite"></a>Wymagania wstępne

1. [Tworzenie obszaru roboczego usługi Azure Machine Learning](setup-create-workspace.md#portal) Jeśli nie masz.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  Zobacz jak [Znajdź obszar roboczy](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Używania Twojego obszaru roboczego

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Dowiedz się, jak obszar roboczy ułatwia zarządzanie skryptów uczenia maszynowego. W tej sekcji wykonasz następujące kroki:

* Otwieranie notesu w usłudze Azure Notebooks.
* Uruchamianie kodu, który tworzy niektóre rejestrowane wartości.
* Wyświetlanie zarejestrowanych wartości w obszarze roboczym.

W tym przykładzie pokazano, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

### <a name="open-a-notebook"></a>Otwieranie notesu 

Usługa [Azure Notebooks](https://notebooks.azure.com) udostępnia bezpłatną platformę w chmurze do przechowywania notesów Jupyter, które są wstępnie skonfigurowane do obsługi wszystkich elementów potrzebnych, aby uruchomić usługę Machine Learning. W obszarze roboczym można uruchomić tej platformie, aby rozpocząć korzystanie z obszaru roboczego usługi Azure Machine Learning.

1. Na stronie Omówienie obszaru roboczego wybierz **Rozpoczynanie pracy notesów usługi Azure** próby swoje pierwsze eksperymentalne rozwiązanie w notesach platformy Azure.  Azure Notebooks to oddzielna usługa, która umożliwia bezpłatne uruchamianie notesów programu Jupyter w chmurze.  Jeśli użyjesz tego linku do usługi, informacje o sposobie łączenia się z obszarem roboczym zostaną dodane do biblioteki tworzonej w usłudze Azure Notebooks.

   ![Eksplorowanie obszaru roboczego](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Zaloguj się do usługi Azure Notebooks.  Upewnij się, że logujesz się przy użyciu tego samego konta, którego używasz do logowania się do witryny Azure Portal. Organizacja może wymagać [zgody administratora](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) przed zalogowaniem się.

1. Po zalogowaniu zostanie otwarta nowa karta z wyświetlonym monitem `Clone Library`. Sklonowanie tej biblioteki spowoduje załadowanie zestawu notesów i innych plików na konto usługi Azure Notebooks.  Te pliki pomogą Ci w eksplorowaniu możliwości usługi Azure Machine Learning.

1. Usuń zaznaczenie pola wyboru **Publiczne**, aby nie udostępniać informacji o obszarze roboczym innym osobom.

1. Wybierz pozycję **Klonuj**.

   ![Klonowanie biblioteki](./media/quickstart-run-cloud-notebook/clone.png)

1. Jeśli zobaczysz, że projekt jest w stanie Zatrzymano, kliknij pozycję **Uruchom w ramach bezpłatnych obliczeń**, aby korzystać z bezpłatnego serwera notesu.

    ![Uruchamianie projektu w ramach bezpłatnych obliczeń](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>Uruchamianie notesu

Na liście plików tego projektu zobaczysz plik `config.json`. Ten plik konfiguracji zawiera informacje o obszarze roboczym utworzonym w witrynie Azure Portal.  Ten plik umożliwia kodowi nawiązywanie połączenia i dodawanie informacji do obszaru roboczego.

1. Wybierz plik **01.run experiment.ipynb**, aby otworzyć notes.

1. W obszarze stanu jest widoczna informacja o tym, że musisz zaczekać na uruchomienie jądra.  Komunikat zniknie, gdy jądro będzie gotowe.

    ![Oczekiwanie na uruchomienie jądra](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Gdy jądro zacznie działać, przy użyciu kombinacji klawiszy **Shift + Enter** uruchom kolejno poszczególne komórki. Możesz też wybrać kolejno pozycje **Komórki** > **Uruchom wszystkie** w celu uruchomienia całego notesu. Gdy obok komórki pojawi się znak gwiazdki, __*__, oznacza to, że komórka jest nadal uruchomiona. Po zakończeniu działania kodu tej komórki pojawi się liczba. 

1. Postępuj zgodnie z instrukcjami w notesie w celu uwierzytelnienia subskrypcji platformy Azure.

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

Utworzono zasoby umożliwiające eksperymentowanie i wdrażanie modeli. Uruchomiono też kod w notesie. Zbadano historię przebiegów dotyczącą tego kodu w obszarze roboczym w chmurze.

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym trenowania i wdrażania modelu w usłudze Machine Learning:  

> [!div class="nextstepaction"]
> [Samouczek: Szkolenie modeli klasyfikacji obrazów](tutorial-train-models-with-aml.md)