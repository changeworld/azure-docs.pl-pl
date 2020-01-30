---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 9d484afb1d80ee6b110438cc3ddea1d3d67ad999
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844687"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-01092020"></a>Data wydania: 01/09/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w ciągu kilku dni.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł dotyczący obsługi [wersji usługi HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nowe funkcje
### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2
Transport Layer Security (TLS) i SSL (SSL) to protokoły kryptograficzne zapewniające bezpieczeństwo komunikacji w sieci komputerowej. Dowiedz się więcej o protokole [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Usługa HDInsight używa protokołu TLS 1,2 na publicznych punktach końcowych HTTPs, ale protokół TLS 1,1 jest nadal obsługiwany dla zgodności z poprzednimi wersjami 

W tej wersji klienci mogą zrezygnować z protokołu TLS 1,2 tylko dla wszystkich połączeń za pomocą publicznego punktu końcowego klastra. Aby to umożliwić, wprowadzono nową właściwość **minSupportedTlsVersion** i można ją określić podczas tworzenia klastra. Jeśli właściwość nie jest ustawiona, klaster nadal obsługuje protokoły TLS 1,0, 1,1 i 1,2, które są takie same jak bieżące zachowanie. Klienci mogą ustawić wartość dla tej właściwości na "1,2", co oznacza, że klaster obsługuje tylko protokół TLS 1,2 lub nowszy. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej — Transport Layer Security](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Przenoszenie własnego klucza do szyfrowania dysków
Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Dane na tych dyskach są domyślnie szyfrowane przez klucze zarządzane przez firmę Microsoft. Począwszy od tej wersji, można Bring Your Own Key (BYOK) na potrzeby szyfrowania dysków i zarządzać nimi przy użyciu Azure Key Vault. Szyfrowanie BYOK jest konfiguracją jednoetapową podczas tworzenia klastra bez dodatkowych kosztów. Zarejestruj się w usłudze HDInsight jako tożsamość zarządzana z Azure Key Vault i Dodaj klucz szyfrowania podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

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

## <a name="known-issues"></a>Znane problemy

Od 24 stycznia 2020 istnieje aktywny problem, w którym podczas próby użycia notesu Jupyter może wystąpić błąd. Wykonaj poniższe kroki, aby rozwiązać ten problem. Możesz również odwołać się do tego [wpisu w witrynie MSDN](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight) lub tego [wpisu StackOverflow](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103) , aby uzyskać aktualne informacje lub zadać dodatkowe pytania. Ta strona zostanie zaktualizowana, gdy problem zostanie rozwiązany.

**Błędy**

* ValueError: nie można skonwertować notesu do wersji v5, ponieważ ta wersja nie istnieje
* Błąd podczas ładowania notesu wystąpił nieznany błąd podczas ładowania tego notesu. Ta wersja może ładować formaty notesu w wersji 4 lub starszej

**Przyczyna** 

Plik _version. PR w klastrze został zaktualizowany do 5. x. x zamiast 4.4. x. # #.

**Rozwiązanie**

W przypadku utworzenia nowego notesu Jupyter i otrzymania jednego z wymienionych powyżej błędów wykonaj następujące kroki, aby rozwiązać ten problem.

1. Otwórz Ambari w przeglądarce internetowej, przechodząc do https://CLUSTERNAME.azurehdinsight.net, gdzie CLUSTERname jest nazwą klastra.
1. W Ambari, w menu po lewej stronie kliknij pozycję **Jupyter**, następnie w obszarze **Akcje usługi**kliknij pozycję **Zatrzymaj**.
1. Protokół SSH do klastra węzła głównego, gdzie działa usługa Jupyter.
1. Otwórz następujący plik/usr/bin/Anaconda/lib/python2.7/site-packages/nbformat/_version. PR w trybie sudo.
1. Istniejący wpis powinien wyglądać podobnie do następującego kodu: 

    version_info = (5, 0, 3)

    Zmodyfikuj wpis, aby: 
    
    version_info = (4, 4, 0)
1. Zapisz plik.
1. Wróć do Ambari i w obszarze **Akcje usługi**kliknij pozycję **Uruchom ponownie wszystkie**.
