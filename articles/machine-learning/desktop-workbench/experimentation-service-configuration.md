---
title: Konfigurowanie usługi Azure Machine Learning eksperymenty usługi | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie usługi Azure Machine Learning eksperymenty usługi instrukcje dotyczące sposobu konfigurowania go.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 25f51618e906c2993ebf02795b9a50d4d7a0b464
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Konfigurowanie usługi Azure Machine Learning eksperymenty usługi

## <a name="overview"></a>Przegląd
Azure Machine Learning eksperymenty usługi umożliwia danych służące do wykonywania eksperymentów, ich przy użyciu usługi Azure Machine Learning wykonywania i uruchom możliwości zarządzania. Zapewnia to struktura agile eksperymenty z szybkiego iteracji. Azure Machine Learning Workbench służy do uruchomienia z lokalnym działa na komputerze, a także łatwe ścieżki skalowania w górę i innych środowiskach, takich jak zdalny maszyn wirtualnych nauki danych z procesora GPU lub systemem Spark w usłudze Hdinsight.

Usługa eksperymenty zaprojektowano pod kątem dostarczanie izolowanym, powtarzalnych i spójne uruchomień eksperymentów. Pomaga zarządzać celów obliczeń środowisk wykonywania i uruchom konfiguracje. Przy użyciu usługi Azure Machine Learning Workbench wykonywania i możliwości zarządzania wykonywania, można łatwo przenosić między różnych środowiskach. 

Można wykonać skryptu Python lub PySpark w projekcie Workbench lokalnie lub na dużą skalę w chmurze. 

Skrypty można uruchomić na: 

* Środowisko Python (3.5.2) na komputerze lokalnym, instalowane przez Workbench
* Środowisko Conda Python wewnątrz kontenera Docker na komputerze lokalnym
* W środowisku Python, który należy do użytkownika i zarządzanie nimi na zdalnym komputerze systemu Linux
* Środowisko Conda Python wewnątrz kontenera Docker na zdalnym komputerze systemu Linux. Na przykład () [systemem Ubuntu DSVM na platformie Azure]https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight dla Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) na platformie Azure

>[!IMPORTANT]
>Azure Machine Learning eksperymenty usługi obecnie obsługuje Python 3.5.2 i Spark 2.1.11 jako wersje środowiska uruchomieniowego języka Python i Spark, odpowiednio. 


### <a name="key-concepts-in-experimentation-service"></a>Podstawowe pojęcia dotyczące usługi eksperymenty
Należy zrozumieć następujące kwestie eksperymentu uczenia maszynowego Azure podczas wykonywania. W kolejnych sekcjach omówiono sposób używania tych pojęć szczegółowo. 

#### <a name="compute-target"></a>Obliczenia bazy danych docelowych
A _obliczeniowe docelowej_ Określa, gdzie do wykonania programu, takie jak rozwiązania Docker pulpitu, zdalny na maszynie Wirtualnej lub w klastrze. Element docelowy obliczeń należy adresowanego i jest dostępny przez użytkownika. Workbench daje możliwość tworzenia celów obliczeniowych i zarządzać nimi przy użyciu aplikacji Workbench i interfejsu wiersza polecenia. 

_Dołącz az ml computetarget_ polecenia interfejsu wiersza polecenia umożliwia tworzenie docelowego obliczeń, który można używać w sekwencji.

Obiekty docelowe obliczeniowe obsługiwane są następujące:
* Lokalne środowisko Python (3.5.2) na komputerze, który został zainstalowany przez Workbench.
* Docker lokalnego na komputerze
* Zarządzana przez użytkownika, środowiska Python w zdalnym Linux Ubuntu maszyn wirtualnych. Na przykład [systemem Ubuntu DSVM na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Zdalne Docker na maszynach wirtualnych Linux Ubuntu. Na przykład [systemem Ubuntu DSVM na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight dla klastra Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) na platformie Azure

Usługa eksperymenty obecnie obsługuje Python 3.5.2 i Spark 2.1.11 jako Python i odpowiednio Spark wersji środowiska wykonawczego. 

>[!IMPORTANT]
> Uruchamianie maszyn wirtualnych systemu Windows są Docker **nie** obsługiwane jako miejsca docelowe zdalnego obliczeń.

#### <a name="execution-environment"></a>Środowiska wykonawczego
_Środowiska wykonawczego_ definiuje konfigurację czas wykonywania i zależności niezbędne do uruchomienia programu w Workbench.

Możesz zarządzać lokalne środowisko wykonawcze przy użyciu ulubionych narzędzi i menedżerów pakietu, jeśli używasz środowiska uruchomieniowego domyślne Workbench. 

Conda służy do zarządzania Docker lokalnego i zdalnego wykonaniami Docker, a także wykonaniami opartej na usłudze HDInsight. Dla tych zasobów obliczeniowych elementów docelowych, wykonanie konfiguracji środowiska jest zarządzana za pomocą **Conda_dependencies.yml** i **pliki Spark_dependencies.yml**. Te pliki znajdują się w **aml_config** folder wewnątrz projektu.

**Obsługiwanych środowisk uruchomieniowych w środowiskach wykonywania są:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Uruchom konfigurację
Oprócz środowiska docelowego i wykonywanie obliczeń, usługi Azure Machine Learning zapewnia platformę do definiowania i zmienić *Uruchom konfiguracje*. Różnych wykonaniami eksperymentu mogą wymagać różnych konfiguracji jako część eksperymenty iteracji. Możesz może być zmiennymi zakresy innym parametrem przy użyciu różnych źródeł danych i dostrajanie parametrów spark. Usługa eksperymenty zapewnia platformę do zarządzania konfiguracjami wykonywania.

Uruchomiona _dołączyć az ml computetarget_ polecenie tworzy dwa pliki w Twojej **aml_config** folder w projekcie: ".compute" i ".runconfig" po tę Konwencję: _< your_ computetarget_name > .compute_ i _.runconfig < your_computetarget_name >_. Plik .runconfig automatycznie jest tworzony dla Twojej wygody, podczas tworzenia obiektu docelowego obliczeń. Można utworzyć i zarządzać nimi inne konfiguracje wykonywania przy użyciu _az ml runconfigurations_ w interfejsu wiersza polecenia. Można również tworzyć i edytować je w systemie plików.

Konfigurację uruchomieniową w Workbench można również określić zmiennych środowiskowych. Można określić zmienne środowiskowe i ich używać w kodzie, dodając w poniższej sekcji w pliku .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Te zmienne środowiskowe są dostępne w kodzie. Na przykład następujący fragment kodu phyton Wyświetla zmienną środowiskową o nazwie "EXAMPLE_ENV_VAR1"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Na poniższej ilustracji przedstawiono ogólny przepływ dla początkowego uruchomienie eksperymentu.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scenariusze wykonywania eksperymentu
W tej sekcji możemy Poznaj scenariuszy wykonywania i Dowiedz się więcej o usłudze Azure Machine Learning działania eksperymenty, w szczególności uruchomienie eksperymentu lokalnie, na zdalnej maszynie Wirtualnej, a w klastrze usługi HDInsight. Ta sekcja ma wskazówki, zaczynając od utworzyć cel obliczeń do wykonywania eksperymentów.

>[!NOTE]
>W dalszej części tego artykułu użyto polecenia interfejsu wiersza polecenia (interfejsu wiersza polecenia) do wyświetlenia pojęć i funkcji. Funkcje opisane w tym miejscu można również ze środowiska roboczego.

## <a name="launching-the-cli"></a>Uruchamianie interfejsu wiersza polecenia
Prosty sposób uruchamiania interfejsu wiersza polecenia jest otwarcie projektu w Workbench i przechodząc do **Plik--> Otwórz okno wiersza polecenia**.

![](media/experimentation-service-configuration/opening-cli.png)

To polecenie uruchamia okno terminalu, w którym należy wprowadzić poleceń do wykonania skryptów w folderze bieżącego projektu. To okno terminalu skonfigurowano środowisku Python 3.5.2, który jest instalowany przez Workbench.

>[!NOTE]
> Podczas wykonywania żadnego _az ml_ polecenie w oknie polecenia muszą być uwierzytelniani Azure. Interfejsu wiersza polecenia używa pamięci podręcznej niezależne uwierzytelniania, a następnie aplikacja klasyczna oraz sposób logowanie się do środowiska roboczego nie oznacza, że użytkownik jest uwierzytelniony w danym środowisku interfejsu wiersza polecenia. Na potrzeby uwierzytelniania, należy korzystać z następujących czynności. Token uwierzytelniania są buforowane lokalnie w danym okresie czasu, dlatego trzeba Powtórz te czynności, po wygaśnięciu tokenu. Po wygaśnięciu tokenu lub pojawia się błędy uwierzytelniania, wykonaj następujące polecenia:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Po uruchomieniu _az ml_ polecenia w folderze projektu, upewnij się, że projekt należy do konta usługi Azure Machine Learning eksperymenty na _bieżącego_ subskrypcji platformy Azure. W przeciwnym razie mogą wystąpić błędy wykonania.


## <a name="running-scripts-and-experiments"></a>Uruchamianie skryptów i eksperymenty
Z Workbench, można wykonać programu Python i skrypty PySpark o różnych obliczeniowe obiektów docelowych za pomocą _przesłać eksperymentu uczenia maszynowego az_ polecenia. To polecenie wymaga definicji konfiguracji uruchamiania. 

Workbench tworzy plik runconfig utworzyć cel obliczeń, ale można utworzyć dodatkowe konfiguracje wykonywania przy użyciu _az ml runconfiguration utworzyć_ polecenia. Można też ręcznie modyfikować pliki konfiguracyjne wykonywania.

Konfiguracje wykonywania wyświetlane jako część eksperymentu uruchomienie środowisko w narzędzia Workbench. 

>[!NOTE]
>Dowiedz się więcej o plik konfiguracji uruchomienia w [wykonywania eksperymentów z informacjami o konfiguracji](experimentation-service-configuration-reference.md) sekcji.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Uruchomienie skryptu lokalnie na zainstalowane Workbench środowiska wykonawczego
Workbench umożliwia uruchamianie skryptów bezpośrednio przed środowiska uruchomieniowego języka Python zainstalowany Workbench 3.5.2. To środowisko uruchomieniowe domyślny jest zainstalowany w czasie konfigurowania Workbench i zawiera bibliotek usługi Azure Machine Learning i zależności. Wyniki uruchomienia i artefaktów dla lokalnych wykonaniami nadal są zapisywane w Uruchom historii usługi w chmurze.

W przeciwieństwie do opartych na Docker wykonaniami ta konfiguracja jest _nie_ zarządza Conda. Należy ręcznie Zapewnij zależności pakietów dla środowiska Workbench Python lokalnego.

Można wykonywać następujące polecenie, aby uruchomić skrypt lokalnie w środowisku Python zainstalowany Workbench. 

```
$az ml experiment submit -c local myscript.py
```

Ścieżka do domyślnego środowiska Python można znaleźć, wpisując następujące polecenie w oknie Workbench interfejsu wiersza polecenia.
```
$ conda env list
```

>[!NOTE]
>Uruchamianie lokalnie PySpark bezpośrednio przed Spark lokalnych środowisk jest obecnie **nie** obsługiwane. Workbench obsługuje skrypty PySpark uruchomiona na lokalnym Docker. Azure Machine Learning podstawowy obraz Docker jest dostarczany z preinstalowanym 2.1.11 Spark. 

_**Omówienie lokalne wykonanie skryptu Python:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Uruchomienie skryptu na lokalnym Docker
Można również uruchomić swoje projekty w kontenerze Docker na komputerze lokalnym za pośrednictwem usługi eksperymenty. Podstawowy obraz Docker, które pochodzą z biblioteki usługi Azure Machine Learning, a także udostępnia narzędzia Workbench jako Spark 2.1.11 środowiska uruchomieniowego, aby ułatwić Spark lokalnego wykonania. Docker musi już być uruchomione na komputerze lokalnym.

Do uruchamiania skryptu Python lub PySpark na Docker lokalny, można wykonywać następujące polecenia w interfejsu wiersza polecenia.

```
$az ml experiment submit -c docker myscript.py
```
lub
```
az ml experiment submit --run-configuration docker myscript.py
```

Środowiska wykonawczego w lokalnym Docker jest przygotowany przy użyciu usługi Azure Machine Learning obrazu podstawowego Docker. Gdy uruchomiony po raz pierwszy i nakładki go z pakietów określony w pliku conda_dependencies.yml, Workbench pobiera ten obraz. Ta operacja powoduje wstępnego uruchamiania wolniej, ale kolejnych uruchomieniach są znacznie szybciej dzięki użyciu Workbench ponowne użycie warstw pamięci podręcznej. 

>[!IMPORTANT]
>Musisz uruchomić _eksperymentu uczenia maszynowego az przygotowanie docker - c_ polecenie, aby przygotować obraz Docker do Twojego pierwszego uruchomienia. Można również ustawić **PrepareEnvironment** parametru na wartość true w pliku docker.runconfig. Ta akcja przygotowuje automatycznie środowiska w ramach działania uruchamiania.  

>[!NOTE]
>W przypadku uruchamiania skryptu PySpark Spark, spark_dependencies.yml jest również używany oprócz conda_dependencies.yml.

Uruchamianie skryptów w obrazie Docker daje następujące korzyści:

1. Zapewnia skrypt może być niezawodnie wykonywana w innych środowiskach wykonywania. Uruchomione w kontenerze Docker pomaga wykrywać oraz uniknąć wszelkie odwołania lokalnych, które mogą mieć wpływ przenoszenia. 

2. Pozwala na szybkie testowania kodu w środowiskach uruchomieniowych i platform, skomplikowane, aby zainstalować i skonfigurować, takich jak Apache Spark, bez konieczności instalowania samodzielnie.


_**Omówienie lokalne Docker wykonanie skryptu Python:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Uruchomienie skryptu na zdalnym Docker
W niektórych przypadkach zasobów dostępnych na komputerze lokalnym nie może być wystarczająca do uczenia modelu żądany. W takiej sytuacji usługa eksperymenty umożliwia w prosty sposób uruchamiania skryptów języka Python lub PySpark na maszynach wirtualnych bardziej zaawansowanych przy użyciu zdalnego wykonywania Docker. 

Zdalnego maszyny Wirtualnej powinny spełniać następujące wymagania:
* Zdalnego maszyny Wirtualnej musi być uruchomiona, Linux Ubuntu i powinien być dostępny za pośrednictwem protokołu SSH. 
* Zdalnego maszyny Wirtualnej musi mieć Docker systemem.

>[!IMPORTANT]
> Uruchamianie maszyn wirtualnych systemu Windows jest Docker **nie** obsługiwane jako miejsca docelowe zdalnego obliczeń


Następujące polecenie służy do tworzenia zarówno definicję docelowego obliczeń i uruchamiania zdalnego wykonaniami Docker na podstawie konfiguracji.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Po skonfigurowaniu docelowego obliczeń służy następujące polecenie do uruchomienia skryptu.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Należy pamiętać, że wykonanie środowisko jest skonfigurowane przy użyciu specyfikacji w conda_dependencies.yml. spark_dependencies.yml również zostanie użyty, jeśli w pliku .runconfig określono PySpark framework. 

Proces konstrukcji Docker zdalnego maszyn wirtualnych jest dokładnie taka sama jak proces lokalny Docker działa, więc należy oczekiwać podobne możliwości wykonywania.

>[!TIP]
>Jeśli wolisz uniknąć opóźnienia wynikające z tworzenia obrazu Docker dla Twojego pierwszego uruchomienia służy następujące polecenie przygotować docelowy obliczeń przed wykonaniem skryptu. eksperymentu uczenia maszynowego az przygotowanie remotedocker - c

_**Omówienie zdalnego maszyny wirtualnej wykonanie skryptu Python:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Uruchomienie skryptu na maszynie Wirtualnej zdalnego przeznaczonych dla środowisk zarządzana przez użytkownika
Usługa eksperymenty obsługuje również uruchamiania skryptu środowiska Python przez użytkownika wewnątrz maszyny wirtualnej systemu Ubuntu zdalnego. Dzięki temu można zarządzać środowiska do wykonania i nadal korzystać z możliwości usługi Azure Machine Learning. 

Wykonaj poniższe kroki, aby uruchomić skrypt w środowisku.
* Przygotowanie środowiska Python w zdalnej maszyny Wirtualnej systemu Ubuntu lub DSVM, instalowanie zależności.
* Zainstaluj wymagania dotyczące usługi Azure Machine Learning przy użyciu następującego polecenia.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>W niektórych przypadkach może być konieczne uruchomienie tego polecenia w trybie sudo w zależności od Twoje uprawnienia. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Użyj następującego polecenia, aby utworzyć definicja docelowego obliczeń i konfigurację uruchomieniową dla przebiegów zarządzana przez użytkownika na zdalnym wykonaniami maszyny Wirtualnej.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>Spowoduje to ustawienie parametru "userManagedEnvironment" w pliku konfiguracyjnym .compute na wartość true.

* Ustaw lokalizację Twoje środowisko uruchomieniowe języka Python pliku wykonywalnego w pliku .compute. Należy zapoznać się do pełnej ścieżki programu wykonywalnego python. 
```
pythonLocation: python3
```

Po skonfigurowaniu docelowego obliczeń służy następujące polecenie do uruchomienia skryptu.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> Po uruchomieniu na DSVM, należy użyć następujących poleceń

Jeśli chcesz uruchamiać bezpośrednio na DSVM w środowisku python globalnych, uruchom to polecenie.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Uruchamianie skryptu w klastrze usługi HDInsight
HDInsight to platforma popularnych analizy danych big data obsługi Apache Spark. Workbench umożliwia eksperymenty danych big Data z użyciem klastrów HDInsight Spark. 

>[!NOTE]
>Klaster usługi HDInsight musi używać usługi Azure Blob Storage jako magazynu głównego. Korzystanie z magazynu usługi Azure Data Lake nie jest jeszcze obsługiwane.

Można utworzyć cel obliczeń i uruchomić konfigurację klastra Spark w usłudze HDInsight przy użyciu następującego polecenia:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Jeśli używasz FQDN zamiast adresu IP i nosi nazwę klastra HDI Spark _foo_, punkt końcowy SSH jest w węźle sterownik o nazwie _foo-ssh.azurehdinsight.net_. Nie zapomnij **-ssh** przyrostka w polu Nazwa serwera, używając nazwy FQDN dla _— adres_ parametru.


Po utworzeniu kontekstu obliczeń, można uruchomić następujące polecenie do wykonania skryptu PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench przygotowuje i zarządza środowiska wykonawczego w klastrze usługi HDInsight przy użyciu Conda. Konfiguracja jest zarządzana przez _conda_dependencies.yml_ i _spark_dependencies.yml_ plików konfiguracyjnych. 

Aby wykonać eksperymenty w tym trybie potrzebny jest dostęp SSH w klastrze usługi HDInsight. 

>[!NOTE]
>Obsługiwana konfiguracja jest uruchomione systemu Linux (Ubuntu Python/PySpark 3.5.2 i Spark 2.1.11) klastry HDInsight Spark.

_**Omówienie usługi HDInsight opartej na wykonanie skryptu PySpark**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Uruchomienie skryptu na procesor GPU
Aby uruchomić skrypty procesora GPU, można postępuj zgodnie ze wskazówkami w tym artykule:[sposób użycia procesora GPU w usłudze Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Przy użyciu uwierzytelniania za pomocą klucza SSH do tworzenia i przy użyciu obliczeniowych elementów docelowych
Azure Machine Learning Workbench umożliwia tworzenie i używanie obliczeniowych elementów docelowych przy użyciu uwierzytelniania opartego na klucz SSH, oprócz systemu opartego na nazwę użytkownika/hasło. Podczas korzystania z remotedocker lub klastra jako urządzenie docelowe obliczeń, można użyć tej funkcji. Korzystając z tego systemu, Workbench tworzy pary kluczy publiczny/prywatny i raportuje ją klucza publicznego. Klucz publiczny można dołączyć do plików ~/.ssh/authorized_keys dla nazwy użytkownika. Azure Machine Learning Workbench, a następnie używa ssh uwierzytelniania opartego na kluczach do uzyskiwania dostępu i wykonywania dla tego elementu docelowego obliczeń. Ponieważ klucz prywatny dla elementu docelowego obliczeń zostanie zapisany w magazynie kluczy dla obszaru roboczego, innych użytkowników obszaru roboczego może użyć docelowej obliczeń tak samo zapewniając podana dla tworzenia obiektu docelowego obliczeń nazwa użytkownika.  

Wykonujesz te kroki, aby używać tej funkcji. 

- Utwórz element docelowy obliczeń przy użyciu jednej z następujących poleceń.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
lub
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Dołącz klucz publiczny, generowane przez narzędzia Workbench do pliku ~/.ssh/authorized_keys w celu dołączone obliczeń. 

>[!IMPORTANT]
>Musisz zalogować się na elemencie docelowym obliczeń przy użyciu tej samej nazwy użytkownika, używanego do tworzenia docelowego obliczeń. 

- Możesz teraz przygotowanie i użyć docelowej obliczeń przy użyciu uwierzytelniania opartego na kluczach SSH.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz i zainstaluj usługi Azure Machine Learning](../service/quickstart-installation.md)
* [Model administracyjny](model-management-overview.md)
