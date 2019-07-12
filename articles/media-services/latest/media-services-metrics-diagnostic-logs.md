---
title: Monitorowanie usługi Azure Media Services, metryki i dzienniki diagnostyczne za pośrednictwem usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie sposobu monitorowania usługi Azure Media Services, metryki i dzienniki diagnostyczne za pośrednictwem usługi Azure Monitor.
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
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805995"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorowanie metryk usług Media Services i dzienników diagnostycznych

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Wszystkie dane zebrane przez usługi Azure Monitor pasuje do jednej z dwóch typów podstawowych, metryk i dzienników. Można monitorować dzienników diagnostycznych usługi Media Services i tworzyć alerty i powiadomienia dotyczące zebranych metryk i dzienników. Możesz wizualizować i analizować dane metryk za pomocą [Eksploratora metryk](../../azure-monitor/platform/metrics-getting-started.md). Możesz wysłać dzienniki do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), ich do usługi stream [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i wyeksportować je do [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/), lub użyj 3 usługi innych firm.

Aby uzyskać szczegółowym omówieniem, zobacz [metryk usługi Azure Monitor](../../azure-monitor/platform/data-platform.md) i [dzienników diagnostycznych platformy Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

W tym temacie omówiono obsługiwane [metryki usług Media](#media-services-metrics) i [dzienniki diagnostyczne usługi multimediów](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metryki usługi Media Services

Metryki są zbierane w regularnych odstępach czasu, czy po zmianie wartości. Są one przydatne w przypadku alertów, ponieważ może być próbkowany często i alertu można szybko wywoływane z logiką stosunkowo proste. Aby uzyskać informacje na temat tworzenia alertów dotyczących metryk, zobacz [tworzenie, wyświetlanie i zarządzanie nimi za pomocą usługi Azure Monitor alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric.md).

Usługa Media Services obsługuje metryki monitorowania dla następujących zasobów:

* Konto
* Punkt końcowy przesyłania strumieniowego
 
### <a name="account"></a>Konto

Można monitorować następujące metryki konta. 

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|AssetCount|Liczba zasobów|Zasoby na swoim koncie.|
|AssetQuota|Limit przydziału zasobów|Przydział zasobów w ramach Twojego konta.|
|AssetQuotaUsedPercentage|Procent używany przydział zasobów|Procent przydziału zasobów już używana.|
|ContentKeyPolicyCount|Zawartość liczby zasad klucza|Zasady kluczy zawartości w ramach Twojego konta.|
|ContentKeyPolicyQuota|Zawartości klucza zasad przydziału|Przydział zasady kluczy zawartości w ramach Twojego konta.|
|ContentKeyPolicyQuotaUsedPercentage|Zawartości przydziału zasady klucza używana wartość procentowa|Procent przydziału zasady klucza zawartości już używana.|
|StreamingPolicyCount|Przesyłanie strumieniowe liczby zasad|Przesyłanie strumieniowe zasad w ramach Twojego konta.|
|StreamingPolicyQuota|Strumieniowe przesyłanie zasad przydziału|Przesyłanie strumieniowe zasad przydziału w ramach Twojego konta.|
|StreamingPolicyQuotaUsedPercentage|Przesyłania strumieniowego przydziału zasady używane wartości procentowej|Procent przydziału przesyłania strumieniowego zasad już używana.|
 
Należy także przejrzeć [konta, przydziały i ograniczenia](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego

Następujące usługi Media Services [punkty końcowe przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) metryki są obsługiwane:

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|Żądania|Żądania|Zapewnia całkowitą liczbę żądań HTTP obsługiwane przez punkt końcowy przesyłania strumieniowego.|
|Ruch wychodzący|Ruch wychodzący|Całkowita liczba bajtów ruchu wychodzącego. Na przykład bajtów przesyłane strumieniowo przez punkt końcowy przesyłania strumieniowego.|
|SuccessE2ELatency|Opóźnienie typu end to end powodzenia|Czas trwania od otrzymania zażadaj wysłania ostatniego bajtu odpowiedzi na punkt końcowy przesyłania strumieniowego.|

### <a name="why-would-i-want-to-use-metrics"></a>Dlaczego chcesz przy użyciu metryk? 

Poniżej przedstawiono przykłady jak monitorowania metryk usług Media Services mogą pomóc Ci zrozumieć, jak działają aplikacje. Kilka pytań, które mogą być kierowane za pomocą metryk usługi Media Services są następujące:

* Jak monitorować Moje standardowy punkt końcowy przesyłania strumieniowego do ustalenia, kiedy przekroczenia limitu?
* Jak sprawdzić, czy wystarczającej liczby jednostek skalowania punkt końcowy przesyłania strumieniowego w warstwie Premium? 
* Jak można ustawić alert, aby wiedzieć, kiedy skalować moje punkty końcowe przesyłania strumieniowego?
* Jak ustawić alert, aby dowiedzieć się, gdy została osiągnięta maksymalna ruch wychodzący skonfigurowany na koncie?
* Jak wyświetlić podział żądań kończy się niepowodzeniem i co jest przyczyną błędu?
* Jak mogę sprawdzić, ile żądań HLS lub KRESKI są pochodzi z funkcji pakowania?
* Jak ustawić alert, aby wiedzieć, kiedy został trafiony wartość progową liczba nieudanych żądań? 

### <a name="example"></a>Przykład

Zobacz [monitorowanie metryk usług Media Services](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Dzienniki diagnostyczne usługi Media Services

Dzienniki diagnostyczne zawierają bogate, często dane dotyczące działania usługi Azure resource. Aby uzyskać więcej informacji, zobacz [jak zbieranie i używanie dane dzienników z zasobów platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Usługa Media Services obsługuje następujące dzienniki diagnostyczne:

* Dostarczania kluczy

### <a name="key-delivery"></a>Dostarczania kluczy

|Name (Nazwa)|Opis|
|---|---|
|Żądanie usługi dostarczania kluczy|Dzienniki, w których wyświetlane są informacje o żądaniach usługi dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [schematów](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Dlaczego chcesz użyć dzienniki diagnostyczne? 

Kilka rzeczy, które można sprawdzić za pomocą dzienników diagnostycznych dostarczania klucza są następujące:

* Zobacz liczbę licencji, dostarczone przez typ DRM
* Zobacz liczbę licencji, dostarczone przez zasady 
* Zobacz błędy według typu zasad lub DRM
* Liczba licencji nieautoryzowanego żądania od klientów

### <a name="example"></a>Przykład

Zobacz [monitorowanie dzienników diagnostycznych usługi multimediów](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Następne kroki 

* [Jak zbierać i wykorzystywać dane dzienników z zasobów platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Tworzenie, wyświetlanie i zarządzanie przy użyciu usługi Azure Monitor alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric.md)
* [Jak monitorować metryki usługi Media Services](media-services-metrics-howto.md)
* [Jak monitorować dzienników diagnostycznych usługi multimediów](media-services-diagnostic-logs-howto.md)
