---
title: Analizowanie & proces JSON z gałęzią Apache — Usługa Azure HDInsight
description: Dowiedz się, jak używać dokumentów JSON i analizować je przy użyciu usługi Apache Hive w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 1c519533625835677ddae0a274c9ce9f10edc6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73097994"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Przetwarzanie i analizowanie dokumentów JSON przy użyciu gałęzi Apache w usłudze Azure HDInsight

Dowiedz się, jak przetwarzać i analizować pliki notacji obiektów JavaScript (JSON) przy użyciu gałęzi apache w usłudze Azure HDInsight. W tym artykule użyto następującego dokumentu JSON:

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

Plik można znaleźć `wasb://processjson@hditutorialdata.blob.core.windows.net/`pod adresem . Aby uzyskać więcej informacji na temat korzystania z magazynu obiektów Blob platformy Azure z usługą HDInsight, zobacz [Używanie magazynu obiektów blob platformy Azure zgodnym z usługą HDFS z apache Hadoop w usłudze HDInsight.](../hdinsight-hadoop-use-blob-storage.md) Można skopiować plik do domyślnego kontenera klastra.

W tym artykule używasz konsoli Gałąź Apache. Aby uzyskać instrukcje dotyczące otwierania konsoli Hive, zobacz [Korzystanie z apache Ambari Hive View with Apache Hadoop w programie HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Spłaszczanie dokumentów JSON

Metody wymienione w następnej sekcji wymagają, aby dokument JSON składał się z jednego wiersza. Należy więc spłaszczyć dokument JSON do ciągu. Jeśli dokument JSON jest już spłaszczony, możesz pominąć ten krok i przejść bezpośrednio do następnej sekcji dotyczącej analizowania danych JSON. Aby spłaszczyć dokument JSON, uruchom następujący skrypt:

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

Surowy plik JSON znajduje `wasb://processjson@hditutorialdata.blob.core.windows.net/`się w pliku . **Tabela StudentsRaw** Hive wskazuje na nieprzetworzony dokument JSON, który nie jest spłaszczony.

**Tabela StudentsOneLine** Hive przechowuje dane w domyślnym systemie plików HDInsight w ścieżce **/json/students/.**

Instrukcja **INSERT** wypełnia tabelę **StudentOneLine** spłaszczonymi danymi JSON.

Instrukcja **SELECT** zwraca tylko jeden wiersz.

Oto dane wyjściowe instrukcji **SELECT:**

![HdInsight spłaszczenie dokumentu JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analizowanie dokumentów JSON w uli

Hive udostępnia trzy różne mechanizmy uruchamiania zapytań w dokumentach JSON lub można napisać własne:

* Użyj funkcji get_json_object zdefiniowanej przez użytkownika (UDF).
* Użyj json_tuple UDF.
* Użyj niestandardowego serializatora/deserializatora (SerDe).
* Napisz własne UDF przy użyciu języka Python lub innych języków. Aby uzyskać więcej informacji na temat uruchamiania własnego kodu Pythona z hive, zobacz [Python UDF z Apaszem Hive i Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Korzystanie z get_json_object UDF

Hive zapewnia wbudowany UDF o nazwie [get_json_object,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) które mogą wykonywać zapytania JSON w czasie wykonywania. Ta metoda przyjmuje dwa argumenty — nazwę tabeli i nazwę metody, która ma spłaszczony dokument JSON i pole JSON, które musi zostać przeanalizowane. Spójrzmy na przykład, aby zobaczyć, jak działa ten UDF.

Następująca kwerenda zwraca imię i nazwisko dla każdego ucznia:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Oto dane wyjściowe po uruchomieniu tej kwerendy w oknie konsoli:

![Apache Hive uzyskać json obiektu UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Istnieją ograniczenia get_json_object UDF:

* Ponieważ każde pole w kwerendzie wymaga ponownego wykonania kwerendy, wpływa na wydajność.
* **FUNKCJA\_GET JSON_OBJECT()** zwraca reprezentację ciągu tablicy. Aby przekonwertować tę tablicę na tablicę Hive, należy użyć wyrażeń regularnych, aby zastąpić nawiasy kwadratowe "[" i "]", a następnie należy również wywołać split, aby uzyskać tablicę.

Dlatego wiki Hive zaleca korzystanie z **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Korzystanie z json_tuple UDF

Inny UDF dostarczony przez Hive nazywa [się json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), który działa lepiej niż [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ta metoda przyjmuje zestaw kluczy i ciąg JSON i zwraca krotę wartości przy użyciu jednej funkcji. Następująca kwerenda zwraca identyfikator ucznia i ocenę z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Dane wyjściowe tego skryptu w konsoli hive:

![Apache Hive json wyniki kwerendy](./media/using-json-in-hive/hdinsight-json-tuple.png)

json_tuple UDF używa składni [widoku bocznego](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) w gałęzi, która umożliwia\_json krople utworzyć tabelę wirtualną, stosując funkcję UDT do każdego wiersza oryginalnej tabeli. Złożone JSONy stają się zbyt nieporęczne z powodu wielokrotnego korzystania z **widoku bocznego**. Ponadto **JSON_TUPLE** nie może obsłużyć zagnieżdżonych sieci JSON.

### <a name="use-a-custom-serde"></a>Używanie niestandardowego serde

SerDe jest najlepszym wyborem do analizowania zagnieżdżonych dokumentów JSON. Umożliwia zdefiniowanie schematu JSON, a następnie można użyć schematu do analizowaniu dokumentów. Aby uzyskać instrukcje, zobacz [Jak używać niestandardowego narzędzia JSON SerDe z usługą Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Podsumowanie

Podsumowując typ operatora JSON w gałęzi, który wybierzesz zależy od scenariusza. Jeśli masz prosty dokument JSON i masz tylko jedno pole do wyszukania, możesz użyć **get_json_object**Hive UDF . Jeśli masz więcej niż jeden klucz do wyszukania, możesz użyć **json_tuple**. Jeśli masz zagnieżdżony dokument, należy użyć **pliku JSON SerDe**.

## <a name="next-steps"></a>Następne kroki

Artykuły pokrewne można znaleźć w:

* [Użyj Apache Hive i HiveQL z Apache Hadoop w HDInsight do analizy przykładowego pliku Apache log4j](../hdinsight-use-hive.md)
* [Analizowanie danych opóźnienia lotu przy użyciu interaktywnej kwerendy w programie HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analizowanie danych Twittera przy użyciu usługi Apache Hive w hdinsight](../hdinsight-analyze-twitter-data-linux.md)
