---
title: Metryki usługi Media Services i dzienniki diagnostyczne za pomocą usługi Azure Monitor
titleSuffix: Azure Media Services
description: Dowiedz się, jak monitorować metryki i dzienniki diagnostyczne usługi Azure Media Services za pośrednictwem usługi Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585275"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitorowanie metryk i dzienników diagnostycznych usługi Media Services za pośrednictwem usługi Azure Monitor

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Wszystkie dane zbierane przez usługę Azure Monitor są jednym z dwóch podstawowych typów: metryk i dzienników. Można monitorować dzienniki diagnostyczne usługi Media Services i tworzyć alerty i powiadomienia dla zebranych metryk i dzienników. Można wizualizować i analizować dane metryk za pomocą [Eksploratora metryk.](../../azure-monitor/platform/metrics-getting-started.md) Dzienniki można wysyłać do [usługi Azure Storage,](https://azure.microsoft.com/services/storage/)przesyłać strumieniowo do [usługi Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/)eksportować je do [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/)lub korzystać z usług innych firm.

Aby uzyskać szczegółowe informacje, zobacz [Metryki usługi Azure Monitor](../../azure-monitor/platform/data-platform.md) i [dzienniki diagnostyczne usługi Azure Monitor.](../../azure-monitor/platform/platform-logs-overview.md)

W tym temacie omówiono obsługiwane [dzienniki metryk usługi Media Services](#media-services-metrics) i [dzienniki diagnostyczne usługi Media Services](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metryki usługi Media Services

Metryki są zbierane w regularnych odstępach czasu, czy wartość zmienia się. Są one przydatne do alertów, ponieważ mogą być pobierane często próbkowania i alert może być uruchamiany szybko za pomocą stosunkowo prostej logiki. Aby uzyskać informacje na temat tworzenia alertów metryk, zobacz [Tworzenie, wyświetlanie i zarządzanie alertami metryk przy użyciu usługi Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Usługi Media Services obsługuje metryki monitorowania dla następujących zasobów:

* Konto
* Punkt końcowy przesyłania strumieniowego

### <a name="account"></a>Konto

Możesz monitorować następujące dane konta.

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|AssetCount (licz assetcount)|Liczba zasobów|Zasoby na twoim koncie.|
|AssetQuota (Quota aktywów)|Przydział środków trwałych|Przydział zasobów na koncie.|
|AssetQuotaUsedPercentage|Użyta wartość procentowa przydziału zasobów|Procent już użytego przydziału zasobów.|
|ContentKeyPolicyCount (Liczba klawiatur contentkeypolicycount)|Liczba zasad klucza zawartości|Zasady dotyczące kluczy zawartości na twoim koncie.|
|ContentKeyPolicyQuota (ContentKeyPolicyQuota)|Przydział zasad klucza zawartości|Przydział zasad klucza zawartości na koncie.|
|ContentKeyPolicyQuotaUsedPercentage|Użyto wartości procentowej przydziału zasad klucza zawartości|Procent już użytego przydziału zasad klucza zawartości.|
|StreamingPolicyCount|Liczba zasad przesyłania strumieniowego|Zasady przesyłania strumieniowego na twoim koncie.|
|StreamingPolicyQuota|Przydział zasad przesyłania strumieniowego|Przydział zasad przesyłania strumieniowego na koncie.|
|StreamingPolicyQuotaUsedPercentage|Użyty procent przydziału zasad przesyłania strumieniowego|Procent już użytego przydziału zasad przesyłania strumieniowego.|

Należy również przejrzeć [przydziały i limity kont](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego

Obsługiwane są następujące [metryki punktów końcowych przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) usługi Media Services:

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|Żądania|Żądania|Zawiera całkowitą liczbę żądań HTTP obsługiwanych przez punkt końcowy przesyłania strumieniowego.|
|Ruch wychodzący|Ruch wychodzący|Całkowita liczba bajtów wychodzących. Na przykład bajty przesyłane strumieniowo przez punkt końcowy przesyłania strumieniowego.|
|SuccessE2ELatency|Sukces koniec do końca Latencja|Czas trwania od momentu odebrania punktu końcowego przesyłania strumieniowego do momentu wysłania ostatniego bajtu odpowiedzi.|

### <a name="why-would-i-want-to-use-metrics"></a>Dlaczego warto używać danych?

Oto przykłady, w jaki sposób monitorowanie metryk usługi Media Services może pomóc w zrozumieniu, jak działają aplikacje. Oto kilka pytań, które można rozwiązać za pomocą metryk usługi Media Services:

* Jak monitorować standardowy punkt końcowy przesyłania strumieniowego, aby wiedzieć, kiedy przekroczyłem limity?
* Skąd mam wiedzieć, czy mam wystarczającą liczbę jednostek w skali premium w skali punktu końcowego przesyłania strumieniowego?
* Jak ustawić alert, aby wiedzieć, kiedy skalować punkty końcowe przesyłania strumieniowego w górę?
* Jak ustawić alert, aby wiedzieć, kiedy osiągnięto maksymalny ruch wychodzący skonfigurowany na koncie?
* Jak sprawdzić, jak podział żądań nie powiódł się i co jest przyczyną awarii?
* Jak sprawdzić, ile żądań HLS lub DASH jest pobieranych z pakowania?
* Jak ustawić alert, aby wiedzieć, kiedy wartość progowa # żądań nie powiodło się został trafiony?

### <a name="example"></a>Przykład

Zobacz [Jak monitorować metryki usługi Media Services](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Dzienniki diagnostyczne usługi Media Services

Dzienniki diagnostyczne zapewniają bogate i częste dane dotyczące działania zasobu platformy Azure. Aby uzyskać więcej informacji, zobacz [Jak zbierać i wykorzystywać dane dziennika z zasobów platformy Azure.](../../azure-monitor/platform/platform-logs-overview.md)

Usługa Media Services obsługuje następujące dzienniki diagnostyczne:

* Dostawa kluczy

### <a name="key-delivery"></a>Dostawa kluczy

|Nazwa|Opis|
|---|---|
|Żądanie usługi dostarczania kluczy|Dzienniki, które pokazują informacje o żądaniu usługi dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [schematy](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Dlaczego warto korzystać z dzienników diagnostycznych?

Oto kilka czynności, które można zbadać za pomocą dzienników diagnostycznych dostarczania kluczy:

* Zobacz liczbę licencji dostarczonych przez typ DRM.
* Zobacz liczbę licencji dostarczonych przez zasady.
* Zobacz błędy drm lub typu zasad.
* Zobacz liczbę nieautoryzowanych żądań licencji od klientów.

### <a name="example"></a>Przykład

Zobacz [Jak monitorować dzienniki diagnostyczne usługi Media Service](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Następne kroki

* [Jak zbierać i wykorzystywać dane dziennika z zasobów platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Jak monitorować metryki usługi Media Services](media-services-metrics-howto.md)
* [Jak monitorować dzienniki diagnostyczne usługi Media Service](media-services-diagnostic-logs-howto.md)
