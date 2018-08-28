---
title: Użyj akcji skryptu, aby zainstalować platformę Solr w HDInsight opartych na systemie Linux — Azure
description: Dowiedz się, jak zainstalować platformę Solr w klastrach opartych na systemie Linux usługi HDInsight Hadoop za pomocą akcji skryptu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 205983344be8ae5bbe566a208ceb862b2e93cb8d
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093101"
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalowanie i korzystanie z platformy Solr w klastrach usługi HDInsight Hadoop

Dowiedz się, jak zainstalować platformę Solr w usłudze Azure HDInsight przy użyciu akcji skryptu. Solr to platforma zaawansowanej funkcji przeszukiwania i oferuje możliwości wyszukiwania na poziomie przedsiębiorstwa w danych zarządzanych przez usługę Hadoop.

> [!IMPORTANT]
    > Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

> [!IMPORTANT]
> Przykładowy skrypt używany w tym dokumencie instaluje Solr 4.9 przy użyciu określonej konfiguracji. Jeśli chcesz skonfigurować klaster Solr przy użyciu różnych kolekcjach, fragmentów, schematów, replik, itp., należy zmodyfikować skrypt i pliki binarne Solr.

## <a name="whatis"></a>Co to jest Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) to platforma wyszukiwania dla przedsiębiorstw, która umożliwia zaawansowane wyszukiwanie pełnotekstowe danych. Gdy Hadoop umożliwia przechowywanie i zarządzanie nimi ogromnych ilości danych, Apache Solr udostępnia możliwości wyszukiwania, aby szybko odzyskać dane.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane przez firmę Microsoft.
>
> Składniki niestandardowe, takie jak Solr, otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Pomoc techniczna firmy Microsoft nie można rozwiązać problemy z niestandardowych składników. Może być konieczne prowadzenia społeczności "open source", aby uzyskać pomoc. Na przykład, istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projektów Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Działanie skryptu

Ten skrypt powoduje następujące zmiany w klastrze HDInsight:

* Instaluje Solr 4.9 do `/usr/hdp/current/solr`
* Tworzy użytkownika, **solrusr**, który jest używany do uruchamiania usług platformy Solr
* Zestawy **solruser** jako właściciela `/usr/hdp/current/solr`
* Dodaje [Upstart](http://upstart.ubuntu.com/) konfiguracji, która jest uruchamiana automatycznie Solr.

## <a name="install"></a>Zainstalować platformę Solr za pomocą akcji skryptu

Przykładowy skrypt, aby zainstalować platformę Solr w klastrze usługi HDInsight jest dostępna w następującej lokalizacji:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Aby utworzyć klaster, który ma zainstalowany Solr, użyj kroków w [klastrów HDInsight tworzenie](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu. W trakcie procesu tworzenia umożliwia zainstalować platformę Solr następujące czynności:

1. Z __klastra Podsumowanie__ settings__ select__Advanced następnie, w sekcji __akcji skryptu__. Skorzystaj z poniższych informacji do wypełnienia formularza:

   * **Nazwa**: Wprowadź przyjazną nazwę dla akcji skryptu.
   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: Zaznacz tę opcję
   * **Proces ROBOCZY**: Zaznacz tę opcję
   * **DOZORCY**: Zaznacz tę opcję, aby zainstalować w węźle dozorcy
   * **Parametry**: pozostaw to pole puste

2. W dolnej części **akcji skryptu** sekcji **wybierz** przycisk, aby zapisać konfigurację. Na koniec użyj **dalej** przycisk, aby powrócić do __podsumowanie klastra__

3. Z __klastra Podsumowanie__ wybierz opcję __Utwórz__ do utworzenia klastra.

## <a name="usesolr"></a>Jak korzystanie z platformy Solr w HDInsight

> [!IMPORTANT]
> Kroki opisane w tej sekcji pokazują podstawową funkcjonalność Solr. Aby uzyskać więcej informacji na temat korzystania z platformy Solr, zobacz [witryny Apache Solr](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Dane indeksu

Wykonaj następujące kroki, aby dodać przykładowe dane do Solr, a następnie wykonuje zapytania:

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:

    > [!NOTE]
    > Zastąp `sshuser` użytkownika SSH dla klastra. Zastąp `clustername` nazwą klastra.

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Kroki opisane w dalszej części tego dokumentu za pomocą tunelu SSH, połączyć z platformy Solr w internetowym interfejsie użytkownika. Aby wykonać te kroki, możesz ustanowić tunel SSH, a następnie skonfiguruj przeglądarkę, aby go użyć.
     >
     > Aby uzyskać więcej informacji, zobacz [użycie tunelowania SSH z HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

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

    `post.jar` Dodaje narzędzie **solr.xml** i **monitor.xml** dokumentów do indeksu.
  
3. Użyj następującego polecenia do wykonywania zapytań interfejsu API REST platformy Solr:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    To polecenie przeszukuje **collection1** dokumenty dopasowania **\*:\*** (zakodowanymi w formacie \*% 3A\* w ciągu zapytania). Poniższy dokument JSON znajduje się przykład odpowiedzi:

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

### <a name="using-the-solr-dashboard"></a>Korzystanie z pulpitu nawigacyjnego platformy Solr

Pulpit nawigacyjny platformy Solr to internetowy interfejs użytkownika, który umożliwia pracę z platformy Solr za pośrednictwem przeglądarki sieci web. Pulpit nawigacyjny platformy Solr nie jest uwidaczniana, bezpośrednio w Internecie z klastra usługi HDInsight. Za pomocą tunelu SSH do niego dostęp. Aby uzyskać więcej informacji na temat używania tunelu SSH, zobacz [użycie tunelowania SSH z HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Po ustanowieniu tunelu SSH, wykonaj następujące kroki, aby korzystać z platformy Solr pulpitu nawigacyjnego:

1. Określ nazwę hosta dla podstawowego węzła głównego:

   1. Łączenie z węzłem głównym klastra za pomocą protokołu SSH. Na przykład `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Aby uzyskać więcej informacji na temat korzystania z protokołu SSH, zobacz [użycia protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Aby uzyskać w pełni kwalifikowana nazwa hosta, użyj następującego polecenia:

        ```bash
        hostname -f
        ```

        To polecenie zwraca wartość podobne do następujących nazwy hosta:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Wartość zwracana, należy zapisać, ponieważ jest on używany później.

2. W przeglądarce, połącz się z **http://HOSTNAME:8983/solr/#/**, gdzie **HOSTNAME** nazywa się określone w poprzednich krokach.

    Żądanie jest kierowane za pośrednictwem tunelu SSH w internetowym interfejsie użytkownika platformy Solr w klastrze. Strony wyświetli się podobnie do poniższej ilustracji:

    ![Obraz przedstawiający pulpit nawigacyjny platformy Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. W okienku po lewej stronie **selektor Core** listy rozwijanej do wybierz **collection1**. Wiele wpisów należy je są wyświetlone poniżej **collection1**.

4. Z poniższych wpisów **collection1**, wybierz opcję **zapytania**. Aby wypełnić stronę wyszukiwania, należy użyć następujących wartości:

   * W **q** tekstu wprowadź  **\*:**\*. To zapytanie zwraca wszystkie dokumenty, które są indeksowane w Solr. Jeśli chcesz wyszukać określony ciąg znaków w dokumentach, możesz wprowadzić tutaj tego ciągu.
   * W **wt** tekstu wybierz format danych wyjściowych. Wartość domyślna to **json**.

     Na koniec wybierz pozycję **wykonaj zapytanie** znajdujący się u dołu Wklej wyszukiwania.

     ![Użyj akcji skryptu, aby dostosować klastra](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     Dane wyjściowe zwraca dwa dokumenty, które zostały dodane do indeksu wcześniej. Dane wyjściowe będą podobne do następujących dokumentów JSON:

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

Aby ręcznie uruchamiać i zatrzymywać Solr, użyj następujących poleceń:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Dane kopii zapasowej indeksowane

Wykonaj następujące kroki, aby tworzył kopie zapasowe danych Solr domyślny magazyn dla klastra:

1. Nawiąż połączenie z klastrem przy użyciu protokołu SSH, a następnie użyj następującego polecenia, aby uzyskać nazwę hosta węzła głównego:

    ```bash
    hostname -f
    ```

2. Użyj następującego polecenia, aby utworzyć migawkę indeksowane dane. Zastąp **HOSTNAME** o nazwie zwrócone w wyniku poprzedniego polecenia:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    Odpowiedź jest podobna do następujący kod XML:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Zmień katalog na `/usr/hdp/current/solr/example/solr`. Brak podkatalogu każdej kolekcji. Każdy katalog kolekcji zawiera `data` katalogu, który zawiera migawkę dla kolekcji.

4. Aby utworzyć skompresowane archiwum folder migawki, użyj następującego polecenia:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Zastąp `snapshot.20150806185338855` wartości o nazwie migawki dla kolekcji.

    To polecenie umożliwia utworzenie archiwum o nazwie **snapshot.20150806185338855.tgz**, który zawiera zawartość **snapshot.20150806185338855** katalogu.

5. Następnie można zapisać archiwum do klastra magazynu podstawowego, korzystając z następującego polecenia:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Aby uzyskać więcej informacji na temat pracy z platformy Solr w kopii zapasowej i przywracanie danych, zobacz [ https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups ](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Kolejne kroki

* [Zainstalować system Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md). Użyj dostosowywania klastra, aby zainstalować system Giraph w klastrach usługi HDInsight Hadoop. System Giraph umożliwia przetwarzanie za pomocą usługi Hadoop wykresów i mogą być używane z usługi Azure HDInsight.

* [Instalowanie aplikacji Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md). Dostosowywanie klastra umożliwia instalowanie aplikacji Hue w klastrach usługi HDInsight Hadoop. HUE to zbiór aplikacji sieci Web umożliwiający interakcję z klastrem usługi Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
