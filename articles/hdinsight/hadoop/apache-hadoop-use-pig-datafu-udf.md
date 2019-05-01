---
title: Korzystanie z funkcji Apache DataFu przy użyciu programu Pig Apache na HDInsight — Azure
description: Apache DataFu Pig to zbiór bibliotek do użycia z usługą Apache Pig na technologii Apache Hadoop. Dowiedz się, jak używać DataFu z technologii Pig w klastrze usługi HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/16/2018
ms.author: hrasheed
ms.openlocfilehash: 59065c1eefedc2dce9eb56394c4c7db862744366
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691850"
---
# <a name="use-apache-datafu-pig-with-apache-pig-on-hdinsight"></a>Apache DataFu Pig za pomocą Pig Apache na HDInsight

Dowiedz się, jak używać Apache DataFu Pig z HDInsight.

Apache DataFu Pig to zbiór bibliotek typu Open Source do użycia z usługą Apache Pig na technologii Apache Hadoop.
Aby uzyskać więcej informacji na temat DataFu Pig, zobacz [ https://datafu.apache.org/ ](https://datafu.apache.org/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.

* Klaster usługi HDInsight Azure (Linux lub Windows, na podstawie)

  > [!IMPORTANT]  
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Podstawowa wiedza o [na HDInsight przy użyciu Apache Pig](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Zainstaluj DataFu na HDInsight opartych na systemie Linux

> [!IMPORTANT]  
> DataFu jest instalowany w klastrach opartych na systemie Linux w wersji 3.3 lub nowszy i w klastrach z systemem Windows. Go nie jest zainstalowany w klastrach opartych na systemie Linux starszych niż 3.3.
>
> Jeśli używasz klastra z systemem Windows lub wyższa niż wersja 3.3 klastra opartego na systemie Linux, należy pominąć tę sekcję.

DataFu zostanie pobrany i zainstalowany z repozytorium Maven. Aby znaleźć wersję, potrzebujesz i dodaj go do klastra usługi HDInsight, wykonaj następujące kroki:

> [!WARNING]  
> Wersje DataFu mogą mieć wymagań, które nie są spełnione przez HDInsight. Na przykład jeśli używasz starszej wersji DataFu, mogą wymagać innej wersji programu Pig niż to, co jest uwzględnione w HDInsight.

### <a name="find-a-version"></a>Znajdź wersję

1. W przeglądarce internetowej przejdź do https://mvnrepository.com/artifact/org.apache.datafu/datafu-pig i wersji, należy znaleźć.

2. Wybierz numer wersji połączone.

3. Wybierz __Wyświetl wszystkie__ do wyświetlenia wszystkich plików.

4. Lista plików zawiera plik JAR. Zazwyczaj ten plik jest największy na liście, ponieważ zawiera ona wszystkie zależności. Kliknij prawym przyciskiem myszy łącze, a następnie skopiuj adres łącza.

### <a name="download-datafu-to-hdinsight"></a>Pobierz DataFu HDInsight

1. Połącz z klastrem HDInsight opartych na systemie Linux przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Następujące polecenie umożliwia pobranie pliku jar DataFu za pomocą narzędzia wget:

    > [!IMPORTANT]  
    > Link w poleceniu Zamień na adres URL, które wcześniej zostały skopiowane.

    ```
    wget https://central.maven.org/maven2/org/apache/datafu/datafu-pig/1.4.0/datafu-pig-1.4.0.jar
    ```

3. Następnie przekaż go do domyślnego magazynu dla klastra usługi HDInsight. Umieszczenie w domyślnym pliku storage sprawia, że jej dostępne we wszystkich węzłach w klastrze.

    > [!IMPORTANT]  
    > Zastąp numer wersji w nazwie pliku wersję, które zostały pobrane.

    ```
    hdfs dfs -put datafu-pig-1.4.0.jar /example/jars
    ```

    > [!NOTE]  
    > Poprzednie polecenie zapisuje plik jar w `/example/jars` ponieważ ten katalog już istnieje w magazynie klastra. Możesz użyć dowolnej lokalizacji, którą chcesz do magazynu klastra HDInsight.

## <a name="use-datafu-with-pig"></a>Korzystanie z funkcji DataFu przy użyciu programu Pig

Kroki opisane w tej sekcji założono, że czytelnik zna korzystania z technologii Pig w HDInsight. Aby uzyskać więcej informacji dotyczących korzystania z języka Pig z usługą HDInsight, zobacz [korzystanie z języka Pig z HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]  
> Jeśli zainstalowano ręcznie przy użyciu kroki opisane w poprzedniej sekcji DataFu, należy zarejestrować go przed jego użyciem.
>
> * Jeśli klaster używa usługi Azure Storage, użyj `wasb://` ścieżki. Na przykład `register wasb:///example/jars/datafu-pig-1.4.0.jar`.
>
> * Jeśli klaster używa usługi Azure Data Lake Store Gen2, użyj `abfs://` ścieżki. Na przykład `register abfs://home/example/jars/datafu-pig-1.4.0.jar`.
>
> * Jeśli klaster używa usługi Azure Data Lake Store Gen1, użyj `adl://` ścieżki. Na przykład `register adl://home/example/jars/datafu-pig-1.4.0.jar`.

Często definiują alias funkcji DataFu. W poniższym przykładzie zdefiniowano alias `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Następnie można użyć tego aliasu w skrypcie Pig Latin do generowania skrótów dla danych wejściowych. Na przykład poniższy kod zastępuje lokalizacji w danych wejściowych z wartością skrótu:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Generuje następujące wyniki:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat DataFu lub Pig zobacz następujące dokumenty:

* [Apache Pig DataFu wprowadzenie](https://datafu.apache.org/docs/datafu/getting-started.html).
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
