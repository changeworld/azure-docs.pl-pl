---
title: Aprowizacja maszyny wirtualnej do nauki o danych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie i tworzenie maszyny wirtualnej do nauki o danych na platformie Azure, analizy i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: b749d8a904bc40eba3346cc03d9274236380c80d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449757"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Aprowizacja maszyny wirtualnej do nauki o danych Windows na platformie Azure
Maszyna wirtualna do nauki o danych firmy Microsoft jest obraz maszyny wirtualnej (VM) platformy Windows Azure wstępnie zainstalowane i skonfigurowane za pomocą kilku popularnych narzędzi, które są często używane do analizy danych i uczenia maszynowego. Narzędzia uwzględnione są:

* [Usługi Azure Machine Learning](../service/index.yml) aplikacji Workbench
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python dystrybucji
* Notes Jupyter (przy użyciu języka R, Python, jądra PySpark)
* Visual Studio Community Edition
* Program Power BI Desktop
* SQL Server 2017 Developer Edition
* Spark autonomiczne wystąpienie lokalne programowanie i testowanie
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning i narzędzia do analizy danych
  * Głębokie uczenie struktury: bogaty zestaw struktur sztucznej Inteligencji, w tym [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras znajdują się na maszynie Wirtualnej.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Obsługa technik, takich jak online, wyznaczania wartości skrótu, allreduce, redukcji, learning2search, są aktywne, system uczenia maszynowego, szybkie i interaktywne szkolenia.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): to narzędzie, zapewniając szybkie i dokładne wzmocnionego drzewa wykonania.
  * [Rattle](http://rattle.togaware.com/) (R analitycznych narzędzie do Dowiedz się, łatwo): to narzędzie, które ułatwia rozpoczęcie korzystania z analizy danych i uczenia maszynowego w R łatwo. Obejmuje Eksplorowanie i modelowanie za pomocą automatycznego generowania kodu języka R graficznego interfejsu użytkownika na podstawie danych.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : wyszukiwania danych wizualnych oraz usługi machine learning oprogramowania w języku Java.
  * [Apache Drill](https://drill.apache.org/): schematów aparatu zapytań SQL do Hadoop, NoSQL i Magazyn w chmurze.  Obsługuje ODBC i sterownika JDBC interfejsów, które umożliwia tworzenie zapytań plików ze standardowych narzędzi analizy Biznesowej, takich jak usługi Power BI, Excel, Tableau i NoSQL.
* Bibliotek języka R i Python do użycia w usłudze Azure Machine Learning i innymi usługami platformy Azure
* Git, w tym powłoki Git Bash, aby pracować z repozytoriów kodu źródłowego, w tym witrynę GitHub, Visual Studio Team Services i udostępnia kilka popularnych Linux narzędzi wiersza polecenia (w tym awk sed, perl, grep, wyszukiwanie, wget, curl, itp.) dostępne zarówno na powłoki git bash i polecenia wiersz. 

Nauki o danych obejmuje iteracja w sekwencji zadań:

1. Znajdowanie, ładowanie i przetwarzanie wstępne danych
1. Tworzenie i testowanie modeli
1. Wdrażanie modeli do użycia w inteligentnych aplikacjach

Analitycy danych użyj szeroką gamą narzędzi w celu wykonania tych zadań. Może być bardzo czasochłonne znaleźć odpowiednie wersje oprogramowania, a następnie Pobierz i zainstaluj je. Maszyna wirtualna do nauki o danych firmy Microsoft mogą ułatwić ten ciężar, zapewniając obraz gotowych do użycia, które mogą być udostępniane na platformie Azure przy użyciu wszystkich kilku popularnych narzędzi wstępnie zainstalowane i skonfigurowane. 

Maszyna wirtualna do nauki o danych Microsoft jump-starts projektu analizy. Pozwala ona do pracy nad zadaniami w różnych językach, w tym języka R, Python, SQL i C#. Program Visual Studio zapewnia środowisko IDE, aby tworzyć i testować swój kod, który jest łatwy w użyciu. Uwzględnione na maszynie wirtualnej i zestawu Azure SDK umożliwia tworzenie aplikacji za pomocą różnych usług na platformie w chmurze firmy Microsoft. 

Opłaty nie będą oprogramowania dla tego obrazu maszyny Wirtualnej do nauki o danych. Płacisz tylko opłaty za użycie platformy Azure które w zależności od rozmiaru maszyny wirtualnej, którą możesz aprowizować. Szczegółowe informacje na temat opłat obliczeniowych można znaleźć w sekcji Szczegóły cennika na [maszyny wirtualnej do nauki o danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) strony. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Inne wersje maszyna wirtualna do nauki o danych
[Ubuntu](dsvm-ubuntu-intro.md) obraz jest dostępny, za pomocą wielu narzędzi podobne plus kilka dodatkowych platform do uczenia głębokiego. A [CentOS](linux-dsvm-intro.md) obrazu jest również dostępna. Oferujemy również [wersji systemu Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) maszyny wirtualnej do nauki o danych mimo że niektóre narzędzia są dostępne tylko w wersji systemu Windows Server 2016.  W przeciwnym razie ten artykuł dotyczy również do wersji Windows Server 2012.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem maszyny wirtualnej do nauki o danych firmy Microsoft, musisz mieć następujące czynności:

* **Subskrypcja platformy Azure**: Aby można było go uzyskać, zobacz [uzyskiwanie bezpłatnej wersji próbnej platformy Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Tworzenie maszyny wirtualnej do nauki o danych firmy Microsoft
Aby utworzyć wystąpienie programu Microsoft maszyny wirtualnej analizy danych, wykonaj następujące kroki:

1. Przejdź do maszyny wirtualnej, w witrynie [witryny Azure portal](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
1. Wybierz **Utwórz** znajdujący się u dołu, aby uwzględnić kreatora.![ Konfigurowanie — — — maszyna wirtualna analizy danych](./media/provision-vm/configure-data-science-virtual-machine.png)
1. Kreator używany do tworzenia maszyny wirtualnej do nauki o danych firmy Microsoft wymaga **dane wejściowe** dla każdego z **cztery kroki** wyliczane po prawej stronie tej liczby. Poniżej przedstawiono dane wejściowe wymagane do skonfigurowania każdego z następujących czynności:
   
   1. **Podstawy**
      
      1. **Nazwa**: Nazwa serwera do nauki o danych tworzysz.
      1. **Typ dysku maszyny Wirtualnej**: Wybieranie między SSD lub HDD. Dla procesora GPU NC_v1 wystąpienia (procesory GPU NVidia Tesla K80 na podstawie), wybierz **HDD** jako typ dysku. 
      1. **Nazwa użytkownika**: identyfikator logowania konta administratora.
      1. **Hasło**: hasło konta administratora.
      1. **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane.
      1. **Grupa zasobów**: możesz utworzyć nową lub użyj istniejącej grupy.
      1. **Lokalizacja**: wybierz centrum danych, które jest najbardziej odpowiednie. Zazwyczaj jest centrum danych, który zawiera większość danych lub znajduje się najbliżej Twojej lokalizacji fizycznej najszybszy dostęp do sieci.
   1. **Rozmiar**: Wybierz jeden z typów serwerów, które spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Więcej opcji dotyczących rozmiarów maszyn wirtualnych można uzyskać, wybierając pozycję "Wyświetl wszystko".
   1. **Ustawienia**:
      
      1. **Użycie usługi Managed Disks**: Wybierz zarządzany, jeśli chcesz, aby platformy Azure do zarządzania dyskami maszyny wirtualnej.  W przeciwnym razie należy określić konto magazynu w nowym lub istniejącym. 
      1. **Inne parametry**: zwykle wystarczy użyć wartości domyślnych. Jeśli chcesz wziąć pod uwagę przy użyciu wartości innych niż domyślne, umieść kursor nad informacyjny łącze, aby uzyskać pomoc dotyczącą określonych pól.
    a. **Podsumowanie**: Sprawdź, czy wszystkie informacje wprowadzone jest poprawny, a następnie kliknij przycisk **Utwórz**. **Uwaga**: maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczenia rozmiaru serwera wybranej w ramach **rozmiar** kroku. 

> [!NOTE]
> Aprowizacja powinno zająć około 10-20 minut. Stan aprowizacji jest wyświetlany w witrynie Azure portal.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej do nauki o danych firmy Microsoft
Po utworzeniu maszyny Wirtualnej możesz pulpitu zdalnego do niej przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. 

Po utworzeniu maszyny Wirtualnej i zainicjowano obsługę administracyjną, jesteś gotowy rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na nim. Brak kafelków menu start i ikony pulpitu dla wielu narzędzi. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Narzędzi zainstalowanych w programie Microsoft maszyny wirtualnej analizy danych



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Jeśli chcesz użyć biblioteki systemu Microsoft enterprise dla skalowalnych R lub Python do celów analizy, maszyna wirtualna ma Microsoft ML Server Developer (wcześniej znane jako Microsoft R Server) zainstalowanej wersji. Microsoft ML Server to platforma analiz szeroko wdrażanym klasy korporacyjnej dostępne dla języków R i Python oraz jest skalowalna, komercyjnie obsługiwanych i bezpieczne. Obsługa różnych statystyk danych big Data, modelowanie predykcyjne i możliwości uczenia maszynowego, ML Server obsługuje pełną gamę analytics — eksplorację, analizę, wizualizacji i modelowania. Dzięki użyciu i rozszerzenie typu open source R i Python, Microsoft ML Server jest w pełni zgodny, przy użyciu języka R / skryptów języka Python, funkcje i CRAN / pip / skalowanie Conda pakietów do analizy danych w przedsiębiorstwie. Dotyczy również ograniczeniami pamięci programu Open Source R, dodając przetwarzanie równoległe i fragmentaryczne danych. Dzięki temu można wykonywać analizy danych znacznie większe niż w pamięci głównej.  Visual Studio Community Edition zawarte na maszynie Wirtualnej zawiera R Tools for Visual Studio i języka Python tools dla rozszerzenia programu Visual Studio, który zapewnia pełne środowisko IDE do pracy z języka R lub Python. Firma Microsoft udostępnia również innych środowisk IDE oraz takich jak [RStudio](http://www.rstudio.com) i [platformy PyCharm Community edition](https://www.jetbrains.com/pycharm/) na maszynie Wirtualnej. 

### <a name="python"></a>Python
Do tworzenia aplikacji przy użyciu języka Python została zainstalowana dystrybucja Anaconda Python 2.7 i 3.6. Rozkład ten zawiera podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy. Możesz użyć narzędzi Python Tools for Visual Studio (PTVS) zainstalowanego w ramach wersji programu Visual Studio 2017 Community edition lub jednego z IDE, powiązane z pakietu Anaconda, takich jak BEZCZYNNE lub Spyder. Możesz uruchomić jeden z nich, wyszukując na pasku wyszukiwania (**wygrać** + **S** klucza).

> [!NOTE]
> Aby wskazać narzędzi Python Tools for Visual Studio w środowisko Anaconda Python 2.7, musisz utworzyć niestandardowego środowiska dla każdej wersji. Aby ustawić te ścieżki środowiska Visual Studio 2017 Community Edition, przejdź do **narzędzia** -> **narzędzi Python Tools** -> **środowiska Python**a następnie kliknij przycisk **+ niestandardowe**. 
> 
> 

Anaconda Python 3.6 jest zainstalowany w ramach C:\Anaconda i Anaconda Python 2.7 jest zainstalowany w ramach c:\Anaconda\envs\python2. Zobacz [dokumentacja narzędzi PTVS](/visualstudio/python/installing-python-interpreters.md) szczegółowy opis kroków. 

### <a name="jupyter-notebook"></a>Jupyter Notebook
Dystrybucja pakietu anaconda również jest dostarczany z notesu programu Jupyter, środowisko, aby udostępniać kod i analizy. Serwer notesu Jupyter został wstępnie skonfigurowany przy użyciu języka Python 2.7 Python 3.x, jądra PySpark, Julia i R. Brak ikony pulpitu, o nazwie "Notesu Jupyter" w celu uruchomienia serwera Jupyter i uruchom przeglądarkę, aby uzyskać dostęp do serwera notesu. 

Firma Microsoft spakowaniu kilka notesów przykładowy w języku Python i R. Notesy Jupyter pokazują, jak pracować z usługą Microsoft ML Server, usług uczenia Maszynowego programu SQL Server (analiza w bazie danych), Python, Microsoft Cognitive ToolKit, Tensorflow i inne technologie platformy Azure, gdy uzyskujesz dostęp do aplikacji Jupyter. Łącza do przykładów widoczne na stronie głównej notesu po uwierzytelnieniu do notesu programu Jupyter, przy użyciu hasła utworzonego w poprzednim kroku. 

### <a name="visual-studio-2017-community-edition"></a>Program Visual Studio 2017 Community edition
Program Visual Studio Community edition zainstalowane na maszynie Wirtualnej. To darmowa wersja popularnego środowiska IDE firmy Microsoft, która służy do celów ewaluacyjnych i dla małych zespołów. Możesz sprawdzić postanowienia licencyjne [tutaj](https://www.visualstudio.com/support/legal/mt171547).  Otwórz program Visual Studio przez dwukrotne kliknięcie ikony pulpitu lub **Start** menu. Można także przeszukać w ramach programów ze **wygrać** + **S** i wprowadzając "Visual Studio". Gdy mogą tworzyć projekty w językach takich jak językach C#, Python, R, środowiska node.js. Wtyczki instalowane są również, które ułatwiają wygodne do pracy z usługami platformy Azure, takich jak Azure Data Catalog, Azure HDInsight (Hadoop, Spark) i Azure Data Lake. Teraz dostępna jest również wtyczki, wywoływana ```Visual Studio Tools for AI``` który bezproblemowo integruje się do usługi Azure Machine Learning i pomaga szybko Twórz aplikacje sztucznej Inteligencji. 

> [!NOTE]
> Może zostać komunikat z informacją, że okres próbny wygasł. Wprowadź poświadczenia konta Microsoft lub Utwórz nowe bezpłatne konto, aby uzyskać dostęp do programu Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Dla deweloperów w wersji programu SQL Server 2017 z usługami uczenia Maszynowego, aby uruchamiać funkcje analityczne w bazie danych znajduje się na maszynie Wirtualnej w języku R lub Python. Usługi ML udostępniają platforma do tworzenia i wdrażania aplikacji inteligentnych. Umożliwia rozbudowane i zaawansowane języki te i wiele pakietów od społeczności tworzenie modeli i generować prognozy dla danych programu SQL Server. Umożliwia zachowanie analytics blisko danych, ponieważ usługi ML (In-database) integruje się języka R i Python w programie SQL Server. Pozwala to wyeliminować koszty i zagrożenia bezpieczeństwa związane z przenoszeniem danych.

> [!NOTE]
> Wersja developer programu SQL Server należy używać tylko do tworzenia i testowania. Niezbędna jest licencja go uruchomić w środowisku produkcyjnym. 
> 
> 

Dostęp do programu SQL server, uruchamiając **SQL Server Management Studio**. Nazwę maszyny Wirtualnej jest wypełniana jako nazwy serwera. Użyj uwierzytelniania Windows, gdy zalogowany jako administrator na Windows. Po otwarciu programu SQL Server Management Studio można tworzyć innych użytkowników, tworzenie baz danych, importowanie danych i uruchamiać zapytania SQL. 

Aby włączyć w bazie danych usługi analytics przy użyciu usługi ML SQL, uruchom następujące polecenie jako czas działania w programie SQL Server management studio po zalogowaniu się jako administrator serwera. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Kilku narzędzi platformy Azure są zainstalowane na maszynie Wirtualnej:

* Brak skrótu na pulpicie, aby uzyskać dostęp do dokumentacji zestawu SDK usługi Azure. 
* **Narzędzie AzCopy**: używane do przenoszenia danych do i z konta usługi Microsoft Azure Storage. Aby zobaczyć sposób użycia, wpisz **Azcopy** w wierszu polecenia, aby sprawdzić użycie. 
* **Microsoft Azure Storage Explorer**: pozwala przeglądać obiekty, które są przechowywane w ramach konta usługi Azure Storage i transfer danych do i z usługi Azure storage. Możesz wpisać **Eksploratora usługi Storage** w Wyszukaj lub Znajdź je w menu Windows Start, aby dostęp do tego narzędzia. 
* **Adlcopy**: używane do przenoszenia danych do usługi Azure Data Lake. Aby zobaczyć sposób użycia, wpisz **adlcopy** w wierszu polecenia. 
* **dtui**: używane do przenoszenia danych do i z usługi Azure Cosmos DB, bazy danych NoSQL w chmurze. Typ **dtui** w wierszu polecenia. 
* **Środowisko uruchomieniowe usługi Azure Data Factory integracji**: umożliwia przenoszenie danych między lokalnymi źródłami danych i w chmurze. Jest używany w ramach narzędzi, takich jak usługi Azure Data Factory. 
* **Microsoft Azure Powershell**: narzędzie służące do administrowania zasobami platformy Azure w programie Powershell język skryptowy również jest zainstalowany na maszynie Wirtualnej. 

### <a name="power-bi"></a>Power BI
Ułatwiające tworzenie pulpitów nawigacyjnych i wizualizacji doskonałe **Power BI Desktop** został zainstalowany. To narzędzie do pobierania danych z różnych źródeł, tworzyć pulpity nawigacyjne i raporty i opublikuj je w chmurze. Aby uzyskać informacje, zobacz [usługi Power BI](http://powerbi.microsoft.com) lokacji. Power BI desktop można znaleźć w Start menu. 

> [!NOTE]
> Wymagane jest konto usługi Office 365, aby dostęp do usługi Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Środowisko robocze usługi Azure Machine Learning

Usługa Azure Machine Learning Workbench jest aplikacja klasyczna i interfejs wiersza polecenia. Usługa Workbench została wbudowana funkcja przygotowywania danych który poznaje kroki przygotowywania, jak je wykonujesz. Umożliwia także zarządzanie projektami, historii uruchamiania i integracji z notesem należy zwiększyć produktywność. Możesz skorzystać z najlepszych platform typu open source, w tym TensorFlow, Cognitive Toolkit, Spark ML i scikit-Dowiedz się, jak tworzyć modele. Na nauki firma Microsoft oferuje ikony pulpitu, aby zainstalować aplikację Azure Machine Learning workbench w katalogu % LOCALAPPDATA % indywidualnego użytkownika. Każdy użytkownik, który musi korzystać z aplikacji Workbench potrzebuje do wykonywania na czas działania dwukrotne kliknięcie ```AzureML Workbench Setup``` ikony pulpitu, aby zainstalować ich wystąpienie aplikacji Workbench. Usługa Azure Machine Learning również tworzy i wykorzystuje środowisko Python na użytkownika, które są wyodrębniane w % LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Dodatkowe narzędzia programistyczne Microsoft
[ **Instalatora platformy sieci Web firmy Microsoft** ](https://www.microsoft.com/web/downloads/platform.aspx) służy do odnajdywania i Pobierz innych narzędzi programistycznych firmy Microsoft. Istnieje również skrót do narzędzia podane na pulpicie maszyny wirtualnej do nauki o danych firmy Microsoft.  

## <a name="important-directories-on-the-vm"></a>Ważne katalogów na maszynie Wirtualnej
| Element | Katalog |
| --- | --- |
| Konfiguracje serwera notesu programu Jupyter |C:\ProgramData\jupyter |
| Katalog macierzysty przykłady notesu programu Jupyter |c:\dsvm\notebooks i c:\users\<username > \notebooks|
| Inne przykłady |c:\dsvm\samples |
| Anaconda (domyślne: środowisko Python 3.6) |c:\Anaconda |
| Anaconda Python 2.7 środowisko |c:\Anaconda\envs\python2 |
| Microsoft ML Server autonomicznego w języku Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Wystąpienie domyślne języka R (ML serwer autonomiczny) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| Katalog wystąpienia w bazie danych SQL usługi ML |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Usługa Azure Machine Learning Workbench (na użytkownika) | %localappdata%\amlworkbench | 
| Różne narzędzia |c:\dsvm\tools |

> [!NOTE]
> W wersji systemu Windows Server 2012 wersji maszyny wirtualnej DSVM i Windows Server 2016 przed marca 2018 r. domyślne środowisko Anaconda jest język Python 2.7. Pomocniczy środowisko jest język Python 3.5, znajdujący się w c:\Anaconda\envs\py35. 
> 
> 

## <a name="next-steps"></a>Następne kroki
Poniżej przedstawiono niektóre następnej czynności, aby kontynuować Twoją naukę oraz eksploracji. 

* Eksplorować różnych narzędzi nauki o danych maszyny Wirtualnej do analizy danych, klikając start menu i wyewidencjonowywanie wymienionych w menu narzędzi.
* Informacje na temat usług Azure Machine Learning i aplikacji Workbench, odwiedzając produktu [strony Szybki Start i samouczków](../service/index.yml). 
* Przejdź do **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** przykładów przy użyciu biblioteki kolekcję funkcji RevoScaleR w języku R, który obsługuje analizy danych w skali przedsiębiorstwa.  
* Przeczytaj artykuł: [10 rzeczy, które można wykonać na maszynie wirtualnej analizy danych](http://aka.ms/dsvmtenthings)
* Dowiedz się, jak tworzyć kompleksowe rozwiązania analityczne systematycznie przy użyciu [zespołu danych dla celów naukowych](../team-data-science-process/index.yml).
* Odwiedź stronę [galerii Azure AI](http://gallery.cortanaintelligence.com) nauki i dane analizy przykładowych maszyny korzystające z usługi Azure Machine learning i powiązane dane usług na platformie Azure. Udostępniliśmy również ikony na **Start** menu i na pulpicie maszyny wirtualnej do tej galerii.

