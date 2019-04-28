---
title: Oprogramowanie Apache Phoenix w HDInsight — usługa Azure HDInsight
description: ''
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 7d9aafeb920eab7f6a87061a135bf2e464add436
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763642"
---
# <a name="apache-phoenix-in-hdinsight"></a>Oprogramowanie Apache Phoenix w usłudze HDInsight

[Apache Phoenix](https://phoenix.apache.org/) typu open source, warstwy równoległe na wielką skalę relacyjna baza danych oparta na [bazy danych Apache HBase](hbase/apache-hbase-overview.md). Phoenix pozwala korzystać z zapytań przypominający SQL za pośrednictwem HBase. Phoenix używa sterowników JDBC poniżej, aby umożliwić użytkownikom tworzenie, usuwanie, alter SQL tabel, indeksów, widoków i sekwencje i upsert wierszy indywidualnie i zbiorczo. Phoenix używa noSQL natywnej kompilacji, zamiast używać MapReduce do kompilowania zapytań, umożliwiając tworzenie aplikacji o małych opóźnieniach w bazie danych HBase. Phoenix dodaje koprocesory umożliwiają uruchamianie kodu dostarczane przez klienta w przestrzeni adresowej serwera, wykonywanie kodu wspólnie przechowywane z danymi. To podejście minimalizuje transfer danych klienta/serwera.

Apache Phoenix otwiera zapytania o dane big data do osoby niebędące deweloperami, którzy mogą używać składni podobnego do SQL zamiast programowania. Phoenix jest wysoce zoptymalizowane na potrzeby bazy danych HBase, w przeciwieństwie do innych narzędzi takich jak [Apache Hive](hadoop/hdinsight-use-hive.md) i Apache Spark SQL. Korzyści dla deweloperów to zapisuje wysoce wydajnych zapytań przy użyciu znacznie mniejszej ilości kodu.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Po przesłaniu zapytania SQL Phoenix kompiluje zapytania w celu wywołania natywne bazy danych HBase i uruchamia skanowanie (lub planu) w sposób równoległy do optymalizacji. Ta warstwa abstrakcji zwalnia deweloperom pisać zadań MapReduce, zamiast tego Skoncentruj się na logice biznesowej i przepływ pracy aplikacji dotyczące magazynu danych big data firmy Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Optymalizacja wydajności zapytań i inne funkcje

Apache Phoenix dodaje kilka ulepszeń wydajności i funkcji do zapytań bazy danych HBase.

### <a name="secondary-indexes"></a>Indeksy pomocnicze

Baza danych HBase ma jednego indeksu, który jest leksykograficznie sortowane według klucza podstawowego wiersza. Te rekordy można uzyskać tylko za pośrednictwem klucza wiersza. Uzyskiwanie dostępu do rekordów za pomocą dowolnej kolumny, innego niż klucz wiersza wymaga skanowania wszystkich danych podczas stosowania wymagany filtr. W indeksie pomocniczym kolumny lub wyrażenia, które są indeksowane formularza klucza wiersz alternatywny, umożliwiając wyszukiwań i zakresu skanowania indeksu.

Utwórz indeks pomocniczy przy użyciu `CREATE INDEX` polecenia:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Takie podejście może przynieść do znacznego zwiększenia wydajności przez wykonywanie kwerend indeksowane pojedynczej. Ten typ pomocniczy indeks jest **obejmujące indeksu**, zawierającą wszystkie kolumny uwzględnione w zapytaniu. W związku z tym wyszukiwanie w tabeli nie jest wymagane, a indeks spełniający całe zapytanie.

### <a name="views"></a>Widoki

Phoenix widoki umożliwiają pokonanie ograniczenie bazy danych HBase, gdzie wydajność zaczyna obniżona, gdy tworzysz więcej niż około 100 tabel fizycznych. Widoki Phoenix włączyć wielu *tabele wirtualne* udostępnianie jednej tabeli HBase fizycznych źródłowej.

Tworzenie widoku Phoenix przypomina przy użyciu standardowej składni widoku SQL. Jedną różnicaą jest to zdefiniować kolumn dla widoku, oprócz kolumn dziedziczone z jego tabeli podstawowej. Można także dodać nowe `KeyValue` kolumn.

Na przykład, w tym miejscu jest fizyczny tabelę o nazwie `product_metrics` przy użyciu następujących definicji:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Zdefiniuj widok za pośrednictwem tej tabeli z kolumnami dodatkowe:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Aby dodać później więcej kolumn, użyj `ALTER VIEW` instrukcji.

### <a name="skip-scan"></a>Pomiń skanowania

Pomiń skanowania używa co najmniej jedną kolumnę indeksu złożonego w celu odnalezienia unikatowe wartości. W przeciwieństwie do zakresu skanowania, Pomiń skanowania implementuje wierszy wewnątrz skanowania, którego można [zwiększona wydajność](https://phoenix.apache.org/performance.html#Skip-Scan). Podczas skanowania, pierwsza wartość dopasowane wraz z indeks zostanie pominięty, aż do znalezienia następnej wartości.

Skanowanie Pomiń używa `SEEK_NEXT_USING_HINT` wyliczenie filtrów bazy danych HBase. Za pomocą `SEEK_NEXT_USING_HINT`, skanowania Pomiń śledzi informacje o który zestaw kluczy lub zakresy kluczy, są wyszukiwane w każdej kolumnie. Pomiń skanowania, a następnie pobiera klucz, który został przekazany do niego podczas obliczania wartości filtru i określa, czy jest jedną z kombinacji. W przeciwnym razie Pomiń skanowania ocenia kluczem najwyższego dalej, aby przejść do.

### <a name="transactions"></a>Transakcje

Gdy baza danych HBase zapewnia transakcji na poziomie wiersza, Phoenix integruje się z [Tephra](https://tephra.io/) dodanie obsługi transakcji wielu wierszy i między tabelami przy użyciu pełnej [ACID](https://en.wikipedia.org/wiki/ACID) semantyki.

Jako przy użyciu tradycyjnych transakcji SQL, transakcji przez Menedżera transakcji Phoenix umożliwiają upewnij się, że pojedynczej Atomowej jednostki danych jest pomyślnie upserted, wycofywanie transakcji w przypadku niepowodzenia operacji upsert w dowolnej tabeli włączone transakcji.

Aby włączyć Phoenix transakcji, zobacz [dokumentacji transakcji Apache Phoenix](https://phoenix.apache.org/transactions.html).

Aby utworzyć nową tabelę z transakcjami włączona, ustaw `TRANSACTIONAL` właściwości `true` w `CREATE` instrukcji:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Aby zmodyfikować istniejącą tabelę transakcyjnej, należy użyć tej samej właściwości w `ALTER` instrukcji:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Nie można przełączyć transakcyjna tabeli wstecz do są nietransakcyjnej.

### <a name="salted-tables"></a>Solone tabel

*Region serwera hotspotting* mogą wystąpić podczas zapisywania rekordów bazy danych HBase przy użyciu kluczy sekwencyjne. Jeśli masz wiele serwerów w regionie w klastrze, zapisów wszystkie pojawiają się na co najmniej jeden. Ta zawartość tworzy problem hotspotting where, zamiast obciążenia zapisu są dystrybuowane na wszystkich serwerach dostępny region z co najmniej jeden jest obsługi obciążenia. Ponieważ w każdym regionie istnieje wstępnie zdefiniowany maksymalny rozmiar, gdy region osiągnie ten limit rozmiaru, zostanie ona podzielona na dwa regiony małe. Kiedy tak się stanie, jeden z tych nowych regionów ma nowe rekordy, staje się nowy punkt aktywny.

Aby uniknąć tego problemu i osiągnąć lepszą wydajność, wstępnie Podziel tabele, aby wszystkie serwery regionów są równie używane. Udostępnia Phoenix *ciągu inicjującego tabel*, w sposób niewidoczny dla użytkownika Dodawanie dodającego ciąg inicjujący bajtów do klucza wiersza dla danej tabeli. Tabela jest wstępnie dzielenie na granice bajtowe ziarna, aby upewnić się, dystrybucji obciążenia równe między serwerami regionu w początkowej fazie tabeli. To podejście dystrybuuje obciążenia zapisu dla wszystkich serwerów dostępny region, poprawy zapisu oraz wydajność odczytu. Aby soli tabeli, należy określić `SALT_BUCKETS` tabeli właściwości po utworzeniu tabeli:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Włącz i dostrajanie Phoenix za pomocą systemu Apache Ambari

Klaster HDInsight HBase obejmuje [interfejsu użytkownika Ambari](hdinsight-hadoop-manage-ambari.md) do wprowadzania zmian w konfiguracji.

1. Aby włączyć lub wyłączyć Phoenix i kontrolować ustawienia limitu czasu zapytania firmy Phoenix, zaloguj się do Interfejsu sieci Web Ambari (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) przy użyciu poświadczeń użytkownika usługi Hadoop.

2. Wybierz **HBase** wybierz z listy usług w menu po lewej stronie, następnie **Configs** kartę.

    ![Konfiguracja bazy danych HBase systemu Ambari](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Znajdź **Phoenix SQL** sekcji konfiguracji, aby włączyć lub wyłączyć phoenix i ustawić limitu czasu zapytania.

    ![Sekcja konfiguracji SQL Phoenix systemu Ambari](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Zobacz także

* [Apache Phoenix za pomocą klastrów HBase opartych na systemie Linux na platformie HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
