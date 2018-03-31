---
title: Pliki konfiguracji w usłudze Azure Machine Learning eksperymenty usługi
description: Ten dokument zawiera szczegóły dotyczące ustawień konfiguracji usługi Azure ML eksperymenty.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 6cec039034f0650d017eb14de584939bb3191223
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Pliki konfiguracji w usłudze Azure Machine Learning eksperymenty usługi

Po uruchomieniu skryptu w Workbench uczenie maszynowe Azure (Azure ML) zachowanie wykonywania jest kontrolowany przez pliki w **aml_config** folderu. Ten folder znajduje się w katalogu głównym folderu projektu. Należy zrozumieć zawartość tych plików, aby osiągnąć żądanego wyniku dla Twojego wykonywania w optymalny sposób.

Poniżej przedstawiono istotne pliki w tym folderze:
- conda_dependencies.yml
- spark_dependencies.yml
- pliki docelowe obliczeniowe
    - \<Nazwa docelowego obliczeniowe > .compute
- uruchamianie plików konfiguracji
    - \<Uruchom Nazwa konfiguracji > .runconfig

>[!NOTE]
>Zazwyczaj plik docelowy obliczeń i uruchom plik konfiguracji dla każdego elementu docelowego obliczeniowe utworzone. Można jednak niezależnie tworzenia tych plików i wielu plików konfigurację uruchomieniową wskazujące na ten sam cel obliczeń.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Ten plik jest [conda środowiska pliku](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) , który określa wersję środowiska uruchomieniowego języka Python i pakiety, które zależy od kodu. Podczas Azure ML Workbench skryptu w kontenerze Docker lub klastra usługi HDInsight, tworzy [środowiska conda](https://conda.io/docs/using/envs.html) skryptu do uruchomienia na. 

W tym pliku należy określić pakietów języka Python, potrzebne do wykonania skryptu. Usługa Azure eksperymenty ML powoduje utworzenie środowiska conda zgodnie z listy zależności. Pakiety wymienione w tym miejscu musi być osiągalna przez aparat wykonywania kanałami takich jak:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* publicznie dostępnym punkcie końcowym (URL)
* lub ścieżka do pliku lokalnego
* inne dostępne przez aparat wykonywania

>[!NOTE]
>Podczas uruchamiania w klastrze usługi HDInsight, Azure ML Workbench tworzy środowisku conda dla użytkownika określonego przebiegu. Dzięki temu różnych użytkownikom na uruchamianie na środowiska python różnych na tym samym klastrze.  

Oto przykład typowe **conda_dependencies.yml** pliku.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench używa tego samego środowiska conda bez ponownie skompilować tak długo, jak **conda_dependencies.yml** jest taka sama. Będzie go odbudować środowiska zależności zmiany.

>[!NOTE]
>W przypadku skierowania miało zostać wykonane _lokalnego_ obliczeniowe kontekście **conda_dependencies.yml** plik jest **nie** używane. Zależności pakietów dla lokalnego środowiska Python Workbench uczenia Maszynowego Azure należy zainstalować ręcznie.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Ten plik Określa nazwę aplikacji Spark podczas przesyłania skryptu PySpark i Spark pakietów, które muszą być zainstalowane. Można również określić publicznego repozytorium Maven, a także Spark pakiety, które znajdują się w tych repozytoria Maven.

Oto przykład:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Dostrajanie parametrów, takich jak rozmiar procesu roboczego i rdzeni klastra powinien znajdować się w sekcji "Konfiguracja" w pliku spark_dependecies.yml 

>[!NOTE]
>Jeśli skrypt są wykonywane w środowisku Python *spark_dependencies.yml* plik zostanie zignorowany. Jest używany tylko w przypadku korzystania z platformy Spark (albo Docker lub do klastra usługi HDInsight).

## <a name="run-configuration"></a>Uruchom konfigurację
Aby określić określoną konfigurację uruchamiania, należy oraz plik .compute .runconfig. Te są zwykle generowane przy użyciu polecenia interfejsu wiersza polecenia. Można również sklonować Kończenie te, zmień ich nazwy i edytować.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

To polecenie tworzy parę plików w oparciu o docelowy obliczeń określony. Załóżmy, że nosi nazwę docelowego obliczeń _foo_. To polecenie generuje _foo.compute_ i _foo.runconfig_ w Twojej **aml_config** folderu.

>[!NOTE]
> _lokalne_ lub _docker_ nazwy dla dowolnego są pliki konfiguracji uruchamiania. Azure ML Workbench dodaje się, że te dwa Uruchom konfiguracji podczas tworzenia pustego projektu dla Twojej wygody. Można zmienić nazwy "<run configuration name>.runconfig" pliki, które pochodzą z szablonem projektu lub Utwórz nowe z dowolną nazwę.

### <a name="compute-target-namecompute"></a>\<Nazwa docelowego obliczeniowe > .compute
_\<Nazwa docelowego obliczeniowe > .compute_ plik Określa połączenia i informacje konfiguracyjne dla elementu docelowego obliczeń. Znajduje się lista par nazwa wartość. Poniżej przedstawiono obsługiwane ustawienia:

**Typ**: Typ środowiska obliczeniowego. Obsługiwane są następujące wartości:
  - lokalne
  - Zdalne
  - Docker
  - remotedocker
  - klaster

**baseDockerImage**: Docker obrazu, który służy do uruchamiania skryptu Python/PySpark. Wartość domyślna to _microsoft/mmlspark:plus-0.7.91_. Obsługujemy również innego obrazu: _microsoft/mmlspark:plus-gpu-0.7.91_, które zapewnia dostęp do procesora GPU na komputerze hosta (jeśli występuje GPU).

**adres**: adres IP lub nazwa FQDN (pełna nazwa domeny) maszyny wirtualnej lub HDInsight head węzła klastra.

**Nazwa użytkownika**: nazwa użytkownika SSH do uzyskiwania dostępu do maszyny wirtualnej lub węzłem head HDInsight.

**hasło**: zaszyfrowane hasło dla połączenia SSH.

**sharedVolumes**: flagi sygnalizują, który aparat wykonywania należy używać Docker udostępnionego woluminu funkcji na potrzeby wysłania plików projektu i z powrotem. O tej flagi włączone przyspieszyć wykonywanie ponieważ Docker mogą uzyskiwać dostęp do projektów bezpośrednio, bez konieczności skopiuj je. Najlepiej ustawić _false_ czy aparatem platformy Docker działa w systemie Windows od woluminu udostępniania dla platformy Docker w systemie Windows może być niestabilnym. Ustaw ją na _true_ Jeśli jest uruchomiony system macOS lub Linux.

**nvidiaDocker**: tej flagi, gdy wartość _true_, informuje usługi Azure ML eksperymenty do _nvidia docker_ polecenia, w przeciwieństwie do zwykłej _docker_polecenia do uruchomienia obrazu Docker. _Nvidia docker_ aparat umożliwia kontenerze Docker na dostęp do procesora GPU sprzętu. Ustawienie jest wymagane, jeśli chcesz uruchomić wykonywania procesora GPU w kontenerze Docker. Obsługuje tylko hoście z systemem Linux _nvidia docker_. Na przykład DSVM opartych na systemie Linux na platformie Azure jest dostarczany z _nvidia docker_. _NVIDIA docker_ od tej chwili nie jest obsługiwana w systemie Windows.

**nativeSharedDirectory**: Ta właściwość określa podstawowego katalogu (na przykład: _~/.azureml/share/_) w celu udostępnienia w którym można zapisać plików działa na tej samej wartości docelowej obliczeń. Jeśli to ustawienie jest używane, gdy uruchomione w kontenerze Docker _sharedVolumes_ musi być ustawiona na wartość true. W przeciwnym razie wykonanie nie powiedzie się.

**userManagedEnvironment**: Ta właściwość określa, czy ten element docelowy obliczeniowe jest zarządzane bezpośrednio przez użytkownika lub zarządzanych za pomocą usługi eksperymenty.  

**pythonLocation**: Ta właściwość określa lokalizację środowiska uruchomieniowego języka python do użycia w celu obliczeń do wykonania programu użytkownika. 

### <a name="run-configuration-namerunconfig"></a>\<Uruchom Nazwa konfiguracji > .runconfig
_\<Uruchom Nazwa konfiguracji > .runconfig_ określa sposób wykonywania eksperymentu uczenia Maszynowego Azure. Można skonfigurować sposób wykonywania takich jak Historia uruchomień śledzenia lub co obliczeniowe docelowych do użycia oraz wiele innych. Nazwy plików konfiguracji uruchamiania są używane do wypełnienia listy rozwijanej kontekstu wykonywania w aplikacji pulpitu Workbench uczenia Maszynowego Azure.

**ArgumentVector**: Ta sekcja określa skryptu do uruchomienia w ramach to wykonanie i Parametry skryptu. Na przykład, jeśli masz poniższy fragment kodu w sieci "<run configuration name>.runconfig" pliku 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"eksperymentu uczenia maszynowego az przesłać foo.runconfig"_ automatycznie uruchamia polecenie z _myscript.py_ pliku przekazując 234 jako parametr i zestawów — pełne flagę.

**Docelowy**: ten parametr jest nazwą _.compute_ pliku _runconfig_ pliku odwołania. Zwykle wskazuje _foo.compute_ plik, ale można ją edytować, wskaż pozycję elementu docelowego innego obliczeniowego.

**Zmienne środowiskowe**: Ta sekcja umożliwia użytkownikom Ustaw zmienne środowiskowe w ramach ich przebiegów. Użytkownik może określić zmiennych środowiskowych przy użyciu pary nazwa wartość w następującym formacie:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Te zmienne środowiskowe są dostępne w kodzie użytkownika. Na przykład ten kod języka Python Wyświetla zmienną środowiskową o nazwie "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: Ta właściwość określa, czy Workbench uczenia Maszynowego Azure powinna uruchomić sesję Spark do uruchomienia skryptu. Wartość domyślna to _PySpark_. Ustaw ją na _Python_ Jeśli nie używasz kodu PySpark, co może pomóc w uruchamianie zadania szybsze dzięki mniejsze koszty.

**CondaDependenciesFile**: Ta właściwość wskazuje plik, który określa zależności środowiska conda w *aml_config* folderu. Jeśli ustawiono _null_, wskazuje domyślną **conda_dependencies.yml** pliku.

**SparkDependenciesFile**: Ta właściwość wskazuje plik, który określa zależności Spark w **aml_config** folderu. Ma ustawioną wartość _null_ domyślnie i jego wskazuje domyślną **spark_dependencies.yml** pliku.

**PrepareEnvironment**: tej właściwości, gdy wartość _true_, informuje usługi eksperymenty, aby przygotować środowisko conda na podstawie współzależności conda określony jako część programu początkowego przebiegu. Ta właściwość jest efektywne tylko wtedy, gdy wykonanie środowisku Docker. To ustawienie nie ma znaczenia w przypadku korzystania z _lokalnego_ środowiska. 

**TrackedRun**: Ta flaga sygnały usługi eksperymenty umożliwia określenie, czy śledzenie przebiegu w Azure ML Workbench Uruchom infrastruktury historii. Wartość domyślna to _true_. 

**UseSampling**: _UseSampling_ Określa, czy aktywny przykładowych zestawów danych dla źródeł danych są używane do uruchomienia. Jeśli ustawiono _false_, źródła danych pozyskiwania i użyj pełne dane do odczytu z magazynu danych. Jeśli ustawiono _true_, aktywne przykłady są używane. Użytkownicy mogą używać ** DataSourceSettings "Aby określić, które określonych przykładowych zestawów danych do użycia, jeśli chcą, aby zastąpić aktywnej próbce. 

**DataSourceSettings**: Ta sekcja konfiguracji określa ustawienia źródła danych. W tej sekcji użytkownika określa, które istniejących przykładowych danych dla źródła danych jest używany jako część uruchomienia. 

Określa następujące ustawienia konfiguracji tej próbki o nazwie "MySample" jest używana dla źródła danych o nazwie "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: podstawień źródła danych można używać, gdy użytkownik chce przełączyć się z jednego źródła danych na inny bez zmiany ich kodu. Na przykład użytkownicy mogą przełączać się z próbkowany w dół, lokalny plik do oryginalnej, większej zestawu danych przechowywanych w obiekcie Blob Azure, zmieniając odwołania do źródła danych. W przypadku podstawienia Azure ML Workbench uruchamia pakiety przygotowywania danych oraz źródeł danych, na których odwołuje się do źródła danych zastępczych.

Poniższy przykład zastępuje odwołania "mylocal.datasource" w źródłach danych uczenie Maszynowe Azure i pakietów przygotowywania danych "myremote.dsource". 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Oparte na zastąpienie powyżej, w poniższym przykładzie kodu teraz odczytuje z "myremote.dsource" zamiast "mylocal.dsource" bez użytkowników zmiana ich kodu.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [konfiguracji usługi eksperymenty](experimentation-service-configuration.md).
