---
title: Utwórz maszynę wirtualną do nauki o danych Windows
titleSuffix: Azure
description: Konfigurowanie i tworzenie maszyny wirtualnej do nauki o danych na platformie Azure, analizy i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: dfb3e9f6390d4c80b8f3c37b87f2659c671fa823
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591826"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Inicjowanie obsługi administracyjnej Data Science Virtual Machine systemu Windows na platformie Azure

Microsoft Windows Data Science Virtual Machine (DSVM) to obraz maszyny wirtualnej z systemem Windows Server 2016 (VM) na platformie Azure, który jest wstępnie zainstalowany i skonfigurowany za pomocą narzędzi do analizy danych i uczenia maszynowego.

## <a name="included-data-science-tools"></a>Dołączone narzędzia do nauki o danych

W DSVM znajdują się następujące narzędzia:

* [Usługa Azure Machine Learning](../service/index.yml) Zestaw SDK języka Python
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Wersja Developer
* Anaconda Python dystrybucji
* Jupyter Notebook z jądrami R, Python i PySpark
* Microsoft Visual Studio Community
* Power BI Desktop firmy Microsoft
* Microsoft SQL Server 2017 Developer Edition
* Autonomiczne wystąpienie Apache Spark na potrzeby lokalnego tworzenia i testowania
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning i data analitycznymi:
  * Platformy uczenia głębokiego — bogaty zestaw środowisk AI jest dostępny na maszynie wirtualnej: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [łańcucha](https://chainer.org/), mxNet i Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) — szybki system uczenia maszynowego, który obsługuje techniki takie jak mieszanie online, allreduce, redukcje, learning2search i aktywna i interaktywna nauka
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) — narzędzie, które zapewnia szybką i dokładną implementację podwyższania poziomu drzewa
  * [Rattle](https://togaware.com/rattle/) — narzędzie analityczne języka r, które pozwala rozpocząć pracę z analizami danych i uczeniem maszynowym w języku r. Obejmuje eksplorowanie i modelowanie danych w graficznym interfejsie użytkownika przy użyciu automatycznego generowania kodu języka R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) — oprogramowanie do wyszukiwania danych i uczenia maszynowego w języku Java
  * [Apache drążenie](https://drill.apache.org/) — aparat zapytań SQL bez schematu dla Apache Hadoop, NoSQL i magazynu w chmurze. Obsługuje ona ODBC i sterownika JDBC interfejsy do wykonywania zapytań, NoSQL i pliki ze standardowych narzędzi analizy Biznesowej, takich jak Power BI, programie Microsoft Excel i Tableau.
* Bibliotek języka R i Python do użycia w usłudze Azure Machine Learning i innymi usługami platformy Azure
* Git, w tym powłoki Git Bash, aby pracować z repozytoriów kodu źródłowego, które zawierają usługi GitHub i DevOps platformy Azure. Usługa Git oferuje kilka popularnych Linux narzędzi wiersza polecenia, które są dostępne zarówno w Git Bash, jak i wiersza polecenia. Przykładami są awk, sed, perl, grep, wyszukiwanie, wget i narzędzie curl.

### <a name="about-data-science"></a>Informacje o nauce danych

Do nauki o danych obejmuje iteracja w sekwencji zadań:

1. Znajdowanie, ładowanie i wstępne przetwarzanie danych
1. Kompilowanie i testowanie modeli
1. Wdrażanie modeli do użycia w inteligentnych aplikacjach

Analitycy danych używać kilku narzędzi do wykonywania tych zadań. Może zająć dużo czasu można znaleźć odpowiednie wersje oprogramowania, a następnie Pobierz i zainstaluj je. DSVM oszczędza czas, dostarczając gotowy do użycia obraz, który można zainicjować na platformie Azure przy użyciu kilku popularnych narzędzi, które są wstępnie zainstalowane i skonfigurowane.

DSVM skacze — uruchamia projekt analizy. Możesz pracować nad zadaniami w różnych językach, w tym języka R, Python, SQL i C#. Visual Studio zapewnia łatwe w użyciu zintegrowanego rozwoju środowiska (IDE) do tworzenia i testowania kodu. Zestaw Azure SDK jest dołączony do maszyny wirtualnej, dzięki czemu można kompilować aplikacje przy użyciu różnych usług na platformie chmury firmy Microsoft.

Opłaty nie będą oprogramowania dla tego obrazu maszyny Wirtualnej do nauki o danych. Opłaty są naliczane tylko opłaty za użycie platformy Azure. Są one zależne od rozmiaru maszyny wirtualnej, którą możesz aprowizować. Szczegółowe informacje na temat obliczeń opłaty znajdują się w **cennik** sekcji na [maszyny wirtualnej do nauki o danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) strony.

### <a name="other-dsvm-versions"></a>Inne wersje DSVM

* [Ubuntu](dsvm-ubuntu-intro.md) obrazu. Ma on wiele narzędzi, podobne do maszyny DSVM plus kilka dodatkowych platform do uczenia głębokiego.
* A [Linux CentOS](linux-dsvm-intro.md) obrazu.
* [Wersji systemu Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) maszyny wirtualnej do nauki o danych. Niektóre narzędzia są dostępne tylko w wersji systemu Windows Server 2016. W przeciwnym razie ten artykuł dotyczy również do wersji Windows Server 2012.

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć maszynę wirtualną do nauki o danych firmy Microsoft, musi mieć subskrypcję platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Tworzenie DSVM

Aby utworzyć wystąpienie DSVM:

1. Przejdź do listy maszyn wirtualnych na [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Jeśli użytkownik nie jest jeszcze zalogowany, może zostać wyświetlony monit o zalogowanie się do konta platformy Azure.
1. Wybierz przycisk **Utwórz** u dołu.

   ![Konfigurowanie — — — maszyna wirtualna analizy danych](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Musisz wprowadzić poniższe informacje, aby skonfigurować każdy z kroków widocznych w prawym okienku zrzutu ekranu:

   1. **Podstawy**:
      * **Name**: Nazwa DSVM
      * **Typ dysku maszyny wirtualnej**: dysk **SSD** lub **dysk twardy**. W przypadku wystąpienia procesora GPU NC_v1 takich jak procesory GPU NVidia Tesla K80 na podstawie wybierz **HDD** jako typ dysku.
      * **Nazwa użytkownika**: Identyfikator konta administratora
      * **Hasło**: hasło konta administratora
      * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane.
      * **Grupa zasobów**. Możesz utworzyć nową lub użyj istniejącej grupy.
      * **Lokalizacja**. Wybierz centrum danych, które jest najbardziej odpowiednie. Najszybszy dostęp do sieci to centrum danych, który zawiera większość danych lub znajduje się najbliżej Twojej lokalizacji fizycznej.
   1. **Rozmiar**: Wybierz jeden z typów serwerów, które spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Aby wyświetlić więcej rozmiarów maszyn wirtualnych, wybierz **Wyświetl wszystko**.
   1. **Ustawienia**:  
      * **Użyj usługi Managed Disks**. Wybierz **zarządzane** chcącym platformy Azure do zarządzania dyskami maszyny wirtualnej. W przeciwnym razie należy określić konto magazynu w nowym lub istniejącym.  
      * **Inne parametry**. Można użyć wartości domyślnych. Jeśli chcesz użyć wartości innych niż domyślne, umieść wskaźnik myszy na linku informacyjnym, aby uzyskać pomoc dotyczącą określonych pól.
   1. **Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. Wybierz pozycję **Utwórz**.

> [!NOTE]
> * Na maszynie wirtualnej nie są naliczane żadne dodatkowe opłaty wykraczające poza koszt obliczeń dla rozmiaru serwera wybranego w kroku **rozmiar** .
> * Aprowizowanie trwa około 10-20 minut. Stan maszyny wirtualnej można wyświetlić na Azure Portal.

## <a name="how-to-access-the-dsvm"></a>Jak uzyskać dostęp do DSVM

Po maszyny Wirtualnej jest tworzony i zainicjowano obsługę administracyjną, można pulpitu zdalnego do niej przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. Możesz rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Do wielu narzędzi można uzyskać dostęp za pomocą kafelków menu Start i ikon pulpitu.

Możesz również dołączyć Data Science VM do Azure Notebooks do uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów notesów — warstwa obliczeniowa](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Narzędzi zainstalowanych w programie Microsoft maszyny wirtualnej analizy danych

Dowiedz się więcej o narzędziach, które są zainstalowane na DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Można użyć Microsoft Enterprise Library dla skalowalnych R lub Python do celów analizy, ponieważ Machine Learning Server Developer edition jest zainstalowany na maszynie Wirtualnej. Wcześniej znane jako Microsoft R Server, Machine Learning Server to szeroko wdrażana platforma analityczna klasy korporacyjnej. Jest on dostępny dla języków R i Python. Jest to również skalowalne, obsługiwane komercyjnie i bezpieczne.

Serwer usługi Machine Learning obsługuje różne statystyki danych big data, modelowanie predykcyjne i zadania uczenia maszynowego. Obsługuje ona pełnego zakresu analytics: eksplorację, analizę, wizualizacji i modelowania. Dzięki użyciu i rozszerzenie typu open source R i Python, serwer Machine Learning jest zgodny z języków R i Python, skryptach i funkcjach. Jest również zgodna z sieci CRAN, pip i pakietów Conda, do analizowania danych w skali przedsiębiorstwa.

Serwer Machine Learning adresy ograniczeniami pamięci języka r typu open source, dodając równoległe i fragmentaryczne przetwarzania danych. Oznacza to, że można uruchamiać analizy na znacznie większej ilości danych niż w przypadku pamięci głównej. Program Visual Studio Community znajduje się na maszynie Wirtualnej. Zawiera rozszerzenia R Tools for Visual Studio i Python Tools for Visual Studio (PTVS), które zapewniają pełny IDE do pracy z językiem R lub Python. Firma Microsoft udostępnia również innych środowisk IDE, takie jak [RStudio](https://www.rstudio.com) i [platformy PyCharm Community edition](https://www.jetbrains.com/pycharm/) na maszynie Wirtualnej.

### <a name="python"></a>Python

W przypadku programowania przy użyciu języka Python są zainstalowane Anacondae dystrybucje języka Python 2,7 i 3,6. Tych dystrybucji mają podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy. Możesz użyć narzędzi PTVS, który jest instalowany w ramach programu Visual Studio Community 2017. Lub można użyć jednego środowiska IDE, powiązane z pakietu Anaconda, takich jak BEZCZYNNE lub Spyder. Wyszukaj, a następnie uruchom jeden z tych pakietów (Win + S).

> [!NOTE]
> Aby wskazać narzędzi Python Tools for Visual Studio w środowisko Anaconda Python 2.7, musisz utworzyć niestandardowego środowiska dla każdej wersji. Aby ustawić te ścieżki środowiska Visual Studio 2017 Community, przejdź do **narzędzia** > **narzędzi Python Tools** > **środowiska Python**. Następnie wybierz pozycję **+ niestandardowe**.

Anaconda Python 3.6 jest zainstalowany w ramach **C:\Anaconda**. Anaconda Python 2.7 jest zainstalowany w ramach **c:\Anaconda\envs\python2**. Aby uzyskać szczegółowe instrukcje, zobacz [dokumentacja narzędzi PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Notes Jupyter

Dystrybucja pakietu anaconda również jest dostarczany z notesu programu Jupyter, środowisko, aby udostępniać kod i analizy. Serwer notesu programu Jupyter jest wstępnie skonfigurowana za pomocą języka Python 2.7 Python 3.x, PySpark, Julia i R jądra. Aby uruchomić serwer Jupyter i uruchomić przeglądarkę w celu uzyskania dostępu do serwera notesu, użyj ikony **Jupyter Notebook** Desktop.

Firma Microsoft spakować kilka notesów przykładowy w języku Python i R. Po uzyskaniu dostępu programu Jupyter, notesów pokazują, jak pracować z następujących technologii:

* Machine Learning Server
* SQL Server Machine Learning Services, analiza w bazie danych
* Python
* Zestaw narzędzi firmy Microsoft do percepcji
* TensorFlow
* Inne technologie platformy Azure

Link do przykładów można znaleźć na stronie głównej notesu po uwierzytelnieniu do Jupyter Notebook przy użyciu hasła utworzonego we wcześniejszym kroku.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM obejmuje program Visual Studio Community. Jest to bezpłatna wersja popularnego środowiska IDE od firmy Microsoft, której można użyć do celów ewaluacyjnych i małych zespołów. Zobacz [postanowienia licencyjne](https://www.visualstudio.com/support/legal/mt171547).

Otwórz program Visual Studio, korzystając z ikony pulpitu lub menu **Start** . Wyszukaj programy (Win + S), a następnie **programu Visual Studio**. W tym miejscu mogą tworzyć projekty w językach takich jak C#, Python, R i node.js. Zainstalowane dodatki plug-in należy wygodne do pracy z następującymi usługami platformy Azure:

* Azure Data Catalog
* Usługa Azure HDInsight Hadoop i Spark
* Azure Data Lake

Istnieje również wtyczka o nazwie **Azure Machine Learning dla Visual Studio Code** , która bezproblemowo integruje się z Azure Machine Learning i pomaga szybko tworzyć aplikacje AI.

> [!NOTE]
> Możesz otrzymać komunikat, okres próbny wygasł. Wprowadź poświadczenia konta Microsoft. Lub Utwórz nowe bezpłatne konto, aby uzyskać dostęp do programu Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM jest dołączona do wersji Developer SQL Server 2017 z Machine Learning Services. Ta wersja SQL Server jest dostępna w języku R lub Python i może być uruchamiana w ramach analizy bazy danych. Usługi Machine Learning zapewnia platformę do tworzenia i wdrażania inteligentnych aplikacji. Języki te i wiele pakietów przez społeczność służy do tworzenia modeli i generować prognozy dla danych programu SQL Server. Umożliwia zachowanie analytics blisko danych, ponieważ usługi Machine Learning, w bazie danych, integruje się języków R i Python w programie SQL Server. Ta integracja pozwala wyeliminować koszty i zagrożenia bezpieczeństwa związane z przenoszeniem danych.

> [!NOTE]
> Wersja programu SQL Server Developer jest tylko do celów tworzenia i testowania. Niezbędna jest licencja go uruchomić w środowisku produkcyjnym.

Dostęp z programu SQL Server, uruchamiając program Microsoft SQL Server Management Studio. Nazwę maszyny Wirtualnej jest wypełniana jako **nazwy serwera**. Uwierzytelnianie Windows po zalogowaniu się jako administrator na Windows. Po otwarciu programu SQL Server Management Studio można tworzyć innych użytkowników, tworzenie baz danych, importowanie danych i uruchamiać zapytania SQL.

Aby włączyć analizę w bazie danych za pomocą usług SQL Machine Learning, uruchom następujące polecenie jako akcja jednorazowa w programie SQL Server Management Studio, po zalogowaniu się jako administrator serwera:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Zamień `%COMPUTERNAME%` na nazwę maszyny wirtualnej.

### <a name="azure"></a>Azure

Kilku narzędzi platformy Azure są zainstalowane na maszynie Wirtualnej:

* Skrót na pulpicie przechodzi do dokumentacji zestawu SDK platformy Azure.
* Użyj **AzCopy** , aby skopiować dane z konta usługi Azure Storage i z niego. Aby zobaczyć sposób użycia, wprowadź **Azcopy** polecenie w wierszu polecenia.
* Użyj **Eksploratora usługi Azure Storage** do przeglądania obiektów przechowywanych na koncie usługi Azure Storage. Kopiuje również dane do i z usługi Azure Storage. Aby uzyskać dostęp do tego narzędzia, wprowadź **Eksplorator usługi Storage** w polu **wyszukiwania** . Lub znajdź je w menu **Start** systemu Windows.
* **Adlcopy** kopiuje dane do Azure Data Lake. Aby zobaczyć sposób użycia, wprowadź **adlcopy** w wierszu polecenia.
* **dtui** kopiuje dane do i z Azure Cosmos DB, NoSQL Database w chmurze. Wprowadź **dtui** w wierszu polecenia.
* **Azure Data Factory Integration Runtime** kopiuje dane między lokalnymi źródłami danych a chmurą. Jest używany w ramach narzędzi, takich jak usługi Azure Data Factory.
* Użyj **Microsoft Azure PowerShell** do administrowania zasobami platformy Azure w języku skryptów programu PowerShell. Jest również instalowany na maszynie Wirtualnej.

### <a name="power-bi"></a>Power BI

DSVM zawiera **Power BI Desktop** zainstalowane, które ułatwiają tworzenie pulpitów nawigacyjnych i wizualizacji. To narzędzie do pobierania danych z różnych źródeł, tworzyć pulpity nawigacyjne i raporty i opublikuj je w chmurze. Aby uzyskać więcej informacji, zobacz [usługi Power BI](https://powerbi.microsoft.com) lokacji. Power BI Desktop można znaleźć w menu **Start** .

> [!NOTE]
> Wymagane jest konto Microsoft Office 365, aby dostęp do usługi Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Usługa Azure Machine Learning zestawu SDK języka Python

Naukowcy danych i deweloperzy AI używają zestawu SDK Azure Machine Learning dla języka Python do kompilowania i uruchamiania przepływów pracy uczenia maszynowego za pomocą [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md). Można korzystać z usługi w notesach Jupyter lub innym środowisku IDE języka Python za pomocą platform typu open source, takich jak TensorFlow i scikit — uczenie się.

Aby rozpocząć pracę, przy użyciu zestawu SDK języka Python, zobacz [za pomocą języka Python Rozpoczynanie pracy z usługą Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Zestaw SDK języka Python jest preinstalowany na Microsoft maszyny wirtualnej analizy danych.

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
| Microsoft Machine Learning Server (autonomiczna) w języku Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Wystąpienia domyślnego języka R, serwer Machine Learning (Wersja autonomiczna) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Katalog wystąpienia w bazie danych SQL Machine Learning Services | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Różne narzędzia | C:\dsvm\tools |

> [!NOTE]
> W tej wersji systemu Windows Server 2012 wersji maszyny wirtualnej DSVM i Windows Server 2016 przed marca 2018 r. domyślne środowisko Anaconda jest język Python 2.7. Środowisko pomocnicze to Python 3,5, który znajduje się w **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Następne kroki

* Odkryj narzędzia i na maszyny Wirtualnej do analizy danych, wybierając **Start** menu.
* Dowiedz się więcej o usłudze Azure Machine Learning, czytając [co to jest usługa Azure Machine Learning?](../service/overview-what-is-azure-ml.md) i wypróbować [przewodników Szybki Start i samouczki](../service/index.yml) , które są dostępne.
* W Eksploratorze plików przejdź do **folderu C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** for Samples, które używają biblioteki kolekcję funkcji revoscaler w języku R, która obsługuje analizę danych w skali przedsiębiorstwa.  
* Przeczytaj artykuł [dziesięć rzeczy, które można wykonać na maszynie wirtualnej analizy danych](https://aka.ms/dsvmtenthings).
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](../team-data-science-process/index.yml).
* Odwiedź stronę [galerii Azure AI](https://gallery.cortanaintelligence.com) nauki i dane analizy przykładowych maszyny korzystające z usługi Azure Machine Learning i powiązane dane usług na platformie Azure. Podano także ikony dla tej galerii na **Start** menu i pulpitu maszyny wirtualnej.
