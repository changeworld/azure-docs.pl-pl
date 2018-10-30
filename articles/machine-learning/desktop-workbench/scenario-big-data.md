---
title: Prognozowanie obciążenia serwera pod kątem terabajtów danych — Azure | Dokumentacja firmy Microsoft
description: Jak szkolenie modelu uczenia maszynowego, danych big Data przy użyciu usługi Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ROBOTS: NOINDEX
ms.openlocfilehash: 0d3c6b78944d9365d1e7e88ed33aba852b71a9c1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232022"
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prognozowanie obciążenia serwera pod kątem terabajtów danych

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

W tym artykule opisano, jak analitycy danych za pomocą usługi Azure Machine Learning Workbench opracowywania rozwiązań, które korzystają z danych big data. Można zacząć od przykładowych dużych zestawów danych, iterację przygotowywania danych, technicznego opracowywania funkcji oraz uczenie maszynowe i następnie rozszerzyć proces całego dużych zestawów danych. 

Poznasz następujące kluczowe funkcje Machine Learning Workbench:
* Łatwo przełączać się między obliczeniowych elementów docelowych. Można skonfigurować różne obliczeniowych elementów docelowych i używać ich w eksperymentowania. W tym przykładzie użyjesz DSVM Ubuntu i klaster usługi HDInsight platformy Azure jako obliczeniowych elementów docelowych. Można również skonfigurować celów obliczeń, w zależności od dostępności zasobów. W szczególności po skalowanie klastra Spark z większą liczbę węzłów procesu roboczego, można użyć zasobów za pomocą aplikacji Machine Learning Workbench do przyspieszenia przebiegów eksperymentu.
* Uruchom śledzenie historii. Usługa Machine Learning Workbench można użyć do śledzenia wydajności modeli i inne istotne metryki uczenia maszynowego.
* Operacjonalizacja modelu uczenia maszynowego. Wbudowane narzędzia w aplikacji Machine Learning Workbench umożliwia wdrażanie uczonego uczenia maszynowego model jako usługę sieci web w usłudze Azure Container Service. Usługa sieci web umożliwia również uzyskiwać prognozy mini usługi batch za pomocą wywołań interfejsu API REST. 
* Obsługa terabajtów danych.

> [!NOTE]
> Aby uzyskać przykłady kodu i inne materiały związane z tego przykładu, zobacz [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Omówienie przypadków użycia

Prognozowanie obciążenia na serwerach jest typowych potrzeb biznesowych dla przedsiębiorstw technologii, które zarządzają własną infrastrukturą. Aby zmniejszyć koszty infrastruktury, usług działających na serwerach w pełni wykorzystywaną powinny być zgrupowane razem w do uruchamiania na mniejszą liczbę maszyn. Usług działających na serwerach nadmiernego obciążenia należy podać więcej maszyn do uruchomienia. 

W tym scenariuszu możesz skoncentrować się na straty obciążenia dla każdej maszyny (lub serwera). W szczególności umożliwia danych sesji na każdym serwerze w przyszłości przewidzieć klasy obciążenie serwera. Klasyfikowanie obciążenia poszczególnych serwerów na niski, średni i wysoki klasy za pomocą losowych Klasyfikator lasu w [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Techniki i przepływu pracy, w tym przykładzie uczenia maszynowego można łatwo rozszerzać inne podobne problemy. 


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

* [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Aby zainstalować ten program i utworzyć obszar roboczy, zobacz [Przewodnik instalacji szybkiego startu](quickstart-installation.md). Jeśli masz wiele subskrypcji, możesz to zrobić [ustaw odpowiednią subskrypcję do bieżącej subskrypcji active](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (zgodnie z instrukcjami w tym przykładzie są generalnie takie same dla systemów macOS).
* Data Science Virtual Machine (dsvm dystrybucji) dla systemu Linux (Ubuntu), najlepiej w regionie wschodnie stany USA, w którym znajduje się dane. Możesz aprowizować DSVM Ubuntu, postępując zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Można również wyświetlić [ten przewodnik Szybki Start](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Firma Microsoft zaleca używanie maszyny wirtualnej z co najmniej 8 rdzeni i pamięci równym 32 GB. 

Postępuj zgodnie z [instrukcji](../desktop-workbench/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) Aby włączyć dostęp bez hasła sudoer na maszynie Wirtualnej AML aplikacji Workbench.  Możesz użyć [uwierzytelniania opartego na kluczach SSH dotyczące tworzenia i używania maszyny Wirtualnej w aplikacji Workbench AML](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). W tym przykładzie używamy hasła do dostępu do maszyny Wirtualnej.  Zapisz Poniższa tabela z informacjami o TRENINGU do dalszych etapów:

 Nazwa pola| Wartość |  
 |------------|------|
Adres IP maszyny wirtualnej DSVM | xxx|
 Nazwa użytkownika  | xxx|
 Hasło   | xxx|


 Możesz użyć dowolnej maszyny Wirtualnej za pomocą [aparat platformy Docker](https://docs.docker.com/engine/) zainstalowane.

* Klaster Spark HDInsight w wersji 3.6 Hortonworks Data Platform i wersji platformy Spark 2.1.x, najlepiej w regionie wschodnie stany USA, w którym znajduje się dane. Odwiedź stronę [Tworzenie klastra Apache Spark w usłudze Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) Aby uzyskać szczegółowe informacje o tym, jak tworzyć klastry HDInsight. Zalecamy używanie klastra trzy-proces roboczy, z każdego pracownika o 16 rdzeni i 112 GB pamięci. Lub możesz tylko wybrać typ maszyny Wirtualnej `D12 V2` węzła głównego i `D14 V2` węzła procesu roboczego. Wdrożenie klastra trwa około 20 minut. Konieczne jest nazwa klastra, nazwa użytkownika SSH i hasło, aby wypróbować w tym przykładzie. Zapisz Poniższa tabela z informacjami o klastrze Azure HDInsight do dalszych etapów:

 Nazwa pola| Wartość |  
 |------------|------|
 Nazwa klastra| xxx|
 Nazwa użytkownika  | xxx (sshuser domyślnie)|
 Hasło   | xxx|


* Konto usługi Azure Storage. Możesz wykonać [w instrukcjach](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) ją utworzyć. Ponadto zostaną utworzone dwa kontenery prywatnego obiektu blob o nazwach `fullmodel` i `onemonthmodel` na tym koncie magazynu. Konto magazynu jest używane do zapisywania wyników pośrednich obliczeń i modeli uczenia maszynowego. Potrzebujesz klucza konta magazynu nazwy i dostępu możesz wypróbować w tym przykładzie. Zapisz tabelę następujące informacje o koncie usługi Azure storage do dalszych etapów:

 Nazwa pola| Wartość |  
 |------------|------|
 Nazwa konta magazynu| xxx|
 Klucz dostępu  | xxx|


Ubuntu maszyny wirtualnej DSVM i klaster HDInsight Azure utworzone na liście wymagań wstępnych są obliczeniowych elementów docelowych. Obliczeniowe cele to zasobów obliczeniowych w kontekście aplikacji Machine Learning Workbench, która może się różnić od komputera, na którym działa aplikacja Workbench.   

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwórz aplikację Machine Learning Workbench.
2.  Na **projektów** wybierz opcję **+** zalogować się i wybrać **nowy projekt**.
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu.
4.  W **Wyszukaj szablony projektów** pola wyszukiwania, typ **Prognozowanie obciążenia terabajtów danych**, a następnie wybierz szablon.
5.  Wybierz pozycję **Utwórz**.

Można utworzyć projekt aplikacji Workbench z repozytorium git wstępnie utworzonych, postępując zgodnie z [tej instrukcji](./tutorial-classifying-iris-part-1.md).  
Uruchom `git status` Aby sprawdzić stan plików dla wersji, śledzenia.

## <a name="data-description"></a>Opis danych

Dane używane w tym przykładzie jest danych obciążenia syntetyzowany serwera. Jest ona hostowana na konto magazynu obiektów Blob platformy Azure, będącego publicaly, które są dostępne w regionie wschodnie stany USA. Informacje o koncie magazynu określonym znajdują się w `dataFile` pole [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) w formacie "wasb: / /<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". Można użyć danych bezpośrednio z magazynu obiektów Blob. Jeśli magazyn jest używany przez wielu użytkowników równocześnie, możesz użyć [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) do pobierania danych do magazynu w celu zwiększenia jakości eksperymentowania. 

Łącznemu rozmiarowi danych wynosi około 1 TB. Każdy plik ma około 1 – 3 GB, a w formacie pliku CSV, bez nagłówka. Każdy wiersz danych reprezentuje obciążenie transakcji na określonym serwerze. Szczegółowe informacje schematu danych jest w następujący sposób:

Numer kolumny | Nazwa pola| Typ | Opis |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Data/godzina |    Czas rozpoczęcia sesji
2  |`SessionEnd`    | Data/godzina | Godzina zakończenia sesji
3 |`ConcurrentConnectionCounts` | Liczba całkowita | Liczba równoczesnych połączeń
4 | `MbytesTransferred` | Podwójne | Znormalizowana danych przesyłanych w megabajtach
5 | `ServiceGrade` | Liczba całkowita |  Usługi klasy korporacyjnej, w sesji
6 | `HTTP1` | Liczba całkowita|  Sesja używa HTTP1 lub protokołu HTTP2
7 |`ServerType` | Liczba całkowita   |Typ serwera
8 |`SubService_1_Load` | Podwójne |   Obciążenia Usługa 1
9 | `SubService_2_Load` | Podwójne |  Usługa 2 obciążenia
10 | `SubService_3_Load` | Podwójne |     Obciążenia usługa 3
11 |`SubService_4_Load` | Podwójne |  Obciążenia usługa 4
12 | `SubService_5_Load`| Podwójne |      Obciążenia usługa 5
13 |`SecureBytes_Load`  | Podwójne | Bezpieczne bajtów obciążenia
14 |`TotalLoad` | Podwójne | Łączna liczba obciążenie serwera
15 |`ClientIP` | Ciąg|    Adres IP klienta
16 |`ServerIP` | Ciąg|    Adres IP serwera



Należy pamiętać, że oczekiwane typy danych są wymienione w powyższej tabeli. Ze względu na brakujące wartości i problemy związane z zakłóconych danych nie ma żadnej gwarancji, że typy danych są rzeczywiście, zgodnie z oczekiwaniami. Przetwarzanie danych należy wziąć pod uwagę. 


## <a name="scenario-structure"></a>Struktura scenariusza

Pliki w tym przykładzie są zorganizowane w następujący sposób.

| Nazwa pliku | Typ | Opis |
|-----------|------|-------------|
| `Code` | Folder | Folder zawiera cały kod w przykładzie. |
| `Config` | Folder | Folder zawiera pliki konfiguracji. |
| `Image` | Folder | Folder używany do zapisywania obrazów w pliku README. |
| `Model` | Folder | Folder używany do zapisywania plików modelu są pobierane z magazynu obiektów Blob. |
| `Code/etl.py` | Soubor Pythonu | Plik języka Python, służy do przygotowania danych i technicznego opracowywania funkcji. |
| `Code/train.py` | Soubor Pythonu | Plik języka Python, używane do trenowania modelu trzy klasy multi klasyfikacja.  |
| `Code/webservice.py` | Soubor Pythonu | Plik języka Python, na potrzeby operacjonalizacji.  |
| `Code/scoring_webservice.py` | Soubor Pythonu |  Plik języka Python, używany do przekształcania danych i wywoływania usługi sieci web. |
| `Code/O16Npreprocessing.py` | Soubor Pythonu | Plik języka Python, używany do wstępnego przetworzenia danych scoring_webservice.py.  |
| `Code/util.py` | Soubor Pythonu | Plik języka Python, który zawiera kod do odczytywania i zapisywania obiektów blob platformy Azure.  
| `Config/storageconfig.json` | Plik JSON | Plik konfiguracji dla kontenera obiektów blob platformy Azure, w którym są przechowywane wyniki pośrednie i modelu do przetwarzania i szkolenia na jeden miesiąc danych. |
| `Config/fulldata_storageconfig.json` | Plik JSON | Plik konfiguracji dla kontenera obiektów blob platformy Azure, w którym są przechowywane wyniki pośrednie i modelu do przetwarzania i szkolenia na całego zestawu danych.|
| `Config/webservice.json` | Plik JSON | Plik konfiguracyjny scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Plik YAML | Plik zależności Conda. |
| `Config/conda_dependencies_webservice.yml` | Plik YAML | Plik zależności Conda usługi sieci web.|
| `Config/dsvm_spark_dependencies.yml` | Plik YAML | Plik zależności Spark Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | Plik YAML | Plik zależności platformy Spark dla klastra HDInsight Spark. |
| `README.md` | Plik markdown | Plik README w języku znaczników markdown. |
| `Code/download_model.py` | Soubor Pythonu | Plik języka Python, używane do pobierania plików modelu poziomu usługi Azure blob na dysk lokalny. |
| `Docs/DownloadModelsFromBlob.md` | Plik markdown | Pliku markdown, który zawiera instrukcje dotyczące sposobu uruchamiania `Code/download_model.py`. |



### <a name="data-flow"></a>Przepływ danych

Kod w [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) ładuje dane z kontenera publicznie (`dataFile` pole [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Obejmuje przygotowanie danych i technicznego opracowywania funkcji i zapisuje wyników pośrednich obliczeń i modeli do prywatnej kontenera. Kod w [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) ładuje wyników pośrednich obliczeń z kontenera prywatnych, szkolenie modeli model klasyfikacji wieloklasowej i zapisuje modelu uczenia maszynowego uczonego kontenera prywatnych. 

Należy użyć jednego kontenera eksperymentów na zestaw danych jednego miesiąca, a innym eksperymentów na całego zestawu danych. Ponieważ danych i modeli są zapisywane w postaci pliku Parquet, każdy plik jest faktycznie folder w kontenerze, zawierające wiele obiektów blob. Wynikowy kontenera wygląda następująco:

| Nazwa prefiksu obiektu blob | Typ | Opis |
|-----------|------|-------------|
| featureScaleModel | Parquet | Model scaler standardowe funkcje numeryczne. |
| stringIndexModel | Parquet | Ciąg indeksatora modelu dla funkcji nieliczbowe.|
| oneHotEncoderModel|Parquet | Koder hot jeden model dla kategorii funkcji. |
| mlModel | Parquet | Model uczenia maszynowego uczony. |
| informacje| Plik z pakietu pickle języka Python | Informacje o przekształcone dane, w tym szkolenia rozpoczęcia, zakończenia szkolenia, czas trwania, sygnaturę czasową dla train-test dzielenie i kolumn do indeksowania i jeden na gorąco kodowania.

Wszystkie pliki i obiekty BLOB w powyższej tabeli są używane do funkcji operacjonalizacji.


### <a name="model-development"></a>Model programowania

#### <a name="architecture-diagram"></a>Diagram architektury


Na poniższym diagramie przedstawiono przepływ pracy end-to-end korzystania z aplikacji Machine Learning Workbench do tworzenia modelu: ![architektury](media/scenario-big-data/architecture.PNG)

W poniższych sekcjach przedstawiono tworzenie modelu za pomocą funkcji docelowej zdalnego obliczeń w aplikacji Machine Learning Workbench. Firma Microsoft najpierw załadować niewielką ilość przykładowych danych, a następnie uruchom skrypt [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) na DSVM Ubuntu szybkie iteracji. Możemy zawęzić pracy, jak w [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) , przekazując dodatkowy argument dla iteracji szybciej. Na koniec używamy klastra usługi HDInsight to w opracowywaniu z pełnymi danymi.     

[ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) Ładuje plik i przygotowuje dane i wykonuje technicznego opracowywania funkcji. Przyjmuje dwa argumenty:
* Plik konfiguracji kontenera magazynu obiektów Blob do przechowywania wyników pośrednich obliczeń i modeli.
* Argument konfiguracji debugowania dla iteracji szybciej.

Pierwszy argument `configFilename`, jest plikiem konfiguracji lokalnej, gdzie przechowywać informacje o magazynu obiektów Blob i określ lokalizację załadować dane. Domyślnie jest [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), i ma być używane w danych jednego miesiąca, uruchom. Możemy również obejmować [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), którego należy użyć na uruchamianie całego zestawu danych. Zawartość w konfiguracji jest następująca: 

| Pole | Typ | Opis |
|-----------|------|-------------|
| storageAccount | Ciąg | Nazwa konta usługi Azure Storage |
| storageContainer | Ciąg | Kontener na koncie usługi Azure Storage do przechowywania wyników pośrednich |
| atrybutu storageKey | Ciąg |Klucz dostępu konta usługi Azure Storage |
| plik danych|Ciąg | Pliki źródła danych  |
| czas trwania| Ciąg | Czas trwania danych w plikach źródłowych danych|

Modyfikować zarówno `Config/storageconfig.json` i `Config/fulldata_storageconfig.json` do konfigurowania konta magazynu, klucz magazynu i kontener obiektów blob do przechowywania wyników pośrednich. Domyślnie jest kontener obiektów blob dla danych jednego miesiąca, uruchom `onemonthmodel`, i jest kontener obiektów blob dla całego zestawu danych, uruchom `fullmodel`. Upewnij się, że utworzono tych dwóch kontenerów na koncie magazynu. `dataFile` Pole [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) Określa, jakie dane są ładowane w [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). `duration` Pola konfiguruje zakres zawiera dane. Jeśli czas trwania ONE_MONTH danych załadowanych powinna być tylko jeden plik CSV między siedem plików danych czerwca 2016 r. Jeśli czas trwania jest pełna, pełny zestaw danych (1 TB) jest załadowany. Nie trzeba zmieniać `dataFile` i `duration` w tych plikach dwóch konfiguracji.

Drugi argument jest debugowania. Ustawienie FILTER_IP umożliwia szybsze iteracji. Korzystanie z tego parametru jest przydatne, jeśli chcesz debugować skrypt.

> [!NOTE]
> We wszystkich poniższych poleceń Zastąp dowolnej zmiennej argumentu z jego rzeczywistą wartością.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Model opracowywania na Docker Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Konfigurowanie obliczeniowego elementu docelowego

Uruchom wiersz polecenia z aplikacji Machine Learning Workbench, wybierając **pliku** > **Otwórz wiersz polecenia**. Następnie uruchom polecenie: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Następujące dwa pliki są tworzone w folderze aml_config projektu:

-  dockerdsvm.COMPUTE: ten plik zawiera połączenia i informacje dotyczące konfiguracji dla miejsca docelowego zdalne wykonywanie kodu.
-  dockerdsvm.runconfig: ten plik jest zestawem opcji uruchamiania używanych w aplikacji Workbench.

Przejdź do dockerdsvm.runconfig i zmiany konfiguracji tych pól do następujących:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Przygotuj środowisko projektu za pomocą:

```az ml experiment prepare -c dockerdsvm```


Za pomocą `PrepareEnvironment` wartość true, Machine Learning Workbench tworzy środowisko uruchomieniowe, zawsze wtedy, gdy prześlesz zadanie. `Config/conda_dependencies.yml` i `Config/dsvm_spark_dependencies.yml` zawiera dostosowania środowiska wykonawczego. Można zawsze zmodyfikować zależności Conda, konfigurowanie aparatu Spark i zależności platformy Spark, edytując te dwa pliki YMAL. W tym przykładzie dodaliśmy `azure-storage` i `azure-ml-api-sdk` jako dodatkowe pakiety języka Python w `Config/conda_dependencies.yml`. Dodaliśmy również `spark.default.parallelism`, `spark.executor.instances`, i `spark.executor.cores` w `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Przygotowywanie danych i technicznego opracowywania funkcji na DSVM platformy Docker

Uruchom skrypt `etl.py` na DSVM platformy Docker. Użyj parametru debugowania, który filtruje załadowanych danych za pomocą adresów IP określonego serwera:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Przejdź do panelu po stronie, a następnie wybierz pozycję **Uruchom** Aby wyświetlić historię uruchamiania `etl.py`. Zauważ, że w czasie wykonywania to około dwóch minut. Jeśli planujesz zmian w kodzie w celu uwzględnienia nowych funkcji dzięki FILTER_IP jako drugi argument zapewnia szybsze iteracji. Konieczne może być wykonywany ten krok wielokrotnie podczas zajmowania się własne problemów, aby eksplorować zestaw danych lub utworzyć nowe funkcje uczenia maszynowego. 

Przy użyciu dostosowanych ograniczenia na temat danych obciążenia w celu dalszego filtrowania, jakie dane należy przetworzyć można przyspieszyć proces iteracji, tworzenie modelu. Jako eksperymenty, należy okresowo zapisywać zmiany w kodzie do repozytorium Git. Należy pamiętać, że użyliśmy następujący kod w `etl.py` umożliwiające dostęp do kontenera prywatnych:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Następnie uruchom skrypt `etl.py` na Docker DSVM bez parametru debugowania FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Przejdź do panelu po stronie, a następnie wybierz pozycję **Uruchom** Aby wyświetlić historię uruchamiania `etl.py`. Zwróć uwagę, że czas wykonywania jest około czterech minut. Przetworzone wynik w tym kroku zostanie zapisany w kontenerze i jest ładowany do szkolenia w train.py. Ponadto indeksatory ciągu, kodera potoków i scalers standardowe są zapisywane w prywatnej kontenera. Są one używane w operacjonalizacji. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Model szkolenia z zakresu nauki platformy Docker

Uruchom skrypt `train.py` na DSVM platformy Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

W tym kroku ładuje wyników pośrednich obliczeń w wyniku uruchomienia `etl.py`, a szkolenie modeli modelu uczenia maszynowego. Ten krok zajmuje około dwóch minut.

Po pomyślnym zakończeniu eksperymentów na małych danych, można nadal uruchamiać eksperymenty na całego zestawu danych. Można uruchomić przy użyciu tego samego kodu, a następnie eksperymentować z argumentem i obliczenia zmiany docelowego.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Tworzenie modelu w klastrze HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Utworzyć obliczeniowego elementu docelowego w aplikacji Machine Learning Workbench dla klastra HDInsight

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Następujące dwa pliki są tworzone w folderze aml_config:
    
-  myhdi.COMPUTE: ten plik zawiera połączenia i informacje dotyczące konfiguracji dla miejsca docelowego zdalne wykonywanie kodu.
-  myhdi.runconfig: ten plik jest zestaw opcji uruchamiania, używany w aplikacji Workbench.


Przejdź do myhdi.runconfig i zmiany konfiguracji tych pól do następujących:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Przygotuj środowisko projektu za pomocą:

```az ml experiment prepare -c myhdi```

Może to potrwać maksymalnie siedem minut.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Przygotowywanie danych i inżynieria funkcji w klastrze HDInsight

Uruchom skrypt `etl.py`, z pełnymi danymi w klastrze HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Ponieważ to zadanie ma trwać stosunkowo długo (około 2 godziny), możesz użyć `-a` aby spowodować wyłączenie przesyłania strumieniowego danych wyjściowych. Jeśli zadanie nie zostanie wykonane, w **historii uruchamiania**, można przeglądać dzienniki sterowników i kontroler. W przypadku większego klastra można zawsze ponownie skonfigurować konfiguracje `Config/hdi_spark_dependencies.yml` więcej wystąpień lub rdzenie. Na przykład, jeśli masz klaster o czterech węzłach procesu roboczego, można zwiększyć wartość `spark.executor.instances` z zakresu od 5 do 7. Można zobaczyć wyniki tego kroku w **fullmodel** kontenera na koncie magazynu. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Szkolenie modelu w klastrze HDInsight

Uruchom skrypt `train.py` w klastrze HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Ponieważ to zadanie ma trwać stosunkowo długo (około 30 minut), możesz użyć `-a` aby spowodować wyłączenie przesyłania strumieniowego danych wyjściowych.

#### <a name="run-history-exploration"></a>Uruchamianie eksploracji historii

Historia przebiegów jest funkcją, która umożliwia monitorowanie usługi eksperymentowanie w aplikacji Machine Learning Workbench. Domyślnie umożliwia śledzenie czasu trwania eksperymentowania. W naszym przykładzie określonej, gdy przejdziemy do pełnego zestawu danych dla `Code/etl.py` w eksperymentowania, zauważymy, czas trwania znacznie zwiększa. Można również rejestrować określonych metryk na potrzeby śledzenia. Aby włączyć śledzenie metryk, Dodaj następujące wiersze kodu do głowy plik języka Python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Oto przykład do śledzenia określonych metryk:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Na prawym pasku bocznym aplikacji Workbench, przejdź do **przebiegów** aby zobaczyć historię uruchomień każdego pliku języka Python. Możesz również przejść do repozytorium GitHub. Nową gałąź o nazwie rozpoczynającej się od "AMLHistory", zostanie utworzona śledzić zmiany wprowadzone do skryptu w każdym przebiegu. 


### <a name="operationalize-the-model"></a>Operacjonalizowanie modelu

W tej sekcji możesz operacjonalizować model który został utworzony w poprzednich krokach, jako usługę sieci web. Poznasz również sposób użycia usługi sieci web, przewidywanie obciążenia. Użyj interfejsów z wierszem polecenia operacjonalizacji językiem maszynowym (interfejsów wiersza polecenia) pakiet kod i zależności jako obrazy platformy Docker i opublikuj go jako usługę internetową konteneryzowanych.

Wiersz polecenia w aplikacji Machine Learning Workbench umożliwia uruchamianie interfejsów wiersza polecenia.  Można również uruchomić interfejsów wiersza polecenia w systemie Ubuntu Linux, wykonując [Przewodnik instalacji](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> W następujących poleceń Zastąp dowolnej zmiennej argumentu z jego rzeczywistą wartością. Trwa około 40 minut na zakończenie tej sekcji.
> 

Wybierz unikatowy ciąg jako środowisko operacjonalizacji. W tym miejscu używamy ciągu "[unique]", do reprezentowania ciąg, który wybierzesz.

1. Utwórz środowisko operacjonalizacji i Utwórz grupę zasobów.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Należy pamiętać, że usługi Container Service można użyć jako środowisko przy użyciu `--cluster` w `az ml env setup` polecenia. Model usługi machine learning można zoperacjonalizować na [usługi Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Używa ona [Kubernetes](https://kubernetes.io/) do automatyzowania wdrażania, skalowania i zarządzanie aplikacjami konteneryzowanymi. Tego polecenia trwa około 20 minut do uruchomienia. Aby sprawdzić, jeśli wdrożenie zakończyło się pomyślnie, należy użyć następującego: 

        az ml env show -g [unique]rg -n [unique]

   Ustaw środowisko wdrażania, który został utworzony, uruchamiając następujące czynności:

        az ml env set -g [unique]rg -n [unique]

2. Tworzenie i używanie konta zarządzania modelami. Aby utworzyć konto zarządzania modelami, uruchom następujące polecenie:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Na użytek zarządzania modelami operacjonalizacji, wykonując następujące czynności:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Konto zarządzania modelami służy do zarządzania modelami i usług sieci web. W witrynie Azure portal zobaczysz, że utworzono nowe konto zarządzania modelami. Widać, modele, manifestów, obrazy platformy Docker i usług, które są tworzone za pomocą tego konta zarządzania modelami.

3. Pobierz i zarejestruj modele.

   Pobierz modele w **fullmodel** kontenera na komputerze lokalnym w katalogu kodu. Nie pobieraj parquet plik danych o nazwie "vmlSource.parquet." Nie jest plikiem modelu; jest to spowodowane pośrednich obliczeń. Można także ponownie użyć plików modelu znajdujących się w repozytorium Git. Aby uzyskać więcej informacji, zobacz [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Przejdź do `Model` następuje modeli jako folder w interfejsu wiersza polecenia, a następnie zarejestrować:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Dane wyjściowe każdego polecenia zawierają identyfikator modelu, który jest wymagany w następnym kroku. Zapisz je w pliku tekstowym do przyszłego użytku.

4. Tworzenie manifestu dla usługi sieci web.

   Manifest zawiera kod dla usługi sieci web, modeli uczenia maszynowego i zależności środowiska środowiska uruchomieniowego. Przejdź do `Code` folderze interfejsu wiersza polecenia, a następnie uruchom następujące polecenie:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   Dane wyjściowe zawierają identyfikator manifestu do kolejnego kroku. 

   Zachowuj `Code` katalogu, a Ty testować webservice.py, uruchamiając następujące czynności: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Utwórz obraz platformy Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   Dane wyjściowe zawierają identyfikator obrazu do kolejnego etapu, ten obraz platformy docker jest używany w usłudze Container Service. 

6. Wdrażanie usługi sieci web z klastrem usługi kontenera.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   Dane wyjściowe zawierają identyfikator usługi. Należy użyć jej do uzyskania klucza autoryzacji i URL usługi.

7. Wywołaj usługę sieci web w kod języka Python, który będzie oceniać partiami mini.

   Aby uzyskać klucz autoryzacji, użyj następującego polecenia:

         az ml service keys realtime -i $ServiceID 

   Można pobrać usługi adres URL oceniania, użyj następującego polecenia:

        az ml service usage realtime -i $ServiceID

   Modyfikowanie zawartości `./Config/webservice.json` z odpowiednią usługę, oceniania adresu URL i klucza autoryzacji. Zachowaj "Bearer" w oryginalnym pliku i zastąp część "xxx". 
   
   Przejdź do katalogu głównego projektu, a następnie przetestować usługę sieci web dla minimalnej wsadowego oceniania przy użyciu następujących czynności:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Skalowanie usługi sieci web. 

   Aby uzyskać więcej informacji, zobacz [sposób skalowania operacjonalizacji w klastrze usługi Azure Container Service](how-to-scale-clusters.md).
 

## <a name="next-steps"></a>Kolejne kroki

Ten przykład pokazuje, jak szkolenie modelu uczenia maszynowego na danych big data za pomocą usługi Machine Learning Workbench i operacjonalizować trenowanego modelu. W szczególności wiesz, jak skonfigurować i używać celów obliczeń różne i uruchom historii śledzenia metryk oraz różnych tras.

Możesz rozszerzyć kod, aby eksplorować dostrajania krzyżowego sprawdzania poprawności i parametrów. Aby dowiedzieć się więcej na temat dostrajania krzyżowego sprawdzania poprawności i parametrów, zobacz [tego zasobu usługi GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Aby dowiedzieć się więcej na temat prognozowania szeregów czasowych, zobacz [tego zasobu usługi GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).