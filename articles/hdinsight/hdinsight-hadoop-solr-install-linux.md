---
title: Aby zainstalować Solr w usłudze HDInsight opartych na systemie Linux - Azure, użyj akcji skryptu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować Solr w klastrach opartych na systemie Linux usługi HDInsight Hadoop za pomocą akcji skryptu.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 77d3964dd54d63db58c63b567ebbe7e529473999
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201564"
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Zainstalować i używać Solr w klastrach HDInsight Hadoop

Dowiedz się, jak zainstalować Solr w usłudze Azure HDInsight za pomocą akcji skryptu. Solr to platforma wyszukiwania zaawansowanego i zapewnia możliwości wyszukiwania na poziomie przedsiębiorstwa na danych zarządzanych przez usługi Hadoop.

> [!IMPORTANT]
    > Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

> [!IMPORTANT]
> Przykładowy skrypt użyty w tym dokumencie instaluje Solr 4.9 z określonej konfiguracji. Jeśli chcesz skonfigurować klaster Solr z różnych kolekcji, odłamków, schematów, replik, itp., należy zmodyfikować skrypt i Solr plików binarnych.

## <a name="whatis"></a>Co to jest Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) to platforma wyszukiwania przedsiębiorstwa, która umożliwia wydajne wyszukiwanie pełnotekstowe danych. Gdy Hadoop umożliwia przechowywanie i zarządzania nimi czasach ogromne ilości danych, Apache Solr zapewnia możliwości wyszukiwania, aby szybko odzyskać dane.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane przez firmę Microsoft.
>
> Niestandardowe składniki, takie jak Solr, otrzymywanie pomocy uzasadnione ekonomicznie ułatwiające aby dalej rozwiązywać ten problem. Pomoc techniczna firmy Microsoft nie można rozwiązać problemy z niestandardowych składników. Konieczne może być Uwzględnij Wspólnot typu open source, aby uzyskać pomoc. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projekty Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Działanie skryptu

Ten skrypt wprowadza następujące zmiany w klastrze usługi HDInsight:

* Instaluje Solr 4.9 do `/usr/hdp/current/solr`
* Tworzy użytkownika, **solrusr**, które jest używane do uruchamiania usługi Solr
* Ustawia **solruser** jako właściciel `/usr/hdp/current/solr`
* Dodaje [Upstart](http://upstart.ubuntu.com/) konfiguracji, które jest uruchamiane automatycznie Solr.

## <a name="install"></a>Zainstaluj Solr za pomocą akcji skryptu

Przykładowy skrypt do zainstalowania Solr w klastrze usługi HDInsight jest dostępna w następującej lokalizacji:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Aby utworzyć klaster ma zainstalowany Solr, wykonaj czynności w [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu. W trakcie procesu tworzenia należy zainstalować Solr następujące czynności:

1. Z __klastra Podsumowanie__ sekcji, settings__ select__Advanced, następnie __skryptu akcji__. Skorzystaj z poniższych informacji, aby wypełnić formularza:

   * **Nazwa**: Wprowadź przyjazną nazwę dla akcji skryptu.
   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: Zaznaczenie tego pola wyboru
   * **Proces ROBOCZY**: Zaznaczenie tego pola wyboru
   * **DOZORCY**: Zaznacz tę opcję, aby zainstalować w węźle dozorcy
   * **Parametry**: pozostaw to pole puste

2. W dolnej części **skryptu akcje** użyj **wybierz** przycisk, aby zapisać konfigurację. Na koniec użyj **dalej** przycisk, aby powrócić do __podsumowanie klastra__

3. Z __klastra Podsumowanie__ wybierz pozycję __Utwórz__ do utworzenia klastra.

## <a name="usesolr"></a>Jak używać Solr w usłudze HDInsight

> [!IMPORTANT]
> Procedura w tej sekcji przedstawia podstawowe funkcje Solr. Aby uzyskać więcej informacji na temat używania Solr, zobacz [lokacji Apache Solr](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Dane indeksu

Wykonaj następujące kroki, aby dodać przykładowe dane do Solr, a następnie wykonasz zapytania:

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:

    > [!NOTE]
    > Zastąp `sshuser` użytkownika SSH dla klastra. Zastąp `clustername` nazwą klastra.

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Kroki opisane w dalszej części tego dokumentu, użyj tunelu SSH do nawiązania połączenia interfejsu użytkownika sieci web Solr. Do wykonania tych czynności, należy ustanowić tunelu SSH, a następnie skonfiguruj przeglądarkę, aby go użyć.
     >
     > Aby uzyskać więcej informacji, zobacz [Użyj SSH Tunneling z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

2. Aby Solr indeksu przykładowych danych, użyj następujących poleceń:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Następujące dane wyjściowe są zwracane do konsoli:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    `post.jar` Dodaje narzędzie **solr.xml** i **monitor.xml** dokumenty do indeksu.
  
3. Aby wykonać zapytania interfejsu API REST Solr, użyj następującego polecenia:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    To polecenie wyszukuje **collection1** dokumenty dopasowania **\*:\*** (zakodowane jako \*% 3A\* w ciągu zapytania). Następujący dokument JSON jest przykładem odpowiedzi:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Przy użyciu pulpitu nawigacyjnego Solr

Pulpit nawigacyjny Solr jest interfejs użytkownika, który umożliwia pracę z Solr za pośrednictwem przeglądarki sieci web. Pulpit nawigacyjny Solr nie jest uwidaczniana bezpośrednio w Internecie z klastrem usługi HDInsight. Tunel SSH służy do niego dostęp. Aby uzyskać więcej informacji na temat używania tunelu SSH, zobacz [Użyj SSH Tunneling z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Po ustanowieniu tunelu SSH, wykonaj następujące kroki, aby za pomocą pulpitu nawigacyjnego Solr:

1. Należy określić nazwę hosta dla podstawowego headnode:

   1. Używanie protokołu SSH nawiązać połączenia z głównym węzłem klastra. Na przykład `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Aby uzyskać więcej informacji o korzystaniu z protokołu SSH, zobacz [używanie SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Aby uzyskać w pełni kwalifikowaną nazwę hosta, użyj następującego polecenia:

        ```bash
        hostname -f
        ```

        To polecenie zwraca wartość podobny do następującego nazwy hosta:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Zapisz wartość zwracana, które jest używane później.

2. W przeglądarce, nawiąż połączenie z **http://HOSTNAME:8983/solr/#/**, gdzie **HOSTNAME** to nazwa określona w poprzednich krokach.

    Żądanie jest kierowane za pośrednictwem tunelu SSH do Solr interfejsu użytkownika sieci web w klastrze. Strona wygląda podobnie jak na poniższej ilustracji:

    ![Obraz pulpitu nawigacyjnego Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. W lewym okienku, użyj **selektora Core** listy rozwijanej wybierz **collection1**. Kilka wpisów powinien je są wyświetlane poniżej **collection1**.

4. Z poniższych wpisów **collection1**, wybierz pozycję **zapytania**. Aby wypełnić stronę wyszukiwania, użyj następujących wartości:

   * W **q** tekst wprowadź  **\*:**\*. To zapytanie zwraca wszystkie dokumenty, które są indeksowane w Solr. Jeśli chcesz wyszukać określony ciąg znaków w dokumentach, możesz wprowadzić ten ciąg w tym miejscu.
   * W **wt** tekst wybierz format danych wyjściowych. Domyślnie jest **json**.

     Na koniec wybierz **wykonywanie zapytania** znajdujący się u dołu pate wyszukiwania.

     ![Aby dostosować klastra, użyj akcji skryptu](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     Dane wyjściowe zwraca dwa dokumenty, które wcześniej dodano do indeksu. Wynik jest podobny do następującego dokumentu JSON:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Uruchamianie i zatrzymywanie Solr

Aby ręcznie zatrzymać i uruchomić Solr, użyj następujących poleceń:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Indeksowane dane kopii zapasowej

Aby utworzyć kopię zapasową danych Solr do magazynu domyślnego dla klastra, wykonaj następujące kroki:

1. Połącz się z klastrem przy użyciu protokołu SSH, a następnie użyj następującego polecenia, aby uzyskać nazwę hosta dla węzła głównego:

    ```bash
    hostname -f
    ```

2. Użyj następującego polecenia, można utworzyć migawki indeksowanego danych. Zastąp **HOSTNAME** o nazwie zwrócony z poprzedniego polecenia:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    Odpowiedź jest podobny do następującego kodu XML:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Przejdź do `/usr/hdp/current/solr/example/solr`. Brak podkatalogu każdej kolekcji. Każdy katalog kolekcji zawiera `data` katalog zawierający migawki dla kolekcji.

4. Aby utworzyć skompresowanego archiwum do folderu migawki, użyj następującego polecenia:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Zastąp `snapshot.20150806185338855` wartości o nazwie migawki kolekcji.

    To polecenie tworzy archiwum o nazwie **snapshot.20150806185338855.tgz**, który zawiera zawartość **snapshot.20150806185338855** katalogu.

5. Następnie można przechowywać archiwum do klastra magazynu głównej przy użyciu następującego polecenia:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Aby uzyskać więcej informacji na temat pracy z Solr tworzenia kopii zapasowej i przywracania, zobacz [ https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups ](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md). Dostosowywanie klastra należy zainstalować Giraph w klastrach HDInsight Hadoop. Giraph umożliwia wykonywanie wykresu przetwarzanie przy użyciu platformy Hadoop i mogą być używane z usługi Azure HDInsight.

* [Instalowanie aplikacji Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md). Dostosowywanie klastra umożliwia instalowanie aplikacji Hue w klastrach HDInsight Hadoop. HUE to zestaw aplikacji sieci Web umożliwiający interakcję z klastrem usługi Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
