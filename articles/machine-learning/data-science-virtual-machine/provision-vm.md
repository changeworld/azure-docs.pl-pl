---
title: 'Szybki start: Utwórz system Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurowanie i tworzenie maszyny wirtualnej do nauki o danych na platformie Azure, analizy i uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: 6c0abc20775e604f9ea1c6ba882e5a9ff1d89e90
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195293"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Szybki start: Konfigurowanie Data Science Virtual Machine systemu Windows na platformie Azure

Microsoft Windows Data Science Virtual Machine (DSVM) to obraz maszyny wirtualnej z systemem Windows Server 2016 (VM) na platformie Azure. Jest ona wstępnie zainstalowana i skonfigurowana za pomocą narzędzi do analizy danych i uczenia maszynowego.

## <a name="included-data-science-tools"></a>Dołączone narzędzia do nauki o danych

W DSVM znajdują się następujące narzędzia:

* Zestaw SDK języka Python dla [usługi Azure Machine Learning](../service/index.yml).
* [Serwer Microsoft Machine Learning](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Anaconda Python dystrybucji.
* Notes Jupyter przy użyciu języka R i Python oraz PySpark jądra.
* Microsoft Visual Studio Community.
* Program Microsoft Power BI Desktop.
* Program Microsoft SQL Server 2017 Developer edition.
* Autonomiczne wystąpienie platformy Apache Spark do lokalnego tworzenia i testowania.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning i data analitycznymi:
  * Platformy uczenia głębokiego: Platformy [TensorFlow](https://www.tensorflow.org/), [łańcucher](https://chainer.org/), MXNet i Keras AI są dołączone do maszyny wirtualnej.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Szybkie uczenia maszynowego, system, który obsługuje technik, takich jak online wyznaczania wartości skrótu, allreduce, redukcji, learning2search i aktywne i interaktywne uczenie się.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Narzędzie, które umożliwia szybkie i dokładne wzmocnione drzewo implementację.
  * [Rattle](https://togaware.com/rattle/). Narzędzie analityczne języka R, które pozwala rozpocząć pracę z analizami danych i uczeniem maszynowym w języku R. Obejmuje eksplorowanie i modelowanie danych w graficznym interfejsie użytkownika przy użyciu automatycznego generowania kodu języka R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/): Wyszukiwanie danych wizualnych i usługi machine learning oprogramowania w języku Java.
  * Przechodzenie do oprogramowania [Apache](https://drill.apache.org/): Bez schematu aparatu zapytań SQL dla usługi Apache Hadoop, NoSQL i Magazyn w chmurze. Obsługuje ona ODBC i sterownika JDBC interfejsy do wykonywania zapytań, NoSQL i pliki ze standardowych narzędzi analizy Biznesowej, takich jak Power BI, programie Microsoft Excel i Tableau.
* Użyj bibliotek w języków R i Python, aby uzyskać w usłudze Azure Machine Learning i innymi usługami platformy Azure.
* Git, w tym git bash, do pracy z repozytoriami kodu źródłowego, takimi jak GitHub i Azure DevOps. System git udostępnia kilka popularnych narzędzi wiersza polecenia systemu Linux, które są dostępne zarówno w systemie Git bash, jak i w wierszu polecenia. Przykładami są awk, sed, perl, grep, wyszukiwanie, wget i narzędzie curl.
* Narzędzia i edytory programistyczne (RStudio, platformy PyCharm itd).

### <a name="about-data-science"></a>Informacje o nauce danych

Do nauki o danych obejmuje iteracja w sekwencji zadań:

1. Znajdź, ładowanie i przetwarzanie wstępne danych.
1. Tworzenie i testowanie modeli.
1. Wdrażaj modele do użycia w inteligentnych aplikacjach.

Analitycy danych używać kilku narzędzi do wykonywania tych zadań. Może zająć dużo czasu można znaleźć odpowiednie wersje oprogramowania, a następnie Pobierz i zainstaluj je. DSVM oszczędza czas, dostarczając gotowy do użycia obraz, który może być obsługiwany na platformie Azure z kilkoma popularnymi narzędziami wstępnie zainstalowanymi i skonfigurowanymi.

DSVM skacze — uruchamia projekt analizy. Możesz korzystać z zadań w różnych językach, w tym w języku R, Python, SQL C#i. Visual Studio zapewnia łatwe w użyciu zintegrowanego rozwoju środowiska (IDE) do tworzenia i testowania kodu. Zestaw Azure SDK jest dołączony do maszyny wirtualnej, dzięki czemu można kompilować aplikacje za pomocą usług na platformie Microsoft Cloud.

Dla tego obrazu DSVM nie są naliczane opłaty za oprogramowanie. Opłaty są naliczane tylko opłaty za użycie platformy Azure. Zależą od rozmiaru maszyny wirtualnej, która została zainicjowana. Aby uzyskać więcej informacji, zobacz [stronę Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Inne wersje DSVM

* [Ubuntu](dsvm-ubuntu-intro.md) obrazu. Ma narzędzia podobne do DSVM oraz kilka platform uczenia głębokiego.
* A [Linux CentOS](linux-dsvm-intro.md) obrazu.
* [Wersji systemu Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) maszyny wirtualnej do nauki o danych. Niektóre narzędzia są dostępne tylko w wersji systemu Windows Server 2016. W przeciwnym razie ten artykuł dotyczy również do wersji Windows Server 2012.

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć maszynę wirtualną do nauki o danych firmy Microsoft, musi mieć subskrypcję platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Tworzenie DSVM

Aby utworzyć wystąpienie DSVM:

1. Przejdź do listy maszyn wirtualnych na [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Jeśli użytkownik nie jest jeszcze zalogowany, może zostać wyświetlony monit o zalogowanie się do konta platformy Azure.
1. Wybierz przycisk **Utwórz** u dołu.

   ![Lista maszyn wirtualnych na Azure Portal za pomocą przycisku Utwórz](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Wprowadź poniższe informacje, aby skonfigurować poszczególne kroki przedstawione w prawym okienku zrzutu ekranu:

   1. **Podstawy**:
      * **Nazwa**: Wprowadź nazwę DSVM.
      * **Typ dysku maszyny wirtualnej**: Wybierz **dysk SSD** lub **dysk twardy**. W przypadku wystąpienia procesora GPU NC_v1, takiego jak NVIDIA Tesla K80, wybierz dysk **twardy** jako typ dysku.
      * **Nazwa użytkownika**: Wprowadź identyfikator konta administratora.
      * **Hasło**: Wprowadź hasło konta administratora.
      * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona.
      * **Grupa zasobów**: Utwórz nową grupę lub Użyj istniejącej.
      * **Lokalizacja**: Wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji.
   1. **Rozmiar**: Wybierz typ serwera, który spełnia wymagania funkcjonalne i ograniczenia dotyczące kosztów. Aby wyświetlić więcej rozmiarów maszyn wirtualnych, wybierz **Wyświetl wszystko**.
   1. **Ustawienia**:  
      * **Użyj usługi Managed Disks**. Wybierz **zarządzane** chcącym platformy Azure do zarządzania dyskami maszyny wirtualnej. W przeciwnym razie należy określić konto magazynu w nowym lub istniejącym.  
      * **Inne parametry**. Można użyć wartości domyślnych. Jeśli chcesz użyć wartości innych niż domyślne, umieść wskaźnik myszy na linku informacyjnym, aby uzyskać pomoc dotyczącą określonych pól.
   1. **Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. Wybierz pozycję **Utwórz**.

> [!NOTE]
> * Na maszynie wirtualnej nie są naliczane żadne dodatkowe opłaty wykraczające poza koszt obliczeń dla rozmiaru serwera wybranego w kroku **rozmiar** .
> * Inicjowanie obsługi trwa od 10 do 20 minut. Stan maszyny wirtualnej można wyświetlić na Azure Portal.

## <a name="access-the-dsvm"></a>Dostęp do DSVM

Po utworzeniu i udostępnieniu maszyny wirtualnej można uzyskać do niej dostęp za pomocą podłączania pulpitu zdalnego. Użyj poświadczeń konta administratora, które zostały skonfigurowane w kroku **podstawowe informacje** o tworzeniu maszyny wirtualnej. 

Możesz rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Do wielu narzędzi można uzyskać dostęp za pomocą kafelków menu **Start** i ikon pulpitu.

Możesz również dołączyć DSVM do Azure Notebooks uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy Bezpłatna usługa. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów notesów](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Narzędzia zainstalowane w DVSM

W poniższych sekcjach dowiesz się więcej o narzędziach, które są zainstalowane na Data Science Virtual Machine.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Możesz użyć biblioteki Microsoft Enterprise dla analizy, ponieważ Machine Learning Server Developer Edition jest zainstalowana na maszynie wirtualnej. Wcześniej znane jako Microsoft R Server, Machine Learning Server to platforma analityczna, którą można wdrożyć. Jest ona skalowalna i komercyjnie obsługiwana.

Serwer usługi Machine Learning obsługuje różne statystyki danych big data, modelowanie predykcyjne i zadania uczenia maszynowego. Obsługuje ona pełnego zakresu analytics: eksplorację, analizę, wizualizacji i modelowania. Dzięki użyciu i rozszerzenie typu open source R i Python, serwer Machine Learning jest zgodny z języków R i Python, skryptach i funkcjach. Jest również zgodna z sieci CRAN, pip i pakietów Conda, do analizowania danych w skali przedsiębiorstwa.

Serwer Machine Learning adresy ograniczeniami pamięci języka r typu open source, dodając równoległe i fragmentaryczne przetwarzania danych. Oznacza to, że można uruchamiać analizy na znacznie większej ilości danych niż w przypadku pamięci głównej. 

Program Visual Studio Community znajduje się na maszynie Wirtualnej. Zawiera rozszerzenia R Tools for Visual Studio i Python Tools for Visual Studio (PTVS), które zapewniają pełny IDE do pracy z językiem R lub Python. Firma Microsoft udostępnia również inne środowisk IDE, takie jak [RStudio](https://www.rstudio.com) i [platformy PyCharm itd Community Edition](https://www.jetbrains.com/pycharm/) na maszynie wirtualnej.

### <a name="python"></a>Python

W przypadku programowania przy użyciu języka Python są zainstalowane Anacondae dystrybucje języka Python 2,7 i 3,6. Tych dystrybucji mają podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy. Możesz użyć PTVS, który jest zainstalowany w programie Visual Studio Community 2017. Można też użyć jednej z środowisk IDE z pakietem Anaconda, takich jak bezczynne lub Spyder. Wyszukaj i otwórz jeden z tych pakietów (klucze logo systemu Windows + S).

> [!NOTE]
> Aby wskazać narzędzi Python Tools for Visual Studio w środowisko Anaconda Python 2.7, musisz utworzyć niestandardowego środowiska dla każdej wersji. Aby ustawić te ścieżki środowiskowe w programie Visual Studio 2017 Community, przejdź do pozycji **Narzędzia** > **Python Tools** > **Python**Environments. Następnie wybierz pozycję **+ niestandardowe**.

Anaconda Python 3,6 jest zainstalowana w C:\Anaconda. Anaconda Python 2,7 jest zainstalowana w C:\Anaconda\envs\python2. Szczegółowe instrukcje znajdują się w [dokumentacji PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Notes Jupyter

Dystrybucja Anaconda zawiera również Jupyter Notebook środowisko do udostępniania kodu i analizy. Serwer notesu programu Jupyter jest wstępnie skonfigurowana za pomocą języka Python 2.7 Python 3.x, PySpark, Julia i R jądra. Aby uruchomić serwer Jupyter i otworzyć przeglądarkę w celu uzyskania dostępu do serwera notesu, użyj ikony **Jupyter Notebook** Desktop.

Firma Microsoft spakować kilka notesów przykładowy w języku Python i R. Po uzyskaniu dostępu programu Jupyter, notesów pokazują, jak pracować z następujących technologii:

* Machine Learning Server
* SQL Server Machine Learning Services, analiza w bazie danych
* Python
* Zestaw narzędzi usług Microsoft Cognitive
* TensorFlow
* Inne technologie platformy Azure

Link do przykładów można znaleźć na stronie głównej notesu po uwierzytelnieniu do Jupyter Notebook przy użyciu utworzonego wcześniej hasła.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM obejmuje program Visual Studio Community. Jest to bezpłatna wersja popularnego środowiska IDE od firmy Microsoft, której można użyć do celów ewaluacyjnych i małych zespołów. Zapoznaj się z postanowieniami licencyjnymi dotyczącymi [oprogramowania firmy Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Otwórz program Visual Studio, korzystając z ikony pulpitu lub menu **Start** . Wyszukaj programy (logo systemu Windows + S), a następnie **program Visual Studio**. W tym miejscu możesz tworzyć projekty w językach takich jak C#, Python, R i Node. js. Zainstalowane dodatki plug-in należy wygodne do pracy z następującymi usługami platformy Azure:

* Azure Data Catalog
* Usługa Azure HDInsight dla usług Hadoop i Spark
* Azure Data Lake

Wtyczka o nazwie Azure Machine Learning dla Visual Studio Code integruje się również z Azure Machine Learning i pomaga szybko tworzyć aplikacje AI.

> [!NOTE]
> Możesz otrzymać komunikat, okres próbny wygasł. Wprowadź poświadczenia konta Microsoft. Lub Utwórz nowe bezpłatne konto, aby uzyskać dostęp do programu Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM jest dołączona do wersji Developer SQL Server 2017 z Machine Learning Services. Ta wersja SQL Server jest dostępna w języku R lub Python i może być uruchamiana w ramach analizy bazy danych. 

Usługi Machine Learning zapewnia platformę do tworzenia i wdrażania inteligentnych aplikacji. Języki te i wiele pakietów przez społeczność służy do tworzenia modeli i generować prognozy dla danych programu SQL Server. Umożliwia zachowanie analytics blisko danych, ponieważ usługi Machine Learning, w bazie danych, integruje się języków R i Python w programie SQL Server. Ta integracja pozwala wyeliminować koszty i zagrożenia bezpieczeństwa związane z przenoszeniem danych.

> [!NOTE]
> Wersja programu SQL Server Developer jest tylko do celów tworzenia i testowania. Niezbędna jest licencja go uruchomić w środowisku produkcyjnym.

Dostęp do SQL Server można uzyskać, otwierając Microsoft SQL Server Management Studio. Nazwa maszyny wirtualnej jest wypełniona jako **Nazwa serwera**. Uwierzytelnianie Windows po zalogowaniu się jako administrator na Windows. Po otwarciu programu SQL Server Management Studio można tworzyć innych użytkowników, tworzenie baz danych, importowanie danych i uruchamiać zapytania SQL.

Aby włączyć funkcję analizy w bazie danych przy użyciu Machine Learning Services SQL Server, uruchom następujące polecenie jako akcję jednorazową w SQL Server Management Studio po zalogowaniu się jako administrator serwera:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Zamień `%COMPUTERNAME%` na nazwę maszyny wirtualnej.

### <a name="azure"></a>Azure

Kilku narzędzi platformy Azure są zainstalowane na maszynie Wirtualnej:

* Skrót na pulpicie przechodzi do dokumentacji zestawu SDK platformy Azure.
* Użyj AzCopy, aby skopiować dane z konta usługi Azure Storage i z niego. Aby zobaczyć sposób użycia, wprowadź **Azcopy** polecenie w wierszu polecenia.
* Użyj Eksplorator usługi Azure Storage, aby przeglądać obiekty przechowywane na koncie usługi Azure Storage. Kopiuje również dane do i z usługi Azure Storage. Aby uzyskać dostęp do tego narzędzia, wprowadź **Eksplorator usługi Storage** w polu **wyszukiwania** . Lub znaleźć go na Windows **Start** menu.
* AdlCopy kopiuje dane do Azure Data Lake. Aby zobaczyć sposób użycia, wprowadź **adlcopy** w wierszu polecenia.
* Narzędzie dtui kopiuje dane do i z Azure Cosmos DB, NoSQL Database w chmurze. Wprowadź **dtui** w wierszu polecenia.
* Środowisko Integration Runtime kopiuje dane między lokalnymi źródłami danych a chmurą. Jest używany w ramach narzędzi, takich jak usługi Azure Data Factory.
* Użyj Azure PowerShell do administrowania zasobami platformy Azure w języku skryptów programu PowerShell. Jest również instalowany na maszynie Wirtualnej.

### <a name="power-bi"></a>Power BI

DSVM zawiera Power BI Desktop zainstalowane, które ułatwiają tworzenie pulpitów nawigacyjnych i wizualizacji. To narzędzie do pobierania danych z różnych źródeł, tworzyć pulpity nawigacyjne i raporty i opublikuj je w chmurze. Aby uzyskać więcej informacji, zobacz [witrynę Power BI](https://powerbi.microsoft.com). Power BI Desktop można znaleźć w menu **Start** .

> [!NOTE]
> Wymagane jest konto Microsoft Office 365, aby dostęp do usługi Power BI.

### <a name="azure-machine-learning-sdk-for-python"></a>Zestaw SDK Azure Machine Learning dla języka Python

Naukowcy danych i deweloperzy AI używają zestawu SDK Azure Machine Learning dla języka Python do kompilowania i uruchamiania przepływów pracy uczenia maszynowego za pomocą [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md). Można korzystać z usługi w notesach Jupyter lub innym środowisku IDE języka Python za pomocą platform typu open source, takich jak TensorFlow i scikit — uczenie się.

Zestaw SDK języka Python jest preinstalowany na Microsoft maszyny wirtualnej analizy danych. Aby rozpocząć korzystanie z zestawu SDK języka Python, zobacz temat [Używanie języka Python do rozpoczynania pracy z Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Więcej narzędzi programistycznych firmy Microsoft

Możesz użyć [Instalator platformy Microsoft Web](https://www.microsoft.com/web/downloads/platform.aspx) , aby znaleźć i pobrać inne narzędzia programistyczne firmy Microsoft. Istnieje również skrót do narzędzia na pulpicie Data Science Virtual Machine firmy Microsoft.  

## <a name="important-directories-on-the-vm"></a>Ważne katalogów na maszynie Wirtualnej

| Element | Katalog |
| --- | --- |
| Konfiguracje serwera notesu programu Jupyter | C:\ProgramData\jupyter |
| Katalog macierzysty przykłady notesu programu Jupyter | C:\dsvm\notebooks i c:\Users\\< nazwa\>użytkownika \notebooks |
| Inne przykłady | C:\dsvm\samples |
| Anaconda, wartość domyślna: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 środowisko | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (autonomiczne) dla języka Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Domyślne wystąpienie języka R, Machine Learning Server (autonomiczne) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services katalogu wystąpienia w bazie danych | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Różne narzędzia | C:\dsvm\tools |

> [!NOTE]
> W systemie Windows Server 2012 wydanie systemu DSVM i Windows Server 2016 Edition przed 2018 marca domyślne środowisko Anaconda to Python 2,7. Środowisko pomocnicze to Python 3,5, który znajduje się w C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z narzędziami w DSVM, otwierając menu **Start** .
* Dowiedz się więcej o usłudze Azure Machine Learning, odczytując informacje o usłudze [Azure Machine Learning Service?](../service/overview-what-is-azure-ml.md) i wypróbować [Przewodniki Szybki Start i samouczki](../service/index.yml).
* W Eksploratorze plików przejdź do folderu C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts for Samples, które używają biblioteki kolekcję funkcji revoscaler w języku R, która obsługuje analizę danych w skali przedsiębiorstwa. 
* Zapoznaj się z artykułem [dziesięć rzeczy, które można wykonać na Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](../team-data-science-process/index.yml).
* Odwiedź stronę [galerii Azure AI](https://gallery.cortanaintelligence.com) nauki i dane analizy przykładowych maszyny korzystające z usługi Azure Machine Learning i powiązane dane usług na platformie Azure. Podano również ikonę dla tej galerii w menu **Start** i na pulpicie maszyny wirtualnej.
