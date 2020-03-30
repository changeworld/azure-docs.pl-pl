---
title: Partnerzy monitorowania sieci szkieletowej usług Azure
description: Dowiedz się, jak monitorować aplikacje, klastry i infrastrukturę usługi Azure Service Fabric za pomocą rozwiązań do monitorowania partnerów.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645722"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partnerzy monitorowania sieci szkieletowej usług Azure

W tym artykule pokazano, jak można monitorować ich aplikacji sieci szkieletowej usług, klastrów i infrastruktury za pomocą kilku rozwiązań partnerskich. Współpracowaliśmy z każdym z poniższych partnerów, aby stworzyć zintegrowaną ofertę dla sieci szkieletowej usług.

## <a name="dynatrace"></a>Dynatrace

Nasza integracja z Dynatrace zapewnia wiele po wyjęciu z pudełka funkcje do monitorowania klastrów sieci szkieletowej usług. Instalowanie programu Dynatrace OneAgent w wystąpieniach usługi VMSS zapewnia liczniki wydajności i topologię wdrożenia sieci szkieletowej usług aż do poziomu aplikacji. Dynatrace jest również doskonałym wyborem do monitorowania lokalnego. Zapoznaj się z więcej funkcji wymienionych w [anonsie](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) i [instrukcje,](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) aby włączyć Dynatrace w klastrze. 

## <a name="datadog"></a>Datadog (polski)

Datadog ma rozszerzenie dla VMSS dla wystąpień systemu Windows i Linux. Za pomocą Datadog można zbierać dzienniki zdarzeń systemu Windows, a tym samym zbierać zdarzenia platformy sieci szkieletowej usług w systemie Windows. Zapoznaj się z instrukcjami dotyczącymi wysyłania danych diagnostycznych do Datadog [tutaj](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Integracja sieci szkieletowej usług z AppDynamics jest na poziomie aplikacji. Aktualizując zmienne środowiskowe i używając usługi App Dynamics NuGets, można wysyłać dane telemetryczne aplikacji do aplikacji AppDynamics. Zapoznaj się z tymi [instrukcjami,](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) jak zintegrować aplikacje sieci szkieletowej usług .NET z aplikacjami AppDynamics.

## <a name="new-relic"></a>New Relic

Nowy relikt to kolejne narzędzie do zarządzania wydajnością aplikacji, które dobrze integruje się z aplikacjami sieci szkieletowej usług. Można zainstalować pakiety New Relic NuGet i dodać określone zmienne środowiskowe w plikach manifestu, aby wysłać dane telemetryczne aplikacji do nowej relikwii. Zapoznaj się z tymi [instrukcjami,](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) aby włączyć nowe dane telemetryczne reliktów dla aplikacji sieci szkieletowej usług .NET.

## <a name="elk"></a>Elk 

Stos ELK to zbiór technologii open source: Elasticsearch, Logstash i Kibana. Korzystając z tych technologii w połączeniu, można zbierać, przechowywać i analizować dane monitorowania i diagnostyki sieci szkieletowej usług. Mamy tutorial jak to zrobić z service fabric rodzimych aplikacji Java [tutaj](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Okręg wyborczy Humio

Humio to usługa zbierania dzienników, która może zbierać dzienniki z aplikacji i zdarzeń z sieci szkieletowej usług w chmurze lub lokalnie w czasie rzeczywistym. Oprócz obserwacji na żywo, Humio oferuje najnowocześniejsze możliwości analizy i wizualizacji do przeglądania i zbierania informacji z diagnostyki. Humio ma opłacalne plany cenowe i jest zbudowany na skalę przy zachowaniu jego szybkiego tempa. Bezpośrednio integruje się ze zdarzeniami platformy sieci szkieletowej usług i telemetrią aplikacji. Możesz przeczytać więcej o integracji Humio i Service Fabric [tutaj](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Następne kroki

* Omówienie [monitorowania i diagnostyki](service-fabric-diagnostics-overview.md) w sieci szkieletowej usług
* Dowiedz się, jak [diagnozować typowe scenariusze](service-fabric-diagnostics-common-scenarios.md) za pomocą naszych narzędzi własnych
