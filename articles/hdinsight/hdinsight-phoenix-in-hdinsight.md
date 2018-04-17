---
title: Apache Phoenix w usłudze HDInsight — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: ''
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5d96b5656881815a82c89e0d159ba2bf556946b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="apache-phoenix-in-hdinsight"></a>Oprogramowanie Apache Phoenix w usłudze HDInsight

[Apache Phoenix](http://phoenix.apache.org/) jest typu open source, warstwa równoległemu relacyjnej bazy danych, w oparciu [HBase](hbase/apache-hbase-overview.md). Phoenix umożliwia przy użyciu kwerend SQL przypominającej za pośrednictwem HBase. Phoenix używa sterowniki JDBC poniżej, aby umożliwić użytkownikom na tworzenie, usuwanie, alter SQL tabel, indeksów, widoków i sekwencji i upsert wierszy indywidualnie i zbiorczo. Phoenix używa noSQL natywnej kompilacji zamiast używać MapReduce do kompilowania zapytań, umożliwiające tworzenie aplikacji małe opóźnienia w bazie danych HBase. Phoenix dodaje koprocesory do obsługi uruchamiania kodu dostarczonych przez klienta w przestrzeni adresowej serwera, wykonywanie kodu kolokowane z danymi. Takie podejście minimalizuje transferu danych klienta i serwera.

Apache Phoenix otwiera kwerend danych big data z systemem innym niż deweloperów, którzy mogą używać składni SQL zamiast programowania. Phoenix stopniu zoptymalizowany dla bazy danych HBase, w przeciwieństwie do innych narzędzi takich jak [Hive](hadoop/hdinsight-use-hive.md) i Spark SQL. Korzyści dla deweloperów jest pisanie wysokiej wydajności zapytań o wiele mniej kodu.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Po przesłaniu zapytania SQL Phoenix kompiluje zapytania do bazy danych HBase macierzystych wywołań i uruchamia skanowanie (lub planujesz) równolegle do optymalizacji. Ta warstwa abstrakcji zwalnia deweloperowi zapisywania zadań MapReduce, zamiast tego Skoncentruj się na logiki biznesowej i przepływ pracy ich stosowania wokół jego Phoenix magazynu danych big data.

## <a name="query-performance-optimization-and-other-features"></a>Optymalizacja wydajności zapytania i inne funkcje

Apache Phoenix dodaje kilka ulepszeń wydajności i funkcji do kwerend bazy danych HBase.

### <a name="secondary-indexes"></a>Indeksów pomocniczych

HBase ma jeden indeks posortowanej lexicographically na klucz podstawowy wiersza. Te rekordy są dostępne tylko za pośrednictwem klucz wiersza. Uzyskiwanie dostępu do rekordów za pomocą dowolnej kolumny, innego niż klucz wiersza wymaga skanowanie wszystkich danych podczas stosowania wymaganego filtru. W indeksie dodatkowej kolumny lub wyrażenia, które są indeksowane formularza skanuje klucz wiersz alternatywny, dzięki czemu wyszukiwań i zakresu dla tego indeksu.

Utwórz pomocniczy indeks z `CREATE INDEX` polecenia:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Takie podejście umożliwia uzyskanie do znacznego zwiększenia wydajności za pośrednictwem wykonywanie kwerend indeksowane pojedynczej. Ten typ pomocniczy indeks jest **obejmujące indeksu**, zawierający wszystkie kolumny uwzględnione w zapytaniu. W związku z tym wyszukiwanie w tabeli nie jest wymagana i indeks spełnia całego zapytania.

### <a name="views"></a>Widoki

Widoki Phoenix umożliwiają pokonać ograniczenia HBase, gdy wydajność zaczyna spadać podczas tworzenia więcej niż około 100 tabel fizycznych. Widoki Phoenix włączyć wielu *tabele wirtualne* udostępnianie jednej tabeli HBase fizycznych źródłowej.

Tworzenie widoku Phoenix jest podobny do przy użyciu standardowej składni widoku SQL. Jeden różnica polega na tym, że mogą definiować kolumn dla widoku, oprócz kolumn odziedziczone po jego tabeli podstawowej. Można także dodać nowe `KeyValue` kolumn.

Na przykład, w tym miejscu jest fizycznej tabeli o nazwie `product_metrics` z następującej definicji:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definiowanie widoku za pośrednictwem tej tabeli z kolumnami dodatkowe:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Aby dodać więcej kolumn później, należy użyć `ALTER VIEW` instrukcji.

### <a name="skip-scan"></a>Pomiń skanowania

Pomiń skanowania użycie co najmniej jedną kolumnę złożonego indeksu w celu znalezienia różne wartości. W przeciwieństwie do skanowania zakresu, Pomiń skanowania implementuje wewnątrz wiersza skanowania, którego można [wyższą wydajność](http://phoenix.apache.org/performance.html#Skip-Scan). Podczas skanowania, pierwsza wartość dopasowane zostało pominięte wraz z indeksu, aż do znalezienia następnej wartości.

Używa skanowania Pomiń `SEEK_NEXT_USING_HINT` wyliczenie filtru HBase. Przy użyciu `SEEK_NEXT_USING_HINT`, Pomiń skanowania przechowuje informacje o który zestaw kluczy lub zakresy kluczy, przeszukiwany dla każdej kolumny. Pomiń skanowania, a następnie pobiera klucz, który został przekazany do niego podczas obliczania wartości filtru i określa, czy jest jedną z kombinacji. W przeciwnym razie Pomiń skanowania ocenia dalej najwyższy klawisz, aby przejść do.

### <a name="transactions"></a>Transakcje

Baza danych HBase zapewnia transakcji na poziomie wiersza, jednocześnie Phoenix integruje się z [Tephra](http://tephra.io/) dodanie obsługi transakcji między wiersza i tabeli z pełną [kwasu](https://en.wikipedia.org/wiki/ACID) semantyki.

Jako tradycyjnych transakcji SQL, transakcji przez Menedżera transakcji Phoenix umożliwiają upewnij się, że Atomowej jednostki danych pomyślnie upserted, wycofywanie transakcji w przypadku niepowodzenia operacji upsert w tabeli włączono transakcji.

Aby włączyć Phoenix transakcji, zobacz [dokumentacji transakcji Apache Phoenix](http://phoenix.apache.org/transactions.html).

Aby utworzyć nową tabelę z transakcjami włączona, ustaw `TRANSACTIONAL` właściwości `true` w `CREATE` instrukcji:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Aby zmodyfikować istniejącą tabelę transakcyjnej, należy użyć tej samej właściwości w `ALTER` instrukcji:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Transakcyjna tabeli nie można przełączyć do trwa nietransakcyjnej.

### <a name="salted-tables"></a>Solone tabel

*Region serwera hotspotting* mogą wystąpić podczas zapisywania rekordów z kluczami sekwencyjnych HBase. Jeśli masz wiele serwerów regionu w klastrze, Twoje zapisy wszystkich występujących w co najmniej jeden. Ta zawartość tworzy problem hotspotting where, zamiast obciążenia zapisu jest dystrybuowana do wszystkich serwerów dostępnych regionów, jeden z nich jest obsługi obciążenia. Ponieważ każdy region ma wstępnie zdefiniowanych maksymalny rozmiar, gdy region osiągnie ten limit rozmiaru, jest podzielony na dwa obszary mała. W takim przypadku przyjmuje jeden z tych nowych regionów nowych rekordów, staje się nowy punkt aktywny.

Aby uniknąć tego problemu i osiągnąć wyższą wydajność, wstępnie podziału tabele, aby wszystkie serwery obszaru jednakowo są używane. Udostępnia Phoenix *ciągu inicjującego tabel*, w sposób niewidoczny dla użytkownika Dodawanie solenie bajtów do klucza wiersza dla danej tabeli. Tabela jest wstępnie podzielone na ziarna bajtowych granicach zapewnienia rozkładu obciążenia równe między serwerami regionu w fazie początkowej tabeli. Takie podejście dystrybuuje obciążenie zapisu we wszystkich serwerów dostępnych regionów, poprawy zapisu i odczytu wydajności. Aby soli tabeli, należy określić `SALT_BUCKETS` tabeli właściwości po utworzeniu tabeli:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-ambari"></a>Włącz i dostrajania Phoenix z Ambari

Klaster HDInsight HBase obejmuje [interfejsu użytkownika narzędzia Ambari](hdinsight-hadoop-manage-ambari.md) do wprowadzania zmian w konfiguracji.

1. Aby włączyć lub wyłączyć Phoenix i kontrolować ustawienia limitu czasu zapytania w Phoenix, zaloguj się do Interfejsu sieci Web Ambari (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) przy użyciu poświadczeń użytkownika usługi Hadoop.

2. Wybierz **HBase** wybierz z listy usług w menu po lewej stronie, następnie **Configs** kartę.

    ![Ambari HBase konfiguracji](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Znajdź **Phoenix SQL** sekcji konfiguracji, aby włączyć lub wyłączyć phoenix i ustawić limit czasu zapytania.

    ![Sekcja konfiguracji Ambari Phoenix SQL](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Zobacz także

* [Apache Phoenix za pomocą klastrów HBase opartych na systemie Linux w usłudze HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
