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
ms.openlocfilehash: 1a1cf731678ef7678b740020a4d61725f9a2b32a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221954"
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

- **Potok danych prywatnych i chronionych** (poziomu zabezpieczenia brzegowe):
    - Obwodowej poziom zabezpieczeń, można osiągnąć przy użyciu usługi Azure Virtual Networks, sieciowe grupy zabezpieczeń i usługi bramy

- **Uwierzytelnianie i autoryzacja dostępu do danych**
    - Utwórz przyłączone do domeny klaster usługi HDI przy użyciu usługi Azure Active Directory Domain Services. (Pakiet Enterprise Security)
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

- Utwórz nowy klaster usługi HDI testu przy użyciu najnowszej dostępnej wersji usługi HDI.
- Testowanie w nowym klastrze, aby upewnić się, że zadania i obciążeń działać zgodnie z oczekiwaniami.
- Zmodyfikuj zadania lub aplikacje lub obciążenia, zgodnie z potrzebami.
- Utwórz kopię zapasową wszelkich danych przejściowych przechowywane lokalnie w węzłach klastra.
- Usuń istniejący klaster.
- Tworzenie klastra HDInsight w najnowszej wersji w tej samej podsieci sieci Wirtualnej przy użyciu tego samego magazynu danych i metadanych domyślne jako poprzedniego klastra.
- Importowanie danych przejściowych, która została utworzona kopia zapasowa.
- Rozpoczęcie zadania/kontynuować przetwarzanie za pomocą nowego klastra.

Aby uzyskać więcej informacji, zobacz artykuł: [klastra HDInsight uaktualnienie do nowej wersji](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Poprawka klastrowanie systemów operacyjnych

Jako zarządzana usługa Hadoop HDInsight zajmuje się stosowanie poprawek systemu operacyjnego maszyny wirtualne używane przez klastry HDInsight.

Aby uzyskać więcej informacji, zobacz artykuł: [for HDInsight stosowania poprawek systemu operacyjnego](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Po przeprowadzeniu migracji

1. **Korygowanie aplikacji** — iteracyjne wprowadź niezbędne zmiany do zadań, procesy i skrypty
2. **Wykonaj testy** — iteracyjne wykonywania funkcjonalności i wydajności testów
3. **Optymalizowanie** — rozwiązać wszelkie problemy z wydajnością na podstawie powyższych wyników testów, a następnie ponów test, aby upewnić się, ulepszenia wydajności.

## <a name="appendix-gathering-details-to-prepare-for-a-migration"></a>Dodatek: zbierania informacji do przygotowania do migracji

Ta sekcja zawiera kwestionariuszy szablonów ułatwiających zbieranie ważne informacje dotyczące:

- Wdrażanie w środowisku lokalnym.
- Szczegóły projektu.
- Wymagania dotyczące usługi Azure.

### <a name="on-premises-deployment-questionnaire"></a>Kwestionariusz wdrożenia w środowisku lokalnym

| **Pytanie** | **Przykład** | **Odpowiedź** |
|---|---|---|
|**Temat**: **środowiska**|||
|Typ dystrybucji klastra|Hortonworks, Cloudera, MapR| |
|Wersja dystrybucji klastra|HDP 2.6.5, 5.7 PROGRAMU CDH|
|Dużych składników ekosystemu danych|Systemu plików HDFS, Yarn, Hive, LLAP, Impala, Kudu, bazy danych HBase, Spark, MapReduce, Kafka, dozorcy, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Typy klastrów|Solr jednowarstwową platformy Kafka, Storm, Hadoop, Spark,|
|Liczba klastrów|4|
|Liczba węzłów wzorca|2|
|Liczba węzłów procesu roboczego|100|
|Liczba węzłów brzegowych| 5|
|Całkowita ilość miejsca na dysku|100 TB|
|Konfiguracja węzła głównego|m/y, procesora cpu, dysku itp.|
|Konfiguracja węzły danych|m/y, procesora cpu, dysku itp.|
|Konfiguracja węzłów brzegowych|m/y, procesora cpu, dysku itp.|
|Szyfrowanie systemu plików HDFS?|Yes|
|Wysoka dostępność|Wysokiej dostępności z systemu plików HDFS i magazynu metadanych wysokiej dostępności|
|Odzyskiwanie po awarii / tworzenie kopii zapasowej|Klaster kopii zapasowej?|  
|Systemy, które są zależne od klastra|SQL Server, Teradata, usługa Power BI, bazy danych MongoDB|
|Integracje innych firm|Firmy TABLEAU GridGain Qubole, Informatica, Splunk|
|**Temat**: **zabezpieczeń**|||
|Zabezpieczenia brzegowe|Zapory|
|Klaster uwierzytelnianie i autoryzacja|Active Directory, Ambari, Cloudera Manager bez uwierzytelniania|
|Kontrola dostępu do systemu plików HDFS|  Ręczne, ssh użytkowników|
|Hive, uwierzytelnianie i autoryzacja|Sentry, LDAP, AD przy użyciu protokołu Kerberos, platformy Ranger|
|Inspekcja|Ambari, Nawigator Cloudera platformy Ranger|
|Monitorowanie|Grafitu, zebrane, statsd, Telegraf, InfluxDB|
|Generowanie alertów|Kapacitor, Prometheus, pomocą usługi Datadog|
|Czas przechowywania danych| 3 lata, 5 lat|
|Administratorzy klastra|Wielu administratorów jednego administratora|

### <a name="project-details-questionnaire"></a>Kwestionariusz szczegóły projektu

|**Pytanie**|**Przykład**|**Odpowiedź**|
|---|---|---|
|**Temat**: **obciążeń i częstotliwości**|||
|Zadania MapReduce|10 zadań — dwa razy dziennie||
|Zadania hive|100 zadań — co godzinę||
|Zadania wsadowe platformy Spark|50 zadań — co 15 minut||
|Zadania przesyłania strumieniowego platformy Spark|5 zadań — co 3 minuty||
|Strukturalne zadania przesyłania strumieniowego|5 zadań — co minutę||
|Zadania szkolenia modelu usługi uczenie Maszynowe|2 zadania — raz w tygodniu||
|Języki programowania|Python i Scala, Java||
|Skrypty|Powłoka, języka Python||
|**Temat**: **danych**|||
|Źródła danych|Pliki proste, Json, Kafka, RDBMS||
|Organizowanie danych|Przepływy pracy programu Oozie, powietrza||
|W wyszukiwaniach pamięci|Apache Ignite, redis Cache||
|Miejsca docelowe danych|System plików HDFS, RDBMS, Kafka, MPP ||
|**Temat**: **metadanych**|||
|Typ bazy danych programu hive|MySQL, Postgres||
|Nie. z magazyny metadanych Hive|2||
|Nie. z tabel programu Hive|100||
|Nie. zasady platformy Ranger|20||
|Nie. przepływów pracy programu Oozie|100||
|**Temat**: **skalowania**|||
|Ilość danych, w tym replikacji|100 TB||
|Dzienna ilość pozyskiwania|50 GB||
|Szybkość wzrostu ilości danych|10% rocznie||
|Tempo wzrostu węzłów klastra|5% rocznie
|**Temat**: **klastra wykorzystania**|||
|Średni procent użycia procesora CPU używane|60%||
|Średni procent pamięci używane|75%||
|Używane miejsce na dysku|75%||
|Średni procent sieci używane|25%
|**Temat**: **pracowników**|||
|Nie. administratorów|2||
|Nie. deweloperów|10||
|Nie. użytkownikom końcowym|100||
|Umiejętności|Hadoop, Spark||
|Nie. zasoby dostępne dla wysiłków migracji|2||
|**Temat**: **ograniczenia**|||
|Bieżące ograniczenia|Opóźnienie jest duże||
|Bieżące problemy.|Problem współbieżności||

### <a name="azure-requirements-questionnaire"></a>Wymagania dotyczące usługi Azure kwestionariusz

|**Temat**: **infrastruktury** |||
|---|---|---|
|**Pytanie**|**Przykład**|**Odpowiedź**|
| Preferowany Region|Wschodnie stany USA||
|Preferowanych sieci wirtualnej?|Yes||
|Wysoka dostępność / odzyskiwanie po awarii potrzebne?|Yes||
|Integracja z innymi usługami w chmurze?|ADF, bazy danych cosmos DB||
|**Temat**: **przenoszenia danych**  |||
|Preferencji ładowania początkowego|Narzędzia DistCp, Data box, ADF, platforma WANDisco||
|Przyrost transferu danych|Narzędzia DistCp, narzędzia AzCopy||
|Trwającą dane przyrostowe transferu|Narzędzia DistCp, Sqoop||
|**Temat**: **monitorowanie i alerty** |||
|Za pomocą monitorowania platformy Azure i integracja programu Vs alertów monitorowania innych firm|Korzystanie z systemu Azure, monitorowanie i alerty||
|**Temat**: **preferencji zabezpieczeń** |||
|Potok danych prywatnych i chronionych?|Yes||
|Klastra dołączono do domeny (ESPP)?|     Yes||
|W środowisku lokalnym AD Sync do chmury?|     Yes||
|Nie. użytkowników usługi AD do synchronizacji?|          100||
|OK, aby zsynchronizować hasła do chmury?|    Yes||
|Użytkownicy tylko w chmurze?|                 Yes||
|Wymagane uwierzytelnianie wieloskładnikowe?|                       Nie|| 
|Wymagania dotyczące autoryzacji danych?|  Yes||
|Kontrola dostępu oparta na rolach?|        Yes||
|Inspekcja potrzebne?|                  Yes||
|Szyfrowanie danych w spoczynku?|          Yes||
|Szyfrowanie danych podczas przesyłania?|       Yes||
|**Temat**: **preferencje Re architektury** |||
|Pojedynczy klaster, a określone typy klastrów|Określone typy klastrów||
|Wspólnie przechowywane Vs zdalnego magazynu?|Magazyn zdalny||
|Mniejszy rozmiar klastra jako dane są przechowywane zdalnie?|Mniejszy rozmiar klastra||
|Korzystać z wielu mniejszych klastrów, a nie pojedynczego dużego klastra?|Użyj wielu mniejszych klastrów||
|Użyj zdalnego magazynu metadanych?|Yes||
|Udostępnianie magazyny metadanych między różnymi klastrami?|Yes||
|Dekonstruować obciążenia?|Zastąp zadań Hive za pomocą zadań platformy Spark||
|Na użytek ADF organizowanie danych?|Nie||
|Vs HDI HDP iaas?|USŁUGI HDI||

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)