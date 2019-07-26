---
title: Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
description: Poznaj różne metody zapewniające bezpieczeństwo przedsiębiorstw w usłudze Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: overview
ms.date: 07/22/2019
ms.openlocfilehash: 4619545ab1fed5f55504e80eede0d1cf240eea87
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488688"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

Usługa Azure HDInsight oferuje różne metody zaspokajania potrzeb związanych z bezpieczeństwem przedsiębiorstwa. Większość z tych rozwiązań nie jest domyślnie aktywowana. Ta elastyczność umożliwia wybranie funkcji zabezpieczeń, które są najważniejsze dla Ciebie, i pomaga uniknąć płacenia za niepotrzebne funkcje. Oznacza to również, że jest odpowiedzialny za zapewnienie, że dla instalacji i środowiska są włączone odpowiednie rozwiązania.

Ten artykuł dotyczy rozwiązań zabezpieczeń dzielących rozwiązania w zakresie zabezpieczeń na linie czterech tradycyjnych filarów zabezpieczeń: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

W tym artykule wprowadzono również **pakiet Enterprise Security usługi Azure HDInsight (ESP)** , która zapewnia uwierzytelnianie oparte na Active Directory, obsługę wieloużytkownikom oraz kontrolę dostępu opartą na rolach dla klastrów usługi HDInsight.

## <a name="enterprise-security-pillars"></a>Filary zabezpieczeń przedsiębiorstwa

Jednym ze sposobów na wyszukanie zabezpieczeń przedsiębiorstwa jest dzielenie rozwiązań zabezpieczeń na cztery główne grupy w oparciu o typ formantu. Te grupy są również nazywane filarami zabezpieczeń i są następujące: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

### <a name="perimeter-security"></a>Zabezpieczenia obwodu

Zabezpieczenia obwodowe w usłudze HDInsight są realizowane za poorednictwem [sieci wirtualnych](../hdinsight-plan-virtual-network-deployment.md). Administrator przedsiębiorstwa może utworzyć klaster w sieci wirtualnej (VNET) i użyć sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w regułach sieciowej grupy zabezpieczeń dla ruchu przychodzącego będą mogły komunikować się z klastrem usługi HDInsight. Ta konfiguracja zapewnia ochronę obwodową.

Wszystkie klastry wdrożone w sieci wirtualnej również mają prywatny punkt końcowy, który jest rozpoznawany jako prywatny adres IP wewnątrz sieci wirtualnej na potrzeby prywatnego dostępu HTTP do bram klastra.

### <a name="authentication"></a>Authentication

[Pakiet Enterprise Security](apache-domain-joined-architecture.md) z usługi HDInsight zapewnia uwierzytelnianie oparte na Active Directoryach, obsługa przez wiele użytkowników oraz kontrolę dostępu opartą na rolach. Integracja Active Directory jest realizowana przy użyciu [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Dzięki tym funkcjom można utworzyć klaster usługi HDInsight połączony z zarządzaną domeną Active Directory. Następnie można skonfigurować listę pracowników w przedsiębiorstwie, którzy mogą uwierzytelniać się w klastrze i logować się do niego.

W przypadku tej konfiguracji pracownicy przedsiębiorstwa mogą zalogować się do węzłów klastra przy użyciu ich poświadczeń domeny. Mogą oni również używać poświadczeń domeny do uwierzytelniania z innymi zatwierdzonymi punktami końcowymi, takimi jak Apache Ambari views, ODBC, JDBC, PowerShell i interfejsy API REST do współpracy z klastrem. 

### <a name="authorization"></a>Authorization

Najlepszym rozwiązaniem w przypadku większości przedsiębiorstw jest upewnienie się, że nie każdy pracownik ma dostęp do wszystkich zasobów przedsiębiorstwa. Analogicznie, administrator może definiować zasady kontroli dostępu opartej na rolach dla zasobów klastra. Ta wartość jest dostępna tylko w klastrach ESP.

Administrator usługi Hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC), aby zabezpieczyć platformę Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) i [Kafka](apache-domain-joined-run-kafka.md) przy użyciu tych wtyczek w programie Apache Ranger. Skonfigurowanie zasad RBAC umożliwia kojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że osoby mają tylko uprawnienia niezbędne do wykonywania swoich obowiązków służbowych. Ranger umożliwia także inspekcję dostępu do danych pracowników i wszelkich zmian dokonanych w zasadach kontroli dostępu.

Na przykład administrator może skonfigurować środowisko [Apache Ranger](https://ranger.apache.org/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia filtrowanie na poziomie wiersza i kolumny (Maskowanie danych) i filtruje poufne dane przed nieautoryzowanymi użytkownikami.

### <a name="auditing"></a>Inspekcja

Inspekcja całego dostępu do zasobów klastra i danych jest niezbędna do śledzenia nieautoryzowanego lub niezamierzonego dostępu do zasobów. Jest to ważne, aby chronić zasoby klastra usługi HDInsight przed nieautoryzowanymi użytkownikami i zabezpieczać dane.

Administrator może wyświetlić i zgłosić cały dostęp do zasobów i danych klastra usługi HDInsight. Administrator może również wyświetlać i raportować wszystkie zmiany zasad kontroli dostępu utworzonych w ramach obsługiwanych przez Apache Ranger punktów końcowych. 

Aby uzyskać dostęp do dzienników inspekcji oprogramowania Apache Ranger i Ambari, a także dzienników dostępu SSH, [włącz Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) i Wyświetl powiązane rekordy inspekcji.

### <a name="encryption"></a>Szyfrowanie

Ochrona danych jest istotna dla spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Wraz z ograniczaniem dostępu do danych przed nieautoryzowanymi pracownikami należy go zaszyfrować.

Oba magazyny danych dla klastrów usługi HDInsight, Azure Blob Storage i Azure Data Lake Storage Gen1/Gen2 obsługują przezroczyste szyfrowanie po stronie serwera [dla danych](../../storage/common/storage-service-encryption.md) przechowywanych w spoczynku. Bezpieczne klastry usługi HDInsight bezproblemowo współpracują z tą możliwością szyfrowania danych po stronie serwera.

## <a name="shared-responsibility-model"></a>Współużytkowany model odpowiedzialności

Poniższy obraz podsumowuje główne obszary zabezpieczeń systemu i dostępne dla Ciebie rozwiązania zabezpieczeń. Wyróżnia także, które obszary zabezpieczeń są odpowiedzialne za klienta i które obszary są odpowiedzialne za usługę HDInsight jako usługodawcę.

![Udostępniony diagram obowiązków usługi HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Poniższa tabela zawiera linki do zasobów dla każdego typu rozwiązania zabezpieczeń.

| Obszar zabezpieczeń | Dostępne rozwiązania | Osoba odpowiedzialna |
|---|---|---|
| Zabezpieczenia dostępu do danych | Konfigurowanie [kontroli dostępu listy ACL](../../storage/blobs/data-lake-storage-access-control.md) dla Azure Data Lake Storage Gen1 i Gen2  | Odbiorcy |
|  | Włącz Właściwość ["wymagany bezpieczny transfer"](../../storage/common/storage-require-secure-transfer.md) na kontach magazynu. | Odbiorcy |
|  | Konfigurowanie zapór i sieci wirtualnych [usługi Azure Storage](../../storage/common/storage-network-security.md) | Odbiorcy |
|  | Upewnij się, że [szyfrowanie TLS](../../storage/common/storage-security-tls.md) jest włączone na potrzeby przesyłania danych. | Odbiorcy |
|  | Konfigurowanie [kluczy zarządzanych przez klienta](../../storage/common/storage-encryption-keys-portal.md) do szyfrowania za pomocą usługi Azure Storage | Odbiorcy |
| Zabezpieczenia aplikacji i oprogramowania pośredniczącego | Integracja z usługą AAD — DS i [Konfigurowanie uwierzytelniania](apache-domain-joined-configure-using-azure-adds.md) | Odbiorcy |
|  | Konfigurowanie zasad [autoryzacji Apache Ranger](apache-domain-joined-run-hive.md) | Odbiorcy |
|  | Korzystanie z [dzienników Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Odbiorcy |
| Zabezpieczenia systemu operacyjnego | Tworzenie klastrów z najnowszym bezpiecznym obrazem podstawowym | Odbiorcy |
|  | Zapewnianie [stosowania poprawek systemu operacyjnego](../hdinsight-os-patching.md) w regularnych odstępach czasu | Odbiorcy |
| Bezpieczeństwo sieci | Konfigurowanie [sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) |
|  | Skonfiguruj [reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla ruchu przychodzącego](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Odbiorcy |
|  | Konfigurowanie [ograniczenia ruchu](../hdinsight-restrict-outbound-traffic.md) wychodzącego za pomocą zapory (wersja zapoznawcza) | Odbiorcy |
| Zwirtualizowana infrastruktura | ND | HDInsight (dostawca usług w chmurze) |
| Zabezpieczenia infrastruktury fizycznej | ND | HDInsight (dostawca usług w chmurze) |

## <a name="next-steps"></a>Następne kroki

* [Planowanie klastrów usługi HDInsight przy użyciu ESP](apache-domain-joined-architecture.md)
* [Konfigurowanie klastrów usługi HDInsight przy użyciu ESP](apache-domain-joined-configure.md)
* [Zarządzanie klastrami usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-manage.md)
