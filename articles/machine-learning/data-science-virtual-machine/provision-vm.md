---
title: Aprowizowanie maszyny wirtualnej nauki danych systemu Windows, na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie i Utwórz maszynę wirtualną nauki danych na platformie Azure w celu wykonania analizy i uczenia maszynowego.
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
ms.openlocfilehash: 445b18dee9efa9561ba1274ef59a9a426332d745
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Aprowizowanie maszyny wirtualnej nauki danych systemu Windows, na platformie Azure
Maszyna wirtualna nauki danych firmy Microsoft znajduje się obraz maszyny wirtualnej (VM) systemu Windows Azure wstępnie zainstalowany i skonfigurowany z kilku popularnych narzędzi, które są często używane do analizy danych i uczenia maszynowego. Narzędzia uwzględnione są:

* [Uczenie maszynowe Azure](../service/index.yml) Workbench
* [Microsoft Machine Learning serwera](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Dystrybucję oprogramowania Python anaconda
* Notesu Jupyter (z R, Python, jądra PySpark)
* Visual Studio Community Edition
* Program Power BI Desktop
* SQL Server 2017 Developer Edition
* Wystąpienia autonomicznego Spark dla lokalnych projektowania i testowania
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Uczenie maszynowe i narzędzia do analizy danych
  * Głębokie uczenia struktury: bogaty zestaw struktur AI tym [kognitywnych zestaw narzędzi firmy Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [moduł łańcucha](https://chainer.org/), mxNet, Keras znajdują się na maszynie Wirtualnej.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): szybki komputer uczenia systemu obsługującego technik, takich jak online, wyznaczania wartości skrótu, allreduce, redukcji, learning2search, aktywna i uczenie się interakcyjne.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): narzędzie, zapewniając szybkie i dokładne boosted drzewa wykonania.
  * [Rattle](http://rattle.togaware.com/) (R analitycznych narzędzia do Dowiedz się łatwo): narzędzie, które umożliwia wprowadzenie do analizy danych i machine learning w łatwy R. Obejmuje on Eksploracja danych z Graficznym interfejsem użytkownika i modelowanie z automatycznego generowania kodu języka R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : wyszukiwania danych wizualnych i oprogramowania w języku Java uczenia maszynowego.
  * [Przechodzenie do szczegółów Apache](https://drill.apache.org/): aparatu zapytań SQL bez schematu dla platformy Hadoop, NoSQL i magazynu w chmurze.  Obsługuje interfejsy ODBC i JDBC, aby umożliwić wykonywanie zapytania NoSQL i pliki ze standardowych narzędzi do analizy Biznesowej, takich jak usługi Power BI, Excel, Tableau.
* Biblioteki języka R i Python dla programu uczenie maszynowe Azure i innych usług Azure
* Git tym Git Bash do pracy z tym GitHub, Visual Studio Team Services repozytoriach kodów źródłowych i udostępnia kilka popularnych Linux narzędzia wiersza polecenia (w tym awk mniejszyć, perl, grep, Znajdź, wget, curl, itp.) dostępny zarówno z git bash i poleceń wiersz. 

Podczas analizy danych obejmuje iteracja w sekwencji zadań:

1. Znajdowanie, ładowania i wstępne przetworzenie danych
2. Tworzenie i testowanie modeli
3. Wdrażanie modeli do wykorzystania w aplikacji inteligentnego

Analityków danych użycia różnych narzędzi do wykonania tych zadań. Może być bardzo czasochłonne Aby znaleźć odpowiednie wersje oprogramowania, a następnie Pobierz i zainstaluj je. Maszyny wirtualnej nauki danych firmy Microsoft może ułatwić to obciążenie poprzez dostarczanie obrazu gotowe do użycia, które można udostępnić na platformie Azure wszystkich kilka narzędzi popularnych wstępnie zainstalowane i skonfigurowane. 

Maszyna wirtualna nauki danych Microsoft jump-starts projektu analizy. Pozwala na pracę nad zadaniami w różnych językach, w tym R, Python, SQL i C#. Program Visual Studio udostępnia IDE umożliwiające opracowanie i przetestowanie swój kod, który jest łatwy w użyciu. Zestaw SDK usługi Azure uwzględnione w maszynie Wirtualnej umożliwia tworzenie aplikacji przy użyciu różnych usług na platformy w chmurze firmy Microsoft. 

Nie ma żadnych opłat oprogramowania dla tego obrazu maszyny Wirtualnej do analizy danych. Płacisz tylko za opłaty za użycie usługi Azure które w zależności od rozmiaru maszyny wirtualnej, którą można udostępnić. Więcej informacji na temat opłat obliczeniowe można znaleźć w sekcji szczegółów cennik na [maszyny wirtualnej nauki danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) strony. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Inne wersje danych maszyny wirtualnej nauki
[Ubuntu](dsvm-ubuntu-intro.md) obraz jest dostępny, jak również w przypadku wielu narzędzi podobne oraz kilka dodatkowych bezpośrednich uczenia struktury. A [CentOS](linux-dsvm-intro.md) obrazu jest również dostępna. Oferujemy również [wersji systemu Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) danych maszyny wirtualnej nauki jednak kilka narzędzia są dostępne tylko w wersji systemu Windows Server 2016.  W przeciwnym razie ten artykuł dotyczy również do wersji Windows Server 2012.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem maszyny wirtualnej nauki danych firmy Microsoft, należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**: uzyskanie takiego, zobacz [Azure Pobierz bezpłatną wersję próbną](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Tworzenie maszyny wirtualnej nauki danych firmy Microsoft
Aby utworzyć wystąpienie programu Microsoft Data nauki maszyny wirtualnej, wykonaj następujące kroki:

1. Przejdź do wyświetlania na maszynie wirtualnej [portalu Azure](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Wybierz **Utwórz** znajdujący się u dołu do kreatora.![ Konfigurowanie danych nauki vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. Kreator używany do tworzenia maszyny wirtualnej nauki danych firmy Microsoft wymaga **dane wejściowe** dla każdego z **cztery kroki** wyliczyć po prawej stronie tej liczby. Poniżej przedstawiono wejść potrzebne do skonfigurowania każdego z następujących czynności:
   
   1. **Podstawy**
      
      1. **Nazwa**: Nazwa serwera nauki danych tworzona.
      2. **Typ dysku maszyny Wirtualnej**: wybór między dysków SSD i HDD. Procesor GPU NC_v1 wystąpienia (na podstawie K80 tesla — NVidia), wybierz **HDD** jako typ dysku. 
      3. **Nazwa użytkownika**: identyfikator logowania konta administratora.
      4. **Hasło**: hasło konta administratora.
      5. **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz jedną, na którym maszyna ma być utworzony i są rozliczane.
      6. **Grupy zasobów**: można utworzyć nowej lub istniejącej grupy.
      7. **Lokalizacja**: wybierz centrum danych, która jest najbardziej odpowiednia. Zazwyczaj jest centrum danych, która zawiera większość danych lub zbliżony do fizycznej lokalizacji najszybszy dostęp do sieci.
   2. **Rozmiar**: Wybierz jeden z typów serwerów, które spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Więcej opcji dotyczących rozmiarów maszyn wirtualnych można uzyskać, wybierając polecenie "Wyświetl wszystko".
   3. **Ustawienia**:
      
      1. **Dysków zarządzanych**: Wybierz zarządzanych, jeśli chcesz Azure do zarządzania dyskami maszyny wirtualnej.  W przeciwnym razie należy określić konto magazynu do nowego lub istniejącego. 
      2. **Inne parametry**: zwykle po prostu użyć wartości domyślnych. Jeśli chcesz wziąć pod uwagę przy użyciu wartości innych niż domyślne, umieść kursor nad informacyjną łącze, aby uzyskać pomoc dotyczącą określonych pól.
    a. **Podsumowanie**: Sprawdź, czy wszystkie informacje wprowadzone jest poprawna, a następnie kliknij przycisk **Utwórz**. **Uwaga**: maszyny Wirtualnej nie ma żadnych dodatkowych kosztów poza obliczeniowe dla rozmiaru serwera w **rozmiar** kroku. 

> [!NOTE]
> Udostępnianie powinno zająć około 10-20 minut. Stan inicjowania obsługi administracyjnej jest wyświetlana w portalu Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Jak dostęp do maszyny wirtualnej nauki danych firmy Microsoft
Po utworzeniu maszyny Wirtualnej można pulpitu zdalnego do niego przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. 

Gdy maszyna wirtualna jest utworzone i udostępnione, można przystąpić do uruchomienia przy użyciu narzędzia, które są zainstalowane i skonfigurowane na nim. Brak Kafelki menu start i ikony pulpitu dla wielu narzędzi. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Narzędzi zainstalowanych w Microsoft danych nauki maszyny wirtualnej



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Jeśli chcesz korzystać z Microsoft enterprise bibliotek skalowalne R lub Python do analizy sieci, czy maszyna wirtualna ma Microsoft ML Server Developer (wcześniej znane jako Microsoft R Server) zainstalowanej wersji. Microsoft ML Server jest platformie analizy szeroko można wdrożyć klasy korporacyjnej dostępny na R i Python i skalowalne, komercyjnie obsługiwane i bezpieczna. Obsługa różnych statystyk danych big data, modelowania predykcyjnego i możliwości uczenia maszynowego, ML serwer obsługuje pełną gamę analytics — eksploracji, analizy wizualizacji i modelowania. Dzięki użyciu i rozszerzanie typu open source R i Python, Microsoft ML Server jest w pełni zgodny z R / skrypty języka Python, funkcje i sieci CRAN / narzędzia pip / skalować Conda pakietów, do analizowania danych w przedsiębiorstwie. Dotyczy również ograniczenia w pamięci Otwórz R źródło przez dodanie przetwarzanie równoległe i podzielony danych. Umożliwia to uruchamianie analizy danych niż co mieści się w pamięci głównej.  Visual Studio Community Edition uwzględnione na maszynie Wirtualnej zawiera narzędzia R do narzędzi Visual Studio i języka Python dla rozszerzenia Visual Studio, który zapewnia pełne IDE do pracy z R lub Python. Firma Microsoft udostępnia również inne IDEs także takie jak [programu RStudio](http://www.rstudio.com) i [PyCharm Community edition](https://www.jetbrains.com/pycharm/) na maszynie Wirtualnej. 

### <a name="python"></a>Python
Do tworzenia aplikacji przy użyciu języka Python dystrybucja Anaconda Python 2.7 i 3,6 została zainstalowana. Rozkład ten zawiera podstawowe języka Python oraz około 300 najbardziej popularnych pakietów analytics matematyczne, inżynieria i danych. Można użyć narzędzia Python Tools dla programu Visual Studio (PTVS) zainstalowanej w wersji Visual Studio 2017 Community lub jednego z IDEs powiązane z Anaconda bezczynności lub Spyder. Można uruchomić te według wyszukiwania na pasku wyszukiwania (**Win** + **S** klucza).

> [!NOTE]
> Aby punkt narzędzi Python Tools for Visual Studio na Anaconda Python 2.7, należy utworzyć niestandardowego środowiska dla każdej wersji. Aby ustawić te ścieżki środowiska Visual Studio 2017 Community Edition, przejdź do **narzędzia** -> **narzędzi Python Tools** -> **środowiska Python**, a następnie kliknij przycisk **+ niestandardowe**. 
> 
> 

Python anaconda 3,6 jest instalowane w obszarze C:\Anaconda oraz Anaconda Python 2.7 w obszarze c:\Anaconda\envs\python2. Zobacz [dokumentacji PTVS](/visualstudio/python/python-environments.md#selecting-and-installing-python-interpreters) szczegółowy opis kroków. 

### <a name="jupyter-notebook"></a>Jupyter Notebook
Dystrybucji anaconda zawiera również notesu Jupyter, środowisko, aby udostępnić kodu i analizy. Serwer notesu Jupyter został wstępnie skonfigurowany z użyciem języka Python 2.7, Python 3.x, jądra PySpark, Julia i R. Brak ikony pulpitu o nazwie "Notesu Jupyter", aby uruchomić serwera Jupyter i uruchomienie przeglądarki do uzyskiwania dostępu do serwera notesu. 

Firma Microsoft ma spakowane kilka przykładowych notesów w języku Python i w języku R. Z notesów Jupyter pokazują, jak pracować z Microsoft ML Server, SQL Server ML Services (analityka w bazie danych), Python, kognitywnych zestaw narzędzi firmy Microsoft, Tensorflow i innych technologii Azure po otwarciu Jupyter. Na stronie głównej notesu można zobaczyć łącza do próbek, po uwierzytelniania notesu Jupyter przy użyciu hasła utworzonego w poprzednim kroku. 

### <a name="visual-studio-2017-community-edition"></a>Program Visual Studio 2017 Community edition
Program Visual Studio Community edition zainstalowany na maszynie Wirtualnej. Jest to bezpłatna wersja popularnych IDE firmy Microsoft, który służy do celów ewaluacyjnych i dla małych zespołów. Postanowienia licencyjne można wyewidencjonować [tutaj](https://www.visualstudio.com/support/legal/mt171547).  Otwórz program Visual Studio przez dwukrotne kliknięcie ikony pulpitu lub **Start** menu. Możesz również wyszukać programy z **Win** + **S** i wprowadzając "Visual Studio". Gdy można tworzyć projektów w językach C#, Python, R, transport, node.js. Wtyczki instalowane są również wchodzące wygodny do pracy z usługami Azure, takich jak Azure Data Catalog, Azure HDInsight (Hadoop, Spark) i usługi Azure Data Lake. Obecnie jest również dodatku o nazwie ```Visual Studio Tools for AI``` bezproblemowo integruje się do usługi Azure Machine Learning i pomaga szybko aplikacji AI kompilacji. 

> [!NOTE]
> Może wystąpić komunikat informujący, że okres próbny minął. Wprowadź poświadczenia konta Microsoft lub Utwórz nowe konto wolnego uzyskać dostęp do programu Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Deweloperska wersja programu SQL Server 2017 z usługi uczenie Maszynowe, aby uruchomić analityka w bazie danych znajduje się na Maszynie wirtualnej w R lub Python. Usługi ML udostępniają platformę do opracowywania i wdrażania aplikacji inteligentnego. Służy bogaty i wydajne tych języków i wiele pakietów przez społeczność do tworzenia modeli i generowania prognoz dla danych programu SQL Server. Umożliwia zachowanie analytics bliski danych, ponieważ usługi uczenia Maszynowego (w database) integruje języka R i Python w programie SQL Server. Eliminuje koszty i zagrożenia bezpieczeństwa związane z przepływu danych.

> [!NOTE]
> Wydanie programu SQL Server developer służy tylko do prac deweloperskich i celów testowych. Potrzebna licencja go uruchomić w środowisku produkcyjnym. 
> 
> 

Dostęp do programu SQL server, uruchamiając **programu SQL Server Management Studio**. Nazwę maszyny Wirtualnej jest wprowadzana jako nazwy serwera. Uwierzytelnianie systemu Windows po zalogowaniu się jako administrator w systemie Windows. Po przejściu do programu SQL Server Management Studio można utworzyć innych użytkowników, tworzenie baz danych, importowanie danych i uruchamiać zapytania SQL. 

Aby włączyć analytics w bazie danych przy użyciu usługi uczenia Maszynowego SQL, uruchom następujące polecenie jako czas działania w programie SQL Server management studio po zalogowaniu się jako administrator serwera. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Kilku narzędzi platformy Azure są zainstalowane na maszynie Wirtualnej:

* Brak skrót na pulpicie, aby uzyskać dostęp do dokumentacji zestawu SDK platformy Azure. 
* **Narzędzie AzCopy**: używane do przenoszenia danych do i z konta magazynu Microsoft Azure. Aby wyświetlić obciążenie, wpisz **Azcopy** w wierszu polecenia, aby zobaczyć użycie. 
* **Eksplorator magazynu Microsoft Azure**: używane do przeglądania obiektów, które są przechowywane w ramach konta magazynu Azure i transferu danych do i z magazynu Azure. Możesz wpisać **Eksploratora usługi Storage** w wyszukiwania lub znajdź go w menu Start systemu Windows można uzyskać dostępu do tego narzędzia. 
* **Adlcopy**: używane do przenoszenia danych do usługi Azure Data Lake. Aby wyświetlić obciążenie, wpisz **adlcopy** w wierszu polecenia. 
* **dtui**: umożliwia przenoszenie danych do i z bazy danych z rozwiązania Cosmos platformy Azure, bazę danych NoSQL w chmurze. Typ **dtui** w wierszu polecenia. 
* **Azure środowiska uruchomieniowego integracji fabryki danych**: umożliwia przenoszenie danych między źródłami danych lokalnych i w chmurze. Jest on używany w ramach narzędzi, takich jak fabryki danych Azure. 
* **Microsoft Azure Powershell**: narzędzie do administrowania zasobami Azure w programie Powershell, językiem skryptowym również jest zainstalowany na maszynie Wirtualnej. 

### <a name="power-bi"></a>Power BI
Do tworzenia pulpitów nawigacyjnych i wspaniałych wizualizacji **Power BI Desktop** został zainstalowany. Użyj tego narzędzia do pobierania danych z różnych źródeł, tworzyć pulpity i raporty i opublikuj je w chmurze. Aby uzyskać informacje, zobacz [usługi Power BI](http://powerbi.microsoft.com) lokacji. Program Power BI desktop można znaleźć w Start menu. 

> [!NOTE]
> Musisz mieć konto usługi Office 365, można uzyskać dostępu do usługi Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Środowisko robocze usługi Azure Machine Learning

Azure Machine Learning Workbench jest aplikacją i interfejsu wiersza polecenia. Workbench ma przygotowanie wbudowanych danych, która uzyskuje informacje o etapów przygotowywania danych je wykonywać. Umożliwia także zarządzanie projektami, uruchom historii i integracji notesu możliwość niemal wydajność pracy. Użytkownik może skorzystać z najlepszych struktur open source, w tym TensorFlow, kognitywnych Toolkit Spark ML i scikit — Dowiedz się, jak tworzenie modeli. Na DSVM udostępniamy ikony pulpitu, aby zainstalować workbench uczenie maszynowe Azure w katalogu % LOCALAPPDATA % dla poszczególnych użytkowników. Każdy użytkownik, który musi używać Workbench musi jedynie na czas działania dwukrotnie ```AzureML Workbench Setup``` ikony pulpitu, aby zainstalować ich wystąpienia środowiska roboczego. Usługa Azure Machine Learning również tworzone i używane środowisko Python dla poszczególnych użytkowników, które są wyodrębniane w % LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Dodatkowe narzędzi programistycznych firmy Microsoft
[ **Instalatora platformy sieci Web firmy Microsoft** ](https://www.microsoft.com/web/downloads/platform.aspx) może służyć do odnajdywania i Pobierz innych narzędzi programistycznych firmy Microsoft. Istnieje również skrót do narzędzia na pulpicie maszyny wirtualnej nauki danych firmy Microsoft.  

## <a name="important-directories-on-the-vm"></a>Ważne katalogi na maszynie Wirtualnej
| Element | Katalog |
| --- | --- |
| Konfiguracje serwera notesu Jupyter |C:\ProgramData\jupyter |
| Katalog macierzysty przykłady notesu Jupyter |c:\dsvm\notebooks i c:\users\<nazwa użytkownika > \notebooks|
| Inne przykłady |c:\dsvm\samples |
| Anaconda (domyślne: Python 3,6) |c:\Anaconda |
| Środowisko Python 2.7 anaconda |c:\Anaconda\envs\python2 |
| Microsoft ML Server autonomiczny Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Wystąpienie domyślne R (ML serwer autonomiczny) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| Katalog wystąpień w bazie danych SQL usługi uczenia Maszynowego |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (dla poszczególnych użytkowników) | %localappdata%\amlworkbench | 
| Różne narzędzia |c:\dsvm\tools |

> [!NOTE]
> W wersji Windows Server 2012 przed 2018 marca wersji DSVM i Windows Server 2016 domyślnym środowisku Anaconda jest Python 2.7. Środowisko dodatkowej jest znajdującym się w c:\Anaconda\envs\py35 3.5 Python. 
> 
> 

## <a name="next-steps"></a>Następne kroki
Poniżej przedstawiono niektóre kolejne kroki, aby kontynuować learning eksploracji. 

* Poznaj różnych narzędzi analizy danych na nauki danych maszyny Wirtualnej, klikając start menu i wyewidencjonowywanie wymienionych w menu Narzędzia.
* Dowiedz się więcej o Azure Machine Learning usług i Workbench, przechodząc na stronę produktu [strony Szybki Start i samouczki](../service/index.yml). 
* Przejdź do **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** dla przykładów przy użyciu biblioteki RevoScaleR w R obsługującego analizy danych w skali przedsiębiorstwa.  
* Przeczytaj artykuł: [10 sposobów na nauki danych maszyny wirtualnej](http://aka.ms/dsvmtenthings)
* Dowiedz się, jak tworzyć kompleksowe rozwiązania analityczne systematycznie przy użyciu [proces nauki danych zespołu](../team-data-science-process/index.yml).
* Odwiedź stronę [galerii Azure AI](http://gallery.cortanaintelligence.com) maszyny uczenie i danych analytics przykłady korzystających z usługi Azure Machine learning oraz dane dotyczące usług na platformie Azure. Ikona zostały również zamieszczone na **Start** menu i na pulpicie maszyny wirtualnej do tej galerii.

