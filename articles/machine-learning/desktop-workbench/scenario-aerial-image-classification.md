---
title: Klasyfikacja satelitarnej obrazu | Dokumentacja firmy Microsoft
description: Instrukcje w świecie rzeczywistym scenariuszu po klasyfikacji satelitarnej obrazu
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: 74cb72b4ddfbeace5e2409dfac4f4b6bf21ffa25
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="aerial-image-classification"></a>Klasyfikacja satelitarnej obrazu

W tym przykładzie pokazano, jak używać usługi Azure Machine Learning Workbench do koordynowania rozproszonej szkolenia i operationalization modele klasyfikacji obrazu. Używamy dwa podejścia do trenowania: i uściślenie przy użyciu sieci neuronowej głębokość [AI usługi partia zadań Azure](https://docs.microsoft.com/azure/batch-ai/) klastra procesora GPU i (ii) przy użyciu [uczenia maszynowego Microsoft platformy Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) pakietu obrazy featurize przy użyciu modeli CNTK pretrained i do uczenia klasyfikatory przy użyciu funkcji pochodnych. Następnie stosujemy przeszkolone modeli w sposób równoległy zestawów duży obraz w chmurze przy użyciu [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) klastra, co pozwala firmie Microsoft w celu skalowania szybkości szkolenia i operationalization przez dodanie lub usunięcie węzłów procesu roboczego.

W tym przykładzie wyróżniono dwa podejścia do transferu uczenia, które korzysta z pretrained modele, aby uniknąć kosztów szkolenia sieci neuronowej głębokość od początku. Zwykle ponownego trenowania sieci neuronowej głębokość wymaga obliczeniowej procesora GPU, ale może prowadzić do większej dokładności, gdy zestaw szkoleniowy jest wystarczająco duża. Uczenie prostego klasyfikatora na obrazach featurized nie wymaga obliczeniowej procesora GPU, jest z założenia szybka i skalowalna arbitralnie i pasuje do mniej parametrów. Ta metoda w związku z tym jest doskonałym rozwiązaniem, gdy kilka przykładów szkolenia są dostępne —, jak często w przypadku dla przypadków użycia niestandardowego. 

Klastra Spark w tym przykładzie ma 40 węzłów procesu roboczego i koszty ~$40/hr działanie; zasoby klastra AI partii obejmują osiem GPU i kosztów ~$10/hr do działania. Kończenie pracy tego przewodnika trwa około trzech godzin. Gdy skończysz, postępuj zgodnie z instrukcjami czyszczenia do usunięcia zasobów, które zostały utworzone i Zatrzymaj naliczania opłat.

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii

W tym scenariuszu rzeczywistych publicznego repozytorium GitHub zawiera wszystkich materiałów, w tym przykłady kodu, potrzebne w tym przykładzie:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Opis przypadków użycia

W tym scenariuszu firma Microsoft uczenia modele uczenia maszyny do klasyfikowania typu pokazano satelitarnej obrazy x miernika 224 miernika 224 powierzchni ziemi. Modele klasyfikacji Użyj ziemi może służyć do śledzenia urbanizacji, wycinanie lasów, utratą podmokłych i innych głównych trendów środowiska przy użyciu okresowo zbierane satelitarnej obrazów. Firma Microsoft została przygotowana szkolenia i sprawdzania poprawności zestawy obrazu na podstawie z obrazami z USA National rolnictwa obrazów programu i ziemi korzystanie z etykiet opublikowane przez amerykański Baza danych obejmują ziemi National. Przykład obrazów w każdej klasie Użyj ziemi przedstawiono poniżej:

![Przykład regiony dla każdego ziemi Użyj etykiety](media/scenario-aerial-image-classification/example-labels.PNG)

Po szkolenia i sprawdzanie poprawności modelu klasyfikatora zostaną zastosowane go do obrazów satelitarnej spanning Województwo Middlesex, MA--Research nowej Anglii głównej z firmy Microsoft & Center Programowanie (NERD) — aby zademonstrować, jak te modele może służyć do badania tendencji zurbanizowane programowanie.

Aby wygenerować klasyfikatora obrazu przy użyciu transferu uczenia, analityków danych często utworzenia wielu modeli z zakresu metod i wybierz największą wydajność modelu. Azure Machine Learning Workbench można ułatwić danych służące koordynować szkolenia w środowiskach różnych obliczeń, śledzenie i porównać wydajność wielu modeli i dotyczą wybranego modelu dużych zestawów danych w chmurze.

## <a name="scenario-structure"></a>Scenariusz — struktura

W tym przykładzie pretrained modele i dane obrazu są trzymane w koncie magazynu platformy Azure. Klaster usługi Azure HDInsight Spark odczytuje tych plików i tworzy model klasyfikacji obrazu przy użyciu MMLSpark. Trenowanego modelu i jego prognoz następnie są zapisywane na koncie magazynu, gdzie mogą być przeanalizowane i wizualizowane przez notesu Jupyter uruchomionej na komputerze lokalnym. Azure Machine Learning Workbench koordynuje zdalne wykonywanie skryptów w klastrze Spark. Pozwala również śledzić metryki dokładność dla wielu modeli uczenia przy użyciu różnych metod, co pozwala użytkownikowi na wybranie najbardziej wydajność modelu.

![Schemat dla scenariusza rzeczywistych klasyfikacji satelitarnej obrazu](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Te instrukcje krok po kroku najpierw omówiono tworzenie i przygotowania kontem magazynu platformy Azure i klastra Spark, w tym instalacji transfer i zależności w danych. Następnie opisują sposób uruchamiania zadań szkolenia i porównywać wynikowy modeli. Na koniec one pokazano, jak zastosować wybrany model do zestawu duży obraz w klastrze Spark i Przeanalizuj wyniki prognozowania lokalnie.


## <a name="set-up-the-execution-environment"></a>Konfigurowanie środowiska wykonawczego

Poniższe instrukcje pomagają konfigurowania środowiska wykonawczego w tym przykładzie.

### <a name="prerequisites"></a>Wymagania wstępne
- [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne)
    - Spowoduje utworzenie klastra Spark w usłudze HDInsight z 40 węzłów procesu roboczego (całkowita liczba rdzeni 168). Upewnij się, że konto użytkownika ma za mało dostępnych rdzeni, przeglądając "użycia + przydziały" kartę dla Twojej subskrypcji w portalu Azure.
       - Jeśli masz mniej dostępne rdzenie może zmodyfikować szablon klastra HDInsight zmniejszyć liczbę procesów roboczych udostępnione. Odpowiednie instrukcje są wyświetlane w sekcji "Tworzenie klastra Spark w usłudze HDInsight".
    - W tym przykładzie jest tworzony klaster szkolenia AI partii z dwóch NC6 (1 procesora GPU, 6 vCPU) maszyn wirtualnych. Upewnij się, że konto użytkownika ma za mało dostępnych rdzeni w regionie wschodnie stany USA, przeglądając "użycia + przydziały" kartę dla Twojej subskrypcji w portalu Azure.
- [Środowisko robocze usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md)
    - Postępuj zgodnie z [zainstalować i utworzyć szybkiego startu](../service/quickstart-installation.md) zainstalować Azure Machine Learning Workbench i utworzyć eksperymentowania oraz konta zarządzania w modelu.
- [Partii AI](https://github.com/Azure/BatchAI) Python SDK i Azure CLI 2.0
    - Wypełnij następujące sekcje w [partii AI przepisami README](https://github.com/Azure/BatchAI/tree/master/recipes):
        - "Wymagania wstępne"
        - "Utwórz i aplikacji usługi Azure Active Directory (AAD)"
        - "Zarejestruj dostawców zasobów BatchAI" (w obszarze "Uruchom przepisami przy użyciu usługi Azure CLI 2.0")
        - "Instalowanie klienta zarządzania partii zadań Azure AI"
        - "Instalowanie Azure Python SDK"
    - Rekord Identyfikatora klienta, klucz tajny i identyfikator dzierżawcy aplikacji usługi Azure Active Directory, które są kierowane do utworzenia. Te poświadczenia zostaną użyte w dalszej części tego samouczka.
    - Opracowywania tego tekstu usługi Azure Machine Learning Workbench i AI usługi partia zadań Azure używają oddzielnych rozwidlenia 2.0 interfejsu wiersza polecenia platformy Azure. Dla jasności nazywamy Workbench wersji interfejsu CLI jako "infrastruktury CLI uruchamiana z usługi Azure Machine Learning Workbench" i (w tym partii AI) ogólne wersji "Azure CLI 2.0".
- [Narzędzie AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), narzędzie do koordynowania transferu plików między kontami magazynu Azure w warstwie bezpłatna
    - Upewnij się, że folder zawierający plik wykonywalny AzCopy znajduje się w zmiennej środowiskowej PATH w systemie. (Dostępne są instrukcje na temat modyfikowania zmiennych środowiskowych [tutaj](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Klient SSH; Firma Microsoft zaleca [PuTTY](http://www.putty.org/).

W tym przykładzie testowania na komputerach z systemem Windows 10; można go uruchomić z dowolnej maszyny systemu Windows, w tym maszyny wirtualne nauki danych Azure. 2.0 interfejsu wiersza polecenia platformy Azure został zainstalowany z Instalatora MSI zgodnie z [tych instrukcji](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Drobne zmiany mogą być wymagane (na przykład zmiany filepaths) podczas uruchamiania w tym przykładzie na macOS.

### <a name="set-up-azure-resources"></a>Konfigurowanie zasobów Azure

W tym przykładzie wymaga klastra Spark w usłudze HDInsight i konto magazynu Azure do odpowiednich plików hosta. Wykonaj te instrukcje, aby utworzyć tych zasobów w nowej grupy zasobów platformy Azure:

#### <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz Azure Machine Learning Workbench
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Satelitarnej Klasyfikacja obrazu" i wybierz szablon
5.  Kliknij przycisk **Utwórz**
 
#### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

1. Po załadowaniu projektu w konsoli usługi Azure Machine Learning Workbench, Otwórz interfejs wiersza polecenia (CLI), klikając plik -> Otwórz wiersz polecenia.
    Użyj tej wersji interfejsu wiersza polecenia dla większości samouczka. (W przypadku, gdy wskazane, należy otworzyć inna wersja interfejsu wiersza polecenia, aby przygotować zasobów AI partii.)

1. Przy użyciu interfejsu wiersza polecenia należy zalogować się za pomocą następującego polecenia do konta platformy Azure:

    ```
    az login
    ```

    Zostanie wyświetlona prośba o odwiedź adres URL i typ podanego kodu tymczasowej; Witryna sieci Web żądania poświadczeń konta platformy Azure.
    
1. Po zakończeniu logowania, wróć do interfejsu wiersza polecenia i uruchom następujące polecenie, aby określić, które subskrypcji platformy Azure są dostępne do konta platformy Azure:

    ```
    az account list
    ```

    To polecenie wyświetla listę wszystkich subskrypcji skojarzonych z Twoim kontem platformy Azure. Znaleźć Identyfikatora subskrypcji, którego chcesz użyć. Zapis identyfikator subskrypcji wskazana w poniższym poleceniu następnie ustaw aktywną subskrypcją, wykonując polecenie:

    ```
    az account set --subscription [subscription ID]
    ```

1. Azure zasoby utworzone w tym przykładzie są przechowywane razem w grupie zasobów platformy Azure. Wybierz nazwę grupy zasobów unikatowy i zapisuje je w przypadku, gdy wskazane, wykonujących zarówno poleceń, aby utworzyć grupy zasobów platformy Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Tworzenie konta magazynu

Teraz utworzymy konta magazynu, że hosty projektu pliki, które muszą być dostępne przez HDInsight Spark.

1. Wybierz nazwę konta magazynu unikatowy i zapisują wskazanych w następujących miejscach `set` polecenia, a następnie utwórz konto magazynu platformy Azure, wykonując oba polecenia:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Listy kluczy konta magazynu, uruchamiając następujące polecenie:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Zarejestruj wartość `key1` jako klucza magazynu w następujące polecenie, a następnie uruchom polecenie do przechowywania wartości.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Tworzenie udziału plików o nazwie `baitshare` na koncie magazynu przy użyciu następującego polecenia:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. W w ulubionym edytorze tekstów, obciążenia `settings.cfg` plik z projektu usługi Azure Machine Learning Workbench podkatalogu "Code" i Wstaw nazwę konta magazynu i klucz wskazane. Zapisz i Zamknij `settings.cfg` pliku.
1. Jeśli nie zostało to jeszcze zrobione, Pobierz i zainstaluj [AzCopy](http://aka.ms/downloadazcopy) narzędzia. Sprawdź, czy plik wykonywalny AzCopy jest na ścieżce systemowej, wpisując "AzCopy" i naciskając klawisz Enter, aby wyświetlić jego dokumentacji.
1. Wydaj następujące polecenia, aby skopiować wszystkie dane przykładowe, pretrained modeli i skryptów uczenia modelu do odpowiedniej lokalizacji na koncie magazynu:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Oczekiwany transferu plików do zająć około jednej godziny. Podczas oczekiwania, możesz przejść do poniższej sekcji: Otwórz innego interfejsu wiersza polecenia za pomocą narzędzia Workbench i ponownie zdefiniować zmienne tymczasowe może być konieczne.

#### <a name="create-the-hdinsight-spark-cluster"></a>Tworzenie klastra Spark w usłudze HDInsight

Nasze zalecana metoda tworzenia klastra usługi HDInsight używa szablonu Menedżera zasobów klastra Spark w usłudze HDInsight zawarte w podfolderze "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" tego projektu.

1. Szablon klastra Spark w usłudze HDInsight jest "template.json" plik w podfolderze "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" tego projektu. Domyślnie szablon tworzy klaster Spark z 40 węzłów procesu roboczego. Jeśli należy dostosować tę liczbę, otwórz szablon w w ulubionym edytorze tekstów i Zastąp wszystkie wystąpienia "40" numer węzła procesu roboczego wybranych przez użytkownika.
    - Mogą wystąpić błędy braku pamięci później, jeśli jest mniejsza liczba węzłów procesu roboczego, którą wybierzesz. Zwalczania błędów pamięci, mogą uruchamiać skrypty szkolenia i operationalization w podzestawie dostępnych danych, zgodnie z opisem w dalszej części tego dokumentu.
2. Wybierz unikatową nazwę i hasło dla usługi HDInsight klastra i zapisanie ich w przypadku, gdy wskazane następujące polecenie: następnie utworzyć klaster, wysyłając polecenia:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Wdrażanie klastra może potrwać do 30 minut (w tym wykonywania akcji skryptu i udostępniania).

### <a name="set-up-batch-ai-resources"></a>Konfigurowanie zasobów partii AI

Podczas oczekiwania dla transferu plików konta magazynu i wdrożenie klastra Spark do wykonania, można przygotować klaster partii AI plików serwera plików NFS (Network) i procesora GPU. Otwórz okno wiersza polecenia Azure CLI 2.0 i uruchom następujące polecenie:

```
az --version 
```

Upewnij się, że `batchai` jest na liście zainstalowanych modułów. Jeśli nie używasz innego interfejsu wiersza polecenia (na przykład, jeden otworzyć za pomocą narzędzia Workbench).

Następnie sprawdź rejestrację dostawcy zostało pomyślnie ukończone. (Rejestrację dostawcy wymaga do 15 minut i mogą być uruchomione, jeśli ostatnio wykonana [instrukcje dotyczące konfigurowania AI partii](https://github.com/Azure/BatchAI/tree/master/recipes).) Upewnij się, że zarówno "Microsoft.Batch" i "Microsoft.BatchAI" są wyświetlane ze stanem "Zarejestrowanej" w danych wyjściowych z następującego polecenia:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Jeśli nie, uruchom następujące dostawcy polecenia rejestracji, a następnie zaczekaj ~ 15 minut do rejestracji w celu ukończenia.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Zmodyfikuj następujące polecenia, aby zastąpić wartości używanego wcześniej podczas tworzenia konta grupy i przechowywanie zasobów wyrażenia w nawiasach kwadratowych. Następnie należy zapisać wartości jako zmienne, wykonując polecenia:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Zidentyfikuj folder zawierający projekt usługi Azure Machine Learning (na przykład `C:\Users\<your username>\AzureML\aerialimageclassification`). Zamień wartość w nawiasach kwadratowych filepath folderu (z nie końcowy ukośnik odwrotny) i uruchom polecenie:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Teraz można przystąpić do tworzenia partii AI zasoby potrzebne w tym samouczku.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Przygotowywanie serwera plików wsadowych sieci AI

Klaster AI partii uzyskuje dostęp do danych szkoleniowych na sieciowym serwerze plików. Dostęp do danych może być several-fold szybciej podczas uzyskiwania dostępu do plików z systemu plików NFS, a udział plików Azure lub usługi Azure Blob Storage.

1. Należy wydać następujące polecenie, aby utworzyć serwer plików sieciowych:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Sprawdź stan inicjowania obsługi administracyjnej sieci serwera plików za pomocą następującego polecenia:

    ```
    az batchai file-server list
    ```

    "ProvisioningState" sieciowego serwera plików o nazwie "landuseclassifier" jest "powiodło się", jest gotowy do użycia. Oczekiwane inicjowania obsługi administracyjnej potrwać około pięciu minut.
1. Znajdź adres IP Twojego systemu plików NFS w wyniku poprzedniego polecenia (właściwość "fileServerPublicIp" w obszarze "mountSettings"). Zapis IP adresów wskazana następujące polecenie, a następnie zapisać wartość, wykonując polecenie:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Za pomocą ulubionego narzędzia SSH (następujące przykładowe polecenia używa [PuTTY](http://www.putty.org/)), wykonanie tego projektu `prep_nfs.sh` skrypt na NFS przeniesienie obrazu szkolenia i sprawdzanie poprawności ustawia.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Nie trzeba się troszczyć Jeśli aktualizacje postępu pobierania i wyodrębniania danych w oknie powłoki tak szybko przewinąć są nieczytelne.

W razie potrzeby można potwierdzić, że transfer danych podjęła zgodnie z planem logowanie do serwera plików z ulubionego narzędzia SSH i sprawdzając `/mnt/data` zawartości katalogu. Użytkownik stwierdzi, że dwa foldery training_images i validation_images, każdy z nich zawiera z podfoldery o nazwach zgodnie z ziemi używanie kategorii.  Zestawy szkoleniowe i weryfikacja powinna zawierać ~ 44 k i obrazów k ~ 11, odpowiednio.

#### <a name="create-a-batch-ai-cluster"></a>Tworzenie klastra partii AI

1. Tworzenia klastra, wykonując następujące polecenie:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Użyj następującego polecenia w celu sprawdzenia, czy stanu do udostępniania klastra:

    ```
    az batchai cluster list
    ```

    Stan alokacji dla klastra o nazwie "landuseclassifier" zmiany z rozmiaru do stała, jest możliwość przesyłania zadań. Jednak zadania są uruchamiane działać do chwili opuścić "przygotowywania" stan wszystkich maszyn wirtualnych w klastrze. Jeśli właściwości "errors" klastra nie jest zerowa, wystąpił błąd podczas tworzenia klastra i nie powinna być używana.

#### <a name="record-batch-ai-training-credentials"></a>Poświadczenia szkolenia AI partii rekordów

Podczas oczekiwania alokacji klastra zakończyć, otwórz `settings.cfg` pliku z podkatalogu 'Code' tego projektu w wybranym w edytorze tekstu. Zaktualizuj następujące zmienne przy użyciu własnych poświadczeń:
- `bait_subscription_id` (36 znaków identyfikator subskrypcji platformy Azure)
- `bait_aad_client_id` (identyfikator aplikacji/klienta usługi Azure Active Directory wymienionych w sekcji "Wymagania wstępne")
- `bait_aad_secret` (tajny aplikacji usługi Azure Active Directory wymienionych w sekcji "Wymagania wstępne")
- `bait_aad_tenant` (identyfikator dzierżawy usługi Azure Active Directory wymienionych w sekcji "Wymagania wstępne")
- `bait_region` (opracowywania tego tekstu jest jedyną opcją: eastus)
- `bait_resource_group_name` (grupę zasobów, które wcześniej wybrano)

Po przypisaniu te wartości zmodyfikowanych wierszy pliku settings.cfg powinien przypominać następujący tekst:

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

Może teraz zamknąć okno interfejsu wiersza polecenia, w którym wykonywane polecenia tworzenia zasobu AI partii. Wszystkie dalszych kroków w tym samouczku Użyj interfejsu wiersza polecenia uruchamiane z usługi Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Przygotowanie środowiska wykonawczego Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Zarejestruj jako obiekt docelowy obliczeniowych Azure Machine Learning Workbench klastra usługi HDInsight

Po zakończeniu tworzenia klastra usługi HDInsight zarejestrować klastra jako element docelowy obliczeniowe dla projektu w następujący sposób:

1.  Wydać z usługi Azure Machine Learning interfejsu wiersza polecenia następujące polecenie:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    To polecenie dodaje dwa pliki `myhdi.runconfig` i `myhdi.compute`, do projektu `aml_config` folderu.

1. Otwórz `myhdi.compute` plik w edytorze tekstu. Modyfikowanie `yarnDeployMode: cluster` wiersza, aby odczytać `yarnDeployMode: client`, Zapisz i zamknij plik.
1. Uruchom następujące polecenie, aby przygotować swoje środowisko HDInsight do użycia:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Zainstaluj zależności lokalne

Otwórz interfejs wiersza polecenia z usługi Azure Machine Learning Workbench i zainstaluj zależności niezbędne do wykonania lokalnego, wykonując następujące polecenie:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Pozyskiwanie danych i ich analiza

W tym scenariuszu publicznie dostępnych obrazów satelitarnej danych z [National rolnictwa obrazów programu](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) rozdzielczością 1 miernika. Wygenerowano zestawy 224 pikseli x 224 pikseli pliki PNG przycięte z oryginalnych danych NAIP i posortowane według ziemi Użyj etykiet z [National bazy ziemi obejmują danych](https://www.mrlc.gov/nlcd2011.php). Obraz przykładowej z etykietą "Developed" jest wyświetlany w pełnym rozmiarze:

![Na kafelku próbki rozwinięte ziemi](media/scenario-aerial-image-classification/sample-tile-developed.png)

Zestawy zrównoważonym klasy ~ 44 k i 11 k obrazy służą do uczenia modelu i sprawdzania poprawności, odpowiednio. Przedstawiony zestawu wdrażania modelu w obrazie k ~ 67 dzielenie na mniejsze strony Województwo Middlesex, MA--nowej Anglii badań i rozwoju (NERD) Centrum głównej z firmy Microsoft. Aby uzyskać więcej informacji na jak te zestawy obrazu zostały wykonane, zobacz [repozytorium git Embarrassingly równoległych klasyfikacji obrazu](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Lokalizacja Middlesex Województwo, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Podczas instalacji ustawia satelitarnej obraz używany w tym przykładzie zostały przeniesione do utworzonego konta magazynu. Szkolenie, weryfikacji i obrazy operationalization są wszystkie pliki PNG z rozdzielczością jeden piksel za metr kwadratowy 224 pikseli x 224 pikseli. Obrazy szkolenia i sprawdzania poprawności ma zostały podzielone na podfoldery na podstawie ich ziemi Użyj etykiety. (Ziemi Użyj etykiet obrazów operationalization są nieznane i w wielu przypadkach niejednoznaczne; niektóre z tych obrazów zawierają wiele typów ziemi). Aby uzyskać więcej informacji na jak te zestawy obrazu zostały wykonane, zobacz [repozytorium git Embarrassingly równoległych klasyfikacji obrazu](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Aby wyświetlić przykład obrazów w magazynie Azure konta (opcjonalnie):
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Wyszukaj nazwę konta magazynu na pasku wyszukiwania wzdłuż górnej części ekranu. Kliknij konto magazynu w wynikach wyszukiwania.
2. Kliknij łącze "Blob" w okienku głównym na koncie magazynu.
3. Polecenie kontenera o nazwie "pociągu." Należy wyświetlić listę katalogów o nazwie zgodnie z ziemi użycia.
4. Kliknij dowolną z tych katalogów można załadować listy obrazów, które zawiera.
5. Kliknij dowolny obraz i pobierz go, aby wyświetlić obraz.
6. W razie potrzeby kliknij kontenery o nazwie "test" i "middlesexma2016", aby wyświetlić ich zawartość również.

## <a name="modeling"></a>Modelowanie

### <a name="training-models-with-azure-batch-ai"></a>Modele uczenia AI usługi partia zadań Azure

`run_batch_ai.py` Skryptu w podfolderze "Code\02_Modeling" projektu Workbench służy do wysyłania zadania wsadowego AI szkolenia. To zadanie retrains klasyfikatora obrazu DNN wybierany przez użytkownika (AlexNet lub 18 ResNet pretrained na ImageNet). Głębokość ponownego trenowania można również określić: ponownego trenowania tylko ostatnią warstwą sieci może zmniejszyć overfitting kilka przykładów szkolenia dostępnych, podczas dostosowywania całej sieci (lub, w przypadku AlexNet, pełni połączonych warstwy) może prowadzić do większa modelu wydajność, gdy zestaw szkoleniowy jest wystarczająco duża.

Po zakończeniu zadania szkolenia, ten skrypt zapisuje modelu (wraz z plikiem mapowanie między modelu całkowitą w danych wyjściowych i etykiety ciąg opisujący) i prognozy do magazynu obiektów blob. Zadanie PRZYNĘTA plik dziennika jest analizowana wyodrębnić timecourse poprawy szybkości błąd za pośrednictwem epok szkolenia. Timecourse poprawy szybkości błąd jest rejestrowany AML Workbench Historia uruchomień funkcji w celu przeglądania ich później.

Wybierz nazwę uczonego modelu, typ modelu pretrained i ponownego trenowania głębokość. Zapis opcje wskazana w poniższym poleceniu zacznij ponownego trenowania, wykonując polecenie z usługi Azure ML interfejsu wiersza polecenia:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Oczekiwane uczenia maszynowego Azure Uruchom niezbędne do wykonania około pół godziny. Zaleca się uruchomienie kilku poleceń podobne (zróżnicowanie nazwę modelu danych wyjściowych, typ modelu pretrained i ponownego trenowania głębokość) tak, aby można było porównać wydajności modeli uczenia z różnych metod.

### <a name="training-models-with-mmlspark"></a>Modele szkolenia MMLSpark

`run_mmlspark.py` Skryptu w podfolderze "Code\02_Modeling" projektu Workbench służy do uczenia [MMLSpark](https://github.com/Azure/mmlspark) modelu klasyfikacji obrazu. Skrypt featurizes pierwszy szkolenia zestawu obrazów przy użyciu klasyfikatora DNN pretrained obrazu w zestawie danych ImageNet (AlexNet lub ResNet warstwy 18). Skrypt używa następnie obrazy featurized do uczenia modelu MMLSpark (losowe lasu lub modelu Regresja logistyczna) do klasyfikowania obrazów. Zestaw testów obrazu jest następnie featurized i oceniane z trenowanego modelu. Dokładność prognoz modelu w zestawie testów jest obliczana i rejestrowany funkcji Historia uruchomień Azure Machine Learning Workbench. Na koniec trenowanego modelu MMLSpark i jego prognoz dla zestawu testu są zapisywane do magazynu obiektów blob.

Wybierz nazwę modelu danych wyjściowych unikatowy trenowanego modelu, typ pretrained modelu i typu modelu MMLSpark. Zapis opcje wskazana w szablonie następujące polecenia zacznij ponownego trenowania, wykonując polecenie z usługi Azure ML interfejsu wiersza polecenia:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Dodatkowe `--sample_frac` parametru może służyć do nauczenia i przetestowania modelu z podzbioru dostępnych danych. Przy użyciu ułamek małej przykładowej zmniejsza wymagania środowiska uruchomieniowego i pamięci kosztem dokładność trenowanego modelu. (Na przykład przebiegu o `--sample_frac 0.1` może potrwać około 20 minut.) Aby uzyskać więcej informacji na ten temat oraz innych parametrów, uruchom `python Code\02_Modeling\run_mmlspark.py -h`.

Zaleca się uruchomić ten skrypt kilka razy z różnych parametrów wejściowych. Następnie można porównywać wydajności wynikowy modeli w funkcji Uruchom historii Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Porównanie wydajności modelu przy użyciu funkcji Workbench Uruchom historii

Po wykonaniu dwóch lub więcej szkoleń uruchamia obu typów, przejdź do funkcji Uruchom historii w Workbench, klikając ikonę zegara na pasku menu po lewej stronie. Wybierz `run_mmlspark.py` z listy skryptów po lewej stronie. Okienko ładuje porównanie dokładność zestaw testów dla wszystkich działań. Aby wyświetlić więcej szczegółów, przewiń w dół i kliknij nazwę indywidualnego uruchomienia.

## <a name="deployment"></a>Wdrożenie

Aby zastosować jedną przeszkolone modeli satelitarnej obrazy Ustawianie widoku kafelków Województwo Middlesex, agenta zarządzania przy użyciu zdalne wykonywanie kodu w usłudze HDInsight, Wstaw nazwę modelu odpowiednie do następującego polecenia i wykonaj go:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Dodatkowe `--sample_frac` parametru może służyć do operacjonalizacji modelu z podzbioru dostępnych danych. Przy użyciu ułamek małej przykładowej zmniejsza wymagania środowiska uruchomieniowego i pamięci kosztem kompletności prognozowania. Aby uzyskać więcej informacji na ten temat oraz innych parametrów, uruchom `python Code\03_Deployment\batch_score_spark -h`.

Ten skrypt zapisuje prognoz modelu do swojego konta magazynu. Prognozy mogą być zbadana, zgodnie z opisem w następnej sekcji.

## <a name="visualization"></a>Wizualizacja

Notesu Jupyter "Model analizy prognozowania" w podfolderze "Code\04_Result_Analysis" projektu Workbench wizualizuje modelu prognozy. Obciążenia i uruchom notesu w następujący sposób:
1. Otwórz projekt w Workbench i ikonę w folderze ("Files") wraz z menu po lewej stronie, aby załadować listy zawartości katalogu.
2. Przejdź do podfolderu "Code\04_Result_Analysis" i kliknij na Notes o nazwie "Model analizy prognozowania". Powinien zostać wyświetlony renderowania podglądu, notesu.
3. Kliknij przycisk "Start notesu Server" załadować notesu.
4. W pierwszej komórki wprowadź nazwę modelu, którego wyniki chcesz przeanalizować wskazanych miejscach.
5. Kliknij pozycję "komórki -> Uruchom wszystkie" do wykonania wszystkich komórek w notesie.
6. Odczyt wraz notesu, aby dowiedzieć się więcej na temat analizy i wizualizacje, które stanowi.

## <a name="cleanup"></a>Czyszczenie
Po zakończeniu przykładzie, zaleca się usunąć wszystkie zasoby, które zostały utworzone, wykonując następujące polecenie z interfejsu wiersza polecenia platformy Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Dokumentacja

- [Repozytorium Embarrassingly równoległych klasyfikacji obrazu](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - W tym artykule opisano budowa zestawu danych z bezpłatnych obrazów i etykiety
- [MMLSpark](https://github.com/Azure/mmlspark) repozytorium GitHub
   - Zawiera dodatkowe przykłady modelu uczenie i Ewaluacja z MMLSpark

## <a name="conclusions"></a>Wnioski

Azure Machine Learning Workbench pomaga analityków danych, łatwe wdrażanie ich kodu dla zdalnego obliczeniowych elementów docelowych. W tym przykładzie kod lokalny szkolenia MMLSpark została wdrożona dla wykonania zdalnego w klastrze usługi HDInsight i lokalnego skryptu uruchomione zadania szkolenia w klastrze GPU AI usługi partia zadań Azure. Funkcja historii wykonywania Azure Machine Learning Workbench w śledzić wydajność wielu modeli i pomogły zidentyfikować najdokładniejszych modelu. Funkcja notesów Jupyter w Workbench pomogła wizualizacji prognoz naszych modeli w środowisku interaktywnych, graficznego.

## <a name="next-steps"></a>Kolejne kroki
Umożliwiają lepsze zapoznanie się w tym przykładzie:
- W funkcji Uruchom historii Azure Machine Learning Workbench kliknij symbole koło zębate, aby wybrać, które wykresów i metryki są wyświetlane.
- Sprawdź przykładowe skrypty dla instrukcji wywoływania `run_logger`. Sprawdź, że rozumiesz, jak są rejestrowane wszystkie metryki.
- Sprawdź przykładowe skrypty dla instrukcji wywoływania `blob_service`. Sprawdź, że rozumiesz, jak przeszkolone modeli i Prognozowanie są przechowywane i pobierane z chmury.
- Przejrzyj zawartość kontenerów utworzone na Twoim koncie magazynu obiektów blob. Upewnij się, że rozumiesz, które skryptu lub polecenia jest odpowiedzialny za tworzenie każdej grupy plików.
- Zmodyfikuj skrypt szkolenia w celu przeszkolenia innego typu modelu MMLSpark lub zmienić hyperparameters modelu. Użyj funkcji Historia uruchomień, aby ustalić, czy zmiany zwiększyć lub zmniejszyć dokładności modelu.
