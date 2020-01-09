---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435419"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Azure HDInsight jest jedną z najpopularniejszych usług używanych przez klientów korporacyjnych na potrzeby analizy w narzędziach typu open source Apache Hadoop i Apache Spark na platformie Azure.

## <a name="release-date-12172019"></a>Data wydania: 12/17/2019

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł dotyczący obsługi [wersji usługi HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nowe funkcje

### <a name="service-tags"></a>Tagi usługi
Tagi usług upraszczają zabezpieczenia maszyn wirtualnych platformy Azure i sieci wirtualnych platformy Azure, umożliwiając łatwe ograniczanie dostępu sieciowego do usług platformy Azure. Możesz użyć tagów usługi w regułach sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby zezwalać na ruch do określonej usługi platformy Azure globalnie lub w danym regionie platformy Azure. Platforma Azure zapewnia konserwację adresów IP poszczególnych tagów. Tagi usługi HDInsight dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) to grupy adresów IP dla usług kondycji i zarządzania. Te grupy pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń. Klienci usługi HDInsight mogą włączyć tag usług za pomocą witryny Azure Portal, programu PowerShell i interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Tagi usług sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Niestandardowa baza danych Ambari
Usługa HDInsight umożliwia teraz korzystanie z własnej bazy danych SQL dla platformy Apache Ambari. Tę niestandardową bazę danych Ambari można skonfigurować z poziomu Azure Portal lub przy użyciu szablonu usługi Resource Manager.  Ta funkcja umożliwia wybranie odpowiedniej bazy danych SQL na potrzeby przetwarzania i pojemności. Możesz również łatwo uaktualnić, aby dopasować wymagania dotyczące rozwoju firmy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrów usługi HDInsight za pomocą niestandardowej bazy danych Ambari](hdinsight-custom-ambari-db.md).

![Niestandardowa baza danych Ambari](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Przestarzałe
Brak zaniecheń dla tej wersji. Aby przygotować się do nadchodzących przestarzałych, zobacz [nadchodzące zmiany](#upcoming-changes).

## <a name="behavior-changes"></a>Zmiany zachowania
Brak zmian w zachowaniu dla tej wersji. Aby przygotować się do nadchodzących zmian w zachowaniu, zobacz [nadchodzące zmiany](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Transport Layer Security (TLS) 1,2
Transport Layer Security (TLS) i SSL (SSL) to protokoły kryptograficzne zapewniające bezpieczeństwo komunikacji w sieci komputerowej. Aby uzyskać więcej informacji, zobacz [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Klastry usługi Azure HDInsight akceptują połączenia protokołu TLS 1,2 na publicznych punktach końcowych HTTPS, więc protokół TLS 1,1 jest nadal obsługiwany w celu zapewnienia zgodności ze starszymi klientami.

Począwszy od kolejnej wersji, będzie można wybrać i skonfigurować nowe klastry usługi HDInsight do akceptowania tylko połączeń TLS 1,2. 

W dalszej części roku, począwszy od 6/30/2020, usługa Azure HDInsight wymusi dla wszystkich połączeń HTTPS wartość TLS 1,2 lub nowszą. Zalecamy, aby upewnić się, że wszyscy klienci są gotowi do obsługi protokołu TLS 1,2 lub jego nowszych wersji.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Rozpoczynając od Feburary 2020 (dokładna data zostanie przekazana później), Usługa HDInsight będzie używać zestawów skalowania maszyn wirtualnych platformy Azure. Zobacz więcej informacji o [zestawach skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Zmiana rozmiaru węzła klastra Spark w ramach platformy ESP 
W nadchodzącym wydaniu:
- Minimalny dozwolony rozmiar węzła dla klastra ESP Spark zostanie zmieniony na Standard_D13_V2. 
- Maszyny wirtualne z serii a są przestarzałe do tworzenia nowych klastrów ESP, ponieważ maszyny wirtualne z serii A mogą powodować problemy z klastrem ESP ze względu na stosunkowo niską pojemność procesora i pamięci.

### <a name="hbase-20-to-21"></a>HBase 2,0 do 2,1
W nadchodzącym wydaniu usługi HDInsight 4,0 wersja HBase zostanie uaktualniona z wersji 2,0 do 2,1.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Rozszerzona obsługa usługi HDInsight 3,6 do 31 grudnia 2020. Więcej szczegółów można znaleźć w temacie [obsługiwane wersje usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

Brak zmian wersji składnika usługi HDInsight 4,0.

Apache Zeppelin w usłudze HDInsight 3,6:0.7.0--> 0.7.3. 

Najbardziej aktualne wersje składników można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="new-regions"></a>Nowe regiony

### <a name="uae-north"></a>Północne Zjednoczone Emiraty Arabskie
Adresy IP zarządzania w regionie Zjednoczone Emiraty Arabskie to: `65.52.252.96` i `65.52.252.97`.
