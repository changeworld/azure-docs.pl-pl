---
title: 'Informacje ogólne: Maszyna DSVM z systemem Windows'
description: Szczegóły dotyczące narzędzi dostępnych w systemie Windows Data Science VM
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200018"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Informacje ogólne: Data Science Virtual Machine systemu Windows

Poniżej znajduje się lista dostępnych narzędzi w systemie Windows Data Science Virtual Machine. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Możesz użyć biblioteki Microsoft Enterprise dla analizy, ponieważ Machine Learning Server Developer Edition jest zainstalowana na maszynie wirtualnej. Wcześniej znane jako Microsoft R Server, Machine Learning Server to platforma analityczna, którą można wdrożyć. Jest ona skalowalna i komercyjnie obsługiwana.

Serwer usługi Machine Learning obsługuje różne statystyki danych big data, modelowanie predykcyjne i zadania uczenia maszynowego. Obsługuje ona pełnego zakresu analytics: eksplorację, analizę, wizualizacji i modelowania. Dzięki użyciu i rozszerzenie typu open source R i Python, serwer Machine Learning jest zgodny z języków R i Python, skryptach i funkcjach. Jest również zgodna z sieci CRAN, pip i pakietów Conda, do analizowania danych w skali przedsiębiorstwa.

Serwer Machine Learning adresy ograniczeniami pamięci języka r typu open source, dodając równoległe i fragmentaryczne przetwarzania danych. Oznacza to, że można uruchamiać analizy na znacznie większej ilości danych niż w przypadku pamięci głównej. 

Program Visual Studio Community znajduje się na maszynie Wirtualnej. Zawiera rozszerzenia R Tools for Visual Studio i Python Tools for Visual Studio (PTVS), które zapewniają pełny IDE do pracy z językiem R lub Python. Firma Microsoft udostępnia również inne środowisk IDE, takie jak [RStudio](https://www.rstudio.com) i [platformy PyCharm itd Community Edition](https://www.jetbrains.com/pycharm/) na maszynie wirtualnej.

## <a name="python"></a>Python

W przypadku programowania przy użyciu języka Python są zainstalowane Anacondae dystrybucje języka Python 2,7 i 3,6. Tych dystrybucji mają podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy. Możesz użyć PTVS, który jest zainstalowany w programie Visual Studio Community 2017. Można też użyć jednej z środowisk IDE z pakietem Anaconda, takich jak bezczynne lub Spyder. Wyszukaj i otwórz jeden z tych pakietów (klucze logo systemu Windows + S).

> [!NOTE]
> Aby wskazać narzędzi Python Tools for Visual Studio w środowisko Anaconda Python 2.7, musisz utworzyć niestandardowego środowiska dla każdej wersji. Aby ustawić te ścieżki środowiskowe w programie Visual Studio 2017 Community, przejdź do pozycji **Narzędzia** > **Python Tools** > **Python**Environments. Następnie wybierz pozycję **+ niestandardowe**.

Anaconda Python 3,6 jest zainstalowana w C:\Anaconda. Anaconda Python 2,7 jest zainstalowana w C:\Anaconda\envs\python2. Szczegółowe instrukcje znajdują się w [dokumentacji PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>Notes Jupyter

Dystrybucja Anaconda zawiera również Jupyter Notebook środowisko do udostępniania kodu i analizy. Serwer notesu programu Jupyter jest wstępnie skonfigurowana za pomocą języka Python 2.7 Python 3.x, PySpark, Julia i R jądra. Aby uruchomić serwer Jupyter i otworzyć przeglądarkę w celu uzyskania dostępu do serwera notesu, użyj ikony **Jupyter Notebook** Desktop.

Firma Microsoft spakować kilka notesów przykładowy w języku Python i R. Po uzyskaniu dostępu programu Jupyter, notesów pokazują, jak pracować z następujących technologii:

* Machine Learning Server
* SQL Server Machine Learning Services, analiza w bazie danych
* Python
* Zestaw narzędzi usług Microsoft Cognitive
* TensorFlow
* Inne technologie platformy Azure

Link do przykładów można znaleźć na stronie głównej notesu po uwierzytelnieniu do Jupyter Notebook przy użyciu utworzonego wcześniej hasła.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM obejmuje program Visual Studio Community. Jest to bezpłatna wersja popularnego środowiska IDE od firmy Microsoft, której można użyć do celów ewaluacyjnych i małych zespołów. Zapoznaj się z [postanowieniami licencyjnymi dotyczącymi oprogramowania firmy Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Otwórz program Visual Studio, korzystając z ikony pulpitu lub menu **Start** . Wyszukaj programy (logo systemu Windows + S), a następnie **program Visual Studio**. W tym miejscu możesz tworzyć projekty w językach takich jak C#, Python, R i Node. js. Zainstalowane dodatki plug-in należy wygodne do pracy z następującymi usługami platformy Azure:

* Azure Data Catalog
* Usługa Azure HDInsight dla usług Hadoop i Spark
* Azure Data Lake

Wtyczka o nazwie Azure Machine Learning dla Visual Studio Code integruje się również z Azure Machine Learning i pomaga szybko tworzyć aplikacje AI.

> [!NOTE]
> Możesz otrzymać komunikat, okres próbny wygasł. Wprowadź poświadczenia konta Microsoft. Lub Utwórz nowe bezpłatne konto, aby uzyskać dostęp do programu Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

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

## <a name="azure"></a>Azure

Kilku narzędzi platformy Azure są zainstalowane na maszynie Wirtualnej:

* Skrót na pulpicie przechodzi do dokumentacji zestawu SDK platformy Azure.
* Użyj AzCopy, aby skopiować dane z konta usługi Azure Storage i z niego. Aby zobaczyć sposób użycia, wprowadź **Azcopy** polecenie w wierszu polecenia.
* Użyj Eksplorator usługi Azure Storage, aby przeglądać obiekty przechowywane na koncie usługi Azure Storage. Kopiuje również dane do i z usługi Azure Storage. Aby uzyskać dostęp do tego narzędzia, wprowadź **Eksplorator usługi Storage** w polu **wyszukiwania** . Lub znaleźć go na Windows **Start** menu.
* AdlCopy kopiuje dane do Azure Data Lake. Aby zobaczyć sposób użycia, wprowadź **adlcopy** w wierszu polecenia.
* Narzędzie dtui kopiuje dane do i z Azure Cosmos DB, NoSQL Database w chmurze. Wprowadź **dtui** w wierszu polecenia.
* Środowisko Integration Runtime kopiuje dane między lokalnymi źródłami danych a chmurą. Jest używany w ramach narzędzi, takich jak usługi Azure Data Factory.
* Użyj Azure PowerShell do administrowania zasobami platformy Azure w języku skryptów programu PowerShell. Jest również instalowany na maszynie Wirtualnej.

## <a name="power-bi"></a>Power BI

DSVM zawiera Power BI Desktop zainstalowane, które ułatwiają tworzenie pulpitów nawigacyjnych i wizualizacji. To narzędzie do pobierania danych z różnych źródeł, tworzyć pulpity nawigacyjne i raporty i opublikuj je w chmurze. Aby uzyskać więcej informacji, zobacz [witrynę Power BI](https://powerbi.microsoft.com). Power BI Desktop można znaleźć w menu **Start** .

> [!NOTE]
> Wymagane jest konto Microsoft Office 365, aby dostęp do usługi Power BI.

## <a name="azure-machine-learning-sdk-for-python"></a>Zestaw SDK Azure Machine Learning dla języka Python

Naukowcy danych i deweloperzy AI używają zestawu SDK Azure Machine Learning dla języka Python do kompilowania i uruchamiania przepływów pracy uczenia maszynowego za pomocą [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md). Można korzystać z usługi w notesach Jupyter lub innym środowisku IDE języka Python za pomocą platform typu open source, takich jak TensorFlow i scikit — uczenie się.

Zestaw SDK języka Python jest preinstalowany na Microsoft maszyny wirtualnej analizy danych. Aby rozpocząć korzystanie z zestawu SDK języka Python, zobacz temat [Używanie języka Python do rozpoczynania pracy z Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Więcej narzędzi programistycznych firmy Microsoft

Możesz użyć [Instalator platformy Microsoft Web](https://www.microsoft.com/web/downloads/platform.aspx) , aby znaleźć i pobrać inne narzędzia programistyczne firmy Microsoft. Istnieje również skrót do narzędzia na pulpicie Data Science Virtual Machine firmy Microsoft.  

## <a name="important-directories-on-the-virtual-machine"></a>Ważne katalogi na maszynie wirtualnej

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

Masz dodatkowe pytania? Rozważ utworzenie [biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).
