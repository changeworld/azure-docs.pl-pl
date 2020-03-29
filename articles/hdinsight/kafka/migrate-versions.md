---
title: Migrowanie obciążeń platformy Apache Platformy Kafka do usługi Azure HDInsight 4.0
description: Dowiedz się, jak migrować obciążenia platformy Apache Kafka w programie HDInsight 3.6 do HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 66ea8a93723c117a364a188ec05b050e1ef60564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548088"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrowanie obciążeń platformy Apache Platformy Kafka do usługi Azure HDInsight 4.0

Usługa Azure HDInsight 4.0 oferuje najnowsze składniki typu open source ze znaczącymi ulepszeniami w zakresie wydajności, łączności i zabezpieczeń. W tym dokumencie wyjaśniono, jak przeprowadzić migrację obciążeń platformy Apache Kafka w programie HDInsight 3.6 do HDInsight 4.0. Po migracji obciążeń do programu HDInsight 4.0 można użyć wielu nowych funkcji, które nie są dostępne w programie HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Ścieżki migracji platformy KAFKA hdinsight 3.6

Program HDInsight 3.6 obsługuje dwie wersje programów Kafka: 1.0.0 i 1.1.0. HdInsight 4.0 obsługuje wersje 1.1.0 i 2.1.0. W zależności od wersji platformy Kafka i wersji programu HDInsight, którą chcesz uruchomić, istnieje wiele obsługiwanych ścieżek migracji. Ścieżki te zostały wyjaśnione poniżej i zilustrowane na poniższym diagramie.

* **Uruchom aplikację Kafka i HDInsight w najnowszych wersjach (zalecane)**: Migruj aplikację HDInsight 3.6 i Kafka 1.0.0 lub 1.1.0 do hdinsight 4.0 z kafka 2.1.0 (ścieżki D i E poniżej).
* **Uruchom HDInsight w najnowszej wersji, ale kafka tylko w nowszej wersji:** Migruj hdinsight 3.6 i Kafka 1.0.0 aplikacji do HDInsight 4.0 z Kafka 1.1.0 (ścieżka B poniżej).
* **Uruchom HDInsight w najnowszej wersji, zachowaj wersję platformy Kafka:** Migruj aplikację HDInsight 3.6 i Kafka 1.1.0 do hdinsight 4.0 z kafka 1.1.0 (ścieżka C poniżej).
* **Uruchom kafkę w nowszej wersji, zachowaj wersję HDInsight:** Migruj aplikację Kafka 1.0.0 do 1.1.0 i pozostań na HDInsight 3.6 (ścieżka A poniżej). Należy zauważyć, że ta opcja będzie nadal wymagać wdrażania nowego klastra. Uaktualnianie wersji platformy Kafka w istniejącym klastrze nie jest obsługiwane. Po utworzeniu klastra z odpowiednią wersją należy przeprowadzić migrację klientów platformy Kafka w celu użycia nowego klastra.

![Uaktualnij ścieżki dla Apache Kafka na 3.6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Wersje Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
W przypadku migracji z platformy Kafka 1.0.0 do 1.1.0 można skorzystać z następujących nowych funkcji:

* Ulepszenia kontrolera platformy Kafka przyspieszają kontrolowane zamykanie systemu, dzięki czemu można ponownie uruchomić brokerów i szybciej odzyskać od problemów. 
* Ulepszenia w [fetchRequests logiki,](https://issues.apache.org/jira/browse/KAFKA-6254) które umożliwiają mieć więcej partycji (a tym samym więcej tematów) w klastrze. 
* Platforma Kafka Connect obsługuje [nagłówki rekordów](https://issues.apache.org/jira/browse/KAFKA-5142) i [wyrażenia regularne](https://issues.apache.org/jira/browse/KAFKA-3073) dla tematów. 

Aby uzyskać pełną listę aktualizacji, zobacz [Apache Kafka 1.1 informacje o wersji](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

W przypadku migracji do platformy Kafka 2.1 można skorzystać z następujących funkcji:

* Lepsza odporność brokera dzięki ulepszonemu protokołowi replikacji.
* Nowe funkcje w interfejsie API Platformy KafkaAdminClient.
* Konfigurowalne zarządzanie przydziałami.
* Obsługa kompresji Zstandard.

Aby uzyskać pełną listę aktualizacji, zobacz [Apache Kafka 2.0 informacje o wersji](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) i [Apache Kafka 2.1 informacje o wersji](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Zgodność klienta platformy Kafka

Nowi brokerzy Kafka wspierają starszych klientów. [KIP-35 - Pobieranie wersji protokołu](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) wprowadzono mechanizm dynamicznego określania funkcjonalności brokera Platformy Kafka i [KIP-97: Ulepszone zasady zgodności RPC klienta platformy Kafka wprowadziły](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) nową politykę zgodności i gwarancje dla klienta Java. Wcześniej klient platformy Kafka musiał wchodzić w interakcje z brokerem tej samej lub nowszej wersji. Teraz nowsze wersje klientów Java i innych klientów, które `librdkafka` obsługują KIP-35, takie jak może wrócić do starszych typów żądań lub zgłosić odpowiednie błędy, jeśli funkcjonalność nie jest dostępna.

![Uaktualnianie zgodności klienta platformy Kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Należy zauważyć, że nie oznacza to, że klient obsługuje starszych brokerów.  Aby uzyskać więcej informacji, zobacz [Macierz zgodności](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Ogólny proces migracji

Następujące wskazówki dotyczące migracji zakładają, że klaster Apache Kafka 1.0.0 lub 1.1.0 wdrożony w udziale HDInsight 3.6 w jednej sieci wirtualnej. Istniejący broker ma pewne tematy i jest aktywnie wykorzystywany przez producentów i konsumentów.

![Obecne środowisko kafka domniemane](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Aby ukończyć migrację, wykonaj następujące czynności:

1. **Wdrażanie nowego klastra HDInsight 4.0 i klientów do testowania.** Wdrażanie nowego klastra platformy Kafka hdinsight 4.0. Jeśli można wybrać wiele wersji klastra platformy Kafka, zaleca się wybranie najnowszej wersji. Po wdrożeniu należy ustawić niektóre parametry w razie potrzeby i utworzyć temat o takiej samej nazwie jak istniejące środowisko. Ponadto ustaw szyfrowanie SSL i bring-your-own-key (BYOK) w razie potrzeby. Następnie sprawdź, czy działa poprawnie z nowym klastrem.

    ![Wdrażanie nowych klastrów hdinsight 4.0](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Przełącz klaster dla aplikacji producenta i poczekaj, aż wszystkie dane kolejki są używane przez bieżących odbiorców.** Gdy nowy klaster platformy Kafka hdinsight 4.0 jest gotowy, przełącz istniejące miejsce docelowe producenta na nowy klaster. Pozostaw go w stanie, w jakim jest, dopóki istniejąca aplikacja Konsument nie zużyje wszystkich danych z istniejącego klastra.

    ![Przełączanie klastra dla aplikacji producenta](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Przełącz klaster w aplikacji konsumenckiej.** Po potwierdzeniu, że istniejąca aplikacja konsumenta zakończyła korzystanie ze wszystkich danych z istniejącego klastra, przełącz połączenie do nowego klastra.

    ![Przełączanie klastra w aplikacji konsumenckiej](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Usuń stary klaster i przetestuj aplikacje zgodnie z potrzebami.** Po zakończeniu i prawidłowym uruchomieniu przełącznika należy usunąć stary klaster platformy HDInsight 3.6 Kafka oraz producentów i konsumentów użytych w teście w razie potrzeby.

## <a name="next-steps"></a>Następne kroki

* [Optymalizacja wydajności klastrów Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Szybki start: tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu witryny Azure portal](apache-kafka-get-started.md)
