---
title: Utwórz pierwszy zautomatyzowany eksperyment uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyszkolić i wdrożyć model klasyfikacji przy użyciu automatycznego uczenia maszynowego na stronie docelowej obszaru roboczego Azure Machine Learning (wersja zapoznawcza).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 38c319fb89e8c763f8231c18cbb59bef099193e2
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259329"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Samouczek: Tworzenie pierwszego modelu klasyfikacji przy użyciu automatycznej uczenia maszynowego

W tym samouczku dowiesz się, jak utworzyć pierwszy zautomatyzowany eksperyment uczenia maszynowego za pomocą strony docelowej obszaru roboczego (wersja zapoznawcza) bez konieczności pisania pojedynczego wiersza kodu. Ten przykład tworzy model klasyfikacji, aby przewidzieć, czy klient zasubskrybuje stały termin wpłaty z instytucją finansową.

Dzięki zautomatyzowanej usłudze Machine Learning można zautomatyzować czasochłonne zadania. Automatyczne Uczenie maszynowe szybko iteruje wiele kombinacji algorytmów i parametrów, aby ułatwić znalezienie najlepszego modelu w oparciu o pomyślną metrykę wybrania.

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Machine Learning.
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

1. W lewym okienku wybierz pozycję **zautomatyzowany ml** w sekcji **Tworzenie** .

   Ponieważ jest to pierwszy zautomatyzowany eksperyment z ML, zobaczysz ekran Wprowadzenie.

   ![Studio uczenia maszynowego Azure](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Wybierz pozycję **Utwórz eksperyment**. 

1. Wprowadź nazwę tego eksperymentu:`my-1st-automl-experiment`

1. Wybierz pozycję **Utwórz nowe obliczenie** i skonfiguruj obiekt docelowy obliczeń. Obiekt docelowy obliczeń to lokalne lub oparte na chmurze środowisko zasobów używane do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. Na potrzeby tego eksperymentu używamy obliczeń opartych na chmurze. 

   Pole | Opis | Wartość dla samouczka
   ----|---|---
   Nazwa obliczeń |Unikatowa nazwa identyfikująca kontekst obliczeniowy.|automl — obliczenia
   Rozmiar&nbsp;maszyny&nbsp;wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.|Standard_DS12_V2
   Minimalna/Maksymalna liczba węzłów (w ustawieniach zaawansowanych)| Aby profilować dane, musisz określić co najmniej jeden węzeł.|Minimalna liczba węzłów: 1<br>Maksymalna liczba węzłów: 6

   >[!NOTE]
   >W ramach tego samouczka będziesz używać domyślnego konta magazynu i kontenera utworzonego przy użyciu nowego obliczenia. Wypełniają one automatycznie w formularzu.
    
1. Wybierz pozycję **Utwórz** , aby uzyskać obiekt docelowy obliczeń. 

   **Wykonanie tej czynności może zająć kilka minut.** 

1. Po utworzeniu wybierz nowe miejsce docelowe obliczeń z listy rozwijanej i wybierz pozycję **dalej**.

1. Wybierz pozycję **Przekaż z pliku lokalnego** , aby rozpocząć tworzenie nowego zestawu danych. 

    1. Wybierz pozycję **Przeglądaj**.
    
    1. Wybierz plik **bankmarketing_train. csv** na komputerze lokalnym. Jest to plik pobrany jako [warunek wstępny](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** w lewym dolnym rogu, aby przekazać ją do domyślnego kontenera, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego. Publiczna wersja zapoznawcza obsługuje tylko lokalne operacje przekazywania plików. 
    
       Po zakończeniu przekazywania ustawienia i formularz podglądu są wstępnie wypełniane na podstawie typu pliku. 
       
    1. Sprawdź, czy **Ustawienia i formularz podglądu** są wypełnione w następujący sposób, a następnie wybierz przycisk **dalej**.
        
        Pole|Wartość dla samouczka
        ---|---
        Format pliku| Rozdzielane
        Ogranicznik| Przecinek
        Kodowanie| UTF-8
        Nagłówki kolumn| Wszystkie pliki mają te same nagłówki
        Pomiń wiersze | Brak
    
    1. Formularz **schematu** umożliwia dalsze Konfigurowanie danych na potrzeby tego eksperymentu. Na potrzeby tego przykładu wybierz przełącznik przełącznika dla funkcji **day_of_week** , tak aby nie obejmował go dla tego eksperymentu. Wybierz pozycję **gotowe**, aby zakończyć przekazywanie plików i tworzenie zestawu danych dla eksperymentu.

        ![Konfiguracja karty podglądu](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Wybierz pozycję **Klasyfikacja** jako zadanie przewidywania.

1. Zaznacz opcję **y** jako kolumnę docelową, którą chcesz przewidzieć. Ta kolumna wskazuje, czy klient subskrybuje termin depozytowy.

1. Rozwiń pozycję **Ustawienia zaawansowane** i wypełnij pola w następujący sposób.

   >[!NOTE]
   > W tym samouczku nie ustawisz oceny metryki lub maksymalnej liczby rdzeni na wartość progową iteracji. Nie można natomiast blokować algorytmów.
   
   Ustawienia&nbsp;zaawansowane|Opis|Wartość&nbsp;dla&nbsp;samouczka
   ------|---------|---
   Metryka podstawowa| Metryka oceny, według której będzie mierzony algorytm uczenia maszynowego.|AUC_weighted
   Kryteria wyjścia| Jeśli kryteria są spełnione, zadanie szkolenia zostanie zatrzymane. |Czas&nbsp;zadania&nbsp;szkoleniowego: 5 <br> <br> Maksymalna&nbsp;liczba#iteracji10&nbsp;&nbsp;&#58;
   Przetwarzania wstępnego| Umożliwia przetwarzanie wstępne wykonywane przez automatyczne Uczenie maszynowe. Obejmuje to automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych.| Włączenie
   Typ walidacji | Wybierz typ krzyżowej walidacji.|K — złożenie krzyżowego sprawdzania poprawności
   Liczba walidacji | Liczba testów. | 2 walidacje krzyżowe 
   Współbieżność| Maksymalna liczba współbieżnych iteracji.|5
   
1. Wybierz pozycję **Rozpocznij** , aby uruchomić eksperyment. Po rozpoczęciu przygotowania eksperymentu zostanie wyświetlony ekran z komunikatem o stanie.

>[!IMPORTANT]
> Przygotowanie eksperymentu trwa do **10-15 minut** . Po uruchomieniu usługi zajmiemy **2-3 minut więcej czasu dla każdej iteracji**.  
>
> W środowisku produkcyjnym najkorzystniej można wyszukać trochę. Jednak w tym samouczku zalecamy rozpoczęcie eksplorowania wyników iteracji po ich zakończeniu, gdy nadal działają pozostałe. 

##  <a name="explore-iteration-results"></a>Eksploruj wyniki iteracji

W miarę postępu eksperymentu ekran aktualizuje **Wykres iteracji** i **listę iteracji** przy użyciu różnych iteracji (modeli) utworzonych w miarę ich ukończenia i porządkuje je według wyniku pomiaru. Domyślnie model, który ocenia najwyższy poziom w oparciu o wybraną metrykę **AUC_weighted** , znajduje się w górnej części listy.

Podczas oczekiwania na zakończenie wszystkich iteracji eksperymentów wybierz **nazwę** ukończonej iteracji, aby poznać jej szczegóły wydajności. 
   
Poniżej przedstawiono wykresy i uruchomienia metryk generowanych dla każdej iteracji, takich jak krzywa odwołania z dokładnością, niepodzielna macierz, oceny ważonej dokładności itd. 

![Szczegóły przebiegu iteracji](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Automatyczne Uczenie maszynowe na stronie docelowej obszaru roboczego umożliwia wdrożenie najlepszego modelu jako usługi sieci Web w kilku krokach. Wdrożenie to integracja modelu, dzięki czemu można przewidzieć nowe dane i identyfikować potencjalne obszary szans sprzedaży. W przypadku tego eksperymentu wdrożenie do usługi sieci Web oznacza, że instytucja finansowa ma teraz iteracyjne i skalowalne rozwiązanie sieci Web służące do identyfikowania potencjalnych klientów z krótkoterminowymi wpłatami. 

Po zakończeniu przebiegu Wróć do strony szczegółów **wykresu iteracji** i **iteracji** . 

W tym kontekście eksperymentu **VotingEnsemble** jest uznawany za najlepszy model w oparciu o metrykę **AUC_weighted** .  Wdrażamy ten model, ale zaleca się wdrożenie trwa około 20 minut. Proces wdrażania obejmuje kilka czynności, takich jak rejestrowanie modelu, Generowanie zasobów i konfigurowanie ich dla usługi sieci Web.

1. Wybierz przycisk **Wdróż najlepszy model** w prawym górnym rogu.

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
