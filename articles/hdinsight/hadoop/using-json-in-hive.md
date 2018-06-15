---
title: Analizowanie i przetwarzania dokumentów JSON z Apache Hive w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać dokumentów JSON i analizować je przy użyciu apache Hive w usłudze Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 19d8e74e6f3ca8e7a0b0a64bdd782ea21b233c17
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202530"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Przetwarzanie i analizowanie dokumentów JSON przy użyciu Apache Hive w usłudze Azure HDInsight

Dowiedz się, jak przetwarzanie i analizowanie plików JavaScript Object Notation (JSON) za pomocą Apache Hive w usłudze Azure HDInsight. W tym samouczku używana następujący dokument JSON:

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

Plik znajduje się w temacie **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Aby uzyskać więcej informacji o tym, jak używać magazynu obiektów Blob platformy Azure z usługą HDInsight, zobacz [magazynu obiektów Blob Azure użycia systemu plików HDFS zgodnych z platformą Hadoop w usłudze HDInsight](../hdinsight-hadoop-use-blob-storage.md). Plik ten można skopiować do domyślnego kontenera klastra.

W tym samouczku używasz konsoli programu Hive. Aby uzyskać instrukcje dotyczące sposobu otwierania konsoli programu Hive, zobacz [używanie Hive z usługą Hadoop w usłudze HDInsight przy użyciu pulpitu zdalnego](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Spłaszczanie dokumentów JSON
Z metod opisanych w następnej sekcji wymagają, że dokument JSON składać się z jednym wierszu. Dlatego musi spłaszczanie dokumentu JSON na ciąg. Jeśli już właściwości jest spłaszczona dokumentu JSON, możesz pominąć ten krok i przejść bezpośrednio do następnej sekcji na analizowanie danych JSON. Do spłaszczenia dokumentów JSON, uruchom następujący skrypt:

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

Plik JSON raw znajduje się w **wasb://processjson@hditutorialdata.blob.core.windows.net/**. **StudentsRaw** Hive punktów tabeli pierwotnych dokumentu JSON, który nie jest spłaszczona.

**StudentsOneLine** tabelę programu Hive przechowuje dane w usłudze HDInsight domyślnego systemu plików w obszarze **/json/uczniów lubstudentów/** ścieżki.

**Wstaw** wypełnia instrukcji **StudentOneLine** tabeli z spłaszczoną dane JSON.

**Wybierz** instrukcja zwraca tylko jeden wiersz.

Poniżej przedstawiono dane wyjściowe **wybierz** instrukcji:

![Spłaszczanie dokumentu JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analizowanie dokumentów JSON w gałęzi
Gałąź udostępnia trzy różne mechanizmy uruchamianie kwerend dotyczących dokumentów JSON lub napisać własny:

* Funkcja get_json_object zdefiniowane przez użytkownika (UDF).
* Użyj json_tuple funkcji zdefiniowanej przez użytkownika.
* Użyj niestandardowego programu szeregującego/deserializacji (SerDe).
* Pisanie własnych funkcji zdefiniowanej przez użytkownika za pomocą języka Python lub innych języków. Aby uzyskać więcej informacji na temat uruchamiania kodu języka Python z Hive, zobacz [UDF języka Python z Apache Hive i Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Użyj get_json_object funkcji zdefiniowanej przez użytkownika
Gałąź zapewnia wbudowanej funkcji zdefiniowanej przez użytkownika o nazwie [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) który może wykonać kwerendy JSON w czasie wykonywania. Ta metoda przyjmuje dwa argumenty--nazwę tabeli i nazwę metody, mającej spłaszczoną dokument JSON i pola JSON, który ma zostać przeanalizowany. Oto przykład, aby zobaczyć, jak działa ten funkcji zdefiniowanej przez użytkownika.

Następujące zapytanie zwraca imię i nazwisko dla użytkowników:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Poniżej przedstawiono dane wyjściowe po uruchomieniu tego zapytania w oknie konsoli:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Istnieją ograniczenia get_json_object funkcji zdefiniowanej przez użytkownika:

* Ponieważ każde pole w zapytaniu wymaga ponownej analizy zapytania, wpływa na wydajność.
* **Pobierz\_JSON_OBJECT()** zwraca reprezentację ciągu tablicy. Aby przekonwertować tej tablicy Hive array, należy użyć wyrażeń regularnych zastąpić nawiasy kwadratowe "[" i "]", a następnie należy także wywołać podziału do pobrania tablicy.

Jest to, dlaczego Hive wiki zaleca użycie json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Użyj json_tuple funkcji zdefiniowanej przez użytkownika
Inny UDF udostępniane przez Hive jest wywoływana [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), który wykonuje lepszym rozwiązaniem niż [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ta metoda przyjmuje zestaw kluczy i ciągu JSON i zwraca spójną kolekcję wartości za pomocą jednej funkcji. Następujące zapytanie zwraca identyfikator dla użytkowników domowych i kategorii z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Dane wyjściowe tego skryptu w konsoli programu Hive:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple używa funkcji zdefiniowanej przez użytkownika [penetracji widoku](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) składni w gałęzi, dzięki czemu json\_spójnej kolekcji, aby utworzyć wirtualny tabelę, stosując funkcję UDT do każdego wiersza oryginalnej tabeli. Złożone JSONs być zbyt trudno obsługiwać z powodu wielokrotnego użycia **PENETRACJI WIDOKU**. Ponadto **JSON_TUPLE** nie może obsłużyć JSONs zagnieżdżonych.

### <a name="use-a-custom-serde"></a>Użyj niestandardowych SerDe
SerDe jest najlepszym wyborem do analizowania zagnieżdżonej dokumentów JSON. Umożliwia zdefiniowanie schematu JSON, a można przeanalizować dokumenty schematu. Aby uzyskać instrukcje, zobacz [jak niestandardowe SerDe JSON za pomocą usługi Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Podsumowanie
Podsumowując typ operatora JSON w gałęzi, którą można wybrać zależy od danego scenariusza. Jeśli masz tylko jedno pole w celu wyszukania prosty dokument JSON, można użyć get_json_object Hive funkcji zdefiniowanej przez użytkownika. Jeśli masz więcej niż jednego klucza do wyszukania, można użyć json_tuple. Jeśli masz zagnieżdżonych dokumentu, należy używać JSON SerDe.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pokrewne artykuły zobacz:

* [Używanie Hive i HiveQL z usługą Hadoop w usłudze HDInsight do analizy przykładowego pliku Apache log4j](../hdinsight-use-hive.md)
* [Analizowanie danych opóźnienie transmitowane przy użyciu usługi Hive w usłudze HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analizowanie danych Twitter przy użyciu usługi Hive w usłudze HDInsight](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

