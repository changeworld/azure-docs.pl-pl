---
title: Apache Phoenix w jakości HDInsight — usługa Azure HDInsight
description: Przegląd apache Phoenix
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435493"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix w usłudze Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) jest open source, masowo równolegle relacyjnej warstwy bazy danych zbudowany na [Apache HBase](hbase/apache-hbase-overview.md). Phoenix umożliwia używanie zapytań podobnych do SQL za pośrednictwem bazy HBase. Phoenix używa sterowników JDBC pod spodem, aby umożliwić użytkownikom tworzenie, usuwanie, zmienianie tabel SQL, indeksów, widoków i sekwencji oraz wierszy upsert indywidualnie i zbiorczo. Phoenix używa kompilacji natywnej noSQL, a nie przy użyciu MapReduce do kompilowania zapytań, umożliwiając tworzenie aplikacji o małym opóźnieniu na bazie HBase. Phoenix dodaje koprocesory do obsługi uruchomionego kodu dostarczonego przez klienta w przestrzeni adresowej serwera, wykonując kod współlokowany z danymi. Takie podejście minimalizuje transfer danych klient/serwer.

Apache Phoenix otwiera zapytania o duże zbiory danych dla osób niebędących deweloperami, którzy mogą używać składni podobnej do SQL, a nie programowania. Phoenix jest wysoce zoptymalizowany pod kątem bazy danych HBase, w przeciwieństwie do innych narzędzi, takich jak [Apache Hive](hadoop/hdinsight-use-hive.md) i Apache Spark SQL. Korzyści dla deweloperów jest pisanie kwerend wysoce wydajne z dużo mniej kodu.

Podczas przesyłania kwerendy SQL, Phoenix kompiluje kwerendę do wywołań natywnych HBase i uruchamia skanowanie (lub plan) równolegle do optymalizacji. Ta warstwa abstrakcji zwalnia dewelopera z pisania zadań MapReduce, aby skupić się zamiast tego na logice biznesowej i przepływie pracy ich aplikacji wokół magazynu dużych zbiorów danych firmy Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Optymalizacja wydajności kwerend i inne funkcje

Apache Phoenix dodaje kilka ulepszeń wydajności i funkcji do zapytań HBase.

### <a name="secondary-indexes"></a>Indeksy pomocnicze

HBase ma pojedynczy indeks, który jest leksykograficznie sortowane na klucz wiersza podstawowego. Te rekordy są dostępne tylko za pośrednictwem klucza wiersza. Uzyskiwanie dostępu do rekordów za pośrednictwem dowolnej kolumny innej niż klucz wiersza wymaga skanowania wszystkich danych podczas stosowania wymaganego filtru. W indeksie pomocniczym kolumny lub wyrażenia, które są indeksowane, tworzą alternatywny klucz wiersza, umożliwiając wyszukiwanie i skanowanie zakresu w tym indeksie.

Utwórz indeks pomocniczy za `CREATE INDEX` pomocą polecenia:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Takie podejście może przynieść znaczny wzrost wydajności podczas wykonywania zapytań z pojedynczym indeksem. Ten typ indeksu pomocniczego jest **indeksem obejmującym,** zawierającym wszystkie kolumny zawarte w kwerendzie. W związku z tym wyszukiwanie tabeli nie jest wymagane, a indeks spełnia całą kwerendę.

### <a name="views"></a>Widoki

Widoki Phoenix umożliwiają przezwyciężenie ograniczenia bazy danych HBase, w którym wydajność zaczyna się obniżać podczas tworzenia więcej niż 100 tabel fizycznych. Widoki Phoenix umożliwiają wiele *tabel wirtualnych* współużytkowanie jednej podstawowej fizycznej tabeli HBase.

Tworzenie widoku Phoenix jest podobne do używania standardowej składni widoku SQL. Jedną z różnic jest to, że można zdefiniować kolumny dla widoku, oprócz kolumn odziedziczonych z jego tabeli bazowej. Można również dodać `KeyValue` nowe kolumny.

Na przykład w tym miejscu `product_metrics` znajduje się tabela fizyczna o nazwie z następującą definicją:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Zdefiniuj widok nad tą tabelą z dodatkowymi kolumnami:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Aby dodać więcej kolumn później, użyj `ALTER VIEW` instrukcji.

### <a name="skip-scan"></a>Pomiń skanowanie

Pomiń skanowanie używa jednej lub więcej kolumn indeksu złożonego, aby znaleźć różne wartości. W przeciwieństwie do skanowania zakresowego, pomiń skanowanie implementuje skanowanie wewnątrz rzędowe, co zapewnia [lepszą wydajność.](https://phoenix.apache.org/performance.html#Skip-Scan) Podczas skanowania pierwsza dopasowana wartość jest pomijana wraz z indeksem, dopóki nie zostanie znaleziona następna wartość.

Pomiń skanowania `SEEK_NEXT_USING_HINT` używa wyliczenia filtru HBase. Za `SEEK_NEXT_USING_HINT`pomocą programu , pomiń skanowanie śledzi, który zestaw kluczy lub zakresy klawiszy są wyszukiwane w każdej kolumnie. Pomiń skanowanie następnie przyjmuje klucz, który został przekazany do niego podczas oceny filtru i określa, czy jest to jedna z kombinacji. Jeśli nie, skanowanie pomijania ocenia następny najwyższy klucz, do który chcesz przejść.

### <a name="transactions"></a>Transakcje

Podczas HBase zapewnia transakcje na poziomie wiersza, Phoenix integruje się z [Tephra,](https://tephra.io/) aby dodać obsługę transakcji między wierszami i między tabelami z pełną semantyką [ACID.](https://en.wikipedia.org/wiki/ACID)

Podobnie jak w przypadku tradycyjnych transakcji SQL, transakcje dostarczane za pośrednictwem menedżera transakcji Phoenix pozwalają upewnić się, że jednostka atomowa danych została pomyślnie wyszecona, cofanie transakcji, jeśli operacja upsert nie powiedzie się w dowolnej tabeli z włączoną obsługą transakcji.

Aby włączyć transakcje phoenix, zobacz [dokumentację transakcji Apache Phoenix](https://phoenix.apache.org/transactions.html).

Aby utworzyć nową tabelę z włączonymi `TRANSACTIONAL` transakcjami, ustaw właściwość `true` na instrukcję: `CREATE`

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Aby zmienić istniejącą tabelę jako transakcyjną, należy użyć tej samej właściwości w instrukcji: `ALTER`

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Nie można przełączyć tabeli transakcyjnej z powrotem do nietransakcyjnych.

### <a name="salted-tables"></a>Stoły solone

*Hotspotting serwera regionu* może wystąpić podczas zapisywania rekordów z kluczami sekwencyjnymi do bazy HBase. Chociaż w klastrze może znajdować się wiele serwerów regionu, zapisy występują tylko na jednym. To stężenie tworzy problem hotspotting, gdzie zamiast obciążenia zapisu są dystrybuowane na wszystkich dostępnych serwerów regionu, tylko jeden jest obsługa obciążenia. Ponieważ każdy region ma wstępnie zdefiniowany maksymalny rozmiar, gdy region osiągnie ten limit rozmiaru, jest podzielony na dwa małe regiony. W takim przypadku jeden z tych nowych regionów przyjmuje wszystkie nowe rekordy, stając się nowym hotspotem.

Aby złagodzić ten problem i osiągnąć lepszą wydajność, wstępnie podzielić tabele, tak aby wszystkie serwery regionu były jednakowo używane. Phoenix zapewnia *solone stoły,* w sposób przejrzysty dodając bajt sosowania do klucza wiersza dla danej tabeli. Tabela jest wstępnie podzielona na granice bajtów soli, aby zapewnić równy rozkład obciążenia między serwerami regionów w początkowej fazie tabeli. Takie podejście dystrybuuje obciążenia zapisu na wszystkich dostępnych serwerach regionu, poprawiając wydajność zapisu i odczytu. Aby zasolć tabelę, określ właściwość `SALT_BUCKETS` tabeli podczas tworzenia tabeli:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Włącz i dostroić Phoenix z Apache Ambari

Klaster HDInsight HBase zawiera [interfejs użytkownika Ambari](hdinsight-hadoop-manage-ambari.md) do wprowadzania zmian konfiguracji.

1. Aby włączyć lub wyłączyć phoenix i kontrolować ustawienia limitu czasu zapytania Phoenix, zaloguj się`https://YOUR_CLUSTER_NAME.azurehdinsight.net`do interfejsu użytkownika sieci Web Ambari ( ) przy użyciu poświadczeń użytkownika Hadoop.

2. Wybierz **HBase** z listy usług w menu po lewej stronie, a następnie wybierz kartę **Konfiguracje.**

    ![Apache Ambari Konfiguracje bazy danych](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Znajdź sekcję konfiguracji **języka Phoenix SQL,** aby włączyć lub wyłączyć phoenix, i ustaw limit czasu kwerendy.

    ![Sekcja konfiguracji JĘZYKA SQL Ambari Phoenix](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Zobacz też

* [Używanie apache Phoenix z klastrami HBase opartymi na systemie Linux w funkcji HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Użyj Apache Zeppelin do uruchamiania zapytań Apache Phoenix przez Apache HBase w usłudze Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
