---
title: Prognozowanie zapotrzebowania na udostępnianie rowerów za pomocą zautomatyzowanego eksperymentu ml
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić i wdrażać model prognozowania popytu za pomocą zautomatyzowanego uczenia maszynowego w studio usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088242"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Samouczek: Prognozowanie zapotrzebowania na udostępnianie rowerów dzięki zautomatyzowanemu uczeniu maszynowemu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym samouczku używasz automatycznego uczenia maszynowego lub zautomatyzowanego uczenia maszynowego w studio usługi Azure Machine Learning, aby utworzyć model prognozowania szeregów czasowych w celu przewidywania zapotrzebowania na wynajem usługi udostępniania rowerów.

W tym samouczku dowiesz się, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Tworzenie i ładowanie zestawu danych.
> * Skonfiguruj i uruchom zautomatyzowany eksperyment uczenia maszynowego.
> * Zapoznaj się z wynikami eksperymentu.
> * Wdrażanie najlepszego modelu.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning w wersji korporacyjnej. Jeśli nie masz obszaru roboczego, [utwórz obszar roboczy wersji Enterprise](how-to-manage-workspace.md). 
    * Automatyczne uczenie maszynowe w studio usługi Azure Machine Learning jest dostępne tylko dla obszarów roboczych w wersji Enterprise. 
* Pobierz plik danych [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Wprowadzenie do studia usługi Azure Machine Learning

W tym samouczku utworzysz zautomatyzowany eksperyment uczenia maszynowego w usłudze Azure Machine Learning studio, skonsolidowany interfejs, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy do nauki o danych dla praktyków nauki o danych na wszystkich poziomach umiejętności. Studio nie jest obsługiwane w przeglądarkach Internet Explorer.

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com).

1. Wybierz subskrypcję i utworzony obszar roboczy.

1. Wybierz **pozycję Wprowadzenie**.

1. W lewym okienku wybierz **pozycję Zautomatyzowane ml** w sekcji **Autor.**

1. Wybierz **+Nowy automatyczny przebieg ML**. 

## <a name="create-and-load-dataset"></a>Tworzenie i ładowanie zestawu danych

Przed skonfigurowaniem eksperymentu należy przekazać plik danych do obszaru roboczego w formie zestawu danych usługi Azure Machine Learning. Dzięki temu można upewnić się, że dane są odpowiednio sformatowane dla eksperymentu.

1. W formularzu **Wybierz zestaw danych** wybierz z **listy** rozwijanej **+Utwórz zestaw danych.** 

    1. W formularzu **Informacje podstawowe** nadaj zestawowi danych nazwę i podaj opcjonalny opis. Typ zestawu danych powinien być domyślnie **tabelaryczny,** ponieważ zautomatyzowane ml w usłudze Azure Machine Learning studio obecnie obsługuje tylko tabelaryczne zestawy danych.
    
    1. Wybierz **pozycję Dalej** w lewym dolnym rogu

    1. W **formularzu Datastore i file selection** wybierz domyślny magazyn danych, który został automatycznie skonfigurowany podczas tworzenia obszaru **roboczego, workspaceblobstore (Azure Blob Storage)**. Jest to miejsce przechowywania, w którym przekażesz plik danych. 

    1. Wybierz pozycję **Przeglądaj**. 
    
    1. Wybierz plik **bike-no.csv** na komputerze lokalnym. Jest to plik pobrany jako [warunek wstępny](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Wybierz pozycję **Dalej**

       Po zakończeniu przekazywania formularz Ustawienia i wersja zapoznawcza są wstępnie wypełniane na podstawie typu pliku. 
       
    1. Sprawdź, czy **formularz Ustawienia i podgląd** jest wypełniony w następujący sposób i wybierz pozycję **Dalej**.
        
        Pole|Opis| Wartość samouczka
        ---|---|---
        Format pliku|Definiuje układ i typ danych przechowywanych w pliku.| Rozdzielany
        Ogranicznik|Jeden lub więcej znaków do określania granicy między&nbsp; oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innych strumieni danych. |Przecinek
        Kodowanie|Określa, jaki bit do tabeli schematu znaków do użycia do odczytu zestawu danych.| UTF-8
        Nagłówki kolumn| Wskazuje, jak nagłówki zestawu danych, jeśli istnieją, będą traktowane.| Używanie nagłówków z pierwszego pliku
        Pomiń wiersze | Wskazuje, ile wierszy jest pomijanych w zestawie danych.| Brak

    1. Formularz **Schemat** umożliwia dalszą konfigurację danych dla tego eksperymentu. 
    
        1. W tym przykładzie należy zignorować **kolumny dorywcze** i **zarejestrowane.** Te kolumny są podziałem kolumny **cnt,** więc nie dołączamy ich.

        1. Również w tym przykładzie pozostaw wartości domyślne dla **właściwości** i **typu**. 
        
        1. Wybierz **pozycję Dalej**.

    1. W formularzu **Potwierdź szczegóły** sprawdź, czy informacje są zgodne z wcześniej wypełnionymi **w formularzach Podstawowe informacje** i Ustawienia oraz w **formularzach podglądu.**

    1. Wybierz **pozycję Utwórz,** aby zakończyć tworzenie zestawu danych.

    1. Wybierz zestaw danych, gdy pojawi się na liście.

    1. Wybierz **pozycję Dalej**.

## <a name="configure-experiment-run"></a>Konfigurowanie przebiegu eksperymentu

Po załadowaniu i skonfigurowaniu danych skonfiguruj zdalny cel obliczeniowy i wybierz kolumnę w danych, którą chcesz przewidzieć.

1. Wypełnij formularz **Konfigurowanie przebiegu w** następujący sposób:
    1. Wprowadź nazwę eksperymentu:`automl-bikeshare`

    1. Wybierz **cnt** jako kolumnę docelową, co chcesz przewidzieć. Ta kolumna wskazuje liczbę wszystkich wypożyczeń udziałów rowerów.

    1. Wybierz **pozycję Utwórz nowe obliczenia** i skonfiguruj cel obliczeniowy. Automatyczna liczba wirtualna obsługuje tylko obliczenia usługi Azure Machine Learning. 

        Pole | Opis | Wartość samouczka
        ----|---|---
        Nazwa obliczeń |Unikatowa nazwa identyfikująca kontekst obliczeniowy.|bike-compute
        Rozmiar&nbsp;&nbsp;maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczeń.|Standard_DS12_V2
        Min / Max węzłów (w ustawieniach zaawansowanych)| Aby dane profilu, należy określić 1 lub więcej węzłów.|Min. węzły: 1<br>Maksymalna liczba węzłów: 6
  
        1. Wybierz **pozycję Utwórz,** aby uzyskać cel obliczeniowy. 

            **Trwa to kilka minut.** 

        1. Po utworzeniu wybierz nowy cel obliczeniowy z listy rozwijanej.

    1. Wybierz **pozycję Dalej**.

## <a name="select-task-type-and-settings"></a>Wybieranie typu i ustawień zadania

Zakończ konfigurację eksperymentu automatycznego uczenia maszynowego, określając typ zadania uczenia maszynowego i ustawienia konfiguracji.

1. W formularzu **Typ zadania i ustawienia** wybierz opcję **Prognozowanie szeregów czasowych** jako typ zadania uczenia maszynowego.

1. Wybierz **datę** jako **kolumnę Czas** i pozostaw pole wyboru Grupa według **kolumn.** 

    1. Wybierz **pozycję Wyświetl dodatkowe ustawienia konfiguracji** i wypełnij pola w następujący sposób. Te ustawienia mają na celu lepszą kontrolę zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych.

  
        Dodatkowe&nbsp;konfiguracje|Opis|Wartość&nbsp;&nbsp;samouczka
        ------|---------|---
        Metryka podstawowa| Metryka oceny, za pomocą których będzie mierzony algorytm uczenia maszynowego.|Znormalizowany błąd kwadratu głównego
        Automatyczne featurization| Umożliwia wstępne przetwarzanie. Obejmuje to automatyczne oczyszczanie danych, przygotowywanie i przekształcanie w celu generowania funkcji syntetycznych.| Włączanie
        Wyjaśnij najlepszy model (wersja zapoznawcza)| Automatycznie pokazuje wytłumaczenia na najlepszy model utworzony przez zautomatyzowane ML.| Włączanie
        Zablokowane algorytmy | Algorytmy, które chcesz wykluczyć z zadania szkoleniowego| Ekstremalne losowe drzewa
        Dodatkowe ustawienia prognozowania| Te ustawienia pomagają poprawić dokładność modelu <br><br> _**Horyzont prognozy:**_ czas w przyszłość, którą chcesz przewidzieć <br> _**Prognoza docelowa opóźnia się:**_ jak daleko wstecz chcesz skonstruować opóźnienia zmiennej docelowej <br> _**Okno docelowe :**_ określa rozmiar okna rolowania, nad którym zostaną wygenerowane operacje, takie jak *max, min* i *suma.* |Horyzont prognozy: 14 <br> Opóźnienie&nbsp;&nbsp;docelowe prognozy: Brak <br> &nbsp;Docelowy&nbsp;&nbsp;rozmiar okna tocznego: Brak
        Kryterium wyjścia| Jeśli spełnione jest kryterium, zadanie szkoleniowe zostanie zatrzymane. |Czas&nbsp;&nbsp;pracy szkoleniowej (w godzinach): 3 <br> Próg&nbsp;wyniku&nbsp;metryki: Brak
        Sprawdzanie poprawności | Wybierz typ krzyżowej weryfikacji i liczbę testów.|Typ sprawdzania poprawności:<br>&nbsp;k-fold&nbsp;krzyżowa walidacja <br> <br> Liczba walidacji: 5
        Współbieżność| Maksymalna liczba iteracji równoległych wykonywanych na iterację| Maksymalna&nbsp;liczba&nbsp;równoczesnych iteracji: 6
        
        Wybierz **pozycję Zapisz**.

## <a name="run-experiment"></a>Uruchom eksperyment

Aby uruchomić eksperyment, wybierz pozycję **Zakończ**. Zostanie otwarty ekran **Uruchom szczegóły** ze **stanem Uruchom** u góry obok numeru uruchomienia. Ten stan jest aktualizowany w miarę postępu eksperymentu.

>[!IMPORTANT]
> Przygotowanie trwa **10-15 minut,** aby przygotować przebieg eksperymentu.
> Po uruchomieniu, trwa **2-3 minut więcej dla każdej iteracji**.  <br> <br>
> W produkcji, można by prawdopodobnie odejść na chwilę, jak ten proces wymaga czasu. Podczas oczekiwania, sugerujemy rozpocząć eksplorowanie przetestowanych algorytmów na **modelu** karty po ich zakończeniu. 

##  <a name="explore-models"></a>Poznaj modele

Przejdź do **modelki,** aby wyświetlić przetestowane algorytmy (modele). Domyślnie modele są uporządkowane według wyniku metryki po ich zakończeniu. W tym samouczku model, który osiąga najwyższą ocenę na podstawie wybranej **znormalizowanej metryki błędu znormalizowanego katalogu głównego,** znajduje się na początku listy.

Podczas oczekiwania na zakończenie wszystkich modeli eksperymentu wybierz **nazwę algorytm ukończonego** modelu, aby zapoznać się z szczegółami wydajności. 

Poniższy przykład nawiguje po **szczegóły modelu** i **wizualizacje** kart, aby wyświetlić właściwości wybranego modelu, metryki i wykresy wydajności. 

![Uruchom szczegóły](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Zautomatyzowane uczenie maszynowe w usłudze Azure Machine Learning studio umożliwia wdrożenie najlepszego modelu jako usługi sieci web w kilku krokach. Wdrożenie jest integracją modelu, dzięki czemu można przewidzieć na nowe dane i zidentyfikować potencjalne obszary możliwości. 

W tym eksperymencie wdrożenie do usługi sieci web oznacza, że firma współdzielenia rowerów ma teraz iteracyjne i skalowalne rozwiązanie sieci web do prognozowania popytu na wynajem udziałów w rowerach. 

Po zakończeniu uruchamiania przejdź z powrotem do strony **Uruchom szczegóły** i wybierz kartę **Modele.**

W tym kontekście **eksperymentu StackEnsemble** jest uważany za najlepszy model, na podstawie **znormalizowanej metryki błędu głównego średniej kwadratu.**  Wdrożymy ten model, ale należy pamiętać, wdrożenie trwa około 20 minut, aby zakończyć. Proces wdrażania wymaga kilku kroków, w tym rejestrowania modelu, generowania zasobów i konfigurowania ich dla usługi sieci web.

1. Wybierz przycisk **Wdrażanie najlepszego modelu** w lewym dolnym rogu.

1. Wypełnij **okienko Wdrażanie modelu w** następujący sposób:

    Pole| Wartość
    ----|----
    Nazwa wdrożenia| bikeshare-deploy
    Opis wdrożenia| wdrożenie zapotrzebowania na udział w rowerze
    Typ obliczeń | Wybierz wystąpienie obliczeniowe platformy Azure (ACI)
    Włącz uwierzytelnianie| Wyłącz. 
    Korzystanie z niestandardowych zasobów wdrażania| Wyłącz. Wyłączenie umożliwia automatyczne wygenerowanie domyślnego pliku sterownika (skrypt oceniania) i pliku środowiska. 
    
    W tym przykładzie używamy ustawień domyślnych podanych w menu *Zaawansowane.* 

1. Wybierz pozycję **Wdróż**.  

    Zielona wiadomość o sukcesie pojawia się w górnej części ekranu **Uruchom** stwierdził, że wdrożenie zostało pomyślnie rozpoczęte. Postęp wdrożenia można znaleźć  
    w okienku **Zalecany model** w obszarze **Stan wdrażania**.
    
Po pomyślnym wdrożeniu masz operacyjną usługę sieci web do generowania prognoz. 

Przejdź do [**następnych kroków,**](#next-steps) aby dowiedzieć się więcej o korzystaniu z nowej usługi sieci web i przetestować prognozy przy użyciu wbudowanej w usługę Azure Machine Learning pomocy technicznej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pliki wdrażania są większe niż pliki danych i eksperymentów, więc kosztują więcej. Usuń tylko pliki wdrożeniowe, aby zminimalizować koszty na koncie lub jeśli chcesz zachować pliki obszaru roboczego i eksperymentu. W przeciwnym razie usuń całą grupę zasobów, jeśli nie zamierzasz używać żadnych plików.  

### <a name="delete-the-deployment-instance"></a>Usuwanie wystąpienia wdrożenia

Usuń tylko wystąpienie wdrożenia ze studia usługi Azure Machine Learning, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do [studia usługi Azure Machine Learning](https://ml.azure.com/). Przejdź do obszaru roboczego i po lewej stronie w okienku **Zasoby** wybierz pozycję **Punkty końcowe**. 

1. Wybierz wdrożenie, które chcesz usunąć, i wybierz pozycję **Usuń**. 

1. Wybierz **pozycję Kontynuuj**.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zautomatyzowanego uczenia maszynowego w studio usługi Azure Machine Learning do utworzenia i wdrożenia modelu prognozowania szeregów czasowych, który przewiduje zapotrzebowanie na wypożyczanie udziałów w rowerze. 

W tym artykule opisano kroki dotyczące tworzenia schematu obsługiwanego przez usługę Power BI w celu ułatwienia użycia nowo wdrożonej usługi sieci web:

> [!div class="nextstepaction"]
> [Używanie usługi internetowej](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Ten zestaw danych udziału roweru został zmodyfikowany dla tego samouczka. Ten zestaw danych został udostępniony w ramach [konkursu Kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) i był pierwotnie dostępny za pośrednictwem [Capital Bikeshare.](https://www.capitalbikeshare.com/system-data) Można go również znaleźć w [bazie danych UCI Machine Learning](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Źródło: Fanaee-T, Hadi i Gama, Joao, Event etykietowania łączący detektory zespołu i wiedzy tła, Postęp w sztucznej inteligencji (2013): s. 1-15, Springer Berlin Heidelberg.
