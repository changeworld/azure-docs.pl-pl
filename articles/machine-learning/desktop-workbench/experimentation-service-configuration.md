---
title: Konfigurowanie usługi eksperymentowanie w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera ogólne omówienie usługi eksperymentowanie w usłudze Azure Machine Learning instrukcje dotyczące sposobu konfigurowania go.
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
ms.openlocfilehash: e79817ffad139e0a3bcb0ba32b9bc6e5666319d0
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644694"
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Konfigurowanie usługi eksperymentowanie w usłudze Azure Machine Learning

## <a name="overview"></a>Przegląd
Usługa eksperymentowanie w usłudze Azure Machine Learning umożliwia analitykom danych do wykonywania eksperymentów, ich przy użyciu wykonywania usługi Azure Machine Learning i do działania funkcji zarządzania. Zapewnia ramy agile eksperymentów przy użyciu szybkich iteracji. Usługa Azure Machine Learning Workbench umożliwia zaczynać lokalnego uruchomienia na komputerze, a także łatwo ścieżki do skalowania w górę i w poziomie do innych środowisk, takich jak zdalne maszyny wirtualne do nauki o danych z procesorem GPU lub klastry HDInsight platformy Spark.

Usługa eksperymentowanie w usłudze zaprojektowano zapewniające izolowane, powtarzalnych i spójne uruchomień eksperymenty. Ułatwia ona zarządzanie celów obliczeń, środowisk wykonawczych i uruchomionych konfiguracjach. Za pomocą wykonywania aplikacji Azure Machine Learning Workbench i możliwości zarządzania wykonywania, można łatwo przenosić między różnymi środowiskami. 

Można wykonać skryptu języka Python lub PySpark w projekcie aplikacji Workbench, lokalnie lub na dużą skalę w chmurze. 

Można uruchamiać skrypty: 

* Środowisko Python (3.5.2) na komputerze lokalnym, zainstalowany przez aplikację Workbench
* Środowisko Conda Python wewnątrz kontenera platformy Docker na komputerze lokalnym
* W środowisku Python, który należy do użytkownika i zarządzanie nimi na komputerze zdalnym z systemem Linux
* Środowisko Conda Python wewnątrz kontenera platformy Docker na komputerze zdalnym z systemem Linux. Na przykład () [DSVM oparta na systemie Ubuntu na platformie Azure]https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) na platformie Azure

>[!IMPORTANT]
>Usługa eksperymentowanie w usłudze Azure Machine Learning aktualnie obsługuje język Python 3.5.2 i platformy Spark 2.1.11 jako wersje środowiska uruchomieniowego języka Python i platformy Spark, odpowiednio. 


### <a name="key-concepts-in-experimentation-service"></a>Podstawowe pojęcia dotyczące usługi eksperymentowanie
Należy zrozumieć następujące pojęcia wykonywania eksperymentu usługi Azure Machine Learning. W kolejnych sekcjach omówiono sposób używania tych pojęć szczegółowo. 

#### <a name="compute-target"></a>Obliczeniowego elementu docelowego
A _obliczeniowego elementu docelowego_ Określa, gdzie do wykonania programu, takich jak platforma Docker pulpitu, zdalne na maszynie Wirtualnej lub w klastrze. Obliczeniowego elementu docelowego musi być adresy i jest dostępny przez użytkownika. Środowisko robocze zapewnia możliwość tworzenia obliczeniowych elementów docelowych i zarządzanie nimi przy użyciu aplikacji Workbench i interfejs wiersza polecenia. 

_Dołącz az ml computetarget_ polecenia w interfejsie wiersza polecenia pozwala utworzyć cel obliczenia używanej w sekwencji.

Są obsługiwane obliczeniowych elementów docelowych:
* Lokalne środowisko Python (3.5.2) na komputerze, który został zainstalowany przez aplikację Workbench.
* Lokalne platformy Docker na komputerze
* Zarządzana przez użytkownika, środowiska Python na zdalnych maszynach wirtualnych z systemem Ubuntu Linux. Na przykład [DSVM oparta na systemie Ubuntu na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Zdalne platformy Docker na maszynach wirtualnych z systemem Ubuntu Linux. Na przykład [DSVM oparta na systemie Ubuntu na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight dla klastra Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) na platformie Azure

Usługa eksperymentowanie w usłudze obecnie obsługuje język Python 3.5.2 Spark 2.1.11 jako języka Python i platformy Spark wersje środowiska uruchomieniowego, odpowiednio. 

>[!IMPORTANT]
> Uruchamianie maszyn wirtualnych Windows są Docker **nie** obsługiwane jako zdalnego obliczeniowych elementów docelowych.

#### <a name="execution-environment"></a>Środowisko wykonawcze
_Środowiska wykonawczego_ definiuje konfiguracji w czasie wykonywania i zależności niezbędne do uruchomienia programu w aplikacji Workbench.

Możesz zarządzać środowiska lokalnego wykonania za pomocą swoje ulubione narzędzia i Menedżery pakietów, jeśli używasz domyślnego środowiska uruchomieniowego aplikacji Workbench. 

Conda służy do zarządzania Docker lokalnego i zdalnego wykonania platformy Docker, a także na podstawie HDInsight wykonań. Dla celów tych obliczeń, konfiguracji środowiska wykonywania odbywa się za pośrednictwem **Conda_dependencies.yml** i **pliki Spark_dependencies.yml**. Te pliki znajdują się w **aml_config** folder wewnątrz projektu.

**Są obsługiwane środowiska uruchomieniowe dla środowisk wykonawczych:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Uruchom konfigurację
Oprócz środowiska docelowego i wykonywanie obliczeń, Azure Machine Learning zapewnia platformę do definiowania i zmienić *uruchomionych konfiguracjach*. Różnymi wykonaniami eksperymentu mogą wymagać różnych konfiguracji jako część eksperymenty iteracyjne. Użytkownik może być sprawdzaniu zakresy różnych parametrów, przy użyciu różnych źródeł danych i dostrajanie parametrów platformy spark. Usługa eksperymentowanie w usłudze zapewnia platformę do zarządzania konfiguracjami wykonywania.

Uruchamianie _az ml computetarget dołączyć_ polecenie tworzy dwa pliki w Twojej **aml_config** folder w projekcie: ".compute" i ".runconfig" zgodnie z niniejszej Konwencji: _< your_ computetarget_name > .compute_ i _.runconfig < your_computetarget_name >_. Plik .runconfig automatycznie jest tworzony dla Twojej wygody, po utworzeniu obliczeniowego elementu docelowego. Można tworzyć i zarządzać inne konfiguracje wykonywania za pomocą _az ml runconfigurations_ polecenia w interfejsie wiersza polecenia. Można również tworzyć i edytować je w systemie plików.

Konfiguracji uruchamiania w aplikacji Workbench umożliwia również określanie zmiennych środowiskowych. Można określić zmienne środowiskowe i ich używać w kodzie, dodając następującą sekcję w pliku .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Te zmienne środowiskowe są dostępne w kodzie. Na przykład następujący fragment kodu phyton drukuje zmienną środowiskową o nazwie "EXAMPLE_ENV_VAR1"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Na poniższej ilustracji przedstawiono ogólny przepływ dla początkowej uruchomienie eksperymentu.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scenariusze wykonywania eksperymentów
W tej sekcji firma Microsoft od razu do realizacji scenariuszy i Dowiedz się więcej o usłudze Azure Machine Learning działania eksperymenty, w szczególności uruchamiania eksperymentu lokalnego na zdalnej maszynie Wirtualnej, a w klastrze usługi HDInsight. W tej sekcji znajduje się przewodnik począwszy od tworzenia do wykonywania eksperymentów obliczeniowego elementu docelowego.

>[!NOTE]
>W dalszej części tego artykułu użyto poleceń interfejsu wiersza polecenia (interfejsu wiersza polecenia) do wyświetlenia koncepcji i możliwości. Funkcje opisane w tym miejscu można również za pomocą aplikacji Workbench.

## <a name="launching-the-cli"></a>Uruchamianie interfejsu wiersza polecenia
Prosty sposób, aby uruchomić interfejs wiersza polecenia otwiera projekt w aplikacji Workbench i przechodząc do **Plik--> Otwórz wiersz polecenia**.

![](media/experimentation-service-configuration/opening-cli.png)

To polecenie uruchamia okno terminalu, w którym należy wprowadzić polecenia, aby wykonywać skrypty w bieżącym folderze projektu. To okno terminala skonfigurowano środowisko Python 3.5.2, w którym jest zainstalowany przez aplikację Workbench.

>[!NOTE]
> Podczas wykonywania dowolnej _az ml_ polecenia w oknie polecenia muszą być uwierzytelniani względem platformy Azure. Interfejs wiersza polecenia korzysta z pamięci podręcznej niezależnie od uwierzytelniania, a następnie aplikacja klasyczna, a więc logowanie do aplikacji Workbench nie oznacza, że użytkownik został uwierzytelniony w środowisku interfejsu wiersza polecenia. Na potrzeby uwierzytelniania, użyj następujących kroków. Token uwierzytelniania są buforowane lokalnie przez czas, dlatego należy powtórzyć te kroki po wygaśnięciu ważności tokenu. Po wygaśnięciu ważności tokenu lub Jeśli widzisz błędy uwierzytelniania, wykonaj następujące polecenia:

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
>Po uruchomieniu _az ml_ polecenia w folderze projektu, upewnij się, że projekt należy do konta eksperymentowanie w usłudze Machine Learning platformy Azure na _bieżącego_ subskrypcji platformy Azure. W przeciwnym razie mogą wystąpić błędy wykonania.


## <a name="running-scripts-and-experiments"></a>Uruchamianie skryptów i eksperymenty
Przy użyciu aplikacji Workbench, można wykonać języka Python oraz skrypty PySpark o różnych obliczeniowych elementów docelowych przy użyciu _przesyłanie eksperymentu uczenia maszynowego az_ polecenia. To polecenie wymaga definicji konfiguracji uruchamiania. 

Workbench tworzy odpowiedni plik runconfig, kiedy utworzyć cel obliczenia, ale można utworzyć dodatkowe konfiguracje wykonywania za pomocą _tworzenie az ml runconfiguration_ polecenia. Można także ręcznie edytować pliki konfiguracji uruchamiania.

Uruchom konfiguracje są wyświetlane jako część eksperymentu środowiska uruchamiania w aplikacji Workbench. 

>[!NOTE]
>Dowiedz się więcej na temat pliku konfiguracji uruchamiania w [informacje o konfiguracji wykonywania eksperymentów](experimentation-service-configuration-reference.md) sekcji.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Działania skryptu lokalnie zainstalowane środowisko robocze środowiska uruchomieniowego
Workbench umożliwia uruchamianie skryptów bezpośrednio w odniesieniu do środowiska uruchomieniowego zainstalowana aplikacja Workbench Python 3.5.2. To domyślne środowisko uruchomieniowe jest instalowany w czasie konfigurowania aplikacji Workbench i zawiera biblioteki usługi Azure Machine Learning i zależności. Wyniki przebiegu i artefaktów dla lokalnych wykonań nadal są zapisywane w Uruchom historii usługi w chmurze.

W przeciwieństwie do opartych na platformie Docker wykonań, ta konfiguracja jest _nie_ zarządzany przez narzędzia Conda. Należy ręcznie umożliwić zależności pakietów dla środowiska Workbench Python lokalnego.

Można wykonać następujące polecenie, aby uruchomić skrypt lokalnie w środowisku Python zainstalowane w aplikacji Workbench. 

```
$az ml experiment submit -c local myscript.py
```

Ścieżka do środowiska domyślnego języka Python można znaleźć, wpisując następujące polecenie w oknie interfejsu wiersza polecenia aplikacji Workbench.
```
$ conda env list
```

>[!NOTE]
>Uruchamianie lokalnie PySpark bezpośrednio w odniesieniu do lokalnych Spark środowisk jest obecnie **nie** obsługiwane. Środowisko robocze obsługuje skrypty PySpark uruchomione na lokalnych platformy Docker. Usługa Azure Machine Learning podstawowego obrazu platformy Docker jest dostarczany za pomocą platformy Spark 2.1.11 wstępnie zainstalowane. 

_**Omówienie lokalne wykonanie skryptu Python:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Uruchomienie skryptu na lokalnej platformy Docker
Można również uruchomić swoje projekty w kontenerze platformy Docker na komputerze lokalnym za pośrednictwem usługi eksperymentowanie w usłudze. Środowisko robocze zawiera podstawowy obraz platformy Docker, który jest dostarczany z bibliotek usługi Azure Machine Learning, a także jako Spark 2.1.11 środowiska uruchomieniowego, aby ułatwić Spark lokalnych wykonań. Platformy docker musi być uruchomiony na komputerze lokalnym.

Aby uruchomić skrypt języka Python lub PySpark na lokalnej platformy Docker, może wykonać następujące polecenia w interfejsie wiersza polecenia.

```
$az ml experiment submit -c docker myscript.py
```
lub
```
az ml experiment submit --run-configuration docker myscript.py
```

Środowisko wykonawcze w lokalnej platformy Docker jest gotowa, za pomocą usługi Azure Machine Learning podstawowego obrazu platformy Docker. Gdy uruchomiona po raz pierwszy i nakładki go za pomocą pakietów określona w pliku conda_dependencies.yml, Workbench pobiera ten obraz. Ta operacja powoduje, że początkowy wykonywania wolniej, ale kolejnych uruchomień są znacznie szybciej dzięki środowisko robocze ponownemu wykorzystaniu warstwy pamięci podręcznej. 

>[!IMPORTANT]
>Musisz uruchomić _eksperymentu uczenia maszynowego az przygotowanie docker - c_ polecenie, aby najpierw przygotować obraz platformy Docker do Twojego pierwszego uruchomienia. Można również ustawić **PrepareEnvironment** parametru na wartość true w pliku docker.runconfig. Ta akcja spowoduje automatyczne przygotowanie środowiska w ramach działania wykonywania.  

>[!NOTE]
>Jeśli skrypt PySpark działający na platformie Spark, spark_dependencies.yml jest również używany poza conda_dependencies.yml.

Uruchamianie skryptów w obrazie platformy Docker zapewnia następujące korzyści:

1. Zapewnia skryptu mogą być niezawodny sposób będą wykonywane w innych środowiskach wykonawczych. Uruchomione w kontenerze platformy Docker pomaga użytkownikom odnajdywanie ich i uniknąć wszystkie odwołania lokalnych, które mogą mieć wpływ na przenoszenia. 

2. Umożliwia ona szybko przetestować kod, środowisk wykonawczych i struktur, które są skomplikowane, aby zainstalować i skonfigurować, takie jak Apache Spark bez konieczności instalowania samodzielnie.


_**Omówienie lokalnych wykonań platformy Docker dla skryptu języka Python:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Uruchomienie skryptu na zdalne platformy Docker
W niektórych przypadkach zasobów dostępnych na komputer lokalny może nie być wystarczająca do nauczenia modelu żądaną. W takiej sytuacji usługa eksperymentowanie umożliwia łatwe uruchamianie skryptów języka Python lub PySpark na bardziej wydajne maszyny wirtualne przy użyciu zdalnego wykonania platformy Docker. 

Zdalnego maszyny Wirtualnej powinny spełniać następujące wymagania:
* Zdalnej maszynie Wirtualnej musi działać Linux Ubuntu i powinny być dostępne za pośrednictwem protokołu SSH. 
* Zdalnego maszyny Wirtualnej wymaga platformy Docker jest uruchomiona.

>[!IMPORTANT]
> Uruchamianie maszyn wirtualnych Windows, Docker jest **nie** obsługiwane jako zdalnego obliczeniowych elementów docelowych


Następujące polecenie służy do tworzenia obu definicji docelowej obliczeń i uruchamiania Konfiguracja zdalnego wykonania na platformie docker.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Po skonfigurowaniu obliczeniowego elementu docelowego, można użyć następującego polecenia, aby uruchomić skrypt.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Należy pamiętać, że wykonanie środowisko jest skonfigurowane przy użyciu specyfikacji w conda_dependencies.yml. spark_dependencies.yml jest też używana, jeśli w pliku .runconfig określono PySpark framework. 

Proces konstruowania platformy Docker na zdalnych maszynach wirtualnych jest dokładnie taka sama jak działa proces lokalnej platformy Docker, więc należy się spodziewać podobne możliwości wykonywania.

>[!TIP]
>Jeśli wolisz uniknąć opóźnienia wynikające z tworzenia obrazu platformy Docker dla Twoje pierwsze uruchomienie, można użyć następującego polecenia przygotować obliczeniowego elementu docelowego przed wykonaniem skryptu. eksperymentu uczenia maszynowego az przygotowanie remotedocker - c

_**Omówienie wykonywania zdalnego maszyny wirtualnej dla skryptu języka Python:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Uruchomienie skryptu na zdalnej maszynie Wirtualnej, przeznaczonych dla środowisk zarządzana przez użytkownika
Usługa eksperymentowanie w usłudze obsługuje również uruchomienie skryptu w środowisku Python firmy użytkownika wewnątrz zdalnego maszyny wirtualnej systemu Ubuntu. Dzięki temu można zarządzać środowiskiem do wykonania i nadal korzystać z możliwości usługi Azure Machine Learning. 

Wykonaj poniższe kroki, aby uruchomić skrypt we własnym środowisku.
* Przygotowywanie środowiska Python na zdalnej maszynie Wirtualnej z systemem Ubuntu lub maszyny wirtualnej DSVM, zainstalowanie zależności.
* Zainstaluj wymagania dotyczące usługi Azure Machine Learning, używając następującego polecenia.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>W niektórych przypadkach może być konieczne uruchomienie tego polecenia w trybie "sudo" w zależności od tego, Twoje uprawnienia. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Użyj następującego polecenia, aby utworzyć definicja docelowego obliczeń i konfiguracji uruchamiania dla przebiegów zarządzana przez użytkownika na zdalnym wykonań maszyny Wirtualnej.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>Spowoduje to ustawienie parametru "userManagedEnvironment" w pliku konfiguracyjnym .compute na wartość true.

* Ustaw lokalizację Twojego środowiska uruchomieniowego Python pliku wykonywalnego w pliku .compute. Należy zapoznać się do pełnej ścieżki pliku wykonywalnego języka python. 
```
pythonLocation: python3
```

Po skonfigurowaniu obliczeniowego elementu docelowego, można użyć następującego polecenia, aby uruchomić skrypt.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> Podczas korzystania z maszyny wirtualnej DSVM, należy użyć następujących poleceń

Jeśli chcesz uruchomić bezpośrednio w środowisku python globalnej firmy nauki, uruchom następujące polecenie.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Uruchamianie skryptu w klastrze usługi HDInsight
HDInsight to popularne platformy do analizy danych big data, obsługi platformy Apache Spark. Workbench umożliwia eksperymentowania o dane big data przy użyciu klastrów usługi HDInsight Spark. 

>[!NOTE]
>Klaster usługi HDInsight musi używać usługi Azure Blob Storage jako magazynu głównego. Korzystanie z magazynu usługi Azure Data Lake nie jest jeszcze obsługiwane.

Można utworzyć obliczeniowego elementu docelowego i Uruchom konfigurację klastra usługi HDInsight Spark przy użyciu następującego polecenia:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Jeśli używasz nazwy FQDN zamiast adresu IP i nosi nazwę klastra usługi HDI Spark _foo_, punkt końcowy SSH znajduje się na węzeł sterownika o nazwie _foo-ssh.azurehdinsight.net_. Nie zapomnij **-ssh** przyrostkowe nazwę serwera, korzystając z nazwy FQDN dla _— adres_ parametru.


Po utworzeniu kontekstu obliczeniowego, możesz uruchomić następujące polecenie, aby wykonać skrypt PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench przygotowuje i zarządza środowiska wykonawczego w klastrze HDInsight przy użyciu narzędzia Conda. Konfiguracja jest zarządzana przez _conda_dependencies.yml_ i _spark_dependencies.yml_ plików konfiguracyjnych. 

Należy dostęp protokołu SSH z klastrem HDInsight w celu wykonywania eksperymentów w tym trybie. 

>[!NOTE]
>HDInsight Spark clusters uruchomionej w systemie Linux (Ubuntu przy użyciu języka Python/PySpark 3.5.2 i platformy Spark 2.1.11) jest obsługiwana konfiguracja.

_**Omówienie systemem HDInsight wykonywania skryptu PySpark**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Uruchomienie skryptu na procesorze GPU
Aby uruchamiać skrypty GPU, możesz wykonać wskazówki zawarte w tym artykule:[sposób użycia procesora GPU w usłudze Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Przy użyciu uwierzytelniania opartego na kluczach SSH dotyczące tworzenia i używania obliczeniowych elementów docelowych
Usługa Azure Machine Learning Workbench umożliwia tworzenie i używanie obliczeniowych elementów docelowych przy użyciu uwierzytelniania opartego na kluczach SSH, oprócz nazwy użytkownika/na podstawie hasła schematu. Mogą używać tej funkcji, podczas używania remotedocker lub w klastrze jako obliczeniowego elementu docelowego. Korzystając z tego systemu, aplikacji Workbench tworzy parę kluczy publiczny/prywatny i raportuje ją klucza publicznego. Można dołączyć klucz publiczny do plików ~/.ssh/authorized_keys dla nazwy użytkownika. Usługa Azure Machine Learning Workbench następnie korzysta z protokołu ssh na podstawie klucza uwierzytelniania do uzyskiwania dostępu i wykonywania w tym celu obliczeń. Ponieważ klucz prywatny do obliczeniowego elementu docelowego jest zapisywany w magazynie kluczy dla obszaru roboczego, inni użytkownicy w obszarze roboczym użyć obliczeniowego elementu docelowego taki sam sposób, podając nazwę użytkownika, pod warunkiem do tworzenia obliczeniowego elementu docelowego.  

Wykonujesz te kroki, aby używać tej funkcji. 

- Utworzyć cel obliczenia przy użyciu jednej z następujących poleceń.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
lub
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Dołącz klucz publiczny, wygenerowany przez aplikację Workbench do pliku ~/.ssh/authorized_keys dołączonych obliczeniowego elementu docelowego. 

>[!IMPORTANT]
>Należy zalogować się w elemencie docelowym obliczeniowych, przy użyciu tej samej nazwy użytkownika, który został użyty do utworzenia obliczeniowego elementu docelowego. 

- Możesz teraz przygotowanie i używać docelowym obliczeniowych, korzystając z uwierzytelniania opartego na kluczach SSH.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie i instalowanie usługi Azure Machine Learning](../service/quickstart-installation.md)
* [Zarządzanie modelami](model-management-overview.md)
