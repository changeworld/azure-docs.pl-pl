---
title: Usługa Azure Service Fabric monitorowania najlepsze rozwiązania | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące monitorowania klastrów usługi Service Fabric i aplikacji.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d90daaf18e5161053e00671b7667d05ec8e5db76
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533813"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

[Monitorowanie i Diagnostyka](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) mają kluczowe znaczenie dla programowania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Na przykład można śledzić, jak Twoje aplikacje są używane, akcje wykonywane przez platformy usługi Service Fabric, wykorzystanie zasobów przy użyciu liczników wydajności i ogólnej kondycji klastra. Aby zdiagnozować i rozwiązać problemy i zapobiec ich w przyszłości, można użyć tych informacji.

## <a name="application-monitoring"></a>Monitorowanie aplikacji

Monitorowanie aplikacji śledzi sposób używania funkcji i składników aplikacji. Monitoruj swoje aplikacje, aby upewnić się, że zostały wykryte problemy, które mają wpływ na użytkowników. Monitorowanie aplikacji jest odpowiedzialność opracowywania aplikacji i usług, ponieważ jest on unikatowy dla logiki biznesowej aplikacji. Zaleca się skonfigurować monitorowanie aplikacji za pomocą [usługi Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), aplikacji platformy Azure, narzędzie do monitorowania.

## <a name="cluster-monitoring"></a>Monitorowanie klastrów

Jednym z celów usługi Service Fabric jest zapewnienie aplikacje odporne na awarie sprzętowe. W tym celu odbywa się za pośrednictwem usługi systemowe platformy możliwość wykrywania problemów z infrastrukturą i szybko obciążeń w tryb failover do innych węzłów w klastrze. Ale co zrobić, jeśli występują problemy w samych usług systemu? Lub jeśli podczas próby wdrażania lub przenoszenia obciążeń, naruszenia reguły umieszczania usług? Usługa Service Fabric udostępnia diagnostyki dla tych i innych problemów, aby upewnić się, że wyświetlana jest także informacja o współdziałania platformy usługi Service Fabric z aplikacjami, usługami, kontenerów i węzłów.

W przypadku klastrów Windows zalecane jest, skonfiguruj monitorowanie klastrów za pomocą [agenta funkcji Diagnostyka](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) i [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

W przypadku klastrów systemu Linux dzienniki usługi Azure Monitor jest również zalecanym narzędziem do platformy Azure i monitorowania infrastruktury. Diagnostyka platformy Linux wymaga innej konfiguracji, jak wspomniano w [zdarzenia klastra usługi Service Fabric systemu Linux usługi SYSLOG](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorowanie infrastruktury

[Dzienniki platformy Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) jest zalecane w przypadku monitorowania zdarzeń na poziomie klastra. Po skonfigurowaniu agenta usługi Log Analytics z obszarem roboczym, zgodnie z opisem w poprzedniej konsolidacji, można zbierać metryki wydajności, takie jak użycie procesora CPU, liczników wydajności platformy .NET, takich jak proces poziom użycia Procesora wydajności usługi Service Fabric liczniki, takie jak liczba wyjątki od usługi reliable service i kontenerów metryk, takich jak użycie procesora CPU.  Konieczne będzie zapisują dzienniki kontenera do strumienia wyjściowego stdout i stderr, dlatego, że będą one dostępne w dziennikach w usłudze Azure Monitor.

## <a name="watchdogs"></a>Watchdogs

Ogólnie rzecz biorąc strażnika jest osobną usługą, który obserwuje kondycji i obciążenia w usługach, polecenia ping do punktów końcowych i raporty kondycji nieoczekiwanych zdarzeń w klastrze. Może to pomóc uniknąć błędów, które mogą nie zostać wykryte oparte tylko na wydajność jednej usługi. Watchdogs jest również dobrym miejscem do śledzenia hostowanie kodu, który wykonuje czynności zaradczych, które nie wymagają interakcji użytkownika, takich jak czyszczenie plików dziennika w magazynie w określonych odstępach czasu. Zobacz przykładową implementację usługi strażnika w [zdarzenia klastra usługi Service Fabric systemu Linux usługi SYSLOG](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Kolejne kroki

* Rozpocznij pracę, instrumentacji aplikacji: [Poziomu generowania zdarzeń i dzienników aplikacji](service-fabric-diagnostics-event-generation-app.md).
* Zapoznaj się z artykułem instrukcje konfigurowania usługi Application Insights dla aplikacji za pomocą [monitorowanie i diagnozowanie aplikacji platformy ASP.NET Core w usłudze Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Dowiedz się więcej o funkcji monitorowania i zdarzeń, które zapewnia usługi Service Fabric platformy: [Platforma poziomu generowania zdarzeń i dziennika](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurowanie integracji dzienników usługi Azure Monitor z usługą Service Fabric: [Konfigurowanie dzienników usługi Azure Monitor dla klastra](service-fabric-diagnostics-oms-setup.md)
* Dowiedz się, jak skonfigurować dzienniki usługi Azure Monitor w celu monitorowania kontenerów: [Monitorowanie i Diagnostyka kontenerów Windows w usłudze Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Zobacz przykład diagnostyki problemy i rozwiązania z usługą Service Fabric: [diagnozowanie typowe scenariusze](service-fabric-diagnostics-common-scenarios.md)
* Więcej informacji na temat ogólnych zaleceń dotyczących monitorowania zasobów platformy Azure: [Najlepsze praktyki — monitorowanie i Diagnostyka](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).