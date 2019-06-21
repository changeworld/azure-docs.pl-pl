---
title: Identyfikowanie scenariuszy dla usługi Azure Machine Learning — zespołu danych dla celów naukowych
description: Wybierz odpowiednie scenariusze to zaawansowane analizy predykcyjnej przy użyciu procesu do nauki o danych zespołu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 130ab6ee8e59b157d1fbdd8769f14814445dea18
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202757"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning
W tym artykule przedstawiono różne źródła danych przykładowych i scenariusze docelowe, które są obsługiwane przez [Team Data Science naukowych](overview.md). Przetwarzania TDSP zapewnia to systematyczne podejście dla zespołów usługi umożliwiające wspólną pracę nad tworzeniem inteligentnych aplikacji. Scenariusze przedstawione w tym miejscu przedstawiono opcje dostępne w przepływie pracy przetwarzania danych, które są zależne od właściwości danych, lokalizacji źródłowych i docelowych repozytoriów na platformie Azure.

**Drzewa decyzyjnego** dla wybierając przykładowe scenariusze, które jest odpowiednie dla Twoich danych i cel są prezentowane w ostatniej sekcji.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Każdy z poniższych sekcjach przedstawiono przykładowy scenariusz. W każdym scenariuszu, do nauki o danych i analizy zaawansowanej przepływu i obsługi zasobów platformy Azure są wyświetlane.

> [!NOTE]
> **Wszystkie z poniższych scenariuszy należy:**
> <br/>
> 
> * [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md)
>   <br/>
> * [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scenariusz \#1: Małe i średnie tabelaryczny zestaw danych w lokalnych plikach
![Małych i średnich plików lokalnych][1]

#### <a name="additional-azure-resources-none"></a>Dodatkowe zasoby platformy Azure: Brak
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Przekaż zestaw danych.
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od przekazanych w zestawach danych.

## <a name="smalllocalprocess"></a>Scenariusz \#2: Małe i średnie zestawu danych lokalnych plików, które wymagają przetworzenia
![Małe i średnie pliki lokalne z przetwarzaniem][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Maszyna wirtualna platformy Azure (serwera IPython Notebook)
1. Utwórz maszynę wirtualną platformy Azure z systemem IPython Notebook.
1. Przekazywanie danych do kontenera usługi Azure storage.
1. Wstępne przetwarzanie i czyszczenie danych w IPython Notebook, uzyskiwanie dostępu do danych z kontenera usługi Azure storage.
1. Przekształcanie danych, czyszczenie, w formie tabelarycznej.
1. Zapisz przekształconych danych w obiektach blob platformy Azure.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytywanie danych z obiektów blob platformy Azure przy użyciu [importu danych] [ import-data] modułu.
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od dwóch zestawów danych.

## <a name="largelocal"></a>Scenariusz \#3: Duży zestaw danych z plików lokalnych przeznaczonych dla obiektów blob platformy Azure
![Duże pliki lokalne][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Maszyna wirtualna platformy Azure (serwera IPython Notebook)
1. Utwórz maszynę wirtualną platformy Azure z systemem IPython Notebook.
1. Przekazywanie danych do kontenera usługi Azure storage.
1. Wstępne przetwarzanie i czyszczenie danych w IPython Notebook, dostępu do danych w obiektach blob platformy Azure.
1. Przekształcanie danych, czyszczenie Tabelaryczną formę, jeśli to konieczne.
1. Eksplorowanie danych i tworzenie funkcji, zgodnie z potrzebami.
1. Wyodrębnij przykładowych danych w małych i średnich.
1. Zapisz próbki danych w obiektach blob platformy Azure.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytywanie danych z obiektów blob platformy Azure przy użyciu [importu danych] [ import-data] modułu.
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od dwóch zestawów danych.

## <a name="smalllocaltodb"></a>Scenariusz \#4: Małe i średnie zestaw plików lokalnych przeznaczonych dla programu SQL Server w maszynie wirtualnej platformy Azure
![Małych i średnich plików lokalnych do bazy danych SQL na platformie Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Maszyna wirtualna platformy Azure (SQL Server / serwera IPython Notebook)
1. Utwórz maszynę wirtualną platformy Azure, uruchamiania programu SQL Server i IPython Notebook.
1. Przekazywanie danych do kontenera usługi Azure storage.
1. Wstępne przetwarzanie i czyszczenie danych w kontenerze usługi Azure storage przy użyciu IPython Notebook.
1. Przekształcanie danych, czyszczenie Tabelaryczną formę, jeśli to konieczne.
1. Zapisywanie danych do plików lokalnych maszyn wirtualnych (IPython Notebook jest uruchomiona na maszynie Wirtualnej, można znaleźć dyski lokalne dyski maszyny Wirtualnej).
1. Ładowanie danych do bazy danych programu SQL Server uruchomiony na Maszynie wirtualnej platformy Azure.
   
   Opcja \#1: Przy użyciu programu SQL Server Management Studio.
   
   * Zaloguj się do programu SQL Server w maszynie Wirtualnej
   * Uruchom program SQL Server Management Studio.
   * Tworzenie tabel bazy danych i docelowej.
   * Użyj jednej ze zbiorczego zaimportować metody, aby załadować dane z plików lokalnej maszyny Wirtualnej.
   
   Opcja \#2: Za pomocą IPython Notebook — nie zaleca się włączenie dla średnich i większych zestawów danych
   
   <!-- -->    
   * Ciąg połączenia ODBC umożliwia dostęp do serwera SQL na maszynie Wirtualnej.
   * Tworzenie tabel bazy danych i docelowej.
   * Użyj jednej ze zbiorczego zaimportować metody, aby załadować dane z plików lokalnej maszyny Wirtualnej.
1. Eksplorowanie danych, tworzenie funkcji, zgodnie z potrzebami. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj tylko niezbędne zapytanie, aby je utworzyć.
1. Decyzję w sprawie wielkość próbki danych, jeśli wymagane lub pożądane.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytywanie danych bezpośrednio za pomocą programu SQL Server [importu danych] [ import-data] modułu. Wklej zapytanie to konieczne, który wyodrębnia pola i tworzy funkcje oraz próbkuje dane, w razie potrzeby bezpośrednio w [importu danych] [ import-data] zapytania.
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od dwóch zestawów danych.

## <a name="largelocaltodb"></a>Scenariusz \#5: Duży zestaw danych w lokalnych plikach docelowych programu SQL Server na maszynie Wirtualnej platformy Azure
![Duże pliki lokalne do bazy danych SQL na platformie Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Maszyna wirtualna platformy Azure (SQL Server / serwera IPython Notebook)
1. Utwórz maszynę wirtualną platformy Azure z programem SQL Server i serwera IPython Notebook.
1. Przekazywanie danych do kontenera usługi Azure storage.
1. (Opcjonalnie) Wstępne przetwarzanie i czyszczenie danych.
   
   a.  Wstępne przetwarzanie i czyszczenie danych w IPython Notebook, uzyskiwanie dostępu do danych z platformy Azure
   
       blobs.
   
   b.  Przekształcanie danych, czyszczenie Tabelaryczną formę, jeśli to konieczne.
   
   c.  Zapisywanie danych do plików lokalnych maszyn wirtualnych (IPython Notebook jest uruchomiona na maszynie Wirtualnej, można znaleźć dyski lokalne dyski maszyny Wirtualnej).
1. Ładowanie danych do bazy danych programu SQL Server uruchomiony na Maszynie wirtualnej platformy Azure.
   
   a.  Zaloguj się do programu SQL Server w maszynie Wirtualnej.
   
   b.  Jeśli nie już zapisany na danych, pobieranie plików danych na platformie Azure
   
       storage container to local-VM folder.
   
   c.  Uruchom program SQL Server Management Studio.
   
   d.  Tworzenie tabel bazy danych i docelowej.
   
   e.  Użyj jednej ze zbiorczego importowanie metod do ładowania danych.
   
   f.  Jeśli sprzężeń tabel są wymagane, należy utworzyć indeksy, aby przyspieszyć sprzężenia.
   
   > [!NOTE]
   > Szybsze ładowanie dużych ilości danych zalecane jest, że tworzenia podzielonych tabel i danych w sposób równoległy importu zbiorczego. Aby uzyskać więcej informacji, zobacz [równoległe importowania danych do tabel na partycje SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Eksplorowanie danych, tworzenie funkcji, zgodnie z potrzebami. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj tylko niezbędne zapytanie, aby je utworzyć.
1. Decyzję w sprawie wielkość próbki danych, jeśli wymagane lub pożądane.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytywanie danych bezpośrednio za pomocą programu SQL Server [importu danych] [ import-data] modułu. Wklej zapytanie to konieczne, który wyodrębnia pola i tworzy funkcje oraz próbkuje dane, w razie potrzeby bezpośrednio w [importu danych] [ import-data] zapytania.
1. Prosty przepływ eksperymentu usługi Azure Machine Learning, począwszy od przekazano zestaw danych

## <a name="largedbtodb"></a>Scenariusz \#6: Duży zestaw danych w bazie programu SQL Server w środowisku lokalnym, przeznaczonych dla programu SQL Server w maszynie wirtualnej platformy Azure
![Duże SQL DB środowiska lokalnego do bazy danych SQL na platformie Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Maszyna wirtualna platformy Azure (SQL Server / serwera IPython Notebook)
1. Utwórz maszynę wirtualną platformy Azure z programem SQL Server i serwera IPython Notebook.
1. Użyj jednego z danych eksportowania metod, aby wyeksportować dane z programu SQL Server do plików zrzutu.
   
   > [!NOTE]
   > Jeśli zdecydujesz przenieść wszystkie dane w lokalnej bazy danych i alternatywna metoda (szybsze), aby przenieść pełnej bazy danych do wystąpienia programu SQL Server na platformie Azure. Pomiń kroki, aby wyeksportować dane, utworzyć bazę danych, a obciążenia/importowanie danych do docelowej bazy danych i postępuj zgodnie z alternatywna metoda.
   > 
   > 
1. Przekaż pliki zrzutu do kontenera usługi Azure storage.
1. Załaduj dane do bazy danych programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure.
   
   a.  Zaloguj się do maszyny Wirtualnej programu SQL Server.
   
   b.  Pobierz pliki danych z kontenera usługi Azure storage do folderu lokalnego maszyn wirtualnych.
   
   c.  Uruchom program SQL Server Management Studio.
   
   d.  Tworzenie tabel bazy danych i docelowej.
   
   e.  Użyj jednej ze zbiorczego importowanie metod do ładowania danych.
   
   f.  Jeśli sprzężeń tabel są wymagane, należy utworzyć indeksy, aby przyspieszyć sprzężenia.
   
   > [!NOTE]
   > Szybsze ładowanie dużych ilości danych, Utwórz partycjonowane tabele i zbiorcze importowanie danych w sposób równoległy. Aby uzyskać więcej informacji, zobacz [równoległe importowania danych do tabel na partycje SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Eksplorowanie danych, tworzenie funkcji, zgodnie z potrzebami. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj tylko niezbędne zapytanie, aby je utworzyć.
1. Decyzję w sprawie wielkość próbki danych, jeśli wymagane lub pożądane.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytywanie danych bezpośrednio za pomocą programu SQL Server [importu danych] [ import-data] modułu. Wklej zapytanie to konieczne, który wyodrębnia pola i tworzy funkcje oraz próbkuje dane, w razie potrzeby bezpośrednio w [importu danych] [ import-data] zapytania.
1. Począwszy od zestawu danych przekazanych prosty przepływ eksperymentu usługi Azure Machine Learning.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Metoda alternatywna do skopiowania pełnej bazy danych na lokalnym serwerze SQL Server do usługi Azure SQL Database
![Odłączanie lokalnej bazy danych i dołączyć do bazy danych SQL na platformie Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Maszyna wirtualna platformy Azure (SQL Server / serwera IPython Notebook)
Aby replikować całą bazę danych programu SQL Server na maszynie Wirtualnej programu SQL Server, należy skopiować bazę danych z jednej lokalizacji/serwera do innego, przy założeniu, że bazy danych mogą być podejmowane tymczasowo w trybie offline. Można to zrobić w Eksploratorze obiektów SQL Server Management Studio lub za pomocą równoważne polecenia języka Transact-SQL.

1. Odłącz bazę danych w lokalizacji źródłowej. Aby uzyskać więcej informacji, zobacz [Odłącz bazę danych](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. W oknie Eksploratora Windows lub Windows, okno polecenia skopiuj plik odłączono bazę danych lub pliki i pliku dziennika lub pliki do lokalizacji docelowej na maszynę Wirtualną programu SQL Server na platformie Azure.
1. Dołączanie plików do docelowym wystąpienia programu SQL Server. Aby uzyskać więcej informacji, zobacz [dołączyć bazy danych](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Przenieś bazę danych za pomocą odłączyć, a następnie dołączyć (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scenariusz \#7: Dane big data w lokalnych plikach docelowa baza danych programu Hive w klastrach usługi Azure HDInsight Hadoop
![Dane big data w celu lokalnej gałęzi][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Usługi Azure HDInsight, klaster Hadoop i maszyny wirtualnej platformy Azure (serwera IPython Notebook)
1. Utwórz maszynę wirtualną platformy Azure z systemem serwera IPython Notebook.
1. Tworzenie klastra usługi Azure HDInsight Hadoop.
1. (Opcjonalnie) Wstępne przetwarzanie i czyszczenie danych.
   
   a.  Wstępne przetwarzanie i czyszczenie danych w IPython Notebook, uzyskiwanie dostępu do danych z platformy Azure
   
       blobs.
   
   b.  Przekształcanie danych, czyszczenie Tabelaryczną formę, jeśli to konieczne.
   
   c.  Zapisywanie danych do plików lokalnych maszyn wirtualnych (IPython Notebook jest uruchomiona na maszynie Wirtualnej, można znaleźć dyski lokalne dyski maszyny Wirtualnej).
1. Przekazywanie danych do domyślnego kontenera klastra Hadoop wybrane w kroku 2.
1. Ładowanie danych do bazy danych Hive w klastrze Azure HDInsight Hadoop.
   
   a.  Zaloguj się do węzła głównego klastra usługi Hadoop
   
   b.  Otwórz okno wiersza polecenia usługi Hadoop.
   
   c.  Wprowadź katalog główny Hive przy użyciu polecenia `cd %hive_home%\bin` w wierszu polecenia usługi Hadoop.
   
   d.  Uruchamianie zapytań programu Hive, aby utworzyć bazę danych i tabele i ładowanie danych z magazynu obiektów blob do tabel programu Hive.
   
   > [!NOTE]
   > Jeśli dane big Data, użytkownicy mogą tworzyć tabelę programu Hive z partycjami. Następnie użytkownicy mogą używać `for` pętli w Hadoop wiersza polecenia w węźle głównym do ładowania danych do tabeli programu Hive na partycje według partycji.
   > 
   > 
1. Eksplorowanie danych i tworzenie funkcji, zgodnie z potrzebami w oknie wiersza polecenia usługi Hadoop. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj tylko niezbędne zapytanie, aby je utworzyć.
   
   a.  Zaloguj się do węzła głównego klastra usługi Hadoop
   
   b.  Otwórz okno wiersza polecenia usługi Hadoop.
   
   c.  Wprowadź katalog główny Hive przy użyciu polecenia `cd %hive_home%\bin` w wierszu polecenia usługi Hadoop.
   
   d.  Uruchom zapytania programu Hive w oknie wiersza polecenia usługi Hadoop na węzła głównego klastra usługi Hadoop do eksplorowania danych i tworzenie funkcji, zgodnie z potrzebami.
1. Jeśli wymagane lub pożądane, przykładowe dane, aby zmieścić ją w usłudze Azure Machine Learning Studio.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytywanie danych bezpośrednio z `Hive Queries` przy użyciu [importu danych] [ import-data] modułu. Wklej zapytanie to konieczne, który wyodrębnia pola i tworzy funkcje oraz próbkuje dane, w razie potrzeby bezpośrednio w [importu danych] [ import-data] zapytania.
1. Począwszy od zestawu danych przekazanych prosty przepływ eksperymentu usługi Azure Machine Learning.

## <a name="decisiontree"></a>Drzewo decyzyjne dotyczące wybór scenariusza
---
Poniższy diagram przedstawia opisanych powyżej scenariuszy i Advanced Analytics Process and technologi wprowadzone prowadzące do poszczególnych scenariuszy dla pozycji. Należy zauważyć, że przetwarzanie danych, eksploracji, technicznego opracowywania funkcji i próbkowanie może potrwać umieścić w co najmniej jeden metoda/środowisko — w źródle, pośrednie, i/lub środowiskach docelowych — i może kontynuować interakcyjnie, zgodnie z potrzebami. Diagram tylko stanowi ilustrację niektóre możliwe przepływów i nie zapewnia pełnego wyliczenia.

![Przykładowe DS procesu wskazówki scenariusze][8]

### <a name="advanced-analytics-in-action-examples"></a>Zaawansowane analizy w działaniu przykłady
Aby uzyskać wskazówki usługi Azure Machine Learning end-to-end, które Advanced Analytics Process and Technology, korzystając z publicznych zestawów danych zobacz:

* [Zespół danych dla celów naukowych w działaniu: przy użyciu programu SQL Server](sql-walkthrough.md).
* [Zespół danych dla celów naukowych w działaniu: przy użyciu klastrów usługi HDInsight Hadoop](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
