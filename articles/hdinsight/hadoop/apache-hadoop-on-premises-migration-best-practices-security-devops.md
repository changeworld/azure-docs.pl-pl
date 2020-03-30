---
title: 'Zabezpieczenia: migrowanie lokalnego usługi Apache Hadoop do usługi Azure HDInsight'
description: Poznaj najważniejsze wskazówki dotyczące zabezpieczeń i devops dotyczące migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974401"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najważniejsze rozwiązania dotyczące zabezpieczeń i programów DevOps

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń i DevOps w systemach Azure HDInsight. Jest to część serii, która zapewnia najlepsze rozwiązania ułatwiające migrację lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Zabezpieczanie klastra i zarządzanie nimi za pomocą pakietu zabezpieczeń przedsiębiorstwa

Pakiet zabezpieczeń przedsiębiorstwa (ESP) obsługuje uwierzytelnianie oparte na usłudze Active Directory, obsługę wielu użytkowniczek i kontrolę dostępu opartą na rolach. Po wybraniu opcji ESP klaster HDInsight jest przyłączany do domeny usługi Active Directory, a administrator przedsiębiorstwa może skonfigurować kontrolę dostępu opartą na rolach (RBAC) dla zabezpieczeń gałęzi Apache przy użyciu apache Ranger. Administrator może również kontrolować dostęp do danych przez pracowników i wszelkie zmiany wprowadzone w zasadach kontroli dostępu.

Protokół ESP jest dostępny na następujących typach klastra: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka i Interactive Query (Hive LLAP).

Aby wdrożyć klaster HDInsight przyłączony do domeny, należy wykonać następujące czynności:

- Wdrażanie usługi Azure Active Directory (AAD) przez przekazanie nazwy domeny.
- Wdrażanie usług domenowych Active Directory platformy Azure (AAD DS).
- Utwórz wymaganą sieć wirtualną i podsieć.
- Wdrażanie maszyny Wirtualnej w sieci wirtualnej w celu zarządzania usługą AAD DS.
- Dołącz do maszyny Wirtualnej do domeny.
- Instalowanie narzędzi AD i DNS.
- Niech administrator usług AAD DS utworzy jednostkę organizacyjną (OU).
- Włącz LDAPS dla AAD DS.
- Utwórz konto usługi w usłudze Azure Active Directory z delegowanym & uprawnienia administratora do zapisu do konsoli organizacyjnej, aby można było. To konto usługi można następnie dołączyć do komputerów do domeny i umieścić podmioty komputera w jednostkach organizacyjnych. Można również utworzyć jednostki usługi w ramach jednostki organizacyjnej, które można określić podczas tworzenia klastra.

    > [!Note]
    > Konto usługi nie musi być kontem administratora domeny usługi AD.

- Wdrażanie klastra USŁUGI HDInsight ESP przez ustawienie następujących parametrów:

    |Parametr |Opis |
    |---|---|
    |Nazwa domeny|Nazwa domeny skojarzona z usługą Azure AD DS.|
    |Nazwa użytkownika domeny|Konto usługi w domenie zarządzanej przez kontroler domeny kontrolera domeny `hdiadmin@contoso.onmicrosoft.com`usługi Azure AD DS utworzone w poprzedniej sekcji, na przykład: . Ten użytkownik domeny będzie administratorem tego klastra HDInsight.|
    |Hasło domeny|Hasło konta usługi.|
    |Jednostka organizacyjna|Wyróżniająca się nazwa ou, której chcesz użyć z klastrem HDInsight, na przykład: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Jeśli ta ou nie istnieje, klaster HDInsight próbuje utworzyć zespół operacyjny przy użyciu uprawnień konta usługi.|
    |LDAPS URL|na przykład `ldaps://contoso.onmicrosoft.com:636`.|
    |Dostęp do grupy użytkowników|Grupy zabezpieczeń, których użytkownicy mają być synchronizowani `HiveUsers`z klastrem, na przykład: . Jeśli chcesz określić wiele grup użytkowników, oddziel je średnikiem ';'. Grupy muszą istnieć w katalogu przed utworzeniem klastra ESP.|

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Wprowadzenie do zabezpieczeń Apache Hadoop z klastrami HDInsight przyłączonych do domeny](../domain-joined/hdinsight-security-overview.md)
- [Planowanie klastrów Apache Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurowanie klastra hdinsight przyłączonych do domeny przy użyciu usług domenowych Usługi domenowe Active Directory azure](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurowanie zasad gałęzi Apache w udziale usługi HDInsight przyłączonych do domeny](../domain-joined/apache-domain-joined-run-hive.md)
- [Uruchamianie apache Oozie w klastrach HDInsight Hadoop przyłączonych do domeny](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Wdrażanie zabezpieczeń przedsiębiorstwa od końca do końca

Kompleksowe zabezpieczenia przedsiębiorstwa można osiągnąć za pomocą następujących formantów:

**Prywatny i chroniony potok danych (zabezpieczenia na poziomie obwodu)**
    - Zabezpieczenia na poziomie obwodu można osiągnąć za pośrednictwem sieci wirtualnych platformy Azure, grup zabezpieczeń sieciowych i usługi bramy.

**Uwierzytelnianie i autoryzacja dostępu do danych**
    - Utwórz klaster HDInsight przyłączony do domeny przy użyciu Usług domenowych Active Directory azure. (Pakiet zabezpieczeń przedsiębiorstwa).
    - Użyj Ambari, aby zapewnić użytkownikom usługi AD dostęp oparty na rolach do zasobów klastra.
    - Użyj Apache Ranger, aby ustawić zasady kontroli dostępu dla hive na poziomie tabeli / kolumny / wiersza.
    - Dostęp SSH do klastra może być ograniczony tylko do administratora.

**Inspekcja**
    - Wyświetlanie i raportowanie całego dostępu do zasobów i danych klastra HDInsight.
    - Wyświetlanie i raportowanie wszystkich zmian w zasadach kontroli dostępu.

**Szyfrowania**
    - Przezroczyste szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta.
    - W szyfrowaniu tranzytowym przy użyciu szyfrowania po stronie klienta, https i TLS.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Omówienie sieci wirtualnych platformy Azure](../../virtual-network/virtual-networks-overview.md)
- [Omówienie grup zabezpieczeń sieciowej platformy Azure](../../virtual-network/security-overview.md)
- [Wirtualne sieci równorzędne platformy Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Przewodnik po zabezpieczeniach usługi Azure Storage](../../storage/blobs/security-recommendations.md)
- [Szyfrowanie usługi Azure Storage w spoczynku](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Użyj monitorowania & alertów

Aby uzyskać więcej informacji, zobacz artykuł:

[Omówienie usługi Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Regularnie uaktualniaj do najnowszej wersji HDInsight, aby korzystać z najnowszych funkcji. Do uaktualnienia klastra do najnowszej wersji można wykonać następujące kroki:

1. Utwórz nowy klaster TEST HDInsight przy użyciu najnowszej dostępnej wersji HDInsight.
1. Przetestuj nowy klaster, aby upewnić się, że zadania i obciążenia działają zgodnie z oczekiwaniami.
1. W razie potrzeby modyfikuj zadania lub aplikacje lub obciążenia.
1. Śmiuj wszystkie dane przejściowe przechowywane lokalnie w węzłach klastra.
1. Usuń istniejący klaster.
1. Utwórz klaster najnowszej wersji usługi HDInsight w tej samej podsieci sieci wirtualnej, używając tych samych domyślnych danych i meta magazynu, co poprzedni klaster.
1. Zaimportuj wszystkie dane przejściowe, których kopię zapasową wykonano.
1. Rozpocznij zadania/kontynuuj przetwarzanie przy użyciu nowego klastra.

Aby uzyskać więcej informacji, zobacz artykuł: [Uaktualnianie klastra HDInsight do nowej wersji](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Systemy operacyjne klastra poprawek

Jako zarządzana usługa Hadoop, usługa HDInsight zajmuje się łataniem systemu operacyjnego maszyn wirtualnych używanych przez klastry HDInsight.

Aby uzyskać więcej informacji, zobacz artykuł: [Łatanie systemu operacyjnego dla usługi HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Po migracji

1. **Korygowanie aplikacji** — iteracyjne wprowadzanie niezbędnych zmian w zadaniach, procesach i skryptach.
2. **Wykonaj testy** — iteracyjne uruchamianie testów funkcjonalnych i wydajności.
3. **Optymalizuj — rozwiąż** wszelkie problemy z wydajnością na podstawie powyższych wyników testów, a następnie ponownie przetestuj, aby potwierdzić poprawę wydajności.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
