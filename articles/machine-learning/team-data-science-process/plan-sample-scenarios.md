---
title: Identyfikowanie scenariuszy dla usługi Azure Machine Learning — proces nauki o danych zespołowych
description: Wybierz odpowiednie scenariusze do prowadzenia zaawansowanej analizy predykcyjnej za pomocą procesu nauki o danych zespołu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251624"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning
W tym artykule opisano różnorodność przykładowych źródeł danych i scenariuszy docelowych, które mogą być obsługiwane przez [proces nauki o danych zespołu (TDSP)](overview.md). TDSP zapewnia systematyczne podejście zespołów do współpracy przy tworzeniu inteligentnych aplikacji. Scenariusze przedstawione w tym miejscu ilustrują opcje dostępne w przepływie pracy przetwarzania danych, które zależą od właściwości danych, lokalizacji źródłowych i repozytoriów docelowych na platformie Azure.

**Drzewo decyzyjne** do wybierania przykładowych scenariuszy, które są odpowiednie dla danych i celu jest przedstawiona w ostatniej sekcji.

Każda z poniższych sekcji przedstawia przykładowy scenariusz. Dla każdego scenariusza są wyświetlane informacje o możliwych danych lub przepływie zaawansowanej analizy i obsłudze zasobów platformy Azure.

> [!NOTE]
> **Dla wszystkich następujących scenariuszy należy:**
> <br/>
> 
> * [Tworzenie konta magazynu](../../storage/common/storage-account-create.md)
>   <br/>
> * [Tworzenie obszaru roboczego usługi Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scenariusz \#1: Mały i średni zestaw danych tabelaryczny w plikach lokalnych
![Małe i średnie pliki lokalne][1]

#### <a name="additional-azure-resources-none"></a>Dodatkowe zasoby platformy Azure: Brak
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Przekaż zestaw danych.
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od przekazanych zestawów danych.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scenariusz \#2: Mały i średni zestaw danych plików lokalnych, które wymagają przetwarzania
![Małe i średnie pliki lokalne z przetwarzaniem][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer notebooka IPython)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym notesem IPython.
1. Przekazywanie danych do kontenera usługi Azure Storage.
1. Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z kontenera usługi Azure Storage.
1. Przekształć dane do oczyszczonego formularza tabelaryczne.
1. Zapisywanie przekształconych danych w obiektach blob platformy Azure.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytuj dane z obiektów blob platformy Azure przy użyciu modułu [Importuj dane.][import-data]
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od zestawu danych pochłoniętych.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scenariusz \#3: Duży zestaw danych plików lokalnych, ukierunkowany na obiekty blob platformy Azure
![Duże pliki lokalne][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer notebooka IPython)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym notesem IPython.
1. Przekazywanie danych do kontenera usługi Azure Storage.
1. Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z obiektów blob platformy Azure.
1. Przekształć dane do oczyszczonego formularza tabelaryczne, jeśli to konieczne.
1. Eksploruj dane i w razie potrzeby twórz funkcje.
1. Wyodrębnij próbkę danych o małej i średniej.
1. Zapisz przykładowe dane w obiektach blob platformy Azure.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytuj dane z obiektów blob platformy Azure przy użyciu modułu [Importuj dane.][import-data]
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od zestawu danych pochłoniętych.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scenariusz \#4: Mały i średni zestaw danych plików lokalnych, ukierunkowany na program SQL Server na maszynie wirtualnej platformy Azure
![Małe i średnie pliki lokalne do bazy danych SQL na platformie Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer SQL Server / IPython Notebook server)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym programem SQL Server + IPython Notebook.
1. Przekazywanie danych do kontenera usługi Azure Storage.
1. Wstępnie przetwarzaj i czyścij dane w kontenerze usługi Azure Storage przy użyciu notesu IPython.
1. Przekształć dane do oczyszczonego formularza tabelaryczne, jeśli to konieczne.
1. Zapisywanie danych w plikach lokalnych maszyn wirtualnych (notebook IPython jest uruchomiony na maszynie Wirtualnej, dyski lokalne odnoszą się do dysków maszyn wirtualnych).
1. Ładowanie danych do bazy danych programu SQL Server uruchomionej na maszynie Wirtualnej platformy Azure.
   
   Opcja \#1: Korzystanie z programu SQL Server Management Studio.
   
   * Zaloguj się do maszyny Wirtualnej programu SQL Server
   * Uruchom program SQL Server Management Studio.
   * Tworzenie tabel bazy danych i tabel docelowych.
   * Użyj jednej z metod importu zbiorczego, aby załadować dane z plików lokalnych maszyn wirtualnych.
   
   Opcja \#2: Korzystanie z notebooka IPython — nie wskazane w przypadku średnich i większych zestawów danych
   
   <!-- -->    
   * Użyj ciągu połączenia ODBC, aby uzyskać dostęp do programu SQL Server na maszynie Wirtualnej.
   * Tworzenie tabel bazy danych i tabel docelowych.
   * Użyj jednej z metod importu zbiorczego, aby załadować dane z plików lokalnych maszyn wirtualnych.
1. Eksploruj dane, twórz funkcje w razie potrzeby. Funkcje nie muszą być materialized w tabelach bazy danych. Zanotuj tylko kwerendę niezbędną do ich utworzenia.
1. Zdecyduj o rozmiarze próbki danych, jeśli jest to konieczne i/lub pożądane.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytuj dane bezpośrednio z programu SQL Server przy użyciu modułu [Importuj dane.][import-data] Wklej niezbędną kwerendę, która wyodrębnia pola, tworzy funkcje i przykłada dane, jeśli jest to potrzebne bezpośrednio w kwerendzie [Importuj dane.][import-data]
1. Tworzenie przepływu eksperymentu usługi Azure Machine Learning, począwszy od zestawu danych pochłoniętych.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scenariusz \#5: Duży zestaw danych w plikach lokalnych, docelowy program SQL Server na maszynie Wirtualnej platformy Azure
![Duże pliki lokalne do bazy danych SQL na platformie Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer SQL Server / IPython Notebook server)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym serwerem SQL Server i serwerem notesu IPython.
1. Przekazywanie danych do kontenera usługi Azure Storage.
1. (Opcjonalnie) Wstępne przetwarzanie i czyszczenie danych.
   
    a.  Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z obiektów blob platformy Azure.
   
    b.  Przekształć dane do oczyszczonego formularza tabelaryczne, jeśli to konieczne.
   
    d.  Zapisywanie danych w plikach lokalnych maszyn wirtualnych (notebook IPython jest uruchomiony na maszynie Wirtualnej, dyski lokalne odnoszą się do dysków maszyn wirtualnych).
1. Ładowanie danych do bazy danych programu SQL Server uruchomionej na maszynie Wirtualnej platformy Azure.
   
    a.  Zaloguj się do maszyny Wirtualnej programu SQL Server.
   
    b.  Jeśli dane nie zostały jeszcze zapisane, pobierz pliki danych z kontenera magazynu platformy Azure do folderu local-VM.
   
    d.  Uruchom program SQL Server Management Studio.
   
    d.  Tworzenie tabel bazy danych i tabel docelowych.
   
    e.  Użyj jednej z metod importu zbiorczego, aby załadować dane.
   
    f.  Jeśli sprzężenia tabeli są wymagane, należy utworzyć indeksy, aby przyspieszyć sprzężenia.
   
   > [!NOTE]
   > W celu szybszego ładowania dużych rozmiarów danych zaleca się tworzenie tabel podzielonych na partycje i zbiorcze importowanie danych równolegle. Aby uzyskać więcej informacji, zobacz [Importowanie danych równoległych do tabel podzielonych na partycje SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Eksploruj dane, twórz funkcje w razie potrzeby. Funkcje nie muszą być materialized w tabelach bazy danych. Zanotuj tylko kwerendę niezbędną do ich utworzenia.
1. Zdecyduj o rozmiarze próbki danych, jeśli jest to konieczne i/lub pożądane.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytuj dane bezpośrednio z programu SQL Server przy użyciu modułu [Importuj dane.][import-data] Wklej niezbędną kwerendę, która wyodrębnia pola, tworzy funkcje i przykłada dane, jeśli jest to potrzebne bezpośrednio w kwerendzie [Importuj dane.][import-data]
1. Prosty przepływ eksperymentu usługi Azure Machine Learning, począwszy od przekazanego zestawu danych

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scenariusz \#6: Duży zestaw danych w lokalnej bazie danych programu SQL Server, kierowanie na program SQL Server na maszynie wirtualnej platformy Azure
![Duży bazę danych SQL on-prem do bazy danych SQL na platformie Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer SQL Server / IPython Notebook server)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym serwerem SQL Server i serwerem notesu IPython.
1. Użyj jednej z metod eksportowania danych, aby wyeksportować dane z programu SQL Server do zrzutu plików.
   
   > [!NOTE]
   > Jeśli zdecydujesz się przenieść wszystkie dane z lokalnej bazy danych, alternatywna (szybsza) metoda przeniesienia pełnej bazy danych do wystąpienia programu SQL Server na platformie Azure. Pomiń kroki, aby wyeksportować dane, utworzyć bazę danych i załadować/zaimportować dane do docelowej bazy danych i postępuj zgodnie z alternatywną metodą.
   > 
   > 
1. Przekaż pliki zrzutu do kontenera usługi Azure Storage.
1. Załaduj dane do bazy danych programu SQL Server uruchomionej na maszynie wirtualnej platformy Azure.
   
   a.  Zaloguj się do maszyny Wirtualnej programu SQL Server.
   
   b.  Pobieranie plików danych z kontenera usługi Azure Storage do folderu lokalnej maszyny Wirtualnej.
   
   d.  Uruchom program SQL Server Management Studio.
   
   d.  Tworzenie tabel bazy danych i tabel docelowych.
   
   e.  Użyj jednej z metod importu zbiorczego, aby załadować dane.
   
   f.  Jeśli sprzężenia tabeli są wymagane, należy utworzyć indeksy, aby przyspieszyć sprzężenia.
   
   > [!NOTE]
   > Aby przyspieszyć ładowanie dużych rozmiarów danych, należy utworzyć tabele podzielone na partycje i zbiorczo importować dane równolegle. Aby uzyskać więcej informacji, zobacz [Importowanie danych równoległych do tabel podzielonych na partycje SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Eksploruj dane, twórz funkcje w razie potrzeby. Funkcje nie muszą być materialized w tabelach bazy danych. Zanotuj tylko kwerendę niezbędną do ich utworzenia.
1. Zdecyduj o rozmiarze próbki danych, jeśli jest to konieczne i/lub pożądane.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytuj dane bezpośrednio z programu SQL Server przy użyciu modułu [Importuj dane.][import-data] Wklej niezbędną kwerendę, która wyodrębnia pola, tworzy funkcje i przykłada dane, jeśli jest to potrzebne bezpośrednio w kwerendzie [Importuj dane.][import-data]
1. Prosty przepływ eksperymentu usługi Azure Machine Learning, począwszy od przekazanego zestawu danych.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatywna metoda kopiowania pełnej bazy danych z lokalnego programu SQL Server do bazy danych SQL Azure
![Odłącz lokalny baz danych i dołącz do bazy danych SQL na platformie Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer SQL Server / IPython Notebook server)
Aby replikować całą bazę danych programu SQL Server w maszynie Wirtualnej programu SQL Server, należy skopiować bazę danych z jednej lokalizacji/serwera na inny, przy założeniu, że baza danych może zostać tymczasowo przeniesiona do trybu offline. Możesz użyć Eksploratora obiektów programu SQL Server Management Studio lub użyć równoważnych poleceń Transact-SQL.

1. Odłącz bazę danych w lokalizacji źródłowej. Aby uzyskać więcej informacji, zobacz [Odłączaj bazę danych](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. W oknie Eksploratora Windows lub wiersza polecenia systemu Windows skopiuj odłączony plik bazy danych lub pliki i pliki dziennika do lokalizacji docelowej na maszynie Wirtualnej programu SQL Server na platformie Azure.
1. Dołącz skopiowane pliki do docelowego wystąpienia programu SQL Server. Aby uzyskać więcej informacji, zobacz [Dołączanie bazy danych](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Przenoszenie bazy danych przy użyciu funkcji odłączania i dołączania (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scenariusz \#7: Duże zbiory danych w plikach lokalnych, docelowa baza danych hive w klastrach usługi Azure HDInsight Hadoop
![Duże zbiory danych w lokalnym docelowym uli][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: klaster usługi Azure HDInsight i maszyna wirtualna platformy Azure (serwer notesu IPython)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym serwerem notesu IPython.
1. Tworzenie klastra usługi Azure HDInsight Hadoop.
1. (Opcjonalnie) Wstępne przetwarzanie i czyszczenie danych.
   
   a.  Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z platformy Azure
   
       blobs.
   
   b.  Przekształć dane do oczyszczonego formularza tabelaryczne, jeśli to konieczne.
   
   d.  Zapisywanie danych w plikach lokalnych maszyn wirtualnych (notebook IPython jest uruchomiony na maszynie Wirtualnej, dyski lokalne odnoszą się do dysków maszyn wirtualnych).
1. Przekaż dane do domyślnego kontenera klastra Hadoop wybranego w kroku 2.
1. Załaduj dane do bazy danych hive w klastrze usługi Azure HDInsight Hadoop.
   
   a.  Zaloguj się do węzła głównego klastra Hadoop
   
   b.  Otwórz wiersz polecenia Hadoop.
   
   d.  Wprowadź katalog główny gałęzi za `cd %hive_home%\bin` pomocą polecenia w wierszu polecenia Hadoop.
   
   d.  Uruchom zapytania hive, aby utworzyć bazę danych i tabele i załadować dane z magazynu obiektów blob do tabel gałęzi.
   
   > [!NOTE]
   > Jeśli dane są duże, użytkownicy mogą utworzyć tabelę hive z partycjami. Następnie użytkownicy mogą `for` użyć pętli w wierszu polecenia Hadoop w węźle głównym, aby załadować dane do tabeli Hive podzielonej na partycje.
   > 
   > 
1. Eksploruj dane i twórz funkcje zgodnie z potrzebami w wierszu polecenia Hadoop. Funkcje nie muszą być materialized w tabelach bazy danych. Zanotuj tylko kwerendę niezbędną do ich utworzenia.
   
   a.  Zaloguj się do węzła głównego klastra Hadoop
   
   b.  Otwórz wiersz polecenia Hadoop.
   
   d.  Wprowadź katalog główny gałęzi za `cd %hive_home%\bin` pomocą polecenia w wierszu polecenia Hadoop.
   
   d.  Uruchom zapytania hive w wierszu polecenia Hadoop w węźle głównym klastra Hadoop, aby eksplorować dane i w razie potrzeby tworzyć funkcje.
1. W razie potrzeby i/lub pożądane, próbki danych, aby zmieścić się w usłudze Azure Machine Learning Studio.
1. Zaloguj się do [usługi Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczyt danych bezpośrednio z `Hive Queries` modułu [Importuj dane.][import-data] Wklej niezbędną kwerendę, która wyodrębnia pola, tworzy funkcje i przykłada dane, jeśli jest to potrzebne bezpośrednio w kwerendzie [Importuj dane.][import-data]
1. Prosty przepływ eksperymentu usługi Azure Machine Learning, począwszy od przekazanego zestawu danych.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Drzewo decyzyjne do wyboru scenariusza
---
Na poniższym diagramie podsumowano scenariusze opisane powyżej oraz zaawansowane wybory procesów i technologii, które zadają Dostęp do każdego z wyszczególnionych scenariuszy. Przetwarzanie danych, eksploracja, inżynieria funkcji i próbkowanie mogą odbywać się w jednej lub więcej metodach/środowisku — w środowiskach źródłowych, pośrednich i/lub docelowych — i mogą przebiegać iteracyjne w razie potrzeby. Diagram służy tylko jako ilustracja niektórych możliwych przepływów i nie zapewnia wyczerpujące wyliczenie.

![Przykładowe scenariusze przewodników procesowych DS][8]

### <a name="advanced-analytics-in-action-examples"></a>Zaawansowane analizy w przykładach akcji
Aby uzyskać kompleksowe wskazówki dotyczące usługi Azure Machine Learning, które wykorzystują proces i technologię zaawansowanej analizy przy użyciu publicznych zestawów danych, zobacz:

* [Proces nauki o danych zespołu w akcji: przy użyciu programu SQL Server](sql-walkthrough.md).
* [Proces nauki o danych zespołu w akcji: korzystanie z klastrów HDInsight Hadoop](hive-walkthrough.md).

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
