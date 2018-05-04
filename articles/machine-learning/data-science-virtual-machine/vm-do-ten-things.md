---
title: 10 sposobów na dane nauki maszyny wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Wykonaj różne Eksploracja danych i zadań modelowania na nauki danych maszyny wirtualnej.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: gokuma
ms.openlocfilehash: a5f0961a99eac805e82cbc5e5d61c485f8661ba0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dziesięć czynności, które można wykonywać na dane nauki maszyny wirtualnej systemu Windows

Maszyna wirtualna nauki danych systemu Windows (DSVM) jest środowisko projektowe nauki zaawansowanych danych, który umożliwia wykonywanie różnych zadań eksploracji i modelowanie danych. Środowisko zawiera już wbudowanych i powiązane z kilku popularnych danych analytics narzędzia, które ułatwiają szybkie rozpoczęcie pracy z analizy dla lokalnego, w chmurze czy hybrydowa wdrożeń. DSVM ściśle współpracuje z wielu usług Azure i jest w stanie odczytywać i przetwarzać dane, które są już przechowywane na platformie Azure, Magazyn danych SQL Azure, usługa Azure Data Lake, usługi Azure Storage lub w usłudze Azure DB rozwiązania Cosmos. Można też skorzystać innych narzędzi analizy, takich jak uczenie maszynowe Azure i fabryki danych Azure.

W tym artykule dowiesz się, jak używać programu DSVM do wykonywania różnych zadań nauki danych i interakcji z innymi usługami Azure. Poniżej podano niektóre czynności, które można wykonywać na DSVM:

1. Eksplorowanie danych i tworzenie modeli lokalnie na DSVM przy użyciu programu Microsoft Server ML, Python
2. Użyj notesu Jupyter do wykonywania eksperymentów z danymi w przeglądarce, za pomocą języka Python, 2, Python 3, Microsoft R wersji enterprise gotowe R przeznaczone dla wydajności
3. Wdrażanie modeli utworzony przy użyciu języków R i Python w usłudze Azure Machine Learning, aby aplikacje klienckie mogą uzyskiwać dostęp do modeli przy użyciu prostego interfejsu sieci web usługi
4. Administrowanie zasobami platformy Azure przy użyciu portalu Azure lub programu Powershell
5. Rozszerzanie miejsca do magazynowania i udostępniania dużych zestawów danych / kodu w całym zespołem przez tworzenie usługi Magazyn plików Azure jako dysk instalację na DSVM Twojego
6. Udostępnianie kodu z zespołem przy użyciu usługi GitHub i dostępu do repozytorium przy użyciu wstępnie zainstalowane Git klientów - Git Bash Git graficznego interfejsu użytkownika.
7. Dostęp do różnych Azure danych i ich analiza usług przykład do magazynu obiektów blob platformy Azure, Azure Data Lake Azure HDInsight (Hadoop), bazy danych rozwiązania Cosmos platformy Azure, Magazyn danych SQL Azure & bazy danych
8. Twórz raporty i pulpit nawigacyjny za pomocą programu Power BI Desktop wstępnie zainstalowanych na DSVM i wdrażanie ich w chmurze
9. Dynamiczne skalowanie sieci DSVM zgodnie z potrzebami projektu
10. Instalowanie dodatkowych narzędzi na maszynie wirtualnej   

> [!NOTE]
> Użycie dodatkowe opłaty dla wielu usług magazynu i analiza danych dodatkowe wymienione w tym artykule. Zapoznaj się [cennik usługi Azure](https://azure.microsoft.com/pricing/) strony, aby uzyskać szczegółowe informacje.
> 
> 

**Wymagania wstępne**

* Potrzebujesz subskrypcji platformy Azure. Można założyć bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/free/).
* Instrukcje dotyczące obsługi danych nauki maszyny wirtualnej w portalu Azure są dostępne pod adresem [tworzenia maszyny wirtualnej](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Eksplorowanie danych i tworzenie modeli przy użyciu programu Microsoft Server ML lub Python
Języków R i Python umożliwia czy Twoje dane analizy prawo DSVM.

Dla języka R możesz użyć IDE, takich jak programu RStudio, który znajduje się w start menu lub pulpitu lub R narzędzi dla programu Visual Studio. Firma Microsoft udostępnia dodatkowe biblioteki u góry Open źródła/sieci CRAN-R umożliwienie analytics skalowalne i umożliwia analizowanie danych jest większy niż rozmiar pamięci może w sposób równoległy podzielony analizy. 

Dla języka Python możesz użyć IDE, takich jak Visual Studio Community Edition, mającej narzędzi Python Tools for wstępnie zainstalowane rozszerzenia programu Visual Studio (PTVS). Domyślnie tylko Python 3,6 środowisko conda głównego jest skonfigurowane na PTVS. Aby włączyć Anaconda Python 2.7, należy wykonać następujące czynności:

* Tworzenie niestandardowego środowiska dla każdej wersji, przechodząc do **narzędzia** -> **narzędzi Python Tools** -> **środowiska Python** , a następnie klikając polecenie "**+ niestandardowych**" w programie Visual Studio 2015 Community Edition
* Wprowadź opis oraz ścieżki prefiks środowiska jako *c:\anaconda\envs\python2* dla Anaconda Python 2.7
* Kliknij przycisk **Autowykrywanie** , a następnie **Zastosuj** można zapisać środowiska.

Oto, jak wygląda konfigurowania środowiska niestandardowych w programie Visual Studio.

![Instalator PTVS](./media/vm-do-ten-things/PTVSSetup.png)

Zobacz [dokumentacji PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) Aby uzyskać więcej informacji na temat tworzenia środowiska Python.

Teraz możesz są skonfigurowane do utworzenia nowego projektu języka Python. Przejdź do **pliku** -> **nowy** -> **projektu** -> **Python** i wybierz typ aplikacji Python tworzona. Można ustawić środowiska Python dla bieżącego projektu wymaganej wersji (Anaconda 2.7 lub 3,6), klikając prawym przyciskiem myszy **środowiska Python**, wybierając żądane **środowiska Python Dodaj lub usuń**, a następnie pobieranie na wymagane środowisko. Można znaleźć więcej informacji na temat pracy z narzędziami PTVS produktu [dokumentacji](https://github.com/Microsoft/PTVS/wiki) strony.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Za pomocą notesu Jupyter do eksplorowania i model danych za pomocą języka Python lub R
Notesu Jupyter jest zaawansowane środowisko, które zapewnia przeglądarki "IDE" Eksploracja danych i modelowania. Python 2, Python 3 lub R (Open Source i Microsoft R Server) służy w notesu Jupyter.

Aby uruchomić notesu Jupyter, kliknij ikonę menu start / zatytułowany ikony pulpitu **notesu Jupyter**. W wierszu polecenia DSVM można również uruchomić polecenie ```jupyter notebook``` z katalogu, w którym masz istniejących notesów lub chcesz utworzyć nowe komputery przenośne.  

Po rozpoczęciu Jupyter katalog, który zawiera kilka notesów przykład, które są wstępnie utworzonych pakietów do DSVM powinna zostać wyświetlona. Teraz możesz:

* Kliknij, aby wyświetlić kod.
* Wykonanie każdej komórki naciskając **wprowadź SHIFT**.
* Uruchom cały notes klikając **komórki** -> **Uruchom**
* Tworzenie nowego notesu kliknięcie ikony Jupyter (w lewym górnym rogu), a następnie klikając polecenie **nowy** przycisk w prawo, a następnie wybierając języka notesu (znanej także jako jądra).   

> [!NOTE]
> Obecnie są obsługiwane Python 2.7, 3,6 Python R, Julia i PySpark jądra w oprogramowaniu Jupyter. Jądra R obsługuje programowanie w zarówno typu Open source R, jak i wydajności R. firmy Microsoft   
> 
> 

Po przejściu w notesie, które można eksplorować danych, budowanie modelu, model przy użyciu bibliotek wybór testów.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Tworzenie modeli przy użyciu R lub Python i Operationalize je za pomocą usługi Azure Machine Learning
Po wbudowane i sprawdzić poprawności modelu następnym krokiem jest zwykle jej wdrożenia w środowisku produkcyjnym. Dzięki temu klient aplikacje do wywołania prognoz modelu w czasie rzeczywistym lub na podstawie trybu partii. Usługa Azure Machine Learning zapewnia mechanizm operacjonalizacji modelu wbudowane R lub Python.

Gdy użytkownik operacjonalizować model w usłudze Azure Machine Learning, usługi sieci web jest narażony, umożliwiającą klientom wywołań REST, które przekazywanie parametrów wejściowych i otrzymywać prognoz modelu jako dane wyjściowe.   

> [!NOTE]
> Jeśli użytkownik ma nie jeszcze zarejstrowano w usłudze Azure Machine Learning, odwiedzając można uzyskać wolnego obszaru roboczego lub standardowy obszaru roboczego [Azure Machine Learning Studio](https://studio.azureml.net/) strony głównej i kliknięcie na "Zacznij."   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Kompilowanie i Python Operacjonalizuj modele
Oto fragment kodu w notesu Jupyter Python korzystająca z modelu prostego za pomocą biblioteki SciKit Dowiedz się więcej.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Metody używane do wdrażania modeli python do usługi Azure Machine Learning opakowuje prognozowania modelu do funkcji i decorates go z atrybutami udostępniane przez wstępnie zainstalowane biblioteka języka python usługi Azure Machine Learning, które określa identyfikator obszaru roboczego uczenia maszynowego Azure, klucz interfejsu API i dane wejściowe i zwracać parametrów.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Klient może teraz utworzyć połączenia z usługą sieci web. Brak otoki wygody, które konstruowania żądania interfejsu API REST. Oto przykładowy kod w celu korzystania z usługi sieci web.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Biblioteka usługi Azure Machine Learning jest obsługiwana tylko na Python 2.7 obecnie.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Modele kompilacji i Operacjonalizacji R
Można wdrożyć modeli R zbudowane na maszynie wirtualnej nauki danych lub w innym miejscu na uczenie maszynowe Azure w sposób podobny do jak jest wykonywane dla języka Python. Oto konkretne kroki:

* Utwórz plik settings.json, aby zapewnić uwierzytelnianie i identyfikator obszaru roboczego, na których token 
* zapis otokę dla modelu prognozowania funkcji.
* wywołanie ```publishWebService``` biblioteki usługi Azure Machine Learning umożliwia przekazywanie otoki funkcji.  

Poniżej przedstawiono procedurę i kod fragmentów, które mogą służyć do konfigurowania, kompilacji, publikowanie i zużywać model jako usługę sieci web w usłudze Azure Machine Learning.

#### <a name="setup"></a>Konfiguracja

* Utwórz plik settings.json w obszarze katalog o nazwie ```.azureml``` w katalogu macierzystym i wprowadź parametry z obszaru roboczego uczenia maszynowego Azure:

Settings.JSON struktury plików:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Tworzenie modelu w R i opublikować ją w usłudze Azure Machine Learning
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Korzystanie z modelu, który został wdrożony w usłudze Azure Machine Learning
Użycie modelu od aplikacji klienckiej, używamy do wyszukiwania usługi opublikowana w sieci web przy użyciu nazwy biblioteki usługi Azure Machine Learning `services` wywołanie interfejsu API w celu określenia punktu końcowego. Następnie należy wywołać `consume` funkcji i przekazywać w ramce danych, aby można przewidzieć.
Poniższy kod służy do pracy z modelem publikowane jako usługi sieci web Azure Machine Learning.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Można znaleźć więcej informacji na temat biblioteki usługi Azure Machine Learning R [tutaj](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrowanie zasobami platformy Azure przy użyciu portalu Azure lub programu Powershell
DSVM nie tylko umożliwia tworzenie rozwiązania analizy lokalnie na maszynie wirtualnej, ale można też uzyskać dostęp do usług w chmurze Azure firmy Microsoft. Platforma Azure udostępnia kilka obliczeń, magazynu usługi analizy danych i innych usług, które mogą administrować i uzyskać dostęp z Twojej DSVM.

Do administrowania subskrypcji i w chmurze zasobów na platformie Azure można za pomocą przeglądarki i wskaż [portalu Azure](https://portal.azure.com). Umożliwia także programu Azure Powershell do administrowania subskrypcji platformy Azure i zasobów za pomocą skryptu.
Można uruchomić programu Azure Powershell, ze skrótu na pulpicie lub z menu start zatytułowany "Microsoft Azure Powershell." Zapoznaj się [dokumentacji programu Microsoft Azure Powershell](../../powershell-azure-resource-manager.md) Aby uzyskać więcej informacji na temat sposobu do administrowania subskrypcji platformy Azure i zasobów za pomocą skryptów programu Windows Powershell.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Rozszerzanie miejsca do magazynowania w systemie plików udostępnionych
Analityków danych może udostępniać dużych zestawów danych, kodu lub innych zasobów w obrębie zespołu. DSVM, sam ma około 45GB dostępnego miejsca. Aby rozszerzyć pamięć masową, można użyć usługi plików Azure i albo zainstalować na co najmniej jedno wystąpienie DSVM lub uzyskać do niego dostęp za pośrednictwem interfejsu API REST.  Można również użyć [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) lub użyj [programu Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) można dodać dysków z danymi bardzo dedykowanych. 

> [!NOTE]
> Maksymalna ilość miejsca udziału Azure usługa plików to 5 TB, a limit rozmiaru poszczególnych plików wynosi 1 TB. 
> 
> 

Przy użyciu programu Azure Powershell można utworzyć udział usługa plików Azure. Poniżej przedstawiono skrypt do uruchamiania programu Azure PowerShell, aby utworzyć udział plików Azure usługi.

    # Authenticate to Azure.
    Connect-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Teraz, po utworzeniu udziału plików na platformę Azure, możesz go zainstalować w żadnej maszyny wirtualnej na platformie Azure. Zdecydowanie zaleca się, że maszyna wirtualna jest w tym samym centrum danych Azure, co konto magazynu, aby uniknąć opóźnień i danych opłat za transfer. Poniżej przedstawiono polecenia można zainstalować na dysku na DSVM, która może działać na programu Azure Powershell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Teraz jak w przypadku dowolnego normalne dysku na maszynie Wirtualnej można dostępu do dysku.

## <a name="6-share-code-with-your-team-using-github"></a>6. Udostępnianie kodu z zespołem przy użyciu usługi GitHub
GitHub jest repozytorium kodu, gdzie można znaleźć wiele przykładowy kod i źródeł dla różnych narzędzi z użyciem różnych technologii udostępnianych przez społeczność deweloperów. Git używa technologii do śledzenia i przechowywania wersji plików kodu. GitHub jest również platformy umożliwiającego utworzenie własnych repozytorium magazynu udostępnionego kodu i dokumentacji Twojego zespołu, wdrożenia kontroli wersji i też kontrolować, kto ma dostęp do wyświetlania i współtworzenia kodu. Odwiedź stronę [GitHub strony pomocy](https://help.github.com/) uzyskać więcej informacji na temat używania narzędzia Git. GitHub służy jako jeden z sposobów współpracować z zespołem, użyj kodu opracowanych przez społeczność i współtworzenia kodu przez społeczność.

DSVM już zawiera załadowane z narzędziami klienckimi zarówno wiersza polecenia, jak dobrze graficznego interfejsu użytkownika można uzyskać dostępu do repozytorium GitHub. Narzędzie wiersza polecenia do pracy z usługi Git i GitHub jest nazywany Git Bash. Visual Studio zainstalowanych na DSVM ma rozszerzenia Git. Ikony rozruchu można znaleźć dla tych narzędzi w start menu i pulpitu.

Aby pobrać kod z repozytorium GitHub, należy użyć ```git clone``` polecenia. Na przykład, aby pobrać dane repozytorium nauki opublikowane przez firmę Microsoft do bieżącego katalogu można wykonać następujące polecenie po przejściu do ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

W programie Visual Studio możesz to zrobić tej samej operacji klonowania. Poniższy zrzut ekranu przedstawia sposób dostęp do narzędzia Git i GitHub w programie Visual Studio.

![Git w programie Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Można znaleźć więcej informacji na temat przy użyciu usługi Git do pracy z repozytorium GitHub z kilku dostępnych zasobów w witrynie github.com. [Ściągawka](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) jest przydatne odwołanie.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Dostęp do różnych usług Azure danych i ich analiza
### <a name="azure-blob"></a>Obiekt bob Azure
Obiektów blob platformy Azure to niezawodne, ekonomiczny chmurze magazynu danych dużych i małych. W tej sekcji opisano, jak przenieść dane obiektów Blob platformy Azure i uzyskiwanie dostępu do danych przechowywanych w obiekcie Blob Azure.

**Wymagania wstępne**

* **Tworzenie konta magazynu obiektów Blob platformy Azure z [portalu Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Upewnij się, że wstępnie zainstalowane narzędzie wiersza polecenia AzCopy znajduje się na ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Katalog zawierający azcopy.exe już znajduje się na Twojej zmiennej środowiskowej PATH, aby uniknąć wpisywania ścieżki pełne polecenie podczas uruchamiania tego narzędzia. Aby uzyskać więcej informacji na temat narzędzia AzCopy, dotyczą [dokumentację programu AzCopy](../../storage/common/storage-use-azcopy.md)
* Uruchom narzędzie Eksploratora usługi Storage platformy Azure. Można go pobrać z [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Przenoszenie danych z maszyny Wirtualnej do obiektów Blob platformy Azure: AzCopy**

Aby przenoszenie danych między lokalnych plików i magazynu obiektów blob, można użyć narzędzia AzCopy w wierszu polecenia lub środowiska PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Zastąp **C:\myfolder** do ścieżki, gdzie zapisany plik **mojekontomagazynu** do nazwy konta magazynu obiektów blob **mojkontener** nazwy kontenera, **klucz konta magazynu** do klucz dostępu do magazynu obiektów blob. Można znaleźć poświadczeń konta magazynu w [portalu Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Uruchom polecenie AzCopy, programu PowerShell lub z wiersza polecenia. Poniżej przedstawiono niektóre Przykładowe użycie polecenia programu AzCopy:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Po uruchomieniu polecenia AzCopy do kopiowania do obiektów blob platformy Azure można znaleźć pliku zostaną wyświetlone w Eksploratorze usługi Storage Azure wkrótce.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Przenoszenie danych z maszyny Wirtualnej do obiektów Blob platformy Azure: Eksploratora usługi Storage platformy Azure**

Możesz również przekazywać dane z pliku lokalnego w maszynie Wirtualnej za pomocą Eksploratora usługi Storage platformy Azure:

* Aby przekazać dane do kontenera, wybierz kontener docelowego, a następnie kliknij przycisk **przekazać** przycisku.![ Przekazywanie do Eksploratora usługi Storage](./media/vm-do-ten-things/storage-accounts.png)
* Polecenie **...**  z prawej strony **pliki** wybierz jednego lub wielu plików, aby przekazać z systemu plików, a następnie kliknij przycisk **przekazać** można rozpocząć przekazywania plików.![ Przekazywanie plików do obiektu blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Odczytywanie danych z obiektów Blob platformy Azure: moduł czytnika uczenia maszynowego**

W usłudze Azure Machine Learning Studio, można użyć **modułu importu danych** odczytywać dane z obiektu blob.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Odczytywanie danych z obiektów Blob platformy Azure: Python ODBC**

Można użyć **BlobService** biblioteki, aby odczytać dane bezpośrednio z obiektu blob w programie notesu Jupyter lub Python.

Po pierwsze zaimportuj wymagane pakiety:

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

Następnie podłącz poświadczeń konta obiektów Blob platformy Azure i odczytywanie danych z obiektu Blob:

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

Dane są odczytywane w jako ramki danych:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage to repozytorium hiperskali obciążeń analizy danych big data i zgodny z Hadoop Distributed pliku System (HDFS). Działa on z platformy Hadoop, Spark i usługą Azure Data Lake Analytics. W tej sekcji dowiesz się, jak przenieść dane do usługi Azure Data Lake Store i uruchom analytics przy użyciu usługi Azure Data Lake Analytics.

**Wymagania wstępne**

* Tworzenie z usługą Azure Data Lake Analytics w [portalu Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* **Azure Data Lake Tools** w **programu Visual Studio** znaleziono w tej [łącze](https://www.microsoft.com/download/details.aspx?id=49504) jest już zainstalowana na Visual Studio Community Edition znajdującego się na maszynie wirtualnej. Po uruchomieniem programu Visual Studio i rejestrowanie w Twojej subskrypcji platformy Azure, powinna zostać wyświetlona Twoje konto usługi Azure Data Analytics i magazynu w lewym panelu programu Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Przenoszenie danych z maszyny Wirtualnej do usługi Data Lake: Azure Data Lake Explorer**

Można użyć **Azure Data Lake Explorer** do przekazania danych z lokalnych plików na maszynie wirtualnej w magazynie usługi Data Lake.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Można także utworzyć potoku danych, który umożliwia operacjonalizację ruchem danych do i z usługi Azure Data Lake za pomocą [Factory(ADF) danych Azure](https://azure.microsoft.com/services/data-factory/). Odwołuje się do tego [artykułu](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) prowadzące przez proces kompilacji danych potoków.

**Odczytywanie danych z obiektów Blob platformy Azure do usługi Data Lake: U-SQL**

Jeśli dane znajdują się w magazynie obiektów Blob platformy Azure, mogą bezpośrednio odczytywać dane z obiektu blob magazynu Azure w zapytaniu U-SQL. Przed tworzenia kwerendy U-SQL, upewnij się, że konto magazynu obiektów blob jest powiązany z usługi Azure Data Lake. Przejdź do **portalu Azure**, Znajdź pulpitu nawigacyjnego usługi Azure Data Lake Analytics, kliknij przycisk **Dodaj źródło danych**, wybierz typ magazynu **usługi Azure Storage** i dołączyć nazwę konta magazynu Azure i klucza. Następnie możesz mogą odwoływać się dane przechowywane na koncie magazynu.

![Wprowadź konto magazynu i klucz](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

W programie Visual Studio można odczytać danych z magazynu obiektów blob, czy niektóre manipulowania danymi, inżynieria i danych wyjściowych danych do usługi Azure Data Lake lub magazynu obiektów Blob Azure. Podając odniesienie danych w magazynie obiektów blob, użyj **wasb: / /**; w przypadku odwoływania danych w usłudze Azure Data Lake, użyj **swbhdfs: / /**

![Ramki danych](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

W programie Visual Studio, może używać następujących zapytań U-SQL:

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



Po przesłaniu zapytania do serwera wyświetlany jest diagram przedstawiający stan zadania.

![Diagram stanu zadania](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Zapytania na danych w usłudze Data Lake: U-SQL**

Po zestawu danych jest pozyskanych w usłudze Azure Data Lake, można użyć [języka U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) do wykonywania zapytań i eksplorować dane. Język U-SQL jest podobny do T-SQL, ale łączy niektóre funkcje w języku C#, dzięki czemu użytkownicy mogą zapisywać niestandardowych modułów, funkcje zdefiniowane przez użytkownika i itp. Skryptów można użyć w poprzednim kroku.

Po zapytaniu jest przesyłany do serwera, tripdata_summary. CSV znajdują się wkrótce w **Azure Data Lake Explorer**, użytkownik może Wyświetl podgląd danych, klikając prawym przyciskiem myszy plik.

![Plik w Eksploratorze Lake danych Azure](./media/vm-do-ten-things/USQL_create_summary.png)

Aby wyświetlić informacje o pliku:

![Podsumowanie pliku](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Klastrów HDInsight Hadoop
Usługa Azure HDInsight to zarządzana usługa Apache Hadoop, Spark, HBase i Storm w chmurze. Można łatwo pracować z klastrami Azure HDInsight z poziomu maszyny wirtualnej do analizy danych.

**Wymagania wstępne**

* Tworzenie konta magazynu obiektów Blob platformy Azure z [portalu Azure](https://portal.azure.com). To konto magazynu jest używany do przechowywania danych w przypadku klastrów usługi HDInsight.

![Tworzenie konta magazynu obiektów Blob platformy Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Dostosowywanie Azure platforma Hadoop w usłudze Hdinsight z [portalu Azure](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Połącz utworzone z klastrem usługi HDInsight, po utworzeniu konta magazynu. To konto magazynu jest używane do uzyskiwania dostępu do danych, które mogą być przetwarzane w ramach klastra.

![Łącze do konta magazynu utworzone z klastrem usługi HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Włącz **dostępu zdalnego** do węzła głównego klastra po jego utworzeniu. Pamiętaj poświadczenia dostępu zdalnego określone w tym miejscu, ponieważ będą potrzebne w następnej procedurze.

![Włączenie dostępu zdalnego](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Utwórz obszar roboczy usługi Azure Machine Learning. Z uczeniem maszynowym są przechowywane w tym obszaru roboczego uczenia maszynowego. Wybierz opcje wyróżnione w portalu, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie obszaru roboczego usługi Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Następnie wprowadź parametry dla obszaru roboczego

![Wprowadź parametry obszaru roboczego uczenia maszynowego](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Przekazywanie danych za pomocą notesu IPython. Najpierw zaimportuj wymagane pakiety, podłącz poświadczeń, Utwórz bazę danych na koncie magazynu, a następnie załadować dane do klastrów HDI.

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


* Alternatywnie można to wykonać [wskazówki](../team-data-science-process/hive-walkthrough.md) przekazywać dane taksówki NYC do klastra HDI. Główne kroki obejmują:
  
  * Narzędzie AzCopy: Pobierz zip CSV z publicznego obiektu blob do folderu lokalnego
  * Narzędzie AzCopy: Przekaż rozpakowane udostępnionych woluminach Klastra z lokalnego folderu do klastra HDI
  * Zaloguj się do węzła głównego klastra usługi Hadoop i Przygotuj się do analizy danych poznawcze

Po załadowaniu danych do klastra HDI można sprawdzić dane w Eksploratorze usługi Storage platformy Azure. I masz nyctaxidb bazy danych, utworzonych w klastra HDI.

**Eksploracja danych: zapytań programu Hive w języku Python**

Ponieważ dane są klastra usługi Hadoop, pakietu pyodbc możesz używać do nawiązania połączenia klastrów platformy Hadoop i zapytań bazy danych przy użyciu Hive eksploracji i inżynieria. Można wyświetlić istniejące tabele, utworzone w kroku wymagań wstępnych.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Wyświetlanie istniejących tabel](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Oto liczby rekordów w każdym miesiącu i częstotliwości występowania Przechylony lub nie znajduje się w tabeli podróży:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Wykres liczby rekordów w każdym miesiącu](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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


![Wykres programu końcówkę częstotliwości](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Można również obliczyć odległości między lokalizacją podnoszenia dropoff lokalizacji i porównania go odległość podróży.

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


![Odbiór i dropoff tabeli](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Wykres odległość podnoszenia/dropoff odległość podróży](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Teraz załóżmy przygotowanie próbkowany down (1%) zbiór danych do modelowania. W module czytnika uczenia maszynowego, można użyć tych danych.

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

        --- now insert contents of the join into the preceding internal table

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

Po chwili można zauważyć, że dane zostały załadowane w klastrów platformy Hadoop:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Tabela danych](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Odczytywanie danych z HDI za pomocą uczenia maszynowego: moduł czytnika**

Można także użyć **czytnika** modułu w usłudze Machine Learning Studio dostępu do bazy danych klastra usługi Hadoop. Dołącz poświadczenia HDI klastrów i konta magazynu Azure, aby umożliwić modeli przy użyciu bazy danych w klastrach HDI uczenia maszynowego budynku.

![Właściwości modułu Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Scored zestaw danych można następnie wyświetlić:

![Widok oceniane zestawu danych](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>& Baz danych Azure SQL Data Warehouse
Usługa Azure SQL Data Warehouse jest magazyn danych elastycznej jako usługi za pomocą klasy korporacyjnej środowisko programu SQL Server.

Azure SQL Data Warehouse można udostępnić, wykonując instrukcje podane w tym [artykułu](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zainicjowanie obsługi usługi Azure SQL Data Warehouse, możesz użyć tej funkcji [wskazówki](../team-data-science-process/sqldw-walkthrough.md) do przekazywania danych, badanie i modelowania w usłudze SQL Data Warehouse przy użyciu danych.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure DB rozwiązania Cosmos jest bazą danych NoSQL w chmurze. Umożliwia pracę z dokumentów, takich jak JSON i służy do przechowywania i zapytania dokumentów.

Poniżej przedstawiono kroki poszczególnych wymagań dostępu do bazy danych Azure rozwiązania Cosmos w DSVM:

1. Zestaw SDK Python usługi Azure rozwiązania Cosmos bazy danych jest już zainstalowana na DSVM (Uruchom ```pip install pydocumentdb --upgrade``` z wiersza polecenia, aby zaktualizować)
2. Tworzenie konta bazy danych Azure rozwiązania Cosmos i bazę danych z [portalu Azure](https://portal.azure.com)
3. Pobierz "Narzędzie migracji DB rozwiązania Cosmos Azure" z [tutaj](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) i wyodrębnij do katalogu wybranych przez użytkownika
4. Importuj dane JSON (dane swe dzieła) przechowywanych w [publicznego obiektu blob](https://cahandson.blob.core.windows.net/samples/volcano.json) w bazie danych rozwiązania Cosmos z następujących parametrów polecenia do narzędzia do migracji (dtui.exe z katalogu, w którym zainstalowano narzędzie do migracji DB rozwiązania Cosmos). Wprowadź lokalizację źródłowych i docelowych z następującymi parametrami:
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Po zaimportowaniu danych, można przejść do aplikacji Jupyter i otworzyć notesu zatytułowany *DocumentDBSample* zawierający kod języka python dostępu do bazy danych Azure rozwiązania Cosmos i czy niektóre podstawowe zapytań. Użytkownik może dowiedzieć się więcej o DB rozwiązania Cosmos, przechodząc na stronę usługi [stronę dokumentacji](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Twórz raporty i pulpit nawigacyjny za pomocą programu Power BI Desktop
Można zwizualizować pliku JSON swe dzieła z poprzedniego przykładu DB rozwiązania Cosmos w usłudze Power BI, aby uzyskać visual wgląd w dane. Szczegółowy opis kroków są dostępne w [artykułu usługi Power BI](../../cosmos-db/powerbi-visualize.md). Poniżej przedstawiono ogólne kroki:

1. Otwórz Power BI Desktop i "Pobierz dane." Podaj adres URL jako: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Powinny pojawić zaimportowany jako listę rekordów JSON
3. Dzięki usłudze Power BI można pracować z takimi samymi skonwertować listę tabeli
4. Rozwiń kolumny, klikając ikonę rozwijania (jedna z ikoną "Strzałka w lewo i Strzałka w prawo" po prawej stronie kolumny)
5. Należy zauważyć, że lokalizacja znajduje się pole "Rekordu". Rozwiń rekordu i wybierz tylko współrzędnych. Współrzędna jest kolumna listy
6. Dodaj nową kolumnę, aby przekonwertować kolumnę współrzędnych listy przecinkami osobna kolumna LatLong łączenie dwa elementy w polu listy współrzędnych przy użyciu formuły ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Konwertuj na koniec ```Elevation``` kolumny do dziesiętnego i wybierz **Zamknij** i **Zastosuj**.

Zamiast poprzednich krokach, mogą Wklej następujący kod skrypty mieszczą się z krokami w edytorze Zaawansowane w usłudze Power BI pozwala na zapis przekształcenia danych w języku zapytań.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Masz teraz danych w modelu danych usługi Power BI. Usługa Power BI pulpitu powinna wyglądać następująco:

![Program Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Można rozpocząć tworzenie raportów i wizualizacje przy użyciu modelu danych. Możesz wykonać kroki opisane w tym [artykułu usługi Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) Aby utworzyć raport. Dane wyjściowe są raport, który wygląda następująco.

![Power BI Desktop widoku raportu - łącznika usługi Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dynamiczne skalowanie sieci DSVM zgodnie z potrzebami projektu
Możesz skalować DSVM zgodnie z potrzebami projektu w górę i w dół. Jeśli nie musisz użyć maszyny Wirtualnej w wieczorem lub w weekendy, można po prostu zamknąć maszyny Wirtualnej z [portalu Azure](https://portal.azure.com).

> [!NOTE]
> Ponosisz opłat za obliczenia, jeśli używasz tylko przycisku zamknięcia systemu operacyjnego na maszynie Wirtualnej.  
> 
> 

Jeśli zachodzi konieczność obsługi analizy na dużą skalę i wymagają większej pojemności procesora CPU, pamięć lub dysk można znaleźć wybór dużych rozmiarów maszyn wirtualnych w postaci liczby rdzeni Procesora, oparte na procesorze Graficznym wystąpień głębokie uczenia, pojemność pamięci i typów dysków (w tym dysków SSD) który spełnia Twoich potrzeb dotyczących budżetowych i obliczeń. Pełną listę maszyn wirtualnych wraz z ich ceny co godzinę obliczeniowego jest dostępna na [cennik maszyn wirtualnych Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) strony.

Podobnie jeśli zmniejsza potrzeby wydajności przetwarzania maszyny Wirtualnej (na przykład: główne obciążenia są przenoszone do usługi Hadoop lub w klastrze Spark), można skalować w dół do klastra z [portalu Azure](https://portal.azure.com) i przechodząc do ustawień wystąpienia maszyny Wirtualnej. Poniżej przedstawiono zrzut ekranu.

![Ustawienia wystąpienie maszyny Wirtualnej](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Instalowanie dodatkowych narzędzi na maszynie wirtualnej
Istnieje kilka narzędzi wstępnie wbudowane DSVM, można rozwiązać wiele typowych danych, które wymaga analizy. Pozwala to zaoszczędzić czas, unikając konieczności Zainstaluj i skonfiguruj środowiska pojedynczo oraz oszczędzić pieniądze przez płatności tylko dla zasobów, należy użyć.

Można korzystać z innymi usługami Azure danych i ich analiza profilowane w tym artykule, aby ulepszyć środowisko analizy. W niektórych przypadkach potrzeb może wymagać dodatkowych narzędzi, w tym niektórych zastrzeżonych innych firm. Masz pełny dostęp administracyjny na maszynie wirtualnej, aby zainstalować nowe narzędzia, które są potrzebne. Można także zainstalować dodatkowe pakiety Python i R, które nie są wstępnie zainstalowane. Dla języka Python możesz użyć dowolnej ```conda``` lub ```pip```. W przypadku R można użyć ```install.packages()``` w R konsoli lub za pomocą środowiska IDE i wybierz polecenie "**pakiety** -> **instalowanie pakietów** ".

## <a name="summary"></a>Podsumowanie
Są to tylko niektóre czynności, które można wykonywać na Microsoft danych nauki maszyny wirtualnej. Istnieje wiele sposobów więcej można zrobić, aby go środowisku analytics skuteczne.

