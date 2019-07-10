---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 46376477aae9f94c1c8f6e1dd6bc718d213cc373
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712595"
---
Kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Śledzi informacje dotyczące rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Tak|[Billing](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisuje dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|Serwer Proxy http|Konfiguruje serwer proxy HTTP dla żądania wychodzącego.|
|Nie|[Logging](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Nie|[Mounts](#mount-settings)|Odczytuje i zapisuje dane z komputera hosta do kontenera i z kontenera, wróć do komputera hosta.|
