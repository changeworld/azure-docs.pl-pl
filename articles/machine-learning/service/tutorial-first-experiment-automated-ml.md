---
title: Utwórz pierwszy zautomatyzowany eksperyment uczenia maszynowego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wyszkolić i wdrożyć model klasyfikacji przy użyciu automatycznego uczenia maszynowego na stronie docelowej obszaru roboczego Azure Machine Learning (wersja zapoznawcza).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 0dd4447736469644875dff914c6284b087be87d0
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910219"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Samouczek: Tworzenie pierwszego modelu klasyfikacji przy użyciu automatycznej uczenia maszynowego

W tym samouczku dowiesz się, jak utworzyć pierwszy zautomatyzowany eksperyment uczenia maszynowego za pomocą strony docelowej obszaru roboczego (wersja zapoznawcza) bez konieczności pisania pojedynczego wiersza kodu. Ten przykład tworzy model klasyfikacji, aby przewidzieć, czy klient zasubskrybuje stały termin wpłaty z instytucją finansową.

Dzięki zautomatyzowanej usłudze Machine Learning można zautomatyzować czasochłonne zadania. Automatyczne Uczenie maszynowe szybko iteruje wiele kombinacji algorytmów i parametrów, aby ułatwić znalezienie najlepszego modelu w oparciu o pomyślną metrykę wybrania.

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Utwórz obszar roboczy usługi Azure Machine Learning.
> * Uruchom zautomatyzowany eksperyment uczenia maszynowego.
> * Wyświetl szczegóły eksperymentu.
> * Wdróż model.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Pobierz plik danych [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . Kolumna **y** wskazuje, czy klient subskrybuje stały termin wpłaty, który jest później zidentyfikowany jako kolumna docelowa dla prognoz w tym samouczku. 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze. 

Aby zarządzać zasobami platformy Azure, można utworzyć obszar roboczy za pośrednictwem Azure Portal konsoli internetowej. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 

## <a name="create-and-run-the-experiment"></a>Tworzenie i uruchamianie eksperymentu

Wykonaj następujące kroki konfiguracji i uruchamiania na stronie docelowej obszaru roboczego, skonsolidowany interfejs, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych o wszystkich poziomach umiejętności.

1. Zaloguj się do [strony docelowej obszaru roboczego](https://ml.azure.com/workspaceportal/).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz pozycję **Rozpocznij**.

1.  Wybierz pozycję **zautomatyzowany ml** w sekcji **Tworzenie** w okienku po lewej stronie.
Zobaczysz ekran **wprowadzenie** , ponieważ jest to pierwszy eksperyment z automatycznym Machine Learningem.

    ![Studio uczenia maszynowego Azure](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Wybierz pozycję **Utwórz eksperyment**. 

1. Wprowadź **My-1-automl-eksperyment** jako nazwę eksperymentu.

1. Wybierz pozycję **Utwórz nowe obliczenie**. 

    1. Skonfiguruj kontekst obliczeniowy dla tego eksperymentu.
        
        Pole | Value
        ----|---
        Nazwa środowiska obliczeniowego |  Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy. W tym przykładzie należy użyć **automl-COMPUTE**.
        Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia. Użyj domyślnej **Standard_DS12_V2**.
        Ustawienia dodatkowe| *Minimalny węzeł*: 1. Aby włączyć Profilowanie danych, musisz mieć co najmniej jeden węzeł. <br> *Maksymalny węzeł*: 6.
 
    1. Aby utworzyć nowe obliczenie, wybierz pozycję **Utwórz**. Wykonanie tej czynności może zająć kilka minut. 

    1. Po zakończeniu tworzenia wybierz nowe obliczenie z listy rozwijanej, a następnie wybierz przycisk **dalej**.

    >[!NOTE]
    >W ramach tego samouczka będziesz używać domyślnego konta magazynu i kontenera utworzonego przy użyciu nowego obliczenia. Wypełniają one automatycznie w formularzu.

1. Wybierz pozycję **Przekaż z pliku lokalnego**. W tym miejscu utworzysz nowy zestaw danych z plikiem **bankmarketing_train. csv** , który został wcześniej pobrany dla tego samouczka. 

    1. Wybierz pozycję **Przeglądaj** , a następnie wybierz plik **bankmarketing_train. csv** na komputerze lokalnym. 

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** , aby przekazać ją do domyślnego kontenera, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego. Publiczna wersja zapoznawcza obsługuje tylko lokalne operacje przekazywania plików. 

    1. Po zakończeniu przekazywania **Ustawienia i formularz podglądu** są inteligentnie wypełniane na podstawie typu pliku. Upewnij się, że formularz jest wypełniony w następujący sposób.
        
        Pole|Value
        ---|---
        Format pliku| Lista
        Ogranicznik| Przecinek
        Kodowanie| UTF-8
        Nagłówki kolumn| Wszystkie pliki mają te same nagłówki pomijają wiersze | Brak

        >[!NOTE]
        > W przypadku zaktualizowania dowolnego z ustawień w tym formularzu wersja zapoznawcza będzie odpowiednio aktualizowana.

        Wybierz opcję **Dalej**.
    

    1. Formularz **schematu** umożliwia dalsze Konfigurowanie danych na potrzeby tego eksperymentu. Na potrzeby tego przykładu wybierz przełącznik przełącznika dla funkcji **day_of_week** , tak aby nie obejmował go dla tego eksperymentu. Wybierz pozycję **gotowe**, aby zakończyć przekazywanie plików i tworzenie zestawu danych dla eksperymentu.

        ![Konfiguracja karty podglądu](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Wybierz pozycję **Klasyfikacja** jako zadanie przewidywania.

1. Zaznacz opcję **y** jako kolumnę docelową, którą chcesz przewidzieć. Ta kolumna wskazuje, czy klient subskrybuje termin depozytowy.

1. Rozwiń pozycję **Ustawienia zaawansowane** i wypełnij pola w następujący sposób.

    Ustawienia zaawansowane|Value
    ------|------
    Metryka podstawowa| AUC_weighted 
    Kryteria wyjścia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zakończone przed ukończeniem ukończenia: <br> *Czas zadania szkoleniowego (minuty)* : 5  <br> *Maksymalna liczba iteracji*: 10 
    Przetwarzania wstępnego| Umożliwia przetwarzanie wstępne wykonywane przez automatyczne Uczenie maszynowe. Obejmuje to automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych.
    Weryfikacja| Wybierz pozycję K — złóż krzyżowe sprawdzanie poprawności i **2** , aby uzyskać liczbę operacji krzyżowych. 
    Współbieżność| Wybierz **5** dla liczby maksymalnych współbieżnych iteracji.

   >[!NOTE]
   > Dla tego eksperymentu nie ustawisz metryki lub maksymalna liczba rdzeni na wartość progową iteracji. Nie można też blokować przetestowanie algorytmów.

1. Wybierz pozycję **Rozpocznij** , aby uruchomić eksperyment.

   Po rozpoczęciu eksperymentu na początku zobaczysz pusty ekran z komunikatem o stanie.

Proces przygotowywania eksperymentu trwa kilka minut. Po zakończeniu tego procesu **zostanie uruchomiony**komunikat o stanie.

##  <a name="view-experiment-details"></a>Wyświetl szczegóły eksperymentu

W miarę postępu eksperymentu ekran aktualizuje **Wykres iteracji** i **listę iteracji** przy użyciu różnych iteracji (modeli), które są uruchamiane. Lista iteracji jest uporządkowana według oceny metryki. Domyślnie model, który ocenia najwyższy poziom w oparciu o metrykę **AUC_weighted** , znajduje się w górnej części listy.

>[!WARNING]
> Zadania szkoleniowe Poświęć kilka minut na zakończenie działania każdego potoku.

[![Pulpit nawigacyjny szczegółów uruchamiania](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Za pomocą funkcji automatycznego uczenia maszynowego na stronie docelowej obszaru roboczego można wdrożyć najlepszy model jako usługę sieci Web, aby przewidzieć nowe dane i zidentyfikować potencjalne obszary szansy sprzedaży. W przypadku tego eksperymentu wdrożenie oznacza, że instytucja finansowa ma teraz iteracyjne i skalowalne rozwiązanie do identyfikowania potencjalnych klientów z krótkoterminowymi wpłatami.

W tym kontekście eksperymentu **VotingEnsemble** jest uznawany za najlepszy model w oparciu o metrykę **AUC_weighted** .  Wdrażamy ten model, ale zaleca się wdrożenie trwa około 20 minut.

1. Na stronie **szczegóły uruchamiania** wybierz przycisk **Wdróż najlepszy model** w prawym górnym rogu.

1. Wypełnij okienko **Wdróż najlepszy model** w następujący sposób:

    Pole| Value
    ----|----
    Nazwa wdrożenia| My-automl-Deploy
    Opis wdrożenia| Moje pierwsze zautomatyzowane wdrożenie eksperymentu w usłudze Machine Learning
    Skrypt oceniania| Automatyczne generowanie
    Skrypt środowiska| Automatyczne generowanie
    
1. Wybierz pozycję **Wdróż**.

    Po pomyślnym zakończeniu wdrażania zostanie wyświetlony komunikat o ukończeniu wdrożenia.
    
Teraz masz działającą usługę sieci Web do generowania prognoz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pliki wdrożeń są większe niż pliki danych i eksperymenty, dzięki czemu są one droższe do przechowywania. Usuń tylko pliki wdrożenia, aby zminimalizować koszty dla konta, lub jeśli chcesz zachować obszar roboczy i pliki eksperymentów. W przeciwnym razie Usuń całą grupę zasobów, jeśli nie planujesz używać żadnego z tych plików.  

### <a name="delete-the-deployment-instance"></a>Usuwanie wystąpienia wdrożenia

Usuń tylko wystąpienie wdrożenia z Azure Portal, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com//). Przejdź do obszaru roboczego i po lewej stronie w okienku **zasoby** wybierz pozycję **wdrożenia**. 

1. Wybierz wdrożenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń**. 

1. Wybierz pozycję **Zastosuj**.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanym uczenia maszynowego została użyta Strona docelowa obszaru roboczego do utworzenia i wdrożenia modelu klasyfikacji. Zobacz następujące artykuły, aby uzyskać więcej informacji i następnych kroków:

> [!div class="nextstepaction"]
> [Korzystanie z usługi sieci Web](how-to-consume-web-service.md)

+ Dowiedz się więcej na temat [przetwarzania wstępnego](how-to-create-portal-experiments.md#preprocess).
+ Dowiedz się więcej na temat [profilowania danych](how-to-create-portal-experiments.md#profile).
+ Dowiedz się więcej o [automatycznym uczeniu maszynowym](concept-automated-ml.md).

>[!NOTE]
> Ten zestaw danych marketingu dla [banku jest udostępniany w ramach Creative Commons Attribution (CCO: Domena publiczna)](https://creativecommons.org/publicdomain/zero/1.0/). Wszystkie prawa do poszczególnych treści bazy danych są licencjonowane w ramach [licencji na zawartość bazy danych](https://creativecommons.org/publicdomain/zero/1.0/) i dostępne w witrynie [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ten zestaw danych był początkowo dostępny w [bazie danych Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez i P. Rita. Oparte na danych podejście do przewidywania sukcesu telemarketingu bankowego. Systemy pomocy technicznej, Elsevier, 62:22-31 czerwca 2014.
