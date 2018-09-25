---
title: Pliki konfiguracji usługi platformy Azure usługa eksperymentowanie w usłudze Machine Learning
description: W tym dokumencie przedstawiono ustawienia konfiguracji dla usługi eksperymentowanie w usłudze uczenie Maszynowe Azure.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ROBOTS: NOINDEX
ms.openlocfilehash: abce80528479ba180783dbab604d4c836ddb7f1e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950781"
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Pliki konfiguracji usługi platformy Azure usługa eksperymentowanie w usłudze Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Po uruchomieniu skryptu w usłudze Azure Machine Learning (uczenie Maszynowe Azure) w aplikacji Workbench zachowanie wykonywania jest kontrolowany przez pliki w **aml_config** folderu. Ten folder znajduje się w katalogu głównym folderu projektu. Należy zrozumieć zawartość tych plików, aby osiągnąć żądany wynik wykonywanie w optymalny sposób.

Poniżej przedstawiono odpowiednie pliki w tym folderze:
- conda_dependencies.yml
- spark_dependencies.yml
- obliczenia pliki docelowe
    - \<Nazwa docelowego obliczeniowego > .compute
- uruchamianie plików konfiguracji
    - \<Nazwa konfiguracji uruchamiania > .runconfig

>[!NOTE]
>Zazwyczaj mają plik docelowy obliczeń i plik konfiguracji dla każdego obiektu docelowego obliczeń, które można utworzyć przebiegu. Można jednak niezależnie tworzenia tych plików i wielu plików konfiguracji uruchamiania, wskazując tej samej wartości docelowej obliczeń.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Ten plik jest [plikiem środowiska conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) , który określa wersja środowiska uruchomieniowego języka Python i pakietów, które zależy od kodu. W przypadku aplikacji Azure ML Workbench jest wykonywany skrypt w kontenerze platformy Docker lub klaster HDInsight, tworzy [środowiska conda](https://conda.io/docs/using/envs.html) skryptu do uruchomienia na. 

W tym pliku należy określić pakiety języka Python, wymaganych przez skrypt do wykonania. Usługa eksperymentowanie w usłudze Azure ML tworzy środowiska conda zgodnie z listy zależności. Pakiety wymienione w tym miejscu musi być osiągalna przez aparat wykonywania kanałami, takich jak:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* publicznie dostępnym punkcie końcowym (URL)
* lub ścieżka do pliku lokalnego
* inne dostępne przez aparat wykonywania

>[!NOTE]
>Podczas uruchamiania w klastrze HDInsight, Azure ML Workbench tworzy środowiska conda, dla określonego przebieg. Dzięki temu różnych użytkowników do uruchamiania na środowiska python różne, w tym samym klastrze.  

Oto przykład typowej **conda_dependencies.yml** pliku.
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
     
     # a wheel file available locally on disk (this only works if you are executing against local Docker target)
     - C:\temp\my_private_python_pkg.whl
```

Usługa Azure ML Workbench używa tego samego środowiska conda bez ponownie skompilować tak długo, jak **conda_dependencies.yml** pozostają bez zmian. Będzie go odbudować środowiska, w przypadku zmiany zależności.

>[!NOTE]
>Jeśli platformą docelową jest program miało zostać wykonane _lokalnego_ kontekstu, obliczeniowego **conda_dependencies.yml** plik jest **nie** używane. Zależności pakietów dla środowiska lokalnego usługi Azure ML Workbench środowiska Python, należy zainstalować ręcznie.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Ten plik Określa nazwę aplikacji, Spark, podczas przesyłania skryptu PySpark i pakietów platformy Spark, które muszą zostać zainstalowane. Można również określić publicznego repozytorium narzędzia Maven, a także pakietów platformy Spark, które znajdują się w tych repozytoriów narzędzia Maven.

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
>Klastra dostrajanie parametrów, takich jak rozmiar procesu roboczego i rdzeni powinien przejść do sekcji "Konfiguracja" w pliku spark_dependecies.yml 

>[!NOTE]
>Jeśli skrypt są wykonywane w środowisku Python *spark_dependencies.yml* plik zostanie zignorowany. Jest używany tylko wtedy, gdy używasz Spark (albo na platformy Docker lub w klastrze HDInsight).

## <a name="run-configuration"></a>Uruchom konfigurację
Aby określić określonej konfiguracji uruchamiania, należy oraz plik .compute .runconfig. Te są zwykle generowane przy użyciu wiersza polecenia. Można również sklonować te zakończone, zmienić ich nazwy i je edytować.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

To polecenie tworzy parę plików w oparciu o określone obliczeniowego elementu docelowego. Załóżmy, że nazwa obliczeniowego elementu docelowego _foo_. To polecenie spowoduje wygenerowanie _foo.compute_ i _foo.runconfig_ w swojej **aml_config** folderu.

>[!NOTE]
> _lokalne_ lub _docker_ nazwy dla dowolnego są pliki konfiguracji uruchamiania. Usługa Azure ML Workbench dodaje te dwa konfiguracji uruchamiania po utworzeniu pustego projektu dla Twojej wygody. Można zmienić nazwy "<run configuration name>.runconfig" pliki, które pochodzą z szablonem projektu lub Utwórz nowe z dowolną nazwę.

### <a name="compute-target-namecompute"></a>\<Nazwa docelowego obliczeniowego > .compute
_\<Nazwa docelowego obliczeniowego > .compute_ plik Określa połączenia i informacje dotyczące konfiguracji dla obliczeniowego elementu docelowego. Jest to Lista par nazwa wartość. Poniżej przedstawiono obsługiwane ustawienia:

**Typ**: Typ środowiska obliczeniowego. Obsługiwane są następujące wartości:
  - lokalne
  - Zdalne
  - Platformy docker
  - remotedocker
  - klaster

**baseDockerImage**: obrazu platformy Docker, który służy do uruchomienia skryptu języka Python/PySpark. Wartość domyślna to _microsoft/mmlspark:plus-0.7.91_. Obsługujemy również innego obrazu: _microsoft/mmlspark:plus-gpu-0.7.91_, który umożliwia procesora GPU dostęp do komputera hosta (jeśli występuje procesora GPU).

**adres**: adres IP lub nazwę FQDN (w pełni kwalifikowana nazwa domeny) maszyny wirtualnej lub HDInsight cluster węzeł główny.

**Nazwa użytkownika**: nazwa użytkownika SSH do uzyskiwania dostępu do maszyny wirtualnej lub węzeł główny HDInsight.

**hasło**: zaszyfrowane hasło dla połączenia SSH.

**sharedVolumes**: flagi sygnalizują silnika wykonywania powinien korzystać z aparatu Docker udostępniony wolumin funkcji do wysłania plików projektu i z powrotem. Posiadanie tej flagi włączone można przyspieszyć wykonywanie ponieważ Docker mogą uzyskiwać dostęp do projektów bezpośrednio, bez potrzeby kopiowania ich. Najlepiej ustawić _false_ czy aparat platformy Docker jest uruchomiona na Windows od udostępnianie woluminów dla aparatu Docker na Windows może być niestabilne. Ustaw ją na _true_ jeśli działa on w systemie macOS lub Linux.

**nvidiaDocker**: tej flagi, gdy wartość _true_, informuje usługi eksperymentowanie w usłudze Azure ML w celu używania _nvidia docker_ polecenia, w przeciwieństwie do zwykłych _docker_polecenia do uruchomienia obrazu platformy Docker. _Nvidia docker_ aparatu umożliwia kontenera Docker, aby dostęp sprzęcie procesora GPU. To ustawienie jest wymagany, jeśli chcesz uruchomić wykonania przez GPU w kontenerze platformy Docker. Obsługuje tylko hoście z systemem Linux _nvidia docker_. Na przykład DSVM opartą na systemie Linux na platformie Azure jest dostarczany z _nvidia docker_. _procesory GPU NVIDIA docker_ aktualnie nie jest obsługiwana na Windows.

**nativeSharedDirectory**: Ta właściwość określa katalog podstawowy (na przykład: _~/.azureml/share/_) gdzie pliki można zapisywać w celu można współdzielić w ramach działa w tej samej wartości docelowej obliczeń. Jeśli to ustawienie jest używane podczas uruchamiania w kontenerze platformy Docker _sharedVolumes_ musi być ustawiona na wartość true. W przeciwnym razie wykonanie nie powiedzie się.

**userManagedEnvironment**: Ta właściwość określa, czy ten cel obliczenia jest zarządzany przez użytkownika bezpośrednio lub za pośrednictwem usługi eksperymentowanie w usłudze.  

**pythonLocation**: Ta właściwość określa lokalizację środowiska uruchomieniowego python, które ma być używany w elemencie docelowym obliczeń do wykonania programu użytkownika. 

### <a name="run-configuration-namerunconfig"></a>\<Nazwa konfiguracji uruchamiania > .runconfig
_\<Nazwa konfiguracji uruchamiania > .runconfig_ określa usługi Azure ML eksperymentować zachowanie wykonywania. Można skonfigurować zachowanie wykonania, takie jak śledzenie historii uruchamiania lub co obliczeniowego elementu docelowego, aby użyć wraz z wielu innych. Nazwy plików konfiguracji uruchamiania są używane do wypełnienia listy rozwijanej kontekstu wykonywania w aplikacji klasycznej aplikacji Azure ML Workbench.

**ArgumentVector**: w tej sekcji Określa skrypt do uruchomienia w ramach tego wykonywania i Parametry skryptu. Na przykład, jeśli masz następujący fragment kodu w swojej "<run configuration name>.runconfig" pliku 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml eksperymentu Prześlij foo.runconfig"_ automatycznie uruchamia polecenie za pomocą _myscript.py_ pliku jako parametru, jak i zestawy przekazując 234--verbose flagę.

**Docelowy**: ten parametr jest nazwą _.compute_ pliku, który _runconfig_ pliku odwołania. Zazwyczaj wskazuje _foo.compute_ plik, ale można edytować to ustawienie, aby innego obliczeniowego elementu docelowego.

**Zmienne środowiskowe**: Ta sekcja umożliwia użytkownikom do ustawiania zmiennych środowiskowych w ramach ich uruchomienia. Użytkownik może określić zmiennych środowiskowych przy użyciu pary nazwa wartość w następującym formacie:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Te zmienne środowiskowe są dostępne w kodzie użytkownika. Na przykład ten kod Python drukuje zmienną środowiskową o nazwie "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: Ta właściwość określa, jeśli w aplikacji Azure ML Workbench powinien być uruchamiany sesja platformy Spark do uruchomienia skryptu. Wartość domyślna to _PySpark_. Ustaw ją na _Python_ Jeśli nie używasz kodu PySpark, co może pomóc uruchamiania zadania, korzystając z mniejsze obciążenie.

**CondaDependenciesFile**: Ta właściwość wskazuje plik, który określa zależności środowiska conda w *aml_config* folderu. Jeśli ustawiono _null_, wskazuje domyślną **conda_dependencies.yml** pliku.

**SparkDependenciesFile**: Ta właściwość wskazuje plik, który określa zależności platformy Spark w **aml_config** folderu. Jest równa _null_ domyślnie i jego wskazuje domyślną **spark_dependencies.yml** pliku.

**PrepareEnvironment**: tej właściwości, gdy wartość _true_, informuje usługa eksperymentowanie w usłudze w celu przygotowania środowiska conda, na podstawie zależności conda, określony jako część Twojego pierwszego uruchomienia. Ta właściwość jest efektywne tylko wtedy, gdy w przypadku wykonywania względem ze środowiska Docker. To ustawienie nie obowiązuje, jeśli używasz _lokalnego_ środowiska. 

**TrackedRun**: Ta flaga sygnalizuje usługa eksperymentowanie umożliwia określenie, czy śledzenie wykonywania w aplikacji Azure ML Workbench Uruchom infrastrukturę historii. Wartość domyślna to _true_. 

**UseSampling**: _UseSampling_ Określa, czy aktywna przykładowe zestawy danych dla źródła danych są używane do uruchomienia. Jeśli ustawiono _false_, źródła danych pozyskiwania i używać pełnych danych odczytu z magazynu danych. Jeśli ustawiono _true_, aktywne przykłady są używane. Użytkownicy mogą używać **DataSourceSettings** Aby określić, które określonych przykładowych zestawów danych do użycia, jeśli chcesz przesłonić aktywny przykładowe. 

**DataSourceSettings**: Ta sekcja konfiguracji określa ustawienia źródła danych. W tej sekcji użytkownik Określa, które istniejących danych przykład z określonego źródła danych jest używany w ramach przebiegu. 

Określa następujące ustawienia konfiguracji tej próbki o nazwie "MySample" jest używany dla źródła danych o nazwie "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: podstawienia źródła danych można użyć, gdy użytkownik chce, aby przełączyć się z jednego źródła danych do innego bez wprowadzania zmian w kodzie. Na przykład użytkownicy przełączyć się z pliku podczas próbkowania w dół, lokalny do oryginalnej, większy zestaw danych przechowywanych w usłudze Azure Blob, zmieniając odwołanie do źródła danych. W przypadku zastąpienia aplikacji Azure ML Workbench uruchamia Twoich źródeł danych i pakietów przygotowywania danych, odwołując się do źródła danych zastępuje.

Poniższy przykład zastępuje odwołania "mylocal.datasource" źródła danych usługi uczenie Maszynowe Azure i pakietów przygotowywania danych przy użyciu "myremote.dsource". 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Oparte na zastąpienie powyżej, poniższy przykładowy kod teraz odczytuje z "myremote.dsource" zamiast "mylocal.dsource" bez użytkowników, zmiana ich kodu.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [Konfiguracja usługi eksperymentowania](experimentation-service-configuration.md).
