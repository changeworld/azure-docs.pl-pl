---
title: Dostosowywanie hiperparametrów dla modelu
titleSuffix: Azure Machine Learning
description: Wydajnie Dostrajaj parametry dla modelu uczenie głębokiego i uczenia maszynowego przy użyciu Azure Machine Learning. Dowiesz się, jak definiować miejsce wyszukiwania parametrów, określać metrykę podstawową do optymalizacji i wczesne zakończenie działania.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4ca9cac00b9d00dcdbbd27f2fe769de2983c13ae
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536644"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Dostrajanie parametrów dla modelu za pomocą Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Efektywne dostrajanie parametrów dla modelu przy użyciu Azure Machine Learning.  Do strojenia Hiperparametrycznego obejmuje następujące kroki:

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

Każdy parametr może być dyskretny lub ciągły i ma rozkład wartości opisany przez [wyrażenie parametru](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

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

Można również określić parametr metody pobierania próbek do użycia za pośrednictwem definicji przestrzeni hiperparametrycznego. Azure Machine Learning obsługuje Próbkowanie losowe, próbkowanie siatki i próbkowanie bayesowskie.

#### <a name="picking-a-sampling-method"></a>Pobieranie metody próbkowania

* Próbkowanie siatki może być używane, jeśli miejsce na parametrze można zdefiniować jako wybór spośród dyskretnych wartości, a jeśli masz wystarczający budżet, aby wyczerpująco przeszukiwać wszystkie wartości w zdefiniowanym obszarze wyszukiwania. Ponadto jeden może korzystać z zautomatyzowanego wczesnego zakończenia działania, co zmniejsza marnowanie zasobów.
* Próbkowanie losowe umożliwia przełączenie między parametrami dyskretnych i ciągłych. W tej chwili daje ona dobre wyniki, a także umożliwia korzystanie z zautomatyzowanego wczesnego zakończenia uruchamiania. Niektórzy użytkownicy wykonują początkowe wyszukiwanie przy użyciu próbkowania losowego, a następnie iteracyjnie ograniczają miejsce wyszukiwania, aby poprawić wyniki.
* Próbkowanie bayesowskie wykorzystuje znajomość poprzednich przykładów podczas wybierania wartości parametrów, efektywnie próbując zwiększyć zgłoszoną metrykę podstawową. Bayesowskie próbkowanie jest zalecane, gdy masz wystarczający budżet do eksplorowania miejsca na parametrze — w celu uzyskania najlepszych wyników z próbkowania bayesowskie zalecamy użycie maksymalnej liczby przebiegów o wartości większej lub równej 20 razy. Należy pamiętać, że próbkowanie bayesowskie nie obsługuje obecnie żadnych zasad wczesnego zakończenia.

#### <a name="random-sampling"></a>Losowe próbkowania

Podczas próbkowania losowych wartości hiperparametrycznego są wybierane losowo z obszaru search zdefiniowane. [Próbkowanie losowe](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) umożliwia miejsce do wyszukiwania, w którym są uwzględniane parametry dyskretne i ciągłe.

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

[Próbkowanie siatki](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) wykonuje proste przeszukiwanie siatki dla wszystkich dopuszczalnych wartości w zdefiniowanym obszarze wyszukiwania. Można używać tylko z hiperparametrów określony za pomocą `choice`. Na przykład w następującym obszarze ma łącznie sześć próbek:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Próbkowanie Bayesowskie

[Próbkowanie bayesowskie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) jest oparte na algorytmie optymalizacji bayesowskie i umożliwia inteligentne wybór wartości parametrów w celu próbkowania dalej. Wybiera przykładowe oparte na jak poprzednie przykłady wykonywane, takie, że nowe próbki zwiększa zgłoszonych podstawową metrykę.

Gdy używasz Bayesowskie próbkowania, liczby równoczesnych uruchomień ma wpływ na efektywność procesu dostosowywania. Zazwyczaj mniejszą liczbę równoczesnych uruchomień może prowadzić do lepszego zbieżność próbkowania, ponieważ mniejszych stopień równoległości zwiększa się liczba uruchomień, które korzystają z wcześniej zakończone przebiegi.

Próbkowanie bayesowskie obsługuje tylko dystrybucje `choice`, `uniform`i `quniform` w obszarze wyszukiwania.

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

Określ [metrykę podstawową](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) , która ma zostać zoptymalizowana przez dostrajanie parametrów. Każde uruchomienie szkolenia jest oceniane pod kątem podstawową metrykę. Niskiej wydajności uruchomienia (gdzie podstawowe metryki nie spełnia kryteriów ustawionego na podstawie zasad wcześniejsze zakończenie) zostanie zakończona. Oprócz podstawowego nazwa metryki, należy także określić celem Optymalizacja - czy zmaksymalizować lub zminimalizować podstawową metrykę.

* `primary_metric_name`: Nazwa podstawową metrykę do optymalizacji. Podstawowe metryki musi dokładnie pasować do nazwy metryki zarejestrowane przez skrypt szkolenia. Zobacz [dziennika metryk do strojenia hiperparametrycznego](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Może być albo `PrimaryMetricGoal.MAXIMIZE` lub `PrimaryMetricGoal.MINIMIZE` i określa, czy podstawowe metryki będą zmaksymalizowane, czy podczas oceny będzie uruchamiany. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optymalizuj przebiegów, aby zmaksymalizować "dokładność".  Upewnij się, że dziennika tej wartości w skrypcie szkolenia.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Rejestruj metryki do strojenia hiperparametrycznego

Skrypt szkoleniowy dla modelu, należy zalogować istotne metryki podczas uczenia modelu. Po skonfigurowaniu strojenia hiperparametrycznego, należy określić podstawową metrykę do użycia do oceny wydajności wykonywania. (Zobacz [Określ metrykę podstawową do optymalizacji](#specify-primary-metric-to-optimize)).  W skrypcie szkoleniowym należy zarejestrować tę metrykę, aby była dostępna dla procesu strojenia parametru.

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

Azure Machine Learning obsługuje następujące zasady wczesnego zakończenia.

### <a name="bandit-policy"></a>Zasady bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) to zasady zakończenia na podstawie wartości współczynnika zapasu/zapasu czasu i interwału ewaluacji. Zasady kończy wcześnie żadnych przebiegów, gdzie podstawowe metryki nie mieści się w określony współczynnik slack / run slack kwota względem najlepiej szkolenia. Zajmuje się następujących parametrów konfiguracji:

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

[Mediana zatrzymywana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) to zasady wczesnej zakończenia na podstawie średnich podstawowych metryk raportowanych przez uruchomienia. Ta zasada oblicza średnie działającej we wszystkich przebiegów szkoleniowych i kończy się przebiegów, w których wydajność jest niższa niż medianę średnie uruchomione. Ta zasada przyjmuje następujące parametry konfiguracji:
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdej odstępach czasu, zaczynając od interwał oceny 5. Uruchom w odstępach 5 zostaną zakończone, jeśli jego najważniejsze metryki podstawowej jest niższa niż medianę średnie uruchomionych za pośrednictwem interwałów 1:5 we wszystkich przebiegów szkoleniowych.

### <a name="truncation-selection-policy"></a>Obcięcie wybór zasad

[Zaznaczenie obcięcia](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) powoduje anulowanie danego procentu najniższych przebiegów wykonywanych w każdym interwale oceny. Przebiegi są porównywane, na podstawie ich wydajności na podstawowe metryki i najniższego X % są zakończone. Zajmuje się następujących parametrów konfiguracji:

* `truncation_percentage`: procent wykonania najniższy uruchamia się zakończyć w każdym interwale oceny. Określ wartość całkowita z przedziału od 1 do 99.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdej odstępach czasu, zaczynając od interwał oceny 5. Przebieg zostanie zakończony z upływem interwału 5, jeśli jego wydajność w interwale 5 jest w najmniejszej 20% wydajności wszystkich przebiegów w interwale 5.

### <a name="no-termination-policy"></a>Brak zasad zakończenia

Jeśli chcesz, aby wszystkie przebiegów szkoleniowych do uruchomienia aż do ukończenia, ustawić zasady na wartość None. Będzie to miało wpływu nie można zastosować zasad wcześniejsze zakończenie.

```Python
policy=None
```

### <a name="default-policy"></a>Zasady domyślne

Jeśli żadna zasada nie zostanie określona, usługa strojenia parametrów będzie zezwalać na wykonywanie wszystkich operacji szkoleniowych.

### <a name="picking-an-early-termination-policy"></a>Wybieranie zasad wczesnego zakończenia

* Jeśli szukasz zachowawcze zasady, które zapewnia oszczędności bez przerywania zadań obietnic można użyć zasad zatrzymywanie mediana z `evaluation_interval` 1 i `delay_evaluation` 5. Te są konserwatywnego ustawienia, które może zapewnić około 25 – 35% oszczędności bez utraty podstawową metrykę (oparte na naszych danych oceny).
* Jeśli szukasz bardziej agresywnego oszczędności ze wczesnego zakończenia, możesz użyć zasad Bandit z bardziej restrykcyjną (mniejszą) lub zasadą wyboru obcinania z większym procentem obcięcia.

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

Ten kod umożliwia skonfigurowanie eksperymentu strojenia parametrów w celu użycia maksymalnie 20 łącznych uruchomień z czterema konfiguracjami jednocześnie.

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

[Skonfiguruj eksperyment strojenia parametrów](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) przy użyciu zdefiniowanego miejsca wyszukiwania z parametrami, zasad wczesnego zakończenia, metryki podstawowej i alokacji zasobów w powyższych sekcjach. Ponadto zapewniają `estimator` , zostanie wywołana z próbkowanych hiperparametrów. `estimator` Opisuje skrypt szkoleniowy uruchomieniu, zasoby na zadanie (jednym lub wieloma procesorami gpu) i obliczeniowego elementu docelowego do użycia. Ponieważ współbieżności dla usługi hiperparametrycznego dostrajania eksperymentu są bramkowane na dostępnych zasobów, upewnij się, że obliczeniowego elementu docelowego określona w `estimator` ma wystarczające zasoby, odpowiednią współbieżność. (Aby uzyskać więcej informacji na temat aplikacjom, zobacz [sposób trenowania modeli](how-to-train-ml-models.md).)

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

Po zdefiniowaniu konfiguracji strojenia parametru [Prześlij eksperyment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` to nazwa przypisana do eksperymentu strojenia parametrów, a `workspace` to obszar roboczy, w którym chcesz utworzyć eksperyment (Aby uzyskać więcej informacji na temat eksperymentów, zobacz [jak działa Azure Machine Learning](concept-azure-machine-learning-architecture.md)).

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Rozpoczęcie eksperymentu strojenia parametrów (opcjonalnie)

Często Znajdowanie wartości najlepszych parametrów dla modelu może być procesem iteracyjnym, wymagając wielu przebiegów strojenia, które uczy się od poprzednich przebiegów dostrajania. Użycie wiedzy z tych poprzednich przebiegów spowoduje przyspieszenie procesu strojenia parametrów, co zmniejsza koszt dostrajania modelu i może poprawić podstawową metrykę modelu powstającego. Po rozpoczęciu uruchamiania eksperymentu strojenia z bayesowskie próbkowania testy z poprzedniego przebiegu będą używane jako wcześniejsza wiedza, aby inteligentnie wybrać nowe przykłady, aby zwiększyć podstawową metrykę. Ponadto w przypadku próbkowania losowego lub siatki wszelkie wczesne decyzje o wygaśnięciu będą korzystać z metryk z poprzednich przebiegów, aby określić niewłaściwie wykonywane przebiegi szkoleniowe. 

Azure Machine Learning pozwala na rozgrzanie przebiegu strojenia parametrów, wykorzystując wiedzę z maksymalnie 5 poprzednio ukończonych/anulowanych przebiegów dostrojenia parametrów nadrzędnych. Możesz określić listę przebiegów nadrzędnych, z których chcesz zacząć korzystać z tego fragmentu kodu:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Ponadto mogą wystąpić sytuacje, w których poszczególne przebiegi szkoleniowe dla eksperymentu strojenia parametrów zostały anulowane z powodu ograniczeń budżetu lub niepowodzenia z innych przyczyn. Teraz można wznowić takie indywidualne szkolenia z ostatniego punktu kontrolnego (przy założeniu, że skrypt szkoleniowy obsługuje punkty kontrolne). Wznowienie pojedynczego przebiegu szkoleniowego spowoduje użycie tej samej konfiguracji z parametrem i zainstalowanie folderu danych wyjściowych używanego dla tego przebiegu. Skrypt szkoleniowy powinien akceptować `resume-from` argument, który zawiera punkt kontrolny lub pliki modelu, z których ma zostać wznowione uruchomienie szkolenia. Poszczególne przebiegi szkoleniowe można wznowić, korzystając z następującego fragmentu kodu:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Możesz skonfigurować eksperyment strojenia parametrów, aby rozpocząć pracę z poprzedniego eksperymentu lub wznowić indywidualne szkolenia przy użyciu parametrów opcjonalnych, `resume_from` i `resume_child_runs` w konfiguracji:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Wizualizacja doświadczenia

Zestaw Azure Machine Learning SDK udostępnia [widżet Notatnik](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) , który wizualizuje postępy przebiegów szkoleniowych. Poniższy fragment kodu wizualizuje swoje hiperparametrycznego dostrajania jest uruchamiany w jednym miejscu w notesie Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ten kod wyświetla tabelę zawierającą szczegóły dotyczące przebiegów szkoleniowych dla każdej konfiguracji hiperparametrycznego.

![tabeli strojenia hiperparametrycznego](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Można również wizualizować wykonanie każdego uruchomienia w miarę postępów szkoleń. 

![Wykres strojenia hiperparametrycznego](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ponadto można zidentyfikować wizualnie korelacji między wydajnością i wartości poszczególnych hiperparametrów przy użyciu równoległych współrzędnych wykresu. 

[Dopasowywanie równoległych współrzędnych ![](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Można wizualizować swoje hiperparametrycznego dostrajania jest uruchamiany w witrynie portal sieci web platformy Azure. Aby uzyskać więcej informacji na temat sposobu wyświetlania eksperymentu w portalu sieci web, zobacz [jak śledzić eksperymentów](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Znajdź najlepszy model

Po zakończeniu wszystkich przebiegów strojenia parametrów należy [zidentyfikować najlepszą wykonywaną konfigurację](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) oraz odpowiednie wartości parametrów:

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
Zapoznaj się z notesem uczenia-*-* w tym folderze:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki
* [Śledź eksperymentu](how-to-track-experiments.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
