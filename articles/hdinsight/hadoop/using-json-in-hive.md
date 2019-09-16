---
title: Analizowanie i przetwarzanie dokumentów JSON za pomocą Apache Hive w usłudze Azure HDInsight
description: Dowiedz się, jak używać dokumentów JSON i analizować je za pomocą Apache Hive w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: dd1c9f5b10583e886c0357ce64bdf9d8bdc6c4c8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883387"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Przetwarzanie i analizowanie dokumentów JSON przy użyciu Apache Hive w usłudze Azure HDInsight

Dowiedz się, jak przetwarzać i analizować pliki JavaScript Object Notation (JSON) za pomocą Apache Hive w usłudze Azure HDInsight. W tym artykule jest stosowany następujący dokument JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Plik można znaleźć pod adresem `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Aby uzyskać więcej informacji na temat korzystania z usługi Azure Blob Storage z usługą HDInsight, zobacz [Korzystanie z usługi Azure Blob Storage zgodnej z systemem plików HDFS przy użyciu Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-use-blob-storage.md). Plik można skopiować do domyślnego kontenera klastra.

W tym artykule użyto konsoli Apache Hive. Aby uzyskać instrukcje dotyczące sposobu otwierania konsoli programu Hive, zobacz temat [Korzystanie z programu Apache Ambari Hive View z Apache Hadoop w usłudze HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Spłaszcz dokumenty JSON
Metody wymienione w następnej sekcji wymagają, aby dokument JSON był złożony z pojedynczego wiersza. Dlatego należy spłaszczyć dokument JSON do ciągu. Jeśli dokument JSON został już spłaszczony, możesz pominąć ten krok i przejść bezpośrednio do następnej sekcji w temacie Analizowanie danych JSON. Aby spłaszczyć dokument JSON, uruchom następujący skrypt:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Plik RAW JSON znajduje się w lokalizacji `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Tabela **StudentsRaw** Hive wskazuje nieprzetworzony dokument JSON, który nie jest spłaszczony.

W tabeli Hive **StudentsOneLine** są przechowywane dane w domyślnym systemie plików usługi HDInsight pod ścieżką **/JSON/Students/** .

Instrukcja **INSERT** wypełnia tabelę **StudentOneLine** za pomocą spłaszczonych danych JSON.

Instrukcja **SELECT** zwraca tylko jeden wiersz.

Oto dane wyjściowe instrukcji **SELECT** :

![Spłaszczanie dokumentu JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analizowanie dokumentów JSON w usłudze Hive
Program Hive oferuje trzy różne mechanizmy uruchamiania zapytań w dokumentach JSON lub można napisać własne:

* Użyj funkcji zdefiniowanej przez użytkownika (UDF) get_json_object.
* Użyj json_tuple UDF.
* Użyj niestandardowego serializatora/deserializacji (elementu SERDE).
* Napisz własny format UDF przy użyciu języka Python lub innych języków. Aby uzyskać więcej informacji na temat sposobu uruchamiania własnego kodu w języku Python za pomocą usługi Hive, zobacz [Python UDF with Apache Hive i Apache świni] [HDInsight-Python].

### <a name="use-the-get_json_object-udf"></a>Korzystanie z get_json_object UDF
Program Hive zawiera wbudowaną funkcję UDF o nazwie [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , która umożliwia wykonywanie zapytań JSON w czasie wykonywania. Ta metoda przyjmuje dwa argumenty — nazwę tabeli i nazwę metody, która ma spłaszczony dokument JSON i pole JSON, które należy przeanalizować. Spójrzmy na przykład, aby zobaczyć, jak działa ta metoda UDF.

Następujące zapytanie zwraca imię i nazwisko dla każdego ucznia:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Oto dane wyjściowe po uruchomieniu tego zapytania w oknie konsoli:

![get_json_object UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Istnieją ograniczenia dotyczące get_json_object UDF:

* Ponieważ każde pole w zapytaniu wymaga przeanalizowania zapytania, ma wpływ na wydajność.
* **Get\_JSON_OBJECT ()** zwraca ciąg reprezentujący tablicę. Aby przekonwertować tę tablicę na tablicę programu Hive, należy użyć wyrażeń regularnych w celu zastąpienia nawiasów kwadratowych "[" i "]", a następnie należy wywołać metodę Split, aby pobrać tablicę.

To dlatego, że witryna typu wiki programu Hive zaleca użycie json_tuple.  

### <a name="use-the-json_tuple-udf"></a>Korzystanie z json_tuple UDF
Inna technologia UDF udostępniona przez gałąź " [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)", która wykonuje lepsze niż [get_ JSON _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ta metoda pobiera zestaw kluczy i ciąg JSON oraz zwraca spójność wartości przy użyciu jednej funkcji. Następujące zapytanie zwraca identyfikator ucznia i ocenę klasy z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Dane wyjściowe tego skryptu w konsoli programu Hive:

![json_tuple UDF](./media/using-json-in-hive/hdinsight-json-tuple.png)

Json_tuple UDF używa składni [widoku bocznego](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) w Hive, co umożliwia spójność notacji JSON\_w celu utworzenia tabeli wirtualnej przez zastosowanie funkcji UDT do każdego wiersza oryginalnej tabeli. Złożone dane JSON stają się zbyt nieporęczny z powodu wielokrotnego użycia **widoku bocznego**. Ponadto **JSON_TUPLE** nie może obsłużyć ZAGNIEŻDŻONych notacji JSON.

### <a name="use-a-custom-serde"></a>Użyj niestandardowego elementu SERDE
Elementu SERDE jest najlepszym wyborem do analizowania zagnieżdżonych dokumentów JSON. Pozwala on definiować schemat JSON, a następnie można użyć schematu do analizy dokumentów. Aby uzyskać instrukcje, zobacz [How to use a Custom JSON elementu SERDE with Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Podsumowanie
Podsumowując, typ operatora JSON w wyborze gałęzi zależy od danego scenariusza. Jeśli masz prosty dokument JSON i masz tylko jedno pole do wyszukania, możesz użyć opcji UDF get_json_object. Jeśli masz więcej niż jeden klucz do wyszukania, możesz użyć json_tuple. Jeśli dokument jest zagnieżdżony, należy użyć elementu SERDE JSON.

## <a name="next-steps"></a>Następne kroki

Aby poznać pokrewne artykuły, zobacz:

* [Analizowanie przykładowego pliku Apache Log4J przy użyciu Apache Hive i HiveQL z Apache Hadoop w usłudze HDInsight](../hdinsight-use-hive.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu interakcyjnych zapytań w usłudze HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analizowanie danych z usługi Twitter przy użyciu Apache Hive w usłudze HDInsight](../hdinsight-analyze-twitter-data-linux.md)
