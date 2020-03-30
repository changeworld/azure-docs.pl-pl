---
title: 'Korzyści: Migrowanie lokalnego programu Apache Hadoop do usługi Azure HDInsight'
description: Poznaj motywację i korzyści związane z migracją lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 2440b93629416ea73fcf211cbe7bf5a3b72ab2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267327"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — motywacja i korzyści

Ten artykuł jest pierwszym z serii najlepszych rozwiązań dotyczących migracji lokalnych wdrożeń ekosystemów Apache Hadoop do usługi Azure HDInsight. Ta seria artykułów jest dla osób odpowiedzialnych za projektowanie, wdrażanie i migrację rozwiązań Apache Hadoop w usłudze Azure HDInsight. Role, które mogą korzystać z tych artykułów obejmują architektów chmury, administratorów Hadoop i inżynierów DevOps. Deweloperzy oprogramowania, inżynierowie danych i analitycy danych powinni również korzystać z wyjaśnienia, jak różne typy klastrów działają w chmurze.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Dlaczego warto przeprowadzić migrację do usługi Azure HDInsight

Usługa Azure HDInsight to dystrybucja w chmurze składników usługi Hadoop. Usługa Azure HDInsight ułatwia i przyspiesza przetwarzanie ogromnych ilości danych przy jednoczesnej minimalizacji kosztów. HDInsight zawiera najpopularniejsze struktury open source, takie jak:

- Apache Hadoop
- Apache Spark
- Ul Apache z LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Zalety usługi Azure HDInsight nad lokalnym hadoop

- **Niski koszt** — koszty można zmniejszyć, [tworząc klastry na żądanie](../hdinsight-hadoop-create-linux-clusters-adf.md) i płacąc tylko za to, czego używasz. Dane wydzielone zasoby obliczeniowe i magazyn zapewniają elastyczność, utrzymując wolumin danych niezależnie od rozmiaru klastra.

- **Automatyczne tworzenie klastra** — automatyczne tworzenie klastra wymaga minimalnej konfiguracji i konfiguracji. Automatyzacja może służyć do klastrów na żądanie.

- **Zarządzany sprzęt i konfiguracja** — nie musisz martwić się o fizyczny sprzęt lub infrastrukturę za pomocą klastra HDInsight. Wystarczy określić konfigurację klastra, a platforma Azure go konfiguruje.

- **Łatwo skalowalny** — hdinsight umożliwia [skalowanie](../hdinsight-administer-use-portal-linux.md) obciążeń w górę lub w dół. Platforma Azure zajmuje się redystrybucją danych i równoważeniem obciążenia bez przerywania zadań przetwarzania danych.

- **Globalna dostępność** — usługa HDInsight jest dostępna w większej liczbie regionów niż jakakolwiek inna oferta analizy dużych [zbiorów](https://azure.microsoft.com/regions/services/) danych. Usługa Azure HDInsight jest również dostępna w ramach chmur Azure Government, Azure (Chiny) i Azure (Niemcy), dzięki czemu odpowiada na potrzeby Twojego przedsiębiorstwa w najważniejszych obszarach suwerenności.

- **Bezpieczne i zgodne —** usługa HDInsight umożliwia ochronę zasobów danych przedsiębiorstwa za pomocą [usługi Azure Virtual Network,](../hdinsight-plan-virtual-network-deployment.md) [szyfrowania](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)i integracji z [usługą Azure Active Directory.](../domain-joined/hdinsight-security-overview.md) HDInsight spełnia również najpopularniejsze [standardy branżowe](https://azure.microsoft.com/overview/trusted-cloud)i rządowe.

- **Uproszczone zarządzanie wersjami** — usługa Azure HDInsight zarządza wersją składników ekosystemu Hadoop i aktualizuje je. Aktualizacje oprogramowania są zwykle złożonym procesem dla wdrożeń lokalnych.

- **Mniejsze klastry zoptymalizowane pod kątem określonych obciążeń z mniejszą liczbą zależności między składnikami** — typowa lokalna konfiguracja Hadoop używa jednego klastra, który służy wielu celom. Za pomocą usługi Azure HDInsight można tworzyć klastry specyficzne dla obciążenia. Tworzenie klastrów dla określonych obciążeń eliminuje złożoność utrzymywania pojedynczego klastra o rosnącej złożoności.

- **Produktywność** — w preferowanym środowisku programowym można używać różnych narzędzi do programów Hadoop i Spark.

- **Rozszerzalność za pomocą narzędzi niestandardowych lub aplikacji innych firm** — klastry HDInsight można rozszerzyć o zainstalowane składniki i można je również zintegrować z innymi rozwiązaniami do dużych zbiorów danych przy użyciu wdrożeń jednym [kliknięciem](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) z usługi Azure Market place.

- **Łatwe zarządzanie, administrowanie i monitorowanie** — usługa Azure HDInsight integruje się z [dziennikami](../hdinsight-hadoop-oms-log-analytics-tutorial.md) usługi Azure Monitor, aby zapewnić jeden interfejs, za pomocą którego można monitorować wszystkie klastry.

- **Integracja z innymi usługami platformy Azure** — usługa HDInsight można łatwo zintegrować z innymi popularnymi usługami platformy Azure, takimi jak:

    - Fabryka danych platformy Azure (ADF)
    - Azure Blob Storage
    - Usługa Azure Data Lake Storage 2. generacji
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Procesy i komponenty samonaprawiające** się — usługa HDInsight stale sprawdza infrastrukturę i komponenty open source przy użyciu własnej infrastruktury monitorowania. Automatycznie odzyskuje również krytyczne awarie, takie jak niedostępność składników i węzłów typu open source. Alerty są wyzwalane w Ambari, jeśli dowolny składnik OSS nie powiedzie się.

Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure HDInsight i stos technologii Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces planowania migracji

Następujące kroki są zalecane do planowania migracji lokalnych klastrów Hadoop do usługi Azure HDInsight:

1. Poznaj bieżące wdrożenie lokalne i topologie.
2. Poznaj aktualny zakres projektu, terminy i wiedzę zespołu.
3. Poznaj wymagania dotyczące platformy Azure.
4. Skompiluj szczegółowy plan na podstawie najlepszych rozwiązań.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Zbieranie szczegółów w celu przygotowania się do migracji

Ta sekcja zawiera kwestionariusze szablonów ułatwiające zbieranie ważnych informacji na temat:

- Wdrożenie lokalne
- Szczegóły projektu
- Wymagania systemu Azure

### <a name="on-premises-deployment-questionnaire"></a>Kwestionariusz wdrażania lokalnego

| **Pytanie** | **Przykład** | **Odpowiedź** |
|---|---|---|
|**Temat**: **Środowisko**|||
|Wersja dystrybucji klastra|HDP 2.6.5, CDH 5.7|
|Komponenty ekosystemu Big Data|HDFS, Przędza, Hive, LLAP, Impala, Kudu, HBase, Iskra, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Sokół, Zeppelin, R|
|Typy klastrów|Hadoop, Iskra, Confluent Kafka, Burza, Solr|
|Liczba klastrów|4|
|Liczba węzłów głównych|2|
|Liczba węzłów procesu roboczego|100|
|Liczba węzłów brzegowych| 5|
|Całkowita ilość miejsca na dysku|100 TB|
|Konfiguracja węzła głównego|m/r, procesor, dysk itp.|
|Konfiguracja węzłów danych|m/r, procesor, dysk itp.|
|Konfiguracja węzłów krawędzi|m/r, procesor, dysk itp.|
|Szyfrowanie HDFS?|Tak|
|Wysoka dostępność|HDFS HA, Metastore HA|
|Odzyskiwanie po awarii / Tworzenie kopii zapasowych|Klaster kopii zapasowej?|  
|Systemy zależne od klastra|SQL Server, Teradata, Power BI, MongoDB|
|Integracje innych firm|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Temat**: **Bezpieczeństwo**|||
|Zabezpieczenia obwodowe|Zapory|
|Autoryzacja & uwierzytelniania klastra|Active Directory, Ambari, Cloudera Manager, Brak uwierzytelniania|
|Kontrola dostępu do plików HDFS|  Ręczny, ssh użytkowników|
|Autoryzacja uwierzytelniania & gałęzi|Działko strażnicze, LDAP, AD z Kerberos, Ranger|
|Inspekcja|Ambari, Cloudera Navigator, Łowca|
|Monitorowanie|Grafit, zbierany, statystyka, Telegraf, InfluxDB|
|Generowanie alertów|Kapacitor, Prometeusz, Datadog|
|Czas przechowywania danych| 3 lata, 5 lat|
|Administratorzy klastrów|Pojedynczy administrator, wielu administratorów|

### <a name="project-details-questionnaire"></a>Kwestionariusz szczegółów projektu

|**Pytanie**|**Przykład**|**Odpowiedź**|
|---|---|---|
|**Temat:** **Obciążenia i częstotliwość**|||
|Oferty pracy: MapReduce|10 miejsc pracy - dwa razy dziennie||
|Oferty pracy: Hive|100 miejsc pracy - co godzinę||
|Zadania wsadowe platformy Spark|50 miejsc pracy - co 15 minut||
|Oferty pracy: Spark Streaming|5 miejsc pracy - co 3 minuty||
|Zadania przesyłania strumieniowego strukturalnego|5 miejsc pracy - co minutę||
|Zadania szkoleniowe ml model|2 miejsca pracy - raz w tygodniu||
|Języki programowania|Python, Scala, Java||
|Wykonywanie skryptów|Powłoka, Python||
|**Temat**: **Dane**|||
|Źródła danych|Pliki płaskie, Json, Kafka, RDBMS||
|Aranżacja danych|Oozie przepływy pracy, Przepływ powietrza||
|W wyszukiwaniach pamięci|Apache Ignite , Redis||
|Miejsca docelowe danych|HDFS, RDBMS, Kafka, MPP ||
|**Temat**: **Meta dane**|||
|Typ bazy danych gałęzi|Mysql, Postgres||
|Liczba metasklepów hive|2||
|Liczba tabel hive|100||
|Liczba zasad Ranger|20||
|Liczba przepływów pracy Oozie|100||
|**Temat**: **Skala**|||
|Wolumin danych, w tym replikacja|100 TB||
|Dzienna objętość spożycia|50 GB||
|Tempo wzrostu danych|10% rocznie||
|Tempo wzrostu węzłów klastra|5% rocznie
|**Temat**: **Wykorzystanie klastra**|||
|Średni % używanego procesora|60%||
|Średnia ilość używanej pamięci|75%||
|Wykorzystane miejsce na dysku|75%||
|Średnia sieć % używane|25%
|**Temat**: **Personel**|||
|Liczba administratorów|2||
|Liczba programistów|10||
|Liczba użytkowników końcowych|100||
|Umiejętności|Hadoop, Iskra||
|Liczba dostępnych zasobów na potrzeby działań w zakresie migracji|2||
|**Temat**: **Ograniczenia**|||
|Bieżące ograniczenia|Opóźnienie jest wysokie||
|Aktualne wyzwania|Problem współbieżności||

### <a name="azure-requirements-questionnaire"></a>Kwestionariusz wymagań platformy Azure

|**Temat**: **Infrastruktura** |||
|---|---|---|
|**Pytanie**|**Przykład**|**Odpowiedź**|
| Preferowany region|Wschodnie stany USA||
|VNet preferowane?|Tak||
|HA / DR potrzebne?|Tak||
|Integracja z innymi usługami w chmurze?|ADF, CosmosDB||
|**Temat**: **Przenoszenie danych**  |||
|Preferencja obciążenia początkowego|DistCp, Data box, ADF, WANDisco||
|Delta transferu danych|DistCp, AzCopy||
|Bieżący przyrostowy transfer danych|DistCp, Sqoop||
|**Temat**: **Monitorowanie & alertów** |||
|Korzystanie z monitorowania platformy Azure & alertów vs integracji monitorowania innych firm|Korzystanie z & alertów monitorowania platformy Azure||
|**Temat**: **Preferencje zabezpieczeń** |||
|Prywatny i chroniony potok danych?|Tak||
|Klaster przyłączony do domeny (ESP)?|     Tak||
|Lokalna synchronizacja usługi AD z chmurą?|     Tak||
|Liczba użytkowników usługi AD do synchronizacji?|          100||
|Ok, aby zsynchronizować hasła z chmurą?|    Tak||
|Chmura tylko użytkownicy?|                 Tak||
|POTRZEBNE MFA?|                       Nie|| 
|Wymagania dotyczące autoryzacji danych?|  Tak||
|Kontrola dostępu oparta na rolach?|        Tak||
|Inspekcja potrzebna?|                  Tak||
|Szyfrowanie danych w spoczynku?|          Tak||
|Szyfrowanie danych podczas przesyłania?|       Tak||
|**Temat**: **Preferencje ponownej architektury** |||
|Pojedynczy klaster a określone typy klastra|Określone typy klastrów||
|Współlokowany magazyn vs Magazyn zdalny?|Magazyn zdalny||
|Mniejszy rozmiar klastra, ponieważ dane są przechowywane zdalnie?|Mniejszy rozmiar klastra||
|Użyj wielu mniejszych klastrów zamiast jednego dużego klastra?|Używanie wielu mniejszych klastrów||
|Użyj zdalnego magazynu metastore?|Tak||
|Udostępnianie metasklepów między różnymi klastrami?|Tak||
|Dekonstrukcji obciążeń?|Zastąp zadania hive zadaniami platformy Spark||
|Czy używać podajnika ADF do aranżacji danych?|Nie||

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii:

- [Najważniejsze wskazówki dotyczące architektury dla lokalnej migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
