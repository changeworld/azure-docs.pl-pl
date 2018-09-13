---
title: Klasyfikacja obrazów z powietrza | Dokumentacja firmy Microsoft
description: Zawiera instrukcje dotyczące rzeczywistych scenariusza Klasyfikacja obrazów z powietrza
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
ms.component: core
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: eb788f56825166ccaa376d32b07371db0588edc8
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35938439"
---
# <a name="aerial-image-classification"></a>Klasyfikacja obrazów z powietrza

W tym przykładzie pokazano, jak używać aplikacji Azure Machine Learning Workbench do koordynacji rozproszonego szkolenia i operacjonalizacji modeli klasyfikacji obrazów. Używamy dwa podejścia do szkolenia: (i) rafinacja sieci neuronowej za pomocą funkcji [usługi Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) klastrze procesorów GPU i (ii) przy użyciu [Microsoft Machine Learning dla platformy Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) pakietu cechowanie obrazów przy użyciu wstępnie przetrenowane modeli CNTK i uczyć klasyfikatory korzystanie z funkcji pochodnych. Firma Microsoft następnie zastosować wytrenowane modele w sposób równoległy ustawia duży obraz w chmurze za pomocą [usługi Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) klastra, pozwalając nam skalowanie szybkość szkolenia i operacjonalizacji przez dodawanie lub usuwanie węzłów procesu roboczego.

W tym przykładzie wyróżniono transfer learning, która korzysta z wstępnie przetrenowane modele, aby uniknąć kosztów szkolenia głębokich sieciach neuronowych od podstaw na dwa sposoby. Ponowne trenowanie sieci neuronowej, zwykle wymaga obliczeniowej procesora GPU, ale może prowadzić do większej dokładności, gdy zestaw szkoleniowy jest wystarczająco duży. Szkolenia proste klasyfikatora na obrazach neural nie wymaga obliczeniowej procesora GPU, jest z natury szybkich i skalowalnych arbitralnie i dostosowane do potrzeb mniej parametrów. Ta metoda jest w związku z tym doskonałym wyborem, gdy kilka próbek szkoleniowych dostępnych — jak często w przypadku dla przypadków użycia niestandardowego. 

Klaster Spark, używany w tym przykładzie ma 40 węzłami procesu roboczego i koszty ~$40/hr działanie; zasoby klastra usługi Batch AI obejmują ośmiu procesorów GPU i koszt ~$10/hr działanie. Ukończenie tego instruktażu trwa około trzy godziny. Gdy skończysz, postępuj zgodnie z instrukcjami oczyszczania, aby usunąć zasoby, które zostały utworzone oraz zatrzymanie naliczania opłat.

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii

Publicznego repozytorium GitHub, w tym scenariuszu rzeczywistych zawiera wszystkie materiały, w tym przykłady kodu, potrzebne w tym przykładzie:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Opis przypadków użycia

W tym scenariuszu firma Microsoft szkolenie modeli uczenia maszynowego do klasyfikowania typu objętego powietrza obrazy x miernika 224 miernika 224 powierzchni ziemi. Modeli klasyfikacji Użyj ziemi może służyć do śledzenia urbanizacji, wycinanie lasów, utraty podmokłych i innych głównych trendów środowiska za pomocą okresowo pobrane zdjęcia lotnicze. Firma Microsoft została przygotowana szkolenia i sprawdzanie poprawności zestawów obrazu, oparte na obrazach ze Stanów Zjednoczonych Krajowe rolnictwa obrazach programu i ziemi korzystanie z etykiet, opublikowane przez amerykański Baza danych Cover National lądzie. Przykładowe obrazy w każdej klasie Użyj ziemi przedstawiono poniżej:

![Przykład regionów dla każdego ziemi Zastosuj etykietę](media/scenario-aerial-image-classification/example-labels.PNG)

Po szkolenia i sprawdzanie poprawności modelu klasyfikatora, firma Microsoft zastosuje je do obrazów powietrza obejmujące Middlesex hrabstwa, MA--głównej z badań firmy Microsoft w Anglii nowy & Centrum rozwoju (NERD) — aby zademonstrować, jak używać tych modeli studiowanie miejskie trendów rozwój.

Aby wygenerować klasyfikatorów obrazów przy użyciu transferu uczenia, analityków, którzy często utworzenia wielu modeli przy użyciu szeregu metod i wybierz najbardziej wybieraniem najskuteczniejszego modelu. Usługa Azure Machine Learning Workbench można dane analityków koordynować szkolenia w środowiskach obliczeniowych w różnych, śledzenie i porównać wydajność różnych modeli i zastosować wybrany model do dużych zestawów danych w chmurze.

## <a name="scenario-structure"></a>Struktura scenariusza

W tym przykładzie dane obrazu i wstępnie przetrenowane modeli są trzymane w konto magazynu platformy Azure. Klaster usługi HDInsight Spark odczytuje te pliki i tworzy model klasyfikacji obrazów przy użyciu MMLSpark. Uczony model i jej prognozy są następnie zapisywane do konta magazynu, gdzie mogą być analizowane i wizualizowany w usłudze notesu programu Jupyter, uruchamiane lokalnie. Usługa Azure Machine Learning Workbench koordynuje zdalne wykonywanie skryptów w klastrze Spark. Pozwala również śledzić metryki dokładność dla wielu modeli uczony przy użyciu różnych metod, umożliwiając użytkownikowi wybranie najbardziej wybieraniem najskuteczniejszego modelu.

![Schemat dla scenariusza rzeczywistych klasyfikacji obrazów z powietrza](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Te instrukcje krok po kroku najpierw przeprowadzi Cię przez utworzenie i przygotowanie konta usługi Azure storage oraz klastra platformy Spark, w tym dane transferu i zależności instalacji. Następnie opisano sposób uruchamiania zadań szkoleniowych i porównywanie wydajności modeli wynikowe. Na koniec one ilustrują Zastosuj wybrany model do zestawu duży obraz w klastrze Spark i analizowanie wyników przewidywań lokalnie.


## <a name="set-up-the-execution-environment"></a>Konfigurowanie środowiska wykonawczego

Poniższe wskazówki ułatwiają skonfigurowanie środowiska wykonawczego, w tym przykładzie.

### <a name="prerequisites"></a>Wymagania wstępne
- [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne)
    - Utworzysz klaster usługi HDInsight Spark z 40 węzłami procesu roboczego (łączna liczba rdzeni 168). Upewnij się, że Twoje konto ma odpowiednią liczbą dostępnych rdzeni, przeglądając "użycie i przydziały" kartę dla Twojej subskrypcji w witrynie Azure portal.
       - W przypadku mniejszej liczby rdzeni może zmodyfikować szablon klastra HDInsight, aby zmniejszyć liczbę procesów roboczych aprowizowane. Odpowiednie instrukcje są wyświetlane w sekcji "Tworzenie klastra HDInsight Spark".
    - W tym przykładzie tworzony jest klaster usługi Batch AI Training przy użyciu dwóch NC6 (1 procesor GPU, 6 procesora wirtualnego vCPU) maszyn wirtualnych. Upewnij się, że Twoje konto ma odpowiednią liczbą dostępnych rdzeni w regionie wschodnie stany USA, przeglądając "użycie i przydziały" kartę dla Twojej subskrypcji w witrynie Azure portal.
- [Środowisko robocze usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md)
    - Postępuj zgodnie z [Instalowanie i Tworzenie szybkiego startu](../service/quickstart-installation.md) na instalowanie aplikacji Azure Machine Learning Workbench i tworzenie eksperymentowania oraz konta zarządzania modelami.
- [Z usługi Batch AI](https://github.com/Azure/BatchAI) Python SDK i platformą Azure, interfejsu wiersza polecenia
    - Wykonaj następujące sekcje w [usługi Batch AI przepisy README](https://github.com/Azure/BatchAI/tree/master/recipes):
        - "Wymagania wstępne"
        - "Utwórz i Pobierz aplikację usługi Azure Active Directory (AAD)"
        - "Zarejestruj dostawców zasobów BatchAI" (w obszarze "przepisy wykonywania przy użyciu wiersza polecenia platformy Azure")
        - "Zainstaluj klienta zarządzania w usłudze Azure Batch AI"
        - "Zainstaluj zestaw Azure Python SDK"
    - Rekord Identyfikatora klienta, klucz tajny i identyfikator dzierżawy aplikacji usługi Azure Active Directory, które są kierowane do utworzenia. Te poświadczenia zostaną użyte w dalszej części tego samouczka.
    - Na chwilę obecną, Azure Machine Learning Workbench i usługi Azure Batch AI używają oddzielnych rozwidlenia interfejsu wiersza polecenia platformy Azure. W celu uściślenia nazywamy aplikacji Workbench wersję interfejsu wiersza polecenia jako "interfejs wiersza polecenia uruchamiane w usłudze Azure Machine Learning Workbench" i (w tym usługi Batch AI) ogólne wersji "Wiersza polecenia platformy Azure."
- [Narzędzie AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), bezpłatne narzędzie do koordynowania transferu plików między kontami magazynu platformy Azure
    - Upewnij się, że folder zawierający plik wykonywalny narzędzia AzCopy w zmiennej środowiskowej PATH systemu. (Instrukcje na temat modyfikowania zmiennych środowiskowych dostępnych [tutaj](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Klient SSH; Firma Microsoft zaleca [PuTTY](http://www.putty.org/).

W tym przykładzie został przetestowany na komputerze z systemem Windows 10; można go uruchomić z dowolnej maszyny Windows, w tym maszyn wirtualnych do nauki o danych platformy Azure. Interfejs wiersza polecenia platformy Azure został zainstalowany z pliku MSI zgodnie z opisem w [w instrukcjach](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Może wymagać drobnych zmian (na przykład zmiany filepaths) podczas uruchamiania tego przykładu w systemie macOS.

### <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

W tym przykładzie wymaga klastra usługi HDInsight Spark i konto magazynu platformy Azure do odpowiednich plików hosta. Wykonaj te instrukcje, aby utworzyć te zasoby w nowej grupie zasobów platformy Azure:

#### <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwieranie usługi Azure Machine Learning Workbench
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Powietrza Klasyfikacja obrazów" i wybierz szablon
5.  Kliknij przycisk **Utwórz**
 
#### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

1. Po załadowaniu projektu w aplikacji Azure Machine Learning Workbench, Otwórz interfejs wiersza polecenia (CLI), klikając pozycję Plik -> Otwórz wiersz polecenia.
    Użyj tej wersji interfejsu wiersza polecenia dla większości tego samouczka. (W przypadku, gdy wskazane, zostanie wyświetlony monit Otwórz inną wersję interfejsu wiersza polecenia, aby przygotować zasoby usługi Batch AI.)

1. Przy użyciu interfejsu wiersza polecenia logowania do konta platformy Azure, uruchamiając następujące polecenie:

    ```
    az login
    ```

    Zostanie wyświetlony monit, odwiedź stronę adresu URL i typu w podanej tymczasowy kod; Witryna sieci Web żąda poświadczeń konta platformy Azure.
    
1. Gdy logowanie zostanie zakończone, wróć do interfejsu wiersza polecenia i uruchom następujące polecenie, aby określić, które subskrypcje platformy Azure są dostępne dla Twojego konta platformy Azure:

    ```
    az account list
    ```

    To polecenie wyświetla listę wszystkich subskrypcji skojarzonych z kontem platformy Azure. Znajdź identyfikator subskrypcji, którą chcesz użyć. Zapis Identyfikatora subskrypcji w przypadku, gdy wskazane w poniższym poleceniu następnie ustawić aktywną subskrypcję, wykonując polecenie:

    ```
    az account set --subscription [subscription ID]
    ```

1. Zasoby platformy Azure utworzone w tym przykładzie są przechowywane razem w grupie zasobów platformy Azure. Wybierz unikatową nazwą grupy zasobów, a następnie zapisać go w przypadku, gdy wskazane, a następnie wykonaj oba polecenia, aby utworzyć grupę zasobów platformy Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Tworzenie konta magazynu

Teraz utworzymy konto magazynu, że hosty projektu pliki, które muszą być dostępne przez rozwiązanie HDInsight Spark.

1. Wybierz unikatowej nazwy konta magazynu, a następnie zapisać go w przypadku, gdy wskazane w następującym `set` polecenia, a następnie utwórz konto magazynu platformy Azure, wykonując oba polecenia:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Wyświetl klucze konta magazynu, uruchamiając następujące polecenie:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Zapisz wartość `key1` jako klucz magazynu w następującym poleceniu, a następnie uruchom polecenie do przechowywania wartości.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Utwórz udział plików o nazwie `baitshare` na koncie magazynu za pomocą następującego polecenia:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. W swoim ulubionym edytorze tekstów, załaduj `settings.cfg` plik z projektu aplikacji Azure Machine Learning Workbench podkatalogu "Code" i Wstaw nazwę konta magazynu i klucz, jak wskazano. Zapisz i Zamknij `settings.cfg` pliku.
1. Jeśli jeszcze tego nie zrobiono, Pobierz i zainstaluj [AzCopy](http://aka.ms/downloadazcopy) narzędzia. Upewnij się, że plik wykonywalny narzędzia AzCopy na ścieżce systemowej, wpisując "Narzędzia AzCopy", a następnie naciskając klawisz Enter, aby wyświetlić jego dokumentacji.
1. Uruchom następujące polecenia, aby skopiować wszystkie przykładowe dane, wstępnie przetrenowane modeli i skryptów szkolenia modelu do odpowiedniej lokalizacji w ramach konta magazynu:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Oczekiwane transferu plików do zająć około jednej godziny. Podczas oczekiwania, możesz przejść do następnej sekcji: Otwórz inny interfejs wiersza polecenia przy użyciu aplikacji Workbench i ponownie definiować zmiennych tymczasowych może być konieczne.

#### <a name="create-the-hdinsight-spark-cluster"></a>Tworzenie klastra HDInsight Spark

Nasze zalecaną metodą tworzenia klastra usługi HDInsight używa szablonu Menedżera zasobów klastra platformy HDInsight Spark uwzględnione w podfolderze "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" ten projekt.

1. Szablon klastra platformy HDInsight Spark jest plik "template.json" w podfolderze "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" ten projekt. Domyślnie szablon tworzy klaster Spark z 40 węzłami procesu roboczego. Jeśli musisz dostosować tę liczbę, otworzyć szablon w swoim ulubionym edytorze tekstów i Zamień wszystkie wystąpienia "40" numer węzła procesu roboczego wybranych przez użytkownika.
    - Mogą wystąpić błędy braku pamięci później, jeśli liczba węzłów procesu roboczego, który wybierzesz jest mniejsza. Aby walczyć błędów pamięci, może uruchamiać skrypty szkolenia i operacjonalizacji dla podzbioru dostępnych danych, zgodnie z opisem w dalszej części tego dokumentu.
2. Wybierz unikatową nazwę i hasło dla HDInsight klastra i zapisanie ich w przypadku, gdy wskazane w następującym poleceniu: następnie utworzyć klaster, wysyłając polecenia:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Wdrożenie klastra może potrwać do 30 minut (w tym wykonanie akcji aprowizacji i skryptów).

### <a name="set-up-batch-ai-resources"></a>Konfigurowanie zasobów usługi Batch AI

Podczas oczekiwania na transfer plików konta magazynu i wdrażanie klastra platformy Spark do wykonania należy przygotować klaster usługi Batch AI sieci plików serwera NFS i procesora GPU. Otwórz wiersz polecenia z wiersza polecenia platformy Azure, a następnie uruchom następujące polecenie:

```
az --version 
```

Upewnij się, że `batchai` jest na liście zainstalowanych modułów. Jeśli nie używasz innego interfejsu wiersza polecenia (na przykład, otwartego za pośrednictwem aplikacji Workbench).

Następnie sprawdź, czy rejestracja została ukończona pomyślnie. (Rejestracja trwa maksymalnie 15 minut i może być trwająca, jeśli niedawno ukończone [instrukcje dotyczące konfigurowania usługi Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes).) Upewnij się, że "Microsoft.Batch" i "Microsoft.BatchAI" są wyświetlane ze stanem "Zarejestrowane" w danych wyjściowych następującego polecenia:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

W przeciwnym razie uruchom następującego dostawcy polecenia rejestracji i poczekaj około 15 minut na rejestrację, aby zakończyć.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Zmodyfikuj następujące polecenia, aby zastąpić wartości, które została użyta wcześniej podczas tworzenia kont grupy i przechowywania zasobów wyrażenia w nawiasach kwadratowych. Następnie należy przechowywać wartości jako zmienne, wykonując polecenia:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Zidentyfikować folder zawierający projekt usługi Azure Machine Learning (na przykład `C:\Users\<your username>\AzureML\aerialimageclassification`). Zamień wartości w nawiasach kwadratowych filepath folderu (z nie ukośnik odwrotny na końcu), a następnie uruchom polecenie:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Teraz możesz przystąpić do tworzenia zasobów usługi Batch AI potrzebne w tym samouczku.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Przygotuj serwer plików sieci sztucznej Inteligencji usługi Batch

Klaster usługi Batch AI uzyskuje dostęp do danych szkoleniowych na serwerze plików z sieci. Dostęp do danych może być several-fold szybciej podczas uzyskiwania dostępu do plików z systemu plików NFS i udziału plików platformy Azure lub usługi Azure Blob Storage.

1. Wykonaj następujące polecenie, aby utworzyć serwer plików sieciowych:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Sprawdź stan inicjowania obsługi administracyjnej sieci serwera plików przy użyciu następującego polecenia:

    ```
    az batchai file-server list
    ```

    "ProvisioningState" sieciowego serwera plików o nazwie "landuseclassifier" to "Powodzenie", jest gotowy do użycia. Oczekiwać, że inicjowanie obsługi administracyjnej, aby zająć około pięciu minut.
1. W danych wyjściowych poprzedniego polecenia (właściwość "fileServerPublicIp" w obszarze "mountSettings"), należy znaleźć adres IP Twojego systemu plików NFS. Zapis IP adresów w przypadku, gdy wskazane w następującym poleceniu, a następnie wartość przechowywana, wykonując polecenie:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Przy użyciu ulubionego narzędzia SSH (następujące przykładowe polecenie używa [PuTTY](http://www.putty.org/)), wykonaj ten projekt `prep_nfs.sh` skrypt systemu plików NFS transfer obrazu szkolenia i sprawdzanie poprawności ustawia istnieje.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Nie trzeba się troszczyć, jeśli aktualizacje postępu pobierania i wyodrębniania danych w oknie powłoki tak szybko przewinąć czy nieczytelne.

Jeśli to konieczne, można potwierdzić, że transfer danych podjęła zgodnie z planem, logując się do serwera plików przy użyciu ulubionego narzędzia SSH i sprawdzanie `/mnt/data` zawartości katalogu. Powinien znajdować się dwa foldery training_images i validation_images, zawierających z podfolderach nazwanych zgodnie z ziemi korzystanie z kategorii.  Zestawy szkoleniowe i sprawdzania poprawności powinna zawierać ~ 44 k i obrazy k ~ 11, odpowiednio.

#### <a name="create-a-batch-ai-cluster"></a>Utwórz klaster usługi Batch AI

1. Tworzenia klastra, wykonując następujące polecenie:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Użyj następującego polecenia w celu sprawdzenia, czy stan jego aprowizacji klastra:

    ```
    az batchai cluster list
    ```

    Gdy stan alokacji klastra o nazwie "landuseclassifier" zmiany rozmiaru do stała, istnieje możliwość przesyłania zadań. Jednak zadania są uruchamiane, dopóki wszystkie maszyny wirtualne w klastrze pozostało stanie "przygotowywania". Jeśli właściwość "błędy" w klastrze nie ma wartość null, wystąpił błąd podczas tworzenia klastra, a nie powinny być używane.

#### <a name="record-batch-ai-training-credentials"></a>Rekord poświadczenia usługi Batch AI Training

Podczas oczekiwania dla alokacji klastra ukończyć Otwórz `settings.cfg` pliku z podkatalogu "Kod" ten projekt w wybranym w edytorze tekstu. Przy użyciu własnych poświadczeń, należy zaktualizować następujące zmienne:
- `bait_subscription_id` (identyfikator subskrypcji platformy Azure 36-znakowy)
- `bait_aad_client_id` (identyfikator aplikacji/klienta usługi Azure Active Directory wymienionych w sekcji "Wymagania wstępne")
- `bait_aad_secret` (tajny aplikacji usługi Azure Active Directory wymienionych w sekcji "Wymagania wstępne")
- `bait_aad_tenant` (identyfikator dzierżawy usługi Azure Active Directory wymienionych w sekcji "Wymagania wstępne")
- `bait_region` (na chwilę obecną, jedyną opcją jest: eastus)
- `bait_resource_group_name` (Grupa zasobów, wybrana wcześniej)

Po przypisaniu wartości te zmodyfikowane wiersze pliku settings.cfg powinien przypominać następujący tekst:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Zapisz i Zamknij `settings.cfg`.

Możesz teraz zamknąć okna interfejsu wiersza polecenia, w których są wykonywane polecenia tworzenia zasobów usługi Batch AI. Wszystkich dalszych krokach w tym samouczku Użyj interfejsu wiersza polecenia, uruchomionego z aplikacji Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Przygotuj środowisko wykonywania aplikacji Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Zarejestruj klastra HDInsight jako cel obliczenia Azure Machine Learning Workbench

Po zakończeniu tworzenia klastra HDInsight zarejestrować klastra jako obliczeniowego elementu docelowego w projekcie w następujący sposób:

1.  Należy wydać następujące polecenie z usługi Azure Machine Learning interfejsu wiersza polecenia:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    To polecenie dodaje dwa pliki `myhdi.runconfig` i `myhdi.compute`, do swojego projektu `aml_config` folderu.

1. Otwórz `myhdi.compute` pliku w swoim ulubionym edytorze tekstów. Modyfikowanie `yarnDeployMode: cluster` wiersz, aby odczytać `yarnDeployMode: client`, a następnie zapisz i zamknij plik.
1. Uruchom następujące polecenie, aby przygotować środowisko do użytku HDInsight:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Instalowanie zależności lokalne

Otwórz interfejs wiersza polecenia z aplikacji Azure Machine Learning Workbench i zainstaluj zależności niezbędne do wykonania lokalnego, wykonując następujące polecenie:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Pozyskiwanie danych i ich analiza

W tym scenariuszu zdjęcia lotnicze publicznie dostępnych danych z [National rolnictwa obrazach programu](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) rozdzielczością 1 miernika. Firma Microsoft ma wygenerowanych zestawów 224 pikseli x 224 pikseli pliki PNG przycięte z oryginalnych danych NAIP i posortowane zgodnie z ziemi Użyj etykiet z [National ziemi obejmują Database](https://www.mrlc.gov/nlcd2011.php). Przykładowy obraz z etykietą "Developed" jest wyświetlana w pełnym rozmiarze:

![Kafelek przykładowe opracowanych ziemi](media/scenario-aerial-image-classification/sample-tile-developed.png)

Zestawy zrównoważoną pod względem klasy ~ 44 k i 11 k obrazy są używane do szkolenia modelu i sprawdzanie poprawności, odpowiednio. Pokażemy, wdrażanie modelu w obrazie k ~ 67 zestawu dzielenie na mniejsze strony Middlesex hrabstwa, MA — Centrum rozwoju (NERD) i badań nowej Anglii głównej z firmy Microsoft. Aby uzyskać więcej informacji na temat jak zostały zbudowane tych zestawów obrazu, zobacz [repozytorium git zaskakująco równoległe Klasyfikacja obrazów](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Lokalizacja Middlesex hrabstwa, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Podczas instalacji zestawy obrazów z powietrza użytego w tym przykładzie zostały przeniesione do konta magazynu, który został utworzony. Szkolenia, weryfikacji i operacjonalizacji obrazy są wszystkie pliki PNG 224 pikseli x 224 pikseli przy rozdzielczości jeden piksel na metr kwadratowy. Szkolenia i sprawdzania poprawności obrazów mają został podzielony na podfoldery na podstawie ich etykiety Użyj ziemi. (Ziemi Użyj etykiet obrazów operacjonalizacji są nieznane i w wielu przypadkach niejednoznaczne; niektóre z tych obrazów zawierają wiele typów ziemi). Aby uzyskać więcej informacji na temat jak zostały zbudowane tych zestawów obrazu, zobacz [repozytorium git zaskakująco równoległe Klasyfikacja obrazów](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Aby wyświetlić przykład, że obrazy w usłudze Azure storage account (opcjonalnie):
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Wyszukaj nazwę konta magazynu, na pasku wyszukiwania u góry ekranu. Kliknij na swoim koncie magazynu w wynikach wyszukiwania.
2. Kliknij link "Blob", w okienku głównym konta magazynu.
3. Kliknij kontener o nazwie "train". Powinien zostać wyświetlony listę katalogów o nazwie zgodnej ze ziemi użycia.
4. Kliknij dowolny z tych katalogów, aby załadować listę obrazów, które zawiera.
5. Kliknij dowolny obraz, a następnie pobrać ją, aby wyświetlić obraz.
6. Jeśli to konieczne, należy kliknąć kontenerów o nazwie "test" i "middlesexma2016", aby wyświetlić ich zawartość również.

## <a name="modeling"></a>Modelowanie

### <a name="training-models-with-azure-batch-ai"></a>Szkolenie modeli przy użyciu usługi Azure Batch AI

`run_batch_ai.py` Skryptu w podfolderze "Code\02_Modeling" Projekt aplikacji Workbench jest używany do wysyłania zadania usługa Batch AI Training. To zadanie retrains klasyfikatora obraz DNN wybierany przez użytkownika (AlexNet lub siecią ResNet 18, wstępnie przetrenowane na sieci ImageNet). Głębokość ponownego szkolenia można również określić: ponownego trenowania tylko ostatnia warstwa sieci może zmniejszyć overfitting, gdy kilka próbek szkoleniowych dostępnych, podczas dostosowywania całej sieci (lub dla AlexNet, w pełni połączone warstwy) może prowadzić do modelu większa wydajność, gdy zestaw szkoleniowy jest wystarczająco duży.

Po zakończeniu zadania szkolenia ten skrypt zapisuje w magazynie obiektów blob modelu (wraz z pliku mapowania między modelu liczby całkowitej w danych wyjściowych i etykiety ciąg opisujący) i prognozy. Można wyodrębnić timecourse błąd stawki podwyższania za pośrednictwem epok szkolenia jest analizowany plik dziennika zadania PRZYNĘTY. Timecourse poprawy szybkości błąd jest rejestrowany wpis AML Workbench historii uruchamiania funkcji w celu przeglądania ich później.

Wybierz nazwę uczonego modelu, typ modelu, wstępnie przetrenowane i głębi ponownego trenowania. Zapis wybrane opcje w przypadku, gdy wskazane w następującym poleceniu zacznij ponownego trenowania, wykonując polecenie z usługi Azure ML interfejsu wiersza polecenia:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Oczekiwać, że usługa Azure Machine Learning, uruchom, aby zająć około pół godziny, aby zakończyć. Firma Microsoft zaleca uruchomienie kilku poleceń podobne (zmieniającego się Nazwa wyjściowego w modelu, typ modelu, wstępnie przetrenowane i ponownego trenowania głębokość) tak, aby porównać wydajność modeli skonfigurowanych pod kątem przy użyciu różnych metod.

### <a name="training-models-with-mmlspark"></a>Szkolenie modeli przy użyciu MMLSpark

`run_mmlspark.py` Skryptu w podfolderze "Code\02_Modeling" Projekt aplikacji Workbench jest używane do trenowania [MMLSpark](https://github.com/Azure/mmlspark) modeli klasyfikacji obrazów. Featurizes pierwszy skrypt szkolenia zestawu obrazów przy użyciu klasyfikatora obrazu, który DNN wstępnie przetrenowane na danych sieci ImageNet (AlexNet lub siecią ResNet warstwy 18). Skrypt następnie używa neural obrazów do szkolenia modelu MMLSpark (losowe lasu lub model regresji logistycznej) do klasyfikowania obrazów. Zestaw obrazów testu jest następnie neural i oceniane za pomocą uczonego modelu. Dokładność modelu prognozy dla zestawu testów jest obliczana, a zalogowany do usługi Azure Machine Learning Workbench w historii uruchamiania funkcji. Na koniec uczonego modelu MMLSpark i jej prognozy w zestawie testów są zapisywane do magazynu obiektów blob.

Wybierz nazwę unikatową danych wyjściowych modelu uczonego modelu, typ wstępnie przetrenowane modelu i typu modelu MMLSpark. Zapis wybrane opcje w przypadku, gdy wskazane w szablonie następujące polecenia, następnie rozpocząć ponownego trenowania, wykonując polecenie z usługi Azure ML interfejsu wiersza polecenia:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Dodatkowe `--sample_frac` parametru może służyć do nauczenia i przetestowania modelu przy użyciu podzestawu danych o dostępności. Za pomocą ułamek małą próbkę zmniejsza wymagania środowiska uruchomieniowego i pamięci, kosztem dokładności uczonego modelu. (Na przykład Uruchom z `--sample_frac 0.1` powinien zająć około 20 minut.) Aby uzyskać więcej informacji na ten temat i inne parametry uruchamiania `python Code\02_Modeling\run_mmlspark.py -h`.

Zachęcamy użytkowników, aby uruchomić ten skrypt kilka razy z różnymi parametrami, danych wejściowych. Następnie można porównać wydajność wynikowy modeli w aplikacji Azure Machine Learning Workbench w historii uruchamiania funkcji.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Porównywanie wydajności modeli za pomocą funkcji historii uruchamiania w aplikacji Workbench

Po wykonaniu dwóch lub więcej szkoleń uruchamia obu typów, przejdź do funkcji historii uruchamiania w aplikacji Workbench, klikając ikonę zegara, na pasku menu po lewej stronie. Wybierz `run_mmlspark.py` z listy skryptów po lewej stronie. Okienko ładuje, porównywanie dokładność zestawu testów dla wszystkich przebiegów. Aby wyświetlić więcej szczegółów, przewiń w dół i kliknij nazwę pojedyncze uruchomienie.

## <a name="deployment"></a>Wdrożenie

Aby zastosować jedną wytrenowane modele powietrza obrazów fragmentacji Middlesex hrabstwa, MA na HDInsight przy użyciu zdalnego wykonania, Wstaw nazwę modelu odpowiednią do następującego polecenia, a następnie uruchomić go:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Dodatkowe `--sample_frac` parametru może służyć do obsługi operacji modelu przy użyciu podzestawu danych o dostępności. Za pomocą ułamek małą próbkę zmniejsza wymagania środowiska uruchomieniowego i pamięci kosztem prognozowania informacje były kompletne. Aby uzyskać więcej informacji na ten temat i inne parametry uruchamiania `python Code\03_Deployment\batch_score_spark -h`.

Ten skrypt zapisuje określane są przewidywania modelu do swojego konta magazynu. Prognozy są tym może sprawdzić, jak opisano w następnej sekcji.

## <a name="visualization"></a>Wizualizacja

"W modelu prognozowania, analizy" notesu programu Jupyter w podfolderze "Code\04_Result_Analysis" Projekt aplikacji Workbench wizualizuje modelu prognozy. Obciążenia, a następnie uruchom Notes w następujący sposób:
1. Otwórz projekt w aplikacji Workbench, a następnie kliknij przycisk w folderze ikonę ("Files") wraz z menu po lewej stronie, aby załadować listy zawartości katalogu.
2. Przejdź do podfolderu "Code\04_Result_Analysis" i kliknij Notes o nazwie "Model prognozowania analizy". Renderowanie w wersji zapoznawczej, notesu powinna być wyświetlana.
3. Kliknij przycisk "Uruchom serwer notesu" załadować notesu.
4. W pierwszej komórki wprowadź nazwę modelu, którego wyniki chcesz analizować symbol.
5. Kliknij pozycję "komórki -> Uruchom wszystkie" do wykonania wszystkich komórek w notesie.
6. Przeczytaj, wraz z notesu, aby dowiedzieć się więcej na temat analizy i wizualizacje, które stanowi.

## <a name="cleanup"></a>Czyszczenie
Po zakończeniu przykładu, zaleca się usunięcie wszystkich zasobów utworzonych za pomocą następującego polecenia z interfejsu wiersza polecenia platformy Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Dokumentacja

- [Repozytorium zaskakująco równoległe Klasyfikacja obrazów](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - W tym artykule opisano zestaw danych konstrukcji od aplikacje dostępne bezpłatnie i etykiety
- [MMLSpark](https://github.com/Azure/mmlspark) repozytorium GitHub
   - Zawiera dodatkowe przykłady szkolenia modeli oraz ocena o MMLSpark

## <a name="conclusions"></a>Wnioski

Usługa Azure Machine Learning Workbench pomaga analitykom łatwe wdrażanie swój kod w zdalnym obliczeniowych elementów docelowych. W tym przykładzie kod szkolenia MMLSpark lokalny został wdrożony w zakresie wykonywania zdalnego w klastrze usługi HDInsight, a lokalnego skryptu uruchomione zadania szkolenia w klastrze usługi Azure Batch AI GPU. Usługa Azure Machine Learning Workbench w historii uruchamiania funkcji śledzone wydajność wielu modeli i pomogło nam to identyfikowanie najdokładniejszych modelu. Funkcja notesów programu Jupyter w aplikacji Workbench pomogła, wizualizowanie naszych modeli prognoz w środowisku interakcyjnego, graficznego.

## <a name="next-steps"></a>Kolejne kroki
Aby szczegółowe informacje w tym przykładzie:
- W aplikacji Azure Machine Learning Workbench w historii uruchamiania funkcji kliknij przycisk symbole koła zębatego, aby wybrać, które wykresów i metryki są wyświetlane.
- Sprawdź przykładowe skrypty do instrukcji podczas wywoływania `run_logger`. Upewnij się, że rozumiesz, jak jest rejestrowane wszystkie metryki.
- Sprawdź przykładowe skrypty do instrukcji podczas wywoływania `blob_service`. Sprawdź, że rozumiesz sposób przeszkolone modele i prognozy są przechowywane i pobierane z chmury.
- Zapoznaj się z zawartość kontenerów utworzone w ramach konta magazynu obiektów blob. Upewnij się, że rozumiesz, której skrypt lub polecenie jest odpowiedzialny za tworzenie każdej grupy plików.
- Zmodyfikuj skrypt szkoleniowy, to w opracowywaniu innego typu modelu MMLSpark lub zmienić hiperparametrów modelu. Funkcja historii uruchamiania służy do określenia, czy zmiany zwiększanie lub zmniejszanie dokładności modelu.
