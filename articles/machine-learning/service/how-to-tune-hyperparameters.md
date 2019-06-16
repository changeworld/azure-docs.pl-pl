---
title: Dostosowywanie hiperparametrów dla modelu
titleSuffix: Azure Machine Learning service
description: Efektywne dostrojenie hiperparametrów dla modelu uczenie głębokie uczenie / machine przy użyciu usługi Azure Machine Learning. Dowiesz się jak zdefiniować parametr przestrzeni wyszukiwania, określić podstawową metrykę do optymalizacji i wczesne zakończyć niskiej wydajności działa.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cf9ac0271e140d719da9a72424e1c01021fdf6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957423"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning-service"></a>Dostosowywanie hiperparametrów dla modelu za pomocą usługi Azure Machine Learning

Efektywne dostrojenie hiperparametrów dla modelu przy użyciu usługi Azure Machine Learning.  Do strojenia Hiperparametrycznego obejmuje następujące kroki:

* Zdefiniuj parametr przestrzeni wyszukiwania
* Określ podstawowe metryki do optymalizacji  
* Określ kryteria wcześniejsze zakończenie niskiej wydajności przebiegów
* Przydzielanie zasobów do strojenia hiperparametrycznego
* Uruchamianie eksperymentu przy użyciu powyższej konfiguracji
* Wizualizuj przebiegów szkoleniowych
* Wybierz najlepiej konfiguracji dla modelu

## <a name="what-are-hyperparameters"></a>Co to są hiperparametrów?

Hiperparametrów są zmieniane parametry wybranych do nauczenia modelu sam proces szkolenia. Na przykład do szkolenia sieci neuronowej, możesz zdecydować, Liczba ukrytych warstw w sieci i liczby węzłów w każdej warstwie przed uczenia modelu. Te wartości zwykle pozostają stałe w procesie szkolenia.

W scenariuszach uczenie głębokie uczenie / machine modelu wydajność zależy od intensywnie wybrano wartości hiperparametrycznego. Celem eksploracji hiperparametrycznego jest wyszukiwanie w różnych konfiguracjach hiperparametrycznego można odnaleźć konfiguracji, które powoduje najlepszą wydajność. Zazwyczaj procesu eksploracji hiperparametrycznego jest żmudne ręczne, biorąc pod uwagę, że miejsce wyszukiwania jest ogromna i oceny każda konfiguracja może być kosztowne.

Usługa Azure Machine Learning pozwala na zautomatyzowanie eksploracji hiperparametrycznego w sposób efektywny, zaoszczędzić znaczną ilość czasu i zasobów. Określ zakres wartości hiperparametrycznego i maksymalna liczba szkolenia przebiegów. System następnie uruchamia wielu równoczesnych uruchomień przy użyciu różnych parametrów konfiguracji i automatycznie wyszukuje konfigurację, która powoduje najlepszą wydajność, mierzone metryki, które wybierzesz. Nieprawidłowo działających przebiegów szkoleniowych są automatycznie wczesne zakończone, zmniejszając nadmierne użycie nadmierny zasobów obliczeniowych. Te zasoby zamiast tego są używane do eksplorowania inne konfiguracje hiperparametrycznego.


## <a name="define-search-space"></a>Definiowanie przestrzeni wyszukiwania

Automatyczne dostosowywanie hiperparametrów, eksplorując zakres wartości zdefiniowane dla każdego hiperparametrycznego.

### <a name="types-of-hyperparameters"></a>Typy hiperparametrów

Każdy hiperparametrycznego może być discrete lub ciągłe.

#### <a name="discrete-hyperparameters"></a>Dyskretne hiperparametrów 

Dyskretne hiperparametrów są określane jako `choice` wśród wartości dyskretnych. `choice` może być:

* jeden lub więcej wartości rozdzielanych przecinkami
* Element `range` obiektu
* wszelkie dowolnego `list` obiektu


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

W tym przypadku `batch_size` przyjmuje jedną z wartości [16, 32, 64, 128] i `number_of_hidden_layers` przyjmuje jedną z wartości [1, 2, 3, 4].

Zaawansowane hiperparametrów dyskretnych można również określić, korzystania z punktów dystrybucji. Obsługiwane są poniższe dystrybucje:

* `quniform(low, high, q)` — Zwraca wartość, takie jak działanie (uniform (niska, wysoka) / q) * pytania i odpowiedzi
* `qloguniform(low, high, q)` — Zwraca wartość, takie jak działanie (exp (uniform (niska, wysoka)) / q) * pytania i odpowiedzi
* `qnormal(mu, sigma, q)` — Zwraca wartość, takie jak działanie (normalny (jednostkę obsługi komunikatów, sigma) / q) * pytania i odpowiedzi
* `qlognormal(mu, sigma, q)` — Zwraca wartość, takie jak działanie (exp (normalny (jednostkę obsługi komunikatów, sigma)) / q) * pytania i odpowiedzi

#### <a name="continuous-hyperparameters"></a>Ciągłe hiperparametrów 

Ciągłe hiperparametrów są określane jako dystrybucji w ciągłym zakresie wartości. Obsługiwane dystrybucje obejmują:

* `uniform(low, high)` — Zwraca wartości równomiernie rozłożone między niski i wysoki
* `loguniform(low, high)` — Zwraca wartość rysowane zgodnie z exp (uniform (niska, wysoka)), aby równomiernie logarytm liczby wartość zwracana
* `normal(mu, sigma)` -Zwraca rzeczywistą wartość, która zwykle jest dystrybuowane z średnią mu i odchylenie standardowe
* `lognormal(mu, sigma)` — Zwraca wartość rysowane zgodnie z exp (normalny (jednostkę obsługi komunikatów, sigma)), aby logarytm liczby wartość zwracana jest zazwyczaj dystrybuowane

Przykład definicji parametru miejsca:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ten kod definiuje miejsce wyszukiwania o dwa parametry — `learning_rate` i `keep_probability`. `learning_rate` ma do rozkładu normalnego z średniej wartości 10 i odchylenie standardowe 3. `keep_probability` ma jednolity rozkład o określonej wartości minimalnej 0,05 i maksymalna wartość 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Próbkowanie miejsca hiperparametrycznego

Można również określić parametr metody pobierania próbek do użycia za pośrednictwem definicji przestrzeni hiperparametrycznego. Usługa Azure Machine Learning obsługuje losowego próbkowanie, próbkowania siatki i Bayesowskie próbkowania.

#### <a name="random-sampling"></a>Losowe próbkowania

Podczas próbkowania losowych wartości hiperparametrycznego są wybierane losowo z obszaru search zdefiniowane. Losowej próbki pozwala przestrzeni wyszukiwania uwzględnić hiperparametrów zarówno dyskretnych, jak i ciągłe.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Próbkowanie siatki

Próbkowanie siatki wykonuje wyszukiwanie prosta siatka wszystkie możliwe wartości w obszarze wyszukiwania zdefiniowanych. Można używać tylko z hiperparametrów określony za pomocą `choice`. Na przykład w następującym obszarze ma łącznie sześć próbek:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Próbkowanie Bayesowskie

Próbkowanie Bayesowskie opiera się na algorytm optymalizacji Bayesowskie i sprawia, że inteligentne opcje na wartościach hiperparametrycznego obok próbkowania. Wybiera przykładowe oparte na jak poprzednie przykłady wykonywane, takie, że nowe próbki zwiększa zgłoszonych podstawową metrykę.

Gdy używasz Bayesowskie próbkowania, liczby równoczesnych uruchomień ma wpływ na efektywność procesu dostosowywania. Zazwyczaj mniejszą liczbę równoczesnych uruchomień może prowadzić do lepszego zbieżność próbkowania, ponieważ mniejszych stopień równoległości zwiększa się liczba uruchomień, które korzystają z wcześniej zakończone przebiegi.

Próbkowanie Bayesowskie obsługuje tylko `choice` i `uniform` dystrybucji za pośrednictwem obszaru search. 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Próbkowanie Bayesowskie nie obsługuje wszystkie wcześniejsze zasad zakończenia (zobacz [określić zasady wcześniejsze zakończenie](#specify-early-termination-policy)). Korzystając z Bayesowskie parametru próbkowania, ustaw `early_termination_policy = None`, lub pozostaw wartość `early_termination_policy` parametru.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Określ podstawowe metryki

Należy określić podstawową metrykę ma hiperparametrycznego dostrajania eksperymentu w celu optymalizacji. Każde uruchomienie szkolenia jest oceniane pod kątem podstawową metrykę. Niskiej wydajności uruchomienia (gdzie podstawowe metryki nie spełnia kryteriów ustawionego na podstawie zasad wcześniejsze zakończenie) zostanie zakończona. Oprócz podstawowego nazwa metryki, należy także określić celem Optymalizacja - czy zmaksymalizować lub zminimalizować podstawową metrykę.

* `primary_metric_name`: Nazwa głównej metrykę do optymalizacji. Podstawowe metryki musi dokładnie pasować do nazwy metryki zarejestrowane przez skrypt szkolenia. Zobacz [dziennika metryk do strojenia hiperparametrycznego](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Może być albo `PrimaryMetricGoal.MAXIMIZE` lub `PrimaryMetricGoal.MINIMIZE` i określa, czy podstawowe metryki będą zmaksymalizowane, czy podczas oceny będzie uruchamiany. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optymalizuj przebiegów, aby zmaksymalizować "dokładność".  Upewnij się, że dziennika tej wartości w skrypcie szkolenia.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Rejestruj metryki do strojenia hiperparametrycznego

Skrypt szkoleniowy dla modelu, należy zalogować istotne metryki podczas uczenia modelu. Po skonfigurowaniu strojenia hiperparametrycznego, należy określić podstawową metrykę do użycia do oceny wydajności wykonywania. (Zobacz [określić podstawową metrykę, aby zoptymalizować](#specify-primary-metric-to-optimize).)  W skrypcie szkolenia należy zalogować ta metryka, dzięki czemu są one dostępne dla hiperparametrycznego dostosowywania procesu.

Ta metryka Zaloguj się następującym fragmentem kodu przykładowy skrypt szkolenia:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Oblicza skrypt szkoleniowy `val_accuracy` i rejestruje go jako "dokładność", który jest używany jako podstawowe metryki. Każdorazowo, gdy metryka jest rejestrowany otrzymał hiperparametrycznego dostrajania usługi. Jest developer modelu do określania, jak często zgłosić tej metryki.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Określ wcześniejsze zakończenie zasady

Zakończ niskiej wydajności jest uruchamiany automatycznie z wczesnym zasad zakończenia. Zakończenie zmniejsza nadmierne użycie nadmierny zasobów i zamiast tego używa tych zasobów do eksplorowania innych parametrów konfiguracji.

Korzystając z początku zasad rozwiązania, można skonfigurować następujące parametry, które kontrolują, po zastosowaniu zasad:

* `evaluation_interval`: częstotliwość stosowania zasad. Każdym logowaniu skrypt szkoleniowy podstawowe metryki jest liczona jako jeden interwał. Ten sposób `evaluation_interval` 1 zostaną zastosowane zasady za każdym razem, gdy skrypt szkoleniowy Raporty podstawowe metryki. `evaluation_interval` 2 zostaną zastosowane zasady każdym innym czasie skrypt szkoleniowy Raporty podstawowe metryki. Jeśli nie zostanie określony, `evaluation_interval` jest ustawiona na 1, domyślnie.
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów. Jest to parametr opcjonalny, który zezwala na wszystkie konfiguracje do uruchamiania dla początkowej minimalna liczba przedziałów, unikając przedwczesne zakończenie szkolenia związane z systemem. Jeśli zostanie określony, zostaną zastosowane zasady co wielu evaluation_interval, która jest większa lub równa delay_evaluation.

Usługa Azure Machine Learning obsługuje następujące zasady wcześniejsze zakończenie.

### <a name="bandit-policy"></a>Zasady bandit

Bandit to zasady zakończenia na podstawie slack współczynnik/zapas czasu interwału kwota i oceny. Zasady kończy wcześnie żadnych przebiegów, gdzie podstawowe metryki nie mieści się w określony współczynnik slack / run slack kwota względem najlepiej szkolenia. Zajmuje się następujących parametrów konfiguracji:

* `slack_factor` lub `slack_amount`: slack dozwolona względem najlepiej szkolenia uruchomienia. `slack_factor` Określa dopuszczalne slack jako stosunek. `slack_amount` Określa dopuszczalne slack jako kwotę bezwzględną, zamiast stosunku.

    Na przykład należy wziąć pod uwagę Bandit zasady stosowane w odstępach 10. Załóżmy, że najlepsze wykonywanie uruchomienia w odstępach 10 zgłoszone podstawowe metryki 0,8 za cel, aby zmaksymalizować podstawowe metryki. Jeśli nie określono zasad za pomocą `slack_factor` 0,2, wszelkie szkolenia jest uruchomiony, w których najważniejsze metryki w odstępach 10 jest mniejsza niż 0.66 (0,8 / (1 +`slack_factor`)) zostanie zakończona. Jeśli zamiast tego zasad został określony za pomocą `slack_amount` 0,2, wszelkie szkolenia jest uruchomiony, w których najważniejsze metryki w odstępach 10 jest mniejsza niż Update 0.6 (0,8 - `slack_amount`) zostanie zakończona.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdym interwale, gdy metryki są zgłaszane, zaczynając od interwał oceny 5. Uruchom którego najlepszą metryka jest mniejsza niż (1/(1+0.1) dowolnej 91% najlepszej wydajności uruchamiania zostanie zakończone.

### <a name="median-stopping-policy"></a>Zatrzymywanie mediany zasad

Zatrzymywanie środkowej to wczesne zasady zakończenia oparte na temat uruchamiania średnie podstawowe metryki zgłoszone przez przebiegi. Ta zasada oblicza średnie działającej we wszystkich przebiegów szkoleniowych i kończy się przebiegów, w których wydajność jest niższa niż medianę średnie uruchomione. Ta zasada przyjmuje następujące parametry konfiguracji:
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdej odstępach czasu, zaczynając od interwał oceny 5. Uruchom w odstępach 5 zostaną zakończone, jeśli jego najważniejsze metryki podstawowej jest niższa niż medianę średnie uruchomionych za pośrednictwem interwałów 1:5 we wszystkich przebiegów szkoleniowych.

### <a name="truncation-selection-policy"></a>Obcięcie wybór zasad

Anuluje wybór obcięcie występuje dany procent wykonania najniższy jest uruchamiana po każdym odstępie oceny. Przebiegi są porównywane, na podstawie ich wydajności na podstawowe metryki i najniższego X % są zakończone. Zajmuje się następujących parametrów konfiguracji:

* `truncation_percentage`: procent wykonania najniższy uruchamia się zakończyć w każdym interwale oceny. Określ wartość całkowita z przedziału od 1 do 99.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdej odstępach czasu, zaczynając od interwał oceny 5. Uruchom w odstępach 5, zostaną zakończone, jeśli jego wydajność w odstępach 5 znajduje się w najniższej 20% wyników wszystkich przebiegów w odstępach 5.

### <a name="no-termination-policy"></a>Brak zasad zakończenia

Jeśli chcesz, aby wszystkie przebiegów szkoleniowych do uruchomienia aż do ukończenia, ustawić zasady na wartość None. Będzie to miało wpływu nie można zastosować zasad wcześniejsze zakończenie.

```Python
policy=None
```

### <a name="default-policy"></a>Zasady domyślne

Jeśli zostanie określona żadna zasada, hiperparametrycznego dostrajania usługi umożliwi wszystkich przebiegów szkoleniowych, które zostało ukończone.

>[!NOTE] 
>Jeśli szukasz zachowawcze zasady, które zapewnia oszczędności bez przerywania zadań obietnic można użyć zasad zatrzymywanie mediana z `evaluation_interval` 1 i `delay_evaluation` 5. Te są konserwatywnego ustawienia, które może zapewnić około 25 – 35% oszczędności bez utraty podstawową metrykę (oparte na naszych danych oceny).

## <a name="allocate-resources"></a>Przydzielanie zasobów

Kontroli budżetu zasobów dla Twojego hiperparametrycznego dostrajania eksperymentu, określając maksymalna całkowita liczba przebiegów szkoleniowych.  Opcjonalnie można określić maksymalny czas trwania dla Twojego hiperparametrycznego dostrajania eksperymentu.

* `max_total_runs`: Maksymalna łączna liczba przebiegów szkoleniowych, które zostaną utworzone. Górna granica — może mieć mniejszej liczby przebiegów, na przykład miejsce hiperparametrycznego jest jednak ograniczona i ma mniejszą liczbę próbek. Musi być liczbą z przedziału od 1 do 1000.
* `max_duration_minutes`: Maksymalny czas trwania w minutach hiperparametrycznego dostrajania eksperymentu. Parametr jest opcjonalny, a jeśli jest obecny, wszystkie przebiegi uruchomione po tym czasie zostaną automatycznie anulowane.

>[!NOTE] 
>Jeśli oba `max_total_runs` i `max_duration_minutes` podano hiperparametrycznego dostrajania eksperymentu kończy działanie, gdy zostanie osiągnięty pierwszy z tych dwóch progów.

Ponadto można określić maksymalną liczbę szkolenia uruchomienia uruchamianych jednocześnie podczas Twojej hiperparametrycznego dostrajania wyszukiwania.

* `max_concurrent_runs`: Maksymalna liczba przepływów można uruchomić jednocześnie w danym momencie. Jeśli nie, wszystkie `max_total_runs` będą uruchamiane równolegle. Jeśli zostanie określony, musi być liczbą z przedziału od 1 do 100.

>[!NOTE] 
>Liczba równoczesnych uruchomień jest uzyskiwany na zasobach, które są dostępne w określonej obliczeniowego elementu docelowego. Z tego powodu należy upewnić się, że obliczeniowego elementu docelowego dostępnych zasobów dla żądanego współbieżności.

Przydzielanie zasobów do strojenia hiperparametrycznego:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ten kod konfiguruje hiperparametrycznego dostrajania eksperymentu można użyć maksymalnie 20, łączna liczba uruchomień uruchamiania 4 konfiguracji w danym momencie.

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

Skonfiguruj swoje hiperparametrycznego dostrajania eksperymentu przy użyciu przestrzeni wyszukiwania zdefiniowanych hiperparametrycznego, wcześniejsze zakończenie zasad, podstawowe metryki i alokacji zasobów z powyższej sekcji. Ponadto zapewniają `estimator` , zostanie wywołana z próbkowanych hiperparametrów. `estimator` Opisuje skrypt szkoleniowy uruchomieniu, zasoby na zadanie (jednym lub wieloma procesorami gpu) i obliczeniowego elementu docelowego do użycia. Ponieważ współbieżności dla usługi hiperparametrycznego dostrajania eksperymentu są bramkowane na dostępnych zasobów, upewnij się, że obliczeniowego elementu docelowego określona w `estimator` ma wystarczające zasoby, odpowiednią współbieżność. (Aby uzyskać więcej informacji na temat aplikacjom, zobacz [sposób trenowania modeli](how-to-train-ml-models.md).)

Skonfiguruj swoje hiperparametrycznego dostrajania eksperymentu:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Przesyłanie eksperymentu

Po zdefiniowaniu swoje hiperparametrycznego dostrajania konfiguracji, przysyłanie eksperymentu:

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` to nazwa, przypisz do Twojego hiperparametrycznego dostrajania eksperymentu, a `workspace` jest obszarem roboczym, w którym chcesz utworzyć eksperyment (Aby uzyskać więcej informacji na temat doświadczeń, zobacz [jak działa usługa Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="visualize-experiment"></a>Wizualizacja doświadczenia

Zestaw SDK usługi Azure Machine Learning zapewnia uruchomienia elementu widget Notes, która wizualizuje postęp szkolenia. Poniższy fragment kodu wizualizuje swoje hiperparametrycznego dostrajania jest uruchamiany w jednym miejscu w notesie Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ten kod wyświetla tabelę zawierającą szczegóły dotyczące przebiegów szkoleniowych dla każdej konfiguracji hiperparametrycznego.

![tabeli strojenia hiperparametrycznego](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Można również wizualizować wykonanie każdego uruchomienia w miarę postępów szkoleń. 

![Wykres strojenia hiperparametrycznego](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ponadto można zidentyfikować wizualnie korelacji między wydajnością i wartości poszczególnych hiperparametrów przy użyciu równoległych współrzędnych wykresu. 

![hiperparametrycznego dostrajania współrzędne równoległe](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Można wizualizować swoje hiperparametrycznego dostrajania jest uruchamiany w witrynie portal sieci web platformy Azure. Aby uzyskać więcej informacji na temat sposobu wyświetlania eksperymentu w portalu sieci web, zobacz [jak śledzić eksperymentów](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

![portal strojenia hiperparametrycznego](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-best-model"></a>Znajdź najlepszy model

Po wszystkich hiperparametrycznego dostrajania przebiegi ukończone, należy określić najlepsze wykonywanie konfiguracji i odpowiadające im wartości hiperparametrycznego:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Przykładowy notes
Zapoznaj się z tymi notesów:
* [How-to-use-azureml/Training-with-deep-Learning/Train-hyperparameter-Tune-Deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) 
* [How-to-use-azureml/Training-with-deep-Learning/Train-hyperparameter-Tune-Deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Śledź eksperymentu](how-to-track-experiments.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
