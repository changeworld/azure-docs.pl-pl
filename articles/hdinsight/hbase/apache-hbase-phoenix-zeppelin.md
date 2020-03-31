---
title: Uruchamianie zapytań podstawowych apache w usłudze Azure HDInsight z usługą Apache Phoenix
description: Dowiedz się, jak używać apache zeppelin do uruchamiania zapytań Apache Base w Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392243"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Użyj Apache Zeppelin do uruchamiania zapytań Apache Phoenix przez Apache HBase w usłudze Azure HDInsight

Apache Phoenix to otwarta, masowo równoległa relacyjna warstwa bazy danych zbudowana na HBase. Phoenix umożliwia używanie sql jak zapytania za pośrednictwem HBase. Phoenix używa sterowników JDBC pod spodem, aby umożliwić tworzenie, usuwanie, zmienianie tabel SQL, indeksów, widoków i sekwencji.  Phoenix można również użyć do aktualizowania wierszy pojedynczo i zbiorczo. Phoenix używa kompilacji natywnej NOSQL, a nie przy użyciu MapReduce do kompilowania zapytań, umożliwiając tworzenie aplikacji o małym opóźnieniu na bazie HBase.

Apache Zeppelin to notes internetowy typu open source, który umożliwia tworzenie opartych na danych, opartych na współpracy dokumentów przy użyciu interaktywnej analizy danych i języków takich jak SQL i Scala. Pomaga deweloperom danych & analityków danych opracowywać, organizować, wykonywać i udostępniać kod do manipulowania danymi. Umożliwia wizualizację wyników bez odwoływania się do wiersza polecenia lub konieczności szczegółów klastra.

Użytkownicy HDInsight mogą używać Apache Zeppelin do wykonywania zapytań o tabele Phoenix. Apache Zeppelin jest zintegrowany z klastrem HDInsight i nie ma żadnych dodatkowych kroków, aby go użyć. Wystarczy utworzyć notes Zeppelin z interpreterem JDBC i rozpocząć pisanie zapytań Phoenix SQL

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache HBase w programie HDInsight. Zobacz [Wprowadzenie do apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Tworzenie notatki Apache Zeppelin

1. Zamień `CLUSTERNAME` na nazwę klastra `https://CLUSTERNAME.azurehdinsight.net/zeppelin`w następującym adresie URL . Następnie wprowadź adres URL w przeglądarce internetowej. Wprowadź nazwę użytkownika i hasło logowania do klastra.

1. Na stronie Zeppelin wybierz pozycję **Utwórz nową notatkę**.

    ![Interaktywne zapytanie HDInsight zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. W oknie dialogowym **Tworzenie nowej notatki** wpisz lub wybierz następujące wartości:

    - Uwaga Nazwa: Wprowadź nazwę notatki.
    - Domyślny interpreter: Wybierz **jdbc** z listy rozwijanej.

    Następnie wybierz **pozycję Utwórz notatkę**.

1. Upewnij się, że nagłówek notesu pokazuje stan połączenia. Jest oznaczona zieloną kropką w prawym górnym rogu.

    ![Stan notebooka Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Stan notebooka Zeppelin")

1. Utwórz tabelę bazy danych HBase. Wprowadź następujące polecenie, a następnie naciśnij **klawisze Shift + Enter**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Instrukcja **%jdbc(phoenix)** w pierwszym wierszu informuje notes o użyciu interpretera Phoenix JDBC.

1. Wyświetlanie utworzonych tabel.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Wstaw wartości w tabeli.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Kwerenda tabeli.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Usuwanie rekordu.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Upuść stół.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Następne kroki

- [Apache Phoenix obsługuje teraz zeppelin w usłudze Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix gramatyka](https://phoenix.apache.org/language/index.html)
