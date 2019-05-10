---
title: Analizowanie i przetwarzanie dokumentów JSON za pomocą technologii Hive — Azure HDInsight
description: Dowiedz się, jak używać dokumentów JSON i analizować je przy użyciu technologii Hive w usłudze Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: hrasheed
ms.openlocfilehash: 4ba77c04f1e7976f2843bbe7117de63c376960b5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717834"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Przetwarzanie i analizowanie dokumentów JSON za pomocą technologii Hive w usłudze Azure HDInsight

Dowiedz się, jak przetwarzać i analizować pliki JavaScript Object Notation (JSON) za pomocą technologii Hive w usłudze Azure HDInsight. W tym samouczku jest używany następujący dokument JSON:

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

Plik znajduje się w temacie **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. Aby uzyskać więcej informacji na temat usługi Azure Blob storage za pomocą HDInsight, zobacz [zgodnego systemem plików HDFS użycia usługi Azure Blob storage przy użyciu technologii Apache Hadoop w HDInsight](../hdinsight-hadoop-use-blob-storage.md). Możesz skopiować plik do kontenera domyślnego klastra.

W tym samouczku użyjesz konsoli Apache Hive. Aby uzyskać instrukcje na temat sposobu Otwórz konsolę Hive, zobacz [Użyj Apache Ambari programu Hive widoku przy użyciu technologii Apache Hadoop w HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Spłaszczanie dokumentów JSON
Z metod opisanych w następnej sekcji wymagają, że dokument JSON składać się z pojedynczego wiersza. Tak musisz spłaszczać dokument JSON do ciągu. Jeśli dokument JSON już jest spłaszczany, możesz pominąć ten krok i przejdź bezpośrednio do następnej sekcji, analizowanie danych JSON. Spłaszczanie dokumentów JSON, uruchom następujący skrypt:

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

Plik JSON raw znajduje się w **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. **StudentsRaw** Hive punktów tabeli pierwotnych dokumencie JSON, który nie jest spłaszczany.

**StudentsOneLine** tabeli hive za pomocą przechowuje dane w HDInsight domyślnego systemu plików w obszarze **/json/studentów/** ścieżki.

**Wstaw** wypełnia instrukcji **StudentOneLine** tabelę zawierającą spłaszczone dane JSON.

**Wybierz** instrukcja zwraca tylko jeden wiersz.

Oto dane wyjściowe **wybierz** instrukcji:

![Spłaszczanie dokumentu JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analizowanie dokumentów JSON w gałęzi
Gałąź zawiera trzy różne mechanizmy uruchamianie zapytań dotyczących dokumentów JSON lub napisać własny:

* Funkcja get_json_object zdefiniowanych przez użytkownika (UDF).
* Użyj json_tuple funkcji zdefiniowanej przez użytkownika.
* Użyj niestandardowego serializatora/Deserializator (SerDe).
* Napisz własny funkcji zdefiniowanej przez użytkownika za pomocą języka Python lub innych języków. Aby uzyskać więcej informacji na temat sposobu uruchamiania kodu języka Python za pomocą technologii Hive, zobacz [UDF języka Python przy użyciu Apache Hive i Apache Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Use the get_json_object UDF
Gałąź udostępnia wbudowanego systemu plików UDF o nazwie [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , można wykonać zapytania JSON w czasie wykonywania. Ta metoda przyjmuje dwa argumenty — nazwę tabeli i nazwy metody, która ma spłaszczonych dokument JSON i pola JSON, który ma zostać przeanalizowany. Spójrzmy na przykład, aby zobaczyć, jak działa ta funkcji zdefiniowanej przez użytkownika.

Następujące zapytanie zwraca imię i nazwisko dla każdego ucznia:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Oto dane wyjściowe po uruchomieniu tego zapytania w oknie konsoli:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Istnieją ograniczenia get_json_object funkcji zdefiniowanej przez użytkownika:

* Ponieważ każde pole w zapytaniu wymaga ponownej analizy zapytania, ma wpływ na wydajność.
* **Pobierz\_JSON_OBJECT()** zwraca ciąg reprezentujący tablicę. Aby przekonwertować tej tablicy do tablicy programu Hive, należy użyć wyrażeń regularnych, aby zamienić nawiasy kwadratowe "[" i "]", a następnie należy także wywołać podziału, aby uzyskać tablicy.

Jest to, dlaczego Hive wiki zaleca używanie json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Użyj json_tuple funkcji zdefiniowanej przez użytkownika
Nosi nazwę innego systemu plików UDF, które są dostarczane przez program Hive [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), który działa lepiej niż [_obiekt json rzeczoznawcy](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ta metoda przyjmuje zestaw kluczy i ciągu JSON i zwraca spójną kolekcję wartości za pomocą jednej funkcji. Następujące zapytanie zwraca identyfikator dla uczniów i klasy korporacyjnej z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Dane wyjściowe tego skryptu w konsoli programu Hive:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple korzysta z systemu plików UDF [poprzecznego widoku](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) składni w gałęzi, która umożliwia json\_spójnej kolekcji, aby utworzyć wirtualne tabelę, stosując funkcję UDT do każdego wiersza oryginalnej tabeli. Złożone JSON Smb1sessionsetup stają się zbyt niewygodne z powodu użycia powtarzanych **WYŚWIETL bocznych**. Ponadto **JSON_TUPLE** nie może obsługiwać JSON Smb1sessionsetup zagnieżdżonych.

### <a name="use-a-custom-serde"></a>Użyj niestandardowego elementu SerDe
Elementu SerDe jest najlepszym wyborem do analizowania zagnieżdżonych dokumentów JSON. Dzięki temu można zdefiniować schemat JSON, a następnie można użyć schematu do analizowania dokumentów. Aby uzyskać instrukcje, zobacz [jak niestandardowe SerDe JSON za pomocą programu Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Podsumowanie
Podsumowując typ operatora JSON w gałęzi, które można wybrać zależy od danego scenariusza. Jeśli masz tylko jedno pole w celu wyszukania proste dokument JSON, można użyć get_json_object Hive funkcji zdefiniowanej przez użytkownika. Jeśli masz więcej niż jednego klucza do wyszukania, można użyć json_tuple. W przypadku zagnieżdżonych dokumentu należy używać elementu SerDe JSON.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pokrewne artykuły zobacz:

* [Użyj Apache Hive i HiveQL z usługą Apache Hadoop w HDInsight do analizy przykładowego pliku Apache log4j](../hdinsight-use-hive.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu technologii Hive w HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)
* [Analizowanie danych serwisu Twitter przy użyciu technologii Hive w HDInsight](../hdinsight-analyze-twitter-data-linux.md)

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

