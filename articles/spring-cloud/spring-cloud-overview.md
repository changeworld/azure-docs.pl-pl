---
title: Wprowadzenie do wiosennej chmury platformy Azure
description: Poznaj funkcje i zalety usługi Azure Spring Cloud do wdrażania aplikacji Java Spring i zarządzania nimi na platformie Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78273254"
---
# <a name="what-is-azure-spring-cloud"></a>Co to jest usługa Azure Spring Cloud?

Usługa Azure Spring Cloud ułatwia wdrażanie aplikacji opartych na wiosennym rozruchu na platformie Azure bez zmian kodu.  Usługa Azure Spring Cloud zarządza cyklem życia aplikacji Spring Cloud, dzięki czemu deweloperzy mogą skupić się na swoim kodzie.  Spring Cloud zapewnia zarządzanie cyklem życia przy użyciu kompleksowego monitorowania i diagnostyki, zarządzania konfiguracją, odnajdowania usług, integracji ciągłej integracji/ciągłej integracji ciągłej integracji/ciągłego wdrażania, wdrożeń niebiesko-zielonych i innych.

W ramach ekosystemu platformy Azure usługa Azure Spring Cloud umożliwia łatwe wiązanie z innymi usługami platformy Azure, w tym magazynem, bazami danych, monitorowaniem i innymi funkcjami.

Usługa Azure Spring Cloud jest obecnie oferowana jako publiczna wersja zapoznawcza. Publiczne oferty w wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną premierą.  Publiczne funkcje i usługi w wersji zapoznawczej nie są przeznaczone do użytku w wersji produkcyjnej.  Aby uzyskać więcej informacji na temat pomocy technicznej podczas wersji zapoznawców, zapoznaj się z [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub złóż [wniosek o pomoc techniczną,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) aby dowiedzieć się więcej.

Aby rozpocząć, ukończ przewodnika Szybki start w chmurze Spring Cloud przy użyciu [interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart-launch-app-cli.md), portalu [Azure](spring-cloud-quickstart-launch-app-portal.md)lub [Maven.](spring-cloud-quickstart-launch-app-maven.md)

Więcej przykładów jest dostępnych w usłudze GitHub: [Samples w chmurze w chmurze Azure Spring.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="spring-cloud-config-server"></a>Serwer konfiguracyjny Spring Cloud

Usługa Azure Spring Cloud Config Server zapewnia konfigurację zewnętrzną w systemie rozproszonym z obsługą serwera i klienta.  Config Server zapewnia centralną lokalizację do zarządzania właściwościami aplikacji we wszystkich środowiskach.  Aby dowiedzieć się więcej, odwiedź [odwołanie do serwera konfiguracji w chmurze Spring](https://spring.io/projects/spring-cloud-config.md) Cloud i ukończ samouczek.

### <a name="enable-bluegreen-deployments"></a>Włączanie wdrożeń niebieski/zielony

Usługa Azure Spring Cloud obsługuje wdrożenia niebieski/zielony do wydawania i aktualizowania kodu do środowisk produkcyjnych.  Wykorzystanie tego wzorca zarządzania zmianami umożliwia deweloperom implementowanie funkcji i zmian kodu z zabezpieczeniami natychmiastowego rezerwowego, gdy jest to konieczne.  Platforma Azure umożliwia deweloperom skoncentrowanie się na pisaniu kodu przez zarządzanie wieloma środowiskami produkcyjnymi i ułatwianie aktualizowania lub wycofywania zmian kodu bez przerywania aplikacji.  Aby dowiedzieć się więcej o środowiskach przejściowych i wdrożeniach niebieski/zielony, odwiedź ten [artykuł inscenizacyjny](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatyzacja potoków ciągłej integracji/ciągłego wdrażania

Usługa Azure Spring Cloud zapewnia integrację z platformą Azure DevOps przy użyciu interfejsu wiersza polecenia platformy Azure.  Za pomocą usługi Azure DevOps można zautomatyzować integrację kodu i wdrażanie do aplikacji Spring.  Aby dowiedzieć się więcej, odwiedź ten [artykuł](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Skalowanie aplikacji

Usługa Azure Spring Cloud umożliwia łatwe skalowanie mikrousług na pulpicie nawigacyjnym usługi Azure Spring Cloud.  Zarówno liczba procesorów wirtualnych, jak i ilość pamięci dostępnej dla mikrousług można skalować w górę lub w dół, aby spełnić wymagania.  Skalowanie staje się skuteczne w ciągu kilku sekund i nie wymaga zmiany kodu lub ponownego rozmieszczenia.  Aby dowiedzieć się więcej, ukończ ten [samouczek](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Application Monitoring (Monitorowanie aplikacji)

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorowanie aplikacji przy użyciu śledzenia rozproszonego i usługi Azure App Insights

Narzędzia śledzenia rozproszonego usługi Spring Cloud umożliwiają deweloperom debugowanie i monitorowanie złożonych połączeń między mikrousługami w aplikacji.  Dzięki integracji [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) z [usługą](../azure-monitor/insights/insights-overview.md)Azure Application Insights platforma Azure zapewnia zaawansowane funkcje śledzenia rozproszonego bezpośrednio z witryny Azure portal.  Aby dowiedzieć się więcej, ukończ ten [samouczek](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Następne kroki

- [Uruchom aplikację Spring Cloud z interfejsu wiersza polecenia](spring-cloud-quickstart-launch-app-cli.md)
