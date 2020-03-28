---
title: Tworzenie zautomatyzowanych modeli klasyfikacji ml
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić & wdrażać modele klasyfikacji za pomocą zautomatyzowanego interfejsu uczenia maszynowego (automatycznego uczenia maszynowego) usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 03/04/2020
ms.openlocfilehash: b5a335a3f215ad5883b1b223245ca9d3f9967c3b
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366523"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Samouczek: Tworzenie modelu klasyfikacji z automatyczną maszyną ML w usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym samouczku dowiesz się, jak utworzyć podstawowy model klasyfikacji bez pisania pojedynczego wiersza kodu przy użyciu zautomatyzowanego interfejsu uczenia maszynowego usługi Azure Machine Learning. Ten model klasyfikacji przewiduje, czy klient będzie subskrybował depozyt na czas określony w instytucji finansowej.

Dzięki zautomatyzowanemu uczeniu maszynowemu można zautomatyzować zadania wymagające intensywnego czasu pracy. Automatyczne uczenie maszynowe szybko iteruje wiele kombinacji algorytmów i hiperparametrów, aby pomóc Ci znaleźć najlepszy model na podstawie wybranej metryki sukcesu.

W tym samouczku dowiesz się, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Machine Learning.
> * Uruchamianie eksperymentu zautomatyzowanego uczenia maszynowego.
> * Wyświetl szczegóły eksperymentu.
> * Wdrażanie modelu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Pobierz plik danych [**bankmarketing_train.csv.**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) Kolumna **y** wskazuje, czy klient subskrybował depozyt o stałym terminie, który jest później identyfikowany jako kolumna docelowa dla prognoz w tym samouczku. 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy usługi Azure Machine Learning to podstawowy zasób w chmurze używany do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego. Wiąże subskrypcję platformy Azure i grupę zasobów z łatwo zużywanym obiektem w usłudze. 

Tworzenie obszaru roboczego za pośrednictwem witryny Azure portal, konsoli opartej na sieci Web do zarządzania zasobami platformy Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Zanotuj **swój obszar roboczy** i **subskrypcję**. Będą one potrzebne, aby zapewnić utworzenie eksperymentu we właściwym miejscu. 

## <a name="create-and-run-the-experiment"></a>Tworzenie i uruchamianie eksperymentu

Należy wykonać następującą konfigurację eksperymentu i uruchomić https://ml.azure.comkroki za pośrednictwem usługi Azure Machine Learning w , skonsolidowanym interfejsie sieci web, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy nauki o danych dla praktyków nauki o danych na wszystkich poziomach umiejętności. Ten interfejs nie jest obsługiwany w przeglądarkach internet explorer.

1. Zaloguj się do [usługi Azure Machine Learning](https://ml.azure.com).

1. Wybierz subskrypcję i utworzony obszar roboczy.

1. Wybierz **pozycję Wprowadzenie**.

1. W lewym okienku wybierz **pozycję Zautomatyzowane ml** w sekcji **Autor.**

   Ponieważ jest to pierwszy zautomatyzowany eksperyment uczenia maszynowego, zobaczysz pustą listę i łącza do dokumentacji.

   ![Strona Wprowadzenie](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Wybierz **pozycję Nowy automatyczny przebieg ml**. 

1. Utwórz nowy zestaw danych, wybierając **opcję Z plików lokalnych** z listy rozwijanej **+Utwórz zestaw danych.** 

    1. W formularzu **Informacje podstawowe** nadaj zestawowi danych nazwę i podaj opcjonalny opis. Zautomatyzowany interfejs ml obsługuje obecnie tylko zestawy kartuek, więc typ zestawu danych powinien być domyślnie *tabelaryczny*.

    1. Wybierz **pozycję Dalej** w lewym dolnym rogu

    1. W **formularzu Datastore i file selection** wybierz domyślny magazyn danych, który został automatycznie skonfigurowany podczas tworzenia obszaru **roboczego, workspaceblobstore (Azure Blob Storage)**. W tym miejscu przekażesz plik danych, aby udostępnić go w obszarze roboczym.

    1. Wybierz pozycję **Przeglądaj**.
    
    1. Wybierz plik **bankmarketing_train.csv** na komputerze lokalnym. Jest to plik pobrany jako [warunek wstępny](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz **przycisk Dalej** w lewym dolnym rogu, aby przekazać go do domyślnego kontenera, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego.  
    
       Po zakończeniu przekazywania formularz Ustawienia i wersja zapoznawcza są wstępnie wypełniane na podstawie typu pliku. 
       
    1. Sprawdź, czy **formularz Ustawienia i podgląd** jest wypełniony w następujący sposób i wybierz pozycję **Dalej**.
        
        Pole|Opis| Wartość samouczka
        ---|---|---
        Format pliku|Definiuje układ i typ danych przechowywanych w pliku.| Rozdzielany
        Ogranicznik|Jeden lub więcej znaków do określania granicy między&nbsp; oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innych strumieni danych. |Przecinek
        Kodowanie|Określa, jaki bit do tabeli schematu znaków do użycia do odczytu zestawu danych.| UTF-8
        Nagłówki kolumn| Wskazuje, jak nagłówki zestawu danych, jeśli istnieją, będą traktowane.| Wszystkie pliki mają te same nagłówki
        Pomiń wiersze | Wskazuje, ile wierszy jest pomijanych w zestawie danych.| Brak

    1. Formularz **Schemat** umożliwia dalszą konfigurację danych dla tego eksperymentu. W tym przykładzie wybierz przełącznik dla funkcji **day_of_week,** aby nie uwzględniać go w tym eksperymencie. Wybierz **pozycję Dalej**.

        ![Konfiguracja karty Podgląd](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. W formularzu **Potwierdź szczegóły** sprawdź, czy informacje są zgodne z wcześniej wypełnionymi **w formularzach Podstawowe informacje** i Ustawienia oraz w **formularzach podglądu.**
    1. Wybierz **pozycję Utwórz,** aby zakończyć tworzenie zestawu danych.
    1. Wybierz zestaw danych, gdy pojawi się na liście.
    1. Przejrzyj **podgląd danych,** aby upewnić się, że nie uwzględniłeś **day_of_week** następnie wybierz przycisk **OK**.

    1. Wybierz **pozycję Dalej**.

1. Wypełnij formularz **Konfiguruj przebieg w** następujący sposób:
    1. Wprowadź tę nazwę eksperymentu:`my-1st-automl-experiment`

    1. Wybierz **y** jako kolumnę docelową, co chcesz przewidzieć. Ta kolumna wskazuje, czy klient subskrybował depozyt terminowy, czy nie.
    1. Wybierz **pozycję Utwórz nowe obliczenia** i skonfiguruj cel obliczeniowy. Obiekt docelowy obliczeń to lokalne lub oparte na chmurze środowisko zasobów używane do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. W tym eksperymencie używamy obliczeń opartych na chmurze. 

        Pole | Opis | Wartość samouczka
        ----|---|---
        Nazwa obliczeń |Unikatowa nazwa identyfikująca kontekst obliczeniowy.|automl-compute
        Rozmiar&nbsp;&nbsp;maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczeń.|Standard_DS12_V2
        Min / Max węzłów (w ustawieniach zaawansowanych)| Aby dane profilu, należy określić 1 lub więcej węzłów.|Min. węzły: 1<br>Maksymalna liczba węzłów: 6
  
        1. Wybierz **pozycję Utwórz,** aby uzyskać cel obliczeniowy. 

            **Trwa to kilka minut.** 

        1. Po utworzeniu wybierz nowy cel obliczeniowy z listy rozwijanej.

    1. Wybierz **pozycję Dalej**.

1. W formularzu **Typ zadania i ustawienia** wybierz pozycję **Klasyfikacja** jako typ zadania uczenia maszynowego.

    1. Wybierz **pozycję Wyświetl dodatkowe ustawienia konfiguracji** i wypełnij pola w następujący sposób. Te ustawienia mają na celu lepszą kontrolę zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych.

        >[!NOTE]
        > W tym samouczku nie ustawisz wyniku metryki ani maksymalnego progu rdzeni na próg iteracji. Nie zablokujesz również testowania algorytmów.
   
        Dodatkowe&nbsp;konfiguracje|Opis|Wartość&nbsp;&nbsp;samouczka
        ------|---------|---
        Metryka podstawowa| Metryka oceny, za pomocą których będzie mierzony algorytm uczenia maszynowego.|AUC_weighted
        Automatyczne featurization| Umożliwia wstępne przetwarzanie. Obejmuje to automatyczne oczyszczanie danych, przygotowywanie i przekształcanie w celu generowania funkcji syntetycznych.| Włączanie
        Zablokowane algorytmy | Algorytmy, które chcesz wykluczyć z zadania szkoleniowego| Brak
        Kryterium wyjścia| Jeśli spełnione jest kryterium, zadanie szkoleniowe zostanie zatrzymane. |Czas&nbsp;&nbsp;pracy szkoleniowej (w godzinach): 1 <br> Próg&nbsp;wyniku&nbsp;metryki: Brak
        Sprawdzanie poprawności | Wybierz typ krzyżowej weryfikacji i liczbę testów.|Typ sprawdzania poprawności:<br>&nbsp;k-fold&nbsp;krzyżowa walidacja <br> <br> Liczba walidacji: 2
        Współbieżność| Maksymalna liczba iteracji równoległych wykonywanych na iterację| Maksymalna&nbsp;liczba&nbsp;równoczesnych iteracji: 5
        
        Wybierz **pozycję Zapisz**.

1. Wybierz **zakończ,** aby uruchomić eksperyment. Ekran **Uruchom szczegóły** zostanie otwarty ze **stanem Uruchom** u góry po rozpoczęciu przygotowania do eksperymentu.

>[!IMPORTANT]
> Przygotowanie trwa **10-15 minut,** aby przygotować przebieg eksperymentu.
> Po uruchomieniu, trwa **2-3 minut więcej dla każdej iteracji**.  
> Wybierz **odświeżanie** okresowo, aby zobaczyć stan uruchomienia w miarę postępu eksperymentu.
>
> W produkcji, to prawdopodobnie odejść na chwilę. Ale w tym samouczku zalecamy rozpoczęcie eksplorowania przetestowanych algorytmów na karcie **Modele,** gdy są one kompletne, podczas gdy inne są nadal uruchomione. 

##  <a name="explore-models"></a>Poznaj modele

Przejdź do **modelki,** aby wyświetlić przetestowane algorytmy (modele). Domyślnie modele są uporządkowane według wyniku metryki po ich zakończeniu. W tym samouczku model, który osiąga najwyższą ocenę na podstawie wybranej metryki **AUC_weighted,** znajduje się na początku listy.

Podczas oczekiwania na zakończenie wszystkich modeli eksperymentu wybierz **nazwę algorytm ukończonego** modelu, aby zapoznać się z szczegółami wydajności. 

Poniżej przedstawiono **szczegóły modelu** i karty **Wizualizacje,** aby wyświetlić właściwości wybranego modelu, metryki i wykresy wydajności. 

![Uruchamianie szczegółów iteracji](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Wdrażanie najlepszego modelu

Zautomatyzowany interfejs uczenia maszynowego umożliwia wdrożenie najlepszego modelu jako usługi sieci web w kilku krokach. Wdrożenie jest integracją modelu, dzięki czemu można przewidzieć na nowe dane i zidentyfikować potencjalne obszary możliwości. 

W tym eksperymencie wdrożenie do usługi sieci web oznacza, że instytucja finansowa ma teraz iteracyjne i skalowalne rozwiązanie sieci web do identyfikowania potencjalnych klientów depozytów terminowych. 

Po zakończeniu uruchamiania przejdź z powrotem do strony **Uruchom szczegóły** i wybierz kartę **Modele.**

W tym kontekście **eksperymentu VotingEnsemble** jest uważany za najlepszy model, na podstawie **metryki AUC_weighted.**  Wdrożymy ten model, ale należy pamiętać, wdrożenie trwa około 20 minut, aby zakończyć. Proces wdrażania wymaga kilku kroków, w tym rejestrowania modelu, generowania zasobów i konfigurowania ich dla usługi sieci web.

1. Wybierz przycisk **Wdrażanie najlepszego modelu** w lewym dolnym rogu.

1. Wypełnij **okienko Wdrażanie modelu w** następujący sposób:

    Pole| Wartość
    ----|----
    Nazwa wdrożenia| my-automl-deploy
    Opis wdrożenia| Moje pierwsze zautomatyzowane wdrożenie eksperymentu uczenia maszynowego
    Typ obliczeń | Wybierz wystąpienie obliczeniowe platformy Azure (ACI)
    Włącz uwierzytelnianie| Wyłącz. 
    Korzystanie z wdrożeń niestandardowych| Wyłącz. Umożliwia automatyczne wygenerowanie domyślnego pliku sterownika (skrypt oceniania) i pliku środowiska. 
    
    W tym przykładzie używamy ustawień domyślnych podanych w menu *Zaawansowane.* 

1. Wybierz pozycję **Wdróż**.  

    W górnej części ekranu **Uruchom** pojawi się zielony komunikat o sukcesie, a w okienku **Zalecany model** w obszarze **Stan wdrażania**pojawi się komunikat o stanie . Wybierz **opcję Odśwież** okresowo, aby sprawdzić stan wdrożenia.
    
Teraz masz operacyjną usługę sieci web do generowania prognoz. 

Przejdź do [**następnych kroków,**](#next-steps) aby dowiedzieć się więcej o korzystaniu z nowej usługi sieci web i przetestować prognozy przy użyciu wbudowanej w usługę Azure Machine Learning pomocy technicznej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pliki wdrażania są większe niż pliki danych i eksperymentów, więc kosztują więcej. Usuń tylko pliki wdrożeniowe, aby zminimalizować koszty na koncie lub jeśli chcesz zachować pliki obszaru roboczego i eksperymentu. W przeciwnym razie usuń całą grupę zasobów, jeśli nie zamierzasz używać żadnych plików.  

### <a name="delete-the-deployment-instance"></a>Usuwanie wystąpienia wdrożenia

Usuń tylko wystąpienie wdrożenia z usługi\/Azure Machine Learning pod adresem https: /ml.azure.com/, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do [usługi Azure Machine Learning](https://ml.azure.com/). Przejdź do obszaru roboczego i po lewej stronie w okienku **Zasoby** wybierz pozycję **Punkty końcowe**. 

1. Wybierz wdrożenie, które chcesz usunąć, i wybierz pozycję **Usuń**. 

1. Wybierz **pozycję Kontynuuj**.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku automatycznego uczenia maszynowego użyto zautomatyzowanego interfejsu uczenia maszynowego usługi Azure Machine Learning do utworzenia i wdrożenia modelu klasyfikacji. Zobacz następujące artykuły, aby uzyskać więcej informacji i następne kroki:

> [!div class="nextstepaction"]
> [Używanie usługi internetowej](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Dowiedz się więcej o [zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md).
+ Aby uzyskać więcej informacji na temat metryk klasyfikacji i wykresów, zobacz artykuł [Opis wyników automatycznego uczenia maszynowego.+](how-to-understand-automated-ml.md#classification) Dowiedz się więcej o [featurization](how-to-use-automated-ml-for-ml-models.md#featurization).
+ Dowiedz się więcej o [profilowaniu danych](how-to-use-automated-ml-for-ml-models.md#profile).


>[!NOTE]
> Ten zestaw danych Marketing bankowy jest udostępniany na licencji [Creative Commons (CCO: Public Domain).](https://creativecommons.org/publicdomain/zero/1.0/) Wszelkie prawa do indywidualnej zawartości bazy danych są licencjonowane na podstawie [licencji zawartości bazy danych](https://creativecommons.org/publicdomain/zero/1.0/) i dostępne na [kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ten zestaw danych był pierwotnie dostępny w [bazie danych UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing)Database .<br><br>
> [Moro i wsp., 2014] S. Moro, P. Cortez i P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing (Podejście oparte na danych do prognozowania powodzenia telemarketingu bankowego). Decision Support Systems, Elsevier, 62:22-31, czerwiec 2014.
