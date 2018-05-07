---
title: Określenie scenariuszy zaawansowana analityka dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Wybierz odpowiednie scenariusze robić, zaawansowane analizy predykcyjnej z procesem nauki danych Team.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 53aecc1e-5089-42cf-8d44-77678653f92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 7b1b8f00e109776a3a5d3cc8c2224e8160b85704
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning
W tym artykule przedstawiono różne źródła danych przykładowych i scenariusze docelowe, które są obsługiwane przez [zespołu danych nauki procesu (TDSP)](overview.md). TDSP dostarcza systematyczne rozwiązanie dla zespołów współpracować nad tworzenia aplikacji inteligentnego. Scenariusze przedstawionych w tym miejscu przedstawiono opcje dostępne w przepływie pracy przetwarzania danych, które są zależne od właściwości danych, lokalizacji źródłowej i repozytoriów docelowego na platformie Azure.

**Drzewa decyzyjnego** dla wybranie przykładowe scenariusze, które jest odpowiednie dla danych i cel są prezentowane w ostatniej sekcji.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Każdy z poniższych sekcji przedstawia przykładowy scenariusz. Dla każdego scenariusza nauki danych lub zaawansowana analityka przepływu i obsługi zasobów platformy Azure są wyświetlane.

> [!NOTE]
> **Wszystkie z poniższych scenariuszy musisz:**
> <br/>
> 
> * [Tworzenie konta magazynu](../../storage/common/storage-create-storage-account.md)
>   <br/>
> * [Utwórz obszar roboczy usługi Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scenariusz \#1: mały, aby średnia tabelarycznym zestawu danych w lokalnych plikach
![Małych i średnich plików lokalnych][1]

#### <a name="additional-azure-resources-none"></a>Dodatkowe zasoby platformy Azure: Brak
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
2. Przekaż zestawu danych.
3. Tworzenie przepływu eksperymentu uczenia maszynowego Azure począwszy przekazane zestawów danych.

## <a name="smalllocalprocess"></a>Scenariusz \#2: małe średnia DataSet lokalnych plików, które wymagają przetworzenia
![Małych i średnich lokalnych plików z przetwarzaniem][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyny wirtualnej platformy Azure (notesu IPython server)
1. Utwórz maszynę wirtualną platformy Azure, systemem IPython notesu.
2. Przekazywanie danych do kontenera magazynu systemu Azure.
3. Wstępnie przetworzyć i czyszczenia danych w notesie IPython, uzyskiwanie dostępu do danych z kontenera magazynu systemu Azure.
4. Przekształć dane czyszczenia tabelarycznej.
5. Zapisz przekształcone dane w obiektach blob Azure.
6. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
7. Odczytywanie danych z obiekty BLOB platformy Azure przy użyciu [i zaimportuj dane] [ import-data] modułu.
8. Tworzenie przepływu eksperymentu uczenia maszynowego Azure począwszy pozyskiwane zestawów danych.

## <a name="largelocal"></a>Scenariusz \#3: dużego zestawu danych lokalnych plików przeznaczonych dla obiektów blob Azure
![Dużych plików lokalnych][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyny wirtualnej platformy Azure (notesu IPython server)
1. Utwórz maszynę wirtualną platformy Azure, systemem IPython notesu.
2. Przekazywanie danych do kontenera magazynu systemu Azure.
3. Wstępnie przetworzyć i czyszczenia danych w notesie IPython, dostęp do danych z obiektów blob Azure.
4. Przekształcanie danych czyszczenia tabelarycznej, w razie potrzeby.
5. Eksplorowanie danych i tworzenie funkcji zgodnie z potrzebami.
6. Wyodrębnij przykładowych danych w małych i średnich.
7. Zapisz próbki danych w obiektach blob Azure.
8. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Odczytywanie danych z obiekty BLOB platformy Azure przy użyciu [i zaimportuj dane] [ import-data] modułu.
10. Tworzenie przepływu eksperymentu uczenia maszynowego Azure począwszy pozyskiwane zestawów danych.

## <a name="smalllocaltodb"></a>Scenariusz \#4: mały, aby średnia zestawu danych lokalnych plików przeznaczonych dla programu SQL Server w maszynie wirtualnej platformy Azure
![Mały, aby średnia lokalnych plików do bazy danych SQL na platformie Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyny wirtualnej platformy Azure (SQL Server / Notes IPython serwera)
1. Utwórz maszynę wirtualną platformy Azure z programu SQL Server + IPython notesu.
2. Przekazywanie danych do kontenera magazynu systemu Azure.
3. Wstępnie przetworzyć i czyszczenia danych w kontenerze magazynu platformy Azure przy użyciu IPython notesu.
4. Przekształcanie danych czyszczenia tabelarycznej, w razie potrzeby.
5. Zapisywanie danych w plikach lokalnej maszyny Wirtualnej (notesu IPython działa na maszynie Wirtualnej, dysków lokalnych można znaleźć dysków maszyny Wirtualnej).
6. Ładowanie danych do bazy danych programu SQL Server uruchomiony na maszynie Wirtualnej platformy Azure.
   
   Opcja \#1: użycie programu SQL Server Management Studio.
   
   * Logowanie do programu SQL Server maszyny Wirtualnej
   * Uruchom program SQL Server Management Studio.
   * Tworzenie tabel bazy danych i obiekt docelowy.
   * Użyj jednej z zbiorczego zaimportować metod do ładowania danych z plików lokalnej maszyny Wirtualnej.
   
   Opcja \#2: przy użyciu IPython notesu — nie zaleca się dla średnich i większych zestawów danych
   
   <!-- -->    
   * Dostęp do serwera SQL na maszynie Wirtualnej, należy użyć ciągu połączenia ODBC.
   * Tworzenie tabel bazy danych i obiekt docelowy.
   * Użyj jednej z zbiorczego zaimportować metod do ładowania danych z plików lokalnej maszyny Wirtualnej.
7. Eksploruj dane, należy utworzyć funkcje zgodnie z potrzebami. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj, tylko niezbędne zapytanie, aby je utworzyć.
8. Podejmowanie decyzji o rozmiarze przykładowych danych, jeśli wymagane lub pożądane.
9. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
10. Odczytywanie danych z bezpośrednio za pomocą programu SQL Server [i zaimportuj dane] [ import-data] modułu. Wklej zapytanie niezbędne, który wyodrębnia pól i tworzy funkcje oraz przykłady danych, w razie potrzeby bezpośrednio w [i zaimportuj dane] [ import-data] zapytania.
11. Tworzenie przepływu eksperymentu uczenia maszynowego Azure począwszy pozyskiwane zestawów danych.

## <a name="largelocaltodb"></a>Scenariusz \#5: dużego zestawu danych w lokalnych plikach, docelowa programu SQL Server w maszynie Wirtualnej platformy Azure
![Dużych plików lokalnych do bazy danych SQL na platformie Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyny wirtualnej platformy Azure (SQL Server / Notes IPython serwera)
1. Utwórz maszynę wirtualną platformy Azure z programu SQL Server oraz serwer IPython notesu.
2. Przekazywanie danych do kontenera magazynu systemu Azure.
3. (Opcjonalnie) Wstępnie przetworzyć i czyszczenia danych.
   
   a.  Wstępnie przetworzyć i czyszczenia danych w notesie IPython, uzyskiwanie dostępu do danych z platformy Azure
   
       blobs.
   
   b.  Przekształcanie danych czyszczenia tabelarycznej, w razie potrzeby.
   
   c.  Zapisywanie danych w plikach lokalnej maszyny Wirtualnej (notesu IPython działa na maszynie Wirtualnej, dysków lokalnych można znaleźć dysków maszyny Wirtualnej).
4. Ładowanie danych do bazy danych programu SQL Server uruchomiony na maszynie Wirtualnej platformy Azure.
   
   a.  Zaloguj się do programu SQL Server maszyny Wirtualnej.
   
   b.  Jeśli nie już zapisany na danych, pobieranie plików danych z platformy Azure
   
       storage container to local-VM folder.
   
   c.  Uruchom program SQL Server Management Studio.
   
   d.  Tworzenie tabel bazy danych i obiekt docelowy.
   
   e.  Użyj jednej z zbiorczego zaimportować metod do ładowania danych.
   
   f.  Jeśli sprzężeń tabel są wymagane, należy utworzyć indeksy w celu przyspieszenia sprzężenia.
   
   > [!NOTE]
   > Szybkość wczytywania rozmiary dużej ilości danych, zalecane jest tworzenie partycjonowane tabele i zbiorczego importowania danych równolegle. Aby uzyskać więcej informacji, zobacz [równoległych importowanie danych do tabel na partycje SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Eksploruj dane, należy utworzyć funkcje zgodnie z potrzebami. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj, tylko niezbędne zapytanie, aby je utworzyć.
6. Podejmowanie decyzji o rozmiarze przykładowych danych, jeśli wymagane lub pożądane.
7. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Odczytywanie danych z bezpośrednio za pomocą programu SQL Server [i zaimportuj dane] [ import-data] modułu. Wklej zapytanie niezbędne, który wyodrębnia pól i tworzy funkcje oraz przykłady danych, w razie potrzeby bezpośrednio w [i zaimportuj dane] [ import-data] zapytania.
9. Proste przepływu eksperymentu uczenia maszynowego Azure, począwszy od dataset przekazany

## <a name="largedbtodb"></a>Scenariusz \#6: dużego zestawu danych w programie SQL Server bazy danych lokalnych, przeznaczonych dla programu SQL Server w maszynie wirtualnej platformy Azure
![Duże SQL bazy danych lokalnych do bazy danych SQL na platformie Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyny wirtualnej platformy Azure (SQL Server / Notes IPython serwera)
1. Utwórz maszynę wirtualną platformy Azure z programu SQL Server oraz serwer IPython notesu.
2. Użyj jednej z danych wyeksportować metod, aby wyeksportować dane z programu SQL Server do plików zrzutu.
   
   > [!NOTE]
   > Jeśli zdecydujesz się przeniesienie wszystkich danych z bazy danych lokalnych, to alternatywna metoda (szybsze), aby przenieść pełnej bazy danych do wystąpienia programu SQL Server na platformie Azure. Pomiń kroki, aby wyeksportować dane, Utwórz bazę danych i obciążenia/importowanie danych do docelowej bazy danych i wykonaj alternatywna metoda.
   > 
   > 
3. Przekazywanie plików zrzutu do kontenera magazynu systemu Azure.
4. Ładowanie danych do bazy danych programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure.
   
   a.  Zaloguj się do programu SQL Server maszyny Wirtualnej.
   
   b.  Pobieranie plików danych z kontenera magazynu Azure do folderu lokalnego wirtualna.
   
   c.  Uruchom program SQL Server Management Studio.
   
   d.  Tworzenie tabel bazy danych i obiekt docelowy.
   
   e.  Użyj jednej z zbiorczego zaimportować metod do ładowania danych.
   
   f.  Jeśli sprzężeń tabel są wymagane, należy utworzyć indeksy w celu przyspieszenia sprzężenia.
   
   > [!NOTE]
   > Szybsze ładowanie rozmiary dużej ilości danych, Utwórz partycjonowane tabele i do zbiorczego importowania danych równolegle. Aby uzyskać więcej informacji, zobacz [równoległych importowanie danych do tabel na partycje SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Eksploruj dane, należy utworzyć funkcje zgodnie z potrzebami. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj, tylko niezbędne zapytanie, aby je utworzyć.
6. Podejmowanie decyzji o rozmiarze przykładowych danych, jeśli wymagane lub pożądane.
7. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Odczytywanie danych z bezpośrednio za pomocą programu SQL Server [i zaimportuj dane] [ import-data] modułu. Wklej zapytanie niezbędne, który wyodrębnia pól i tworzy funkcje oraz przykłady danych, w razie potrzeby bezpośrednio w [i zaimportuj dane] [ import-data] zapytania.
9. Począwszy od dataset przekazany prosty przepływ eksperymentu uczenia maszynowego Azure.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatywna metoda kopiowania pełnej bazy danych z lokalnego serwera SQL do bazy danych SQL Azure
![Odłączanie lokalnej bazy danych i Dołącz do bazy danych SQL na platformie Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyny wirtualnej platformy Azure (SQL Server / Notes IPython serwera)
Aby replikować całą bazę danych programu SQL Server w maszyną Wirtualną programu SQL Server, należy skopiować bazę danych z jednej lokalizacji/serwera na inny, przy założeniu, że bazy danych może być podjęta tymczasowo w trybie offline. Możesz to zrobić w Eksploratorze obiektów SQL Server Management Studio lub za pomocą równoważnych poleceń języka Transact-SQL.

1. Odłącz bazę danych w lokalizacji źródłowej. Aby uzyskać więcej informacji, zobacz [odłączyć bazy danych](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
2. W oknie Eksploratora Windows lub wiersza polecenia systemu Windows skopiuj plik odłączono bazę danych lub pliki i pliku dziennika lub pliki do lokalizacji docelowej na maszynie Wirtualnej serwera SQL na platformie Azure.
3. Dołącz skopiowanych plików w wystąpieniu programu SQL Server docelowym. Aby uzyskać więcej informacji, zobacz [dołączyć bazę danych](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Przenoszenie bazy danych przy użyciu odłączyć i dołączyć (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scenariusz \#7: danych Big data w lokalnych plikach docelowa baza danych gałęzi w klastrach usługi Azure HDInsight Hadoop
![Dane big data w celu lokalnego gałęzi][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: klastra usługi Hadoop w usłudze Azure HDInsight i maszyny wirtualnej platformy Azure (notesu IPython server)
1. Utwórz maszynę wirtualną platformy Azure, usługami IPython notesu.
2. Tworzenie klastra usługi Azure HDInsight Hadoop.
3. (Opcjonalnie) Wstępnie przetworzyć i czyszczenia danych.
   
   a.  Wstępnie przetworzyć i czyszczenia danych w notesie IPython, uzyskiwanie dostępu do danych z platformy Azure
   
       blobs.
   
   b.  Przekształcanie danych czyszczenia tabelarycznej, w razie potrzeby.
   
   c.  Zapisywanie danych w plikach lokalnej maszyny Wirtualnej (notesu IPython działa na maszynie Wirtualnej, dysków lokalnych można znaleźć dysków maszyny Wirtualnej).
4. Przekazywanie danych do domyślnego kontenera klastra usługi Hadoop wybranej w kroku 2.
5. Ładowanie danych do bazy danych klastra usługi Azure HDInsight Hadoop Hive.
   
   a.  Zaloguj się do węzła głównego klastra usługi Hadoop
   
   b.  Otwórz okno wiersza polecenia usługi Hadoop.
   
   c.  Wprowadź katalog główny Hive za pomocą polecenia `cd %hive_home%\bin` w wierszu polecenia Hadoop.
   
   d.  Uruchamianie zapytań Hive, można utworzyć bazy danych i tabele i ładowanie danych z magazynu obiektów blob, tabel, w gałęzi.
   
   > [!NOTE]
   > W przypadku dużych danych, użytkownicy mogą tworzyć tabeli Hive z partycjami. Następnie użytkownicy mogą używać `for` pętli w Hadoop wiersza polecenia w węźle głównym ładowanie danych do tabeli Hive partycjonowanego partycji.
   > 
   > 
6. Eksplorowanie danych i tworzenie funkcji zgodnie z potrzebami w wierszu polecenia Hadoop. Należy pamiętać, że funkcje nie muszą być zmaterializowany w tabelach bazy danych. Pamiętaj, tylko niezbędne zapytanie, aby je utworzyć.
   
   a.  Zaloguj się do węzła głównego klastra usługi Hadoop
   
   b.  Otwórz okno wiersza polecenia usługi Hadoop.
   
   c.  Wprowadź katalog główny Hive za pomocą polecenia `cd %hive_home%\bin` w wierszu polecenia Hadoop.
   
   d.  Uruchamianie zapytań Hive w wierszu polecenia Hadoop na węzła głównego klastra usługi Hadoop do danych i tworzenie funkcji zgodnie z potrzebami.
7. Jeśli wymagane lub pożądane, przykładowe dane, aby zmieścić ją w usłudze Azure Machine Learning Studio.
8. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Odczytywanie danych bezpośrednio z `Hive Queries` przy użyciu [i zaimportuj dane] [ import-data] modułu. Wklej zapytanie niezbędne, który wyodrębnia pól i tworzy funkcje oraz przykłady danych, w razie potrzeby bezpośrednio w [i zaimportuj dane] [ import-data] zapytania.
10. Począwszy od dataset przekazany prosty przepływ eksperymentu uczenia maszynowego Azure.

## <a name="decisiontree"></a>Drzewo decyzyjne dotyczące wyboru scenariusza
- - -
Poniższy diagram przedstawia opisanych powyżej scenariuszy i zaawansowane procesu analizy i technologii dokonanej prowadzące do każdego z wyszczególnione scenariuszy. Uwaga: przetwarzanie danych, eksploracji engineering funkcji i próbkowanie może być umieścić w metody na środowiskową — w źródle, pośrednie, lub środowiska docelowego — i można kontynuować wielokrotnie powtarzane, zgodnie z potrzebami. Diagram tylko służy jako ilustrację niektóre możliwe przepływów i nie zapewnia kompleksowe wyliczenia.

![Przykładowe DS procesu wskazówki scenariusze][8]

### <a name="advanced-analytics-in-action-examples"></a>Zaawansowane analizy w akcji przykłady
Przegląd usługi Azure Machine Learning end-to-end procesu zaawansowane analizy i technologii przy użyciu publicznego zestawów danych zobacz:

* [Zespół proces analizy danych w działaniu: przy użyciu programu SQL Server](sql-walkthrough.md).
* [Zespół proces analizy danych w działaniu: z użyciem klastrów usługi HDInsight Hadoop](hive-walkthrough.md).

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
