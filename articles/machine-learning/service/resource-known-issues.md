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
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 81eabadba70a2d5334fab43157f17d24c41d97ec
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103413"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Znane problemy i rozwiązywanie problemów Azure Machine Learning

Ten artykuł pomaga znaleźć i poprawić błędy lub błędy występujące podczas korzystania z Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problemy z interfejsem wizualnym

Interfejs wizualizacji problemów z usługą Machine Learning.

### <a name="long-compute-preparation-time"></a>Długi czas przygotowania obliczeń

Utwórz nowe obliczenia lub Evoke, opuszczając czas obliczeń, może być kilka minut, a nawet dłużej. Zespół pracuje nad optymalizacją.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Nie można uruchomić eksperymentu zawiera tylko zestaw danych 

Możesz chcieć uruchomić eksperyment zawierający tylko zestaw danych, aby wizualizować zestaw danych. Nie jest jednak możliwe uruchamianie eksperymentu tylko zawiera zestaw danych. Aktywnie Naprawiamy ten problem.
 
Przed usunięciem można połączyć zestaw danych z dowolnym modułem przekształcania danych (Wybierz kolumny w zestawie danych, edytować metadane, podzielić dane itp.) i uruchomić eksperyment. Następnie można wizualizować zestaw danych. 

Na poniższej ilustracji przedstawiono sposób ![: visulize — dane](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemy z instalacją zestawu SDK

**Komunikat o błędzie: Nie można odinstalować "PyYAML"**

Zestaw Azure Machine Learning SDK dla języka Python: PyYAML jest zainstalowaną distutils projektem. W związku z tym nie można dokładnie określić, które pliki należą do niej, jeśli istnieje częściowe odinstalowanie. Aby kontynuować instalację zestawu SDK podczas ignorowanie tego błędu, należy użyć:

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

## <a name="fpgas"></a>Układów FPGA

Nie można wdrażać modele na układów FPGA dopiero po przeprowadzeniu mają wymagane i zostało zatwierdzone dla limitu przydziału FPGA. Aby zażądać dostępu, wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Automatyczne Uczenie maszynowe w przepływie dwuosiowym obecnie nie obsługuje przepływu dwuosiowego w wersji 1,13. Zainstalowanie tej wersji spowoduje, że zależności pakietu przestaną działać. Pracujemy nad rozwiązaniem tego problemu w przyszłej wersji. 

### <a name="experiment-charts"></a>Wykresy eksperymentu

Binarne wykresy klasyfikacji (precyzja-odwoływanie, ROC, krzywa zysku itp.) pokazana w zautomatyzowanych iteracjach eksperymentów w przypadku nieprawidłowego renderowania w interfejsie użytkownika od 4/12. Wykresy wykresów są obecnie wyświetlane z wynikami odwrotnymi, gdzie lepsze są modele z niższymi wynikami. Zbadano rozwiązanie.

## <a name="databricks"></a>Databricks

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

W obszarze zautomatyzowane ustawienia uczenia maszynowego, jeśli masz więcej niż 10 iteracji, `show_output` Ustaw `False` wartość podczas przesyłania przebiegu.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Element widget dla Azure Machine Learning SDK/automatycznej uczenia maszynowego

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

`FailToSendFeather` Jeśli wystąpi błąd podczas odczytywania danych w klastrze Azure Databricks, zapoznaj się z następującymi rozwiązaniami:

* Uaktualnij `azureml-sdk[automl_databricks]` pakiet do najnowszej wersji.
* Dodaj `azure-dataprep` wersję 1.1.8 lub nowszą.
* Dodaj `pyarrow` wersję 0,11 lub nowszą.

## <a name="azure-portal"></a>Azure Portal

Jeśli przejdziesz bezpośrednio, aby wyświetlić obszar roboczy z Udostępnij link z zestawu SDK lub w portalu, nie można wyświetlić strony z normalnej Przegląd informacji o subskrypcji w rozszerzeniu. Ponadto nie można przełączyć się do innego obszaru roboczego. Jeśli zachodzi potrzeba wyświetlenia innego obszaru roboczego, obejście to przejście bezpośrednio do [Azure Portal](https://portal.azure.com) i wyszukanie nazwy obszaru roboczego.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Aby wyświetlić niektóre dzienniki, odwiedź stronę [Azure Portal](https://portal.azure.com) i przejdź do obszaru roboczego, a następnie wybierz pozycję **obszar roboczy > eksperymentuj > Uruchom > dzienników**.  Te informacje można również znaleźć w sekcji **eksperymenty** na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com).

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

Jeśli wystąpi błąd `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zalecenie polega na użyciu obiektów blob, dzięki czemu udział plików jest bezpłatny do użycia na potrzeby przesyłania przebiegów. Obciążenie można także podzielić między dwa różne obszary robocze.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Awarie usług WebServices w usłudze Azure Kubernetes 

Wiele błędów sieci Web w usłudze Azure Kubernetes można debugować, łącząc się z klastrem przy użyciu `kubectl`programu. Możesz uzyskać dostęp `kubeconfig.json` do klastra usługi Azure Kubernetes Service, uruchamiając

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizowanie składników Azure Machine Learning w klastrze AKS

Należy ręcznie zastosować aktualizacje Azure Machine Learning składników zainstalowanych w klastrze usługi Azure Kubernetes. Możesz zastosować te klastry, odłączając klaster od obszaru roboczego Azure Machine Learning, a następnie ponownie dołączając klaster do obszaru roboczego. Jeśli w klastrze jest włączony protokół SSL, podczas ponownego dołączania klastra należy podać certyfikat SSL i klucz prywatny. 

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

Jeśli certyfikat SSL i klucz prywatny nie są już używane lub używasz certyfikatu wygenerowanego przez Azure Machine Learning, możesz pobrać pliki przed odłączeniem klastra, łącząc się z klastrem przy użyciu `kubectl` i pobierając klucz tajny `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w zakodowanym formacie Base-64. Przed udostępnieniem tych `key.pem` elementów tajnych należy oprzeć `cert.pem` na Base-64. `attach_config.enable_ssl` 