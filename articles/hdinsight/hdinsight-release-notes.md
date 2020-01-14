---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 37b49b3fbe91d199b13f548e8aaf72a6a2f0f848
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895296"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-01092019"></a>Data wydania: 01/09/2019

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w ciągu kilku dni.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł dotyczący obsługi [wersji usługi HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nowe funkcje
### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2
Transport Layer Security (TLS) i SSL (SSL) to protokoły kryptograficzne zapewniające bezpieczeństwo komunikacji w sieci komputerowej. Dowiedz się więcej o protokole [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Usługa HDInsight używa protokołu TLS 1,2 na publicznych punktach końcowych HTTPs, ale protokół TLS 1,1 jest nadal obsługiwany dla zgodności z poprzednimi wersjami 

W tej wersji klienci mogą zrezygnować z protokołu TLS 1,2 enformence dla wszystkich połączeń za pośrednictwem protokołu TLS 1,2. Nowa właściwość **minSupportedTlsVersion** jest wprowadzana za pośrednictwem szablonu Azure Resource Manager na potrzeby tworzenia klastra. Jeśli właściwość nie jest ustawiona, klaster nadal obsługuje 1,0, 1,1 i 1,2, tak samo jak zachowanie dzisiejsze. Klienci mogą ustawić wartość dla tej właściwości na "1,2", co oznacza, że klaster obsługuje tylko protokół TLS 1,2 lub nowszy. 

### <a name="bring-your-own-key-for-disk-encryption"></a>Przenoszenie własnego klucza do szyfrowania dysków
Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Dane na tych dyskach są domyślnie szyfrowane przez klucze zarządzane przez firmę Microsoft. Począwszy od tej wersji, można Bring Your Own Key (BYOK) na potrzeby szyfrowania dysków i zarządzać nimi przy użyciu Azure Key Vault. Szyfrowanie BYOK jest konfiguracją jednoetapową podczas tworzenia klastra bez dodatkowych kosztów. Zarejestruj się w usłudze HDInsight jako tożsamość zarządzana z Azure Key Vault i Dodaj klucz szyfrowania podczas tworzenia klastra. 

## <a name="deprecation"></a>Przestarzałe
Brak zaniecheń dla tej wersji. Aby przygotować się do nadchodzących przestarzałych, zobacz [nadchodzące zmiany](#upcoming-changes).

## <a name="behavior-changes"></a>Zmiany zachowania
Brak zmian w zachowaniu dla tej wersji. Aby przygotować się do nadchodzących zmian, zobacz [nadchodzące zmiany](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Minimalna 4-rdzeniowa maszyna wirtualna jest wymagana dla węzła głównego. 
Aby węzeł główny zapewniał wysoką dostępność i niezawodność klastrów usługi HDInsight, wymagana jest co najmniej 4 rdzenie maszyny wirtualnej. Począwszy od 6 kwietnia 2020, klienci mogą wybrać tylko 4-rdzeniową lub wyższą maszynę wirtualną jako węzeł główny dla nowych klastrów usługi HDInsight. Istniejące klastry będą nadal działać zgodnie z oczekiwaniami. 

### <a name="esp-spark-cluster-node-size-change"></a>Zmiana rozmiaru węzła klastra Spark w ramach platformy ESP 
W nadchodzącym wydaniu Minimalny dozwolony rozmiar węzła dla klastra ESP Spark zostanie zmieniony na Standard_D13_V2. Maszyny wirtualne z serii A mogą powodować problemy z klastrem ESP ze względu na stosunkowo niską pojemność procesora i pamięci. Maszyny wirtualne z serii A są przestarzałe do tworzenia nowych klastrów ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. W nadchodzącym wydaniu Usługa HDInsight będzie używać zestawów skalowania maszyn wirtualnych platformy Azure. Zobacz więcej informacji o zestawach skalowania maszyn wirtualnych platformy Azure.

### <a name="hbase-20-to-21"></a>HBase 2,0 do 2,1
W nadchodzącym wydaniu usługi HDInsight 4,0 wersja HBase zostanie uaktualniona z wersji 2,0 do 2,1.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 AD HDInsight 3,6 można znaleźć tutaj.
