---
title: Utwórz pierwszy zautomatyzowany eksperyment uczenia maszynowego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak nauczyć i wdrożyć model klasyfikacji przy użyciu automatycznej uczenia maszynowego w Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 30dc731efdb6016f505b7a16860e0cb9c6480333
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716595"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>Samouczek: Używanie automatycznej uczenia maszynowego do uczenia i wdrożenia pierwszego modelu klasyfikacji (wersja zapoznawcza)

W tym samouczku dowiesz się, jak utworzyć pierwszy zautomatyzowany eksperyment uczenia maszynowego w Azure Portal. Ten przykład tworzy model klasyfikacji, aby przewidzieć, czy klient będzie subskrybował termin depozytowy w banku. 

Dzięki automatycznym funkcjom uczenia maszynowego usługi i Azure Portal uruchamiasz zautomatyzowany proces uczenia maszynowego, a wybór algorytmu i dostrajanie parametrów są wykonywane. Technika automatycznej uczenia maszynowego wykonuje iterację wielu kombinacji algorytmów i parametrów do momentu znalezienia najlepszego modelu na podstawie kryterium, bez konieczności pisania jednego wiersza kodu.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego usługi Azure Machine Learning.
> * Utwórz eksperyment.
> * Autouczenie modelu klasyfikacji.
> * Wyświetl szczegóły przebiegu szkoleniowego.
> * Wdróż model.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Plik danych **bankmarketing_train. csv** . Pobierz go [tutaj](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

1. Przejdź do lewego okienka obszaru roboczego. Wybierz pozycję **automatyczne Machine Learning** w sekcji **Tworzenie (wersja zapoznawcza)** .

    ![Azure Portal okienku nawigacji](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Ponieważ jest to pierwszy eksperyment z automatycznym Machine Learningem, zobaczysz ekran **Witamy w Machine Learning automatycznym** . 

1.  Wybierz pozycję **Utwórz eksperyment**. Następnie wprowadź **My-1-automl-eksperyment** jako nazwę eksperymentu.

1. Wybierz pozycję **Utwórz nowe obliczenie** i skonfiguruj kontekst obliczeniowy dla tego eksperymentu.

    Pole| Value
    ---|---
    Nazwa środowiska obliczeniowego| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy. W tym przykładzie używamy **automl-COMPUTE**.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia. Korzystamy z **Standard_DS12_V2**.
    Ustawienia dodatkowe| *Minimalny węzeł*: 1. Aby włączyć Profilowanie danych, musisz mieć co najmniej jeden węzeł. <br> *Maksymalny węzeł*: 6. 

    Aby rozpocząć tworzenie nowego obliczenia, wybierz pozycję **Utwórz**. Może to potrwać kilka minut. 

    Po zakończeniu tworzenia wybierz nowe obliczenie z listy rozwijanej, a następnie wybierz przycisk **dalej**.

1. W tym samouczku używamy domyślnego konta magazynu i kontenera utworzonego przy użyciu nowego obliczenia. To spowoduje automatyczne wypełnienie formularza.

1. Wybierz pozycję **Przekaż** , aby wybrać plik **bankmarketing_train. csv** z komputera lokalnego i przekazać go do kontenera domyślnego. Publiczna wersja zapoznawcza obsługuje tylko lokalne operacje przekazywania plików i konta usługi Azure Blob Storage. Po zakończeniu przekazywania wybierz plik z listy. 

    [![Wybierz plik danych](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. Karta **Podgląd** pozwala na dalsze Konfigurowanie naszych danych dla tego eksperymentu.

    Na karcie Podgląd wskaż, że dane obejmują nagłówki. Usługa domyślnie obejmuje wszystkie funkcje (kolumny) do szkoleń. Na potrzeby tego przykładu przewiń w prawo i **zignoruj** funkcję **day_of_week** .

    ![Konfiguracja karty podglądu](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Profilowanie danych nie jest dostępne w przypadku obliczeń, które mają 0 minimalnych węzłów.

1. Wybierz pozycję **Klasyfikacja** jako zadanie przewidywania.

1. Wybierz wartość **y** jako kolumnę docelową, w kolumnie, w której chcesz przeprowadzić prognozę. Ta kolumna wskazuje, czy klient subskrybuje termin depozytowy.

1. Rozwiń **Ustawienia zaawansowane** i wypełnij pola w następujący sposób.

    Ustawienia zaawansowane|Value
    ------|------
    Metryka podstawowa| AUC_weighted 
    Kryteria wyjścia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zakończone przed pełnym ukończeniem. <br> *Czas zadania szkoleniowego (minuty)* : 5  <br> *Maksymalna liczba iteracji*: 10 
    Przetwarzania wstępnego| Włącz przetwarzanie wstępne wykonywane przez automatyczne Uczenie maszynowe. Obejmuje to automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych.
    Weryfikacja| Wybierz pozycję K — złóż krzyżowe sprawdzanie poprawności i 2 dla liczby operacji krzyżowych. 
    Współbieżność| W polu Maksymalna liczba współbieżnych iteracji wybierz pozycję 5.

   >[!NOTE]
   > Dla tego eksperymentu nie ustawimy wartości progowej metryki lub maksymalnej liczby iteracji, a nie blokuj algorytmy.

1. Kliknij przycisk **Uruchom** , aby uruchomić eksperyment.

   Po rozpoczęciu eksperymentu zobaczysz pusty ekran **szczegóły uruchamiania** z następującym stanem u góry. Proces przygotowywania eksperymentu trwa kilka minut. Po zakończeniu procesu przygotowania komunikat o stanie **zostanie uruchomiony**.

      ![Uruchom przygotowywanie](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>Wyświetl szczegóły eksperymentu

W miarę postępu eksperymentu ekran **szczegóły uruchamiania** aktualizuje wykres iteracji i listę przy użyciu różnych iteracji (modeli), które są uruchamiane. Lista iteracji jest uporządkowana według oceny metryki i domyślnie model, który otrzymuje najwyższą wartość opartą na naszej **AUC_weighted** , znajduje się na górze listy.

>[!TIP]
> Ukończenie zadań szkoleniowych może potrwać kilka minut.

[![Pulpit nawigacyjny szczegółów uruchamiania](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>Wdrażanie modelu

Dla tego eksperymentu **VotingEnsemble** jest uznawany za najlepszy model oparty na metryce **AUC_weighted** . Dzięki automatycznemu uczeniu maszynowego w Azure Portal można wdrożyć ten model jako usługę sieci Web, aby przewidzieć nowe dane za pomocą jednego kliknięcia. 

1. Na stronie **szczegóły uruchamiania** wybierz przycisk **Wdróż najlepszy model** .

1. Wypełnij okienko **Wdróż najlepszy model** w następujący sposób:

    Pole| Value
    ----|----
    Nazwa wdrożenia| My-automl-Deploy
    Opis wdrożenia| Moje pierwsze zautomatyzowane wdrożenie eksperymentu w usłudze Machine Learning
    Skrypt oceniania| Automatyczne generowanie
    Skrypt środowiska| Automatyczne generowanie
    
1. Wybierz pozycję **Wdróż**. Wdrożenie może potrwać około 20 minut.

    Po pomyślnym zakończeniu wdrażania zostanie wyświetlony następujący komunikat.

    ![Wdrażanie ukończone](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    To wszystko! Masz działającą usługę sieci Web do generowania prognoz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pliki wdrożeń są większe niż pliki danych i eksperymentów, co oznacza, że są one droższe do przechowywania. Usuń tylko pliki wdrożenia, aby zminimalizować koszty dla konta, a jeśli chcesz zachować obszar roboczy i pliki eksperymentów. W przeciwnym razie Usuń całą grupę zasobów, jeśli nie planujesz używać żadnego z tych plików.  

### <a name="delete-deployment-instance"></a>Usuń wystąpienie wdrożenia

Usuń tylko wystąpienie wdrożenia z Azure Portal, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do okienka **zasoby** po lewej stronie i wybierz pozycję **wdrożenia**. 

1. Wybierz wdrożenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń**. 

1. Wybierz pozycję **Zastosuj**.

### <a name="delete-resource-group"></a>Usuń grupę zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanym uczenia maszynowego Azure Portal utworzyć i wdrożyć model klasyfikacji. Zobacz te artykuły, aby uzyskać więcej informacji i następnych kroków.

+ Jak [korzystać z usługi sieci Web](how-to-consume-web-service.md).
+ Dowiedz się więcej na temat [przetwarzania wstępnego](how-to-create-portal-experiments.md#preprocess).
+ Dowiedz się więcej na temat [profilowania danych](how-to-create-portal-experiments.md#profile).
+ Dowiedz się [](concept-automated-ml.md)więcej o automatycznym uczeniu maszynowym.

>[!NOTE]
> Ten zestaw danych marketingu dla [banku jest udostępniany w ramach Creative Commons Attribution (CCO: Domena publiczna)](https://creativecommons.org/publicdomain/zero/1.0/). Wszystkie prawa w poszczególnych zawartości bazy danych są licencjonowane w ramach [licencji zawartości bazy danych](https://creativecommons.org/publicdomain/zero/1.0/) i są dostępne w witrynie [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ten zestaw danych jest początkowo dostępny w [bazie danych Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
>  Zapoznaj się z następującymi zadaniami: <br> [Moro et al., 2014] S. Moro, P. Cortez i P. Rita. Oparte na danych podejście do przewidywania sukcesu telemarketingu bankowego. Systemy pomocy technicznej, Elsevier, 62:22-31 czerwca 2014.
