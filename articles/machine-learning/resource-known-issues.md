---
title: Znane problemy & Rozwiązywanie problemów
titleSuffix: Azure Machine Learning
description: Zapoznaj się z listą znanych problemów, obejść i rozwiązywania problemów dotyczących Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9824f5cfd7b42860079536232b8a5ad40ea608c9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638361"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Znane problemy i rozwiązywanie problemów Azure Machine Learning

Ten artykuł pomaga znaleźć i poprawić błędy lub błędy występujące podczas korzystania z Azure Machine Learning.

## <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Awaria: uaktualnianie SR-IOV do maszyn seria NCV3 w AmlCompute

Usługa Azure COMPUTE będzie aktualizować jednostki SKU seria NCV3 zaczynające się od początku listopada 2019, aby obsługiwały wszystkie implementacje i wersje MPI oraz czasowniki RDMA dla maszyn wirtualnych z systemem InfiniBand. Będzie to wymagało krótkiego przestoju — [Przeczytaj więcej na temat uaktualnienia wirtualizacji SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Jako klient z zarządzaną ofertą obliczeniową Azure Machine Learning (AmlCompute) nie musisz wprowadzać żadnych zmian w tym momencie. Na podstawie [harmonogramu aktualizacji](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) konieczne jest zaplanowanie krótkiej przerwy w szkoleniu. Usługa będzie odpowiedzialna za Aktualizowanie obrazów maszyn wirtualnych w węzłach klastra i automatyczne skalowanie klastra w górę. Po zakończeniu uaktualniania może być możliwe użycie wszystkich innych dystrybucji MPI (na przykład OpenMPI z Pytorch) Oprócz uzyskania wyższej przepustowości InfiniBand, mniejszych opóźnień i lepszej wydajności aplikacji rozproszonej.

## <a name="azure-machine-learning-designer-issues"></a>Problemy z programem Azure Machine Learning Designer

Znane problemy związane z projektantem.

### <a name="long-compute-preparation-time"></a>Długi czas przygotowania obliczeń

Utwórz nowe obliczenia lub Evoke, opuszczając czas obliczeń, może być kilka minut, a nawet dłużej. Zespół pracuje nad optymalizacją.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Nie można uruchomić eksperymentu zawiera tylko zestaw danych 

Możesz chcieć uruchomić eksperyment zawierający tylko zestaw danych, aby wizualizować zestaw danych. Nie jest jednak możliwe uruchamianie eksperymentu tylko zawiera zestaw danych. Aktywnie Naprawiamy ten problem.
 
Przed usunięciem można połączyć zestaw danych z dowolnym modułem przekształcania danych (Wybierz kolumny w zestawie danych, edytować metadane, podzielić dane itp.) i uruchomić eksperyment. Następnie można wizualizować zestaw danych. 

Poniższy obraz pokazuje, jak: ![visulize — dane](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemy z instalacją zestawu SDK

**Komunikat o błędzie: nie można odinstalować "PyYAML"**

Zestaw Azure maszyny Learning SDK dla języka Python: PyYAML jest projektem distutils zainstalowane. W związku z tym nie można dokładnie określić, które pliki należą do niej, jeśli istnieje częściowe odinstalowanie. Aby kontynuować instalację zestawu SDK podczas ignorowanie tego błędu, należy użyć:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Komunikat o błędzie: `ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda 3.7.4 w języku Python zawiera usterkę, która powoduje przerwanie instalacji aplikacji Azure-SDK. Ten problem został omówiony w tym [problemie](https://github.com/ContinuumIO/anaconda-issues/issues/11195) z usługą GitHub, ponieważ może to obejść, tworząc nowe środowisko Conda za pomocą tego polecenia:
```bash
conda create -n <env-name> python=3.7.3
```
Tworzy środowisko Conda przy użyciu języka Python 3.7.3, który nie ma problemu z instalacją w 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemy z utworzeniem obliczeniowego usługi Azure Machine Learning

Brak rzadkich prawdopodobieństwo, że niektórych użytkowników, którzy utworzyli swój obszar roboczy usługi Azure Machine Learning w witrynie Azure portal przed wydaniem Ogólnodostępnej mogą nie można utworzyć obliczeniowego usługi Azure Machine Learning, w tym obszarze roboczym. Możesz zgłosić żądanie pomocy technicznej, korzystająca z usługi lub Utwórz nowy obszar roboczy za pośrednictwem portalu lub zestawu SDK, aby odblokować samodzielnie natychmiast.

## <a name="image-building-failure"></a>Błąd tworzenia obrazu

Obraz tworzenia niepowodzenia podczas wdrażania usługi sieci web. Obejście polega na dodawanie "pynacl == 1.2.1" jako zależność pip Conda pliku konfiguracji obrazu.

## <a name="deployment-failure"></a>Niepowodzenie wdrożenia

Jeśli obserwujesz `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, Zmień jednostkę SKU dla maszyn wirtualnych używanych we wdrożeniu na taką, która ma więcej pamięci.

## <a name="fpgas"></a>Układy FPGA

Nie można wdrażać modele na układów FPGA dopiero po przeprowadzeniu mają wymagane i zostało zatwierdzone dla limitu przydziału FPGA. Aby zażądać dostępu, wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Automatyczne Uczenie maszynowe w przepływie dwuosiowym obecnie nie obsługuje przepływu dwuosiowego w wersji 1,13. Zainstalowanie tej wersji spowoduje, że zależności pakietu przestaną działać. Pracujemy nad rozwiązaniem tego problemu w przyszłej wersji. 

### <a name="experiment-charts"></a>Wykresy eksperymentu

Binarne wykresy klasyfikacji (precyzja-odwoływanie, ROC, krzywa zysku itp.) pokazana w zautomatyzowanych iteracjach eksperymentów w przypadku nieprawidłowego renderowania w interfejsie użytkownika od 4/12. Wykresy wykresów są obecnie wyświetlane z wynikami odwrotnymi, gdzie lepsze są modele z niższymi wynikami. Zbadano rozwiązanie.

## <a name="datasets-and-data-preparation"></a>Zestawy danych i przygotowanie

Są to znane problemy dotyczące Azure Machine Learning zestawów danych.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: Brak pliku lub katalogu

Ten błąd występuje, gdy podano ścieżkę do pliku, w którym znajduje się plik. Należy upewnić się, że sposób odwoływania się do pliku jest zgodny z miejscem, w którym został zainstalowany zestaw danych na obiekcie docelowym obliczeń. Aby zapewnić jednoznaczny stan, zalecamy użycie ścieżki abstrakcyjnej podczas instalowania zestawu danych do obiektu docelowego obliczeń. Na przykład, w poniższym kodzie instalujemy zestaw danych w katalogu głównym systemu plików obiektu docelowego obliczeń `/tmp`. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Jeśli nie dołączysz wiodącego ukośnika "/", musisz utworzyć prefiks katalogu roboczego, np. `/mnt/batch/.../tmp/dataset` w elemencie docelowym obliczeń, aby wskazać, gdzie ma być zainstalowany zestaw danych. 

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Nie można odczytać pliku Parquet z HTTP lub ADLS Gen 2

Istnieje znany problem w wersji zestawu SDK dataprzygotowania usługi Azure DATA1.1.25, która powoduje awarię podczas tworzenia zestawu danych przez odczytywanie plików Parquet z protokołu HTTP lub ADLS generacji 2. Zakończy się niepowodzeniem z `Cannot seek once reading started.`. Aby rozwiązać ten problem, Uaktualnij `azureml-dataprep` do wersji nowszej niż 1.1.26 lub zmień wersję na starszą niż 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: Mount () otrzymał nieoczekiwany argument słowa kluczowego "invocation_id"

Ten błąd występuje, jeśli masz niezgodną wersję między `azureml-core` i `azureml-dataprep`. Jeśli ten błąd wystąpi, Uaktualnij pakiet `azureml-dataprep` do nowszej wersji (nie więcej niż lub równą 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Usługa Databricks

Problemy z usługi Databricks i Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Błąd podczas instalowania pakietów

Azure Machine Learning Instalacja zestawu SDK kończy się niepowodzeniem na Azure Databricks po zainstalowaniu większej liczby pakietów. Niektóre pakiety, takich jak `psutil`, mogą powodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety przez zamarzanie wersji biblioteki. Ten problem jest związany z kostkami, a nie z zestawem SDK Azure Machine Learning. Ten problem może również wystąpić z innymi bibliotekami. Przykład:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Alternatywnie można użyć skryptów init w przypadku, gdy występują problemy z instalacją w języku Python. Takie podejście nie jest oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [skrypty init z zakresem klastra](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Anuluj automatyczny przebieg uczenia maszynowego

W przypadku korzystania z funkcji automatycznego uczenia maszynowego na Azure Databricks w celu anulowania przebiegu i uruchomienia nowego eksperymentu Uruchom ponownie klaster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iteracji dla automatycznej uczenia maszynowego

W obszarze zautomatyzowane ustawienia uczenia maszynowego, jeśli masz więcej niż 10 iteracji, ustaw `show_output` na `False` podczas przesyłania przebiegu.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Element widget dla zestawu SDK Azure Machine Learning i automatycznego uczenia maszynowego

Element widget zestawu Azure Machine Learning SDK nie jest obsługiwany w notesie datacegły, ponieważ notesy nie mogą analizować widżetów HTML. Widżet można wyświetlić w portalu przy użyciu tego kodu w języku Python w komórce notesu Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Błąd importowania: Brak modułu o nazwie "Pandas. Core. Indexs"

Jeśli ten błąd wystąpi podczas korzystania z automatycznej uczenia maszynowego:

1. Uruchom to polecenie, aby zainstalować dwa pakiety w klastrze Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odłącz i ponownie Dołącz klaster do notesu. 

Jeśli te kroki nie rozwiążą problemu, spróbuj ponownie uruchomić klaster.

### <a name="failtosendfeather"></a>FailToSendFeather

Jeśli zobaczysz błąd `FailToSendFeather` podczas odczytywania danych w klastrze Azure Databricks, zapoznaj się z następującymi rozwiązaniami:

* Uaktualnij pakiet `azureml-sdk[automl]` do najnowszej wersji.
* Dodaj `azureml-dataprep` w wersji 1.1.8 lub nowszej.
* Dodaj `pyarrow` w wersji 0,11 lub nowszej.

## <a name="azure-portal"></a>Portal Azure

Jeśli przejdziesz bezpośrednio, aby wyświetlić obszar roboczy z Udostępnij link z zestawu SDK lub w portalu, nie można wyświetlić strony z normalnej Przegląd informacji o subskrypcji w rozszerzeniu. Ponadto nie można przełączyć się do innego obszaru roboczego. Jeśli zachodzi potrzeba wyświetlenia innego obszaru roboczego, obejście to przejście bezpośrednio do [Azure Machine Learning Studio](https://ml.azure.com) i wyszukanie nazwy obszaru roboczego.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Aby wyświetlić niektóre dzienniki, odwiedź stronę [Azure Machine Learning Studio](https://ml.azure.com) i przejdź do obszaru roboczego, a następnie wybierz pozycję **obszar roboczy > eksperymentuj > Uruchom > dzienników**.  

> [!NOTE]
> Azure Machine Learning rejestruje informacje z różnych źródeł podczas szkolenia, takie jak AutoML lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Wiele z tych dzienników nie jest udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.

## <a name="activity-logs"></a>Dzienniki aktywności

Niektóre akcje w obszarze roboczym Azure Machine Learning nie rejestrują informacji w __dzienniku aktywności__. Na przykład uruchomienie szkolenia lub zarejestrowanie modelu.

Niektóre z tych akcji są wyświetlane w obszarze __działania__ obszaru roboczego, ale nie wskazują, kto zainicjował działanie.

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [limity przydziałów zasobów](how-to-manage-quotas.md) można napotkać podczas pracy z usługą Azure Machine Learning.

## <a name="authentication-errors"></a>Błędy uwierzytelniania

W przypadku wykonywania operacji zarządzania na obiekcie docelowym obliczeń z zadania zdalnego zostanie wyświetlony jeden z następujących błędów:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Na przykład, jeśli spróbujesz utworzyć lub dołączyć obiekt docelowy obliczeń z potoku, który został przesłany do wykonania zdalnego, zostanie wyświetlony komunikat o błędzie.

## <a name="overloaded-azurefile-storage"></a>Przeciążony magazyn AzureFile

Jeśli zostanie wyświetlony komunikat o błędzie `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zalecenie polega na użyciu obiektów blob, dzięki czemu udział plików jest bezpłatny do użycia na potrzeby przesyłania przebiegów. Obciążenie można także podzielić między dwa różne obszary robocze.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Awarie usług WebServices w usłudze Azure Kubernetes 

Wiele błędów sieci Web w usłudze Azure Kubernetes można debugować, łącząc się z klastrem przy użyciu `kubectl`. `kubeconfig.json` klastra usługi Azure Kubernetes Service można uzyskać, uruchamiając

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizowanie składników Azure Machine Learning w klastrze AKS

Należy ręcznie zastosować aktualizacje Azure Machine Learning składników zainstalowanych w klastrze usługi Azure Kubernetes. 

Te aktualizacje można zastosować, odłączając klaster od obszaru roboczego Azure Machine Learning, a następnie dołączając ponownie klaster do obszaru roboczego. Jeśli w klastrze jest włączony protokół SSL, podczas ponownego dołączania klastra należy podać certyfikat SSL i klucz prywatny. 

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

Jeśli certyfikat SSL i klucz prywatny nie są już używane lub używasz certyfikatu wygenerowanego przez Azure Machine Learning, możesz pobrać pliki przed odłączeniem klastra, łącząc się z klastrem przy użyciu `kubectl` i pobierając `azuremlfessl`klucza tajnego.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w zakodowanym formacie Base-64. Aby zapewnić `attach_config.enable_ssl`, należy 64 najpierw zdekodować `cert.pem` i `key.pem` składniki wpisów tajnych. 

## <a name="recommendations-for-error-fix"></a>Zalecenia dotyczące poprawki błędów
W oparciu o ogólną obserwację poniżej przedstawiono zalecenia dotyczące platformy Azure ML do rozwiązywania niektórych typowych błędów w usłudze Azure ML.

### <a name="metric-document-is-too-large"></a>Dokument metryki jest zbyt duży
Azure Machine Learning ma wewnętrzne limity rozmiaru obiektów metryk, które mogą być rejestrowane jednocześnie z poziomu przebiegu szkoleniowego. Jeśli wystąpi błąd "dokument metryki jest zbyt duży" podczas rejestrowania metryki o wartościach listy, spróbuj podzielić listę na mniejsze fragmenty, na przykład:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

 Wewnętrznie usługa historia uruchamiania łączy bloki z tą samą nazwą metryki z nieciągłą listą.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (Brak modułu o nazwie)
Jeśli używasz programu ModuleErrors podczas przesyłania eksperymentów na platformie Azure ML, oznacza to, że skrypt szkoleniowy oczekuje na zainstalowanie pakietu, ale nie został dodany. Po podaniu nazwy pakietu platforma Azure ML zainstaluje pakiet w środowisku używanym do uczenia się. 

Jeśli używasz [szacowania](concept-azure-machine-learning-architecture.md#estimators) do przesyłania eksperymentów, możesz określić nazwę pakietu za pośrednictwem `pip_packages` lub `conda_packages` parametru w szacowania, na podstawie którego źródła chcesz zainstalować pakiet. Można również określić plik yml ze wszystkimi zależnościami przy użyciu `conda_dependencies_file`lub wyświetlić wszystkie wymagania dotyczące PIP w pliku txt przy użyciu parametru `pip_requirements_file`.

Platforma Azure ML udostępnia również specyficzne dla platformy szacowania dla Tensorflow, PyTorch, łańcucha i skryptu sklearn. Przy użyciu tych szacowania upewnij się, że zależności struktury są zainstalowane w Twoim imieniu w środowisku używanym do uczenia się. Istnieje możliwość określenia dodatkowych zależności, jak opisano powyżej. 
 
Platforma Azure ML obsługuje obrazy platformy Docker i ich zawartość można zobaczyć w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers).
Zależności specyficzne dla platformy są wymienione w odpowiedniej strukturze dokumentacji programu — [łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [skryptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Jeśli uważasz, że określony pakiet jest wystarczająco powszechny do dodania do obrazów i środowisk konserwowanych platformy Azure, zgłoś problem w usłudze GitHub w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers)ml. 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (nazwa niezdefiniowana), AttributeError (obiekt nie ma atrybutu)
Ten wyjątek powinien pochodzić ze skryptów szkoleniowych. Można przyjrzeć się plikom dziennika z Azure Portal, aby uzyskać więcej informacji na temat konkretnej nazwy niezdefiniowanej lub błędu atrybutu. Z zestawu SDK można użyć `run.get_details()`, aby sprawdzić komunikat o błędzie. Spowoduje to wyświetlenie listy wszystkich plików dziennika wygenerowanych dla danego przebiegu. Upewnij się, że zapoznaj się z skryptem szkoleniowym, usuń błąd przed ponowną próbą. 

### <a name="horovod-is-shut-down"></a>Horovod jest wyłączona
W większości przypadków ten wyjątek oznacza, że wystąpił podstawowy wyjątek w jednym z procesów, które spowodowały zamknięcie horovod. Każda ranga w zadaniu MPI pobiera własny dedykowany plik dziennika w usłudze Azure ML. Te dzienniki mają nazwę `70_driver_logs`. W przypadku szkolenia rozproszonego nazwy dzienników są sufiksem z `_rank`, aby ułatwić odróżnienie dzienników. Aby znaleźć dokładny błąd, który spowodował zamknięcie horovod, przejdź przez wszystkie pliki dziennika i poszukaj `Traceback` na końcu plików driver_log. Jeden z tych plików daje rzeczywisty wyjątek podstawowy. 

## <a name="labeling-projects-issues"></a>Problemy z etykietami projektów

Znane problemy związane z etykietowaniem projektów.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Można używać tylko zestawów danych utworzonych w magazynach danych obiektów BLOB

Jest to znane ograniczenie bieżącej wersji. 

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>Po utworzeniu projekt pokazuje "Inicjowanie" przez długi czas

Ręcznie Odśwież stronę. Inicjalizacja powinna być w przybliżeniu 20 punktów, na sekundę. Brak autoodświeżania to znany problem. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Podczas przeglądania obrazów nie są wyświetlane nowe obrazy z etykietami

Aby załadować wszystkie obrazy z etykietami, wybierz **pierwszy** przycisk. **Pierwszy** przycisk przeprowadzi Cię z powrotem do początku listy, ale ładuje wszystkie dane z etykietami.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Naciśnięcie klawisza Esc podczas etykietowania dla wykrywania obiektów tworzy etykietę o zerowej wielkości w lewym górnym rogu. Przesyłanie etykiet w tym stanie nie powiodło się.

Usuń etykietę, klikając znak krzyżyka obok niego.
