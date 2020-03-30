---
title: Często zadawane pytania dotyczące apache kafka w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące platformy Apache Kafka w usłudze Azure HDInsight, zarządzanej usłudze w chmurze Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206983"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Często zadawane pytania dotyczące platformy Apache Kafka w ramach usługi Azure HDInsight

Ten artykuł dotyczy niektórych typowych pytań dotyczących korzystania z platformy Apache Kafka w usłudze Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Jakie wersje platformy Kafka są obsługiwane przez hdinsight?

Więcej informacji na temat oficjalnie obsługiwanych wersji komponentów hdinsight można znaleźć w części [Jakie są komponenty i wersje Apache Hadoop dostępne w formacie HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Zalecamy zawsze używanie najnowszej wersji, aby zapewnić najlepszą możliwą wydajność i środowisko użytkownika.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Jakie zasoby są dostarczane w klastrze platformy HDInsight w kafce i za jakie zasoby pobieram opłatę?

Klaster platformy Kafka usługi HDInsight zawiera następujące zasoby:

* Węzły głowy
* Węzły zookeeper
* Węzły brokera (pracownika) 
* Dyski zarządzane platformy Azure dołączone do węzłów brokera
* Węzły bramy

Wszystkie te zasoby są naliczane na podstawie naszego [modelu cenowego HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), z wyjątkiem węzłów bramy. Nie są naliczane opłaty za węzły bramy.

Aby uzyskać bardziej szczegółowy opis różnych typów węzłów, zobacz [architektura sieci wirtualnej usługi Azure HDInsight](../hdinsight-virtual-network-architecture.md). Ceny są oparte na użyciu węzła na minutę. Ceny różnią się w zależności od rozmiaru węzła, liczby węzłów, typu używanego dysku zarządzanego i regionu.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Czy interfejsy API Apache Kafka współpracują z interfejsami HDInsight?

Tak, hdinsight używa natywnych interfejsów API platformy Kafka. Kod aplikacji klienckiej nie musi się zmieniać. Zobacz [samouczek: Użyj interfejsów API producenta i konsumentów platformy Apache Kafka,](./apache-kafka-producer-consumer-api.md) aby zobaczyć, jak można używać interfejsów API producenta/konsumenta opartych na języku Java w klastrze.

## <a name="can-i-change-cluster-configurations"></a>Czy można zmienić konfiguracje klastra?

Tak, za pośrednictwem portalu Ambari. Każdy składnik w portalu ma sekcję **konfiguracjów,** która może służyć do zmiany konfiguracji składników. Niektóre zmiany mogą wymagać ponownego uruchomienia brokera.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Jaki typ uwierzytelniania obsługuje usługi HDInsight dla platformy Apache Kafka?

Korzystając z [pakietu zabezpieczeń przedsiębiorstwa (ESP),](../domain-joined/apache-domain-joined-architecture.md)można uzyskać zabezpieczenia na poziomie tematu dla ich klastrów platformy Kafka. Zobacz [samouczek: Konfigurowanie zasad platformy Apache Kafka w aplikacji HDInsight z pakietem zabezpieczeń przedsiębiorstwa (wersja zapoznawcza)](../domain-joined/apache-domain-joined-run-kafka.md)— aby uzyskać więcej informacji.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Czy moje dane są szyfrowane? Czy mogę używać własnych kluczy?

Wszystkie komunikaty platformy Kafka na dyskach zarządzanych są szyfrowane za pomocą [szyfrowania usługi Azure Storage Service Encryption (SSE).](../../storage/common/storage-service-encryption.md) Dane w tranzycie (na przykład dane przesyłane od klientów do brokerów i na odwrót) nie są domyślnie szyfrowane. Można zaszyfrować taki [ruch, konfigurując SSL na własną rękę.](./apache-kafka-ssl-encryption-authentication.md) Ponadto usługa HDInsight umożliwia zarządzanie własnymi kluczami w celu szyfrowania danych w spoczynku. Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysku klucza zarządzanego](../disk-encryption.md)przez klienta.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Jak połączyć klientów z klastrem?

Aby klienci platformy Kafka mogli komunikować się z brokerami platformy Kafka, muszą oni mieć możliwość dotarcia do brokerów za pośrednictwem sieci. W przypadku klastrów HDInsight granica zabezpieczeń jest granicą sieci wirtualnej(VNet). W związku z tym najprostszym sposobem łączenia klientów z klastrem HDInsight jest utworzenie klientów w tej samej sieci wirtualnej co klaster. Inne scenariusze obejmują:

* Łączenie klientów w innej sieci wirtualnej platformy Azure — równoważy sieć wirtualną klastra i sieć wirtualną klienta i konfiguruj klaster dla [reklam IP.](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising) Podczas korzystania z reklam IP klienci platformy Kafka muszą używać adresów IP brokera do łączenia się z brokerami, a nie z w pełni kwalifikowanymi nazwami domen (FQDN).

* Łączenie klientów lokalnych — korzystanie z sieci VPN i konfigurowanie niestandardowych serwerów DNS zgodnie z opisem w [programie Plan sieci wirtualnej dla usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Tworzenie publicznego punktu końcowego dla usługi Platformy Kafka — jeśli wymagania dotyczące zabezpieczeń przedsiębiorstwa na to pozwalają, można wdrożyć publiczny punkt końcowy dla brokerów platformy Kafka lub samodzielnie zarządzany punkt końcowy REST typu open source z publicznym punktem końcowym.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Czy można dodać więcej miejsca na dysku w istniejącym klastrze?

Aby zwiększyć ilość miejsca dostępnego dla wiadomości platformy Kafka, można zwiększyć liczbę węzłów. Obecnie dodawanie większej liczby dysków do istniejącego klastra nie jest obsługiwane.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Czy klaster platformy Kafka może współpracować z databricks? 

Tak, klastry platformy Kafka mogą pracować z Databricks tak długo, jak są one w tej samej sieci wirtualnej. Aby użyć klastra platformy Kafka z databricks, należy utworzyć sieć wirtualną z klastrem platformy HDInsight Kafka w nim, a następnie określić, że sieć wirtualna podczas tworzenia obszaru roboczego Databricks i użyj iniekcji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Wdrażanie usługi Azure Databricks w sieci wirtualnej platformy Azure (iniekcja sieci wirtualnej).](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject) Podczas tworzenia obszaru roboczego Databricks należy podać nazwy brokera bootstrap klastra platformy Kafka. Aby uzyskać informacje na temat pobierania nazw brokerów Platformy Kafka, zobacz [Pobierz informacje o hostu Apache Zookeeper i Broker](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Jak uzyskać maksymalną trwałość danych?

Trwałość danych pozwala osiągnąć najniższe ryzyko utraty wiadomości. Aby osiągnąć maksymalną trwałość danych, zalecamy następujące ustawienia:

* minimalnego współczynnika replikacji 3 w większości regionów
* użyj minimalnego współczynnika replikacji 4 w regionach z tylko dwiema domenami błędów
* wyłączyć nieczyste wybory przywódców
* ustaw **min.insync.replicas** na 2 lub więcej - zmienia to liczbę replik, które muszą być całkowicie zsynchronizowane z liderem, zanim zapis będzie mógł być kontynuowany
* ustaw właściwość **acks** na **wszystkie** — ta właściwość wymaga, aby wszystkie repliki potwierdzało wszystkie wiadomości

Konfigurowanie platformy Kafka dla większej spójności danych wpływa na dostępność brokerów do tworzenia żądań.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Czy można replikować dane do wielu klastrów?

Tak, dane mogą być replikowane do wielu klastrów przy użyciu programu Kafka MirrorMaker. Zobacz szczegóły dotyczące konfigurowania MirrorMaker można znaleźć w [Mirror Apache Tematy Kafka](apache-kafka-mirroring.md). Ponadto istnieją inne zarządzane przez siebie technologie open source i dostawcy, którzy mogą pomóc w osiągnięciu replikacji do wielu klastrów, takich jak [Brooklin.](https://github.com/linkedin/Brooklin/)

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Czy mogę uaktualnić klaster? Jak uaktualnić klaster?

Obecnie nie obsługujemy uaktualnień wersji klastra w miejscu. Aby zaktualizować klaster do wyższej wersji platformy Kafka, utwórz nowy klaster z odpowiednią wersją i migruj klientów platformy Kafka, aby użyć nowego klastra.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Jak monitorować klaster platformy Kafka?

Użyj monitora platformy Azure do analizowania [dzienników platformy Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie szyfrowania i uwierzytelniania warstwy SSL (Secure Sockets Layer) dla platformy Apache Kafka w usłudze Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Replikowanie tematów platformy Apache Kafka przy użyciu platformy Kafka w usłudze HDInsight za pomocą narzędzia MirrorMaker](./apache-kafka-mirroring.md)
