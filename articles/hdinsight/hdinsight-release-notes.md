---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926420"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Azure HDInsight jest jedną z najpopularniejszych usług używanych przez klientów korporacyjnych na potrzeby analizy w narzędziach typu open source Apache Hadoop i Apache Spark na platformie Azure.

## <a name="release-date-11072019"></a>Data wydania: 11/07/2019

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł dotyczący obsługi [wersji usługi HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nowe funkcje

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (HIB) (wersja zapoznawcza)

Usługa HDInsight Identity Broker (HIB) umożliwia użytkownikom zalogowanie się do usługi Apache Ambari przy użyciu uwierzytelniania wieloskładnikowego (MFA) i uzyskanie wymaganych biletów Kerberos bez konieczności używania skrótów haseł w Azure Active Directory Domain Services (AAD-DS). Obecnie HIB jest dostępna tylko dla klastrów wdrożonych przy użyciu szablonu ARM.

### <a name="kafka-rest-api-proxy-preview"></a>Serwer proxy interfejsu API REST Kafka (wersja zapoznawcza)

Serwer proxy interfejsu API REST usługi Kafka umożliwia wdrożenie jednego kliknięcia serwera proxy REST o wysokiej dostępności z klastrem Kafka za pośrednictwem zabezpieczonej autoryzacji w usłudze AAD i protokołu OAuth. 

### <a name="auto-scale"></a>Automatyczne skalowanie

Funkcja automatycznego skalowania dla usługi Azure HDInsight jest teraz ogólnie dostępna we wszystkich regionach dla typów klastrów Apache Spark i Hadoop. Ta funkcja umożliwia zarządzanie obciążeniami analizy danych Big Data w bardziej wydajny i wydajny sposób. Teraz możesz zoptymalizować korzystanie z klastrów usługi HDInsight i płacisz tylko za to, czego potrzebujesz.

W zależności od wymagań możesz wybierać między automatycznym skalowaniem opartym na obciążeniu i automatycznym skalowaniem opartym na harmonogramie. Automatyczne skalowanie na podstawie obciążenia może skalować rozmiar klastra w górę i w dół w zależności od bieżących potrzeb zasobów, gdy automatyczne skalowanie oparte na harmonogramie może zmienić rozmiar klastra na podstawie wstępnie zdefiniowanego harmonogramu. 

Obsługa automatycznego skalowania dla obciążeń HBase i LLAP jest również publiczną wersją zapoznawczą. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie klastrów usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Szybsze zapisy usługi HDInsight dla platformy Apache HBase 

Funkcja przyspieszonych zapisów korzysta z dysków zarządzanych SSD w warstwie Azure Premium w celu zwiększenia wydajności usługi Apache HBase Write Ahead Log (WAL). Aby uzyskać więcej informacji, zobacz [Usługa Azure HDInsight — przyspieszone zapisy dla oprogramowania Apache HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes).

### <a name="custom-ambari-db"></a>Niestandardowa baza danych Ambari

Usługa HDInsight oferuje teraz nową wydajność, która umożliwia klientom korzystanie z własnych baz danych SQL dla Ambari. Teraz klienci mogą wybrać odpowiednią bazę danych SQL dla Ambari i łatwo ją uaktualnić w oparciu o własne wymagania dotyczące rozwoju firmy. Wdrożenie jest wykonywane z szablonem Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrów usługi HDInsight za pomocą niestandardowej bazy danych Ambari](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Maszyny wirtualne z serii F są teraz dostępne w usłudze HDInsight

Maszyny wirtualne z serii F są dobrym wyborem, aby rozpocząć pracę z usługą HDInsight z wymaganiami dotyczącymi przetwarzania światła. Oferując niższą cenę za godzinę, seria F zapewnia najlepszy w portfolio platformy Azure stosunek ceny do wydajności, określany na podstawie liczby jednostek ACU (Azure Compute Unit) na procesor wirtualny vCPU. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniego rozmiaru maszyny wirtualnej dla klastra usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Przestarzałe

### <a name="g-series-virtual-machine-deprecation"></a>Przestarzałe maszyny wirtualne z serii G
W tej wersji maszyny wirtualne z serii G nie są już dostępne w usłudze HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Dv1 przestarzałą maszynę wirtualną
W tej wersji korzystanie z maszyn wirtualnych Dv1 z usługą HDInsight jest przestarzałe. Wszystkie żądania klienta dotyczące usługi Dv1 będą obsługiwane z użyciem usługi Dv2 automatycznie. Nie ma różnicy cen między maszynami wirtualnymi Dv1 i Dv2.

## <a name="behavior-changes"></a>Zmiany zachowania

### <a name="cluster-managed-disk-size-change"></a>Zmiana rozmiaru dysku zarządzanego przez klaster
Usługa HDInsight zapewnia zarządzane miejsce na dysku z klastrem. W tej wersji rozmiar dysku zarządzanego każdego węzła w nowym klastrze zostanie zmieniony na 128 GB.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wersjach. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Począwszy od grudnia Usługa HDInsight będzie używać zestawów skalowania maszyn wirtualnych platformy Azure. Zobacz więcej informacji o [zestawach skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBase 2,0 do 2,1
W nadchodzącym wydaniu usługi HDInsight 4,0 wersja HBase zostanie uaktualniona z wersji 2,0 do 2,1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Przestarzałe maszyny wirtualne z serii A dla klastra ESP
Maszyny wirtualne z serii A mogą powodować problemy z klastrem ESP z powodu stosunkowo niskiej pojemności procesora i pamięci. W nadchodzącym wydaniu maszyny wirtualne z serii A będą przestarzałe do tworzenia nowych klastrów ESP.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Nie ma żadnej zmiany wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
