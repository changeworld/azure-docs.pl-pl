---
title: Co to jest wystąpienie obliczeniowe usługi Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o wystąpieniu obliczeniowym usługi Azure Machine Learning, w pełni zarządzanej stacji roboczej opartej na chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283929"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co to jest wystąpienie obliczeniowe usługi Azure Machine Learning?

Wystąpienie obliczeniowe usługi Azure Machine Learning (wersja zapoznawcza) to w pełni zarządzana chmurowa stacja robocza dla analityków danych. 

Wystąpienia obliczeniowe ułatwiają rozpoczęcie pracy z programem Azure Machine Learning, a także zapewniają administratorom IT funkcje zarządzania i gotowości w przedsiębiorstwie.  

Użyj wystąpienia obliczeniowego jako w pełni skonfigurowanego i zarządzanego środowiska programistycznego w chmurze.

Wystąpienia obliczeniowe są zwykle używane jako środowiska programistyczne.  Mogą być również używane jako cel obliczeniowy dla szkolenia i wnioskowania do rozwoju i testowania.  W przypadku dużych zadań [klaster obliczeniowy usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) z możliwościami skalowania wielu węzłów jest lepszym wyborem docelowym obliczeń.


## <a name="why-use-a-compute-instance"></a>Dlaczego warto użyć wystąpienia obliczeniowego?

Wystąpienie obliczeniowe to w pełni zarządzana stacja robocza oparta na chmurze zoptymalizowana pod kątem środowiska programowania uczenia maszynowego. Zapewnia następujące korzyści:

|Najważniejsze korzyści||
|----|----|
|Produktywność|Analitycy danych mogą tworzyć i wdrażać modele przy użyciu zintegrowanych notesów i następujących narzędzi w przeglądarce internetowej:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio|
|Zarządzane & bezpieczne|Zmniejsz bezpieczeństwo i zwiększ zgodność z wymaganiami dotyczącymi zabezpieczeń przedsiębiorstwa. Wystąpienia obliczeniowe zapewniają niezawodne zasady zarządzania i bezpieczne konfiguracje sieci, takie jak:<br/><br/>- Automatyczne inicjowanie obsługi administracyjnej z szablonów Usługi Resource Manager lub zestaw SDK usługi Azure Machine Learning<br/>- [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview)<br/>- [Obsługa sieci wirtualnej](how-to-enable-virtual-network.md#compute-instance)<br/>- Zasady SSH, aby włączyć / wyłączyć dostęp SSH|
|Wstępnie skonfigurowane&nbsp;&nbsp;lub ML|Zaoszczędź czas na zadaniach konfiguracyjnych dzięki wstępnie skonfigurowanym i aktualnym pakietom uczenia maszynowego, platformom uczenia głębokiego, sterownikom GPU.|
|W pełni konfigurowalny|Szeroka obsługa typów maszyn wirtualnych platformy Azure, w tym procesorów GPU i utrwalonych dostosowywania niskiego poziomu, takich jak instalowanie pakietów i sterowników, sprawia, że zaawansowane scenariusze są bardzo proste. |

## <a name="tools-and-environments"></a><a name="contents"></a>Narzędzia i środowiska

Wystąpienie obliczeniowe usługi Azure Machine Learning umożliwia tworzenie, szkolenie i wdrażanie modeli w pełni zintegrowanym doświadczeniu notesu w obszarze roboczym.


Te narzędzia i środowiska są instalowane w wystąpieniu obliczeniowym: 

|Narzędzia ogólne & środowiskach|Szczegóły|
|----|:----:|
|Sterowniki|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteka Intel MPI||
|Interfejs wiersza polecenia platformy Azure ||
|Przykłady usługi Azure Machine Learning ||
|Aparat EDAT usługi Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**Narzędzia R** & środowiskach|Szczegóły|
|----|:----:|
|Wersja open source serwera RStudio||
|Jądro R||
|Zestaw SDK usługi Azure Machine Learning dla języka R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Przykłady dla zestawu SDK|

|Narzędzia **PYTHON** & środowiskach|Szczegóły|
|----|----|
|Anaconda Python||
|Jupyter i rozszerzenia||
|Jupyterlab i rozszerzenia||
|Visual Studio Code ||
[Zestaw SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>od PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Inne pakiety PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pakiety Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pakiety uczenia głębokiego|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pakiety ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Przykłady zestawów SDK usługi Azure Machine Learning Python & R||

Pakiety Python są instalowane w środowisku **Python 3.6 — AzureML.**  

Wystąpienia obliczeniowe są zwykle używane jako środowiska programistyczne.  Mogą być również używane jako cel obliczeniowy dla szkolenia i wnioskowania do rozwoju i testowania.  W przypadku dużych zadań [klaster obliczeniowy usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) z możliwościami skalowania wielu węzłów jest lepszym wyborem docelowym obliczeń.

### <a name="installing-packages"></a>Instalowanie pakietów

Pakiety można instalować bezpośrednio w notesie Jupyter lub Rstudio:

* RStudio Użyj karty **Pakiety** w prawym dolnym rogu lub karty **Konsola** w lewym górnym rogu.  
* Python: Dodaj kod instalacji i wykonaj w komórce notesu Jupyter.

Lub można uzyskać dostęp do okna terminala w dowolny z następujących sposobów:

* RStudio: Wybierz kartę **Terminal** w lewym górnym rogu.
* Laboratorium Jupyter: Wybierz kafelek **Terminal** pod nagłówkiem **Inne** na karcie Launcher.
* Jupyter: Wybierz **nowy terminal>** w prawym górnym rogu na karcie Pliki.
* SSH do urządzenia.  Następnie zainstaluj pakiety Języka Python w środowisku **Python 3.6 — AzureML.**  Zainstaluj pakiety R w środowisku **R.**

## <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Notesy i skrypty języka R są przechowywane na domyślnym koncie magazynu obszaru roboczego w udziale plików platformy Azure.  Pliki te znajdują się w katalogu "Pliki użytkownika". Ta pamięć masowa ułatwia udostępnianie notesów między wystąpieniami obliczeniowymi. Konto magazynu przechowuje również notesy bezpiecznie zachowane po zatrzymaniu lub usunięciu wystąpienia obliczeniowego.

Konto udziału plików platformy Azure obszaru roboczego jest zainstalowane jako dysk w wystąpieniu obliczeniowym. Ten dysk jest domyślnym katalogiem roboczym dla Jupyter, Jupyter Labs i RStudio.

Pliki w udziale plików są dostępne ze wszystkich wystąpień obliczeniowych w tym samym obszarze roboczym. Wszelkie zmiany tych plików w wystąpieniu obliczeniowym będą niezawodnie utrwalone z powrotem do udziału plików.

Można również sklonować najnowsze przykłady usługi Azure Machine Learning do folderu w katalogu plików użytkownika w udziale plików obszaru roboczego.

Zapisywanie małych plików może być wolniejsze na dyskach sieciowych niż zapisywanie na samej maszynie wirtualnej.  Jeśli piszesz wiele małych plików, spróbuj użyć katalogu bezpośrednio na wystąpienie `/tmp` obliczeniowe, takich jak katalog. Należy pamiętać, że te pliki nie będą dostępne z innych wystąpień obliczeniowych w obszarze roboczym.

## <a name="managing-a-compute-instance"></a>Zarządzanie wystąpieniem obliczeniowym

W obszarze roboczym w studiu usługi Azure Machine Learning wybierz pozycję **Oblicz,** a następnie wybierz pozycję **Wystąpienie obliczeniowe** u góry.

![Zarządzanie wystąpieniem obliczeniowym](./media/concept-compute-instance/manage-compute-instance.png)

Można wykonać następujące czynności:

* Tworzenie wystąpienia obliczeniowego. Określ nazwę, typ maszyny Wirtualnej platformy Azure, w tym procesory GPU (należy pamiętać, że nie można zmienić typu maszyny Wirtualnej po utworzeniu), włączyć/wyłączyć dostęp SSH i opcjonalnie skonfigurować ustawienia sieci wirtualnej. Wystąpienie można również utworzyć bezpośrednio ze zintegrowanych notesów, witryny Azure Portal, szablonu Usługi Resource Manager lub zestawu SDK usługi Azure Machine Learning. Przydział dedykowanych rdzeni na region, który ma zastosowanie do tworzenia wystąpienia obliczeniowego, jest ujednolicony i współużytkowany przydziałem klastra obliczeniowego usługi Azure Machine Learning.
* Odświeżanie karty wystąpienia obliczeniowe
* Uruchamianie, zatrzymywania i ponownego uruchamiania wystąpienia obliczeniowego
* Usuwanie wystąpienia obliczeń

Dla każdego wystąpienia obliczeniowego w obszarze roboczym można:

* Dostęp Jupyter, JupyterLab, RStudio w instancji obliczeniowej
* SSH do instancji obliczeniowej. Dostęp SSH jest domyślnie wyłączony, ale można go włączyć w czasie tworzenia wystąpienia obliczeniowego. Dostęp SSH odbywa się za pośrednictwem mechanizmu klucza publicznego/prywatnego. Karta zawiera szczegółowe informacje dotyczące połączenia SSH, takie jak adres IP, nazwa użytkownika i numer portu.
* Uzyskaj szczegółowe informacje o określonym wystąpieniu obliczeniowym, takim jak adres IP i region.

[RBAC](/azure/role-based-access-control/overview) pozwala kontrolować, którzy użytkownicy w obszarze roboczym mogą tworzyć, usuwać, uruchamiać, zatrzymywać, ponownie uruchamiać wystąpienie obliczeniowe. Wszyscy użytkownicy w roli współautora i właściciela obszaru roboczego mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienia obliczeniowe w obszarze roboczym. Jednak tylko twórca określonego wystąpienia obliczeń może uzyskać dostęp do Jupyter, JupyterLab i RStudio w tym wystąpieniu obliczeniowym. Twórca instancji obliczeniowej ma dedykowane wystąpienie obliczeniowe, dostęp do roota i może się wyjąć za pośrednictwem Jupytera. Compute wystąpienie będzie miał logowania jednego użytkownika użytkownika twórcy i wszystkie akcje będą używać tożsamości tego użytkownika dla RBAC i przypisanie przebiegów eksperymentu. Dostęp SSH jest kontrolowany za pośrednictwem mechanizmu klucza publicznego/prywatnego.

Można również utworzyć wystąpienie
* Bezpośrednio ze zintegrowanych notebooków
* W witrynie Azure portal
* Z szablonu usługi Azure Resource Manager
* Dzięki zestawowi SDK usługi Azure Machine Learning

Dedykowane rdzenie na przydział regionu, który ma zastosowanie do tworzenia wystąpienia obliczeniowego jest ujednolicony i współużytkowany z przydziałem klastra szkolenia usługi Azure Machine Learning. 

## <a name="compute-target"></a>Docelowy obiekt obliczeniowy

Wystąpienia obliczeniowe mogą służyć jako [docelowy obliczeń szkoleniowych](concept-compute-target.md#train) podobny do klastrów szkoleniowych obliczeń usługi Azure Machine Learning. Aprowizuj maszynę wirtualną z wieloma procesorami GPU w celu uruchamiania rozproszonych zadań szkoleniowych przy użyciu estymatorów TensorFlow/PyTorch. Można również utworzyć konfigurację uruchamiania i użyć jej do uruchomienia eksperymentu na wystąpieniu obliczeniowym. Wystąpienie obliczeniowe można użyć jako lokalnego miejsca docelowego wdrożenia wnioskowania do testowania/debugowania scenariuszy.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Co się stało z maszyną wirtualną notesu?

Wystąpienia obliczeniowe zastępują maszynę wirtualną notesu.  

Wszystkie pliki notesu przechowywane w udziale plików obszaru roboczego i dane w magazynach danych obszaru roboczego będą dostępne z wystąpienia obliczeniowego. Jednak wszystkie pakiety niestandardowe zainstalowane wcześniej na maszynie Wirtualnej notesu będą musiały zostać ponownie zainstalowane w wystąpieniu obliczeniowym. Ograniczenia przydziału, które mają zastosowanie do tworzenia klastrów obliczeniowych będą miały zastosowanie również do tworzenia wystąpień obliczeniowych. 

Nie można utworzyć nowych maszyn wirtualnych notesu. Jednak nadal można uzyskiwać dostęp do utworzonych maszyn wirtualnych notesu i używać ich z pełną funkcjonalnością. Wystąpienia obliczeniowe można tworzyć w tym samym obszarze roboczym, co istniejące maszyny wirtualne notesu. 


## <a name="next-steps"></a>Następne kroki

 * [Samouczek: Trenuj swój pierwszy model uczenia maszynowego](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego ze zintegrowanym notesem.
