---
title: Prognozowanie obciążenia pracą serwera na terabajtów danych - Azure | Dokumentacja firmy Microsoft
description: Jak do uczenia modelu uczenia maszynowego danych big Data za pomocą usługi Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: 450c033fbce3544cdc17ddc6d47ff726b01a4d3e
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832666"
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prognozowanie obciążenia serwera pod kątem terabajtów danych

W tym artykule omówiono analityków danych użycia usługi Azure Machine Learning Workbench umożliwiające tworzenie rozwiązań, które korzystają z danych big data. Można rozpocząć od próbkę dużych zestawów danych, iterowania po przygotowaniu danych, funkcja inżynieryjne i uczenia maszynowego i następnie rozszerzają proces dużych cały zestaw danych. 

Dowiesz się o następujących kluczowych możliwości Machine Learning Workbench:
* Łatwo przełączać się między obliczeniowe elementy docelowe. Można skonfigurować różne obliczeniowe elementy docelowe i używać ich w eksperymenty. W tym przykładzie należy użyć DSVM Ubuntu i klaster Azure HDInsight jako miejsca docelowe obliczeń. Można również skonfigurować elementy docelowe obliczeń, w zależności od dostępności zasobów. W szczególności po skalowania klastra Spark z większą liczbę węzłów procesu roboczego, można użyć zasobów za pomocą Machine Learning Workbench aby przyspieszyć uruchamia eksperymentu.
* Uruchom śledzenie historii. Machine Learning Workbench służy do śledzenia realizacji machine learning modeli i innych metryk zainteresowań.
* Operationalization modelu uczenia maszynowego. Wbudowane narzędzia środowiska roboczego uczenia maszyny można użyć do wdrożenia maszyny przeszkolone uczenie modelu jako usługę sieci web w usłudze kontenera platformy Azure. Usługi sieci web umożliwia również pobrać prognoz mini partii za pośrednictwem wywołania interfejsu API REST. 
* Obsługa terabajtów danych.

> [!NOTE]
> Aby uzyskać przykłady kodu i inne materiały związane z tego przykładu, zobacz [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Omówienie przypadków użycia

Prognozowanie obciążenie serwerów jest typowe potrzeb biznesowych dla przedsiębiorstw technologii, które zarządzają własnej infrastruktury. Aby zmniejszyć koszty infrastruktury, usług działających na serwerach używany należy zgrupować razem do uruchamiania na mniejszą liczbę maszyn. Usług działających na serwerach nadmiernego obciążenia, należy podać więcej maszyny do uruchomienia. 

W tym scenariuszu można skupić się na prognozowania obciążenia dla każdego komputera (lub serwera). W szczególności umożliwia danych sesji na każdym serwerze w przyszłości prognozowania klasa obciążenia serwera. Klasyfikowanie obciążenia poszczególnych serwerów na niski, średni i wysoki klasy przy użyciu losowego Klasyfikator lasu w [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Machine learning technik i przepływ pracy, w tym przykładzie można łatwo rozszerzyć inne podobne problemy. 


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

* [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Aby zainstalować ten program i utworzyć obszaru roboczego, zobacz [Przewodnik Szybki Start instalacji](../service/quickstart-installation.md). Jeśli masz wiele subskrypcji, możesz [ustaw odpowiednią subskrypcję można bieżącej subskrypcji active](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (instrukcje w tym przykładzie są generalnie takie same dla systemów macOS).
* Dane nauki maszyny wirtualnej (DSVM) dla systemu Linux (Ubuntu), najlepiej w regionie wschodnie stany USA, gdzie znajduje się dane. Ubuntu DSVM można udostępnić, wykonując [tych instrukcji](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Możesz również sprawdzić [tego przewodnika Szybki Start](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Zaleca się używania maszyny wirtualnej z co najmniej 8 rdzeni i 32 GB pamięci. 

Postępuj zgodnie z [instrukcji](../service/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) Aby włączyć dostęp bez hasła sudoer na maszynie Wirtualnej do AML Workbench.  Możesz użyć [uwierzytelniania opartego na kluczach SSH dotyczące tworzenia i używania maszyny Wirtualnej w AML Workbench](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). W tym przykładzie używamy hasła do maszyny Wirtualnej.  Zapisz Poniższa tabela z informacjami o DSVM do wykonania kolejnych kroków:

 Nazwa pola| Wartość |  
 |------------|------|
Adres DSVM IP | xxx|
 Nazwa użytkownika  | xxx|
 Hasło   | xxx|


 Można użyć żadnej maszyny Wirtualnej z [aparatem platformy Docker](https://docs.docker.com/engine/) zainstalowane.

* Klaster usługi HDInsight Spark o platformie Hortonworks Data Platform 3,6 i wersja Spark 2.1.x, najlepiej w regionie wschodnie stany USA, gdzie znajduje się dane. Odwiedź stronę [utworzyć klaster Apache Spark w usłudze Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) szczegółowe informacje o sposobie tworzenia klastrów usługi HDInsight. Zalecamy używanie klastrze proces roboczy trzy z każdy pracownik mający 16 rdzeni i 112 GB pamięci. Można też tylko typu maszyny Wirtualnej `D12 V2` dla węzła głównego i `D14 V2` węzła procesu roboczego. Wdrożenie klastra trwa około 20 minut. Należy nazwa klastra, nazwa użytkownika SSH i hasło, aby wypróbować usługę w tym przykładzie. Zapisz Poniższa tabela z informacjami o klaster Azure HDInsight do wykonania kolejnych kroków:

 Nazwa pola| Wartość |  
 |------------|------|
 Nazwa klastra| xxx|
 Nazwa użytkownika  | xxx (sshuser domyślnie)|
 Hasło   | xxx|


* Konto usługi Azure Storage. Możesz wykonać [tych instrukcji](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) go utworzyć. Ponadto Utwórz dwa kontenery prywatnego obiektu blob o nazwach `fullmodel` i `onemonthmodel` na tym koncie magazynu. Konto magazynu służy do zapisania wyników obliczeń pośrednich i modeli uczenia maszynowego. Należy klucz konta magazynu nazwy i dostęp do wypróbowania w tym przykładzie. Zapisz Poniższa tabela z informacjami o koncie magazynu Azure do wykonania kolejnych kroków:

 Nazwa pola| Wartość |  
 |------------|------|
 Nazwa konta magazynu| xxx|
 Klucz dostępu  | xxx|


Ubuntu DSVM i klaster Azure HDInsight utworzony na liście wymagań wstępnych są elementy docelowe obliczeń. Obiekty docelowe są zasobów obliczeniowych w kontekście Machine Learning Workbench, które mogą się różnić od komputera, na którym jest uruchomiona Workbench obliczeń.   

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt, korzystając z tego przykładu jako szablon:
1.  Otwórz aplikację Machine Learning Workbench.
2.  Na **projekty** wybierz pozycję **+** Zaloguj, a następnie wybierz **nowy projekt**.
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu.
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz **obciążenia prognozowania terabajtów danych**i wybierz szablon.
5.  Wybierz pozycję **Utwórz**.

Umożliwia utworzenie projektu Workbench z repozytorium git wstępnie utworzone, wykonując [tej instrukcji](./tutorial-classifying-iris-part-1.md).  
Uruchom `git status` Aby sprawdzić stan plików dla wersji śledzenia.

## <a name="data-description"></a>Opis elementu danych

Dane używane w tym przykładzie jest danych obciążenia syntezatora serwera. Jest obsługiwany na koncie magazynu obiektów Blob platformy Azure, który jest publicznie dostępna w regionie wschodnie stany USA Informacje o koncie magazynu określonym znajdują się w `dataFile` pole [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) w formacie "wasb: / /<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". Można użyć danych bezpośrednio z magazynu obiektów Blob. Jeśli magazyn jest używany przez wielu użytkowników równocześnie, możesz użyć [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) można pobrać danych w magazynie na lepsze środowisko eksperymenty. 

Rozmiar całkowitą danych jest około 1 TB. Każdego pliku wynosi około 1 – 3 GB, a w formacie pliku CSV, bez nagłówka. Każdy wiersz danych reprezentuje obciążenia transakcji na określonym serwerze. Szczegółowe informacje o schemat danych przebiega następująco:

Numer kolumny | Nazwa pola| Typ | Opis |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Data/godzina |    Czas rozpoczęcia sesji
2  |`SessionEnd`    | Data/godzina | Godzina zakończenia sesji
3 |`ConcurrentConnectionCounts` | Liczba całkowita | Liczba jednoczesnych połączeń
4 | `MbytesTransferred` | O podwójnej precyzji | Znormalizowany danych przesyłanych w megabajtach
5 | `ServiceGrade` | Liczba całkowita |  Klasa usługi dla sesji
6 | `HTTP1` | Liczba całkowita|  Sesja używa HTTP1 lub HTTP2
7 |`ServerType` | Liczba całkowita   |Typ serwera
8 |`SubService_1_Load` | O podwójnej precyzji |   Obciążenia subservice 1
9 | `SubService_2_Load` | O podwójnej precyzji |  Subservice 2 obciążenia
10 | `SubService_3_Load` | O podwójnej precyzji |     Obciążenia subservice 3
11 |`SubService_4_Load` | O podwójnej precyzji |  Subservice 4 obciążenia
12 | `SubService_5_Load`| O podwójnej precyzji |      Obciążenia subservice 5
13 |`SecureBytes_Load`  | O podwójnej precyzji | Bezpieczne bajtów obciążenia
14 |`TotalLoad` | O podwójnej precyzji | Całkowita liczba obciążenie serwera
15 |`ClientIP` | Ciąg|    Adres IP klienta
16 |`ServerIP` | Ciąg|    Adres IP serwera



Należy pamiętać, że typy oczekiwane dane są wymienione w powyższej tabeli. Z powodu problemów dirty danych i brakujące wartości należy nie ma żadnej gwarancji, że typy danych są faktycznie zgodnie z oczekiwaniami. Przetwarzanie danych to należy wziąć pod uwagę. 


## <a name="scenario-structure"></a>Scenariusz — struktura

Pliki w tym przykładzie są zorganizowane w następujący sposób.

| Nazwa pliku | Typ | Opis |
|-----------|------|-------------|
| `Code` | Folder | Folder zawiera cały kod w przykładzie. |
| `Config` | Folder | Folder zawiera pliki konfiguracji. |
| `Image` | Folder | Folder używany do zapisywania obrazów na plik README. |
| `Model` | Folder | Folder używany do zapisywania plików modelu pobrana z magazynu obiektów Blob. |
| `Code/etl.py` | Plik Python | Plik Python Przygotowanie danych i funkcji zespołu inżynieryjnego. |
| `Code/train.py` | Plik Python | Plik języka Python, używany do uczenia modelu klasy trzech multi klasyfikacja.  |
| `Code/webservice.py` | Plik Python | Plik języka Python, używany dla operationalization.  |
| `Code/scoring_webservice.py` | Plik Python |  Plik języka Python, używany do transformacji danych i wywoływania usługi sieci web. |
| `Code/O16Npreprocessing.py` | Plik Python | Plik języka Python, używany do wstępnego przetworzenia danych scoring_webservice.py.  |
| `Code/util.py` | Plik Python | Plik języka Python, który zawiera kod do odczytywania i zapisywania obiektów blob Azure.  
| `Config/storageconfig.json` | Plik JSON | Plik konfiguracji dla tego kontenera obiektów blob platformy Azure, który przechowuje wyniki pośrednie i model dla przetwarzania i szkolenia na jeden miesiąc danych. |
| `Config/fulldata_storageconfig.json` | Plik JSON | Plik konfiguracji dla tego kontenera obiektów blob platformy Azure, który przechowuje wyniki pośrednie i model dla przetwarzania i szkolenia na pełny zestaw danych.|
| `Config/webservice.json` | Plik JSON | Plik konfiguracji scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Plik yaml programu | Plik zależności Conda. |
| `Config/conda_dependencies_webservice.yml` | Plik yaml programu | Plik Conda zależności usługi sieci web.|
| `Config/dsvm_spark_dependencies.yml` | Plik yaml programu | Plik zależności Spark Ubuntu DSVM. |
| `Config/hdi_spark_dependencies.yml` | Plik yaml programu | Plik zależności Spark dla klastra Spark w usłudze HDInsight. |
| `README.md` | Pliku markdown | Plik README pliku markdown. |
| `Code/download_model.py` | Plik Python | Plik języka Python, używany do pobierania plików modelu z platformy Azure blob na dysku lokalnym. |
| `Docs/DownloadModelsFromBlob.md` | Pliku markdown | Plik markdown, który zawiera instrukcje dotyczące sposobu uruchamiania `Code/download_model.py`. |



### <a name="data-flow"></a>Przepływ danych

Kod w [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) ładuje dane z kontenera publicznie (`dataFile` pole [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Obejmuje przygotowanie danych i funkcji inżynieryjne i zapisuje wyniki pośrednie obliczeń i modeli do własnych prywatnych kontenera. Kod w [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) ładuje wyników pośrednich obliczeń z Kontener prywatny, przygotowuje model klasyfikacji wielu klas i zapisuje modelu uczenia maszynowego przeszkolone Kontener prywatny. 

Jeden kontener należy używać do eksperymentów na jeden miesiąc w zestawie danych, a inny dla eksperymenty na pełny zestaw danych. Ponieważ modele i dane są zapisywane jako plik Parquet, każdy plik jest rzeczywiście folderu w kontenerze, zawierający wiele obiektów blob. Wynikowa kontenera wygląda następująco:

| Nazwa prefiksu obiektu blob | Typ | Opis |
|-----------|------|-------------|
| featureScaleModel | Parquet | Model scaler standardowe funkcje numeryczne. |
| stringIndexModel | Parquet | Model indeksatora ciąg nieliczbowy funkcji.|
| oneHotEncoderModel|Parquet | Model hot jeden koder podzielone na kategorie funkcji. |
| mlModel | Parquet | Modelu uczenia maszynowego przeszkolone. |
| informacje| Plik pickle Python | Informacje o przekształcone dane, w tym start szkolenia, szkolenia zakończenia, czas trwania, sygnaturę czasową dla train-test dzielenia i kolumny na potrzeby indeksowania hot jeden kodowania i.

Operationalization używane pliki i obiekty BLOB w powyższej tabeli.


### <a name="model-development"></a>Projektowanie modelu

#### <a name="architecture-diagram"></a>Diagram architektury


Na poniższym diagramie przedstawiono przepływ pracy na trasie do opracowywania modelu za pomocą Machine Learning Workbench: ![architektury](media/scenario-big-data/architecture.PNG)

W poniższych sekcjach zostanie przedstawiony programowanie modelu przy użyciu funkcji zdalnego obliczeń docelowego w Machine Learning Workbench. Firma Microsoft najpierw załadować niewielką ilość danych przykładowych, a następnie uruchom skrypt [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) na DSVM Ubuntu dla szybkiego iteracji. Firma Microsoft może jeszcze bardziej ograniczyć pracy, jak w [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) przez przekazanie dodatkowy argument dla szybsze iteracji. W końcu używamy klastra usługi HDInsight w celu przeszkolenia z pełnymi danymi.     

[ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) Pliku ładuje i przygotowuje danych i wykonuje engineering funkcji. Przyjmuje dwa argumenty:
* Plik konfiguracji kontenera magazynu obiektów Blob do przechowywania wyników pośrednich obliczeń i modeli.
* Argument konfiguracji debugowania dla szybsze iteracji.

Pierwszy argument `configFilename`, jest plikiem konfiguracji lokalnej, której przechowuje informacje dotyczące magazynu obiektów Blob, a następnie określ, gdzie do ładowania danych. Domyślnie jest [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), i ma być używane w danych jednego miesiąca, uruchom. Możemy również obejmować [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), którego musisz użyć na Uruchom pełny zestaw danych. Zawartość w konfiguracji jest następujący: 

| Pole | Typ | Opis |
|-----------|------|-------------|
| storageAccount | Ciąg | Nazwa konta magazynu Azure |
| storageContainer | Ciąg | Kontener na koncie magazynu Azure do przechowywania wyników pośrednich |
| atrybutu storageKey | Ciąg |Klucz dostępu do konta magazynu Azure |
| Pliku danych|Ciąg | Pliki źródła danych  |
| czas trwania| Ciąg | Czas trwania danych w plikach źródłowych danych|

Zmodyfikuj oba `Config/storageconfig.json` i `Config/fulldata_storageconfig.json` do konfigurowania konta magazynu, klucz magazynu i kontener obiektów blob do przechowywania wyników pośrednich. Domyślnie jest kontenera obiektów blob danych jednego miesiąca Uruchom `onemonthmodel`, i jest kontenera obiektów blob dla pełnego zestawu danych, uruchom `fullmodel`. Upewnij się, że tworzenie tych dwóch kontenerów na koncie magazynu. `dataFile` w [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) konfiguruje, jakie dane są ładowane w [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). `duration` Pola konfiguruje zakres obejmuje dane. Jeśli czas trwania ONE_MONTH danych załadowanych powinna być tylko jeden plik CSV między siedem plików danych dla czerwca 2016. Jeśli czas trwania jest pełna, pełny zestaw danych (1 TB) została załadowana. Nie trzeba zmieniać `dataFile` i `duration` w tych plikach konfiguracji dwóch.

Drugi argument jest debugowania. Ustawieniem dla niego FILTER_IP umożliwia szybsze iteracji. Użyj tego parametru jest przydatne, gdy chcesz debugować skrypt.

> [!NOTE]
> We wszystkich następujących poleceń Zastąp wszystkie argumentu zmiennej jego rzeczywistą wartością.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Projektowanie modelu na Docker Ubuntu DSVM

#####  <a name="1-set-up-the-compute-target"></a>1. Konfigurowanie docelowej obliczeń

Uruchom wiersz polecenia z Machine Learning Workbench, wybierając **pliku** > **Otwórz okno wiersza polecenia**. Następnie uruchom polecenie: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Następujące dwa pliki są tworzone w folderze aml_config projektu:

-  dockerdsvm.COMPUTE: ten plik zawiera dane połączenie i konfigurację dla elementu docelowego zdalne wykonywanie kodu.
-  dockerdsvm.runconfig: ten plik jest zestawem używanych aplikacji Workbench opcji uruchamiania.

Przejdź do dockerdsvm.runconfig i zmiany konfiguracji tych pól do następującego:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Przygotuj środowisko projektu za pomocą:

```az ml experiment prepare -c dockerdsvm```


Z `PrepareEnvironment` ma wartość true, Machine Learning Workbench tworzy środowisko uruchomieniowe przy każdym przesyłania zadania. `Config/conda_dependencies.yml` i `Config/dsvm_spark_dependencies.yml` zawiera dostosowania środowiska uruchomieniowego. Zależności Conda, Spark, konfiguracji i zależności Spark można zawsze zmodyfikować, edytując następujące dwa pliki YMAL. W tym przykładzie dodano `azure-storage` i `azure-ml-api-sdk` jako dodatkowe pakiety Python w `Config/conda_dependencies.yml`. Dodaliśmy również `spark.default.parallelism`, `spark.executor.instances`, i `spark.executor.cores` w `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Przygotowanie danych i funkcji engineering na DSVM Docker

Uruchom skrypt `etl.py` na DSVM Docker. Użyj parametru debugowania, filtrujące załadowanych danych z adresami IP określonego serwera:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Przejdź do panelu strony, a następnie wybierz **Uruchom** wyświetlić historię wykonywania `etl.py`. Należy zauważyć, że czas jest o dwie minuty. Jeśli planujesz Zmień swój kod, aby uwzględnić nowe funkcje, dzięki FILTER_IP jako drugi argument zapewnia szybsze iteracji. Może być konieczne uruchomienie tego kroku wiele razy podczas pracy z własnych machine learning problemy, aby eksplorować zestawu danych lub Utwórz nowe funkcje. 

Z dostosowanych ograniczenie obciążenia i dalszego filtrowania, jakie dane do przetwarzania danych można przyspieszyć proces iteracji w modelu programowania. Eksperymentując, należy okresowo zapisać zmiany w kodzie do repozytorium Git. Należy pamiętać, że użyliśmy następujący kod w `etl.py` umożliwiające dostęp do kontenera prywatnych:

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

Przejdź do panelu strony, a następnie wybierz **Uruchom** wyświetlić historię wykonywania `etl.py`. Należy zauważyć, że czas uruchomienia to około czterech minut. Przetworzone wynik tego kroku jest zapisany w kontenerze i jest załadowany do trenowania w train.py. Ponadto indeksatory ciągu, potoki koder i scalers standardowe są zapisywane w kontenerze prywatnych. Są one używane w operationalization. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Model udział w szkoleniach dotyczących DSVM Docker

Uruchom skrypt `train.py` na DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Ten krok ładuje wyników pośrednich obliczeń z uruchamiania z `etl.py`i przygotowuje modelu uczenia maszynowego. Ten krok zajmuje około dwóch minut.

Po pomyślnym zakończeniu eksperymenty na małych danych, można nadal uruchamiać eksperymenty na pełny zestaw danych. Można uruchomić przy użyciu tego samego kodu, a następnie Poeksperymentuj z argumentem i obliczeniowe zmiany docelowego.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Projektowanie modelu w klastrze usługi HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Utwórz element docelowy obliczeń w Machine Learning Workbench dla klastra usługi HDInsight

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Następujące dwa pliki są tworzone w folderze aml_config:
    
-  myhdi.COMPUTE: ten plik zawiera połączenia i informacje konfiguracyjne dla elementu docelowego zdalne wykonywanie kodu.
-  myhdi.runconfig: ten plik jest ustawiony używanych aplikacji Workbench opcji uruchamiania.


Przejdź do myhdi.runconfig i zmiany konfiguracji tych pól do następującego:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Przygotuj środowisko projektu za pomocą:

```az ml experiment prepare -c myhdi```

Ten krok może potrwać do siedmiu minut.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Przygotowanie danych i funkcji inżynieryjne w klastrze usługi HDInsight

Uruchom skrypt `etl.py`, z pełnymi danymi w klastrze usługi HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Ponieważ to zadanie trwać stosunkowo długo (około dwie godziny), można użyć `-a` wyłączyć strumienia wyjściowego. Po zakończeniu zadania, w **Uruchom historii**, można wyświetlić w dziennikach sterownika i kontrolera. Jeśli masz większego klastra zawsze ponownie skonfigurować konfiguracje `Config/hdi_spark_dependencies.yml` więcej wystąpień lub rdzeni. Na przykład, jeśli masz klaster z czterema węzłami procesu roboczego, należy zwiększyć wartość `spark.executor.instances` od 5 do 7. Można wyświetlić dane wyjściowe tego etapu w **fullmodel** kontenera na koncie magazynu. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Szkolenie modelu w klastrze usługi HDInsight

Uruchom skrypt `train.py` w klastrze usługi HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Ponieważ to zadanie trwać stosunkowo długo (około 30 minut), można użyć `-a` wyłączyć strumienia wyjściowego.

#### <a name="run-history-exploration"></a>Uruchom eksplorację historii

Historia uruchomień to funkcja, która umożliwia śledzenie sieci eksperymenty w Machine Learning Workbench. Domyślnie śledzi czas trwania eksperymenty. W naszym przykładzie określone, gdy są dostarczane do pełnego zestawu danych dla `Code/etl.py` w eksperymenty, zauważymy, że czas trwania znacznie zwiększa. Możesz także zalogować określonych metryk na potrzeby śledzenia. Aby włączyć metryki śledzenia, Dodaj następujące wiersze kodu nagłówek pliku Python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Oto przykład, aby śledzić określonej metryki:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Na prawo paska bocznego Workbench, przejdź do **działa** wyświetlić historię uruchomień na potrzeby każdego pliku Python. Można także przejść do repozytorium GitHub. Nowa gałąź, o nazwie "AMLHistory", zaczynając od jest tworzony śledzić zmiany wprowadzone do skryptu w każdym przebiegu. 


### <a name="operationalize-the-model"></a>Operacjonalizuj modelu

W tej sekcji możesz operacjonalizacji model, który został utworzony w poprzednich krokach jako usługę sieci web. Można również sposób korzystania z usługi sieci web na potrzeby prognozowania obciążenia. Użyj języka maszyny operationalization wiersza polecenia interfejsów (CLIs) pakietu kodu i zależności jako obrazy usługi Docker i publikowanie model jako usługę sieci web konteneryzowanych.

W wierszu polecenia w Machine Learning Workbench służy do uruchamiania CLIs.  Można również uruchomić CLIs na Ubuntu Linux, wykonując [Przewodnik instalacji](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> W następujących poleceń Zamień wszystkie argumentu zmiennej jego rzeczywistą wartością. Trwa około 40 minut na zakończenie tej sekcji.
> 

Wybierz unikatowy ciąg jako środowisko operationalization. W tym miejscu używamy ciąg "[unikatowe]", do reprezentowania ciąg, który można wybrać.

1. Utwórz środowisko operationalization, a następnie utwórz grupę zasobów.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Należy pamiętać, że usługi kontenera można użyć jako nazwa środowiska przy użyciu `--cluster` w `az ml env setup` polecenia. Aby operacjonalizować model uczenia maszynowego na [usługi kontenera platformy Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Używa [Kubernetes](https://kubernetes.io/) do automatyzacji wdrażania, skalowanie i zarządzanie konteneryzowanych aplikacji. To polecenie trwa około 20 minut do uruchomienia. Sprawdź, czy wdrożenie zakończyło się pomyślnie, należy wykonać następujące kroki: 

        az ml env show -g [unique]rg -n [unique]

   Ustaw środowiska wdrażania, który właśnie utworzony, uruchamiając następujące czynności:

        az ml env set -g [unique]rg -n [unique]

2. Tworzenie i używanie konta zarządzania modelu. Aby utworzyć konto zarządzania modelu, uruchom następujące polecenie:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Na użytek zarządzania modelu operationalization, wykonując następujące czynności:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Konto zarządzania modelu służy do zarządzania modelami i usług sieci web. W portalu Azure można zauważyć, że utworzono nowe konto zarządzania w modelu. Widać modeli, manifesty, obrazy usługi Docker i usług, które są tworzone za pomocą tego konta zarządzania w modelu.

3. Pobierz i zarejestruj modeli.

   Pobierz modeli w **fullmodel** kontenera na komputerze lokalnym w katalogu kodu. Nie pobieraj parquet plik danych o nazwie "vmlSource.parquet." Nie jest plikiem modelu; jest to spowodowane pośrednich obliczeń. Można również wykorzystać modelu plików znajdujących się w repozytorium Git. Aby uzyskać więcej informacji, zobacz [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Przejdź do `Model` następuje modeli jako folder w interfejsu wiersza polecenia, a następnie zarejestrować:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Dane wyjściowe każdego polecenia daje Identyfikatora modelu, który jest wymagany w następnym kroku. Zapisz je w pliku tekstowym do użytku w przyszłości.

4. Tworzenie manifestu dla usługi sieci web.

   Manifest zawiera kod dla usługi sieci web, modeli uczenia maszynowego i zależności środowiska środowiska wykonawczego. Przejdź do `Code` folderu w interfejsu wiersza polecenia, a następnie uruchom następujące polecenie:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   Dane wyjściowe zawiera identyfikator manifestu do następnego kroku. 

   Pozostanie w `Code` katalogu i przetestować webservice.py wykonując następujące czynności: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Utwórz obraz platformy Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   Dane wyjściowe stanowi identyfikator obrazu do następnego kroku, ten obraz docker jest używana w usłudze kontenera. 

6. Wdrażanie usługi sieci web z klastrem usługi kontenera.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   Dane wyjściowe zawiera identyfikator usługi. Należy używać go do uzyskania klucza autoryzacji i URL usługi.

7. Wywoływanie usługi sieci web w kodzie języka Python wyniki w partiach mini.

   Aby uzyskać klucz autoryzacji, użyj następującego polecenia:

         az ml service keys realtime -i $ServiceID 

   Użyj następującego polecenia, aby pobrać oceniania adres URL usługi:

        az ml service usage realtime -i $ServiceID

   Zmiany zawartości w `./Config/webservice.json` z usługą prawo oceniania adresu URL i autoryzacji klucza. Zachowaj "Bearer" w oryginalnym pliku i zamienić część "xxx". 
   
   Przejdź do katalogu głównego projektu i przetestować usługę sieci web dla minimalnej wsadowego oceniania przy użyciu następujących czynności:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Skalowanie usługi sieci web. 

   Aby uzyskać więcej informacji, zobacz [jak skalować operationalization w klastrze usługi kontenera platformy Azure](how-to-scale-clusters.md).
 

## <a name="next-steps"></a>Kolejne kroki

Ten przykład prezentuje sposób użycia Machine Learning Workbench do uczenia modelu danych big Data uczenia maszynowego i operacjonalizacji trenowanego modelu. W szczególności przedstawiono sposób konfigurowania i użyć różnych obliczeniowe i uruchomić historii śledzenia metryki i korzystać z różnych uruchomień.

Można rozszerzyć kod, aby eksplorować dostrajanie krzyżowego sprawdzania poprawności i parametru funkcji hyper. Aby dowiedzieć się więcej na temat dostrajania krzyżowego sprawdzania poprawności i parametru funkcji hyper, zobacz [tego zasobu GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Aby dowiedzieć się więcej na temat prognozowania szeregu czasowego, zobacz [tego zasobu GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
