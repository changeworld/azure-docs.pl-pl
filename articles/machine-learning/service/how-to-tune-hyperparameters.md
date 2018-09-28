---
title: Dostosowywanie hiperparametrów dla modelu przy użyciu usługi Azure Machine Learning
description: Dowiedz się, jak dostosowywanie hiperparametrów dla modelu uczenie głębokie uczenie / machine przy użyciu usługi Azure Machine Learning. Zobaczysz sposobu definiowania obszaru search parametr, określić podstawową metrykę do optymalizacji i wczesne zakończyć niskiej wydajności konfiguracje.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6370fd9125c5b14df781b27e028c139175b7589
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405805"
---
# <a name="tune-hyperparameters-for-your-model"></a>Dostosowywanie hiperparametrów dla modelu

W tym artykule dowiesz się skutecznie dostroić hiperparametrów dla modelu. Zobaczysz sposobu definiowania obszaru search parametr, określić podstawową metrykę do optymalizacji i wczesne zakończyć niskiej wydajności konfiguracje. Możesz również wizualizację różnych przebiegów szkoleniowych i wybrać najlepsze wykonywanie konfiguracji dla modelu.

## <a name="what-are-hyperparameters"></a>Co to są hiperparametrów?
Hiperparametrów są regulowane parametry wybrana przed uczenia modelu sam proces szkolenia. Na przykład przed uczenie sieci neuronowej, musisz zdecydować, Liczba ukrytych warstw w sieci i liczby węzłów w każdej warstwie. Te wartości zwykle pozostają stałe w procesie szkolenia.

W scenariuszach uczenie głębokie uczenie / machine modelu wydajność zależy od intensywnie wybrano wartości hiperparametrycznego. Celem eksploracji hiperparametrycznego jest wyszukiwanie w różnych konfiguracjach hiperparametrycznego można odnaleźć konfiguracji, które powoduje żądaną wydajność. Zazwyczaj procesu eksploracji hiperparametrycznego jest żmudne ręczne, biorąc pod uwagę, że miejsce wyszukiwania jest ogromna i oceny każda konfiguracja może być kosztowne.

Usługa Azure Machine Learning pozwala na zautomatyzowanie tego eksploracji hiperparametrycznego w sposób efektywny, zaoszczędzić znaczną ilość czasu i zasobów. Można określić zakres wartości hiperparametrycznego do eksplorowania i maksymalną liczbę szkolenia działa dla tej prezentacji. System następnie uruchamia wiele przebiegów szkoleniowych jednoczesnych przy użyciu różnych parametrów konfiguracji i automatycznie wyszukuje konfigurację, która powoduje najlepszą wydajność, gdyż jest mierzone przez metrykę, wybierany przez użytkownika. Nieprawidłowo działających przebiegów szkoleniowych są automatycznie wczesne zakończone, zmniejszając nadmierne użycie nadmierny zasobów obliczeniowych. Te zasoby zamiast tego są używane do eksplorowania inne konfiguracje hiperparametrycznego.

W celu dostrojenia hiperparametrów dla modelu przy użyciu usługi Azure Machine Learning, należy wykonać następujące polecenie:
* Definiowanie przestrzeni wyszukiwania hiperparametrycznego
* Określ podstawowe metryki do optymalizacji
* Określ zasadę wcześniejsze zakończenie
* Przydzielanie zasobów do strojenia hiperparametrycznego
* Uruchamianie eksperymentu przy użyciu powyższej konfiguracji

## <a name="define-the-hyperparameter-search-space"></a>Definiowanie przestrzeni wyszukiwania hiperparametrycznego
Usługa Azure Machine Learning jej automatyczne dostosowywanie hiperparametrów, eksplorując zakres wartości zdefiniowane dla każdego hiperparametrycznego.

### <a name="types-of-hyperparameters"></a>Typy hiperparametrów
Każdy hiperparametrycznego może być discrete lub ciągłe.

#### <a name="discrete-hyperparameters"></a>Dyskretne hiperparametrów 
Dyskretne hiperparametrów może być określony jako `choice` wśród wartości dyskretnych. `choice` można wykonać jedną lub więcej CSV, `range` obiektu lub dowolnych dowolnego `list` obiektu. Na przykład:  

```Python
    {    
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

W tym przypadku batch_size może przyjmować jedną z wartości [16, 32, 64, 128] i number_of_hidden_layers może przyjmować jedną z wartości [1, 2, 3, 4].

Zaawansowane hiperparametrów dyskretnych można również określić, korzystania z punktów dystrybucji. Obsługiwane są poniższe dystrybucje-
* `quniform(low, high, q)` — Zwraca wartość, takie jak działanie (uniform (niska, wysoka) / q) * pytania i odpowiedzi
* `qloguniform(low, high, q)` — Zwraca wartość, takie jak działanie (exp (uniform (niska, wysoka)) / q) * pytania i odpowiedzi
* `qnormal(mu, sigma, q)` — Zwraca wartość, takie jak działanie (normalny (jednostkę obsługi komunikatów, sigma) / q) * pytania i odpowiedzi
* `qlognormal(mu, sigma, q)` — Zwraca wartość, takie jak działanie (exp (normalny (jednostkę obsługi komunikatów, sigma)) / q) * pytania i odpowiedzi

#### <a name="continuous-hyperparameters"></a>Ciągłe hiperparametrów 
Ciągłe hiperparametrów można określić jako dystrybucji za pośrednictwem ciągły zakres wartości. Obsługiwane dystrybucje obejmują-
* `uniform(low, high)` — Zwraca wartości równomiernie rozłożone między niski i wysoki
* `loguniform(low, high)` — Zwraca wartość rysowane zgodnie z exp (uniform (niska, wysoka)), aby równomiernie logarytm liczby wartość zwracana
* `normal(mu, sigma)` -Zwraca rzeczywistą wartość, która zwykle jest dystrybuowane z średnią mu i odchylenie standardowe
* `lognormal(mu, sigma)` — Zwraca wartość rysowane zgodnie z exp (normalny (jednostkę obsługi komunikatów, sigma)), aby logarytm liczby wartość zwracana jest zazwyczaj dystrybuowane

Oto przykład definicji miejsca na parametr-

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

W tym przykładzie definiuje miejsce wyszukiwania o dwa parametry — learning_rate i keep_probability. learning_rate będzie miał do rozkładu normalnego z średniej wartości 10 i odchylenie standardowe 3. keep_probability będzie miał jednolity rozkład o określonej wartości minimalnej 0,05 i maksymalna wartość 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Próbkowanie miejsca hiperparametrycznego
Użytkownik określa również metody pobierania próbek parametr do użycia za pośrednictwem definicji miejsca na określonym hiperparametrycznego. Usługa Azure Machine Learning obsługuje Random próbkowania, próbkowania siatki i Bayesowskie próbkowania.

#### <a name="random-sampling"></a>Losowe próbkowania
Podczas próbkowania losowych wartości hiperparametrycznego są wybierane losowo z obszaru search zdefiniowane. Losowej próbki pozwala przestrzeni wyszukiwania uwzględnić hiperparametrów zarówno dyskretnych, jak i ciągłe. Na przykład:

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
Próbkowanie siatki wykonuje wyszukiwanie prosta siatka wszystkie możliwe wartości w obszarze wyszukiwania zdefiniowanych. Można używać tylko z hiperparametrów określony za pomocą `choice`. Na przykład następujące miejsca ma łącznie sześć przykłady-

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Próbkowanie Bayesowskie
Próbkowanie Bayesowskie opiera się na algorytm optymalizacji Bayesowskie i sprawia, że inteligentne opcje na wartościach hiperparametrycznego obok próbkowania. Wybiera w tym przykładzie, w oparciu o jak poprzednie przykłady wykonywane, takie, że nowe próbki zwiększa zgłoszonych podstawową metrykę.

Korzystając z Bayesowskie próbkowania, liczby równoczesnych uruchomień ma wpływ na efektywność procesu dostosowywania. Zazwyczaj mniejszą liczbę równoczesnych uruchomień może prowadzić do lepszego zbieżność próbkowania. Jest to spowodowane mniejszych stopień równoległości zwiększa się liczba uruchomień, które korzystają z wcześniej zakończone przebiegi.

Próbkowanie Bayesowskie obsługuje tylko `choice` i `uniform` dystrybucji za pośrednictwem obszaru search. Na przykład: 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Próbkowanie Bayesowskie aktualnie nie obsługuje wszystkie wcześniejsze zasad zakończenia (zobacz [określić zasady wcześniejsze zakończenie](#specify-an-early-termination-policy)). Jeśli używasz Bayesowskie parametru próbkowania, należy ustawić zasady `None`. Nie należy określać zasady rozwiązania z włączonym próbkowaniem Bayesowskie mają ten sam efekt.
>
> ```Python
> early_termination_policy = None
> ```

## <a name="specify-a-primary-metric-to-optimize"></a>Określ podstawowe metryki do optymalizacji
Podczas dostosowywania hiperparametrów, należy określić podstawowy metrykę, która ma hiperparametrycznego dostrajania eksperymentu w celu optymalizacji. Każde uruchomienie szkolenia jest oceniana dla tej metryki podstawowej i niskiej wydajności uruchomienia (gdzie podstawowe metryki nie spełnia kryteriów ustawionego na podstawie zasad wcześniejsze zakończenie) zostanie zakończona. Oprócz określenia nazwy metryki podstawowej, należy również określić celem Optymalizacja - czy zmaksymalizować lub zminimalizować podstawową metrykę.
* `primary_metric_name`: Nazwa podstawową metrykę do optymalizacji. Podstawowe metryki musi dokładnie pasować do nazwy metryki zarejestrowane przez skrypt szkolenia. Zobacz [dziennika metryk do strojenia hiperparametrycznego](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Może być albo `PrimaryMetricGoal.MAXIMIZE` lub `PrimaryMetricGoal.MINIMIZE` i określa, czy podstawowe metryki będą zmaksymalizowane, czy podczas oceny będzie uruchamiany. 

Na przykład —
```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```
Zoptymalizuj pod przebiegów, aby zmaksymalizować "dokładność".

### <a name="log-metrics-for-hyperparameter-tuning"></a>Rejestruj metryki do strojenia hiperparametrycznego
Aby korzystać z usługi Azure Machine Learning do strojenia hiperparametrycznego skrypt szkolenia dotyczące modelu należy zgłaszać istotne metryki, gdy wykonuje modelu. Użytkownik określa podstawową metrykę chcą, aby usługa do użycia do oceny wydajności wykonywania, a skrypt szkoleniowy będą musieli rejestrować tej metryki. Zobacz [określić podstawową metrykę, aby zoptymalizować](#specify-a-primary-metric-to-optimize).

Możesz zaktualizować skrypt szkolenia się ta metryka przy użyciu następującego fragmentu kodu przykładowej -

```Python
from azureml.core.run import Run
run_logger = Run.get_submitted_run()
run_logger.log("accuracy", float(val_accuracy))
```

W tym przykładzie skrypt szkoleniowy oblicza `val_accuracy` i rejestruje to "dokładność", który jest używany jako podstawowe metryki. Jest developer modelu do określania, jak często zgłosić tej metryki.

## <a name="specify-an-early-termination-policy"></a>Określ zasadę wcześniejsze zakończenie
Po za pomocą usługi Azure Machine Learning na dostosowywanie hiperparametrów, niskiej wydajności uruchamia się automatycznie wcześniej zakończone. Zmniejsza nadmierne użycie nadmierny zasobów i zamiast tego używa tych zasobów do eksplorowania innych parametrów konfiguracji.

Korzystając z wczesnym zasad zakończenia, użytkownik może konfigurować następujące parametry, które kontrolują stosowania zasad —
* `evaluation_interval`: częstotliwość stosowania zasad. Każdym logowaniu skrypt szkoleniowy podstawowe metryki jest liczona jako jeden interwał. Ten sposób `evaluation_interval` 1 zostaną zastosowane zasady za każdym razem, gdy skrypt szkoleniowy Raporty podstawowe metryki. `evaluation_interval` 2 zostaną zastosowane zasady każdym innym czasie skrypt szkoleniowy Raporty podstawowe metryki. Jest to parametr opcjonalny, a jeśli nie zostanie określony, `evaluation_interval` jest ustawiona na 1, domyślnie.
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów. To jest opcjonalnym parametrem, który zezwala na wszystkie konfiguracje do uruchamiania dla początkowej minimalna liczba przedziałów, unikając przedwczesne zakończenie wysyłanie przebiegów szkoleniowych. Jeśli zostanie określony, zostaną zastosowane zasady co wielu evaluation_interval, która jest większa lub równa delay_evaluation.

Usługa Azure Machine Learning obsługuje następujące wczesne zasady zakończenia —

### <a name="bandit-policy"></a>Zasady bandit
Bandit zasad to zasady zakończenia na podstawie slack współczynnik/zapas czasu interwału kwota i oceny. Ta zasada kończy wcześnie żadnych przebiegów, gdzie podstawowe metryki nie mieści się w określony współczynnik slack / run slack kwota względem najlepiej szkolenia. Zajmuje się następujących parametrów konfiguracji:
* `slack_factor` lub `slack_amount`: slack dozwolona względem najlepiej szkolenia uruchomienia. `slack_factor` Określa dopuszczalne slack jako stosunek. `slack_amount` Określa dopuszczalne slack jako kwotę bezwzględną, zamiast stosunku.

    Na przykład należy wziąć pod uwagę Bandit zasady stosowane w odstępach 10. Załóżmy, że najlepsze wykonywanie uruchomienia w odstępach 10 zgłoszone podstawowe metryki 0,8 za cel, aby zmaksymalizować podstawowe metryki. Jeśli nie określono zasad za pomocą `slack_factor` 0,2, wszelkie szkolenia jest uruchomiony, w których najważniejsze metryki w odstępach 10 jest mniejsza niż 0.66 (0,8 / (1 +`slack_factor`)) zostanie zakończona. Jeśli zamiast tego zasad został określony za pomocą `slack_amount` 0,2, wszelkie szkolenia jest uruchomiony, w których najważniejsze metryki w odstępach 10 jest mniejsza niż Update 0.6 (0,8 - `slack_amount`) zostanie zakończona.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).

Rozważmy następujący przykład-

```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdym interwale, gdy metryki są zgłaszane, zaczynając od interwał oceny 5. Uruchom którego najlepszą metryka jest mniejsza niż (1/(1+0.1) dowolnej 91% najlepszej wydajności uruchamiania zostanie zakończone.

### <a name="median-stopping-policy"></a>Zatrzymywanie zasad medianę
Mediana zatrzymywanie zasad jest wcześniejsze zasady rozwiązania, które są oparte na uruchamianie średnie podstawowe metryki zgłoszone przez przebiegi. Ta zasada oblicza średnie działającej we wszystkich przebiegów szkoleniowych i kończy się przebiegów, w których wydajność jest niższa niż medianę średnie uruchomione. Ta zasada przyjmuje następujące parametry konfiguracji —
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).

Rozważmy następujący przykład-

```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdej odstępach czasu, zaczynając od interwał oceny 5. Uruchom w odstępach 5 zostaną zakończone, jeśli jego najważniejsze metryki podstawowej jest niższa niż medianę średnie uruchomionych za pośrednictwem interwałów 1:5 we wszystkich przebiegów szkoleniowych.

### <a name="truncation-selection-policy"></a>Obcięcie wybór zasad
Anuluje wybór zasad obcięcie występuje dany procent wykonania najniższy jest uruchamiana po każdym odstępie oceny. Przebiegi są porównywane, na podstawie ich wydajności na podstawowe metryki i najniższego X % są zakończone. Zajmuje się następujących parametrów konfiguracji:
* `truncation_percentage`: procent wykonania najniższy uruchamia się zakończyć w każdym interwale oceny. Powinna to być wartość całkowita z przedziału od 1 do 99.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszej oceny zasad określoną liczbę interwałów (parametr opcjonalny).

Rozważmy następujący przykład-

```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie wczesne zasady zakończenia są stosowane w każdej odstępach czasu, zaczynając od interwał oceny 5. Uruchom w odstępach 5, zostaną zakończone, jeśli jego wydajność w odstępach 5 znajduje się w najniższej 20% wyników wszystkich przebiegów w odstępach 5.

### <a name="no-termination-policy"></a>Brak zasad zakończenia
Jeśli chcesz, aby wszystkie przebiegów szkoleniowych zostało ukończone, należy użyć NoTerminationPolicy. Będzie to miało wpływu nie można zastosować zasad wcześniejsze zakończenie. Na przykład: 

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>Zasady domyślne
Jeśli zostanie określona żadna zasada, hiperparametrycznego dostrajania usługi użyje zasady zatrzymywanie mediana z `evaluation_interval` 1 i `delay_evaluation` 5 domyślnie. Te są konserwatywnego ustawienia, które może zapewnić około 25 – 35% oszczędności bez utraty podstawową metrykę (oparte na naszych danych oceny).

## <a name="allocate-resources-for-hyperparameter-tuning"></a>Przydzielanie zasobów do strojenia hiperparametrycznego
Możesz kontrolować swój budżet zasobów dla Twojego hiperparametrycznego dostrajania eksperymentu, określając maksymalna całkowita liczba przebiegów szkoleniowych i opcjonalnie, maksymalny czas trwania dla Twojego hiperparametrycznego dostrajania eksperymentu (w minutach). 
* `max_total_runs`: Maksymalna łączna liczba przebiegów szkoleniowych, które zostaną utworzone. Jest to górna granica — firma Microsoft może mieć mniejszej liczby przebiegów, na przykład jeśli miejsce hiperparametrycznego jest jednak ograniczona i ma mniejszą liczbę próbek. Musi być liczbą z przedziału od 1 do 1000.
* `max_duration_minutes`: Maksymalny czas trwania hiperparametrycznego dostrajania eksperymentu w ciągu kilku minut. Jest to parametr opcjonalny, a jeśli jest obecny, wszystkie przebiegi, które mogą być uruchamiane po tym czasie zostaną automatycznie anulowane.

>[!NOTE] 
>Jeśli oba `max_total_runs` i `max_duration_minutes` podano hiperparametrycznego dostrajania eksperymentu zostanie zakończony, gdy zostanie osiągnięty pierwszy z tych dwóch progów.

Ponadto można określić maksymalną liczbę szkolenia uruchomienia uruchamianych jednocześnie podczas Twojej hiperparametrycznego dostrajania wyszukiwania.
* `max_concurrent_runs`: To jest maksymalna liczba uruchomień uruchomić jednocześnie w danym momencie. Jeśli nie, wszystkie `max_total_runs` będą uruchamiane równolegle. Jeśli zostanie określony, musi być liczbą z przedziału od 1 do 100.

>[!NOTE] 
>Liczba równoczesnych uruchomień jest uzyskiwany na zasobach, które są dostępne w określonej obliczeniowego elementu docelowego. Z tego powodu należy upewnić się, że obliczeniowego elementu docelowego dostępnych zasobów dla żądanego współbieżności.

Możesz przydzielić zasoby do strojenia hiperparametrycznego, jak pokazano w poniższym przykładzie —
```Python
max_total_runs=20,
max_concurrent_runs=4
```
To spowoduje skonfigurowanie hiperparametrycznego dostrajania eksperymentu można użyć maksymalnie 20, łączna liczba uruchomień uruchamiania 4 konfiguracji w danym momencie.

## <a name="configure-your-hyperparameter-tuning-experiment"></a>Konfigurowanie usługi hiperparametrycznego dostrajania eksperymentu
Można skonfigurować usługi hiperparametrycznego dostrajania eksperymentu przy użyciu przestrzeni wyszukiwania zdefiniowanych hyperpameter, wcześniejsze zakończenie zasad, podstawowe metryki i alokacji zasobów z powyższej sekcji. Ponadto konieczne będzie zapewnienie `estimator` , zostanie wywołana z próbkowanych hiperparametrów. `estimator` Opisuje skrypt szkoleniowy uruchomieniu, zasoby na zadanie (jednym lub wieloma procesorami gpu) i obliczeniowego elementu docelowego do użycia. Od współbieżności dla usługi hiperparametrycznego dostrajania eksperymentu są bramkowane na dostępnych zasobów, należy upewnić się, że obliczeniowego elementu docelowego określona w `estimator` ma wystarczające zasoby, odpowiednią współbieżność. (Zobacz [sposób trenowania modeli](how-to-train-ml-models.md) więcej informacji na temat aplikacjom).

Oto przykład sposobu konfigurowania eksperymentu strojenia hiperparametrycznego-

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-your-hyperparameter-tuning-experiment"></a>Prześlij swoje hiperparametrycznego dostrajania eksperymentu
Po zdefiniowaniu swoje hiperparametrycznego dostrajania konfiguracji, możesz przesłać eksperymentu za pomocą tej konfiguracji —

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

gdzie `experiment_name` to nazwa ma zostać przypisany do Twojej hiperparametrycznego dostrajania eksperymentu, i `workspace` jest obszarem roboczym, w którym chcesz utworzyć eksperyment (zobacz [łącze](/concept-azure-machine-learning-architecture.md) Aby uzyskać więcej informacji na temat doświadczeń).

## <a name="visualize-your-hyperparameter-tuning-experiment"></a>Wizualizuj swoje hiperparametrycznego dostrajania eksperymentu
Zestaw Azure SDK Learning maszyny zawiera element widget Notes, który może służyć do wizualizacji postępu przebiegów szkoleniowych. Poniższy fragment kodu można wizualizować wszystkie hiperparametrycznego dostrajania przebiegów w jednym miejscu —

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Zostanie wyświetlona tabela zawierająca szczegóły dotyczące przebiegów szkoleniowych dla każdej konfiguracji hiperparametrycznego. Na przykład:

![tabeli strojenia hiperparametrycznego](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Można również wizualizować wykonanie każdego uruchomienia w miarę postępów szkoleń. Na przykład:

![Wykres strojenia hiperparametrycznego](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Na koniec można zidentyfikować wizualnie korelacji między wydajnością i wartości poszczególnych hiperparametrów przy użyciu równoległych współrzędnych wykresu. Na przykład: 

![hiperparametrycznego dostrajania współrzędne równoległe](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Alternatywnie można wizualizować swoje hiperparametrycznego dostrajania jest uruchamiany w witrynie portal sieci web platformy Azure. Zobacz [łącze](/how-to-track-experiments.md/#view-the-experiment-in-the-web-portal) Aby uzyskać więcej informacji na temat sposobu wyświetlania eksperymentu w portalu sieci web. Na przykład-

![portal strojenia hiperparametrycznego](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-configuration-that-resulted-in-the-best-performance"></a>Znajdź konfiguracji, które spowodowały najlepszej wydajności
Gdy wszystkie hiperparametrycznego dostrajania przebiegi zakończone, można zidentyfikować najlepsze wykonywanie konfiguracji i odpowiadające im wartości hiperparametrycznego za pomocą następujących fragment kodu -

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
Zapoznaj się 
* `training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb` samouczek dotyczący Dostosowywanie hiperparametrów modelu Tensorflow. 

Pobierz ten notes:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Śledź eksperymentu](how-to-track-experiments.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
