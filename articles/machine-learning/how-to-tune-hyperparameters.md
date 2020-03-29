---
title: Dostrajanie hiperparametrów dla swojego modelu
titleSuffix: Azure Machine Learning
description: Efektywne dostrajanie hiperparametrów dla modelu uczenia głębokiego / uczenia maszynowego przy użyciu usługi Azure Machine Learning. Dowiesz się, jak zdefiniować przestrzeń wyszukiwania parametrów, określić metrykę podstawową do optymalizacji i wczesne zakończenie wykonywania przebiegów o niskiej wydajności.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536644"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Dostrajanie hiperparametrów dla swojego modelu za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Efektywne dostrajanie hiperparametrów dla modelu przy użyciu usługi Azure Machine Learning.  Dostrajanie hiperparametryczne obejmuje następujące kroki:

* Definiowanie przestrzeni wyszukiwania parametrów
* Określanie metryki podstawowej do optymalizacji  
* Określ kryteria wcześniejszego zakończenia dla słabo wydajnych przebiegów
* Przydzielanie zasobów do strojenia hiperparametryczego
* Rozpoczęcie eksperymentu z powyższą konfiguracją
* Wizualizuj przebiegi szkoleniowe
* Wybierz najlepszą konfigurację dla swojego modelu

## <a name="what-are-hyperparameters"></a>Co to są hiperparametry?

Hiperparametry to regulowane parametry, które umożliwiają szkolenie modelu, które regulują sam proces szkolenia. Na przykład, aby wyszkolić głęboką sieć neuronową, należy określić liczbę ukrytych warstw w sieci i liczbę węzłów w każdej warstwie przed szkoleniem modelu. Wartości te zwykle pozostają stałe podczas procesu szkolenia.

W scenariuszach uczenia głębokiego / uczenia maszynowego wydajność modelu zależy w dużej mierze od wybranych wartości hiperparametru. Celem eksploracji hiperparametryczne jest wyszukiwanie w różnych konfiguracjach hiperparametru, aby znaleźć konfigurację, która powoduje najlepszą wydajność. Zazwyczaj proces eksploracji hiperparametrycznie jest skrupulatnie ręczny, biorąc pod uwagę, że przestrzeń wyszukiwania jest ogromna, a ocena każdej konfiguracji może być kosztowna.

Usługa Azure Machine Learning umożliwia wydajne automatyzację eksploracji hiperparametrycznym, co pozwala zaoszczędzić dużo czasu i zasobów. Należy określić zakres wartości hiperparametru i maksymalną liczbę przebiegów szkoleniowych. Następnie system automatycznie uruchamia wiele jednoczesnych przebiegów z różnymi konfiguracjami parametrów i znajduje konfigurację, która zapewnia najlepszą wydajność, mierzoną przez metrykę, którą wybierzesz. Słabo wydajne przebiegi szkoleniowe są automatycznie wcześnie zakończone, co zmniejsza marnotrawstwo zasobów obliczeniowych. Te zasoby są zamiast tego używane do eksplorowania innych konfiguracji hiperparametru.


## <a name="define-search-space"></a>Definiowanie przestrzeni wyszukiwania

Automatyczne dostrajanie hiperparametrów przez eksplorowanie zakresu wartości zdefiniowanych dla każdego hiperparametru.

### <a name="types-of-hyperparameters"></a>Rodzaje hiperparametrów

Każdy hiperparametr może być dyskretny lub ciągły i ma rozkład wartości opisanych przez [wyrażenie parametru.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py)

#### <a name="discrete-hyperparameters"></a>Dyskretne hiperparametry 

Dyskretne hiperparametry są określone `choice` jako wartości dyskretne. `choice`może to być:

* jedna lub więcej wartości oddzielonych przecinkami
* `range` obiekt
* dowolny `list` dowolny obiekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

W tym `batch_size` przypadku przyjmuje jedną z wartości [16, 32, 64, `number_of_hidden_layers` 128] i przyjmuje jedną z wartości [1, 2, 3, 4].

Zaawansowane hiperparametry dyskretne można również określić za pomocą rozkładu. Obsługiwane są następujące dystrybucje:

* `quniform(low, high, q)`- Zwraca wartość jak okrągła (jednolita (niska, wysoka) / q) * q
* `qloguniform(low, high, q)`- Zwraca wartość jak okrągła(exp(uniform(niska, wysoka)) / q) * q
* `qnormal(mu, sigma, q)`- Zwraca wartość jak okrągły (normalny(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`- Zwraca wartość jak round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Ciągłe hiperparametry 

Ciągłe hiperparametry są określone jako rozkład w ciągłym zakresie wartości. Obsługiwane dystrybucje obejmują:

* `uniform(low, high)`- Zwraca wartość równomiernie rozłożoną między niskim i wysokim
* `loguniform(low, high)`- Zwraca wartość narysowaną zgodnie z exp(uniform(low, high)), tak aby logarytm zwracanej wartości był równomiernie rozłożony
* `normal(mu, sigma)`- Zwraca rzeczywistą wartość, która jest zwykle rozłożona ze średnią mu i odchylenia standardowego sigma
* `lognormal(mu, sigma)`- Zwraca wartość narysowaną zgodnie z exp(normal(mu, sigma)), tak aby logarytm zwracanej wartości jest normalnie dystrybuowany

Przykład definicji obszaru parametrów:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ten kod definiuje przestrzeń wyszukiwania z `learning_rate` `keep_probability`dwoma parametrami - i . `learning_rate`ma rozkład normalny o średniej wartości 10 i odchylenie standardowe 3. `keep_probability`ma równomierny rozkład o minimalnej wartości 0,05 i maksymalnej wartości 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Pobieranie próbek przestrzeni hiperparametryczej

Można również określić metodę próbkowania parametrów, która ma być używana w definicji przestrzeni hiperparametryczej. Usługa Azure Machine Learning obsługuje losowe próbkowanie, próbkowanie siatki i próbkowanie Bayesa.

#### <a name="picking-a-sampling-method"></a>Pobieranie metody pobierania próbek

* Próbkowanie siatki może być używane, jeśli przestrzeń hiperparametryczna może być zdefiniowana jako wybór między wartościami dyskretnymi i jeśli masz wystarczający budżet, aby wyczerpująco przeszukiwać wszystkie wartości w zdefiniowanej przestrzeni wyszukiwania. Dodatkowo można użyć automatycznego wcześniejszego zakończenia słabo wydajnych przebiegów, co zmniejsza marnotrawstwo zasobów.
* Losowe próbkowanie pozwala przestrzeni hiperparametryczne obejmują zarówno dyskretne, jak i ciągłe hiperparametry. W praktyce daje dobre wyniki w większości przypadków, a także pozwala na stosowanie automatycznego wcześniejszego zakończenia słabo działających przebiegów. Niektórzy użytkownicy wykonują wyszukiwanie początkowe przy użyciu losowego próbkowania, a następnie iteracyjne uściślić przestrzeń wyszukiwania, aby poprawić wyniki.
* Pobieranie próbek bayesowskich wykorzystuje wiedzę na temat poprzednich próbek przy wyborze wartości hiperparametru, skutecznie próbując poprawić raportowane metryki pierwotne. Pobieranie próbek bayesowskich jest zalecane, gdy masz wystarczający budżet na zbadanie przestrzeni hiperparametryczej - aby uzyskać najlepsze wyniki z Bayesian Sampling zalecamy użycie maksymalnej liczby przebiegów większych lub równych 20-krotności liczby dostrojonych hiperparametrów. Należy zauważyć, że pobieranie próbek Bayesian nie obsługuje obecnie żadnych zasad wcześniejszego zakończenia.

#### <a name="random-sampling"></a>Próbkowanie losowe

W losowym pobieraniu próbek wartości hiperparametru są wybierane losowo ze zdefiniowanej przestrzeni wyszukiwania. [Losowe próbkowanie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) umożliwia obszar wyszukiwania obejmują zarówno dyskretne, jak i ciągłe hiperparametry.

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

[Próbkowanie siatki](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) wykonuje proste wyszukiwanie siatki nad wszystkimi możliwymi wartościami w zdefiniowanej przestrzeni wyszukiwania. Może być używany tylko z hiperparametrami określonymi za pomocą `choice`. Na przykład następujące miejsce ma w sumie sześć próbek:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Próbkowanie Bayesowskie

[Pobieranie próbek bayesowskich](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) opiera się na algorytmie optymalizacji Bayesa i dokonuje inteligentnych wyborów na wartościach hiperparametru, aby pobrać próbkę dalej. Wybiera próbki na podstawie sposobu wykonania poprzednich próbek, tak aby nowa próbka poprawia raportowana metryka pierwotna.

Podczas korzystania z bayesowskich próbkowania liczba równoczesnych przebiegów ma wpływ na skuteczność procesu strojenia. Zazwyczaj mniejsza liczba równoczesnych przebiegów może prowadzić do lepszej zbieżności próbkowania, ponieważ mniejszy stopień równoległości zwiększa liczbę przebiegów, które korzystają z wcześniej ukończonych przebiegów.

Pobieranie próbek w `choice`Bayesian obsługuje tylko program `uniform`, i `quniform` dystrybucje w przestrzeni wyszukiwania.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Pobieranie próbek w bayesowskim pobieraniu próbek nie obsługuje żadnych zasad wcześniejszego zakończenia (zobacz [Określanie zasad wcześniejszego zakończenia).](#specify-early-termination-policy) W przypadku korzystania z próbkowania parametrów Bayesa należy ustawić `early_termination_policy = None`lub pozostawić `early_termination_policy` parametr.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Określanie metryki podstawowej

Określ [podstawową metrykę,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) którą ma zoptymalizować eksperyment dostrajania hiperparametrycznego. Każdy przebieg szkolenia jest oceniany dla metryki podstawowej. Słabo działające przebiegi (w przypadku gdy metryka podstawowa nie spełnia kryteriów określonych przez zasady wcześniejszego zakończenia) zostaną zakończone. Oprócz podstawowej nazwy metryki można również określić cel optymalizacji — czy zmaksymalizować lub zminimalizować metryki podstawowej.

* `primary_metric_name`: Nazwa metryki podstawowej do optymalizacji. Nazwa metryki podstawowej musi dokładnie odpowiadać nazwie metryki rejestrowanej przez skrypt szkolenia. Zobacz [Log metryki dla dostrajania hiperparametrycznego](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Może to `PrimaryMetricGoal.MAXIMIZE` być `PrimaryMetricGoal.MINIMIZE` albo lub określa, czy metryka podstawowa zostanie zmaksymalizowana lub zminimalizowana podczas oceny przebiegów. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Zoptymalizuj przebiegi, aby zmaksymalizować "dokładność".  Pamiętaj, aby rejestrować tę wartość w skrypcie treningowym.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Rejestrowanie danych dotyczących dostrajania hiperparametrycznego

Skrypt szkolenia dla modelu musi rejestrować odpowiednie metryki podczas szkolenia modelu. Podczas konfigurowania dostrajania hiperparametrycznego należy określić metrykę podstawową, która ma być używana do oceny wydajności uruchamiania. (Zobacz [Określanie metryki podstawowej do optymalizacji).](#specify-primary-metric-to-optimize)  W skrypcie szkolenia należy zarejestrować tę metrykę, aby była dostępna dla procesu dostrajania hiperparametrycznego.

Zarejestruj tę metrykę w skrypcie treningowym za pomocą następującego fragmentu przykładu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Skrypt szkolenia oblicza `val_accuracy` i rejestruje go jako "dokładność", który jest używany jako metryka podstawowa. Za każdym razem, gdy metryka jest rejestrowana, jest odbierana przez usługę dostrajania hiperparametrycznego. To do dewelopera modelu, aby określić, jak często raportować tę metrykę.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Określanie zasad wcześniejszego zakończenia

Automatyczne zakończenie wykonywania przebiegów z zasadą wcześniejszego zakończenia. Zakończenie zmniejsza marnotrawstwo zasobów i zamiast tego używa tych zasobów do eksplorowania innych konfiguracji parametrów.

Korzystając z zasad wcześniejszego zakończenia, można skonfigurować następujące parametry, które kontrolują, gdy zasada jest stosowana:

* `evaluation_interval`: częstotliwość stosowania zasad. Za każdym razem, gdy skrypt szkolenia rejestruje metrykę podstawową liczy się jako jeden interwał. W `evaluation_interval` związku z tym z 1 będzie stosować zasady za każdym razem skrypt szkolenia raportuje metryki podstawowej. Liczba `evaluation_interval` 2 będzie stosować zasady co drugi raz skrypt szkolenia raportuje metrykę podstawową. Jeśli nie `evaluation_interval` zostanie określony, jest domyślnie ustawiona na 1.
* `delay_evaluation`: opóźnia pierwszą ocenę zasad dla określonej liczby interwałów. Jest to opcjonalny parametr, który umożliwia uruchamianie wszystkich konfiguracji dla początkowej minimalnej liczby interwałów, unikając przedwczesnego zakończenia przebiegów szkoleniowych. Jeśli zostanie określony, zasady stosuje się co wielokrotność evaluation_interval, która jest większa lub równa delay_evaluation.

Usługa Azure Machine Learning obsługuje następujące zasady wcześniejszego zakończenia.

### <a name="bandit-policy"></a>Polityka bandytów

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) jest zasadą zakończenia opartą na współczynniku luzu/kwocie luzu i interwale oceny. Zasady na początku kończy wszystkie przebiegi, gdzie metryka podstawowa nie jest w ramach określonego współczynnika luzu / ilość zapasu w odniesieniu do najskuteczniejszego przebiegu szkolenia. Przyjmuje następujące parametry konfiguracji:

* `slack_factor`lub `slack_amount`: luz dozwolony w odniesieniu do najlepiej radzącego sobie przebiegu treningowego. `slack_factor`określa dopuszczalny luz jako współczynnik. `slack_amount`określa dopuszczalny zapas zapasów jako kwotę bezwzględną, a nie współczynnik.

    Rozważmy na przykład zasady Bandit stosowane w odstępach 10. Załóżmy, że najlepsze wykonanie uruchomienia w interwale 10 zgłosiło metrykę podstawową 0,8 z celem maksymalizacji metryki podstawowej. Jeśli zasada została określona `slack_factor` z a 0.2, wszystkie przebiegi szkolenia, których najlepsze metryki w interwale 10 jest`slack_factor`mniejsza niż 0,66 (0,8/(1+ )) zostaną zakończone. Jeśli zamiast tego zasady został `slack_amount` określony z 0,2, wszystkie przebiegi szkolenia, których najlepsze metryki w interwale 10 jest mniejsza niż 0,6 (0,8 - `slack_amount`) zostaną zakończone.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszą ocenę zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wcześniejszego zakończenia są stosowane w każdym przedziale, gdy dane są zgłaszane, począwszy od interwału oceny 5. Każdy bieg, którego najlepsza metryka jest mniejsza niż (1/(1+0,1) lub 91% najlepszego przebiegu zostanie zakończony.

### <a name="median-stopping-policy"></a>Mediana zasad zatrzymywania

[Mediana zatrzymania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) jest zasadą wcześniejszego zakończenia opartą na średnich biegowych metryk podstawowych zgłaszanych przez przebiegi. Ta zasada oblicza średnie biegowe we wszystkich przebiegach szkoleniowych i kończy przebiegi, których wydajność jest gorsza niż mediana średnich biegowych. Ta zasada przyjmuje następujące parametry konfiguracji:
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszą ocenę zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wcześniejszego zakończenia są stosowane w każdym przedziale, począwszy od interwału oceny 5. Przebieg zostanie zakończony w odstępie 5, jeśli jego najlepsza metryka podstawowa jest gorsza niż mediana średnich biegowych w odstępach 1:5 we wszystkich przebiegach treningowych.

### <a name="truncation-selection-policy"></a>Zasady wyboru obcinania

[Wybór obcinania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) anuluje dany procent przebiegów o najniższej wydajności w każdym interwale oceny. Przebiegi są porównywane na podstawie ich wydajności na metryki podstawowej i najniższy X% są zakończone. Przyjmuje następujące parametry konfiguracji:

* `truncation_percentage`: procent przebiegów o najniższej skuteczności do zakończenia w każdym przedziale oceny. Określ wartość całkowitą z 1 do 99.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszą ocenę zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie zasady wcześniejszego zakończenia są stosowane w każdym przedziale, począwszy od interwału oceny 5. Przebieg zostanie zakończony w interwale 5, jeśli jego wydajność w interwale 5 jest w najniższym 20% wydajności wszystkich uruchomień w interwale 5.

### <a name="no-termination-policy"></a>Brak zasad rozwiązania umowy

Jeśli chcesz, aby wszystkie przebiegi szkolenia zostały uruchomione do ukończenia, ustaw zasady na Brak. Spowoduje to niestosowanie żadnych zasad wcześniejszego rozwiązania umowy.

```Python
policy=None
```

### <a name="default-policy"></a>Zasady domyślne

Jeśli nie określono żadnych zasad, usługa dostrajania hiperparametryczne pozwoli wszystkie przebiegi szkolenia wykonać do zakończenia.

### <a name="picking-an-early-termination-policy"></a>Wybieranie zasady wcześniejszego rozwiązania umowy

* Jeśli szukasz konserwatywnej polityki, która zapewnia oszczędności bez przerywania obiecujących zadań, możesz użyć zasady zatrzymania mediany z `evaluation_interval` 1 i `delay_evaluation` 5. Są to ustawienia zachowawcze, które mogą zapewnić około 25%-35% oszczędności bez utraty podstawowego wskaźnika (na podstawie naszych danych ewaluacyjnych).
* Jeśli szukasz bardziej agresywnych oszczędności z przedterminowego zakończenia, możesz użyć Polityki Bandytów z bardziej rygorystycznym (mniejszym) dopuszczalnym zapasem luzu lub zasadą wyboru obcinania o większym procentie obcinania.

## <a name="allocate-resources"></a>Przydzielanie zasobów

Kontroluj budżet zasobów w eksperymencie dostrajania hiperparametrycznym, określając maksymalną całkowitą liczbę przebiegów szkoleniowych.  Opcjonalnie określ maksymalny czas trwania eksperymentu dostrajania hiperparametry.

* `max_total_runs`: Maksymalna całkowita liczba przebiegów szkoleniowych, które zostaną utworzone. Górna granica — może być mniej przebiegów, na przykład, jeśli przestrzeń hiperparametryczną jest skończona i ma mniej próbek. Musi być liczbą z 1 do 1000.
* `max_duration_minutes`: Maksymalny czas trwania w minutach eksperymentu strojenia hiperparametryczego. Parametr jest opcjonalny, a jeśli jest obecny, wszystkie uruchomienia, które będą uruchomione po tym czasie trwania są automatycznie anulowane.

>[!NOTE] 
>Jeśli `max_total_runs` oba `max_duration_minutes` i są określone, eksperyment dostrajania hiperparametryczne kończy się po osiągnięciu pierwszego z tych dwóch progów.

Ponadto należy określić maksymalną liczbę przebiegów szkoleniowych do uruchomienia jednocześnie podczas wyszukiwania dostrajania hiperparametryczne.

* `max_concurrent_runs`: Maksymalna liczba przebiegów do uruchomienia jednocześnie w danym momencie. Jeśli nie określono, wszystkie `max_total_runs` zostaną uruchomione równolegle. Jeśli jest określony, musi być liczbą z 1 a 100.

>[!NOTE] 
>Liczba równoczesnych przebiegów jest bramowana na zasoby dostępne w określonym miejscu docelowym obliczeń. W związku z tym należy upewnić się, że obiekt docelowy obliczeń ma dostępne zasoby dla żądanej współbieżności.

Przydzielanie zasobów do dostrajania hiperparametry:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ten kod konfiguruje eksperyment dostrajania hiperparametryczne, aby używać maksymalnie 20 całkowitych przebiegów, uruchamiając cztery konfiguracje naraz.

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

[Skonfiguruj eksperyment dostrajania hiperparametrycznego](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) przy użyciu zdefiniowanej przestrzeni wyszukiwania hiperparametrycznego, zasad wcześniejszego zakończenia, metryki podstawowej i alokacji zasobów z powyższych sekcji. Ponadto podaj, `estimator` który zostanie wywołany z próbkowania hiperparametrów. Opisano `estimator` skrypt szkolenia, który można uruchomić, zasoby na zadanie (pojedynczy lub multi-gpu) i cel obliczeniowy do użycia. Ponieważ współbieżność eksperymentu dostrajania hiperparametryczego jest bramą na dostępnych zasobach, upewnij się, że cel obliczeniowy określony w `estimator` ma wystarczające zasoby dla żądanej współbieżności. (Aby uzyskać więcej informacji na temat estymatorów, [zobacz, jak szkolić modele](how-to-train-ml-models.md).)

Skonfiguruj eksperyment strojenia hiperparametryczne:

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

## <a name="submit-experiment"></a>Prześlij eksperyment

Po zdefiniowaniu konfiguracji dostrajania [hiperparametryczne, prześlij eksperyment:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`to nazwa przypisana do eksperymentu dostrajania `workspace` hiperparametrycznego i jest obszarem roboczym, w którym chcesz utworzyć eksperyment (Aby uzyskać więcej informacji na temat eksperymentów, zobacz [Jak działa usługa Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Ciepły początek eksperymentu strojenia hiperparametrycznym (opcjonalnie)

Często znalezienie najlepszych wartości hiperparametru dla modelu może być procesem iteracyjnym, wymagającym wielu przebiegów dostrajania, które uczą się z poprzednich przebiegów dostrajania hiperparametry. Ponowne pożyczenie wiedzy z tych poprzednich przebiegów przyspieszy proces dostrajania hiperparametrycznego, zmniejszając w ten sposób koszt dostrajania modelu i potencjalnie poprawi podstawową metrykę wynikowego modelu. Po nagrzaniu rozpoczynającym eksperyment strojenia hiperparametrycznego z próbkowaniem Bayesa, próby z poprzedniego przebiegu będą używane jako wcześniejsza wiedza, aby inteligentnie wybierać nowe próbki, aby poprawić metrykę podstawową. Ponadto podczas korzystania z próbkowania losowego lub siatki wszelkie decyzje o wcześniejszym zakończeniu będą korzystać z metryk z poprzednich przebiegów w celu określenia słabo działających przebiegów szkoleniowych. 

Usługa Azure Machine Learning umożliwia ogrzanie uruchamiania dostrajania hiperparametrycznego uruchamiane przez wykorzystanie wiedzy z maksymalnie 5 wcześniej ukończonych / anulowanych przebiegów nadrzędnych dostrajania hiperparametrycznego. Można określić listę nadrzędnych uruchomień, które mają zostać ogrzać, począnia od użycia tego fragmentu kodu:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Ponadto mogą wystąpić sytuacje, w których poszczególne przebiegi szkoleniowe eksperymentu dostrajania hiperparametryczne są anulowane z powodu ograniczeń budżetowych lub nie powiodły się z innych powodów. Teraz można wznowić takie indywidualne szkolenia uruchamia z ostatniego punktu kontrolnego (przy założeniu, że skrypt szkolenia obsługuje punkty kontrolne). Wznowienie indywidualnego przebiegu szkoleniowego spowoduje użycie tej samej konfiguracji hiperparametryczne i zainstalowanie folderu wyjść używanego do tego uruchomienia. Skrypt szkolenia należy `resume-from` zaakceptować argument, który zawiera punkt kontrolny lub pliki modelu, z którego można wznowić przebieg szkolenia. Poszczególne treningi można wznowić, korzystając z następującego fragmentu kodu:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Eksperyment strojenia hiperparametrycznym można skonfigurować tak, aby ogrzali początek poprzedniego `resume_from` `resume_child_runs` eksperymentu lub wznowić poszczególne przebiegi treningowe przy użyciu parametrów opcjonalnych i w konfiguracji:

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

## <a name="visualize-experiment"></a>Wizualizuj eksperyment

Zestaw SDK usługi Azure Machine Learning udostępnia [widżet notesu,](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) który wizualizuje postęp przebiegów szkoleniowych. Poniższy fragment kodu wizualizuje wszystkie strojenie hiperparametryczne w jednym miejscu w notesie Jupytera:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ten kod wyświetla tabelę ze szczegółami dotyczącymi przebiegów szkoleniowych dla każdej konfiguracji hiperparametryczne.

![tabela strojenia hiperparametryczne](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Można również wizualizować wydajność każdego z przebiegów w miarę postępów szkolenia. 

![wykres strojenia hiperparametrycznym](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ponadto można wizualnie zidentyfikować korelację między wydajnością a wartościami poszczególnych hiperparametrów przy użyciu wykresu współrzędnych równoległych. 

[![współrzędne równoległe dostrajania hiperparametry](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Można wizualizować wszystkie przebiegi dostrajania hiperparametryczne w portalu internetowym platformy Azure, jak również. Aby uzyskać więcej informacji na temat wyświetlania eksperymentu w portalu internetowym, zobacz [jak śledzić eksperymenty](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Znajdź najlepszy model

Po zakończeniu wszystkich przebiegów dostrajania hiperparametryczne [należy zidentyfikować najlepszą konfigurację](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) i odpowiadające im wartości hiperparametryczne:

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
Zobacz tłeczki train-hyperparameter-*w tym folderze:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki
* [Śledzenie eksperymentu](how-to-track-experiments.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
