---
title: Użyj akcji skryptu, aby zainstalować platformę Solr w klastrze usługi Hadoop — Azure
description: Dowiedz się, jak dostosować klastra HDInsight z platformy Solr za pomocą akcji skryptu.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 3f382aab5a00030b922fb890e82110454e7f3839
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384500"
---
# <a name="install-and-use-apache-solr-on-windows-based-hdinsight-clusters"></a>Instalowanie i używanie Apache Solr w klastrach HDInsight z systemem Windows

Dowiedz się jak dostosować klastra HDInsight z systemem Windows przy użyciu Apache Solr za pomocą akcji skryptu oraz korzystanie z platformy Solr wyszukiwanie danych.

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie działają tylko z klastrami HDInsight z systemem Windows. HDInsight jest dostępna tylko na Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Aby uzyskać informacje dotyczące użycia platformy Solr w klastrze opartych na systemie Linux, zobacz [instalacji i używania Apache Solr w klastrach usługi Hadoop w usłudze HDinsight (Linux)](hdinsight-hadoop-solr-install-linux.md).


Możesz zainstalować platformę Solr w dowolny typ klastra (Hadoop, Storm, HBase, Spark) w usłudze Azure HDInsight, za pomocą *akcji skryptu*. Przykładowy skrypt, aby zainstalować platformę Solr w klastrze usługi HDInsight jest dostępna tylko do odczytu z usługi Azure storage blob pod [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

Przykładowy skrypt działa tylko w przypadku klastra HDInsight w wersji 3.1. Aby uzyskać więcej informacji na temat wersji klastra HDInsight, zobacz [wersji klastra HDInsight](hdinsight-component-versioning.md).

Przykładowy skrypt używaną w tym temacie tworzy klaster z systemem Windows z platformy Solr przy użyciu określonej konfiguracji. Aby skonfigurować klaster Solr przy użyciu różnych kolekcjach, fragmentów, schematów, replik, itp., należy odpowiednio zmodyfikować skrypt i pliki binarne Solr.

**Powiązane artykuły**

* [Instalowanie i używanie Apache Solr w klastrach usługi Hadoop w usłudze HDinsight (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Tworzenie klastrów Hadoop w HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów HDInsight.
* [Dostosowywanie klastrów HDInsight za pomocą akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat dostosowywania klastrów HDInsight za pomocą akcji skryptu.
* [Tworzenie akcji skryptu skryptów dla HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Co to jest Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> to platforma wyszukiwania dla przedsiębiorstw, która umożliwia zaawansowane wyszukiwanie pełnotekstowe danych. Gdy Hadoop umożliwia przechowywanie i zarządzanie nimi ogromnych ilości danych, Apache Solr udostępnia możliwości wyszukiwania, aby szybko odzyskać dane.

## <a name="install-solr-using-portal"></a>Zainstalować platformę Solr przy użyciu portalu
1. Rozpocznij tworzenie klastra przy użyciu **tworzenie niestandardowe** opcji, zgodnie z opisem w [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-provision-clusters.md).
2. Na **akcji skryptu** strony kreatora, kliknij przycisk **Dodaj akcję skryptu** dostarczanie szczegółowych informacji o akcji skryptu, jak pokazano poniżej:

    ![Użyć akcji skryptu, aby dostosować klaster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "użyj akcji skryptu, aby dostosować klastra")

    <table border='1'>
        <tr><th>Właściwość</th><th>Wartość</th></tr>
        <tr><td>Name (Nazwa)</td>
            <td>Określ nazwę dla akcji skryptu. Na przykład <b>zainstalować platformę Solr</b>.</td></tr>
        <tr><td>Identyfikator URI skryptu</td>
            <td>Określ identyfikator URI (Uniform Resource) do skryptu, który jest wywoływana w celu dostosowania do klastra. Na przykład <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Typ węzła</td>
            <td>Określ węzły, na których jest uruchamiany skrypt dostosowywania. Możesz wybrać <b>wszystkie węzły</b>, <b>tylko węzłami głównymi</b>, lub <b>węzłów procesu roboczego tylko</b>.
        <tr><td>Parametry</td>
            <td>Określ parametry, jeśli jest to wymagane przez skrypt. Skrypt, aby zainstalować platformę Solr nie wymaga żadnych parametrów, dzięki czemu użytkownik może pozostaw to pole puste.</td></tr>
    </table>

    Możesz dodać więcej niż jedna akcja skryptu do zainstalowania wiele składników w klastrze. Po dodaniu skryptów, kliknij znacznik wyboru, aby rozpocząć tworzenie klastra.

## <a name="use-solr"></a>Korzystanie z platformy Solr
Należy rozpocząć od indeksowanie Solr z niektórych plików danych. Korzystanie z platformy Solr, aby uruchamiać zapytania wyszukiwania na indeksowane dane. Wykonaj poniższe kroki, aby korzystanie z platformy Solr w klastrze usługi HDInsight:

1. **Za pomocą protokołu RDP (Remote Desktop) do zdalnego w klastrze HDInsight zainstalować platformę Solr**. W witrynie Azure portal Włącz pulpit zdalny dla klastra, utworzony za pomocą Solr zainstalowane, a następnie zdalne do klastra. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z klastrami HDInsight przy użyciu protokołu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Solr indeksu, przekazując pliki danych**. Indeksujesz Solr, wystarczy umieścić dokumentów w nim, który może być konieczne do wyszukania. Indeks Solr, za pomocą protokołu RDP zdalnego do klastra, przejdź do pulpitu, otwórz wiersz polecenia usługi Hadoop i przejdź do **C:\apps\dist\solr-4.7.2\example\exampledocs**. Uruchom następujące polecenie:

        java -jar post.jar solr.xml monitor.xml

    W konsoli zostaną wyświetlone następujące dane wyjściowe:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Narzędzie post.jar indeksuje Solr z dwa przykładowe dokumenty, **solr.xml** i **monitor.xml**. Narzędzie post.jar i przykładowe dokumenty są dostępne z instalacją Solr.
3. **Pulpit nawigacyjny platformy Solr umożliwia wyszukiwanie w indeksowanej dokumenty**. W sesji protokołu RDP z klastrem HDInsight, Otwórz program Internet Explorer, a następnie uruchomić Pulpit nawigacyjny platformy Solr w **http://headnodehost:8983/solr/#/**. Z okienka po lewej stronie z **selektor Core** listę rozwijaną, wybierz opcję **collection1**, a w ciągu, kliknij przycisk **zapytania**. Na przykład, aby wybrać i zwrócić wszystkie dokumenty w Solr, podaj następujące wartości:

   * W **q** tekstu wprowadź  **\*:**\*. Spowoduje to zwrócenie wszystkich dokumentów, które są indeksowane w Solr. Jeśli chcesz wyszukać określony ciąg znaków w dokumentach, możesz wprowadzić tutaj tego ciągu.
   * W **wt** tekstu wybierz format danych wyjściowych. Wartość domyślna to **json**. Kliknij przycisk **wykonaj zapytanie**.

     ![Użyć akcji skryptu, aby dostosować klaster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "uruchomić zapytanie na pulpicie nawigacyjnym platformy Solr")

     Dane wyjściowe zwraca dwa dokumenty, używane do indeksowania Solr. Dane wyjściowe podobne do następującego:

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
4. **Zalecane: Kopie zapasowe danych indeksowanych z platformy Solr w usłudze Azure Blob storage skojarzonego z klastrem HDInsight**. Dobrym rozwiązaniem należy wykonać kopię zapasową indeksowane dane z platformy Solr węzłów klastra do usługi Azure Blob storage. Wykonaj poniższe kroki, aby to zrobić:

   1. W sesji protokołu RDP Otwórz program Internet Explorer i wskaż następujący adres URL:

           http://localhost:8983/solr/replication?command=backup

       Powinny pojawić się odpowiedź podobna:
            
      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
          <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
            <str name="status">OK</str>
          </response>
      ```
      
   2. W sesji zdalnej, przejdź do {SOLR_HOME}\{kolekcji} \data. Dla klastra, utworzone za pomocą przykładowego skryptu, powinna to być `C:\apps\dist\solr-4.7.2\example\solr\collection1\data`. W tym miejscu powinien zostać wyświetlony folderu migawek utworzonych za pomocą nazwę podobną do **migawki.\*Sygnatura czasowa**\*.
   
   3. Folder migawki zip i przekaż go do usługi Azure Blob storage. Z wiersza polecenia usługi Hadoop przejdź do lokalizacji folderu migawki za pomocą następującego polecenia:

      ```
      hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data
      ```

   To polecenie kopiuje migawkę do /example/data/znajdującego się w kontenerze w ramach domyślnego konta magazynu skojarzonego z klastrem.

## <a name="install-solr-using-aure-powershell"></a>Zainstalować platformę Solr przy użyciu programu Azure PowerShell
Zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  W przykładzie pokazano, jak zainstalować platformę Apache Spark przy użyciu programu Azure PowerShell. Należy dostosować skrypt, który chcesz użyć [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Zainstalować platformę Solr przy użyciu zestawu .NET SDK
Zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). W przykładzie pokazano, jak zainstalować platformę Apache Spark przy użyciu zestawu .NET SDK. Należy dostosować skrypt, który chcesz użyć [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Zobacz także
* [Instalowanie i używanie Apache Solr w klastrach usługi Hadoop w usłudze HDinsight (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Tworzenie klastrów usługi Apache Hadoop w HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów HDInsight.
* [Dostosowywanie klastrów HDInsight za pomocą akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat dostosowywania klastrów HDInsight za pomocą akcji skryptu.
* [Tworzenie akcji skryptu skryptów dla HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalowanie i używanie platformy Apache Spark w klastrach HDInsight][hdinsight-install-spark]: Przykład akcji skryptu o instalowaniu platformy Spark.
* [Instalowanie systemu Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md): Przykład akcji skryptu o instalowaniu systemu Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
