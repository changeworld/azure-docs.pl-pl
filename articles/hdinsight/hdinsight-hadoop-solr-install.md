---
title: Użyj akcji skryptu, aby zainstalować Solr na klastra usługi Hadoop - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować klastra usługi HDInsight za pomocą Solr za pomocą akcji skryptu.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: d263f6255eedb9b45b7f0b232e1595197556b7c3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Zainstalować i używać Solr w klastrach HDInsight opartych na systemie Windows

Dowiedz się, jak dostosować klastra usługi HDInsight opartej na systemie Windows za pomocą Solr za pomocą akcji skryptu i sposobu użycia Solr do wyszukiwania danych.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracować tylko z klastrami HDInsight opartych na systemie Windows. HDInsight jest dostępna tylko w systemie Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Dla informacji o korzystaniu z opartą na systemie Linux klastrem Solr, zobacz [instalacji i używania Solr w klastrach HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).


Solr można zainstalować w klastrze (na platformie Hadoop, Storm, HBase, Spark) w usłudze Azure HDInsight dowolnego typu za pomocą *akcji skryptu*. Przykładowy skrypt do zainstalowania Solr w klastrze usługi HDInsight jest dostępna z obiektu blob magazynu Azure w trybie tylko do odczytu w [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

Przykładowy skrypt działa tylko w przypadku klastra HDInsight w wersji 3.1. Aby uzyskać więcej informacji o wersjach klastra usługi HDInsight, zobacz [wersji klastra usługi HDInsight](hdinsight-component-versioning.md).

Przykładowy skrypt używane w tym temacie tworzy klaster Solr opartych na systemie Windows z określonej konfiguracji. Aby skonfigurować klaster Solr z różnych kolekcji, odłamków, schematów, replik, itp., należy odpowiednio zmodyfikować skrypt i Solr plików binarnych.

**Pokrewne artykuły**

* [Zainstalować i używać Solr w klastrach HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Co to jest Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> to platforma wyszukiwania przedsiębiorstwa, która umożliwia wydajne wyszukiwanie pełnotekstowe danych. Gdy Hadoop umożliwia przechowywanie i zarządzania nimi czasach ogromne ilości danych, Apache Solr zapewnia możliwości wyszukiwania, aby szybko odzyskać dane.

## <a name="install-solr-using-portal"></a>Zainstaluj Solr przy użyciu portalu
1. Rozpocznij tworzenie klastra przy użyciu **Utwórz niestandardowy** opcji, zgodnie z opisem w [klastrów utworzyć Hadoop w HDInsight](hdinsight-provision-clusters.md).
2. Na **akcji skryptu** strony kreatora, kliknij przycisk **dodać akcję skryptu** do udostępnienia szczegółów dotyczących akcji skryptu, jak pokazano poniżej:

    ![Aby dostosować klastra, użyj akcji skryptu](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "użyj akcji skryptu, aby dostosować klastra")

    <table border='1'>
        <tr><th>Właściwość</th><th>Wartość</th></tr>
        <tr><td>Name (Nazwa)</td>
            <td>Określ nazwę akcji skryptu. Na przykład <b>zainstalować Solr</b>.</td></tr>
        <tr><td>Identyfikator URI skryptu</td>
            <td>Określ identyfikator URI (Uniform Resource) do skryptu, które jest wywoływane, aby dostosować klastra. Na przykład <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Typ węzła</td>
            <td>Określ węzły, na których uruchomiono skryptu dostosowania. Możesz wybrać <b>we wszystkich węzłach</b>, <b>tylko węzły główne</b>, lub <b>tylko węzłów procesu roboczego</b>.
        <tr><td>Parametry</td>
            <td>Określ parametry, jeśli jest to wymagane przez skrypt. Skrypt do zainstalowania Solr nie wymaga żadnych parametrów, więc można to pole pozostanie puste.</td></tr>
    </table>

    Można dodać więcej niż jedna akcja skryptu, aby zainstalować wiele składników w klastrze. Po dodaniu skryptów, kliknij znacznik wyboru, aby rozpocząć tworzenie klastra.

## <a name="use-solr"></a>Korzystanie z platformy Solr
Musi rozpoczynać się od indeksowanie Solr z niektórych plików danych. Solr można następnie użyć do uruchamiania zapytań wyszukiwania danych indeksowanego. Wykonaj poniższe kroki, aby użyć Solr w klastrze usługi HDInsight:

1. **Przy użyciu protokołu RDP (Remote Desktop) zdalnego w klastrze usługi HDInsight za pomocą Solr zainstalowane**. W portalu Azure włączenie pulpitu zdalnego dla klastra, który został utworzony z Solr zainstalowane, a następnie zdalnego w klastrze. Aby uzyskać instrukcje, zobacz [Connect do klastrów usługi HDInsight za pomocą protokołu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Indeks Solr, przekazując pliki danych**. Indeks Solr, możesz zaznaczyć dokumentów w nim, który może być konieczne wyszukiwania na. Indeks Solr, za pomocą protokołu RDP zdalnego do klastra, przejdź do pulpitu, Otwórz okno wiersza polecenia platformy Hadoop i przejdź do **C:\apps\dist\solr-4.7.2\example\exampledocs**. Uruchom następujące polecenie:

        java -jar post.jar solr.xml monitor.xml

    Zostanie wyświetlony następujące dane wyjściowe do konsoli:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Narzędzie post.jar indeksuje Solr z dwa dokumenty przykładowe, **solr.xml** i **monitor.xml**. Narzędzie post.jar i przykładowe dokumenty są dostępne z instalacją Solr.
3. **Wyszukiwanie w indeksowanej dokumentów za pomocą pulpitu nawigacyjnego Solr**. W sesji protokołu RDP w klastrze usługi HDInsight, Otwórz program Internet Explorer, a następnie uruchom Solr pulpitu nawigacyjnego w **http://headnodehost:8983/solr/#/**. W lewym okienku z **selektora Core** listy rozwijanej, wybierz pozycję **collection1**, a w ramach, kliknij przycisk **zapytania**. Na przykład, aby wybrać i zwraca wszystkie dokumenty w Solr, podaj następujące wartości:

   * W **q** tekst wprowadź  **\*:**\*. Zwróci wszystkie dokumenty, które są indeksowane w Solr. Jeśli chcesz wyszukać określony ciąg znaków w dokumentach, możesz wprowadzić ten ciąg w tym miejscu.
   * W **wt** tekst wybierz format danych wyjściowych. Domyślnie jest **json**. Kliknij przycisk **wykonać kwerendy**.

     ![Aby dostosować klastra, użyj akcji skryptu](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "uruchomić kwerendę na pulpicie nawigacyjnym Solr")

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
4. **Zalecane: Kopii zapasowej danych indeksowanego z Solr do magazynu obiektów Blob Azure skojarzonego z klastrem usługi HDInsight**. Dobrym rozwiązaniem należy wykonać kopię zapasową zindeksowanych danych z węzłów klastra Solr do magazynu obiektów Blob platformy Azure. Wykonaj poniższe kroki, aby to zrobić:

   1. Z sesji protokołu RDP Otwórz program Internet Explorer, a następnie wskaż następujący adres URL:

           http://localhost:8983/solr/replication?command=backup

       Powinna zostać wyświetlona odpowiedź następująco:

           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. W sesji zdalnej, przejdź do {SOLR_HOME}\{kolekcji} \data. Dla klastra utworzone za pomocą przykładowego skryptu, należy to **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. W tej lokalizacji powinna zostać wyświetlona utworzony z nazwą podobne do folderu migawki **migawki.* Sygnatura czasowa ***.
   3. Zip w folderze migawek i przekaż go do magazynu obiektów Blob Azure. Z poziomu wiersza polecenia platformy Hadoop przejdź do lokalizacji folderu migawki za pomocą następującego polecenia:

             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

       To polecenie powoduje skopiowanie migawki do /example/data/w kontenerze w ramach domyślnego konta magazynu skojarzone z klastrem.

## <a name="install-solr-using-aure-powershell"></a>Zainstaluj Solr przy użyciu programu Azure PowerShell
Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Przykład pokazuje, jak zainstalować Spark przy użyciu programu Azure PowerShell. Należy dostosować skrypt, aby użyć [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Zainstaluj Solr przy użyciu zestawu .NET SDK
Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Przykład pokazuje, jak zainstalować Spark przy użyciu zestawu .NET SDK. Należy dostosować skrypt, aby użyć [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Zobacz także
* [Zainstalować i używać Solr w klastrach HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions.md).
* [Zainstalować i używać platformy Spark w klastrach HDInsight][hdinsight-install-spark]: Akcja skryptu próbki o instalowaniu Spark.
* [Zainstalować język R w klastrach HDInsight][hdinsight-install-r]: Akcja skryptu próbki o instalowaniu R.
* [Zainstaluj w klastrach HDInsight Giraph](hdinsight-hadoop-giraph-install.md): Akcja skryptu próbki o instalowaniu Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
