---
title: Eksplorowanie i modelowanie maszyny wirtualnej do nauki o danych danych
titleSuffix: Azure
description: Na maszynie wirtualnej analizy danych, należy wykonać różne eksploracji danych i zadań modelowania.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: f30c241feced3031d9ed9791c27c6bb1e1e99efb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60366274"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dziesięć rzeczy, które można wykonać na Windows maszyny wirtualnej analizy danych

Windows Data Science Virtual Machine (dsvm dystrybucji) to środowisko programistyczne do nauki o zaawansowanych danych, która umożliwia wykonywanie różnych zadań Eksplorowanie i modelowanie danych. W środowisku jest już utworzone i powiązane z kilku narzędzi analizy popularnych danych, które ułatwiają szybkie rozpoczynanie pracy przy użyciu analizy i lokalnych, wdrożeń w chmurze czy hybrydowa. Maszyny DSVM ściśle współpracuje z wieloma usługami platformy Azure i może odczytywać i przetwarzać dane, które zostały już zapisane na platformie Azure, Azure SQL Data Warehouse, Azure Data Lake, Azure Storage lub Azure Cosmos DB. Można również korzystać innych narzędzi analitycznych, takich jak Azure Machine Learning i Azure Data Factory.

W tym artykule dowiesz się, jak używać maszyny wirtualnej DSVM do wykonywania różnych zadań do nauki o danych i interakcji z innymi usługami platformy Azure. Oto kilka rzeczy, które można wykonać na maszyny DSVM:

1. Eksplorowanie danych i tworzenie modeli lokalnie na DSVM przy użyciu Microsoft ML Server, języka Python
2. Użyj notesu programu Jupyter, aby eksperymentować z danymi w przeglądarce, za pomocą języka Python 2, 3 języka Python, R Microsoft enterprise gotowe wersję języka R z myślą o wydajności
3. Wdrażanie modeli skompilowanych przy użyciu języków R i Python w usłudze Azure Machine Learning, aby aplikacje klienckie mogą uzyskiwać dostęp do modeli przy użyciu prostego interfejsu sieci web usługi
4. Administrowania zasobami platformy Azure przy użyciu witryny Azure portal lub programu Powershell
5. Rozszerzanie miejsca do magazynowania i udostępnianie dużych ilości danych / kodu całego zespołu, tworząc usługi Azure File storage jako dysk możliwa instalacja na maszyny wirtualnej DSVM
6. Udostępniać kod swojemu zespołowi, korzystając z usługi GitHub, a dostęp do repozytorium przy użyciu wstępnie zainstalowanych Git klientów - Git Bash, graficznego interfejsu użytkownika narzędzia Git.
7. Dostęp różne usługi platformy Azure danych i analizy takie jak magazyn obiektów blob platformy Azure, Azure Data Lake, Azure HDInsight (Hadoop), usługi Azure Cosmos DB, Azure SQL Data Warehouse & bazy danych
8. Tworzyć raporty i pulpity nawigacyjne programu Power BI Desktop, wstępnie zainstalowanych na maszyny DSVM i wdrażać je w chmurze
9. Dynamiczne skalowanie maszyny wirtualnej DSVM stosownie do potrzeb projektu
10. Zainstaluj dodatkowe narzędzia na maszynie wirtualnej.   

> [!NOTE]
> Dla wielu usług magazynu i analizy danych dodatkowych wymienione w tym artykule mają zastosowanie dodatkowe opłaty. Zapoznaj się [cennika systemu Azure](https://azure.microsoft.com/pricing/) strony, aby uzyskać szczegółowe informacje.
> 
> 

**Wymagania wstępne**

* Musisz mieć subskrypcję platformy Azure. Możesz zasubskrybować bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/free/).
* Instrukcje dotyczące obsługi maszyny wirtualnej do nauki o danych w witrynie Azure portal są dostępne pod adresem [tworzenia maszyny wirtualnej](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Badaj dane i tworzyć modele przy użyciu usługi Microsoft ML Server lub Python
Języki R i Python umożliwia przeprowadzenie analizy swoje dane bezpośrednio na maszyny DSVM.

Dla języka R można użyć środowiska IDE, takich jak program RStudio, które znajdują się w start menu lub z pulpitu lub R Tools for Visual Studio. Firma Microsoft udostępnia dodatkowe biblioteki, na podstawie Open źródło/CRAN-R umożliwienie skalowalnej analizy i możliwości analizowania danych jest większy niż dozwolony, wykonując równoległe analizy fragmentaryczne rozmiar pamięci. 

Dla języka Python można użyć środowiska IDE, takie jak Visual Studio Community Edition, które oferuje narzędzia języka Python dla wstępnie zainstalowane rozszerzenie programu Visual Studio (PTVS). Domyślnie tylko środowisko Python 3.6, środowiska conda główny jest skonfigurowany w narzędziach PTVS. Aby włączyć środowisko Anaconda Python 2.7, musisz wykonaj następujące czynności:

* Tworzenie niestandardowego środowiska dla każdej wersji, przechodząc do **narzędzia** -> **narzędzi Python Tools** -> **środowiska Python** , a następnie klikając polecenie " **+ Niestandardowe**"w programie Visual Studio Community Edition
* Wprowadź opis i Ustaw ścieżkę prefiks środowiska jako *c:\anaconda\envs\python2* Anaconda Python 2.7
* Kliknij przycisk **Autowykrywanie** i następnie **Zastosuj** można zapisać środowiska.

Oto jak wygląda instalacji środowiska niestandardowego w programie Visual Studio.

![Zrzut ekranu programu Visual Studio za pomocą narzędzi Python Tools for Visual Studio wybrane](./media/vm-do-ten-things/PTVSSetup.png)

Zobacz [dokumentacja narzędzi PTVS](https://aka.ms/ptvsdocs) Aby uzyskać więcej informacji na temat tworzenia środowiska Python.

Teraz możesz są skonfigurowane do tworzenia nowego projektu języka Python. Przejdź do **pliku** -> **New** -> **projektu** -> **Python** i wybierz typ Aplikacja języka Python, które tworzysz. Można ustawić środowisko Python dla bieżącego projektu do żądanej wersji (Python 2.7 lub 3.6), klikając prawym przyciskiem myszy **środowiska Python**, wybierając opcję **środowiska Python Dodaj/Usuń**, a następnie pobrania wymagane środowisko. Można znaleźć więcej informacji na temat pracy z narzędziami PTVS w produkcie [dokumentacji](https://aka.ms/ptvsdocs).

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Eksplorowanie i modelowanie danych za pomocą języka Python lub R przy użyciu notesu programu Jupyter
Notes Jupyter jest zaawansowane środowisko, która zapewnia oparte na przeglądarce "IDE" Eksplorowanie i modelowanie danych. Używając języka Python 2, 3 języka Python lub R ("Open Source" i Microsoft R Server) w notesie Jupyter.

Aby uruchomić Notes Jupyter, kliknij ikonę menu start / ikony pulpitu pod tytułem **notesu programu Jupyter**. W wierszu polecenia maszyny wirtualnej DSVM, można również uruchomić polecenie ```jupyter notebook``` z katalogu, w którym masz istniejących notesów lub do utworzenia nowych w notesach.  

Po uruchomieniu programu Jupyter, powinien zostać wyświetlony katalog, który zawiera kilka notesów przykładu, które są wstępnie utworzonych pakietów do maszyny DSVM. Teraz możesz wykonywać następujące czynności:

* Kliknij w notesie, aby wyświetlić kod.
* Wykonaj każda komórka, naciskając klawisz **wprowadź SHIFT**.
* Uruchom cały notes, klikając **komórki** -> **uruchamiania**
* Utwórz nowy notes, klikając ikonę Jupyter (w lewym górnym rogu), a następnie klikając polecenie **New** przycisk po prawej stronie, a następnie wybierając języka Notes (znany także jako jądra).   

> [!NOTE]
> Obecnie są obsługiwane środowisko Python 2.7, środowiska Python 3.6, R, Julia i PySpark jądra w programie Jupyter. Jądra R obsługuje programowanie w zarówno R typu Open source, jak i wydajne Microsoft R.   
> 
> 

Po przejściu w notesie, które można eksplorować dane i tworzenie modelu, przetestowania modelu przy użyciu wybranych bibliotek.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Tworzenie modeli przy użyciu języka R lub Python i operacjonalizacja jednym je przy użyciu usługi Azure Machine Learning
Po skompilowane i sprawdzania poprawności modelu następnym krokiem jest zwykle można wdrożyć ją w środowisku produkcyjnym. Dzięki temu klient aplikacjom wywoływanie określane są przewidywania modelu w czasie rzeczywistym lub na zasadzie tryb usługi batch. Usługa Azure Machine Learning udostępnia mechanizm do obsługi operacji modelu utworzone w języku R lub Python.

Po użytkownik operacjonalizować model w usłudze Azure Machine Learning, usługi sieci web jest udostępniany, który pozwala na wykonywanie wywołań REST, które przekazać parametry wejściowe i otrzymywać przewidywań modelu jako dane wyjściowe.   

> [!NOTE]
> Jeśli nie jeszcze zarejestrowaniu się do usługi Azure Machine Learning, możesz uzyskać obszaru roboczego warstwy bezpłatna lub standardowa obszaru roboczego, odwiedzając [Azure Machine Learning Studio](https://studio.azureml.net/) strony głównej i kliknięcie na "pierwsze kroki".   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Modele kompilacji i Operacjonalizacja języka Python
Poniżej przedstawiono fragment kodu opracowany w języku Python notesu programu Jupyter, tworzący prosty model przy użyciu biblioteki SciKit-learn.

```python
#IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Metoda użyta do wdrożenia Twoich modeli języka python do usługi Azure Machine Learning zawija prognozowania modelu do funkcji i rozszerza je za pomocą atrybutów udostępniane przez wstępnie zainstalowanych biblioteka języka python usługi Azure Machine Learning, które oznaczają usługi Azure Machine Learning Identyfikator obszaru roboczego, klucz interfejsu API i parametry wejściowe i zwracana.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(int) #0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]
return clf.predict(inputArray)
```

Klient może teraz wykonywać wywołania do usługi sieci web. Brak otoki wygody, które konstruowania żądania interfejsu API REST. Poniżej przedstawiono przykładowy kod w celu korzystania z usługi sieci web.

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Biblioteka usługi Azure Machine Learning jest obsługiwana tylko na środowisko Python 2.7 obecnie.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Modele kompilacji i Operacjonalizacja języka R
Można wdrażać modele języka R utworzone w sposób podobny do jak jest wykonywane dla języka Python na maszynie wirtualnej do nauki o danych lub w innym miejscu na usłudze Azure Machine Learning. Oto konkretne kroki:

* Utwórz plik settings.json, aby zapewnić uwierzytelnianie i identyfikator obszaru roboczego, na których token 
* Napisz otokę dla modelu prognozowania funkcji.
* Wywołaj ```publishWebService``` w bibliotece usługi Azure Machine Learning, aby przekazać otoki funkcji.  

Poniżej przedstawiono procedurę i fragmentów kodu, które mogą służyć do konfigurowania, tworzenie, publikowanie i korzystanie z modelu jako usługi sieci web Azure Machine Learning.

#### <a name="setup"></a>Konfigurowanie

* Utwórz plik settings.json w obszarze katalog o nazwie ```.azureml``` w katalogu macierzystym i wprowadź parametry z obszaru roboczego usługi Azure Machine Learning:

Struktura pliku Settings.JSON:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Tworzenie modelu w języku R i opublikować w usłudze Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Używanie modelu wdrożonych w usłudze Azure Machine Learning
Korzystanie z modelu z aplikacji klienckiej, używamy biblioteki usługi Azure Machine Learning do wyszukania opublikowanej usługi sieci web przy użyciu nazwy `services` wywołania interfejsu API w celu określenia punktu końcowego. Następnie należy wywołać `consume` działać, a następnie przekazać w ramce danych, aby można przewidzieć.
Poniższy kod umożliwia używanie modelu publikowane jako usługi sieci web Azure Machine Learning.

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# OK, try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Więcej informacji na temat biblioteki usługi Azure Machine Learning R można znaleźć [tutaj](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrowania zasobami platformy Azure przy użyciu witryny Azure portal lub programu Powershell
Maszyny DSVM nie tylko umożliwia tworzenie rozwiązania do analizy lokalnie na maszynie wirtualnej, ale pozwala również na uzyskiwanie dostępu do usług w chmurze Microsoft Azure. System Azure oferuje kilka obliczeń, magazynu, usługi analizy danych i innych usług, które mogą administrować i pobierać z maszyny wirtualnej DSVM.

Do administrowania zasobami platformy Azure subskrypcji i w chmurze można użyć przeglądarki i wskaż [witryny Azure portal](https://portal.azure.com). Umożliwia także programu Azure Powershell do administrowania subskrypcji platformy Azure i zasobów za pomocą skryptu.
Można uruchomić programu Azure Powershell przy użyciu skrótu na pulpicie lub w menu start o nazwie "Microsoft Azure Powershell." Zapoznaj się [dokumentację programu Azure Powershell Microsoft](../../powershell-azure-resource-manager.md) więcej informacji na temat sposobu do administrowania subskrypcji platformy Azure i zasobów za pomocą skryptów programu Windows Powershell.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Rozszerzanie miejsca do magazynowania w systemie plików udostępnionych
Analitycy danych mogą udostępniać dużych zestawów danych, kodu lub innych zasobów w ramach zespołu. NAUKI, sama ma około 45GB dostępnego miejsca. Aby rozszerzyć magazynu, można użyć usługi Azure File Service i albo zainstalować na co najmniej jedno wystąpienie maszyny wirtualnej DSVM lub uzyskać do niego dostęp za pośrednictwem interfejsu API REST.  Można również użyć [witryny Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) lub użyj [programu Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) dodawanie dysków danych dodatkowych dedykowanych. 

> [!NOTE]
> Maksymalna ilość miejsca na udział usługi Azure File Service jest 5 TB, a limit rozmiaru poszczególnych plików wynosi 1 TB. 
> 
> 

Można użyć programu Azure Powershell, aby utworzyć udział usługi Azure File Service. Oto skrypt do uruchomienia w ramach programu Azure PowerShell, aby utworzyć udział plików platformy Azure usługi.

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you wish.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure File Service Share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the FIle share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Teraz, po utworzeniu udziału plików platformy Azure, możesz go zainstalować w dowolnej maszyny wirtualnej na platformie Azure. Zdecydowanie zaleca się, że maszyna wirtualna znajduje się w tym samym centrum danych platformy Azure jako konto magazynu, aby uniknąć opłat za transfer opóźnienia i danych. Poniżej przedstawiono polecenia do zainstalowania dysku maszyny wirtualnej DSVM, uruchamianą w programie Azure Powershell.

```powershell
# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Możesz teraz dostęp ten dysk, jak w przypadku dowolnego normalne dysku na maszynie Wirtualnej.

## <a name="6-share-code-with-your-team-using-github"></a>6. Udostępniać kod swojemu zespołowi, korzystając z usługi GitHub
GitHub to repozytorium kodu, gdzie można znaleźć wiele przykładowy kod i źródeł dla różnych narzędzi przy użyciu różnych technologii udostępnione przez społeczność deweloperów. Używa usługi Git jako technologia do śledzenia i przechowywania wersji plików kodu. GitHub to też platformy umożliwiającego utworzenie własnego repozytorium do przechowywania wspólny kod Twojego zespołu i dokumentacji, implementowanie kontroli wersji i kontrolują także, kto ma dostęp do wyświetlanie i Współtworzenie kodu. Odwiedź stronę [stron Pomocy usługi GitHub](https://help.github.com/) Aby uzyskać więcej informacji na temat korzystania z usługi Git. GitHub służy jako sposoby współpracować z zespołem, korzystać z kodem tworzonym przez społeczność i współtworzyć kod społeczności.

Maszyny DSVM zawiera już załadowany z narzędziami klienckimi zarówno wiersza polecenia jako dobrze graficznego interfejsu użytkownika, aby dostęp do repozytorium GitHub. Narzędzie wiersza polecenia do pracy z Git i GitHub nosi nazwę powłoki Git Bash. Visual Studio zainstalowane natym maszyny DSVM ma rozszerzenia Git. Ikony rozruchu można znaleźć w tych narzędzi, w start menu i pulpitu.

Aby pobrać kod z repozytorium GitHub, należy użyć ```git clone``` polecenia. Na przykład, można pobrać opublikowane przez firmę Microsoft do bieżącego katalogu repozytorium do nauki o danych uruchomieniem następującego polecenia po dołączeniu ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

W programie Visual Studio możesz tworzyć tej samej operacji klonowania. Poniższy zrzut ekranu pokazuje, jak dostęp do narzędzia Git i GitHub w programie Visual Studio.

![Zrzut ekranu programu Visual Studio z połączeniem usługi GitHub, wyświetlane](./media/vm-do-ten-things/VSGit.PNG)

Można znaleźć więcej informacji na temat korzystania z narzędzia Git do pracy z repozytorium GitHub z kilku dostępnych zasobów w witrynie github.com. [Cheat-sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) jest odwołaniem przydatne.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Dostęp do różnych usług Azure data i analizy
### <a name="azure-blob"></a>Obiekt bob Azure
Obiektów blob platformy Azure to niezawodne i ekonomiczne przechowywanie w chmurze danych dużych i małych. W tej sekcji opisano, jak przenosić dane do obiektów Blob platformy Azure i dostęp do danych przechowywanych w usłudze Azure Blob.

**Wymagania wstępne**

* **Tworzenie konta usługi Azure Blob storage z [witryny Azure portal](https://portal.azure.com).**

![Zrzut ekranu przedstawiający proces tworzenia konta magazynu w witrynie Azure portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Upewnij się, że wstępnie zainstalowane narzędzie wiersza polecenia narzędzia AzCopy znajduje się na ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Katalog zawierający azcopy.exe jest już w zmiennej środowiskowej PATH, aby uniknąć wpisywania ścieżki pełne polecenie podczas uruchamiania tego narzędzia. Aby uzyskać więcej informacji na temat narzędzia AzCopy, zapoznaj się [dokumentację programu AzCopy](../../storage/common/storage-use-azcopy.md)
* Uruchom narzędzie Eksplorator usługi Azure Storage. Można go pobrać ze [Microsoft Azure Storage Explorer](https://storageexplorer.com/). 

![Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage uzyskiwania dostępu do konta magazynu](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Przenoszenie danych z maszyny Wirtualnej do obiektów Blob platformy Azure: AzCopy**

Do przenoszenia danych między lokalnymi plikami i usługi blob storage, narzędzia AzCopy można użyć w wiersza polecenia lub programu PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Zastąp **C:\myfolder** do ścieżki, w którym jest zapisany plik, **mystorageaccount** na nazwę konta magazynu obiektów blob, **mycontainer** do nazwy kontenera **klucz konta magazynu** na klucz dostępu do magazynu obiektów blob. Można znaleźć poświadczeń konta magazynu w [witryny Azure portal](https://portal.azure.com).

![Zrzut ekranu przedstawiający kluczy konta magazynu i kontener informacji w witrynie Azure portal](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

W programie PowerShell lub wierszu polecenia, Uruchom polecenia narzędzia AzCopy. Poniżej przedstawiono przykłady użycia polecenia narzędzia AzCopy:

```powershell
# Copy *.sql from local machine to an Azure Blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from Azure Blob container to Local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Po uruchomieniu polecenia narzędzia AzCopy do kopiowania do obiektu blob platformy Azure, zobaczysz, że plik pojawia się w Eksploratorze usługi Storage Azure wkrótce.

![Zrzut ekranu przedstawiający konto magazynu, wyświetlanie przekazanego pliku CSV](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Przenoszenie danych z maszyny Wirtualnej do obiektów Blob platformy Azure: Azure Storage Explorer**

Możesz również przekazać dane z pliku lokalnego na maszynie wirtualnej za pomocą Eksploratora usługi Azure Storage:

* Aby przekazać dane do kontenera, wybierz kontener docelowy, a następnie kliknij przycisk **przekazywanie** przycisku.![ Zrzut ekranu przedstawiający przycisk Przekaż w Eksploratorze usługi Azure Storage](./media/vm-do-ten-things/storage-accounts.png)
* Kliknij pozycję **...**  po prawej stronie **pliki** wybierz jednego lub wielu plików do przekazania z systemu plików, a następnie kliknij przycisk **przekazywanie** Rozpocznij przekazywanie plików.![ Zrzut ekranu przedstawiający okno dialogowe przekazywania plików](./media/vm-do-ten-things/upload-files-to-blob.png)

**Odczytywanie danych z obiektów Blob platformy Azure: Moduł czytnika uczenia maszynowego**

W usłudze Azure Machine Learning Studio, można użyć **modułu importu danych** można odczytać danych z obiektu blob.

![Zrzut ekranu przedstawiający modułu importu danych w usłudze Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Odczytywanie danych z obiektów Blob platformy Azure: Python ODBC**

Możesz użyć **BlobService** biblioteki do odczytywania danych bezpośrednio z obiektu blob w programie Jupyter Notebook lub Python.

Najpierw zaimportuj wymagane pakiety:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Następnie podłącz poświadczeń konta usługi Azure Blob i odczytywanie danych z magazynu obiektów Blob:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzipping downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Dane są odczytywane w jako ramkę danych:

![Zrzut ekranu przedstawiający pierwszych 10 wierszy danych](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Usługi Azure Data Lake Storage to ogromne repozytorium dla obciążeń analizy danych big data i zgodny z pliku System (HDFS, Hadoop Distributed). Działa z usługi Hadoop, Spark i Azure Data Lake Analytics. W tej sekcji dowiesz się, jak przenieść dane do usługi Azure Data Lake Store i wykonywać analizy przy użyciu usługi Azure Data Lake Analytics.

**Wymagania wstępne**

* Tworzenie usługi Azure Data Lake Analytics w [witryny Azure portal](https://portal.azure.com).

![Zrzut ekranu przedstawiający tworzenie usługi Data Lake Analytics w witrynie Azure portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* **Azure Data Lake Tools** w **programu Visual Studio** adrese to [łącze](https://www.microsoft.com/download/details.aspx?id=49504) jest już zainstalowana na Visual Studio Community Edition, znajduje się na maszynie wirtualnej. Uruchamianie programu Visual Studio i rejestrowanie w ramach subskrypcji platformy Azure, powinno pojawić Twoje konto platformy Azure, analiza danych i magazynu w panelu po lewej stronie programu Visual Studio.

![Zrzut ekranu przedstawiający narzędzi Data Lake Tools w programie Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Przenoszenie danych z maszyny Wirtualnej do usługi Data Lake: Usługa Azure Data Lake Explorer**

Możesz użyć **usługi Azure Data Lake Explorer** do przekazania danych z plików lokalnych na maszynie wirtualnej do magazynu usługi Data Lake.

![Zrzut ekranu przedstawiający przekazywanie plików przy użyciu programu Data Lake Explorer](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Możesz również tworzyć potoku danych do obsługi operacji usługi przenoszenia danych do i z usługi Azure Data Lake za pomocą [Factory(ADF) danych platformy Azure](https://azure.microsoft.com/services/data-factory/). Zapoznaj się z tym [artykułu](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) przeprowadzenie Cię przez kroki do utworzenia danych potoków.

**Odczyt danych z magazynu obiektów Blob Azure do usługi Data Lake: U-SQL**

Jeśli dane znajdują się w usłudze Azure Blob storage, mogą bezpośrednio odczytywać dane z usługi Azure storage blob zapytania U-SQL. Przed redagowania zapytania U-SQL, upewnij się, że konto usługi blob storage jest połączone z usługi Azure Data Lake. Przejdź do **witryny Azure portal**, Znajdź pulpit nawigacyjny usługi Azure Data Lake Analytics, kliknij przycisk **Dodaj źródło danych**, wybierz typ magazynu **usługi Azure Storage** i dołączyć konta usługi Azure Storage Nazwa i klucz. Następnie możesz się odwoływać się do danych przechowywanych na koncie magazynu.

![Zrzut ekranu przedstawiający okno dialogowe Dodaj źródło danych](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

W programie Visual Studio można odczytać danych z magazynu obiektów blob, wykonaj operacje na danych, inżynieria i dane wyjściowe dane wynikowe do usługi Azure Data Lake lub usługi Azure Blob Storage. Gdy odwołujesz się dane w magazynie obiektów blob, użyj **wasb: / /**; Gdy odwołujesz się dane w usłudze Azure Data Lake, użyj **swbhdfs: / /**

![Zrzut ekranu zapytania z wyróżnioną pozycją wpisem WASB](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Możesz wykorzystać następujące zapytania U-SQL w programie Visual Studio:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Po przesłaniu zapytania do serwera jest wyświetlany diagram przedstawiający stan zadania.

![Zrzut ekranu przedstawiający okno dialogowe Stan zadania](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Wykonywanie zapytań dotyczących danych w usłudze Data Lake: U-SQL**

Po zestawu danych, pobieranym do usługi Azure Data Lake, można użyć [języka U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) do tworzenia zapytań i eksplorowania danych. Język U-SQL jest podobny do języka T-SQL, ale łączy niektóre funkcje za pomocą języka C#, aby użytkownicy mogą zapisywać niestandardowe moduły, funkcje zdefiniowane przez użytkownika i itp. Skrypty można użyć w poprzednim kroku.

Po kwerendy jest przesyłany do serwera, tripdata_summary. CSV znajdują się wkrótce w **usługi Azure Data Lake Explorer**, użytkownik może wyświetlić podgląd danych, kliknij prawym przyciskiem myszy plik.

![Zrzut ekranu przedstawiający plik csv w Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Aby wyświetlić informacje o pliku:

![Zrzut ekranu przedstawiający podsumowanie informacji o pliku](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Klastry usługi HDInsight Hadoop
Usługa Azure HDInsight to zarządzana usługa Apache Hadoop, Spark, HBase i Storm w chmurze. Można było łatwo pracować z klastrami usługi Azure HDInsight z maszyny wirtualnej do nauki o danych.

**Wymagania wstępne**

* Tworzenie konta usługi Azure Blob storage z [witryny Azure portal](https://portal.azure.com). To konto magazynu jest używane do przechowywania danych w przypadku klastrów HDInsight.

![Zrzut ekranu przedstawiający tworzenie HDInsight w witrynie Azure portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Dostosowywanie klastrów usługi Azure HDInsight Hadoop, z [witryny Azure portal](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Połącz konto magazynu utworzone za pomocą klastra usługi HDInsight podczas jego tworzenia. To konto magazynu jest używane do uzyskiwania dostępu do danych, które mogą być przetwarzane w klastrze.

![Link do kont magazynu utworzonych za pomocą klastra HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Włącz **dostępu zdalnego** z węzłem głównym klastra po jego utworzeniu. Pamiętaj poświadczenia dostępu zdalnego, określone w tym miejscu, ponieważ będą one potrzebne w następnej procedurze.

![Włącz dostęp zdalny z klastrem HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Tworzenie obszaru roboczego usługi Azure Machine Learning. Twoje eksperymenty uczenia maszynowego są przechowywane w tym obszarze roboczym usługi Machine Learning. Wybieranie opcji wyróżnionych w portalu, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie obszaru roboczego usługi Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Następnie wprowadź parametry dla obszaru roboczego

![Wprowadź parametry obszaru roboczego usługi Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Przekazywanie danych za pomocą IPython Notebook. Najpierw zaimportuj wymagane pakiety, podłącz poświadczeń, Utwórz bazę danych na koncie magazynu, a następnie ładowania danych w klastrach HDI.

```python
#Import required Packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


#Create the connection to Hive using ODBC
SERVER_NAME='xxx.azurehdinsight.net'
DATABASE_NAME='nyctaxidb'
USERID='xxx'
PASSWORD='xxxx'
DB_DRIVER='Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor=connection.cursor()


#Create Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


#Upload data from blob storage to HDI cluster
for i in range(1,13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
    cursor.execute(queryString)
```

* Alternatywnie, można wykonać to [wskazówki](../team-data-science-process/hive-walkthrough.md) przekazywać dane taksówek NYC do klastra usługi HDI. Główne kroki są następujące:
  
  * Narzędzia AzCopy: Pobierz skompresowany plik CSV z publicznego obiektu blob do folderu lokalnego
  * Narzędzia AzCopy: przekazywanie rozpakowany udostępnionych woluminach Klastra z lokalnego folderu do klastra usługi HDI
  * Zaloguj się do węzła głównego klastra usługi Hadoop i przygotować się do analizy danych eksploracyjne

Po załadowaniu danych do klastra usługi HDI możesz sprawdzić swoje dane w Eksploratorze usługi Azure Storage. I masz nyctaxidb bazy danych, utworzonych w klaster usługi HDI.

**Eksploracja danych: Zapytania programu hive w języku Python**

Ponieważ dane znajdują się w klastrze usługi Hadoop, można użyć pakietu moduł pyodbc połączyć się z klastrów Hadoop i zapytania bazy danych przy użyciu technologii Hive eksploracji i inżynieria funkcji. Można wyświetlić istniejące tabele, utworzonego w kroku wymagań wstępnych.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString,connection)
```

![Wyświetlanie istniejących tabel](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Przyjrzyjmy się liczby rekordów w każdym miesiącu i częstotliwości występowania Przechylony w czy nie tabelą trip:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Wykres liczby rekordów w każdym miesiącu](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString,connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Wykres częstotliwości Porada](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Można również obliczyć odległość między lokalizacji odbioru i dropoff lokalizacji, a następnie porównaj ją do odległości podróży.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString,connection)
results.head(5)
```

![Pierwsze wiersze w tabeli odbiór i dropoff](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                    'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance']<=100] #remove outliers
df = df.loc[df['direct_distance']<=100] #remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Wykres odbioru/dropoff odległość odległość podróży](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Teraz możemy przygotować próbkowana w dół (1%) zbiór danych do modelowania. W module czytnik usługi Machine Learning, można użyć tych danych.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Teraz wstawić zawartość sprzężenia do powyższej tabeli wewnętrznej

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Po krótkiej chwili widać, że dane zostały załadowane w klastrach usługi Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString,connection)
```

![Pierwsze wiersze danych z tabeli](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Odczytywanie danych z usługi HDI przy użyciu usługi Machine Learning: moduł czytnika**

Można także użyć **czytnika** modułu w usłudze Machine Learning Studio dostępu do bazy danych w klastrze usługi Hadoop. Podłącz poświadczenia w klastrach HDI oraz konta magazynu platformy Azure, aby umożliwić tworzenie usługi machine learning modele przy użyciu bazy danych w klastrach HDI.

![Właściwości modułu czytnika](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Można wyświetlić ocenami zestawu danych:

![Wyświetl oceniane zestawu danych](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Usługa Azure SQL Data Warehouse & bazy danych
Usługa Azure SQL Data Warehouse to elastyczny magazyn danych jako usługa oferująca środowisko programu SQL Server klasy korporacyjnej.

Możesz aprowizować usługi Azure SQL Data Warehouse, postępując zgodnie z instrukcjami podanymi w tym [artykułu](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zainicjowaniu usługi Azure SQL Data Warehouse, możesz użyć tej funkcji [wskazówki](../team-data-science-process/sqldw-walkthrough.md) do przekazywania danych, eksplorowanie i modelowanie przy użyciu danych w usłudze SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Usługa Azure Cosmos DB to baza danych NoSQL w chmurze. Umożliwia do pracy z dokumentami, takich jak JSON i pozwala na przechowywanie i wykonywania zapytań względem dokumentów.

Poniżej przedstawiono kroki na — wymagania wstępne, aby uzyskać dostęp do usługi Azure Cosmos DB z maszyny DSVM:

1. Zestaw SDK Python usługi Azure Cosmos DB jest już zainstalowana na maszyny DSVM (uruchamianie ```pip install pydocumentdb --upgrade``` z wiersza polecenia, aby zaktualizować)
2. Tworzenie konta usługi Azure Cosmos DB i bazę danych z [witryny Azure portal](https://portal.azure.com)
3. Pobierz "Usługi Azure Cosmos DB narzędzia migracji" z [tutaj](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) i Wyodrębnij zawartość do katalogu wybranego
4. Importuj dane JSON (swe dzieła danych) w udziale [publicznego obiektu blob](https://cahandson.blob.core.windows.net/samples/volcano.json) do usługi Cosmos DB z następującymi parametrami polecenia do narzędzia do migracji (dtui.exe z katalogu, w którym zainstalowano narzędzie migracji usługi Cosmos DB). Wprowadź lokalizację źródłowych i docelowych, z następującymi parametrami:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po zaimportowaniu danych, możesz przejść do aplikacji Jupyter i otworzyć notes o nazwie *DocumentDBSample* zawierający kod python dostęp do usługi Azure Cosmos DB i wykonaj kilka podstawowych zapytań. Znajdziesz więcej informacji na temat usługi Cosmos DB, odwiedzając usługi [stronę z dokumentacją dotyczącą](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Tworzenie raportów i pulpitów nawigacyjnych przy użyciu programu Power BI Desktop
Można wizualizować pliku swe dzieła JSON z poprzedniego przykładu Cosmos DB w usłudze Power BI, aby uzyskać wizualny wgląd w dane. Szczegółowe instrukcje są dostępne w [artykule dotyczącym usługi Power BI](../../cosmos-db/powerbi-visualize.md). Poniżej przedstawiono ogólne kroki:

1. Otwórz program Power BI Desktop i "Pobieranie danych." Określ adres URL w postaci: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Powinien zostać wyświetlony zaimportowanych jako listę rekordów JSON
3. Dzięki usłudze Power BI można pracować z takimi samymi przekonwertować listy na tabelę
4. Rozwiń kolumny, klikając ikonę rozwijania (jedna z ikoną "Strzałka w lewo i Strzałka w prawo" po prawej stronie kolumny)
5. Należy zauważyć, że lokalizacja znajduje się pole "Record". Rozwiń ten rekord i wybierz tylko współrzędnych. Współrzędna jest kolumna listy
6. Dodaj nową kolumnę, aby przekonwertować kolumnę współrzędnych listy przecinkami oddzielnej kolumnie LatLong łączenia dwóch elementów w polu listy współrzędnych przy użyciu formuły ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Konwertuj na koniec ```Elevation``` kolumny do wartości dziesiętnej i wybierz **Zamknij** i **Zastosuj**.

Zamiast w poprzednich krokach, możesz wkleić poniższy kod, w poziomie z krokami w edytorze zaawansowanym w usłudze Power BI, który pozwala na zapis przekształcenia danych w języku kwerend w skryptach.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Masz teraz dane w modelu danych usługi Power BI. Usługi Power BI desktop powinien wyglądać w następujący sposób:

![Program Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Możesz rozpocząć tworzenie raportów i wizualizacji przy użyciu modelu danych. Możesz wykonać kroki opisane w tej [artykule dotyczącym usługi Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) do tworzenia raportu. Dane wyjściowe to raport, który wygląda podobnie do poniższego.

![Usługa Power BI Desktop widoku raportu — łącznik usługi Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dynamiczne skalowanie maszyny wirtualnej DSVM stosownie do potrzeb projektu
Możesz skalować nauki do swoich potrzeb projektu w górę i w dół. Jeśli nie potrzebujesz użyć maszyny Wirtualnej wieczorami lub w weekendy, można po prostu zamknąć maszynę Wirtualną z [witryny Azure portal](https://portal.azure.com).

> [!NOTE]
> Jeśli używasz tylko przycisku zamknięcia systemu operacyjnego na maszynie Wirtualnej powoduje naliczenie opłaty za zasoby obliczeniowe.  
> 
> 

Jeśli potrzebujesz do obsługi analizy na dużą skalę i potrzebujesz więcej możliwości procesora CPU lub pamięci i/lub dysków można znaleźć wyboru dużych rozmiarów maszyn wirtualnych w zakresie rdzeni procesora CPU, wystąpienia oparte na procesorze GPU uczenia głębokiego, pojemność pamięci i typy dysków (w tym dysków SSD) spełniające co do obliczeń i budżetowe. Pełną listę maszyn wirtualnych wraz z ich godzinowe ceny za usługi obliczeniowe jest dostępna na [cennika usługi Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) strony.

Podobnie jeśli zmniejsza Twoje potrzeby możliwości przetwarzania maszyny Wirtualnej (na przykład: został przeniesiony najważniejszych obciążeń usługi Hadoop lub w klastrze Spark), możesz skalować w dół do klastra z [witryny Azure portal](https://portal.azure.com) i przechodząc do ustawień z wystąpieniem maszyny Wirtualnej. Poniżej przedstawiono zrzut ekranu.

![Ustawienia wystąpienia maszyny Wirtualnej](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Zainstaluj dodatkowe narzędzia na maszynie wirtualnej.
Istnieje kilka narzędzi, wstępnie skompilowane do nauki, które może uwzględnić wiele wspólnych danych, czego potrzebuje analizy. Pozwala to zaoszczędzić czas, unikając konieczności Zainstaluj i skonfiguruj środowiska pojedynczo oraz zaoszczędzić pieniądze, płacąc tylko za zasoby, możesz użyć.

Korzystanie z innymi usługami platformy Azure danych i analizy profilowane w tym artykule, aby ulepszyć środowiska analizy. W niektórych przypadkach potrzeb może wymagać dodatkowych narzędzi, takich jak niektóre zastrzeżone narzędzia innych firm. Masz pełny dostęp administracyjny na maszynie wirtualnej, aby zainstalować nowe narzędzia, których potrzebujesz. Można także zainstalować dodatkowe pakiety języka Python i R, które są wstępnie zainstalowane. Dla języka Python można użyć dowolnego ```conda``` lub ```pip```. W przypadku języka R można użyć ```install.packages()``` języka R w konsoli lub użyć IDE i wybierz pozycję "**pakietów** -> **instalowanie pakietów** ".

## <a name="summary"></a>Podsumowanie
To tylko niektóre rzeczy, które można zrobić w programie Microsoft maszyny wirtualnej analizy danych. Istnieje wiele więcej rzeczy, które można zrobić, aby stał się środowisko analizy skuteczne.

