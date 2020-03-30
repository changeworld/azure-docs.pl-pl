---
title: Często zadawane pytania dotyczące usługi Azure Spring Cloud | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298770"
---
# <a name="azure-spring-cloud-faq"></a>Często zadawane pytania dotyczące chmury wiosennej platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Spring Cloud. 

## <a name="general"></a>Ogólne

### <a name="why-azure-spring-cloud"></a>Dlaczego Azure Spring Cloud?

Usługa Azure Spring Cloud zapewnia platformę jako usługę (PaaS) dla deweloperów Spring Cloud. Usługa Azure Spring Cloud zarządza infrastrukturą aplikacji, dzięki czemu można skupić się na kodzie aplikacji i logice biznesowej. Podstawowe funkcje wbudowane w usługę Azure Spring Cloud obejmują Eureka, serwer konfiguracyjny, serwer rejestru usług, usługę pivotal build service, wdrożenia blue-green i inne. Ta usługa umożliwia również deweloperom powiązanie swoich aplikacji z innymi usługami platformy Azure, takimi jak Usługa Azure Cosmos DB, usługa Azure Database for MySQL i Azure Cache for Redis.

Usługa Azure Spring Cloud usprawnia diagnostykę aplikacji dla deweloperów i operatorów, integrując usługę Azure Monitor, usługa Application Insights i usługa Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Jakie plany usług oferuje usługa Azure Spring Cloud?

Usługa Azure Spring Cloud oferuje jeden plan usługi w okresie wersji zapoznawczej.  Wdrożenie w chmurze Spring Cloud zawiera 16 rdzeni vCPU i 32 gigabajty (GB) pamięci.  Górna granica dla każdego wystąpienia mikrousług w ramach wdrożenia jest 4 rdzenie vCPU z 8 GB pamięci.

Zasób | Kwota
------- | -------
Wystąpienia aplikacji na aplikację Spring | 20
Całkowita liczba wystąpień aplikacji na wystąpienie usługi Azure Spring Cloud | 500
Wystąpienia usługi Azure Spring Cloud na region na subskrypcję | 10
Trwałe woluminy | 10 x 50 GBytów

\*_Aby podnieść limit, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/faq/)._

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Jak bezpieczna jest usługa Azure Spring Cloud?

Bezpieczeństwo i prywatność są jednymi z najważniejszych priorytetów dla klientów platformy Azure i usługi Azure Spring Cloud. Platforma Azure pomaga zapewnić, że tylko klienci mają dostęp do danych aplikacji, dzienników lub konfiguracji, bezpiecznie szyfrując wszystkie te dane. Wszystkie wystąpienia usługi w usłudze Azure Spring Cloud są odizolowane od siebie.

Usługa Azure Spring Cloud zapewnia pełne zarządzanie tls/SSL i certyfikatami.

Krytyczne poprawki zabezpieczeń dla środowiska wykonawczego OpenJDK i Spring Cloud są stosowane do usługi Azure Spring Cloud tak szybko, jak to możliwe.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>W jakich regionach jest dostępna usługa Azure Spring Cloud?

Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jakie są znane ograniczenia usługi Azure Spring Cloud?

Podczas wersji zapoznawczej usługa Azure Spring Cloud ma następujące znane ograniczenia:

* `spring.application.name`zostanie zastąpiona przez nazwę aplikacji, która jest używana do tworzenia każdej aplikacji.
* `server.port`nie jest dozwolone w pliku konfiguracyjnym z repozytorium Git. Dodanie go do pliku konfiguracyjnego prawdopodobnie spowoduje, że aplikacja będzie nieosiągalna z innych aplikacji lub Internetu.
* Szablony witryny Azure portal i usługi Azure Resource Manager nie obsługują przekazywania pakietów aplikacji. Pakiety aplikacji można przekazać tylko przez wdrożenie aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure.
* Aby dowiedzieć się więcej o ograniczeniach przydziału, zobacz [Jakie plany usług oferuje usługa Azure Spring Cloud?](#what-service-plans-does-azure-spring-cloud-offer)

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak mogę przekazać opinię i zgłosić problemy?

Jeśli wystąpią jakiekolwiek problemy z usługą Azure Spring Cloud, utwórz [żądanie pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Aby przesłać żądanie funkcji lub przekazać opinię, przejdź do [usługi Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Opracowywanie zawartości

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jestem deweloperem Spring Cloud, ale nowym na platformie Azure. Jaki jest najszybszy sposób, abym dowiedział się, jak opracować aplikację Usługi Azure Spring Cloud?

Aby uzyskać najszybszy sposób rozpoczęcia pracy z usługą Azure Spring Cloud, postępuj zgodnie z instrukcjami w [przewodniku Szybki start: Uruchom aplikację usługi Azure Spring Cloud przy użyciu portalu Azure.](spring-cloud-quickstart-launch-app-portal.md)

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jakie środowisko wykonawcze Java obsługuje usługa Azure Spring Cloud?

Usługa Azure Spring Cloud obsługuje programy Java 8 i 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Gdzie mogę wyświetlić dzienniki aplikacji i metryki aplikacji Spring Cloud?

Znajdź metryki na karcie Omówienie aplikacji i na karcie [Monitor platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)

Usługa Azure Spring Cloud obsługuje eksportowanie dzienników i metryk aplikacji w chmurze Spring Cloud do usługi Azure Storage, EventHub i [usługi Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) Nazwa tabeli w usłudze Log Analytics to *AppPlatformLogsforSpring*. Aby dowiedzieć się, jak go włączyć, zobacz [Usługi diagnostyczne](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Czy usługa Azure Spring Cloud obsługuje śledzenie rozproszone?

Tak. Aby uzyskać więcej informacji, zobacz [Samouczek: Korzystanie z śledzenia rozproszonego z usługą Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Jakie typy zasobów obsługuje powiązanie usługi?

Obecnie obsługiwane są trzy usługi: Usługa Azure Cosmos DB, usługa Azure Database for MySQL i Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Czy mogę wyświetlać, dodawać lub przenosić woluminy trwałe z wewnątrz moich aplikacji?

Tak.

## <a name="deployment"></a>wdrażania

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Czy usługa Azure Spring Cloud obsługuje wdrożenie w niebiesko-zielonej chmurze?
Tak. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska przejściowego](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Czy mogę uzyskać dostęp do kubernetes do manipulowania kontenerami aplikacji?

Nie.  Usługa Azure Spring Cloud wyodrębnia dewelopera z podstawowej architektury, umożliwiając skoncentrowanie się na kodzie aplikacji i logice biznesowej.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Czy usługa Azure Spring Cloud obsługuje kontenery tworzenia ze źródła?

Tak. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji Spring Cloud z kodu źródłowego](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Czy usługa Azure Spring Cloud obsługuje skalowanie automatyczne w wystąpieniach aplikacji?

Nie.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Jakie są najlepsze rozwiązania dotyczące migracji istniejących mikrousług Spring Cloud do usługi Azure Spring Cloud?

Podczas migracji istniejących mikrousług Spring Cloud do usługi Azure Spring Cloud warto przestrzegać następujących najlepszych rozwiązań:
* Wszystkie zależności aplikacji muszą zostać rozwiązane.
* Przygotuj wpisy konfiguracji, zmienne środowiskowe i parametry JVM, aby można je porównać z wdrożeniem w usłudze Azure Spring Cloud.
* Jeśli chcesz używać powiązania usługi, przejdź przez usługi platformy Azure i upewnij się, że masz ustawione odpowiednie uprawnienia dostępu.
* Zaleca się usunięcie lub wyłączenie wszelkich usług osadzonych, które mogą kolidować z usługami zarządzanymi przez usługę Azure Spring Cloud, takimi jak nasza usługa odnajdowania usług, serwer konfiguracji itd.
* Zalecamy korzystanie z oficjalnych, stabilnych bibliotek Pivotal Spring. Nieoficjalne, beta lub rozwidlane wersje bibliotek Pivotal Spring nie obsługują umowy dotyczącej poziomu usług (SLA).

Po migracji należy monitorować metryki procesora/pamięci RAM i ruch sieciowy, aby upewnić się, że wystąpienia aplikacji są odpowiednio skalowane.

## <a name="next-steps"></a>Następne kroki

Jeśli masz dalsze pytania, zapoznaj się z [przewodnikiem rozwiązywania problemów](spring-cloud-troubleshoot.md)z usługą Azure Spring Cloud .
