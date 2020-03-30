---
title: Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
description: Poznaj różne metody zapewnienia bezpieczeństwa przedsiębiorstwa w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 95bfe7d7788133d8548598cb30c8084bf64a977f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78267722"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

Usługa Azure HDInsight oferuje szereg metod, które odpowiadają twoim potrzebom w zakresie zabezpieczeń przedsiębiorstwa. Większość z tych rozwiązań nie jest domyślnie aktywowana. Ta elastyczność pozwala wybrać funkcje zabezpieczeń, które są dla Ciebie najważniejsze, i pomaga uniknąć płacenia za funkcje, których nie chcesz. Oznacza to również, że twoim obowiązkiem jest upewnienie się, że odpowiednie rozwiązania są włączone dla twojej konfiguracji i środowiska.

W tym artykule omówiono rozwiązania zabezpieczeń, dzieląc rozwiązania zabezpieczeń na wzór czterech tradycyjnych filarów zabezpieczeń: zabezpieczeń obwodowych, uwierzytelniania, autoryzacji i szyfrowania.

W tym artykule przedstawiono również **pakiet zabezpieczeń usługi Azure HDInsight Enterprise Security Package (ESP),** który zapewnia uwierzytelnianie oparte na usłudze Active Directory, obsługę wielu użytkowników i kontrolę dostępu opartą na rolach dla klastrów HDInsight.

## <a name="enterprise-security-pillars"></a>Filary zabezpieczeń przedsiębiorstwa

Jednym ze sposobów patrzenia na zabezpieczenia przedsiębiorstwa dzieli rozwiązania zabezpieczeń na cztery główne grupy w zależności od typu kontroli. Grupy te są również nazywane filarami zabezpieczeń i są następujące: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

### <a name="perimeter-security"></a>Zabezpieczenia obwodowe

Bezpieczeństwo obwodowe w HDInsight uzyskuje się za pośrednictwem [sieci wirtualnych.](../hdinsight-plan-virtual-network-deployment.md) Administrator przedsiębiorstwa może utworzyć klaster wewnątrz sieci wirtualnej (VNET) i użyć sieciowych grup zabezpieczeń (NSG) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w przychodzących regułach sieciowej grupy sieciowej będą mogły komunikować się z klastrem HDInsight. Ta konfiguracja zapewnia zabezpieczenia obwodowe.

Wszystkie klastry wdrożone w sieci wirtualnej będą również miały prywatny punkt końcowy, który jest rozpoznawany na prywatnym adresie IP wewnątrz sieci wirtualnej dla prywatnego dostępu HTTP do bram klastra.

### <a name="authentication"></a>Uwierzytelnianie

[Pakiet zabezpieczeń przedsiębiorstwa](apache-domain-joined-architecture.md) firmy HDInsight zapewnia uwierzytelnianie oparte na usłudze Active Directory, obsługę wielu użytkowników i kontrolę dostępu opartą na rolach. Integracja z usługą Active Directory jest osiągana za pomocą [usług domenowych Active Directory platformy Azure](../../active-directory-domain-services/overview.md). Dzięki tym funkcjom można utworzyć klaster HDInsight, który jest połączony z zarządzaną domeną usługi Active Directory. Następnie można skonfigurować listę pracowników z przedsiębiorstwa, którzy mogą uwierzytelniać się i logować do klastra.

Dzięki tej konfiguracji pracownicy przedsiębiorstwa mogą logować się do węzłów klastra przy użyciu poświadczeń domeny. Mogą również używać swoich poświadczeń domeny do uwierzytelniania przy użyciu innych zatwierdzonych punktów końcowych, takich jak Apache Ambari Views, ODBC, JDBC, PowerShell i REST API do interakcji z klastrem.

### <a name="authorization"></a>Autoryzacja

Najlepszym rozwiązaniem, które stosuje większość przedsiębiorstw, jest upewnienie się, że nie każdy pracownik ma dostęp do wszystkich zasobów przedsiębiorstwa. Podobnie administrator może zdefiniować zasady kontroli dostępu oparte na rolach dla zasobów klastra. Jest to dostępne tylko w klastrach ESP.

Administrator hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC) w celu zabezpieczenia Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)i [Kafka](apache-domain-joined-run-kafka.md) za pomocą tych wtyczek w Apache Ranger. Konfigurowanie zasad RBAC umożliwia skojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że ludzie mają tylko uprawnienia potrzebne do wykonywania swoich obowiązków służbowych. Ranger umożliwia również inspekcję dostępu do danych pracowników i wszelkich zmian dokonanych w zasadach kontroli dostępu.

Na przykład administrator może skonfigurować środowisko [Apache Ranger](https://ranger.apache.org/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia filtrowanie na poziomie wiersza i kolumny (maskowanie danych) i filtruje poufne dane od nieautoryzowanych użytkowników.

### <a name="auditing"></a>Inspekcja

Inspekcja całego dostępu do zasobów klastra i danych jest niezbędna do śledzenia nieautoryzowanego lub niezamierzonego dostępu do zasobów. Jest to równie ważne, jak ochrona zasobów klastra HDInsight przed nieautoryzowanymi użytkownikami i zabezpieczanie danych.

Administrator może wyświetlać i raportować cały dostęp do zasobów i danych klastra HDInsight. Administrator może również wyświetlać i zgłaszać wszystkie zmiany w zasadach kontroli dostępu utworzonych w punktach końcowych obsługiwanych przez Apache Ranger.

Aby uzyskać dostęp do dzienników inspekcji Apache Ranger i Ambari oraz dzienników dostępu ssh, [włącz usługę Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) i wyświetl tabele, które zapewniają rekordy inspekcji.

### <a name="encryption"></a>Szyfrowanie

Ochrona danych jest ważna dla spełnienia wymagań dotyczących zabezpieczeń i zgodności organizacji. Wraz z ograniczeniem dostępu do danych od nieautoryzowanych pracowników należy je zaszyfrować.

Oba magazyny danych dla klastrów HDInsight, magazyn obiektów Blob platformy Azure i usługa Azure Data Lake Storage Gen1/Gen2 obsługują przezroczyste [szyfrowanie danych](../../storage/common/storage-service-encryption.md) po stronie serwera w stanie spoczynku. Bezpieczne klastry HDInsight bezproblemowo będą współpracować z tą funkcją szyfrowania danych po stronie serwera w spoczynku.

### <a name="compliance"></a>Zgodność

Oferty zgodności platformy Azure są oparte na różnych rodzajach gwarancji, w tym formalnych certyfikatach, zaświadczeniach, walidacjach, zezwoleniach i ocenach sporządzanych przez niezależne zewnętrzne firmy audytorów, zmiany umowy, samooceny i dokumentów dotyczących porad dotyczących klientów opracowanych przez firmę Microsoft. Aby uzyskać informacje o zgodności usługi HDInsight, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center) i [omówienie zgodności platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Model wspólnej odpowiedzialności

Na poniższej ilustracji podsumowano główne obszary zabezpieczeń systemu i rozwiązania zabezpieczeń, które są dostępne w każdym z nich. Podkreśla również, które obszary bezpieczeństwa są twoim obowiązkiem jako klienta i które obszary są odpowiedzialne za HDInsight jako usługodawcę.

![Diagram współodpowiedzialności hdinsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Poniższa tabela zawiera łącza do zasobów dla każdego typu rozwiązania zabezpieczeń.

| Obszar bezpieczeństwa | Dostępne rozwiązania | Strona odpowiedzialna |
|---|---|---|
| Zabezpieczenia dostępu do danych | Konfigurowanie [list kontroli dostępu](../../storage/blobs/data-lake-storage-access-control.md) dla usługi Azure Data Lake Storage Gen1 i Gen2  | Klient |
|  | Włącz właściwość ["Bezpieczne wymagane przeniesienie"](../../storage/common/storage-require-secure-transfer.md) na kontach magazynu. | Klient |
|  | Konfigurowanie [zapór usługi Azure Storage](../../storage/common/storage-network-security.md) i sieci wirtualnych | Klient |
|  | Konfigurowanie [punktów końcowych usługi sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) dla usługi Cosmos DB i usługi Azure SQL [DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Klient |
|  | Upewnij się, że [szyfrowanie TLS](../../storage/common/storage-security-tls.md) jest włączone dla przesyłanych danych. | Klient |
|  | Konfigurowanie [kluczy zarządzanych przez klienta](../../storage/common/storage-encryption-keys-portal.md) do szyfrowania usługi Azure Storage | Klient |
| Zabezpieczenia aplikacji i oprogramowania pośredniczącego | Integracja z usługą AAD-DS i [konfigurowanie uwierzytelniania](apache-domain-joined-configure-using-azure-adds.md) | Klient |
|  | Konfigurowanie zasad [autoryzacji Łowców Apache](apache-domain-joined-run-hive.md) | Klient |
|  | Korzystanie z [dzienników usługi Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Klient |
| Bezpieczeństwo systemu operacyjnego | Tworzenie klastrów z najnowszym bezpiecznym obrazem podstawowym | Klient |
|  | Zapewnianie [łatanie systemu operacyjnego](../hdinsight-os-patching.md) w regularnych odstępach czasu | Klient |
| Bezpieczeństwo sieci | Konfigurowanie [sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurowanie [reguł grupy zabezpieczeń sieci przychodzącej](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Klient |
|  | Konfigurowanie [ograniczenia ruchu wychodzącego](../hdinsight-restrict-outbound-traffic.md) za pomocą zapory | Klient |
| Zwirtualizowanej infrastruktury | Nie dotyczy | HDInsight (dostawca chmury) |
| Bezpieczeństwo infrastruktury fizycznej | Nie dotyczy | HDInsight (dostawca chmury) |

## <a name="next-steps"></a>Następne kroki

* [Planowanie klastrów HDInsight za pomocą esp](apache-domain-joined-architecture.md)
* [Konfigurowanie klastrów usługi HDInsight za pomocą usługi ESP](apache-domain-joined-configure.md)
* [Zarządzanie klastrami HDInsight za pomocą esp](apache-domain-joined-manage.md)
