---
title: Często zadawane pytania dotyczące chmury wiosennej platformy Azure | Microsoft Docs
description: Przejrzyj często zadawane pytania dotyczące chmury wiosennej platformy Azure
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 12ef5586ac1e7ecf27824f95add3355451cf555a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163774"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące chmury wiosennej platformy Azure. 

## <a name="general"></a>Ogólne

### <a name="why-azure-spring-cloud"></a>Dlaczego chmura sprężynowa platformy Azure?

Chmura Wiosnowa platformy Azure udostępnia platformę jako usługę (PaaS) dla deweloperów wiosennych. Chmura sprężynowa platformy Azure zarządza infrastrukturą aplikacji, dzięki czemu możesz skupić się na kodzie aplikacji i logice biznesowej. Podstawowe funkcje wbudowane w chmurę Azure wiosną, m.in. Eureka, config Server, serwer rejestru usługi, usługi kompilacji Pivot, wdrożenia Blue-Green i nie tylko. Ta usługa umożliwia również deweloperom powiązanie aplikacji z usługami platformy Azure, takimi jak CosmosDB, MySQL i Azure cache for Redis.

Chmura sprężynowa platformy Azure rozszerza środowisko diagnostyki aplikacji dla deweloperów i operatorów przez integrację Azure Monitor, Application Insights i Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Jakie plany usług są oferowane przez chmurę Azure ze sprężyną?

Chmura Wiosenna Azure oferuje jeden plan usługi w okresie zapoznawczym.  Wdrożenie z chmurą wiosenną zawiera 16 rdzeni vCPU i 32 GB pamięci.  Górna granica każdego wystąpienia mikrousług w ramach wdrożenia wynosi 4 vCPU rdzeni z 8 GB pamięci.

Zasób | Ilość
------- | -------
Wystąpienia aplikacji na wiosnę | 20
Łączna liczba wystąpień aplikacji na usługę Azure sprężynowego wystąpienia usługi w chmurze | 50 *
Wystąpienia usługi w chmurze ze sprężyną na platformie Azure na region na subskrypcję | dwóch
Woluminy trwałe | 10 x 50 GBytes

*_otworzyć [bilet pomocy technicznej](https://azure.microsoft.com/support/faq/) w celu podniesienia limitu._

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Jak bezpieczny jest chmura Wiosenna platformy Azure?

Bezpieczeństwo i prywatność to jedno z najważniejszych priorytetów dla klientów korzystających z platformy Azure i usługi Azure wiosny Cloud. System Azure zapewnia, że tylko klient ma dostęp do danych, dzienników lub konfiguracji aplikacji przez bezpieczne szyfrowanie wszystkich tych danych. Wszystkie wystąpienia usługi w chmurze Azure wiosennej są odizolowane od siebie nawzajem.

Chmura sprężynowa platformy Azure oferuje pełne zarządzanie protokołami SSL i certyfikatami.

Krytyczne poprawki zabezpieczeń dla środowisk uruchomieniowych w chmurze OpenJDK i wiosny są stosowane do chmury wiosennej platformy Azure najszybciej, jak to możliwe.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Które regiony są dostępne w chmurze Azure wiosennej?

Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jakie są znane ograniczenia chmury wiosennej platformy Azure

Poniżej przedstawiono znane ograniczenia chmury wiosennej platformy Azure, gdy usługa jest w wersji zapoznawczej.

* `spring.application.name` zostanie przesłonięty przez nazwę aplikacji używaną do tworzenia każdej aplikacji.
* `server.port` nie jest dozwolony w pliku konfiguracji z repozytorium git. Dodanie go do pliku konfiguracji prawdopodobnie spowoduje, że aplikacja nie będzie mogła być niedostępna z innych aplikacji lub Internetu.
* Szablony Azure Portal i Menedżer zasobów nie obsługują przekazywania pakietów aplikacji. Można to zrobić tylko za pomocą wdrażania aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.
* Aby uzyskać ograniczenia dotyczące limitu przydziału, zapoznaj się z [planami usług dotyczącymi usługi Azure wiosny Cloud](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak mogę przekazać Opinie i zgłosić problemy?

Jeśli napotykasz jakiekolwiek problemy z chmurą Azure wiosennej, Utwórz [żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Aby uzyskać informacje na temat żądań funkcji, przejdź do [odpowiedzi na platformę Azure](https://feedback.azure.com/forums/34192--general-feedback) .

## <a name="development"></a>Tworzenie oprogramowania

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Jestem deweloperem wiosną, ale nowym na platformie Azure, co jest najszybszym sposobem na zapoznanie się ze sposobem tworzenia aplikacji w chmurze wystąpienia wiosennej?

Najszybszym sposobem na rozpoczęcie pracy z chmurą Azure wiosną jest wykonanie [tego przewodnika Szybki Start](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jakie środowisko uruchomieniowe języka Java obsługuje chmurę Azure?

Chmura sprężynowa platformy Azure obsługuje środowisko Java 8 i 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Gdzie mogę zobaczyć moje dzienniki aplikacji ze sprężyną i metryki?

Znajdź metryki na karcie Przegląd aplikacji i karcie [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Chmura sprężynowa platformy Azure obsługuje eksportowanie dzienników aplikacji i metryk do usługi Azure Storage, centrów EventHub i [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Nazwa tabeli w Log Analytics jest `AppPlatformLogsforSpring`. Aby ją włączyć, zapoznaj się z tym artykułem dotyczącym naszych [usług diagnostycznych](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Czy chmura Wiosenna platformy Azure obsługuje śledzenie rozproszone?

Tak. Aby uzyskać więcej informacji, odwiedź stronę [śledzenie rozproszone](spring-cloud-tutorial-distributed-tracing.md) .

### <a name="what-resource-types-does-service-binding-support"></a>Jakie typy zasobów obsługuje usługa powiązania usługi?

Obecnie są obsługiwane trzy usługi: Azure Cosmos DB, Azure Database for MySQL i pamięć podręczna platformy Azure dla Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Czy mogę wyświetlać/dodawać/przenosić woluminy trwałe z wewnątrz aplikacji?
Tak.

## <a name="deployment"></a>Wdrażanie

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Czy usługa Azure Wiosenna Cloud obsługuje wdrożenie Blue-Green?
Tak. więcej informacji można znaleźć w [przewodniku po środowisku przejściowym](spring-cloud-howto-staging-environment.md) .

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Czy mogę uzyskać dostęp do programu Kubernetes w celu manipulowania kontenerami aplikacji?

Nie.  Chmura ze sprężyną platformy Azure jest abstrakcyjna dla deweloperów z podstawowej architektury, co pozwala skoncentrować się na kodzie aplikacji i logice biznesowej.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Czy chmura sprężynowa platformy Azure obsługuje tworzenie kontenerów ze źródła?

Tak. Aby uzyskać więcej informacji, odwiedź stronę [wdrażanie ze źródła](spring-cloud-launch-from-source.md) .

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Czy chmura sprężynowa platformy Azure obsługuje skalowanie automatyczne w wystąpieniach aplikacji?

Nie.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Jakie są najlepsze rozwiązania dotyczące migrowania istniejących mikrousług do chmurowej platformy Azure?

Przed przeprowadzeniem migracji istniejących mikrousług wiosennych do chmury Azure wiosennej,
* Należy rozwiązać wszystkie zależności aplikacji.
* Przygotuj wpisy konfiguracji, zmienne środowiskowe i JVM parametry, aby można było je porównać z wdrożeniem w chmurze Azure wiosennej.
* Jeśli chcesz użyć powiązania usługi, przejdź przez usługi platformy Azure i upewnij się, że ustawiono odpowiednie uprawnienia dostępu.
* Zalecamy usunięcie lub wyłączenie usług osadzonych, które mogą powodować konflikt z usługami zarządzanymi przez chmurę wiosenną platformy Azure, taką jak nasza usługa odnajdowania usług, serwer konfiguracji itp.
*-* Zalecamy używanie oficjalnych i stabilnych bibliotek sprężyny Pivot. Nieoficjalne, beta lub rozwidlenia wersji bibliotek sprężyny Pivot nie mają wsparcia dla umowy SLA.

Po migracji Monitoruj metryki procesora/pamięci RAM i ruch sieciowy, aby zapewnić odpowiednie skalowanie wystąpień aplikacji.

## <a name="next-steps"></a>Następne kroki

[Zapoznaj się z przewodnikiem rozwiązywania problemów, jeśli masz więcej pytań](spring-cloud-troubleshoot.md).