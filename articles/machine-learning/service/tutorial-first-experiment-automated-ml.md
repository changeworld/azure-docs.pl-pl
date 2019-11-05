---
title: Utwórz pierwszy zautomatyzowany eksperyment klasyfikacji ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nauczyć i wdrożyć model klasyfikacji przy użyciu automatycznego uczenia maszynowego w programie Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 98ebc8d9408d937730643056f65c3d8011c1fdc4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493470"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Samouczek: Tworzenie pierwszego modelu klasyfikacji przy użyciu automatycznej uczenia maszynowego
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

W tym samouczku dowiesz się, jak utworzyć pierwszy zautomatyzowany eksperyment uczenia maszynowego za pomocą programu Azure Machine Learning Studio bez konieczności pisania jednego wiersza kodu. Ten przykład tworzy model klasyfikacji, aby przewidzieć, czy klient zasubskrybuje stały termin wpłaty z instytucją finansową.

Dzięki zautomatyzowanej usłudze Machine Learning można zautomatyzować czasochłonne zadania. Automatyczne Uczenie maszynowe szybko iteruje wiele kombinacji algorytmów i parametrów, aby ułatwić znalezienie najlepszego modelu w oparciu o pomyślną metrykę wybrania.

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Utwórz obszar roboczy Azure Machine Learning.
> * Uruchom zautomatyzowany eksperyment uczenia maszynowego.
> * Wyświetl szczegóły eksperymentu.
> * Wdróż model.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Pobierz plik danych [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . Kolumna **y** wskazuje, czy klient subskrybuje stały termin wpłaty, który jest później zidentyfikowany jako kolumna docelowa dla prognoz w tym samouczku. 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze. 

Aby zarządzać zasobami platformy Azure, możesz utworzyć obszar roboczy za pośrednictwem Azure Machine Learning Studio.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 

## <a name="create-and-run-the-experiment"></a>Tworzenie i uruchamianie eksperymentu

Należy wykonać następujące czynności w celu skonfigurowania i uruchomienia kroków w programie Azure Machine Learning Studio — skonsolidowany interfejs, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych wszystkich poziomów umiejętności. Program Studio nie jest obsługiwany w przeglądarkach programu Internet Explorer.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz pozycję **Rozpocznij**.

1. W lewym okienku wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

   Ponieważ jest to pierwszy zautomatyzowany eksperyment z ML, zobaczysz pustą listę i linki do dokumentacji.

   ![Studio uczenia maszynowego Azure](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Wybierz kolejno pozycje **Nowy zautomatyzowany przebieg**. 

1. Utwórz nowy zestaw danych, wybierając pozycję **z plików lokalnych** z listy rozwijanej **+ Utwórz zestaw danych** . 

    1. Wybierz pozycję **Przeglądaj**.
    
    1. Wybierz plik **bankmarketing_train. csv** na komputerze lokalnym. Jest to plik pobrany jako [warunek wstępny](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Wybierz **Tabelaryczny** jako typ zestawu danych. 

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** w lewym dolnym rogu, aby przekazać ją do domyślnego kontenera, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego.  
    
       Po zakończeniu przekazywania ustawienia i formularz podglądu są wstępnie wypełniane na podstawie typu pliku. 
       
    1. Sprawdź, czy **Ustawienia i formularz podglądu** są wypełnione w następujący sposób, a następnie wybierz przycisk **dalej**.
        
        Pole|Opis| Wartość dla samouczka
        ---|---|---
        Format pliku|Definiuje układ i typ danych przechowywanych w pliku.| Lista
        Ogranicznik|Co najmniej jeden znak służący do określania granicy między&nbsp; oddzielnych, niezależnych regionów w postaci zwykłego tekstu lub innych strumieni danych. |Pliku
        Encoding|Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.| UTF-8
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.| Wszystkie pliki mają te same nagłówki
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.| Brak

    1. Formularz **schematu** umożliwia dalsze Konfigurowanie danych na potrzeby tego eksperymentu. Na potrzeby tego przykładu wybierz przełącznik przełącznika dla funkcji **day_of_week** , tak aby nie obejmował go dla tego eksperymentu. Wybierz opcję **Dalej**.

        ![Konfiguracja karty podglądu](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

    1. Na formularzu **Potwierdź szczegóły** Sprawdź, czy informacje są zgodne z informacjami o tym, co zostało wcześniej wypełnione w **podstawowych informacjach** i ustawieniach oraz w formularzach **wersji zapoznawczej** .
    1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.
    1. Wybierz zestaw danych, który zostanie wyświetlony na liście.
    1. Przejrzyj **Podgląd danych** , aby upewnić się, że nie doszło do **day_of_week** , a następnie wybierz **przycisk OK**.

    1. Wybierz pozycję **Dalej**.

1. Wypełnij formularz **konfigurowania przebiegu** w następujący sposób:
    1. Wprowadź tę nazwę eksperymentu: `my-1st-automl-experiment`

    1. Zaznacz opcję **y** jako kolumnę docelową, którą chcesz przewidzieć. Ta kolumna wskazuje, czy klient subskrybuje termin depozytowy.
    1. Wybierz pozycję **Utwórz nowe obliczenie** i skonfiguruj obiekt docelowy obliczeń. Obiekt docelowy obliczeń to lokalne lub oparte na chmurze środowisko zasobów używane do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. W przypadku tego eksperymentu używamy obliczeń opartych na chmurze. 

        Pole | Opis | Wartość dla samouczka
        ----|---|---
        Nazwa obliczeniowa |Unikatowa nazwa identyfikująca kontekst obliczeniowy.|automl — obliczenia
        Rozmiar&nbsp;maszyny wirtualnej&nbsp;| Wybierz rozmiar maszyny wirtualnej dla obliczenia.|Standard_DS12_V2
        Minimalna/Maksymalna liczba węzłów (w ustawieniach zaawansowanych)| Aby profilować dane, musisz określić co najmniej jeden węzeł.|Minimalna liczba węzłów: 1<br>Maksymalna liczba węzłów: 6
  
        1. Wybierz pozycję **Utwórz** , aby uzyskać obiekt docelowy obliczeń. 

            **Wykonanie tej czynności może zająć kilka minut.** 

        1. Po utworzeniu wybierz nowe miejsce docelowe obliczeń z listy rozwijanej.

    1. Wybierz opcję **Dalej**.

1. W formularzu **Typ zadania i ustawienia** wybierz pozycję **Klasyfikacja** jako typ zadania Uczenie maszynowe.

    1. Wybierz pozycję **Wyświetl dodatkowe ustawienia konfiguracji** i wypełnij pola w następujący sposób. Te ustawienia służą do lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych.

        >[!NOTE]
        > W tym samouczku nie ustawisz oceny metryki lub maksymalnej liczby rdzeni na wartość progową iteracji. Nie można natomiast blokować algorytmów.
   
        Dodatkowe konfiguracje&nbsp;|Opis|&nbsp;wartości dla&nbsp;— samouczek
        ------|---------|---
        Metryka podstawowa| Metryka oceny, według której będzie mierzony algorytm uczenia maszynowego.|AUC_weighted
        Automatyczne cechowania| Włącza przetwarzanie wstępne. Obejmuje to automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych.| Włączanie
        Zablokowane algorytmy | Algorytmy, które mają zostać wykluczone z zadania szkoleniowego| Brak
        Kryterium zakończenia| Jeśli kryteria są spełnione, zadanie szkolenia zostanie zatrzymane. |Czas&nbsp;zadania&nbsp;szkolenia (godziny): 1 <br> Próg&nbsp;&nbsp;metryki: brak
        Walidacja | Wybierz typ i liczbę testów.|Typ walidacji:<br>&nbsp;k — złóż&nbsp;a krzyżowego sprawdzania poprawności <br> <br> Liczba walidacji: 2
        Współbieżność| Maksymalna liczba wykonanych równoległych iteracji i użytych rdzeni na iterację| Maksymalnie&nbsp;współbieżnych&nbsp;iteracji: 5<br> Maksymalna liczba rdzeni&nbsp;&nbsp;na&nbsp;iteracja: brak
        
        Kliknij przycisk **OK**.

1. Wybierz pozycję **Utwórz** , aby uruchomić eksperyment. Zostanie otwarty ekran **szczegóły uruchamiania** ze **stanem Uruchom** jako przygotowanie eksperymentu.

>[!IMPORTANT]
> Przygotowanie eksperymentu trwa do **10-15 minut** .
> Po uruchomieniu usługi zajmiemy **2-3 minut więcej czasu dla każdej iteracji**.  
> Wybierz pozycję **Odśwież** okresowo, aby zobaczyć stan przebiegu w miarę postępu eksperymentu.
>
> W środowisku produkcyjnym najkorzystniej można wyszukać trochę. Jednak w tym samouczku zalecamy rozpoczęcie eksplorowania przetestowanych algorytmów na karcie modele po ich zakończeniu, gdy inne nadal działają. 

##  <a name="explore-models"></a>Eksploruj modele

Przejdź do karty **modele** , aby zobaczyć przetestowane algorytmy (modele). Domyślnie modele są uporządkowane według wyniku metryk po ich zakończeniu. Na potrzeby tego samouczka model, który ocenia najwyższy poziom w oparciu o wybraną metrykę **AUC_weighted** , znajduje się na szczycie listy.

Podczas oczekiwania na zakończenie wszystkich modeli eksperymentów wybierz **nazwę algorytmu** kompletnego modelu, aby poznać jego szczegóły wydajności. 

Poniżej przedstawiono **Informacje o modelu** i kartach **wizualizacje** , aby wyświetlić właściwości wybranego modelu, metryki i wykresy wydajności. 

![Szczegóły przebiegu iteracji](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Automatyczne Uczenie maszynowe w programie Azure Machine Learning Studio umożliwia wdrożenie najlepszego modelu jako usługi sieci Web w kilku krokach. Wdrożenie to integracja modelu, dzięki czemu można przewidzieć nowe dane i identyfikować potencjalne obszary szans sprzedaży. 

W przypadku tego eksperymentu wdrożenie do usługi sieci Web oznacza, że instytucja finansowa ma teraz iteracyjne i skalowalne rozwiązanie sieci Web służące do identyfikowania potencjalnych klientów z krótkoterminowymi wpłatami. 

Po zakończeniu przebiegu Wróć do strony **szczegóły uruchamiania** i wybierz kartę **modele** . Wybierz pozycję **Odśwież**. 

W tym kontekście eksperymentu **VotingEnsemble** jest uznawany za najlepszy model w oparciu o metrykę **AUC_weighted** .  Wdrażamy ten model, ale zaleca się wdrożenie trwa około 20 minut. Proces wdrażania obejmuje kilka czynności, takich jak rejestrowanie modelu, Generowanie zasobów i konfigurowanie ich dla usługi sieci Web.

1. Wybierz przycisk **Wdróż najlepszy model** w lewym dolnym rogu.

1. Wypełnij okienko **Wdróż najlepszy model** w następujący sposób:

    Pole| Wartość
    ----|----
    Nazwa wdrożenia| My-automl-Deploy
    Opis wdrożenia| Moje pierwsze zautomatyzowane wdrożenie eksperymentu w usłudze Machine Learning
    Skrypt oceniania| Automatyczne generowanie
    Skrypt środowiska| Automatyczne generowanie
    
1. Wybierz pozycję **Wdróż**.  

    W okienku **zalecany model** zostanie wyświetlony komunikat o powodzeniu wdrożenia w obszarze **Wdróż stan** po zakończeniu wdrażania. Należy okresowo wybierać pozycję **Odśwież** , aby sprawdzić stan wdrożenia.
    
Teraz masz działającą usługę sieci Web do generowania prognoz. 

Przejdź do [**następnych kroków**](#next-steps) , aby dowiedzieć się więcej na temat korzystania z nowej usługi sieci Web i testowania prognoz przy użyciu Power BI wbudowanej Azure Machine Learning obsługi.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pliki wdrożeń są większe niż pliki danych i eksperymenty, dzięki czemu są one droższe do przechowywania. Usuń tylko pliki wdrożenia, aby zminimalizować koszty dla konta, lub jeśli chcesz zachować obszar roboczy i pliki eksperymentów. W przeciwnym razie Usuń całą grupę zasobów, jeśli nie planujesz używać żadnego z tych plików.  

### <a name="delete-the-deployment-instance"></a>Usuwanie wystąpienia wdrożenia

Usuń tylko wystąpienie wdrożenia z programu Azure Machine Learning Studio, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do [Azure Machine Learning Studio](https://ml.azure.com/). Przejdź do obszaru roboczego i po lewej stronie okienka **elementy zawartości** wybierz pozycję **punkty końcowe**. 

1. Wybierz wdrożenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń**. 

1. Wybierz pozycję **Zastosuj**.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanym uczenia maszynowego użyto Azure Machine Learning Studio do utworzenia i wdrożenia modelu klasyfikacji. Zobacz następujące artykuły, aby uzyskać więcej informacji i następnych kroków:

> [!div class="nextstepaction"]
> [Korzystanie z usługi sieci Web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Dowiedz się więcej na temat [przetwarzania wstępnego](how-to-create-portal-experiments.md#preprocess).
+ Dowiedz się więcej na temat [profilowania danych](how-to-create-portal-experiments.md#profile).
+ Dowiedz się więcej o [automatycznym uczeniu maszynowym](concept-automated-ml.md).
+ Aby uzyskać więcej informacji na temat metryk i wykresów klasyfikacji, zobacz artykuł [Omówienie automatycznego uczenia maszynowego](how-to-understand-automated-ml.md#classification) .

>[!NOTE]
> Ten zestaw danych marketingu dla banku jest udostępniany w ramach [licencji Creative Commons Attribution (CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Wszystkie prawa do poszczególnych treści bazy danych są licencjonowane w ramach [licencji na zawartość bazy danych](https://creativecommons.org/publicdomain/zero/1.0/) i dostępne w witrynie [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ten zestaw danych był początkowo dostępny w [bazie danych Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez i P. Rita. Oparte na danych podejście do przewidywania sukcesu telemarketingu bankowego. Systemy pomocy technicznej, Elsevier, 62:22-31 czerwca 2014.
