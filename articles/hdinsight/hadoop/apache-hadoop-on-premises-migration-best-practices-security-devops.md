---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — zabezpieczenia i najlepsze rozwiązania metodyki DevOps
description: Dowiedz się, zabezpieczeń i najlepsze rozwiązania metodyki DevOps dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 9489d6e8780a30c5c54ee307d6c45c4bc2eb0e5d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419286"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — zabezpieczenia i najlepsze rozwiązania metodyki DevOps

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń i metodyki DevOps w systemach usługi Azure HDInsight. Jest częścią serii, która zapewnia najlepsze rozwiązania w celu ułatwienia migrowania lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="use-the-enterprise-security-package-to-secure-and-govern-the-cluster"></a>Pakiet Enterprise Security umożliwia bezpieczne i określają sposób klastra

Pakiet zabezpieczeń przedsiębiorstwa (ESP) obsługuje uwierzytelnianie oparte na usłudze Active Directory, obsługa wielu użytkowników i kontroli dostępu opartej na rolach. Z wybraną opcją ESP klaster HDInsight jest przyłączony do domeny usługi Active Directory, a administrator przedsiębiorstwa można skonfigurować kontroli dostępu opartej na rolach (RBAC) dla zabezpieczeń Hive przy użyciu struktury Apache Ranger. Administrator może również przeprowadzać inspekcje dostępu do danych przez pracowników oraz wszystkich zmian zasad kontroli dostępu.

ESP funkcje są obecnie dostępne w wersji zapoznawczej i są dostępne tylko na następujące typy klastrów: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka i zapytanie interakcyjne Apache.

Aby wdrożyć klaster HDInsight przyłączone do domeny, należy użyć następujące czynności:

- Wdrażanie usługi Azure Active Directory (AAD), przekazując nazwy domeny
- Wdrażanie usługi Azure Active Directory Domain Services (DS usługi AAD)
- Tworzenie wymaganych sieci wirtualnej i podsieci
- Wdrażanie maszyny Wirtualnej w sieci wirtualnej do zarządzania DS usługi AAD
- Przyłącz maszynę Wirtualną do domeny
- Instalowanie usługi AD i narzędzia systemu DNS
- Poproś administratora DS usługi AAD, utworzenie jednostki organizacyjnej (OU)
- Włączanie protokołu LDAPS dla DS usługi AAD
- Utwórz konto usługi w usłudze Azure Active Directory za pomocą delegowanego odczytu & admin uprawnienia do zapisu w jednostce Organizacyjnej, tak, że jest to możliwe. To konto usługi można, a następnie dołączysz maszyny do domeny i umieścić podmiotów maszyn w jednostce Organizacyjnej. Można również utworzyć jednostki usługi w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.

    > [!Note]
    > Konto usługi nie musi być kontem administratora domeny usługi AD

- Wdrażanie klastra HDInsight ESP, ustawiając następujące parametry:
    - **Nazwa domeny**: nazwa domeny, która jest skojarzona z usługi Azure AD DS.
    - **Domena nazwa użytkownika**: konto usługi w usłudze Azure AD zarządzane przez kontroler domeny DS domeny utworzonego w poprzedniej sekcji, na przykład: `hdiadmin@contoso.onmicrosoft.com`. Ten użytkownik domeny będzie administratorem tego klastra HDInsight.
    - **Hasło domeny**: hasło konta usługi.
    - **Jednostka organizacyjna**: nazwa wyróżniająca jednostki organizacyjnej, którego chcesz użyć z klastrem usługi HDInsight, na przykład: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Jeśli nie ma tej jednostki Organizacyjnej, klaster HDInsight próbuje utworzyć jednostkę Organizacyjną, korzystając z uprawnień konta usługi.
    - **Adres URL protokołu LDAPS**: na przykład `ldaps://contoso.onmicrosoft.com:636`.
    - **Dostęp do grupy użytkowników**: grup zabezpieczeń, której użytkownicy mają być synchronizowane z klastrem, na przykład: `HiveUsers`. Jeśli chcesz określić wiele grup użytkowników, oddziel je średnikami ";". Grupy muszą istnieć w katalogu przed utworzeniem klastra ESP.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Wprowadzenie do zabezpieczeń usługi Hadoop przy użyciu klastrów HDInsight przyłączone do domeny](../domain-joined/apache-domain-joined-introduction.md)
- [Planowanie Azure klastrów Hadoop przyłączonych do domeny w HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurowanie klastra HDInsight przyłączone do domeny za pomocą usługi Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronizowanie użytkowników usługi Azure Active Directory do klastra usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurowanie zasad usługi Hive HDInsight przyłączone do domeny](../domain-joined/apache-domain-joined-run-hive.md)
- [Uruchamiaj klastry Apache Oozie w przyłączonym do domeny usługi HDInsight Hadoop](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security-management"></a>Implementowanie enterprise kompleksowe zarządzanie zabezpieczeniami

Kompleksowa Usługa zabezpieczeń przedsiębiorstwa można osiągnąć przy użyciu następujących formantów:

- **Potok danych prywatnych i chronionych (granicznej zabezpieczeń na poziomie)**
    - Obwodowej poziom zabezpieczeń, można osiągnąć przy użyciu usługi Azure Virtual Networks, sieciowe grupy zabezpieczeń i usługi bramy

- **Uwierzytelnianie i autoryzacja dostępu do danych**
    - Tworzenie klastra HDInsight przyłączone do domeny przy użyciu usługi Azure Active Directory Domain Services. (Pakiet Enterprise Security)
    - Używać narzędzia Ambari w celu zapewnienia opartej na rolach dostęp do zasobów klastra dla użytkowników usługi AD
    - Użyj struktury Apache Ranger do ustawiania zasad kontroli dostępu dla usługi Hive w tabeli / kolumny / wiersz poziom.
    - Dostęp SSH do klastra może być ograniczona tylko do administratora.

- **Inspekcja**
    - Wyświetl i raportować wszystkie przypadki dostępu do danych i zasobów klastra usługi HDInsight.
    - Wyświetlanie i raportować wszystkie zmiany wprowadzane do zasad kontroli dostępu

- **Szyfrowanie**
    - Przezroczyste szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta.
    - W przypadku szyfrowania przesyłania przy użyciu szyfrowania po stronie klienta, https i protokołu TLS

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Omówienie sieci wirtualnych na platformie Azure](../../virtual-network/virtual-networks-overview.md)
- [Omówienie sieciowych grup zabezpieczeń usługi Azure](../../virtual-network/security-overview.md)
- [Komunikacja równorzędna sieci wirtualnych platformy Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Przewodnik po zabezpieczeniach usługi Azure storage](../../storage/common/storage-security-guide.md)
- [Szyfrowanie usługi Azure Storage w spoczynku](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Monitorowanie użycia i alerty

Aby uzyskać więcej informacji zobacz artykuł:

[Omówienie usługi Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Regularnie uaktualnienia do najnowszej wersji HDInsight, aby korzystać z zalet najnowszych funkcji. Poniższe kroki, może służyć do uaktualnienia klastra do najnowszej wersji:

1. Utwórz nowy klaster HDInsight testu przy użyciu najnowszej dostępnej wersji HDInsight.
1. Testowanie w nowym klastrze, aby upewnić się, że zadania i obciążeń działać zgodnie z oczekiwaniami.
1. Zmodyfikuj zadania lub aplikacje lub obciążenia, zgodnie z potrzebami.
1. Utwórz kopię zapasową wszelkich danych przejściowych przechowywane lokalnie w węzłach klastra.
1. Usuń istniejący klaster.
1. Tworzenie klastra HDInsight w najnowszej wersji w tej samej podsieci sieci Wirtualnej przy użyciu tego samego magazynu danych i metadanych domyślne jako poprzedniego klastra.
1. Importowanie danych przejściowych, która została utworzona kopia zapasowa.
1. Rozpoczęcie zadania/kontynuować przetwarzanie za pomocą nowego klastra.

Aby uzyskać więcej informacji, zobacz artykuł: [klastra HDInsight uaktualnienie do nowej wersji](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Poprawka klastrowanie systemów operacyjnych

Jako zarządzana usługa Hadoop HDInsight zajmuje się stosowanie poprawek systemu operacyjnego maszyny wirtualne używane przez klastry HDInsight.

Aby uzyskać więcej informacji, zobacz artykuł: [for HDInsight stosowania poprawek systemu operacyjnego](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Po przeprowadzeniu migracji

1. **Korygowanie aplikacji** — iteracyjne wprowadź niezbędne zmiany do zadań, procesów i skryptów.
2. **Wykonaj testy** — iteracyjne wykonywania funkcjonalności i wydajności testów.
3. **Optymalizowanie** — rozwiązać wszelkie problemy z wydajnością na podstawie powyższych wyników testów, a następnie ponów test, aby upewnić się, ulepszenia wydajności.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)