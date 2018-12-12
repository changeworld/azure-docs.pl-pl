---
title: Utwórz maszynę wirtualną do nauki o danych Windows
titleSuffix: Azure
description: Konfigurowanie i tworzenie maszyny wirtualnej do nauki o danych na platformie Azure, analizy i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: gokuma
ms.openlocfilehash: 5dacbbcba44879b28e311f08b089d10d0ad8d95b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079640"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Aprowizacja maszyny wirtualnej do nauki o danych Windows na platformie Azure
Microsoft Data Science Virtual Machine (dsvm dystrybucji) jest obraz maszyny wirtualnej (VM) platformy Windows Azure. Ma wstępnie zainstalowane i skonfigurowane za pomocą kilku narzędzi, które są używane do analizy danych i uczenia maszynowego. Uwzględnione są następujące narzędzia:

* [Usługa Azure Machine Learning](../service/index.yml) zestawu SDK języka Python.
* [Serwer Microsoft Machine Learning](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Anaconda Python dystrybucji.
* Notes Jupyter przy użyciu języka R i Python oraz PySpark jądra.
* Microsoft Visual Studio Community.
* Microsoft Power BI desktop.
* Program Microsoft SQL Server 2017 Developer edition.
* Autonomiczne wystąpienie platformy Apache Spark do lokalnego tworzenia i testowania.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning i data analitycznymi:
  * Platformy uczenia głębokiego. Bogaty zestaw struktury sztucznej Inteligencji znajdują się na maszynie Wirtualnej: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet i biblioteki Keras.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit). Szybkie uczenia maszynowego, system, który obsługuje technik, takich jak online wyznaczania wartości skrótu, allreduce, redukcji, learning2search i aktywne i interaktywne uczenie się.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/). Narzędzie, które umożliwia szybkie i dokładne wzmocnione drzewo implementację.
  * [Rattle](https://togaware.com/rattle/), narzędzia analitycznego R, aby dowiedzieć się więcej w prosty sposób. To narzędzie, które ułatwia pracę dzięki analizie danych i uczenia maszynowego w języku R. Obejmuje Eksplorowanie i modelowanie za pomocą automatycznego generowania kodu języka R graficznego interfejsu użytkownika na podstawie danych.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/). Wyszukiwanie danych wizualnych i usługi machine learning oprogramowania w języku Java.
  * [Apache Drill](https://drill.apache.org/). Bez schematu aparatu zapytań SQL dla usługi Apache Hadoop, NoSQL i Magazyn w chmurze.  Obsługuje ona ODBC i sterownika JDBC interfejsy do wykonywania zapytań, NoSQL i pliki ze standardowych narzędzi analizy Biznesowej, takich jak Power BI, programie Microsoft Excel i Tableau.
* Użyj bibliotek w języków R i Python, aby uzyskać w usłudze Azure Machine Learning i innymi usługami platformy Azure.
* Git, w tym powłoki Git Bash, aby pracować z repozytoriów kodu źródłowego, które zawierają usługi GitHub i DevOps platformy Azure. Usługa Git oferuje kilka popularnych Linux narzędzi wiersza polecenia, które są dostępne zarówno w Git Bash, jak i wiersza polecenia. Przykładami są awk, sed, perl, grep, wyszukiwanie, wget i narzędzie curl.

Do nauki o danych obejmuje iteracja w sekwencji zadań:

1. Znajdź, ładowanie i przetwarzanie wstępne danych.
1. Tworzenie i testowanie modeli.
1. Wdrażaj modele do użycia w inteligentnych aplikacjach.

Analitycy danych używać kilku narzędzi do wykonywania tych zadań. Może zająć dużo czasu można znaleźć odpowiednie wersje oprogramowania, a następnie Pobierz i zainstaluj je. Maszyna wirtualna do nauki o danych firmy Microsoft pozwala zaoszczędzić czas, zapewniając obraz gotowych do użycia, które mogą być udostępniane na platformie Azure za pomocą kilku popularnych narzędzi wstępnie zainstalowane i skonfigurowane. 

Maszyna wirtualna do nauki o danych Microsoft jump-starts projektu analizy. Możesz pracować nad zadaniami w różnych językach, w tym języka R, Python, SQL i C#. Visual Studio zapewnia łatwe w użyciu zintegrowanego rozwoju środowiska (IDE) do tworzenia i testowania kodu. Zestaw SDK usługi Azure znajduje się na maszynie wirtualnej. Dlatego można tworzyć aplikacje przy użyciu różnych usług na platformie w chmurze firmy Microsoft. 

Opłaty nie będą oprogramowania dla tego obrazu maszyny Wirtualnej do nauki o danych. Opłaty są naliczane tylko opłaty za użycie platformy Azure. Są one zależne od rozmiaru maszyny wirtualnej, którą możesz aprowizować. Szczegółowe informacje na temat obliczeń opłaty znajdują się w **cennik** sekcji na [maszyny wirtualnej do nauki o danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) strony. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Inne wersje maszyna wirtualna do nauki o danych
* [Ubuntu](dsvm-ubuntu-intro.md) obrazu. Ma on wiele narzędzi, podobne do maszyny DSVM plus kilka dodatkowych platform do uczenia głębokiego. 
* A [Linux CentOS](linux-dsvm-intro.md) obrazu.
* [Wersji systemu Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) maszyny wirtualnej do nauki o danych. Niektóre narzędzia są dostępne tylko w wersji systemu Windows Server 2016. W przeciwnym razie ten artykuł dotyczy również do wersji Windows Server 2012.

## <a name="prerequisite"></a>Wymagania wstępne
Aby utworzyć maszynę wirtualną do nauki o danych firmy Microsoft, musi mieć subskrypcję platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Tworzenie maszyny wirtualnej do nauki o danych firmy Microsoft
Aby utworzyć wystąpienie programu Microsoft maszyny wirtualnej analizy danych, wykonaj następujące kroki:

1. Przejdź do maszyny wirtualnej, w witrynie [witryny Azure portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Może zostać wyświetlony monit można zalogować się do konta platformy Azure, jeśli użytkownik są nie już zalogowany.
1. Wybierz **Utwórz** znajdujący się u dołu, aby uwzględnić kreatora.

  ![Konfigurowanie — — — maszyna wirtualna analizy danych](./media/provision-vm/configure-data-science-virtual-machine.png) 

1. Kreator, który umożliwia utworzenie maszyny wirtualnej do nauki o danych firmy Microsoft wymaga **wejściowych**. Aby skonfigurować poszczególne kroki wyświetlane po prawej stronie rysunku potrzebne są następujące dane wejściowe:

  a. **Podstawy**:

    i. **Nazwa**. Nazwa serwera do nauki o danych, który tworzysz.  

    ii. **Typ dysku maszyny Wirtualnej**. Wybierz **SSD** lub **HDD**. W przypadku wystąpienia procesora GPU NC_v1 takich jak procesory GPU NVidia Tesla K80 na podstawie wybierz **HDD** jako typ dysku.   

    III. **Nazwa użytkownika**. Identyfikator konta administratora do logowania.   

    IV. **Hasło**. Hasło do konta administratora.  

    v. **Subskrypcja**. Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane.   

    VI. **Grupa zasobów**. Możesz utworzyć nową lub użyj istniejącej grupy.   

    VII. **Lokalizacja**. Wybierz centrum danych, które jest najbardziej odpowiednie. Najszybszy dostęp do sieci to centrum danych, który zawiera większość danych lub znajduje się najbliżej Twojej lokalizacji fizycznej.   

  b. **Rozmiar**. Wybierz jeden z typów serwerów, które spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Aby wyświetlić więcej rozmiarów maszyn wirtualnych, wybierz **Wyświetl wszystko**.  

  c. **Ustawienia**:  

    i. **Użyj usługi Managed Disks**. Wybierz **zarządzane** chcącym platformy Azure do zarządzania dyskami maszyny wirtualnej. W przeciwnym razie należy określić konto magazynu w nowym lub istniejącym.  

    ii. **Inne parametry**. Można użyć wartości domyślnych. Jeśli chcesz użyć wartości innej niż domyślna, umieść kursor nad informacyjny łącze, aby uzyskać pomoc dotyczącą określonych pól.  

  d. **Podsumowanie**. Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. Wybierz pozycję **Utwórz**. 

> [!NOTE]
> * Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza koszt obliczeń dla rozmiaru serwera wybranej w ramach **rozmiar** kroku. 
> * Aprowizowanie trwa około 10-20 minut. Jego stan jest wyświetlany w witrynie Azure portal.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej do nauki o danych firmy Microsoft
Po maszyny Wirtualnej jest tworzony i zainicjowano obsługę administracyjną, można pulpitu zdalnego do niej przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. Możesz rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Mają wiele narzędzi, kafelków menu start i ikony pulpitu. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Narzędzi zainstalowanych w programie Microsoft maszyny wirtualnej analizy danych

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition
Można użyć Microsoft Enterprise Library dla skalowalnych R lub Python do celów analizy, ponieważ Machine Learning Server Developer edition jest zainstalowany na maszynie Wirtualnej. Wcześniej znane jako Microsoft R Server, serwer Machine Learning to platforma analityczna szeroko wdrażanym klasy korporacyjnej. Jest dostępna dla języków R i Python i skalowalne, komercyjnie obsługiwanych i bezpieczne. 

Serwer usługi Machine Learning obsługuje różne statystyki danych big data, modelowanie predykcyjne i zadania uczenia maszynowego. Obsługuje ona pełnego zakresu analytics: eksplorację, analizę, wizualizacji i modelowania. Dzięki użyciu i rozszerzenie typu open source R i Python, serwer Machine Learning jest zgodny z języków R i Python, skryptach i funkcjach. Jest również zgodna z sieci CRAN, pip i pakietów Conda, do analizowania danych w skali przedsiębiorstwa. 

Serwer Machine Learning adresy ograniczeniami pamięci języka r typu open source, dodając równoległe i fragmentaryczne przetwarzania danych. Dlatego możesz uruchomić analitykę na dużą ilość danych większe niż w pamięci głównej. Program Visual Studio Community znajduje się na maszynie Wirtualnej. Posiada R tools for Visual Studio i narzędzi języka Python rozszerzenia programu Visual Studio (PTVS), które zapewnia pełne środowisko IDE do pracy z języka R lub Python. Firma Microsoft udostępnia również innych środowisk IDE, takie jak [RStudio](http://www.rstudio.com) i [platformy PyCharm Community edition](https://www.jetbrains.com/pycharm/) na maszynie Wirtualnej. 

### <a name="python"></a>Python
Do tworzenia aplikacji przy użyciu języka Python zostały zainstalowane dystrybucje Anaconda Python 2.7 i 3.6. Tych dystrybucji mają podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy. Możesz użyć narzędzi PTVS, który jest instalowany w ramach programu Visual Studio Community 2017. Lub można użyć jednego środowiska IDE, powiązane z pakietu Anaconda, takich jak BEZCZYNNE lub Spyder. Wyszukaj, a następnie uruchom jeden z tych pakietów (Win + S).

> [!NOTE]
> Aby wskazać narzędzi Python Tools for Visual Studio w środowisko Anaconda Python 2.7, musisz utworzyć niestandardowego środowiska dla każdej wersji. Aby ustawić te ścieżki środowiska Visual Studio 2017 Community, przejdź do **narzędzia** > **narzędzi Python Tools** > **środowiska Python**. Następnie wybierz pozycję **+ niestandardowe**. 
> 
> 

Anaconda Python 3.6 jest zainstalowany w ramach **C:\Anaconda**. Anaconda Python 2.7 jest zainstalowany w ramach **c:\Anaconda\envs\python2**. Aby uzyskać szczegółowe instrukcje, zobacz [dokumentacja narzędzi PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters). 

### <a name="the-jupyter-notebook"></a>Notes Jupyter
Dystrybucja pakietu anaconda również jest dostarczany z notesu programu Jupyter, środowisko, aby udostępniać kod i analizy. Serwer notesu programu Jupyter jest wstępnie skonfigurowana za pomocą języka Python 2.7 Python 3.x, PySpark, Julia i R jądra. Uruchom serwer Jupyter i uruchom przeglądarkę, aby uzyskać dostęp do serwera notesu istnieje ikony pulpitu nosi nazwę **notesu programu Jupyter**. 

Firma Microsoft spakować kilka notesów przykładowy w języku Python i R. Po uzyskaniu dostępu programu Jupyter, notesów pokazują, jak pracować z następujących technologii:

* Serwer Machine Learning.
* SQL Server Machine Learning Services, analiza w bazie danych. 
* Python.
* Microsoft Cognitive ToolKit.
* Tensorflow.
* Inne technologie platformy Azure. 

Zobaczysz łącza do przykładów na stronie głównej Notes, po uwierzytelnieniu w notesie Jupyter przy użyciu hasła utworzonego w poprzednim kroku. 

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017
Program Visual Studio Community jest zainstalowany na maszynie Wirtualnej. To darmowa wersja popularnego środowiska IDE firmy Microsoft, która służy do celów oceny i małych zespołów. Zobacz [postanowienia licencyjne](https://www.visualstudio.com/support/legal/mt171547). 

Otwórz program Visual Studio przez dwukrotne kliknięcie ikony pulpitu lub **Start** menu. Wyszukaj programy (Win + S), a następnie **programu Visual Studio**. W tym miejscu mogą tworzyć projekty w językach takich jak C#, Python, R i node.js. Zainstalowane dodatki plug-in należy wygodne do pracy z następującymi usługami platformy Azure:
* Azure Data Catalog
* Usługa Azure HDInsight Hadoop i Spark
* Azure Data Lake 

Istnieje również wtyczki, wywoływana ```Azure Machine Learning for Visual Studio Code``` który bezproblemowo integruje się do usługi Azure Machine Learning i pomaga szybko Twórz aplikacje sztucznej Inteligencji. 

> [!NOTE]
> Możesz otrzymać komunikat, okres próbny wygasł. Wprowadź poświadczenia konta Microsoft. Lub Utwórz nowe bezpłatne konto, aby uzyskać dostęp do programu Visual Studio Community. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Dla deweloperów w wersji programu SQL Server 2017 przy użyciu usług Machine Learning, aby uruchamiać funkcje analityczne w bazie danych znajduje się na maszynie Wirtualnej w języku R lub Python. Usługi Machine Learning zapewnia platformę do tworzenia i wdrażania inteligentnych aplikacji. Języki te i wiele pakietów przez społeczność służy do tworzenia modeli i generować prognozy dla danych programu SQL Server. Umożliwia zachowanie analytics blisko danych, ponieważ usługi Machine Learning, w bazie danych, integruje się języków R i Python w programie SQL Server. Ta integracja pozwala wyeliminować koszty i zagrożenia bezpieczeństwa związane z przenoszeniem danych.

> [!NOTE]
> Wersja programu SQL Server Developer jest tylko do celów tworzenia i testowania. Niezbędna jest licencja go uruchomić w środowisku produkcyjnym. 
> 
> 

Dostęp z programu SQL Server, uruchamiając program Microsoft SQL Server Management Studio. Nazwę maszyny Wirtualnej jest wypełniana jako **nazwy serwera**. Uwierzytelnianie Windows po zalogowaniu się jako administrator na Windows. Po otwarciu programu SQL Server Management Studio można tworzyć innych użytkowników, tworzenie baz danych, importowanie danych i uruchamiać zapytania SQL. 

Aby włączyć analizę w bazie danych za pomocą usług SQL Machine Learning, uruchom następujące polecenie jako akcja jednorazowa w programie SQL Server Management Studio, po zalogowaniu się jako administrator serwera: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Kilku narzędzi platformy Azure są zainstalowane na maszynie Wirtualnej:

* Skrót na pulpicie przechodzi do dokumentacji zestawu SDK platformy Azure. 
* **Narzędzie AzCopy** służy do przenoszenia danych do i z konta usługi Azure Storage. Aby zobaczyć sposób użycia, wprowadź **Azcopy** polecenie w wierszu polecenia. 
* Użyj **Eksploratora usługi Azure Storage** do przeglądania obiektów przechowywanych na koncie usługi Azure Storage. Przesyłania danych do i z usługi Azure Storage. Aby uzyskać dostęp do tego narzędzia, możesz wprowadzić **Eksploratora usługi Storage** w **wyszukiwania** pola. Lub znaleźć go na Windows **Start** menu. 
* **Adlcopy** przenosi dane do usługi Azure Data Lake. Aby zobaczyć sposób użycia, wprowadź **adlcopy** w wierszu polecenia. 
* **dtui** przenoszenia danych do i z usługi Azure Cosmos DB, bazy danych NoSQL w chmurze. Wprowadź **dtui** w wierszu polecenia. 
* **Środowisko uruchomieniowe usługi Azure Data Factory integracji** przenosi dane między lokalnymi źródłami danych i w chmurze. Jest używany w ramach narzędzi, takich jak usługi Azure Data Factory. 
* **Microsoft Azure PowerShell** to narzędzie, które jest używane do administrowania zasobami platformy Azure w języku skryptów programu PowerShell. Jest również instalowany na maszynie Wirtualnej. 

### <a name="power-bi"></a>Power BI
**Power BI Desktop** jest zainstalowana w celu ułatwienia tworzenia pulpitów nawigacyjnych i wizualizacji. To narzędzie do pobierania danych z różnych źródeł, tworzyć pulpity nawigacyjne i raporty i opublikuj je w chmurze. Aby uzyskać więcej informacji, zobacz [usługi Power BI](https://powerbi.microsoft.com) lokacji. Power BI desktop można znaleźć na **Start** menu. 

> [!NOTE]
> Wymagane jest konto Microsoft Office 365, aby dostęp do usługi Power BI. 
> 
> 

### <a name="azure-machine-learning-service-python-sdk"></a>Usługa Azure Machine Learning zestawu SDK języka Python

Zestaw Azure Machine Learning SDK for Python jest używany przez analityków danych i deweloperom sztucznej Inteligencji, aby skompilować i uruchomić machine learning z przepływów pracy za pomocą [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md). Możesz porozmawiać z usługi w dowolnym środowisku Python, w tym notesów programu Jupyter lub ulubionego środowiska IDE języka Python, przy użyciu struktur typu open source, takich jak TensorFlow i scikit-Dowiedz się więcej.

Aby rozpocząć pracę, przy użyciu zestawu SDK języka Python, zobacz [za pomocą języka Python Rozpoczynanie pracy z usługą Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Zestaw SDK języka Python jest preinstalowany na Microsoft maszyny wirtualnej analizy danych.

## <a name="more-microsoft-development-tools"></a>Więcej narzędzi programistycznych firmy Microsoft
[Instalatora platformy sieci Web firmy Microsoft](https://www.microsoft.com/web/downloads/platform.aspx) jest używany w celu znalezienia i pobrania innych narzędzi programistycznych firmy Microsoft. Istnieje również skrót do narzędzia podane na pulpicie maszyny wirtualnej do nauki o danych firmy Microsoft.  

## <a name="important-directories-on-the-vm"></a>Ważne katalogów na maszynie Wirtualnej
| Element | Katalog |
| --- | --- |
| Konfiguracje serwera notesu programu Jupyter | C:\ProgramData\jupyter |
| Katalog macierzysty przykłady notesu programu Jupyter | c:\dsvm\notebooks i c:\users\<username > \notebooks |
| Inne przykłady | c:\dsvm\samples |
| Anaconda, domyślna: środowisko Python 3.6 | c:\Anaconda |
| Anaconda Python 2.7 środowisko | c:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (autonomiczna) w języku Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Wystąpienia domyślnego języka R, serwer Machine Learning (Wersja autonomiczna) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Katalog wystąpienia w bazie danych SQL Machine Learning Services | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Różne narzędzia | c:\dsvm\tools |

> [!NOTE]
> W tej wersji systemu Windows Server 2012 wersji maszyny wirtualnej DSVM i Windows Server 2016 przed marca 2018 r. domyślne środowisko Anaconda jest język Python 2.7. Pomocniczy środowisko jest język Python 3.5, znajdujący się w **c:\Anaconda\envs\py35**. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki

* Odkryj narzędzia i na maszyny Wirtualnej do analizy danych, wybierając **Start** menu.
* Dowiedz się więcej o usłudze Azure Machine Learning, czytając [co to jest usługa Azure Machine Learning?](../service/overview-what-is-azure-ml.md) i wypróbować [przewodników Szybki Start i samouczki](../service/index.yml) , które są dostępne.
* Przejdź do **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** przykładów, korzystające z biblioteki kolekcję funkcji RevoScaleR w języku R, który obsługuje analizy danych w skali przedsiębiorstwa.  
* Przeczytaj artykuł [dziesięć rzeczy, które można wykonać na maszynie wirtualnej analizy danych](https://aka.ms/dsvmtenthings).
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](../team-data-science-process/index.yml).
* Odwiedź stronę [galerii Azure AI](http://gallery.cortanaintelligence.com) nauki i dane analizy przykładowych maszyny korzystające z usługi Azure Machine Learning i powiązane dane usług na platformie Azure. Podano także ikony dla tej galerii na **Start** menu i pulpitu maszyny wirtualnej.

