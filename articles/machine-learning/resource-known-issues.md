---
title: Znane problemy & rozwiązywanie problemów
titleSuffix: Azure Machine Learning
description: Pobierz listę znanych problemów, obejścia i rozwiązywania problemów dla usługi Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619374"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Znane problemy i rozwiązywanie problemów z usługą Azure Machine Learning

Ten artykuł ułatwia znajdowanie i poprawianie błędów lub błędów, które mogą wystąpić podczas korzystania z usługi Azure Machine Learning.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Czasami może to być pomocne, jeśli możesz podać informacje diagnostyczne, gdy prosisz o pomoc. Aby wyświetlić niektóre dzienniki: 
1. Odwiedź [studio usługi Azure Machine Learning](https://ml.azure.com). 
1. Po lewej stronie wybierz opcję **Eksperymentuj** 
1. Wybierz eksperyment.
1. Wybierz bieg.
1. Na górze wybierz **pozycję Wyjścia + dzienniki**.

> [!NOTE]
> Usługa Azure Machine Learning rejestruje informacje z różnych źródeł podczas szkolenia, takich jak AutoML lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Wiele z tych dzienników nie są udokumentowane. Jeśli wystąpią problemy i skontaktuj się z pomocą techniczną firmy Microsoft, mogą one być w stanie korzystać z tych dzienników podczas rozwiązywania problemów.


## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [przydziałach zasobów,](how-to-manage-quotas.md) które mogą wystąpić podczas pracy z usługą Azure Machine Learning.

## <a name="installation-and-import"></a>Instalacja i import

* **Komunikat o błędzie: nie można odinstalować 'PyYAML'**

    Zestaw SDK usługi Azure Machine Learning dla `distutils` języka Python: PyYAML jest zainstalowanym projektem. W związku z tym nie możemy dokładnie określić, które pliki należą do niego, jeśli istnieje częściowe odinstalowanie. Aby kontynuować instalowanie zestawu SDK podczas ignorowania tego błędu, należy użyć:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Awaria databricks podczas instalowania pakietów**

    Instalacja sdk usługi Azure Machine Learning kończy się niepowodzeniem na platformie Azure Databricks po zainstalowaniu większej liczby pakietów. Niektóre pakiety, `psutil`takie jak , mogą powodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety przez zamrożenie wersji biblioteki. Ten problem jest związany z Databricks, a nie do usługi Azure Machine Learning SDK. Ten problem może wystąpić również w przypadku innych bibliotek. Przykład:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Alternatywnie można użyć skryptów init, jeśli nadal naprzetykasz problemy z instalacją bibliotek Pythona. Takie podejście nie jest oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [skrypty init o zakresie klastra](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks import error: nie można zaimportować nazwy "Timedelta" z "pandas._libs.tslibs":** Jeśli widzisz ten błąd podczas korzystania z automatycznego uczenia maszynowego, uruchom dwa następujące wiersze w notesie:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks import error: No module named 'pandas.core.indexes:** Jeśli widzisz ten błąd podczas korzystania z automatycznego uczenia maszynowego:

    1. Uruchom to polecenie, aby zainstalować dwa pakiety w klastrze usługi Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Odłącz, a następnie podłącz klaster do notesu.
    
    Jeśli te kroki nie rozwiążą problemu, spróbuj ponownie uruchomić klaster.

* **Databricks FailToSendFeather**: Jeśli `FailToSendFeather` podczas odczytywania danych w klastrze usługi Azure Databricks zostanie wyświetlony błąd, zapoznaj się z następującymi rozwiązaniami:
    
    * Uaktualnienie `azureml-sdk[automl]` pakietu do najnowszej wersji.
    * Dodaj `azureml-dataprep` wersję 1.1.8 lub powyżej.
    * Dodaj `pyarrow` wersję 0.11 lub powyżej.

## <a name="create-and-manage-workspaces"></a>Tworzenie obszarów roboczych i zarządzanie nimi

> [!WARNING]
> Przenoszenie obszaru roboczego usługi Azure Machine Learning do innej subskrypcji lub przenoszenie subskrypcji właścicielskiej do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

* **Azure portal:** Jeśli przejdziesz bezpośrednio do widoku obszaru roboczego z łącza udziału z SDK lub portalu, nie będzie można wyświetlić normalnej strony **przegląd** z informacjami o subskrypcji w rozszerzeniu. Nie będzie również można przełączyć się do innego obszaru roboczego. Jeśli chcesz wyświetlić inny obszar roboczy, przejdź bezpośrednio do [studia usługi Azure Machine Learning](https://ml.azure.com) i wyszukaj nazwę obszaru roboczego.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

* **Problemy z tworzeniem AmlCompute:** Istnieje rzadka szansa, że niektórzy użytkownicy, którzy utworzyli swój obszar roboczy usługi Azure Machine Learning z witryny Azure portal przed wydaniem ga może nie być w stanie utworzyć AmlCompute w tym obszarze roboczym. Można utworzyć żądanie pomocy technicznej dla usługi lub utworzyć nowy obszar roboczy za pośrednictwem portalu lub sdk, aby natychmiast odblokować siebie.

## <a name="work-with-data"></a>Praca z danymi

### <a name="overloaded-azurefile-storage"></a>Przeciążony magazyn plików AzureFile

Jeśli zostanie wyświetlony `Unable to upload project files to working directory in AzureFile because the storage is overloaded`błąd, zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zaleca się użycie obiektów blob, aby udział plików był wolny do przesyłania przebiegów. Można również podzielić obciążenie między dwa różne obszary robocze.

### <a name="passing-data-as-input"></a>Przekazywanie danych jako danych wejściowych

*  **TypeError: FileNotFound: Brak takiego pliku lub katalogu**: Ten błąd występuje, jeśli podasz ścieżkę pliku nie znajduje się tam, gdzie znajduje się plik. Należy upewnić się, że sposób odwoływania się do pliku jest zgodny z miejscem, w którym zamontowano zestaw danych na docelowej ilości obliczeń. Aby zapewnić stan deterministyczny, zaleca się użycie ścieżki abstrakcyjnej podczas montażu zestawu danych do obiektu docelowego obliczeń. Na przykład w poniższym kodzie montujemy zestaw danych w katalogu głównym `/tmp`systemu plików obiektu docelowego obliczeń, . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Jeśli nie uwzględnisz wiodącego ukośnika do przodu , '/', musisz prefiksować katalog roboczy np. `/mnt/batch/.../tmp/dataset`

### <a name="data-labeling-projects"></a>Projekty etykietowania danych

|Problem  |Rozwiązanie  |
|---------|---------|
|Można używać tylko zestawów danych utworzonych w magazynach danych obiektów blob     |  jest to znane ograniczenie bieżącej wersji.       |
|Po utworzeniu projekt pokazuje "Inicjowanie" przez długi czas     | Ręcznie odśwież stronę. Inicjowanie należy postępować z około 20 punktów danych na sekundę. Brak autorefresh jest znany problem.         |
|Podczas przeglądania obrazów nowo oznaczone obrazy nie są wyświetlane     |   Aby załadować wszystkie obrazy oznaczone etykietami, wybierz przycisk **Pierwszy.** **Przycisk Pierwszy** spowoduje powrót na przód listy, ale ładuje wszystkie oznaczone dane.      |
|Naciśnięcie klawisza Esc podczas etykietowania w celu wykrycia obiektu powoduje utworzenie etykiety o zerowym rozmiarze w lewym górnym rogu. Przesyłanie etykiet w tym stanie kończy się niepowodzeniem.     |   Usuń etykietę, klikając znak krzyżyka obok niej.  |

## <a name="azure-machine-learning-designer"></a>Projektant usługi Azure Machine Learning

Znane problemy:

* **Długi czas przygotowania obliczeń:** może to być kilka minut lub nawet dłużej, gdy po raz pierwszy połączyć się lub utworzyć cel obliczeniowy. 

## <a name="train-models"></a>Szkolenie modeli

* **ModuleErrors (Brak nazwy modułu)**: Jeśli używasz do ModuleErrors podczas przesyłania eksperymentów w usłudze Azure ML, oznacza to, że skrypt szkoleniowy oczekuje, że pakiet zostanie zainstalowany, ale nie zostanie dodany. Po podaniu nazwy pakietu usługa Azure ML zainstaluje pakiet w środowisku używanym do przebiegu szkolenia. 

    Jeśli używasz [Estymatorów](concept-azure-machine-learning-architecture.md#estimators) do przesyłania eksperymentów, można `pip_packages` określić nazwę pakietu za pośrednictwem lub `conda_packages` parametr w estymatorze na podstawie źródła, z którego chcesz zainstalować pakiet. Można również określić plik yml ze `conda_dependencies_file`wszystkimi zależnościami przy użyciu lub wyświetlić wszystkie wymagania pip w pliku txt przy użyciu `pip_requirements_file` parametru. Jeśli masz własny obiekt środowiska usługi Azure ML, który chcesz zastąpić domyślny obraz używany przez estymatora, można określić to środowisko za pomocą `environment` parametru konstruktora estymatora.

    Usługa Azure ML zapewnia również estymatory specyficzne dla struktury dla Tensorflow, PyTorch, Chainer i SKLearn. Za pomocą tych estymatorów upewnij się, że podstawowe zależności struktury są instalowane w Twoim imieniu w środowisku używanym do szkolenia. Masz możliwość określenia dodatkowych zależności, jak opisano powyżej. 
 
    Obrazy platformy Azure ML obsługiwane docker i ich zawartość można zobaczyć w [kontenerach azureml.](https://github.com/Azure/AzureML-Containers)
    Zależności specyficzne dla struktury są wymienione w odpowiedniej dokumentacji ramowej - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Jeśli uważasz, że określony pakiet jest wystarczająco wspólny, aby można było dodać do obrazów i środowisk obsługiwanych przez usługę Azure ML, należy zgłosić problem z usługą GitHub w [kontenerach azureml.](https://github.com/Azure/AzureML-Containers) 
 
* **NameError (nazwa nie zdefiniowana), AttributeError (Obiekt nie ma atrybutu)**: Ten wyjątek powinien pochodzić ze skryptów szkoleniowych. Możesz spojrzeć na pliki dziennika z witryny Azure portal, aby uzyskać więcej informacji na temat określonej nazwy nie zdefiniowane lub błąd atrybutu. Z SDK, można `run.get_details()` użyć, aby spojrzeć na komunikat o błędzie. Spowoduje to również wyświetlenie listy wszystkich plików dziennika wygenerowanych dla twojego uruchomienia. Przed ponownym rozpoczęciem biegu należy zapoznać się ze skryptem treningowym i naprawić ten błąd. 

* **Horovod został zamknięty**: W większości przypadków, jeśli napotkasz "AbortedError: Horovod został zamknięty" wyjątek ten oznacza, że był podstawowy wyjątek w jednym z procesów, które spowodowały Horovod do zamknięcia. Każda ranga w zadaniu MPI pobiera własny dedykowany plik dziennika w usłudze Azure ML. Te dzienniki `70_driver_logs`są nazwane . W przypadku szkolenia rozproszonego nazwy dziennika są sufiksami, `_rank` aby ułatwić rozróżnianie dzienników. Aby znaleźć dokładny błąd, który spowodował zamknięcie Horovod, przejdź przez `Traceback` wszystkie pliki dziennika i poszukaj na końcu driver_log plików. Jeden z tych plików daje rzeczywisty wyjątek. 

* **Uruchamianie lub usuwanie eksperymentu:** eksperymenty można archiwizować przy użyciu metody [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) lub z widoku karty Eksperyment w kliencie studia usługi Azure Machine Learning za pomocą przycisku "Eksperyment archiwum". Ta akcja ukrywa eksperyment z kwerend listy i widoków, ale nie usuwa go.

    Trwałe usuwanie poszczególnych eksperymentów lub przebiegów nie jest obecnie obsługiwane. Aby uzyskać więcej informacji na temat usuwania zasobów obszaru roboczego, zobacz [Eksportowanie lub usuwanie danych obszaru roboczego usługi uczenia maszynowego](how-to-export-delete-data.md).

* **Dokument metryki jest zbyt duży:** usługa Azure Machine Learning ma wewnętrzne limity rozmiaru obiektów metryk, które mogą być rejestrowane jednocześnie z przebiegu szkolenia. Jeśli podczas rejestrowania metryki o wartości listy wystąpi błąd "Dokument metrykowy jest zbyt duży", spróbuj podzielić listę na mniejsze fragmenty, na przykład:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Wewnętrznie usługi Azure ML łączy bloki o tej samej nazwie metryki do ciągłej listy.

## <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

* **Tensor Flow:** Automatyczne uczenie maszynowe nie obsługuje obecnie przepływu tensorów w wersji 1.13. Zainstalowanie tej wersji spowoduje, że zależności pakietu przestaną działać. Pracujemy nad rozwiązaniem tego problemu w przyszłej wersji.

* **Wykresy eksperymentu:** Binarne wykresy klasyfikacji (precyzyjne przywoływanie, ROC, krzywa wzmocnienia itp.) pokazane w zautomatyzowanych iteracjach eksperymentu ML nie są poprawnie renderowane w interfejsie użytkownika od 4/12. Wykresy wykresu są obecnie wyświetlane wyniki odwrotne, gdzie lepsze wyniki modeli są wyświetlane z niższych wyników. Rezolucja jest przedmiotem dochodzenia.

* **Databricks anulować automatyczne uruchomienie uczenia maszynowego:** Podczas korzystania z funkcji automatycznego uczenia maszynowego na platformie Azure Databricks, aby anulować uruchomienie i rozpocząć nowy eksperyment uruchomić, uruchom ponownie klaster usługi Azure Databricks.

* **Databricks >10 iteracji dla automatycznego uczenia maszynowego:** W zautomatyzowanych ustawieniach uczenia maszynowego, jeśli masz więcej niż 10 iteracji, ustaw `show_output` podczas `False` przesyłania przebiegu.

* **Widżet Databricks dla sdk usługi Azure Machine Learning i automatycznego uczenia maszynowego:** widżet Azure Machine Learning SDK nie jest obsługiwany w notesie Databricks, ponieważ notesy nie mogą analizować widżetów HTML. Widżet można wyświetlić w portalu przy użyciu tego kodu języka Python w komórce notesu usługi Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Wdrażanie i obsługa modeli

Wykonaj następujące działania w przypadku następujących błędów:

|Błąd  | Rozwiązanie  |
|---------|---------|
|Błąd tworzenia obrazów podczas wdrażania usługi sieci web     |  Dodaj "pynacl==1.2.1" jako zależność pip do pliku Conda dla konfiguracji obrazu       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Zmień jednostkę SKU dla maszyn wirtualnych używane w we wdrożeniu na taki, który ma więcej pamięci. |
|Awaria fpga     |  Nie będzie można wdrażać modeli w układach FPGA, dopóki nie zażądasz i nie zostaniesz zatwierdzony do przydziału FPGA. Aby uzyskać dostęp, wypełnij formularz żądania przydziału:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizowanie składników usługi Azure Machine Learning w klastrze AKS

Aktualizacje składników usługi Azure Machine Learning zainstalowane w klastrze usługi Azure Kubernetes muszą być stosowane ręcznie. 

Te aktualizacje można zastosować, odłączając klaster od obszaru roboczego usługi Azure Machine Learning, a następnie ponownie dołączając klaster do obszaru roboczego. Jeśli protokół TLS jest włączony w klastrze, podczas ponownego dołączania klastra należy podać certyfikat TLS/SSL i klucz prywatny. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Jeśli nie masz już certyfikatu TLS/SSL i klucza prywatnego lub używasz certyfikatu wygenerowanego przez usługę Azure Machine Learning, możesz `kubectl` pobrać pliki `azuremlfessl`przed odłączeniem klastra, łącząc się z klastrem przy użyciu klucza tajnego i pobierając go.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w formacie zakodowanym base-64. Musisz base-64 dekodować `cert.pem` `key.pem` i składniki tajemnic przed dostarczeniem ich do `attach_config.enable_ssl`. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Usługi sieci Web w błędach usługi Azure Kubernetes

Wiele błędów usługi sieci web w usłudze Azure Kubernetes można `kubectl`debugować, łącząc się z klastrem przy użyciu . Klaster usług `kubeconfig.json` Kubernetes platformy Azure można uzyskać, uruchamiając

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Błędy uwierzytelniania

Jeśli wykonujesz operację zarządzania na celu obliczeniowym z zadania zdalnego, zostanie wyświetlony jeden z następujących błędów:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Na przykład zostanie wyświetlony błąd, jeśli spróbujesz utworzyć lub dołączyć obiekt docelowy obliczeń z potoku ml, który jest przesyłany do zdalnego wykonania.
